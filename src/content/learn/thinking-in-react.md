---
title: React এর আলোকে চিন্তা করা
---

<Intro>

React আপনার দেখা ডিজাইন এবং তৈরী করা অ্যাপগুলি সম্বন্ধে আপনার চিন্তাভাবনা পরিবর্তন করতে পারে। React দিয়ে আপনি যখন একটি ইউজার ইন্টারফেস তৈরি করবেন, প্রথমে আপনি এটি কম্পোনেন্ট নামে বিভিন্ন অংশে ভাগ করে ফেলবেন। তারপর, আপনি আপনার প্রতিটি কম্পোনেন্টের জন্য বিভিন্ন দৃশ্যমান state বর্ণনা করবেন। সবশেষে, আপনি আপনার কম্পোনেন্টগুলি এমনভাবে সংযুক্ত করবেন যাতে তাদের মাঝে ডেটা প্রবাহিত হয়। এই টিউটোরিয়ালে, আমরা React দিয়ে একটি সার্চেবল প্রোডাক্টের ডেটা টেবিল তৈরীর চিন্তা প্রক্রিয়া কেমন হবে সে বিষয়ে ধারণা করাতে আপনাকে সহায়তা করব।

</Intro>

## মকাপ দিয়ে শুরু করুন {/*start-with-the-mockup*/}

ধরে নিন, আপনার কাছে ইতোমধ্যে একজন ডিজাইনারের কাছ থেকে পাওয়া মকাপ আছে এবং একটি JSON API ও আছে। 

JSON API যে তথ্য রিটার্ন করে সেটা কিছুটা এমন দেখতেঃ

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

মকাপটা দেখতে এমনঃ

<img src="/images/docs/s_thinking-in-react_ui.png" width="300" style={{margin: '0 auto'}} />

React এ একটি UI ইমপ্লিমেন্ট করতে আপনাকে সাধারণত নিম্নের ৫টি ধাপ অনুসরণ করতে হবে।

## ধাপ ১ঃ UI কে একটি কম্পোনেন্ট hierarchy তে বিন্যস্ত করুন {/*step-1-break-the-ui-into-a-component-hierarchy*/}

মকাপে সবগুলো কম্পোনেন্ট এবং সাবকম্পোনেন্ট এর চারিদিকে বক্স আঁকার মাধ্যমে শুরু করেন। সেগুলোর নামকরণ করুন। যদি আপনি একজন ডিজাইনারের সাথে কাজ করে থাকেন, দেখা যেতে পারে উনি ইতোমধ্যে তার ডিজাইনে এই কম্পোনেন্টগুলোর নামকরণ করে রেখেছেন। উনার কাছ থেকে জেনে নিন।

একটি ডিজাইনের কম্পোনেন্ট গুলোকে আপনি বিভিন্ন ভাবে ভাগ করতে পারেন। কীভাবে করবেন সেটা নির্ভর করবে আপনার কাজের ক্ষেত্রের উপর।

* **Programming**--একটি নতুন function বা object তৈরী করবেন কি না এই সিদ্ধান্তটি যেভাবে নিতেন, ঠিক একই পদ্ধতিতে সিদ্ধান্ত নিন। এমন একটি পদ্ধতি হচ্ছে [single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle), যা অনুসারে, একটা কম্পোনেন্ট আদর্শত কেবল একটি কাজই করবে। যদি সেটা বড় হয়ে যায় তাহলে ছোট ছোট সাবকম্পোনেন্ট এ ভাগ করে ফেলতে হবে। 
* **CSS**--আপনি class selector কীসের জন্য তৈরী করতেন সেটা বিবেচনায় রাখুন (যদিও কম্পোনেন্ট আরেকটু কম granular)।
* **Design**--চিন্তা করুন আপনি আপনার ডিজাইনের layer গুলো কীভাবে বিন্যস্ত করতেন।

যদি আপনার JSON গোছানো থাকে, আপনি দেখবেন বেশির ভাগ সময় এটা স্বাভাবিকতই আপনার UI এর কম্পোনেন্ট এর গঠন বিন্যাসের সাথে মিলে যাবে। এর কারণ UI এবং ডেটা মডেলগুলো সাধারণত একই information architecture ব্যবহার করে, অর্থাৎ একই আকারে বিন্যস্ত হয়। আপনার UI কে এমন ভাবে কম্পোনেন্ট এ ভাগ করুন যেন প্রতিটি কম্পোনেন্ট আপনার ডেটা মডেলের একটি অংশের সাথে মিলে যায়। 

এই স্ক্রিনে ৫টি কম্পোনেন্ট রয়েছেঃ

<FullWidth>

<CodeDiagram flip>

<img src="/images/docs/s_thinking-in-react_ui_outline.png" width="500" style={{margin: '0 auto'}} />

