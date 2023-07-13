---
title: useInsertionEffect
---

<Pitfall>

`useInsertionEffect`  CSS-in-JS library author-দের জন্য। যদি না আপনি একটি কাজ করছেন এবং স্টাইল ইনজেক্ট করার জন্য একটা জায়গার প্রয়োজন বোধ করছেন, সম্ভবত আপনি `useInsertionEffect` এর বদলে [`useEffect`](/reference/react/useEffect) অথবা [`useLayoutEffect`](/reference/react/useLayoutEffect) ব্যবহার করতে চান।

</Pitfall>

<Intro>

`useInsertionEffect` হচ্ছে [`useEffect`](/reference/react/useEffect) এর একটি ভার্শন যা যেকোন DOM মিউটেশনের আগে fire হয়।

```js
useInsertionEffect(setup, dependencies?)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `useInsertionEffect(setup, dependencies?)` {/*useinsertioneffect*/}

যেকোন DOM মিউটেশনের আগে স্টাইল ইনসার্ট করার জন্য `useInsertionEffect` কল করুনঃ

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

* `setup`: যেই ফাংশনে আপনার Effect এর লজিক আছে। আপনার সেটআপ ফাংশন একটি *cleanup* ফাংশন optionally রিটার্ন করতে পারে। আপনার কম্পোনেন্ট DOM এ যুক্ত হবার আগে, React আপনার সেটআপ ফাংশন রান করবে। পরিবর্তিত ডিপেন্ডেন্সির সাথে যতবার রি-রেন্ডার হবে, React প্রথমে আপনার পুরনো ভ্যালুগুলো ব্যবহার করে cleanup ফাংশন রান করবে (যদি আপনি দিয়ে থাকেন), তারপর নতুন ভ্যালুগুলো ব্যবহার করে সেটআপ ফাংশন রান করবে। আপনার কম্পোনেন্ট DOM থেকে সরিয়ে ফেলবার আগে, React আপনার cleanup ফাংশন রান করবে।
 
* **optional** `dependencies`: The list of all reactive values referenced inside of the `setup` code. Reactive values include props, state, and all the variables and functions declared directly inside your component body. If your linter is [configured for React](/learn/editor-setup#linting), it will verify that every reactive value is correctly specified as a dependency. The list of dependencies must have a constant number of items and be written inline like `[dep1, dep2, dep3]`. React will compare each dependency with its previous value using the [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) comparison algorithm. If you don't specify the dependencies at all, your Effect will re-run after every re-render of the component.

#### রিটার্ন {/*returns*/}

`useInsertionEffect` রিটার্ন করে `undefined`.

#### সতর্কতা {/*caveats*/}

* Effects শুধুমাত্র ক্লায়েন্টে কাজ করে। এগুলো সার্ভার রেন্ডারিং এর সময় রান করে না।
* আপনি `useInsertionEffect` এর মধ্য হতে state আপডেট করতে পারবেন না।
* যতক্ষণে `useInsertionEffect` রান করে, ref তখনো যুক্ত হয় নাই এবং DOM আপডেট হয় নাই।

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

Some teams prefer to author styles directly in JavaScript code instead of writing CSS files. This usually requires using a CSS-in-JS library or a tool. There are three common approaches to CSS-in-JS:

1. Static extraction to CSS files with a compiler
2. Inline styles, e.g. `<div style={{ opacity: 1 }}>`
3. Runtime injection of `<style>` tags

If you use CSS-in-JS, we recommend a combination of the first two approaches (CSS files for static styles, inline styles for dynamic styles). **We don't recommend runtime `<style>` tag injection for two reasons:**

1. Runtime injection forces the browser to recalculate the styles a lot more often.
2. Runtime injection can be very slow if it happens at the wrong time in the React lifecycle.

The first problem is not solvable, but `useInsertionEffect` helps you solve the second problem.

Call `useInsertionEffect` to insert the styles before any DOM mutations:

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

Similarly to `useEffect`, `useInsertionEffect` does not run on the server. If you need to collect which CSS rules have been used on the server, you can do it during rendering:

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

[Read more about upgrading CSS-in-JS libraries with runtime injection to `useInsertionEffect`.](https://github.com/reactwg/react-18/discussions/110)

<DeepDive>

#### How is this better than injecting styles during rendering or useLayoutEffect? {/*how-is-this-better-than-injecting-styles-during-rendering-or-uselayouteffect*/}

If you insert styles during rendering and React is processing a [non-blocking update,](/reference/react/useTransition#marking-a-state-update-as-a-non-blocking-transition) the browser will recalculate the styles every single frame while rendering a component tree, which can be **extremely slow.**

`useInsertionEffect` is better than inserting styles during [`useLayoutEffect`](/reference/react/useLayoutEffect) or [`useEffect`](/reference/react/useEffect) because it ensures that by the time other Effects run in your components, the `<style>` tags have already been inserted. Otherwise, layout calculations in regular Effects would be wrong due to outdated styles.

</DeepDive>
