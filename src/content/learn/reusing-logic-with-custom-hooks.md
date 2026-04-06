---
title: 'Custom Hook দিয়ে লজিক পুনরায় ব্যবহার করা'
---

<Intro>

React এর সাথে বেশ কিছু built-in Hook আসে যেমন `useState`, `useContext`, এবং `useEffect`। কখনো কখনো, আপনি চাইবেন যে আরো কিছু নির্দিষ্ট উদ্দেশ্যের জন্য একটি Hook থাকুক: উদাহরণস্বরূপ, ডেটা fetch করার জন্য, ইউজার অনলাইন আছে কিনা তা ট্র্যাক করার জন্য, অথবা একটি chat room এ কানেক্ট করার জন্য। আপনি হয়তো React এ এই Hook গুলো খুঁজে পাবেন না, কিন্তু আপনি আপনার অ্যাপ্লিকেশনের প্রয়োজন অনুযায়ী নিজের Hook তৈরি করতে পারেন।

</Intro>

<YouWillLearn>

- Custom Hook কি, এবং কিভাবে আপনার নিজের লিখবেন
- কিভাবে কম্পোনেন্টগুলোর মধ্যে লজিক পুনরায় ব্যবহার করবেন
- কিভাবে আপনার custom Hook এর নাম এবং স্ট্রাকচার করবেন
- কখন এবং কেন custom Hook extract করবেন

</YouWillLearn>

## Custom Hooks: কম্পোনেন্টগুলোর মধ্যে লজিক শেয়ার করা {/*custom-hooks-sharing-logic-between-components*/}

কল্পনা করুন আপনি এমন একটি অ্যাপ ডেভেলপ করছেন যা নেটওয়ার্কের উপর অনেক বেশি নির্ভরশীল (যেমন বেশিরভাগ অ্যাপ করে)। আপনি ইউজারকে সতর্ক করতে চান যদি তাদের নেটওয়ার্ক কানেকশন দুর্ঘটনাক্রমে বন্ধ হয়ে যায় যখন তারা আপনার অ্যাপ ব্যবহার করছিল। আপনি এটা কিভাবে করবেন? মনে হচ্ছে আপনার কম্পোনেন্টে দুটি জিনিস প্রয়োজন হবে:

1. একটি state যা ট্র্যাক করে নেটওয়ার্ক অনলাইন আছে কিনা।
2. একটি Effect যা global [`online`](https://developer.mozilla.org/en-US/docs/Web/API/Window/online_event) এবং [`offline`](https://developer.mozilla.org/en-US/docs/Web/API/Window/offline_event) event এ সাবস্ক্রাইব করে, এবং সেই state আপডেট করে।

এটি আপনার কম্পোনেন্টকে নেটওয়ার্ক স্ট্যাটাসের সাথে [synchronized](/learn/synchronizing-with-effects) রাখবে। আপনি এরকম কিছু দিয়ে শুরু করতে পারেন:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}
```

</Sandpack>

আপনার নেটওয়ার্ক অন এবং অফ করার চেষ্টা করুন, এবং লক্ষ্য করুন কিভাবে এই `StatusBar` আপনার action এর প্রতিক্রিয়ায় আপডেট হয়।

এখন কল্পনা করুন আপনি একটি ভিন্ন কম্পোনেন্টেও একই লজিক ব্যবহার করতে চান। আপনি একটি Save বাটন implement করতে চান যা disabled হয়ে যাবে এবং নেটওয়ার্ক অফ থাকাকালীন "Save" এর পরিবর্তে "Reconnecting..." দেখাবে।

শুরু করতে, আপনি `isOnline` state এবং Effect টি `SaveButton` এ কপি এবং পেস্ট করতে পারেন:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}
```

</Sandpack>

যাচাই করুন যে, যদি আপনি নেটওয়ার্ক বন্ধ করেন, বাটনটি তার appearance পরিবর্তন করবে।

এই দুটি কম্পোনেন্ট ঠিকভাবে কাজ করে, কিন্তু তাদের মধ্যে লজিকের duplication দুর্ভাগ্যজনক। মনে হচ্ছে যদিও তাদের ভিন্ন *visual appearance* আছে, আপনি তাদের মধ্যে লজিক পুনরায় ব্যবহার করতে চান।

### একটি কম্পোনেন্ট থেকে আপনার নিজের custom Hook extract করা {/*extracting-your-own-custom-hook-from-a-component*/}

এক মুহূর্তের জন্য কল্পনা করুন যে, [`useState`](/reference/react/useState) এবং [`useEffect`](/reference/react/useEffect) এর মতো, একটি built-in `useOnlineStatus` Hook ছিল। তাহলে এই দুটি কম্পোনেন্টই সরলীকৃত হতে পারত এবং আপনি তাদের মধ্যে duplication সরাতে পারতেন:

```js {2,7}
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
```

যদিও এরকম কোনো built-in Hook নেই, আপনি নিজেই এটি লিখতে পারেন। `useOnlineStatus` নামে একটি ফাংশন ডিক্লেয়ার করুন এবং আপনার আগে লেখা কম্পোনেন্টগুলো থেকে সমস্ত duplicated কোড এতে সরিয়ে নিন:

```js {2-16}
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

ফাংশনের শেষে, `isOnline` রিটার্ন করুন। এটি আপনার কম্পোনেন্টগুলোকে সেই value পড়তে দেয়:


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
import { useState, useEffect } from 'react';

export function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

</Sandpack>

যাচাই করুন যে নেটওয়ার্ক অন এবং অফ করলে উভয় কম্পোনেন্ট আপডেট হয়।

এখন আপনার কম্পোনেন্টগুলোতে আর তেমন repetitive লজিক নেই। **আরো গুরুত্বপূর্ণভাবে, তাদের ভিতরের কোড বর্ণনা করে *তারা কি করতে চায়* (অনলাইন স্ট্যাটাস ব্যবহার করুন!) বরং *কিভাবে এটি করতে হবে* (ব্রাউজার event এ সাবস্ক্রাইব করে) তার পরিবর্তে।**

যখন আপনি লজিক custom Hook এ extract করেন, আপনি কিছু external system বা browser API এর সাথে কিভাবে ডিল করেন তার জটিল বিস্তারিত লুকিয়ে রাখতে পারেন। আপনার কম্পোনেন্টের কোড আপনার intent প্রকাশ করে, implementation নয়।

### Hook এর নাম সবসময় `use` দিয়ে শুরু হয় {/*hook-names-always-start-with-use*/}

React অ্যাপ্লিকেশনগুলো কম্পোনেন্ট থেকে তৈরি হয়। কম্পোনেন্টগুলো Hook থেকে তৈরি হয়, built-in হোক বা custom। আপনি সম্ভবত প্রায়ই অন্যদের তৈরি custom Hook ব্যবহার করবেন, কিন্তু মাঝে মাঝে আপনি নিজেও একটি লিখতে পারেন!

আপনাকে অবশ্যই এই naming convention গুলো অনুসরণ করতে হবে:

1. **React কম্পোনেন্টের নাম অবশ্যই একটি capital letter দিয়ে শুরু হতে হবে,** যেমন `StatusBar` এবং `SaveButton`। React কম্পোনেন্টগুলোকে এমন কিছু রিটার্ন করতে হবে যা React প্রদর্শন করতে জানে, যেমন JSX এর একটি অংশ।
2. **Hook এর নাম অবশ্যই `use` দিয়ে শুরু হতে হবে এবং তারপর একটি capital letter,** যেমন [`useState`](/reference/react/useState) (built-in) বা `useOnlineStatus` (custom, যেমন পৃষ্ঠার আগে)। Hook যেকোনো arbitrary value রিটার্ন করতে পারে।

এই convention গ্যারান্টি দেয় যে আপনি সবসময় একটি কম্পোনেন্টের দিকে তাকিয়ে জানতে পারবেন কোথায় এর state, Effect, এবং অন্যান্য React ফিচার "লুকিয়ে" থাকতে পারে। উদাহরণস্বরূপ, যদি আপনি আপনার কম্পোনেন্টের ভিতরে একটি `getColor()` ফাংশন কল দেখেন, আপনি নিশ্চিত হতে পারেন যে এটির ভিতরে React state থাকতে পারে না কারণ এর নাম `use` দিয়ে শুরু হয় না। তবে, একটি ফাংশন কল যেমন `useOnlineStatus()` সম্ভবত ভিতরে অন্যান্য Hook এর কল ধারণ করবে!

<Note>

যদি আপনার linter [React এর জন্য configured](/learn/editor-setup#linting) থাকে, এটি এই naming convention enforce করবে। উপরের sandbox এ স্ক্রল করুন এবং `useOnlineStatus` কে `getOnlineStatus` এ rename করুন। লক্ষ্য করুন যে linter আপনাকে আর এর ভিতরে `useState` বা `useEffect` কল করতে দেবে না। শুধুমাত্র Hook এবং কম্পোনেন্ট অন্যান্য Hook কল করতে পারে!

</Note>

<DeepDive>

#### rendering এর সময় কল করা সমস্ত ফাংশন কি use prefix দিয়ে শুরু হওয়া উচিত? {/*should-all-functions-called-during-rendering-start-with-the-use-prefix*/}

না। যে ফাংশনগুলো Hook *কল করে না* তাদের Hook *হতে হবে না*।

যদি আপনার ফাংশন কোনো Hook কল না করে, `use` prefix এড়িয়ে চলুন। পরিবর্তে, এটিকে একটি regular ফাংশন হিসাবে লিখুন `use` prefix *ছাড়া*। উদাহরণস্বরূপ, নিচের `useSorted` Hook কল করে না, তাই এটিকে `getSorted` বলুন:

```js
// 🔴 এড়িয়ে চলুন: একটি Hook যা Hook ব্যবহার করে না
function useSorted(items) {
  return items.slice().sort();
}

