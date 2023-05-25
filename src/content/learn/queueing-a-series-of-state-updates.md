---
title: State আপডেটস এর একটি ক্রম সারিবদ্ধ করা
---

<Intro>

State ভেরিয়েবল সেট করা হলে আরেকটি রেন্ডারকে সারিবদ্ধ করবে। কিন্তু পরের রেন্ডারকে সারিবদ্ধ করার আগে, কখনো কখনো আপনি হয়তো ভ্যালুতে অনেকগুলো অপারেশন করতে চাইতে পারেন। এটা করতে, React কিভাবে state আপডেট গুলোকে ব্যাচ করে তা বুঝতে পারবেন।

</Intro>

<YouWillLearn>

* "ব্যাচিং" কি এবং React কিভাবে এটা ব্যবহার করে অনেকগুলো state আপডেটসকে প্রক্রিয়া করে।
* একটি সারিতে একই state ভেরিয়েবলে কীভাবে বেশ কয়েকটি আপডেট প্রয়োগ করবেন।

</YouWillLearn>

## React state আপডেটসকে ব্যাচিং করে {/*react-batches-state-updates*/}

আপনি হয়তো অনুমান করতে পারেন যে, "+৩" বাটনে ক্লিক করার পর, বাটনটি কাউন্টার কে তিন বার বৃদ্ধি করবে কারণ এটা এই `setNumber(number + 1)` ফাংশনকে ৩ বার কল করেঃ

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+৩</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

তবে, আপনি হয়তো আগের অধ্যায় থেকে মনে করতে পারেন, [প্রতিটি রেন্ডারের state এর মানগুলো স্থায়ী](/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time), সুতরাং প্রথম ইভেন্ট হ্যান্ডলারের ভিতরে `number` এর মান সর্বদাই `0` হবে, আপনি যতবারই `setNumber(1)` ফাংশনটি কল করুন না কেনঃ

```js
setNumber(0 + 1);
setNumber(0 + 1);
setNumber(0 + 1);
```

কিন্তু এখানে অন্য একটি ফ্যাক্টর কাজ করে। **React আপনার state আপডেটগুলো প্রসেসিং করার আগে, ইভেন্ট হ্যান্ডলারের *সকল* কোড রান করা পর্যন্ত অপেক্ষা করে।** এই কারণে সকল `setNumber()` ফাংশন কল করার *পরে*, শুধুমাত্র তখনি রি-রেন্ডার ঘটে।

এটি আপনাকে একজন ওয়েটারের রেস্টুরেন্টে অর্ডার নেওয়ার কথা মনে করিয়ে দিতে পারে। একজন ওয়েটার আপনার প্রথম খাবারের কথা বলার সাথে সাথে রান্নাঘরে দৌড়ে যায় না! এর বদলে, তারা আপনাকে আপনার অর্ডারটি শেষ করতে দেয়, আপনাকে এতে পরিবর্তন করতে দেয় এবং এমনকি টেবিলে থাকা অন্য লোকেদের কাছ থেকে অর্ডার নেয়।

<Illustration src="/images/docs/illustrations/i_react-batching.png"  alt="An elegant cursor at a restaurant places and order multiple times with React, playing the part of the waiter. After she calls setState() multiple times, the waiter writes down the last one she requested as her final order." />

