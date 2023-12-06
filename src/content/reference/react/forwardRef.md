---
title: forwardRef
---

<Intro>

`forwardRef` আপনার কম্পোনেন্টকে একটি DOM নোড একটি প্যারেন্ট কম্পোনেন্টে একটি [ref](/learn/manipulating-the-dom-with-refs) সহ এক্সপোজ করার সুযোগ দেয়।

```js
const SomeComponent = forwardRef(render)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `forwardRef(render)` {/*forwardref*/}

`forwardRef()` কল করুন যেন আপনার কম্পোনেন্ট একটি ref রিসিভ করতে পারে এবং একটি চাইল্ড কম্পোনেন্টে ফরোয়ার্ড করতে পারেঃ

```js
import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  // ...
});
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

* `render`: আপনার কম্পোনেন্টের রেন্ডার ফাংশন। আপনার কম্পোনেন্ট তার প্যারেন্ট থেকে যে প্রপ এবং `ref` পায় সেটা নিয়ে React এই ফাংশনে কল করে। আপনি যেই JSX রিটার্ন করবেন সেটা হবে আপনার কম্পোনেন্টের আউটপুট।

#### রিটার্ন {/*returns*/}

`forwardRef` এমন একটা React কম্পোনেন্ট রিটার্ন করে যেটা আপনি JSX এ রেন্ডার করতে পারেন। সোজাসাপ্টা ফাংশন হিসেবে ডিফাইন করা React কম্পোনেন্টের সাথে এর অমিল এখানেই যে, `forwardRef` দ্বারা রিটার্ন হওয়া কম্পোনেন্ট একটা `ref` প্রপ রিসিভও করতে পারে।

#### সতর্কতা {/*caveats*/}

