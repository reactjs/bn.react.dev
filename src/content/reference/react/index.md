---
title: "React-এর বিল্ট-ইন হুক্স"
---

<Intro>

*হুক্স*  আপনাকে আপনার কম্পোনেন্ট থেকে React-এর বিভিন্ন ফিচার ব্যবহার করতে দেয়। আপনি বিল্ট-ইন হুকগুলো ব্যবহার করতে পারেন অথবা তাদের সংযোজন করে আপনার নিজস্ব হুক তৈরি করতে পারেন। এই পেজে React-এর সব বিল্ট-ইন হুকগুলোর তালিকা করা আছে।

</Intro>

---

## State হুক্স {/*state-hooks*/}

*State*  হুকগুলো আপনার কম্পোনেন্টের মধ্যে [ব্যবহৃত "তথ্য সংরক্ষণ" করতে দেয়](/learn/state-a-components-memory)। উদাহরণস্বরূপ, একটি ফর্ম কম্পোনেন্ট স্টেট ব্যবহার করে ইনপুট ভ্যালু সংরক্ষণ করতে পারে, আর একটি ইমেজ গ্যালারি কম্পোনেন্ট স্টেট ব্যবহার করে সিলেক্টেড ইমেজ ইনডেক্স সংরক্ষণ করতে পারে।

কম্পোনেন্টে স্টেট যুক্ত করতে আপনি নিচের হুকগুলোর একটি ব্যবহার করতে পারেন:

* [`useState`](/reference/react/useState) একটি স্টেট ভ্যারিয়েবল ডিক্লেয়ার করে যা আপনি সরাসরি আপডেট করতে পারেন।
* [`useReducer`](/reference/react/useReducer) একটি স্টেট ভ্যারিয়েবল ডিক্লেয়ার করে যা একটি [reducer ফাংশনের](/learn/extracting-state-logic-into-a-reducer) মধ্যে আপডেট করা হয়।

```js
function ImageGallery() {
  const [index, setIndex] = useState(0);
  // ...
```

---

## Context হুক্স {/*context-hooks*/}

*Context*  হুকগুলো একটি কম্পোনেন্টে [দূরবর্তী যেকোনো প্যারেন্ট কম্পোনেন্ট থেকে প্রপ্‌স হিসেবে না পাঠিয়ে সরাসরি তথ্য পাঠাতে দেয়](/learn/passing-props-to-a-component)। উদাহরণস্বরূপ, আপনার অ্যাপের টপ-লেভেল কম্পোনেন্ট নিচের সকল কম্পোনেন্টের মধ্যে বর্তমান UI থিম পাঠাতে পারে, সেটি যত গভীর হোক না কেন।
 
* [`createContext`](/reference/react/createContext) একটি কনটেক্সট পড়ে এবং সেটিতে subscribe করে।

```js
function Button() {
  const theme = useContext(ThemeContext);
  // ...
```

---

## Ref হুক্স {/*ref-hooks*/}

*Ref*  হুকগুলো একটি কম্পোনেন্টের [তথ্য সংরক্ষণ করতে দেয় যা রেন্ডারিং এর জন্য ব্যবহৃত হয় না,](/learn/referencing-values-with-refs) যেমন একটি DOM নোড বা timeout ID। স্টেট আপডেট করলে যেমন কম্পোনেন্ট রি-রেন্ডার হয়, ref আপডেট করলে কিন্তু কম্পোনেন্ট রি-রেন্ডার হয় না। Ref হুকগুলো হচ্ছে React-এর প্যারাডাইম থেকে বের হওয়ার একটি "escape hatch"। এগুলি তখনই ব্যবহার করা যেতে পারে যখন আপনার কোনো non-React সিস্টেম এর সাথে কাজ করতে হয়, যেমন ব্রাউজারের বিল্ট-ইন API।

* [`useRef`](/reference/react/useRef) একটি ref ডিক্লেয়ার করে। আপনি এর মধ্যে যেকোনো ভ্যালু রাখতে পারেন, কিন্তু সবচেয়ে বেশি এটি DOM নোড রাখতে ব্যবহৃত হয়।
* [`useImperativeHandle`](/reference/react/useImperativeHandle) আপনার কম্পোনেন্টের ref কাস্টমাইজ করতে দেয়। এটি খুব কমই ব্যবহৃত হয়।
 
```js
function Form() {
  const inputRef = useRef(null);
  // ...
```

---

## Effect হুক্স {/*effect-hooks*/}

*Effect*  হুকগুলো একটি কম্পোনেন্টকে [বাইরের সিস্টেমের সাথে সংযোগ করে এবং সিংক্রোনাইজ করে](/learn/synchronizing-with-effects)। এটি নেটওয়ার্ক, ব্রাউজার DOM, অ্যানিমেশন, বিভিন্ন non-React কোড এবং বাইরের যেকোনো UI লাইব্রেরির সাথে সংযোগ স্থাপন করতে ব্যবহৃত হয়।

* [`useEffect`](/reference/react/useEffect) একটি কম্পোনেন্টকে বাইরের সিস্টেমের সাথে সংযোগ করে।

```js
function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);
  // ...
```

Effect হুকগুলো React-এর প্যারাডাইমের একটি "escape hatch"। আপনার অ্যাপ্লিকেশানের ডেটা ফ্লো সুসমন্বিত করার জন্য Effect ব্যবহার করবেন না। যদি আপনি কোনো বাইরের সিস্টেমের সাথে ইন্টারেক্ট না করেন, তাহলে আপনার [কোন Effect-এর দরকার নাও হতে পারে।](/learn/you-might-not-need-an-effect)

