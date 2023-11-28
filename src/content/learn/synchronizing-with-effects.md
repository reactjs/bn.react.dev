---
title: 'Effects দিয়ে Synchronizing'
---

<Intro>

কিছু কম্পোনেন্ট কে বাইরের সিস্টেমের সাথে সিংক্রোনাইজ করতে হতে পারে। উদাহরণস্বরূপ, আপনি নন-রিয়েক্ট কম্পোনেন্টকে রিয়েক্ট state এর উপর নির্ভর করে নিয়ন্ত্রণ করতে চান,  একটি সার্ভার সংযোগ স্থাপন করতে চান, বা যখন একটি কম্পোনেন্ট স্ক্রিনে দেখা যায় তখন একটি বিশ্লেষণ লগ পাঠাতে চান। *Effects* আপনাকে রেন্ডার এর পর কিছু কোড রান করার সুযোগ দেয় যাতে আপনি আপনার কম্পোনেন্টটি রিয়েক্টের বাইরে কোন সিস্টেম এর সঙ্গে সিংক্রোনাইজ করতে পারেন।

</Intro>

<YouWillLearn>

- Effects কী  
- কীভাবে Effect গুলো events থেকে আলাদা 
- কীভাবে আপনার কম্পোনেন্টে Effect ডিক্লার করবেন 
- কীভাবে অকারণে কোন Effect রি-রানিং এড়াবেন
- কেন ডেভেলপমেন্টের সময় Effects দুইবার রান হয় এবং সেগুল কীভাবে ঠিক করবেন

</YouWillLearn>

## Effects কী এবং কীভাবে সেগুলো events থেকে আলাদা? {/*what-are-effects-and-how-are-they-different-from-events*/}

Effects সম্পর্কে শুরুর আগে, আপনার রিয়েক্ট কম্পোনেন্টের ভেতরের দুই প্রকার লজিকের সাথে পরিচিয় থাকতে হবে:

- **Rendering code** (যা [UI এর বর্ণনা](/learn/describing-the-ui) অধ্যায়ে পরিচয় দেওয়া হয়েছে ) আপনার কম্পোনেন্টের টপ লেভেলে থাকে। এটি সেখানে থাকে, যেখানে আপনি props এবং state নেন, তাদের পরিবর্তন করেন, এবং আপনি যে JSX দেখতে চান তা রিটার্ন করেন।   [Rendering code অবশ্যই পিওর হতে হবে](/learn/keeping-components-pure) একটি গণিত সূত্র মতো, যে সূত্রটি শুধু ফলাফিল হিসাব করে, কিন্তু অন্য কিছু করে না।

