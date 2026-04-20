---
title: createContext
---

<Intro>

`createContext` আপনাকে একটি [context](/learn/passing-data-deeply-with-context) তৈরি করতে দেয় যা component provide বা read করতে পারে।

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

* `defaultValue`: এটা হল সেই ভ্যালু যেটা আপনি চান যে কনটেক্সটের থাকুক। কখন? যখন সেই কনটেক্সট যেই component read করবে, ট্রিতে তার উপরে কোন ম্যাচিং কনটেক্সট প্রোভাইডার নেই। যদি আপনার কোন অর্থবহ default ভ্যালু না থাকে, ভ্যালুটা `null` করে দেন। ডিফল্ট ভ্যালু একদম "last resort" ফলব্যাক হিসেবে রাখা হয়। এটা স্ট্যাটিক এবং সময়ের সাথে অপরিবর্তিত থাকে।

#### রিটার্ন {/*returns*/}

`createContext` একটি কনটেক্সট অবজেক্ট রিটার্ন করে। 

**কনটেক্সট অবজেক্ট নিজে কোন তথ্য ধারণ করে না।** এটা _সেই_ কনটেক্সটকে রিপ্রেজেন্ট করে যেটা অন্যান্য কম্পোনেন্টগুলো read বা provide করে। সাধারণত, কনটেক্সট ভ্যালু নির্দিষ্ট করতে আপনি উপরের কম্পোনেন্টে [`SomeContext`](#provider) ব্যবহার করবেন, এবং এটাকে read করতে নিচের কম্পোনেন্টগুলোতে [`useContext(SomeContext)`](/reference/react/useContext) কল করবেন। কনটেক্সট অবজেক্টের কিছু প্রপার্টি আছেঃ

* `SomeContext` আপনাকে কম্পোনেন্টে কনটেক্সট ভ্যালু provide করার সুযোগ দেয়।
* `SomeContext.Consumer` একটি উপায়ান্তর যা কনটেক্সট ভ্যালু read করার জন্য বিরল ভাবে ব্যবহৃত হয়।
* `SomeContext.Provider` React 19 এর আগে কনটেক্সট ভ্যালু provide করার legacy উপায়।

---

### `SomeContext` Provider {/*provider*/}

আপনার কম্পোনেন্টগুলোকে একটি কনটেক্সট প্রোভাইডারে wrap করে ফেলুন এবং এর মাধ্যমে এই সকল কম্পোনেন্টের জন্য এই কন্টেক্সটের মান নির্দিষ্ট করে দিনঃ

```js
function App() {
  const [theme, setTheme] = useState('light');
  // ...
  return (
    <ThemeContext value={theme}>
      <Page />
    </ThemeContext>
  );
}
```

<Note>

<<<<<<< HEAD
React 19 থেকে শুরু করে, আপনি `<SomeContext>` কে একটি provider হিসেবে render করতে পারেন। 
=======
Starting in React 19, you can render `<SomeContext>` as a provider.
>>>>>>> abe931a8cb3aee3e8b15ef7e187214789164162a

React এর পুরনো ভার্সনে, `<SomeContext.Provider>` ব্যবহার করুন।

</Note>

#### Props {/*provider-props*/}

* `value`: এটা হচ্ছে সেই ভ্যালু যেটা আপনি এই প্রোভাইডারের সেই সকল কম্পোনেন্ট দিয়ে read করাতে চান, সেটা যত গভীরেই হোক না কেন। কনটেক্সট ভ্যালু যেকোন টাইপের হতে পারে। যখন প্রোভাইডারের মধ্যে থাকা একটি কম্পোনেন্ট [`useContext(SomeContext)`](/reference/react/useContext) কল করে, তখন এটা তার উপরে সবচেয়ে ভিতরকার কনটেক্সট প্রোভাইডারের `value` রিসিভ করে।

---

### `SomeContext.Consumer` {/*consumer*/}

`useContext` আসার আগে, কনটেক্সট read করার জন্য একটি পুরনো উপায় ছিলঃ

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

যদিও এই পুরনো উপায়টি এখনো কাজ করে, তবে **নতুন করে লেখা কোডে কনটেক্সট read করার জন্য বরং [`useContext()`](/reference/react/useContext) ব্যবহার করা উচিতঃ**

```js
function Button() {
  // ✅ Recommended way
  const theme = useContext(ThemeContext);
  return <button className={theme} />;
}
```

#### Props {/*consumer-props*/}

* `children`: একটি ফাংশন। React আপনার pass করা ফাংশনকে কল করবে বর্তমান কনটেক্সট ভ্যালু দিয়ে যেটা [`useContext()`](/reference/react/useContext) এর মতই এলগোরিদম দিয়ে নির্ণয়কৃত, এবং এই ফাংশন থেকে আপনার রিটার্ন করা ফলাফল রেন্ডার করবে। React এই ফাংশন আবার রান করবে এবং প্যারেন্ট কম্পোনেন্টের কনটেক্সট বদলালেই UI আপডেট করে ফেলবে।

---

## ব্যবহার {/*usage*/}

### কনটেক্সট তৈরি {/*creating-context*/}

কনটেক্সট কম্পোনেটদেরকে [বেশ গভীরে তথ্য পাঠানোর সুযোগ দেয়](/learn/passing-data-deeply-with-context) explicitly props পাঠানো ছাড়াই।

যেকোন কম্পোনেন্টের বাইরে `createContext` কল করুন এক বা একাধিক কনটেক্সট তৈরি করার জন্য।

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

ডিফল্টভাবে, যেই ভ্যালুগুলো তারা পাবে সেগুলো হবে <CodeStep step={3}>default values</CodeStep> যা আপনি কনটেক্সট তৈরি করার সময় নির্দিষ্ট করে দিয়েছিলেন। তবে, এটা নিজে নিজে খুব একটা কাজের না কারণ ডিফল্ট ভ্যালু কখনো বদলায় না।

Context কাজের কেননা আপনি **আপনার কম্পোনেন্ট গুলো থেকে অন্যান্য, dynamic মান provide করতে পারবেনঃ**

```js {8-9,11-12}
function App() {
  const [theme, setTheme] = useState('dark');
  const [currentUser, setCurrentUser] = useState({ name: 'Taylor' });

  // ...

  return (
    <ThemeContext value={theme}>
      <AuthContext value={currentUser}>
        <Page />
      </AuthContext>
    </ThemeContext>
  );
}
```

এখন `Page` কম্পোনেন্ট এবং এর মধ্যে থাকা যেকোন কম্পোনেন্ট, সেটা যত গভীরেই হোক না কেন, pass হওয়া context ভ্যালু "দেখতে" পারবে। যদি passed হওয়া কনটেক্সট এর ভ্যালু বদলায়, React কনটেক্সট read করে কম্পোনেন্ট পুনরায় render করবে।

[কনটেক্সট reading এবং providing এর বিষয়ে আরো পড়ুন এবং উদাহরণ দেখুন।](/reference/react/useContext)

---

### একটি ফাইল থেকে কনটেক্সট ইমপোর্ট এবং এক্সপোর্ট {/*importing-and-exporting-context-from-a-file*/}

প্রায় সময়ই, ভিন্ন ভিন্ন ফাইলের কম্পোনেন্ট এর একই কনটেক্সট এক্সেস করার প্রয়োজন হবে। এ কারণে, সাধারণত, একটা আলাদা ফাইলে কনটেক্সট ডিক্লেয়ার করা হয়। তখন আপনি অন্যান্য ফাইলের জন্য কনতেক্সট এভেইলেবল করতে [`export` statement](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export) ব্যবহার করতে পারেনঃ

```js {4-5}
// Contexts.js
import { createContext } from 'react';

export const ThemeContext = createContext('light');
export const AuthContext = createContext(null);
```

এর পর অন্যান্য ফাইলে ডিক্লেয়ার হওয়া কম্পোনেন্টগুলো এই কনটেক্সট রিড বা প্রোভাইড করার জন্য [`import`](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/import) statement ব্যবহার করতে পারেঃ

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
    <ThemeContext value={theme}>
      <AuthContext value={currentUser}>
        <Page />
      </AuthContext>
    </ThemeContext>
  );
}
```

এটা [components ইমপোর্ট এবং এক্সপোর্টের](/learn/importing-and-exporting-components) মত কাজ করে।

---

## ট্রাবলশ্যুট {/*troubleshooting*/}

### আমি কনটেক্সট ভ্যালু পরিবর্তন করার কোন উপায় খুঁজে পাচ্ছি না {/*i-cant-find-a-way-to-change-the-context-value*/}


এমন কোড *default* কনটেক্সট ভ্যালু নির্দেশ করেঃ

```js
const ThemeContext = createContext('light');
```

এই ভ্যালু কখনো পরিবর্তিত হয় না। যদি React উপড়ে কোন matching provider না পায় তখন এই ভ্যালুটা কেবল মাত্র fallback হিসেবে ব্যবহার করে।

সময়ের সাথে সাথে কনটেক্সট চেঞ্জ করতে, [state যোগ করুন এবং কনটেক্সট প্রোভাইডারের মধ্যে components wrap করুন।](/reference/react/useContext#updating-data-passed-via-context)
