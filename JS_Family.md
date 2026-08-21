# JavaScript, ES6, TypeScript, CommonJS, JSX ও অন্যান্য — পার্থক্য ও ব্যাখ্যা

আধুনিক জাভাস্ক্রিপ্ট ইকোসিস্টেমে অনেকগুলো টার্ম ঘুরে বেড়ায় যেগুলো নতুনদের কাছে গুলিয়ে যায় — JS, ES6, TS, CJS, JSX, MJS/ESM ইত্যাদি। আসলে এগুলোর কিছু হলো **ভাষা** (language), কিছু হলো **ভাষার সংস্করণ** (version/standard), কিছু হলো **সিনট্যাক্স এক্সটেনশন**, আর কিছু হলো **মডিউল সিস্টেম**। নিচে প্রতিটি বিষয় আলাদা করে, উদাহরণসহ ব্যাখ্যা করা হলো।

---

## ১. JavaScript (JS) — মূল ভাষা

**JS** হলো মূল প্রোগ্রামিং ভাষা, যা ECMAScript স্ট্যান্ডার্ডের উপর ভিত্তি করে তৈরি। এটি ব্রাউজার ও Node.js উভয় জায়গায় চলে।

- **কেন দরকার:** ওয়েবপেজকে ইন্টারঅ্যাক্টিভ করা, সার্ভার-সাইড লজিক লেখা, ইত্যাদি।
- **কীভাবে কাজ করে:** ব্রাউজার বা Node.js-এর JS ইঞ্জিন (যেমন V8) কোড পড়ে ও এক্সিকিউট করে।

```js
// title: basic-javascript-example.js

// A simple JS function using var/function keyword (old-school style)
function greet(name) {
  // string concatenation using +
  return "Hello, " + name + "!";
}

console.log(greet("World"));
```

**সীমাবদ্ধতা:** পুরনো JS (ES5 এবং তার আগের) সিনট্যাক্সে `let`/`const`, arrow function, class, template literal ইত্যাদি ছিল না — যার ফলে কোড অনেক সময় ভারবোজ (verbose) ও কম রিডেবল হতো।

---

## ২. ES6 (ECMAScript 2015) — JS-এর আধুনিক সংস্করণ

**ES6** কোনো আলাদা ভাষা নয়, এটি **JavaScript-এরই একটি সংস্করণ/স্ট্যান্ডার্ড** (২০১৫ সালে রিলিজ হয়েছিল)। এরপর ES2016, ES2017... এভাবে প্রতি বছর নতুন ফিচার যুক্ত হয়েছে।

- **কেন দরকার:** কোড লেখা সহজ, পরিষ্কার ও কম বাগপ্রবণ করার জন্য।
- **কীভাবে কাজ করে:** নতুন সিনট্যাক্স (`let`, `const`, arrow function, class, destructuring, template literal, module `import`/`export`, Promise ইত্যাদি) যোগ করা হয়েছে, যা আধুনিক ব্রাউজার ও Node.js সরাসরি সাপোর্ট করে।

```js
// title: es6-features-example.js

// let/const instead of var (block-scoped)
const PI = 3.1416;
let counter = 0;

// Arrow function
const greet = (name) => `Hello, ${name}!`; // template literal

// Destructuring
const user = { name: "Rafi", age: 25 };
const { name, age } = user;

// Class syntax (syntactic sugar over prototypes)
class Person {
  constructor(name) {
    this.name = name;
  }
  sayHi() {
    return `Hi, I'm ${this.name}`;
  }
}

