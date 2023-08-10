---
title: startTransition
---

<Intro>

`startTransition` আপনাকে UI ব্লক না করে state আপডেট করতে দিবে।

```js
startTransition(scope)
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `startTransition(scope)` {/*starttransitionscope*/}

`startTransition` ফাংশন আপনাকে একটি state update কে transition হিসেবে চিহ্নিত করতে দেবে।

```js {7,9}
import { startTransition } from 'react';

function TabContainer() {
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
  // ...
}
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্যারামিটার {/*parameters*/}

* `scope`: একটি ফাংশন যেটা এক বা একাধিক [`set` functions.](/reference/react/useState#setstate) কল করার মাধ্যমে কোন state আপডেট করে। React তৎক্ষণাৎভাবে কোন প্যারামিটার ছাড়া `scope` কল করে এবং `scope` ফাংশন কল হবার সময়ে সিঙ্ক্রোনাসভাবে শিডিউল হওয়া সকল state update কে transition হিসেবে চিহ্নিত করে। এগুলো [non-blocking](/reference/react/useTransition#marking-a-state-update-as-a-non-blocking-transition) হবে এবং [অবাঞ্ছিত কোন লোডিং ইনডিকেটর দেখাবনে না।](/reference/react/useTransition#preventing-unwanted-loading-indicators)

#### রিটার্ন {/*returns*/}

`startTransition` কিছু রিটার্ন করে না।

#### Caveats {/*caveats*/}

* `startTransition` does not provide a way to track whether a transition is pending. To show a pending indicator while the transition is ongoing, you need [`useTransition`](/reference/react/useTransition) instead.

* You can wrap an update into a transition only if you have access to the `set` function of that state. If you want to start a transition in response to some prop or a custom Hook return value, try [`useDeferredValue`](/reference/react/useDeferredValue) instead.

* The function you pass to `startTransition` must be synchronous. React immediately executes this function, marking all state updates that happen while it executes as transitions. If you try to perform more state updates later (for example, in a timeout), they won't be marked as transitions.

* A state update marked as a transition will be interrupted by other state updates. For example, if you update a chart component inside a transition, but then start typing into an input while the chart is in the middle of a re-render, React will restart the rendering work on the chart component after handling the input state update.

* Transition updates can't be used to control text inputs.

* If there are multiple ongoing transitions, React currently batches them together. This is a limitation that will likely be removed in a future release.

---

## Usage {/*usage*/}

### Marking a state update as a non-blocking transition {/*marking-a-state-update-as-a-non-blocking-transition*/}

You can mark a state update as a *transition* by wrapping it in a `startTransition` call:

```js {7,9}
import { startTransition } from 'react';

function TabContainer() {
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
  // ...
}
```

Transitions let you keep the user interface updates responsive even on slow devices.

With a transition, your UI stays responsive in the middle of a re-render. For example, if the user clicks a tab but then change their mind and click another tab, they can do that without waiting for the first re-render to finish.

<Note>

`startTransition` is very similar to [`useTransition`](/reference/react/useTransition), except that it does not provide the `isPending` flag to track whether a transition is ongoing. You can call `startTransition` when `useTransition` is not available. For example, `startTransition` works outside components, such as from a data library.

[Learn about transitions and see examples on the `useTransition` page.](/reference/react/useTransition)

</Note>
