---
title: কম্পোনেন্টে প্রপস পাস করা
---

<Intro>

React কম্পোনেন্টগুলো *props* ব্যবহার করে একে অপরের সাথে যোগাযোগ করে। প্রতিটি parent কম্পোনেন্ট তার child কম্পোনেন্টগুলোকে props দিয়ে কিছু তথ্য পাঠাতে পারে। Props HTML attributes-এর মত মনে হতে পারে, তবে আপনি এর মাধ্যমে object, array এবং function সহ যেকোনো JavaScript মান পাঠাতে পারেন।

</Intro>

<YouWillLearn>

* কীভাবে একটি কম্পোনেন্টে props পাঠাতে হয়
* কীভাবে একটি কম্পোনেন্ট থেকে props পড়তে হয়
* কীভাবে props-এর জন্য default মান নির্দিষ্ট করতে হয়
* কীভাবে একটি কম্পোনেন্টে কিছু JSX পাঠাতে হয়
* কীভাবে সময়ের সাথে সাথে props পরিবর্তিত হয়

</YouWillLearn>

## সুপরিচিত props {/*familiar-props*/}

প্রপস হলো সেই তথ্য যা আপনি একটি JSX ট্যাগে পাঠান। উদাহরণস্বরূপ, `className`, `src`, `alt`, `width`, এবং `height` হল কিছু প্রপস, যা আপনি একটি `<img>` ট্যাগে পাঠাতে পারেন।

<Sandpack>

```js
function Avatar() {
  return (
    <img
      className="avatar"
      src="https://i.imgur.com/1bX5QH6.jpg"
      alt="Lin Lanying"
      width={100}
      height={100}
    />
  );
}

export default function Profile() {
  return (
    <Avatar />
  );
}
```

```css
body { min-height: 120px; }
.avatar { margin: 20px; border-radius: 50%; }
```

</Sandpack>