- **Event handlers** (যা [Adding Interactivity](/learn/adding-interactivity) অধ্যায়ে পরিচয় দেওয়া হয়েছে) আপনার কম্পোনেন্টের ভিতরে একটি নেস্টেড ফাংশন যা কেবল সেগুলো গণনা করার পরিবর্তে অন্য কিছু *করে*।  এটি যে কাজগুলো করতে পারে সেগুলো হতে পারে একটি ইনপুট ফিল্ড আপডেট করা, একটি পণ্য কিনতে HTTP POST request দেওয়া, অথবা ব্যবহারকারীকে অন্য একটি স্ক্রিনে navigate করা। Event handler এ ["side effects"](https://en.wikipedia.org/wiki/Side_effect_(computer_science)) থাকে (এগুলো program এর অবস্থা পরিবর্তন করে) যা নির্দিষ্ট ব্যবহারকারীর ক্রিয়া (উদাহরণস্বরূপ  button click অথবা typing)।

কখনও কখনও এটা যথেষ্ট নয়।  একটি `ChatRoom` কম্পোনেন্ট চিন্তা করুন যখনই স্ক্রিনে দৃশ্যমান হয় তখন অবশ্যই চ্যাট সার্ভারের সাথে সংযোগ করতে হয়। সার্ভারে সংযোগ স্থাপন pure calculation নয় (এটি একটি side effect)  তাই এটি রেন্ডার এর সময় সম্পন্ন হয় না। যাইহোক, ক্লিক ইভেন্ট এর মত কোন নির্দিষ্ট ইভেন্ট নাই যা `ChatRoom` ডিসপ্লে করায়।

***Effect গুলো*  নির্দিষ্ট ইভেন্টের মাধ্যমে নয়, বরং স্বয়ংক্রিয় রেন্ডারিং দ্বারা সৃষ্ট side effect গুলো নির্দিষ্ট করতে দেয়।** চ্যাটে message পাঠানো একটি *event* কারণ এটি সরাসরি একজন ব্যবহারকারীর দ্বারা একটি নির্দিষ্ট বাটনে ক্লিক করার মাধ্যমে ঘটে। তবু, সার্ভারের সাথে সংযোগ স্থাপন একটি *Effect* কারণ এটা উপস্থিত কোম্পোনেন্টের প্রদর্শনের কোন ইন্টারেকশনের কারণে হয় না। Effect গুলো স্ক্রিন আপডেটের পরে একটি [commit](/learn/render-and-commit) এর শেষে চালানো হয়। কিছু external system (যেমন network অথবা একটি third-party library) এর সাথে React component গুলো synchronize করার জন্য এটি একটি ভাল সময় ।

<Note>

এখানে এবং পরে এই পাঠটিতে, বড় হাতের "Effect" উপরের React-specific সংজ্ঞা বোঝায়, অর্থাৎ রেন্ডারিংয়ের ফলে সৃষ্ট side effect। বিস্তৃত এই প্রোগ্রামিং concept টি বুঝাতে, আমরা এটিকে "side effect" বলব।

</Note>

## আপনার কোন Effect প্রয়োজন নাও হতে পারে {/*you-might-not-need-an-effect*/}

**অপ্রয়োজনে আপনার component এ Effects অ্যাড করবেন না।** মনে রাখবেন যে Effect গুলো সাধারণত আপনার React কোডের "step out" করতে এবং কিছু *বাহ্যিক* সিস্টামের সাথে synchronize করতে ব্যবহৃত হয়। এর মধ্যে রয়েছে browser APIs, third-party widgets, network, এবং আরও অনেক কিছু। যদি আপনার Effect টি কেবল অন্য state এর উপর ভিত্তি করে কিছু state কে সামঞ্জস্য করে, [তবে আপনার কোন Effect প্রয়োজন নাও হতে পারে।](/learn/you-might-not-need-an-effect)

## কিভাবে একটি Effect লিখবেন {/*how-to-write-an-effect*/}

একটি Effect লিখতে, এই তিনটি ধাপ অনুসরণ করুনঃ 

1. **Effect ডিক্লার** By default, প্রত্যেক বার রেন্ডারের সময় Effect রান হবে।

2. **Effect এর dependenci গুলো specify করুন** বেশিরভাগ Effects প্রত্যেকবার রেন্ডার হওয়ার পরে re-run হওয়ার থেকে *যখন প্রয়োজন*  তখন re-run হওয়া উচিৎ। উদাহরণস্বরূপ, একটি fade-in animation কেবল তখনি টিগার করা উচিৎ যখন কোন একটি component দৃশ্যমান হয়। কোন chat room এর সাথে সংযোগ স্থাপন এবং বিচ্ছিন্ন তখনই ঘটে যখন component টি দৃশ্যমান এবং অদৃশ্যমান হয়ে যায় বা যখন chat room টি পরিবর্তন হয়। কীভাবে *dependencies* specifying এর মাধ্যমে  এটি কন্ট্রোল করবেন তা শিখবেন।

3. **প্রয়োজনে cleanup অ্যাড করুন** কিছু Effects কিভাবে থামানো হবে, আন্ডু হবে বা এগুলো যা করছে তা clean up করতে হবে তা specify করে দিতে হয়। উধাহরণস্বরূপ, "connect" এর জন্য প্রয়োজন "disconnect", "subscribe" এর জন্য "unsubscribe", and "fetch" এর জন্য প্রয়োজন হয়ত "cancel" অথবা "ignore"। আপনি একটি *cleanup function* রিটার্ন করে কীভাবে এটি করবেন তা শিখবেন।
আসুন, এবার প্রতিটি ধাপ বিস্তারিত দেখি।

### ধাপ ১: একটি Effect ডিক্লার {/*step-1-declare-an-effect*/}

আপনার component এ কোন Effect ডিক্লার করতে, [`useEffect` হুক](/reference/react/useEffect) React থেকে import করুন:

```js
import { useEffect } from 'react';
```

এরপরে, এটিকে আপনার component এর top level এ call করুন এবং Effects এর মধ্যে কিছু code রাখুন।

```js {2-4}
function MyComponent() {
  useEffect(() => {
    // *প্রতিবার* রেন্ডারে এখানের code রান হবে
  });
  return <div />;
}
```

প্রতিবার যখন component রেন্ডার করবে, React স্কিন আপডেট করবে *এবং এর পরে* `useEffect` এর ভিতরের কোড রান করবে। অর্থাৎ, **`useEffect` এক টুকরা কোড রান হতে " বিলম্ব করায় " যতক্ষণ না রেন্ডারটি স্কিনে reflected হয়।**

চলুন দেখা যাক কিভাবে আপনি Effect ব্যবহার করে একটি external system এর সাথে synchronize করবেন। একটি `<VideoPlayer>` React component এর কথা চিন্তা করুন। এটি কন্ট্রল করতে ভাল হবে যদি এটিতে একটি `isPlaying` প্রপস পাঠানো হয় যে এটি চালু আছে অথবা বন্ধ:

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

তবে, browser এর `<video>` tag এ `isPlaying` প্রপ্স নাই। এটি নিয়ন্ত্রণের একমাত্র উপয় হলো DOM element টিতে ম্যানুয়ালি [`play()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play) এবং  [`pause()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause) call করা। **আপনাকে `isPlaying` প্রপ্স এর value টি synchronize করতে হবে, যা `play()` এবং `pause()`কে কল করে video টি বর্তমানে বাজানো উচিৎ কিনা তা নির্দেশ করে।**

আমাদের প্রথমে `<video>` DOM node এর একটি [ref পেতে হবে](/learn/manipulating-the-dom-with-refs)।

রেন্ডারিং এর সময় আপনি `play()` অথবা `pause()` কল করার চেষ্টা করতে পারেন, তবে এটি সঠিক নয়:

<Sandpack>

```js
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

এই কোডটি সঠিক না হওয়ার কারণ হলো এটি রেন্ডারিং এর সময় DOM node এর সাথে কিছু একটা করার চেষ্টা করে। React এ, [রেন্ডারিং JSX এর pure calculation হওয়া উচিৎ](/learn/keeping-components-pure) এবং DOM কে modify করে এমন কোন side effects থাকা উচিৎ নয়।

উপরন্ত, যখন `VideoPlayer` কে প্রথমবারের জন্য call করা হয়, এটির DOM তখন exist করে না! `play()` বা `pause()` করার জন্য এখানে কোন DOM node নাই, কারণ React জানে না কি DOM তৈরি হবে যতক্ষণ না আপনি JSX রিটার্ন করেন। 

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

Play/Pause একাধিকবার চাপুন এবং দেখুন video player কিভাবে `isPlaying` এর value তে synchronize থাকে:

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

এই উদাহরণে, আপনি যে "external system" React state এর সাথে synchronize করেছেন তা হলো ব্রাউজার মিডিয়া API। আপনি legacy non-React code (যেমন jQuery plugins) থেকে declarative React component এ wrap করতে  অনুরূপ পদ্ধতি ব্যবহার করেতে পারেন। 

মনে রাখবেন যে কোন ভিডিও প্লেয়ার কন্ট্রল করা প্রাক্টিকালি আরও জটিল। `play()` কল fail হতে পারে, user built-in ব্রাউজার control গুলো ব্যবহার করে play বা pause করতে পারে, এবং আরও অনেক কিছু। এই উদাহরণটি খুবই সহজ এবং অসম্পূর্ণ।

<Pitfall>

By default, Effect গুলো *প্রত্যেক* রেন্ডারের পরে run হয়। এ কারণেই এ জাতীয় কোড **infinite loop তৈরি করে:**
```js
const [count, setCount] = useState(0);
useEffect(() => {
  setCount(count + 1);
});
```

রেন্ডারিং এর *ফলস্বরূপ*  Effect চলে। state সেট করা রেন্ডারিং টি *টিগার করে*। একটি Effect সিঙ্গে সঙ্গে state এ সেট করা  যেমন একটি পাওয়ার আউটলেটকে তার নিজেতেই প্লাগ করা। Effect run হয়, এটি state সেট করে, যা একটি re-render তৈরি করে, যার ফলে Effect টি run হয়, এটি আবার state টি সেট করে, এটি অন্য একটি re-render তৈরি করে, আর এভাবেই চলতে থাকে।

Effect গুলো সাধারণত আপনার component গুলোকে একটি *external* system এর সাথে synchronize করে। যদি কোন external system না থাকে এবং আপনি কেবল অন্য state এর উপর ভিত্তি করে কিছু state এডজাস্ট করতে চান, [আপনার কোন Effect প্রয়োজন নাও হতে পারে।](/learn/you-might-not-need-an-effect)

</Pitfall>

### ধাপ ২: Effect এর dependency গুলো নির্দিষ্ট করুন {/*step-2-specify-the-effect-dependencies*/}

By default,  Effect গুলো *প্রত্যেক* রেন্ডারের পরে run হয়। অনেক সময়, এটি **আপনি চান না:**

- কখনো কখনো, এটি slow কাজ করে। একটি external system এর সাথে Synchroniz করা সর্বদা তাতক্ষণিক হয় না, সুতরং আপনি এটি প্রয়োজন না হলে এটি এড়িয়ে যেতে চাইতে পারেন। উদাহরণস্বরূপ, আপনি প্রতি keystoke এ চ্যাট সার্ভারের সাথে পুনরায় সংযোগ স্থাপন করতে চান না। 
- কখনো কখনো, এটি ভুল।  উদাহরণস্বরূপ, আপনি প্রতিটি keystroke এ কোন component ফেড-ইন animation ট্রিগার করতে চান না। component টি প্রথমবারের মত appear হলে animation টি কেবল একবার play হওয়া উচিৎ।

সমস্যাটি প্রদর্শনের করতে, এখানে কয়েকটি `console.log` কল এবং একটি টেক্সট ইনপুট সহ পূর্ববর্তী উদাহরণটি যেটি parent component এর স্টেটকে update করে । খেয়াল করুন কিভাবে typing এর ফলে Effect টি re-run হয়:

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

সমস্যাটি হলো আপনার Effect এর মধ্যের কোড কি করবে তা সিদ্ধান্ত নেওয়ার জন্য `isPlaying` প্রপ্সের উপর *নির্ভর করে*, কিন্তু এই dependency টি স্পষ্টভাবে declare করা হয়নি। এই সমস্যাটির সমাধান করতে, dependency array তে `isPlaying` যুক্ত করুন:

```js {2,7}
  useEffect(() => {
    if (isPlaying) { // It's used here...
      // ...
    } else {
      // ...
    }
  }, [isPlaying]); // ...so it must be declared here!
```

এখন সকল dependency গুলো declar করা হয়ে গেছে, সুতারং কোন error নাই। `[isPlaying]` কে dependency array তে রাখার মানে হলো React কে বলা যে যদি `isPlaying` এর মান আগের রেন্ডারে যেমন ছিল তেমন থাকে তবে re-running স্কিপ করতে। এই পরিবর্তনের কারণে, ইনপুট ফিল্ডটিতে টাইপ করালেও Effect টি re-run হয় না, কিন্তু Play/Pause বাটনে press করলে হয়:

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

dependency array তে একাধিক dependency থাকতে পারে। যদি *সবগুলো* dependency এর value গুলো previous render এর মতই থাকে কেবল তখনই React Effect টি re-runn করবে না। React dependency value গুলোকে তুলনা করতে [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) comparison ব্যবহার করে। বিস্তারির জানতে [`useEffect` reference](/reference/react/useEffect#reference) দেখুন। 

**লক্ষ্য করুন যে আপনি আপনার dependency গুলো "choose" করতে পারছেন না।** আপনি যে dependecy গুলো specify করেছেন তা যদি আপনি Effect এর মধ্যে যে কোড রেখেছেন তার উপর base করে React এর expectation এর সাথে না মিলে তাহলে আপনি একটি lint error পাবেন। এটি আপনার কোডে অনেক bug খুজে পাতে সাহায্য করে । যদি আপনি কছু কোড re-run করতে না চান, [*Effect কোড edit করুন* যাতে ঐ  dependencyর "প্রয়োজন" না হয়।](/learn/lifecycle-of-reactive-effects#what-to-do-when-you-dont-want-to-re-synchronize)

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

আমরা পরবর্তি step এ "mount" এর মানে কী তা ভালোভাবে দেখবো।

</Pitfall>

<DeepDive>

#### dependency array থেকে কেন ref বাদ দেওয়া হয়েছিল? {/*why-was-the-ref-omitted-from-the-dependency-array*/}

এই Effect টিতে `ref` এবং `isPlaying` উভয়ই ব্যবহার হচ্ছে, কিন্তু কেবল `isPlaying` কে dependency হিসাবে ডিক্লার করা হয়েছে:

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
এর কারণ হল `ref` object এর একটি *stable identity* রয়েছে: React গ্যারান্টি দেয় যে [প্রতি রেন্ডারে একই `useRef` কল থেকে সর্বদা একই object পাবেন](/reference/react/useRef#returns)। এটি কখনো পরিবর্তন হয় না, সুতারং  এটি নিজেই Effect টি re-run হওয়ার কারণ হতে পারেনা। অতএব, এটি বিবেচ্য বিষয় নয় যে আপনি এটি include করছেন কি করেন নাই।  এটি Includ করাও ঠিক আছে:

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

`useState` দ্বারা রিটার্ন করা [`set` function গুলোরও](/reference/react/useState#setstate) stable identity রয়েছে, তাই আপনি প্রায়ই দেখতে পাবেন তাদের dependencie থেকে বাদ দেওয়া হয়েছে। যদি lint আপনাকে error ছাড়াই dependency বাদ দিতে দেয়, তবে এটি করা নিরাপদ।

always-stable dependency বাদ দেওয়া তখনই কাজ করে যখন linter "দেখতে" পারে যা object টি stable। উদাহরণস্বরূপ, যদি কোন parent component থেকে `ref` pass করা হয়, আপনাকে একটি dependency array specify করতে হবে। যাইহোক, এটি ভাল কারণ আপনি জানতে পারবেন না যে parent component সবসময় একই রেফ পাস করে কিনা, অথবা শর্তসাপেক্ষে বেশ কয়েকটি রেফের একটি পাস করে কিনা। সুতারং আপনার Effect নির্ভর _করবে_ কোন ref pass করা হয়েছে তার উপর।

</DeepDive>

### ধাপ ৩: প্রয়োজনে cleanup যোগ করুন {/*step-3-add-cleanup-if-needed*/}

Consider a different example. You're writing a `ChatRoom` component that needs to connect to the chat server when it appears. You are given a `createConnection()` API that returns an object with `connect()` and `disconnect()` methods. How do you keep the component connected while it is displayed to the user?

Start by writing the Effect logic:

```js
useEffect(() => {
  const connection = createConnection();
  connection.connect();
});
```

It would be slow to connect to the chat after every re-render, so you add the dependency array:

```js {4}
useEffect(() => {
  const connection = createConnection();
  connection.connect();
}, []);
```

**The code inside the Effect does not use any props or state, so your dependency array is `[]` (empty). This tells React to only run this code when the component "mounts", i.e. appears on the screen for the first time.**

Let's try running this code:

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

```js chat.js
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

This Effect only runs on mount, so you might expect `"✅ Connecting..."` to be printed once in the console. **However, if you check the console, `"✅ Connecting..."` gets printed twice. Why does it happen?**

Imagine the `ChatRoom` component is a part of a larger app with many different screens. The user starts their journey on the `ChatRoom` page. The component mounts and calls `connection.connect()`. Then imagine the user navigates to another screen--for example, to the Settings page. The `ChatRoom` component unmounts. Finally, the user clicks Back and `ChatRoom` mounts again. This would set up a second connection--but the first connection was never destroyed! As the user navigates across the app, the connections would keep piling up.

Bugs like this are easy to miss without extensive manual testing. To help you spot them quickly, in development React remounts every component once immediately after its initial mount.

Seeing the `"✅ Connecting..."` log twice helps you notice the real issue: your code doesn't close the connection when the component unmounts.

To fix the issue, return a *cleanup function* from your Effect:

```js {4-6}
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, []);
```

React will call your cleanup function each time before the Effect runs again, and one final time when the component unmounts (gets removed). Let's see what happens when the cleanup function is implemented:

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

```js chat.js
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

Now you get three console logs in development:

1. `"✅ Connecting..."`
2. `"❌ Disconnected."`
3. `"✅ Connecting..."`

**This is the correct behavior in development.** By remounting your component, React verifies that navigating away and back would not break your code. Disconnecting and then connecting again is exactly what should happen! When you implement the cleanup well, there should be no user-visible difference between running the Effect once vs running it, cleaning it up, and running it again. There's an extra connect/disconnect call pair because React is probing your code for bugs in development. This is normal--don't try to make it go away!

**In production, you would only see `"✅ Connecting..."` printed once.** Remounting components only happens in development to help you find Effects that need cleanup. You can turn off [Strict Mode](/reference/react/StrictMode) to opt out of the development behavior, but we recommend keeping it on. This lets you find many bugs like the one above.

## How to handle the Effect firing twice in development? {/*how-to-handle-the-effect-firing-twice-in-development*/}

React intentionally remounts your components in development to find bugs like in the last example. **The right question isn't "how to run an Effect once", but "how to fix my Effect so that it works after remounting".**

Usually, the answer is to implement the cleanup function.  The cleanup function should stop or undo whatever the Effect was doing. The rule of thumb is that the user shouldn't be able to distinguish between the Effect running once (as in production) and a _setup → cleanup → setup_ sequence (as you'd see in development).

Most of the Effects you'll write will fit into one of the common patterns below.

### Controlling non-React widgets {/*controlling-non-react-widgets*/}

Sometimes you need to add UI widgets that aren't written to React. For example, let's say you're adding a map component to your page. It has a `setZoomLevel()` method, and you'd like to keep the zoom level in sync with a `zoomLevel` state variable in your React code. Your Effect would look similar to this:

```js
useEffect(() => {
  const map = mapRef.current;
  map.setZoomLevel(zoomLevel);
}, [zoomLevel]);
```

Note that there is no cleanup needed in this case. In development, React will call the Effect twice, but this is not a problem because calling `setZoomLevel` twice with the same value does not do anything. It may be slightly slower, but this doesn't matter because it won't remount needlessly in production.

Some APIs may not allow you to call them twice in a row. For example, the [`showModal`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDialogElement/showModal) method of the built-in [`<dialog>`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDialogElement) element throws if you call it twice. Implement the cleanup function and make it close the dialog:

```js {4}
useEffect(() => {
  const dialog = dialogRef.current;
  dialog.showModal();
  return () => dialog.close();
}, []);
```

In development, your Effect will call `showModal()`, then immediately `close()`, and then `showModal()` again. This has the same user-visible behavior as calling `showModal()` once, as you would see in production.

### Subscribing to events {/*subscribing-to-events*/}

If your Effect subscribes to something, the cleanup function should unsubscribe:

```js {6}
useEffect(() => {
  function handleScroll(e) {
    console.log(window.scrollX, window.scrollY);
  }
  window.addEventListener('scroll', handleScroll);
  return () => window.removeEventListener('scroll', handleScroll);
}, []);
```

In development, your Effect will call `addEventListener()`, then immediately `removeEventListener()`, and then `addEventListener()` again with the same handler. So there would be only one active subscription at a time. This has the same user-visible behavior as calling `addEventListener()` once, as in production.

### Triggering animations {/*triggering-animations*/}

If your Effect animates something in, the cleanup function should reset the animation to the initial values:

```js {4-6}
useEffect(() => {
  const node = ref.current;
  node.style.opacity = 1; // Trigger the animation
  return () => {
    node.style.opacity = 0; // Reset to the initial value
  };
}, []);
```

In development, opacity will be set to `1`, then to `0`, and then to `1` again. This should have the same user-visible behavior as setting it to `1` directly, which is what would happen in production. If you use a third-party animation library with support for tweening, your cleanup function should reset the timeline to its initial state.

### Fetching data {/*fetching-data*/}

If your Effect fetches something, the cleanup function should either [abort the fetch](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) or ignore its result:

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

You can't "undo" a network request that already happened, but your cleanup function should ensure that the fetch that's _not relevant anymore_ does not keep affecting your application. If the `userId` changes from `'Alice'` to `'Bob'`, cleanup ensures that the `'Alice'` response is ignored even if it arrives after `'Bob'`.

**In development, you will see two fetches in the Network tab.** There is nothing wrong with that. With the approach above, the first Effect will immediately get cleaned up so its copy of the `ignore` variable will be set to `true`. So even though there is an extra request, it won't affect the state thanks to the `if (!ignore)` check.

**In production, there will only be one request.** If the second request in development is bothering you, the best approach is to use a solution that deduplicates requests and caches their responses between components:

```js
function TodoList() {
  const todos = useSomeDataLibrary(`/api/user/${userId}/todos`);
  // ...
```

This will not only improve the development experience, but also make your application feel faster. For example, the user pressing the Back button won't have to wait for some data to load again because it will be cached. You can either build such a cache yourself or use one of the many alternatives to manual fetching in Effects.

<DeepDive>

#### What are good alternatives to data fetching in Effects? {/*what-are-good-alternatives-to-data-fetching-in-effects*/}

Writing `fetch` calls inside Effects is a [popular way to fetch data](https://www.robinwieruch.de/react-hooks-fetch-data/), especially in fully client-side apps. This is, however, a very manual approach and it has significant downsides:

- **Effects don't run on the server.** This means that the initial server-rendered HTML will only include a loading state with no data. The client computer will have to download all JavaScript and render your app only to discover that now it needs to load the data. This is not very efficient.
- **Fetching directly in Effects makes it easy to create "network waterfalls".** You render the parent component, it fetches some data, renders the child components, and then they start fetching their data. If the network is not very fast, this is significantly slower than fetching all data in parallel.
- **Fetching directly in Effects usually means you don't preload or cache data.** For example, if the component unmounts and then mounts again, it would have to fetch the data again.
- **It's not very ergonomic.** There's quite a bit of boilerplate code involved when writing `fetch` calls in a way that doesn't suffer from bugs like [race conditions.](https://maxrozen.com/race-conditions-fetching-data-react-with-useeffect)

This list of downsides is not specific to React. It applies to fetching data on mount with any library. Like with routing, data fetching is not trivial to do well, so we recommend the following approaches:

- **If you use a [framework](/learn/start-a-new-react-project#production-grade-react-frameworks), use its built-in data fetching mechanism.** Modern React frameworks have integrated data fetching mechanisms that are efficient and don't suffer from the above pitfalls.
- **Otherwise, consider using or building a client-side cache.** Popular open source solutions include [React Query](https://tanstack.com/query/latest), [useSWR](https://swr.vercel.app/), and [React Router 6.4+.](https://beta.reactrouter.com/en/main/start/overview) You can build your own solution too, in which case you would use Effects under the hood, but add logic for deduplicating requests, caching responses, and avoiding network waterfalls (by preloading data or hoisting data requirements to routes).

You can continue fetching data directly in Effects if neither of these approaches suit you.

</DeepDive>

### Sending analytics {/*sending-analytics*/}

Consider this code that sends an analytics event on the page visit:

```js
useEffect(() => {
  logVisit(url); // Sends a POST request
}, [url]);
```

In development, `logVisit` will be called twice for every URL, so you might be tempted to try to fix that. **We recommend keeping this code as is.** Like with earlier examples, there is no *user-visible* behavior difference between running it once and running it twice. From a practical point of view, `logVisit` should not do anything in development because you don't want the logs from the development machines to skew the production metrics. Your component remounts every time you save its file, so it logs extra visits in development anyway.

**In production, there will be no duplicate visit logs.**

To debug the analytics events you're sending, you can deploy your app to a staging environment (which runs in production mode) or temporarily opt out of [Strict Mode](/reference/react/StrictMode) and its development-only remounting checks. You may also send analytics from the route change event handlers instead of Effects. For more precise analytics, [intersection observers](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) can help track which components are in the viewport and how long they remain visible.

### Not an Effect: Initializing the application {/*not-an-effect-initializing-the-application*/}

Some logic should only run once when the application starts. You can put it outside your components:

```js {2-3}
if (typeof window !== 'undefined') { // Check if we're running in the browser.
  checkAuthToken();
  loadDataFromLocalStorage();
}

function App() {
  // ...
}
```

This guarantees that such logic only runs once after the browser loads the page.

### Not an Effect: Buying a product {/*not-an-effect-buying-a-product*/}

Sometimes, even if you write a cleanup function, there's no way to prevent user-visible consequences of running the Effect twice. For example, maybe your Effect sends a POST request like buying a product:

```js {2-3}
useEffect(() => {
  // 🔴 Wrong: This Effect fires twice in development, exposing a problem in the code.
  fetch('/api/buy', { method: 'POST' });
}, []);
```

You wouldn't want to buy the product twice. However, this is also why you shouldn't put this logic in an Effect. What if the user goes to another page and then presses Back? Your Effect would run again. You don't want to buy the product when the user *visits* a page; you want to buy it when the user *clicks* the Buy button.

Buying is not caused by rendering; it's caused by a specific interaction. It should run only when the user presses the button. **Delete the Effect and move your `/api/buy` request into the Buy button event handler:**

```js {2-3}
  function handleClick() {
    // ✅ Buying is an event because it is caused by a particular interaction.
    fetch('/api/buy', { method: 'POST' });
  }
```

**This illustrates that if remounting breaks the logic of your application, this usually uncovers existing bugs.** From a user's perspective, visiting a page shouldn't be different from visiting it, clicking a link, then pressing Back to view the page again. React verifies that your components abide by this principle by remounting them once in development.

## Putting it all together {/*putting-it-all-together*/}

This playground can help you "get a feel" for how Effects work in practice.

This example uses [`setTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/setTimeout) to schedule a console log with the input text to appear three seconds after the Effect runs. The cleanup function cancels the pending timeout. Start by pressing "Mount the component":

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

You will see three logs at first: `Schedule "a" log`, `Cancel "a" log`, and `Schedule "a" log` again. Three second later there will also be a log saying `a`. As you learned earlier, the extra schedule/cancel pair is because React remounts the component once in development to verify that you've implemented cleanup well.

Now edit the input to say `abc`. If you do it fast enough, you'll see `Schedule "ab" log` immediately followed by `Cancel "ab" log` and `Schedule "abc" log`. **React always cleans up the previous render's Effect before the next render's Effect.** This is why even if you type into the input fast, there is at most one timeout scheduled at a time. Edit the input a few times and watch the console to get a feel for how Effects get cleaned up.

Type something into the input and then immediately press "Unmount the component". Notice how unmounting cleans up the last render's Effect. Here, it clears the last timeout before it has a chance to fire.

Finally, edit the component above and comment out the cleanup function so that the timeouts don't get cancelled. Try typing `abcde` fast. What do you expect to happen in three seconds? Will `console.log(text)` inside the timeout print the *latest* `text` and produce five `abcde` logs? Give it a try to check your intuition!

Three seconds later, you should see a sequence of logs (`a`, `ab`, `abc`, `abcd`, and `abcde`) rather than five `abcde` logs. **Each Effect "captures" the `text` value from its corresponding render.**  It doesn't matter that the `text` state changed: an Effect from the render with `text = 'ab'` will always see `'ab'`. In other words, Effects from each render are isolated from each other. If you're curious how this works, you can read about [closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures).

<DeepDive>

#### Each render has its own Effects {/*each-render-has-its-own-effects*/}

You can think of `useEffect` as "attaching" a piece of behavior to the render output. Consider this Effect:

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

Let's see what exactly happens as the user navigates around the app.

#### Initial render {/*initial-render*/}

The user visits `<ChatRoom roomId="general" />`. Let's [mentally substitute](/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time) `roomId` with `'general'`:

```js
  // JSX for the first render (roomId = "general")
  return <h1>Welcome to general!</h1>;
```

**The Effect is *also* a part of the rendering output.** The first render's Effect becomes:

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

React runs this Effect, which connects to the `'general'` chat room.

#### Re-render with same dependencies {/*re-render-with-same-dependencies*/}

Let's say `<ChatRoom roomId="general" />` re-renders. The JSX output is the same:

```js
  // JSX for the second render (roomId = "general")
  return <h1>Welcome to general!</h1>;
```

React sees that the rendering output has not changed, so it doesn't update the DOM.

The Effect from the second render looks like this:

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

React compares `['general']` from the second render with `['general']` from the first render. **Because all dependencies are the same, React *ignores* the Effect from the second render.** It never gets called.

#### Re-render with different dependencies {/*re-render-with-different-dependencies*/}

Then, the user visits `<ChatRoom roomId="travel" />`. This time, the component returns different JSX:

```js
  // JSX for the third render (roomId = "travel")
  return <h1>Welcome to travel!</h1>;
```

React updates the DOM to change `"Welcome to general"` into `"Welcome to travel"`.

The Effect from the third render looks like this:

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

React compares `['travel']` from the third render with `['general']` from the second render. One dependency is different: `Object.is('travel', 'general')` is `false`. The Effect can't be skipped.

**Before React can apply the Effect from the third render, it needs to clean up the last Effect that _did_ run.** The second render's Effect was skipped, so React needs to clean up the first render's Effect. If you scroll up to the first render, you'll see that its cleanup calls `disconnect()` on the connection that was created with `createConnection('general')`. This disconnects the app from the `'general'` chat room.

After that, React runs the third render's Effect. It connects to the `'travel'` chat room.

#### Unmount {/*unmount*/}

Finally, let's say the user navigates away, and the `ChatRoom` component unmounts. React runs the last Effect's cleanup function. The last Effect was from the third render. The third render's cleanup destroys the `createConnection('travel')` connection. So the app disconnects from the `'travel'` room.

#### Development-only behaviors {/*development-only-behaviors*/}

When [Strict Mode](/reference/react/StrictMode) is on, React remounts every component once after mount (state and DOM are preserved). This [helps you find Effects that need cleanup](#step-3-add-cleanup-if-needed) and exposes bugs like race conditions early. Additionally, React will remount the Effects whenever you save a file in development. Both of these behaviors are development-only.

</DeepDive>

<Recap>

- Unlike events, Effects are caused by rendering itself rather than a particular interaction.
- Effects let you synchronize a component with some external system (third-party API, network, etc).
- By default, Effects run after every render (including the initial one).
- React will skip the Effect if all of its dependencies have the same values as during the last render.
- You can't "choose" your dependencies. They are determined by the code inside the Effect.
- Empty dependency array (`[]`) corresponds to the component "mounting", i.e. being added to the screen.
- In Strict Mode, React mounts components twice (in development only!) to stress-test your Effects.
- If your Effect breaks because of remounting, you need to implement a cleanup function.
- React will call your cleanup function before the Effect runs next time, and during the unmount.

</Recap>

<Challenges>

#### Focus a field on mount {/*focus-a-field-on-mount*/}

In this example, the form renders a `<MyInput />` component.

Use the input's [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) method to make `MyInput` automatically focus when it appears on the screen. There is already a commented out implementation, but it doesn't quite work. Figure out why it doesn't work, and fix it. (If you're familiar with the `autoFocus` attribute, pretend that it does not exist: we are reimplementing the same functionality from scratch.)

<Sandpack>

```js MyInput.js active
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

```js App.js hidden
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


To verify that your solution works, press "Show form" and verify that the input receives focus (becomes highlighted and the cursor is placed inside). Press "Hide form" and "Show form" again. Verify the input is highlighted again.

`MyInput` should only focus _on mount_ rather than after every render. To verify that the behavior is right, press "Show form" and then repeatedly press the "Make it uppercase" checkbox. Clicking the checkbox should _not_ focus the input above it.

<Solution>

Calling `ref.current.focus()` during render is wrong because it is a *side effect*. Side effects should either be placed inside an event handler or be declared with `useEffect`. In this case, the side effect is _caused_ by the component appearing rather than by any specific interaction, so it makes sense to put it in an Effect.

To fix the mistake, wrap the `ref.current.focus()` call into an Effect declaration. Then, to ensure that this Effect runs only on mount rather than after every render, add the empty `[]` dependencies to it.

<Sandpack>

```js MyInput.js active
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

```js App.js hidden
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

#### Focus a field conditionally {/*focus-a-field-conditionally*/}

This form renders two `<MyInput />` components.

Press "Show form" and notice that the second field automatically gets focused. This is because both of the `<MyInput />` components try to focus the field inside. When you call `focus()` for two input fields in a row, the last one always "wins".

Let's say you want to focus the first field. The first `MyInput` component now receives a boolean `shouldFocus` prop set to `true`. Change the logic so that `focus()` is only called if the `shouldFocus` prop received by `MyInput` is `true`.

<Sandpack>

```js MyInput.js active
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

```js App.js hidden
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

To verify your solution, press "Show form" and "Hide form" repeatedly. When the form appears, only the *first* input should get focused. This is because the parent component renders the first input with `shouldFocus={true}` and the second input with `shouldFocus={false}`. Also check that both inputs still work and you can type into both of them.

<Hint>

You can't declare an Effect conditionally, but your Effect can include conditional logic.

</Hint>

<Solution>

Put the conditional logic inside the Effect. You will need to specify `shouldFocus` as a dependency because you are using it inside the Effect. (This means that if some input's `shouldFocus` changes from `false` to `true`, it will focus after mount.)

<Sandpack>

```js MyInput.js active
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

```js App.js hidden
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

#### Fix an interval that fires twice {/*fix-an-interval-that-fires-twice*/}

This `Counter` component displays a counter that should increment every second. On mount, it calls [`setInterval`.](https://developer.mozilla.org/en-US/docs/Web/API/setInterval) This causes `onTick` to run every second. The `onTick` function increments the counter.

However, instead of incrementing once per second, it increments twice. Why is that? Find the cause of the bug and fix it.

<Hint>

Keep in mind that `setInterval` returns an interval ID, which you can pass to [`clearInterval`](https://developer.mozilla.org/en-US/docs/Web/API/clearInterval) to stop the interval.

</Hint>

<Sandpack>

```js Counter.js active
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

```js App.js hidden
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

When [Strict Mode](/reference/react/StrictMode) is on (like in the sandboxes on this site), React remounts each component once in development. This causes the interval to be set up twice, and this is why each second the counter increments twice.

However, React's behavior is not the *cause* of the bug: the bug already exists in the code. React's behavior makes the bug more noticeable. The real cause is that this Effect starts a process but doesn't provide a way to clean it up.

To fix this code, save the interval ID returned by `setInterval`, and implement a cleanup function with [`clearInterval`](https://developer.mozilla.org/en-US/docs/Web/API/clearInterval):

<Sandpack>

```js Counter.js active
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

```js App.js hidden
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

In development, React will still remount your component once to verify that you've implemented cleanup well. So there will be a `setInterval` call, immediately followed by `clearInterval`, and `setInterval` again. In production, there will be only one `setInterval` call. The user-visible behavior in both cases is the same: the counter increments once per second.

</Solution>

#### Fix fetching inside an Effect {/*fix-fetching-inside-an-effect*/}

This component shows the biography for the selected person. It loads the biography by calling an asynchronous function `fetchBio(person)` on mount and whenever `person` changes. That asynchronous function returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) which eventually resolves to a string. When fetching is done, it calls `setBio` to display that string under the select box.

<Sandpack>

```js App.js
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

```js api.js hidden
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


There is a bug in this code. Start by selecting "Alice". Then select "Bob" and then immediately after that select "Taylor". If you do this fast enough, you will notice that bug: Taylor is selected, but the paragraph below says "This is Bob's bio."

Why does this happen? Fix the bug inside this Effect.

<Hint>

If an Effect fetches something asynchronously, it usually needs cleanup.

</Hint>

<Solution>

To trigger the bug, things need to happen in this order:

- Selecting `'Bob'` triggers `fetchBio('Bob')`
- Selecting `'Taylor'` triggers `fetchBio('Taylor')`
- **Fetching `'Taylor'` completes *before* fetching `'Bob'`**
- The Effect from the `'Taylor'` render calls `setBio('This is Taylor’s bio')`
- Fetching `'Bob'` completes
- The Effect from the `'Bob'` render calls `setBio('This is Bob’s bio')`

This is why you see Bob's bio even though Taylor is selected. Bugs like this are called [race conditions](https://en.wikipedia.org/wiki/Race_condition) because two asynchronous operations are "racing" with each other, and they might arrive in an unexpected order.

To fix this race condition, add a cleanup function:

<Sandpack>

```js App.js
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

```js api.js hidden
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

Each render's Effect has its own `ignore` variable. Initially, the `ignore` variable is set to `false`. However, if an Effect gets cleaned up (such as when you select a different person), its `ignore` variable becomes `true`. So now it doesn't matter in which order the requests complete. Only the last person's Effect will have `ignore` set to `false`, so it will call `setBio(result)`. Past Effects have been cleaned up, so the `if (!ignore)` check will prevent them from calling `setBio`:

- Selecting `'Bob'` triggers `fetchBio('Bob')`
- Selecting `'Taylor'` triggers `fetchBio('Taylor')` **and cleans up the previous (Bob's) Effect**
- Fetching `'Taylor'` completes *before* fetching `'Bob'`
- The Effect from the `'Taylor'` render calls `setBio('This is Taylor’s bio')`
- Fetching `'Bob'` completes
- The Effect from the `'Bob'` render **does not do anything because its `ignore` flag was set to `true`**

In addition to ignoring the result of an outdated API call, you can also use [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) to cancel the requests that are no longer needed. However, by itself this is not enough to protect against race conditions. More asynchronous steps could be chained after the fetch, so using an explicit flag like `ignore` is the most reliable way to fix this type of problems.

</Solution>

</Challenges>

