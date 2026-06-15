---
title: useContext
---

<Intro>

`useContext` হচ্ছে একটি React হুক যেটা আপনাকে কম্পোনেন্ট থেকে [context](/learn/passing-data-deeply-with-context) পড়তে এবং সাবস্কাইব করতে দিবে।

```js
const value = useContext(SomeContext)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `useContext(SomeContext)` {/*usecontext*/}

[context](/learn/passing-data-deeply-with-context) রিড করা এবং সেখানে সাবস্ক্রাইব করার জন্য `useContext` কে আপনার কম্পোনেন্ট এর একেবারে উপরের স্তরে কল করতে হবে।

```js
import { useContext } from 'react';

function MyComponent() {
  const theme = useContext(ThemeContext);
  // ...
```

[নীচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটারস {/*parameters*/}

* `SomeContext`: এটি হচ্ছে সেই context যেটি আপনি আগে [`createContext`](/reference/react/createContext) ব্যবহার করে তৈরি করেছিলেন। context নিজে থেকে তথ্য ধারণ করে না, এটি শুধুমাত্র সেই তথ্যগুলিকেই উপস্থাপন করে যা আপনি দিতে পারেন অথবা কম্পোনেন্ট থেকে রিড করতে পারেন।

#### রিটার্নস {/*returns*/}

`useContext` যে কম্পোনেন্টে কল করা হয় তার জন্য context এর মান রিটার্ন করে। এটি কম্পোনেন্ট ট্রিতে কলিং কম্পোনেন্টের উপরে সবচেয়ে কাছের `SomeContext` এর কাছে পাঠানো `value` দ্বারা নির্ধারিত হয়। যদি এমন কোন provider না থাকে, তাহলে রিটার্ন করা মান হবে `defaultValue` যা আপনি সেই context এর জন্য [`createContext`](/reference/react/createContext) এ পাঠিয়েছিলেন। রিটার্ন করা মান সবসময় আপ-টু-ডেট থাকে। যদি context পরিবর্তিত হয়, তাহলে React স্বয়ংক্রিয়ভাবে সেই কম্পোনেন্টগুলোকে পুনরায় রেন্ডার করে যারা কোন context পড়ে।

#### সতর্কতা {/*caveats*/}

* একটি কম্পোনেন্টের ভিতর `useContext()` কল করা হলে সেই *একই* কম্পোনেন্ট থেকে রিটার্ন করা provider দিয়ে এটি প্রভাবিত হয় না। যে কম্পোনেন্ট থেকে `useContext()` কল করা হয়েছে, সংশ্লিষ্ট `<Context>` কে সেই কম্পোনেন্টের ***উপরে* থাকতে হবে**।
* React **স্বয়ংক্রিয়ভাবে পুনরায় রেন্ডার** করে সেইসব চিলড্রেনকে যারা একটি নির্দিষ্ট context ব্যবহার করে, শুরু করে সেই provider থেকে যা ভিন্ন `value` পায়। আগের এবং পরের মান [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) এর মাধ্যমে তুলনা করা হয়। [`memo`](/reference/react/memo) দিয়ে পুনরায় রেন্ডার এড়ানো চিলড্রেনদের নতুন context এর মান পেতে বাধা দেয় না।
* যদি আপনার বিল্ড সিস্টেম আউটপুটে ডুপ্লিকেট মডিউল উৎপাদন করে (যা symlinks দ্বারা হতে পারে), তাহলে সেটা আপনার context কে ভাঙতে পারে। কোন কিছু context এর মাধ্যমে পাঠানো শুধুমাত্র তখনই কাজ করে যখন আপনার context প্রদান করার জন্য ব্যবহারকৃত `SomeContext` এবং রিড করার জন্য ব্যবহারকৃত `SomeContext` ***হুবহু* একই object** হয়, যা `===` তুলনা দ্বারা নির্ধারিত হয়।

---

## ব্যবহারবিধি {/*usage*/}


### ট্রির গভীরে ডাটা পাস করা {/*passing-data-deeply-into-the-tree*/}

[context](/learn/passing-data-deeply-with-context) রিড করা এবং সেখানে সাবস্ক্রাইব করার জন্য `useContext` কে আপনার কম্পোনেন্ট এর একেবারে উপরের স্তরে কল করতে হবে।

```js [[2, 4, "theme"], [1, 4, "ThemeContext"]]
import { useContext } from 'react';

function Button() {
  const theme = useContext(ThemeContext);
  // ...
```

`useContext` আপনার পাস করা <CodeStep step={1}>context</CodeStep> এর জন্য <CodeStep step={2}>context এর মান</CodeStep> রিটার্ন করে। context এর মান নির্ধারণ করার জন্য, React কম্পোনেন্ট ট্রিতে সার্চ করে এবং এই নির্দিষ্ট context এর জন্য **উপরের দিকে সব থেকে কাছের context provider** কে খুঁজে বের করে।

একটি `Button` এ context পাস করতে, এটিকে বা এর প্যারেন্ট কম্পোনেন্টগুলির একটিকে সংশ্লিষ্ট context provider দিয়ে wrap করতে হবেঃ

```js [[1, 3, "ThemeContext"], [2, 3, "\\"dark\\""], [1, 5, "ThemeContext"]]
function MyPage() {
  return (
    <ThemeContext value="dark">
      <Form />
    </ThemeContext>
  );
}

function Form() {
  // ... renders buttons inside ...
}
```

Provider এবং `Button` এর মধ্যে কম্পোনেন্টগুলির কতগুলি স্তর রয়েছে তা বিবেচ্য নয়। যখন `Form` এর ভিতরে যেকোনো জায়গায় একটি `Button` `useContext(ThemeContext)` কল করে, তখন এটি মান হিসাবে `"dark"` পাবে।

<Pitfall>

`useContext()` সবসময় একে কল করে এমন কম্পোনেন্টের *উপরের দিকে* নিকটতম provider কে খুঁজে। এটি উপরের দিকে সার্চ করে এবং আপনি যে কম্পোনেন্ট থেকে `useContext()` কল করছেন সেখানকার provider গুলোকে বিবেচনা **করা হয় না**।

</Pitfall>

<Sandpack>

```js
import { createContext, useContext } from 'react';

const ThemeContext = createContext(null);

export default function MyApp() {
  return (
    <ThemeContext value="dark">
      <Form />
    </ThemeContext>
  )
}

function Form() {
  return (
    <Panel title="Welcome">
      <Button>Sign up</Button>
      <Button>Log in</Button>
    </Panel>
  );
}

function Panel({ title, children }) {
  const theme = useContext(ThemeContext);
  const className = 'panel-' + theme;
  return (
    <section className={className}>
      <h1>{title}</h1>
      {children}
    </section>
  )
}

function Button({ children }) {
  const theme = useContext(ThemeContext);
  const className = 'button-' + theme;
  return (
    <button className={className}>
      {children}
    </button>
  );
}
```

```css
.panel-light,
.panel-dark {
  border: 1px solid black;
  border-radius: 4px;
  padding: 20px;
}
.panel-light {
  color: #222;
  background: #fff;
}

.panel-dark {
  color: #fff;
  background: rgb(23, 32, 42);
}

.button-light,
.button-dark {
  border: 1px solid #777;
  padding: 5px;
  margin-right: 10px;
  margin-top: 10px;
}

.button-dark {
  background: #222;
  color: #fff;
}

.button-light {
  background: #fff;
  color: #222;
}
```

</Sandpack>

---

### context পাস করার মাধ্যমে ডাটা আপডেট করা {/*updating-data-passed-via-context*/}

মাঝে মাঝেই আপনি সময়ের সাথে সাথে context পরিবর্তন করতে চাইবেন। context আপডেট করতে, এটিকে [state](/reference/react/useState) এর সাথে একত্রে ব্যবহার করতে হবে। প্যারেন্ট কম্পোনেন্টে একটি state ভ্যারিয়েবল ডিক্লেয়ার করতে হবে এবং provider এ <CodeStep step={2}>context এর মান</CodeStep> হিসেবে বর্তমান state কে পাস করে দিতে হবে।

```js {2} [[1, 4, "ThemeContext"], [2, 4, "theme"], [1, 11, "ThemeContext"]]
function MyPage() {
  const [theme, setTheme] = useState('dark');
  return (
    <ThemeContext value={theme}>
      <Form />
      <Button onClick={() => {
        setTheme('light');
      }}>
        Switch to light theme
      </Button>
    </ThemeContext>
  );
}
```

এখন provider এর ভিতরে যেকোনো `Button` বর্তমান `theme` এর মান পাবে। আপনি provider এর কাছে যে `theme` এর মানটি পাস করেছেন সেটি আপডেট করতে আপনি `setTheme` কে কল করলে, সব `Button` কম্পোনেন্ট নতুন `'light'` মান এর জন্য পুনরায় রেন্ডার হবে।

<Recipes titleText="context আপডেট করার উদাহরণ" titleId="examples-basic">

#### context এর মাধ্যমে মান আপডেট করা {/*updating-a-value-via-context*/}

এই উদাহরণে, `MyApp` কম্পোনেন্ট একটি state ভ্যারিয়েবল ধারণ করে যা পরবর্তীতে `ThemeContext` provider এ পাস করা হয়। "Dark mode" চেকবক্স চেক করলে state আপডেট হয়। প্রদত্ত মানের পরিবর্তন সেই সব কম্পোনেন্টকে পুনরায় রেন্ডার করে যারা এই context ব্যবহার করেছে।

<Sandpack>

```js
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext(null);

export default function MyApp() {
  const [theme, setTheme] = useState('light');
  return (
    <ThemeContext value={theme}>
      <Form />
      <label>
        <input
          type="checkbox"
          checked={theme === 'dark'}
          onChange={(e) => {
            setTheme(e.target.checked ? 'dark' : 'light')
          }}
        />
        Use dark mode
      </label>
    </ThemeContext>
  )
}

function Form({ children }) {
  return (
    <Panel title="Welcome">
      <Button>Sign up</Button>
      <Button>Log in</Button>
    </Panel>
  );
}

function Panel({ title, children }) {
  const theme = useContext(ThemeContext);
  const className = 'panel-' + theme;
  return (
    <section className={className}>
      <h1>{title}</h1>
      {children}
    </section>
  )
}

function Button({ children }) {
  const theme = useContext(ThemeContext);
  const className = 'button-' + theme;
  return (
    <button className={className}>
      {children}
    </button>
  );
}
```

```css
.panel-light,
.panel-dark {
  border: 1px solid black;
  border-radius: 4px;
  padding: 20px;
  margin-bottom: 10px;
}
.panel-light {
  color: #222;
  background: #fff;
}

.panel-dark {
  color: #fff;
  background: rgb(23, 32, 42);
}

.button-light,
.button-dark {
  border: 1px solid #777;
  padding: 5px;
  margin-right: 10px;
  margin-top: 10px;
}

.button-dark {
  background: #222;
  color: #fff;
}

.button-light {
  background: #fff;
  color: #222;
}
```

</Sandpack>

উল্লেখ্য যে `value="dark"`, `"dark"` কে string হিসাবে পাস করা হচ্ছে, কিন্তু `value={theme}` জাভাস্ক্রিপ্টের `theme` ভ্যারিয়েবলের মান [JSX curly braces](/learn/javascript-in-jsx-with-curly-braces) দিয়ে পাস করা হচ্ছে। স্ট্রিং নয় এমন context এর মানগুলিও curly braces পাস করতে দেয়।

<Solution />

#### context এর মাধ্যমে object আপডেট করা {/*updating-an-object-via-context*/}

এই উদাহরণে, একটি `currentUser` state ভ্যারিয়েবল রয়েছে যা একটি object কে ধারণ করে। আপনি একটি object এর মধ্যে `{ currentUser, setCurrentUser }` কে একত্রিত করুন এবং context এর ভিতর `value={}` এর মাধ্যমে পাস করে দিন। এটি নীচের যেকোন কম্পোনেন্ট যেমন ধরুন `LoginButton` কে, `currentUser` এবং `setCurrentUser` উভয়ই রিড করতে দেয়, এবং যখন প্রয়োজন `setCurrentUser` কে কল করতে দেয়।

<Sandpack>

```js
import { createContext, useContext, useState } from 'react';

const CurrentUserContext = createContext(null);

export default function MyApp() {
  const [currentUser, setCurrentUser] = useState(null);
  return (
    <CurrentUserContext
      value={{
        currentUser,
        setCurrentUser
      }}
    >
      <Form />
    </CurrentUserContext>
  );
}

function Form({ children }) {
  return (
    <Panel title="Welcome">
      <LoginButton />
    </Panel>
  );
}

function LoginButton() {
  const {
    currentUser,
    setCurrentUser
  } = useContext(CurrentUserContext);

  if (currentUser !== null) {
    return <p>You logged in as {currentUser.name}.</p>;
  }

  return (
    <Button onClick={() => {
      setCurrentUser({ name: 'Advika' })
    }}>Log in as Advika</Button>
  );
}

function Panel({ title, children }) {
  return (
    <section className="panel">
      <h1>{title}</h1>
      {children}
    </section>
  )
}

function Button({ children, onClick }) {
  return (
    <button className="button" onClick={onClick}>
      {children}
    </button>
  );
}
```

```css
label {
  display: block;
}

.panel {
  border: 1px solid black;
  border-radius: 4px;
  padding: 20px;
  margin-bottom: 10px;
}

.button {
  border: 1px solid #777;
  padding: 5px;
  margin-right: 10px;
  margin-top: 10px;
}
```

</Sandpack>

<Solution />

#### একাধিক context {/*multiple-contexts*/}

এই উদাহরণে, দুটি স্বাধীন context আছে। `ThemeContext` বর্তমান থিম প্রদান করে, যা একটি string, যখন `CurrentUserContext` বর্তমান ব্যবহারকারীর প্রতিনিধিত্ব করে এমন object কে ধারণ করে।

<Sandpack>

```js
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext(null);
const CurrentUserContext = createContext(null);

export default function MyApp() {
  const [theme, setTheme] = useState('light');
  const [currentUser, setCurrentUser] = useState(null);
  return (
    <ThemeContext value={theme}>
      <CurrentUserContext
        value={{
          currentUser,
          setCurrentUser
        }}
      >
        <WelcomePanel />
        <label>
          <input
            type="checkbox"
            checked={theme === 'dark'}
            onChange={(e) => {
              setTheme(e.target.checked ? 'dark' : 'light')
            }}
          />
          Use dark mode
        </label>
      </CurrentUserContext>
    </ThemeContext>
  )
}

function WelcomePanel({ children }) {
  const {currentUser} = useContext(CurrentUserContext);
  return (
    <Panel title="Welcome">
      {currentUser !== null ?
        <Greeting /> :
        <LoginForm />
      }
    </Panel>
  );
}

function Greeting() {
  const {currentUser} = useContext(CurrentUserContext);
  return (
    <p>You logged in as {currentUser.name}.</p>
  )
}

function LoginForm() {
  const {setCurrentUser} = useContext(CurrentUserContext);
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  const canLogin = firstName.trim() !== '' && lastName.trim() !== '';
  return (
    <>
      <label>
        First name{': '}
        <input
          required
          value={firstName}
          onChange={e => setFirstName(e.target.value)}
        />
      </label>
      <label>
        Last name{': '}
        <input
        required
          value={lastName}
          onChange={e => setLastName(e.target.value)}
        />
      </label>
      <Button
        disabled={!canLogin}
        onClick={() => {
          setCurrentUser({
            name: firstName + ' ' + lastName
          });
        }}
      >
        Log in
      </Button>
      {!canLogin && <i>Fill in both fields.</i>}
    </>
  );
}

function Panel({ title, children }) {
  const theme = useContext(ThemeContext);
  const className = 'panel-' + theme;
  return (
    <section className={className}>
      <h1>{title}</h1>
      {children}
    </section>
  )
}

function Button({ children, disabled, onClick }) {
  const theme = useContext(ThemeContext);
  const className = 'button-' + theme;
  return (
    <button
      className={className}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
}
```

```css
label {
  display: block;
}

.panel-light,
.panel-dark {
  border: 1px solid black;
  border-radius: 4px;
  padding: 20px;
  margin-bottom: 10px;
}
.panel-light {
  color: #222;
  background: #fff;
}

.panel-dark {
  color: #fff;
  background: rgb(23, 32, 42);
}

.button-light,
.button-dark {
  border: 1px solid #777;
  padding: 5px;
  margin-right: 10px;
  margin-top: 10px;
}

.button-dark {
  background: #222;
  color: #fff;
}

.button-light {
  background: #fff;
  color: #222;
}
```

</Sandpack>

<Solution />

#### কম্পোনেন্ট থেকে provider কে পৃথক করা {/*extracting-providers-to-a-component*/}

আপনার অ্যাপ বড় হবার সাথে সাথে আপনার অ্যাপের রুটের কাছাকাছি context গুলোর একটি "পিরামিড" থাকবে বলে আশা করা যাচ্ছে। এটাতে কোন সমস্যা নেই। যাই হোক, আপনি যদি নান্দনিকভাবে নেস্টিং অপছন্দ করেন, তাহলে আপনি একটি কম্পোনেন্টে provider গুলোকে পৃথক করে ফেলতে পারেন। এই উদাহরণে, `MyProviders` "plumbing" লুকিয়ে রাখে এবং এর ভিতর দিয়ে প্রয়োজনীয় provider দের জন্য পাস করা চিলড্রেন রেন্ডার করে। উল্লেখ্য যে `MyApp` এ `theme` এবং `setTheme` state প্রয়োজন, তাই `MyApp` এখনও state এর এই অংশটুকুর মালিক।

<Sandpack>

```js
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext(null);
const CurrentUserContext = createContext(null);

export default function MyApp() {
  const [theme, setTheme] = useState('light');
  return (
    <MyProviders theme={theme} setTheme={setTheme}>
      <WelcomePanel />
      <label>
        <input
          type="checkbox"
          checked={theme === 'dark'}
          onChange={(e) => {
            setTheme(e.target.checked ? 'dark' : 'light')
          }}
        />
        Use dark mode
      </label>
    </MyProviders>
  );
}

function MyProviders({ children, theme, setTheme }) {
  const [currentUser, setCurrentUser] = useState(null);
  return (
    <ThemeContext value={theme}>
      <CurrentUserContext
        value={{
          currentUser,
          setCurrentUser
        }}
      >
        {children}
      </CurrentUserContext>
    </ThemeContext>
  );
}

function WelcomePanel({ children }) {
  const {currentUser} = useContext(CurrentUserContext);
  return (
    <Panel title="Welcome">
      {currentUser !== null ?
        <Greeting /> :
        <LoginForm />
      }
    </Panel>
  );
}

function Greeting() {
  const {currentUser} = useContext(CurrentUserContext);
  return (
    <p>You logged in as {currentUser.name}.</p>
  )
}

function LoginForm() {
  const {setCurrentUser} = useContext(CurrentUserContext);
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  const canLogin = firstName !== '' && lastName !== '';
  return (
    <>
      <label>
        First name{': '}
        <input
          required
          value={firstName}
          onChange={e => setFirstName(e.target.value)}
        />
      </label>
      <label>
        Last name{': '}
        <input
        required
          value={lastName}
          onChange={e => setLastName(e.target.value)}
        />
      </label>
      <Button
        disabled={!canLogin}
        onClick={() => {
          setCurrentUser({
            name: firstName + ' ' + lastName
          });
        }}
      >
        Log in
      </Button>
      {!canLogin && <i>Fill in both fields.</i>}
    </>
  );
}

function Panel({ title, children }) {
  const theme = useContext(ThemeContext);
  const className = 'panel-' + theme;
  return (
    <section className={className}>
      <h1>{title}</h1>
      {children}
    </section>
  )
}

function Button({ children, disabled, onClick }) {
  const theme = useContext(ThemeContext);
  const className = 'button-' + theme;
  return (
    <button
      className={className}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
}
```

```css
label {
  display: block;
}

.panel-light,
.panel-dark {
  border: 1px solid black;
  border-radius: 4px;
  padding: 20px;
  margin-bottom: 10px;
}
.panel-light {
  color: #222;
  background: #fff;
}

.panel-dark {
  color: #fff;
  background: rgb(23, 32, 42);
}

.button-light,
.button-dark {
  border: 1px solid #777;
  padding: 5px;
  margin-right: 10px;
  margin-top: 10px;
}

.button-dark {
  background: #222;
  color: #fff;
}

.button-light {
  background: #fff;
  color: #222;
}
```

</Sandpack>

<Solution />

#### context এবং reducer এর মাধ্যমে স্কেল করা {/*scaling-up-with-context-and-a-reducer*/}

বড় অ্যাপ্লিকেশানগুলিতে, context এর সাথে একটি [reducer](/reference/react/useReducer) কে একত্রিত করে কম্পোনেন্টগুলোর বাইরের কিছু লজিক সম্পর্কিত state কে পৃথক করা খুবই সাধারণ। এই উদাহরণে, সমস্ত "ওয়্যারিং" `TasksContext.js` এ লুকানো আছে, যেটিতে একটি reducer এবং দুটি পৃথক context রয়েছে।

এই উদাহরণের [সম্পূর্ণ ওয়াকথ্রু](/learn/scaling-up-with-reducer-and-context) পড়ুন।

<Sandpack>

```js src/App.js
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { TasksProvider } from './TasksContext.js';

export default function TaskApp() {
  return (
    <TasksProvider>
      <h1>Day off in Kyoto</h1>
      <AddTask />
      <TaskList />
    </TasksProvider>
  );
}
```

```js src/TasksContext.js
import { createContext, useContext, useReducer } from 'react';

const TasksContext = createContext(null);

const TasksDispatchContext = createContext(null);

export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  return (
    <TasksContext value={tasks}>
      <TasksDispatchContext value={dispatch}>
        {children}
      </TasksDispatchContext>
    </TasksContext>
  );
}

export function useTasks() {
  return useContext(TasksContext);
}

export function useTasksDispatch() {
  return useContext(TasksDispatchContext);
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

const initialTasks = [
  { id: 0, text: 'Philosopher's Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js src/AddTask.js
import { useState, useContext } from 'react';
import { useTasksDispatch } from './TasksContext.js';

export default function AddTask() {
  const [text, setText] = useState('');
  const dispatch = useTasksDispatch();
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        dispatch({
          type: 'added',
          id: nextId++,
          text: text,
        });
      }}>Add</button>
    </>
  );
}

let nextId = 3;
```

```js src/TaskList.js
import { useState, useContext } from 'react';
import { useTasks, useTasksDispatch } from './TasksContext.js';

export default function TaskList() {
  const tasks = useTasks();
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task task={task} />
        </li>
      ))}
    </ul>
  );
}

