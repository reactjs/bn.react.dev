---
title: 'Effect থেকে Event আলাদা করা'
---

<Intro>

Event handler শুধুমাত্র তখনই পুনরায় চালু হয় যখন আপনি একই interaction আবার সম্পাদন করেন। Event handler এর বিপরীতে, Effect পুনরায় সিঙ্ক্রোনাইজ হয় যদি তারা যে কোনো মান পড়ে, যেমন একটি prop বা একটি state variable, শেষ render এর সময় যা ছিল তার থেকে ভিন্ন হয়। কখনও কখনও, আপনি উভয় আচরণের মিশ্রণও চান: একটি Effect যা কিছু মানের প্রতিক্রিয়ায় পুনরায় চালু হয় কিন্তু অন্যদের জন্য নয়। এই পৃষ্ঠাটি আপনাকে শেখাবে কিভাবে এটি করতে হয়।

</Intro>

<YouWillLearn>

- কিভাবে একটি event handler এবং একটি Effect এর মধ্যে বেছে নিতে হয়
- কেন Effect reactive, এবং event handler reactive নয়
- আপনার Effect এর কোডের একটি অংশ reactive না হতে চাইলে কি করতে হবে
- Effect Event কি, এবং কিভাবে আপনার Effect থেকে সেগুলি extract করতে হয়
- কিভাবে Effect Event ব্যবহার করে Effect থেকে সর্বশেষ props এবং state পড়তে হয়

</YouWillLearn>

## Event handler এবং Effect এর মধ্যে বেছে নেওয়া {/*choosing-between-event-handlers-and-effects*/}

প্রথমে, আসুন event handler এবং Effect এর মধ্যে পার্থক্য পুনরায় দেখি।

কল্পনা করুন আপনি একটি chat room কম্পোনেন্ট implement করছেন। আপনার requirements এরকম দেখাচ্ছে:

1. আপনার কম্পোনেন্ট স্বয়ংক্রিয়ভাবে নির্বাচিত chat room এ সংযুক্ত হওয়া উচিত।
1. যখন আপনি "Send" button এ ক্লিক করেন, তখন এটি chat এ একটি message পাঠানো উচিত।

ধরা যাক আপনি ইতিমধ্যে তাদের জন্য কোড implement করেছেন, কিন্তু আপনি নিশ্চিত নন কোথায় এটি রাখবেন। আপনার কি event handler ব্যবহার করা উচিত নাকি Effect? প্রতিবার যখন আপনাকে এই প্রশ্নের উত্তর দিতে হবে, বিবেচনা করুন [*কেন* কোডটি চালানো প্রয়োজন।](/learn/synchronizing-with-effects#what-are-effects-and-how-are-they-different-from-events)

### Event handler নির্দিষ্ট interaction এর প্রতিক্রিয়ায় চালু হয় {/*event-handlers-run-in-response-to-specific-interactions*/}

ব্যবহারকারীর দৃষ্টিকোণ থেকে, একটি message পাঠানো উচিত *কারণ* নির্দিষ্ট "Send" button ক্লিক করা হয়েছিল। ব্যবহারকারী বেশ বিরক্ত হবেন যদি আপনি তাদের message অন্য কোনো সময়ে বা অন্য কোনো কারণে পাঠান। এই কারণেই একটি message পাঠানো একটি event handler হওয়া উচিত। Event handler আপনাকে নির্দিষ্ট interaction handle করতে দেয়:


```js {4-6}
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');
  // ...
  function handleSendClick() {
    sendMessage(message);
  }
  // ...
  return (
    <>
      <input value={message} onChange={e => setMessage(e.target.value)} />
      <button onClick={handleSendClick}>Send</button>
    </>
  );
}
```

একটি event handler এর সাথে, আপনি নিশ্চিত হতে পারেন যে `sendMessage(message)` *শুধুমাত্র* তখনই চালু হবে যদি ব্যবহারকারী button টি চাপেন।

### Effect যখনই synchronization প্রয়োজন তখন চালু হয় {/*effects-run-whenever-synchronization-is-needed*/}

মনে করুন আপনাকে কম্পোনেন্টটিকে chat room এর সাথে সংযুক্ত রাখতে হবে। সেই কোডটি কোথায় যায়?

এই কোডটি চালানোর *কারণ* কোনো নির্দিষ্ট interaction নয়। ব্যবহারকারী কেন বা কিভাবে chat room screen এ navigate করেছে তা কোনো ব্যাপার না। এখন যেহেতু তারা এটি দেখছে এবং এটির সাথে interact করতে পারে, কম্পোনেন্টটিকে নির্বাচিত chat server এর সাথে সংযুক্ত থাকতে হবে। এমনকি যদি chat room কম্পোনেন্ট আপনার app এর প্রাথমিক screen হয়, এবং ব্যবহারকারী কোনো interaction সম্পাদন না করে থাকে, তবুও আপনাকে *এখনও* সংযুক্ত হতে হবে। এই কারণেই এটি একটি Effect:

```js {3-9}
function ChatRoom({ roomId }) {
  // ...
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId]);
  // ...
}
```

এই কোডের সাথে, আপনি নিশ্চিত হতে পারেন যে বর্তমানে নির্বাচিত chat server এর সাথে সর্বদা একটি সক্রিয় connection আছে, *নির্বিশেষে* ব্যবহারকারী দ্বারা সম্পাদিত নির্দিষ্ট interaction এর। ব্যবহারকারী শুধুমাত্র আপনার app খুলেছে, একটি ভিন্ন room নির্বাচন করেছে, বা অন্য screen এ navigate করেছে এবং ফিরে এসেছে, আপনার Effect নিশ্চিত করে যে কম্পোনেন্টটি বর্তমানে নির্বাচিত room এর সাথে *সিঙ্ক্রোনাইজড থাকবে*, এবং [যখনই এটি প্রয়োজন তখন পুনরায় সংযুক্ত হবে।](/learn/lifecycle-of-reactive-effects#why-synchronization-may-need-to-happen-more-than-once)


<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection, sendMessage } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  function handleSendClick() {
    sendMessage(message);
  }

  return (
    <>
      <h1>Welcome to the {roomId} room!</h1>
      <input value={message} onChange={e => setMessage(e.target.value)} />
      <button onClick={handleSendClick}>Send</button>
    </>
  );
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
export function sendMessage(message) {
  console.log('🔵 You sent: ' + message);
}

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
input, select { margin-right: 20px; }
```

</Sandpack>

## Reactive value এবং reactive logic {/*reactive-values-and-reactive-logic*/}

স্বজ্ঞাতভাবে, আপনি বলতে পারেন যে event handler সর্বদা "manually" trigger হয়, উদাহরণস্বরূপ একটি button ক্লিক করে। অন্যদিকে Effect, "automatic": তারা চালু হয় এবং পুনরায় চালু হয় যতবার সিঙ্ক্রোনাইজড থাকার প্রয়োজন হয়।

এটি সম্পর্কে চিন্তা করার আরও সুনির্দিষ্ট উপায় আছে।

আপনার কম্পোনেন্টের body এর ভিতরে ঘোষিত Props, state, এবং variable গুলিকে <CodeStep step={2}>reactive value</CodeStep> বলা হয়। এই উদাহরণে, `serverUrl` একটি reactive value নয়, কিন্তু `roomId` এবং `message` হল। তারা rendering data flow এ অংশগ্রহণ করে:

```js [[2, 3, "roomId"], [2, 4, "message"]]
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  // ...
}
```

এই ধরনের reactive value একটি re-render এর কারণে পরিবর্তিত হতে পারে। উদাহরণস্বরূপ, ব্যবহারকারী `message` edit করতে পারে বা একটি dropdown এ একটি ভিন্ন `roomId` বেছে নিতে পারে। Event handler এবং Effect পরিবর্তনের প্রতি ভিন্নভাবে প্রতিক্রিয়া জানায়:

- **Event handler এর ভিতরের logic *reactive নয়।*** এটি আবার চালু হবে না যদি না ব্যবহারকারী একই interaction (যেমন একটি click) আবার সম্পাদন করে। Event handler reactive value পড়তে পারে তাদের পরিবর্তনের প্রতি "react" না করে।
- **Effect এর ভিতরের logic *reactive।*** যদি আপনার Effect একটি reactive value পড়ে, [আপনাকে এটি একটি dependency হিসাবে নির্দিষ্ট করতে হবে।](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) তারপর, যদি একটি re-render সেই মানটি পরিবর্তন করে, React নতুন মান দিয়ে আপনার Effect এর logic পুনরায় চালু করবে।

আসুন এই পার্থক্যটি চিত্রিত করতে আগের উদাহরণটি পুনরায় দেখি।


### Event handler এর ভিতরের logic reactive নয় {/*logic-inside-event-handlers-is-not-reactive*/}

কোডের এই লাইনটি দেখুন। এই logic কি reactive হওয়া উচিত নাকি নয়?

```js [[2, 2, "message"]]
    // ...
    sendMessage(message);
    // ...
