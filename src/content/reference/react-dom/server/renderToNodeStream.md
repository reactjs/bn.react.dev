---
title: renderToNodeStream
---

<Deprecated>

এই API ভবিষ্যতে React এর বড় কোন আপডেটে অপসারণ করা হবে। এর বদলে [`renderToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) ব্যবহার করুন।

</Deprecated>

<Intro>

`renderToNodeStream` একটি React ট্রি কে একটি [Node.js Readable Stream](https://nodejs.org/api/stream.html#readable-streams) এ রেন্ডার করে।

```js
const stream = renderToNodeStream(reactNode)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `renderToNodeStream(reactNode)` {/*rendertonodestream*/}

সার্ভারে, [Node.js Readable Stream](https://nodejs.org/api/stream.html#readable-streams) পাবার জন্য `renderToNodeStream` কল করুন, যেটা আপনি রেসপন্সের মধ্যে pipe করে দিতে পারবেন।

```js
import { renderToNodeStream } from 'react-dom/server';

const stream = renderToNodeStream(<App />);
stream.pipe(response);
```

ক্লায়েন্টে, সার্ভার থেকে তৈরী HTML ইন্টার‍্যাক্টিভ করতে [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) কল করুন।

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

* `reactNode`: একটা React নোড যেটা আপনি HTML এ রেন্ডার করতে চান। উদাহরণস্বরূপ, `<App />` এর মত একটি JSX এলিমেন্ট।

#### রিটার্ন {/*returns*/}

একটা [Node.js Readable Stream](https://nodejs.org/api/stream.html#readable-streams) যা একটি HTML স্ট্রিং আউটপুট হিসেবে দেয়।

#### সতর্কতা {/*caveats*/}

* এই মেথডটি কোন আউটপুট রিটার্ন করবার আগে সকল [Suspense boundary](/reference/react/Suspense) এর সম্পূর্ণ হবার জন্য অপেক্ষা করবে। 

* React 18 নাগাদ, এই মেথডটি এর সকল আউটপুট বাফার করে, তাই এটা আসলে streaming এর সুবিধা দিতে পারে না। এ কারণেই, এর বদলে  [`renderToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) ব্যবহার করার জন্য আপনাকে পরামর্শ দেওয়া হচ্ছে।

* রিটার্ন হওয়া stream হচ্ছে utf-8 এ এনকোড করা byte stream। আপনি যদি অন্য কোন এনকোডিং এ stream চান, তাহলে [iconv-lite](https://www.npmjs.com/package/iconv-lite) এর মত কোন প্রজেক্ট দেখতে পারেন, যা টেক্সট transcoding এর জন্য transform stream দেয়।

---

## ব্যবহার {/*usage*/}

### একটা React ট্রি কে HTML হিসেবে একটা Node.js Readable Stream এ রেন্ডার করা {/*rendering-a-react-tree-as-html-to-a-nodejs-readable-stream*/}


একটি [Node.js Readable Stream](https://nodejs.org/api/stream.html#readable-streams) পাবার জন্য `renderToNodeStream` কল করুন, যেটা আপনি সার্ভার রেসপন্সে pipe করে দিতে পারবেনঃ

```js {5-6}
import { renderToNodeStream } from 'react-dom/server';

// Route handler syntax আপনার ব্যাকেন্ড ফ্রেমওয়ার্কের উপর নির্ভর করে
app.use('/', (request, response) => {
  const stream = renderToNodeStream(<App />);
  stream.pipe(response);
});
```

Stream আপনার React কম্পোনেন্টগুলোর প্রাথমিক নন-ইন্টার‍্যাকটিভ HTML আউটপুট তৈরী করবে। ক্লায়েন্টে, সার্ভারে তৈরী হওয়া HTML কে *hydrate* করতে এবং একে ইন্টার‍্যাকটিভ করতে আপনাকে  [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) কল করতে হবে।
