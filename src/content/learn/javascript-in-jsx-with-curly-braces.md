---
title: JavaScript in JSX with Curly Braces
---

<Intro>

JSX lets you write HTML-like markup inside a JavaScript file, keeping rendering logic and content in the same place. Sometimes you will want to add a little JavaScript logic or reference a dynamic property inside that markup. In this situation, you can use curly braces in your JSX to open a window to JavaScript.

</Intro>

<YouWillLearn>

* Quotes দিয়ে স্ট্রিং কিভাবে পাস করবেন
* Curly braces দিয়ে JSX এর ভিতরে একটি জাভাস্ক্রিপ্ট ভেরিয়েবলকে কীভাবে reference করবেন
* Curly braces দিয়ে JSX-এর ভিতরে একটি জাভাস্ক্রিপ্ট ফাংশনকে কীভাবে কল করবেন
* Curly braces দিয়ে JSX এর ভিতরে একটি জাভাস্ক্রিপ্ট অবজেক্ট কীভাবে ব্যবহার করবেন

</YouWillLearn>

## quotes এর সাথে স্ট্রিং পাসিং {/*passing-strings-with-quotes*/}

আপনি যখন JSX-এ একটি স্ট্রিং attribute পাস করতে চান, তখন আপনাকে এটি single বা double quotes এ রাখতে হবে:

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

এখানে, `"https://i.imgur.com/7vQD0fPs.jpg"` এবং `"Gregorio Y. Zara"` কে স্ট্রিং হিসেবে পাস করা হয়েছে ।

কিন্তু যদি আপনি dynamic ভাবে `src` বা `alt` টেক্সট ব্যবহার করতে চান? তাহলে আপনি **জাভাস্ক্রিপ্ট থেকে একটি value ব্যবহার করে `"`and`"` কে curly braces `{`and`}`** দিয়ে পরিবর্তন করতে পারেন:

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

`className="avatar"` এবং `src={avatar}` এর মধ্যে পার্থক্য লক্ষ্য করুন। `className="avatar"` একটি `"avatar"` নামের CSS ক্লাসকে নির্দিষ্ট করে ও ইমেজকে গোলাকার করে অপরদিকে, `src={avatar}`, `avatar` নামের জাভাস্ক্রিপ্ট ভেরিয়েবলের value পড়ে। এর কারণ হচ্ছে, curly braces আপনাকে আপনার মার্কআপ এর ভিতরে জাভাস্ক্রিপ্ট এর কাজ করতে দেয়!

## Curly braces ব্যবহার: জাভাস্ক্রিপ্ট জগতের একটি window {/*using-curly-braces-a-window-into-the-javascript-world*/}

JSX হল জাভাস্ক্রিপ্ট লেখার একটি বিশেষ উপায়। তার মানে curly braces `{ }` দিয়ে JSX এর ভিতরে জাভাস্ক্রিপ্ট ব্যবহার করা সম্ভব। নীচের উদাহরণটিতে প্রথমে বিজ্ঞানীর জন্য একটি নাম ঘোষণা করা হয়েছে, `name`, তারপর এটিকে `<h1>` এর ভিতরে curly braces দিয়ে embeds করা হয়েছে:

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

নামের value `'Gregorio Y. Zara'` থেকে `'Hedy Lamarr'`-এ পরিবর্তন করার চেষ্টা করুন। দেখুন কিভাবে list title পরিবর্তন হয়?

যেকোন জাভাস্ক্রিপ্ট এক্সপ্রেশন curly braces মধ্যে কাজ করবে, যেমন `formatDate()` এর মতো function calls:

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

### কোথায় curly braces ব্যবহার করবেন {/*where-to-use-curly-braces*/}

আপনি JSX এর ভিতরে শুধুমাত্র দুটি উপায়ে curly braces ব্যবহার করতে পারেন:

1. সরাসরি JSX ট্যাগের ভিতরে **text হিসাবে**: `<h1>{name}'s To Do List</h1>` কাজ করে, কিন্তু `<{tag}>Gregorio Y. Zara's To Do List</{tag}>` করবে না৷

2. **Attributes হিসেবে** সরাসরি `=` চিহ্ন ব্যবহার করে: `src={avatar}` এখানে `avatar` ভেরিয়েবল নিবে, কিন্তু এটি `src="{avatar}"` স্ট্রিং হিসেবে পাস করবে `"{avatar}"`

## "double curlies" ব্যবহার: JSX-এ CSS এবং অন্যান্য অবজেক্ট {/*using-double-curlies-css-and-other-objects-in-jsx*/}


স্ট্রিং, নাম্বার এবং অন্যান্য জাভাস্ক্রিপ্ট এক্সপ্রেশন ছাড়াও, আপনি JSX এ অবজেক্টও পাস করতে পারেন। জাভাস্ক্রিপ্ট এ অবজেক্টকে curly braces দিয়ে লেখা হয়, যেমন `{ name: "Hedy Lamarr", inventions: 5 }`। অতএব, JSX-এ একটি JS অবজেক্ট পাস করতে হলে, অবশ্যই অবজেক্টকে অন্য আরেকটি curly braces এর ভিতরে দিয়ে দিতে হবে: `person={{ name: "Hedy Lamarr", inventions: 5 }}`।

