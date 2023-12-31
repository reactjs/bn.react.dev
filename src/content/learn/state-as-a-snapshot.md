---
title: স্ন্যাপশট হিসেবে State
---

<Intro>

State ভ্যারিয়েবলকে সাধারণ জাভাস্ক্রিপ্ট ভ্যারিয়েবলের মতো রিড এবং রাইট করতে পারলেও এটি আসলে একটি স্ন্যাপশটের মতো আচরণ করে। state ভ্যারিয়েবল সেট করলে তার পূর্ববর্তী মান পরিবর্তন না হলেও একটি রেন্ডার ট্রিগার হয়।

</Intro>

<YouWillLearn>

* State সেট করলে কিভাবে রেন্ডার ট্রিগার হয়
* কখন এবং কিভাবে state আপডেট হয়
* সেট করার সাথে সাথেই কেন state আপডেট হয় না
* Event handler কিভাবে একটি স্ন্যাপশট এক্সেস করে

</YouWillLearn>

## State সেট করলে রি-রেন্ডার ট্রিগার হয় {/*setting-state-triggers-renders*/}

আপনি হয়ত ভাবতে পারেন ইউজার ইন্টারফেস ক্লিক এর মতো ইউজার ইভেন্টের সরাসরি প্রতিক্রিয়া হিসেবে পরিবর্তন হয়। কিন্তু React-এ এটি একটু অন্য ভাবে কাজ করে। আগের পেইজে দেখেছেন [state সেট করলে রি-রেন্ডার ট্রিগার হয়](/learn/render-and-commit#step-1-trigger-a-render)। তার মানে কোন ইভেন্টের প্রতিক্রিয়া পেতে আপনার আগে *State আপডেট* করতে হবে।

এই উদাহরণে "send" বাটনে চাপলে `setIsSent(true)` এর মাধ্যমে React-কে রি-রেন্ডার করতে জানানো হবেঃ

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [isSent, setIsSent] = useState(false);
  const [message, setMessage] = useState('Hi!');
  if (isSent) {
    return <h1>Your message is on its way!</h1>
  }
  return (
    <form onSubmit={(e) => {
      e.preventDefault();
      setIsSent(true);
      sendMessage(message);
    }}>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit">Send</button>
    </form>
  );
}

