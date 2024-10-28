---
title: 'টিউটোরিয়াল: Tic-Tac-Toe'
---

<Intro>

আপনি এই টিউটোরিয়ালে একটি ছোট টিক-ট্যাক-টো গেম তৈরি করবেন। এই টিউটোরিয়ালটিতে কোনও পূর্ববর্তী রিয়েক্ট জ্ঞানের প্রয়োজনীয়তা নেই। আপনি যে প্রযুক্তিগুলি এই টিউটোরিয়ালে শিখবেন তা যেকোন রিয়েক্ট অ্যাপ তৈরি করার জন্য মৌলিক জ্ঞান, এবং এটি সম্পূর্ণভাবে বুঝতে পারলে এটি আপনাকে রিয়েক্ট সম্পর্কে একটি গভীর ধারণা দেবে।

</Intro>

<Note>

এই টিউটোরিয়ালটি তাদের জন্য ডিজাইন করা হয়েছে যারা কাজ করে শিখতে পছন্দ করেন এবং দ্রুত কিছু বাস্তবধর্মী কিছু তৈরি করার চেষ্টা করতে চান। যদি আপনি ধাপে ধাপে প্রতিটি ধারণা শেখাকে অগ্রাধিকার দিতে চান, তবে [Describing the UI](/learn/describing-the-ui) দিয়ে শুরু করুন।

</Note>

এই টিউটোরিয়ালটি কয়েকটি বিভাগে বিভক্ত:

