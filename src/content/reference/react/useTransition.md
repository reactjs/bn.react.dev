---
title: useTransition
---

<Intro>

`useTransition` হলো একটি React হুক যা আপনাকে UI ব্লক না করেই স্টেট আপডেট করতে দেয়।

```js
const [isPending, startTransition] = useTransition()
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `useTransition()` {/*usetransition*/}

আপনার কম্পোনেন্টের একেবারে উপরের স্তরে `useTransition` কল করুন যাতে কিছু state আপডেটকে ট্রানজিশন হিসাবে চিহ্নিত করা যায়।

```js
import { useTransition } from 'react';

function TabContainer() {
  const [isPending, startTransition] = useTransition();
  // ...
}
```

[নীচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটারস {/*parameters*/}

`useTransition` কোনো প্যারামিটার নেয় না।

#### রিটার্নস {/*returns*/}

`useTransition` একটি অ্যারে রিটার্ন করে যাতে ঠিক দুটি আইটেম থাকে:

১. `isPending` ফ্ল্যাগ যা আপনাকে জানায় যে একটি পেন্ডিং ট্রানজিশন আছে।
২. [`startTransition` ফাংশনটি](#starttransition) যা আপনাকে একটি স্টেট আপডেটকে ট্রানজিশন হিসাবে চিহ্নিত করতে দেয়।

---

### `startTransition` ফাংশন {/*starttransition*/}

`useTransition` দ্বারা রিটার্ন করা `startTransition` ফাংশনটি আপনাকে একটি state আপডেটকে ট্রানজিশন হিসাবে চিহ্নিত করতে দেয়।

```js {6,8}
function TabContainer() {
  const [isPending, startTransition] = useTransition();
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
  // ...
}
```

#### প্যারামিটারসমূহ {/*starttransition-parameters*/}

* `scope`: একটি ফাংশন যা এক বা একাধিক [`set` ফাংশন](/reference/react/useState#setstate) কল করে কিছু state আপডেট করে। React কোনো প্রকার দেরি না করে `scope` কে কোনো প্যারামিটার ছাড়াই কল করে এবং `scope` ফাংশন কলের সময় নির্ধারিত সব state আপডেটকে ট্রানজিশন হিসাবে চিহ্নিত করে। এগুলি কোনো কিছু দ্বারা [বাধাপ্রাপ্ত হবে না বা non-blocking](#marking-a-state-update-as-a-non-blocking-transition) হবে এবং [অনাকাঙ্ক্ষিত লোডিং ইন্ডিকেটর প্রদর্শন করবে না।](#preventing-unwanted-loading-indicators)

#### রিটার্নস {/*starttransition-returns*/}

`startTransition` কিছু রিটার্ন করে না।

#### সতর্কতা {/*starttransition-caveats*/}

* `useTransition` হল একটি হুক, তাই এটি কেবল কম্পোনেন্ট বা কাস্টম হুকের মধ্যে কল করা যেতে পারে। যদি অন্য কোনো স্থানে (যেমন, একটি ডেটা লাইব্রেরি থেকে) ট্রানজিশন শুরু করার প্রয়োজন হয়, তাহলে স্বতন্ত্র [`startTransition`](/reference/react/startTransition) কল করুন।

* যদি আপনি একটি স্টেটের `সেট` ফাংশনে অ্যাক্সেস পেয়ে থাকেন তবে আপনি একটি ট্রানজিশনে আপডেট  wrap করতে পারেন। কোনো প্রপ বা কাস্টম হুক ভ্যালুর রেসপন্সে ট্রানজিশন শুরু করতে চাইলে, [`useDeferredValue`](/reference/react/useDeferredValue) ব্যবহার করার চেষ্টা করুন।

* `startTransition` এ আপনি যে ফাংশন পাস করবেন তা অবশ্যই একই সময়ে হতে হবে। React এই ফাংশনটি তাৎক্ষণিকভাবে চালায়, এটি চালানোর সময় ঘটে যাওয়া সমস্ত state আপডেটকে ট্রানজিশন হিসাবে চিহ্নিত করে। যদি আপনি পরে আরো state আপডেট করার চেষ্টা করেন (উদাহরণস্বরূপ, একটি timeout এ), তবে সেগুলি ট্রানজিশন হিসাবে চিহ্নিত হবে না।

<<<<<<< HEAD
* একটি state আপডেট যদি ট্রানজিশন হিসাবে চিহ্নিত হয়, তাহলে অন্যান্য state আপডেট দ্বারা তা বাধাগ্রস্ত হবে। উদাহরণস্বরূপ, যদি আপনি একটি চার্ট কম্পোনেন্টে একটি ট্রানজিশনের মধ্যে আপডেট করেন, কিন্তু তারপর চার্টটি পুনরায় রেন্ডার হওয়ার মাঝখানে একটি input-এ টাইপ শুরু করেন, React ইনপুট আপডেট সম্পর্কিত কাজ সম্পন্ন করার পরে চার্ট কম্পোনেন্টে রেন্ডারিং কাজটি পুনরায় শুরু করবে।
=======
* The `startTransition` function has a stable identity, so you will often see it omitted from Effect dependencies, but including it will not cause the Effect to fire. If the linter lets you omit a dependency without errors, it is safe to do. [Learn more about removing Effect dependencies.](/learn/removing-effect-dependencies#move-dynamic-objects-and-functions-inside-your-effect)

* A state update marked as a Transition will be interrupted by other state updates. For example, if you update a chart component inside a Transition, but then start typing into an input while the chart is in the middle of a re-render, React will restart the rendering work on the chart component after handling the input update.
>>>>>>> eb174dd932613fb0784a78ee2d9360554538cc08

* Transition আপডেটগুলি টেক্সট ইনপুটগুলি নিয়ন্ত্রণের জন্য ব্যবহৃত হতে পারে না।

* যদি একাধিক চলমান transitions থাকে, React বর্তমানে তাদেরকে একসাথে ব্যাচ করে। এটি একটি সীমাবদ্ধতা যা সম্ভবত ভবিষ্যতের কোনো রিলিজে সরানো হবে।

---

## ব্যবহারবিধি {/*usage*/}

### একটি state আপডেটকে নন-ব্লকিং ট্রানজিশন হিসাবে চিহ্নিত করা {/*marking-a-state-update-as-a-non-blocking-transition*/}

কম্পোনেন্টের একেবারে উপরে `useTransition` কল করুন যাতে state আপডেটগুলি কোনো প্রকার বাধা ছাড়াই *ট্রানজিশন* হিসাবে চিহ্নিত করা যায়।

```js [[1, 4, "isPending"], [2, 4, "startTransition"]]
import { useState, useTransition } from 'react';

