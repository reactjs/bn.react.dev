---
title: ক্লায়েন্ট React DOM API
---

<Intro>

`react-dom/client` API গুলো ক্লায়েন্টে (ব্রাউজারের মধ্যে) React কম্পোনেন্ট রেন্ডার করতে দেয়। এই API গুলো সাধারণত আপনার অ্যাপের একদম উপরের স্তরে React ট্রি ইনিশিয়ালাইজ করার কাজে ব্যবহৃত হয়। আপনার জন্য কল দিয়ে দিতে পারে একটি [framework](/learn/start-a-new-react-project#production-grade-react-frameworks)। আপনার বেশিরভাগ কম্পোনেন্টের সেগুলো ইমপোর্ট বা ব্যবহারের প্রয়োজন পড়বে না। 

</Intro>

---

## ক্লায়েন্ট API {/*client-apis*/}

* একটি ব্রাউজার DOM নোডে React কম্পোনেন্ট দেখানোর জন্য [`createRoot`](/reference/react-dom/client/createRoot) আপনাকে একটি root তৈরী করতে দেবে।
* [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) আপনাকে একটি ব্রাউজার DOM নোডে সেই React কম্পোনেন্ট গুলো দেখানোর সুযোগ দেবে যার HTML কনটেন্ট তৈরী করেছিল [`react-dom/server`.](/reference/react-dom/server)

---

## ব্রাউজার সাপোর্ট {/*browser-support*/}

React ইন্টারনেট এক্সপ্লোরার ৯ এবং এর পরের সকল ভার্সন সহ সকল জনপ্রিয় ব্রাউজার সাপোর্ট করে। IE 9 এবং IE 10 এর মত পুরোনো ব্রাউজারের সাপোর্টের জন্য কিছু polyfill এর দরকার হয়।