আপনি যে প্রপসগুলো `<img>` ট্যাগে পাঠাতে পারেন, সেগুলো পূর্বনির্ধারিত (ReactDOM [HTML স্ট্যান্ডার্ড](https://www.w3.org/TR/html52/semantics-embedded-content.html#the-img-element)-এর সাথে সামঞ্জস্যপূর্ণ)। তবে আপনি *নিজের* কম্পোনেন্টগুলো, যেমন `<Avatar>`,-এ যেকোনো প্রপস পাঠাতে পারেন তাদের কাস্টমাইজ করার জন্য। এটি কীভাবে করবেন, দেখুন!

## একটি কম্পোনেন্টে প্রপস পাঠানো {/*passing-props-to-a-component*/}

এই কোডে, `Profile` কম্পোনেন্টটি এর চাইল্ড কম্পোনেন্ট `Avatar`-এ কোনো প্রপস পাঠাচ্ছে না:

```js
export default function Profile() {
  return (
    <Avatar />
  );
}
```

আপনি দুটি ধাপে `Avatar`-এ কিছু props দিতে পারেন।

### ধাপ ১: child কম্পোনেন্টে props পাঠানো {/*step-1-pass-props-to-the-child-component*/}

প্রথমে, কিছু প্রপস `Avatar`-এ পাঠান। উদাহরণস্বরূপ, দুটি প্রপস পাঠানো যাক: `person` (একটি অবজেক্ট), এবং `size` (একটি সংখ্যা):

```js
export default function Profile() {
  return (
    <Avatar
      person={{ name: 'Lin Lanying', imageId: '1bX5QH6' }}
      size={100}
    />
  );
}
```

<Note>

যদি `person=` এর পর ডাবল কার্লি ব্রেস আপনাকে বিভ্রান্ত করে, মনে রাখুন [এগুলো কেবল একটি অবজেক্ট](https://react.dev/learn/javascript-in-jsx-with-curly-braces#using-double-curlies-css-and-other-objects-in-jsx) JSX-এর কার্লি ব্রেসের ভিতরে।

</Note>

এখন আপনি এই props `Avatar` কম্পোনেন্টের ভিতরে পড়তে পারেন।

### ধাপ ২: চাইল্ড কম্পোনেন্টের ভেতরে প্রপস পড়ুন {/*step-2-read-props-inside-the-child-component*/}

আপনি `function Avatar`-এর ঠিক পরে `({` এবং `})` এর ভিতরে props-এর নামগুলো কমা দিয়ে আলাদা করে লিখে এই props পড়তে পারেন। এভাবে, আপনি সেগুলোকে ভেরিয়েবলের মত ব্যবহার করতে পারেন।

```js
function Avatar({ person, size }) {
  // person and size are available here
}
```

`person` এবং `size` props ব্যবহার করে `Avatar`-এ কিছু logic যোগ করুন, আর আপনার কাজ শেষ।

এখন আপনি বিভিন্ন props-এর সাথে `Avatar`-কে বিভিন্ন উপায়ে রেন্ডার করতে পারেন। মান পরিবর্তন করে দেখুন!

<Sandpack>

```js src/App.js
import { getImageUrl } from './utils.js';

function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <div>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi', 
          imageId: 'YfeOqp2'
        }}
      />
      <Avatar
        size={80}
        person={{
          name: 'Aklilu Lemma', 
          imageId: 'OKS67lh'
        }}
      />
      <Avatar
        size={50}
        person={{ 
          name: 'Lin Lanying',
          imageId: '1bX5QH6'
        }}
      />
    </div>
  );
}
```

```js src/utils.js
export function getImageUrl(person, size = 's') {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
body { min-height: 120px; }
.avatar { margin: 10px; border-radius: 50%; }
```

</Sandpack>

প্রপস আপনাকে প্যারেন্ট এবং চাইল্ড কম্পোনেন্টগুলোকে স্বাধীনভাবে ভাবতে সাহায্য করে। উদাহরণস্বরূপ, আপনি Profile-এর মধ্যে person বা size প্রপস পরিবর্তন করতে পারেন, Avatar কীভাবে সেগুলো ব্যবহার করে তা নিয়ে চিন্তা না করেই। একইভাবে, আপনি Avatar-এ প্রপসগুলো কীভাবে ব্যবহৃত হচ্ছে তা পরিবর্তন করতে পারেন, Profile-এর দিকে না তাকিয়েই।

আপনি প্রপসকে “নব” বা নিয়ন্ত্রণ কাঁটা হিসেবে ভাবতে পারেন যা আপনি সামঞ্জস্য করতে পারেন। এগুলো আসলে ফাংশনের আর্গুমেন্টের মতো কাজ করে—আসলে, প্রপস হচ্ছে আপনার কম্পোনেন্টের একমাত্র আর্গুমেন্ট! React কম্পোনেন্ট ফাংশনগুলো একটি মাত্র আর্গুমেন্ট, অর্থাৎ একটি প্রপস অবজেক্ট গ্রহণ করে।

```js
function Avatar(props) {
  let person = props.person;
  let size = props.size;
  // ...
}
```

সাধারণত আপনাকে সম্পূর্ণ `props` object প্রয়োজন হয় না, তাই আপনি এটি আলাদা props এ ভেঙে ফেলেন।

<Pitfall>

**প্রপস ডিক্লেয়ার করার সময় `{` এবং `}` জোড়া মিস করবেন না** `(` এবং `)` এর ভেতরে:

```js
function Avatar({ person, size }) {
  // ...
}
```

এই সিনট্যাক্সটিকে ["ডিস্ট্রাকচারিং"](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Unpacking_fields_from_objects_passed_as_a_function_parameter) বলা হয় এবং এটি ফাংশন প্যারামিটার থেকে প্রপার্টি পড়ার সমতুল্য।

```js
function Avatar(props) {
  let person = props.person;
  let size = props.size;
  // ...
}
```

</Pitfall>

## প্রপের জন্য একটি ডিফল্ট মান নির্ধারণ করা {/*specifying-a-default-value-for-a-prop*/}

যদি আপনি কোনো প্রপের জন্য ডিফল্ট মান নির্ধারণ করতে চান, যাতে কোনো মান নির্দিষ্ট না থাকলে তা প্রযোজ্য হয়, তাহলে ডিস্ট্রাকচারিংয়ে প্যারামিটারের পরে `=` এবং ডিফল্ট মানটি যোগ করতে পারেন:

```js
function Avatar({ person, size = 100 }) {
  // ...
}
```

এখন, যদি `<Avatar person={...} />` কোনো `size` প্রপ ছাড়া রেন্ডার করা হয়, তাহলে `size` সেট হবে `100`।

ডিফল্ট মানটি কেবল তখনই ব্যবহৃত হবে যদি `size` প্রপটি অনুপস্থিত থাকে অথবা আপনি `size={undefined}` পাঠান। তবে, যদি আপনি `size={null}` বা `size={0}` পাঠান, তাহলে ডিফল্ট মান **ব্যবহৃত হবে না**।

## JSX স্প্রেড syntax এর সাথে props ফরোয়ার্ড করা {/*forwarding-props-with-the-jsx-spread-syntax*/}

কখনও কখনও, props পাঠানো খুবই পুনরাবৃত্তিমূলক হতে পারে:

```js
function Profile({ person, size, isSepia, thickBorder }) {
  return (
    <div className="card">
      <Avatar
        person={person}
        size={size}
        isSepia={isSepia}
        thickBorder={thickBorder}
      />
    </div>
  );
}
```

পুনরাবৃত্তিমূলক কোডে কোনো সমস্যা নেই—এটি আরও বোধগম্য হতে পারে। কিন্তু কখনও কখনও আপনি সংক্ষিপ্ততা মূল্যায়ন করতে পারেন। কিছু কম্পোনেন্ট তাদের সমস্ত props child কম্পোনেন্টে ফরোয়ার্ড করে, যেমন `Profile` `Avatar`-এর সাথে করে। যেহেতু তারা কোনো props সরাসরি ব্যবহার করে না, তাই একটি সংক্ষিপ্ত "স্প্রেড" syntax ব্যবহার করা যেতে পারে:

```js
function Profile(props) {
  return (
    <div className="card">
      <Avatar {...props} />
    </div>
  );
}
```

এটি `Profile` এর সমস্ত props `Avatar` এ ফরোয়ার্ড করে কোনো নাম তালিকা ছাড়াই।

**স্প্রেড syntax নিয়ন্ত্রণের সাথে ব্যবহার করুন।** যদি আপনি এটি প্রতিটি কম্পোনেন্টে ব্যবহার করেন, তাহলে কিছু সমস্যা রয়েছে। সাধারণত, এটি নির্দেশ করে যে আপনাকে আপনার কম্পোনেন্টগুলো ভাগ করা উচিত এবং children JSX হিসেবে পাঠানো উচিত।

## children হিসেবে JSX পাঠানো {/*passing-jsx-as-children*/}

বিল্ট-ইন ব্রাউজার ট্যাগ নেস্ট করা সাধারণ:

```js
<div>
  <img />
</div>
```

আপনার নিজস্ব কম্পোনেন্টগুলো একইভাবে নেস্ট করতে পারেন:

```js
<Card>
  <Avatar />
</Card>
```

যখন আপনি একটি JSX ট্যাগের ভিতরে কন্টেন্ট নেস্ট করেন, parent কম্পোনেন্ট সেই কন্টেন্টকে একটি `children` prop-এ গ্রহণ করবে। উদাহরণস্বরূপ, নিচের `Card` কম্পোনেন্ট একটি `children` prop গ্রহণ করবে যেটি `<Avatar />` সেট করা আছে এবং এটি একটি wrapper div এ রেন্ডার করবে:

<Sandpack>

```js src/App.js
import Avatar from './Avatar.js';

function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

export default function Profile() {
  return (
    <Card>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi',
          imageId: 'YfeOqp2'
        }}
      />
    </Card>
  );
}
```

```js src/Avatar.js
import { getImageUrl } from './utils.js';

export default function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}
```

```js src/utils.js
export function getImageUrl(person, size = 's') {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.card {
  width: fit-content;
  margin: 5px;
  padding: 5px;
  font-size: 20px;
  text-align: center;
  border: 1px solid #aaa;
  border-radius: 20px;
  background: #fff;
}
.avatar {
  margin: 20px;
  border-radius: 50%;
}
```

</Sandpack>

`Card`-এর ভিতরে `<Avatar>` পরিবর্তন করে কিছু টেক্সট যোগানোর চেষ্টা করুন এবং দেখুন কীভাবে `Card` যেকোনো নেস্টেড কন্টেন্টকে র‍্যাপ করতে পারে। এটি জানে না যে এর ভিতরে কী রেন্ডার করা হচ্ছে। আপনি এই নমনীয় pattern অনেক জায়গায় দেখতে পাবেন।

একটি `children` prop সহ কম্পোনেন্টকে এমন একটি "ছিদ্র" হিসেবে ভাবতে পারেন যা parent কম্পোনেন্টগুলো যেকোনো JSX দিয়ে "পূর্ণ" করতে পারে। আপনি ভিজ্যুয়াল wrapper-দের জন্য children prop প্রায়ই ব্যবহার করবেন: প্যানেল, গ্রিড ইত্যাদি।

<Illustration src="/images/docs/illustrations/i_children-prop.png" alt='A puzzle-like Card tile with a slot for "children" pieces like text and Avatar' />

## How props change over time {/*how-props-change-over-time*/}

নিচের `Clock` কম্পোনেন্টটি parent কম্পোনেন্ট থেকে দুটি props গ্রহণ করে: `color` এবং `time`। (parent কম্পোনেন্টের কোডটি বাদ দেওয়া হয়েছে কারণ এটি [state](/learn/state-a-components-memory) ব্যবহার করে, যা আমরা এখনো আলোচনা করছি না।)

নীচের সিলেক্ট বক্সে রং পরিবর্তন করার চেষ্টা করুন:

<Sandpack>

```js src/Clock.js active
export default function Clock({ color, time }) {
  return (
    <h1 style={{ color: color }}>
      {time}
    </h1>
  );
}
```

```js src/App.js hidden
import { useState, useEffect } from 'react';
import Clock from './Clock.js';

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}

export default function App() {
  const time = useTime();
  const [color, setColor] = useState('lightcoral');
  return (
    <div>
      <p>
        Pick a color:{' '}
        <select value={color} onChange={e => setColor(e.target.value)}>
          <option value="lightcoral">lightcoral</option>
          <option value="midnightblue">midnightblue</option>
          <option value="rebeccapurple">rebeccapurple</option>
        </select>
      </p>
      <Clock color={color} time={time.toLocaleTimeString()} />
    </div>
  );
}
```

</Sandpack>

এই উদাহরণটি দেখায় যে **একটি কম্পোনেন্ট সময়ের সাথে সাথে বিভিন্ন প্রপস পেতে পারে।** প্রপস সর্বদা স্থির নয়! এখানে, `time` প্রপ প্রতি সেকেন্ডে পরিবর্তিত হয়, এবং `color` প্রপটি যখন আপনি অন্য রঙ নির্বাচন করেন তখন পরিবর্তিত হয়। প্রপস যেকোনো সময়ে একটি কম্পোনেন্টের ডেটা প্রতিফলিত করে, শুধুমাত্র শুরুতে নয়।

তবে, প্রপস [অপরিবর্তনীয়](https://en.wikipedia.org/wiki/Immutable_object)—এটি একটি কম্পিউটার বিজ্ঞান সম্পর্কিত শব্দ যার অর্থ "অপরিবর্তনীয়"। যখন একটি কম্পোনেন্ট তার প্রপস পরিবর্তন করতে চায় (যেমন, ব্যবহারকারীর একটি ক্রিয়ার প্রতিক্রিয়া হিসেবে বা নতুন ডেটার জন্য), তখন এটি তার প্যারেন্ট কম্পোনেন্টকে _বিভিন্ন প্রপস_—একটি নতুন অবজেক্ট পাঠানোর জন্য "বিনীত" করবে! তারপর তার পুরানো প্রপস বাদ দেওয়া হবে, এবং অবশেষে জাভাস্ক্রিপ্ট ইঞ্জিন তাদের দ্বারা নেওয়া মেমরি পুনরুদ্ধার করবে।

**"প্রপস পরিবর্তন করার চেষ্টা করবেন না"।** যখন আপনাকে ব্যবহারকারীর ইনপুটের প্রতিক্রিয়া জানাতে হবে (যেমন নির্বাচিত রঙ পরিবর্তন করা), তখন আপনাকে "স্টেট সেট" করতে হবে, যা আপনি [স্টেট: একটি কম্পোনেন্টের মেমরি](/learn/state-a-components-memory) থেকে শিখতে পারেন।

<Recap>

* প্রপস পাঠাতে, সেগুলো JSX-এ যুক্ত করুন, যেমন আপনি HTML অ্যাট্রিবিউটগুলোর সাথে করেন।
* প্রপস পড়তে, `function Avatar({ person, size })` ডিস্ট্রাকচারিং সিনট্যাক্স ব্যবহার করুন।
* আপনি একটি ডিফল্ট মান নির্ধারণ করতে পারেন যেমন `size = 100`, যা অনুপস্থিত এবং `undefined` প্রপসের জন্য ব্যবহৃত হয়।
* আপনি সমস্ত প্রপসকে `<Avatar {...props} />` JSX স্প্রেড সিনট্যাক্সের মাধ্যমে ফরওয়ার্ড করতে পারেন, তবে এটিকে অতিরিক্ত ব্যবহার করবেন না!
* নেস্টেড JSX যেমন `<Card><Avatar /></Card>` `Card` কম্পোনেন্টের `children` প্রপ হিসেবে উপস্থিত হবে।
* প্রপস হল সময়ের একটি রিড-অনলি স্ন্যাপশট: প্রতিটি রেন্ডারে প্রপসের একটি নতুন সংস্করণ পাওয়া যায়।
* আপনি প্রপস পরিবর্তন করতে পারবেন না। যখন আপনাকে ইন্টারঅ্যাকটিভিটি প্রয়োজন, তখন আপনাকে স্টেট সেট করতে হবে।

</Recap>



<Challenges>

#### একটি কম্পোনেন্ট বের করুন {/*extract-a-component*/}

এই `Gallery` কম্পোনেন্টে দুটি প্রফাইলের জন্য কিছু খুব অনুরূপ মার্কআপ রয়েছে। ডুপ্লিকেশন কমানোর জন্য একটি `Profile` কম্পোনেন্ট বের করুন। আপনাকে এটি কী প্রপস পাঠাতে হবে তা নির্বাচন করতে হবে।

<Sandpack>

```js src/App.js
import { getImageUrl } from './utils.js';

export default function Gallery() {
  return (
    <div>
      <h1>Notable Scientists</h1>
      <section className="profile">
        <h2>Maria Skłodowska-Curie</h2>
        <img
          className="avatar"
          src={getImageUrl('szV5sdG')}
          alt="Maria Skłodowska-Curie"
          width={70}
          height={70}
        />
        <ul>
          <li>
            <b>Profession: </b> 
            physicist and chemist
          </li>
          <li>
            <b>Awards: 4 </b> 
            (Nobel Prize in Physics, Nobel Prize in Chemistry, Davy Medal, Matteucci Medal)
          </li>
          <li>
            <b>Discovered: </b>
            polonium (chemical element)
          </li>
        </ul>
      </section>
      <section className="profile">
        <h2>Katsuko Saruhashi</h2>
        <img
          className="avatar"
          src={getImageUrl('YfeOqp2')}
          alt="Katsuko Saruhashi"
          width={70}
          height={70}
        />
        <ul>
          <li>
            <b>Profession: </b> 
            geochemist
          </li>
          <li>
            <b>Awards: 2 </b> 
            (Miyake Prize for geochemistry, Tanaka Prize)
          </li>
          <li>
            <b>Discovered: </b>
            a method for measuring carbon dioxide in seawater
          </li>
        </ul>
      </section>
    </div>
  );
}
```

```js src/utils.js
export function getImageUrl(imageId, size = 's') {
  return (
    'https://i.imgur.com/' +
    imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 5px; border-radius: 50%; min-height: 70px; }
.profile {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}
h1, h2 { margin: 5px; }
h1 { margin-bottom: 10px; }
ul { padding: 0px 10px 0px 20px; }
li { margin: 5px; }
```

</Sandpack>

<Hint>

একজন বিজ্ঞানীর জন্য মার্কআপ বের করার মাধ্যমে শুরু করুন। তারপর দ্বিতীয় উদাহরণে যা মেলে না তা খুঁজে বের করুন এবং সেগুলোকে প্রপস দ্বারা কনফিগারেবল করুন।

</Hint>

<Solution>

এই সমাধানে, `Profile` কম্পোনেন্টটি একাধিক প্রপ গ্রহণ করে: `imageId` (একটি স্ট্রিং), `name` (একটি স্ট্রিং), `profession` (একটি স্ট্রিং), `awards` (স্ট্রিংয়ের একটি অ্যারে), `discovery` (একটি স্ট্রিং), এবং `imageSize` (একটি সংখ্যা)।

মনে রাখবেন যে `imageSize` প্রপের একটি ডিফল্ট মান রয়েছে, এই কারণে আমরা এটি কম্পোনেন্টে পাঠাচ্ছি না।

<Sandpack>

```js src/App.js
import { getImageUrl } from './utils.js';

function Profile({
  imageId,
  name,
  profession,
  awards,
  discovery,
  imageSize = 70
}) {
  return (
    <section className="profile">
      <h2>{name}</h2>
      <img
        className="avatar"
        src={getImageUrl(imageId)}
        alt={name}
        width={imageSize}
        height={imageSize}
      />
      <ul>
        <li><b>Profession:</b> {profession}</li>
        <li>
          <b>Awards: {awards.length} </b>
          ({awards.join(', ')})
        </li>
        <li>
          <b>Discovered: </b>
          {discovery}
        </li>
      </ul>
    </section>
  );
}

export default function Gallery() {
  return (
    <div>
      <h1>Notable Scientists</h1>
      <Profile
        imageId="szV5sdG"
        name="Maria Skłodowska-Curie"
        profession="physicist and chemist"
        discovery="polonium (chemical element)"
        awards={[
          'Nobel Prize in Physics',
          'Nobel Prize in Chemistry',
          'Davy Medal',
          'Matteucci Medal'
        ]}
      />
      <Profile
        imageId='YfeOqp2'
        name='Katsuko Saruhashi'
        profession='geochemist'
        discovery="a method for measuring carbon dioxide in seawater"
        awards={[
          'Miyake Prize for geochemistry',
          'Tanaka Prize'
        ]}
      />
    </div>
  );
}
```

```js src/utils.js
export function getImageUrl(imageId, size = 's') {
  return (
    'https://i.imgur.com/' +
    imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 5px; border-radius: 50%; min-height: 70px; }
.profile {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}
h1, h2 { margin: 5px; }
h1 { margin-bottom: 10px; }
ul { padding: 0px 10px 0px 20px; }
li { margin: 5px; }
```

</Sandpack>

মনে রাখবেন যে যদি `awards` একটি অ্যারে হয় তবে আপনাকে আলাদা `awardCount` প্রপের প্রয়োজন নেই। আপনি `awards.length` ব্যবহার করে পুরস্কারের সংখ্যা গুণতে পারেন। মনে রাখবেন যে প্রপস যেকোনো মান নিতে পারে, এবং এর মধ্যে অ্যারেও রয়েছে!

আরেকটি সমাধান, যা এই পৃষ্ঠায় আগের উদাহরণগুলির সাথে আরও সাদৃশ্যপূর্ণ, হল একটি ব্যক্তির সম্পর্কে সমস্ত তথ্য একটি একক অবজেক্টে গ্রুপ করা এবং সেই অবজেক্টটি একটি প্রপ হিসেবে পাঠানো:

<Sandpack>

```js src/App.js
import { getImageUrl } from './utils.js';

function Profile({ person, imageSize = 70 }) {
  const imageSrc = getImageUrl(person)

  return (
    <section className="profile">
      <h2>{person.name}</h2>
      <img
        className="avatar"
        src={imageSrc}
        alt={person.name}
        width={imageSize}
        height={imageSize}
      />
      <ul>
        <li>
          <b>Profession:</b> {person.profession}
        </li>
        <li>
          <b>Awards: {person.awards.length} </b>
          ({person.awards.join(', ')})
        </li>
        <li>
          <b>Discovered: </b>
          {person.discovery}
        </li>
      </ul>
    </section>
  )
}

export default function Gallery() {
  return (
    <div>
      <h1>Notable Scientists</h1>
      <Profile person={{
        imageId: 'szV5sdG',
        name: 'Maria Skłodowska-Curie',
        profession: 'physicist and chemist',
        discovery: 'polonium (chemical element)',
        awards: [
          'Nobel Prize in Physics',
          'Nobel Prize in Chemistry',
          'Davy Medal',
          'Matteucci Medal'
        ],
      }} />
      <Profile person={{
        imageId: 'YfeOqp2',
        name: 'Katsuko Saruhashi',
        profession: 'geochemist',
        discovery: 'a method for measuring carbon dioxide in seawater',
        awards: [
          'Miyake Prize for geochemistry',
          'Tanaka Prize'
        ],
      }} />
    </div>
  );
}
```

```js src/utils.js
export function getImageUrl(person, size = 's') {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 5px; border-radius: 50%; min-height: 70px; }
.profile {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}
h1, h2 { margin: 5px; }
h1 { margin-bottom: 10px; }
ul { padding: 0px 10px 0px 20px; }
li { margin: 5px; }
```

</Sandpack>

যদিও সিনট্যাক্সটি কিছুটা ভিন্ন দেখাচ্ছে কারণ আপনি একটি জাভাস্ক্রিপ্ট অবজেক্টের প্রপার্টি বর্ণনা করছেন, এটি JSX অ্যাট্রিবিউটগুলোর একটি সংগ্রহের পরিবর্তে, এই উদাহরণগুলি প্রধানত সমতুল্য, এবং আপনি যেকোনো একটি পদ্ধতি বেছে নিতে পারেন।

</Solution>

#### একটি প্রপের উপর ভিত্তি করে ইমেজের আকার সমন্বয় করুন {/*adjust-the-image-size-based-on-a-prop*/}

আপনার উদাহরণে, `Avatar` একটি সংখ্যাগত `size` প্রপ গ্রহণ করে যা `<img>` এর প্রস্থ এবং উচ্চতা নির্ধারণ করে। এই উদাহরণে, `size` প্রপটি `40` এ সেট করা হয়েছে। তবে, যদি আপনি নতুন ট্যাবে চিত্রটি খোলেন, আপনি লক্ষ্য করবেন যে চিত্রের আকার আসলে বড় (`160` পিক্সেল)। বাস্তব চিত্রের আকার নির্ধারণ করা হয় আপনি কোন থাম্বনেল আকারটি অনুরোধ করছেন তার দ্বারা।

`Avatar` উপাদানটি পরিবর্তন করুন যাতে `size` প্রপের উপর ভিত্তি করে সবচেয়ে কাছের চিত্রের আকার অনুরোধ করা হয়। বিশেষভাবে, যদি `size` `90` এর কম হয়, তাহলে `getImageUrl` ফাংশনে `'s'` ("ছোট") পাস করুন `'b'` ("বড়") এর পরিবর্তে। আপনার পরিবর্তনগুলি কার্যকর কিনা তা যাচাই করুন বিভিন্ন `size` প্রপ মান দিয়ে অ্যাভাটারগুলি রেন্ডার করে এবং নতুন ট্যাবে চিত্রগুলি খুলে।

<Sandpack>

```js src/App.js
import { getImageUrl } from './utils.js';

function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person, 'b')}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <Avatar
      size={40}
      person={{ 
        name: 'Gregorio Y. Zara', 
        imageId: '7vQD0fP'
      }}
    />
  );
}
```

```js src/utils.js
export function getImageUrl(person, size) {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 20px; border-radius: 50%; }
```

</Sandpack>

<Solution>

Here is how you could go about it:

<Sandpack>

```js src/App.js
import { getImageUrl } from './utils.js';

function Avatar({ person, size }) {
  let thumbnailSize = 's';
  if (size > 90) {
    thumbnailSize = 'b';
  }
  return (
    <img
      className="avatar"
      src={getImageUrl(person, thumbnailSize)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <>
      <Avatar
        size={40}
        person={{ 
          name: 'Gregorio Y. Zara', 
          imageId: '7vQD0fP'
        }}
      />
      <Avatar
        size={120}
        person={{ 
          name: 'Gregorio Y. Zara', 
          imageId: '7vQD0fP'
        }}
      />
    </>
  );
}
```

```js src/utils.js
export function getImageUrl(person, size) {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 20px; border-radius: 50%; }
```

</Sandpack>

আপনি উচ্চ DPI স্ক্রীনের জন্য আরও তীক্ষ্ণ চিত্রও দেখাতে পারেন `window.devicePixelRatio` কে বিবেচনায় নিয়ে: [MDN ডোকুমেন্টেশন](https://developer.mozilla.org/en-US/docs/Web/API/Window/devicePixelRatio)

<Sandpack>

```js src/App.js
import { getImageUrl } from './utils.js';

const ratio = window.devicePixelRatio;

function Avatar({ person, size }) {
  let thumbnailSize = 's';
  if (size * ratio > 90) {
    thumbnailSize = 'b';
  }
  return (
    <img
      className="avatar"
      src={getImageUrl(person, thumbnailSize)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <>
      <Avatar
        size={40}
        person={{ 
          name: 'Gregorio Y. Zara', 
          imageId: '7vQD0fP'
        }}
      />
      <Avatar
        size={70}
        person={{ 
          name: 'Gregorio Y. Zara', 
          imageId: '7vQD0fP'
        }}
      />
      <Avatar
        size={120}
        person={{ 
          name: 'Gregorio Y. Zara', 
          imageId: '7vQD0fP'
        }}
      />
    </>
  );
}
```

```js src/utils.js
export function getImageUrl(person, size) {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 20px; border-radius: 50%; }
```

</Sandpack>

প্রপগুলি আপনাকে `Avatar` উপাদানের ভিতরে এইরকম লজিক এনক্যাপসুলেট করতে দেয় (এবং প্রয়োজনে পরে এটি পরিবর্তন করতে) যাতে সবাই `<Avatar>` উপাদানটি ব্যবহার করতে পারে চিন্তা না করেই যে চিত্রগুলি কিভাবে অনুরোধ করা হয় এবং আকার পরিবর্তন করা হয়।

</Solution>

#### `children` প্রপে JSX পাস করা {/*passing-jsx-in-a-children-prop*/}

নিচের মার্কআপ থেকে একটি `Card` উপাদান বের করুন এবং `children` প্রপ ব্যবহার করে এতে ভিন্ন ভিন্ন JSX পাঠান:

<Sandpack>

```js
export default function Profile() {
  return (
    <div>
      <div className="card">
        <div className="card-content">
          <h1>Photo</h1>
          <img
            className="avatar"
            src="https://i.imgur.com/OKS67lhm.jpg"
            alt="Aklilu Lemma"
            width={70}
            height={70}
          />
        </div>
      </div>
      <div className="card">
        <div className="card-content">
          <h1>About</h1>
          <p>Aklilu Lemma was a distinguished Ethiopian scientist who discovered a natural treatment to schistosomiasis.</p>
        </div>
      </div>
    </div>
  );
}
```

```css
.card {
  width: fit-content;
  margin: 20px;
  padding: 20px;
  border: 1px solid #aaa;
  border-radius: 20px;
  background: #fff;
}
.card-content {
  text-align: center;
}
.avatar {
  margin: 10px;
  border-radius: 50%;
}
h1 {
  margin: 5px;
  padding: 0;
  font-size: 24px;
}
```

</Sandpack>

<Hint>

আপনি একটি উপাদানের ট্যাগের ভিতরে যে কোনও JSX রাখলে সেটি সেই উপাদানের `children` প্রপ হিসেবে পাঠানো হবে।

</Hint>

<Solution>

এভাবে আপনি উভয় স্থানে `Card` উপাদানটি ব্যবহার করতে পারেন:

<Sandpack>

```js
function Card({ children }) {
  return (
    <div className="card">
      <div className="card-content">
        {children}
      </div>
    </div>
  );
}

export default function Profile() {
  return (
    <div>
      <Card>
        <h1>Photo</h1>
        <img
          className="avatar"
          src="https://i.imgur.com/OKS67lhm.jpg"
          alt="Aklilu Lemma"
          width={100}
          height={100}
        />
      </Card>
      <Card>
        <h1>About</h1>
        <p>Aklilu Lemma was a distinguished Ethiopian scientist who discovered a natural treatment to schistosomiasis.</p>
      </Card>
    </div>
  );
}
```

```css
.card {
  width: fit-content;
  margin: 20px;
  padding: 20px;
  border: 1px solid #aaa;
  border-radius: 20px;
  background: #fff;
}
.card-content {
  text-align: center;
}
.avatar {
  margin: 10px;
  border-radius: 50%;
}
h1 {
  margin: 5px;
  padding: 0;
  font-size: 24px;
}
```

</Sandpack>

আপনি যদি চান যে প্রতিটি `Card`-এ সব সময় একটি শিরোনাম থাকুক, তাহলে `title`-কে একটি পৃথক প্রপ হিসাবেও তৈরি করতে পারেন:

<Sandpack>

```js
function Card({ children, title }) {
  return (
    <div className="card">
      <div className="card-content">
        <h1>{title}</h1>
        {children}
      </div>
    </div>
  );
}

export default function Profile() {
  return (
    <div>
      <Card title="Photo">
        <img
          className="avatar"
          src="https://i.imgur.com/OKS67lhm.jpg"
          alt="Aklilu Lemma"
          width={100}
          height={100}
        />
      </Card>
      <Card title="About">
        <p>Aklilu Lemma was a distinguished Ethiopian scientist who discovered a natural treatment to schistosomiasis.</p>
      </Card>
    </div>
  );
}
```

```css
.card {
  width: fit-content;
  margin: 20px;
  padding: 20px;
  border: 1px solid #aaa;
  border-radius: 20px;
  background: #fff;
}
.card-content {
  text-align: center;
}
.avatar {
  margin: 10px;
  border-radius: 50%;
}
h1 {
  margin: 5px;
  padding: 0;
  font-size: 24px;
}
```

</Sandpack>

</Solution>

</Challenges>
