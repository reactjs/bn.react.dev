---
title: createPortal
---

<Intro>

`createPortal` আপনাকে DOM-এর ভিন্ন অংশে কিছু চাইল্ড রেন্ডার করতে দেয়।


```js
<div>
  <SomeComponent />
  {createPortal(children, domNode, key?)}
</div>
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `createPortal(children, domNode, key?)` {/*createportal*/}

একটি পোর্টাল তৈরি করতে, `createPortal` কল করুন, কিছু JSX এবং DOM নোড পাস করুন যেখানে এটি রেন্ডার করা হবেঃ

```js
import { createPortal } from 'react-dom';

// ...

<div>
  <p>This child is placed in the parent div.</p>
  {createPortal(
    <p>This child is placed in the document body.</p>,
    document.body
  )}
</div>
```

[নীচে আরও উদাহরণ দেখুন।](#usage)

একটি পোর্টাল কেবল DOM নোডের physical অবস্থান পরিবর্তন করে। অন্যান্য সকল উপায়ে, আপনি যে JSX পোর্টালে রেন্ডার করেন তা React কম্পোনেন্টের একটি চাইল্ড নোড হিসাবে কাজ করে যা এটিকে রেন্ডার করে। উদাহরণস্বরূপ, চাইল্ড প্যারেন্ট ট্রি দ্বারা প্রদত্ত কনটেক্স্ট অ্যাক্সেস করতে পারে, এবং ইভেন্টগুলি চাইল্ড থেকে প্যারেন্টদের কাছে React ট্রি অনুসারে বুদবুদ করে উঠে।

#### প্যারামিটার {/*parameters*/}

* `children`: যেকোনো জিনিস যা React দ্বারা রেন্ডার করা যায়, যেমন একটি JSX অংশ (উদাহরণস্বরূপ `<div />` বা `<SomeComponent />`), একটি [ফ্র্যাগমেন্ট](/reference/react/Fragment) (`<>...</>`), একটি স্ট্রিং বা একটি সংখ্যা, অথবা এগুলির একটি অ্যারে।

* `domNode`: কিছু DOM নোড, যেমন `document.getElementById()` দ্বারা ফেরত আসা নোডগুলি। নোডটি ইতোমধ্যে বিদ্যমান থাকতে হবে। আপডেটের সময় ভিন্ন DOM নোড পাস করা হলে পোর্টাল কন্টেন্ট পুনরায় তৈরি করা হবে।

* **ঐচ্ছিক** `key`: পোর্টালের [কী](/learn/rendering-lists/#keeping-list-items-in-order-with-key) হিসাবে ব্যবহার করার জন্য একটি অনন্য স্ট্রিং বা সংখ্যা।

#### রিটার্নস {/*returns*/}

`createPortal` একটি React নোড ফেরত দেয় যা JSX-এ অন্তর্ভুক্ত করা যেতে পারে বা একটি React কম্পোনেন্ট থেকে ফেরত দেওয়া যেতে পারে। যদি React এটি রেন্ডার আউটপুটে দেখে, এটি প্রদত্ত `children`-কে প্রদত্ত `domNode`-এর মধ্যে রাখবে।

#### সাবধানতা {/*caveats*/}

* পোর্টাল থেকে ইভেন্টগুলি DOM ট্রি নয়, বরং React ট্রি অনুসারে এগিয়ে যায়। উদাহরণস্বরূপ, যদি আপনি একটি পোর্টালের ভেতরে ক্লিক করেন, এবং পোর্টালটি `<div onClick>`-এ wrap করা থাকে, তাহলে `onClick` হ্যান্ডলারটি ফায়ার হবে। যদি এটি সমস্যা তৈরি করে, তাহলে পোর্টালের ভেতরে event propagation বন্ধ করুন, অথবা পোর্টালটি React ট্রিতে উপরের দিকে উঠিয়ে নিন।

---

## ব্যবহার {/*usage*/}

### DOM-এর ভিন্ন একটি অংশে রেন্ডারিং {/*rendering-to-a-different-part-of-the-dom*/}

*পোর্টাল* আপনার কম্পোনেন্টগুলিকে তাদের কিছু চাইল্ডকদ DOM-এর ভিন্ন স্থানে রেন্ডার করতে দেয়। এটি আপনার কম্পোনেন্টের একটি অংশকে যেকোনো কন্টেইনার থেকে "মুক্ত" করে। উদাহরণস্বরূপ, একটি কম্পোনেন্ট একটি মডাল ডায়লগ বা একটি টুলটিপ প্রদর্শন করতে পারে যা বাকি পৃষ্ঠার উপরে এবং বাইরে প্রদর্শিত হয়।

একটি পোর্টাল তৈরি করতে, `createPortal`-এর ফলাফল রেন্ডার করুন <CodeStep step={1}>কিছু JSX</CodeStep> এবং <CodeStep step={2}>DOM নোডে যেখানে এটি যাওয়া উচিত</CodeStep>:

```js [[1, 8, "<p>This child is placed in the document body.</p>"], [2, 9, "document.body"]]
import { createPortal } from 'react-dom';

