---
title: UI এর বর্ণনা
---

<Intro>

React একটা জাভাস্ক্রিপ্ট লাইব্রেরি যার কাজ user interfaces (UI) রেন্ডার করা। UI বিভিন্ন ক্ষুদ্র একক যেমন বাটন, টেক্সট বা ছবির মাধ্যমে গড়ে ওঠে। React আপনাকে সুযোগ দেয় সেগুলোকে reusable, nestable *কম্পোনেন্টে* একত্রিত করার। ওয়েবসাইট হোক বা ফোনের অ্যাপ, যা কিছু আপনি স্ক্রিনে দেখতে পান, সব কিছুই কম্পোনেন্টে ভেঙে ফেলা সম্ভব। এই অধ্যায়ে, আপনি React কম্পোনেন্ট বানানো, পরিবর্তন করা এবং লজিকের উপর ভিত্তি করে প্রদর্শন করা শিখবেন। 

</Intro>

<YouWillLearn isChapter={true}>

* [আপনার প্রথম কম্পোনেন্ট কীভাবে লিখবেন](/learn/your-first-component)
* [কখন এবং কীভাবে মাল্টি-কম্পোনেন্ট ফাইল তৈরি করবেন](/learn/importing-and-exporting-components)
* [JSX দিয়ে কীভাবে জাভাস্ক্রিপ্টে marrkap যুক্ত করবেন](/learn/writing-markup-with-jsx)
* [কীভাবে আপনার কম্পোনেন্ট থেকে জাভাস্ক্রিপ্ট ফাংশনালিটি ব্যবহারের জন্য কার্লি ব্রেস ব্যবহার করবেন](/learn/javascript-in-jsx-with-curly-braces)
* [Prop দিয়ে কীভাবে কম্পোনেন্ট কনফিগার করবেন](/learn/passing-props-to-a-component)
* [কীভাবে কম্পোনেন্ট কন্ডিশনালি রেন্ডার করবেন](/learn/conditional-rendering)
* [কীভাবে এক সাথে একাধিক কম্পোনেন্ট রেন্ডার করবেন](/learn/rendering-lists)
* [কীভাবে কম্পোনেন্ট pure রাখার মাধ্যমে বিভ্রান্তিকর বাগ এড়িয়ে চলবেন](/learn/keeping-components-pure)

</YouWillLearn>

## আপনার প্রথম কম্পোনেন্ট {/*your-first-component*/}

React অ্যাপ্লিকেশনগুলো UI এর পৃথক পৃথক কিছু অংশের মাধ্যমে গড়ে ওঠে যাদের নাম *কম্পোনেন্ট*। একটা React কম্পোনেন্ট হচ্ছে একটা জাভাস্ক্রিপ্ট ফাংশন যা আপনি মার্কাপ দিয়ে সুন্দর করে তুলতে পারেন। একটা কম্পোনেন্ট একটা বাটনের মত ছোট বা একটা পেইজের মত বড় হতে পারে। এখানে দেখতে পাচ্ছেন একটা একটা  `Gallery` যা তিনটি `Profile` কম্পোনেন্ট রেন্ডার করছেঃ

<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

<LearnMore path="/learn/your-first-component">

কীভাবে React কম্পোনেন্ট ডিক্লেয়ার এবং ব্যবহার করবেন তা জানতে পড়ুন **[আপনার প্রথম কম্পোনেন্ট।](/learn/your-first-component)** 

</LearnMore>

## কম্পোনেন্টের ইমপোর্ট এবং এক্সপোর্ট {/*importing-and-exporting-components*/}

আপনি একটা ফাইলে অনেকগুলো কম্পোনেন্ট ডিক্লেয়ার করতে পারেন, তবে বড় বড় ফাইল navigate করাটা বেশ কষ্টসাধ্য হতে পারে। এটা সমাধানের জন্য, আপনি একটা কম্পোনেন্টকে তার নিজের ফাইলে *এক্সপোর্ট* করতে পারেন, এবং পরে সেটাকে অন্য ফাইলে *ইমপোর্ট* করতে পারেন।


<Sandpack>

```js App.js hidden
import Gallery from './Gallery.js';

export default function App() {
  return (
    <Gallery />
  );
}
```