// ✅ ভালো: একটি regular ফাংশন যা Hook ব্যবহার করে না
function getSorted(items) {
  return items.slice().sort();
}
```

এটি নিশ্চিত করে যে আপনার কোড এই regular ফাংশনকে যেকোনো জায়গায় কল করতে পারে, condition সহ:

```js
function List({ items, shouldSort }) {
  let displayedItems = items;
  if (shouldSort) {
    // ✅ conditionally getSorted() কল করা ঠিক আছে কারণ এটি একটি Hook নয়
    displayedItems = getSorted(items);
  }
  // ...
}
```

আপনার একটি ফাংশনকে `use` prefix দেওয়া উচিত (এবং এইভাবে এটিকে একটি Hook বানানো উচিত) যদি এটি ভিতরে অন্তত একটি Hook ব্যবহার করে:

```js
// ✅ ভালো: একটি Hook যা অন্যান্য Hook ব্যবহার করে
function useAuth() {
  return useContext(Auth);
}
```

প্রযুক্তিগতভাবে, এটি React দ্বারা enforce করা হয় না। নীতিগতভাবে, আপনি এমন একটি Hook তৈরি করতে পারেন যা অন্যান্য Hook কল করে না। এটি প্রায়ই বিভ্রান্তিকর এবং সীমাবদ্ধ তাই সেই pattern এড়ানো ভালো। তবে, এমন বিরল ক্ষেত্রে থাকতে পারে যেখানে এটি সহায়ক। উদাহরণস্বরূপ, হয়তো আপনার ফাংশন এখন কোনো Hook ব্যবহার করে না, কিন্তু আপনি ভবিষ্যতে এতে কিছু Hook কল যোগ করার পরিকল্পনা করছেন। তাহলে এটিকে `use` prefix দিয়ে নাম দেওয়া অর্থপূর্ণ:

```js {3-4}
// ✅ ভালো: একটি Hook যা সম্ভবত পরে কিছু অন্যান্য Hook ব্যবহার করবে
function useAuth() {
  // TODO: authentication implement হলে এই লাইন দিয়ে প্রতিস্থাপন করুন:
  // return useContext(Auth);
  return TEST_USER;
}
```

তাহলে কম্পোনেন্টগুলো এটিকে conditionally কল করতে পারবে না। এটি গুরুত্বপূর্ণ হয়ে উঠবে যখন আপনি আসলে ভিতরে Hook কল যোগ করবেন। যদি আপনি এর ভিতরে Hook ব্যবহার করার পরিকল্পনা না করেন (এখন বা পরে), এটিকে একটি Hook বানাবেন না।

</DeepDive>

### Custom Hook আপনাকে stateful লজিক শেয়ার করতে দেয়, state নিজেই নয় {/*custom-hooks-let-you-share-stateful-logic-not-state-itself*/}

আগের উদাহরণে, যখন আপনি নেটওয়ার্ক অন এবং অফ করেছিলেন, উভয় কম্পোনেন্ট একসাথে আপডেট হয়েছিল। তবে, এটি ভাবা ভুল যে একটি একক `isOnline` state variable তাদের মধ্যে শেয়ার করা হয়। এই কোডটি দেখুন:

```js {2,7}
function StatusBar() {
  const isOnline = useOnlineStatus();
  // ...
}

function SaveButton() {
  const isOnline = useOnlineStatus();
  // ...
}
```

এটি আগের মতোই কাজ করে যেভাবে আপনি duplication extract করার আগে করেছিলেন:

```js {2-5,10-13}
function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    // ...
  }, []);
  // ...
}

function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    // ...
  }, []);
  // ...
}
```

এগুলো দুটি সম্পূর্ণ স্বাধীন state variable এবং Effect! তারা একই সময়ে একই value পেয়েছিল কারণ আপনি তাদের একই external value (নেটওয়ার্ক অন আছে কিনা) এর সাথে synchronized করেছিলেন।

এটি আরো ভালোভাবে ব্যাখ্যা করতে, আমাদের একটি ভিন্ন উদাহরণ প্রয়োজন। এই `Form` কম্পোনেন্টটি বিবেচনা করুন:

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [firstName, setFirstName] = useState('Mary');
  const [lastName, setLastName] = useState('Poppins');

  function handleFirstNameChange(e) {
    setFirstName(e.target.value);
  }

  function handleLastNameChange(e) {
    setLastName(e.target.value);
  }

  return (
    <>
      <label>
        First name:
        <input value={firstName} onChange={handleFirstNameChange} />
      </label>
      <label>
        Last name:
        <input value={lastName} onChange={handleLastNameChange} />
      </label>
      <p><b>Good morning, {firstName} {lastName}.</b></p>
    </>
  );
}
```

```css
label { display: block; }
input { margin-left: 10px; }
```

</Sandpack>

প্রতিটি form field এর জন্য কিছু repetitive লজিক আছে:

1. একটি state আছে (`firstName` এবং `lastName`)।
1. একটি change handler আছে (`handleFirstNameChange` এবং `handleLastNameChange`)।
1. JSX এর একটি অংশ আছে যা সেই input এর জন্য `value` এবং `onChange` attribute নির্দিষ্ট করে।

আপনি repetitive লজিক এই `useFormInput` custom Hook এ extract করতে পারেন:

<Sandpack>

```js
import { useFormInput } from './useFormInput.js';

export default function Form() {
  const firstNameProps = useFormInput('Mary');
  const lastNameProps = useFormInput('Poppins');

  return (
    <>
      <label>
        First name:
        <input {...firstNameProps} />
      </label>
      <label>
        Last name:
        <input {...lastNameProps} />
      </label>
      <p><b>Good morning, {firstNameProps.value} {lastNameProps.value}.</b></p>
    </>
  );
}
```

