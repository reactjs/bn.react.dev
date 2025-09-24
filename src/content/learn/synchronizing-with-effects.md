---
title: 'Effects দিয়ে Synchronizing'
---

<Intro>

কিছু কম্পোনেন্টকে বাইরের কোনো সিস্টেমের সাথে সিংক্রোনাইজ করতে হতে পারে। উদাহরণস্বরূপ, আপনি non-React কম্পোনেন্টকে React state এর উপর নির্ভর করে নিয়ন্ত্রণ করতে চাইতে পারেন, একটি সার্ভার সংযোগ স্থাপন করতে, বা যখন একটি কম্পোনেন্ট স্ক্রিনে দেখা যায় তখন একটি analytics লগ পাঠাতে চাইতে পারেন। *Effect* আপনাকে রেন্ডারের পর কিছু কোড রান করার সুযোগ দেয় যাতে আপনি আপনার কম্পোনেন্টটি React এর বাইরে কোন সিস্টেম এর সঙ্গে সিংক্রোনাইজ করতে পারেন।

</Intro>

<YouWillLearn>

- Effects কী  
- Effects কীভাবে events থেকে আলাদা 
- কীভাবে আপনার কম্পোনেন্টে Effect ডিক্লেয়ার করবেন 
- কীভাবে অকারণে কোন Effect রি-রানিং এড়াবেন
- কেনো ডেভেলপমেন্টের সময় Effects দুইবার রান হয় এবং সেগুলো কীভাবে ঠিক করবেন

</YouWillLearn>

## Effects কী এবং কীভাবে তারা events থেকে আলাদা? {/*what-are-effects-and-how-are-they-different-from-events*/}

Effects সম্পর্কে শুরুর আগে, আপনার রিয়েক্ট কম্পোনেন্টের ভেতরের দুই প্রকার লজিকের সাথে পরিচিত থাকতে হবে:

- **Rendering code** (যা [UI এর বর্ণনা](/learn/describing-the-ui) অধ্যায়ে পরিচয় দেওয়া হয়েছে) আপনার কম্পোনেন্টের টপ লেভেলে থাকে। এটি সেখানে থাকে, যেখানে আপনি props এবং state নিয়ে, তাদের পরিবর্তন করেন, এবং আপনি যে JSX স্ক্রিনে দেখতে চান তা রিটার্ন করেন। [Rendering code অবশ্যই পিওর হতে হবে।](/learn/keeping-components-pure) একটি গণিত সূত্রের মতো, এটি শুধু ফলাফল _হিসাব_ করবে, কিন্তু অন্য কিছু না।

- **Event handlers** (যা [Adding Interactivity](/learn/adding-interactivity) অধ্যায়ে পরিচয় দেওয়া হয়েছে) হলো আপনার কম্পোনেন্টের ভিতরের নেস্টেড ফাংশন যা বিভিন্ন জিনিস *করে*, শুধুমাত্র হিসাব করাই এর কাজ না। এটি যে কাজগুলো করতে পারে সেগুলো হতে পারে একটি ইনপুট ফিল্ড আপডেট করা, একটি পণ্য কিনতে HTTP POST request দেওয়া, অথবা ইউজারকে অন্য একটি স্ক্রিনে navigate করা। Event handler এ ["side effects"](https://en.wikipedia.org/wiki/Side_effect_(computer_science)) থাকে (এগুলো program এর স্টেট পরিবর্তন করে) যা ইউজারের নির্দিষ্ট কোনো ক্রিয়ার দ্বারা ঘটে (উদাহরণস্বরূপ  button click অথবা typing)।

কখনো কখনো এটা যথেষ্ট নয়। একটি `ChatRoom` কম্পোনেন্ট এর ব্যাপারে ভাবুন যেটি যখনই স্ক্রিনে দৃশ্যমান হয় তখনই তাকে চ্যাট সার্ভারের সাথে কানেক্ট করতে হয়। একটি সার্ভারের সাথে কানেক্ট করা pure calculation নয় (এটি একটি side effect) তাই এটি রেন্ডার এর সময় সম্পন্ন হতে পারবে না। তবে, click এর মত কোন নির্দিষ্ট event নেই যার কারণে `ChatRoom` প্রদর্শিত হয়।

***Effects* আপনাকে নির্দিষ্ট কোনো Event-এর মাধ্যমে নয়, বরং রেন্ডারিং দ্বারা সৃষ্ট side effect গুলো নির্ধারণ করতে দেয়।** চ্যাটে message পাঠানো একটি *event* কারণ এটি সরাসরি একজন ইউজারের দ্বারা একটি নির্দিষ্ট বাটনে ক্লিক করার মাধ্যমে ঘটে। তবে, সার্ভারের সাথে কানেকশন সেটআপ করা একটি *Effect* কারণ এটা (অর্থাৎ সার্ভারের সাথে কানেক্টেড হওয়া) সম্পন্ন হতেই হবে, যে ইন্টার‍্যাকশনের কারণেই কম্পোনেন্ট প্রদর্শিত হোক না কেনো তাতে কিছু আসে যায় না। Effects স্ক্রিন আপডেটের পরে একটি [commit](/learn/render-and-commit) এর শেষে রান করে। কিছু external system (যেমন network অথবা একটি third-party library) এর সাথে React component গুলো synchronize করার জন্য এটি একটি ভালো সময়।

<Note>

এই পেজে এখানে এবং সামনে, বড় হাতের E যুক্ত "Effect" উপরের React-specific সংজ্ঞা বোঝায়, অর্থাৎ রেন্ডারিংয়ের ফলে সৃষ্ট side effect। বিস্তৃত এই প্রোগ্রামিং concept টি বুঝাতে, আমরা এটিকে "side effect" বলবো।

</Note>


## আপনার কোন Effect প্রয়োজন নাও হতে পারে {/*you-might-not-need-an-effect*/}

**অপ্রয়োজনে আপনার component এ Effects অ্যাড করবেন না।** মনে রাখবেন যে Effect সাধারণত আপনার React কোডের থেকে "step out" করতে এবং কিছু *এক্সটার্নাল* সিস্টেমের সাথে synchronize (সামঞ্জস্য তৈরি) করতে ব্যবহৃত হয়। এর মধ্যে রয়েছে browser APIs, third-party widgets, network এবং আরও অনেক কিছু। যদি আপনার Effect টি কেবল অন্য state এর উপর ভিত্তি করে কিছু state কে সামঞ্জস্য করে, [তবে আপনার কোন Effect প্রয়োজন নাও হতে পারে।](/learn/you-might-not-need-an-effect)

## কীভাবে একটি Effect লিখবেন {/*how-to-write-an-effect*/}

একটি Effect লিখতে, এই তিনটি ধাপ অনুসরণ করুনঃ 

1. **Effect ডিক্লেয়ার করুন** By default, আপনার Effect প্রত্যেক [commit](/learn/render-and-commit) এর পর রান করবে।
2. **Effect এর dependency গুলো specify করুন** বেশিরভাগ Effects প্রত্যেকবার রেন্ডার হওয়ার পরে re-run হওয়ার থেকে *যখন প্রয়োজন* তখন re-run হওয়া উচিত। উদাহরণস্বরূপ, একটি fade-in animation কেবল তখনি ট্রিগার করা উচিত যখন কোন একটি component দৃশ্যমান হয়। কোন chat room এর সাথে সংযোগ স্থাপন এবং বিচ্ছিন্ন তখনই ঘটে যখন component টি দৃশ্যমান এবং অদৃশ্যমান হয়ে যায় বা যখন chat room টি পরিবর্তন হয়। আপনি কীভাবে *dependencies* specify করার মাধ্যমে এটি কন্ট্রোল করতে পারবেন তা শিখতে পারবেন।
3. **প্রয়োজনে cleanup অ্যাড করুন** কিছু Effects কীভাবে থামানো হবে, undo হবে বা এগুলো যা করছে তা clean up করতে হবে তা specify করে দিতে হয়। উদাহরণস্বরূপ, "connect" এর জন্য প্রয়োজন "disconnect", "subscribe" এর জন্য "unsubscribe", and "fetch" এর জন্য প্রয়োজন হয়ত "cancel" অথবা "ignore"। আপনি একটি *cleanup function* রিটার্ন করে কীভাবে এটি করবেন তা শিখবেন।

আসুন, এবার প্রতিটি ধাপ বিস্তারিত দেখি।

### ধাপ ১: একটি Effect ডিক্লেয়ার {/*step-1-declare-an-effect*/}

আপনার component এ কোন Effect ডিক্লেয়ার করতে, [`useEffect` হুক](/reference/react/useEffect) React থেকে import করুন:

```js
import { useEffect } from 'react';
```

এরপরে, এটিকে আপনার component এর top level এ call করুন এবং Effects এর মধ্যে কিছু code রাখুন।

```js {2-4}
function MyComponent() {
  useEffect(() => {
    // Code here will run after *every* render
  });
  return <div />;
}
```

প্রতিবার যখন component রেন্ডার করবে, React স্কিন আপডেট করবে *এবং এর পরে* `useEffect` এর ভিতরের কোড রান করবে। অর্থাৎ, **`useEffect` এক টুকরা কোড রান হতে " বিলম্ব করায় " যতক্ষণ না রেন্ডারটি স্কিনে reflected হয়।**

চলুন দেখা যাক কীভাবে আপনি Effect ব্যবহার করে একটি external system এর সাথে synchronize করবেন। একটি `<VideoPlayer>` React component এর কথা চিন্তা করুন। এটি কন্ট্রোল করতে ভালো হবে যদি এটিতে একটি `isPlaying` প্রপস পাঠানো হয় যে এটি চালু আছে অথবা বন্ধ:

```js
<VideoPlayer isPlaying={isPlaying} />;
```

আপনার কাস্টম `VideoPlayer` component টি ব্রাউজারের built-in [`<video>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video) tag রেন্ডার করে:

```js
function VideoPlayer({ src, isPlaying }) {
  // TODO: do something with isPlaying
  return <video src={src} />;
}
```

তবে, browser এর `<video>` tag এ `isPlaying` প্রপস নাই। এটি নিয়ন্ত্রণের একমাত্র উপায় হলো DOM element টিতে ম্যানুয়ালি [`play()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play) এবং  [`pause()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause) call করা। **আপনাকে `isPlaying` প্রপ্স এর value টি synchronize করতে হবে, যা `play()` এবং `pause()`কে কল করে video টি বর্তমানে বাজানো উচিত কিনা তা নির্দেশ করে।**

আমাদের প্রথমে `<video>` DOM node এর একটি [ref পেতে হবে](/learn/manipulating-the-dom-with-refs)।

রেন্ডারিং এর সময় আপনি `play()` অথবা `pause()` কল করার চেষ্টা করতে পারেন, তবে এটি সঠিক নয়:

<Sandpack>

```js {expectedErrors: {'react-compiler': [7, 9]}}
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  if (isPlaying) {
    ref.current.play();  // Calling these while rendering isn't allowed.
  } else {
    ref.current.pause(); // Also, this crashes.
  }

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  return (
    <>
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 20px; }
video { width: 250px; }
```

</Sandpack>

এই কোডটি সঠিক না হওয়ার কারণ হলো এটি রেন্ডারিং এর সময় DOM node এর সাথে কিছু একটা করার চেষ্টা করে। React এ, [রেন্ডারিং JSX এর pure calculation হওয়া উচিত](/learn/keeping-components-pure) এবং DOM কে modify করে এমন কোন side effects থাকা উচিত নয়।

উপরন্তু, যখন `VideoPlayer` কে প্রথমবারের জন্য call করা হয়, এটির DOM তখন exist করে না! `play()` বা `pause()` করার জন্য এখানে কোন DOM node নাই, কারণ React জানে না কী DOM তৈরি হবে যতক্ষণ না আপনি JSX রিটার্ন করেন। 

এখানে সমাধানটি হলো **রেন্ডারিং calculation এর বাইরে সরানোর জন্য `useEffect` এর দ্বারা side effect টি wrap করে রাখা:**

```js {6,12}
import { useEffect, useRef } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  });

  return <video ref={ref} src={src} loop playsInline />;
}
```

DOM update কে একটি Effect দিয়ে wrap করার মাধ্যমে, আপনি প্রথমে React কে screen টি আপডেট করতে দিন। এরপরে আপনার Effect রান হবে।

যখন আপনার `VideoPlayer` component টি রেন্ডার করে (হয় প্রথমবার বা যদি এটি পুনরায় রেন্ডার করে), কয়েকটি জিনিস ঘটবে। প্রথমে, React স্কিন আপডেট করবে, `<video>` tag টি সঠিক প্রপস সহ DOM এ আছে কিনা তা নিশ্চিত করবে । তারপরে React আপনার Effect চালাবে। অবশেষে, আপনার Effect টি `isPlaying` এর মানের উপর depend করে `play()` বা `pause()` কল করবে।

Play/Pause একাধিকবার চাপুন এবং দেখুন video player কীভাবে `isPlaying` এর value তে synchronize থাকে:

<Sandpack>

```js
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  });

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  return (
    <>
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 20px; }
video { width: 250px; }
```

</Sandpack>

এই উদাহরণে, আপনি যে "external system" React state এর সাথে synchronize করেছেন তা হলো ব্রাউজার মিডিয়া API। আপনি legacy non-React code (যেমন jQuery plugins) থেকে declarative React component এ wrap করতে  অনুরূপ পদ্ধতি ব্যবহার করতে পারেন। 

মনে রাখবেন যে কোন ভিডিও প্লেয়ার কন্ট্রোল করা প্রাক্টিকালি আরও জটিল। `play()` কল fail হতে পারে, user built-in ব্রাউজার control গুলো ব্যবহার করে play বা pause করতে পারে, এবং আরও অনেক কিছু। এই উদাহরণটি খুবই সহজ এবং অসম্পূর্ণ।

<Pitfall>

By default, Effect গুলো *প্রত্যেক* রেন্ডারের পরে run হয়। এ কারণেই এ জাতীয় কোড **infinite loop তৈরি করে:**

```js
const [count, setCount] = useState(0);
useEffect(() => {
  setCount(count + 1);
});
```

রেন্ডারিং এর *ফলস্বরূপ* Effect চলে। state সেট করা রেন্ডারিং *ট্রিগার করে*। একটি Effect এ সঙ্গে সঙ্গে state সেট করা যেন একটি মাল্টিপ্লাগকে তার নিজের মধ্যেই প্লাগ করা। Effect run হয়, এটি state সেট করে, যা একটি re-render তৈরি করে, যার ফলে Effect টি run হয়, এটি আবার state টি সেট করে, এটি অন্য একটি re-render তৈরি করে, আর এভাবেই চলতে থাকে।

Effect গুলো সাধারণত আপনার component গুলোকে একটি *external* system এর সাথে synchronize করে। যদি কোন external system না থাকে এবং আপনি কেবল অন্য state এর উপর ভিত্তি করে কিছু state এডজাস্ট করতে চান, [আপনার কোন Effect প্রয়োজন নাও হতে পারে।](/learn/you-might-not-need-an-effect)

</Pitfall>

### ধাপ ২: Effect এর dependency গুলো নির্দিষ্ট করুন {/*step-2-specify-the-effect-dependencies*/}

By default,  Effect গুলো *প্রত্যেক* রেন্ডারের পরে run হয়। অনেক সময়, এটি **আপনি চান না:**

- কখনো কখনো, এটি slow কাজ করে। একটি external system এর সাথে Synchroniz করা সর্বদা তাৎক্ষণিক হয় না, সুতরাং আপনি এটি প্রয়োজন না হলে এটি এড়িয়ে যেতে চাইতে পারেন। উদাহরণস্বরূপ, আপনি প্রতি keystoke এ চ্যাট সার্ভারের সাথে পুনরায় সংযোগ স্থাপন করতে চান না। 
- কখনো কখনো, এটি ভুল।  উদাহরণস্বরূপ, আপনি প্রতিটি keystroke এ কোন component ফেড-ইন animation ট্রিগার করতে চান না। component টি প্রথমবারের মত appear হলে animation টি কেবল একবার play হওয়া উচিত।

সমস্যাটি প্রদর্শনের করতে, এখানে কয়েকটি `console.log` কল এবং একটি টেক্সট ইনপুট সহ পূর্ববর্তী উদাহরণটি যেটি parent component এর স্টেটকে update করে । খেয়াল করুন কীভাবে typing এর ফলে Effect টি re-run হয়:

<Sandpack>

```js
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      console.log('Calling video.play()');
      ref.current.play();
    } else {
      console.log('Calling video.pause()');
      ref.current.pause();
    }
  });

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  const [text, setText] = useState('');
  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

