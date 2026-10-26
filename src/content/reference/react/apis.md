---
title: "বিল্ট-ইন React API"
---

<Intro>

[হুক](/reference/react/hooks) এবং [কম্পোনেন্ট](/reference/react/components) এর সাথে সাথে, `react` প্যাকেজ আরো কিছু API এক্সপোর্ট করে যা কম্পোনেন্ট ডিফাইন করতে কাজে লাগে। এই পেইজে বাকি সকল আধুনিক React API এর তালিকা আছে।

</Intro>

---

* [`createContext`](/reference/react/createContext) আপনাকে চাইল্ড কম্পোনেন্টে কনটেক্সট ডিফাইন করে যুক্ত করতে সাহায্য করে। এটা [`useContext`.](/reference/react/useContext) এর সাথে ব্যবহৃত হয়।
* [`lazy`](/reference/react/lazy) আপনাকে কোন একটা কম্পোনেন্টের কোডের লোডিং এর প্রথম বার রেন্ডার হবার সময় পর্যন্ত আটকে রাখার সুযোগ দেয়।
* [`memo`](/reference/react/memo) আপনার কম্পোনেন্টকে একই প্রপ নিয়ে বার বার রেন্ডার হওয়া এড়ানোর ক্ষেত্রে সাহায্য করে। এটা [`useMemo`](/reference/react/useMemo) এবং [`useCallback`](/reference/react/useCallback) এর সাথে ব্যবহৃত হয়।
* [`startTransition`](/reference/react/startTransition) আপনাকে একটা state এর আপডেট non-urgent হিসেবে চিহ্নিত করার সুযোগ দেয়। এটা [`useTransition`](/reference/react/useTransition) এর মতোই।
* [`addTransitionType`](/reference/react/addTransitionType) দিয়ে একটি Transition-এর কারণ নির্দিষ্ট করতে পারেন। এটি [`startTransition`](/reference/react/startTransition) এবং [`<ViewTransition>`](/reference/react/ViewTransition)-এর সাথে ব্যবহার করা হয়।
* [`act`](/reference/react/act) আপনাকে টেস্টে renders এবং interactions wrap করার সুযোগ দেয় যাতে assertions করার আগে আপডেটগুলো প্রসেস হয়ে যায়।
* [`cache`](/reference/react/cache) দিয়ে ডেটা fetch বা কোনো গণনার ফলাফল cache করতে পারেন।
* [`cacheSignal`](/reference/react/cacheSignal) দিয়ে জানতে পারেন কখন `cache()`-এর জীবনকাল শেষ হয়েছে।
* [`captureOwnerStack`](/reference/react/captureOwnerStack) development-এর সময় বর্তমান Owner Stack পড়ে এবং পাওয়া গেলে string হিসেবে রিটার্ন করে।

---

## Resource APIs {/*resource-apis*/}

*Resources* কম্পোনেন্ট দ্বারা অ্যাক্সেস করা যায় সেগুলোকে তাদের state এর অংশ হিসেবে না রেখেই। উদাহরণস্বরূপ, একটি কম্পোনেন্ট একটি Promise থেকে একটি মেসেজ পড়তে পারে বা একটি context থেকে styling তথ্য পড়তে পারে।

আপনি [`use`](/reference/react/use)-এ এই ধরনের resource পাস করতে পারেন:

* একটি [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise), তার resolved মান পড়ার জন্য।
* একটি [context](/learn/passing-data-deeply-with-context), তার মান পড়ার জন্য।
* [`browser`](/reference/react-dom/browser) থেকে রিটার্ন হওয়া মান, সার্ভার রেন্ডারিংয়ের সময় কম্পোনেন্টটিকে শুধু ব্রাউজারে রেন্ডার করার জন্য চিহ্নিত করতে।
```js
function MessageComponent({ messagePromise }) {
  const message = use(messagePromise);
  const theme = use(ThemeContext);
  use(browser());
  // ...
}
```