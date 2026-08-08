---
title: 'Effect Dependency সরানো'
---

<Intro>

যখন আপনি একটি Effect লিখেন, linter যাচাই করবে যে আপনি প্রতিটি reactive value (যেমন props এবং state) যা Effect পড়ে তা আপনার Effect এর dependency এর তালিকায় অন্তর্ভুক্ত করেছেন। এটি নিশ্চিত করে যে আপনার Effect আপনার কম্পোনেন্টের সর্বশেষ props এবং state এর সাথে সিঙ্ক্রোনাইজড থাকে। অপ্রয়োজনীয় dependency আপনার Effect কে খুব ঘন ঘন চালু করতে পারে, এমনকি একটি infinite loop তৈরি করতে পারে। আপনার Effect থেকে অপ্রয়োজনীয় dependency পর্যালোচনা এবং সরাতে এই গাইড অনুসরণ করুন।

</Intro>

<YouWillLearn>

- কিভাবে infinite Effect dependency loop ঠিক করবেন
- যখন আপনি একটি dependency সরাতে চান তখন কি করতে হবে
- কিভাবে আপনার Effect থেকে একটি মান পড়বেন এটির প্রতি "react" না করে
- কিভাবে এবং কেন object এবং function dependency এড়াতে হবে
- কেন dependency linter suppress করা বিপজ্জনক, এবং পরিবর্তে কি করতে হবে

</YouWillLearn>

## Dependency কোডের সাথে মিলতে হবে {/*dependencies-should-match-the-code*/}

যখন আপনি একটি Effect লিখেন, আপনি প্রথমে নির্দিষ্ট করেন কিভাবে [start এবং stop](/learn/lifecycle-of-reactive-effects#the-lifecycle-of-an-effect) করতে হবে যা আপনি আপনার Effect দিয়ে করতে চান:

```js {5-7}
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  	// ...
}
```

তারপর, যদি আপনি Effect dependency খালি (`[]`) রাখেন, linter সঠিক dependency সুপারিশ করবে:

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []); // <-- Fix the mistake here!
  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
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
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

```js src/chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

linter যা বলে সেই অনুযায়ী সেগুলি পূরণ করুন:

```js {6}
function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
}
```

[Effect "react" করে reactive value এর প্রতি।](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) যেহেতু `roomId` একটি reactive value (এটি একটি re-render এর কারণে পরিবর্তিত হতে পারে), linter যাচাই করে যে আপনি এটি একটি dependency হিসাবে নির্দিষ্ট করেছেন। যদি `roomId` একটি ভিন্ন মান পায়, React আপনার Effect পুনরায় সিঙ্ক্রোনাইজ করবে। এটি নিশ্চিত করে যে chat নির্বাচিত room এর সাথে সংযুক্ত থাকে এবং dropdown এর প্রতি "react" করে:


<Sandpack>

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
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

```js src/chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

### একটি dependency সরাতে, প্রমাণ করুন যে এটি একটি dependency নয় {/*to-remove-a-dependency-prove-that-its-not-a-dependency*/}

লক্ষ্য করুন যে আপনি আপনার Effect এর dependency "বেছে নিতে" পারবেন না। আপনার Effect এর কোড দ্বারা ব্যবহৃত প্রতিটি <CodeStep step={2}>reactive value</CodeStep> অবশ্যই আপনার dependency তালিকায় ডিক্লেয়ার করতে হবে। dependency তালিকা আশেপাশের কোড দ্বারা নির্ধারিত হয়:

```js [[2, 3, "roomId"], [2, 5, "roomId"], [2, 8, "roomId"]]
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) { // এটি একটি reactive value
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // এই Effect সেই reactive value পড়ে
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ তাই আপনাকে অবশ্যই সেই reactive value টি আপনার Effect এর dependency হিসাবে নির্দিষ্ট করতে হবে
  // ...
}
```

[Reactive value](/learn/lifecycle-of-reactive-effects#all-variables-declared-in-the-component-body-are-reactive) এর মধ্যে রয়েছে props এবং সমস্ত variable এবং function যা সরাসরি আপনার কম্পোনেন্টের ভিতরে ডিক্লেয়ার করা হয়েছে। যেহেতু `roomId` একটি reactive value, আপনি এটি dependency তালিকা থেকে সরাতে পারবেন না। linter এটি অনুমতি দেবে না:

```js {8}
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []); // 🔴 React Hook useEffect has a missing dependency: 'roomId'
  // ...
}
```

এবং linter সঠিক হবে! যেহেতু `roomId` সময়ের সাথে পরিবর্তিত হতে পারে, এটি আপনার কোডে একটি bug introduce করবে।

**একটি dependency সরাতে, linter কে "প্রমাণ" করুন যে এটি একটি dependency হওয়ার *প্রয়োজন নেই*।** উদাহরণস্বরূপ, আপনি `roomId` কে আপনার কম্পোনেন্টের বাইরে সরাতে পারেন এটি প্রমাণ করতে যে এটি reactive নয় এবং re-render এ পরিবর্তিত হবে না:

```js {2,9}
const serverUrl = 'https://localhost:1234';
const roomId = 'music'; // আর reactive value নয়

function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
}
```

এখন যেহেতু `roomId` একটি reactive value নয় (এবং re-render এ পরিবর্তিত হতে পারে না), এটি একটি dependency হওয়ার প্রয়োজন নেই:

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';
const roomId = 'music';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []);
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js src/chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

