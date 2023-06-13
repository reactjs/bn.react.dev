---
title: hydrateRoot
---

<Intro>

`hydrateRoot` আপনাকে একটা ব্রাউজার DOM এর মধ্যে সেই React কম্পোনেন্ট গুলো দেখানোর সুযোগ দেয়, যা [`react-dom/server`](/reference/react-dom/server) আগে থেকেই তৈরি করে রেখছে। 

```js
const root = hydrateRoot(domNode, reactNode, options?)
```

</Intro>

<InlineToc />

---

## Reference {/*reference*/}

### `hydrateRoot(domNode, reactNode, options?)` {/*hydrateroot*/}

একটা সার্ভার এনভায়রনমেন্টে React এর ইতোমধ্যে রেন্ডার করা HTML এর সাথে React "যুক্ত" করতে `hydrateRoot` কল করুন।

```js
import { hydrateRoot } from 'react-dom/client';

const domNode = document.getElementById('root');
const root = hydrateRoot(domNode, reactNode);
```

`domNode` এর ভেতরে থাকা HTML এর সাথে React যুক্ত হয়ে যাবে, এবং এর ভেতরে থাকা DOM পরিচালনার দায়িত্ব নিয়ে নিবে। সম্পূর্ণরূপে React দিয়ে বানানো একটি অ্যাপে সাধারণত এর রুট কম্পোনেন্টের সাথে একটি মাত্র `hydrateRoot` কল থাকে।

