---
title: নতুন প্রোজেক্ট শুরু করুন
---

<Intro>

<<<<<<< HEAD
আপনি যদি React দিয়ে কোন অ্যাপ বা ওয়েবসাইট বানাতে চান তাহলে, React কমিউনিটি ব্যাবহার করে এমন যেকোনো একটি জনপ্রিয় ফ্রেমওয়ার্ক ব্যাবহার করতে আমরা আপনাকে উৎসাহিত করব। এই ফ্রেমওয়ার্ক গুলো আপনাকে গুরুত্বপূর্ণ কিছু ফিচার দিবে, যেমনঃ Routing, Data fetching এবং HTML generate করা, যা মোটামুটি সব ধরণের অ্যাপ অথবা ওয়েবসাইটের দরকার হয়।
=======
If you want to build a new app or a new website fully with React, we recommend picking one of the React-powered frameworks popular in the community.
>>>>>>> 2372ecf920ac4cda7c900f9ac7f9c0cd4284f281

</Intro>


<<<<<<< HEAD
**লোকাল ডেভেলপমেন্টের জন্যে আপনার অবশ্যই [Node.js](https://nodejs.org/en/) ইন্সটল থাকতে হবে।** আপনি চাইলে আপনার production সার্ভারে Node.js ইন্সটল করতে পারেন কিন্তু এটা আবশ্যক নয় কারণ অনেক React ফ্রেমওয়ার্ক আপনার অ্যাপ বা ওয়েবসাইট এর স্ট্যাটিক HTML/CSS/JS তৈরি করে দেয়।
=======
You can use React without a framework, however we’ve found that most apps and sites eventually build solutions to common problems such as code-splitting, routing, data fetching, and generating HTML. These problems are common to all UI libraries, not just React.
>>>>>>> 2372ecf920ac4cda7c900f9ac7f9c0cd4284f281

By starting with a framework, you can get started with React quickly, and avoid essentially building your own framework later.

<DeepDive>

#### Can I use React without a framework? {/*can-i-use-react-without-a-framework*/}

You can definitely use React without a framework--that's how you'd [use React for a part of your page.](/learn/add-react-to-an-existing-project#using-react-for-a-part-of-your-existing-page) **However, if you're building a new app or a site fully with React, we recommend using a framework.**

Here's why.

Even if you don't need routing or data fetching at first, you'll likely want to add some libraries for them. As your JavaScript bundle grows with every new feature, you might have to figure out how to split code for every route individually. As your data fetching needs get more complex, you are likely to encounter server-client network waterfalls that make your app feel very slow. As your audience includes more users with poor network conditions and low-end devices, you might need to generate HTML from your components to display content early--either on the server, or during the build time. Changing your setup to run some of your code on the server or during the build can be very tricky.

**These problems are not React-specific. This is why Svelte has SvelteKit, Vue has Nuxt, and so on.** To solve these problems on your own, you'll need to integrate your bundler with your router and with your data fetching library. It's not hard to get an initial setup working, but there are a lot of subtleties involved in making an app that loads quickly even as it grows over time. You'll want to send down the minimal amount of app code but do so in a single client–server roundtrip, in parallel with any data required for the page. You'll likely want the page to be interactive before your JavaScript code even runs, to support progressive enhancement. You may want to generate a folder of fully static HTML files for your marketing pages that can be hosted anywhere and still work with JavaScript disabled. Building these capabilities yourself takes real work.

**React frameworks on this page solve problems like these by default, with no extra work from your side.** They let you start very lean and then scale your app with your needs. Each React framework has a community, so finding answers to questions and upgrading tooling is easier. Frameworks also give structure to your code, helping you and others retain context and skills between different projects. Conversely, with a custom setup it's easier to get stuck on unsupported dependency versions, and you'll essentially end up creating your own framework—albeit one with no community or upgrade path (and if it's anything like the ones we've made in the past, more haphazardly designed).

If your app has unusual constraints not served well by these frameworks, or you prefer to solve these problems yourself, you can roll your own custom setup with React. Grab `react` and `react-dom` from npm, set up your custom build process with a bundler like [Vite](https://vitejs.dev/) or [Parcel](https://parceljs.org/), and add other tools as you need them for routing, static generation or server-side rendering, and more.

</DeepDive>

## Production grade React ফ্রেমওয়ার্কের তালিকা {/*production-grade-react-frameworks*/}

These frameworks support all the features you need to deploy and scale your app in production and are working towards supporting our [full-stack architecture vision](#which-features-make-up-the-react-teams-full-stack-architecture-vision). All of the frameworks we recommend are open source with active communities for support, and can be deployed to your own server or a hosting provider. If you’re a framework author interested in being included on this list, [please let us know](https://github.com/reactjs/react.dev/issues/new?assignees=&labels=type%3A+framework&projects=&template=3-framework.yml&title=%5BFramework%5D%3A+).

<<<<<<< HEAD
**[Next.js](https://nextjs.org/) একটি full-stack React ফ্রেমওয়ার্ক।** এটি খুবই জনপ্রিয় এবং খুবই সহজে একটি ছোট ব্লগ ওয়েবসাইট অথবা অনেক বড় এবং জটিল অ্যাপ বানানোর জন্যে উপযোগী। নিচের কমান্ড টি আপনার টার্মিনাল এ রান করে আপনি একটি Next.js প্রোজেক্ট তৈরি করতে পারবেন।
=======
### Next.js {/*nextjs-pages-router*/}

**[Next.js' Pages Router](https://nextjs.org/) is a full-stack React framework.** It's versatile and lets you create React apps of any size--from a mostly static blog to a complex dynamic application. To create a new Next.js project, run in your terminal:
>>>>>>> 2372ecf920ac4cda7c900f9ac7f9c0cd4284f281

<TerminalBlock>
npx create-next-app@latest
</TerminalBlock>

আপনি যদি Next.js এ নতুন হয়ে থাকেন তাহলে [learn Next.js course](https://nextjs.org/learn)-টা দেখে ফেলুন।

Next.js এর রক্ষণাবেক্ষণের কাজ করে থাকে [Vercel](https://vercel.com/)। যেকোনো Node.js অথবা serverless হোস্টিং, অথবা আপনার নিজস্ব সার্ভারে [একটি Next.js অ্যাপ deploy](https://nextjs.org/docs/deployment) করতে পারবেন। Next.js সম্পূর্ণ [স্ট্যাটিক এক্সপোর্ট](https://nextjs.org/docs/pages/building-your-application/deploying/static-exports) সাপোর্ট করে যার কোন সার্ভারের প্রয়োজন নেই।

### Remix {/*remix*/}

**[Remix](https://remix.run/) হল nested routing সহ একটি full-stack React ফ্রেমওয়ার্ক।** এটি আপনার অ্যাপকে ছোট ছোট অংশে ভাগ করে যাতে সেগুলোকে পারালাল ভাবে ডাটা লোড করতে পারে। এটি ইউজার এর কাজের এর উপর ভিত্তি করে সেই ছোট ছোট অংশ গুলোকে রিফ্রেশ করতেও সাহায্য করে। নিচের কমান্ড টি আপনার টার্মিনাল এ রান করে আপনি একটি Remix প্রোজেক্ট তৈরি করতে পারবেন।

<TerminalBlock>
npx create-remix
</TerminalBlock>

আপনি যদি Remix এ নতুন হয়ে থাকেন তাহলে তাদের অফিসিয়াল Remix [blog tutorial](https://remix.run/docs/en/main/tutorials/blog) (short) এবং [app tutorial](https://remix.run/docs/en/main/tutorials/jokes) (long) দেখতে পারেন।

Remix এর রক্ষণাবেক্ষণের কাজ করে থাকে [Shopify](https://www.shopify.com/)। Remix প্রোজেক্ট তৈরি করার সময় আপনাকে আপনার [deployment target](https://remix.run/docs/en/main/guides/deployment) করে নিতে হবে। একটি [adapter](https://remix.run/docs/en/main/other-api/adapter) লিখে আপনি আপনার Remix অ্যাপ যেকোনো Node.js বা serverless হোস্টিং ব্যাবহার করে deploy করতে পারবেন।

### Gatsby {/*gatsby*/}

**[Gatsby](https://www.gatsbyjs.com/) CMS-backed ওয়েবসাইটগুলির জন্য তৈরি করা একটি React ফ্রেমওয়ার্ক।** এর সমৃদ্ধ প্লাগইন ইকোসিস্টেম এবং এর ডেটা লেয়ার কন্টেন্ট, APIs এবং সার্ভিস গুলোকে একটি ওয়েবসাইটে একত্রিত করা সহজ করে। নিচের কমান্ড টি আপনার টার্মিনাল এ রান করে আপনি একটি Gatsby প্রোজেক্ট তৈরি করতে পারবেন।

<TerminalBlock>
npx create-gatsby
</TerminalBlock>

আপনি যদি Gatsby এ নতুন হয়ে থাকেন তাহলে তাদের অফিসিয়াল [Gatsby tutorial](https://www.gatsbyjs.com/docs/tutorial/) দেখতে পারেন।

Gatsby এর রক্ষণাবেক্ষণের কাজ করে থাকে [Netlify](https://www.netlify.com/)। আপনি যেকোন স্ট্যাটিক হোস্টিংয়ে একটি সম্পূর্ণ স্ট্যাটিক Gatsby সাইট deploy করতে পারবেন। আপনি যদি শুধুমাত্র সার্ভারের বৈশিষ্ট্যগুলি ব্যবহার করার বিকল্প বেছে নেন, তাহলে নিশ্চিত করুন যে আপনার হোস্টিংয়ে Gatsby এর জন্য সার্ভারের বৈশিষ্ট্যগুলি আছে।

### Expo (নেটিভ অ্যাপের জন্য) {/*expo*/}

যা আপনাকে সত্যিকারের নেটিভ UI সহ সার্বজনীন Android, iOS এবং ওয়েব অ্যাপ তৈরি করতে দেয়।
**[Expo](https://expo.dev/) হল একটি React ফ্রেমওয়ার্ক যা আপনাকে নেটিভ UI সহ অ্যান্ড্রয়েড, iOS এবং ওয়েব অ্যাপ তৈরি করতে দেয়।** এটি [React Native](https://reactnative.dev/) জন্য একটি SDK প্রদান করে যা native অংশগুলিকে ব্যবহার করা সহজ করে তোলে। নিচের কমান্ড টি আপনার টার্মিনাল এ রান করে আপনি একটি Expo প্রোজেক্ট তৈরি করতে পারবেন।

<TerminalBlock>
npx create-expo-app
</TerminalBlock>

আপনি যদি Expo এ নতুন হয়ে থাকেন তাহলে তাদের অফিসিয়াল [Expo tutorial](https://www.gatsbyjs.com/docs/tutorial/) দেখতে পারেন।

Expo এর রক্ষণাবেক্ষণের কাজ করে থাকে [Expo (the company)](https://expo.dev/about)। Expo দিয়ে বিনামূল্যে অ্যাপ তৈরি করতে পারবেন, এবং কোন সীমাবদ্ধতা ছাড়াই আপনি সেগুলিকে Google এবং Apple অ্যাপ স্টোরগুলিতে জমা দিতে পারবেন৷ Expo এছাড়াও অপ্ট-ইন ক্লাউড সার্ভিস প্রদান করে থাকে৷

<<<<<<< HEAD
<DeepDive>

#### আমি কি ফ্রেমওয়ার্ক ছাড়া React ব্যবহার করতে পারি? {/*can-i-use-react-without-a-framework*/}

আপনি অবশ্যই একটি ফ্রেমওয়ার্ক ছাড়া React ব্যবহার করতে পারেন--এইভাবে আপনি আপনার [পৃষ্ঠাটির একটি অংশের জন্য React ব্যবহার করবেন।](/learn/add-react-to-an-existing-project#using-react-for-a-part-of-your-existing-page) **পরিশেষে, আপনি যদি সম্পূর্ণভাবে React সহ একটি নতুন অ্যাপ বা সাইট তৈরি করেন, আমরা একটি ফ্রেমওয়ার্ক ব্যবহার করার পরামর্শ দিই।**

বিস্তারিত কারণ।

যদিও আপনার প্রথমে রাউটিং বা ডেটা আনার প্রয়োজন না হয়, পরবর্তীতে আপনি সম্ভবত তাদের জন্য কিছু লাইব্রেরি যোগ করতে চাইবেন। আপনার জাভাস্ক্রিপ্ট বান্ডেলে প্রতিটি নতুন ফিচার বৃদ্ধি পাওয়ার সাথে সাথে আপনাকে প্রতিটি রুটের জন্য পৃথকভাবে কোড বিভক্ত করার উপায় বের করতে হতে পারে। যেহেতু আপনার ডেটা আনার প্রয়োজনীয়তা আরও জটিল হয়ে উঠছে, আপনি সম্ভবত সার্ভার-ক্লায়েন্ট network waterfalls এর সম্মুখীন হতে পারেন যা আপনার অ্যাপকে খুব স্লো করে দিবে। যেহেতু আপনার ইউজার দের মধ্যে দুর্বল নেটওয়ার্ক সম্পন্ন এবং কম শক্তিশালী ডিভাইস ব্যবহারকারী থাকতে পারে, তাই আপনাকে জলদি কন্টেন্ট দেখানোর জন্য আপনার সার্ভারে বা বিল্ডের এর সময় কম্পোনেন্টস থেকে HTML তৈরি করতে হতে পারে। সার্ভারে বা বিল্ডের সময় কিছু কোড চালানোর জন্য আপনার সেটআপ পরিবর্তন করা খুব কঠিন হতে পারে।

**এই সমস্যাগুলি React এর নয়। এই কারণেই Svelte এর SvelteKit আছে, Vue এর Nuxt আছে ইত্যাদি।** এই সমস্যাগুলি নিজে থেকে সমাধান করতে, আপনাকে রাউটার এবং ডেটা আনার লাইব্রেরির সাথে আপনার বান্ডলারকে সংযোগ করতে হবে। একটি প্রাথমিক কার্যকারী সেটআপ করা কঠিন নয়, তবে একটি অ্যাপ তৈরির সাথে অনেক সূক্ষ্মতা জড়িত থাকে যা সময়ের সাথে সাথে অ্যাপের সাইজ বাড়লেও দ্রুত লোড করতে পারেতে হয়। আপনি একটি পৃষ্ঠা লোড করার জন্যে নুন্নতম কোড পাঠাতে চাইবেন কিন্তু পৃষ্ঠার ডাটা প্যারালাল ভাবে লোড একটি একক ক্লায়েন্ট-সার্ভার রাউন্ডট্রিপে করতে চান। ওয়েবসাইটের Progressive enhancement এর জন্যে আপনি সম্ভবত আপনার জাভাস্ক্রিপ্ট কোডটি চালানোর আগে পৃষ্ঠাটি ইন্টারেক্টিভ করতে চাইবেন। আপনি আপনার মার্কেটিং পেইজ গুলির জন্য সম্পূর্ণরূপে স্ট্যাটিক HTML ফাইলগুলির একটি ফোল্ডার তৈরি করতে চাইতে পারেন যা যে কোনও জায়গায় হোস্ট করা যেতে পারে এবং জাভাস্ক্রিপ্ট বন্ধ থাকেলও কাজ করতে পারে। এই ক্ষমতাগুলি অ্যাপের মধ্যে তৈরি করতে আসল কাজ লাগে।

**কোন অতিরিক্ত কাজ ছাড়াই এই পৃষ্ঠার React ফ্রেমওয়ার্ক গুলো এমনিতেই এই ধরণের সমস্যাগুলো সমাধান করে।** এগুলো আপনাকে খুব সহজে শুরু করতে সাহায্য করে এবং তারপরে আপনার প্রয়োজনের সাথে আপনার অ্যাপটি স্কেল করতে সাহায্য করে। প্রতিটি React ফ্রেমওয়ার্কের একটি কমিউনিটি রয়েছে, তাই প্রশ্নের উত্তর খুঁজে পাওয়া এবং টুলিং আপগ্রেড করা সহজ। ফ্রেমওয়ার্কগুলি আপনার কোডের কাঠামোও দেয়, আপনাকে এবং অন্যদেরকে বিভিন্ন প্রোজেক্টের মধ্যে প্রসঙ্গ এবং দক্ষতা বজায় রাখতে সাহায্য করে। বিপরীতভাবে, একটি কাস্টম সেটাপের  মধ্যে খুব সহজেই unsupported dependency version গুলিতে আটকে যেতে পারেন, এবং শেষমেশ দেখা যাবে আপনি আপনার নিজের ফ্রেমওয়ার্ক তৈরী করে ফেলেছেন—অবশ্যই কোন কমিউনিটি বা আপগ্রেডের পথ ছাড়া (এবং যদি দেখা যায় এটা আমাদের অতীতে তৈরী করা ফ্রেমওয়ার্কগুলোর মত, তাহলে সেটা হবে খুব তাড়াহুড়ো করে ডিজাইন করা একটা ফ্রেমওয়ার্ক)।


আপনি যদি এখনও নিশ্চিত না হন, বা আপনার অ্যাপে অস্বাভাবিক সীমাবদ্ধতা থাকে যা এই framework গুলো দ্বারা ভালভাবে পরিবেশিত না হয় এবং আপনি আপনার নিজস্ব কাস্টম সেটআপ রোল করতে চান, আমরা আপনাকে থামাব না--এটির জন্য npm থেকে `react` এবং `react-dom` ইন্সটল করুন, [Vite](https://vitejs.dev/) or [Parcel](https://parceljs.org/) এর মতো একটি বান্ডলার দিয়ে আপনার কাস্টম বিল্ড প্রক্রিয়া সেট আপ করুন এবং রাউটিং, স্ট্যাটিক জেনারেশন বা সার্ভার-সাইড রেন্ডারিং এবং আরও অনেক কিছুর জন্য আপনার প্রয়োজন অনুযায়ী অন্যান্য টুল যোগ করুন।
</DeepDive>

## জনপ্রিয় কিছু React ফ্রেমওয়ার্ক {/*bleeding-edge-react-frameworks*/}
=======
## Bleeding-edge React frameworks {/*bleeding-edge-react-frameworks*/}
>>>>>>> 2372ecf920ac4cda7c900f9ac7f9c0cd4284f281

আমরা কীভাবে React এর উন্নতি চালিয়ে যেতে পারি তার খোঁজ করেছি , আমরা বুঝতে পেরেছি যে ফ্রেমওয়ার্কগুলির সাথে React কে আরও ঘনিষ্ঠভাবে একীভূত করা (বিশেষত, রাউটিং, বান্ডলিং এবং সার্ভার প্রযুক্তির সাথে) React ব্যবহারকারীদের আরও ভাল অ্যাপ তৈরি করতে সহায়তা করার সবচেয়ে বড় সুযোগ। Next.js টিম [React সার্ভার কম্পোনেন্টের](/blog/2023/03/22/react-labs-what-we-have-been-working-on-march-2023#react-server-components) মতো ফিচারের গবেষণা, বিকাশ, একীভূতকরণ এবং পরীক্ষা করতে আমাদের সাথে সহযোগিতা করতে সম্মত হয়েছে।

এই বৈশিষ্ট্যগুলি প্রতিদিন প্রস্তুত হওয়ার কাছাকাছি আসছে, এবং আমরা সেগুলিকে একীভূত করার বিষয়ে অন্যান্য বান্ডলার এবং ফ্রেমওয়ার্ক বিকাশকারীদের সাথে কথা বলেছি। আমাদের আশা যে এক বা দুই বছরের মধ্যে, এই পৃষ্ঠায় তালিকাভুক্ত সমস্ত কাঠামো এই বৈশিষ্ট্যগুলির জন্য সম্পূর্ণ সমর্থন পাবে৷ (আপনি যদি এই বৈশিষ্ট্যগুলির সাথে পরীক্ষা করার জন্য আমাদের সাথে অংশীদারি করতে আগ্রহী একজন ফ্রেমওয়ার্ক লেখক হন তবে দয়া করে আমাদের জানান!)

### Next.js (অ্যাপ রাউটার) {/*nextjs-app-router*/}

**[Next.js's অ্যাপ রাউটার](https://beta.nextjs.org/docs/getting-started) এটি Next.js API-এর একটি পুনঃডিজাইন যা React টিমের ফুল-স্ট্যাক আর্কিটেকচার ভিশন পূরণ করার লক্ষ্যে।** এটি আপনাকে asynchronous কম্পোনেন্ট গুলিতে ডেটা আনতে দেয় যা সার্ভারে বা এমনকি বিল্ড চলাকালীনও চলে।

[Vercel](https://vercel.com/), Next.js এর রক্ষণাবেক্ষণের কাজ করে থাকে। যেকোনো Node.js অথবা serverless হোস্টিং, অথবা আপনার নিজস্ব সার্ভারে [একটি Next.js অ্যাপ deploy](https://nextjs.org/docs/deployment) করতে পারবেন। Next.js [স্ট্যাটিক এক্সপোর্ট](https://nextjs.org/docs/app/building-your-application/deploying/static-exports) সমর্থন করে যার জন্য সার্ভারের প্রয়োজন হয় না।

<DeepDive>

#### কোন বৈশিষ্ট্যগুলি React টিমের ফুল-স্ট্যাক আর্কিটেকচার ভিশন তৈরি করে? {/*which-features-make-up-the-react-teams-full-stack-architecture-vision*/}

Next.js অ্যাপ রাউটার বান্ডলার অফিসিয়াল [React সার্ভার কম্পোনেন্ট](https://github.com/reactjs/rfcs/blob/main/text/0188-server-components.md) স্পেসিফিকেশন সম্পূর্ণরূপে প্রয়োগ করে। এটি আপনাকে একটি একক React বিল্ড-টাইম, শুধুমাত্র সার্ভার এবং ইন্টারেক্টিভ কম্পোনেন্ট গুলকে মিশ্রিত করতে দেয়।

উদাহরণস্বরূপ, আপনি একটি সার্ভার-অনলি React কম্পোনেন্টকে একটি `async` ফাংশন হিসেবে লিখতে পারেন যা কোনো ডাটাবেস বা কোনো ফাইল থেকে পড়ে। তারপরে আপনি এটি থেকে আপনার ইন্টারেক্টিভ উপাদানগুলিতে ডেটা প্রেরণ করতে পারেনঃ

```js
// This component runs *only* on the server (or during the build).
async function Talks({ confId }) {
  // 1. You're on the server, so you can talk to your data layer. API endpoint not required.
  const talks = await db.Talks.findAll({ confId });

  // 2. Add any amount of rendering logic. It won't make your JavaScript bundle larger.
  const videos = talks.map(talk => talk.video);

  // 3. Pass the data down to the components that will run in the browser.
  return <SearchableVideoList videos={videos} />;
}
```

Next.js's অ্যাপ রাউটার [Suspense এর  মাধ্যমে ডেটা Fetch করার প্রক্রিয়া](/blog/2022/03/29/react-v18#suspense-in-data-frameworks) দিয়ে দেয়। এটি আপনার অ্যাপ এর বিভিন্ন UI অংশের লোডিং state(যেমন একটি স্কেলেটন প্লেসহোল্ডার) তৈরি করতে সাহায্য করেঃ

```js
<Suspense fallback={<TalksLoading />}>
  <Talks confId={conf.id} />
</Suspense>
```

সার্ভার কম্পোনেন্ট এবং Suspense হল React এর ফিচার এদের Next.js এর সাথে কোন সম্পর্ক নেই। যাইহোক, ফ্রেমওয়ার্ক এর সাথে এগুলো ব্যাবহার করতে হলে যতটুকু কাজ করতে তা খুবই কম এবং নগণ্য এই মুহূর্তে, Next.js অ্যাপ রাউটার হল সবচেয়ে সম্পূর্ণ বাস্তবায়ন। রিঅ্যাক্ট টিম বান্ডলার ডেভেলপারদের সাথে কাজ করছে যাতে এই বৈশিষ্ট্যগুলিকে পরবর্তী ভার্সনের ফ্রেমওয়ার্কগুলিতে প্রয়োগ করা সহজ হয়।

</DeepDive>
