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

---

## Entry points {/*entry-points*/}

`react-dom` প্যাকেজ দুটি অতিরিক্ত entry point দেয়ঃ

* [`react-dom/client`](/reference/react-dom/client) এর মধ্যে রয়েছে সেই API গুলো যেগুলো ক্লায়েন্টে (ব্রাউজারে) React কম্পোনেন্ট রেন্ডার করে।
* [`react-dom/server`](/reference/react-dom/server) এর মধ্যে রয়েছে সেই API গুলো যেগুলো সার্ভারে React কম্পোনেন্ট রেন্ডার করে।

---

## Deprecated APIs {/*deprecated-apis*/}

<Deprecated>

এই API গুলো React এর সামনের কোন বড় ভার্সনে ফেলে দেওয়া হবে।

</Deprecated>

* [`findDOMNode`](/reference/react-dom/findDOMNode) সবচেয়ে নিকটবর্তী সেই DOM নোডকে খুঁজে বের করে একটি ক্লাস কম্পোনেন্ট ইন্সট্যান্সের সাথে correspond করে।
* [`hydrate`](/reference/react-dom/hydrate) সার্ভার HTML থেকে তৈরি করা DOM এর মধ্যে একটি ট্রি মাউন্ট করে।  [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) এর কারণে deprecated।
* [`render`](/reference/react-dom/render) DOM এর মধ্যে একটি ট্রি মাউন্ট করে। [`createRoot`](/reference/react-dom/client/createRoot) এর কারণে deprecated।
* [`unmountComponentAtNode`](/reference/react-dom/unmountComponentAtNode) DOM থেকে ট্রি আনমাউন্ট করে। [`root.unmount()`](/reference/react-dom/client/createRoot#root-unmount) এর কারণে deprecated।

