---
title: "বিল্ট-ইন React API"
---

<Intro>

[হুক](/reference/react/hooks) এবং [কম্পোনেন্ট](/reference/react/components) এর সাথে সাথে, `react` প্যাকেজ আরো কিছু API এক্সপোর্ট করে যা কম্পোনেন্ট ডিফাইন করতে কাজে লাগে। এই পেইজে বাকি সকল আধুনিক React API এর তালিকা আছে।

</Intro>

---

<<<<<<< HEAD
* [`createContext`](/reference/react/createContext) আপনাকে চাইল্ড কম্পোনেন্টে কনটেক্সট ডিফাইন করে যুক্ত করতে সাহায্য করে। এটা [`useContext`.](/reference/react/useContext) এর সাথে ব্যবহৃত হয়।
* [`lazy`](/reference/react/lazy) আপনাকে কোন একটা কম্পোনেন্টের কোডের লোডিং এর প্রথম বার রেন্ডার হবার সময় পর্যন্ত আটকে রাখার সুযোগ দেয়।
* [`memo`](/reference/react/memo) আপনার কম্পোনেন্টকে একই প্রপ নিয়ে বার বার রেন্ডার হওয়া এড়ানোর ক্ষেত্রে সাহায্য করে। এটা [`useMemo`](/reference/react/useMemo) এবং [`useCallback`](/reference/react/useCallback) এর সাথে ব্যবহৃত হয়।
* [`startTransition`](/reference/react/startTransition) আপনাকে একটা state এর আপডেট non-urgent হিসেবে চিহ্নিত করার সুযোগ দেয়। এটা [`useTransition`](/reference/react/useTransition) এর মতোই।
* [`act`](/reference/react/act) আপনাকে টেস্টে renders এবং interactions wrap করার সুযোগ দেয় যাতে assertions করার আগে আপডেটগুলো প্রসেস হয়ে যায়।
=======
* [`createContext`](/reference/react/createContext) lets you define and provide context to the child components. Used with [`useContext`.](/reference/react/useContext)
* [`lazy`](/reference/react/lazy) lets you defer loading a component's code until it's rendered for the first time.
* [`memo`](/reference/react/memo) lets your component skip re-renders with same props. Used with [`useMemo`](/reference/react/useMemo) and [`useCallback`.](/reference/react/useCallback)
* [`startTransition`](/reference/react/startTransition) lets you mark a state update as non-urgent. Similar to [`useTransition`.](/reference/react/useTransition)
* [`act`](/reference/react/act) lets you wrap renders and interactions in tests to ensure updates have processed before making assertions.
* [`cache`](/reference/react/cache) lets you cache the result of a data fetch or computation.
* [`cacheSignal`](/reference/react/cacheSignal) lets you know when the `cache()` lifetime is over.
* [`captureOwnerStack`](/reference/react/captureOwnerStack) reads the current Owner Stack in development and returns it as a string if available.
>>>>>>> 7c36f7ac329fe3cf2e11222edce9a535158c2cab

---

## Resource APIs {/*resource-apis*/}

*Resources* কম্পোনেন্ট দ্বারা অ্যাক্সেস করা যায় সেগুলোকে তাদের state এর অংশ হিসেবে না রেখেই। উদাহরণস্বরূপ, একটি কম্পোনেন্ট একটি Promise থেকে একটি মেসেজ পড়তে পারে বা একটি context থেকে styling তথ্য পড়তে পারে।

<<<<<<< HEAD
একটি resource থেকে মান পড়তে, এই API ব্যবহার করুনঃ

* [`use`](/reference/react/use) আপনাকে একটি resource এর মান পড়ার সুযোগ দেয় যেমন একটি [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) বা [context](/learn/passing-data-deeply-with-context)।
=======
You can pass these types of resources to [`use`](/reference/react/use):

* A [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) to read its resolved value.
* A [context](/learn/passing-data-deeply-with-context) to read its value.
* <CanaryBadge /> The value returned by [`browser`](/reference/react-dom/browser) to mark a component as browser-only during server rendering.

>>>>>>> 7c36f7ac329fe3cf2e11222edce9a535158c2cab
```js
function MessageComponent({ messagePromise }) {
  const message = use(messagePromise);
  const theme = use(ThemeContext);
  use(browser());
  // ...
}
```