`useEffect` এর দুইটি অপেক্ষাকৃত কম ব্যবহৃত ভ্যারিয়েশন আছে যা টাইমিং নিয়ে ভিন্নতা রাখেঃ

* [`useLayoutEffect`](/reference/react/useLayoutEffect) ব্রাউজারের রি-পেইন্ট করার আগে কল হয়। আপনি এখানে layout পরিমাপ করতে পারেন।
* [`useInsertionEffect`](/reference/react/useInsertionEffect) React DOM-এ পরিবর্তন করার আগে কল হয়। লাইব্রেরিগুলি এখানে ডায়নামিক CSS ইনসার্ট করতে পারে।

---

## Performance হুক্স {/*performance-hooks*/}

রি-রেন্ডারিং অপ্টিমাইজ করার একটি সাধারণ উপায় হল অপ্রয়োজনীয় কাজ এড়িয়ে যাওয়া। যেমন, আপনি React-কে জানিয়ে দিতে পারেন যেন একটি cache করা ক্যালকুলেশন ব্যবহার করে অথবা একটি রি-রেন্ডার এড়িয়ে যায় যদি ডেটা পূর্বের রেন্ডার থেকে পরিবর্তিত না হয়ে থাকে।

অপ্রয়োজনীয় ক্যালকুলেশন এবং রি-রেন্ডারিং এড়িয়ে যাওয়ার জন্য, এই হুকগুলোর মধ্যে থেকে একটি ব্যবহার করতে পারেনঃ

- [`useMemo`](/reference/react/useMemo) আপনাকে একটি ব্যয়বহুল ক্যালকুলেশন cache করে রাখতে দেয়।
- [`useCallback`](/reference/react/useCallback) একটি অপটিমাইজড কম্পোনেন্টে পাঠানোর আগে একটি ফাংশন ডেফিনিশন cache করে রাখতে দেয়।

```js
function TodoList({ todos, tab, theme }) {
  const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
  // ...
}
```

কখনও কখনও, আপনি রি-রেন্ডারিং এড়িয়ে যেতে পারবেন না কারণ স্ক্রীনটিকে আসলেই আপডেট করতে হবে। এই ক্ষেত্রে, আপনি ব্লকিং আপডেটগুলি যা সিংক্রোনাস হতে হবে (যেমন ইনপুটে টাইপ করা) এবং নন-ব্লকিং আপডেটগুলি যা ব্যবহারকারীর ইন্টারফেস ব্লক করতে হয় না (যেমন একটি চার্ট আপডেট করা) আলাদা করে পারফর্মেন্স বৃদ্ধি করতে পারেন।

রি-রেন্ডারিং অগ্রাধিকার দেওয়ার জন্য, এই হুকগুলোর মধ্যে থেকে একটি ব্যবহার করতে পারেনঃ

- [`useTransition`](/reference/react/useTransition) আপনাকে একটি স্টেট ট্রানজিশনকে ব্লক না করে অন্যান্য আপডেটগুলি তার মধ্যে অব্যাহত রাখতে দেয়।
- [`useDeferredValue`](/reference/react/useDeferredValue) আপনাকে একটি অপ্রয়োজনীয় অংশের UI আপডেট পিছিয়ে দেয় এবং অন্যান্য অংশগুলিকে প্রথমে আপডেট করতে দেয়।

---

<<<<<<< HEAD
## অন্যান্য হুক্স {/*other-hooks*/}
=======
## Resource Hooks {/*resource-hooks*/}

*Resources* can be accessed by a component without having them as part of their state. For example, a component can read a message from a Promise or read styling information from a context.

To read a value from a resource, use this Hook:

- [`use`](/reference/react/use) lets you read the value of a resource like a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) or [context](/learn/passing-data-deeply-with-context).

```js
function MessageComponent({ messagePromise }) {
  const message = use(messagePromise);
  const theme = use(ThemeContext);
  // ...
}
```

---

## Other Hooks {/*other-hooks*/}
>>>>>>> bdc60c26848820239db732b7218d41f4c2b204af

এই হুকগুলো মূলত লাইব্রেরি লেখকদের জন্য প্রয়োজনীয় এবং অ্যাপ্লিকেশন কোডে খুব একটা ব্যবহৃত হয় না।

- [`useDebugValue`](/reference/react/useDebugValue) আপনাকে আপনার কাস্টম হুকের জন্য React ডেভটুলসের প্রদর্শিত লেবেল কাস্টমাইজ করতে দেয়।
- [`useId`](/reference/react/useId) একটি কম্পোনেন্টকে একটি ইউনিক ID দিয়ে সংযুক্ত করে। এটি সাধারণত অ্যাক্সেসিবিলিটি API-এর সঙ্গে ব্যবহার করা হয়।
- [`useSyncExternalStore`](/reference/react/useSyncExternalStore) একটি কম্পোনেন্টকে একটি বাহ্যিক স্টোরে subscribe করতে দেয়।

---

## আপনার নিজস্ব হুক {/*your-own-hooks*/}

আপনি নিজেও জাভাস্ক্রিপ্ট ফাংশন হিসাবে [আপনার নিজস্ব কাস্টম হুক বানাতে পারেন।](/learn/reusing-logic-with-custom-hooks#extracting-your-own-custom-hook-from-a-component)