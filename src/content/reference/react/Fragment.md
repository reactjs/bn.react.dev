---
title: <Fragment> (<>...</>)
---

<Intro>

<<<<<<< HEAD
`<Fragment>`, যেটা বেশিরভাগ সময় `<>...</>` সিনট্যাক্স ব্যবহার করে প্রকাশ করা হয়, আপনাকে একটা wrapper নোড ছাড়াই এলিমেন্ট গ্রুপ করার সুবিধা দেবে।
=======
`<Fragment>`, often used via `<>...</>` syntax, lets you group elements without a wrapper node. 

<Canary> Fragments can also accept refs, which enable interacting with underlying DOM nodes without adding wrapper elements. See reference and usage below.</Canary>
>>>>>>> 427f24d694674be458f0fe7cb97ab1c8fe736586

```js
<>
  <OneChild />
  <AnotherChild />
</>
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `<Fragment>` {/*fragment*/}

যেসব ক্ষেত্রে আপনার একটি মাত্র এলিমেন্ট লাগবে `<Fragment>` এর মধ্যে একাধিক এলিমেন্ট গ্রুপ করে wrap করে ফেলুন। `Fragment` এর মধ্যে গ্রুপ করা হলে ফলাফলে যে DOM পাওয়া যায় তার উপর কোন প্রভাব পড়ে না; গ্রুপ করা না হলে যেমন হত ঠিক তেমনি ঘটে। বেশিরভাগ ক্ষেত্রে খালি JSX ট্যাগ `<></>` কে `<Fragment></Fragment>` প্রকাশ করতে সংক্ষেপে ব্যবহার করা হয়।

#### Props {/*props*/}

<<<<<<< HEAD
- **অপশনাল** `key`: যেসব ফ্র্যাগমেন্ট স্পষ্টভাবে `<Fragment>` সিনট্যাক্স দিয়ে ডিক্লেয়ার করা হয় তাদের [keys](/learn/rendering-lists#keeping-list-items-in-order-with-key) থাকতে পারে।
=======
- **optional** `key`: Fragments declared with the explicit `<Fragment>` syntax may have [keys.](/learn/rendering-lists#keeping-list-items-in-order-with-key)
- <CanaryBadge />  **optional** `ref`: A ref object (e.g. from [`useRef`](/reference/react/useRef)) or [callback function](/reference/react-dom/components/common#ref-callback). React provides a `FragmentInstance` as the ref value that implements methods for interacting with the DOM nodes wrapped by the Fragment.

### <CanaryBadge /> FragmentInstance {/*fragmentinstance*/}

When you pass a ref to a fragment, React provides a `FragmentInstance` object with methods for interacting with the DOM nodes wrapped by the fragment:

**Event handling methods:**
- `addEventListener(type, listener, options?)`: Adds an event listener to all first-level DOM children of the Fragment.
- `removeEventListener(type, listener, options?)`: Removes an event listener from all first-level DOM children of the Fragment.
- `dispatchEvent(event)`: Dispatches an event to a virtual child of the Fragment to call any added listeners and can bubble to the DOM parent.

**Layout methods:**
- `compareDocumentPosition(otherNode)`: Compares the document position of the Fragment with another node.
  - If the Fragment has children, the native `compareDocumentPosition` value is returned. 
  - Empty Fragments will attempt to compare positioning within the React tree and include `Node.DOCUMENT_POSITION_IMPLEMENTATION_SPECIFIC`.
  - Elements that have a different relationship in the React tree and DOM tree due to portaling or other insertions are `Node.DOCUMENT_POSITION_IMPLEMENTATION_SPECIFIC`.
- `getClientRects()`: Returns a flat array of `DOMRect` objects representing the bounding rectangles of all children.
- `getRootNode()`: Returns the root node containing the Fragment's parent DOM node.

**Focus management methods:**
- `focus(options?)`: Focuses the first focusable DOM node in the Fragment. Focus is attempted on nested children depth-first.
- `focusLast(options?)`: Focuses the last focusable DOM node in the Fragment. Focus is attempted on nested children depth-first.
- `blur()`: Removes focus if `document.activeElement` is within the Fragment.

**Observer methods:**
- `observeUsing(observer)`: Starts observing the Fragment's DOM children with an IntersectionObserver or ResizeObserver.
- `unobserveUsing(observer)`: Stops observing the Fragment's DOM children with the specified observer.
>>>>>>> 427f24d694674be458f0fe7cb97ab1c8fe736586

#### সতর্কতা {/*caveats*/}

- আপনি যদি একটা ফ্র্যাগমেন্টে `key` পাস করতে চান, আপনি `<>...</>` সিনট্যাক্স ব্যবহার করতে পারবেন না। আপনাকে স্পষ্টভাবে `'react'` থেকে `Fragment` ইমপোর্ট করতে হবে এবং `<Fragment key={yourKey}>...</Fragment>` রেন্ডার করতে হবে।

- যখন আপনি `<><Child /></>` থেকে `[<Child />]` রেন্ডারিং এ চলে যান বা ফিরে আসেন, অথবা `<><Child /></>` থেকে `<Child />` রেন্ডারিং এ যান এবং ফিরে আসেন React [state রিসেট](/learn/preserving-and-resetting-state) করে না। এটা শুধুমাত্র এক স্তর গভীরে কাজ করেঃ উদাহরণস্বরূপ, `<><><Child /></></>` থেকে `<Child />` এ গেলে state রিসেট হয়। সুনির্দিষ্ট semantics দেখুন [এখানে।](https://gist.github.com/clemmy/b3ef00f9507909429d8aa0d3ee4f986b)

- <CanaryBadge /> If you want to pass `ref` to a Fragment, you can't use the `<>...</>` syntax. You have to explicitly import `Fragment` from `'react'` and render `<Fragment ref={yourRef}>...</Fragment>`.

---

## ব্যবহার {/*usage*/}

### একাধিক এলিমেন্ট রিটার্নিং {/*returning-multiple-elements*/}

একাধিক এলিমেন্ট গ্রুপ করতে `Fragment` বা সমার্থক `<>...</>` সিনট্যাক্স ব্যবহার করুন। এমন যেকোন জায়গা যেখানে একটি এলিমেন্ট যেতে পারে সেখানে আপনি একাধিক এলিমেন্ট রাখার জন্য এটা ব্যবহার করতে পারেন। উদাহরণস্বরূপ, একটা কম্পোনেন্ট শুধুমাত্র একটি এলিমেন্ট রিটার্ন করতে পারে, কিন্তু ফ্র্যাগমেন্ট ব্যবহার করে আপনি একাধিক এলিমেন্টকে একসাথে করে গ্রুপ হিসেবে রিটার্ন করতে পারেনঃ

```js {3,6}
function Post() {
  return (
    <>
      <PostTitle />
      <PostBody />
    </>
  );
}
```

ফ্র্যাগমেন্ট কাজে লাগে কারণ ফ্র্যাগমেন্টে এলিমেন্ট গ্রুপ করলে সেটা লেআউট বা স্টাইলে কোন প্রভাব ফেলে না, যে সুবিধাটা আপনি DOM এলিমেন্টের মত অন্য একটা কনটেইনারে এলিমেন্টগুলো wrap করলে পেতেন না। আপনি যদি ব্রাউজার টুল ব্যবহার করে এই উদাহরণটি inspect করেন, দেখবেন যে সব `<h1>` এবং `<article>` DOM নোডকে sibling হিসেবে দেখায়। তাদের ঘিরে কোন wrapper দেখবেন নাঃ

<Sandpack>

```js
export default function Blog() {
  return (
    <>
      <Post title="An update" body="It's been a while since I posted..." />
      <Post title="My new blog" body="I am starting a new blog!" />
    </>
  )
}

