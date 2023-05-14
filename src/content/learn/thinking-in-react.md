---
title: Thinking in React
---

<Intro>

React can change how you think about the designs you look at and the apps you build. When you build a user interface with React, you will first break it apart into pieces called *components*. Then, you will describe the different visual states for each of your components. Finally, you will connect your components together so that the data flows through them. In this tutorial, we’ll guide you through the thought process of building a searchable product data table with React.

</Intro>

## Start with the mockup {/*start-with-the-mockup*/}

Imagine that you already have a JSON API and a mockup from a designer.

The JSON API returns some data that looks like this:

```json
[
  { category: "Fruits", price: "$1", stocked: true, name: "Apple" },
  { category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit" },
  { category: "Fruits", price: "$2", stocked: false, name: "Passionfruit" },
  { category: "Vegetables", price: "$2", stocked: true, name: "Spinach" },
  { category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin" },
  { category: "Vegetables", price: "$1", stocked: true, name: "Peas" }
]
```

The mockup looks like this:

<img src="/images/docs/s_thinking-in-react_ui.png" width="300" style={{margin: '0 auto'}} />

To implement a UI in React, you will usually follow the same five steps.

## Step 1: Break the UI into a component hierarchy {/*step-1-break-the-ui-into-a-component-hierarchy*/}

Start by drawing boxes around every component and subcomponent in the mockup and naming them. If you work with a designer, they may have already named these components in their design tool. Ask them!

Depending on your background, you can think about splitting up a design into components in different ways:

* **Programming**--use the same techniques for deciding if you should create a new function or object. One such technique is the [single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle), that is, a component should ideally only do one thing. If it ends up growing, it should be decomposed into smaller subcomponents. 
* **CSS**--consider what you would make class selectors for. (However, components are a bit less granular.)
* **Design**--consider how you would organize the design's layers.

If your JSON is well-structured, you'll often find that it naturally maps to the component structure of your UI. That's because UI and data models often have the same information architecture--that is, the same shape. Separate your UI into components, where each component matches one piece of your data model.

There are five components on this screen:

<FullWidth>

<CodeDiagram flip>

<img src="/images/docs/s_thinking-in-react_ui_outline.png" width="500" style={{margin: '0 auto'}} />

1. `FilterableProductTable` (grey) contains the entire app.
2. `SearchBar` (blue) receives the user input.
3. `ProductTable` (lavender) displays and filters the list according to the user input.
4. `ProductCategoryRow` (green) displays a heading for each category.
5. `ProductRow`	(yellow) displays a row for each product.

</CodeDiagram>

</FullWidth>

If you look at `ProductTable` (lavender), you'll see that the table header (containing the "Name" and "Price" labels) isn't its own component. This is a matter of preference, and you could go either way. For this example, it is a part of `ProductTable` because it appears inside the `ProductTable`'s list. However, if this header grows to be complex (e.g., if you add sorting), you can move it into its own `ProductTableHeader` component.

Now that you've identified the components in the mockup, arrange them into a hierarchy. Components that appear within another component in the mockup should appear as a child in the hierarchy:

* `FilterableProductTable`
    * `SearchBar`
    * `ProductTable`
        * `ProductCategoryRow`
        * `ProductRow`

## Step 2: Build a static version in React {/*step-2-build-a-static-version-in-react*/}

Now that you have your component hierarchy, it's time to implement your app. The most straightforward approach is to build a version that renders the UI from your data model without adding any interactivity... yet! It's often easier to build the static version first and add interactivity later. Building a static version requires a lot of typing and no thinking, but adding interactivity requires a lot of thinking and not a lot of typing.

To build a static version of your app that renders your data model, you'll want to build [components](/learn/your-first-component) that reuse other components and pass data using [props.](/learn/passing-props-to-a-component) Props are a way of passing data from parent to child. (If you're familiar with the concept of [state](/learn/state-a-components-memory), don't use state at all to build this static version. State is reserved only for interactivity, that is, data that changes over time. Since this is a static version of the app, you don't need it.)

