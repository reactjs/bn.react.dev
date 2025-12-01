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

#### সতর্কতা {/*caveats*/}

- React প্রথমবার মাউন্ট হওয়ার আগে থেমে যাওয়া রেন্ডারগুলোর জন্য কোনও state সংরক্ষণ করে না। কম্পোনেন্ট লোড হলে, React থেমে যাওয়া ট্রি পুনরায় শূন্য থেকে রেন্ডার করার চেষ্টা করবে।
- Suspense যদি ট্রির জন্য কন্টেন্ট দেখাতে থাকে, কিন্তু পরে আবার থেমে যায়, তবে `fallback` আবার দেখানো হবে যদিনা এর আপডেটের কারণ [`startTransition`](/reference/react/startTransition) বা [`useDeferredValue`](/reference/react/useDeferredValue) দ্বারা সৃষ্টি হয়ে থাকে।  
- যদি React পুনরায় থেমে যাওয়ার কারণে আগে দেখানো কন্টেন্ট লুকানোর প্রয়োজন হয়, তবে এটি কন্টেন্ট ট্রির [layout Effects](/reference/react/useLayoutEffect) কে পরিষ্কার করবে। কন্টেন্ট যখন আবার দেখানোর জন্য প্রস্তুত হয়ে যায়, রিয়েক্ট আবার layout Effects চালাবে। এটি নিশ্চিত করে যে, DOM layout পরিমাপ করা ইফেক্টগুলো কন্টেন্ট লুকায়িত অবস্থায় এটা করার চেষ্টা করে না। 
- React এ রয়েছে *Streaming Server Rendering* এবং *Selective Hydration* এর মত অন্তর্নিহিত অপটিমাইজেশন যা Suspense এর সাথে সমন্বিত। আরো জানার জন্য পড়ুন [একটি গঠনমূলক সারমর্ম](https://github.com/reactwg/react-18/discussions/37) এবং দেখুন [একটি টেকনিকাল আলোচনা](https://www.youtube.com/watch?v=pj5N-Khihgc)

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

<Note>

**শুধুমাত্র Suspense-enabled ডেটা উৎসগুলো Suspense কম্পোনেন্টকে সক্রিয় করতে পারবে।** এদের মধ্যে রয়েছেঃ
 
- Suspense-enabled ফ্রেমওয়ার্কের সাহায্যে ডেটা নিয়ে আসা যেমন, [Relay](https://relay.dev/docs/guided-tour/rendering/loading-states/) এবং [Next.js](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming#streaming-with-suspense)
- [`lazy`](/reference/react/lazy) দিয়ে lazy-loading component code
- [`use`](/reference/react/use) ব্যবহার করে একটি cached Promise এর মান read করা

উপরের `Albums` কম্পোনেন্টে আপনি ঠিক কিভাবে ডেটা লোড করবেন তা আপনার ফ্রেমওয়ার্ক এর উপর নির্ভর করে। আপনি যদি Suspense-enabled ফ্রেমওয়ার্ক ব্যাবহার করেন তাহলে আপনি এর ডেটা নিয়ে আসার ডকুমেন্টেশনে বিস্তারিত তথ্য পাবেন। 

মতানুযায়ী নির্ধারিত ফ্রেমওয়ার্ক ছাড়া Suspense-enabled ডেটা নিয়ে আসা এখনো সমর্থিত নয়। Suspense-enabled ডেটা উৎস সংযোজনের জন্য প্রয়োজনীয় বিবরণগুলো অস্থিতিশীল এবং নথিভুক্ত নয়। Suspense দিয়ে ডেটা উৎসগুলোর সংযোজনের জন্য একটি অফিসিয়াল API পরবর্তী React সংস্করণে প্রকাশিত হবে। 

</Note>

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

একটি ট্রান্সিশনের সময় React ইতিমধ্যে প্রকাশিত কন্টেন্ট লুকানো থেকে বিরত থাকবে। তবে, আপনি যদি ভিন্ন কোনো প্যরামিটার বিশিষ্ট রাউটে নেভিগেট করেন, আপনি হয়ত React কে বলতে চান যে এটি *ভিন্ন* কন্টেন্ট। আপনি এটি একটি `key` দিয়ে বুঝাতে পারেনঃ

```js
<ProfilePage key={queryParams.id} />
```

ধরুন, আপনি একজন ব্যবহারকারীর প্রোফাইল পেজের মধ্যেই নেভিগেট করছেন এবং কিছু একটা থেমে গেল। যদি সেই আপডেটটি একটি ট্রান্সিশনের মধ্যে থাকে, তবে এটি ইতিমধ্যেই প্রকাশিত কন্টেন্টের জন্য fallback কে ট্রিগার করবে না। এরকমটাই প্রত্যাশিত।

যাইহোক, এখন আবার মনে করুন আপনি দুটি ভিন্ন ব্যবহারকারীর প্রোফাইলের মধ্যে নেভিগেট করছেন। এই ক্ষেত্রে fallback টি দেখানো যুক্তিসই। উদাহরণস্বরূপ, একজন ব্যবহারকারীর টাইমলাই আরেকজন ব্যবহারকারীর টাইমলাইন থেকে *আলাদা কন্টেন্ট*। একটি `key` নির্দিষ্ট করে দেয়ার মাধ্যমে আপনি নিশ্চিত করেন যে, React ভিন্ন ভিন্ন ব্যবহারকারীর প্রোফাইলকে ভিন্ন ভিন্ন কম্পোনেন্ট হিসাবে বিবেচনা করে এবং নেভিগেশনের সময় Suspense boundary গুলো রিসেট করে। Suspense-integrated রাউটারগুলো এটি স্বয়ংক্রিয়ভাবেই করবে।

---

### Server errors এবং client-only কন্টেন্টের জন্য একটি fallback দেওয়া {/*providing-a-fallback-for-server-errors-and-server-only-content*/}

আপনি যদি [streaming server rendering APIs](/reference/react-dom/server) (অথবা এদের উপর নির্ভরকারী একটি ফ্রেমওয়ার্ক) এর কোনোটি ব্যবহার করেন, React আপনার `<Suspense>` boundary গুলোও ব্যবহার করবে সার্ভার এর এরর গুলো তত্ত্বাবধান করতে। যদি কোনো কম্পোনেন্ট সার্ভারে এরর থ্রো করে, React সার্ভার রেন্ডার বন্ধ করবে না। বরং, এর সবচেয়ে কাছের `<Suspense>` কম্পোনেন্টটি খুঁজে বের করবে এবং তার fallback (যেমন একটি স্পিনার), তৈরি করা সার্ভার HTML এর মধ্যে যোগ করবে। ব্যবহারকারী প্রথমে একটি স্পিনার দেখবে।

<<<<<<< HEAD
ক্লায়েন্টে, একই কম্পোনেন্টটি React আবার রেন্ডার করার চেষ্টা করবে। যদি ক্লায়েন্টেও এরর হয়, React এররটি থ্রো করবে এবং সবচেয়ে কাছের [error boundary](/reference/react/Component#static-getderivedstatefromerror) টি দেখাবে। তবে, যদি ক্লায়েন্টে এরর না হয়, React এরর টি ক্লায়েন্টে দেখাবে না যেহেতু কন্টেন্টটি শেষ পর্যন্ত সফলভাবে প্রদর্শিত হয়েছিল।
=======
On the client, React will attempt to render the same component again. If it errors on the client too, React will throw the error and display the closest [Error Boundary.](/reference/react/Component#static-getderivedstatefromerror) However, if it does not error on the client, React will not display the error to the user since the content was eventually displayed successfully.
>>>>>>> 2534424ec6c433cc2c811d5a0bd5a65b75efa5f0

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