```js Gallery.js active
import Profile from './Profile.js';

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
export default function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}
```

```css
img { margin: 0 10px 10px 0; }
```

</Sandpack>

<LearnMore path="/learn/importing-and-exporting-components">

কীভাবে কম্পোনেন্টকে তার নিজের একাধিক ফাইলে ভাগ করে নিবেন জানতে পড়ুন **[কম্পোনেন্টের ইমপোর্ট এবং এক্সপোর্ট।](/learn/importing-and-exporting-components)**

</LearnMore>

## JSX দিয়া মার্ক আপ লেখা {/*writing-markup-with-jsx*/}

প্রতিটা React কম্পোনেন্ট একেকটা জাভাস্ক্রিপ্ট ফাংশন যেটাতে কিছু মার্কাপ থাকতে পারে যা React ব্রাউজারে রেন্ডার করে।এই মার্কাপ এর প্রতিনিধিত্ব করার জন্য React কম্পোনেন্টগুলো একটা সিনট্যাক্স এক্সটেনশন ব্যবহার করে যার নাম JSX। JSX অনেকটা HTML এর মত দেখতে, কিন্তু আরেকটু কঠোর যা dynamic তথ্য দেখাতে পারে।

আমরা যদি ইতোমধ্যে লেখা HTML মার্কাপ একটা React কম্পোনেন্টে পেস্ট করে দেই, এটা সব সময় কাজ করবে নাঃ

<Sandpack>

```js
export default function TodoList() {
  return (
    // This doesn't quite work!
    <h1>Hedy Lamarr's Todos</h1>
    <img
      src="https://i.imgur.com/yXOvdOSs.jpg"
      alt="Hedy Lamarr"
      class="photo"
    >
    <ul>
      <li>Invent new traffic lights
      <li>Rehearse a movie scene
      <li>Improve spectrum technology
    </ul>
  );
}
```

```css
img { height: 90px; }
```

</Sandpack>

