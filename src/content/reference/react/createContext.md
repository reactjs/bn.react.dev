---
title: createContext
---

<Intro>

`createContext` আপনাকে একটি [context](/learn/passing-data-deeply-with-context) তৈরি করতে দেয় যা component provide বা read করতে পারে।

```js
const SomeContext = createContext(defaultValue)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `createContext(defaultValue)` {/*createcontext*/}

কনটেক্সট তৈরী করবার জন্য যেকোন কম্পোনেন্টের বাইরে `createContext` কল করুন।

```js
import { createContext } from 'react';

const ThemeContext = createContext('light');
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

* `defaultValue`: এটা হল সেই ভ্যালু যেটা আপনি চান যে কনটেক্সটের থাকুক। কখন? যখন সেই কনটেক্সট যেই component read করবে, ট্রিতে তার উপরে কোন ম্যাচিং কনটেক্সট প্রোভাইডার নেই। যদি আপনার কোন অর্থবহ default ভ্যালু না থাকে, ভ্যালুটা `null` করে দেন। ডিফল্ট ভ্যালু একদম "last resort" ফলব্যাক হিসেবে রাখা হয়। এটা স্ট্যাটিক এবং সময়ের সাথে অপরিবর্তিত থাকে।

#### রিটার্ন {/*returns*/}

`createContext` একটি কনটেক্সট অবজেক্ট রিটার্ন করে। 

