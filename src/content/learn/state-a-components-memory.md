---
title: "State: কম্পোনেন্ট এর মেমোরি"
---

<Intro>

ইন্টারেকশনের ফলে প্রায়ই কম্পোনেন্ট কে স্ক্রিনে যা আছে তা পরিবর্তন করতে হয়। ফর্মে টাইপ করলে ইনপুট ফিল্ড আপডেট হয়ে যাওয়া উচিত, ছবির ক্যারোসেল এর "next" এ ক্লিক করলে ডিসপ্লের ছবি বদলানো উচিত, "buy" এ ক্লিক করলে প্রোডাক্ট শপিং কার্ট এ যাওয়া উচিত। কম্পোনেন্ট এর কিছু জিনিস মনে রাখা উচিত, যেমন: বর্তমান ইনপুট ভ্যালু, বর্তমান ছবি, শপিং কার্ট। React এর এমন কম্পোনেন্ট-ভিত্তিক মেমোরি কে *state* বলে।

</Intro>

<YouWillLearn>

* [`useState`](/reference/react/useState) দিয়ে কিভাবে একটি state ভ্যারিয়েবল তৈরি করা যায়
* `useState` হুক কোন দুইটি ভ্যারিয়েবল রিটার্ন করে 
* কিভাবে একাধিক state ভ্যারিয়েবল তৈরি করতে হয়
* state কে কেন লোকাল বলা হয় 

</YouWillLearn>

## যখন সাধারণ ভ্যারিয়েবল যথেষ্ট নয় {/*when-a-regular-variable-isnt-enough*/}

নিচের কম্পোনেন্টটি একটি ভাস্কর্যের ছবি প্রদর্শন করে। "Next" বাটন এ ক্লিক করলে `index` এর ভ্যালু `1`, এরপর `2` এবং এভাবে পরিবর্তন হয়ে পরের ভাস্কর্যের ছবি দেখানো উচিত। কিন্তু, এটা **কাজ করবে না** (আপনি চেষ্টা করে দেখতে পারেন!):

<Sandpack>

```js
import { sculptureList } from './data.js';

export default function Gallery() {
  let index = 0;

  function handleClick() {
    index = index + 1;
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i> 
        by {sculpture.artist}
      </h2>
      <h3>  
        ({index + 1} of {sculptureList.length})
      </h3>
      <img 
        src={sculpture.url} 
        alt={sculpture.alt}
      />
      <p>
        {sculpture.description}
      </p>
    </>
  );
}
```

```js src/data.js
export const sculptureList = [{
  name: 'Homenaje a la Neurocirugía',
  artist: 'Marta Colvin Andrade',
  description: 'Although Colvin is predominantly known for abstract themes that allude to pre-Hispanic symbols, this gigantic sculpture, an homage to neurosurgery, is one of her most recognizable public art pieces.',
  url: 'https://i.imgur.com/Mx7dA2Y.jpg',
  alt: 'A bronze statue of two crossed hands delicately holding a human brain in their fingertips.'  
}, {
  name: 'Floralis Genérica',
  artist: 'Eduardo Catalano',
  description: 'This enormous (75 ft. or 23m) silver flower is located in Buenos Aires. It is designed to move, closing its petals in the evening or when strong winds blow and opening them in the morning.',
  url: 'https://i.imgur.com/ZF6s192m.jpg',
  alt: 'A gigantic metallic flower sculpture with reflective mirror-like petals and strong stamens.'
}, {
  name: 'Eternal Presence',
  artist: 'John Woodrow Wilson',
  description: 'Wilson was known for his preoccupation with equality, social justice, as well as the essential and spiritual qualities of humankind. This massive (7ft. or 2,13m) bronze represents what he described as "a symbolic Black presence infused with a sense of universal humanity."',
  url: 'https://i.imgur.com/aTtVpES.jpg',
  alt: 'The sculpture depicting a human head seems ever-present and solemn. It radiates calm and serenity.'
}, {
  name: 'Moai',
  artist: 'Unknown Artist',
  description: 'Located on the Easter Island, there are 1,000 moai, or extant monumental statues, created by the early Rapa Nui people, which some believe represented deified ancestors.',
  url: 'https://i.imgur.com/RCwLEoQm.jpg',
  alt: 'Three monumental stone busts with the heads that are disproportionately large with somber faces.'
}, {
  name: 'Blue Nana',
  artist: 'Niki de Saint Phalle',
  description: 'The Nanas are triumphant creatures, symbols of femininity and maternity. Initially, Saint Phalle used fabric and found objects for the Nanas, and later on introduced polyester to achieve a more vibrant effect.',
  url: 'https://i.imgur.com/Sd1AgUOm.jpg',
  alt: 'A large mosaic sculpture of a whimsical dancing female figure in a colorful costume emanating joy.'
}, {
  name: 'Ultimate Form',
  artist: 'Barbara Hepworth',
  description: 'This abstract bronze sculpture is a part of The Family of Man series located at Yorkshire Sculpture Park. Hepworth chose not to create literal representations of the world but developed abstract forms inspired by people and landscapes.',
  url: 'https://i.imgur.com/2heNQDcm.jpg',
  alt: 'A tall sculpture made of three elements stacked on each other reminding of a human figure.'
}, {
  name: 'Cavaliere',
  artist: 'Lamidi Olonade Fakeye',
  description: "Descended from four generations of woodcarvers, Fakeye's work blended traditional and contemporary Yoruba themes.",
  url: 'https://i.imgur.com/wIdGuZwm.png',
  alt: 'An intricate wood sculpture of a warrior with a focused face on a horse adorned with patterns.'
}, {
  name: 'Big Bellies',
  artist: 'Alina Szapocznikow',
  description: "Szapocznikow is known for her sculptures of the fragmented body as a metaphor for the fragility and impermanence of youth and beauty. This sculpture depicts two very realistic large bellies stacked on top of each other, each around five feet (1,5m) tall.",
  url: 'https://i.imgur.com/AlHTAdDm.jpg',
  alt: 'The sculpture reminds a cascade of folds, quite different from bellies in classical sculptures.'
}, {
  name: 'Terracotta Army',
  artist: 'Unknown Artist',
  description: 'The Terracotta Army is a collection of terracotta sculptures depicting the armies of Qin Shi Huang, the first Emperor of China. The army consisted of more than 8,000 soldiers, 130 chariots with 520 horses, and 150 cavalry horses.',
  url: 'https://i.imgur.com/HMFmH6m.jpg',
  alt: '12 terracotta sculptures of solemn warriors, each with a unique facial expression and armor.'
}, {
  name: 'Lunar Landscape',
  artist: 'Louise Nevelson',
  description: 'Nevelson was known for scavenging objects from New York City debris, which she would later assemble into monumental constructions. In this one, she used disparate parts like a bedpost, juggling pin, and seat fragment, nailing and gluing them into boxes that reflect the influence of Cubism’s geometric abstraction of space and form.',
  url: 'https://i.imgur.com/rN7hY6om.jpg',
  alt: 'A black matte sculpture where the individual elements are initially indistinguishable.'
}, {
  name: 'Aureole',
  artist: 'Ranjani Shettar',
  description: 'Shettar merges the traditional and the modern, the natural and the industrial. Her art focuses on the relationship between man and nature. Her work was described as compelling both abstractly and figuratively, gravity defying, and a "fine synthesis of unlikely materials."',
  url: 'https://i.imgur.com/okTpbHhm.jpg',
  alt: 'A pale wire-like sculpture mounted on concrete wall and descending on the floor. It appears light.'
}, {
  name: 'Hippos',
  artist: 'Taipei Zoo',
  description: 'The Taipei Zoo commissioned a Hippo Square featuring submerged hippos at play.',
  url: 'https://i.imgur.com/6o5Vuyu.jpg',
  alt: 'A group of bronze hippo sculptures emerging from the sett sidewalk as if they were swimming.'
}];
```