```

ব্যবহারকারীর দৃষ্টিকোণ থেকে, **`message` এ একটি পরিবর্তন _মানে এই নয়_ যে তারা একটি message পাঠাতে চায়।** এর মানে শুধুমাত্র এই যে ব্যবহারকারী টাইপ করছে। অন্য কথায়, যে logic একটি message পাঠায় তা reactive হওয়া উচিত নয়। এটি শুধুমাত্র <CodeStep step={2}>reactive value</CodeStep> পরিবর্তিত হওয়ার কারণে আবার চালু হওয়া উচিত নয়। এই কারণেই এটি event handler এ থাকে:

```js {2}
  function handleSendClick() {
    sendMessage(message);
  }
```

Event handler reactive নয়, তাই `sendMessage(message)` শুধুমাত্র তখনই চালু হবে যখন ব্যবহারকারী Send button চাপবে।

### Effect এর ভিতরের logic reactive {/*logic-inside-effects-is-reactive*/}

এখন আসুন এই লাইনগুলিতে ফিরে যাই:

```js [[2, 2, "roomId"]]
    // ...
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    // ...
```

ব্যবহারকারীর দৃষ্টিকোণ থেকে, **`roomId` এ একটি পরিবর্তন *মানে* যে তারা একটি ভিন্ন room এ সংযুক্ত হতে চায়।** অন্য কথায়, room এ সংযুক্ত হওয়ার logic reactive হওয়া উচিত। আপনি *চান* যে কোডের এই লাইনগুলি <CodeStep step={2}>reactive value</CodeStep> এর সাথে "keep up" করুক, এবং সেই মান ভিন্ন হলে আবার চালু হোক। এই কারণেই এটি একটি Effect এ থাকে:

```js {2-3}
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect()
    };
  }, [roomId]);
```

Effect reactive, তাই `createConnection(serverUrl, roomId)` এবং `connection.connect()` `roomId` এর প্রতিটি স্বতন্ত্র মানের জন্য চালু হবে। আপনার Effect chat connection টিকে বর্তমানে নির্বাচিত room এর সাথে সিঙ্ক্রোনাইজড রাখে।

## Effect থেকে non-reactive logic বের করা {/*extracting-non-reactive-logic-out-of-effects*/}

যখন আপনি reactive logic এর সাথে non-reactive logic মিশ্রিত করতে চান তখন জিনিসগুলি আরও জটিল হয়ে যায়।

উদাহরণস্বরূপ, কল্পনা করুন যে আপনি ব্যবহারকারী chat এ সংযুক্ত হলে একটি notification দেখাতে চান। আপনি props থেকে বর্তমান theme (dark বা light) পড়েন যাতে আপনি সঠিক রঙে notification দেখাতে পারেন:

```js {1,4-6}
function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    // ...
```

তবে, `theme` একটি reactive value (এটি re-rendering এর ফলে পরিবর্তিত হতে পারে), এবং [একটি Effect দ্বারা পড়া প্রতিটি reactive value অবশ্যই তার dependency হিসাবে ডিক্লেয়ার করতে হবে।](/learn/lifecycle-of-reactive-effects#react-verifies-that-you-specified-every-reactive-value-as-a-dependency) এখন আপনাকে আপনার Effect এর dependency হিসাবে `theme` নির্দিষ্ট করতে হবে:

```js {5,11}
function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    return () => {
      connection.disconnect()
    };
  }, [roomId, theme]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