function TabContainer() {
  const [isPending, startTransition] = useTransition();
  // ...
}
```

`useTransition` ঠিক দুটি আইটেম সহ একটি array রিটার্ন করে:

১. <CodeStep step={1}>`isPending` ফ্ল্যাগ</CodeStep> যা আপনাকে জানায় যে একটি পেন্ডিং ট্রানজিশন রয়েছে।
২. <CodeStep step={2}>`startTransition` ফাংশন</CodeStep> যা আপনাকে একটি state আপডেটকে ট্রানজিশন হিসাবে চিহ্নিত করতে দেয়।

আপনি তখন একটি state আপডেটকে এইরকম একটি ট্রানজিশন হিসাবে চিহ্নিত করতে পারেন:

```js {6,8}
function TabContainer() {
  const [isPending, startTransition] = useTransition();
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
  // ...
}
```

ট্রানজিশন আপনাকে ইউজার ইন্টারফেস আপডেটগুলিকে এমনকি ধীরগতির ডিভাইসেও রেস্পন্সিভ রাখতে দেয়।

একটি ট্রানজিশনের সাথে, আপনার UI রি-রেন্ডারের মাঝখানে রেসপন্সিভ থাকে। উদাহরণস্বরূপ, যদি ব্যবহারকারী একটি ট্যাবে ক্লিক করে কিন্তু তারপর তাদের মন পরিবর্তন করে এবং অন্য ট্যাবে ক্লিক করে, তারা প্রথম রি-রেন্ডার শেষ হওয়ার জন্য অপেক্ষা না করে এটি করতে পারে।

<Recipes titleText="useTransition এবং রেগুলার state আপডেটের মধ্যে পার্থক্য" titleId="examples">

#### ট্রানজিশনের মাধ্যমে বর্তমান ট্যাবটি আপডেট করা হচ্ছে {/*updating-the-current-tab-in-a-transition*/}

এই উদাহরণে, "Posts" ট্যাবটি **কৃত্রিমভাবে ধীর করা হয়েছে** যাতে এটি রেন্ডার করতে কমপক্ষে এক সেকেন্ড সময় নেয়।

"Posts" এ ক্লিক করুন এবং তারপর তাৎক্ষণিকভাবে "Contact" এ ক্লিক করুন। লক্ষ্য করুন যে এটি "Posts" এর ধীরগতির রেন্ডারকে বাধা দেয়। "Contact" ট্যাবটি তাৎক্ষণিকভাবে দেখায়। কারণ এই state আপডেটটি একটি transition হিসেবে চিহ্নিত হয়েছে, একটি ধীরগতির রি-রেন্ডার ইউজার ইন্টারফেসকে স্থির রাখতে পারেনি।

<Sandpack>

```js
import { useState, useTransition } from 'react';
import TabButton from './TabButton.js';
import AboutTab from './AboutTab.js';
import PostsTab from './PostsTab.js';
import ContactTab from './ContactTab.js';

export default function TabContainer() {
  const [isPending, startTransition] = useTransition();
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }

  return (
    <>
      <TabButton
        isActive={tab === 'about'}
        onClick={() => selectTab('about')}
      >
        About
      </TabButton>
      <TabButton
        isActive={tab === 'posts'}
        onClick={() => selectTab('posts')}
      >
        Posts (slow)
      </TabButton>
      <TabButton
        isActive={tab === 'contact'}
        onClick={() => selectTab('contact')}
      >
        Contact
      </TabButton>
      <hr />
      {tab === 'about' && <AboutTab />}
      {tab === 'posts' && <PostsTab />}
      {tab === 'contact' && <ContactTab />}
    </>
  );
}
```

```js src/TabButton.js
import { useTransition } from 'react';

export default function TabButton({ children, isActive, onClick }) {
  if (isActive) {
    return <b>{children}</b>
  }
  return (
    <button onClick={() => {
      onClick();
    }}>
      {children}
    </button>
  )
}

```

```js src/AboutTab.js
export default function AboutTab() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/PostsTab.js
import { memo } from 'react';