```css
input, button { display: block; margin-bottom: 20px; }
video { width: 250px; }
```

</Sandpack>

আপনি `useEffect` এর দ্বিতীয় আর্গুমেন্ট হিসাবে *dependency* এর একটি array specify করে React কে **অপ্রয়োজনীয়ভাবে Effect টি re-running এড়িয়ে** যেতে বলতে পারেন। উপরের উদাহরণের ১৪ লাইনে একটি খালি `[]` array যুক্ত করে শুরু করুন:

```js {3}
  useEffect(() => {
    // ...
  }, []);
```

আপনি একটি error দেখতে পাবেন যে `React Hook useEffect has a missing dependency: 'isPlaying'`:

<Sandpack>

```js
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      console.log('Calling video.play()');
      ref.current.play();
    } else {
      console.log('Calling video.pause()');
      ref.current.pause();
    }
  }, []); // This causes an error

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  const [text, setText] = useState('');
  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

```css
input, button { display: block; margin-bottom: 20px; }
video { width: 250px; }
```

</Sandpack>

সমস্যাটি হলো আপনার Effect এর মধ্যের কোড কি করবে তা সিদ্ধান্ত নেওয়ার জন্য `isPlaying` প্রপসের উপর *নির্ভর করে*, কিন্তু এই dependency টি স্পষ্টভাবে declare করা হয়নি। এই সমস্যাটির সমাধান করতে, dependency array তে `isPlaying` যুক্ত করুন:

```js {2,7}
  useEffect(() => {
    if (isPlaying) { // It's used here...
      // ...
    } else {
      // ...
    }
  }, [isPlaying]); // ...so it must be declared here!
```

এখন সকল dependency গুলো declare করা হয়ে গেছে, সুতরাং কোন error নাই। `[isPlaying]` কে dependency array তে রাখার মানে হলো React কে বলা যে যদি `isPlaying` এর মান আগের রেন্ডারে যেমন ছিল তেমন থাকে তবে re-running স্কিপ করতে। এই পরিবর্তনের কারণে, ইনপুট ফিল্ডটিতে টাইপ করালেও Effect টি re-run হয় না, কিন্তু Play/Pause বাটনে press করলে হয়:

<Sandpack>

```js
import { useState, useRef, useEffect } from 'react';

function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);

  useEffect(() => {
    if (isPlaying) {
      console.log('Calling video.play()');
      ref.current.play();
    } else {
      console.log('Calling video.pause()');
      ref.current.pause();
    }
  }, [isPlaying]);

  return <video ref={ref} src={src} loop playsInline />;
}

export default function App() {
  const [isPlaying, setIsPlaying] = useState(false);
  const [text, setText] = useState('');
  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={() => setIsPlaying(!isPlaying)}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <VideoPlayer
        isPlaying={isPlaying}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
      />
    </>
  );
}
```

```css
input, button { display: block; margin-bottom: 20px; }
video { width: 250px; }
```

</Sandpack>

dependency array তে একাধিক dependency থাকতে পারে। যদি *সবগুলো* dependency এর value গুলো previous render এর মতই থাকে কেবল তখনই React Effect টি re-runn করবে না। React dependency value গুলোকে তুলনা করতে [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) comparison ব্যবহার করে। বিস্তারিত জানতে [`useEffect` reference](/reference/react/useEffect#reference) দেখুন। 

**লক্ষ্য করুন যে আপনি আপনার dependency গুলো "choose" করতে পারছেন না।** আপনি যে dependency গুলো specify করেছেন তা যদি আপনি Effect এর মধ্যে যে কোড রেখেছেন তার উপর base করে React এর expectation এর সাথে না মিলে তাহলে আপনি একটি lint error পাবেন। এটি আপনার কোডে অনেক bug খুঁজে পাতে সাহায্য করে । যদি আপনি কিছু কোড re-run করতে না চান, [*Effect কোড edit করুন* যাতে ঐ  dependency-র "প্রয়োজন" না হয়।](/learn/lifecycle-of-reactive-effects#what-to-do-when-you-dont-want-to-re-synchronize)

<Pitfall>

dependency array ছাড়া এবং একটি *empty* `[]` dependency array সহ এদের behavior আলাদা হয়ে থাকে:

```js {3,7,11}
useEffect(() => {
  // This runs after every render
});

useEffect(() => {
  // This runs only on mount (when the component appears)
}, []);

useEffect(() => {
  // This runs on mount *and also* if either a or b have changed since the last render
}, [a, b]);
```

আমরা পরবর্তী step এ "mount" এর মানে কী তা ভালোভাবে দেখবো।

</Pitfall>

<DeepDive>

#### dependency array থেকে কেন ref বাদ দেওয়া হয়েছিল? {/*why-was-the-ref-omitted-from-the-dependency-array*/}

এই Effect টিতে `ref` এবং `isPlaying` উভয়ই ব্যবহার হচ্ছে, কিন্তু কেবল `isPlaying` কে dependency হিসাবে ডিক্লেয়ার করা হয়েছে:

```js {9}
function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);
  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }, [isPlaying]);
