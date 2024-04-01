---
title: startTransition
---

<Intro>

`startTransition` আপনাকে UI ব্লক না করে state আপডেট করতে দিবে।

```js
startTransition(scope)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `startTransition(scope)` {/*starttransitionscope*/}

<<<<<<< HEAD
`startTransition` ফাংশন আপনাকে একটি state update কে transition হিসেবে চিহ্নিত করতে দেবে।
=======
The `startTransition` function lets you mark a state update as a Transition.
>>>>>>> 97489434323b0c4cce78588cd0f48e3808e0eba4

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
* `scope`: A function that updates some state by calling one or more [`set` functions.](/reference/react/useState#setstate) React immediately calls `scope` with no arguments and marks all state updates scheduled synchronously during the `scope` function call as Transitions. They will be [non-blocking](/reference/react/useTransition#marking-a-state-update-as-a-non-blocking-transition) and [will not display unwanted loading indicators.](/reference/react/useTransition#preventing-unwanted-loading-indicators)
>>>>>>> 97489434323b0c4cce78588cd0f48e3808e0eba4

#### রিটার্ন {/*returns*/}

`startTransition` কিছু রিটার্ন করে না।

#### সতর্কতা {/*caveats*/}

<<<<<<< HEAD
* `startTransition` এমন কোন পন্থা দেয় না যেটা দিয়ে বুঝা বুঝা যাবে কোন transition পেন্ডীং আছে কি না। যখন transition চলছে সে সময়ে পেন্ডীঙ ইনডিকেটর দেখাবার জন্য আপনার বরং [`useTransition`](/reference/react/useTransition) এর দরকার পড়বে।

* আপনি একটি আপডেট একটি transition এ wrap করতে পারবেন কেবল যদি আপনার ওই state এর `set` ফাংশনে এক্সেস করার অনুমতি থাকে। যদি আপনি কোন prop বা কাস্টম হুকের রেসপন্স হিসেবে একটি transition শুরু করতে চান, বরং [`useDeferredValue`](/reference/react/useDeferredValue) ব্যবহার করুন। 

* আপনি `startTransition` এ যে ফাংশন পাস করবেন সেটা অবশ্যই সিঙ্ক্রোনাস হতে হবে। React সাথে সাথে এই ফাংশন এক্সিকিউট করে, এবং এটা এক্সিকিউট হবার সময়ে চলমান সকল state update কে transition হিসেবে চিহ্নিত করে। আপনি যদি আরো state update করতে চান (যেমন্, একটা টাইমআউটের মধ্যে), তাহলে সেগুলো transition হিসেবে চিহ্নিত হবে না।

* Transition হিসেবে চিহ্নিত একটি state আপডেট অন্যান্য state update দ্বারা বাধাপ্রাপ্ত হবে। উদাহরণস্বরূপ, আপনি যদি একটি transition এর মধ্যে একটি চার্ট component আপডেট করেন, কিন্তু তার পরি চার্ট re-render এর মধ্যে থাকা অবস্থায় একটি ইনপুট ফিল্ডে টাইপ করা শুরু করেন, React ইনপুট state আপডেট হ্যান্ডল করার পর চার্ট componenT এর রেন্ডারিং এর কাজ পুনরায় শুরু করবে।
=======
* `startTransition` does not provide a way to track whether a Transition is pending. To show a pending indicator while the Transition is ongoing, you need [`useTransition`](/reference/react/useTransition) instead.

* You can wrap an update into a Transition only if you have access to the `set` function of that state. If you want to start a Transition in response to some prop or a custom Hook return value, try [`useDeferredValue`](/reference/react/useDeferredValue) instead.

* The function you pass to `startTransition` must be synchronous. React immediately executes this function, marking all state updates that happen while it executes as Transitions. If you try to perform more state updates later (for example, in a timeout), they won't be marked as Transitions.

* A state update marked as a Transition will be interrupted by other state updates. For example, if you update a chart component inside a Transition, but then start typing into an input while the chart is in the middle of a re-render, React will restart the rendering work on the chart component after handling the input state update.
>>>>>>> 97489434323b0c4cce78588cd0f48e3808e0eba4

* Transition আপডেট টেক্সট ইনপুট নিয়ন্ত্রণ করার জন্য ব্যবহার করা যাবে না।

<<<<<<< HEAD
* যদি একাধিক transition চলমান থাকে, React সেগুলোকে একসাথে ব্যাচ করে। এটা একতা সীমাবদ্ধতা যা সম্ভবত ভবিষ্যতের কোন রিলিজে ঠিক করা হবে।
=======
* If there are multiple ongoing Transitions, React currently batches them together. This is a limitation that will likely be removed in a future release.
>>>>>>> 97489434323b0c4cce78588cd0f48e3808e0eba4

---

## ব্যবহার {/*usage*/}

<<<<<<< HEAD
### একটি state update কে non-blocking transition হিসেবে চিহ্নিত করা {/*marking-a-state-update-as-a-non-blocking-transition*/}

আপনি একটি state update কে `startTransition` কলের মধ্যে wrap করার মাধ্যমে *transition* হিসেবে চিহ্নিত করতে পারেনঃ
=======
### Marking a state update as a non-blocking Transition {/*marking-a-state-update-as-a-non-blocking-transition*/}

You can mark a state update as a *Transition* by wrapping it in a `startTransition` call:
>>>>>>> 97489434323b0c4cce78588cd0f48e3808e0eba4

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

<<<<<<< HEAD
Transition এর সাথে, একতা re-render এর মধ্যে আপনার UI রেস্পন্সিভ থাকে। উদাহরণস্বরূপ, যদি একজন ব্যবহারকারী একটা ট্যাবে ক্লিক করে এবং এর পরেই তার ইচ্ছা পরিবর্তন করে অন্য একটি ইয়াব ক্লিক করে, সেটা তারা প্রথম re-render শেষ হবার অপেক্ষা না করেই করতে পারবেন।

<Note>

`startTransition` এর সাথে খুব মিল আছে [`useTransition`](/reference/react/useTransition) এর, শুধু এই বিষয়টা ব্যতীত যে এটা কোন transition চলছে কি না ট্র্যাক করবার জন্য `isPending` ফ্ল্যাগ দেয় না। `useTransition` যদি না থাকে তখন আপনি  `startTransition` কল দিতে পারেন। উদাহরণস্বরূপ, `startTransition` component এর বাইরে কাজ করে, যেমন সেটা হতে পারে একটা ডেটা লাইব্রেরি থেকে।

[Transition এর ব্যাপারে শিখুন এবং উদাহরণ দেখুন `useTransition` পেইজে।](/reference/react/useTransition)
=======
With a Transition, your UI stays responsive in the middle of a re-render. For example, if the user clicks a tab but then change their mind and click another tab, they can do that without waiting for the first re-render to finish.

<Note>

`startTransition` is very similar to [`useTransition`](/reference/react/useTransition), except that it does not provide the `isPending` flag to track whether a Transition is ongoing. You can call `startTransition` when `useTransition` is not available. For example, `startTransition` works outside components, such as from a data library.

[Learn about Transitions and see examples on the `useTransition` page.](/reference/react/useTransition)
>>>>>>> 97489434323b0c4cce78588cd0f48e3808e0eba4

</Note>
