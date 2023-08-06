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
* `flushSync` may flush updates outside the callback when necessary to flush the updates inside the callback. For example, if there are pending updates from a click, React may flush those before flushing the updates inside the callback.

---

## Usage {/*usage*/}

### Flushing updates for third-party integrations {/*flushing-updates-for-third-party-integrations*/}

When integrating with third-party code such as browser APIs or UI libraries, it may be necessary to force React to flush updates. Use `flushSync` to force React to flush any <CodeStep step={1}>state updates</CodeStep> inside the callback synchronously:

```js [[1, 2, "setSomething(123)"]]
flushSync(() => {
  setSomething(123);
});
// By this line, the DOM is updated.
```

This ensures that, by the time the next line of code runs, React has already updated the DOM.

**Using `flushSync` is uncommon, and using it often can significantly hurt the performance of your app.** If your app only uses React APIs, and does not integrate with third-party libraries, `flushSync` should be unnecessary.

However, it can be helpful for integrating with third-party code like browser APIs.

Some browser APIs expect results inside of callbacks to be written to the DOM synchronously, by the end of the callback, so the browser can do something with the rendered DOM. In most cases, React handles this for you automatically. But in some cases it may be necessary to force a synchronous update.

For example, the browser `onbeforeprint` API allows you to change the page immediately before the print dialog opens. This is useful for applying custom print styles that allow the document to display better for printing. In the example below, you use `flushSync` inside of the `onbeforeprint` callback to immediately "flush" the React state to the DOM. Then, by the time the print dialog opens, `isPrinting` displays "yes":

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

Without `flushSync`, when the print dialog will display `isPrinting` as "no". This is because React batches the updates asynchronously and the print dialog is displayed before the state is updated.

<Pitfall>

`flushSync` can significantly hurt performance, and may unexpectedly force pending Suspense boundaries to show their fallback state.

Most of the time, `flushSync` can be avoided, so use `flushSync` as a last resort.

</Pitfall>
