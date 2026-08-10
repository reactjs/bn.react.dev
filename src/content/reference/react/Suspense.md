---
title: <Suspense>
---

<Intro>

`<Suspense>` তার children এর লোডিং শেষ না হওয়া পর্যন্ত একটি fallback প্রদর্শন করে। 


```js
<Suspense fallback={<Loading />}>
  <SomeComponent />
</Suspense>
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `<Suspense>` {/*suspense*/}

<<<<<<< HEAD
#### প্রপ্স {/*props*/}
* `children`: আপনি যেই প্রকৃত UI রেন্ডার করতে চাইছেন। `children` যদি রেন্ডারিং এর সময় থেমে যায় তাহলে Suspense boundary `fallback` রেন্ডার করবে।
* `fallback`: এটি হচ্ছে, প্রকৃত UI পুরোপুরি লোড না হলে তার স্থানে রেন্ডার হওয়া একটি বিকল্প UI। যেকোনো বৈধ React নোডই গৃহীত হয়, যদিও কার্যত, একটি fallback হলো একটি হালকা placeholder view, যেমন একটি লোডিং স্পিনার বা স্কেলেটন। Suspense স্বয়ংক্রিয় ভাবে `fallback` এ পরিবর্তিত হয়ে যাবে যখন `children` থেমে যাবে এবং আবার `children` এ ফিরে আসবে যখন ডেটা তৈরি হয়ে যাবে। রেন্ডারিং এর সময় যদি `fallback` থেমে যায়, এটি তার নিকটতম প্যারেন্ট Suspense boundary সক্রিয় করবে। 
=======
#### Props {/*props*/}
* `children`: The actual UI you intend to render. If `children` suspends while rendering, the Suspense boundary will switch to rendering `fallback`.
* `fallback`: An alternate UI to render in place of the actual UI if it has not finished loading. Any valid React node is accepted, though in practice, a fallback is a lightweight placeholder view, such as a loading spinner or skeleton. Suspense will automatically switch to `fallback` when `children` suspends, and back to `children` when the data is ready. If `fallback` suspends while rendering, it will activate the closest parent Suspense boundary.
* <ExperimentalBadge /> **optional** `defer`: A boolean. When `true`, React may show the `fallback` first and render or stream `children` later, even when nothing in them suspends. Use it for content that is expensive to render. Defaults to `false`.
>>>>>>> c7d6b700038c63d1aaf2c649af1aefe01ebbacac

#### সতর্কতা {/*caveats*/}

<<<<<<< HEAD
- React প্রথমবার মাউন্ট হওয়ার আগে থেমে যাওয়া রেন্ডারগুলোর জন্য কোনও state সংরক্ষণ করে না। কম্পোনেন্ট লোড হলে, React থেমে যাওয়া ট্রি পুনরায় শূন্য থেকে রেন্ডার করার চেষ্টা করবে।
- Suspense যদি ট্রির জন্য কন্টেন্ট দেখাতে থাকে, কিন্তু পরে আবার থেমে যায়, তবে `fallback` আবার দেখানো হবে যদিনা এর আপডেটের কারণ [`startTransition`](/reference/react/startTransition) বা [`useDeferredValue`](/reference/react/useDeferredValue) দ্বারা সৃষ্টি হয়ে থাকে।  
- যদি React পুনরায় থেমে যাওয়ার কারণে আগে দেখানো কন্টেন্ট লুকানোর প্রয়োজন হয়, তবে এটি কন্টেন্ট ট্রির [layout Effects](/reference/react/useLayoutEffect) কে পরিষ্কার করবে। কন্টেন্ট যখন আবার দেখানোর জন্য প্রস্তুত হয়ে যায়, রিয়েক্ট আবার layout Effects চালাবে। এটি নিশ্চিত করে যে, DOM layout পরিমাপ করা ইফেক্টগুলো কন্টেন্ট লুকায়িত অবস্থায় এটা করার চেষ্টা করে না। 
- React এ রয়েছে *Streaming Server Rendering* এবং *Selective Hydration* এর মত অন্তর্নিহিত অপটিমাইজেশন যা Suspense এর সাথে সমন্বিত। আরো জানার জন্য পড়ুন [একটি গঠনমূলক সারমর্ম](https://github.com/reactwg/react-18/discussions/37) এবং দেখুন [একটি টেকনিকাল আলোচনা](https://www.youtube.com/watch?v=pj5N-Khihgc)

---

## ব্যবহারবিধি {/*usage*/}
=======
- Suspense does not detect when data is fetched inside an Effect or event handler. It only activates in the [cases listed below.](#what-activates-a-suspense-boundary)
- React does not preserve any state for renders that got suspended before they were able to mount for the first time. When the component has loaded, React will retry rendering the suspended tree from scratch.
- If Suspense was displaying content for the tree, but then it suspended again, the `fallback` will be shown again unless the update causing it was caused by [`startTransition`](/reference/react/startTransition) or [`useDeferredValue`](/reference/react/useDeferredValue).
- React reveals suspended content at most once every 300ms, measured from the last reveal. Boundaries that become ready within that window are [revealed together](/blog/2025/10/01/react-19-2#batching-suspense-boundaries-for-ssr) rather than one at a time.
- If React needs to hide the already visible content because it suspended again, it will clean up [layout Effects](/reference/react/useLayoutEffect) in the content tree. When the content is ready to be shown again, React will fire the layout Effects again. This ensures that Effects measuring the DOM layout don't try to do this while the content is hidden.
- React includes under-the-hood optimizations like *Streaming Server Rendering* and *Selective Hydration* that are integrated with Suspense. Read [an architectural overview](https://github.com/reactwg/react-18/discussions/37) and watch [a technical talk](https://www.youtube.com/watch?v=pj5N-Khihgc) to learn more.

---

### What activates a Suspense boundary {/*what-activates-a-suspense-boundary*/}

A Suspense boundary waits for its content to be ready before revealing it. Any of the following keeps a boundary from revealing its content:

- Lazy-loading component code with [`lazy`](/reference/react/lazy).
- Reading a Promise with [`use`](/reference/react/use), including data streamed from [Server Components](/reference/rsc/server-components) or loaded through a [Suspense-enabled framework](#suspense-enabled-frameworks).
- Loading a stylesheet rendered with [`<link rel="stylesheet">` and a `precedence` prop.](/reference/react-dom/components/link#special-rendering-behavior) React blocks the boundary until the stylesheet loads, up to a timeout. [See an example below.](#waiting-for-a-stylesheet-to-load)
- Waiting for a large boundary's HTML to arrive during streaming server rendering. Sending HTML takes time, so a boundary with enough content activates even when nothing in it suspends. React reveals the content as the HTML arrives.
- <CanaryBadge /> Loading fonts. Suspense doesn't wait for fonts by default, but a [`<ViewTransition>`](/reference/react/ViewTransition) update waits for new fonts to load, up to a timeout, so text doesn't flash with a fallback font. [See an example below.](#waiting-for-a-font-to-load)
- <CanaryBadge /> Loading images. Suspense doesn't wait for images by default, but during a [`<ViewTransition>`](/reference/react/ViewTransition) update, React blocks the boundary until the image loads, up to a timeout. Adding an `onLoad` handler opts a specific image out. [See an example below.](#waiting-for-an-image-to-load)
- <ExperimentalBadge /> Performing CPU-bound render work inside a [`<Suspense defer>`](#props) boundary.

<Note>

#### Suspense-enabled frameworks {/*suspense-enabled-frameworks*/}

A *Suspense-enabled framework* gives you a way to read data in your component in a way that activates the closest Suspense boundary. The exact way you load your data depends on your framework, and you'll find the details in its documentation. Under the hood, a Suspense-enabled framework maintains a cache of Promises and calls [`use`](/reference/react/use) to suspend on a Promise.

Without a framework, you can read a Promise with `use` directly, as long as the Promise is [cached so the same instance is reused across renders.](/reference/react/use#caching-promises-for-client-components)

</Note>

---

## Usage {/*usage*/}
>>>>>>> c7d6b700038c63d1aaf2c649af1aefe01ebbacac

### কন্টেন্ট লোড হওয়ার সময় একটি fallback দেখানো {/*displaying-a-fallback-while-content-is-loading*/}

আপনি আপনার অ্যাপ্লিকেশনের যে কোন অংশকে একটি Suspense boundary দিয়ে আবদ্ধ করতে পারেনঃ

```js [[1, 1, "<Loading />"], [2, 2, "<Albums />"]]
<Suspense fallback={<Loading />}>
  <Albums />
</Suspense>
```

<CodeStep step={2}>Children</CodeStep> এর প্রয়োজনীয় সমস্ত কোড এবং ডেটা লোড না হওয়া পর্যন্ত React আপনার <CodeStep step={1}>লোডিং fallback</CodeStep> দেখাবে।

নিচের উদাহরণে, `Albums` কম্পোনেন্ট একটি অ্যালবাম তালিকা নিয়ে আসার সময় *থেমে* যায়। রেন্ডারের জন্য প্রস্তুত হওয়া পর্যন্ত, React উপরের সবচেয়ে কাছের Suspense boundary ব্যবহার করে fallback হিসেবে আপনার লোডিং কম্পোনেন্ত দেখায়। তারপরে, ডেটা লোড হলে, React লোডিং fallback লুকিয়ে দেয় এবং ডেটা সহ `Albums` কম্পোনেন্টটি রেন্ডার করে।

<Sandpack>

```js src/App.js hidden
import { useState } from 'react';
import ArtistPage from './ArtistPage.js';

