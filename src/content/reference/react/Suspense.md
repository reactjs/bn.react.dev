---
title: <Suspense>
---

<Intro>

`<Suspense>` তার children এর লোডিং শেষ না হওয়া পর্যন্ত একটি fallback প্রদর্শন করে। 


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
* `children`: আপনি যেই প্রকৃত UI রেন্ডার করতে চাইছেন। `children` যদি রেন্ডারিং এর সময় থেমে যায় তাহলে Suspense boundary `fallback` রেন্ডার করবে।
* `fallback`: এটি হচ্ছে, প্রকৃত UI পুরোপুরি লোড না হলে তার স্থলে রেন্ডার হওয়া একটি বিকল্প UI। যেকোনো বৈধ React নোডই গৃহীত হয়, যদিও কার্যত, একটি ফলব্যাক হলো একটি হালকা placeholder view, যেমন একটি লোডিং স্পিনার বা স্কেলেটন। Suspense স্বয়ংক্রিয় ভাবে `fallback` এ পরিবর্তিত হয়ে যাবে যখন `children` থেমে যাবে এবং আবার `children` এ ফিরে আসবে যখন ডেটা তৈরি হয়ে যাবে। রেন্ডারিং এর সময় যদি `fallback` থেমে যায়, এটি তার নিকটতম parent Suspense boundary সক্রিয় করবে। 

#### সতর্কতা {/*caveats*/}

