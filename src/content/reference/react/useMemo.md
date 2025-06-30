---
title: useMemo
---

<Intro>

`useMemo` হল একটি React হুক, যা আপনাকে রি-রেন্ডারিং এর সময় কোনো রেজাল্ট মেমোরিতে সংরক্ষণ করে রাখতে দেয়।

```js
const cachedValue = useMemo(calculateValue, dependencies)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `useMemo(calculateValue, dependencies)` {/*usememo*/}

`useMemo` কে আপনার কম্পোনেন্ট এর একেবারে উপরের স্তরে কল করতে হবে যাতে রি-রেন্ডারগুলির মধ্যে রেজাল্ট cache সংরক্ষণ করা যায।

```js
import { useMemo } from 'react';

function TodoList({ todos, tab }) {
  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab]
  );
  // ...
}
```

[নীচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটারস {/*parameters*/}

* `calculateValue`: এটি এমন একটি ফাংশন যা আপনি cache করতে চান তার ভ্যালু ক্যালকুলেট করে। এটি পিওর হওয়া উচিত, কোনো আর্গুমেন্ট নেওয়া উচিত না, এবং যেকোনো টাইপের একটি ভ্যালু রিটার্ন করা উচিত। React প্রাথমিক রেন্ডারের সময় আপনার ফাংশন কল করবে। পরবর্তী রেন্ডারগুলিতে, যদি dependencies শেষ রেন্ডার থেকে পরিবর্তিত না হয়, তাহলে একই মান আবার রিটার্ন করবে। অন্যথায়, এটি `calculateValue`, কল করবে, এর ফলাফল রিটার্ন করবে, এবং তা সংরক্ষণ করবে যাতে পরবর্তীতে পুনরায় ব্যবহার করা যায়।

* `dependencies`: এটি হল `calculateValue` কোডের ভিতরে উল্লেখিত সকল রিঅ্যাক্টিভ মানের একটি রেফারেন্স। রিঅ্যাক্টিভ মান অন্তর্ভুক্ত করে props, state, এবং আপনার কম্পোনেন্ট বডির ভিতরে ভেরিয়েবল এবং ফাংশন সরাসরি ডিক্লেয়ার করে। যদি আপনার লিন্টার [React এর জন্য কনফিগার](/learn/editor-setup#linting) করা থাকে, তাহলে এটি প্রতিটি রিঅ্যাক্টিভ মান সঠিকভাবে ডিপেন্ডেন্সিস নির্দিষ্ট করা আছে কিনা তা যাচাই করবে। ডিপেন্ডেন্সিগুলির তালিকায় একটি স্থির সংখ্যক আইটেম থাকতে হবে এবং এটি ইনলাইনে লেখা হতে হবে যেমন [dep1, dep2, dep3]। React প্রতিটি ডিপেন্ডেন্সি তার আগের মানের সাথে [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) comparison ব্যবহার করে তুলনা করবে।

#### রিটার্নস {/*returns*/}

প্রাথমিক রেন্ডারে, `useMemo` কোনো আর্গুমেন্ট ছাড়াই `calculateValue` কল করে এবং ফলাফল রিটার্ন করে।

পরবর্তী রেন্ডারগুলিতে, এটি হয় শেষ রেন্ডার থেকে ইতিমধ্যে সংরক্ষিত একটি মান রিটার্ন করবে (যদি ডিপেন্ডেন্সিসগুলি পরিবর্তিত না হয়ে থাকে), অথবা আবার `calculateValue` কল করবে, এবং `calculateValue` যে ফলাফল দিয়েছে সেটি রিটার্ন করে দেবে।

#### সতর্কতা {/*caveats*/}

* `useMemo` একটি হুক, তাই আপনি এটি শুধুমাত্র **আপনার কম্পোনেন্টের শীর্ষ স্তরে** অথবা আপনার নিজের হুকগুলিতে কল করতে পারেন। আপনি এটি লুপ অথবা কন্ডিশনের ভিতরে কল করতে পারবেন না। যদি আপনার এমন প্রয়োজন হয়, তাহলে একটি নতুন কম্পোনেন্ট তৈরি করে স্টেটটি তাতে সরিয়ে নিন।
* Strict Mode এ, React **আপনার calculation ফাংশনটি দুইবার কল করবে** [যাতে আপনার আকস্মিক ভুল খুঁজে পেতে সাহায্য হয়](#my-calculation-runs-twice-on-every-re-render)। এটি শুধুমাত্র ডেভেলপমেন্ট-এর জন্য এবং প্রোডাকশনে কোনো প্রভাব ফেলবে না। যদি আপনার calculation ফাংশনটি পিওর হয় (যেমনটি হওয়া উচিত), তাহলে এটি আপনার লজিকে কোনো প্রভাব ফেলা উচিত নয়। দুইবার কল করলেও একবার ফলাফল প্রেরণ করবে।
* React **ক্যাশে সংরক্ষিত মানটি ত্যাগ করবে না, যদি না তা করার বিশেষ কোনো কারণ থাকে**। উদাহরণস্বরূপ, ডেভেলপমেন্টে, যখন আপনি আপনার কম্পোনেন্টের ফাইল এডিট করেন, React ক্যাশে তখন তা ত্যাগ করে। ডেভেলপমেন্ট এবং প্রোডাকশন উভয়ক্ষেত্রেই, React ক্যাশে ত্যাগ করবে যদি আপনার কম্পোনেন্ট প্রাথমিক মাউন্টের সময় সাসপেন্ড হয়। ভবিষ্যতে, React আরও ফিচার যোগ করতে পারে যা ক্যাশে ত্যাগ করার সুবিধা নেয়—উদাহরণস্বরূপ, যদি React ভবিষ্যতে ভার্চুয়ালাইজড লিস্টের জন্য অন্তর্নির্মিত সমর্থন যোগ করে, তবে ভার্চুয়ালাইজড টেবিল ভিউপোর্ট থেকে স্ক্রল করে বাইরে যাওয়া আইটেমগুলির জন্য ক্যাশে ত্যাগ করা যুক্তিসঙ্গত হবে। এটি ঠিক থাকা উচিত যে আপনি যদি useMemo কে শুধুমাত্র একটি পারফরমেন্স অপটিমাইজেশন হিসেবে ব্যবহার করেন। নতুবা, একটি [স্টেট ভেরিয়েবল](/reference/react/useState#avoiding-recreating-the-initial-state) অথবা একটি [ref](/reference/react/useRef#avoiding-recreating-the-ref-contents) আরও উপযুক্ত হতে পারে। 

<Note>

এই ধরণের রিটার্ন মানগুলি cache সংরক্ষণ করাকে [*memoization*,](https://en.wikipedia.org/wiki/Memoization) বলা হয়, যার কারনে এই হুকের নাম রাখা হয়েছে `useMemo`।

</Note>

---

## ব্যবহারবিধি {/*usage*/}

### খরুচে recalculation এড়ানো {/*skipping-expensive-recalculations*/}

রি-রেন্ডারের মধ্যে একটি calculation cache সংরক্ষণ করতে, `useMemo` আপনার কম্পোনেন্টের শীর্ষ স্তরে কল করে রাখুন:

```js [[3, 4, "visibleTodos"], [1, 4, "() => filterTodos(todos, tab)"], [2, 4, "[todos, tab]"]]
import { useMemo } from 'react';

