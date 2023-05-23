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

সাধারণত ডিজাইন সিস্টেমে, বাটন, ইনপুট এবং এরকম কিছু অন্যান্য নিম্ন-স্তরের কম্পোনেন্টগুলির জন্য তাদের ref তাদের DOM নোডে ফরওয়ার্ড করা একটি সাধারণ প্যাটার্ন। অন্যদিকে, ফরম, তালিকা, বা পেইজের সেকশনের মতো উচ্চ-স্তরের কম্পোনেন্টগুলি সাধারণত DOM নোড প্রকাশ করবে না যাতে DOM স্ট্রাকচারে আকস্মিক নির্ভরতা কমে যায়।

<DeepDive>

#### API এর একাংশ imperative handle এর সাহায্যে উন্মুক্ত করা {/*exposing-a-subset-of-the-api-with-an-imperative-handle*/}

উপরের উদাহরণে, `MyInput` মূল DOM ইনপুট এলিমেন্টটি প্রকাশ করে। এটি প্যারেন্ট কম্পোনেন্টকে এটির উপর `focus()` কল করার সুযোগ দেয়। যদিও, এটি প্যারেন্ট কম্পোনেন্টকে অন্য একটা কাজ সুযোগ করে দেয় - উদাহরণস্বরূপ, এর CSS স্টাইল পরিবর্তন করা। হঠাৎ হঠাৎ, আপনি হয়তো পexposed functionality সীমাবদ্ধ করতে চাইবেন। আপনি এটি `useImperativeHandle` এর সাহায্যে করতে পারেনঃ

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

এখানে, `MyInput` এর মধ্যে `realInputRef` আসল ইনপুট DOM নোডটি ধারণ করে। যদিও, `useImperativeHandle` React-কে বলে যেন প্যারেন্ট কম্পোনেন্টের জন্য ref এর মান হিসাবে আপনার নিজের বিশেষ একটা অবজেক্ট সরবরাহ করবে। তাই `Form` কম্পোনেন্টের ভেতরে `inputRef.current` শুধু মাত্র `focus` মেথডটি আছে। এই ক্ষেত্রে, ref "handle" DOM নোড নয়, বরং `useImperativeHandle` কলের ভেতরে আপনি যে কাস্টম অবজেক্ট তৈরি করেন সেটা।

</DeepDive>

## When React attaches the refs {/*when-react-attaches-the-refs*/}