1. `FilterableProductTable` (grey) পুরো অ্যাপটিকে ধারণ করে।
2. `SearchBar` (blue) ইউজারের ইনপুট নেয়।
3. `ProductTable` (lavender) ফিল্টারগুলো দেখায় এবং ইউজারের ইনপুটের হিসেবে তালিকা দেখায়।
4. `ProductCategoryRow` (green) প্রতিটি ক্যাটাগরির জন্য একটি শিরোনাম দেখায়।
5. `ProductRow`	(yellow) প্রতিটি পণ্যের জন্য একটি সারি দেখায়।

</CodeDiagram>

</FullWidth>

আপনি যদি `ProductTable` (lavender) খেয়াল করেন, আপনি দেখবেন যে টেবিল হেডারে (যার মধ্যে "Name" এবং "Price" লেবেলগুলো রয়েছে) নিজেই নিজের কম্পোনেন্ট না। এটা আপনার পছন্দ অনুযায়ী সিদ্ধান্তের বিষয়। আপনি যেকোন দিকে যেতে পারেন। এই উদাহরণে, এটা `ProductTable` এর অংশ কারণ এটা `ProductTable`এর তালিকার মধ্যে দেখা যায়। কিন্তু, এই হেডার যদি জটিল আকার ধারণ করে (উদাহরণস্বরূপ, আপনি যদি সর্টিং যুক্ত করতে চান), আপনি এটাকে এর নিজের `ProductTableHeader` কম্পোনেন্টে নিয়ে যেতে পারেন।

এখন যেহেতু আপনি মকাপে কম্পোনেন্টগুলো চিনে ফেলেছেন, সেগুলোকে একটা hierarchy তে সাজিয়ে ফেলুন। মকাপে যেই কম্পোনেন্টগুলো অন্য একটি কম্পোনেন্ট এর মধ্যে রয়েছে সেগুলো hierarchy তে child component হিসেবে দেখানো উচিত।

* `FilterableProductTable`
    * `SearchBar`
    * `ProductTable`
        * `ProductCategoryRow`
        * `ProductRow`

## ধাপ ২ঃ React এ একটি স্ট্যাটিক ভার্সন তৈরী করুন {/*step-2-build-a-static-version-in-react*/}

এখন যেহেতু আপনার কাছে কম্পোনেন্ট hierarchy আছে, এখন সময় অ্যাপ ইমপ্লিমেন্ট করার। সবচেয়ে সোজাসাপ্টা পথ হল এমন একটা ভার্সন তৈরী করা যেটা নতুন কোন interactivity যুক্ত না করে আপনার ডেটা মডেল থেকে UI তৈরী করতে পারে...প্রাথমিকভাবে! বেশিরভাগ সময়ে প্রথমে একটি স্ট্যাটিক ভার্সন তৈরী করা এবং পরে এর সাথে interactivity যুক্ত করা সহজ হয়। স্ট্যাটিক ভার্সন তৈরী করতে প্রচুর কোড লেখবার দরকার পড়ে এবং চিন্তা করতে হয় কম। অন্যদিকে interactivity যুক্ত করার জন্য চিন্তা করতে হয় অনেক এবং এত বেশি লেখার দরকার পড়ে না।     

প্রথমে বানাতে হবে আপনার অ্যাপের একটা স্ট্যাটিক ভার্সন যা আপনার ডেটা মডেল প্রদর্শন করতে পারে। এজন্য আপনি এমন [components](/learn/your-first-component) বানাতে চাইবেন যা অন্যান্য কম্পোনেন্ট ব্যবহার করবে এবং [props](/learn/passing-props-to-a-component) ব্যবহার করে ডেটা আদান প্রদান করবে। Parent থেকে child এ ডেটা পাঠাতে props ব্যবহার করা হয়। (যদি আপনার [state](/learn/state-a-components-memory)এর বিষয়ে জানাশোনা থাকে, এই স্ট্যাটিক ভার্সন বানাতে গিয়ে state এর কথা একদমই মাথায় আনবেন না। State শুধুমাত্র interactivity-র জন্য অর্থাৎ সেই ডেটা যা সময়ের সাথে সাথে বদলায়। যেহেতু এইটা আপনার অ্যাপের একটি স্ট্যাটিক ভার্সন, আপনার state এর প্রয়োজন নেই।)

আপনি "top down" পন্থায় যেতে পারেন, যেখানে hierarchy-তে উপর থেকে নিচের দিকে কম্পোনেন্টগুলো আপনি বানাতে থাকবেন (যেমন `FilterableProductTable`) অথবা "bottom up" পদ্ধতি ব্যবহার করতে পারেন যেক্ষেত্রে  আগে নিচের দিকের কম্পোনেন্টগুলো আগে বানাতে হবে এবং পর্যায়ক্রমে উপরের দিকে যাবেন (যেমন `ProductRow`)।  সরল একটা অ্যাপের ক্ষেত্রে top-down করাই সাধারণত সহজতর হয়, একটু জটিল বড় কাজের ক্ষেত্রে bottom-up করা সুবিধাজনক হয়।

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