function sendMessage(message) {
  // ...
}
```

```css
label, textarea { margin-bottom: 10px; display: block; }
```

</Sandpack>

বাটনটি ক্লিক করলে নিম্নলিখিত ঘটনাগুলো ঘটেঃ

1. `onSubmit` event handler এক্সিকিউট হয়।
2. `setIsSent(true)` এর মাধ্যমে `isSent` এর মান true করা হয় এবং একটি নতুন রি-রেন্ডার queue করা হয়।
3. React নতুন `isSent` মান অনুযায়ী কম্পোনেন্টটি পুনরায় রি-রেন্ডার করে।

এখন state এবং রেন্ডারিং এর মধ্যে সম্পর্কটিতে নজর দেই।

## রেন্ডারিং সময়ের একটি স্ন্যাপশট নেয় {/*rendering-takes-a-snapshot-in-time*/}

কম্পোনেন্ট হল একটি ফাংশন। আপনার কম্পোনেন্ট React-এর দ্বারা কল হওয়াকেই ["রেন্ডারিং"](/learn/render-and-commit#step-2-react-renders-your-components) বলে। সেই ফাংশন থেকে রিটার্ন করা JSX টি ঐ মুহূর্তের একটি স্ন্যাপশটের মতো। এর সমস্ত props, event handler এবং লোকাল ভ্যারিয়েবলগুলো **রেন্ডার করার সময়ের State ব্যবহার করে ক্যালকুলেট করা হয়েছিল।**

আপনার কম্পোনেন্ট থেকে রিটার্ন হওয়া "স্ন্যাপশট" একটি ছবি বা একটি মুভির ফ্রেমের মতো না, এটি ইন্টারেক্টিভ। ইনপুটের উত্তর হিসাবে কী ঘটবে তার লজিক event handler-এ দেওয়া আছে। React স্ন্যাপশটের সাথে মেলানোর জন্য স্ক্রিনটি আপডেট করে এবং event handler-গুলো UI-এর সাথে কানেক্ট করে। ফলস্বরূপ, আপনার JSX থেকে একটি বাটন চাপলে click handler-টি ট্রিগার হয়।

যখন React কোন কম্পোনেন্ট রি-রেন্ডার করেঃ

1. React আবার আপনার ফাংশনটি কল করে।
2. আপনার ফাংশন একটি নতুন JSX স্ন্যাপশট রিটার্ন করে।
3. তারপরে React আপনার ফাংশনের রিটার্ন করা স্ন্যাপশটের সাথে মেলানোর জন্য স্ক্রিনটি আপডেট করে।

<IllustrationBlock sequential>
    <Illustration caption="React ফাংশন এক্সিকিউট করছে" src="/images/docs/illustrations/i_render1.png" />
    <Illustration caption="স্ন্যাপশট ক্যালকুলেট করছে" src="/images/docs/illustrations/i_render2.png" />
    <Illustration caption="DOM-ট্রি আপডেট করছে" src="/images/docs/illustrations/i_render3.png" />
</IllustrationBlock>

State একটি নিয়মিত ভ্যারিয়েবলের মতো নয় যা আপনার ফাংশন রিটার্ন করার পরে হারিয়ে যায়। বরং state React-এর ভেতরে বাস করে--আপনার ফাংশনের বাইরে! React কম্পোনেন্টটি কল করলে এটি আপনাকে ঐ রেন্ডারের সময়ের State-এর একটি স্ন্যাপশট দেয়। আপনার কম্পোনেন্ট তার নতুন props এবং event handler সহ একটি নতুন স্ন্যাপশট ফেরত দেয়, যা আপনার **ঐ রেন্ডারের সময়ের State-এর ভ্যালুগুলি ব্যবহার করে ক্যালকুলেট করা হয়!**

<IllustrationBlock sequential>
  <Illustration caption="React-কে State আপডেট করতে বলা হচ্ছে" src="/images/docs/illustrations/i_state-snapshot1.png" />
  <Illustration caption="React State-এর ভ্যালু আপডেট করছে" src="/images/docs/illustrations/i_state-snapshot2.png" />
  <Illustration caption="রিয়েক্ট কম্পোনেন্টে State ভ্যালুর স্ন্যাপশট পাঠায়।" src="/images/docs/illustrations/i_state-snapshot3.png" />
</IllustrationBlock>

নিচে একটি ছোট এক্সপিরিমেন্টে এটি কিভাবে কাজ করে তা দেখানো হল। এই উদাহরণে আপনি যেহেতু তিনবার `setNumber(number + 1)` কল করবেন, আপনি হয়ত ধরে নিতে পারেন যে "+3" বোতামে ক্লিক করলে কাউন্টারটি তিনবার ইনক্রিমেন্ট হবে।

"+3" বাটনটি ক্লিক করে দেখুন কী হয়ঃ

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
      }}>+3</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

লক্ষ্য করুন যে `number` প্রতি ক্লিকে কেবল একবার বাড়ে!

**State সেট করা কেবল পরের রেন্ডারের জন্য এটি পরিবর্তন করে।** প্রথম রেন্ডারে, `number` ছিল `0`। এ কারণে, *সেই রেন্ডারের* `onClick` হ্যান্ডলারে, `setNumber(number + 1)` কল করার পরেও `number`-এর মান `0` থাকেঃ

```js
<button onClick={() => {
  setNumber(number + 1);
  setNumber(number + 1);
  setNumber(number + 1);
}}>+3</button>
```

Here is what this button's click handler tells React to do:

1. `setNumber(number + 1)`: `number` হচ্ছে `0` তাই `setNumber(0 + 1)`.
    - React `number` কে পরের রেন্ডারে `1` এ বদলানোর প্রস্তুতি নেয়।
2. `setNumber(number + 1)`: `number` হচ্ছে `0` তাই `setNumber(0 + 1)`.
    - React `number` কে পরের রেন্ডারে `1` এ বদলানোর প্রস্তুতি নেয়।
3. `setNumber(number + 1)`: `number` হচ্ছে `0` তাই `setNumber(0 + 1)`.
    - React `number` কে পরের রেন্ডারে `1` এ বদলানোর প্রস্তুতি নেয়।

যদিও আপনি `setNumber(number + 1)` তিনবার কল করেছেন, *এই রেন্ডারের* ইভেন্ট হ্যান্ডলারে `number` সবসময় `0` থাকে, তাই আপনি তিনবার state-কে `1`-এ সেট করেন। এই কারণে, আপনার ইভেন্ট হ্যান্ডলার শেষ হওয়ার পরে, React কম্পোনেন্টটি `number`-কে `1` হিসেবে পুনরায় রেন্ডার করে, `3`-এর পরিবর্তে।

আপনি এটি আরও ভালোভাবে বুঝতে পারেন যদি মনে মনে আপনার কোডে state ভ্যারিয়েবলগুলিকে তাদের মানের সাথে প্রতিস্থাপন করেন। যেহেতু `number` state ভ্যারিয়েবল *এই রেন্ডারের জন্য* `0` হয়, তার ইভেন্ট হ্যান্ডলার এরকম দেখায়ঃ

```js
<button onClick={() => {
  setNumber(0 + 1);
  setNumber(0 + 1);
  setNumber(0 + 1);
}}>+3</button>
```

পরের রেন্ডারের জন্য, `number` হল `1`, তাই *সেই রেন্ডারের* ক্লিক হ্যান্ডলার এরকম দেখায়ঃ

```js
<button onClick={() => {
  setNumber(1 + 1);
  setNumber(1 + 1);
  setNumber(1 + 1);
}}>+3</button>
```

এ জন্য বাটনে আবার ক্লিক করলে কাউন্টারটি `2`-এ সেট হয়, তারপর পরের ক্লিকে `3`-এ, এবং এইভাবে চলতে থাকে।

## সময়ের সাথে সাথে state {/*state-over-time*/}

যাই হোক, এটা মজার ছিল। চেষ্টা করুন অনুমান করতে যে এই বাটনে ক্লিক করলে কী অ্যালার্ট হবেঃ

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
        alert(number);
      }}>+5</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

আগের মতো প্রতিস্থাপন পদ্ধতি ব্যবহার করলে, আপনি অনুমান করতে পারেন যে অ্যালার্টে "0" দেখাবেঃ

```js
setNumber(0 + 5);
alert(0);
```

কিন্তু যদি আপনি অ্যালার্টে একটি টাইমার সেট করেন, যাতে কম্পোনেন্ট পুনরায় রেন্ডার হওয়ার _পরে_ এটি ট্রিগার হয়? এটি কি "0" নাকি "5" বলবে? একটি অনুমান করে দেখুন!

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
        setTimeout(() => {
          alert(number);
        }, 3000);
      }}>+5</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

অবাক হয়েছেন? যদি আপনি প্রতিস্থাপন পদ্ধতি ব্যবহার করেন, তাহলে আপনি অ্যালার্টে পাস করা state-এর "স্ন্যাপশট" দেখতে পাবেন।

```js
setNumber(0 + 5);
setTimeout(() => {
  alert(0);
}, 3000);
```

অ্যালার্ট চালানোর সময়ে React-এ থাকা state পরিবর্তন হতে পারে, কিন্তু এটি শিডিউল করা হয়েছিল ব্যবহারকারীর ইন্টারেকশনের সময়ে state এর একটি স্ন্যাপশট ব্যবহার করে!

**একটি state ভ্যারিয়েবলের মান একটি রেন্ডারের মধ্যে কখনই পরিবর্তন হয় না,** এমনকি যদি এর ইভেন্ট হ্যান্ডলারের কোড অ্যাসিঙ্ক্রোনাস হয়। *সেই রেন্ডারের* `onClick`-এ, `setNumber(number + 5)` কল করার পরেও `number`-এর মান `0` থাকে। এর মান "স্থির" হয়ে গেছে যখন React আপনার কম্পোনেন্ট কল করে UI-এর "স্ন্যাপশট" নেয়।

এখানে একটি উদাহরণ রয়েছে যেভাবে এটি আপনার ইভেন্ট হ্যান্ডলারগুলির টাইমিং ভুল হবার সম্ভাবনা কমিয়ে দেয়। নীচে একটি ফর্ম রয়েছে যা পাঁচ সেকেন্ডে পরে একটি বার্তা পাঠায়। এই পরিস্থিতিটি কল্পনা করুনঃ

1. আপনি "Send" বাটনে চাপুন, "Hello" মেসেজটি Alice-এর কাছে পাঠানো হবে।
2. পাঁচ সেকেন্ডের বিলম্ব শেষ হওয়ার আগে, আপনি "To" ফিল্ডের মান Bob-এ পরিবর্তন করেন।

আপনি কী অ্যালার্ট দেখার প্রত্যাশা করেন? এটি কি দেখাবে, "তুমি Alice-কে Hello বলেছো"? নাকি এটি দেখাবে, "তুমি Bob-কে Hello বলেছো"? আপনি যা জানেন তার ভিত্তিতে একটি অনুমান করুন, এবং তারপর এটি চেষ্টা করুনঃ

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [to, setTo] = useState('Alice');
  const [message, setMessage] = useState('Hello');

  function handleSubmit(e) {
    e.preventDefault();
    setTimeout(() => {
      alert(`You said ${message} to ${to}`);
    }, 5000);
  }

  return (
    <form onSubmit={handleSubmit}>
      <label>
        To:{' '}
        <select
          value={to}
          onChange={e => setTo(e.target.value)}>
          <option value="Alice">Alice</option>
          <option value="Bob">Bob</option>
        </select>
      </label>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit">Send</button>
    </form>
  );
}
```

