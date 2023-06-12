---
title: <Profiler>
---

<Intro>

`<Profiler>` আপনাকে একটা React ট্রিয়ের রেন্ডারিং পারফরম্যান্স প্রোগ্রামের সাহায্যে পরিমাপ করার সুযোগ দেয়।

```js
<Profiler id="App" onRender={onRender}>
  <App />
</Profiler>
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `<Profiler>` {/*profiler*/}

কম্পোনেন্ট ট্রিয়ের রেন্ডারিং পারফরম্যান্স পরিমাপ করার জন্য একে `<Profiler>` এর মধ্যে wrap করুন।

```js
<Profiler id="App" onRender={onRender}>
  <App />
</Profiler>
```

#### Props {/*props*/}

* `id`: একটা স্ট্রিং যেটা আপনি UI এর যে অংশ পরিমাপ করতে চান সেটাকে চিহ্নিত করতে ব্যবহৃত হয়।
* `onRender`: এটা একটা [`onRender` কলব্যাক](#onrender-callback) যেটাকে প্রতিবার প্রোফাইল হতে থাকা ট্রিয়ের মধ্যকার কম্পোনেন্ট আপডেট হলে React কল করে। কী রেন্ডার হল এবং কেমন সময় লাগল এই তথ্যটা সে পায়।

#### Caveats {/*caveats*/}

* Profiling adds some additional overhead, so **it is disabled in the production build by default.** To opt into production profiling, you need to enable a [special production build with profiling enabled.](https://fb.me/react-profiling)

---

### `onRender` callback {/*onrender-callback*/}

React will call your `onRender` callback with information about what was rendered.

```js
function onRender(id, phase, actualDuration, baseDuration, startTime, commitTime) {
  // Aggregate or log render timings...
}
```

#### Parameters {/*onrender-parameters*/}

* `id`: The string `id` prop of the `<Profiler>` tree that has just committed. This lets you identify which part of the tree was committed if you are using multiple profilers.
* `phase`: `"mount"`, `"update"` or `"nested-update"`. This lets you know whether the tree has just been mounted for the first time or re-rendered due to a change in props, state, or hooks.
* `actualDuration`: The number of milliseconds spent rendering the `<Profiler>` and its descendants for the current update. This indicates how well the subtree makes use of memoization (e.g. [`memo`](/reference/react/memo) and [`useMemo`](/reference/react/useMemo)). Ideally this value should decrease significantly after the initial mount as many of the descendants will only need to re-render if their specific props change.
* `baseDuration`: The number of milliseconds estimating how much time it would take to re-render the entire `<Profiler>` subtree without any optimizations. It is calculated by summing up the most recent render durations of each component in the tree. This value estimates a worst-case cost of rendering (e.g. the initial mount or a tree with no memoization). Compare `actualDuration` against it to see if memoization is working.
* `startTime`: A numeric timestamp for when React began rendering the current update.
* `endTime`: A numeric timestamp for when React committed the current update. This value is shared between all profilers in a commit, enabling them to be grouped if desirable.

---

## Usage {/*usage*/}

### Measuring rendering performance programmatically {/*measuring-rendering-performance-programmatically*/}

Wrap the `<Profiler>` component around a React tree to measure its rendering performance.

```js {2,4}
<App>
  <Profiler id="Sidebar" onRender={onRender}>
    <Sidebar />
  </Profiler>
  <PageContent />
</App>
```

It requires two props: an `id` (string) and an `onRender` callback (function) which React calls any time a component within the tree "commits" an update.

<Pitfall>

Profiling adds some additional overhead, so **it is disabled in the production build by default.** To opt into production profiling, you need to enable a [special production build with profiling enabled.](https://fb.me/react-profiling)

</Pitfall>

<Note>

`<Profiler>` আপনাকে প্রোগ্রামেটিকালি পরিমাপ জানতে সাহায্য করে। আপনি যদি interactive প্রোফাইলার চান, তাহলে [React ডেভেলপার টুলসের](/learn/react-developer-tools) Profiler ট্যাবটা ব্যবহার করে দেখতে পারেন। এটা ব্রাউজার এক্সটেনশন হিসেবে কাছাকাছি রকম কাজ করে।

</Note>

---

### অ্যাপ্লিকেশনের বিভিন্ন অংশের পরিমাপ {/*measuring-different-parts-of-the-application*/}

অ্যাপ্লিকেশনের বিভিন্ন অংশের পরিমাপের জন্য আপনি একাধিক `<Profiler>` ব্যবহার করতে পারেনঃ

```js {5,7}
<App>
  <Profiler id="Sidebar" onRender={onRender}>
    <Sidebar />
  </Profiler>
  <Profiler id="Content" onRender={onRender}>
    <Content />
  </Profiler>
</App>
```

আপনি `<Profiler>` কম্পোনেন্টগুলো নেস্টও করতে পারেনঃ

```js {5,7,9,12}
<App>
  <Profiler id="Sidebar" onRender={onRender}>
    <Sidebar />
  </Profiler>
  <Profiler id="Content" onRender={onRender}>
    <Content>
      <Profiler id="Editor" onRender={onRender}>
        <Editor />
      </Profiler>
      <Preview />
    </Content>
  </Profiler>
</App>
```

যদি `<Profiler>` একটা হালকা কম্পোনেন্ট, এটা শুধুমাত্র তখনি ব্যবহার করা উচিত যখন প্রয়োজন পড়ছে। প্রতি বার ব্যবহারে অ্যাপের CPU এবং Memory এর উপরে অতিরিক্ত কিছু চাপ পড়ে।

---

