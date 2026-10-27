---
title: আপনার প্রথম কম্পোনেন্ট
---

<Intro>

*কম্পোনেন্ট* React-এর মূল ধারণাগুলোর একটি। কম্পোনেন্ট দিয়েই user interface (UI) তৈরি হয়, তাই React শেখা শুরু করার জন্য এটি দারুণ একটি জায়গা!

</Intro>

<YouWillLearn>

* কম্পোনেন্ট কী
* React অ্যাপে কম্পোনেন্টের ভূমিকা কী
* কীভাবে আপনার প্রথম React কম্পোনেন্ট লিখবেন

</YouWillLearn>

## কম্পোনেন্ট: UI তৈরির একেকটি অংশ {/*components-ui-building-blocks*/}

ওয়েবে HTML-এর `<h1>`, `<li>`-এর মতো বিল্ট-ইন ট্যাগ দিয়ে সাজানো-গোছানো document তৈরি করা যায়:

```html
<article>
  <h1>My First Component</h1>
  <ol>
    <li>Components: UI Building Blocks</li>
    <li>Defining a Component</li>
    <li>Using a Component</li>
  </ol>
</article>
```

এই মার্কআপে লেখাটিকে `<article>`, শিরোনামকে `<h1>` এবং সংক্ষিপ্ত সূচিপত্রকে একটি ক্রমিক তালিকা `<ol>` দিয়ে দেখানো হয়েছে। এমন মার্কআপের সাথে ডিজাইনের জন্য CSS আর ব্যবহারকারীর কাজের প্রতিক্রিয়া দেখানোর জন্য জাভাস্ক্রিপ্ট যোগ হয়। ওয়েবে দেখা প্রতিটি sidebar, avatar, modal, dropdown—UI-এর প্রতিটি অংশের পেছনেই এগুলো থাকে।

React-এ মার্কআপ, CSS আর জাভাস্ক্রিপ্ট একসাথে মিলিয়ে নিজের কম্পোনেন্ট বানাতে পারেন—**আপনার অ্যাপের UI-এর এমন অংশ, যা বারবার ব্যবহার করা যায়।** উপরের সূচিপত্রের কোড দিয়ে একটি `<TableOfContents />` কম্পোনেন্ট বানিয়ে প্রতিটি পেইজে রেন্ডার করতে পারেন। ভেতরে এটি তখনও `<article>`, `<h1>`-এর মতো একই HTML ট্যাগ ব্যবহার করে।

HTML ট্যাগের মতোই কম্পোনেন্টগুলো সাজিয়ে, একত্রে ব্যবহার করে এবং একটির ভেতরে আরেকটি রেখে পুরো পেইজ ডিজাইন করতে পারেন। যেমন, আপনি যে ডকুমেন্টেশন পেইজটি পড়ছেন, সেটিও React কম্পোনেন্ট দিয়ে তৈরি:

```js
<PageLayout>
  <NavigationHeader>
    <SearchBar />
    <Link to="/docs">Docs</Link>
  </NavigationHeader>
  <Sidebar />
  <PageContent>
    <TableOfContents />
    <DocumentationText />
  </PageContent>
</PageLayout>
```

