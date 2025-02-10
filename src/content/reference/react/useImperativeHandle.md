---
title: useImperativeHandle
---

<Intro>

`useImperativeHandle` হচ্ছে একটি React Hook যা আপনাকে [ref](/learn/manipulating-the-dom-with-refs) হিসেবে exposed হওয়া হ্যান্ডল কাস্টমাইজ করার সুযোগ দেয়।

```js
useImperativeHandle(ref, createHandle, dependencies?)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `useImperativeHandle(ref, createHandle, dependencies?)` {/*useimperativehandle*/}

আপনার কম্পোনেন্ট যে ref handle এক্সপোজ করে সেটাকে কাস্টমাইজ করতে কম্পোনেন্টের উচ্চ স্তরে `useImperativeHandle` কল করুন।

```js
import { useImperativeHandle } from 'react';

function MyInput({ ref }) {
  useImperativeHandle(ref, () => {
    return {
      // ... your methods ...
    };
  }, []);
  // ...
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

<<<<<<< HEAD
* `ref`: আপনি [`forwardRef` রেন্ডার ফাংশনের](/reference/react/forwardRef#render-function) দ্বিতীয় আর্গুমেন্ট হিসেবে যে `ref` পেয়েছিলেন সেটা।
=======
* `ref`: The `ref` you received as a prop to the `MyInput` component.
>>>>>>> 91614a51a1be9078777bc337ba83fc62e606cc14

* `createHandle`: একটা ফাংশন যা কোন আর্গুমেন্ট নেয় না এবং আপনি যে ref হ্যান্ডল উন্মুক্ত করতে চান সেটা রিটার্ন করে। ওই ref হ্যান্ডলের যেকোন টাইপ থাকতে পারে। সাধারণত আপনি একটা অবজেক্ট রিটার্ন করবেন যেটার সাথে সেই মেথডগুলো থাকবে যেগুলো আপনি উন্মুক্ত করতে চান।

* **optional** `dependencies`: `createHandle` কোডের মধ্যে রেফারেন্স দেওয়া আছে এমন সকল reactive ভ্যালুর তালিকা। Reactive ভ্যালুর মধ্যে রয়েছে আপনার কম্পোনেন্টে সরাসরি declared সকল props, state এবং সকল ভ্যারিয়েবল এবং ফাংশন। যদি আপনার লিন্টার [React এর জন্য কনফিগার করা থাকে](/learn/editor-setup#linting), এটা দেখবে যে সকল reactive ভ্যালু সঠিক ভাবে ডিপেন্ডেন্সি হিসেবে চিহ্নিত হয়েছে কি না। ডিপেন্ডেন্সির তালিকায় সব সময় ধ্রুব সংখ্যক আইটেম থাকবে এবং inline এ লেখা থাকবে এমন ভাবে, `[dep1, dep2, dep3]`। React [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) comparison ব্যবহার করে সকল ডিপেন্ডেন্সি তার আগের মানের সাথে তুলনা করবে। যদি কোন ডিপেন্ডেন্সির পরিবর্তনের কারণে পুনরায় রেন্ডার হয়ে থাকে, অথবা আপনি যদি এই আর্গুমেন্টটি মুছে ফেলে থাকেন, তবে আপনার `createHandle` ফাংশন re-execute হবে, এবং নতুন করে তৈরী হওয়া হ্যান্ডেল ref এ এসাইন হয়ে যাবে। 

<<<<<<< HEAD
#### রিটার্ন {/*returns*/}
=======
<Note>

Starting with React 19, [`ref` is available as a prop.](/blog/2024/12/05/react-19#ref-as-a-prop) In React 18 and earlier, it was necessary to get the `ref` from [`forwardRef`.](/reference/react/forwardRef) 

</Note>

#### Returns {/*returns*/}
>>>>>>> 91614a51a1be9078777bc337ba83fc62e606cc14

`useImperativeHandle` রিটার্ন করে `undefined`.

---

## ব্যবহার {/*usage*/}

### প্যারেন্ট কম্পোণেন্টের একটি কাস্টম ref handle উন্মুক্ত করা {/*exposing-a-custom-ref-handle-to-the-parent-component*/}

<<<<<<< HEAD
ডিফল্ট ভাবে, কম্পোনেন্ট তাদের DOM নোড প্যারেন্ট কম্পোনেন্টে উন্মুক্ত করে না। উদাহরণস্বরূপ, আপনি যদি চান `MyInput` এর প্যারেন্ট কম্পোনেন্ট `<input>` DOM নোডের [অ্যাক্সেস পেয়ে যাক](/learn/manipulating-the-dom-with-refs), আপনাকে [`forwardRef`](/reference/react/forwardRef) ব্যবহার করতে হবেঃ
=======
To expose a DOM node to the parent element, pass in the `ref` prop to the node.
>>>>>>> 91614a51a1be9078777bc337ba83fc62e606cc14

```js {2}
function MyInput({ ref }) {
  return <input ref={ref} />;
};
```

<<<<<<< HEAD
উপরের কোডে, [`MyInput` এর ref `<input>` DOM নোড রিসিভ করবে।](/reference/react/forwardRef#exposing-a-dom-node-to-the-parent-component) কিন্তু, এর জায়গায় আপনি একটি কাস্টম ভ্যালু উন্মুক্ত করতে পারেন। উন্মুক্ত হওয়া হ্যান্ডেল কাস্টমাইজ করার জন্য, আপনার কম্পোনেন্টের সর্বোচ্চ স্তরে `useImperativeHandle` কল করুনঃ
=======
With the code above, [a ref to `MyInput` will receive the `<input>` DOM node.](/learn/manipulating-the-dom-with-refs) However, you can expose a custom value instead. To customize the exposed handle, call `useImperativeHandle` at the top level of your component:
>>>>>>> 91614a51a1be9078777bc337ba83fc62e606cc14

```js {4-8}
import { useImperativeHandle } from 'react';

function MyInput({ ref }) {
  useImperativeHandle(ref, () => {
    return {
      // ... আপনার মেথড ...
    };
  }, []);

  return <input />;
};
```

<<<<<<< HEAD
উপরের কোডে লক্ষ্য করুণ, `ref` কে আর `<input>` এ ফরোয়ার্ড করা হচ্ছে না।
=======
Note that in the code above, the `ref` is no longer passed to the `<input>`.
>>>>>>> 91614a51a1be9078777bc337ba83fc62e606cc14

উদাহরণস্বরূপ, ধরা যাক আপনি পুরো `<input>` ডম নোড উন্মুক্ত করতে চান না, কিন্তু আপনি এর দুটি মেথড উন্মুক্ত করতে চানঃ `focus` এবং `scrollIntoView`। এটা ্করতে হলে, প্রকৃত ব্রাউজার DOM আলাদা একটি ref এ রাখুন। তারপর প্যারেন্ট কম্পোনেন্ট যেই মেথডগুলো কল করবে বলে আপনি চান, সেগুলো সহ একটি হ্যান্ডেল উন্মুক্ত করতে `useImperativeHandle` ব্যবহার করুণঃ 

```js {7-14}
import { useRef, useImperativeHandle } from 'react';

function MyInput({ ref }) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      scrollIntoView() {
        inputRef.current.scrollIntoView();
      },
    };
  }, []);

  return <input ref={inputRef} />;
};
```

এখন, যদি প্যারেন্ট কম্পোনেন্ট `MyInput` এ একটি ref পেয়ে যায়, এটা `focus` এবং `scrollIntoView` মেথডগুলোকে এর উপর কল করতে পারবে। যদিও, এটা পর্দার পেছনের `<input>` DOM নোডের সম্পূর্ণ access পাবে না।

<Sandpack>

```js
import { useRef } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
    // এটা কাজ করবে না কারণ DOM নোড exposed নাঃ
    // ref.current.style.opacity = 0.5;
  }

  return (
    <form>
      <MyInput placeholder="Enter your name" ref={ref} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

```js src/MyInput.js
import { useRef, useImperativeHandle } from 'react';

function MyInput({ ref, ...props }) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      scrollIntoView() {
        inputRef.current.scrollIntoView();
      },
    };
  }, []);

  return <input {...props} ref={inputRef} />;
};

export default MyInput;
```

```css
input {
  margin: 5px;
}
```

</Sandpack>

---

### আপনার নিজের imperative মেথড উন্মুক্ত করা {/*exposing-your-own-imperative-methods*/}

আপনি imperative handle এর মাধ্যমে যেসব মেথড উন্মুক্ত করেন সেগুলোর DOM মেথডের সাথে মিলার প্রয়োজন নেই। উদাহরস্বরূপ, এই `Post` কম্পোনেন্টটি imperative handle এর মাধ্যমে একটি `scrollAndFocusAddComment` মেথড উন্মুক্ত করে। এটা প্যারেন্ট `Page` কে কমেন্টের লিস্ট স্ক্রল করতে দেয় *এবং* যখন আপনি বাটন ক্লিক করেন তখন ইনপুট ফোকাস করতে দেয়ঃ

<Sandpack>

```js
import { useRef } from 'react';
import Post from './Post.js';

export default function Page() {
  const postRef = useRef(null);

  function handleClick() {
    postRef.current.scrollAndFocusAddComment();
  }

  return (
    <>
      <button onClick={handleClick}>
        Write a comment
      </button>
      <Post ref={postRef} />
    </>
  );
}
```

```js src/Post.js
import { useRef, useImperativeHandle } from 'react';
import CommentList from './CommentList.js';
import AddComment from './AddComment.js';

function Post({ ref }) {
  const commentsRef = useRef(null);
  const addCommentRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      scrollAndFocusAddComment() {
        commentsRef.current.scrollToBottom();
        addCommentRef.current.focus();
      }
    };
  }, []);

  return (
    <>
      <article>
        <p>Welcome to my blog!</p>
      </article>
      <CommentList ref={commentsRef} />
      <AddComment ref={addCommentRef} />
    </>
  );
};

