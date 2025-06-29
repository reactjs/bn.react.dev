---
title: JSX এ কার্লি ব্রেসের মধ্যে জাভাস্ক্রিপ্ট
---

<Intro>

JSX আপনাকে জাভাস্ক্রিপ্ট ফাইলের মধ্যে HTML-এর মতো মার্কআপ লিখতে দেয়, যেখানে রেন্ডারিং লজিক এবং কন্টেন্ট একসাথে থাকে। মাঝে মাঝে আপনি এই মার্কআপের মধ্যে কিছু জাভাস্ক্রিপ্ট লজিক যোগ করতে বা ডায়নামিক প্রপার্টি রেফারেন্স করতে চাইবেন। এই পরিস্থিতিতে, আপনি আপনার JSX-এ `{ }` ব্যবহার করে জাভাস্ক্রিপ্টে প্রবেশ করতে পারবেন।

</Intro>

<YouWillLearn>

- কিভাবে কোটেশনসহ স্ট্রিং পাঠাতে হয়
- কিভাবে JSX-এ `{ }` দিয়ে জাভাস্ক্রিপ্ট ভেরিয়েবল রেফারেন্স করতে হয়
- কিভাবে `{ }` দিয়ে JSX-এ জাভাস্ক্রিপ্ট ফাংশন কল করতে হয়
- কিভাবে `{ }` দিয়ে JSX-এ জাভাস্ক্রিপ্ট অবজেক্ট ব্যবহার করতে হয়

</YouWillLearn>

## Passing strings with quotes {/*passing-strings-with-quotes*/}

যখন আপনি JSX-এ একটি স্ট্রিং অ্যাট্রিবিউট পাঠাতে চান, তখন এটি সিঙ্গেল বা ডাবল কোটেশনে রাখুন:

<Sandpack>

```js
export default function Avatar() {
  return (
    <img
      className="avatar"
      src="https://i.imgur.com/7vQD0fPs.jpg"
      alt="Gregorio Y. Zara"
    />
  );
}
```

```css
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

এখানে, `"https://i.imgur.com/7vQD0fPs.jpg"` এবং `"Gregorio Y. Zara"` স্ট্রিং হিসেবে পাঠানো হয়েছে।

কিন্তু যদি আপনি `src` বা `alt` টেক্সটকে ডাইনামিকভাবে নির্দিষ্ট করতে চান, তাহলে **`"` এবং `"` পরিবর্তে `{` এবং `}` ব্যবহার করে জাভাস্ক্রিপ্ট থেকে একটি মান ব্যবহার করতে পারেন**।

<Sandpack>

```js
export default function Avatar() {
  const avatar = 'https://i.imgur.com/7vQD0fPs.jpg';
  const description = 'Gregorio Y. Zara';
  return (
    <img
      className="avatar"
      src={avatar}
      alt={description}
    />
  );
}
```

```css
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

একটি পার্থক্য লক্ষ্য করুন `className="avatar"`, যা `"avatar"` নামে একটি CSS ক্লাস নির্দিষ্ট করে যাতে ইমেজটি গোলাকৃতি হয়, এবং `src={avatar}` যা `avatar` নামে জাভাস্ক্রিপ্ট ভেরিয়েবলের মান পড়ে। এর কারণ হলো `{ }` আপনাকে আপনার মার্কআপে জাভাস্ক্রিপ্ট ব্যবহার করতে দেয়!

## `{ }` ব্যবহার: জাভাস্ক্রিপ্ট দুনিয়ার একটি জানালা {/*using-curly-braces-a-window-into-the-javascript-world*/}

JSX হল জাভাস্ক্রিপ্ট লেখার একটি বিশেষ পদ্ধতি। যার মানে, এর মধ্যে `{ }` ব্যবহার করে জাভাস্ক্রিপ্ট ব্যবহার করা সম্ভব। নিচের উদাহরণে প্রথমে একজন বিজ্ঞানীর নাম, `name`, ঘোষণা করা হয়েছে, তারপর `{ }` দিয়ে `<h1>` ট্যাগের ভিতরে এটি এম্বেড করা হয়েছে:

