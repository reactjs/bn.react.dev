---
title: Escape Hatches
---

<Intro>

আপনার কিছু কম্পোনেন্টের সম্ভবত React এর বাইরের সিস্টেমগুলির সাথে নিয়ন্ত্রণ এবং সিঙ্ক্রোনাইজ করার প্রয়োজন হতে পারে। উদাহরণস্বরূপ, আপনার ব্রাউজার API ব্যবহার করে একটি ইনপুটে ফোকাস করা লাগতে পারে, React ব্যবহার না করে বানানো একটি ভিডিও প্লেয়ার চালু এবং বন্ধ করতে হতে পারে, অথবা একটি রিমোট সার্ভারের সাথে সংযুক্ত হয়ে message listen করতে হতে পারে। এই অধ্যায়ে, আপনি শিখবেন যে escape hatch আপনাকে React থেকে "বাইরে পদক্ষেপ" নিতে এবং বাইরের সিস্টেমগুলির সাথে সংযোগ করতে দেয়। আপনার অ্যাপ্লিকেশনের অধিকাংশ যুক্তি এবং ডাটা ফ্লো এই ফিচারগুলির উপর নির্ভর করা উচিত নয়।

</Intro>

<YouWillLearn isChapter={true}>

* [কীভাবে re-render না করে তথ্য "মনে রাখবেন"](/learn/referencing-values-with-refs)
* [কীভাবে React এর পরিচালিত DOM এলিমেন্টে ঢুকবেন](/learn/manipulating-the-dom-with-refs)
* [কীভাবে বাইরের সিস্টেমের সাথে সিঙ্ক্রোনাইজ করবেন](/learn/synchronizing-with-effects)
* [কীভাবে কম্পোনেন্ট থেকে অপ্রয়োজনীয় Effect সরিয়ে ফেলবেন](/learn/you-might-not-need-an-effect)
* [কীভাবে একটি Effect এর লাইফ সাইকেল একটি কম্পোনেন্টের লাইফ সাইকেল থেকে ভিন্ন](/learn/lifecycle-of-reactive-effects)
* [কীভাবে কিছু ভ্যালুকে Effects re-triggering করা থেকে বিরত রাখবেন](/learn/separating-events-from-effects)
* [কীভাবে আপনার Effect গুলোর re-run এর সংখ্যা কমাবেন](/learn/removing-effect-dependencies)
* [কীভাবে একাধিক কম্পোনেন্টের মধ্যে লজিক ভাগাভাগি করে নেবেন](/learn/reusing-logic-with-custom-hooks)

</YouWillLearn>

## ref এর সাহায্যে value referencing {/*referencing-values-with-refs*/}

আপনি যখন একটি কম্পোনেন্টের কিছু তথ্য 'remember' করার চাহিদা অনুভব করেন, কিন্তু আপনি চান না যে ওই তথ্য [নতুন রেন্ডার চালু করুক](/learn/render-and-commit), তখন আপনি *ref* ব্যবহার করতে পারেন:

```js
const ref = useRef(0);
```

state এর মতো, re-render এর ফাঁকে ফাঁকে React ref গুলোকে সংরক্ষণ করে। তবে, state সেট করলে একটি কম্পোনেন্ট re-render হয়। একটি ref পরিবর্তন করলে তা হয় না! আপনি `ref.current` প্রপার্টির মাধ্যমে ওই ref এর বর্তমান মান অ্যাক্সেস করতে পারেন।

<Sandpack>

```js
import { useRef } from 'react';

export default function Counter() {
  let ref = useRef(0);

  function handleClick() {
    ref.current = ref.current + 1;
    alert('You clicked ' + ref.current + ' times!');
  }

  return (
    <button onClick={handleClick}>
      Click me!
    </button>
  );
}
```

</Sandpack>