function TodoList({ todos, tab, theme }) {
  const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
  // ...
}
```

`useMemo` তে আপনাকে দুটি জিনিস পাস করতে হবে:

1. <CodeStep step={1}>calculation ফাংশন</CodeStep> যা কোনো আর্গুমেন্ট নেয় না, যেমন () =>, এবং আপনি যা calculation করতে চেয়েছিলেন তা রিটার্ন করে।
2. <CodeStep step={2}>ডিপেন্ডেন্সিস তালিকা</CodeStep> যা আপনার calculation এর ভিতরে ব্যবহৃত আপনার কম্পোনেন্টের প্রতিটি মান অন্তর্ভুক্ত করে।

প্রাথমিক রেন্ডারে, `useMemo` থেকে আপনি যে <CodeStep step={3}>মানটি</CodeStep> পাবেন তা আপনার <CodeStep step={1}>calculation</CodeStep> করার ফলাফল হবে।

প্রতিটি পরবর্তী রেন্ডারে, React <CodeStep step={2}>ডিপেন্ডেন্সিস</CodeStep> আগের রেন্ডারে আপনার পাস করা ডিপেন্ডেন্সিসগুলির সাথে তুলনা করবে। যদি ডিপেন্ডেন্সিসগুলির কোনোটিই পরিবর্তিত না হয় (যেমন [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) দিয়ে তুলনা করা), `useMemo` আগে আপনি যে মানটি calculation করেছিলেন তা রিটার্ন করে দেবে। অন্যথায়, React আপনার calculation টি পুনরায় চালাবে এবং নতুন মানটি রিটার্ন করবে।

অন্য কথায়, `useMemo` তার ডিপেন্ডেন্সিসগুলির পরিবর্তন না হওয়া পর্যন্ত রি-রেন্ডারের সময় এটি আগের calculation এর ফলাফল ক্যাশে সংরক্ষণ করে। In other words,

**একটি উদাহরণের মাধ্যমে আমরা দেখব এটির উপকারীতা**

ডিফল্ট হিসেবে, React প্রতিবার আপনার কম্পোনেন্টের সম্পূর্ণ বডি পুনরায় রান করবে যখন তা রি-রেন্ডার হবে। উদাহরণস্বরূপ, যদি এই `TodoList` এর স্টেট আপডেট হয় অথবা এটি তার প্যারেন্ট থেকে নতুন props পায়, তাহলে `filterTodos` ফাংশনটি পুনরায় রান করবে: 

```js {2}
function TodoList({ todos, tab, theme }) {
  const visibleTodos = filterTodos(todos, tab);
  // ...
}
```

সাধারণত, এটি একটি সমস্যা নয় কারণ বেশিরভাগ calculation খুবই দ্রুত হয়। তবে, যদি আপনি একটি বড় array ফিল্টার করছেন অথবা পরিবর্তন করছেন, অথবা কিছু এক্সপেন্সিভ calculation করেছেন, এবং আপনি চাইতেছেন ডেটা পরিবর্তিত না হলে এটি আবার রেন্ডার না হক। যদি `todos` এবং `tab` উভয়ই আগের রেন্ডারের যা ছিল তাই থাকে, তাহলে `useMemo` ব্যবহার করে আগের মতো calculation পেতে পারেন এবং আপনি `visibleTodo` পুনরায় ব্যবহার করে আগের calculation পেতে পারেন।

এই ধরণের ক্যাশিংকে *[মেমোইজেশন](https://en.wikipedia.org/wiki/Memoization)* বলা হয়।

<Note>

**আপনার কেবলমাত্র পারফরমেন্স অপ্টিমাইজেশন হিসেবে `useMemo` এর উপর নির্ভর করা উচিত।** যদি আপনার কোড এটি ছাড়া কাজ না করে, প্রথমে মূল সমস্যাটি খুঁজে বের করুন এবং তা ঠিক করুন। তারপর আপনি পারফরমেন্স উন্নতির জন্য `useMemo` ব্যবহার করতে পারেন।

</Note>

<DeepDive>

#### একটি calculation এক্সপেন্সিভ কিনা কিভাবে বুঝবেন? {/*how-to-tell-if-a-calculation-is-expensive*/}

সাধারণভাবে, আপনি যদি হাজার হাজার অবজেক্ট তৈরি করেন বা তাদের উপর লুপ চালান, তাহলে সম্ভবত এটি এক্সপেন্সিভ নয়। আপনি যদি আরও নিশ্চিত হতে চান, তাহলে আপনি একটি কোডের অংশে সময় পরিমাপ করতে console log যোগ করতে পারেন:

```js {1,3}
console.time('filter array');
const visibleTodos = filterTodos(todos, tab);
console.timeEnd('filter array');
```

আপনি যে ইন্টার‌্যাকশনটি পরিমাপ করছেন তা সম্পাদন করুন (উদাহরণ স্বরূপ, ইনপুটে টাইপ করা)। তারপর আপনি আপনার কনসোলে `filter array: 0.15ms` এর মতো লগ দেখতে পাবেন। যদি মোট লগ করা সময় উল্লেখযোগ্য পরিমাণে জমা হয় (ধরুন, `1ms` বা তার বেশি), তাহলে সেই calculation টি মেমোইজ করা যুক্তিসঙ্গত হতে পারে। একটি পরীক্ষা হিসেবে, আপনি তখন `useMemo` ব্যবহার করে সেই calculation টি পুনরায় যাচাই করতে পারেন যে সেই ইন্টার‌্যাকশনের জন্য মোট লগ করা সময় কমেছে কিনা:

```js
console.time('filter array');
const visibleTodos = useMemo(() => {
  return filterTodos(todos, tab); // Skipped if todos and tab haven't changed
}, [todos, tab]);
console.timeEnd('filter array');
```

`useMemo` প্রথম রেন্ডারটি দ্রুত করে না। এটি কেবল আপডেটে অপ্রয়োজনীয় কাজ এড়াতে আপনাকে সাহায্য করে।

মনে রাখবেন যে আপনার মেশিন সম্ভবত আপনার ব্যবহারকারীদের মেশিনের চেয়ে দ্রুততর, তাই কৃত্রিম ধীর গতির মাধ্যমে পারফরমেন্স পরীক্ষা করা একটি ভাল প্র্যাক্টিস। উদাহরণস্বরূপ, [CPU Throttling](https://developer.chrome.com/blog/new-in-devtools-61/#throttling) অপশন এই উদ্দেশ্যে অফার করে। 

আরও মনে রাখবেন যে ডেভেলপমেন্টে পারফরমেন্স আপনাকে সবচেয়ে সঠিক ফলাফল দেবে না। (উদাহরণ স্বরূপ, যখন [Strict Mode](/reference/react/StrictMode) চালু থাকে, আপনি প্রতিটি কম্পোনেন্ট একবারের বদলে দুবার রেন্ডার হতে দেখবেন।) সবচেয়ে সঠিক সময় পাওয়ার জন্য, আপনার অ্যাপটি প্রোডাকশনের জন্য বিল্ড করুন এবং এটি আপনার ব্যবহারকারীদের মতো একটি ডিভাইসে পরীক্ষা করুন।

</DeepDive>

<DeepDive>

#### আপনি কি সর্বত্র useMemo যোগ করবেন? {/*should-you-add-usememo-everywhere*/}

আপনার অ্যাপ যদি এই সাইটের মতো হয়, এবং বেশিরভাগ ইন্টার‌্যাকশন স্থূল (যেমন একটি পৃষ্ঠা বা একটি পুরো সেকশন রিপ্লেস করা) হয়, তাহলে সাধারণত মেমোইজেশন অপ্রয়োজনীয়। অন্যদিকে, যদি আপনার অ্যাপ একটি ড্রয়িং এডিটরের মতো হয়, এবং বেশিরভাগ ইন্টার‌্যাকশন সূক্ষ্ম (যেমন আকৃতি সরানো) হয়, তাহলে আপনি মেমোইজেশনকে খুবই উপকারী মনে করতে পারেন।

`useMemo` এর মাধ্যমে অপ্টিমাইজেশন কেবল কয়েকটি ক্ষেত্রে মূল্যবান:

- `useMemo` এ যে calculation টি আপনি করছেন তা লক্ষণীয়ভাবে ধীর, এবং এর ডিপেন্ডেন্সিসগুলি খুব কম পরিবর্তিত হয়।
- আপনি এটি একটি কম্পোনেন্টকে প্রপ হিসেবে পাস করেন যা [`memo`.](/reference/react/memo). দ্বারা মোড়ানো। মান যদি অপরিবর্তিত থাকে তাহলে আপনি কি-রেন্ডারিং এড়াতে চান। মেমোইজেশন আপনার কম্পোনেন্টকে শুধুমাত্র তখন রি-রেন্ডার করতে দেয় যখন ডিপেন্ডেন্সিসগুলি একই নয়।
- আপনি যে মানটি পাস করছেন তা পরবর্তীতে কোনো হুকের নির্ভরতা হিসেবে ব্যবহৃত হয়। উদাহরণস্বরূপ, হয়তো অন্য একটি `useMemo` calculation এর মান এটির উপর নির্ভর করে। অথবা হয়তো আপনি এই মানটির উপর [`useEffect.`](/reference/react/useEffect). থেকে নির্ভর করছেন।

অন্য ক্ষেত্রে, `useMemo` ব্যবহার করে কোনো calculation মোড়ানোর কোনো সুবিধা নেই। তবে করার ফলে কোনো গুরুতর ক্ষতি হয় না, তাই কিছু টিম প্রতিটি ক্ষেত্রে আলাদাভাবে চিন্তা না করে যতটা সম্ভব মেমোইজ করে। এই পদ্ধতির অসুবিধা হল কোড কম পাঠযোগ্য হয়। এছাড়া, সব মেমোইজেশন কার্যকর নয়: “সর্বদা নতুন” একটি একক মান পুরো কম্পোনেন্টের জন্য মেমোইজেশন ব্যর্থ করতে পারে।

**বাস্তবে, কিছু নীতি অনুসরণ করে আপনি অনেক মেমোইজেশন অপ্রয়োজনীয় করে তুলতে পারেন:**

1. যখন একটি কম্পোনেন্ট অন্যান্য কম্পোনেন্টকে দৃশ্যমানভাবে মোড়ানো, তখন এটিকে [JSX চাইল্ড হিসেবে গ্রহণ করতে দিন](/learn/passing-props-to-a-component#passing-jsx-as-children)। এইভাবে, যখন র‍্যাপার কম্পোনেন্ট নিজের স্টেট আপডেট করে, React জানে যে তার চাইল্ডগুলিকে পুনরায় রেন্ডার করা দরকার নেই। 
1. লোকাল স্টেটকে প্রাধান্য দিন এবং প্রয়োজনের চেয়ে বেশি [স্টেট উপরে পাঠানোর](/learn/sharing-state-between-components) দরকার নাই। উদাহরণ স্বরূপ, ফর্ম এবং কোনো আইটেম হোভার করা আছে কিনা এমন অস্থায়ী স্টেটকে আপনার ট্রির শীর্ষে বা কোনো গ্লোবাল স্টেট লাইব্রেরিতে রাখবেন না।
1. আপনার [রেন্ডারিং লজিককে পিওর রাখুন](/learn/keeping-components-pure)। যদি কোনো কম্পোনেন্ট পুনরায় রেন্ডার করার সময় কোনো সমস্যা হয় বা কোনো লক্ষণীয় দৃশ্যমান আর্টিফ্যাক্ট তৈরি হয়, তা আপনার কম্পোনেন্টে একটি বাগ! মেমোইজেশন যোগ করার পরিবর্তে বাগটি ঠিক করুন। 
1. [অপ্রয়োজনীয় এফেক্ট এড়িয়ে চলুন যা স্টেট আপডেট করে](/learn/you-might-not-need-an-effect)। React অ্যাপসে বেশিরভাগ পারফরমেন্স সমস্যা এমন এফেক্ট থেকে উদ্ভূত আপডেটের চেইনের কারণে হয়, যা আপনার কম্পোনেন্টগুলিকে বারবার রেন্ডার করতে বাধ্য করে।
1. [আপনার এফেক্ট থেকে অপ্রয়োজনীয় নির্ভরতাগুলি সরান।](/learn/removing-effect-dependencies) উদাহরণস্বরূপ, মেমোইজেশনের পরিবর্তে এটি প্রায়ই সহজতর হয় কিছু অবজেক্ট বা ফাংশনকে একটি এফেক্টের মধ্যে বা কম্পোনেন্টের বাইরে সরানো।

যদি কোনো নির্দিষ্ট ইন্টার‌্যাকশন ধীর মনে হয়, [React Developer Tools প্রোফাইলার ব্যবহার করে দেখুন কোন](https://legacy.reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html) কম্পোনেন্টগুলি মেমোইজেশন থেকে সর্বাধিক উপকৃত হবে, এবং প্রয়োজন অনুসারে মেমোইজেশন যোগ করুন। এই নীতিগুলি আপনার কম্পোনেন্টগুলিকে ডিবাগ এবং বোঝা সহজ করে তোলে, তাই যেকোনো ক্ষেত্রে এগুলি অনুসরণ করা ভালো। দীর্ঘ মেয়াদে, আমরা [সূক্ষ্ম মেমোইজেশন স্বয়ংক্রিয়ভাবে করা](https://www.youtube.com/watch?v=lGEMwh32soc) নিয়ে গবেষণা করছি যাতে একবারেই সবার জন্য এই সমস্যা সমাধান করা যায়।

</DeepDive>

<Recipes titleText="`useMemo` এবং সরাসরি মান calculation করার মধ্যে পার্থক্য" titleId="examples-recalculation">

#### `useMemo` এর মাধ্যমে পুনর্গণনা স্কিপিং করা {/*skipping-recalculation-with-usememo*/}

এই উদাহরণে, `filterTodos` বাস্তবায়নটি **কৃত্রিমভাবে স্লো করা হয়েছে** যাতে আপনি দেখতে পারেন যে রেন্ডারিং করার সময় আপনি যে সকল জাভাস্ক্রিপ্ট ফাংশন কল করেন তা যদি সত্যিকারের স্লো হয় তবে কী হয়। ট্যাব পরিবর্তন করে এবং থিম টগল করে দেখুন।

ট্যাব পরিবর্তন করার সময় স্লো মনে হবে কারণ `filterTodos` কে ইচ্ছা করেই স্লো করা হয়েছে যখন পুনরায় এক্সেকিউট করে। এটি প্রত্যাশিত কারণ tab পরিবর্তিত হয়েছে, এবং প্রয়োজন হবে পুরো calculation টি পুনরায় চালানো। (যদি আপনি কৌতুহলী হন যে এটি দুইবার কেন চলে, তার ব্যাখ্যা [এখানে দেওয়া হয়েছে।](#my-calculation-runs-twice-on-every-re-render))

থিম টগল করুন। `useMemo` এর ধন্যবাদ, কৃত্রিম ধীরতার পরেও এটি দ্রুত! ধীর `filterTodos` কলটি এড়ানো হয়েছে কারণ todos এবং tab (যা আপনি `useMemo`-এ ডিপেন্ডেন্সিস হিসেবে পাস করেছেন) আগের রেন্ডারের পর থেকে পরিবর্তিত হয়নি।

<Sandpack>

```js src/App.js
import { useState } from 'react';
import { createTodos } from './utils.js';
import TodoList from './TodoList.js';