* Strict Mode এ, React [আপনাকে accidental impurities খুঁজে বের করতে সাহায্য করার জন্য](#my-initializer-or-updater-function-runs-twice) **আপনার রেন্ডার ফাংশন দুবার কল করবে।** এটা development-only আচরণ এবং production এ কোন প্রভাব ফেলবে না। যদি আপনার রেন্ডার ফাংশন pure হয় (যেমন এর হওয়া উচিত), এটা আপনার কম্পোনেন্টের লজিকে কোন প্রভাব ফেলবার কথা না। দুটি কলের একটির ফলাফলকে আমলে আনা হবে না।


---

### `render` ফাংশন {/*render-function*/}

`forwardRef` একটি রেন্ডার ফাংশনকে একটি আর্গুমেন্ট হিসেবে গ্রহণ করে। React এই ফাংশনে `props` এবং `ref` সহ কল করেঃ

```js
const MyInput = forwardRef(function MyInput(props, ref) {
  return (
    <label>
      {props.label}
      <input ref={ref} />
    </label>
  );
});
```

#### প্যারামিটার {/*render-parameters*/}

* `props`: প্যারেন্ট কম্পোনেন্ট যে প্রপ পাস করে।

* `ref`: প্যারেন্ট কম্পোনেন্টের পাস করা `ref` এট্রিবিউট। এই `ref` হতে পারে অবজেক্ট বা ফাংশন। যদি প্যারেন্ট কম্পোনেন্ট কোন ref পাস না করে থাকে, এটা `null` হবে। আপনার রিসিভ করা `ref` অন্য একটি কম্পোনেন্টে পাস করা উচিত, অথবা [`useImperativeHandle` এ পাস করা উচিত।](/reference/react/useImperativeHandle)

#### রিটার্ন {/*render-returns*/}

`forwardRef` একটি React কম্পোনেন্ট রিটার্ন করে যেটা আপনি JSX এ রেন্ডার করতে পারবেন। Plain ফাংশন হিসেবে সংজ্ঞায়িত React কম্পোনেন্টের সাথে এর অমিল এখানেই যে, `forwardRef` থেকে রিটার্ন করা কম্পোনেন্ট একটি `ref` প্রপ নিতে পারে।

---

## ব্যবহার {/*usage*/}

### প্যারেন্ট কম্পোনেন্টে একটি DOM নোড এক্সপোজ করা {/*exposing-a-dom-node-to-the-parent-component*/}

ডিফল্ট ভাবে, প্রতিটা কম্পোনেন্টের DOM নোড প্রাইভেট। তবে, কখনো কখনো প্যারেন্টের দিকে একটা DOM নোড এক্সপোজ করা কাজে লাগতে পারে--যেমন, একে ফোকাসের সুযোগ দেবার জন্য। রটা করার জন্য আপনার কম্পোনেন্ট ডেফিনিশন  `forwardRef()` দিয়ে wrap করে ফেলুনঃ

```js {3,11}
import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const { label, ...otherProps } = props;
  return (
    <label>
      {label}
      <input {...otherProps} />
    </label>
  );
});
```

আপনি props এর পর দ্বিতীয় আর্গুমেন্ট হিসেবে একটি <CodeStep step={1}>ref</CodeStep> রিসিভ করবেন। আপনি যেই DOM নোড এক্সপোজ করতে চান সেখানে এটি পাস করে দিনঃ

```js {8} [[1, 3, "ref"], [1, 8, "ref", 30]]
import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const { label, ...otherProps } = props;
  return (
    <label>
      {label}
      <input {...otherProps} ref={ref} />
    </label>
  );
});
```

এইটা প্যারেন্ট `Form` কম্পোনেন্টকে `MyInput` এর কারণে এক্সপোজ হওয়া <CodeStep step={2}>`<input>` DOM নোড</CodeStep> এক্সেসের সুযোগ দেয়।  

```js [[1, 2, "ref"], [1, 10, "ref", 41], [2, 5, "ref.current"]]
function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
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

এই `Form` কম্পোনেন্টটি `MyInput` এ [একটি ref পাস করে।](/reference/react/useRef#manipulating-the-dom-with-a-ref) `MyInput` কম্পোনেন্ট এই ref কে `<input>` ব্রাউজার ট্যাগে  *ফরোয়ার্ড* করে দেয়। ফলে, `Form` কম্পোনেন্ট ঐ `<input>` DOM নোডে এক্সেস করতে পারে এবং এতে [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) কল দিতে পারে।

মনে রাখবেন যে, আপনার কম্পোনেন্টের মধ্যে একটি ref এক্সপোজ করার ফোলে আপনার কম্পোনেন্টের ভেতরকার তথ্য পরিবর্তন করা কঠিন হয়ে পড়ে। আপনি বাটন বা টেক্সট ইনপুটের মত পুনরায় ব্যবহারযোগ্য লো-লেভেল কম্পোনেন্ট থেকেই সাধরণত DOM nodes এক্সপোজ করবেন, কিন্তু আপনি এপ্লিকেশন-লেভেল কম্পোনেন্ট যেমন avatar বা কমেন্ট এর জন্য এটি করবেন না।

<Recipes titleText="ref ফরোওয়ার্ডের উদাহরণ">

#### একটি টেক্সট ইনপুটে ফোকাস করা {/*focusing-a-text-input*/}

বাটনে ক্লিক করা হলে ইনপুট ফোকাস হবে। `Form` কম্পোনেন্ট একটি ref ডিফাইন করে এবং `MyInput` কম্পোনেন্টে পাস করে দেয়। `MyInput` কম্পোনেন্ট সেই ref টি ব্রাউজার `<input>` এ ফরোয়ার্ড করে দেয়। এর কারণে `Form` কম্পোনেন্ট `<input>` এ ফোকাস করতে পারে।

<Sandpack>

```js
import { useRef } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
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
import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const { label, ...otherProps } = props;
  return (
    <label>
      {label}
      <input {...otherProps} ref={ref} />
    </label>
  );
});

