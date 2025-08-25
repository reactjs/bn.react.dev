---
title: memo
---

<Intro>

`memo` আপনাকে একটি কম্পোনেন্টের পুনরায় রেন্ডারিং এড়াতে দেয় যখন এর প্রপস অপরিবর্তিত থাকে।

```
const MemoizedComponent = memo(SomeComponent, arePropsEqual?)
```

</Intro>

<Note>

[React Compiler](/learn/react-compiler) automatically applies the equivalent of `memo` to all components, reducing the need for manual memoization. You can use the compiler to handle component memoization automatically.

</Note>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `memo(Component, arePropsEqual?)` {/*memo*/}

একটি কম্পোনেন্টকে `memo`-তে মোড়ানো হলে এর *memoized* সংস্করণ পাওয়া যায়। এই মেমোয়াইজড কম্পোনেন্ট সাধারণত তার parent কম্পোনেন্ট পুনরায় রেন্ডার হলেও পুনরায় রেন্ডার করা হয় না, যদি তার প্রপস অপরিবর্তিত থাকে। তবে React তারপরও এটি পুনরায় রেন্ডার করতে পারেঃ মেমোয়াইজেশন একটি পারফরম্যান্স অপ্টিমাইজেশন, গ্যারান্টি নয়।

```js
import { memo } from 'react';

const SomeComponent = memo(function SomeComponent(props) {
  // ...
});
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটারস {/*parameters*/}

* `Component`: যে কম্পোনেন্টটি আপনি মেমোয়াইজ করতে চান। `memo` এই কম্পোনেন্টকে পরিবর্তন করে না, বরং এর পরিবর্তে একটি নতুন, মেমোয়াইজড কম্পোনেন্ট রিটার্ন করে। যেকোনো বৈধ React কম্পোনেন্ট, ফাংশন এবং [`forwardRef`](/reference/react/forwardRef) কম্পোনেন্টসহ, গ্রহণযোগ্য।

* **ঐচ্ছিক** `arePropsEqual`: দুটি আর্গুমেন্ট গ্রহণকারী একটি ফাংশন: কম্পোনেন্টের পূর্ববর্তী প্রপস এবং এর নতুন প্রপস। যদি পুরানো এবং নতুন প্রপস একই হয়ঃ অর্থাৎ, যদি কম্পোনেন্ট নতুন প্রপসের সাথে পুরানো প্রপসের মতোই আউটপুট দেয় এবং আচরণ করে, তাহলে এটি `true` রিটার্ন করবে। অন্যথায় এটি `false` রিটার্ন করবে। সাধারণত, আপনি এই ফাংশনটি নির্দিষ্ট করবেন না। ডিফল্ট হিসাবে, React প্রতিটি প্রপকে [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)-এর সাথে তুলনা করবে।

#### রিটার্নস {/*returns*/}

`memo` একটি নতুন React কম্পোনেন্ট ফেরত দেয়। এটি `memo`-তে প্রদত্ত কম্পোনেন্টের মতোই আচরণ করে, তবে React সবসময় এটিকে তার প্যারেন্ট পুনরায় রেন্ডার হলে পুনরায় রেন্ডার করবে না, যদি না তার প্রপস পরিবর্তিত হয়।

---

## ব্যবহার {/*usage*/}

### প্রপস অপরিবর্তিত থাকলে রি-রেন্ডারিং এড়ানো {/*skipping-re-rendering-when-props-are-unchanged*/}

React সাধারণত একটি কম্পোনেন্ট পুনরায় রেন্ডার করে যখন এর প্যারেন্ট পুনরায় রেন্ডার হয়। `memo` দ্বারা, আপনি এমন একটি কম্পোনেন্ট তৈরি করতে পারেন যা React তার প্যারেন্ট পুনরায় রেন্ডার হলেও পুনরায় রেন্ডার করবে না, যতক্ষণ না এর নতুন প্রপস পুরানো প্রপসের সাথে একই থাকে। এমন একটি কম্পোনেন্টকে *মেমোয়াইজড* বলা হয়।

একটি কম্পোনেন্টকে মেমোয়াইজ করতে, এটিকে `memo`-এ wrap korun মোড়ান এবং এর রিটার্ন করা মানটি আপনার মূল কম্পোনেন্টের পরিবর্তে ব্যবহার করুনঃ

```js
const Greeting = memo(function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
});