- [Setup for the tutorial](#setup-for-the-tutorial) আপনাকে একেবারে গোড়া থেকে শুরু করাবে, যাতে আপনি টিউটোরিয়ালটি অনুসরণ করতে পারেন।
- [Overview](#overview) **রিয়েক্টের মৌলিক ধারণাগুলি** শেখাবে:যেমন ধরুন- কম্পোনেন্ট, প্রপস এবং স্টেট।
- [Completing the game](#completing-the-game) আপনাকে **রিয়েক্ট ডেভেলপমেন্টে সবচেয়ে প্রচলিত কৌশলগুলি** শেখাবে।
- [Adding time travel](#adding-time-travel) আপনাকে রিয়েক্টের অনন্য শক্তিগুলি সম্পর্কে **একটি গভীর পরিজ্ঞান** দেবে।

### আপনি কি তৈরি করছেন? {/*what-are-you-building*/}

এই টিউটোরিয়ালে, আপনি রিয়েক্টের সাথে একটি ইন্টারঅ্যাকটিভ টিক-ট্যাক-টো গেম তৈরি করবেন।
আপনি এখানে দেখতে পারেন এটি আপনার শেষ হওয়ার পর কেমন দেখতে হবে:

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const xIsNext = currentMove % 2 === 0;
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li key={move}>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

যদি কোডটি এখনও আপনার কাছে বোঝা না যায়, বা কোডের সিনট্যাক্সের সাথে আপনি পরিচিত না হন, তবে চিন্তা করবেন না! এই টিউটোরিয়ালের লক্ষ্য হল আপনাকে রিয়েক্ট এবং এর সিনট্যাক্স বুঝতে সাহায্য করা।

আমরা আপনাকে টিউটোরিয়ালটি চালিয়ে যাওয়ার আগে উপরের টিক-ট্যাক-টো গেমটি দেখার পরামর্শ দিচ্ছি। আপনি যে বৈশিষ্ট্যটি লক্ষ্য করবেন তা হল গেমের বোর্ডের ডান দিকে একটি নম্বরযুক্ত তালিকা রয়েছে। এই তালিকাটি গেমে ঘটে যাওয়া সমস্ত ধাপগুলির ইতিবৃত্ত দেখায় এবং এটি গেমের অগ্রগতির সাথে আপডেট হয়।

একবার আপনি সম্পন্ন টিক-ট্যাক-টো গেমটি নিয়ে খেলা শুরু করলে, স্ক্রোল করতে থাকুন। এই টিউটোরিয়ালে আপনি একটি সহজ টেমপ্লেট দিয়ে শুরু করবেন। আমাদের পরবর্তী পদক্ষেপ হল আপনি যাতে গেমটি তৈরি করা শুরু করতে পারেন সেভাবে আপনাকে তৈরি করা।

## টিউটোরিয়ালের জন্য সেটআপ {/*setup-for-the-tutorial*/}

নিচের লাইভ কোড এডিটরটিতে, **Fork** বাটনে ক্লিক করুন, যা উপরের ডান কোণায় রয়েছে, CodeSandbox ওয়েবসাইট ব্যবহার করে নতুন ট্যাবে এডিটরটিতে খুলতে। CodeSandbox আপনাকে আপনার ব্রাউজারে কোড লেখার সুযোগ দেয় এবং আপনি যে অ্যাপটি তৈরি করেছেন তা আপনার ব্যবহারকারীরা কিভাবে দেখতে পাবে তা প্রিভিউ করতে দেয়। নতুন ওপেন হওয়া ট্যাবটি একটি খালি বর্গ এবং এই টিউটোরিয়ালের জন্য শুরু কোড প্রদর্শন করবে।

<Sandpack>

```js src/App.js
export default function Square() {
  return <button className="square">X</button>;
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

<Note>

আপনি আপনার পিসিতে লোকাল ডেভেলপমেন্ট করেও এই টিউটোরিয়ালটি অনুসরণ করতে পারেন। এটি করতে, আপনাকে:

1. [Node.js](https://nodejs.org/en/) ইনস্টল করতে হবে।
2.আপনি আগে খুলেছিলেন CodeSandbox ট্যাবে, উপরের বাম কোণের বোতামে ক্লিক করুন যাতে মেনু খুলে, এবং তারপর সেই মেনুতে **Download Sandbox** নির্বাচন করুন যাতে ফাইলগুলির একটি আর্কাইভ স্থানীয়ভাবে ডাউনলোড করা যায়।
3. আর্কাইভটি আনজিপ করুন, তারপর একটি টার্মিনাল খুলুন এবং আনজিপ করা ডিরেক্টরিতে `cd` করুন।
4. `npm install` দিয়ে ডিপেন্ডেন্সিগুলি ইনস্টল করুন।
5. লোকাল সার্ভার শুরু করতে `npm start` চালান এবং একটি ব্রাউজারে কোডটি চলমান দেখতে প্রম্পট অনুসরণ করুন।

যদি আপনি আটকেও যান, থেমে যাবেন না! পরিবর্তে অনলাইনে অনুসরণ করুন এবং পরে আবার লোকাল সেটআপ করার চেষ্টা করুন।

</Note>

## ওভারভিউ {/*overview*/}

যেহেতু আপনার সেটআপ হয়ে গেছে, চলুন রিয়েক্টের একটি ওভারভিউ দেখা যাক!

### স্টার্টারকোড পর্যবেক্ষন {/*inspecting-the-starter-code*/}

CodeSandbox-এ আপনি তিনটি প্রধান বিভাগ দেখতে পাবেন:

![CodeSandbox with starter code](../images/tutorial/react-starter-code-codesandbox.png)

1. _ফাইল_ সেকশনে `App.js`, `index.js`, `styles.css` এর মতো ফাইলগুলোর একটি তালিকা এবং `public` নামক একটি ফোল্ডার থাকবে।
2. _কোড এডিটর_ যেখানে আপনি আপনার সিলেক্টেড ফাইলের সোর্স কোড দেখতে পাবেন।
3. _ব্রাউজার_ পার্টে আপনি যে কোডটি লিখেছেন তা কিভাবে প্রদর্শিত হবে তা দেখতে পাবেন।

ফাইল সেকশনে `App.js` ফাইলটি নির্বাচিত থাকতে হবে। _কোড এডিটর_ এ সেই ফাইলের বিষয়বস্তু হবে:

```jsx
export default function Square() {
  return <button className="square">X</button>;
}
```

_ব্রাউজার_ সেকশনটি একটি বর্গক্ষেত্রের মধ্যে X চিহ্ন দেখাচ্ছে, এরকম:

![x-filled square](../images/tutorial/x-filled-square.png)

এখন আসুন স্টার্টার কোডের ফাইলগুলোর দিকে নজর দিই।

#### `App.js` {/*appjs*/}

`App.js`-এর কোড একটি _কম্পোনেন্ট_ তৈরি করে। React-এ, একটি কম্পোনেন্ট হলো পুনঃব্যবহারযোগ্য কোডের একটি অংশ, যা একটি ইউজার ইন্টারফেসের একটি অংশকে উপস্থাপন করে। কম্পোনেন্টগুলোকে আপনার অ্যাপ্লিকেশনে UI উপাদানগুলো রেন্ডার, পরিচালনা এবং আপডেট করার জন্য ব্যবহার করা হয়। চলুন লাইন ধরে ধরে দেখি এই কম্পোনেন্টটি কীভাবে কাজ করছে।


```js {1}
export default function Square() {
  return <button className="square">X</button>;
}
```

প্রথম লাইনে `Square` নামের একটি ফাংশন সংজ্ঞায়িত করা হয়েছে। JavaScript এর `export` কিওয়ার্ডটি এই ফাংশনকে এই ফাইলের বাইরে থেকে অ্যাক্সেসযোগ্য করে তোলে। `default` কিওয়ার্ডটি অন্যান্য ফাইলকে জানায় যে এটি আপনার ফাইলে প্রধান ফাংশন:

```js {2}
export default function Square() {
  return <button className="square">X</button>;
}
```

দ্বিতীয় লাইনে একটি বাটন রিটার্ন করে। JavaScript এর `return` কিওয়ার্ডটি নির্দেশ করে যে, পরবর্তীতে যা আসবে তা ফাংশনের কলারের জন্য একটি মান হিসেবে ফেরত দেওয়া হবে। `<button>` একটি _JSX উপাদান_। একটি JSX উপাদান হলো JavaScript কোড এবং HTML ট্যাগের একটি সমন্বয়, যা আপনি যা প্রদর্শন করতে চান তা বর্ণনা করে। `className="square"` হলো একটি বাটন প্রপার্টি বা _prop_ যা CSS-কে বাটনটি কীভাবে স্টাইল করতে হবে তা জানায়। `X` হলো বাটনের ভিতরে প্রদর্শিত টেক্সট এবং `</button>` JSX উপাদানটি বন্ধ করে দেয়, যা নির্দেশ করে যে পরবর্তী কনটেন্টটি বাটনের ভিতরে রাখা উচিত নয়।

#### `styles.css` {/*stylescss*/}

_ফাইল_ সেকশনে `styles.css` নামক ফাইলে ক্লিক করুন। এই ফাইলটি আপনার React অ্যাপের স্টাইলগুলি সংজ্ঞায়িত করে। প্রথম দুটি _CSS সিলেক্টর_ (`*` এবং `body`) আপনার অ্যাপের বড় অংশগুলোর স্টাইল নির্ধারণ করে, `.square` সিলেক্টরটি যেকোনো কম্পোনেন্টের স্টাইল নির্ধারণ করে যেখানে `className` প্রপার্টি `square` সেট করা হয়েছে। আপনার কোডে, এটি `App.js` ফাইলে আপনার Square কম্পোনেন্ট থেকে বাটনের সাথে মিলবে।

#### `index.js` {/*indexjs*/}

_CodeSandbox_এর ফাইল সেকশনে `index.js` নামক ফাইলে ক্লিক করুন। আপনি এই টিউটোরিয়াল চলাকালীন এই ফাইলটি এডিট করবেন না, কিন্তু এটি আপনার তৈরি করা `App.js` ফাইলের কম্পোনেন্ট এবং ওয়েব ব্রাউজারের মধ্যে একটি সেতুর কাজ করে।

```jsx
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';
```

এই লাইনগুলো সব প্রয়োজনীয় অংশকে একত্রে নিয়ে আসেঃ
- React
- ব্রাউজার(React DOM)এর সাথে ইন্টারাকশন এর জন্য React’s library
- কম্পোনেন্ট এর জন্য স্টাইল
- আপ্পনার তৈরি করা এপ কম্পোনেন্ট `App.js`.

ফাইলের বাকি অংশ সব টুকরোগুলোকে একত্রিত করে এবং চূড়ান্ত ফলাফল `public` ফোল্ডারের `index.html`-এ অন্তর্ভুক্ত করে।

### বোর্ড তৈরি করা {/*building-the-board*/}

চলুন আবার `App.js`-এ ফিরে যাই। এখানে আপনি টিউটোরিয়ালটির বাকি সময় ব্যয় করবেন।

বর্তমানে বোর্ডটি কেবল একটি একক বর্গ, কিন্তু আপনাকে নয়টি বর্গের প্রয়োজন! যদি আপনি কেবল কপি পেস্ট করার চেষ্টা করেন যাতে দুইটি বর্গ হয় এরকম:

```js {2}
export default function Square() {
  return <button className="square">X</button><button className="square">X</button>;
}
```

তুমি এই এরোরটি দেখবে:

<ConsoleBlock level="error">

/src/App.js: Adjacent JSX elements must be wrapped in an enclosing tag. Did you want a JSX Fragment `<>...</>`?

</ConsoleBlock>

React কম্পোনেন্টগুলিকে একটি একক JSX উপাদান ফেরত দিতে হয় এবং একাধিক পাশাপাশি থাকা JSX উপাদান, যেমন দুটি বোতাম ফেরত দেওয়া যাবে না। এটি ঠিক করতে আপনি _Fragments_ (`<>` এবং `</>`) ব্যবহার করতে পারেন, যা একাধিক পাশাপাশি থাকা JSX উপাদানগুলিকে এরকমভাবে মোড়াবে:

```js {3-6}
export default function Square() {
  return (
    <>
      <button className="square">X</button>
      <button className="square">X</button>
    </>
  );
}
```

এখন এটা দেখা যাবে:

![two x-filled squares](../images/tutorial/two-x-filled-squares.png)

দারুণ! এখন আপনাকে কেবল কয়েকবার কপি-পেস্ট করতে হবে যাতে নয়টি স্কয়ার যোগ করা যায় এবং..

![nine x-filled squares in a line](../images/tutorial/nine-x-filled-squares.png)

ওহ না! সব স্কয়ার একটি লাইনেই রয়েছে, বোর্ডের মতো গ্রিডে নেই। এটি ঠিক করতে আপনাকে `div` দিয়ে স্কয়ারগুলোকে সারিতে সাজাতে হবে এবং কিছু CSS ক্লাস যোগ করতে হবে। একইসাথে, আপনি প্রতিটি স্কয়ারকে একটি নম্বর দেবেন, যাতে আপনি জানেন প্রতিটি স্কয়ার কোথায় প্রদর্শিত হচ্ছে।

`App.js` ফাইলে, `Square` কম্পোনেন্টটি এইভাবে আপডেট করুন:

```js {3-19}
export default function Square() {
  return (
    <>
      <div className="board-row">
        <button className="square">1</button>
        <button className="square">2</button>
        <button className="square">3</button>
      </div>
      <div className="board-row">
        <button className="square">4</button>
        <button className="square">5</button>
        <button className="square">6</button>
      </div>
      <div className="board-row">
        <button className="square">7</button>
        <button className="square">8</button>
        <button className="square">9</button>
      </div>
    </>
  );
}
```

`styles.css`-এ সংজ্ঞায়িত CSS কোড `className` হিসাবে `board-row` যুক্ত div-এর স্টাইল নির্ধারণ করে। এখন আপনি আপনার কম্পোনেন্টগুলোকে স্টাইলযুক্ত `div` দিয়ে সারিতে গোষ্ঠীবদ্ধ করেছেন, ফলে আপনার টিক-ট্যাক-টো বোর্ড তৈরি হয়েছে:

![tic-tac-toe board filled with numbers 1 through 9](../images/tutorial/number-filled-board.png)

কিন্তু এখন আপনার একটি সমস্যা হয়েছে। আপনার `Square` নামে যে কম্পোনেন্টটি আছে, তা আর সত্যিই একটি স্কয়ার নয়। চলুন এটি ঠিক করি নামটি `Board`-এ পরিবর্তন করে:

```js {1}
export default function Board() {
  //...
}
```

এ পর্যায়ে আপনার কোডটি কিছুটা এরকম দেখাবে:

<Sandpack>

```js
export default function Board() {
  return (
    <>
      <div className="board-row">
        <button className="square">1</button>
        <button className="square">2</button>
        <button className="square">3</button>
      </div>
      <div className="board-row">
        <button className="square">4</button>
        <button className="square">5</button>
        <button className="square">6</button>
      </div>
      <div className="board-row">
        <button className="square">7</button>
        <button className="square">8</button>
        <button className="square">9</button>
      </div>
    </>
  );
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

<Note>

এটি টাইপ করার জন্য অনেক কিছু! এই পেজ থেকে কোড কপি এবং পেস্ট করা ঠিক আছে। তবে, আপনি যদি একটু চ্যালেঞ্জ নিতে চান, আমরা পরামর্শ দেব যে আপনি শুধুমাত্র সেই কোডটি কপি করুন, যা আপনি অন্তত একবার নিজে হাতে টাইপ করেছেন।

</Note>

### Props দিয়ে ডেটা পাঠানো {/*passing-data-through-props*/}

এবার আপনি চাইবেন, ব্যবহারকারী যখন একটি স্কোয়ারে ক্লিক করবে, সেই স্কোয়ারের মানটি খালি থেকে “X” তে পরিবর্তিত হবে। আপনি যেভাবে বোর্ডটি তৈরি করেছেন, তাতে আপনাকে স্কোয়ারের মান আপডেট করার কোডটি নয়বার (প্রতিটি স্কোয়ারের জন্য একবার করে) কপি-পেস্ট করতে হবে! কপি-পেস্টের পরিবর্তে, React-এর কম্পোনেন্ট আর্কিটেকচার আপনাকে একটি পুনরায় ব্যবহারযোগ্য কম্পোনেন্ট তৈরি করার সুযোগ দেয় যাতে কোডের বিশৃঙ্খল ও পুনরাবৃত্তি এড়ানো যায়।

প্রথমে, আপনি আপনার `Board` কম্পোনেন্ট থেকে প্রথম স্কোয়ারের (`<button className="square">1</button>`) জন্য ডিফাইন করা লাইনটি কপি করে একটি নতুন `Square` কম্পোনেন্টে নিয়ে যাবেন।

```js {1-3}
function Square() {
  return <button className="square">1</button>;
}

export default function Board() {
  // ...
}
```

এরপর আপনি `Board` কম্পোনেন্টটি আপডেট করবেন যাতে এটি JSX সিনট্যাক্স ব্যবহার করে সেই `Square` কম্পোনেন্টটি রেন্ডার করে:

```js {5-19}
// ...
export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
    </>
  );
}
```

লক্ষ্য করুন, ব্রাউজারের `div`-এর মতো নয়, আপনার নিজের কম্পোনেন্টগুলো যেমন `Board` এবং `Square` অবশ্যই বড় হাতের অক্ষর দিয়ে শুরু হতে হবে। 

আসুন একবার দেখে নেই:

![one-filled board](../images/tutorial/board-filled-with-ones.png)

ওহহো! আপনি আগের নম্বরযুক্ত স্কোয়ারগুলো হারিয়ে ফেলেছেন। এখন প্রতিটি স্কোয়ারই "1" দেখাচ্ছে। এটি ঠিক করার জন্য, আপনি _props_ ব্যবহার করবেন যাতে প্যারেন্ট কম্পোনেন্ট (`Board`) থেকে চাইল্ড কম্পোনেন্টে (`Square`) প্রতিটি স্কোয়ারের মান পাঠানো যায়।

`Square` কম্পোনেন্টটিকে আপডেট করুন যাতে এটি `Board` থেকে পাঠানো `value` প্রপটি পড়ে:

```js {1}
function Square({ value }) {
  return <button className="square">1</button>;
}
```

`function Square({ value })` নির্দেশ করে যে `Square` কম্পোনেন্টে একটি `value` নামে প্রপ পাস করা যেতে পারে।

এখন আপনি প্রতিটি স্কোয়ারের ভিতরে "1"-এর পরিবর্তে সেই `value` প্রদর্শন করতে চান। এটি এইভাবে করার চেষ্টা করুন:

```js {2}
function Square({ value }) {
  return <button className="square">value</button>;
}
```

ওহ না, এটা তো আমরা যা চেয়েছিলাম তা নয়:

![value-filled board](../images/tutorial/board-filled-with-value.png)

আমরা আমাদের কম্পোনেন্ট থেকে `value` নামে জাভাস্ক্রিপ্ট ভ্যারিয়েবলটি রেন্ডার করতে চেয়েছিলাম, “value” শব্দটি নয়। JSX থেকে জাভাস্ক্রিপ্টে "escape" করতে হলে আমাদের কার্লি ব্রেস ব্যবহার করতে হবে। JSX-এ `value` এর চারপাশে কার্লি ব্রেস যোগ করুন এইভাবে:

```js {2}
function Square({ value }) {
  return <button className="square">{value}</button>;
}
```

এখন, আমরা একটি খালি বোর্ড দেখতে পাওয়া উচিত:

![empty board](../images/tutorial/empty-board.png)

এটি হচ্ছে কারণ `Board` কম্পোনেন্টটি এখনও প্রতিটি `Square` কম্পোনেন্টে রেন্ডার করার সময় `value` প্রপটি পাঠায়নি। এটি ঠিক করতে, আপনি `Board` কম্পোনেন্ট দ্বারা রেন্ডার করা প্রতিটি `Square` কম্পোনেন্টে `value` প্রপটি যোগ করবেন:

```js {5-7,10-12,15-17}
export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square value="1" />
        <Square value="2" />
        <Square value="3" />
      </div>
      <div className="board-row">
        <Square value="4" />
        <Square value="5" />
        <Square value="6" />
      </div>
      <div className="board-row">
        <Square value="7" />
        <Square value="8" />
        <Square value="9" />
      </div>
    </>
  );
}
```

এখন আপনি আবার সংখ্যার একটি গ্রিড দেখতে পাবেন:

![tic-tac-toe board filled with numbers 1 through 9](../images/tutorial/number-filled-board.png)

আপনার আপডেট করা কোডটি এরকম দেখতে হওয়া উচিত:

<Sandpack>

```js src/App.js
function Square({ value }) {
  return <button className="square">{value}</button>;
}

export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square value="1" />
        <Square value="2" />
        <Square value="3" />
      </div>
      <div className="board-row">
        <Square value="4" />
        <Square value="5" />
        <Square value="6" />
      </div>
      <div className="board-row">
        <Square value="7" />
        <Square value="8" />
        <Square value="9" />
      </div>
    </>
  );
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

### ইন্টারেক্টিভ কম্পোনেন্ট তৈরি করা {/*making-an-interactive-component*/}

যখন আপনি স্কোয়ারটিতে ক্লিক করবেন, তখন `Square` কম্পোনেন্টটিকে একটি `X` দিয়ে পূর্ণ করুন। `Square` এর ভিতরে একটি ফাংশন ঘোষণা করুন যাকে `handleClick` বলা হবে। তারপর, `Square` থেকে ফেরত দেওয়া বাটন JSX উপাদানের প্রপ্সে `onClick` যোগ করুন:

```js {2-4,9}
function Square({ value }) {
  function handleClick() {
    console.log('clicked!');
  }

  return (
    <button
      className="square"
      onClick={handleClick}
    >
      {value}
    </button>
  );
}
```

যদি আপনি এখন একটি স্কোয়ারে ক্লিক করেন, তাহলে আপনি _Browser_ সেকশনের নিচে _Console_ ট্যাবে `"clicked!"` লেখা একটি লগ দেখতে পাবেন। একাধিকবার স্কোয়ারটিতে ক্লিক করলে `"clicked!"` আবার লগ হবে। একই বার্তার পুনরাবৃত্তি লগ কনসোলে আরও লাইন তৈরি করবে না। পরিবর্তে, আপনার প্রথম `"clicked!"` লগের পাশে একটি বৃদ্ধি পাওয়া কাউন্টার দেখতে পাবেন।

<Note>

যদি আপনি আপনার লোকাল ডেভেলপমেন্ট পরিবেশে এই টিউটোরিয়ালটি অনুসরণ করছেন, তাহলে আপনাকে আপনার ব্রাউজারের কনসোল খুলতে হবে। উদাহরণস্বরূপ, আপনি যদি ক্রোম ব্রাউজার ব্যবহার করেন, তাহলে **Shift + Ctrl + J** (Windows/Linux-এ) অথবা **Option + ⌘ + J** (macOS-এ) কীবোর্ড শর্টকাট ব্যবহার করে কনসোলটি দেখতে পারেন।

</Note>

পরবর্তী ধাপে, আপনি চান যে `Square` কম্পোনেন্টটি “মনে রাখুক” যে এটি ক্লিক করা হয়েছে এবং এটি একটি “X” চিহ্ন দিয়ে পূর্ণ করুক। “মনে রাখার” জন্য, কম্পোনেন্টগুলি _state_ ব্যবহার করে।

React একটি বিশেষ ফাংশন প্রদান করে যার নাম `useState`, যা আপনি আপনার কম্পোনেন্ট থেকে কল করতে পারেন যাতে এটি “মনে রাখে”। চলুন, `Square` এর বর্তমান মানটি স্টেটে সংরক্ষণ করি এবং যখন `Square` ক্লিক করা হয় তখন এটি পরিবর্তন করি।

ফাইলের উপরের দিকে `useState` ইম্পোর্ট করুন। `Square` কম্পোনেন্ট থেকে `value` প্রপটি সরান। পরিবর্তে, `Square` এর শুরুর দিকে একটি নতুন লাইন যোগ করুন যা `useState` কল করে। এটি একটি স্টেট ভেরিয়েবল `value` ফিরিয়ে দেবে:

```js {1,3,4}
import { useState } from 'react';

function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
    //...
```

`value` মানটি সংরক্ষণ করে এবং `setValue` একটি ফাংশন যা মান পরিবর্তন করতে ব্যবহৃত হতে পারে। `useState`-এ পাঠানো `null` এই স্টেট ভেরিয়েবলের প্রাথমিক মান হিসাবে ব্যবহৃত হয়, তাই এখানে `value` শুরুতে `null` সমান।

যেহেতু `Square` কম্পোনেন্টটি এখন আর প্রপস গ্রহণ করে না, আপনি `Board` কম্পোনেন্ট দ্বারা তৈরি সব নয়টি `Square` কম্পোনেন্ট থেকে `value` প্রপটি সরিয়ে ফেলবেন:

```js {6-8,11-13,16-18}
// ...
export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
    </>
  );
}
```

এখন আপনি `Square`-কে ক্লিক করা হলে একটি “X” প্রদর্শন করতে পরিবর্তন করবেন। `console.log("clicked!");` ইভেন্ট হ্যান্ডলারটি `setValue('X');` দিয়ে প্রতিস্থাপন করুন। এখন আপনার `Square` কম্পোনেন্টটি এরকম দেখতে হবে:

```js {5}
function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
    setValue('X');
  }

  return (
    <button
      className="square"
      onClick={handleClick}
    >
      {value}
    </button>
  );
}
```

এই `set` ফাংশনটিকে একটি `onClick` হ্যান্ডলার থেকে কল করে, আপনি React-কে বলছেন যে `<button>` ক্লিক করার সময় সেই `Square`-টি পুনরায় রেন্ডার করতে হবে। আপডেটের পর, `Square` এর `value` `'X'` হবে, তাই আপনি গেম বোর্ডে “X” দেখতে পাবেন। যে কোনও স্কোয়ারে ক্লিক করুন, এবং “X” প্রদর্শিত হবে:

![adding xes to board](../images/tutorial/tictac-adding-x-s.gif)

প্রতিটি `Square`-এর নিজস্ব স্টেট রয়েছে: প্রতিটি `Square`-এ সংরক্ষিত `value` অন্যগুলোর থেকে সম্পূর্ণ স্বাধীন। যখন আপনি একটি কম্পোনেন্টে `set` ফাংশন কল করেন, React স্বয়ংক্রিয়ভাবে এর ভিতরের চাইল্ড কম্পোনেন্টগুলোকে আপডেট করে।

আপনার উপরোক্ত পরিবর্তনগুলি করার পর, আপনার কোডটি এরকম দেখাবে:

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
    setValue('X');
  }

  return (
    <button
      className="square"
      onClick={handleClick}
    >
      {value}
    </button>
  );
}

export default function Board() {
  return (
    <>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
      <div className="board-row">
        <Square />
        <Square />
        <Square />
      </div>
    </>
  );
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

### React Developer Tools {/*react-developer-tools*/}

React DevTools আপনাকে আপনার React কম্পোনেন্টগুলির প্রপস এবং স্টেট পরীক্ষা করতে দেয়। আপনি CodeSandbox-এ _browser_ সেকশনের নিচে React DevTools ট্যাবটি খুঁজে পাবেন:

![React DevTools in CodeSandbox](../images/tutorial/codesandbox-devtools.png)

React DevTools-এ কোনো নির্দিষ্ট কম্পোনেন্ট পরিদর্শন করতে, উপরের বাঁ দিকের বাটনটি ব্যবহার করুন:

![Selecting components on the page with React DevTools](../images/tutorial/devtools-select.gif)

<Note>

লোকাল ডেভেলপমেন্টের জন্য, React DevTools [Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en), [Firefox](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/), এবং [Edge](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil) ব্রাউজারের একটি এক্সটেনশন হিসেবে পাওয়া যায়। এটি ইনস্টল করুন, এবং React ব্যবহার করা সাইটগুলির জন্য আপনার ব্রাউজারের ডেভেলপার টুলসে _Components_ ট্যাবটি উপস্থিত হবে।

</Note>

## গেম সম্পূর্ণ করা {/*completing-the-game*/}

এই পর্যায়ে এসে, আপনার টিক-ট্যাক-টো গেমটির সব মৌলিক উপাদান প্রস্তুত। সম্পূর্ণ গেমটি তৈরি করতে, এখন আপনাকে বোর্ডে "X" এবং "O" পালাক্রমে বসানোর ব্যবস্থা করতে হবে এবং বিজয়ী নির্ধারণের একটি উপায় লাগবে।

### স্টেট উপরে তোলা {/*lifting-state-up*/}

বর্তমানে, প্রতিটি `Square` কম্পোনেন্ট গেমের স্টেটের একটি অংশ ধরে রাখছে। টিক-ট্যাক-টো গেমে বিজয়ী নির্ধারণ করতে `Board` কম্পোনেন্টের জন্য প্রতিটি ৯টি `Square` কম্পোনেন্টের স্টেট জানা প্রয়োজন।

আপনি কীভাবে এই সমস্যার সমাধান করবেন? প্রথমে আপনার মনে হতে পারে যে, `Board` কম্পোনেন্ট প্রতিটি `Square`-এর স্টেট জানতে তাদের কাছে "জানতে" চাইবে। যদিও React-এ এই পদ্ধতি ব্যবহার করা সম্ভব, তবে আমরা এটি নিরুৎসাহিত করি কারণ এতে কোড জটিল হয়ে যায়, বাগের সম্ভাবনা বাড়ে এবং রিফ্যাক্টরিং করা কঠিন হয়ে যায়। এর পরিবর্তে, সবচেয়ে ভালো পদ্ধতি হল গেমের স্টেট প্রতিটি `Square` কম্পোনেন্টে রাখার পরিবর্তে পেরেন্ট `Board` কম্পোনেন্টে সংরক্ষণ করা। `Board` কম্পোনেন্ট প্রতিটি `Square`-কে প্রপসের মাধ্যমে জানিয়ে দিতে পারে যে তাদের কী প্রদর্শন করতে হবে, যেমনটি আপনি প্রতিটি `Square`-এ একটি সংখ্যা পাঠানোর সময় করেছিলেন।

**একাধিক চাইল্ডের থেকে ডেটা সংগ্রহ করার জন্য, অথবা দুটি চাইল্ড কম্পোনেন্টের মধ্যে যোগাযোগের জন্য, তাদের প্যারেন্ট কম্পোনেন্টে শেয়ার্ড স্টেট ঘোষণা করুন। প্যারেন্ট কম্পোনেন্ট সেই স্টেটটি চাইল্ডদের কাছে প্রপসের মাধ্যমে পাঠাতে পারে। এটি চাইল্ড কম্পোনেন্টগুলিকে একে অপরের এবং তাদের প্যারেন্টের সাথে সিঙ্ক রাখতে সাহায্য করে।**

প্যারেন্ট কম্পোনেন্টে স্টেট লিফট করা একটি সাধারণ প্রক্রিয়া যখন React কম্পোনেন্টগুলি রিফ্যাক্টর করা হয়।

এখন আমরা এটি চেষ্টা করার সুযোগ নেব। `Board` কম্পোনেন্টটি সম্পাদনা করুন যাতে এটি একটি স্টেট ভেরিয়েবল ঘোষণা করে যার নাম `squares` এবং এটি 9টি null এর একটি অ্যারেতে ডিফল্ট থাকে, যা 9টি স্কোয়ারকে উপস্থাপন করে:

```js {3}
// ...
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    // ...
  );
}
```

`Array(9).fill(null)` একটি অ্যারে তৈরি করে যার মধ্যে নয়টি উপাদান থাকে এবং প্রতিটির মান `null` সেট করে। এর চারপাশে থাকা `useState()` কলটি একটি `squares` স্টেট ভেরিয়েবল ঘোষণা করে, যা প্রাথমিকভাবে সেই অ্যারেতে সেট করা হয়। অ্যারেটির প্রতিটি এন্ট্রি একটি স্কোয়ারের মানের সাথে সম্পর্কিত। যখন আপনি পরে বোর্ডটি পূর্ণ করবেন, `squares` অ্যারেটি এরকম দেখাবে:

```jsx
['O', null, 'X', 'X', 'X', 'O', 'O', null, null]
```

এখন আপনার `Board` কম্পোনেন্টটি যে প্রতিটি `Square` রেন্ডার করে, সেগুলিতে `value` প্রপটি পাঠাতে হবে:

```js {6-8,11-13,16-18}
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} />
        <Square value={squares[1]} />
        <Square value={squares[2]} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} />
        <Square value={squares[4]} />
        <Square value={squares[5]} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} />
        <Square value={squares[7]} />
        <Square value={squares[8]} />
      </div>
    </>
  );
}
```

পরবর্তী পদক্ষেপে, আপনি `Square` কম্পোনেন্টটি আপডেট করবেন যাতে এটি `Board` কম্পোনেন্ট থেকে `value` প্রপটি গ্রহণ করে। এর জন্য `Square` কম্পোনেন্টের নিজস্ব স্টেটফুল ট্র্যাকিংটি `value` এবং বাটনের `onClick` প্রপটি সরাতে হবে:

```js {1,2}
function Square({value}) {
  return <button className="square">{value}</button>;
}
```

এই পর্যায়ে আপনি একটি খালি টিক-ট্যাক-টো বোর্ড দেখতে পাবেন:

![empty board](../images/tutorial/empty-board.png)

এবং আপনার কোডটি এরকম দেখতে হবে:

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square({ value }) {
  return <button className="square">{value}</button>;
}

export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} />
        <Square value={squares[1]} />
        <Square value={squares[2]} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} />
        <Square value={squares[4]} />
        <Square value={squares[5]} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} />
        <Square value={squares[7]} />
        <Square value={squares[8]} />
      </div>
    </>
  );
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

প্রতিটি `Square` এখন একটি `value` প্রপ গ্রহণ করবে যা `'X'`, `'O'`, অথবা খালি স্কোয়ারের জন্য `null` হবে।

পরবর্তী পদক্ষেপে, আপনাকে পরিবর্তন করতে হবে যে একটি `Square` ক্লিক করা হলে কী ঘটে। এখন `Board` কম্পোনেন্টটি কোন স্কোয়ারগুলি পূর্ণ হয়েছে তা বজায় রাখে। আপনাকে একটি উপায় তৈরি করতে হবে যাতে `Square` `Board` এর স্টেট আপডেট করতে পারে। যেহেতু স্টেট একটি কম্পোনেন্টের জন্য প্রাইভেট, তাই আপনি `Square` থেকে সরাসরি `Board` এর স্টেট আপডেট করতে পারবেন না।

বরং, আপনি `Board` কম্পোনেন্ট থেকে `Square` কম্পোনেন্টে একটি ফাংশন পাঠাবেন, এবং `Square` ক্লিক করার সময় সেই ফাংশনটি কল করবে। আপনি যে ফাংশনটি `Square` কম্পোনেন্টটি ক্লিক করার সময় কল করবে সেটি `onSquareClick` নামকরণ করবেন:

```js {3}
function Square({ value }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}
```

পরবর্তী পদক্ষেপে, আপনি `onSquareClick` ফাংশনটি `Square` কম্পোনেন্টের প্রপসে যুক্ত করবেন:

```js {1}
function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}
```

এখন আপনি `onSquareClick` প্রপটিকে `Board` কম্পোনেন্টের একটি ফাংশনের সাথে সংযুক্ত করবেন, যার নাম আপনি `handleClick` রাখবেন। `onSquareClick` কে `handleClick` এর সাথে সংযুক্ত করতে, আপনি প্রথম `Square` কম্পোনেন্টের `onSquareClick` প্রপটিতে একটি ফাংশন পাঠাবেন:

```js {7}
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={handleClick} />
        //...
  );
}
```

শেষে, আপনি `Board` কম্পোনেন্টের ভিতরে `handleClick` ফাংশনটি সংজ্ঞায়িত করবেন যাতে `squares` অ্যারেটি আপডেট হয়, যা আপনার বোর্ডের স্টেট ধারণ করে:

```js {4-8}
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick() {
    const nextSquares = squares.slice();
    nextSquares[0] = "X";
    setSquares(nextSquares);
  }

  return (
    // ...
  )
}
```

`handleClick` ফাংশনটি JavaScript `slice()` অ্যারে মেথড ব্যবহার করে `squares` অ্যারির একটি কপি (`nextSquares`) তৈরি করে। তারপর, `handleClick` প্রথম স্কোয়ারের (অর্থাৎ `[0]` ইনডেক্স) মান আপডেট করতে `nextSquares` অ্যারিটি `X` দিয়ে পরিবর্তন করে।

`setSquares` ফাংশনটি কল করলে React জানে যে কম্পোনেন্টের স্টেট পরিবর্তিত হয়েছে। এটি সেই কম্পোনেন্টগুলোর রেন্ডার পুনরায় করার জন্য ট্রিগার করবে যা `squares` স্টেট ব্যবহার করে (`Board`) এবং এর চাইল্ড কম্পোনেন্টগুলোর (বোর্ডের স্কোয়ার কম্পোনেন্টগুলো)।

<Note>

JavaScript [closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) সমর্থন করে, যার মানে একটি অভ্যন্তরীণ ফাংশন (যেমন `handleClick`) একটি বাইরের ফাংশনের (যেমন `Board`) মধ্যে সংজ্ঞায়িত ভেরিয়েবল এবং ফাংশনগুলির অ্যাক্সেস পায়। `handleClick` ফাংশনটি `squares` স্টেট পড়তে এবং `setSquares` মেথড কল করতে পারে কারণ উভয়ই `Board` ফাংশনের ভিতরে সংজ্ঞায়িত।

</Note>

এখন আপনি বোর্ডে X যোগ করতে পারেন... কিন্তু শুধুমাত্র উপরের বাম স্কোয়ারে। আপনার `handleClick` ফাংশনটি উপরের বাম স্কোয়ারের ইনডেক্স (`0`) আপডেট করার জন্য হার্ডকোড করা। চলুন `handleClick` কে আপডেট করি যাতে এটি যেকোনো স্কোয়ার আপডেট করতে পারে। `handleClick` ফাংশনের একটি আর্গুমেন্ট `i` যোগ করুন, যা আপডেট করার স্কোয়ারের ইনডেক্স নেয়:

```js {4,6}
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    const nextSquares = squares.slice();
    nextSquares[i] = "X";
    setSquares(nextSquares);
  }

  return (
    // ...
  )
}
```

পরবর্তী পদক্ষেপে, আপনাকে সেই `i` কে `handleClick` এ পাঠাতে হবে। আপনি `onSquareClick` প্রপটিকে সরাসরি JSX তে `handleClick(0)` হিসেবে সেট করার চেষ্টা করতে পারেন, তবে এটি কাজ করবে না:

```jsx
<Square value={squares[0]} onSquareClick={handleClick(0)} />
```

এটি কেন কাজ করে না তার কারণ হল। `handleClick(0)` কলটি বোর্ড কম্পোনেন্টের রেন্ডারিংয়ের অংশ হবে। যেহেতু `handleClick(0)` `setSquares` কল করে বোর্ড কম্পোনেন্টের স্টেট পরিবর্তন করে, আপনার সম্পূর্ণ বোর্ড কম্পোনেন্টটি আবার রেন্ডার হবে। কিন্তু এটি আবার `handleClick(0)` চালায়, যা একটি অসীম লুপের দিকে নিয়ে যায়:

<ConsoleBlock level="error">

Too many re-renders. React limits the number of renders to prevent an infinite loop.

</ConsoleBlock>

এই সমস্যাটি কেন আগে ঘটেনি?

যখন আপনি `onSquareClick={handleClick}` পাঠাচ্ছিলেন, তখন আপনি `handleClick` ফাংশনটিকে একটি প্রপ হিসাবে নিচে পাঠাচ্ছিলেন। আপনি এটিকে কল করছেন না! কিন্তু এখন আপনি সেই ফাংশনটি সোজা সোজা কল করছেন—`handleClick(0)` এর মধ্যে প্যারেন্টেসিসগুলি লক্ষ্য করুন—এবং তাই এটি খুব তাড়াতাড়ি চলে। আপনি চান না যে ব্যবহারকারী ক্লিক না করা পর্যন্ত `handleClick` কে কল করা হোক!

আপনি এটি সমাধান করতে পারেন একটি ফাংশন তৈরি করে যেমন `handleFirstSquareClick` যা `handleClick(0)` কল করে, একটি ফাংশন যেমন `handleSecondSquareClick` যা `handleClick(1)` কল করে, এবং এভাবে। আপনি এই ফাংশনগুলিকে (কল করার পরিবর্তে) প্রপ হিসাবে নিচে পাঠাবেন যেমন `onSquareClick={handleFirstSquareClick}`। এটি অসীম লুপ সমাধান করবে।

তবে নয়টি ভিন্ন ফাংশন সংজ্ঞায়িত করা এবং তাদের প্রতিটির একটি নাম দেওয়া খুব দীর্ঘ। বরং, চলুন এটি করি:

```js {6}
export default function Board() {
  // ...
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        // ...
  );
}
```

নতুন `() =>` সিনট্যাক্সটি লক্ষ্য করুন। এখানে, `() => handleClick(0)` একটি _অ্যারো ফাংশন,_ যা ফাংশন সংজ্ঞায়িত করার জন্য একটি ছোট উপায়। যখন স্কোয়ারটি ক্লিক করা হয়, `=>` "অ্যারো"-এর পরের কোডটি চলবে, যা `handleClick(0)` কল করবে।

এখন আপনাকে অন্যান্য আটটি স্কোয়ার আপডেট করতে হবে যাতে সেগুলো অ্যারো ফাংশনগুলির মাধ্যমে `handleClick` কল করে। প্রতিটি `handleClick` কলের জন্য আর্গুমেন্টটি সঠিক স্কোয়ারের ইনডেক্সের সাথে সঙ্গতিপূর্ণ তা নিশ্চিত করুন:

```js {6-8,11-13,16-18}
export default function Board() {
  // ...
  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
};
```

এখন আপনি আবার বোর্ডের যেকোনো স্কোয়ারে ক্লিক করে X যোগ করতে পারেন:

![filling the board with X](../images/tutorial/tictac-adding-x-s.gif)

কিন্তু এই সময় সমস্ত স্টেট ম্যানেজমেন্ট `Board` কম্পোনেন্ট দ্বারা পরিচালিত হচ্ছে!

এখন আপনার কোডটি এভাবে দেখতে হবে:

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    const nextSquares = squares.slice();
    nextSquares[i] = 'X';
    setSquares(nextSquares);
  }

  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

এখন যেহেতু আপনার স্টেট পরিচালনা `Board` কম্পোনেন্টে রয়েছে, অভিভাবক `Board` কম্পোনেন্টটি শিশু `Square` কম্পোনেন্টগুলিতে প্রপস প্রেরণ করে যাতে সেগুলি সঠিকভাবে প্রদর্শিত হতে পারে। যখন আপনি একটি `Square`-এ ক্লিক করেন, শিশু `Square` কম্পোনেন্টটি এখন অভিভাবক `Board` কম্পোনেন্টকে বোর্ডের স্টেট আপডেট করার জন্য অনুরোধ করে। যখন `Board`-এর স্টেট পরিবর্তন হয়, তখন `Board` কম্পোনেন্ট এবং এর সমস্ত শিশু স্বয়ংক্রিয়ভাবে পুনঃরূপরেখা তৈরি করে। সমস্ত স্কোয়ারের স্টেট `Board` কম্পোনেন্টে রাখতে সক্ষম হওয়া ভবিষ্যতে বিজয়ী নির্ধারণ করতে সহায়তা করবে।

চলুন দেখে নিই যখন একজন ব্যবহারকারী আপনার বোর্ডের উপরের বাম স্কোয়ারে ক্লিক করে একটি `X` যুক্ত করতে চান, তখন কি ঘটে:

1. উপরের বাম স্কোয়ারে ক্লিক করার ফলে সেই ফাংশনটি চলে যা `Square` এর `onClick` প্রপস হিসাবে পেয়েছে। `Square` কম্পোনেন্টটি এই ফাংশনটি `Board` থেকে `onSquareClick` প্রপস হিসাবে পেয়েছে। `Board` কম্পোনেন্টটি সেই ফাংশনটি সরাসরি JSX-এ সংজ্ঞায়িত করেছে। এটি `handleClick` কে `0` আর্গুমেন্ট সহ কল করে।
2. `handleClick` আর্গুমেন্ট (`0`) ব্যবহার করে `squares` অ্যারেতে প্রথম উপাদানটিকে `null` থেকে `X`-এ আপডেট করে।
3. `Board` কম্পোনেন্টের `squares` স্টেট আপডেট হয়েছে, তাই `Board` এবং এর সমস্ত শিশু পুনঃরূপরেখা তৈরি করে। এর ফলে `0` ইনডেক্সের `Square` কম্পোনেন্টের `value` প্রপস `null` থেকে `X`-তে পরিবর্তিত হয়।

শেষে, ব্যবহারকারী দেখে যে উপরের বাম স্কোয়ারটি খালি থেকে `X`-তে পরিবর্তিত হয়েছে ক্লিক করার পর।

<Note>

DOM `<button>` উপাদানের `onClick` অ্যাট্রিবিউটের একটি বিশেষ অর্থ রয়েছে React-এর জন্য কারণ এটি একটি বিল্ট-ইন কম্পোনেন্ট। `Square` এর মতো কাস্টম কম্পোনেন্টগুলির জন্য নামকরণ আপনার উপরে নির্ভর করে। আপনি `Square` এর `onSquareClick` প্রপস বা `Board` এর `handleClick` ফাংশনের জন্য যেকোন নাম দিতে পারেন, এবং কোডটি একইভাবে কাজ করবে। React-এ, ইভেন্টগুলিকে প্রতিনিধিত্বকারী প্রপসের জন্য `onSomething` নাম ব্যবহার করা এবং সেই ইভেন্টগুলিকে পরিচালনা করার জন্য ফাংশন সংজ্ঞাগুলির জন্য `handleSomething` ব্যবহার করা প্রথাগত।

</Note>

### কেন ইমুইটাবিলিটি গুরুত্বপূর্ণ {/*why-immutability-is-important*/}

`handleClick`-এ লক্ষ্য করুন, আপনি বিদ্যমান অ্যারেটি পরিবর্তন করার পরিবর্তে `squares` অ্যারেটির একটি কপি তৈরি করতে `.slice()` কল করেন। কেন তার গুরুত্ব বোঝাতে আমাদের অমান্যযোগ্যতা এবং এর শিখন প্রয়োজন সম্পর্কে আলোচনা করতে হবে।

ডেটা পরিবর্তনের জন্য সাধারণত দুটি পন্থা রয়েছে। প্রথম পন্থাটি হল ডেটাকে সরাসরি পরিবর্তন করে ডেটার মান পরিবর্তন করা। দ্বিতীয় পন্থাটি হল নতুন কপিটি তৈরি করা যা প্রয়োজনীয় পরিবর্তনগুলি অন্তর্ভুক্ত করে। এখানে কীভাবে আপনি `squares` অ্যারেটিকে পরিবর্তন করবেন তা হবে:

```jsx
const squares = [null, null, null, null, null, null, null, null, null];
squares[0] = 'X';
// Now `squares` is ["X", null, null, null, null, null, null, null, null];
```

এবং যদি আপনি `squares` অ্যারের ডেটা পরিবর্তন করেন কিন্তু মিউটেট না করেন তবে এটি কেমন দেখাবে:

```jsx
const squares = [null, null, null, null, null, null, null, null, null];
const nextSquares = ['X', null, null, null, null, null, null, null, null];
// Now `squares` is unchanged, but `nextSquares` first element is 'X' rather than `null`
```

ফলাফল একই, কিন্তু সরাসরি (মৌলিক ডেটা পরিবর্তন না করে) মিউটেট না করে আপনি বেশ কিছু সুবিধা পান।

ইমিউটিবিলিটি জটিল বৈশিষ্ট্যগুলি বাস্তবায়ন করা অনেক সহজ করে তোলে। এই টিউটোরিয়ালের পরে, আপনি একটি “টাইম ট্রাভেল” বৈশিষ্ট্য বাস্তবায়ন করবেন যা আপনাকে গেমের ইতিহাস পর্যালোচনা করতে এবং “ফিরে আসতে” দেয়। এই কার্যকারিতা গেমগুলির জন্য বিশেষ নয়—কিছু ক্রিয়াকলাপ রিভার্স এবং রিডো করার ক্ষমতা অ্যাপগুলির জন্য একটি সাধারণ প্রয়োজন। সরাসরি ডেটা মিউটেশন এড়িয়ে চলা আপনাকে পূর্ববর্তী সংস্করণগুলি অক্ষুণ্ণ রাখতে এবং পরে পুনরায় ব্যবহার করতে দেয়।

ইমিউটিবিলিটির আরেকটি সুবিধাও রয়েছে। ডিফল্টভাবে, সমস্ত শিশু উপাদানগুলি স্বয়ংক্রিয়ভাবে পুনঃরেন্ডার হয় যখন একটি অভিভাবক উপাদানের অবস্থা পরিবর্তিত হয়। এতে এমন শিশু উপাদানগুলিও অন্তর্ভুক্ত রয়েছে যেগুলি পরিবর্তনের দ্বারা প্রভাবিত হয়নি। যদিও পুনঃরেন্ডারিং নিজেই ব্যবহারকারীর কাছে লক্ষ্যযোগ্য নয় (আপনাকে এটি এড়ানোর জন্য সক্রিয়ভাবে চেষ্টা করা উচিত নয়!), তবে আপনি পারফরম্যান্সের কারণে পরিষ্কারভাবে প্রভাবিত না হওয়া একটি গাছের অংশ পুনঃরেন্ডারিং এড়াতে চাইতে পারেন। ইমিউটিবিলিটি উপাদানগুলিকে তুলনা করা খুব সস্তা করে যে তাদের ডেটা পরিবর্তিত হয়েছে কি না। আপনি [`memo` API রেফারেন্সে](/reference/react/memo) জানতে পারেন কিভাবে React নির্ধারণ করে কখন একটি উপাদান পুনঃরেন্ডার করা হবে।

### পালা বদলানো {/*taking-turns*/}

এখন টিক-ট্যাক-টো গেমের একটি বড় ত্রুটি সংশোধন করার সময় হয়েছে: বোর্ডে "O" চিহ্ন দেওয়া যাচ্ছে না।

প্রথম মুভটিকে ডিফল্ট হিসেবে "X" নির্ধারণ করুন। এটি ট্র্যাক করতে `Board` কম্পোনেন্টে আরেকটি স্টেট যোগ করুন।

```js {2}
function Board() {
  const [xIsNext, setXIsNext] = useState(true);
  const [squares, setSquares] = useState(Array(9).fill(null));

  // ...
}
```

প্রতি বার যখন একজন প্লেয়ার moves করে, `xIsNext` (একটি বুলিয়ান) পাল্টে যাবে যাতে নির্ধারণ করা যায় পরবর্তী প্লেয়ার কে এবং গেমের অবস্থা সংরক্ষিত হবে। আপনি `Board` এর `handleClick` ফাংশনটি আপডেট করবেন `xIsNext` এর মান পাল্টানোর জন্য:

```js {7,8,9,10,11,13}
export default function Board() {
  const [xIsNext, setXIsNext] = useState(true);
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = "X";
    } else {
      nextSquares[i] = "O";
    }
    setSquares(nextSquares);
    setXIsNext(!xIsNext);
  }

  return (
    //...
  );
}
```

এখন, যখন আপনি বিভিন্ন স্কোয়ারে ক্লিক করেন, তারা `X` এবং `O` এর মধ্যে পাল্টাতে থাকবে, যেমনটি হওয়া উচিত!

কিন্তু অপেক্ষা করুন, একটি সমস্যা রয়েছে। একই স্কোয়ারে একাধিকবার ক্লিক করার চেষ্টা করুন:

![O overwriting an X](../images/tutorial/o-replaces-x.gif)

`X` একটি `O` দ্বারা ওভাররাইট হচ্ছে! যদিও এটি গেমটিতে একটি খুব মজার মোড় যুক্ত করবে, আমরা আপাতত মূল নিয়মগুলি মেনে চলব।

যখন আপনি একটি স্কোয়ারকে `X` বা `O` দিয়ে চিহ্নিত করেন, আপনি প্রথমে পরীক্ষা করছেন না যে স্কোয়ারটিতে ইতিমধ্যে একটি `X` বা `O` মান রয়েছে কি না। আপনি এটি _আগে ফিরে আসা_ দ্বারা ঠিক করতে পারেন। আপনি পরীক্ষা করবেন যে স্কোয়ারটিতে ইতিমধ্যে একটি `X` বা `O` আছে কি না। যদি স্কোয়ারটি ইতিমধ্যে পূর্ণ হয়, তাহলে আপনি `handleClick` ফাংশনে আগে ফিরে আসবেন—এর আগে যে এটি বোর্ডের অবস্থা আপডেট করার চেষ্টা করে।

```js {2,3,4}
function handleClick(i) {
  if (squares[i]) {
    return;
  }
  const nextSquares = squares.slice();
  //...
}
```

এখন আপনি কেবল খালি স্কোয়ারে `X` অথবা `O` যুক্ত করতে পারেন! এখানে এই পর্যায়ে আপনার কোড কেমন হওয়া উচিত:

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square({value, onSquareClick}) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

export default function Board() {
  const [xIsNext, setXIsNext] = useState(true);
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    if (squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    setSquares(nextSquares);
    setXIsNext(!xIsNext);
  }

  return (
    <>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

### বিজয়ী ঘোষণা করা {/*declaring-a-winner*/}

এখন যেহেতু খেলোয়াড়রা পালা নিতে পারে, আপনি দেখতে চাইবেন কখন গেমটি জিতেছে এবং আর কোনো পালা নেওয়ার নেই। এটি করার জন্য, আপনি একটি সহায়ক ফাংশন যোগ করবেন যাকে `calculateWinner` বলা হয়, যা ৯টি স্কোয়ার সম্বলিত একটি অ্যারে গ্রহণ করে, বিজয়ী পরীক্ষা করে এবং যথাযথভাবে `'X'`, `'O'`, বা `null` রিটার্ন করে। `calculateWinner` ফাংশন নিয়ে খুব বেশি চিন্তা করবেন না; এটি React এর জন্য বিশেষ নয়:

```js src/App.js
export default function Board() {
  //...
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6]
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