You can either build "top down" by starting with building the components higher up in the hierarchy (like `FilterableProductTable`) or "bottom up" by working from components lower down (like `ProductRow`). In simpler examples, it’s usually easier to go top-down, and on larger projects, it’s easier to go bottom-up.

<Sandpack>

```jsx App.js
function ProductCategoryRow({ category }) {
  return (
    <tr>
      <th colSpan="2">
        {category}
      </th>
    </tr>
  );
}

function ProductRow({ product }) {
  const name = product.stocked ? product.name :
    <span style={{ color: 'red' }}>
      {product.name}
    </span>;

  return (
    <tr>
      <td>{name}</td>
      <td>{product.price}</td>
    </tr>
  );
}

function ProductTable({ products }) {
  const rows = [];
  let lastCategory = null;

  products.forEach((product) => {
    if (product.category !== lastCategory) {
      rows.push(
        <ProductCategoryRow
          category={product.category}
          key={product.category} />
      );
    }
    rows.push(
      <ProductRow
        product={product}
        key={product.name} />
    );
    lastCategory = product.category;
  });

  return (
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>{rows}</tbody>
    </table>
  );
}

function SearchBar() {
  return (
    <form>
      <input type="text" placeholder="Search..." />
      <label>
        <input type="checkbox" />
        {' '}
        Only show products in stock
      </label>
    </form>
  );
}

function FilterableProductTable({ products }) {
  return (
    <div>
      <SearchBar />
      <ProductTable products={products} />
    </div>
  );
}

const PRODUCTS = [
  {category: "Fruits", price: "$1", stocked: true, name: "Apple"},
  {category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit"},
  {category: "Fruits", price: "$2", stocked: false, name: "Passionfruit"},
  {category: "Vegetables", price: "$2", stocked: true, name: "Spinach"},
  {category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin"},
  {category: "Vegetables", price: "$1", stocked: true, name: "Peas"}
];

export default function App() {
  return <FilterableProductTable products={PRODUCTS} />;
}
```

```css
body {
  padding: 5px
}
label {
  display: block;
  margin-top: 5px;
  margin-bottom: 5px;
}
th {
  padding-top: 10px;
}
td {
  padding: 2px;
  padding-right: 40px;
}
```

</Sandpack>

(If this code looks intimidating, go through the [Quick Start](/learn/) first!)

After building your components, you'll have a library of reusable components that render your data model. Because this is a static app, the components will only return JSX. The component at the top of the hierarchy (`FilterableProductTable`) will take your data model as a prop. This is called _one-way data flow_ because the data flows down from the top-level component to the ones at the bottom of the tree.

<Pitfall>

At this point, you should not be using any state values. That’s for the next step!

</Pitfall>

## Step 3: Find the minimal but complete representation of UI state {/*step-3-find-the-minimal-but-complete-representation-of-ui-state*/}

To make the UI interactive, you need to let users change your underlying data model. You will use *state* for this.

