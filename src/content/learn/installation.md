---
title: ইন্সটলেশন
---

<Intro>

React শুরু থেকেই gradual adoption এর জন্য সাজানো হয়েছে। আপনার যতটুকু কম বা যতটুকু বেশি প্রয়োজন React ব্যবহার করতে পারেন। আপনি হয়ত কেবল React এর স্বাদ চান, বা একটা HTML পেইজে কিছু interactivity যুক্ত করতে চান, অথবা একটা জটিল React-powered অ্যাপ বানাতে চান, এই সেকশন থেকে শুরু করতে পারেন।

</Intro>

<YouWillLearn isChapter={true}>

* [কীভাবে একটি React প্রজেক্ট শুরু করবেন](/learn/start-a-new-react-project)
* [ইতোমধ্যে বানানো একটা প্রজেক্টে কীভাবে React যুক্ত করবেন](/learn/add-react-to-an-existing-project)
* [কীভাবে এডিটর সেট আপ করবেন](/learn/editor-setup)
* [কীভাবে React ডেভেলপার টুলস ইনস্টল করবেন](/learn/react-developer-tools)

</YouWillLearn>

## React এর স্বাদ নিয়ে দেখুন {/*try-react*/}

React নিয়ে খেলবার জন্য আপনাকে কিছু ইনস্টল করতে হবে না। এই স্যান্ডবক্স এডিট করার চেষ্টা করে দেখুন।

<Sandpack>

```js
function Greeting({ name }) {
  return <h1>Hello, {name}</h1>;
}

export default function App() {
  return <Greeting name="world" />
}
```

</Sandpack>

এটা আপনি সরাসরি এডিট করতে পারেন অথবা ডান পাশে উপরে কোণায় থাকা "ফর্ক" বাটন চেপে একটা নতুন ট্যাবে খুলতে পারেন। 

React ডকুমেন্টেশনের বেশির ভাগ পেইজেই এমন স্যান্ডবক্স রয়েছে। React ডকুমেন্টেশনের এর বাইরে এমন অনেক অনলাইন স্যান্ডবক্স আছে যেগুলো React ব্যবহার সমর্থন করঃ উদাহরণস্বরূপ, [CodeSandbox](https://codesandbox.io/s/new), [StackBlitz](https://stackblitz.com/fork/react), অথবা [CodePen।](https://codepen.io/pen?&editors=0010&layout=left&prefill_data_id=3f4569d1-1b11-4bce-bd46-89090eed5ddb)

### নিজের কম্পিউটারে React চালিয়ে দেখুন {/*try-react-locally*/}

আপনার কম্পিউটারে React চালাবার জন্য [এই HTML পেইজটি ডাউনলোড করুন।](https://gist.githubusercontent.com/gaearon/0275b1e1518599bbeafcde4722e79ed1/raw/db72dcbf3384ee1708c4a07d3be79860db04bff0/example.html) এটা আপনার এডিটরে খুলুন এবং আপনার ব্রাউজারে খুলুন।

## একটি নতুন React প্রজেক্ট শুরু করুন {/*start-a-new-react-project*/}

আপনি যদি সম্পূর্ণ React ব্যবহার করে একটি অ্যাপ বা ওয়েবসাইট বানাতে চান, [একটি নতুন React প্রজেক্ট শুরু করুন।](/learn/start-a-new-react-project)

## ইতোমধ্যে বানানো একটি প্রজেক্টে React যুক্ত করুন {/*add-react-to-an-existing-project*/}

যদি আপনার ইতোমধ্যে আছে এমন একটি অ্যাপ বা ওয়েবসাইটে React ব্যবহার করতে চান তাহলে [সেটায় React যুক্ত করুন](/learn/add-react-to-an-existing-project)

## এর পরের ধাপ {/*next-steps*/}

React এর সবচেয়ে জরুরী ধারণাগুলো আপনার প্রতিদিন মুখোমুখি হবে সেগুলো সম্বন্ধে জানতে [কুইক স্টার্ট](/learn) গাইডে চলে যান। 

