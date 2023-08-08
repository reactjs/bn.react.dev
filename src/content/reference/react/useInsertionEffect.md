---
title: useInsertionEffect
---

<Pitfall>

`useInsertionEffect`  CSS-in-JS library author-দের জন্য। যদি না আপনি একটি কাজ করছেন এবং স্টাইল ইনজেক্ট করার জন্য একটা জায়গার প্রয়োজন বোধ করছেন, সম্ভবত আপনি `useInsertionEffect` এর বদলে [`useEffect`](/reference/react/useEffect) অথবা [`useLayoutEffect`](/reference/react/useLayoutEffect) ব্যবহার করতে চান।

</Pitfall>

<Intro>

`useInsertionEffect` কোন লেআউট effect fire করার আগেই DOM এ বিভিন্ন এলিমেন্ট ইন্সার্ট করার সুযোগ দেয়।

```js
useInsertionEffect(setup, dependencies?)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `useInsertionEffect(setup, dependencies?)` {/*useinsertioneffect*/}

লেআউট রিড করতে হবে এমন কোন effect fire করার আগে স্টাইল ইনসার্ট করার জন্য `useInsertionEffect` কল করুনঃ

```js
import { useInsertionEffect } from 'react';

// আপনার CSS-in-JS লাইব্রেরির মধ্যে
function useCSS(rule) {
  useInsertionEffect(() => {
    // ... <style> ট্যাগগুলো এখানে ইনজেক্ট করুন ...
  });
  return rule;
}
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

* `setup`: যেই ফাংশনে আপনার Effect এর লজিক আছে। আপনার সেটআপ ফাংশন একটি *cleanup* ফাংশন optionally রিটার্ন করতে পারে। আপনার কম্পোনেন্ট DOM এ যুক্ত হবার সময়, কিন্তু কোন লেআউট effect fire করার আগে, React আপনার সেটআপ ফাংশন রান করবে। পরিবর্তিত ডিপেন্ডেন্সির সাথে যতবার রি-রেন্ডার হবে, React প্রথমে আপনার পুরনো ভ্যালুগুলো ব্যবহার করে cleanup ফাংশন রান করবে (যদি আপনি দিয়ে থাকেন), তারপর নতুন ভ্যালুগুলো ব্যবহার করে সেটআপ ফাংশন রান করবে। আপনার কম্পোনেন্ট DOM থেকে সরিয়ে ফেলবার আগে, React আপনার cleanup ফাংশন রান করবে।
 
