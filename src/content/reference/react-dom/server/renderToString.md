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

<<<<<<< HEAD
### সার্ভারে `renderToString` এর বদলে একটি streaming মেথড ব্যাবহার করা {/*migrating-from-rendertostring-to-a-streaming-method-on-the-server*/}

`renderToString` সঙ্গে সঙ্গেই একটি স্ট্রিং রিটার্ন করে, তাই এটি স্ট্রিমিং বা ডেটার জন্য অপেক্ষা করা সাপোর্ট করে না।
=======
### Migrating from `renderToString` to a streaming render on the server {/*migrating-from-rendertostring-to-a-streaming-method-on-the-server*/}

`renderToString` returns a string immediately, so it does not support streaming content as it loads.
>>>>>>> 341c312916e1b657262bbe14b134a6f1779fecf1

যখন সম্ভব, আমরা এই fully-featured বিকল্পগুলো ব্যাবহার করা রেকমেন্ড করিঃ

* আপনি যদি Node.js ব্যবহার করেন, তাহলে [`renderToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) ব্যবহার করুন।
* আপনি যদি Deno বা [Web Streams](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) সহ একটি আধুনিক Edge রানটাইম ব্যবহার করেন, তাহলে [`renderToReadableStream`](/reference/react-dom/server/renderToReadableStream) ব্যবহার করুন।

আপনার সার্ভার environment যদি stream সাপোর্ট না করে, তাহলে আপনি `renderToString` ব্যবহার চালিয়ে যেতে পারেন।

---

<<<<<<< HEAD
### ক্লায়েন্ট কোড থেকে `renderToString` দূর করা {/*removing-rendertostring-from-the-client-code*/}
=======
### Migrating from `renderToString` to a static prerender on the server {/*migrating-from-rendertostring-to-a-static-prerender-on-the-server*/}

`renderToString` returns a string immediately, so it does not support waiting for data to load for static HTML generation.

We recommend using these fully-featured alternatives:

* If you use Node.js, use [`prerenderToNodeStream`.](/reference/react-dom/static/prerenderToNodeStream)
* If you use Deno or a modern edge runtime with [Web Streams](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API), use [`prerender`.](/reference/react-dom/static/prerender)

You can continue using `renderToString` if your static site generation environment does not support streams.

---

### Removing `renderToString` from the client code {/*removing-rendertostring-from-the-client-code*/}
>>>>>>> 341c312916e1b657262bbe14b134a6f1779fecf1

কখনো কখনো, কোনো কম্পোনেন্টকে HTML-এ রূপান্তর করতে ক্লায়েন্ট-সাইডে `renderToString` ব্যবহার করা হয়।

```js {1-2}
// 🚩 Unnecessary: using renderToString on the client
import { renderToString } from 'react-dom/server';

const html = renderToString(<MyIcon />);
console.log(html); // For example, "<svg>...</svg>"
```

**ক্লায়েন্ট-সাইডে** `react-dom/server` ইমপোর্ট করা বিনা প্রয়োজনে আপনার বান্ডল সাইজ বাড়িয়ে দেয় এবং এটা এড়ানো উচিত। যদি আপনার ব্রাউজারে কোনো কম্পোনেন্টকে HTML-এ রেন্ডার করতে হয়, তাহলে [`createRoot`](/reference/react-dom/client/createRoot) ব্যবহার করুন এবং DOM থেকে HTML রিড করুনঃ

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

এখানে [`flushSync`](/reference/react-dom/flushSync) কল করা জরুরী যাতে DOM তার [`innerHTML`](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML) প্রোপার্টি read করার আগে আপডেট হয়।

---

## ট্রাবলশুটিং {/*troubleshooting*/}

### যখন একটি কম্পোনেন্ট Suspense এ থাকে, তখন HTML এর সর্বদা একটি ফলব্যাক থাকে {/*when-a-component-suspends-the-html-always-contains-a-fallback*/}

`renderToString` পুরোপুরি ভাবে Suspense সাপোর্ট করে না।

যদি কোনো কম্পোনেন্ট সাসপেন্স অবস্থায় থাকে (যেমন, যদি এটাকে [`lazy`](/reference/react/lazy) করা হয় কিংবা এটা ডেটা ফেচ করে), তাহলে এর কন্টেন্ট resolve হওয়ার জন্য `renderToString` অপেক্ষা করবে না। পরিবর্তে, `renderToString` উপরের সবচেয়ে কাছের [`<Suspense>`](/reference/react/Suspense) বাউন্ডারি খুঁজে বের করবে এবং HTML-এ এর `fallback` প্রপ রেন্ডার করবে। ক্লায়েন্ট কোড লোড না হওয়া পর্যন্ত ঐ কন্টেন্ট প্রদর্শিত হবে না।

<<<<<<< HEAD
এই সমস্যা সমাধান করতে, [রেকমেন্ডেড স্ট্রিমিং সমাধানগুলোর](#migrating-from-rendertostring-to-a-streaming-method-on-the-server) একটি ব্যবহার করুন। এ সমাধানগুলোর ক্ষেত্রে সার্ভারে resolve হওয়ার সাথে সাথে কন্টেন্ট ভাগে ভাগে স্ট্রিম হয়ে আসতে পারবে যাতে ইউজার ক্লায়েন্ট কোড লোড হওয়ার আগেই পৃষ্ঠাটি ক্রমে ক্রমে পূরণ হতে দেখতে পায়।
=======
To solve this, use one of the [recommended streaming solutions.](#alternatives) For server side rendering, they can stream content in chunks as it resolves on the server so that the user sees the page being progressively filled in before the client code loads. For static site generation, they can wait for all the content to resolve before generating the static HTML.
>>>>>>> 341c312916e1b657262bbe14b134a6f1779fecf1

