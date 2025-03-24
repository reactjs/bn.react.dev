---
title: startTransition
---

<Intro>

<<<<<<< HEAD
`startTransition` আপনাকে UI ব্লক না করে state আপডেট করতে দিবে।
=======
`startTransition` lets you render a part of the UI in the background.
>>>>>>> f6d762cbbf958ca45bb8d1d011b31e5289e43a3d

```js
startTransition(action)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `startTransition(action)` {/*starttransition*/}

`startTransition` ফাংশন আপনাকে একটি state update কে transition হিসেবে চিহ্নিত করতে দেবে।

```js {7,9}
import { startTransition } from 'react';

function TabContainer() {
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
  // ...
}
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

<<<<<<< HEAD
* `scope`: একটি ফাংশন যেটা এক বা একাধিক [`set` functions.](/reference/react/useState#setstate) কল করার মাধ্যমে কোন state আপডেট করে। React তৎক্ষণাৎভাবে কোন প্যারামিটার ছাড়া `scope` কল করে এবং `scope` ফাংশন কল হবার সময়ে সিঙ্ক্রোনাসভাবে শিডিউল হওয়া সকল state update কে transition হিসেবে চিহ্নিত করে। এগুলো [non-blocking](/reference/react/useTransition#marking-a-state-update-as-a-non-blocking-transition) হবে এবং [অবাঞ্ছিত কোন লোডিং ইনডিকেটর দেখাবে না।](/reference/react/useTransition#preventing-unwanted-loading-indicators)
=======
* `action`: A function that updates some state by calling one or more [`set` functions](/reference/react/useState#setstate). React calls `action` immediately with no parameters and marks all state updates scheduled synchronously during the `action` function call as Transitions. Any async calls awaited in the `action` will be included in the transition, but currently require wrapping any `set` functions after the `await` in an additional `startTransition` (see [Troubleshooting](/reference/react/useTransition#react-doesnt-treat-my-state-update-after-await-as-a-transition)). State updates marked as Transitions will be [non-blocking](#marking-a-state-update-as-a-non-blocking-transition) and [will not display unwanted loading indicators.](/reference/react/useTransition#preventing-unwanted-loading-indicators).
>>>>>>> f6d762cbbf958ca45bb8d1d011b31e5289e43a3d

#### রিটার্ন {/*returns*/}

`startTransition` কিছু রিটার্ন করে না।

#### সতর্কতা {/*caveats*/}

* `startTransition` এমন কোন পন্থা দেয় না যেটা দিয়ে বুঝা বুঝা যাবে কোন transition পেন্ডীং আছে কি না। যখন transition চলছে সে সময়ে পেন্ডীঙ ইনডিকেটর দেখাবার জন্য আপনার বরং [`useTransition`](/reference/react/useTransition) এর দরকার পড়বে।

* আপনি একটি আপডেট একটি transition এ wrap করতে পারবেন কেবল যদি আপনার ওই state এর `set` ফাংশনে এক্সেস করার অনুমতি থাকে। যদি আপনি কোন prop বা কাস্টম হুকের রেসপন্স হিসেবে একটি transition শুরু করতে চান, বরং [`useDeferredValue`](/reference/react/useDeferredValue) ব্যবহার করুন। 

<<<<<<< HEAD
* আপনি `startTransition` এ যে ফাংশন পাস করবেন সেটা অবশ্যই সিঙ্ক্রোনাস হতে হবে। React সাথে সাথে এই ফাংশন এক্সিকিউট করে, এবং এটা এক্সিকিউট হবার সময়ে চলমান সকল state update কে transition হিসেবে চিহ্নিত করে। আপনি যদি আরো state update করতে চান (যেমন্, একটা টাইমআউটের মধ্যে), তাহলে সেগুলো transition হিসেবে চিহ্নিত হবে না।
=======
* The function you pass to `startTransition` is called immediately, marking all state updates that happen while it executes as Transitions. If you try to perform state updates in a `setTimeout`, for example, they won't be marked as Transitions.

* You must wrap any state updates after any async requests in another `startTransition` to mark them as Transitions. This is a known limitation that we will fix in the future (see [Troubleshooting](/reference/react/useTransition#react-doesnt-treat-my-state-update-after-await-as-a-transition)).
>>>>>>> f6d762cbbf958ca45bb8d1d011b31e5289e43a3d

* Transition হিসেবে চিহ্নিত একটি state আপডেট অন্যান্য state update দ্বারা বাধাপ্রাপ্ত হবে। উদাহরণস্বরূপ, আপনি যদি একটি transition এর মধ্যে একটি চার্ট component আপডেট করেন, কিন্তু তার পরি চার্ট re-render এর মধ্যে থাকা অবস্থায় একটি ইনপুট ফিল্ডে টাইপ করা শুরু করেন, React ইনপুট state আপডেট হ্যান্ডল করার পর চার্ট componenT এর রেন্ডারিং এর কাজ পুনরায় শুরু করবে।

* Transition আপডেট টেক্সট ইনপুট নিয়ন্ত্রণ করার জন্য ব্যবহার করা যাবে না।

<<<<<<< HEAD
* যদি একাধিক transition চলমান থাকে, React সেগুলোকে একসাথে ব্যাচ করে। এটা একটা সীমাবদ্ধতা যা সম্ভবত ভবিষ্যতের কোন রিলিজে ঠিক করা হবে।
=======
* If there are multiple ongoing Transitions, React currently batches them together. This is a limitation that may be removed in a future release.
>>>>>>> f6d762cbbf958ca45bb8d1d011b31e5289e43a3d

---

## ব্যবহার {/*usage*/}

### একটি state update কে non-blocking transition হিসেবে চিহ্নিত করা {/*marking-a-state-update-as-a-non-blocking-transition*/}

আপনি একটি state update কে `startTransition` কলের মধ্যে wrap করার মাধ্যমে *transition* হিসেবে চিহ্নিত করতে পারেনঃ

```js {7,9}
import { startTransition } from 'react';

function TabContainer() {
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
  // ...
}
```

Transitions আপনাকে ধীরগতির ডিভাইসগুলোতেও ইউজার ইন্টারফেস আপডেট রেস্পন্সিভ রাখার সুযোগ দেয়।

Transition এর সাথে, একতা re-render এর মধ্যে আপনার UI রেস্পন্সিভ থাকে। উদাহরণস্বরূপ, যদি একজন ব্যবহারকারী একটা ট্যাবে ক্লিক করে এবং এর পরেই তার ইচ্ছা পরিবর্তন করে অন্য একটি ইয়াব ক্লিক করে, সেটা তারা প্রথম re-render শেষ হবার অপেক্ষা না করেই করতে পারবেন।

<Note>

`startTransition` এর সাথে খুব মিল আছে [`useTransition`](/reference/react/useTransition) এর, শুধু এই বিষয়টা ব্যতীত যে এটা কোন transition চলছে কি না ট্র্যাক করবার জন্য `isPending` ফ্ল্যাগ দেয় না। `useTransition` যদি না থাকে তখন আপনি  `startTransition` কল দিতে পারেন। উদাহরণস্বরূপ, `startTransition` component এর বাইরে কাজ করে, যেমন সেটা হতে পারে একটা ডেটা লাইব্রেরি থেকে।

[Transition এর ব্যাপারে শিখুন এবং উদাহরণ দেখুন `useTransition` পেইজে।](/reference/react/useTransition)

</Note>
