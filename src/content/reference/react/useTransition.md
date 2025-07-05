---
title: useTransition
---

<Intro>

`useTransition` হলো একটি React হুক যা আপনাকে UI এর একটি অংশ ব্যাকগ্রাউন্ডে রেন্ডার করতে দেয়।

```js
const [isPending, startTransition] = useTransition()
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `useTransition()` {/*usetransition*/}

আপনার কম্পোনেন্টের একেবারে উপরের স্তরে `useTransition` কল করুন যাতে কিছু state আপডেটকে ট্রানজিশন হিসাবে চিহ্নিত করা যায়।

```js
import { useTransition } from 'react';

function TabContainer() {
  const [isPending, startTransition] = useTransition();
  // ...
}
```

[নীচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটারস {/*parameters*/}

`useTransition` কোনো প্যারামিটার নেয় না।

#### রিটার্নস {/*returns*/}

`useTransition` একটি অ্যারে রিটার্ন করে যাতে ঠিক দুটি আইটেম থাকে:

১. `isPending` ফ্ল্যাগ যা আপনাকে জানায় যে একটি পেন্ডিং ট্রানজিশন আছে।
২. [`startTransition` ফাংশন](#starttransition) যা আপনাকে আপডেটগুলিকে ট্রানজিশন হিসাবে চিহ্নিত করতে দেয়।

---

### `startTransition(action)` {/*starttransition*/}

`useTransition` দ্বারা রিটার্ন করা `startTransition` ফাংশনটি আপনাকে একটি আপডেটকে ট্রানজিশন হিসাবে চিহ্নিত করতে দেয়।

```js {6,8}
function TabContainer() {
  const [isPending, startTransition] = useTransition();
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
  // ...
}
```

<Note>
#### `startTransition` এ কল করা ফাংশনগুলিকে "অ্যাকশন" বলা হয়। {/*functions-called-in-starttransition-are-called-actions*/}

`startTransition` এ পাস করা ফাংশনটিকে "অ্যাকশন" বলা হয়। প্রথা অনুযায়ী, `startTransition` এর ভিতরে কল করা যেকোনো কলব্যাক (যেমন একটি কলব্যাক প্রপ) এর নাম `action` হওয়া উচিত অথবা "Action" সাফিক্স যুক্ত হওয়া উচিত:

```js {1,9}
function SubmitButton({ submitAction }) {
  const [isPending, startTransition] = useTransition();

  return (
    <button
      disabled={isPending}
      onClick={() => {
        startTransition(async () => {
          await submitAction();
        });
      }}
    >
      Submit
    </button>
  );
}

```

</Note>



#### প্যারামিটারসমূহ {/*starttransition-parameters*/}

* `action`: একটি ফাংশন যা এক বা একাধিক [`set` ফাংশন](/reference/react/useState#setstate) কল করে কিছু state আপডেট করে। React `action` কে কোনো প্যারামিটার ছাড়াই তাৎক্ষণিকভাবে কল করে এবং `action` ফাংশন কলের সময় সিনক্রোনাসভাবে নির্ধারিত সব state আপডেটকে ট্রানজিশন হিসাবে চিহ্নিত করে। `action` এ যে কোনো অ্যাসিনক কলকে await করা হলে তা ট্রানজিশনে অন্তর্ভুক্ত হবে, তবে বর্তমানে `await` এর পরে যেকোনো `set` ফাংশনকে একটি অতিরিক্ত `startTransition` এ মোড়ানোর প্রয়োজন ([সমস্যা সমাধান](#react-doesnt-treat-my-state-update-after-await-as-a-transition) দেখুন)। ট্রানজিশন হিসাবে চিহ্নিত state আপডেটগুলি [নন-ব্লকিং](#marking-a-state-update-as-a-non-blocking-transition) হবে এবং [অনাকাঙ্ক্ষিত লোডিং ইন্ডিকেটর প্রদর্শন করবে না।](#preventing-unwanted-loading-indicators)

#### রিটার্নস {/*starttransition-returns*/}

`startTransition` কিছু রিটার্ন করে না।

#### সতর্কতা {/*starttransition-caveats*/}

* `useTransition` হল একটি হুক, তাই এটি কেবল কম্পোনেন্ট বা কাস্টম হুকের মধ্যে কল করা যেতে পারে। যদি অন্য কোনো স্থানে (যেমন, একটি ডেটা লাইব্রেরি থেকে) ট্রানজিশন শুরু করার প্রয়োজন হয়, তাহলে স্বতন্ত্র [`startTransition`](/reference/react/startTransition) কল করুন।

* যদি আপনি একটি স্টেটের `সেট` ফাংশনে অ্যাক্সেস পেয়ে থাকেন তবে আপনি একটি ট্রানজিশনে আপডেট wrap করতে পারেন। কোনো প্রপ বা কাস্টম হুক ভ্যালুর রেসপন্সে ট্রানজিশন শুরু করতে চাইলে, [`useDeferredValue`](/reference/react/useDeferredValue) ব্যবহার করার চেষ্টা করুন।

* `startTransition` এ আপনি যে ফাংশন পাস করবেন তা তাৎক্ষণিকভাবে কল হবে, এবং এটি চালানোর সময় ঘটে যাওয়া সমস্ত state আপডেটকে ট্রানজিশন হিসাবে চিহ্নিত করবে। যদি আপনি `setTimeout` এ state আপডেট করার চেষ্টা করেন, উদাহরণস্বরূপ, তবে সেগুলি ট্রানজিশন হিসাবে চিহ্নিত হবে না।

* কোনো async request এর পরে state আপডেট করতে হলে আপনাকে সেগুলিকে আরেকটি `startTransition` এ wrap করতে হবে যাতে সেগুলি ট্রানজিশন হিসাবে চিহ্নিত হয়। এটি একটি পরিচিত সীমাবদ্ধতা যা আমরা ভবিষ্যতে ঠিক করব (দেখুন [সমস্যা সমাধান](#react-doesnt-treat-my-state-update-after-await-as-a-transition))।

* `startTransition` ফাংশনের একটি স্থিতিশীল identity আছে, তাই আপনি প্রায়ই এটিকে Effect dependencies থেকে বাদ দিতে দেখবেন, কিন্তু এটি অন্তর্ভুক্ত করলে Effect fire করবে না। যদি linter আপনাকে কোনো error ছাড়াই একটি dependency বাদ দিতে দেয়, তাহলে এটি নিরাপদ। [Effect dependencies অপসারণ সম্পর্কে আরো জানুন।](/learn/removing-effect-dependencies#move-dynamic-objects-and-functions-inside-your-effect)

* একটি state আপডেট যদি ট্রানজিশন হিসাবে চিহ্নিত হয়, তাহলে অন্যান্য state আপডেট দ্বারা তা বাধাগ্রস্ত হবে। উদাহরণস্বরূপ, যদি আপনি একটি চার্ট কম্পোনেন্টে একটি ট্রানজিশনের মধ্যে আপডেট করেন, কিন্তু তারপর চার্টটি পুনরায় রেন্ডার হওয়ার মাঝখানে একটি input-এ টাইপ শুরু করেন, React ইনপুট আপডেট সম্পর্কিত কাজ সম্পন্ন করার পরে চার্ট কম্পোনেন্টে রেন্ডারিং কাজটি পুনরায় শুরু করবে।

* Transition আপডেটগুলি টেক্সট ইনপুটগুলি নিয়ন্ত্রণের জন্য ব্যবহৃত হতে পারে না।

* যদি একাধিক চলমান Transitions থাকে, React বর্তমানে তাদেরকে একসাথে ব্যাচ করে। এটি একটি সীমাবদ্ধতা যা সম্ভবত ভবিষ্যতের কোনো রিলিজে সরানো হবে।

## ব্যবহারবিধি {/*usage*/}

### Actions দিয়ে নন-ব্লকিং আপডেট সম্পাদন করুন {/*perform-non-blocking-updates-with-actions*/}

Actions তৈরি করতে এবং পেন্ডিং state অ্যাক্সেস করতে আপনার কম্পোনেন্টের একেবারে উপরে `useTransition` কল করুন:

```js [[1, 4, "isPending"], [2, 4, "startTransition"]]
import {useState, useTransition} from 'react';

