---
title: কম্পোনেন্ট ইম্পোর্ট এবং এক্সপোর্ট করা
---

<Intro>

কম্পোনেন্ট ব্যাবহারের যাদু রয়েছে এদের পুনঃব্যবহারযোগ্যতার মধ্যেঃ আপনি এমন কম্পোনেন্ট তৈরি করতে পারেন যা অন্যান্য কম্পোনেন্ট এর সমন্বয়ে গঠিত। কিন্তু আপনি যখন কম্পোনেন্ট এর ভিতর কম্পোনেন্ট নেস্ট করা শুরু করবেন, তখন সেগুলিকে নিজস্ব ফাইল এ বিভক্ত করা ই শ্রেয়। এতে করে আপনার ফাইল গুলোকে সহজে স্ক্যান এবং অন্যান্য স্থানে পুনঃব্যবহার করতে পারবেন। 

</Intro>

<YouWillLearn>

* রুট কম্পোনেন্ট ফাইল কি
* কম্পোনেন্ট ইম্পোর্ট এবং এক্সপোর্ট কিভাবে করবেন
* ডিফল্ট এবং নেমড ইম্পোর্ট এবং এক্সপোর্ট কখন ব্যাবহার করবেন
* একটি ফাইল থেকে একাধিক কম্পোনেন্ট ইম্পোর্ট এবং এক্সপোর্ট কিভাবে করবেন
* কম্পোনেন্ট কে একাধিক ফাইলে কিভাবে ভাগ করবেন

</YouWillLearn>

## রুট কম্পোনেন্ট ফাইল {/*the-root-component-file*/}

[আপনার প্রথম কম্পোনেন্ট](/learn/your-first-component) এ আপনি একটি `Profile` কম্পোনেন্ট, এবং এটি রেন্ডার করে এমন একটি `Gallery` কম্পোনেন্ট তৈরি করেছেনঃ

<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