const todos = createTodos();

export default function App() {
  const [tab, setTab] = useState('all');
  const [isDark, setIsDark] = useState(false);
  return (
    <>
      <button onClick={() => setTab('all')}>
        All
      </button>
      <button onClick={() => setTab('active')}>
        Active
      </button>
      <button onClick={() => setTab('completed')}>
        Completed
      </button>
      <br />
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Dark mode
      </label>
      <hr />
      <TodoList
        todos={todos}
        tab={tab}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}

```

```js src/TodoList.js active
import { useMemo } from 'react';
import { filterTodos } from './utils.js'

export default function TodoList({ todos, theme, tab }) {
  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab]
  );
  return (
    <div className={theme}>
      <p><b>Note: <code>filterTodos</code> is artificially slowed down!</b></p>
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ?
              <s>{todo.text}</s> :
              todo.text
            }
          </li>
        ))}
      </ul>
    </div>
  );
}
```

```js src/utils.js
export function createTodos() {
  const todos = [];
  for (let i = 0; i < 50; i++) {
    todos.push({
      id: i,
      text: "Todo " + (i + 1),
      completed: Math.random() > 0.5
    });
  }
  return todos;
}

export function filterTodos(todos, tab) {
  console.log('[ARTIFICIALLY SLOW] Filtering ' + todos.length + ' todos for "' + tab + '" tab.');
  let startTime = performance.now();
  while (performance.now() - startTime < 500) {
    // Do nothing for 500 ms to emulate extremely slow code
  }

  return todos.filter(todo => {
    if (tab === 'all') {
      return true;
    } else if (tab === 'active') {
      return !todo.completed;
    } else if (tab === 'completed') {
      return todo.completed;
    }
  });
}
```

```css
label {
  display: block;
  margin-top: 10px;
}

.dark {
  background-color: black;
  color: white;
}

