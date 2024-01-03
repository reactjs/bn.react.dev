---
title: memo
---

<Intro>

`memo` আপনাকে একটি কম্পোনেন্টের পুনরায় রেন্ডারিং এড়াতে দেয় যখন এর প্রপস অপরিবর্তিত থাকে।

```
const MemoizedComponent = memo(SomeComponent, arePropsEqual?)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `memo(Component, arePropsEqual?)` {/*memo*/}

একটি কম্পোনেন্টকে `memo`-তে মোড়ানো হলে এর *memoized* সংস্করণ পাওয়া যায়। এই মেমোয়াইজড কম্পোনেন্ট সাধারণত তার parent কম্পোনেন্ট পুনরায় রেন্ডার হলেও পুনরায় রেন্ডার করা হয় না, যদি তার প্রপস অপরিবর্তিত থাকে। তবে React তারপরও এটি পুনরায় রেন্ডার করতে পারেঃ মেমোয়াইজেশন একটি পারফরম্যান্স অপ্টিমাইজেশন, গ্যারান্টি নয়।

```js
import { memo } from 'react';

const SomeComponent = memo(function SomeComponent(props) {
  // ...
});
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটারস {/*parameters*/}

* `Component`: যে কম্পোনেন্টটি আপনি মেমোয়াইজ করতে চান। `memo` এই কম্পোনেন্টকে পরিবর্তন করে না, বরং এর পরিবর্তে একটি নতুন, মেমোয়াইজড কম্পোনেন্ট রিটার্ন করে। যেকোনো বৈধ React কম্পোনেন্ট, ফাংশন এবং [`forwardRef`](/reference/react/forwardRef) কম্পোনেন্টসহ, গ্রহণযোগ্য।

* **ঐচ্ছিক** `arePropsEqual`: দুটি আর্গুমেন্ট গ্রহণকারী একটি ফাংশন: কম্পোনেন্টের পূর্ববর্তী প্রপস এবং এর নতুন প্রপস। যদি পুরানো এবং নতুন প্রপস একই হয়ঃ অর্থাৎ, যদি কম্পোনেন্ট নতুন প্রপসের সাথে পুরানো প্রপসের মতোই আউটপুট দেয় এবং আচরণ করে, তাহলে এটি `true` রিটার্ন করবে। অন্যথায় এটি `false` রিটার্ন করবে। সাধারণত, আপনি এই ফাংশনটি নির্দিষ্ট করবেন না। ডিফল্ট হিসাবে, React প্রতিটি প্রপকে [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)-এর সাথে তুলনা করবে।

#### রিটার্নস {/*returns*/}

`memo` একটি নতুন React কম্পোনেন্ট ফেরত দেয়। এটি `memo`-তে প্রদত্ত কম্পোনেন্টের মতোই আচরণ করে, তবে React সবসময় এটিকে তার প্যারেন্ট পুনরায় রেন্ডার হলে পুনরায় রেন্ডার করবে না, যদি না তার প্রপস পরিবর্তিত হয়।

---

## ব্যবহার {/*usage*/}

### প্রপস অপরিবর্তিত থাকলে রি-রেন্ডারিং এড়ানো {/*skipping-re-rendering-when-props-are-unchanged*/}

React সাধারণত একটি কম্পোনেন্ট পুনরায় রেন্ডার করে যখন এর প্যারেন্ট পুনরায় রেন্ডার হয়। `memo` দ্বারা, আপনি এমন একটি কম্পোনেন্ট তৈরি করতে পারেন যা React তার প্যারেন্ট পুনরায় রেন্ডার হলেও পুনরায় রেন্ডার করবে না, যতক্ষণ না এর নতুন প্রপস পুরানো প্রপসের সাথে একই থাকে। এমন একটি কম্পোনেন্টকে *মেমোয়াইজড* বলা হয়।

একটি কম্পোনেন্টকে মেমোয়াইজ করতে, এটিকে `memo`-এ wrap korun মোড়ান এবং এর রিটার্ন করা মানটি আপনার মূল কম্পোনেন্টের পরিবর্তে ব্যবহার করুনঃ