React এ, প্রতিটি আপডেট [দুটি পর্যায়ে](/learn/render-and-commit#step-3-react-commits-changes-to-the-dom) ভাগ করা হয়ঃ

* **render** এর সময়, React আপনার কম্পোনেন্টগুলি কল করে যেন পর্দায় কী থাকা উচিত তা নির্ধারণ করতে পারে।
* **commit** এর সময়, React DOM এ পরিবর্তনগুলি প্রয়োগ করে।

সাধারণত, আপনি রেন্ডারিং এর সময় ref গুলিতে অ্যাক্সেস করতে [চান না](/learn/referencing-values-with-refs#best-practices-for-refs)। DOM নোডগুলি ধারণ করা ref এর জন্য এটিও প্রযোজ্য। প্রথম রেন্ডারের সময়, DOM নোডগুলি এখনও তৈরি হয়নি, সুতরাং `ref.current` হবে `null`। আর আপডেট রেন্ডারিং এর সময়, DOM নোডগুলি এখনও আপডেট হয়নি। সুতরাং এখনো তাদের read করার সময় হয় নাই।

React কমিটের সময় `ref.current` সেট করে। DOM আপডেট করার আগে, React প্রভাবিত `ref.current` মানগুলিকে `null` সেট করে। DOM আপডেট করার পরে, React তা অবিলম্বে সম্পর্কিত DOM নোডগুলিতে সেট করে।

**সাধারণত, আপনি ইভেন্ট হ্যান্ডলারগুলি থেকে ref গুলি অ্যাক্সেস করবেন।** আপনি যদি কোনও ref এর সাথে কিছু করতে চান, কিন্তু এটি করার জন্য কোনও নির্দিষ্ট ইভেন্ট নেই, আপনার একটি Effect প্রয়োজন হতে পারে। আমরা পরবর্তী সেকশন গুলিতে effects সম্পর্কে আলোচনা করব।

<DeepDive>

#### flushSync ব্যবহার করে সিঙ্ক্রোনাস ভাবে state update flush করা {/*flushing-state-updates-synchronously-with-flush-sync*/}

এরকম কোড বিবেচনা করুন, যা একটি নতুন todo যোগ করে এবং স্ক্রিনটি তালিকার শেষ চাইল্ডের দিকে স্ক্রল করে। লক্ষ্য করুন কিভাবে, কিছু কারণে, এটি সবসময় শেষে যোগ করা todo এর *ঠিক আগের* একটিতে স্ক্রল করেঃ

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

এই দুটি লাইনে সমস্যাটি আছেঃ

```js
setTodos([ ...todos, newTodo]);
listRef.current.lastChild.scrollIntoView();
```

React এ, [state আপডেট কিউ করে রাখা হয়।](/learn/queueing-a-series-of-state-updates) সাধারণত, আপনি এইটাই চাইবেন।  তবে, এখানে এটা সমস্যা করে কারণ `setTodos` DOM কে সাথে সাথে আপডেট করে না। সুতরাং যখন আপনি স্ক্রল করে তালিকের শেষ এলিমেন্টে যাবেন, তখনো todo যোগ করা হয় নাই। এই কারণে স্ক্রলিং সব সময়ে এক আইটেমের হিসেবে "পিছিয়ে থাকে।"

এই সমস্যাটা সমাধানের জন্য, আপনি React কে DOM সিঙ্ক্রোনাসভাবে আপডেট ("flush") করতে বাধ্য করতে পারেন। এটা করার জন্য `react-dom` থেকে `flushSync` ইমপোর্ট করুন এবং **state আপডেটকে ঘিরে ফেলুন** একটা `flushSync` কলের মধ্যেঃ

```js
flushSync(() => {
  setTodos([ ...todos, newTodo]);
});
listRef.current.lastChild.scrollIntoView();
```

এটা React কে নির্দেশ করবে যেন `flushSync` দিয়ে ঘেরা কোড চলার সাথে সাথে সিঙ্ক্রোনাস ভাবে DOM আপডেট হয়ে যায়। ফলাফলস্বরূপ, আপনি যতক্ষণে শেষ TODO তে স্ক্রল করে যাবেন তার আগেই এটা DOM এর মধ্যে থাকবে।

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

## ref ব্যবহার করে DOM ম্যানিপুলেশনের ক্ষেত্রে যা যা মেনে চলা উচিত {/*best-practices-for-dom-manipulation-with-refs*/}

Ref হচ্ছে এক ধরণের escape hatch। আপনার শুধুমাত্র "React এর বাইরে পদক্ষেপ নিতে" হলে এর ব্যবহার করা উচিত। এই ধরনের সাধারণ উদাহরণগুলির মধ্যে রয়েছে ফোকাস করা, স্ক্রোল অবস্থান বা ব্রাউজার API গুলি কল করা যা React প্রকাশ করে না।

আপনি যদি ফোকাসিং এবং স্ক্রোলিং এর মতো অবিনাশী কাজগুলিতে স্থাপিত থাকেন, তবে আপনার কোনও সমস্যা হওয়ার কথা নয়। যদিও, আপনি যদি ম্যানুয়ালি DOM পরিবর্তন করার চেষ্টা করেন, তবে আপনি React এর করা পরিবর্তনের সাথে বিরোধিতা ঘটানোর ঝুঁকি নিতে পারেন।

এই সমস্যাটা ব্যাখ্যা করতে, এই ইদাহরণে একটা ওয়েলকাম মেসেজ এবং দুটি বাটন আছে। প্রথম বাটনটি [conditional rendering](/learn/conditional-rendering) এবং [state](/learn/state-a-components-memory) ব্যবহার করে এর উপস্থিতি বদল করে, যেমন আপনি স্বাভাবিকভাবে React এ করেন। দ্বিতীয় বাটনটি [`remove()` DOM API](https://developer.mozilla.org/en-US/docs/Web/API/Element/remove) ব্যবহার করে একে জোর করে DOM থকে সরিয়ে React এর নিয়ন্ত্রণের বাইরে পাঠিয়ে দেয়। 

কয়েকবার "Toggle with setState" চাপার চেষ্টা করেন। মেসেজটা হারিয়ে যাবার কথা আবার আসার কথা। এরপরে "Remove from the DOM" এ চাপ দিন। এটা একে জোর করে সরিয়ে ফেলবে। সবশেষে,  "Toggle with setState" চাপ দিনঃ

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

আপনি নিজে DOM এলিমেন্ট সরিয়ে ফেলবার পরে, `setState` ব্যবহার করে একে আবার দেখানোর চেষ্টা করলে সিস্টেম ক্র্যাশ করবে। এর কারণ আপনি DOM বদলে ফেলেছেন, এবং React জানে না এর পরে একে কীভাবে পরিচালনা করবে।

**React দিয়ে পরিচালিত DOM নোড পরিবর্তন এড়িয়ে চলবেন।** যেসব এলিমেন্ট React পরিচালনা করে সেগুলোর পরিবর্তন, চাইল্ড যোগ করা বা চাইল্ড বাদ দেয়ার কারণে ভিজ্যুয়াল ফলাফলে অসামাঞ্জস্য দেখা যেতে পারে বা উপরের মত ক্র্যাশ করতে পারে।

তবে, এর মানে এটা না যে আপনি এটা একদমই করতে পারবেন না। এর জন্য দরকার সতর্কতা। **আপনি নিরাপদভাবে DOM এর সেই অংশগুলো পরিবর্তন করতে পারবেন যেগুলো React এর আপডেট করার কোন _কারণ নেই_।** উদাহরণস্বরূপ, যদি JSX এ কোন `<div>` সবসময় খালি থাকে, এর চিলড্রেন তালিকায় হাত দেয়ার কোন কারণ থাকবে না React এর। সুতরাং, সেখানে নিজ থেকে এলিমেন্ট যুক্ত করা বা বাদ দেওয়াটা নিরাপদ। 

<Recap>

- Ref একটি সাধারণ ধারণা, কিন্তু বেশিরভাগ সময় আপনি DOM এলিমেন্ট হোল্ড করার জন্য এর ব্যবহার করবেন।
- আপনি `<div ref={myRef}>` পাস করার মাধ্যমে React কে নির্দেশ করবেন যেন `myRef.current` এর মধ্যে একটি DOM নোড ঢুকানো হয়। 
- সাধারণত, আপনি ref ব্যবহার করবেন এমন কাজ করার জন্য যা ধ্বংসাত্মক না। যেমন ফোকাস করা, স্ক্রল করা বা DOM এলিমেন্টের measure করা।
- একটা কম্পোনেন্ট এর DOM নোড স্বাভাবিকভাবে উন্মুক্ত করে না। আপনি `forwardRef` ব্যবহার করার মাধ্যমে এবং একটা নির্দিষ্ট নোডে দ্বিতীয় `ref` আর্গুমেন্ট পাঠিয়ে দেবার মাধ্যমে DOM নোড উন্মুক্ত করার সিদ্ধান্ত নিতে পারেন।
- React পরিচালনা করে এমন DOM নোড পরিবর্তন করা এড়িয়ে চলুন।
- আপনি যদি React এর পরিচালিত DOM নোড পরিবর্তন করে, তাহলে এমন অংশে পরিবর্তনটা আনেন যেটা React এর আপডেট করার কোন কারণ নেই। 

</Recap>



<Challenges>

#### ভিডিও চালু এবং বন্ধ করুন {/*play-and-pause-the-video*/}

এই উদাহরণে, ভিডিও চালু এবং বন্ধ থাকা অবস্থার সুইচ করার জন্য বাটন একটি state ভ্যারিয়েবল পরিবর্তন করে। তবে, ভিডিও আসলেই বন্ধ বা চালু করার জন্য, state ভ্যারিয়েবল পরিবর্তন করা যতেষ্ট না। আপনাকে `<video>` এর DOM এলিমেন্টে [`play()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play) এবং [`pause()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause) কল করতে হবে। এর সাথে একটি ref যোগ করুন, এবং নিশ্চিত করুন যে বাটনটা কাজ করছে।

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

একটা অতিরিক্ত চ্যালেঞ্জের জন্য, যদি ব্যবহারকারী ভিডিওতে রাইট ক্লিক করে বিল্ট-ইন ব্রাউজার মিডিয়া কন্ট্রোল ব্যবহার করে ভিডিও চালু করে দেয়, সেটার সাথে "Play" বাটন sync রাখুন। আপনি ভিডিওর `onPlay` এবং `onPause` listen করতে চাইতে পারেন।

<Solution>

একটা ref ডিক্লেয়ার করুন এবং তাকে `<video>` এলিমেন্টে রাখুন। তারপর পরবর্তী state এর উপর নির্ভর করে ইভেন্ট হ্যান্ডলারে `ref.current.play()` এবং `ref.current.pause()` কল করুন।

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

ব্রাউজারের বিল্ট-ইন কন্ট্রোল ব্যবহার করতে, আপনি  `<video>` এলিমেন্টে `onPlay` এবং `onPause` যোগ করতে পারেন এবং সেখান থেকে  `setIsPlaying` কল করতে পারেন। এই ভাবে, যদি ব্যবহারকারী ব্রাউজারের কন্ট্রোল ব্যবহার করে, state যথাযথভাবে নিজেকে বদলে নেবে।

</Solution>

#### সার্চ ফিল্ডে ফোকাস করুন {/*focus-the-search-field*/}

একে এমন ভাবে বানান যেন "Search" বাটনে ক্লিক করা হলে ফিল্ডটা ফোকাস হয়।

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

ইনপুটে একটা ref যোগ করুন, এবং একে ফোকাস করতে DOM নোডে `focus()` কল করুন।

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

#### একটি ছবির ক্যারোসেলে স্ক্রল করা {/*scrolling-an-image-carousel*/}

এই ছবির ক্যারোসেলের একটা "Next" বাটন আছে যেটা সক্রিয় ছবিটাকে বদলে ফেলে। নিশ্চিত করুন যেন ক্লিক করলে গ্যালারি উপর নিচে স্ক্রল করে সক্রিয় ছবিটায় চলে যায়। আপনি সক্রিয় ছবির DOM নোডে [`scrollIntoView()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) কল করতে চাইবেন।

```js
node.scrollIntoView({
  behavior: 'smooth',
  block: 'nearest',
  inline: 'center'
});
```

<Hint>

এই অনুশীলনের জন্য প্রতিটি ছবিতে একটা ref এর দরকার নাই। বর্তমানে সক্রিয় যে ছবি তার, অথবা তালিকার নিজের একটা ref থাকাই যথেষ্ট। আপনি স্ক্রল করার *আগে* যেন DOM আপডেটেড হয়ে যায় সেটা নিশ্চিত করতে `flushSync` ব্যবহার করুন।

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

আপনি একটি `selectedRef` ঘোষণা দিতে পারেন, এবং শর্ত দিয়ে শুধুমাত্র বর্তমান ছবিতে একে পাস করে দিতে পারেন।

```js
<li ref={index === i ? selectedRef : null}>
```

যখন `index === i`, অর্থাৎ এই ছবিটাই নির্বাচিত ছবি, `<li>` `selectedRef` রিসিভ করবে। React নিশ্চিত করবে যে `selectedRef.current` সবসময় সঠিক DOM নোড নির্দেশ করছে। 

লক্ষ্য করুন স্ক্রল এর আগে DOM নোড আপডেট করার জন্য React-কে বাধ্য করতে `flushSync` কলটা দরকার পড়ে। নাহলে, `selectedRef.current` সবসময়ই আগে থেকে নির্বাচিত আইটেমটাতেই নির্দেশ করবে। 

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

এমনভাবে একে বানান যেন "Search" বাটনে ক্লিক করা হলে ফিল্ডটা ফোকাস হয়। লক্ষ্য করবেন প্রতিটা কম্পোনেন্ট একটা পৃথক ফাইলে ডিফাইন করা আছে এবং সেখান থেকে তাদের বের করা উচিত না। সেগুলোকে আপনি কীভাবে একসাথে যুক্ত করবেন?

<Hint>

`SearchInput` এর মত, আপনার নিজের কোন কম্পোনেন্ট থেকে একটি DOM নোড উন্মুক্ত করার জন্য আপনার প্রয়োজন হবে `forwardRef`

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

আপনাকে `SearchButton`এ একটা `onClick` প্রপ যোগ করতে হবে, এবং নিশ্চিত করতে হবে যেন `SearchButton` একে ব্রাউজার `<button>` পর্যন্ত পাস করে নিয়ে যায়। আপনি `<SearchInput>` পর্যন্ত একটি ref ও নিয়ে যাবেন, যা একে আসল `<input>` পর্যন্ত ফরোয়ার্ড করবে এবং populate করবে। সবশেষে, ক্লিক হ্যান্ডলারে, আপনি ref এর মধ্যে থাকা DOM নোডে `focus` কল করবেন।

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