export default MyInput;
```

```css
input {
  margin: 5px;
}
```

</Sandpack>

<Solution />

#### একটি ভিডিও চালানো এবং থামানো {/*playing-and-pausing-a-video*/}

বাটনে ক্লিক করা হলে `<video>` DOM এ [`play()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play) এবং [`pause()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause) কল হবে। `App` কম্পোনেন্ট একটি ref ডিফাইন করে এবং `MyVideoPlayer` কম্পোনেন্টে পাস করে দেয়। The `MyVideoPlayer` কম্পোনেন্ট সেই ref টি ব্রাউজার `<video>` নোডে ফরোয়ার্ড করে দেয়। এটি `App` কম্পোনেন্টকে `<video>` চালানো এবং থামানোর সুযোগ দেয়।

<Sandpack>

```js
import { useRef } from 'react';
import MyVideoPlayer from './MyVideoPlayer.js';

export default function App() {
  const ref = useRef(null);
  return (
    <>
      <button onClick={() => ref.current.play()}>
        Play
      </button>
      <button onClick={() => ref.current.pause()}>
        Pause
      </button>
      <br />
      <MyVideoPlayer
        ref={ref}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
        type="video/mp4"
        width="250"
      />
    </>
  );
}
```

```js MyVideoPlayer.js
import { forwardRef } from 'react';

const VideoPlayer = forwardRef(function VideoPlayer({ src, type, width }, ref) {
  return (
    <video width={width} ref={ref}>
      <source
        src={src}
        type={type}
      />
    </video>
  );
});

export default VideoPlayer;
```

```css
button { margin-bottom: 10px; margin-right: 10px; }
```

</Sandpack>

<Solution />

</Recipes>

---

### একাধিক কম্পোনেন্টের মধ্য দিয়ে ref ফরোয়ার্ড {/*forwarding-a-ref-through-multiple-components*/}

DOM নোডে `ref` ফরোয়ার্ড করার বদলে, আপনি এটি আপনার নিজের কম্পোনেন্ট যেমন `MyInput` এ ফরোয়ার্ড করতে পারেনঃ

```js {1,5}
const FormField = forwardRef(function FormField(props, ref) {
  // ...
  return (
    <>
      <MyInput ref={ref} />
      ...
    </>
  );
});
```

যদি `MyInput` কম্পোনেন্ট তার `<input>` এ একটি ref ফরোয়ার্ড করে, `FormField` এর ref আপনাকে সেই `<input>` দিবেঃ

```js {2,5,10}
function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
  }

  return (
    <form>
      <FormField label="Enter your name:" ref={ref} isRequired={true} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

`Form` কম্পোনেন্ট একটি ref ডিফাইন করে এবং `FormField` কম্পোনেন্টে পাস করে দেয়। `FormField` কম্পোনেন্ট সেই ref টি `MyInput` এ ফরোয়ার্ড করে, যা একে ব্রাউজার DOM নোড `<input>` এ ফরোয়ার্ড করে। এই ভাবে `Form` ঐ DOM নোড এক্সেস করে।


<Sandpack>

```js
import { useRef } from 'react';
import FormField from './FormField.js';

export default function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
  }

  return (
    <form>
      <FormField label="Enter your name:" ref={ref} isRequired={true} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

```js FormField.js
import { forwardRef, useState } from 'react';
import MyInput from './MyInput.js';

const FormField = forwardRef(function FormField({ label, isRequired }, ref) {
  const [value, setValue] = useState('');
  return (
    <>
      <MyInput
        ref={ref}
        label={label}
        value={value}
        onChange={e => setValue(e.target.value)} 
      />
      {(isRequired && value === '') &&
        <i>Required</i>
      }
    </>
  );
});

export default FormField;
```


```js MyInput.js
import { forwardRef } from 'react';

const MyInput = forwardRef((props, ref) => {
  const { label, ...otherProps } = props;
  return (
    <label>
      {label}
      <input {...otherProps} ref={ref} />
    </label>
  );
});

export default MyInput;
```

```css
input, button {
  margin: 5px;
}
```

</Sandpack>

---

### DOM নোডের বদলে একটি imperative handle এক্সপোজ করা {/*exposing-an-imperative-handle-instead-of-a-dom-node*/}

সম্পূর্ণ DOM নোড এক্সপোজ করবার বদলে, আপনি একটি কাস্টম অবজেক্ট এক্সপোজ করতে পারেন, যাকে *imperative handle* বলা হয়, যার সীমিত কিছু মেথড আছে। এটা করার জন্য, DOM নোড ধরে রাখতে আপনাকে একটি আলাদা ref ডিফাইন করতে হবেঃ

```js {2,6}
const MyInput = forwardRef(function MyInput(props, ref) {
  const inputRef = useRef(null);

  // ...

  return <input {...props} ref={inputRef} />;
});
```

আপনার রিসিভ করা `ref`, [`useImperativeHandle`](/reference/react/useImperativeHandle) এ পাস করে করে দিন এবং আপনি যেই ভ্যালু `ref` এ এক্সপোজ করতে চান সেটা নির্দেশ করে দিনঃ

```js {6-15}
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

যদি কোন কম্পোনেন্ট `MyInput` এর ref পায়, এটা DOM নোডের বদলে কেবল আপনার `{ focus, scrollIntoView }` অবজেক্ট রিসিভ করবে। এভাবে আপনি আপনার DOM নোডের তথ্যের সর্বনিম্ন পরিমাণ এক্সপোজ হবে।

<Sandpack>

```js
import { useRef } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
    // This won't work because the DOM node isn't exposed:
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

[Imperative handles সম্বন্ধে আরো পড়ুন।](/reference/react/useImperativeHandle)

<Pitfall>

**Ref অতিরিক্ত ব্যবহার করবেন না।** শুধুমাত্র *imperative* আচরণ যা আপনি prop দিয়ে দেখাতে পারবেন না সেগুলোর ক্ষেত্রেই ref ব্যবহার করবেনঃ যেমন, নোড স্ক্রোল করা, নোড ফোকাস করা, এনিমশন ট্রিগার করা, টেক্সট সিলেক্ট করা, ইত্যাদি।

**আপনি যদি কোন কিছু prop এর মাধ্যমে উন্মুক্ত করতে পারেন, তবে ref ব্যবহার করা উচিত হবে না।** যেমন, `Modal` কম্পোনেন্ট থেকে এর মত একটি imperative handle এক্সপোজ করার বদলে, `<Modal isOpen={isOpen} />` এর মত করে `isOpen` prop নেওয়া বেশি ভাল হবে। [Effects](/learn/synchronizing-with-effects) prop এর মাধ্যমে আপনাকে imperative আচরণ এক্সপোজের সুযোগ দেবে।

</Pitfall>

---

## ট্রাবলশ্যুট {/*troubleshooting*/}

### আমার কম্পোনেন্ট `forwardRef` এর মধ্যে wrap করা, কিন্তু এর `ref` সবসময় `null` {/*my-component-is-wrapped-in-forwardref-but-the-ref-to-it-is-always-null*/}

সাধারণত এর অর্থ হল আপনি যেই `ref` রিসিভ করেছেন, সেটা ব্যবহার করতে ভুলে গেছেন।

উদাহরণস্বরূপ, এই কম্পোনেন্ট এই `ref` এর সাথে কিছু করে নাঃ

```js {1}
const MyInput = forwardRef(function MyInput({ label }, ref) {
  return (
    <label>
      {label}
      <input />
    </label>
  );
});
```

এটা ঠিক করার জন্য, `ref` কে নিচে DOM নোড বা অন্য এমন কোন কম্পোনেন্ট যা ref গ্রহণ করতে পারে সে পর্যন্ত নিয়ে যানঃ

```js {1,5}
const MyInput = forwardRef(function MyInput({ label }, ref) {
  return (
    <label>
      {label}
      <input ref={ref} />
    </label>
  );
});
```

যদি কিছু লজিক কন্ডিশনাল হয় সেক্ষেত্রেও `MyInput` এর `ref` `null` হতে পারেঃ

```js {1,5}
const MyInput = forwardRef(function MyInput({ label, showInput }, ref) {
  return (
    <label>
      {label}
      {showInput && <input ref={ref} />}
    </label>
  );
});
```

যদি `showInput` `false` হয়, তাহলে ref কোন নোডে ফরোয়ার্ড হবে না, এবং `MyInput` এর একটি ref ফাঁকা থাকবে। বিশেষ করে এই বিষয়টি সহজেই উপেক্ষিত হতে পারে যদি কন্ডিশন অন্য কোন কম্পোনেন্টের মধ্যে লুকিয়ে থাকে, যেমন এই উদাহরণে `Panel`:

```js {5,7}
const MyInput = forwardRef(function MyInput({ label, showInput }, ref) {
  return (
    <label>
      {label}
      <Panel isExpanded={showInput}>
        <input ref={ref} />
      </Panel>
    </label>
  );
});
```
