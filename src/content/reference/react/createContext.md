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

* `defaultValue`: The value that you want the context to have when there is no matching context provider in the tree above the component that reads context. If you don't have any meaningful default value, specify `null`. The default value is meant as a "last resort" fallback. It is static and never changes over time.

#### Returns {/*returns*/}

`createContext` একটি কনটেক্সট অবজেক্ট রিটার্ন করে। 

**The context object itself does not hold any information.** It represents _which_ context other components read or provide. Typically, you will use [`SomeContext.Provider`](#provider) in components above to specify the context value, and call [`useContext(SomeContext)`](/reference/react/useContext) in components below to read it. The context object has a few properties:

* `SomeContext.Provider` lets you provide the context value to components.
* `SomeContext.Consumer` is an alternative and rarely used way to read the context value.

---

### `SomeContext.Provider` {/*provider*/}

Wrap your components into a context provider to specify the value of this context for all components inside:

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

* `value`: The value that you want to pass to all the components reading this context inside this provider, no matter how deep. The context value can be of any type. A component calling [`useContext(SomeContext)`](/reference/react/useContext) inside of the provider receives the `value` of the innermost corresponding context provider above it.

---

### `SomeContext.Consumer` {/*consumer*/}

Before `useContext` existed, there was an older way to read context:

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

Although this older way still works, but **newly written code should read context with [`useContext()`](/reference/react/useContext) instead:**

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

### Importing and exporting context from a file {/*importing-and-exporting-context-from-a-file*/}

Often, components in different files will need access to the same context. This is why it's common to declare contexts in a separate file. Then you can use the [`export` statement](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export) to make context available for other files:

```js {4-5}
// Contexts.js
import { createContext } from 'react';

export const ThemeContext = createContext('light');
export const AuthContext = createContext(null);
```

Components declared in other files can then use the [`import`](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/import) statement to read or provide this context:

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