এটি আপনাকে একাধিক state ভেরিয়েবল আপডেট করতে দেয়।--এমনকি একাধিক কম্পোনেন্টস থেকেও--অনেক [রি-রেন্ডারস](/learn/render-and-commit#re-renders-when-state-updates) ট্রিগার করা ছাড়াই। কিন্তু এর মানে হল যে UI আপডেট করা হবে না যতক্ষণ না আপনার ইভেন্ট হ্যান্ডলার থাকা কোনো কোড সম্পূর্ণ না হয়। এই আচরণ, **ব্যাচিং** নামেও পরিচিত, এটা আপনার react অ্যাপকে আরও দ্রুত চালায়। এটি বিভ্রান্তিকর "অর্ধ-সমাপ্ত" রেন্ডার কে এড়িয়ে যায় যেখানে শুধুমাত্র কিছু ভেরিয়েবল আপডেট করা হয়েছে।

**ক্লিকের মত, *একাধিক* ইচ্ছাকৃত ইভেন্ট জুড়ে react ব্যাচ করে না**--প্রতিটি ক্লিক আলাদাভাবে পরিচালনা করা হয়। নিশ্চিন্ত থাকুন যে react শুধুমাত্র তখনই ব্যাচিং করে যখন এটি করা সাধারণত নিরাপদ। এটি নিশ্চিত করে যে, উদাহরণস্বরূপ, যদি প্রথম বাটন ক্লিকে একটি ফর্ম নিষ্ক্রিয় করে, দ্বিতীয় ক্লিকটি আবার সাবমিট দিবে না।

## পরবর্তী রেন্ডারের আগে একই state একাধিকবার আপডেট করা {/*updating-the-same-state-multiple-times-before-the-next-render*/}

এটা একটি বিরল ব্যবহার, কিন্তু যদি আপনার ভাল লাগে তাহলে পরবর্তী রেন্ডারের আগে একই state একাদিকবার আপডেট করতে পারেন, `setNumber(number + 1)` এর মতো করে *পরবর্তী state এর মান* পাস করার পরিবর্তে, আপনি একটা *ফাংশন* পাস করতে পারেন যেটা সারিতে থাকা আগেরটির উপর ভিত্তি করে পরবর্তী state গণনা করে, উদাহরণস্বরূপ `setNumber(n => n + 1)`। এটি react কে প্রতিস্থাপনের পরিবর্তে "state এর মান দিয়ে কিছু করতে" বলে।

এখন কাউন্টার বৃদ্ধি করার চেষ্টা করুনঃ

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        setNumber(n => n + 1);
      }}>+৩</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

এখানে, `n => n + 1` একটি **আপডেটার ফাংশন।** যখন আপনি এটিকে state setter এ পাস করেন:

১। ইভেন্ট হ্যান্ডলারের অন্যান্য সমস্ত কোড চালানোর পরে এই ফাংশনটি প্রক্রিয়া করার জন্য react সারিবদ্ধ করে।
২। পরবর্তী রেন্ডারের সময়, React সারির মধ্য দিয়ে যায় এবং আপনাকে চূড়ান্ত আপডেটেড state দেয়।

```js
setNumber(n => n + 1);
setNumber(n => n + 1);
setNumber(n => n + 1);
```

ইভেন্ট হ্যান্ডলার চালানোর সময় কোডের এই লাইনগুলির মাধ্যমে react কীভাবে কাজ করে তা এখানে বলা হল:

১। `setNumber(n => n + 1)`: `n => n + 1` একটি ফাংশন। React এটিকে একটি সারিতে যোগ করে।
২। `setNumber(n => n + 1)`: `n => n + 1` একটি ফাংশন। React এটিকে একটি সারিতে যোগ করে।
৩। `setNumber(n => n + 1)`: `n => n + 1` একটি ফাংশন। React এটিকে একটি সারিতে যোগ করে।

আপনি যখন পরবর্তী রেন্ডারের সময় `useState` কল করেন, তখন react সারির মধ্য দিয়ে যায়। আগের `number` এর state ছিল `0`, তাই 'n' আর্গুমেন্ট হিসাবে প্রথম আপডেটার ফাংশনে react একটাকে পাস করে। তারপর react আপনার পূর্ববর্তী আপডেটার ফাংশনের রিটার্নের মান নেয় এবং এটিকে পরবর্তী আপডেটারকে `n` হিসাবে প্রেরণ করে, ইত্যাদিঃ

|  queued update | `n` | returns |
|--------------|---------|-----|
| `n => n + 1` | `0` | `0 + 1 = 1` |
| `n => n + 1` | `1` | `1 + 1 = 2` |
| `n => n + 1` | `2` | `2 + 1 = 3` |

চূড়ান্ত ফলাফল হিসাবে react `3` কে সঞ্চয় করে এবং `useState` এ রিটার্ন করে।