```css
h2 { margin-top: 10px; margin-bottom: 0; }
h3 {
  margin-top: 5px;
  font-weight: normal;
  font-size: 100%;
}
img { width: 120px; height: 120px; }
button {
  display: block;
  margin-top: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

`handleClick` ইভেন্ট হ্যান্ডলারটি লোকাল ভ্যারিয়েবল `index` কে আপডেট করছে। তবে, দুটি বিষয় এই পরিবর্তন দেখতে বাধা তৈরি করছে।

1. **লোকাল ভ্যারিয়েবলগুলো রেন্ডারগুলির মধ্যে স্থায়ী হয় না।** যখন React কোন কম্পোনেন্ট দ্বিতীয়বার রেন্ডার করে, তখন এটি সম্পূর্ণ রূপে নতুন করে রেন্ডার করে—এটি লোকাল ভ্যারিয়েবলগুলিতে কোনও পরিবর্তন বিবেচনা করে না।
2. **লোকাল ভ্যারিয়েবলগুলিতে পরিবর্তন রেন্ডারগুলি ট্রিগার করে না।** নতুন ডেটার জন্যে React তার কম্পোনেন্ট কে নতুন করে রেন্ডার করার প্রয়োজন মনে করে না।

একটি কম্পোনেন্টকে নতুন ডেটা দিয়ে আপডেট করতে, দুটি বিষয় হতে হবে:

1. রেন্ডার এর মধ্যে ডাটা **Retain** করা।
2. কম্পোনেন্টকে নতুন ডাটা দিয়ে রেন্ডার করতে রিয়েক্ট কে **Trigger** করা। (রি-রেন্ডারিং)

[`useState`](/reference/react/useState) হুক আপনাকে নিচের দুটি জিনিস দেয়ঃ

1. একটি **state ভ্যারিয়েবল** যা রি-রেন্ডারের মধ্যে ডাটা মনে রাখে।
2. একটি **state setter ফাংশন** যা দিয়ে ভ্যারিয়েবল আপডেট করা যায় এবং রিয়েক্ট কে ট্রিগার করে কম্পোনেন্টকে আবার রেন্ডার করতে।

## State ভ্যারিয়েবল তৈরি করা {/*adding-a-state-variable*/}

State ভ্যারিয়েবল তৈরি করতে হলে,রিয়েক্ট থেকে ফাইলের উপরে `useState` ইমপোর্ট করতে হবেঃ

```js
import { useState } from 'react';
```

এরপর, নিচের লাইন টা পরিবর্তন করেঃ

```js
let index = 0;
```

নিচের মতো করে নিন

```js
const [index, setIndex] = useState(0);
```

`index` একটি state ভ্যারিয়েবল এবং `setIndex` হচ্ছে setter ফাংশন।

> এখানে `[` এবং `]` সিনট্যাক্সকে বলা হয় [array destructuring](https://javascript.info/destructuring-assignment) এবং এটা আপনাকে একটি অ্যারে থেকে ভ্যালু রিড করতে দেয়। যেই অ্যারেটা `useState` রিটার্ন করে সেটাতে সব সময় দুটি আইটেম থাকে।

এইভাবে তারা `handleClick` এ একসাথে কাজ করেঃ

```js
function handleClick() {
  setIndex(index + 1);
}
```

এখন "Next" বাটনে ক্লিক করলে বর্তমান ভাস্কর্যটি পরিবর্তন হয়ঃ

<Sandpack>

```js
import { useState } from 'react';
import { sculptureList } from './data.js';

