---
title: useLayoutEffect
---

<Pitfall>

`useLayoutEffect` ব্যবহার করলে কর্মক্ষমতা কমে যেতে পারে। সম্ভব হলে [`useEffect`](/reference/react/useEffect) ব্যবহার করা শ্রেয়।

</Pitfall>

<Intro>

`useLayoutEffect` হলো [`useEffect`](/reference/react/useEffect) এর একটি সংস্করণ যা ব্রাউজার স্ক্রিন পুনরায় পেইন্ট করার আগে সক্রিয় হয়।

```js
useLayoutEffect(setup, dependencies?)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `useLayoutEffect(setup, dependencies?)` {/*useinsertioneffect*/}

ব্রাউজার স্ক্রিন পুনরায় পেইন্ট করার আগে লেআউট মাপজোক সম্পন্ন করতে `useLayoutEffect` কল করুন।

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

* `setup`: আপনার Effect-এর লজিক সহ ফাংশন। আপনার setup ফাংশন ঐচ্ছিকভাবে একটি cleanup ফাংশন ফেরত দিতে পারে। আপনার কম্পোনেন্ট DOM-এ যোগ করার আগে, React আপনার setup ফাংশন চালাবে। প্রতিটি পুনরায়-রেন্ডার করার পর যেখানে ডিপেন্ডেন্সিগুলি পরিবর্তিত হয়েছে, React প্রথমে cleanup ফাংশন চালাবে (যদি আপনি তা প্রদান করেন) পুরানো মানগুলির সাথে, এবং তারপর নতুন মানগুলির সাথে setup ফাংশন চালাবে। আপনার কম্পোনেন্ট DOM থেকে সরানোর আগে, React আপনার cleanup ফাংশন চালাবে।

* **optional** `dependencies`: সমস্ত reactive মানগুলির তালিকা যা setup কোডের ভিতরে উল্লেখ করা হয়েছে। Reactive মানগুলির মধ্যে props, state, এবং আপনার কম্পোনেন্ট বডির ভিতরে সরাসরি ঘোষিত সমস্ত ভেরিয়েবল এবং ফাংশন অন্তর্ভুক্ত। যদি আপনার লিন্টার [React এর জন্য কনফিগার করা থাকে](/learn/editor-setup#linting), এটি যাচাই করবে যে প্রতিটি reactive মানটি সঠিকভাবে ডিপেন্ডেন্সি হিসাবে উল্লেখ করা হয়েছে। ডিপেন্ডেন্সিগুলির তালিকায় একটি নির্দিষ্ট সংখ্যা আইটেম থাকতে হবে এবং এটি `[dep1, dep2, dep3]` এর মতো ইনলাইন লেখা উচিত। React প্রতিটি নির্ভরশীলতাকে এর পূর্ববর্তী মানের সাথে [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) তুলনার মাধ্যমে তুলনা করবে। আপনি যদি এই আর্গুমেন্টটি বাদ দেন, তবে আপনার Effect প্রতিটি রি-রেন্ডার করার পর পুনরায় চালানো হবে।

#### রিটার্নস {/*returns*/}

`useLayoutEffect` `undefined` রিটার্ন করে।

#### সতর্কতা {/*caveats*/}

* `useLayoutEffect` একটি হুক, তাই আপনি এটি কেবল আপনার **কম্পোনেন্টের একেবারে উপরের স্তরে** বা আপনার নিজের হুকের একেবারে উপরের স্তরে কল করতে পারেন। আপনি এটিকে কোনো লুপ বা কন্ডিশনের মধ্যে কল করতে পারবেন না। যদি আপনার এটির দরকার হয়, তাহলে একটি কম্পোনেন্ট বের করুন এবং সেখানে ইফেক্টটি সরান।

* যখন স্ট্রিক্ট মোড চালু থাকে, তখন React প্রথম আসল সেটআপের আগে **একটি অতিরিক্ত ডেভেলপমেন্ট-অনলি সেটআপ+ক্লিনআপ সাইকেল চালায়।** এটি একটি স্ট্রেস-টেস্ট যা নিশ্চিত করে যে আপনার ক্লিনআপ লজিক আপনার সেটআপ লজিকের “মিরর” এবং এটি সেটআপ যা করছে তা বন্ধ করে দেয় বা আগের অবস্থায় ফেরায়। যদি এটি একটি সমস্যা সৃষ্টি করে, তাহলে [ক্লিনআপ ফাংশন বাস্তবায়ন করুন।](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development)

* আপনার কিছু ডিপেন্ডেন্সি যদি অবজেক্ট বা ফাংশন হয় যা কম্পোনেন্টের ভিতরে সংজ্ঞায়িত করা হয়েছে, তবে এটি **ইফেক্টকে প্রয়োজনের চেয়ে বেশি বার পুনরায় চালানোর ঝুঁকি থাকে।** এটি ঠিক করতে, অপ্রয়োজনীয় [অবজেক্ট](/reference/react/useEffect#removing-unnecessary-object-dependencies) এবং [ফাংশন](/reference/react/useEffect#removing-unnecessary-function-dependencies) ডিপেন্ডেন্সিগুলো সরিয়ে ফেলুন। আপনি [স্টেট আপডেট সরিয়ে ফেলতে পারেন](/reference/react/useEffect#updating-state-based-on-previous-state-from-an-effect) এবং [নন-রিঅ্যাক্টিভ লজিক](/reference/react/useEffect#reading-the-latest-props-and-state-from-an-effect) আপনার ইফেক্টের বাইরে বের করেও রাখতে পারেন।

* ইফেক্ট **কেবল ক্লায়েন্টে চালায়।** তারা সার্ভার রেন্ডারিংয়ের সময় চালায় না।

* `useLayoutEffect` এর ভিতরের কোড এবং এর থেকে নির্ধারিত সমস্ত স্টেট আপডেট **ব্রাউজারকে স্ক্রিন পুনরায় পেইন্ট করা থেকে ব্লক করে।** অতিরিক্ত ব্যবহারের সময়, এটি আপনার অ্যাপকে ধীর করে তোলে। সম্ভব হলে, [`useEffect`](/reference/react/useEffect) পছন্দ করুন।

---

## ব্যবহারবিধি {/*usage*/}

### ব্রাউজারের স্ক্রিন পুনরায় আঁকার আগে লে-আউট পরিমাপ করা {/*measuring-layout-before-the-browser-repaints-the-screen*/}

বেশিরভাগ কম্পোনেন্ট তাদের অবস্থান এবং আকার জানার প্রয়োজন হয় না কী রেন্ডার করবে তা সিদ্ধান্ত নেওয়ার জন্য। তারা কেবল কিছু JSX ফেরত দেয়। তারপর ব্রাউজার তাদের *লেআউট* (অবস্থান এবং আকার) গণনা করে এবং স্ক্রিন পুনরায় আঁকে।

কখনও কখনও, এটি যথেষ্ট নয়। কল্পনা করুন একটি টুলটিপ যা হোভার করার সময় কিছু উপাদানের পাশে প্রদর্শিত হয়। যদি পর্যাপ্ত জায়গা থাকে, তবে টুলটিপটি উপাদানের উপরে প্রদর্শিত হওয়া উচিত, কিন্তু যদি এটি ফিট না হয়, তবে এটি নীচে প্রদর্শিত হওয়া উচিত। সঠিক চূড়ান্ত অবস্থানে টুলটিপটি রেন্ডার করার জন্য, আপনাকে এর উচ্চতা জানতে হবে (যেমন, এটি উপরে ফিট হয় কিনা)।

এটি করার জন্য, আপনাকে দুটি পাসে রেন্ডার করতে হবে:

১. টুলটিপটি যেকোনো জায়গায় রেন্ডার করুন (যদিও এটি ভুল অবস্থানে থাকে)।
২. এর উচ্চতা পরিমাপ করুন এবং কোথায় টুলটিপটি স্থাপন করতে হবে তা নির্ধারণ করুন।
৩. টুলটিপটি *আবার* সঠিক স্থানে রেন্ডার করুন।

**এটি সমস্ত কিছু ব্রাউজার স্ক্রিন পুনরায় আঁকার আগে ঘটতে হবে।** আপনি চান না যে ব্যবহারকারী টুলটিপটি সরতে দেখুক। ব্রাউজার স্ক্রিন পুনরায় আঁকার আগে লেআউট পরিমাপ সম্পন্ন করতে `useLayoutEffect` কল করুন।

```js {5-8}
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

