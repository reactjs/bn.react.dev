---
title: 'Ref দিয়ে কোন value এর রেফারেন্স দেওয়া'
---

<Intro>

যখন আপনি চান যে একটা কম্পোনেন্ট কোন একটা তথ্য "মনে রাখুক", কিন্তু চান না যে এই তথ্য নতুন কোন [রেন্ডার চালু করে দিক](/learn/render-and-commit), আপনি একটা *ref* ব্যবহার করতে পারেন।

</Intro>

<YouWillLearn>

- কীভাবে কম্পোনেন্টে একটি ref যুক্ত করবেন
- কীভাবে একটি ref এর মান পরিবর্তন করবেন
- state এর সাথে ref এর তফাৎ কোথায়
- কীভাবে নিরাপদভাবে ref ব্যবহার করা যায়

</YouWillLearn>

## আপনার কম্পোনেন্টে ref এর সংযুক্তি {/*adding-a-ref-to-your-component*/}

React থেকে `useRef` hook ইম্পোর্ট করার মাধ্যমে আপনার কম্পোনেন্টে একটি ref যুক্ত করতে পারেনঃ

```js
import { useRef } from 'react';
```

আপনার কম্পোনেন্টের মধ্যে, `useRef` hook-টি কল করুন এবং এর মধ্যে আপনি যেই প্রাথমিক মান reference হিসেবে দিতে চান সেটা একমাত্র argument হিসেবে পাঠিয়ে দিন। উদাহরণস্বরূপ, এখানে  `0` মানটির একটি ref রয়েছেঃ

```js
const ref = useRef(0);
```

`useRef` এমন একটি অবজেক্ট রিটার্ন করেঃ

```js
{ 
  current: 0 // The value you passed to useRef
}
```

<Illustration src="/images/docs/illustrations/i_ref.png" alt="An arrow with 'current' written on it stuffed into a pocket with 'ref' written on it." />

আপনি `ref.current` property-র মাধ্যমে ঐ ref এর বর্তমান মান অ্যাক্সেস করতে পারেন। এই মানটি ইচ্ছাকৃতভাবে পরিবর্তনযোগ্য, অর্থাৎ আপনি এটি read এবং write করতে পারেন। এটি আপনার কম্পোনেন্টের একটি গোপন পকেটের মতো যা React ট্র্যাক করে না। (এই বৈশিষ্ট্যটাই একে React এর একমুখী ডেটা প্রবাহ থেকে একটি "escape hatch" বানায়--নিচে এটি সম্পর্কে আরও তথ্য রয়েছে!)

এখানে, একটি বাটন প্রতিটি ক্লিকে `ref.current` এর মান বাড়াবেঃ

<Sandpack>

```js
import { useRef } from 'react';

export default function Counter() {
  let ref = useRef(0);

  function handleClick() {
    ref.current = ref.current + 1;
    alert('You clicked ' + ref.current + ' times!');
  }

  return (
    <button onClick={handleClick}>
      Click me!
    </button>
  );
}
```

</Sandpack>

ref একটি সংখ্যা নির্দেশ করে, তবে, [state](/learn/state-a-components-memory) এর মত, আপনি যে কোন কিছুর দিকে নির্দেশ করতে পারেনঃ একটি স্ট্রিং, একটি অবজেক্ট, বা এমনকি একটি ফাংশন। state এর বিপরীতে, ref একটি সাধারণ জাভাস্ক্রিপ্ট অবজেক্ট যার `current` property রয়েছে, যা আপনি read করতে এবং পরিবর্তন করতে পারেন।

লক্ষ্য করুন যে **প্রতি increment এর সাথে কম্পোনেন্টটি পুনরায় রেন্ডার হয় না।** state এর মত, রেন্ডারের ফাঁকে ফাঁকে React ref-কে সংরক্ষণ করে। তবে, state সেট করলে একটি কম্পোনেন্ট পুনরায় রেন্ডার হয়। ref এর পরিবর্তনে সেটা হয় না!

## উদাহরণঃ একটি স্টপওয়াচ যেভাবে বানাবেন {/*example-building-a-stopwatch*/}