প্রজেক্ট বড় হলে দেখবেন, আগে লেখা কম্পোনেন্ট আবার ব্যবহার করেই অনেক ডিজাইন তৈরি করা যায়। এতে কাজও দ্রুত এগোয়। উপরের সূচিপত্রটি যেকোনো স্ক্রিনে শুধু `<TableOfContents />` লিখেই যোগ করা যায়! চাইলে React ওপেন সোর্স কমিউনিটির শেয়ার করা হাজারো কম্পোনেন্ট দিয়েও প্রজেক্ট শুরু করতে পারেন, যেমন [Chakra UI](https://chakra-ui.com/) ও [Material UI।](https://material-ui.com/)

## কম্পোনেন্ট লেখা {/*defining-a-component*/}

আগে ওয়েব ডেভেলপাররা প্রথমে কনটেন্টের মার্কআপ লিখতেন, তারপর প্রয়োজনমতো জাভাস্ক্রিপ্ট যোগ করে ইন্টারঅ্যাক্টিভ করতেন। ওয়েবে ইন্টারঅ্যাক্টিভিটি বাড়তি সুবিধা হিসেবে থাকলে এই পদ্ধতি ভালোই কাজ করত। এখন অনেক ওয়েবসাইটে এবং সব অ্যাপেই ইন্টারঅ্যাক্টিভিটি প্রত্যাশিত। একই প্রযুক্তি ব্যবহার করেও React ইন্টারঅ্যাক্টিভিটিকে শুরু থেকেই গুরুত্ব দেয়: **একটি React কম্পোনেন্ট হলো জাভাস্ক্রিপ্ট ফাংশন, যার মধ্যে _মার্কআপ যোগ করতে পারেন_।** দেখতে কেমন হয় নিচে দেখুন (উদাহরণটি বদলেও দেখতে পারেন):

<Sandpack>

```js
export default function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/MK3eW3Am.jpg"
      alt="Katherine Johnson"
    />
  )
}
```

```css
img { height: 200px; }
```

</Sandpack>

And here's how to build a component:

### Step 1: Export the component {/*step-1-export-the-component*/}

The `export default` prefix is a [standard JavaScript syntax](https://developer.mozilla.org/docs/web/javascript/reference/statements/export) (not specific to React). It lets you mark the main function in a file so that you can later import it from other files. (More on importing in [Importing and Exporting Components](/learn/importing-and-exporting-components)!)

### Step 2: Define the function {/*step-2-define-the-function*/}

With `function Profile() { }` you define a JavaScript function with the name `Profile`.

<Pitfall>

React components are regular JavaScript functions, but **their names must start with a capital letter** or they won't work!

</Pitfall>

### Step 3: Add markup {/*step-3-add-markup*/}

The component returns an `<img />` tag with `src` and `alt` attributes. `<img />` is written like HTML, but it is actually JavaScript under the hood! This syntax is called [JSX](/learn/writing-markup-with-jsx), and it lets you embed markup inside JavaScript.

Return statements can be written all on one line, as in this component:

```js
return <img src="https://react.dev/images/docs/scientists/MK3eW3As.jpg" alt="Katherine Johnson" />;
```

But if your markup isn't all on the same line as the `return` keyword, you must wrap it in a pair of parentheses:

```js
return (
  <div>
    <img src="https://react.dev/images/docs/scientists/MK3eW3As.jpg" alt="Katherine Johnson" />
  </div>
);
```

<Pitfall>

Without parentheses, any code on the lines after `return` [will be ignored](https://stackoverflow.com/questions/2846283/what-are-the-rules-for-javascripts-automatic-semicolon-insertion-asi)!

</Pitfall>

## Using a component {/*using-a-component*/}

Now that you've defined your `Profile` component, you can nest it inside other components. For example, you can export a `Gallery` component that uses multiple `Profile` components:

<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/MK3eW3As.jpg"
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

### What the browser sees {/*what-the-browser-sees*/}

Notice the difference in casing:

* `<section>` is lowercase, so React knows we refer to an HTML tag.
* `<Profile />` starts with a capital `P`, so React knows that we want to use our component called `Profile`.

And `Profile` contains even more HTML: `<img />`. In the end, this is what the browser sees:

```html
<section>
  <h1>Amazing scientists</h1>
  <img src="https://react.dev/images/docs/scientists/MK3eW3As.jpg" alt="Katherine Johnson" />
  <img src="https://react.dev/images/docs/scientists/MK3eW3As.jpg" alt="Katherine Johnson" />
  <img src="https://react.dev/images/docs/scientists/MK3eW3As.jpg" alt="Katherine Johnson" />
</section>
```

### Nesting and organizing components {/*nesting-and-organizing-components*/}

Components are regular JavaScript functions, so you can keep multiple components in the same file. This is convenient when components are relatively small or tightly related to each other. If this file gets crowded, you can always move `Profile` to a separate file. You will learn how to do this shortly on the [page about imports.](/learn/importing-and-exporting-components)

Because the `Profile` components are rendered inside `Gallery`—even several times!—we can say that `Gallery` is a **parent component,** rendering each `Profile` as a "child". This is part of the magic of React: you can define a component once, and then use it in as many places and as many times as you like.

<Pitfall>

Components can render other components, but **you must never nest their definitions:**

```js {2-5}
export default function Gallery() {
  // 🔴 Never define a component inside another component!
  function Profile() {
    // ...
  }
  // ...
}
```

The snippet above is [very slow and causes bugs.](/learn/preserving-and-resetting-state#different-components-at-the-same-position-reset-state) Instead, define every component at the top level:

```js {5-8}
export default function Gallery() {
  // ...
}

// ✅ Declare components at the top level
function Profile() {
  // ...
}
```

When a child component needs some data from a parent, [pass it by props](/learn/passing-props-to-a-component) instead of nesting definitions.

</Pitfall>

<DeepDive>

#### Components all the way down {/*components-all-the-way-down*/}

Your React application begins at a "root" component. Usually, it is created automatically when you start a new project. For example, if you use [CodeSandbox](https://codesandbox.io/) or if you use the framework [Next.js](https://nextjs.org/), the root component is defined in `pages/index.js`. In these examples, you've been exporting root components.

Most React apps use components all the way down. This means that you won't only use components for reusable pieces like buttons, but also for larger pieces like sidebars, lists, and ultimately, complete pages! Components are a handy way to organize UI code and markup, even if some of them are only used once.

[React-based frameworks](/learn/creating-a-react-app) take this a step further. Instead of using an empty HTML file and letting React "take over" managing the page with JavaScript, they *also* generate the HTML automatically from your React components. This allows your app to show some content before the JavaScript code loads.

Still, many websites only use React to [add interactivity to existing HTML pages.](/learn/add-react-to-an-existing-project#using-react-for-a-part-of-your-existing-page) They have many root components instead of a single one for the entire page. You can use as much—or as little—React as you need.

</DeepDive>

<Recap>

You've just gotten your first taste of React! Let's recap some key points.

* React lets you create components, **reusable UI elements for your app.**
* In a React app, every piece of UI is a component.
* React components are regular JavaScript functions except:

  1. Their names always begin with a capital letter.
  2. They return JSX markup.

</Recap>



<Challenges>

#### Export the component {/*export-the-component*/}

This sandbox doesn't work because the root component is not exported:

<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/lICfvbD.jpg"
      alt="Aklilu Lemma"
    />
  );
}
```

```css
img { height: 181px; }
```

</Sandpack>

Try to fix it yourself before looking at the solution!

<Solution>

Add `export default` before the function definition like so:

<Sandpack>

```js
export default function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/lICfvbD.jpg"
      alt="Aklilu Lemma"
    />
  );
}
```

```css
img { height: 181px; }
```

</Sandpack>

You might be wondering why writing `export` alone is not enough to fix this example. You can learn the difference between `export` and `export default` in [Importing and Exporting Components.](/learn/importing-and-exporting-components)

</Solution>

#### Fix the return statement {/*fix-the-return-statement*/}

Something isn't right about this `return` statement. Can you fix it?

<Hint>

You may get an "Unexpected token" error while trying to fix this. In that case, check that the semicolon appears *after* the closing parenthesis. Leaving a semicolon inside `return ( )` will cause an error.

</Hint>


<Sandpack>

```js
export default function Profile() {
  return
    <img src="https://react.dev/images/docs/scientists/jA8hHMpm.jpg" alt="Katsuko Saruhashi" />;
}
```

```css
img { height: 180px; }
```

</Sandpack>

<Solution>

You can fix this component by moving the return statement to one line like so:

<Sandpack>

```js
export default function Profile() {
  return <img src="https://react.dev/images/docs/scientists/jA8hHMpm.jpg" alt="Katsuko Saruhashi" />;
}
```

```css
img { height: 180px; }
```

</Sandpack>

Or by wrapping the returned JSX markup in parentheses that open right after `return`:

<Sandpack>

```js
export default function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/jA8hHMpm.jpg"
      alt="Katsuko Saruhashi"
    />
  );
}
```

```css
img { height: 180px; }
```

</Sandpack>

</Solution>

#### Spot the mistake {/*spot-the-mistake*/}

Something's wrong with how the `Profile` component is declared and used. Can you spot the mistake? (Try to remember how React distinguishes components from the regular HTML tags!)

<Sandpack>

```js
function profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <profile />
      <profile />
      <profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

<Solution>

React component names must start with a capital letter.

Change `function profile()` to `function Profile()`, and then change every `<profile />` to `<Profile />`:

<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/QIrZWGIs.jpg"
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
img { margin: 0 10px 10px 0; }
```

</Sandpack>

</Solution>

#### Your own component {/*your-own-component*/}

Write a component from scratch. You can give it any valid name and return any markup. If you're out of ideas, you can write a `Congratulations` component that shows `<h1>Good job!</h1>`. Don't forget to export it!

<Sandpack>

```js
// Write your component below!

```

</Sandpack>

<Solution>

<Sandpack>

```js
export default function Congratulations() {
  return (
    <h1>Good job!</h1>
  );
}
```

</Sandpack>

</Solution>

</Challenges>
