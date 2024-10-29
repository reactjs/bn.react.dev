---
title: কন্ডিশনাল বা শর্তসাপেক্ষ রেন্ডারিং
---

<Intro>

আপনার কম্পোনেন্টগুলো বিভিন্ন শর্তের ওপর ভিত্তি করে বিভিন্ন কিছু প্রদর্শন করতে পারে। React-এ, `if` স্টেটমেন্ট, `&&`, এবং `? :` অপারেটরের মতো জাভাস্ক্রিপ্ট সিনট্যাক্স ব্যবহার করে JSX শর্তানুযায়ী রেন্ডার করতে পারেন।

</Intro>

<YouWillLearn>

* কিভাবে শর্তানুযায়ী ভিন্ন JSX রিটার্ন করবেন
* কিভাবে কোনো JSX কে শর্তানুযায়ী অন্তর্ভুক্ত বা বাদ দিবেন
* সাধারণ শর্তগত সিনট্যাক্স শর্টকাটগুলো, যা আপনি React কোডবেসে দেখতে পাবেন

</YouWillLearn>

## Conditionally returning JSX {/*conditionally-returning-jsx*/}

ধরুন আপনার একটি `PackingList` কম্পোনেন্ট আছে, যা কয়েকটি `Item` রেন্ডার করে, যেগুলোকে প্যাক করা হয়েছে বা হয়নি এমন হিসেবে চিহ্নিত করা যায়:

<Sandpack>

