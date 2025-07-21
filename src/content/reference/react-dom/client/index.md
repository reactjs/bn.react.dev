---
title: ক্লায়েন্ট React DOM API
---

<Intro>

<<<<<<< HEAD
`react-dom/client` API গুলো ক্লায়েন্টে (ব্রাউজারের মধ্যে) React কম্পোনেন্ট রেন্ডার করতে দেয়। এই API গুলো সাধারণত আপনার অ্যাপের একদম উপরের স্তরে React ট্রি ইনিশিয়ালাইজ করার কাজে ব্যবহৃত হয়। আপনার জন্য কল দিয়ে দিতে পারে একটি [framework](/learn/start-a-new-react-project#production-grade-react-frameworks)। আপনার বেশিরভাগ কম্পোনেন্টের সেগুলো ইমপোর্ট বা ব্যবহারের প্রয়োজন পড়বে না। 
=======
The `react-dom/client` APIs let you render React components on the client (in the browser). These APIs are typically used at the top level of your app to initialize your React tree. A [framework](/learn/creating-a-react-app#full-stack-frameworks) may call them for you. Most of your components don't need to import or use them.
>>>>>>> d52b3ec734077fd56f012fc2b30a67928d14cc73

</Intro>

---

## ক্লায়েন্ট API {/*client-apis*/}

* একটি ব্রাউজার DOM নোডে React কম্পোনেন্ট দেখানোর জন্য [`createRoot`](/reference/react-dom/client/createRoot) আপনাকে একটি root তৈরী করতে দেবে।
* [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) আপনাকে একটি ব্রাউজার DOM নোডে সেই React কম্পোনেন্ট গুলো দেখানোর সুযোগ দেবে যার HTML কনটেন্ট তৈরী করেছিল [`react-dom/server`.](/reference/react-dom/server)

---

## ব্রাউজার সাপোর্ট {/*browser-support*/}

React ইন্টারনেট এক্সপ্লোরার ৯ এবং এর পরের সকল ভার্সন সহ সকল জনপ্রিয় ব্রাউজার সাপোর্ট করে। IE 9 এবং IE 10 এর মত পুরোনো ব্রাউজারের সাপোর্টের জন্য কিছু polyfill এর দরকার হয়।