const p = new Person("Nabila");
console.log(p.sayHi());
console.log(greet(name));
```

**মূল কথা:** ES6 = JS + নতুন সিনট্যাক্স/ফিচার। ES6 লেখা মানে JS-ই লেখা, শুধু আধুনিক পদ্ধতিতে।

---

## ৩. TypeScript (TS) — টাইপযুক্ত JavaScript

**TS** হলো মাইক্রোসফটের তৈরি একটি ভাষা, যা JavaScript-এর **সুপারসেট** (superset)। অর্থাৎ যেকোনো বৈধ JS কোড, বৈধ TS কোডও বটে — কিন্তু TS-এ অতিরিক্ত ফিচার হিসেবে **স্ট্যাটিক টাইপিং** (static typing) যোগ করা হয়েছে।

- **কেন দরকার:** বড় প্রজেক্টে বাগ আগে থেকেই ধরা, কোড অটোকমপ্লিট ও ডকুমেন্টেশন ভালো হওয়া, রিফ্যাক্টরিং সহজ হওয়া।
- **কীভাবে কাজ করে:** TS কম্পাইলার (`tsc`) `.ts` ফাইলকে টাইপ-চেক করে সাধারণ `.js` ফাইলে **কম্পাইল/ট্রান্সপাইল** করে দেয়, কারণ ব্রাউজার বা Node.js সরাসরি TS বোঝে না।

```ts
// title: typescript-typing-example.ts

// Explicit type annotations catch bugs at compile time
interface UserProfile {
  name: string;
  age: number;
  isActive?: boolean; // optional property
}

function greet(user: UserProfile): string {
  return `Hello, ${user.name} (${user.age})`;
}

const user: UserProfile = { name: "Tanvir", age: 30 };
console.log(greet(user));

// This would cause a compile-time error if uncommented:
// greet({ name: "Tanvir", age: "thirty" }); // Error: age must be a number
```

**মূল কথা:** TS চালাতে হলে আগে কম্পাইল করে JS বানাতে হয় (`tsc file.ts` → `file.js`)। ব্রাউজার/Node.js শেষ পর্যন্ত সবসময় প্লেইন JS-ই রান করে।

---

## ৪. CommonJS (CJS) — মডিউল সিস্টেম (পুরনো/Node.js স্টাইল)

**CJS** কোনো ভাষা নয়, এটি একটি **মডিউল সিস্টেম** — অর্থাৎ কীভাবে একটি ফাইল থেকে আরেকটি ফাইলে কোড `import`/`export` করা হবে তার নিয়ম। এটি মূলত Node.js-এর ডিফল্ট মডিউল সিস্টেম ছিল (Node.js-এর শুরু থেকে)।

- **কেন দরকার:** কোডকে ফাইলে ফাইলে ভাগ করে পুনঃব্যবহারযোগ্য (reusable) করা।
- **কীভাবে কাজ করে:** `require()` দিয়ে ইম্পোর্ট এবং `module.exports` দিয়ে এক্সপোর্ট করা হয়। এটি **সিনক্রোনাসভাবে (synchronously)** লোড হয়।

```js
// title: commonjs-math-module.js (CJS export)

// Exporting functions using module.exports
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

module.exports = { add, subtract };
```

```js
// title: commonjs-main-file.js (CJS import)

// Importing using require()
const { add, subtract } = require("./commonjs-math-module.js");

console.log(add(5, 3));
console.log(subtract(5, 3));
```

**উল্লেখ্য:** CJS-এর বিকল্প হলো **ESM (ECMAScript Modules)**, যা `import`/`export` কীওয়ার্ড ব্যবহার করে (ES6-এ চালু হয়েছিল) এবং `.mjs` এক্সটেনশন বা `package.json`-এ `"type": "module"` সেট করলে ব্যবহার করা যায়।

```js
// title: esm-module-example.mjs (ESM export & import, modern standard)

// ESM export
export function multiply(a, b) {
  return a * b;
}

// In another file:
// import { multiply } from "./esm-module-example.mjs";
```

---

## ৫. JSX — JavaScript-এর ভেতরে HTML-এর মতো সিনট্যাক্স

**JSX (JavaScript XML)** হলো একটি **সিনট্যাক্স এক্সটেনশন**, মূলত **React**-এর সাথে ব্যবহৃত হয়। এটি নিজে কোনো ভাষা নয়, বরং JS ফাইলের মধ্যে HTML-এর মতো ট্যাগ লেখার সুবিধা দেয়।

- **কেন দরকার:** UI (User Interface) কম্পোনেন্ট লেখা সহজ ও ভিজ্যুয়ালি বোঝা সহজ করার জন্য।
- **কীভাবে কাজ করে:** JSX কোড ব্রাউজার সরাসরি বোঝে না। **Babel** (বা TS কম্পাইলার) JSX-কে সাধারণ `React.createElement()` কল-এ ট্রান্সপাইল করে দেয়।

```jsx
// title: jsx-react-component-example.jsx