এই কারণে উপরের উদাহরণে "+৩" ক্লিক করলে মানটি 3 দ্বারা সঠিকভাবে বৃদ্ধি পায়।
### State এর মান প্রতিস্থাপন করার পর আপনি যদি এটিকে আবার আপডেট করেন তাহলে কি হয় {/*what-happens-if-you-update-state-after-replacing-it*/}

এই ইভেন্ট হ্যান্ডলার সম্পর্কে কি ভাবেন? আপনি কি মনে করেন `number` এর মান পরবর্তী রেন্ডারে কি হবে?

```js
<button onClick={() => {
  setNumber(number + 5);
  setNumber(n => n + 1);
}}>
```

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setNumber(n => n + 1);
      }}>সংখ্যার মান বৃদ্ধি করুন</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

এই ইভেন্ট হ্যান্ডলার react কে কী করতে বলে তা এখানে দেখানো হল:

১। `setNumber(number + 5)`: `number` হয় `0`, তাই `setNumber(0 + 5)`. React তার সারিতে *"`5` দিয়ে প্রতিস্থাপন করে"* যোগ করে।
২। `setNumber(n => n + 1)`: `n => n + 1` একটি আপডেটার ফাংশন. React তার সারিতে *সেই আপডেটার ফাংশন কে* যোগ করে।

পরবর্তী রেন্ডারের সময়, React state এর সারির মধ্য দিয়ে যায়:

|   সারিবদ্ধ আপডেট       | `n` | রিটার্নস |
|--------------|---------|-----|
| "`5` দিয়ে প্রতিস্থাপন" | `0` (অব্যবহৃত) | `5` |
| `n => n + 1` | `5` | `5 + 1 = 6` |

চূড়ান্ত ফলাফল হিসাবে React `6` সঞ্চয় করে এবং `useState` এ রিটার্ন করে।

<Note>

আপনি হয়তো লক্ষ্য করেছেন যে `setState(5)` আসলে `setState(n => 5)` এর মতো কাজ করে, কিন্তু `n` অব্যবহৃত!

</Note>

### State আপডেট করার পরে যদি এটিকে কে আবার প্রতিস্থাপন করেন তাহলে কি হয় {/*what-happens-if-you-replace-state-after-updating-it*/}

চলেন আরো একটি উদাহরণ দিয়ে চেষ্টা করি। আপনি কি মনে করেন `number` এর মান পরবর্তী রেন্ডারে কি হবে?

```js
<button onClick={() => {
  setNumber(number + 5);
  setNumber(n => n + 1);
  setNumber(42);
}}>
```

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setNumber(n => n + 1);
        setNumber(42);
      }}>সংখ্যার মান বৃদ্ধি করুন</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

এই ইভেন্ট হ্যান্ডলারটি চালানোর সময় কোডের এই লাইনগুলির মাধ্যমে react কীভাবে কাজ করে তা এখানে দেখানো হল:

১। `setNumber(number + 5)`: `number` হয় `0`, তাই `setNumber(0 + 5)`. React তার সারিতে *"`5` দিয়ে প্রতিস্থাপন করে"* যোগ করে।
২। `setNumber(n => n + 1)`: `n => n + 1` একটি আপডেটার ফাংশন। React তার সারিতে *সেই আপডেটার ফাংশন কে* যোগ করে।
৩। `setNumber(42)`: React তার সারিতে *"`42` দিয়ে প্রতিস্থাপন করে"* যোগ করে।

পরবর্তী রেন্ডারের সময়, React state এর সারির মধ্য দিয়ে যায়:

|   সারিবদ্ধ আপডেট       | `n` | রিটার্নস |
|--------------|---------|-----|
| “`5` দিয়ে প্রতিস্থাপন" | `0` (অব্যবহৃত) | `5` |
| `n => n + 1` | `5` | `5 + 1 = 6` |
| “`42` দিয়ে প্রতিস্থাপন" | `6` (অব্যবহৃত) | `42` |

তারপর react চূড়ান্ত ফলাফল হিসাবে `42` সঞ্চয় করে এবং `useState` এ রিটার্ন করে।