```
এর কারণ হল `ref` object এর একটি *stable identity* রয়েছে: React গ্যারান্টি দেয় যে [প্রতি রেন্ডারে একই `useRef` কল থেকে সর্বদা একই object পাবেন](/reference/react/useRef#returns)। এটি কখনো পরিবর্তন হয় না, সুতরাং  এটি নিজেই Effect টি re-run হওয়ার কারণ হতে পারেনা। অতএব, এটি বিবেচ্য বিষয় নয় যে আপনি এটি include করছেন কি করেন নাই।  এটি Includ করাও ঠিক আছে:

```js {9}
function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);
  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }, [isPlaying, ref]);
```

`useState` দ্বারা রিটার্ন করা [`set` function গুলোরও](/reference/react/useState#setstate) stable identity রয়েছে, তাই আপনি প্রায়ই দেখতে পাবেন তাদের dependencies থেকে বাদ দেওয়া হয়েছে। যদি lint আপনাকে error ছাড়াই dependency বাদ দিতে দেয়, তবে এটি করা নিরাপদ।

always-stable dependency বাদ দেওয়া তখনই কাজ করে যখন linter "দেখতে" পারে যা object টি stable। উদাহরণস্বরূপ, যদি কোন parent component থেকে `ref` pass করা হয়, আপনাকে একটি dependency array specify করতে হবে। যাইহোক, এটি ভালো কারণ আপনি জানতে পারবেন না যে parent component সবসময় একই রেফ পাস করে কিনা, অথবা শর্তসাপেক্ষে বেশ কয়েকটি রেফের একটি পাস করে কিনা। সুতরাং আপনার Effect নির্ভর _করবে_ কোন ref pass করা হয়েছে তার উপর।

</DeepDive>

### ধাপ ৩: প্রয়োজনে cleanup যোগ করুন {/*step-3-add-cleanup-if-needed*/}

একটি ভিন্ন উদাহরণ বিবেচনা করুন। আপনি একটি `ChatRoom` component লিখেছেন যা এটি প্রদর্শিত হওয়ার সময় chat server এর সাথে সংযোগ স্থাপন করা দরকার। আপনাকে একটি `createConnection()` API দেওয়া হয়েছে যেটি `connect()` এবং `disconnect()` method এর একটি object রিটার্ন করে। user এর কাছে প্রদর্শিত হওয়ার সময় আপনি কীভাবে component টিকে সংযুক্ত রাখবেন?

Effect logic লিখে শুরু করুন:

```js
useEffect(() => {
  const connection = createConnection();
  connection.connect();
});
```

প্রত্যেকবার re-render এর পরে chat এর সাথে সংযোগ স্থাপন করা ধীর হবে, সুতরাং আপনি dependency array যুক্ত করুন:

```js {4}
useEffect(() => {
  const connection = createConnection();
  connection.connect();
}, []);
```

**Effect এর ভিতরের কোড কোনো props or state ব্যবহার করে না, সুতরাং আপনার dependency array টি `[]` (empty)। এটি React কে শুধুমাত্র তখনই এই কোডটি চালাতে বলে যখন component টি "মাউন্ট" হয়, অর্থাৎ, প্রথমবারের জন্য স্কিনে উপস্থিত হয়।**

আসুন code টি রান করার চেষ্টা করি:

<Sandpack>

```js
import { useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
  }, []);
  return <h1>Welcome to the chat!</h1>;
}
```

```js src/chat.js
export function createConnection() {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting...');
    },
    disconnect() {
      console.log('❌ Disconnected.');
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
```

</Sandpack>

এই Effect টি কেবল মাউন্ট হওয়ার সময় চলে, সুতরাং আপনি প্রত্যাশা করতে পারেন console একবার `"✅ Connecting..."` প্রিন্ট হবে। **তবে, আপনি যদি console চেক করেন, দেখবেন `"✅ Connecting..."` দুই বার প্রিন্ট হয়েছে। কেন এমন হচ্ছে?**

কল্পনা করুন যে `ChatRoom` এর component টি অনেক গুলো ভিন্ন ভিন্ন স্কিন সহ একটি বৃহত্তর app এর একটি অংশ। ব্যবহারকারী তাদের journey শুরু করে `ChatRoom` পেইজ দিয়ে। component টি মাউন্ট করে এবং `connection.connect()` কে কল করে। তারপরে কল্পনা করুন যে ব্যবহারকারী অন্য স্কিনে নেভিগেট করেছে --উদাহরণস্বরূপ, Settings পেইজে। এখন `ChatRoom` এর component আনমাউন্ট। অবশেষে, ব্যবহারকারী Back এ ক্লিক করে এবং `ChatRoom` টি আবার মাউন্ট করে। এটি একটি second connection স্থাপন করবে--তবে প্রথম connection টি কখনই বিচ্ছিন্ন হয়নি! ব্যবহারকারী অ্যাপ জুড়ে নেভিগেট করার সাথে সাথে সংযোগগুলি pulling হতে থাকবে।

এই ধরনের বাগগুলি ব্যাপক ম্যানুয়াল পরীক্ষা ছাড়া সহজই মিস হয়ে যায়। আপনাকে দ্রুত সেগুলি শনাক্ত করতে সহায়তা করার জন্য, React development এ প্রতিটি component কে তার প্রাথমিক মাউন্টের পরপরই পুনরায় মাউন্ট করে।

`"✅ Connecting..."` দু'বার log হচ্ছে দেখা আপনাকে আসল সমস্যাটি লক্ষ্য করতে সাহায্য করে: যখন component টি আনমিউট হয় আপনার কোড সংযোগটি বন্ধ করে না।

সমস্যাটি সমাধান করতে, আপনার Effect থেকে একটি *cleanup function* return করুন:

```js {4-6}
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, []);
```

Effect পুনরায় run হওয়ার আগে প্রতিবার আপনার cleanup function কে কল করবে, এবং শেষ সময় যখন component টি আনমিউট করে (রিমুভ করা হয়)। আসুন দেখা যাক যখন cleanup function টি implemente করা হয় তখন কি ঘটেঃ

<Sandpack>

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    return () => connection.disconnect();
  }, []);
  return <h1>Welcome to the chat!</h1>;
}
```

```js src/chat.js
export function createConnection() {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting...');
    },
    disconnect() {
      console.log('❌ Disconnected.');
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
```

</Sandpack>

এখন আপনি development এ তিনটি console log পাবেন:

1. `"✅ Connecting..."`
2. `"❌ Disconnected."`
3. `"✅ Connecting..."`

**এটি development এর সঠিক behavior।** আপনার component রিমাউন্টিং করে, React যাচাই করে যে নেভিগেট করে সামনে গিয়ে এবং পিছনে back করলে আপনার কোড ব্রেক করবে না। সংযোগ বিচ্ছিন্ন এবং তারপর আবার সংযোগ স্থাপন করলে ঠিক কি হওয়া উচিত! যখন আপনি cleanup টি ভালোভাবে implement করেন, Effect টি একবার run করা vs এটি চালাতে থাকা, এটি cleaning করা এবং পুনরায় run করার মধ্যে কোন ব্যবহারকারীর দৃশ্যমান পার্থক্য থাকা উচিত নয়। এখানে একটি অতিরিক্ত কানেক্ট/ডিসকানেক্ট কল পেয়ার আছে কারণ React ডেভেলপমেন্টে থাকা বাগগুলির জন্য আপনার কোড পরীক্ষা করছে। এটি স্বাভাবিক--এটিকে দুরে সরিয়ে দেওয়ার চেষ্টা করবেন না!

**production এ, আপনি কেবল একবার `"✅ Connecting..."` প্রিন্ট হতে দেখতে পাবেন।** component গুলো রিমাউন্টং কেবল development এর ক্ষেত্রে ঘটে যা আপনাকে এমন Effect গুলো খুঁজে পেতে সাহায্য করে যা ক্লিনাপের প্রয়োজন। আপনি development behavior থেকে বেরিয়ে আসার জন্য [Strict Mode](/reference/react/StrictMode) অফ করতে পারেন, তবে আমরা এটি চালিয়ে যাওয়ার পরামর্শ দেই। এটি আপনাকে উপরের মত অনেক গুলো বাগ খুঁজে পেতে সাহায্য করবে।

## How to handle the Effect firing twice in development? {/*how-to-handle-the-effect-firing-twice-in-development*/}

React ইচ্ছাকৃতভাবে আপনার কম্পোনেন্ট গুলোকে ডেভেলপমেন্টে রিমাউন্ট করে যাতে শেষ উদাহরণের মতো বাগ খুঁজে পাওয়া যায়। **প্রশ্ন ঠিক নয় "কীভাবে একটি Effect একবার চালাতে হয়", তবে "কীভাবে আমার Effect টি ঠিক করব যাতে এটি পুনরায় মাউন্ট করার পরে কাজ করে"।**

সাধারণত, উত্তর হল ক্লিনআপ ফাংশন implement করা। ক্লিনআপ ফাংশনটির বন্ধ করা উচিত বা Effect যা কিছু করতেছল তা পূর্বাবস্থায় ফিরিয়ে আনা উচিত। rule of thumb হল যে ব্যবহারকারী একবার Effect run হওয়া (production এ)  এবং একটি _setup → cleanup → setup_ সিকোয়েন্সের (যেমন আপনি development এ দেখতে পাবেন) মধ্যে পার্থক্য করতে সক্ষম হবে না।

আপনি যে Effect গুলো লিখবেন তার বেশিরভাগই নীচের সাধারণ প্যাটার্নগুলির মধ্যে একটিতে ফিট হবে৷

<Pitfall>

#### Effects রান করা এড়াতে refs ব্যবহার করবেন না {/*dont-use-refs-to-prevent-effects-from-firing*/}

ডেভেলপমেন্টে Effects দুইবার fire হওয়া থেকে বিরত রাখার একটি সাধারণ ভুল হলো Effect কে একবারের বেশি run হওয়া থেকে বিরত রাখতে ref ব্যবহার করা। উদাহরণস্বরূপ, আপনি উপরের bug টি useRef দিয়ে "ফিক্স" করতে পারেন।
  
```js {1,3-4}
  const connectionRef = useRef(null);
  useEffect(() => {
    // 🚩 This wont fix the bug!!!
    if (!connectionRef.current) {
      connectionRef.current = createConnection();
      connectionRef.current.connect();
    }
  }, []);
```

এটি করার ফলে আপনি development এ শুধুমাত্র একবার `"✅ Connecting..."` দেখতে পাবেন, কিন্তু এটি bug টি fix করে না।
  
যখন ইউজার navigate করে চলে যায়, connection তখনও বন্ধ হয়না এবং যখন তারা আবার ফিরে আসে, একটি নতুন connection তৈরি হয়। User যখন app জুড়ে navigate করে, connection গুলো জমতে থাকবে, ঠিক যেমনটি এই "fix" এর আগে জমতে থাকতো।

