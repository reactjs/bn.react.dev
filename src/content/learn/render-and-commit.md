---
title: রেন্ডার এবং কমিট
---

<Intro>

কম্পোনেন্টগুলো স্ক্রীনে প্রদর্শিত হওয়ার আগে React দ্বারা তা রেন্ডার হয়। এই রেন্ডার প্রক্রিয়ার ধাপগুলি বোঝার মাধ্যমে আপনি আপনার কোড কিভাবে আচরণ করে এবং কিভাবে তা আরো কার্যকর করা যায় তা বুঝতে পারবেন।

</Intro>

<YouWillLearn>

* React এর ভাষায় রেন্ডারিং বলতে কি বুঝায়
* কখন এবং কিভাবে React একটি কম্পোনেন্ট রেন্ডার করে
* একটি কম্পোনেন্ট ব্রাউজারে প্রদর্শিত হওয়ার আগে কি কি ধাপ অনুসরণ করে
* রেন্ডারিং এ কেন সবসময় DOM আপডেট হয় না

</YouWillLearn>

ধরুন কম্পোনেন্টগুলো রান্নাঘরের বাবুর্চি যারা বিভিন্ন উপকরন থেকে সুস্বাদু খাবার রান্না করছে। এই উদাহরণে React যেহেতু গ্রাহকদের থেকে তাদের অর্ডারগুলো গ্রহন করে এবং তা পরিবেশন করে, React-কে আমরা ওয়েটার হিসেবে চিন্তা করতে পারি। UI থেকে অনুরোধ গ্রহন করা এবং তা পরিবেশন করার এই প্রক্রিয়াটির তিনটি পদক্ষেপ রয়েছে।

1. **ট্রিগারিং** : একটি রেন্ডার ট্রিগার করা (গ্রাহকের অর্ডার রান্নাঘরে প্রেরণ করা)
2. **রেন্ডারিং** : কম্পোনেন্ট রেন্ডার করা (রান্নাঘরে অর্ডার তৈরি করা)
3. **কমিটিং** : DOM এ কমিট করা (অর্ডার টেবিলে পরিবেশন করা)

<IllustrationBlock sequential>
  <Illustration caption="Trigger" alt="React as a server in a restaurant, fetching orders from the users and delivering them to the Component Kitchen." src="/images/docs/illustrations/i_render-and-commit1.png" />
  <Illustration caption="Render" alt="The Card Chef gives React a fresh Card component." src="/images/docs/illustrations/i_render-and-commit2.png" />
  <Illustration caption="Commit" alt="React delivers the Card to the user at their table." src="/images/docs/illustrations/i_render-and-commit3.png" />
</IllustrationBlock>

## ধাপ ১: একটি রেন্ডার ট্রিগার করা {/*step-1-trigger-a-render*/}

একটি কম্পোনেন্ট রেন্ডার হবার দুটি কারণ রয়েছে:

1. এটি কম্পোনেন্টের **প্রাথমিক রেন্ডার।**
2. কম্পোনেন্টের (বা এর একটি প্যারেন্ট কম্পোনেন্টের) **স্টেট আপডেট করা হয়েছে।**

### প্রাথমিক রেন্ডার {/*initial-render*/}

 অ্যাপ শুরু হওয়ার সময় প্রাথমিক রেন্ডার ট্রিগার হয়। টার্গেট DOM নোড দিয়ে createRoot কল করে তারপর কম্পোনেন্ট দিয়ে তার render মেথড কল করে এই কাজটি করা হয়। ফ্রেমওয়ার্ক এবং স্যান্ডবক্স কখনো কখনো এই কোডটি লুকিয়ে রাখে।

<Sandpack>

```js index.js active
import Image from './Image.js';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'))
root.render(<Image />);
```

```js Image.js
export default function Image() {
  return (
    <img
      src="https://i.imgur.com/ZF6s192.jpg"
      alt="'Floralis Genérica' by Eduardo Catalano: a gigantic metallic flower sculpture with reflective petals"
    />
  );
}
```

</Sandpack>

`root.render()` কমেন্ট আউট করলে দেখবেন কম্পোনেন্টটি আর দেখা যাচ্ছে না।

### স্টেট আপডেট হওয়ার সময় রি-রেন্ডার: {/*re-renders-when-state-updates*/}