function MyComponent() {
  return (
    <div style={{ border: '2px solid black' }}>
      <p>This child is placed in the parent div.</p>
      {createPortal(
        <p>This child is placed in the document body.</p>,
        document.body
      )}
    </div>
  );
}
```

React <CodeStep step={1}>আপনি যে JSX পাস করেছেন</CodeStep> এর DOM নোডগুলি <CodeStep step={2}>আপনার প্রদত্ত DOM নোডের</CodeStep> মধ্যে রাখবে।

একটি পোর্টাল ছাড়া, দ্বিতীয় `<p>` প্যারেন্ট `<div>`-এর ভেতরে স্থাপন করা হত, কিন্তু পোর্টালটি এটিকে [`document.body`:](https://developer.mozilla.org/en-US/docs/Web/API/Document/body) এ "টেলিপোর্ট" করেছে।

<Sandpack>

```js
import { createPortal } from 'react-dom';

export default function MyComponent() {
  return (
    <div style={{ border: '2px solid black' }}>
      <p>This child is placed in the parent div.</p>
      {createPortal(
        <p>This child is placed in the document body.</p>,
        document.body
      )}
    </div>
  );
}
```

</Sandpack>

লক্ষ্য করুন কিভাবে দ্বিতীয় প্যারাগ্রাফ দৃশ্যত বর্ডার সহ প্যারেন্ট `<div>`-এর বাইরে প্রদর্শিত হচ্ছে। যদি আপনি ডেভেলপার টুলস দ্বারা DOM কাঠামো পরীক্ষা করেন, আপনি দেখবেন যে দ্বিতীয় `<p>` সরাসরি `<body>`-তে রাখা হয়েছে:

```html {4-6,9}
<body>
  <div id="root">
    ...
      <div style="border: 2px solid black">
        <p>This child is placed inside the parent div.</p>
      </div>
    ...
  </div>
  <p>This child is placed in the document body.</p>
</body>
```

একটি পোর্টাল কেবল DOM নোডের physcial অবস্থান পরিবর্তন করে। অন্যান্য সকল উপায়ে, আপনি যে JSX পোর্টালে রেন্ডার করেন তা React কম্পোনেন্টের একটি চাইল্ড নোড হিসেবে কাজ করে যা এটিকে রেন্ডার করে। উদাহরণস্বরূপ, চাইল্ড প্যারেন্ট ট্রি দ্বারা প্রদত্ত কনটেক্স্ট অ্যাক্সেস করতে পারে, এবং ইভেন্টগুলি চাইল্ড থেকে প্যারেন্টের কাছে React ট্রি অনুসারে বুদবুদ করে উপরে উঠে।

---

### একটি পোর্টাল দ্বারা মোডাল ডায়লগ রেন্ডার করা {/*rendering-a-modal-dialog-with-a-portal*/}

আপনি একটি পোর্টাল ব্যবহার করে একটি মোডাল ডায়লগ তৈরি করতে পারেন যা পেইজের বাকি অংশের উপরে ভাসমান, এমনকি যদি ডায়লগ কল করা কম্পোনেন্টটি `overflow: hidden` বা অন্যান্য স্টাইল যা ডায়লগের সাথে বাধা দেয় এমন কন্টেইনারের মধ্যে থাকে।

এই উদাহরণে, দুটি কন্টেইনারের স্টাইলগুলি মডাল ডায়লগের জন্য বিঘ্ন ঘটাচ্ছে, কিন্তু পোর্টালে রেন্ডার করা একটি অপ্রভাবিত থাকে, কারণ DOM-এ, মোডালটি প্যারেন্ট JSX উপাদানগুলির মধ্যে contained নয়।

<Sandpack>

```js src/App.js active
import NoPortalExample from './NoPortalExample';
import PortalExample from './PortalExample';