সংক্ষেপে, আপনি `setNumber` state সেটারে কী পাস করছেন তা আপনি কীভাবে ভাবতে পারেন তা এখানে দেয়া হলঃ

* **একটি আপডেটার ফাংশন** (যেমন `n => n + 1`) সারিতে যোগ করা হয়।
* **অন্য যেকোনো মান** (যেমন `5` সংখ্যা) সারিতে "`5` দিয়ে প্রতিস্থাপন করে" যোগ করে, যা ইতিমধ্যে সারিবদ্ধ আছে তা বাতিল করে।

ইভেন্ট হ্যান্ডলার সম্পূর্ণ হওয়ার পরে, React পুনরায় একটি রেন্ডার ট্রিগার করবে। পুনরায় রেন্ডার করার সময়, React সারিটি প্রক্রিয়া করবে। আপডেটার ফাংশনগুলি রেন্ডারিংয়ের সময় চলে, তাই **আপডেটার ফাংশনগুলি অবশ্যই [বিশুদ্ধ](/learn/keeping-components-pure)** হতে হবে এবং ফলাফলটি শুধুমাত্র *রিটার্ন* করতে হবে। তাদের ভিতর থেকে state সেট করার চেষ্টা করবেন না বা অন্যান্য পার্শ্ব প্রতিক্রিয়া চালাবেন না। কঠোর মোডে, React আপনার ভুল খুঁজে পেতে সাহায্য করার জন্য প্রতিটি আপডেটার ফাংশন দুবার চালাবে (কিন্তু দ্বিতীয় ফলাফলটি বাতিল করবে)।

### নামকরণের কনভেনশন {/*naming-conventions*/}

সংশ্লিষ্ট state ভেরিয়েবলের প্রথম অক্ষর দ্বারা আপডেটার ফাংশন এর আর্গুমেন্টের নাম দেওয়া হয় প্রচলিতভাবে:

```js
setEnabled(e => !e);
setLastName(ln => ln.reverse());
setFriendCount(fc => fc * 2);
```

আপনি যদি আরও শব্দবহুল কোড পছন্দ করেন, তাহলে আরেকটি সাধারণ নিয়ম হল `setEnabled(enabled => !enabled)` এটার মতো করে পূর্ণ state ভেরিয়েবল নাম পুনরাবৃত্তি করা, অথবা `setEnabled(prevEnabled => !prevEnabled)` এর মতো করে একটি প্রিফিক্স ব্যবহার করা।

<Recap>

* সেটিং state বিদ্যমান রেন্ডারে ভেরিয়েবলকে পরিবর্তন করে না, কিন্তু এটি একটি নতুন রেন্ডারের অনুরোধ করে।
* ইভেন্ট হ্যান্ডলারদের চালানো শেষ হওয়ার পরে react state আপডেটগুলি প্রক্রিয়া করে। একে ব্যাচিং বলে।
* একটি ইভেন্টে কিছু state একাধিকবার আপডেট করতে, আপনি `setNumber(n => n + 1)` আপডেটার ফাংশন ব্যবহার করতে পারেন।

</Recap>



<Challenges>

#### Request কাউন্টারটি ঠিক করুন {/*fix-a-request-counter*/}

আপনি একটি আর্ট মার্কেটপ্লেস অ্যাপে কাজ করছেন যা ব্যবহারকারীকে একই সময়ে একটি আর্ট আইটেমের জন্য একাধিক অর্ডার জমা দিতে দেয়। প্রতিবার ব্যবহারকারী "কিনুন" বাটনে ক্লিক করলে, "পেন্ডিং" কাউন্টারটি এক দ্বারা বৃদ্ধি করা উচিত। তিন সেকেন্ড পরে, "পেন্ডিং" কাউন্টারটি হ্রাস করা উচিত এবং "সম্পূর্ণ" কাউন্টারটি বৃদ্ধি করা উচিত।