Bug টি fix করতে, শুধুমাত্র Effect কে একবার run করানোই যথেষ্ট নয়। Effect কে re-mounting এর পর কাজ করতে হবে, যার মানে হলো connection কে উপরের solution এর মতো clean up করতে হবে।
  
কমন pattern গুলো কীভাবে handle করতে হয় তার জন্য নিচের উদাহরণগুলো দেখুন।

</Pitfall>

### non-React widgets control করা {/*controlling-non-react-widgets*/}

কখনো কখনো আপনাকে UI widget অ্যাড করতে হবে যা React দিয়ে লেখা হয়নি। উদাহরণস্বরূপ, আপনি আপনার পেইজে একটি ম্যাপ component অ্যাড করেছেন। এটিতে একটি `setZoomLevel()` method রয়েছে, এবং আপনি আপনার React কোডে `zoomLevel` স্টেট variable এর সাথে zoom level সিঙ্ক রাখতে চান। আপনার Effect এটির মত দেখতে হবে:

```js
useEffect(() => {
  const map = mapRef.current;
  map.setZoomLevel(zoomLevel);
}, [zoomLevel]);
```

মনে রাখবেন যে এই ক্ষেত্রে কোন cleanup এর প্রয়োজন নেই। development এ, React দু'বার Effect কল করে, কিন্তু এটি কোন সমস্যা নয় কারণ একই value সহ `setZoomLevel` কে দু'বার কল করলে কিছুই হবে না। এটি কিছুটা স্লো হতে পারে, তবে ব্যাপার না কারণ এটি production এ অযথা রিমাউন্ট করবে না। 

কিছু API আপনাকে পরপর দুবার কল করার allow নাও দিতে পারে। উদাহরণস্বরূপ, বিল্ট-ইন [`<dialog>`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDialogElement) ইলিমেন্টটি [`showModal`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDialogElement/showModal) ম্যাথোড থ্রো করে যদি আপনি এটিকে দুবার কল করেন। cleanup function টি ইমপ্লিমেন্ট করুন এবং ডায়লগটি close করুন:

```js {4}
useEffect(() => {
  const dialog = dialogRef.current;
  dialog.showModal();
  return () => dialog.close();
}, []);
```

development এ, আপনার Effect `showModal()` কল করবে, তারপর immediately `close()` কল করবে, এবং এরপর আবার `showModal()` কল করবে। `showModal()` কে একবার কল করার মতই user-visible behavior এটির, যেমনটি আপনি production এ দেখতে পাবেন।

### Event subscribing {/*subscribing-to-events*/}

যদি আপনার Effect কোন কিছু সাবস্ক্রাইব করে, তবে ক্লিনআপ ফাংশনটির তা আনসাবস্ক্রাইব করা উচিত:

```js {6}
useEffect(() => {
  function handleScroll(e) {
    console.log(window.scrollX, window.scrollY);
  }
  window.addEventListener('scroll', handleScroll);
  return () => window.removeEventListener('scroll', handleScroll);
}, []);
```

development এ, আপনার Effect `addEventListener()` কে কল করবে, তারপর immediately `removeEventListener()` কে, এবং তারপরে আবার `addEventListener()` কে একই handler দিয়ে কল করবে। তাই এক সময়ে শুধুমাত্র একটি subscription এক্টিভ থাকবে। `addEventListener()` কে একবার কল করার মতই user-visible behavior এটির, যেমনটি আপনি production এ দেখতে পাবেন।

### animation ট্রিগার করা {/*triggering-animations*/}

যদি আপনার Effect টি কিছু animate করে, তবে আপনার ক্লিনাপ function টির উচিত initial value দিয়ে animation টি reset করা:

```js {4-6}
useEffect(() => {
  const node = ref.current;
  node.style.opacity = 1; // Trigger the animation
  return () => {
    node.style.opacity = 0; // Reset to the initial value
  };
}, []);
```

development এ, opacity সেট করা হবে `1`, এরপরে `0`, এবং তারপরে আবার `1` । এটি সরাসরি `1` এ সেট করার মতই user-visible behavior হওয়া উচিত, যা production এ ঘটবে। আপনি যদি tweening এর জন্য support সহ একটি third-party অ্যানিমেশন লাইব্রেরি ব্যবহার করেন, তবে আপনার ক্লিনআপ ফাংশনটি টাইমলাইনটিকে তার initial state পুনরায় সেট করা উচিত।

### ডাটা Fetch করা {/*fetching-data*/}

যদি আপনার Effect টি কিছু fetch করে, তবে আপনার ক্লিনাপ function টির উচিত হয়ত [fetch বাতিল করা](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) অথবা এর ফলাফল ignore করা:

```js {2,6,13-15}
useEffect(() => {
  let ignore = false;

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json);
    }
  }

  startFetching();

  return () => {
    ignore = true;
  };
}, [userId]);
```

নেটওয়ার্ক রিকুয়েস্ট যা ইতিমধ্যে ঘটে দিয়েছেন, তা আপনি "বাতিল" করতে পারবেন না, তবে আপনার ক্লিন-আপ ফাংশনটির এটি নিশ্চিত করতে হবে যে, যে fetch গুলো _আর প্রাসঙ্গিক নয়_ সেগুলো আপনার অ্যাপ্লিকেশনে প্রভাব ফেলবে না। যদি `userId` `'Alice'` থেকে `'Bob'` পরিবর্তন করে, তবে ক্লিন-আপ নিশ্চিত করবে যে `'Alice'` রেসপন্সটি `'Bob'` এর পরেও যদি আসে তাহলেও সেটি আপনার অ্যাপ্লিকেশনে প্রভাবিত করবে না।

**development এ, আপনি Network tab এ দুটি fetch দেখতে পাবেন।** এতে কোন সমস্যা নাই।  উপরের পদ্ধতিতে, প্রথম Effect-টি তাৎক্ষণিকভাবে ক্লিন-আপ হবে তাই তার `ignore` ভেরিয়েবলের কপি `true` হবে। তাই, যদিও অতিরিক্ত একটি রিকুয়েস্ট আছে, সুতরাং অতিরিক্ত অনুরোধ থাকা সত্ত্বেও, এটি state কে প্রভাবিত করবে না  `if (!ignore)` চেক কে ধন্যবাদ।

**production এ, কেবল একটি request থাকবে।** যদি development এর দ্বিতীয় request টি আপনাকে বিরক্ত করে, তবে সর্বোত্তম পদ্ধতি হল এমন একটি সমাধান ব্যবহার করা যা request গুলিকে deduplicate করবে এবং component গুলো তাদের response গুলো ক্যাশ করবে:

```js
function TodoList() {
  const todos = useSomeDataLibrary(`/api/user/${userId}/todos`);
  // ...
```

এটি শুধুমাত্র ডেভেলপমেন্ট experience ই  improve করবে না, বরং আপনার অ্যাপ্লিকেশনকে দ্রুত অনুভব করতে সাহায্য করবে।  উদাহরণস্বরূপ, ব্যবহারকারী যদি ব্যাক বোতাম চাপে, তাকে আবার কিছু ডেটা লোড করতে অপেক্ষা করতে হয় না কারণ সেটি ক্যাশ করা থাকবে। আপনি এমন একটি ক্যাশ নিজেই তৈরি করতে পারেন অথবা Effect এ ম্যানুয়াল ফেচিংয়ের জন্য অনেকগুলো alternative ব্যবহার করতে পারেন।

<DeepDive>

#### Effect ডেটা ফেচিংয়ের জন্য ভাlO Alternatives কী? {/*what-are-good-alternatives-to-data-fetching-in-effects*/}

