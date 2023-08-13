---
title: hydrate
---

<Deprecated>

এই API ভবিষ্যতে React এর একটি মেজর ভার্সনে সরিয়ে ফেলা হবে।

React 18 এ, `hydrate` এর জায়গায় এসেছে [`hydrateRoot`।](/reference/react-dom/client/createRoot) React 18 এ `render` ব্যবহার করলে সতর্কতা দেখাবে যে আপনার অ্যাপ এমন আচরণ করবে যেন এতে React 17 চলছে। আরো জানুন [এখানে।](/blog/2022/03/08/react-18-upgrade-guide#updates-to-client-rendering-apis)

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
* `hydrate` expects the rendered content to be identical with the server-rendered content. React can patch up differences in text content, but you should treat mismatches as bugs and fix them.
* In development mode, React warns about mismatches during hydration. There are no guarantees that attribute differences will be patched up in case of mismatches. This is important for performance reasons because in most apps, mismatches are rare, and so validating all markup would be prohibitively expensive.
* You'll likely have only one `hydrate` call in your app. If you use a framework, it might do this call for you.
* If your app is client-rendered with no HTML rendered already, using `hydrate()` is not supported. Use [render()](/reference/react-dom/render) (for React 17 and below) or [createRoot()](/reference/react-dom/client/createRoot) (for React 18+) instead.

---

## Usage {/*usage*/}

Call `hydrate` to attach a <CodeStep step={1}>React component</CodeStep> into a server-rendered <CodeStep step={2}>browser DOM node</CodeStep>.

```js [[1, 3, "<App />"], [2, 3, "document.getElementById('root')"]]
import { hydrate } from 'react-dom';

hydrate(<App />, document.getElementById('root'));
```

Using `hydrate()` to render a client-only app (an app without server-rendered HTML) is not supported. Use [`render()`](/reference/react-dom/render) (in React 17 and below) or [`createRoot()`](/reference/react-dom/client/createRoot) (in React 18+) instead.

### Hydrating server-rendered HTML {/*hydrating-server-rendered-html*/}

In React, "hydration" is how React "attaches" to existing HTML that was already rendered by React in a server environment. During hydration, React will attempt to attach event listeners to the existing markup and take over rendering the app on the client.

In apps fully built with React, **you will usually only hydrate one "root", once at startup for your entire app**.

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

Usually you shouldn't need to call `hydrate` again or to call it in more places. From this point on, React will be managing the DOM of your application. To update the UI, your components will [use state.](/reference/react/useState)

For more information on hydration, see the docs for [`hydrateRoot`.](/reference/react-dom/client/hydrateRoot)

---

### Suppressing unavoidable hydration mismatch errors {/*suppressing-unavoidable-hydration-mismatch-errors*/}

If a single element’s attribute or text content is unavoidably different between the server and the client (for example, a timestamp), you may silence the hydration mismatch warning.

To silence hydration warnings on an element, add `suppressHydrationWarning={true}`:

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

This only works one level deep, and is intended to be an escape hatch. Don’t overuse it. Unless it’s text content, React still won’t attempt to patch it up, so it may remain inconsistent until future updates.

---

### Handling different client and server content {/*handling-different-client-and-server-content*/}

If you intentionally need to render something different on the server and the client, you can do a two-pass rendering. Components that render something different on the client can read a [state variable](/reference/react/useState) like `isClient`, which you can set to `true` in an [effect](/reference/react/useEffect):

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

This way the initial render pass will render the same content as the server, avoiding mismatches, but an additional pass will happen synchronously right after hydration.

<Pitfall>

This approach makes hydration slower because your components have to render twice. Be mindful of the user experience on slow connections. The JavaScript code may load significantly later than the initial HTML render, so rendering a different UI immediately after hydration may feel jarring to the user.

</Pitfall>
