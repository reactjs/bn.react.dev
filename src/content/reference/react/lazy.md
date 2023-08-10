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

একটি lazy-loaded React component ডিক্লেয়ার করার জন্য আপনার component অস্মূহের বাইরে `lazy` কল করুনঃ

```js
import { lazy } from 'react';

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

* `load`: A function that returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) or another *thenable* (a Promise-like object with a `then` method). React will not call `load` until the first time you attempt to render the returned component. After React first calls `load`, it will wait for it to resolve, and then render the resolved value as a React component. Both the returned Promise and the Promise's resolved value will be cached, so React will not call `load` more than once. If the Promise rejects, React will `throw` the rejection reason for the nearest Error Boundary to handle.

#### Returns {/*returns*/}

`lazy` returns a React component you can render in your tree. While the code for the lazy component is still loading, attempting to render it will *suspend.* Use [`<Suspense>`](/reference/react/Suspense) to display a loading indicator while it's loading.

---

### `load` function {/*load*/}

#### Parameters {/*load-parameters*/}

`load` receives no parameters.

#### Returns {/*load-returns*/}

You need to return a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) or some other *thenable* (a Promise-like object with a `then` method). It needs to eventually resolve to a valid React component type, such as a function, [`memo`](/reference/react/memo), or a [`forwardRef`](/reference/react/forwardRef) component.

---

## Usage {/*usage*/}

### Lazy-loading components with Suspense {/*suspense-for-code-splitting*/}

Usually, you import components with the static [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) declaration:

```js
import MarkdownPreview from './MarkdownPreview.js';
```

To defer loading this component's code until it's rendered for the first time, replace this import with:

```js
import { lazy } from 'react';

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
```

This code relies on [dynamic `import()`,](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) which might require support from your bundler or framework.

Now that your component's code loads on demand, you also need to specify what should be displayed while it is loading. You can do this by wrapping the lazy component or any of its parents into a [`<Suspense>`](/reference/react/Suspense) boundary:

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