export default function Gallery() {
  const [index, setIndex] = useState(0);

  function handleClick() {
    setIndex(index + 1);
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i> 
        by {sculpture.artist}
      </h2>
      <h3>  
        ({index + 1} of {sculptureList.length})
      </h3>
      <img 
        src={sculpture.url} 
        alt={sculpture.alt}
      />
      <p>
        {sculpture.description}
      </p>
    </>
  );
}
```

```js src/data.js
export const sculptureList = [{
  name: 'Homenaje a la Neurocirugía',
  artist: 'Marta Colvin Andrade',
  description: 'Although Colvin is predominantly known for abstract themes that allude to pre-Hispanic symbols, this gigantic sculpture, an homage to neurosurgery, is one of her most recognizable public art pieces.',
  url: 'https://i.imgur.com/Mx7dA2Y.jpg',
  alt: 'A bronze statue of two crossed hands delicately holding a human brain in their fingertips.'  
}, {
  name: 'Floralis Genérica',
  artist: 'Eduardo Catalano',
  description: 'This enormous (75 ft. or 23m) silver flower is located in Buenos Aires. It is designed to move, closing its petals in the evening or when strong winds blow and opening them in the morning.',
  url: 'https://i.imgur.com/ZF6s192m.jpg',
  alt: 'A gigantic metallic flower sculpture with reflective mirror-like petals and strong stamens.'
}, {
  name: 'Eternal Presence',
  artist: 'John Woodrow Wilson',
  description: 'Wilson was known for his preoccupation with equality, social justice, as well as the essential and spiritual qualities of humankind. This massive (7ft. or 2,13m) bronze represents what he described as "a symbolic Black presence infused with a sense of universal humanity."',
  url: 'https://i.imgur.com/aTtVpES.jpg',
  alt: 'The sculpture depicting a human head seems ever-present and solemn. It radiates calm and serenity.'
}, {
  name: 'Moai',
  artist: 'Unknown Artist',
  description: 'Located on the Easter Island, there are 1,000 moai, or extant monumental statues, created by the early Rapa Nui people, which some believe represented deified ancestors.',
  url: 'https://i.imgur.com/RCwLEoQm.jpg',
  alt: 'Three monumental stone busts with the heads that are disproportionately large with somber faces.'
}, {
  name: 'Blue Nana',
  artist: 'Niki de Saint Phalle',
  description: 'The Nanas are triumphant creatures, symbols of femininity and maternity. Initially, Saint Phalle used fabric and found objects for the Nanas, and later on introduced polyester to achieve a more vibrant effect.',
  url: 'https://i.imgur.com/Sd1AgUOm.jpg',
  alt: 'A large mosaic sculpture of a whimsical dancing female figure in a colorful costume emanating joy.'
}, {
  name: 'Ultimate Form',
  artist: 'Barbara Hepworth',
  description: 'This abstract bronze sculpture is a part of The Family of Man series located at Yorkshire Sculpture Park. Hepworth chose not to create literal representations of the world but developed abstract forms inspired by people and landscapes.',
  url: 'https://i.imgur.com/2heNQDcm.jpg',
  alt: 'A tall sculpture made of three elements stacked on each other reminding of a human figure.'
}, {
  name: 'Cavaliere',
  artist: 'Lamidi Olonade Fakeye',
  description: "Descended from four generations of woodcarvers, Fakeye's work blended traditional and contemporary Yoruba themes.",
  url: 'https://i.imgur.com/wIdGuZwm.png',
  alt: 'An intricate wood sculpture of a warrior with a focused face on a horse adorned with patterns.'
}, {
  name: 'Big Bellies',
  artist: 'Alina Szapocznikow',
  description: "Szapocznikow is known for her sculptures of the fragmented body as a metaphor for the fragility and impermanence of youth and beauty. This sculpture depicts two very realistic large bellies stacked on top of each other, each around five feet (1,5m) tall.",
  url: 'https://i.imgur.com/AlHTAdDm.jpg',
  alt: 'The sculpture reminds a cascade of folds, quite different from bellies in classical sculptures.'
}, {
  name: 'Terracotta Army',
  artist: 'Unknown Artist',
  description: 'The Terracotta Army is a collection of terracotta sculptures depicting the armies of Qin Shi Huang, the first Emperor of China. The army consisted of more than 8,000 soldiers, 130 chariots with 520 horses, and 150 cavalry horses.',
  url: 'https://i.imgur.com/HMFmH6m.jpg',
  alt: '12 terracotta sculptures of solemn warriors, each with a unique facial expression and armor.'
}, {
  name: 'Lunar Landscape',
  artist: 'Louise Nevelson',
  description: 'Nevelson was known for scavenging objects from New York City debris, which she would later assemble into monumental constructions. In this one, she used disparate parts like a bedpost, juggling pin, and seat fragment, nailing and gluing them into boxes that reflect the influence of Cubism’s geometric abstraction of space and form.',
  url: 'https://i.imgur.com/rN7hY6om.jpg',
  alt: 'A black matte sculpture where the individual elements are initially indistinguishable.'
}, {
  name: 'Aureole',
  artist: 'Ranjani Shettar',
  description: 'Shettar merges the traditional and the modern, the natural and the industrial. Her art focuses on the relationship between man and nature. Her work was described as compelling both abstractly and figuratively, gravity defying, and a "fine synthesis of unlikely materials."',
  url: 'https://i.imgur.com/okTpbHhm.jpg',
  alt: 'A pale wire-like sculpture mounted on concrete wall and descending on the floor. It appears light.'
}, {
  name: 'Hippos',
  artist: 'Taipei Zoo',
  description: 'The Taipei Zoo commissioned a Hippo Square featuring submerged hippos at play.',
  url: 'https://i.imgur.com/6o5Vuyu.jpg',
  alt: 'A group of bronze hippo sculptures emerging from the sett sidewalk as if they were swimming.'
}];
```

```css
h2 { margin-top: 10px; margin-bottom: 0; }
h3 {
 margin-top: 5px;
 font-weight: normal;
 font-size: 100%;
}
img { width: 120px; height: 120px; }
button {
  display: block;
  margin-top: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

### আপনার প্রথম হুকের সাথে পরিচয় {/*meet-your-first-hook*/}

React এ, `useState`, এবং অন্য যেকোনো ফাংশন যার শুরু "`use`" দিয়ে তাদেরকে হুক বলা হয়। 

*Hooks* এক ধরনের স্পেশাল ফাংশন যা শুধু React এর [রেন্ডারিং](/learn/render-and-commit#step-1-trigger-a-render) এর সময় কাজ করে (রেন্ডারিং নিয়ে পরের পাতায় আমরা আরো বিস্তারিত জানব)। এরা রিয়েক্ট এর বিভিন্ন ফিচারকে "আঁকরে ধরতে" সাহায্য করে।

State এদের মধ্যে একটি ফিচার, কিন্তু অন্যান্য হুক নিয়ে আমরা পরবর্তীতে জানব।

<Pitfall>

**`use` দিয়ে শুরু হয়েছে এমন Hooks—ফাংশনগুলি—শুধুমাত্র আপনার কম্পোনেন্টগুলির শীর্ষ স্তরে বা [আপনার নিজস্ব Hooks-এ](/learn/reusing-logic-with-custom-hooks) কল করা যায়** আপনি কন্ডিশন, লুপ, বা অন্যান্য নেস্টেড ফাংশনের মধ্যে Hooks কল করতে পারবেন না। Hooks হলো ফাংশন, কিন্তু এদের অনেকটা কম্পোনেন্টের নিশর্ত প্রয়োজন হিসাবে ভাবতে পারেন। এইখানে আপনি কম্পোনেন্টের এর উপরে React এর ফিচার "ব্যবহার" করেন অনেকটা যেভাবে ফাইলের উপরে "import" ব্যবহার করে মডিউল লোড করেন।
</Pitfall>

### `useState` এর বিস্তারিত {/*anatomy-of-usestate*/}

যখন আপনি [`useState`](/reference/react/useState) কল দিচ্ছেন, আপনি React-কে বলছেন যে আপনি চান এই কম্পোনেন্ট কিছু একটা মনে রাখুকঃ

```js
const [index, setIndex] = useState(0);
```

এই ক্ষেত্রে আপনি চাচ্ছেন React `index` টি মনে রাখুক.

<Note>

রীতিমতে এই জোড়ার নাম দেওয়া উচিত এভাবে `const [something, setSomething]`. আপনি এটার নাম নিজের ইচ্ছা মত দিতে পারেন, তবে রীতি মেনে চললে বিভিন্ন প্রজেক্টের মধ্যে বুঝতে সুবিধা হয়।

</Note>

`useState` এর একমাত্র আর্গুমেন্ট হচ্ছে আপনার state ভ্যারিয়েবলের **initial value**। এই উদাহরণে, `index` এর initial value `useState(0)` দিয়ে `0` তে সেট করা। 

প্রতিবার যখন আপনার কম্পোনেন্ট রেন্ডার হয়, `useState` আপনাকে দুটি মান সহ একটি অ্যারে দেয়ঃ

1. আপনার স্টোর করা ভ্যালু সহ **state variable** (`index`)।
2. **State setter function** (`setIndex`) যেটা state ভ্যারিয়েবল আপডেট করতে পারে এবং কম্পোনেন্ট আবার রেন্ডার করার জন্য React কে ট্রিগার করতে পারে।

এটা আসলে কীভাবে হয় নিচে বর্ণিত আছেঃ

```js
const [index, setIndex] = useState(0);
```

1. **আপনার কম্পোনেন্ট প্রথমবার রেন্ডার করে।** আপনি `useState`-এ প্রারম্ভিক মান হিসেবে `0` পাস করেছেন, তাই এটি `[0, setIndex]` ফেরত দেবে। React মনে রাখে যে `0` সর্বশেষ state মান।
2. **আপনি state আপডেট করেন।** যখন একজন ব্যবহারকারী বাটনে ক্লিক করে, এটি `setIndex(index + 1)` কল করে। `index` হল `0`, তাই এটি `setIndex(1)`। এটি React-কে বলে যে এখন থেকে `index` হল `1` এবং অন্য একটি রেন্ডার ট্রিগার করে।
3. **আপনার কম্পোনেন্টের দ্বিতীয় রেন্ডার।** React এখনও `useState(0)` দেখে, কিন্তু যেহেতু React *মনে রাখে* যে আপনি `index`-কে `1`-এ সেট করেছেন, তাই এটি `[1, setIndex]` ফেরত দেয়।
4. এবং এভাবেই চলতে থাকে!

## কম্পোনেন্ট এ একাধিক state ভ্যারিয়েবল ব্যবহার করা {/*giving-a-component-multiple-state-variables*/}

একটি কম্পোনেন্ট এ আপনি যত খুশি তত ধরনের ষ্টেট ভ্যারিয়েবল রাখতে পারেন। এই কম্পোনেন্ট এর দুটি state ভ্যারিয়েবল আছে, একটি নাম্বার `index` এবং একটি বুলিয়ান `showMore` যা Toggle করা হয় যখন আপনি "Show details" এ ক্লিক করেন :

<Sandpack>

```js
import { useState } from 'react';
import { sculptureList } from './data.js';

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const [showMore, setShowMore] = useState(false);

  function handleNextClick() {
    setIndex(index + 1);
  }

  function handleMoreClick() {
    setShowMore(!showMore);
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button onClick={handleNextClick}>
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i> 
        by {sculpture.artist}
      </h2>
      <h3>  
        ({index + 1} of {sculptureList.length})
      </h3>
      <button onClick={handleMoreClick}>
        {showMore ? 'Hide' : 'Show'} details
      </button>
      {showMore && <p>{sculpture.description}</p>}
      <img 
        src={sculpture.url} 
        alt={sculpture.alt}
      />
    </>
  );
}
```

```js src/data.js
export const sculptureList = [{
  name: 'Homenaje a la Neurocirugía',
  artist: 'Marta Colvin Andrade',
  description: 'Although Colvin is predominantly known for abstract themes that allude to pre-Hispanic symbols, this gigantic sculpture, an homage to neurosurgery, is one of her most recognizable public art pieces.',
  url: 'https://i.imgur.com/Mx7dA2Y.jpg',
  alt: 'A bronze statue of two crossed hands delicately holding a human brain in their fingertips.'  
}, {
  name: 'Floralis Genérica',
  artist: 'Eduardo Catalano',
  description: 'This enormous (75 ft. or 23m) silver flower is located in Buenos Aires. It is designed to move, closing its petals in the evening or when strong winds blow and opening them in the morning.',
  url: 'https://i.imgur.com/ZF6s192m.jpg',
  alt: 'A gigantic metallic flower sculpture with reflective mirror-like petals and strong stamens.'
}, {
  name: 'Eternal Presence',
  artist: 'John Woodrow Wilson',
  description: 'Wilson was known for his preoccupation with equality, social justice, as well as the essential and spiritual qualities of humankind. This massive (7ft. or 2,13m) bronze represents what he described as "a symbolic Black presence infused with a sense of universal humanity."',
  url: 'https://i.imgur.com/aTtVpES.jpg',
  alt: 'The sculpture depicting a human head seems ever-present and solemn. It radiates calm and serenity.'
}, {
  name: 'Moai',
  artist: 'Unknown Artist',
  description: 'Located on the Easter Island, there are 1,000 moai, or extant monumental statues, created by the early Rapa Nui people, which some believe represented deified ancestors.',
  url: 'https://i.imgur.com/RCwLEoQm.jpg',
  alt: 'Three monumental stone busts with the heads that are disproportionately large with somber faces.'
}, {
  name: 'Blue Nana',
  artist: 'Niki de Saint Phalle',
  description: 'The Nanas are triumphant creatures, symbols of femininity and maternity. Initially, Saint Phalle used fabric and found objects for the Nanas, and later on introduced polyester to achieve a more vibrant effect.',
  url: 'https://i.imgur.com/Sd1AgUOm.jpg',
  alt: 'A large mosaic sculpture of a whimsical dancing female figure in a colorful costume emanating joy.'
}, {
  name: 'Ultimate Form',
  artist: 'Barbara Hepworth',
  description: 'This abstract bronze sculpture is a part of The Family of Man series located at Yorkshire Sculpture Park. Hepworth chose not to create literal representations of the world but developed abstract forms inspired by people and landscapes.',
  url: 'https://i.imgur.com/2heNQDcm.jpg',
  alt: 'A tall sculpture made of three elements stacked on each other reminding of a human figure.'
}, {
  name: 'Cavaliere',
  artist: 'Lamidi Olonade Fakeye',
  description: "Descended from four generations of woodcarvers, Fakeye's work blended traditional and contemporary Yoruba themes.",
  url: 'https://i.imgur.com/wIdGuZwm.png',
  alt: 'An intricate wood sculpture of a warrior with a focused face on a horse adorned with patterns.'
}, {
  name: 'Big Bellies',
  artist: 'Alina Szapocznikow',
  description: "Szapocznikow is known for her sculptures of the fragmented body as a metaphor for the fragility and impermanence of youth and beauty. This sculpture depicts two very realistic large bellies stacked on top of each other, each around five feet (1,5m) tall.",
  url: 'https://i.imgur.com/AlHTAdDm.jpg',
  alt: 'The sculpture reminds a cascade of folds, quite different from bellies in classical sculptures.'
}, {
  name: 'Terracotta Army',
  artist: 'Unknown Artist',
  description: 'The Terracotta Army is a collection of terracotta sculptures depicting the armies of Qin Shi Huang, the first Emperor of China. The army consisted of more than 8,000 soldiers, 130 chariots with 520 horses, and 150 cavalry horses.',
  url: 'https://i.imgur.com/HMFmH6m.jpg',
  alt: '12 terracotta sculptures of solemn warriors, each with a unique facial expression and armor.'
}, {
  name: 'Lunar Landscape',
  artist: 'Louise Nevelson',
  description: 'Nevelson was known for scavenging objects from New York City debris, which she would later assemble into monumental constructions. In this one, she used disparate parts like a bedpost, juggling pin, and seat fragment, nailing and gluing them into boxes that reflect the influence of Cubism’s geometric abstraction of space and form.',
  url: 'https://i.imgur.com/rN7hY6om.jpg',
  alt: 'A black matte sculpture where the individual elements are initially indistinguishable.'
}, {
  name: 'Aureole',
  artist: 'Ranjani Shettar',
  description: 'Shettar merges the traditional and the modern, the natural and the industrial. Her art focuses on the relationship between man and nature. Her work was described as compelling both abstractly and figuratively, gravity defying, and a "fine synthesis of unlikely materials."',
  url: 'https://i.imgur.com/okTpbHhm.jpg',
  alt: 'A pale wire-like sculpture mounted on concrete wall and descending on the floor. It appears light.'
}, {
  name: 'Hippos',
  artist: 'Taipei Zoo',
  description: 'The Taipei Zoo commissioned a Hippo Square featuring submerged hippos at play.',
  url: 'https://i.imgur.com/6o5Vuyu.jpg',
  alt: 'A group of bronze hippo sculptures emerging from the sett sidewalk as if they were swimming.'
}];
```

```css
h2 { margin-top: 10px; margin-bottom: 0; }
h3 {
 margin-top: 5px;
 font-weight: normal;
 font-size: 100%;
}
img { width: 120px; height: 120px; }
button {
  display: block;
  margin-top: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

একাধিক state ভ্যারিয়েবল রাখা ভালো কাজ করে যদি তাদের state পরস্পর অসম্পর্কিত হয়, যেমন `index` এবং `showMore` এই উদাহরণে। কিন্তু যদি আপনি দেখেন যে আপনি প্রায়ই দুটি state ভ্যারিয়েবল একসাথে পরিবর্তন করছেন, তাহলে এগুলিকে একটি ভ্যারিয়েবলে মিলিয়ে ফেলা সহজ হতে পারে। উদাহরণস্বরূপ, যদি আপনার একটি ফর্ম থাকে অনেকগুলি ফিল্ড সহ, প্রতি ফিল্ডের জন্য একটি state ভ্যারিয়েবলের চেয়ে একটি অবস্থা ভ্যারিয়েবল থাকা যে একটি অবজেক্ট ধারণ করে তা আরো সুবিধাজনক। [স্টেট স্ট্রাকচার নির্বাচন](/learn/choosing-the-state-structure) এ আরও টিপস পড়ুন।

<DeepDive>

#### রিয়েক্ট কিভাবে জানে কোন state রিটার্ন করতে হবে? {/*how-does-react-know-which-state-to-return*/}

আপনি লক্ষ্য করে থাকতে পারেন যে `useState` কলের সময় কোনো তথ্য পাস করা হয় না যে এটি *কোন* state ভ্যারিয়েবলের কথা বলছে। `useState`-এ কোনো "আইডেন্টিফায়ার" পাস করা হয় না, তাহলে এটি কীভাবে জানে কোন state ভ্যারিয়েবল ফেরত দিতে হবে? এটি কি আপনার ফাংশনগুলি পার্স করার মতো কোনো ম্যাজিকের উপর নির্ভর করে? উত্তর হল না।

পরিবর্তে, তাদের সংক্ষিপ্ত সিনট্যাক্স সক্ষম করার জন্য, হুকস **একই কম্পোনেন্টের প্রতিটি রেন্ডারে একটি স্থির কল ক্রমের উপর নির্ভর করে।** এটি ব্যবহারিকভাবে ভালো কাজ করে কারণ যদি আপনি উপরের নিয়মটি অনুসরণ করেন ("কেবল হুকস শীর্ষ স্তরে কল করুন"), হুকস সবসময় একই ক্রমে কল করা হবে। এছাড়াও, একটি [লিন্টার প্লাগইন](https://www.npmjs.com/package/eslint-plugin-react-hooks) বেশিরভাগ ভুল ধরে ফেলে।

অভ্যন্তরীণভাবে, React প্রতিটি কম্পোনেন্টের জন্য একটি state জুটির অ্যারে রাখে। এটি বর্তমান জুটি ইনডেক্সও বজায় রাখে, যা রেন্ডারিংয়ের আগে `0`-এ সেট করা হয়। প্রতিবার আপনি `useState` কল করলে, React আপনাকে পরবর্তী স্টেট জুটি দেয় এবং ইনডেক্স বাড়িয়ে দেয়। এই মেকানিজম সম্পর্কে আরও জানতে [React হুকস: ম্যাজিক নয়, শুধুমাত্র অ্যারে](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e) পড়ুন।

এই উদাহরণটি **React ব্যবহার করে না** কিন্তু এটি আপনাকে একটি ধারণা দেয় যে `useState` অভ্যন্তরীণভাবে কীভাবে কাজ করে:

<Sandpack>

```js src/index.js active
let componentHooks = [];
let currentHookIndex = 0;

// How useState works inside React (simplified).
function useState(initialState) {
  let pair = componentHooks[currentHookIndex];
  if (pair) {
    // This is not the first render,
    // so the state pair already exists.
    // Return it and prepare for next Hook call.
    currentHookIndex++;
    return pair;
  }

  // This is the first time we're rendering,
  // so create a state pair and store it.
  pair = [initialState, setState];

  function setState(nextState) {
    // When the user requests a state change,
    // put the new value into the pair.
    pair[0] = nextState;
    updateDOM();
  }

  // Store the pair for future renders
  // and prepare for the next Hook call.
  componentHooks[currentHookIndex] = pair;
  currentHookIndex++;
  return pair;
}

function Gallery() {
  // Each useState() call will get the next pair.
  const [index, setIndex] = useState(0);
  const [showMore, setShowMore] = useState(false);

  function handleNextClick() {
    setIndex(index + 1);
  }

  function handleMoreClick() {
    setShowMore(!showMore);
  }

  let sculpture = sculptureList[index];
  // This example doesn't use React, so
  // return an output object instead of JSX.
  return {
    onNextClick: handleNextClick,
    onMoreClick: handleMoreClick,
    header: `${sculpture.name} by ${sculpture.artist}`,
    counter: `${index + 1} of ${sculptureList.length}`,
    more: `${showMore ? 'Hide' : 'Show'} details`,
    description: showMore ? sculpture.description : null,
    imageSrc: sculpture.url,
    imageAlt: sculpture.alt
  };
}

function updateDOM() {
  // Reset the current Hook index
  // before rendering the component.
  currentHookIndex = 0;
  let output = Gallery();

  // Update the DOM to match the output.
  // This is the part React does for you.
  nextButton.onclick = output.onNextClick;
  header.textContent = output.header;
  moreButton.onclick = output.onMoreClick;
  moreButton.textContent = output.more;
  image.src = output.imageSrc;
  image.alt = output.imageAlt;
  if (output.description !== null) {
    description.textContent = output.description;
    description.style.display = '';
  } else {
    description.style.display = 'none';
  }
}

let nextButton = document.getElementById('nextButton');
let header = document.getElementById('header');
let moreButton = document.getElementById('moreButton');
let description = document.getElementById('description');
let image = document.getElementById('image');
let sculptureList = [{
  name: 'Homenaje a la Neurocirugía',
  artist: 'Marta Colvin Andrade',
  description: 'Although Colvin is predominantly known for abstract themes that allude to pre-Hispanic symbols, this gigantic sculpture, an homage to neurosurgery, is one of her most recognizable public art pieces.',
  url: 'https://i.imgur.com/Mx7dA2Y.jpg',
  alt: 'A bronze statue of two crossed hands delicately holding a human brain in their fingertips.'  
}, {
  name: 'Floralis Genérica',
  artist: 'Eduardo Catalano',
  description: 'This enormous (75 ft. or 23m) silver flower is located in Buenos Aires. It is designed to move, closing its petals in the evening or when strong winds blow and opening them in the morning.',
  url: 'https://i.imgur.com/ZF6s192m.jpg',
  alt: 'A gigantic metallic flower sculpture with reflective mirror-like petals and strong stamens.'
}, {
  name: 'Eternal Presence',
  artist: 'John Woodrow Wilson',
  description: 'Wilson was known for his preoccupation with equality, social justice, as well as the essential and spiritual qualities of humankind. This massive (7ft. or 2,13m) bronze represents what he described as "a symbolic Black presence infused with a sense of universal humanity."',
  url: 'https://i.imgur.com/aTtVpES.jpg',
  alt: 'The sculpture depicting a human head seems ever-present and solemn. It radiates calm and serenity.'
}, {
  name: 'Moai',
  artist: 'Unknown Artist',
  description: 'Located on the Easter Island, there are 1,000 moai, or extant monumental statues, created by the early Rapa Nui people, which some believe represented deified ancestors.',
  url: 'https://i.imgur.com/RCwLEoQm.jpg',
  alt: 'Three monumental stone busts with the heads that are disproportionately large with somber faces.'
}, {
  name: 'Blue Nana',
  artist: 'Niki de Saint Phalle',
  description: 'The Nanas are triumphant creatures, symbols of femininity and maternity. Initially, Saint Phalle used fabric and found objects for the Nanas, and later on introduced polyester to achieve a more vibrant effect.',
  url: 'https://i.imgur.com/Sd1AgUOm.jpg',
  alt: 'A large mosaic sculpture of a whimsical dancing female figure in a colorful costume emanating joy.'
}, {
  name: 'Ultimate Form',
  artist: 'Barbara Hepworth',
  description: 'This abstract bronze sculpture is a part of The Family of Man series located at Yorkshire Sculpture Park. Hepworth chose not to create literal representations of the world but developed abstract forms inspired by people and landscapes.',
  url: 'https://i.imgur.com/2heNQDcm.jpg',
  alt: 'A tall sculpture made of three elements stacked on each other reminding of a human figure.'
}, {
  name: 'Cavaliere',
  artist: 'Lamidi Olonade Fakeye',
  description: "Descended from four generations of woodcarvers, Fakeye's work blended traditional and contemporary Yoruba themes.",
  url: 'https://i.imgur.com/wIdGuZwm.png',
  alt: 'An intricate wood sculpture of a warrior with a focused face on a horse adorned with patterns.'
}, {
  name: 'Big Bellies',
  artist: 'Alina Szapocznikow',
  description: "Szapocznikow is known for her sculptures of the fragmented body as a metaphor for the fragility and impermanence of youth and beauty. This sculpture depicts two very realistic large bellies stacked on top of each other, each around five feet (1,5m) tall.",
  url: 'https://i.imgur.com/AlHTAdDm.jpg',
  alt: 'The sculpture reminds a cascade of folds, quite different from bellies in classical sculptures.'
}, {
  name: 'Terracotta Army',
  artist: 'Unknown Artist',
  description: 'The Terracotta Army is a collection of terracotta sculptures depicting the armies of Qin Shi Huang, the first Emperor of China. The army consisted of more than 8,000 soldiers, 130 chariots with 520 horses, and 150 cavalry horses.',
  url: 'https://i.imgur.com/HMFmH6m.jpg',
  alt: '12 terracotta sculptures of solemn warriors, each with a unique facial expression and armor.'
}, {
  name: 'Lunar Landscape',
  artist: 'Louise Nevelson',
  description: 'Nevelson was known for scavenging objects from New York City debris, which she would later assemble into monumental constructions. In this one, she used disparate parts like a bedpost, juggling pin, and seat fragment, nailing and gluing them into boxes that reflect the influence of Cubism’s geometric abstraction of space and form.',
  url: 'https://i.imgur.com/rN7hY6om.jpg',
  alt: 'A black matte sculpture where the individual elements are initially indistinguishable.'
}, {
  name: 'Aureole',
  artist: 'Ranjani Shettar',
  description: 'Shettar merges the traditional and the modern, the natural and the industrial. Her art focuses on the relationship between man and nature. Her work was described as compelling both abstractly and figuratively, gravity defying, and a "fine synthesis of unlikely materials."',
  url: 'https://i.imgur.com/okTpbHhm.jpg',
  alt: 'A pale wire-like sculpture mounted on concrete wall and descending on the floor. It appears light.'
}, {
  name: 'Hippos',
  artist: 'Taipei Zoo',
  description: 'The Taipei Zoo commissioned a Hippo Square featuring submerged hippos at play.',
  url: 'https://i.imgur.com/6o5Vuyu.jpg',
  alt: 'A group of bronze hippo sculptures emerging from the sett sidewalk as if they were swimming.'
}];

// Make UI match the initial state.
updateDOM();
```

```html public/index.html
<button id="nextButton">
  Next
</button>
<h3 id="header"></h3>
<button id="moreButton"></button>
<p id="description"></p>
<img id="image">

<style>
* { box-sizing: border-box; }
body { font-family: sans-serif; margin: 20px; padding: 0; }
button { display: block; margin-bottom: 10px; }
</style>
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

You don't have to understand it to use React, but you might find this a helpful mental model.

</DeepDive>

## State is isolated and private {/*state-is-isolated-and-private*/}

State is local to a component instance on the screen. In other words, **if you render the same component twice, each copy will have completely isolated state!** Changing one of them will not affect the other.

In this example, the `Gallery` component from earlier is rendered twice with no changes to its logic. Try clicking the buttons inside each of the galleries. Notice that their state is independent:

<Sandpack>

```js
import Gallery from './Gallery.js';

export default function Page() {
  return (
    <div className="Page">
      <Gallery />
      <Gallery />
    </div>
  );
}

```

```js src/Gallery.js
import { useState } from 'react';
import { sculptureList } from './data.js';

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const [showMore, setShowMore] = useState(false);

  function handleNextClick() {
    setIndex(index + 1);
  }

  function handleMoreClick() {
    setShowMore(!showMore);
  }

  let sculpture = sculptureList[index];
  return (
    <section>
      <button onClick={handleNextClick}>
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i> 
        by {sculpture.artist}
      </h2>
      <h3>  
        ({index + 1} of {sculptureList.length})
      </h3>
      <button onClick={handleMoreClick}>
        {showMore ? 'Hide' : 'Show'} details
      </button>
      {showMore && <p>{sculpture.description}</p>}
      <img 
        src={sculpture.url} 
        alt={sculpture.alt}
      />
    </section>
  );
}
```

```js src/data.js
export const sculptureList = [{
  name: 'Homenaje a la Neurocirugía',
  artist: 'Marta Colvin Andrade',
  description: 'Although Colvin is predominantly known for abstract themes that allude to pre-Hispanic symbols, this gigantic sculpture, an homage to neurosurgery, is one of her most recognizable public art pieces.',
  url: 'https://i.imgur.com/Mx7dA2Y.jpg',
  alt: 'A bronze statue of two crossed hands delicately holding a human brain in their fingertips.'  
}, {
  name: 'Floralis Genérica',
  artist: 'Eduardo Catalano',
  description: 'This enormous (75 ft. or 23m) silver flower is located in Buenos Aires. It is designed to move, closing its petals in the evening or when strong winds blow and opening them in the morning.',
  url: 'https://i.imgur.com/ZF6s192m.jpg',
  alt: 'A gigantic metallic flower sculpture with reflective mirror-like petals and strong stamens.'
}, {
  name: 'Eternal Presence',
  artist: 'John Woodrow Wilson',
  description: 'Wilson was known for his preoccupation with equality, social justice, as well as the essential and spiritual qualities of humankind. This massive (7ft. or 2,13m) bronze represents what he described as "a symbolic Black presence infused with a sense of universal humanity."',
  url: 'https://i.imgur.com/aTtVpES.jpg',
  alt: 'The sculpture depicting a human head seems ever-present and solemn. It radiates calm and serenity.'
}, {
  name: 'Moai',
  artist: 'Unknown Artist',
  description: 'Located on the Easter Island, there are 1,000 moai, or extant monumental statues, created by the early Rapa Nui people, which some believe represented deified ancestors.',
  url: 'https://i.imgur.com/RCwLEoQm.jpg',
  alt: 'Three monumental stone busts with the heads that are disproportionately large with somber faces.'
}, {
  name: 'Blue Nana',
  artist: 'Niki de Saint Phalle',
  description: 'The Nanas are triumphant creatures, symbols of femininity and maternity. Initially, Saint Phalle used fabric and found objects for the Nanas, and later on introduced polyester to achieve a more vibrant effect.',
  url: 'https://i.imgur.com/Sd1AgUOm.jpg',
  alt: 'A large mosaic sculpture of a whimsical dancing female figure in a colorful costume emanating joy.'
}, {
  name: 'Ultimate Form',
  artist: 'Barbara Hepworth',
  description: 'This abstract bronze sculpture is a part of The Family of Man series located at Yorkshire Sculpture Park. Hepworth chose not to create literal representations of the world but developed abstract forms inspired by people and landscapes.',
  url: 'https://i.imgur.com/2heNQDcm.jpg',
  alt: 'A tall sculpture made of three elements stacked on each other reminding of a human figure.'
}, {
  name: 'Cavaliere',
  artist: 'Lamidi Olonade Fakeye',
  description: "Descended from four generations of woodcarvers, Fakeye's work blended traditional and contemporary Yoruba themes.",
  url: 'https://i.imgur.com/wIdGuZwm.png',
  alt: 'An intricate wood sculpture of a warrior with a focused face on a horse adorned with patterns.'
}, {
  name: 'Big Bellies',
  artist: 'Alina Szapocznikow',
  description: "Szapocznikow is known for her sculptures of the fragmented body as a metaphor for the fragility and impermanence of youth and beauty. This sculpture depicts two very realistic large bellies stacked on top of each other, each around five feet (1,5m) tall.",
  url: 'https://i.imgur.com/AlHTAdDm.jpg',
  alt: 'The sculpture reminds a cascade of folds, quite different from bellies in classical sculptures.'
}, {
  name: 'Terracotta Army',
  artist: 'Unknown Artist',
  description: 'The Terracotta Army is a collection of terracotta sculptures depicting the armies of Qin Shi Huang, the first Emperor of China. The army consisted of more than 8,000 soldiers, 130 chariots with 520 horses, and 150 cavalry horses.',
  url: 'https://i.imgur.com/HMFmH6m.jpg',
  alt: '12 terracotta sculptures of solemn warriors, each with a unique facial expression and armor.'
}, {
  name: 'Lunar Landscape',
  artist: 'Louise Nevelson',
  description: 'Nevelson was known for scavenging objects from New York City debris, which she would later assemble into monumental constructions. In this one, she used disparate parts like a bedpost, juggling pin, and seat fragment, nailing and gluing them into boxes that reflect the influence of Cubism’s geometric abstraction of space and form.',
  url: 'https://i.imgur.com/rN7hY6om.jpg',
  alt: 'A black matte sculpture where the individual elements are initially indistinguishable.'
}, {
  name: 'Aureole',
  artist: 'Ranjani Shettar',
  description: 'Shettar merges the traditional and the modern, the natural and the industrial. Her art focuses on the relationship between man and nature. Her work was described as compelling both abstractly and figuratively, gravity defying, and a "fine synthesis of unlikely materials."',
  url: 'https://i.imgur.com/okTpbHhm.jpg',
  alt: 'A pale wire-like sculpture mounted on concrete wall and descending on the floor. It appears light.'
}, {
  name: 'Hippos',
  artist: 'Taipei Zoo',
  description: 'The Taipei Zoo commissioned a Hippo Square featuring submerged hippos at play.',
  url: 'https://i.imgur.com/6o5Vuyu.jpg',
  alt: 'A group of bronze hippo sculptures emerging from the sett sidewalk as if they were swimming.'
}];
```

```css
button { display: block; margin-bottom: 10px; }
.Page > * {
  float: left;
  width: 50%;
  padding: 10px;
}
h2 { margin-top: 10px; margin-bottom: 0; }
h3 {
  margin-top: 5px;
  font-weight: normal;
  font-size: 100%;
}
img { width: 120px; height: 120px; }
button {
  display: block;
  margin-top: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

এটাই state কে আপনার মডিউলের শীর্ষে ডিক্লেয়ার করা সাধারণ ভ্যারিয়েবলগুলি থেকে ভিন্ন করে তোলে। state কোনো নির্দিষ্ট ফাংশন কল বা কোডের একটি বিশেষ স্থানের সাথে সংযুক্ত নয়, বরং এটি স্ক্রিনের নির্দিষ্ট স্থানে 'স্থানীয়'। আপনি দুটি `<Gallery />` কম্পোনেন্ট রেন্ডার করেছেন, তাই তাদের state পৃথকভাবে সংরক্ষিত হয়।

`Page` কম্পোনেন্ট সম্পর্কে লক্ষ্য করুন, এটি `Gallery` state সম্পর্কে কিছুই 'জানে না' বা এটি কোনো state আছে কিনা তাও জানে না। প্রপসের বিপরীতে, **state পুরোপুরি তার ডিক্লেয়ার করা কম্পোনেন্টের জন্য গোপন।** অভিভাবক কম্পোনেন্ট এটি পরিবর্তন করতে পারে না। এটি আপনাকে যেকোনো কম্পোনেন্টে state যোগ করতে বা সরিয়ে নিতে দেয়, অন্যান্য কম্পোনেন্টের উপর কোনো প্রভাব না ফেলে।

আপনি যদি চান দুটি গ্যালারি তাদের state সিঙ্ক্রোনাইজড রাখুক, তাহলে React-এ এটি করার সঠিক উপায় হলো সন্তান কম্পোনেন্টগুলি থেকে state সরিয়ে নেওয়া এবং তা তাদের নিকটতম যৌথ parent এ যোগ করা। পরের কয়েকটি পৃষ্ঠা একটি একক কম্পোনেন্টের state সংগঠনের উপর ফোকাস করবে, কিন্তু আমরা এই বিষয়ে [কম্পোনেন্টগুলির মধ্যে state শেয়ারিং](/learn/sharing-state-between-components) পৃষ্ঠায় ফিরে আসব।

<Recap>

* যখন একটি কম্পোনেন্টের রেন্ডারের মধ্যে কিছু তথ্য 'মনে রাখতে' হয়, তখন একটি state ভ্যারিয়েবল ব্যবহার করুন।
* state ভ্যারিয়েবলগুলি `useState` হুক কল করে ডিক্লেয়ার হয়।
* হুক হলো বিশেষ ফাংশন যা `use` দিয়ে শুরু হয়। এগুলি আপনাকে state এর মতো React বৈশিষ্ট্যগুলিতে 'হুক করতে' দেয়।
* হুক আপনাকে ইম্পোর্টের কথা মনে করাতে পারে: এগুলি অবশ্যই শর্তহীনভাবে কল করতে হবে। কোনো কম্পোনেন্টের শীর্ষ স্তরে অথবা অন্য কোনো হুকের মধ্যে হুকস, যেমন `useState`, কল করা বৈধ।
* `useState` হুক দুটি মান ফেরত দেয়: বর্তমান state এবং এটি আপডেট করার ফাংশন।
* আপনি একাধিক state ভ্যারিয়েবল রাখতে পারেন। অভ্যন্তরীণভাবে, React এগুলিকে তাদের ক্রম অনুসারে মিলিয়ে দেয়।
* state একটি কম্পোনেন্টের জন্য ব্যক্তিগত। যদি আপনি এটি দুটি জায়গায় রেন্ডার করেন, প্রতিটি কপি নিজের state পায়।

</Recap>



<Challenges>

#### গ্যালারিটি সম্পূর্ণ করুণ {/*complete-the-gallery*/}

যখন আপনি শেষ ভাস্কর্যের উপর "Next" বাটন চাপেন, তখন কোড ক্র্যাশ করে। ক্র্যাশ প্রতিরোধের জন্য লজিক সংশোধন করুন। আপনি ইভেন্ট হ্যান্ডলারে অতিরিক্ত লজিক যোগ করে অথবা অ্যাকশন সম্ভব না হলে বাটনটি ডিজেবল করে এটি করতে পারেন।

ক্র্যাশ ঠিক করার পরে, একটি "Previous" বাটন যোগ করুন যা আগের ভাস্কর্যটি দেখায়। এটি প্রথম ভাস্কর্যে ক্র্যাশ করা উচিত নয়।

<Sandpack>

```js
import { useState } from 'react';
import { sculptureList } from './data.js';

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const [showMore, setShowMore] = useState(false);

  function handleNextClick() {
    setIndex(index + 1);
  }

  function handleMoreClick() {
    setShowMore(!showMore);
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button onClick={handleNextClick}>
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i> 
        by {sculpture.artist}
      </h2>
      <h3>  
        ({index + 1} of {sculptureList.length})
      </h3>
      <button onClick={handleMoreClick}>
        {showMore ? 'Hide' : 'Show'} details
      </button>
      {showMore && <p>{sculpture.description}</p>}
      <img 
        src={sculpture.url} 
        alt={sculpture.alt}
      />
    </>
  );
}
```

```js src/data.js
export const sculptureList = [{
  name: 'Homenaje a la Neurocirugía',
  artist: 'Marta Colvin Andrade',
  description: 'Although Colvin is predominantly known for abstract themes that allude to pre-Hispanic symbols, this gigantic sculpture, an homage to neurosurgery, is one of her most recognizable public art pieces.',
  url: 'https://i.imgur.com/Mx7dA2Y.jpg',
  alt: 'A bronze statue of two crossed hands delicately holding a human brain in their fingertips.'  
}, {
  name: 'Floralis Genérica',
  artist: 'Eduardo Catalano',
  description: 'This enormous (75 ft. or 23m) silver flower is located in Buenos Aires. It is designed to move, closing its petals in the evening or when strong winds blow and opening them in the morning.',
  url: 'https://i.imgur.com/ZF6s192m.jpg',
  alt: 'A gigantic metallic flower sculpture with reflective mirror-like petals and strong stamens.'
}, {
  name: 'Eternal Presence',
  artist: 'John Woodrow Wilson',
  description: 'Wilson was known for his preoccupation with equality, social justice, as well as the essential and spiritual qualities of humankind. This massive (7ft. or 2,13m) bronze represents what he described as "a symbolic Black presence infused with a sense of universal humanity."',
  url: 'https://i.imgur.com/aTtVpES.jpg',
  alt: 'The sculpture depicting a human head seems ever-present and solemn. It radiates calm and serenity.'
}, {
  name: 'Moai',
  artist: 'Unknown Artist',
  description: 'Located on the Easter Island, there are 1,000 moai, or extant monumental statues, created by the early Rapa Nui people, which some believe represented deified ancestors.',
  url: 'https://i.imgur.com/RCwLEoQm.jpg',
  alt: 'Three monumental stone busts with the heads that are disproportionately large with somber faces.'
}, {
  name: 'Blue Nana',
  artist: 'Niki de Saint Phalle',
  description: 'The Nanas are triumphant creatures, symbols of femininity and maternity. Initially, Saint Phalle used fabric and found objects for the Nanas, and later on introduced polyester to achieve a more vibrant effect.',
  url: 'https://i.imgur.com/Sd1AgUOm.jpg',
  alt: 'A large mosaic sculpture of a whimsical dancing female figure in a colorful costume emanating joy.'
}, {
  name: 'Ultimate Form',
  artist: 'Barbara Hepworth',
  description: 'This abstract bronze sculpture is a part of The Family of Man series located at Yorkshire Sculpture Park. Hepworth chose not to create literal representations of the world but developed abstract forms inspired by people and landscapes.',
  url: 'https://i.imgur.com/2heNQDcm.jpg',
  alt: 'A tall sculpture made of three elements stacked on each other reminding of a human figure.'
}, {
  name: 'Cavaliere',
  artist: 'Lamidi Olonade Fakeye',
  description: "Descended from four generations of woodcarvers, Fakeye's work blended traditional and contemporary Yoruba themes.",
  url: 'https://i.imgur.com/wIdGuZwm.png',
  alt: 'An intricate wood sculpture of a warrior with a focused face on a horse adorned with patterns.'
}, {
  name: 'Big Bellies',
  artist: 'Alina Szapocznikow',
  description: "Szapocznikow is known for her sculptures of the fragmented body as a metaphor for the fragility and impermanence of youth and beauty. This sculpture depicts two very realistic large bellies stacked on top of each other, each around five feet (1,5m) tall.",
  url: 'https://i.imgur.com/AlHTAdDm.jpg',
  alt: 'The sculpture reminds a cascade of folds, quite different from bellies in classical sculptures.'
}, {
  name: 'Terracotta Army',
  artist: 'Unknown Artist',
  description: 'The Terracotta Army is a collection of terracotta sculptures depicting the armies of Qin Shi Huang, the first Emperor of China. The army consisted of more than 8,000 soldiers, 130 chariots with 520 horses, and 150 cavalry horses.',
  url: 'https://i.imgur.com/HMFmH6m.jpg',
  alt: '12 terracotta sculptures of solemn warriors, each with a unique facial expression and armor.'
}, {
  name: 'Lunar Landscape',
  artist: 'Louise Nevelson',
  description: 'Nevelson was known for scavenging objects from New York City debris, which she would later assemble into monumental constructions. In this one, she used disparate parts like a bedpost, juggling pin, and seat fragment, nailing and gluing them into boxes that reflect the influence of Cubism’s geometric abstraction of space and form.',
  url: 'https://i.imgur.com/rN7hY6om.jpg',
  alt: 'A black matte sculpture where the individual elements are initially indistinguishable.'
}, {
  name: 'Aureole',
  artist: 'Ranjani Shettar',
  description: 'Shettar merges the traditional and the modern, the natural and the industrial. Her art focuses on the relationship between man and nature. Her work was described as compelling both abstractly and figuratively, gravity defying, and a "fine synthesis of unlikely materials."',
  url: 'https://i.imgur.com/okTpbHhm.jpg',
  alt: 'A pale wire-like sculpture mounted on concrete wall and descending on the floor. It appears light.'
}, {
  name: 'Hippos',
  artist: 'Taipei Zoo',
  description: 'The Taipei Zoo commissioned a Hippo Square featuring submerged hippos at play.',
  url: 'https://i.imgur.com/6o5Vuyu.jpg',
  alt: 'A group of bronze hippo sculptures emerging from the sett sidewalk as if they were swimming.'
}];
```

```css
button { display: block; margin-bottom: 10px; }
.Page > * {
  float: left;
  width: 50%;
  padding: 10px;
}
h2 { margin-top: 10px; margin-bottom: 0; }
h3 {
  margin-top: 5px;
  font-weight: normal;
  font-size: 100%;
}
img { width: 120px; height: 120px; }
```

</Sandpack>

<Solution>

এটি উভয় ইভেন্ট হ্যান্ডলারের মধ্যে একটি সুরক্ষামূলক শর্ত যোগ করে এবং প্রয়োজনে বাটনগুলি ডিজেবল করে:

<Sandpack>

```js
import { useState } from 'react';
import { sculptureList } from './data.js';

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const [showMore, setShowMore] = useState(false);

  let hasPrev = index > 0;
  let hasNext = index < sculptureList.length - 1;

  function handlePrevClick() {
    if (hasPrev) {
      setIndex(index - 1);
    }
  }

  function handleNextClick() {
    if (hasNext) {
      setIndex(index + 1);
    }
  }

  function handleMoreClick() {
    setShowMore(!showMore);
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button
        onClick={handlePrevClick}
        disabled={!hasPrev}
      >
        Previous
      </button>
      <button
        onClick={handleNextClick}
        disabled={!hasNext}
      >
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i> 
        by {sculpture.artist}
      </h2>
      <h3>  
        ({index + 1} of {sculptureList.length})
      </h3>
      <button onClick={handleMoreClick}>
        {showMore ? 'Hide' : 'Show'} details
      </button>
      {showMore && <p>{sculpture.description}</p>}
      <img 
        src={sculpture.url} 
        alt={sculpture.alt}
      />
    </>
  );
}
```

```js src/data.js hidden
export const sculptureList = [{
  name: 'Homenaje a la Neurocirugía',
  artist: 'Marta Colvin Andrade',
  description: 'Although Colvin is predominantly known for abstract themes that allude to pre-Hispanic symbols, this gigantic sculpture, an homage to neurosurgery, is one of her most recognizable public art pieces.',
  url: 'https://i.imgur.com/Mx7dA2Y.jpg',
  alt: 'A bronze statue of two crossed hands delicately holding a human brain in their fingertips.'  
}, {
  name: 'Floralis Genérica',
  artist: 'Eduardo Catalano',
  description: 'This enormous (75 ft. or 23m) silver flower is located in Buenos Aires. It is designed to move, closing its petals in the evening or when strong winds blow and opening them in the morning.',
  url: 'https://i.imgur.com/ZF6s192m.jpg',
  alt: 'A gigantic metallic flower sculpture with reflective mirror-like petals and strong stamens.'
}, {
  name: 'Eternal Presence',
  artist: 'John Woodrow Wilson',
  description: 'Wilson was known for his preoccupation with equality, social justice, as well as the essential and spiritual qualities of humankind. This massive (7ft. or 2,13m) bronze represents what he described as "a symbolic Black presence infused with a sense of universal humanity."',
  url: 'https://i.imgur.com/aTtVpES.jpg',
  alt: 'The sculpture depicting a human head seems ever-present and solemn. It radiates calm and serenity.'
}, {
  name: 'Moai',
  artist: 'Unknown Artist',
  description: 'Located on the Easter Island, there are 1,000 moai, or extant monumental statues, created by the early Rapa Nui people, which some believe represented deified ancestors.',
  url: 'https://i.imgur.com/RCwLEoQm.jpg',
  alt: 'Three monumental stone busts with the heads that are disproportionately large with somber faces.'
}, {
  name: 'Blue Nana',
  artist: 'Niki de Saint Phalle',
  description: 'The Nanas are triumphant creatures, symbols of femininity and maternity. Initially, Saint Phalle used fabric and found objects for the Nanas, and later on introduced polyester to achieve a more vibrant effect.',
  url: 'https://i.imgur.com/Sd1AgUOm.jpg',
  alt: 'A large mosaic sculpture of a whimsical dancing female figure in a colorful costume emanating joy.'
}, {
  name: 'Ultimate Form',
  artist: 'Barbara Hepworth',
  description: 'This abstract bronze sculpture is a part of The Family of Man series located at Yorkshire Sculpture Park. Hepworth chose not to create literal representations of the world but developed abstract forms inspired by people and landscapes.',
  url: 'https://i.imgur.com/2heNQDcm.jpg',
  alt: 'A tall sculpture made of three elements stacked on each other reminding of a human figure.'
}, {
  name: 'Cavaliere',
  artist: 'Lamidi Olonade Fakeye',
  description: "Descended from four generations of woodcarvers, Fakeye's work blended traditional and contemporary Yoruba themes.",
  url: 'https://i.imgur.com/wIdGuZwm.png',
  alt: 'An intricate wood sculpture of a warrior with a focused face on a horse adorned with patterns.'
}, {
  name: 'Big Bellies',
  artist: 'Alina Szapocznikow',
  description: "Szapocznikow is known for her sculptures of the fragmented body as a metaphor for the fragility and impermanence of youth and beauty. This sculpture depicts two very realistic large bellies stacked on top of each other, each around five feet (1,5m) tall.",
  url: 'https://i.imgur.com/AlHTAdDm.jpg',
  alt: 'The sculpture reminds a cascade of folds, quite different from bellies in classical sculptures.'
}, {
  name: 'Terracotta Army',
  artist: 'Unknown Artist',
  description: 'The Terracotta Army is a collection of terracotta sculptures depicting the armies of Qin Shi Huang, the first Emperor of China. The army consisted of more than 8,000 soldiers, 130 chariots with 520 horses, and 150 cavalry horses.',
  url: 'https://i.imgur.com/HMFmH6m.jpg',
  alt: '12 terracotta sculptures of solemn warriors, each with a unique facial expression and armor.'
}, {
  name: 'Lunar Landscape',
  artist: 'Louise Nevelson',
  description: 'Nevelson was known for scavenging objects from New York City debris, which she would later assemble into monumental constructions. In this one, she used disparate parts like a bedpost, juggling pin, and seat fragment, nailing and gluing them into boxes that reflect the influence of Cubism’s geometric abstraction of space and form.',
  url: 'https://i.imgur.com/rN7hY6om.jpg',
  alt: 'A black matte sculpture where the individual elements are initially indistinguishable.'
}, {
  name: 'Aureole',
  artist: 'Ranjani Shettar',
  description: 'Shettar merges the traditional and the modern, the natural and the industrial. Her art focuses on the relationship between man and nature. Her work was described as compelling both abstractly and figuratively, gravity defying, and a "fine synthesis of unlikely materials."',
  url: 'https://i.imgur.com/okTpbHhm.jpg',
  alt: 'A pale wire-like sculpture mounted on concrete wall and descending on the floor. It appears light.'
}, {
  name: 'Hippos',
  artist: 'Taipei Zoo',
  description: 'The Taipei Zoo commissioned a Hippo Square featuring submerged hippos at play.',
  url: 'https://i.imgur.com/6o5Vuyu.jpg',
  alt: 'A group of bronze hippo sculptures emerging from the sett sidewalk as if they were swimming.'
}];
```

```css
button { display: block; margin-bottom: 10px; }
.Page > * {
  float: left;
  width: 50%;
  padding: 10px;
}
h2 { margin-top: 10px; margin-bottom: 0; }
h3 {
  margin-top: 5px;
  font-weight: normal;
  font-size: 100%;
}
img { width: 120px; height: 120px; }
```

</Sandpack>

লক্ষ্য করুন কীভাবে `hasPrev` এবং `hasNext` রিটার্ন করা JSX এবং ইভেন্ট হ্যান্ডলারগুলি *উভয়ের* মধ্যে ব্যবহৃত হয়েছে! এই সুবিধাজনক প্যাটার্নটি কাজ করে কারণ ইভেন্ট হ্যান্ডলার ফাংশনগুলি রেন্ডারিং সময় ঘোষিত যেকোনো ভ্যারিয়েবলের উপর ["ক্লোজ ওভার"](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) হয়।

</Solution>

#### আটকে যাওয়া ফর্ম ইনপুটগুলি ঠিক করুন {/*fix-stuck-form-inputs*/}

আপনি যখন ইনপুট ফিল্ডগুলিতে টাইপ করেন, কিছুই প্রদর্শিত হয় না। মনে হয় যেন ইনপুট মানগুলি খালি স্ট্রিংগুলি দিয়ে "আটকে" গেছে। প্রথম `<input>`-এর `value` সবসময় `firstName` ভ্যারিয়েবলের সাথে মিলে যায়, এবং দ্বিতীয় `<input>`-এর `value` সবসময় `lastName` ভ্যারিয়েবলের সাথে মিলে যায়। এটি ঠিক। উভয় ইনপুটের `onChange` ইভেন্ট হ্যান্ডলার রয়েছে, যা সর্বশেষ ব্যবহারকারীর ইনপুট (`e.target.value`) অনুসারে ভ্যারিয়েবলগুলিকে আপডেট করার চেষ্টা করে। তবে, ভ্যারিয়েবলগুলি মনে হয় রি-রেন্ডারের মধ্যে তাদের মানগুলি "মনে রাখে না"। এটি ঠিক করতে state ভ্যারিয়েবলের ব্যবহার করুন।

<Sandpack>

```js
export default function Form() {
  let firstName = '';
  let lastName = '';

  function handleFirstNameChange(e) {
    firstName = e.target.value;
  }

  function handleLastNameChange(e) {
    lastName = e.target.value;
  }

  function handleReset() {
    firstName = '';
    lastName = '';
  }

  return (
    <form onSubmit={e => e.preventDefault()}>
      <input
        placeholder="First name"
        value={firstName}
        onChange={handleFirstNameChange}
      />
      <input
        placeholder="Last name"
        value={lastName}
        onChange={handleLastNameChange}
      />
      <h1>Hi, {firstName} {lastName}</h1>
      <button onClick={handleReset}>Reset</button>
    </form>
  );
}
```

```css 
h1 { margin-top: 10px; }
```

</Sandpack>

<Solution>

প্রথমে, React থেকে `useState` ইম্পোর্ট করুন। তারপর, `firstName` এবং `lastName`-কে `useState` দ্বারা ডিক্লেয়ার করা state ভ্যারিয়েবলগুলি দিয়ে প্রতিস্থাপন করুন। শেষে, প্রতিটি `firstName = ...` অ্যাসাইনমেন্টকে `setFirstName(...)` দিয়ে প্রতিস্থাপন করুন, এবং `lastName`-এর জন্যও একই কাজ করুন। `handleReset`-কে আপডেট করতে ভুলবেন না, যাতে রিসেট বাটন কাজ করে।

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  function handleFirstNameChange(e) {
    setFirstName(e.target.value);
  }

  function handleLastNameChange(e) {
    setLastName(e.target.value);
  }

  function handleReset() {
    setFirstName('');
    setLastName('');
  }

  return (
    <form onSubmit={e => e.preventDefault()}>
      <input
        placeholder="First name"
        value={firstName}
        onChange={handleFirstNameChange}
      />
      <input
        placeholder="Last name"
        value={lastName}
        onChange={handleLastNameChange}
      />
      <h1>Hi, {firstName} {lastName}</h1>
      <button onClick={handleReset}>Reset</button>
    </form>
  );
}
```

