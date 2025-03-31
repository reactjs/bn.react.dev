---
title: Context এর মাধ্যমে ডেটা Deeply Pass করা
---

<Intro>

সাধারণত, আপনি props এর মাধ্যমে একটি প্যারেন্ট কম্পোনেন্ট থেকে একটি চাইল্ড কম্পোনেন্টে ইনফর্মেশন pass করবেন। কিন্তু যদি আপনার props কে মাঝের অনেক অনেক কম্পোনেন্টের মধ্যে দিয়ে pass করা লাগে, অথবা আপনার অ্যাপের অনেক কম্পোনেন্টের একই ইনফর্মেশনের দরকার হয়, তখন props পাস করা শব্দবহুল এবং ঝামেলাপূর্ণ হতে পারে। *Context* প্যারেন্ট কম্পোনেন্টকে এর নিচের যেকোনো স্তরের যেকোনো কম্পোনেন্টের জন্য কিছু ইনফর্মেশন অ্যাভেইলেবল করতে দেয় (সে নিচের কম্পোনেন্ট যতই গভীরে হোক না কেনো) এ ইনফর্মেশনকে props এর মাধ্যমে স্পষ্টভাবে pass করাও লাগেনা।

</Intro>

<YouWillLearn>

- "Prop drilling (প্রপ ড্রিলিং)" কী
- কিভাবে context এর ব্যবহার করে বার বার prop পাস করা এড়াবেন 
- Context ব্যবহারের সাধারণ ক্ষেত্রসমূহ
- Context এর কিছু প্রচলিত বিকল্প

</YouWillLearn>

## Props পাস করার মূল সমস্যা {/*the-problem-with-passing-props*/}

[প্রপস পাস করা](/learn/passing-props-to-a-component) UI tree এর মধ্য দিয়ে ডেটাকে এমন কম্পোনেন্টস যেগুলোর ঐ ডেটা কাজে আসবে সেগুলো পর্যন্ত স্পষ্টভাবে পৌঁছে দেয়ার একটি বেশ ভালো পদ্ধতি।

কিন্তু প্রপস পাস করা অনেক শব্দ লেখার এবং ঝামেলার কারণ হতে পরে যখন আপনার কোনো প্রপকে tree এর মধ্য দিয়ে অনেক গভীরে (প্যারেন্ট থেকে অনেক দূরের চাইল্ড পর্যন্ত) পাস করা লাগে কিংবা যদি একাধিক কম্পোনেন্টের একই প্রপের দরকার হয়। যে কম্পোনেন্টগুলোর ডেটা প্রয়োজন, তাদের নিকটতম সাধারণ পূর্বপুরুষ (nearest common ancestor) তাদের থেকে অনেক অনেক দূরে হতে পারে, আর এত বেশি উপরের স্তরে [state কে উঠানো](/learn/sharing-state-between-components) এমন একটা পরিস্থিতি তৈরি করতে পারে যাকে বলা হয় "prop drilling"।

<DiagramGroup>

<Diagram name="passing_data_lifting_state" height={160} width={608} captionPosition="top" alt="Diagram with a tree of three components. The parent contains a bubble representing a value highlighted in purple. The value flows down to each of the two children, both highlighted in purple." >

State কে উপরে উঠানো

</Diagram>
<Diagram name="passing_data_prop_drilling" height={430} width={608} captionPosition="top" alt="Diagram with a tree of ten nodes, each node with two children or less. The root node contains a bubble representing a value highlighted in purple. The value flows down through the two children, each of which pass the value but do not contain it. The left child passes the value down to two children which are both highlighted purple. The right child of the root passes the value through to one of its two children - the right one, which is highlighted purple. That child passed the value through its single child, which passes it down to both of its two children, which are highlighted purple.">

Prop drilling

</Diagram>

</DiagramGroup>

এমন হলে কী চমৎকার হতোনা যদি প্রপস পাস না করেই tree এর মধ্যে যে কম্পোনেন্টগুলোর ডেটাটি প্রয়োজন সেগুলোর কাছে ডেটাকে "ম্যাজিকের মতো" নিয়ে যাওয়ার কোন উপায় থাকতো? React এর context ফিচারই হলো সে উপায়!

## কনটেক্সট: প্রপস পাস করার একটি বিকল্প পদ্ধতি {/*context-an-alternative-to-passing-props*/}