আপনি একটি কম্পোনেন্টের মধ্যে refs এবং state একসাথে সমন্বয় করতে পারেন। উদাহরণস্বরূপ, চলেন একটি স্টপওয়াচ তৈরি করি যেটি ব্যবহারকারী একটি বাটন চাপের মাধ্যমে শুরু বা বন্ধ করতে পারবে। ব্যবহারকারী "Start" চাপার পরে কতটা সময় পার হয়েছে তা প্রদর্শন করার জন্য, আপনাকে স্টার্ট বোতাম চাপা হয়েছে তার সময় এবং বর্তমান সময় কী তা হিসেব রাখতে হবে। **এই তথ্যটি রেন্ডারিং এর জন্য ব্যবহৃত হয়, তাই আপনি এটি state এ রাখবেন:**

```js
const [startTime, setStartTime] = useState(null);
const [now, setNow] = useState(null);
```

ব্যবহারকারী যখন "Start" চাপবে, আপনি প্রতি 10 মিলিসেকেন্ড পর পর সময় আপডেট করার জন্য [`setInterval`](https://developer.mozilla.org/docs/Web/API/setInterval) ব্যবহার করবেনঃ

<Sandpack>

```js
import { useState } from 'react';

export default function Stopwatch() {
  const [startTime, setStartTime] = useState(null);
  const [now, setNow] = useState(null);

  function handleStart() {
    // Start counting.
    setStartTime(Date.now());
    setNow(Date.now());

    setInterval(() => {
      // Update the current time every 10ms.
      setNow(Date.now());
    }, 10);
  }

  let secondsPassed = 0;
  if (startTime != null && now != null) {
    secondsPassed = (now - startTime) / 1000;
  }

  return (
    <>
      <h1>Time passed: {secondsPassed.toFixed(3)}</h1>
      <button onClick={handleStart}>
        Start
      </button>
    </>
  );
}
```

</Sandpack>

"Stop" বাটন চাপা হলে, আপনাকে বিদ্যমান interval বাতিল করতে হবে যাতে এটি state ভ্যারিয়েবল `now` আপডেট করা বন্ধ করে। আপনি এটি [`clearInterval`](https://developer.mozilla.org/en-US/docs/Web/API/clearInterval) কল করে করতে পারেন, কিন্তু আপনাকে এটিকে সেই interval ID দিতে হবে যা ব্যবহারকারী Start চাপলে পূর্বে `setInterval` কল থেকে রিটার্ন পাওয়া গিয়েছিল। আপনাকে interval ID-টি কোথাও রাখতে হবে। **যেহেতু interval ID রেন্ডারিং এর জন্য ব্যবহৃত হয় না, আপনি এটিকে একটি ref এ রাখতে পারেনঃ**

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Stopwatch() {
  const [startTime, setStartTime] = useState(null);
  const [now, setNow] = useState(null);
  const intervalRef = useRef(null);

  function handleStart() {
    setStartTime(Date.now());
    setNow(Date.now());

    clearInterval(intervalRef.current);
    intervalRef.current = setInterval(() => {
      setNow(Date.now());
    }, 10);
  }

  function handleStop() {
    clearInterval(intervalRef.current);
  }

  let secondsPassed = 0;
  if (startTime != null && now != null) {
    secondsPassed = (now - startTime) / 1000;
  }

  return (
    <>
      <h1>Time passed: {secondsPassed.toFixed(3)}</h1>
      <button onClick={handleStart}>
        Start
      </button>
      <button onClick={handleStop}>
        Stop
      </button>
    </>
  );
}
```

</Sandpack>

রেন্ডারিং এর জন্য একটি তথ্য ব্যবহার করা হলে, এটি state এ রাখুন। যখন কোন তথ্য কেবলমাত্র event handler-গুলি দ্বারা প্রয়োজন হয় এবং এর পরিবর্তনে পুনরায় রেন্ডার করা প্রয়োজন হয় না, সেক্ষেত্রে ref ব্যবহার করা অধিক কার্যকর হতে পারে।

## ref এবং state এর মধ্যকার পার্থক্য {/*differences-between-refs-and-state*/}

হয়তো আপনি মনে করছেন ref, state এর তুলনায় কম "কঠোর" - উদাহরণস্বরূপ, আপনি এগুলোকে পরিবর্তন করতে পারেন যেখানে state-এর ক্ষেত্রে সর্বদা state সেটিং ফাংশন ব্যবহার করার প্রয়োজন। কিন্তু বেশিরভাগ ক্ষেত্রে, আপনি state-ই ব্যবহার করতে চাইবেন। Ref গুলো একটি "escape hatch" যা আপনার খুব একটা প্রয়োজন হবে না। এখানে state এবং ref এর তুলনা কিভাবে হয় তা দেখুনঃ

| refs                                                                                  | state                                                                                                                     |
| ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `useRef(initialValue)` রিটার্ন করে `{ current: initialValue }`                            | `useState(initialValue)` রিটার্ন করে একটি stat variable এর বর্তমান মান এবং একটি state setter function ( `[value, setValue]`) |
|  যখন আপনি এর পরিবর্তন করেন, re-render ট্রিগার করে না।                                   | এর পরিবর্তন করা হলে re-render ট্রিগার করে                                                                                    |
| পরিবর্তনযোগ্য—রেন্ডারিং প্রক্রিয়ার বাইরে আপনি `current` এর মান পরিবর্তন করে আপডেট করতে পারবেন।| "পরিবর্তনযোগ্য নয়"—একটা re-render, queue এ ঢুকানোর জন্য আপনাকে অবশ্যই state setting function ব্যবহার করে state variable পরিবর্তন করতে হবে।                       |
| রেন্ডারিং এর সময় `current` এর মান আপনার read বা write করা উচিত নয়। | আপনি যেকোন সময়ে state read করতে পারেন। কিন্তু প্রতি রেন্ডারের নিজের state এর [snapshot](/learn/state-as-a-snapshot) আছে যা বদলায় না।

এখানে state ব্যবহার করে বানানো একটি counter বাটন দেখুনঃ

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      You clicked {count} times
    </button>
  );
}
```

</Sandpack>

যেহেতু `count` এর মানটি দেখানো হয়, এর জন্য একটি state মান ব্যবহার করা যুক্তিযুক্ত। যখন counter-এর মানটি `setCount()` দিয়ে সেট করা হয়, React কম্পোনেন্টটি পুনরায় রেন্ডার করে এবং স্ক্রিন নতুন কাউন্ট দেখানোর জন্য আপডেট হয়।

যদি আপনি এটি ref দিয়ে বানানোর করার চেষ্টা করতেন, তাহলে React কখনই কম্পোনেন্টটি পুনরায় রেন্ডার করত না, তাই আপনি কখনই কাউন্টের পরিবর্তন দেখতেন না! দেখুন এই বাটনে ক্লিক করলে কীভাবে এর **টেক্সট আপডেট হয় নাঃ**

<Sandpack>

```js
import { useRef } from 'react';

export default function Counter() {
  let countRef = useRef(0);

  function handleClick() {
    // This doesn't re-render the component!
    countRef.current = countRef.current + 1;
  }

  return (
    <button onClick={handleClick}>
      You clicked {countRef.current} times
    </button>
  );
}
```

</Sandpack>

এ কারণেই রেন্ডারের সময় `ref.current` read করলে সেটা কোডের নির্ভরযোগ্যতা কমিয়ে ফেলে। যদি আপনার সেটা করার প্রয়োজন হয়, বরং state ব্যবহার করুন।

<DeepDive>

#### useRef ভিতরে ভিতরে কীভাবে কাজ করে? {/*how-does-use-ref-work-inside*/}

যদিও `useState` এবং `useRef` উভয়ই React দেয়, মূলত `useRef`,  `useState` এর _উপরে_ ব্যবহার করা যেতে পারে। আপনি কল্পনা করতে পারেন যে React এর মধ্যে, `useRef` এর বাস্তবায়ন এরকমঃ

```js
// Inside of React
function useRef(initialValue) {
  const [ref, unused] = useState({ current: initialValue });
  return ref;
}
```

প্রথম রেন্ডারের সময় `useRef` রিটার্ন করে `{ current: initialValue }`। এই অবজেক্টটি React সংরক্ষণ করে, সুতরাং পরবর্তী রেন্ডারের সময় একই অবজেক্টটি রিটার্ন করে। লক্ষ্য করুন যে এই উদাহরণে state setter ব্যবহার করা হয়নি। এটি অপ্রয়োজনীয় কারণ `useRef` এর সর্বদা একই অবজেক্ট ফিরিয়ে দেওয়া প্রয়োজন!

React একটি built-in `useRef` দেয় কারণ সাধারণত এর ব্যবহার বেশ ভালই হয়। কিন্তু আপনি এটিকে একটি সাধারণ state ভ্যারিয়েবল হিসাবে চিন্তা করতে পারেন যার কোনও সেটার নেই। যদি আপনি object-oriented programming এর সাথে পরিচিত হন, তাহলে ref আপনাকে instance fields এর কথা মনে করিয়ে দিতে পারে -- কিন্তু `this.something` এর পরিবর্তে আপনি এক্ষেত্রে `somethingRef.current` লিখছেন।

</DeepDive>

## কখন ref ব্যবহার করবেন {/*when-to-use-refs*/}

সাধারণত, আপনি একটি ref ব্যবহার করবেন যখন আপনার component এর React এর বাইরে "পা রাখতে হবে" এবং বাইরের API এর সাথে যোগাযোগ করতে হবে - প্রায়শই এটা হবে একটি ব্রাউজার API যা কম্পোনেন্টের চেহারার উপর প্রভাব ফেলবে না। এখানে কিছু পরিস্থিতির উদাহরণ দেওয়া হচ্ছে যার দেখা খুব হঠাতই হয়ত মিলবেঃ

- [timeout IDs](https://developer.mozilla.org/docs/Web/API/setTimeout) store করতে।
- [DOM elements](https://developer.mozilla.org/docs/Web/API/Element) store করা এবং সেখানে পরিবর্তন আনা, এটা আমরা [পরের পাতায়](/learn/manipulating-the-dom-with-refs) বর্ণনা করেছি।
- অন্যান্য অব্জেক্ট store করা যা JSX হিসেব করতে প্রয়োজন পড়ে না।

যদি আপনার কম্পোনেন্টে কোন মান store করার দরকার পড়ে, এবং এটা রেন্ডার করার হিসেবে কোন প্রভাব না ফেলে তবে ref ব্যবহার করুন।

## ref ব্যবহারের ক্ষেত্রে যা যা মেনে চলা ভাল {/*best-practices-for-refs*/}

নিম্নোক্ত মূলনীতিগুলো মাথায় রাখলে আপনার কম্পোনেন্টগুলো আরো বেশি নির্ভরযোগ্য আচরণ করবেঃ

- **ref কে escape hatch হিসেবে ব্যবহার করুন।** যখন আপনি বাইরের কোন সিস্টেম বা ব্রাউজার API ব্যবহার করছেন তখন ref বেশ কাজের। যদি আপনার অ্যাপ্লিকেশনের বেশিরভাগ লজিক এবং ডেটা প্রবার ref এর উপর নির্ভরশীল হয় তবে আপনার উচিত আপনার আগানোর প্রক্রিয়া নিয়ে আবার ভাবা।
- **রেন্ডারিং এর সময় `ref.current` read বা write করবেন না।** যদি রেন্ডারিং এর সময় কোন তথ্যের প্রয়োজন পড়ে, তখন বরঞ্চ [state](/learn/state-a-components-memory) ব্যবহার করুন। যেহেতু React জানে না কখন `ref.current` বদলায়, রেন্ডারিং এর সময়ে একে এমনকি read করতে গেলেও আপনার কম্পোনেন্টের আচার আচরণ বোঝা কঠিন হয়ে যাবে। ( এর একমাত্র ব্যতিক্রম হবে তখন যখন আপনি `if (!ref.current) ref.current = new Thing()` এভাবে কোড করছেন, যা একদম প্রথম রেন্ডারের সময়ে ref সেট করে। )

React state এর যে সীমাবদ্ধতা তা ref এর নেই। উদাহরণস্বরূপ, state [প্রতিটি রেন্ডারের একটি স্ন্যাপশটের](/learn/state-as-a-snapshot) মত কাজ করে এবং [synchronously আপডেট করে না।](/learn/queueing-a-series-of-state-updates) কিন্তু আপনি যখন একটি ref এর বর্তমান মান পরিবর্তন করেন, তখন তা সাথে সাথে পরিবর্তিত হয়।

```js
ref.current = 5;
console.log(ref.current); // 5
```

এর কারণ **ref নিজেই একটি সাধারণ জাভাস্ক্রিপ্ট অব্জেক্ট,** তাই এটা তেমনই আচরণ করে।

যখন আপনি ref নিয়ে কাজ করবেন আপনাকে  [mutation এড়ানো](/learn/updating-objects-in-state) নিয়েও দুশ্চিন্তা করতে হবে না। যতক্ষণ পর্যন্ত আপনি যেই অব্জেক্ট mutate করছেন সেটা রেন্ডারিং এ ব্যবহৃত হচ্ছে, ততক্ষণ আপনি ref বা এর content নিয়ে কী করছেন তা নিয়ে React পরোয়া করবে না।

## Ref এবং DOM {/*refs-and-the-dom*/}

আপনি যে কোনও মানের জন্য একটি ref নির্দেশ করতে পারেন। যদিও, একটি ref ব্যবহার করার সবচেয়ে সাধারণ ক্ষেত্র হল DOM element অ্যাক্সেস করা। উদাহরণস্বরূপ, এটি কাজে লাগে যদি আপনি একটি input কে প্রোগ্রামের মাধ্যমে focus করতে চান। যখন আপনি JSX এ একটি `ref` attribute-এ একটি ref pass করেন, যেমন `<div ref={myRef}>`, React `myRef.current` এ সংশ্লিষ্ট DOM এলিমেন্ট রাখবে। যখন DOM থেকে এলিমেন্টটি সরিয়ে ফেলা হয়, React `myRef.current` এর মান আপডেট করে `null` করে দেবে। আপনি এই বিষয়ে আরও পড়তে পারেন [ref এর সাহায্যে DOM manipulation](/learn/manipulating-the-dom-with-refs) অংশে।

<Recap>

- Ref একটি escape hatch যা রেন্ডারিং এর জন্য ব্যবহৃত না হওয়া মানগুলি ধরে রাখতে সাহায্য করে। আপনার একে খুব একটা প্রয়োজন পড়বে না।
- একটি ref হল একটি সাধারণ জাভাস্ক্রিপ্ট অব্জেক্ট যার একটি মাত্র property `current` রয়েছে। এটা আপনি read করতে বা সেট করতে পারেন।
- আপনি `useRef` Hook কল করার মাধ্যমে React এর কাছ থেকে একটি ref চাইতে পারেন।
- State এর মত, ref re-render এর মধ্যবর্তী সময়ে তথ্য সংরক্ষণ করার সুযোগ দেয়।
- State এর বিপরীতে, ref এর  `current` মান সেট করা হলে re-render ট্রিগার হয় না।
- রেন্ডারিং এর সময়ে `ref.current` read বা write করবেন না। এতে আপনার কম্পোনেন্টের গতিবিধি বোঝা কঠিন হয়ে যায়।

</Recap>



<Challenges>

#### অকার্যকর একটি চ্যাট ইনপুট ঠিক করুন {/*fix-a-broken-chat-input*/}

একটি বার্তা লিখুন এবং "Send" ক্লিক করুন। আপনি লক্ষ্য করবেন যে "Sent!" এলার্ট দেখার আগে একটি তিন সেকেন্ডের বিলম্ব রয়েছে। এই বিলম্বের সময়, আপনি একটি "Undo" বাটন দেখতে পারবেন। এটি ক্লিক করুন। এই "Undo" বাটনটি "Sent!" এলার্ট সামনে আসতে বাধা দেবার কথা। সে এটা করে `handleSend` এর সময়ে সেইভ হওয়া timeout ID এর জন্য [`clearTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/clearTimeout) কল করার মাধ্যমে। তবে, "Undo" ক্লিক করার পরেও, "Sent!" লেখাটা এখনও সামনে আসছে। কেন এটি কাজ করছে না তা খুঁজে বের করুন এবং ঠিক করুন।

<Hint>

`let timeoutID` এর মত সাধারণ ভ্যারিয়েবল re-render এর মধ্যবর্তী সময়ে "বেঁচে থাকতে পারে না, কারণ প্রতিটা রেন্ডার আপনার কম্পোনেন্টকে একদম শুরু থেকে run করে (এবং এর ভ্য্যারিয়েবলগুলো initialize করে)। আপনার কি তাহলে timeout ID অন্য কোথাও রাখা উচিত?" 

</Hint>

<Sandpack>

```js
import { useState } from 'react';

export default function Chat() {
  const [text, setText] = useState('');
  const [isSending, setIsSending] = useState(false);
  let timeoutID = null;

  function handleSend() {
    setIsSending(true);
    timeoutID = setTimeout(() => {
      alert('Sent!');
      setIsSending(false);
    }, 3000);
  }

  function handleUndo() {
    setIsSending(false);
    clearTimeout(timeoutID);
  }

  return (
    <>
      <input
        disabled={isSending}
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button
        disabled={isSending}
        onClick={handleSend}>
        {isSending ? 'Sending...' : 'Send'}
      </button>
      {isSending &&
        <button onClick={handleUndo}>
          Undo
        </button>
      }
    </>
  );
}
```

</Sandpack>

<Solution>

যখনই আপনার কম্পোনেন্ট re-render হয় (যেমন যখন আপনি state set করেন), সকল local ভ্যারিয়েবল একদম শুরু থেকে initialized হয়। এ কারণেই আপনি `timeoutID`এর মত একটা local ভ্যারিয়েবলে timeout ID রেখে এটা আশা করতে পারবেন না যে অন্য একটা event handler এটাকে ভবিষ্যতে "দেখবে"। বরং, একে একটা ref এ রাখেন, যা React রেন্ডার এর মধ্যবর্তী সময়ে সংরক্ষিত রাখবে।

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Chat() {
  const [text, setText] = useState('');
  const [isSending, setIsSending] = useState(false);
  const timeoutRef = useRef(null);

  function handleSend() {
    setIsSending(true);
    timeoutRef.current = setTimeout(() => {
      alert('Sent!');
      setIsSending(false);
    }, 3000);
  }

  function handleUndo() {
    setIsSending(false);
    clearTimeout(timeoutRef.current);
  }

  return (
    <>
      <input
        disabled={isSending}
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button
        disabled={isSending}
        onClick={handleSend}>
        {isSending ? 'Sending...' : 'Send'}
      </button>
      {isSending &&
        <button onClick={handleUndo}>
          Undo
        </button>
      }
    </>
  );
}
```

</Sandpack>

</Solution>


#### Re-render করতে পারছে না এমন একটি কম্পোনেন্ট ঠিক করুন {/*fix-a-component-failing-to-re-render*/}

এই বাটনটির "On" এবং "Off" এর মধ্যে toggle করার কথা। তবে, এটা সব সময়ই "Off" দেখায়। এই কোডের সমস্যাটা কী? এটা ঠিক করুন।

<Sandpack>

```js
import { useRef } from 'react';

export default function Toggle() {
  const isOnRef = useRef(false);

  return (
    <button onClick={() => {
      isOnRef.current = !isOnRef.current;
    }}>
      {isOnRef.current ? 'On' : 'Off'}
    </button>
  );
}
```

</Sandpack>

<Solution>

এই উদাহরণে, একটি ref এর বর্তমান মান রেন্ডারিং আউটপুট হিসেব করতে ব্যবহৃত হয়েছেঃ `{isOnRef.current ? 'On' : 'Off'}`। এটি একটি ইঙ্গিত যে এই তথ্যটি একটি ref এ থাকা উচিত ছিল না এবং এর পরিবর্তে state-এ রাখা উচিত ছিল। এটি ঠিক করার জন্য, ref টি সরান এবং এর পরিবর্তে state ব্যবহার করুনঃ

<Sandpack>

```js
import { useState } from 'react';

export default function Toggle() {
  const [isOn, setIsOn] = useState(false);

  return (
    <button onClick={() => {
      setIsOn(!isOn);
    }}>
      {isOn ? 'On' : 'Off'}
    </button>
  );
}
```

</Sandpack>

</Solution>

#### Debouncing ঠিক করুন {/*fix-debouncing*/}

এই উদাহরণে, সকল বাটন click handlers-রা ["debounced".](https://redd.one/blog/debounce-vs-throttle) এর অর্থ বুঝার জন্য, একটা বাটনে চাপ দিন। খেয়াল করুন কীভাবে এক সেকেন্ড পর একটা মেসেজ দেখা যায়। আপনি যদি অপেক্ষার সময়ে বাটনে চাপ দিন, তাহলে timer রিসেট হয়ে যায়। সুতরাং আপনি যদি খুব কম সময়ের ব্যবধানে বার বার বাটনে ক্লিক দিতে থাকেন তাহলে আপনি ক্লিক করা বন্ধ করার এক সেকেন্ড *পর* পর্যন্ত মেসেজ দেখবেন না। "কেউ কিছু করা বন্ধ করা" পর্যন্ত কোন কাজ বিলম্ব করার জন্য debouncing সাহায্য কয়রে। 

এই উদাহরণটা কাজ করছে, তবে যেভাবে করা উচিত ঠিক সেভাবে না। বাটনগুলো স্বাধীন না। সমস্যাটা দেখার জন্য, প্রথমে একটি বাটন ক্লিক করুন এবং খুব দ্ররুত আরেকটি বাটন ক্লিক করুন। আপনি আশা করবেন যে, একটু বিলম্বের পর দুটো বাটনের মেসেজই দেখা যাবে। কিন্তু শুধু শেষ বাটনের মেসেজ দেখা যায়। প্রথম বাটনের মেসেজ হারিয়ে যায়।

বাটনগুলো নিজেদের মধ্যে ঝামেলা করছে কেন? সমস্যাটা খুঁজে বের করুন এবং ঠিক করুন।

<Hint>

শেষ timeout ID variable সকল `DebouncedButton` কম্পোনেন্টের মধ্যে শেয়ার করা আছে। এজন্য একটা বাটন ক্লিক করলে অন্য বাটনের timeout রিসেট হয়ে যায়। আপনি কি প্রতিটি বাটনের জন্য আলাদা timeout ID রাখতে পারবেন?

</Hint>

<Sandpack>

```js
let timeoutID;

function DebouncedButton({ onClick, children }) {
  return (
    <button onClick={() => {
      clearTimeout(timeoutID);
      timeoutID = setTimeout(() => {
        onClick();
      }, 1000);
    }}>
      {children}
    </button>
  );
}

export default function Dashboard() {
  return (
    <>
      <DebouncedButton
        onClick={() => alert('Spaceship launched!')}
      >
        Launch the spaceship
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Soup boiled!')}
      >
        Boil the soup
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Lullaby sung!')}
      >
        Sing a lullaby
      </DebouncedButton>
    </>
  )
}
```

```css
button { display: block; margin: 10px; }
```

</Sandpack>

<Solution>

`timeoutID` এর মত একটি ভ্যারিয়েবল সকল কম্পোনেন্টের মধ্যে শেয়ার করা থাকে। যেই কারণে দ্বিতীয় বাটনে ক্লিক করা হলে প্রথম বাটনের অপেক্ষারত timeout রিসেট হয়ে যায়। এটা ঠিক করার জন্য আপনি timeout একটা ref এ রাখতে পারেন। প্রতিটি বাটন নিজের ref পাবে, সুতরাং তাদের নিজেদের মধ্যে কোন দ্বন্দ্ব তৈরি হবে না। লক্ষ্য করে দেখুন যে দুইটা বাটনই খুব দ্রুত চাপ দিলে দুটো মেসেজই দেখা যায়।

<Sandpack>

```js
import { useRef } from 'react';

function DebouncedButton({ onClick, children }) {
  const timeoutRef = useRef(null);
  return (
    <button onClick={() => {
      clearTimeout(timeoutRef.current);
      timeoutRef.current = setTimeout(() => {
        onClick();
      }, 1000);
    }}>
      {children}
    </button>
  );
}

export default function Dashboard() {
  return (
    <>
      <DebouncedButton
        onClick={() => alert('Spaceship launched!')}
      >
        Launch the spaceship
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Soup boiled!')}
      >
        Boil the soup
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Lullaby sung!')}
      >
        Sing a lullaby
      </DebouncedButton>
    </>
  )
}
```

```css
button { display: block; margin: 10px; }
```

</Sandpack>

</Solution>

#### সর্বষেশ state read করুন {/*read-the-latest-state*/}

এই উদাহরণে, আপনি "Send"-এ চাপ দেবার পরে, মেসেজ দেখাবার আগে ছোট্ট একটু বিলম্ব আছে। "hello" লিখুন, Send এ চাপ দিন, এবং তখন খুব দ্ররুত ইনপুটটা বদলে ফেলুন। আপনি এডিট করার পরও, এলার্টে তখনো "hello" দেখাবে (যেটা state এর মান তখন ছিল [যখন](/learn/state-as-a-snapshot#state-over-time) বাটনটি ক্লিক করা হয়).

সাধারণত, আপনি একটা অ্যাপে এই আচরণটাই চান। কিন্তু, কখনো কখনো কিছু ক্ষেত্রে আপনি চাইবেন যে কোন একটা state এর *সর্বশেষ* মানটা কোন একটা asynchronous কোড read করতে পারুক। আপনি কি কোন এমন উপায় চিন্তা করতে পারবেন যাতে এলার্টটি ক্লিকের সময়কার ইনপুট টেক্সট না দেখেই *এখনকার* ইনপুট টেক্সট দেখায়?  

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Chat() {
  const [text, setText] = useState('');

  function handleSend() {
    setTimeout(() => {
      alert('Sending: ' + text);
    }, 3000);
  }

  return (
    <>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button
        onClick={handleSend}>
        Send
      </button>
    </>
  );
}
```

</Sandpack>

<Solution>

State [একটা স্ন্যাপশটের মত](/learn/state-as-a-snapshot) কাজ করে, সুতরাং আপনি timeout এর মত একটা asynchronous কাজ থেকে সর্বশেষ state read করতে পারবেন না। কিন্তু আপনি সর্বশেষ ইনপুট টেক্সট একটা ref এ রাখতে পারবেন। একটা ref পরিবর্তনযোগ্য, সুতরাং আপনি `current` property যেকোন সময়ে read করতে পারেন। যেহেতু বর্তমান টেক্সটটা রেন্ডারিং এর কাজেও লাগে, এই উদাহরণে, আপনার একটা state ভ্যারিয়েবল (রেন্ডারিং এর জন্য), *এবং* একটা ref (timeout এর মধ্যে read করার জন্য) *উভয়ই* লাগবে। বর্তমান ref এর মানটা আপনাকে নিজে আপডেট করতে হবে। 

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Chat() {
  const [text, setText] = useState('');
  const textRef = useRef(text);

  function handleChange(e) {
    setText(e.target.value);
    textRef.current = e.target.value;
  }

  function handleSend() {
    setTimeout(() => {
      alert('Sending: ' + textRef.current);
    }, 3000);
  }

  return (
    <>
      <input
        value={text}
        onChange={handleChange}
      />
      <button
        onClick={handleSend}>
        Send
      </button>
    </>
  );
}
```

</Sandpack>

</Solution>

</Challenges>
