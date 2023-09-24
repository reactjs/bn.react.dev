---
title: flushSync
---

<Pitfall>

`flushSync` এর ব্যবহার খুব একটা হয় না এবং এটা আপনার অ্যাপের পারফরম্যান্সে বিরূপ প্রভাব ফেলতে পারে।

</Pitfall>

<Intro>

`flushSync` আপনাকে সাহায্য করবে যদি আপনি কোন একটি কলব্যাকের মধ্যকার যেকোন আপডেট সিঙ্ক্রোনাসভাবে flush করার জন্য React কে বাধ্য করতে চান। এটা নিশ্চিত করে যে DOM সাথে সাথে আপডেট হয়ে যাচ্ছে। 

```js
flushSync(callback)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `flushSync(callback)` {/*flushsync*/}

কোন একটি pending কাজ ফ্লাশ করার জন্য এবং একই সাথে DOM আপডেট করার জন্য React কে বাধ্য করতে এবং `flushSync` কল করুন।

```js
import { flushSync } from 'react-dom';

flushSync(() => {
  setSomething(123);
});
```

বেশির ভাগ সময়, `flushSync` এর ব্যবহার এড়িয়ে চলা যায়। `flushSync` কে ব্যবহার করুন last resort হিসেবে।

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}


* `callback`: একটি ফাংশন। React সাথে সাথে এই কলব্যাক কল দিবে এবং এর মধ্যে থাকা যেকোন আপডেট সিঙ্ক্রোনাসভাবে ফ্লাশ করে দিবে। এটা একই সাথে যেকোন পেন্ডীং আপডেট, Effect অথবা Effect এর মধ্যকার আপডেট ফ্লাশ করে দিতে পারে। যদি এই `flushSync` কলের জন্য একটি আপডেট সাসপেন্ড হয়ে যায়, ফলব্যাক আবার দেখা যেতে পারে।

#### রিটার্ন {/*returns*/}

`flushSync` রিটার্ন করে `undefined`।

#### সতর্কতা {/*caveats*/}

* `flushSync` বেশ উল্লেখজনকভাবে পারফরম্যান্স কমিয়ে দিতে পারে, কম ব্যবহারের চেষ্টা করুন।
* `flushSync` পেন্ডিং সাসপেন্স বাউন্ডারিগুলোকে তাদের `fallback` state দেখাতে বাধ্য করতে পারে।
* `flushSync` পেন্ডিং Effect গুলো রান করতে পারে এবং সিঙ্ক্রোনাসভাবে  may run pending effects and synchronously apply any updates they contain before returning.
* `flushSync` প্রয়োজনে কলব্যাকের ভিতরের আপডেট flush করার জন্য কলব্যাকের বাইরে আপডেট flush করতে পারে। যেমন, যদি কোন একটি ক্লিক থেকে কোন আপডেট পেন্ডীং থাকে, React কলব্যাকের ভিতরের আপডেট flush করার আগে ওগুলো flush করতে পারে।

---

## ব্যবহার {/*usage*/}

### থার্ড পার্টি ইন্টিগ্রেশনের জন্য flushing updates {/*flushing-updates-for-third-party-integrations*/}

যখন থার্ড-পার্টি কোড যেমন ব্রাউজার API বা UI লাইব্রেরির সাথে ইন্টিগ্রেট করা হয়, React কে আপডেট flush করার জন্য বাধ্য করার প্রয়োজন পড়তে পারে। কলব্যাকের মধ্যে যেকোন <CodeStep step={1}>state আপডেটের</CodeStep> synchronously flush করার জন্য React কে বাধ্য করতে `flushSync` ব্যবহার করুনঃ

```js [[1, 2, "setSomething(123)"]]
flushSync(() => {
  setSomething(123);
});
// By this line, the DOM is updated.
```

এটা নিশ্চিত করে যে, যতক্ষণে এর পরের লাইনের কোড রান হচ্ছে, ততক্ষণে যেন React DOM আপডেট করে ফেলে।

**`flushSync` এর ব্যবহার বিরল, এর নিয়মিত ব্যবহার আপনার অ্যাপের পারফরম্যান্স উল্লেখযোগ্য ভাবে কমিয়ে ফেলতে পারে।** যদি আপনার অ্যাপ কেবল মাত্র React APIs ব্যবহার করে, এবং থার্ড পার্টি লাইব্রেরির সাথে ইন্টিগ্রেট না করে, `flushSync` এর প্রয়োজন হবার কথা না।

তবে, ব্রাউজার API এর মত থার্ড পার্টি কোডের সাথে ইন্টিগ্রেশনের জন্য এটা কাজে লাগতে পারে।

কিছু ব্রাউজার API প্রত্যাশা করে কলব্যাকের মধ্যকার রেজাল্ট DOM এ সিঙ্ক্রোনাসভাবে লেখা হয়ে যাবে, কলব্যাক শেষ হবার আগেই, যাতে ব্রাউজার রেন্ডার হওয়া DOM ব্যবহার করে কিছু করতে পারে। বেশিরাভগ ক্ষেত্রে React এটা আপনার জন্য স্বয়ংক্রিয়ভাবে হ্যান্ডেল করবে। কিন্তু কিছু কিছু ক্ষেত্রে একটা সিঙ্ক্রোনাস আপডেট জোর করে করা জরুরী হতে পারে।

উদাহরণস্বরূপ, ব্রাউজার `onbeforeprint` API আপনাকে প্রিন্ট ডায়ালগ খুলার ঠিক আগ মুহুর্তে পেইজ বদলাতে দেয়। যেসব কাস্টম প্রিন্ট স্টাইল ডকুমেন্টের প্রিন্টিং সুন্দর করে ডিসপ্লে করতে দেয় সেগুলো এপ্লাই করার জন্য এটা কাজে লাগে। নিচের উদাহরণে, `onbeforeprint` কলব্যাকের মধ্যে DOM এ তৎক্ষণাৎ React state "flush" করে দেবার জন্য `flushSync` ব্যবহার করুন। তারপর, যতক্ষণে প্রিন্ট ডায়ালগ খুলছে, `isPrinting` "yes" দেখাবে।

<Sandpack>

```js App.js active
import { useState, useEffect } from 'react';
import { flushSync } from 'react-dom';