এই কারণেই আপনি এখন একটি [খালি (`[]`) dependency তালিকা](/learn/lifecycle-of-reactive-effects#what-an-effect-with-empty-dependencies-means) নির্দিষ্ট করতে পারেন। আপনার Effect *সত্যিই* আর কোনো reactive value এর উপর নির্ভর করে না, তাই এটি *সত্যিই* পুনরায় চালু হওয়ার প্রয়োজন নেই যখন কম্পোনেন্টের কোনো props বা state পরিবর্তিত হয়।

### Dependency পরিবর্তন করতে, কোড পরিবর্তন করুন {/*to-change-the-dependencies-change-the-code*/}

আপনি হয়তো আপনার workflow এ একটি pattern লক্ষ্য করেছেন:

1. প্রথমে, আপনি আপনার Effect এর **কোড পরিবর্তন করেন** বা আপনার reactive value কিভাবে ডিক্লেয়ার করা হয়েছে তা পরিবর্তন করেন।
2. তারপর, আপনি linter অনুসরণ করেন এবং **আপনার পরিবর্তিত কোডের সাথে মিলানোর জন্য** dependency সামঞ্জস্য করেন।
3. যদি আপনি dependency এর তালিকায় সন্তুষ্ট না হন, আপনি **প্রথম ধাপে ফিরে যান** (এবং আবার কোড পরিবর্তন করেন)।

শেষ অংশটি গুরুত্বপূর্ণ। **যদি আপনি dependency পরিবর্তন করতে চান, প্রথমে আশেপাশের কোড পরিবর্তন করুন।** আপনি dependency তালিকাকে [আপনার Effect এর কোড দ্বারা ব্যবহৃত সমস্ত reactive value এর একটি তালিকা](/learn/lifecycle-of-reactive-effects#react-verifies-that-you-specified-every-reactive-value-as-a-dependency) হিসাবে ভাবতে পারেন। আপনি সেই তালিকায় কি রাখবেন তা *বেছে নেন না*। তালিকাটি আপনার কোড *বর্ণনা* করে। dependency তালিকা পরিবর্তন করতে, কোড পরিবর্তন করুন।

এটি একটি equation সমাধান করার মতো মনে হতে পারে। আপনি একটি লক্ষ্য নিয়ে শুরু করতে পারেন (উদাহরণস্বরূপ, একটি dependency সরাতে), এবং আপনাকে সেই লক্ষ্যের সাথে মিলে এমন কোড "খুঁজে বের করতে" হবে। সবাই equation সমাধান করা মজাদার মনে করে না, এবং Effect লেখার ক্ষেত্রেও একই কথা বলা যেতে পারে! সৌভাগ্যবশত, নিচে সাধারণ recipe এর একটি তালিকা রয়েছে যা আপনি চেষ্টা করতে পারেন।

<Pitfall>

যদি আপনার একটি বিদ্যমান codebase থাকে, আপনার কিছু Effect থাকতে পারে যা এইভাবে linter suppress করে:

```js {3-4}
useEffect(() => {
  // ...
  // 🔴 এইভাবে linter suppress করা এড়িয়ে চলুন:
  // eslint-ignore-next-line react-hooks/exhaustive-deps
}, []);
```

**যখন dependency কোডের সাথে মিলে না, তখন bug introduce করার খুব বেশি ঝুঁকি থাকে।** linter suppress করে, আপনি React কে আপনার Effect যে value এর উপর নির্ভর করে সে সম্পর্কে "মিথ্যা বলেন"।

পরিবর্তে, নিচের কৌশলগুলি ব্যবহার করুন।

</Pitfall>

<DeepDive>

#### কেন dependency linter suppress করা এত বিপজ্জনক? {/*why-is-suppressing-the-dependency-linter-so-dangerous*/}

linter suppress করা খুব unintuitive bug এর দিকে নিয়ে যায় যা খুঁজে পাওয়া এবং ঠিক করা কঠিন। এখানে একটি উদাহরণ:

<Sandpack>

```js {expectedErrors: {'react-compiler': [14]}}
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  function onTick() {
	setCount(count + increment);
  }

  useEffect(() => {
    const id = setInterval(onTick, 1000);
    return () => clearInterval(id);
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Every second, increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
    </>
  );
}
```

```css
button { margin: 10px; }
```

</Sandpack>

ধরা যাক আপনি Effect টি "শুধুমাত্র mount এ" চালাতে চেয়েছিলেন। আপনি পড়েছেন যে [খালি (`[]`) dependency](/learn/lifecycle-of-reactive-effects#what-an-effect-with-empty-dependencies-means) এটি করে, তাই আপনি linter উপেক্ষা করার সিদ্ধান্ত নিয়েছেন, এবং জোর করে `[]` dependency হিসাবে নির্দিষ্ট করেছেন।

এই counter টি দুটি button দিয়ে configurable পরিমাণ দ্বারা প্রতি সেকেন্ডে বৃদ্ধি পাওয়ার কথা ছিল। তবে, যেহেতু আপনি React কে "মিথ্যা বলেছেন" যে এই Effect কিছুর উপর নির্ভর করে না, React চিরকাল initial render থেকে `onTick` function ব্যবহার করতে থাকে। [সেই render এর সময়,](/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time) `count` ছিল `0` এবং `increment` ছিল `1`। এই কারণেই সেই render থেকে `onTick` সর্বদা প্রতি সেকেন্ডে `setCount(0 + 1)` কল করে, এবং আপনি সর্বদা `1` দেখেন। এই ধরনের bug ঠিক করা আরও কঠিন যখন তারা একাধিক কম্পোনেন্ট জুড়ে ছড়িয়ে থাকে।

linter উপেক্ষা করার চেয়ে সর্বদা একটি ভালো সমাধান আছে! এই কোড ঠিক করতে, আপনাকে dependency তালিকায় `onTick` যোগ করতে হবে। (interval শুধুমাত্র একবার setup হয় তা নিশ্চিত করতে, [`onTick` কে একটি Effect Event করুন।](/learn/separating-events-from-effects#reading-latest-props-and-state-with-effect-events))

**আমরা dependency lint error কে একটি compilation error হিসাবে বিবেচনা করার সুপারিশ করি। যদি আপনি এটি suppress না করেন, আপনি কখনও এই ধরনের bug দেখবেন না।** এই পৃষ্ঠার বাকি অংশ এই এবং অন্যান্য ক্ষেত্রে বিকল্পগুলি নথিভুক্ত করে।

</DeepDive>

## অপ্রয়োজনীয় dependency সরানো {/*removing-unnecessary-dependencies*/}

প্রতিবার যখন আপনি কোড প্রতিফলিত করতে Effect এর dependency সামঞ্জস্য করেন, dependency তালিকার দিকে তাকান। এই dependency গুলির যেকোনো একটি পরিবর্তিত হলে Effect পুনরায় চালু হওয়া কি বোধগম্য? কখনও কখনও, উত্তর হল "না":

* আপনি বিভিন্ন শর্তে আপনার Effect এর *বিভিন্ন অংশ* পুনরায় execute করতে চাইতে পারেন।
* আপনি কিছু dependency এর পরিবর্তনের প্রতি "react" করার পরিবর্তে শুধুমাত্র তার *সর্বশেষ মান* পড়তে চাইতে পারেন।
* একটি dependency *অনিচ্ছাকৃতভাবে* খুব ঘন ঘন পরিবর্তিত হতে পারে কারণ এটি একটি object বা একটি function।

সঠিক সমাধান খুঁজে পেতে, আপনাকে আপনার Effect সম্পর্কে কয়েকটি প্রশ্নের উত্তর দিতে হবে। আসুন তাদের মধ্য দিয়ে যাই।

### এই কোড কি একটি event handler এ সরানো উচিত? {/*should-this-code-move-to-an-event-handler*/}

আপনার প্রথম যে বিষয়টি চিন্তা করা উচিত তা হল এই কোডটি আদৌ একটি Effect হওয়া উচিত কিনা।

একটি form কল্পনা করুন। submit এ, আপনি `submitted` state variable `true` তে set করেন। আপনাকে একটি POST request পাঠাতে হবে এবং একটি notification দেখাতে হবে। আপনি এই logic একটি Effect এর ভিতরে রেখেছেন যা `submitted` `true` হওয়ার প্রতি "react" করে:

```js {6-8}
function Form() {
  const [submitted, setSubmitted] = useState(false);

  useEffect(() => {
    if (submitted) {
      // 🔴 এড়িয়ে চলুন: Event-specific logic একটি Effect এর ভিতরে
      post('/api/register');
      showNotification('Successfully registered!');
    }
  }, [submitted]);

  function handleSubmit() {
    setSubmitted(true);
  }

  // ...
}
```

পরে, আপনি বর্তমান theme অনুযায়ী notification message style করতে চান, তাই আপনি বর্তমান theme পড়েন। যেহেতু `theme` component body তে ডিক্লেয়ার করা হয়েছে, এটি একটি reactive value, তাই আপনি এটি একটি dependency হিসাবে যোগ করেন:

```js {3,9,11}
function Form() {
  const [submitted, setSubmitted] = useState(false);
  const theme = useContext(ThemeContext);

  useEffect(() => {
    if (submitted) {
      // 🔴 এড়িয়ে চলুন: Event-specific logic একটি Effect এর ভিতরে
      post('/api/register');
      showNotification('Successfully registered!', theme);
    }
  }, [submitted, theme]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে

  function handleSubmit() {
    setSubmitted(true);
  }

  // ...
}
```

এটি করে, আপনি একটি bug introduce করেছেন। কল্পনা করুন আপনি প্রথমে form submit করেছেন এবং তারপর Dark এবং Light theme এর মধ্যে switch করেছেন। `theme` পরিবর্তিত হবে, Effect পুনরায় চালু হবে, এবং এটি আবার একই notification প্রদর্শন করবে!

**এখানে সমস্যা হল এটি প্রথম স্থানে একটি Effect হওয়া উচিত নয়।** আপনি এই POST request পাঠাতে এবং notification দেখাতে চান *form submit করার* প্রতিক্রিয়ায়, যা একটি নির্দিষ্ট interaction। নির্দিষ্ট interaction এর প্রতিক্রিয়ায় কিছু কোড চালাতে, সেই logic সরাসরি সংশ্লিষ্ট event handler এ রাখুন:

```js {6-7}
function Form() {
  const theme = useContext(ThemeContext);

  function handleSubmit() {
    // ✅ ভালো: Event-specific logic event handler থেকে কল করা হয়
    post('/api/register');
    showNotification('Successfully registered!', theme);
  }

  // ...
}
```

এখন যেহেতু কোডটি একটি event handler এ আছে, এটি reactive নয়—তাই এটি শুধুমাত্র তখনই চালু হবে যখন ব্যবহারকারী form submit করবে। [event handler এবং Effect এর মধ্যে বেছে নেওয়া](/learn/separating-events-from-effects#reactive-values-and-reactive-logic) এবং [কিভাবে অপ্রয়োজনীয় Effect মুছে ফেলতে হয়](/learn/you-might-not-need-an-effect) সম্পর্কে আরও পড়ুন।

### আপনার Effect কি বিভিন্ন সম্পর্কহীন কাজ করছে? {/*is-your-effect-doing-several-unrelated-things*/}

পরবর্তী প্রশ্ন যা আপনার নিজেকে জিজ্ঞাসা করা উচিত তা হল আপনার Effect বিভিন্ন সম্পর্কহীন কাজ করছে কিনা।

কল্পনা করুন আপনি একটি shipping form তৈরি করছেন যেখানে ব্যবহারকারীকে তাদের city এবং area বেছে নিতে হবে। আপনি নির্বাচিত `country` অনুযায়ী server থেকে `cities` এর তালিকা fetch করেন একটি dropdown এ দেখানোর জন্য:

```js
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  const [city, setCity] = useState(null);

  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে

  // ...
```

এটি [একটি Effect এ data fetching](/learn/you-might-not-need-an-effect#fetching-data) এর একটি ভালো উদাহরণ। আপনি `country` prop অনুযায়ী network এর সাথে `cities` state সিঙ্ক্রোনাইজ করছেন। আপনি এটি একটি event handler এ করতে পারবেন না কারণ আপনাকে fetch করতে হবে যখনই `ShippingForm` প্রদর্শিত হয় এবং যখনই `country` পরিবর্তিত হয় (কোন interaction এটি ঘটায় তা বিবেচনা না করে)।

এখন ধরা যাক আপনি city area এর জন্য একটি দ্বিতীয় select box যোগ করছেন, যা বর্তমানে নির্বাচিত `city` এর জন্য `areas` fetch করবে। আপনি একই Effect এর ভিতরে area এর তালিকার জন্য একটি দ্বিতীয় `fetch` call যোগ করে শুরু করতে পারেন:

```js {15-24,28}
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);

  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    // 🔴 এড়িয়ে চলুন: একটি একক Effect দুটি স্বতন্ত্র প্রক্রিয়া সিঙ্ক্রোনাইজ করে
    if (city) {
      fetch(`/api/areas?city=${city}`)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setAreas(json);
          }
        });
    }
    return () => {
      ignore = true;
    };
  }, [country, city]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে

  // ...
```

তবে, যেহেতু Effect এখন `city` state variable ব্যবহার করে, আপনাকে dependency এর তালিকায় `city` যোগ করতে হয়েছে। এটি, পরিবর্তে, একটি সমস্যা introduce করেছে: যখন ব্যবহারকারী একটি ভিন্ন city নির্বাচন করে, Effect পুনরায় চালু হবে এবং `fetchCities(country)` কল করবে। ফলস্বরূপ, আপনি অপ্রয়োজনীয়ভাবে city এর তালিকা অনেকবার refetch করবেন।

**এই কোডের সমস্যা হল আপনি দুটি ভিন্ন সম্পর্কহীন জিনিস সিঙ্ক্রোনাইজ করছেন:**

1. আপনি `country` prop এর উপর ভিত্তি করে network এর সাথে `cities` state সিঙ্ক্রোনাইজ করতে চান।
2. আপনি `city` state এর উপর ভিত্তি করে network এর সাথে `areas` state সিঙ্ক্রোনাইজ করতে চান।


logic টি দুটি Effect এ বিভক্ত করুন, যার প্রতিটি সেই prop এর প্রতি react করে যার সাথে এটি সিঙ্ক্রোনাইজ করতে হবে:

```js {19-33}
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে

  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);
  useEffect(() => {
    if (city) {
      let ignore = false;
      fetch(`/api/areas?city=${city}`)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setAreas(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [city]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে

  // ...
```

এখন প্রথম Effect শুধুমাত্র তখনই পুনরায় চালু হয় যখন `country` পরিবর্তিত হয়, যখন দ্বিতীয় Effect পুনরায় চালু হয় যখন `city` পরিবর্তিত হয়। আপনি তাদের উদ্দেশ্য অনুযায়ী আলাদা করেছেন: দুটি ভিন্ন জিনিস দুটি পৃথক Effect দ্বারা সিঙ্ক্রোনাইজ করা হয়। দুটি পৃথক Effect এর দুটি পৃথক dependency তালিকা আছে, তাই তারা অনিচ্ছাকৃতভাবে একে অপরকে trigger করবে না।

চূড়ান্ত কোডটি মূল কোডের চেয়ে দীর্ঘ, কিন্তু এই Effect গুলি বিভক্ত করা এখনও সঠিক। [প্রতিটি Effect একটি স্বতন্ত্র synchronization প্রক্রিয়া প্রতিনিধিত্ব করা উচিত।](/learn/lifecycle-of-reactive-effects#each-effect-represents-a-separate-synchronization-process) এই উদাহরণে, একটি Effect মুছে ফেললে অন্য Effect এর logic ভাঙে না। এর মানে তারা *বিভিন্ন জিনিস সিঙ্ক্রোনাইজ করে,* এবং তাদের আলাদা করা ভালো। যদি আপনি duplication নিয়ে চিন্তিত হন, আপনি [একটি custom Hook এ repetitive logic extract করে](/learn/reusing-logic-with-custom-hooks#when-to-use-custom-hooks) এই কোড উন্নত করতে পারেন।

### আপনি কি পরবর্তী state গণনা করতে কিছু state পড়ছেন? {/*are-you-reading-some-state-to-calculate-the-next-state*/}

এই Effect প্রতিবার একটি নতুন message আসলে একটি নতুন তৈরি array দিয়ে `messages` state variable আপডেট করে:

```js {2,6-8}
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      setMessages([...messages, receivedMessage]);
    });
    // ...
```

এটি `messages` variable ব্যবহার করে সমস্ত বিদ্যমান message দিয়ে শুরু করে [একটি নতুন array তৈরি করে](/learn/updating-arrays-in-state) এবং শেষে নতুন message যোগ করে। তবে, যেহেতু `messages` একটি reactive value যা একটি Effect দ্বারা পড়া হয়, এটি অবশ্যই একটি dependency হতে হবে:

```js {7,10}
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      setMessages([...messages, receivedMessage]);
    });
    return () => connection.disconnect();
  }, [roomId, messages]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

এবং `messages` কে একটি dependency করা একটি সমস্যা introduce করে।

প্রতিবার আপনি একটি message পান, `setMessages()` কম্পোনেন্টকে একটি নতুন `messages` array সহ re-render করে যাতে প্রাপ্ত message অন্তর্ভুক্ত থাকে। তবে, যেহেতু এই Effect এখন `messages` এর উপর নির্ভর করে, এটি Effect কেও *পুনরায় সিঙ্ক্রোনাইজ* করবে। তাই প্রতিটি নতুন message chat কে পুনরায় সংযুক্ত করবে। ব্যবহারকারী এটি পছন্দ করবে না!

সমস্যা সমাধান করতে, Effect এর ভিতরে `messages` পড়বেন না। পরিবর্তে, `setMessages` এ একটি [updater function](/reference/react/useState#updating-state-based-on-the-previous-state) পাস করুন:

```js {7,10}
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      setMessages(msgs => [...msgs, receivedMessage]);
    });
    return () => connection.disconnect();
  }, [roomId]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

**লক্ষ্য করুন কিভাবে আপনার Effect এখন আর `messages` variable পড়ে না।** আপনাকে শুধুমাত্র একটি updater function পাস করতে হবে যেমন `msgs => [...msgs, receivedMessage]`। React [আপনার updater function কে একটি queue এ রাখে](/learn/queueing-a-series-of-state-updates) এবং পরবর্তী render এর সময় এটিকে `msgs` argument প্রদান করবে। এই কারণেই Effect নিজেই আর `messages` এর উপর নির্ভর করতে হবে না। এই fix এর ফলে, একটি chat message পাওয়া আর chat কে পুনরায় সংযুক্ত করবে না।


### আপনি কি একটি মান পড়তে চান এর পরিবর্তনের প্রতি "react" না করে? {/*do-you-want-to-read-a-value-without-reacting-to-its-changes*/}

ধরুন আপনি একটি sound play করতে চান যখন ব্যবহারকারী একটি নতুন message পায় যদি না `isMuted` `true` হয়:

```js {3,10-12}
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  const [isMuted, setIsMuted] = useState(false);

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      setMessages(msgs => [...msgs, receivedMessage]);
      if (!isMuted) {
        playSound();
      }
    });
    // ...
```

যেহেতু আপনার Effect এখন তার কোডে `isMuted` ব্যবহার করে, আপনাকে এটি dependency তে যোগ করতে হবে:

```js {10,15}
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  const [isMuted, setIsMuted] = useState(false);

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      setMessages(msgs => [...msgs, receivedMessage]);
      if (!isMuted) {
        playSound();
      }
    });
    return () => connection.disconnect();
  }, [roomId, isMuted]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

সমস্যা হল প্রতিবার `isMuted` পরিবর্তিত হলে (উদাহরণস্বরূপ, যখন ব্যবহারকারী "Muted" toggle চাপে), Effect পুনরায় সিঙ্ক্রোনাইজ হবে, এবং chat এ পুনরায় সংযুক্ত হবে। এটি কাঙ্ক্ষিত user experience নয়! (এই উদাহরণে, এমনকি linter নিষ্ক্রিয় করাও কাজ করবে না--যদি আপনি তা করেন, `isMuted` তার পুরানো মান নিয়ে "আটকে" যাবে।)

এই সমস্যা সমাধান করতে, আপনাকে Effect থেকে সেই logic extract করতে হবে যা reactive হওয়া উচিত নয়। আপনি চান না এই Effect `isMuted` এর পরিবর্তনের প্রতি "react" করুক। [এই non-reactive logic এর অংশটি একটি Effect Event এ সরান:](/learn/separating-events-from-effects#declaring-an-effect-event)

```js {1,7-12,18,21}
import { useState, useEffect, useEffectEvent } from 'react';

function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  const [isMuted, setIsMuted] = useState(false);

  const onMessage = useEffectEvent(receivedMessage => {
    setMessages(msgs => [...msgs, receivedMessage]);
    if (!isMuted) {
      playSound();
    }
  });

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      onMessage(receivedMessage);
    });
    return () => connection.disconnect();
  }, [roomId]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

Effect Event আপনাকে একটি Effect কে reactive অংশে (যা reactive value যেমন `roomId` এবং তাদের পরিবর্তনের প্রতি "react" করা উচিত) এবং non-reactive অংশে (যা শুধুমাত্র তাদের সর্বশেষ মান পড়ে, যেমন `onMessage` `isMuted` পড়ে) বিভক্ত করতে দেয়। **এখন যেহেতু আপনি একটি Effect Event এর ভিতরে `isMuted` পড়েন, এটি আপনার Effect এর একটি dependency হওয়ার প্রয়োজন নেই।** ফলস্বরূপ, যখন আপনি "Muted" setting on এবং off toggle করেন তখন chat পুনরায় সংযুক্ত হবে না, মূল সমস্যা সমাধান করে!

#### Props থেকে একটি event handler wrap করা {/*wrapping-an-event-handler-from-the-props*/}

আপনি একটি অনুরূপ সমস্যায় পড়তে পারেন যখন আপনার component একটি event handler prop হিসাবে পায়:

```js {1,8,11}
function ChatRoom({ roomId, onReceiveMessage }) {
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      onReceiveMessage(receivedMessage);
    });
    return () => connection.disconnect();
  }, [roomId, onReceiveMessage]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

ধরুন parent component প্রতিটি render এ একটি *ভিন্ন* `onReceiveMessage` function পাস করে:

```js {3-5}
<ChatRoom
  roomId={roomId}
  onReceiveMessage={receivedMessage => {
    // ...
  }}
/>
```

যেহেতু `onReceiveMessage` একটি dependency, এটি প্রতিটি parent re-render এর পরে Effect কে পুনরায় সিঙ্ক্রোনাইজ করবে। এটি chat এ পুনরায় সংযুক্ত করবে। এটি সমাধান করতে, call টি একটি Effect Event এ wrap করুন:

```js {4-6,12,15}
function ChatRoom({ roomId, onReceiveMessage }) {
  const [messages, setMessages] = useState([]);

  const onMessage = useEffectEvent(receivedMessage => {
    onReceiveMessage(receivedMessage);
  });

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      onMessage(receivedMessage);
    });
    return () => connection.disconnect();
  }, [roomId]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

