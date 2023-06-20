---
title: "বিল্ট-ইন React API"
---

<Intro>

[হুক](/reference/react) এবং [কম্পোনেন্ট](/reference/react/components) এর সাথে সাথে, `react` প্যাকেজ আরো কিছু API এক্সপোর্ট করে যা কম্পোনেন্ট ডিফাইন করতে কাজে লাগে। এই পেইজে বাকি সকল আধুনিক React API এর তালিকা আছে।

</Intro>

---

* [`createContext`](/reference/react/createContext) আপনাকে চাইল্ড কম্পোনেন্টে কনটেক্সট ডিফাইন করে যুক্ত করতে সাহায্য করে। এটা [`useContext`.](/reference/react/useContext) এর সাথে ব্যবহৃত হয়।
* [`forwardRef`](/reference/react/forwardRef) আপনার কম্পোনেন্টকে প্যারেন্ট এর রেফ হিসেবে একটা DOM নোড উন্মুক্ত করার সুযোগ দেয়। এটা ব্যবহৃত হয় [`useRef`](/reference/react/useRef) এর সাথে।
* [`lazy`](/reference/react/lazy) আপনাকে কোন একটা কম্পোনেন্টের কোডের লোডিং এর প্রথম বার রেন্ডার হবার সময় পর্যন্ত আটকে রাখার সুযোগ দেয়।
* [`memo`](/reference/react/memo) আপনার কম্পোনেন্টকে একই প্রপ নিয়ে বার বার রেন্ডার হওয়া এড়ানোর ক্ষেত্রে সাহায্য করে। এটা [`useMemo`](/reference/react/useMemo) এবং [`useCallback`](/reference/react/useCallback) এর সাথে ব্যবহৃত হয়।
* [`startTransition`](/reference/react/startTransition) আপনাকে একটা state এর আপডেট non-urgent হিসেবে চিহ্নিত করার সুযোগ দেয়। এটা [`useTransition`](/reference/react/useTransition) এর মতোই।