export default function PrintApp() {
  const [isPrinting, setIsPrinting] = useState(false);

  useEffect(() => {
    function handleBeforePrint() {
      flushSync(() => {
        setIsPrinting(true);
      })
    }

    function handleAfterPrint() {
      setIsPrinting(false);
    }

    window.addEventListener('beforeprint', handleBeforePrint);
    window.addEventListener('afterprint', handleAfterPrint);
    return () => {
      window.removeEventListener('beforeprint', handleBeforePrint);
      window.removeEventListener('afterprint', handleAfterPrint);
    }
  }, []);

  return (
    <>
      <h1>isPrinting: {isPrinting ? 'yes' : 'no'}</h1>
      <button onClick={() => window.print()}>
        Print
      </button>
    </>
  );
}
```

</Sandpack>

`flushSync` ব্যতীত, প্রিন্ট ডায়ালগ `isPrinting` কে "no" হিসেবে দেখাবে। এর কারণ React আপডেটগুলোকে asynchronous ভাবে ব্যাচ করে এবং state আপডেট হবার আগেই প্রিন্ট ডায়ালগ দেখা যায়।

<Pitfall>

`flushSync` পারফরম্যান্স উল্লেখযোগ্য ভাবে কমিয়ে ফেলতে পারে, এবং পেন্ডীং সাসপেন্স বাউন্ডারিগুলোকে অপ্রত্যাশিতভাবে তাদের ফলব্যাক state দেখাতে বাধ্য করতে পারে।

বেশির ভাগ সময়, `flushSync` এর ব্যবহার এড়িয়ে চলা যায়। `flushSync` কে ব্যবহার করুন last resort হিসেবে।

</Pitfall>