export default Greeting;
```

একটি React কম্পোনেন্টের সবসময় [pure রেন্ডারিং লজিক](/learn/keeping-components-pure) থাকা উচিত। এর অর্থ এটি একই আউটপুট ফেরত দেবে যদি এর প্রপস, স্টেট এবং কন্টেক্স্ট পরিবর্তিত না হয়। `memo` ব্যবহার করে, আপনি React-কে জানাচ্ছেন যে আপনার কম্পোনেন্ট এই প্রয়োজনীয়তাটি পূরণ করে, তাই React-এর প্রপস পরিবর্তিত না হলে পুনরায় রেন্ডার করতে হবে না। `memo` ব্যবহার করলেও, আপনার কম্পোনেন্ট তার নিজের স্টেট পরিবর্তন হলে অথবা ব্যবহৃত কন্টেক্স্ট পরিবর্তন হলে পুনরায় রেন্ডার করবে।

এই উদাহরণে, লক্ষ্য করুন যে `Greeting` কম্পোনেন্টটি `name` পরিবর্তিত হলে পুনরায় রেন্ডার হয় (কারণ এটি এর একটি প্রপ), কিন্তু `address` পরিবর্তন হলে নয় (কারণ এটি `Greeting`-এ প্রপ হিসেবে পাস করা হয় না):

<Sandpack>

```js
import { memo, useState } from 'react';

export default function MyApp() {
  const [name, setName] = useState('');
  const [address, setAddress] = useState('');
  return (
    <>
      <label>
        Name{': '}
        <input value={name} onChange={e => setName(e.target.value)} />
      </label>
      <label>
        Address{': '}
        <input value={address} onChange={e => setAddress(e.target.value)} />
      </label>
      <Greeting name={name} />
    </>
  );
}

