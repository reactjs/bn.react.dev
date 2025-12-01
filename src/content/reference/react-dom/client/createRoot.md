---
title: createRoot
---

<Intro>

একটি ব্রাউজার DOM নোডে React কম্পোনেন্ট দেখানোর জন্য `createRoot` আপনাকে একটি root তৈরী করতে দেবে।

```js
const root = createRoot(domNode, options?)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `createRoot(domNode, options?)` {/*createroot*/}

একটি ব্রাউজার DOM এলিমেন্টে React কম্পোনেন্ট দেখানোর স্বার্থে একটি React root তৈরী করার জন্য `createRoot` কল করুন।

```js
import { createRoot } from 'react-dom/client';

const domNode = document.getElementById('root');
const root = createRoot(domNode);
```

React `domNode` এর জন্য একটি root তৈরী করবে, এবং এর মধ্যকার DOM পরিচালনার দায়িত্ব নিয়ে নিবে। root তৈরী করবার পর আপনাকে এর মধ্যে একটি React কম্পোনেন্ট দেখাবার জন্য [`root.render`](#root-render) কল করতে হবেঃ

```js
root.render(<App />);
```

সম্পূর্ণরূপে React দিয়ে বানানো একটি অ্যাপে সাধারণত একটি মাত্র `createRoot` কল থাকবে এর root কম্পোনেন্টের জন্য। যেই পেইজের বিভিন্ন অংশের জন্য React এর "ছিটেফোটা" ব্যবহৃত হয় সেগুলোতে যত গুলো ইচ্ছে আলাদা আলাদা root থাকতে পারে।

[নিচে আরো উদাহরণ দেখ...](#usage)

#### প্যারামিটার {/*parameters*/}

* `domNode`: একটা [DOM এলিমেন্ট।](https://developer.mozilla.org/en-US/docs/Web/API/Element) React এই DOM এলিমেন্টের জন্য একটি root তৈরী করবে এবং root এ আপনাকে ফাংশন কল করতে দেবে, যেমন হতে পারে রেন্ডার হওয়া React কম্পোনেন্ট দেখানোর জন্য ফাংশন `render`।

* **optional** `options`: এই React root এর জন্য বিভিন্ন option সংবলিত একটি অবজেক্ট।

  * **optional** `onCaughtError`: যখন React একটি Error Boundary-তে কোন error ধরে তখন কল হওয়া কলব্যাক। Error Boundary যে `error` ধরেছে এবং `componentStack` সংবলিত একটি `errorInfo` অবজেক্ট দিয়ে কল হয়।
  * **optional** `onUncaughtError`: যখন কোন error throw হয় কিন্তু Error Boundary দিয়ে ধরা হয় না তখন কল হওয়া কলব্যাক। যে `error` throw হয়েছে এবং `componentStack` সংবলিত একটি `errorInfo` অবজেক্ট দিয়ে কল হয়।
  * **optional** `onRecoverableError`: যখন React স্বয়ংক্রিয়ভাবে error থেকে recover করে তখন কল হওয়া কলব্যাক। React যে `error` throw করে এবং `componentStack` সংবলিত একটি `errorInfo` অবজেক্ট দিয়ে কল হয়। কিছু recoverable error-এ মূল error কারণ `error.cause` হিসেবে অন্তর্ভুক্ত থাকতে পারে।
  * **optional** `identifierPrefix`: [`useId`](/reference/react/useId) দিয়ে তৈরী হওয়া ID গুলোর জন্য React যে string prefix ব্যবহার করে। একই পেইজে যখন একাধিক root থাকে তখন conflict এড়াতে এটা কাজে লাগে।

#### রিটার্ন {/*returns*/}

`createRoot` দুটি মেথডসহ একটি অব্জেক্ট রিটার্ন করেঃ [`render`](#root-render) এবং [`unmount`.](#root-unmount)

#### যেসব বিষয়ে সতর্ক থাকতে হবে {/*caveats*/}
* আপনার অ্যাপ যদি সার্ভার থেকে রেন্ডার হয়, `createRoot()` কাজ করবে না। বরং [`hydrateRoot()`](/reference/react-dom/client/hydrateRoot) ব্যব্যহার করুন।
* খুব সম্ভবত আপনার অ্যাপে একটি মাত্র `createRoot` কল থাকবে। আপনি যদি একটি ফ্রেমওয়ার্ক ব্যবহার করেন, সম্ভবতঃ সে-ই আপনার হয়ে কলটি করে দিবে।
* যখন আপনি DOM ট্রি-এর অন্য একটি অংশে যা আপনার কম্পোনেন্টের চাইল্ড না(উদাহরণস্বরূপ একটা মোডাল বা টুলটিপ), JSX রেন্ডার করতে চাইবেন তখন `createRoot` এর বদলে [`createPortal`](/reference/react-dom/createPortal) ব্যবহার করুন।

---

### `root.render(reactNode)` {/*root-render*/}

React root-এর ব্রাউজার DOM নোডে কোন [JSX](/learn/writing-markup-with-jsx) ("React node") দেখানোর জন্য `root.render` কল করুন।

```js
root.render(<App />);
```

React `root`-এ `<App />` দেখাবে, এবং এর মধ্যকার DOM পরিচালনার দায়িত্ব নিয়ে নিবে।

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*root-render-parameters*/}

* `reactNode`: আপনি দেখাতে চান এমন একটি *React নোড*। এটা সাধারণত `<App />` এর মত এক টুকরো JSX হবে।, কিন্তু আপনি [`createElement()`](/reference/react/createElement) দিয়ে তৈরী করা একটি React এলিমেন্ট, একটি স্ট্রিং, একটি সংখ্যা, `null` বা  `undefined` ও পাস করতে পারেন। 


#### রিটার্ন {/*root-render-returns*/}

`root.render` রিটার্ন করে `undefined`.

#### সতর্কতা {/*root-render-caveats*/}

* আপনি যখন প্রথম বারের মত `root.render` কল করবেন, React এর রুটের ভিতরে কম্পোনেন্ট রেন্ডার করবার আগে থাকা সব HTML কনটেন্ট মুছে ফেলবে। 

* যদি আপনার রুটের DOM নোডে এমন HTML থাকে যা React সার্ভারে তৈরি করেছে বা বিল্ডের সময় তৈরি করেছে, তাহলে বরং [`hydrateRoot()`](/reference/react-dom/client/hydrateRoot) ব্যবহার করুন, যা বিদ্যমান HTML এ ইভেন্ট হ্যান্ডলারগুলো যুক্ত করে দেয়।

* আপনি যদি একি রুটে একাধিকবার  `render` কল করেন, তাহলে আপনার পাঠানো সর্বশেষ JSX দেখানোর খাতিরে React প্রয়োজনমত DOM আপডেট করে ফেলবে। React আগেরবার রেন্ডার হওয়া ট্রি এর সাথে ["মিলিয়ে দেখবে"](/learn/preserving-and-resetting-state) এবং সিদ্ধান্ত নিবে DOM এর কোণ অংশগুলো পুনর্ব্যবহার করা যায় আর কোনগুলো আবার বানাতে হবে। একই রুটে আবার `render` কল করা রুট কম্পোনেন্টে [`set` function](/reference/react/useState#setstate) কল করার মতঃ React অপ্রয়োজনীয় DOM আপডেট এড়ানোর চেষ্টা করে।

* যদিও rendering শুরু হওয়ার পর synchronous, `root.render(...)` synchronous নয়। এর মানে হল `root.render()` এর পরের কোড ওই নির্দিষ্ট render এর কোন effects (`useLayoutEffect`, `useEffect`) fire হওয়ার আগেই রান হতে পারে। এটা সাধারণত ঠিক আছে এবং খুব কমই adjustment প্রয়োজন হয়। বিরল ক্ষেত্রে যেখানে effect timing গুরুত্বপূর্ণ, আপনি `root.render(...)` কে [`flushSync`](https://react.dev/reference/react-dom/client/flushSync) দিয়ে wrap করতে পারেন যাতে initial render সম্পূর্ণভাবে synchronously রান হয়।
  
  ```js
  const root = createRoot(document.getElementById('root'));
  root.render(<App />);
  // 🚩 HTML এ এখনো rendered <App /> অন্তর্ভুক্ত হবে নাঃ
  console.log(document.body.innerHTML);
  ```

---

### `root.unmount()` {/*root-unmount*/}

React রুটের মধ্যে রেন্ডার হওয়া একটি ট্রি মুছে ফেলতে `root.unmount` কল করুন।

```js
root.unmount();
```

সম্পূর্ণরূপে React দিয়ে বানানো অ্যাপের সাধারণত  `root.unmount` এ কোন কল থাকে না।

এটা সে ক্ষেত্রে সবচেয়ে কাজে লাগে যদি অন্য কোন কোডের কারণে DOM থেকে আপনার React রুটের DOM নোড (বা ট্রিতে এর কোন পূর্বসূরী) মুছে যায়। উদাহরণস্বরূপ, ধরেন একটা jQuery ট্যাব প্যানেল আছে যা DOM থেকে অচল ট্যাবগুলোকে ফেলে দেয়। যদি একটা ট্যাব ফেলে দেওয়া হয়, তাহলে এই ট্যাবের মধ্যে থাকা যাবতীয় সব কিছু(ভিতরকার React রুটগুলো সহ) DOM থেকে মুছে যায়। সেক্ষেত্রে, `root.unmount` কল করার মাধ্যমে আপনার React কে বলতে হবে মুছে যাওয়া রুটের কনটেন্ট ম্যানেজ করা "বন্ধ" করতে। না হলে, মুছে যাওয়া রুটের ভেতরকার কম্পোনেন্ট সাবস্ক্রিপশনের মত global resource মুছবে না এবং সেগুলো ফ্রি হবে না।

`root.unmount` কল করলে রুটের সব কম্পোনেন্ট আনমাউন্ট হবে এবং রুট DOM নোড থেকে React "detach" হয়ে যাবে। একই সাথে ট্রিতে কোন ইভেন্ট হ্যান্ডলার বা স্টেট থাকলে সেটাও মুছে যাবে। 


#### প্যারামিটার {/*root-unmount-parameters*/}

`root.unmount` কোন প্যারামিটার গ্রহণ করে না।


#### রিটার্ন {/*root-unmount-returns*/}

`root.unmount` `undefined` রিটার্ন করে।

#### সতর্কতা {/*root-unmount-caveats*/}

* `root.unmount` কল করলে ট্রি-এর সকল কম্পোনেন্ট আনমাউন্ট হবে এবং React কে রুট DOM নোড থেকে "বিচ্ছিন" করবে।

* একবার `root.unmount` কল করা হলে একই রুটে `root.render` আর কল করা যাবে না। আনমাউন্ট করা রুটে `root.render` কলের চেষ্টা করা হলে "Cannot update an unmounted root" এরর দেখাবে। যদিও, আপনি একই DOM নোডে নতুন রুট তৈরী করতে পারেন যখন সেই নোডে আগের রুট আনমাউন্ট করা হয়ে গেছে। 

---

## ব্যবহার {/*usage*/}

### সম্পূর্ণরূপে React দিয়ে তৈরি অ্যাপের রেন্ডারিং {/*rendering-an-app-fully-built-with-react*/}

যদি আপনার অ্যাপটি সম্পূর্ণরূপে React দিয়ে বানানো হয়ে থাকে, তবে পুরো অ্যাপের জন্য একটি মাত্র রুট তৈরি করুন।

```js [[1, 3, "document.getElementById('root')"], [2, 4, "<App />"]]
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

