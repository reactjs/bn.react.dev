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

এই API গুলো আপনার অ্যাপকে দ্রুততর করতে ব্যবহার করা যেতে পারে resource গুলো যেমন scripts, stylesheets, এবং fonts আগে থেকেই লোড করে রেখে যেগুলো আপনার প্রয়োজন হবে বলে জানেন, উদাহরণস্বরূপ অন্য পেজে নেভিগেট করার আগে যেখানে এই resource গুলো ব্যবহৃত হবে।

[React-based frameworks](/learn/start-a-new-react-project) প্রায়ই আপনার জন্য resource loading হ্যান্ডেল করে, তাই হয়ত আপনাকে এই API গুলো নিজে কল করতে হবে না। বিস্তারিত জানতে আপনার ফ্রেমওয়ার্কের ডকুমেন্টেশন দেখুন।

* [`prefetchDNS`](/reference/react-dom/prefetchDNS) আপনাকে একটি DNS ডোমেইন নামের IP ঠিকানা আগে থেকেই fetch করতে দেয় যেটার সাথে আপনি সংযোগ করার আশা করেন।
* [`preconnect`](/reference/react-dom/preconnect) আপনাকে এমন একটি সার্ভারের সাথে সংযোগ করতে দেয় যেখান থেকে আপনি resource রিকোয়েস্ট করার আশা করেন, এমনকি যদি আপনি এখনো জানেন না যে কোন resource গুলো লাগবে।
* [`preload`](/reference/react-dom/preload) আপনাকে stylesheet, font, image, বা external script fetch করতে দেয় যেগুলো আপনি ব্যবহার করার আশা করেন।
* [`preloadModule`](/reference/react-dom/preloadModule) আপনাকে একটি ESM module fetch করতে দেয় যেটা আপনি ব্যবহার করার আশা করেন।
* [`preinit`](/reference/react-dom/preinit) আপনাকে একটি external script fetch এবং evaluate করতে বা stylesheet fetch এবং insert করতে দেয়।
* [`preinitModule`](/reference/react-dom/preinitModule) আপনাকে একটি ESM module fetch এবং evaluate করতে দেয়।

---

## Entry points {/*entry-points*/}

`react-dom` প্যাকেজ দুটি অতিরিক্ত entry point দেয়ঃ

* [`react-dom/client`](/reference/react-dom/client) এর মধ্যে রয়েছে সেই API গুলো যেগুলো ক্লায়েন্টে (ব্রাউজারে) React কম্পোনেন্ট রেন্ডার করে।
* [`react-dom/server`](/reference/react-dom/server) এর মধ্যে রয়েছে সেই API গুলো যেগুলো সার্ভারে React কম্পোনেন্ট রেন্ডার করে।

---

## Removed APIs {/*removed-apis*/}

এই API গুলো React 19 এ সরিয়ে ফেলা হয়েছে:

* [`findDOMNode`](https://18.react.dev/reference/react-dom/findDOMNode): [বিকল্প সমাধান](https://18.react.dev/reference/react-dom/findDOMNode#alternatives) দেখুন।
* [`hydrate`](https://18.react.dev/reference/react-dom/hydrate): এর পরিবর্তে [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) ব্যবহার করুন।
* [`render`](https://18.react.dev/reference/react-dom/render): এর পরিবর্তে [`createRoot`](/reference/react-dom/client/createRoot) ব্যবহার করুন।
* [`unmountComponentAtNode`](/reference/react-dom/unmountComponentAtNode): এর পরিবর্তে [`root.unmount()`](/reference/react-dom/client/createRoot#root-unmount) ব্যবহার করুন।
* [`renderToNodeStream`](https://18.react.dev/reference/react-dom/server/renderToNodeStream): এর পরিবর্তে [`react-dom/server`](/reference/react-dom/server) API গুলো ব্যবহার করুন।
* [`renderToStaticNodeStream`](https://18.react.dev/reference/react-dom/server/renderToStaticNodeStream): এর পরিবর্তে [`react-dom/server`](/reference/react-dom/server) API গুলো ব্যবহার করুন।