```css
label, textarea { margin-bottom: 10px; display: block; }
```

</Sandpack>

**React এক রেন্ডারের ইভেন্ট হ্যান্ডলারগুলিতে state এর মানগুলি ফিক্সড রাখে।** আপনাকে এটা নিয়ে চিন্তা করতে হবে না যে কোড চলাকালীন state পরিবর্তন হয়েছে কি না।

কিন্তু যদি আপনি পুনরায় রেন্ডারের আগে সর্বশেষ state রিড করতে চান? তাহলে আপনি [state আপডেটার ফাংশন](/learn/queueing-a-series-of-state-updates) ব্যবহার করতে চাইবেন, যা পরের পৃষ্ঠায় আলোচিত হবে!

<Recap>

* state সেট করা নতুন রেন্ডার অনুরোধ করে।
* React আপনার কম্পোনেন্টের বাইরে state রাখে, যেন একটি তাকের উপর।
* আপনি যখন `useState` কল করেন, React আপনাকে *সেই রেন্ডারের* জন্য state এর একটি স্ন্যাপশট দেয়।
* ভ্যারিয়েবল এবং ইভেন্ট হ্যান্ডলারগুলি পুনরায় রেন্ডারে "বেঁচে থাকে না"। প্রতিটি রেন্ডারের নিজস্ব ইভেন্ট হ্যান্ডলার থাকে।
* প্রতিটি রেন্ডার (এবং এর মধ্যে থাকা ফাংশনগুলি) সর্বদা *সেই* রেন্ডারের জন্য React দ্বারা দেওয়া state এর স্ন্যাপশটটি "দেখবে"।
* আপনি ইভেন্ট হ্যান্ডলারে state মনে মনে প্রতিস্থাপন করতে পারেন, ঠিক যেভাবে আপনি রেন্ডার করা JSX সম্পর্কে চিন্তা করেন।
* অতীতে তৈরি করা ইভেন্ট হ্যান্ডলারগুলি সেই রেন্ডারের state মান বহন করে, যে রেন্ডারে তারা তৈরি হয়েছিল।