ref হল আপনার কম্পোনেন্টের এমন একটি গোপন পকেট যেটি React ট্র্যাক করে না। উদাহরণস্বরূপ, আপনি [timeout IDs](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout#return_value), [DOM এলিমেন্ট](https://developer.mozilla.org/en-US/docs/Web/API/Element), এবং অন্যান্য অবজেক্ট সংরক্ষণ করার জন্য ref ব্যবহার করতে পারেন, যা কম্পোনেন্টের রেন্ডারিং আউটপুটে প্রভাব ফেলে না।

<LearnMore path="/learn/referencing-values-with-refs">

তথ্য মনে রাখার খাতিরে ref ব্যবহার করা শিখতে পড়ুন **[Referencing Values with Refs](/learn/referencing-values-with-refs)**।

</LearnMore>

## ref ব্যবহার করে DOM এর পরিবর্তন {/*manipulating-the-dom-with-refs*/}

React স্বয়ংক্রিয়ভাবে আপনার রেন্ডার আউটপুটের সাথে মিলিয়ে DOM আপডেট করে, তাই আপনার কম্পোনেন্টগুলোর প্রায়শই এটি পরিবর্তন করার দরকার হয় না। তবে, মাঝে মাঝে আপনার DOM এলিমেন্টগুলোতে অ্যাক্সেস প্রয়োজন হতে পারে যা React দ্বারা পরিচালিত—উদাহরণস্বরূপ, একটি নোডে ফোকাস করা, এটিতে স্ক্রল করা, অথবা এর আকার এবং অবস্থান মাপা। React এ এরকম কিছু করার জন্য কোনো বিল্ট-ইন উপায় নেই, তাই আপনার DOM নোডের জন্য একটি ref প্রয়োজন হবে। উদাহরণস্বরূপ, বাটনটি ক্লিক করলে একটি ref ব্যবহার করে ইনপুটে ফোকাস হবে:

<Sandpack>

```js
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

<LearnMore path="/learn/manipulating-the-dom-with-refs">

React পরিচালিত DOM এলিমেন্টে অ্যাক্সেস নেওয়া শিখতে পড়ুন **[Manipulating the DOM with Refs](/learn/manipulating-the-dom-with-refs)**।

</LearnMore>

## Effect এর সাথে সিঙ্ক্রোনাইজেসন {/*synchronizing-with-effects*/}

কিছু কম্পোনেন্টের বাইরের সিস্টেমগুলোর সাথে সিঙ্ক্রোনাইজ করার প্রয়োজন হতে পারে। উদাহরণস্বরূপ, হতে পারে আপনি React state এর উপর ভিত্তি করে একটি non-React কম্পোনেন্ট নিয়ন্ত্রণ করতে চান, অথবা চান একটি সার্ভার সংযোগ সেট আপ করতে, অথবা একটি কম্পোনেন্ট স্ক্রিনে প্রদর্শিত হলে একটি এনালিটিক্স লগ পাঠাতে চাইতে পারেন। যেখানে event handler আপনাকে নির্দিষ্ট event পরিচালনা করতে দেয়, Effects রেন্ডারিং এর পরে কিছু কোড চালাতে দেয়। আপনার কম্পোনেন্টকে React এর বাইরের একটি সিস্টেমের সাথে সিঙ্ক্রোনাইজ করার জন্য Effects ব্যবহার করুন।

কয়েকবার Play/Pause এ চাপ দিন এবং খেয়াল করুন কীভাবে ভিডিও প্লেয়ার `isPlaying` prop এর সাথে সিঙ্ক্রোনাইজড  থাকছে।

<Sandpack>

```js
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }, [isPlaying]);

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  return (
    <>
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 20px; }
video { width: 250px; }
```

</Sandpack>


অনেক Effects নিজেদের "clean up" নিজেরাই করে নেয়। উদাহরণস্বরূপ, একটি চ্যাট সার্ভারের সাথে সংযোগ স্থাপন করা Effect এর উচিত একটি *cleanup function* ফেরত দেওয়া যা React কে বলে দিবে আপনার কম্পোনেন্টকে সেই সার্ভার থেকে সংযোগ বিচ্ছিন্ন করতে।

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    return () => connection.disconnect();
  }, []);
  return <h1>Welcome to the chat!</h1>;
}
```