১. `টুলটিপ` শুরুতে initial `tooltipHeight = 0` নিয়ে রেন্ডার হয় (ফলে টুলটিপ ভুলভাবে অবস্থান নিতে পারে)।
২. রিঅ্যাক্ট এটিকে DOM-এ স্থাপন করে এবং `useLayoutEffect` কোড চালায়।
৩. আপনার `useLayoutEffect` টুলটিপ বিষয়বস্তুর [উচ্চতা মাপে](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect) এবং একটি তাৎক্ষণিক পুনরায়-রেন্ডার ট্রিগার করে।
৪. `টুলটিপ` আবার প্রকৃত `tooltipHeight` নিয়ে রেন্ডার হয় (ফলে টুলটিপ সঠিকভাবে অবস্থান নেয়)।
৫. রিঅ্যাক্ট এটিকে DOM-এ আপডেট করে, এবং ব্রাউজার শেষ পর্যন্ত টুলটিপটি প্রদর্শন করে।

নিচের বোতামগুলোর উপর হোভার করে দেখুন কিভাবে টুলটিপটি তার অবস্থান সামঞ্জস্য করে তা নির্ভর করে এটি ফিট করে কিনা:

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

```js src/Tooltip.js active
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

যদিও `Tooltip` কম্পোনেন্টকে দুটি ধাপে রেন্ডার করতে হয় (প্রথমে, `tooltipHeight` `শূন্য` হিসেবে প্রাথমিকভাবে সেট করা থাকে এবং পরে প্রকৃত মাপা উচ্চতার সাথে), আপনি কেবল চূড়ান্ত ফলাফলটি দেখেন। এই কারণেই আপনাকে এই উদাহরণের জন্য [`useEffect`](/reference/react/useEffect) এর পরিবর্তে `useLayoutEffect` ব্যবহার করতে হবে। নিচে বিস্তারিতভাবে পার্থক্যটি দেখে নেওয়া যাক।

<Recipes titleText="useLayoutEffect বনাম useEffect" titleId="examples">

#### `useLayoutEffect` ব্রাউজারকে পুনরায় পেইন্টিং থেকে আটকায় {/*uselayouteffect-blocks-the-browser-from-repainting*/}

React নিশ্চয়তা দেয় যে `useLayoutEffect`-এর ভিতরের কোড এবং এর ভিতরে নির্ধারিত যেকোনো স্টেট আপডেটগুলি **ব্রাউজার স্ক্রিন পুনরায় পেইন্টিং করার আগে** প্রক্রিয়া করা হবে। এটি আপনাকে টুলটিপটি রেন্ডার করতে, এটি মাপতে এবং টুলটিপটি আবার পুনরায় রেন্ডার করতে দেয় ব্যবহারকারী প্রথম অতিরিক্ত রেন্ডারটি খেয়াল না করে। অন্য কথায়, `useLayoutEffect` ব্রাউজারকে পেইন্ট করা থেকে আটকায়।

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

```js src/Tooltip.js active
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

