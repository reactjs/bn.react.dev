---
title: useLayoutEffect
---

<Pitfall>

`useLayoutEffect` ব্যবহার করলে পারফরম্যান্স কমে যেতে পারে। সম্ভব হলে [`useEffect`](/reference/react/useEffect) ব্যবহার করাই ভাল।

</Pitfall>

<Intro>

`useLayoutEffect` হলো [`useEffect`](/reference/react/useEffect) এর একটি সংস্করণ যা ব্রাউজারের স্ক্রিন পুনরায় পেইন্ট করার আগে চালু হয়।

```js
useLayoutEffect(setup, dependencies?)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `useLayoutEffect(setup, dependencies?)` {/*useinsertioneffect*/}

ব্রাউজারের স্ক্রিন পুনরায় পেইন্ট করার আগে লেআউট মাপজোক সম্পন্ন করতে `useLayoutEffect` কল করুন:

```js
import { useState, useRef, useLayoutEffect } from 'react';

function Tooltip() {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  useLayoutEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
  }, []);
  // ...
```


[নীচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটারস {/*parameters*/}

<<<<<<< HEAD
* `setup`: যা আপনার Effect এর লজিক সহ ফাংশন। আপনার এই setup ফাংশন প্রয়োজনে একটি *cleanup* ফাংশন রিটার্ন করতে পারে। আপনার কম্পোনেন্ট DOM-এ যোগ করার আগে, React আপনার setup ফাংশন চালাবে। প্রতিটি রি-রেন্ডার করার পর যেখানে ডিপেন্ডেন্সিগুলি পরিবর্তিত হয়েছে, React প্রথমে পুরোনো মানগুলির সাথে cleanup ফাংশন চালাবে (যদি আপনি তা প্রদান করেন), এবং তারপর নতুন মানগুলির সাথে setup ফাংশন চালাবে। DOM থেকে আপনার কম্পোনেন্ট সরানোর আগে, React আপনার cleanup ফাংশন চালাবে।
=======
* `setup`: The function with your Effect's logic. Your setup function may also optionally return a *cleanup* function. Before your component is added to the DOM, React will run your setup function. After every re-render with changed dependencies, React will first run the cleanup function (if you provided it) with the old values, and then run your setup function with the new values. Before your component is removed from the DOM, React will run your cleanup function.

* **optional** `dependencies`: The list of all reactive values referenced inside of the `setup` code. Reactive values include props, state, and all the variables and functions declared directly inside your component body. If your linter is [configured for React](/learn/editor-setup#linting), it will verify that every reactive value is correctly specified as a dependency. The list of dependencies must have a constant number of items and be written inline like `[dep1, dep2, dep3]`. React will compare each dependency with its previous value using the [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) comparison. If you omit this argument, your Effect will re-run after every re-render of the component.
>>>>>>> 366b5fbdadefecbbf9f6ef36c0342c083248c691

* **optional** `dependencies`: `setup` কোডের মধ্যে রেফারেন্স করা সকল রিঅ্যাক্টিভ ভ্যালুর তালিকা। রিঅ্যাক্টিভ ভ্যালুর মধ্যে রয়েছে props, state এবং আপনার কম্পোনেন্ট বডির মধ্যে সরাসরি ডিক্লেয়ার হওয়া সকল ভ্যারিয়েবল এবং ফাংশন। যদি আপনার লিন্টার [React এর জন্য কনফিগার করা থাকে](/learn/editor-setup#linting), এটা নিশ্চিত করবে যে প্রতিটা রিঅ্যাক্টিভ ভ্যালু সঠিকভাবে ডিপেন্ডেন্সি হিসেবে উল্লেখ করা আছে। ডিপেন্ডেন্সিগুলির তালিকায় অবশ্যই আইটেমের সংখ্যা ধ্রুবক হতে হবে এবং এটি `[dep1, dep2, dep3]` এর মতো ইনলাইনে থাকতে হবে। React প্রতিটি ডিপেন্ডেন্সিকে এর পূর্ববর্তী মানের সাথে [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) কোম্পারিসন অ্যালগরিদম ব্যবহার করে তুলনা করবে। আপনি যদি এই আর্গুমেন্টটি বাদ দেন, তবে কম্পোনেন্টের প্রতিটি রি-রেন্ডার করার পর আপনার Effect পুনরায় চালু হবে।

#### রিটার্নস {/*returns*/}

`useLayoutEffect` `undefined` রিটার্ন করে।

#### সতর্কতা {/*caveats*/}

* `useLayoutEffect` একটি হুক, তাই আপনি এটি কেবল আপনার **কম্পোনেন্টের একেবারে উপরের দিকে** বা আপনার নিজের **হুকের একেবারে উপরের দিকে** কল করতে পারেন। আপনি এটিকে কোনো লুপ বা কন্ডিশনের মধ্যে কল করতে পারবেন না। যদি দরকার হয়, তাহলে একটি কম্পোনেন্ট বের করুন এবং সেখানে Effectটি সরান।

* যখন স্ট্রিক্ট মোড চালু থাকে, তখন React তার প্রথম আসল সেটআপের আগে **একটি অতিরিক্ত ডেভেলপমেন্ট-অনলি সেটআপ+ক্লিনআপ সাইকেল চালায়।** এটি একটি স্ট্রেস-টেস্ট যা নিশ্চিত করে যে আপনার ক্লিনআপ লজিক আপনার সেটআপ লজিককে "প্রতিফলিত করে" এবং এটি সেটআপ যা করছে তা বন্ধ করে দেয় বা আগের অবস্থায় ফেরায়। যদি এটি সমস্যা করে, তাহলে [ক্লিনআপ ফাংশন প্রয়োগ করুন।](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development)

* আপনার কিছু ডিপেন্ডেন্সি যদি অবজেক্ট বা ফাংশন হয় যা কম্পোনেন্টের ভিতরে ডিফাইন করা হয়েছে, তবে এটি **Effect-কে প্রয়োজনের চেয়ে বেশি বার রি-রান করার ঝুঁকিতে ফেলে।** এটি ঠিক করতে, অপ্রয়োজনীয় [অবজেক্ট](/reference/react/useEffect#removing-unnecessary-object-dependencies) এবং [ফাংশন](/reference/react/useEffect#removing-unnecessary-function-dependencies) ডিপেন্ডেন্সিগুলো সরিয়ে ফেলুন। আপনি [স্টেট আপডেটগুলো সরিয়ে ফেলতে পারেন](/reference/react/useEffect#updating-state-based-on-previous-state-from-an-effect) এবং [নন-রিঅ্যাক্টিভ লজিক](/reference/react/useEffect#reading-the-latest-props-and-state-from-an-effect) আপনার Effect এর বাইরে বের করেও রাখতে পারেন।

* Effects **শুধুমাত্র ক্লায়েন্টে কাজ করে।** এগুলো সার্ভার রেন্ডারিং এর সময় রান করে না।

* `useLayoutEffect` এর ভিতরের কোড এবং এর থেকে নির্ধারিত সকল স্টেট আপডেট **ব্রাউজারকে স্ক্রিন পুনরায় পেইন্ট করা থেকে ব্লক করে।** অতিরিক্ত ব্যবহারের সময়, এটি আপনার অ্যাপকে ধীরগতির করে তোলে। সম্ভব হলে, [`useEffect`](/reference/react/useEffect) ব্যবহার করুন।

* If you trigger a state update inside `useLayoutEffect`, React will execute all remaining Effects immediately including `useEffect`.

---

## ব্যবহারবিধি {/*usage*/}

### ব্রাউজারের স্ক্রিন পুনরায় পেইন্ট করার আগে লে-আউট মাপজোক করা {/*measuring-layout-before-the-browser-repaints-the-screen*/}

বেশিরভাগ কম্পোনেন্ট কী রেন্ডার করবে তা সিদ্ধান্ত নেওয়ার জন্য তাদের অবস্থান এবং আকার জানার প্রয়োজন হয় না। এগুলো শুধুমাত্র কিছু JSX রিটার্ন করে। তারপর ব্রাউজার তাদের *লেআউট* (অবস্থান এবং আকার) হিসাব করে এবং স্ক্রিন পুনরায় পেইন্ট করে।

কখনো কখনো, এটি যথেষ্ট নয়। মনে করুন একটি টুলটিপ যা হোভার করার সময় কিছু element-এর পাশে প্রদর্শিত হয়। যদি পর্যাপ্ত জায়গা থাকে, তবে টুলটিপটি element-এর উপরে দেখানো উচিত, কিন্তু যদি এটি ফিট না হয়, তাহলে এটি নীচে দেখানো উচিত। সঠিক চূড়ান্ত অবস্থানে টুলটিপটি রেন্ডার করার জন্য, আপনাকে এর উচ্চতা জানতে হবে (যেমন, এটি উপরে ফিট হয় কিনা)।

এটি করার জন্য, আপনাকে দুটি পাসে রেন্ডার করতে হবে:

১. টুলটিপটি যেকোনো জায়গায় রেন্ডার করুন (যদিও এটি ভুল অবস্থানে থাকে)।
২. এর উচ্চতা পরিমাপ করুন এবং কোথায় টুলটিপটি বসাতে হবে তা নির্ধারণ করুন।
৩. টুলটিপটি *আবার* সঠিক জায়গায় রেন্ডার করুন।

**এই সবকিছু ব্রাউজারের স্ক্রিন পুনরায় পেইন্ট হওয়ার আগে ঘটতে হবে।** আপনি নিশ্চয়ই চান না যে ব্যবহারকারী টুলটিপটি সরতে দেখুক। তাই ব্রাউজারের স্ক্রিন পুনরায় পেইন্ট হওয়ার আগে লেআউটের মাপজোক সম্পন্ন করতে `useLayoutEffect` কল করুন।

{/* TODO(@poteto) - fixed by https://github.com/facebook/react/pull/34462. need a new release */}
```js {expectedErrors: {'react-compiler': [7]}} {5-8}
function Tooltip() {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0); // You don't know real height yet

  useLayoutEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height); // Re-render now that you know the real height
  }, []);

  // ...use tooltipHeight in the rendering logic below...
}
```

এটি ধাপে ধাপে কীভাবে কাজ করে তা এখানে দেওয়া হল:

১. `টুলটিপ` শুরুতে `tooltipHeight = 0` নিয়ে রেন্ডার হয় (ফলে টুলটিপটি ভুলভাবে অবস্থান নেয়)।
২. React এটিকে DOM-এ স্থাপন করে এবং `useLayoutEffect` এ কোড চালায়।
৩. আপনার `useLayoutEffect` টুলটিপ কন্টেন্টের [উচ্চতা মাপে](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect) এবং একটি তাৎক্ষণিক রি-রেন্ডার ট্রিগার করে।
৪. `টুলটিপ` আবার প্রকৃত `tooltipHeight` নিয়ে রেন্ডার হয় (ফলে টুলটিপটি সঠিকভাবে অবস্থান নেয়)।
৫. React এটিকে DOM-এ আপডেট করে, এবং ব্রাউজার অবশেষে টুলটিপটি দেখায়।

নিচের বাটনগুলোর উপর মাউসটি নিয়ে যান এবং দেখুন টুলটিপটির অবস্থান কীভাবে সমন্বয় করে, এটা বোঝার জন্য যে এটি ফিট হচ্ছে কিনা:

<Sandpack>

```js
import ButtonWithTooltip from './ButtonWithTooltip.js';