```js src/useFormInput.js active
import { useState } from 'react';

export function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  function handleChange(e) {
    setValue(e.target.value);
  }

  const inputProps = {
    value: value,
    onChange: handleChange
  };

  return inputProps;
}
```

```css
label { display: block; }
input { margin-left: 10px; }
```

</Sandpack>

লক্ষ্য করুন এটি শুধুমাত্র `value` নামে *একটি* state variable ডিক্লেয়ার করে।

তবে, `Form` কম্পোনেন্ট `useFormInput` কে *দুইবার* কল করে:

```js
function Form() {
  const firstNameProps = useFormInput('Mary');
  const lastNameProps = useFormInput('Poppins');
  // ...
```

এই কারণেই এটি দুটি আলাদা state variable ডিক্লেয়ার করার মতো কাজ করে!

**Custom Hook আপনাকে *stateful লজিক* শেয়ার করতে দেয় কিন্তু *state নিজেই* নয়। একটি Hook এর প্রতিটি কল একই Hook এর অন্য প্রতিটি কল থেকে সম্পূর্ণ স্বাধীন।** এই কারণেই উপরের দুটি sandbox সম্পূর্ণ সমতুল্য। যদি চান, উপরে স্ক্রল করে তাদের তুলনা করুন। custom Hook extract করার আগে এবং পরে আচরণ অভিন্ন।

যখন আপনার একাধিক কম্পোনেন্টের মধ্যে state নিজেই শেয়ার করার প্রয়োজন হয়, পরিবর্তে [এটি উপরে তুলুন এবং নিচে পাস করুন](/learn/sharing-state-between-components)।


## Hook এর মধ্যে reactive value পাস করা {/*passing-reactive-values-between-hooks*/}

আপনার custom Hook এর ভিতরের কোড আপনার কম্পোনেন্টের প্রতিটি re-render এর সময় পুনরায় চলবে। এই কারণেই, কম্পোনেন্টের মতো, custom Hook [pure হতে হবে।](/learn/keeping-components-pure) custom Hook এর কোডকে আপনার কম্পোনেন্টের body এর অংশ হিসাবে ভাবুন!

যেহেতু custom Hook আপনার কম্পোনেন্টের সাথে একসাথে re-render হয়, তারা সবসময় সর্বশেষ props এবং state পায়। এর অর্থ কি তা দেখতে, এই chat room উদাহরণটি বিবেচনা করুন। server URL বা chat room পরিবর্তন করুন:

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

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
      <ChatRoom
        roomId={roomId}
      />
    </>
  );
}
```

```js src/ChatRoom.js active
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';
import { showNotification } from './notifications.js';

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]);

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
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
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
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
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl + '');
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

export function showNotification(message, theme = 'dark') {
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

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

যখন আপনি `serverUrl` বা `roomId` পরিবর্তন করেন, Effect আপনার পরিবর্তনের প্রতি ["react" করে](/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values) এবং re-synchronize হয়। আপনি console message দ্বারা বলতে পারেন যে chat প্রতিবার re-connect হয় যখন আপনি আপনার Effect এর dependency পরিবর্তন করেন।

এখন Effect এর কোড একটি custom Hook এ সরান:

```js {2-13}
export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

এটি আপনার `ChatRoom` কম্পোনেন্টকে আপনার custom Hook কল করতে দেয় এটি ভিতরে কিভাবে কাজ করে তা নিয়ে চিন্তা না করে:

```js {4-7}
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

এটি অনেক সহজ দেখাচ্ছে! (কিন্তু এটি একই কাজ করে।)

লক্ষ্য করুন যে লজিক *এখনও prop এবং state পরিবর্তনের প্রতি সাড়া দেয়*। server URL বা selected room edit করার চেষ্টা করুন:

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

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
      <ChatRoom
        roomId={roomId}
      />
    </>
  );
}
```

```js src/ChatRoom.js active
import { useState } from 'react';
import { useChatRoom } from './useChatRoom.js';

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

```js src/useChatRoom.js
import { useEffect } from 'react';
import { createConnection } from './chat.js';
import { showNotification } from './notifications.js';

export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
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
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
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
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl + '');
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

export function showNotification(message, theme = 'dark') {
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

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

লক্ষ্য করুন কিভাবে আপনি একটি Hook এর return value নিচ্ছেন:

```js {2}
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });
  // ...
```

এবং এটি অন্য একটি Hook এ input হিসাবে পাস করছেন:

```js {6}
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });
  // ...
```

প্রতিবার আপনার `ChatRoom` কম্পোনেন্ট re-render হয়, এটি সর্বশেষ `roomId` এবং `serverUrl` আপনার Hook এ পাস করে। এই কারণেই আপনার Effect chat এ re-connect হয় যখনই তাদের value একটি re-render এর পরে ভিন্ন হয়। (যদি আপনি কখনো audio বা video processing software এর সাথে কাজ করে থাকেন, এভাবে Hook chain করা আপনাকে visual বা audio effect chain করার কথা মনে করিয়ে দিতে পারে। এটি যেন `useState` এর output `useChatRoom` এর input এ "feeds into" করে।)

### custom Hook এ event handler পাস করা {/*passing-event-handlers-to-custom-hooks*/}

<Wip>

এই অনুচ্ছেদটি একটি **experimental API বর্ণনা করে যা এখনো React এর stable version এ release** হয়নি।

</Wip>

যখন আপনি আরো কম্পোনেন্টে `useChatRoom` ব্যবহার করা শুরু করবেন, আপনি হয়তো কম্পোনেন্টগুলোকে এর আচরণ customize করতে দিতে চাইবেন। উদাহরণস্বরূপ, বর্তমানে, একটি message আসলে কি করতে হবে তার লজিক Hook এর ভিতরে hardcoded আছে:

```js {9-11}
export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

ধরা যাক আপনি এই লজিক আপনার কম্পোনেন্টে ফিরিয়ে নিতে চান:

```js {7-9}
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl,
    onReceiveMessage(msg) {
      showNotification('New message: ' + msg);
    }
  });
  // ...
```

এটি কাজ করানোর জন্য, আপনার custom Hook কে `onReceiveMessage` কে এর named option গুলোর একটি হিসাবে নিতে পরিবর্তন করুন:

```js {1,10,13}
export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onReceiveMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl, onReceiveMessage]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
}
```

এটি কাজ করবে, কিন্তু আরো একটি উন্নতি আছে যা আপনি করতে পারেন যখন আপনার custom Hook event handler গ্রহণ করে।

