---
title: 'টিউটোরিয়াল: টিক-ট্যাক-টো'
---

<Intro>

এই টিউটোরিয়ালে একটি ছোট টিক-ট্যাক-টো গেম তৈরি করবেন। আগে থেকে React জানা প্রয়োজন নেই। এখানে শেখা কৌশলগুলো যেকোনো React অ্যাপ তৈরির জন্য গুরুত্বপূর্ণ, আর সেগুলো ভালোভাবে বুঝলে React সম্পর্কে গভীর ধারণা পাবেন।

</Intro>

<Note>

এই টিউটোরিয়াল তাঁদের জন্য, যাঁরা **কাজ করে শিখতে** পছন্দ করেন এবং দ্রুত কিছু তৈরি করতে চান। প্রতিটি ধারণা ধাপে ধাপে শিখতে চাইলে [UI-এর বর্ণনা](/learn/describing-the-ui) দিয়ে শুরু করুন।

</Note>

টিউটোরিয়ালটি কয়েকটি বিভাগে বিভক্ত:

- [টিউটোরিয়ালের জন্য সেটআপ](#setup-for-the-tutorial) অনুসরণ করার **প্রস্তুতি নিতে** সাহায্য করবে।
- [সংক্ষিপ্ত বিবরণ](#overview) React-এর **মৌলিক বিষয়গুলো**—কম্পোনেন্ট, props এবং state—শেখাবে।
- [গেমটি সম্পূর্ণ করা](#completing-the-game) React ডেভেলপমেন্টের **সবচেয়ে প্রচলিত কৌশলগুলো** শেখাবে।
- [সময় ভ্রমণ যোগ করা](#adding-time-travel) React-এর বিশেষ ক্ষমতাগুলো সম্পর্কে **আরও গভীর ধারণা** দেবে।

### আপনি কী বানাবেন? {/*what-are-you-building*/}

এই টিউটোরিয়ালে, আপনি React-এর সাহায্যে একটি ইন্টারেক্টিভ টিক-ট্যাক-টো গেম তৈরি করবেন।

গেমটির অন্তিম রূপ আপনি এখানে দেখতে পাচ্ছেন:

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

কোড এখনও না বুঝলে বা সিনট্যাক্স অপরিচিত মনে হলে চিন্তার কিছু নেই! এই টিউটোরিয়ালের উদ্দেশ্যই হলো React ও এর সিনট্যাক্স বুঝতে সাহায্য করা।

শুরু করার আগে উপরের টিক-ট্যাক-টো গেমটি একটু দেখে নিন। বোর্ডের ডান দিকে একটি সংখ্যাযুক্ত তালিকা দেখতে পাবেন। এতে গেমের সব চালের ইতিহাস থাকে এবং খেলার অগ্রগতির সাথে তালিকাটি আপডেট হয়।

গেমটি দেখে ও খেলে নেওয়া হলে নিচে স্ক্রল করুন। টিউটোরিয়ালটি সহজ একটি টেমপ্লেট দিয়ে শুরু করবেন। পরের ধাপে গেম তৈরির জন্য প্রয়োজনীয় সেটআপ করবেন।

## টিউটোরিয়ালের জন্য সেটআপ {/*setup-for-the-tutorial*/}

নীচের লাইভ কোড এডিটরে, CodeSandbox ওয়েবসাইট ব্যবহার করে সম্পাদকটিকে একটি নতুন ট্যাবে খুলতে উপরের-ডানদিকের কোণায় **Fork**-এ ক্লিক করুন। CodeSandbox আপনাকে আপনার ব্রাউজারে কোড লিখতে দেয় এবং আপনার তৈরি করা অ্যাপটি কীভাবে দেখাবে তার প্রিভিউ দেখতে দেয়। নতুন ট্যাবে এই টিউটোরিয়ালের জন্য একটি খালি বর্গক্ষেত্র এবং স্টার্টার কোড প্রদর্শন করা উচিত।

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

নিজের লোকাল ডেভেলপমেন্ট পরিবেশেও এই টিউটোরিয়াল অনুসরণ করতে পারেন। এজন্য নিচের ধাপগুলো অনুসরণ করুন:

1. [Node.js](https://nodejs.org/en/) ইন্সটল করুন।
2. আগে যে CodeSandbox ট্যাবটি খুলেছিলেন, বামে সর্বোচ্চ কোণার বাটনটি চাপ দিয়ে মেনু খুলুন, এবং এর পর সেই মেনুতে  **Download Sandbox** নির্বাচন করুন। এতে আপনার কম্পিউটারে ফাইলগুলো ডাউনলোড হবে।
3. আর্কাইভটি আনজিপ করুন, এর পর একটি টার্মিনাল খুলুন এবং যেই ডিরেক্টরি আনজিপ করেছেন সেখানে `cd` করুন।
4. `npm install` দিয়ে ডিপেন্ডেন্সিগুলো ইন্সটল করুন।
5. `npm start` রান করার মধ্য দিয়ে একটি লোকাল সার্ভার চালু করুন এবং প্রম্পট গুলো অনুসরণ করে ব্রাউজারে দেখুন কীভাবে কোডটা কাজ করছে।

কোথাও আটকে গেলে থেমে যাবেন না! আপাতত অনলাইনে অনুসরণ করুন, পরে আবার লোকাল সেটআপ চেষ্টা করতে পারেন।

</Note>

## সংক্ষিপ্ত বিবরণ {/*overview*/}

আপনার সেটাপ সম্পূর্ণ হয়েছে, চলুন React সম্পর্কে ধারণা লাভ করি!

### প্রাথমিক কোড দেখা {/*inspecting-the-starter-code*/}

CodeSandbox-এ আপনি তিনটি প্রধান সেকশন দেখতে পাবেন:

![প্রাথমিক কোডসহ CodeSandbox](../images/tutorial/react-starter-code-codesandbox.png)

1. _Files_ সেকশনে `App.js`, `index.js`, `styles.css`-এর মতো ফাইল এবং `public` নামের ফোল্ডার দেখতে পাবেন।
2. _Code Editor_-এ নির্বাচিত ফাইলের সোর্স কোড দেখতে পাবেন।
3. _Browser_ সেকশনে আপনার লেখা কোড ব্রাউজারে কেমন দেখায় তা দেখতে পাবেন।

Files সেকশনে `App.js` ফাইলটি নির্বাচিত থাকার কথা। _Code Editor_-এ এর কনটেন্ট হলো:

```jsx
export default function Square() {
  return <button className="square">X</button>;
}
```

_browser_ সেকশনে নিচের মত X সহ একটি বর্গ দেখানোর কথা:

![X লেখা একটি ঘর](../images/tutorial/x-filled-square.png)

এবার প্রাথমিক কোডের ফাইলগুলো দেখে নেওয়া যাক।

#### `App.js` {/*appjs*/}

`App.js`-এর কোড একটি *কম্পোনেন্ট* তৈরি করে। React-এ কম্পোনেন্ট হলো পুনর্ব্যবহারযোগ্য কোড, যা ইউজার ইন্টারফেসের কোনো অংশ নির্দেশ করে। কম্পোনেন্ট দিয়ে অ্যাপের UI এলিমেন্ট রেন্ডার, পরিচালনা ও আপডেট করা হয়। কী ঘটছে বুঝতে প্রতিটি লাইন আলাদা করে দেখা যাক:

```js {1}
export default function Square() {
  return <button className="square">X</button>;
}
```

প্রথম লাইনটি `Square` নামের একটি ফাংশন সংজ্ঞায়িত করে। জাভাস্ক্রিপ্টের `export` কীওয়ার্ড ফাংশনটি অন্য ফাইলে ব্যবহার করতে দেয়। `default` কীওয়ার্ড অন্য ফাইলগুলোকে জানায়, এটি এই ফাইলের প্রধান ফাংশন।

```js {2}
export default function Square() {
  return <button className="square">X</button>;
}
```

দ্বিতীয় লাইনটি একটি বাটন রিটার্ন করে। জাভাস্ক্রিপ্টের `return` কীওয়ার্ডটি বোঝায় যে এর পরে যা আসবে, তা function caller-এর মান হিসাবে রিটার্ন করা হয়। `<button>` হল একটি *JSX element*। JSX এলিমেন্ট হল জাভাস্ক্রিপ্ট কোড এবং HTML ট্যাগ-গুলির সমন্বয়, যা বর্ণনা করে আপনি প্রদর্শনের জন্য কী দেখাতে চান। `className="square"` হল একটি বাটন প্রপার্টি বা *prop*, যা CSS-কে বলে বাটনটিকে কিভাবে style করতে হবে। `X` হল বাটনের ভিতরে প্রদর্শিত টেক্সট এবং `</button>` JSX এলিমেন্টটিকে বন্ধ করে, এবং এর থেকে বোঝা যায় যে পরবর্তী কোনো কম্পোনেন্ট বাটন এর ভিতরে রাখা হবে না।

#### `styles.css` {/*stylescss*/}

CodeSandbox-এর _Files_ সেকশনে `styles.css` ফাইলে ক্লিক করুন। এতে React অ্যাপের স্টাইল লেখা আছে। প্রথম দুটি *CSS selector* (`*` ও `body`) অ্যাপের বড় অংশের স্টাইল নির্ধারণ করে। `.square` selector যেসব কম্পোনেন্টে `className` prop-এর মান `square`, তাদের স্টাইল নির্ধারণ করে। আপনার কোডে এটি `App.js`-এর `Square` কম্পোনেন্টের বাটনে প্রযোজ্য।

#### `index.js` {/*indexjs*/}

CodeSandbox-এর _Files_ সেকশনে `index.js` ফাইলে ক্লিক করুন। টিউটোরিয়ালে এই ফাইল সম্পাদনা করবেন না, তবে এটি `App.js`-এ তৈরি কম্পোনেন্ট ও ওয়েব ব্রাউজারের মধ্যে সংযোগ তৈরি করে।

```jsx
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';
```

১–৫ নম্বর লাইন প্রয়োজনীয় অংশগুলো নিয়ে আসে:

* React
* ওয়েব ব্রাউজারের সাথে কাজ করার জন্য React-এর লাইব্রেরি (React DOM)
* আপনার কম্পোনেন্টগুলোর স্টাইল
* `App.js`-এ তৈরি করা কম্পোনেন্ট।

ফাইলের বাকি অংশ সবকিছু একত্রিত করে এবং `public` ফোল্ডারের `index.html`-এ চূড়ান্ত ফলাফল যুক্ত করে।

### বোর্ড তৈরি করা {/*building-the-board*/}

আবার `App.js`-এ ফিরে যাওয়া যাক। টিউটোরিয়ালের বাকি কাজ এখানেই করবেন।

এখন বোর্ডে শুধু একটি ঘর আছে, কিন্তু প্রয়োজন নয়টি! যদি ঘরটি কপি-পেস্ট করে দুটি ঘর বানানোর চেষ্টা করেন, যেমন:

```js {2}
export default function Square() {
  return <button className="square">X</button><button className="square">X</button>;
}
```

আপনি এই এররটি পাবেন:

<ConsoleBlock level="error">

/src/App.js: Adjacent JSX এলিমেন্ট অবশ্যই enclosing ট্যাগ এ wrap করা থাকতে হবে। আপনি কি একটি JSX Fragment `<>...</>` চান?

</ConsoleBlock>

React কম্পোনেন্ট থেকে পাশাপাশি একাধিক JSX এলিমেন্টের (যেমন দুটি বাটন) বদলে একটি JSX এলিমেন্ট রিটার্ন করতে হয়। এটি ঠিক করতে *Fragment* (`<>` ও `</>`) দিয়ে একাধিক এলিমেন্ট একসাথে ঘিরে রাখতে পারেন:

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

এখন আপনি দেখবেন:

![X লেখা দুটি ঘর](../images/tutorial/two-x-filled-squares.png)

ভালো! এবার আরও কয়েকবার কপি-পেস্ট করে নয়টি ঘর যোগ করুন, তারপর...

![এক সারিতে X লেখা নয়টি ঘর](../images/tutorial/nine-x-filled-squares.png)

ওহ! বোর্ডের মতো গ্রিডে না থেকে সব ঘর এক সারিতে আছে। এটি ঠিক করতে `div` দিয়ে ঘরগুলোকে সারিতে সাজিয়ে কিছু CSS class যোগ করতে হবে। এবার প্রতিটি ঘরে একটি সংখ্যা দেবেন, যাতে কোন ঘর কোথায় দেখা যাচ্ছে বোঝা যায়।

`App.js`-এ `Square` কম্পোনেন্টটি এভাবে আপডেট করুন:

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

`styles.css`-এ সংজ্ঞায়িত CSS, `board-row` `className`-যুক্ত div-গুলোতে স্টাইল প্রয়োগ করে। স্টাইল করা `div` দিয়ে কম্পোনেন্টগুলোকে সারিতে সাজানোর ফলে এখন আপনার টিক-ট্যাক-টো বোর্ড তৈরি হয়েছে:

![১ থেকে ৯ পর্যন্ত সংখ্যা লেখা টিক-ট্যাক-টো বোর্ড](../images/tutorial/number-filled-board.png)

তবে এখন একটি সমস্যা হয়েছে। আপনার `Square` নামের কম্পোনেন্টটি আসলে আর একটি ঘর নয়। নামটি `Board` করে সমস্যাটি ঠিক করা যাক:

```js {1}
export default function Board() {
  //...
}
```

এ পর্যায়ে আপনার কোড অনেকটা এমন হওয়ার কথা:

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

এত কোড টাইপ করতে হবে! চাইলে এই পেইজ থেকে কোড কপি-পেস্ট করতে পারেন। তবে একটু চ্যালেঞ্জ নিতে চাইলে, আগে অন্তত একবার নিজে হাতে টাইপ করেছেন এমন কোডই শুধু কপি করার পরামর্শ দিচ্ছি।

</Note>

### props দিয়ে ডেটা পাঠানো {/*passing-data-through-props*/}

এরপর আপনি চাইবেন, ব্যবহারকারী কোনো ঘরে ক্লিক করলে সেটির মান ফাঁকা থেকে "X" হয়ে যাক। বোর্ডটি এখন যেভাবে তৈরি করেছেন, তাতে ঘর আপডেটের কোড নয়বার কপি-পেস্ট করতে হবে—প্রতিটি ঘরের জন্য একবার! React-এর কম্পোনেন্ট কাঠামো আপনাকে পুনর্ব্যবহারযোগ্য কম্পোনেন্ট তৈরি করতে দেয়, যাতে এমন অগোছালো ও পুনরাবৃত্ত কোড এড়ানো যায়।

প্রথমে, `Board` কম্পোনেন্ট থেকে প্রথম ঘরটি সংজ্ঞায়িত করা লাইনটি (`<button className="square">1</button>`) কপি করে নতুন একটি `Square` কম্পোনেন্টে রাখুন:

```js {1-3}
function Square() {
  return <button className="square">1</button>;
}

export default function Board() {
  // ...
}
```

তারপর JSX সিনট্যাক্স দিয়ে সেই `Square` কম্পোনেন্ট রেন্ডার করার জন্য `Board` কম্পোনেন্টটি আপডেট করুন:

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

খেয়াল করুন, ব্রাউজারের `div`-এর বিপরীতে আপনার নিজস্ব `Board` এবং `Square` কম্পোনেন্টের নাম বড় হাতের অক্ষর দিয়ে শুরু হতে হবে।

এবার ফলাফল দেখা যাক:

![প্রতিটি ঘরে ১ লেখা বোর্ড](../images/tutorial/board-filled-with-ones.png)

ওহ! আগের সংখ্যাযুক্ত ঘরগুলো আর নেই। এখন প্রতিটি ঘরেই "1" দেখা যাচ্ছে। এটি ঠিক করতে *props* ব্যবহার করে প্যারেন্ট কম্পোনেন্ট (`Board`) থেকে তার চাইল্ড (`Square`)-এ প্রতিটি ঘরের জন্য নির্ধারিত মান পাঠাবেন।

`Board` থেকে পাঠানো `value` prop পড়ার জন্য `Square` কম্পোনেন্টটি আপডেট করুন:

```js {1}
function Square({ value }) {
  return <button className="square">1</button>;
}
```

`function Square({ value })` নির্দেশ করে যে `Square` কম্পোনেন্টে `value` নামের একটি prop পাস করা যায়।

এখন প্রতিটি ঘরের ভেতরে `1`-এর বদলে সেই `value` দেখাতে চান। এভাবে চেষ্টা করুন:

```js {2}
function Square({ value }) {
  return <button className="square">value</button>;
}
```

ওহ, আপনি তো এটি চাননি:

![প্রতিটি ঘরে value লেখা বোর্ড](../images/tutorial/board-filled-with-value.png)

আপনি কম্পোনেন্টের `value` নামের জাভাস্ক্রিপ্ট ভ্যারিয়েবলটি রেন্ডার করতে চেয়েছিলেন, "value" শব্দটি নয়। JSX থেকে জাভাস্ক্রিপ্ট ব্যবহার করতে কার্লি ব্রেস লাগে। JSX-এ `value`-এর চারপাশে এভাবে কার্লি ব্রেস যোগ করুন:

```js {2}
function Square({ value }) {
  return <button className="square">{value}</button>;
}
```

আপাতত একটি ফাঁকা বোর্ড দেখার কথা:

![ফাঁকা বোর্ড](../images/tutorial/empty-board.png)

এর কারণ, `Board` কম্পোনেন্ট এখনও তার রেন্ডার করা প্রতিটি `Square` কম্পোনেন্টে `value` prop পাস করেনি। এটি ঠিক করতে `Board`-এর রেন্ডার করা প্রতিটি `Square` কম্পোনেন্টে `value` prop যোগ করুন:

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

এখন আবার সংখ্যার একটি গ্রিড দেখতে পাবেন:

![১ থেকে ৯ পর্যন্ত সংখ্যা লেখা টিক-ট্যাক-টো বোর্ড](../images/tutorial/number-filled-board.png)

আপনার আপডেট করা কোড এমন হওয়ার কথা:

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

### একটি ইন্টারঅ্যাক্টিভ কম্পোনেন্ট তৈরি করা {/*making-an-interactive-component*/}

ক্লিক করলে `Square` কম্পোনেন্টটিতে একটি `X` বসানো যাক। `Square`-এর ভেতরে `handleClick` নামের একটি ফাংশন ডিক্লেয়ার করুন। তারপর `Square` থেকে রিটার্ন করা বাটনের JSX এলিমেন্টের props-এ `onClick` যোগ করুন:

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

এখন কোনো ঘরে ক্লিক করলে CodeSandbox-এর _Browser_ সেকশনের নিচে _Console_ ট্যাবে `"clicked!"` লেখা একটি লগ দেখার কথা। একাধিকবার ক্লিক করলে আবার `"clicked!"` লগ হবে। একই বার্তার পুনরাবৃত্ত লগ কনসোলে নতুন লাইন তৈরি করে না। তার বদলে প্রথম `"clicked!"` লগের পাশে একটি সংখ্যা বাড়তে দেখবেন।

<Note>

নিজের লোকাল ডেভেলপমেন্ট পরিবেশে এই টিউটোরিয়াল অনুসরণ করলে ব্রাউজারের Console খুলতে হবে। যেমন, Chrome-এ **Shift + Ctrl + J** (Windows/Linux) বা **Option + ⌘ + J** (macOS) কিবোর্ড শর্টকাট দিয়ে Console দেখতে পারেন।

</Note>

পরের ধাপে আপনি চান, `Square` কম্পোনেন্টটি মনে রাখুক যে এতে ক্লিক করা হয়েছে এবং একটি "X" চিহ্ন দেখাক। কোনো কিছু মনে রাখতে কম্পোনেন্ট *state* ব্যবহার করে।

React-এর `useState` নামের একটি বিশেষ ফাংশন আছে, যা কম্পোনেন্ট থেকে কল করে কোনো কিছু মনে রাখা যায়। `Square`-এর বর্তমান মান state-এ রাখা যাক এবং `Square`-এ ক্লিক করলে সেটি বদলানো যাক।

ফাইলের শুরুতে `useState` ইমপোর্ট করুন। `Square` কম্পোনেন্ট থেকে `value` prop সরিয়ে দিন। তার বদলে `Square`-এর শুরুতে নতুন একটি লাইনে `useState` কল করুন। এর রিটার্ন করা state ভ্যারিয়েবলের নাম দিন `value`:

```js {1,3,4}
import { useState } from 'react';

function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
    //...
```

`value` মানটি ধরে রাখে এবং `setValue` হলো সেই মান পরিবর্তনের ফাংশন। `useState`-এ পাস করা `null` এই state ভ্যারিয়েবলের প্রাথমিক মান হিসেবে ব্যবহৃত হয়। তাই এখানে `value` শুরুতে `null` থাকে।

`Square` কম্পোনেন্ট আর props গ্রহণ করে না। তাই `Board` কম্পোনেন্টের তৈরি নয়টি `Square` কম্পোনেন্ট থেকেই `value` prop সরিয়ে দিন:

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

এবার ক্লিক করলে "X" দেখানোর জন্য `Square` বদলাবেন। event handler-এর `console.log("clicked!");`-এর বদলে `setValue('X');` লিখুন। এখন `Square` কম্পোনেন্টটি এমন হবে:

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

`onClick` handler থেকে এই `set` ফাংশন কল করে আপনি React-কে বলছেন, `<button>`-এ ক্লিক হলে সেই `Square` আবার রেন্ডার করতে। আপডেটের পরে `Square`-এর `value` হবে `'X'`, তাই গেম বোর্ডে "X" দেখবেন। যেকোনো ঘরে ক্লিক করলে সেখানে "X" দেখা যাওয়ার কথা:

![বোর্ডে X যোগ করা](../images/tutorial/tictac-adding-x-s.gif)

প্রতিটি `Square`-এর নিজস্ব state আছে: একেকটি ঘরে রাখা `value` অন্য ঘরগুলোর মান থেকে সম্পূর্ণ স্বাধীন। কোনো কম্পোনেন্টে `set` ফাংশন কল করলে React তার ভেতরের চাইল্ড কম্পোনেন্টগুলোও স্বয়ংক্রিয়ভাবে আপডেট করে।

উপরের পরিবর্তনগুলোর পরে কোড এমন হবে:

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

React Developer Tools দিয়ে আপনার React কম্পোনেন্টের props ও state পরীক্ষা করতে পারেন। এটি [Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en), [Firefox](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/) এবং [Edge](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)-এর ব্রাউজার এক্সটেনশন হিসেবে পাওয়া যায়।

ইনস্টল করার পরে React ব্যবহার করা সাইটে ব্রাউজারের Developer Tools-এ নতুন একটি *Components* ট্যাব দেখা যাবে। CodeSandbox-এ অনুসরণ করলে প্রথমে sandbox-এর preview নতুন ট্যাবে খুলতে হবে:

![নতুন ট্যাবে preview খোলা](../images/tutorial/sandbox-new-tab.png)

তারপর preview পেইজে ব্রাউজারের DevTools খুলে *Components* ট্যাবটি খুঁজুন:

![Components ট্যাব](../images/tutorial/components-tab.png)

স্ক্রিনের নির্দিষ্ট কোনো কম্পোনেন্ট পরীক্ষা করতে Components ট্যাবের উপরের বাঁ কোণের বাটনটি ব্যবহার করুন:

![DevTools দিয়ে কম্পোনেন্ট পরীক্ষা করা](../images/tutorial/devtools-inspect.gif)


## গেমটি সম্পূর্ণ করা {/*completing-the-game*/}

এতক্ষণে আপনার টিক-ট্যাক-টো গেমের মৌলিক অংশগুলো তৈরি হয়েছে। গেমটি সম্পূর্ণ করতে এখন বোর্ডে পালাক্রমে "X" ও "O" বসাতে হবে এবং বিজয়ী নির্ধারণের ব্যবস্থা করতে হবে।

### state উপরে তুলে নেওয়া {/*lifting-state-up*/}

এখন প্রতিটি `Square` কম্পোনেন্ট গেমের state-এর একটি অংশ রাখে। টিক-ট্যাক-টো গেমে বিজয়ী নির্ধারণ করতে `Board`-কে কোনোভাবে নয়টি `Square` কম্পোনেন্টের প্রতিটির state জানতে হবে।

এটি কীভাবে করবেন? প্রথমে মনে হতে পারে, `Board` প্রতিটি `Square`-কে তার state জিজ্ঞেস করবে। React-এ এটি সম্ভব হলেও আমরা নিরুৎসাহিত করি, কারণ এতে কোড বোঝা ও রিফ্যাক্টর করা কঠিন হয় এবং বাগের ঝুঁকি বাড়ে। সবচেয়ে ভালো উপায় হলো প্রতিটি `Square`-এ না রেখে প্যারেন্ট `Board` কম্পোনেন্টে গেমের state রাখা। আগে প্রতিটি ঘরে সংখ্যা পাঠানোর মতোই, `Board` prop পাস করে প্রতিটি `Square`-কে বলতে পারে কী দেখাতে হবে।

**একাধিক চাইল্ড থেকে ডেটা সংগ্রহ করতে বা দুটি চাইল্ড কম্পোনেন্টের মধ্যে যোগাযোগ করাতে, তাদের ভাগ করা state প্যারেন্ট কম্পোনেন্টে ডিক্লেয়ার করুন। প্যারেন্ট সেই state props দিয়ে চাইল্ডদের কাছে পাঠাতে পারে। এতে চাইল্ড কম্পোনেন্টগুলো নিজেদের মধ্যে এবং প্যারেন্টের সাথে সিঙ্কে থাকে।**

React কম্পোনেন্ট রিফ্যাক্টর করার সময় প্যারেন্ট কম্পোনেন্টে state তুলে নেওয়া খুবই সাধারণ ব্যাপার।

এবার সেটি চেষ্টা করে দেখা যাক। `Board` কম্পোনেন্টে `squares` নামের একটি state ভ্যারিয়েবল ডিক্লেয়ার করুন। এর ডিফল্ট মান হবে নয়টি `null`-এর অ্যারে, যা নয়টি ঘরকে নির্দেশ করবে:

```js {3}
// ...
export default function Board() {
  const [squares, setSquares] = useState(Array(9).fill(null));
  return (
    // ...
  );
}
```

`Array(9).fill(null)` নয়টি এলিমেন্টের অ্যারে তৈরি করে প্রতিটির মান `null` করে। একে ঘিরে থাকা `useState()` কলটি `squares` state ভ্যারিয়েবল ডিক্লেয়ার করে, যার প্রাথমিক মান ওই অ্যারে। অ্যারের প্রতিটি এন্ট্রি একটি ঘরের মান নির্দেশ করে। পরে বোর্ডে ঘর পূরণ করলে `squares` অ্যারে এমন দেখাবে:

```jsx
['O', null, 'X', 'X', 'X', 'O', 'O', null, null]
```

এখন `Board`-এর রেন্ডার করা প্রতিটি `Square`-এ `value` prop পাঠাতে হবে:

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

এরপর `Board` থেকে `value` prop গ্রহণ করার জন্য `Square` বদলান। এজন্য `Square`-এর নিজস্ব state-এ `value` রাখার ব্যবস্থা এবং বাটনের `onClick` prop সরাতে হবে:

```js {1,2}
function Square({value}) {
  return <button className="square">{value}</button>;
}
```

এ পর্যায়ে একটি ফাঁকা টিক-ট্যাক-টো বোর্ড দেখার কথা:

![ফাঁকা বোর্ড](../images/tutorial/empty-board.png)

আপনার কোড এমন হওয়ার কথা:

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

এখন প্রতিটি `Square` একটি `value` prop পাবে, যার মান হবে `'X'`, `'O'` অথবা ফাঁকা ঘরের জন্য `null`।

এরপর `Square`-এ ক্লিক করলে কী ঘটবে তা বদলাতে হবে। কোন ঘরগুলো পূর্ণ, সেটি এখন `Board` রাখে। তাই `Square` থেকে `Board`-এর state আপডেট করার ব্যবস্থা লাগবে। state সেটি ডিক্লেয়ার করা কম্পোনেন্টের নিজস্ব হওয়ায়, `Square` থেকে সরাসরি `Board`-এর state আপডেট করা যায় না।

তার বদলে `Board` থেকে `Square`-এ একটি ফাংশন পাঠাবেন, আর ঘরে ক্লিক হলে `Square` সেটি কল করবে। প্রথমে `Square`-এ ক্লিক হলে যে ফাংশন কল হবে, সেটি দিয়ে শুরু করা যাক। এর নাম দিন `onSquareClick`:

```js {3}
function Square({ value }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}
```

এরপর `Square` কম্পোনেন্টের props-এ `onSquareClick` ফাংশনটি যোগ করুন:

```js {1}
function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}
```

এখন `onSquareClick` prop-কে `Board`-এর একটি ফাংশনের সাথে যুক্ত করবেন, যার নাম হবে `handleClick`। এজন্য প্রথম `Square` কম্পোনেন্টের `onSquareClick` prop-এ একটি ফাংশন পাস করুন:

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

সবশেষে বোর্ডের state রাখা `squares` অ্যারে আপডেট করতে `Board`-এর ভেতরে `handleClick` ফাংশনটি সংজ্ঞায়িত করুন:

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

`handleClick` জাভাস্ক্রিপ্ট অ্যারের `slice()` মেথড দিয়ে `squares`-এর একটি কপি (`nextSquares`) তৈরি করে। তারপর `nextSquares` আপডেট করে প্রথম ঘরে (`[0]` ইনডেক্সে) `X` যোগ করে।

`setSquares` কল করলে React জানতে পারে কম্পোনেন্টের state বদলেছে। এতে `squares` state ব্যবহার করা কম্পোনেন্ট (`Board`) এবং তার চাইল্ড কম্পোনেন্টগুলো (বোর্ডের `Square`-গুলো) আবার রেন্ডার হয়।

<Note>

জাভাস্ক্রিপ্ট [closure](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) সমর্থন করে। অর্থাৎ, ভেতরের কোনো ফাংশন (যেমন `handleClick`) বাইরের ফাংশনে (যেমন `Board`) সংজ্ঞায়িত ভ্যারিয়েবল ও ফাংশন ব্যবহার করতে পারে। `handleClick`, `squares` state পড়তে এবং `setSquares` মেথড কল করতে পারে, কারণ দুটোই `Board` ফাংশনের ভেতরে সংজ্ঞায়িত।

</Note>

এখন বোর্ডে X বসাতে পারেন, তবে শুধু উপরের বাঁ দিকের ঘরে। `handleClick`-এ শুধু ওই ঘরের ইনডেক্স (`0`) আপডেট করার কথা সরাসরি লেখা আছে। যেকোনো ঘর আপডেট করার জন্য `handleClick` বদলানো যাক। আপডেট করতে চাওয়া ঘরের ইনডেক্স নিতে `i` নামের একটি আর্গুমেন্ট যোগ করুন:

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

এরপর ওই `i` মানটি `handleClick`-এ পাস করতে হবে। JSX-এ সরাসরি এভাবে `onSquareClick` prop-এর মান `handleClick(0)` দেওয়ার চেষ্টা করতে পারেন, তবে এটি কাজ করবে না:

```jsx
<Square value={squares[0]} onSquareClick={handleClick(0)} />
```

কেন কাজ করবে না দেখা যাক। বোর্ড রেন্ডার করার সময়ই `handleClick(0)` কলটি চলবে। এটি `setSquares` কল করে বোর্ডের state বদলায় বলে পুরো বোর্ড আবার রেন্ডার হবে। কিন্তু তখন আবার `handleClick(0)` চলবে, ফলে একটি অসীম লুপ তৈরি হবে:

<ConsoleBlock level="error">

Too many re-renders. React limits the number of renders to prevent an infinite loop.

</ConsoleBlock>

আগে এই সমস্যা হয়নি কেন?

যখন `onSquareClick={handleClick}` লিখছিলেন, তখন `handleClick` ফাংশনটিকে prop হিসেবে পাঠাচ্ছিলেন, কল করছিলেন না! এখন ফাংশনটি সঙ্গে সঙ্গেই *কল* করছেন—`handleClick(0)`-এর বন্ধনীগুলো খেয়াল করুন—তাই এটি সময়ের আগেই চলছে। ব্যবহারকারী ক্লিক করার আগে আপনি `handleClick` কল করতে *চান না*!

`handleClick(0)` কল করে এমন `handleFirstSquareClick`, `handleClick(1)` কল করে এমন `handleSecondSquareClick` ইত্যাদি ফাংশন তৈরি করে সমস্যাটি মেটাতে পারেন। এগুলো কল না করে prop হিসেবে পাঠাবেন, যেমন `onSquareClick={handleFirstSquareClick}`। এতে অসীম লুপ বন্ধ হবে।

তবে নয়টি আলাদা ফাংশন সংজ্ঞায়িত করে প্রতিটির নাম দেওয়া বেশ দীর্ঘ ব্যাপার। তার বদলে এভাবে করা যাক:

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

নতুন `() =>` সিনট্যাক্স খেয়াল করুন। এখানে `() => handleClick(0)` একটি *arrow function*, যা ফাংশন সংজ্ঞায়িত করার সংক্ষিপ্ত উপায়। ঘরে ক্লিক হলে `=>` তীরচিহ্নের পরের কোড চলে এবং `handleClick(0)` কল হয়।

এবার বাকি আটটি ঘরেও পাস করা arrow function থেকে `handleClick` কল করার ব্যবস্থা করুন। প্রতিটি কলের আর্গুমেন্ট যেন সংশ্লিষ্ট ঘরের ইনডেক্স হয়, তা নিশ্চিত করুন:

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

এখন আবার বোর্ডের যেকোনো ঘরে ক্লিক করে X বসাতে পারেন:

![বোর্ডে X বসানো](../images/tutorial/tictac-adding-x-s.gif)

তবে এবার state পরিচালনার সব কাজ `Board` কম্পোনেন্ট করছে!

এখন কোড এমন হওয়ার কথা:

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

state এখন `Board`-এ থাকায়, প্যারেন্ট `Board` চাইল্ড `Square`-গুলোকে props পাঠায়, যাতে তারা সঠিক মান দেখাতে পারে। কোনো `Square`-এ ক্লিক হলে সেই চাইল্ড প্যারেন্ট `Board`-কে বোর্ডের state আপডেট করতে বলে। `Board`-এর state বদলালে `Board` এবং প্রতিটি চাইল্ড `Square` স্বয়ংক্রিয়ভাবে আবার রেন্ডার হয়। সব ঘরের state `Board`-এ রাখায় পরে সেটি বিজয়ী নির্ধারণ করতে পারবে।

উপরের বাঁ দিকের ঘরে `X` বসাতে ক্লিক করলে কী ঘটে, আবার দেখা যাক:

1. উপরের বাঁ দিকের ঘরে ক্লিক করলে `button`, `Square` থেকে `onClick` prop হিসেবে পাওয়া ফাংশনটি চালায়। `Square` সেটি `Board` থেকে `onSquareClick` prop হিসেবে পেয়েছিল। `Board` JSX-এর মধ্যেই ফাংশনটি সংজ্ঞায়িত করেছে। এটি `0` আর্গুমেন্ট দিয়ে `handleClick` কল করে।
1. `handleClick` আর্গুমেন্টটি (`0`) ব্যবহার করে `squares` অ্যারের প্রথম এলিমেন্ট `null` থেকে `X` করে।
1. `Board`-এর `squares` state আপডেট হয়, তাই `Board` ও তার সব চাইল্ড আবার রেন্ডার হয়। এতে `0` ইনডেক্সের `Square`-এর `value` prop `null` থেকে `X` হয়ে যায়।

শেষ পর্যন্ত ব্যবহারকারী দেখেন, ক্লিক করার পরে উপরের বাঁ দিকের ফাঁকা ঘরটিতে `X` বসেছে।

<Note>

DOM-এর `<button>` একটি বিল্ট-ইন কম্পোনেন্ট হওয়ায় এর `onClick` অ্যাট্রিবিউটের React-এ বিশেষ অর্থ আছে। `Square`-এর মতো নিজস্ব কম্পোনেন্টের ক্ষেত্রে নাম আপনার ইচ্ছাধীন। `Square`-এর `onSquareClick` prop বা `Board`-এর `handleClick` ফাংশনের যেকোনো নাম দিলেও কোড একইভাবে কাজ করবে। React-এ ইভেন্ট বোঝানো prop-এর জন্য `onSomething` এবং সেই ইভেন্ট সামলানো ফাংশনের জন্য `handleSomething` ধরনের নাম ব্যবহার করার প্রচলন আছে।

</Note>

### ইমিউটেবিলিটি কেন গুরুত্বপূর্ণ {/*why-immutability-is-important*/}

খেয়াল করুন, `handleClick`-এ বিদ্যমান `squares` অ্যারে না বদলে `.slice()` কল করে একটি কপি তৈরি করছেন। এর কারণ বুঝতে ইমিউটেবিলিটি কী এবং কেন এটি শেখা গুরুত্বপূর্ণ, তা আলোচনা করতে হবে।

ডেটা পরিবর্তনের সাধারণত দুটি উপায় আছে। প্রথমটি হলো সরাসরি মান বদলে ডেটা *mutate* করা। দ্বিতীয়টি হলো প্রয়োজনীয় পরিবর্তনসহ নতুন কপি দিয়ে পুরোনো ডেটা প্রতিস্থাপন করা। `squares` অ্যারে mutate করলে তা এমন হবে:

```jsx
const squares = [null, null, null, null, null, null, null, null, null];
squares[0] = 'X';
// Now `squares` is ["X", null, null, null, null, null, null, null, null];
```

আর `squares` অ্যারে mutate না করে ডেটা বদলালে এমন হবে:

```jsx
const squares = [null, null, null, null, null, null, null, null, null];
const nextSquares = ['X', null, null, null, null, null, null, null, null];
// Now `squares` is unchanged, but `nextSquares` first element is 'X' rather than `null`
```

ফলাফল একই, তবে সরাসরি মূল ডেটা পরিবর্তন না করায় বেশ কিছু সুবিধা পাওয়া যায়।

ইমিউটেবিলিটি জটিল ফিচার তৈরি সহজ করে। এই টিউটোরিয়ালের পরের অংশে "সময় ভ্রমণ" ফিচার যোগ করবেন, যাতে গেমের ইতিহাস দেখে আগের চালে ফিরে যাওয়া যায়। এটি শুধু গেমের বিষয় নয়—কোনো কাজ undo বা redo করার সুবিধা অ্যাপেও প্রায়ই লাগে। সরাসরি ডেটা mutate না করলে আগের সংস্করণগুলো অক্ষত রাখা এবং পরে আবার ব্যবহার করা যায়।

ইমিউটেবিলিটির আরেকটি সুবিধাও আছে। ডিফল্টভাবে প্যারেন্টের state বদলালে সব চাইল্ড কম্পোনেন্ট স্বয়ংক্রিয়ভাবে আবার রেন্ডার হয়, এমনকি পরিবর্তনের প্রভাব না পড়া চাইল্ডগুলোও। রি-রেন্ডার নিজে সাধারণত ব্যবহারকারীর চোখে পড়ে না (তাই সবসময় এটি এড়ানোর চেষ্টা করার দরকার নেই!)। তবে পারফরম্যান্সের জন্য ট্রির প্রভাবিত হয়নি এমন অংশের রি-রেন্ডার এড়াতে চাইতে পারেন। ইমিউটেবিলিটি থাকলে ডেটা বদলেছে কি না তুলনা করা কম্পোনেন্টের জন্য খুব সহজ হয়। React কখন কোনো কম্পোনেন্ট আবার রেন্ডার করবে তা কীভাবে ঠিক করে, সে বিষয়ে [ `memo` API রেফারেন্সে](/reference/react/memo) আরও জানতে পারেন।

### পালাক্রমে চাল দেওয়া {/*taking-turns*/}

এবার গেমের একটি বড় সমস্যা ঠিক করার সময়: বোর্ডে "O" বসানো যাচ্ছে না।

ডিফল্টভাবে প্রথম চালটি হবে "X"। এটি মনে রাখতে `Board`-এ আরেকটি state যোগ করা যাক:

```js {2}
function Board() {
  const [xIsNext, setXIsNext] = useState(true);
  const [squares, setSquares] = useState(Array(9).fill(null));

  // ...
}
```

প্রতিবার কোনো খেলোয়াড় চাল দিলে `xIsNext` boolean-এর মান উল্টে পরের খেলোয়াড় নির্ধারণ করা হবে এবং গেমের state সংরক্ষণ করা হবে। `xIsNext`-এর মান উল্টাতে `Board`-এর `handleClick` আপডেট করুন:

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

এখন বিভিন্ন ঘরে ক্লিক করলে প্রত্যাশামতো পালাক্রমে `X` ও `O` বসবে!

তবে একটু দাঁড়ান, একটি সমস্যা আছে। একই ঘরে কয়েকবার ক্লিক করে দেখুন:

![X-এর জায়গায় O বসে যাচ্ছে](../images/tutorial/o-replaces-x.gif)

`X`-এর জায়গায় `O` বসে যাচ্ছে! এতে গেমে মজার মোড় যোগ হতে পারে, তবে আপাতত মূল নিয়মেই থাকব।

কোনো ঘরে `X` বা `O` বসানোর আগে সেটিতে ইতিমধ্যে `X` বা `O` আছে কি না পরীক্ষা করছেন না। *আগেই return করে* এটি ঠিক করতে পারেন। ঘরটি পূর্ণ কি না পরীক্ষা করুন। পূর্ণ থাকলে `handleClick`-এ বোর্ডের state আপডেট করার আগেই `return` করুন।

```js {2,3,4}
function handleClick(i) {
  if (squares[i]) {
    return;
  }
  const nextSquares = squares.slice();
  //...
}
```

এখন শুধু ফাঁকা ঘরেই `X` বা `O` বসানো যাবে! এ পর্যায়ে কোড এমন হওয়ার কথা:

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

### বিজয়ী ঘোষণা করা {/*declaring-a-winner*/}

এখন খেলোয়াড়রা পালাক্রমে চাল দিতে পারেন। কেউ জিতলে এবং আর কোনো চাল দেওয়া না গেলে তা দেখাতে হবে। এজন্য `calculateWinner` নামের একটি সহায়ক ফাংশন যোগ করবেন। এটি নয়টি ঘরের অ্যারে নিয়ে বিজয়ী পরীক্ষা করে প্রয়োজনমতো `'X'`, `'O'` বা `null` রিটার্ন করবে। ফাংশনটি নিয়ে খুব বেশি ভাবার দরকার নেই; এটি React-এর বিশেষ কিছু নয়:

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

`calculateWinner`, `Board`-এর আগে নাকি পরে সংজ্ঞায়িত করছেন, তাতে কিছু যায় আসে না। শেষে রাখা যাক, যাতে প্রতিবার কম্পোনেন্ট সম্পাদনা করতে গিয়ে এটি পেরিয়ে স্ক্রল করতে না হয়।

</Note>

কেউ জিতেছেন কি না পরীক্ষা করতে `Board`-এর `handleClick`-এ `calculateWinner(squares)` কল করুন। ব্যবহারকারী ইতিমধ্যে `X` বা `O` বসানো ঘরে ক্লিক করেছেন কি না পরীক্ষার সাথেই এটি করতে পারেন। দুই ক্ষেত্রেই আগে return করতে চাই:

```js {2}
function handleClick(i) {
  if (squares[i] || calculateWinner(squares)) {
    return;
  }
  const nextSquares = squares.slice();
  //...
}
```

গেম শেষ হলে খেলোয়াড়দের জানাতে "Winner: X" বা "Winner: O"-এর মতো টেক্সট দেখাতে পারেন। এজন্য `Board`-এ একটি `status` অংশ যোগ করুন। গেম শেষ হলে এটি বিজয়ী দেখাবে, আর গেম চলতে থাকলে পরের চাল কার তা দেখাবে:

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

অভিনন্দন! আপনার টিক-ট্যাক-টো গেম এখন কাজ করছে। সেই সাথে React-এর মৌলিক বিষয়গুলোও শিখে ফেলেছেন। তাই আসল বিজয়ী *আপনিই*। এখন কোড এমন হওয়ার কথা:

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

## সময় ভ্রমণ যোগ করা {/*adding-time-travel*/}

শেষ অনুশীলন হিসেবে, গেমের আগের চালগুলোতে ফিরে যাওয়ার ব্যবস্থা করা যাক।

### চালের ইতিহাস সংরক্ষণ করা {/*storing-a-history-of-moves*/}

`squares` অ্যারে mutate করলে সময় ভ্রমণ তৈরি করা খুব কঠিন হতো।

তবে প্রতিটি চালের পরে `slice()` দিয়ে `squares`-এর নতুন কপি তৈরি করেছেন এবং সেটিকে immutable হিসেবে বিবেচনা করেছেন। ফলে `squares`-এর প্রতিটি আগের সংস্করণ সংরক্ষণ করতে এবং আগের চালগুলোর মধ্যে যাতায়াত করতে পারবেন।

আগের `squares` অ্যারেগুলো `history` নামের আরেকটি অ্যারেতে রাখবেন, যা নতুন একটি state ভ্যারিয়েবলে সংরক্ষিত হবে। `history` প্রথম থেকে শেষ চাল পর্যন্ত বোর্ডের সব অবস্থা নির্দেশ করে। এর গঠন এমন:

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

### আবারও state উপরে তুলে নেওয়া {/*lifting-state-up-again*/}

এবার আগের চালগুলোর তালিকা দেখাতে `Game` নামের নতুন একটি টপ-লেভেল কম্পোনেন্ট লিখবেন। পুরো গেমের ইতিহাস রাখা `history` state সেখানেই থাকবে।

`history`, `Game`-এ রাখলে চাইল্ড `Board` থেকে `squares` state সরাতে পারবেন। আগে যেমন `Square` থেকে `Board`-এ state তুলে নিয়েছিলেন, এবার `Board` থেকে টপ-লেভেল `Game`-এ তুলবেন। এতে `Game` বোর্ডের ডেটা পুরোপুরি নিয়ন্ত্রণ করতে পারবে এবং `history` থেকে আগের চাল রেন্ডার করতে `Board`-কে নির্দেশ দিতে পারবে।

প্রথমে `export default`-সহ একটি `Game` কম্পোনেন্ট যোগ করুন। সেটি দিয়ে `Board` এবং কিছু মার্কআপ রেন্ডার করুন:

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

খেয়াল করুন, `function Board() {`-এর আগে থেকে `export default` সরিয়ে `function Game() {`-এর আগে যোগ করছেন। এতে `index.js` টপ-লেভেল কম্পোনেন্ট হিসেবে `Board`-এর বদলে `Game` ব্যবহার করবে। `Game` থেকে রিটার্ন করা অতিরিক্ত `div`-গুলোতে পরে গেমের তথ্য রাখবেন।

পরের খেলোয়াড় কে এবং চালের ইতিহাস কী, তা রাখতে `Game`-এ state যোগ করুন:

```js {2-3}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  // ...
```

খেয়াল করুন, `[Array(9).fill(null)]` একটি মাত্র এলিমেন্টের অ্যারে; সেই এলিমেন্টটিই আবার নয়টি `null`-এর অ্যারে।

বর্তমান চালের ঘরগুলো রেন্ডার করতে `history` থেকে শেষ `squares` অ্যারেটি পড়তে হবে। এজন্য `useState` লাগে না—রেন্ডার করার সময় হিসাব করার মতো যথেষ্ট তথ্য ইতিমধ্যে আছে:

```js {4}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const currentSquares = history[history.length - 1];
  // ...
```

এরপর `Game`-এ `handlePlay` ফাংশন তৈরি করুন, যা গেম আপডেট করতে `Board` কল করবে। `xIsNext`, `currentSquares` এবং `handlePlay`, props হিসেবে `Board`-এ পাঠান:

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

`Board`-কে পুরোপুরি তার পাওয়া props দিয়ে নিয়ন্ত্রণ করা যাক। এটিকে তিনটি prop নিতে দিন: `xIsNext`, `squares` এবং নতুন `onPlay` ফাংশন। খেলোয়াড় চাল দিলে `Board` আপডেট করা ঘরের অ্যারে দিয়ে `onPlay` কল করবে। তারপর `Board`-এর শুরুতে `useState` কল করা দুটি লাইন সরান:

```js {1}
function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    //...
  }
  // ...
}
```

এখন `Board`-এর `handleClick`-এ `setSquares` ও `setXIsNext` কলের বদলে শুধু নতুন `onPlay` ফাংশনটি কল করুন। এতে ব্যবহারকারী ঘরে ক্লিক করলে `Game`, `Board` আপডেট করতে পারবে:

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

`Board` এখন `Game` থেকে পাওয়া props দিয়ে পুরোপুরি নিয়ন্ত্রিত। গেম আবার চালু করতে `Game`-এর `handlePlay` ফাংশনটি ইমপ্লিমেন্ট করতে হবে।

`handlePlay` কল হলে কী করবে? মনে করুন, আগে `Board` আপডেট করা অ্যারে দিয়ে `setSquares` কল করত; এখন আপডেট করা `squares` অ্যারে `onPlay`-এ পাঠায়।

রি-রেন্ডার ঘটাতে `handlePlay`-কে `Game`-এর state আপডেট করতে হবে। তবে এখন আর `setSquares` নেই—এই তথ্য রাখতে `history` state ব্যবহার করছেন। আপডেট করা `squares` অ্যারেটি নতুন এন্ট্রি হিসেবে শেষে যোগ করে `history` আপডেট করবেন। আগে `Board` যেমন করত, তেমনি `xIsNext`-এর মানও উল্টাবেন:

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

এখানে `[...history, nextSquares]` নতুন একটি অ্যারে তৈরি করে। এতে প্রথমে `history`-র সব এলিমেন্ট এবং শেষে `nextSquares` থাকে। (`...history`-এর [*spread syntax*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)-কে "`history`-র সব এলিমেন্ট বের করে দেওয়া" হিসেবে ভাবতে পারেন।)

যেমন, `history` যদি `[[null,null,null], ["X",null,null]]` এবং `nextSquares` যদি `["X",null,"O"]` হয়, তবে নতুন `[...history, nextSquares]` অ্যারে হবে `[[null,null,null], ["X",null,null], ["X",null,"O"]]`।

এ পর্যায়ে state `Game`-এ সরিয়েছেন, আর রিফ্যাক্টর করার আগের মতোই UI পুরোপুরি কাজ করার কথা। কোড এখন এমন হবে:

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

### আগের চালগুলো দেখানো {/*showing-the-past-moves*/}

টিক-ট্যাক-টো গেমের ইতিহাস সংরক্ষণ করছেন বলে এখন খেলোয়াড়কে আগের চালগুলোর তালিকা দেখাতে পারেন।

`<button>`-এর মতো React এলিমেন্ট সাধারণ জাভাস্ক্রিপ্ট অবজেক্ট; অ্যাপ্লিকেশনের বিভিন্ন জায়গায় এগুলো পাস করতে পারেন। React-এ একাধিক আইটেম রেন্ডার করতে React এলিমেন্টের অ্যারে ব্যবহার করতে পারেন।

চালের `history` অ্যারে ইতিমধ্যে state-এ আছে। এখন সেটিকে React এলিমেন্টের অ্যারেতে রূপান্তর করতে হবে। জাভাস্ক্রিপ্টে একটি অ্যারেকে অন্যটিতে রূপান্তর করতে [অ্যারের `map` মেথড](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) ব্যবহার করা যায়:

```jsx
[1, 2, 3].map((x) => x * 2) // [2, 4, 6]
```

`map` দিয়ে চালের `history`-কে স্ক্রিনে বাটন দেখানো React এলিমেন্টে রূপান্তর করবেন। এই বাটনের তালিকা দিয়ে আগের চালে ফিরে যাওয়া যাবে। `Game`-এ `history`-র উপর `map` চালানো যাক:

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

নিচে কোডটি কেমন হবে দেখতে পারেন। খেয়াল করুন, developer tools-এর console-এ এমন একটি error দেখার কথা:

<ConsoleBlock level="warning">
Warning: Each child in an array or iterator should have a unique "key" prop. Check the render method of &#96;Game&#96;.
</ConsoleBlock>

পরের অংশে এই error ঠিক করবেন।

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

`map`-এ পাস করা ফাংশনের ভেতরে `history` অ্যারে ঘুরে দেখার সময় `squares` আর্গুমেন্টটি `history`-র প্রতিটি এলিমেন্ট পায়, আর `move` আর্গুমেন্টটি প্রতিটি ইনডেক্স পায়: `0`, `1`, `2`, …। সাধারণত অ্যারের আসল এলিমেন্ট প্রয়োজন হলেও, চালের তালিকা দেখাতে শুধু ইনডেক্সই লাগবে।

গেমের ইতিহাসের প্রতিটি চালের জন্য একটি `<li>` তালিকা-আইটেম তৈরি করছেন, যার ভেতরে `<button>` আছে। বাটনের `onClick` handler, `jumpTo` নামের ফাংশন কল করে, যা এখনও ইমপ্লিমেন্ট করেননি।

এখন গেমে দেওয়া চালগুলোর তালিকা এবং developer tools-এর console-এ একটি error দেখার কথা। "key" error-এর অর্থ নিয়ে আলোচনা করা যাক।

### Picking a key {/*picking-a-key*/}

When you render a list, React stores some information about each rendered list item. When you update a list, React needs to determine what has changed. You could have added, removed, re-arranged, or updated the list's items.

Imagine transitioning from

```html
<li>Alexa: 7 tasks left</li>
<li>Ben: 5 tasks left</li>
```

to

```html
<li>Ben: 9 tasks left</li>
<li>Claudia: 8 tasks left</li>
<li>Alexa: 5 tasks left</li>
```

In addition to the updated counts, a human reading this would probably say that you swapped Alexa and Ben's ordering and inserted Claudia between Alexa and Ben. However, React is a computer program and does not know what you intended, so you need to specify a _key_ property for each list item to differentiate each list item from its siblings. If your data was from a database, Alexa, Ben, and Claudia's database IDs could be used as keys.

```js {1}
<li key={user.id}>
  {user.name}: {user.taskCount} tasks left
</li>
```

When a list is re-rendered, React takes each list item's key and searches the previous list's items for a matching key. If the current list has a key that didn't exist before, React creates a component. If the current list is missing a key that existed in the previous list, React destroys the previous component. If two keys match, the corresponding component is moved.

Keys tell React about the identity of each component, which allows React to maintain state between re-renders. If a component's key changes, the component will be destroyed and re-created with a new state.

`key` is a special and reserved property in React. When an element is created, React extracts the `key` property and stores the key directly on the returned element. Even though `key` may look like it is passed as props, React automatically uses `key` to decide which components to update. There's no way for a component to ask what `key` its parent specified.

**It's strongly recommended that you assign proper keys whenever you build dynamic lists.** If you don't have an appropriate key, you may want to consider restructuring your data so that you do.

If no key is specified, React will report an error and use the array index as a key by default. Using the array index as a key is problematic when trying to re-order a list's items or inserting/removing list items. Explicitly passing `key={i}` silences the error but has the same problems as array indices and is not recommended in most cases.

Keys do not need to be globally unique; they only need to be unique between components and their siblings.

### Implementing time travel {/*implementing-time-travel*/}

In the tic-tac-toe game's history, each past move has a unique ID associated with it: it's the sequential number of the move. Moves will never be re-ordered, deleted, or inserted in the middle, so it's safe to use the move index as a key.

In the `Game` function, you can add the key as `<li key={move}>`, and if you reload the rendered game, React's "key" error should disappear:

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

Before you can implement `jumpTo`, you need the `Game` component to keep track of which step the user is currently viewing. To do this, define a new state variable called `currentMove`, defaulting to `0`:

```js {4}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const currentSquares = history[history.length - 1];
  //...
}
```

Next, update the `jumpTo` function inside `Game` to update that `currentMove`. You'll also set `xIsNext` to `true` if the number that you're changing `currentMove` to is even.

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

You will now make two changes to the `Game`'s `handlePlay` function which is called when you click on a square.

- If you "go back in time" and then make a new move from that point, you only want to keep the history up to that point. Instead of adding `nextSquares` after all items (`...` spread syntax) in `history`, you'll add it after all items in `history.slice(0, currentMove + 1)` so that you're only keeping that portion of the old history.
- Each time a move is made, you need to update `currentMove` to point to the latest history entry.

```js {2-4}
function handlePlay(nextSquares) {
  const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
  setHistory(nextHistory);
  setCurrentMove(nextHistory.length - 1);
  setXIsNext(!xIsNext);
}
```

Finally, you will modify the `Game` component to render the currently selected move, instead of always rendering the final move:

```js {5}
export default function Game() {
  const [xIsNext, setXIsNext] = useState(true);
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const currentSquares = history[currentMove];

  // ...
}
```

If you click on any step in the game's history, the tic-tac-toe board should immediately update to show what the board looked like after that step occurred.

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

### Final cleanup {/*final-cleanup*/}

If you look at the code very closely, you may notice that `xIsNext === true` when `currentMove` is even and `xIsNext === false` when `currentMove` is odd. In other words, if you know the value of `currentMove`, then you can always figure out what `xIsNext` should be.

There's no reason for you to store both of these in state. In fact, always try to avoid redundant state. Simplifying what you store in state reduces bugs and makes your code easier to understand. Change `Game` so that it doesn't store `xIsNext` as a separate state variable and instead figures it out based on the `currentMove`:

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

You no longer need the `xIsNext` state declaration or the calls to `setXIsNext`. Now, there's no chance for `xIsNext` to get out of sync with `currentMove`, even if you make a mistake while coding the components.

### Wrapping up {/*wrapping-up*/}

Congratulations! You've created a tic-tac-toe game that:

- Lets you play tic-tac-toe,
- Indicates when a player has won the game,
- Stores a game's history as a game progresses,
- Allows players to review a game's history and see previous versions of a game's board.

Nice work! We hope you now feel like you have a decent grasp of how React works.

Check out the final result here:

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

If you have extra time or want to practice your new React skills, here are some ideas for improvements that you could make to the tic-tac-toe game, listed in order of increasing difficulty:

1. For the current move only, show "You are at move #..." instead of a button.
1. Rewrite `Board` to use two loops to make the squares instead of hardcoding them.
1. Add a toggle button that lets you sort the moves in either ascending or descending order.
1. When someone wins, highlight the three squares that caused the win (and when no one wins, display a message about the result being a draw).
1. Display the location for each move in the format (row, col) in the move history list.

Throughout this tutorial, you've touched on React concepts including elements, components, props, and state. Now that you've seen how these concepts work when building a game, check out [Thinking in React](/learn/thinking-in-react) to see how the same React concepts work when building an app's UI.