.light {
  background-color: white;
  color: black;
}
```

</Sandpack>

<Solution />

#### সবসময় মান পুনরায় calculation করা {/*always-recalculating-a-value*/}

এই উদাহরণে, `filterTodos` বাস্তবায়নটি **কৃত্রিমভাবে স্লো করা হয়েছে** যাতে আপনি দেখতে পারেন রেন্ডারিং এর সময় আপনার কল করা কিছু জাভাস্ক্রিপ্ট ফাংশন যদি সত্যিকারের স্লো হয় তবে কি হয়। ট্যাব পরিবর্তন করে দেখুন এবং থিম টগল করুন।

আগের উদাহরণের মতো নয়, এখন থিম টগল করাও ধীর হয়ে গেছে! এটি কারণ **এই ভার্সনে `useMemo` কল নেই**, তাই কৃত্রিমভাবে স্লো করা `filterTodos` সব সময় রি-রেন্ডারে করা হচ্ছে। এমনকি শুধু `theme` পরিবর্তিত হলেও এটি কল করা হচ্ছে।

<Sandpack>

```js src/App.js
import { useState } from 'react';
import { createTodos } from './utils.js';
import TodoList from './TodoList.js';

const todos = createTodos();

export default function App() {
  const [tab, setTab] = useState('all');
  const [isDark, setIsDark] = useState(false);
  return (
    <>
      <button onClick={() => setTab('all')}>
        All
      </button>
      <button onClick={() => setTab('active')}>
        Active
      </button>
      <button onClick={() => setTab('completed')}>
        Completed
      </button>
      <br />
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Dark mode
      </label>
      <hr />
      <TodoList
        todos={todos}
        tab={tab}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}

```

```js src/TodoList.js active
import { filterTodos } from './utils.js'

export default function TodoList({ todos, theme, tab }) {
  const visibleTodos = filterTodos(todos, tab);
  return (
    <div className={theme}>
      <ul>
        <p><b>Note: <code>filterTodos</code> is artificially slowed down!</b></p>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ?
              <s>{todo.text}</s> :
              todo.text
            }
          </li>
        ))}
      </ul>
    </div>
  );
}
```

```js src/utils.js
export function createTodos() {
  const todos = [];
  for (let i = 0; i < 50; i++) {
    todos.push({
      id: i,
      text: "Todo " + (i + 1),
      completed: Math.random() > 0.5
    });
  }
  return todos;
}

export function filterTodos(todos, tab) {
  console.log('[ARTIFICIALLY SLOW] Filtering ' + todos.length + ' todos for "' + tab + '" tab.');
  let startTime = performance.now();
  while (performance.now() - startTime < 500) {
    // Do nothing for 500 ms to emulate extremely slow code
  }

  return todos.filter(todo => {
    if (tab === 'all') {
      return true;
    } else if (tab === 'active') {
      return !todo.completed;
    } else if (tab === 'completed') {
      return todo.completed;
    }
  });
}
```

```css
label {
  display: block;
  margin-top: 10px;
}

.dark {
  background-color: black;
  color: white;
}

.light {
  background-color: white;
  color: black;
}
```

</Sandpack>

তবে, এখানে একই কোড **কৃত্রিম স্লো অপসারণ করা হয়েছে।** দেখুনতো `useMemo` এর অভাবটি লক্ষণীয় মনে হয় কিনা?

<Sandpack>

```js src/App.js
import { useState } from 'react';
import { createTodos } from './utils.js';
import TodoList from './TodoList.js';

const todos = createTodos();

export default function App() {
  const [tab, setTab] = useState('all');
  const [isDark, setIsDark] = useState(false);
  return (
    <>
      <button onClick={() => setTab('all')}>
        All
      </button>
      <button onClick={() => setTab('active')}>
        Active
      </button>
      <button onClick={() => setTab('completed')}>
        Completed
      </button>
      <br />
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Dark mode
      </label>
      <hr />
      <TodoList
        todos={todos}
        tab={tab}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}

```

```js src/TodoList.js active
import { filterTodos } from './utils.js'

export default function TodoList({ todos, theme, tab }) {
  const visibleTodos = filterTodos(todos, tab);
  return (
    <div className={theme}>
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ?
              <s>{todo.text}</s> :
              todo.text
            }
          </li>
        ))}
      </ul>
    </div>
  );
}
```

```js src/utils.js
export function createTodos() {
  const todos = [];
  for (let i = 0; i < 50; i++) {
    todos.push({
      id: i,
      text: "Todo " + (i + 1),
      completed: Math.random() > 0.5
    });
  }
  return todos;
}

export function filterTodos(todos, tab) {
  console.log('Filtering ' + todos.length + ' todos for "' + tab + '" tab.');

  return todos.filter(todo => {
    if (tab === 'all') {
      return true;
    } else if (tab === 'active') {
      return !todo.completed;
    } else if (tab === 'completed') {
      return todo.completed;
    }
  });
}
```

```css
label {
  display: block;
  margin-top: 10px;
}

.dark {
  background-color: black;
  color: white;
}

.light {
  background-color: white;
  color: black;
}
```

</Sandpack>

প্রায়ই, মেমোইজেশন ছাড়া কোড ভালোভাবে কাজ করে। যদি আপনার ইন্টার‌্যাকশনগুলি যথেষ্ট দ্রুত হয়, তাহলে আপনার মেমোইজেশনের প্রয়োজন নাও হতে পারে।

আপনি `utils.js` এ টুডু আইটেমগুলির সংখ্যা বাড়িয়ে দেখতে পারেন এবং দেখতে পারেন আচরণ কিভাবে পরিবর্তিত হয়। এই নির্দিষ্ট calculation শুরু থেকেই খুব ব্যয়বহুল ছিল না, তবে যদি টুডুগুলির সংখ্যা উল্লেখযোগ্যভাবে বাড়ে, তবে বেশিরভাগ ওভারহেড ফিল্টারিংয়ের চেয়ে রি-রেন্ডারিংয়ে হবে। `useMemo` এর সাথে রি-রেন্ডারিং কিভাবে অপ্টিমাইজ করা যায় তা দেখতে নীচে পড়তে থাকুন।

<Solution />

</Recipes>

---

### কম্পোনেন্টের রি-রেন্ডারিং এড়িয়ে যাওয়া {/*skipping-re-rendering-of-components*/}

কিছু ক্ষেত্রে, `useMemo` আপনাকে চাইল্ড কম্পোনেন্টের রি-রেন্ডারিং এর পারফরমেন্স অপটিমাইজ করতে সাহায্য করতে পারে। এটি বোঝাতে, ধরা যাক এই `TodoList` কম্পোনেন্টটি `visibleTodos` কে একটি প্রপ হিসেবে চাইল্ড `List` কম্পোনেন্টকে পাস করে:

```js {5}
export default function TodoList({ todos, tab, theme }) {
  // ...
  return (
    <div className={theme}>
      <List items={visibleTodos} />
    </div>
  );
}
```

আপনি লক্ষ্য করেছেন যে `theme` প্রপ টগল করার সময় অ্যাপটি এক মুহূর্তের জন্য থেমে যায়, কিন্তু যদি আপনি JSX থেকে `<List />` সরিয়ে ফেলেন, তাহলে এটি দ্রুত মনে হয়। এটি জানান দেয় যে `List` কম্পোনেন্টটি অপটিমাইজ করার চেষ্টা করা যেতে পারে।

**ডিফল্ট হিসেবে, যখন একটি কম্পোনেন্ট রি-রেন্ডার হয়, React তার সমস্ত চাইল্ড কম্পোনেন্টকে পুনরায় রেন্ডার করে।** এই কারণে, যখন `TodoList` একটি ভিন্ন theme রি-রেন্ডার করে, `List` কম্পোনেন্টও রি-রেন্ডার হয়। রি-রেন্ডার হবার সময় বেশি calculation প্রয়োজন না হলে এটি সমস্যা নয়। কিন্তু যদি আপনি নিশ্চিত হন যে রি-রেন্ডার ধীর হচ্ছে, তাহলে আপনি `List` কে বলতে পারেন যে তার props আগের রেন্ডারের মতোই থাকলে রি-রেন্ডারিং এড়াতে [`memo`:](/reference/react/memo) ব্যবহার করেতে পারেন।

```js {3,5}
import { memo } from 'react';