Think of state as the minimal set of changing data that your app needs to remember. The most important principle for structuring state is to keep it [DRY (Don't Repeat Yourself).](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) Figure out the absolute minimal representation of the state your application needs and compute everything else on-demand. For example, if you're building a shopping list, you can store the items as an array in state. If you want to also display the number of items in the list, don't store the number of items as another state value--instead, read the length of your array.

Now think of all of the pieces of data in this example application:

1. The original list of products
2. The search text the user has entered
3. The value of the checkbox
4. The filtered list of products

Which of these are state? Identify the ones that are not:

* Does it **remain unchanged** over time? If so, it isn't state.
* Is it **passed in from a parent** via props? If so, it isn't state.
* **Can you compute it** based on existing state or props in your component? If so, it *definitely* isn't state!

What's left is probably state.

Let's go through them one by one again:

1. The original list of products is **passed in as props, so it's not state.** 
2. The search text seems to be state since it changes over time and can't be computed from anything.
3. The value of the checkbox seems to be state since it changes over time and can't be computed from anything.
4. The filtered list of products **isn't state because it can be computed** by taking the original list of products and filtering it according to the search text and value of the checkbox.

This means only the search text and the value of the checkbox are state! Nicely done!

<DeepDive>

#### Props বনাম State {/*props-vs-state*/}

React-এ দু'ধরণের "model" ডেটা রয়েছেঃ props এবং state। এই দুইটা খুবই আলাদাঃ

* [**Props** হচ্ছে একটা ফাংশনে আপনার pass করা argument এর মত।](/learn/passing-props-to-a-component) এরা একটা parent কম্পোনেন্ট থেকে child কম্পোনেন্টে এ ডেটা পাঠানোর এবং কাস্টমাইজ করার সুযোগ দেয়। উদাহরণস্বরূপ,  একটা `Form`, একটা `Button` এ `color` prop পাঠাতে পারে।
* [**State** হচ্ছে কম্পোনেন্টের স্মৃতির মত।](/learn/state-a-components-memory) এটা একটা কম্পোনেন্টকে কোন একটা তথ্যের হালচাল হিসেব রাখার সুযোগ দেয় এবং ব্যবহারকারীর interaction অনুসারে বদলাতে দেয়। উদাহরণস্বরূপ, `Button`, `isHovered` state এর track রাখতে পারে।

Props এবং state আলাদা বটে, তবে এরা একই সাথে কাজ করে। একটা parent কম্পোনেন্ট বেশিরভাগ সময়ই কিছু তথ্য state এ রাখবে (যাতে সে এটা বদলাতে পারে), এবং prop হিসেবে child কম্পোনেন্টে *pass down* করবে। শুরুর দিকে যদি এই পার্থক্যটা একটু ঝামেলার মনে হয় সেটা খুব স্বাভাবিক। এটা মাথার মধ্যে গেঁথে যেতে বেশ চর্চ্চার দরকার পড়ে।

</DeepDive>

## ধাপ ৪ঃ আপনার state কোথায় থাকা উচিত সেটা চিহ্নিত করুন। {/*step-4-identify-where-your-state-should-live*/}

আপনার অ্যাপের ন্যূনতম state ডেটা চিহ্নিত করার পর, আপনাকে বের করতে হবে এর state পরিবর্তন কোন কম্পোনেন্টের দায়িত্ব, বা কে এই state এর *মালিকানা রাখে*। মনে রাখবেনঃ React একমুখী তথ্য প্রবাহ ব্যবহার করে, অর্থাৎ hierarchy-তে parent থেকে child কম্পোনেন্টের দিকে data pass হয়। কোন কম্পোনেন্টে state থাকা উচিত এইটা হয়ত প্রথমেই পরিষ্কার হবে না। আপনি যদি এই ধারণার সাথে নতুন নতুন পরিচিত হয়ে থাকেন, এটা একটু কঠিন লাগতে পারে, তবে নিচের ধাপগুলার মাধ্যমে আপনি আপনার উত্তর পেয়ে যাবেন।

আপনার অ্যাপ্লিকেশনের প্রতিটা অংশের state-এর জন্যঃ

1. সেই *প্রতিটি* কম্পোনেন্ট চিহ্নিত করুন যা ওই state এর উপর নির্ভর করে কিছু রেন্ডার করে। 
2. তাদের সবচেয়ে কাছের সেই কম্পোনেন্ট খুঁজে বের করুন যা তাদের উভয়েরই parent -- একটি কম্পোনেন্ট যা hierarchy তে তাদের সবার উপরে। 
3. state কোথায় থাকা উচিত সেটা সিদ্ধান্ত নিনঃ
    1. বেশিরভাগ সময়ে, আপনি তাদের সাধারণ parent এ state রেখে দিতে পারবেন। 
    2. আপনি তাদের সাধারণ parent এর উপরের কোন কম্পোনেন্টেও state রাখতে পারেন।
    3. আপনি যদি এমন কোন কম্পোনেন্ট খুঁজে না পান যেখানে state রাখা যুক্তিযুক্ত হবে, তাহলে নতুন একটা কম্পোনেন্ট তৈরি করুন শুধুমাত্র তাদের state রাখবার জন্য এবং hierarchy-তে তাদের সাধারণ parent এর উপরে কোথাও যুক্ত করে দিন। 

এর আগের ধাপে অ্যাপ্লিকেশনে আপনি state হবার মত দুটি বিষয় পেয়েছিলেনঃ সার্চের জন্য ইনপুট টেক্সট এবং চেকবক্সের ভ্যালু। এই উদাহরণে, তারা সব সময়ে একই সাথে আসে, তাই তাদেরকে একই জায়গায় রাখাটা যুক্তিযুক্ত হবে।  

এখন তাদের জন্য আমাদের পরিকল্পনা ঝালাই করে নিই। 

1. **state ব্যবহার করে এমন কম্পোনেন্টগুলো চিহ্নিত করুন**
    * `ProductTable`কে ওই state(সার্চ টেক্সট এবং চেকবক্স) ব্যবহার করে পণ্যের তালিকা বাছাই করতে হবে। 
    * `SearchBar` কে ওই state(সার্চ টেক্সট এবং চেকবক্স) দেখাতে হবে। 
1. **তাদের সাধারণ parent খুঁজে বের করুন** প্রথম যেই কম্পোনেন্ট এদের দুজনেরই parent তা হল `FilterableProductTable`।
2. **State কোথায় থাকবে সেটা সিদ্ধান্ত নিন**: আমরা বাছাই করা text এবং checked state ভ্যালুগুলো  `FilterableProductTable` -তে রাখব। 

সুতরাং state ভ্যালুগুলো থাকবে `FilterableProductTable` এর মধ্যে।

কম্পোনেন্টে [`useState()` Hook.](/reference/react/useState) ব্যবহার করে state যুক্ত করুন। Hook হচ্ছে বিশেষ ফাংশন যা আপনাকে Reac† এর "মায়ায় হারাতে" বাধ্য করবে। `FilterableProductTable` এর একদম উপরে দুটো state variable যুক্ত করুন এবং তাদের প্রাথমিক state ঠিক করে দিন। 

```js
function FilterableProductTable({ products }) {
  const [filterText, setFilterText] = useState('');
  const [inStockOnly, setInStockOnly] = useState(false);  
```

Then, pass `filterText` and `inStockOnly` to `ProductTable` and `SearchBar` as props:

```js
<div>
  <SearchBar 
    filterText={filterText} 
    inStockOnly={inStockOnly} />
  <ProductTable 
    products={products}
    filterText={filterText}
    inStockOnly={inStockOnly} />
</div>
```

You can start seeing how your application will behave. Edit the `filterText` initial value from `useState('')` to `useState('fruit')` in the sandbox code below. You'll see both the search input text and the table update:

<Sandpack>

```jsx App.js
import { useState } from 'react';

function FilterableProductTable({ products }) {
  const [filterText, setFilterText] = useState('');
  const [inStockOnly, setInStockOnly] = useState(false);

  return (
    <div>
      <SearchBar 
        filterText={filterText} 
        inStockOnly={inStockOnly} />
      <ProductTable 
        products={products}
        filterText={filterText}
        inStockOnly={inStockOnly} />
    </div>
  );
}

function ProductCategoryRow({ category }) {
  return (
    <tr>
      <th colSpan="2">
        {category}
      </th>
    </tr>
  );
}

function ProductRow({ product }) {
  const name = product.stocked ? product.name :
    <span style={{ color: 'red' }}>
      {product.name}
    </span>;

  return (
    <tr>
      <td>{name}</td>
      <td>{product.price}</td>
    </tr>
  );
}

function ProductTable({ products, filterText, inStockOnly }) {
  const rows = [];
  let lastCategory = null;

  products.forEach((product) => {
    if (
      product.name.toLowerCase().indexOf(
        filterText.toLowerCase()
      ) === -1
    ) {
      return;
    }
    if (inStockOnly && !product.stocked) {
      return;
    }
    if (product.category !== lastCategory) {
      rows.push(
        <ProductCategoryRow
          category={product.category}
          key={product.category} />
      );
    }
    rows.push(
      <ProductRow
        product={product}
        key={product.name} />
    );
    lastCategory = product.category;
  });

  return (
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>{rows}</tbody>
    </table>
  );
}

function SearchBar({ filterText, inStockOnly }) {
  return (
    <form>
      <input 
        type="text" 
        value={filterText} 
        placeholder="Search..."/>
      <label>
        <input 
          type="checkbox" 
          checked={inStockOnly} />
        {' '}
        Only show products in stock
      </label>
    </form>
  );
}

const PRODUCTS = [
  {category: "Fruits", price: "$1", stocked: true, name: "Apple"},
  {category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit"},
  {category: "Fruits", price: "$2", stocked: false, name: "Passionfruit"},
  {category: "Vegetables", price: "$2", stocked: true, name: "Spinach"},
  {category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin"},
  {category: "Vegetables", price: "$1", stocked: true, name: "Peas"}
];

export default function App() {
  return <FilterableProductTable products={PRODUCTS} />;
}
```

```css
body {
  padding: 5px
}
label {
  display: block;
  margin-top: 5px;
  margin-bottom: 5px;
}
th {
  padding-top: 5px;
}
td {
  padding: 2px;
}
```

</Sandpack>

খেয়াল করুন form এর পরিবর্তন এখনো কাজ করে না। উপড়ে স্যান্ডবক্সে আমরা একটা কনসোল এরর দেখতে পাচ্ছি যেটা কারণটা ব্যাখ্যা করে। 

<ConsoleBlock level="error">

আপনি \`onChange\` handler নেই এমন একটা ফিল্ডে  \`value\`  prop পাঠিয়েছেন। এটা কেবলমাত্র একটি read-only ফিল্ড রেন্ডার করবে। 

</ConsoleBlock>

উপরের স্যান্ডবক্সে, টেবিল, ইনপুট এবং চেকবক্স দেখানোর জন্য `ProductTable`এবং `SearchBar` যথাক্রমে `filterText` এবং  `inStockOnly` prop গুলো নজরে রাখে। উদাহরণস্বরূপ, নিচে দেখতে পাবেন কীভাবে `SearchBar` ইনপুটের ভ্যালুগুলোকে সামনে নিয়ে আসে। 

```js {1,6}
function SearchBar({ filterText, inStockOnly }) {
  return (
    <form>
      <input 
        type="text" 
        value={filterText} 
        placeholder="Search..."/>
```

কিন্তু আপনি এখনো ব্যবহারকারীর কোন কাজ যেমন টাইপিং এর ফলাফল দেখানোর জন্য কোড লিখেননি। এটা হবে আপনার সর্বশেষ ধাপ।  


## ধাপ ৫ঃ inverse data flow যুক্ত করুন {/*step-5-add-inverse-data-flow*/}

এখন hierarchy-তে props এবং state এর তথ্য সঠিকভাবে নিচের দিকে প্রবাহিত হওয়ায় আপনার অ্যাপ ঠিকঠাক দেখা যাচ্ছে। কিন্তু ব্যবহারকারীর দেওয়া তথ্যের ভিত্তিতে state পরিবর্তন করতে আপনাকে উল্টোটিকে তথ্য প্রবাহের ব্যবস্থা রাখতে হবেঃ hierarchy-র গভীরে থাকা কম্পোনেন্টগুলো দিয়ে `FilterableProductTable` এ থাকা state পরিবর্তন করবে হবে।   

React makes this data flow explicit, but it requires a little more typing than two-way data binding. If you try to type or check the box in the example above, you'll see that React ignores your input. This is intentional. By writing `<input value={filterText} />`, you've set the `value` prop of the `input` to always be equal to the `filterText` state passed in from `FilterableProductTable`. Since `filterText` state is never set, the input never changes.

You want to make it so whenever the user changes the form inputs, the state updates to reflect those changes. The state is owned by `FilterableProductTable`, so only it can call `setFilterText` and `setInStockOnly`. To let `SearchBar` update the `FilterableProductTable`'s state, you need to pass these functions down to `SearchBar`:

```js {2,3,10,11}
function FilterableProductTable({ products }) {
  const [filterText, setFilterText] = useState('');
  const [inStockOnly, setInStockOnly] = useState(false);

  return (
    <div>
      <SearchBar 
        filterText={filterText} 
        inStockOnly={inStockOnly}
        onFilterTextChange={setFilterText}
        onInStockOnlyChange={setInStockOnly} />
```

`SearchBar` এর মধ্যে আপনি `onChange` event handlers যুক্ত করবেন এবং তাদের থেকে parent state ঠিক করবেন।

```js {5}
<input 
  type="text" 
  value={filterText} 
  placeholder="Search..." 
  onChange={(e) => onFilterTextChange(e.target.value)} />
```

এখন আপ্লিকেশনটা পুরোপুরি চলছে।

<Sandpack>

```jsx App.js
import { useState } from 'react';

function FilterableProductTable({ products }) {
  const [filterText, setFilterText] = useState('');
  const [inStockOnly, setInStockOnly] = useState(false);

  return (
    <div>
      <SearchBar 
        filterText={filterText} 
        inStockOnly={inStockOnly} 
        onFilterTextChange={setFilterText} 
        onInStockOnlyChange={setInStockOnly} />
      <ProductTable 
        products={products} 
        filterText={filterText}
        inStockOnly={inStockOnly} />
    </div>
  );
}

function ProductCategoryRow({ category }) {
  return (
    <tr>
      <th colSpan="2">
        {category}
      </th>
    </tr>
  );
}

function ProductRow({ product }) {
  const name = product.stocked ? product.name :
    <span style={{ color: 'red' }}>
      {product.name}
    </span>;

  return (
    <tr>
      <td>{name}</td>
      <td>{product.price}</td>
    </tr>
  );
}

function ProductTable({ products, filterText, inStockOnly }) {
  const rows = [];
  let lastCategory = null;

  products.forEach((product) => {
    if (
      product.name.toLowerCase().indexOf(
        filterText.toLowerCase()
      ) === -1
    ) {
      return;
    }
    if (inStockOnly && !product.stocked) {
      return;
    }
    if (product.category !== lastCategory) {
      rows.push(
        <ProductCategoryRow
          category={product.category}
          key={product.category} />
      );
    }
    rows.push(
      <ProductRow
        product={product}
        key={product.name} />
    );
    lastCategory = product.category;
  });

  return (
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>{rows}</tbody>
    </table>
  );
}

function SearchBar({
  filterText,
  inStockOnly,
  onFilterTextChange,
  onInStockOnlyChange
}) {
  return (
    <form>
      <input 
        type="text" 
        value={filterText} placeholder="Search..." 
        onChange={(e) => onFilterTextChange(e.target.value)} />
      <label>
        <input 
          type="checkbox" 
          checked={inStockOnly} 
          onChange={(e) => onInStockOnlyChange(e.target.checked)} />
        {' '}
        Only show products in stock
      </label>
    </form>
  );
}

const PRODUCTS = [
  {category: "Fruits", price: "$1", stocked: true, name: "Apple"},
  {category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit"},
  {category: "Fruits", price: "$2", stocked: false, name: "Passionfruit"},
  {category: "Vegetables", price: "$2", stocked: true, name: "Spinach"},
  {category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin"},
  {category: "Vegetables", price: "$1", stocked: true, name: "Peas"}
];

export default function App() {
  return <FilterableProductTable products={PRODUCTS} />;
}
```

```css
body {
  padding: 5px
}
label {
  display: block;
  margin-top: 5px;
  margin-bottom: 5px;
}
th {
  padding: 4px;
}
td {
  padding: 2px;
}
```

</Sandpack>

আপনি event handling এবং state আপডেটের খুঁটিনাটি  [Interactivity সংযুক্তি](/learn/adding-interactivity) সেকশন থেকে শিখে নিতে পারবেন।

## এখান থেকে কোথায় যাবেন? {/*where-to-go-from-here*/}

এটা ছিল React দিয়ে কীভাবে কম্পোনেন্ট এবং অ্যাপ্লিকেশন তৈরির বিষয়ে চিন্তা করতে হয় তার একটা সংক্ষেপিত পরিচিতি।  আপনি এখুনি [একটি React প্রজেক্ট শুরু করতে পারেন](/learn/installation) অথবা  এই টিউটোরিয়ালে ব্যবহৃত সকল [syntax নিয়ে গভীরভাবে জানতে পারেন।](/learn/describing-the-ui) 
