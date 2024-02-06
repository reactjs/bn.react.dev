---
title: কম্পোনেন্টকে বিশুদ্ধ রাখা
---

<Intro>

কিছু জাভাস্ক্রিপ্ট ফাংশন *বিশুদ্ধ।* বিশুদ্ধ ফাংশনগুলো শুধুমাত্র একটি হিসাব করে, অন্য কিছু নয়। আপনি যথাযথভাবে আপনার কম্পোনেন্টগুলোকে বিশুদ্ধ ফাংশন হিসাবে লিখার মাধ্যমে আপনার কোডবেইজ বাড়ার সাথে সাথে অনেক বিভ্রান্তিকর বাগ এবং অনিশ্চিত আচরণ এড়িয়ে যেতে পারবেন। এই সুবিধাগুলো পাওয়ার জন্য আপনার নির্দিষ্ট কিছু নিয়ম মেনে চলতে হবে।

</Intro>

<YouWillLearn>

* বিশুদ্ধতা কি এবং এটি কিভাবে আপনাকে বাগ এড়িয়ে যেতে সাহায্য করে
* কম্পোনেন্টকে কিভাবে বিশুদ্ধ রাখা যায় পরিবর্তনগুলোকে রেন্ডারের ধাপের বাইরে রেখে
* কিভাবে Strict Mode ব্যবহার করে আপনার কম্পোনেন্টের ভুলগুলো ধরতে পারবেন

</YouWillLearn>

## বিশুদ্ধতাঃ সূত্র হিসেবে কম্পোনেন্ট {/*purity-components-as-formulas*/}