<Note>

আপনি `calculateWinner` ফাংশনটি `Board` এর আগে বা পরে সংজ্ঞায়িত করেছেন কিনা তা কোনো বিষয় নয়। আসুন এটি শেষে রাখি যাতে আপনাকে প্রতিবার আপনার উপাদানগুলি সম্পাদনা করার সময় এর মধ্য দিয়ে স্ক্রোল করতে না হয়।

</Note>

আপনি `Board` উপাদানের `handleClick` ফাংশনে `calculateWinner(squares)` কল করবেন যাতে পরীক্ষা করতে পারেন যে একজন খেলোয়াড় জিতেছে কি না। আপনি যখন পরীক্ষা করছেন যে একটি ব্যবহারকারী ইতিমধ্যে একটি `X` বা `O` রয়েছে এমন স্কোয়ার ক্লিক করেছে তখন একই সময়ে এই পরীক্ষা করতে পারেন। উভয় ক্ষেত্রেই আমরা আগে ফিরে আসতে চাই:

```js {2}
function handleClick(i) {
  if (squares[i] || calculateWinner(squares)) {
    return;
  }
  const nextSquares = squares.slice();
  //...
}
```

খেলোয়াড়দের গেমটি শেষ হলে জানাতে, আপনি “Winner: X” অথবা “Winner: O” মত লেখা প্রদর্শন করতে পারেন। এটি করার জন্য, আপনি `Board` উপাদানে একটি `status` সেকশন যোগ করবেন। গেমটি শেষ হলে স্ট্যাটাস বিজয়ী প্রদর্শন করবে এবং গেম চলমান থাকলে আপনি পরবর্তী কোন খেলোয়াড়ের পালা তা প্রদর্শন করবেন:

```js {3-9,13}
export default function Board() {
  // ...
  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = "Winner: " + winner;
  } else {
    status = "Next player: " + (xIsNext ? "X" : "O");
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        // ...
  )
}
```

অভিনন্দন! এখন আপনার কাছে একটি কার্যকর টিক-ট্যাক-টো গেম রয়েছে। এবং আপনি React এর মৌলিক বিষয়গুলি শিখেছেন। সুতরাং _আপিনিই_ এখানে সত্যিকারের বিজয়ী। এখানে কোড কেমন হওয়া উচিত:

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square({value, onSquareClick}) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

export default function Board() {
  const [xIsNext, setXIsNext] = useState(true);
  const [squares, setSquares] = useState(Array(9).fill(null));

  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    setSquares(nextSquares);
    setXIsNext(!xIsNext);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

## সময়ে ফিরে যাওয়া যোগ করা {/*adding-time-travel*/}

শেষ একটি অনুশীলন হিসাবে, আসুন এটি সম্ভব করি যাতে গেমে পূর্ববর্তী পদক্ষেপগুলিতে "যেতে" পারি।

### চালগুলির ইতিহাস সংরক্ষণ {/*storing-a-history-of-moves*/}

যদি আপনি `squares` অ্যারেটি মিউটেট করেন, তবে সময়ে ফিরে যাওয়া বাস্তবায়ন করা খুব কঠিন হবে।

তবে, আপনি প্রতি পদক্ষেপের পরে `squares` অ্যারেটির একটি নতুন কপি তৈরি করতে `slice()` ব্যবহার করেছেন এবং এটিকে অImmutable হিসাবে বিবেচনা করেছেন। এটি আপনাকে `squares` অ্যারেটির প্রতিটি অতীত সংস্করণ সংরক্ষণ করতে দেবে এবং ইতিমধ্যে ঘটে যাওয়া পালাগুলির মধ্যে নেভিগেট করতে সহায়তা করবে।

আপনি অতীতের `squares` অ্যারেগুলিকে `history` নামক একটি অন্য অ্যারেতে সংরক্ষণ করবেন, যা একটি নতুন স্টেট ভেরিয়েবল হিসাবে সংরক্ষণ করবেন। `history` অ্যারে সমস্ত বোর্ডের অবস্থাগুলি উপস্থাপন করে, প্রথম থেকে শেষ পদক্ষেপ পর্যন্ত, এবং এর আকৃতি এই রকম:

```jsx
[
  // Before first move
  [null, null, null, null, null, null, null, null, null],
  // After first move
  [null, null, null, null, 'X', null, null, null, null],
  // After second move
  [null, null, null, null, 'X', null, null, null, 'O'],
  // ...
]
```

### স্টেট উপরে উঠানো, আবার {/*lifting-state-up-again*/}

আপনি এখন একটি নতুন শীর্ষ স্তরের উপাদান `Game` লিখবেন যা অতীতের পদক্ষেপগুলির একটি তালিকা প্রদর্শন করবে। সেখানেই আপনি পুরো গেমের ইতিহাস ধারণকারী `history` স্টেটটি স্থাপন করবেন।

`Game` উপাদানে `history` স্টেটটি স্থাপন করা আপনাকে এর সন্তান `Board` উপাদান থেকে `squares` স্টেটটি অপসারণ করতে দেবে। যেমন আপনি `Square` উপাদান থেকে `Board` উপাদানে “স্টেটটি উপরে তুলেছিলেন”, ঠিক তেমনই আপনি এখন এটি `Board` থেকে শীর্ষ স্তরের `Game` উপাদানে তুলবেন। এটি `Game` উপাদানটিকে `Board`’এর ডেটার সম্পূর্ণ নিয়ন্ত্রণ দেয় এবং এটি `Board` কে ইতিহাস থেকে পূর্ববর্তী পালাগুলি রেন্ডার করতে নির্দেশ দিতে সক্ষম করে।

প্রথমে, `export default` সহ একটি `Game` উপাদান যুক্ত করুন। এটি `Board` উপাদানটি এবং কিছু মার্কআপ রেন্ডার করবে:

```js {1,5-16}
function Board() {
  // ...
}

export default function Game() {
  return (
    <div className="game">
      <div className="game-board">
        <Board />
      </div>
      <div className="game-info">
        <ol>{/*TODO*/}</ol>
      </div>
    </div>
  );
}
```

নোট করুন যে আপনি `function Board() {` ঘোষণার আগে `export default` কিওয়ার্ডগুলি অপসারণ করছেন এবং `function Game() {` ঘোষণার আগে সেগুলি যোগ করছেন। এটি আপনার `index.js` ফাইলকে জানায় যে `Game` উপাদানটিকে শীর্ষ স্তরের উপাদান হিসেবে ব্যবহার করতে হবে, আপনার `Board` উপাদানের পরিবর্তে। `Game` উপাদান দ্বারা ফেরত দেওয়া অতিরিক্ত `div` গুলি পরে বোর্ডে আপনি যে গেমের তথ্যগুলি যোগ করবেন তার জন্য স্থান তৈরি করছে।

আপনার পরবর্তী খেলোয়াড় এবং পদক্ষেপগুলির ইতিহাস ট্র্যাক করার জন্য `Game` উপাদানে কিছু স্টেট যোগ করুন:

```js {2-3}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  // ...
```

নোটিস করুন যে `[Array(9).fill(null)]` হল একটি অ্যারে যা একটি একক আইটেম নিয়ে গঠিত, যা নিজেই 9টি `null` দিয়ে গঠিত একটি অ্যারে।

বর্তমান মুভের জন্য স্কয়ারগুলি রেন্ডার করতে, আপনাকে `history` থেকে শেষ স্কয়ারস অ্যারেটি পড়তে হবে। আপনার জন্য `useState` প্রয়োজন নেই—আপনার কাছে এটি রেন্ডার করার সময় হিসাব করার জন্য যথেষ্ট তথ্য রয়েছে।

```js {4}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];
  // ...
```

এখন, `Game` কম্পোনেন্টের ভিতরে একটি `handlePlay` ফাংশন তৈরি করুন যা `Board` কম্পোনেন্ট দ্বারা খেলার আপডেট করার জন্য কল করা হবে। `xIsNext`, `currentSquares` এবং `handlePlay` কে `Board` কম্পোনেন্টে প্রপস হিসাবে পাঠান:

```js {6-8,13}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    // TODO
  }

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
        //...
  )
}
```

আমরা `Board` কম্পোনেন্টটিকে সম্পূর্ণরূপে সেই প্রপস দ্বারা নিয়ন্ত্রিত করি যা এটি গ্রহণ করে। `Board` কম্পোনেন্টটিকে তিনটি প্রপস নিতে পরিবর্তন করুন: `xIsNext`, `squares`, এবং একটি নতুন `onPlay` ফাংশন যা `Board` একটি প্লেয়ার যখন একটি মুভ করে, তখন আপডেট করা স্কয়ারস অ্যারে নিয়ে কল করতে পারে। এরপর, `Board` ফাংশনের প্রথম দুই লাইন মুছে ফেলুন যা `useState` কল করে:

```js {1}
function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    //...
  }
  // ...
}
```

এখন `Board` কম্পোনেন্টের `handleClick`-এ `setSquares` এবং `setXIsNext` কলগুলিকে আপনার নতুন `onPlay` ফাংশনের একটি একক কলের সাথে প্রতিস্থাপন করুন, যাতে `Game` কম্পোনেন্ট ব্যবহারকারী যখন একটি স্কয়ার ক্লিক করে তখন `Board` আপডেট করতে পারে:

```js {12}
function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = "X";
    } else {
      nextSquares[i] = "O";
    }
    onPlay(nextSquares);
  }
  //...
}
```

`Board` কম্পোনেন্ট সম্পূর্ণরূপে `Game` কম্পোনেন্ট দ্বারা দেওয়া প্রপস দ্বারা নিয়ন্ত্রিত। আপনাকে আবার খেলা কাজ করতে `Game` কম্পোনেন্টে `handlePlay` ফাংশনটি বাস্তবায়ন করতে হবে।

`handlePlay` কল করা হলে কী করা উচিত? মনে রাখবেন যে `Board` সাধারণত একটি আপডেট করা অ্যারির সাথে `setSquares` কল করত; এখন এটি আপডেট করা `squares` অ্যারিটি `onPlay` এর মাধ্যমে পাস করছে।

`handlePlay` ফাংশনটি `Game` এর স্টেট আপডেট করতে হবে যাতে একটি পুনরায় রেন্ডার ট্রিগার হয়, কিন্তু আপনার আর একটি `setSquares` ফাংশন নেই যা আপনি কল করতে পারেন—এখন আপনি এই তথ্য সংরক্ষণ করতে `history` স্টেট ভ্যারিয়েবল ব্যবহার করছেন। আপনাকে `history` আপডেট করতে হবে নতুন একটি ইতিহাস এন্ট্রি হিসাবে আপডেট করা `squares` অ্যারিটি সংযুক্ত করে। আপনাকে `xIsNext`-কে টগলও করতে হবে, যেমনটি `Board` আগে করত:

```js {4-5}
export default function Game() {
  //...
  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }
  //...
}
```

এখানে, `[...history, nextSquares]` একটি নতুন অ্যারে তৈরি করে যা `history`-এর সমস্ত আইটেমকে অন্তর্ভুক্ত করে, তার পর `nextSquares`। (`...history` [*স্প্রেড সিনট্যাক্স*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) কে আপনি “`history`-এর সমস্ত আইটেম তালিকাভুক্ত করুন” হিসাবে পড়তে পারেন।)

যদি উদাহরণস্বরূপ, `history` হয় `[[null,null,null], ["X",null,null]]` এবং `nextSquares` হয় `["X",null,"O"]`, তবে নতুন `[...history, nextSquares]` অ্যারিটি হবে `[[null,null,null], ["X",null,null], ["X",null,"O"]]`।

এই পর্যায়ে, আপনি স্টেটকে `Game` কম্পোনেন্টে স্থানান্তরিত করেছেন, এবং UI সম্পূর্ণরূপে কাজ করছে, যেমন এটি পুনর্গঠন করার আগে ছিল। এখন কোডটি এরকম হওয়া উচিত:

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{/*TODO*/}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

### পূর্ববর্তী মুভগুলো প্রদর্শন করা {/*showing-the-past-moves*/}

যেহেতু আপনি টিক-ট্যাক-টো খেলার ইতিহাস রেকর্ড করছেন, আপনি এখন খেলোয়াড়ের জন্য পূর্ববর্তী মুভগুলির একটি তালিকা প্রদর্শন করতে পারেন।

React উপাদানগুলি যেমন `<button>` সাধারণ জাভাস্ক্রিপ্ট অবজেক্ট; আপনি এগুলো আপনার অ্যাপে একত্রিত করতে পারেন। React-এ একাধিক আইটেম রেন্ডার করতে, আপনি React উপাদানের একটি অ্যারে ব্যবহার করতে পারেন।

আপনার কাছে ইতিমধ্যে `history` মুভগুলির একটি অ্যারে রয়েছে, তাই এখন আপনাকে এটিকে React উপাদানের একটি অ্যারেতে রূপান্তর করতে হবে। জাভাস্ক্রিপ্টে, একটি অ্যারেকে অন্য একটি অ্যারেতে রূপান্তর করতে, আপনি [array `map` method:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) মেথড ব্যবহার করতে পারেন:

```jsx
[1, 2, 3].map((x) => x * 2) // [2, 4, 6]
```

আপনি `map` ব্যবহার করবেন আপনার মুভগুলোর `history`-কে React উপাদানে রূপান্তর করতে, যা স্ক্রিনে বোতামগুলি উপস্থাপন করে এবং পূর্ববর্তী মুভগুলিতে “জাম্প” করার জন্য বোতামের একটি তালিকা প্রদর্শন করে। আসুন `Game` কম্পোনেন্টে `history`-এর উপর `map` করি:

```js {11-13,15-27,35}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }

  function jumpTo(nextMove) {
    // TODO
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}
```

আপনার কোডটি নিচে কেমন হবে তা দেখতে পারেন। লক্ষ্য করুন যে আপনাকে ডেভেলপার টুলস কনসোলে একটি ত্রুটি দেখতে হবে যা বলছে:

<ConsoleBlock level="warning">
Warning: Each child in an array or iterator should have a unique "key" prop. Check the render method of &#96;Game&#96;.
</ConsoleBlock>
  
আপনি পরবর্তী বিভাগে এই ত্রুটিটি সমাধান করবেন।

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }

  function jumpTo(nextMove) {
    // TODO
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}