* **optional** `dependencies`: `setup` কোডের মধ্যে রেফারেন্স করা সকল রিয়াক্টিভ ভ্যালুর তালিকা। রিয়াক্টিভ ভ্যালুর মধ্যে রয়েছে props, state এবং আপনার কম্পোনেন্ট বডির মধ্যে সরাসরি ডিক্লেয়ার হওয়া সকল ভ্যারিয়েবল এবং ফাংশন। যদি আপনার লিন্টার  [React এর জন্য কনফিগার করা থাকে](/learn/editor-setup#linting), এটা নিশ্চিত করবে যে প্রতিটা রিয়াক্টিভ ভ্যালু সঠিকভাবে ডিপেন্ডেন্সি হিসেবে চিহ্নিত করা আছে। ডিপেন্ডেন্সির তালিকাতে অবশ্যই আইটেমের সংখ্যা ধ্রুবক হতে হবে এবং `[dep1, dep2, dep3]` এর মত ইনলাইনে থাকতে হবে।  React will compare each dependency with its previous value using the [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) comparison algorithn ব্যবহার করে প্রতিটা ডিপেন্ডেন্সি এবং এর আগের ভ্যালু তুলনা করবে।আপনি যদি ডিপেন্ডেন্সি চিহ্নিত করে না দেন, আপনার Effect কম্পোনেন্টের প্রতিটি re-render এ re-run হবে।

#### রিটার্ন {/*returns*/}

`useInsertionEffect` রিটার্ন করে `undefined`.

#### সতর্কতা {/*caveats*/}

* Effects শুধুমাত্র ক্লায়েন্টে কাজ করে। এগুলো সার্ভার রেন্ডারিং এর সময় রান করে না।
* আপনি `useInsertionEffect` এর মধ্য হতে state আপডেট করতে পারবেন না।
* যতক্ষণে `useInsertionEffect` রান করে, ref তখনো যুক্ত হয় নাই এবং DOM আপডেট হয় নাই।
* `useInsertionEffect` DOM আপডেটের আগেও রান করতে পারে, পরেও করতে পারে। কোন একটি নির্দিষ্ট সময়ে DOM আপডেট হবে এর উপর নির্ভর করে থাকবেন না।
* অন্যান্য Effect যেম্ন প্রতিবার ক্লিনআপ শুরু করে তার পর সেটাপ শুরু করে, `useInsertionEffect` তার ব্যতিক্রম। এটা প্রতিটি কম্পোনেন্টে একই সাথে ক্লিনাপ এবন সেটাপ শুরু করবে। যেটার ফলস্বরূপ, ক্লিনাপ এবং সেটাপ ফাংশনে একটা "interleaving" থেকে যায়।
---

## ব্যবহার {/*usage*/}

### CSS-in-JS লাইব্রেরিতে dynamic styles injection {/*injecting-dynamic-styles-from-css-in-js-libraries*/}

প্রথাগতভাবে, আপনি React কম্পোনেন্ট plain CSS ব্যবহার করেই স্টাইল করবেন। 

```js
// আপনার JS ফাইলেঃ
<button className="success" />

// আপনার CSS ফাইলেঃ
.success { color: green; }
```

কেউ কেউ আলাদা CSS ফাইলে স্টাইল করার কোড লেখার চেয়ে জাভাস্ক্রিপ্টে স্টাইল কোড লিখতে পছন্দ করেন। এতে সাধারণত কোন CSS-in-JS লাইব্রেরি বা কোন একটি টুল ব্যবহার করা প্রয়োজন পড়ে। CSS-in-JS ব্যবহারের তিনটি বহুল ব্যবহৃত পদ্ধতি আছেঃ

1. কম্পাইলার ব্যবহার করে CSS ফাইলে স্ট্যাটিক এক্সট্রাকশন
2. ইনলাইন স্টাইল, যেমন, `<div style={{ opacity: 1 }}>`
3. `<style>` ট্যাগের রানটাইম ইনজেকশন

আপনি যদি CSS-in-JS ব্যবহার করেন, আমরা উপরের দুটি পদ্ধতির সমন্বয় ব্যবহার করতে উপদেশ দেব (স্ট্যাটিক স্টাইলের জন্য CSS ফাইল, ডাইনামিক স্টাইলের জন্য ইনলাইন স্টাইল।) **আমরা দুটি কারণে রানটাইম `<style>` ট্যাগ ইনজেকশন এড়িয়ে চলতে বলবঃ**

1. রানটাইম ইনজেকশনের ক্ষেত্রে স্টাইলগুলোকে বার বার হিসেব করতে বাধ্য হয় ব্রাউজার। 
2. React লাইফসাইকেলের ভুল একটা সময়ে যদি রানটাইম ইনজেকশন হয় তাহলে সেটা খুবই ধীরগতির হতে পারে। 

প্রথম সমস্যাটা সমাধানযোগ্য নয়, কিন্তু `useInsertionEffect` আপনাকে দ্বিতীয় সমস্যাটা সমাধানে সাহায্য করবে। 

কোন লেআউট effect fire করার আগে স্টাইল ইনসার্ট করার জন্য `useInsertionEffect` কল করুনঃ

```js {4-11}
// আপনার CSS-in-JS লাইব্রেরির মধ্যে
let isInserted = new Set();
function useCSS(rule) {
  useInsertionEffect(() => {
    // আগেই যেভাবে ব্যখ্যা করা হয়েছে, আমরা রানটাইমে <style> ট্যাগ ইনজেক্ট না করার পরামর্শ দেই।
    // কিন্তু যদি আপনাকে এটা করতেই হয়, এটা useInsertionEffect এর মধ্যে করা জরুরি।
    if (!isInserted.has(rule)) {
      isInserted.add(rule);
      document.head.appendChild(getStyleForRule(rule));
    }
  });
  return rule;
}

function Button() {
  const className = useCSS('...');
  return <div className={className} />;
}
```

`useEffect` এর ন্যায়, `useInsertionEffect` সার্ভারে রান করে না। আপনার যদি সার্ভারে ব্যবহৃত CSS rule গুলো সংগ্রহ করার প্রয়োজন পড়ে, আপনি সেটা রেন্ডারিং এর সময়ে করতে পারেনঃ

```js {1,4-6}
let collectedRulesSet = new Set();

function useCSS(rule) {
  if (typeof window === 'undefined') {
    collectedRulesSet.add(rule);
  }
  useInsertionEffect(() => {
    // ...
  });
  return rule;
}
```

[CSS-in-JS libraries with runtime injection থেকে `useInsertionEffect` এ আপগ্রেড নিয়ে আরো পড়ুন।](https://github.com/reactwg/react-18/discussions/110)

<DeepDive>

#### Rendering বা useLayoutEffect এর সময় স্টাইল ইঞ্জেক্ট করবার চেয়ে এটা কীভাবে আরো ভাল? {/*how-is-this-better-than-injecting-styles-during-rendering-or-uselayouteffect*/}

আপনি যদি রেন্ডারিং এর সময় স্টাইল ইনসার্ট করেন যখন React একটি [নন-ব্লকিং আপডেট,](/reference/react/useTransition#marking-a-state-update-as-a-non-blocking-transition) প্রসেস করছে, ব্রাউজার একটি কম্পোনেন্ট ট্রি রেন্ডার করতে করতে প্রতিটা ফ্রেমে স্টাইলগুলো আবার হিসেব করে, যেটা **অত্যন্ত ধীরগতির হতে পারে।** 

[`useLayoutEffect`](/reference/react/useLayoutEffect) বা [`useEffect`](/reference/react/useEffect) এর সময় স্টাইল ইনসার্ট করার চেয়ে `useInsertionEffect` ভাল, কারণ এটা নিশ্চিত করে যে যতক্ষণে আপনার কম্পোনেন্টে অন্যান্য Effect রান করছে, ততক্ষণে সকল `<style>` ট্যাগ ইনসার্ট করা হয়ে গেছে। অন্যথায়, পুরনো স্টাইলের কারণে সাধারণ Effect এ লেআউটের হিসেব ভুল হবে।

</DeepDive>