function CheckoutForm() {
  const [isPending, startTransition] = useTransition();
  // ...
}
```

`useTransition` ঠিক দুটি আইটেম সহ একটি array রিটার্ন করে:

1. <CodeStep step={1}>`isPending` ফ্ল্যাগ</CodeStep> যা আপনাকে জানায় যে একটি পেন্ডিং Transition রয়েছে।
2. <CodeStep step={2}>`startTransition` ফাংশন</CodeStep> যা আপনাকে একটি Action তৈরি করতে দেয়।

একটি Transition শুরু করতে, `startTransition`-এ এইরকম একটি ফাংশন পাস করুন:

```js
import {useState, useTransition} from 'react';
import {updateQuantity} from './api';

function CheckoutForm() {
  const [isPending, startTransition] = useTransition();
  const [quantity, setQuantity] = useState(1);

  function onSubmit(newQuantity) {
    startTransition(async function () {
      const savedQuantity = await updateQuantity(newQuantity);
      startTransition(() => {
        setQuantity(savedQuantity);
      });
    });
  }
  // ...
}
```

`startTransition` এ পাস করা ফাংশনটিকে "Action" বলা হয়। আপনি একটি Action এর মধ্যে state আপডেট করতে এবং (ঐচ্ছিকভাবে) side effects সম্পাদন করতে পারেন, এবং পৃষ্ঠায় ব্যবহারকারীর ইন্টারঅ্যাকশনগুলি ব্লক না করে এই কাজটি ব্যাকগ্রাউন্ডে করা হবে। একটি Transition একাধিক Actions অন্তর্ভুক্ত করতে পারে, এবং যখন একটি Transition চলমান থাকে, আপনার UI রেস্পন্সিভ থাকে। উদাহরণস্বরূপ, যদি ব্যবহারকারী একটি ট্যাবে ক্লিক করে কিন্তু তারপর তাদের মন পরিবর্তন করে এবং অন্য ট্যাবে ক্লিক করে, তাহলে প্রথম আপডেট শেষ হওয়ার জন্য অপেক্ষা না করে দ্বিতীয় ক্লিকটি তাৎক্ষণিকভাবে পরিচালনা করা হবে।

চলমান Transitions সম্পর্কে ব্যবহারকারীকে ফিডব্যাক দিতে, `isPending` state `startTransition` এর প্রথম কল এ `true` হয়ে যায়, এবং সমস্ত Actions সম্পূর্ণ হওয়া এবং চূড়ান্ত state ব্যবহারকারীকে দেখানো পর্যন্ত `true` থাকে। Transitions [অবাঞ্ছিত লোডিং ইন্ডিকেটর প্রতিরোধ করতে](#preventing-unwanted-loading-indicators) Actions এর side effects ক্রমানুসারে সম্পূর্ণ করা নিশ্চিত করে, এবং আপনি `useOptimistic` দিয়ে Transition চলাকালীন তাৎক্ষণিক ফিডব্যাক প্রদান করতে পারেন।

<Recipes titleText="Actions এবং নিয়মিত event handling এর মধ্যে পার্থক্য">

#### একটি Action এ quantity আপডেট করা {/*updating-the-quantity-in-an-action*/}

এই উদাহরণে, `updateQuantity` ফাংশনটি কার্টে আইটেমের quantity আপডেট করার জন্য সার্ভারে একটি অনুরোধ অনুকরণ করে। এই ফাংশনটি *কৃত্রিমভাবে ধীর করা হয়েছে* যাতে অনুরোধটি সম্পূর্ণ করতে কমপক্ষে এক সেকেন্ড সময় লাগে।

দ্রুত একাধিকবার quantity আপডেট করুন। লক্ষ্য করুন যে কোনো অনুরোধ চলমান থাকার সময় pending "Total" state দেখানো হয়, এবং চূড়ান্ত অনুরোধ সম্পূর্ণ হওয়ার পরেই "Total" আপডেট হয়। যেহেতু আপডেটটি একটি Action এ রয়েছে, তাই অনুরোধ চলমান থাকার সময় "quantity" আপডেট করা অব্যাহত থাকতে পারে।

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "beta",
    "react-dom": "beta"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js src/App.js
import { useState, useTransition } from "react";
import { updateQuantity } from "./api";
import Item from "./Item";
import Total from "./Total";

export default function App({}) {
  const [quantity, setQuantity] = useState(1);
  const [isPending, startTransition] = useTransition();

  const updateQuantityAction = async newQuantity => {
    // To access the pending state of a transition,
    // call startTransition again.
    startTransition(async () => {
      const savedQuantity = await updateQuantity(newQuantity);
      startTransition(() => {
        setQuantity(savedQuantity);
      });
    });
  };

  return (
    <div>
      <h1>Checkout</h1>
      <Item action={updateQuantityAction}/>
      <hr />
      <Total quantity={quantity} isPending={isPending} />
    </div>
  );
}
```

```js src/Item.js
import { startTransition } from "react";

export default function Item({action}) {
  function handleChange(event) {
    // To expose an action prop, await the callback in startTransition.
    startTransition(async () => {
      await action(event.target.value);
    })
  }
  return (
    <div className="item">
      <span>Eras Tour Tickets</span>
      <label htmlFor="name">Quantity: </label>
      <input
        type="number"
        onChange={handleChange}
        defaultValue={1}
        min={1}
      />
    </div>
  )
}
```

```js src/Total.js
const intl = new Intl.NumberFormat("en-US", {
  style: "currency",
  currency: "USD"
});

export default function Total({quantity, isPending}) {
  return (
    <div className="total">
      <span>Total:</span>
      <span>
        {isPending ? "🌀 Updating..." : `${intl.format(quantity * 9999)}`}
      </span>
    </div>
  )
}
```