export default function App() {
  const [show, setShow] = useState(false);
  if (show) {
    return (
      <ArtistPage
        artist={{
          id: 'the-beatles',
          name: 'The Beatles',
        }}
      />
    );
  } else {
    return (
      <button onClick={() => setShow(true)}>
        Open The Beatles artist page
      </button>
    );
  }
}
```

```js src/ArtistPage.js active
import { Suspense } from 'react';
import Albums from './Albums.js';

export default function ArtistPage({ artist }) {
  return (
    <>
      <h1>{artist.name}</h1>
      <Suspense fallback={<Loading />}>
        <Albums artistId={artist.id} />
      </Suspense>
    </>
  );
}

function Loading() {
  return <h2>🌀 Loading...</h2>;
}
```

```js src/Albums.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Albums({ artistId }) {
  const albums = use(fetchData(`/${artistId}/albums`));
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url === '/the-beatles/albums') {
    return await getAlbums();
  } else {
    throw Error('Not implemented');
  }
}

async function getAlbums() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 3000);
  });

  return [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];
}
```

</Sandpack>

By contrast, code that fetches data outside of `use`, such as inside an Effect, does not activate the boundary:

<<<<<<< HEAD
**শুধুমাত্র Suspense-enabled ডেটা উৎসগুলো Suspense কম্পোনেন্টকে সক্রিয় করতে পারবে।** এদের মধ্যে রয়েছেঃ
 
- Suspense-enabled ফ্রেমওয়ার্কের সাহায্যে ডেটা নিয়ে আসা যেমন, [Relay](https://relay.dev/docs/guided-tour/rendering/loading-states/) এবং [Next.js](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming#streaming-with-suspense)
- [`lazy`](/reference/react/lazy) দিয়ে lazy-loading component code
- [`use`](/reference/react/use) ব্যবহার করে একটি cached Promise এর মান read করা

উপরের `Albums` কম্পোনেন্টে আপনি ঠিক কিভাবে ডেটা লোড করবেন তা আপনার ফ্রেমওয়ার্ক এর উপর নির্ভর করে। আপনি যদি Suspense-enabled ফ্রেমওয়ার্ক ব্যাবহার করেন তাহলে আপনি এর ডেটা নিয়ে আসার ডকুমেন্টেশনে বিস্তারিত তথ্য পাবেন। 

মতানুযায়ী নির্ধারিত ফ্রেমওয়ার্ক ছাড়া Suspense-enabled ডেটা নিয়ে আসা এখনো সমর্থিত নয়। Suspense-enabled ডেটা উৎস সংযোজনের জন্য প্রয়োজনীয় বিবরণগুলো অস্থিতিশীল এবং নথিভুক্ত নয়। Suspense দিয়ে ডেটা উৎসগুলোর সংযোজনের জন্য একটি অফিসিয়াল API পরবর্তী React সংস্করণে প্রকাশিত হবে। 
=======
<Sandpack>

```js src/App.js hidden
import { useState } from 'react';
import ArtistPage from './ArtistPage.js';

export default function App() {
  const [show, setShow] = useState(false);
  if (show) {
    return (
      <ArtistPage
        artist={{
          id: 'the-beatles',
          name: 'The Beatles',
        }}
      />
    );
  } else {
    return (
      <button onClick={() => setShow(true)}>
        Open The Beatles artist page
      </button>
    );
  }
}
```

```js src/ArtistPage.js active
import { Suspense } from 'react';
import EffectAlbums from './EffectAlbums.js';

export default function ArtistPage({ artist }) {
  return (
    <>
      <h1>{artist.name}</h1>
      <Suspense fallback={<Loading />}>
        <EffectAlbums artistId={artist.id} />
      </Suspense>
    </>
  );
}
>>>>>>> c7d6b700038c63d1aaf2c649af1aefe01ebbacac

function Loading() {
  return <h2>🌀 Loading...</h2>;
}
```

```js src/EffectAlbums.js
import { useState, useEffect } from 'react';
import { fetchData } from './data.js';

export default function EffectAlbums({ artistId }) {
  const [albums, setAlbums] = useState([]);

  useEffect(() => {
    let active = true;
    fetchData(`/${artistId}/albums`).then(result => {
      if (active) {
        setAlbums(result);
      }
    });
    return () => {
      active = false;
    };
  }, [artistId]);

  // Suspense can't see this fetch, so its fallback never
  // shows. The list stays empty until the data arrives.
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url === '/the-beatles/albums') {
    return await getAlbums();
  } else {
    throw Error('Not implemented');
  }
}

async function getAlbums() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 3000);
  });

  return [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];
}
```

</Sandpack>

During streaming server rendering, a boundary also activates while its HTML is still streaming in. With any streaming server rendering API, React sends [the shell](/reference/react-dom/server/renderToPipeableStream#specifying-what-goes-into-the-shell) with the `fallback` first, then streams in each boundary's HTML and swaps out its `fallback` as that content arrives. Press "Render the page" to watch the page stream in:

<Sandpack>

```js src/App.js hidden
```

```html public/index.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Streaming SSR</title>
</head>
<body>
  <button id="render">Render the page</button>
  <br /><br />
  <iframe id="container" style="width: 100%; height: 180px; border: 1px solid #aaa;"></iframe>
</body>
</html>
```

```js src/index.js
import { flushReadableStreamToFrame } from './demo-helpers.js';
import { Suspense, use } from 'react';
import { renderToReadableStream } from 'react-dom/server';

let posts = null;

function Posts() {
  const text = use(posts.promise);
  return <p>{text}</p>;
}

function ProfilePage() {
  return (
    <html>
      <body>
        <h1>Alice</h1>
        <p>Photographer and traveler.</p>
        <Suspense fallback={<p>⌛ Loading posts...</p>}>
          <Posts />
        </Suspense>
      </body>
    </html>
  );
}

async function main(frame) {
  posts = Promise.withResolvers();
  const stream = await renderToReadableStream(<ProfilePage />);

  // The posts resolve after the shell has streamed, so React
  // streams their HTML in and swaps out the fallback.
  setTimeout(() => {
    posts.resolve(
      'Just got back from two weeks along the coast. The drive ' +
      'was longer than expected, but every stop was worth it. ' +
      'A full write-up and more photos are coming soon.'
    );
  }, 1500);

  await flushReadableStreamToFrame(stream, frame);
}

document.getElementById('render').addEventListener('click', () => {
  main(document.getElementById('container'));
});
```

```js src/demo-helpers.js hidden
export async function flushReadableStreamToFrame(readable, frame) {
  const doc = frame.contentWindow.document;
  const decoder = new TextDecoder();
  for await (const chunk of readable) {
    doc.write(decoder.decode(chunk, { stream: true }));
  }
  doc.close();
}
```

</Sandpack>

---

### সামগ্রিকভাবে একসাথে কন্টেন্ট প্রকাশ করা {/*revealing-content-together-at-once*/}

স্বাভাবিকভাবে, Suspense এর ভিতরে অবস্থিত সম্পূর্ণ ট্রি একটি একক ইউনিট হিসাবে বিবেচিত হয়। উদাহরণস্বরূপ, যদিও এই কম্পোনেন্টগুলোর মধ্যে *কেবল একটিই* কিছু ডেটার জন্য অপেক্ষায় থাকে, *সবগুলো* কম্পোনেন্ট একসাথে লোডিং ইন্ডিকেটর দ্বারা প্রতিস্থাপিত হবেঃ

```js {2-5}
<Suspense fallback={<Loading />}>
  <Biography />
  <Panel>
    <Albums />
  </Panel>
</Suspense>
```

তারপরে, সবগুলো দেখনোর জন্য প্রস্তুত হয়ে গেলে, তাদের সবাইকে একসাথে দেখা যাবে। 

নিচের উদাহরণে, `Biography` এবং `Albums` উভয়ই কিছু ডেটা নিয়ে আসে। তবে, তারা একটি একক Suspense boundary এর মধ্যে গ্রুপ করে থাকায় সবসময় এই কম্পোনেন্টগুলি একই সময়ে "পপ ইন" করে। 

<Sandpack>

```js src/App.js hidden
import { useState } from 'react';
import ArtistPage from './ArtistPage.js';

export default function App() {
  const [show, setShow] = useState(false);
  if (show) {
    return (
      <ArtistPage
        artist={{
          id: 'the-beatles',
          name: 'The Beatles',
        }}
      />
    );
  } else {
    return (
      <button onClick={() => setShow(true)}>
        Open The Beatles artist page
      </button>
    );
  }
}
```

```js src/ArtistPage.js active
import { Suspense } from 'react';
import Albums from './Albums.js';
import Biography from './Biography.js';
import Panel from './Panel.js';

export default function ArtistPage({ artist }) {
  return (
    <>
      <h1>{artist.name}</h1>
      <Suspense fallback={<Loading />}>
        <Biography artistId={artist.id} />
        <Panel>
          <Albums artistId={artist.id} />
        </Panel>
      </Suspense>
    </>
  );
}

function Loading() {
  return <h2>🌀 Loading...</h2>;
}
```

```js src/Panel.js
export default function Panel({ children }) {
  return (
    <section className="panel">
      {children}
    </section>
  );
}
```

```js src/Biography.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Biography({ artistId }) {
  const bio = use(fetchData(`/${artistId}/bio`));
  return (
    <section>
      <p className="bio">{bio}</p>
    </section>
  );
}
```

```js src/Albums.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Albums({ artistId }) {
  const albums = use(fetchData(`/${artistId}/albums`));
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url === '/the-beatles/albums') {
    return await getAlbums();
  } else if (url === '/the-beatles/bio') {
    return await getBio();
  } else {
    throw Error('Not implemented');
  }
}

async function getBio() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 1500);
  });

  return `The Beatles were an English rock band,
    formed in Liverpool in 1960, that comprised
    John Lennon, Paul McCartney, George Harrison
    and Ringo Starr.`;
}