তবে, "পেন্ডিং" কাউন্টারটি যেভাবে চাচ্ছি সে অনুযায়ী আচরণ করে না। আপনি যখন "কিনুন" বাটনে ক্লিক করেন, তখন তা কমে `-1` হয়ে যায় (যা সম্ভব নয়!)। এবং যদি আপনি দ্রুত দুইবার ক্লিক করেন, উভয় কাউন্টার অপ্রত্যাশিতভাবে আচরণ করে বলে মনে হচ্ছে।

কেন এটা ঘটবে? উভয় কাউন্টার ঠিক করুন।

<Sandpack>

```js
import { useState } from 'react';

export default function RequestTracker() {
  const [pending, setPending] = useState(0);
  const [completed, setCompleted] = useState(0);

  async function handleClick() {
    setPending(pending + 1);
    await delay(3000);
    setPending(pending - 1);
    setCompleted(completed + 1);
  }

  return (
    <>
      <h3>
        পেন্ডিং: {pending}
      </h3>
      <h3>
        সম্পূর্ণ: {completed}
      </h3>
      <button onClick={handleClick}>
        কিনুন     
      </button>
    </>
  );
}

function delay(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms);
  });
}
```

</Sandpack>

<Solution>

`HandleClick` ইভেন্ট হ্যান্ডলারের ভিতরে, `পেন্ডিং` এবং `সম্পূর্ণ` এর মানগুলি ক্লিক ইভেন্টের সময় যা ছিল তার সাথে মিলে যায়। প্রথম রেন্ডারের জন্য, `পেন্ডিং` ছিল `0`, তাই `setPending(pending - 1)` হয়ে `setPending(-1)`, যা ভুল। যেহেতু আপনি কাউন্টারগুলিকে *বৃদ্ধি* বা *হ্রাস* করতে চান, ক্লিক করার সময় নির্ধারিত একটি কংক্রিট মান সেট করার পরিবর্তে, আপডেটার ফাংশনগুলি পাস করতে পারেন:

<Sandpack>

```js
import { useState } from 'react';

export default function RequestTracker() {
  const [pending, setPending] = useState(0);
  const [completed, setCompleted] = useState(0);

  async function handleClick() {
    setPending(p => p + 1);
    await delay(3000);
    setPending(p => p - 1);
    setCompleted(c => c + 1);
  }

  return (
    <>
      <h3>
        পেন্ডিং: {pending}
      </h3>
      <h3>
        সম্পূর্ণ: {completed}
      </h3>
      <button onClick={handleClick}>
        কিনুন     
      </button>
    </>
  );
}

function delay(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms);
  });
}
```

</Sandpack>

এটি নিশ্চিত করে যে আপনি যখন একটি কাউন্টার বৃদ্ধি বা হ্রাস করেন, আপনি প্রথম ক্লিক করার সময় state টি কী ছিল তার চেয়ে আপনি এটির সর্বশেষ অবস্থার সাথে সম্পর্কিত করতে পারেন।

</Solution>

#### State এর সারি আপনি নিজেই তৈরি করুন {/*implement-the-state-queue-yourself*/}

এই চ্যালেঞ্জে, আপনি স্ক্র্যাচ থেকে state এর একটি ছোট অংশ পুনরায় প্রয়োগ করবেন! এটা যতটা কঠিন মনে হচ্ছে ততটা কঠিন নয়।

স্যান্ডবক্সের পূর্বরূপ স্ক্রোল করুন। লক্ষ্য করুন যে এটি **চারটি টেস্ট কেস দেখায়।** এগুলি আপনি এই পৃষ্ঠায় আগে দেখা উদাহরণগুলির সাথে মিলে যায়৷ আপনার কাজ হল `getFinalState` ফাংশন বাস্তবায়ন করা যাতে এটি প্রতিটি ক্ষেত্রে সঠিক ফলাফল প্রদান করে। আপনি যদি এটি সঠিকভাবে বাস্তবায়ন করেন তবে চারটি পরীক্ষাই পাস করা উচিত.

