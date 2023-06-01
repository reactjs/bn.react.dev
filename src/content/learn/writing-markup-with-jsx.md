---
title: JSX দিয়ে মার্কআপ লেখা
---

<Intro>

*JSX* হল JavaScript-এর জন্য একটি সিনট্যাক্স এক্সটেনশন যা আপনাকে জাভাস্ক্রিপ্ট ফাইলের ভিতরে HTML-এর মতো মার্কআপ লিখতে দেয়। যদিও কম্পোনেন্টগুলো লেখার অন্যান্য উপায় রয়েছে, বেশিরভাগ ডেভেলপার  JSX এর সংক্ষিপ্ততা পছন্দ করে এবং বেশিরভাগ কোডবেস এটি ব্যবহার করে।

</Intro>

<YouWillLearn>

* React কেন রেন্ডারিং লজিকের সাথে মার্কআপকে মিশ্রিত করে
* JSX কিভাবে HTML থেকে আলাদা
* কিভাবে JSX এর সাথে তথ্য প্রদর্শন করবেন

</YouWillLearn>

## JSX: জাভাস্ক্রিপ্টে মার্কআপ করা {/*jsx-putting-markup-into-javascript*/}

ওয়েবটি এইচটিএমএল, সিএসএস এবং জাভাস্ক্রিপ্টে তৈরি করা হয়েছে। বহু বছর ধরে, ওয়েব ডেভেলপাররা HTML-এ কন্টেন্ট, সিএসএস-এ ডিজাইন এবং জাভাস্ক্রিপ্টে লজিক রাখে—প্রায়ই আলাদা ফাইলে! পেইজের লজিক জাভাস্ক্রিপ্টে আলাদাভাবে থাকলেও বিষয়বস্তু HTML-এর মধ্যে চিহ্নিত করা হয়েছিল:

<DiagramGroup>

<Diagram name="writing_jsx_html" height={237} width={325} alt="HTML markup with purple background and a div with two child tags: p and form. ">

HTML

</Diagram>

<Diagram name="writing_jsx_js" height={237} width={325} alt="Three JavaScript handlers with yellow background: onSubmit, onLogin, and onClick.">

জাভাস্ক্রিপ্ট

</Diagram>

</DiagramGroup>

কিন্তু ওয়েব যত বেশি ইন্টারেক্টিভ হয়ে ওঠে, যুক্তি ক্রমবর্ধমান বিষয়বস্তু নির্ধারণ করে। এইচটিএমএল এর দায়িত্বে ছিল জাভাস্ক্রিপ্ট!  **এই কারণেই React-এ, রেন্ডারিং লজিক এবং মার্কআপ একই জায়গায় একসাথে থাকে—কম্পোনেন্ট।**

<DiagramGroup>

<Diagram name="writing_jsx_sidebar" height={330} width={325} alt="React component with HTML and JavaScript from previous examples mixed. Function name is Sidebar which calls the function isLoggedIn, highlighted in yellow. Nested inside the function highlighted in purple is the p tag from before, and a Form tag referencing the component shown in the next diagram.">

`Sidebar.js` React component

</Diagram>

<Diagram name="writing_jsx_form" height={330} width={325} alt="React component with HTML and JavaScript from previous examples mixed. Function name is Form containing two handlers onClick and onSubmit highlighted in yellow. Following the handlers is HTML highlighted in purple. The HTML contains a form element with a nested input element, each with an onClick prop.">

`Form.js` React component

</Diagram>

</DiagramGroup>

একটি বাটনের রেন্ডারিং লজিক এবং মার্কআপ একসাথে রাখা নিশ্চিত করে যে তারা প্রতিটি ইডিটে একে অপরের সাথে সিঙ্কে থাকে। বিপরীতভাবে, বিশদ বিবরণ যা সম্পর্কহীন, যেমন বাটনের মার্কআপ এবং একটি সাইডবারের মার্কআপ, একে অপরের থেকে বিচ্ছিন্ন হয়ে যায়, যার ফলে তাদের যেকোনো একটিকে নিজেরাই পরিবর্তন করা নিরাপদ করে তোলে।

