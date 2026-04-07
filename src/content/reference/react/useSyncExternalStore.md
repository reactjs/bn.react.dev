---
title: useSyncExternalStore
---

<Intro>

`useSyncExternalStore` একটি রিয়েক্ট হুক যা আপনাকে একটি এক্সটার্নাল স্টোরে সাবস্ক্রাইব করতে দেয়।

```js
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)` {/*usesyncexternalstore*/}

একটি এক্সটার্নাল ডেটা স্টোর থেকে কোনো ভ্যালু রিড করতে আপনার কম্পোনেন্টের টপ লেভেলে `useSyncExternalStore` কল করুন।

```js
import { useSyncExternalStore } from 'react';
import { todosStore } from './todoStore.js';

function TodosApp() {
  const todos = useSyncExternalStore(todosStore.subscribe, todosStore.getSnapshot);
  // ...
}
```

এটি স্টোরে থাকা ডেটার স্ন্যাপশট (snapshot) রিটার্ন করে। আপনাকে এর আর্গুমেন্ট হিসেবে দুটি ফাংশন পাস করতে হবে:

1. `subscribe` ফাংশনটি স্টোরে সাবস্ক্রাইব করবে এবং এমন একটি ফাংশন রিটার্ন করবে যা সাবস্ক্রিপশনটি আনসাবস্ক্রাইব করে।
2. `getSnapshot` ফাংশনটি স্টোর থেকে ডেটার স্ন্যাপশট রিড করবে।