```css 
h1 { margin-top: 10px; }
```

</Sandpack>

</Solution>

#### ক্র্যাশ ঠিক করুন {/*fix-a-crash*/}

এখানে একটা ছোট্ট ফর্ম আছে যেটা ব্যবহারকারী থেকে কিছু ফিডব্যাক নেবার কথা।. ফিডব্যাক সাবমিট হলে, এটা একটা thank-you মেসেজ দেখানর কথা।. কিন্তু, এটা ক্র্যাশ করে এবং এরর মেসেজ দেখায় যে "Rendered fewer hooks than expected"। আপনি কি ভুলটা চিহ্নিত করে ঠিক করতে পারবেন?

<Hint>

_কোথায়_ হুক কল করা যাবে, সেটার কি কোন সীমাবদ্ধতা আছে? এই কম্পোনেন্টটি কি কোন নিয়ম ভঙ্গ করছে? খুঁজে দেখুন কোন কমেন্ট লিন্টার চেক বন্ধ করে রেখেছে কি না -- বেশিরভাগ সময় এমন জায়গাতেই বাগ লুকিয়ে থাকে!

</Hint>

<Sandpack>

```js
import { useState } from 'react';

export default function FeedbackForm() {
  const [isSent, setIsSent] = useState(false);
  if (isSent) {
    return <h1>Thank you!</h1>;
  } else {
    // eslint-disable-next-line
    const [message, setMessage] = useState('');
    return (
      <form onSubmit={e => {
        e.preventDefault();
        alert(`Sending: "${message}"`);
        setIsSent(true);
      }}>
        <textarea
          placeholder="Message"
          value={message}
          onChange={e => setMessage(e.target.value)}
        />
        <br />
        <button type="submit">Send</button>
      </form>
    );
  }
}
```