এই উদাহরণটি নিয়ে খেলুন এবং দেখুন আপনি কি এই user experience এর সমস্যাটি খুঁজে পেতে পারেন:


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

```js src/chat.js
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
label { display: block; margin-top: 10px; }
```

</Sandpack>

যখন `roomId` পরিবর্তিত হয়, chat আপনার প্রত্যাশা অনুযায়ী পুনরায় সংযুক্ত হয়। কিন্তু যেহেতু `theme` ও একটি dependency, chat *এছাড়াও* প্রতিবার পুনরায় সংযুক্ত হয় যখন আপনি dark এবং light theme এর মধ্যে switch করেন। এটা ভালো নয়!

অন্য কথায়, আপনি *চান না* যে এই লাইনটি reactive হোক, যদিও এটি একটি Effect এর ভিতরে আছে (যা reactive):

```js
      // ...
      showNotification('Connected!', theme);
      // ...
```

আপনার এই non-reactive logic কে এর চারপাশের reactive Effect থেকে আলাদা করার একটি উপায় প্রয়োজন।

### একটি Effect Event ডিক্লেয়ার করা {/*declaring-an-effect-event*/}

<<<<<<< HEAD
<Wip>

এই অনুচ্ছেদটি একটি **experimental API বর্ণনা করে যা এখনও React এর একটি stable version এ release হয়নি।**

</Wip>

[`useEffectEvent`](/reference/react/experimental_useEffectEvent) নামক একটি বিশেষ Hook ব্যবহার করুন আপনার Effect থেকে এই non-reactive logic বের করতে:
=======
Use a special Hook called [`useEffectEvent`](/reference/react/useEffectEvent) to extract this non-reactive logic out of your Effect:
>>>>>>> 40ea071c846b3ab1232391bab15d31f508913bf4

```js {1,4-6}
import { useEffect, useEffectEvent } from 'react';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });
  // ...
```

এখানে, `onConnected` কে একটি *Effect Event* বলা হয়। এটি আপনার Effect logic এর একটি অংশ, কিন্তু এটি একটি event handler এর মতো অনেক বেশি আচরণ করে। এর ভিতরের logic reactive নয়, এবং এটি সর্বদা আপনার props এবং state এর সর্বশেষ মান "দেখে"।

এখন আপনি আপনার Effect এর ভিতর থেকে `onConnected` Effect Event কল করতে পারেন:


```js {2-4,9,13}
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
  }, [roomId]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
```

এটি সমস্যার সমাধান করে। লক্ষ্য করুন যে আপনাকে আপনার Effect এর dependency এর তালিকা থেকে `theme` *সরাতে* হয়েছিল, কারণ এটি আর Effect এ ব্যবহৃত হচ্ছে না। আপনাকে এটিতে `onConnected` *যোগ* করারও প্রয়োজন নেই, কারণ **Effect Event reactive নয় এবং dependency থেকে বাদ দিতে হবে।**

যাচাই করুন যে নতুন আচরণটি আপনার প্রত্যাশা অনুযায়ী কাজ করে:

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
import { useEffectEvent } from 'react';
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

```js src/chat.js
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

```js src/notifications.js hidden
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

<<<<<<< HEAD
আপনি Effect Event কে event handler এর খুব অনুরূপ হিসাবে ভাবতে পারেন। প্রধান পার্থক্য হল যে event handler user interaction এর প্রতিক্রিয়ায় চালু হয়, যেখানে Effect Event আপনার দ্বারা Effect থেকে trigger করা হয়। Effect Event আপনাকে Effect এর reactivity এবং যে কোড reactive হওয়া উচিত নয় তার মধ্যে "chain ভাঙতে" দেয়।
=======
You can think of Effect Events as being very similar to event handlers. The main difference is that event handlers run in response to user interactions, whereas Effect Events are triggered by you from Effects. Effect Events let you "break the chain" between the reactivity of Effects and code that should not be reactive.
>>>>>>> 40ea071c846b3ab1232391bab15d31f508913bf4

### Effect Event দিয়ে সর্বশেষ props এবং state পড়া {/*reading-latest-props-and-state-with-effect-events*/}

<<<<<<< HEAD
<Wip>

এই অনুচ্ছেদটি একটি **experimental API বর্ণনা করে যা এখনও React এর একটি stable version এ release হয়নি।**

</Wip>

Effect Event আপনাকে অনেক pattern ঠিক করতে দেয় যেখানে আপনি dependency linter suppress করতে প্রলুব্ধ হতে পারেন।
=======
Effect Events let you fix many patterns where you might be tempted to suppress the dependency linter.
>>>>>>> 40ea071c846b3ab1232391bab15d31f508913bf4

উদাহরণস্বরূপ, ধরুন আপনার একটি Effect আছে page visit log করার জন্য:

```js
function Page() {
  useEffect(() => {
    logVisit();
  }, []);
  // ...
}
```

পরে, আপনি আপনার site এ একাধিক route যোগ করেন। এখন আপনার `Page` কম্পোনেন্ট বর্তমান path সহ একটি `url` prop পায়। আপনি আপনার `logVisit` call এর অংশ হিসাবে `url` pass করতে চান, কিন্তু dependency linter অভিযোগ করে:

```js {1,3}
function Page({ url }) {
  useEffect(() => {
    logVisit(url);
  }, []); // 🔴 React Hook useEffect has a missing dependency: 'url'
  // ...
}
```

কোডটি কি করতে চায় তা নিয়ে চিন্তা করুন। আপনি বিভিন্ন URL এর জন্য একটি পৃথক visit log করতে *চান* কারণ প্রতিটি URL একটি ভিন্ন page প্রতিনিধিত্ব করে। অন্য কথায়, এই `logVisit` call `url` এর সাপেক্ষে reactive *হওয়া উচিত*। এই কারণে, এই ক্ষেত্রে, dependency linter অনুসরণ করা এবং `url` কে একটি dependency হিসাবে যোগ করা বোধগম্য:

```js {4}
function Page({ url }) {
  useEffect(() => {
    logVisit(url);
  }, [url]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
}
```

এখন ধরুন আপনি প্রতিটি page visit এর সাথে shopping cart এ item এর সংখ্যা অন্তর্ভুক্ত করতে চান:


```js {2-3,6}
function Page({ url }) {
  const { items } = useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  useEffect(() => {
    logVisit(url, numberOfItems);
  }, [url]); // 🔴 React Hook useEffect has a missing dependency: 'numberOfItems'
  // ...
}
```

আপনি Effect এর ভিতরে `numberOfItems` ব্যবহার করেছেন, তাই linter আপনাকে এটি একটি dependency হিসাবে যোগ করতে বলে। তবে, আপনি *চান না* যে `logVisit` call `numberOfItems` এর সাপেক্ষে reactive হোক। যদি ব্যবহারকারী shopping cart এ কিছু রাখে, এবং `numberOfItems` পরিবর্তিত হয়, এর *মানে এই নয়* যে ব্যবহারকারী আবার page টি visit করেছে। অন্য কথায়, *page visit করা* হল, কিছু অর্থে, একটি "event"। এটি সময়ের একটি সুনির্দিষ্ট মুহূর্তে ঘটে।

কোডটি দুটি অংশে বিভক্ত করুন:

```js {5-7,10}
function Page({ url }) {
  const { items } = useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  const onVisit = useEffectEvent(visitedUrl => {
    logVisit(visitedUrl, numberOfItems);
  });

  useEffect(() => {
    onVisit(url);
  }, [url]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
  // ...
}
```

এখানে, `onVisit` একটি Effect Event। এর ভিতরের কোড reactive নয়। এই কারণেই আপনি `numberOfItems` (বা অন্য কোনো reactive value!) ব্যবহার করতে পারেন চিন্তা না করে যে এটি পরিবর্তনের সময় আশেপাশের কোড পুনরায় execute হবে।

অন্যদিকে, Effect নিজেই reactive থাকে। Effect এর ভিতরের কোড `url` prop ব্যবহার করে, তাই Effect প্রতিটি re-render এর পরে একটি ভিন্ন `url` দিয়ে পুনরায় চালু হবে। এটি, পরিবর্তে, `onVisit` Effect Event কল করবে।

ফলস্বরূপ, আপনি `url` এর প্রতিটি পরিবর্তনের জন্য `logVisit` কল করবেন, এবং সর্বদা সর্বশেষ `numberOfItems` পড়বেন। তবে, যদি `numberOfItems` নিজে থেকে পরিবর্তিত হয়, এটি কোনো কোড পুনরায় চালু করবে না।

<Note>

আপনি হয়তো ভাবছেন আপনি কি কোনো argument ছাড়াই `onVisit()` কল করতে পারেন, এবং এর ভিতরে `url` পড়তে পারেন:

```js {2,6}
  const onVisit = useEffectEvent(() => {
    logVisit(url, numberOfItems);
  });

  useEffect(() => {
    onVisit();
  }, [url]);
```

এটি কাজ করবে, কিন্তু এই `url` টি আপনার Effect Event এ স্পষ্টভাবে pass করা ভালো। **আপনার Effect Event এ argument হিসাবে `url` pass করে, আপনি বলছেন যে একটি ভিন্ন `url` দিয়ে একটি page visit করা ব্যবহারকারীর দৃষ্টিকোণ থেকে একটি পৃথক "event" গঠন করে।** `visitedUrl` হল ঘটে যাওয়া "event" এর একটি *অংশ*:

```js {1-2,6}
  const onVisit = useEffectEvent(visitedUrl => {
    logVisit(visitedUrl, numberOfItems);
  });

  useEffect(() => {
    onVisit(url);
  }, [url]);
```

যেহেতু আপনার Effect Event স্পষ্টভাবে `visitedUrl` এর জন্য "জিজ্ঞাসা" করে, এখন আপনি ভুলবশত Effect এর dependency থেকে `url` সরাতে পারবেন না। যদি আপনি `url` dependency সরান (distinct page visit কে একটি হিসাবে গণনা করা হয়), linter আপনাকে এটি সম্পর্কে সতর্ক করবে। আপনি চান `onVisit` `url` এর সাপেক্ষে reactive হোক, তাই এর ভিতরে `url` পড়ার পরিবর্তে (যেখানে এটি reactive হবে না), আপনি এটি আপনার Effect *থেকে* pass করেন।

এটি বিশেষভাবে গুরুত্বপূর্ণ হয়ে ওঠে যদি Effect এর ভিতরে কিছু asynchronous logic থাকে:

```js {6,8}
  const onVisit = useEffectEvent(visitedUrl => {
    logVisit(visitedUrl, numberOfItems);
  });

  useEffect(() => {
    setTimeout(() => {
      onVisit(url);
    }, 5000); // visit log করা delay করুন
  }, [url]);
```