<Sandpack>

```js
export default function TodoList() {
  const name = 'Gregorio Y. Zara';
  return (
    <h1>{name}'s To Do List</h1>
  );
}
```

</Sandpack>

`name` এর মান `'Gregorio Y. Zara'` থেকে `'Hedy Lamarr'` এ পরিবর্তন করার চেষ্টা করুন। দেখুন কিভাবে তালিকার শিরোনাম পরিবর্তিত হয়?

কোনও জাভাস্ক্রিপ্ট এক্সপ্রেশন `{ }` এর মধ্যে কাজ করবে, যেমন ফাংশন কল `formatDate()`:

<Sandpack>

```js
const today = new Date();

function formatDate(date) {
  return new Intl.DateTimeFormat(
    'en-US',
    { weekday: 'long' }
  ).format(date);
}

export default function TodoList() {
  return (
    <h1>To Do List for {formatDate(today)}</h1>
  );
}
```

</Sandpack>

### `{ }` কোথায় ব্যবহার করবেন {/*where-to-use-curly-braces*/}

আপনি JSX-এর মধ্যে দুটি উপায়ে `{ }` ব্যবহার করতে পারেন:

১. **টেক্সট হিসেবে** সরাসরি একটি JSX ট্যাগের মধ্যে: `<h1>{name}'s To Do List</h1>` কাজ করবে, কিন্তু `<{tag}>Gregorio Y. Zara's To Do List</{tag}>` কাজ করবে না।
২. **অ্যাট্রিবিউট হিসেবে** `=` চিহ্নের পরে সরাসরি: `src={avatar}` `avatar` ভেরিয়েবলটি পড়বে, কিন্তু `src="{avatar}"` `{avatar}` স্ট্রিংটি পাঠাবে।

## "ডাবল কার্লি" ব্যবহার: CSS এবং অন্যান্য অবজেক্ট JSX-এ {/*using-double-curlies-css-and-other-objects-in-jsx*/}

স্ট্রিং, সংখ্যা এবং অন্যান্য জাভাস্ক্রিপ্ট এক্সপ্রেশনের পাশাপাশি, আপনি JSX-এ অবজেক্টও পাঠাতে পারেন। অবজেক্টও `{ }` দিয়ে নির্ধারণ করা হয়, যেমন `{ name: "Hedy Lamarr", inventions: 5 }`। তাই JSX-এ একটি জাভাস্ক্রিপ্ট অবজেক্ট পাঠাতে আপনাকে অবজেক্টটিকে আরেকটি `{ }` এর মধ্যে মোড়াতে হবে: `person={{ name: "Hedy Lamarr", inventions: 5 }}`।

আপনি এটি ইনলাইন CSS স্টাইলের সাথে দেখতে পারেন JSX-এ। React ইনলাইন স্টাইল ব্যবহার করতে বলে না (CSS ক্লাস বেশিরভাগ ক্ষেত্রে ভাল কাজ করে)। কিন্তু যখন আপনি একটি ইনলাইন স্টাইলের প্রয়োজন হয়, তখন আপনি `style` অ্যাট্রিবিউটে একটি অবজেক্ট পাঠাতে পারেন:

<Sandpack>

```js
export default function TodoList() {
  return (
    <ul style={{
      backgroundColor: 'black',
      color: 'pink'
    }}>
      <li>Improve the videophone</li>
      <li>Prepare aeronautics lectures</li>
      <li>Work on the alcohol-fuelled engine</li>
    </ul>
  );
}
```

```css
body { padding: 0; margin: 0 }
ul { padding: 20px 20px 20px 40px; margin: 0; }
```

</Sandpack>

`backgroundColor` এবং `color` এর মান পরিবর্তন করার চেষ্টা করুন।

আপনি আসলেই জাভাস্ক্রিপ্ট অবজেক্টটি `{ }` এর ভিতরে দেখতে পাবেন যখন এটি এভাবে লিখবেন:

```js {2-5}
<ul style={
  {
    backgroundColor: 'black',
    color: 'pink'
  }
}>
```

পরের বার যখন আপনি `{{` এবং `}}` JSX-এ দেখবেন, জানবেন এটি কিছু নয় বরং JSX `{ }` এর ভিতরে একটি জাভাস্ক্রিপ্ট অবজেক্ট!

<Pitfall>

ইনলাইন `style` প্রপার্টিগুলি camelCase-এ লেখা হয়। উদাহরণস্বরূপ, HTML `<ul style="background-color: black">` আপনার কম্পোনেন্টে `<ul style={{ backgroundColor: 'black' }}>` হিসাবে লেখা হবে।

</Pitfall>

## জাভাস্ক্রিপ্ট অবজেক্ট এবং কার্লি ব্রেসের সাথে আরও মজার বিষয় {/*more-fun-with-javascript-objects-and-curly-braces*/}

আপনি একাধিক এক্সপ্রেশন এক অবজেক্টে স্থানান্তর করতে পারেন এবং JSX-এ `{ }` এর ভিতরে তাদের রেফারেন্স করতে পারেন:

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

এই উদাহরণে, `person` জাভাস্ক্রিপ্ট অবজেক্টটি একটি `name` স্ট্রিং এবং একটি `theme` অবজেক্ট ধারণ করে:

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};
```

কম্পোনেন্টটি `person` থেকে এই মানগুলো ব্যবহার করতে পারে:

```js
<div style={person.theme}>
  <h1>{person.name}'s Todos</h1>
```

JSX খুবই সংক্ষিপ্ত একটি টেম্পলেটিং ভাষা কারণ এটি আপনাকে জাভাস্ক্রিপ্ট ব্যবহার করে ডেটা এবং লজিক সংগঠিত করতে দেয়।

<Recap>

এখন আপনি JSX সম্পর্কে প্রায় সবকিছু জানেন:
- JSX অ্যাট্রিবিউট কোটেশনের মধ্যে থাকলে সেগুলো স্ট্রিং হিসেবে পাঠানো হয়।
- `{ }` জাভাস্ক্রিপ্ট লজিক এবং ভেরিয়েবলকে আপনার মার্কআপে নিয়ে আসে।
- এগুলি JSX ট্যাগের কন্টেন্টের ভিতরে বা অ্যাট্রিবিউট হিসেবে `=` এর পরে কাজ করে।
- `{{` এবং `}}` বিশেষ কোন সিনট্যাক্স নয়: এটি JSX `{ }` এর ভিতরে একটি জাভাস্ক্রিপ্ট অবজেক্ট।

</Recap>

<Challenges>

#### ভুলটি ঠিক করুন {/*fix-the-mistake*/}


এই কোডটি ক্র্যাশ করে এবং `Objects are not valid as a React child` এরর প্রদর্শন করে।

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
      <h1>{person}'s Todos</h1>
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

আপনি কি সমস্যাটি খুঁজে পেতে পারেন?

<Hint>কার্লি ব্রেসের ভেতরে কী রয়েছে, তা লক্ষ্য করুন। আমরা কি সঠিক জিনিসটি সেখানে রাখছি?</Hint>

<Solution>



এই সমস্যা হচ্ছে কারণ এই উদাহরণটি *একটি অবজেক্টকেই* মার্কআপে রেন্ডার করার চেষ্টা করছে একটি স্ট্রিংয়ের পরিবর্তে: `<h1>{person}'s Todos</h1>` পুরো `person` অবজেক্টটি রেন্ডার করার চেষ্টা করছে! টেক্সট কন্টেন্ট হিসেবে সরাসরি অবজেক্ট অন্তর্ভুক্ত করলে এরর দেখা দেয়, কারণ React জানে না আপনি কীভাবে তা প্রদর্শন করতে চান।

এটি ঠিক করতে, `<h1>{person}'s Todos</h1>` পরিবর্তন করে `<h1>{person.name}'s Todos</h1>` ব্যবহার করুন।

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