কম্পোনেন্টের প্রাথমিক রেন্ডারের পরে আপনি [set ফাংশন](/reference/react/useState#setstate) দ্বারা কম্পোনেন্টের স্টেট আপডেট করে আরও রেন্ডার ট্রিগার করতে পারবেন। কম্পোনেন্টের স্টেট আপডেট হলে React একটি রেন্ডার স্বয়ংক্রিয়ভাবে কিউ করে রাখে। (রেস্তোরাঁয় গেস্টের প্রথম অর্ডারের পরে চা, ডেজার্ট এবং অন্যান্য ধরনের জিনিস অর্ডার দেওয়ার মতো কল্পনা করতে পারেন।)

<IllustrationBlock sequential>
  <Illustration caption="State update..." alt="React as a server in a restaurant, serving a Card UI to the user, represented as a patron with a cursor for their head. They patron expresses they want a pink card, not a black one!" src="/images/docs/illustrations/i_rerender1.png" />
  <Illustration caption="...triggers..." alt="React returns to the Component Kitchen and tells the Card Chef they need a pink Card." src="/images/docs/illustrations/i_rerender2.png" />
  <Illustration caption="...render!" alt="The Card Chef gives React the pink Card." src="/images/docs/illustrations/i_rerender3.png" />
</IllustrationBlock>

## ধাপ ২: React কম্পোনেন্ট রেন্ডার করে {/*step-2-react-renders-your-components*/}

রেন্ডার ট্রিগার করার পরে স্ক্রীনে কী প্রদর্শন করতে হবে তা বের করতে React কম্পোনেন্টগুলো কল করে। কম্পোনেন্টগুলো React দ্বারা এই কল করাই হল "রেন্ডারিং"।

* **প্রাথমিক রেন্ডারে,** React root কম্পোনেন্ট কল করবে।
* **পরবর্তী রেন্ডার এ,** React সেই কম্পোনেন্টটি কল করবে যার স্টেট আপডেটে রেন্ডার ট্রিগার হয়েছে।

এই প্রক্রিয়াটি রিকার্সিভ: আপডেট করা কম্পোনেন্ট যদি অন্য কোনও কম্পোনেন্ট রিটার্ন করে, তবে React পরবর্তীতে সেই কম্পোনেন্টটি রেন্ডার করবে এবং যদি সেই কম্পোনেন্টটি আবার কোন কম্পোনেন্ট রিটার্ন করে, তবে ঐ কম্পোনেন্টটিও রেন্ডার করবে। রেন্ডারিং এভাবে চলতে থাকবে যতক্ষণ পর্যন্ত সব নেস্টেড কম্পোনেন্ট রেন্ডার না হয়। এরপরেই React স্ক্রীনে কি দেখাতে হবে তা স্পষ্টভাবে জানতে পারে।

নিচের উদাহরণে React কয়েকবার `Gallery()` এবং `Image()` কল করবে:

<Sandpack>

```js Gallery.js active
export default function Gallery() {
  return (
    <section>
      <h1>Inspiring Sculptures</h1>
      <Image />
      <Image />
      <Image />
    </section>
  );
}

function Image() {
  return (
    <img
      src="https://i.imgur.com/ZF6s192.jpg"
      alt="'Floralis Genérica' by Eduardo Catalano: a gigantic metallic flower sculpture with reflective petals"
    />
  );
}
```

```js index.js
import Gallery from './Gallery.js';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'))
root.render(<Gallery />);
```

```css
img { margin: 0 10px 10px 0; }
```

</Sandpack>

* **প্রাথমিক রেন্ডারের সময়,** React `<section>`, `<h1>`, এবং তিনটি `<img>` ট্যাগের জন্য [DOM নোডগুলো তৈরি করবে](https://developer.mozilla.org/docs/Web/API/Document/createElement)। 
* **রি-রেন্ডারের সময়,** React পূর্ববর্তী রেন্ডার আর বর্তমান রেন্ডারের মধ্যবর্তী পরিবর্তন ক্যালকুলেট করবে। কিন্তু কমিট ধাপের আগ পর্যন্ত React এই তথ্য দিয়ে কিছুই করবে না।

<Pitfall>

রেন্ডারিং-এ সবসময় [pure ক্যালকুলেশন](/learn/keeping-components-pure) হতে হবে:

* **একই ইনপুট , একই আউটপুট।** যদি একটি কম্পোনেন্টে একই ইনপুট দেওয়া হয় তাহলে ঐ কম্পোনেন্টটি সর্বদা একই JSX রিটার্ন করবে। (যদি কেউ টমেটো সালাদ অর্ডার করে, তখন যেন পেঁয়াজের সালাদ না পায়!)
* **এটি নিজে নিজের কাজ করে।** এটি রেন্ডারিং এর আগে বিদ্যমান কোনো অবজেক্ট বা ভেরিয়েবল পরিবর্তন করে না। (রেস্তোরাঁয় যেরকম একজনের অর্ডারে অন্য কারো অর্ডার পরিবর্তন হয় না।)

অন্যথায়, যখন আপনার কোডবেস বড় হবে তখন বিভ্রান্তিকর বাগ এবং অপ্রত্যাশিত আচরণ পেতে পারেন। "Strict Mode"-এ React প্রতিটি কম্পোনেন্ট দুবার করে কল করে যা impure ফাংশন দ্বারা উদ্ভূত বাগ বের করতে সাহায্য করে।

</Pitfall>

<DeepDive>

#### পারফরম্যান্স অপ্টিমাইজ করা {/*optimizing-performance*/}

যদি আপডেটেড কম্পোনেন্ট DOM-tree এর অনেক উপরে থাকে তাহলে React এর ডিফল্ট রেন্ডারিং এর সময় আপডেটেড কম্পোনেন্টের ভেতরের সব কম্পোনেন্ট রেন্ডার হওয়ার ব্যাপারটি optimal নয়। যদি আপনি কোন পারফরম্যান্স সমস্যার সম্মুখীন হন, তবে তা সমাধানের কয়েকটি উপায় [পারফরম্যান্স](https://reactjs.org/docs/optimizing-performance.html) সেকশনে বর্ণনা করা হয়েছে। **Don't optimize prematurely!**

</DeepDive>

## ধাপ ৩: React DOM-এ পরিবর্তন কমিট করে {/*step-3-react-commits-changes-to-the-dom*/}

কম্পোনেন্টগুলো রেন্ডার(কল) করার পর React DOM-কে পরিবর্তন করবে।

* **প্রাথমিক রেন্ডারের জন্য,** [`appendChild()`](https://developer.mozilla.org/docs/Web/API/Node/appendChild) DOM API ব্যবহার করে React সমস্ত DOM নোডগুলো স্ক্রীনে দেখাবে। 
* **রি-রেন্ডারের জন্য,** DOM-কে সাম্প্রতিক রেন্ডারিং আউটপুটের সাথে মেলানোর জন্য React রেন্ডারিং-ধাপে ক্যালকুলেট করা পরিবর্তনগুলো ন্যূনতম দরকারি অপারেশনে সম্পন্ন করবে।

**React শুধুমাত্র পাশাপাশি দুটি রেন্ডারের মধ্যে পার্থক্য থাকলেই DOM নোডগুলো উপডেট করে।** উদাহরণস্বরূপ, এখানে একটি কম্পোনেন্ট দেওয়া হলো যা প্রতি সেকেন্ডে নতুন props নিয়ে রি-রেন্ডার হয়। লক্ষ্য করুন কীভাবে `<input>` এ কিছু টেক্সট লিখে তার `value` পরিবর্তন করলেও রি-রেন্ডারে তা হারিয়ে যায় না:

<Sandpack>

```js Clock.js active
export default function Clock({ time }) {
  return (
    <>
      <h1>{time}</h1>
      <input />
    </>
  );
}
```

```js App.js hidden
import { useState, useEffect } from 'react';
import Clock from './Clock.js';

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}

export default function App() {
  const time = useTime();
  return (
    <Clock time={time.toLocaleTimeString()} />
  );
}
```

</Sandpack>

কারণ এই শেষ ধাপে, React শুধু নতুন `time`-এর সাথে `<h1>` এর কনটেন্ট আপডেট করে। React দেখে যে  JSX-এ `<input>` এর অবস্থান অপরিবর্তিত আছে, তাই React `<input>` - বা এর `value` কোনটাই স্পর্শ করে না!
## উপসংহার: ব্রাউজার পেইন্ট {/*epilogue-browser-paint*/}

রেন্ডারিং সম্পন্ন করে React DOM আপডেট করার পর ব্রাউজার স্ক্রীন পুনরায় পেইন্ট করে। যদিও এই প্রক্রিয়াটি "ব্রাউজার রেন্ডারিং" নামে পরিচিত, আমরা ডক জুড়ে বিভ্রান্তি এড়াতে এটিকে "পেইন্টিং" হিসাবেই উল্লেখ করব৷

<Illustration alt="A browser painting 'still life with card element'." src="/images/docs/illustrations/i_browser-paint.png" />

<Recap>

* একটি React অ্যাপে যেকোনো স্ক্রীন আপডেট তিনটি ধাপে ঘটে:
  1. ট্রিগার
  2. রেন্ডার
  3. কমিট
* কম্পোনেন্টের ভুল-ত্রুটি খুঁজে বের করতে "Strict Mode" ব্যাবহার করতে পারেন
* রেন্ডারিং-এর ফলাফল আগেরবারের মত একই হলে React DOM-কে স্পর্শ করে না

</Recap>