const Greeting = memo(function Greeting({ name }) {
  console.log("Greeting was rendered at", new Date().toLocaleTimeString());
  return <h3>Hello{name && ', '}{name}!</h3>;
});
```

```css
label {
  display: block;
  margin-bottom: 16px;
}
```

</Sandpack>

<Note>

**আপনি কেবল কর্মক্ষমতা অপ্টিমাইজেশন হিসাবে `memo`-এর উপর নির্ভর করা উচিত।** যদি আপনার কোড `memo` ছাড়া কাজ না করে, তাহলে প্রথমে মৌলিক সমস্যাটি খুঁজে বের করুন এবং তা ঠিক করুন। তারপর আপনি কর্মক্ষমতা উন্নতির জন্য `memo` যোগ করতে পারেন।

</Note>

<DeepDive>

#### আপনি কি সর্বত্র `memo` যোগ করবেন? {/*should-you-add-memo-everywhere*/}

<<<<<<< HEAD
যদি আপনার অ্যাপ এই সাইটের মতো হয়, এবং বেশিরভাগ ইন্টারেকশন ভারী (যেমন একটি পৃষ্ঠা বা পুরো অংশ প্রতিস্থাপন করা) হয়, তাহলে মেমোয়াইজেশন সাধারণত অপ্রয়োজনীয়। অন্যদিকে, যদি আপনার অ্যাপ একটি ড্রয়িং এডিটরের মতো হয়, এবং বেশিরভাগ ইন্টারেকশন সূক্ষ্ম (যেমন shape সরানো), তাহলে মেমোয়াইজেশন আপনার কাছে খুব উপকারী মনে হতে পারে।
=======
If your app is like this site, and most interactions are coarse (like replacing a page or an entire section), memoization is usually unnecessary. On the other hand, if your app is more like a drawing editor, and most interactions are granular (like moving shapes), then you might find memoization very helpful.
>>>>>>> 27d86ffe6ec82e3642c6490d2187bae2271020a4

`memo` দ্বারা অপ্টিমাইজেশন তখনই মূল্যবান যখন আপনার কম্পোনেন্ট একই নির্দিষ্ট প্রপসের সাথে প্রায়শই পুনরায় রেন্ডার হয়, এবং এর পুনরায় রেন্ডার লজিক খরচবহুল। যদি আপনার কম্পোনেন্ট পুনরায় রেন্ডার হওয়ার সময় কোনো লক্ষণীয় ধীরতা না থাকে, তাহলে `memo` অপ্রয়োজনীয়। মনে রাখবেন, যদি আপনার কম্পোনেন্টের পাস করা প্রপস *সর্বদা আলাদা* হয়, যেমন যদি আপনি একটি অবজেক্ট বা রেন্ডারিং সময় ডিফাইন করা একটি সাধারণ ফাংশন পাস করেন। এই কারণে আপনার প্রায়শই [`useMemo`](/reference/react/useMemo#skipping-re-rendering-of-components) এবং [`useCallback`](/reference/react/useCallback#skipping-re-rendering-of-components) এর সাথে `memo` ব্যবহার করা প্রয়োজন হবে।

`memo` দ্বারা একটি কম্পোনেন্ট wrap করার অন্যান্য ক্ষেত্রে কোনো উপকার নেই। এটি করলে বিশেষ কোনো ক্ষতি হয় না, তাই কিছু টিম প্রতিটি ক্ষেত্রে ভেবে না দেখে যথাসম্ভব মেমোয়াইজ করতে চায়। এই পদ্ধতির নেতিবাচক দিক হল কোড কম পাঠযোগ্য হয়ে যায়। এছাড়াও, সব মেমোয়াইজেশন কার্যকর নয়ঃ একটি "সর্বদা নতুন" মান পুরো কম্পোনেন্টের মেমোয়াইজেশন ভেঙ্গে দেবার জন্য যথেষ্ট।

**ব্যবহারিকভাবে, কয়েকটি নীতি অনুসরণ করে আপনি ক্ষেত্রবিশেষে অনেক মেমোয়াইজেশন অপ্রয়োজনীয় করে তুলতে পারেনঃ**

1. যখন একটি কম্পোনেন্ট অন্যান্য কম্পোনেন্টগুলিকে দৃশ্যত জড়িয়ে রাখে, তখন এটিকে [JSX চাইল্ড হিসাবে গ্রহণ করতে দিন](/learn/passing-props-to-a-component#passing-jsx-as-children)। এর ফলে, যখন র‍্যাপার কম্পোনেন্ট নিজের স্টেট আপডেট করে, React জানে যে এর চাইল্ডগুলিকে পুনরায় রেন্ডার করার প্রয়োজন নেই।
1. স্থানীয় স্টেটকে প্রাধান্য দিন এবং [স্টেট উপরে তোলা](/learn/sharing-state-between-components) প্রয়োজনের চেয়ে বেশি না করা ভালো। উদাহরণস্বরূপ, ফর্ম এবং কোনো আইটেম উপরে হোভার করা নিয়ে অস্থায়ী স্টেট আপনার ট্রির শীর্ষে বা একটি গ্লোবাল স্টেট লাইব্রেরিতে রাখবেন না।
1. আপনার [রেন্ডারিং লজিক শুদ্ধ](/learn/keeping-components-pure) রাখুন। যদি কোনো কম্পোনেন্ট পুনরায় রেন্ডার করা সমস্যা সৃষ্টি করে বা কোনো লক্ষণীয় দৃশ্য সমস্যা তৈরি করে, তাহলে এটি আপনার কম্পোনেন্টের বাগ! মেমোয়াইজেশন যোগ করার পরিবর্তে বাগটি ঠিক করুন।
1. [অপ্রয়োজনীয় ইফেক্ট যা স্টেট আপডেট করে](/learn/you-might-not-need-an-effect) এড়িয়ে চলুন। বেশিরভাগ React অ্যাপের কর্মক্ষমতা সমস্যা এমন আপডেট চেইন থেকে উদ্ভূত হয় যা ইফেক্ট থেকে আসে এবং আপনার কম্পোনেন্টগুলিকে বারবার রেন্ডার করায়।
1. আপনার ইফেক্ট থেকে [অপ্রয়োজনীয় নির্ভরতা সরিয়ে ফেলার](/learn/removing-effect-dependencies) চেষ্টা করুন। উদাহরণস্বরূপ, মেমোয়াইজেশনের পরিবর্তে, কোনো অবজেক্ট বা ফাংশনকে একটি ইফেক্টের মধ্যে বা কম্পোনেন্টের বাইরে সরান৤

যদি কোনো নির্দিষ্ট ইন্টারেকশন এখনও ধীর অনুভূত হয়, [React Developer Tools প্রোফাইলার ব্যবহার করুন](https://legacy.reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html) যেগুলি কম্পোনেন্টগুলি মেমোয়াইজেশন থেকে সর্বাধিক উপকার পাবে তা দেখার জন্য, এবং প্রয়োজনমতো মেমোয়াইজেশন যোগ করুন। এই নীতিগুলি আপনার কম্পোনেন্টগুলিকে ডিবাগ করা এবং বোঝা সহজ করে তোলে, তাই যেকোনো অবস্থায় এগুলি অনুসরণ করা ভালো। দীর্ঘমেয়াদে, আমরা [স্বয়ংক্রিয়ভাবে সূক্ষ্ম মেমোয়াইজেশন করা](https://www.youtube.com/watch?v=lGEMwh32soc) নিয়ে গবেষণা করছি যা এই সমস্যাটি একবারের জন্য সমাধান করবে।

</DeepDive>

---

### স্টেট ব্যবহার করে মেমোয়াইজড কম্পোনেন্ট আপডেট করা {/*updating-a-memoized-component-using-state*/}

যদিও একটি কম্পোনেন্ট মেমোয়াইজ করা হয়, এটি তবুও পুনরায় রেন্ডার হবে যখন এর নিজের স্টেট বদলায়। মেমোয়াইজেশন শুধুমাত্র প্যারেন্ট কম্পোনেন্ট থেকে পাস করা প্রপস নিয়ে কাজ করে।

<Sandpack>

```js
import { memo, useState } from 'react';