function Post({ title, body }) {
  return (
    <>
      <PostTitle title={title} />
      <PostBody body={body} />
    </>
  );
}

function PostTitle({ title }) {
  return <h1>{title}</h1>
}

function PostBody({ body }) {
  return (
    <article>
      <p>{body}</p>
    </article>
  );
}
```

</Sandpack>

<DeepDive>

#### বিশেষ সিনট্যাক্স ব্যবহার না করে কীভাবে একটি ফ্র্যাগমেন্ট লিখতে হয়? {/*how-to-write-a-fragment-without-the-special-syntax*/}

উপরের এই উদাহরণটি React থেকে `Fragment` ইমপোর্ট করার মতই কাজ করবেঃ

```js {1,5,8}
import { Fragment } from 'react';

function Post() {
  return (
    <Fragment>
      <PostTitle />
      <PostBody />
    </Fragment>
  );
}
```

সাধারণত আপনার এর দরকার পড়বে না যদি না আপনার [`Fragment` এ একটা `key` পাস করার](#rendering-a-list-of-fragments) প্রয়োজন পড়ে।

</DeepDive>

---

### একটি ভ্যারিয়েবলে একাধিক এলিমেন্ট এসাইনিং {/*assigning-multiple-elements-to-a-variable*/}

অন্য যেকোন এলিমেন্টের মত, আপনি ফ্র্যাগমেন্ট এলিমেন্ট ভ্যারিয়েবলে এসাইন করতে পারবেন, prop হিসেবে পাস করতে পারবেন, এবং আরো যা যা করা যায় করতে পারবেনঃ

```js
function CloseDialog() {
  const buttons = (
    <>
      <OKButton />
      <CancelButton />
    </>
  );
  return (
    <AlertDialog buttons={buttons}>
      Are you sure you want to leave this page?
    </AlertDialog>
  );
}
```

---

### টেক্সটের সাথে এলিমেন্ট এর গ্রুপিং {/*grouping-elements-with-text*/}

আপনি কম্পোনেন্টের সাথে টেক্সট গ্রুপ করতে চাইলে `Fragment` ব্যবহার করতে পারেনঃ

```js
function DateRangePicker({ start, end }) {
  return (
    <>
      From
      <DatePicker date={start} />
      to
      <DatePicker date={end} />
    </>
  );
}
```

---

### ফ্র্যাগমেন্টের একটা তালিকার রেন্ডার {/*rendering-a-list-of-fragments*/}

এখানে এমন একটা অবস্থা দেখা যাচ্ছে যেখানে আপনাকে `<></>` এর জায়গায় স্পষ্টভাবে `Fragment` সিনট্যাক্স ব্যবহার করতে হবে। যখন আপনি [একটা লুপে একাধিক এলিমেন্ট রেন্ডার করছেন](/learn/rendering-lists), আপনাকে প্রতি এলিমেন্টের জন্য একটি `key` ঠিক করে দিতে হবে। যদি লুপের মধ্যকার এলিমেন্টগুলা ফ্র্যাগমেন্ট হয়, আপনাকে `key` এট্রিবিউট দেবার জন্য সাধারণ JSX এলিমেন্ট সিনট্যাক্স ব্যবহার করতে হবেঃ

```js {3,6}
function Blog() {
  return posts.map(post =>
    <Fragment key={post.id}>
      <PostTitle title={post.title} />
      <PostBody body={post.body} />
    </Fragment>
  );
}
```

আপনি DOM এ inspect করে দেখতে পারেন ফ্র্যাগমেন্ট চিলড্রেন এর আসে পাশে কোন wrapper এলিমেন্ট নেইঃ

<Sandpack>

```js
import { Fragment } from 'react';