`onReceiveMessage` এ একটি dependency যোগ করা আদর্শ নয় কারণ এটি প্রতিবার কম্পোনেন্ট re-render হলে chat কে re-connect করবে। [এই event handler কে একটি Effect Event এ wrap করুন এটি dependency থেকে সরাতে:](/learn/removing-effect-dependencies#wrapping-an-event-handler-from-the-props)

```js {1,4,5,15,18}
import { useEffect, useEffectEvent } from 'react';
// ...

export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  const onMessage = useEffectEvent(onReceiveMessage);

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]); // ✅ সমস্ত dependency ডিক্লেয়ার করা হয়েছে
}
```

এখন `ChatRoom` কম্পোনেন্ট প্রতিবার re-render হলে chat re-connect হবে না। এখানে একটি custom Hook এ event handler পাস করার একটি সম্পূর্ণ কার্যকর demo আছে যা আপনি নিয়ে খেলতে পারেন:


<Sandpack>

```js src/App.js
import { useState } from 'react';
import ChatRoom from './ChatRoom.js';

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
      <ChatRoom
        roomId={roomId}
      />
    </>
  );
}
```

```js src/ChatRoom.js active
import { useState } from 'react';
import { useChatRoom } from './useChatRoom.js';
import { showNotification } from './notifications.js';

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl,
    onReceiveMessage(msg) {
      showNotification('New message: ' + msg);
    }
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

```js src/useChatRoom.js
import { useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { createConnection } from './chat.js';

export function useChatRoom({ serverUrl, roomId, onReceiveMessage }) {
  const onMessage = useEffectEvent(onReceiveMessage);

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      onMessage(msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
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
  let intervalId;
  let messageCallback;
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
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
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl + '');
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

export function showNotification(message, theme = 'dark') {
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

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

লক্ষ্য করুন কিভাবে আপনার আর জানার প্রয়োজন নেই *কিভাবে* `useChatRoom` কাজ করে এটি ব্যবহার করার জন্য। আপনি এটি অন্য যেকোনো কম্পোনেন্টে যোগ করতে পারেন, অন্য যেকোনো option পাস করতে পারেন, এবং এটি একইভাবে কাজ করবে। এটাই custom Hook এর শক্তি।

## কখন custom Hook ব্যবহার করবেন {/*when-to-use-custom-hooks*/}

আপনার প্রতিটি ছোট duplicated কোডের জন্য একটি custom Hook extract করার প্রয়োজন নেই। কিছু duplication ঠিক আছে। উদাহরণস্বরূপ, আগের মতো একটি একক `useState` কল wrap করার জন্য একটি `useFormInput` Hook extract করা সম্ভবত অপ্রয়োজনীয়।

তবে, যখনই আপনি একটি Effect লিখেন, বিবেচনা করুন এটি একটি custom Hook এ wrap করাও কি আরো পরিষ্কার হবে। [আপনার খুব বেশি Effect এর প্রয়োজন হওয়া উচিত নয়,](/learn/you-might-not-need-an-effect) তাই যদি আপনি একটি লিখছেন, এর মানে হল আপনার "React এর বাইরে পা রাখতে" হবে কোনো external system এর সাথে synchronize করতে বা এমন কিছু করতে যার জন্য React এর কোনো built-in API নেই। এটি একটি custom Hook এ wrap করা আপনাকে সুনির্দিষ্টভাবে আপনার intent এবং কিভাবে ডেটা এর মধ্য দিয়ে প্রবাহিত হয় তা communicate করতে দেয়।

উদাহরণস্বরূপ, একটি `ShippingForm` কম্পোনেন্ট বিবেচনা করুন যা দুটি dropdown প্রদর্শন করে: একটি city এর তালিকা দেখায়, এবং অন্যটি selected city এর area এর তালিকা দেখায়। আপনি এরকম কিছু কোড দিয়ে শুরু করতে পারেন:

```js {3-16,20-35}
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  // এই Effect একটি country এর জন্য city fetch করে
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
  }, [country]);

  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);
  // এই Effect selected city এর জন্য area fetch করে
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
  }, [city]);

  // ...
```

যদিও এই কোড বেশ repetitive, [এই Effect গুলো একে অপর থেকে আলাদা রাখা সঠিক।](/learn/removing-effect-dependencies#is-your-effect-doing-several-unrelated-things) তারা দুটি ভিন্ন জিনিস synchronize করে, তাই আপনার তাদের একটি Effect এ merge করা উচিত নয়। পরিবর্তে, আপনি উপরের `ShippingForm` কম্পোনেন্ট সরলীকৃত করতে পারেন তাদের মধ্যে common লজিক আপনার নিজের `useData` Hook এ extract করে:

```js {2-18}
function useData(url) {
  const [data, setData] = useState(null);
  useEffect(() => {
    if (url) {
      let ignore = false;
      fetch(url)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setData(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [url]);
  return data;
}
```

এখন আপনি `ShippingForm` কম্পোনেন্টে উভয় Effect কে `useData` এর কল দিয়ে প্রতিস্থাপন করতে পারেন:

```js {2,4}
function ShippingForm({ country }) {
  const cities = useData(`/api/cities?country=${country}`);
  const [city, setCity] = useState(null);
  const areas = useData(city ? `/api/areas?city=${city}` : null);
  // ...
```

একটি custom Hook extract করা data flow কে explicit করে তোলে। আপনি `url` feed করেন এবং আপনি `data` পান। আপনার Effect কে `useData` এর ভিতরে "লুকিয়ে" রেখে, আপনি `ShippingForm` কম্পোনেন্টে কাজ করা কাউকে এতে [অপ্রয়োজনীয় dependency](/learn/removing-effect-dependencies) যোগ করা থেকেও বিরত রাখেন। সময়ের সাথে সাথে, আপনার অ্যাপের বেশিরভাগ Effect custom Hook এ থাকবে।

<DeepDive>

#### আপনার custom Hook কে concrete high-level use case এ focused রাখুন {/*keep-your-custom-hooks-focused-on-concrete-high-level-use-cases*/}

আপনার custom Hook এর নাম বেছে নেওয়া দিয়ে শুরু করুন। যদি আপনি একটি পরিষ্কার নাম বাছাই করতে সংগ্রাম করেন, এর মানে হতে পারে যে আপনার Effect আপনার কম্পোনেন্টের বাকি লজিকের সাথে খুব বেশি coupled, এবং এখনো extract করার জন্য প্রস্তুত নয়।

আদর্শভাবে, আপনার custom Hook এর নাম যথেষ্ট পরিষ্কার হওয়া উচিত যাতে এমন একজন ব্যক্তি যিনি প্রায়ই কোড লিখেন না তিনিও আপনার custom Hook কি করে, কি নেয়, এবং কি রিটার্ন করে সে সম্পর্কে একটি ভালো অনুমান করতে পারেন:

* ✅ `useData(url)`
* ✅ `useImpressionLog(eventName, extraData)`
* ✅ `useChatRoom(options)`

যখন আপনি একটি external system এর সাথে synchronize করেন, আপনার custom Hook এর নাম আরো technical হতে পারে এবং সেই system এর নির্দিষ্ট jargon ব্যবহার করতে পারে। এটি ভালো যতক্ষণ এটি সেই system এর সাথে পরিচিত একজন ব্যক্তির কাছে পরিষ্কার হবে:

* ✅ `useMediaQuery(query)`
* ✅ `useSocket(url)`
* ✅ `useIntersectionObserver(ref, options)`

**custom Hook কে concrete high-level use case এ focused রাখুন।** custom "lifecycle" Hook তৈরি এবং ব্যবহার করা এড়িয়ে চলুন যা `useEffect` API নিজেই এর বিকল্প এবং convenience wrapper হিসাবে কাজ করে:

* 🔴 `useMount(fn)`
* 🔴 `useEffectOnce(fn)`
* 🔴 `useUpdateEffect(fn)`

উদাহরণস্বরূপ, এই `useMount` Hook নিশ্চিত করার চেষ্টা করে যে কিছু কোড শুধুমাত্র "on mount" এ চলে:

```js {4-5,14-15}
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  // 🔴 এড়িয়ে চলুন: custom "lifecycle" Hook ব্যবহার করা
  useMount(() => {
    const connection = createConnection({ roomId, serverUrl });
    connection.connect();

    post('/analytics/event', { eventName: 'visit_chat' });
  });
  // ...
}

// 🔴 এড়িয়ে চলুন: custom "lifecycle" Hook তৈরি করা
function useMount(fn) {
  useEffect(() => {
    fn();
  }, []); // 🔴 React Hook useEffect has a missing dependency: 'fn'
}
```

**custom "lifecycle" Hook যেমন `useMount` React paradigm এ ভালোভাবে fit করে না।** উদাহরণস্বরূপ, এই কোড উদাহরণে একটি ভুল আছে (এটি `roomId` বা `serverUrl` পরিবর্তনের প্রতি "react" করে না), কিন্তু linter আপনাকে এ সম্পর্কে সতর্ক করবে না কারণ linter শুধুমাত্র সরাসরি `useEffect` কল চেক করে। এটি আপনার Hook সম্পর্কে জানবে না।

যদি আপনি একটি Effect লিখছেন, React API সরাসরি ব্যবহার করে শুরু করুন:

```js
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  // ✅ ভালো: উদ্দেশ্য অনুযায়ী আলাদা করা দুটি raw Effect

  useEffect(() => {
    const connection = createConnection({ serverUrl, roomId });
    connection.connect();
    return () => connection.disconnect();
  }, [serverUrl, roomId]);

  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_chat', roomId });
  }, [roomId]);

  // ...
}
```

তারপর, আপনি (কিন্তু করতে হবে না) বিভিন্ন high-level use case এর জন্য custom Hook extract করতে পারেন:

```js
function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  // ✅ দুর্দান্ত: তাদের উদ্দেশ্য অনুযায়ী নামকরণ করা custom Hook
  useChatRoom({ serverUrl, roomId });
  useImpressionLog('visit_chat', { roomId });
  // ...
}
```

**একটি ভালো custom Hook calling কোডকে আরো declarative করে তোলে এটি কি করে তা সীমাবদ্ধ করে।** উদাহরণস্বরূপ, `useChatRoom(options)` শুধুমাত্র chat room এ connect করতে পারে, যখন `useImpressionLog(eventName, extraData)` শুধুমাত্র analytics এ একটি impression log পাঠাতে পারে। যদি আপনার custom Hook API use case সীমাবদ্ধ না করে এবং খুব abstract হয়, দীর্ঘমেয়াদে এটি সম্ভবত যত সমস্যা সমাধান করে তার চেয়ে বেশি সমস্যা তৈরি করবে।

</DeepDive>

### Custom Hook আপনাকে better pattern এ migrate করতে সাহায্য করে {/*custom-hooks-help-you-migrate-to-better-patterns*/}

Effect একটি ["escape hatch"](/learn/escape-hatches): আপনি তাদের ব্যবহার করেন যখন আপনার "React এর বাইরে পা রাখতে" হয় এবং যখন আপনার use case এর জন্য কোনো better built-in solution নেই। সময়ের সাথে সাথে, React টিমের লক্ষ্য হল আপনার অ্যাপে Effect এর সংখ্যা minimum এ কমিয়ে আনা আরো নির্দিষ্ট সমস্যার জন্য আরো নির্দিষ্ট solution প্রদান করে। আপনার Effect গুলো custom Hook এ wrap করা আপনার কোড upgrade করা সহজ করে তোলে যখন এই solution উপলব্ধ হয়।

এই উদাহরণে ফিরে যাই:


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

```js src/useOnlineStatus.js active
import { useState, useEffect } from 'react';

export function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

</Sandpack>

উপরের উদাহরণে, `useOnlineStatus` একজোড়া [`useState`](/reference/react/useState) এবং [`useEffect`](/reference/react/useEffect) দিয়ে implement করা হয়েছে। তবে, এটি সর্বোত্তম সম্ভাব্য solution নয়। এটি বেশ কিছু edge case বিবেচনা করে না। উদাহরণস্বরূপ, এটি ধরে নেয় যে যখন কম্পোনেন্ট mount হয়, `isOnline` ইতিমধ্যে `true`, কিন্তু এটি ভুল হতে পারে যদি নেটওয়ার্ক ইতিমধ্যে offline চলে গিয়ে থাকে। আপনি এটি চেক করতে browser [`navigator.onLine`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/onLine) API ব্যবহার করতে পারেন, কিন্তু এটি সরাসরি ব্যবহার করা server এ initial HTML generate করার জন্য কাজ করবে না। সংক্ষেপে, এই কোড উন্নত করা যেতে পারে।

React এ একটি dedicated API আছে যার নাম [`useSyncExternalStore`](/reference/react/useSyncExternalStore) যা আপনার জন্য এই সমস্ত সমস্যার যত্ন নেয়। এখানে আপনার `useOnlineStatus` Hook, এই নতুন API এর সুবিধা নিতে পুনরায় লেখা হয়েছে:

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

```js src/useOnlineStatus.js active
import { useSyncExternalStore } from 'react';

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

export function useOnlineStatus() {
  return useSyncExternalStore(
    subscribe,
    () => navigator.onLine, // How to get the value on the client
    () => true // How to get the value on the server
  );
}

```

</Sandpack>

লক্ষ্য করুন কিভাবে **আপনার এই migration করতে কোনো কম্পোনেন্ট পরিবর্তন করার প্রয়োজন হয়নি:**

```js {2,7}
function StatusBar() {
  const isOnline = useOnlineStatus();
  // ...
}

function SaveButton() {
  const isOnline = useOnlineStatus();
  // ...
}
```

এটি আরেকটি কারণ কেন Effect গুলো custom Hook এ wrap করা প্রায়ই উপকারী:

1. আপনি আপনার Effect এ এবং থেকে data flow খুব explicit করে তোলেন।
2. আপনি আপনার কম্পোনেন্টগুলোকে আপনার Effect এর সঠিক implementation এর পরিবর্তে intent এ focus করতে দেন।
3. যখন React নতুন ফিচার যোগ করে, আপনি আপনার কোনো কম্পোনেন্ট পরিবর্তন না করেই সেই Effect গুলো সরাতে পারেন।

একটি [design system](https://uxdesign.cc/everything-you-need-to-know-about-design-systems-54b109851969) এর মতো, আপনি আপনার অ্যাপের কম্পোনেন্ট থেকে common idiom custom Hook এ extract করা শুরু করা সহায়ক মনে করতে পারেন। এটি আপনার কম্পোনেন্টের কোড intent এ focused রাখবে, এবং আপনাকে খুব বেশি raw Effect লেখা এড়াতে দেবে। React community দ্বারা অনেক চমৎকার custom Hook রক্ষণাবেক্ষণ করা হয়।

<DeepDive>

#### React কি data fetching এর জন্য কোনো built-in solution প্রদান করবে? {/*will-react-provide-any-built-in-solution-for-data-fetching*/}

আজকে, [`use`](/reference/react/use#streaming-data-from-server-to-client) API এর সাথে, render এ data পড়া যায় একটি [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) `use` তে পাস করে:

```js {1,4,11}
import { use, Suspense } from "react";

function Message({ messagePromise }) {
  const messageContent = use(messagePromise);
  return <p>Here is the message: {messageContent}</p>;
}

export function MessageContainer({ messagePromise }) {
  return (
    <Suspense fallback={<p>⌛Downloading message...</p>}>
      <Message messagePromise={messagePromise} />
    </Suspense>
  );
}
```

আমরা এখনো বিস্তারিত কাজ করছি, কিন্তু আমরা আশা করি যে ভবিষ্যতে, আপনি এভাবে data fetching লিখবেন:

```js {1,4,6}
import { use } from 'react';

function ShippingForm({ country }) {
  const cities = use(fetch(`/api/cities?country=${country}`));
  const [city, setCity] = useState(null);
  const areas = city ? use(fetch(`/api/areas?city=${city}`)) : null;
  // ...
```

যদি আপনি আপনার অ্যাপে উপরের মতো `useData` এর মতো custom Hook ব্যবহার করেন, প্রতিটি কম্পোনেন্টে manually raw Effect লেখার চেয়ে eventually recommended approach এ migrate করতে কম পরিবর্তন প্রয়োজন হবে। তবে, পুরানো approach এখনো ঠিকভাবে কাজ করবে, তাই যদি আপনি raw Effect লিখতে খুশি থাকেন, আপনি তা চালিয়ে যেতে পারেন।

</DeepDive>

### এটি করার একাধিক উপায় আছে {/*there-is-more-than-one-way-to-do-it*/}

ধরা যাক আপনি browser [`requestAnimationFrame`](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) API ব্যবহার করে *scratch থেকে* একটি fade-in animation implement করতে চান। আপনি একটি Effect দিয়ে শুরু করতে পারেন যা একটি animation loop সেটআপ করে। animation এর প্রতিটি frame এর সময়, আপনি DOM node এর opacity পরিবর্তন করতে পারেন যা আপনি [একটি ref এ ধরে রাখেন](/learn/manipulating-the-dom-with-refs) যতক্ষণ না এটি `1` এ পৌঁছায়। আপনার কোড এরকম শুরু হতে পারে:

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';

function Welcome() {
  const ref = useRef(null);

  useEffect(() => {
    const duration = 1000;
    const node = ref.current;

    let startTime = performance.now();
    let frameId = null;

    function onFrame(now) {
      const timePassed = now - startTime;
      const progress = Math.min(timePassed / duration, 1);
      onProgress(progress);
      if (progress < 1) {
        // We still have more frames to paint
        frameId = requestAnimationFrame(onFrame);
      }
    }

    function onProgress(progress) {
      node.style.opacity = progress;
    }

    function start() {
      onProgress(0);
      startTime = performance.now();
      frameId = requestAnimationFrame(onFrame);
    }

    function stop() {
      cancelAnimationFrame(frameId);
      startTime = null;
      frameId = null;
    }

    start();
    return () => stop();
  }, []);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
.welcome {
  opacity: 0;
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);
}
```

</Sandpack>

কম্পোনেন্টকে আরো readable করতে, আপনি লজিক একটি `useFadeIn` custom Hook এ extract করতে পারেন:

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import { useFadeIn } from './useFadeIn.js';

function Welcome() {
  const ref = useRef(null);

  useFadeIn(ref, 1000);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```js src/useFadeIn.js
import { useEffect } from 'react';

export function useFadeIn(ref, duration) {
  useEffect(() => {
    const node = ref.current;

    let startTime = performance.now();
    let frameId = null;

    function onFrame(now) {
      const timePassed = now - startTime;
      const progress = Math.min(timePassed / duration, 1);
      onProgress(progress);
      if (progress < 1) {
        // We still have more frames to paint
        frameId = requestAnimationFrame(onFrame);
      }
    }

    function onProgress(progress) {
      node.style.opacity = progress;
    }

    function start() {
      onProgress(0);
      startTime = performance.now();
      frameId = requestAnimationFrame(onFrame);
    }

    function stop() {
      cancelAnimationFrame(frameId);
      startTime = null;
      frameId = null;
    }

    start();
    return () => stop();
  }, [ref, duration]);
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
.welcome {
  opacity: 0;
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);
}
```

</Sandpack>

আপনি `useFadeIn` কোড যেমন আছে তেমন রাখতে পারেন, কিন্তু আপনি এটি আরো refactor করতে পারেন। উদাহরণস্বরূপ, আপনি animation loop সেটআপ করার লজিক `useFadeIn` থেকে একটি custom `useAnimationLoop` Hook এ extract করতে পারেন:


<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import { useFadeIn } from './useFadeIn.js';

function Welcome() {
  const ref = useRef(null);

  useFadeIn(ref, 1000);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```js src/useFadeIn.js active
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export function useFadeIn(ref, duration) {
  const [isRunning, setIsRunning] = useState(true);

  useAnimationLoop(isRunning, (timePassed) => {
    const progress = Math.min(timePassed / duration, 1);
    ref.current.style.opacity = progress;
    if (progress === 1) {
      setIsRunning(false);
    }
  });
}

function useAnimationLoop(isRunning, drawFrame) {
  const onFrame = useEffectEvent(drawFrame);

  useEffect(() => {
    if (!isRunning) {
      return;
    }

    const startTime = performance.now();
    let frameId = null;

    function tick(now) {
      const timePassed = now - startTime;
      onFrame(timePassed);
      frameId = requestAnimationFrame(tick);
    }

    tick();
    return () => cancelAnimationFrame(frameId);
  }, [isRunning]);
}
```

```css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
.welcome {
  opacity: 0;
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);
}
```

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

</Sandpack>

তবে, আপনার এটি করতে *হবে না*। regular ফাংশনের মতো, শেষ পর্যন্ত আপনি সিদ্ধান্ত নেন আপনার কোডের বিভিন্ন অংশের মধ্যে কোথায় সীমানা টানবেন। আপনি একটি খুব ভিন্ন approach ও নিতে পারেন। Effect এ লজিক রাখার পরিবর্তে, আপনি বেশিরভাগ imperative লজিক একটি JavaScript [class](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) এর ভিতরে সরাতে পারেন:

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import { useFadeIn } from './useFadeIn.js';

function Welcome() {
  const ref = useRef(null);

  useFadeIn(ref, 1000);

  return (
    <h1 className="welcome" ref={ref}>
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```js src/useFadeIn.js active
import { useState, useEffect } from 'react';
import { FadeInAnimation } from './animation.js';

export function useFadeIn(ref, duration) {
  useEffect(() => {
    const animation = new FadeInAnimation(ref.current);
    animation.start(duration);
    return () => {
      animation.stop();
    };
  }, [ref, duration]);
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
    if (progress === 1) {
      this.stop();
    } else {
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
.welcome {
  opacity: 0;
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);
}
```

</Sandpack>

Effect আপনাকে React কে external system এর সাথে connect করতে দেয়। Effect এর মধ্যে যত বেশি coordination প্রয়োজন (উদাহরণস্বরূপ, একাধিক animation chain করতে), উপরের sandbox এর মতো সেই লজিক Effect এবং Hook থেকে *সম্পূর্ণভাবে* extract করা তত বেশি অর্থপূর্ণ। তারপর, আপনার extract করা কোড *হয়ে যায়* "external system"। এটি আপনার Effect গুলোকে সহজ থাকতে দেয় কারণ তাদের শুধুমাত্র আপনার React এর বাইরে সরানো system এ message পাঠাতে হবে।

উপরের উদাহরণগুলো ধরে নেয় যে fade-in লজিক JavaScript এ লিখতে হবে। তবে, এই নির্দিষ্ট fade-in animation একটি plain [CSS Animation](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations/Using_CSS_animations) দিয়ে implement করা অনেক সহজ এবং অনেক বেশি efficient:

<Sandpack>

```js
import { useState, useEffect, useRef } from 'react';
import './welcome.css';

function Welcome() {
  return (
    <h1 className="welcome">
      Welcome
    </h1>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Remove' : 'Show'}
      </button>
      <hr />
      {show && <Welcome />}
    </>
  );
}
```

```css src/styles.css
label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }
```

```css src/welcome.css active
.welcome {
  color: white;
  padding: 50px;
  text-align: center;
  font-size: 50px;
  background-image: radial-gradient(circle, rgba(63,94,251,1) 0%, rgba(252,70,107,1) 100%);

  animation: fadeIn 1000ms;
}

@keyframes fadeIn {
  0% { opacity: 0; }
  100% { opacity: 1; }
}

```

</Sandpack>

কখনো কখনো, আপনার একটি Hook এরও প্রয়োজন নেই!

<Recap>

- Custom Hook আপনাকে কম্পোনেন্টগুলোর মধ্যে লজিক শেয়ার করতে দেয়।
- Custom Hook এর নাম অবশ্যই `use` দিয়ে শুরু হতে হবে এবং তারপর একটি capital letter।
- Custom Hook শুধুমাত্র stateful লজিক শেয়ার করে, state নিজেই নয়।
- আপনি একটি Hook থেকে অন্য Hook এ reactive value পাস করতে পারেন, এবং তারা up-to-date থাকে।
- সমস্ত Hook প্রতিবার আপনার কম্পোনেন্ট re-render হলে পুনরায় চলে।
- আপনার custom Hook এর কোড আপনার কম্পোনেন্টের কোডের মতো pure হওয়া উচিত।
- custom Hook দ্বারা প্রাপ্ত event handler গুলো Effect Event এ wrap করুন।
- `useMount` এর মতো custom Hook তৈরি করবেন না। তাদের উদ্দেশ্য নির্দিষ্ট রাখুন।
- আপনার কোডের সীমানা কিভাবে এবং কোথায় বেছে নেবেন তা আপনার উপর নির্ভর করে।

</Recap>

<Challenges>

#### একটি `useCounter` Hook extract করুন {/*extract-a-usecounter-hook*/}

এই কম্পোনেন্ট একটি state variable এবং একটি Effect ব্যবহার করে একটি সংখ্যা প্রদর্শন করে যা প্রতি সেকেন্ডে বৃদ্ধি পায়। এই লজিক একটি custom Hook এ extract করুন যার নাম `useCounter`। আপনার লক্ষ্য হল `Counter` কম্পোনেন্ট implementation ঠিক এরকম দেখতে করা:

```js
export default function Counter() {
  const count = useCounter();
  return <h1>Seconds passed: {count}</h1>;
}
```

আপনাকে আপনার custom Hook `useCounter.js` এ লিখতে হবে এবং এটি `App.js` ফাইলে import করতে হবে।

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return <h1>Seconds passed: {count}</h1>;
}
```

```js src/useCounter.js
// এখানে আপনার custom Hook লিখুন!
```

</Sandpack>

<Solution>

আপনার কোড এরকম দেখতে হওয়া উচিত:

<Sandpack>

```js
import { useCounter } from './useCounter.js';

export default function Counter() {
  const count = useCounter();
  return <h1>Seconds passed: {count}</h1>;
}
```

```js src/useCounter.js
import { useState, useEffect } from 'react';

export function useCounter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return count;
}
```

</Sandpack>

লক্ষ্য করুন যে `App.js` এ আর `useState` বা `useEffect` import করার প্রয়োজন নেই।

</Solution>

#### counter delay configurable করুন {/*make-the-counter-delay-configurable*/}

এই উদাহরণে, একটি `delay` state variable আছে যা একটি slider দ্বারা নিয়ন্ত্রিত, কিন্তু এর value ব্যবহার করা হয় না। `delay` value আপনার custom `useCounter` Hook এ পাস করুন, এবং `useCounter` Hook পরিবর্তন করুন hardcoded `1000` ms এর পরিবর্তে passed `delay` ব্যবহার করতে।

<Sandpack>

```js
import { useState } from 'react';
import { useCounter } from './useCounter.js';

export default function Counter() {
  const [delay, setDelay] = useState(1000);
  const count = useCounter();
  return (
    <>
      <label>
        Tick duration: {delay} ms
        <br />
        <input
          type="range"
          value={delay}
          min="10"
          max="2000"
          onChange={e => setDelay(Number(e.target.value))}
        />
      </label>
      <hr />
      <h1>Ticks: {count}</h1>
    </>
  );
}
```

```js src/useCounter.js
import { useState, useEffect } from 'react';

export function useCounter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return count;
}
```

</Sandpack>

<Solution>

`useCounter(delay)` দিয়ে আপনার Hook এ `delay` পাস করুন। তারপর, Hook এর ভিতরে, hardcoded `1000` value এর পরিবর্তে `delay` ব্যবহার করুন। আপনাকে আপনার Effect এর dependency তে `delay` যোগ করতে হবে। এটি নিশ্চিত করে যে `delay` এর একটি পরিবর্তন interval reset করবে।

<Sandpack>

```js
import { useState } from 'react';
import { useCounter } from './useCounter.js';

export default function Counter() {
  const [delay, setDelay] = useState(1000);
  const count = useCounter(delay);
  return (
    <>
      <label>
        Tick duration: {delay} ms
        <br />
        <input
          type="range"
          value={delay}
          min="10"
          max="2000"
          onChange={e => setDelay(Number(e.target.value))}
        />
      </label>
      <hr />
      <h1>Ticks: {count}</h1>
    </>
  );
}
```

```js src/useCounter.js
import { useState, useEffect } from 'react';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, delay);
    return () => clearInterval(id);
  }, [delay]);
  return count;
}
```

</Sandpack>

</Solution>


#### `useCounter` থেকে `useInterval` extract করুন {/*extract-useinterval-out-of-usecounter*/}

বর্তমানে, আপনার `useCounter` Hook দুটি কাজ করে। এটি একটি interval সেটআপ করে, এবং এটি প্রতিটি interval tick এ একটি state variable বৃদ্ধি করে। interval সেটআপ করার লজিক একটি আলাদা Hook এ আলাদা করুন যার নাম `useInterval`। এটি দুটি argument নেওয়া উচিত: `onTick` callback, এবং `delay`। এই পরিবর্তনের পরে, আপনার `useCounter` implementation এরকম দেখতে হওয়া উচিত:

```js
export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}
```

`useInterval.js` ফাইলে `useInterval` লিখুন এবং এটি `useCounter.js` ফাইলে import করুন।

<Sandpack>

```js
import { useCounter } from './useCounter.js';

export default function Counter() {
  const count = useCounter(1000);
  return <h1>Seconds passed: {count}</h1>;
}
```

```js src/useCounter.js
import { useState, useEffect } from 'react';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, delay);
    return () => clearInterval(id);
  }, [delay]);
  return count;
}
```

```js src/useInterval.js
// এখানে আপনার Hook লিখুন!
```

</Sandpack>

<Solution>

`useInterval` এর ভিতরের লজিক interval সেটআপ এবং clear করা উচিত। এটি অন্য কিছু করার প্রয়োজন নেই।

<Sandpack>

```js
import { useCounter } from './useCounter.js';