function Task({ task }) {
  const [isEditing, setIsEditing] = useState(false);
  const dispatch = useTasksDispatch();
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            dispatch({
              type: 'changed',
              task: {
                ...task,
                text: e.target.value
              }
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={e => {
          dispatch({
            type: 'changed',
            task: {
              ...task,
              done: e.target.checked
            }
          });
        }}
      />
      {taskContent}
      <button onClick={() => {
        dispatch({
          type: 'deleted',
          id: task.id
        });
      }}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
```

</Sandpack>

<Solution />

</Recipes>

---

### ফলব্যাক এর ক্ষেত্রে ডিফল্ট মান নির্ধারণ করা {/*specifying-a-fallback-default-value*/}

React যদি প্যারেন্ট ট্রিতে নির্দিষ্ট <CodeStep step={1}>context</CodeStep> এর কোন provider খুঁজে না পায়, তাহলে `useContext()` থেকে রিটার্ন্ড context এর মান <CodeStep step={3}>ডিফল্ট মানের</CodeStep> সমান হবে যা আপনি সেই [context টি তৈরি](/reference/react/createContext) করার সময় নির্ধারণ করেছিলেনঃ

```js [[1, 1, "ThemeContext"], [3, 1, "null"]]
const ThemeContext = createContext(null);
```

ডিফল্ট মান **কখনই পরিবর্তন হয় না**। আপনি যদি context আপডেট করতে চান, তাহলে [উপরে বর্ণিত](#updating-data-passed-via-context) নিয়মে state এর সাথে এটি ব্যবহার করুন।

প্রায়শই, `null` এর পরিবর্তে আপনি ডিফল্ট মান হিসাবে ব্যবহার করতে পারেন এমন অনেক অর্থপূর্ণ মান রয়েছে, উদাহরণ স্বরূপঃ

```js [[1, 1, "ThemeContext"], [3, 1, "light"]]
const ThemeContext = createContext('light');
```

এইভাবে, আপনি যদি দুর্ঘটনাক্রমে সংশ্লিষ্ট provider ছাড়া কোন কম্পোনেন্ট রেন্ডার করেন, তবে এটি ভাংবে করবে না। এটি আপনার কম্পোনেন্টগুলিকে টেস্টের সময় অনেক অনেক provider সেট আপ না করে একটি টেস্ট ইনভায়রনমেন্টে ভালভাবে কাজ করতে সহায়তা করে।

নীচের উদাহরণে, "Toggle theme" বাটনটি সবসময় light কারণ এটি **সবরকম theme context provider এর বাইরে** এবং ডিফল্ট context theme এর মান `'light'`। ডিফল্ট থিমকে `'dark'` করার জন্য এডিট করন।

<Sandpack>

```js
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext('light');

export default function MyApp() {
  const [theme, setTheme] = useState('light');
  return (
    <>
      <ThemeContext value={theme}>
        <Form />
      </ThemeContext>
      <Button onClick={() => {
        setTheme(theme === 'dark' ? 'light' : 'dark');
      }}>
        Toggle theme
      </Button>
    </>
  )
}

function Form({ children }) {
  return (
    <Panel title="Welcome">
      <Button>Sign up</Button>
      <Button>Log in</Button>
    </Panel>
  );
}

function Panel({ title, children }) {
  const theme = useContext(ThemeContext);
  const className = 'panel-' + theme;
  return (
    <section className={className}>
      <h1>{title}</h1>
      {children}
    </section>
  )
}

function Button({ children, onClick }) {
  const theme = useContext(ThemeContext);
  const className = 'button-' + theme;
  return (
    <button className={className} onClick={onClick}>
      {children}
    </button>
  );
}
```

```css
.panel-light,
.panel-dark {
  border: 1px solid black;
  border-radius: 4px;
  padding: 20px;
  margin-bottom: 10px;
}
.panel-light {
  color: #222;
  background: #fff;
}

.panel-dark {
  color: #fff;
  background: rgb(23, 32, 42);
}

.button-light,
.button-dark {
  border: 1px solid #777;
  padding: 5px;
  margin-right: 10px;
  margin-top: 10px;
}

.button-dark {
  background: #222;
  color: #fff;
}

.button-light {
  background: #fff;
  color: #222;
}
```

</Sandpack>

---

### ট্রির একটি অংশের জন্য context ওভাররাইড করা {/*overriding-context-for-a-part-of-the-tree*/}

আপনি ট্রির একটি অংশকে একটি ভিন্ন মান সহ একটি provider দিয়ে wrap করার মাধ্যমে সেই অংশের জন্য context টি ওভাররাইড করতে পারেন।

```js {3,5}
<ThemeContext value="dark">
  ...
  <ThemeContext value="light">
    <Footer />
  </ThemeContext>
  ...
</ThemeContext>
```

আপনি যতবার প্রয়োজন ততবার provider গুলিকে নেস্ট এবং ওভাররাইড করতে পারেন।

<Recipes titleText="Examples of overriding context">

#### একটি থিম ওভাররাইড করা {/*overriding-a-theme*/}

এখানে, `Footer` এর *ভিতরের* বাটনটি বাইরের বাটনের (`"dark"`) না পেয়ে একটি ভিন্ন context মান (`"light"`) পায়।

<Sandpack>

```js
import { createContext, useContext } from 'react';

const ThemeContext = createContext(null);

export default function MyApp() {
  return (
    <ThemeContext value="dark">
      <Form />
    </ThemeContext>
  )
}

function Form() {
  return (
    <Panel title="Welcome">
      <Button>Sign up</Button>
      <Button>Log in</Button>
      <ThemeContext value="light">
        <Footer />
      </ThemeContext>
    </Panel>
  );
}

function Footer() {
  return (
    <footer>
      <Button>Settings</Button>
    </footer>
  );
}

function Panel({ title, children }) {
  const theme = useContext(ThemeContext);
  const className = 'panel-' + theme;
  return (
    <section className={className}>
      {title && <h1>{title}</h1>}
      {children}
    </section>
  )
}

function Button({ children }) {
  const theme = useContext(ThemeContext);
  const className = 'button-' + theme;
  return (
    <button className={className}>
      {children}
    </button>
  );
}
```

```css
footer {
  margin-top: 20px;
  border-top: 1px solid #aaa;
}

.panel-light,
.panel-dark {
  border: 1px solid black;
  border-radius: 4px;
  padding: 20px;
}
.panel-light {
  color: #222;
  background: #fff;
}

.panel-dark {
  color: #fff;
  background: rgb(23, 32, 42);
}

.button-light,
.button-dark {
  border: 1px solid #777;
  padding: 5px;
  margin-right: 10px;
  margin-top: 10px;
}

.button-dark {
  background: #222;
  color: #fff;
}

.button-light {
  background: #fff;
  color: #222;
}
```

</Sandpack>

<Solution />

#### স্বয়ংক্রিয়ভাবে নেস্টেড হেডিং {/*automatically-nested-headings*/}

আপনি যখন context provider গুলো নেস্ট করবেন তখন আপনি তথ্য "সঞ্চয়" করতে পারেন। এই উদাহরণে, `Section` কম্পোনেন্টটি `LevelContext` এর ট্র্যাক রাখে যা সেকশন নেস্টিংয়ের গভীরতা পরিমাপ করে। এটি প্যারেন্ট সেকশন থেকে `LevelContext` রিড করে এবং তার চিলড্রেনের জন্য `LevelContext` সংখ্যা এক এক করে বৃদ্ধি করে প্রদান করে। ফলস্বরূপ, এর ভিতরে কতগুলি সেকশন কম্পোনেন্ট নেস্ট করা হয়েছে তার উপর ভিত্তি করে `Heading` কম্পোনেন্ট স্বয়ংক্রিয়ভাবে সিদ্ধান্ত নিতে পারে যে `<h1>`, `<h2>`, `<h3>`, ..., এর ভিতর কোন ট্যাগটি ব্যবহার করতে হবে।

এই উদাহরণের [বিস্তারিত ওয়াকথ্রু](/learn/passing-data-deeply-with-context) পড়ুন।

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section>
      <Heading>Title</Heading>
      <Section>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Section>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Section>
            <Heading>Sub-sub-heading</Heading>
            <Heading>Sub-sub-heading</Heading>
            <Heading>Sub-sub-heading</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

```js src/Section.js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Section({ children }) {
  const level = useContext(LevelContext);
  return (
    <section className="section">
      <LevelContext value={level + 1}>
        {children}
      </LevelContext>
    </section>
  );
}
```

```js src/Heading.js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Heading({ children }) {
  const level = useContext(LevelContext);
  switch (level) {
    case 0:
      throw Error('Heading must be inside a Section!');
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('Unknown level: ' + level);
  }
}
```

```js src/LevelContext.js
import { createContext } from 'react';

export const LevelContext = createContext(0);
```

```css
.section {
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #aaa;
}
```

</Sandpack>

<Solution />

</Recipes>

---

### object এবং function পাস করার সময় পুনরায় রেন্ডার অপ্টিমাইজ করা {/*optimizing-re-renders-when-passing-objects-and-functions*/}

আপনি object এবং function সহ যেকোনো মান context এর মাধ্যমে পাস করতে পারেন।

```js [[2, 10, "{ currentUser, login }"]]
function MyApp() {
  const [currentUser, setCurrentUser] = useState(null);

  function login(response) {
    storeCredentials(response.credentials);
    setCurrentUser(response.user);
  }

  return (
    <AuthContext value={{ currentUser, login }}>
      <Page />
    </AuthContext>
  );
}
```

এখানে, <CodeStep step={2}>context এর মান</CodeStep> হল একটি JavaScript অবজেক্ট যার দুইটি প্যারামিটার রয়েছে, যার ভিতর একটি function। যখনই `MyApp` পুনরায় রেন্ডার করে (উদাহরণস্বরূপ, একটি রাউট আপডেটে), তখন এটি একটি *ভিন্ন* object হবে যা একটি ভিন্ন function নির্দেশ করে, তাই React কেও ট্রির গভীরে সব কম্পোনেন্টকে পুনরায় রেন্ডার করতে হবে যারা `useContext(AuthContext)` কল করেছে।

ছোট অ্যাপগুলিতে এটি কোনো সমস্যা নয়। যাই হোক, যদি `currentUser` এর মত অন্তর্নিহিত ডেটা পরিবর্তিত না হয় তবে তাদের পুনরায় রেন্ডার করার কোন প্রয়োজন নেই। এই ক্ষেত্রে React কে সুবিধা নিতে সাহায্য করার জন্য আপনি [`useCallback`](/reference/react/useCallback) দিয়ে `login` ফাংশনটি wrap করে দিতে পারেন এবং object তৈরিকে [`useMemo`](/reference/react/useMemo) দিয়ে wrap করে দিতে পারেন। এটি একটি পারফরমেন্স অপ্টিমাইজেশানঃ

```js {6,9,11,14,17}
import { useCallback, useMemo } from 'react';

function MyApp() {
  const [currentUser, setCurrentUser] = useState(null);

  const login = useCallback((response) => {
    storeCredentials(response.credentials);
    setCurrentUser(response.user);
  }, []);

  const contextValue = useMemo(() => ({
    currentUser,
    login
  }), [currentUser, login]);

  return (
    <AuthContext value={contextValue}>
      <Page />
    </AuthContext>
  );
}
```

এই পরিবর্তনের ফলস্বরূপ, এমনকি যদি `MyApp` কে পুনরায় রেন্ডার করার প্রয়োজন হয়, তবুও `useContext(AuthContext)` কল করা কম্পোনেন্ট পুনরায় রেন্ডার করার প্রয়োজন হবে না যদি না বর্তমান ব্যবহারকারী পরিবর্তিত হয়।

[`useMemo`](/reference/react/useMemo#skipping-re-rendering-of-components) এবং [`useCallback`](/reference/react/useCallback#skipping-re-rendering-of-components) সম্পর্কে আরও পড়ুন।

---

## সমস্যা সমাধান {/*troubleshooting*/}

### আমার কম্পোনেন্ট আমার provider এর মান দেখতে পায় না {/*my-component-doesnt-see-the-value-from-my-provider*/}

কিছু সাধারণ কারণে এমনটি ঘটতে পারে:

1. আপনি যেখানে `useContext()` কল করছেন সেই কম্পোনেন্টে (অথবা নিচে) `<SomeContext>` রেন্ডার করেছেন। যে কম্পোনেন্টে `useContext()` কল হচ্ছে তার *উপরে এবং বাইরে* `<SomeContext>` কে সরিয়ে ফেলুন।
2. আপনি হয়ত `<SomeContext>` দিয়ে আপনার কম্পোনেন্টকে wrap করতে ভুলে গেছেন, অথবা আপনি এটিকে ট্রির যেখানে রাখার কথা ভেবেছিলেন তার থেকে হয়ত ভিন্ন কোথাও রেখেছেন। [React DevTools](/learn/react-developer-tools) ব্যবহার করে hierarchy টি সঠিক কি না তা পরীক্ষা করুন।
3. আপনি হয়ত আপনার টুলিং সম্পর্কিত কিছু বিল্ড সমস্যার মধ্যে পড়েছেন যার ফলে প্রোভাইডিং কম্পোনেন্ট থেকে `SomeContext` এবং রিডিং কম্পোনেন্ট থেকে `SomeContext` ভিন্ন object হিসাবে দেখাচ্ছে। উদাহরণস্বরূপ, আপনি যদি symlink ব্যবহার করেন, আপনি এগুলোকে গ্লোবালে যুক্ত করে (যেমন `window.SomeContext1` এবং `window.SomeContext2`) এবং তারপর কনসোলে `window.SomeContext1 === window.SomeContext2` কি না তা পরীক্ষা করে এটি যাচাই করতে পারেন। যদি তারা একই না হয়, তাহলে বিল্ড টুল স্তরে সেই সমস্যাটি ঠিক করুন।

### আমি সব সময় আমার context থেকে `undefined` পাচ্ছি যদিও ডিফল্ট মান ভিন্ন {/*i-am-always-getting-undefined-from-my-context-although-the-default-value-is-different*/}

ট্রিতে হয়ত আপনার একটি `value` বিহীন provider আছেঃ

```js {1,2}
// 🚩 Doesn't work: no value prop
<ThemeContext>
   <Button />
</ThemeContext>
```

আপনি যদি `value` উল্লেখ করতে ভুলে যান, তাহলে এটি `value={undefined}` এরকম কিছু পাস করে।

আপনি হয়ত ভুল করে একটি ভিন্ন প্রপ নাম ব্যবহার করতে পারেন:

```js {1,2}
// 🚩 Doesn't work: prop should be called "value"
<ThemeContext theme={theme}>
   <Button />
</ThemeContext>
```

উভয় ক্ষেত্রেই আপনি কনসোলে React থেকে একটি ওয়ার্নিং দেখতে পাবেন। তাদের ঠিক করতে, প্রপ হিসাবে `value` কল করুন:

```js {1,2}
// ✅ Passing the value prop
<ThemeContext value={theme}>
   <Button />
</ThemeContext>
```