সাধারণত, আপনাকে একবারই শুরুতে এই কোড রান করতে হবে। এটা যা করবে তা হলঃ

1. আপনার HTML এ ডিফাইন্ড হওয়া <CodeStep step={1}>ব্রাউজার DOM নোড</CodeStep> খুঁজে বের করবে।
2. আপনার অ্যাপের ভেতর থাকা <CodeStep step={2}>React কম্পোনেন্ট</CodeStep> দেখাবে।

<Sandpack>

```html public/index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <!-- এইটা DOM নোড -->
    <div id="root"></div>
  </body>
</html>
```

```js src/index.js active
import { createRoot } from 'react-dom/client';
import App from './App.js';
import './styles.css';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

```js src/App.js
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

**যদি আপনার অ্যাপটি সম্পূর্ণরূপে React দিয়ে বানানো হয়ে থাকে, আপনার আর কোন রুট তৈরির বা আবার [`root.render`](#root-render) কল করার প্রয়োজন হবার কথা না** 

এর পর থেকে, React আপনার পুরো অ্যাপের DOM পরিচালনা করবে। আরো কম্পোনেন্ট যুক্ত করবার জন্য [`App` কম্পোনেন্টে তাদের nest করুন।](/learn/importing-and-exporting-components) যখন আপনার UI আপডেটের প্রয়োজন হবে, আপনার প্রতিটি কম্পোনেন্ট [স্টেট ব্যবহার করে](/reference/react/useState) সেটা করতে পারবে। যখন আপনার DOM নোডের বাইরে অতিরিক্ত কোন কনটেন্ট যেমন একটা মোডাল বা টুলটিপ দেখানোর প্রয়োজন হবে তখন, [একটা পোর্টাল ব্যবহার করে সেটা রেন্ডার করুন।](/reference/react-dom/createPortal)

<Note>

যখন আপনার HTML খালি থাকে, যতক্ষণ পর্যন্ত জাভাস্ক্রিপ্ট লোড এবং রান না হয়, ততক্ষণ ব্যবহারকারী একটা খালি পেইজ দেখবে।

```html
<div id="root"></div>
```

<<<<<<< HEAD
বিষয়টা খুব ধীর রকম অনুভূত হতে পারে! এটা সমাধানের জন্য, আপনি [বিল্ডের সময় বা সার্ভারে](/reference/react-dom/server) আপনার কম্পোনেন্ট গুলো থেকে প্রাথমিক HTML তৈরি করে ফেলতে পারেন। এর পরে আপনার ভিজিটররা জাভাস্ক্রিপ্ট লোড হবার আগেই টেক্সট পড়তে, ছবি দেখতে এবং লিঙ্ক ক্লিক করতে পারবে। আমাদের উপদেশ থাকবে যে আপনি এর জন্য একটা [ফ্রেমওয়ার্ক ব্যবহার করুন](/learn/start-a-new-react-project#full-stack-frameworks) যা নিজেই এই অপটিমাইজেশনটুকু করে দেবে। কখন এটা রান করছে তার উপর নির্ভর করে একে বলা হয়, *server-side rendering (SSR)* অথবা *static site generation (SSG)।*
=======
This can feel very slow! To solve this, you can generate the initial HTML from your components [on the server or during the build.](/reference/react-dom/server) Then your visitors can read text, see images, and click links before any of the JavaScript code loads. We recommend [using a framework](/learn/creating-a-react-app#full-stack-frameworks) that does this optimization out of the box. Depending on when it runs, this is called *server-side rendering (SSR)* or *static site generation (SSG).*
>>>>>>> 2534424ec6c433cc2c811d5a0bd5a65b75efa5f0

</Note>

<Pitfall>

**যেসব অ্যাপ server rendering বা static generation ব্যবহার করে তাদেরকে অবশ্যই `createRoot` এর বদলে [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) কল করতে হবে।** React তখন আপনার HTML থেকে DOM নোডগুলো ধ্বংস এবং পুনরায় তৈরি করার বদলে *hydrate* (পুনর্ব্যবহার) করবে।

</Pitfall>

---

### React দিয়ে আংশিকভাবে বানানো পেইজের রেন্ডারিং {/*rendering-a-page-partially-built-with-react*/}

যদি আপনার পেইজ [সম্পূর্ণরূপে React দিয়ে বানানো না হয়ে থাকে](/learn/add-react-to-an-existing-project#using-react-for-a-part-of-your-existing-page), React দিয়ে পরিচালিত প্রতিটি উচ্চ স্তরের UI piece এর জন্য একটি রুট তৈরি করার খাতিরে আপনি একাধিকবার `createRoot` কল করতে পারেন। আপনি [`root.render`](#root-render) কল করার মাধ্যমে প্রতিটি রুটে ভিন্ন ভিন্ন কনটেন্ট দেখাতে পারেন।

এখানে, দুটি ভিন্ন React কম্পোনেন্ট `index.html` এর দুটি DOM নোডে রেন্ডার হচ্ছেঃ

<Sandpack>

```html public/index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <nav id="navigation"></nav>
    <main>
      <p>This paragraph is not rendered by React (open index.html to verify).</p>
      <section id="comments"></section>
    </main>
  </body>
</html>
```

```js src/index.js active
import './styles.css';
import { createRoot } from 'react-dom/client';
import { Comments, Navigation } from './Components.js';

const navDomNode = document.getElementById('navigation');
const navRoot = createRoot(navDomNode); 
navRoot.render(<Navigation />);

const commentDomNode = document.getElementById('comments');
const commentRoot = createRoot(commentDomNode); 
commentRoot.render(<Comments />);
```

```js src/Components.js
export function Navigation() {
  return (
    <ul>
      <NavLink href="/">Home</NavLink>
      <NavLink href="/about">About</NavLink>
    </ul>
  );
}

function NavLink({ href, children }) {
  return (
    <li>
      <a href={href}>{children}</a>
    </li>
  );
}

export function Comments() {
  return (
    <>
      <h2>Comments</h2>
      <Comment text="Hello!" author="Sophie" />
      <Comment text="How are you?" author="Sunil" />
    </>
  );
}

function Comment({ text, author }) {
  return (
    <p>{text} — <i>{author}</i></p>
  );
}
```

```css
nav ul { padding: 0; margin: 0; }
nav ul li { display: inline-block; margin-right: 20px; }
```

</Sandpack>

আপনি [`document.createElement()`](https://developer.mozilla.org/en-US/docs/Web/API/Document/createElement) ব্যবহার করে একটা নতুন DOM নোড তৈরীও করতে পারেন এবং একে নিজে নিজে ডকুমেন্টে যোগ করতে পারেন।

```js
const domNode = document.createElement('div');
const root = createRoot(domNode); 
root.render(<Comment />);
document.body.appendChild(domNode); // আপনি ডকুমেন্টের যেকোন জায়গায় এটা যোগ করতে পারেন
```

DOM নোড থেকে React ট্রি সরাতে এবং এর ব্যবহৃত সকল রিসোর্স মুছে ফেলতে [`root.unmount`](#root-unmount) কল করুন।

```js
root.unmount();
```

এইটা সাধারণত সবচেয়ে কাজে লাগে যদি অ্যাপের ভিতরে থাকা আপনার React কম্পোনেন্টগুলো ভিন্ন কোন ফ্রেমওয়ার্কে লেখা থাকে। 

---

### একটি রুট কম্পোনেন্ট আপডেট করা {/*updating-a-root-component*/}

আপনি একই রুটে একাধিকবার `render` কল করতে পারেন। যতক্ষণ পর্যন্ত আগে থেকে রেন্ডার হওয়া অবস্থার সাথে কম্পোনেন্ট ট্রি এর গঠনবিন্যাস মিলে যাচ্ছজে, React [state সংরক্ষণ করবে।](/learn/preserving-and-resetting-state) খেয়াল করুন যে আপনি ইনপুটে টাইপ করতে পারছেন, যার অর্থ প্রতি সেকেন্ডে পুনারবৃত্ত হওয়া  `render` কল ধ্বংসাত্মক নয়ঃ

<Sandpack>

```js src/index.js active
import { createRoot } from 'react-dom/client';
import './styles.css';
import App from './App.js';

const root = createRoot(document.getElementById('root'));

let i = 0;
setInterval(() => {
  root.render(<App counter={i} />);
  i++;
}, 1000);
```

```js src/App.js
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

সাধারণত `render` একাধিকবার কল করা হয় না, বরং আপনার কম্পোনেট গুলোই [state আপডেট](/reference/react/useState) করে।

### প্রোডাকশনে Error logging {/*error-logging-in-production*/}

ডিফল্টভাবে, React সকল error কনসোলে log করে। আপনার নিজস্ব error reporting implement করার জন্য, আপনি optional error handler root options `onUncaughtError`, `onCaughtError` এবং `onRecoverableError` প্রদান করতে পারেনঃ

```js [[1, 6, "onCaughtError"], [2, 6, "error", 1], [3, 6, "errorInfo"], [4, 10, "componentStack", 15]]
import { createRoot } from "react-dom/client";
import { reportCaughtError } from "./reportError";

const container = document.getElementById("root");
const root = createRoot(container, {
  onCaughtError: (error, errorInfo) => {
    if (error.message !== "Known error") {
      reportCaughtError({
        error,
        componentStack: errorInfo.componentStack,
      });
    }
  },
});
```

<CodeStep step={1}>onCaughtError</CodeStep> অপশন হল একটি ফাংশন যা দুটি argument দিয়ে কল হয়ঃ

1. যে <CodeStep step={2}>error</CodeStep> throw হয়েছে।
2. একটি <CodeStep step={3}>errorInfo</CodeStep> অবজেক্ট যাতে error এর <CodeStep step={4}>componentStack</CodeStep> রয়েছে।

`onUncaughtError` এবং `onRecoverableError` এর সাথে একত্রে, আপনি আপনার নিজস্ব error reporting system implement করতে পারেনঃ

<Sandpack>

```js src/reportError.js
function reportError({ type, error, errorInfo }) {
  // নির্দিষ্ট implementation আপনার উপর নির্ভর করে।
  // `console.error()` শুধুমাত্র demonstration উদ্দেশ্যে ব্যবহৃত হয়েছে।
  console.error(type, error, "Component Stack: ");
  console.error("Component Stack: ", errorInfo.componentStack);
}

export function onCaughtErrorProd(error, errorInfo) {
  if (error.message !== "Known error") {
    reportError({ type: "Caught", error, errorInfo });
  }
}

export function onUncaughtErrorProd(error, errorInfo) {
  reportError({ type: "Uncaught", error, errorInfo });
}

export function onRecoverableErrorProd(error, errorInfo) {
  reportError({ type: "Recoverable", error, errorInfo });
}
```

```js src/index.js active
import { createRoot } from "react-dom/client";
import App from "./App.js";
import {
  onCaughtErrorProd,
  onRecoverableErrorProd,
  onUncaughtErrorProd,
} from "./reportError";

const container = document.getElementById("root");
const root = createRoot(container, {
  // মনে রাখবেন development এ এই options গুলো সরিয়ে দিন যাতে
  // React এর default handlers ব্যবহার করতে পারেন বা development এর জন্য আপনার নিজস্ব overlay implement করতে পারেন।
  // handlers গুলো এখানে শুধুমাত্র demonstration উদ্দেশ্যে unconditionally specify করা হয়েছে।
  onCaughtError: onCaughtErrorProd,
  onRecoverableError: onRecoverableErrorProd,
  onUncaughtError: onUncaughtErrorProd,
});
root.render(<App />);
```

```js src/App.js
import { Component, useState } from "react";

function Boom() {
  foo.bar = "baz";
}

class ErrorBoundary extends Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}

export default function App() {
  const [triggerUncaughtError, settriggerUncaughtError] = useState(false);
  const [triggerCaughtError, setTriggerCaughtError] = useState(false);

  return (
    <>
      <button onClick={() => settriggerUncaughtError(true)}>
        Trigger uncaught error
      </button>
      {triggerUncaughtError && <Boom />}
      <button onClick={() => setTriggerCaughtError(true)}>
        Trigger caught error
      </button>
      {triggerCaughtError && (
        <ErrorBoundary>
          <Boom />
        </ErrorBoundary>
      )}
    </>
  );
}
```

</Sandpack>

## Troubleshooting {/*troubleshooting*/}

### একটা রুট তৈরী করবার পরও কিছুই দেখাচ্ছে না {/*ive-created-a-root-but-nothing-is-displayed*/}

নিশ্চিত করুন যে আপনি আসলেই রুটে আপনার অ্যাপ *রেন্ডার* করতে ভুলে যাননি।

```js {5}
import { createRoot } from 'react-dom/client';
import App from './App.js';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

আপনি এটা করার আগ পর্যন্ত কিছুই দেখা যাবে না।

---

### একটা এরর দেখাচ্ছেঃ "You passed a second argument to root.render" {/*im-getting-an-error-you-passed-a-second-argument-to-root-render*/}

একটি সাধারণ ভুল হল `createRoot` এর options `root.render(...)` এ পাঠানোঃ

<ConsoleBlock level="error">

Warning: You passed a second argument to root.render(...) but it only accepts one argument.

</ConsoleBlock>

এটা ঠিক করতে, root options `root.render(...)` এ না দিয়ে `createRoot(...)` এ দিনঃ
```js {2,5}
// 🚩 ভুলঃ root.render শুধুমাত্র একটি argument নেয়।
root.render(App, {onUncaughtError});

// ✅ সঠিকঃ options createRoot এ দিন।
const root = createRoot(container, {onUncaughtError}); 
root.render(<App />);
```

---

### একটা এরর দেখাচ্ছেঃ "Target container is not a DOM element" {/*im-getting-an-error-target-container-is-not-a-dom-element*/}

এই এররের অর্থ হল, আপনি যা `createRoot`-এ পাঠাচ্ছেন তা DOM নোড না।

আপনি যদি নিশ্চিত না হন যে কি হচ্ছে, একে logging করার চেষ্টা করুনঃ

```js {2}
const domNode = document.getElementById('root');
console.log(domNode); // ???
const root = createRoot(domNode);
root.render(<App />);
```

উদাহরণস্বরূপ, যদি `domNode` `null` হয়, এর অর্থ [`getElementById`](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById)`null` রিটার্ন করেছে। এটা হবে যদি আপনার কল করার সময়ে ডকুমেন্টে ওই ID এর কোন নোড না থাকে। এর কিছু কারণ হতে পারে এমনঃ

1. হতে পারে আপনার যেই ID খুজছেন সেটা আপনার HTML এ ব্যবহার করা ID থেকে আলাদা। টাইপিং এ ভুল হয়েছে কি নিশ্চিত হন!
2. হয়ত আপনার বান্ডলের `<script>` ট্যাগ HTML-এ *এর পরে* কোন DOM নোড "দেখতে" পারছে না। 

এই এরর পাবার আরেকটি কমন কারণ হল `createRoot(domNode)` এর বদলে `createRoot(<App />)` লেখা।

---

### I'm getting an error: "Functions are not valid as a React child." {/*im-getting-an-error-functions-are-not-valid-as-a-react-child*/}

এই এররের অর্থ হল, আপনি যা `root.render` এ পাঠাচ্ছেন তা React কম্পোনেন্ট নয়।

এটা হতে পারে যদি আপনি `root.render` কে `<Component />` এর বদলে `Component` দিয়ে কল করেনঃ

```js {2,5}
// 🚩 ভুলঃ অ্যাপ একটা ফাংশন, কম্পোনেন্ট না।
root.render(App);

// ✅ সঠিকঃ: <App /> একটা কম্পোনেন্ট।
root.render(<App />);
```

অথবা যদি আপনি `root.render` এ একটা ফাংশন পাঠিয়ে থাকেন, যেখানে আসলে এই ফাংশন কলের ফলাফল পাঠাবার কথাঃ

```js {2,5}
// 🚩 ভুলঃ createApp একটা ফাংশন, কম্পোনেন্ট না।
root.render(createApp);

// ✅ সঠিকঃ একটা কম্পোনেন্ট রিটার্ন করার জন্য createApp কল করুন।
root.render(createApp());
```

---

### সার্ভার থেকে রেন্ডার হওয়া HTML একদম শুরু থেকে তৈরী হচ্ছে {/*my-server-rendered-html-gets-re-created-from-scratch*/}

যদি আপনার অ্যাপ সার্ভার থেকে রেন্ডার হয়ে থাকে এবং React এর তৈরী করা ইনিশিয়াল HTML থাকে ওতে, আপনি খেয়াল করবেন যে `root.render` কল করলে সব HTML মুছে যায়, এবং একদম শুরু থেকে সব DOM নোড তৈরি হয়। এটা ধীরতর হতে পারে, ফোকাস এবং স্ক্রল পজিশন রিসেট হয়ে যায়, এবং ব্যবহারকারীর ইনপুটও হারিয়ে যেতে পারে।

সার্ভার থেকে রেন্ডার হওয়া অ্যাপের ক্ষেত্রে অবশ্যই `createRoot` এর বদলে [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) এর ব্যবহার করতে হবেঃ

```js {1,4-7}
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(
  document.getElementById('root'),
  <App />
);
```

খেয়াল করুন এর API আলাদা। বিশেষ করে, আর কোন `root.render` কল হবে না।
