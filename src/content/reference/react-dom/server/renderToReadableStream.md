---
title: renderToReadableStream
---

<Intro>

`renderToReadableStream` একটি রিয়েক্ট ট্রিকে একটি [Readable Web Stream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream) এ রেন্ডার করে।

```js
const stream = await renderToReadableStream(reactNode, options?)
```

</Intro>

<InlineToc />

<Note>

এই API [Web Streams](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) এর উপর নির্ভর করে। Node.js এর জন্য, এর পরিবর্তে [`renderToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) ব্যবহার করুন।

</Note>

---

## রেফারেন্স {/*reference*/}

### `renderToReadableStream(reactNode, options?)` {/*rendertoreadablestream*/}

আপনার রিয়েক্ট ট্রিকে HTML হিসেবে একটি [Readable Web Stream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream) এ রেন্ডার করতে `renderToReadableStream` কল করুন।

```js
import { renderToReadableStream } from 'react-dom/server';

async function handler(request) {
  const stream = await renderToReadableStream(<App />, {
    bootstrapScripts: ['/main.js']
  });
  return new Response(stream, {
    headers: { 'content-type': 'text/html' },
  });
}
```

ক্লায়েন্টে, সার্ভারে জেনারেট হওয়া HTML কে ইন্টারঅ্যাক্টিভ করতে [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) কল করুন।

[নিচে আরও উদাহরণ দেখুন।](#usage)

#### প্যারামিটারস {/*parameters*/}

* `reactNode`: একটি রিয়েক্ট নোড যেটিকে আপনি HTML এ রেন্ডার করতে চান। উদাহরণস্বরূপ, `<App />` এর মতো একটি JSX এলিমেন্ট। এটা প্রত্যাশিত যে, এটি পুরো ডকুমেন্টকে ধারণ করবে, তাই `App` কম্পোনেন্টটির `<html>` ট্যাগ রেন্ডার করার কথা।

* **optional** `options`: স্ট্রিমিং অপশনসহ একটি অবজেক্ট।
  * **optional** `bootstrapScriptContent`: যদি দেওয়া হয়, তাহলে এই স্ট্রিংটি একটি ইনলাইন `<script>` ট্যাগের ভেতরে রাখা হবে।
  * **optional** `bootstrapScripts`: পেজে এমিট করার জন্য `<script>` ট্যাগগুলোর স্ট্রিং URL এর একটি অ্যারে। [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) কল করে এমন `<script>` যুক্ত করতে এটি ব্যবহার করুন। আপনি যদি ক্লায়েন্টে একেবারেই রিয়েক্ট রান করাতে না চান, তাহলে এটি বাদ দিন।
  * **optional** `bootstrapModules`: `bootstrapScripts` এর মতো, কিন্তু এর পরিবর্তে [`<script type="module">`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) এমিট করে।
  * **optional** `formState`: একটি [Server Function](/reference/rsc/server-functions) দিয়ে হ্যান্ডেল করা ফর্ম সাবমিশন থেকে পাওয়া ফর্ম স্টেট। যদি পেজটি এমন কোনো ফর্ম সাবমিশনের রেসপন্সে রেন্ডার করা হয় যেটি `permalink` সহ [`useActionState`](/reference/react/useActionState) ব্যবহার করে, তাহলে ফলস্বরূপ পাওয়া ফর্ম স্টেটটি পাস করুন যাতে রিয়েক্ট হাইড্রেশনের (hydration) জন্য এটিকে HTML এ যুক্ত করে দিতে পারে। ক্লায়েন্টে [`hydrateRoot`](/reference/react-dom/client/hydrateRoot#parameters) এও একই ভ্যালু পাস করতে হবে। সাধারণত আপনার ফ্রেমওয়ার্ক এটি পাস করে দেয়।
  * **optional** `identifierPrefix`: [`useId`](/reference/react/useId) দ্বারা তৈরি ID এর জন্য রিয়েক্ট যে স্ট্রিং প্রিফিক্স ব্যবহার করে। একই পেজে একাধিক রুট ব্যবহার করার সময় কনফ্লিক্ট এড়াতে এটি কাজে আসে। [`hydrateRoot`](/reference/react-dom/client/hydrateRoot#parameters) এ পাস করা প্রিফিক্সের সাথে অবশ্যই একই হতে হবে।
  * **optional** `importMap`: `imports` এবং `scopes` প্রপার্টিসহ একটি [import map](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script/type/importmap) অবজেক্ট। রিয়েক্ট যেকোনো মডিউল স্ক্রিপ্টের আগে এটিকে একটি ইনলাইন `<script type="importmap">` ট্যাগ হিসেবে এমিট করে, যাতে `<script type="module">` ট্যাগগুলো (উদাহরণস্বরূপ, `bootstrapModules` থেকে আসা ট্যাগ) বেয়ার মডিউল স্পেসিফায়ার (bare module specifier) ব্যবহার করতে পারে। <CanaryBadge /> `nonce` সেট করা থাকলে, এটি import map স্ক্রিপ্টেও প্রয়োগ করা হয়।
  * **optional** `maxHeadersLength`: `onHeaders` এ পাস করা হেডার কনটেন্টের সর্বোচ্চ মোট দৈর্ঘ্য, যা UTF-16 কোড ইউনিটে মাপা হয়। ডিফল্ট ভ্যালু 2000। এই সীমায় পৌঁছানোর পর রিয়েক্ট হেডারে আর কোনো রিসোর্স হিন্ট যোগ করে না।
  * **optional** `namespaceURI`: স্ট্রিমের জন্য রুট [namespace URI](https://developer.mozilla.org/en-US/docs/Web/API/Document/createElementNS#important_namespace_uris) সহ একটি স্ট্রিং। ডিফল্টভাবে এটি সাধারণ HTML। SVG এর জন্য `'http://www.w3.org/2000/svg'` অথবা MathML এর জন্য `'http://www.w3.org/1998/Math/MathML'` পাস করুন।
  * **optional** `nonce`: [`script-src` Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) এর জন্য স্ক্রিপ্টগুলোকে অনুমতি দিতে একটি [`nonce`](http://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#nonce) স্ট্রিং। স্ক্রিপ্ট এবং স্টাইলের জন্য আলাদা nonce ব্যবহার করতে চাইলে, এর পরিবর্তে `script` এবং `style` প্রপার্টিসহ একটি অবজেক্ট পাস করুন।
  * **optional** `onBrowserBailout`: একটি কলব্যাক যেটি রিয়েক্ট তখন কল করে যখন এটি ব্রাউজারের রিপ্লেস করার জন্য একটি Suspense ফলব্যাক রেখে দিয়ে [`browser()`](/reference/react-dom/browser) থেকে রিকভার করে। এটি ব্রাউজার-অনলি রেন্ডারের বর্ণনা দেওয়া একটি `Error` এবং `componentStack` সহ একটি `errorInfo` অবজেক্ট রিসিভ করে। যদি `browser` এ কোনো কারণ (reason) পাস করা হয়ে থাকে, তবে সেটি `error.cause` হিসেবে পাওয়া যায়। ডিফল্টভাবে, রিয়েক্ট কিছুই করে না। [ব্রাউজার-অনলি রেন্ডারিং কীভাবে রিপোর্ট করবেন দেখুন।](/reference/react-dom/browser#reporting-browser-only-rendering-on-the-server)
  * **optional** `onError`: একটি কলব্যাক যেটি যেকোনো সার্ভার এরর হলেই ফায়ার হয়, সেটি [রিকভারযোগ্য](#recovering-from-errors-outside-the-shell) হোক বা [না হোক।](#recovering-from-errors-inside-the-shell) ডিফল্টভাবে, এটি শুধু `console.error` কল করে। আপনি যদি [ক্র্যাশ রিপোর্ট লগ করতে](#logging-crashes-on-the-server) এটিকে ওভাররাইড করেন, তাহলে নিশ্চিত করুন যে আপনি তখনও `console.error` কল করছেন। শেল এমিট হওয়ার আগে [স্ট্যাটাস কোড অ্যাডজাস্ট করতেও](#setting-the-status-code) আপনি এটি ব্যবহার করতে পারেন।
  * **optional** `onHeaders`: একটি কলব্যাক যেটি তখন ফায়ার হয় যখন রিয়েক্ট ডকুমেন্টের জন্য রিসোর্স হিন্টগুলো (resource hints) নির্ধারণ করে ফেলে, যেমন প্রিকানেক্ট (preconnect) এবং স্টাইলশিট, ফন্ট বা হাই-প্রায়োরিটি ইমেজের প্রিলোড (preload)। এটি সংশ্লিষ্ট [`Link` হেডারের](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/link) ভ্যালুসহ একটি [`Headers`](https://developer.mozilla.org/en-US/docs/Web/API/Headers) ইনস্ট্যান্স রিসিভ করে, যাতে আপনি এটিকে একটি HTTP রেসপন্স হেডার হিসেবে অথবা একটি [103 Early Hints](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/103) রেসপন্স হিসেবে পাঠাতে পারেন। পাঠানোর মতো কোনো রিসোর্স হিন্ট না থাকলেও রিয়েক্ট এটিকে কল করে। হেডার কনটেন্টের সীমা `maxHeadersLength` দ্বারা নির্ধারিত হয়।
  * **optional** `progressiveChunkSize`: একটি চাঙ্কে (chunk) বাইটের সংখ্যা। [ডিফল্ট হিউরিস্টিক সম্পর্কে আরও পড়ুন।](https://github.com/react/react/blob/14c2be8dac2d5482fda8a0906a31d239df8551fc/packages/react-server/src/ReactFizzServer.js#L210-L225)
  * **optional** `signal`: একটি [abort signal](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal) যেটি আপনাকে [সার্ভার রেন্ডারিং অ্যাবোর্ট করতে](#aborting-server-rendering) এবং বাকিটা ক্লায়েন্টে রেন্ডার করতে দেয়।


#### রিটার্নস {/*returns*/}

`renderToReadableStream` একটি Promise রিটার্ন করে:

- যদি [শেল](#specifying-what-goes-into-the-shell) রেন্ডার করা সফল হয়, তাহলে সেই Promise টি একটি [Readable Web Stream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream) এ রিজলভ (resolve) হবে।
- যদি শেল রেন্ডার করা ব্যর্থ হয়, তাহলে Promise টি রিজেক্ট (reject) হবে। [একটি ফলব্যাক শেল আউটপুট করতে এটি ব্যবহার করুন।](#recovering-from-errors-inside-the-shell)

রিটার্ন করা স্ট্রিমটিতে একটি অতিরিক্ত প্রপার্টি থাকে:

* `allReady`: একটি Promise যেটি তখন রিজলভ হয় যখন [শেল](#specifying-what-goes-into-the-shell) এবং সমস্ত অতিরিক্ত [কনটেন্ট](#streaming-more-content-as-it-loads) সহ সকল রেন্ডারিং সম্পন্ন হয়। [ক্রলার এবং স্ট্যাটিক জেনারেশনের জন্য](#waiting-for-all-content-to-load-for-crawlers-and-static-generation) রেসপন্স রিটার্ন করার আগে আপনি `await stream.allReady` করতে পারেন। আপনি যদি এটি করেন, তাহলে কোনো প্রোগ্রেসিভ লোডিং (progressive loading) পাবেন না। স্ট্রিমটিতে চূড়ান্ত HTML থাকবে।

---

## ব্যবহার {/*usage*/}

### রিয়েক্ট ট্রিকে HTML হিসেবে একটি Readable Web Stream এ রেন্ডার করা {/*rendering-a-react-tree-as-html-to-a-readable-web-stream*/}

আপনার রিয়েক্ট ট্রিকে HTML হিসেবে একটি [Readable Web Stream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream) এ রেন্ডার করতে `renderToReadableStream` কল করুন:

```js [[1, 4, "<App />"], [2, 5, "['/main.js']"]]
import { renderToReadableStream } from 'react-dom/server';

async function handler(request) {
  const stream = await renderToReadableStream(<App />, {
    bootstrapScripts: ['/main.js']
  });
  return new Response(stream, {
    headers: { 'content-type': 'text/html' },
  });
}
```

<CodeStep step={1}>রুট কম্পোনেন্টের</CodeStep> সাথে, আপনাকে <CodeStep step={2}>বুটস্ট্র্যাপ `<script>` পাথগুলোর</CodeStep> একটি লিস্ট দিতে হবে। আপনার রুট কম্পোনেন্টটির **রুট `<html>` ট্যাগসহ পুরো ডকুমেন্ট রিটার্ন করা উচিত।**

উদাহরণস্বরূপ, এটি দেখতে এরকম হতে পারে:

```js [[1, 1, "App"]]
export default function App() {
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

রিয়েক্ট [doctype](https://developer.mozilla.org/en-US/docs/Glossary/Doctype) এবং আপনার <CodeStep step={2}>বুটস্ট্র্যাপ `<script>` ট্যাগগুলো</CodeStep> ফলাফলস্বরূপ পাওয়া HTML স্ট্রিমে ইনজেক্ট করবে:

```html [[2, 5, "/main.js"]]
<!DOCTYPE html>
<html>
  <!-- ... HTML from your components ... -->
</html>
<script src="/main.js" async=""></script>
```

ক্লায়েন্টে, আপনার বুটস্ট্র্যাপ স্ক্রিপ্টের উচিত [`hydrateRoot` কল করে পুরো `document` হাইড্রেট করা:](/reference/react-dom/client/hydrateRoot#hydrating-an-entire-document)

```js [[1, 4, "<App />"]]
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document, <App />);
```

এটি সার্ভারে জেনারেট হওয়া HTML এ ইভেন্ট লিসেনার যুক্ত করবে এবং এটিকে ইন্টারঅ্যাক্টিভ করে তুলবে।

<DeepDive>

#### বিল্ড আউটপুট থেকে CSS এবং JS অ্যাসেট পাথ রিড করা {/*reading-css-and-js-asset-paths-from-the-build-output*/}

চূড়ান্ত অ্যাসেট URL গুলো (যেমন JavaScript এবং CSS ফাইল) প্রায়ই বিল্ডের পরে হ্যাশ করা হয়। উদাহরণস্বরূপ, `styles.css` এর পরিবর্তে আপনি হয়তো `styles.123456.css` পাবেন। স্ট্যাটিক অ্যাসেটের ফাইলনেম হ্যাশ করা নিশ্চিত করে যে একই অ্যাসেটের প্রতিটি আলাদা বিল্ডের ফাইলনেম আলাদা হবে। এটি উপকারী কারণ এটি আপনাকে স্ট্যাটিক অ্যাসেটের জন্য নিরাপদে লং-টার্ম ক্যাশিং (long-term caching) চালু করতে দেয়: একটি নির্দিষ্ট নামের ফাইলের কনটেন্ট কখনো পরিবর্তন হবে না।

তবে, বিল্ড হওয়ার আগ পর্যন্ত যদি আপনি অ্যাসেট URL গুলো না জানেন, তাহলে সেগুলো সোর্স কোডে রাখার কোনো উপায় নেই। উদাহরণস্বরূপ, আগের মতো JSX এ `"/styles.css"` হার্ডকোড করা কাজ করবে না। এগুলোকে আপনার সোর্স কোডের বাইরে রাখতে, আপনার রুট কম্পোনেন্ট একটি প্রপ হিসেবে পাস করা ম্যাপ থেকে আসল ফাইলনেমগুলো রিড করতে পারে:

```js {1,6}
export default function App({ assetMap }) {
  return (
    <html>
      <head>
        <title>My app</title>
        <link rel="stylesheet" href={assetMap['styles.css']}></link>
      </head>
      ...
    </html>
  );
}
```

সার্ভারে, `<App assetMap={assetMap} />` রেন্ডার করুন এবং অ্যাসেট URL গুলোসহ আপনার `assetMap` পাস করুন:

```js {1-5,8,9}
// You'd need to get this JSON from your build tooling, e.g. read it from the build output.
const assetMap = {
  'styles.css': '/styles.123456.css',
  'main.js': '/main.123456.js'
};

async function handler(request) {
  const stream = await renderToReadableStream(<App assetMap={assetMap} />, {
    bootstrapScripts: [assetMap['/main.js']]
  });
  return new Response(stream, {
    headers: { 'content-type': 'text/html' },
  });
}
```

যেহেতু আপনার সার্ভার এখন `<App assetMap={assetMap} />` রেন্ডার করছে, তাই হাইড্রেশন এরর এড়াতে আপনাকে ক্লায়েন্টেও `assetMap` সহ এটি রেন্ডার করতে হবে। আপনি এভাবে `assetMap` সিরিয়ালাইজ (serialize) করে ক্লায়েন্টে পাস করতে পারেন:

```js {9-10}
// You'd need to get this JSON from your build tooling.
const assetMap = {
  'styles.css': '/styles.123456.css',
  'main.js': '/main.123456.js'
};

async function handler(request) {
  const stream = await renderToReadableStream(<App assetMap={assetMap} />, {
    // Careful: It's safe to stringify() this because this data isn't user-generated.
    bootstrapScriptContent: `window.assetMap = ${JSON.stringify(assetMap)};`,
    bootstrapScripts: [assetMap['/main.js']],
  });
  return new Response(stream, {
    headers: { 'content-type': 'text/html' },
  });
}
```

উপরের উদাহরণে, `bootstrapScriptContent` অপশনটি একটি অতিরিক্ত ইনলাইন `<script>` ট্যাগ যোগ করে যেটি ক্লায়েন্টে গ্লোবাল `window.assetMap` ভ্যারিয়েবল সেট করে। এটি ক্লায়েন্ট কোডকে একই `assetMap` রিড করতে দেয়:

```js {4}
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document, <App assetMap={window.assetMap} />);
```

ক্লায়েন্ট এবং সার্ভার উভয়ই একই `assetMap` প্রপসহ `App` রেন্ডার করে, তাই কোনো হাইড্রেশন এরর হবে না।

</DeepDive>

---

### লোড হতে হতে আরও কনটেন্ট স্ট্রিম করা {/*streaming-more-content-as-it-loads*/}

স্ট্রিমিং ইউজারকে সার্ভারে সব ডেটা লোড হওয়ার আগেই কনটেন্ট দেখা শুরু করতে দেয়। উদাহরণস্বরূপ, একটি প্রোফাইল পেজের কথা ভাবুন যেটি একটি কভার, ফ্রেন্ড এবং ফটোসহ একটি সাইডবার, এবং পোস্টের একটি লিস্ট দেখায়:

```js
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Sidebar>
        <Friends />
        <Photos />
      </Sidebar>
      <Posts />
    </ProfileLayout>
  );
}
```

ধরুন `<Posts />` এর জন্য ডেটা লোড হতে কিছুটা সময় লাগে। আদর্শভাবে, আপনি পোস্টগুলোর জন্য অপেক্ষা না করেই ইউজারকে প্রোফাইল পেজের বাকি কনটেন্ট দেখাতে চাইবেন। এটি করতে, [`Posts` কে একটি `<Suspense>` বাউন্ডারিতে র‍্যাপ করুন:](/reference/react/Suspense#displaying-a-fallback-while-content-is-loading)

```js {9,11}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Sidebar>
        <Friends />
        <Photos />
      </Sidebar>
      <Suspense fallback={<PostsGlimmer />}>
        <Posts />
      </Suspense>
    </ProfileLayout>
  );
}
```

এটি রিয়েক্টকে `Posts` তার ডেটা লোড করার আগেই HTML স্ট্রিমিং শুরু করতে বলে। রিয়েক্ট প্রথমে লোডিং ফলব্যাক (`PostsGlimmer`) এর HTML পাঠাবে, এবং তারপর, যখন `Posts` তার ডেটা লোড করা শেষ করবে, রিয়েক্ট বাকি HTML টি একটি ইনলাইন `<script>` ট্যাগসহ পাঠাবে যেটি লোডিং ফলব্যাককে সেই HTML দিয়ে রিপ্লেস করবে। ইউজারের দৃষ্টিকোণ থেকে, পেজটি প্রথমে `PostsGlimmer` সহ দেখা যাবে, পরে সেটি `Posts` দিয়ে রিপ্লেস হবে।

আরও গ্রানুলার (granular) লোডিং সিকোয়েন্স তৈরি করতে আপনি [`<Suspense>` বাউন্ডারিগুলোকে আরও নেস্ট](/reference/react/Suspense#revealing-nested-content-as-it-loads) করতে পারেন:

```js {5,13}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<BigSpinner />}>
        <Sidebar>
          <Friends />
          <Photos />
        </Sidebar>
        <Suspense fallback={<PostsGlimmer />}>
          <Posts />
        </Suspense>
      </Suspense>
    </ProfileLayout>
  );
}
```


এই উদাহরণে, রিয়েক্ট পেজটির স্ট্রিমিং আরও আগে শুরু করতে পারে। শুধুমাত্র `ProfileLayout` এবং `ProfileCover` কে প্রথমে রেন্ডারিং শেষ করতে হবে কারণ এগুলো কোনো `<Suspense>` বাউন্ডারিতে র‍্যাপ করা নেই। তবে, যদি `Sidebar`, `Friends`, অথবা `Photos` এর কোনো ডেটা লোড করার প্রয়োজন হয়, রিয়েক্ট তার পরিবর্তে `BigSpinner` ফলব্যাকের HTML পাঠাবে। তারপর, যত বেশি ডেটা পাওয়া যাবে, তত বেশি কনটেন্ট প্রকাশিত হতে থাকবে যতক্ষণ না সবকিছু দৃশ্যমান হয়।

স্ট্রিমিংয়ের জন্য ব্রাউজারে রিয়েক্ট নিজে লোড হওয়া পর্যন্ত, অথবা আপনার অ্যাপ ইন্টারঅ্যাক্টিভ হওয়া পর্যন্ত অপেক্ষা করার প্রয়োজন নেই। সার্ভার থেকে আসা HTML কনটেন্ট কোনো `<script>` ট্যাগ লোড হওয়ার আগেই ধাপে ধাপে প্রকাশিত হতে থাকবে।

[HTML স্ট্রিমিং কীভাবে কাজ করে সে সম্পর্কে আরও পড়ুন।](https://github.com/reactwg/react-18/discussions/37)

<Note>

শুধুমাত্র এমন কোনো সোর্স থেকে রিড করা ডেটা রেন্ডারিংয়ের সময় সাসপেন্ড (suspend) করবে যেটি [একটি Suspense বাউন্ডারিকে অ্যাক্টিভেট করে](/reference/react/Suspense#what-activates-a-suspense-boundary), যেমন [`use`](/reference/react/use) দিয়ে রিড করা একটি Promise। কোনো Effect বা ইভেন্ট হ্যান্ডলারের ভেতরে ফেচ করা ডেটা Suspense শনাক্ত করতে পারে না।

</Note>

---

### শেলে কী যাবে তা নির্দিষ্ট করা {/*specifying-what-goes-into-the-shell*/}

আপনার অ্যাপের যে অংশটি কোনো `<Suspense>` বাউন্ডারির বাইরে থাকে সেটিকে বলা হয় *শেল (shell):*

```js {3-5,13,14}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<BigSpinner />}>
        <Sidebar>
          <Friends />
          <Photos />
        </Sidebar>
        <Suspense fallback={<PostsGlimmer />}>
          <Posts />
        </Suspense>
      </Suspense>
    </ProfileLayout>
  );
}
```

এটা ঠিক করে দেয় যে ইউজার সবার আগে কোন লোডিং স্টেটটি দেখতে পারে:

```js {3-5,13
<ProfileLayout>
  <ProfileCover />
  <BigSpinner />
</ProfileLayout>
```

যদি আপনি রুটে পুরো অ্যাপটিকে একটি `<Suspense>` বাউন্ডারিতে র‍্যাপ করেন, তাহলে শেলে শুধু সেই স্পিনারটি থাকবে। তবে, এটি একটি ভালো ইউজার এক্সপেরিয়েন্স নয় কারণ একটু বেশি অপেক্ষা করে আসল লেআউট দেখার চেয়ে স্ক্রিনে একটি বড় স্পিনার দেখা বেশি ধীর এবং বিরক্তিকর মনে হতে পারে। এই কারণেই সাধারণত আপনি `<Suspense>` বাউন্ডারিগুলো এমনভাবে রাখতে চাইবেন যাতে শেলটি *ন্যূনতম কিন্তু সম্পূর্ণ* মনে হয়--পুরো পেজ লেআউটের একটি স্কেলেটনের মতো।

পুরো শেল রেন্ডার হওয়ার সাথে সাথেই `renderToReadableStream` এর async কলটি একটি `stream` এ রিজলভ হবে। সাধারণত, আপনি তখনই সেই `stream` দিয়ে একটি রেসপন্স তৈরি করে রিটার্ন করার মাধ্যমে স্ট্রিমিং শুরু করবেন:

```js {5}
async function handler(request) {
  const stream = await renderToReadableStream(<App />, {
    bootstrapScripts: ['/main.js']
  });
  return new Response(stream, {
    headers: { 'content-type': 'text/html' },
  });
}
```

`stream` টি রিটার্ন হওয়ার সময়ে, নেস্টেড `<Suspense>` বাউন্ডারির কম্পোনেন্টগুলো তখনও ডেটা লোড করতে থাকতে পারে।

---

### সার্ভারে ক্র্যাশ লগ করা {/*logging-crashes-on-the-server*/}

স্বাভাবিকভাবে, সার্ভারের সকল এরর কনসোলে লগ হয়। ক্র্যাশ রিপোর্ট লগ করতে আপনি এই আচরণ ওভাররাইড করতে পারেন:

```js {4-7}
async function handler(request) {
  const stream = await renderToReadableStream(<App />, {
    bootstrapScripts: ['/main.js'],
    onError(error) {
      console.error(error);
      logServerCrashReport(error);
    }
  });
  return new Response(stream, {
    headers: { 'content-type': 'text/html' },
  });
}
```

যদি আপনি একটি কাস্টম `onError` ইমপ্লিমেন্টেশন দেন, তাহলে উপরের মতো কনসোলেও এরর লগ করতে ভুলবেন না।

---

### শেলের ভেতরের এরর থেকে রিকভার করা {/*recovering-from-errors-inside-the-shell*/}

এই উদাহরণে, শেলে `ProfileLayout`, `ProfileCover`, এবং `PostsGlimmer` রয়েছে:

```js {3-5,7-8}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<PostsGlimmer />}>
        <Posts />
      </Suspense>
    </ProfileLayout>
  );
}
```

যদি এই কম্পোনেন্টগুলো রেন্ডার করার সময় কোনো এরর হয়, রিয়েক্টের কাছে ক্লায়েন্টে পাঠানোর জন্য কোনো বোধগম্য HTML থাকবে না। শেষ অবলম্বন হিসেবে সার্ভার রেন্ডারিংয়ের উপর নির্ভর করে না এমন একটি ফলব্যাক HTML পাঠাতে আপনার `renderToReadableStream` কলটিকে একটি `try...catch` এ র‍্যাপ করুন:

```js {2,13-18}
async function handler(request) {
  try {
    const stream = await renderToReadableStream(<App />, {
      bootstrapScripts: ['/main.js'],
      onError(error) {
        console.error(error);
        logServerCrashReport(error);
      }
    });
    return new Response(stream, {
      headers: { 'content-type': 'text/html' },
    });
  } catch (error) {
    return new Response('<h1>Something went wrong</h1>', {
      status: 500,
      headers: { 'content-type': 'text/html' },
    });
  }
}
```

যদি শেল জেনারেট করার সময় কোনো এরর হয়, তাহলে `onError` এবং আপনার `catch` ব্লক উভয়ই ফায়ার করবে। এরর রিপোর্টিংয়ের জন্য `onError` ব্যবহার করুন এবং ফলব্যাক HTML ডকুমেন্ট পাঠানোর জন্য `catch` ব্লক ব্যবহার করুন। আপনার ফলব্যাক HTML যে একটি এরর পেজই হতে হবে এমন নয়। বরং, আপনি একটি বিকল্প শেল যুক্ত করতে পারেন যেটি শুধু ক্লায়েন্টে আপনার অ্যাপ রেন্ডার করে।

---

### শেলের বাইরের এরর থেকে রিকভার করা {/*recovering-from-errors-outside-the-shell*/}

এই উদাহরণে, `<Posts />` কম্পোনেন্টটি `<Suspense>` এ র‍্যাপ করা আছে তাই এটি শেলের *অংশ নয়*:

```js {6}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<PostsGlimmer />}>
        <Posts />
      </Suspense>
    </ProfileLayout>
  );
}
```

যদি `Posts` কম্পোনেন্টে বা এর ভেতরে কোথাও কোনো এরর ঘটে, রিয়েক্ট [এর থেকে রিকভার করার চেষ্টা করবে:](/reference/react/Suspense#providing-a-fallback-for-server-errors-and-server-only-content)

1. এটি নিকটতম `<Suspense>` বাউন্ডারির (`PostsGlimmer`) লোডিং ফলব্যাকটি HTML এ এমিট করবে।
2. এটি সার্ভারে আর `Posts` কনটেন্ট রেন্ডার করার চেষ্টা থেকে "হাল ছেড়ে দেবে"।
3. যখন JavaScript কোড ক্লায়েন্টে লোড হবে, রিয়েক্ট ক্লায়েন্টে `Posts` রেন্ডার করার জন্য *পুনরায় চেষ্টা করবে*।

যদি ক্লায়েন্টে `Posts` রেন্ডার করার পুনরায় চেষ্টা *এবারও* ব্যর্থ হয়, রিয়েক্ট ক্লায়েন্টে এররটি থ্রো করবে। রেন্ডারিংয়ের সময় থ্রো হওয়া সব এররের মতোই, [নিকটতম প্যারেন্ট এরর বাউন্ডারি](/reference/react/Component#static-getderivedstatefromerror) নির্ধারণ করে যে এররটি ইউজারের কাছে কীভাবে উপস্থাপন করা হবে। বাস্তবে, এর মানে হলো যতক্ষণ না নিশ্চিত হওয়া যায় যে এররটি রিকভারযোগ্য নয়, ততক্ষণ ইউজার একটি লোডিং ইন্ডিকেটর দেখতে পাবে।

যদি ক্লায়েন্টে `Posts` রেন্ডার করার পুনরায় চেষ্টা সফল হয়, তাহলে সার্ভার থেকে আসা লোডিং ফলব্যাকটি ক্লায়েন্টে রেন্ডার করা আউটপুট দিয়ে রিপ্লেস হয়ে যাবে। ইউজার জানতেও পারবে না যে সার্ভারে কোনো এরর হয়েছিল। তবে, সার্ভারের `onError` কলব্যাক এবং ক্লায়েন্টের [`onRecoverableError`](/reference/react-dom/client/hydrateRoot#hydrateroot) কলব্যাক ফায়ার হবে যাতে আপনি এররটি সম্পর্কে নোটিফাইড হতে পারেন।

---

### স্ট্যাটাস কোড সেট করা {/*setting-the-status-code*/}

স্ট্রিমিং একটি ট্রেডঅফ (tradeoff) নিয়ে আসে। আপনি যত তাড়াতাড়ি সম্ভব পেজ স্ট্রিমিং শুরু করতে চান যাতে ইউজার তাড়াতাড়ি কনটেন্ট দেখতে পায়। তবে, একবার স্ট্রিমিং শুরু করলে, আপনি আর রেসপন্স স্ট্যাটাস কোড সেট করতে পারবেন না।

আপনার অ্যাপকে শেল (সকল `<Suspense>` বাউন্ডারির উপরের অংশ) এবং বাকি কনটেন্টে [ভাগ করে](#specifying-what-goes-into-the-shell), আপনি ইতিমধ্যে এই সমস্যার একটি অংশ সমাধান করে ফেলেছেন। যদি শেলে এরর হয়, তাহলে আপনার `catch` ব্লকটি রান করবে যা আপনাকে এরর স্ট্যাটাস কোড সেট করতে দেয়। অন্যথায়, আপনি জানেন যে অ্যাপটি ক্লায়েন্টে রিকভার করতে পারে, তাই আপনি "OK" পাঠাতে পারেন।

```js {11}
async function handler(request) {
  try {
    const stream = await renderToReadableStream(<App />, {
      bootstrapScripts: ['/main.js'],
      onError(error) {
        console.error(error);
        logServerCrashReport(error);
      }
    });
    return new Response(stream, {
      status: 200,
      headers: { 'content-type': 'text/html' },
    });
  } catch (error) {
    return new Response('<h1>Something went wrong</h1>', {
      status: 500,
      headers: { 'content-type': 'text/html' },
    });
  }
}
```

যদি শেলের *বাইরের* (অর্থাৎ কোনো `<Suspense>` বাউন্ডারির ভেতরে থাকা) কোনো কম্পোনেন্ট একটি এরর থ্রো করে, রিয়েক্ট রেন্ডারিং থামাবে না। এর মানে হলো `onError` কলব্যাক ফায়ার হবে, কিন্তু আপনার কোড `catch` ব্লকে না গিয়েই রান হতে থাকবে। এর কারণ হলো রিয়েক্ট ক্লায়েন্টে ওই এরর থেকে রিকভার করার চেষ্টা করবে, [উপরে যেমন ব্যাখ্যা করা হয়েছে।](#recovering-from-errors-outside-the-shell)

তবে চাইলে, কোনো কিছুতে এরর হয়েছে—এই তথ্যটি ব্যবহার করে আপনি স্ট্যাটাস কোড সেট করতে পারেন:

```js {3,7,13}
async function handler(request) {
  try {
    let didError = false;
    const stream = await renderToReadableStream(<App />, {
      bootstrapScripts: ['/main.js'],
      onError(error) {
        didError = true;
        console.error(error);
        logServerCrashReport(error);
      }
    });
    return new Response(stream, {
      status: didError ? 500 : 200,
      headers: { 'content-type': 'text/html' },
    });
  } catch (error) {
    return new Response('<h1>Something went wrong</h1>', {
      status: 500,
      headers: { 'content-type': 'text/html' },
    });
  }
}
```

এটি শুধুমাত্র প্রাথমিক শেল কনটেন্ট তৈরি হওয়ার সময় শেলের বাইরে ঘটে যাওয়া এররগুলোই ধরবে, তাই এটি পূর্ণাঙ্গ (exhaustive) নয়। যদি কোনো নির্দিষ্ট কনটেন্টে এরর হয়েছে কি না জানা অত্যন্ত গুরুত্বপূর্ণ হয়, তাহলে আপনি সেই কনটেন্টকে শেলের ভেতরে নিয়ে আসতে পারেন।

---

### Handling different errors in different ways {/*handling-different-errors-in-different-ways*/}

You can [create your own `Error` subclasses](https://javascript.info/custom-errors) and use the [`instanceof`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof) operator to check which error is thrown. For example, you can define a custom `NotFoundError` and throw it from your component. Then you can save the error in `onError` and do something different before returning the response depending on the error type:

```js {2-3,5-15,22,28,33}
async function handler(request) {
  let didError = false;
  let caughtError = null;

  function getStatusCode() {
    if (didError) {
      if (caughtError instanceof NotFoundError) {
        return 404;
      } else {
        return 500;
      }
    } else {
      return 200;
    }
  }

  try {
    const stream = await renderToReadableStream(<App />, {
      bootstrapScripts: ['/main.js'],
      onError(error) {
        didError = true;
        caughtError = error;
        console.error(error);
        logServerCrashReport(error);
      }
    });
    return new Response(stream, {
      status: getStatusCode(),
      headers: { 'content-type': 'text/html' },
    });
  } catch (error) {
    return new Response('<h1>Something went wrong</h1>', {
      status: getStatusCode(),
      headers: { 'content-type': 'text/html' },
    });
  }
}
```

Keep in mind that once you emit the shell and start streaming, you can't change the status code.

---

### Waiting for all content to load for crawlers and static generation {/*waiting-for-all-content-to-load-for-crawlers-and-static-generation*/}

Streaming offers a better user experience because the user can see the content as it becomes available.

However, when a crawler visits your page, or if you're generating the pages at the build time, you might want to let all of the content load first and then produce the final HTML output instead of revealing it progressively.

You can wait for all the content to load by awaiting the `stream.allReady` Promise:

```js {12-15}
async function handler(request) {
  try {
    let didError = false;
    const stream = await renderToReadableStream(<App />, {
      bootstrapScripts: ['/main.js'],
      onError(error) {
        didError = true;
        console.error(error);
        logServerCrashReport(error);
      }
    });
    let isCrawler = // ... depends on your bot detection strategy ...
    if (isCrawler) {
      await stream.allReady;
    }
    return new Response(stream, {
      status: didError ? 500 : 200,
      headers: { 'content-type': 'text/html' },
    });
  } catch (error) {
    return new Response('<h1>Something went wrong</h1>', {
      status: 500,
      headers: { 'content-type': 'text/html' },
    });
  }
}
```

A regular visitor will get a stream of progressively loaded content. A crawler will receive the final HTML output after all the data loads. However, this also means that the crawler will have to wait for *all* data, some of which might be slow to load or error. Depending on your app, you could choose to send the shell to the crawlers too.

---

### Aborting server rendering {/*aborting-server-rendering*/}

You can force the server rendering to "give up" after a timeout:

```js {3,4-6,9}
async function handler(request) {
  try {
    const controller = new AbortController();
    setTimeout(() => {
      controller.abort();
    }, 10000);

    const stream = await renderToReadableStream(<App />, {
      signal: controller.signal,
      bootstrapScripts: ['/main.js'],
      onError(error) {
        didError = true;
        console.error(error);
        logServerCrashReport(error);
      }
    });
    // ...
```

React will flush the remaining loading fallbacks as HTML, and will attempt to render the rest on the client.
