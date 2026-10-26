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

#### প্রপ্স {/*props*/}
* `children`: আপনি যেই প্রকৃত UI রেন্ডার করতে চাইছেন। `children` যদি রেন্ডারিং এর সময় থেমে যায় তাহলে Suspense boundary `fallback` রেন্ডার করবে।
* `fallback`: এটি হচ্ছে, প্রকৃত UI পুরোপুরি লোড না হলে তার স্থানে রেন্ডার হওয়া একটি বিকল্প UI। যেকোনো বৈধ React নোডই গৃহীত হয়, যদিও কার্যত, একটি fallback হলো একটি হালকা placeholder view, যেমন একটি লোডিং স্পিনার বা স্কেলেটন। Suspense স্বয়ংক্রিয় ভাবে `fallback` এ পরিবর্তিত হয়ে যাবে যখন `children` থেমে যাবে এবং আবার `children` এ ফিরে আসবে যখন ডেটা তৈরি হয়ে যাবে। রেন্ডারিং এর সময় যদি `fallback` থেমে যায়, এটি তার নিকটতম প্যারেন্ট Suspense boundary সক্রিয় করবে।
* <ExperimentalBadge /> **অপশনাল** `defer`: একটি boolean। `true` হলে React প্রথমে `fallback` দেখিয়ে পরে `children` রেন্ডার বা stream করতে পারে, এমনকি তাদের কোনো কিছু suspend না করলেও। রেন্ডার করতে বেশি কাজ লাগে এমন কনটেন্টের জন্য এটি ব্যবহার করুন। ডিফল্ট মান `false`।

#### সতর্কতা {/*caveats*/}