export default function MyApp() {
  const [name, setName] = useState('');
  const [address, setAddress] = useState('');
  return (
    <>
      <label>
        Name{': '}
        <input value={name} onChange={e => setName(e.target.value)} />
      </label>
      <label>
        Address{': '}
        <input value={address} onChange={e => setAddress(e.target.value)} />
      </label>
      <Greeting name={name} />
    </>
  );
}

const Greeting = memo(function Greeting({ name }) {
  console.log('Greeting was rendered at', new Date().toLocaleTimeString());
  const [greeting, setGreeting] = useState('Hello');
  return (
    <>
      <h3>{greeting}{name && ', '}{name}!</h3>
      <GreetingSelector value={greeting} onChange={setGreeting} />
    </>
  );
});

function GreetingSelector({ value, onChange }) {
  return (
    <>
      <label>
        <input
          type="radio"
          checked={value === 'Hello'}
          onChange={e => onChange('Hello')}
        />
        Regular greeting
      </label>
      <label>
        <input
          type="radio"
          checked={value === 'Hello and welcome'}
          onChange={e => onChange('Hello and welcome')}
        />
        Enthusiastic greeting
      </label>
    </>
  );
}
```

```css
label {
  display: block;
  margin-bottom: 16px;
}
```

</Sandpack>

যদি আপনি একটি স্টেট ভেরিয়েবলকে এর বর্তমান মানে সেট করেন, React আপনার কম্পোনেন্টের পুনরায় রেন্ডার করা এড়িয়ে যাবে, এমনকি `memo` ছাড়াও। আপনি হয়তো দেখতে পাবেন যে আপনার কম্পোনেন্ট ফাংশনটি অতিরিক্ত সময় কল করা হচ্ছে, কিন্তু ফলাফলটি বাতিল করা হবে।

---

### একটি কনটেক্সট ব্যবহার করে মেমোয়াইজড কম্পোনেন্ট আপডেট করা {/*updating-a-memoized-component-using-a-context*/}

যদিও একটি কম্পোনেন্ট মেমোয়াইজ করা হয়, এটি তবুও পুনরায় রেন্ডার হবে যখন এর ব্যবহার করা কনটেক্সট পরিবর্তন হয়। মেমোয়াইজেশন শুধুমাত্র অভিভাবক কম্পোনেন্ট থেকে পাস করা প্রপস নিয়ে কাজ করে।

<Sandpack>

```js
import { createContext, memo, useContext, useState } from 'react';