- React প্রথমবার মাউন্ট হওয়ার আগে থেমে যাওয়া রেন্ডারগুলির জন্য কোনও state সংরক্ষণ করে না। কম্পোনেন্ট লোড হলে, React থেমে যাওয়া ট্রি পুনরায় শূন্য থেকে রেন্ডার করার চেষ্টা করবে।
- যদি Suspense ট্রির জন্য কন্টেন্ট দেখানো হয়, কিন্তু পরে আবার থেমে যায়, তবে `fallback` আবার দেখানো হবে যদিনা এর হালনাগাদের কারণ [`startTransition`](/reference/react/startTransition) বা [`useDeferredValue`](/reference/react/useDeferredValue) দ্বারা সৃষ্টি হয়ে থাকে।  
- যদি React পুনরায় থেমে যাওার কারণে আগে দেখানো কন্টেন্ট লুকানোর প্রয়োজন হয়, তবে এটি কন্টেন্ট ট্রির [layout Effects](/reference/react/useLayoutEffect) পরিষ্কার করবে। কন্টেন্ট যখন আবার দেখানোর জন্য প্রস্তুত হয়ে যায়, রিয়েক্ট আবার layout Effects চালাবে। এটি নিশ্চিত করে যে, DOM layout পরিমাপ করা ইফেক্টগুলো কন্টেন্ট লুকায়িত অবস্থায় এটা করার চেষ্টা করে না। 
- React এ রয়েছে *Streaming Server Rendering* এবং *Selective Hydration* এর মত অন্তর্নিহিত অপটিমাইজেশন যা Suspense এর সাথে সমন্বিত। আরো জানার জন্য পড়ুন [একটি স্থাপত্য পরিদর্শন](https://github.com/reactwg/react-18/discussions/37) এবং দেখুন [একটি প্রযুক্তিগত আলোচনা](https://www.youtube.com/watch?v=pj5N-Khihgc)

---

## ব্যবহারবিধি {/*usage*/}

### কন্টেন্ট লোড হওয়ার সময় একটি ফলব্যাক দেখানো {/*displaying-a-fallback-while-content-is-loading*/}

আপনি আপনার অ্যাপ্লিকেশনের যে কোন অংশকে একটি Suspense boundary দ্বারা আবদ্ধ করতে পারেনঃ

```js [[1, 1, "<Loading />"], [2, 2, "<Albums />"]]
<Suspense fallback={<Loading />}>
  <Albums />
</Suspense>
```

<CodeStep step={2}>Children</CodeStep> এর প্রয়োজনীয় সমস্ত কোড এবং ডেটা লোড না হওয়া পর্যন্ত React আপনার <CodeStep step={1}>লোডিং ফলব্যাক</CodeStep> দেখাবে।

নিচের উদাহরণে, `Albums` কম্পোনেন্ট একটি অ্যালবাম তালিকা নিয়ে আসার সময় *থেমে* যায়। রেন্ডারের জন্য প্রস্তুত হওয়া পর্যন্ত, React নিকটবর্তী Suspense boundary ব্যবহার করে ফলব্যাক হিসেবে আপনার লোডিং কম্পোনেন্ট দেখায়। তারপরে, ডেটা লোড হলে, React লোডিং ফলব্যাক লুকিয়ে দেয় এবং ডেটা সহ `Albums` কম্পোনেন্টটি রেন্ডার করে।

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js App.js hidden
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

```js ArtistPage.js active
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

```js Albums.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

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

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js data.js hidden
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

**শুধুমাত্র Suspense-enabled ডেটা উৎসগুলো Suspense কম্পোনেন্টকে সক্রিয় করতে পারবে।** এদের মধ্যে রয়েছেঃ
 
- Suspense-enabled ফ্রেমওয়ার্ক দ্বারা ডেটা নিয়ে আসা যেমন, [Relay](https://relay.dev/docs/guided-tour/rendering/loading-states/) এবং [Next.js](https://nextjs.org/docs/advanced-features/react-18)
- [`lazy`](/reference/react/lazy) এর সাথে Lazy-loading কম্পোনেন্ট কোড 

Suspense বুঝতে **পারে না** যখন কোনো Effect বা ইভেন্ট হ্যান্ডলারের ভিতরে ডেটা নিয়ে আসা হয়।

উপরের `Albums` কম্পোনেন্টে আপনি যেভাবে ডেটা লোড করবেন তা আপনার ফ্রেমওয়ার্ক এর উপর নির্ভর করে। আপনি যদি Suspense-enabled ফ্রেমওয়ার্ক ব্যাবহার করে থাকেন তাহলে আপনি এর ডেটা নিয়ে আসার ডকুমেন্টেশনে বিস্তারিত তথ্য পাবেন। 

মতানুযায়ী নির্ধারিত ফ্রেমওয়ার্ক ছাড়া Suspense-enabled ডেটা নিয়ে আসা এখনো সমর্থিত নয়। Suspense-enabled ডেটা উৎস সংযোজনের জন্য প্রয়োজনীয় বিবরণগুলি অস্থিতিশীল এবং নথিভুক্ত নয়। Suspense দিয়ে ডেটা উৎসগুলোর সংযোজনের জন্য একটি অফিসিয়াল API পরবর্তী React সংস্করণে প্রকাশিত হবে। 

</Note>

---

### সামগ্রিকভাবে একসঙ্গে কন্টেন্ট প্রকাশ করা {/*revealing-content-together-at-once*/}

স্বাভাবিকভাবে, Suspense এর ভিতরে অবস্থিত সমস্ত ট্রি একটি একক ইউনিট হিসাবে বিবেচিত হয়। উদাহরণস্বরূপ, যদিও এই কম্পোনেন্টগুলোর মধ্যে *কেবল একটিই* কিছু ডেটার জন্য অপেক্ষায় থাকে, *সবগুলো* কম্পোনেন্ট একসঙ্গে লোডিং ইন্ডিকেটর দ্বারা প্রতিস্থাপিত হবেঃ

```js {2-5}
<Suspense fallback={<Loading />}>
  <Biography />
  <Panel>
    <Albums />
  </Panel>
</Suspense>
```

তারপরে, সবগুলো প্রদর্শনের জন্য প্রস্তুত হয়ে গেলে, তাদের সবাইকে একসাথে দেখা যাবে। 

নিচের উদাহরণে, `Biography` এবং `Albums` উভয়ই কিছু ডেটা নিয়ে আসে। তবে, তারা একটি একক Suspense boundary এর মধ্যে গ্রুপ করে থাকায় সর্বদা এই কম্পোনেন্টগুলি একই সময়ে "পপ ইন" করে। 

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js App.js hidden
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

```js ArtistPage.js active
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

```js Panel.js
export default function Panel({ children }) {
  return (
    <section className="panel">
      {children}
    </section>
  );
}
```

```js Biography.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

export default function Biography({ artistId }) {
  const bio = use(fetchData(`/${artistId}/bio`));
  return (
    <section>
      <p className="bio">{bio}</p>
    </section>
  );
}

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js Albums.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

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

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js data.js hidden
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

যে কম্পোনেন্টগুলো ডেটা লোড করে তাদেরকে Suspense boundary এর direct children হতে হবে এমনটি নয়। উদাহরণস্বরূপ, আপনি `Biography` এবং `Albums` কে একটি নতুন `Details` কম্পোনেন্টে সরিয়ে নিতে পারেন। এটা আচরণের পরিবর্তন করে না। `Biography` এবং `Albums` একই নিকটতম parent Suspense boundary সংযোজিত, তাই তাদের প্রদর্শন একসঙ্গে সমন্বিত হয়।

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

### নেস্টেড কন্টেন্ট লোড হওয়ার সাথে সাথে প্রকাশ করা {/*revealing-nested-content-as-it-loads*/}

যখন একটি কম্পোনেন্ট থেমে যায়, তখন নিকটতম parent Suspense কম্পোনেন্ট ফলব্যাক প্রদর্শন করে। এর মাধ্যমে আপনি একাধিক Suspense কম্পোনেন্ট দ্বারা একটি লোডিং সিকোয়েন্স তৈরি করতে পারেন। প্রতিটি Suspense boundary এর ফলব্যাক পূর্ণ হবে যদি পরবর্তী স্তরের কন্টেন্ট থাকে। উদাহরণস্বরূপ, আপনি অ্যালবাম তালিকায় এর নিজস্ব ফলব্যাক দিতে পারেনঃ

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

এই পরিবর্তনের ফলে `Biography` প্রদর্শনে `Albums` এর লোডের জন্য অপেক্ষা করার প্রয়োজন নেই। 

ক্রমটি হবে এরকমঃ

১। যদি `Biography` এখনো লোড না হয়ে থাকে, কন্টেন্ট এর সম্পূর্ণ জায়গাজুড়ে `BigSpinner` দেখানো হলো। 
২। `Biography` লোড করা শেষ হয়ে গেলে `BigSpinner` দ্বারা কন্টেন্ট প্রতিস্থাপিত হয়ে গেল। 
৩। যদি এখনো `Albums` লোড না হয়ে থাকে, `AlbumsGlimmer` এর স্থানে `Albums` এবং এর প্যারেন্ট `Panel` দেখানো হলো। 
৪। সব শেষে, `Albums` এর লোডিং শেষ হলে এটি `AlbumsGlimmer` কে প্রতিস্থাপন করে। 

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js App.js hidden
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

```js ArtistPage.js active
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

```js Panel.js
export default function Panel({ children }) {
  return (
    <section className="panel">
      {children}
    </section>
  );
}
```

```js Biography.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

export default function Biography({ artistId }) {
  const bio = use(fetchData(`/${artistId}/bio`));
  return (
    <section>
      <p className="bio">{bio}</p>
    </section>
  );
}

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js Albums.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

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

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js data.js hidden
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

Suspense boundary এর মাধ্যমে আপনি আপনার UI এর কোন অংশগুলি সর্বদা একই সময়ে "পপ ইন" করা হবে এবং কোন অংশগুলি লোডিং স্টেটের একটি অনুক্রমিক সিকোয়েন্সে আরো কন্টেন্ট প্রকাশ করবে তা সমন্বিত করতে পারেন। আপনি ট্রির যে কোন জায়গায় Suspense boundaries যোগ করতে পারেন, সরাতে পারেন বা মুছতে পারেন কিন্তু অ্যাপ্লিকেশনের অন্যান্য অংশের আচরণের উপর কোন প্রভাব পড়বে না। 

প্রত্যেকটি কম্পোনেন্টের সাথে একটি করে Suspense boundary যোগ করবেন না। Suspense boundary গুলো লোডিং ক্রমের চেয়ে ক্ষুদ্র হওয়া উচিৎ নয় যা ব্যহারকারিকে দেখাবেন। যদি আপনি একজন ডিজাইনার সঙ্গে কাজ করেন, তবে তাদেরকে জিজ্ঞাসা করুন যে লোডিং স্টেটগুলি কোথায় রাখা উচিত - সম্ভবতই তারা আগেই তা তাদের ডিজাইন ওয়ায়ারফ্রেমগুলোতে অন্তর্ভুক্ত করেছেন।

---

### নতুন কন্টেন লোড হতে হতে পুরাতন কন্টেন্ট দেখানো {/*showing-stale-content-while-fresh-content-is-loading*/}


এই উদাহরণে `SearchResults` কম্পোনেন্টটি সার্চের ফলাফল নিয়ে আসার সময় থেমে যায়। `"a"` টাইপ করে অপেক্ষা করুন, তারপর এটিকে এডিট করে `"ab"` করুন। `"a"` এর ফলাফলগুলো লোডিং ফলব্যাক দ্বারা প্রতিস্থাপিত হয়ে যাবে।


<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js App.js
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

```js SearchResults.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

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

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js data.js hidden
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

একটি প্রচলিত বিকল্প UI প্যাটার্ন হলো তালিকা হালনাগাদকে *বিলম্বিত করা(defer)* এবং নতুন ফলাফল তৈরি হওয়া পর্যন্ত পূর্ববর্তী ফলাফলগুলো দেখানো।
[`useDeferredValue`](/reference/react/useDeferredValue) হুকটি আপনাকে query করার একটি deferred ভার্সন পাঠানোর সুবিধা দেয়ঃ

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

`query` টি তাৎক্ষণিকভাবে হালনাগাদ হবে, তাই ইনপুটটি নতুন মানটি প্রদর্শন করবে। তবে, `deferredQuery` ডেটা লোড হওয়া অবধি তার পূর্ববর্তী মানটি রেখে দেবে, তাই `SearchResults` কিছুক্ষণের জন্য জীর্ণ ফলাফল দেখাবে।

ব্যবহারকারী্র কাছে এটিকে আরও স্পষ্ট করতে, যখন পুরাতন ফলাফল তালিকা প্রদর্শিত হচ্ছে তখন একটি ভিজুয়াল ইন্ডিকেশন যোগ করতে পারেনঃ

```js {2}
<div style={{
  opacity: query !== deferredQuery ? 0.5 : 1 
}}>
  <SearchResults query={deferredQuery} />
</div>
```

নিচের উদাহরণে `"a"` ইনপুট দিন, ফলাফল লোড হওয়ার জন্য অপেক্ষা করুন এবং তারপর ইনপুটটি এডিট করে `"ab"` করুন। লক্ষ্য করুন যে নতুন ফলাফলগুলি লোড না হওয়া পর্যন্ত Suspense fallback এর পরিবর্তে আপনি এখন পুরাতন ফলাফল তালিকাটি কিছুটা অস্পষ্ট ভাবে দেখতে পাচ্ছেনঃ


<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js App.js
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

```js SearchResults.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

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

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js data.js hidden
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

Deferred values এবং [transitions](#preventing-already-revealed-content-from-hiding) উভয়ের দ্বারাই আপনি ইনলাইন ইন্ডিকেটর এর পক্ষে Suspense fallback দেখানো থেকে বিরত থাকতে পারেন। Transitions সম্পূর্ণ হালনাগাদটিকে গুরুত্বহীন হিসেবে ধরে নেয় তাই এগুলো সাধারণত ফ্রেমওয়ার্ক এবং রাউটার লাইব্রেরীগুলিতে নেভিগেশনের জন্য ব্যবহৃত হয়। অন্যদিকে, deferred values সাধারণত অ্যাপ্লিকেশন কোডে ব্যবহারকারী ইন্টারফেসের জন্য বেশি উপযোগী যেখানে আপনি UI এর একটি অংশকে গুরুত্বহীন হিসেবে চিহ্নিত করে দিতে পারেন এবং এটিকে অন্যান্য UI এর "lag behind" হিসেবে রেখে দিতে পারেন।

</Note>

---

### Preventing already revealed content from hiding {/*preventing-already-revealed-content-from-hiding*/}

When a component suspends, the closest parent Suspense boundary switches to showing the fallback. This can lead to a jarring user experience if it was already displaying some content. Try pressing this button:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js App.js
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

```js Layout.js
export default function Layout({ children }) {
  return (
    <div className="layout">
      <section className="header">
        Music Browser
      </section>
      <main>
        {children}
      </main>
    </div>
  );
}
```

```js IndexPage.js
export default function IndexPage({ navigate }) {
  return (
    <button onClick={() => navigate('/the-beatles')}>
      Open The Beatles artist page
    </button>
  );
}
```

```js ArtistPage.js
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

```js Albums.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

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

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js Biography.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

export default function Biography({ artistId }) {
  const bio = use(fetchData(`/${artistId}/bio`));
  return (
    <section>
      <p className="bio">{bio}</p>
    </section>
  );
}

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js Panel.js hidden
export default function Panel({ children }) {
  return (
    <section className="panel">
      {children}
    </section>
  );
}
```

```js data.js hidden
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

When you pressed the button, the `Router` component rendered `ArtistPage` instead of `IndexPage`. A component inside `ArtistPage` suspended, so the closest Suspense boundary started showing the fallback. The closest Suspense boundary was near the root, so the whole site layout got replaced by `BigSpinner`.

To prevent this, you can mark the navigation state update as a *transition* with [`startTransition`:](/reference/react/startTransition)

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

This tells React that the state transition is not urgent, and it's better to keep showing the previous page instead of hiding any already revealed content. Now clicking the button "waits" for the `Biography` to load:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js App.js
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

```js Layout.js
export default function Layout({ children }) {
  return (
    <div className="layout">
      <section className="header">
        Music Browser
      </section>
      <main>
        {children}
      </main>
    </div>
  );
}
```

```js IndexPage.js
export default function IndexPage({ navigate }) {
  return (
    <button onClick={() => navigate('/the-beatles')}>
      Open The Beatles artist page
    </button>
  );
}
```

```js ArtistPage.js
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

```js Albums.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

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

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js Biography.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

export default function Biography({ artistId }) {
  const bio = use(fetchData(`/${artistId}/bio`));
  return (
    <section>
      <p className="bio">{bio}</p>
    </section>
  );
}

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js Panel.js hidden
export default function Panel({ children }) {
  return (
    <section className="panel">
      {children}
    </section>
  );
}
```

```js data.js hidden
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

A transition doesn't wait for *all* content to load. It only waits long enough to avoid hiding already revealed content. For example, the website `Layout` was already revealed, so it would be bad to hide it behind a loading spinner. However, the nested `Suspense` boundary around `Albums` is new, so the transition doesn't wait for it.

<Note>

Suspense-enabled routers are expected to wrap the navigation updates into transitions by default.

</Note>

---

### Indicating that a transition is happening {/*indicating-that-a-transition-is-happening*/}

In the above example, once you click the button, there is no visual indication that a navigation is in progress. To add an indicator, you can replace [`startTransition`](/reference/react/startTransition) with [`useTransition`](/reference/react/useTransition) which gives you a boolean `isPending` value. In the example below, it's used to change the website header styling while a transition is happening:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "experimental",
    "react-dom": "experimental"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js App.js
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

```js Layout.js
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

```js IndexPage.js
export default function IndexPage({ navigate }) {
  return (
    <button onClick={() => navigate('/the-beatles')}>
      Open The Beatles artist page
    </button>
  );
}
```

```js ArtistPage.js
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

```js Albums.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

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

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js Biography.js hidden
import { fetchData } from './data.js';

// Note: this component is written using an experimental API
// that's not yet available in stable versions of React.

// For a realistic example you can follow today, try a framework
// that's integrated with Suspense, like Relay or Next.js.

export default function Biography({ artistId }) {
  const bio = use(fetchData(`/${artistId}/bio`));
  return (
    <section>
      <p className="bio">{bio}</p>
    </section>
  );
}

// This is a workaround for a bug to get the demo running.
// TODO: replace with real implementation when the bug is fixed.
function use(promise) {
  if (promise.status === 'fulfilled') {
    return promise.value;
  } else if (promise.status === 'rejected') {
    throw promise.reason;
  } else if (promise.status === 'pending') {
    throw promise;
  } else {
    promise.status = 'pending';
    promise.then(
      result => {
        promise.status = 'fulfilled';
        promise.value = result;
      },
      reason => {
        promise.status = 'rejected';
        promise.reason = reason;
      },      
    );
    throw promise;
  }
}
```

```js Panel.js hidden
export default function Panel({ children }) {
  return (
    <section className="panel">
      {children}
    </section>
  );
}
```

```js data.js hidden
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

### Resetting Suspense boundaries on navigation {/*resetting-suspense-boundaries-on-navigation*/}

During a transition, React will avoid hiding already revealed content. However, if you navigate to a route with different parameters, you might want to tell React it is *different* content. You can express this with a `key`:

```js
<ProfilePage key={queryParams.id} />
```

Imagine you're navigating within a user's profile page, and something suspends. If that update is wrapped in a transition, it will not trigger the fallback for already visible content. That's the expected behavior.

However, now imagine you're navigating between two different user profiles. In that case, it makes sense to show the fallback. For example, one user's timeline is *different content* from another user's timeline. By specifying a `key`, you ensure that React treats different users' profiles as different components, and resets the Suspense boundaries during navigation. Suspense-integrated routers should do this automatically.

---

### Providing a fallback for server errors and server-only content {/*providing-a-fallback-for-server-errors-and-server-only-content*/}

If you use one of the [streaming server rendering APIs](/reference/react-dom/server) (or a framework that relies on them), React will also use your `<Suspense>` boundaries to handle errors on the server. If a component throws an error on the server, React will not abort the server render. Instead, it will find the closest `<Suspense>` component above it and include its fallback (such as a spinner) into the generated server HTML. The user will see a spinner at first.

On the client, React will attempt to render the same component again. If it errors on the client too, React will throw the error and display the closest [error boundary.](/reference/react/Component#static-getderivedstatefromerror) However, if it does not error on the client, React will not display the error to the user since the content was eventually displayed successfully.

You can use this to opt out some components from rendering on the server. To do this, throw an error in the server environment and then wrap them in a `<Suspense>` boundary to replace their HTML with fallbacks:

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

The server HTML will include the loading indicator. It will be replaced by the `Chat` component on the client.

---

## Troubleshooting {/*troubleshooting*/}

### How do I prevent the UI from being replaced by a fallback during an update? {/*preventing-unwanted-fallbacks*/}

Replacing visible UI with a fallback creates a jarring user experience. This can happen when an update causes a component to suspend, and the nearest Suspense boundary is already showing content to the user.

To prevent this from happening, [mark the update as non-urgent using `startTransition`](#preventing-already-revealed-content-from-hiding). During a transition, React will wait until enough data has loaded to prevent an unwanted fallback from appearing:

```js {2-3,5}
function handleNextPageClick() {
  // If this update suspends, don't hide the already displayed content
  startTransition(() => {
    setCurrentPage(currentPage + 1);
  });
}
```

This will avoid hiding existing content. However, any newly rendered `Suspense` boundaries will still immediately display fallbacks to avoid blocking the UI and let the user see the content as it becomes available.

**React will only prevent unwanted fallbacks during non-urgent updates**. It will not delay a render if it's the result of an urgent update. You must opt in with an API like [`startTransition`](/reference/react/startTransition) or [`useDeferredValue`](/reference/react/useDeferredValue).

If your router is integrated with Suspense, it should wrap its updates into [`startTransition`](/reference/react/startTransition) automatically.