const PostsTab = memo(function PostsTab() {
  // Log once. The actual slowdown is inside SlowPost.
  console.log('[ARTIFICIALLY SLOW] Rendering 500 <SlowPost />');

  let items = [];
  for (let i = 0; i < 500; i++) {
    items.push(<SlowPost key={i} index={i} />);
  }
  return (
    <ul className="items">
      {items}
    </ul>
  );
});

function SlowPost({ index }) {
  let startTime = performance.now();
  while (performance.now() - startTime < 1) {
    // Do nothing for 1 ms per item to emulate extremely slow code
  }

  return (
    <li className="item">
      Post #{index + 1}
    </li>
  );
}

export default PostsTab;
```

```js src/ContactTab.js
export default function ContactTab() {
  return (
    <>
      <p>
        You can find me online here:
      </p>
      <ul>
        <li>admin@mysite.com</li>
        <li>+123456789</li>
      </ul>
    </>
  );
}
```

```css
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
```

</Sandpack>

<Solution />

#### ট্রানজিশন ছাড়াই বর্তমান ট্যাবটি আপডেট করা হচ্ছে {/*updating-the-current-tab-without-a-transition*/}

এই উদাহরণে, "Posts" ট্যাবটি **কৃত্রিমভাবে ধীর করা হয়েছে** যাতে এটি রেন্ডার হতে অন্তত এক সেকেন্ড সময় নেয়। আগের উদাহরণের মতো, এই state আপডেটটি কোনো **ট্রানজিশন নয়।**

"Posts" এ ক্লিক করুন এবং তারপর তাৎক্ষণিকভাবে "Contact" এ ক্লিক করুন। লক্ষ্য করুন যে অ্যাপটি ধীরগতির ট্যাব রেন্ডার করার সময় স্থির থাকে এবং ইউআই সাড়া দেয় না। এই state আপডেটটি কোনো ট্রানজিশন নয়, তাই ধীর রি-রেন্ডারের কারণে ইউজার ইন্টারফেস স্থির থাকে।

<Sandpack>

```js
import { useState } from 'react';
import TabButton from './TabButton.js';
import AboutTab from './AboutTab.js';
import PostsTab from './PostsTab.js';
import ContactTab from './ContactTab.js';

export default function TabContainer() {
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    setTab(nextTab);
  }

  return (
    <>
      <TabButton
        isActive={tab === 'about'}
        onClick={() => selectTab('about')}
      >
        About
      </TabButton>
      <TabButton
        isActive={tab === 'posts'}
        onClick={() => selectTab('posts')}
      >
        Posts (slow)
      </TabButton>
      <TabButton
        isActive={tab === 'contact'}
        onClick={() => selectTab('contact')}
      >
        Contact
      </TabButton>
      <hr />
      {tab === 'about' && <AboutTab />}
      {tab === 'posts' && <PostsTab />}
      {tab === 'contact' && <ContactTab />}
    </>
  );
}
```

```js src/TabButton.js
import { useTransition } from 'react';

export default function TabButton({ children, isActive, onClick }) {
  if (isActive) {
    return <b>{children}</b>
  }
  return (
    <button onClick={() => {
      onClick();
    }}>
      {children}
    </button>
  )
}

```

```js src/AboutTab.js
export default function AboutTab() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/PostsTab.js
import { memo } from 'react';

const PostsTab = memo(function PostsTab() {
  // Log once. The actual slowdown is inside SlowPost.
  console.log('[ARTIFICIALLY SLOW] Rendering 500 <SlowPost />');

  let items = [];
  for (let i = 0; i < 500; i++) {
    items.push(<SlowPost key={i} index={i} />);
  }
  return (
    <ul className="items">
      {items}
    </ul>
  );
});

function SlowPost({ index }) {
  let startTime = performance.now();
  while (performance.now() - startTime < 1) {
    // Do nothing for 1 ms per item to emulate extremely slow code
  }

  return (
    <li className="item">
      Post #{index + 1}
    </li>
  );
}

export default PostsTab;
```

```js src/ContactTab.js
export default function ContactTab() {
  return (
    <>
      <p>
        You can find me online here:
      </p>
      <ul>
        <li>admin@mysite.com</li>
        <li>+123456789</li>
      </ul>
    </>
  );
}
```

```css
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
```

</Sandpack>

<Solution />

</Recipes>

---

### ট্রানজিশনে প্যারেন্ট কম্পোনেন্ট আপডেট করা {/*updating-the-parent-component-in-a-transition*/}

আপনি `useTransition` কল থেকে একটি প্যারেন্ট কম্পোনেন্টের state আপডেট করতে পারেন। উদাহরণস্বরূপ, এই `TabButton` কম্পোনেন্টটি এর `onClick` লজিককে একটি ট্রানজিশনে রাখে:

```js {8-10}
export default function TabButton({ children, isActive, onClick }) {
  const [isPending, startTransition] = useTransition();
  if (isActive) {
    return <b>{children}</b>
  }
  return (
    <button onClick={() => {
      startTransition(() => {
        onClick();
      });
    }}>
      {children}
    </button>
  );
}
```

কারণ প্যারেন্ট কম্পোনেন্ট তার state আপডেট করে `onClick` ইভেন্ট হ্যান্ডলারের মধ্যে, সেই state আপডেটটি একটি ট্রানজিশন হিসাবে বিবেচিত হয়। এই কারণে, আগের উদাহরণের মতো, আপনি "Posts" এ ক্লিক করতে পারেন এবং তারপর অবিলম্বে "Contact" এ ক্লিক করতে পারেন। নির্বাচিত ট্যাব আপডেট করা একটি ট্রানজিশন হিসাবে বিবেচিত হয়, তাই এটি ইউজার ইন্টারঅ্যাকশনগুলি বাধা দেয় না।

<Sandpack>

```js
import { useState } from 'react';
import TabButton from './TabButton.js';
import AboutTab from './AboutTab.js';
import PostsTab from './PostsTab.js';
import ContactTab from './ContactTab.js';

