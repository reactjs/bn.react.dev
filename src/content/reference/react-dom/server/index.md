---
title: Server React DOM API
---

<Intro>

<<<<<<< HEAD
`react-dom/server` API গুলো আপনাকে সার্ভারে React কম্পোনেন্টকে HTML এ রেন্ডার করার সুযোগ দেয়। প্রাথমিক HTML তৈরীর জন্য এই API গুলো কেবল মাত্র সার্ভারে আপনার অ্যাপের একদম উপরের লেভেলে ব্যবহৃত হয়। আপনার হয়ে একটা [ফ্রেমওয়ার্ক](/learn/start-a-new-react-project#production-grade-react-frameworks) এই API গুলোকে কল দিতে পারে। আপনার বেশিরভাগ কম্পোনেন্টের এগুলোকে ব্যবহারের দরকার পড়বে না।
=======
The `react-dom/server` APIs let you server-side render React components to HTML. These APIs are only used on the server at the top level of your app to generate the initial HTML. A [framework](/learn/start-a-new-react-project#full-stack-frameworks) may call them for you. Most of your components don't need to import or use them.
>>>>>>> 27d86ffe6ec82e3642c6490d2187bae2271020a4

</Intro>

---

## Node.js Stream জন্য Server API {/*server-apis-for-nodejs-streams*/}

[Node.js Streams](https://nodejs.org/api/stream.html) সহ এনভায়রনমেন্ট গুলোতে কেবল এই মেথডগুলো পাওয়া যাবেঃ 

* [`renderToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) একটা React ট্রি কে একটা pipeable [Node.js Stream](https://nodejs.org/api/stream.html) এ রেন্ডার করে।

---

## Web Stream এর জন্য Server API {/*server-apis-for-web-streams*/}

[Web Stream](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) সহ এনভায়রনমেন্ট গুলোতে (যার মধ্যে রয়েছে ব্রাউজার, Deno এবং কিছু আধুনিক Edge runtime) কেবল এই মেথডগুলো পাওয়া যাবেঃ

* [`renderToReadableStream`](/reference/react-dom/server/renderToReadableStream) একটা React ট্রিকে একটা [Readable Web Stream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream) এ রেন্ডার করে।

---

## Legacy Server APIs for non-streaming environments {/*legacy-server-apis-for-non-streaming-environments*/}

এই মেথডগুলো সেই সব এনভায়রনমেন্টে ব্যবহার করা যাবে যেগুলো stream সাপোর্ট করে নাঃ

* [`renderToString`](/reference/react-dom/server/renderToString) একটা React ট্রি কে একটা স্ট্রিং এ রেন্ডার করে।
* [`renderToStaticMarkup`](/reference/react-dom/server/renderToStaticMarkup) একটা নন-ইন্টার‍্যাকটিভ React ট্রি কে একটা স্ট্রিং এ রেন্ডার করে।

Streaming API গুলোর তুলনায় এদের ফাংশনালিটি সীমাবদ্ধ।
