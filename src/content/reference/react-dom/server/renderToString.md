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

#### সাবধানতা {/*caveats*/}

* `renderToString` এর সীমিত Suspense সাপোর্ট রয়েছে। যদি কোনো কম্পোনেন্ট suspend করে, `renderToString` কোনো বিলম্ব ছাড়াই সেটির fallback কে HTML হিসেবে পাঠিয়ে দেয়।

* `renderToString` ব্রাউজারে কাজ করে, কিন্তু ক্লায়েন্ট সাইডে এটির ব্যাবহার [রিকমেন্ডেড না।](#removing-rendertostring-from-the-client-code)

---

## ব্যাবহার {/*usage*/}

### একটি React tree কে HTML হিসেবে একটি স্ট্রিং-এ রেন্ডার করা {/*rendering-a-react-tree-as-html-to-a-string*/}

আপনার সার্ভার রেসপন্সের সাথে পাঠানোর জন্য আপনার অ্যাপকে একটি HTML স্ট্রিং-এ রেন্ডার করতে `renderToString` কল করুনঃ

```js {5-6}
import { renderToString } from 'react-dom/server';

// The route handler syntax depends on your backend framework
app.use('/', (request, response) => {
  const html = renderToString(<App />);
  response.send(html);
});
```

এটি আপনার React কম্পোনেন্টগুলির প্রাথমিক নন-ইন্টারেক্টিভ HTML আউটপুট তৈরি করবে। ক্লায়েন্ট সাইডে, আপনাকে সেই সার্ভার-জেনারেটেড HTML কে *হাইড্রেট* এবং ইন্টারেক্টিভ করতে [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) কল করতে হবে।


<Pitfall>

`renderToString` streaming সাপোর্ট করে না এবং ডেটা (ফেচিং বা কোনো এসিংক্রোনাস কাজ) এর জন্য বিলম্ব করে না [বিকল্প সমূহ দেখুন।](#alternatives)

</Pitfall>

---

## বিকল্প সমূহ {/*alternatives*/}

### সার্ভারে `renderToString` এর বদলে একটি streaming মেথড ব্যাবহার করা {/*migrating-from-rendertostring-to-a-streaming-method-on-the-server*/}

`renderToString` সঙ্গে সঙ্গেই একটি স্ট্রিং রিটার্ন করে, তাই এটি স্ট্রিমিং বা ডেটার জন্য অপেক্ষা করা সাপোর্ট করে না।

যখন সম্ভব, আমরা এই fully-featured বিকল্পগুলো ব্যাবহার করা রেকমেন্ড করিঃ

* আপনি যদি Node.js ব্যবহার করেন, তাহলে [`renderToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) ব্যবহার করুন।
* আপনি যদি Deno বা [Web Streams](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) সহ একটি আধুনিক Edge রানটাইম ব্যবহার করেন, তাহলে [`renderToReadableStream`](/reference/react-dom/server/renderToReadableStream) ব্যবহার করুন।

আপনার সার্ভার environment যদি stream সাপোর্ট না করে, তাহলে আপনি `renderToString` ব্যবহার চালিয়ে যেতে পারেন।

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