const List = memo(function List({ items }) {
  // ...
});
```

**এই পরিবর্তনের সাথে, `List` তার সমস্ত props আগের রেন্ডারের মতো একই থাকলে রি-রেন্ডারিং এড়িয়ে যাবে।** এখানেই calculation ক্যাশে করার গুরুত্ব প্রকাশ পায়! কল্পনা করুন আপনি `useMemo` ছাড়া `visibleTodos` calculation করেছেন:

```js {2-3,6-7}
export default function TodoList({ todos, tab, theme }) {
  // Every time the theme changes, this will be a different array...
  const visibleTodos = filterTodos(todos, tab);
  return (
    <div className={theme}>
      {/* ... so List's props will never be the same, and it will re-render every time */}
      <List items={visibleTodos} />
    </div>
  );
}
```

**উপরের উদাহরণে, `filterTodos` ফাংশন সবসময় একটি আলাদা array তৈরি করে**, যেমনটি `{}` অবজেক্ট আক্ষরিকভাবে সবসময় একটি নতুন অবজেক্ট তৈরি করে। সাধারণত, এটি কোন সমস্যা নয়, কিন্তু এর মানে হল যে `List` props কখনোই একই থাকবে না, এবং আপনার [`memo`](/reference/react/memo) অপটিমাইজেশন কাজ করবে না। এখানেই `useMemo` উপযোগী হয়ে ওঠে:

```js {2-3,5,9-10}
export default function TodoList({ todos, tab, theme }) {
  // Tell React to cache your calculation between re-renders...
  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab] // ...so as long as these dependencies don't change...
  );
  return (
    <div className={theme}>
      {/* ...List will receive the same props and can skip re-rendering */}
      <List items={visibleTodos} />
    </div>
  );
}
```


**`visibleTodos` calculation কে `useMemo` দিয়ে মোড়ানোর মাধ্যমে, আপনি নিশ্চিত করেন যে এটি রি-রেন্ডারের মধ্যে একই মান বজায় রাখে (যতক্ষণ না ডিপেন্ডেন্সিস পরিবর্তিত হয়)।** কোনো বিশেষ কারণ ছাড়া আপনার calculation কে `useMemo` এ মোড়ানো *আবশ্যক নয়*। এই উদাহরণে, কারণটি হল আপনি এটিকে `memo`,](/reference/react/memo), দ্বারা মোড়ানো কম্পোনেন্টকে পাস করেন এবং এটি তাকে রি-রেন্ডারিং এড়াতে দেয়। এখানে `useMemo` ব্যবহার করার আরও কিছু কারণ বর্ণিত আছে।

<DeepDive>

#### প্রতিটি JSX নোড মেমোইজ করা {/*memoizing-individual-jsx-nodes*/}

[`memo`](/reference/react/memo) এ `List` কে মোড়ানোর পরিবর্তে, আপনি `<List />` JSX নোডটিকেই  `useMemo` দিয়ে মোড়াতে পারেন:

```js {3,6}
export default function TodoList({ todos, tab, theme }) {
  const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
  const children = useMemo(() => <List items={visibleTodos} />, [visibleTodos]);
  return (
    <div className={theme}>
      {children}
    </div>
  );
}
```

আচরণটি একই থাকবে। যদি `visibleTodos` পরিবর্তিত না হয়, তাহলে `List` পুনরায় রেন্ডার করবে না।

`<List items={visibleTodos} />` এর মতো JSX নোড হল একটি অবজেক্ট যেমন `{ type: List, props: { items: visibleTodos } }`। এই অবজেক্ট তৈরি করা খুবই সহজ, কিন্তু React জানে না এর বিষয়বস্তু আগেরবারের মতো একই আছে কিনা। এই কারণে ডিফল্ট হিসেবে, React `List` কম্পোনেন্টটি পুনরায় রেন্ডার করে।

তবে, যদি React পূর্ববর্তী রেন্ডারের সময়ে একই JSX দেখে, তাহলে আপনার কম্পোনেন্টটি পুনরায় রেন্ডার করার চেষ্টা করবে না। এর কারণে JSX নোডগুলি [অপরিবর্তনীয়](https://en.wikipedia.org/wiki/Immutable_object) আছে বা JSX নোড অবজেক্ট সময়ের সাথে পরিবর্তিত হয়নি, তাই React জানে এটি রি-রেন্ডার এড়িয়ে যাওয়া নিরাপদ। তবে, এর জন্য নোডটি আসলে একই অবজেক্ট হতে হবে, শুধুমাত্র কোডে একই দেখানোর জন্য নয়। এই উদাহরণে `useMemo` এটাই করে।

ম্যানুয়ালি JSX নোডগুলিকে `useMemo`-এ মোড়ানো সুবিধাজনক নয়। উদাহরণস্বরূপ, আপনি এটি শর্তাধীনভাবে করতে পারবেন না। সাধারণত এই কারণে আপনি JSX নোডগুলিকে মোড়ানোর পরিবর্তে কম্পোনেন্টগুলিকে [`memo`](/reference/react/memo) দিয়ে মোড়ান। এতে করে, props একই থাকলে কম্পোনেন্টের অপ্রয়োজনীয় রি-রেন্ডার এড়ানো সহজ হয়, যা পারফরমেন্সের উন্নতি ঘটায়।

</DeepDive>

<Recipes titleText="রি-রেন্ডার স্কিপ এবং সবসময় রি-রেন্ডার করার মধ্যে পার্থক্য" titleId="examples-rerendering">

#### `useMemo` এবং `memo` ব্যবহার করে রি-রেন্ডারিং স্কিপ করা {/*skipping-re-rendering-with-usememo-and-memo*/}

এই উদাহরণে, List কম্পোনেন্টটি **কৃত্রিমভাবে ধীর করা হয়েছে** যাতে আপনি দেখতে পারেন একটি স্লো React কম্পোনেন্ট রেন্ডার করার সময় কেমন হয়। ট্যাবগুলি পরিবর্তন করে এবং থিম টগল করে দেখেন।

ট্যাবগুলি পরিবর্তন করার সময় ধীর মনে হয় কারণ এটি স্লো কম্পোনেন্ট `List` কে পুনরায় রেন্ডার করতে বাধ্য করে। এটি প্রত্যাশিত কারণ `tab` পরিবর্তিত হয়েছে, এবং তাই আপনাকে ব্যবহারকারীর পছন্দ অনুযায়ী স্ক্রিনে দেখাতে হয়। 

এরপর, থিম টগল করে দেখুন। **`useMemo` এবং [`memo`](/reference/react/memo) এর সাহায্যে, এটি কৃত্রিম ধীরতার সত্ত্বেও দ্রুত!** `List` পুনরায় রেন্ডারিং এড়িয়ে গেছে কারণ `visibleTodos` array আগের রেন্ডারের পর থেকে পরিবর্তিত হয়নি। `visibleTodos` array পরিবর্তিত হয়নি কারণ `todos` এবং `tab` (যা আপনি `useMemo`-এ ডিপেন্ডেন্সিস হিসেবে পাস করেন) আগের রেন্ডারের পর থেকে পরিবর্তিত হয়নি।

<Sandpack>

```js src/App.js
import { useState } from 'react';
import { createTodos } from './utils.js';
import TodoList from './TodoList.js';

const todos = createTodos();

