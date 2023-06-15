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

#### সতর্কতা {/*caveats*/}

* প্রোফাইলিং কিছু অতিরিক্ত কাজ বাড়ায়, তাই এটা **প্রোডাকশন বিল্ডে স্বাভাবিকভাবে বন্ধ থাকে।** প্রোডাকশন প্রোফাইলিং করতে, আপনাকে [প্রোফাইলিং সক্রিয় আছে এমন একটি বিশেষ প্রোডাকশন বিল্ড](https://fb.me/react-profiling) চালু করতে হবে।

---

### `onRender` কলব্যাক {/*onrender-callback*/}

React `onRender` কে কল করবে যেখানে কী রেন্ডার হয়েছিল সেই তথ্য থাকবে।

```js
function onRender(id, phase, actualDuration, baseDuration, startTime, commitTime) {
  // Aggregate or log render timings...
}
```

#### প্যারামিটার {/*onrender-parameters*/}

* `id`: `<Profiler>` ট্রিয়ের স্ট্রিং `id` প্রপ যা মাত্র কমিট করেছে। এটা আপনাকে জানায় যে ট্রিয়ের কোন অংশ কমিট করেছে যদি আপনি একাধিক প্রোফাইলার ব্যবহার করে থাকেন।
* `phase`: `"mount"`, `"update"` অথবা `"nested-update"`। এটা আপনাকে জানায় যে ট্রি কি এই প্রথম মাউন্ট হল নাকি prop, state বা hook এর পরিবর্তনের কারণে পরিবর্তিত হয়েছে।
* `actualDuration`: বর্তমান আপডেটে `<Profiler>` এবং এর উত্তরসূরিদের রেন্ডার করতে যত মিলিসেকেন্ড খরচ হয়েছে সেই সংখ্যাটা। এটা নির্দেশ করে সাবট্রি মেমোইজেশন কতটা ভালভাবে ব্যবহার করতে পারছে (উদাহরণস্বরূপঃ [`memo`](/reference/react/memo) এবং [`useMemo`](/reference/react/useMemo))। সব ঠিকঠাক থাকলে এই মানটা প্রথম মাউন্টের পর থেকে উল্লেখজনকভাবে কমে যাবার কথা কারণ, উত্তরসূরিদের অনেকেরই পুনরায় রেন্ডার তখনই হবে যখন তাদের কোন prop এর পরিবর্তন হয়।
* `baseDuration`: এই সংখ্যাটা বোঝায় যে কোন অপটিমাইজেশন ছাড়া পুরো `<Profiler>` সাবট্রি আবার রেন্ডার করার জন্য আনুমানিক কত মিলিসেকেন্ড লাগবে। সর্বশেষ  রেন্ডারের জন্য ট্রিয়ের প্রতিটি কম্পোনেন্টে যে সময় লেগেছিল সেটা থেকে এই সংখ্যাটা হিসেব করা হয়। এই মানটা নির্দেশ করে রেন্ডারিং এর জন্য সবচেয়ে বেশি কত সময় লাগতে পারে (উদাহরণস্বরূপঃ একদম প্রাথমিক মাউন্ট বা মেমোইজেশন ছাড়া মাউন্টের সময়)। এর সাথে `actualDuration` তুলনা করে আপনি বুঝতে পারবেন  মেমোইজেশন কাজ করছে কি না।
* `startTime`: একটা সাংখ্যিক টাইমস্ট্যাম্প যা নির্দেশ করে কখন React বর্তমান আপডেট শুরু করেছিল।
* `endTime`: একটা সাংখ্যিক টাইমস্ট্যাম্প যা নির্দেশ করে কখন React বর্তমান আপডেট commit করেছে। এই মানটা একটা কমিটে সব প্রোফাইলের সাথে শেয়ার করা হয়। যা তাদের প্রয়োজনে গ্রুপ হবার সুযোগ দেয়।

---

## ব্যবহার {/*usage*/}

### প্রোগামেটিকালি রেন্ডারিং পারফরম্যান্সের পরিমাপ {/*measuring-rendering-performance-programmatically*/}

একটা React ট্রিয়ের রেন্ডারিং পারফরম্যান্স পরিমাপ করার জন্য একে `<Profiler>` কম্পোনেন্ট দিয়ে wrap করে ফেলুন।

```js {2,4}
<App>
  <Profiler id="Sidebar" onRender={onRender}>
    <Sidebar />
  </Profiler>
  <PageContent />
</App>
```

এর দুটি প্রপ প্রয়োজন হয়ঃ একটা `id` (স্ট্রিং) এবং একটি `onRender` কলব্যাক (ফাংশন) which React calls any time a component within the tree "commits" an update.

<Pitfall>

Profiling adds some additional overhead, so **it is disabled in the production build by default.** To opt into production profiling, you need to enable a [special production build with profiling enabled.](https://fb.me/react-profiling)
প্রোফাইলিং কিছু অতিরিক্ত overhead যুক্ত করে, তাই **বাই ডিফল্ট এটা প্রোডাকশন বিল্ডে বন্ধ থাকে।** প্রোডাকশন প্রোফাইলিং চালু করার জন্য আপনাকে [প্রোফাইলিং সক্রিয় আছে এমন একটি বিশেষ প্রোডাকশন বিল্ড](https://fb.me/react-profiling) enable করতে হবে।

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

যদিও `<Profiler>` একটা হালকা কম্পোনেন্ট, এটা শুধুমাত্র তখনই ব্যবহার করা উচিত যখন প্রয়োজন পড়ছে। প্রতি বার ব্যবহারে অ্যাপের CPU এবং Memory এর উপরে অতিরিক্ত কিছু চাপ পড়ে।

---