export default Post;
```


```js src/CommentList.js
import { useRef, useImperativeHandle } from 'react';

function CommentList({ ref }) {
  const divRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      scrollToBottom() {
        const node = divRef.current;
        node.scrollTop = node.scrollHeight;
      }
    };
  }, []);

  let comments = [];
  for (let i = 0; i < 50; i++) {
    comments.push(<p key={i}>Comment #{i}</p>);
  }

  return (
    <div className="CommentList" ref={divRef}>
      {comments}
    </div>
  );
}

export default CommentList;
```

```js src/AddComment.js
import { useRef, useImperativeHandle } from 'react';

function AddComment({ ref }) {
  return <input placeholder="Add comment..." ref={ref} />;
}

export default AddComment;
```

```css
.CommentList {
  height: 100px;
  overflow: scroll;
  border: 1px solid black;
  margin-top: 20px;
  margin-bottom: 20px;
}
```

</Sandpack>

<Pitfall>

**ref এর যথেচ্ছ ব্যবহার এড়িয়ে চলুন।** আপনার শুধু মাত্র সেই সকল *imperative* আচরণের জন্য ref ব্যবহার করা উচিত যেগুলো আপনি প্রপ হিসেবে প্রকাশ করতে পারবেন নাঃ যেমন, একটা নোড পর্যন্ত স্ক্রল করা, একতা নোডে ফোকাস করা, একতা অ্যানিমেশন ট্রিগার করা, টেক্সট সিলেক্ট করা, এবং এরকম আরো যা যা আছে।

**আপনি যদি কোন কিছু prop হিসেবে প্রকাশ করতে পারেন, তবে আপনার উচিত ref ব্যবহার না করা।** উদাহরণস্বরূপ, একটা `Modal` কম্পোনেন্ট থেকে `{ open, close }` এর মত একটি imperative handle এক্সপোজ করার জায়গায় `isOpen` কে `<Modal isOpen={isOpen} />` এর মত prop হিসেবে নেওয়া ভাল। Prop এর মাধ্যমে imperative কাজ করবার জন্য আপনাকে সাহায্য করতে পারে [Effects।](/learn/synchronizing-with-effects)

</Pitfall>