export default function Counter() {
  const count = useCounter(1000);
  return <h1>Seconds passed: {count}</h1>;
}
```

```js src/useCounter.js
import { useState } from 'react';
import { useInterval } from './useInterval.js';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}
```

```js src/useInterval.js active
import { useEffect } from 'react';

export function useInterval(onTick, delay) {
  useEffect(() => {
    const id = setInterval(onTick, delay);
    return () => clearInterval(id);
  }, [onTick, delay]);
}
```

</Sandpack>

লক্ষ্য করুন যে এই solution এর সাথে একটু সমস্যা আছে, যা আপনি পরবর্তী challenge এ সমাধান করবেন।

</Solution>

#### একটি resetting interval ঠিক করুন {/*fix-a-resetting-interval*/}

এই উদাহরণে, *দুটি* আলাদা interval আছে।

`App` কম্পোনেন্ট `useCounter` কল করে, যা প্রতি সেকেন্ডে counter আপডেট করতে `useInterval` কল করে। কিন্তু `App` কম্পোনেন্ট *এছাড়াও* প্রতি দুই সেকেন্ডে randomly পৃষ্ঠার background color আপডেট করতে `useInterval` কল করে।

কোনো কারণে, callback যা পৃষ্ঠার background আপডেট করে তা কখনো চলে না। `useInterval` এর ভিতরে কিছু log যোগ করুন:

```js {2,5}
  useEffect(() => {
    console.log('✅ Setting up an interval with delay ', delay)
    const id = setInterval(onTick, delay);
    return () => {
      console.log('❌ Clearing an interval with delay ', delay)
      clearInterval(id);
    };
  }, [onTick, delay]);