**কনটেক্সট অবজেক্ট নিজে কোন তথ্য ধারণ করে না।** It represents _which_ context other components read or provide. Typically, you will use [`SomeContext.Provider`](#provider) in components above to specify the context value, and call [`useContext(SomeContext)`](/reference/react/useContext) in components below to read it. The context object has a few properties:

* `SomeContext.Provider` আপনাকে কম্পোনেন্টে কনটেক্সট ভ্যালু দেয়ার সুযোগ দেয়।
* `SomeContext.Consumer` একটি উপায়ান্তর যা কনটেক্সট ভ্যালু read করার জন্য বিরল ভাবে ব্যবহৃত হয়।

---

### `SomeContext.Provider` {/*provider*/}

আপনার কম্পোনেন্টগুলোকে একটি কনটেক্সট প্রোভাইডারে wrap করে ফেলুন এবং এর মাধ্যমে এই সকল কম্পোনেন্টের জন্য এই কন্টেক্সটের মান নির্দিষ্ট করে দিনঃ

```js
function App() {
  const [theme, setTheme] = useState('light');
  // ...
  return (
    <ThemeContext.Provider value={theme}>
      <Page />
    </ThemeContext.Provider>
  );
}
```

#### Props {/*provider-props*/}

* `value`: এটা হচ্ছে সেই ভ্যালু যেটা আপনি এই প্রোভাইডারের সেই সকল কম্পোনেন্ট দিয়ে read করাতে চান, সেটা যত গভীরেই হোক না কেন। কনটেক্সট ভ্যালু যেকোন টাইপের হতে পারে। যখন প্রোভাইডারের মধ্যে থাকা একটি কম্পোনেন্ট [`useContext(SomeContext)`](/reference/react/useContext) কল করে, তখন এটা তার উপরে সবচেয়ে ভিতরকার কনটেক্সট প্রোভাইডারের `value` রিসিভ করে।

---

### `SomeContext.Consumer` {/*consumer*/}

`useContext` আসার আগে, কনটেক্সট read করার জন্য একটি পুরনো উপায় ছিলঃ

```js
function Button() {
  // 🟡 Legacy way (not recommended)
  return (
    <ThemeContext.Consumer>
      {theme => (
        <button className={theme} />
      )}
    </ThemeContext.Consumer>
  );
}
```

যদিও এই পুরনো উপায়টি এখনো কাজ করে, তবে **নতুন করে লেখা কোডে কনটেক্সট read করার জন্য বরং [`useContext()`](/reference/react/useContext) ব্যবহার করা উচিতঃ**

```js
function Button() {
  // ✅ Recommended way
  const theme = useContext(ThemeContext);
  return <button className={theme} />;
}
```

#### Props {/*consumer-props*/}

* `children`: A function. React will call the function you pass with the current context value determined by the same algorithm as [`useContext()`](/reference/react/useContext) does, and render the result you return from this function. React will also re-run this function and update the UI whenever the context from the parent components changes.

---

## Usage {/*usage*/}

### Creating context {/*creating-context*/}

Context lets components [pass information deep down](/learn/passing-data-deeply-with-context) without explicitly passing props.

Call `createContext` outside any components to create one or more contexts.

```js [[1, 3, "ThemeContext"], [1, 4, "AuthContext"], [3, 3, "'light'"], [3, 4, "null"]]
import { createContext } from 'react';

const ThemeContext = createContext('light');
const AuthContext = createContext(null);
```

`createContext` returns a <CodeStep step={1}>context object</CodeStep>. Components can read context by passing it to [`useContext()`](/reference/react/useContext):

```js [[1, 2, "ThemeContext"], [1, 7, "AuthContext"]]
function Button() {
  const theme = useContext(ThemeContext);
  // ...
}

function Profile() {
  const currentUser = useContext(AuthContext);
  // ...
}
```

By default, the values they receive will be the <CodeStep step={3}>default values</CodeStep> you have specified when creating the contexts. However, by itself this isn't useful because the default values never change.

Context is useful because you can **provide other, dynamic values from your components:**

```js {8-9,11-12}
function App() {
  const [theme, setTheme] = useState('dark');
  const [currentUser, setCurrentUser] = useState({ name: 'Taylor' });

  // ...

  return (
    <ThemeContext.Provider value={theme}>
      <AuthContext.Provider value={currentUser}>
        <Page />
      </AuthContext.Provider>
    </ThemeContext.Provider>
  );
}
```

Now the `Page` component and any components inside it, no matter how deep, will "see" the passed context values. If the passed context values change, React will re-render the components reading the context as well.

[Read more about reading and providing context and see examples.](/reference/react/useContext)

---

### একটি ফাইল থেকে কনটেক্সট ইমপোর্ট এবং এক্সপোর্ট {/*importing-and-exporting-context-from-a-file*/}

প্রায় সময়ই, ভিন্ন ভিন্ন ফাইলের component এর একই কনটেক্সট এক্সেস করার প্রয়োজন হবে। এ কারণে, সাধারণত, একটা আলাদা ফাইলে কনটেক্সট ডিক্লেয়ার করা হয়। তখন আপনি অন্যান্য ফাইলের জন্য কনতেক্সট এভেইলেবল করতে [`export` statement](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export) ব্যবহার করতে পারেনঃ

```js {4-5}
// Contexts.js
import { createContext } from 'react';

export const ThemeContext = createContext('light');
export const AuthContext = createContext(null);
```

এর পর অন্যান্য ফাইলে ডিক্লেয়ার হওয়া কম্পোনেন্টগুলো এই কনটেক্সট রিড বা প্রোভাইড করার জন্য [`import`](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/import) statement ব্যবহার করতে পারেঃ

```js {2}
// Button.js
import { ThemeContext } from './Contexts.js';

function Button() {
  const theme = useContext(ThemeContext);
  // ...
}
```

```js {2}
// App.js
import { ThemeContext, AuthContext } from './Contexts.js';

function App() {
  // ...
  return (
    <ThemeContext.Provider value={theme}>
      <AuthContext.Provider value={currentUser}>
        <Page />
      </AuthContext.Provider>
    </ThemeContext.Provider>
  );
}
```

এটা [components ইমপোর্ট এবং এক্সপোর্টের](/learn/importing-and-exporting-components) মত কাজ করে।

---

## ট্রাবলশ্যুট {/*troubleshooting*/}

### আমি কনটেক্সট ভ্যালু পরিবর্তন করার কোন উপায় খুঁজে পাচ্ছি না {/*i-cant-find-a-way-to-change-the-context-value*/}


এমন কোড *default* কনটেক্সট ভ্যালু নির্দেশ করেঃ

```js
const ThemeContext = createContext('light');
```

এই ভ্যালু কখনো পরিবর্তিত হয় না। যদি React উপড়ে কোন matching provider না পায় তখন এই ভ্যালুটা কেবল মাত্র fallback হিসেবে ব্যবহার করে।

সময়ের সাথে সাথে কনটেক্সট চেঞ্জ করতে, [state যোগ করুন এবং কনটেক্সট প্রোভাইডারের মধ্যে components wrap করুন।](/reference/react/useContext#updating-data-passed-via-context)