export default function App() {
  return (
    <>
      <div className="clipping-container">
        <NoPortalExample  />
      </div>
      <div className="clipping-container">
        <PortalExample />
      </div>
    </>
  );
}
```

```js src/NoPortalExample.js
import { useState } from 'react';
import ModalContent from './ModalContent.js';

export default function NoPortalExample() {
  const [showModal, setShowModal] = useState(false);
  return (
    <>
      <button onClick={() => setShowModal(true)}>
        Show modal without a portal
      </button>
      {showModal && (
        <ModalContent onClose={() => setShowModal(false)} />
      )}
    </>
  );
}
```

```js src/PortalExample.js active
import { useState } from 'react';
import { createPortal } from 'react-dom';
import ModalContent from './ModalContent.js';

export default function PortalExample() {
  const [showModal, setShowModal] = useState(false);
  return (
    <>
      <button onClick={() => setShowModal(true)}>
        Show modal using a portal
      </button>
      {showModal && createPortal(
        <ModalContent onClose={() => setShowModal(false)} />,
        document.body
      )}
    </>
  );
}
```

```js src/ModalContent.js
export default function ModalContent({ onClose }) {
  return (
    <div className="modal">
      <div>I'm a modal dialog</div>
      <button onClick={onClose}>Close</button>
    </div>
  );
}
```


```css src/styles.css
.clipping-container {
  position: relative;
  border: 1px solid #aaa;
  margin-bottom: 12px;
  padding: 12px;
  width: 250px;
  height: 80px;
  overflow: hidden;
}

.modal {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  box-shadow: rgba(100, 100, 111, 0.3) 0px 7px 29px 0px;
  background-color: white;
  border: 2px solid rgb(240, 240, 240);
  border-radius: 12px;
  position:  absolute;
  width: 250px;
  top: 70px;
  left: calc(50% - 125px);
  bottom: 70px;
}
```

</Sandpack>

<Pitfall>

পোর্টাল ব্যবহার করার সময় আপনার অ্যাপটি অ্যাক্সেসিবল হওয়া নিশ্চিত করা গুরুত্বপূর্ণ। উদাহরণস্বরূপ, আপনাকে কীবোর্ড ফোকাস পরিচালনা করতে হতে পারে যাতে ব্যবহারকারী স্বাভাবিকভাবে পোর্টালের ভিতরে এবং বাইরে ফোকাস নড়াতে পারে।

<<<<<<< HEAD
মডাল তৈরি করার সময় [WAI-ARIA Modal Authoring Practices](https://www.w3.org/WAI/ARIA/apg/#dialog_modal) অনুসরণ করুন। যদি আপনি কোনো কমিউনিটি প্যাকেজ ব্যবহার করেন, নিশ্চিত করুন যে এটি অ্যাক্সেসিবল এবং এই নির্দেশিকাগুলি অনুসরণ করে।
=======
Follow the [WAI-ARIA Modal Authoring Practices](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal) when creating modals. If you use a community package, ensure that it is accessible and follows these guidelines.
>>>>>>> 50d6991ca6652f4bc4c985cf0c0e593864f2cc91

</Pitfall>

---

### নন-React সার্ভার মার্কআপে React কম্পোনেন্টগুলি রেন্ডার করা {/*rendering-react-components-into-non-react-server-markup*/}

যদি আপনার React রুট স্ট্যাটিক বা সার্ভার-রেন্ডার করা পেইজের শুধুমাত্র একটি অংশ হয় যা React দ্বারা তৈরি নয়, তাহলে পোর্টাল উপকারী হতে পারে। উদাহরণস্বরূপ, যদি আপনার পেইজ Rails বা অন্য কোনো সার্ভার ফ্রেমওয়ার্ক দ্বারা তৈরি হয়, আপনি সাইডবারের মতো স্ট্যাটিক অঞ্চলের মধ্যে ইন্টার‌্যাক্টিভ এলাকা তৈরি করতে পারেন। [আলাদা আলাদা React রুটের](/reference/react-dom/client/createRoot#rendering-a-page-partially-built-with-react) তুলনায়, পোর্টালগুলি আপনাকে অ্যাপটিকে একটি একক React ট্রি হিসাবে আচরণ করতে দেয় যার shared স্টেট রয়েছে, যদিও এর অংশগুলি DOM-এর ভিন্ন ভিন্ন অংশে রেন্ডার হয়।

<Sandpack>

```html public/index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <h1>Welcome to my hybrid app</h1>
    <div class="parent">
      <div class="sidebar">
        This is server non-React markup
        <div id="sidebar-content"></div>
      </div>
      <div id="root"></div>
    </div>
  </body>
