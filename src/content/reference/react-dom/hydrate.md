---
title: hydrate
---

<Deprecated>

এই API ভবিষ্যতে React এর একটি মেজর ভার্সনে সরিয়ে ফেলা হবে।

React 18 এ, `hydrate` এর জায়গায় এসেছে [`hydrateRoot`।](/reference/react-dom/client/hydrateRoot) React 18 এ `render` ব্যবহার করলে সতর্কতা দেখাবে যে, আপনার অ্যাপ এমন আচরণ করবে যেন এতে React 17 চলছে। আরো জানুন [এখানে।](/blog/2022/03/08/react-18-upgrade-guide#updates-to-client-rendering-apis)

</Deprecated>

<Intro>

`hydrate` আপনাকে React component এমন ব্রাউজার DOM নোডের মধ্যে ডিসপ্লে করার সুযোগ দেয় যার HTML কনটেন্ট React 17 বা তার পূর্ববর্তী কোন ভার্সনের [`react-dom/server`](/reference/react-dom/server) এর সাহায্যে বানানো হয়েছিল।

```js
hydrate(reactNode, domNode, callback?)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `hydrate(reactNode, domNode, callback?)` {/*hydrate*/}

React 17 বা তার নিচের কোন ভার্সনে সার্ভার এনভায়রনমেন্টে React এর রেন্ডার করা HTML এর সাথে React "সংযুক্ত" করার জন্য `hydrate` কল করুন।

```js
import { hydrate } from 'react-dom';

hydrate(reactNode, domNode);
```

React `domNode` DOM নোডে থাকা HTML এর সাথে যুক্ত হবে, এবং এর ভেতরের DOM ম্যানেজ করার দায়িত্ব গ্রহণ করবে। সম্পূর্ণরূপে React দিয়ে তৈরি একটি অ্যাপে সাধারণত এর রুট component এর সাথে `hydrate` কল একবারই থাকবে। 

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

* `reactNode`: বিদ্যমান HTML রেন্ডার করার জন্য ব্যবহৃত "React node"। এটা সাধারণত `<App />` এর মত JSX এর একটি অংশ হয়ে থাকে, যা React 17 এ `renderToString(<App />)` এর মত একটি `ReactDOM Server` মেথড ব্যবহার করে রেন্ডার করা।

* `domNode`: একটা [DOM এলিমেন্ট](https://developer.mozilla.org/en-US/docs/Web/API/Element) যা সার্ভারে রুট এলিমেন্ট হিসেবে রেন্ডার করা হয়েছিল।

* **optional**: `callback`: একটি ফাংশন। যদি পাস করা হয়, আপনার component hydrated হবার পরে React এটাকে কল করবে।

#### রিটার্ন {/*returns*/}

`hydrate` null রিটার্ন করে।

#### সতর্কতা {/*caveats*/}
* `hydrate` প্রত্যাশা করে যে রেন্ডার হওয়া কনটেন্ট সার্ভারে রেন্ডার হওয়া কনটেন্টের সাথে হুবহু মিলে যাবে। টেক্সট কনটেন্টে অমিল React patch up করতে পারে, তবে আপনার উচিত অমিলগুলোকে বাগ হিসেবে বিবেচনা করা এবং সেগুলোকে ঠিক করা।
* ডেভেলপমেন্ট মোডে, React hydration এর সময়ে অমিলের বিষয়ে সতর্কবাণী দেখায়। অমিলের ক্ষেত্রে এট্রিবিউটের ভিন্নতা patch up হবে কি না তার কোন নিশ্চয়তা নেই। এটা পারফরম্যান্সের জন্য দরকার কেননা বেশিরভাগ অ্যাপে, অমিল খুবই বিরল, সুতরাং সকল মার্কাপ ভ্যালিডেট করাটা এতটাই চাপ ফেলবে যে সেটা করা নিষিদ্ধ বলা যায়।
* আপনার অ্যাপে `hydrate` কল এক বারই থাকার কথা। আপনি যদি একটি ফ্রেমওয়ার্ক ব্যবহার করেন, সেটা আপনার জন্য এই কল করে দিতে পারে।
* যদি আপনার অ্যাপ ক্লায়েন্ট-রেন্ডার্ড হয় এবং এতে আগে থেকে কোন HTML রেন্ডার করা না থাকে, `hydrate()` ব্যবহারের সাপোর্ট নেই। [`render()`](/reference/react-dom/render) (React 17 বা তার পূর্ববর্তী ভার্সনের জন্য) অথবা [`createRoot()`](/reference/react-dom/client/createRoot) (React 18+ এ) ব্যবহার করুন বরং।

---

## ব্যবহার {/*usage*/}

সার্ভারে রেন্ডার হওয়া <CodeStep step={2}>ব্রাউজার DOM নোডে</CodeStep>  <CodeStep step={1}>React component</CodeStep> যুক্ত করার জন্য `hydrate` কল করুন।

```js [[1, 3, "<App />"], [2, 3, "document.getElementById('root')"]]
import { hydrate } from 'react-dom';

hydrate(<App />, document.getElementById('root'));
```

একটি client-only অ্যাপ (সার্ভারে রেন্ডার হওয়া HTML ব্যতীত একটি অ্যাপ) `hydrate()` এর সাহায্যে রেন্ডার করার সাপোর্ট নেই। [`render()`](/reference/react-dom/render) (React 17 বা তার পূর্ববর্তী ভার্সনের জন্য) অথবা [`createRoot()`](/reference/react-dom/client/createRoot) (React 18+ এ) ব্যবহার করুন বরং।

### Hydrating server-rendered HTML {/*hydrating-server-rendered-html*/}

React এ, "hydration" হচ্ছে যেভাবে React ইতোমধ্যে সার্ভার এনভায়রনমেন্টে React এর রেন্ডার করা HTML এর সাথে "যুক্ত হয়"। Hydration এর সময়ে, React বিদ্যমান মার্কাপের সাথে ইভেন্ট লিসেনার যুক্ত করার চেষ্টা করবে এবং ক্লায়েন্টে অ্যাপের রেন্ডারিং এর দায়িত্ব নিয়ে নিবে।

সম্পূর্ণভাবে React দিয়ে তৈরি অ্যাপসমূহে, **আপনি সাধারণত একটি মাত্র "রুট" hydrate করবেন, একদম শুরুতে আপনার পুরো অ্যাপের জন্য।**

<Sandpack>

```html public/index.html
<!--
  HTML content inside <div id="root">...</div>
  was generated from App by react-dom/server.
-->
<div id="root"><h1>Hello, world!</h1></div>
```

```js index.js active
import './styles.css';
import { hydrate } from 'react-dom';
import App from './App.js';

hydrate(<App />, document.getElementById('root'));
```

```js App.js
export default function App() {
  return <h1>Hello, world!</h1>;
}
```

</Sandpack>

সাধারণত আপনার `hydrate` আবার কল দেবার বা আরো জায়গায় কল দেবার প্রয়োজন হবার কথা না। এই জায়গা থেকে, React আপনার অ্যাপ্লিকেশনের DOM ম্যানেজ করবে। UI আপডেটের জন্য, আপনার component গুলো [state ব্যবহার করবে।](/reference/react/useState)

Hydration নিয়ে আরো তথ্য জানতে, [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) এর ডকুমেন্টেশন দেখুন।

---

### অনিবার্য hydration mismatch error এর দমন {/*suppressing-unavoidable-hydration-mismatch-errors*/}

যদি একটি মাত্র এলিমেন্টের এট্রিবিউট অথবা টেক্সট কনটেন্ট অনিবার্যভাবে সার্ভার এবং ক্লায়েন্টে ভিন্ন থাকে (যেমন, টাইমস্ট্যাম্প), তবে আপনি hydration mismatch warning কে সাইলেন্ট রাখতে পারেন।

কোন এলিমেন্টের hydration সতর্কবাণী সাইলেন্ট করতে চাইলে, `suppressHydrationWarning={true}` যোগ করুনঃ

<Sandpack>

```html public/index.html
<!--
  HTML content inside <div id="root">...</div>
  was generated from App by react-dom/server.
-->
<div id="root"><h1>Current Date: 01/01/2020</h1></div>
```

```js index.js
import './styles.css';
import { hydrate } from 'react-dom';
import App from './App.js';

hydrate(<App />, document.getElementById('root'));
```

```js App.js active
export default function App() {
  return (
    <h1 suppressHydrationWarning={true}>
      Current Date: {new Date().toLocaleDateString()}
    </h1>
  );
}
```

</Sandpack>

এটা শুধুমাত্র এক লেভেল গভীরে কাজ করে, এবং এটা একটা escape hatch হবার কথা। এর মাত্রাতিরিক্ত ব্যবহার করবেন না। যদি না এটা টেক্সট কনটেন্ট হয়, React তাও এটাকে patch up করার চেষ্টা করবে না, সুতরাং এটা ভবিষ্যতের কোণ আপডেটের আগ পর্যন্ত ধারাবাহিকতা নাও রক্ষা করতে পারে।

---

### ভিন্ন ক্লায়েন্ট এবং সার্ভার কনটেন্ট এর হ্যান্ডলিং {/*handling-different-client-and-server-content*/}

আপনার যদি ইচ্ছাকৃতভাবে সার্ভার এবং ক্লায়েন্টে ভিন্ন কনটেন্ট রেন্ডার করার প্রয়োজন পড়ে, আপনি two-pass রেন্ডারিং করতে পারেন। যেসব component ক্লায়েন্টে ভিন্ন কিছু রেন্ডার করে তারা `isClient` এর মত একটি [state ভ্যারিয়েবল](/reference/react/useState) read করে নিতে পারে, যেটা আপনি একটা [effect](/reference/react/useEffect) এ `true` সেট করে দিতে পারেনঃ

<Sandpack>

```html public/index.html
<!--
  HTML content inside <div id="root">...</div>
  was generated from App by react-dom/server.
-->
<div id="root"><h1>Is Server</h1></div>
```

```js index.js
import './styles.css';
import { hydrate } from 'react-dom';
import App from './App.js';

hydrate(<App />, document.getElementById('root'));
```

```js App.js active
import { useState, useEffect } from "react";

export default function App() {
  const [isClient, setIsClient] = useState(false);

  useEffect(() => {
    setIsClient(true);
  }, []);

  return (
    <h1>
      {isClient ? 'Is Client' : 'Is Server'}
    </h1>
  );
}
```

</Sandpack>

এভাবে প্রাথমিক রেন্ডার পাস সার্ভারের মত একই কনটেন্ট রেন্ডার করবে, অমিল এড়িয়ে গিয়ে, কিন্তু hydration এর ঠিক পরেই একটা অতিরিক্ত পাস সিঙ্ক্রোনাসভাবে হবে।

<Pitfall>

এই পদ্ধতি আপনার hydration কে ধীর গতির বানায় কারণ আপনার component গুলোকে দুই বার রেন্ডার হতে হয়। ধীর গতির ইন্টারনেট সংযোগের ক্ষেত্রে ব্যবহারকারীর অভিজ্ঞতার বিষয়ে খেয়াল রাখবেন। প্রাথমিক HTML রেন্ডারের পরে বেশ উল্লেখযোগ্য বিলম্বে জাভাস্ক্রিপ্ট কোড লোড হতে পারে, সুতরাং hydration এর ঠিক পর পর ভিন্ন একটা UI এর রেন্ডারিং ব্যবহারকারীর জন্য বিরক্তিকর হতে পারে।

</Pitfall>
