---
title: <StrictMode>
---


<Intro>

`<StrictMode>` আপনাকে ডেভেলপমেন্টের সময় আপনার কম্পোনেন্টগুলিতে সাধারণ বাগ সহজে খুঁজে পেতে সাহায্য করে।


```js
<StrictMode>
  <App />
</StrictMode>
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `<StrictMode>` {/*strictmode*/}

কম্পোনেন্ট ট্রির মধ্যে অতিরিক্ত ডেভেলপমেন্ট বিহেভিয়ার এবং সতর্কতা সক্রিয় করতে `StrictMode` ব্যবহার করুন:

```js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

[নীচে আরও উদাহরণ দেখুন।](#usage)

স্ট্রিক্ট মোড নিম্নলিখিত development-only আচরণগুলি সক্রিয় করে:

- আপনার কম্পোনেন্টগুলি impure রেন্ডারিং এর কারণে সৃষ্ট বাগ খুঁজে পেতে [একটি অতিরিক্ত সময় রেন্ডার হবে](#fixing-bugs-found-by-double-rendering-in-development)।
- আপনার কম্পোনেন্টগুলি ইফেক্ট ক্লিনআপ মিস হবার কারণে সৃষ্ট বাগ খুঁজে পেতে [ইফেক্টগুলি একটি অতিরিক্ত সময় চালাবে](#fixing-bugs-found-by-re-running-effects-in-development)।
- আপনার কম্পোনেন্টগুলি [deprecated এপিআই ব্যবহারের জন্য পরীক্ষা করা হবে](#fixing-deprecation-warnings-enabled-by-strict-mode)।

#### প্রপ্স {/*props*/}

`StrictMode` কোনো প্রপ গ্রহণ করে না।

#### সাবধানতা {/*caveats*/}

* `<StrictMode>`-এ মোড়ানো একটি ট্রিতে স্ট্রিক্ট মোড থেকে বের হওয়ার কোনো উপায় নেই। এটি আপনাকে এই আশ্বাস দেয় যে `<StrictMode>`-এর মধ্যে সমস্ত কম্পোনেন্ট পরীক্ষিত হয়েছে। যদি দুটি টিম যারা একটি পণ্য নিয়ে কাজ করছে তারা এই চেকগুলির দরকার নিয়ে একমত না হয়, তাদের হয় সমঝোতায় পৌঁছাতে হবে অথবা ট্রিতে `<StrictMode>` নিচে সরিয়ে নিতে হবে।

---

## ব্যবহার {/*usage*/}

### পুরো অ্যাপের জন্য স্ট্রিক্ট মোড সক্রিয় করা {/*enabling-strict-mode-for-entire-app*/}

`<StrictMode>` কম্পোনেন্টের মধ্যে থাকা পুরো কম্পোনেন্ট ট্রির জন্য স্ট্রিক্ট মোড অতিরিক্ত development-only চেক সক্রিয় করে। এই চেকগুলি আপনাকে ডেভেলপমেন্ট প্রক্রিয়ার শুরুতেই আপনার কম্পোনেন্টগুলিতে সাধারণ বাগগুলি খুঁজে পেতে সাহায্য করে।


আপনার পুরো অ্যাপের জন্য স্ট্রিক্ট মোড সক্রিয় করতে, এটি রেন্ডার করার সময় আপনার রুট কম্পোনেন্টকে `<StrictMode>` দিয়ে মোড়ান:

```js {6,8}
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

আমরা সুপারিশ করি যে আপনার পুরো অ্যাপটি স্ট্রিক্ট মোডে wrap করুন, বিশেষ করে নতুন তৈরি করা অ্যাপের ক্ষেত্রে। যদি আপনি কোনো ফ্রেমওয়ার্ক ব্যবহার করেন যা আপনার জন্য [`createRoot`](/reference/react-dom/client/createRoot) কল করে, তাহলে স্ট্রিক্ট মোড সক্রিয় করার জন্য তার ডকুমেন্টেশন দেখুন।

যদিও স্ট্রিক্ট মোড চেকগুলি **কেবল ডেভেলপমেন্টে চালানো হয়,** এগুলো আপনাকে সেই বাগগুলি খুঁজে পেতে সাহায্য করে যা আপনার কোডে ইতোমধ্যেই বিদ্যমান কিন্তু প্রডাকশনে নির্ভরযোগ্যভাবে reproduce করা কঠিন। স্ট্রিক্ট মোড আপনাকে বাগগুলি আপনার ব্যবহারকারীরা রিপোর্ট করার আগেই ঠিক করতে দেয়।

<Note>

স্ট্রিক্ট মোড নিম্নলিখিত চেক গুলো ডেভেলপমেন্টে সক্রিয় করে:

- আপনার কম্পোনেন্টগুলি impure রেন্ডারিং এর কারণে সৃষ্ট বাগ খুঁজে পেতে [একটি অতিরিক্ত সময় রেন্ডার হবে](#fixing-bugs-found-by-double-rendering-in-development)।
- আপনার কম্পোনেন্টগুলি ইফেক্ট ক্লিনআপ মিস হবার কারণে সৃষ্ট বাগ খুঁজে পেতে [ইফেক্টগুলি একটি অতিরিক্ত সময় চালাবে](#fixing-bugs-found-by-re-running-effects-in-development)।
- আপনার কম্পোনেন্টগুলি [deprecated এপিআই ব্যবহারের জন্য পরীক্ষা করা হবে](#fixing-deprecation-warnings-enabled-by-strict-mode)।

**এই সকল চেক শুধুমাত্র ডেভেলপমেন্টেই কাজ করে এবং প্রডাকশন বিল্ডে কোন প্রভাব ফেলে না।**

</Note>

---

### অ্যাপের একটি অংশের জন্য স্ট্রিক্ট মোড সক্রিয় করা {/*enabling-strict-mode-for-a-part-of-the-app*/}

আপনি আপনার অ্যাপ্লিকেশনের যেকোন অংশের জন্যও স্ট্রিক্ট মোড সক্রিয় করতে পারেনঃ

```js {7,12}
import { StrictMode } from 'react';

function App() {
  return (
    <>
      <Header />
      <StrictMode>
        <main>
          <Sidebar />
          <Content />
        </main>
      </StrictMode>
      <Footer />
    </>
  );
}
```

এই উদাহরণে, Strict Mode চেকগুলি `Header` এবং `Footer` কম্পোনেন্টগুলির বিরুদ্ধে চালানো হবে না। তবে, `Sidebar` এবং `Content`, সেই সাথে তাদের ভেতরে থাকা সমস্ত কম্পোনেন্টগুলিতে, যত গভীরেই হোক না কেন, চেকগুলি চালানো হবে।

---

### ডেভেলপমেন্টে ডাবল রেন্ডারিং দ্বারা পাওয়া বাগ ঠিক করা {/*fixing-bugs-found-by-double-rendering-in-development*/}

[React ধরে নেয় যে আপনি যেকোনো কম্পোনেন্ট লিখেন সেটি একটি পিওর ফাংশন হয়।](/learn/keeping-components-pure) এর অর্থ হলো React আপনার লিখিত কম্পোনেন্টগুলি সর্বদা একই ইনপুট (প্রপস, স্টেট এবং কনটেক্স্ট) দেওয়া হলে একই JSX রিটার্ন করবে।

এই নিয়ম ভঙ্গ করা কম্পোনেন্টগুলি অনির্দিষ্টভাবে আচরণ করে এবং বাগ তৈরি করে। আপনাকে আকস্মিকভাবে অশুদ্ধ কোড খুঁজে পেতে সাহায্য করার জন্য, Strict Mode ডেভেলপমেন্টে **আপনার কিছু ফাংশনকে দুবার কল করে**। এতে অন্তর্ভুক্ত:

- আপনার কম্পোনেন্ট ফাংশন বডি (শুধুমাত্র টপ-লেভেল লজিক, তাই এতে ইভেন্ট হ্যান্ডলারের ভেতরের কোড অন্তর্ভুক্ত নয়)
- সে সকল ফাংশন যা আপনি [`useState`](/reference/react/useState), [`set` ফাংশনগুলি](/reference/react/useState#setstate), [`useMemo`](/reference/react/useMemo), অথবা [`useReducer`](/reference/react/useReducer)-এ পাস করেন
- ক্লাস কম্পোনেন্টের কিছু মেথড যেমন [`constructor`](/reference/react/Component#constructor), [`render`](/reference/react/Component#render), [`shouldComponentUpdate`](/reference/react/Component#shouldcomponentupdate) ([পুরো তালিকা দেখুন](https://reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects))

যদি কোনো ফাংশন পিওর হয়, তাহলে এটি দুবার চালানোর ফলে এর আচরণ পরিবর্তন হয় না কারণ একটি পিওর ফাংশন প্রতিবারই একই ফলাফল উৎপন্ন করে। তবে, যদি কোনো ইমপিওর হয় (উদাহরণস্বরূপ, এটি প্রাপ্ত ডেটা পরিবর্তন করে), তাহলে এটি দু'বার চালানো সাধারণত লক্ষ্যণীয় হয় (এটাই এটিকে ইমপিওর করে!) এটি আপনাকে বাগটি শীঘ্রই চিহ্নিত করতে এবং ঠিক করতে সাহায্য করে।

**এখানে একটি উদাহরণ দেওয়া হয়েছে যা দেখায় কিভাবে Strict Mode-এ ডাবল রেন্ডারিং আপনাকে বাগগুলি শীঘ্রই খুঁজে পেতে সাহায্য করে।**

এই `StoryTray` কম্পোনেন্টটি `stories` নামের একটি অ্যারে নেয় এবং শেষে একটি "Create Story" আইটেম যোগ করে:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

```js src/App.js
import { useState } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState(initialStories)
  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <StoryTray stories={stories} />
    </div>
  );
}
```

```js src/StoryTray.js active
export default function StoryTray({ stories }) {
  const items = stories;
  items.push({ id: 'create', label: 'Create Story' });
  return (
    <ul>
      {items.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
  height: 100%;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

উপরের কোডে একটি ভুল রয়েছে। তবে, এটি লক্ষ্য করা সহজ নয় কারণ প্রাথমিক আউটপুট সঠিক মনে হয়।

এই ভুলটি আরও প্রকট হবে যদি `StoryTray` কম্পোনেন্টটি একাধিকবার রি-রেন্ডার হয়। উদাহরণস্বরূপ, চলুন দেখি যদি `StoryTray` আপনি এর উপরে মাউস নিয়ে গেলে ব্যাকগ্রাউন্ডের রঙ পরিবর্তন হয়ে পুনরায় রেন্ডার হয়:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

```js src/App.js
import { useState } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState(initialStories)
  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <StoryTray stories={stories} />
    </div>
  );
}
```

```js src/StoryTray.js active
import { useState } from 'react';

export default function StoryTray({ stories }) {
  const [isHover, setIsHover] = useState(false);
  const items = stories;
  items.push({ id: 'create', label: 'Create Story' });
  return (
    <ul
      onPointerEnter={() => setIsHover(true)}
      onPointerLeave={() => setIsHover(false)}
      style={{
        backgroundColor: isHover ? '#ddd' : '#fff'
      }}
    >
      {items.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
  height: 100%;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

লক্ষ করুন যে প্রতিবার আপনি `StoryTray` কম্পোনেন্টের উপরে মাউস নিয়ে যান, "Create Story" পুনরায় তালিকায় যোগ হচ্ছে। কোডের উদ্দেশ্য ছিল এটি একবার শেষে যোগ করা। কিন্তু `StoryTray` সরাসরি প্রপস থেকে `stories` অ্যারেটি পরিবর্তন করে। প্রতিবার `StoryTray` রেন্ডার হলে, এটি "Create Story" আবার সেই একই অ্যারের শেষে যোগ করে। অর্থাৎ, `StoryTray` একটি পিওর ফাংশন নয়—এটি বারবার চালানো হলে ভিন্ন ভিন্ন ফলাফল দেখা যায়।

এই সমস্যাটি ঠিক করতে, আপনি অ্যারের একটি কপি তৈরি করতে পারেন, এবং মূল অ্যারের পরিবর্তে সেই কপিটি পরিবর্তন করুন:

```js {2}
export default function StoryTray({ stories }) {
  const items = stories.slice(); // Clone the array
  // ✅ Good: Pushing into a new array
  items.push({ id: 'create', label: 'Create Story' });
```

এটি [করলে `StoryTray` ফাংশনটি পিওর হবে।](/learn/keeping-components-pure) প্রতিবার এটি কল হলে, এটি শুধু অ্যারের একটি নতুন কপি পরিবর্তন করবে, এবং কোনো বাহ্যিক অবজেক্ট বা ভেরিয়েবলে প্রভাব ফেলবে না। এটি বাগটি সমাধান করে, কিন্তু আপনাকে কম্পোনেন্টটি আরও প্রায়ই রি-রেন্ডার করতে হয়েছে যাতে বোঝা যায় যে এর আচরণে কিছু ভুল আছে।

**মূল উদাহরণে, বাগটি স্পষ্ট ছিল না। এখন চলুন মূল (বাগযুক্ত) কোডটি `<StrictMode>`-এ রাখি:**

<Sandpack>

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js src/App.js
import { useState } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState(initialStories)
  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <StoryTray stories={stories} />
    </div>
  );
}
```

```js src/StoryTray.js active
export default function StoryTray({ stories }) {
  const items = stories;
  items.push({ id: 'create', label: 'Create Story' });
  return (
    <ul>
      {items.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
  height: 100%;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

**স্ট্রিক্ট মোড *সর্বদা* আপনার রেন্ডারিং ফাংশনটি দুইবার কল করে, তাই আপনি সহজেই ভুলটি দেখতে পাবেন** ("Create Story" দুইবার প্রদর্শিত হয়)। এটি আপনাকে প্রক্রিয়ার শুরুতেই এমন ভুলগুলি লক্ষ্য করতে সাহায্য করে। আপনি যখন আপনার কম্পোনেন্টকে স্ট্রিক্ট মোডে রেন্ডার করার জন্য সংশোধন করেন, তখন আপনি *এছাড়াও* অনেক সম্ভাব্য ভবিষ্যতের প্রোডাকশন বাগগুলি ঠিক করেন যেমন আগের হভার ফাংশনালিটি:

<Sandpack>

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js src/App.js
import { useState } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState(initialStories)
  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <StoryTray stories={stories} />
    </div>
  );
}
```

```js src/StoryTray.js active
import { useState } from 'react';

export default function StoryTray({ stories }) {
  const [isHover, setIsHover] = useState(false);
  const items = stories.slice(); // Clone the array
  items.push({ id: 'create', label: 'Create Story' });
  return (
    <ul
      onPointerEnter={() => setIsHover(true)}
      onPointerLeave={() => setIsHover(false)}
      style={{
        backgroundColor: isHover ? '#ddd' : '#fff'
      }}
    >
      {items.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
  height: 100%;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

স্ট্রিক্ট মোড ছাড়া, আরও রি-রেন্ডার যোগ করা পর্যন্ত বাগটি লক্ষ্য করা সহজ ছিল না। স্ট্রিক্ট মোড একই বাগটি সঙ্গে সঙ্গে প্রদর্শন করেছে। স্ট্রিক্ট মোড আপনাকে আপনার টিম এবং আপনার ব্যবহারকারীদের কাছে বাগগুলি প্রকাশ করার আগে তা খুঁজে পেতে সাহায্য করে।

[কম্পোনেন্টগুলিকে পিওর রাখা সম্পর্কে আরও জানুন।](/learn/keeping-components-pure)

<Note>

আপনার যদি [React DevTools](/learn/react-developer-tools) ইনস্টল করা থাকে, তাহলে দ্বিতীয় রেন্ডার কলের সময় যেকোনো `console.log` কলগুলি সামান্য ম্লান দেখাবে। React DevTools একটি সেটিংও অফার করে (ডিফল্ট অনুযায়ী বন্ধ) যা এগুলিকে সম্পূর্ণভাবে দমন করতে পারে।

</Note>

---

### ডেভেলপমেন্টে ইফেক্টস পুনরায় চালানোর মাধ্যমে পাওয়া বাগগুলি সংশোধন করা {/*fixing-bugs-found-by-re-running-effects-in-development*/}

স্ট্রিক্ট মোড [ইফেক্টসে](/learn/synchronizing-with-effects) বাগ খুঁজে পেতেও সাহায্য করতে পারে।

প্রতিটি ইফেক্টের কিছু সেটআপ কোড থাকে এবং কিছু ক্লিনআপ কোড থাকতে পারে। সাধারণত, React কম্পোনেন্ট *মাউন্ট* হলে (স্ক্রিনে যোগ হলে) সেটআপ কল করে এবং কম্পোনেন্ট *আনমাউন্ট* হলে (স্ক্রিন থেকে সরানো হলে) ক্লিনআপ কল করে। তারপর React তার ডিপেন্ডেন্সি গত রেন্ডার থেকে পরিবর্তিত হলে ক্লিনআপ এবং সেটআপ পুনরায় কল করে।

স্ট্রিক্ট মোড চালু থাকলে, React ডেভেলপমেন্টের জন্য প্রতিটি ইফেক্টের জন্য **একটি অতিরিক্ত সেটআপ+ক্লিনআপ চক্র চালাবে।** এটি হয়তো অবাক করে দিতে পারে, কিন্তু এটি সূক্ষ্ম বাগগুলি খুঁজে পেতে সাহায্য করে যা ম্যানুয়ালি ধরা কঠিন।

**এখানে একটি উদাহরণ রয়েছে যা দেখায় যে স্ট্রিক্ট মোডে ইফেক্টস পুনরায় চালানো কীভাবে আপনাকে বাগ খুঁজে পেতে সাহায্য করে।**

এই উদাহরণটি বিবেচনা করুন যা একটি কম্পোনেন্টকে একটি চ্যাটে সংযুক্ত করে:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';
const roomId = 'general';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
  }, []);
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js src/chat.js
let connections = 0;

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      connections++;
      console.log('Active connections: ' + connections);
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
      connections--;
      console.log('Active connections: ' + connections);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

এই কোডে একটি সমস্যা রয়েছে, কিন্তু এটি সঙ্গে সঙ্গে স্পষ্ট নাও হতে পারে।

সমস্যাটি আরও স্পষ্ট করতে, চলুন একটি ফিচার বাস্তবায়ন করি। নীচের উদাহরণে, `roomId` হার্ডকোডেড নয়। বরং, ব্যবহারকারী ড্রপডাউন থেকে তারা যে `roomId`-এ সংযুক্ত হতে চান তা নির্বাচন করতে পারে। "Open chat" ক্লিক করুন এবং তারপর একে একে বিভিন্ন চ্যাট রুম নির্বাচন করুন। কনসোলে সক্রিয় সংযোগের সংখ্যা লক্ষ্য রাখুন:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [show, setShow] = useState(false);
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <button onClick={() => setShow(!show)}>
        {show ? 'Close chat' : 'Open chat'}
      </button>
      {show && <hr />}
      {show && <ChatRoom roomId={roomId} />}
    </>
  );
}
```

```js src/chat.js
let connections = 0;

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      connections++;
      console.log('Active connections: ' + connections);
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
      connections--;
      console.log('Active connections: ' + connections);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

আপনি লক্ষ্য করবেন যে খোলা সংযোগের সংখ্যা সর্বদা বাড়তে থাকে। একটি বাস্তব অ্যাপে, এটি পারফরম্যান্স এবং নেটওয়ার্ক সমস্যা তৈরি করবে। সমস্যাটি হলো [আপনার ইফেক্টে একটি ক্লিনআপ ফাংশনের অভাব:](/learn/synchronizing-with-effects#step-3-add-cleanup-if-needed)

```js {4}
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);
```

এখন যেহেতু আপনার ইফেক্ট "নিজের ঝামেলা পরিষ্কার" করে এবং পুরানো সংযোগগুলি ধ্বংস করে, লিক সমাধান হয়েছে। তবে, লক্ষ্য করুন যে সমস্যাটি তখনই প্রকাশ পেল যখন আপনি আরও বৈশিষ্ট্য (সিলেক্ট বক্স) যোগ করেছেন।

**In the original example, the bug wasn't obvious. Now let's wrap the original (buggy) code in `<StrictMode>`:**

<Sandpack>

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';
const roomId = 'general';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
  }, []);
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js src/chat.js
let connections = 0;

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      connections++;
      console.log('Active connections: ' + connections);
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
      connections--;
      console.log('Active connections: ' + connections);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

**With Strict Mode, you immediately see that there is a problem** (the number of active connections jumps to 2). Strict Mode runs an extra setup+cleanup cycle for every Effect. This Effect has no cleanup logic, so it creates an extra connection but doesn't destroy it. This is a hint that you're missing a cleanup function.

Strict Mode lets you notice such mistakes early in the process. When you fix your Effect by adding a cleanup function in Strict Mode, you *also* fix many possible future production bugs like the select box from before:

<Sandpack>

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [show, setShow] = useState(false);
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <button onClick={() => setShow(!show)}>
        {show ? 'Close chat' : 'Open chat'}
      </button>
      {show && <hr />}
      {show && <ChatRoom roomId={roomId} />}
    </>
  );
}
```

```js src/chat.js
let connections = 0;

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      connections++;
      console.log('Active connections: ' + connections);
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
      connections--;
      console.log('Active connections: ' + connections);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Notice how the active connection count in the console doesn't keep growing anymore.

Without Strict Mode, it was easy to miss that your Effect needed cleanup. By running *setup → cleanup → setup* instead of *setup* for your Effect in development, Strict Mode made the missing cleanup logic more noticeable.

[Read more about implementing Effect cleanup.](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development)

---

### Fixing deprecation warnings enabled by Strict Mode {/*fixing-deprecation-warnings-enabled-by-strict-mode*/}

React warns if some component anywhere inside a `<StrictMode>` tree uses one of these deprecated APIs:

* [`findDOMNode`](/reference/react-dom/findDOMNode). [See alternatives.](https://reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)
* `UNSAFE_` class lifecycle methods like [`UNSAFE_componentWillMount`](/reference/react/Component#unsafe_componentwillmount). [See alternatives.](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#migrating-from-legacy-lifecycles) 
* Legacy context ([`childContextTypes`](/reference/react/Component#static-childcontexttypes), [`contextTypes`](/reference/react/Component#static-contexttypes), and [`getChildContext`](/reference/react/Component#getchildcontext)). [See alternatives.](/reference/react/createContext)
* Legacy string refs ([`this.refs`](/reference/react/Component#refs)). [See alternatives.](https://reactjs.org/docs/strict-mode.html#warning-about-legacy-string-ref-api-usage)

These APIs are primarily used in older [class components](/reference/react/Component) so they rarely appear in modern apps.



==============================
==============================
==============================





এই কোডে একটি সমস্যা রয়েছে, কিন্তু এটি সঙ্গে সঙ্গে পরিষ্কার নাও হতে পারে।






===============================
===============================
===============================

When Strict Mode is on, React will also run **one extra setup+cleanup cycle in development for every Effect.** This may feel surprising, but it helps reveal subtle bugs that are hard to catch manually.

**Here is an example to illustrate how re-running Effects in Strict Mode helps you find bugs early.**

Consider this example that connects a component to a chat:


There is an issue with this code, but it might not be immediately clear.

To make the issue more obvious, let's implement a feature. In the example below, `roomId` is not hardcoded. Instead, the user can select the `roomId` that they want to connect to from a dropdown. Click "Open chat" and then select different chat rooms one by one. Keep track of the number of active connections in the console:

You'll notice that the number of open connections always keeps growing. In a real app, this would cause performance and network problems. The issue is that [your Effect is missing a cleanup function:](/learn/synchronizing-with-effects#step-3-add-cleanup-if-needed)

Now that your Effect "cleans up" after itself and destroys the outdated connections, the leak is solved. However, notice that the problem did not become visible until you've added more features (the select box).

**In the original example, the bug wasn't obvious. Now let's wrap the original (buggy) code in `<StrictMode>`:**

**With Strict Mode, you immediately see that there is a problem** (the number of active connections jumps to 2). Strict Mode runs an extra setup+cleanup cycle for every Effect. This Effect has no cleanup logic, so it creates an extra connection but doesn't destroy it. This is a hint that you're missing a cleanup function.

Strict Mode lets you notice such mistakes early in the process. When you fix your Effect by adding a cleanup function in Strict Mode, you *also* fix many possible future production bugs like the select box from before:

Notice how the active connection count in the console doesn't keep growing anymore.

Without Strict Mode, it was easy to miss that your Effect needed cleanup. By running *setup → cleanup → setup* instead of *setup* for your Effect in development, Strict Mode made the missing cleanup logic more noticeable.

[Read more about implementing Effect cleanup.](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development)

---

### Fixing deprecation warnings enabled by Strict Mode {/*fixing-deprecation-warnings-enabled-by-strict-mode*/}

React warns if some component anywhere inside a `<StrictMode>` tree uses one of these deprecated APIs:

* [`findDOMNode`](/reference/react-dom/findDOMNode). [See alternatives.](https://reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)
* `UNSAFE_` class lifecycle methods like [`UNSAFE_componentWillMount`](/reference/react/Component#unsafe_componentwillmount). [See alternatives.](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#migrating-from-legacy-lifecycles) 
* Legacy context ([`childContextTypes`](/reference/react/Component#static-childcontexttypes), [`contextTypes`](/reference/react/Component#static-contexttypes), and [`getChildContext`](/reference/react/Component#getchildcontext)). [See alternatives.](/reference/react/createContext)
* Legacy string refs ([`this.refs`](/reference/react/Component#refs)). [See alternatives.](https://reactjs.org/docs/strict-mode.html#warning-about-legacy-string-ref-api-usage)

These APIs are primarily used in older [class components](/reference/react/Component) so they rarely appear in modern apps.






---






