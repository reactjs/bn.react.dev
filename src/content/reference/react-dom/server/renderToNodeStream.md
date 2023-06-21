---
title: renderToNodeStream
---

<Deprecated>

এই API ভবিষ্যতে React এর বড় কোন আপডেতে ফেলে দেওয়া হবে। এর বদলে  [`renderToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) ব্যবহার করুন।

</Deprecated>

<Intro>

`renderToNodeStream` একটি React ট্রিকে একটি [Node.js Readable Stream](https://nodejs.org/api/stream.html#readable-streams) এ রেন্ডার করে।

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

[See more examples below.](#usage)

#### Parameters {/*parameters*/}

* `reactNode`: A React node you want to render to HTML. For example, a JSX element like `<App />`.

#### Returns {/*returns*/}

A [Node.js Readable Stream](https://nodejs.org/api/stream.html#readable-streams) that outputs an HTML string.

#### সতর্কতা {/*caveats*/}

* এই মেথডটি কোন আউটপুট রিটার্ন করবার আগে সকল [Suspense boundary](/reference/react/Suspense) এর সম্পূর্ণ হবার জন্য অপেক্ষা করে।

* React 18 নাগাদ, এই মেথডটি এর সকল আউটপুট বাফার করে, তার এটা আসলে streaming এর সুবিধা দিতে পারে না। এ কারণেই, এর বদলে  [`renderToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) ব্যবহার করার জন্য আপনাকে পরামর্শ দেওয়া হচ্ছে।

* The returned stream is a byte stream encoded in utf-8. If you need a stream in another encoding, take a look at a project like [iconv-lite](https://www.npmjs.com/package/iconv-lite), which provides transform streams for transcoding text.

---

## Usage {/*usage*/}

### Rendering a React tree as HTML to a Node.js Readable Stream {/*rendering-a-react-tree-as-html-to-a-nodejs-readable-stream*/}

Call `renderToNodeStream` to get a [Node.js Readable Stream](https://nodejs.org/api/stream.html#readable-streams) which you can pipe to your server response:

```js {5-6}
import { renderToNodeStream } from 'react-dom/server';

// The route handler syntax depends on your backend framework
app.use('/', (request, response) => {
  const stream = renderToNodeStream(<App />);
  stream.pipe(response);
});
```

The stream will produce the initial non-interactive HTML output of your React components. On the client, you will need to call [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) to *hydrate* that server-generated HTML and make it interactive.