.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

যখন আপনি `map`-এ পাঠানো ফাংশনের ভিতরে `history` অ্যারের মধ্য দিয়ে পুনরাবৃত্তি করেন, তখন `squares` আর্গুমেন্টটি `history` এর প্রতিটি উপাদান দিয়ে যায়, এবং `move` আর্গুমেন্টটি প্রতিটি ইনডেক্স দিয়ে যায়: `0`, `1`, `2`, …। (বেশিরভাগ ক্ষেত্রে, প্রকৃত অ্যারের উপাদানগুলি প্রয়োজন হয়, তবে মুভের একটি তালিকা রেন্ডার করতে শুধুমাত্র ইনডেক্স প্রয়োজন।)

টিক-ট্যাক-টো গেমের `history` এর প্রতিটি মুভের জন্য, আপনি একটি তালিকা আইটেম `<li>` তৈরি করছেন যা একটি বোতাম `<button>` ধারণ করে। এই বোতামের একটি `onClick` হ্যান্ডলার রয়েছে, যা `jumpTo` নামের একটি ফাংশনকে কল করে (যা আপনি এখনো তৈরি করেননি)।

এখন আপনি গেমে হওয়া মুভগুলোর একটি তালিকা দেখতে পাবেন এবং ডেভেলপার টুলের কনসোলে একটি ত্রুটি (error) দেখতে পাবেন। আসুন আলোচনা করি "key" ত্রুটির অর্থ কী।