const ThemeContext = createContext(null);

export default function MyApp() {
  const [theme, setTheme] = useState('dark');

  function handleClick() {
    setTheme(theme === 'dark' ? 'light' : 'dark');
  }

  return (
    <ThemeContext value={theme}>
      <button onClick={handleClick}>
        Switch theme
      </button>
      <Greeting name="Taylor" />
    </ThemeContext>
  );
}

const Greeting = memo(function Greeting({ name }) {
  console.log("Greeting was rendered at", new Date().toLocaleTimeString());
  const theme = useContext(ThemeContext);
  return (
    <h3 className={theme}>Hello, {name}!</h3>
  );
});
```

```css
label {
  display: block;
  margin-bottom: 16px;
}

.light {
  color: black;
  background-color: white;
}

.dark {
  color: white;
  background-color: black;
}
```

</Sandpack>

আপনার কম্পোনেন্টকে কেবল তখনই পুনরায় রেন্ডার করতে দিন যখন কোনো কনটেক্সটের _একটি অংশ_ পরিবর্তন হয়, আপনার কম্পোনেন্টকে দুই ভাগে ভাগ করুন। বাইরের কম্পোনেন্টে কনটেক্সট থেকে প্রয়োজনীয় তথ্য পড়ুন এবং এটি একটি মেমোয়াইজড চাইল্ডে প্রপ হিসেবে পাস করুন।

---

### প্রপস পরিবর্তন সর্বনিম্ন করা {/*minimizing-props-changes*/}

`memo` ব্যবহার করলে, আপনার কম্পোনেন্ট পুনরায় রেন্ডার হয় যখন যেকোনো প্রপ আগের থেকে *shallowly equal* নয়। এর অর্থ হলো React আপনার কম্পোনেন্টের প্রতিটি প্রপকে এর আগের মানের সাথে [`Object.is` comparison](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) ব্যবহার করে তুলনা করে। লক্ষ্য করুন যে `Object.is(3, 3)` `true` হলেও, `Object.is({}, {})` `false`।


`memo` থেকে সর্বাধিক উপকার পেতে, প্রপস পরিবর্তনের সংখ্যা সর্বনিম্ন করুন। উদাহরণস্বরূপ, যদি প্রপটি একটি অবজেক্ট হয়, তাহলে প্যারেন্ট কম্পোনেন্ট প্রতিবার সেই অবজেক্টটি পুনরায় তৈরি করা থেকে বিরত থাকতে [`useMemo`](/reference/react/useMemo) ব্যবহার করুনঃ

```js {5-8}
function Page() {
  const [name, setName] = useState('Taylor');
  const [age, setAge] = useState(42);

  const person = useMemo(
    () => ({ name, age }),
    [name, age]
  );

  return <Profile person={person} />;
}

const Profile = memo(function Profile({ person }) {
  // ...
});
```

প্রপস পরিবর্তন আরও কমানোর ভালো উপায় হলো নিশ্চিত করা যে কম্পোনেন্টটি তার প্রপসে কেবল সর্বনিম্ন প্রয়োজনীয় তথ্য গ্রহণ করে। উদাহরণস্বরূপ, এটি একটি পুরো অবজেক্টের পরিবর্তে পৃথক মানগুলি গ্রহণ করতে পারেঃ

```js {4,7}
function Page() {
  const [name, setName] = useState('Taylor');
  const [age, setAge] = useState(42);
  return <Profile name={name} age={age} />;
}

const Profile = memo(function Profile({ name, age }) {
  // ...
});
```

এমনকি পৃথক মানগুলিও কখনও কখনও কম পরিবর্তিত হওয়া মানগুলিতে project করা যেতে পারে। উদাহরণস্বরূপ, এখানে একটি কম্পোনেন্ট একটি মানের উপস্থিতি নির্দেশ করা একটি বুলিয়ান গ্রহণ করেঃ

```js {3}
function GroupsLanding({ person }) {
  const hasGroups = person.groups !== null;
  return <CallToAction hasGroups={hasGroups} />;
}