এখানে একই উদাহরণ আছে, কিন্তু `useLayoutEffect` এর পরিবর্তে [`useEffect`](/reference/react/useEffect) ব্যবহার করা হয়েছে। আপনি যদি ধীর গতির ডিভাইসে থাকেন, আপনি লক্ষ্য করতে পারেন যে কখনও কখনও টুলটিপটি "ঝলক" দেয় এবং আপনি সংক্ষিপ্ত সময়ের জন্য এর প্রাথমিক অবস্থান দেখতে পাবেন তার সংশোধিত অবস্থানের আগে।

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

```js src/Tooltip.js active
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

বাগটি পুনরুৎপাদন সহজ করতে, এই সংস্করণটি রেন্ডারিংয়ের সময় একটি কৃত্রিম বিলম্ব যোগ করে। React ব্রাউজারকে স্ক্রিন পেইন্ট করতে দেবে তার আগে এটি `useEffect` এর ভিতরে স্টেট আপডেট প্রক্রিয়া করবে। ফলস্বরূপ, টুলটিপটি ঝলকানির মতো হবে।

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

```js src/Tooltip.js active
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

এই উদাহরণটি ব্যবহার করে `useLayoutEffect` এডিট করুন এবং লক্ষ্য করুন যে এটি রেন্ডারিং ধীরগতি হলেও পেইন্ট ব্লক করে।

<Solution />

</Recipes>

<Note>

দুটি ধাপে রেন্ডারিং এবং ব্রাউজার ব্লকিং পারফরম্যান্সের উপর প্রভাব ফেলে। আপনি যখন পারেন, এটি এড়ানোর চেষ্টা করুন।

</Note>

---

## সমস্যা সমাধান {/*troubleshooting*/}

### আমি একটি ত্রুটি পাচ্ছি: "`useLayoutEffect` সার্ভারে কিছুই করে না" {/*im-getting-an-error-uselayouteffect-does-nothing-on-the-server*/}

