---
title: React DOM APIs
---

<Intro>

The `react-dom` প্যাকেজে যেসব মেথড রয়েছে সেগুলো কেবল ওয়েব অ্যাপ্লিকেশন (যেগুলো ব্রাউজারের DOM এনভায়রনমেন্টে চলে) সাপোর্ট করে। এগুলো React native সাপোর্ট করে না।

</Intro>

---

## APIs {/*apis*/}

এই API গুলো আপনার কম্পোনেন্ট থেকে ইমপোর্ট করা যেতে পারে। এগুলো ব্যবহার হয় না বললেই চলেঃ

* [`createPortal`](/reference/react-dom/createPortal) আপনাকে DOM ট্রি এর অন্য একটি অংশে চাইল্ড কম্পোনেন্ট রেন্ডার করতে দেয়।
* [`flushSync`](/reference/react-dom/flushSync) আপনাকে সুযোগ দেয় React কে একটি state আপডেট flush করতে বাধ্য করতে এবং একি সাথে DOM আপডেট করতে।

## Resource Preloading APIs {/*resource-preloading-apis*/}

These APIs can be used to make apps faster by pre-loading resources such as scripts, stylesheets, and fonts as soon as you know you need them, for example before navigating to another page where the resources will be used.

[React-based frameworks](/learn/start-a-new-react-project) frequently handle resource loading for you, so you might not have to call these APIs yourself. Consult your framework's documentation for details.

* [`prefetchDNS`](/reference/react-dom/prefetchDNS) lets you prefetch the IP address of a DNS domain name that you expect to connect to.
* [`preconnect`](/reference/react-dom/preconnect) lets you connect to a server you expect to request resources from, even if you don't know what resources you'll need yet.
* [`preload`](/reference/react-dom/preload) lets you fetch a stylesheet, font, image, or external script that you expect to use.
* [`preloadModule`](/reference/react-dom/preloadModule) lets you fetch an ESM module that you expect to use.
* [`preinit`](/reference/react-dom/preinit) lets you fetch and evaluate an external script or fetch and insert a stylesheet.
* [`preinitModule`](/reference/react-dom/preinitModule) lets you fetch and evaluate an ESM module.

---

## Entry points {/*entry-points*/}

`react-dom` প্যাকেজ দুটি অতিরিক্ত entry point দেয়ঃ

* [`react-dom/client`](/reference/react-dom/client) এর মধ্যে রয়েছে সেই API গুলো যেগুলো ক্লায়েন্টে (ব্রাউজারে) React কম্পোনেন্ট রেন্ডার করে।
* [`react-dom/server`](/reference/react-dom/server) এর মধ্যে রয়েছে সেই API গুলো যেগুলো সার্ভারে React কম্পোনেন্ট রেন্ডার করে।

---

## Removed APIs {/*removed-apis*/}

<<<<<<< HEAD
<Deprecated>

এই API গুলো React এর সামনের কোন বড় ভার্সনে ফেলে দেওয়া হবে।

</Deprecated>

* [`findDOMNode`](/reference/react-dom/findDOMNode) সবচেয়ে নিকটবর্তী সেই DOM নোডকে খুঁজে বের করে যেটা একটি ক্লাস কম্পোনেন্ট ইন্সট্যান্সের সাথে correspond করে।
* [`hydrate`](/reference/react-dom/hydrate) সার্ভার HTML থেকে তৈরি করা DOM এর মধ্যে একটি ট্রি মাউন্ট করে।  [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) এর কারণে deprecated।
* [`render`](/reference/react-dom/render) DOM এর মধ্যে একটি ট্রি মাউন্ট করে। [`createRoot`](/reference/react-dom/client/createRoot) এর কারণে deprecated।
* [`unmountComponentAtNode`](/reference/react-dom/unmountComponentAtNode) DOM থেকে ট্রি আনমাউন্ট করে। [`root.unmount()`](/reference/react-dom/client/createRoot#root-unmount) এর কারণে deprecated।
=======
These APIs were removed in React 19:
>>>>>>> 3ee3a60a1bcc687c0b87039a3a6582e3b1d6887c

* [`findDOMNode`](https://18.react.dev/reference/react-dom/findDOMNode): see [alternatives](https://18.react.dev/reference/react-dom/findDOMNode#alternatives).
* [`hydrate`](https://18.react.dev/reference/react-dom/hydrate): use [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) instead.
* [`render`](https://18.react.dev/reference/react-dom/render): use [`createRoot`](/reference/react-dom/client/createRoot) instead.
* [`unmountComponentAtNode`](/reference/react-dom/unmountComponentAtNode): use [`root.unmount()`](/reference/react-dom/client/createRoot#root-unmount) instead.
* [`renderToNodeStream`](https://18.react.dev/reference/react-dom/server/renderToNodeStream): use [`react-dom/server`](/reference/react-dom/server) APIs instead.
* [`renderToStaticNodeStream`](https://18.react.dev/reference/react-dom/server/renderToStaticNodeStream): use [`react-dom/server`](/reference/react-dom/server) APIs instead.