Effect Event reactive নয়, তাই আপনাকে তাদের dependency হিসাবে নির্দিষ্ট করতে হবে না। ফলস্বরূপ, এমনকি parent component প্রতিটি re-render এ একটি ভিন্ন function পাস করলেও chat আর পুনরায় সংযুক্ত হবে না।


#### Reactive এবং non-reactive কোড আলাদা করা {/*separating-reactive-and-non-reactive-code*/}

এই উদাহরণে, আপনি প্রতিবার `roomId` পরিবর্তিত হলে একটি visit log করতে চান। আপনি প্রতিটি log এর সাথে বর্তমান `notificationCount` অন্তর্ভুক্ত করতে চান, কিন্তু আপনি *চান না* `notificationCount` এর পরিবর্তন একটি log event trigger করুক।

সমাধান আবার non-reactive কোড একটি Effect Event এ আলাদা করা:

```js {2-4,7}
function Chat({ roomId, notificationCount }) {
  const onVisit = useEffectEvent(visitedRoomId => {
    logVisit(visitedRoomId, notificationCount);
  });

  useEffect(() => {
    onVisit(roomId);
  }, [roomId]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
}
```

আপনি চান আপনার logic `roomId` এর সাপেক্ষে reactive হোক, তাই আপনি আপনার Effect এর ভিতরে `roomId` পড়েন। তবে, আপনি চান না `notificationCount` এর পরিবর্তন একটি অতিরিক্ত visit log করুক, তাই আপনি Effect Event এর ভিতরে `notificationCount` পড়েন। [Effect Event ব্যবহার করে Effect থেকে সর্বশেষ props এবং state পড়া সম্পর্কে আরও জানুন।](/learn/separating-events-from-effects#reading-latest-props-and-state-with-effect-events)

### কিছু reactive value কি অনিচ্ছাকৃতভাবে পরিবর্তিত হয়? {/*does-some-reactive-value-change-unintentionally*/}

কখনও কখনও, আপনি *চান* আপনার Effect একটি নির্দিষ্ট মানের প্রতি "react" করুক, কিন্তু সেই মান আপনার পছন্দের চেয়ে বেশি ঘন ঘন পরিবর্তিত হয়--এবং ব্যবহারকারীর দৃষ্টিকোণ থেকে কোনো প্রকৃত পরিবর্তন প্রতিফলিত নাও করতে পারে। উদাহরণস্বরূপ, ধরা যাক আপনি আপনার component এর body তে একটি `options` object তৈরি করেন, এবং তারপর আপনার Effect এর ভিতর থেকে সেই object পড়েন:

```js {3-6,9}
function ChatRoom({ roomId }) {
  // ...
  const options = {
    serverUrl: serverUrl,
    roomId: roomId
  };

  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    // ...
```

এই object component body তে ডিক্লেয়ার করা হয়েছে, তাই এটি একটি [reactive value।](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) যখন আপনি একটি Effect এর ভিতরে এইরকম একটি reactive value পড়েন, আপনি এটি একটি dependency হিসাবে ডিক্লেয়ার করেন। এটি নিশ্চিত করে যে আপনার Effect এর পরিবর্তনের প্রতি "react" করে:

```js {3,6}
  // ...
  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [options]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

এটি একটি dependency হিসাবে ডিক্লেয়ার করা গুরুত্বপূর্ণ! এটি নিশ্চিত করে, উদাহরণস্বরূপ, যদি `roomId` পরিবর্তিত হয়, আপনার Effect নতুন `options` দিয়ে chat এ পুনরায় সংযুক্ত হবে। তবে, উপরের কোডেও একটি সমস্যা আছে। এটি দেখতে, নিচের sandbox এ input এ টাইপ করার চেষ্টা করুন, এবং console এ কি ঘটে তা দেখুন:

<Sandpack>

```js {expectedErrors: {'react-compiler': [10]}}
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  // Temporarily disable the linter to demonstrate the problem
  // eslint-disable-next-line react-hooks/exhaustive-deps
  const options = {
    serverUrl: serverUrl,
    roomId: roomId
  };

  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [options]);

  return (
    <>
      <h1>Welcome to the {roomId} room!</h1>
      <input value={message} onChange={e => setMessage(e.target.value)} />
    </>
  );
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
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
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

