---
title: "React DOM Components"
---

<Intro>

React সকল প্রকার ব্রাউজার বিল্ট-ইন [HTML](https://developer.mozilla.org/en-US/docs/Web/HTML/Element) এবং [SVG](https://developer.mozilla.org/en-US/docs/Web/SVG/Element) কম্পোনেন্ট সাপোর্ট করে।

</Intro>

---

## সাধারণ কম্পোনেন্ট {/*common-components*/}

সকল বিল্ট-ইন ব্রাউজার কম্পোনেন্ট কিছু প্রপ এবং ইভেন্ট সাপোর্ট করে।

* [সাধারণ component (e.g. `<div>`)](/reference/react-dom/components/common)

এর মধ্যে রয়েছে React-specific প্রপ যেমন `ref` এবং `dangerouslySetInnerHTML`।

---

## ফর্ম কম্পোনেন্ট {/*form-components*/}

এই বিল্ট-ইন ব্রাউজার কম্পোনেন্টগুলো ইউজার ইনপুট গ্রহণ করে।

* [`<input>`](/reference/react-dom/components/input)
* [`<select>`](/reference/react-dom/components/select)
* [`<textarea>`](/reference/react-dom/components/textarea)

তারা React এর মধ্যে বিশেষ কারণ তাদের কাছে `value` প্রপ পাঠানোর বিষয়টা তাদেরকে *[নিয়ন্ত্রিত](/reference/react-dom/components/input#controlling-an-input-with-a-state-variable)* বানায়।

---

## Resource and Metadata Components {/*resource-and-metadata-components*/}

<<<<<<< HEAD
এই বিল্ট-ইন ব্রাউজার কম্পোনেন্টগুলি আপনাকে বাহ্যিক রিসোর্স লোড করতে অথবা ডকুমেন্টে মেটাডেটা দিয়ে মার্ক করতে দেয়:
=======
These built-in browser components let you load external resources or annotate the document with metadata:
>>>>>>> 2859efa07357dfc2927517ce9765515acf903c7c

* [`<link>`](/reference/react-dom/components/link)
* [`<meta>`](/reference/react-dom/components/meta)
* [`<script>`](/reference/react-dom/components/script)
* [`<style>`](/reference/react-dom/components/style)
* [`<title>`](/reference/react-dom/components/title)

React-এ এগুলি বিশেষ কারণ React এগুলিকে ডকুমেন্টের হেডে রেন্ডার করতে পারে, রিসোর্স লোড হওয়া অবধি সাসপেন্ড করতে পারে, এবং প্রতিটি বিশেষ কম্পোনেন্টের জন্য রেফারেন্স পেজে বর্ণিত অন্যান্য আচরণ সম্পাদন করতে পারে।

---

## সকল HTML কম্পোনেন্ট {/*all-html-components*/}

React সকল বিল্ট-ইন ব্রাউজার HTML কম্পোনেন্ট সাপোর্ট করে। এর মধ্যে রয়েছেঃ

* [`<aside>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/aside)
* [`<audio>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/audio)
* [`<b>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/b)
* [`<base>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base)
* [`<bdi>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/bdi)
* [`<bdo>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/bdo)
* [`<blockquote>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/blockquote)
* [`<body>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/body)
* [`<br>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/br)
* [`<button>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button)
* [`<canvas>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas)
* [`<caption>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/caption)
* [`<cite>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/cite)
* [`<code>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/code)
* [`<col>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/col)
* [`<colgroup>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/colgroup)
* [`<data>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/data)
* [`<datalist>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/datalist)
* [`<dd>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dd)
* [`<del>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/del)
* [`<details>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details)
* [`<dfn>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dfn)
* [`<dialog>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog)
* [`<div>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/div)
* [`<dl>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dl)
* [`<dt>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dt)
* [`<em>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/em)
* [`<embed>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/embed)
* [`<fieldset>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/fieldset)
* [`<figcaption>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/figcaption)
* [`<figure>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/figure)
* [`<footer>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/footer)
* [`<form>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form)
* [`<h1>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/h1)
* [`<head>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/head)
* [`<header>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/header)
* [`<hgroup>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/hgroup)
* [`<hr>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/hr)
* [`<html>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/html)
* [`<i>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/i)
* [`<iframe>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe)
* [`<img>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img)
* [`<input>`](/reference/react-dom/components/input)
* [`<ins>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/ins)
* [`<kbd>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/kbd)
* [`<label>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label)
* [`<legend>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/legend)
* [`<li>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/li)
* [`<link>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link)
* [`<main>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/main)
* [`<map>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/map)
* [`<mark>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/mark)
* [`<menu>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/menu)
* [`<meta>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta)
* [`<meter>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meter)
* [`<nav>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/nav)
* [`<noscript>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/noscript)
* [`<object>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/object)
* [`<ol>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/ol)
* [`<optgroup>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/optgroup)
* [`<option>`](/reference/react-dom/components/option)
* [`<output>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/output)
* [`<p>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/p)
* [`<picture>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture)
* [`<pre>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/pre)
* [`<progress>`](/reference/react-dom/components/progress)
* [`<q>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/q)
* [`<rp>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/rp)
* [`<rt>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/rt)
* [`<ruby>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/ruby)
* [`<s>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/s)
* [`<samp>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/samp)
* [`<script>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script)
* [`<section>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/section)
* [`<select>`](/reference/react-dom/components/select)
* [`<slot>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot)
* [`<small>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/small)
* [`<source>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/source)
* [`<span>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/span)
* [`<strong>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/strong)
* [`<style>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/style)
* [`<sub>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/sub)
* [`<summary>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/summary)
* [`<sup>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/sup)
* [`<table>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/table)
* [`<tbody>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/tbody)
* [`<td>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/td)
* [`<template>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template)
* [`<textarea>`](/reference/react-dom/components/textarea)
* [`<tfoot>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/tfoot)
* [`<th>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/th)
* [`<thead>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/thead)
* [`<time>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/time)
* [`<title>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/title)
* [`<tr>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/tr)
* [`<track>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/track)
* [`<u>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/u)
* [`<ul>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/ul)
* [`<var>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/var)
* [`<video>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/video)
* [`<wbr>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/wbr)

<Note>

[DOM স্ট্যান্ডার্ড](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model) এর মতোই React প্রপের নামের জন্য `camelCase` রীতি অনুসরণ করে। উদাহরণস্বরূপ, আপনি `tabindex` এর জায়গায় লিখবেন `tabIndex`। আপনি [online converter](https://transform.tools/html-to-jsx) ব্যবহার করে বিদ্যমান HTML কে JSX এ রূপান্তরিত করতে পারবেন।

</Note>

---

### কাস্টম HTML এলিমেন্ট {/*custom-html-elements*/}

আপনি যদি dash আছে এমন একটি ট্যাগ রেন্ডার করেন, like `<my-element>`, React ধরে নেবে যে আপনি একটি [কাস্টম HTML এলিমেন্ট](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements) রেন্ডার করতে চান।  React এর ক্ষেত্রে, কাস্টম এলিমেন্ট রেন্ডার করা এবং বিল্ট-ইন ব্রাউজার ট্যাগ রেন্ডার করা ভিন্ন ভাবে কাজ করে।

- সকল কাস্টম এলিমেন্ট প্রপ স্ট্রিং এ সিরিয়ালাইজ করা হয় এবং সব সময় এট্রিবিউট ব্যবহার করে সেট করা হয়।
- কাস্টম এলিমেন্ট এর জায়গায় `class` গ্রহণ করে, এবং `htmlFor` এর জায়গায় `for`।

আপনি যদি [`is`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/is) এট্রিবিউট সহ একটি ব্রাউজার বিল্ট-ইন HTML এলিমেন্ট রেন্ডার করেন, তবে এটিকে একটি কাস্টম এলিমেন্ট হিসাবে গণ্য করা হবে।

<Note>

[ভবিষ্যতে React এর একটি ভার্শনে কাস্টম এলিমেন্টের জন্য আরো বিস্তারিত সাপোর্ট থাকবে।](https://github.com/facebook/react/issues/11347#issuecomment-1122275286)

React প্যাকেজগুলো সর্বশেষ পরীক্ষামূলক ভার্শনে আপগ্রেড করার মাধ্যমে আপনি এগুলো ব্যবহার করে দেখতে পারেনঃ

- `react@experimental`
- `react-dom@experimental`

React  এর পরীক্ষামূলক ভার্শনগুলোতে বাগ থাকতে পারে। প্রোডাকশনে এই ভার্শঙ্গুলো ব্যবহার করবেন না।

</Note>
---

## সকল SVG কম্পোনেন্ট {/*all-svg-components*/}

React ব্রাউজারে থাকা সকল বিল্ট-ইন SVG কম্পোনেন্ট সাপোর্ট করে। এর মধ্যে রয়েছেঃ

* [`<a>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/a)
* [`<animate>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/animate)
* [`<animateMotion>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/animateMotion)
* [`<animateTransform>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/animateTransform)
* [`<circle>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/circle)
* [`<clipPath>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/clipPath)
* [`<defs>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/defs)
* [`<desc>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/desc)
* [`<discard>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/discard)
* [`<ellipse>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/ellipse)
* [`<feBlend>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feBlend)
* [`<feColorMatrix>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feColorMatrix)
* [`<feComponentTransfer>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feComponentTransfer)
* [`<feComposite>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feComposite)
* [`<feConvolveMatrix>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feConvolveMatrix)
* [`<feDiffuseLighting>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feDiffuseLighting)
* [`<feDisplacementMap>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feDisplacementMap)
* [`<feDistantLight>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feDistantLight)
* [`<feDropShadow>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feDropShadow)
* [`<feFlood>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feFlood)
* [`<feFuncA>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feFuncA)
* [`<feFuncB>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feFuncB)
* [`<feFuncG>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feFuncG)
* [`<feFuncR>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feFuncR)
* [`<feGaussianBlur>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feGaussianBlur)
* [`<feImage>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feImage)
* [`<feMerge>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feMerge)
* [`<feMergeNode>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feMergeNode)
* [`<feMorphology>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feMorphology)
* [`<feOffset>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feOffset)
* [`<fePointLight>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/fePointLight)
* [`<feSpecularLighting>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feSpecularLighting)
* [`<feSpotLight>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feSpotLight)
* [`<feTile>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feTile)
* [`<feTurbulence>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feTurbulence)
* [`<filter>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/filter)
* [`<foreignObject>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/foreignObject)
* [`<g>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/g)
* `<hatch>`
* `<hatchpath>`
* [`<image>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/image)
* [`<line>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/line)
* [`<linearGradient>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/linearGradient)
* [`<marker>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/marker)
* [`<mask>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/mask)
* [`<metadata>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/metadata)
* [`<mpath>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/mpath)
* [`<path>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/path)
* [`<pattern>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/pattern)
* [`<polygon>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/polygon)
* [`<polyline>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/polyline)
* [`<radialGradient>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/radialGradient)
* [`<rect>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/rect)
* [`<script>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/script)
* [`<set>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/set)
* [`<stop>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/stop)
* [`<style>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/style)
* [`<svg>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/svg)
* [`<switch>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/switch)
* [`<symbol>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/symbol)
* [`<text>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/text)
* [`<textPath>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/textPath)
* [`<title>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/title)
* [`<tspan>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/tspan)
* [`<use>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/use)
* [`<view>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/view)

<Note>

[DOM স্ট্যান্ডার্ড](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model) এর মতোই React প্রপের নামের জন্য `camelCase` রীতি অনুসরণ করে। উদাহরণস্বরূপ, আপনি `tabindex` এর জায়গায় লিখবেন `tabIndex`। আপনি [online converter](https://transform.tools/html-to-jsx) ব্যবহার করে বিদ্যমান SVG কে JSX এ রূপান্তরিত করতে পারবেন।

Namespaced অ্যাট্রিবিউটগুলো কোলন ছাড়া লিখতে হবেঃ

* `xlink:actuate` becomes `xlinkActuate`.
* `xlink:arcrole` becomes `xlinkArcrole`.
* `xlink:href` becomes `xlinkHref`.
* `xlink:role` becomes `xlinkRole`.
* `xlink:show` becomes `xlinkShow`.
* `xlink:title` becomes `xlinkTitle`.
* `xlink:type` becomes `xlinkType`.
* `xml:base` becomes `xmlBase`.
* `xml:lang` becomes `xmlLang`.
* `xml:space` becomes `xmlSpace`.
* `xmlns:xlink` becomes `xmlnsXlink`.

</Note>