- Effect বা event handler-এর ভেতরে ডেটা fetch করা হলে Suspense তা শনাক্ত করে না। এটি শুধু [নিচে দেওয়া ক্ষেত্রগুলোতে সক্রিয় হয়।](#what-activates-a-suspense-boundary)
- প্রথমবার মাউন্ট হওয়ার আগেই suspend হয়ে যাওয়া রেন্ডারের কোনো state React সংরক্ষণ করে না। কম্পোনেন্ট লোড হলে React সেই ট্রি শুরু থেকে আবার রেন্ডার করার চেষ্টা করে।
- Suspense কোনো ট্রির কনটেন্ট দেখানোর পরে সেটি আবার suspend হলে `fallback` আবার দেখানো হবে, যদি না সংশ্লিষ্ট আপডেটটি [`startTransition`](/reference/react/startTransition) বা [`useDeferredValue`](/reference/react/useDeferredValue) দিয়ে করা হয়ে থাকে।
- React সর্বশেষ কনটেন্ট প্রকাশের সময় থেকে প্রতি 300 মিলিসেকেন্ডে সর্বোচ্চ একবার suspend হওয়া কনটেন্ট প্রকাশ করে। এই সময়ের মধ্যে প্রস্তুত হওয়া বাউন্ডারিগুলো একটির পর একটি না দেখিয়ে [একসাথে প্রকাশ করা হয়।](/blog/2025/10/01/react-19-2#batching-suspense-boundaries-for-ssr)
- আবার suspend হওয়ার কারণে আগে দেখানো কনটেন্ট লুকাতে হলে, React কনটেন্ট ট্রির [layout Effect](/reference/react/useLayoutEffect)-গুলোর cleanup চালায়। কনটেন্ট আবার দেখানোর জন্য প্রস্তুত হলে React layout Effect-গুলো পুনরায় চালায়। এতে DOM layout মাপার Effect-গুলো কনটেন্ট লুকানো অবস্থায় তা মাপার চেষ্টা করে না।
- React-এর *Streaming Server Rendering* এবং *Selective Hydration*-এর মতো অভ্যন্তরীণ অপটিমাইজেশনগুলো Suspense-এর সাথে সমন্বিত। আরও জানতে [আর্কিটেকচারের সারমর্ম](https://github.com/reactwg/react-18/discussions/37) পড়ুন এবং [টেকনিক্যাল আলোচনা](https://www.youtube.com/watch?v=pj5N-Khihgc) দেখুন।

---

### কী কী কারণে একটি Suspense বাউন্ডারি সক্রিয় হয় {/*what-activates-a-suspense-boundary*/}

একটি Suspense বাউন্ডারি তার কনটেন্ট প্রস্তুত হওয়া পর্যন্ত সেটি প্রকাশের জন্য অপেক্ষা করে। নিচের যেকোনো কারণে বাউন্ডারির কনটেন্ট প্রকাশ আটকে থাকতে পারে:

- [`lazy`](/reference/react/lazy) দিয়ে কম্পোনেন্টের কোড লোড করা।
- [`use`](/reference/react/use) দিয়ে একটি Promise পড়া। এর মধ্যে [Server Component](/reference/rsc/server-components) থেকে stream করা ডেটা বা [Suspense সমর্থিত ফ্রেমওয়ার্ক](#suspense-enabled-frameworks) দিয়ে লোড করা ডেটাও রয়েছে।
- [`<link rel="stylesheet">` এবং `precedence` prop দিয়ে রেন্ডার করা stylesheet](/reference/react-dom/components/link#special-rendering-behavior) লোড করা। একটি নির্দিষ্ট সময়সীমা পর্যন্ত, stylesheet লোড না হওয়া অবধি React বাউন্ডারির কনটেন্ট প্রকাশ আটকে রাখে। [নিচের উদাহরণ দেখুন।](#waiting-for-a-stylesheet-to-load)
- Streaming server rendering-এর সময় বড় কোনো বাউন্ডারির HTML এসে পৌঁছানোর জন্য অপেক্ষা করা। HTML পাঠাতে সময় লাগে, তাই যথেষ্ট পরিমাণ কনটেন্ট থাকলে তার ভেতরে কিছু suspend না হলেও বাউন্ডারি সক্রিয় হয়। HTML পৌঁছানোর সাথে সাথে React কনটেন্ট প্রকাশ করে।
- ফন্ট লোড করা। সাধারণভাবে Suspense ফন্টের জন্য অপেক্ষা করে না। তবে [`<ViewTransition>`](/reference/react/ViewTransition) দিয়ে বাউন্ডারির কনটেন্ট প্রকাশের অ্যানিমেশন চালালে, React নতুন ফন্ট লোড হওয়ার জন্য সর্বোচ্চ 500 মিলিসেকেন্ড অপেক্ষা করে, যেন টেক্সট হঠাৎ fallback ফন্টে দেখা না যায়। [নিচের উদাহরণ দেখুন।](#waiting-for-a-font-to-load)
- ছবি লোড করা। সাধারণভাবে Suspense ছবির জন্য অপেক্ষা করে না। তবে [`<ViewTransition>`](/reference/react/ViewTransition) দিয়ে বাউন্ডারির কনটেন্ট প্রকাশের অ্যানিমেশন চালালে, অ্যানিমেশন শুরুর আগে React দৃশ্যমান ছবিগুলো লোড হওয়ার জন্য সর্বোচ্চ 500 মিলিসেকেন্ড অপেক্ষা করে। কোনো ছবিতে `loading="lazy"` বা `onLoad` handler দিলে সেই ছবির জন্য অপেক্ষা করা হয় না। [নিচের উদাহরণ দেখুন।](#waiting-for-an-image-to-load)
- <ExperimentalBadge /> [`<Suspense defer>`](#props) বাউন্ডারির ভেতরে CPU-নির্ভর রেন্ডারিংয়ের কাজ করা।

<Note>

#### Suspense সমর্থিত ফ্রেমওয়ার্ক {/*suspense-enabled-frameworks*/}

একটি *Suspense সমর্থিত ফ্রেমওয়ার্ক* আপনাকে কম্পোনেন্টে এমনভাবে ডেটা পড়তে দেয়, যাতে সবচেয়ে কাছের Suspense বাউন্ডারি সক্রিয় হয়। ডেটা লোড করার সুনির্দিষ্ট পদ্ধতি আপনার ফ্রেমওয়ার্কের উপর নির্ভর করে; বিস্তারিত তার ডকুমেন্টেশনে পাবেন। ভেতরে ভেতরে এ ধরনের ফ্রেমওয়ার্ক Promise-এর একটি cache রাখে এবং কোনো Promise-এর জন্য রেন্ডারিং suspend করতে [`use`](/reference/react/use) কল করে।

ফ্রেমওয়ার্ক ছাড়াও সরাসরি `use` দিয়ে Promise পড়তে পারেন, যদি Promise-টি [cache করা থাকে, যাতে প্রতিবার রেন্ডারে একই instance পুনরায় ব্যবহার করা হয়।](/reference/react/use#caching-promises-for-client-components)

</Note>

---

## ব্যবহারবিধি {/*usage*/}

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

অন্যদিকে, `use` ছাড়া অন্য কোনোভাবে—যেমন Effect-এর ভেতরে—ডেটা fetch করলে বাউন্ডারিটি সক্রিয় হয় না:

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
  const reader = readable.getReader();

  while (true) {
    const {done, value} = await reader.read();
    if (done) {
      break;
    }
    doc.write(decoder.decode(value, {stream: true}));
  }

  doc.write(decoder.decode());
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

একটি প্রচলিত বিকল্প UI প্যাটার্ন হলো তালিকা আপডেটকে *বিলম্বিত করা(defer)* এবং নতুন ফলাফল তৈরি হওয়া পর্যন্ত আগের ফলাফলগুলো দেখানো। [`useDeferredValue`](/reference/react/useDeferredValue) হুকটি আপনাকে query করার একটি deferred ভার্সন পাঠানোর সুবিধা দেয়ঃ

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

Transition চলাকালে React আগে প্রকাশিত কনটেন্ট লুকানো এড়িয়ে চলে। তবে অন্য কোনো ব্যবহারকারীর প্রোফাইলের মতো *ভিন্ন* কনটেন্টে নেভিগেট করলে, আগের কনটেন্টের বদলে বাউন্ডারির fallback দেখানো প্রয়োজন। একটি `key` দিয়ে আপনি এই পার্থক্যটি বোঝাতে পারেন:

```js
<ProfilePage key={queryParams.id} />
```

আলাদা `key` থাকলে React প্রোফাইলগুলোকে ভিন্ন কনটেন্ট হিসেবে বিবেচনা করে এবং নেভিগেশনের সময় Suspense বাউন্ডারি রিসেট করে। বাউন্ডারিতে বা তার উপরের কোনো কম্পোনেন্টে `key` দেওয়া যায়। Suspense-এর সাথে সমন্বিত রাউটারগুলোর এটি স্বয়ংক্রিয়ভাবে করার কথা।

নিচের উদাহরণে, প্রোফাইল পেইজ খুললে প্রথম প্রোফাইলটি লোড হয়। "Bob" চাপলে অন্য প্রোফাইলে নেভিগেট করা হয় এবং `key` বাউন্ডারিটি রিসেট করে। তাই আগের ব্যবহারকারীর পরিচিতির বদলে fallback দেখানো হয়। `key` সরিয়ে দেখুন: পরের পরিচিতি লোড হওয়ার সময় আগেরটি দেখা যেতে থাকে:

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

### শুধু ব্রাউজারে রেন্ডার করা কনটেন্টের জন্য fallback দেখানো {/*providing-a-fallback-for-browser-only-content*/}

একটি Suspense বাউন্ডারি শুধু ব্রাউজারে রেন্ডার করা কম্পোনেন্টের জন্য fallback দেখাতে পারে। কম্পোনেন্টটিকে `<Suspense>` দিয়ে ঘিরে তার ভেতরে [`use(browser())`](/reference/react/use#use-browser) কল করুন।

প্রাথমিক HTML-এ লোডিং fallback দেখতে **Reload** চাপুন। hydration-এর পরে React `localStorage` থেকে লোড করা খসড়া দেখায়।

<Sandpack>

```js src/App.js active
import { Suspense, use, useState } from 'react';
import { browser } from 'react-dom';

function SavedDraft() {
  use(browser('The draft is stored in localStorage.'));
  const [draft, setDraft] = useState(
    () => localStorage.getItem('draft') ?? ''
  );

  function handleChange(event) {
    const nextDraft = event.target.value;
    setDraft(nextDraft);
    localStorage.setItem('draft', nextDraft);
  }

  return (
    <label>
      Draft:
      <textarea
        value={draft}
        onChange={handleChange}
        rows={4}
        cols={30}
      />
    </label>
  );
}

export default function App() {
  return (
    <>
      <h1>Saved draft</h1>
      <Suspense fallback={<p>Loading draft...</p>}>
        <SavedDraft />
      </Suspense>
    </>
  );
}
```

```js src/Document.js hidden
import App from './App.js';

export default function Document() {
  return (
    <html lang="en">
      <head>
        <title>Saved draft</title>
        <style>{`
          h1 { font-size: 24px; margin-top: 0; }
          label, textarea { display: block; }
          textarea { margin-top: 5px; }
        `}</style>
      </head>
      <body>
        <App />
      </body>
    </html>
  );
}
```

```js src/index.js hidden
import { hydrateRoot } from 'react-dom/client';
import { renderToReadableStream } from 'react-dom/server';
import Document from './Document.js';
import { flushReadableStreamToFrame } from './demo-helpers.js';
import './styles.css';

async function main(frame) {
  const stream = await renderToReadableStream(<Document />);
  await flushReadableStreamToFrame(stream, frame);

  // Wait so both the fallback and hydrated content are visible.
  await new Promise(resolve => setTimeout(resolve, 1200));
  hydrateRoot(frame.contentDocument, <Document />);
}

main(document.getElementById('preview'));
```

```js src/demo-helpers.js hidden
export async function flushReadableStreamToFrame(readable, frame) {
  const doc = frame.contentWindow.document;
  const decoder = new TextDecoder();
  const reader = readable.getReader();

  while (true) {
    const {done, value} = await reader.read();
    if (done) {
      break;
    }
    doc.write(decoder.decode(value, {stream: true}));
  }

  doc.write(decoder.decode());
  doc.close();
}
```

```html public/index.html hidden
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Browser-only rendering</title>
</head>
<body>
  <iframe id="preview" title="Rendered page"></iframe>
</body>
</html>
```

```css src/styles.css hidden
iframe {
  width: 100%;
  height: 160px;
  border: 0;
}
```

```json package.json hidden
{
  "dependencies": {
    "react": "19.3.0-canary-f1f7ed2a-20260904",
    "react-dom": "19.3.0-canary-f1f7ed2a-20260904",
    "react-scripts": "latest"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

</Sandpack>

সার্ভার রেন্ডারিংয়ের সময় React HTML-এ Suspense বাউন্ডারির fallback যুক্ত করে। ব্রাউজারে React সেই fallback-এর বদলে সংরক্ষিত খসড়াটি দেখায়।

---

### stylesheet লোড হওয়ার জন্য অপেক্ষা করা {/*waiting-for-a-stylesheet-to-load*/}

[`<link rel="stylesheet">` এবং `precedence` prop দিয়ে রেন্ডার করা stylesheet](/reference/react-dom/components/link#special-rendering-behavior) লোড না হওয়া পর্যন্ত, একটি নির্দিষ্ট সময়সীমার মধ্যে, Suspense বাউন্ডারির কনটেন্ট প্রকাশ আটকে থাকে। এতে কনটেন্ট স্টাইল ছাড়া দেখা যায় না।

নিচের উদাহরণে `Card` কম্পোনেন্ট `precedence`-সহ একটি stylesheet রেন্ডার করে। "Show card" চাপুন: stylesheet লোড না হওয়া পর্যন্ত React fallback দেখায়, তারপর স্টাইল প্রয়োগ করা কার্ডটি প্রকাশ করে।

তুলনার জন্য, দ্বিতীয় বাটনটি React ছাড়াই আলাদা document-এ একই আপডেট করে। stylesheet-এর জন্য অপেক্ষা করা হয় না বলে কার্ডের টেক্সট প্রথমে fallback ফন্টে দেখা যায়, পরে ফন্ট বদলে যায়:

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

### Suspense কনটেন্ট থেকে অ্যানিমেশন চালানো {/*animating-from-suspense-content*/}

fallback থেকে কনটেন্টে পরিবর্তনের অ্যানিমেশন চালাতে Suspense-এর সাথে [`<ViewTransition>`](/reference/react/ViewTransition) ব্যবহার করা যায়। বাউন্ডারিটিকে `<ViewTransition>` দিয়ে ঘিরে দিলে React এই পরিবর্তনকে একটি আপডেট হিসেবে বিবেচনা করে। ডিফল্টভাবে fallback ধীরে মিলিয়ে গিয়ে কনটেন্ট ফুটে ওঠে:

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
    "react": "19.3.0-canary-f1f7ed2a-20260904",
    "react-dom": "19.3.0-canary-f1f7ed2a-20260904",
    "react-scripts": "latest"
  }
}
```

</Sandpack>

<Note>

বাউন্ডারির তুলনায় `<ViewTransition>` কোথায় রাখছেন, তার উপর নির্ভর করে fallback ও কনটেন্ট একটি আপডেট হিসেবে cross-fade করবে, নাকি আলাদা প্রস্থান ও প্রবেশের অ্যানিমেশন দেখাবে। View Transition class দিয়ে [অ্যানিমেশন পরিবর্তনও করতে পারেন।](/reference/react/ViewTransition#customizing-animations)

[Suspense কনটেন্ট থেকে অ্যানিমেশন চালানো সম্পর্কে আরও জানুন।](/reference/react/ViewTransition#animating-from-suspense-content)

</Note>

---

### ফন্ট লোড হওয়ার জন্য অপেক্ষা করা {/*waiting-for-a-font-to-load*/}

[`<ViewTransition>`](/reference/react/ViewTransition) দিয়ে Suspense বাউন্ডারির কনটেন্ট প্রকাশের অ্যানিমেশন চালালে, কনটেন্টে ব্যবহৃত নতুন ফন্ট লোড হওয়ার জন্য React সর্বোচ্চ 500 মিলিসেকেন্ড অপেক্ষা করে। এতে টেক্সট হঠাৎ fallback ফন্টে দেখা যায় না। এটি শুধু `<ViewTransition>` আপডেটের সময় ঘটে।

নিচের উদাহরণে Suspense বাউন্ডারিটিকে `<ViewTransition>` দিয়ে ঘেরা হয়েছে, আর ডেটা লোড হওয়ার সময় `Quote` কম্পোনেন্ট suspend করে। উদ্ধৃতিটি রেন্ডার করার সময় একটি stylesheet লোড হয়, যা ফন্টটি যোগ করে। stylesheet ও ফন্ট লোড হওয়ার সময় React fallback দেখিয়ে রাখে। ফন্টটি 500 মিলিসেকেন্ডের মধ্যে লোড হলে, উদ্ধৃতিটি শুরু থেকেই সেই ফন্টে দেখা যায়।

তুলনার জন্য, দ্বিতীয় বাটনটি React ছাড়াই একই আপডেট করে। ফন্টের জন্য অপেক্ষা করা হয় না বলে টেক্সট প্রথমে fallback ফন্টে দেখা যায়, পরে ফন্ট বদলে যায়:

<Sandpack>

```js
import {
  ViewTransition,
  Suspense,
  use,
  useState,
  startTransition,
} from 'react';
import { fetchQuote } from './data.js';
import {
  freshFontUrl,
  freshStylesheetUrl,
} from './font.js';
import VanillaQuote from './VanillaQuote.js';

function Quote({ stylesheet }) {
  const quote = use(fetchQuote());
  return (
    <>
      <link
        rel="stylesheet"
        href={stylesheet}
        precedence="default"
      />
      <p className="quote fancy">{quote}</p>
    </>
  );
}

export default function App() {
  const [stylesheet, setStylesheet] = useState(null);
  return (
    <>
      <button
        onClick={() => {
          startTransition(() => {
            setStylesheet(freshStylesheetUrl());
          });
        }}>
        Show quote
      </button>
      {stylesheet && (
        <ViewTransition>
          <Suspense fallback={<p className="quote">⌛ Loading quote...</p>}>
            <Quote stylesheet={stylesheet} />
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
      src: url(${freshFontUrl()}) format('woff2');
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
export function freshStylesheetUrl() {
  // Add a unique parameter so the stylesheet isn't cached.
  return (
    'https://fonts.googleapis.com/css2?family=Caveat&display=swap' +
    '&t=' +
    Date.now()
  );
}

export function freshFontUrl() {
  // Add a unique parameter so the font isn't cached.
  return (
    'https://fonts.gstatic.com/s/caveat/v23/WnznHAc5bAfYB2QRah7pcpNvOx-pjfJ9eIWpYT5Kmgq3sw.woff2' +
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
  font-family: 'Caveat', sans-serif;
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
    "react": "19.3.0",
    "react-dom": "19.3.0",
    "react-scripts": "latest"
  }
}
```

</Sandpack>

---

### ছবি লোড হওয়ার জন্য অপেক্ষা করা {/*waiting-for-an-image-to-load*/}

[`<ViewTransition>`](/reference/react/ViewTransition)-এর ভেতরে কোনো Suspense বাউন্ডারি কনটেন্ট প্রকাশ করলে, অ্যানিমেশন শুরুর আগে React দৃশ্যমান ছবিগুলো লোড হওয়ার জন্য সর্বোচ্চ 500 মিলিসেকেন্ড অপেক্ষা করে। কোনো ছবিতে `loading="lazy"` বা `onLoad` handler দিলে সেই ছবির জন্য অপেক্ষা করা হয় না।

একই বাউন্ডারি `<ViewTransition>`-এর ভেতরে ও বাইরে রেখে তুলনা করুন। ভেতরে থাকলে, ছবি লোড হওয়ার সময় React সর্বোচ্চ 500 মিলিসেকেন্ড প্রোফাইলের skeleton দেখিয়ে রাখে, যাতে ছবিসহ কার্ডটি প্রকাশ করা যায়। বাইরে থাকলে, Promise resolve হওয়া মাত্রই Suspense skeleton দেখানো বন্ধ করে। ছবি তখনও লোড হতে থাকলে, প্রথমে কার্ড দেখা যায়, পরে হঠাৎ ছবিটি দেখা দেয়:

<Sandpack>

```js
import {
  ViewTransition,
  Suspense,
  use,
  useState,
} from 'react';
import { fetchImageSrc } from './image.js';

function Profile({ cacheKey }) {
  const src = use(fetchImageSrc(cacheKey));
  return (
    <div className="card">
      <img src={src} alt="" width={80} height={80} />
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
  const [showWithTransition, setShowWithTransition] =
    useState(false);
  const [showWithoutTransition, setShowWithoutTransition] =
    useState(false);
  return (
    <>
      <button onClick={() => setShowWithTransition(true)}>
        Show profile with View Transition
      </button>
      {showWithTransition && (
        <ViewTransition>
          <Suspense fallback={<ProfilePlaceholder />}>
            <Profile cacheKey="with-transition" />
          </Suspense>
        </ViewTransition>
      )}
      <hr />
      <button onClick={() => setShowWithoutTransition(true)}>
        Show profile without View Transition
      </button>
      {showWithoutTransition && (
        <Suspense fallback={<ProfilePlaceholder />}>
          <Profile cacheKey="without-transition" />
        </Suspense>
      )}
    </>
  );
}
```

```js src/image.js hidden
// Normally, the caching logic would be inside a framework.
const cache = new Map();

export function fetchImageSrc(cacheKey) {
  if (!cache.has(cacheKey)) {
    cache.set(cacheKey, loadImageSrc());
  }
  return cache.get(cacheKey);
}

async function loadImageSrc() {
  // Delay the response so the Suspense fallback is visible.
  await new Promise(resolve => setTimeout(resolve, 1000));
  // Add a unique parameter so the image isn't cached.
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
    "react": "19.3.0",
    "react-dom": "19.3.0",
    "react-scripts": "latest"
  }
}
```

</Sandpack>

---

### ফন্ট, ছবি ও stylesheet-এর লোডিং সমন্বয় করা {/*coordinating-fonts-images-and-stylesheets*/}

একটি Suspense বাউন্ডারি একইসাথে ডেটা, stylesheet, ফন্ট ও ছবির জন্য অপেক্ষা করতে পারে। ফন্ট ও ছবির জন্য অপেক্ষা করা হয় শুধু [`<ViewTransition>`](/reference/react/ViewTransition) আপডেটের সময়। নিচের উদাহরণে, ডেটা লোড হওয়ার সময় `ProfileCard` কম্পোনেন্ট suspend করে। এটি `precedence`-সহ একটি stylesheet, নতুন ফন্টের টেক্সট এবং একটি প্রতিকৃতি রেন্ডার করে। ডেটা ও stylesheet লোড হওয়ার সময় React skeleton দেখিয়ে রাখে। তারপর `<ViewTransition>` দিয়ে কনটেন্ট প্রকাশের আগে ফন্ট ও ছবির জন্য অপেক্ষা করা হয়, যাতে কার্ডটি সম্পূর্ণ অবস্থায় দেখা যায়।

তুলনার জন্য, React ছাড়া সংস্করণটি একই ডেটা লোড করে এবং প্রতিটি resource যখন পৌঁছায় তখনই দেখায়:

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
        <img src={resources.image} alt="" width={80} height={80} />
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
    const doc = ref.current.contentWindow.document;
    doc.open();
    doc.write(`
      <style>
        body { margin: 0; font-family: sans-serif; }
        .profile-card { display: flex; gap: 12px; align-items: center; }
        .profile-card img { border-radius: 50%; background: #dfe3e9; }
        .name { margin: 0 0 4px; font-family: 'Caveat', sans-serif; font-size: 22px; line-height: 28px; font-weight: bold; }
        .bio { margin: 0; font-family: 'Caveat', sans-serif; font-size: 20px; line-height: 26px; }
        .avatar-placeholder { width: 80px; height: 80px; border-radius: 50%; background: #dfe3e9; }
        .name-placeholder, .bio-placeholder { border-radius: 4px; background: #dfe3e9; color: transparent; }
        .name-placeholder { width: 90px; }
        .bio-placeholder { width: 220px; height: 52px; }
      </style>
      <div class="profile-card">
        <div class="avatar-placeholder"></div>
        <div>
          <p class="name name-placeholder">&nbsp;</p>
          <p class="bio bio-placeholder">&nbsp;</p>
        </div>
      </div>
    `);
    doc.close();

    const quote = await fetchQuote();
    doc.body.innerHTML = `
      <div class="profile-card">
        <img src="${freshImageUrl()}" alt="" width="80" height="80" />
        <div>
          <p class="name">Jack Pope</p>
          <p class="bio">${quote}</p>
        </div>
      </div>`;
    const stylesheet = doc.createElement('link');
    stylesheet.rel = 'stylesheet';
    stylesheet.href = freshStylesheetUrl();
    doc.head.appendChild(stylesheet);
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
  height: 52px;
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
    "react": "19.3.0",
    "react-dom": "19.3.0",
    "react-scripts": "latest"
  }
}
```

</Sandpack>

---

## সমস্যার সমাধান করা {/*troubleshooting*/}

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