### কী নির্বাচন {/*picking-a-key*/}

যখন আপনি একটি তালিকা রেন্ডার করেন, React প্রতিটি রেন্ডার করা তালিকা আইটেম সম্পর্কে কিছু তথ্য সংরক্ষণ করে। যখন আপনি একটি তালিকা আপডেট করেন, React-কে জানতে হবে কী পরিবর্তিত হয়েছে। আপনি তালিকার আইটেমগুলি যোগ করেছেন, মুছেছেন, পুনর্বিন্যস্ত করেছেন বা আপডেট করেছেন।

এখন থেকে ট্রানজিশন কল্পনা করুন:

```html
<li>Alexa: 7 tasks left</li>
<li>Ben: 5 tasks left</li>
```

থেকে

```html
<li>Ben: 9 tasks left</li>
<li>Claudia: 8 tasks left</li>
<li>Alexa: 5 tasks left</li>
```

আপডেট হওয়া গণনার পাশাপাশি, একজন মানুষ এটি পড়ে সম্ভবত বলবে যে আপনি Alexa এবং Ben-এর ক্রম পরিবর্তন করেছেন এবং তাদের মধ্যে Claudia-কে যোগ করেছেন। তবে, React একটি কম্পিউটার প্রোগ্রাম এবং আপনার উদ্দেশ্য বোঝে না, তাই প্রতিটি তালিকা আইটেমকে তার অন্যান্য আইটেমের থেকে আলাদা করতে আপনাকে একটি _key_ প্রপার্টি নির্দিষ্ট করতে হবে। যদি আপনার ডেটা একটি ডেটাবেস থেকে আসত, তবে Alexa, Ben, এবং Claudia-র ডেটাবেস ID গুলো কী হিসেবে ব্যবহার করা যেত।