[নিচে আরও উদাহরণ দেখুন।](#usage)

#### প্যারামিটারস {/*parameters*/}

* `subscribe`: একটি ফাংশন যা একটিমাত্র `callback` আর্গুমেন্ট নেয় এবং এটিকে স্টোরটিতে সাবস্ক্রাইব করে। যখন স্টোরে কোনো পরিবর্তন হয়, তখন এটিকে প্রদানকৃত `callback` ফাংশনটিকে ইনভোক বা কল করতে হবে, যার ফলে রিয়েক্ট `getSnapshot` কে পুনরায় কল করবে এবং (প্রয়োজন হলে) কম্পোনেন্টটিকে রি-রেন্ডার করবে। `subscribe` ফাংশনটিকে এমন একটি ফাংশন রিটার্ন করতে হবে যা এই সাবস্ক্রিপশনটিকে ক্লিন আপ (clean up) করে দেয়।

* `getSnapshot`: একটি ফাংশন যা স্টোরে থাকা ডেটার একটি স্ন্যাপশট রিটার্ন করে যা কম্পোনেন্টের জন্য প্রয়োজন। যতদিন স্টোরে কোনো পরিবর্তন না আসে, ততদিন `getSnapshot` কে বারবার কল করলেও এটি একই ভ্যালু রিটার্ন করবে। যদি স্টোরটি পরিবর্তিত হয় এবং রিটার্নকৃত ভ্যালুটি ভিন্ন হয় (যা [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) দিয়ে তুলনা করে দেখা হয়), তখন রিয়েক্ট কম্পোনেন্টটিকে রি-রেন্ডার করে।

* **optional** `getServerSnapshot`: একটি ফাংশন যা স্টোরে থাকা ডেটার ইনিশিয়াল স্ন্যাপশট রিটার্ন করে। এটি শুধুমাত্র সার্ভার রেন্ডারিংয়ের সময় এবং ক্লায়েন্টে সার্ভার-রেন্ডার করা কনটেন্টের হাইড্রেশনের (hydration) সময় ব্যবহৃত হয়। সার্ভার স্ন্যাপশটটি অবশ্যই ক্লায়েন্ট এবং সার্ভারের মধ্যে একই হতে হবে, এবং এটি সাধারণত সিরিয়ালাইজ (serialize) করে সার্ভার থেকে ক্লায়েন্টে পাস করা হয়। আপনি যদি এই আর্গুমেন্টটি বাদ দেন, তবে সার্ভারে কম্পোনেন্টটি রেন্ডার করার সময় একটি error থ্রো (throw) হবে।

#### রিটার্নস {/*returns*/}

স্টোরের বর্তমান স্ন্যাপশট যা আপনি আপনার রেন্ডারিং লজিকে ব্যবহার করতে পারবেন।

#### লক্ষ্যনীয় {/*caveats*/}

* `getSnapshot` দ্বারা রিটার্নকৃত স্টোর স্ন্যাপশট অবশ্যই ইমিউটেবল (immutable) হতে হবে। আন্ডারলায়িং স্টোরটিতে মিউটেবল (mutable) ডেটা থাকলে, ডেটা পরিবর্তিত হওয়ার পর একটি নতুন ইমিউটেবল স্ন্যাপশট রিটার্ন করুন। নাহলে, ক্যাশ (cache) করা সর্বশেষ স্ন্যাপশটটি রিটার্ন করুন।

* যদি কোনো রি-রেন্ডারের সময় সম্পূর্ণ ভিন্ন একটি `subscribe` ফাংশন পাস করা হয়, তাহলে রিয়েক্ট নতুন পাস করা `subscribe` ফাংশনটি ব্যবহার করে স্টোরে রি-সাবস্ক্রাইব করবে। আপনি `subscribe` ফাংশনটি কম্পোনেন্টের বাইরে ডিক্লেয়ার করে এটি এড়াতে পারেন।

* যদি কোনো [নন-ব্লকিং ট্রানজিশন আপডেটের (non-blocking Transition update)](/reference/react/useTransition) সময় স্টোরটি মিউটেট (mutate) হয়, রিয়েক্ট তখন ফলব্যাক হিসেবে এই আপডেটটি ব্লকিং (blocking) হিসেবে সম্পন্ন করবে। নির্দিষ্ট করে বললে, প্রতিটি ট্রানজিশন আপডেটের ক্ষেত্রে, DOM-এ পরিবর্তন অ্যাপ্লাই করার ঠিক আগে রিয়েক্ট `getSnapshot` কে দ্বিতীয়বারের মতো কল করবে। যদি এটি প্রথমবার কল করার সময়ের চেয়ে ভিন্ন কোনো ভ্যালু রিটার্ন করে, তবে রিয়েক্ট আপডেটটি একদম শুরু থেকে আবার রিস্টার্ট করবে, এবার এটিকে ব্লকিং আপডেট হিসেবে অ্যাপ্লাই করবে, যাতে নিশ্চিত হওয়া যায় যে স্ক্রিনের প্রতিটা কম্পোনেন্ট স্টোরের একই সংস্করণ প্রতিফলিত করছে।

* `useSyncExternalStore` থেকে রিটার্ন করা কোনো স্টোর ভ্যালুর ওপর ভিত্তি করে কোনো রেন্ডার _সাসপেন্ড (suspend)_ করা রিকমেন্ডেড নয়। এর কারণ হলো এক্সটার্নাল স্টোরে হওয়া মিউটেশনগুলোকে [নন-ব্লকিং ট্রানজিশন আপডেট](/reference/react/useTransition) হিসেবে মার্ক করা যায় না, তাই এগুলো নিকটবর্তী [`Suspense` ফলব্যাক](/reference/react/Suspense) কে ট্রিগার করবে, যা স্ক্রিনে ইতিমধ্যে রেন্ডার করা কনটেন্টকে একটি লোডিং স্পিনার দিয়ে রিপ্লেস করে ফেলবে, যা সাধারণত একটি দুর্বল ইউজার এক্সপেরিয়েন্স তৈরি করে।

  উদাহরণস্বরূপ, নিচে দেখানো কাজগুলো নিরুৎসাহিত করা হয়:

  ```js
  const LazyProductDetailPage = lazy(() => import('./ProductDetailPage.js'));

  function ShoppingApp() {
    const selectedProductId = useSyncExternalStore(...);

    // ❌ Calling `use` with a Promise dependent on `selectedProductId`
    const data = use(fetchItem(selectedProductId))

    // ❌ Conditionally rendering a lazy component based on `selectedProductId`
    return selectedProductId != null ? <LazyProductDetailPage /> : <FeaturedProducts />;
  }
  ```

---

### ব্যবহার {/*usage*/}

### এক্সটার্নাল স্টোরে সাবস্ক্রাইব করা {/*subscribing-to-an-external-store*/}

আপনার বেশিরভাগ রিয়েক্ট কম্পোনেন্ট শুধুমাত্র তাদের [props,](/learn/passing-props-to-a-component) [state,](/reference/react/useState) এবং [context](/reference/react/useContext) থেকে ডেটা রিড করবে। তবে, মাঝে মাঝে এমন কোনো রিয়েক্ট কম্পোনেন্টের প্রয়োজন হতে পারে রিয়েক্টের বাইরে কোনো এক্সটার্নাল স্টোর থেকে ডেটা রিড করার — যা সময়ের সাথে সাথে পরিবর্তিত হয়। এর মধ্যে রয়েছে:

* থার্ড-পার্টি স্টেট ম্যানেজমেন্ট লাইব্রেরি যা রিয়েক্টের বাইরে স্টেট ধরে রাখে।
* ব্রাউজার API যা মিউটেবল ভ্যালু প্রকাশ করে এবং এর পরিবর্তনে সাবস্ক্রাইব করার জন্য ইভেন্ট সরবরাহ করে।

একটি এক্সটার্নাল ডেটা স্টোর থেকে ভ্যালু রিড করতে আপনার কম্পোনেন্টের টপ লেভেলে `useSyncExternalStore` কল করুন।

```js [[1, 5, "todosStore.subscribe"], [2, 5, "todosStore.getSnapshot"], [3, 5, "todos", 0]]
import { useSyncExternalStore } from 'react';
import { todosStore } from './todoStore.js';

function TodosApp() {
  const todos = useSyncExternalStore(todosStore.subscribe, todosStore.getSnapshot);
  // ...
}
```

এটি স্টোরে থাকা ডেটার <CodeStep step={3}>স্ন্যাপশট (snapshot)</CodeStep> রিটার্ন করে। আপনাকে এর আর্গুমেন্ট হিসেবে দুটি ফাংশন পাস করতে হবে:

1. <CodeStep step={1}>`subscribe` ফাংশনটি</CodeStep> স্টোরে সাবস্ক্রাইব করবে এবং এমন একটি ফাংশন রিটার্ন করবে যা এই সাবস্ক্রিপশনটি আনসাবস্ক্রাইব করে।
2. <CodeStep step={2}>`getSnapshot` ফাংশনটি</CodeStep> স্টোর থেকে ডেটার স্ন্যাপশট রিড করবে।

রিয়েক্ট এই ফাংশনগুলি ব্যবহার করবে আপনার কম্পোনেন্টটিকে স্টোরে সাবস্ক্রাইব করে রাখার জন্য এবং কোনো পরিবর্তন এলে সেটিকে রি-রেন্ডার করার জন্য।

উদাহরণস্বরূপ, নিচের স্যান্ডবক্সে, `todosStore` কে এমন একটি এক্সটার্নাল স্টোর হিসেবে ইমপ্লিমেন্ট করা হয়েছে যা রিয়েক্টের বাইরে ডেটা স্টোর করে। `TodosApp` কম্পোনেন্টটি ঐ এক্সটার্নাল স্টোরের সাথে `useSyncExternalStore` হুক দিয়ে সংযুক্ত।

<Sandpack>

```js
import { useSyncExternalStore } from 'react';
import { todosStore } from './todoStore.js';

export default function TodosApp() {
  const todos = useSyncExternalStore(todosStore.subscribe, todosStore.getSnapshot);
  return (
    <>
      <button onClick={() => todosStore.addTodo()}>Add todo</button>
      <hr />
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}
```

```js src/todoStore.js
// This is an example of a third-party store
// that you might need to integrate with React.

// If your app is fully built with React,
// we recommend using React state instead.

let nextId = 0;
let todos = [{ id: nextId++, text: 'Todo #1' }];
let listeners = [];

export const todosStore = {
  addTodo() {
    todos = [...todos, { id: nextId++, text: 'Todo #' + nextId }]
    emitChange();
  },
  subscribe(listener) {
    listeners = [...listeners, listener];
    return () => {
      listeners = listeners.filter(l => l !== listener);
    };
  },
  getSnapshot() {
    return todos;
  }
};

function emitChange() {
  for (let listener of listeners) {
    listener();
  }
}
```

</Sandpack>

<Note>

যখন সম্ভব হবে, আমরা এর বদলে রিয়েক্টের বিল্ট-ইন স্টেট [`useState`](/reference/react/useState) এবং [`useReducer`](/reference/react/useReducer) ব্যবহারের পরামর্শ দিই। `useSyncExternalStore` API বেশিরভাগ তখনই উপকারী যখন আপনাকে বিদ্যমান নন-রিয়েক্ট কোডের সাথে ইন্টিগ্রেট করতে হবে।

</Note>

---

### একটি ব্রাউজার API তে সাবস্ক্রাইব করা {/*subscribing-to-a-browser-api*/}

`useSyncExternalStore` যোগ করার আরও একটি কারণ হলো যখন আপনি ব্রাউজার কর্তৃক সরবরাহকৃত এমন কোনো ভ্যালুতে সাবস্ক্রাইব করতে চান যা সময়ের সাথে সাথে পরিবর্তিত হয়। উদাহরণস্বরূপ, ধরুন আপনি আপনার কম্পোনেন্টে ডিসপ্লে করতে চান যে নেটওয়ার্ক কানেকশনটি অ্যাক্টিভ আছে কি না। ব্রাউজার এই তথ্যটি [`navigator.onLine`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/onLine) নামের একটি প্রপার্টির মাধ্যমে প্রদান করে।

এই ভ্যালুটি রিয়েক্টের অগোচরে পরিবর্তিত হতে পারে, তাই আপনার উচিত `useSyncExternalStore` এর মাধ্যমে এটি রিড করা।

```js
import { useSyncExternalStore } from 'react';

function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  // ...
}
```

`getSnapshot` ফাংশনটিকে ইমপ্লিমেন্ট করার জন্য ব্রাউজার API থেকে বর্তমান ভ্যালুটি রিড করুন:

```js
function getSnapshot() {
  return navigator.onLine;
}
```

এরপরে, আপনাকে `subscribe` ফাংশনটি ইমপ্লিমেন্ট করতে হবে। উদাহরণস্বরূপ, যখন `navigator.onLine` পরিবর্তিত হয়, তখন ব্রাউজার `window` অবজেক্টের ওপর [`online`](https://developer.mozilla.org/en-US/docs/Web/API/Window/online_event) এবং [`offline`](https://developer.mozilla.org/en-US/docs/Web/API/Window/offline_event) ইভেন্টগুলো ফায়ার (fire) করে। আপনাকে ঐ নির্দিষ্ট ইভেন্টগুলোর কারেসপন্ডিং `callback` আর্গুমেন্টটিকে সাবস্ক্রাইব করতে হবে, এবং এরপর একটি ফাংশন রিটার্ন করতে হবে যা এই সাবস্ক্রিপশনগুলোকে ক্লিন আপ (clean up) করবে:

```js
function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}
```

এখন রিয়েক্ট জানে কীভাবে এক্সটার্নাল `navigator.onLine` API থেকে ভ্যালুটি রিড করতে হবে এবং কীভাবে এর পরিবর্তনগুলোতে সাবস্ক্রাইব করতে হবে। আপনার ডিভাইসটিকে নেটওয়ার্ক থেকে ডিসকানেক্ট করুন এবং খেয়াল করুন কীভাবে কম্পোনেন্টটি রেসপন্সে রি-রেন্ডার হয়:

<Sandpack>

```js
import { useSyncExternalStore } from 'react';

export default function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function getSnapshot() {
  return navigator.onLine;
}

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}
```

</Sandpack>

---

### কোনো কাস্টম হুকে লজিক এক্সট্র্যাক্ট করা {/*extracting-the-logic-to-a-custom-hook*/}

সাধারণত আপনি আপনার কম্পোনেন্টে সরাসরি `useSyncExternalStore` লিখবেন না। এর পরিবর্তে, আপনার নিজস্ব কাস্টম হুক থেকে এটি কল করাটাই সচরাচর ব্যবহৃত হয়। এটি আপনাকে বিভিন্ন কম্পোনেন্টে একই এক্সটার্নাল স্টোর ব্যবহার করতে দেয়। 

উদাহরণস্বরূপ, এই কাস্টম `useOnlineStatus` হুকটি ট্র্যাক করে যে নেটওয়ার্কটি অনলাইনে আছে কি না:

```js {3,6}
import { useSyncExternalStore } from 'react';

export function useOnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return isOnline;
}

function getSnapshot() {
  // ...
}

function subscribe(callback) {
  // ...
}
```

এখন বিভিন্ন কম্পোনেন্ট কোনো ভেতরের ইমপ্লিমেন্টেশনের পুনরাবৃত্তি ছাড়াই `useOnlineStatus` কে কল করতে পারবে:

<Sandpack>

```js
import { useOnlineStatus } from './useOnlineStatus.js';

function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}

export default function App() {
  return (
    <>
      <SaveButton />
      <StatusBar />
    </>
  );
}
```

```js src/useOnlineStatus.js
import { useSyncExternalStore } from 'react';

export function useOnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return isOnline;
}

function getSnapshot() {
  return navigator.onLine;
}

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}
```

</Sandpack>

---

### সার্ভার রেন্ডারিংয়ের সাপোর্ট যুক্ত করা {/*adding-support-for-server-rendering*/}

যদি আপনার রিয়েক্ট অ্যাপ্লিকেশনটি [সার্ভার রেন্ডারিং](/reference/react-dom/server) ব্যবহার করে, তবে আপনার রিয়েক্ট কম্পোনেন্ট ব্রাউজার এনভায়রনমেন্টের বাইরেও রান করবে যাতে ইনিশিয়াল (প্রাথমিক) HTML তৈরি করা যায়। এক্সটার্নাল স্টোরের সাথে সংযুক্ত থাকতে এটি বেশ কয়েকটি চ্যালেঞ্জ তৈরি করে:

- আপনি যদি কোনো ব্রাউজার-অনলি API এর সাথে কানেক্ট করতে চান, তবে সেটি কাজ করবে না কারণ সার্ভারে এর কোনো অস্তিত্ব নেই।
- যদি আপনি কোনো থার্ড-পার্টি ডেটা স্টোরের সাথে কানেক্ট করেন, আপনাকে লক্ষ্য রাখতে হবে যেন এর ডেটা সার্ভার এবং ক্লায়েন্টের মধ্যে মিলে যায় (match)।

এই সমস্যাগুলোর সমাধান করতে, `useSyncExternalStore`-এর তৃতীয় আর্গুমেন্ট হিসেবে একটি `getServerSnapshot` ফাংশন পাস করুন:

```js {4,12-14}
import { useSyncExternalStore } from 'react';

export function useOnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot);
  return isOnline;
}

function getSnapshot() {
  return navigator.onLine;
}

function getServerSnapshot() {
  return true; // Always show "Online" for server-generated HTML
}

function subscribe(callback) {
  // ...
}
```

`getServerSnapshot` ফাংশনটি `getSnapshot` এর মতোই, কিন্তু এটি শুধু দুটি পরিস্থিতিতে রান করে:

- এটি HTML জেনারেট করার সময় সার্ভারে রান করে।
- এটি [হাইড্রেশনের (hydration)](/reference/react-dom/client/hydrateRoot) সময় ক্লায়েন্টে রান করে, অর্থাৎ যখন রিয়েক্ট সার্ভারের HTML কে ইন্টারঅ্যাক্টিভ করে তোলে।

এটি আপনাকে ইনিশিয়াল স্ন্যাপশট ভ্যালু প্রদান করার সুযোগ দেয় যা অ্যাপটি পুরোপুরি ইন্টারঅ্যাক্টিভ হওয়ার আগে ব্যবহৃত হবে। সার্ভার রেন্ডারিংয়ের জন্য যদি কোনো অর্থপূর্ণ ইনিশিয়াল ভ্যালু না থাকে, তবে এই আর্গুমেন্টটি বাদ দিন যাতে করে শুধুমাত্র [ক্লায়েন্টে রেন্ডার করতে বাধ্য করা যায়।](/reference/react/Suspense#providing-a-fallback-for-server-errors-and-server-only-content)

<Note>

নিশ্চিত করুন যে `getServerSnapshot` ইনিশিয়াল ক্লায়েন্ট রেন্ডারের সময় হুবহু একই ডেটা রিটার্ন করে যা এটি সার্ভারে রিটার্ন করেছিল। উদাহরণস্বরূপ, যদি `getServerSnapshot` সার্ভারে আগে থেকে পপুলেট (populate) করা কিছু স্টোর কনটেন্ট রিটার্ন করে থাকে, তবে আপনাকে এই কনটেন্ট ক্লায়েন্টে ট্রান্সফার করতে হবে। এটি করার একটি উপায় হলো সার্ভার রেন্ডারিংয়ের সময় একটি `<script>` ট্যাগ এমিট করা যা `window.MY_STORE_DATA` এর মতো কোনো গ্লোবাল সেট করবে, এবং ক্লায়েন্টের `getServerSnapshot` এ ওই গ্লোবালটি রিড করা। আপনার এক্সটার্নাল স্টোরটির সাধারণত এটি কীভাবে করতে হবে সেই বিষয়ে নির্দেশনা দেওয়া উচিত।

</Note>

---

## Troubleshooting {/*troubleshooting*/}

### I'm getting an error: "The result of `getSnapshot` should be cached" {/*im-getting-an-error-the-result-of-getsnapshot-should-be-cached*/}

This error means your `getSnapshot` function returns a new object every time it's called, for example:

```js {2-5}
function getSnapshot() {
  // 🔴 Do not return always different objects from getSnapshot
  return {
    todos: myStore.todos
  };
}
```

React will re-render the component if `getSnapshot` return value is different from the last time. This is why, if you always return a different value, you will enter an infinite loop and get this error.

Your `getSnapshot` object should only return a different object if something has actually changed. If your store contains immutable data, you can return that data directly:

```js {2-3}
function getSnapshot() {
  // ✅ You can return immutable data
  return myStore.todos;
}
```

If your store data is mutable, your `getSnapshot` function should return an immutable snapshot of it. This means it *does* need to create new objects, but it shouldn't do this for every single call. Instead, it should store the last calculated snapshot, and return the same snapshot as the last time if the data in the store has not changed. How you determine whether mutable data has changed depends on your mutable store.

---

### My `subscribe` function gets called after every re-render {/*my-subscribe-function-gets-called-after-every-re-render*/}

This `subscribe` function is defined *inside* a component so it is different on every re-render:

```js {2-5}
function ChatIndicator() {
  // 🚩 Always a different function, so React will resubscribe on every re-render
  function subscribe() {
    // ...
  }
  
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);

  // ...
}
```
  
React will resubscribe to your store if you pass a different `subscribe` function between re-renders. If this causes performance issues and you'd like to avoid resubscribing, move the `subscribe` function outside:

```js {1-4}
// ✅ Always the same function, so React won't need to resubscribe
function subscribe() {
  // ...
}

function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  // ...
}
```

Alternatively, wrap `subscribe` into [`useCallback`](/reference/react/useCallback) to only resubscribe when some argument changes:

```js {2-5}
function ChatIndicator({ userId }) {
  // ✅ Same function as long as userId doesn't change
  const subscribe = useCallback(() => {
    // ...
  }, [userId]);
  
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);

  // ...
}
```
