---
title: TypeScript ব্যাবহার করা
re: https://github.com/reactjs/react.dev/issues/5960
---

<Intro>

TypeScript একটি জনপ্রিয় উপায় যা JavaScript কোডবেসে টাইপ ডিফিনিশন যোগ করতে ব্যবহৃত হয়। আউট অফ দ্য বক্স, TypeScript [JSX সমর্থন করে](/learn/writing-markup-with-jsx) এবং আপনি আপনার প্রকল্পে [`@types/react`](https://www.npmjs.com/package/@types/react) এবং [`@types/react-dom`](https://www.npmjs.com/package/@types/react-dom) যোগ করে পূর্ণ React ওয়েব সমর্থন পেতে পারেন।

</Intro>

<YouWillLearn>

* [React কম্পোনেন্টের সাথে TypeScript](/learn/typescript#typescript-with-react-components)
* [হুকের সাথে টাইপিং এর উদাহরণ](/learn/typescript#example-hooks)
* [`@types/react` থেকে সাধারণ টাইপগুলি](/learn/typescript/#useful-types)
* [অতিরিক্ত শিক্ষার স্থান](/learn/typescript/#further-learning)

</YouWillLearn>

## ইন্সটলেশন {/*installation*/}

সব [প্রোডাকশন-গ্রেড React ফ্রেমওয়ার্ক](/learn/start-a-new-react-project#production-grade-react-frameworks) TypeScript ব্যবহারের সমর্থন দেয়। ইনস্টলেশনের জন্য ফ্রেমওয়ার্ক স্পেসিফিক গাইড অনুসরণ করুন:

- [Next.js](https://nextjs.org/docs/app/building-your-application/configuring/typescript)
- [Remix](https://remix.run/docs/en/1.19.2/guides/typescript)
- [Gatsby](https://www.gatsbyjs.com/docs/how-to/custom-configuration/typescript/)
- [Expo](https://docs.expo.dev/guides/typescript/)

### বিদ্যমান React প্রকল্পে টাইপস্ক্রিপ্ট যোগ করা {/*adding-typescript-to-an-existing-react-project*/}

React-এর সর্বশেষ ভার্ষন টাইপ ডেফিনিশন ইনস্টল করতে:

<TerminalBlock>
npm install @types/react @types/react-dom
</TerminalBlock>

নিম্নলিখিত কম্পাইলার বিকল্পগুলি আপনার `tsconfig.json` ফাইলে সেট করতে হবে:

1. `lib` তে [`dom`](https://www.typescriptlang.org/tsconfig/#lib) অন্তর্ভুক্ত থাকতে হবে। (নোট: যদি কোনো `lib` বিকল্প নির্দিষ্ট না করা হয়, তবে `dom` ডিফল্টভাবে অন্তর্ভুক্ত হয়।)
2. [`jsx`](https://www.typescriptlang.org/tsconfig/#jsx) একটি বৈধ অপশনে সেট করতে হবে। অধিকাংশ অ্যাপ্লিকেশনের জন্য `preserve` যথেষ্ট হবে।  
   যদি আপনি একটি লাইব্রেরি প্রকাশ করছেন, তবে কোন মান নির্বাচন করতে হবে তা জানতে [`jsx` ডকুমেন্টেশন](https://www.typescriptlang.org/tsconfig/#jsx) পরামর্শ করুন।

## React কম্পোনেন্টের সাথে টাইপস্ক্রিপ্ট {/*typescript-with-react-components*/}

<Note>

প্রত্যেক ফাইল যেখানে JSX থাকে, সেখানে `.tsx` ফাইল এক্সটেনশন ব্যবহার করতে হবে। এটি টাইপস্ক্রিপ্টকে জানায় যে এই ফাইলে JSX আছে।

</Note>

React এর সাথে টাইপস্ক্রিপ্ট ব্যবহার করা বেশিরভাগ ক্ষেত্রেই জাভাস্ক্রিপ্টের সাথে React ব্যবহারের মতো। মূল পার্থক্য হলো আপনার কম্পোনেন্টের প্রপসে টাইপ যোগ করতে পারেন। এই টাইপস সম্পূর্ণতার যাচাই এবং এডিটরে ইনলাইন ডকুমেন্টেশন প্রদান করতে ব্যবহৃত হতে পারে।

[Quick Start](/learn) গাইড থেকে [`MyButton` কম্পোনেন্ট](/learn#components) নেওয়ার মাধ্যমে, আমরা বোতামের জন্য `title` বর্ণনা করতে একটি টাইপ যোগ করতে পারি:

<Sandpack>

```tsx src/App.tsx active
function MyButton({ title }: { title: string }) {
  return (
    <button>{title}</button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton title="I'm a button" />
    </div>
  );
}
```

```js src/App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```
</Sandpack>

 <Note>

এই স্যান্ডবক্সগুলো টাইপস্ক্রিপ্ট কোড হ্যান্ডেল করতে পারে, কিন্তু টাইপ-চেকার চালায় না। এর অর্থ হলো, আপনি টাইপস্ক্রিপ্ট স্যান্ডবক্সগুলো পরিবর্তন করতে পারেন, কিন্তু টাইপ এরর বা সতর্কতা পাবেন না। টাইপ-চেকিং পেতে [TypeScript Playground](https://www.typescriptlang.org/play) বা অন্য অনলাইন স্যান্ডবক্স ব্যবহার করতে পারেন।

</Note>

এই ইনলাইন সিনট্যাক্স কম্পোনেন্টের জন্য টাইপ প্রদান করার সহজতম উপায়, যদিও একাধিক ক্ষেত্র থাকলে এটি জটিল হতে পারে। পরিবর্তে, আপনি একটি `interface` বা `type` ব্যবহার করতে পারেন:

<Sandpack>

```tsx src/App.tsx active
interface MyButtonProps {
  /** The text to display inside the button */
  title: string;
  /** Whether the button can be interacted with */
  disabled: boolean;
}

function MyButton({ title, disabled }: MyButtonProps) {
  return (
    <button disabled={disabled}>{title}</button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton title="I'm a disabled button" disabled={true}/>
    </div>
  );
}
```

```js src/App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```

</Sandpack>

আপনার কম্পোনেন্টের প্রপস বর্ণনা করার টাইপটি যতটা সহজ বা জটিল প্রয়োজন ততটাই হতে পারে, তবে এটি একটি `type` বা `interface` দিয়ে বর্ণনা করা একটি অবজেক্ট টাইপ হওয়া উচিত। TypeScript কীভাবে অবজেক্ট বর্ণনা করে তা শিখতে [Object Types](https://www.typescriptlang.org/docs/handbook/2/objects.html) দেখুন, তবে আপনি [Union Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#union-types) ব্যবহার করতেও আগ্রহী হতে পারেন, যা একটি প্রপকে কয়েকটি ভিন্ন টাইপের মধ্যে একটির বর্ণনা করতে সক্ষম করে এবং [Creating Types from Types](https://www.typescriptlang.org/docs/handbook/2/types-from-types.html) গাইডটি আরও উন্নত ব্যবহারের জন্য।

## হুকের উদাহরণ {/*example-hooks*/}

`@types/react` থেকে টাইপ ডিফিনিশনগুলি বিল্ট-ইন হুকগুলোর জন্য টাইপ অন্তর্ভুক্ত করে, তাই আপনি আপনার কম্পোনেন্টে অতিরিক্ত সেটআপ ছাড়াই সেগুলি ব্যবহার করতে পারেন। এগুলি আপনার কম্পোনেন্টে যে কোড আপনি লেখেন তার প্রতি মনোযোগ দিয়ে তৈরি করা হয়েছে, তাই আপনি অনেক সময় [inferred types](https://www.typescriptlang.org/docs/handbook/type-inference.html) পাবেন এবং আদর্শভাবে টাইপ সরবরাহ করার সূক্ষ্ম বিষয়গুলো পরিচালনা করার প্রয়োজন পড়বে না।

তবে, আমরা হুকগুলোর জন্য টাইপ প্রদান করার কয়েকটি উদাহরণ দেখতে পারি।

### `useState` {/*typing-usestate*/}

[`useState` হুক](/reference/react/useState) প্রাথমিক অবস্থার জন্য দেওয়া মানটি পুনঃব্যবহার করবে এটি নির্ধারণ করতে যে মানের টাইপটি কী হওয়া উচিত। উদাহরণস্বরূপ:

```ts
// Infer the type as "boolean"
const [enabled, setEnabled] = useState(false);
```

`enabled` এর জন্য `boolean` টাইপ নির্ধারণ করবে, এবং `setEnabled` হবে একটি ফাংশন যা বা একটি `boolean` আর্গুমেন্ট গ্রহণ করে, অথবা একটি ফাংশন যা একটি `boolean` ফেরত দেয়। যদি আপনি অবস্থার জন্য স্পষ্টভাবে একটি টাইপ প্রদান করতে চান, তবে আপনি `useState` কলের সাথে একটি টাইপ আর্গুমেন্ট প্রদান করে এটি করতে পারেন:

```ts 
// Explicitly set the type to "boolean"
const [enabled, setEnabled] = useState<boolean>(false);
```

এটি এই ক্ষেত্রে খুব বেশি কার্যকর নয়, তবে একটি সাধারণ ক্ষেত্রে যেখানে আপনি একটি টাইপ প্রদান করতে চাইতে পারেন তা হল যখন আপনার একটি ইউনিয়ন টাইপ থাকে। উদাহরণস্বরূপ, এখানে `status` কয়েকটি ভিন্ন স্ট্রিংয়ের মধ্যে একটি হতে পারে:

```ts
type Status = "idle" | "loading" | "success" | "error";

const [status, setStatus] = useState<Status>("idle");
```

অথবা, [State গঠন করার নীতির](https://reactjs.org/docs/faq-structure.html#principles-for-structuring-state) অনুসারে, আপনি সম্পর্কিত অবস্থাগুলিকে একটি অবজেক্ট হিসাবে গ্রুপ করতে পারেন এবং অবজেক্ট টাইপের মাধ্যমে বিভিন্ন সম্ভাবনাগুলি বর্ণনা করতে পারেন:

```ts
type RequestState =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success', data: any }
  | { status: 'error', error: Error };

const [requestState, setRequestState] = useState<RequestState>({ status: 'idle' });
```

### `useReducer` {/*typing-usereducer*/}

[`useReducer` হুক](/reference/react/useReducer) একটি আরও জটিল হুক যা একটি রিডিউসার ফাংশন এবং একটি প্রাথমিক অবস্থা নেয়। রিডিউসার ফাংশনের জন্য টাইপগুলি প্রাথমিক অবস্থাটি থেকে ইনফার করা হয়। আপনি ঐচ্ছিকভাবে `useReducer` কলের সাথে একটি টাইপ আর্গুমেন্ট প্রদান করে অবস্থার জন্য একটি টাইপ প্রদান করতে পারেন, তবে প্রায়শই এটি প্রাথমিক অবস্থার উপর টাইপ সেট করা ভাল:

<Sandpack>

```tsx src/App.tsx active
import {useReducer} from 'react';

interface State {
   count: number 
};

type CounterAction =
  | { type: "reset" }
  | { type: "setCount"; value: State["count"] }

const initialState: State = { count: 0 };

function stateReducer(state: State, action: CounterAction): State {
  switch (action.type) {
    case "reset":
      return initialState;
    case "setCount":
      return { ...state, count: action.value };
    default:
      throw new Error("Unknown action");
  }
}

export default function App() {
  const [state, dispatch] = useReducer(stateReducer, initialState);

  const addFive = () => dispatch({ type: "setCount", value: state.count + 5 });
  const reset = () => dispatch({ type: "reset" });

  return (
    <div>
      <h1>Welcome to my counter</h1>

      <p>Count: {state.count}</p>
      <button onClick={addFive}>Add 5</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}

```

```js src/App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```

</Sandpack>


আমরা TypeScript কয়েকটি গুরুত্বপূর্ণ স্থানে ব্যবহার করছি:

- `interface State` রিডিউসারের স্টেটের আকৃতি বর্ণনা করে।
- `type CounterAction` বিভিন্ন অ্যাকশনগুলো বর্ণনা করে যা রিডিউসারে পাঠানো যেতে পারে।
- `const initialState: State` প্রাথমিক স্টেটের জন্য টাইপ প্রদান করে এবং `useReducer`-এর ডিফল্ট টাইপ হিসেবেও ব্যবহৃত হয়।
- `stateReducer(state: State, action: CounterAction): State` রিডিউসার ফাংশনের আর্গুমেন্ট এবং রিটার্ন ভ্যালুর টাইপ সেট করে।

প্রাথমিক স্টেটে টাইপ নির্ধারণের আরেকটি স্পষ্ট পদ্ধতি হলো `useReducer`-এ টাইপ আর্গুমেন্ট ব্যবহার করা:

```ts
import { stateReducer, State } from './your-reducer-implementation';

const initialState = { count: 0 };

export default function App() {
  const [state, dispatch] = useReducer<State>(stateReducer, initialState);
}
```

### `useContext` {/*typing-usecontext*/}

[`useContext` হুক](/reference/react/useContext) হল কম্পোনেন্ট গাছের নিচে ডেটা পাঠানোর একটি কৌশল যা কম্পোনেন্টের মাধ্যমে প্রপ্স প্রেরণের প্রয়োজন নেই। এটি একটি প্রোভাইডার কম্পোনেন্ট তৈরি করে ব্যবহার করা হয় এবং প্রায়ই একটি হুক তৈরি করে একটি শিশু কম্পোনেন্টে মানটি ব্যবহার করতে হয়।

কনটেক্সট দ্বারা প্রদত্ত মানের টাইপটি `createContext` কলের সাথে দেওয়া মান থেকে ইনফার করা হয়:

<Sandpack>

```tsx src/App.tsx active
import { createContext, useContext, useState } from 'react';

type Theme = "light" | "dark" | "system";
const ThemeContext = createContext<Theme>("system");

const useGetTheme = () => useContext(ThemeContext);

export default function MyApp() {
  const [theme, setTheme] = useState<Theme>('light');

  return (
    <ThemeContext.Provider value={theme}>
      <MyComponent />
    </ThemeContext.Provider>
  )
}

function MyComponent() {
  const theme = useGetTheme();

  return (
    <div>
      <p>Current theme: {theme}</p>
    </div>
  )
}
```

```js src/App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```

</Sandpack>

এই কৌশলটি যখন আপনার কাছে একটি ডিফল্ট মান থাকে যা যুক্তিযুক্ত হয় তখন কাজ করে—কিন্তু মাঝে মাঝে এমন পরিস্থিতি থাকতে পারে যখন আপনার ডিফল্ট মান নেই, এবং সেক্ষেত্রে `null` একটি যুক্তিযুক্ত ডিফল্ট মান হতে পারে। তবে, টাইপ সিস্টেমকে আপনার কোড বোঝাতে দিতে, আপনাকে `createContext`-এ স্পষ্টভাবে `ContextShape | null` সেট করতে হবে।

এটি কনটেক্সট কনজ্যুমারদের জন্য টাইপে `| null` বাদ দিতে হবে বলে একটি সমস্যা সৃষ্টি করে। আমাদের পরামর্শ হল, হুকটি তার অস্তিত্বের জন্য একটি রানটাইম চেক করতে পারে এবং যখন এটি উপস্থিত না থাকে তখন একটি এরোর থ্রো করে:

```js {5, 16-20}
import { createContext, useContext, useState, useMemo } from 'react';

// This is a simpler example, but you can imagine a more complex object here
type ComplexObject = {
  kind: string
};

// The context is created with `| null` in the type, to accurately reflect the default value.
const Context = createContext<ComplexObject | null>(null);

// The `| null` will be removed via the check in the Hook.
const useGetComplexObject = () => {
  const object = useContext(Context);
  if (!object) { throw new Error("useGetComplexObject must be used within a Provider") }
  return object;
}

export default function MyApp() {
  const object = useMemo(() => ({ kind: "complex" }), []);

  return (
    <Context.Provider value={object}>
      <MyComponent />
    </Context.Provider>
  )
}

function MyComponent() {
  const object = useGetComplexObject();

  return (
    <div>
      <p>Current object: {object.kind}</p>
    </div>
  )
}
```

### `useMemo` {/*typing-usememo*/}

[`useMemo`](/reference/react/useMemo) হুক একটি মেমোরাইজড মান তৈরি বা পুনরায় অ্যাক্সেস করবে, যা নির্দিষ্ট একটি ফাংশন কল থেকে আসে। এই ফাংশনটি শুধুমাত্র তখনই পুনরায় রান করবে যখন দ্বিতীয় প্যারামিটার হিসাবে পাস করা নির্ভরশীল উপাদানগুলো পরিবর্তিত হয়। হুকটি কল করার ফলাফলটি প্রথম প্যারামিটারে দেওয়া ফাংশনের রিটার্ন মান থেকে অনুমান করা হয়। তবে আপনি টাইপিং আরো স্পষ্ট করতে চাইলে হুকে একটি টাইপ আর্গুমেন্টও সরবরাহ করতে পারেন।

```ts
// The type of visibleTodos is inferred from the return value of filterTodos
const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
```


### `useCallback` {/*typing-usecallback*/}

[`useCallback`](/reference/react/useCallback) হুক একটি ফাংশনের স্থিতিশীল রেফারেন্স সরবরাহ করে যতক্ষণ দ্বিতীয় প্যারামিটারে পাস করা নির্ভরশীল উপাদানগুলো একই থাকে। `useMemo`-র মতো, ফাংশনের টাইপ প্রথম প্যারামিটারে দেওয়া ফাংশনের রিটার্ন মান থেকে অনুমান করা হয়, তবে আপনি চাইলে হুকে একটি টাইপ আর্গুমেন্ট দিয়ে এটিকে আরও স্পষ্ট করতে পারেন।

```ts
const handleClick = useCallback(() => {
  // ...
}, [todos]);
```

টাইপস্ক্রিপ্টের স্ট্রিক্ট মোডে কাজ করার সময়, `useCallback`-এর জন্য কলব্যাক ফাংশনের প্যারামিটারগুলোর টাইপ যোগ করতে হয়। এর কারণ হলো, কলব্যাক ফাংশনের টাইপটি মূলত ফাংশনের রিটার্ন ভ্যালু থেকে নির্ণয় করা হয়, এবং প্যারামিটার ছাড়া টাইপটি সম্পূর্ণভাবে বোঝা যায় না।

আপনার কোড-স্টাইল পছন্দের ওপর নির্ভর করে, আপনি *EventHandler ফাংশনগুলো ব্যবহার করতে পারেন React টাইপ থেকে। এটি আপনাকে একই সাথে কলব্যাক ফাংশন ডিফাইন এবং ইভেন্ট হ্যান্ডলারটির টাইপ প্রদান করতে সহায়তা করে।

```ts
import { useState, useCallback } from 'react';

export default function Form() {
  const [value, setValue] = useState("Change me");

  const handleChange = useCallback<React.ChangeEventHandler<HTMLInputElement>>((event) => {
    setValue(event.currentTarget.value);
  }, [setValue])
  
  return (
    <>
      <input value={value} onChange={handleChange} />
      <p>Value: {value}</p>
    </>
  );
}
```

## উপকারী টাইপসমূহ {/*useful-types*/}

`@types/react` প্যাকেজ থেকে আসা একটি বিস্তৃত টাইপ সেট রয়েছে, এটি পড়া উপকারী যখন আপনি বুঝতে পারবেন যে React এবং TypeScript কীভাবে взаимодействуют। আপনি এগুলি [DefinitelyTyped-এ React-এর ফোল্ডারে](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/react/index.d.ts) খুঁজে পেতে পারেন। এখানে আমরা কয়েকটি সাধারণ টাইপ নিয়ে আলোচনা করব।

### DOM ইভেন্টসমূহ {/*typing-dom-events*/}

React-এ DOM ইভেন্ট নিয়ে কাজ করার সময়, সাধারণত ইভেন্ট হ্যান্ডলার থেকে ইভেন্টের টাইপটি নির্ধারণ করা যায়। তবে, যখন আপনি একটি ফাংশনকে ইভেন্ট হ্যান্ডলারে পাস করার জন্য বের করতে চান, তখন আপনাকে স্পষ্টভাবে ইভেন্টের টাইপ সেট করতে হবে।

<Sandpack>

```tsx src/App.tsx active
import { useState } from 'react';

export default function Form() {
  const [value, setValue] = useState("Change me");

  function handleChange(event: React.ChangeEvent<HTMLInputElement>) {
    setValue(event.currentTarget.value);
  }

  return (
    <>
      <input value={value} onChange={handleChange} />
      <p>Value: {value}</p>
    </>
  );
}
```

```js src/App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```

</Sandpack>

React টাইপগুলিতে অনেক ধরনের ইভেন্ট দেওয়া হয়েছে—পূর্ণ তালিকা [এখানে](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/b580df54c0819ec9df62b0835a315dd48b8594a9/types/react/index.d.ts#L1247C1-L1373) পাওয়া যাবে, যা [DOM থেকে সবচেয়ে জনপ্রিয় ইভেন্টগুলির](https://developer.mozilla.org/en-US/docs/Web/Events) উপর ভিত্তি করে। 

আপনি যে টাইপটি খুঁজছেন তা নির্ধারণ করার সময়, আপনি প্রথমে যেই ইভেন্ট হ্যান্ডলারটি ব্যবহার করছেন তার হভার তথ্য দেখতে পারেন, যা ইভেন্টের টাইপটি দেখাবে। 

যদি আপনার এমন একটি ইভেন্ট ব্যবহার করতে হয় যা এই তালিকায় অন্তর্ভুক্ত নয়, তাহলে আপনি `React.SyntheticEvent` টাইপ ব্যবহার করতে পারেন, যা সমস্ত ইভেন্টের জন্য বেস টাইপ।

### Children {/*typing-children*/}

একটি কম্পোনেন্টের চাইল্ড বর্ণনা করার জন্য দুটি সাধারণ পদ্ধতি রয়েছে। প্রথমটি হল `React.ReactNode` টাইপ ব্যবহার করা, যা JSX-এ চাইল্ড হিসেবে পাস করা সমস্ত সম্ভাব্য টাইপের একটি ইউনিয়ন। 

এখানে, "চাইল্ড" হচ্ছে প্রোগ্রামিংয়ের একটি ধারণা, যা নির্দেশ করে যে একটি কম্পোনেন্টের মধ্যে অন্য একটি কম্পোনেন্ট বা উপাদান কিভাবে নেস্ট করা হয়েছে।

```ts
interface ModalRendererProps {
  title: string;
  children: React.ReactNode;
}
```

এটি চাইল্ডসমূহের একটি খুবই বিস্তৃত সংজ্ঞা। দ্বিতীয়টি হল `React.ReactElement` টাইপ ব্যবহার করা, যা শুধুমাত্র JSX উপাদান এবং স্ট্রিং বা সংখ্যা মতো জাভাস্ক্রিপ্ট প্রিমিটিভ নয়:

```ts
interface ModalRendererProps {
  title: string;
  children: React.ReactElement;
}
```

দ্রষ্টব্য, আপনি TypeScript ব্যবহার করে বর্ণনা করতে পারবেন না যে চাইল্ডসমূহের একটি নির্দিষ্ট ধরনের JSX উপাদান, তাই আপনি টাইপ সিস্টেম ব্যবহার করে এমন একটি কম্পোনেন্ট বর্ণনা করতে পারবেন না যা কেবল `<li>` শিশু গ্রহণ করে।

আপনি উভয় `React.ReactNode` এবং `React.ReactElement` এর একটি উদাহরণ টাইপ-চেকার সহ [এই TypeScript প্লেগ্রাউন্ডে](https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAJQKYEMDG8BmUIjgIilQ3wChSB6CxYmAOmXRgDkIATJOdNJMGAZzgwAFpxAR+8YADswAVwGkZMJFEzpOjDKw4AFHGEEBvUnDhphwADZsi0gFw0mDWjqQBuUgF9yaCNMlENzgAXjgACjADfkctFnYkfQhDAEpQgD44AB42YAA3dKMo5P46C2tbJGkvLIpcgt9-QLi3AEEwMFCItJDMrPTTbIQ3dKywdIB5aU4kKyQQKpha8drhhIGzLLWODbNs3b3s8YAxKBQAcwXpAThMaGWDvbH0gFloGbmrgQfBzYpd1YjQZbEYARkB6zMwO2SHSAAlZlYIBCdtCRkZpHIrFYahQYQD8UYYFA5EhcfjyGYqHAXnJAsIUHlOOUbHYhMIIHJzsI0Qk4P9SLUBuRqXEXEwAKKfRZcNA8PiCfxWACecAAUgBlAAacFm80W-CU11U6h4TgwUv11yShjgJjMLMqDnN9Dilq+nh8pD8AXgCHdMrCkWisVoAet0R6fXqhWKhjKllZVVxMcavpd4Zg7U6Qaj+2hmdG4zeRF10uu-Aeq0LBfLMEe-V+T2L7zLVu+FBWLdLeq+lc7DYFf39deFVOotMCACNOCh1dq219a+30uC8YWoZsRyuEdjkevR8uvoVMdjyTWt4WiSSydXD4NqZP4AymeZE072ZzuUeZQKheQgA) দেখতে পারেন।

### টাইল প্রপস {/*typing-style-props*/}

React-এ ইনলাইন স্টাইল ব্যবহার করার সময়, আপনি `React.CSSProperties` ব্যবহার করে স্টাইল প্রপে পাস করা অবজেক্টটি বর্ণনা করতে পারেন। এই টাইপটি সমস্ত সম্ভাব্য CSS প্রপার্টির একটি ইউনিয়ন এবং এটি নিশ্চিত করার জন্য একটি ভালো উপায় যে আপনি স্টাইল প্রপে বৈধ CSS প্রপার্টি পাস করছেন, এবং আপনার এডিটরে অটো-কমপ্লিট পাওয়ার জন্য।

```ts
interface MyComponentProps {
  style: React.CSSProperties;
}
```

## আরও শেখা {/*further-learning*/}

এই গাইডে React এর সাথে TypeScript ব্যবহারের মৌলিক বিষয়গুলো আলোচনা করা হয়েছে, তবে শেখার জন্য আরও অনেক কিছু রয়েছে। ডকসের পৃথক API পৃষ্ঠাগুলো TypeScript এর সাথে কীভাবে ব্যবহার করতে হয় সে সম্পর্কে আরও গভীর ডকুমেন্টেশন ধারণ করতে পারে।

আমরা নিম্নলিখিত রিসোর্সগুলোর সুপারিশ করছি:

- [TypeScript হ্যান্ডবুক](https://www.typescriptlang.org/docs/handbook/) হল TypeScript এর অফিসিয়াল ডকুমেন্টেশন, যা বেশিরভাগ মূল ভাষার বৈশিষ্ট্যগুলো আলোচনা করে।

- [TypeScript রিলিজ নোটস](https://devblogs.microsoft.com/typescript/) নতুন বৈশিষ্ট্যগুলো বিস্তারিতভাবে আলোচনা করে।

- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/) হল TypeScript এর সাথে React ব্যবহার করার জন্য একটি কমিউনিটি দ্বারা রক্ষণাবেক্ষিত চিটশিট, যা অনেক কার্যকর প্রান্তের কেস এবং এই ডকুমেন্টের চেয়ে আরও বিস্তৃত বিষয়গুলি কভার করে।

- [TypeScript কমিউনিটি ডিসকর্ড](https://discord.com/invite/typescript) TypeScript এবং React সমস্যাগুলির জন্য প্রশ্ন জিজ্ঞাসা করতে এবং সাহায্য পাওয়ার জন্য একটি চমৎকার জায়গা।