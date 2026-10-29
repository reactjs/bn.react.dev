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

### Streaming more content as it loads {/*streaming-more-content-as-it-loads*/}

Streaming allows the user to start seeing the content even before all the data has loaded on the server. For example, consider a profile page that shows a cover, a sidebar with friends and photos, and a list of posts:

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

Imagine that loading data for `<Posts />` takes some time. Ideally, you'd want to show the rest of the profile page content to the user without waiting for the posts. To do this, [wrap `Posts` in a `<Suspense>` boundary:](/reference/react/Suspense#displaying-a-fallback-while-content-is-loading)

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

This tells React to start streaming the HTML before `Posts` loads its data. React will send the HTML for the loading fallback (`PostsGlimmer`) first, and then, when `Posts` finishes loading its data, React will send the remaining HTML along with an inline `<script>` tag that replaces the loading fallback with that HTML. From the user's perspective, the page will first appear with the `PostsGlimmer`, later replaced by the `Posts`.

You can further [nest `<Suspense>` boundaries](/reference/react/Suspense#revealing-nested-content-as-it-loads) to create a more granular loading sequence:

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


In this example, React can start streaming the page even earlier. Only `ProfileLayout` and `ProfileCover` must finish rendering first because they are not wrapped in any `<Suspense>` boundary. However, if `Sidebar`, `Friends`, or `Photos` need to load some data, React will send the HTML for the `BigSpinner` fallback instead. Then, as more data becomes available, more content will continue to be revealed until all of it becomes visible.

Streaming does not need to wait for React itself to load in the browser, or for your app to become interactive. The HTML content from the server will get progressively revealed before any of the `<script>` tags load.

[Read more about how streaming HTML works.](https://github.com/reactwg/react-18/discussions/37)

<Note>

Only data read from a source that [activates a Suspense boundary](/reference/react/Suspense#what-activates-a-suspense-boundary), such as a Promise read with [`use`](/reference/react/use), will suspend during rendering. Suspense does not detect data fetched inside an Effect or event handler.

</Note>

---

### Specifying what goes into the shell {/*specifying-what-goes-into-the-shell*/}

The part of your app outside of any `<Suspense>` boundaries is called *the shell:*

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

It determines the earliest loading state that the user may see:

```js {3-5,13
<ProfileLayout>
  <ProfileCover />
  <BigSpinner />
</ProfileLayout>
```

If you wrap the whole app into a `<Suspense>` boundary at the root, the shell will only contain that spinner. However, that's not a pleasant user experience because seeing a big spinner on the screen can feel slower and more annoying than waiting a bit more and seeing the real layout. This is why usually you'll want to place the `<Suspense>` boundaries so that the shell feels *minimal but complete*--like a skeleton of the entire page layout.

The async call to `renderToReadableStream` will resolve to a `stream` as soon as the entire shell has been rendered. Usually, you'll start streaming then by creating and returning a response with that `stream`:

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

By the time the `stream` is returned, components in nested `<Suspense>` boundaries might still be loading data.

---

### Logging crashes on the server {/*logging-crashes-on-the-server*/}

By default, all errors on the server are logged to console. You can override this behavior to log crash reports:

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

If you provide a custom `onError` implementation, don't forget to also log errors to the console like above.

---

### Recovering from errors inside the shell {/*recovering-from-errors-inside-the-shell*/}

In this example, the shell contains `ProfileLayout`, `ProfileCover`, and `PostsGlimmer`:

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

If an error occurs while rendering those components, React won't have any meaningful HTML to send to the client. Wrap your `renderToReadableStream` call in a `try...catch` to send a fallback HTML that doesn't rely on server rendering as the last resort:

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

If there is an error while generating the shell, both `onError` and your `catch` block will fire. Use `onError` for error reporting and use the `catch` block to send the fallback HTML document. Your fallback HTML does not have to be an error page. Instead, you may include an alternative shell that renders your app on the client only.

---

### Recovering from errors outside the shell {/*recovering-from-errors-outside-the-shell*/}

In this example, the `<Posts />` component is wrapped in `<Suspense>` so it is *not* a part of the shell:

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

If an error happens in the `Posts` component or somewhere inside it, React will [try to recover from it:](/reference/react/Suspense#providing-a-fallback-for-server-errors-and-server-only-content)

1. It will emit the loading fallback for the closest `<Suspense>` boundary (`PostsGlimmer`) into the HTML.
2. It will "give up" on trying to render the `Posts` content on the server anymore.
3. When the JavaScript code loads on the client, React will *retry* rendering `Posts` on the client.

If retrying rendering `Posts` on the client *also* fails, React will throw the error on the client. As with all the errors thrown during rendering, the [closest parent error boundary](/reference/react/Component#static-getderivedstatefromerror) determines how to present the error to the user. In practice, this means that the user will see a loading indicator until it is certain that the error is not recoverable.

If retrying rendering `Posts` on the client succeeds, the loading fallback from the server will be replaced with the client rendering output. The user will not know that there was a server error. However, the server `onError` callback and the client [`onRecoverableError`](/reference/react-dom/client/hydrateRoot#hydrateroot) callbacks will fire so that you can get notified about the error.

---

### Setting the status code {/*setting-the-status-code*/}

Streaming introduces a tradeoff. You want to start streaming the page as early as possible so that the user can see the content sooner. However, once you start streaming, you can no longer set the response status code.

By [dividing your app](#specifying-what-goes-into-the-shell) into the shell (above all `<Suspense>` boundaries) and the rest of the content, you've already solved a part of this problem. If the shell errors, your `catch` block will run which lets you set the error status code. Otherwise, you know that the app may recover on the client, so you can send "OK".

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

If a component *outside* the shell (i.e. inside a `<Suspense>` boundary) throws an error, React will not stop rendering. This means that the `onError` callback will fire, but your code will continue running without getting into the `catch` block. This is because React will try to recover from that error on the client, [as described above.](#recovering-from-errors-outside-the-shell)

However, if you'd like, you can use the fact that something has errored to set the status code:

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

This will only catch errors outside the shell that happened while generating the initial shell content, so it's not exhaustive. If knowing whether an error occurred for some content is critical, you can move it up into the shell.

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