```js src/chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

উপরের sandbox এ, input শুধুমাত্র `message` state variable আপডেট করে। ব্যবহারকারীর দৃষ্টিকোণ থেকে, এটি chat connection কে প্রভাবিত করা উচিত নয়। তবে, প্রতিবার আপনি `message` আপডেট করেন, আপনার component re-render হয়। যখন আপনার component re-render হয়, এর ভিতরের কোড আবার শুরু থেকে চালু হয়।

প্রতিটি `ChatRoom` component এর re-render এ একটি নতুন `options` object শুরু থেকে তৈরি হয়। React দেখে যে `options` object শেষ render এর সময় তৈরি `options` object থেকে একটি *ভিন্ন object*। এই কারণেই এটি আপনার Effect পুনরায় সিঙ্ক্রোনাইজ করে (যা `options` এর উপর নির্ভর করে), এবং আপনি টাইপ করার সাথে সাথে chat পুনরায় সংযুক্ত হয়।

**এই সমস্যা শুধুমাত্র object এবং function কে প্রভাবিত করে। JavaScript এ, প্রতিটি নতুন তৈরি object এবং function অন্য সবগুলি থেকে আলাদা বলে বিবেচিত হয়। এটি কোন ব্যাপার না যে তাদের ভিতরের content একই হতে পারে!**

```js {7-8}
// During the first render
const options1 = { serverUrl: 'https://localhost:1234', roomId: 'music' };

