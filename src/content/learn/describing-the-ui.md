---
title: UI এর বর্ণনা
---

<Intro>

React একটা জাভাস্ক্রিপ্ট লাইব্রেরি যার কাজ user interfaces (UI) রেন্ডার করা। UI বিচিন্ন ক্ষুদ্র একক যেমন বাটন, টেক্সট বা ছবির মাধ্যমে গোড়ে ওঠে। React আপনাকে সুযোগ দেয় সেগুলোকে reusable, nestable *কম্পোনেন্টে* একত্রিত করার। ওয়েবসাইট থেকে ফোনের অ্যাপ, যা কিছু আপনি স্ক্রিনে দেখতে পান, সব কিছুই কম্পোনেন্টে ভেঙে ফেলা সম্ভব। এই অধ্যায়ে, আপনি React কম্পোনেন্ট বানানো, পরিবর্তন করা এবং লজিকের উপর ভিত্তি করে প্রদর্শন করা শিখবেন। 

</Intro>

<YouWillLearn isChapter={true}>

* [আপনার প্রথম কম্পোনেন্ট কীভাবে লিখবেন](/learn/your-first-component)
* [কখন এবং কীভাবে মাল্টি-কম্পোনেন্ট ফাইল তৈরি করবেন](/learn/importing-and-exporting-components)
* [JSX দিয়ে কীভাবে জাভাস্ক্রিপ্টে মার্কআপ যুক্ত করবেন](/learn/writing-markup-with-jsx)
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

## JSX দিয়া মার্ক আপ লেখা{/*writing-markup-with-jsx*/}

Each React component is a JavaScript function that may contain some markup that React renders into the browser. React components use a syntax extension called JSX to represent that markup. JSX looks a lot like HTML, but it is a bit stricter and can display dynamic information.

If we paste existing HTML markup into a React component, it won't always work:

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

If you have existing HTML like this, you can fix it using a [converter](https://transform.tools/html-to-jsx):

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

Read **[Writing Markup with JSX](/learn/writing-markup-with-jsx)** to learn how to write valid JSX.

</LearnMore>

## JavaScript in JSX with curly braces {/*javascript-in-jsx-with-curly-braces*/}

JSX lets you write HTML-like markup inside a JavaScript file, keeping rendering logic and content in the same place. Sometimes you will want to add a little JavaScript logic or reference a dynamic property inside that markup. In this situation, you can use curly braces in your JSX to "open a window" to JavaScript:

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

Read **[JavaScript in JSX with Curly Braces](/learn/javascript-in-jsx-with-curly-braces)** to learn how to access JavaScript data from JSX.

</LearnMore>

## Passing props to a component {/*passing-props-to-a-component*/}

React components use *props* to communicate with each other. Every parent component can pass some information to its child components by giving them props. Props might remind you of HTML attributes, but you can pass any JavaScript value through them, including objects, arrays, functions, and even JSX!

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

Read **[Passing Props to a Component](/learn/passing-props-to-a-component)** to learn how to pass and read props.

</LearnMore>

## Conditional rendering {/*conditional-rendering*/}

Your components will often need to display different things depending on different conditions. In React, you can conditionally render JSX using JavaScript syntax like `if` statements, `&&`, and `? :` operators.

In this example, the JavaScript `&&` operator is used to conditionally render a checkmark:

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

Read **[Conditional Rendering](/learn/conditional-rendering)** to learn the different ways to render content conditionally.

</LearnMore>

## Rendering lists {/*rendering-lists*/}

You will often want to display multiple similar components from a collection of data. You can use JavaScript's `filter()` and `map()` with React to filter and transform your array of data into an array of components.

For each array item, you will need to specify a `key`. Usually, you will want to use an ID from the database as a `key`. Keys let React keep track of each item's place in the list even if the list changes.

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

Read **[Rendering Lists](/learn/rendering-lists)** to learn how to render a list of components, and how to choose a key.

</LearnMore>

## Keeping components pure {/*keeping-components-pure*/}

Some JavaScript functions are *pure.* A pure function:

* **Minds its own business.** It does not change any objects or variables that existed before it was called.
* **Same inputs, same output.** Given the same inputs, a pure function should always return the same result.

By strictly only writing your components as pure functions, you can avoid an entire class of baffling bugs and unpredictable behavior as your codebase grows. Here is an example of an impure component:

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

You can make this component pure by passing a prop instead of modifying a preexisting variable:

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

Read **[Keeping Components Pure](/learn/keeping-components-pure)** to learn how to write components as pure, predictable functions.

</LearnMore>

## What's next? {/*whats-next*/}

Head over to [Your First Component](/learn/your-first-component) to start reading this chapter page by page!

Or, if you're already familiar with these topics, why not read about [Adding Interactivity](/learn/adding-interactivity)?