async function getAlbums() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 3000);
  });

  return [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];
}
```

```css
.bio { font-style: italic; }

.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}
```

</Sandpack>

যে কম্পোনেন্টগুলো ডেটা লোড করে তাদেরকে Suspense boundary এর direct children হতে হবে এমনটি নয়। উদাহরণস্বরূপ, আপনি `Biography` এবং `Albums` কে একটি নতুন `Details` কম্পোনেন্টে সরিয়ে নিতে পারেন। এটা আচরণের পরিবর্তন করে না। `Biography` এবং `Albums` এর সবচেয়ে কাছের প্যারেন্ট Suspense boundary একই, তাই তাদের প্রকাশ এক সঙ্গে সমন্বিতভাবে হয়।

```js {2,8-11}
<Suspense fallback={<Loading />}>
  <Details artistId={artist.id} />
</Suspense>

function Details({ artistId }) {
  return (
    <>
      <Biography artistId={artistId} />
      <Panel>
        <Albums artistId={artistId} />
      </Panel>
    </>
  );
}
```

---

### নেস্টেড কন্টেন্ট লোড হওয়ার সাথে সাথে প্রকাশ করা {/*revealing-nested-content-as-it-loads*/}

যখন একটি কম্পোনেন্ট থেমে যায়, তখন সবচেয়ে কাছের প্যারেন্ট Suspense কম্পোনেন্ট fallback দেখায়। এর মাধ্যমে আপনি একাধিক Suspense কম্পোনেন্ট দ্বারা একটি লোডিং ক্রম তৈরি করতে পারেন। প্রতিটি Suspense boundary এর fallback পূর্ণ হবে যদি পরবর্তী স্তরের কন্টেন্ট থাকে। উদাহরণস্বরূপ, আপনি অ্যালবাম তালিকায় এর নিজস্ব fallback দিতে পারেনঃ

```js {3,7}
<Suspense fallback={<BigSpinner />}>
  <Biography />
  <Suspense fallback={<AlbumsGlimmer />}>
    <Panel>
      <Albums />
    </Panel>
  </Suspense>
</Suspense>
```

এই পরিবর্তনের ফলে `Biography` প্রদর্শনে `Albums` এর লোডের জন্য অপেক্ষা করার প্রয়োজন নেই। 

ক্রমটি হবে এরকমঃ

১। যদি `Biography` এখনো লোড না হয়ে থাকে, কন্টেন্ট এর সম্পূর্ণ জায়গাজুড়ে `BigSpinner` দেখানো হয়। 
২। `Biography` লোড করা শেষ হলে `BigSpinner` দ্বারা কন্টেন্ট প্রতিস্থাপিত হয়।
৩। যদি এখনো `Albums` লোড না হয়ে থাকে, `AlbumsGlimmer` এর স্থানে `Albums` এবং এর প্যারেন্ট `Panel` দেখানো হয়। 
৪। সব শেষে, `Albums` এর লোডিং শেষ হলে এটি `AlbumsGlimmer` কে প্রতিস্থাপন করে। 

<Sandpack>

```js src/App.js hidden
import { useState } from 'react';
import ArtistPage from './ArtistPage.js';

export default function App() {
  const [show, setShow] = useState(false);
  if (show) {
    return (
      <ArtistPage
        artist={{
          id: 'the-beatles',
          name: 'The Beatles',
        }}
      />
    );
  } else {
    return (
      <button onClick={() => setShow(true)}>
        Open The Beatles artist page
      </button>
    );
  }
}
```

```js src/ArtistPage.js active
import { Suspense } from 'react';
import Albums from './Albums.js';
import Biography from './Biography.js';
import Panel from './Panel.js';

export default function ArtistPage({ artist }) {
  return (
    <>
      <h1>{artist.name}</h1>
      <Suspense fallback={<BigSpinner />}>
        <Biography artistId={artist.id} />
        <Suspense fallback={<AlbumsGlimmer />}>
          <Panel>
            <Albums artistId={artist.id} />
          </Panel>
        </Suspense>
      </Suspense>
    </>
  );
}

function BigSpinner() {
  return <h2>🌀 Loading...</h2>;
}

function AlbumsGlimmer() {
  return (
    <div className="glimmer-panel">
      <div className="glimmer-line" />
      <div className="glimmer-line" />
      <div className="glimmer-line" />
    </div>
  );
}
```

```js src/Panel.js
export default function Panel({ children }) {
  return (
    <section className="panel">
      {children}
    </section>
  );
}
```

```js src/Biography.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Biography({ artistId }) {
  const bio = use(fetchData(`/${artistId}/bio`));
  return (
    <section>
      <p className="bio">{bio}</p>
    </section>
  );
}
```

```js src/Albums.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Albums({ artistId }) {
  const albums = use(fetchData(`/${artistId}/albums`));
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url === '/the-beatles/albums') {
    return await getAlbums();
  } else if (url === '/the-beatles/bio') {
    return await getBio();
  } else {
    throw Error('Not implemented');
  }
}

async function getBio() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 500);
  });

  return `The Beatles were an English rock band,
    formed in Liverpool in 1960, that comprised
    John Lennon, Paul McCartney, George Harrison
    and Ringo Starr.`;
}

async function getAlbums() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 3000);
  });

  return [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];
}
```

```css
.bio { font-style: italic; }

.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}

.glimmer-panel {
  border: 1px dashed #aaa;
  background: linear-gradient(90deg, rgba(221,221,221,1) 0%, rgba(255,255,255,1) 100%);
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}

.glimmer-line {
  display: block;
  width: 60%;
  height: 20px;
  margin: 10px;
  border-radius: 4px;
  background: #f0f0f0;
}
```

</Sandpack>

Suspense boundary এর মাধ্যমে আপনি আপনার UI এর কোন অংশগুলো সবসময় একই সময়ে "পপ ইন" করা হবে এবং কোন অংশগুলি লোডিং state এর একটি অনুক্রমিক সিকোয়েন্সে আরো কন্টেন্ট প্রকাশ করবে তা সমন্বিত করতে পারেন। আপনি ট্রির যে কোন জায়গায় Suspense boundaries যোগ করতে পারেন, সরাতে পারেন বা মুছতে পারেন কিন্তু অ্যাপ্লিকেশনের অন্যান্য অংশের আচরণের উপর এর কোন প্রভাব পড়বে না। 

প্রত্যেকটি কম্পোনেন্টের সাথে একটি করে Suspense boundary যোগ করবেন না। Suspense boundary গুলো লোডিং ক্রমের চেয়ে ক্ষুদ্র হওয়া উচিৎ নয় যা ব্যহারকারীকে দেখাবেন। যদি আপনি একজন ডিজাইনার সঙ্গে কাজ করেন, তবে তাদেরকে জিজ্ঞাসা করুন যে লোডিং state গুলো কোথায় রাখা উচিত--সম্ভবতঃ তারা আগেই তা তাদের ডিজাইন ওয়্যারফ্রেমে অন্তর্ভুক্ত করেছেন।

---

### নতুন কনটেন্ট লোড হতে হতে পুরাতন কন্টেন্ট দেখানো {/*showing-stale-content-while-fresh-content-is-loading*/}

এই উদাহরণে `SearchResults` কম্পোনেন্টটি সার্চের ফলাফল নিয়ে আসার সময় থেমে যায়। `"a"` টাইপ করে অপেক্ষা করুন, তারপর এটিকে এডিট করে `"ab"` করুন। `"a"` এর ফলাফলগুলো লোডিং fallback দ্বারা প্রতিস্থাপিত হয়ে যাবে।

<Sandpack>

```js src/App.js
import { Suspense, useState } from 'react';
import SearchResults from './SearchResults.js';

export default function App() {
  const [query, setQuery] = useState('');
  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={e => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
        <SearchResults query={query} />
      </Suspense>
    </>
  );
}
```

```js src/SearchResults.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function SearchResults({ query }) {
  if (query === '') {
    return null;
  }
  const albums = use(fetchData(`/search?q=${query}`));
  if (albums.length === 0) {
    return <p>No matches for <i>"{query}"</i></p>;
  }
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url.startsWith('/search?q=')) {
    return await getSearchResults(url.slice('/search?q='.length));
  } else {
    throw Error('Not implemented');
  }
}

async function getSearchResults(query) {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 500);
  });

  const allAlbums = [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];

  const lowerQuery = query.trim().toLowerCase();
  return allAlbums.filter(album => {
    const lowerTitle = album.title.toLowerCase();
    return (
      lowerTitle.startsWith(lowerQuery) ||
      lowerTitle.indexOf(' ' + lowerQuery) !== -1
    )
  });
}
```

```css
input { margin: 10px; }
```

</Sandpack>

<<<<<<< HEAD
একটি প্রচলিত বিকল্প UI প্যাটার্ন হলো তালিকা আপডেটকে *বিলম্বিত করা(defer)* এবং নতুন ফলাফল তৈরি হওয়া পর্যন্ত আগের ফলাফলগুলো দেখানো। [`useDeferredValue`](/reference/react/useDeferredValue) হুকটি আপনাকে query করার একটি deferred ভার্সন পাঠানোর সুবিধা দেয়ঃ
=======
A common alternative UI pattern is to *defer* updating the list and to keep showing the previous results until the new results are ready. The [`useDeferredValue`](/reference/react/useDeferredValue) Hook lets you pass a deferred version of the query down:
>>>>>>> c7d6b700038c63d1aaf2c649af1aefe01ebbacac

```js {3,11}
export default function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);
  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={e => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
        <SearchResults query={deferredQuery} />
      </Suspense>
    </>
  );
}
```

`query` টি তাৎক্ষণিকভাবে আপডেট হবে, তাই ইনপুটটি নতুন মানটি দেখাবে। তবে, `deferredQuery` ডেটা লোড না হওয়া পর্যন্ত তার আগের মানটি রেখে দিবে, তাই `SearchResults` কিছুক্ষণের জন্য পুরাতন ফলাফল দেখাবে।

ব্যবহারকারীর কাছে এটিকে আরও স্পষ্ট করতে, যখন পুরাতন ফলাফল তালিকা দেখানো হচ্ছে তখন একটি ভিজুয়াল ইন্ডিকেশন যোগ করতে পারেনঃ

```js {2}
<div style={{
  opacity: query !== deferredQuery ? 0.5 : 1
}}>
  <SearchResults query={deferredQuery} />
