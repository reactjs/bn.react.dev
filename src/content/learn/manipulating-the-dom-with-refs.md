---
title: 'Ref ব্যবহার করে DOM ম্যানিপুলেশন'
---

<Intro>

React স্বয়ংক্রিয়ভাবে [DOM](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) আপডেট করে যেন এটি আপনার রেন্ডার আউটপুটের সাথে মিলে যায়, তাই আপনার components দ্বারা প্রায় সময়ই এটি পরিবর্তন করার প্রয়োজন হবে না। তবে, মাঝে মাঝে আপনার React দ্বারা পরিচালিত DOM elements এর প্রয়োজন হতে পারে--উদাহরণস্বরূপ, একটি node কে focus করতে, scroll করতে, অথবা এর আকার এবং অবস্থান পরিমাপ করতে। React এ এই ধরণের কাজ করার জন্য কোনো বিল্ট-ইন উপায় নেই, তাই আপনার DOM node এর *ref* প্রয়োজন হবে।

</Intro>

<YouWillLearn>

- কীভাবে React এর পরিচালিত একটি DOM নোড `ref` এট্রিবিউট ব্যবহার করে অ্যাক্সেস করবেন
- কীভাবে `ref` JSX এট্রিবিউট `useRef` হুকের সাথে সম্পর্কিত।
- কীভাবে অন্য একটি কম্পোনেন্টের DOM নোড অ্যাক্সেস করবেন
- কোন কোন ক্ষেত্রে React এর পরিচালিত একটি DOM নোড পরিবর্তন করা নিরাপদ

</YouWillLearn>

## একটা নোডকে ref পর্যন্ত নিয়ে যাওয়া {/*getting-a-ref-to-the-node*/}

React এর পরিচালিত একটি DOM নোড অ্যাক্সেস করতে, প্রথমে, `useRef` হুকটি ইমপোর্ট করুনঃ

```js
import { useRef } from 'react';
```

এর পর, একে ব্যবহার করে আপনার কম্পোনেন্টের মধ্যে একটি ref ডিক্লেয়ার করেনঃ

```js
const myRef = useRef(null);
```

সবশেষে, DOM নোডে একে `ref` এট্রিবিউট হিসেবে পাস করে দিনঃ

```js
<div ref={myRef}>
```