এই উদাহরণ টি বর্তমানে রয়েছে **রুট কম্পোনেন্ট ফাইলে**, যার নাম `App.js`। [Create React App](https://create-react-app.dev/) এ আপনার অ্যাপ্লিকেশন টি `src/App.js` ফাইলে থাকে। যদিও আপনার সেটআপ এর উপর নির্ভর করে আপনার রুট কম্পোনেন্ট অন্য ফাইলেও থাকতে পারে। যদি আপনি ফাইল ভিত্তিক রাউটিং সহ কোন ফ্রেমওয়ার্ক ব্যবহার করেন, যেমন Next.js, তাহলে প্রতিটি পৃষ্ঠার জন্য আপনার রুট কম্পোনেন্ট ভিন্ন হবে।

## Exporting and importing a component {/*exporting-and-importing-a-component*/}

What if you want to change the landing screen in the future and put a list of science books there? Or place all the profiles somewhere else? It makes sense to move `Gallery` and `Profile` out of the root component file. This will make them more modular and reusable in other files. You can move a component in three steps:

1. **Make** a new JS file to put the components in.
2. **Export** your function component from that file (using either [default](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/export#using_the_default_export) or [named](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/export#using_named_exports) exports).
3. **Import** it in the file where you’ll use the component (using the corresponding technique for importing [default](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import#importing_defaults) or [named](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import#import_a_single_export_from_a_module) exports).

Here both `Profile` and `Gallery` have been moved out of `App.js` into a new file called `Gallery.js`. Now you can change `App.js` to import `Gallery` from `Gallery.js`:

<Sandpack>

```js App.js
import Gallery from './Gallery.js';

export default function App() {
  return (
    <Gallery />
  );
}
```

```js Gallery.js
function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

Notice how this example is broken down into two component files now:

1. `Gallery.js`:
     - Defines the `Profile` component which is only used within the same file and is not exported.
     - Exports the `Gallery` component as a **default export.**
2. `App.js`:
     - Imports `Gallery` as a **default import** from `Gallery.js`.
     - Exports the root `App` component as a **default export.**


<Note>

You may encounter files that leave off the `.js` file extension like so:

```js 
import Gallery from './Gallery';
```

Either `'./Gallery.js'` or `'./Gallery'` will work with React, though the former is closer to how [native ES Modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) work.

</Note>

<DeepDive>

#### Default vs named exports {/*default-vs-named-exports*/}

There are two primary ways to export values with JavaScript: default exports and named exports. So far, our examples have only used default exports. But you can use one or both of them in the same file. **A file can have no more than one _default_ export, but it can have as many _named_ exports as you like.**

![Default and named exports](/images/docs/illustrations/i_import-export.svg)

How you export your component dictates how you must import it. You will get an error if you try to import a default export the same way you would a named export! This chart can help you keep track:

| Syntax           | Export statement                           | Import statement                          |
| -----------      | -----------                                | -----------                               |
| Default  | `export default function Button() {}` | `import Button from './Button.js';`     |
| Named    | `export function Button() {}`         | `import { Button } from './Button.js';` |

When you write a _default_ import, you can put any name you want after `import`. For example, you could write `import Banana from './Button.js'` instead and it would still provide you with the same default export. In contrast, with named imports, the name has to match on both sides. That's why they are called _named_ imports!

**People often use default exports if the file exports only one component, and use named exports if it exports multiple components and values.** Regardless of which coding style you prefer, always give meaningful names to your component functions and the files that contain them. Components without names, like `export default () => {}`, are discouraged because they make debugging harder.

</DeepDive>

## Exporting and importing multiple components from the same file {/*exporting-and-importing-multiple-components-from-the-same-file*/}

What if you want to show just one `Profile` instead of a gallery? You can export the `Profile` component, too. But `Gallery.js` already has a *default* export, and you can't have _two_ default exports. You could create a new file with a default export, or you could add a *named* export for `Profile`. **A file can only have one default export, but it can have numerous named exports!**

<Note>

To reduce the potential confusion between default and named exports, some teams choose to only stick to one style (default or named), or avoid mixing them in a single file. Do what works best for you!

</Note>

First, **export** `Profile` from `Gallery.js` using a named export (no `default` keyword):

```js
export function Profile() {
  // ...
}
```

Then, **import** `Profile` from `Gallery.js` to `App.js` using a named import (with the curly braces):

```js
import { Profile } from './Gallery.js';
```

Finally, **render** `<Profile />` from the `App` component:

```js
export default function App() {
  return <Profile />;
}
```

Now `Gallery.js` contains two exports: a default `Gallery` export, and a named `Profile` export. `App.js` imports both of them. Try editing `<Profile />` to `<Gallery />` and back in this example:

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import { Profile } from './Gallery.js';

export default function App() {
  return (
    <Profile />
  );
}
```

```js Gallery.js
export function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

Now you're using a mix of default and named exports:

* `Gallery.js`:
  - Exports the `Profile` component as a **named export called `Profile`.**
  - Exports the `Gallery` component as a **default export.**
* `App.js`:
  - Imports `Profile` as a **named import called `Profile`** from `Gallery.js`.
  - Imports `Gallery` as a **default import** from `Gallery.js`.
  - Exports the root `App` component as a **default export.**

<Recap>

On this page you learned:

* What a root component file is
* How to import and export a component
* When and how to use default and named imports and exports
* How to export multiple components from the same file

</Recap>



<Challenges>

#### Split the components further {/*split-the-components-further*/}

Currently, `Gallery.js` exports both `Profile` and `Gallery`, which is a bit confusing.

Move the `Profile` component to its own `Profile.js`, and then change the `App` component to render both `<Profile />` and `<Gallery />` one after another.

You may use either a default or a named export for `Profile`, but make sure that you use the corresponding import syntax in both `App.js` and `Gallery.js`! You can refer to the table from the deep dive above:

| Syntax           | Export statement                           | Import statement                          |
| -----------      | -----------                                | -----------                               |
| Default  | `export default function Button() {}` | `import Button from './Button.js';`     |
| Named    | `export function Button() {}`         | `import { Button } from './Button.js';` |

<Hint>

Don't forget to import your components where they are called. Doesn't `Gallery` use `Profile`, too?

</Hint>

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import { Profile } from './Gallery.js';

export default function App() {
  return (
    <div>
      <Profile />
    </div>
  );
}
```

```js Gallery.js active
// Move me to Profile.js!
export function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

After you get it working with one kind of exports, make it work with the other kind.

<Solution>

This is the solution with named exports:

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import { Profile } from './Profile.js';

export default function App() {
  return (
    <div>
      <Profile />
      <Gallery />
    </div>
  );
}
```

```js Gallery.js
import { Profile } from './Profile.js';

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
export function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

This is the solution with default exports:

<Sandpack>

```js App.js
import Gallery from './Gallery.js';
import Profile from './Profile.js';

export default function App() {
  return (
    <div>
      <Profile />
      <Gallery />
    </div>
  );
}
```

```js Gallery.js
import Profile from './Profile.js';

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js Profile.js
export default function Profile() {
  return (
    <img
      src="https://i.imgur.com/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

</Solution>

</Challenges>