এখানে, `onVisit` এর ভিতরে `url` *সর্বশেষ* `url` এর সাথে মিলে যায় (যা ইতিমধ্যে পরিবর্তিত হতে পারে), কিন্তু `visitedUrl` সেই `url` এর সাথে মিলে যায় যা মূলত এই Effect (এবং এই `onVisit` call) চালু করেছিল।

</Note>

<DeepDive>

#### পরিবর্তে dependency linter suppress করা কি ঠিক? {/*is-it-okay-to-suppress-the-dependency-linter-instead*/}

বিদ্যমান codebase গুলিতে, আপনি কখনও কখনও এইভাবে lint rule suppress করা দেখতে পারেন:

```js {expectedErrors: {'react-compiler': [8]}} {7-9}
function Page({ url }) {
  const { items } = useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  useEffect(() => {
    logVisit(url, numberOfItems);
    // 🔴 এইভাবে linter suppress করা এড়িয়ে চলুন:
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [url]);
  // ...
}
```

<<<<<<< HEAD
`useEffectEvent` React এর একটি stable অংশ হয়ে গেলে, আমরা সুপারিশ করি **কখনও linter suppress করবেন না**।
=======
We recommend **never suppressing the linter**.
>>>>>>> 40ea071c846b3ab1232391bab15d31f508913bf4

Rule suppress করার প্রথম অসুবিধা হল যে React আর আপনাকে সতর্ক করবে না যখন আপনার Effect কে একটি নতুন reactive dependency এর প্রতি "react" করতে হবে যা আপনি আপনার কোডে introduce করেছেন। আগের উদাহরণে, আপনি dependency তে `url` যোগ করেছিলেন *কারণ* React আপনাকে এটি করতে মনে করিয়ে দিয়েছিল। আপনি linter disable করলে সেই Effect এ ভবিষ্যতের কোনো edit এর জন্য আর এই ধরনের reminder পাবেন না। এটি bug এর দিকে নিয়ে যায়।

এখানে linter suppress করার কারণে সৃষ্ট একটি বিভ্রান্তিকর bug এর উদাহরণ। এই উদাহরণে, `handleMove` function টি বর্তমান `canMove` state variable মান পড়ার কথা যাতে dot cursor অনুসরণ করবে কিনা তা সিদ্ধান্ত নিতে পারে। তবে, `handleMove` এর ভিতরে `canMove` সর্বদা `true`।

আপনি কি দেখতে পাচ্ছেন কেন?


<Sandpack>

```js {expectedErrors: {'react-compiler': [16]}}
import { useState, useEffect } from 'react';

export default function App() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [canMove, setCanMove] = useState(true);

  function handleMove(e) {
    if (canMove) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
  }

  useEffect(() => {
    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  return (
    <>
      <label>
        <input type="checkbox"
          checked={canMove}
          onChange={e => setCanMove(e.target.checked)}
        />
        The dot is allowed to move
      </label>
      <hr />
      <div style={{
        position: 'absolute',
        backgroundColor: 'pink',
        borderRadius: '50%',
        opacity: 0.6,
        transform: `translate(${position.x}px, ${position.y}px)`,
        pointerEvents: 'none',
        left: -20,
        top: -20,
        width: 40,
        height: 40,
      }} />
    </>
  );
}
```

```css
body {
  height: 200px;
}
```

</Sandpack>

এই কোডের সমস্যা হল dependency linter suppress করা। যদি আপনি suppression সরান, আপনি দেখবেন যে এই Effect `handleMove` function এর উপর নির্ভর করা উচিত। এটি বোধগম্য: `handleMove` component body এর ভিতরে ডিক্লেয়ার করা হয়েছে, যা এটিকে একটি reactive value করে তোলে। প্রতিটি reactive value অবশ্যই একটি dependency হিসাবে নির্দিষ্ট করতে হবে, অথবা এটি সময়ের সাথে সাথে stale হতে পারে!

মূল কোডের লেখক React কে "মিথ্যা বলেছেন" যে Effect কোনো reactive value এর উপর নির্ভর করে না (`[]`)। এই কারণেই React `canMove` পরিবর্তিত হওয়ার পরে (এবং এর সাথে `handleMove`) Effect পুনরায় সিঙ্ক্রোনাইজ করেনি। কারণ React Effect পুনরায় সিঙ্ক্রোনাইজ করেনি, listener হিসাবে attached `handleMove` হল initial render এর সময় তৈরি `handleMove` function। Initial render এর সময়, `canMove` ছিল `true`, যে কারণে initial render থেকে `handleMove` চিরকাল সেই মান দেখবে।

**যদি আপনি কখনও linter suppress না করেন, আপনি কখনও stale value এর সমস্যা দেখবেন না।**

`useEffectEvent` এর সাথে, linter কে "মিথ্যা বলার" প্রয়োজন নেই, এবং কোডটি আপনার প্রত্যাশা অনুযায়ী কাজ করে:

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { useEffectEvent } from 'react';

