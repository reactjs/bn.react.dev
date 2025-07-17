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

ধরুন যে `<Posts />` এর জন্য data লোড হতে কিছু সময় লাগে। স্বাভাবিকভাবে, আপনি post এর জন্য অপেক্ষা না করে user কে profile page এর বাকি content দেখাতে চাইবেন। এটি করতে, [`Posts` কে একটি `<Suspense>` boundary তে wrap করুন:](/reference/react/Suspense#displaying-a-fallback-while-content-is-loading)

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

আরও granular loading sequence তৈরি করতে আপনি আরও [nested `<Suspense>` boundary](/reference/react/Suspense#revealing-nested-content-as-it-loads) যোগ করতে পারেন:

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

In this example, React can start streaming the page even earlier. Only `ProfileLayout` and `ProfileCover` must finish rendering first because they are not wrapped in any `<Suspense>` boundary. However, if `Sidebar`, `Friends`, or `Photos` need to load some data, React will send the HTML for the `BigSpinner` fallback instead. Then, as more data becomes available, more content will continue to be revealed until all of it becomes visible.

Streaming does not need to wait for React itself to load in the browser, or for your app to become interactive. The HTML content from the server will get progressively revealed before any of the `<script>` tags load.

[Read more about how streaming HTML works.](https://github.com/reactwg/react-18/discussions/37)

<Note>

**Only Suspense-enabled data sources will activate the Suspense component.** They include:

- Data fetching with Suspense-enabled frameworks like [Relay](https://relay.dev/docs/guided-tour/rendering/loading-states/) and [Next.js](https://nextjs.org/docs/getting-started/react-essentials)
- Lazy-loading component code with [`lazy`](/reference/react/lazy)
- Reading the value of a Promise with [`use`](/reference/react/use)

Suspense **does not** detect when data is fetched inside an Effect or event handler.

The exact way you would load data in the `Posts` component above depends on your framework. If you use a Suspense-enabled framework, you'll find the details in its data fetching documentation.

Suspense-enabled data fetching without the use of an opinionated framework is not yet supported. The requirements for implementing a Suspense-enabled data source are unstable and undocumented. An official API for integrating data sources with Suspense will be released in a future version of React. 

</Note>

---

### Specifying what goes into the shell {/*specifying-what-goes-into-the-shell*/}

The part of your app outside of any `<Suspense>` boundaries is called *the shell:*

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

It determines the earliest loading state that the user may see:

```js {3-5,13
<ProfileLayout>
  <ProfileCover />
  <BigSpinner />
</ProfileLayout>
```

If you wrap the whole app into a `<Suspense>` boundary at the root, the shell will only contain that spinner. However, that's not a pleasant user experience because seeing a big spinner on the screen can feel slower and more annoying than waiting a bit more and seeing the real layout. This is why usually you'll want to place the `<Suspense>` boundaries so that the shell feels *minimal but complete*--like a skeleton of the entire page layout.

The `onShellReady` callback fires when the entire shell has been rendered. Usually, you'll start streaming then:

```js {3-6}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  }
});
```

By the time `onShellReady` fires, components in nested `<Suspense>` boundaries might still be loading data.

---

### Logging crashes on the server {/*logging-crashes-on-the-server*/}

By default, all errors on the server are logged to console. You can override this behavior to log crash reports:

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

If you provide a custom `onError` implementation, don't forget to also log errors to the console like above.

---

### Recovering from errors inside the shell {/*recovering-from-errors-inside-the-shell*/}

In this example, the shell contains `ProfileLayout`, `ProfileCover`, and `PostsGlimmer`:

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

If an error occurs while rendering those components, React won't have any meaningful HTML to send to the client. Override `onShellError` to send a fallback HTML that doesn't rely on server rendering as the last resort:

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

If there is an error while generating the shell, both `onError` and `onShellError` will fire. Use `onError` for error reporting and use `onShellError` to send the fallback HTML document. Your fallback HTML does not have to be an error page. Instead, you may include an alternative shell that renders your app on the client only.

---

### Recovering from errors outside the shell {/*recovering-from-errors-outside-the-shell*/}

In this example, the `<Posts />` component is wrapped in `<Suspense>` so it is *not* a part of the shell:

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

If an error happens in the `Posts` component or somewhere inside it, React will [try to recover from it:](/reference/react/Suspense#providing-a-fallback-for-server-errors-and-client-only-content)

1. It will emit the loading fallback for the closest `<Suspense>` boundary (`PostsGlimmer`) into the HTML.
2. It will "give up" on trying to render the `Posts` content on the server anymore.
3. When the JavaScript code loads on the client, React will *retry* rendering `Posts` on the client.

If retrying rendering `Posts` on the client *also* fails, React will throw the error on the client. As with all the errors thrown during rendering, the [closest parent error boundary](/reference/react/Component#static-getderivedstatefromerror) determines how to present the error to the user. In practice, this means that the user will see a loading indicator until it is certain that the error is not recoverable.

If retrying rendering `Posts` on the client succeeds, the loading fallback from the server will be replaced with the client rendering output. The user will not know that there was a server error. However, the server `onError` callback and the client [`onRecoverableError`](/reference/react-dom/client/hydrateRoot#hydrateroot) callbacks will fire so that you can get notified about the error.

---

### Setting the status code {/*setting-the-status-code*/}

Streaming introduces a tradeoff. You want to start streaming the page as early as possible so that the user can see the content sooner. However, once you start streaming, you can no longer set the response status code.

By [dividing your app](#specifying-what-goes-into-the-shell) into the shell (above all `<Suspense>` boundaries) and the rest of the content, you've already solved a part of this problem. If the shell errors, you'll get the `onShellError` callback which lets you set the error status code. Otherwise, you know that the app may recover on the client, so you can send "OK".

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