```js
function Item({ name, isPacked }) {
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

দেখুন, কিছু `Item` কম্পোনেন্টে `isPacked` প্রপ `true` সেট করা হয়েছে, আর কিছুতে `false`। আপনি প্যাক করা আইটেমগুলোতে একটি চেকমার্ক (✅) যোগ করতে চান যদি `isPacked={true}` হয়।

এটি আপনি [`if`/`else` statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) স্টেটমেন্ট দিয়ে লিখতে পারেন, যেমন:

```js
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;
```

যদি `isPacked` প্রপ `true` হয়, এই কোড **একটি ভিন্ন JSX ট্রি রিটার্ন করবে**। এই পরিবর্তনের মাধ্যমে কিছু আইটেমের শেষে একটি চেকমার্ক যোগ হবে।

<Sandpack>

```js
function Item({ name, isPacked }) {
  if (isPacked) {
    return <li className="item">{name} ✔</li>;
  }
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

যেকোনো ক্ষেত্রে কী রিটার্ন করা হচ্ছে তা এডিট করে দেখুন, এবং ফলাফল কিভাবে পরিবর্তিত হচ্ছে তা লক্ষ্য করুন!

খেয়াল করুন কিভাবে JavaScript-এর `if` এবং `return` স্টেটমেন্ট ব্যবহার করে আপনি ব্রাঞ্চিং লজিক তৈরি করছেন। React-এ কন্ট্রোল ফ্লো (যেমন কন্ডিশন) JavaScript দ্বারা হ্যান্ডেল করা হয়।

### ### `null` দিয়ে কিছুই রেন্ডার না করা {/*conditionally-returning-nothing-with-null*/}

কিছু পরিস্থিতিতে, আপনি কোনো কিছুই রেন্ডার করতে চাইবেন না। উদাহরণস্বরূপ, ধরুন আপনি প্যাক করা আইটেমগুলো একদম দেখাতে চান না। এমন পরিস্থিতিতে, আপনি `null` রিটার্ন করতে পারেন:

```js
if (isPacked) {
  return null;
}
return <li className="item">{name}</li>;
```

যদি `isPacked` সত্য হয়, কম্পোনেন্টটি কিছুই রিটার্ন করবে না, অর্থাৎ `null` রিটার্ন করবে। অন্যথায়, এটি JSX রিটার্ন করবে।

<Sandpack>

```js
function Item({ name, isPacked }) {
  if (isPacked) {
    return null;
  }
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

প্রকৃতপক্ষে, কোনো কম্পোনেন্ট থেকে `null` রিটার্ন করা খুব একটা সাধারণ নয়, কারণ এটি রেন্ডার করার চেষ্টা করা কোনো ডেভেলপারকে বিভ্রান্ত করতে পারে। বরং, আপনি সাধারণত মূল কম্পোনেন্টের JSX-এর মধ্যে শর্তানুযায়ী সেই কম্পোনেন্টটি অন্তর্ভুক্ত বা বাদ দেওয়ার চেষ্টা করবেন। এটি কীভাবে করতে হয় তা এখানে দেওয়া হলো!

## শর্ত-সাপেক্ষে JSX অন্তর্ভুক্ত করা {/*conditionally-including-jsx*/}

উপরের উদাহরণে, আপনি নির্ধারণ করেছেন কোন (যদি থাকে!) JSX ট্রি কম্পোনেন্টটি রিটার্ন করবে। আপনি হয়তো ইতোমধ্যে রেন্ডার আউটপুটে কিছু ডুপ্লিকেশন লক্ষ্য করেছেন:

```js
<li className="item">{name} ✔</li>
```

এটি খুবই অনুরূপ

```js
<li className="item">{name}</li>
```

উভয় শর্তের ক্ষেত্রেই `<li className="item">...</li>` রিটার্ন করছে:

```js
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;
```

যদিও এই পুনরাবৃত্তি ক্ষতিকর নয়, এটি আপনার কোড রক্ষণাবেক্ষণ করা কঠিন করে তুলতে পারে। ধরুন আপনি `className` পরিবর্তন করতে চান? সেক্ষেত্রে আপনাকে কোডের দুই জায়গায় এটি পরিবর্তন করতে হবে! এমন পরিস্থিতিতে, আপনার কোড আরও [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) রাখতে একটু JSX শর্তসাপেক্ষে যুক্ত করতে পারেন।

### শর্তসাপেক্ষ (টার্নারি) অপারেটর (`? :`) {/*conditional-ternary-operator--*/}

JavaScript-এ শর্তসাপেক্ষ এক্সপ্রেশন লেখার একটি সংক্ষিপ্ত সিনট্যাক্স রয়েছে -- [শর্তসাপেক্ষ অপারেটর](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) বা "টার্নারি অপারেটর"। 

এইভাবে লেখার পরিবর্তে:

```js
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;
```

You can write this:

```js
return (
  <li className="item">
    {isPacked ? name + ' ✔' : name}
  </li>
);
```

আপনি এটা এভাবে পড়তে পারেন *"if `isPacked` is true, then (`?`) render `name + ' ✔'`, otherwise (`:`) render `name`"*.

<DeepDive>

#### এই দুটি উদাহরণ কি পুরোপুরি সমতুল্য? {/*are-these-two-examples-fully-equivalent*/}

যদি আপনি অবজেক্ট-ওরিয়েন্টেড প্রোগ্রামিং ব্যাকগ্রাউন্ড থেকে এসে থাকেন, তাহলে হয়তো মনে করবেন যে উপরোক্ত দুটি উদাহরণ সূক্ষ্মভাবে ভিন্ন, কারণ একটি উদাহরণ সম্ভবত `<li>`-এর দুটি আলাদা "ইনস্ট্যান্স" তৈরি করতে পারে। কিন্তু JSX এলিমেন্টগুলো "ইনস্ট্যান্স" নয়, কারণ এগুলো কোনো অভ্যন্তরীণ স্টেট ধারণ করে না এবং প্রকৃত DOM নোডও নয়। এগুলো হালকা বর্ণনা বা ব্লুপ্রিন্টের মতো। সুতরাং, বাস্তবে এই দুটি উদাহরণ সম্পূর্ণ সমতুল্য। [স্টেট সংরক্ষণ ও রিসেট করা](/learn/preserving-and-resetting-state) বিস্তারিতভাবে ব্যাখ্যা করে কিভাবে এটি কাজ করে।

</DeepDive>

এখন ধরুন আপনি সম্পন্ন আইটেমের টেক্সটটিকে অন্য একটি HTML ট্যাগের মধ্যে জড়ানোর জন্য চান, যেমন `<del>` যাতে এটি আউটলাইন করা যায়। আপনি আরও বেশি নিউলাইন এবং বন্ধনী যোগ করতে পারেন যাতে প্রতিটি ক্ষেত্রে আরও JSX নেস্ট করা সহজ হয়।

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {isPacked ? (
        <del>
          {name + ' ✔'}
        </del>
      ) : (
        name
      )}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

এই স্টাইলটি সহজ শর্তগুলোর জন্য ভালো কাজ করে, তবে এটি সংযতভাবে ব্যবহার করুন। যদি আপনার কম্পোনেন্টগুলো অতিরিক্ত নেস্টেড শর্তযুক্ত মার্কআপ দিয়ে অগোছালো হয়ে যায়, তাহলে শিশু কম্পোনেন্টগুলো আলাদা করে আপনার কোডটিকে পরিষ্কার রাখার কথা ভাবুন। React-এ মার্কআপ কোডেরই অংশ, তাই জটিল এক্সপ্রেশনগুলোকে গোছানোর জন্য আপনি ভ্যারিয়েবল ও ফাংশনের মতো টুল ব্যবহার করতে পারেন।

### লজিক্যাল AND অপারেটর (`&&`) {/*logical-and-operator-*/}

আরেকটি সাধারণ শর্টকাট হলো [JavaScript লজিক্যাল AND (`&&`) অপারেটর।](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND#:~:text=The%20logical%20AND%20(%20%26%26%20)%20operator,it%20returns%20a%20Boolean%20value.) React কম্পোনেন্টের ভেতরে এটি প্রায়ই ব্যবহৃত হয় যখন আপনি শর্তটি সত্য হলে কিছু JSX রেন্ডার করতে চান, **বা অন্যথায় কিছুই রেন্ডার করতে চান না।** `&&` ব্যবহার করে, আপনি কেবল তখনই চেকমার্কটি শর্তসাপেক্ষে রেন্ডার করতে পারেন যদি `isPacked` `true` হয়:

```js
return (
  <li className="item">
    {name} {isPacked && '✔'}
  </li>
);
```

এটি এভাবে পড়া যায় *"if `isPacked`, then (`&&`) render the checkmark, otherwise, render nothing"*.

এখানে এটি কার্যকরভাবে দেখানো হলো:

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked && '✔'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

একটি [JavaScript && এক্সপ্রেশন](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND) তার ডান পাশের মানটি (আমাদের ক্ষেত্রে চেকমার্ক) ফেরত দেয় যদি বাম পাশের শর্তটি `true` হয়। কিন্তু যদি শর্তটি `false` হয়, তবে পুরো এক্সপ্রেশনটি `false` হয়ে যায়। React `false`-কে JSX ট্রিতে একটি "ফাঁকা" হিসেবে গণ্য করে, যেমন `null` বা `undefined`, এবং এর স্থানে কিছুই রেন্ডার করে না।


<Pitfall>

**সংখ্যাগুলোকে `&&`-এর বাম পাশে ব্যবহার করবেন না।**

শর্ত পরীক্ষা করার জন্য, JavaScript স্বয়ংক্রিয়ভাবে বাম পাশের মানকে একটি বুলিয়ান এ রূপান্তর করে। তবে, যদি বাম পাশের মান `0` হয়, তাহলে পুরো এক্সপ্রেশন `0` মানটি পায়, এবং React এটি কিছু না রেন্ডার করার পরিবর্তে `0` রেন্ডার করবে।

উদাহরণস্বরূপ, একটি সাধারণ ভুল হলো কোড লেখা `messageCount && <p>New messages</p>` এর মতো। এটি সহজে মনে হতে পারে যে `messageCount` যদি `0` হয় তবে এটি কিছুই রেন্ডার করবে না, কিন্তু আসলে এটি `0` রেন্ডার করে!

এটি ঠিক করতে, বাম পাশের মানটিকে বুলিয়ান করে তুলুন: `messageCount > 0 && <p>New messages</p>`।

</Pitfall>

### শর্ত-সাপেক্ষে একটি ভেরিয়েবলে JSX অ্যাসাইন কর {/*conditionally-assigning-jsx-to-a-variable*/}

যখন শর্টকাটগুলি সাধারণ কোড লেখার পথে বাধা দেয়, তখন একটি `if` স্টেটমেন্ট এবং একটি ভেরিয়েবল ব্যবহার করার চেষ্টা করুন। [`let`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let) দিয়ে সংজ্ঞায়িত ভেরিয়েবলগুলিকে পুনঃনির্ধারণ করা যায়, তাই শুরুতে আপনি প্রদর্শন করতে চান এমন ডিফল্ট কনটেন্টটি দিয়ে শুরু করুন, অর্থাৎ নাম:

```js
let itemContent = name;
```

`if` স্টেটমেন্ট ব্যবহার করে `itemContent`-কে একটি JSX এক্সপ্রেশন পুনঃনির্ধারণ করুন যদি `isPacked` সত্য হয়:

```js
if (isPacked) {
  itemContent = name + " ✔";
}
```

[কার্লি ব্রেসের মাধ্যমে "জাভাস্ক্রিপ্টের জানালা খোলে।](/learn/javascript-in-jsx-with-curly-braces#using-curly-braces-a-window-into-the-javascript-world)" রিটার্নকৃত JSX ট্রিতে ভেরিয়েবলটি কার্লি ব্রেসের সাথে এম্বেড করুন এবং পূর্বে গণনা করা এক্সপ্রেশনটি JSX-এর ভেতরে নেস্ট করুন:

```js
<li className="item">
  {itemContent}
</li>
```

এই স্টাইলটি সবচেয়ে ব্যাখ্যামূলক, তবে এটি সবচেয়ে নমনীয়ও। এখানে এটি কার্যকরভাবে প্রদর্শিত হচ্ছে:

<Sandpack>

```js
function Item({ name, isPacked }) {
  let itemContent = name;
  if (isPacked) {
    itemContent = name + " ✔";
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

আগের মত, এটি শুধুমাত্র টেক্সটের জন্য নয়, বরং যেকোনও অর্বিট্রারি JSX-এর জন্যও কাজ করে:

<Sandpack>

```js
function Item({ name, isPacked }) {
  let itemContent = name;
  if (isPacked) {
    itemContent = (
      <del>
        {name + " ✔"}
      </del>
    );
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

যদি আপনি জাভাস্ক্রিপ্টে নতুন হন, তাহলে বিভিন্ন স্টাইল প্রথমে কিছুটা জটিল মনে হতে পারে। তবে এগুলো শেখা আপনাকে যেকোনো জাভাস্ক্রিপ্ট কোড বুঝতে ও লিখতে সহায়তা করবে — এবং শুধু React কম্পোনেন্টেই নয়! যেটা আপনার সবচেয়ে সহজ মনে হয় তা দিয়ে শুরু করুন, এবং পরবর্তীতে অন্যগুলো মনে না থাকলে এই রেফারেন্সটি আবার দেখে নিতে পারেন।

<Recap>

* React-এ, আপনি JavaScript দিয়ে শাখা লজিক নিয়ন্ত্রণ করেন।
* আপনি একটি `if` বিবৃতি দিয়ে শর্তসাপেক্ষে একটি JSX প্রকাশ্য ফিরিয়ে দিতে পারেন।
* আপনি কিছু JSX শর্তসাপেক্ষে একটি ভেরিয়েবলে সংরক্ষণ করতে পারেন এবং তারপর অন্য JSX-এর ভিতরে এটিকে অন্তর্ভুক্ত করতে পারেন কুরি ব্রেস ব্যবহার করে।
* JSX-এ, `{cond ? <A /> : <B />}` এর অর্থ *"যদি `cond` হয়, তবে `<A />` রেন্ডার করুন, অন্যথায় `<B />` রেন্ডার করুন"*।
* JSX-এ, `{cond && <A />}` এর অর্থ *"যদি `cond` হয়, তবে `<A />` রেন্ডার করুন, অন্যথায় কিছুই নয়"*।
* শর্টকাটগুলো সাধারণ, তবে আপনি যদি সাধারণ `if` ব্যবহার করতে পছন্দ করেন তবে আপনাকে সেগুলো ব্যবহার করতে হবে না।

</Recap>



<Challenges>

#### "অপূর্ণ আইটেমগুলির জন্য `? :` সহ একটি আইকন দেখান" {/*show-an-icon-for-incomplete-items-with--*/}

"`isPacked` যদি `true` না হয় তবে একটি ❌ রেন্ডার করতে শর্তাধীন অপারেটর (`cond ? a : b`) ব্যবহার করুন।"

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked && '✔'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

<Solution>

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked ? '✔' : '❌'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

</Solution>

#### "আইটেমের গুরুত্ব `&&` ব্যবহার করে দেখান {/*show-the-item-importance-with-*/}

এই উদাহরণে, প্রতিটি `Item` একটি সংখ্যাগত `importance` প্রপ পায়। শূন্যের বাইরে গুরুত্ব থাকা আইটেমগুলির জন্য `_ (গুরুত্ব: X) _` ইতালিকসে রেন্ডার করতে `&&` অপারেটর ব্যবহার করুন। আপনার আইটেমের তালিকা এরকম দেখতে হবে:

* স্পেস স্যুট _(গুরুত্ব: ৯)_
* স্বর্ণ পাতা যুক্ত হেলমেট
* ট্যামের ছবি _(গুরুত্ব: ৬)_

দুই লেবেলের মধ্যে একটি স্পেস যুক্ত করতে ভুলবেন না!"

<Sandpack>

```js
function Item({ name, importance }) {
  return (
    <li className="item">
      {name}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          importance={9} 
          name="Space suit" 
        />
        <Item 
          importance={0} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          importance={6} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

<Solution>

এটি কাজটি সঠিকভাবে করবে::

<Sandpack>

```js
function Item({ name, importance }) {
  return (
    <li className="item">
      {name}
      {importance > 0 && ' '}
      {importance > 0 &&
        <i>(Importance: {importance})</i>
      }
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          importance={9} 
          name="Space suit" 
        />
        <Item 
          importance={0} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          importance={6} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

"নোট করুন যে আপনাকে `importance > 0 && ...` লিখতে হবে `importance && ...` এর পরিবর্তে, যাতে `importance` যদি `0` হয়, তাহলে `0` ফলস্বরূপ রেন্ডার না হয়!

এই সমাধানে, নাম এবং গুরুত্ব লেবেলের মধ্যে একটি স্পেস যুক্ত করতে দুটি আলাদা শর্ত ব্যবহার করা হয়েছে। বিকল্পভাবে, আপনি একটি ফ্রাগমেন্ট ব্যবহার করতে পারেন একটি লিডিং স্পেস সহ: `importance > 0 && <> <i>...</i></>` অথবা `<i>` এর ভিতরে অবিলম্বে একটি স্পেস যুক্ত করতে পারেন: `importance > 0 && <i> ...</i>`।

</Solution>

#### একটি সিরিজ `? :` কে `if` এবং ভেরিয়েবল দ্বারা রিফ্যাক্টর করুন {/*refactor-a-series-of---to-if-and-variables*/}

এই `Drink` কম্পোনেন্টটি `name` প্রপ `"tea"` অথবা `"coffee"` এর উপর ভিত্তি করে ভিন্ন তথ্য প্রদর্শন করতে `? :` শর্তগুলির একটি সিরিজ ব্যবহার করে। সমস্যাটি হল প্রতিটি পানীয় সম্পর্কিত তথ্য বিভিন্ন শর্তের মধ্যে ছড়িয়ে ছিটিয়ে আছে। এই কোডটি তিনটি `? :` শর্তের পরিবর্তে একটি একক `if` বিবৃতি ব্যবহার করতে রিফ্যাক্টর করুন।"

<Sandpack>

```js
function Drink({ name }) {
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{name === 'tea' ? 'leaf' : 'bean'}</dd>
        <dt>Caffeine content</dt>
        <dd>{name === 'tea' ? '15–70 mg/cup' : '80–185 mg/cup'}</dd>
        <dt>Age</dt>
        <dd>{name === 'tea' ? '4,000+ years' : '1,000+ years'}</dd>
      </dl>
    </section>
  );
}

export default function DrinkList() {
  return (
    <div>
      <Drink name="tea" />
      <Drink name="coffee" />
    </div>
  );
}
```

</Sandpack>

"আপনি যখন কোডটি `if` ব্যবহার করতে রিফ্যাক্টর করবেন, তখন কি আপনি এটি সরল করার জন্য আরও কিছু ধারণা আছে?

<Solution>

এটি করার জন্য বিভিন্ন উপায় রয়েছে, কিন্তু এখানে একটি শুরু পয়েন্ট রয়েছে:"

<Sandpack>

```js
function Drink({ name }) {
  let part, caffeine, age;
  if (name === 'tea') {
    part = 'leaf';
    caffeine = '15–70 mg/cup';
    age = '4,000+ years';
  } else if (name === 'coffee') {
    part = 'bean';
    caffeine = '80–185 mg/cup';
    age = '1,000+ years';
  }
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{part}</dd>
        <dt>Caffeine content</dt>
        <dd>{caffeine}</dd>
        <dt>Age</dt>
        <dd>{age}</dd>
      </dl>
    </section>
  );
}

export default function DrinkList() {
  return (
    <div>
      <Drink name="tea" />
      <Drink name="coffee" />
    </div>
  );
}
```

</Sandpack>

"এখানে প্রতিটি পানীয়ের তথ্য একসাথে গোষ্ঠীবদ্ধ করা হয়েছে, যা একাধিক শর্তের মধ্যে ছড়িয়ে ছিটিয়ে থাকার পরিবর্তে। এটি ভবিষ্যতে আরও পানীয় যুক্ত করা সহজ করে তোলে।

অন্য একটি সমাধান হবে শর্তটি সম্পূর্ণরূপে অপসারণ করা, তথ্যগুলিকে অবজেক্টে স্থানান্তরিত করে:"

<Sandpack>

```js
const drinks = {
  tea: {
    part: 'leaf',
    caffeine: '15–70 mg/cup',
    age: '4,000+ years'
  },
  coffee: {
    part: 'bean',
    caffeine: '80–185 mg/cup',
    age: '1,000+ years'
  }
};

function Drink({ name }) {
  const info = drinks[name];
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{info.part}</dd>
        <dt>Caffeine content</dt>
        <dd>{info.caffeine}</dd>
        <dt>Age</dt>
        <dd>{info.age}</dd>
      </dl>
    </section>
  );
}

export default function DrinkList() {
  return (
    <div>
      <Drink name="tea" />
      <Drink name="coffee" />
    </div>
  );
}
```

</Sandpack>

</Solution>

</Challenges>