export default function App() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [canMove, setCanMove] = useState(true);

  const onMove = useEffectEvent(e => {
    if (canMove) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
  });

  useEffect(() => {
    window.addEventListener('pointermove', onMove);
    return () => window.removeEventListener('pointermove', onMove);
  }, []);

  return (
    <>
      <label>
        <input type="checkbox"
          checked={canMove}
          onChange={e => setCanMove(e.target.checked)}
        />
        The dot is allowed to move
      </label>
      <hr />
      <div style={{
        position: 'absolute',
        backgroundColor: 'pink',
        borderRadius: '50%',
        opacity: 0.6,
        transform: `translate(${position.x}px, ${position.y}px)`,
        pointerEvents: 'none',
        left: -20,
        top: -20,
        width: 40,
        height: 40,
      }} />
    </>
  );
}
```

```css
body {
  height: 200px;
}
```

</Sandpack>

এর মানে এই নয় যে `useEffectEvent` *সর্বদা* সঠিক সমাধান। আপনার শুধুমাত্র সেই কোডের লাইনগুলিতে এটি প্রয়োগ করা উচিত যা আপনি reactive হতে চান না। উপরের sandbox এ, আপনি চাননি যে Effect এর কোড `canMove` এর সাপেক্ষে reactive হোক।

[Effect Dependency সরানো](/learn/removing-effect-dependencies) পড়ুন linter suppress করার অন্যান্য সঠিক বিকল্পের জন্য।

</DeepDive>

### Effect Event এর সীমাবদ্ধতা {/*limitations-of-effect-events*/}

<<<<<<< HEAD
<Wip>

এই অনুচ্ছেদটি একটি **experimental API বর্ণনা করে যা এখনও React এর একটি stable version এ release হয়নি।**

</Wip>

Effect Event আপনি কিভাবে ব্যবহার করতে পারেন তাতে খুবই সীমিত:
=======
Effect Events are very limited in how you can use them:
>>>>>>> 40ea071c846b3ab1232391bab15d31f508913bf4

* **শুধুমাত্র Effect এর ভিতর থেকে তাদের কল করুন।**
* **কখনও তাদের অন্য কম্পোনেন্ট বা Hook এ pass করবেন না।**

উদাহরণস্বরূপ, এইভাবে একটি Effect Event ডিক্লেয়ার এবং pass করবেন না:

```js {4-6,8}
function Timer() {
  const [count, setCount] = useState(0);

  const onTick = useEffectEvent(() => {
    setCount(count + 1);
  });

  useTimer(onTick, 1000); // 🔴 এড়িয়ে চলুন: Effect Event pass করা

  return <h1>{count}</h1>
}

function useTimer(callback, delay) {
  useEffect(() => {
    const id = setInterval(() => {
      callback();
    }, delay);
    return () => {
      clearInterval(id);
    };
  }, [delay, callback]); // dependency তে "callback" নির্দিষ্ট করতে হবে
}
```

পরিবর্তে, সর্বদা Effect Event সরাসরি সেই Effect এর পাশে ডিক্লেয়ার করুন যা তাদের ব্যবহার করে:


```js {10-12,16,21}
function Timer() {
  const [count, setCount] = useState(0);
  useTimer(() => {
    setCount(count + 1);
  }, 1000);
  return <h1>{count}</h1>
}

function useTimer(callback, delay) {
  const onTick = useEffectEvent(() => {
    callback();
  });

  useEffect(() => {
    const id = setInterval(() => {
      onTick(); // ✅ ভালো: শুধুমাত্র একটি Effect এর ভিতরে locally কল করা হয়
    }, delay);
    return () => {
      clearInterval(id);
    };
  }, [delay]); // "onTick" (একটি Effect Event) কে dependency হিসাবে নির্দিষ্ট করার প্রয়োজন নেই
}
```

Effect Event হল আপনার Effect কোডের non-reactive "pieces"। তাদের সেই Effect এর পাশে থাকা উচিত যা তাদের ব্যবহার করে।

<Recap>

- Event handler নির্দিষ্ট interaction এর প্রতিক্রিয়ায় চালু হয়।
- Effect যখনই synchronization প্রয়োজন তখন চালু হয়।
- Event handler এর ভিতরের logic reactive নয়।
- Effect এর ভিতরের logic reactive।
- আপনি Effect থেকে non-reactive logic Effect Event এ সরাতে পারেন।
- শুধুমাত্র Effect এর ভিতর থেকে Effect Event কল করুন।
- Effect Event অন্য কম্পোনেন্ট বা Hook এ pass করবেন না।

</Recap>

<Challenges>

#### একটি variable ঠিক করুন যা update হচ্ছে না {/*fix-a-variable-that-doesnt-update*/}

এই `Timer` কম্পোনেন্ট একটি `count` state variable রাখে যা প্রতি সেকেন্ডে বৃদ্ধি পায়। যে মান দ্বারা এটি বৃদ্ধি পাচ্ছে তা `increment` state variable এ সংরক্ষিত। আপনি plus এবং minus button দিয়ে `increment` variable নিয়ন্ত্রণ করতে পারেন।

তবে, আপনি যতবারই plus button ক্লিক করুন না কেন, counter এখনও প্রতি সেকেন্ডে এক করে বৃদ্ধি পাচ্ছে। এই কোডের সাথে কি সমস্যা? কেন Effect এর কোডের ভিতরে `increment` সর্বদা `1` এর সমান? ভুলটি খুঁজুন এবং ঠিক করুন।

<Hint>

এই কোড ঠিক করতে, নিয়ম অনুসরণ করাই যথেষ্ট।

</Hint>

<Sandpack>

```js {expectedErrors: {'react-compiler': [14]}}
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + increment);
    }, 1000);
    return () => {
      clearInterval(id);
    };
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

<Solution>

যথারীতি, যখন আপনি Effect এ bug খুঁজছেন, linter suppression খোঁজা দিয়ে শুরু করুন।

যদি আপনি suppression comment সরান, React আপনাকে বলবে যে এই Effect এর কোড `increment` এর উপর নির্ভর করে, কিন্তু আপনি React কে "মিথ্যা বলেছেন" যে এই Effect কোনো reactive value এর উপর নির্ভর করে না (`[]`)। dependency array তে `increment` যোগ করুন:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + increment);
    }, 1000);
    return () => {
      clearInterval(id);
    };
  }, [increment]);

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

এখন, যখন `increment` পরিবর্তিত হয়, React আপনার Effect পুনরায় সিঙ্ক্রোনাইজ করবে, যা interval restart করবে।

</Solution>

#### একটি freezing counter ঠিক করুন {/*fix-a-freezing-counter*/}

