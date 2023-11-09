---
title: এক নজরে React Reference
---

<Intro>
এই অংশে React নিয়ে কাজ করার জন্য বিস্তারিত রেফারেন্স ডকুমেন্টেশন রয়েছে। 
React এর সাথে পরিচিত হবার জন্য [Learn](/learn) অংশ দেখুন।
</Intro>

আমাদের 'The React reference documentation' কয়েকটি ফাংশনাল অংশে ভাগ করা হয়েছেঃ 

## React {/*react*/}
Programmatic React ফিচারঃ  
* [Hooks](/reference/react/hooks) - আপনার কম্পোনেন্ট থেকে বিভিন্ন React ফিচার ব্যবহার করুন।
* [Components](/reference/react/components) - যেসব বিল্ট-ইন কম্পোনেন্ট আপনার JSX এ ব্যবহার করতে পারেন।
* [APIs](/reference/react/apis) - কম্পোনেন্ট ডিফাইন করার জন্য কার্যকরী API।
* [Directives](/reference/react/directives) - সার্ভার কম্পোনেন্টের সাথে যেসব bundlers ব্যবহারোপযোগী তাদের কার্যপ্রণালী।

## React DOM {/*react-dom*/}
React-dom contains features that are only supported for web applications 
(which run in the browser DOM environment). This section is broken into the following:

* [Hooks](/reference/react-dom/hooks) - ওয়েব এপ্লিকেশনের জন্য hooks যা ব্রাউজারের DOM এনভায়রনমেন্টে চলে।
* [Components](/reference/react-dom/components) - React ব্রাউজারের সকল বিল্ট-ইন HTML এবং SVG কম্পোনেন্ট সাপোর্ট করে।
* [APIs](/reference/react-dom) - `react-dom` প্যাকেজে সেই সব মেথড রয়েছে যার শুধু মাত্র ওয়েব এপ্লিকেশনে সাপোর্ট আছে।
* [Client APIs](/reference/react-dom/client) - `react-dom/client` API গুলো আপনাকে ক্লায়েন্টে (ব্রাউজারে) React কম্পোনেন্ট রেন্ডার করার সুযোগ দেয়। 
* [Server APIs](/reference/react-dom/server) - `react-dom/server` API গুলো আপনাকে সার্ভারে React কম্পোনেন্ট থেকে HTML এ রেন্ডারের সুযোগ দেয়।

## Legacy APIs {/*legacy-apis*/}
* [Legacy APIs](/reference/react/legacy) - React প্যাকেজ থেকে এক্সপোর্ট করা হয়েছে এমন, কিন্তু নতুন কোডে ব্যবহারে নিরুৎসাহিত করা হবে।