export default function TabContainer() {
  const [tab, setTab] = useState('about');
  return (
    <>
      <TabButton
        isActive={tab === 'about'}
        onClick={() => setTab('about')}
      >
        About
      </TabButton>
      <TabButton
        isActive={tab === 'posts'}
        onClick={() => setTab('posts')}
      >
        Posts (slow)
      </TabButton>
      <TabButton
        isActive={tab === 'contact'}
        onClick={() => setTab('contact')}
      >
        Contact
      </TabButton>
      <hr />
      {tab === 'about' && <AboutTab />}
      {tab === 'posts' && <PostsTab />}
      {tab === 'contact' && <ContactTab />}
    </>
  );
}
```

```js src/TabButton.js active
import { useTransition } from 'react';

export default function TabButton({ children, isActive, onClick }) {
  const [isPending, startTransition] = useTransition();
  if (isActive) {
    return <b>{children}</b>
  }
  return (
    <button onClick={() => {
      startTransition(() => {
        onClick();
      });
    }}>
      {children}
    </button>
  );
}
```

```js src/AboutTab.js
export default function AboutTab() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/PostsTab.js
import { memo } from 'react';

const PostsTab = memo(function PostsTab() {
  // Log once. The actual slowdown is inside SlowPost.
  console.log('[ARTIFICIALLY SLOW] Rendering 500 <SlowPost />');

  let items = [];
  for (let i = 0; i < 500; i++) {
    items.push(<SlowPost key={i} index={i} />);
  }
  return (
    <ul className="items">
      {items}
    </ul>
  );
});

function SlowPost({ index }) {
  let startTime = performance.now();
  while (performance.now() - startTime < 1) {
    // Do nothing for 1 ms per item to emulate extremely slow code
  }

  return (
    <li className="item">
      Post #{index + 1}
    </li>
  );
}