```js {1}
<li key={user.id}>
  {user.name}: {user.taskCount} tasks left
</li>
```

যখন কোনো তালিকা পুনরায় রেন্ডার করা হয়, React প্রতিটি তালিকা আইটেমের `key` নিয়ে আগের তালিকার আইটেমগুলোর মধ্যে মিল খোঁজে। যদি বর্তমান তালিকায় এমন কোনো `key` থাকে যা আগে ছিল না, তাহলে React একটি নতুন কম্পোনেন্ট তৈরি করে। আর যদি আগের তালিকায় থাকা কোনো `key` বর্তমান তালিকায় না থাকে, তাহলে React সেই কম্পোনেন্টটি মুছে দেয়। যদি দুটি `key` মিলে যায়, তবে সংশ্লিষ্ট কম্পোনেন্টটিকে সরানো হয়।

`key` React-কে প্রতিটি কম্পোনেন্টের পরিচয় সম্পর্কে জানায়, যা React-কে পুনরায় রেন্ডার করার সময় স্টেট বজায় রাখতে সাহায্য করে। যদি কোনো কম্পোনেন্টের `key` পরিবর্তিত হয়, তাহলে কম্পোনেন্টটি ধ্বংস করা হয় এবং নতুন স্টেট নিয়ে পুনরায় তৈরি হয়।

`key` একটি বিশেষ ও সংরক্ষিত প্রপার্টি React-এ। যখন কোনো এলিমেন্ট তৈরি হয়, React `key` প্রপার্টিটি নিয়ে সরাসরি সেই এলিমেন্টে সংরক্ষণ করে। যদিও `key` প্রপস হিসেবে মনে হতে পারে, React স্বয়ংক্রিয়ভাবে `key` ব্যবহার করে নির্ধারণ করে কোন কম্পোনেন্টগুলো আপডেট হবে। কোনো কম্পোনেন্ট তার প্যারেন্ট কী `key` নির্দিষ্ট করেছে তা জানতে পারে না।