export default function App() {
  return (
    <div>
      <ButtonWithTooltip
        tooltipContent={
          <div>
            This tooltip does not fit above the button.
            <br />
            This is why it's displayed below instead!
          </div>
        }
      >
        Hover over me (tooltip above)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
    </div>
  );
}
```

```js src/ButtonWithTooltip.js
import { useState, useRef } from 'react';
import Tooltip from './Tooltip.js';

export default function ButtonWithTooltip({ tooltipContent, ...rest }) {
  const [targetRect, setTargetRect] = useState(null);
  const buttonRef = useRef(null);
  return (
    <>
      <button
        {...rest}
        ref={buttonRef}
        onPointerEnter={() => {
          const rect = buttonRef.current.getBoundingClientRect();
          setTargetRect({
            left: rect.left,
            top: rect.top,
            right: rect.right,
            bottom: rect.bottom,
          });
        }}
        onPointerLeave={() => {
          setTargetRect(null);
        }}
      />
      {targetRect !== null && (
        <Tooltip targetRect={targetRect}>
          {tooltipContent}
        </Tooltip>
      )
    }
    </>
  );
}
```

{/* TODO(@poteto) - fixed by https://github.com/facebook/react/pull/34462. need a new release */}
```js {expectedErrors: {'react-compiler': [11]}} src/Tooltip.js active
import { useRef, useLayoutEffect, useState } from 'react';
import { createPortal } from 'react-dom';
import TooltipContainer from './TooltipContainer.js';