export default PostsTab;
```

```js src/ContactTab.js
export default function ContactTab() {
  return (
    <>
      <p>
        You can find me online here:
      </p>
      <ul>
        <li>admin@mysite.com</li>
        <li>+123456789</li>
      </ul>
    </>
  );
}
```

```css
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
```

</Sandpack>

---

### ট্রানজিশনের সময় পেন্ডিং ভিজ্যুয়াল state প্রদর্শন করা {/*displaying-a-pending-visual-state-during-the-transition*/}

আপনি `useTransition` হতে রিটার্ন আসা `isPending` বুলিয়ান মান ব্যবহার করে ব্যবহারকারীকে জানাতে পারেন যে একটি ট্রানজিশন চলছে। উদাহরণস্বরূপ, ট্যাব বোতামটি একটি বিশেষ "pending" ভিজ্যুয়াল state থাকতে পারে:

```js {4-6}
function TabButton({ children, isActive, onClick }) {
  const [isPending, startTransition] = useTransition();
  // ...
  if (isPending) {
    return <b className="pending">{children}</b>;
  }
  // ...
```

লক্ষ্য করুন যে "Posts" ক্লিক করা এখন কিভাবে আরও প্রতিক্রিয়াশীল মনে হয় কারণ ট্যাব বাটনটি নিজেই তাৎক্ষণিকভাবে আপডেট হয়:

<Sandpack>

```js
import { useState } from 'react';
import TabButton from './TabButton.js';
import AboutTab from './AboutTab.js';
import PostsTab from './PostsTab.js';
import ContactTab from './ContactTab.js';

export default function TabContainer() {
  const [tab, setTab] = useState('about');
  return (
    <>
      <TabButton
        isActive={tab === 'about'}
        onClick={() => setTab('about')}
      >
        About
      </TabButton>
      <TabButton
        isActive={tab === 'posts'}
        onClick={() => setTab('posts')}
      >
        Posts (slow)
      </TabButton>
      <TabButton
        isActive={tab === 'contact'}
        onClick={() => setTab('contact')}
      >
        Contact
      </TabButton>
      <hr />
      {tab === 'about' && <AboutTab />}
      {tab === 'posts' && <PostsTab />}
      {tab === 'contact' && <ContactTab />}
    </>
  );
}
```

```js src/TabButton.js active
import { useTransition } from 'react';

export default function TabButton({ children, isActive, onClick }) {
  const [isPending, startTransition] = useTransition();
  if (isActive) {
    return <b>{children}</b>
  }
  if (isPending) {
    return <b className="pending">{children}</b>;
  }
  return (
    <button onClick={() => {
      startTransition(() => {
        onClick();
      });
    }}>
      {children}
    </button>
  );
}
```

```js src/AboutTab.js
export default function AboutTab() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/PostsTab.js
import { memo } from 'react';

const PostsTab = memo(function PostsTab() {
  // Log once. The actual slowdown is inside SlowPost.
  console.log('[ARTIFICIALLY SLOW] Rendering 500 <SlowPost />');

  let items = [];
  for (let i = 0; i < 500; i++) {
    items.push(<SlowPost key={i} index={i} />);
  }
  return (
    <ul className="items">
      {items}
    </ul>
  );
});

function SlowPost({ index }) {
  let startTime = performance.now();
  while (performance.now() - startTime < 1) {
    // Do nothing for 1 ms per item to emulate extremely slow code
  }

  return (
    <li className="item">
      Post #{index + 1}
    </li>
  );
}

export default PostsTab;
```

```js src/ContactTab.js
export default function ContactTab() {
  return (
    <>
      <p>
        You can find me online here:
      </p>
      <ul>
        <li>admin@mysite.com</li>
        <li>+123456789</li>
      </ul>
    </>
  );
}
```

```css
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
```

</Sandpack>

---

### অনাকাঙ্ক্ষিত লোডিং ইন্ডিকেটরগুলি প্রতিরোধ করা {/*preventing-unwanted-loading-indicators*/}

এই উদাহরণে, `PostsTab` কম্পোনেন্টটি একটি [Suspense-enabled](/reference/react/Suspense) ডেটা সোর্স ব্যবহার করে কিছু ডেটা আনয়ন করে। যখন আপনি "Posts" ট্যাবে ক্লিক করেন, তখন `PostsTab` কম্পোনেন্টটি *সাসপেন্ড* হয়, যা কাছাকাছি লোডিং ফলব্যাক প্রদর্শন করে:

<Sandpack>

```js
import { Suspense, useState } from 'react';
import TabButton from './TabButton.js';
import AboutTab from './AboutTab.js';
import PostsTab from './PostsTab.js';
import ContactTab from './ContactTab.js';

export default function TabContainer() {
  const [tab, setTab] = useState('about');
  return (
    <Suspense fallback={<h1>🌀 Loading...</h1>}>
      <TabButton
        isActive={tab === 'about'}
        onClick={() => setTab('about')}
      >
        About
      </TabButton>
      <TabButton
        isActive={tab === 'posts'}
        onClick={() => setTab('posts')}
      >
        Posts
      </TabButton>
      <TabButton
        isActive={tab === 'contact'}
        onClick={() => setTab('contact')}
      >
        Contact
      </TabButton>
      <hr />
      {tab === 'about' && <AboutTab />}
      {tab === 'posts' && <PostsTab />}
      {tab === 'contact' && <ContactTab />}
    </Suspense>
  );
}
```

```js src/TabButton.js
export default function TabButton({ children, isActive, onClick }) {
  if (isActive) {
    return <b>{children}</b>
  }
  return (
    <button onClick={() => {
      onClick();
    }}>
      {children}
    </button>
  );
}
```

```js src/AboutTab.js hidden
export default function AboutTab() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/PostsTab.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

function PostsTab() {
  const posts = use(fetchData('/posts'));
  return (
    <ul className="items">
      {posts.map(post =>
        <Post key={post.id} title={post.title} />
      )}
    </ul>
  );
}

function Post({ title }) {
  return (
    <li className="item">
      {title}
    </li>
  );
}

export default PostsTab;

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },
    );
    throw promise;
  }
}
```

```js src/ContactTab.js hidden
export default function ContactTab() {
  return (
    <>
      <p>
        You can find me online here:
      </p>
      <ul>
        <li>admin@mysite.com</li>
        <li>+123456789</li>
      </ul>
    </>
  );
}
```


```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url.startsWith('/posts')) {
    return await getPosts();
  } else {
    throw Error('Not implemented');
  }
}

async function getPosts() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 1000);
  });
  let posts = [];
  for (let i = 0; i < 500; i++) {
    posts.push({
      id: i,
      title: 'Post #' + (i + 1)
    });
  }
  return posts;
}
```

```css
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
```

</Sandpack>

পুরো ট্যাব কন্টেইনার লুকিয়ে একটি লোডিং ইন্ডিকেটর দেখানো ব্যবহারকারীর জন্য অস্বস্তিকর অভিজ্ঞতা তৈরি করে। যদি আপনি `TabButton` এ `useTransition` যোগ করেন, তাহলে আপনি ট্যাব বোতামে পেন্ডিং state দেখানোর জন্য নির্দেশ করতে পারেন।

লক্ষ্য করুন যে "Posts" ক্লিক করলে আর পুরো ট্যাব কন্টেইনার একটি স্পিনার দিয়ে প্রতিস্থাপিত হয় না:

<Sandpack>

```js
import { Suspense, useState } from 'react';
import TabButton from './TabButton.js';
import AboutTab from './AboutTab.js';
import PostsTab from './PostsTab.js';
import ContactTab from './ContactTab.js';

export default function TabContainer() {
  const [tab, setTab] = useState('about');
  return (
    <Suspense fallback={<h1>🌀 Loading...</h1>}>
      <TabButton
        isActive={tab === 'about'}
        onClick={() => setTab('about')}
      >
        About
      </TabButton>
      <TabButton
        isActive={tab === 'posts'}
        onClick={() => setTab('posts')}
      >
        Posts
      </TabButton>
      <TabButton
        isActive={tab === 'contact'}
        onClick={() => setTab('contact')}
      >
        Contact
      </TabButton>
      <hr />
      {tab === 'about' && <AboutTab />}
      {tab === 'posts' && <PostsTab />}
      {tab === 'contact' && <ContactTab />}
    </Suspense>
  );
}
```

```js src/TabButton.js active
import { useTransition } from 'react';

