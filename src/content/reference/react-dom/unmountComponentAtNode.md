---
title: unmountComponentAtNode
---

<Deprecated>

এই API টি React এর একটি আগাম ভার্সনে অপসারিত হবে।

React 18 এ, `unmountComponentAtNode` কে প্রতিস্থাপন করেছে [`root.unmount()`](/reference/react-dom/client/createRoot#root-unmount)।

</Deprecated>

<Intro>

`unmountComponentAtNode` DOM থেকে একটি মাউন্ট করা React কম্পোনেন্ট অপসারণ করে।

```js
unmountComponentAtNode(domNode)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `unmountComponentAtNode(domNode)` {/*unmountcomponentatnode*/}

DOM থেকে একটি mounted React কম্পোনেন্ট সরাতে এবং এর ইভেন্ট হ্যান্ডলার তথা স্টেট মুছে ফেলতে কল করুন `unmountComponentAtNode`।

```js
import { unmountComponentAtNode } from 'react-dom';

const domNode = document.getElementById('root');
render(<App />, domNode);

unmountComponentAtNode(domNode);
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

* `domNode`: একটি [DOM এলিমেন্ট।](https://developer.mozilla.org/en-US/docs/Web/API/Element) React এই এলিমেন্ট থেকে একটি mounted React কম্পোনেন্ট সরিয়ে ফেলবে।

#### রিটার্ন {/*returns*/}

`unmountComponentAtNode` `true` রিটার্ন করে যদি একটি কম্পোনেন্ট unmounted হয়ে থাকে, অন্যথা `false` রিটার্ন করে।

---

## ব্যবহার {/*usage*/}

একটি <CodeStep step={2}>ব্রাউজার DOM নোড</CodeStep> থেকে একটি <CodeStep step={1}>মাউন্টেড React কম্পোনেন্ট</CodeStep> সরিয়ে ফেলবার জন্য এবং এর ইভেন্ট হ্যান্ডলার তথা state মুছে ফেলবার জন্য `unmountComponentAtNode` কল করুন।

```js [[1, 5, "<App />"], [2, 5, "rootNode"], [2, 8, "rootNode"]]
import { render, unmountComponentAtNode } from 'react-dom';
import App from './App.js';

const rootNode = document.getElementById('root');
render(<App />, rootNode);

// ...
unmountComponentAtNode(rootNode);
```


### একটি DOM এলিমেন্ট থেকে একটি React অ্যাপ সরিয়ে ফেলা {/*removing-a-react-app-from-a-dom-element*/}

হঠাত হঠাত, আপনি হয়ত আগে থেকে বানানো পেইজে বা সম্পূর্ণরূপে React দিয়ে বানানো নয় এমন একটি পেইজে একটু খানি React "ছিটিয়ে" দিতে চাইবেন। সেই সকল ক্ষেত্রে, আপনাকে হয়ত React অ্যাপটা "বন্ধ" করতে হবে, এর সকল UI, state এবং লিসেনার যেই DOM নোডে রেন্ডার হয়েছে সেখান থেকে মুছে ফেলার মাধ্যমে।

এই উদাহরণে, "Render React App" এ ক্লিক করলে একটি React অ্যাপ রেন্ডার হবে। "Unmount React App" ক্লিক করার মাধ্যমে একে মুছে ফেলুনঃ

<Sandpack>

```html index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <button id='render'>Render React App</button>
    <button id='unmount'>Unmount React App</button>
    <!-- This is the React App node -->
    <div id='root'></div>
  </body>
</html>
```

```js index.js active
import './styles.css';
import { render, unmountComponentAtNode } from 'react-dom';
import App from './App.js';

const domNode = document.getElementById('root');

document.getElementById('render').addEventListener('click', () => {
  render(<App />, domNode);
});

document.getElementById('unmount').addEventListener('click', () => {
  unmountComponentAtNode(domNode);
});
```

```js App.js
export default function App() {
  return <h1>Hello, world!</h1>;
}
```

</Sandpack>