</div>
```

নিচের উদাহরণে `"a"` ইনপুট দিন, ফলাফল লোড হওয়ার জন্য অপেক্ষা করুন এবং তারপর ইনপুটটি এডিট করে `"ab"` করুন। লক্ষ্য করুন যে নতুন ফলাফলগুলো লোড না হওয়া পর্যন্ত Suspense fallback এর পরিবর্তে আপনি এখন পুরাতন ফলাফল তালিকাটি কিছুটা অস্পষ্ট ভাবে দেখতে পাচ্ছেনঃ


<Sandpack>

```js src/App.js
import { Suspense, useState, useDeferredValue } from 'react';
import SearchResults from './SearchResults.js';

export default function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);
  const isStale = query !== deferredQuery;
  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={e => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
        <div style={{ opacity: isStale ? 0.5 : 1 }}>
          <SearchResults query={deferredQuery} />
        </div>
      </Suspense>
    </>
  );
}
```

```js src/SearchResults.js hidden
import {use} from 'react';
import { fetchData } from './data.js';

export default function SearchResults({ query }) {
  if (query === '') {
    return null;
  }
  const albums = use(fetchData(`/search?q=${query}`));
  if (albums.length === 0) {
    return <p>No matches for <i>"{query}"</i></p>;
  }
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url.startsWith('/search?q=')) {
    return await getSearchResults(url.slice('/search?q='.length));
  } else {
    throw Error('Not implemented');
  }
}

async function getSearchResults(query) {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 500);
  });

  const allAlbums = [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];

  const lowerQuery = query.trim().toLowerCase();
  return allAlbums.filter(album => {
    const lowerTitle = album.title.toLowerCase();
    return (
      lowerTitle.startsWith(lowerQuery) ||
      lowerTitle.indexOf(' ' + lowerQuery) !== -1
    )
  });
}
```

```css
input { margin: 10px; }
```

</Sandpack>

<Note>

Deferred values এবং [transitions](#preventing-already-revealed-content-from-hiding) উভয়ের দ্বারাই আপনি ইনলাইন ইন্ডিকেটর এর হিসেবে Suspense fallback দেখানো থেকে বিরত থাকতে পারেন। Transitions সম্পূর্ণ আপডেটটিকে জরুরী নয় হিসেবে ধরে নেয় তাই এগুলো সাধারণত ফ্রেমওয়ার্ক এবং রাউটার লাইব্রেরীগুলিতে নেভিগেশনের জন্য ব্যবহৃত হয়। অন্যদিকে, deferred values সাধারণত অ্যাপ্লিকেশন কোডে বেশি উপযোগী যেখানে আপনি UI এর একটি অংশকে কম-জরুরী হিসেবে চিহ্নিত করে দিতে পারেন এবং এটিকে অন্যান্য UI এর "পরে আসতে" দিতে পারেন।

</Note>

---

### প্রকাশ করে ফেলা কন্টেন্টগুলো লুকাতে না দেয়া {/*preventing-already-revealed-content-from-hiding*/}

যখন একটি কম্পোনেন্ট থেমে যায় তখন সবচেয়ে কাছের প্যরেন্ট Suspense boundary এটির পরিবর্তে fallback দেখায়। এমন যদি হয় যে এটি আগে থেকেই কিছু কন্টেন্ট দেখাচ্ছিল, তবে এটি ব্যবহারকারীর জন্য একটি অস্বাভাবিক অভিজ্ঞতা সৃষ্টি করতে পারে। এই বাটনটি প্রেস করে দেখুনঃ 

<Sandpack>

```js src/App.js
import { Suspense, useState } from 'react';
import IndexPage from './IndexPage.js';
import ArtistPage from './ArtistPage.js';
import Layout from './Layout.js';

export default function App() {
  return (
    <Suspense fallback={<BigSpinner />}>
      <Router />
    </Suspense>
  );
}

function Router() {
  const [page, setPage] = useState('/');

  function navigate(url) {
    setPage(url);
  }

  let content;
  if (page === '/') {
    content = (
      <IndexPage navigate={navigate} />
    );
  } else if (page === '/the-beatles') {
    content = (
      <ArtistPage
        artist={{
          id: 'the-beatles',
          name: 'The Beatles',
        }}
      />
    );
  }
  return (
    <Layout>
      {content}
    </Layout>
  );
}

function BigSpinner() {
  return <h2>🌀 Loading...</h2>;
}
```

```js src/Layout.js
export default function Layout({ children }) {
  return (
    <div className="layout">
      <section className="er">
        Music Browser
      </section>
      <main>
        {children}
      </main>
    </div>
  );
}
```

```js src/IndexPage.js
export default function IndexPage({ navigate }) {
  return (
    <button onClick={() => navigate('/the-beatles')}>
      Open The Beatles artist page
    </button>
  );
}
```

```js src/ArtistPage.js
import { Suspense } from 'react';
import Albums from './Albums.js';
import Biography from './Biography.js';
import Panel from './Panel.js';

export default function ArtistPage({ artist }) {
  return (
    <>
      <h1>{artist.name}</h1>
      <Biography artistId={artist.id} />
      <Suspense fallback={<AlbumsGlimmer />}>
        <Panel>
          <Albums artistId={artist.id} />
        </Panel>
      </Suspense>
    </>
  );
}

function AlbumsGlimmer() {
  return (
    <div className="glimmer-panel">
      <div className="glimmer-line" />
      <div className="glimmer-line" />
      <div className="glimmer-line" />
    </div>
  );
}
```

```js src/Albums.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Albums({ artistId }) {
  const albums = use(fetchData(`/${artistId}/albums`));
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/Biography.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Biography({ artistId }) {
  const bio = use(fetchData(`/${artistId}/bio`));
  return (
    <section>
      <p className="bio">{bio}</p>
    </section>
  );
}
```

```js src/Panel.js
export default function Panel({ children }) {
  return (
    <section className="panel">
      {children}
    </section>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url === '/the-beatles/albums') {
    return await getAlbums();
  } else if (url === '/the-beatles/bio') {
    return await getBio();
  } else {
    throw Error('Not implemented');
  }
}

async function getBio() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 500);
  });

  return `The Beatles were an English rock band,
    formed in Liverpool in 1960, that comprised
    John Lennon, Paul McCartney, George Harrison
    and Ringo Starr.`;
}

async function getAlbums() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 3000);
  });

  return [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];
}
```

```css
main {
  min-height: 200px;
  padding: 10px;
}

.layout {
  border: 1px solid black;
}

.er {
  background: #222;
  padding: 10px;
  text-align: center;
  color: white;
}

.bio { font-style: italic; }

.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}

.glimmer-panel {
  border: 1px dashed #aaa;
  background: linear-gradient(90deg, rgba(221,221,221,1) 0%, rgba(255,255,255,1) 100%);
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}