কম্পিউটার সায়েন্সে (বিশেষ করে ফাংশনাল প্রোগ্রামিং-এ), [একটি বিশুদ্ধ ফাংশন](https://wikipedia.org/wiki/Pure_function) হল এমন একটি ফাংশন যার নিচের বৈশিষ্ট্যগুলো রয়েছেঃ

* **নিজের কাজ নিয়েই ব্যস্ত।** এটি কল করার আগের কোন অবজেক্ট বা ভ্যারিয়েবল পরিবর্তন করেনা।
* **একই ইনপুট, একই আউটপুট।** একই ইনপুটের জন্য একটি বিশুদ্ধ ফাংশন সবসময় একই আউটপুট রিটার্ন করে।

আপনি হয়তো এরই মধ্যে বিশুদ্ধ ফাংশনের একটি উদাহরণের সাথে পরিচিতঃ গণিতের সূত্র।

এই গণিতের সূত্রটিই ধরুনঃ <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math>.

যদি <Math><MathI>x</MathI> = 2</Math> হয়, তাহলে <Math><MathI>y</MathI> = 4</Math> হবে। সবসময়। 

যদি <Math><MathI>x</MathI> = 3</Math> হয়, তাহলে <Math><MathI>y</MathI> = 6</Math> হবে। সবসময়। 

যদি <Math><MathI>x</MathI> = 3</Math> হয়, তাহলে <MathI>y</MathI> কখনো কখনো <Math>9</Math> অথবা <Math>–1</Math> অথবা <Math>2.5</Math> হবেনা, দিনের কোন সময় বা স্টক মার্কেটের অবস্থার উপর নির্ভর করে।

যদি <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math> এবং <Math><MathI>x</MathI> = 3</Math> হয়, <MathI>y</MathI> তাহলে _সবসময়_ <Math>6</Math> হবে। 

আমরা যদি এটিকে একটি জাভাস্ক্রিপ্ট ফাংশনে রুপ দেই, তাহলে এটি এমন দেখাবেঃ

```js
function double(number) {
  return 2 * number;
}
```

উপরের উদাহরণে, `double` একটি **বিশুদ্ধ ফাংশন।** আপনি যদি এটিতে `3` পাস করেন এটি `6` রিটার্ন করবে। সবসময়।

React এই ধারণার উপর ভিত্তি করেই তৈরি করা হয়েছে। **React ধরে নেয় আপনার লেখা সব কম্পোনেন্টই বিশুদ্ধ ফাংশন।** তার মানে আপনার লিখা React কম্পোনেন্টগুলোর অবশ্যই একই ইনপুটের জন্য একই JSX রিটার্ন করতে হবেঃ

<Sandpack>

```js src/App.js
function Recipe({ drinkers }) {
  return (
    <ol>    
      <li>Boil {drinkers} cups of water.</li>
      <li>Add {drinkers} spoons of tea and {0.5 * drinkers} spoons of spice.</li>
      <li>Add {0.5 * drinkers} cups of milk to boil and sugar to taste.</li>
    </ol>
  );
}

export default function App() {
  return (
    <section>
      <h1>Spiced Chai Recipe</h1>
      <h2>For two</h2>
      <Recipe drinkers={2} />
      <h2>For a gathering</h2>
      <Recipe drinkers={4} />
    </section>
  );
}
```

</Sandpack>

আপনি যখন `Recipe` তে `drinkers={2}` পাস করবেন, এটি JSX রিটার্ন করবে যাতে `2 cups of water` থাকবে। সবসময়। 

যদি আপনি `drinkers={4}` পাস করেন, এটি JSX রিটার্ন করবে যাতে `4 cups of water` থাকবে। সবসময়।

ঠিক একটি গণিতের সূত্রের মত।

আপনি আপনার কম্পোনেন্টকে একটি রেসিপি হিসেবে চিন্তা করতে পারেনঃ যদি আপনি এগুলো অনুসরণ করেন এবং রান্নার সময়ে নতুন কোন উপাদান না আনেন তাহলে আপনি সবসময় একই খাবার পাবেন। এই "খাবার" হল JSX যা আপনার কম্পোনেন্ট React কে [রেন্ডার](/learn/render-and-commit) করার জন্য পরিবেশন করে।

<Illustration src="/images/docs/illustrations/i_puritea-recipe.png" alt="A tea recipe for x people: take x cups of water, add x spoons of tea and 0.5x spoons of spices, and 0.5x cups of milk" />

## পার্শ্ব-প্রতিক্রিয়াঃ (অন)ইচ্ছাকৃত প্রভাব {/*side-effects-unintended-consequences*/}

React এর রেন্ডারিং প্রসেস সবসময় বিশুদ্ধ হতে হবে। কম্পোনেন্টগুলোর শুধুমাত্র তাদের JSX *রিটার্ন* করা উচিত, এবং রেন্ডারিং এর আগের কোন অবজেক্ট বা ভ্যারিয়েবলে পরিবর্তন আনা উচিত নয়-যা এগুলোকে অবিশুদ্ধ করে দেবে!

এই কম্পোনেন্টটি এই নিয়ম ভঙ্গ করেঃ

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

এই কম্পোনেন্টটি এর বাইরে ডিক্লেয়ার করা `guest` ভ্যারিয়েবল রিড এবং রাইট করছে। তার মানে **এই কম্পোনেন্ট বারবার কল করলে ভিন্ন ভিন্ন JSX রিটার্ন করবে!** তার উপর, যদি _অন্য_ কম্পোনেন্ট `guest` থেকে রিড করে, তারাও ভিন্ন JSX রিটার্ন করবে, তারা কখন রেন্ডার হয়েছিল এর উপর ভিত্তি করে! এটি মোটেও অনুমানযোগ্য নয়।

আমরা যদি আমাদের সূত্রে ফিরে যাই, <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math>, এখন <Math><MathI>x</MathI> = 2</Math> হওয়ার পরেও, আমরা নিশ্চিত হতে পারবনা <Math><MathI>y</MathI> = 4</Math> হবে। আমাদের টেস্ট ফেইল করতে পারে, আমাদের ইউজার বিস্মিত হতে পারে, প্লেইন আকাশ থেকে পড়ে যেতে পারে-দেখতেই পাচ্ছেন এটি কিভাবে বিভিন্ন বিভ্রান্তিকর বাগ তৈরি করতে পারে!

আপনি এই কম্পোনেন্টে [`guest` একটি prop হিসেবে পাস করার মাধ্যমে](/learn/passing-props-to-a-component) এটি ঠিক করতে পারেনঃ

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

এখন আপনার কম্পোনেন্টটি বিশুদ্ধ, যেহেতু এর দ্বারা রিটার্নকৃত JSX শুধুমাত্র `guest` prop এর উপর নির্ভর করে।

সাধারণত, আপনার কম্পোনেন্টকে নির্দিষ্ট কোন ক্রমে রেন্ডার করতে হবে এমন আশা করা উচিত নয়। এটি কোন ব্যাপার না আপনি <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math> এর আগে অথবা পরে  <Math><MathI>y</MathI> = 5<MathI>x</MathI></Math> কল করলেনঃ উভয় সূত্রই স্বাধীনভাবে সমাধান হবে। একইভাবে, প্রতিটা ক্মপোনেন্টেরই "নিজের জন্য চিন্তা করা" উচিত, এবং রেন্ডারিং এর সময় অন্যের সাথে সমন্বয়ের চেষ্টা অথবা অন্যের উপর নির্ভর করা উচিত নয়। রেন্ডারিং হল স্কুলের পরীক্ষার মতঃ প্রতিটা কম্পোনেন্টেরই তাদের নিজেদের JSX হিসাব করা উচিত!

<DeepDive>

#### স্ট্রিক্ট মোডের সাহায্যে ইমপিওর হিসাব শনাক্ত করা {/*detecting-impure-calculations-with-strict-mode*/}

যদিও আপনি এখনো সবগুলো ব্যবহার করেননি, React-এ রেন্ডারিং করার সময় রিড করার জন্য তিন ধরনের ইনপুট রয়েছে: [প্রপস](/learn/passing-props-to-a-component), [স্টেট](/learn/state-a-components-memory), এবং [কন্টেক্সট।](/learn/passing-data-deeply-with-context) আপনার উচিত এই ইনপুটগুলিকে সবসময় রিড-ওনলি হিসেবে বিবেচনা করা।

যখন আপনি ব্যবহারকারীর ইনপুটের প্রতিক্রিয়ায় কিছু *পরিবর্তন* করতে চান, তখন আপনার উচিত [স্টেট সেট করা](/learn/state-a-components-memory) একটি ভেরিয়েবল লিখার পরিবর্তে। আপনার কম্পোনেন্ট রেন্ডারিং হচ্ছে এমন সময়ে আপনার উচিত নয় পূর্ববর্তী ভেরিয়েবলগুলি বা অবজেক্টগুলি পরিবর্তন করা।

React "স্ট্রিক্ট মোড" অফার করে, যেখানে এটি উন্নয়নের সময় প্রতিটি কম্পোনেন্টের ফাংশনটি দুইবার কল করে। **কম্পোনেন্ট ফাংশনগুলি দুইবার কল করার মাধ্যমে, স্ট্রিক্ট মোড এমন কম্পোনেন্টগুলি খুঁজে পেতে সাহায্য করে যেগুলি এই নিয়মগুলি ভঙ্গ করে।**

Notice how the original example displayed "Guest #2", "Guest #4", and "Guest #6" instead of "Guest #1", "Guest #2", and "Guest #3". The original function was impure, so calling it twice broke it. But the fixed pure version works even if the function is called twice every time. **Pure functions only calculate, so calling them twice won't change anything**--just like calling `double(2)` twice doesn't change what's returned, and solving <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math> twice doesn't change what <MathI>y</MathI> is. Same inputs, same outputs. Always.

Strict Mode has no effect in production, so it won't slow down the app for your users. To opt into Strict Mode, you can wrap your root component into `<React.StrictMode>`. Some frameworks do this by default.

</DeepDive>

### Local mutation: Your component's little secret {/*local-mutation-your-components-little-secret*/}

In the above example, the problem was that the component changed a *preexisting* variable while rendering. This is often called a **"mutation"** to make it sound a bit scarier. Pure functions don't mutate variables outside of the function's scope or objects that were created before the call—that makes them impure!

However, **it's completely fine to change variables and objects that you've *just* created while rendering.** In this example, you create an `[]` array, assign it to a `cups` variable, and then `push` a dozen cups into it:

<Sandpack>

```js
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaGathering() {
  let cups = [];
  for (let i = 1; i <= 12; i++) {
    cups.push(<Cup key={i} guest={i} />);
  }
  return cups;
}
```

</Sandpack>

If the `cups` variable or the `[]` array were created outside the `TeaGathering` function, this would be a huge problem! You would be changing a *preexisting* object by pushing items into that array.

However, it's fine because you've created them *during the same render*, inside `TeaGathering`. No code outside of `TeaGathering` will ever know that this happened. This is called **"local mutation"**—it's like your component's little secret.

## Where you _can_ cause side effects {/*where-you-_can_-cause-side-effects*/}

While functional programming relies heavily on purity, at some point, somewhere, _something_ has to change. That's kind of the point of programming! These changes—updating the screen, starting an animation, changing the data—are called **side effects.** They're things that happen _"on the side"_, not during rendering.

In React, **side effects usually belong inside [event handlers.](/learn/responding-to-events)** Event handlers are functions that React runs when you perform some action—for example, when you click a button. Even though event handlers are defined *inside* your component, they don't run *during* rendering! **So event handlers don't need to be pure.**

If you've exhausted all other options and can't find the right event handler for your side effect, you can still attach it to your returned JSX with a [`useEffect`](/reference/react/useEffect) call in your component. This tells React to execute it later, after rendering, when side effects are allowed. **However, this approach should be your last resort.**

When possible, try to express your logic with rendering alone. You'll be surprised how far this can take you!

<DeepDive>

#### Why does React care about purity? {/*why-does-react-care-about-purity*/}

Writing pure functions takes some habit and discipline. But it also unlocks marvelous opportunities:

* Your components could run in a different environment—for example, on the server! Since they return the same result for the same inputs, one component can serve many user requests.
* You can improve performance by [skipping rendering](/reference/react/memo) components whose inputs have not changed. This is safe because pure functions always return the same results, so they are safe to cache.
* If some data changes in the middle of rendering a deep component tree, React can restart rendering without wasting time to finish the outdated render. Purity makes it safe to stop calculating at any time.

Every new React feature we're building takes advantage of purity. From data fetching to animations to performance, keeping components pure unlocks the power of the React paradigm.

</DeepDive>

<Recap>

* A component must be pure, meaning:
  * **It minds its own business.** It should not change any objects or variables that existed before rendering.
  * **Same inputs, same output.** Given the same inputs, a component should always return the same JSX. 
* Rendering can happen at any time, so components should not depend on each others' rendering sequence.
* You should not mutate any of the inputs that your components use for rendering. That includes props, state, and context. To update the screen, ["set" state](/learn/state-a-components-memory) instead of mutating preexisting objects.
* Strive to express your component's logic in the JSX you return. When you need to "change things", you'll usually want to do it in an event handler. As a last resort, you can `useEffect`.
* Writing pure functions takes a bit of practice, but it unlocks the power of React's paradigm.

</Recap>


  
<Challenges>

#### Fix a broken clock {/*fix-a-broken-clock*/}

This component tries to set the `<h1>`'s CSS class to `"night"` during the time from midnight to six hours in the morning, and `"day"` at all other times. However, it doesn't work. Can you fix this component?

You can verify whether your solution works by temporarily changing the computer's timezone. When the current time is between midnight and six in the morning, the clock should have inverted colors!

<Hint>

Rendering is a *calculation*, it shouldn't try to "do" things. Can you express the same idea differently?

</Hint>

<Sandpack>

```js src/Clock.js active
export default function Clock({ time }) {
  let hours = time.getHours();
  if (hours >= 0 && hours <= 6) {
    document.getElementById('time').className = 'night';
  } else {
    document.getElementById('time').className = 'day';
  }
  return (
    <h1 id="time">
      {time.toLocaleTimeString()}
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
  return (
    <Clock time={time} />
  );
}
```

```css
body > * {
  width: 100%;
  height: 100%;
}
.day {
  background: #fff;
  color: #222;
}
.night {
  background: #222;
  color: #fff;
}
```

</Sandpack>

<Solution>

You can fix this component by calculating the `className` and including it in the render output:

<Sandpack>

```js src/Clock.js active
export default function Clock({ time }) {
  let hours = time.getHours();
  let className;
  if (hours >= 0 && hours <= 6) {
    className = 'night';
  } else {
    className = 'day';
  }
  return (
    <h1 className={className}>
      {time.toLocaleTimeString()}
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
  return (
    <Clock time={time} />
  );
}
```

```css
body > * {
  width: 100%;
  height: 100%;
}
.day {
  background: #fff;
  color: #222;
}
.night {
  background: #222;
  color: #fff;
}
```

</Sandpack>

In this example, the side effect (modifying the DOM) was not necessary at all. You only needed to return JSX.

</Solution>

#### Fix a broken profile {/*fix-a-broken-profile*/}

Two `Profile` components are rendered side by side with different data. Press "Collapse" on the first profile, and then "Expand" it. You'll notice that both profiles now show the same person. This is a bug.

Find the cause of the bug and fix it.

<Hint>

The buggy code is in `Profile.js`. Make sure you read it all from top to bottom!

</Hint>

<Sandpack>

```js src/Profile.js
import Panel from './Panel.js';
import { getImageUrl } from './utils.js';

let currentPerson;

export default function Profile({ person }) {
  currentPerson = person;
  return (
    <Panel>
      <Header />
      <Avatar />
    </Panel>
  )
}

function Header() {
  return <h1>{currentPerson.name}</h1>;
}

function Avatar() {
  return (
    <img
      className="avatar"
      src={getImageUrl(currentPerson)}
      alt={currentPerson.name}
      width={50}
      height={50}
    />
  );
}
```

```js src/Panel.js hidden
import { useState } from 'react';

export default function Panel({ children }) {
  const [open, setOpen] = useState(true);
  return (
    <section className="panel">
      <button onClick={() => setOpen(!open)}>
        {open ? 'Collapse' : 'Expand'}
      </button>
      {open && children}
    </section>
  );
}
```

```js src/App.js
import Profile from './Profile.js';

export default function App() {
  return (
    <>
      <Profile person={{
        imageId: 'lrWQx8l',
        name: 'Subrahmanyan Chandrasekhar',
      }} />
      <Profile person={{
        imageId: 'MK3eW3A',
        name: 'Creola Katherine Johnson',
      }} />
    </>
  )
}
```

```js src/utils.js hidden
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
.avatar { margin: 5px; border-radius: 50%; }
.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
  width: 200px;
}
h1 { margin: 5px; font-size: 18px; }
```

</Sandpack>

<Solution>

The problem is that the `Profile` component writes to a preexisting variable called `currentPerson`, and the `Header` and `Avatar` components read from it. This makes *all three of them* impure and difficult to predict.

To fix the bug, remove the `currentPerson` variable. Instead, pass all information from `Profile` to `Header` and `Avatar` via props. You'll need to add a `person` prop to both components and pass it all the way down.

<Sandpack>

```js src/Profile.js active
import Panel from './Panel.js';
import { getImageUrl } from './utils.js';

export default function Profile({ person }) {
  return (
    <Panel>
      <Header person={person} />
      <Avatar person={person} />
    </Panel>
  )
}

function Header({ person }) {
  return <h1>{person.name}</h1>;
}

function Avatar({ person }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={50}
      height={50}
    />
  );
}
```

```js src/Panel.js hidden
import { useState } from 'react';

export default function Panel({ children }) {
  const [open, setOpen] = useState(true);
  return (
    <section className="panel">
      <button onClick={() => setOpen(!open)}>
        {open ? 'Collapse' : 'Expand'}
      </button>
      {open && children}
    </section>
  );
}
```

```js src/App.js
import Profile from './Profile.js';

export default function App() {
  return (
    <>
      <Profile person={{
        imageId: 'lrWQx8l',
        name: 'Subrahmanyan Chandrasekhar',
      }} />
      <Profile person={{
        imageId: 'MK3eW3A',
        name: 'Creola Katherine Johnson',
      }} />
    </>
  );
}
```

```js src/utils.js hidden
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
.avatar { margin: 5px; border-radius: 50%; }
.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
  width: 200px;
}
h1 { margin: 5px; font-size: 18px; }
```

</Sandpack>

Remember that React does not guarantee that component functions will execute in any particular order, so you can't communicate between them by setting variables. All communication must happen through props.

</Solution>

#### Fix a broken story tray {/*fix-a-broken-story-tray*/}

The CEO of your company is asking you to add "stories" to your online clock app, and you can't say no. You've written a `StoryTray` component that accepts a list of `stories`, followed by a "Create Story" placeholder.

You implemented the "Create Story" placeholder by pushing one more fake story at the end of the `stories` array that you receive as a prop. But for some reason, "Create Story" appears more than once. Fix the issue.

<Sandpack>

```js src/StoryTray.js active
export default function StoryTray({ stories }) {
  stories.push({
    id: 'create',
    label: 'Create Story'
  });

  return (
    <ul>
      {stories.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```js src/App.js hidden
import { useState, useEffect } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState([...initialStories])
  let time = useTime();

  // HACK: Prevent the memory from growing forever while you read docs.
  // We're breaking our own rules here.
  if (stories.length > 100) {
    stories.length = 100;
  }

  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <h2>It is {time.toLocaleTimeString()} now.</h2>
      <StoryTray stories={stories} />
    </div>
  );
}

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
```

```css
ul {
  margin: 0;
  list-style-type: none;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

</Sandpack>

<Solution>

Notice how whenever the clock updates, "Create Story" is added *twice*. This serves as a hint that we have a mutation during rendering--Strict Mode calls components twice to make these issues more noticeable.

`StoryTray` function is not pure. By calling `push` on the received `stories` array (a prop!), it is mutating an object that was created *before* `StoryTray` started rendering. This makes it buggy and very difficult to predict.

The simplest fix is to not touch the array at all, and render "Create Story" separately:

<Sandpack>

```js src/StoryTray.js active
export default function StoryTray({ stories }) {
  return (
    <ul>
      {stories.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
      <li>Create Story</li>
    </ul>
  );
}
```

```js src/App.js hidden
import { useState, useEffect } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState([...initialStories])
  let time = useTime();

  // HACK: Prevent the memory from growing forever while you read docs.
  // We're breaking our own rules here.
  if (stories.length > 100) {
    stories.length = 100;
  }

  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <h2>It is {time.toLocaleTimeString()} now.</h2>
      <StoryTray stories={stories} />
    </div>
  );
}

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
```

```css
ul {
  margin: 0;
  list-style-type: none;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

Alternatively, you could create a _new_ array (by copying the existing one) before you push an item into it:

<Sandpack>

```js src/StoryTray.js active
export default function StoryTray({ stories }) {
  // Copy the array!
  let storiesToDisplay = stories.slice();

  // Does not affect the original array:
  storiesToDisplay.push({
    id: 'create',
    label: 'Create Story'
  });

  return (
    <ul>
      {storiesToDisplay.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```js src/App.js hidden
import { useState, useEffect } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState([...initialStories])
  let time = useTime();

  // HACK: Prevent the memory from growing forever while you read docs.
  // We're breaking our own rules here.
  if (stories.length > 100) {
    stories.length = 100;
  }

  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <h2>It is {time.toLocaleTimeString()} now.</h2>
      <StoryTray stories={stories} />
    </div>
  );
}

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
```

```css
ul {
  margin: 0;
  list-style-type: none;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

This keeps your mutation local and your rendering function pure. However, you still need to be careful: for example, if you tried to change any of the array's existing items, you'd have to clone those items too.

It is useful to remember which operations on arrays mutate them, and which don't. For example, `push`, `pop`, `reverse`, and `sort` will mutate the original array, but `slice`, `filter`, and `map` will create a new one.

</Solution>

</Challenges>