এই `Timer` কম্পোনেন্ট একটি `count` state variable রাখে যা প্রতি সেকেন্ডে বৃদ্ধি পায়। যে মান দ্বারা এটি বৃদ্ধি পাচ্ছে তা `increment` state variable এ সংরক্ষিত, যা আপনি plus এবং minus button দিয়ে নিয়ন্ত্রণ করতে পারেন। উদাহরণস্বরূপ, plus button নয়বার চাপার চেষ্টা করুন, এবং লক্ষ্য করুন যে `count` এখন প্রতি সেকেন্ডে এক এর পরিবর্তে দশ করে বৃদ্ধি পাচ্ছে।

এই user interface এর সাথে একটি ছোট সমস্যা আছে। আপনি হয়তো লক্ষ্য করবেন যে আপনি যদি প্রতি সেকেন্ডে একবারের চেয়ে দ্রুত plus বা minus button চাপতে থাকেন, timer নিজেই pause হয়ে যাচ্ছে বলে মনে হয়। এটি শুধুমাত্র আপনি শেষবার যেকোনো button চাপার এক সেকেন্ড পরে resume হয়। কেন এটি ঘটছে তা খুঁজে বের করুন, এবং সমস্যাটি ঠিক করুন যাতে timer *প্রতি* সেকেন্ডে বিরতি ছাড়াই tick করে।

<Hint>

মনে হচ্ছে যে Effect যা timer সেট আপ করে তা `increment` value এর প্রতি "react" করে। বর্তমান `increment` value ব্যবহার করে `setCount` কল করার লাইনটি কি সত্যিই reactive হওয়া প্রয়োজন?

</Hint>

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + increment);
    }, 1000);
    return () => {
      clearInterval(id);
    };
  }, [increment]);

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

<Solution>

সমস্যা হল যে Effect এর ভিতরের কোড `increment` state variable ব্যবহার করে। যেহেতু এটি আপনার Effect এর একটি dependency, `increment` এর প্রতিটি পরিবর্তন Effect কে পুনরায় সিঙ্ক্রোনাইজ করে, যা interval clear করে। যদি আপনি প্রতিবার fire হওয়ার সুযোগ পাওয়ার আগে interval clear করতে থাকেন, তাহলে মনে হবে যেন timer stall হয়ে গেছে।

সমস্যা সমাধান করতে, Effect থেকে একটি `onTick` Effect Event extract করুন:

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  const onTick = useEffectEvent(() => {
    setCount(c => c + increment);
  });

  useEffect(() => {
    const id = setInterval(() => {
      onTick();
    }, 1000);
    return () => {
      clearInterval(id);
    };
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

যেহেতু `onTick` একটি Effect Event, এর ভিতরের কোড reactive নয়। `increment` এর পরিবর্তন কোনো Effect trigger করে না।

</Solution>

#### একটি non-adjustable delay ঠিক করুন {/*fix-a-non-adjustable-delay*/}

এই উদাহরণে, আপনি interval delay customize করতে পারেন। এটি একটি `delay` state variable এ সংরক্ষিত যা দুটি button দ্বারা update করা হয়। তবে, এমনকি আপনি "plus 100 ms" button চাপতে থাকলেও যতক্ষণ না `delay` 1000 milliseconds (অর্থাৎ, এক সেকেন্ড) হয়, আপনি লক্ষ্য করবেন যে timer এখনও খুব দ্রুত (প্রতি 100 ms) বৃদ্ধি পাচ্ছে। মনে হচ্ছে আপনার `delay` এর পরিবর্তনগুলি উপেক্ষা করা হচ্ছে। bug খুঁজুন এবং ঠিক করুন।

<Hint>

Effect Event এর ভিতরের কোড reactive নয়। এমন কি কোনো ক্ষেত্রে আছে যেখানে আপনি `setInterval` call পুনরায় চালু _চাইবেন_?

</Hint>

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);
  const [delay, setDelay] = useState(100);

  const onTick = useEffectEvent(() => {
    setCount(c => c + increment);
  });

  const onMount = useEffectEvent(() => {
    return setInterval(() => {
      onTick();
    }, delay);
  });

  useEffect(() => {
    const id = onMount();
    return () => {
      clearInterval(id);
    }
  }, []);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
      <p>
        Increment delay:
        <button disabled={delay === 100} onClick={() => {
          setDelay(d => d - 100);
        }}>–100 ms</button>
        <b>{delay} ms</b>
        <button onClick={() => {
          setDelay(d => d + 100);
        }}>+100 ms</button>
      </p>
    </>
  );
}
```


```css
button { margin: 10px; }
```

</Sandpack>

<Solution>

উপরের উদাহরণের সমস্যা হল যে এটি `onMount` নামে একটি Effect Event extract করেছে কোডটি আসলে কি করা উচিত তা বিবেচনা না করে। আপনার শুধুমাত্র একটি নির্দিষ্ট কারণে Effect Event extract করা উচিত: যখন আপনি আপনার কোডের একটি অংশ non-reactive করতে চান। তবে, `setInterval` call `delay` state variable এর সাপেক্ষে reactive *হওয়া উচিত*। যদি `delay` পরিবর্তিত হয়, আপনি interval নতুন করে সেট আপ করতে চান! এই কোড ঠিক করতে, সমস্ত reactive কোড Effect এর ভিতরে ফিরিয়ে আনুন:

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);
  const [delay, setDelay] = useState(100);

  const onTick = useEffectEvent(() => {
    setCount(c => c + increment);
  });

  useEffect(() => {
    const id = setInterval(() => {
      onTick();
    }, delay);
    return () => {
      clearInterval(id);
    }
  }, [delay]);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
      <p>
        Increment delay:
        <button disabled={delay === 100} onClick={() => {
          setDelay(d => d - 100);
        }}>–100 ms</button>
        <b>{delay} ms</b>
        <button onClick={() => {
          setDelay(d => d + 100);
        }}>+100 ms</button>
      </p>
    </>
  );
}
```

```css
button { margin: 10px; }
```

</Sandpack>