```js src/api.js
export async function updateQuantity(newQuantity) {
  return new Promise((resolve, reject) => {
    // Simulate a slow network request.
    setTimeout(() => {
      resolve(newQuantity);
    }, 2000);
  });
}
```

```css
.item {
  display: flex;
  align-items: center;
  justify-content: start;
}

.item label {
  flex: 1;
  text-align: right;
}

.item input {
  margin-left: 4px;
  width: 60px;
  padding: 4px;
}

.total {
  height: 50px;
  line-height: 25px;
  display: flex;
  align-content: center;
  justify-content: space-between;
}
```

</Sandpack>

This is a basic example to demonstrate how Actions work, but this example does not handle requests completing out of order. When updating the quantity multiple times, it's possible for the previous requests to finish after later requests causing the quantity to update out of order. This is a known limitation that we will fix in the future (see [Troubleshooting](#my-state-updates-in-transitions-are-out-of-order) below).

For common use cases, React provides built-in abstractions such as:
- [`useActionState`](/reference/react/useActionState)
- [`<form>` actions](/reference/react-dom/components/form)
- [Server Functions](/reference/rsc/server-functions)

These solutions handle request ordering for you. When using Transitions to build your own custom hooks or libraries that manage async state transitions, you have greater control over the request ordering, but you must handle it yourself.

<Solution />

#### Action ছাড়াই quantity আপডেট করা {/*updating-the-users-name-without-an-action*/}

এই উদাহরণে, `updateQuantity` ফাংশনটি কার্টে আইটেমের quantity আপডেট করার জন্য সার্ভারে একটি অনুরোধ অনুকরণ করে। এই ফাংশনটি *কৃত্রিমভাবে ধীর করা হয়েছে* যাতে অনুরোধটি সম্পূর্ণ করতে কমপক্ষে এক সেকেন্ড সময় লাগে।

দ্রুত একাধিকবার quantity আপডেট করুন। লক্ষ্য করুন যে কোনো অনুরোধ চলমান থাকার সময় pending "Total" state দেখানো হয়, কিন্তু "quantity" এ প্রতিবার ক্লিক করার জন্য "Total" একাধিকবার আপডেট হয়:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "beta",
    "react-dom": "beta"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js src/App.js
import { useState } from "react";
import { updateQuantity } from "./api";
import Item from "./Item";
import Total from "./Total";

export default function App({}) {
  const [quantity, setQuantity] = useState(1);
  const [isPending, setIsPending] = useState(false);

  const onUpdateQuantity = async newQuantity => {
    // Manually set the isPending State.
    setIsPending(true);
    const savedQuantity = await updateQuantity(newQuantity);
    setIsPending(false);
    setQuantity(savedQuantity);
  };

  return (
    <div>
      <h1>Checkout</h1>
      <Item onUpdateQuantity={onUpdateQuantity}/>
      <hr />
      <Total quantity={quantity} isPending={isPending} />
    </div>
  );
}

```

```js src/Item.js
export default function Item({onUpdateQuantity}) {
  function handleChange(event) {
    onUpdateQuantity(event.target.value);
  }
  return (
    <div className="item">
      <span>Eras Tour Tickets</span>
      <label htmlFor="name">Quantity: </label>
      <input
        type="number"
        onChange={handleChange}
        defaultValue={1}
        min={1}
      />
    </div>
  )
}
```

```js src/Total.js
const intl = new Intl.NumberFormat("en-US", {
  style: "currency",
  currency: "USD"
});

export default function Total({quantity, isPending}) {
  return (
    <div className="total">
      <span>Total:</span>
      <span>
        {isPending ? "🌀 Updating..." : `${intl.format(quantity * 9999)}`}
      </span>
    </div>
  )
}
```

```js src/api.js
export async function updateQuantity(newQuantity) {
  return new Promise((resolve, reject) => {
    // Simulate a slow network request.
    setTimeout(() => {
      resolve(newQuantity);
    }, 2000);
  });
}
```

```css
.item {
  display: flex;
  align-items: center;
  justify-content: start;
}

.item label {
  flex: 1;
  text-align: right;
}

.item input {
  margin-left: 4px;
  width: 60px;
  padding: 4px;
}

.total {
  height: 50px;
  line-height: 25px;
  display: flex;
  align-content: center;
  justify-content: space-between;
}
```

</Sandpack>

A common solution to this problem is to prevent the user from making changes while the quantity is updating:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "beta",
    "react-dom": "beta"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js src/App.js
import { useState, useTransition } from "react";
import { updateQuantity } from "./api";
import Item from "./Item";
import Total from "./Total";

export default function App({}) {
  const [quantity, setQuantity] = useState(1);
  const [isPending, setIsPending] = useState(false);

  const onUpdateQuantity = async event => {
    const newQuantity = event.target.value;
    // Manually set the isPending state.
    setIsPending(true);
    const savedQuantity = await updateQuantity(newQuantity);
    setIsPending(false);
    setQuantity(savedQuantity);
  };

  return (
    <div>
      <h1>Checkout</h1>
      <Item isPending={isPending} onUpdateQuantity={onUpdateQuantity}/>
      <hr />
      <Total quantity={quantity} isPending={isPending} />
    </div>
  );
}

```

```js src/Item.js
export default function Item({isPending, onUpdateQuantity}) {
  return (
    <div className="item">
      <span>Eras Tour Tickets</span>
      <label htmlFor="name">Quantity: </label>
      <input
        type="number"
        disabled={isPending}
        onChange={onUpdateQuantity}
        defaultValue={1}
        min={1}
      />
    </div>
  )
}
```

```js src/Total.js
const intl = new Intl.NumberFormat("en-US", {
  style: "currency",
  currency: "USD"
});

export default function Total({quantity, isPending}) {
  return (
    <div className="total">
      <span>Total:</span>
      <span>
        {isPending ? "🌀 Updating..." : `${intl.format(quantity * 9999)}`}
      </span>
    </div>
  )
}
```

```js src/api.js
export async function updateQuantity(newQuantity) {
  return new Promise((resolve, reject) => {
    // Simulate a slow network request.
    setTimeout(() => {
      resolve(newQuantity);
    }, 2000);
  });
}
```

```css
.item {
  display: flex;
  align-items: center;
  justify-content: start;
}

.item label {
  flex: 1;
  text-align: right;
}

.item input {
  margin-left: 4px;
  width: 60px;
  padding: 4px;
}

.total {
  height: 50px;
  line-height: 25px;
  display: flex;
  align-content: center;
  justify-content: space-between;
}
```

</Sandpack>

This solution makes the app feel slow, because the user must wait each time they update the quantity. It's possible to add more complex handling manually to allow the user to interact with the UI while the quantity is updating, but Actions handle this case with a straight-forward built-in API.

<Solution />

</Recipes>

---

### কম্পোনেন্ট থেকে `action` prop expose করা {/*exposing-action-props-from-components*/}

আপনি একটি কম্পোনেন্ট থেকে একটি `action` prop expose করতে পারেন যাতে একটি parent একটি Action কল করতে পারে।

For example, this `TabButton` component wraps its `onClick` logic in an `action` prop:

```js {8-12}
export default function TabButton({ action, children, isActive }) {
  const [isPending, startTransition] = useTransition();
  if (isActive) {
    return <b>{children}</b>
  }
  return (
    <button onClick={() => {
      startTransition(async () => {
        // await the action that's passed in.
        // This allows it to be either sync or async. 
        await action();
      });
    }}>
      {children}
    </button>
  );
}
```

কারণ প্যারেন্ট কম্পোনেন্ট তার state আপডেট করে `action` এর ভিতরে, সেই state আপডেটটি একটি Transition হিসাবে চিহ্নিত হয়। এর মানে আপনি "Posts" এ ক্লিক করতে পারেন এবং তারপর তাৎক্ষণিকভাবে "Contact" এ ক্লিক করতে পারেন এবং এটি ইউজার ইন্টারঅ্যাকশনগুলি বাধা দেয় না:

<Sandpack>

```js
import { useState } from 'react';
import TabButton from './TabButton.js';
import AboutTab from './AboutTab.js';
import PostsTab from './PostsTab.js';
import ContactTab from './ContactTab.js';

export default function TabContainer() {
  const [tab, setTab] = useState('about');
  return (
    <>
      <TabButton
        isActive={tab === 'about'}
        action={() => setTab('about')}
      >
        About
      </TabButton>
      <TabButton
        isActive={tab === 'posts'}
        action={() => setTab('posts')}
      >
        Posts (slow)
      </TabButton>
      <TabButton
        isActive={tab === 'contact'}
        action={() => setTab('contact')}
      >
        Contact
      </TabButton>
      <hr />
      {tab === 'about' && <AboutTab />}
      {tab === 'posts' && <PostsTab />}
      {tab === 'contact' && <ContactTab />}
    </>
  );
}
```

```js src/TabButton.js active
import { useTransition } from 'react';

export default function TabButton({ action, children, isActive }) {
  const [isPending, startTransition] = useTransition();
  if (isActive) {
    return <b>{children}</b>
  }
  if (isPending) {
    return <b className="pending">{children}</b>;
  }
  return (
    <button onClick={async () => {
      startTransition(async () => {
        // await the action that's passed in.
        // This allows it to be either sync or async. 
        await action();
      });
    }}>
      {children}
    </button>
  );
}
```

```js src/AboutTab.js
export default function AboutTab() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/PostsTab.js
import { memo } from 'react';

const PostsTab = memo(function PostsTab() {
  // Log once. The actual slowdown is inside SlowPost.
  console.log('[ARTIFICIALLY SLOW] Rendering 500 <SlowPost />');

  let items = [];
  for (let i = 0; i < 500; i++) {
    items.push(<SlowPost key={i} index={i} />);
  }
  return (
    <ul className="items">
      {items}
    </ul>
  );
});

function SlowPost({ index }) {
  let startTime = performance.now();
  while (performance.now() - startTime < 1) {
    // Do nothing for 1 ms per item to emulate extremely slow code
  }

  return (
    <li className="item">
      Post #{index + 1}
    </li>
  );
}

export default PostsTab;
```

```js src/ContactTab.js
export default function ContactTab() {
  return (
    <>
      <p>
        You can find me online here:
      </p>
      <ul>
        <li>admin@mysite.com</li>
        <li>+123456789</li>
      </ul>
    </>
  );
}
```

```css
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
```

</Sandpack>

<Note>

When exposing an `action` prop from a component, you should `await` it inside the transition. 

This allows the `action` callback to be either synchronous or asynchronous without requiring an additional `startTransition` to wrap the `await` in the action.

</Note>

---

### পেন্ডিং ভিজ্যুয়াল state প্রদর্শন করা {/*displaying-a-pending-visual-state*/}

আপনি `useTransition` হতে রিটার্ন আসা `isPending` বুলিয়ান মান ব্যবহার করে ব্যবহারকারীকে জানাতে পারেন যে একটি ট্রানজিশন চলছে। উদাহরণস্বরূপ, ট্যাব বোতামটি একটি বিশেষ "pending" ভিজ্যুয়াল state থাকতে পারে:

```js {4-6}
function TabButton({ action, children, isActive }) {
  const [isPending, startTransition] = useTransition();
  // ...
  if (isPending) {
    return <b className="pending">{children}</b>;
  }
  // ...
```

লক্ষ্য করুন যে "Posts" ক্লিক করা এখন কিভাবে আরও প্রতিক্রিয়াশীল মনে হয় কারণ ট্যাব বাটনটি নিজেই তাৎক্ষণিকভাবে আপডেট হয়:

<Sandpack>

```js
import { useState } from 'react';
import TabButton from './TabButton.js';
import AboutTab from './AboutTab.js';
import PostsTab from './PostsTab.js';
import ContactTab from './ContactTab.js';

export default function TabContainer() {
  const [tab, setTab] = useState('about');
  return (
    <>
      <TabButton
        isActive={tab === 'about'}
        action={() => setTab('about')}
      >
        About
      </TabButton>
      <TabButton
        isActive={tab === 'posts'}
        action={() => setTab('posts')}
      >
        Posts (slow)
      </TabButton>
      <TabButton
        isActive={tab === 'contact'}
        action={() => setTab('contact')}
      >
        Contact
      </TabButton>
      <hr />
      {tab === 'about' && <AboutTab />}
      {tab === 'posts' && <PostsTab />}
      {tab === 'contact' && <ContactTab />}
    </>
  );
}
```

```js src/TabButton.js active
import { useTransition } from 'react';

export default function TabButton({ action, children, isActive }) {
  const [isPending, startTransition] = useTransition();
  if (isActive) {
    return <b>{children}</b>
  }
  if (isPending) {
    return <b className="pending">{children}</b>;
  }
  return (
    <button onClick={() => {
      startTransition(async () => {
        await action();
      });
    }}>
      {children}
    </button>
  );
}
```

```js src/AboutTab.js
export default function AboutTab() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/PostsTab.js
import { memo } from 'react';

const PostsTab = memo(function PostsTab() {
  // Log once. The actual slowdown is inside SlowPost.
  console.log('[ARTIFICIALLY SLOW] Rendering 500 <SlowPost />');

  let items = [];
  for (let i = 0; i < 500; i++) {
    items.push(<SlowPost key={i} index={i} />);
  }
  return (
    <ul className="items">
      {items}
    </ul>
  );
});

function SlowPost({ index }) {
  let startTime = performance.now();
  while (performance.now() - startTime < 1) {
    // Do nothing for 1 ms per item to emulate extremely slow code
  }

  return (
    <li className="item">
      Post #{index + 1}
    </li>
  );
}

export default PostsTab;
```

```js src/ContactTab.js
export default function ContactTab() {
  return (
    <>
      <p>
        You can find me online here:
      </p>
      <ul>
        <li>admin@mysite.com</li>
        <li>+123456789</li>
      </ul>
    </>
  );
}
```

```css
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
```

</Sandpack>

---

### অনাকাঙ্ক্ষিত লোডিং ইন্ডিকেটরগুলি প্রতিরোধ করা {/*preventing-unwanted-loading-indicators*/}