const CallToAction = memo(function CallToAction({ hasGroups }) {
  // ...
});
```

যখন আপনার মেমোয়াইজড কম্পোনেন্টে একটি ফাংশন পাস করতে হবে, তখন এটি হয় আপনার কম্পোনেন্টের বাইরে declare করুন যাতে এটি কখনই পরিবর্তিত না হয়, অথবা এর definition পুনরায় রেন্ডারের মধ্যে cache করতে [`useCallback`](/reference/react/useCallback#skipping-re-rendering-of-components) ব্যবহার করুন।

---

### কাস্টম তুলনা ফাংশন নির্দিষ্ট করা {/*specifying-a-custom-comparison-function*/}

বিরল ক্ষেত্রে মেমোয়াইজড কম্পোনেন্টের প্রপস পরিবর্তন সর্বনিম্ন করা অসম্ভব হতে পারে। সেক্ষেত্রে, আপনি একটি কাস্টম তুলনা ফাংশন দিতে পারেন, যা React পুরানো এবং নতুন প্রপসের তুলনা করতে ব্যবহার করবে shallow equality এর পরিবর্তে। এই ফাংশনটি `memo`-র দ্বিতীয় আর্গুমেন্ট হিসেবে পাস করা হয়। এটি কেবল `true` ফেরত দেবে যদি নতুন প্রপস পুরানো প্রপসের মতোই আউটপুট তৈরি করে; অন্যথায় এটি `false` ফেরত দেবে।

```js {3}
const Chart = memo(function Chart({ dataPoints }) {
  // ...
}, arePropsEqual);

function arePropsEqual(oldProps, newProps) {
  return (
    oldProps.dataPoints.length === newProps.dataPoints.length &&
    oldProps.dataPoints.every((oldPoint, index) => {
      const newPoint = newProps.dataPoints[index];
      return oldPoint.x === newPoint.x && oldPoint.y === newPoint.y;
    })
  );
}
```

এটি করলে, আপনার ব্রাউজার ডেভেলপার টুলসের পারফরম্যান্স প্যানেল ব্যবহার করে নিশ্চিত করুন যে আপনার comparison ফাংশনটি কম্পোনেন্টের পুনরায় রেন্ডার করার চেয়ে দ্রুত হচ্ছে। আপনি অবাক হয়ে যেতে পারেন।

যখন আপনি পারফরম্যান্স পরিমাপ করবেন, নিশ্চিত করুন যে React প্রোডাকশন মোডে চলছে।

<Pitfall>

যদি আপনি কাস্টম `arePropsEqual` implementation সরবরাহ করেন, **আপনাকে প্রতিটি প্রপ, ফাংশনসহ তুলনা করতে হবে।** ফাংশনগুলি প্রায়শই প্যারেন্ট কম্পোনেন্টের প্রপস এবং স্টেটের উপর [ক্লোজ করে](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)। যদি আপনি `oldProps.onClick !== newProps.onClick` হলে `true` ফেরত দেন, আপনার কম্পোনেন্ট তার `onClick` হ্যান্ডলারে আগের রেন্ডারের প্রপস এবং স্টেট দেখতে থাকবে, যা খুব বিভ্রান্তিকর বাগের জন্ম দেয়।

`arePropsEqual`-এ deep equality চেক করা এড়িয়ে চলুন, যদি না আপনি শতভাগ নিশ্চিত হন যে আপনি যে ডেটা স্ট্রাকচার নিয়ে কাজ করছেন তার একটি পরিচিত সীমিত গভীরতা রয়েছে। **Deep equality চেক অত্যন্ত ধীর হতে পারে** এবং যদি কেউ পরে ডেটা স্ট্রাকচার পরিবর্তন করে তাহলে আপনার অ্যাপটি অনেক সেকেন্ড ধরে আটকে থাকতে পারে।

</Pitfall>

---

<<<<<<< HEAD
## ট্রাবলশুটিং {/*troubleshooting*/}
### আমার কম্পোনেন্ট পুনরায় রেন্ডার হয় যখন একটি প্রপ অবজেক্ট, অ্যারে, অথবা ফাংশন হয় {/*my-component-rerenders-when-a-prop-is-an-object-or-array*/}
=======
### Do I still need React.memo if I use React Compiler? {/*react-compiler-memo*/}

When you enable [React Compiler](/learn/react-compiler), you typically don't need `React.memo` anymore. The compiler automatically optimizes component re-rendering for you.

Here's how it works:

**Without React Compiler**, you need `React.memo` to prevent unnecessary re-renders:

```js
// Parent re-renders every second
function Parent() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);
    return () => clearInterval(interval);
  }, []);

  return (
    <>
      <h1>Seconds: {seconds}</h1>
      <ExpensiveChild name="John" />
    </>
  );
}

