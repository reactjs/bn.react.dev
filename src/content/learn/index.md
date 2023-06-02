---
title: Quick Start
---

<Intro>

React ডকুমেন্টেশনে স্বাগতম! এই পৃষ্ঠাটি আপনাকে ৮০% ভূমিকা দেবে React এর, যা আপনি প্রতিদিন ব্যবহার করবেন।

</Intro>

<YouWillLearn>

- কিভাবে কম্পোনেন্টস তৈরী করবেন এবং নেস্ট করবেন 
- কিভাবে মার্কআপ এবং স্টাইলস সংযুক্ত করবেন 
- কিভাবে ডেটা প্রদর্শন করবেন
- কিভাবে কন্ডিশনস এবং লিস্টস গুলো রেন্ডার করবেন
- কিভাবে ইভেন্ট গুলো রেস্পন্ড করবেন এবং সেই অনুযায়ী স্ক্রিন আপডেট করবেন
- কিভাবে কম্পোনেন্টস এর মধ্যে ডেটা শেয়ার করবেন

</YouWillLearn>

## কম্পোনেন্টস তৈরী এন্ড নেস্টিং {/*components*/}

React অ্যাপগুলি *কম্পোনেন্টস* দিয়ে তৈরি। একটি কম্পোনেন্ট হল ইউএই (ইউজার ইন্টারফেস) এর একটি অংশ যার নিজস্ব লজিক এবং এপিয়ারেন্স রয়েছে। একটি কম্পোনেন্ট একটি বাটনের মতো ছোট বা একটি সম্পূর্ণ পেজ এর মতো বড় হতে পারে।

React কম্পোনেন্ট গুলি হল জাভাস্ক্রিপ্ট ফাংশন যা মার্কআপ রিটার্ন করে:

```js
function MyButton() {
  return (
    <button>I'm a button</button>
  );
}
```

এখন আপনি `MyButton` ডিক্লেয়ার করেছেন, এটিকে অন্য কম্পোনেন্টে নেস্ট করতে পারেন:


```js {5}
export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton />
    </div>
  );
}
```

লক্ষ্য করুন যে `<MyButton />` একটি বড় অক্ষর দিয়ে শুরু হয়েছে। এইভাবে আপনি বলতে পারেন যে এটি একটি react কম্পোনেন্ট। সর্বদা, React কম্পোনেন্টের নামগুলি একটি বড় অক্ষর, আর HTML ট্যাগগুলি ছোট হাতের অক্ষর দিয়ে শুরু হওয়া উচিত।

ফলাফল দেখুন:

<Sandpack>

```js
function MyButton() {
  return (
    <button>
      I'm a button
    </button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton />
    </div>
  );
}
```

</Sandpack>