প্রতিটি React কম্পোনেন্ট একটি জাভাস্ক্রিপ্ট ফাংশন যাতে কিছু মার্কআপ থাকতে পারে যা React ব্রাউজারে রেন্ডার করে। React কম্পোনেন্টগুলি সেই মার্কআপটি উপস্থাপন করতে JSX নামক একটি সিনট্যাক্স এক্সটেনশন ব্যবহার করে। JSX দেখতে অনেকটা HTML এর মত, কিন্তু এটি একটু জটিল এবং ডাইন্যামিক তথ্য প্রদর্শন করতে পারে। এটি বোঝার সর্বোত্তম উপায় হল কিছু HTML মার্কআপকে JSX মার্কআপে রূপান্তর করা।

<Note>

জেএসএক্স এবং React দুটি পৃথক জিনিস। এগুলি প্রায়শই একসাথে ব্যবহার করা হয়,  তবে  আপনি একে অপরের থেকে স্বাধীনভাবে [ব্যবহার করতে পারেন](https://reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html#whats-a-jsx-transform) ।JSX হল একটি সিনট্যাক্স এক্সটেনশন, যখন React হল একটি JavaScript লাইব্রেরি।

</Note>

##  HTML কে JSX এ রূপান্তর {/*converting-html-to-jsx*/}

ধরুন আপনার কিছু (ভ্যালিড) HTML আছে:

```html
<h1>Hedy Lamarr's Todos</h1>
<img 
  src="https://i.imgur.com/yXOvdOSs.jpg" 
  alt="Hedy Lamarr" 
  class="photo"
>
<ul>
    <li>Invent new traffic lights
    <li>Rehearse a movie scene
    <li>Improve the spectrum technology
</ul>
```

এবং আপনি এটি আপনার কম্পোনেন্ট- এ রাখতে চান:

```js
export default function TodoList() {
  return (
    // ???
  )
}
```

আপনি যদি এটিকে কপি এবং পেস্ট করেন তবে এটি কাজ করবে না:


<Sandpack>

```js
export default function TodoList() {
  return (
    // This doesn't quite work!
    <h1>Hedy Lamarr's Todos</h1>
    <img 
      src="https://i.imgur.com/yXOvdOSs.jpg" 
      alt="Hedy Lamarr" 
      class="photo"
    >
    <ul>
      <li>Invent new traffic lights
      <li>Rehearse a movie scene
      <li>Improve the spectrum technology
    </ul>
  );
}
```

```css
img { height: 90px }
```

</Sandpack>

এর কারণ হল JSX জটিল এবং এইচটিএমএল থেকে আরও কিছু নিয়ম রয়েছে! আপনি যদি উপরের error messages পড়ে থাকেন, তাহলে তারা আপনাকে মার্কআপ ঠিক করার জন্য গাইড করবে, অথবা আপনি নীচের নির্দেশিকা অনুসরণ করতে পারেন।

<Note>

বেশিরভাগ সময়, React-এর অন-স্ক্রিন error messages আপনাকে সমস্যাটি কোথায় তা খুঁজে পেতে সহায়তা করবে। আপনি আটকে গেলে এগুলো ভালভাবে পড়ে নিতে পারেন!

</Note>

## JSX এর নিয়মাবলী {/*the-rules-of-jsx*/}

### 1. Return a single root element {/*1-return-a-single-root-element*/}

একটি কম্পোনেন্ট থেকে একাধিক ইলিমেন্ট রিটার্ন করতে,**একটি একক অভিভাবক ট্যাগ দিয়ে তাদের মোড়িয়ে দিন**.

উদাহরণস্বরূপ , আপনি `<div>` ব্যবহার করতে পারেন:

```js {1,11}
<div>
  <h1>Hedy Lamarr's Todos</h1>
  <img 
    src="https://i.imgur.com/yXOvdOSs.jpg" 
    alt="Hedy Lamarr" 
    class="photo"
  >
  <ul>
    ...
  </ul>
</div>
```


আপনি যদি আপনার মার্কআপে  অতিরিক্ত `<div>` যোগ করতে না চান, তাহলে আপনি এর পরিবর্তে `<>` এবং `</>` লিখতে পারেন:

```js {1,11}
<>
  <h1>Hedy Lamarr's Todos</h1>
  <img 
    src="https://i.imgur.com/yXOvdOSs.jpg" 
    alt="Hedy Lamarr" 
    class="photo"
  >
  <ul>
    ...
  </ul>
</>
```

এই খালি ট্যাগটিকে *[ফ্র্যাগমেন্ট ](/reference/react/Fragment)* বলা হয়। ফ্র্যাগমেন্ট আপনাকে ব্রাউজার HTML ট্রিতে কোনো চিহ্ন না রেখে জিনিসগুলিকে গোষ্ঠীবদ্ধ করতে দেয়।

<DeepDive>

#### কেন একাধিক JSX ট্যাগ মোড়ানো প্রয়োজন? {/*why-do-multiple-jsx-tags-need-to-be-wrapped*/}

JSX দেখতে HTML এর মতো, কিন্তু আসলে এটি প্লেইন জাভাস্ক্রিপ্ট অবজেক্টে রূপান্তরিত হয়। আপনি একটি অ্যারের মধ্যে মোড়ানো ছাড়া একটি ফাংশন থেকে দুটি অবজেক্ট ফেরত দিতে পারবেন না। এটি ব্যাখ্যা করে যে আপনি কেন দুটি JSX ট্যাগকে অন্য ট্যাগ বা একটি ফ্র্যাগমেন্টে মোড়ানো ছাড়াই ফেরত দিতে পারবেন না।

</DeepDive>

### 2. সবগুলো ট্যাগ ক্লোজ করুন {/*2-close-all-the-tags*/}

JSX-এর জন্য ট্যাগগুলি স্পষ্টভাবে ক্লোজ করতে হবে:  `<img>`-এর মতো স্ব-বন্ধ হওয়া ট্যাগগুলিকে অবশ্যই `<img />` হতে হবে, এবং `<li>oranges` মতো মোড়ানো ট্যাগগুলিকে `<li>oranges</li>` হিসাবে লিখতে হবে।

হেডি লামারের ইমেইজ এবং লিস্টআইটেমগুলি ক্লোজ হলে এমন দেখায়:

```js {2-6,8-10}
<>
  <img 
    src="https://i.imgur.com/yXOvdOSs.jpg" 
    alt="Hedy Lamarr" 
    class="photo"
   />
  <ul>
    <li>Invent new traffic lights</li>
    <li>Rehearse a movie scene</li>
    <li>Improve the spectrum technology</li>
  </ul>
</>
```

### 3. ক্যামেলকেস <s>প্রায়</s> অধিকাংশ জিনিস! {/*3-camelcase-salls-most-of-the-things*/}

JSX জাভাস্ক্রিপ্টে পরিণত হয় এবং JSX-এ লেখা বৈশিষ্ট্যগুলি জাভাস্ক্রিপ্ট অবজেক্টের কী হয়ে ওঠে। আপনার নিজস্ব কম্পোনেন্টে, আপনি প্রায়শই এই এট্রিবিউটগুলোকে ভেরিয়েবলে রাখবেন। কিন্তু জাভাস্ক্রিপ্টের ভেরিয়েবলের নামের সীমাবদ্ধতা রয়েছে। উদাহরণস্বরূপ, তাদের নামের ড্যাশ থাকতে পারে না বা `class`-এর মতো সংরক্ষিত শব্দ থাকতে পারে না।

এই কারণেই, React-এ, অনেক HTML এবং SVG অ্যাট্রিবিউট ক্যামেলকেসে লেখা থাকে। উদাহরণস্বরূপ, `stroke-width` এর পরিবর্তে আপনি `strokeWidth` ব্যবহার করেন। যেহেতু `class` একটি সংরক্ষিত শব্দ, তাই React-এ আপনি পরিবর্তে `className` লিখবেন,[সংশ্লিষ্ট DOM প্রপার্টি](https://developer.mozilla.org/en-US/docs/Web/API/Element/className)এর নামানুসারে। ):

```js {4}
<img 
  src="https://i.imgur.com/yXOvdOSs.jpg" 
  alt="Hedy Lamarr" 
  className="photo"
/>
```

আপনি [DOM কম্পোনেন্ট প্রপসের তালিকায় এই সমস্ত বৈশিষ্ট্যগুলি খুঁজে পেতে পারেন](/reference/react-dom/components/common) If you get one wrong, don't worry—React will print a message with a possible correction to the [browser console.](https://developer.mozilla.org/docs/Tools/Browser_Console)। আপনি যদি একটিও ভুল বুঝে থাকেন, চিন্তা করবেন না—React ব্রাউজার কনসোলে সম্ভাব্য সংশোধন সহ একটি মেসেজ প্রিন্ট করবে।

<Pitfall>

ঐতিহাসিক কারণে, [`aria-*`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA) এবং [`data-*`](https://developer.mozilla.org/docs/Learn/HTML/Howto/Use_data_attributes) অ্যাট্রিবিউটগুলি ড্যাশ সহ HTML এর মতো লেখা হয়।

</Pitfall>

### প্রো-টিপ: JSX কনভার্টার ব্যবহার করুন {/*pro-tip-use-a-jsx-converter*/}

বিদ্যমান মার্কআপে এই সমস্ত বৈশিষ্ট্যগুলিকে রূপান্তর করা ক্লান্তিকর হতে পারে! আপনার বিদ্যমান HTML এবং SVG কে JSX-এ অনুবাদ করতে আমরা একটি [কনভার্টার](https://transform.tools/html-to-jsx) ব্যবহার করার পরামর্শ দিই। রূপান্তরকারীগুলি অনুশীলনে খুব দরকারী, তবে কী চলছে তা বোঝার জন্য এটি এখনও জরুরী যেন আপনি স্বাচ্ছন্দ্যে JSX লিখতে পারেন৷

এখানে আপনার চূড়ান্ত ফলাফল:

<Sandpack>

```js
export default function TodoList() {
  return (
    <>
      <h1>Hedy Lamarr's Todos</h1>
      <img 
        src="https://i.imgur.com/yXOvdOSs.jpg" 
        alt="Hedy Lamarr" 
        className="photo" 
      />
      <ul>
        <li>Invent new traffic lights</li>
        <li>Rehearse a movie scene</li>
        <li>Improve the spectrum technology</li>
      </ul>
    </>
  );
}
```

```css
img { height: 90px }
```

</Sandpack>

<Recap>

এখন আপনি জানেন JSX কেন রয়েছে এবং কীভাবে এটি কম্পনেন্টে ব্যবহার করবেন:

* রিঅ্যাক্ট কম্পোনেন্ট গ্রুপ রেন্ডারিং লজিক মার্কআপের সাথে একসাথে থাকে কারণ তারা সম্পর্কিত।
* JSX কিছু পার্থক্য সহ HTML এর অনুরূপ। আপনার প্রয়োজন হলে আপনি একটি [কনভার্টার](https://transform.tools/html-to-jsx) ব্যবহার করতে পারেন।
* Error messages প্রায়ই আপনার মার্কআপ ঠিক করার জন্য সঠিক দিক নির্দেশ করবে.

</Recap>



<Challenges>

#### Convert some HTML to JSX {/*convert-some-html-to-jsx*/}

এই HTML একটি কম্পোনেন্টে ব্যবহার করা হয়েছে, কিন্তু এটি সঠিক JSX নয়। ঠিক করুন:

<Sandpack>

```js
export default function Bio() {
  return (
    <div class="intro">
      <h1>Welcome to my website!</h1>
    </div>
    <p class="summary">
      You can find my thoughts here.
      <br><br>
      <b>And <i>pictures</b></i> of scientists!
    </p>
  );
}
```

```css
.intro {
  background-image: linear-gradient(to left, violet, indigo, blue, green, yellow, orange, red);
  background-clip: text;
  color: transparent;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.summary {
  padding: 20px;
  border: 10px solid gold;
}
```

</Sandpack>

এটা নিজের হাতে করবেন নাকি কনভার্টার ব্যবহার করবেন সেটা আপনার ব্যাপার!

<Solution>

<Sandpack>

```js
export default function Bio() {
  return (
    <div>
      <div className="intro">
        <h1>Welcome to my website!</h1>
      </div>
      <p className="summary">
        You can find my thoughts here.
        <br /><br />
        <b>And <i>pictures</i></b> of scientists!
      </p>
    </div>
  );
}
```

```css
.intro {
  background-image: linear-gradient(to left, violet, indigo, blue, green, yellow, orange, red);
  background-clip: text;
  color: transparent;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.summary {
  padding: 20px;
  border: 10px solid gold;
}
```

</Sandpack>

</Solution>

</Challenges>
