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
* You'll likely have only one `hydrateRoot` call in your app. If you use a framework, it might do this call for you.
* If your app is client-rendered with no HTML rendered already, using `hydrateRoot()` is not supported. Use [`createRoot()`](/reference/react-dom/client/createRoot) instead.

---

### `root.render(reactNode)` {/*root-render*/}

Call `root.render` to update a React component inside a hydrated React root for a browser DOM element.

```js
root.render(<App />);
```

React will update `<App />` in the hydrated `root`.

[See more examples below.](#usage)

#### Parameters {/*root-render-parameters*/}

* `reactNode`: A "React node" that you want to update. This will usually be a piece of JSX like `<App />`, but you can also pass a React element constructed with [`createElement()`](/reference/react/createElement), a string, a number, `null`, or `undefined`.


#### Returns {/*root-render-returns*/}

`root.render` returns `undefined`.

#### Caveats {/*root-render-caveats*/}

* If you call `root.render` before the root has finished hydrating, React will clear the existing server-rendered HTML content and switch the entire root to client rendering.

---

### `root.unmount()` {/*root-unmount*/}

Call `root.unmount` to destroy a rendered tree inside a React root.

```js
root.unmount();
```

An app fully built with React will usually not have any calls to `root.unmount`.

This is mostly useful if your React root's DOM node (or any of its ancestors) may get removed from the DOM by some other code. For example, imagine a jQuery tab panel that removes inactive tabs from the DOM. If a tab gets removed, everything inside it (including the React roots inside) would get removed from the DOM as well. You need to tell React to "stop" managing the removed root's content by calling `root.unmount`. Otherwise, the components inside the removed root won't clean up and free up resources like subscriptions.

Calling `root.unmount` will unmount all the components in the root and "detach" React from the root DOM node, including removing any event handlers or state in the tree. 


#### Parameters {/*root-unmount-parameters*/}

`root.unmount` does not accept any parameters.


#### Returns {/*root-unmount-returns*/}

`render` returns `null`.

#### Caveats {/*root-unmount-caveats*/}

* Calling `root.unmount` will unmount all the components in the tree and "detach" React from the root DOM node.

* Once you call `root.unmount` you cannot call `root.render` again on the root. Attempting to call `root.render` on an unmounted root will throw a "Cannot update an unmounted root" error.

---

## Usage {/*usage*/}

### Hydrating server-rendered HTML {/*hydrating-server-rendered-html*/}

If your app's HTML was generated by [`react-dom/server`](/reference/react-dom/client/createRoot), you need to *hydrate* it on the client.

```js [[1, 3, "document.getElementById('root')"], [2, 3, "<App />"]]
import { hydrateRoot } from 'react-dom/client';

hydrateRoot(document.getElementById('root'), <App />);
````

This will hydrate the server HTML inside the <CodeStep step={1}>browser DOM node</CodeStep> with the <CodeStep step={2}>React component</CodeStep> for your app. Usually, you will do it once at startup. If you use a framework, it might do this behind the scenes for you.

To hydrate your app, React will "attach" your components' logic to the initial generated HTML from the server. Hydration turns the initial HTML snapshot from the server into a fully interactive app that runs in the browser.

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

You shouldn't need to call `hydrateRoot` again or to call it in more places. From this point on, React will be managing the DOM of your application. To update the UI, your components will [use state](/reference/react/useState) instead.

<Pitfall>

The React tree you pass to `hydrateRoot` needs to produce **the same output** as it did on the server.

This is important for the user experience. The user will spend some time looking at the server-generated HTML before your JavaScript code loads. Server rendering creates an illusion that the app loads faster by showing the HTML snapshot of its output. Suddenly showing different content breaks that illusion. This is why the server render output must match the initial render output on the client.

The most common causes leading to hydration errors include:

* Extra whitespace (like newlines) around the React-generated HTML inside the root node.
* Using checks like `typeof window !== 'undefined'` in your rendering logic.
* Using browser-only APIs like [`window.matchMedia`](https://developer.mozilla.org/en-US/docs/Web/API/Window/matchMedia) in your rendering logic.
* Rendering different data on the server and the client.

React recovers from some hydration errors, but **you must fix them like other bugs.** In the best case, they'll lead to a slowdown; in the worst case, event handlers can get attached to the wrong elements.

</Pitfall>

---

### Hydrating an entire document {/*hydrating-an-entire-document*/}

Apps fully built with React can render the entire document as JSX, including the [`<html>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/html) tag:

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

To hydrate the entire document, pass the [`document`](https://developer.mozilla.org/en-US/docs/Web/API/Window/document) global as the first argument to `hydrateRoot`:

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