(এই কোডটি বেশি জটিল মনে হলে, [Quick Start](/learn/) আগে দেখে নিন!)

কম্পোনেন্ট বানানো হয়ে গেলে, আপনার কাছে একটি লাইব্রেরি থাকবে যেখানে এমন কম্পোনেন্ট থাকবে যা আপনার ডেটা মডেলকে প্রদর্শন করে এবং যা পুনরায় ব্যবহারযোগ্য। যেহেতু এটি একটি স্ট্যাটিক অ্যাপ, সুতরাং কম্পোনেন্টগুলি কেবল JSX রিটার্ন করবে। আপনার hierarchy-র একদম শীর্ষে থাকা কম্পোনেন্ট (`FilterableProductTable`) আপনার ডেটা মডলকে prop হিসেবে গ্রহণ করবে। একে বলা হয় _one-way data flow_ কারণ এক্ষেত্রে tree-তে উপরের দিকে থাকা কম্পোনেন্টগুলো থেকে নিচের দিকে থাকা কম্পোনেন্টের দিকে ডেটা প্রবাহিত হয়।

<Pitfall>

এ পর্যন্ত আপনার কোন state value ব্যবহার করা উচিত হবে না। সেটা পরবর্তী ধাপের জন্য!

</Pitfall>

##  ধাপ ৩ঃ UI state এর ন্যূনতম অথচ সম্পূর্ণ একটা অবস্থা খুঁজে বের করা {/*step-3-find-the-minimal-but-complete-representation-of-ui-state*/}

UI interactive করার জন্য আপনাকে ব্যবহারকারীদেরকে ভিতরের ডেটা মডেল পরিবর্তনের সুযোগ দিতে হবে। এর জন্য আপনি *state* ব্যবহার করবেন। 

ধরে নেন state হইল সেই ক্ষুদ্রতম ডেটার সেট যেটার পরিবর্তন আপনার অ্যাপের মনে রাখা প্রয়োজন। State এর গঠনকাঠামো এর ক্ষেত্রে সবচেয়ে জরুরী মূলমন্ত্র হল একে [DRY (Don't Repeat Yourself)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) রাখতে হবে। আপনার অ্যাপ্লিকেশনের একদম ন্যুনতম যে representation দরকার সেটা খুঁজে বের করুন এবং বাকি সব কিছু যখন দরকার সে অনুযায়ী compute করুন। উদাহরণস্বরূপ, আপনি যদি একটা বাজারের তালিকা করেন সেক্ষেত্রে পণ্যগুলো state এর মধ্যে array হিসেবে  রাখতে পারেন। যদি আপনাকে পণ্যের সংখ্যা প্রদর্শন করার দরকার পড়ে, তাহলে সেটা আরেকটা state হিসেবে না রেখে বরং array এর length হিসেব করে বের করুন।

এখন এই অ্যাপ্লিকেশনের উদাহরণে প্রতিটা তথ্য নিয়ে একটু ভাবুনঃ 

1. পণ্যের আসল তালিকা
2. ব্যবহারকারী পণ্য খুঁজার জন্য যা লিখবে অর্থাৎ সার্চ টেক্সট
3. চেকবক্সের ভ্যালু
4. পণ্যের বাছাই করা তালিকা

এগুলোর মধ্যে কোনগুলো state? যেগুলো state না সেগুলো আগে চিহ্নিত করুনঃ

* এটা কি সময়ের সাথে **অপরিবর্তিত থাকে**? যদি থাকে, তাহলে এটা state না।
* এটা কি props এর মাধ্যমে **parent থেকে পাঠানো হয়**? যদি উত্তর হ্যাঁ হয়, তাহলে এটা state না।
* এখনকার state এবং props ব্যবহার করে আপনি কি এটা **compute করতে পারেন**? সেক্ষেত্রে, এটা *কোনভাবেই* state না!

এর পরে যা যা থাকে, তা সম্ভবত state। 

আমরা একে একে আরেকবার বুঝে নেইঃ

1. পণ্যের আসল তালিকা  **props হিসেবে চলে আসে, সুতরাং এটা state না।** 
2. খুঁজার জন্য ব্যবহারকারী যে ইনপুট দিবেন সেটা state হবার কথা, কারণ এটা সময়ের সাথে সাথে বদলে যাবে এবং কোন কিছু থেকে এটা compute করা সম্ভব না।
3. চেকবক্সের ভ্যালুটাও একটা state কারণ এটা সময়ের সাথে সাথে বদলাবে এবং কোনভাবে compute করা সম্ভব না।
4. পণ্য বাছাই করা তালিকাটি **state না** কারণ চেকবক্সের ভ্যালু এবং পণ্যের তালিকা থেকে সার্চ টেক্সট বাছাই করার মাধ্যমে তা **compute করা সম্ভব**। 

অর্থাৎ, শুধুমাত্র সার্চ টেক্সট এবং চেকবক্সের ভ্যালু এই দুইটাই state! কী সুন্দরমত হয়ে গেল!

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
