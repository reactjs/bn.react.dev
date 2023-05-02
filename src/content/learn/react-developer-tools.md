---
title: React ডেভেলপার টুলস
---

<Intro>

 React এর [components](/learn/your-first-component) ইন্সপেক্ট, [props](/learn/passing-props-to-a-component) এবং [state](/learn/state-a-components-memory) এডিট, এবং পারফরমেন্স সমস্যা শনাক্ত করার জন্য React ডেভেলপার টুলস ব্যবহার করুন।

</Intro>

<YouWillLearn>

* কিভাবে React ডেভেলপার টুলস ইন্সটল করতে হয়

</YouWillLearn>

## ব্রাউজার এক্সটেনশন {/*browser-extension*/}

React দিয়ে তৈরি ওয়েবসাইট ডিবাগ করার সবচেয়ে সহজ উপায় হল React ডেভেলপার টুলস ব্রাউজার এক্সটেনশন ইনস্টল করা। এই ব্রাউজার এক্সটেনশনটি কয়েকটি জনপ্রিয় ব্রাউজারে পাওয়া যাচ্ছে।

* [**গুগল ক্রোম** এর জন্য ইন্সটল করুন](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)
* [**মজিলা ফায়ারফক্স** এর জন্য ইন্সটল করুন](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/)
* [**মাইক্রোসফট এজ** এর জন্য ইন্সটল করুন](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)

এখন, আপনি যদি **React দিয়ে তৈরি** একটি ওয়েবসাইটে যান তাহলে, আপনি _Components_ এবং _Profiler_ প্যানেল ব্রাউজার ডেভেলপার টুলস এ দেখতে পাবেন।

![React Developer Tools extension](/images/docs/react-devtools-extension.png)

### সাফারি এবং অন্যান্য ব্রাউজার {/*safari-and-other-browsers*/}
অন্যান্য ব্রাউজার (যেমনঃ সাফারি) এর জন্য ইন্সটল করুন [`react-devtools`](https://www.npmjs.com/package/react-devtools) npm প্যাকেজঃ
```bash
# Yarn
yarn global add react-devtools

# Npm
npm install -g react-devtools
```

তারপর Terminal থেকে ডেভেলপার টুলসটি ওপেন করুনঃ
```bash
react-devtools
```

তারপর আপনার ওয়েবসাইটের সাথে সংযোগ স্থাপনের জন্য নিচের `<script>` ট্যাগ টি আপনার ওয়েবসাইটের শুরুতে `<head>` ট্যাগ এর ভিতরে যুক্ত করুনঃ
```html {3}
<html>
  <head>
    <script src="http://localhost:8097"></script>
```

এখন React ডেভেলপার টুলসটি, ব্রাউজার ডেভেলপার টুলস এ দেখার জন্য ব্রাউজারে আপনার ওয়েবসাইটি Reload করুন।

![React Developer Tools standalone](/images/docs/react-devtools-standalone.png)

## মোবাইল (React Native) {/*mobile-react-native*/}
[React Native](https://reactnative.dev/) দিয়ে তৈরি অ্যাপস গুলো inspect করার জন্যেও আপনি React ডেভেলপার টুলস ব্যবহার করতে পারেন।

সবচেয়ে সহজ উপায় হলো React ডেভেলপার টুলস টি আপনার সিস্টেমে globally ইন্সটল করাঃ
```bash
# Yarn
yarn global add react-devtools

# Npm
npm install -g react-devtools
```

তারপর Terminal থেকে ডেভেলপার টুলসটি ওপেন করুনঃ 
```bash
react-devtools
```

তারপর এটি আপনার যেকোনো চলমান local React Native অ্যাপ এর সাথে কাজ করবে। 

> যদি ডেভেলপার টুলসটি আপনার অ্যাপ এর সাথে কয়েক সেকেন্ডের মধ্যে কানেক্ট না হয় তাহলে আপনার অ্যাপটি Reload করুন।

[React Native দিয়ে তৈরি অ্যাপ এর ডিবাগিং করা সম্পর্কে আরো জানুন।](https://reactnative.dev/docs/debugging)