```js chat.js
export function createConnection() {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting...');
    },
    disconnect() {
      console.log('❌ Disconnected.');
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
```

</Sandpack>

ডেভেলপমেন্টে, React আপনার Effect টি তাৎক্ষণিকভাবে চালাবে এবং একবার অতিরিক্ত clean up করবে। এই কারণেই আপনি "✅ Connecting..."  দুবার দেখবেন। এটি নিশ্চিত করে যে আপনি cleanup function বাস্তবায়ন করতে ভুলছেন না।

<LearnMore path="/learn/synchronizing-with-effects">

বাইরের সিস্টেমগুলির সাথে কম্পোনেন্টগুলি সিঙ্ক্রোনাইজ করা শিখতে **[Effects এর সাথে সিঙ্ক্রোনাইজেশন](/learn/synchronizing-with-effects)** পড়ুন।

</LearnMore>

## আপনার Effect এর প্রয়োজন নাও পড়তে পারে {/*you-might-not-need-an-effect*/}

Effect হল React এর জগত থেকে একটি escape hatch। এটি আপনাকে "React এর বাইরে পদক্ষেপ" নিতে দেয় এবং সাথে আপনার কম্পোনেন্টগুলিকে কিছু বাইরের সিস্টেমের সাথে সিঙ্ক্রোনাইজ করতে দেয়। যদি কোনো বাইরের সিস্টেম জড়িত না থাকে (উদাহরণস্বরূপ, আপনি যদি কিছু props অথবা state পরিবর্তন হলে একটি কম্পোনেন্টের state আপডেট করতে চান), আপনার Effect এর প্রয়োজন হবার কথা না। অপ্রয়োজনীয় Effect সরিয়ে ফেললে আপনার কোড সহজে পড়া যাবে, দ্রুত চলবে, এবং ভুল কম হবে। 

সাধারণত দুটি ক্ষেত্রে আপনার Effect দরকার হবে নাঃ
- **রেন্ডারের জন্য ডেটা transform করতে Effect এর দরকার নেই।**
- **User event দেখাশোনা করার জন্য আপনার Effect এর প্রয়োজন নেই।**

```js {5-9}
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');

  // 🔴 Avoid: redundant state and unnecessary Effect
  const [fullName, setFullName] = useState('');
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);
  // ...
}
```

বরং, রেন্ডার করার সময় যতটা সম্ভব হিসেব করে রাখেনঃ

```js {4-5}
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  // ✅ Good: calculated during rendering
  const fullName = firstName + ' ' + lastName;
  // ...
}
```

কিন্তু, বাইরের সিস্টেমের সাথে সিঙ্ক্রোনাইজ করতে আপনার Effect *লাগবেই*।

<LearnMore path="/learn/you-might-not-need-an-effect">

অপ্রয়োজনীয় Effect কীভাবে সরাবেন শিখার জন্য পড়ুন **[আপনার Effect এর প্রয়োজন নাও পড়তে পারে](/learn/you-might-not-need-an-effect)**

</LearnMore>

## Reactive effects এর জীবনচক্র {/*lifecycle-of-reactive-effects*/}

Effect এর জীবনচক্র কম্পোনেন্টের চেয়ে আলাদা। কম্পোনেন্ট মাউন্ট, আপডেট অথবা আনমাউন্ট করতে পারে। একটি Effect কেবল দুটি কাজ করতে পারে: কিছু সিঙ্ক্রোনাইজ শুরু করা, এবং পরে এটি সিঙ্ক্রোনাইজ বন্ধ করা। আপনার Effect যদি props এবং state এর উপর নির্ভর করে থাকে যা সময়ের সাথে সাথে পরিবর্তিত হয়, তবে এই চক্রটি একাধিকবার ঘটতে পারে।

