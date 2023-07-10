---
title: State সংরক্ষণ এবং রিসেট করা
---

<Intro>

State কম্পোনেন্টের মধ্যে ভিন্ন রাখা হয়। React একটি UI ট্রি এর ভিতরে কোন State কোন কম্পোনেন্টের সঙ্গে সংযোগস্থলে পরিচয় রেখে থাকে। আপনি নিয়ন্ত্রণ করতে পারেন যখন অবস্থা সংরক্ষণ করতে হবে এবং যখন রি-রেন্ডার এর মধ্যে তা পুনরায় সেট করতে হবে। 

</Intro>

<YouWillLearn>

* React কীভাবে কম্পোনেন্ট স্ট্রাকচারগুলি "দেখে"
* React কোন সময়ে State সংরক্ষণ বা রিসেট করতে সিলেক্ট করে
* React এ কীভাবে কম্পোনেন্টের অবস্থা রিসেট করতে বাধ্য করা যায়
* React এ State সংরক্ষণ কি ভাবে প্রভাহিত হয় কীস (keys) এবং প্রকার (types) এর জন্য

</YouWillLearn>

## UI ট্রি {/*the-ui-tree*/}

ব্রাউজার অনেক ধরনের ট্রি কাঠামো ব্যবহার করে থাকে UI মডেল করার জন্য। [DOM](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) HTML উপাদানগুলি প্রতিষ্ঠা করে, [CSSOM](https://developer.mozilla.org/docs/Web/API/CSS_Object_Model) একইভাবে CSS এর জন্য করে। এখানে [Accessibility tree](https://developer.mozilla.org/docs/Glossary/Accessibility_tree) নামক ট্রি আছে।

React সাধারণতঃ UI গড়ে তুলতে এবং পরিচালনা করতে ট্রি স্ট্রাকচার ব্যবহার করে। React JSX থেকে UI ট্রি তৈরি করে। তারপরে React DOM ব্রাউজারের DOM উপাদানগুলি আপডেট করে যাতে সেই UI ট্রির সাথে মিল খায়। (React Native এই ট্রিগুলি মোবাইল প্ল্যাটফর্মের উপাদানগুলির জন্য প্রতিষ্ঠান করে।)।

<DiagramGroup>

<Diagram name="preserving_state_dom_tree" height={193} width={864} alt="Diagram with three sections arranged horizontally. In the first section, there are three rectangles stacked vertically, with labels 'Component A', 'Component B', and 'Component C'. Transitioning to the next pane is an arrow with the React logo on top labeled 'React'. The middle section contains a tree of components, with the root labeled 'A' and two children labeled 'B' and 'C'. The next section is again transitioned using an arrow with the React logo on top labeled 'React'. The third and final section is a wireframe of a browser, containing a tree of 8 nodes, which has only a subset highlighted (indicating the subtree from the middle section).">

কম্পোনেন্ট থেকে শুরু করে, React একটি UI ট্রি তৈরি করে যা React DOM ব্যবহার করে ডম রেন্ডার করতে।
</Diagram>

</DiagramGroup>

## State ট্রির একটি অবস্থানের সাথে বাঁধা রয়েছে {/*state-is-tied-to-a-position-in-the-tree*/}

যখন আপনি কোন কম্পোনেন্টকে স্টেট দিন, আপনি সম্প্রতি মনে করতে পারেন যে স্টেটটি কেবলমাত্র কম্পোনেন্টের "ভিতরে" বসে থাকে। কিন্তু স্টেটটি প্রদত্তক্ষেত্রে বাস করে যেমন রিয়েক্টে। রিয়েক্ট প্রতিটি স্টেট টুকরা সঠিক কম্পোনেন্ট সঙ্গে যুক্ত করে রাখে যেখানে ঐ কম্পোনেন্টটি UI ট্রি এর মধ্যে অবস্থান করে।


এখানে, কেবলমাত্র একটি  `<Counter />`  JSX ট্যাগ আছে, কিন্তু এটি দুটি জায়গায় রেন্ডার হয়েছে:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const counter = <Counter />;
  return (
    <div>
      {counter}
      {counter}
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

এখানে এগুলি কিভাবে ট্রি হিসেবে দেখায়:    

<DiagramGroup>

<Diagram name="preserving_state_tree" height={248} width={395} alt="Diagram of a tree of React components. The root node is labeled 'div' and has two children. Each of the children are labeled 'Counter' and both contain a state bubble labeled 'count' with value 0.">

React ট্রি

</Diagram>

</DiagramGroup>

**এগুলি দুটি আলাদা কাউন্টার কারণ প্রতিটি নিজস্ব অবস্থানে ট্রি এর মধ্যে রেন্ডার করা হয়েছে।** আপনাকে সাধারণতঃ রিয়েক্ট ব্যবহার করার সময় এই অবস্থানগুলি চিন্তা করতে হয় না, তবে কিভাবে এটি কার্য করে তা বোঝার জন্য এটা কার্যকর হতে পারে।

React-এ, প্রদর্শিত প্রতিটি কম্পোনেন্টের জন্য একটি সম্পূর্ণ আলাদা State আছে। উদাহরণস্বরূপ, যদি আপনি পাশাপাশি দুটি `Counter` কম্পোনেন্টকে রেন্ডার করেন, তখন প্রতিটি কম্পোনেন্ট নিজস্ব, স্বতন্ত্র, `score` এবং `hover` states পাওয়া যাবে। 

দুটি কাউন্টার একই সাথে ক্লিক করুন এবং দেখুন যে তারা একে অপরকে প্রভাবিত করে না:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  return (
    <div>
      <Counter />
      <Counter />
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

যেমন আপনি দেখতে পাচ্ছেন, একটি কাউন্টার আপডেট করা হলে, শুধুমাত্র সেই কম্পোনেন্টের স্টেটটি আপডেট হয়ছে:


<DiagramGroup>

<Diagram name="preserving_state_increment" height={248} width={441} alt="Diagram of a tree of React components. The root node is labeled 'div' and has two children. The left child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The right child is labeled 'Counter' and contains a state bubble labeled 'count' with value 1. The state bubble of the right child is highlighted in yellow to indicate its value has updated.">

স্টেট আপডেট করা

</Diagram>

</DiagramGroup>


React ততোক্ষণ State ধরে রাকবে যতক্ষণ একই পজিশনে একই কম্পোনেন্ট রেন্ডার করা হচ্ছে। এটা দেখতে, দুটি কাউন্টারের মান বাড়ানোর পর দ্বিতীয় কম্পোনেন্টটি সরানোর জন্য "দ্বিতীয় কাউন্টার রেন্ডার করুন" চেকবক্স আনচেক করুন, এবং তারপরে আবার সেইটি যুক্ত করতে আবার চেক করুন।

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [showB, setShowB] = useState(true);
  return (
    <div>
      <Counter />
      {showB && <Counter />} 
      <label>
        <input
          type="checkbox"
          checked={showB}
          onChange={e => {
            setShowB(e.target.checked)
          }}
        />
        Render the second counter
      </label>
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

লক্ষ্য করুন যে মুহূর্তে আপনি দ্বিতীয় কাউন্টার রেন্ডার করা বন্ধ করেন, তখন ঐ কাউন্টারের স্টেট সম্পূর্ণভাবে অদৃশ্য হয়ে যায়। এটা হওয়ার কারণ React কম্পোনেন্টটি সরানোর সময় তার স্টেট ধ্বংস করে।

<DiagramGroup>

<Diagram name="preserving_state_remove_component" height={253} width={422} alt="Diagram of a tree of React components. The root node is labeled 'div' and has two children. The left child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The right child is missing, and in its place is a yellow 'poof' image, highlighting the component being deleted from the tree.">

কোম্পোনেন্টটি মুছে ফেলা হল

</Diagram>

</DiagramGroup>
যখন আপনি "দ্বিতীয় কাউন্টার রেন্ডার করুন" টিক চিহ্নতে টিক করেন, তখন একটি দ্বিতীয় `Counter` এবং এর স্টেটটি শূন্য থেকে শুরু করা হয়  (`score = 0`) এবং এটি DOM-এ যুক্ত করা হয়।

<DiagramGroup>

<Diagram name="preserving_state_add_component" height={258} width={500} alt="Diagram of a tree of React components. The root node is labeled 'div' and has two children. The left child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The right child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The entire right child node is highlighted in yellow, indicating that it was just added to the tree.">

কম্পোনেন্ট যোগ করা

</Diagram>

</DiagramGroup>

**React কম্পোনেন্টের State সংরক্ষণ করে রাখে ততক্ষণ পর্যন্ত যতোক্ষণ ঐ কম্পোনেন্টটি UI ট্রির মধ্যে নির্দিষ্ট অবস্থানে রেন্ডার হচ্ছে।** যদি কোনও কম্পোনেন্ট সরানো হয় অথবা একই অবস্থানে একটি অন্য কম্পোনেন্ট রেন্ডার করা হয়, তবে React তার স্টেটটি বাতিল করে ফেলে।


## একই কম্পোনেন্ট একই অবস্থানে থাকলে State সংরক্ষিত থাকে {/*same-component-at-the-same-position-preserves-state*/}

এই উদাহরণে, দুটি আলাদা `<Counter />` ট্যাগ আছে:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <Counter isFancy={true} /> 
      ) : (
        <Counter isFancy={false} /> 
      )}
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}

function Counter({ isFancy }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }
  if (isFancy) {
    className += ' fancy';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.fancy {
  border: 5px solid gold;
  color: #ff6767;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

যখন আপনি চেকবক্স টিক বা সাফ করার সময়, কাউন্টারের স্টেট রিসেট হয় না। `isFancy` যদি is `true` সত্য বা `false` মিথ্যা, আপনি সবসময় একটি `<Counter />` একটি প্রথম চাইল্‌ড্‌  হিসাবে পেতে পারেন যা রুট `App` কম্পোনেন্ট থেকে ফিরে আসা `div` থেকে প্রাপ্ত হয়েছে:

<DiagramGroup>

<Diagram name="preserving_state_same_component" height={461} width={600} alt="Diagram with two sections separated by an arrow transitioning between them. Each section contains a layout of components with a parent labeled 'App' containing a state bubble labeled isFancy. This component has one child labeled 'div', which leads to a prop bubble containing isFancy (highlighted in purple) passed down to the only child. The last child is labeled 'Counter' and contains a state bubble with label 'count' and value 3 in both diagrams. In the left section of the diagram, nothing is highlighted and the isFancy parent state value is false. In the right section of the diagram, the isFancy parent state value has changed to true and it is highlighted in yellow, and so is the props bubble below, which has also changed its isFancy value to true.">

`App` এর স্টেট আপডেট করলেও `Counter` রিসেট হয় না কারণ `Counter` একই অবস্থানেই থাকে।

</Diagram>

</DiagramGroup>


এটি একই কম্পোনেন্ট একই অবস্থানে তখন, সুতরাং React এর দৃষ্টিতে এটি একই কাউন্টার:

<Pitfall>


মনে রাখবেন, **React এর জন্য গুরুত্বপূর্ণ জেএসএক্স মার্কআপ নয়, বরং ইউআই ট্রির মধ্যে অবস্থানটি গুরুত্বপূর্ণ!** এই কম্পোনেন্টটি দুটি `return` ক্লজ আছে যেখানে `if` এর ভিতরে এবং বাইরে ভিন্ন সংখ্যক `<Counter />` জেএসএক্স ট্যাগ আছে:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  if (isFancy) {
    return (
      <div>
        <Counter isFancy={true} />
        <label>
          <input
            type="checkbox"
            checked={isFancy}
            onChange={e => {
              setIsFancy(e.target.checked)
            }}
          />
          Use fancy styling
        </label>
      </div>
    );
  }
  return (
    <div>
      <Counter isFancy={false} />
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}

function Counter({ isFancy }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }
  if (isFancy) {
    className += ' fancy';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.fancy {
  border: 5px solid gold;
  color: #ff6767;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>


আপনি যদি চেকবক্স টিক করার পরে স্টেটটি রিসেট হবার আশা করেন, তবে এটা হয় না! এটা কারণে, **এই দুটি `<Counter />` ট্যাগ একই অবস্থানে রেন্ডার হয়।** React জানে না আপনি কোন শর্তগুলি কোথায় রাখেন আপনার ফাংশনে। এটি শুধুমাত্র আপনি return করা ট্রি দেখতে পায়।

উভয় ক্ষেত্রেই `App` কম্পোনেন্ট `<div>` রিটার্ন করে যা `<Counter />` একটি প্রথম চাইল্‌ড্‌  হিসাবে। রিয়েক্ট এর দৃষ্টিতে, এই দুটি কাউন্টার একই "ঠিকানায়" আছে: এটি মূল রুটের প্রথম চাইল্‌ড্‌-এর প্রথম চাইল্‌ড্‌। এটি রিয়েক্টের পূর্ববর্তী এবং পরবর্তী রেন্ডার মধ্যে তাদের মিলাতে সমান করে। এটি আপনি যদি আপনার লজিক কেমন বিন্যাস করুন না কেন।


</Pitfall>

## একই অবস্থানে বিভিন্ন কম্পোনেন্টগুলি স্টেট রিসেট করে {/*different-components-at-the-same-position-reset-state*/}

এই উদাহরণে, চেকবক্স চিহ্নিত করলে `<Counter>` এর স্থানে `<p>` যুক্ত হবে:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [isPaused, setIsPaused] = useState(false);
  return (
    <div>
      {isPaused ? (
        <p>See you later!</p> 
      ) : (
        <Counter /> 
      )}
      <label>
        <input
          type="checkbox"
          checked={isPaused}
          onChange={e => {
            setIsPaused(e.target.checked)
          }}
        />
        Take a break
      </label>
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

এখানে, আপনি একই অবস্থানে বিভিন্ন ধরণের কম্পোনেন্ট পরিবর্তন করেন। প্রাথমিকভাবে, `<div>` এর প্রথম চাইল্‌ড্‌-টির মধ্যে একটি `Counter` ছিল। But when you swapped in a `p`, React removed the `Counter` from the UI tree and destroyed its state. কিন্তু p কে স্থানান্তরিত করার সময়, React কাউন্‌টারটিকে UI-ট্রি থেকে সরে নিয়েছে এবং এর স্টেট ধ্বংস করেছে।

<DiagramGroup>

<Diagram name="preserving_state_diff_pt1" height={290} width={753} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'div' with a single child labeled 'Counter' containing a state bubble labeled 'count' with value 3. The middle section has the same 'div' parent, but the child component has now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'p', highlighted in yellow.">

যখন `Counter` পরিবর্তন হয়ে `p` হয়, তখন `Counter` মুছে যায় এবং `p` যুক্ত হয়।

</Diagram>

</DiagramGroup>

<DiagramGroup>

<Diagram name="preserving_state_diff_pt2" height={290} width={753} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'p'. The middle section has the same 'div' parent, but the child component has now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'Counter' containing a state bubble labeled 'count' with value 0, highlighted in yellow.">

পুনরায় পরিবর্তন করার সময়, `p` মুছে যায় এবং `Counter` যুক্ত হয়।

</Diagram>

</DiagramGroup>

**যখন একই অবস্থানে একটি নতুন কম্পোনেন্ট রেন্ডার করা হয়, তখন এর সম্পূর্ণ উপপাদ্যের স্টেট রিসেট হয়।** এটি কার্যকর হওয়ার পদ্ধতি দেখতে একটি উদাহরণে, কাউন্টার সংখ্যা বাড়ান এবং তারপরে চেকবক্স চিহ্নিত করুন:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <div>
          <Counter isFancy={true} /> 
        </div>
      ) : (
        <section>
          <Counter isFancy={false} />
        </section>
      )}
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}

function Counter({ isFancy }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }
  if (isFancy) {
    className += ' fancy';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.fancy {
  border: 5px solid gold;
  color: #ff6767;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

চেকবক্স ক্লিক করার সময় কাউন্টারের স্টেট রিসেট হয়। যদিও আপনি একটি `Counter` রেন্ডার করেন, কিন্তু `div` এর প্রথম চাইল্‌ড্‌ পরিবর্তন হয়, যা একটি `section` হয়ে যায়। যখন DOM থেকে চাইল্‌ড্‌ `div` সরানো হয়, তখন তার নীচের সমস্ত ট্রি (যথায়থ `Counter` এবং এর স্টেটসহ) ধ্বংস হয়ে যায়।


<DiagramGroup>

<Diagram name="preserving_state_diff_same_pt1" height={350} width={794} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'div' with a single child labeled 'section', which has a single child labeled 'Counter' containing a state bubble labeled 'count' with value 3. The middle section has the same 'div' parent, but the child components have now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'div', highlighted in yellow, also with a new child labeled 'Counter' containing a state bubble labeled 'count' with value 0, all highlighted in yellow.">

`section` থেকে `div` এ পরিবর্তন হলে, `section` মুছে যায় এবং নতুন `div` যুক্ত হয়।

</Diagram>

</DiagramGroup>

<DiagramGroup>

<Diagram name="preserving_state_diff_same_pt2" height={350} width={794} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'div' with a single child labeled 'div', which has a single child labeled 'Counter' containing a state bubble labeled 'count' with value 0. The middle section has the same 'div' parent, but the child components have now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'section', highlighted in yellow, also with a new child labeled 'Counter' containing a state bubble labeled 'count' with value 0, all highlighted in yellow.">

পুনরায় পরিবর্তন করার সময়, `div` মুছে যায় এবং নতুন `section` যুক্ত হয়

</Diagram>

</DiagramGroup>

একটি নির্দিষ্ট নিয়ম হিসাবে ধরা যায় **যদি আপনি পুনরায় রেন্ডার মাধ্যমে স্টেটকে সংরক্ষণ রাখতে চান তবে আপনার ট্রির গঠনটি পুনরায় রেন্ডার থেকে একে অন্যকে "মিলানো" প্রয়োজন।** যদি গঠন ভিন্ন হয়, তবে স্টেটটি ধ্বংস করা হয় কারণ React একটি কম্পোনেন্টকে ট্রি থেকে সরানোর সময় স্টেট ধ্বংস করে।

<Pitfall>

এটা হলো কারণ আপনাকে কম্পোনেন্ট ফাংশনের সংজ্ঞা নেস্ট করা উচিত নয়।

এখানে, `MyTextField` কম্পোনেন্ট ফাংশনটি `MyComponent` এর মধ্যে সংজ্ঞায়িত করা হয়েছে:

<Sandpack>

```js
import { useState } from 'react';

export default function MyComponent() {
  const [counter, setCounter] = useState(0);

  function MyTextField() {
    const [text, setText] = useState('');

    return (
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
    );
  }

  return (
    <>
      <MyTextField />
      <button onClick={() => {
        setCounter(counter + 1)
      }}>Clicked {counter} times</button>
    </>
  );
}
```

</Sandpack>


বাটন ক্লিক প্রতিটি সময়ে, ইনপুট স্টেট মুছে যায়! এটি হলো কারণ প্রতিটি `MyComponent` রেন্ডারের জন্য একটি *ভিন্ন* `MyTextField` ফাংশন তৈরি হয়। আপনি একই অবস্থানে ভিন্ন কম্পোনেন্ট রেন্ডার করছেন, তাই React সমস্ত স্টেট রিসেট করে। এটি বাগ এবং কার্যক্ষমতা সমস্যার কারণ হয়। এই সমস্যা সমাধানের জন্য, **সবসময় কম্পোনেন্ট ফাংশনগুলি শীর্ষ-স্তরে ঘোষণা করুন এবং তাদের সংজ্ঞায়িতি নেস্ট না করুন।**


</Pitfall>

## একই অবস্থানে স্টেট রিসেট করা {/*resetting-state-at-the-same-position*/}

ডিফল্ট অবস্থানে থাকলে রিয়েক্ট কম্পোনেন্টের স্টেট সংরক্ষণ রাখে। সাধারণতঃ, এটি আপনি চান তাই এটি ডিফল্ট আচরণ হিসাবে সুসংগত। কিন্তু কখনও কখনও, আপনি কম্পোনেন্টের স্টেট রিসেট করতে চান হতে পারেন। একটি অ্যাপটি চিন্তা করুন যা দুটি খেলোয়াড় অপরপরের স্কোরগুলি পর্যালোচনা করতে দেয় প্রতিটি টার্নে:

<Sandpack>

```js
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter person="Taylor" />
      ) : (
        <Counter person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}

function Counter({ person }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{person}'s score: {score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
h1 {
  font-size: 18px;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

বর্তমানে, যখন আপনি খেলোয়াড় পরিবর্তন করেন, স্কোর সংরক্ষণ করা হয়। দুটি `Counter` একই অবস্থানে দেখা যায়, তাই রিয়েক্ট তাদের *একই* `Counter` হিসাবে ধরে নেয় যার প্রপার্টি `person` পরিবর্তন হয়েছে।

তবে ধারণাগতভাবে, এই অ্যাপে তাদের দুটি পৃথক কাউন্টার হওয়া উচিত। তারা ইউআইতে একই জায়গায় দেখা যাতে পারে, কিন্তু একটি টেলরের জন্য একটি কাউন্টার এবং অন্যটি সারাহের জন্য একটি কাউন্টার।

এখানে দুটি উপায় আছে যখন তাদের মধ্যে স্থানান্তর করা হয়, স্টেট রিসেট করতে:

1. বিভিন্ন অবস্থানে কম্পোনেন্টগুলি রেন্ডার করুন
2. প্রতিটি কম্পোনেন্টের জন্য একটি স্পষ্ট পরিচয় দিন সাধারিত করুন `key` দ্বারা


### অপশন ১: কম্পোনেন্টটি বিভিন্ন অবস্থানে রেন্ডার করা {/*option-1-rendering-a-component-in-different-positions*/}

যদি আপনি এই দুটি `Counter` কে স্বাধীন রাখতে চান, তবে আপনি তাদের দুটি ভিন্ন অবস্থানে রেন্ডার করতে পারেন:

<Sandpack>

```js
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA &&
        <Counter person="Taylor" />
      }
      {!isPlayerA &&
        <Counter person="Sarah" />
      }
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}

function Counter({ person }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{person}'s score: {score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
h1 {
  font-size: 18px;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

* প্রাথমিকভাবে, `isPlayerA` এ `true` হয়। সুতরাং, প্রথম অবস্থানে `Counter` স্টেট থাকে এবং দ্বিতীয়টি খালি থাকে।
* আপনি "Next Player" বাটনটি চাপানোর পরে প্রথম অবস্থানটি খালি হয়ে যায় কিন্তু দ্বিতীয় অবস্থানটি এখন একটি Counter ধারণ করে।

<DiagramGroup>

<Diagram name="preserving_state_diff_position_p1" height={375} width={504} alt="Diagram with a tree of React components. The parent is labeled 'Scoreboard' with a state bubble labeled isPlayerA with value 'true'. The only child, arranged to the left, is labeled Counter with a state bubble labeled 'count' and value 0. All of the left child is highlighted in yellow, indicating it was added.">

প্রাথমিক স্টেট

</Diagram>

<Diagram name="preserving_state_diff_position_p2" height={375} width={504} alt="Diagram with a tree of React components. The parent is labeled 'Scoreboard' with a state bubble labeled isPlayerA with value 'false'. The state bubble is highlighted in yellow, indicating that it has changed. The left child is replaced with a yellow 'poof' image indicating that it has been deleted and there is a new child on the right, highlighted in yellow indicating that it was added. The new child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0.">

"next" বাটনটি চাপালে

</Diagram>

<Diagram name="preserving_state_diff_position_p3" height={375} width={504} alt="Diagram with a tree of React components. The parent is labeled 'Scoreboard' with a state bubble labeled isPlayerA with value 'true'. The state bubble is highlighted in yellow, indicating that it has changed. There is a new child on the left, highlighted in yellow indicating that it was added. The new child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The right child is replaced with a yellow 'poof' image indicating that it has been deleted.">

আবার "next" বাটনটি চাপালে

</Diagram>

</DiagramGroup>

প্রতিটি `Counter` এর স্টেট প্রতিবার DOM থেকে অপসারিত করা হলে ধ্বংস হয়ে যায়। এটি হচ্ছে কেন তারা প্রতিবার আপনি বাটনটি চাপার সময় রিসেট হয়।

এই সমাধানটি সুবিধাজনক যখন আপনার একই স্থানে কয়েকটি স্বতন্ত্র কম্পোনেন্ট রেন্ডার করা হয়। এই উদাহরণে আপনার কেবল দুটি কম্পোনেন্ট আছে, তাই JSX এ উভয়কে পৃথক ভাবে রেন্ডার করা একটি ঝামেলা নয়।

### Option 2: Resetting state with a key {/*option-2-resetting-state-with-a-key*/}

আরও একটি, ভাবে কম্পোনেন্টের স্টেট রিসেট করার একটি সাধারণ উপায় আছে।

আপনি সম্ভবত দেখেছেন `key` গুলি যখন [তালিকা রেন্ডার করা হয়।](/learn/rendering-lists#keeping-list-items-in-order-with-key) কীগুলি শুধুমাত্র তালিকার জন্য নয়! আপনি কীগুলি ব্যবহার করে রিয়েক্টকে যেকোনো কম্পোনেন্টগুলি পৃথক করতে ব্যবহার করতে পারেন। ডিফল্টভাবে, রিয়েক্ট অর্ডার ব্যবহার করে প্যারেন্টের মধ্যে ("প্রথম কাউন্টার", "দ্বিতীয় কাউন্টার") কম্পোনেন্টগুলি পৃথক করতে। কিন্তু কীগুলি আপনাকে বলতে দিয়ে দিতে পারে যে এটি শুধুমাত্র একটি *প্রথম* কাউন্টার নয়, অথবা *দ্বিতীয়* কাউন্টার নয়, বরং একটি নির্দিষ্ট কাউন্টার - উদাহরণস্বরূপ, *টেইলরের* কাউন্টার। এই ভাবে, রিয়েক্ট জানতে পারবে যে যেখানে এটি প্রদর্শিত হয়, সেখানে *টেইলরের* কাউন্টার!

এই উদাহরণে, প্রদর্শিত হলেও দুটি `<Counter />` সমস্ত অবস্থানে স্টেট ভাগ করে না:

<Sandpack>

```js
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter key="Taylor" person="Taylor" />
      ) : (
        <Counter key="Sarah" person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}

function Counter({ person }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{person}'s score: {score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
h1 {
  font-size: 18px;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

টেইলর এবং সারাহের মধ্যে স্টেট সংরক্ষিত থাকলেও স্থিতিশীল রাখা হয়না। এটি কারণ **আপনি তাদের জন্য ভিন্ন `key` দিয়েছেন:**

```js
{isPlayerA ? (
  <Counter key="Taylor" person="Taylor" />
) : (
  <Counter key="Sarah" person="Sarah" />
)}
```

একটি `key` নির্দিষ্ট করা রিয়েক্টকে বলে দেয় যে এটি স্থানের একটি অংশ হিসাবে ব্যবহার করবে, প্যারেন্টের অর্ডারের পরিবর্তে। এটি কারণেই, যখনই আপনি তাদের একই স্থানে JSX এ রেন্ডার করেন, রিয়েক্ট তাদের দুটি পৃথক কাউন্টার হিসাবে ধরে নেয়। সুতরাং, তারা কখনই স্টেট ভাগ করবে না। যেখানেই একটি কাউন্টার পর্যালোচনা করা হয়, তার স্টেট তৈরি হয়। যেকোনো সময় যখন এটি অপসারিত হয়, তখন এর স্টেট ধ্বংস হয়ে যায়। তাদের মধ্যে টগলিং করা হলে বারবার তাদের স্টেট রিসেট হয়।

<Note>

মনে রাখবেন যে কীগুলি গ্লোবালী অনন্য নয়। তারা কেবল *প্যারেন্টের মধ্যে অবস্থান নির্দিষ্ট করে*

</Note>

### একটি কী ব্যবহার করে ফর্ম রিসেট করা {/*resetting-a-form-with-a-key*/}

ফর্ম সম্পর্কে কথা বলার সময় কী ব্যবহার করে স্টেট রিসেট করা অত্যন্ত উপযুক্ত।

এই চ্যাট অ্যাপে, `<Chat>` কম্পোনেন্টটিতে টেক্সট ইনপুট স্টেট রয়েছে।:

<Sandpack>

```js App.js
import { useState } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';

export default function Messenger() {
  const [to, setTo] = useState(contacts[0]);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedContact={to}
        onSelect={contact => setTo(contact)}
      />
      <Chat contact={to} />
    </div>
  )
}

const contacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js ContactList.js
export default function ContactList({
  selectedContact,
  contacts,
  onSelect
}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact);
            }}>
              {contact.name}
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js Chat.js
import { useState } from 'react';

export default function Chat({ contact }) {
  const [text, setText] = useState('');
  return (
    <section className="chat">
      <textarea
        value={text}
        placeholder={'Chat to ' + contact.name}
        onChange={e => setText(e.target.value)}
      />
      <br />
      <button>Send to {contact.email}</button>
    </section>
  );
}
```

```css
.chat, .contact-list {
  float: left;
  margin-bottom: 20px;
}
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

ইনপুটে কিছু লিখে প্রেস করুন এবং তারপর "আলিস" বা "বব" এ প্রেস করুন যাতে আপনি একটি পৃথক প্রাপক প্রেস করতে পারেন। আপনি দেখবেন যে ইনপুট স্টেট সংরক্ষিত থাকে কারণ `<Chat>` একই অবস্থানে ট্রি রেন্ডার করা হয়।

**অনেক অ্যাপস এই অবস্থানটি কার্যকর হতে পারে, কিন্তু চ্যাট অ্যাপে এটি কার্যকর হবে না!** একটি অকার্যকর ক্লিকের ফলে ব্যবহারকারীকে একটি ভুল ব্যক্তির কাছে যেটি তারা ইতিমধ্যে লিখেছেন পাঠিয়ে দেওয়া উচিত নয়। এটি ঠিক করতে, একটি `key` যুক্ত করুন:

```js
<Chat key={to.id} contact={to} />
```

এটি নিশ্চিত করে যে যখন আপনি একটি পৃথক প্রাপক নির্বাচন করেন, তখন `Chat` কম্পোনেন্টটি পুনরায় সৃষ্টি হবে, নিচের ট্রির কোনো স্থিতির সাথে স্ক্র্যাচ থেকে নতুন স্থানে। রিয়েক্ট এটিও পুনরায় DOM উপাদানগুলি সৃষ্টি করবে, ব্যবহার করার পরিবর্তে।

এখন প্রাপক পরিবর্তন করলে সর্বদা টেক্সট ফিল্ড পরিষ্কার হয়:

<Sandpack>

```js App.js
import { useState } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';

export default function Messenger() {
  const [to, setTo] = useState(contacts[0]);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedContact={to}
        onSelect={contact => setTo(contact)}
      />
      <Chat key={to.id} contact={to} />
    </div>
  )
}

const contacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js ContactList.js
export default function ContactList({
  selectedContact,
  contacts,
  onSelect
}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact);
            }}>
              {contact.name}
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js Chat.js
import { useState } from 'react';

export default function Chat({ contact }) {
  const [text, setText] = useState('');
  return (
    <section className="chat">
      <textarea
        value={text}
        placeholder={'Chat to ' + contact.name}
        onChange={e => setText(e.target.value)}
      />
      <br />
      <button>Send to {contact.email}</button>
    </section>
  );
}
```

```css
.chat, .contact-list {
  float: left;
  margin-bottom: 20px;
}
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

<DeepDive>

#### অপসারিত কম্পোনেন্টগুলির জন্য স্টেট সংরক্ষণ করা {/*preserving-state-for-removed-components*/}

একটি প্রাসঙ্গিক চ্যাট অ্যাপে, আপনি সম্ভবত পূর্ববর্তী প্রাপকটি আবার নির্বাচন করলে ইনপুট স্টেট পুনরুদ্ধার করতে চান। একটি দৃষ্টিগোচর নয় কোম্পোনেন্টের জন্য স্টেটকে "সজীব" রাখতে কিছু উপায় আছে:

- আপনি বর্তমান প্রাপ্যের পরিবর্তে সমস্ত চ্যাট সংখ্যায় রেন্ডার করতে পারেন, তবে সকল অন্যান্য চ্যাটগুলি CSS দ্বারা লুকিয়ে রাখবেন। চ্যাটগুলি ট্রি থেকে অপসারিত হবেনা, তাই তাদের স্থানীয় স্টেটটি সংরক্ষিত থাকবে। এই সমাধানটি সাধারণ UI এর জন্য বিশেষভাবে ভালো কাজ করে। কিন্তু যদি লুকিয়ে রাখা ট্রি বড় হয় এবং বহুগুণ DOM নোড ধারণ করে, তবে এটি খুব নিউনতম হতে পারে।
- আপনি [স্টেটটি উপরে উঠিয়ে তুলতে পারেন](/learn/sharing-state-between-components) এবং প্রতিটি প্রাপকের জন্য মাতৃক কম্পোনেন্টে মুল্য রাখতে পারেন। এই ভাবে, যখন শিশু কম্পোনেন্টগুলি অপসারিত হয়, তখন এটি ব্যাপার নেই, কারণ প্যারেন্টটি গুরুত্বপূর্ণ তথ্য রাখে। এটি সবচেয়ে সাধারণ সমাধান।
- আপনি আপাতত রিয়েক্ট স্টেটের পাশাপাশি অতিরিক্ত একটি উৎস ব্যবহার করতে পারেন। উদাহরণস্বরূপ, ব্যবহারকারী যদি দুর্ঘটনাপূর্বক পৃষ্ঠাটি বন্ধ করে তবে মেসেজ ড্রাফটটি ধ্বংস না হয়। এটি সম্পাদন করতে, আপনি `Chat` কম্পোনেন্টটির স্টেটকে [`localStorage`](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) থেকে পড়ে তুলতে পারেন এবং ড্রাফটগুলি সেখানেই সংরক্ষণ করতে পারেন।

আপনি যে কোনটি ব্যবহার করুন, অলিসের সাথে একটি চ্যাট অ্যাপ সংজ্ঞায়িতভাবে বিচক্ষণ একটি চ্যাট থেকে ভিন্ন, তাই বর্তমান প্রাপকের উপর ভিত্তি করে `<Chat>` ট্রির জন্য একটি `key` দেওয়া যায়।

</DeepDive>

<Recap>

- React স্টেটটি সম্পর্কিত কম্পোনেন্ট সমান অবস্থানে রেন্ডার হওয়ার সময় পর্যন্ত স্টেটটিকে
 সংরক্ষণ করে রাখে।
- স্টেট JSX ট্যাগগুলিতে রাখা হয় না। এটি আপনি যে স্থানে JSX ট্যাগটি রাখেন সেই ট্রির অবস্থার সাথে সংযুক্ত হয়।
- আপনি একটি সাবট্রি কে পুনরায় স্থাপন করতে পারেন তার স্থিতিকে পুনরায় স্থাপন করার জন্য একটি নতুন কী দিয়ে।
- কম্পোনেন্ট ডেফিনিশন নেস্ট করবেন না, অন্যথায় আপনি কাছাকাছি স্টেট পুনরায় স্থাপন করতে পারেন।

</Recap>



<Challenges>

#### অপসারিত ইনপুট পাঠ্য ঠিক করুন {/*fix-disappearing-input-text*/}

এই উদাহরণে আপনি বাটন চাপার সময় একটি বার্তা প্রদর্শন করেন। তবে, বাটন চাপার সাথে সাথেই ইনপুট ভুলভাবে পুনরায় স্থাপন হয়ে যায়। কেন এটা ঘটছে? এটিকে ঠিক করুন যাতে বাটন চাপার মাধ্যমে ইনপুট পাঠ্যটি পুনরায় স্থাপন না হয়।

<Sandpack>

```js App.js
import { useState } from 'react';

export default function App() {
  const [showHint, setShowHint] = useState(false);
  if (showHint) {
    return (
      <div>
        <p><i>Hint: Your favorite city?</i></p>
        <Form />
        <button onClick={() => {
          setShowHint(false);
        }}>Hide hint</button>
      </div>
    );
  }
  return (
    <div>
      <Form />
      <button onClick={() => {
        setShowHint(true);
      }}>Show hint</button>
    </div>
  );
}

function Form() {
  const [text, setText] = useState('');
  return (
    <textarea
      value={text}
      onChange={e => setText(e.target.value)}
    />
  );
}
```

```css
textarea { display: block; margin: 10px 0; }
```

</Sandpack>

<Solution>

সমস্যাটি হলো `Form` কম্পোনেন্টটি বিভিন্ন অবস্থানে রেন্ডার করা হচ্ছে। `if` শাখায় এটি `<div>` এর দ্বিতীয় চাইল্‌ড্‌ হয়, কিন্তু `else` শাখায় এটি প্রথম চাইল্‌ড্‌ হয়। সুতরাং, প্রতিটি অবস্থানে কম্পোনেন্টের প্রকার পরিবর্তন হয়। প্রথম অবস্থানটি একটি `p` এবং `Form` ধারণ করার মধ্যে পরিবর্তন করে, যখন দ্বিতীয় অবস্থানটি একটি `Form` এবং `button` ধারণ করার মধ্যে পরিবর্তন করে। রিয়েক্ট প্রতিবার কম্পোনেন্টের প্রকার পরিবর্তন হওয়ায় স্টেট পুনরায় স্থাপন করে।

সহজতম সমাধানটি হলো শাখাগুলি একই অবস্থায় `Form` রেন্ডার করতে সংযোগ করা।:

<Sandpack>

```js App.js
import { useState } from 'react';

export default function App() {
  const [showHint, setShowHint] = useState(false);
  return (
    <div>
      {showHint &&
        <p><i>Hint: Your favorite city?</i></p>
      }
      <Form />
      {showHint ? (
        <button onClick={() => {
          setShowHint(false);
        }}>Hide hint</button>
      ) : (
        <button onClick={() => {
          setShowHint(true);
        }}>Show hint</button>
      )}
    </div>
  );
}

function Form() {
  const [text, setText] = useState('');
  return (
    <textarea
      value={text}
      onChange={e => setText(e.target.value)}
    />
  );
}
```

```css
textarea { display: block; margin: 10px 0; }
```

</Sandpack>


কাঠামোর দৃষ্টিকোণ থেকে, আপনি যেহেতু `<Form />` এর আগে একটি `null` যুক্ত করতে পারেন তা দিয়েও সমাধান সম্ভব। এটি `if` শাখার কাঠামোর সাথে মেলানোর জন্য সাহায্য করবে:

<Sandpack>

```js App.js
import { useState } from 'react';

export default function App() {
  const [showHint, setShowHint] = useState(false);
  if (showHint) {
    return (
      <div>
        <p><i>Hint: Your favorite city?</i></p>
        <Form />
        <button onClick={() => {
          setShowHint(false);
        }}>Hide hint</button>
      </div>
    );
  }
  return (
    <div>
      {null}
      <Form />
      <button onClick={() => {
        setShowHint(true);
      }}>Show hint</button>
    </div>
  );
}

function Form() {
  const [text, setText] = useState('');
  return (
    <textarea
      value={text}
      onChange={e => setText(e.target.value)}
    />
  );
}
```

```css
textarea { display: block; margin: 10px 0; }
```

</Sandpack>

এই উপায়ে, `Form` সর্বদা দ্বিতীয় চাইল্‌ড্‌ হয়ে থাকে, তাই সে একই অবস্থায় থাকে এবং তার স্টেটটি বজায় রাখে। কিন্তু এই পদ্ধতিটি অন্যান্যদের জন্য অন্যতম স্পষ্ট নয় এবং এটি একটি ঝুঁকি তৈরি করে যে কেউ ঐ `null` অংশটি সরাবলে।

</Solution>

#### দুটি ফর্ম ফিল্ড স্বপ করুন {/*swap-two-form-fields*/}

এই ফর্মে আপনি প্রথম এবং শেষ নাম লিখতে পারেন। এটির একটি চেকবক্স আছে যা নিয়ন্ত্রণ করে কোন ফিল্ড প্রথমে যাবে। যখন আপনি চেকবক্স চিহ্নিত করবেন, তখন "শেষ নাম" ফিল্ডটি "প্রথম নাম" ফিল্ডের আগে দেখানো হবে।

এটি সাম্প্রতিকতঃ কাজ করছে, তবে একটি বাগ রয়েছে। যখন আপনি "প্রথম নাম" ইনপুট পূর্ণ করে এবং চেকবক্স চিহ্নিত করেন, তখন টেক্সটটি প্রথম ইনপুটে (যা এখন "শেষ নাম") থাকবে। এটি সংশোধন করুন যাতে আপনি অর্ডার প্রতিবদ্ধ করলে ইনপুট টেক্সটও *সরাসরি* স্থানান্তর করতে পারে।

<Hint>

এই ফিল্ডগুলোর জন্য, তাদের প্যারেন্টের অবস্থান কেবলমাত্র যথাযথ নয়। আর কোন রাস্তা আছে যা দিয়ে React কে বোঝানো যাবে যে রিই-রেন্ডার এর মাজখানে স্টেট ম্যাচ করা যায়ে ?  

</Hint>

<Sandpack>

```js App.js
import { useState } from 'react';

export default function App() {
  const [reverse, setReverse] = useState(false);
  let checkbox = (
    <label>
      <input
        type="checkbox"
        checked={reverse}
        onChange={e => setReverse(e.target.checked)}
      />
      Reverse order
    </label>
  );
  if (reverse) {
    return (
      <>
        <Field label="Last name" /> 
        <Field label="First name" />
        {checkbox}
      </>
    );
  } else {
    return (
      <>
        <Field label="First name" /> 
        <Field label="Last name" />
        {checkbox}
      </>
    );    
  }
}

function Field({ label }) {
  const [text, setText] = useState('');
  return (
    <label>
      {label}:{' '}
      <input
        type="text"
        value={text}
        placeholder={label}
        onChange={e => setText(e.target.value)}
      />
    </label>
  );
}
```

```css
label { display: block; margin: 10px 0; }
```

</Sandpack>

<Solution>

উভয় `if` এবং `else` শাখাগুলিতে উভয় `<Field>` কম্পোনেন্টের জন্য একটি `key` দিন।এটি রিয়েক্টকে বলে দেয় যে যদি প্যারেন্টের ভিতরের ক্রম পরিবর্তিত হয়ে থাকুক, তাহলেও উভয় `<Field>` এর জন্য *সঠিক* স্টেট মিলাতে হবে।:

<Sandpack>

```js App.js
import { useState } from 'react';

export default function App() {
  const [reverse, setReverse] = useState(false);
  let checkbox = (
    <label>
      <input
        type="checkbox"
        checked={reverse}
        onChange={e => setReverse(e.target.checked)}
      />
      Reverse order
    </label>
  );
  if (reverse) {
    return (
      <>
        <Field key="lastName" label="Last name" /> 
        <Field key="firstName" label="First name" />
        {checkbox}
      </>
    );
  } else {
    return (
      <>
        <Field key="firstName" label="First name" /> 
        <Field key="lastName" label="Last name" />
        {checkbox}
      </>
    );    
  }
}

function Field({ label }) {
  const [text, setText] = useState('');
  return (
    <label>
      {label}:{' '}
      <input
        type="text"
        value={text}
        placeholder={label}
        onChange={e => setText(e.target.value)}
      />
    </label>
  );
}
```

```css
label { display: block; margin: 10px 0; }
```

</Sandpack>

</Solution>

#### রিসেট ডিটেল ফর্ম {/*reset-a-detail-form*/}

এটি একটি সম্পাদনযোগ্য যোগাযোগ তালিকা। আপনি নির্বাচিত যোগাযোগের বিশদগুলি সম্পাদনা করতে পারেন এবং তারপরে "Save" বাটনটি চাপুন যদি আপডেট করতে চান বা "Reset" বাটনটি চাপুন যদি আপনার পরিবর্তনগুলি বাতিল করতে চান।

যখন আপনি একটি নতুন যোগাযোগ নির্বাচন করেন (উদাহরণস্বরূপ, এলিস), স্টেট আপডেট হয়, কিন্তু ফর্মটি পূর্বের যোগাযোগের বিশদগুলি প্রদর্শন করে। নির্বাচিত যোগাযোগ পরিবর্তন হলে ফর্মটি রিসেট হওয়ার জন্য এটা ঠিক করুন।

<Sandpack>

```js App.js
import { useState } from 'react';
import ContactList from './ContactList.js';
import EditContact from './EditContact.js';

export default function ContactManager() {
  const [
    contacts,
    setContacts
  ] = useState(initialContacts);
  const [
    selectedId,
    setSelectedId
  ] = useState(0);
  const selectedContact = contacts.find(c =>
    c.id === selectedId
  );

  function handleSave(updatedData) {
    const nextContacts = contacts.map(c => {
      if (c.id === updatedData.id) {
        return updatedData;
      } else {
        return c;
      }
    });
    setContacts(nextContacts);
  }

  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={selectedId}
        onSelect={id => setSelectedId(id)}
      />
      <hr />
      <EditContact
        initialData={selectedContact}
        onSave={handleSave}
      />
    </div>
  )
}

const initialContacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js ContactList.js
export default function ContactList({
  contacts,
  selectedId,
  onSelect
}) {
  return (
    <section>
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact.id);
            }}>
              {contact.id === selectedId ?
                <b>{contact.name}</b> :
                contact.name
              }
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js EditContact.js
import { useState } from 'react';

export default function EditContact({ initialData, onSave }) {
  const [name, setName] = useState(initialData.name);
  const [email, setEmail] = useState(initialData.email);
  return (
    <section>
      <label>
        Name:{' '}
        <input
          type="text"
          value={name}
          onChange={e => setName(e.target.value)}
        />
      </label>
      <label>
        Email:{' '}
        <input
          type="email"
          value={email}
          onChange={e => setEmail(e.target.value)}
        />
      </label>
      <button onClick={() => {
        const updatedData = {
          id: initialData.id,
          name: name,
          email: email
        };
        onSave(updatedData);
      }}>
        Save
      </button>
      <button onClick={() => {
        setName(initialData.name);
        setEmail(initialData.email);
      }}>
        Reset
      </button>
    </section>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li { display: inline-block; }
li button {
  padding: 10px;
}
label {
  display: block;
  margin: 10px 0;
}
button {
  margin-right: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

<Solution>

সম্পাদনা করুন `EditContact` কম্পোনেন্টে একটি `key={selectedId}` দিন। এই ভাবে, পাত্র পরিবর্তন করলে ফর্মটি রিসেট হবে।:

<Sandpack>

```js App.js
import { useState } from 'react';
import ContactList from './ContactList.js';
import EditContact from './EditContact.js';

export default function ContactManager() {
  const [
    contacts,
    setContacts
  ] = useState(initialContacts);
  const [
    selectedId,
    setSelectedId
  ] = useState(0);
  const selectedContact = contacts.find(c =>
    c.id === selectedId
  );

  function handleSave(updatedData) {
    const nextContacts = contacts.map(c => {
      if (c.id === updatedData.id) {
        return updatedData;
      } else {
        return c;
      }
    });
    setContacts(nextContacts);
  }

  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={selectedId}
        onSelect={id => setSelectedId(id)}
      />
      <hr />
      <EditContact
        key={selectedId}
        initialData={selectedContact}
        onSave={handleSave}
      />
    </div>
  )
}

const initialContacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js ContactList.js
export default function ContactList({
  contacts,
  selectedId,
  onSelect
}) {
  return (
    <section>
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact.id);
            }}>
              {contact.id === selectedId ?
                <b>{contact.name}</b> :
                contact.name
              }
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js EditContact.js
import { useState } from 'react';

export default function EditContact({ initialData, onSave }) {
  const [name, setName] = useState(initialData.name);
  const [email, setEmail] = useState(initialData.email);
  return (
    <section>
      <label>
        Name:{' '}
        <input
          type="text"
          value={name}
          onChange={e => setName(e.target.value)}
        />
      </label>
      <label>
        Email:{' '}
        <input
          type="email"
          value={email}
          onChange={e => setEmail(e.target.value)}
        />
      </label>
      <button onClick={() => {
        const updatedData = {
          id: initialData.id,
          name: name,
          email: email
        };
        onSave(updatedData);
      }}>
        Save
      </button>
      <button onClick={() => {
        setName(initialData.name);
        setEmail(initialData.email);
      }}>
        Reset
      </button>
    </section>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li { display: inline-block; }
li button {
  padding: 10px;
}
label {
  display: block;
  margin: 10px 0;
}
button {
  margin-right: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

</Solution>

#### একটি ইমেজ লোড হওয়া সময়ে যদি ইমেজ ক্লিয়ার করা {/*clear-an-image-while-its-loading*/}

"Next" বাটনটি চাপ দিলে ব্রাউজার পরবর্তী ইমেজ লোড করতে শুরু করে। তবে, ডিফল্ট অবস্থায় এটি একই `<img>` ট্যাগে প্রদর্শিত হয়, তাই আপনি পরবর্তীটি লোড হওয়া পর্যন্ত পূর্বের ইমেজটি দেখতেই থাকবেন। এটি অপ্রাসঙ্গিক হতে পারে যদি টেক্সটটি সর্বদা ইমেজের সাথে মিলে যাওয়া প্রয়োজনীয়। "Next" বাটনটি চাপ দিলে, তাড়াতাড়ি পূর্ববর্তী ইমেজটি ক্লিয়ার হয়ে যাবে।

<Hint>

রিঅ্যাক্টকে এটি পুনঃব্যবহারের পরিবর্তে DOM পুনরায় তৈরি করতে বলার কোন উপায় আছে কি?

</Hint>

<Sandpack>

```js
import { useState } from 'react';

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const hasNext = index < images.length - 1;

  function handleClick() {
    if (hasNext) {
      setIndex(index + 1);
    } else {
      setIndex(0);
    }
  }

  let image = images[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h3>
        Image {index + 1} of {images.length}
      </h3>
      <img src={image.src} />
      <p>
        {image.place}
      </p>
    </>
  );
}

let images = [{
  place: 'Penang, Malaysia',
  src: 'https://i.imgur.com/FJeJR8M.jpg'
}, {
  place: 'Lisbon, Portugal',
  src: 'https://i.imgur.com/dB2LRbj.jpg'
}, {
  place: 'Bilbao, Spain',
  src: 'https://i.imgur.com/z08o2TS.jpg'
}, {
  place: 'Valparaíso, Chile',
  src: 'https://i.imgur.com/Y3utgTi.jpg'
}, {
  place: 'Schwyz, Switzerland',
  src: 'https://i.imgur.com/JBbMpWY.jpg'
}, {
  place: 'Prague, Czechia',
  src: 'https://i.imgur.com/QwUKKmF.jpg'
}, {
  place: 'Ljubljana, Slovenia',
  src: 'https://i.imgur.com/3aIiwfm.jpg'
}];
```

```css
img { width: 150px; height: 150px; }
```

</Sandpack>

<Solution>

আপনি `<img>` ট্যাগের জন্য একটি `key` সরবরাহ করতে পারেন। যখন ঐ `key` পরিবর্তন হয়, তখন রিয়েক্ট `<img>` ডোম নোডটি পুনর্সৃষ্টি করবে। এটি প্রতিটি ইমেজের লোড হওয়ার সময় একটি সংক্ষেপ ফ্ল্যাশ উত্পন্ন করে, তাই এটি আপনার অ্যাপ্লিকেশনের প্রতিটি ইমেজের জন্য করতে চান না। তবে এটি সম্ভবত সম্ভাব্যতঃ যদি আপনি নিশ্চিত করতে চান যে ইমেজ সর্বদা টেক্সটের সাথে মেলে।

<Sandpack>

```js
import { useState } from 'react';

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const hasNext = index < images.length - 1;

  function handleClick() {
    if (hasNext) {
      setIndex(index + 1);
    } else {
      setIndex(0);
    }
  }

  let image = images[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h3>
        Image {index + 1} of {images.length}
      </h3>
      <img key={image.src} src={image.src} />
      <p>
        {image.place}
      </p>
    </>
  );
}

let images = [{
  place: 'Penang, Malaysia',
  src: 'https://i.imgur.com/FJeJR8M.jpg'
}, {
  place: 'Lisbon, Portugal',
  src: 'https://i.imgur.com/dB2LRbj.jpg'
}, {
  place: 'Bilbao, Spain',
  src: 'https://i.imgur.com/z08o2TS.jpg'
}, {
  place: 'Valparaíso, Chile',
  src: 'https://i.imgur.com/Y3utgTi.jpg'
}, {
  place: 'Schwyz, Switzerland',
  src: 'https://i.imgur.com/JBbMpWY.jpg'
}, {
  place: 'Prague, Czechia',
  src: 'https://i.imgur.com/QwUKKmF.jpg'
}, {
  place: 'Ljubljana, Slovenia',
  src: 'https://i.imgur.com/3aIiwfm.jpg'
}];
```

```css
img { width: 150px; height: 150px; }
```

</Sandpack>

</Solution>

#### তালিকায় ভুল অবস্থানে রাখা স্টেট ঠিক করুন {/*fix-misplaced-state-in-the-list*/}

এই তালিকায়, প্রতিটি `Contact` একটি স্টেট রাখে যা নির্ধারণ করে যে কি "Show Email" এর জন্য প্রেস করা হয়েছে। এলিসের জন্য "Show Email" চাপুন এবং তারপরে "Show in reverse order" চেকবক্সটি চেক করুন। আপনি লক্ষ্য করবেন যে এটি _টেলরের_ ইমেল যা এখন প্রসারিত হয়েছে, কিন্তু অ্যালিসের-- যা নীচে চলে গেছে-- ভেঙে পড়েছে বলে মনে হচ্ছে।

এটি ঠিক করুন যাতে প্রসারিত অবস্থা প্রতিটি পরিচিতির সাথে যুক্ত থাকে, নির্বাচিত আদেশ নির্বিশেষে।

<Sandpack>

```js App.js
import { useState } from 'react';
import Contact from './Contact.js';

export default function ContactList() {
  const [reverse, setReverse] = useState(false);

  const displayedContacts = [...contacts];
  if (reverse) {
    displayedContacts.reverse();
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          value={reverse}
          onChange={e => {
            setReverse(e.target.checked)
          }}
        />{' '}
        Show in reverse order
      </label>
      <ul>
        {displayedContacts.map((contact, i) =>
          <li key={i}>
            <Contact contact={contact} />
          </li>
        )}
      </ul>
    </>
  );
}

const contacts = [
  { id: 0, name: 'Alice', email: 'alice@mail.com' },
  { id: 1, name: 'Bob', email: 'bob@mail.com' },
  { id: 2, name: 'Taylor', email: 'taylor@mail.com' }
];
```

```js Contact.js
import { useState } from 'react';

export default function Contact({ contact }) {
  const [expanded, setExpanded] = useState(false);
  return (
    <>
      <p><b>{contact.name}</b></p>
      {expanded &&
        <p><i>{contact.email}</i></p>
      }
      <button onClick={() => {
        setExpanded(!expanded);
      }}>
        {expanded ? 'Hide' : 'Show'} email
      </button>
    </>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li {
  margin-bottom: 20px;
}
label {
  display: block;
  margin: 10px 0;
}
button {
  margin-right: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

<Solution>

সমস্যা হল যে এই উদাহরণটি একটি `key` হিসাবে সূচক ব্যবহার করছে:

```js
{displayedContacts.map((contact, i) =>
  <li key={i}>
```

যাইহোক, আপনি স্টেইট্কে _প্রত্যেকটি নির্দিষ্ট পরিচিতির_ সাথে যুক্ত করতে চান।

পরিবর্তে একটি `key` হিসাবে পরিচিতি কন্‌ট্যাক্‌ট্‌ আইডি ব্যবহার করে সমস্যাটি সমাধান করে:

<Sandpack>

```js App.js
import { useState } from 'react';
import Contact from './Contact.js';

export default function ContactList() {
  const [reverse, setReverse] = useState(false);

  const displayedContacts = [...contacts];
  if (reverse) {
    displayedContacts.reverse();
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          value={reverse}
          onChange={e => {
            setReverse(e.target.checked)
          }}
        />{' '}
        Show in reverse order
      </label>
      <ul>
        {displayedContacts.map(contact =>
          <li key={contact.id}>
            <Contact contact={contact} />
          </li>
        )}
      </ul>
    </>
  );
}

const contacts = [
  { id: 0, name: 'Alice', email: 'alice@mail.com' },
  { id: 1, name: 'Bob', email: 'bob@mail.com' },
  { id: 2, name: 'Taylor', email: 'taylor@mail.com' }
];
```

```js Contact.js
import { useState } from 'react';

export default function Contact({ contact }) {
  const [expanded, setExpanded] = useState(false);
  return (
    <>
      <p><b>{contact.name}</b></p>
      {expanded &&
        <p><i>{contact.email}</i></p>
      }
      <button onClick={() => {
        setExpanded(!expanded);
      }}>
        {expanded ? 'Hide' : 'Show'} email
      </button>
    </>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li {
  margin-bottom: 20px;
}
label {
  display: block;
  margin: 10px 0;
}
button {
  margin-right: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

স্টেটটি ট্রি অবস্থার সাথে সংযুক্ত হয়। `key` দ্বারা আপনি ক্রমে নির্ধারিত হতে অবস্থানের পরিবর্তে নামকরণ করতে পারেন।

</Solution>

</Challenges>