Effect এ `fetch` কল লেখা [ডেটা ফেচিংয়ের জন্য একটি জনপ্রিয় উপায়](https://www.robinwieruch.de/react-hooks-fetch-data/), বিশেষভাবে সম্পূর্ণ ক্লায়েন্ট-সাইড অ্যাপসগুলোতে। তবে, এটি একটি অনেকটাই ম্যানুয়াল পদ্ধতি এবং এটির কিছু উল্লেখযোগ্য downside রয়েছে:

- **Effects সার্ভারে চলতে পারে না।** যার মানে, initial সার্ভার একটি লোডিং স্টেট সহ HTML রেন্ডার করবে কোনো ডেটা ছাড়াই। ক্লায়েন্ট কম্পিউটারকে সমস্ত জাভাস্ক্রিপ্ট ডাউনলোড করতে হবে এবং আপনার অ্যাপটি রেন্ডার করতে হবে শুধুমাত্র এটি discover করতে যে এটির এখন ডেটা লোড করতে হবে। এটি খুব একটা efficient না।
- **Effects এ সরাসরি ডেটা ফেচিং "নেটওয়ার্ক ওয়াটারফল" তৈরি করতে সাহায্য করে।** আপনি parent কম্পোনেন্টটি রেন্ডার করেন, এটি কিছু ডেটা ফেচ করে, চাইল্ড কম্পোনেন্টগুলো রেন্ডার হয়, এবং তারপরে তারা তাদের ডেটা ফেচ করতে শুরু করে। যদি নেটওয়ার্ক খুব ফাস্ট না হয়, এটি সব ডেটা parallel ভাবে ফেচ হওয়ার তুলনায় অনেকটাই ধীর।

- **মূলত Effect এ সরাসরি ডেটা ফেচ করার মানে এই যে, আপনি ডেটা প্রিলোড বা ক্যাশ করতে পারবেন না।** উদাহরণস্বরূপ, যদি কম্পোনেন্টটি আনমাউন্ট হয় এবং পুনরায় মাউন্ট হয়, এটিকে পুনরায় ডাটা ফেচ করতে হবে।
- **এটা খুব একটা ergonomic নয়।** ফেচ কল লেখার সময়, যাতে [রেস কন্ডিশন](https://maxrozen.com/race-conditions-fetching-data-react-with-useeffect) এর মতো বাগে suffer করতে না হয়, তার জন্য বেশ ভালো পরিমাণের বয়লারপ্লেট কোড লেখা লাগে।

ডাউনসাইডের এই তালিকাটি React জন্য নির্দিষ্ট নয়। এটি যে কোনো লাইব্রেরির মাধ্যমে মাউন্টের সময় ডেটা ফেচের ক্ষেত্রে প্রযোজ্য। রাউটিংয়ের মতো, ডেটা ফেচিং ভালোভাবে করা সহজ নয়, তাই আমরা নিম্নলিখিত পদ্ধতির পরামর্শ দিই:

- **আপনি যদি একটি [framework](/learn/start-a-new-react-project#full-stack-frameworks) ব্যবহার করেন, তার built-in ডেটা ফেচিং প্রক্রিয়া ব্যবহার করুন।** আধুনিক রিয়্যাক্ট ফ্রেমওয়ার্কগুলির মধ্যে integrated ডেটা ফেচিং প্রক্রিয়া রয়েছে যা কার্যকর এবং উপরের সমস্যা গুলো মুক্ত।
- **অন্যথায়, একটি ক্লায়েন্ট-সাইড ক্যাশ ইউজ করুন বা বিল্ড করুন।** জনপ্রিয় ওপেন সোর্স সমাধানের মধ্যে [React Query](https://tanstack.com/query/latest), [useSWR](https://swr.vercel.app/), এবং [React Router 6.4+.](https://beta.reactrouter.com/en/main/start/overview) রয়েছে। আপনি একটি নিজস্ব সমাধানো তৈরি করতে পারেন এই ক্ষেত্রে আপনি Effect গুলো আন্ডার দ্যা হুডে ব্যবহার করতে পারেন, তবে request ডিডুপ্লিকেট করাতে, response ক্যাশ করতে, এবং নেটওয়ার্ক ওয়াটারফল এড়াতে লজিক add করুন। (ডাটা প্রিলোডিং করে বা ডাটা requirement গুলো রাউটে hoisting করে)।

যদি এই পদক্ষেপগুলোর মধ্যে কোনটিই আপনার জন্য প্রযোজ্য না হয়, তবে সরাসরি Effect-এ ডেটা ফেচিং চালিয়ে যেতে পারেন।"

</DeepDive>

### Analytics সেন্ড করা {/*sending-analytics*/}

এই কোডটি একটু দেখুন যেটি কোনো পেজ ভিজিট করা হলে একটি analytics event সেন্ড করেঃ

```js
useEffect(() => {
  logVisit(url); // Sends a POST request
}, [url]);
```

ডেভেলপমেন্টের সময়, প্রতিটি URL-এর জন্য `logVisit` দুইবার কল করা হবে, এর জন্য আপনি এটি ফিক্স করতে তৎপর হয়ে উঠতে পারেন। **আমরা রেকমেন্ড করি এই কোডটি যেমন আছে তেমনই রাখতে।** আগের উদাহরণগুলোর মতো, একবার চালানো এবং দুইবার চালানোর মধ্যে কোনো *ইউজারের-কাছে-দৃশ্যমান* আচরণগত পার্থক্য নেই। বাস্তব দিক থেকে, `logVisit` ডেভেলপমেন্টের সময় কিছু করা উচিত নয় কারণ আপনি চান না যে ডেভেলপমেন্ট মেশিনগুলির লগ প্রোডাকশন metrics এর সাথে হযবরল অবস্থা হোক। আপনার কম্পোনেন্টের ফাইল যতবার আপনি সেভ করেন ততবার কম্পোনেন্টটি রিমাউন্ট করে, তাই এটি এমনিতেও ডেভেলপমেন্টের সময় অনর্থক ভিজিট লগ করে।

**প্রোডাকশনের সময়, কোনো ডুপ্লিকেট ভিজিট-লগ থাকবে না।**

আপনি যে analytics events সেন্ড করছেন তা ডিবাগ করতে, আপনি আপনার অ্যাপকে একটি staging environment-এ deploy করতে পারেন (যা প্রোডাকশন মোডে চলে) অথবা সাময়িকভাবে [Strict Mode](/reference/react/StrictMode) এবং এর development-only রিমাউন্টিং চেকগুলো থেকে opt-out করতে পারেন (বা বন্ধ করে রাখতে পারেন)। আপনি Effects এর পরিবর্তে route change event hanlder থেকেও অ্যানালিটিক্স পাঠাতে পারেন। আরো সূক্ষ্ম অ্যানালিটিক্সের জন্য, [Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) কোন কম্পোনেন্টগুলি viewport-এ আছে এবং সেগুলো কতক্ষণ দৃশ্যমান থাকে তা ট্র্যাক করতে সাহায্য করতে পারে।

### Effect হিসেবে গণ্য নয়: অ্যাপ্লিকেশন ইনিশিয়ালাইজ করা {/*not-an-effect-initializing-the-application*/}

কিছু লজিক শুধুমাত্র যখন অ্যাপ্লিকেশন শুরু হয় তখন একবার রান করা উচিত। আপনি এগুলোকে আপনার কম্পোনেন্টগুলোর বাইরে রাখতে পারেনঃ

```js {2-3}
if (typeof window !== 'undefined') { // Check if we're running in the browser.
  checkAuthToken();
  loadDataFromLocalStorage();
}

function App() {
  // ...
}
```

এটি নিশ্চিত করে যখন ব্রাউজার পেজটি লোড করবে তখন এই ধরনের লজিক শুধুমাত্র একবার রান করবে।

### Effect হিসেবে গণ্য নয়: একটি প্রোডাক্ট কেনা {/*not-an-effect-buying-a-product*/}

কখনো কখনো, আপনি একটি ক্লিনআপ ফাংশন লিখলেও, Effect-টি দুইবার চালানোর প্রভাব ইউজারের চোখে পড়া থেকে এড়ানোর কোনো উপায় থাকে না। উদাহরণস্বরূপ, হতে পারে আপনার Effect একটি প্রোডাক্ট কেনার জন্য একটি POST রিকোয়েস্ট পাঠায়ঃ

```js {2-3}
useEffect(() => {
  // 🔴 Wrong: This Effect fires twice in development, exposing a problem in the code.
  fetch('/api/buy', { method: 'POST' });
}, []);
```

আপনি প্রোডাক্টটি দুইবার কিনতে চাইবেন না। কিন্তু, আপনার এই ধরনের লজিককে কেনো একটি Effect-এ রাখা উচিত না, এটাও তার একটা কারণ। কী হবে যদি ইউজার অন্য আরেকটি পেজে যায় তারপর Back বাটন চাপে? আপনার Effect তখন আবার রান করবে। আপনি চান না যে ইউজার একটি পেজ *visit* করলেই প্রোডাক্ট কেনা হোক; আপনি চান যে ইউজার Buy বাটন *click* করলেই কেনা হোক।

Buying বা ক্রয় করা রেন্ডারিংয়ের দ্বারা হবে না বরং এটা একটি নির্দিষ্ট ইন্টারেকশনের দ্বারা হবে। এটা শুধু তখনই হবে যখন ইউজার ঐ নির্দিষ্ট বাটনটি চাপবে। **Effect-টি ডিলিট করে `/api/buy` রিকুয়েস্টটি Buy বাটনের event handler-এ সরিয়ে ফেলুনঃ**

```js {2-3}
  function handleClick() {
    // ✅ Buying is an event because it is caused by a particular interaction.
    fetch('/api/buy', { method: 'POST' });
  }
```

**এতে বুঝা যায় যে, যদি রিমাউন্টিংয়ের কারণে আপনার অ্যাপ্লিকেশনের লজিক ব্রেক করে, এটা সাধারণত এটা প্রকাশ করে যে আপনার কোডে বাগ রয়েছে।** একজন ইউজারের দৃষ্টিকোণ থেকে, একটি পেজ ভিজিট করার মধ্যে এবং সেই পেজ ভিজিট করে, একটি লিঙ্কে ক্লিক করে, তারপর ব্যাক টিপে আবার পেজ দেখার মধ্যে কোনো পার্থক্য থাকা উচিত নয়। React ডেভেলপমেন্টে একবার কম্পোনেন্টগুলিকে রিমাউন্ট করে যাচাই করে যে আপনার কম্পোনেন্টগুলি এই নীতি মেনে চলে কিনা।

## মূলকথা {/*putting-it-all-together*/}

এই প্লেগ্রাউন্ডটি আপনাকে বাস্তবে Effect কীভাবে কাজ করে তা "একটু অনুভব করতে" সাহায্য করতে পারে।

এই উদাহরণটি Effect রান করার তিন সেকেন্ড পর ইনপুটের টেক্সট নিয়ে একটি console log করার জন্য [`setTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout) ব্যবহার করছে। ক্লিন-আপ ফাংশনটি অপেক্ষারত timeout কে বাদ করে দেয়। "Mount the component" বাটনটি চাপার মাধ্যমে শুরু করুনঃ

<Sandpack>

```js
import { useState, useEffect } from 'react';

function Playground() {
  const [text, setText] = useState('a');

  useEffect(() => {
    function onTimeout() {
      console.log('⏰ ' + text);
    }

    console.log('🔵 Schedule "' + text + '" log');
    const timeoutId = setTimeout(onTimeout, 3000);

    return () => {
      console.log('🟡 Cancel "' + text + '" log');
      clearTimeout(timeoutId);
    };
  }, [text]);

  return (
    <>
      <label>
        What to log:{' '}
        <input
          value={text}
          onChange={e => setText(e.target.value)}
        />
      </label>
      <h1>{text}</h1>
    </>
  );
}

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>
        {show ? 'Unmount' : 'Mount'} the component
      </button>
      {show && <hr />}
      {show && <Playground />}
    </>
  );
}
```

</Sandpack>

আপনি প্রথমে তিনটি লগ দেখতে পাবেন: `Schedule "a" log`, `Cancel "a" log`, এবং আবার `Schedule "a" log`। তিন সেকেন্ড পরে `a` লেখা আরো একটি লগ দেখা যাবে। আপনি আগে যা শিখেছেন সে অনুযায়ী, অতিরিক্ত schedule/cancel-দ্বয়ের কারণ React ডেভেলপমেন্টে একবার কম্পোনেন্টটি পুনরায় মাউন্ট করে যাচাই করে যে আপনি ক্লিন-আপ ভালোভাবে বাস্তবায়ন করেছেন কিনা।

এখন ইনপুটটি এডিট করে `abc` লিখুন। যদি আপনি যথেষ্ট দ্রুত টাইপ করেন, তাহলে আপনি দেখবেন `Schedule "ab" log` এর পর পরই `Cancel "ab" log` এবং `Schedule "abc" log`। **React সর্বদা পরবর্তী রেন্ডারের Effect-এর আগেই পূর্ববর্তী রেন্ডারের Effect ক্লিন করে।** এই কারণে আপনি দ্রুত ইনপুটে টাইপ করলেও, একবারে সর্বাধিক একটি টাইমআউট শিডিউল করা হবে। ইনপুটটি কয়েকবার এডিট করুন এবং কনসোল দেখুন যাতে Effect-গুলো কীভাবে ক্লিন হয় তা অনুধাবন করতে পারেন।

ইনপুটে কিছু টাইপ করুন তারপর সাথে সাথেই "Unmount the component" বাটনটি চাপুন। খেয়াল করুন কীভাবে আনমাউন্ট করলে শেষ রেন্ডারের Effect-টি ক্লিনআপ হয়ে যায়। এক্ষেত্রে, এটি শেষ টাইমআউটটিকে রান করতে পারার আগেই ক্লিয়ার করে দেয়।

সবশেষে, উপরের কম্পোনেন্টটি এডিট করে ক্লিনআপ ফাংশনটিকে কমেন্ট আউট করে দিন যাতে টাইমআউটগুলো ক্যান্সেল না হয়। দ্রুত টাইপ করে `abcde` লেখার চেষ্টা করুন। তিন সেকেন্ডের মধ্যে কি ঘটবে বলে আপনি আশা করেন? টাইমআউটের ভিতরের `console.log(text)` কি *সর্বশেষ* `text` প্রিন্ট করবে এবং পাঁচটি `abcde` লগ তৈরি করবে? একটু ট্রাই করে দেখুন আপনি কতটুকু বুঝেছেন তা চেক করতে।

তিন সেকেন্ড পরে, আপনি পাঁচটি `abcde` লগের পরিবর্তে একটি লগের ধারা দেখতে পাবেন (`a`, `ab`, `abc`, `abcd`, এবং `abcde`)। **প্রত্যেক Effect তার সংশ্লিষ্ট রেন্ডার থেকে `text` ভ্যালু "ক্যাপচার" করে।** `text` স্টেট পরিবর্তন হয়ে গেছে তাতে কিছু আসে যায়নাঃ `text = 'ab'` ওয়ালা একটি রেন্ডার থেকে একটি Effect সর্বদা `'ab'`-ই দেখবে। অন্য কথায়, প্রত্যেক রেন্ডারের Effect-গুলো একে অপরের থেকে বিচ্ছিন্ন। আপনি যদি এটা কীভাবে কাজ করে তা জানতে আগ্রহী হন, তাহলে আপনি [closure](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) সম্পর্কে পড়তে পারেন।

<DeepDive>

#### প্রত্যেক রেন্ডারের নিজ নিজ Effect আছে {/*each-render-has-its-own-effects*/}

আপনি `useEffect` এর ব্যাপারে মনে করতে পারেন রেন্ডার আউটপুটের সাথে কোনো একটি ক্রিয়া "সম্পৃক্ত" করে দেয়া। এই Effect-টি দেখুনঃ

```js
export default function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Welcome to {roomId}!</h1>;
}
```

চলুন দেখি আসলে কি ঘটছে যখন ইউজার অ্যাপের মধ্যে ন্যাভিগেট করে।

#### ইনিশিয়াল রেন্ডার {/*initial-render*/}

ইউজার `<ChatRoom roomId="general" />` করলো। আপাতত [মনেকরি](/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time) `roomId` হচ্ছে `'general'`:

```js
  // JSX for the first render (roomId = "general")
  return <h1>Welcome to general!</h1>;
```

**উক্ত Effect-টি রেন্ডারিং *আউটপুটেরও* একটি অংশ।** এখন তাহলে প্রথম রেন্ডারের Effect হবেঃ

```js
  // Effect for the first render (roomId = "general")
  () => {
    const connection = createConnection('general');
    connection.connect();
    return () => connection.disconnect();
  },
  // Dependencies for the first render (roomId = "general")
  ['general']
```

React এই Effect-টি রান করে, যা `'general'` চ্যাট রুমের সাথে কানেক্ট করে।

#### একই dependency নিয়ে রি-রেন্ডার {/*re-render-with-same-dependencies*/}

ধরুন `<ChatRoom roomId="general" />` রি-রেন্ডার করলো। JSX আউটপুট সেইম-ই আছেঃ

```js
  // JSX for the second render (roomId = "general")
  return <h1>Welcome to general!</h1>;
```

React দেখে যে রেন্ডারিং আউটপুট একই আছে, তাই সে আর DOM আপডেট করে না।

দ্বিতীয় রেন্ডারের Effect-টি দেখতে এমনঃ

```js
  // Effect for the second render (roomId = "general")
  () => {
    const connection = createConnection('general');
    connection.connect();
    return () => connection.disconnect();
  },
  // Dependencies for the second render (roomId = "general")
  ['general']
```

React ২য় রেন্ডারের `['general']` এর সাথে ১ম রেন্ডারের `['general']` তুলনা করে। **যেহেতু সব dependecy একই আছে, তাই React ২য় রেন্ডারের Effect-টিকে *ইগনোর* করে।** সেটিকে কখনোই কল করা হয়না।

#### ভিন্ন dependencies নিয়ে রি-রেন্ডার {/*re-render-with-different-dependencies*/}

এরপর ধরুন, ইউজার `<ChatRoom roomId="travel" />` ভিজিট করলো। এইবার, কম্পোনেন্টটি অন্যরকম JSX রিটার্ন করেঃ

```js
  // JSX for the third render (roomId = "travel")
  return <h1>Welcome to travel!</h1>;
```

React `"Welcome to general"` কে `"Welcome to travel"` এ বদলাতে DOM আপডেট করে।

৩য় রেন্ডারের Effect দেখতে এমন দেখাবেঃ

```js
  // Effect for the third render (roomId = "travel")
  () => {
    const connection = createConnection('travel');
    connection.connect();
    return () => connection.disconnect();
  },
  // Dependencies for the third render (roomId = "travel")
  ['travel']
```

React ৩য় রেন্ডারের `['travel']` এর সাথে ২য় রেন্ডারের `['general']` এর তুলনা করে। দেখা যাচ্ছে একটি dependency বদলে গেছেঃ `Object.is('travel', 'general')` এর ফলাফল `false` হয়। তাই এই Effect-টি স্কিপ করা যাবেনা।

**তৃতীয় রেন্ডারের Effect প্রয়োগ করার আগে, React কে সর্বশেষ যে Effect _রান করেছিলো_ তা ক্লিন-আপ করতে হবে।** ২য় রেন্ডারের Effect-টি স্কিপ করা হয়েছিলো, তাই React কে প্রথম রেন্ডারের Effect ক্লিন-আপ করতে হবে। আপনি যদি উপরে স্ক্রল করে ১ম রেন্ডারের ওখানে যান, দেখবেন যে এর ক্লিন-আপ `createConnection('general')` দিয়ে তৈরি করা কানেকশনের উপর `disconnect()` কল করে। এটি অ্যাপকে `'general'` চ্যাট রুম থেকে ডিসকানেক্ট করে দেয়।

এর পরে, React ৩য় রেন্ডারের Effect রান করে এবং `'travel'` চ্যাট রুমের সাথে কানেক্ট করে।

#### আনমাউন্ট {/*unmount*/}

সবশেষে, ধরুন ইউজার অন্য পেজে চলে গেল, এবং `ChatRoom` কম্পোনেন্টটি আনমাউন্ট হলো। রিয়েক্ট তখন সর্বশেষ Effect-এর ক্লিন-আপ ফাংশনটি রান করে। সর্বশেষ Effect-টি ছিল তৃতীয় রেন্ডার থেকে। তৃতীয় রেন্ডারের ক্লিন-আপ `createConnection('travel')` কানেকশনটি কেটে দেয়। ফলে অ্যাপটি `'travel'` রুম থেকে ডিসকানেক্ট হয়ে যায়।

#### শুধুমাত্র ডেভেলপমেন্টর সময় দেখা যায় এমন কিছু আচরণ {/*development-only-behaviors*/}

যখন [স্ট্রিক্ট মোড](/reference/react/StrictMode) চালু থাকে, রিয়েক্ট প্রতিটি কম্পোনেন্টকে মাউন্ট হওয়ার পর একবার পুনঃ-মাউন্ট (remount) করে (স্টেট এবং DOM অপরিবর্তিত থাকে)। এটি আপনাকে [এমন Effect খুঁজে বের করতে সাহায্য করে যেগুলোর ক্লিন-আপ প্রয়োজন](#step-3-add-cleanup-if-needed) এবং রেস কন্ডিশনের মতো বাগ আগের থেকেই প্রকাশ করে দেয়। এছাড়াও, ডেভেলপমেন্টে যখনই আপনি কোনো ফাইল সেভ করবেন, রিয়েক্ট Effect-গুলোকে রি-মাউন্ট করবে। এই আচরণগুলো শুধুমাত্র ডেভেলপমেন্ট মোডেই দেখা যায়।

</DeepDive>

<Recap>

- Effect নিজের রেন্ডারের কারণেই ঘটে, event-এর মতো নির্দিষ্ট কোনো ইন্টার‍্যাকশনের কারণে ঘটে না।
- Effect আপনাকে একটি কম্পোনেন্টের সাথে বাইরের কোনো সিস্টেমের (থার্ড-পার্টি API, নেটওয়ার্ক ইত্যাদি) সামঞ্জস্য তৈরি করতে দেয়।
- ডিফল্ট অবস্থায়, Effect প্রত্যেক রেন্ডারের (প্রথমটা সহ) পরে রান করে।
- যদি কোনো Effect-এর সকল dependecy সর্বশেষ রেন্ডারের অবিকল থাকে তাহলে React Effect-টিকে স্কিপ করবে।
- আপনি dependencies "বাছাই করতে" পারবেন না। সেগুলো Effect-এর ভিতরের কোড দ্বারা নির্ধারিত হয়ে থাকে।
- খালি dependecy অ্যারে (`[]`) এর সম্পর্ক হলো কম্পোনেন্ট "mounting" এর সাথে অর্থাৎ, যখন সেটি স্ক্রিনে অ্যাড করা হয় তার সাথে।
- Strict Mode-এ, React আপনার Effects ভালোভাবে পরীক্ষা করার জন্য কম্পোনেন্টসমূহকে দুইবার করে মাউন্ট করে (এটা শুধু ডেভেলপমেন্টের সময়ই!)।
- যদি আপনার Effect বারংবার মাউন্টিংয়ের ফলে ব্রেক করে, এতে বুঝা যায় আপনার একটি ক্লিন-আপ ফাংশন লিখতে হবে।
- Effect পরবর্তীবার রান করার পূর্বে এবং কম্পোনেন্ট আনমাউন্টিংয়ের এর সময় React আপনার ক্লিন-আপ ফাংশন কল করে।

</Recap>

<Challenges>

#### মাউন্ট হওয়ার সময় একটি ফিল্ডে ফোকাস করুন {/*focus-a-field-on-mount*/}

এই উদাহরণে, ফর্মটি একটি `<MyInput />` কম্পোনেন্ট রেন্ডার করে।

ইনপুটের [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) মেথড ব্যবহার করে `MyInput` স্ক্রিনে দেখা যাওয়ার সাথে সাথে অটোম্যাটিকভাবে ফোকাস করান। ইতিমধ্যে একটি কমেন্ট আউট করা ইমপ্লেমেন্টেশন রয়েছে, কিন্তু এটি ঠিকভাবে কাজ করছে না। এটি কেন কাজ করছে না তা বের করুন এবং সমস্যাটি ঠিক করুন। (যদি আপনি `autoFocus` অ্যাট্রিবিউট সম্পর্কে জেনে থাকেন, তাহলে ধরে নিন যে এটির কোনো অস্তিত্ব নেইঃ আমরা একই ফাংশনালিটি পুনরায় ইমপ্লেমেন্ট করছি।)

<Sandpack>

```js src/MyInput.js active
import { useEffect, useRef } from 'react';

export default function MyInput({ value, onChange }) {
  const ref = useRef(null);

  // TODO: This doesn't quite work. Fix it.
  // ref.current.focus()

  return (
    <input
      ref={ref}
      value={value}
      onChange={onChange}
    />
  );
}
```

```js src/App.js hidden
import { useState } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const [show, setShow] = useState(false);
  const [name, setName] = useState('Taylor');
  const [upper, setUpper] = useState(false);
  return (
    <>
      <button onClick={() => setShow(s => !s)}>{show ? 'Hide' : 'Show'} form</button>
      <br />
      <hr />
      {show && (
        <>
          <label>
            Enter your name:
            <MyInput
              value={name}
              onChange={e => setName(e.target.value)}
            />
          </label>
          <label>
            <input
              type="checkbox"
              checked={upper}
              onChange={e => setUpper(e.target.checked)}
            />
            Make it uppercase
          </label>
          <p>Hello, <b>{upper ? name.toUpperCase() : name}</b></p>
        </>
      )}
    </>
  );
}
```

```css
label {
  display: block;
  margin-top: 20px;
  margin-bottom: 20px;
}

body {
  min-height: 150px;
}
```

</Sandpack>


আপনার সমাধান কাজ করছে কিনা তা যাচাই করতে, "Show form" বাটনে ক্লিক করুন এবং নিশ্চিত করুন যে ইনপুটটি ফোকাস পাচ্ছে (হাইলাইট হচ্ছে এবং কার্সর এর ভিতরে যাচ্ছে)। "Hide form" ক্লিক করুন এবং আবার "Show form" এ ক্লিক করুন। যাচাই করুন যে ইনপুটটি আবার হাইলাইট হচ্ছে কিনা।

`MyInput` শুধুমাত্র _মাউন্ট হওয়ার সময়_ ফোকাস পাবে, প্রতিটি রেন্ডারের পরে নয়। ইনপুট সঠিক আচরণ করছে কিনা তা যাচাই করতে, "Show form" এ ক্লিক করুন এবং তারপর বারবার "Make it uppercase" চেকবক্সে ক্লিক করুন। চেকবক্সে ক্লিক করলে এর উপরের ইনপুটটি ফোকাস _পাওয়ার কথা না_।

<Solution>

রেন্ডারের সময় `ref.current.focus()` কল করা ভুল, কারণ এটি একটি *side effect*। side effect হয় একটি event handler-এর মধ্যে রাখা উচিত অথবা `useEffect` এর মধ্যে ডিক্লেয়ার করা উচিত। এই ক্ষেত্রে, side effect-টি কোনো নির্দিষ্ট ইন্টার‍্যাকশনের কারণে নয়, বরং কম্পোনেন্টটি প্রদর্শিত হওয়ার কারণে ঘটে, তাই এটিকে একটি Effect-এ রাখাই যুক্তিযুক্ত।

ভুলটি সংশোধন করতে, `ref.current.focus()` কলটিকে একটি Effect ডিক্লেয়ারেশনের মধ্যে রাখুন। তারপর, এই Effect-টি যাতে শুধুমাত্র মাউন্টের সময় চলে, প্রতিটি রেন্ডারের পরে নয়, তার জন্য খালি `[]` ডিপেন্ডেন্সি যোগ করুন।

<Sandpack>

```js src/MyInput.js active
import { useEffect, useRef } from 'react';

export default function MyInput({ value, onChange }) {
  const ref = useRef(null);

  useEffect(() => {
    ref.current.focus();
  }, []);

  return (
    <input
      ref={ref}
      value={value}
      onChange={onChange}
    />
  );
}
```

```js src/App.js hidden
import { useState } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const [show, setShow] = useState(false);
  const [name, setName] = useState('Taylor');
  const [upper, setUpper] = useState(false);
  return (
    <>
      <button onClick={() => setShow(s => !s)}>{show ? 'Hide' : 'Show'} form</button>
      <br />
      <hr />
      {show && (
        <>
          <label>
            Enter your name:
            <MyInput
              value={name}
              onChange={e => setName(e.target.value)}
            />
          </label>
          <label>
            <input
              type="checkbox"
              checked={upper}
              onChange={e => setUpper(e.target.checked)}
            />
            Make it uppercase
          </label>
          <p>Hello, <b>{upper ? name.toUpperCase() : name}</b></p>
        </>
      )}
    </>
  );
}
```

```css
label {
  display: block;
  margin-top: 20px;
  margin-bottom: 20px;
}