এই উদাহরণে, `PostsTab` কম্পোনেন্টটি [use](/reference/react/use) ব্যবহার করে কিছু ডেটা আনয়ন করে। যখন আপনি "Posts" ট্যাবে ক্লিক করেন, তখন `PostsTab` কম্পোনেন্টটি *সাসপেন্ড* হয়, যা কাছাকাছি লোডিং ফলব্যাক প্রদর্শন করে:

<Sandpack>

```js
import { Suspense, useState } from 'react';
import TabButton from './TabButton.js';
import AboutTab from './AboutTab.js';
import PostsTab from './PostsTab.js';
import ContactTab from './ContactTab.js';

export default function TabContainer() {
  const [tab, setTab] = useState('about');
  return (
    <Suspense fallback={<h1>🌀 Loading...</h1>}>
      <TabButton
        isActive={tab === 'about'}
        action={() => setTab('about')}
      >
        About
      </TabButton>
      <TabButton
        isActive={tab === 'posts'}
        action={() => setTab('posts')}
      >
        Posts
      </TabButton>
      <TabButton
        isActive={tab === 'contact'}
        action={() => setTab('contact')}
      >
        Contact
      </TabButton>
      <hr />
      {tab === 'about' && <AboutTab />}
      {tab === 'posts' && <PostsTab />}
      {tab === 'contact' && <ContactTab />}
    </Suspense>
  );
}
```

```js src/TabButton.js
export default function TabButton({ action, children, isActive }) {
  if (isActive) {
    return <b>{children}</b>
  }
  return (
    <button onClick={() => {
      action();
    }}>
      {children}
    </button>
  );
}
```

```js src/AboutTab.js hidden
export default function AboutTab() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/PostsTab.js hidden
import {use} from 'react';
import { fetchData } from './data.js';

function PostsTab() {
  const posts = use(fetchData('/posts'));
  return (
    <ul className="items">
      {posts.map(post =>
        <Post key={post.id} title={post.title} />
      )}
    </ul>
  );
}

function Post({ title }) {
  return (
    <li className="item">
      {title}
    </li>
  );
}

export default PostsTab;
```

```js src/ContactTab.js hidden
export default function ContactTab() {
  return (
    <>
      <p>
        You can find me online here:
      </p>
      <ul>
        <li>admin@mysite.com</li>
        <li>+123456789</li>
      </ul>
    </>
  );
}
```


```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url.startsWith('/posts')) {
    return await getPosts();
  } else {
    throw Error('Not implemented');
  }
}

async function getPosts() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 1000);
  });
  let posts = [];
  for (let i = 0; i < 500; i++) {
    posts.push({
      id: i,
      title: 'Post #' + (i + 1)
    });
  }
  return posts;
}
```

```css
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
```

</Sandpack>

পুরো ট্যাব কন্টেইনার লুকিয়ে একটি লোডিং ইন্ডিকেটর দেখানো ব্যবহারকারীর জন্য অস্বস্তিকর অভিজ্ঞতা তৈরি করে। যদি আপনি `TabButton` এ `useTransition` যোগ করেন, তাহলে আপনি বরং ট্যাব বোতামেই পেন্ডিং state প্রদর্শন করতে পারেন।

লক্ষ্য করুন যে "Posts" ক্লিক করলে আর পুরো ট্যাব কন্টেইনার একটি স্পিনার দিয়ে প্রতিস্থাপিত হয় না:

<Sandpack>

```js
import { Suspense, useState } from 'react';
import TabButton from './TabButton.js';
import AboutTab from './AboutTab.js';
import PostsTab from './PostsTab.js';
import ContactTab from './ContactTab.js';

export default function TabContainer() {
  const [tab, setTab] = useState('about');
  return (
    <Suspense fallback={<h1>🌀 Loading...</h1>}>
      <TabButton
        isActive={tab === 'about'}
        action={() => setTab('about')}
      >
        About
      </TabButton>
      <TabButton
        isActive={tab === 'posts'}
        action={() => setTab('posts')}
      >
        Posts
      </TabButton>
      <TabButton
        isActive={tab === 'contact'}
        action={() => setTab('contact')}
      >
        Contact
      </TabButton>
      <hr />
      {tab === 'about' && <AboutTab />}
      {tab === 'posts' && <PostsTab />}
      {tab === 'contact' && <ContactTab />}
    </Suspense>
  );
}
```

```js src/TabButton.js active
import { useTransition } from 'react';

export default function TabButton({ action, children, isActive }) {
  const [isPending, startTransition] = useTransition();
  if (isActive) {
    return <b>{children}</b>
  }
  if (isPending) {
    return <b className="pending">{children}</b>;
  }
  return (
    <button onClick={() => {
      startTransition(async () => {
        await action();
      });
    }}>
      {children}
    </button>
  );
}
```

```js src/AboutTab.js hidden
export default function AboutTab() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/PostsTab.js hidden
import {use} from 'react';
import { fetchData } from './data.js';

function PostsTab() {
  const posts = use(fetchData('/posts'));
  return (
    <ul className="items">
      {posts.map(post =>
        <Post key={post.id} title={post.title} />
      )}
    </ul>
  );
}

function Post({ title }) {
  return (
    <li className="item">
      {title}
    </li>
  );
}

export default PostsTab;
```

```js src/ContactTab.js hidden
export default function ContactTab() {
  return (
    <>
      <p>
        You can find me online here:
      </p>
      <ul>
        <li>admin@mysite.com</li>
        <li>+123456789</li>
      </ul>
    </>
  );
}
```


```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url.startsWith('/posts')) {
    return await getPosts();
  } else {
    throw Error('Not implemented');
  }
}

async function getPosts() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 1000);
  });
  let posts = [];
  for (let i = 0; i < 500; i++) {
    posts.push({
      id: i,
      title: 'Post #' + (i + 1)
    });
  }
  return posts;
}
```

```css
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
```

</Sandpack>