.glimmer-line {
  display: block;
  width: 60%;
  height: 20px;
  margin: 10px;
  border-radius: 4px;
  background: #f0f0f0;
}
```

</Sandpack>

আপনি যখন বাটনটি প্রেস করেছেন তখন `Router` কম্পোনেন্টটি `IndexPage` এর পরিবর্তে `ArtistPage` কে রেন্ডার করেছে। `ArtistPage` এর ভিতরের একটি কম্পোনেন্ট থেমে গেছে, তাই সবচেয়ে কাছের Suspense boundary fallback দেখানো শুরু করেছে। সবচেয়ে কাছের Suspense boundary রুটের কাছাকাছি ছিল, তাই সাইটের সমগ্র লেআউটটি `BigSpinner` দ্বারা প্রতিস্থাপিত হয়ে গেছে।

আপনি যদি চান যে এমনটি না হোক সেক্ষেত্রে আপনি [`startTransition`](/reference/react/startTransition) দ্বারা navigation state update কে *transition* হিসেবে চিহ্নিত করে দিতে পারেনঃ

```js {5,7}
function Router() {
  const [page, setPage] = useState('/');

  function navigate(url) {
    startTransition(() => {
      setPage(url);
    });
  }
  // ...
```

এটা React কে বলে যে state ট্রান্সিশনটি এখনই জরুরী নয়, এবং ইতিমধ্যেই প্রকাশ হয়ে যাওয়া কোনো কন্টেন্ট লুকানোর চেয়ে আগের পেজটি দেখিয়ে রাখাই ভাল। এখন বাটনটি ক্লিক করা হলে এটি `Biography` লোড হওয়ার জন্য "অপেক্ষা" করবেঃ

<Sandpack>

```js src/App.js
import { Suspense, startTransition, useState } from 'react';
import IndexPage from './IndexPage.js';
import ArtistPage from './ArtistPage.js';
import Layout from './Layout.js';

export default function App() {
  return (
    <Suspense fallback={<BigSpinner />}>
      <Router />
    </Suspense>
  );
}

function Router() {
  const [page, setPage] = useState('/');

  function navigate(url) {
    startTransition(() => {
      setPage(url);
    });
  }

  let content;
  if (page === '/') {
    content = (
      <IndexPage navigate={navigate} />
    );
  } else if (page === '/the-beatles') {
    content = (
      <ArtistPage
        artist={{
          id: 'the-beatles',
          name: 'The Beatles',
        }}
      />
    );
  }
  return (
    <Layout>
      {content}
    </Layout>
  );
}

function BigSpinner() {
  return <h2>🌀 Loading...</h2>;
}
```

```js src/Layout.js
export default function Layout({ children }) {
  return (
    <div className="layout">
      <section className="er">
        Music Browser
      </section>
      <main>
        {children}
      </main>
    </div>
  );
}
```

```js src/IndexPage.js
export default function IndexPage({ navigate }) {
  return (
    <button onClick={() => navigate('/the-beatles')}>
      Open The Beatles artist page
    </button>
  );
}
```

```js src/ArtistPage.js
import { Suspense } from 'react';
import Albums from './Albums.js';
import Biography from './Biography.js';
import Panel from './Panel.js';

export default function ArtistPage({ artist }) {
  return (
    <>
      <h1>{artist.name}</h1>
      <Biography artistId={artist.id} />
      <Suspense fallback={<AlbumsGlimmer />}>
        <Panel>
          <Albums artistId={artist.id} />
        </Panel>
      </Suspense>
    </>
  );
}

function AlbumsGlimmer() {
  return (
    <div className="glimmer-panel">
      <div className="glimmer-line" />
      <div className="glimmer-line" />
      <div className="glimmer-line" />
    </div>
  );
}
```

```js src/Albums.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Albums({ artistId }) {
  const albums = use(fetchData(`/${artistId}/albums`));
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/Biography.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Biography({ artistId }) {
  const bio = use(fetchData(`/${artistId}/bio`));
  return (
    <section>
      <p className="bio">{bio}</p>
    </section>
  );
}
```

```js src/Panel.js
export default function Panel({ children }) {
  return (
    <section className="panel">
      {children}
    </section>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url === '/the-beatles/albums') {
    return await getAlbums();
  } else if (url === '/the-beatles/bio') {
    return await getBio();
  } else {
    throw Error('Not implemented');
  }
}

async function getBio() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 500);
  });

  return `The Beatles were an English rock band,
    formed in Liverpool in 1960, that comprised
    John Lennon, Paul McCartney, George Harrison
    and Ringo Starr.`;
}

async function getAlbums() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 3000);
  });

  return [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];
}
```

```css
main {
  min-height: 200px;
  padding: 10px;
}

.layout {
  border: 1px solid black;
}

.er {
  background: #222;
  padding: 10px;
  text-align: center;
  color: white;
}

.bio { font-style: italic; }

.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}

.glimmer-panel {
  border: 1px dashed #aaa;
  background: linear-gradient(90deg, rgba(221,221,221,1) 0%, rgba(255,255,255,1) 100%);
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}

.glimmer-line {
  display: block;
  width: 60%;
  height: 20px;
  margin: 10px;
  border-radius: 4px;
  background: #f0f0f0;
}
```

</Sandpack>

একটি ট্রান্সিশন *সবগুলো* কন্টেন্ট লোড হওয়ার জন্য অপেক্ষা করে না। এটি কেবলমাত্র ইতিমধ্যে প্রকাশিত কন্টেন্ট লুকানো এড়ানোর জন্য যতক্ষণ সময় প্রয়োজন ততক্ষণ অপেক্ষা করে। উদাহরণস্বরূপ, ওয়েবসাইটের `Layout` ইতিমধ্যেই প্রকাশিত হয়ে গেছে, তাই এটিকে লোডিং স্পিনারের পিছনে লুকানো ভাল হবে না। তবে, `Albums` কে ঘিরে নেস্টেড `Suspense` boundary টি নতুন, তাই ট্রান্সিশনটি এর জন্য অপেক্ষা করে না।

<Note>

Suspense-enabled রাউটার থেকে এটা আশা করা যায় যে এগুলো স্বাভাবিকভাবেই নেভিগেশন আপডেটগুলোকে ট্রান্সিশনের মধ্যে রেখে দেবে।

</Note>

---

### ট্রান্সিশন ঘটছে এটি ইন্ডিকেট করা {/*indicating-that-a-transition-is-happening*/}

উপরের উদাহরণটিতে, আপনি যদি একবার বাটনটিতে ক্লিক করেন দেখবেন কোনো দৃশ্যমান ইংগিত নেই যে একটি নেভিগেশনের অগ্রগতি হচ্ছে। একটি ইন্ডিকেটর যোগ করতে আপনি [`startTransition`](/reference/react/startTransition) এর স্থানে [`useTransition`](/reference/react/useTransition) ব্যবহার করতে পারেন যা আপনাকে একটি boolean মান `isPending` দেয়। নিচের উদাহরণে ওয়েবসাইটের হেডার স্টাইল পরিবর্তন করার সময় একটি ট্রান্সিশন ঘটছে তা দেখানোর জন্য এটি ব্যবহার করা হয়েছেঃ

<Sandpack>

```js src/App.js
import { Suspense, useState, useTransition } from 'react';
import IndexPage from './IndexPage.js';
import ArtistPage from './ArtistPage.js';
import Layout from './Layout.js';

export default function App() {
  return (
    <Suspense fallback={<BigSpinner />}>
      <Router />
    </Suspense>
  );
}

function Router() {
  const [page, setPage] = useState('/');
  const [isPending, startTransition] = useTransition();

  function navigate(url) {
    startTransition(() => {
      setPage(url);
    });
  }

  let content;
  if (page === '/') {
    content = (
      <IndexPage navigate={navigate} />
    );
  } else if (page === '/the-beatles') {
    content = (
      <ArtistPage
        artist={{
          id: 'the-beatles',
          name: 'The Beatles',
        }}
      />
    );
  }
  return (
    <Layout isPending={isPending}>
      {content}
    </Layout>
  );
}

function BigSpinner() {
  return <h2>🌀 Loading...</h2>;
}
```

```js src/Layout.js
export default function Layout({ children, isPending }) {
  return (
    <div className="layout">
      <section className="header" style={{
        opacity: isPending ? 0.7 : 1
      }}>
        Music Browser
      </section>
      <main>
        {children}
      </main>
    </div>
  );
}
```

```js src/IndexPage.js
export default function IndexPage({ navigate }) {
  return (
    <button onClick={() => navigate('/the-beatles')}>
      Open The Beatles artist page
    </button>
  );
}
```

```js src/ArtistPage.js
import { Suspense } from 'react';
import Albums from './Albums.js';
import Biography from './Biography.js';
import Panel from './Panel.js';

export default function ArtistPage({ artist }) {
  return (
    <>
      <h1>{artist.name}</h1>
      <Biography artistId={artist.id} />
      <Suspense fallback={<AlbumsGlimmer />}>
        <Panel>
          <Albums artistId={artist.id} />
        </Panel>
      </Suspense>
    </>
  );
}

function AlbumsGlimmer() {
  return (
    <div className="glimmer-panel">
      <div className="glimmer-line" />
      <div className="glimmer-line" />
      <div className="glimmer-line" />
    </div>
  );
}
```

```js src/Albums.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Albums({ artistId }) {
  const albums = use(fetchData(`/${artistId}/albums`));
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/Biography.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function Biography({ artistId }) {
  const bio = use(fetchData(`/${artistId}/bio`));
  return (
    <section>
      <p className="bio">{bio}</p>
    </section>
  );
}
```

```js src/Panel.js
export default function Panel({ children }) {
  return (
    <section className="panel">
      {children}
    </section>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url === '/the-beatles/albums') {
    return await getAlbums();
  } else if (url === '/the-beatles/bio') {
    return await getBio();
  } else {
    throw Error('Not implemented');
  }
}

async function getBio() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 500);
  });

  return `The Beatles were an English rock band,
    formed in Liverpool in 1960, that comprised
    John Lennon, Paul McCartney, George Harrison
    and Ringo Starr.`;
}

async function getAlbums() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 3000);
  });

  return [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];
}
```

```css
main {
  min-height: 200px;
  padding: 10px;
}

.layout {
  border: 1px solid black;
}

.header {
  background: #222;
  padding: 10px;
  text-align: center;
  color: white;
}

.bio { font-style: italic; }

.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}

.glimmer-panel {
  border: 1px dashed #aaa;
  background: linear-gradient(90deg, rgba(221,221,221,1) 0%, rgba(255,255,255,1) 100%);
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
}