export default function App() {
  const [tab, setTab] = useState('all');
  const [isDark, setIsDark] = useState(false);
  return (
    <>
      <button onClick={() => setTab('all')}>
        All
      </button>
      <button onClick={() => setTab('active')}>
        Active
      </button>
      <button onClick={() => setTab('completed')}>
        Completed
      </button>
      <br />
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Dark mode
      </label>
      <hr />
      <TodoList
        todos={todos}
        tab={tab}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

```js src/TodoList.js active
import { useMemo } from 'react';
import List from './List.js';
import { filterTodos } from './utils.js'

export default function TodoList({ todos, theme, tab }) {
  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab]
  );
  return (
    <div className={theme}>
      <p><b>Note: <code>List</code> is artificially slowed down!</b></p>
      <List items={visibleTodos} />
    </div>
  );
}
```

```js src/List.js
import { memo } from 'react';

const List = memo(function List({ items }) {
  console.log('[ARTIFICIALLY SLOW] Rendering <List /> with ' + items.length + ' items');
  let startTime = performance.now();
  while (performance.now() - startTime < 500) {
    // Do nothing for 500 ms to emulate extremely slow code
  }

  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>
          {item.completed ?
            <s>{item.text}</s> :
            item.text
          }
        </li>
      ))}
    </ul>
  );
});

export default List;
```

```js src/utils.js
export function createTodos() {
  const todos = [];
  for (let i = 0; i < 50; i++) {
    todos.push({
      id: i,
      text: "Todo " + (i + 1),
      completed: Math.random() > 0.5
    });
  }
  return todos;
}

export function filterTodos(todos, tab) {
  return todos.filter(todo => {
    if (tab === 'all') {
      return true;
    } else if (tab === 'active') {
      return !todo.completed;
    } else if (tab === 'completed') {
      return todo.completed;
    }
  });
}
```

```css
label {
  display: block;
  margin-top: 10px;
}

.dark {
  background-color: black;
  color: white;
}

.light {
  background-color: white;
  color: black;
}
```

</Sandpack>

<Solution />

#### কম্পোনেন্টকে সর্বদাই পুনরায় রি-রেন্ডার করা {/*always-re-rendering-a-component*/}

এই উদাহরণে, `List` বাস্তবায়নটিও **কৃত্রিমভাবে ধীর করা হয়েছে** যাতে আপনি দেখতে পারেন যখন কোনো React কম্পোনেন্ট রেন্ডার করেন এবং তা যদি সত্যিকারের ধীর হয়ে যায় তখন কি হয়। ট্যাবগুলি পরিবর্তন করে দেখুন এবং থিম টগল করুন।

পূর্ববর্তী উদাহরণের মতো নয়, এখন থিম টগল করাও ধীর হয়ে গেছে! এটি কারণ এই সংস্করণে `useMemo` কল নেই, ফলে `visibleTodos` সর্বদা একটি আলাদা array হয়ে যায়, এবং ধীর করা `List` কম্পোনেন্টটি রি-রেন্ডারিং এড়াতে পারে না।

<Sandpack>

```js src/App.js
import { useState } from 'react';
import { createTodos } from './utils.js';
import TodoList from './TodoList.js';

const todos = createTodos();

export default function App() {
  const [tab, setTab] = useState('all');
  const [isDark, setIsDark] = useState(false);
  return (
    <>
      <button onClick={() => setTab('all')}>
        All
      </button>
      <button onClick={() => setTab('active')}>
        Active
      </button>
      <button onClick={() => setTab('completed')}>
        Completed
      </button>
      <br />
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Dark mode
      </label>
      <hr />
      <TodoList
        todos={todos}
        tab={tab}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

```js src/TodoList.js active
import List from './List.js';
import { filterTodos } from './utils.js'

export default function TodoList({ todos, theme, tab }) {
  const visibleTodos = filterTodos(todos, tab);
  return (
    <div className={theme}>
      <p><b>Note: <code>List</code> is artificially slowed down!</b></p>
      <List items={visibleTodos} />
    </div>
  );
}
```

```js src/List.js
import { memo } from 'react';

const List = memo(function List({ items }) {
  console.log('[ARTIFICIALLY SLOW] Rendering <List /> with ' + items.length + ' items');
  let startTime = performance.now();
  while (performance.now() - startTime < 500) {
    // Do nothing for 500 ms to emulate extremely slow code
  }

  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>
          {item.completed ?
            <s>{item.text}</s> :
            item.text
          }
        </li>
      ))}
    </ul>
  );
});

export default List;
```

```js src/utils.js
export function createTodos() {
  const todos = [];
  for (let i = 0; i < 50; i++) {
    todos.push({
      id: i,
      text: "Todo " + (i + 1),
      completed: Math.random() > 0.5
    });
  }
  return todos;
}

export function filterTodos(todos, tab) {
  return todos.filter(todo => {
    if (tab === 'all') {
      return true;
    } else if (tab === 'active') {
      return !todo.completed;
    } else if (tab === 'completed') {
      return todo.completed;
    }
  });
}
```

```css
label {
  display: block;
  margin-top: 10px;
}

.dark {
  background-color: black;
  color: white;
}

.light {
  background-color: white;
  color: black;
}
```

</Sandpack>

তবে, এখানে একই কোড কৃত্রিম স্লো অপসারণ করা হয়েছে। দেখুনতো `useMemo` এর অভাবটি লক্ষণীয় মনে হয় কিনা?

<Sandpack>

```js src/App.js
import { useState } from 'react';
import { createTodos } from './utils.js';
import TodoList from './TodoList.js';

const todos = createTodos();

