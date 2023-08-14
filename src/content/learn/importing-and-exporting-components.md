---
title: কম্পোনেন্ট ইম্পোর্ট এবং এক্সপোর্ট করা
---

<Intro>

কম্পোনেন্ট ব্যাবহারের যাদু রয়েছে এদের পুনঃব্যবহারযোগ্যতার মধ্যেঃ আপনি এমন কম্পোনেন্ট তৈরি করতে পারেন যা অন্যান্য কম্পোনেন্ট এর সমন্বয়ে গঠিত। কিন্তু আপনি যখন কম্পোনেন্ট এর ভিতর কম্পোনেন্ট নেস্ট করা শুরু করবেন, তখন সেগুলিকে নিজস্ব ফাইল এ বিভক্ত করা ই শ্রেয়। এতে করে আপনার ফাইল গুলোকে সহজে স্ক্যান এবং অন্যান্য স্থানে পুনঃব্যবহার করতে পারবেন। 

</Intro>

<YouWillLearn>

* রুট কম্পোনেন্ট ফাইল কি
* কম্পোনেন্ট ইম্পোর্ট এবং এক্সপোর্ট কিভাবে করবেন
* ডিফল্ট এবং নেমড ইম্পোর্ট এবং এক্সপোর্ট কখন ব্যাবহার করবেন
* একটি ফাইল থেকে একাধিক কম্পোনেন্ট ইম্পোর্ট এবং এক্সপোর্ট কিভাবে করবেন
* কম্পোনেন্ট কে একাধিক ফাইলে কিভাবে ভাগ করবেন

</YouWillLearn>

## রুট কম্পোনেন্ট ফাইল {/*the-root-component-file*/}

[আপনার প্রথম কম্পোনেন্ট](/learn/your-first-component) হিসেবে আপনি একটি `Profile` কম্পোনেন্ট, এবং এটি রেন্ডার করে এমন একটি `Gallery` কম্পোনেন্ট তৈরি করেছেনঃ

<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