`useLayoutEffect` ব্যবহারের উদ্দেশ্য হল আপনার কম্পোনেন্টকে [লেআউট তথ্য ব্যবহার করে রেন্ডার করতে দেয়া:](#measuring-layout-before-the-browser-repaints-the-screen)

১. প্রাথমিক বিষয়বস্তু রেন্ডার করুন।
২. ব্রাউজার স্ক্রীনটি পুনরায় রঙ করার আগে লেআউট পরিমাপ করুন।
৩. আপনি যে লেআউট তথ্য পড়েছেন তা ব্যবহার করে চূড়ান্ত বিষয়বস্তু রেন্ডার করুন।

যখন আপনি বা আপনার ফ্রেমওয়ার্ক [সার্ভার রেন্ডারিং](/reference/react-dom/server) ব্যবহার করেন, তখন আপনার রিঅ্যাক্ট অ্যাপ্লিকেশনটি প্রাথমিক রেন্ডারের জন্য সার্ভারে HTML-এ রেন্ডার হয়। এটি আপনাকে জাভাস্ক্রিপ্ট কোড লোড হওয়ার আগে প্রাথমিক HTML দেখানোর সুযোগ দেয়।

সমস্যাটি হল যে সার্ভারে কোনও লেআউট তথ্য নেই।

[আগের উদাহরণে](#measuring-layout-before-the-browser-repaints-the-screen), `Tooltip` কম্পোনেন্টের মধ্যে `useLayoutEffect` কলটি এর বিষয়বস্তু উচ্চতার উপর নির্ভর করে সঠিকভাবে নিজেকে অবস্থান করতে দেয় (বিষয়বস্তুর উপরে বা নিচে)। আপনি যদি `Tooltip`-কে প্রাথমিক সার্ভার HTML-এর অংশ হিসেবে রেন্ডার করার চেষ্টা করেন, তাহলে এটি নির্ধারণ করা অসম্ভব হবে। সার্ভারে এখনও কোনো লেআউট নেই! তাই, এমনকি যদি আপনি এটি সার্ভারে রেন্ডার করেন, তবুও এর অবস্থান জাভাস্ক্রিপ্ট লোড এবং রান করার পরে ক্লায়েন্টে "লাফাবে"।

সাধারণত, যেসব কম্পোনেন্ট লেআউট তথ্যের উপর নির্ভর করে সেগুলো সার্ভারে রেন্ডার করার প্রয়োজন হয় না। উদাহরণস্বরূপ, প্রাথমিক রেন্ডারের সময় `Tooltip` দেখানো সম্ভবত অর্থহীন। এটি একটি ক্লায়েন্ট ইন্টারঅ্যাকশনের মাধ্যমে ট্রিগার হয়।

তবে, যদি আপনি এই সমস্যার সম্মুখীন হন, আপনার কাছে কয়েকটি বিকল্প রয়েছে:

- `useLayoutEffect` এর পরিবর্তে [`useEffect`](/reference/react/useEffect) ব্যবহার করুন। এটি React-কে বলে যে এটি প্রাথমিক রেন্ডার ফলাফল প্রদর্শন করতে পারে পেইন্ট ব্লক না করে (কারণ মূল HTML আপনার ইফেক্ট রান করার আগে দৃশ্যমান হয়ে যাবে)।

- বিকল্পভাবে, [আপনার কম্পোনেন্টকে ক্লায়েন্ট-অনলি হিসেবে চিহ্নিত করুন।](/reference/react/Suspense#providing-a-fallback-for-server-errors-and-client-only-content) এটি React-কে বলে যে এটি এর বিষয়বস্তুকে সবচেয়ে কাছের [`<Suspense>`](/reference/react/Suspense) সীমারেখা পর্যন্ত একটি লোডিং ফলব্যাক (উদাহরণস্বরূপ, একটি স্পিনার বা একটি গ্লিমার) দিয়ে প্রতিস্থাপন করবে সার্ভার রেন্ডারিংয়ের সময়।

- বিকল্পভাবে, আপনি হাইড্রেশনের পরে শুধুমাত্র `useLayoutEffect` সহ একটি কম্পোনেন্ট রেন্ডার করতে পারেন। একটি বুলিয়ান `isMounted` স্টেট রাখুন যা শুরুতে `false` হিসাবে ইনিশিয়ালাইজড হয় এবং একটি `useEffect` কলের মধ্যে এটি `true` এ সেট করুন। আপনার রেন্ডারিং লজিকটি তখন এইরকম হতে পারে: `return isMounted ? <RealContent /> : <FallbackContent />`। সার্ভার এবং হাইড্রেশনের সময়, ব্যবহারকারী `FallbackContent` দেখবে যা `useLayoutEffect` কল করবে না। তারপর React এটিকে `RealContent` দিয়ে প্রতিস্থাপন করবে যা শুধুমাত্র ক্লায়েন্টে রান করবে এবং `useLayoutEffect` কল অন্তর্ভুক্ত করতে পারে।

- যদি আপনি আপনার কম্পোনেন্টকে একটি বাহ্যিক ডেটা স্টোরের সাথে সিঙ্ক্রোনাইজ করেন এবং লেআউট পরিমাপের চেয়ে ভিন্ন কারণে `useLayoutEffect` এর উপর নির্ভর করেন, তবে [`useSyncExternalStore`](/reference/react/useSyncExternalStore) বিবেচনা করুন যা [সার্ভার রেন্ডারিং সমর্থন করে।](/reference/react/useSyncExternalStore#adding-support-for-server-rendering)
