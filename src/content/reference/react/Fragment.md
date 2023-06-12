---
title: <Fragment> (<>...</>)
---

<Intro>

`<Fragment>`, যেটা বেশিরভাগ সময় `<>...</>` সিনট্যাক্স ব্যবহার করে প্রকাশ করা হয়, আপনাকে একটা wrapper নোড ছাড়াই এলিমেন্ট গ্রুপ করার সুবিধা দেবে।

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

- **অপশনাল** `key`: যেসব ফ্র্যাগমেন্ট স্পষ্টভাবে `<Fragment>` সিনট্যাক্স দিয়ে ডিক্লেয়ার করা হয় তাদের [keys](/learn/rendering-lists#keeping-list-items-in-order-with-key) থাকতে পারে।

#### সতর্কতা {/*caveats*/}

- আপনি যদি একটা ফ্র্যাগমেন্টে `key` পাস করতে চান, আপনি `<>...</>` সিনট্যাক্স ব্যবহার করতে পারবেন না। আপনাকে স্পষ্টভাবে `'react'` থেকে `Fragment` ইমপোর্ট করতে হবে এবং `<Fragment key={yourKey}>...</Fragment>` রেন্ডার করতে হবে।

- React does not [reset state](/learn/preserving-and-resetting-state) when you go from rendering `<><Child /></>` to `[<Child />]` or back, or when you go from rendering `<><Child /></>` to `<Child />` and back. This only works a single level deep: for example, going from `<><><Child /></></>` to `<Child />` resets the state. See the precise semantics [here.](https://gist.github.com/clemmy/b3ef00f9507909429d8aa0d3ee4f986b)

---

## ব্যবহার {/*usage*/}

### একাধিক এলিমেন্ট রিটার্নিং {/*returning-multiple-elements*/}

Use `Fragment`, or the equivalent `<>...</>` syntax, to group multiple elements together. You can use it to put multiple elements in any place where a single element can go. For example, a component can only return one element, but by using a Fragment you can group multiple elements together and then return them as a group:

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

Fragments are useful because grouping elements with a Fragment has no effect on layout or styles, unlike if you wrapped the elements in another container like a DOM element. If you inspect this example with the browser tools, you'll see that all `<h1>` and `<article>` DOM nodes appear as siblings without wrappers around them:

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

#### How to write a Fragment without the special syntax? {/*how-to-write-a-fragment-without-the-special-syntax*/}

The example above is equivalent to importing `Fragment` from React:

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

Usually you won't need this unless you need to [pass a `key` to your `Fragment`.](#rendering-a-list-of-fragments)

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