body {
  min-height: 150px;
}
```

</Sandpack>

</Solution>

#### কন্ডিশনালি একটি ফিল্ডে ফোকাস করুন {/*focus-a-field-conditionally*/}

এই ফর্মটি দুটি `<MyInput />` কম্পোনেন্ট রেন্ডার করে।

"Show form" চাপুন এবং লক্ষ্য করুন যে দ্বিতীয় ফিল্ডটি অটোম্যাটিকভাবে ফোকাস পাচ্ছে। এটা ঘটার কারণ উভয় `<MyInput />` কম্পোনেন্টই নিজেদের ফিল্ডে ফোকাস করার চেষ্টা করে। যখন আপনি পরপর দুটি ইনপুট ফিল্ডের জন্য `focus()` কল করেন, শেষেরটি সবসময় "জিতে" যায়।

ধরুন আপনি প্রথম ফিল্ডটিকে ফোকাস করতে চান। প্রথম `MyInput` কম্পোনেন্টটি এখন একটি বুলিয়ান `shouldFocus` প্রপ পায় যা `true` সেট করা আছে। লজিকে এমন পরিবর্তন আনুন যাতে `focus()` কেবল তখনই কল করা হয় যখন `MyInput` এ পাস করা `shouldFocus` প্রপটি `true` হয়।

<Sandpack>

```js src/MyInput.js active
import { useEffect, useRef } from 'react';