export default function Tooltip({ children, targetRect }) {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  useLayoutEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
    console.log('Measured tooltip height: ' + height);
  }, []);

  let tooltipX = 0;
  let tooltipY = 0;
  if (targetRect !== null) {
    tooltipX = targetRect.left;
    tooltipY = targetRect.top - tooltipHeight;
    if (tooltipY < 0) {
      // It doesn't fit above, so place below.
      tooltipY = targetRect.bottom;
    }
  }

  return createPortal(
    <TooltipContainer x={tooltipX} y={tooltipY} contentRef={ref}>
      {children}
    </TooltipContainer>,
    document.body
  );
}
```

```js src/TooltipContainer.js
export default function TooltipContainer({ children, x, y, contentRef }) {
  return (
    <div
      style={{
        position: 'absolute',
        pointerEvents: 'none',
        left: 0,
        top: 0,
        transform: `translate3d(${x}px, ${y}px, 0)`
      }}
    >
      <div ref={contentRef} className="tooltip">
        {children}
      </div>
    </div>
  );
}
```

```css
.tooltip {
  color: white;
  background: #222;
  border-radius: 4px;
  padding: 4px;
}
```

</Sandpack>

লক্ষ্য করুন, যদিও `Tooltip` কম্পোনেন্টকে দুটি ধাপে রেন্ডার করতে হয় (প্রথমে, `tooltipHeight` `০` হিসেবে প্রাথমিকভাবে সেট করা থাকে এবং পরে মাপা প্রকৃত উচ্চতার সাথে), আপনি কেবল চূড়ান্ত ফলাফলটি দেখেন। এই কারণেই এই উদাহরণের জন্য আপনাকে [`useEffect`](/reference/react/useEffect) এর পরিবর্তে `useLayoutEffect` ব্যবহার করতে হবে। নিচে বিস্তারিতভাবে পার্থক্যটি দেখে নেওয়া যাক।

<Recipes titleText="useLayoutEffect বনাম useEffect" titleId="examples">

#### `useLayoutEffect` ব্রাউজারকে পুনরায় পেইন্টিং থেকে আটকায় {/*uselayouteffect-blocks-the-browser-from-repainting*/}

React নিশ্চয়তা দেয় যে `useLayoutEffect`-এর ভিতরের কোড এবং এর ভিতরে নির্ধারিত যেকোনো স্টেট আপডেটগুলি **ব্রাউজার স্ক্রিন পুনরায় পেইন্টিং করার আগে** প্রক্রিয়া করা হবে। এটি ব্যবহারকারীকে প্রথমেই করা অতিরিক্ত রেন্ডারটি না দেখিয়ে আপনাকে টুলটিপটি রেন্ডার করতে, মাপতে এবং টুলটিপটি আবার পুনরায় রেন্ডার করতে দেয়। অন্য কথায়, `useLayoutEffect` ব্রাউজারকে পেইন্ট করা থেকে আটকায়।

<Sandpack>

```js
import ButtonWithTooltip from './ButtonWithTooltip.js';

