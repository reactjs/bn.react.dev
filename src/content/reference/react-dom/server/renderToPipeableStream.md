---
title: renderToPipeableStream
---

<Intro>

`renderToPipeableStream` একটি React tree কে একটি pipeable [Node.js Stream.](https://nodejs.org/api/stream.html) এ রেন্ডার করে।

```js
const { pipe, abort } = renderToPipeableStream(reactNode, options?)
```

</Intro>

<InlineToc />

<Note>

এই API শুধুমাত্র Node.js এর জন্য। [Web Streams,](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) এর মতো environment, যেমন Deno এবং আধুনিক Edge রানটাইমে, [`renderToReadableStream`](/reference/react-dom/server/renderToReadableStream) ব্যবহার করা উচিত।

</Note>

---

## রেফারেন্স {/*reference*/}

### `renderToPipeableStream(reactNode, options?)` {/*rendertopipeablestream*/}

আপনার React tree কে একটি [Node.js Stream](https://nodejs.org/api/stream.html#writable-streams) এ HTML হিসেবে রেন্ডার করতে `renderToPipeableStream` কল করুন।

```js
import { renderToPipeableStream } from 'react-dom/server';

const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  }
});
```

Client-side এ, server-generated HTML কে ইন্টার‍্যাক্টিভ করতে [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) কল করুন।

[নিচে আরও উদাহরণ দেখুন।](#usage)

#### প্যারামিটারস {/*parameters*/}

* `reactNode`: একটি React node যেটিকে আপনি HTML এ রেন্ডার করতে চান। উদাহরণস্বরূপ, `<App />` এর মতো একটি JSX element । এটা এক্সপেক্টেড যে, এটি পুরো document কে ধারণ করবে, তাই `App` কম্পোনেটটির `<html>` ট্যাগ রেন্ডার করার কথা। 

* **optional** `options`: একটি object যাতে streaming options থাকবে।
  * **optional** `bootstrapScriptContent`: যদি প্রদান করা হয়, তাহলে এই string টি একটি inline `<script>` tag এ থাকবে।
  * **optional** `bootstrapScripts`: page এ emit করার জন্য `<script>` tag এর string URL গুলোর একটি array। [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) কল করে এমন `<script>` include করতে এটি ব্যবহার করুন। যদি আপনি client এ একেবারেই React রান করতে না চান তাহলে এটি বাদ দিন।
  * **optional** `bootstrapModules`: `bootstrapScripts` এর মতো, কিন্তু এর পরিবর্তে [`<script type="module">`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) emit করে।
  * **optional** `identifierPrefix`: React এ [`useId`](/reference/react/useId) দ্বারা তৈরি ID এর জন্য ব্যবহৃত একটি string prefix। একই page এ একাধিক root ব্যবহার করার সময় conflict এড়াতে কাজে আসে। [`hydrateRoot`](/reference/react-dom/client/hydrateRoot#parameters) এ পাস করা prefix এর অনুরূপ হতে হবে।
  * **optional** `namespaceURI`: stream এর জন্য root [namespace URI](https://developer.mozilla.org/en-US/docs/Web/API/Document/createElementNS#important_namespace_uris) এর একটি string । ডিফল্টভাবে এটি regular HTML । SVG এর জন্য `'http://www.w3.org/2000/svg'` অথবা MathML এর জন্য `'http://www.w3.org/1998/Math/MathML'` পাস করুন।
  * **optional** `nonce`: [`script-src` Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) এর জন্য script গুলোকে allow করতে একটি [`nonce`](http://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#nonce) string।
  * **optional** `onAllReady`: একটি callback যেটি সকল rendering কমপ্লিট হওয়ার পর ফায়ার হয়, [shell](#specifying-what-goes-into-the-shell) এবং সকল অতিরিক্ত [content](#streaming-more-content-as-it-loads) সহ। [crawler এবং static generation এর জন্য](#waiting-for-all-content-to-load-for-crawlers-and-static-generation) আপনি `onShellReady` এর পরিবর্তে এটি ব্যবহার করতে পারেন। যদি আপনি এখানে streaming শুরু করেন, তাহলে আপনি কোনো progressive loading পাবেন না। stream টিতে final HTML থাকবে।
  * **optional** `onError`: একটি callback যেটি যেকোনো server error হলে ফায়ার হয়, [recoverable](#recovering-from-errors-outside-the-shell) হোক বা [না হোক](#recovering-from-errors-inside-the-shell)। By default, এটি শুধু `console.error` কল করে। যদি আপনি [crash report log করার জন্য](#logging-crashes-on-the-server) এটি override করেন, তাহলে নিশ্চিত করুন যে আপনি তখনো `console.error` কল করতে পারছেন। এছাড়াও shell emit হওয়ার আগে [status code adjust করতে](#setting-the-status-code) এটি ব্যবহার করতে পারেন।
  * **optional** `onShellReady`: একটি callback যেটি [initial shell](#specifying-what-goes-into-the-shell) render হওয়ার ঠিক পরেই ফায়ার হয়। আপনি এখানে [status code set করতে](#setting-the-status-code) এবং streaming শুরু করতে `pipe` কল করতে পারেন। React shell এর পর [অতিরিক্ত content stream করবে](#streaming-more-content-as-it-loads) inline `<script>` tag সহ যেগুলো HTML loading fallback গুলোকে content দিয়ে replace করে।
  * **optional** `onShellError`: একটি callback যেটি initial shell render করতে error হলে ফায়ার হয়। এটি error কে argument হিসেবে receive করে। এখনো stream থেকে কোনো byte emit হয়নি, এবং `onShellReady` বা `onAllReady` কোনোটিই call হবে না, তাই আপনি [একটি fallback HTML shell output করতে পারেন](#recovering-from-errors-inside-the-shell)।
  * **optional** `progressiveChunkSize`: একটি chunk এ byte এর সংখ্যা। [default heuristic সম্পর্কে আরো পড়ুন।](https://github.com/facebook/react/blob/14c2be8dac2d5482fda8a0906a31d239df8551fc/packages/react-server/src/ReactFizzServer.js#L210-L225)


#### রিটার্নস {/*returns*/}

`renderToPipeableStream` দুইটি method ওয়ালা একটি object return করেঃ

* `pipe` প্রদত্ত [Writable Node.js Stream](https://nodejs.org/api/stream.html#writable-streams) এ HTML output করে। streaming enable করতে চাইলে `onShellReady` তে `pipe` কল করুন, অথবা crawler এবং static generation এর জন্য `onAllReady` তে কল করুন।
* `abort` আপনাকে [server rendering abort করতে](#aborting-server-rendering) এবং বাকিটা client এ render করতে দেয়।

---

## ব্যবহার {/*usage*/}

### React tree কে Node.js Stream এ HTML হিসেবে render করা {/*rendering-a-react-tree-as-html-to-a-nodejs-stream*/}

আপনার React tree কে একটি [Node.js Stream](https://nodejs.org/api/stream.html#writable-streams) এ HTML হিসেবে render করতে `renderToPipeableStream` কল করুনঃ

```js [[1, 5, "<App />"], [2, 6, "['/main.js']"]]
import { renderToPipeableStream } from 'react-dom/server';

// The route handler syntax depends on your backend framework
app.use('/', (request, response) => {
  const { pipe } = renderToPipeableStream(<App />, {
    bootstrapScripts: ['/main.js'],
    onShellReady() {
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  });
});
```

<CodeStep step={1}>root component</CodeStep> এর সাথে, আপনাকে <CodeStep step={2}>bootstrap `<script>` path গুলোর</CodeStep> একটি list প্রদান করতে হবে। আপনার root component টি যেন **root `<html>` tag সহ পুরো document return করে।**

উদাহরণস্বরূপ, এটি এরকম দেখতে হতে পারেঃ

```js [[1, 1, "App"]]
export default function App() {
  return (
    <html>
      <head>
        <meta charSet="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <link rel="stylesheet" href="/styles.css"></link>
        <title>My app</title>
      </head>
      <body>
        <Router />
      </body>
    </html>
  );
}
```

React [doctype](https://developer.mozilla.org/en-US/docs/Glossary/Doctype) এবং আপনার <CodeStep step={2}>bootstrap `<script>` tag গুলো</CodeStep> HTML stream এ inject করবেঃ

```html [[2, 5, "/main.js"]]
<!DOCTYPE html>
<html>
  <!-- ... HTML from your components ... -->
</html>
<script src="/main.js" async=""></script>
```

Client এ, আপনার bootstrap script যেন [`hydrateRoot` কল করে পুরো `document` hydrate করেঃ](/reference/react-dom/client/hydrateRoot#hydrating-an-entire-document)

```js [[1, 4, "<App />"]]
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document, <App />);
```

এটি server-generated HTML এ event listener attach করবে এবং এটিকে interactive করে তুলবে।

<DeepDive>

#### Build output থেকে CSS এবং JS asset path read করা {/*reading-css-and-js-asset-paths-from-the-build-output*/}

Final asset URL গুলো (যেমন JavaScript এবং CSS file) প্রায়ই build এর পর hash করা হয়। উদাহরণস্বরূপ, `styles.css` এর পরিবর্তে আপনি `styles.123456.css` পেতে পারেন। Static asset filename hash করা নিশ্চিত করে যে একই asset এর প্রতিটি আলাদা build এর আলাদা filename থাকবে। এটি উপকারী কারণ এটি আপনাকে static asset এর জন্য safely long-term caching enable করতে দেয়: একটি নির্দিষ্ট নামের file এর content কখনো পরিবর্তন হবে না।

তবে, build এর পর পর্যন্ত যদি আপনি asset URL গুলো না জানেন, তাহলে source code এ সেগুলো রাখার কোনো উপায় নেই। উদাহরণস্বরূপ, আগের মতো JSX এ `"/styles.css"` hardcode করা কাজ করবে না। এগুলো আপনার source code থেকে দূরে রাখতে, আপনার root component একটি prop হিসেবে পাস করা map থেকে আসল filename গুলো read করতে পারে:

```js {1,6}
export default function App({ assetMap }) {
  return (
    <html>
      <head>
        ...
        <link rel="stylesheet" href={assetMap['styles.css']}></link>
        ...
      </head>
      ...
    </html>
  );
}
```

Server এ, `<App assetMap={assetMap} />` render করুন এবং asset URL গুলো সহ আপনার `assetMap` পাস করুনঃ

```js {1-5,8,9}
// You'd need to get this JSON from your build tooling, e.g. read it from the build output.
const assetMap = {
  'styles.css': '/styles.123456.css',
  'main.js': '/main.123456.js'
};

app.use('/', (request, response) => {
  const { pipe } = renderToPipeableStream(<App assetMap={assetMap} />, {
    bootstrapScripts: [assetMap['main.js']],
    onShellReady() {
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  });
});
```

যেহেতু আপনার server এখন `<App assetMap={assetMap} />` render করছে, hydration error এড়াতে client এও `assetMap` সহ এটি render করতে হবে। আপনি এইভাবে `assetMap` serialize করে client এ পাস করতে পারেন:

```js {9-10}
// You'd need to get this JSON from your build tooling.
const assetMap = {
  'styles.css': '/styles.123456.css',
  'main.js': '/main.123456.js'
};

app.use('/', (request, response) => {
  const { pipe } = renderToPipeableStream(<App assetMap={assetMap} />, {
    // Careful: It's safe to stringify() this because this data isn't user-generated.
    bootstrapScriptContent: `window.assetMap = ${JSON.stringify(assetMap)};`,
    bootstrapScripts: [assetMap['main.js']],
    onShellReady() {
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  });
});
```

উপরের উদাহরণে, `bootstrapScriptContent` option একটি অতিরিক্ত inline `<script>` tag যোগ করে যেটি client এ global `window.assetMap` variable সেট করে। এটি client code কে একই `assetMap` read করতে দেয়:

```js {4}
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document, <App assetMap={window.assetMap} />);
```

Client এবং server উভয়েই একই `assetMap` prop সহ `App` render করে, তাই কোনো hydration error থাকবে না।

</DeepDive>

---

### Load হতে হতে আরো content stream করা {/*streaming-more-content-as-it-loads*/}

Streaming ইউজারকে সকল ডাটা সার্ভারে লোড হওয়ার আগেই কন্টেন্ট দেখতে দেয়। উদাহরণস্বরূপ, একটি profile page এর কথা ভাবুন যেটি একটি cover, friend এবং photo সহ একটি sidebar, এবং post এর একটি লিস্ট দেখায়ঃ

```js
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Sidebar>
        <Friends />
        <Photos />
      </Sidebar>
      <Posts />
    </ProfileLayout>
  );
}
```

ধরুন যে `<Posts />` এর জন্য data লোড হতে কিছু সময় লাগে। স্বাভাবিকভাবে, আপনি post এর জন্য অপেক্ষা না করে user কে profile page এর বাকি content দেখাতে চাইবেন। এটি করতে, [`Posts` কে একটি `<Suspense>` boundary তে wrap করুনঃ](/reference/react/Suspense#displaying-a-fallback-while-content-is-loading)

```js {9,11}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Sidebar>
        <Friends />
        <Photos />
      </Sidebar>
      <Suspense fallback={<PostsGlimmer />}>
        <Posts />
      </Suspense>
    </ProfileLayout>
  );
}
```

এটি React কে `Posts` এর data লোড হওয়ার আগেই HTML streaming শুরু করতে বলে। React প্রথমে loading fallback (`PostsGlimmer`) এর জন্য HTML পাঠাবে, এবং তারপর, যখন `Posts` এর data লোডিং শেষ হবে, React বাকি HTML পাঠাবে একটি inline `<script>` tag সহ যেটি লোডিং fallback কে সেই HTML দিয়ে replace করবে। ইউজারের দৃষ্টিকোণ থেকে, page প্রথমে `PostsGlimmer` নিয়ে দেখাবে, পরে `Posts` দিয়ে রিপ্লেস হবে।

আরও granular loading sequence তৈরি করতে আপনি আরও [nested `<Suspense>` boundary](/reference/react/Suspense#revealing-nested-content-as-it-loads) যোগ করতে পারেনঃ

```js {5,13}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<BigSpinner />}>
        <Sidebar>
          <Friends />
          <Photos />
        </Sidebar>
        <Suspense fallback={<PostsGlimmer />}>
          <Posts />
        </Suspense>
      </Suspense>
    </ProfileLayout>
  );
}
```

এই উদাহরণে, React আরো আগে page streaming শুরু করতে পারে। শুধুমাত্র `ProfileLayout` এবং `ProfileCover` এর প্রথমে rendering শেষ করতে হবে কারণ তারা কোনো `<Suspense>` boundary তে wrap করা নেই। তবে, যদি `Sidebar`, `Friends`, অথবা `Photos` এর কিছু data load করতে হয়, React তার পরিবর্তে `BigSpinner` fallback এর HTML পাঠাবে। তারপর, যত বেশি data লোড হবে, ততবেশি content প্রকাশিত হতে থাকবে যতক্ষণ না সবকিছু দৃশ্যমান হয়।

Streaming এর React নিজে browser এ load হওয়ার জন্য অথবা আপনার app interactive হওয়ার জন্য অপেক্ষা করার প্রয়োজন নেই। Server থেকে HTML content কোনো `<script>` tag load হওয়ার আগেই একে একে প্রকাশিত হতে থাকবে।

[HTML Streaming কিভাবে কাজ করে সে সম্পর্কে আরো পড়ুন।](https://github.com/reactwg/react-18/discussions/37)

<Note>

**শুধুমাত্র Suspense-enabled ডাটা সোর্সগুলো Suspense component কে activate করবে।** এগুলোর মধ্যে রয়েছে:

- [Relay](https://relay.dev/docs/guided-tour/rendering/loading-states/) এবং [Next.js](https://nextjs.org/docs/getting-started/react-essentials) এর মতো Suspense-enabled ফ্রেমওয়ার্ক দিয়ে ডাটা ফেচিং
- [`lazy`](/reference/react/lazy) দিয়ে কম্পোনেন্ট কোড lazy-load করা
- [`use`](/reference/react/use) দিয়ে Promise এর value read করা

যদি ডাটা একটি Effect অথবা event handler এর ভিতরে fetch করা হয় তবে Suspense তা **ডিটেক্ট করবে না**।

উপরের `Posts` component এ আপনি ঠিক কিভাবে data load করবেন তা আপনার framework এর উপর নির্ভর করে। যদি আপনি একটি Suspense-enabled framework ব্যবহার করেন, তাহলে আপনি তার data fetching documentation এ বিস্তারিত তথ্য পাবেন।

Opinionated framework ব্যবহার ছাড়া Suspense-enabled ডাটা ফেচিং এখনো সাপোর্টেড না। Suspense-enabled ডাটা সোর্স implement করার requirement গুলো unstable এবং undocumented। Suspense এর সাথে ডাটা সোর্স integrate করার জন্য একটি official API রিয়েক্টের ভবিষ্যৎ ভার্সনে রিলিজ হবে।

</Note>

---

### Shell এ কী যাবে তা নির্দিষ্ট করা {/*specifying-what-goes-into-the-shell*/}

আপনার app এর `<Suspense>` boundary এর বাইরের যেকোনো অংশকে বলা হয় *shell:*

```js {3-5,13,14}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<BigSpinner />}>
        <Sidebar>
          <Friends />
          <Photos />
        </Sidebar>
        <Suspense fallback={<PostsGlimmer />}>
          <Posts />
        </Suspense>
      </Suspense>
    </ProfileLayout>
  );
}
```

এটা ঠিক করে দেয় যে ইউজার প্রথমে কোন loading state টি দেখতে পারেঃ

```js {3-5,13
<ProfileLayout>
  <ProfileCover />
  <BigSpinner />
</ProfileLayout>
```

যদি আপনি root এ পুরো app কে একটি `<Suspense>` boundary তে wrap করেন, shell এ শুধু সেই spinner থাকবে। তবে, এটি একটি সুন্দর user experience না কারণ screen এ একটি বড় spinner দেখা আরো slow এবং বিরক্তিকর মনে হতে পারে একটু বেশি অপেক্ষা করে আসল layout দেখার চেয়ে। এই কারণেই সাধারণত আপনি `<Suspense>` boundary গুলো এমনভাবে রাখবেন যাতে shell *নুন্যতম কিন্তু সম্পূর্ণ* মনে হয়--পুরো page layout এর একটি skeleton এর মতো।

`onShellReady` callback পুরো shell render হওয়ার পর ফায়ার হয়। সাধারণত, আপনি তখনই streaming শুরু করবেনঃ

```js {3-6}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  }
});
```

`onShellReady` ফায়ার হওয়ার সময়ে, নেস্টেড `<Suspense>` boundary এর component গুলো তখনো data load করতে থাকতে পারে।

---

### Server এ crash log করা {/*logging-crashes-on-the-server*/}

স্বাভাবিকভাবে, সার্ভারের সকল error console এ log হয়। আপনি ক্র্যাশ রিপোর্ট log করতে এই আচরণ পরিবর্তন করতে পারেন:

```js {7-10}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onError(error) {
    console.error(error);
    logServerCrashReport(error);
  }
});
```

যদি আপনি একটি custom `onError` implementation প্রদান করেন, উপরের মতো console এ error log করতে ভুলবেন না।

---

### Shell এর ভিতরের error থেকে রিকভার করা {/*recovering-from-errors-inside-the-shell*/}

এই উদাহরণে, shell এ `ProfileLayout`, `ProfileCover`, এবং `PostsGlimmer` রয়েছেঃ

```js {3-5,7-8}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<PostsGlimmer />}>
        <Posts />
      </Suspense>
    </ProfileLayout>
  );
}
```

যদি এই component গুলো render করার সময় error হয়, React এর client এ পাঠানোর জন্য কোনো বোধগম্য HTML থাকবে না। শেষ অবলম্বন হিসেবে server rendering এ নির্ভর করে না এমন একটি fallback HTML পাঠাতে `onShellError` override করুনঃ

```js {7-11}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onShellError(error) {
    response.statusCode = 500;
    response.setHeader('content-type', 'text/html');
    response.send('<h1>Something went wrong</h1>'); 
  },
  onError(error) {
    console.error(error);
    logServerCrashReport(error);
  }
});
```

যদি shell generate করার সময় error হয়, `onError` এবং `onShellError` উভয়ই ফায়ার করবে। Error reporting এর জন্য `onError` ব্যবহার করুন এবং fallback HTML document পাঠানোর জন্য `onShellError` ব্যবহার করুন। আপনার fallback HTML একটি error page হতে হবে এমন না। বরং, আপনি একটি বিকল্প shell z যুক্ত করতে পারেন যেটি শুধু client এ আপনার অ্যাপ রেন্ডার করে।

---

### Shell এর বাইরের error থেকে রিকভার করা {/*recovering-from-errors-outside-the-shell*/}

এই উদাহরণে, `<Posts />` component টি `<Suspense>` এর মধ্যে রয়েছে তাই এটি shell এর *অংশ নয়*:

```js {6}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<PostsGlimmer />}>
        <Posts />
      </Suspense>
    </ProfileLayout>
  );
}
```

যদি `Posts` component বা এর ভিতরে কোথাও একটি error ঘটে, React [এর থেকে রিকভার করার চেষ্টা করবেঃ](/reference/react/Suspense#providing-a-fallback-for-server-errors-and-client-only-content)

1. এটি নিকটতম `<Suspense>` boundary (`PostsGlimmer`) এর জন্য loading fallback HTML এ emit করবে।
2. এটি আর সার্ভারে `Posts` কনটেন্ট রেন্ডার করার চেষ্টা করবে না।
3. যখন JavaScript কোড ক্লায়েন্টে লোড হয়, React ক্লায়েন্টে `Posts` রেন্ডার করার জন্য *পুনরায় চেষ্টা করবে*।

যদি `Posts` ক্লায়েন্টে রেন্ডার করার জন্য পুনরায় চেষ্টা করাও ব্যর্থ হয়, React ক্লায়েন্টে error টি throw করবে। রেন্ডারিংয়ের সময় throw হওয়া সব error এর মতো, [closest parent error boundary](/reference/react/Component#static-getderivedstatefromerror) নির্ধারণ করে কিভাবে ইউজারের কাছে error টি উপস্থাপন করতে হবে। বাস্তবে, এর মানে হল যে ইউজার একটি লোডিং ইন্ডিকেটর দেখতে পাবে যতক্ষণ না এটি নিশ্চিত হয় যে error টি recoverable না।

যদি retrying এর সময় client এ `Posts` render করা সফল হয়, সার্ভারের loading fallback টি client এ রেন্ডার করা আউটপুট দিয়ে replace হবে। User জানবে না যে server error হয়েছিল। তবে, server `onError` callback এবং client [`onRecoverableError`](/reference/react-dom/client/hydrateRoot#hydrateroot) callback গুলো ফায়ার হবে যাতে আপনি error সম্পর্কে notified হতে পারেন।

---

### Status code সেট করা {/*setting-the-status-code*/}

Streaming একটি tradeoff নিয়ে আসে। আপনি যত তাড়াতাড়ি সম্ভব পেজ streaming শুরু করতে চান যাতে ইউজার তাড়াতাড়ি কন্টেন্ট দেখতে পায়। তবে, একবার streaming শুরু করলে, আপনি আর response status code সেট করতে পারবেন না।

আপনার app কে [shell](#specifying-what-goes-into-the-shell) (সকল `<Suspense>` boundary এর উপরে) এবং বাকি content এ ভাগ করে, আপনি ইতিমধ্যে এই সমস্যার একটি অংশ সমাধান করেছেন। যদি shell এ error হয়, আপনি `onShellError` callback পাবেন যা আপনাকে error status code সেট করতে দেয়। অন্যথায়, আপনি জানেন যে app client এ recover হতে পারে, তাই আপনি "OK" পাঠাতে পারেন।

```js {4}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.statusCode = 200;
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onShellError(error) {
    response.statusCode = 500;
    response.setHeader('content-type', 'text/html');
    response.send('<h1>Something went wrong</h1>'); 
  },
  onError(error) {
    console.error(error);
    logServerCrashReport(error);
  }
});
```

If a component *outside* the shell (i.e. inside a `<Suspense>` boundary) throws an error, React will not stop rendering. This means that the `onError` callback will fire, but you will still get `onShellReady` instead of `onShellError`. This is because React will try to recover from that error on the client, [as described above.](#recovering-from-errors-outside-the-shell)

However, if you'd like, you can use the fact that something has errored to set the status code:

```js {1,6,16}
let didError = false;

const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.statusCode = didError ? 500 : 200;
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onShellError(error) {
    response.statusCode = 500;
    response.setHeader('content-type', 'text/html');
    response.send('<h1>Something went wrong</h1>'); 
  },
  onError(error) {
    didError = true;
    console.error(error);
    logServerCrashReport(error);
  }
});
```

This will only catch errors outside the shell that happened while generating the initial shell content, so it's not exhaustive. If knowing whether an error occurred for some content is critical, you can move it up into the shell.

---

### Handling different errors in different ways {/*handling-different-errors-in-different-ways*/}

You can [create your own `Error` subclasses](https://javascript.info/custom-errors) and use the [`instanceof`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof) operator to check which error is thrown. For example, you can define a custom `NotFoundError` and throw it from your component. Then your `onError`, `onShellReady`, and `onShellError` callbacks can do something different depending on the error type:

```js {2,4-14,19,24,30}
let didError = false;
let caughtError = null;

function getStatusCode() {
  if (didError) {
    if (caughtError instanceof NotFoundError) {
      return 404;
    } else {
      return 500;
    }
  } else {
    return 200;
  }
}

const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.statusCode = getStatusCode();
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onShellError(error) {
   response.statusCode = getStatusCode();
   response.setHeader('content-type', 'text/html');
   response.send('<h1>Something went wrong</h1>'); 
  },
  onError(error) {
    didError = true;
    caughtError = error;
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Keep in mind that once you emit the shell and start streaming, you can't change the status code.

---

### Waiting for all content to load for crawlers and static generation {/*waiting-for-all-content-to-load-for-crawlers-and-static-generation*/}

Streaming offers a better user experience because the user can see the content as it becomes available.

However, when a crawler visits your page, or if you're generating the pages at the build time, you might want to let all of the content load first and then produce the final HTML output instead of revealing it progressively.

You can wait for all the content to load using the `onAllReady` callback:


```js {2,7,11,18-24}
let didError = false;
let isCrawler = // ... depends on your bot detection strategy ...

const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    if (!isCrawler) {
      response.statusCode = didError ? 500 : 200;
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  },
  onShellError(error) {
    response.statusCode = 500;
    response.setHeader('content-type', 'text/html');
    response.send('<h1>Something went wrong</h1>'); 
  },
  onAllReady() {
    if (isCrawler) {
      response.statusCode = didError ? 500 : 200;
      response.setHeader('content-type', 'text/html');
      pipe(response);      
    }
  },
  onError(error) {
    didError = true;
    console.error(error);
    logServerCrashReport(error);
  }
});
```

A regular visitor will get a stream of progressively loaded content. A crawler will receive the final HTML output after all the data loads. However, this also means that the crawler will have to wait for *all* data, some of which might be slow to load or error. Depending on your app, you could choose to send the shell to the crawlers too.

---

### Aborting server rendering {/*aborting-server-rendering*/}

You can force the server rendering to "give up" after a timeout:

```js {1,5-7}
const { pipe, abort } = renderToPipeableStream(<App />, {
  // ...
});

setTimeout(() => {
  abort();
}, 10000);
```

React will flush the remaining loading fallbacks as HTML, and will attempt to render the rest on the client.