[এখানে আরো উদাহরণ দেখুন](#usage)

#### প্যারামিটার {/*parameters*/}

* `domNode`: একটা [DOM এলিমেন্ট](https://developer.mozilla.org/en-US/docs/Web/API/Element) যা সার্ভারে রুট এলিমেন্ট হিসেবে রেন্ডার হয়েছিল।

* `reactNode`: বিদ্যমান HTML রেন্ডার করার জন্য যে "React নোড" ব্যবহৃত হয়। এটা সাধারণত `<App />` এর মত একটি JSX এর অংশ হয়, যা `renderToPipeableStream(<App />)` এর মত একটি `ReactDOM Server` মেথড ব্যবহার করে রেন্ডার করা হয়েছে। 

* **optional** `options`: এই React root এর জন্য বিভিন্ন option সংবলিত একটি অবজেক্ট।

  * **optional** `onRecoverableError`: যখন React স্বয়ংক্রিয় ভাবে কোন error থেকে নিজেকে recover করে তখন হওয়া কলব্যাক।
  * **optional** `identifierPrefix`: [`useId`](/reference/react/useId) দিয়ে তৈরী হওয়া ID গুলোর জন্য React যে string prefix ব্যবহার করে। একই পেইজে যখন একাধিক rot থাকে তখন conflict এড়াতে এটা কাজে লাগে।


#### রিটার্ন {/*returns*/}

`hydrateRoot` দুটি মেথডসহ একটি অব্জেক্ট রিটার্ন করেঃ [`render`](#root-render) এবং [`unmount`.](#root-unmount)

#### সতর্কতা {/*caveats*/}

* `hydrateRoot()` প্রত্যাশা করে যে রেন্ডার হওয়া কন্টেন্ট সার্ভারে রেন্ডার হওয়া কম্পোনেন্টের সাথে হুবহু মিলে যাবে। যদি অমিল পান, তাহলে সেটাকে বাগ হিসেবে ধরে নিয়ে ঠিক করে ফেলুন। 
* ্ডেভেলপমেন্ট মোডে থাকা অবস্থায়, hydration চলাকালীন React অমিলের বিষয়ে সতর্ক করে। এমন কোন নিশ্চয়তা নাই যে অমিলের ক্ষেত্রে এট্রিবিউটের ভিন্নতা patch up হয়ে যাবে। এটা পারফরম্যান্স ঠিক রাখতে জরুরী। কারণ বেশিরভাগ অ্যাপে, অমিল থাকেই না বলা যায়, তাই সব markup ঠিকঠাক আছে কি না দেখা খুব কষ্টসাধ্য হয়।
* আপনার অ্যাপে খুব সম্ভবতঃ একটি মাত্র `hydrateRoot` কল থাকবে। আপনি যদি একটি ফ্রেমওয়ার্ক ব্যবহার করেন, সেটা আপনার জন্য কলটা করে দিতে পারে।
* যদি আপনার অ্যাপ ক্লায়েন্ট সাইডে রেন্ডার করা হয় যেখানে ইতোমধ্যে কোন HTML রেন্ডার করা নেই, সেক্ষেত্রে `hydrateRoot()` ব্যবহার করা যাবে না। বরং [`createRoot()`](/reference/react-dom/client/createRoot) ব্যবহার করুন।

---

### `root.render(reactNode)` {/*root-render*/}

একটি ব্রাউজার DOM এলিমেন্টের জন্য hydrated React রুটের মধ্যকার একটি React কম্পোনেন্ট আপডেট করতে `root.render` কল করুন।

```js
root.render(<App />);
```

React hydrated `root` এর ভেতরে থাকা `<App />` আপডেট করবে।

[নিচে আরো উদাহরণ দেখুন](#usage)

#### প্যারামিটার {/*root-render-parameters*/}

* `reactNode`: এটা হচ্ছে এমন একটি "React নোড" যা আপনি আপডেট করতে চান। এটা সাধারণত `<App />` এর মত একটি JSX এর অংশ হয়ে থাকে, তবে আপনি [`createElement()`](/reference/react/createElement) দিয়ে বানানো একটি React এলিমেন্ট, একটি স্ট্রিং, একটি সংখ্যা, `null` বা `undefined` পাস করে দিতে পারেন।


#### রিটার্ন {/*root-render-returns*/}

`root.render` `undefined` রিটার্ন করে।

#### সতর্কতা {/*root-render-caveats*/}

* রুটের hydrating শেষ হবার আগে আপনি যদি  `root.render` কল করেন, তাহলে React সার্ভার-রেন্ডার্ড HTML মুছে ফেলবে এবং সম্পূর্ণ রুটটাকে ক্লায়েন্ট রেন্ডারিং এর জন্য তৈরী করে ফেলবে।

---

### `root.unmount()` {/*root-unmount*/}

একটি React রুটের মধ্যে রেন্ডার হওয়া একটি ট্রি মুছে ফেলতে `root.unmount` কল করুন।

```js
root.unmount();
```

সম্পূর্ণরূপে React দিয়ে বানানো একটি অ্যাপের সাধারণত `root.unmount` এ কোন কল থাকবে না।

এটা তখন সবচেয়ে কাজে লাগে যদি আপনার React রুটের DOM নোড (বা এর কোন ancestor) অন্য কোন কোডের কারণে DOM থেকে মুছে যায়। উদাহরণস্বরূপ, ধরে একটা jQuery ট্যাব প্যানেল আছে যা DOM থেকে অচল ট্যাবগুলো মুছে ফেলে। যদি একটা ট্যাব মুছে যায় তবে এর ভেতর থাকা সব কিছুও (ভিতরে থাকা React রুটগুলো সহ) DOM থেকে মুছে যাবে। সেক্ষেত্রে, `root.unmount` কল করার মাধ্যমে আপনার React কে বলতে হবে মুছে যাওয়া রুটের কনটেন্ট ম্যানেজ করা "বন্ধ" করতে। না হলে, মুছে যাওয়া রুটের ভেতরকার কম্পোনেন্ট সাবস্ক্রিপশনের মত global resource মুছবে না এবং সেগুলো ফ্রি হবে না। 

`root.unmount` কল করলে রুটের সব কম্পোনেন্ট আনমাউন্ট হবে এবং রুট DOM নোড থেকে React "detach" হয়ে যাবে। একই সাথে ট্রিতে কোন ইভেন্ট হ্যান্ডলার বা স্টেট থাকলে সেটাও মুছে যাবে। 


#### প্যারামিটার {/*root-unmount-parameters*/}

`root.unmount` does not accept any parameters.


#### রিটার্ন {/*root-unmount-returns*/}

`render` `null` রিটার্ন করে।

#### সতর্কতা {/*root-unmount-caveats*/}

* `root.unmount` কল করলে ট্রি-এর সকল কম্পোনেন্ট আনমাউন্ট হবে এবং React কে রুট DOM নোড থেকে "বিচ্ছিন্ন" করবে।

* একবার `root.unmount` কল করা হলে একই রুটে `root.render` আর কল করা যাবে না। আনমাউন্ট করা রুটে `root.render` কলের চেষ্টা করা হলে "Cannot update an unmounted root" এরর দেখাবে।

---

## ব্যবহার {/*usage*/}

### সার্ভার-রেন্ডার্ড HTML এর hydrating {/*hydrating-server-rendered-html*/}

আপনার অ্যাপের HTML যদি [`react-dom/server`](/reference/react-dom/client/createRoot) দিয়ে বানানো হয়ে থাকে, আপনাকে একে ক্লায়েন্টে *hydrate* করতে হবে।

```js [[1, 3, "document.getElementById('root')"], [2, 3, "<App />"]]
import { hydrateRoot } from 'react-dom/client';

hydrateRoot(document.getElementById('root'), <App />);
```

এটা আপনার অ্যাপের জন্য <CodeStep step={2}>React কম্পোনেন্ট</CodeStep> দিয়ে <CodeStep step={1}>ব্রাউজার DOM নোডের</CodeStep> ভিতরকার সার্ভার HTML hydrate করে দেবে। সাধারণত, এটা আপনি startup এর সময় একবার করবেন। আপনি যদি একটি ফ্রেমওয়ার্ক ব্যবহার করেন, সে আপনার হয়ে ভেতরে ভেতরে কাজটা করে দেবে।

আপনার অ্যাপ hydrate করতে, React প্রাথমিকভাবে সার্ভারে তৈরী হওয়া HTML এর সাথে আপনার কম্পোনেন্টগুলোর লজিক "যুক্ত" করে দেবে। Hydration সার্ভার থেকে হওয়া প্রাথমিক HTML স্ন্যাপশটকে সম্পূর্ণভাবে interactive একটা অ্যাপে রূপান্তরিত করে যা ব্রাউজারে চলতে পারে। 

<Sandpack>

```html public/index.html
<!--
  <div id="root">...</div> এর মধ্যকার সব HTML কনটেন্ট react-dom/server
  দ্বারা <App /> রেন্ডার করার মাধ্যমে তৈরি করা হয়েছে।
-->
<div id="root"><h1>Hello, world!</h1><button>You clicked me <!-- -->0<!-- --> times</button></div>
```

```js index.js active
import './styles.css';
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(
  document.getElementById('root'),
  <App />
);
```

```js App.js
import { useState } from 'react';

export default function App() {
  return (
    <>
      <h1>Hello, world!</h1>
      <Counter />
    </>
  );
}

function Counter() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      You clicked me {count} times
    </button>
  );
}
```

</Sandpack>

আপনার `hydrateRoot` আবার বা একাধিক জায়গায় কল করার প্রয়োজন হবার কথা না। এর পর থেকে React আপনার অ্যাপ্লিকেশনের DOM ম্যানেজ করবে। UI আপডেটের জন্য আপনার কম্পোনেন্ট বরং [state ব্যবহার](/reference/react/useState) করবে।

<Pitfall>

এটা জরুরি যে আপনি `hydrateRoot` এ যে React ট্রি পাস করছেন সেটা সার্ভারে যেমন আউটপুট দিয়েছিল **ঠিক হুবহু আউটপুট** দিবে।

এটা ব্যবহারকারীর অভিজ্ঞতার জন্য জরুরী। আপনার জাভাস্ক্রিপ্ট কোড লোড হবার আগে ব্যবহারকারী কিছুক্ষণ সার্ভার থেকে রেন্ডার হওয়া HTML দেখবে। সার্ভারে হওয়া HTML স্ন্যাপশটের যে আউটপুট সেটার রেন্ডারিং অ্যাপ দ্রুত লোড হবার একটা ভ্রম তৈরী করছে। হঠাৎ করে অন্য কিছু দেখালে সেই ভ্রমটা ভেঙে যায়। এইজন্য সার্ভার আর ক্লায়েন্টে প্রাথমিকভাবে হওয়া রেন্ডার একইরকম হতে হবে।

যেসব কারণে সবচেয়ে hydration এরর সবচেয়ে বেশি দেখা যায় তার মধ্যে অন্যতম হলঃ

* রুট নোডের মধ্যে থাকা React এর বানানো HTML এর আশে পাশে থাকা অতিরিক্ত whitespace (যেমন newline)।
* রেন্ডারিং লজিকে `typeof window !== 'undefined'` এর মত চেক ব্যবহার করা।
* আপনার রেন্ডারিং লজিকে [`window.matchMedia`](https://developer.mozilla.org/en-US/docs/Web/API/Window/matchMedia) এর মত browser-only API এর ব্যবহার।
* সার্ভার আর ক্লায়েন্টে ভিন্ন ডেটা রেন্ডার করা।

React কিছু hydration এরর থেকে সামলে ওঠে, কিন্তু **আপনাকে অবশ্যই এগুলোকে অন্যান্য বাগের মতই ঠিক করতে হবে।** যদি সব ঠিকঠাক থাকেও, এরা আপনার অ্যাপ ধীরগতির করে ফেলবে; আর সবচেয়ে খারাপ যেটা হতে পারে, ইভেন্ট হ্যান্ডলার ভুল এলিমেন্টে যুক্ত হয়ে যেতে পারে।

</Pitfall>

---

### একটি সম্পূর্ণ ডকুমেন্টের hydrating {/*hydrating-an-entire-document*/}

সম্পূর্ণরূপে React দিয়ে বানানো একটি অ্যাপ [`<html>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/html) ট্যাগসহ পুরো ডকুমেন্ট JSX হিসেবে রেন্ডার করতে পারেঃ 

```js {3,13}
function App() {
  return (
    <html>
      <head>
        <meta charSet="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <link rel="stylesheet" href="/styles.css"></link>
        <title>My app</title>
      </head>
      <body>
        <Router />
      </body>
    </html>
  );
}
```

সম্পূর্ণ ডকুমেন্ট hydrate করতে, `hydrateRoot` এ [`document`](https://developer.mozilla.org/en-US/docs/Web/API/Window/document) global প্রথম আর্গুমেন্ট হিসেবে পাস করে দিনঃ

```js {4}
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document, <App />);
```

---

### অনিবার্য hydrartion অমিলের এরর বন্ধ করবেন যেভাবে {/*suppressing-unavoidable-hydration-mismatch-errors*/}

যদি একটিমাত্র এলিমেন্টের এট্রিবিউট বা টেক্সট কনটেন্ট সার্ভার এবং ক্লায়েন্টে আবশ্যিকভাবে আলাদা রাখা দরকার হয়, যেমন একতা টাইমস্ট্যাম্প, তাহলে আপনি hydration অমিল হবার কারণে যে সতর্কতা বার্তা দেখায় তা বন্ধ করতে পারেন।

একটা এলিমেন্টে hydration বিষয়ক সতর্কতা বার্তা দেখানো বন্ধ করতে চাইলে, `suppressHydrationWarning={true}` যুক্ত করুনঃ

<Sandpack>

```html public/index.html
<!--
  <div id="root">...</div> এর মধ্যকার সব HTML কনটেন্ট react-dom/server
  দ্বারা <App /> রেন্ডার করার মাধ্যমে তৈরি করা হয়েছে।
-->
<div id="root"><h1>Current Date: <!-- -->01/01/2020</h1></div>
```

```js index.js
import './styles.css';
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document.getElementById('root'), <App />);
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

এটা শুধুমাত্র এক স্তর গভীরে কাজ করে, এবং এর উদ্দেশ্য একটা escape hatch এর মত কাজ করা। এর অতিরিক্ত ব্যবহার এড়িয়ে চলুন। যদি না এটা টেক্সট কনটেন্ট হয়, React একে প্যাচ আপ করার চেষ্টা করবে না, সুতরাং এটা ভবিষ্যতে আপডেট হবার আগ পর্যন্ত অসামাঞ্জস্যপূর্ন থাকতে পারে।

---

### ক্লায়েন্ট এবং সার্ভারে ভিন্ন কনটেন্ট পরিচালনা করতে হবে যেভাবে {/*handling-different-client-and-server-content*/}

আপনি যদি ইচ্ছাকৃতভাবে সার্ভার এবং ক্লায়েন্টে ভিন্ন কিছু রেন্ডার করতে চান, আপনি দুই পাসে রেন্ডারিং চালাতে পারেন। ক্লায়েন্টের যেসব কম্পোনেন্ট কিছুটা ভিন্ন ভাবে রেন্ডার করবে, তারা `isClient` এর মত একটি [state variable](/reference/react/useState) রিড করতে পারে, যেটা আপনি একটা [Effect](/reference/react/useEffect) এ `true` সেট করে দিতে পারেনঃ

<Sandpack>

```html public/index.html
<!--
  <div id="root">...</div> এর মধ্যকার সব HTML কনটেন্ট react-dom/server
  দ্বারা <App /> রেন্ডার করার মাধ্যমে তৈরি করা হয়েছে।
-->
<div id="root"><h1>Is Server</h1></div>
```

```js index.js
import './styles.css';
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document.getElementById('root'), <App />);
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

এই ভাবে প্রাথমিক রেন্ডার পাস সার্ভারের মত একই কনটেন্ট রেন্ডার করবে, অমিল এড়িয়ে গিয়ে, কিন্তু hydration এর ঠিক পর সিংক্রোনাসভাবে অতিরিক্ত একটি পাস হবে।

<Pitfall>

এই পন্থায় hydration ধীর হয়ে যায় কারণ আপনার কম্পোনেন্ট দুই বার রেন্ডার হয়। যেসব ব্যবহারকারীর ইন্টারনেট ধীর গতির তাদের এটা ব্যবহারের অভিজ্ঞতার দিকে খেয়াল রাখবেন। প্রাথমিক HTML এর চেয়ে জাভাস্ক্রিপ্ট উল্লেখজনক ভাবে ধীরে লোড হতে পারে, সুতরাং hydration এর পর পর অন্য কোন UI রেন্ডার করা ব্যবহারকারীর জন্য বিরক্তিকর হতে পারে।

</Pitfall>

---

### একটি hydrated root কম্পোনেন্টের আপডেট যেভাবে করা হয় {/*updating-a-hydrated-root-component*/}

রুটের hydrating শেষ হবার পরে, রুট React কম্পোনেন্ট আপডেট করার জন্য আপনি [`root.render`](#root-render) কল করতে পারেন। **[`createRoot`](/reference/react-dom/client/createRoot) এর সাথে অমিল এখানে যে, এখানে আপনাকে আসলে এটা করবার দরকার নেই, কারণ ইতোমধ্যেই প্রাথমিক কনটেন্ট HTML হিসেবে রেন্ডার করা হয়ে গেছে।**

Hydration এর পরে কোন এক সময়ে যদি আপনি  `root.render` কল করেন, এবং আগের বার তৈরি হওয়া ট্রির সাথে নতুন কম্পোনেন্ট ট্রির গঠন কাঠামো মিলে যায়, React [state সংরক্ষণ করবে।](/learn/preserving-and-resetting-state) খেয়াল করুন যে আপনি ইনপুটে টাই করে পারছেন, যার অর্থ এই উদাহরণে প্রতি সেকেন্ডে বার বার হওয়া `render` কল ধ্বংসাত্মক নাঃ

<Sandpack>

```html public/index.html
<!--
  <div id="root">...</div> এর মধ্যকার সব HTML কনটেন্ট react-dom/server
  দ্বারা <App /> রেন্ডার করার মাধ্যমে তৈরি করা হয়েছে।
-->
<div id="root"><h1>Hello, world! <!-- -->0</h1><input placeholder="Type something here"/></div>
```

```js index.js active
import { hydrateRoot } from 'react-dom/client';
import './styles.css';
import App from './App.js';

const root = hydrateRoot(
  document.getElementById('root'),
  <App counter={0} />
);

let i = 0;
setInterval(() => {
  root.render(<App counter={i} />);
  i++;
}, 1000);
```

```js App.js
export default function App({counter}) {
  return (
    <>
      <h1>Hello, world! {counter}</h1>
      <input placeholder="Type something here" />
    </>
  );
}
```

</Sandpack>

একটা hydrated রুটে  [`root.render`](#root-render) কল করার বিষয়টা বিরল। সাধারণত, আপনি এর বদলে কম্পোনেন্টগুলোর কোন একটির মধ্যে [state আপডেট](/reference/react/useState) করবেন। 