export default function App() {
  return (
    <div>
      <ButtonWithTooltip
        tooltipContent={
          <div>
            This tooltip does not fit above the button.
            <br />
            This is why it's displayed below instead!
          </div>
        }
      >
        Hover over me (tooltip above)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
    </div>
  );
}
```

```js src/ButtonWithTooltip.js
import { useState, useRef } from 'react';
import Tooltip from './Tooltip.js';

export default function ButtonWithTooltip({ tooltipContent, ...rest }) {
  const [targetRect, setTargetRect] = useState(null);
  const buttonRef = useRef(null);
  return (
    <>
      <button
        {...rest}
        ref={buttonRef}
        onPointerEnter={() => {
          const rect = buttonRef.current.getBoundingClientRect();
          setTargetRect({
            left: rect.left,
            top: rect.top,
            right: rect.right,
            bottom: rect.bottom,
          });
        }}
        onPointerLeave={() => {
          setTargetRect(null);
        }}
      />
      {targetRect !== null && (
        <Tooltip targetRect={targetRect}>
          {tooltipContent}
        </Tooltip>
      )
    }
    </>
  );
}
```

{/* TODO(@poteto) - fixed by https://github.com/facebook/react/pull/34462. need a new release */}
```js {expectedErrors: {'react-compiler': [11]}} src/Tooltip.js active
import { useRef, useLayoutEffect, useState } from 'react';
import { createPortal } from 'react-dom';
import TooltipContainer from './TooltipContainer.js';

