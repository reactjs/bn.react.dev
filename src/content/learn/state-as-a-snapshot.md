---
title: স্ন্যাপশট হিসেবে State
---

<Intro>

State ভেরিয়েবলকে সাধারণ জাভাস্ক্রিপ্ট ভেরিয়েবলের মতো রিড এবং রাইট করতে পারলেও এটি আসলে একটি স্ন্যাপশটের মতো আচরণ করে। State ভেরিয়েবল সেট করলে তার পূর্ববর্তী মান পরিবর্তন না হলেও একটি রেন্ডার ট্রিগার হয়।

</Intro>

<YouWillLearn>

* State সেট করলে কিভাবে রেন্ডার ট্রিগার হয়
* কখন এবং কিভাবে State আপডেট হয়
* সেট করার সাথেসাথেই কেন State আপডেট হয় না
* Event handler কিভাবে একটি স্ন্যাপশট এক্সেস করে

</YouWillLearn>

## State সেট করলে রি-রেন্ডার ট্রিগার হয় {/*setting-state-triggers-renders*/}

আপনি হয়ত ভাবতে পারেন ইউজার ইন্টারফেস ক্লিক এর মতো ইউজার ইভেন্টের সরাসরি প্রতিক্রিয়া হিসেবে পরিবর্তন হয়। কিন্তু React-এ এটি একটু অন্যভাবে কাজ করে। আগের পৃষ্ঠায় দেখেছেন [State সেট করলে রি-রেন্ডার ট্রিগার হয়](/learn/render-and-commit#step-1-trigger-a-render)। তার মানে কোন ইভেন্টের প্রতিক্রিয়া পেতে আপনার আগে *State আপডেট* করতে হবে।

এই উদাহরণে "send" বাটনে চাপলে `setIsSent(true)` এর মাধ্যমে React-কে রি-রেন্ডার করতে জানানো হবেঃ

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [isSent, setIsSent] = useState(false);
  const [message, setMessage] = useState('Hi!');
  if (isSent) {
    return <h1>Your message is on its way!</h1>
  }
  return (
    <form onSubmit={(e) => {
      e.preventDefault();
      setIsSent(true);
      sendMessage(message);
    }}>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit">Send</button>
    </form>
  );
}

