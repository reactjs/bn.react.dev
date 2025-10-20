---
title: Server React DOM API
---

<Intro>

`react-dom/server` API গুলো আপনাকে সার্ভারে React কম্পোনেন্টকে HTML এ রেন্ডার করার সুযোগ দেয়। প্রাথমিক HTML তৈরীর জন্য এই API গুলো কেবল মাত্র সার্ভারে আপনার অ্যাপের একদম উপরের লেভেলে ব্যবহৃত হয়। আপনার হয়ে একটা [ফ্রেমওয়ার্ক](/learn/start-a-new-react-project#full-stack-frameworks) এই API গুলোকে কল দিতে পারে। আপনার বেশিরভাগ কম্পোনেন্টের এগুলোকে ব্যবহারের দরকার পড়বে না।

</Intro>

---

<<<<<<< HEAD
## Node.js Stream জন্য Server API {/*server-apis-for-nodejs-streams*/}

[Node.js Streams](https://nodejs.org/api/stream.html) সহ এনভায়রনমেন্ট গুলোতে কেবল এই মেথডগুলো পাওয়া যাবেঃ 

* [`renderToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) একটা React ট্রি কে একটা pipeable [Node.js Stream](https://nodejs.org/api/stream.html) এ রেন্ডার করে।

---

## Web Stream এর জন্য Server API {/*server-apis-for-web-streams*/}
=======
## Server APIs for Web Streams {/*server-apis-for-web-streams*/}
>>>>>>> f8c81a0f4f8e454c850f0c854ad054b32313345c

[Web Stream](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) সহ এনভায়রনমেন্ট গুলোতে (যার মধ্যে রয়েছে ব্রাউজার, Deno এবং কিছু আধুনিক Edge runtime) কেবল এই মেথডগুলো পাওয়া যাবেঃ

<<<<<<< HEAD
* [`renderToReadableStream`](/reference/react-dom/server/renderToReadableStream) একটা React ট্রিকে একটা [Readable Web Stream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream) এ রেন্ডার করে।
=======
* [`renderToReadableStream`](/reference/react-dom/server/renderToReadableStream) renders a React tree to a [Readable Web Stream.](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream)
* [`resume`](/reference/react-dom/server/renderToPipeableStream) resumes [`prerender`](/reference/react-dom/static/prerender) to a [Readable Web Stream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream).


<Note>

Node.js also includes these methods for compatibility, but they are not recommended due to worse performance. Use the [dedicated Node.js APIs](#server-apis-for-nodejs-streams) instead.

</Note>
---

## Server APIs for Node.js Streams {/*server-apis-for-nodejs-streams*/}

These methods are only available in the environments with [Node.js Streams:](https://nodejs.org/api/stream.html)

* [`renderToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) renders a React tree to a pipeable [Node.js Stream.](https://nodejs.org/api/stream.html)
* [`resumeToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) resumes [`prerenderToNodeStream`](/reference/react-dom/static/prerenderToNodeStream) to a pipeable [Node.js Stream.](https://nodejs.org/api/stream.html)
>>>>>>> f8c81a0f4f8e454c850f0c854ad054b32313345c

---

## Legacy Server APIs for non-streaming environments {/*legacy-server-apis-for-non-streaming-environments*/}

এই মেথডগুলো সেই সব এনভায়রনমেন্টে ব্যবহার করা যাবে যেগুলো stream সাপোর্ট করে নাঃ

* [`renderToString`](/reference/react-dom/server/renderToString) একটা React ট্রি কে একটা স্ট্রিং এ রেন্ডার করে।
* [`renderToStaticMarkup`](/reference/react-dom/server/renderToStaticMarkup) একটা নন-ইন্টার‍্যাকটিভ React ট্রি কে একটা স্ট্রিং এ রেন্ডার করে।

Streaming API গুলোর তুলনায় এদের ফাংশনালিটি সীমাবদ্ধ।