```

log গুলো কি আপনার প্রত্যাশা অনুযায়ী মিলছে? যদি আপনার কিছু Effect অপ্রয়োজনীয়ভাবে re-synchronize হচ্ছে বলে মনে হয়, আপনি কি অনুমান করতে পারেন কোন dependency এটি ঘটাচ্ছে? আপনার Effect থেকে সেই dependency [সরানোর](/learn/removing-effect-dependencies) কোনো উপায় আছে কি?

সমস্যা ঠিক করার পরে, আপনার প্রত্যাশা করা উচিত যে পৃষ্ঠার background প্রতি দুই সেকেন্ডে আপডেট হবে।

<Hint>

মনে হচ্ছে আপনার `useInterval` Hook একটি event listener argument হিসাবে গ্রহণ করে। আপনি কি সেই event listener wrap করার কোনো উপায় ভাবতে পারেন যাতে এটি আপনার Effect এর dependency হতে না হয়?

</Hint>

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
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
import { useCounter } from './useCounter.js';
import { useInterval } from './useInterval.js';

export default function Counter() {
  const count = useCounter(1000);

  useInterval(() => {
    const randomColor = `hsla(${Math.random() * 360}, 100%, 50%, 0.2)`;
    document.body.style.backgroundColor = randomColor;
  }, 2000);

  return <h1>Seconds passed: {count}</h1>;
}
```