আপনি JSX এ ইনলাইন CSS styles এ এটি দেখতে পারেন। React জন্য আপনাকে ইনলাইন styles ব্যবহার করতেই হবে তা না (প্রায় ক্ষেত্রেই CSS ক্লাসগুলি ভালো কাজ করে)। কিন্তু যখন আপনার একটি ইনলাইন style এর প্রয়োজন হবে, তখন আপনি একটি অবজেক্টকে `style` attribute এ পাস করতে পারেন:

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

curly braces এর ভিতরে জাভাস্ক্রিপ্ট অবজেক্ট দেখতে পাবেন যখন আপনি এটি এভাবে লিখবেন:

```js {2-5}
<ul style={
  {
    backgroundColor: 'black',
    color: 'pink'
  }
}>
```

পরবর্তীতে যখন আপনি JSX-এ `{{` and `}}` দেখবেন, তখন আপনি বুঝে নিবেন যে এটি JSX curlies এর ভিতরে একটি বস্তু ছাড়া আর কিছুই নয়! 

<Pitfall>

ইনলাইন `style` এ property গুলোকে camelCase ব্যবহার করে লেখা হয়. উদাহরণস্বরূপ, এই HTML `<ul style="background-color: black">` আপনার কম্পোনেন্টের মধ্যে `<ul style={{ backgroundColor: 'black' }}>` এভাবে লিখতে হবে।

</Pitfall>

## More fun with JavaScript objects and curly braces {/*more-fun-with-javascript-objects-and-curly-braces*/}

আপনি একটি অবজেক্ট এ বেশ কয়েকটি এক্সপ্রেশন রেখে, পরে সেটিকে curly braces এর ভিতরে JSX-এ ব্যবহার করতে পারেন:

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

এই উদাহরণটিতে, `person` জাভাস্ক্রিপ্ট অবজেক্ট এ একটি `name` স্ট্রিং এবং একটি `theme` অবজেক্ট রয়েছে:

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};
```

এই কম্পোনেন্ট 'person' অবজেক্ট থেকে value গুলি ব্যবহার করতে পারে:

```js
<div style={person.theme}>
  <h1>{person.name}'s Todos</h1>
```

JSX হল একটি খুবই সাধারণ টেমপ্লেটিং ল্যাংগুয়েজে কারণ এটি আপনাকে জাভাস্ক্রিপ্ট ব্যবহার করে data এবং logic সংগঠিত করতে দেয়।

<Recap>

এখন আপনি JSX সম্পর্কে প্রায় সবকিছু জানেন:

* JSX attribute গুলির এর ভিতরে quote গুলি স্ট্রিং হিসাবে পাস করা হয়।
* Curly braces আপনাকে আপনার মার্কআপে জাভাস্ক্রিপ্ট লজিক এবং ভেরিয়েবল লিখতে দেয়।
* Curly braces JSX ট্যাগ কন্টেন্টের ভিতরে কাজ করে অথবা attributes `=` এর পরপরই কাজ করে।
* `{{` and `}}` এটি কোন বিশেষ সিনট্যাক্স নয়: এটি একটি জাভাস্ক্রিপ্ট অবজেক্ট যা JSX এ curly braces এর ভিতরে ব্যবহার করা হয়।

</Recap>

<Challenges>

#### Fix the mistake {/*fix-the-mistake*/}

This code crashes with an error saying `Objects are not valid as a React child`:

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

Can you find the problem?

<Hint>Look for what's inside the curly braces. Are we putting the right thing there?</Hint>

<Solution>

This is happening because this example renders *an object itself* into the markup rather than a string: `<h1>{person}'s Todos</h1>` is trying to render the entire `person` object! Including raw objects as text content throws an error because React doesn't know how you want to display them.

To fix it, replace `<h1>{person}'s Todos</h1>` with `<h1>{person.name}'s Todos</h1>`:

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

#### Extract information into an object {/*extract-information-into-an-object*/}

Extract the image URL into the `person` object.

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

Move the image URL into a property called `person.imageUrl` and read it from the `<img>` tag using the curlies:

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

#### Write an expression inside JSX curly braces {/*write-an-expression-inside-jsx-curly-braces*/}

In the object below, the full image URL is split into four parts: base URL, `imageId`, `imageSize`, and file extension.

We want the image URL to combine these attributes together: base URL (always `'https://i.imgur.com/'`), `imageId` (`'7vQD0fP'`), `imageSize` (`'s'`), and file extension (always `'.jpg'`). However, something is wrong with how the `<img>` tag specifies its `src`.

Can you fix it?

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

To check that your fix worked, try changing the value of `imageSize` to `'b'`. The image should resize after your edit.

<Solution>

You can write it as `src={baseUrl + person.imageId + person.imageSize + '.jpg'}`.

1. `{` opens the JavaScript expression
2. `baseUrl + person.imageId + person.imageSize + '.jpg'` produces the correct URL string
3. `}` closes the JavaScript expression

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

You can also move this expression into a separate function like `getImageUrl` below:

<Sandpack>

```js App.js
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

```js utils.js
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

Variables and functions can help you keep the markup simple!

</Solution>

</Challenges>
