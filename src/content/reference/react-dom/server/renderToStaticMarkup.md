---
title: renderToStaticMarkup
---

<Intro>

`renderToStaticMarkup` একটি নন-ইন্টার‌্যাক্টিভ React ট্রি কে একটি HTML স্ট্রিং এ রেন্ডার করে।

```js
const html = renderToStaticMarkup(reactNode)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `renderToStaticMarkup(reactNode)` {/*rendertostaticmarkup*/}

সার্ভারে, আপনার অ্যাপটি HTML এ রেন্ডার করার জন্য কল করুন `renderToStaticMarkup`।

```js
import { renderToStaticMarkup } from 'react-dom/server';

const html = renderToStaticMarkup(<Page />);
```

এটা আপনার React কম্পোনেন্টের নন-ইন্টার‌্যাক্টিভ HTML আউটপুট তৈরি করবে।

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

* `reactNode`: একটা React নোড যা আপনি HTML এ রেন্ডার করতে চান। উদাহরণস্বরূপ,  `<Page />` এর মত একটি JSX নোড।

#### রিটার্ন {/*returns*/}

একটি HTML স্ট্রিং।

#### সতর্কতা {/*caveats*/}

* `renderToStaticMarkup` এর আউটপুটকে hydrate করা যায় না।

* `renderToStaticMarkup` সীমাবদ্ধভাবে suspense সাপোর্ট করে। যদি একটি কম্পোনেন্ট suspend করে, `renderToStaticMarkup` তৎক্ষণাৎ এর ফলব্যাক HTML হিসেবে পাঠিয়ে দেয়।

* `renderToStaticMarkup` ব্রাউজারে কাজ করে, কিন্তু একে ক্লায়েন্ট কোডে ব্যবহার না করার পরামর্শ দেওয়া হয়। আপনার যদি ব্রাউজারে একটি কম্পোনেন্ট HTML এ রেন্ডার করার প্রয়োজন হয়, [HTML কোডটিকে একটি DOM নোডে রেন্ডার করুন।](/reference/react-dom/server/renderToString#removing-rendertostring-from-the-client-code)

---

## ব্যবহার {/*usage*/}

### একটি নন-ইন্টার‌্যাক্টিভ React ট্রি কে HTML হিসেবে একটি স্ট্রিং এ রেন্ডার করা {/*rendering-a-non-interactive-react-tree-as-html-to-a-string*/}

আপনার অ্যাপটি একটি HTML স্ট্রিং এ রেন্ডার করার জন্য `renderToStaticMarkup` কল করুন যেটা আপনি আপনার সার্ভার রেসপন্সের সাথে পাঠাতে পারবেনঃ

```js {5-6}
import { renderToStaticMarkup } from 'react-dom/server';

// Route handler syntax আপনার ব্যাকেন্ড ফ্রেমওয়ার্কের উপর নির্ভর করবে
app.use('/', (request, response) => {
  const html = renderToStaticMarkup(<Page />);
  response.send(html);
});
```

এটা আপনার React কম্পোনেন্টের প্রাথমিক নন-ইন্টার‌্যাক্টিভ HTML আউটপুট তৈরি করবে।

<Pitfall>

এই মেথডটা রেন্ডার করে **নন-ইন্টার‌্যাক্টিভ HTML যা hydrate করা যায় না।** এটা কাজে লাগে যখন আপনি React কে একটি simple static page generator হিসেবে ব্যবহার করতে চান, অথবা আপনি সম্পূর্ণরূপে স্ট্যাটিক কনটেন্ট যেমন ইমেইল রেন্ডার করছেন।

ইন্টার‌্যাক্টিভ অ্যাপের উচিত সার্ভারে [`renderToString`](/reference/react-dom/server/renderToString) ব্যবহার করা এবং ক্লায়েন্টে [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) ব্যবহার করা।

</Pitfall>