```js
const Greeting = memo(function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
});

export default Greeting;
```

একটি React কম্পোনেন্টের সবসময় [pure রেন্ডারিং লজিক](/learn/keeping-components-pure) থাকা উচিত। এর অর্থ এটি একই আউটপুট ফেরত দেবে যদি এর প্রপস, স্টেট এবং কন্টেক্স্ট পরিবর্তিত না হয়। `memo` ব্যবহার করে, আপনি React-কে জানাচ্ছেন যে আপনার কম্পোনেন্ট এই প্রয়োজনীয়তাটি পূরণ করে, তাই React-এর প্রপস পরিবর্তিত না হলে পুনরায় রেন্ডার করতে হবে না। `memo` ব্যবহার করলেও, আপনার কম্পোনেন্ট তার নিজের স্টেট পরিবর্তন হলে অথবা ব্যবহৃত কন্টেক্স্ট পরিবর্তন হলে পুনরায় রেন্ডার করবে।

এই উদাহরণে, লক্ষ্য করুন যে `Greeting` কম্পোনেন্টটি `name` পরিবর্তিত হলে পুনরায় রেন্ডার হয় (কারণ এটি এর একটি প্রপ), কিন্তু `address` পরিবর্তন হলে নয় (কারণ এটি `Greeting`-এ প্রপ হিসেবে পাস করা হয় না):

<Sandpack>

```js
import { memo, useState } from 'react';

export default function MyApp() {
  const [name, setName] = useState('');
  const [address, setAddress] = useState('');
  return (
    <>
      <label>
        Name{': '}
        <input value={name} onChange={e => setName(e.target.value)} />
      </label>
      <label>
        Address{': '}
        <input value={address} onChange={e => setAddress(e.target.value)} />
      </label>
      <Greeting name={name} />
    </>
  );
}

const Greeting = memo(function Greeting({ name }) {
  console.log("Greeting was rendered at", new Date().toLocaleTimeString());
  return <h3>Hello{name && ', '}{name}!</h3>;
});
```

```css
label {
  display: block;
  margin-bottom: 16px;
}
```

</Sandpack>

<Note>

**আপনি কেবল কর্মক্ষমতা অপ্টিমাইজেশন হিসাবে `memo`-এর উপর নির্ভর করা উচিত।** যদি আপনার কোড `memo` ছাড়া কাজ না করে, তাহলে প্রথমে মৌলিক সমস্যাটি খুঁজে বের করুন এবং তা ঠিক করুন। তারপর আপনি কর্মক্ষমতা উন্নতির জন্য `memo` যোগ করতে পারেন।

</Note>

<DeepDive>

#### Should you add memo everywhere? {/*should-you-add-memo-everywhere*/}

If your app is like this site, and most interactions are coarse (like replacing a page or an entire section), memoization is usually unnecessary. On the other hand, if your app is more like a drawing editor, and most interactions are granular (like moving shapes), then you might find memoization very helpful. 