.glimmer-line {
  display: block;
  width: 60%;
  height: 20px;
  margin: 10px;
  border-radius: 4px;
  background: #f0f0f0;
}
```

</Sandpack>

---

### নেভিগেশনে Suspense boundary গুলো রিসেট করা {/*resetting-suspense-boundaries-on-navigation*/}

<<<<<<< HEAD
একটি ট্রান্সিশনের সময় React ইতিমধ্যে প্রকাশিত কন্টেন্ট লুকানো থেকে বিরত থাকবে। তবে, আপনি যদি ভিন্ন কোনো প্যরামিটার বিশিষ্ট রাউটে নেভিগেট করেন, আপনি হয়ত React কে বলতে চান যে এটি *ভিন্ন* কন্টেন্ট। আপনি এটি একটি `key` দিয়ে বুঝাতে পারেনঃ
=======
During a Transition, React avoids hiding already revealed content. However, when you navigate to *different* content, such as another user's profile, you'll want the boundary to show the fallback instead of the previous content. You can express this with a `key`:
>>>>>>> c7d6b700038c63d1aaf2c649af1aefe01ebbacac

```js
<ProfilePage key={queryParams.id} />
```

<<<<<<< HEAD
ধরুন, আপনি একজন ব্যবহারকারীর প্রোফাইল পেজের মধ্যেই নেভিগেট করছেন এবং কিছু একটা থেমে গেল। যদি সেই আপডেটটি একটি ট্রান্সিশনের মধ্যে থাকে, তবে এটি ইতিমধ্যেই প্রকাশিত কন্টেন্টের জন্য fallback কে ট্রিগার করবে না। এরকমটাই প্রত্যাশিত।

যাইহোক, এখন আবার মনে করুন আপনি দুটি ভিন্ন ব্যবহারকারীর প্রোফাইলের মধ্যে নেভিগেট করছেন। এই ক্ষেত্রে fallback টি দেখানো যুক্তিসই। উদাহরণস্বরূপ, একজন ব্যবহারকারীর টাইমলাই আরেকজন ব্যবহারকারীর টাইমলাইন থেকে *আলাদা কন্টেন্ট*। একটি `key` নির্দিষ্ট করে দেয়ার মাধ্যমে আপনি নিশ্চিত করেন যে, React ভিন্ন ভিন্ন ব্যবহারকারীর প্রোফাইলকে ভিন্ন ভিন্ন কম্পোনেন্ট হিসাবে বিবেচনা করে এবং নেভিগেশনের সময় Suspense boundary গুলো রিসেট করে। Suspense-integrated রাউটারগুলো এটি স্বয়ংক্রিয়ভাবেই করবে।
=======
With a different `key`, React treats the profiles as different content and resets the Suspense boundary during navigation. The `key` can go on the boundary itself or on a component above it. Suspense-integrated routers should do this automatically.

In the example below, opening the profile page loads the first profile. Pressing "Bob" navigates to a different profile, and the `key` resets the boundary, so the fallback shows instead of the previous user's bio. Try removing the `key`: the previous bio stays visible while the next one loads:

<Sandpack>

```js src/App.js hidden
import { useState } from 'react';
import ProfilePage from './ProfilePage.js';

export default function App() {
  const [show, setShow] = useState(false);
  if (show) {
    return <ProfilePage />;
  }
  return (
    <button onClick={() => setShow(true)}>
      Open profile page
    </button>
  );
}
```

```js src/ProfilePage.js active
import { Suspense, useState, startTransition } from 'react';
import Bio from './Bio.js';
import { fetchBio } from './data.js';

export default function ProfilePage() {
  const [user, setUser] = useState(() => ({
    id: 'alice',
    bioPromise: fetchBio('alice'),
  }));
  function navigate(id) {
    startTransition(() => {
      setUser({ id, bioPromise: fetchBio(id) });
    });
  }
  return (
    <>
      <button onClick={() => navigate('alice')}>
        Alice
      </button>
      <button onClick={() => navigate('bob')}>
        Bob
      </button>
      <Suspense key={user.id} fallback={<p>⌛ Loading profile...</p>}>
        <Bio bioPromise={user.bioPromise} />
      </Suspense>
    </>
  );
}
```

```js src/Bio.js
import { use } from 'react';

export default function Bio({ bioPromise }) {
  const bio = use(bioPromise);
  return <p>{bio}</p>;
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.

export async function fetchBio(userId) {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 1500);
  });

  return userId === 'alice'
    ? 'Alice is a photographer and traveler.'
    : 'Bob collects vintage synthesizers.';
}
```

```css
button {
  margin-right: 8px;
}
```

</Sandpack>
>>>>>>> c7d6b700038c63d1aaf2c649af1aefe01ebbacac

---

### Server errors এবং client-only কন্টেন্টের জন্য একটি fallback দেওয়া {/*providing-a-fallback-for-server-errors-and-server-only-content*/}

আপনি যদি [streaming server rendering APIs](/reference/react-dom/server) (অথবা এদের উপর নির্ভরকারী একটি ফ্রেমওয়ার্ক) এর কোনোটি ব্যবহার করেন, React আপনার `<Suspense>` boundary গুলোও ব্যবহার করবে সার্ভার এর এরর গুলো তত্ত্বাবধান করতে। যদি কোনো কম্পোনেন্ট সার্ভারে এরর থ্রো করে, React সার্ভার রেন্ডার বন্ধ করবে না। বরং, এর সবচেয়ে কাছের `<Suspense>` কম্পোনেন্টটি খুঁজে বের করবে এবং তার fallback (যেমন একটি স্পিনার), তৈরি করা সার্ভার HTML এর মধ্যে যোগ করবে। ব্যবহারকারী প্রথমে একটি স্পিনার দেখবে।

ক্লায়েন্টে, একই কম্পোনেন্টটি React আবার রেন্ডার করার চেষ্টা করবে। যদি ক্লায়েন্টেও এরর হয়, React এররটি থ্রো করবে এবং সবচেয়ে কাছের [Error Boundary](/reference/react/Component#static-getderivedstatefromerror) টি দেখাবে। তবে, যদি ক্লায়েন্টে এরর না হয়, React এরর টি ক্লায়েন্টে দেখাবে না যেহেতু কন্টেন্টটি শেষ পর্যন্ত সফলভাবে প্রদর্শিত হয়েছিল।

সার্ভারে রেন্ডার হওয়া থেকে কিছু কম্পোনেন্ট তুলে নিতে চাইলে আপনি এটি ব্যবহার করতে পারেন। এটি করতে, সার্ভার এনভায়রনমেন্ট এ এরর থ্রো করুন এবং তারপর তাদের HTML গুলো fallback দিয়ে পরিবর্তন করতে তাদের একটি `<Suspense>` বাউন্ডারির মধ্যে রেখে দিনঃ

```js
<Suspense fallback={<Loading />}>
  <Chat />
</Suspense>

function Chat() {
  if (typeof window === 'undefined') {
    throw Error('Chat should only render on the client.');
  }
  // ...
}
```

সার্ভার HTML টির মধ্যেই লোডিং নির্দেশকটি থাকবে। এটি ক্লায়েন্টে `Chat` কম্পোনেন্ট দ্বারা প্রতিস্থাপিত হবে।

---

<<<<<<< HEAD
## সমস্যার সমাধান করা {/*troubleshooting*/}
=======
### Waiting for a stylesheet to load {/*waiting-for-a-stylesheet-to-load*/}

A stylesheet rendered with [`<link rel="stylesheet">` and a `precedence` prop](/reference/react-dom/components/link#special-rendering-behavior) blocks the Suspense boundary until the stylesheet loads, up to a timeout, so the content doesn't appear unstyled.

In the example below, the `Card` component renders a stylesheet with `precedence`. Press "Show card": React shows the fallback until the stylesheet has loaded, and then reveals the card with its styles applied.

For comparison, the second button performs the same update without React, in a separate document. Nothing waits for the stylesheet, so the card's text appears in a fallback font first and then switches:

<Sandpack>

```js
import { Suspense, useState, startTransition } from 'react';
import { freshStylesheetUrl } from './styles.js';
import VanillaCard from './VanillaCard.js';

function Card({ href }) {
  return (
    <>
      <link rel="stylesheet" href={href} precedence="default" />
      <div className="fancy-card">This card uses a font from the stylesheet.</div>
    </>
  );
}

export default function App() {
  const [href, setHref] = useState(null);
  return (
    <>
      <button
        onClick={() => {
          startTransition(() => {
            setHref(freshStylesheetUrl());
          });
        }}>
        Show card
      </button>
      {href && (
        <Suspense fallback={<p>⌛ Loading styles...</p>}>
          <Card href={href} />
        </Suspense>
      )}
      <hr />
      <VanillaCard />
    </>
  );
}
```

```js src/VanillaCard.js
import { useRef } from 'react';
import { freshStylesheetUrl } from './styles.js';