আপনি দুটি আর্গুমেন্ট পাবেন: `বেসস্টেট` হল প্রারম্ভিক অবস্থা (যেমন `0`), এবং `সারি` হল একটি অ্যারে যাতে সংখ্যার মিশ্রণ থাকে (যেমন `5`) এবং আপডেটার ফাংশন (যেমন `n => n + 1`) ক্রমানুসারে তাদের যোগ করা হয়েছে।

আপনার টাস্ক হল চূড়ান্ত state রিটার্ন করা, যাতে ঠিক এই পৃষ্ঠার টেবিলের মত দেখায়!

<Hint>

আপনি যদি আটকে জান তবে এই কোড কাঠামো দিয়ে শুরু করুন:

```js
export function getFinalState(baseState, queue) {
  let finalState = baseState;

  for (let update of queue) {
    if (typeof update === 'function') {
      // TODO: apply the updater function
    } else {
      // TODO: replace the state
    }
  }

  return finalState;
}
```

অনুপস্থিত লাইন পূরণ করুন!

</Hint>

<Sandpack>

```js processQueue.js active
export function getFinalState(baseState, queue) {
  let finalState = baseState;

  // TODO: do something with the queue...

  return finalState;
}
```

```js App.js
import { getFinalState } from './processQueue.js';

function increment(n) {
  return n + 1;
}
increment.toString = () => 'n => n+1';

export default function App() {
  return (
    <>
      <TestCase
        baseState={0}
        queue={[1, 1, 1]}
        expected={1}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          increment,
          increment,
          increment
        ]}
        expected={3}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
        ]}
        expected={6}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
          42,
        ]}
        expected={42}
      />
    </>
  );
}

function TestCase({
  baseState,
  queue,
  expected
}) {
  const actual = getFinalState(baseState, queue);
  return (
    <>
      <p>বেসস্টেটঃ <b>{baseState}</b></p>
      <p>সারিঃ <b>[{queue.join(', ')}]</b></p>
      <p>আশানুরূপ ফালাফলঃ <b>{expected}</b></p>
      <p style={{
        color: actual === expected ?
          'green' :
          'red'
      }}>
        আপনার ফলাফলঃ <b>{actual}</b>
        {' '}
        ({actual === expected ?
          'সঠিক' :
          'ভুল'
        })
      </p>
    </>
  );
}
```

</Sandpack>

<Solution>

এটি এই পৃষ্ঠায় বর্ণিত সঠিক অ্যালগরিদম যা react চূড়ান্ত state গণনা করতে ব্যবহার করে:

<Sandpack>

```js processQueue.js active
export function getFinalState(baseState, queue) {
  let finalState = baseState;

  for (let update of queue) {
    if (typeof update === 'function') {
      // Apply the updater function.
      finalState = update(finalState);
    } else {
      // Replace the next state.
      finalState = update;
    }
  }

  return finalState;
}
```

```js App.js
import { getFinalState } from './processQueue.js';

function increment(n) {
  return n + 1;
}
increment.toString = () => 'n => n+1';

export default function App() {
  return (
    <>
      <TestCase
        baseState={0}
        queue={[1, 1, 1]}
        expected={1}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          increment,
          increment,
          increment
        ]}
        expected={3}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
        ]}
        expected={6}
      />
      <hr />
      <TestCase
        baseState={0}
        queue={[
          5,
          increment,
          42,
        ]}
        expected={42}
      />
    </>
  );
}

function TestCase({
  baseState,
  queue,
  expected
}) {
  const actual = getFinalState(baseState, queue);
  return (
    <>
      <p>বেসস্টেটঃ <b>{baseState}</b></p>
      <p>সারিঃ <b>[{queue.join(', ')}]</b></p>
      <p>আশানুরূপ ফালাফলঃ <b>{expected}</b></p>
      <p style={{
        color: actual === expected ?
          'green' :
          'red'
      }}>
        আপনার ফলাফলঃ <b>{actual}</b>
        {' '}
        ({actual === expected ?
          'সঠিক' :
          'ভুল'
        })
      </p>
    </>
  );
}
```

</Sandpack>

এখন আপনি জানেন কিভাবে react এই অংশ কাজ করে!

</Solution>

</Challenges>