সাধারণভাবে, আপনার `onMount` এর মতো function সম্পর্কে সন্দেহজনক হওয়া উচিত যা কোডের একটি অংশের *উদ্দেশ্য* এর পরিবর্তে *timing* এর উপর ফোকাস করে। এটি প্রথমে "আরও বর্ণনামূলক" মনে হতে পারে কিন্তু এটি আপনার intent অস্পষ্ট করে। একটি নিয়ম হিসাবে, Effect Event *ব্যবহারকারীর* দৃষ্টিকোণ থেকে যা ঘটে তার সাথে সম্পর্কিত হওয়া উচিত। উদাহরণস্বরূপ, `onMessage`, `onTick`, `onVisit`, বা `onConnected` ভালো Effect Event নাম। তাদের ভিতরের কোড সম্ভবত reactive হওয়ার প্রয়োজন হবে না। অন্যদিকে, `onMount`, `onUpdate`, `onUnmount`, বা `onAfterRender` এত generic যে ভুলবশত এমন কোড রাখা সহজ যা reactive *হওয়া উচিত*। এই কারণেই আপনার Effect Event এর নাম *ব্যবহারকারী কি মনে করে ঘটেছে* তার উপর ভিত্তি করে রাখা উচিত, কিছু কোড কখন চালু হয়েছিল তার উপর নয়।

</Solution>

#### একটি delayed notification ঠিক করুন {/*fix-a-delayed-notification*/}

যখন আপনি একটি chat room এ যোগ দেন, এই কম্পোনেন্ট একটি notification দেখায়। তবে, এটি notification অবিলম্বে দেখায় না। পরিবর্তে, notification কৃত্রিমভাবে দুই সেকেন্ড delay করা হয় যাতে ব্যবহারকারী UI এর চারপাশে তাকানোর সুযোগ পায়।

এটি প্রায় কাজ করে, কিন্তু একটি bug আছে। dropdown "general" থেকে "travel" এবং তারপর "music" এ খুব দ্রুত পরিবর্তন করার চেষ্টা করুন। যদি আপনি এটি যথেষ্ট দ্রুত করেন, আপনি দুটি notification দেখবেন (প্রত্যাশিত!) কিন্তু তারা *উভয়ই* বলবে "Welcome to music"।

এটি ঠিক করুন যাতে আপনি যখন "general" থেকে "travel" এবং তারপর "music" এ খুব দ্রুত switch করেন, আপনি দুটি notification দেখেন, প্রথমটি "Welcome to travel" এবং দ্বিতীয়টি "Welcome to music"। (একটি অতিরিক্ত challenge এর জন্য, ধরে নিচ্ছি আপনি *ইতিমধ্যে* notification গুলি সঠিক room দেখাতে তৈরি করেছেন, কোড পরিবর্তন করুন যাতে শুধুমাত্র পরবর্তী notification প্রদর্শিত হয়।)

<Hint>

আপনার Effect জানে এটি কোন room এ সংযুক্ত হয়েছিল। এমন কোনো তথ্য আছে যা আপনি আপনার Effect Event এ pass করতে চাইতে পারেন?

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

```js
import { useState, useEffect } from 'react';
import { useEffectEvent } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Welcome to ' + roomId, theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      setTimeout(() => {
        onConnected();
      }, 2000);
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

```js src/chat.js
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

```js src/notifications.js hidden
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

<Solution>

আপনার Effect Event এর ভিতরে, `roomId` হল সেই মান *যখন Effect Event কল করা হয়েছিল।*

আপনার Effect Event দুই সেকেন্ড delay সহ কল করা হয়। যদি আপনি দ্রুত travel থেকে music room এ switch করছেন, যখন travel room এর notification দেখায়, `roomId` ইতিমধ্যে `"music"`। এই কারণেই উভয় notification "Welcome to music" বলে।

সমস্যা ঠিক করতে, Effect Event এর ভিতরে *সর্বশেষ* `roomId` পড়ার পরিবর্তে, এটিকে আপনার Effect Event এর একটি parameter করুন, যেমন নিচে `connectedRoomId`। তারপর `onConnected(roomId)` কল করে আপনার Effect থেকে `roomId` pass করুন:

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
import { useEffectEvent } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(connectedRoomId => {
    showNotification('Welcome to ' + connectedRoomId, theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      setTimeout(() => {
        onConnected(roomId);
      }, 2000);
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

```js src/chat.js
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

```js src/notifications.js hidden
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

যে Effect এ `roomId` `"travel"` এ set ছিল (তাই এটি `"travel"` room এ সংযুক্ত হয়েছিল) সেটি `"travel"` এর জন্য notification দেখাবে। যে Effect এ `roomId` `"music"` এ set ছিল (তাই এটি `"music"` room এ সংযুক্ত হয়েছিল) সেটি `"music"` এর জন্য notification দেখাবে। অন্য কথায়, `connectedRoomId` আপনার Effect থেকে আসে (যা reactive), যেখানে `theme` সর্বদা সর্বশেষ মান ব্যবহার করে।

অতিরিক্ত challenge সমাধান করতে, notification timeout ID সংরক্ষণ করুন এবং আপনার Effect এর cleanup function এ এটি clear করুন:

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
import { useEffectEvent } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(connectedRoomId => {
    showNotification('Welcome to ' + connectedRoomId, theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    let notificationTimeoutId;
    connection.on('connected', () => {
      notificationTimeoutId = setTimeout(() => {
        onConnected(roomId);
      }, 2000);
    });
    connection.connect();
    return () => {
      connection.disconnect();
      if (notificationTimeoutId !== undefined) {
        clearTimeout(notificationTimeoutId);
      }
    };
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

```js src/chat.js
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

```js src/notifications.js hidden
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

এটি নিশ্চিত করে যে ইতিমধ্যে scheduled (কিন্তু এখনও প্রদর্শিত হয়নি) notification গুলি আপনি room পরিবর্তন করলে cancel হয়ে যায়।

</Solution>

</Challenges>