import React from "react";

// JSX lets you write HTML-like syntax inside JS
function WelcomeCard({ name }) {
  return (
    <div className="card">
      <h1>Hello, {name}!</h1>
      <p>Welcome to the app.</p>
    </div>
  );
}

export default WelcomeCard;
```

```js
// title: jsx-compiled-output-example.js
// The above JSX compiles down to plain JS like this:

function WelcomeCard({ name }) {
  return React.createElement(
    "div",
    { className: "card" },
    React.createElement("h1", null, "Hello, ", name, "!"),
    React.createElement("p", null, "Welcome to the app.")
  );
}
```

**বোনাস:** `.tsx` হলো TypeScript + JSX-এর কম্বিনেশন — টাইপ-চেকিং সহ React কম্পোনেন্ট লেখার জন্য ব্যবহৃত হয়।

---

## ৬. দ্রুত তুলনা টেবিল

| নাম | ধরন | কী যোগ করে | কীভাবে রান হয় |
|---|---|---|---|
| **JS** | ভাষা | মূল ভাষা | সরাসরি ব্রাউজার/Node.js-এ |
| **ES6** | ভাষার সংস্করণ | নতুন সিনট্যাক্স (`let`, class, arrow fn) | আধুনিক ইঞ্জিন সরাসরি সাপোর্ট করে |
| **TS** | সুপারসেট ভাষা | স্ট্যাটিক টাইপিং | কম্পাইল হয়ে JS-এ পরিণত হয় |
| **CJS** | মডিউল সিস্টেম | `require`/`module.exports` | Node.js-এ সিনক্রোনাস লোডিং |
| **ESM/MJS** | মডিউল সিস্টেম | `import`/`export` | ব্রাউজার ও নতুন Node.js-এ নেটিভ সাপোর্ট |
| **JSX** | সিনট্যাক্স এক্সটেনশন | HTML-এর মতো UI সিনট্যাক্স | Babel/TS দিয়ে ট্রান্সপাইল হয়ে JS-এ পরিণত হয় |
| **TSX** | JSX + TS | টাইপ-চেক করা UI কম্পোনেন্ট | কম্পাইল হয়ে JS-এ পরিণত হয় |

---

## ৭. সব একসাথে কীভাবে কাজ করে?

একটি বাস্তব প্রজেক্টে (যেমন React + TypeScript) সাধারণত এই ফ্লো থাকে:

```
.tsx ফাইল (TS + JSX)
      │
      ▼  (TypeScript কম্পাইলার / Babel টাইপ-চেক ও ট্রান্সপাইল করে)
.js ফাইল (ES6+ সিনট্যাক্স সহ প্লেইন JS)
      │
      ▼  (Bundler যেমন Webpack/Vite প্রয়োজনে আরও পুরনো JS-এ কনভার্ট করে)
ব্রাউজার-কমপ্যাটিবল JS (ES5/ES6, ব্রাউজার সরাসরি রান করে)
```

অর্থাৎ শেষ পর্যন্ত সবকিছুই **সাধারণ JavaScript**-এ পরিণত হয় — TS, JSX এগুলো ডেভেলপমেন্টের সময় কোড লেখা সহজ ও নিরাপদ করার টুল মাত্র।

---

### সংক্ষেপে মনে রাখার সহজ উপায়

- **JS** = ভিত্তি ভাষা
- **ES6+** = JS-এর নতুন সংস্করণ/নিয়মকানুন
- **TS** = JS + টাইপ সেফটি (কম্পাইল প্রয়োজন)
- **CJS vs ESM** = মডিউল ইম্পোর্ট/এক্সপোর্ট করার দুইটি ভিন্ন পদ্ধতি (পুরনো vs নতুন)
- **JSX/TSX** = JS/TS-এর ভেতরে UI লেখার সিনট্যাক্স (মূলত React-এ ব্যবহৃত)