function sendMessage(message) {
  // ...
}
```

```css
label, textarea { margin-bottom: 10px; display: block; }
```

</Sandpack>

বাটনটি ক্লিক করলে নিম্নলিখিত ঘটনাগুলো ঘটেঃ

১. `onSubmit` event handler এক্সিকিউট হয়।
২. `setIsSent(true)` এর মাধ্যমে `isSent` এর মান true করা হয় এবং একটি নতুন রি-রেন্ডার কিউ করা হয়।
৩. React নতুন `isSent` মান অনুযায়ী কম্পোনেন্টটি পুনরায় রি-রেন্ডার করে।

এখন State এবং রেন্ডারিং মধ্যে সম্পর্কটিতে নজর দেই।

## রেন্ডারিং সময়ের একটি স্ন্যাপশট নেয় {/*rendering-takes-a-snapshot-in-time*/}

কম্পোনেন্ট হল একটি ফাংশন। আপনার কম্পোনেন্ট React-এর দ্বারা কল হওয়াকেই ["রেন্ডারিং"](/learn/render-and-commit#step-2-react-renders-your-components) বলে। সেই ফাংশন থেকে রিটার্ন করা JSX টি ঐ মুহূর্তের একটি স্ন্যাপশটের মতো। এর সমস্ত props, event handler এবং লোকাল ভেরিয়েবলগুলো **রেন্ডার করার সময়ের State ব্যবহার করে ক্যালকুলেট করা হয়েছিল।**

আপনার কম্পোনেন্ট থেকে রিটার্ন হওয়া "স্ন্যাপশট" একটি ছবি বা একটি মুভির ফ্রেমের মতো না, এটি ইন্টারেক্টিভ। ইনপুটের উত্তর হিসাবে কী ঘটবে তার লজিক event handler-এ দেওয়া আছে। React স্ন্যাপশটের সাথে মেলানোর জন্য স্ক্রিনটি আপডেট করে এবং event handler-গুলো UI-এর সাথে কানেক্ট করে। ফলস্বরূপ, আপনার JSX থেকে একটি বাটন চাপলে click handler-টি ট্রিগার হয়।

যখন React কোন কম্পোনেন্ট রি-রেন্ডার করেঃ

1. React আবার আপনার ফাংশনটি কল করে।
2. আপনার ফাংশন একটি নতুন JSX স্ন্যাপশট রিটার্ন করে।
3. তারপরে React আপনার ফাংশনের রিটার্ন করা স্ন্যাপশটের সাথে মেলানোর জন্য স্ক্রিনটি আপডেট করে।

<IllustrationBlock sequential>
    <Illustration caption="React ফাংশন এক্সিকিউট করছে" src="/images/docs/illustrations/i_render1.png" />
    <Illustration caption="স্ন্যাপশট ক্যালকুলেট করছে" src="/images/docs/illustrations/i_render2.png" />
    <Illustration caption="DOM-ট্রি আপডেট করছে" src="/images/docs/illustrations/i_render3.png" />
</IllustrationBlock>

State একটি নিয়মিত ভেরিয়েবলের মতো নয় যা আপনার ফাংশন রিটার্ন করার পরে হারিয়ে যায়। বরং State React-এর ভেতরে বাস করে--আপনার ফাংশনের বাইরে! React কম্পোনেন্টটি কল করলে এটি আপনাকে ঐ রেন্ডারের সময়ের State-এর একটি স্ন্যাপশট দেয়। আপনার কম্পোনেন্ট তার নতুন props এবং event handler সহ একটি নতুন স্ন্যাপশট ফেরত দেয়, যা আপনার **ঐ রেন্ডারের সময়ের State-এর ভ্যালুগুলি ব্যবহার করে ক্যালকুলেট করা হয়!**

<IllustrationBlock sequential>
  <Illustration caption="React-কে State আপডেট করতে বলা হচ্ছে" src="/images/docs/illustrations/i_state-snapshot1.png" />
  <Illustration caption="React State-এর ভ্যালু আপডেট করছে" src="/images/docs/illustrations/i_state-snapshot2.png" />
  <Illustration caption="রিয়েক্ট কম্পোনেন্টে State ভ্যালুর স্ন্যাপশট পাঠায়।" src="/images/docs/illustrations/i_state-snapshot3.png" />
</IllustrationBlock>

 নিচে একটি ছোট এক্সপিরিমেন্টে এটি কিভাবে কাজ করে তা দেখানো হল। এই উদাহরণে আপনি যেহেতু তিনবার `setNumber(number + 1)` কল করবেন, আপনি হয়ত ধরে নিতে পারেন যে "+3" বোতামে ক্লিক করলে কাউন্টারটি তিনবার ইনক্রিমেন্ট হবে।

 "+3" বাটনটি ক্লিক করে দেখুন কি হয়ঃ

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+3</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

Notice that `number` only increments once per click!

**Setting state only changes it for the *next* render.** During the first render, `number` was `0`. This is why, in *that render's* `onClick` handler, the value of `number` is still `0` even after `setNumber(number + 1)` was called:

```js
<button onClick={() => {
  setNumber(number + 1);
  setNumber(number + 1);
  setNumber(number + 1);
}}>+3</button>
```

Here is what this button's click handler tells React to do:

1. `setNumber(number + 1)`: `number` is `0` so `setNumber(0 + 1)`.
    - React prepares to change `number` to `1` on the next render.
2. `setNumber(number + 1)`: `number` is `0` so `setNumber(0 + 1)`.
    - React prepares to change `number` to `1` on the next render.
3. `setNumber(number + 1)`: `number` is `0` so `setNumber(0 + 1)`.
    - React prepares to change `number` to `1` on the next render.

Even though you called `setNumber(number + 1)` three times, in *this render's* event handler `number` is always `0`, so you set the state to `1` three times. This is why, after your event handler finishes, React re-renders the component with `number` equal to `1` rather than `3`.

You can also visualize this by mentally substituting state variables with their values in your code. Since the `number` state variable is `0` for *this render*, its event handler looks like this:

```js
<button onClick={() => {
  setNumber(0 + 1);
  setNumber(0 + 1);
  setNumber(0 + 1);
}}>+3</button>
```

For the next render, `number` is `1`, so *that render's* click handler looks like this:

```js
<button onClick={() => {
  setNumber(1 + 1);
  setNumber(1 + 1);
  setNumber(1 + 1);
}}>+3</button>
```

This is why clicking the button again will set the counter to `2`, then to `3` on the next click, and so on.

## State over time {/*state-over-time*/}

Well, that was fun. Try to guess what clicking this button will alert:

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        alert(number);
      }}>+5</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

If you use the substitution method from before, you can guess that the alert shows "0":

```js
setNumber(0 + 5);
alert(0);
```

But what if you put a timer on the alert, so it only fires _after_ the component re-rendered? Would it say "0" or "5"? Have a guess!

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setTimeout(() => {
          alert(number);
        }, 3000);
      }}>+5</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

Surprised? If you use the substitution method, you can see the "snapshot" of the state passed to the alert.

```js
setNumber(0 + 5);
setTimeout(() => {
  alert(0);
}, 3000);
```

The state stored in React may have changed by the time the alert runs, but it was scheduled using a snapshot of the state at the time the user interacted with it!

**A state variable's value never changes within a render,** even if its event handler's code is asynchronous. Inside *that render's* `onClick`, the value of `number` continues to be `0` even after `setNumber(number + 5)` was called. Its value was "fixed" when React "took the snapshot" of the UI by calling your component.

Here is an example of how that makes your event handlers less prone to timing mistakes. Below is a form that sends a message with a five-second delay. Imagine this scenario:

1. You press the "Send" button, sending "Hello" to Alice.
2. Before the five-second delay ends, you change the value of the "To" field to "Bob".

What do you expect the `alert` to display? Would it display, "You said Hello to Alice"? Or would it display, "You said Hello to Bob"? Make a guess based on what you know, and then try it:

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [to, setTo] = useState('Alice');
  const [message, setMessage] = useState('Hello');

  function handleSubmit(e) {
    e.preventDefault();
    setTimeout(() => {
      alert(`You said ${message} to ${to}`);
    }, 5000);
  }

  return (
    <form onSubmit={handleSubmit}>
      <label>
        To:{' '}
        <select
          value={to}
          onChange={e => setTo(e.target.value)}>
          <option value="Alice">Alice</option>
          <option value="Bob">Bob</option>
        </select>
      </label>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit">Send</button>
    </form>
  );
}
```