এই Effect টি `roomId` prop এর মানের উপর নির্ভর করে। Prop হল *reactive value,* যার অর্থ তারা একটি re-render এ পরিবর্তিত হতে পারে। লক্ষ্য করুন যে `roomId` পরিবর্তিত হলে Effect *re-synchronize* করে। (এবং সার্ভারের সাথে পুনরায় সংযোগ স্থাপন করে):

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

```js chat.js
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

React একটি linter rule দেয় যা লক্ষ্য করে যে আপনি আপনার Effect এর ডিপেন্ডেন্সি সঠিকভাবে নির্দিষ্ট করেছেন কিনা। যদি আপনি উপরের উদাহরণে ডিপেন্ডেন্সিগুলির তালিকায় `roomId` উল্লেখ করতে ভুলে যান, তবে linter স্বয়ংক্রিয়ভাবে সেই বাগটি খুঁজে বের করবে।

<LearnMore path="/learn/lifecycle-of-reactive-effects">

একটা কম্পোনেন্টের জীবনচক্র থেকে একটা Effect এর জীবনচক্র কীভাবে আলাদা শিখার জন্য পড়ুন **[Reactive Events এর জীবনচক্র](/learn/lifecycle-of-reactive-effects)**

</LearnMore>

## Events থেকে Effects আলাদা করা {/*separating-events-from-effects*/}

<Wip>

এই সেকশনে একটি **গবেষণামূলক API** নিয়ে বিবরণ দেওয়া হয়েছে **যা এখনো React এর কোন স্টেবল ভার্শনে উন্মুক্ত করা হয়নি**।

</Wip>

Event handler-গুলি তখনি পুনরায় চালানো হয় যখন আপনি একই interaction আবার করেন। Event handler এর বিপরীতে, Effects পুনরায় সিঙ্ক্রোনাইজ করে যদি তারা এমন কোন মান read করে যা আগের রেন্ডার থেকে আলাদা, যেমন props বা state এর মান। মাঝে মাঝে, আপনি দুটি আচরণের মিশ্রণ চান: এমন একটি Effect যা কিছু মানের Response এ আবার চলে, কিন্তু অন্যান্যগুলোর বিষয়ে re-run করে না। 

Effect এর মধ্য থাকা সব কোড *reactive.* এটা আবার চলবে যদি এটা re-render এর কারণে পরিবর্তিত কোন মান read করে। উদাহরণস্বরূপ, এই Effect টা চ্যাটের সাথে পুনরায় সংযোগ স্থাপন করবে যদি `roomId` অথবা `theme` এর মান পরিবর্তিত হয়।

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

```js
import { useState, useEffect } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, theme]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
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
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        theme={isDark ? 'dark' : 'light'} 
      />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  let connectedCallback;
  let timeout;
  return {
    connect() {
      timeout = setTimeout(() => {
        if (connectedCallback) {
          connectedCallback();
        }
      }, 100);
    },
    on(event, callback) {
      if (connectedCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'connected') {
        throw Error('Only "connected" event is supported.');
      }
      connectedCallback = callback;
    },
    disconnect() {
      clearTimeout(timeout);
    }
  };
}
```

```js notifications.js
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
label { display: block; margin-top: 10px; }
```

</Sandpack>

এটি আদর্শ নয়। আপনি কেবলমাত্র `roomId` পরিবর্তিত হলে চ্যাটে পুনরায় সংযোগ করতে চান। `theme` পরিবর্তন করা হলেই চ্যাটে পুনরায় সংযোগ করা উচিত নয়! আপনার Effect থেকে theme read করার কোডটি একটি *Effect Event* এ সরিয়ে নিন:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
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

```js
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      onConnected();
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
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
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        theme={isDark ? 'dark' : 'light'} 
      />
    </>
  );
}
```

```js chat.js
export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  let connectedCallback;
  let timeout;
  return {
    connect() {
      timeout = setTimeout(() => {
        if (connectedCallback) {
          connectedCallback();
        }
      }, 100);
    },
    on(event, callback) {
      if (connectedCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'connected') {
        throw Error('Only "connected" event is supported.');
      }
      connectedCallback = callback;
    },
    disconnect() {
      clearTimeout(timeout);
    }
  };
}
```

```js notifications.js hidden
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
label { display: block; margin-top: 10px; }
```

</Sandpack>

Code inside Effect Events isn't reactive, so changing the `theme` no longer makes your Effect re-connect.

<LearnMore path="/learn/separating-events-from-effects">

Read **[Separating Events from Effects](/learn/separating-events-from-effects)** to learn how to prevent some values from re-triggering Effects.

</LearnMore>

## Effect dependencies সরানো {/*removing-effect-dependencies*/}

আপনি যখন একটি Effect লিখেন, linter যাচাই করবে যে আপনি Effect এর ডিপেন্ডেন্সিগুলির তালিকায় Effect যে সমস্ত reactive মান (যেমন props এবং state) read করে তার সব অন্তর্ভুক্ত করেছেন কি না। এটি নিশ্চিত করে যে আপনার Effect আপনার কম্পোনেন্টের সর্বশেষ props এবং state এর সাথে সিঙ্ক্রোনাইজেশন বজায় রাখছে। অপ্রয়োজনীয় ডিপেন্ডেন্সিগুলি আপনার Effect অতিরিক্ত বার চালাতে পারে, বা এমনকি একটি কখনো শেষ হবে না এমন লুপ তৈরি করতে পারে। আপনি তাদের কীভাবে সরাবেন তা case এর উপর নির্ভর করে।

উদাহরণস্বরূপ, এই Effect `options` অবজেক্টের উপর নির্ভর করে যা আপনি প্রতিবার ইনপুট পরিবর্তন করলে  পুনরায় তৈরি হয়:

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

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

```js chat.js
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