</Sandpack>

<Solution>

হুক কম্পোনেন্ট ফাংশনের একদম টপ লেভেলেই কেবল কল দেওয়া যায়। এখানে, প্রথম `isSent` ডেফিনিশন এই নিয়ম অনুসরণ করে, কিন্তু `message` ডেফিনিশন একটি কন্ডিশনে নেস্টেড করা হয়েছে।

সমস্যা ঠিক করতে কন্ডিশন থেকে এটি সরিয়ে নিনঃ

<Sandpack>

```js
import { useState } from 'react';

export default function FeedbackForm() {
  const [isSent, setIsSent] = useState(false);
  const [message, setMessage] = useState('');

  if (isSent) {
    return <h1>Thank you!</h1>;
  } else {
    return (
      <form onSubmit={e => {
        e.preventDefault();
        alert(`Sending: "${message}"`);
        setIsSent(true);
      }}>
        <textarea
          placeholder="Message"
          value={message}
          onChange={e => setMessage(e.target.value)}
        />
        <br />
        <button type="submit">Send</button>
      </form>
    );
  }
}
```

</Sandpack>

মনে রাখবেন, Hooks অবশ্যই শর্তহীনভাবে এবং সবসময় একই ক্রমে কল করতে হবে!

আপনি অপ্রয়োজনীয় `else` শাখা সরিয়ে নেস্টিং কমাতে পারেন। তবে, প্রথম `return`-এর আগে সব Hooks কল ঘটানো গুরুত্বপূর্ণ।

