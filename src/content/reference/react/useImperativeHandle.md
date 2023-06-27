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
import { forwardRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  useImperativeHandle(ref, () => {
    return {
      // ... your methods ...
    };
  }, []);
  // ...
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

* `ref`: The `ref` you received as the second argument from the [`forwardRef` render function.](/reference/react/forwardRef#render-function)

* `createHandle`: A function that takes no arguments and returns the ref handle you want to expose. That ref handle can have any type. Usually, you will return an object with the methods you want to expose.

* **optional** `dependencies`: The list of all reactive values referenced inside of the `createHandle` code. Reactive values include props, state, and all the variables and functions declared directly inside your component body. If your linter is [configured for React](/learn/editor-setup#linting), it will verify that every reactive value is correctly specified as a dependency. The list of dependencies must have a constant number of items and be written inline like `[dep1, dep2, dep3]`. React will compare each dependency with its previous value using the [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) comparison. If a re-render resulted in a change to some dependency, or if you omitted this argument, your `createHandle` function will re-execute, and the newly created handle will be assigned to the ref.

#### রিটার্ন {/*returns*/}

`useImperativeHandle` রিটার্ন করে `undefined`.

---

## ব্যবহার {/*usage*/}

### Exposing a custom ref handle to the parent component {/*exposing-a-custom-ref-handle-to-the-parent-component*/}

By default, components don't expose their DOM nodes to parent components. For example, if you want the parent component of `MyInput` to [have access](/learn/manipulating-the-dom-with-refs) to the `<input>` DOM node, you have to opt in with [`forwardRef`:](/reference/react/forwardRef)

```js {4}
import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  return <input {...props} ref={ref} />;
});
```

With the code above, [a ref to `MyInput` will receive the `<input>` DOM node.](/reference/react/forwardRef#exposing-a-dom-node-to-the-parent-component) However, you can expose a custom value instead. To customize the exposed handle, call `useImperativeHandle` at the top level of your component:

```js {4-8}
import { forwardRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  useImperativeHandle(ref, () => {
    return {
      // ... আপনার মেথড ...
    };
  }, []);

  return <input {...props} />;
});
```

Note that in the code above, the `ref` is no longer forwarded to the `<input>`.

For example, suppose you don't want to expose the entire `<input>` DOM node, but you want to expose two of its methods: `focus` and `scrollIntoView`. To do this, keep the real browser DOM in a separate ref. Then use `useImperativeHandle` to expose a handle with only the methods that you want the parent component to call:

```js {7-14}
import { forwardRef, useRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
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
});
```

Now, if the parent component gets a ref to `MyInput`, it will be able to call the `focus` and `scrollIntoView` methods on it. However, it will not have full access to the underlying `<input>` DOM node.

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
      <MyInput label="Enter your name:" ref={ref} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

```js MyInput.js
import { forwardRef, useRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
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
});

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

The methods you expose via an imperative handle don't have to match the DOM methods exactly. For example, this `Post` component exposes a `scrollAndFocusAddComment` method via an imperative handle. This lets the parent `Page` scroll the list of comments *and* focus the input field when you click the button:

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

```js Post.js
import { forwardRef, useRef, useImperativeHandle } from 'react';
import CommentList from './CommentList.js';
import AddComment from './AddComment.js';

const Post = forwardRef((props, ref) => {
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
});

export default Post;
```


```js CommentList.js
import { forwardRef, useRef, useImperativeHandle } from 'react';

const CommentList = forwardRef(function CommentList(props, ref) {
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
});

export default CommentList;
```

```js AddComment.js
import { forwardRef, useRef, useImperativeHandle } from 'react';

const AddComment = forwardRef(function AddComment(props, ref) {
  return <input placeholder="Add comment..." ref={ref} />;
});

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