export default function VanillaCard() {
  const ref = useRef(null);
  function show() {
    const doc = ref.current.contentWindow.document;
    doc.open();
    doc.write(`
      <style>
        body { margin: 0; }
        .fancy-card {
          padding: 20px;
          border-radius: 8px;
          color: white;
          font-family: 'Caveat', sans-serif;
          font-size: 24px;
          background: linear-gradient(135deg, #087ea4, #2b3491);
        }
      </style>
      <div class="fancy-card">This card uses a font from the stylesheet.</div>
      <link rel="stylesheet" href="${freshStylesheetUrl()}">
    `);
    doc.close();
  }
  return (
    <>
      <button onClick={show}>Show card (without React)</button>
      <iframe ref={ref} title="Vanilla card" className="vanilla-frame" />
    </>
  );
}
```

```js src/styles.js hidden
// Add a unique parameter so the stylesheet isn't cached,
// and every run shows the loading state.
export function freshStylesheetUrl() {
  return (
    'https://fonts.googleapis.com/css2?family=Caveat&display=swap' +
    '&t=' +
    Date.now()
  );
}
```

```css
#root {
  min-height: 300px;
}
button {
  margin-right: 8px;
}
hr {
  margin: 16px 0;
}
.fancy-card {
  margin-top: 1em;
  padding: 20px;
  border-radius: 8px;
  color: white;
  font-family: 'Caveat', sans-serif;
  font-size: 24px;
  background: linear-gradient(135deg, #087ea4, #2b3491);
}
.vanilla-frame {
  display: block;
  margin-top: 1em;
  border: none;
  width: 100%;
  height: 90px;
}
```

</Sandpack>

---

### <CanaryBadge /> Animating from Suspense content {/*animating-from-suspense-content*/}

Suspense composes with [`<ViewTransition>`](/reference/react/ViewTransition) to animate the swap from the fallback to the content. Wrap the boundary in a `<ViewTransition>`, and React treats the swap as an update, cross-fading between the fallback and the content by default:

<Sandpack>

```js src/Video.js hidden
function Thumbnail({video, children}) {
  return (
    <div
      aria-hidden="true"
      tabIndex={-1}
      className={`thumbnail ${video.image}`}
    />
  );
}

export function Video({video}) {
  return (
    <div className="video">
      <div className="link">
        <Thumbnail video={video}></Thumbnail>
        <div className="info">
          <div className="video-title">{video.title}</div>
          <div className="video-description">{video.description}</div>
        </div>
      </div>
    </div>
  );
}

export function VideoPlaceholder() {
  const video = {image: 'loading'};
  return (
    <div className="video">
      <div className="link">
        <Thumbnail video={video}></Thumbnail>
        <div className="info">
          <div className="video-title loading" />
          <div className="video-description loading" />
        </div>
      </div>
    </div>
  );
}
```

```js
import {ViewTransition, useState, startTransition, Suspense} from 'react';
import {Video, VideoPlaceholder} from './Video';
import {useLazyVideoData} from './data';

function LazyVideo() {
  const video = useLazyVideoData();
  return <Video video={video} />;
}

export default function Component() {
  const [showItem, setShowItem] = useState(false);
  return (
    <>
      <button
        onClick={() => {
          startTransition(() => {
            setShowItem((prev) => !prev);
          });
        }}>
        {showItem ? '➖' : '➕'}
      </button>
      {showItem ? (
        <ViewTransition>
          <Suspense fallback={<VideoPlaceholder />}>
            <LazyVideo />
          </Suspense>
        </ViewTransition>
      ) : null}
    </>
  );
}
```

```js src/data.js hidden
import {use} from 'react';

let cache = null;

function fetchVideo() {
  if (!cache) {
    cache = new Promise((resolve) => {
      setTimeout(() => {
        resolve({
          id: '1',
          title: 'First video',
          description: 'Video description',
          image: 'blue',
        });
      }, 1000);
    });
  }
  return cache;
}

export function useLazyVideoData() {
  return use(fetchVideo());
}
```

```css
#root {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 200px;
}
button {
  border: none;
  border-radius: 50%;
  width: 50px;
  height: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #f0f8ff;
  color: white;
  font-size: 20px;
  cursor: pointer;
  transition: background-color 0.3s, border 0.3s;
}
button:hover {
  border: 2px solid #ccc;
  background-color: #e0e8ff;
}
.thumbnail {
  position: relative;
  aspect-ratio: 16 / 9;
  display: flex;
  overflow: hidden;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  border-radius: 0.5rem;
  outline-offset: 2px;
  width: 8rem;
  vertical-align: middle;
  background-color: #ffffff;
  background-size: cover;
  user-select: none;
}
.thumbnail.blue {
  background-image: conic-gradient(at top right, #c76a15, #087ea4, #2b3491);
}
.loading {
  background-image: linear-gradient(
    90deg,
    rgba(173, 216, 230, 0.3) 25%,
    rgba(135, 206, 250, 0.5) 50%,
    rgba(173, 216, 230, 0.3) 75%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
}
@keyframes shimmer {
  0% {
    background-position: -200% 0;
  }
  100% {
    background-position: 200% 0;
  }
}
.video {
  display: flex;
  flex-direction: row;
  gap: 0.75rem;
  align-items: center;
  margin-top: 1em;
}
.video .link {
  display: flex;
  flex-direction: row;
  flex: 1 1 0;
  gap: 0.125rem;
  outline-offset: 4px;
  cursor: pointer;
}
.video .info {
  display: flex;
  flex-direction: column;
  justify-content: center;
  margin-left: 8px;
  gap: 0.125rem;
}
.video .info:hover {
  text-decoration: underline;
}
.video-title {
  font-size: 15px;
  line-height: 1.25;
  font-weight: 700;
  color: #23272f;
}
.video-title.loading {
  height: 20px;
  width: 80px;
  border-radius: 0.5rem;
}
.video-description {
  color: #5e687e;
  font-size: 13px;
  border-radius: 0.5rem;
}
.video-description.loading {
  height: 15px;
  width: 100px;
}
```

```json package.json hidden
{
  "dependencies": {
    "react": "canary",
    "react-dom": "canary",
    "react-scripts": "latest"
  }
}
```

</Sandpack>

<Note>

Where you place the `<ViewTransition>` relative to the boundary determines whether the fallback and content cross-fade as one update or animate as separate exit and enter animations. You can also [customize the animation](/reference/react/ViewTransition#customizing-animations) with View Transition classes.

[Learn more about animating from Suspense content.](/reference/react/ViewTransition#animating-from-suspense-content)

</Note>

---

### <CanaryBadge /> Waiting for a font to load {/*waiting-for-a-font-to-load*/}

When a [`<ViewTransition>`](/reference/react/ViewTransition) animates a Suspense boundary's reveal, React waits for new fonts the content introduces, up to a timeout, so the text doesn't flash with a fallback font. This only happens during a `<ViewTransition>` update.

In the example below, the Suspense boundary is wrapped in a `<ViewTransition>`, and the `Quote` component suspends while its data loads. Rendering the quote starts its font download. React keeps the fallback visible until the font has loaded, so the quote appears already in its font.

For comparison, the second button performs the same update without React. Nothing waits for the font, so the text appears in a fallback font first and then switches:

<Sandpack>

```js
import { ViewTransition, Suspense, use, useState, startTransition } from 'react';
import { fetchQuote } from './data.js';
import { freshFontUrl } from './font.js';
import VanillaQuote from './VanillaQuote.js';

function Quote({ fontSrc }) {
  const quote = use(fetchQuote());
  return (
    <>
      <style href={fontSrc} precedence="default">
        {`@font-face {
          font-family: 'Fancy';
          src: url(${fontSrc}) format('truetype');
          font-display: swap;
        }`}
      </style>
      <p className="quote fancy">{quote}</p>
    </>
  );
}

export default function App() {
  const [fontSrc, setFontSrc] = useState(null);
  return (
    <>
      <button
        onClick={() => {
          startTransition(() => {
            setFontSrc(freshFontUrl());
          });
        }}>
        Show quote
      </button>
      {fontSrc && (
        <ViewTransition>
          <Suspense fallback={<p className="quote">⌛ Loading quote...</p>}>
            <Quote fontSrc={fontSrc} />
          </Suspense>
        </ViewTransition>
      )}
      <hr />
      <VanillaQuote />
    </>
  );
}
```

```js src/VanillaQuote.js
import { useRef } from 'react';
import { freshFontUrl } from './font.js';

export default function VanillaQuote() {
  const ref = useRef(null);
  function show() {
    const style = document.createElement('style');
    style.textContent = `@font-face {
      font-family: 'VanillaFancy';
      src: url(${freshFontUrl()}) format('truetype');
      font-display: swap;
    }`;
    document.head.appendChild(style);
    ref.current.innerHTML = `<p class="quote vanilla-fancy">The best way to predict the future is to invent it.</p>`;
  }
  return (
    <>
      <button onClick={show}>Show quote (without React)</button>
      <div ref={ref} />
    </>
  );
}
```

```js src/font.js hidden
// Add a unique parameter so the font isn't cached,
// and every run shows the loading state.
export function freshFontUrl() {
  return (
    'https://raw.githubusercontent.com/google/fonts/main/ofl/caveat/Caveat%5Bwght%5D.ttf' +
    '?t=' +
    Date.now()
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = null;

export function fetchQuote() {
  if (!cache) {
    cache = new Promise((resolve) => {
      // Add a fake delay to make waiting noticeable.
      setTimeout(() => {
        resolve(
          'The best way to predict the future is to invent it.'
        );
      }, 500);
    });
  }
  return cache;
}
```

```css
#root {
  min-height: 260px;
}
.quote {
  font-size: 20px;
  margin-top: 1em;
}
.fancy {
  font-family: 'Fancy', sans-serif;
}
.vanilla-fancy {
  font-family: 'VanillaFancy', sans-serif;
}
hr {
  margin: 16px 0;
}
```

```json package.json hidden
{
  "dependencies": {
    "react": "canary",
    "react-dom": "canary",
    "react-scripts": "latest"
  }
}
```

</Sandpack>

---

### <CanaryBadge /> Waiting for an image to load {/*waiting-for-an-image-to-load*/}

When a [`<ViewTransition>`](/reference/react/ViewTransition) animates a Suspense boundary's reveal, React waits for visible images to load, up to a timeout, so the animation doesn't start with a half-loaded image. This only happens during a `<ViewTransition>` update. Adding an `onLoad` handler opts a specific image out, even inside a `<ViewTransition>`.

In the example below, the Suspense boundary is wrapped in a `<ViewTransition>` and shows a profile skeleton until the portrait has loaded.

For comparison, the second button performs the same update without React. Nothing waits for the image, so the card appears immediately and the image pops in when it loads:

<Sandpack>

```js
import { ViewTransition, Suspense, useState, startTransition } from 'react';
import { freshImageUrl } from './image.js';
import VanillaProfile from './VanillaProfile.js';

function Profile({ src }) {
  return (
    <div className="card">
      <img src={src} alt="Jack Pope" width={80} height={80} />
      <p>Jack Pope</p>
    </div>
  );
}

function ProfilePlaceholder() {
  return (
    <div className="card">
      <div className="avatar-placeholder" />
      <p className="name-placeholder">&nbsp;</p>
    </div>
  );
}

export default function App() {
  const [src, setSrc] = useState(null);
  return (
    <>
      <button
        onClick={() => {
          startTransition(() => {
            setSrc(freshImageUrl());
          });
        }}>
        Show profile
      </button>
      {src && (
        <ViewTransition>
          <Suspense fallback={<ProfilePlaceholder />}>
            <Profile src={src} />
          </Suspense>
        </ViewTransition>
      )}
      <hr />
      <VanillaProfile />
    </>
  );
}
```

```js src/VanillaProfile.js
import { useRef } from 'react';
import { freshImageUrl } from './image.js';

export default function VanillaProfile() {
  const ref = useRef(null);
  function show() {
    ref.current.innerHTML = `<div class="card">
      <img src="${freshImageUrl()}" alt="Jack Pope" width="80" height="80" />
      <p>Jack Pope</p>
    </div>`;
  }
  return (
    <>
      <button onClick={show}>Show profile (without React)</button>
      <div ref={ref} />
    </>
  );
}
```

```js src/image.js hidden
// Add a unique parameter so the image isn't cached,
// and every run shows the loading state.
export function freshImageUrl() {
  return 'https://react.dev/images/team/jack-pope.jpg?t=' + Date.now();
}
```

```css
#root {
  min-height: 390px;
}
.card {
  margin-top: 1em;
}
.card img {
  display: block;
  border-radius: 50%;
  background: #dfe3e9;
}
.card p {
  font-weight: bold;
}
.avatar-placeholder {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: #dfe3e9;
}
.name-placeholder {
  width: 90px;
  border-radius: 4px;
  background: #dfe3e9;
}
hr {
  margin: 16px 0;
}
```

```json package.json hidden
{
  "dependencies": {
    "react": "canary",
    "react-dom": "canary",
    "react-scripts": "latest"
  }
}
```

</Sandpack>

---

### <CanaryBadge /> Coordinating fonts, images, and stylesheets {/*coordinating-fonts-images-and-stylesheets*/}

A Suspense boundary can wait for data, stylesheets, fonts, and images at once. Waiting for fonts and images only happens during a [`<ViewTransition>`](/reference/react/ViewTransition) update. In the example below, the `ProfileCard` component suspends while its data loads, and renders a stylesheet with `precedence`, text in a new font, and a portrait. React keeps the skeleton visible while the data and the stylesheet load. The `<ViewTransition>` reveal then waits for the font and the image, so the card appears complete.

For comparison, the version without React loads the same data and shows every resource arriving on its own schedule:

<Sandpack>

```js
import { ViewTransition, Suspense, use, useState, startTransition } from 'react';
import { fetchQuote } from './data.js';
import { freshStylesheetUrl, freshImageUrl } from './resources.js';
import VanillaProfileCard from './VanillaProfileCard.js';

function ProfileCard({ resources }) {
  const quote = use(resources.quotePromise);
  return (
    <>
      <link rel="stylesheet" href={resources.stylesheet} precedence="default" />
      <div className="profile-card">
        <img src={resources.image} alt="Jack Pope" width={80} height={80} />
        <div>
          <p className="name">Jack Pope</p>
          <p className="bio">{quote}</p>
        </div>
      </div>
    </>
  );
}

function ProfileCardPlaceholder() {
  return (
    <div className="profile-card">
      <div className="avatar-placeholder" />
      <div>
        <p className="name name-placeholder">&nbsp;</p>
        <p className="bio bio-placeholder">&nbsp;</p>
      </div>
    </div>
  );
}

export default function App() {
  const [resources, setResources] = useState(null);
  return (
    <>
      <button
        onClick={() => {
          startTransition(() => {
            setResources({
              quotePromise: fetchQuote(),
              stylesheet: freshStylesheetUrl(),
              image: freshImageUrl(),
            });
          });
        }}>
        Show profile
      </button>
      {resources && (
        <ViewTransition>
          <Suspense fallback={<ProfileCardPlaceholder />}>
            <ProfileCard resources={resources} />
          </Suspense>
        </ViewTransition>
      )}
      <hr />
      <VanillaProfileCard />
    </>
  );
}
```

```js src/VanillaProfileCard.js
import { useRef } from 'react';
import { fetchQuote } from './data.js';
import { freshStylesheetUrl, freshImageUrl } from './resources.js';

export default function VanillaProfileCard() {
  const ref = useRef(null);
  async function show() {
    const quote = await fetchQuote();
    const doc = ref.current.contentWindow.document;
    doc.open();
    doc.write(`
      <style>
        body { margin: 0; font-family: sans-serif; }
        .profile-card { display: flex; gap: 12px; align-items: center; }
        .profile-card img { border-radius: 50%; background: #dfe3e9; }
        .name { margin: 0 0 4px; font-family: 'Caveat', sans-serif; font-size: 22px; line-height: 28px; font-weight: bold; }
        .bio { margin: 0; font-family: 'Caveat', sans-serif; font-size: 20px; line-height: 26px; }
      </style>
      <div class="profile-card">
        <img src="${freshImageUrl()}" alt="Jack Pope" width="80" height="80" />
        <div>
          <p class="name">Jack Pope</p>
          <p class="bio">${quote}</p>
        </div>
      </div>
      <link rel="stylesheet" href="${freshStylesheetUrl()}">
    `);
    doc.close();
  }
  return (
    <>
      <button onClick={show}>Show profile (without React)</button>
      <iframe ref={ref} title="Vanilla profile card" className="vanilla-frame" />
    </>
  );
}
```

```js src/resources.js hidden
// Add a unique parameter so the resources aren't cached,
// and every run shows the loading state.
export function freshStylesheetUrl() {
  return (
    'https://fonts.googleapis.com/css2?family=Caveat&display=swap' +
    '&t=' +
    Date.now()
  );
}

export function freshImageUrl() {
  return 'https://react.dev/images/team/jack-pope.jpg?t=' + Date.now();
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.

export async function fetchQuote() {
  // Add a fake delay to make waiting noticeable.
  await new Promise((resolve) => {
    setTimeout(resolve, 1000);
  });
  return 'The best way to predict the future is to invent it.';
}
```

```css
#root {
  min-height: 320px;
}
button {
  margin-right: 8px;
}
hr {
  margin: 16px 0;
}
.profile-card {
  display: flex;
  gap: 12px;
  align-items: center;
  margin-top: 1em;
}
.profile-card img {
  border-radius: 50%;
  background: #dfe3e9;
}
.name {
  margin: 0 0 4px;
  font-family: 'Caveat', sans-serif;
  font-size: 22px;
  line-height: 28px;
  font-weight: bold;
}
.bio {
  margin: 0;
  font-family: 'Caveat', sans-serif;
  font-size: 20px;
  line-height: 26px;
}
.profile-card img {
  display: block;
}
.avatar-placeholder {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: #dfe3e9;
}
.name-placeholder,
.bio-placeholder {
  border-radius: 4px;
  background: #dfe3e9;
  color: transparent;
}
.name-placeholder {
  width: 90px;
}
.bio-placeholder {
  width: 220px;
}
.vanilla-frame {
  display: block;
  margin-top: 1em;
  border: none;
  width: 100%;
  height: 110px;
}
```

```json package.json hidden
{
  "dependencies": {
    "react": "canary",
    "react-dom": "canary",
    "react-scripts": "latest"
  }
}
```

</Sandpack>

---

## Troubleshooting {/*troubleshooting*/}
>>>>>>> c7d6b700038c63d1aaf2c649af1aefe01ebbacac

### কিভাবে আমি একটি আপডেটের সময় fallback দ্বারা UI প্রতিস্থাপিত হওয়াকে রোধ করবো? {/*preventing-unwanted-fallbacks*/}

fallback দ্বারা দৃশ্যমান UI প্রতিস্থাপন করা হলে ব্যবহারকারীর জন্য এটি অস্বাভাবিক অভিজ্ঞতা তৈরি করে। এটি ঘটতে পারে যখন একটি আপডেট কোনো কম্পোনেন্টকে থামিয়ে দেয় এবং সবচেয়ে কাছের Suspense boundary ইতিমধ্যেই ব্যবহারকারীর জন্য কন্টেন্ট দেখাচ্ছে।

এটি ঘটানো থেকে বিরত থাকার জন্য [আপডেটটিকে অতি জরুরি নয় হিসেবে চিহ্নিত করুন `startTransition`](#preventing-already-revealed-content-from-hiding)। একটি ট্রান্সিশনের সময়, React একটি অবাঞ্ছিত fallback দেখানো থেকে বিরত রাখতে পর্যাপ্ত পরিমাণ ডেটা লোড না হওয়ার পর্যন্ত অপেক্ষা করবেঃ

```js {2-3,5}
function handleNextPageClick() {
  // If this update suspends, don't hide the already displayed content
  startTransition(() => {
    setCurrentPage(currentPage + 1);
  });
}
```

এটা বিদ্যমান কন্টেন্ট লুকানো থেকে এড়িয়ে যাবে। তবে, যেকোনো নতুন রেন্ডার করা `Suspense` বাউন্ডারি তাৎক্ষণাৎ fallback দেখাবে যাতে UI ব্লক না হয় এবং কন্টেন্ট পাওয়া মাত্র ব্যবহারকারী তা দেখতে পারে।

**React শুধুমাত্র অতি জরুরি নয় এমন আপডেট এর সময়ই অস্বাভাবিক fallback গুলো প্রতিরোধ করবে**। এটি একটি রেন্ডারকে বিলম্ব করবে না যদি এটি কোনো জরুরী আপডেটের ফলাফল হয়ে থাকে। আপনাকে অবশ্যই [`startTransition`](/reference/react/startTransition) বা [`useDeferredValue`](/reference/react/useDeferredValue) এর মত API ব্যবহার করতে হবে।

আপনার রাউটারটি যদি Suspense এর সাথে সংযোজিত থাকে, তাহলে এটি তার আপডেটগুলোকে স্বয়ংক্রিয়ভাবেই [`startTransition`](/reference/react/startTransition) এর মধ্যে রেখে দিবে।