</Solution>

#### তথ্যগুলো একটি অবজেক্টে স্থানান্তর করুন {/*extract-information-into-an-object*/}

ইমেজের URL `person` অবজেক্টে সংরক্ষণ করুন।

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

<Solution>

ইমেজের URL-টি `person.imageUrl` নামে একটি প্রপার্টিতে স্থানান্তর করুন এবং কার্লি ব্রেস ব্যবহার করে এটি `<img>` ট্যাগ থেকে পড়ুন:

<Sandpack>

```js
const person = {
  name: 'Gregorio Y. Zara',
  imageUrl: "https://i.imgur.com/7vQD0fPs.jpg",
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
        src={person.imageUrl}
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

</Solution>

#### JSX কার্লি ব্রেসের মধ্যে একটি এক্সপ্রেশন লিখুন {/*write-an-expression-inside-jsx-curly-braces*/}

নিচের অবজেক্টটিতে সম্পূর্ণ ইমেজ URL চারটি অংশে বিভক্ত: বেস URL, `imageId`, `imageSize`, এবং ফাইল এক্সটেনশন।

আমরা চাই যে ইমেজ URL এই অ্যাট্রিবিউটগুলোকে একত্রিত করে: বেস URL (সদা `'https://i.imgur.com/'`), `imageId` (`'7vQD0fP'`), `imageSize` (`'s'`), এবং ফাইল এক্সটেনশন (সদা `'.jpg'`)। তবে, `<img>` ট্যাগটি এর `src` নির্দিষ্ট করার সময় কিছু ভুল হয়েছে।

আপনি কি এটি ঠিক করতে পারেন?

<Sandpack>

```js

const baseUrl = 'https://i.imgur.com/';
const person = {
  name: 'Gregorio Y. Zara',
  imageId: '7vQD0fP',
  imageSize: 's',
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
        src="{baseUrl}{person.imageId}{person.imageSize}.jpg"
        alt={person.name}
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
.avatar { border-radius: 50%; }
```

</Sandpack>

আপনার ঠিক করা কাজ করেছে কিনা পরীক্ষা করতে, `imageSize` এর মান `'b'` এ পরিবর্তন করে দেখুন। আপনার সম্পাদনার পরে ইমেজের আকার পরিবর্তিত হওয়া উচিত।

<Solution>

আপনি এটি এভাবে লিখতে পারেন: `src={baseUrl + person.imageId + person.imageSize + '.jpg'}`।

1. `{` জাভাস্ক্রিপ্ট এক্সপ্রেশন খুলছে  
2. `baseUrl + person.imageId + person.imageSize + '.jpg'` সঠিক URL স্ট্রিং তৈরি করছে  
3. `}` জাভাস্ক্রিপ্ট এক্সপ্রেশন বন্ধ করছে  

<Sandpack>

```js
const baseUrl = 'https://i.imgur.com/';
const person = {
  name: 'Gregorio Y. Zara',
  imageId: '7vQD0fP',
  imageSize: 's',
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
        src={baseUrl + person.imageId + person.imageSize + '.jpg'}
        alt={person.name}
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
.avatar { border-radius: 50%; }
```

</Sandpack>

আপনি এই এক্সপ্রেশনটিকে একটি আলাদা ফাংশনে, যেমন `getImageUrl`, এ স্থানান্তরিত করতে পারেন:

<Sandpack>

```js src/App.js
import { getImageUrl } from './utils.js'

const person = {
  name: 'Gregorio Y. Zara',
  imageId: '7vQD0fP',
  imageSize: 's',
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
        src={getImageUrl(person)}
        alt={person.name}
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

```js src/utils.js
export function getImageUrl(person) {
  return (
    'https://i.imgur.com/' +
    person.imageId +
    person.imageSize +
    '.jpg'
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; }
```

</Sandpack>

ভেরিয়েবল এবং ফাংশনগুলো আপনার মার্কআপকে সরল রাখতে সাহায্য করতে পারে!

</Solution>

</Challenges>