</Recap>
<Challenges>

#### একটি ট্রাফিক লাইট বাস্তবায়ন করুন {/*implement-a-traffic-light*/}

এখানে একটি পারাপারের লাইট কম্পোনেন্ট রয়েছে যা বাটন চাপা হলে টগল করেঃ

<Sandpack>

```js
import { useState } from 'react';

export default function TrafficLight() {
  const [walk, setWalk] = useState(true);

  function handleClick() {
    setWalk(!walk);
  }

  return (
    <>
      <button onClick={handleClick}>
        Change to {walk ? 'Stop' : 'Walk'}
      </button>
      <h1 style={{
        color: walk ? 'darkgreen' : 'darkred'
      }}>
        {walk ? 'Walk' : 'Stop'}
      </h1>
    </>
  );
}
```

```css
h1 { margin-top: 20px; }
```

</Sandpack>

ক্লিক হ্যান্ডলারে একটি `alert` যোগ করুন। যখন লাইট সবুজ এবং "Walk" বলে, তখন বাটনে ক্লিক করলে "Stop is next" বলা উচিত। যখন লাইট লাল এবং "Stop" বলে, তখন বাটনে ক্লিক করলে "Walk is next" বলা উচিত।

`alert`-টি `setWalk` কলের আগে নাকি পরে রাখলে তা কি কোনো পার্থক্য করে?