Optimizing with `memo`  is only valuable when your component re-renders often with the same exact props, and its re-rendering logic is expensive. If there is no perceptible lag when your component re-renders, `memo` is unnecessary. Keep in mind that `memo` is completely useless if the props passed to your component are *always different,* such as if you pass an object or a plain function defined during rendering. This is why you will often need [`useMemo`](/reference/react/useMemo#skipping-re-rendering-of-components) and [`useCallback`](/reference/react/useCallback#skipping-re-rendering-of-components) together with `memo`.

There is no benefit to wrapping a component in `memo` in other cases. There is no significant harm to doing that either, so some teams choose to not think about individual cases, and memoize as much as possible. The downside of this approach is that code becomes less readable. Also, not all memoization is effective: a single value that's "always new" is enough to break memoization for an entire component.

**In practice, you can make a lot of memoization unnecessary by following a few principles:**

1. When a component visually wraps other components, let it [accept JSX as children.](/learn/passing-props-to-a-component#passing-jsx-as-children) This way, when the wrapper component updates its own state, React knows that its children don't need to re-render.
1. Prefer local state and don't [lift state up](/learn/sharing-state-between-components) any further than necessary. For example, don't keep transient state like forms and whether an item is hovered at the top of your tree or in a global state library.
1. Keep your [rendering logic pure.](/learn/keeping-components-pure) If re-rendering a component causes a problem or produces some noticeable visual artifact, it's a bug in your component! Fix the bug instead of adding memoization.
1. Avoid [unnecessary Effects that update state.](/learn/you-might-not-need-an-effect) Most performance problems in React apps are caused by chains of updates originating from Effects that cause your components to render over and over.
1. Try to [remove unnecessary dependencies from your Effects.](/learn/removing-effect-dependencies) For example, instead of memoization, it's often simpler to move some object or a function inside an Effect or outside the component.

If a specific interaction still feels laggy, [use the React Developer Tools profiler](https://legacy.reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html) to see which components would benefit the most from memoization, and add memoization where needed. These principles make your components easier to debug and understand, so it's good to follow them in any case. In the long term, we're researching [doing granular memoization automatically](https://www.youtube.com/watch?v=lGEMwh32soc) to solve this once and for all.

</DeepDive>

---

### Updating a memoized component using state {/*updating-a-memoized-component-using-state*/}

Even when a component is memoized, it will still re-render when its own state changes. Memoization only has to do with props that are passed to the component from its parent.

<Sandpack>

```js
import { memo, useState } from 'react';

export default function MyApp() {
  const [name, setName] = useState('');
  const [address, setAddress] = useState('');
  return (
    <>
      <label>
        Name{': '}
        <input value={name} onChange={e => setName(e.target.value)} />
      </label>
      <label>
        Address{': '}
        <input value={address} onChange={e => setAddress(e.target.value)} />
      </label>
      <Greeting name={name} />
    </>
  );
}

const Greeting = memo(function Greeting({ name }) {
  console.log('Greeting was rendered at', new Date().toLocaleTimeString());
  const [greeting, setGreeting] = useState('Hello');
  return (
    <>
      <h3>{greeting}{name && ', '}{name}!</h3>
      <GreetingSelector value={greeting} onChange={setGreeting} />
    </>
  );
});

function GreetingSelector({ value, onChange }) {
  return (
    <>
      <label>
        <input
          type="radio"
          checked={value === 'Hello'}
          onChange={e => onChange('Hello')}
        />
        Regular greeting
      </label>
      <label>
        <input
          type="radio"
          checked={value === 'Hello and welcome'}
          onChange={e => onChange('Hello and welcome')}
        />
        Enthusiastic greeting
      </label>
    </>
  );
}
```

```css
label {
  display: block;
  margin-bottom: 16px;
}
```

</Sandpack>

If you set a state variable to its current value, React will skip re-rendering your component even without `memo`. You may still see your component function being called an extra time, but the result will be discarded.

---

### Updating a memoized component using a context {/*updating-a-memoized-component-using-a-context*/}

Even when a component is memoized, it will still re-render when a context that it's using changes. Memoization only has to do with props that are passed to the component from its parent.

<Sandpack>

```js
import { createContext, memo, useContext, useState } from 'react';

const ThemeContext = createContext(null);

export default function MyApp() {
  const [theme, setTheme] = useState('dark');

  function handleClick() {
    setTheme(theme === 'dark' ? 'light' : 'dark'); 
  }

  return (
    <ThemeContext.Provider value={theme}>
      <button onClick={handleClick}>
        Switch theme
      </button>
      <Greeting name="Taylor" />
    </ThemeContext.Provider>
  );
}

const Greeting = memo(function Greeting({ name }) {
  console.log("Greeting was rendered at", new Date().toLocaleTimeString());
  const theme = useContext(ThemeContext);
  return (
    <h3 className={theme}>Hello, {name}!</h3>
  );
});
```

```css
label {
  display: block;
  margin-bottom: 16px;
}

.light {
  color: black;
  background-color: white;
}

.dark {
  color: white;
  background-color: black;
}
```

</Sandpack>

To make your component re-render only when a _part_ of some context changes, split your component in two. Read what you need from the context in the outer component, and pass it down to a memoized child as a prop.

---

### Minimizing props changes {/*minimizing-props-changes*/}

When you use `memo`, your component re-renders whenever any prop is not *shallowly equal* to what it was previously. This means that React compares every prop in your component with its previous value using the [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) comparison. Note that `Object.is(3, 3)` is `true`, but `Object.is({}, {})` is `false`.


To get the most out of `memo`, minimize the times that the props change. For example, if the prop is an object, prevent the parent component from re-creating that object every time by using [`useMemo`:](/reference/react/useMemo)

```js {5-8}
function Page() {
  const [name, setName] = useState('Taylor');
  const [age, setAge] = useState(42);

  const person = useMemo(
    () => ({ name, age }),
    [name, age]
  );

  return <Profile person={person} />;
}

const Profile = memo(function Profile({ person }) {
  // ...
});
```

A better way to minimize props changes is to make sure the component accepts the minimum necessary information in its props. For example, it could accept individual values instead of a whole object:

```js {4,7}
function Page() {
  const [name, setName] = useState('Taylor');
  const [age, setAge] = useState(42);
  return <Profile name={name} age={age} />;
}

const Profile = memo(function Profile({ name, age }) {
  // ...
});
```

Even individual values can sometimes be projected to ones that change less frequently. For example, here a component accepts a boolean indicating the presence of a value rather than the value itself:

```js {3}
function GroupsLanding({ person }) {
  const hasGroups = person.groups !== null;
  return <CallToAction hasGroups={hasGroups} />;
}

const CallToAction = memo(function CallToAction({ hasGroups }) {
  // ...
});
```

When you need to pass a function to memoized component, either declare it outside your component so that it never changes, or [`useCallback`](/reference/react/useCallback#skipping-re-rendering-of-components) to cache its definition between re-renders.

---

### Specifying a custom comparison function {/*specifying-a-custom-comparison-function*/}

In rare cases it may be infeasible to minimize the props changes of a memoized component. In that case, you can provide a custom comparison function, which React will use to compare the old and new props instead of using shallow equality. This function is passed as a second argument to `memo`. It should return `true` only if the new props would result in the same output as the old props; otherwise it should return `false`.

```js {3}
const Chart = memo(function Chart({ dataPoints }) {
  // ...
}, arePropsEqual);

function arePropsEqual(oldProps, newProps) {
  return (
    oldProps.dataPoints.length === newProps.dataPoints.length &&
    oldProps.dataPoints.every((oldPoint, index) => {
      const newPoint = newProps.dataPoints[index];
      return oldPoint.x === newPoint.x && oldPoint.y === newPoint.y;
    })
  );
}
```

If you do this, use the Performance panel in your browser developer tools to make sure that your comparison function is actually faster than re-rendering the component. You might be surprised.

When you do performance measurements, make sure that React is running in the production mode.

<Pitfall>

If you provide a custom `arePropsEqual` implementation, **you must compare every prop, including functions.** Functions often [close over](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) the props and state of parent components. If you return `true` when `oldProps.onClick !== newProps.onClick`, your component will keep "seeing" the props and state from a previous render inside its `onClick` handler, leading to very confusing bugs.

Avoid doing deep equality checks inside `arePropsEqual` unless you are 100% sure that the data structure you're working with has a known limited depth. **Deep equality checks can become incredibly slow** and can freeze your app for many seconds if someone changes the data structure later.

</Pitfall>

---

## Troubleshooting {/*troubleshooting*/}
### My component re-renders when a prop is an object, array, or function {/*my-component-rerenders-when-a-prop-is-an-object-or-array*/}

React compares old and new props by shallow equality: that is, it considers whether each new prop is reference-equal to the old prop. If you create a new object or array each time the parent is re-rendered, even if the individual elements are each the same, React will still consider it to be changed. Similarly, if you create a new function when rendering the parent component, React will consider it to have changed even if the function has the same definition. To avoid this, [simplify props or memoize props in the parent component](#minimizing-props-changes).