export default function TabButton({ children, isActive, onClick }) {
  const [isPending, startTransition] = useTransition();
  if (isActive) {
    return <b>{children}</b>
  }
  if (isPending) {
    return <b className="pending">{children}</b>;
  }
  return (
    <button onClick={() => {
      startTransition(() => {
        onClick();
      });
    }}>
      {children}
    </button>
  );
}
```

```js src/AboutTab.js hidden
export default function AboutTab() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/PostsTab.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

function PostsTab() {
  const posts = use(fetchData('/posts'));
  return (
    <ul className="items">
      {posts.map(post =>
        <Post key={post.id} title={post.title} />
      )}
    </ul>
  );
}

function Post({ title }) {
  return (
    <li className="item">
      {title}
    </li>
  );
}

export default PostsTab;

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },
    );
    throw promise;
  }
}
```

```js src/ContactTab.js hidden
export default function ContactTab() {
  return (
    <>
      <p>
        You can find me online here:
      </p>
      <ul>
        <li>admin@mysite.com</li>
        <li>+123456789</li>
      </ul>
    </>
  );
}
```


```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url.startsWith('/posts')) {
    return await getPosts();
  } else {
    throw Error('Not implemented');
  }
}

async function getPosts() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 1000);
  });
  let posts = [];
  for (let i = 0; i < 500; i++) {
    posts.push({
      id: i,
      title: 'Post #' + (i + 1)
    });
  }
  return posts;
}
```

```css
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
```

</Sandpack>

[Suspense এর সাথে transitions ব্যবহার সম্পর্কে আরও পড়ুন।](/reference/react/Suspense#preventing-already-revealed-content-from-hiding)

<Note>

ট্রানজিশনগুলি কেবল *ইতিমধ্যে প্রকাশিত* কন্টেন্ট (যেমন ট্যাব কন্টেইনার) লুকানো এড়াতে যথেষ্ট দীর্ঘ সময় "অপেক্ষা" করবে। যদি Posts ট্যাবের মধ্যে একটি [নেস্টেড `<Suspense>` সীমানা](/reference/react/Suspense#revealing-nested-content-as-it-loads) থাকত, তবে ট্রানজিশনটি এর জন্য "অপেক্ষা" করত না।

</Note>

---

### Suspense-সংবলিত রাউটার তৈরি করা {/*building-a-suspense-enabled-router*/}

যদি আপনি একটি React ফ্রেমওয়ার্ক অথবা রাউটার তৈরি করেন, আমরা পরামর্শ দিই আপনি আপনার নেভিগেশনগুলিকে ট্রানজিশন হিসেবে চিহ্নিত করুন।

```js {3,6,8}
function Router() {
  const [page, setPage] = useState('/');
  const [isPending, startTransition] = useTransition();

  function navigate(url) {
    startTransition(() => {
      setPage(url);
    });
  }
  // ...
```

এটি দুটি কারণে পরামর্শ দেয়া হয়:

- [ট্রানজিশনগুলি বাধাগ্রস্ত হতে পারে,](#marking-a-state-update-as-a-non-blocking-transition) যা ইউজারকে রি-রেন্ডার সম্পূর্ণ হওয়ার অপেক্ষা না করে অন্য কিছুতে ক্লিক করতে দেয়।
- [ট্রানজিশনগুলি অনাকাঙ্ক্ষিত লোডিং ইন্ডিকেটরগুলি প্রতিরোধ করে,](#preventing-unwanted-loading-indicators) যা ইউজারকে নেভিগেশনে বিভ্রান্তিকর লাফ এড়াতে সাহায্য করে।

এখানে নেভিগেশনের জন্য ট্রানজিশন ব্যবহার করে একটি খুব সহজ রাউটার উদাহরণ দেওয়া হল।

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental"
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
import { Suspense, useState, useTransition } from 'react';
import IndexPage from './IndexPage.js';
import ArtistPage from './ArtistPage.js';
import Layout from './Layout.js';

export default function App() {
  return (
    <Suspense fallback={<BigSpinner />}>
      <Router />
    </Suspense>
  );
}

function Router() {
  const [page, setPage] = useState('/');
  const [isPending, startTransition] = useTransition();

  function navigate(url) {
    startTransition(() => {
      setPage(url);
    });
  }

  let content;
  if (page === '/') {
    content = (
      <IndexPage navigate={navigate} />
    );
  } else if (page === '/the-beatles') {
    content = (
      <ArtistPage
        artist={{
          id: 'the-beatles',
          name: 'The Beatles',
        }}
      />
    );
  }
  return (
    <Layout isPending={isPending}>
      {content}
    </Layout>
  );
}

function BigSpinner() {
  return <h2>🌀 Loading...</h2>;
}
```

```js src/Layout.js
export default function Layout({ children, isPending }) {
  return (
    <div className="layout">
      <section className="header" style={{
        opacity: isPending ? 0.7 : 1
      }}>
        Music Browser
      </section>
      <main>
        {children}
      </main>
    </div>
  );
}
```

```js src/IndexPage.js
export default function IndexPage({ navigate }) {
  return (
    <button onClick={() => navigate('/the-beatles')}>
      Open The Beatles artist page
    </button>
  );
}
```

```js src/ArtistPage.js
import { Suspense } from 'react';
import Albums from './Albums.js';
import Biography from './Biography.js';
import Panel from './Panel.js';

export default function ArtistPage({ artist }) {
  return (
    <>
      <h1>{artist.name}</h1>
      <Biography artistId={artist.id} />
      <Suspense fallback={<AlbumsGlimmer />}>
        <Panel>
          <Albums artistId={artist.id} />
        </Panel>
      </Suspense>
    </>
  );
}

function AlbumsGlimmer() {
  return (
    <div className="glimmer-panel">
      <div className="glimmer-line" />
      <div className="glimmer-line" />
      <div className="glimmer-line" />
    </div>
  );
}
```

```js src/Albums.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

export default function Albums({ artistId }) {
  const albums = use(fetchData(`/${artistId}/albums`));
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },
    );
    throw promise;
  }
}
```

```js src/Biography.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

export default function Biography({ artistId }) {
  const bio = use(fetchData(`/${artistId}/bio`));
  return (
    <section>
      <p className="bio">{bio}</p>
    </section>
  );
}

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },
    );
    throw promise;
  }
}
```

```js src/Panel.js hidden
export default function Panel({ children }) {
  return (
    <section className="panel">
      {children}
    </section>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url === '/the-beatles/albums') {
    return await getAlbums();
  } else if (url === '/the-beatles/bio') {
    return await getBio();
  } else {
    throw Error('Not implemented');
  }
}

async function getBio() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 500);
  });

  return `The Beatles were an English rock band,
    formed in Liverpool in 1960, that comprised
    John Lennon, Paul McCartney, George Harrison
    and Ringo Starr.`;
}

async function getAlbums() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 3000);
  });

  return [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];
}
```

```css
main {
  min-height: 200px;
  padding: 10px;
}

.layout {
  border: 1px solid black;
}

.header {
  background: #222;
  padding: 10px;
  text-align: center;
  color: white;
}

.bio { font-style: italic; }

.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}

.glimmer-panel {
  border: 1px dashed #aaa;
  background: linear-gradient(90deg, rgba(221,221,221,1) 0%, rgba(255,255,255,1) 100%);
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}

.glimmer-line {
  display: block;
  width: 60%;
  height: 20px;
  margin: 10px;
  border-radius: 4px;
  background: #f0f0f0;
}
```

</Sandpack>

<Note>

[Suspense-enabled](/reference/react/Suspense) রাউটারগুলি সাধারণত নেভিগেশন আপডেটগুলিকে ডিফল্টভাবে transitions এ মোড়ানোর আশা করা হয়।

</Note>

---

### ব্যবহারকারীদের কাছে ত্রুটি সীমানা ব্যবহার করে ত্রুটি প্রদর্শন করা {/*displaying-an-error-to-users-with-error-boundary*/}

<Canary>

useTransition জন্য এরর বাউন্ডারি বর্তমানে কেবল React-এর canary এবং পরীক্ষামূলক চ্যানেলগুলিতে পাওয়া যায়। [এখানে React-এর রিলিজ চ্যানেলগুলি](/community/versioning-policy#all-release-channels) সম্পর্কে আরও জানুন।

</Canary>

যদি `startTransition` এ পাস করা কোনো ফাংশন কোনো ত্রুটি দেখায়, তাহলে আপনি আপনার ব্যবহারকারীকে সেই ত্রুটির বার্তাটি প্রদর্শন করতে পারেন একটি [এরর বাউন্ডারির](/reference/react/Component#catching-rendering-errors-with-an-error-boundary) মাধ্যমে। এরর বাউন্ডারি ব্যবহার করতে, যে কম্পোনেন্টে আপনি `useTransition` কল করছেন তাকে একটি এরর বাউন্ডারির মধ্যে মোড়ান। একবার `startTransition` এ পাস করা ফাংশনে ত্রুটি ঘটলে, এরর বাউন্ডারির জন্য নির্ধারিত ফলব্যাক প্রদর্শিত হবে।

<Sandpack>

```js src/AddCommentContainer.js active
import { useTransition } from "react";
import { ErrorBoundary } from "react-error-boundary";

export function AddCommentContainer() {
  return (
    <ErrorBoundary fallback={<p>⚠️Something went wrong</p>}>
      <AddCommentButton />
    </ErrorBoundary>
  );
}

function addComment(comment) {
  // For demonstration purposes to show Error Boundary
  if (comment == null) {
    throw new Error("Example Error: An error thrown to trigger error boundary");
  }
}

function AddCommentButton() {
  const [pending, startTransition] = useTransition();

  return (
    <button
      disabled={pending}
      onClick={() => {
        startTransition(() => {
          // Intentionally not passing a comment
          // so error gets thrown
          addComment();
        });
      }}
    >
      Add comment
    </button>
  );
}
```

```js src/App.js hidden
import { AddCommentContainer } from "./AddCommentContainer.js";

export default function App() {
  return <AddCommentContainer />;
}
```

```js src/index.js hidden
// TODO: update to import from stable
// react instead of canary once the `use`
// Hook is in a stable release of React
import React, { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

// TODO: update this example to use
// the Codesandbox Server Component
// demo environment once it is created
import App from './App';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```json package.json hidden
{
  "dependencies": {
    "react": "canary",
    "react-dom": "canary",
    "react-scripts": "^5.0.0",
    "react-error-boundary": "4.0.3"
  },
  "main": "/index.js"
}
```
</Sandpack>

---

## সমস্যা সমাধান {/*troubleshooting*/}

### একটি ট্রানজিশনে ইনপুট আপডেট করা কাজ করে না {/*updating-an-input-in-a-transition-doesnt-work*/}

আপনি এমন একটি state ভেরিয়েবলের জন্য ট্রানজিশন ব্যবহার করতে পারবেন না যা একটি ইনপুট নিয়ন্ত্রণ করে:

```js {4,10}
const [text, setText] = useState('');
// ...
function handleChange(e) {
  // ❌ Can't use Transitions for controlled input state
  startTransition(() => {
    setText(e.target.value);
  });
}
// ...
return <input value={text} onChange={handleChange} />;
```

এর কারণ হল ট্রানজিশনগুলি নন-ব্লকিং, কিন্তু change ইভেন্টের রেসপন্সে ইনপুট আপডেট করা অবশ্যই একই সময়ে হওয়া উচিত। যদি আপনি টাইপ করার সময় একটি ট্রানজিশন চালাতে চান, তাহলে আপনার দুটি বিকল্প উপায় রয়েছে:

১. আপনি দুটি আলাদা state ভেরিয়েবল ঘোষণা করতে পারেন: একটি ইনপুট state এর জন্য (যা সর্বদা একই সময়ে আপডেট হয়), এবং একটি যা আপনি ট্রানজিশনে আপডেট করবেন। এটি আপনাকে একই সময়ে state ব্যবহার করে ইনপুট নিয়ন্ত্রণ করতে দেয়, এবং বাকি রেন্ডারিং লজিকে ট্রানজিশন state ভেরিয়েবল (যা ইনপুটের পিছনে "বিলম্বিত" হবে) পাস করতে দেয়।
২. বিকল্প হিসেবে, আপনি একটি state ভেরিয়েবল রাখতে পারেন, এবং [`useDeferredValue`](/reference/react/useDeferredValue) যোগ করতে পারেন যা বাস্তব মানের পিছনে "বিলম্বিত" হবে। এটি নতুন মানের সাথে "মেলে যাওয়ার" জন্য নন-ব্লকিং রি-রেন্ডারগুলি স্বয়ংক্রিয়ভাবে ট্রিগার করবে।

---

### React আমার state আপডেটকে ট্রানজিশন হিসেবে গ্রহণ করে না {/*react-doesnt-treat-my-state-update-as-a-transition*/}

যখন আপনি একটি state আপডেটকে ট্রানজিশনে মোড়ান, নিশ্চিত করুন যে এটি `startTransition` কলের *সময়* ঘটে:

```js
startTransition(() => {
  // ✅ Setting state *during* startTransition call
  setPage('/about');
});
```

`startTransition` এ আপনি যে ফাংশন পাস করবেন তা অবশ্যই একই সময়ে হতে হবে।

আপনি এভাবে একটি আপডেটকে ট্রানজিশন হিসেবে চিহ্নিত করতে পারবেন না:

```js
startTransition(() => {
  // ❌ Setting state *after* startTransition call
  setTimeout(() => {
    setPage('/about');
  }, 1000);
});
```

বরং, আপনি এটি করতে পারেন:

```js
setTimeout(() => {
  startTransition(() => {
    // ✅ Setting state *during* startTransition call
    setPage('/about');
  });
}, 1000);
```

একইভাবে, আপনি একটি আপডেটকে এইরকম ট্রানজিশন হিসেবে চিহ্নিত করতে পারবেন না:

```js
startTransition(async () => {
  await someAsyncFunction();
  // ❌ Setting state *after* startTransition call
  setPage('/about');
});
```

তবে, এটি এর পরিবর্তে কাজ করে:

```js
await someAsyncFunction();
startTransition(() => {
  // ✅ Setting state *during* startTransition call
  setPage('/about');
});
```

---

### আমি একটি কম্পোনেন্টের বাইরে থেকে `useTransition` কল করতে চাই {/*i-want-to-call-usetransition-from-outside-a-component*/}

আপনি একটি কম্পোনেন্টের বাইরে থেকে `useTransition` কল করতে পারবেন না কারণ এটি একটি Hook। এই ক্ষেত্রে, এর পরিবর্তে standalone [`startTransition`](/reference/react/startTransition) পদ্ধতি ব্যবহার করুন। এটি একইভাবে কাজ করে, কিন্তু এটি `isPending` ইন্ডিকেটর প্রদান করে না।

---

### আমি যে ফাংশন `startTransition`-এ পাস করি তা তাৎক্ষণিকভাবে কার্যকর হয় {/*the-function-i-pass-to-starttransition-executes-immediately*/}

আপনি যদি এই কোডটি চালান, তা 1, 2, 3 প্রিন্ট করবে:

```js {1,3,6}
console.log(1);
startTransition(() => {
  console.log(2);
  setPage('/about');
});
console.log(3);
```

**এটি 1, 2, 3 প্রিন্ট করার কথা।** `startTransition`-এ আপনি যে ফাংশন পাস করেন তা দেরি হয় না। ব্রাউজারের `setTimeout`-এর মতো, এটি পরে কলব্যাক চালায় না। React আপনার ফাংশনটি তাৎক্ষণিকভাবে কার্যকর করে, কিন্তু *এটি চালানোর সময়* নির্ধারিত কোনো state আপডেটগুলি ট্রানজিশন হিসাবে চিহ্নিত হয়। এটি এভাবে কাজ করে বলে আপনি কল্পনা করতে পারেন:

```js
// A simplified version of how React works

let isInsideTransition = false;

function startTransition(scope) {
  isInsideTransition = true;
  scope();
  isInsideTransition = false;
}

function setState() {
  if (isInsideTransition) {
    // ... schedule a Transition state update ...
  } else {
    // ... schedule an urgent state update ...
  }
}
```
