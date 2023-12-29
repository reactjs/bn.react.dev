---
title: ইতোমধ্যে বানানো প্রজেক্টে React যুক্ত করুন
---

<Intro>

আপনি যদি আপনার Existing প্রজেক্ট কিছু ইন্টারেক্টিভিটি যুক্ত করতে চান তবে আপনাকে তা React-এ নতুনভাবে লেখার প্রয়োজন নেই। আপনার Existing স্ট্যাকে React যোগ করুন এবং যে কোনো জায়গায় ইন্টারেক্টিভ React কম্পোনেন্ট রেন্ডার করুন।

</Intro>

<Note>

**Local ডেভেলপমেন্ট জন্য আপনাকে [Node.js](https://nodejs.org/en/) ইনস্টল করতে হবে।** যদিও আপনি একটি সহজ HTML পৃষ্ঠায় বা অনলাইনে [try React](/learn/installation#try-react) করতে পারেন, কিন্তু বাস্তবতানুযায়ী বেশিরভাগ জাভাস্ক্রিপ্ট টুলিং আপনি যেটিই ডেভেলপমেন্ট জন্য ব্যবহার করতে চান তার জন্য Node.js এর প্রয়োজন।

</Note>

## আপনার existing ওয়েবসাইটের একটি সম্পূর্ণ subroute এর জন্য React ব্যবহার করা {/*using-react-for-an-entire-subroute-of-your-existing-website*/}

চলুন ধরা যাক আপনার কিছু ইতিমধ্যে `example.com` নামে existing ওয়েব অ্যাপ রয়েছে এবং এটি একটি অন্য সার্ভার প্রযুক্তি (যেমন Rails) দ্বারা বানানো হয়েছে এবং আপনি চান `example.com/some-app/` দিয়ে শুরু হওয়া সমস্ত routes পুরোপুরি React দিয়ে ব্যবহার করতে।

এখানে আমরা যে ভাবে এটি সেট আপ করতে পরামর্শ দিচ্ছিঃ

১. **আপনার অ্যাপ এর React অংশটি** [React-ভিত্তিক ফ্রেমওয়ার্ক](/learn/start-a-new-react-project) দিয়ে বিল্ড করুন।
২. **ফ্রেমওয়ার্কের কনফিগারেশন অংশে `/some-app` _base path_ হিসেবে সেট করুন** (এখানে কিভাবে করবেন তা দেখুনঃ [Next.js](https://nextjs.org/docs/api-reference/next.config.js/basepath), [Gatsby](https://www.gatsbyjs.com/docs/how-to/previews-deploys-hosting/path-prefix/))।
৩. **আপনার সার্ভার বা প্রক্সি কনফিগার করুন** যাতে `/some-app/` এর সমস্ত request আপনার React অ্যাপ দ্বারা হ্যান্ডেল করা হয়।

এটি নিশ্চিত করে যে আপনার অ্যাপের React অংশ [এই ফ্রেমওয়ার্কগুলির উন্নয়নগুলির উপর ভিত্তি করে](/learn/start-a-new-react-project#can-i-use-react-without-a-framework)

অনেক React ভিত্তিক ফ্রেমওয়ার্ক full-stack এবং আপনার React অ্যাপটিকে সার্ভারের সুবিধা নিতে দেয়। তবে আপনি যদি সার্ভারে জাভাস্ক্রিপ্ট রান করার সুযোগ না পান অথবা না চান, সে ক্ষেত্রেও একই পদক্ষেপটি নিতে পারেন। সে ক্ষেত্রে, `/some-app/` এর পরিবর্তে HTML/CSS/JS এক্সপোর্ট করুন (Next.js এর জন্য [`next export` output](https://nextjs.org/docs/advanced-features/static-html-export), Gatsby এর জন্য ডিফল্ট )।

## আগে থেকে বিদ্যমান একটি পেইজে React ব্যবহার {/*using-react-for-a-part-of-your-existing-page*/}

আবার ধরা যাক, আপনার একটি পেইজ আগে থেকে আছে যা অন্য টেকনোলজি দিয়ে তৈরি করা হয়েছে (যেমন Rails এর মতো একটি সার্ভার টেকনোলজি বা Backbone এর মতো একটি ক্লায়েন্ট টেকনোলজি), এবং আপনি ঐ পেইজে ইন্টারেক্টিভ React কম্পোনেন্টগুলি রেন্ডার করতে চান। এটি React সংমিশ্রণ করার একটি সাধারণ উপায় । বাস্তবে, এটি মেটা এর জন্য বহু বছর ধরে React ব্যবহারের মধ্যে সবচেয়ে সাধারণ উপায় হিসাবে দেখা হচ্ছে!

আপনি এটি দুইটি ধাপে করতে পারেনঃ

১. **আপনাকে [JSX সিনট্যাক্স](/learn/writing-markup-with-jsx) ব্যবহার করার জন্য একটি জাভাস্ক্রিপ্ট এনভায়রনমেন্ট সেট আপ করতে হবে**, [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) / [`export`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) সিনট্যাক্স দিয়ে আপনার কোডকে মডিউলে ভাগ করতে হবে এবং npm (উদাহরণস্বরূপ, React) এর মতো প্যাকেজগুলি [npm] (https://www.npmjs.com/) প্যাকেজ রেজিস্ট্রি থেকে ব্যবহার করতে হবে।
২. আপনার React কম্পোনেন্টগুলি পেজে যেখানে দেখতে চান সেখানে **রেন্ডার করতে হবে**।

সঠিক পদক্ষেপটি আপনার বর্তমান পেইজ সেটআপের উপর নির্ভর করে, তাই আসুন কিছু বিস্তারিত দেখে নেই।

### ধাপ ১: একটি মডিউলার জাভাস্ক্রিপ্ট এনভায়রনমেন্ট সেট আপ করুন" {/*step-1-set-up-a-modular-javascript-environment*/}

একটি মডিউলার জাভাস্ক্রিপ্ট এনভায়রনমেন্ট আপনাকে একটি ফাইলে আপনার সমস্ত কোড লেখার বিপরীতে আলাদা আলাদা ফাইলগুলিতে আপনার React উপাদানগুলি লিখতে দেয়। এটি আপনাকে React সহ অন্যান্য ডেভেলপারদের [npm](https://www.npmjs.com/) রেজিস্ট্রির উপর প্রকাশিত সমস্ত অসাধারণ প্যাকেজগুলি ব্যবহার করতে দেয়। এখন এটি আপনি কিভাবে করতে চান তা আপনার বর্তমান সেটআপের উপর নির্ভর করে।

* **যদি আপনার অ্যাপ ইতোমধ্যে import স্টেটমেন্ট ব্যবহার করে ফাইলে ভাগ করা থাকে**, তাহলে সেই সেটআপটি ব্যবহার করা চেষ্টা করুন। আপনার জাভাস্ক্রিপ্ট কোডে `<div />` লিখলে কী সিনট্যাক্স এরর হয় তা চেক করুন। যদি এটি সিনট্যাক্স এরর দেখায়, তবে আপনাকে [Babel দিয়ে আপনার জাভাস্ক্রিপ্ট কোড রূপান্তর করতে হতে পারে](<(https://babeljs.io/setup)>) এবং JSX ব্যবহার করতে [Babel React preset](https://babeljs.io/docs/babel-preset-react) সক্ষম করতে হতে পারে।

* **যদি আপনার অ্যাপে জাভাস্ক্রিপ্ট মডিউল কম্পাইল করার জন্য ইতিমধ্যে কোনও সেটআপ না থাকে**, তবে [Vite](https://vitejs.dev/) দিয়ে সেটআপ করুন। Vite কমিউনিটি Rails, Django এবং Laravel সহ [ব্যাকেন্ড ফ্রেমওয়ার্ক সহ অনেক ইন্টিগ্রেশন](https://github.com/vitejs/awesome-vite#integrations-with-backends) বজায় রাখে। যদি আপনার ব্যাকেন্ড ফ্রেমওয়ার্ক তালিকাভুক্ত না থাকে তবে [এই গাইড](https://vitejs.dev/guide/backend-integration.html) অনুসরণ করে ব্যাকেন্ডের সাথে Vite বিল্ড ইন্টিগ্রেট করতে পারেন।

আপনার সেটআপ কাজ করছে কি না তা চেক করতে, আপনার প্রজেক্ট ফোল্ডারে এই কমান্ডটি চালানঃ

<TerminalBlock>
npm install react react-dom
</TerminalBlock>

তারপর আপনার মূল JavaScript ফাইলের শীর্ষে এই লাইনগুলি যোগ করুন (এটি হতে পারে `index.js` বা `main.js` নামের ফাইল):

<Sandpack>

```html index.html hidden
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <!-- ইতোমধ্যে আপনার পেইজে থাকা কনটেন্ট (এই উদাহরণে, এটি রিপ্লেস হয়ে যাবে) -->
  </body>
</html>
```

```js src/index.js active
import { createRoot } from 'react-dom/client';

// আগে থেকে বিদ্যমান HTML কনটেন্ট সরিয়ে ফেলুন
document.body.innerHTML = '<div id="app"></div>';

// এর জায়গায় আপনার React কম্পোনেন্ট রেন্ডার করুন
const root = createRoot(document.getElementById('app'));
root.render(<h1>Hello, world</h1>);
```

</Sandpack>

যদি আপনার পেইজের সম্পূর্ণ কনটেন্ট "Hello, world!" দ্বারা প্রতিস্থাপিত হয়ে যায়, তবে সবকিছু কাজ করছে! পড়তে থাকুন।

<Note>

একটি বিদ্যমান প্রজেক্টে একটি মডুলার জাভাস্ক্রিপ্ট এনভায়রনমেন্টকে যুক্ত করা প্রথমবার ভীতিজনক বোধ হতে পারে, কিন্তু এই পরিশ্রমটুকু এর প্রাপ্য! আপনি আটকে গেলে, আমাদের [কমিউনিটি রিসোর্স](/community) অথবা [Vite Chat](https://chat.vitejs.dev/) ব্যবহার করে দেখুন।

</Note>

### ধাপ ২: পেইজে যেকোনো জায়গায় React কম্পোনেন্ট রেন্ডার করুন। {/*step-2-render-react-components-anywhere-on-the-page*/}

আগের ধাপে, আপনি আপনার মূল ফাইলের শীর্ষে এই কোডটি রাখুনঃ
```js
import { createRoot } from 'react-dom/client';

// আগে থেকে বিদ্যমান HTML কনটেন্ট সরিয়ে ফেলুন
document.body.innerHTML = '<div id="app"></div>';

// এর জায়গায় আপনার React কম্পোনেন্ট রেন্ডার করুন
const root = createRoot(document.getElementById('app'));
root.render(<h1>Hello, world</h1>);
```


নিঃসন্দেহে আপনি সত্যি সত্যি বিদ্যমান HTML কন্টেন্ট মুছতে চান না!

এই কোডটি মুছে ফেলুন।

বরং, আপনি সম্ভবত আপনার HTML এ React কম্পোনেন্টগুলি নির্দিষ্ট জায়গায় রেন্ডার করতে চান। এখন আপনার HTML পেইজটি খুলুন
(অথবা সার্ভার টেমপ্লেটগুলি যা একে তৈরি করে) এবং কোনও ট্যাগে একটি ইউনিক [`id`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/id) এট্রিবিউট যুক্ত করুন, যেমনঃ

```html
<!-- ... আপনার HTML এর কোন এক জায়গায় ... -->
<nav id="navigation"></nav>
<!-- ... আরো HTML ... -->
```
এটি আপনাকে [`document.getElementById`](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById) ব্যবহার করে ঐ HTML উপাদানটি খুঁজে বের করতে দেয়। এরপর [`createRoot`](/reference/react-dom/client/createRoot) এ তা পাঠাতে হবে, যাতে আপনি আপনার নিজের React কম্পোনেন্টটি রেন্ডার করতে পারেনঃ

<Sandpack>

```html index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <p>This paragraph is a part of HTML.</p>
    <nav id="navigation"></nav>
    <p>This paragraph is also a part of HTML.</p>
  </body>
</html>
```

```js src/index.js active
import { createRoot } from 'react-dom/client';

function NavigationBar() {
  // TODO: প্রকৃতপক্ষে একটি ন্যাভিগেশন বার তৈরি করা
  return <h1>Hello from React!</h1>;
}

const domNode = document.getElementById('navigation');
const root = createRoot(domNode);
root.render(<NavigationBar />);
```

</Sandpack>

লক্ষ্য করুন যে index.html ফাইলের মূল HTML কন্টেন্টটি সংরক্ষিত আছে এবং আপনার নিজের NavigationBar React কম্পোনেন্টটি এখন আপনার HTML এর মধ্যে প্রদর্শিত হয় যেটি `<nav id="navigation">` এর ভিতরে অবস্থিত। React কম্পোনেন্টগুলি অবশ্যই বিদ্যমান HTML পৃষ্ঠার ভিতরে রেন্ডার করা সম্ভব হবে। বিস্তারিত জানতে, [createRoot usage documentation](/reference/react-dom/client/createRoot#rendering-a-page-partially-built-with-react) পড়ুন।

যখন আপনি একটি বিদ্যমান প্রকল্পে React অনুমোদন করবেন, তখন সাধারণত ছোট ইন্টারেক্টিভ কম্পোনেন্টগুলি দিয়ে শুরু করা হয় (যেমন বাটন), এবং পরে ধীরে ধীরে "উপরের দিকে" যাওয়া হয় এবং আপনার পুরো পেইজ এক সময় React দিয়ে বিল্ড হয়। আপনি যদি ঐ পর্যায়ে চলে আসেন, আমাদের উপদেশ থাকবে এর ঠিক পরই একটি [React ফ্রেমওয়ার্কে](/learn/start-a-new-react-project) মাইগ্রেট করবেন যেন React এর সুবিধাদি পূর্ণরূপে ব্যবহার করতে পারেন।

## একটি বিদ্যমান নেটিভ মোবাইল অ্যাপ এ React Native ব্যবহার করা {/*using-react-native-in-an-existing-native-mobile-app*/}

[React Native](https://reactnative.dev/) বিদ্যমান নেটিভ অ্যাপগুলির সাথে এক করা যেতে পারে। আপনার যদি Android (Java বা Kotlin) বা iOS (Objective-C বা Swift) এর জন্য একটি বিদ্যমান নেটিভ অ্যাপ থাকে, তবে [এই গাইডটি](https://reactnative.dev/docs/integration-with-existing-apps) অনুসরণ করে সেখানে একটি React Native স্ক্রিন যুক্ত করতে পারেন।