export default function MyInput({ shouldFocus, value, onChange }) {
  const ref = useRef(null);

  // TODO: call focus() only if shouldFocus is true.
  useEffect(() => {
    ref.current.focus();
  }, []);

  return (
    <input
      ref={ref}
      value={value}
      onChange={onChange}
    />
  );
}
```

```js src/App.js hidden
import { useState } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const [show, setShow] = useState(false);
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  const [upper, setUpper] = useState(false);
  const name = firstName + ' ' + lastName;
  return (
    <>
      <button onClick={() => setShow(s => !s)}>{show ? 'Hide' : 'Show'} form</button>
      <br />
      <hr />
      {show && (
        <>
          <label>
            Enter your first name:
            <MyInput
              value={firstName}
              onChange={e => setFirstName(e.target.value)}
              shouldFocus={true}
            />
          </label>
          <label>
            Enter your last name:
            <MyInput
              value={lastName}
              onChange={e => setLastName(e.target.value)}
              shouldFocus={false}
            />
          </label>
          <p>Hello, <b>{upper ? name.toUpperCase() : name}</b></p>
        </>
      )}
    </>
  );
}
```

```css
label {
  display: block;
  margin-top: 20px;
  margin-bottom: 20px;
}

body {
  min-height: 150px;
}
```

</Sandpack>

আপনার সমাধান যাচাই করতে, বারবার "Show form" এবং "Hide form" বাটনে চাপ দিন। ফর্মটি প্রদর্শিত হলে, কেবল *প্রথম* ইনপুটটি ফোকাস পাওয়ার কথা। এর কারণ প্যারেন্ট কম্পোনেন্ট প্রথম ইনপুটটিকে `shouldFocus={true}` এবং দ্বিতীয় ইনপুটটিকে `shouldFocus={false}` দিয়ে রেন্ডার করে। এছাড়াও চেক করুন যে উভয় ইনপুটই এখনও কাজ করছে এবং আপনি উভয়টিতেই টাইপ করতে পারছেন।

<Hint>

আপনি কন্ডিশনালি একটি Effect ডিক্লেয়ার করতে পারবেন না, কিন্তু আপনার Effect-এর মধ্যে কন্ডিশনাল লজিক রাখতে পারেন।

</Hint>

<Solution>

কন্ডিশনাল লজিকটি Effect-এর ভিতরে রাখুন। আপনাকে `shouldFocus` কে একটি ডিপেন্ডেন্সি হিসেবে উল্লেখ করতে হবে কারণ আপনি এটি Effect-এর ভিতরে ব্যবহার করছেন। (এর মানে হল যে যদি কোনো ইনপুটের `shouldFocus` `false` থেকে `true` তে পরিবর্তিত হয়, তাহলে এটি মাউন্টের পরে ফোকাস পাবে।)

<Sandpack>

```js src/MyInput.js active
import { useEffect, useRef } from 'react';

export default function MyInput({ shouldFocus, value, onChange }) {
  const ref = useRef(null);

  useEffect(() => {
    if (shouldFocus) {
      ref.current.focus();
    }
  }, [shouldFocus]);

  return (
    <input
      ref={ref}
      value={value}
      onChange={onChange}
    />
  );
}
```

```js src/App.js hidden
import { useState } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const [show, setShow] = useState(false);
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  const [upper, setUpper] = useState(false);
  const name = firstName + ' ' + lastName;
  return (
    <>
      <button onClick={() => setShow(s => !s)}>{show ? 'Hide' : 'Show'} form</button>
      <br />
      <hr />
      {show && (
        <>
          <label>
            Enter your first name:
            <MyInput
              value={firstName}
              onChange={e => setFirstName(e.target.value)}
              shouldFocus={true}
            />
          </label>
          <label>
            Enter your last name:
            <MyInput
              value={lastName}
              onChange={e => setLastName(e.target.value)}
              shouldFocus={false}
            />
          </label>
          <p>Hello, <b>{upper ? name.toUpperCase() : name}</b></p>
        </>
      )}
    </>
  );
}
```

```css
label {
  display: block;
  margin-top: 20px;
  margin-bottom: 20px;
}

body {
  min-height: 150px;
}
```

</Sandpack>

</Solution>

#### একটি interval ফিক্স করুন যেটি দুইবার ফায়ার করে {/*fix-an-interval-that-fires-twice*/}

এই `Counter` কম্পোনেন্টটি একটি কাউন্টার প্রদর্শন করে যা প্রতি সেকেন্ডে বৃদ্ধি পাওয়ার কথা। মাউন্ট হওয়ার সময়, এটি [`setInterval`](https://developer.mozilla.org/en-US/docs/Web/API/setInterval) কল করে। এটি প্রতি সেকেন্ডে `onTick` চালায়। `onTick` ফাংশনটি কাউন্টারকে বাড়ায়।

তবে, প্রতি সেকেন্ডে একবার বাড়ানোর পরিবর্তে, এটি দুইবার বাড়ছে। কেন এমন হচ্ছে? বাগটির কারণ খুঁজে বের করুন এবং এটি ঠিক করুন।

<Hint>

মনে রাখবেন যে `setInterval` একটি ইন্টারভাল আইডি রিটার্ন করে, যা আপনি [`clearInterval`](https://developer.mozilla.org/en-US/docs/Web/API/clearInterval) এ পাস করে ইন্টারভাল বন্ধ করতে পারেন।

</Hint>

<Sandpack>

```js src/Counter.js active
import { useState, useEffect } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    function onTick() {
      setCount(c => c + 1);
    }

    setInterval(onTick, 1000);
  }, []);

  return <h1>{count}</h1>;
}
```

```js src/App.js hidden
import { useState } from 'react';
import Counter from './Counter.js';