```css
label, textarea { margin-bottom: 10px; display: block; }
```

</Sandpack>

**React keeps the state values "fixed" within one render's event handlers.** You don't need to worry whether the state has changed while the code is running.

But what if you wanted to read the latest state before a re-render? You'll want to use a [state updater function](/learn/queueing-a-series-of-state-updates), covered on the next page!

<Recap>

* Setting state requests a new render.
* React stores state outside of your component, as if on a shelf.
* When you call `useState`, React gives you a snapshot of the state *for that render*.
* Variables and event handlers don't "survive" re-renders. Every render has its own event handlers.
* Every render (and functions inside it) will always "see" the snapshot of the state that React gave to *that* render.
* You can mentally substitute state in event handlers, similarly to how you think about the rendered JSX.
* Event handlers created in the past have the state values from the render in which they were created.

</Recap>



<Challenges>

#### Implement a traffic light {/*implement-a-traffic-light*/}

Here is a crosswalk light component that toggles when the button is pressed:

<Sandpack>

```js
import { useState } from 'react';

export default function TrafficLight() {
  const [walk, setWalk] = useState(true);

  function handleClick() {
    setWalk(!walk);
  }

  return (
    <>
      <button onClick={handleClick}>
        Change to {walk ? 'Stop' : 'Walk'}
      </button>
      <h1 style={{
        color: walk ? 'darkgreen' : 'darkred'
      }}>
        {walk ? 'Walk' : 'Stop'}
      </h1>
    </>
  );
}
```

```css
h1 { margin-top: 20px; }
```

</Sandpack>

Add an `alert` to the click handler. When the light is green and says "Walk", clicking the button should say "Stop is next". When the light is red and says "Stop", clicking the button should say "Walk is next".

Does it make a difference whether you put the `alert` before or after the `setWalk` call?

<Solution>

Your `alert` should look like this:

<Sandpack>

```js
import { useState } from 'react';

export default function TrafficLight() {
  const [walk, setWalk] = useState(true);

  function handleClick() {
    setWalk(!walk);
    alert(walk ? 'Stop is next' : 'Walk is next');
  }

  return (
    <>
      <button onClick={handleClick}>
        Change to {walk ? 'Stop' : 'Walk'}
      </button>
      <h1 style={{
        color: walk ? 'darkgreen' : 'darkred'
      }}>
        {walk ? 'Walk' : 'Stop'}
      </h1>
    </>
  );
}
```

```css
h1 { margin-top: 20px; }
```

</Sandpack>

Whether you put it before or after the `setWalk` call makes no difference. That render's value of `walk` is fixed. Calling `setWalk` will only change it for the *next* render, but will not affect the event handler from the previous render.

This line might seem counter-intuitive at first:

```js
alert(walk ? 'Stop is next' : 'Walk is next');
```

But it makes sense if you read it as: "If the traffic light shows 'Walk now', the message should say 'Stop is next.'" The `walk` variable inside your event handler matches that render's value of `walk` and does not change.

You can verify that this is correct by applying the substitution method. When `walk` is `true`, you get:

```js
<button onClick={() => {
  setWalk(false);
  alert('Stop is next');
}}>
  Change to Stop
</button>
<h1 style={{color: 'darkgreen'}}>
  Walk
</h1>
```

So clicking "Change to Stop" queues a render with `walk` set to `false`, and alerts "Stop is next".

</Solution>

</Challenges>
