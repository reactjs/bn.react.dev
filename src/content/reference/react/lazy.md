---
title: lazy
---

<Intro>

`lazy` আপনাকে component এর কোড বিলম্বিত করার সুযোগ দেয় যতক্ষণ না এটা প্রথম বারের মত রেন্ডার করা হচ্ছে।

```js
const SomeComponent = lazy(load)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `lazy(load)` {/*lazy*/}

একটি lazy-loaded React component ডিক্লেয়ার করার জন্য আপনার component গুলোর বাইরে `lazy` কল করুনঃ

```js
import { lazy } from 'react';

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

* `load`: একটি ফাংশন যা একটি [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) অথবা অন্য কোন *thenable* (`then` মেথড সহ একটি Promise-like অবজেক্ট) রিটার্ন করে।  আপনি যতক্ষণ রিটার্ন হওয়া component লোড করার চেষ্টা করছেন ততক্ষণ React `load` কল করবে না। React প্রথম বার `load` কল করার পর, এটা resolved হবার অপেক্ষা করবে, এবং এর পরে resolved value React component হিসেবে রেন্ডার করবে। রিটার্ন হওয়া Promise এবং Promise এর resolved value cached হয়ে থাকবে, সুতরাং React একবারের বেশি `load` কল করবে না। যদি Promise reject করে, React rejection এর কারণ `throw` করবে যেন সবচেয়ে নিকটবর্তী error boundary সেটা হ্যান্ডেল করে।

#### রিটার্ন {/*returns*/}

`lazy` একটি React component রিটার্ন করে যা আপনি আপনার ট্রিতে রেন্ডার রক্তে পারবেন। যতক্ষণ পর্যন্ত lazy component এর কোড লোড হচ্ছে, এটাকে রেন্ডারের চেষ্টা *suspend* হবে। এটা লোডীং এর সময় লোডীং ইনডিকেটর দেখানোর জন্য [`<Suspense>`](/reference/react/Suspense) ব্যবহার করুন।

---

### `load` ফাংশন {/*load*/}

#### প্যারামিটার {/*load-parameters*/}

`load` কোন প্যারামিটার রিসিভ করে না।

#### রিটার্ন {/*load-returns*/}

আপনাকে একটি [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) অথবা অন্য কোন *thenable* (`then` মেথড সহ একটি Promise-like অবজেক্ট)। একে শেষমেশ একটি যথাযথ React component টাইপে যেতে হবে, যেমন একটি ফাংশন, [`memo`](/reference/react/memo), অথবা একটি [`forwardRef`](/reference/react/forwardRef) component।

---

## ব্যবহার {/*usage*/}

### Lazy-loading components with Suspense {/*suspense-for-code-splitting*/}

সাধারণত, আপনি component ইমপোর্ট করেন static [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) ডিক্লেয়ারেশন সহঃ

```js
import MarkdownPreview from './MarkdownPreview.js';
```

এই component এর কোডের লোডীং প্রথম বার রেন্ডার হবার আগ পর্যন্ত বিলম্বিত করতে এই ইমপোর্ট নিচের কোড দিয়ে প্রতিস্থাপন করুনঃ

```js
import { lazy } from 'react';

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
```

এই কোড [dynamic `import()`,](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) এর উপর নির্ভর করে যার আপনার বান্ডলার বা ফ্রেমওয়ার্ক থেকে support এর দরকার পড়তে পারে।

এখন যেহেতু আপনার component এর কোড চাহিদানুযায়ী লোড হয়, আপনাকে এটা নির্ধারণ করে দিতে হবে যে এটা লোডীং এর সময়ে কী ডিসপ্লে হওয়া উচিত। এটা আপনি করতে পারেন lazy component অথবা এর যেকোন parent কে [`<Suspense>`](/reference/react/Suspense) boundary এর মধ্যে wrap করার মাধ্যমেঃ

```js {1,4}
<Suspense fallback={<Loading />}>
  <h2>Preview</h2>
  <MarkdownPreview />
 </Suspense>
```

এই উদাহরণে, `MarkdownPreview` এর কোড ততক্ষণ লোড হবে না যতক্ষণ আপনি এটা রেন্ডারের চেষ্টা করেন। যদি `MarkdownPreview` লোড না হয়ে থাকে, `Loading` তার নিজের জায়গায় দেখাবে। চেকবক্স টিক করে দেখুনঃ

<Sandpack>

```js App.js
import { useState, Suspense, lazy } from 'react';
import Loading from './Loading.js';

const MarkdownPreview = lazy(() => delayForDemo(import('./MarkdownPreview.js')));

export default function MarkdownEditor() {
  const [showPreview, setShowPreview] = useState(false);
  const [markdown, setMarkdown] = useState('Hello, **world**!');
  return (
    <>
      <textarea value={markdown} onChange={e => setMarkdown(e.target.value)} />
      <label>
        <input type="checkbox" checked={showPreview} onChange={e => setShowPreview(e.target.checked)} />
        Show preview
      </label>
      <hr />
      {showPreview && (
        <Suspense fallback={<Loading />}>
          <h2>Preview</h2>
          <MarkdownPreview markdown={markdown} />
        </Suspense>
      )}
    </>
  );
}

// Add a fixed delay so you can see the loading state
function delayForDemo(promise) {
  return new Promise(resolve => {
    setTimeout(resolve, 2000);
  }).then(() => promise);
}
```

```js Loading.js
export default function Loading() {
  return <p><i>Loading...</i></p>;
}
```

```js MarkdownPreview.js
import { Remarkable } from 'remarkable';

const md = new Remarkable();

export default function MarkdownPreview({ markdown }) {
  return (
    <div
      className="content"
      dangerouslySetInnerHTML={{__html: md.render(markdown)}}
    />
  );
}
```

```json package.json hidden
{
  "dependencies": {
    "immer": "1.7.3",
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "remarkable": "2.0.1"
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
label {
  display: block;
}

input, textarea {
  margin-bottom: 10px;
}

body {
  min-height: 200px;
}
```

</Sandpack>

এই ডেমো কৃত্রিম একটা বিলম্বের সাথে লোড হয়। এর পরের বার যখন আপনি চেকবক্স টিক করবেন এবং আনটিক করবেন, `Preview` এর cache হয়ে যাবে, সুতরাং কোন লোডীং state থাকবে না। লোডীং state আবার দেখতে চাইলে, স্যান্ডবক্সের উপর "Reset" বাটন ক্লিক করুন।

[Learn more about managing loading states with Suspense.](/reference/react/Suspense)

---

## ট্রাবলশ্যুট {/*troubleshooting*/}

### আমার `lazy` component এর state অপ্রত্যাশিতভাবে রিসেট হয়ে যায় {/*my-lazy-components-state-gets-reset-unexpectedly*/}

`lazy` component গুলোকে অন্যান্য component গুলোর *ভেতরে* ডিক্লেয়ার করবেন নাঃ

```js {4-5}
import { lazy } from 'react';

function Editor() {
  // 🔴 Bad: This will cause all state to be reset on re-renders
  const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
  // ...
}
```

তার বদলে, এগুলোকে সব সময় আপনার মডিউলের সর্বোচ্চ স্তরে ডিক্লেয়ার করুনঃ

```js {3-4}
import { lazy } from 'react';

// ✅ Good: Declare lazy components outside of your components
const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));

function Editor() {
  // ...
}
```
