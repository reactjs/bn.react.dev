---
title: "বিল্ট-ইন React API"
---

<Intro>

[হুক](/reference/react) এবং [কম্পোনেন্ট](/reference/react/components) এর সাথে সাথে, `react` প্যাকেজ আরো কিছু API এক্সপোর্ট করে যা কম্পোনেন্ট ডিফাইন করতে কাজে লাগে। এই পেইজে বাকি সকল আধুনিক React API এর তালিকা আছে।

</Intro>

---

* [`createContext`](/reference/react/createContext) আপনাকে চাইল্ড কম্পোনেন্টে কনটেক্সট ডিফাইন করে যুক্ত করতে সাহায্য করে। এটা [`useContext`.](/reference/react/useContext) এর সাথে ব্যবহৃত হয়।
* [`lazy`](/reference/react/lazy) আপনাকে কোন একটা কম্পোনেন্টের কোডের লোডিং এর প্রথম বার রেন্ডার হবার সময় পর্যন্ত আটকে রাখার সুযোগ দেয়।
* [`memo`](/reference/react/memo) আপনার কম্পোনেন্টকে একই প্রপ নিয়ে বার বার রেন্ডার হওয়া এড়ানোর ক্ষেত্রে সাহায্য করে। এটা [`useMemo`](/reference/react/useMemo) এবং [`useCallback`](/reference/react/useCallback) এর সাথে ব্যবহৃত হয়।
* [`startTransition`](/reference/react/startTransition) আপনাকে একটা state এর আপডেট non-urgent হিসেবে চিহ্নিত করার সুযোগ দেয়। এটা [`useTransition`](/reference/react/useTransition) এর মতোই।
* [`act`](/reference/react/act) আপনাকে টেস্টে renders এবং interactions wrap করার সুযোগ দেয় যাতে assertions করার আগে আপডেটগুলো প্রসেস হয়ে যায়।

---

## Resource APIs {/*resource-apis*/}

*Resources* কম্পোনেন্ট দ্বারা অ্যাক্সেস করা যায় সেগুলোকে তাদের state এর অংশ হিসেবে না রেখেই। উদাহরণস্বরূপ, একটি কম্পোনেন্ট একটি Promise থেকে একটি মেসেজ পড়তে পারে বা একটি context থেকে styling তথ্য পড়তে পারে।

একটি resource থেকে মান পড়তে, এই API ব্যবহার করুনঃ

* [`use`](/reference/react/use) আপনাকে একটি resource এর মান পড়ার সুযোগ দেয় যেমন একটি [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) বা [context](/learn/passing-data-deeply-with-context)।
```js
function MessageComponent({ messagePromise }) {
  const message = use(messagePromise);
  const theme = use(ThemeContext);
  // ...
}
```