`useRef` হুক একটি অবজেক্ট রিটার্ন করে যার একটি মাত্র প্রপার্টি থাকে `current` নামে। প্রাথমিকভাবে, `myRef.current` হবে `null`। যখন React এই `<div>` এর জন্য একটি DOM node তৈরি করে, React এই নোডের একটি রেফারেন্স `myRef.current`-এ রাখবে। তারপর আপনি আপনার [event handlers](/learn/responding-to-events) থেকে এই DOM node এ অ্যাক্সেস করতে পারেন এবং এর উপর defined বিল্ট-ইন [browser APIs](https://developer.mozilla.org/docs/Web/API/Element) ব্যবহার করতে পারেন।

```js
// আপনি যেকোন ব্রাউজার API ব্যবহার করতে পারেন। উদাহরণস্বরূপঃ
myRef.current.scrollIntoView();
```

### উদাহরণঃ একটা টেক্সট ইনপুটে ফোকাস করা {/*example-focusing-a-text-input*/}

এই উদাহরণে, বাটনে ক্লিক করলে ইনপুট ফোকাস হবেঃ

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

এটা করার জন্যঃ

1. `useRef` হুক ব্যবহার করে `inputRef` ডিক্লেয়ার করুন।
2. `<input ref={inputRef}>` হিসেবে একে পাস করে দিন। এটা React কে বলবে যেন  **এই  `<input>`এর DOM নোড `inputRef.current`এর মধ্যে রাখা হয়।**
3. `handleClick` ফাংশনে, `inputRef.current` থেকে ইনপুট DOM নোড রিড করুন এবং `inputRef.current.focus()` দিয়ে এর উপর [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) কল করুন।
4. `handleClick` ইভেন্ট হ্যান্ডলারটি  `onClick` এর সাহায্যে `<button>` এ পাঠিয়ে দিন।

যদিও DOM ম্যানিপুলেশন এর জন্যই refs এর ব্যবহার সবচেয়ে বেশি হয়, `useRef` হুক React এর বাইরে অন্যান্য জিনিস সংরক্ষণ করার জন্য ব্যবহৃত হতে পারে, যেমন টাইমার ID। State এর মতোই, রেন্ডারে মধ্যবর্তী সময়ে ref ঠিকঠাক থাকে। Refs হল state variables এর মত যেগুলি সেট করার সময় re-render ট্রিগার হয় না। রেফস সম্বন্ধে পড়ুন [ref এর সাহায্যে ভ্যালু রেফারেন্সিং](/learn/referencing-values-with-refs) অংশে।

### উদাহরণঃ কোন এলিমেন্ট পর্যন্ত স্ক্রল করা {/*example-scrolling-to-an-element*/}

আপনি একটি কম্পোনেন্টে একাধিক ref রাখতে পারেন। এই উদাহরণে, তিনটি চিত্রের একটি ক্যারাসেল আছে। প্রতিটি বাটন ব্রাউজারে ওই ছবির সাথে সম্পর্কিত DOM নোডে [`scrollIntoView()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) মেথড কল করে ছবিটিকে মাঝামাঝি আনে।

<Sandpack>

```js
import { useRef } from 'react';

export default function CatFriends() {
  const firstCatRef = useRef(null);
  const secondCatRef = useRef(null);
  const thirdCatRef = useRef(null);

  function handleScrollToFirstCat() {
    firstCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function handleScrollToSecondCat() {
    secondCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function handleScrollToThirdCat() {
    thirdCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  return (
    <>
      <nav>
        <button onClick={handleScrollToFirstCat}>
          Tom
        </button>
        <button onClick={handleScrollToSecondCat}>
          Maru
        </button>
        <button onClick={handleScrollToThirdCat}>
          Jellylorum
        </button>
      </nav>
      <div>
        <ul>
          <li>
            <img
              src="https://placekitten.com/g/200/200"
              alt="Tom"
              ref={firstCatRef}
            />
          </li>
          <li>
            <img
              src="https://placekitten.com/g/300/200"
              alt="Maru"
              ref={secondCatRef}
            />
          </li>
          <li>
            <img
              src="https://placekitten.com/g/250/200"
              alt="Jellylorum"
              ref={thirdCatRef}
            />
          </li>
        </ul>
      </div>
    </>
  );
}
```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}
```

</Sandpack>

<DeepDive>

#### কীভাবে একটি ref callback ব্যবহার করে ref এর একটা তালিকা ম্যানেজ করবেন {/*how-to-manage-a-list-of-refs-using-a-ref-callback*/}

উপরের উদাহরণগুলিতে, ref এর একটি পূর্বনির্ধারিত সংখ্যা রয়েছে। যদিও, মাঝে মাঝে আপনার তালিকার প্রতিটি আইটেমের জন্য একটি ref প্রয়োজন হতে পারে, এবং আপনি জানবেন না আপনার কতগুলো থাকবে। এরকম কিছু **কাজ করবে নাঃ**

```js
<ul>
  {items.map((item) => {
    // কাজ করে না!
    const ref = useRef(null);
    return <li ref={ref} />;
  })}
</ul>
```

এর কারণ **হুক শুধুমাত্র আপনার কম্পোনেন্টের শীর্ষ-স্তরে ডাকতে হবে।** আপনি একটি লুপে, একটি কন্ডিশনে, বা একটি `map()` কলের ভিতরে `useRef` ডাকতে পারবেন না।

একে এডিয়ে যাবার একটি সম্ভাব্য উপায় হল প্যারেন্ট এলিমেন্টে একটা মাত্র ref নিয়ে যাওয়া এবং তারপরে DOM ম্যানিপুলেশন পদ্ধতিগুলি যেমন [`querySelectorAll`](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) ব্যবহার করে এটি থেকে individual child node "খুঁজে" বের করা। যদিও, এটি অত্যন্ত নাজুক এবং যদি আপনার DOM কাঠামো পরিবর্তন হয় তবে এটি ভেঙে যেতে পারে।

অন্য একটি সমাধান হলো **`ref` এট্রিবিউটে একটি ফাংশন পাস করা।** এটি [`ref` callback.](/reference/react-dom/components/common#ref-callback) নামে পরিচিত। React যখন রেফ সেট করার সময় হবে তখন এটি আপনার ref callback কে DOM নোড দিয়ে কল করবে, এবং যখন এটি সাফ করার সময় হবে তখন `null` দিয়ে ডাকবে। এটি আপনাকে আপনার নিজের একটি array বা একটি [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) বজায় রাখতে দেয়, এবং এর মাধ্যমে আপনি তার ইনডেক্স বা কোন ধরণের ID দ্বারা যেকোনো ref অ্যাক্সেস করতে পারেন।

এই উদাহরণটি দেখায় যে আপনি এই পদ্ধতিটি কীভাবে ব্যবহার করে একটি দীর্ঘ তালিকায় যেকোনো নোডে স্ক্রল করতে পারেনঃ

<Sandpack>

```js
import { useRef } from 'react';

export default function CatFriends() {
  const itemsRef = useRef(null);

  function scrollToId(itemId) {
    const map = getMap();
    const node = map.get(itemId);
    node.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function getMap() {
    if (!itemsRef.current) {
      // Initialize the Map on first usage.
      itemsRef.current = new Map();
    }
    return itemsRef.current;
  }

  return (
    <>
      <nav>
        <button onClick={() => scrollToId(0)}>
          Tom
        </button>
        <button onClick={() => scrollToId(5)}>
          Maru
        </button>
        <button onClick={() => scrollToId(9)}>
          Jellylorum
        </button>
      </nav>
      <div>
        <ul>
          {catList.map(cat => (
            <li
              key={cat.id}
              ref={(node) => {
                const map = getMap();
                if (node) {
                  map.set(cat.id, node);
                } else {
                  map.delete(cat.id);
                }
              }}
            >
              <img
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

const catList = [];
for (let i = 0; i < 10; i++) {
  catList.push({
    id: i,
    imageUrl: 'https://placekitten.com/250/200?image=' + i
  });
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}
```

</Sandpack>

In this example, `itemsRef` doesn't hold a single DOM node. Instead, it holds a [Map](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map) from item ID to a DOM node. ([Refs can hold any values!](/learn/referencing-values-with-refs)) The [`ref` callback](/reference/react-dom/components/common#ref-callback) on every list item takes care to update the Map:

```js
<li
  key={cat.id}
  ref={node => {
    const map = getMap();
    if (node) {
      // Add to the Map
      map.set(cat.id, node);
    } else {
      // Remove from the Map
      map.delete(cat.id);
    }
  }}
>
```

This lets you read individual DOM nodes from the Map later.

</DeepDive>

## Accessing another component's DOM nodes {/*accessing-another-components-dom-nodes*/}

আপনি যখন এমন একটা বিল্ট-ইন কম্পোনেন্টে রেফ বসান যা `<input />` এর মত একটি ব্রাউজার এলিমেন্ট আউটপুট হিসেবে দেয়, React সেই ref এর  `current` হিসেবে প্রপার্টি সম্পর্কিত DOM নোড (যেমন ব্রাউজারের প্রকৃত `<input />`) সেট করে দেবে। 

তবে, আপনি যদি **আপনার নিজের** কম্পোনেন্টে একটা ref বসাতে চান, যেমন `<MyInput />`, তাহলে স্বাভাবিকভাবে আপনি `null` পাবেন। এখানে বিষয়টা দেখায় এমন একটি উদাহরণ দেখানো হল। খেয়াল করুন বাটনে ক্লিক করলে ইনপুটে ফোকাস **হয় না।**

<Sandpack>

```js
import { useRef } from 'react';

function MyInput(props) {
  return <input {...props} />;
}

export default function MyForm() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

আপনি যেন ঝামেলাটা খেয়াল করেন, সেজন্য React কনসোলে একটি এরর দেখিয়ে দেয়ঃ

<ConsoleBlock level="error">

সতর্কতাঃ ফাংশন কম্পোনেন্টে ref দেওয়া যায় না। এমন একটি ref এ অ্যাক্সেসের চেষ্টা বিফল হবে। আপনি কি React.forwardRef() ব্যবহার করতে চেয়েছিলেন?

</ConsoleBlock>

এটি ঘটে কারণ স্বাভাবিকভাবে React কম্পোনেন্টকে অন্যান্য কম্পোনেন্টের DOM নোড অ্যাক্সেস করতে দেয় না। তার নিজের childrenদের জন্যও নয়! এটি ইচ্ছাকৃত। Ref এক ধরণের escape hatch যা খুব কম ব্যবহার করা উচিত। ম্যানুয়ালি _অন্য_ কম্পোনেন্টের DOM নোড পরিবর্তন করা আপনার কোডকে আরও নাজুক বানিয়ে ফেলে।

Instead, components that _want_ to expose their DOM nodes have to **opt in** to that behavior. A component can specify that it "forwards" its ref to one of its children. Here's how `MyInput` can use the `forwardRef` API:

এর পরিবর্তে, যে কম্পোনেন্টগুলি তাদের DOM নোড উন্মুক্ত করতে _চায়_ তাদেরকে এই আচরণ **আয়ত্ব করে** নিতে হবে। একটি কম্পোনেন্ট নির্দিষ্ট করতে পারে যে এটি তার ref তার একটি সন্তানের কাছে "ফরোয়ার্ড" করে। এটা কিভাবে MyInput forwardRef API ব্যবহার করতে পারে তা দেখানো হলো:

```js
const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});
```

এটা কাজ করে এই ভাবেঃ

1. `<MyInput ref={inputRef} />` React কে বলে corresponding DOM নোড `inputRef.current` এর মধ্যে রাখতে। কিন্তু, এই সিদ্ধান্তটা`MyInput` কম্পোনেন্টের উপর নির্ভর করে যে সে এটা করবে কি না--স্বাভাবিকভাবে সে এটা করে না।
2. `MyInput` কম্পোনেন্টটা `forwardRef` ব্যবহার করে ডিক্লেয়ার করা হয়। **এটা  উপরের `inputRef` কে দ্বিতীয় `ref` আর্গুমেন্ট হিসেবে নেওয়ার সিদ্ধান্ত নেয়,** যা `props` এর পরে ডিক্লেয়ার করা হয়।
3. `MyInput` যেই `ref` টা পায় সেটা নিজেই এর ভিতরকার `<input>` এ পাস করে দেয়।

এখন বাটন ক্লিক করে ইনপুট ফোকাস হচ্ছে ঠিকঠাকভাবেঃ

<Sandpack>

```js
import { forwardRef, useRef } from 'react';

const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

In design systems, it is a common pattern for low-level components like buttons, inputs, and so on, to forward their refs to their DOM nodes. On the other hand, high-level components like forms, lists, or page sections usually won't expose their DOM nodes to avoid accidental dependencies on the DOM structure.

<DeepDive>

#### Exposing a subset of the API with an imperative handle {/*exposing-a-subset-of-the-api-with-an-imperative-handle*/}

In the above example, `MyInput` exposes the original DOM input element. This lets the parent component call `focus()` on it. However, this also lets the parent component do something else--for example, change its CSS styles. In uncommon cases, you may want to restrict the exposed functionality. You can do that with `useImperativeHandle`:

<Sandpack>

```js
import {
  forwardRef, 
  useRef, 
  useImperativeHandle
} from 'react';

const MyInput = forwardRef((props, ref) => {
  const realInputRef = useRef(null);
  useImperativeHandle(ref, () => ({
    // Only expose focus and nothing else
    focus() {
      realInputRef.current.focus();
    },
  }));
  return <input {...props} ref={realInputRef} />;
});

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

Here, `realInputRef` inside `MyInput` holds the actual input DOM node. However, `useImperativeHandle` instructs React to provide your own special object as the value of a ref to the parent component. So `inputRef.current` inside the `Form` component will only have the `focus` method. In this case, the ref "handle" is not the DOM node, but the custom object you create inside `useImperativeHandle` call.

</DeepDive>

## When React attaches the refs {/*when-react-attaches-the-refs*/}

In React, every update is split in [two phases](/learn/render-and-commit#step-3-react-commits-changes-to-the-dom):

* During **render,** React calls your components to figure out what should be on the screen.
* During **commit,** React applies changes to the DOM.

In general, you [don't want](/learn/referencing-values-with-refs#best-practices-for-refs) to access refs during rendering. That goes for refs holding DOM nodes as well. During the first render, the DOM nodes have not yet been created, so `ref.current` will be `null`. And during the rendering of updates, the DOM nodes haven't been updated yet. So it's too early to read them.

React sets `ref.current` during the commit. Before updating the DOM, React sets the affected `ref.current` values to `null`. After updating the DOM, React immediately sets them to the corresponding DOM nodes.

**Usually, you will access refs from event handlers.** If you want to do something with a ref, but there is no particular event to do it in, you might need an Effect. We will discuss effects on the next pages.

<DeepDive>

#### Flushing state updates synchronously with flushSync {/*flushing-state-updates-synchronously-with-flush-sync*/}

Consider code like this, which adds a new todo and scrolls the screen down to the last child of the list. Notice how, for some reason, it always scrolls to the todo that was *just before* the last added one:

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function TodoList() {
  const listRef = useRef(null);
  const [text, setText] = useState('');
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    setText('');
    setTodos([ ...todos, newTodo]);
    listRef.current.lastChild.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest'
    });
  }

  return (
    <>
      <button onClick={handleAdd}>
        Add
      </button>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <ul ref={listRef}>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}

let nextId = 0;
let initialTodos = [];
for (let i = 0; i < 20; i++) {
  initialTodos.push({
    id: nextId++,
    text: 'Todo #' + (i + 1)
  });
}
```

</Sandpack>

The issue is with these two lines:

```js
setTodos([ ...todos, newTodo]);
listRef.current.lastChild.scrollIntoView();
```

In React, [state updates are queued.](/learn/queueing-a-series-of-state-updates) Usually, this is what you want. However, here it causes a problem because `setTodos` does not immediately update the DOM. So the time you scroll the list to its last element, the todo has not yet been added. This is why scrolling always "lags behind" by one item.

To fix this issue, you can force React to update ("flush") the DOM synchronously. To do this, import `flushSync` from `react-dom` and **wrap the state update** into a `flushSync` call:

```js
flushSync(() => {
  setTodos([ ...todos, newTodo]);
});
listRef.current.lastChild.scrollIntoView();
```

This will instruct React to update the DOM synchronously right after the code wrapped in `flushSync` executes. As a result, the last todo will already be in the DOM by the time you try to scroll to it:

<Sandpack>

```js
import { useState, useRef } from 'react';
import { flushSync } from 'react-dom';

export default function TodoList() {
  const listRef = useRef(null);
  const [text, setText] = useState('');
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    flushSync(() => {
      setText('');
      setTodos([ ...todos, newTodo]);      
    });
    listRef.current.lastChild.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest'
    });
  }

  return (
    <>
      <button onClick={handleAdd}>
        Add
      </button>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <ul ref={listRef}>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}

let nextId = 0;
let initialTodos = [];
for (let i = 0; i < 20; i++) {
  initialTodos.push({
    id: nextId++,
    text: 'Todo #' + (i + 1)
  });
}
```

</Sandpack>

</DeepDive>

## Best practices for DOM manipulation with refs {/*best-practices-for-dom-manipulation-with-refs*/}

Refs are an escape hatch. You should only use them when you have to "step outside React". Common examples of this include managing focus, scroll position, or calling browser APIs that React does not expose.

If you stick to non-destructive actions like focusing and scrolling, you shouldn't encounter any problems. However, if you try to **modify** the DOM manually, you can risk conflicting with the changes React is making.

To illustrate this problem, this example includes a welcome message and two buttons. The first button toggles its presence using [conditional rendering](/learn/conditional-rendering) and [state](/learn/state-a-components-memory), as you would usually do in React. The second button uses the [`remove()` DOM API](https://developer.mozilla.org/en-US/docs/Web/API/Element/remove) to forcefully remove it from the DOM outside of React's control.

Try pressing "Toggle with setState" a few times. The message should disappear and appear again. Then press "Remove from the DOM". This will forcefully remove it. Finally, press "Toggle with setState":

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Counter() {
  const [show, setShow] = useState(true);
  const ref = useRef(null);

  return (
    <div>
      <button
        onClick={() => {
          setShow(!show);
        }}>
        Toggle with setState
      </button>
      <button
        onClick={() => {
          ref.current.remove();
        }}>
        Remove from the DOM
      </button>
      {show && <p ref={ref}>Hello world</p>}
    </div>
  );
}
```

```css
p,
button {
  display: block;
  margin: 10px;
}
```

</Sandpack>

After you've manually removed the DOM element, trying to use `setState` to show it again will lead to a crash. This is because you've changed the DOM, and React doesn't know how to continue managing it correctly.

**Avoid changing DOM nodes managed by React.** Modifying, adding children to, or removing children from elements that are managed by React can lead to inconsistent visual results or crashes like above.

However, this doesn't mean that you can't do it at all. It requires caution. **You can safely modify parts of the DOM that React has _no reason_ to update.** For example, if some `<div>` is always empty in the JSX, React won't have a reason to touch its children list. Therefore, it is safe to manually add or remove elements there.

<Recap>

- Refs are a generic concept, but most often you'll use them to hold DOM elements.
- You instruct React to put a DOM node into `myRef.current` by passing `<div ref={myRef}>`.
- Usually, you will use refs for non-destructive actions like focusing, scrolling, or measuring DOM elements.
- A component doesn't expose its DOM nodes by default. You can opt into exposing a DOM node by using `forwardRef` and passing the second `ref` argument down to a specific node.
- Avoid changing DOM nodes managed by React.
- If you do modify DOM nodes managed by React, modify parts that React has no reason to update.

</Recap>



<Challenges>

#### Play and pause the video {/*play-and-pause-the-video*/}

In this example, the button toggles a state variable to switch between a playing and a paused state. However, in order to actually play or pause the video, toggling state is not enough. You also need to call [`play()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play) and [`pause()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause) on the DOM element for the `<video>`. Add a ref to it, and make the button work.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function VideoPlayer() {
  const [isPlaying, setIsPlaying] = useState(false);

  function handleClick() {
    const nextIsPlaying = !isPlaying;
    setIsPlaying(nextIsPlaying);
  }

  return (
    <>
      <button onClick={handleClick}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <video width="250">
        <source
          src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
          type="video/mp4"
        />
      </video>
    </>
  )
}
```

```css
button { display: block; margin-bottom: 20px; }
```

</Sandpack>

For an extra challenge, keep the "Play" button in sync with whether the video is playing even if the user right-clicks the video and plays it using the built-in browser media controls. You might want to listen to `onPlay` and `onPause` on the video to do that.

<Solution>

Declare a ref and put it on the `<video>` element. Then call `ref.current.play()` and `ref.current.pause()` in the event handler depending on the next state.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function VideoPlayer() {
  const [isPlaying, setIsPlaying] = useState(false);
  const ref = useRef(null);

  function handleClick() {
    const nextIsPlaying = !isPlaying;
    setIsPlaying(nextIsPlaying);

    if (nextIsPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }

  return (
    <>
      <button onClick={handleClick}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <video
        width="250"
        ref={ref}
        onPlay={() => setIsPlaying(true)}
        onPause={() => setIsPlaying(false)}
      >
        <source
          src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
          type="video/mp4"
        />
      </video>
    </>
  )
}
```

```css
button { display: block; margin-bottom: 20px; }
```

</Sandpack>

In order to handle the built-in browser controls, you can add `onPlay` and `onPause` handlers to the `<video>` element and call `setIsPlaying` from them. This way, if the user plays the video using the browser controls, the state will adjust accordingly.

</Solution>

#### Focus the search field {/*focus-the-search-field*/}

Make it so that clicking the "Search" button puts focus into the field.

<Sandpack>

```js
export default function Page() {
  return (
    <>
      <nav>
        <button>Search</button>
      </nav>
      <input
        placeholder="Looking for something?"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

<Solution>

Add a ref to the input, and call `focus()` on the DOM node to focus it:

<Sandpack>

```js
import { useRef } from 'react';

export default function Page() {
  const inputRef = useRef(null);
  return (
    <>
      <nav>
        <button onClick={() => {
          inputRef.current.focus();
        }}>
          Search
        </button>
      </nav>
      <input
        ref={inputRef}
        placeholder="Looking for something?"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

</Solution>

#### Scrolling an image carousel {/*scrolling-an-image-carousel*/}

This image carousel has a "Next" button that switches the active image. Make the gallery scroll horizontally to the active image on click. You will want to call [`scrollIntoView()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) on the DOM node of the active image:

```js
node.scrollIntoView({
  behavior: 'smooth',
  block: 'nearest',
  inline: 'center'
});
```

<Hint>

You don't need to have a ref to every image for this exercise. It should be enough to have a ref to the currently active image, or to the list itself. Use `flushSync` to ensure the DOM is updated *before* you scroll.

</Hint>

<Sandpack>

```js
import { useState } from 'react';

export default function CatFriends() {
  const [index, setIndex] = useState(0);
  return (
    <>
      <nav>
        <button onClick={() => {
          if (index < catList.length - 1) {
            setIndex(index + 1);
          } else {
            setIndex(0);
          }
        }}>
          Next
        </button>
      </nav>
      <div>
        <ul>
          {catList.map((cat, i) => (
            <li key={cat.id}>
              <img
                className={
                  index === i ?
                    'active' :
                    ''
                }
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

const catList = [];
for (let i = 0; i < 10; i++) {
  catList.push({
    id: i,
    imageUrl: 'https://placekitten.com/250/200?image=' + i
  });
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}

img {
  padding: 10px;
  margin: -10px;
  transition: background 0.2s linear;
}

.active {
  background: rgba(0, 100, 150, 0.4);
}
```

</Sandpack>

<Solution>

You can declare a `selectedRef`, and then pass it conditionally only to the current image:

```js
<li ref={index === i ? selectedRef : null}>
```

When `index === i`, meaning that the image is the selected one, the `<li>` will receive the `selectedRef`. React will make sure that `selectedRef.current` always points at the correct DOM node.

Note that the `flushSync` call is necessary to force React to update the DOM before the scroll. Otherwise, `selectedRef.current` would always point at the previously selected item.

<Sandpack>

```js
import { useRef, useState } from 'react';
import { flushSync } from 'react-dom';

export default function CatFriends() {
  const selectedRef = useRef(null);
  const [index, setIndex] = useState(0);

  return (
    <>
      <nav>
        <button onClick={() => {
          flushSync(() => {
            if (index < catList.length - 1) {
              setIndex(index + 1);
            } else {
              setIndex(0);
            }
          });
          selectedRef.current.scrollIntoView({
            behavior: 'smooth',
            block: 'nearest',
            inline: 'center'
          });            
        }}>
          Next
        </button>
      </nav>
      <div>
        <ul>
          {catList.map((cat, i) => (
            <li
              key={cat.id}
              ref={index === i ?
                selectedRef :
                null
              }
            >
              <img
                className={
                  index === i ?
                    'active'
                    : ''
                }
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

const catList = [];
for (let i = 0; i < 10; i++) {
  catList.push({
    id: i,
    imageUrl: 'https://placekitten.com/250/200?image=' + i
  });
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}

img {
  padding: 10px;
  margin: -10px;
  transition: background 0.2s linear;
}

.active {
  background: rgba(0, 100, 150, 0.4);
}
```

</Sandpack>

</Solution>

#### Focus the search field with separate components {/*focus-the-search-field-with-separate-components*/}

Make it so that clicking the "Search" button puts focus into the field. Note that each component is defined in a separate file and shouldn't be moved out of it. How do you connect them together?

<Hint>

You'll need `forwardRef` to opt into exposing a DOM node from your own component like `SearchInput`.

</Hint>

<Sandpack>

```js App.js
import SearchButton from './SearchButton.js';
import SearchInput from './SearchInput.js';

export default function Page() {
  return (
    <>
      <nav>
        <SearchButton />
      </nav>
      <SearchInput />
    </>
  );
}
```

```js SearchButton.js
export default function SearchButton() {
  return (
    <button>
      Search
    </button>
  );
}
```

```js SearchInput.js
export default function SearchInput() {
  return (
    <input
      placeholder="Looking for something?"
    />
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

<Solution>

You'll need to add an `onClick` prop to the `SearchButton`, and make the `SearchButton` pass it down to the browser `<button>`. You'll also pass a ref down to `<SearchInput>`, which will forward it to the real `<input>` and populate it. Finally, in the click handler, you'll call `focus` on the DOM node stored inside that ref.

<Sandpack>

```js App.js
import { useRef } from 'react';
import SearchButton from './SearchButton.js';
import SearchInput from './SearchInput.js';

export default function Page() {
  const inputRef = useRef(null);
  return (
    <>
      <nav>
        <SearchButton onClick={() => {
          inputRef.current.focus();
        }} />
      </nav>
      <SearchInput ref={inputRef} />
    </>
  );
}
```

```js SearchButton.js
export default function SearchButton({ onClick }) {
  return (
    <button onClick={onClick}>
      Search
    </button>
  );
}
```

```js SearchInput.js
import { forwardRef } from 'react';

export default forwardRef(
  function SearchInput(props, ref) {
    return (
      <input
        ref={ref}
        placeholder="Looking for something?"
      />
    );
  }
);
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

</Solution>

</Challenges>