এই `export default` কীওয়ার্ডস নির্দিষ্ট করে দেয় যে কোনটি ফাইলের প্রধান কম্পোনেন্ট। আপনি যদি জাভাস্ক্রিপ্ট সিনট্যাক্সের কিছু অংশের সাথে পরিচিত না হন, [MDN](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export) এবং [javascript.info](https://javascript.info/import-export) এর মধ্যে চমৎকার রেফারেন্স আছে পরিচিত হবার।

## JSX দিয়ে মার্কআপ লেখা {/*writing-markup-with-jsx*/}

আপনি উপরে যে মার্কআপ সিনট্যাক্সটি দেখেছেন তাকে বলা হয় *JSX*। এটি ঐচ্ছিক, তবে বেশিরভাগ React প্রজেক্ট গুলিতে সুবিধার জন্য JSX ব্যবহার করা হয়। [লোকাল ডেভেলপমেন্টের জন্য আমরা যে সমস্ত টুলের পরামর্শ দিই](/learn/installation) সেগুলির সবকটিই JSX সমর্থন করে।

JSX HTML এর চেয়ে বেশি স্ট্রিক্ট। আপনাকে `<br />` এর মত ট্যাগ বন্ধ করতে হবে। আপনার কম্পোনেন্ট একাধিক JSX ট্যাগ রিটার্ন করতে পারবে না। সেগুলিকে একটি parent div এর মধ্যে মোড়াতে হবে, যেমন একটি `<div>...</div>` বা একটি খালি `<>...</>` মোড়ক:

```js {3,6}
function AboutPage() {
  return (
    <>
      <h1>About</h1>
      <p>Hello there.<br />How do you do?</p>
    </>
  );
}
```

JSX এ পোর্ট করার জন্য আপনার যদি অনেকগুলো HTML থাকে, আপনি একটি [অনলাইন কনভার্টার](https://transform.tools/html-to-jsx) ব্যবহার করতে পারেন।

## Styles যোগ করা {/*adding-styles*/}

React এ আপনি `className` সহ একটি CSS ক্লাস নির্দিষ্ট করেন। যা একেবারে HTML এর মতই কাজ করে [`class`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/class) attribute:

```js
<img className="avatar" />
```

তারপর আপনি একটি পৃথক CSS ফাইলে এটির জন্য CSS নিয়ম লিখুন:

```css
/* In your CSS */
.avatar {
  border-radius: 50%;
}
```

আপনি কিভাবে CSS ফাইল যোগ করবেন, তা নিয়ে React কোনো কিছুই নির্ধারণ করে না। সবচেয়ে সহজ ক্ষেত্রে, আপনি আপনার HTML-এ একটি [`<link>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link) ট্যাগ যোগ করবেন। আপনি যদি একটি বিল্ড টুল বা ফ্রেমওয়ার্ক ব্যবহার করেন, তাহলে আপনার প্রোজেক্টে কিভাবে একটি CSS ফাইল যোগ করবেন তা জানতে তাদের ডকুমেন্টেশন গুলো দেখুন।

## Data প্রদর্শন করা {/*displaying-data*/}

JSX আপনাকে জাভাস্ক্রিপ্টে মার্কআপ ব্যবহার করতে দেয়। Curly braces আপনাকে জাভাস্ক্রিপ্টে "escape back" করতে দেয় যাতে আপনি আপনার কোড থেকে কিছু ভ্যারিয়েবল embed করতে পারেন এবং ব্যবহারকারীর কাছে এটি প্রদর্শন করতে পারেন। উদাহরণস্বরূপ, এটি প্রদর্শন করবে `user.name`:

```js {3}
return (
  <h1>
    {user.name}
  </h1>
);
```

আপনি JSX অ্যাট্রিবিউটগুলি থেকে "escape into JavaScript" করতে পারেন, তবে আপনাকে quotes এর *পরিবর্তে* curly braces ব্যবহার করতে হবে।  উদাহরণ স্বরূপ, `className="avatar"` CSS ক্লাস হিসেবে `"avatar"` স্ট্রিংকে pass করে, কিন্তু `src={user.imageUrl}` জাভাস্ক্রিপ্ট `user.imageUrl` ভেরিয়েবল মানটি  পড়ে এবং তারপর সেই মানটিকে এভাবে পাস করে `src` বৈশিষ্ট্য:

```js {3,4}
return (
  <img
    className="avatar"
    src={user.imageUrl}
  />
);
```

আপনি JSX curly braces এর ভিতরে আরও জটিল expressions রাখতে পারেন, উদাহরণস্বরূপ, [string concatenation](https://javascript.info/operators#string-concatenation-with-binary):

<Sandpack>

```js
const user = {
  name: 'Hedy Lamarr',
  imageUrl: 'https://i.imgur.com/yXOvdOSs.jpg',
  imageSize: 90,
};

export default function Profile() {
  return (
    <>
      <h1>{user.name}</h1>
      <img
        className="avatar"
        src={user.imageUrl}
        alt={'Photo of ' + user.name}
        style={{
          width: user.imageSize,
          height: user.imageSize
        }}
      />
    </>
  );
}
```

```css
.avatar {
  border-radius: 50%;
}

.large {
  border: 4px solid gold;
}
```

</Sandpack>

উপরের উদাহরণে, `style={{}}` বিশেষ কোনো সিনট্যাক্স নয়, এখানে `style={ }` শুধুমাত্র jsx curly braces এর ভিতরে একটি নিয়মিত `{}` অবজেক্ট। যখন আপনার styles গুলো জাভাস্ক্রিপ্ট ভেরিয়েবলের উপর নির্ভর করবে তখন আপনি `style` অ্যাট্রিবিউট ব্যবহার করতে পারেন।

## শর্তসাপেক্ষে rendering {/*conditional-rendering*/}

React এ শর্ত লেখার জন্য কোন বিশেষ বাক্য গঠন নেই। যার ফলে, আপনি নিয়মিত জাভাস্ক্রিপ্ট কোড লেখার সময় যে কৌশলগুলি ব্যবহার করতে পারেন সেই একই কৌশলগুলি এখানে ব্যবহার করতে পারবেন।
 উদাহরণস্বরূপ, শর্তসাপেক্ষে JSX অন্তর্ভুক্ত করতে, আপনি একটি [`if`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) বিবৃতি ব্যবহার করতে পারেন:

```js
let content;
if (isLoggedIn) {
  content = <AdminPanel />;
} else {
  content = <LoginForm />;
}
return (
  <div>
    {content}
  </div>
);
```

আপনি যদি আরও কমপ্যাক্ট কোড পছন্দ করেন তবে আপনি [শর্তাধীন `?` অপারেটর ব্যবহার করতে পারেন।](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) `if` এর বিপরীত , এটি JSX এর ভিতরে কাজ করে:

```js
<div>
  {isLoggedIn ? (
    <AdminPanel />
  ) : (
    <LoginForm />
  )}
</div>
```
আপনার যখন `else ` শাখার প্রয়োজন হয় না, আপনি একটি সংক্ষিপ্ত [লজিক্যাল `&&` সিনট্যাক্স] ব্যবহার করতে পারেন(https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND#short-circuit_evaluation):

```js
<div>
  {isLoggedIn && <AdminPanel />}
</div>
```

এই সমস্ত পদ্ধতিগুলি শর্তসাপেক্ষে নির্দিষ্ট বৈশিষ্ট্যগুলির জন্যও কাজ করে। আপনি যদি এই জাভাস্ক্রিপ্ট সিনট্যাক্সের কিছুর সাথে অপরিচিত হন তবে আপনি সর্বদা `if...else` ব্যবহার করে শুরু করতে পারেন।

## Rendering তালিকা {/*rendering-lists*/}

আপনি [`for ` লুপ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for) এবং [array `map()` ফাংশনের মতো জাভাস্ক্রিপ্ট features এর উপর নির্ভর করবেন ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) কম্পোনেন্টস এর তালিকা রেন্ডার করতে।

উদাহরণস্বরূপ, ধরুন আপনার কাছে products এর একটি array আছে:

```js
const products = [
  { title: 'Cabbage', id: 1 },
  { title: 'Garlic', id: 2 },
  { title: 'Apple', id: 3 },
];
```
আপনার কম্পোনেন্টের ভিতরে, products এর একটি অ্যারেকে `<li>` আইটেমগুলির array তে রূপান্তর করতে `map()` ফাংশন ব্যবহার করুন:

```js
const listItems = products.map(product =>
  <li key={product.id}>
    {product.title}
  </li>
);

return (
  <ul>{listItems}</ul>
);
```

লক্ষ্য করুন কিভাবে `<li>` এর একটি `key ` attribute আছে। একটি তালিকার প্রতিটি আইটেমের জন্য, আপনাকে একটি স্ট্রিং বা একটি সংখ্যা পাস করতে হবে যা সেই আইটেমটিকে তার ভাইবোনদের মধ্যে অনন্যভাবে সনাক্ত করে। সাধারণত, একটি কী আপনার ডেটা থেকে আসা উচিত, যেমন একটি ডাটাবেস আইডি। আপনি পরে আইটেমগুলি সন্নিবেশ, মুছে বা পুনর্বিন্যাস করলে কী ঘটেছিল তা জানতে React আপনার key গুলি ব্যবহার করে৷

<Sandpack>

```js
const products = [
  { title: 'Cabbage', isFruit: false, id: 1 },
  { title: 'Garlic', isFruit: false, id: 2 },
  { title: 'Apple', isFruit: true, id: 3 },
];

export default function ShoppingList() {
  const listItems = products.map(product =>
    <li
      key={product.id}
      style={{
        color: product.isFruit ? 'magenta' : 'darkgreen'
      }}
    >
      {product.title}
    </li>
  );

  return (
    <ul>{listItems}</ul>
  );
}
```

</Sandpack>

## Events এর জন্যে প্রতিক্রিয়া করা {/*responding-to-events*/}

আপনি আপনার উপাদানগুলির মধ্যে *event handler* ফাংশন ঘোষণা করে ইভেন্টগুলিতে প্রতিক্রিয়া জানাতে পারেন:

```js {2-4,7}
function MyButton() {
  function handleClick() {
    alert('You clicked me!');
  }

  return (
    <button onClick={handleClick}>
      Click me
    </button>
  );
}
```

লক্ষ্য করুন কিভাবে `onClick={handleClick}` এর শেষে কোন বন্ধনী নেই! ইভেন্ট হ্যান্ডলার ফাংশন _কল_ করবেন না: আপনাকে শুধুমাত্র এটি পাস করতে হবে। ব্যবহারকারী button টি ক্লিক করলে React আপনার ইভেন্ট হ্যান্ডলারকে কল করবে।

## স্ক্রিন আপডেট করা {/*updating-the-screen*/}

প্রায়শই, আপনি চাইবেন আপনার উপাদান কিছু তথ্য "মনে রাখুক" এবং এটি প্রদর্শন করুক। উদাহরণস্বরূপ, হয়ত আপনি একটি বোতামে ক্লিক করার সংখ্যা গণনা করতে চান। এটি করতে, আপনার কম্পোনেন্টে *state* যোগ করুন।

প্রথমে, React থেকে [`useState`](/reference/react/useState) import করুন:

```js
import { useState } from 'react';
```

এখন আপনি আপনার উপাদানের ভিতরে একটি *state variable* ঘোষণা করতে পারেন:


```js
function MyButton() {
  const [count, setCount] = useState(0);
  // ...
```

আপনি `useState` থেকে দুটি জিনিস পাবেন: বর্তমান state (`count`), এবং ফাংশন যা আপনাকে এটি আপডেট করতে দেয় (`setCount`)। আপনি তাদের যে কোনো নাম দিতে পারেন, কিন্তু নিয়ম হল `[something, setSomething]` লিখতে হবে।

প্রথমবার button টি প্রদর্শিত হলে, `count` হবে `0` কারণ আপনি `0`কে `useState()`-এ পাস করেছেন। আপনি যখন state পরিবর্তন করতে চান, `setCount()` কল করুন এবং এটিতে নতুন মান পাস করুন। এই button টি ক্লিক করলে কাউন্টারটি বৃদ্ধি পাবে:

```js {5}
function MyButton() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      Clicked {count} times
    </button>
  );
}
```

React আপনার কম্পোনেন্ট ফাংশনকে আবার কল করবে। এবার, `count` হবে `1`। পরবর্তীতে এটা হবে `2` এবং এভাবে বাড়তে থাকবে।

আপনি যদি একই component একাধিকবার render করেন তবে প্রতিটি তার নিজস্ব state পাবে। প্রতিটি button এ আলাদাভাবে ক্লিক করুন:

<Sandpack>

```js
import { useState } from 'react';

export default function MyApp() {
  return (
    <div>
      <h1>যে কাউন্টারগুলো আলাদাভাবে আপডেট হয়</h1>
      <MyButton />
      <MyButton />
    </div>
  );
}

function MyButton() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      Clicked {count} times
    </button>
  );
}
```

```css
button {
  display: block;
  margin-bottom: 5px;
}
```

</Sandpack>

লক্ষ্য করুন কিভাবে প্রতিটি button তার নিজস্ব `count` state  কে "মনে রাখে" এবং অন্যান্য button গুলিকে প্রভাবিত না করে।

## Using Hooks {/*using-hooks*/}

Functions starting with `use` are called *Hooks*. `useState` is a built-in Hook provided by React. You can find other built-in Hooks in the [API reference.](/reference/react) You can also write your own Hooks by combining the existing ones.

Hooks are more restrictive than other functions. You can only call Hooks *at the top* of your components (or other Hooks). If you want to use `useState` in a condition or a loop, extract a new component and put it there.

## Sharing data between components {/*sharing-data-between-components*/}

In the previous example, each `MyButton` had its own independent `count`, and when each button was clicked, only the `count` for the button clicked changed:

<DiagramGroup>

<Diagram name="sharing_data_child" height={367} width={407} alt="Diagram showing a tree of three components, one parent labeled MyApp and two children labeled MyButton. Both MyButton components contain a count with value zero.">

Initially, each `MyButton`'s `count` state is `0`

</Diagram>

<Diagram name="sharing_data_child_clicked" height={367} width={407} alt="The same diagram as the previous, with the count of the first child MyButton component highlighted indicating a click with the count value incremented to one. The second MyButton component still contains value zero." >

The first `MyButton` updates its `count` to `1`

</Diagram>

</DiagramGroup>

However, often you'll need components to *share data and always update together*.

To make both `MyButton` components display the same `count` and update together, you need to move the state from the individual buttons "upwards" to the closest component containing all of them.

In this example, it is `MyApp`:

<DiagramGroup>

<Diagram name="sharing_data_parent" height={385} width={410} alt="Diagram showing a tree of three components, one parent labeled MyApp and two children labeled MyButton. MyApp contains a count value of zero which is passed down to both of the MyButton components, which also show value zero." >

Initially, `MyApp`'s `count` state is `0` and is passed down to both children

</Diagram>

<Diagram name="sharing_data_parent_clicked" height={385} width={410} alt="The same diagram as the previous, with the count of the parent MyApp component highlighted indicating a click with the value incremented to one. The flow to both of the children MyButton components is also highlighted, and the count value in each child is set to one indicating the value was passed down." >

On click, `MyApp` updates its `count` state to `1` and passes it down to both children

</Diagram>

</DiagramGroup>

Now when you click either button, the `count` in `MyApp` will change, which will change both of the counts in `MyButton`. Here's how you can express this in code.

First, *move the state up* from `MyButton` into `MyApp`:

```js {2-6,18}
export default function MyApp() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Counters that update separately</h1>
      <MyButton />
      <MyButton />
    </div>
  );
}