[Suspense এর সাথে transitions ব্যবহার সম্পর্কে আরও পড়ুন।](/reference/react/Suspense#preventing-already-revealed-content-from-hiding)

<Note>

ট্রানজিশনগুলি কেবল *ইতিমধ্যে প্রকাশিত* কন্টেন্ট (যেমন ট্যাব কন্টেইনার) লুকানো এড়াতে যথেষ্ট দীর্ঘ সময় "অপেক্ষা" করবে। যদি Posts ট্যাবে একটি [নেস্টেড `<Suspense>` সীমানা](/reference/react/Suspense#revealing-nested-content-as-it-loads) থাকত, তবে ট্রানজিশনটি এর জন্য "অপেক্ষা" করত না।

</Note>

---

### Suspense-সংবলিত রাউটার তৈরি করা {/*building-a-suspense-enabled-router*/}

যদি আপনি একটি React ফ্রেমওয়ার্ক অথবা রাউটার তৈরি করেন, আমরা পরামর্শ দিই আপনি আপনার নেভিগেশনগুলিকে ট্রানজিশন হিসেবে চিহ্নিত করুন।

```js {3,6,8}
function Router() {
  const [page, setPage] = useState('/');
  const [isPending, startTransition] = useTransition();

  function navigate(url) {
    startTransition(() => {
      setPage(url);
    });
  }
  // ...
```

এটি তিনটি কারণে পরামর্শ দেয়া হয়:

- [ট্রানজিশনগুলি বাধাগ্রস্ত হতে পারে,](#marking-a-state-update-as-a-non-blocking-transition) যা ইউজারকে রি-রেন্ডার সম্পূর্ণ হওয়ার অপেক্ষা না করে অন্য কিছুতে ক্লিক করতে দেয়।
- [ট্রানজিশনগুলি অনাকাঙ্ক্ষিত লোডিং ইন্ডিকেটরগুলি প্রতিরোধ করে,](#preventing-unwanted-loading-indicators) যা ইউজারকে নেভিগেশনে বিভ্রান্তিকর লাফ এড়াতে সাহায্য করে।
- [ট্রানজিশনগুলি সমস্ত pending actions এর জন্য অপেক্ষা করে](#perform-non-blocking-updates-with-actions) যা ইউজারকে নতুন পৃষ্ঠা দেখানোর আগে side effects সম্পূর্ণ হওয়ার জন্য অপেক্ষা করতে দেয়।

এখানে নেভিগেশনের জন্য ট্রানজিশন ব্যবহার করে একটি খুব সহজ রাউটার উদাহরণ দেওয়া হল।

<Sandpack>

```js src/App.js
import { Suspense, useState, useTransition } from 'react';
import IndexPage from './IndexPage.js';
import ArtistPage from './ArtistPage.js';
import Layout from './Layout.js';

export default function App() {
  return (
    <Suspense fallback={<BigSpinner />}>
      <Router />
    </Suspense>
  );
}

function Router() {
  const [page, setPage] = useState('/');
  const [isPending, startTransition] = useTransition();

  function navigate(url) {
    startTransition(() => {
      setPage(url);
    });
  }

  let content;
  if (page === '/') {
    content = (
      <IndexPage navigate={navigate} />
    );
  } else if (page === '/the-beatles') {
    content = (
      <ArtistPage
        artist={{
          id: 'the-beatles',
          name: 'The Beatles',
        }}
      />
    );
  }
  return (
    <Layout isPending={isPending}>
      {content}
    </Layout>
  );
}

function BigSpinner() {
  return <h2>🌀 Loading...</h2>;
}
```

```js src/Layout.js
export default function Layout({ children, isPending }) {
  return (
    <div className="layout">
      <section className="header" style={{
        opacity: isPending ? 0.7 : 1
      }}>
        Music Browser
      </section>
      <main>
        {children}
      </main>
    </div>
  );
}
```

```js src/IndexPage.js
export default function IndexPage({ navigate }) {
  return (
    <button onClick={() => navigate('/the-beatles')}>
      Open The Beatles artist page
    </button>
  );
}
```

```js src/ArtistPage.js
import { Suspense } from 'react';
import Albums from './Albums.js';
import Biography from './Biography.js';
import Panel from './Panel.js';

export default function ArtistPage({ artist }) {
  return (
    <>
      <h1>{artist.name}</h1>
      <Biography artistId={artist.id} />
      <Suspense fallback={<AlbumsGlimmer />}>
        <Panel>
          <Albums artistId={artist.id} />
        </Panel>
      </Suspense>
    </>
  );
}

function AlbumsGlimmer() {
  return (
    <div className="glimmer-panel">
      <div className="glimmer-line" />
      <div className="glimmer-line" />
      <div className="glimmer-line" />
    </div>
  );
}
```

```js src/Albums.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Albums({ artistId }) {
  const albums = use(fetchData(`/${artistId}/albums`));
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/Biography.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Biography({ artistId }) {
  const bio = use(fetchData(`/${artistId}/bio`));
  return (
    <section>
      <p className="bio">{bio}</p>
    </section>
  );
}
```

```js src/Panel.js
export default function Panel({ children }) {
  return (
    <section className="panel">
      {children}
    </section>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url === '/the-beatles/albums') {
    return await getAlbums();
  } else if (url === '/the-beatles/bio') {
    return await getBio();
  } else {
    throw Error('Not implemented');
  }
}

async function getBio() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 500);
  });

  return `The Beatles were an English rock band,
    formed in Liverpool in 1960, that comprised
    John Lennon, Paul McCartney, George Harrison
    and Ringo Starr.`;
}

async function getAlbums() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 3000);
  });

  return [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];
}
```

```css
main {
  min-height: 200px;
  padding: 10px;
}

.layout {
  border: 1px solid black;
}

.header {
  background: #222;
  padding: 10px;
  text-align: center;
  color: white;
}

.bio { font-style: italic; }

.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}

.glimmer-panel {
  border: 1px dashed #aaa;
  background: linear-gradient(90deg, rgba(221,221,221,1) 0%, rgba(255,255,255,1) 100%);
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}

.glimmer-line {
  display: block;
  width: 60%;
  height: 20px;
  margin: 10px;
  border-radius: 4px;
  background: #f0f0f0;
}
```

</Sandpack>

<Note>

[Suspense-enabled](/reference/react/Suspense) রাউটারগুলি সাধারণত নেভিগেশন আপডেটগুলিকে ডিফল্টভাবে transitions এ মোড়ানোর আশা করা হয়।

</Note>

---

### ব্যবহারকারীদের কাছে ত্রুটি সীমানা ব্যবহার করে ত্রুটি প্রদর্শন করা {/*displaying-an-error-to-users-with-error-boundary*/}

যদি `startTransition` এ পাস করা কোনো ফাংশন কোনো ত্রুটি দেখায়, তাহলে আপনি আপনার ব্যবহারকারীকে সেই ত্রুটির বার্তাটি প্রদর্শন করতে পারেন একটি [এরর বাউন্ডারির](/reference/react/Component#catching-rendering-errors-with-an-error-boundary) মাধ্যমে। এরর বাউন্ডারি ব্যবহার করতে, যে কম্পোনেন্টে আপনি `useTransition` কল করছেন তাকে একটি এরর বাউন্ডারির মধ্যে মোড়ান। একবার `startTransition` এ পাস করা ফাংশনে ত্রুটি ঘটলে, এরর বাউন্ডারির জন্য নির্ধারিত ফলব্যাক প্রদর্শিত হবে।

<Sandpack>

```js src/AddCommentContainer.js active
import { useTransition } from "react";
import { ErrorBoundary } from "react-error-boundary";

export function AddCommentContainer() {
  return (
    <ErrorBoundary fallback={<p>⚠️Something went wrong</p>}>
      <AddCommentButton />
    </ErrorBoundary>
  );
}

function addComment(comment) {
  // For demonstration purposes to show Error Boundary
  if (comment == null) {
    throw new Error("Example Error: An error thrown to trigger error boundary");
  }
}

function AddCommentButton() {
  const [pending, startTransition] = useTransition();

  return (
    <button
      disabled={pending}
      onClick={() => {
        startTransition(() => {
          // Intentionally not passing a comment
          // so error gets thrown
          addComment();
        });
      }}
    >
      Add comment
    </button>
  );
}
```

```js src/App.js hidden
import { AddCommentContainer } from "./AddCommentContainer.js";

export default function App() {
  return <AddCommentContainer />;
}
```

```js src/index.js hidden
import React, { StrictMode } from 'react';
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

```json package.json hidden
{
  "dependencies": {
    "react": "19.0.0-rc-3edc000d-20240926",
    "react-dom": "19.0.0-rc-3edc000d-20240926",
    "react-scripts": "^5.0.0",
    "react-error-boundary": "4.0.3"
  },
  "main": "/index.js"
}
```
</Sandpack>

---

## সমস্যা সমাধান {/*troubleshooting*/}

### একটি ট্রানজিশনে ইনপুট আপডেট করা কাজ করে না {/*updating-an-input-in-a-transition-doesnt-work*/}

আপনি এমন একটি state ভেরিয়েবলের জন্য ট্রানজিশন ব্যবহার করতে পারবেন না যা একটি ইনপুট নিয়ন্ত্রণ করে:

```js {4,10}
const [text, setText] = useState('');
// ...
function handleChange(e) {
  // ❌ Can't use Transitions for controlled input state
  startTransition(() => {
    setText(e.target.value);
  });
}
// ...
return <input value={text} onChange={handleChange} />;
```

এর কারণ হল ট্রানজিশনগুলি নন-ব্লকিং, কিন্তু change ইভেন্টের রেসপন্সে ইনপুট আপডেট করা অবশ্যই একই সময়ে হওয়া উচিত। যদি আপনি টাইপ করার সময় একটি ট্রানজিশন চালাতে চান, তাহলে আপনার দুটি বিকল্প উপায় রয়েছে:

১. আপনি দুটি আলাদা state ভেরিয়েবল ঘোষণা করতে পারেন: একটি ইনপুট state এর জন্য (যা সর্বদা একই সময়ে আপডেট হয়), এবং একটি যা আপনি ট্রানজিশনে আপডেট করবেন। এটি আপনাকে একই সময়ে state ব্যবহার করে ইনপুট নিয়ন্ত্রণ করতে দেয়, এবং বাকি রেন্ডারিং লজিকে ট্রানজিশন state ভেরিয়েবল (যা ইনপুটের পিছনে "বিলম্বিত" হবে) পাস করতে দেয়।
২. বিকল্প হিসেবে, আপনি একটি state ভেরিয়েবল রাখতে পারেন, এবং [`useDeferredValue`](/reference/react/useDeferredValue) যোগ করতে পারেন যা বাস্তব মানের পিছনে "বিলম্বিত" হবে। এটি নতুন মানের সাথে "মেলে যাওয়ার" জন্য নন-ব্লকিং রি-রেন্ডারগুলি স্বয়ংক্রিয়ভাবে ট্রিগার করবে।

---

### React আমার state আপডেটকে ট্রানজিশন হিসেবে গ্রহণ করে না {/*react-doesnt-treat-my-state-update-as-a-transition*/}

যখন আপনি একটি state আপডেটকে ট্রানজিশনে মোড়ান, নিশ্চিত করুন যে এটি `startTransition` কলের *সময়* ঘটে:

```js
startTransition(() => {
  // ✅ Setting state *during* startTransition call
  setPage('/about');
});
```

<<<<<<< HEAD
`startTransition` এ আপনি যে ফাংশন পাস করবেন তা অবশ্যই একই সময়ে হতে হবে।

আপনি এভাবে একটি আপডেটকে ট্রানজিশন হিসেবে চিহ্নিত করতে পারবেন না:
=======
The function you pass to `startTransition` must be synchronous. You can't mark an update as a Transition like this:
>>>>>>> 50d6991ca6652f4bc4c985cf0c0e593864f2cc91

```js
startTransition(() => {
  // ❌ Setting state *after* startTransition call
  setTimeout(() => {
    setPage('/about');
  }, 1000);
});
```

বরং, আপনি এটি করতে পারেন:

```js
setTimeout(() => {
  startTransition(() => {
    // ✅ Setting state *during* startTransition call
    setPage('/about');
  });
}, 1000);
```

<<<<<<< HEAD
একইভাবে, আপনি একটি আপডেটকে এইরকম ট্রানজিশন হিসেবে চিহ্নিত করতে পারবেন না:
=======
---

### React doesn't treat my state update after `await` as a Transition {/*react-doesnt-treat-my-state-update-after-await-as-a-transition*/}

When you use `await` inside a `startTransition` function, the state updates that happen after the `await` are not marked as Transitions. You must wrap state updates after each `await` in a `startTransition` call:
>>>>>>> 50d6991ca6652f4bc4c985cf0c0e593864f2cc91

```js
startTransition(async () => {
  await someAsyncFunction();
  // ❌ Not using startTransition after await
  setPage('/about');
});
```

তবে, এটি এর পরিবর্তে কাজ করে:

```js
startTransition(async () => {
  await someAsyncFunction();
  // ✅ Using startTransition *after* await
  startTransition(() => {
    setPage('/about');
  });
});
```

This is a JavaScript limitation due to React losing the scope of the async context. In the future, when [AsyncContext](https://github.com/tc39/proposal-async-context) is available, this limitation will be removed.

---

### আমি একটি কম্পোনেন্টের বাইরে থেকে `useTransition` কল করতে চাই {/*i-want-to-call-usetransition-from-outside-a-component*/}

আপনি একটি কম্পোনেন্টের বাইরে থেকে `useTransition` কল করতে পারবেন না কারণ এটি একটি Hook। এই ক্ষেত্রে, এর পরিবর্তে standalone [`startTransition`](/reference/react/startTransition) পদ্ধতি ব্যবহার করুন। এটি একইভাবে কাজ করে, কিন্তু এটি `isPending` ইন্ডিকেটর প্রদান করে না।

---

### আমি যে ফাংশন `startTransition`-এ পাস করি তা তাৎক্ষণিকভাবে কার্যকর হয় {/*the-function-i-pass-to-starttransition-executes-immediately*/}

আপনি যদি এই কোডটি চালান, তা 1, 2, 3 প্রিন্ট করবে:

```js {1,3,6}
console.log(1);
startTransition(() => {
  console.log(2);
  setPage('/about');
});
console.log(3);
```

**এটি 1, 2, 3 প্রিন্ট করার কথা।** `startTransition`-এ আপনি যে ফাংশন পাস করেন তা দেরি হয় না। ব্রাউজারের `setTimeout`-এর মতো, এটি পরে কলব্যাক চালায় না। React আপনার ফাংশনটি তাৎক্ষণিকভাবে কার্যকর করে, কিন্তু *এটি চালানোর সময়* নির্ধারিত কোনো state আপডেটগুলি ট্রানজিশন হিসাবে চিহ্নিত হয়। এটি এভাবে কাজ করে বলে আপনি কল্পনা করতে পারেন:

```js
// A simplified version of how React works

let isInsideTransition = false;

function startTransition(scope) {
  isInsideTransition = true;
  scope();
  isInsideTransition = false;
}

function setState() {
  if (isInsideTransition) {
    // ... schedule a Transition state update ...
  } else {
    // ... schedule an urgent state update ...
  }
}
```

### My state updates in Transitions are out of order {/*my-state-updates-in-transitions-are-out-of-order*/}

If you `await` inside `startTransition`, you might see the updates happen out of order.

In this example, the `updateQuantity` function simulates a request to the server to update the item's quantity in the cart. This function *artificially returns the every other request after the previous* to simulate race conditions for network requests.

Try updating the quantity once, then update it quickly multiple times. You might see the incorrect total:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "beta",
    "react-dom": "beta"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js src/App.js
import { useState, useTransition } from "react";
import { updateQuantity } from "./api";
import Item from "./Item";
import Total from "./Total";

export default function App({}) {
  const [quantity, setQuantity] = useState(1);
  const [isPending, startTransition] = useTransition();
  // Store the actual quantity in separate state to show the mismatch.
  const [clientQuantity, setClientQuantity] = useState(1);
  
  const updateQuantityAction = newQuantity => {
    setClientQuantity(newQuantity);

    // Access the pending state of the transition,
    // by wrapping in startTransition again.
    startTransition(async () => {
      const savedQuantity = await updateQuantity(newQuantity);
      startTransition(() => {
        setQuantity(savedQuantity);
      });
    });
  };

  return (
    <div>
      <h1>Checkout</h1>
      <Item action={updateQuantityAction}/>
      <hr />
      <Total clientQuantity={clientQuantity} savedQuantity={quantity} isPending={isPending} />
    </div>
  );
}

```

```js src/Item.js
import {startTransition} from 'react';

export default function Item({action}) {
  function handleChange(e) {
    // Update the quantity in an Action.
    startTransition(async () => {
      await action(e.target.value);
    });
  }  
  return (
    <div className="item">
      <span>Eras Tour Tickets</span>
      <label htmlFor="name">Quantity: </label>
      <input
        type="number"
        onChange={handleChange}
        defaultValue={1}
        min={1}
      />
    </div>
  )
}
```

```js src/Total.js
const intl = new Intl.NumberFormat("en-US", {
  style: "currency",
  currency: "USD"
});

export default function Total({ clientQuantity, savedQuantity, isPending }) {
  return (
    <div className="total">
      <span>Total:</span>
      <div>
        <div>
          {isPending
            ? "🌀 Updating..."
            : `${intl.format(savedQuantity * 9999)}`}
        </div>
        <div className="error">
          {!isPending &&
            clientQuantity !== savedQuantity &&
            `Wrong total, expected: ${intl.format(clientQuantity * 9999)}`}
        </div>
      </div>
    </div>
  );
}
```

```js src/api.js
let firstRequest = true;
export async function updateQuantity(newName) {
  return new Promise((resolve, reject) => {
    if (firstRequest === true) {
      firstRequest = false;
      setTimeout(() => {
        firstRequest = true;
        resolve(newName);
        // Simulate every other request being slower
      }, 1000);
    } else {
      setTimeout(() => {
        resolve(newName);
      }, 50);
    }
  });
}
```

```css
.item {
  display: flex;
  align-items: center;
  justify-content: start;
}

.item label {
  flex: 1;
  text-align: right;
}

.item input {
  margin-left: 4px;
  width: 60px;
  padding: 4px;
}

.total {
  height: 50px;
  line-height: 25px;
  display: flex;
  align-content: center;
  justify-content: space-between;
}

.total div {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

.error {
  color: red;
}
```

</Sandpack>


When clicking multiple times, it's possible for previous requests to finish after later requests. When this happens, React currently has no way to know the intended order. This is because the updates are scheduled asynchronously, and React loses context of the order across the async boundary.

This is expected, because Actions within a Transition do not guarantee execution order. For common use cases, React provides higher-level abstractions like [`useActionState`](/reference/react/useActionState) and [`<form>` actions](/reference/react-dom/components/form) that handle ordering for you. For advanced use cases, you'll need to implement your own queuing and abort logic to handle this.


Example of `useActionState` handling execution order:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "beta",
    "react-dom": "beta"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js src/App.js
import { useState, useActionState } from "react";
import { updateQuantity } from "./api";
import Item from "./Item";
import Total from "./Total";

export default function App({}) {
  // Store the actual quantity in separate state to show the mismatch.
  const [clientQuantity, setClientQuantity] = useState(1);
  const [quantity, updateQuantityAction, isPending] = useActionState(
    async (prevState, payload) => {
      setClientQuantity(payload);
      const savedQuantity = await updateQuantity(payload);
      return savedQuantity; // Return the new quantity to update the state
    },
    1 // Initial quantity
  );

  return (
    <div>
      <h1>Checkout</h1>
      <Item action={updateQuantityAction}/>
      <hr />
      <Total clientQuantity={clientQuantity} savedQuantity={quantity} isPending={isPending} />
    </div>
  );
}

```

```js src/Item.js
import {startTransition} from 'react';

export default function Item({action}) {
  function handleChange(e) {
    // Update the quantity in an Action.
    startTransition(() => {
      action(e.target.value);
    });
  }  
  return (
    <div className="item">
      <span>Eras Tour Tickets</span>
      <label htmlFor="name">Quantity: </label>
      <input
        type="number"
        onChange={handleChange}
        defaultValue={1}
        min={1}
      />
    </div>
  )
}
```

```js src/Total.js
const intl = new Intl.NumberFormat("en-US", {
  style: "currency",
  currency: "USD"
});

export default function Total({ clientQuantity, savedQuantity, isPending }) {
  return (
    <div className="total">
      <span>Total:</span>
      <div>
        <div>
          {isPending
            ? "🌀 Updating..."
            : `${intl.format(savedQuantity * 9999)}`}
        </div>
        <div className="error">
          {!isPending &&
            clientQuantity !== savedQuantity &&
            `Wrong total, expected: ${intl.format(clientQuantity * 9999)}`}
        </div>
      </div>
    </div>
  );
}
```

```js src/api.js
let firstRequest = true;
export async function updateQuantity(newName) {
  return new Promise((resolve, reject) => {
    if (firstRequest === true) {
      firstRequest = false;
      setTimeout(() => {
        firstRequest = true;
        resolve(newName);
        // Simulate every other request being slower
      }, 1000);
    } else {
      setTimeout(() => {
        resolve(newName);
      }, 50);
    }
  });
}
```

```css
.item {
  display: flex;
  align-items: center;
  justify-content: start;
}

.item label {
  flex: 1;
  text-align: right;
}

.item input {
  margin-left: 4px;
  width: 60px;
  padding: 4px;
}

.total {
  height: 50px;
  line-height: 25px;
  display: flex;
  align-content: center;
  justify-content: space-between;
}

.total div {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

.error {
  color: red;
}
```

</Sandpack>