export default function App() {
  const [tab, setTab] = useState('all');
  const [isDark, setIsDark] = useState(false);
  return (
    <>
      <button onClick={() => setTab('all')}>
        All
      </button>
      <button onClick={() => setTab('active')}>
        Active
      </button>
      <button onClick={() => setTab('completed')}>
        Completed
      </button>
      <br />
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Dark mode
      </label>
      <hr />
      <TodoList
        todos={todos}
        tab={tab}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

```js src/TodoList.js active
import List from './List.js';
import { filterTodos } from './utils.js'

export default function TodoList({ todos, theme, tab }) {
  const visibleTodos = filterTodos(todos, tab);
  return (
    <div className={theme}>
      <List items={visibleTodos} />
    </div>
  );
}
```

```js src/List.js
import { memo } from 'react';

function List({ items }) {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>
          {item.completed ?
            <s>{item.text}</s> :
            item.text
          }
        </li>
      ))}
    </ul>
  );
}

export default memo(List);
```

```js src/utils.js
export function createTodos() {
  const todos = [];
  for (let i = 0; i < 50; i++) {
    todos.push({
      id: i,
      text: "Todo " + (i + 1),
      completed: Math.random() > 0.5
    });
  }
  return todos;
}

export function filterTodos(todos, tab) {
  return todos.filter(todo => {
    if (tab === 'all') {
      return true;
    } else if (tab === 'active') {
      return !todo.completed;
    } else if (tab === 'completed') {
      return todo.completed;
    }
  });
}
```

```css
label {
  display: block;
  margin-top: 10px;
}

.dark {
  background-color: black;
  color: white;
}

.light {
  background-color: white;
  color: black;
}
```

</Sandpack>

প্রায়ই, মেমোইজেশন ছাড়াই কোড ভালোভাবে কাজ করে। যদি আপনার ইন্টার‌্যাকশনগুলি যথেষ্ট দ্রুত হয়, তাহলে আপনার মেমোইজেশনের প্রয়োজন নেই।

মনে রাখবেন যে আপনার অ্যাপটি বাস্তব পরিস্থিতিতে কি ধীর করে তা বুঝতে হলে আপনাকে React প্রোডাকশন মোডে চালাতে হবে, [React Developer Tools](/learn/react-developer-tools) নিষ্ক্রিয় করতে হবে, এবং আপনার অ্যাপের ব্যবহারকারীদের মতো ডিভাইসগুলি ব্যবহার করতে হবে।

<Solution />

</Recipes>

---

<<<<<<< HEAD
### অন্য হুকের ডিপেন্ডেন্সিস মেমোইজ করা {/*memoizing-a-dependency-of-another-hook*/}
=======
### Preventing an Effect from firing too often {/*preventing-an-effect-from-firing-too-often*/}

Sometimes, you might want to use a value inside an [Effect:](/learn/synchronizing-with-effects)

```js {4-7,10}
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  const options = {
    serverUrl: 'https://localhost:1234',
    roomId: roomId
  }

  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    // ...
```

This creates a problem. [Every reactive value must be declared as a dependency of your Effect.](/learn/lifecycle-of-reactive-effects#react-verifies-that-you-specified-every-reactive-value-as-a-dependency) However, if you declare `options` as a dependency, it will cause your Effect to constantly reconnect to the chat room:


```js {5}
  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [options]); // 🔴 Problem: This dependency changes on every render
  // ...
```

To solve this, you can wrap the object you need to call from an Effect in `useMemo`:

```js {4-9,16}
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  const options = useMemo(() => {
    return {
      serverUrl: 'https://localhost:1234',
      roomId: roomId
    };
  }, [roomId]); // ✅ Only changes when roomId changes

  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [options]); // ✅ Only changes when options changes
  // ...
```

This ensures that the `options` object is the same between re-renders if `useMemo` returns the cached object.

However, since `useMemo` is performance optimization, not a semantic guarantee, React may throw away the cached value if [there is a specific reason to do that](#caveats). This will also cause the effect to re-fire, **so it's even better to remove the need for a function dependency** by moving your object *inside* the Effect:

```js {5-8,13}
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const options = { // ✅ No need for useMemo or object dependencies!
      serverUrl: 'https://localhost:1234',
      roomId: roomId
    }
    
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ Only changes when roomId changes
  // ...
```

Now your code is simpler and doesn't need `useMemo`. [Learn more about removing Effect dependencies.](/learn/removing-effect-dependencies#move-dynamic-objects-and-functions-inside-your-effect)


### Memoizing a dependency of another Hook {/*memoizing-a-dependency-of-another-hook*/}
>>>>>>> c0c955ed1d1c4fe3bf3e18c06a8d121902a01619

ধরুন আপনি এমন একটি calculation করছেন যা কম্পোনেন্ট বডির মধ্যে সরাসরি তৈরি করা একটি অবজেক্টের উপর নির্ভর করে:

```js {2}
function Dropdown({ allItems, text }) {
  const searchOptions = { matchMode: 'whole-word', text };

  const visibleItems = useMemo(() => {
    return searchItems(allItems, searchOptions);
  }, [allItems, searchOptions]); // 🚩 Caution: Dependency on an object created in the component body
  // ...
```

এই ধরনের একটি অবজেক্টের উপর নির্ভর করা মেমোইজেশনের উদ্দেশ্যকে বানচাল করে। যখন একটি কম্পোনেন্ট পুনরায় রেন্ডার হয়, তখন কম্পোনেন্ট বডির মধ্যে সরাসরি থাকা সমস্ত কোড আবার রান হয়। **`searchOptions` অবজেক্ট তৈরির কোডের লাইনগুলিও প্রতিবার রি-রেন্ডার হয়।** যেহেতু `searchOptions` আপনার useMemo কলের উপর নির্ভরশিল, এবং প্রতিবার এটি আলাদা, React জানে যে ডিপেন্ডেন্সিস আলাদা তাই প্রতিবার `searchItems` পুনরায় রেন্ডার করে।

এই সমস্যাটি ঠিক করার জন্য, আপনি ডিপেন্ডেন্সিস হিসেবে পাস করার আগে `searchOptions` অবজেক্টটি মেমোইজ করতে পারেন:

```js {2-4}
function Dropdown({ allItems, text }) {
  const searchOptions = useMemo(() => {
    return { matchMode: 'whole-word', text };
  }, [text]); // ✅ Only changes when text changes

  const visibleItems = useMemo(() => {
    return searchItems(allItems, searchOptions);
  }, [allItems, searchOptions]); // ✅ Only changes when allItems or searchOptions changes
  // ...
```

উপরের উদাহরণে, যদি `text` পরিবর্তিত না হয়, তাহলে `searchOptions` অবজেক্টটিও পরিবর্তিত হবে না। তবে, আরও ভালো সমাধান হলো `searchOptions` অবজেক্টটি `useMemo` ফাংশনের ভেতরে সরানো:

```js {3}
function Dropdown({ allItems, text }) {
  const visibleItems = useMemo(() => {
    const searchOptions = { matchMode: 'whole-word', text };
    return searchItems(allItems, searchOptions);
  }, [allItems, text]); // ✅ Only changes when allItems or text changes
  // ...
```

এখন আপনার calculation `text` এর উপর সরাসরি নির্ভর করে (যা একটি string এবং “আকস্মিকভাবে” পরিবর্তিত হতে পারবে না)।

---

### একটি ফাংশন মেমোইজ করা {/*memoizing-a-function*/}

ধরুন `Form` কম্পোনেন্টটি [`memo`.](/reference/react/memo) দ্বারা মোড়ানো। আপনি এটির মধ্যে একটি ফাংশনকে prop হিসেবে পাস করতে চান:

```js {2-7}
export default function ProductPage({ productId, referrer }) {
  function handleSubmit(orderDetails) {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails
    });
  }

  return <Form onSubmit={handleSubmit} />;
}
```

যেমন `{}` একটি আলাদা অবজেক্ট তৈরি করে, ফাংশন যেমন `function() {}` এবং এক্সপ্রেশন `() => {}` প্রতিটি রি-রেন্ডারে আলাদা ফাংশন তৈরি করে। নিজে নিজে, একটি নতুন ফাংশন তৈরি করা একটি সমস্যা নয়। এটি এড়িয়ে চলার মতো কিছু নয়! তবে, যদি Form কম্পোনেন্টটি মেমোইজ করা হয়, ধরে নেওয়া হয় যে কোনো props পরিবর্তিত হবে না তাহলে এটি রি-রেন্ডার করবে না। কিন্তু যদি একটি prop *সর্বদা* ও আলাদা হয়, তাহলে মেমোইজেশনের উদ্দেশ্য নষ্ট হয়ে যাবে।

একটি ফাংশনকে `useMemo` দিয়ে মেমোইজ করতে হয়, ক্যাল্কুলেটেড ফাংশনটি আরেকটি ফাংশন ফেরত দেবে: 

```js {2-3,8-9}
export default function Page({ productId, referrer }) {
  const handleSubmit = useMemo(() => {
    return (orderDetails) => {
      post('/product/' + productId + '/buy', {
        referrer,
        orderDetails
      });
    };
  }, [productId, referrer]);

  return <Form onSubmit={handleSubmit} />;
}
```

এটি খানিকটা বেমানান মনে হচ্ছে! **ফাংশনগুলি মেমোইজ করা যথেষ্ট সাধারণ, এবং React এর জন্য একটি নির্মিত হুক রয়েছে যা বিশেষভাবে এই উদ্দেশ্যের জন্য। `useMemo`-এর পরিবর্তে আপনার ফাংশনগুলিকে [`useCallback`](/reference/react/useCallback) ব্যবহার করুন** একটি অতিরিক্ত নেস্টেড ফাংশন লেখার প্রয়োজন হবে না।

```js {2,7}
export default function Page({ productId, referrer }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails
    });
  }, [productId, referrer]);

  return <Form onSubmit={handleSubmit} />;
}
```

উপরের উদাহরণ দুটি সম্পূর্ণরূপে সমতুল্য। `useCallback`-এর একমাত্র সুবিধা হল এটি আপনাকে একটি অতিরিক্ত নেস্টেড ফাংশন লেখার প্রয়োজন পড়বে না। এটি অন্য কিছু করে না। [useCallback সম্পর্কে আরও পড়ুন।](/reference/react/useCallback)

---

## সমস্যা সমাধান {/*troubleshooting*/}

### calculation প্রতিটি রি-রেন্ডারে দুইবার রান করে {/*my-calculation-runs-twice-on-every-re-render*/}

[Strict Mode](/reference/react/StrictMode)-এ, React আপনার কিছু ফাংশন একবারের পরিবর্তে দুইবার কল করবে:

```js {2,5,6}
function TodoList({ todos, tab }) {
  // This component function will run twice for every render.

  const visibleTodos = useMemo(() => {
    // This calculation will run twice if any of the dependencies change.
    return filterTodos(todos, tab);
  }, [todos, tab]);

  // ...
```

এটি প্রত্যাশিত এবং আপনার কোডের কোন সমস্যা সৃষ্টি করবে না।

এই **শুধুমাত্র ডেভেলপমেন্ট এর সময়** এটি আপনাকে [কম্পোনেন্টগুলি পিওর রাখতে](/learn/keeping-components-pure) সাহায্য করে। React একটি কলের ফলাফল ব্যবহার করে এবং অন্য কলের ফলাফল উপেক্ষা করে। যতক্ষণ আপনার কম্পোনেন্ট এবং calculation ফাংশনগুলি পিওর হয়, তাহলে এটি আপনার লজিকে প্রভাব ফেলবে না। তবে, যদি তারা আকস্মিকভাবে ইমপিওর হয়, তাহলে এটি আপনাকে ভুল খুঁজে বের করে ঠিক করতে সাহায্য করে।

উদাহরণস্বরূপ, এই অপিওর ফাংশন একটি array পরিবর্তন করে যা আপনি একটি prop হিসেবে পেয়েছেন:

```js {2-3}
  const visibleTodos = useMemo(() => {
    // 🚩 Mistake: mutating a prop
    todos.push({ id: 'last', text: 'Go for a walk!' });
    const filtered = filterTodos(todos, tab);
    return filtered;
  }, [todos, tab]);
```

React আপনার ফাংশনটি দুইবার কল করে, তাই আপনি লক্ষ্য করবেন যে `todo` দুইবার যোগ করা হয়েছে। আপনার calculation কোনো বিদ্যমান অবজেক্ট পরিবর্তন করা উচিত নয়, তবে calculation এর সময় আপনি যে কোনো নতুন অবজেক্ট পরিবর্তন করা ঠিক আছে। উদাহরণস্বরূপ, যদি `filterTodos` ফাংশন সবসময় একটি *আলাদা* array ফেরত দেয়, তাহলে আপনি *সেই* array পরিবর্তন করতে পারেন:

```js {3,4}
  const visibleTodos = useMemo(() => {
    const filtered = filterTodos(todos, tab);
    // ✅ Correct: mutating an object you created during the calculation
    filtered.push({ id: 'last', text: 'Go for a walk!' });
    return filtered;
  }, [todos, tab]);
```

পিওরিটি সম্পর্কে আরও জানতে [কম্পোনেন্টগুলি পিওর রাখা](/learn/keeping-components-pure) পড়ুন।

এছাড়াও, মিউটেশন ছাড়া [অবজেক্ট আপডেট করা](/learn/updating-objects-in-state) এবং [array আপডেট করা](/learn/updating-arrays-in-state) সম্পর্কে গাইডগুলি দেখুন।

---

### আমার `useMemo` কলটি একটি অবজেক্ট ফেরত দেওয়ার কথা, কিন্তু এটি `undefined` ফেরত দেয় {/*my-usememo-call-is-supposed-to-return-an-object-but-returns-undefined*/}

এই কোডটি কাজ করে না:

```js {1-2,5}
  // 🔴 You can't return an object from an arrow function with () => {
  const searchOptions = useMemo(() => {
    matchMode: 'whole-word',
    text: text
  }, [text]);
```

জাভাস্ক্রিপ্টে, `() => {` এরো ফাংশনের বডি শুরু করে, তাই `{` ব্রেসটি আপনার অবজেক্টের অংশ নয়। এই কারণেই এটি কোনো অবজেক্ট ফেরত দেয় না, এবং ভুলের সৃষ্টি করে। আপনি এটি ঠিক করতে পারেন `({` এবং `})` এর মতো প্যারেন্থেসিস যোগ করে:

```js {1-2,5}
  // This works, but is easy for someone to break again
  const searchOptions = useMemo(() => ({
    matchMode: 'whole-word',
    text: text
  }), [text]);
```

তবে, এটি এখনও বিভ্রান্তিকর এবং কেউ প্যারেন্থেসিস সরিয়ে দিয়ে এটিকে ভেঙে ফেলার জন্য খুবই সহজ।

এই ভুল এড়াতে, স্পষ্টভাবে একটি `return` স্টেটমেন্ট লিখুন:

```js {1-3,6-7}
  // ✅ This works and is explicit
  const searchOptions = useMemo(() => {
    return {
      matchMode: 'whole-word',
      text: text
    };
  }, [text]);
```

---

### প্রতিবার আমার কম্পোনেন্ট রেন্ডার হয়, `useMemo` ব্যবহারের পরেও পুনরায় রান করে {/*every-time-my-component-renders-the-calculation-in-usememo-re-runs*/}

নিশ্চিত করুন যে আপনি দ্বিতীয় আর্গুমেন্ট হিসেবে ডিপেন্ডেন্সিস array নির্দিষ্ট করেছেন!

যদি আপনি ডিপেন্ডেন্সিস array ভুলে যায়, `useMemo` প্রতিবার calculation পুনরায় চালাবে:

```js {2-3}
function TodoList({ todos, tab }) {
  // 🔴 Recalculates every time: no dependency array
  const visibleTodos = useMemo(() => filterTodos(todos, tab));
  // ...
```

এটি দ্বিতীয় আর্গুমেন্ট হিসেবে ডিপেন্ডেন্সিস array পাস করার সংশোধিত ভার্সন:

```js {2-3}
function TodoList({ todos, tab }) {
  // ✅ Does not recalculate unnecessarily
  const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
  // ...
```

যদি এটি সাহায্য না করে, তাহলে সমস্যাটি হল আপনার অন্তত একটি ডিপেন্ডেন্সিস পূর্ববর্তী রেন্ডারের থেকে ভিন্ন। আপনি কনসোলে ম্যানুয়ালি আপনার ডিপেন্ডেন্সিসগুলি লগ করে এই সমস্যাটি ডিবাগ করতে পারেন:

```js
  const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
  console.log([todos, tab]);
```

তারপর আপনি কনসোলে ভিন্ন রি-রেন্ডারের array গুলির উপর রাইট-ক্লিক করে `“Store as a global variable”` নির্বাচন করতে পারেন উভয়ের জন্য। ধরুন প্রথমটি `temp1` হিসেবে এবং দ্বিতীয়টি `temp2` হিসেবে সংরক্ষিত হয়েছে, তাহলে আপনি browser এর কনসোল ব্যবহার করে পরীক্ষা করতে পারেন যে উভয় array প্রতিটি ডিপেন্ডেন্সিস একই কিনা:

```js
Object.is(temp1[0], temp2[0]); // Is the first dependency the same between the arrays?
Object.is(temp1[1], temp2[1]); // Is the second dependency the same between the arrays?
Object.is(temp1[2], temp2[2]); // ... and so on for every dependency ...
```

যখন আপনি সনাক্ত করেন কোন ডিপেন্ডেন্সিস মেমোইজেশনকে ভঙ্গ করতেছে, তখন এটিকে সরানোর একটি উপায় খুঁজুন, অথবা [এটিও মেমোইজ করুন।](#memoizing-a-dependency-of-another-hook)

---

### আমাকে প্রতিটি লিস্ট আইটেমের জন্য একটি লুপে `useMemo` কল করতে হবে, কিন্তু এটি অনুমোদিত নয় {/*i-need-to-call-usememo-for-each-list-item-in-a-loop-but-its-not-allowed*/}

ধরুন `Chart` কম্পোনেন্টটি [`memo`](/reference/react/memo)-এ মোড়ানো। আপনি চান `ReportList` কম্পোনেন্ট পুনরায় রেন্ডার হলে লিস্টে প্রতিটি `Chart` রি-রেন্ডারিং না হক। তবে, আপনি একটি লুপে মদ্ধে `useMemo` কল করতে পারবেন না: 

```js {5-11}
function ReportList({ items }) {
  return (
    <article>
      {items.map(item => {
        // 🔴 You can't call useMemo in a loop like this:
        const data = useMemo(() => calculateReport(item), [item]);
        return (
          <figure key={item.id}>
            <Chart data={data} />
          </figure>
        );
      })}
    </article>
  );
}
```

এর পরিবর্তে, প্রতিটি আইটেমের জন্য একটি কম্পোনেন্ট পৃথক করুন এবং প্রতিটি আইটেমের ডেটাকে মেমোইজ করুন:

```js {5,12-18}
function ReportList({ items }) {
  return (
    <article>
      {items.map(item =>
        <Report key={item.id} item={item} />
      )}
    </article>
  );
}

function Report({ item }) {
  // ✅ Call useMemo at the top level:
  const data = useMemo(() => calculateReport(item), [item]);
  return (
    <figure>
      <Chart data={data} />
    </figure>
  );
}
```

বিকল্প হিসেবে, আপনি `useMemo` সরিয়ে ফেলে `Report`-কে [`memo`.](/reference/react/memo) দিয়ে মোড়াতে পারেন। এর ফলে যদি item এর prop পরিবর্তিত না হয়, তাহলে `Report` রি-রেন্ডারিং করেবে না, সুতরাং `Chart` ও রি-রেন্ডারিং এড়িয়ে যাবে:

```js {5,6,12}
function ReportList({ items }) {
  // ...
}

const Report = memo(function Report({ item }) {
  const data = calculateReport(item);
  return (
    <figure>
      <Chart data={data} />
    </figure>
  );
});
```
