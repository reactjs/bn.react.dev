---
title: renderToString
---

<Pitfall>

`renderToString` streaming সাপোর্ট করে না এবং ডেটা (ফেচিং বা কোনো এসিংক্রোনাস কাজ) এর জন্য বিলম্ব করে না [বিকল্প সমূহ দেখুন।](#alternatives)

</Pitfall>

<Intro>

`renderToString` একটি React tree কে HTML string হিসেবে রেন্ডার করে।

```js
const html = renderToString(reactNode, options?)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `renderToString(reactNode, options?)` {/*rendertostring*/}

আপনার অ্যাপকে HTML এ রেন্ডার করতে, সার্ভার থেকে `renderToString` কল করুন।

```js
import { renderToString } from 'react-dom/server';

const html = renderToString(<App />);
```

সার্ভার-জেনারেটেড HTML কে ইন্টার‍্যাক্টিভ করতে, ক্লায়েন্ট থেকে [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) কল করুন।

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার্স {/*parameters*/}

* `reactNode`: একটি React node যাকে আপনি HTML এ রেন্ডার করতে চান। উদাহরণস্বরূপ,`<App />` এর মতো একটি JSX node ।

* **অপশনাল** `options`: সার্ভার রেন্ডারের জন্য একটি অবজেক্ট।
  * **অপশনাল** `identifierPrefix`: একটি string prefix (উপসর্গ) যেটি React [`useId`](/reference/react/useId) এর দ্বারা জেনারেট করা id এর সঙ্গে ব্যবহার করে। একই পেজে একাধিক root ব্যবহারের সময় আইডির সাথে আইডির সংঘর্ষ এড়াতে এটি উপকারে আসে। এটি [`hydrateRoot`](/reference/react-dom/client/hydrateRoot#parameters) এর কাছে পাস করা prefix এর অবশ্যই অনুরূপ হতে হবে।

#### রিটার্নস {/*returns*/}

একটি HTML স্ট্রিং।

#### Caveats {/*caveats*/}

* `renderToString` has limited Suspense support. If a component suspends, `renderToString` immediately sends its fallback as HTML.

* `renderToString` works in the browser, but using it in the client code is [not recommended.](#removing-rendertostring-from-the-client-code)

---

## Usage {/*usage*/}

### Rendering a React tree as HTML to a string {/*rendering-a-react-tree-as-html-to-a-string*/}

Call `renderToString` to render your app to an HTML string which you can send with your server response:

```js {5-6}
import { renderToString } from 'react-dom/server';

// The route handler syntax depends on your backend framework
app.use('/', (request, response) => {
  const html = renderToString(<App />);
  response.send(html);
});
```

This will produce the initial non-interactive HTML output of your React components. On the client, you will need to call [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) to *hydrate* that server-generated HTML and make it interactive.


<Pitfall>

`renderToString` does not support streaming or waiting for data. [See the alternatives.](#alternatives)

</Pitfall>

---

## Alternatives {/*alternatives*/}

### Migrating from `renderToString` to a streaming method on the server {/*migrating-from-rendertostring-to-a-streaming-method-on-the-server*/}

`renderToString` returns a string immediately, so it does not support streaming or waiting for data.

When possible, we recommend using these fully-featured alternatives:

* If you use Node.js, use [`renderToPipeableStream`.](/reference/react-dom/server/renderToPipeableStream)
* If you use Deno or a modern edge runtime with [Web Streams](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API), use [`renderToReadableStream`.](/reference/react-dom/server/renderToReadableStream)

You can continue using `renderToString` if your server environment does not support streams.

---

### Removing `renderToString` from the client code {/*removing-rendertostring-from-the-client-code*/}

Sometimes, `renderToString` is used on the client to convert some component to HTML.

```js {1-2}
// 🚩 Unnecessary: using renderToString on the client
import { renderToString } from 'react-dom/server';

const html = renderToString(<MyIcon />);
console.log(html); // For example, "<svg>...</svg>"
```

Importing `react-dom/server` **on the client** unnecessarily increases your bundle size and should be avoided. If you need to render some component to HTML in the browser, use [`createRoot`](/reference/react-dom/client/createRoot) and read HTML from the DOM:

```js
import { createRoot } from 'react-dom/client';
import { flushSync } from 'react-dom';

const div = document.createElement('div');
const root = createRoot(div);
flushSync(() => {
  root.render(<MyIcon />);
});
console.log(div.innerHTML); // For example, "<svg>...</svg>"
```

The [`flushSync`](/reference/react-dom/flushSync) call is necessary so that the DOM is updated before reading its [`innerHTML`](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML) property.

---

## Troubleshooting {/*troubleshooting*/}

### When a component suspends, the HTML always contains a fallback {/*when-a-component-suspends-the-html-always-contains-a-fallback*/}

`renderToString` does not fully support Suspense.

If some component suspends (for example, because it's defined with [`lazy`](/reference/react/lazy) or fetches data), `renderToString` will not wait for its content to resolve. Instead, `renderToString` will find the closest [`<Suspense>`](/reference/react/Suspense) boundary above it and render its `fallback` prop in the HTML. The content will not appear until the client code loads.

To solve this, use one of the [recommended streaming solutions.](#migrating-from-rendertostring-to-a-streaming-method-on-the-server) They can stream content in chunks as it resolves on the server so that the user sees the page being progressively filled in before the client code loads.

