---
title: renderToStaticNodeStream
---

<Intro>

`renderToStaticNodeStream` একটি নন-ইন্টার‍্যাকটিভ React ট্রি কে একটি [Node.js Readable Stream.](https://nodejs.org/api/stream.html#readable-streams) এ রেন্ডার করে।

```js
const stream = renderToStaticNodeStream(reactNode)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `renderToStaticNodeStream(reactNode)` {/*rendertostaticnodestream*/}

সার্ভারে, [Node.js Readable Stream](https://nodejs.org/api/stream.html#readable-streams) পাবার জন্য `renderToStaticNodeStream` কল করুন।

```js
import { renderToStaticNodeStream } from 'react-dom/server';

const stream = renderToStaticNodeStream(<Page />);
stream.pipe(response);
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

এই stream আপনার React কম্পোনেন্ট থেকে নন-ইন্টার‍্যাকটিভ HTML আউটপুট তৈরি করবে।

#### প্যারামিটার {/*parameters*/}

* `reactNode`: একটা React নোড যেটা আপনি HTML এ রেন্ডার করতে চান। যেমন `<Page />` এর মত একটি JSX এলিমেন্ট।

#### রিটার্ন {/*returns*/}

একটা [Node.js Readable Stream](https://nodejs.org/api/stream.html#readable-streams) যেটা একটা HTML স্ট্রিং আউটপুট দেয়। ফলাফলে যে HTML পাওয়া যায় সেটা ক্লায়েন্টে hydrate করা যায় না।

#### Caveats {/*caveats*/}

* `renderToStaticNodeStream` আউটপুট hydrate করা যায় না।

* এই মেথডটি কোন আউটপুট রিটার্ন করবার আগে সকল [Suspense boundary](/reference/react/Suspense) এর সম্পূর্ণ হবার জন্য অপেক্ষা করবে।

* React 18 নাগাদ, এই মেথডটি এর সকল আউটপুট বাফার করে, তাই এটা আসলে streaming এর সুবিধা দিতে পারে না।

* রিটার্ন হওয়া stream হচ্ছে utf-8 এ এনকোড করা byte stream। আপনি যদি অন্য কোন এনকোডিং এ stream চান, তাহলে [iconv-lite](https://www.npmjs.com/package/iconv-lite) এর মত কোন প্রজেক্ট দেখতে পারেন, যা টেক্সট transcoding এর জন্য transform stream দেয়।

---

## ব্যবহার {/*usage*/}

### একটা React ট্রি কে HTML হিসেবে একটা Node.js Readable Stream এ রেন্ডার করা {/*rendering-a-react-tree-as-html-to-a-nodejs-readable-stream*/}

একটি [Node.js Readable Stream](https://nodejs.org/api/stream.html#readable-streams) পাবার জন্য `renderToStaticNodeStream` কল করুন, যেটা আপনি সার্ভার রেসপন্সে pipe করে দিতে পারবেনঃ

```js {5-6}
import { renderToStaticNodeStream } from 'react-dom/server';

// Route handler syntax আপনার ব্যাকেন্ড ফ্রেমওয়ার্কের উপর নির্ভর করে
app.use('/', (request, response) => {
  const stream = renderToStaticNodeStream(<Page />);
  stream.pipe(response);
});
```

Stream টি আপনার React কম্পোনেন্টগুলোর প্রাথমিক নন-ইন্টার‍্যাকটিভ HTML আউটপুট তৈরী করবে।

<Pitfall>

এই মেথডটি রেন্ডার করে **নন-ইন্টার‍্যাকটিভ HTML যা hydrate করা যায় না।** এটা কাজে লাগে যদি আপনি React কে simple static page generator হিসেবে ব্যবহার করতে চান, অথবা আপনি ইমাইলের মত সম্পূর্ণ static কন্টেন্ট রেন্ডার করছেন।

ইন্টার‍্যাকটিভ অ্যাপের উচিত সার্ভারে [`renderToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) এবং ক্লায়েন্টে [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) ব্যবহার করা।

</Pitfall>