<<<<<<< HEAD
এই উদাহরণ টি বর্তমানে রয়েছে **রুট কম্পোনেন্ট ফাইলে**, যার নাম `App.js`। [Create React App](https://create-react-app.dev/) এ আপনার অ্যাপ্লিকেশন টি `src/App.js` ফাইলে থাকে। যদিও আপনার সেটআপ এর উপর নির্ভর করে আপনার রুট কম্পোনেন্ট অন্য ফাইলেও থাকতে পারে। যদি আপনি ফাইল ভিত্তিক রাউটিং সহ কোন ফ্রেমওয়ার্ক ব্যবহার করেন, যেমন Next.js, তাহলে প্রতিটি পৃষ্ঠার জন্য আপনার রুট কম্পোনেন্ট ভিন্ন হবে।
=======
These currently live in a **root component file,** named `App.js` in this example. Depending on your setup, your root component could be in another file, though. If you use a framework with file-based routing, such as Next.js, your root component will be different for every page.
>>>>>>> 819518cfe32dd2db3b765410247c30feea713c77

## কম্পোনেন্ট ইম্পোর্ট এবং এক্সপোর্ট করা {/*exporting-and-importing-a-component*/}

আমরা যদি ল্যান্ডিং পেজ টা চেঞ্জ করে এখানে কিছু বিজ্ঞান বই এর তালিকা দেখাতে চাই বা সব প্রোফাইল কে অন্য কোথাও দেখাতে চাই তাহলে আমাদের রুট কম্পোনেন্ট ফাইল থেকে `Gallery` এবং `Profile` কে বের করে নেওয়া উচিত। এটি করলে আপনি এদের অন্য ফাইলে ব্যবহার করতে পারবেন। কম্পোনেন্ট কে একটি ফাইল থেকে অন্য ফাইলে নিয়ে যেতে আপনাকে তিনটি ধাপ অনুসরণ করতে হবেঃ

১. কম্পোনেন্ট গুলো রাখার জন্য একটি নতুন জাভাস্ক্রিপ্ট ফাইল **তৈরি** করুন।
২. আপনার ফাংশন কম্পোনেন্ট কে এই নতুন ফাইল থেকে **এক্সপোর্ট** করুন। ([ডিফল্ট](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/export#using_the_default_export) অথবা [নেমড](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/export#using_named_exports) এক্সপোর্ট ব্যবহার করে)
৩. কম্পোনেন্ট টা ব্যবহার করার জন্য পূর্বের ফাইল থেকে এটি **ইম্পোর্ট** করুন। (যথাযত ইম্পোর্ট টেকনিক, [ডিফল্ট](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import#importing_defaults) অথবা [নেমড](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import#import_a_single_export_from_a_module), ব্যাবহার করে)

এখানে `Profile` এবং `Gallery` দুটি কম্পোনেন্ট কে `App.js` থেকে নতুন ফাইল `Gallery.js` এ নিয়ে আসা হয়েছে। এখন আপনি `Gallery.js` ফাইল থেকে `App.js` ফাইলে `Gallery` কে ইম্পোর্ট করতে পারবেনঃ

<Sandpack>

```js App.js
import Gallery from './Gallery.js';

export default function App() {
  return (
    <Gallery />
  );
}
```

```js Gallery.js
function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

খেয়াল করুন এই উদাহরণটি এখন দুটি কম্পোনেন্ট ফাইলে ভাগ করা হয়েছেঃ

- `Gallery.js`:
     - এখানে `Profile` কম্পোনেন্ট একটি ফাংশন যা একই ফাইলের মধ্যে ব্যবহার করা হয় এবং এটি এক্সপোর্ট করা হয় নি।
     - `Gallery` কম্পোনেন্টটি **ডিফল্ট এক্সপোর্ট** হিসেবে এক্সপোর্ট করা হয়েছে।
     
- `App.js`:
     - `Gallery` কম্পোনেন্টটি **ডিফল্ট ইম্পোর্ট** হিসেবে `Gallery.js` থেকে ইম্পোর্ট করা হয়েছে।
     - রুট `App` কম্পোনেন্টটি **ডিফল্ট এক্সপোর্ট** হিসেবে এক্সপোর্ট করা হয়েছে।

<Note>

হয়তো আপনি এমন ইম্পোর্ট দেখেছেন যেখানে `.js` ফাইল এক্সটেনশন ব্যবহার করা হয় নি যেমনঃ

```js 
import Gallery from './Gallery';
```

React এ `'./Gallery.js'` অথবা `'./Gallery'` দুটিই কাজ করবে, তবে প্রথম টি হলো [native ES Modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) এর কাছাকাছি।

</Note>

<DeepDive>

#### ডিফল্ট বনাম নেমড এক্সপোর্ট {/*default-vs-named-exports*/}

দুটি প্রাথমিক উপায়ে জাভাস্ক্রিপ্টে ভ্যালু এক্সপোর্ট করা যায়ঃ ডিফল্ট এক্সপোর্ট এবং নেমড এক্সপোর্ট। এখন পর্যন্ত আমাদের উদাহরণগুলোতে শুধুমাত্র ডিফল্ট এক্সপোর্ট ব্যবহার করা হয়েছে। তবে আপনি একই ফাইলে একটি বা দুটোই ব্যবহার করতে পারেন। **একটি ফাইলে একাধিক _ডিফল্ট_ এক্সপোর্ট থাকতে পারে না, তবে একটি ফাইলে যত খুশি _নেমড_ এক্সপোর্ট থাকতে পারে।**

![ডিফল্ট বনাম নেমড এক্সপোর্ট](/images/docs/illustrations/i_import-export.svg)

যেভাবে আপনি কম্পোনেন্ট এক্সপোর্ট করবেন তার উপর নির্ভর করে আপনাকে কম্পোনেন্টটি ইম্পোর্ট করতে হবে। আপনি যদি একটি ডিফল্ট এক্সপোর্ট কে ইম্পোর্ট করার জন্য নেমড এক্সপোর্ট এর মত করে কোড লিখেন তাহলে আপনি একটি এরর পাবেন। এই চার্ট আপনাকে সহজে সাহায্য করতে পারবেঃ

| সিনট্যাক্স           | এক্সপোর্ট স্টেটমেন্ট                           | ইম্পোর্ট স্টেটমেন্ট                          |
| -----------      | -----------                                | -----------                               |
| ডিফল্ট  | `export default function Button() {}` | `import Button from './Button.js';`     |
| নেমড    | `export function Button() {}`         | `import { Button } from './Button.js';` |

যখন আপনি একটি _ডিফল্ট_ ইম্পোর্ট করবেন তখন `import` এর পরে যেকোনো নাম ব্যবহার করতে পারেন। উদাহরণস্বরূপ, আপনি `import Banana from './Button.js'` লিখতে পারেন এবং এটি আপনাকে একই ডিফল্ট এক্সপোর্ট দেবে। তবে নেমড ইম্পোর্ট এর ক্ষেত্রে নামটি উভয় ফাইল এ মিলতে হবে। এই কারণেই এদেরকে _নেমড_ ইম্পোর্ট বলা হয়েছে।

**সাধারণত একটি ফাইল থেকে একটি কম্পোনেন্ট এক্সপোর্ট করতে ডিফল্ট এক্সপোর্ট এবং একাধিক কম্পোনেন্ট এবং ভ্যালু এক্সপোর্ট করতে নেমড এক্সপোর্ট ব্যবহৃত হয়।** আপনি যেকোনো কোডিং স্টাইল ব্যবহার করুন তবে সবসময় আপনার কম্পোনেন্ট ফাংশন এবং তাদের ফাইল এর নামগুলো তাদের কাজ সম্পর্কিত ভালো নাম দিন। নাম ছাড়া কম্পোনেন্ট, যেমন `export default () => {}`, ব্যবহার করতে নিরুৎসাহিত করা হয় কারণ এদের ডিবাগিং করা কঠিন।

</DeepDive>

## একটি ফাইল থেকে একাধিক কম্পোনেন্ট ইম্পোর্ট এবং এক্সপোর্ট করা {/*exporting-and-importing-multiple-components-from-the-same-file*/}

ধরুন আপনি গ্যালারির পরিবর্তে একটিমাত্র `Profile` দেখাতে চাচ্ছেন। তাহলে আপনি `Profile` কম্পোনেন্ট টাও এক্সপোর্ট করতে পারবেন। কিন্তু `Gallery.js` ফাইলে ইতিমধ্যে একটি *ডিফল্ট* এক্সপোর্ট আছে এবং একটি ফাইল এ _দুইটি_ ডিফল্ট এক্সপোর্ট থাকতে পারে না। আপনি একটি নতুন ফাইল তৈরি করে সেটি থেকে ডিফল্ট এক্সপোর্ট করতে পারেন অথবা `Profile` এর জন্য একটি *নেমড* এক্সপোর্ট যোগ করতে পারেন। **একটি ফাইলে শুধুমাত্র একটি ডিফল্ট এক্সপোর্ট থাকতে পারে কিন্তু নেমড এক্সপোর্ট একাধিক থাকতে পারে!**

<Note>

ডিফল্ট এবং নেমড এক্সপোর্ট এর মধ্যে বিভ্রান্তি এড়ানোর জন্য কিছু দল শুধুমাত্র একটি স্টাইল (ডিফল্ট বা নেমড) ব্যবহার করতে পছন্দ করে এবং একটি ফাইলে একাধিক স্টাইলের ব্যাবহার এড়িয়ে চলে। আপনার কাজের জন্য যেটি ভালো মনে হয় সেটি ব্যাবহার করুন।

</Note>

প্রথমত নেমড এক্সপোর্ট ব্যবহার করে `Gallery.js` থেকে `Profile` **এক্সপোর্ট** করুন (কোন `default` কীওয়ার্ড নেই):

```js
export function Profile() {
  // ...
}
```

এরপর নেমড ইম্পোর্ট ব্যাবহার করে `App.js` থেকে `Gallery.js` এর `Profile` কম্পোনেন্ট **ইম্পোর্ট** করুন (দ্বিতীয় বন্ধনী সহ):

```js
import { Profile } from './Gallery.js';
```

সবশেষে `App` কম্পোনেন্ট থেকে `<Profile />` **রেন্ডার** করুন:

```js
export default function App() {
  return <Profile />;
}
```

এখন `Gallery.js` এ দুটি এক্সপোর্ট আছে: একটি ডিফল্ট `Gallery` এক্সপোর্ট এবং একটি নেমড `Profile` এক্সপোর্ট। `App.js` উভয়ই ইম্পোর্ট করে। এই উদাহরণে `<Profile />` কে `<Gallery />` তে পরিবর্তন করে দেখুনঃ

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import { Profile } from './Gallery.js';

export default function App() {
  return (
    <Profile />
  );
}
```

```js Gallery.js
export function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

এখানে আপনি ডিফল্ট এবং নেমড এক্সপোর্ট এর মিশ্রণ ব্যবহার করছেনঃ

* `Gallery.js`:
  - `Profile` কম্পোনেন্ট কে **নেমড এক্সপোর্ট হিসেবে `Profile` নামে এক্সপোর্ট** করে।
  - `Gallery` কম্পোনেন্ট কে **ডিফল্ট এক্সপোর্ট হিসেবে** এক্সপোর্ট করে।
* `App.js`:
  - `Profile` কে **নেমড ইম্পোর্ট হিসেবে `Profile` নামে** `Gallery.js` থেকে ইম্পোর্ট করে।
  - `Gallery` কে **ডিফল্ট ইম্পোর্ট হিসেবে** `Gallery.js` থেকে ইম্পোর্ট করে।
  - রুট `App` কম্পোনেন্ট কে **ডিফল্ট এক্সপোর্ট হিসেবে** এক্সপোর্ট করে।

<Recap>

এই পৃষ্ঠায় আপনি শিখলেনঃ

* রুট কম্পোনেন্ট ফাইল কি
* কিভাবে কম্পোনেন্ট ইম্পোর্ট এবং এক্সপোর্ট করা হয়
* কখন এবং কিভাবে ডিফল্ট এবং নেমড ইম্পোর্ট এবং এক্সপোর্ট ব্যবহার করা হয়
* একই ফাইল থেকে একাধিক কম্পোনেন্ট এক্সপোর্ট করার পদ্ধতি

</Recap>



<Challenges>

#### কম্পোনেন্টকে আরো ভাগ করুন {/*split-the-components-further*/}

বর্তমানে `Gallery.js` ফাইল থেকে `Profile` এবং `Gallery` উভয়ই এক্সপোর্ট করা হয়েছে, যা কিছুটা বিভ্রান্তিকর।

`Profile` কম্পোনেন্ট কে এর নিজস্ব `Profile.js` ফাইলে সরিয়ে নিন, এবং `App` কম্পোনেন্ট কে পরিবর্তন করে `<Profile />` এবং `<Gallery />` উভয়ই একটির পর অন্যটি রেন্ডার করুন।

`Profile` কম্পোনেন্টটির জন্য আপনার ইচ্ছামত ডিফল্ট অথবা নেমড এক্সপোর্ট ব্যবহার করতে পারেন, কিন্তু নিশ্চিত হউন যে আপনি `App.js` এবং `Gallery.js` উভয় ফাইলেই সঠিক ইম্পোর্ট সিনট্যাক্স ব্যবহার করছেন। আপনি ডিপ ডাইভ থেকে নিচের টেবিলটি দেখতে পারেনঃ

| সিনট্যাক্স           | এক্সপোর্ট স্টেটমেন্ট                           | ইম্পোর্ট স্টেটমেন্ট                          |
| -----------      | -----------                                | -----------                               |
| ডিফল্ট  | `export default function Button() {}` | `import Button from './Button.js';`     |
| নেমড    | `export function Button() {}`         | `import { Button } from './Button.js';` |

<Hint>

আপনার কম্পোনেন্ট টি যেখানে ব্যবহার করা হয়েছে সেখানে সেটি ইম্পোর্ট করতে ভুলবেন না। `Gallery` ও তো `Profile` কে ব্যবহার করছে, তাই না? 

</Hint>

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import { Profile } from './Gallery.js';

export default function App() {
  return (
    <div>
      <Profile />
    </div>
  );
}
```

```js Gallery.js active
// Move me to Profile.js!
export function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

এক প্রকারের এক্সপোর্ট সঠিকভাবে কাজ করার পর অন্য প্রকারের এক্সপোর্ট ব্যাবহার করে কাজ করার চেষ্টা করুন।

<Solution>

নেমড এক্সপোর্ট ব্যাবহার করে সমাধানঃ 

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import { Profile } from './Profile.js';

export default function App() {
  return (
    <div>
      <Profile />
      <Gallery />
    </div>
  );
}
```

```js Gallery.js
import { Profile } from './Profile.js';

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
export function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

ডিফল্ট এক্সপোর্ট ব্যাবহার করে সমাধানঃ 

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import Profile from './Profile.js';

export default function App() {
  return (
    <div>
      <Profile />
      <Gallery />
    </div>
  );
}
```

```js Gallery.js
import Profile from './Profile.js';

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
export default function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

</Solution>

</Challenges>