function MyButton() {
  // ... we're moving code from here ...
}

```

Then, *pass the state down* from `MyApp` to each `MyButton`, together with the shared click handler. You can pass information to `MyButton` using the JSX curly braces, just like you previously did with built-in tags like `<img>`:

```js {11-12}
export default function MyApp() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Counters that update together</h1>
      <MyButton count={count} onClick={handleClick} />
      <MyButton count={count} onClick={handleClick} />
    </div>
  );
}
```

The information you pass down like this is called _props_. Now the `MyApp` component contains the `count` state and the `handleClick` event handler, and *passes both of them down as props* to each of the buttons.

Finally, change `MyButton` to *read* the props you have passed from its parent component:

```js {1,3}
function MyButton({ count, onClick }) {
  return (
    <button onClick={onClick}>
      Clicked {count} times
    </button>
  );
}
```

When you click the button, the `onClick` handler fires. Each button's `onClick` prop was set to the `handleClick` function inside `MyApp`, so the code inside of it runs. That code calls `setCount(count + 1)`, incrementing the `count` state variable. The new `count` value is passed as a prop to each button, so they all show the new value. This is called "lifting state up". By moving state up, you've shared it between components.

<Sandpack>

```js
import { useState } from 'react';

export default function MyApp() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Counters that update together</h1>
      <MyButton count={count} onClick={handleClick} />
      <MyButton count={count} onClick={handleClick} />
    </div>
  );
}

function MyButton({ count, onClick }) {
  return (
    <button onClick={onClick}>
      Clicked {count} times
    </button>
  );
}
```

```css
button {
  display: block;
  margin-bottom: 5px;
}
```

</Sandpack>

## Next Steps {/*next-steps*/}

By now, you know the basics of how to write React code!

Check out the [Tutorial](/learn/tutorial-tic-tac-toe) to put them into practice and build your first mini-app with React.