// During the next render
const options2 = { serverUrl: 'https://localhost:1234', roomId: 'music' };

// These are two different objects!
console.log(Object.is(options1, options2)); // false
```

<<<<<<< HEAD
**Object এবং function dependency আপনার Effect কে আপনার প্রয়োজনের চেয়ে বেশি ঘন ঘন পুনরায় সিঙ্ক্রোনাইজ করতে পারে।**
=======
**Object and function dependencies can make your Effect re-synchronize more often than you need.**
>>>>>>> abe931a8cb3aee3e8b15ef7e187214789164162a

এই কারণেই, যখনই সম্ভব, আপনার Effect এর dependency হিসাবে object এবং function এড়ানোর চেষ্টা করা উচিত। পরিবর্তে, তাদের component এর বাইরে, Effect এর ভিতরে সরানোর চেষ্টা করুন, অথবা তাদের থেকে primitive value extract করুন।


#### Static object এবং function আপনার component এর বাইরে সরান {/*move-static-objects-and-functions-outside-your-component*/}

যদি object কোনো props এবং state এর উপর নির্ভর না করে, আপনি সেই object আপনার component এর বাইরে সরাতে পারেন:

```js {1-4,13}
const options = {
  serverUrl: 'https://localhost:1234',
  roomId: 'music'
};

function ChatRoom() {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, []); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

এইভাবে, আপনি linter কে *প্রমাণ* করেন যে এটি reactive নয়। এটি একটি re-render এর ফলে পরিবর্তিত হতে পারে না, তাই এটি একটি dependency হওয়ার প্রয়োজন নেই। এখন `ChatRoom` re-render করলে আপনার Effect পুনরায় সিঙ্ক্রোনাইজ হবে না।

এটি function এর জন্যও কাজ করে:

```js {1-6,12}
function createOptions() {
  return {
    serverUrl: 'https://localhost:1234',
    roomId: 'music'
  };
}

function ChatRoom() {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const options = createOptions();
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, []); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

যেহেতু `createOptions` আপনার component এর বাইরে ডিক্লেয়ার করা হয়েছে, এটি একটি reactive value নয়। এই কারণেই এটি আপনার Effect এর dependency তে নির্দিষ্ট করার প্রয়োজন নেই, এবং কেন এটি কখনও আপনার Effect কে পুনরায় সিঙ্ক্রোনাইজ করবে না।

#### Dynamic object এবং function আপনার Effect এর ভিতরে সরান {/*move-dynamic-objects-and-functions-inside-your-effect*/}

যদি আপনার object কোনো reactive value এর উপর নির্ভর করে যা একটি re-render এর ফলে পরিবর্তিত হতে পারে, যেমন একটি `roomId` prop, আপনি এটি আপনার component এর *বাইরে* টানতে পারবেন না। তবে, আপনি এর creation আপনার Effect এর কোডের *ভিতরে* সরাতে পারেন:

```js {7-10,11,14}
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

এখন যেহেতু `options` আপনার Effect এর ভিতরে ডিক্লেয়ার করা হয়েছে, এটি আর আপনার Effect এর একটি dependency নয়। পরিবর্তে, আপনার Effect দ্বারা ব্যবহৃত একমাত্র reactive value হল `roomId`। যেহেতু `roomId` একটি object বা function নয়, আপনি নিশ্চিত হতে পারেন যে এটি *অনিচ্ছাকৃতভাবে* ভিন্ন হবে না। JavaScript এ, number এবং string তাদের content দ্বারা তুলনা করা হয়:

```js {7-8}
// During the first render
const roomId1 = 'music';

// During the next render
const roomId2 = 'music';

// These two strings are the same!
console.log(Object.is(roomId1, roomId2)); // true
```

এই fix এর জন্য ধন্যবাদ, যদি আপনি input edit করেন তাহলে chat আর পুনরায় সংযুক্ত হয় না:

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return (
    <>
      <h1>Welcome to the {roomId} room!</h1>
      <input value={message} onChange={e => setMessage(e.target.value)} />
    </>
  );
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
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
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

```js src/chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

তবে, এটি *পুনরায় সংযুক্ত হয়* যখন আপনি `roomId` dropdown পরিবর্তন করেন, যেমনটি আপনি আশা করবেন।

এটি function এর জন্যও কাজ করে:

```js {7-12,14}
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    function createOptions() {
      return {
        serverUrl: serverUrl,
        roomId: roomId
      };
    }

    const options = createOptions();
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

আপনি আপনার Effect এর ভিতরে logic এর অংশগুলি group করতে আপনার নিজস্ব function লিখতে পারেন। যতক্ষণ আপনি তাদের আপনার Effect এর *ভিতরে* ডিক্লেয়ার করেন, তারা reactive value নয়, এবং তাই তাদের আপনার Effect এর dependency হওয়ার প্রয়োজন নেই।


#### Object থেকে primitive value পড়ুন {/*read-primitive-values-from-objects*/}

কখনও কখনও, আপনি props থেকে একটি object পেতে পারেন:

```js {1,5,8}
function ChatRoom({ options }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [options]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

এখানে ঝুঁকি হল parent component rendering এর সময় object তৈরি করবে:

```js {3-6}
<ChatRoom
  roomId={roomId}
  options={{
    serverUrl: serverUrl,
    roomId: roomId
  }}