<Solution>

আপনার `alert` এরকম দেখানো উচিতঃ

<Sandpack>

```js
import { useState } from 'react';

export default function TrafficLight() {
  const [walk, setWalk] = useState(true);

  function handleClick() {
    setWalk(!walk);
    alert(walk ? 'Stop is next' : 'Walk is next');
  }

  return (
    <>
      <button onClick={handleClick}>
        Change to {walk ? 'Stop' : 'Walk'}
      </button>
      <h1 style={{
        color: walk ? 'darkgreen' : 'darkred'
      }}>
        {walk ? 'Walk' : 'Stop'}
      </h1>
    </>
  );
}
```

```css
h1 { margin-top: 20px; }
```

</Sandpack>

এটি আপনি `setWalk` কলের আগে বা পরে রাখুন, তা কোনো পার্থক্য করে না। সেই রেন্ডারের `walk`-এর মান স্থির। `setWalk` কল করা শুধুমাত্র পরের *রেন্ডারে* এটি পরিবর্তন করবে, কিন্তু আগের রেন্ডারের ইভেন্ট হ্যান্ডলারে কোনো প্রভাব ফেলবে না।

প্রথমে এই লাইনটি counter-intuitive মনে হতে পারেঃ

```js
alert(walk ? 'Stop is next' : 'Walk is next');
```

কিন্তু এটি যদি আপনি এভাবে পড়েন তাহলে অর্থপূর্ণ হয়ঃ "যদি ট্রাফিক লাইট 'Walk now' দেখায়, তাহলে বার্তাটি বলা উচিত 'Stop is next.' আপনার ইভেন্ট হ্যান্ডলারের `walk` ভ্যারিয়েবলটি সেই রেন্ডারের `walk`-এর মানের সাথে মেলে এবং পরিবর্তন হয় না।

আপনি প্রতিস্থাপন পদ্ধতি প্রয়োগ করে এটি সঠিক হয়েছে কি না যাচাই করতে পারেন। যখন `walk` `true`, আপনি পেয়ে যানঃ

```js
<button onClick={() => {
  setWalk(false);
  alert('Stop is next');
}}>
  Change to Stop
</button>
<h1 style={{color: 'darkgreen'}}>
  Walk
</h1>
```

তাহলে "Change to Stop" এ ক্লিক করে `walk` `false` সেট করা একটি রেন্ডার queue করে, এবং "Stop is next" বলে অ্যালার্ট দেয়।

</Solution>

</Challenges>
