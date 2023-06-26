---
title: "<option>"
---

<Intro>

[বিল্ট-ইন ব্রাউজার `<option>` কম্পোনেন্ট](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option) আপনাকে একটি [`<select>`](/reference/react-dom/components/select) বক্সের মধ্যে একটি অপশন তৈরি করার সুযোগ দেয়।

```js
<select>
  <option value="someOption">Some option</option>
  <option value="otherOption">Other option</option>
</select>
```

</Intro>

<InlineToc />

---

## রেফারেন্স {/*reference*/}

### `<option>` {/*option*/}

[বিল্ট-ইন ব্রাউজার `<option>` কম্পোনেন্ট](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option) আপনাকে একটি [`<select>`](/reference/react-dom/components/select) বক্সের মধ্যে একটি অপশন তৈরি করার সুযোগ দেয়।

```js
<select>
  <option value="someOption">Some option</option>
  <option value="otherOption">Other option</option>
</select>
```

[নিচে আরো উদাহরণ দেখুন।](#usage)

#### প্রপ {/*props*/}

`<option>` সকল [কমন এলিমেন্ট প্রপের](/reference/react-dom/components/common#props) সাপোর্ট দেয়।

সাথে সাথে, `<option>` এই প্রপগুলোর সাপোর্ট দেয়ঃ

* [`disabled`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option#disabled): একটি বুলিয়ান. `true` হলে, অপশন সিলেক্ট করা যাবে না এবং dimmed দেখাবে এটাকে।
* [`label`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option#label): একটি স্ট্রিং। অপশনটা কী বুঝাচ্ছে সেটা নির্দেশ করে। যদি নির্দিষ্ট না করা হয়, তবে অপশনের মধ্যকার টেক্সট ব্যবহৃত হয়।
* [`value`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option#value): [যখন একটা ফর্মে প্যারেন্ট `<select>` সাবমিট করা হয়](/reference/react-dom/components/select#reading-the-select-box-value-when-submitting-a-form) তখন এই অপশনটা সিলেক্ট করা হলে যেই ভ্যালু ব্যবহৃত হবে।

#### সতর্কতা {/*caveats*/}

* React  `<option>` এ `selected` এট্রিবিউট সাপোর্ট করে না। বরং, একটি অনিয়ন্ত্রিত সিলেক্ট বক্সের জন্য এই অপশনের `value` প্যারেন্ট [`<select defaultValue>`](/reference/react-dom/components/select#providing-an-initially-selected-option) এ পাস করে দিন, অথবা একটি নিয়ন্ত্রিত সিলেক্ট এর জন্য [`<select value>`](/reference/react-dom/components/select#controlling-a-select-box-with-a-state-variable) এ পাস করুন।

---

## ব্যবহার {/*usage*/}

### অপশন সহ একটি সিলেক্ট বক্স প্রদর্শন করা {/*displaying-a-select-box-with-options*/}

Render a `<select>` with a list of `<option>` components inside to display a select box. Give each `<option>` a `value` representing the data to be submitted with the form.
একটি সিলেক্ট বক্স প্রদর্শনের জন্য একটি `<option>` এর লিস্ট সহ `<select>` রেন্ডার করুন। প্রতিটা `<option>` এ একটি `value` দিন যারা ফর্মে সাবমিট হতে চলা ডেটার প্রতিনিধিত্ব করে।

[`<option>` এর তালিকা সহ `<select>` প্রদর্শন করা নিয়ে আরো পড়ুন।](/reference/react-dom/components/select)

<Sandpack>

```js
export default function FruitPicker() {
  return (
    <label>
      Pick a fruit:
      <select name="selectedFruit">
        <option value="apple">Apple</option>
        <option value="banana">Banana</option>
        <option value="orange">Orange</option>
      </select>
    </label>
  );
}
```

```css
select { margin: 5px; }
```

</Sandpack>  