export default function Form() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(s => !s)}>{show ? 'Hide' : 'Show'} counter</button>
      <br />
      <hr />
      {show && <Counter />}
    </>
  );
}
```

```css
label {
  display: block;
  margin-top: 20px;
  margin-bottom: 20px;
}

body {
  min-height: 150px;
}
```

</Sandpack>

<Solution>

যখন [স্ট্রিক্ট মোড](/reference/react/StrictMode) চালু থাকে (যেমন এই সাইটের স্যান্ডবক্সগুলিতে), React ডেভেলপমেন্টে প্রতিটি কম্পোনেন্টকে একবার রিমাউন্ট করে। এর ফলে ইন্টারভালটি দুইবার সেট আপ হয়, এবং এই কারণে প্রতি সেকেন্ডে কাউন্টার দুইবার বাড়ে।

তবে, React-এর এই আচরণ বাগটির *কারণ* নয়: বাগটি ইতিমধ্যেই কোডে বিদ্যমান। React-এর আচরণ বাগটিকে আরও বেশি চোখে পড়ার মতো করে তোলে। আসল কারণ হল যে এই Effect-টি একটি process শুরু করে কিন্তু এটি ক্লিন-আপ করার কোনো উপায় প্রদান করে না।

এই কোডটি ফিক্স করতে, `setInterval` দ্বারা রিটার্ন করা interval ID স্টোর করুন, এবং [`clearInterval`](https://developer.mozilla.org/en-US/docs/Web/API/clearInterval) দিয়ে একটি ক্লিনআপ ফাংশন ইমপ্লেমেন্ট করুনঃ

<Sandpack>

```js src/Counter.js active
import { useState, useEffect } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    function onTick() {
      setCount(c => c + 1);
    }

    const intervalId = setInterval(onTick, 1000);
    return () => clearInterval(intervalId);
  }, []);

  return <h1>{count}</h1>;
}
```

```js src/App.js hidden
import { useState } from 'react';
import Counter from './Counter.js';

export default function App() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(s => !s)}>{show ? 'Hide' : 'Show'} counter</button>
      <br />
      <hr />
      {show && <Counter />}
    </>
  );
}
```

```css
label {
  display: block;
  margin-top: 20px;
  margin-bottom: 20px;
}

body {
  min-height: 150px;
}
```

</Sandpack>

ডেভেলপমেন্টে, React এখনও আপনার কম্পোনেন্টটিকে একবার রিমাউন্ট করবে এটা যাচাই করতে যে আপনি ক্লিনআপ ভালোভাবে ইমপ্লেমেন্ট করেছেন কিনা। তাই একটি `setInterval` কল হবে, তারপর সাথে সাথে `clearInterval`, এবং আবার `setInterval`। প্রোডাকশনে, কেবল একটি `setInterval` কল হবে। উভয় ক্ষেত্রেই ইউজারের-কাছে-দৃশ্যমান আচরণ একই থাকবেঃ কাউন্টার প্রতি সেকেন্ডে একবার বাড়বে।

</Solution>

#### একটি Effect-এর মধ্যে fetching ফিক্স করুন {/*fix-fetching-inside-an-effect*/}

এই কম্পোনেন্টটি সিলেক্ট করা ব্যক্তির bio দেখায়। এটি মাউন্টের সময় এবং যখনই `person` পরিবর্তিত হয় তখন `fetchBio(person)` নামক একটি অ্যাসিঙ্ক্রোনাস ফাংশন কল করে bio লোড করে। সেই অ্যাসিঙ্ক্রোনাস ফাংশনটি একটি [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) রিটার্ন করে যা শেষ পর্যন্ত একটি স্ট্রিং-এ resolve হয়। ফেচিং শেষ হলে, এটি `setBio` কল করে সেই স্ট্রিংটি সিলেক্ট বক্সের নিচে প্রদর্শন করে।

<Sandpack>

{/* not the most efficient, but this validation is enabled in the linter only, so it's fine to ignore it here since we know what we're doing */}
```js {expectedErrors: {'react-compiler': [9]}} src/App.js
import { useState, useEffect } from 'react';
import { fetchBio } from './api.js';

export default function Page() {
  const [person, setPerson] = useState('Alice');
  const [bio, setBio] = useState(null);

  useEffect(() => {
    setBio(null);
    fetchBio(person).then(result => {
      setBio(result);
    });
  }, [person]);

  return (
    <>
      <select value={person} onChange={e => {
        setPerson(e.target.value);
      }}>
        <option value="Alice">Alice</option>
        <option value="Bob">Bob</option>
        <option value="Taylor">Taylor</option>
      </select>
      <hr />
      <p><i>{bio ?? 'Loading...'}</i></p>
    </>
  );
}
```

```js src/api.js hidden
export async function fetchBio(person) {
  const delay = person === 'Bob' ? 2000 : 200;
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('This is ' + person + '’s bio.');
    }, delay);
  })
}

```

</Sandpack>


এই কোডে একটি বাগ আছে। প্রথমে "Alice" সিলেক্ট করুন। তারপর "Bob" সিলেক্ট করুন এবং তার পরপরই "Taylor" সিলেক্ট করুন। আপনি যদি এটা দ্রুত করেন, তাহলে আপনি বাগটি লক্ষ্য করবেনঃ Taylor সিলেক্টেড হয়েছে, কিন্তু নিচের paragraph-এ লেখা আছে "This is Bob's bio."

এটা কেনো হচ্ছে? Effect-এর মধ্যের বাগটি ফিক্স করুন।

<Hint>

যদি একটি Effect কিছু অ্যাসিঙ্ক্রোনাসভাবে ফেচ করে, তাহলে সাধারণত এটির ক্লিনআপের প্রয়োজন হয়।

</Hint>

<Solution>

বাগটি ট্রিগার করতে, জিনিসগুলো এই ক্রমে ঘটতে হবে:

- `'Bob'` সিলেক্ট করা `fetchBio('Bob')` ট্রিগার করে
- `'Taylor'` সিলেক্ট করা `fetchBio('Taylor')` ট্রিগার করে
- **`'Taylor'` ফেচ করা `'Bob'` ফেচ করার *আগে* সম্পন্ন হয়**
- `'Taylor'` রেন্ডারের Effect `setBio('This is Taylor's bio')` কল করে
- `'Bob'` ফেচ করা সম্পন্ন হয়
- `'Bob'` রেন্ডারের Effect `setBio('This is Bob's bio')` কল করে

এই কারণেই আপনি Taylor নির্বাচিত থাকা সত্ত্বেও Bob-এর bio দেখছেন। এই ধরনের বাগগুলোকে [race condition](https://en.wikipedia.org/wiki/Race_condition) বলা হয় কারণ দুটি অ্যাসিঙ্ক্রোনাস অপারেশন একে অপরের সাথে "race" (প্রতিযোগিতা) করছে, এবং তারা অপ্রত্যাশিত ক্রমে সম্পন্ন হতে পারে।

এই রেস কন্ডিশন ফিক্স করতে, একটি ক্লিনআপ ফাংশন যোগ করুনঃ

<Sandpack>

{/* not the most efficient, but this validation is enabled in the linter only, so it's fine to ignore it here since we know what we're doing */}
```js {expectedErrors: {'react-compiler': [9]}} src/App.js
import { useState, useEffect } from 'react';
import { fetchBio } from './api.js';

export default function Page() {
  const [person, setPerson] = useState('Alice');
  const [bio, setBio] = useState(null);
  useEffect(() => {
    let ignore = false;
    setBio(null);
    fetchBio(person).then(result => {
      if (!ignore) {
        setBio(result);
      }
    });
    return () => {
      ignore = true;
    }
  }, [person]);

  return (
    <>
      <select value={person} onChange={e => {
        setPerson(e.target.value);
      }}>
        <option value="Alice">Alice</option>
        <option value="Bob">Bob</option>
        <option value="Taylor">Taylor</option>
      </select>
      <hr />
      <p><i>{bio ?? 'Loading...'}</i></p>
    </>
  );
}
```

```js src/api.js hidden
export async function fetchBio(person) {
  const delay = person === 'Bob' ? 2000 : 200;
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('This is ' + person + '’s bio.');
    }, delay);
  })
}

```

</Sandpack>

প্রতিটি রেন্ডারের Effect-এর নিজস্ব `ignore` ভ্যারিয়েবল আছে। প্রাথমিকভাবে, `ignore` ভ্যারিয়েবলটি `false` সেট করা হয়। তবে, যদি একটি Effect ক্লিন আপ করা হয় (যেমন যখন আপনি একটি ভিন্ন ব্যক্তি সিলেক্ট করেন), তার `ignore` ভ্যারিয়েবল `true` হয়ে যায়। তাই এখন রিকোয়েস্টগুলো কোন ক্রমে সম্পন্ন হয় তাতে কিছু যায় আসে না। শুধুমাত্র শেষ ব্যক্তির Effect-এর `ignore`-ই `false` সেট থাকবে, তাই এটি `setBio(result)` কল করবে। পূর্ববর্তী Effect-গুলো ইতিমধ্যে ক্লিন আপ করা হয়ে গেছে, তাই `if (!ignore)` চেক সেগুলোকে `setBio` কল করা থেকে প্রতিরোধ করবেঃ

- `'Bob'` সিলেক্ট করা `fetchBio('Bob')` ট্রিগার করে
- `'Taylor'` সিলেক্ট করা `fetchBio('Taylor')` ট্রিগার করে **এবং পূর্ববর্তী (Bob-এর) Effect ক্লিন আপ করে**
- `'Taylor'` ফেচ করা `'Bob'` ফেচ করার *আগে* সম্পন্ন হয়
- `'Taylor'` রেন্ডারের Effect `setBio('This is Taylor's bio')` কল করে
- `'Bob'` ফেচ করা সম্পন্ন হয়
- `'Bob'` রেন্ডারের Effect **কিছুই করে না কারণ এর `ignore` ফ্ল্যাগ `true` সেট করা হয়েছিল**

আউটডেটেড API কলের ফলাফল ignore করার পাশাপাশি, আপনি [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) ব্যবহার করে যেসব রিকোয়েস্টের আর প্রয়োজন নেই সেগুলো বাতিল করতে পারেন। তবে, শুধুমাত্র এটাই রেস কন্ডিশন থেকে রক্ষা করার জন্য যথেষ্ট নয়। ফেচের পরে আরও অ্যাসিঙ্ক্রোনাস ধাপ যুক্ত হতে পারে, তাই `ignore` এর মতো একটি স্পষ্ট ফ্ল্যাগ ব্যবহার করা এই ধরনের সমস্যা সমাধানের সবচেয়ে নির্ভরযোগ্য উপায়।

</Solution>

</Challenges>