আপনি চাইবেন না যে প্রতিবার আপনি মেসেজ লিখতে গেলেই চ্যাট পুনরায় সংযোগ স্থাপন করুক। এই সমস্যা সমাধান করতে, `options` অবজেক্টের উৎপত্তি Effect এর মধ্যে নিয়ে যান। এতে Effect কেবল মাত্র `roomId` স্ট্রিং এর উপর নির্ভর করবে।

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

```js chat.js
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

লক্ষ্য করুন যে আপনি `options` ডিপেন্ডেন্সিটি সরানোর জন্য আপনি শুরুতে ডিপেন্ডেন্সি তালিকা ঠিক করতে যাননি। এটা করা ভুল হবে। এর পরিবর্তে, আপনি আশপাশের কোড পরিবর্তন করেছেন যাতে ডিপেন্ডেন্সিটি *অপ্রয়োজনীয়* হয়। ধরে নিন যে, ডিপেন্ডেন্সি তালিকা হচ্ছে আপনার Effect এর কোড দ্বারা ব্যবহৃত সমস্ত reactive মানের তালিকা। আপনি সচেতনভাবে ঠিক করেন না যে আপনি ঐ তালিকায় কী রাখবেন। বরং, তালিকাটি আপনার কোডকে ব্যাখ্যা করে। ডিপেন্ডেন্সি তালিকা পরিবর্তন করতে, কোড পরিবর্তন করুন।

<LearnMore path="/learn/removing-effect-dependencies">

কী করবেন যেন আপনার Effect কম বার re-run হয়, শেখার জন্য **[Effect ডিপেন্ডেন্সি সরানো](/learn/removing-effect-dependencies)** পড়ুন।

</LearnMore>

## কাস্টম Hooks এর সাহায্যে লজিকের পুনর্ব্যবহার {/*reusing-logic-with-custom-hooks*/}

React এ `useState`, `useContext`, and `useEffect` এর মত built-in hooks আছে। মাঝে মাঝে, আপনার মনে হবে যে আরও নির্দিষ্ট কোন উদ্দেশ্যের জন্য একটি Hook থাকত যেমন, ডেটা নিয়ে আসা, ব্যবহারকারী অনলাইনে আছেন কি না তা নজরে রাখা, বা একটি চ্যাট রুমের সাথে সংযোগ করা। এটি করার জন্য, আপনি আপনার অ্যাপ্লিকেশনের প্রয়োজনীয়তা মাথায় রেখে আপনার নিজস্ব Hooks তৈরি করতে পারেন।

এই উদাহরণে, `usePointerPosition` কাস্টম Hook-টি কার্সরের অবস্থান নজরে রাখে, অন্যদিকে `useDelayedValue` কাস্টম Hook-টি এমন একটু মান return করে যা আপনার pass করা মান থেকে একটি নির্দিষ্ট সংখ্যক মিলিসেকেন্ডের ব্যবধানে "পিছিয়ে আছে"। স্যান্ডবক্সের প্রিভিউ অংশের উপরে কার্সর সরিয়ে খ্যেয়াল করুন যে কার্সরের পিছনে কয়েকটি বিন্দুর একটি পথরেখা দেখা যাচ্ছেঃ

<Sandpack>

```js
import { usePointerPosition } from './usePointerPosition.js';
import { useDelayedValue } from './useDelayedValue.js';