আপনার যদি এরকম আগে থেকে HTML লেখা থাকে, সেটা আপনি [converter](https://transform.tools/html-to-jsx) ব্যবহার করে ঠিক করতে পারেনঃ

<Sandpack>

```js
export default function TodoList() {
  return (
    <>
      <h1>Hedy Lamarr's Todos</h1>
      <img
        src="https://i.imgur.com/yXOvdOSs.jpg"
        alt="Hedy Lamarr"
        className="photo"
      />
      <ul>
        <li>Invent new traffic lights</li>
        <li>Rehearse a movie scene</li>
        <li>Improve spectrum technology</li>
      </ul>
    </>
  );
}
```

```css
img { height: 90px; }
```

</Sandpack>

<LearnMore path="/learn/writing-markup-with-jsx">

কীভাবে সঠিকভাবে JSX লিখবেন জানার জন্য পড়ুন **[JSX দিয়ে মার্কাপ লেখা।](/learn/writing-markup-with-jsx)** 

</LearnMore>

## কার্লি ব্রেইস ব্যবহার করে JSX এর মধ্যে জাভাস্ক্রিপ্ট {/*javascript-in-jsx-with-curly-braces*/}

JSX আপনাকে একটা জাভাস্ক্রিপ্ট ফাইলে HTML এর মত মার্কাপ লিখতে দেয়, এতে রেন্ডারিং লজিক আর কনটেন্ট একি জায়গায় থাকে। মাঝে মাঝে আপনি চাইবেন সেই মার্কাপের মধ্যে কিছু জাভাস্ক্রিপ্ট লজিক যুক্ত করতে বা ডাইনামিক প্রোপার্টি রেফারেন্স করতে। এরকম ক্ষেত্রে, আপনি আপনার JSX এর মধ্যে কার্লি ব্রেইস ব্যবহার করে জাভাস্ক্রিপ্টের জন্য "একটা জানালা খুলে দিতে পারেন":

<Sandpack>

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="https://i.imgur.com/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

<LearnMore path="/learn/javascript-in-jsx-with-curly-braces">

JSX থেকে কীভাবে জাভাস্ক্রিপ্ট ডেটায় এক্সেস নিবেন জানতে, **[কার্লি ব্রেইস ব্যবহার করে JSX এর মধ্যে জাভাস্ক্রিপ্ট](/learn/javascript-in-jsx-with-curly-braces)** পড়ুন।

</LearnMore>

## একটা কম্পোনেন্টে প্রপ পাঠানো {/*passing-props-to-a-component*/}

React কম্পোনেন্ট নিজেদের মধ্যে যোগাযোগ করতে *প্রপ* ব্যবহার করে। যেকোন প্যারেন্ট কম্পোনেন্ট তার চাইল্ড কম্পোনেন্টে তথ্য পাঠাতে পারে প্রপ দেবার মাধ্যমে। প্রপ হয়ত আপনাকে HTML এট্রিবিউটের কথা মনে করিয়ে দেবে, তবে আপনি এর মধ্য দিয়ে যেকোন জাভাস্ক্রিপ্ট ভ্যালু পাঠাতে পারেন। সেটা হতে পারে অবজেক্ট, অ্যারে, ফাংশন এমনকি JSX।

<Sandpack>

```js
import { getImageUrl } from './utils.js'

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

function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

```

```js utils.js
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

<LearnMore path="/learn/passing-props-to-a-component">

কীভাবে প্রপ পাস এবং রিড করবেন জানতে পড়ুন **[একটা কম্পোনেন্টে প্রপ পাঠানো](/learn/passing-props-to-a-component)**

</LearnMore>

## কন্ডিশনাল রেন্ডারিং {/*conditional-rendering*/}

বেশির ভাগ সময় ক্ষেত্র বিশেষে আপনার কম্পোনেন্টগুলোর প্রয়োজন হবে বিভিন্ন কিছু প্রদর্শন করা। React এ, জাভাস্ক্রিপ্ট সিনট্যাক্স যেমন `if` স্টেটমেন্ট, `&&` এবং `? :` অপারেটর ব্যবহার করে কন্ডিশনালি JSX রেন্ডার করতে পারেন।

এই উদাহরণে জাভাস্ক্রিপ্টের `&&` অপারেটর ব্যবহৃত হয়েছে একটা চেকমার্ক কন্ডিশনালি রেন্ডার করার জন্যঃ

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

<LearnMore path="/learn/conditional-rendering">

কন্ডিশনালি কনটেন্ট প্রদর্শনের বিভিন্ন পন্থা জানতে পড়ুন **[Conditional Rendering](/learn/conditional-rendering)**।

</LearnMore>

## লিস্টের রেন্ডারিং {/*rendering-lists*/}

আপনি প্রায়ই চাইবেন একটা ডেটা কালেকশন থেকে একাধিক একই রকম কনটেন্ট প্রদর্শন করতে। আপনি আপনার ডেটার অ্যারে-কে একটা কম্পোনেন্টের অ্যারে-তে রূপান্তরিত করার জন্য React এর সাথে জাভাস্ক্রিপ্টের `filter()` এবং `map()` ব্যবহার করতে পারেন।

অ্যারের প্রতিটা আইটেমের জন্য, আপনাকে একটা `key` নির্দিষ্ট করতে হবে। আপনি সাধারণত ডেটাবেইজ থেকে একটা আইডিকে `key` হিসেবে ব্যবহার করতে চাইবেন। লিস্টে কোন আইটেমটা কোথায় আছে ট্র্যাক রাখতে React কী-গুলো ব্যবহার করতে পারে, এমনকি লিস্টে পরিবর্তন আসলেও এটা কাজ করে।

<Sandpack>

```js App.js
import { people } from './data.js';
import { getImageUrl } from './utils.js';

export default function List() {
  const listItems = people.map(person =>
    <li key={person.id}>
      <img
        src={getImageUrl(person)}
        alt={person.name}
      />
      <p>
        <b>{person.name}:</b>
        {' ' + person.profession + ' '}
        known for {person.accomplishment}
      </p>
    </li>
  );
  return (
    <article>
      <h1>Scientists</h1>
      <ul>{listItems}</ul>
    </article>
  );
}
```

```js data.js
export const people = [{
  id: 0,
  name: 'Creola Katherine Johnson',
  profession: 'mathematician',
  accomplishment: 'spaceflight calculations',
  imageId: 'MK3eW3A'
}, {
  id: 1,
  name: 'Mario José Molina-Pasquel Henríquez',
  profession: 'chemist',
  accomplishment: 'discovery of Arctic ozone hole',
  imageId: 'mynHUSa'
}, {
  id: 2,
  name: 'Mohammad Abdus Salam',
  profession: 'physicist',
  accomplishment: 'electromagnetism theory',
  imageId: 'bE7W1ji'
}, {
  id: 3,
  name: 'Percy Lavon Julian',
  profession: 'chemist',
  accomplishment: 'pioneering cortisone drugs, steroids and birth control pills',
  imageId: 'IOjWm71'
}, {
  id: 4,
  name: 'Subrahmanyan Chandrasekhar',
  profession: 'astrophysicist',
  accomplishment: 'white dwarf star mass calculations',
  imageId: 'lrWQx8l'
}];
```

```js utils.js
export function getImageUrl(person) {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    's.jpg'
  );
}
```

```css
ul { list-style-type: none; padding: 0px 10px; }
li {
  margin-bottom: 10px;
  display: grid;
  grid-template-columns: 1fr 1fr;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
h1 { font-size: 22px; }
h2 { font-size: 20px; }
```

</Sandpack>

<LearnMore path="/learn/rendering-lists">

কীভাবে একটা কম্পোনেন্টের লিস্ট রেন্ডার করবেন এবং কীভাবে একটা key নির্দিষ্ট করবেন জানতে পড়ুন **[লিস্টের রেন্ডারিং।](/learn/rendering-lists)**

</LearnMore>

## কম্পোনেন্ট pure রাখা {/*keeping-components-pure*/}

কিছু কিছু জাভাস্ক্রিপ্ট ফাংশন *pure.* একটা pure ফাংশনঃ

* **কেবলমাত্র নিজেরটাই বুঝে।** এটা এর কল হবার আগে থেকে বিদ্যমান কোন অবজেক্ট বা ভ্যারিয়েবল পরিবর্তন করে না।
* **একই ইনপুটের একই আউটপুট।** একই ইনপুট দেওয়া হলে একটা pure ফাংশন সব সময় একই আউটপুটই দেয়।

আপনার কোডবেইজ যত বড় হবে, কঠোরভাবে আপনার কম্পোনেন্টগুলোকে pure ফাংশন হিসেবে লেখার মাধ্যমে, আপনি খুব বিরক্তিকর এক গুচ্ছ বাগ এবং অসামাঞ্জস্যপূর্ণ আচরণ থেকে নিজেকে রক্ষা করতে পারেন। এখানে একটি impure কম্পোনেন্টের উদাহরণ দেওয়া হলঃ

<Sandpack>

```js
let guest = 0;

function Cup() {
  // Bad: changing a preexisting variable!
  guest = guest + 1;
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup />
      <Cup />
      <Cup />
    </>
  );
}
```

</Sandpack>

আগে থেকে বিদ্যমান একটা ভ্যারিয়েবল পরিবর্তনের জায়গায় আপনি যদি একটা প্রপ পাস করে দেন তাহলেই এই কম্পোনেন্টকে pure বানিয়ে ফেলতে পারবেনঃ

<Sandpack>

```js
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup guest={1} />
      <Cup guest={2} />
      <Cup guest={3} />
    </>
  );
}
```

</Sandpack>

<LearnMore path="/learn/keeping-components-pure">

কীভাবে pure এবং অনুমানযোগ্য ফাংশন হিসেবে কম্পোনেন্ট লিখবেন জানতে পড়ুন **[কম্পোনেন্ট pure রাখা।]**

</LearnMore>

## এর পর তাহলে কী? {/*whats-next*/}

এই অধ্যায়টি গুছিয়ে পড়া শুরু করতে চলে যান  [আপনার প্রথম কম্পোনেন্ট](/learn/your-first-component) অংশে।

অথবা, বিষয়গুলো যদি আপনার আগে থেকেই জানা থাকে তাহলে, পড়ে ফেলতে পারেন [ইন্টার‍্যাক্টিভিটির সংযুক্তি](/learn/adding-interactivity) অংশটি!