/>
```

এটি আপনার Effect কে প্রতিবার parent component re-render হলে পুনরায় সংযুক্ত করবে। এটি ঠিক করতে, Effect এর *বাইরে* object থেকে information পড়ুন, এবং object এবং function dependency থাকা এড়িয়ে চলুন:

```js {4,7-8,12}
function ChatRoom({ options }) {
  const [message, setMessage] = useState('');

  const { roomId, serverUrl } = options;
  useEffect(() => {
    const connection = createConnection({
      roomId: roomId,
      serverUrl: serverUrl
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

Logic টি একটু repetitive হয় (আপনি একটি Effect এর বাইরে একটি object থেকে কিছু value পড়েন, এবং তারপর Effect এর ভিতরে একই value দিয়ে একটি object তৈরি করেন)। কিন্তু এটি খুব স্পষ্ট করে যে আপনার Effect *আসলে* কোন information এর উপর নির্ভর করে। যদি parent component দ্বারা অনিচ্ছাকৃতভাবে একটি object পুনরায় তৈরি করা হয়, chat পুনরায় সংযুক্ত হবে না। তবে, যদি `options.roomId` বা `options.serverUrl` সত্যিই ভিন্ন হয়, chat পুনরায় সংযুক্ত হবে।

#### Function থেকে primitive value গণনা করুন {/*calculate-primitive-values-from-functions*/}

একই পদ্ধতি function এর জন্য কাজ করতে পারে। উদাহরণস্বরূপ, ধরুন parent component একটি function পাস করে:

```js {3-8}
<ChatRoom
  roomId={roomId}
  getOptions={() => {
    return {
      serverUrl: serverUrl,
      roomId: roomId
    };
  }}
/>
```

এটি একটি dependency করা এড়াতে (এবং re-render এ পুনরায় সংযুক্ত হওয়া এড়াতে), Effect এর বাইরে এটি call করুন। এটি আপনাকে `roomId` এবং `serverUrl` value দেয় যা object নয়, এবং যা আপনি আপনার Effect এর ভিতর থেকে পড়তে পারেন:

```js {1,4}
function ChatRoom({ getOptions }) {
  const [message, setMessage] = useState('');

  const { roomId, serverUrl } = getOptions();
  useEffect(() => {
    const connection = createConnection({
      roomId: roomId,
      serverUrl: serverUrl
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

এটি শুধুমাত্র [pure](/learn/keeping-components-pure) function এর জন্য কাজ করে কারণ rendering এর সময় তাদের call করা নিরাপদ। যদি আপনার function একটি event handler হয়, কিন্তু আপনি চান না এর পরিবর্তন আপনার Effect পুনরায় সিঙ্ক্রোনাইজ করুক, [পরিবর্তে এটি একটি Effect Event এ wrap করুন।](#do-you-want-to-read-a-value-without-reacting-to-its-changes)

<Recap>

- Dependency সর্বদা কোডের সাথে মিলতে হবে।
- যখন আপনি আপনার dependency নিয়ে সন্তুষ্ট নন, আপনাকে যা edit করতে হবে তা হল কোড।
- Linter suppress করা খুব বিভ্রান্তিকর bug এর দিকে নিয়ে যায়, এবং আপনার সর্বদা এটি এড়ানো উচিত।
- একটি dependency সরাতে, আপনাকে linter কে "প্রমাণ" করতে হবে যে এটি প্রয়োজনীয় নয়।
- যদি কিছু কোড একটি নির্দিষ্ট interaction এর প্রতিক্রিয়ায় চালু হওয়া উচিত, সেই কোড একটি event handler এ সরান।
- যদি আপনার Effect এর বিভিন্ন অংশ বিভিন্ন কারণে পুনরায় চালু হওয়া উচিত, এটি বিভিন্ন Effect এ বিভক্ত করুন।
- যদি আপনি পূর্ববর্তী state এর উপর ভিত্তি করে কিছু state আপডেট করতে চান, একটি updater function পাস করুন।
- যদি আপনি "react" না করে সর্বশেষ মান পড়তে চান, আপনার Effect থেকে একটি Effect Event extract করুন।
- JavaScript এ, object এবং function ভিন্ন বলে বিবেচিত হয় যদি তারা বিভিন্ন সময়ে তৈরি করা হয়।
- Object এবং function dependency এড়ানোর চেষ্টা করুন। তাদের component এর বাইরে বা Effect এর ভিতরে সরান।

</Recap>

<Challenges>

#### একটি resetting interval ঠিক করুন {/*fix-a-resetting-interval*/}

এই Effect একটি interval setup করে যা প্রতি সেকেন্ডে tick করে। আপনি কিছু অদ্ভুত ঘটতে দেখেছেন: মনে হচ্ছে interval প্রতিবার tick করার সময় destroy এবং re-create হয়। কোড ঠিক করুন যাতে interval ক্রমাগত পুনরায় তৈরি না হয়।

<Hint>

মনে হচ্ছে এই Effect এর কোড `count` এর উপর নির্ভর করে। এই dependency এর প্রয়োজন না হওয়ার কোনো উপায় আছে কি? সেই value এ dependency যোগ না করে তার পূর্ববর্তী মানের উপর ভিত্তি করে `count` state আপডেট করার একটি উপায় থাকা উচিত।

</Hint>

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('✅ Creating an interval');
    const id = setInterval(() => {
      console.log('⏰ Interval tick');
      setCount(count + 1);
    }, 1000);
    return () => {
      console.log('❌ Clearing an interval');
      clearInterval(id);
    };
  }, [count]);

  return <h1>Counter: {count}</h1>
}
```

</Sandpack>

<Solution>

আপনি Effect এর ভিতর থেকে `count` state কে `count + 1` এ আপডেট করতে চান। তবে, এটি আপনার Effect কে `count` এর উপর নির্ভরশীল করে, যা প্রতিটি tick এ পরিবর্তিত হয়, এবং এই কারণেই আপনার interval প্রতিটি tick এ পুনরায় তৈরি হয়।

এটি সমাধান করতে, [updater function](/reference/react/useState#updating-state-based-on-the-previous-state) ব্যবহার করুন এবং `setCount(count + 1)` এর পরিবর্তে `setCount(c => c + 1)` লিখুন:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('✅ Creating an interval');
    const id = setInterval(() => {
      console.log('⏰ Interval tick');
      setCount(c => c + 1);
    }, 1000);
    return () => {
      console.log('❌ Clearing an interval');
      clearInterval(id);
    };
  }, []);

  return <h1>Counter: {count}</h1>
}
```

</Sandpack>

Effect এর ভিতরে `count` পড়ার পরিবর্তে, আপনি React কে `c => c + 1` instruction ("এই number বৃদ্ধি করুন!") পাস করেন। React এটি পরবর্তী render এ প্রয়োগ করবে। এবং যেহেতু আপনাকে আর আপনার Effect এর ভিতরে `count` এর মান পড়তে হবে না, আপনি আপনার Effect এর dependency খালি (`[]`) রাখতে পারেন। এই fix interval কে প্রতিটি tick এ পুনরায় তৈরি হওয়া থেকে বাধা দেয়।

</Solution>


#### একটি retriggering animation ঠিক করুন {/*fix-a-retriggering-animation*/}

এই উদাহরণে, যখন আপনি "Show" চাপেন, একটি welcome message fade in হয়। animation এক সেকেন্ড সময় নেয়। যখন আপনি "Remove" চাপেন, welcome message অবিলম্বে অদৃশ্য হয়ে যায়। fade-in animation এর logic `animation.js` file এ plain JavaScript [animation loop](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) হিসাবে implement করা হয়েছে। আপনাকে সেই logic পরিবর্তন করতে হবে না। আপনি এটিকে একটি third-party library হিসাবে বিবেচনা করতে পারেন। আপনার Effect DOM node এর জন্য `FadeInAnimation` এর একটি instance তৈরি করে, এবং তারপর animation নিয়ন্ত্রণ করতে `start(duration)` বা `stop()` call করে। `duration` একটি slider দ্বারা নিয়ন্ত্রিত হয়। slider সামঞ্জস্য করুন এবং দেখুন animation কিভাবে পরিবর্তিত হয়।

এই কোড ইতিমধ্যে কাজ করে, কিন্তু আপনি কিছু পরিবর্তন করতে চান। বর্তমানে, যখন আপনি slider সরান যা `duration` state variable নিয়ন্ত্রণ করে, এটি animation পুনরায় trigger করে। আচরণ পরিবর্তন করুন যাতে Effect `duration` variable এর প্রতি "react" না করে। যখন আপনি "Show" চাপেন, Effect slider এ বর্তমান `duration` ব্যবহার করা উচিত। তবে, slider সরানো নিজে থেকে animation পুনরায় trigger করা উচিত নয়।

<Hint>

Effect এর ভিতরে কি কোডের একটি লাইন আছে যা reactive হওয়া উচিত নয়? আপনি কিভাবে non-reactive কোড Effect থেকে বের করতে পারেন?

</Hint>

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import { useEffectEvent } from 'react';
import { FadeInAnimation } from './animation.js';

function Welcome({ duration }) {
  const ref = useRef(null);

  useEffect(() => {
    const animation = new FadeInAnimation(ref.current);
    animation.start(duration);
    return () => {
      animation.stop();
    };
  }, [duration]);

  return (
    <h1
      ref={ref}
      style={{
        opacity: 0,
        color: 'white',
        padding: 50,
        textAlign: 'center',
        fontSize: 50,
        backgroundImage: 'radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%)'
      }}
    >
      Welcome
    </h1>
  );
}

export default function App() {
  const [duration, setDuration] = useState(1000);
  const [show, setShow] = useState(false);

  return (
    <>
      <label>
        <input
          type="range"
          min="100"
          max="3000"
          value={duration}
          onChange={e => setDuration(Number(e.target.value))}
        />
        <br />
        Fade in duration: {duration} ms
      </label>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome duration={duration} />}
    </>
  );
}
```

```js src/animation.js
export class FadeInAnimation {
  constructor(node) {
    this.node = node;
  }
  start(duration) {
    this.duration = duration;
    if (this.duration === 0) {
      // Jump to end immediately
      this.onProgress(1);
    } else {
      this.onProgress(0);
      // Start animating
      this.startTime = performance.now();
      this.frameId = requestAnimationFrame(() => this.onFrame());
    }
  }
  onFrame() {
    const timePassed = performance.now() - this.startTime;
    const progress = Math.min(timePassed / this.duration, 1);
    this.onProgress(progress);
    if (progress < 1) {
      // We still have more frames to paint
      this.frameId = requestAnimationFrame(() => this.onFrame());
    }
  }
  onProgress(progress) {
    this.node.style.opacity = progress;
  }
  stop() {
    cancelAnimationFrame(this.frameId);
    this.startTime = null;
    this.frameId = null;
    this.duration = 0;
  }
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
```

</Sandpack>

<Solution>

আপনার Effect কে `duration` এর সর্বশেষ মান পড়তে হবে, কিন্তু আপনি চান না এটি `duration` এর পরিবর্তনের প্রতি "react" করুক। আপনি animation শুরু করতে `duration` ব্যবহার করেন, কিন্তু animation শুরু করা reactive নয়। non-reactive কোডের লাইনটি একটি Effect Event এ extract করুন, এবং আপনার Effect থেকে সেই function call করুন।

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import { FadeInAnimation } from './animation.js';
import { useEffectEvent } from 'react';

function Welcome({ duration }) {
  const ref = useRef(null);

  const onAppear = useEffectEvent(animation => {
    animation.start(duration);
  });

  useEffect(() => {
    const animation = new FadeInAnimation(ref.current);
    onAppear(animation);
    return () => {
      animation.stop();
    };
  }, []);

  return (
    <h1
      ref={ref}
      style={{
        opacity: 0,
        color: 'white',
        padding: 50,
        textAlign: 'center',
        fontSize: 50,
        backgroundImage: 'radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%)'
      }}
    >
      Welcome
    </h1>
  );
}

export default function App() {
  const [duration, setDuration] = useState(1000);
  const [show, setShow] = useState(false);

  return (
    <>
      <label>
        <input
          type="range"
          min="100"
          max="3000"
          value={duration}
          onChange={e => setDuration(Number(e.target.value))}
        />
        <br />
        Fade in duration: {duration} ms
      </label>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome duration={duration} />}
    </>
  );
}
```

```js src/animation.js
export class FadeInAnimation {
  constructor(node) {
    this.node = node;
  }
  start(duration) {
    this.duration = duration;
    this.onProgress(0);
    this.startTime = performance.now();
    this.frameId = requestAnimationFrame(() => this.onFrame());
  }
  onFrame() {
    const timePassed = performance.now() - this.startTime;
    const progress = Math.min(timePassed / this.duration, 1);
    this.onProgress(progress);
    if (progress < 1) {
      // We still have more frames to paint
      this.frameId = requestAnimationFrame(() => this.onFrame());
    }
  }
  onProgress(progress) {
    this.node.style.opacity = progress;
  }
  stop() {
    cancelAnimationFrame(this.frameId);
    this.startTime = null;
    this.frameId = null;
    this.duration = 0;
  }
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
```

</Sandpack>

`onAppear` এর মতো Effect Event reactive নয়, তাই আপনি animation পুনরায় trigger না করে এর ভিতরে `duration` পড়তে পারেন।

</Solution>


#### একটি reconnecting chat ঠিক করুন {/*fix-a-reconnecting-chat*/}

এই উদাহরণে, প্রতিবার আপনি "Toggle theme" চাপলে, chat পুনরায় সংযুক্ত হয়। এটি কেন ঘটে? ভুল ঠিক করুন যাতে chat শুধুমাত্র তখনই পুনরায় সংযুক্ত হয় যখন আপনি Server URL edit করেন বা একটি ভিন্ন chat room বেছে নেন।

`chat.js` কে একটি external third-party library হিসাবে বিবেচনা করুন: আপনি এর API চেক করতে এটি consult করতে পারেন, কিন্তু এটি edit করবেন না।

<Hint>

এটি ঠিক করার একাধিক উপায় আছে, কিন্তু শেষ পর্যন্ত আপনি একটি object কে আপনার dependency হিসাবে রাখা এড়াতে চান।

</Hint>

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  const [roomId, setRoomId] = useState('general');
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  const options = {
    serverUrl: serverUrl,
    roomId: roomId
  };

  return (
    <div className={isDark ? 'dark' : 'light'}>
      <button onClick={() => setIsDark(!isDark)}>
        Toggle theme
      </button>
      <label>
        Server URL:{' '}
        <input
          value={serverUrl}
          onChange={e => setServerUrl(e.target.value)}
        />
      </label>
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
      <hr />
      <ChatRoom options={options} />
    </div>
  );
}
```

```js src/ChatRoom.js active
import { useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom({ options }) {
  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [options]);

  return <h1>Welcome to the {options.roomId} room!</h1>;
}
```

```js src/chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
label, button { display: block; margin-bottom: 5px; }
.dark { background: #222; color: #eee; }
```

</Sandpack>

<Solution>

আপনার Effect পুনরায় চালু হচ্ছে কারণ এটি `options` object এর উপর নির্ভর করে। Object অনিচ্ছাকৃতভাবে পুনরায় তৈরি হতে পারে, আপনার যখনই সম্ভব তাদের আপনার Effect এর dependency হিসাবে এড়ানোর চেষ্টা করা উচিত।

সবচেয়ে কম invasive fix হল Effect এর ঠিক বাইরে `roomId` এবং `serverUrl` পড়া, এবং তারপর Effect কে সেই primitive value এর উপর নির্ভরশীল করা (যা অনিচ্ছাকৃতভাবে পরিবর্তিত হতে পারে না)। Effect এর ভিতরে, একটি object তৈরি করুন এবং এটি `createConnection` এ পাস করুন:

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  const [roomId, setRoomId] = useState('general');
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  const options = {
    serverUrl: serverUrl,
    roomId: roomId
  };

  return (
    <div className={isDark ? 'dark' : 'light'}>
      <button onClick={() => setIsDark(!isDark)}>
        Toggle theme
      </button>
      <label>
        Server URL:{' '}
        <input
          value={serverUrl}
          onChange={e => setServerUrl(e.target.value)}
        />
      </label>
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
      <hr />
      <ChatRoom options={options} />
    </div>
  );
}
```

```js src/ChatRoom.js active
import { useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom({ options }) {
  const { roomId, serverUrl } = options;
  useEffect(() => {
    const connection = createConnection({
      roomId: roomId,
      serverUrl: serverUrl
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]);

  return <h1>Welcome to the {options.roomId} room!</h1>;
}
```

```js src/chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
label, button { display: block; margin-bottom: 5px; }
.dark { background: #222; color: #eee; }
```

</Sandpack>

object `options` prop কে আরও নির্দিষ্ট `roomId` এবং `serverUrl` props দিয়ে প্রতিস্থাপন করা আরও ভালো হবে:

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  const [roomId, setRoomId] = useState('general');
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  return (
    <div className={isDark ? 'dark' : 'light'}>
      <button onClick={() => setIsDark(!isDark)}>
        Toggle theme
      </button>
      <label>
        Server URL:{' '}
        <input
          value={serverUrl}
          onChange={e => setServerUrl(e.target.value)}
        />
      </label>
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
      <hr />
      <ChatRoom
        roomId={roomId}
        serverUrl={serverUrl}
      />
    </div>
  );
}
```

```js src/ChatRoom.js active
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom({ roomId, serverUrl }) {
  useEffect(() => {
    const connection = createConnection({
      roomId: roomId,
      serverUrl: serverUrl
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]);

  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js src/chat.js
export function createConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
    }
  };
}
```

```css
label, button { display: block; margin-bottom: 5px; }
.dark { background: #222; color: #eee; }
```

</Sandpack>

যখনই সম্ভব primitive props এ লেগে থাকা পরে আপনার component optimize করা সহজ করে।

</Solution>

#### আবার একটি reconnecting chat ঠিক করুন {/*fix-a-reconnecting-chat-again*/}

এই উদাহরণ encryption সহ বা ছাড়া chat এ সংযুক্ত হয়। checkbox toggle করুন এবং console এ বিভিন্ন message লক্ষ্য করুন যখন encryption on এবং off থাকে। room পরিবর্তন করার চেষ্টা করুন। তারপর, theme toggle করার চেষ্টা করুন। যখন আপনি একটি chat room এ সংযুক্ত থাকেন, আপনি প্রতি কয়েক সেকেন্ডে নতুন message পাবেন। যাচাই করুন যে তাদের রঙ আপনার বেছে নেওয়া theme এর সাথে মিলে।

এই উদাহরণে, প্রতিবার আপনি theme পরিবর্তন করার চেষ্টা করলে chat পুনরায় সংযুক্ত হয়। এটি ঠিক করুন। fix এর পরে, theme পরিবর্তন করলে chat পুনরায় সংযুক্ত হওয়া উচিত নয়, কিন্তু encryption setting toggle করা বা room পরিবর্তন করলে পুনরায় সংযুক্ত হওয়া উচিত।

`chat.js` এ কোনো কোড পরিবর্তন করবেন না। এটি ছাড়া, আপনি যেকোনো কোড পরিবর্তন করতে পারেন যতক্ষণ এটি একই আচরণের ফলাফল দেয়। উদাহরণস্বরূপ, আপনি কোন props পাস করা হচ্ছে তা পরিবর্তন করা সহায়ক মনে করতে পারেন।

<Hint>

আপনি দুটি function পাস করছেন: `onMessage` এবং `createConnection`। উভয়ই প্রতিবার `App` re-render হলে শুরু থেকে তৈরি হয়। তারা প্রতিবার নতুন value হিসাবে বিবেচিত হয়, যে কারণে তারা আপনার Effect পুনরায় trigger করে।

এই function গুলির একটি একটি event handler। আপনি কি এমন কোনো উপায় জানেন যে একটি Effect এ একটি event handler call করা যায় event handler function এর নতুন value এর প্রতি "react" না করে? এটি কাজে আসবে!

অন্য function টি শুধুমাত্র একটি imported API method এ কিছু state পাস করার জন্য বিদ্যমান। এই function কি সত্যিই প্রয়োজনীয়? কোন essential information পাস করা হচ্ছে? আপনাকে `App.js` থেকে `ChatRoom.js` এ কিছু import সরাতে হতে পারে।

</Hint>

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
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
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';
import {
  createEncryptedConnection,
  createUnencryptedConnection,
} from './chat.js';
import { showNotification } from './notifications.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  const [roomId, setRoomId] = useState('general');
  const [isEncrypted, setIsEncrypted] = useState(false);

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <label>
        <input
          type="checkbox"
          checked={isEncrypted}
          onChange={e => setIsEncrypted(e.target.checked)}
        />
        Enable encryption
      </label>
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
      <hr />
      <ChatRoom
        roomId={roomId}
        onMessage={msg => {
          showNotification('New message: ' + msg, isDark ? 'dark' : 'light');
        }}
        createConnection={() => {
          const options = {
            serverUrl: 'https://localhost:1234',
            roomId: roomId
          };
          if (isEncrypted) {
            return createEncryptedConnection(options);
          } else {
            return createUnencryptedConnection(options);
          }
        }}
      />
    </>
  );
}
```

```js src/ChatRoom.js active
import { useState, useEffect } from 'react';
import { useEffectEvent } from 'react';

export default function ChatRoom({ roomId, createConnection, onMessage }) {
  useEffect(() => {
    const connection = createConnection();
    connection.on('message', (msg) => onMessage(msg));
    connection.connect();
    return () => connection.disconnect();
  }, [createConnection, onMessage]);

  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js src/chat.js
export function createEncryptedConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ 🔐 Connecting to "' + roomId + '" room... (encrypted)');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey')
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ 🔐 Disconnected from "' + roomId + '" room (encrypted)');
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}

export function createUnencryptedConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room (unencrypted)...');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey')
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ Disconnected from "' + roomId + '" room (unencrypted)');
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}
```

```js src/notifications.js
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme) {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```css
label, button { display: block; margin-bottom: 5px; }
```

</Sandpack>

<Solution>

এটি সমাধান করার একাধিক সঠিক উপায় আছে, কিন্তু এখানে একটি সম্ভাব্য সমাধান।

মূল উদাহরণে, theme toggle করা বিভিন্ন `onMessage` এবং `createConnection` function তৈরি এবং পাস করার কারণ হয়। যেহেতু Effect এই function গুলির উপর নির্ভর করে, প্রতিবার আপনি theme toggle করলে chat পুনরায় সংযুক্ত হত।

`onMessage` এর সমস্যা ঠিক করতে, আপনাকে এটি একটি Effect Event এ wrap করতে হয়েছিল:

```js {1,2,6}
export default function ChatRoom({ roomId, createConnection, onMessage }) {
  const onReceiveMessage = useEffectEvent(onMessage);

  useEffect(() => {
    const connection = createConnection();
    connection.on('message', (msg) => onReceiveMessage(msg));
    // ...
```

`onMessage` prop এর বিপরীতে, `onReceiveMessage` Effect Event reactive নয়। এই কারণেই এটি আপনার Effect এর একটি dependency হওয়ার প্রয়োজন নেই। ফলস্বরূপ, `onMessage` এর পরিবর্তন chat কে পুনরায় সংযুক্ত করবে না।

আপনি `createConnection` এর সাথে একই কাজ করতে পারবেন না কারণ এটি *reactive হওয়া উচিত*। আপনি *চান* Effect পুনরায় trigger হোক যদি ব্যবহারকারী একটি encrypted এবং একটি unencrypted connection এর মধ্যে switch করে, অথবা যদি ব্যবহারকারী বর্তমান room switch করে। তবে, যেহেতু `createConnection` একটি function, আপনি চেক করতে পারবেন না যে এটি যে information পড়ে তা *আসলে* পরিবর্তিত হয়েছে কিনা। এটি সমাধান করতে, `App` component থেকে `createConnection` পাস করার পরিবর্তে, raw `roomId` এবং `isEncrypted` value পাস করুন:

```js {2-3}
      <ChatRoom
        roomId={roomId}
        isEncrypted={isEncrypted}
        onMessage={msg => {
          showNotification('New message: ' + msg, isDark ? 'dark' : 'light');
        }}
      />
```

এখন আপনি `App` থেকে পাস করার পরিবর্তে Effect এর *ভিতরে* `createConnection` function সরাতে পারেন:

```js {1-4,6,10-20}
import {
  createEncryptedConnection,
  createUnencryptedConnection,
} from './chat.js';

export default function ChatRoom({ roomId, isEncrypted, onMessage }) {
  const onReceiveMessage = useEffectEvent(onMessage);

  useEffect(() => {
    function createConnection() {
      const options = {
        serverUrl: 'https://localhost:1234',
        roomId: roomId
      };
      if (isEncrypted) {
        return createEncryptedConnection(options);
      } else {
        return createUnencryptedConnection(options);
      }
    }
    // ...
```

এই দুটি পরিবর্তনের পরে, আপনার Effect আর কোনো function value এর উপর নির্ভর করে না:

```js {1,8,10,21}
export default function ChatRoom({ roomId, isEncrypted, onMessage }) { // Reactive values
  const onReceiveMessage = useEffectEvent(onMessage); // Not reactive

  useEffect(() => {
    function createConnection() {
      const options = {
        serverUrl: 'https://localhost:1234',
        roomId: roomId // Reading a reactive value
      };
      if (isEncrypted) { // Reading a reactive value
        return createEncryptedConnection(options);
      } else {
        return createUnencryptedConnection(options);
      }
    }

    const connection = createConnection();
    connection.on('message', (msg) => onReceiveMessage(msg));
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, isEncrypted]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
```

ফলস্বরূপ, chat শুধুমাত্র তখনই পুনরায় সংযুক্ত হয় যখন কিছু অর্থপূর্ণ (`roomId` বা `isEncrypted`) পরিবর্তিত হয়:


<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
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
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

import { showNotification } from './notifications.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  const [roomId, setRoomId] = useState('general');
  const [isEncrypted, setIsEncrypted] = useState(false);

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <label>
        <input
          type="checkbox"
          checked={isEncrypted}
          onChange={e => setIsEncrypted(e.target.checked)}
        />
        Enable encryption
      </label>
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
      <hr />
      <ChatRoom
        roomId={roomId}
        isEncrypted={isEncrypted}
        onMessage={msg => {
          showNotification('New message: ' + msg, isDark ? 'dark' : 'light');
        }}
      />
    </>
  );
}
```

```js src/ChatRoom.js active
import { useState, useEffect } from 'react';
import { useEffectEvent } from 'react';
import {
  createEncryptedConnection,
  createUnencryptedConnection,
} from './chat.js';