export default function Tooltip({ children, targetRect }) {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  useLayoutEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
  }, []);

  let tooltipX = 0;
  let tooltipY = 0;
  if (targetRect !== null) {
    tooltipX = targetRect.left;
    tooltipY = targetRect.top - tooltipHeight;
    if (tooltipY < 0) {
      // It doesn't fit above, so place below.
      tooltipY = targetRect.bottom;
    }
  }

  return createPortal(
    <TooltipContainer x={tooltipX} y={tooltipY} contentRef={ref}>
      {children}
    </TooltipContainer>,
    document.body
  );
}
```

```js src/TooltipContainer.js
export default function TooltipContainer({ children, x, y, contentRef }) {
  return (
    <div
      style={{
        position: 'absolute',
        pointerEvents: 'none',
        left: 0,
        top: 0,
        transform: `translate3d(${x}px, ${y}px, 0)`
      }}
    >
      <div ref={contentRef} className="tooltip">
        {children}
      </div>
    </div>
  );
}
```

```css
.tooltip {
  color: white;
  background: #222;
  border-radius: 4px;
  padding: 4px;
}
```

</Sandpack>

<Solution />

#### `useEffect` ব্রাউজারকে ব্লক করে না {/*useeffect-does-not-block-the-browser*/}

এখানে একই উদাহরণ আছে, কিন্তু `useLayoutEffect` এর পরিবর্তে [`useEffect`](/reference/react/useEffect) ব্যবহার করা হয়েছে। আপনি যদি ধীর গতির ডিভাইসে থাকেন, আপনি লক্ষ্য করবেন যে কখনো কখনো টুলটিপটি "পিটপিট্" করে এবং আপনি এটির সঠিক অবস্থানের আগে অল্প সময়ের জন্য এটিকে শুরুর অবস্থান দেখতে পাবেন।

<Sandpack>

```js
import ButtonWithTooltip from './ButtonWithTooltip.js';