export default function Canvas() {
  const pos1 = usePointerPosition();
  const pos2 = useDelayedValue(pos1, 100);
  const pos3 = useDelayedValue(pos2, 200);
  const pos4 = useDelayedValue(pos3, 100);
  const pos5 = useDelayedValue(pos4, 50);
  return (
    <>
      <Dot position={pos1} opacity={1} />
      <Dot position={pos2} opacity={0.8} />
      <Dot position={pos3} opacity={0.6} />
      <Dot position={pos4} opacity={0.4} />
      <Dot position={pos5} opacity={0.2} />
    </>
  );
}

function Dot({ position, opacity }) {
  return (
    <div style={{
      position: 'absolute',
      backgroundColor: 'pink',
      borderRadius: '50%',
      opacity,
      transform: `translate(${position.x}px, ${position.y}px)`,
      pointerEvents: 'none',
      left: -20,
      top: -20,
      width: 40,
      height: 40,
    }} />
  );
}
```

```js usePointerPosition.js
import { useState, useEffect } from 'react';

export function usePointerPosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  useEffect(() => {
    function handleMove(e) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
  }, []);
  return position;
}
```

```js useDelayedValue.js
import { useState, useEffect } from 'react';

export function useDelayedValue(value, delay) {
  const [delayedValue, setDelayedValue] = useState(value);

  useEffect(() => {
    setTimeout(() => {
      setDelayedValue(value);
    }, delay);
  }, [value, delay]);

  return delayedValue;
}
```

```css
body { min-height: 300px; }
```

</Sandpack>

আপনি কাস্টম Hooks তৈরি করতে পারেন, এগুলোকে একত্রিত করতে পারেন, এদের মধ্যে ডাটা pass করতে পারেন, এবং কম্পোনেন্টগুলির মধ্যে এদের পুনরায় ব্যবহার করতে পারেন। আপনার অ্যাপ বড় হবার সাথে সাথে, আপনি নতুন করে Effects কম লিখবেন কারণ আপনি ইতোমধ্যে লিখেছেন এমন কাস্টম Hooks পুনরায় ব্যবহার করতে পারবেন। React কমিউনিটি দেখাশোনা করে এমন বেশ অনেকগুলি ভাল কাস্টম Hook রয়েছে।

<LearnMore path="/learn/reusing-logic-with-custom-hooks">

কম্পোনেন্টের মধ্যে লজিক শেয়ার কীভাবে করতে হয়ে শেখার জন্য পড়ুন  **[কাস্টম Hooks এর সাহায্যে লজিকের পুনর্ব্যবহার](/learn/reusing-logic-with-custom-hooks)**।

</LearnMore>

## এর পর কী? {/*whats-next*/}

এই অধ্যায়টি বিস্তারিতভাবে পড়ার জন্য চলে যান [Ref এর সাহায্য value referencing](/learn/referencing-values-with-refs) অংশে।