const posts = [
  { id: 1, title: 'An update', body: "It's been a while since I posted..." },
  { id: 2, title: 'My new blog', body: 'I am starting a new blog!' }
];

export default function Blog() {
  return posts.map(post =>
    <Fragment key={post.id}>
      <PostTitle title={post.title} />
      <PostBody body={post.body} />
    </Fragment>
  );
}

function PostTitle({ title }) {
  return <h1>{title}</h1>
}

function PostBody({ body }) {
  return (
    <article>
      <p>{body}</p>
    </article>
  );
}
```

</Sandpack>

---

### <CanaryBadge /> Using Fragment refs for DOM interaction {/*using-fragment-refs-for-dom-interaction*/}

Fragment refs allow you to interact with the DOM nodes wrapped by a Fragment without adding extra wrapper elements. This is useful for event handling, visibility tracking, focus management, and replacing deprecated patterns like `ReactDOM.findDOMNode()`.

```js
import { Fragment } from 'react';

function ClickableFragment({ children, onClick }) {
  return (
    <Fragment ref={fragmentInstance => {
      fragmentInstance.addEventListener('click', handleClick);
      return () => fragmentInstance.removeEventListener('click', handleClick);
    }}>
      {children}
    </Fragment>
  );
}
```
---

### <CanaryBadge /> Tracking visibility with Fragment refs {/*tracking-visibility-with-fragment-refs*/}

Fragment refs are useful for visibility tracking and intersection observation. This enables you to monitor when content becomes visible without requiring the child Components to expose refs:

```js {19,21,31-34}
import { Fragment, useRef, useLayoutEffect } from 'react';

function VisibilityObserverFragment({ threshold = 0.5, onVisibilityChange, children }) {
  const fragmentRef = useRef(null);

  useLayoutEffect(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        onVisibilityChange(entries.some(entry => entry.isIntersecting))
      },
      { threshold }
    );
    
    fragmentRef.current.observeUsing(observer);
    return () => fragmentRef.current.unobserveUsing(observer);
  }, [threshold, onVisibilityChange]);

  return (
    <Fragment ref={fragmentRef}>
      {children}
    </Fragment>
  );
}

function MyComponent() {
  const handleVisibilityChange = (isVisible) => {
    console.log('Component is', isVisible ? 'visible' : 'hidden');
  };

  return (
    <VisibilityObserverFragment onVisibilityChange={handleVisibilityChange}>
      <SomeThirdPartyComponent />
      <AnotherComponent />
    </VisibilityObserverFragment>
  );
}
```

This pattern is an alternative to Effect-based visibility logging, which is an anti-pattern in most cases. Relying on Effects alone does not guarantee that the rendered Component is observable by the user.

---

### <CanaryBadge /> Focus management with Fragment refs {/*focus-management-with-fragment-refs*/}

Fragment refs provide focus management methods that work across all DOM nodes within the Fragment:

```js
import { Fragment, useRef } from 'react';

function FocusFragment({ children }) {
  return (
    <Fragment ref={(fragmentInstance) => fragmentInstance?.focus()}>
      {children}
    </Fragment>
  );
}
```

The `focus()` method focuses the first focusable element within the Fragment, while `focusLast()` focuses the last focusable element.