export default function App() {
  return (
    <div>
      <ButtonWithTooltip
        tooltipContent={
          <div>
            This tooltip does not fit above the button.
            <br />
            This is why it's displayed below instead!
          </div>
        }
      >
        Hover over me (tooltip above)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
    </div>
  );
}
```

```js src/ButtonWithTooltip.js
import { useState, useRef } from 'react';
import Tooltip from './Tooltip.js';

export default function ButtonWithTooltip({ tooltipContent, ...rest }) {
  const [targetRect, setTargetRect] = useState(null);
  const buttonRef = useRef(null);
  return (
    <>
      <button
        {...rest}
        ref={buttonRef}
        onPointerEnter={() => {
          const rect = buttonRef.current.getBoundingClientRect();
          setTargetRect({
            left: rect.left,
            top: rect.top,
            right: rect.right,
            bottom: rect.bottom,
          });
        }}
        onPointerLeave={() => {
          setTargetRect(null);
        }}
      />
      {targetRect !== null && (
        <Tooltip targetRect={targetRect}>
          {tooltipContent}
        </Tooltip>
      )
    }
    </>
  );
}
```

{/* TODO(@poteto) - fixed by https://github.com/facebook/react/pull/34462. need a new release */}
```js {expectedErrors: {'react-compiler': [11]}} src/Tooltip.js active
import { useRef, useEffect, useState } from 'react';
import { createPortal } from 'react-dom';
import TooltipContainer from './TooltipContainer.js';

export default function Tooltip({ children, targetRect }) {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  useEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
  }, []);

  let tooltipX = 0;
  let tooltipY = 0;
  if (targetRect !== null) {
    tooltipX = targetRect.left;
    tooltipY = targetRect.top - tooltipHeight;
    if (tooltipY < 0) {
      // It doesn't fit above, so place below.
      tooltipY = targetRect.bottom;
    }
  }

  return createPortal(
    <TooltipContainer x={tooltipX} y={tooltipY} contentRef={ref}>
      {children}
    </TooltipContainer>,
    document.body
  );
}
```

```js src/TooltipContainer.js
export default function TooltipContainer({ children, x, y, contentRef }) {
  return (
    <div
      style={{
        position: 'absolute',
        pointerEvents: 'none',
        left: 0,
        top: 0,
        transform: `translate3d(${x}px, ${y}px, 0)`
      }}
    >
      <div ref={contentRef} className="tooltip">
        {children}
      </div>
    </div>
  );
}
```

```css
.tooltip {
  color: white;
  background: #222;
  border-radius: 4px;
  padding: 4px;
}
```

</Sandpack>

বাগটি সহজে আবার দেখানোর জন্য, এই ভার্সনটিতে রেন্ডারিংয়ের সময় ইচ্ছে করে অতিরিক্ত সময় যোগ করা হয়েছে। React `useEffect` এর ভিতরে স্টেট আপডেট করার আগে ব্রাউজারকে স্ক্রীন পেইন্ট করতে দেবে। এর ফলে, টুলটিপটি পিটপিট্ করে:

<Sandpack>

```js
import ButtonWithTooltip from './ButtonWithTooltip.js';

export default function App() {
  return (
    <div>
      <ButtonWithTooltip
        tooltipContent={
          <div>
            This tooltip does not fit above the button.
            <br />
            This is why it's displayed below instead!
          </div>
        }
      >
        Hover over me (tooltip above)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
    </div>
  );
}
```

```js src/ButtonWithTooltip.js
import { useState, useRef } from 'react';
import Tooltip from './Tooltip.js';

export default function ButtonWithTooltip({ tooltipContent, ...rest }) {
  const [targetRect, setTargetRect] = useState(null);
  const buttonRef = useRef(null);
  return (
    <>
      <button
        {...rest}
        ref={buttonRef}
        onPointerEnter={() => {
          const rect = buttonRef.current.getBoundingClientRect();
          setTargetRect({
            left: rect.left,
            top: rect.top,
            right: rect.right,
            bottom: rect.bottom,
          });
        }}
        onPointerLeave={() => {
          setTargetRect(null);
        }}
      />
      {targetRect !== null && (
        <Tooltip targetRect={targetRect}>
          {tooltipContent}
        </Tooltip>
      )
    }
    </>
  );
}
```

```js {expectedErrors: {'react-compiler': [10, 11]}} src/Tooltip.js active
import { useRef, useEffect, useState } from 'react';
import { createPortal } from 'react-dom';
import TooltipContainer from './TooltipContainer.js';