export default function ChatRoom({ roomId, isEncrypted, onMessage }) {
  const onReceiveMessage = useEffectEvent(onMessage);

  useEffect(() => {
    function createConnection() {
      const options = {
        serverUrl: 'https://localhost:1234',
        roomId: roomId
      };
      if (isEncrypted) {
        return createEncryptedConnection(options);
      } else {
        return createUnencryptedConnection(options);
      }
    }

    const connection = createConnection();
    connection.on('message', (msg) => onReceiveMessage(msg));
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, isEncrypted]);

  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js src/chat.js
export function createEncryptedConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ 🔐 Connecting to "' + roomId + '" room... (encrypted)');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey')
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ 🔐 Disconnected from "' + roomId + '" room (encrypted)');
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}

export function createUnencryptedConnection({ serverUrl, roomId }) {
  // A real implementation would actually connect to the server
  if (typeof serverUrl !== 'string') {
    throw Error('Expected serverUrl to be a string. Received: ' + serverUrl);
  }
  if (typeof roomId !== 'string') {
    throw Error('Expected roomId to be a string. Received: ' + roomId);
  }
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room (unencrypted)...');
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        if (messageCallback) {
          if (Math.random() > 0.5) {
            messageCallback('hey')
          } else {
            messageCallback('lol');
          }
        }
      }, 3000);
    },
    disconnect() {
      clearInterval(intervalId);
      messageCallback = null;
      console.log('❌ Disconnected from "' + roomId + '" room (unencrypted)');
    },
    on(event, callback) {
      if (messageCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'message') {
        throw Error('Only "message" event is supported.');
      }
      messageCallback = callback;
    },
  };
}
```

```js src/notifications.js
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme) {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```css
label, button { display: block; margin-bottom: 5px; }
```

</Sandpack>

</Solution>

</Challenges>