কনটেক্সট একটি প্যারেন্ট কম্পোনেন্টকে এর নিম্নস্থ সকল কম্পোনেন্টের tree কে ডেটা সরবরাহ করতে দেয়। কনটেক্সটের বহু ব্যবহার রয়েছে। একটি উদাহরণ দেখা যাক। এই `Heading` কম্পোনেন্টকে একটু দেখুন যেটি এর সাইজের জন্য কোনো `level` গ্রহণ করে:

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section>
      <Heading level={1}>Title</Heading>
      <Heading level={2}>Heading</Heading>
      <Heading level={3}>Sub-heading</Heading>
      <Heading level={4}>Sub-sub-heading</Heading>
      <Heading level={5}>Sub-sub-sub-heading</Heading>
      <Heading level={6}>Sub-sub-sub-sub-heading</Heading>
    </Section>
  );
}
```

```js src/Section.js
export default function Section({ children }) {
  return (
    <section className="section">
      {children}
    </section>
  );
}
```

```js src/Heading.js
export default function Heading({ level, children }) {
  switch (level) {
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

```css
.section {
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #aaa;
}
```

</Sandpack>

ধরুন আপনি একই `Section` এর ভিতরের বিভিন্ন headings সবসময় একই সাইজের হোক এটা চান:

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section>
      <Heading level={1}>Title</Heading>
      <Section>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Section>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Section>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

```js src/Section.js
export default function Section({ children }) {
  return (
    <section className="section">
      {children}
    </section>
  );
}
```

```js src/Heading.js
export default function Heading({ level, children }) {
  switch (level) {
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

```css
.section {
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
  border: 1px solid #aaa;
}
```

</Sandpack>

এখন, প্রত্যেক `<Heading>` কে আপনার `level` প্রপটি আলাদা আলাদা করে পাস করতে হচ্ছে:

```js
<Section>
  <Heading level={3}>About</Heading>
  <Heading level={3}>Photos</Heading>
  <Heading level={3}>Videos</Heading>
</Section>
```

এটা আরো সুন্দর হতো যদি আপনি এর বদলে `level` প্রপটিকে `<Section>` কম্পোনেন্টকে পাস করতে, আর `<Heading>` থেকে রিমুভ করতে পারতেন। এভাবে আপনি নিশ্চিত করতে পারতেন যে, একই সেকশনের সব হেডিংস একই সাইজের হবে:

```js
<Section level={3}>
  <Heading>About</Heading>
  <Heading>Photos</Heading>
  <Heading>Videos</Heading>
</Section>
```

কিন্তু `<Heading>` কম্পোনেন্টটি কিভাবে এর সবচেয়ে কাছের `<Section>` এর level জানবে? **তা করার জন্য tree এর উপরের কোথাও বিদ্যমান ডাটা, চাইল্ডের "চাইবার" জন্য কোনো উপায় থাকা লাগবে।**

আপনি শুধু প্রপস দিয়েই এটা করতে পারবেন না। এখানেই context এর ভূমিকা চলে আসে। আপনার তা তিনটি ধাপে করতে হবে:

1. একটি কনটেক্সট **Create** করা। (আপনি এর নাম দিবেন `LevelContext`, কেননা এটা হেডিং লেভেলের জন্য।)
2. যেসব কম্পোনেন্টের ডেটটি প্রয়োজন তাদের মধ্যে কনেটেক্সটটি **Use** করা। (`Heading` কম্পোনেন্টটি `LevelContext` কে use করবে।)
3. যে কম্পোনেন্টটি ডেটাটিকে স্পেসিফাই (উল্লেখ) করে তাদের থেকে কনটেক্সটটি **Provide** করা। (`Section` কম্পোনেন্টটি `LevelContext` কে provide করবে।)

Context একটি প্যারেন্টকে--এমনকি অনেক দূরের হলেও--এর নিচের সম্পূর্ণ tree কে কিছু ডেটা provide (সরবরাহ) করতে দেয়।

<DiagramGroup>

<Diagram name="passing_data_context_close" height={160} width={608} captionPosition="top" alt="Diagram with a tree of three components. The parent contains a bubble representing a value highlighted in orange which projects down to the two children, each highlighted in orange." >

কাছের চিলড্রেনদের জন্য context এর ব্যবহার

</Diagram>

<Diagram name="passing_data_context_far" height={430} width={608} captionPosition="top" alt="Diagram with a tree of ten nodes, each node with two children or less. The root parent node contains a bubble representing a value highlighted in orange. The value projects down directly to four leaves and one intermediate component in the tree, which are all highlighted in orange. None of the other intermediate components are highlighted.">

দূরের চিলড্রেনদের জন্য context এর ব্যবহার

</Diagram>

</DiagramGroup>

### ধাপ ১: কনটেক্সটটি create করুন {/*step-1-create-the-context*/}

প্রথমে, আপনার কনটেক্সটটি create করতে হবে। আপনার একে **একটি ফাইল থেকে export করতে হবে** যাতে করে আপনার কম্পোনেন্টগুলো একে use করতে পারে:

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section>
      <Heading level={1}>Title</Heading>
      <Section>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Section>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Section>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

```js src/Section.js
export default function Section({ children }) {
  return (
    <section className="section">
      {children}
    </section>
  );
}
```

```js src/Heading.js
export default function Heading({ level, children }) {
  switch (level) {
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

```js src/LevelContext.js active
import { createContext } from 'react';

export const LevelContext = createContext(1);
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

`createContext` এর একমাত্র আর্গুমেন্ট হলো _default_ ভ্যালু। এখানে `1` দ্বারা উদ্দেশ্য হলো সবচেয়ে বড় হেডিং লেভেল, কিন্তু আপনি যেকোনো ধরনের ভ্যালু (এমনকি একটি object) পাস করতে পারতেন। আপনি এই ডিফল্ট ভ্যালুর গুরুত্ব এর পরের ধাপে উপলব্ধি করতে পারবেন।

### ধাপ ২: কনটেক্সটটি use করুন {/*step-2-use-the-context*/}

`useContext` হুককে React থেকে এবং আপনার কনটেক্সট import করুন:

```js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';
```

বর্তমানে, `Heading` কম্পোনেন্টটি প্রপস থেকে `level` কে read করছে:

```js
export default function Heading({ level, children }) {
  // ...
}
```

এর পরিবর্তে, `level` প্রপটিকে রিমুভ করে দিন এবং আপনি `LevelContext` নামের যে কনটেক্সটটিকে মাত্র import করেছেন তার থেকে ভ্যালুটি read করুন:

```js {2}
export default function Heading({ children }) {
  const level = useContext(LevelContext);
  // ...
}
```

`useContext` একটি হুক। ঠিক `useState` এবং `useReducer` এর মতো, আপনি একটি হুককে React কম্পোনেন্টের ভিতর শুধুমাত্র সবার শুরুতে কল করতে পারবেন (লুপ কিংবা কন্ডিশনের ভিতর না)। **`useContext` React কে বলে দেয় যে `Heading` কম্পোনেন্টটি `LevelContext` কে read করতে চাচ্ছে।**

এখন যেহেতু `Heading` কম্পোনেন্টটির কোনো `level` প্রপ নেই, আপনার লেভেল প্রপটিকে JSX এর ভিতর `Heading` কে এভাবে পাস করার কোনো প্রয়োজন নেই:

```js
<Section>
  <Heading level={4}>Sub-sub-heading</Heading>
  <Heading level={4}>Sub-sub-heading</Heading>
  <Heading level={4}>Sub-sub-heading</Heading>
</Section>
```

JSX কে আপডেট করুন যাতে এর পরিবর্তে লেভেলটিকে শুধুমাত্র `Section` রিসিভ করে:

```jsx
<Section level={4}>
  <Heading>Sub-sub-heading</Heading>
  <Heading>Sub-sub-heading</Heading>
  <Heading>Sub-sub-heading</Heading>
</Section>
```

মনে আছে তো, এই হলো সেই মার্কআপ যেটার মতো মার্কআপ নিয়ে কাজ করার আশা আপনি করছিলেন:

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section level={1}>
      <Heading>Title</Heading>
      <Section level={2}>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Section level={3}>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Section level={4}>
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
export default function Section({ children }) {
  return (
    <section className="section">
      {children}
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

export const LevelContext = createContext(1);
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

খেয়াল করুন এই এক্সাম্পলটি এখন পর্যন্ত কাজ করছেনা! সব হেডিংয়ের সাইজ একই, কারণ আপনি **কনটেক্সট *use* করলেও, এখনো আপনি একে *provide* করেননি।** React জানেন কোথায় এই কনটেক্সটকে পাওয়া যাবে!

আপনি যদি কনটেক্সটি provide না করেন, React আপনি আগের ধাপে যে ডিফল্ট ভ্যালু ঠিক করে দিয়েছেন তাকেই ব্যবহার করবে। এই উদাহরণে, আপনি `createContext` এর আর্গুমেন্ট হিসেবে `1` ঠিক করে দিয়েছেন, `useContext(LevelContext)` তাই `1` রিটার্ন করছে, ফলে ঐসব হেডিংকে `<h1>` বানিয়ে দিচ্ছে। এখন প্রত্যেক `Section` থেকে এর নিজের কনটেক্সট provide করে চলুন এর সমাধান করা যাক।

### ধাপ ৩: কনটেক্সটটি provide করুন {/*step-3-provide-the-context*/}

`Section` কম্পোনেন্টটি এর চিলড্রেনকে রেন্ডার করছে:

```js
export default function Section({ children }) {
  return (
    <section className="section">
      {children}
    </section>
  );
}
```

**চিলড্রেনকে context provider দিয়ে wrap করুন** যাতে তাদেরকে `LevelContext` টি provide করতে পারেন:

```js {1,6,8}
import { LevelContext } from './LevelContext.js';

export default function Section({ level, children }) {
  return (
    <section className="section">
      <LevelContext value={level}>
        {children}
      </LevelContext>
    </section>
  );
}
```

<<<<<<< HEAD
এটা React কে বলে দেয় যে: "যদি এই `<Section>` এর ভিতরের কোনো কম্পোনেন্ট `LevelContext` তালাশ করে, তবে তাকে এই `level` দিয়ে দাও"। তখন কম্পোনেন্টটি UI ট্রি এর ভিতর এর সবচেয়ে কাছের `<LevelContext.Provider>` এর ভ্যালু ইউজ করবে।
=======
This tells React: "if any component inside this `<Section>` asks for `LevelContext`, give them this `level`." The component will use the value of the nearest `<LevelContext>` in the UI tree above it.
>>>>>>> 2859efa07357dfc2927517ce9765515acf903c7c

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section level={1}>
      <Heading>Title</Heading>
      <Section level={2}>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Heading>Heading</Heading>
        <Section level={3}>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Heading>Sub-heading</Heading>
          <Section level={4}>
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
import { LevelContext } from './LevelContext.js';

export default function Section({ level, children }) {
  return (
    <section className="section">
      <LevelContext value={level}>
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

export const LevelContext = createContext(1);
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

ফলস্বরূপ আমরা অরিজিনাল কোডের মতো হুবহু ফলাফল পেলাম, কিন্তু আপনার `level` প্রপটিকে প্রত্যেক `Heading` কম্পোনেন্টে পাস করতে হয়নি! তার পরিবর্তে `Heading` কম্পোনেন্টটি এর হেডিং লেভেল, উপরস্থ সবচেয়ে কাছের `Section` থেকে "বুঝে নিতে" পারছে:

<<<<<<< HEAD
1. আপনি `<Section>` কে `level` প্রপ পাস করলেন।
2. `Section` এর চিলড্রেনকে `<LevelContext.Provider value={level}>` দিয়ে wrap করে নেয়।
3. `useContext(LevelContext)` এর দ্বারা `Heading` এর উপরস্থ নিকটতম `levelContext` এর ভ্যালু তালাশ করে।
=======
1. You pass a `level` prop to the `<Section>`.
2. `Section` wraps its children into `<LevelContext value={level}>`.
3. `Heading` asks the closest value of `LevelContext` above with `useContext(LevelContext)`.
>>>>>>> 2859efa07357dfc2927517ce9765515acf903c7c

## একই কম্পোনেন্ট থেকে কনটেক্সট Use এবং Provide করা {/*using-and-providing-context-from-the-same-component*/}

এখনও, প্রত্যেক সেকশনের `level` আপনি নিজেই নির্ধারিত করে দেয়া লাগছে:

```js
export default function Page() {
  return (
    <Section level={1}>
      ...
      <Section level={2}>
        ...
        <Section level={3}>
          ...
```

যেহেতু কনটেক্সট উপরের একটি কম্পোনেন্ট থেকে আপনাকে ইনফর্মেশন read করতে দেয়, সেহেতু প্রত্যেক `Section` তার উপরের `Section` থেকে `level` কে read করতে, এবং নিচে `level + 1` অটোম্যাটিক ভাবে পাস করে দিতে পারে। আপনি চাইলে এমনটা এভাবে করে ফেলতে পারেন:

```js src/Section.js {5,8}
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

এই পরিবর্তনের কারণে, আপনার `level` প্রপটিকে `<Section>` কিংবা `<Heading>` *কোনোটিকেই* পাস করা লাগবে না:

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

এখন `Heading` এবং `Section` তারা উভয়ই কতো "deep" লেভেলে আছে সেটা বুঝার জন্য `LevelContext` কে read করছে। এবং `Section` এর ভিতরে যা আছে তা "deeper" লেভেল আছে এটা স্পষ্ট করার জন্য, এর চিলড্রেনকে `LevelContext` দিয়ে wrap করে।

<Note>

এই উদাহরণটি হেডিং লেভেলের ব্যবহার করেছে কারণ এতে দেখা যায় যে নেস্টেড কম্পোনেন্ট কিভাবে কনটেক্সটের ভ্যালু পুনরায় পরিবর্তন করে দিতে পরে। কিন্তু কনটেক্সট আরো অনেক ক্ষেত্রেও কাজে আসে। অ্যাপের নিম্নস্থ subtree এর প্রয়োজন এমন যেকোনো ইনফর্মেশন আপনি নিচের দিকে পাস করে দিতে পারেন: বর্তমান কালার থিম, বর্তমানে যে ইউজার লগড ইন ইত্যাদি।

</Note>

## কনটেক্সট মধ্যবর্তী কম্পোনেন্টগুলোকে ভেদ করে যেতে সক্ষম {/*context-passes-through-intermediate-components*/}

যে কম্পোনেন্টটি কনটেক্সটকে provide করে আর যে কম্পোনেন্টটি use করে উভয়ের মাঝে আপনি যত খুশি তত কম্পোনেন্ট বসাতে পারবেন। এদের মাঝে আপনি বিল্ট-ইন কম্পোনেন্ট যেমন `div` এবং আপনার নিজের বানানো কম্পোনেন্ট উভয়ই ব্যবহার করতে পারবেন।

এই উদাহরণে, একই `Post` কম্পোনেন্ট (ড্যাশড বর্ডারওয়ালা) দুইটা ভিন্ন ভিন্ন নেস্টেড লেভেলে রেন্ডার হচ্ছে। খেয়াল করুন এর ভিতরের `<Heading>` তার লেভেল, অটোমেটিক্যালি নিকটতম `<Section>` থেকে পাচ্ছে:

<Sandpack>

```js
import Heading from './Heading.js';
import Section from './Section.js';

export default function ProfilePage() {
  return (
    <Section>
      <Heading>My Profile</Heading>
      <Post
        title="Hello traveller!"
        body="Read about my adventures."
      />
      <AllPosts />
    </Section>
  );
}

function AllPosts() {
  return (
    <Section>
      <Heading>Posts</Heading>
      <RecentPosts />
    </Section>
  );
}

function RecentPosts() {
  return (
    <Section>
      <Heading>Recent Posts</Heading>
      <Post
        title="Flavors of Lisbon"
        body="...those pastéis de nata!"
      />
      <Post
        title="Buenos Aires in the rhythm of tango"
        body="I loved it!"
      />
    </Section>
  );
}

function Post({ title, body }) {
  return (
    <Section isFancy={true}>
      <Heading>
        {title}
      </Heading>
      <p><i>{body}</i></p>
    </Section>
  );
}
```

```js src/Section.js
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Section({ children, isFancy }) {
  const level = useContext(LevelContext);
  return (
    <section className={
      'section ' +
      (isFancy ? 'fancy' : '')
    }>
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

.fancy {
  border: 4px dashed pink;
}
```

</Sandpack>

এটা কাজ করানোর জন্য আপনার বিশেষ কিছু করা লাগেনি। একটি `Section` এর নিম্নস্থ ট্রির জন্য কনটেক্সট নির্ধারিত করে দেয়, তাই আপনি একটি `<Heading>` যেকোনো জায়গায় বসাতে পারবেন, আর এটি এর সঠিক সাইজ পেয়ে যাবে। উপরের স্যান্ডবক্সে চর্চা করে দেখুন!

**কনটেক্সট আপনাকে এমন কম্পোনেন্ট তৈরি করতে দেয়, যা তার "আসে পাশের সাথে তাল মিলিয়ে চলতে পারে" এবং সেটি  _কোথায়_ (অন্যভাবে বলতে গেলে, _কোন কনটেক্সটে_) রেন্ডার হচ্ছে, তার উপর নির্ভর করে নিজেকে ভিন্ন ভিন্ন ভাবে ডিসপ্লে করতে পারে।**

কনটেক্সটের কাজ করার পদ্ধতি আপনাকে [CSS property inheritance](https://developer.mozilla.org/en-US/docs/Web/CSS/inheritance) এর কথা মনে করিয়ে দিতে পারে, আপনি একটা `<div>` এর জন্য `color: blue` ঠিক করে দিবেন, তাহলে এর ভিতরের যেকোনো DOM node, তা যত গভীরেই হোক না কেনো, সেটি ঐ কালার পাবে যদিনা মাঝের অন্য কোনো DOM node কালারকে পরিবর্তন করে `color: green` বানিয়ে দেয়। একইভাবে, React এ, উপর থেকে আসতে থাকা কোনো কনটেক্সটকে পরিবর্তন করার একমাত্র উপায় হচ্ছে চিলড্রেনকে ভিন্ন একটি ভ্যালুর context provider দিয়ে wrap করা।

CSS এ, ভিন্ন ভিন্ন property যেমন `color` এবং `background-color` একে অপরকে পরিবর্তন করে না। আপনি সকল `<div>` এর `color` কে red সেট করে দিলে সেটা `background-color` এর উপর কোনো প্রভাব পড়বে না। একইভাবে, **ভিন্ন ভিন্ন React কনটেক্সট একে অপরকে পরিবর্তন করে না।** আপনার `createContext()` দিয়ে তৈরি করা প্রত্যেক কনটেক্সট বাকি সকল কনটেক্সটগুলো থেকে পুরোপুরি বিচ্ছিন্ন, *এবং ঐ বিশেষ কনটেক্সটটি* use এবং provide করার দ্বারা কম্পোনেন্টসমূহ ঘিরে থাকে। একটি কম্পোনেন্ট একাধিক ভিন্ন ভিন্ন কনটেক্সট কোনো সমস্যা ছাড়াই use এবং provide করতে পারে।

## কনটেক্সট ব্যবহারের পূর্বে যা জানা থাকা দরকার {/*before-you-use-context*/}

কনটেক্সট ব্যবহার অনেক লোভনীয় মনে হতে পারে! তবে বুঝতে হবে, এটাকে খুব অতিরিক্ত মাত্রায় ব্যবহার করা খুব সহজ। **শুধু কয়েক লেভেল গভীরে আপনার কিছু প্রপস পাস করতে হবে তাহলেই যে আপনার এই ইনফর্মেশন কনটেক্সট এ রাখতে হবে এমনটি নয়।**

কিছু বিকল্প রয়েছে যেগুলো কনটেক্সট ব্যবহারের পূর্বে বিবেচনা করা উচিৎ:

1. **[প্রপস পাস করা](/learn/passing-props-to-a-component) শুরু করতে পারেন** যদি আপনার কম্পোনেন্টগুলো মামুলি না হয়ে থাকে (মানে সেটি গুরুত্ব বহন করে), তাহলে ডজন খানিক প্রপসকে ডজন খানিক কম্পোনেন্টের মধ্যে দিয়ে পাস করা অস্বাভাবিক নয়। এটি অনেক সময়সাপেক্ষ কঠিন কাজ মানে হতে পারে, কিন্তু এই পদ্ধতিতে কোন কম্পোনেন্ট কোন ডেটা ইউজ করছে সেটি খুব পরিষ্কার হয়ে যায়! যে ব্যক্তি আপনার কোড মেইন্টেইন করবে সে আপনার ডেটা-প্রবাহ প্রপসের মাধ্যমে প্রকাশ্য রাখার জন্য বেশ খুশি হবে।
2. **কম্পোনেন্টগুলোকে এক্সট্র্যাক্ট (আলাদা) করে নিয়ে [JSX কে `children` হিসেবে পাস করতে পারেন](/learn/passing-props-to-a-component#passing-jsx-as-children)।** যদি আপনি কিছু ডেটা অনেক স্তরের মধ্যে দিয়ে মাঝের এমন অনেক কম্পোনেন্ট ভেদ করে পাস করেন (শুধুমাত্র ডেটাকে অনেক নিচে পাঠানোর উদ্দেশ্যে) যেসব কম্পোনেন্টের ঐ ডেটার প্রয়োজন নেই, প্রায়ই এর মানে এই যে আপনি মাঝের পথের কিছু কম্পোনেন্টকে আলাদা (এক্সট্র্যাক্ট) করতে ভুলে গেছেন। উদাহরণস্বরূপ, হয়তো আপনি ডেটা প্রপ যেমন `posts` এমন দৃশ্যমান কম্পোনেন্টসমূহকে পাস করেছেন যারা সে ডেটা সরাসরি ইউজ করে না, যেমন `<Layout posts={posts} />`। এর পরিবর্তে, `Layout` কে এমন করে দিন যাতে প্রপ হিসেবে `children` কে গ্রহণ করতে পারে, এবং রেন্ডার করে `<Layout><Posts posts={posts} /></Layout>`। এটা ডেটা নির্ধারণকারী কম্পোনেন্ট এবং যে কম্পোনেন্টগুলো ডেটা গ্রহণ করবে তাদের মধ্যবর্তী স্তরের সংখ্যা কমায়।

এই উভয় পদ্ধতিই যদি আপনার কাছে ঠিক না মনে হয় তাহলে কনটেক্সট ব্যবহার নিয়ে ভেবে দেখতে পারেন। 

## কনটেক্সটের ব্যবহার {/*use-cases-for-context*/}

* **থিমিং:** যদি আপনার অ্যাপ ইউজারকে থিম চেঞ্জ করতে দেয় (যেমন, ডার্ক মোড), তখন আপনি আপনার অ্যাপের সবার উপরের স্তরে একটি কনটেক্সট প্রোভাইডার রাখতে পারেন, এবং কনটেক্সটটি ঐসকল কম্পোনেন্টের ভিতর ইউজ করতে পারেন যাদের বর্ণ থিমের সাথে পরিবর্তন হতে পারে। 
* **বর্তমান একাউন্ট:** বর্তমানে কোন ইউজার লগড ইন আছে তা অনেক কম্পোনেন্টের জানা দরকার হতে পারে। এই ডেটটি কনটেক্সটি রাখলে tree এর যেকোনো স্থানে ডেটটি রিড করা সুবিধাজনক হয়ে যায়। কিছু অ্যাপ আপনাকে একই সময়ে কয়েকটি একাউন্ট ব্যবহার করতে দেয় (যেমন, আরেক ইউজার হয়ে কমেন্ট করতে দেয়া)। ঐসকল ক্ষেত্রে, UI এর একটি অংশ, ভিন্ন current account ভ্যালুর একটি নেস্টেড প্রোভাইডার দিয়ে wrap করাটা সহজ হতে পারে।
* **রাউটিং:** অধিকাংশ রাউটিং করার পদ্ধতিগুলো, বর্তমান রাউট মনে রাখার জন্য ভিতরে ভিতরে কনটেক্সট ইউজ করে। এভাবেই প্রত্যেকটি লিঙ্ক "জানতে পারে" যে সে active কিনা। যদি আপনি নিজেই রাউটার তৈরি করেন, আপনিও হয়তো এমনটাই করতে চাইবেন।
* **স্টেট ম্যানেজ করা:** আপনার অ্যাপ যখন বড় হতে থাকে, আপনি এমন পর্যায়ে চলে যেতে পারেন যখন আপনার অ্যাপের সবার উপরের স্তরের খুব কাছেই অনেক স্টেট একত্র হয়ে যায়। যেগুলো নিচের অনেক দূরবর্তী কম্পোনেন্ট পরিবর্তন করার প্রয়োজন পড়তে পারে। বেশি ঝামেলা ছাড়াই, জটিল স্টেট ম্যানেজ এবং সেগুলোকে নিচের অনেক দূরবর্তী কম্পোনেন্টসের কাছে পাস করার জন্য [একটি reducer কে কনটেক্সটের সাথে ব্যবহার করা](/learn/scaling-up-with-reducer-and-context) খুবই স্বাভাবিক।
  
কনটেক্সট শুধু স্ট্যাটিক ভ্যালুর মধ্যেই সীমাবদ্ধ নয়। যদি আপনি পরবর্তী রেন্ডারে ভিন্ন ভ্যালু পাস করেন, React তখন এর নিম্নবর্তী সকল কম্পোনেন্ট যেগুলো ঐ ভ্যালু রিড করছিলো তাদেরকে আপডেট করবে! এজন্যই প্রায়ই কনটেক্সট ও স্টেট একত্রে ব্যবহার করা হয়ে থাকে।

সাধারণভাবে, যদি কোনো ইনফর্মেশন tree এর বিভিন্ন অংশে দূরবর্তী কম্পোনেন্টগুলোর প্রয়োজন হয়, তাহলে কনটেক্সট তখন আপনার উপকারে আসবে এটি তার উত্তম লক্ষণ।

<Recap>

<<<<<<< HEAD
* কনটেক্সট একটি কম্পোনেন্টকে এর নিম্নস্থ পুরো ট্রি কে কিছু ইনফর্মেশন প্রোভাইড করতে দেয়।
* কনটেক্সট পাস করতে হলে:
  1. `export const MyContext = createContext(defaultValue)` দিয়ে কনটেক্সট create করে export করুন।
  2. `useContext(MyContext)` হুককে কনটেক্সটটি পাস করুন যাতে যেকোনো চাইল্ড কম্পোনেন্ট থেকে সেটিকে read করা যায়, তা যত গভীরেই হোক না কেনো।
  3. চিলড্রেনকে `<MyContext.Provider value={...}>` দিয়ে wrap করুন যাতে একটি প্যারেন্ট থেকে কনটেক্সটটি প্রোভাইড করতে পারেন।
* কনটেক্সট মধ্যবর্তী যেকোনো কম্পোনেন্ট ভেদ করে যেতে পারে।
* কনটেক্সট আপনাকে এমন কম্পোনেন্ট তৈরি করতে দেয় যেগুলো "তাদের আসে পাশের সাথে তাল মিলিয়ে চলতে পারে"।
* কনটেক্সট ব্যবহার করার আগে, চেষ্টা করুন প্রপস পাস করতে বা JSX কে `children` হিসেবে পাস করতে।
=======
* Context lets a component provide some information to the entire tree below it.
* To pass context:
  1. Create and export it with `export const MyContext = createContext(defaultValue)`.
  2. Pass it to the `useContext(MyContext)` Hook to read it in any child component, no matter how deep.
  3. Wrap children into `<MyContext value={...}>` to provide it from a parent.
* Context passes through any components in the middle.
* Context lets you write components that "adapt to their surroundings".
* Before you use context, try passing props or passing JSX as `children`.
>>>>>>> 2859efa07357dfc2927517ce9765515acf903c7c

</Recap>

<Challenges>

#### প্রপ ড্রিলিং এর পরিবর্তে কনটেক্সট ব্যবহার করুন {/*replace-prop-drilling-with-context*/}

এই উদাহরণে, চেকবক্সটি toggle করলে `imageSize` প্রপকে পরিবর্তন হয়, যেটিকে প্রত্যেক `<PlaceImage>` এ পাস করা হয়েছে। চেকবক্সের স্টেটটি `App` কম্পোনেন্টে সবার উপরে আছে, কিন্তু প্রত্যেক `<PlaceImage>` এর এই স্টেট সম্পর্কে জানা প্রয়োজন।

বর্তমানে, `imageSize` স্টেটটি `App` থেকে `List` এ পাস হচ্ছে, সেখান থেকে আবার প্রত্যেক `Place` এ পাস হচ্ছে, সেখান থেকে আবার `PlaceImage` এ পাস হচ্ছে। এখন `imageSize` প্রপটিকে রিমুভ করে দিন, আর এর বদলে একে `App` কম্পোনেন্ট থেকে সরাসরি `PlaceImage` এ পাস করুন।

আপনি কনটেক্সটটি `Context.js` এ declare করতে পারেন।

<Sandpack>

```js src/App.js
import { useState } from 'react';
import { places } from './data.js';
import { getImageUrl } from './utils.js';

export default function App() {
  const [isLarge, setIsLarge] = useState(false);
  const imageSize = isLarge ? 150 : 100;
  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={isLarge}
          onChange={e => {
            setIsLarge(e.target.checked);
          }}
        />
        Use large images
      </label>
      <hr />
      <List imageSize={imageSize} />
    </>
  )
}

function List({ imageSize }) {
  const listItems = places.map(place =>
    <li key={place.id}>
      <Place
        place={place}
        imageSize={imageSize}
      />
    </li>
  );
  return <ul>{listItems}</ul>;
}

function Place({ place, imageSize }) {
  return (
    <>
      <PlaceImage
        place={place}
        imageSize={imageSize}
      />
      <p>
        <b>{place.name}</b>
        {': ' + place.description}
      </p>
    </>
  );
}

function PlaceImage({ place, imageSize }) {
  return (
    <img
      src={getImageUrl(place)}
      alt={place.name}
      width={imageSize}
      height={imageSize}
    />
  );
}
```

```js src/Context.js

```

```js src/data.js
export const places = [{
  id: 0,
  name: 'Bo-Kaap in Cape Town, South Africa',
  description: 'The tradition of choosing bright colors for houses began in the late 20th century.',
  imageId: 'K9HVAGH'
}, {
  id: 1, 
  name: 'Rainbow Village in Taichung, Taiwan',
  description: 'To save the houses from demolition, Huang Yung-Fu, a local resident, painted all 1,200 of them in 1924.',
  imageId: '9EAYZrt'
}, {
  id: 2, 
  name: 'Macromural de Pachuca, Mexico',
  description: 'One of the largest murals in the world covering homes in a hillside neighborhood.',
  imageId: 'DgXHVwu'
}, {
  id: 3, 
  name: 'Selarón Staircase in Rio de Janeiro, Brazil',
  description: 'This landmark was created by Jorge Selarón, a Chilean-born artist, as a "tribute to the Brazilian people."',
  imageId: 'aeO3rpI'
}, {
  id: 4, 
  name: 'Burano, Italy',
  description: 'The houses are painted following a specific color system dating back to 16th century.',
  imageId: 'kxsph5C'
}, {
  id: 5, 
  name: 'Chefchaouen, Marocco',
  description: 'There are a few theories on why the houses are painted blue, including that the color repels mosquitos or that it symbolizes sky and heaven.',
  imageId: 'rTqKo46'
}, {
  id: 6,
  name: 'Gamcheon Culture Village in Busan, South Korea',
  description: 'In 2009, the village was converted into a cultural hub by painting the houses and featuring exhibitions and art installations.',
  imageId: 'ZfQOOzf'
}];
```

```js src/utils.js
export function getImageUrl(place) {
  return (
    'https://i.imgur.com/' +
    place.imageId +
    'l.jpg'
  );
}
```

```css
ul { list-style-type: none; padding: 0px 10px; }
li { 
  margin-bottom: 10px; 
  display: grid; 
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
```

</Sandpack>

<Solution>

Remove `imageSize` prop from all the components.

Create and export `ImageSizeContext` from `Context.js`. Then wrap the List into `<ImageSizeContext value={imageSize}>` to pass the value down, and `useContext(ImageSizeContext)` to read it in the `PlaceImage`:

<Sandpack>

```js src/App.js
import { useState, useContext } from 'react';
import { places } from './data.js';
import { getImageUrl } from './utils.js';
import { ImageSizeContext } from './Context.js';

export default function App() {
  const [isLarge, setIsLarge] = useState(false);
  const imageSize = isLarge ? 150 : 100;
  return (
    <ImageSizeContext
      value={imageSize}
    >
      <label>
        <input
          type="checkbox"
          checked={isLarge}
          onChange={e => {
            setIsLarge(e.target.checked);
          }}
        />
        Use large images
      </label>
      <hr />
      <List />
    </ImageSizeContext>
  )
}

function List() {
  const listItems = places.map(place =>
    <li key={place.id}>
      <Place place={place} />
    </li>
  );
  return <ul>{listItems}</ul>;
}

function Place({ place }) {
  return (
    <>
      <PlaceImage place={place} />
      <p>
        <b>{place.name}</b>
        {': ' + place.description}
      </p>
    </>
  );
}

function PlaceImage({ place }) {
  const imageSize = useContext(ImageSizeContext);
  return (
    <img
      src={getImageUrl(place)}
      alt={place.name}
      width={imageSize}
      height={imageSize}
    />
  );
}
```

```js src/Context.js
import { createContext } from 'react';

export const ImageSizeContext = createContext(500);
```

```js src/data.js
export const places = [{
  id: 0,
  name: 'Bo-Kaap in Cape Town, South Africa',
  description: 'The tradition of choosing bright colors for houses began in the late 20th century.',
  imageId: 'K9HVAGH'
}, {
  id: 1, 
  name: 'Rainbow Village in Taichung, Taiwan',
  description: 'To save the houses from demolition, Huang Yung-Fu, a local resident, painted all 1,200 of them in 1924.',
  imageId: '9EAYZrt'
}, {
  id: 2, 
  name: 'Macromural de Pachuca, Mexico',
  description: 'One of the largest murals in the world covering homes in a hillside neighborhood.',
  imageId: 'DgXHVwu'
}, {
  id: 3, 
  name: 'Selarón Staircase in Rio de Janeiro, Brazil',
  description: 'This landmark was created by Jorge Selarón, a Chilean-born artist, as a "tribute to the Brazilian people".',
  imageId: 'aeO3rpI'
}, {
  id: 4, 
  name: 'Burano, Italy',
  description: 'The houses are painted following a specific color system dating back to 16th century.',
  imageId: 'kxsph5C'
}, {
  id: 5, 
  name: 'Chefchaouen, Marocco',
  description: 'There are a few theories on why the houses are painted blue, including that the color repels mosquitos or that it symbolizes sky and heaven.',
  imageId: 'rTqKo46'
}, {
  id: 6,
  name: 'Gamcheon Culture Village in Busan, South Korea',
  description: 'In 2009, the village was converted into a cultural hub by painting the houses and featuring exhibitions and art installations.',
  imageId: 'ZfQOOzf'
}];
```

```js src/utils.js
export function getImageUrl(place) {
  return (
    'https://i.imgur.com/' +
    place.imageId +
    'l.jpg'
  );
}
```

```css
ul { list-style-type: none; padding: 0px 10px; }
li { 
  margin-bottom: 10px; 
  display: grid; 
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
```

</Sandpack>

Note how components in the middle don't need to pass `imageSize` anymore.

</Solution>

</Challenges>