export default function Tooltip({ children, targetRect }) {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  // This artificially slows down rendering
  let now = performance.now();
  while (performance.now() - now < 100) {
    // Do nothing for a bit...
  }

  useEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
  }, []);

  let tooltipX = 0;
  let tooltipY = 0;
  if (targetRect !== null) {
    tooltipX = targetRect.left;
    tooltipY = targetRect.top - tooltipHeight;
    if (tooltipY < 0) {
      // It doesn't fit above, so place below.
      tooltipY = targetRect.bottom;
    }
  }

  return createPortal(
    <TooltipContainer x={tooltipX} y={tooltipY} contentRef={ref}>
      {children}
    </TooltipContainer>,
    document.body
  );
}
```

```js src/TooltipContainer.js
export default function TooltipContainer({ children, x, y, contentRef }) {
  return (
    <div
      style={{
        position: 'absolute',
        pointerEvents: 'none',
        left: 0,
        top: 0,
        transform: `translate3d(${x}px, ${y}px, 0)`
      }}
    >
      <div ref={contentRef} className="tooltip">
        {children}
      </div>
    </div>
  );
}
```

```css
.tooltip {
  color: white;
  background: #222;
  border-radius: 4px;
  padding: 4px;
}
```

</Sandpack>

এই উদাহরণটি ব্যবহার করে `useLayoutEffect` এডিট করুন এবং লক্ষ্য করুন যে এটির রেন্ডারিং ধীরগতির হলেও পেইন্ট ব্লক করে।

<Solution />

</Recipes>

<Note>

দুটি ধাপে রেন্ডারিং এবং ব্রাউজার ব্লকিং পারফরম্যান্সের উপর প্রভাব ফেলে। আপনি যখন পারেন, এটি এড়ানোর চেষ্টা করুন।

</Note>

---

## সমস্যা সমাধান {/*troubleshooting*/}

### আমি একটি এরর পাচ্ছি: "`useLayoutEffect` সার্ভারে কিছুই করে না" {/*im-getting-an-error-uselayouteffect-does-nothing-on-the-server*/}

`useLayoutEffect`এর উদ্দেশ্য হল আপনার কম্পোনেন্টকে [লেআউট তথ্য ব্যবহার করে রেন্ডার করতে দেয়া:](#measuring-layout-before-the-browser-repaints-the-screen)

১. একদম শুরুর কন্টেন্ট রেন্ডার করুন।
২. *ব্রাউজার স্ক্রীনটি পুনরায় পেইন্ট করার আগে* লেআউট মাপজোক করুন।
৩. আপনি যে লেআউট তথ্য পড়েছেন তা ব্যবহার করে চূড়ান্ত বিষয়বস্তু রেন্ডার করুন।

যখন আপনি বা আপনার ফ্রেমওয়ার্ক [সার্ভার রেন্ডারিং](/reference/react-dom/server) ব্যবহার করেন, তখন আপনার React অ্যাপ্লিকেশনটি শুরুতে রেন্ডারের জন্য সার্ভারে HTML-এ রেন্ডার হয়। এটি আপনাকে জাভাস্ক্রিপ্ট কোড লোড হওয়ার আগে প্রাথমিক HTML দেখানোর সুযোগ দেয়।

সমস্যাটি হলো যে সার্ভারে কোনো লেআউট তথ্য নেই।

[আগের উদাহরণে](#measuring-layout-before-the-browser-repaints-the-screen), `Tooltip` কম্পোনেন্টের মধ্যে `useLayoutEffect` কলটি এর কন্টেন্টের উচ্চতার উপর নির্ভর করে সঠিকভাবে (কন্টেন্টের উপরে বা নিচে) নিজেকে অবস্থান করতে দেয়। আপনি যদি `Tooltip`-কে শুরুর সার্ভার HTML-এর অংশ হিসেবে রেন্ডার করার চেষ্টা করেন, তাহলে এটি নির্ধারণ করা অসম্ভব হবে। কারণ সার্ভারে এখনও কোনো লেআউট নেই! তাই, এমনকি যদি আপনি এটি সার্ভারে রেন্ডার করেন, তবুও এর অবস্থান জাভাস্ক্রিপ্ট লোড এবং রান করার পরে ক্লায়েন্টে "লাফাবে"।

সাধারণত, যেসব কম্পোনেন্ট লেআউট তথ্যের উপর নির্ভর করে সেগুলো সার্ভারে রেন্ডার করার প্রয়োজন হয় না। উদাহরণস্বরূপ, প্রাথমিক রেন্ডারের সময় `Tooltip` দেখানো সম্ভবত অর্থহীন। এটি ক্লায়েন্ট ইন্টারঅ্যাকশনের মাধ্যমে ট্রিগার হয়।

তবে, যদি আপনি এই সমস্যার মুখোমুখি হন, আপনার কাছে কয়েকটি বিকল্প উপায় রয়েছে:

- `useLayoutEffect` এর পরিবর্তে [`useEffect`](/reference/react/useEffect) ব্যবহার করুন। এটি React-কে বলে পেইন্ট ব্লক না করে যে এটি প্রাথমিক রেন্ডার ফলাফল প্রদর্শন করতে পারে (কারণ আসল HTML আপনার Effect রান করার আগে দৃশ্যমান হয়ে যাবে)।

- বিকল্পভাবে, [আপনার কম্পোনেন্টকে ক্লায়েন্ট-অনলি হিসেবে বিবেচিত করুন।](/reference/react/Suspense#providing-a-fallback-for-server-errors-and-server-only-content) এটি React-কে বলে যে সার্ভার রেন্ডারিংয়ের সময় এটি এর কন্টেন্টকে সবচেয়ে কাছের [`<Suspense>`](/reference/react/Suspense) বাউন্ডারি পর্যন্ত একটি লোডিং ফলব্যাক (উদাহরণস্বরূপ, একটি স্পিনার বা একটি গ্লিমার) দিয়ে বদল করবে ।

- বিকল্পভাবে, আপনি হাইড্রেশনের পরে শুধুমাত্র `useLayoutEffect` সহ একটি কম্পোনেন্ট রেন্ডার করতে পারেন। একটি বুলিয়ান `isMounted` স্টেট রাখুন যা শুরুতে `false` দিয়ে আরম্ভ করা হয় এবং এটিকে একটি `useEffect` কলের ভিতরে `true` তে সেট করুন। আপনার রেন্ডারিং লজিকটি তখন এইরকম হতে পারে: `return isMounted ? <RealContent /> : <FallbackContent />`। সার্ভার এবং হাইড্রেশনের সময়, ব্যবহারকারী `FallbackContent` দেখবে যা `useLayoutEffect` কল করবে না। তারপর React এটিকে `RealContent` দিয়ে বদলে দিবে যা শুধুমাত্র ক্লায়েন্টে রান করবে এবং `useLayoutEffect` কল করতে পারে।

- যদি আপনি আপনার কম্পোনেন্টকে একটি বাহিরের কোনো ডেটা স্টোরের সাথে সমন্বয় করেন এবং লেআউট পরিমাপের চেয়ে ভিন্ন কারণে `useLayoutEffect` এর উপর নির্ভর করেন, তবে [`useSyncExternalStore`](/reference/react/useSyncExternalStore) বিবেচনা করুন যা [সার্ভার রেন্ডারিং সমর্থন করে।](/reference/react/useSyncExternalStore#adding-support-for-server-rendering)
