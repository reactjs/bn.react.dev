---
title: <StrictMode>
---


<Intro>

`<StrictMode>` আপনাকে ডেভেলপমেন্টের সময় আপনার কম্পোনেন্টগুলিতে সাধারণ বাগ সহজে খুঁজে পেতে সাহায্য করে।


```js
<StrictMode>
  <App />
</StrictMode>
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `<StrictMode>` {/*strictmode*/}

কম্পোনেন্ট ট্রির মধ্যে অতিরিক্ত ডেভেলপমেন্ট বিহেভিয়ার এবং সতর্কতা সক্রিয় করতে `StrictMode` ব্যবহার করুন:

```js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

[নীচে আরও উদাহরণ দেখুন।](#usage)

স্ট্রিক্ট মোড নিম্নলিখিত development-only আচরণগুলি সক্রিয় করে:

- আপনার কম্পোনেন্টগুলি impure রেন্ডারিং এর কারণে সৃষ্ট বাগ খুঁজে পেতে [একটি অতিরিক্ত সময় রেন্ডার হবে](#fixing-bugs-found-by-double-rendering-in-development)।
- আপনার কম্পোনেন্টগুলি ইফেক্ট ক্লিনআপ মিস হবার কারণে সৃষ্ট বাগ খুঁজে পেতে [ইফেক্টগুলি একটি অতিরিক্ত সময় চালাবে](#fixing-bugs-found-by-re-running-effects-in-development)।
- আপনার কম্পোনেন্টগুলি [deprecated এপিআই ব্যবহারের জন্য পরীক্ষা করা হবে](#fixing-deprecation-warnings-enabled-by-strict-mode)।

#### প্রপ্স {/*props*/}

`StrictMode` কোনো প্রপ গ্রহণ করে না।

#### সাবধানতা {/*caveats*/}

* `<StrictMode>`-এ মোড়ানো একটি ট্রিতে স্ট্রিক্ট মোড থেকে বের হওয়ার কোনো উপায় নেই। এটি আপনাকে এই আশ্বাস দেয় যে `<StrictMode>`-এর মধ্যে সমস্ত কম্পোনেন্ট পরীক্ষিত হয়েছে। যদি দুটি টিম যারা একটি পণ্য নিয়ে কাজ করছে তারা এই চেকগুলির দরকার নিয়ে একমত না হয়, তাদের হয় সমঝোতায় পৌঁছাতে হবে অথবা ট্রিতে `<StrictMode>` নিচে সরিয়ে নিতে হবে।

---

## ব্যবহার {/*usage*/}

### পুরো অ্যাপের জন্য স্ট্রিক্ট মোড সক্রিয় করা {/*enabling-strict-mode-for-entire-app*/}

`<StrictMode>` কম্পোনেন্টের মধ্যে থাকা পুরো কম্পোনেন্ট ট্রির জন্য স্ট্রিক্ট মোড অতিরিক্ত development-only চেক সক্রিয় করে। এই চেকগুলি আপনাকে ডেভেলপমেন্ট প্রক্রিয়ার শুরুতেই আপনার কম্পোনেন্টগুলিতে সাধারণ বাগগুলি খুঁজে পেতে সাহায্য করে।


আপনার পুরো অ্যাপের জন্য স্ট্রিক্ট মোড সক্রিয় করতে, এটি রেন্ডার করার সময় আপনার রুট কম্পোনেন্টকে `<StrictMode>` দিয়ে মোড়ান:

```js {6,8}
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

আমরা সুপারিশ করি যে আপনার পুরো অ্যাপটি স্ট্রিক্ট মোডে wrap করুন, বিশেষ করে নতুন তৈরি করা অ্যাপের ক্ষেত্রে। যদি আপনি কোনো ফ্রেমওয়ার্ক ব্যবহার করেন যা আপনার জন্য [`createRoot`](/reference/react-dom/client/createRoot) কল করে, তাহলে স্ট্রিক্ট মোড সক্রিয় করার জন্য তার ডকুমেন্টেশন দেখুন।

যদিও স্ট্রিক্ট মোড চেকগুলি **কেবল ডেভেলপমেন্টে চালানো হয়,** এগুলো আপনাকে সেই বাগগুলি খুঁজে পেতে সাহায্য করে যা আপনার কোডে ইতোমধ্যেই বিদ্যমান কিন্তু প্রডাকশনে নির্ভরযোগ্যভাবে reproduce করা কঠিন। স্ট্রিক্ট মোড আপনাকে বাগগুলি আপনার ব্যবহারকারীরা রিপোর্ট করার আগেই ঠিক করতে দেয়।

<Note>

স্ট্রিক্ট মোড নিম্নলিখিত চেক গুলো ডেভেলপমেন্টে সক্রিয় করে:

- আপনার কম্পোনেন্টগুলি impure রেন্ডারিং এর কারণে সৃষ্ট বাগ খুঁজে পেতে [একটি অতিরিক্ত সময় রেন্ডার হবে](#fixing-bugs-found-by-double-rendering-in-development)।
- আপনার কম্পোনেন্টগুলি ইফেক্ট ক্লিনআপ মিস হবার কারণে সৃষ্ট বাগ খুঁজে পেতে [ইফেক্টগুলি একটি অতিরিক্ত সময় চালাবে](#fixing-bugs-found-by-re-running-effects-in-development)।
- আপনার কম্পোনেন্টগুলি [deprecated এপিআই ব্যবহারের জন্য পরীক্ষা করা হবে](#fixing-deprecation-warnings-enabled-by-strict-mode)।

**এই সকল চেক শুধুমাত্র ডেভেলপমেন্টেই কাজ করে এবং প্রডাকশন বিল্ডে কোন প্রভাব ফেলে না।**

</Note>

---

### Enabling Strict Mode for a part of the app {/*enabling-strict-mode-for-a-part-of-the-app*/}

You can also enable Strict Mode for any part of your application:

```js {7,12}
import { StrictMode } from 'react';

function App() {
  return (
    <>
      <Header />
      <StrictMode>
        <main>
          <Sidebar />
          <Content />
        </main>
      </StrictMode>
      <Footer />
    </>
  );
}
```

In this example, Strict Mode checks will not run against the `Header` and `Footer` components. However, they will run on `Sidebar` and `Content`, as well as all of the components inside them, no matter how deep.

---

### Fixing bugs found by double rendering in development {/*fixing-bugs-found-by-double-rendering-in-development*/}

[React assumes that every component you write is a pure function.](/learn/keeping-components-pure) This means that React components you write must always return the same JSX given the same inputs (props, state, and context).

Components breaking this rule behave unpredictably and cause bugs. To help you find accidentally impure code, Strict Mode calls some of your functions (only the ones that should be pure) **twice in development.** This includes:

- Your component function body (only top-level logic, so this doesn't include code inside event handlers)
- Functions that you pass to [`useState`](/reference/react/useState), [`set` functions](/reference/react/useState#setstate), [`useMemo`](/reference/react/useMemo), or [`useReducer`](/reference/react/useReducer)
- Some class component methods like [`constructor`](/reference/react/Component#constructor), [`render`](/reference/react/Component#render), [`shouldComponentUpdate`](/reference/react/Component#shouldcomponentupdate) ([see the whole list](https://reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects))

If a function is pure, running it twice does not change its behavior because a pure function produces the same result every time. However, if a function is impure (for example, it mutates the data it receives), running it twice tends to be noticeable (that's what makes it impure!) This helps you spot and fix the bug early.

**Here is an example to illustrate how double rendering in Strict Mode helps you find bugs early.**

This `StoryTray` component takes an array of `stories` and adds one last "Create Story" item at the end:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

```js src/App.js
import { useState } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState(initialStories)
  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <StoryTray stories={stories} />
    </div>
  );
}
```

```js src/StoryTray.js active
export default function StoryTray({ stories }) {
  const items = stories;
  items.push({ id: 'create', label: 'Create Story' });
  return (
    <ul>
      {items.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
  height: 100%;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

There is a mistake in the code above. However, it is easy to miss because the initial output appears correct.

This mistake will become more noticeable if the `StoryTray` component re-renders multiple times. For example, let's make the `StoryTray` re-render with a different background color whenever you hover over it: 

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

```js src/App.js
import { useState } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState(initialStories)
  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <StoryTray stories={stories} />
    </div>
  );
}
```

```js src/StoryTray.js active
import { useState } from 'react';

export default function StoryTray({ stories }) {
  const [isHover, setIsHover] = useState(false);
  const items = stories;
  items.push({ id: 'create', label: 'Create Story' });
  return (
    <ul
      onPointerEnter={() => setIsHover(true)}
      onPointerLeave={() => setIsHover(false)}
      style={{
        backgroundColor: isHover ? '#ddd' : '#fff'
      }}
    >
      {items.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
  height: 100%;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

Notice how every time you hover over the `StoryTray` component, "Create Story" gets added to the list again. The intention of the code was to add it once at the end. But `StoryTray` directly modifies the `stories` array from the props. Every time `StoryTray` renders, it adds "Create Story" again at the end of the same array. In other words, `StoryTray` is not a pure function--running it multiple times produces different results.

To fix this problem, you can make a copy of the array, and modify that copy instead of the original one:

```js {2}
export default function StoryTray({ stories }) {
  const items = stories.slice(); // Clone the array
  // ✅ Good: Pushing into a new array
  items.push({ id: 'create', label: 'Create Story' });
```

This would [make the `StoryTray` function pure.](/learn/keeping-components-pure) Each time it is called, it would only modify a new copy of the array, and would not affect any external objects or variables. This solves the bug, but you had to make the component re-render more often before it became obvious that something is wrong with its behavior.

**In the original example, the bug wasn't obvious. Now let's wrap the original (buggy) code in `<StrictMode>`:**

<Sandpack>

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js src/App.js
import { useState } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState(initialStories)
  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <StoryTray stories={stories} />
    </div>
  );
}
```

```js src/StoryTray.js active
export default function StoryTray({ stories }) {
  const items = stories;
  items.push({ id: 'create', label: 'Create Story' });
  return (
    <ul>
      {items.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
  height: 100%;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

**Strict Mode *always* calls your rendering function twice, so you can see the mistake right away** ("Create Story" appears twice). This lets you notice such mistakes early in the process. When you fix your component to render in Strict Mode, you *also* fix many possible future production bugs like the hover functionality from before:

<Sandpack>

```js src/index.js
import { StrictMode } from 'react';
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

```js src/App.js
import { useState } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState(initialStories)
  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <StoryTray stories={stories} />
    </div>
  );
}
```

```js src/StoryTray.js active
import { useState } from 'react';

export default function StoryTray({ stories }) {
  const [isHover, setIsHover] = useState(false);
  const items = stories.slice(); // Clone the array
  items.push({ id: 'create', label: 'Create Story' });
  return (
    <ul
      onPointerEnter={() => setIsHover(true)}
      onPointerLeave={() => setIsHover(false)}
      style={{
        backgroundColor: isHover ? '#ddd' : '#fff'
      }}
    >
      {items.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
  height: 100%;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

Without Strict Mode, it was easy to miss the bug until you added more re-renders. Strict Mode made the same bug appear right away. Strict Mode helps you find bugs before you push them to your team and to your users.

[Read more about keeping components pure.](/learn/keeping-components-pure)

<Note>

If you have [React DevTools](/learn/react-developer-tools) installed, any `console.log` calls during the second render call will appear slightly dimmed. React DevTools also offers a setting (off by default) to suppress them completely.

</Note>

---

### Fixing bugs found by re-running Effects in development {/*fixing-bugs-found-by-re-running-effects-in-development*/}

Strict Mode can also help find bugs in [Effects.](/learn/synchronizing-with-effects)

Every Effect has some setup code and may have some cleanup code. Normally, React calls setup when the component *mounts* (is added to the screen) and calls cleanup when the component *unmounts* (is removed from the screen). React then calls cleanup and setup again if its dependencies changed since the last render.

When Strict Mode is on, React will also run **one extra setup+cleanup cycle in development for every Effect.** This may feel surprising, but it helps reveal subtle bugs that are hard to catch manually.

**Here is an example to illustrate how re-running Effects in Strict Mode helps you find bugs early.**

Consider this example that connects a component to a chat:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';
const roomId = 'general';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
  }, []);
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js src/chat.js
let connections = 0;

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      connections++;
      console.log('Active connections: ' + connections);
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
      connections--;
      console.log('Active connections: ' + connections);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

There is an issue with this code, but it might not be immediately clear.

To make the issue more obvious, let's implement a feature. In the example below, `roomId` is not hardcoded. Instead, the user can select the `roomId` that they want to connect to from a dropdown. Click "Open chat" and then select different chat rooms one by one. Keep track of the number of active connections in the console:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>;
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
let connections = 0;

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      connections++;
      console.log('Active connections: ' + connections);
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
      connections--;
      console.log('Active connections: ' + connections);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

You'll notice that the number of open connections always keeps growing. In a real app, this would cause performance and network problems. The issue is that [your Effect is missing a cleanup function:](/learn/synchronizing-with-effects#step-3-add-cleanup-if-needed)

```js {4}
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);
```

Now that your Effect "cleans up" after itself and destroys the outdated connections, the leak is solved. However, notice that the problem did not become visible until you've added more features (the select box).

**In the original example, the bug wasn't obvious. Now let's wrap the original (buggy) code in `<StrictMode>`:**

<Sandpack>

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';
const roomId = 'general';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
  }, []);
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js src/chat.js
let connections = 0;

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      connections++;
      console.log('Active connections: ' + connections);
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
      connections--;
      console.log('Active connections: ' + connections);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

**With Strict Mode, you immediately see that there is a problem** (the number of active connections jumps to 2). Strict Mode runs an extra setup+cleanup cycle for every Effect. This Effect has no cleanup logic, so it creates an extra connection but doesn't destroy it. This is a hint that you're missing a cleanup function.

Strict Mode lets you notice such mistakes early in the process. When you fix your Effect by adding a cleanup function in Strict Mode, you *also* fix many possible future production bugs like the select box from before:

<Sandpack>

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

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
let connections = 0;

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      connections++;
      console.log('Active connections: ' + connections);
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
      connections--;
      console.log('Active connections: ' + connections);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Notice how the active connection count in the console doesn't keep growing anymore.

Without Strict Mode, it was easy to miss that your Effect needed cleanup. By running *setup → cleanup → setup* instead of *setup* for your Effect in development, Strict Mode made the missing cleanup logic more noticeable.

[Read more about implementing Effect cleanup.](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development)

---

### Fixing deprecation warnings enabled by Strict Mode {/*fixing-deprecation-warnings-enabled-by-strict-mode*/}

React warns if some component anywhere inside a `<StrictMode>` tree uses one of these deprecated APIs:

* [`findDOMNode`](/reference/react-dom/findDOMNode). [See alternatives.](https://reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)
* `UNSAFE_` class lifecycle methods like [`UNSAFE_componentWillMount`](/reference/react/Component#unsafe_componentwillmount). [See alternatives.](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#migrating-from-legacy-lifecycles) 
* Legacy context ([`childContextTypes`](/reference/react/Component#static-childcontexttypes), [`contextTypes`](/reference/react/Component#static-contexttypes), and [`getChildContext`](/reference/react/Component#getchildcontext)). [See alternatives.](/reference/react/createContext)
* Legacy string refs ([`this.refs`](/reference/react/Component#refs)). [See alternatives.](https://reactjs.org/docs/strict-mode.html#warning-about-legacy-string-ref-api-usage)

These APIs are primarily used in older [class components](/reference/react/Component) so they rarely appear in modern apps.




================
### Enabling Strict Mode for a part of the app {/*enabling-strict-mode-for-a-part-of-the-app*/}

You can also enable Strict Mode for any part of your application:

In this example, Strict Mode checks will not run against the `Header` and `Footer` components. However, they will run on `Sidebar` and `Content`, as well as all of the components inside them, no matter how deep.

---

### Fixing bugs found by double rendering in development {/*fixing-bugs-found-by-double-rendering-in-development*/}

[React assumes that every component you write is a pure function.](/learn/keeping-components-pure) This means that React components you write must always return the same JSX given the same inputs (props, state, and context).

Components breaking this rule behave unpredictably and cause bugs. To help you find accidentally impure code, Strict Mode calls some of your functions (only the ones that should be pure) **twice in development.** This includes:

- Your component function body (only top-level logic, so this doesn't include code inside event handlers)
- Functions that you pass to [`useState`](/reference/react/useState), [`set` functions](/reference/react/useState#setstate), [`useMemo`](/reference/react/useMemo), or [`useReducer`](/reference/react/useReducer)
- Some class component methods like [`constructor`](/reference/react/Component#constructor), [`render`](/reference/react/Component#render), [`shouldComponentUpdate`](/reference/react/Component#shouldcomponentupdate) ([see the whole list](https://reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects))

If a function is pure, running it twice does not change its behavior because a pure function produces the same result every time. However, if a function is impure (for example, it mutates the data it receives), running it twice tends to be noticeable (that's what makes it impure!) This helps you spot and fix the bug early.

**Here is an example to illustrate how double rendering in Strict Mode helps you find bugs early.**

This `StoryTray` component takes an array of `stories` and adds one last "Create Story" item at the end:

There is a mistake in the code above. However, it is easy to miss because the initial output appears correct.

This mistake will become more noticeable if the `StoryTray` component re-renders multiple times. For example, let's make the `StoryTray` re-render with a different background color whenever you hover over it: 

Notice how every time you hover over the `StoryTray` component, "Create Story" gets added to the list again. The intention of the code was to add it once at the end. But `StoryTray` directly modifies the `stories` array from the props. Every time `StoryTray` renders, it adds "Create Story" again at the end of the same array. In other words, `StoryTray` is not a pure function--running it multiple times produces different results.

To fix this problem, you can make a copy of the array, and modify that copy instead of the original one:

This would [make the `StoryTray` function pure.](/learn/keeping-components-pure) Each time it is called, it would only modify a new copy of the array, and would not affect any external objects or variables. This solves the bug, but you had to make the component re-render more often before it became obvious that something is wrong with its behavior.

**In the original example, the bug wasn't obvious. Now let's wrap the original (buggy) code in `<StrictMode>`:**

**Strict Mode *always* calls your rendering function twice, so you can see the mistake right away** ("Create Story" appears twice). This lets you notice such mistakes early in the process. When you fix your component to render in Strict Mode, you *also* fix many possible future production bugs like the hover functionality from before:

Without Strict Mode, it was easy to miss the bug until you added more re-renders. Strict Mode made the same bug appear right away. Strict Mode helps you find bugs before you push them to your team and to your users.

[Read more about keeping components pure.](/learn/keeping-components-pure)

<Note>

If you have [React DevTools](/learn/react-developer-tools) installed, any `console.log` calls during the second render call will appear slightly dimmed. React DevTools also offers a setting (off by default) to suppress them completely.


### Fixing bugs found by re-running Effects in development {/*fixing-bugs-found-by-re-running-effects-in-development*/}

Strict Mode can also help find bugs in [Effects.](/learn/synchronizing-with-effects)

Every Effect has some setup code and may have some cleanup code. Normally, React calls setup when the component *mounts* (is added to the screen) and calls cleanup when the component *unmounts* (is removed from the screen). React then calls cleanup and setup again if its dependencies changed since the last render.

When Strict Mode is on, React will also run **one extra setup+cleanup cycle in development for every Effect.** This may feel surprising, but it helps reveal subtle bugs that are hard to catch manually.

**Here is an example to illustrate how re-running Effects in Strict Mode helps you find bugs early.**

Consider this example that connects a component to a chat:


There is an issue with this code, but it might not be immediately clear.

To make the issue more obvious, let's implement a feature. In the example below, `roomId` is not hardcoded. Instead, the user can select the `roomId` that they want to connect to from a dropdown. Click "Open chat" and then select different chat rooms one by one. Keep track of the number of active connections in the console:

You'll notice that the number of open connections always keeps growing. In a real app, this would cause performance and network problems. The issue is that [your Effect is missing a cleanup function:](/learn/synchronizing-with-effects#step-3-add-cleanup-if-needed)

Now that your Effect "cleans up" after itself and destroys the outdated connections, the leak is solved. However, notice that the problem did not become visible until you've added more features (the select box).

**In the original example, the bug wasn't obvious. Now let's wrap the original (buggy) code in `<StrictMode>`:**

**With Strict Mode, you immediately see that there is a problem** (the number of active connections jumps to 2). Strict Mode runs an extra setup+cleanup cycle for every Effect. This Effect has no cleanup logic, so it creates an extra connection but doesn't destroy it. This is a hint that you're missing a cleanup function.

Strict Mode lets you notice such mistakes early in the process. When you fix your Effect by adding a cleanup function in Strict Mode, you *also* fix many possible future production bugs like the select box from before:

Notice how the active connection count in the console doesn't keep growing anymore.

Without Strict Mode, it was easy to miss that your Effect needed cleanup. By running *setup → cleanup → setup* instead of *setup* for your Effect in development, Strict Mode made the missing cleanup logic more noticeable.

[Read more about implementing Effect cleanup.](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development)

---

### Fixing deprecation warnings enabled by Strict Mode {/*fixing-deprecation-warnings-enabled-by-strict-mode*/}

React warns if some component anywhere inside a `<StrictMode>` tree uses one of these deprecated APIs:

* [`findDOMNode`](/reference/react-dom/findDOMNode). [See alternatives.](https://reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)
* `UNSAFE_` class lifecycle methods like [`UNSAFE_componentWillMount`](/reference/react/Component#unsafe_componentwillmount). [See alternatives.](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#migrating-from-legacy-lifecycles) 
* Legacy context ([`childContextTypes`](/reference/react/Component#static-childcontexttypes), [`contextTypes`](/reference/react/Component#static-contexttypes), and [`getChildContext`](/reference/react/Component#getchildcontext)). [See alternatives.](/reference/react/createContext)
* Legacy string refs ([`this.refs`](/reference/react/Component#refs)). [See alternatives.](https://reactjs.org/docs/strict-mode.html#warning-about-legacy-string-ref-api-usage)

These APIs are primarily used in older [class components](/reference/react/Component) so they rarely appear in modern apps.




### DOM-এর ভিন্ন অংশে রেন্ডারিং {/*rendering-to-a-different-part-of-the-dom*/}

*পোর্টালস* আপনার কম্পোনেন্টগুলিকে তাদের কিছু চাইল্ড DOM-এর ভিন্ন স্থানে রেন্ডার করতে দেয়। এটি আপনার কম্পোনেন্টের একটি অংশকে যেকোনো কন্টেইনার থেকে "মুক্ত" করে। উদাহরণস্বরূপ, একটি কম্পোনেন্ট একটি মডাল ডায়লগ বা একটি টুলটিপ প্রদর্শন করতে পারে যা বাকি পৃষ্ঠার উপরে এবং বাইরে প্রদর্শিত হয়।

একটি পোর্টাল তৈরি করতে, `createPortal`-এর ফলাফল রেন্ডার করুন <CodeStep step={1}>কিছু JSX</CodeStep> এবং <CodeStep step={2}>DOM নোড যেখানে এটি যাওয়া উচিত</CodeStep>:

React <CodeStep step={1}>আপনি যে JSX পাস করেছেন</CodeStep> এর DOM নোডগুলি <CodeStep step={2}>আপনি প্রদত্ত DOM নোডের</CodeStep> মধ্যে রাখবে।

একটি পোর্টাল ছাড়া, দ্বিতীয় `<p>` অভিভাবক `<div>`-এর ভেতরে স্থাপন করা হবে, কিন্তু পোর্টালটি এটিকে [`document.body`:](https://developer.mozilla.org/en-US/docs/Web/API/Document/body) এ "টেলিপোর্ট" করেছে।

লক্ষ্য করুন কিভাবে দ্বিতীয় প্যারাগ্রাফ দৃশ্যত বর্ডার সহ অভিভাবক `<div>`-এর বাইরে প্রদর্শিত হচ্ছে। যদি আপনি ডেভেলপার টুলস দ্বারা DOM কাঠামো পরীক্ষা করেন, আপনি দেখবেন যে দ্বিতীয় `<p>` সরাসরি `<body>`-তে স্থাপন করা হয়েছে:

একটি পোর্টাল কেবল DOM নোডের শারীরিক অবস্থান পরিবর্তন করে। অন্যান্য সকল উপায়ে, আপনি যে JSX পোর্টালে রেন্ডার করেন তা React কম্পোনেন্টের একটি চাইল্ড নোড হিসেবে কাজ করে যা এটি রেন্ডার করে। উদাহরণস্বরূপ, চাইল্ড অভিভাবক ট্রি দ্বারা প্রদত্ত কনটেক্স্ট অ্যাক্সেস করতে পারে, এবং ইভেন্টগুলি চাইল্ড থেকে অভিভাবকদের কাছে React ট্রি অনুসারে বুদবুদ করে উঠে।

### একটি পোর্টাল দ্বারা মডাল ডায়লগ রেন্ডার করা {/*rendering-a-modal-dialog-with-a-portal*/}

আপনি একটি পোর্টাল ব্যবহার করে একটি মডাল ডায়লগ তৈরি করতে পারেন যা পৃষ্ঠার বাকি অংশের উপরে ভাসমান, এমনকি যদি ডায়লগ ডাকা কম্পোনেন্টটি `overflow: hidden` বা অন্যান্য স্টাইল যা ডায়লগের সাথে বাধা দেয় এমন কন্টেইনারের মধ্যে থাকে।

এই উদাহরণে, Strict Mode চেকগুলি `Header` এবং `Footer` কম্পোনেন্টগুলির বিরুদ্ধে চালানো হবে না। তবে, `Sidebar` এবং `Content`, সেইসাথে তাদের ভেতরে থাকা সমস্ত কম্পোনেন্টগুলিতে, যত গভীরেই হোক না কেন, চেকগুলি চালানো হবে।

---

### ডেভেলপমেন্টে ডাবল রেন্ডারিং দ্বারা পাওয়া বাগ ঠিক করা {/*fixing-bugs-found-by-double-rendering-in-development*/}

[React ধরে নেয় যে আপনি যেকোনো কম্পোনেন্ট লিখেন সেটি একটি শুদ্ধ ফাংশন হয়।](/learn/keeping-components-pure) এর অর্থ হলো React আপনার লিখিত কম্পোনেন্টগুলি সর্বদা একই ইনপুট (প্রপস, স্টেট এবং কনটেক্স্ট) দেওয়া হলে একই JSX ফেরত দেবে।

এই নিয়ম ভঙ্গ করা কম্পোনেন্টগুলি অনির্দিষ্টভাবে আচরণ করে এবং বাগ তৈরি করে। আপনাকে আকস্মিকভাবে অশুদ্ধ কোড খুঁজে পেতে সাহায্য করার জন্য, Strict Mode ডেভেলপমেন্টে **আপনার কিছু ফাংশনকে দুবার কল করে**। এতে অন্তর্ভুক্ত:

- আপনার কম্পোনেন্ট ফাংশন বডি (শুধুমাত্র টপ-লেভেল লজিক, তাই এতে ইভেন্ট হ্যান্ডলারের ভেতরের কোড অন্তর্ভুক্ত নয়)
- ফাংশনগুলি যা আপনি [`useState`](/reference/react/useState), [`set` ফাংশনগুলি](/reference/react/useState#setstate), [`useMemo`](/reference/react/useMemo), অথবা [`useReducer`](/reference/react/useReducer)-এ পাস করেন
- ক্লাস কম্পোনেন্টের কিছু মেথড যেমন [`constructor`](/reference/react/Component#constructor), [`render`](/reference/react/Component#render), [`shouldComponentUpdate`](/reference/react/Component#shouldcomponentupdate) ([পুরো তালিকা দেখুন](https://reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects))

যদি কোনো ফাংশন শুদ্ধ হয়, তাহলে এটি দুবার চালানোর ফলে এর আচরণ পরিবর্তন হয় না কারণ একটি শুদ্ধ ফাংশন প্রতিবারই একই ফলাফল উৎপন্ন করে। তবে, যদি কোনো ফাংশন অশুদ্ধ হয় (উদাহরণস্বরূপ, এটি প্রাপ্ত ডেটা পরিবর্তন করে), তাহলে এটি দুবার চালানো সাধারণত লক্ষ্যণীয় হয় (এটাই এটিকে অশুদ্ধ করে!) এটি আপনাকে বাগটি শীঘ্রই চিহ্নিত করতে এবং ঠিক করতে সাহায্য করে।

**এখানে একটি উদাহরণ দেওয়া হয়েছে যা দেখায় কিভাবে Strict Mode-এ ডাবল রেন্ডারিং আপনাকে বাগগুলি শীঘ্রই খুঁজে পেতে সাহায্য করে।**