<Sandpack>

```js
import { useState } from 'react';

export default function FeedbackForm() {
  const [isSent, setIsSent] = useState(false);
  const [message, setMessage] = useState('');

  if (isSent) {
    return <h1>Thank you!</h1>;
  }

  return (
    <form onSubmit={e => {
      e.preventDefault();
      alert(`Sending: "${message}"`);
      setIsSent(true);
    }}>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <br />
      <button type="submit">Send</button>
    </form>
  );
}
```

</Sandpack>

`useState` কলটির দ্বিতীয় উদাহরণটি `if` কন্ডিশনের পরে সরিয়ে নিয়ে যান এবং লক্ষ্য করুন কীভাবে এটি আবার ভেঙে যায়।

যদি আপনার লিন্টার [React-এর জন্য কনফিগার করা থাকে](/learn/editor-setup#linting), তাহলে এরকম ভুল করলে আপনি একটি লিন্ট এরর দেখতে পাবেন। যদি আপনি লোকালি এই ভুল কোড চেষ্টা করে কোনো এরর না দেখেন, তাহলে আপনার প্রজেক্টের জন্য লিন্টিং সেট আপ করা প্রয়োজন।

</Solution>

#### অপ্রয়োজনীয় state বাদ দিন {/*remove-unnecessary-state*/}

<<<<<<< HEAD
যখন বাটনটি ক্লিক করা হয়, এই উদাহরণটি ব্যবহারকারীর নাম জানতে চাইবার কথা এবং এর পরে অভিবাদন সহ একটি এলার্ট দেখানোর কথা। নামতই রাখবার জন্য আপনি state ব্যবহারের চেষ্টা করেছেন, কিন্তু কোন একটি কারণে এটি কেবল "Hello, !" দেখায়।
=======
When the button is clicked, this example should ask for the user's name and then display an alert greeting them. You tried to use state to keep the name, but for some reason the first time it shows "Hello, !", and then "Hello, [name]!" with the previous input every time after.
>>>>>>> 55986965fbf69c2584040039c9586a01bd54eba7

এই কোড ঠিক করার জন্য, অপ্রয়োজনীয় state ভ্যারিয়েবল ফেলে দিন। ([এটা কেন কাজ করল না](/learn/state-as-a-snapshot) সেটা নিয়ে আমরা পরে আলোচনা করব।)

আপনি ব্যাখ্যা করতে পারবেন কেন এই state ভ্যারিয়েবলটি অপ্রয়োজনীয়?

<Sandpack>

```js
import { useState } from 'react';

export default function FeedbackForm() {
  const [name, setName] = useState('');

  function handleClick() {
    setName(prompt('What is your name?'));
    alert(`Hello, ${name}!`);
  }

  return (
    <button onClick={handleClick}>
      Greet
    </button>
  );
}
```

</Sandpack>

<Solution>

এখানে একটি ঠিক ভার্সন দেওয়া হলো যা ফাংশনের এর মধ্যে প্রয়োজনীয় রেগুলার `name` ভ্যারিয়েবল ব্যবহার করে:

<Sandpack>

```js
export default function FeedbackForm() {
  function handleClick() {
    const name = prompt('What is your name?');
    alert(`Hello, ${name}!`);
  }

  return (
    <button onClick={handleClick}>
      Greet
    </button>
  );
}
```

</Sandpack>

State ভ্যারিয়েবল শুধু রি-রেন্ডার এর মাঝে তথ্য মনে রাখার জন্য দরকার। যেকোনো একই ইভেন্ট হ্যান্ডলার এর মধ্যে রেগুলার ভ্যারিয়েবলই যথেষ্ট। যেখানে রেগুলার ভ্যারিয়েবল যথেষ্ট সেখানে state ভ্যারিয়েবল ব্যবহার করা উচিত না।

</Solution>

</Challenges>