**যখন আপনি ডায়নামিক তালিকা তৈরি করেন, তখন সঠিক `key` নির্ধারণ করা রেকমেন্ডেট।** যদি আপনার কাছে উপযুক্ত `key` না থাকে, তাহলে আপনি আপনার ডেটা পুনর্গঠন করার কথা বিবেচনা করতে পারেন যাতে আপনি তা পেতে পারেন।

যদি কোনো `key` নির্দিষ্ট না করা হয়, React একটি ত্রুটি রিপোর্ট করবে এবং ডিফল্টভাবে অ্যারের ইনডেক্সকে `key` হিসেবে ব্যবহার করবে। অ্যারের ইনডেক্সকে `key` হিসেবে ব্যবহার করা তালিকার আইটেমগুলোকে পুনরায় সাজানোর সময় অথবা তালিকা থেকে আইটেম যোগ/মুছে ফেলার সময় সমস্যা সৃষ্টি করে। স্পষ্টভাবে `key={i}` পাঠানো ত্রুটিটি চাপা দেয়, কিন্তু এটি অ্যারের ইনডেক্সের মতো একই সমস্যা নিয়ে আসে এবং বেশিরভাগ ক্ষেত্রে এটি সুপারিশ করা হয় না।

`key` গুলোর বিশ্বব্যাপী অনন্য হওয়া দরকার নেই; বরং এটি শুধুমাত্র কম্পোনেন্টগুলোর এবং তাদের সাথীদের মধ্যে অনন্য হতে হবে।

### টাইম ট্রাভেল বাস্তবায়ন {/*implementing-time-travel*/}

টিক-ট্যাক-টো খেলার ইতিহাসে, প্রতিটি পূর্ববর্তী মুভের সাথে একটি ইউনিক আইডি যুক্ত থাকে: এটি মুভের ক্রমিক সংখ্যা। মুভগুলিকে কখনই পুনর্বিন্যাস করা হবে না, মুছে ফেলা হবে না বা মাঝখানে যোগ করা হবে না, তাই মুভ ইনডেক্স কী হিসেবে ব্যবহার করা নিরাপদ।

`Game` ফাংশনে, আপনি কীটি `li`-এ `key={move}` হিসাবে যোগ করতে পারেন, এবং যদি আপনি রেন্ডার করা খেলাটি রিলোড করেন, তাহলে React-এর “key” ত্রুটিটি অদৃশ্য হয়ে যাবে:

```js {4}
const moves = history.map((squares, move) => {
  //...
  return (
    <li key={move}>
      <button onClick={() => jumpTo(move)}>{description}</button>
    </li>
  );
});
```

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];

  function handlePlay(nextSquares) {
    setHistory([...history, nextSquares]);
    setXIsNext(!xIsNext);
  }

  function jumpTo(nextMove) {
    // TODO
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li key={move}>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}

```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}

.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

`jumpTo` বাস্তবায়ন করার আগে, আপনাকে `Game` কম্পোনেন্টে ট্র্যাক রাখতে হবে যে ব্যবহারকারী কোন ধাপে বর্তমানে রয়েছে। এটি করতে, একটি নতুন স্টেট ভ্যারিয়েবল `currentMove` নামে সংজ্ঞায়িত করুন, যার ডিফল্ট মান হবে `0`:

```js {4}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const currentSquares = history[history.length - 1];
  //...
}
```

পরবর্তী ধাপে, `Game` এর ভিতরে `jumpTo` ফাংশন আপডেট করুন যাতে এটি `currentMove` আপডেট করে। এছাড়াও, আপনি `currentMove`-কে যে সংখ্যায় পরিবর্তন করছেন তা যদি জোড় সংখ্যা হয়, তাহলে `xIsNext`-কে `true` সেট করবেন।

```js {4-5}
export default function Game() {
  // ...
  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
    setXIsNext(nextMove % 2 === 0);
  }
  //...
}
```

এখন আপনি `Game` এর `handlePlay` ফাংশনে দুটি পরিবর্তন করবেন, যা তখন কল করা হয় যখন আপনি একটি স্কয়ারে ক্লিক করেন।

- যদি আপনি "সময়ের মধ্যে পিছনে যান" এবং তারপর সেই বিন্দু থেকে একটি নতুন মুভ করেন, তাহলে আপনি কেবল সেই পর্যন্ত ইতিহাস সংরক্ষণ করতে চান। `history`-এর সমস্ত আইটেমের (`...` স্প্রেড সিনট্যাক্স) পরে `nextSquares` যোগ করার পরিবর্তে, আপনি এটিকে `history.slice(0, currentMove + 1)`-এর পরে যোগ করবেন, যাতে আপনি কেবল পুরানো ইতিহাসের সেই অংশটি রাখছেন।
- প্রতিবার একটি মুভ করা হলে, আপনাকে `currentMove` আপডেট করতে হবে যাতে এটি সর্বশেষ ইতিহাস এন্ট্রির দিকে নির্দেশ করে।

```js {2-4}
function handlePlay(nextSquares) {
  const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
  setHistory(nextHistory);
  setCurrentMove(nextHistory.length - 1);
  setXIsNext(!xIsNext);
}
```

শেষে, আপনি `Game` কম্পোনেন্টটি সংশোধন করবেন যাতে এটি সর্বদা শেষ মুভটি রেন্ডার করার পরিবর্তে বর্তমানে নির্বাচিত মুভটি রেন্ডার করে:

```js {5}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const currentSquares = history[currentMove];

  // ...
}
```

যদি আপনি গেমের ইতিহাসের কোনো ধাপে ক্লিক করেন, তাহলে টিক-ট্যাক-টো বোর্ডটি সঙ্গে সঙ্গে আপডেট হবে এবং সেই ধাপের পর বোর্ড কেমন ছিল তা প্রদর্শন করবে।

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square({value, onSquareClick}) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
    setXIsNext(!xIsNext);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
    setXIsNext(nextMove % 2 === 0);
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li key={move}>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

### চূড়ান্ত গোছগাছ {/*final-cleanup*/}

যদি আপনি কোডটি খুব নিবিড়ভাবে দেখেন, তাহলে আপনি লক্ষ্য করতে পারেন যে `xIsNext === true` হয় যখন `currentMove` জোড় সংখ্যা হয় এবং `xIsNext === false` হয় যখন `currentMove` বিজোড় সংখ্যা হয়। অর্থাৎ, আপনি যদি `currentMove` এর মান জানেন, তাহলে আপনি সবসময় নির্ণয় করতে পারবেন যে `xIsNext` কী হওয়া উচিত।

এই কারণে, উভয়কেই স্টেটে সংরক্ষণ করার কোনো প্রয়োজন নেই। প্রকৃতপক্ষে, সবসময় অনুরূপ স্টেট সংরক্ষণ এড়াতে চেষ্টা করুন। স্টেটে যা সংরক্ষণ করছেন তা সরল করা বাগ কমায় এবং আপনার কোডটি বুঝতে সহজ করে তোলে। `Game` কে পরিবর্তন করুন যাতে এটি `xIsNext`-কে একটি আলাদা স্টেট ভ্যারিয়েবল হিসাবে সংরক্ষণ না করে এবং পরিবর্তে এটি `currentMove` এর উপর ভিত্তি করে নির্ধারণ করে:

```js {4,11,15}
export default function Game() {
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const xIsNext = currentMove % 2 === 0;
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
  }
  // ...
}
```

আপনার আর `xIsNext` স্টেট ডিক্লারেশন বা `setXIsNext` কলগুলির প্রয়োজন নেই। এখন, এমন কোনো সুযোগ নেই যে `xIsNext` এর মান `currentMove` এর সাথে অসামঞ্জস্যপূর্ণ হয়ে যাবে, এমনকি আপনি কম্পোনেন্ট কোডিং করার সময় কোনো ভুল করলেও।

### শেষ কথা {/*wrapping-up*/}

অভিনন্দন! আপনি একটি টিক-ট্যাক-টো গেম তৈরি করেছেন যা:
- আপনাকে টিক-ট্যাক-টো খেলতে দেয়,
- দেখায় কখন একজন খেলোয়াড় গেমটি জিতেছে,
- গেমের অগ্রগতির সাথে সাথে গেমের ইতিহাস সংরক্ষণ করে,
- খেলোয়াড়দের গেমের ইতিহাস পর্যালোচনা করতে এবং গেমের বোর্ডের পূর্ববর্তী সংস্করণগুলি দেখতে দেয়।

চমৎকার কাজ! আশা করি এখন আপনি React কীভাবে কাজ করে সে সম্পর্কে একটি ভালো ধারণা পেয়েছেন।

ফাইনাল ফলাফলটি এখানে দেখুন:

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const xIsNext = currentMove % 2 === 0;
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li key={move}>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

যদি আপনার হাতে অতিরিক্ত সময় থাকে বা আপনি আপনার নতুন React দক্ষতা অনুশীলন করতে চান, এখানে কিছু উন্নতির আইডিয়া দেওয়া হল যা আপনি টিক-ট্যাক-টো গেমে করতে পারেন, ক্রমবর্ধমান কঠিনতার ভিত্তিতে তালিকাভুক্ত করা হয়েছে:

1. শুধুমাত্র বর্তমান মুভটির জন্য, “You are at move #…” বার্তা দেখান একটি বোতামের পরিবর্তে।
2. `Board`-কে পুনরায় লিখুন যাতে দুটি লুপ ব্যবহার করে স্কোয়ার তৈরি করা হয়, হার্ডকোড করার পরিবর্তে।
3. এমন একটি টগল বোতাম যুক্ত করুন যা আপনাকে মুভগুলোকে উর্ধ্বগামী বা নিম্নগামী ক্রমে সাজানোর সুযোগ দেয়।
4. যখন কেউ জিতে যায়, সেই তিনটি স্কোয়ারকে হাইলাইট করুন যা জয়ের কারণ হয়েছিল (এবং যখন কেউ জিততে পারে না, তখন ড্র হওয়ার বার্তা দেখান)।
5. প্রতিটি মুভের অবস্থানকে (সারি, কলাম) ফরম্যাটে মুভ ইতিহাস তালিকায় দেখান।

এই টিউটোরিয়ালে, আপনি React এর বিভিন্ন ধারণা যেমন এলিমেন্ট, কম্পোনেন্ট, প্রপস এবং স্টেট সম্পর্কে জেনেছেন। এখন আপনি কিভাবে গেম তৈরি করতে এই ধারণাগুলি ব্যবহার করেছেন তা দেখেছেন, [Thinking in React](/learn/thinking-in-react) চেক করে দেখুন কিভাবে একই React ধারণাগুলি অ্যাপের UI তৈরি করতে কাজ করে।
