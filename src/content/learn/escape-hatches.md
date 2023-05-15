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

In development, React will immediately run and clean up your Effect one extra time. This is why you see `"✅ Connecting..."` printed twice. This ensures that you don't forget to implement the cleanup function.
ডেভেলপমেন্টে, React আপনার Effect টি তাৎক্ষণিকভাবে চালাবে এবং একবার অতিরিক্ত পরিষ্কার করবে। এই কারণেই আপনি "✅ Connecting..."  দুবার। এটি নিশ্চিত করে যে আপনি cleanup function বাস্তবায়ন করতে ভুলবেন না।

<LearnMore path="/learn/synchronizing-with-effects">

Read **[Synchronizing with Effects](/learn/synchronizing-with-effects)** to learn how to synchronize components with external systems.

</LearnMore>

## You Might Not Need An Effect {/*you-might-not-need-an-effect*/}

Effects are an escape hatch from the React paradigm. They let you "step outside" of React and synchronize your components with some external system. If there is no external system involved (for example, if you want to update a component's state when some props or state change), you shouldn't need an Effect. Removing unnecessary Effects will make your code easier to follow, faster to run, and less error-prone.

There are two common cases in which you don't need Effects:
- **You don't need Effects to transform data for rendering.**
- **You don't need Effects to handle user events.**

For example, you don't need an Effect to adjust some state based on other state:

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

Instead, calculate as much as you can while rendering:

```js {4-5}
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  // ✅ Good: calculated during rendering
  const fullName = firstName + ' ' + lastName;
  // ...
}
```

However, you *do* need Effects to synchronize with external systems. 

<LearnMore path="/learn/you-might-not-need-an-effect">

Read **[You Might Not Need an Effect](/learn/you-might-not-need-an-effect)** to learn how to remove unnecessary Effects.

</LearnMore>

## Lifecycle of reactive effects {/*lifecycle-of-reactive-effects*/}

Effects have a different lifecycle from components. Components may mount, update, or unmount. An Effect can only do two things: to start synchronizing something, and later to stop synchronizing it. This cycle can happen multiple times if your Effect depends on props and state that change over time.

This Effect depends on the value of the `roomId` prop. Props are *reactive values,* which means they can change on a re-render. Notice that the Effect *re-synchronizes* (and re-connects to the server) if `roomId` changes:

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

React provides a linter rule to check that you've specified your Effect's dependencies correctly. If you forget to specify `roomId` in the list of dependencies in the above example, the linter will find that bug automatically.

<LearnMore path="/learn/lifecycle-of-reactive-effects">

Read **[Lifecycle of Reactive Events](/learn/lifecycle-of-reactive-effects)** to learn how an Effect's lifecycle is different from a component's.

</LearnMore>

## Separating events from Effects {/*separating-events-from-effects*/}

<Wip>

This section describes an **experimental API that has not yet been released** in a stable version of React.

</Wip>

Event handlers only re-run when you perform the same interaction again. Unlike event handlers, Effects re-synchronize if any of the values they read, like props or state, are different than during last render. Sometimes, you want a mix of both behaviors: an Effect that re-runs in response to some values but not others.

All code inside Effects is *reactive.* It will run again if some reactive value it reads has changed due to a re-render. For example, this Effect will re-connect to the chat if either `roomId` or `theme` have changed:

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

## Removing Effect dependencies {/*removing-effect-dependencies*/}

When you write an Effect, the linter will verify that you've included every reactive value (like props and state) that the Effect reads in the list of your Effect's dependencies. This ensures that your Effect remains synchronized with the latest props and state of your component. Unnecessary dependencies may cause your Effect to run too often, or even create an infinite loop. The way you remove them depends on the case.

For example, this Effect depends on the `options` object which gets re-created every time you edit the input:

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

You don't want the chat to re-connect every time you start typing a message in that chat. To fix this problem, move creation of the `options` object inside the Effect so that the Effect only depends on the `roomId` string:

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

Notice that you didn't start by editing the dependency list to remove the `options` dependency. That would be wrong. Instead, you changed the surrounding code so that the dependency became *unnecessary.* Think of the dependency list as a list of all the reactive values used by your Effect's code. You don't intentionally choose what to put on that list. The list describes your code. To change the dependency list, change the code.

<LearnMore path="/learn/removing-effect-dependencies">

Read **[Removing Effect Dependencies](/learn/removing-effect-dependencies)** to learn how to make your Effect re-run less often.

</LearnMore>

## Reusing logic with custom Hooks {/*reusing-logic-with-custom-hooks*/}

React comes with built-in Hooks like `useState`, `useContext`, and `useEffect`. Sometimes, you’ll wish that there was a Hook for some more specific purpose: for example, to fetch data, to keep track of whether the user is online, or to connect to a chat room. To do this, you can create your own Hooks for your application's needs.

In this example, the `usePointerPosition` custom Hook tracks the cursor position, while `useDelayedValue` custom Hook returns a value that's "lagging behind" the value you passed by a certain number of milliseconds. Move the cursor over the sandbox preview area to see a moving trail of dots following the cursor:

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

You can create custom Hooks, compose them together, pass data between them, and reuse them between components. As your app grows, you will write fewer Effects by hand because you'll be able to reuse custom Hooks you already wrote. There are also many excellent custom Hooks maintained by the React community.

<LearnMore path="/learn/reusing-logic-with-custom-hooks">

Read **[Reusing Logic with Custom Hooks](/learn/reusing-logic-with-custom-hooks)** to learn how to share logic between components.

</LearnMore>

## What's next? {/*whats-next*/}

Head over to [Referencing Values with Refs](/learn/referencing-values-with-refs) to start reading this chapter page by page!
