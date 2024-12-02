---
title: "বিল্ট-ইন React API"
---

<Intro>

[হুক](/reference/react) এবং [কম্পোনেন্ট](/reference/react/components) এর সাথে সাথে, `react` প্যাকেজ আরো কিছু API এক্সপোর্ট করে যা কম্পোনেন্ট ডিফাইন করতে কাজে লাগে। এই পেইজে বাকি সকল আধুনিক React API এর তালিকা আছে।

</Intro>

---

<<<<<<< HEAD
* [`createContext`](/reference/react/createContext) আপনাকে চাইল্ড কম্পোনেন্টে কনটেক্সট ডিফাইন করে যুক্ত করতে সাহায্য করে। এটা [`useContext`.](/reference/react/useContext) এর সাথে ব্যবহৃত হয়।
* [`forwardRef`](/reference/react/forwardRef) আপনার কম্পোনেন্টকে প্যারেন্ট এর রেফ হিসেবে একটা DOM নোড উন্মুক্ত করার সুযোগ দেয়। এটা ব্যবহৃত হয় [`useRef`](/reference/react/useRef) এর সাথে।
* [`lazy`](/reference/react/lazy) আপনাকে কোন একটা কম্পোনেন্টের কোডের লোডিং এর প্রথম বার রেন্ডার হবার সময় পর্যন্ত আটকে রাখার সুযোগ দেয়।
* [`memo`](/reference/react/memo) আপনার কম্পোনেন্টকে একই প্রপ নিয়ে বার বার রেন্ডার হওয়া এড়ানোর ক্ষেত্রে সাহায্য করে। এটা [`useMemo`](/reference/react/useMemo) এবং [`useCallback`](/reference/react/useCallback) এর সাথে ব্যবহৃত হয়।
* [`startTransition`](/reference/react/startTransition) আপনাকে একটা state এর আপডেট non-urgent হিসেবে চিহ্নিত করার সুযোগ দেয়। এটা [`useTransition`](/reference/react/useTransition) এর মতোই।
=======
* [`createContext`](/reference/react/createContext) lets you define and provide context to the child components. Used with [`useContext`.](/reference/react/useContext)
* [`forwardRef`](/reference/react/forwardRef) lets your component expose a DOM node as a ref to the parent. Used with [`useRef`.](/reference/react/useRef)
* [`lazy`](/reference/react/lazy) lets you defer loading a component's code until it's rendered for the first time.
* [`memo`](/reference/react/memo) lets your component skip re-renders with same props. Used with [`useMemo`](/reference/react/useMemo) and [`useCallback`.](/reference/react/useCallback)
* [`startTransition`](/reference/react/startTransition) lets you mark a state update as non-urgent. Similar to [`useTransition`.](/reference/react/useTransition)
* [`act`](/reference/react/act) lets you wrap renders and interactions in tests to ensure updates have processed before making assertions.

---

## Resource APIs {/*resource-apis*/}

*Resources* can be accessed by a component without having them as part of their state. For example, a component can read a message from a Promise or read styling information from a context.

To read a value from a resource, use this API:

* [`use`](/reference/react/use) lets you read the value of a resource like a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) or [context](/learn/passing-data-deeply-with-context).
```js
function MessageComponent({ messagePromise }) {
  const message = use(messagePromise);
  const theme = use(ThemeContext);
  // ...
}
```
>>>>>>> 84f29eb20af17e9c154b9ad71c21af4c9171e4a2
