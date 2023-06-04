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

  * **optional** `onRecoverableError`: যখন React স্বয়ংক্রিয় ভাবে কোন error থেকে নিজেকে recover করে তখন হওয়া কলব্যাক।
  * **optional** `identifierPrefix`: [`useId`](/reference/react/useId) দিয়ে তৈরী হওয়া ID গুলোর জন্য React যে string prefix ব্যবহার করে। একই পেইজে যখন একাধিক rot থাকে তখন conflict এড়াতে এটা কাজে লাগে। 

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

* If your root's DOM node contains HTML generated by React on the server or during the build, use [`hydrateRoot()`](/reference/react-dom/client/hydrateRoot) instead, which attaches the event handlers to the existing HTML.

* If you call `render` on the same root more than once, React will update the DOM as necessary to reflect the latest JSX you passed. React will decide which parts of the DOM can be reused and which need to be recreated by ["matching it up"](/learn/preserving-and-resetting-state) with the previously rendered tree. Calling `render` on the same root again is similar to calling the [`set` function](/reference/react/useState#setstate) on the root component: React avoids unnecessary DOM updates.

---

### `root.unmount()` {/*root-unmount*/}

React রুটের মধ্যে রেন্ডার হওয়া একটি ট্রি মুছে ফেলতে `root.unmount` কল করুন।

```js
root.unmount();
```

An app fully built with React will usually not have any calls to `root.unmount`.

This is mostly useful if your React root's DOM node (or any of its ancestors) may get removed from the DOM by some other code. For example, imagine a jQuery tab panel that removes inactive tabs from the DOM. If a tab gets removed, everything inside it (including the React roots inside) would get removed from the DOM as well. In that case, you need to tell React to "stop" managing the removed root's content by calling `root.unmount`. Otherwise, the components inside the removed root won't know to clean up and free up global resources like subscriptions.

Calling `root.unmount` will unmount all the components in the root and "detach" React from the root DOM node, including removing any event handlers or state in the tree. 


#### প্যারামিটার {/*root-unmount-parameters*/}

`root.unmount` কোন প্যারামিটার গ্রহণ করে না।


#### রিটার্ন {/*root-unmount-returns*/}

`root.unmount` `undefined` রিটার্ন করে।

#### সতর্কতা {/*root-unmount-caveats*/}

* `root.unmount` কল করলে ট্রি-এর সকল কম্পোনেন্ট আনমাউন্ট হবে এবং React কে রুট DOM নোড থেকে "বিচ্ছিন" করবে।

* একবার `root.unmount` কল করা হলে একই রুটে `root.render` আর কল করা যাবে না। আনমাউন্ট করা রুটে `root.render` কলের চেষ্টা করা হলে "Cannot update an unmounted root" এরর দেখাবে। যদিও, আপনি একই DOM নোডে নতুন রুট তৈরী করতে পারেন যখন সেই নোডে আগের রুট আনমাউন্ট করা হয়ে গেছে। 

---

## Usage {/*usage*/}

### Rendering an app fully built with React {/*rendering-an-app-fully-built-with-react*/}

If your app is fully built with React, create a single root for your entire app.

```js [[1, 3, "document.getElementById('root')"], [2, 4, "<App />"]]
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

Usually, you only need to run this code once at startup. It will:

1. Find the <CodeStep step={1}>browser DOM node</CodeStep> defined in your HTML.
2. Display the <CodeStep step={2}>React component</CodeStep> for your app inside.

<Sandpack>

```html index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <!-- এইটা DOM নোড -->
    <div id="root"></div>
  </body>
</html>
```

```js index.js active
import { createRoot } from 'react-dom/client';
import App from './App.js';
import './styles.css';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
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

**If your app is fully built with React, you shouldn't need to create any more roots, or to call [`root.render`](#root-render) again.** 

From this point on, React will manage the DOM of your entire app. To add more components, [nest them inside the `App` component.](/learn/importing-and-exporting-components) When you need to update the UI, each of your components can do this by [using state.](/reference/react/useState) When you need to display extra content like a modal or a tooltip outside the DOM node, [render it with a portal.](/reference/react-dom/createPortal)

<Note>

When your HTML is empty, the user sees a blank page until the app's JavaScript code loads and runs:

```html
<div id="root"></div>
```

This can feel very slow! To solve this, you can generate the initial HTML from your components [on the server or during the build.](/reference/react-dom/server) Then your visitors can read text, see images, and click links before any of the JavaScript code loads. We recommend [using a framework](/learn/start-a-new-react-project#production-grade-react-frameworks) that does this optimization out of the box. Depending on when it runs, this is called *server-side rendering (SSR)* or *static site generation (SSG).*

</Note>

<Pitfall>

**Apps using server rendering or static generation must call [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) instead of `createRoot`.** React will then *hydrate* (reuse) the DOM nodes from your HTML instead of destroying and re-creating them.

</Pitfall>

---

### Rendering a page partially built with React {/*rendering-a-page-partially-built-with-react*/}

If your page [isn't fully built with React](/learn/add-react-to-an-existing-project#using-react-for-a-part-of-your-existing-page), you can call `createRoot` multiple times to create a root for each top-level piece of UI managed by React. You can display different content in each root by calling [`root.render`.](#root-render)

Here, two different React components are rendered into two DOM nodes defined in the `index.html` file:

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

```js index.js active
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

```js Components.js
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

```js index.js active
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

সাধারণত `render` একাধিকবার কল করা হয় না, বরং আপনার কম্পোনেট গুলোই [state আপডেট](/reference/react/useState) করে।

---
## ট্রাবলশ্যুট {/*troubleshooting*/}

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

1. হতে পারে আপনি যেই ID খুজছেন সেটা আপনার HTML এ ব্যবহার করা ID থেকে আলাদা। টাইপিং এ ভুল হয়েছে কি না নিশ্চিত হন!
2. হয়ত আপনার বান্ডলের `<script>` ট্যাগ HTML-এ *এর পরে* কোন DOM নোড "দেখতে" পারছে না। 

এই এরর পাবার আরেকটি কমন কারণ হল `createRoot(domNode)` এর বদলে `createRoot(<App />)` লেখা।

---

### একটা এরর দেখাচ্ছেঃ "Functions are not valid as a React child." {/*im-getting-an-error-functions-are-not-valid-as-a-react-child*/}

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

যদি আপনার অ্যাপ সার্ভার থেকে রেন্ডার হয়ে থাকে এবং React এর তৈরী করা ইনিশিয়াল HTML থাকে ওতে, আপনি খেয়াল করবেন যে `root.render` কল করলে সব HTML মুছে যায়, এবং একদম শুরু থেকে সব DOM নোড তৈরী হয়। এটা ধীরতর হতে পারে, ফোকাস এবং স্ক্রল পজিশন রিসেট হয়ে যায়, এবং ব্যবহারকারীর ইনপুটও হারিয়ে যেতে পারে।

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