```js src/useCounter.js
import { useState } from 'react';
import { useInterval } from './useInterval.js';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}
```

```js src/useInterval.js
import { useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export function useInterval(onTick, delay) {
  useEffect(() => {
    const id = setInterval(onTick, delay);
    return () => {
      clearInterval(id);
    };
  }, [onTick, delay]);
}
```

</Sandpack>

<Solution>

`useInterval` এর ভিতরে, tick callback কে একটি Effect Event এ wrap করুন, যেমন আপনি [এই পৃষ্ঠায় আগে করেছিলেন।](/learn/reusing-logic-with-custom-hooks#passing-event-handlers-to-custom-hooks)

এটি আপনাকে আপনার Effect এর dependency থেকে `onTick` বাদ দিতে দেবে। Effect কম্পোনেন্টের প্রতিটি re-render এ re-synchronize হবে না, তাই পৃষ্ঠার background color পরিবর্তন interval প্রতি সেকেন্ডে reset হবে না এটি fire করার সুযোগ পাওয়ার আগে।

এই পরিবর্তনের সাথে, উভয় interval প্রত্যাশিত হিসাবে কাজ করে এবং একে অপরের সাথে হস্তক্ষেপ করে না:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental",
    "react-scripts": "latest"
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
import { useCounter } from './useCounter.js';
import { useInterval } from './useInterval.js';

export default function Counter() {
  const count = useCounter(1000);

  useInterval(() => {
    const randomColor = `hsla(${Math.random() * 360}, 100%, 50%, 0.2)`;
    document.body.style.backgroundColor = randomColor;
  }, 2000);

  return <h1>Seconds passed: {count}</h1>;
}
```

```js src/useCounter.js
import { useState } from 'react';
import { useInterval } from './useInterval.js';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}
```

```js src/useInterval.js active
import { useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export function useInterval(callback, delay) {
  const onTick = useEffectEvent(callback);
  useEffect(() => {
    const id = setInterval(onTick, delay);
    return () => clearInterval(id);
  }, [delay]);
}
```

</Sandpack>

</Solution>

#### একটি staggering movement implement করুন {/*implement-a-staggering-movement*/}

এই উদাহরণে, `usePointerPosition()` Hook বর্তমান pointer position ট্র্যাক করে। preview area এর উপর আপনার cursor বা আপনার আঙুল সরানোর চেষ্টা করুন এবং দেখুন লাল dot আপনার movement অনুসরণ করে। এর position `pos1` variable এ সংরক্ষিত।

আসলে, পাঁচটি (!) ভিন্ন লাল dot render করা হচ্ছে। আপনি তাদের দেখতে পাচ্ছেন না কারণ বর্তমানে তারা সবাই একই position এ প্রদর্শিত হয়। এটাই আপনার ঠিক করতে হবে। আপনি যা implement করতে চান তা হল একটি "staggered" movement: প্রতিটি dot আগের dot এর path "অনুসরণ" করা উচিত। উদাহরণস্বরূপ, যদি আপনি দ্রুত আপনার cursor সরান, প্রথম dot অবিলম্বে এটি অনুসরণ করা উচিত, দ্বিতীয় dot প্রথম dot কে একটি ছোট delay সহ অনুসরণ করা উচিত, তৃতীয় dot দ্বিতীয় dot কে অনুসরণ করা উচিত, এবং আরো।

আপনাকে `useDelayedValue` custom Hook implement করতে হবে। এর বর্তমান implementation এটিকে প্রদত্ত `value` রিটার্ন করে। পরিবর্তে, আপনি `delay` millisecond আগের value রিটার্ন করতে চান। এটি করতে আপনার কিছু state এবং একটি Effect প্রয়োজন হতে পারে।

`useDelayedValue` implement করার পরে, আপনার dot গুলো একে অপরকে অনুসরণ করে চলতে দেখা উচিত।

<Hint>

আপনাকে আপনার custom Hook এর ভিতরে `delayedValue` কে একটি state variable হিসাবে সংরক্ষণ করতে হবে। যখন `value` পরিবর্তিত হয়, আপনি একটি Effect চালাতে চাইবেন। এই Effect `delay` এর পরে `delayedValue` আপডেট করা উচিত। আপনি `setTimeout` কল করা সহায়ক মনে করতে পারেন।

এই Effect এর কি cleanup প্রয়োজন? কেন বা কেন নয়?

</Hint>

<Sandpack>

```js
import { usePointerPosition } from './usePointerPosition.js';

function useDelayedValue(value, delay) {
  // TODO: এই Hook implement করুন
  return value;
}

export default function Canvas() {
  const pos1 = usePointerPosition();
  const pos2 = useDelayedValue(pos1, 100);
  const pos3 = useDelayedValue(pos2, 200);
  const pos4 = useDelayedValue(pos3, 100);
  const pos5 = useDelayedValue(pos3, 50);
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

```js src/usePointerPosition.js
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

```css
body { min-height: 300px; }
```

</Sandpack>

<Solution>

এখানে একটি কার্যকর version আছে। আপনি `delayedValue` কে একটি state variable হিসাবে রাখেন। যখন `value` আপডেট হয়, আপনার Effect `delayedValue` আপডেট করতে একটি timeout schedule করে। এই কারণেই `delayedValue` সবসময় আসল `value` এর "পিছনে থাকে"।

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { usePointerPosition } from './usePointerPosition.js';

function useDelayedValue(value, delay) {
  const [delayedValue, setDelayedValue] = useState(value);

  useEffect(() => {
    setTimeout(() => {
      setDelayedValue(value);
    }, delay);
  }, [value, delay]);

  return delayedValue;
}

export default function Canvas() {
  const pos1 = usePointerPosition();
  const pos2 = useDelayedValue(pos1, 100);
  const pos3 = useDelayedValue(pos2, 200);
  const pos4 = useDelayedValue(pos3, 100);
  const pos5 = useDelayedValue(pos3, 50);
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

```js src/usePointerPosition.js
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

```css
body { min-height: 300px; }
```

</Sandpack>

লক্ষ্য করুন যে এই Effect এর cleanup এর প্রয়োজন *নেই*। যদি আপনি cleanup ফাংশনে `clearTimeout` কল করেন, তাহলে প্রতিবার `value` পরিবর্তিত হলে, এটি ইতিমধ্যে scheduled timeout reset করবে। movement continuous রাখতে, আপনি সমস্ত timeout fire হতে চান।

</Solution>

</Challenges>