</html>
```

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import App from './App.js';
import './styles.css';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js src/App.js active
import { createPortal } from 'react-dom';

const sidebarContentEl = document.getElementById('sidebar-content');

export default function App() {
  return (
    <>
      <MainContent />
      {createPortal(
        <SidebarContent />,
        sidebarContentEl
      )}
    </>
  );
}

function MainContent() {
  return <p>This part is rendered by React</p>;
}

function SidebarContent() {
  return <p>This part is also rendered by React!</p>;
}
```

```css
.parent {
  display: flex;
  flex-direction: row;
}

#root {
  margin-top: 12px;
}

.sidebar {
  padding:  12px;
  background-color: #eee;
  width: 200px;
  height: 200px;
  margin-right: 12px;
}

#sidebar-content {
  margin-top: 18px;
  display: block;
  background-color: white;
}

p {
  margin: 0;
}
```

</Sandpack>

---

### নন-React DOM নোডে React কম্পোনেন্টগুলি রেন্ডার করা {/*rendering-react-components-into-non-react-dom-nodes*/}

আপনি একটি পোর্টাল ব্যবহার করেও React এর বাইরে পরিচালিত DOM নোডের কন্টেন্ট পরিচালনা করতে পারেন। উদাহরণস্বরূপ, ধরুন আপনি একটি নন-React ম্যাপ উইজেটের সাথে সংযোজন করছেন এবং আপনি একটি পপআপের মধ্যে React কন্টেন্ট রেন্ডার করতে চান। এটি করতে, `popupContainer` নামক একটি স্টেট ভেরিয়েবল ঘোষণা করুন যেখানে আপনি রেন্ডার করতে যাচ্এঁরন সেই DOM নোডটি সংরক্ষণ করবেন

```js
const [popupContainer, setPopupContainer] = useState(null);
```

যখন আপনি থার্ড-পার্টি উইজেট তৈরি করবেন, উইজেট দ্বারা ফেরত আসা DOM নোডটি সংরক্ষণ করুন যাতে আপনি এতে রেন্ডার করতে পারেন:

```js {5-6}
useEffect(() => {
  if (mapRef.current === null) {
    const map = createMapWidget(containerRef.current);
    mapRef.current = map;
    const popupDiv = addPopupToMapWidget(map);
    setPopupContainer(popupDiv);
  }
}, []);
```

এটি আপনাকে `createPortal` ব্যবহার করে `popupContainer`-এ React কন্টেন্ট রেন্ডার করতে দেয় যখন এটি available হয়:

```js {3-6}
return (
  <div style={{ width: 250, height: 250 }} ref={containerRef}>
    {popupContainer !== null && createPortal(
      <p>Hello from React!</p>,
      popupContainer
    )}
  </div>
);
```

এখানে একটি সম্পূর্ণ উদাহরণ রয়েছে যা নিয়ে আপনি ঘাটাঘাটি করতে পারেন:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "leaflet": "1.9.1",
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "remarkable": "2.0.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js src/App.js
import { useRef, useEffect, useState } from 'react';
import { createPortal } from 'react-dom';
import { createMapWidget, addPopupToMapWidget } from './map-widget.js';

export default function Map() {
  const containerRef = useRef(null);
  const mapRef = useRef(null);
  const [popupContainer, setPopupContainer] = useState(null);

  useEffect(() => {
    if (mapRef.current === null) {
      const map = createMapWidget(containerRef.current);
      mapRef.current = map;
      const popupDiv = addPopupToMapWidget(map);
      setPopupContainer(popupDiv);
    }
  }, []);

  return (
    <div style={{ width: 250, height: 250 }} ref={containerRef}>
      {popupContainer !== null && createPortal(
        <p>Hello from React!</p>,
        popupContainer
      )}
    </div>
  );
}
```

```js src/map-widget.js
import 'leaflet/dist/leaflet.css';
import * as L from 'leaflet';

export function createMapWidget(containerDomNode) {
  const map = L.map(containerDomNode);
  map.setView([0, 0], 0);
  L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap'
  }).addTo(map);
  return map;
}

export function addPopupToMapWidget(map) {
  const popupDiv = document.createElement('div');
  L.popup()
    .setLatLng([0, 0])
    .setContent(popupDiv)
    .openOn(map);
  return popupDiv;
}
```

```css
button { margin: 5px; }
```

</Sandpack>