// Without memo, this re-renders every second even though props don't change
const ExpensiveChild = memo(function ExpensiveChild({ name }) {
  console.log('ExpensiveChild rendered');
  return <div>Hello, {name}!</div>;
});
```

**With React Compiler enabled**, the same optimization happens automatically:

```js
// No memo needed - compiler prevents re-renders automatically
function ExpensiveChild({ name }) {
  console.log('ExpensiveChild rendered');
  return <div>Hello, {name}!</div>;
}
```

Here's the key part of what the React Compiler generates:

```js {6-12}
function Parent() {
  const $ = _c(7);
  const [seconds, setSeconds] = useState(0);
  // ... other code ...

  let t3;
  if ($[4] === Symbol.for("react.memo_cache_sentinel")) {
    t3 = <ExpensiveChild name="John" />;
    $[4] = t3;
  } else {
    t3 = $[4];
  }
  // ... return statement ...
}
```

Notice the highlighted lines: The compiler wraps `<ExpensiveChild name="John" />` in a cache check. Since the `name` prop is always `"John"`, this JSX is created once and reused on every parent re-render. This is exactly what `React.memo` does - it prevents the child from re-rendering when its props haven't changed.

The React Compiler automatically:
1. Tracks that the `name` prop passed to `ExpensiveChild` hasn't changed
2. Reuses the previously created JSX for `<ExpensiveChild name="John" />`
3. Skips re-rendering `ExpensiveChild` entirely

This means **you can safely remove `React.memo` from your components when using React Compiler**. The compiler provides the same optimization automatically, making your code cleaner and easier to maintain.

<Note>

The compiler's optimization is actually more comprehensive than `React.memo`. It also memoizes intermediate values and expensive computations within your components, similar to combining `React.memo` with `useMemo` throughout your component tree.

</Note>

---

## Troubleshooting {/*troubleshooting*/}
### My component re-renders when a prop is an object, array, or function {/*my-component-rerenders-when-a-prop-is-an-object-or-array*/}
>>>>>>> 27d86ffe6ec82e3642c6490d2187bae2271020a4

React পুরানো এবং নতুন প্রপসগুলি shallow equality দ্বারা তুলনা করেঃ অর্থাৎ, এটি বিবেচনা করে যে প্রতিটি নতুন প্রপ পুরানো প্রপের সাথে রেফারেন্স-সমান কিনা। যদি আপনি প্রতি বার প্যারেন্ট পুনরায় রেন্ডার হলে একটি নতুন অবজেক্ট বা অ্যারে তৈরি করেন, এমনকি যদি প্রতিটি উপাদান একই হয়, React এটিকে পরিবর্তিত হিসেবে বিবেচনা করবে। একই ভাবে, যদি আপনি প্যারেন্ট কম্পোনেন্ট রেন্ডার করার সময় একটি নতুন ফাংশন তৈরি করেন, React এটিকে পরিবর্তিত হিসেবে বিবেচনা করবে, এমনকি যদি ফাংশনের ডেফিনিশন একই হয়। এটি এড়াতে, [প্রপসগুলি সরল করুন অথবা প্যারেন্ট কম্পোনেন্টে প্রপসগুলি মেমোয়াইজ করুন](#minimizing-props-changes)।
