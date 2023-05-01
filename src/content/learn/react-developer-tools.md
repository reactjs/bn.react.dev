---
title: React ডেভেলপার টুলস
---

<Intro>

 React এর [components](/learn/your-first-component), edit [props](/learn/passing-props-to-a-component) এবং [state](/learn/state-a-components-memory), এবং পারফরমেন্স সমস্যা শনাক্ত করার জন্য ব্যবহার করুন React ডেভেলপার টুলস।

</Intro>

<YouWillLearn>

* কিভাবে React ডেভেলপার টুলস ইন্সটল করতে হয়

</YouWillLearn>

## ব্রাউজার এক্সটেনশন {/*browser-extension*/}

React দিয়ে তৈরি ওয়েবসাইট ডিবাগ করার সবচেয়ে সহজ উপায় হল React Developer Tools ব্রাউজার এক্সটেনশন ইনস্টল করা। এই ব্রাউজার এক্সটেনশনটি কয়েকটি জনপ্রিয় ব্রাউজারে পাওয়া যাচ্ছে।

* [**গুগল ক্রোম** এর জন্য ইন্সটল করুন](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)
* [**মজিলা ফায়ারফক্স** এর জন্য ইন্সটল করুন](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/)
* [**মাইক্রোসফট এজ** এর জন্য ইন্সটল করুন](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)

এখন, আপনি যদি **React দিয়ে তৈরি** ওয়েবসাইটে যান তাহলে, আপনি _Components_ এবং _Profiler_ প্যানেল ব্রাউজার ডেভেলপার টুলস এ দেখতে পাবেন।

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

## Mobile (React Native) {/*mobile-react-native*/}
React Developer Tools can be used to inspect apps built with [React Native](https://reactnative.dev/) as well.

The easiest way to use React Developer Tools is to install it globally:
```bash
# Yarn
yarn global add react-devtools

# Npm
npm install -g react-devtools
```

Next open the developer tools from the terminal.
```bash
react-devtools
```

It should connect to any local React Native app that's running.

> Try reloading the app if developer tools doesn't connect after a few seconds.

[Learn more about debugging React Native.](https://reactnative.dev/docs/debugging)
