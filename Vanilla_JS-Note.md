# 🌊 Core JavaScript Ocean Roadmap

## 1. Basic (Foundations)

### • Variables: var, let, const & Memory Allocation

- **1. What?**
  ভেরিয়েবল হলো ডেটা সংরক্ষণ করার পাত্র বা কন্টেইনার। জাভাস্ক্রিপ্টে ডেটা স্টোর করার জন্য তিনটি কিওয়ার্ড ব্যবহার করা হয়: var, let, এবং const। আর মেমরি অ্যালোকেশন (Memory Allocation) হলো, এই ডেটাগুলো কম্পিউটারের মেমরিতে (RAM) কীভাবে জায়গা দখল করে তার মেকানিজম।

- **2. Why?**
  আমাদের প্রজেক্টের বিভিন্ন ডাইনামিক ডেটা (যেমন: ইউজারের নাম, পাসওয়ার্ড, ইনভয়েসের টোটাল দাম) সাময়িকভাবে ধরে রাখার জন্য ভেরিয়েবল লাগে।
  আর তিনটি আলাদা ধরন কেন? কারণ বড় প্রজেক্টে ডেটা যেন ভুল করে ওভাররাইট বা পরিবর্তন না হয়ে যায় (যাতে Bug তৈরি না হয়), তা কন্ট্রোল করার জন্যই ES6-এ let এবং const আনা হয়েছে।

- **3. How?**
  var: এটি অনেক পুরনো নিয়ম। এটি ফাংশন স্কোপড (Function-scoped) এবং একে চাইলেই বারবার ডিক্লেয়ার ও আপডেট করা যায়, যা কোডে বিভ্রান্তি তৈরি করে।

let: এটি ব্লক স্কোপড (Block-scoped), অর্থাৎ { } এর বাইরে কাজ করে না। একে আপডেট করা যায়, কিন্তু একই স্কোপে দু'বার ডিক্লেয়ার করা যায় না।

const: এটিও ব্লক স্কোপড। তবে এর মান একবার সেট করলে আর পরিবর্তন (Re-assign) করা যায় না। ডিক্লেয়ার করার সময়ই ভ্যালু বলে দিতে হয়।

Memory Allocation: জাভাস্ক্রিপ্ট সাধারণ বা ছোট ডেটা (Primitive: Number, String) Stack Memory-তে সেভ রাখে। আর বড় বা জটিল ডেটা (Non-Primitive: Object, Array) Heap Memory-তে রাখে এবং স্ট্যাক মেমরিতে শুধু তার অ্যাড্রেস বা রেফারেন্স সেভ করে রাখে।

- **4. Code Example**

```JavaScript
// 1. var (The old way - try to avoid in modern projects)
var userName = "John Doe";
var userName = "Jane Doe"; // Re-declaration is allowed (can cause bugs)
userName = "Mark"; // Updation is allowed
console.log(userName); // Output: Mark

// 2. let (The modern way for changeable data)
let userAge = 25;
// let userAge = 26; // Error: Identifier 'userAge' has already been declared
userAge = 26; // Updation is allowed
console.log(userAge); // Output: 26

// 3. const (The modern way for fixed data)
const apiBaseUrl = "https://api.example.com";
// apiBaseUrl = "https://newapi.com"; // TypeError: Assignment to constant variable.

// 4. Block Scope Example (The magic of { })
{
  let blockScopedVar = "I live inside these brackets";
  const secretKey = "12345";
  var globalVar = "I can escape the block";
}
// console.log(blockScopedVar); // ReferenceError: blockScopedVar is not defined
// console.log(secretKey); // ReferenceError: secretKey is not defined
console.log(globalVar); // Output: I can escape the block

// 5. Memory Allocation Trick (const with Objects/Arrays in Heap memory)
const userProfile = { name: "Alex" };
// userProfile = { name: "Bob" }; // Error: Cannot reassign a new memory reference
userProfile.name = "Bob"; // Works! We are changing data inside the Heap memory
console.log(userProfile.name); // Output: Bob
```

- **5. Need to know**
  Golden Rule: প্রজেক্টে সবসময় ডিফল্টভাবে const ব্যবহার করবেন। যদি দেখেন ভ্যালু ভবিষ্যতে চেঞ্জ করার দরকার হচ্ছে (যেমন: লুপের কাউন্টার বা টোটাল বিল), শুধু তখনই let ব্যবহার করবেন। var ব্যবহার করা পুরোপুরি ছেড়ে দিন।

Temporal Dead Zone (TDZ): ইন্টারভিউতে এটি খুব জিজ্ঞাসা করে। let এবং const ডিক্লেয়ার করার আগে তাদের অ্যাক্সেস করতে চাইলে জাভাস্ক্রিপ্ট এরর দেয়। মেমরিতে জায়গা পাওয়ার পর থেকে ডিক্লেয়ার করার আগ পর্যন্ত এই সময়টাকেই TDZ বলে।

const Mutation: const দিয়ে অবজেক্ট বা অ্যারে বানালে পুরো অবজেক্টকে ওভাররাইট করা যায় না ঠিকই, কিন্তু এর ভেতরের প্রপার্টি (যেমন: অ্যারেতে নতুন ডেটা push করা) পরিবর্তন করা যায়। কারণ const শুধু স্ট্যাক মেমরির অ্যাড্রেসকে লক করে, হিপ মেমরির ভেতরের ডেটাকে নয়।

### • Scope & Lexical Environment

- **1. What?**
  Scope (স্কোপ) মানে হলো সীমানা। আপনার কোডের কোনো একটি ভেরিয়েবল বা ফাংশনকে কোডের ঠিক কোন কোন জায়গা থেকে অ্যাক্সেস করা যাবে (দেখা যাবে) আর কোথা থেকে যাবে না, সেই সীমানাই হলো স্কোপ।

Lexical Environment (লেক্সিকাল এনভায়রনমেন্ট) হলো একটি গিয়ার বা ইন্টারনাল মেকানিজম। যখনই জাভাস্ক্রিপ্ট কোনো ফাংশন রান করে, সে ব্যাকগ্রাউন্ডে একটি লোকাল মেমরি স্পেস তৈরি করে যেখানে ওই ফাংশনের নিজস্ব ডেটা থাকে এবং একই সাথে সে তার প্যারেন্ট (Parent) বা বাইরের আবরণের মেমরির একটা লিঙ্ক ধরে রাখে।

- **2. Why?**
  প্রজেক্টের সিকিউরিটি এবং ডেটা আইসোলেশনের জন্য এটি দরকার। ধরুন, আপনার ইনভয়েস অ্যাপের একটি ফাংশনের ভেতর ডিসকাউন্ট হিসাব হচ্ছে let discount = 10;। আপনি নিশ্চয়ই চাইবেন না কোডের অন্য কোনো সাধারণ ফাংশন এসে ভুল করে এই ডিসকাউন্ট ভ্যালু চেঞ্জ করে দিক। স্কোপিং কোডের এক অংশকে অন্য অংশ থেকে সুরক্ষিত রাখে।

- **3. How?**
  জাভাস্ক্রিপ্ট মূলত ৩ ধরনের স্কোপ মেইনটেইন করে:

Global Scope: কোডের একদম বাইরে ডিক্লেয়ার করা ভেরিয়েবল, যা যেকোনো জায়গা থেকে অ্যাক্সেস করা যায়।

Functional Scope: কোনো ফাংশনের ভেতরের এলাকা। এখানে var, let, const সবাই বন্দি থাকে।

Block Scope: শুধু { } সেকেন্ড ব্র্যাকেটের ভেতরের এলাকা (যেমন if, for)। এখানে শুধু let এবং const বন্দি থাকে, var দেয়াল ভেঙে বাইরে চলে যায়।

Scope Chain & Lexical Environment-এর কাজ:
যখন আপনি কোনো ফাংশনের ভেতর একটি ভেরিয়েবল খোঁজেন, জাভাস্ক্রিপ্ট প্রথমে তার নিজস্ব লোকাল লেক্সিকাল এনভায়রনমেন্টে খোঁজে। সেখানে না পেলে সে তার প্যারেন্টের (Parent) লেক্সিকাল এনভায়রনমেন্টে উঁকি মারে। এভাবে খুঁজতে খুঁজতে সে একদম গ্লোবাল স্কোপ পর্যন্ত চলে যায়। এই ক্রমান্বয়ে খুঁজে বেড়ানোর চেইনটাকেই বলে Scope Chain।

- **4. Code Example**

```JavaScript
// 1. Global Scope
const globalDevice = "Laptop";

function outerFunction() {
  // 2. Functional Scope (Parent Lexical Environment)
  const outerSecret = "Parent Data";

  function innerFunction() {
    // 3. Local Functional Scope (Child Lexical Environment)
    const innerSecret = "Child Data";

    // Scope Chain in action:
    console.log(innerSecret); // Found locally -> Output: Child Data
    console.log(outerSecret); // Not found locally -> Looks into parent -> Output: Parent Data
    console.log(globalDevice); // Not found in parent -> Looks into global -> Output: Laptop
  }

  innerFunction();
  // console.log(innerSecret); // ReferenceError: innerSecret is not defined (Cannot look down into child)
}

outerFunction();

// 4. Block Scope vs Var Escape
if (true) {
  let blockLet = "I am locked";
  const blockConst = "I am also locked";
  var leakedVar = "I escaped the block!";
}

// console.log(blockLet); // ReferenceError: blockLet is not defined
console.log(leakedVar); // Works! Output: I escaped the block!
```

- **5. Need to know**
  Lexical Meaning: "Lexical" শব্দের অর্থ হলো "কোডটি ফিজিক্যালি কোথায় লেখা হয়েছে"। জাভাস্ক্রিপ্ট কোনো ফাংশন রান করার সময় স্কোপ ঠিক করে না, বরং আপনি কোডটি খাতার কোন লাইনে লিখেছেন (তার প্যারেন্ট কে) তা দেখেই জন্মের সময় তার লেক্সিকাল এনভায়রনমেন্ট ফিক্সড হয়ে যায়।

Shadowing: যদি গ্লোবাল স্কোপে let x = 10; থাকে এবং কোনো ব্লকের ভেতরে আবার let x = 50; লেখেন, তবে ভেতরের x বাইরের x-কে আড়াল বা Shadow করে ফেলে। একেই বলে Variable Shadowing।

Interview Hook: ইন্টারভিউতে খুব কমন প্রশ্ন: "লেক্সিকাল এনভায়রনমেন্ট আসলে কী ধারণ করে?" উত্তর হবে: "It holds the local memory of the current scope AND a reference to its parent's lexical environment."

### • Data Types: Primitive vs Non-Primitive

- **1. What?**
  জাভাস্ক্রিপ্টে ডেটা টাইপস বলতে বোঝায় আমরা ভেরিয়েবলের মধ্যে ঠিক কী ধরনের ডেটা রাখছি। জাভাস্ক্রিপ্টের সমস্ত ডেটাকে মূলত দুটি ভাগে ভাগ করা যায়: Primitive (আদিম বা সরল ডেটা) এবং Non-Primitive (জটিল বা অবজেক্ট ডেটা)।

- **2. Why?**
  কম্পিউটার যেন বোঝে কোন ডেটার সাথে কেমন আচরণ করতে হবে, সেজন্য ডেটা টাইপ জানা জরুরি। উদাহরণস্বরূপ, দুটি সংখ্যার মধ্যে যোগ করা যায়, কিন্তু দুটি টেক্সট বা অবজেক্টের মধ্যে গাণিতিক যোগ করা সম্ভব নয়। এছাড়া বড় প্রজেক্টে মেমরি অপ্টিমাইজড রাখতে এবং বাগ (Bug) এড়াতে এই দুটির ভেতরের মেকানিজম জানা প্রয়োজন।

- **3. How?**
  Primitive (7টি টাইপ): Number, String, Boolean, Undefined, Null, Symbol, এবং BigInt।

এদের মান সরাসরি মেমরির Stack-এ সেভ হয়।

এরা হলো Immutable (অপরিবর্তনশীল)। অর্থাৎ, এদের মূল মান কখনো সরাসরি মডিফাই করা যায় না, কোনো পরিবর্তন করতে হলে নতুন মান দিয়ে রিপ্লেস করতে হয়।

Non-Primitive (প্রধানত Objects & Arrays):

এদের আসল বিশাল ডেটা সেভ হয় মেমরির Heap-এ, আর স্ট্যাকের মধ্যে শুধু ওই হিপ মেমরির ঠিকানা বা Reference (অ্যাড্রেস) থাকে।

এরা হলো Mutable (পরিবর্তনশীল)। অর্থাৎ, এদের ভেতরের ডেটা সরাসরি চেঞ্জ বা এডিট করা সম্ভব।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Primitive Types (Value Behavior)
// ==========================================

let originalScore = 100;
let copyScore = originalScore; // Passes the actual value

copyScore = 200; // Modifying the copy doesn't affect the original

console.log(originalScore); // Output: 100 (Safe and independent)
console.log(copyScore);     // Output: 200

// String immutability check
let greeting = "Hello";
greeting[0] = "Y"; // Trying to change 'H' to 'Y' directly
console.log(greeting); // Output: Hello (Primitive data cannot be mutated this way)


// ==========================================
// 2. Non-Primitive Types (Reference Behavior)
// ==========================================

let originalInvoice = { id: 101, total: 500 };
let sharedInvoice = originalInvoice; // Passes the memory reference/address

// Modifying the shared object updates the heap memory directly
sharedInvoice.total = 750;

console.log(originalInvoice.total); // Output: 750 (Original is changed!)
console.log(sharedInvoice.total);   // Output: 750

// Array behavior (Arrays are objects under the hood)
let fruits = ["Apple", "Banana"];
let updatedFruits = fruits;
updatedFruits.push("Mango");

console.log(fruits); // Output: ["Apple", "Banana", "Mango"]
```

- **5. Need to know**
  TypeOf Traps (ইন্টারভিউ স্পেশাল): জাভাস্ক্রিপ্টে typeof null চেক করলে আউটপুট আসে "object"। এটি জাভাস্ক্রিপ্টের প্রথম ভার্সনের একটি ঐতিহাসিক ভুল (Bug), যা ব্যাকওয়ার্ড কমপ্যাটিবিলিটির জন্য আর ফিক্স করা হয়নি।

Deep vs Shallow Copy: নন-প্রিমিটিভ ডেটাকে কপি করার সময় সাধারণ = ব্যবহার করলে শুধু রেফারেন্স বা লিংক কপি হয় (যা উপরে কোডে দেখছেন)। আসল ডেটা আলাদাভাবে ক্লোন করতে হলে মডার্ন স্প্রেড অপারেটর ({...original}) বা JSON.parse(JSON.stringify(obj)) ব্যবহার করতে হয়।

Value vs Reference: প্রিমিটিভ পাস হয় Pass by Value হিসেবে এবং নন-প্রিমিটিভ পাস হয় Pass by Reference হিসেবে।

### • Loops (for loop, while loop, do while loop, forEach loop)

- **1. What?**
  লুপ (Loop) মানে হলো চক্র। যখন কোডের কোনো একটি নির্দিষ্ট অংশ বা লজিক বারবার রান করার প্রয়োজন হয়, তখন ম্যানুয়ালি বারবার কোড না লিখে লুপ ব্যবহার করা হয়। জাভাস্ক্রিপ্টে বিভিন্ন প্রয়োজনের ওপর ভিত্তি করে কয়েক ধরনের লুপ রয়েছে, যেমন: for, while, do-while, এবং অ্যারের জন্য স্পেশাল বিল্ট-ইন মেথড forEach।

- **2. Why?**
  প্রজেক্টে একই কোড বারবার টাইপ করা এড়াতে (DRY - Don't Repeat Yourself নীতি বজায় রাখতে) লুপ ব্যবহার করা হয়। যেমন: ডাটাবেজ থেকে আসা ১০০টি প্রোডাক্টের লিস্ট ব্রাউজারে ফুটিয়ে তোলা, ১ থেকে ১০০০ পর্যন্ত সংখ্যার যোগফল বের করা, বা আপনার স্মার্ট ইনভয়েস সিস্টেমে সব আইটেমের দাম একটা একটা করে যোগ করে গ্র্যান্ড টোটাল বের করার কাজে লুপ অপরিহার্য।

- **3. How?**
  for loop: যখন আপনি আগে থেকেই জানেন যে লুপটি ঠিক কতবার ঘুরবে (Iteration সংখ্যা নির্দিষ্ট)। এর ৩টি পার্ট থাকে: ইনিশিয়েলাইজেশন, কন্ডিশন এবং ইনক্রিমেন্ট/ডিক্রিমেন্ট।

while loop: যখন লুপ কতবার ঘুরবে তা নির্দিষ্ট নয়, বরং একটি নির্দিষ্ট কন্ডিশন বা শর্ত সত্য (True) থাকা পর্যন্ত লুপটি চলতেই থাকবে।

do-while loop: এটি while লুপের মতোই, তবে পার্থক্য হলো কন্ডিশন সত্য হোক বা মিথ্যা, ভেতরের কোডটি অন্তত পক্ষে একবার রান করবেই। কারণ এর কন্ডিশন চেক হয় কোড এক্সিকিউট হওয়ার পরে।

forEach loop: এটি কোনো ট্র্যাডিশনাল লুপ নয়, এটি হলো অ্যারের একটি বিল্ট-ইন মেথড (Higher-Order Function)। এটি কোনো কন্ডিশন বা ইনক্রিমেন্টের ঝামেলা ছাড়াই একটি অ্যারের প্রতিটি উপাদানের (Element) জন্য একবার করে কাজ করে।

- **4. Code Example**

```JavaScript
// 1. Standard 'for' loop (When execution count is known)
console.log("--- Standard For Loop ---");
for (let i = 1; i <= 3; i++) {
  console.log("Iteration number: " + i);
}

// 2. 'while' loop (When count depends on a condition)
console.log("\n--- While Loop ---");
let currentBalance = 100;
const itemCost = 30;

while (currentBalance >= itemCost) {
  console.log("Bought an item! Current balance: " + currentBalance);
  currentBalance -= itemCost; // Decrementing condition variables to avoid infinite loop
}

// 3. 'do-while' loop (Runs at least once regardless of condition)
console.log("\n--- Do-While Loop ---");
let executionCount = 5;
do {
  console.log("This will print exactly once even if the condition is false.");
} while (executionCount < 2);

// 4. Array 'forEach' loop (The modern way to read array items)
console.log("\n--- Array forEach Loop ---");
const invoiceItems = [
  { product: "Laptop", price: 1200 },
  { product: "Mouse", price: 25 },
  { product: "Keyboard", price: 75 }
];

let grandTotal = 0;

// forEach passes (currentElement, currentIndex, wholeArray) inside callback
invoiceItems.forEach((item, index) => {
  console.log(`Item ${index + 1}: ${item.product} - $${item.price}`);
  grandTotal += item.price;
});

console.log("Grand Total Invoice Price: $" + grandTotal);
```

- **5. Need to know**
  Infinite Loop Danger: while বা for লুপের কন্ডিশন যদি কখনো মিথ্যা না হয় (যেমন ভুল করে ইনক্রিমেন্ট দিতে ভুলে গেলে), তবে লুপটি চিরকাল চলতে থাকবে। একে Infinite Loop বলে। এটি ব্রাউজার বা নোড জেএস সার্ভার ক্র্যাশ করার মূল কারণ।

The break & continue Keywords: লুপের ভেতর break ব্যবহার করলে লুপটি সাথে সাথে বন্ধ হয়ে যায়। আর continue ব্যবহার করলে লুপের ওই স্টেপের বাকি কোডটুকু স্কিপ করে পরের স্টেপে চলে যায়। (মনে রাখবেন, forEach লুপের ভেতরে কিন্তু break বা continue কাজ করে না!)

Performance Insight: বড় অ্যারের ক্ষেত্রে ট্র্যাডিশনাল for লুপ forEach এর চেয়ে সামান্য দ্রুত কাজ করে, তবে মডার্ন জাভাস্ক্রিপ্ট কোড ক্লিন ও রিডেবল রাখার জন্য forEach ব্যবহার করাই বেস্ট প্র্যাকটিস।

### • Type Coercion & Strict Equality (== vs ===)

- **1. What?**
  Type Coercion (টাইপ কোয়ার্সন) হলো জাভাস্ক্রিপ্টের একটি অটোমেটিক স্বভাব। যখন আপনি দুটি ভিন্ন ডেটা টাইপের (যেমন: একটি Number এবং একটি String) মধ্যে কোনো অপারেশন বা তুলনা করতে যান, জাভাস্ক্রিপ্ট নিজে থেকেই ব্যাকগ্রাউন্ডে জোর করে একটির টাইপ পরিবর্তন করে অন্যটির সমান করে নেয়।

Equality (সমতা) চেক করার জন্য জাভাস্ক্রিপ্টে দুটি অপারেটর আছে: == (Loose Equality) এবং === (Strict Equality)।

- **2. Why?**
  জাভাস্ক্রিপ্ট একটি ডাইনামিকালি-টাইপড ল্যাঙ্গুয়েজ, অর্থাৎ ভেরিয়েবলের টাইপ আগে থেকে ফিক্সড থাকে না। কোড লেখার সময় বা ইউজারের ইনপুট (যা সবসময় স্ট্রিং আকারে আসে) নিয়ে কাজ করার সময় জাভাস্ক্রিপ্টের এই টাইপ পরিবর্তনের স্বভাবটি জানা না থাকলে, ক্যালকুলেশনে বা কন্ডিশনে অদ্ভুত সব বাগ (Bug) তৈরি হবে যা খুঁজে পাওয়া অসম্ভব হয়ে দাঁড়াবে।

- **3. How?**
  Implicit Coercion (স্বয়ংক্রিয়): জাভাস্ক্রিপ্ট নিজে থেকে যখন টাইপ চেঞ্জ করে। যেমন: প্লাস অপারেটরের (+) একপাশে String থাকলে সে অন্যপাশের Number-কেও String বানিয়ে পাশাপাশি বসিয়ে দেয় (Concatenation)। কিন্তু বিয়োগ (-), গুণ (\*), বা ভাগের (/) সময় সে String-কে জোর করে Number বানিয়ে গাণিতিক কাজ সম্পন্ন করে।

Loose Equality (==): এটি শুধু দুটি ভ্যালুর মান সমান কি না তা চেক করে। যদি টাইপ আলাদা হয় (যেমন: ৫ এবং "৫"), সে ব্যাকগ্রাউন্ডে Type Coercion ঘটিয়ে আগে টাইপ এক করে, তারপর তুলনা করে। তাই 5 == "5" এর রেজাল্ট আসে true।

Strict Equality (===): এটি কোনো টাইপ কোয়ার্সন বা জোরজুলুম করে না। এটি একই সাথে মান (Value) এবং ডেটা টাইপ (Data Type) দুটিই নিখুঁতভাবে চেক করে। টাইপ আলাদা হলে সে সরাসরি false রিটার্ন করে। তাই 5 === "5" এর রেজাল্ট আসে false।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Type Coercion Under the Hood
// ==========================================

console.log("5" + 2);  // Output: "52" (Number 2 becomes a String)
console.log("5" - 2);  // Output: 3 (String "5" becomes a Number)
console.log("5" * "2"); // Output: 10 (Both strings become Numbers)
console.log(false == 0); // Output: true (false is coerced into Number 0)


// ==========================================
// 2. Loose Equality (==) vs Strict Equality (===)
// ==========================================

const stringPrice = "250";
const numberPrice = 250;

// Loose Equality: Coerces type, compares only value
if (stringPrice == numberPrice) {
  console.log("== says: They are equal!"); // This will run
}

// Strict Equality: No coercion, compares both value and type
if (stringPrice === numberPrice) {
  console.log("=== says: They are equal!");
} else {
  console.log("=== says: Not equal because types are different!"); // This will run
}


// ==========================================
// 3. Truthy vs Falsy Coercion in Conditions
// ==========================================

let userInput = ""; // Empty string is a 'Falsy' value

if (!userInput) {
  console.log("Input is empty or invalid."); // This runs because "" coerces to false
}
```

- **5. Need to know**
  Golden Rule: প্রজেক্টে চোখ বন্ধ করে সবসময় === (Strict Equality) ব্যবহার করবেন। == ব্যবহার করলে ব্যাকগ্রাউন্ডের অদ্ভুত টাইপ পরিবর্তনের কারণে সিকিউরিটি হোল বা লজিক্যাল বাগ তৈরি হতে পারে।

Falsy Values: জাভাস্ক্রিপ্টে মাত্র ৬টি ভ্যালু কন্ডিশনের ভেতর গেলে অটোমেটিক false হয়ে যায় (Coerced to false): false, 0, "" (খালি স্ট্রিং), null, undefined, এবং NaN (Not a Number)। এগুলো বাদে বাকি সব true (Truthy)।

The NaN Paradox (ইন্টারভিউ স্পেশাল): জাভাস্ক্রিপ্টে NaN এমন এক অদ্ভুত জিনিস যা নিজের সমানও নিজে নয়! অর্থাৎ NaN === NaN এর আউটপুট আসবে false। কোনো ভ্যালু আসলেই NaN কি না তা চেক করার জন্য isNaN() ফাংশন ব্যবহার করতে হয়।

### • Functions: Declaration, Expression & Arrow Functions

- **1. What?**
  ফাংশন হলো জাভাস্ক্রিপ্টের কোডের একটি ব্লক বা প্যাকেট, যা একটি নির্দিষ্ট কাজ বারবার করার জন্য তৈরি করা হয়। জাভাস্ক্রিপ্টে এই কোডের প্যাকেট তৈরি করার প্রধান ৩টি উপায় বা সিনট্যাক্স রয়েছে: Function Declaration (ঐতিহ্যগত ডিক্লেয়ারেশন), Function Expression (ভেরিয়েবলে ফাংশন রাখা), এবং Arrow Functions (আধুনিক ছোট সিনট্যাক্স)।

- **2. Why?**
  প্রজেক্টের একই লজিক যেন বারবার লিখতে না হয় এবং কোড যেন গোছানো থাকে, সেজন্য ফাংশন ব্যবহার করা হয়। তবে ৩টি আলাদা উপায় কেন? কারণ জাভাস্ক্রিপ্ট ইঞ্জিনের হোইস্টিং (Hoisting) মেকানিজম হ্যান্ডেল করা, কোড আরও রিডেবল (সহজে পাঠযোগ্য) করা এবং বিশেষ করে অবজেক্টের ভেতর this কিওয়ার্ডের জটিল আচরণ নিয়ন্ত্রণ করার জন্য এই আলাদা ধরনগুলো জানা অত্যন্ত জরুরি।

- **3. How?**
  Function Declaration: এটি হলো সবচেয়ে সাধারণ নিয়ম। function কিওয়ার্ড দিয়ে শুরু হয় এবং এর একটি নাম থাকে। এর সবচেয়ে বড় সুবিধা (বা অসুবিধা) হলো একে কোডের যেকোনো জায়গায়—এমনকি ডিক্লেয়ার করার আগের লাইনেও কল করা যায় (Hoisting-এর কারণে)।

Function Expression: এখানে একটি বেনামী ফাংশন (Anonymous Function) তৈরি করে সেটিকে একটি ভেরিয়েবলের (let বা const) ভেতর অ্যাসাইন বা স্টোর করা হয়। এটি হোইস্ট হয় না, তাই ডিক্লেয়ার করার আগে একে কল করলে এরর দেয়।

Arrow Functions (ES6): এটি ফাংশন লেখার একটি মডার্ন এবং ফাস্ট ট্র্যাকিং সিনট্যাক্স। এতে function কিওয়ার্ডটি লিখতে হয় না, বদলে একটি তীর চিহ্ন বা অ্যারো (=>) ব্যবহার করা হয়। এর নিজস্ব কোনো this কিওয়ার্ড নেই; এটি তার বাইরের স্কোপের this-কে ধার করে কাজ করে।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Function Declaration (Hoisted)
// ==========================================

// Calling before declaration works!
const receipt1 = calculateTax(1000);
console.log("Declaration Result: " + receipt1); // Output: 1150

function calculateTax(amount) {
  return amount + (amount * 0.15); // 15% Tax
}


// ==========================================
// 2. Function Expression (Not Hoisted)
// ==========================================

// console.log(formatCurrency(500)); // ReferenceError: Cannot access 'formatCurrency' before initialization

const formatCurrency = function(value) {
  return "$" + value.toFixed(2);
};

console.log("Expression Result: " + formatCurrency(500)); // Output: $500.00


// ==========================================
// 3. Arrow Functions (Shorthand & Lexical 'this')
// ==========================================

// Standard Arrow Function
const doubleAmount = (price) => {
  return price * 2;
};

// Implicit Return (If it's a single line, remove curly braces and 'return' keyword)
const multiply = (a, b) => a * b;

console.log("Arrow Multiplied: " + multiply(5, 6)); // Output: 30


// ==========================================
// 4. Arrow Function 'this' Behavior Trap
// ==========================================
const userSession = {
  username: "Sabbir",
  // Standard function: 'this' points to the owner object (userSession)
  greetStandard: function() {
    console.log("Standard context: Hello " + this.username);
  },
  // Arrow function: 'this' points to parent/global context (Window or Empty object)
  greetArrow: () => {
    console.log("Arrow context: Hello " + this.username);
  }
};

userSession.greetStandard(); // Output: Standard context: Hello Sabbir
userSession.greetArrow();    // Output: Arrow context: Hello undefined
```

- **5. Need to know**
  Implicit Return (অদৃশ্য রিটার্ন): অ্যারো ফাংশনে যদি মাত্র একটি লাইনের স্টেটমেন্ট থাকে, তবে সেকেন্ড ব্র্যাকেট {} এবং return কিওয়ার্ডটি তুলে দেওয়া যায় (যেমন উপরের multiply উদাহরণে)। কোডটি তখন এক লাইনে চমৎকার দেখায়।

The this Trap (ইন্টারভিউ স্পেশাল): ইন্টারভিউতে সবচেয়ে বেশি ট্র্যাপে ফেলা হয় অ্যারো ফাংশনের this নিয়ে। মনে রাখবেন, কোনো অবজেক্টের মেথড (Method) তৈরি করার সময় কখনো অ্যারো ফাংশন ব্যবহার করবেন না, কারণ এটি অবজেক্টের ভেতরের ডেটা খুঁজে পাবে না (undefined দেখাবে)।

Arguments Object: ট্র্যাডিশনাল ফাংশনের ভেতর পাস করা সব প্যারামিটার arguments নামক একটি বিশেষ অবজেক্টে জমা থাকে, যা অ্যারো ফাংশনে অনুপস্থিত। অ্যারো ফাংশনে সেই সুবিধা পেতে চাইলে রেস্ট প্যারামিটার (...args) ব্যবহার করতে হয়।

## 2. BOM & DOM (Browser Runtime Environment)

- **1. What?**
  BOM (Browser Object Model) এবং DOM (Document Object Model) হলো দুটি শক্তিশালী সেতু বা উইন্ডো, যার মাধ্যমে জাভাস্ক্রিপ্ট ব্রাউজারের সাথে সরাসরি যোগাযোগ করতে পারে। সহজ ভাষায়, একটি ওয়েবসাইট যখন ব্রাউজারে ওপেন হয়, তখন ব্রাউজারের নিজস্ব সিস্টেম (যেমন: স্ক্রিনের সাইজ, ইউআরএল লিঙ্ক, হিস্ট্রি) নিয়ন্ত্রণ করার মেকানিজম হলো BOM; আর পেজের ভেতরের মূল কন্টেন্ট বা ডিজাইন (যেমন: টেক্সট, বাটন, ইমেজ) ডাইনামিকালি চেঞ্জ করার মেকানিজম হলো DOM। এই পুরো সিস্টেমটি যে পরিবেশের ভেতর ঘটে, তাকেই বলে Browser Runtime Environment।

- **2. Why?**
  জাভাস্ক্রিপ্ট ল্যাঙ্গুয়েজটি নিজে কিন্তু ব্রাউজারের বাটন, টেক্সট বা ব্যাক-বাটনকে চেনে না। আপনি যদি চান আপনার ইউজার একটি বাটনে ক্লিক করলে ব্যাকগ্রাউন্ডের কালার লাল হয়ে যাক (DOM-এর কাজ), কিংবা ইউজার ভুল লিঙ্কে গেলে তাকে অন্য পেজে রিডাইরেক্ট করে দেওয়া হোক বা তার ব্রাউজারে ডেটা সেভ রাখা হোক (BOM-এর কাজ)—তবে এই দুটি মডেল ছাড়া তা অসম্ভব। যেকোনো রিয়াক্ট (React) বা মডার্ন ওয়েব প্রজেক্টের ইন্টারঅ্যাক্টিভিটি তৈরি করার জন্য এটি জানা আবশ্যিক।

- **3. How?**
  যখন ব্রাউজার একটি এইচটিএমএল (HTML) ফাইল লোড করে, সে ব্যাকগ্রাউন্ডে পুরো পেজের একটি গাছ বা ট্রি-স্ট্রাকচার (Tree Structure) তৈরি করে ফেলে, যেখানে প্রতিটি ট্যাগ একেকটি ডালপালা বা নোড (Node)। জাভাস্ক্রিপ্ট এই গাছের গোড়ায় বসে window এবং document নামক দুটি গ্লোবাল অবজেক্টের মাধ্যমে পুরো ব্রাউজার ও পেজের ওপর নজরদারি এবং নিয়ন্ত্রণ চালায়।

BOM-এর লিডার হলো window: এটি সরাসরি ব্রাউজারের উইন্ডোকে রিপ্রেজেন্ট করে।

DOM-এর লিডার হলো document: এটি window-এরই একটি অংশ, যা শুধুমাত্র এইচটিএমএল পেজের ভেতরের কন্টেন্টটুকুকে রিপ্রেজেন্ট করে।

### • DOM Tree Structure & Traversal Mechanics

- **1. What?**
  DOM Tree Structure হলো ব্রাউজারের তৈরি করা একটি অদৃশ্য পারিবারিক গাছ (Family Tree)। যখন একটি HTML পেজ ব্রাউজারে লোড হয়, তখন ব্রাউজার কোডের প্রতিটি ট্যাগ, টেক্সট এবং কমেন্টকে একেকটি অবজেক্ট বা Node-এ রূপান্তর করে একটি চেইন তৈরি করে।

DOM Traversal Mechanics হলো এই গাছের এক ডাল থেকে অন্য ডালে চলাচলের রাস্তা। অর্থাৎ, কোডের কোনো একটি নির্দিষ্ট উপাদানকে (Element) ধরে তার বাবা (Parent), ভাইবোন (Siblings) বা সন্তানদের (Children) খুঁজে বের করার মেকানিজম।

- **2. Why?**
  প্রজেক্টে কাজ করার সময় সবসময় সব এলিমেন্টের আইডি (id) বা ক্লাস (class) সরাসরি পাওয়া যায় না। ধরুন, ইউজার একটি টেবিলের নির্দিষ্ট লাইনের ডিলিট বাটনে ক্লিক করলেন; এখন আপনার দায়িত্ব হলো ওই বাটনের ঠিক উপরের প্যারেন্ট লাইনে (Table Row) গিয়ে পুরো লাইনটি রিমুভ করে দেওয়া। এই ধরনের ডাইনামিক এবং রিলেশনাল কাজ করার জন্য ট্রাভার্সাল বা ডালপালা বেয়ে চলাচলের লজিক জানা অত্যন্ত জরুরি।

- **3. How?**
  ব্রাউজার পুরো HTML-কে একটি অবজেক্ট চেইনে সাজায় যেখানে document হলো মূল কাণ্ড (Root)। এই গাছের নোডগুলোর মধ্যে রক্তের সম্পর্কের মতো রিলেশন থাকে:

Parent-Child: বাইরের ট্যাগটি হলো Parent, আর তার ভেতরের ট্যাগগুলো হলো Children।

Siblings: একই প্যারেন্টের অধীনে থাকা পাশাপাশি ট্যাগগুলো হলো একে অপরের ভাইবোন বা Siblings।

জাভাস্ক্রিপ্ট আমাদের দুইভাবে চলাচলের সুবিধা দেয়:

Node Traversal (সবকিছুর ওপর দিয়ে): এটি টেক্সট নোড, সাদা জায়গা (White-space) এবং কমেন্টকেও কাউন্ট করে। (যেমন: parentNode, childNodes, nextSibling)

Element-Only Traversal (শুধু ট্যাগের ওপর দিয়ে): এটি সাদা জায়গা বা কমেন্টকে ইগনোর করে শুধু আসল HTML ট্যাগগুলোর মধ্যে চলাচল করে। রিয়েল প্রজেক্টে এটিই সবচেয়ে বেশি ব্যবহৃত হয়। (যেমন: parentElement, children, nextElementSibling)

- **4. Code Example**

```HTML
<!-- Imagine this HTML Structure in the browser -->
<div id="invoice-card" class="card">
  <!-- Invoice Header -->
  <h2 class="title">Invoice #4402</h2>
  <ul id="item-list">
    <li>Laptop - $1200</li>
    <li class="active-item">Mouse - $25</li>
    <li>Keyboard - $75</li>
  </ul>
</div>
```

```JavaScript
// Selecting a starting node
const activeItem = document.querySelector('.active-item');

// ==========================================
// 1. Moving Upwards (Looking for Parents)
// ==========================================
const parentList = activeItem.parentElement;
console.log(parentList.id); // Output: "item-list"

// Going higher up the chain
const mainCard = activeItem.closest('.card'); // Finds the nearest matching ancestor
console.log(mainCard.id); // Output: "invoice-card"


// ==========================================
// 2. Moving Downwards (Looking for Children)
// ==========================================
const itemList = document.getElementById('item-list');
const totalChildren = itemList.children; // Returns an HTMLCollection of tags only
console.log(totalChildren.length); // Output: 3

const firstProduct = itemList.firstElementChild;
console.log(firstProduct.textContent); // Output: "Laptop - $1200"


// ==========================================
// 3. Moving Sideways (Looking for Siblings)
// ==========================================
const nextProduct = activeItem.nextElementSibling;
console.log(nextProduct.textContent); // Output: "Keyboard - $75"

const previousProduct = activeItem.previousElementSibling;
console.log(previousProduct.textContent); // Output: "Laptop - $1200"


// ==========================================
// 4. Trap: Node vs Element Check
// ==========================================
console.log(itemList.childNodes); // Includes text (line breaks/spaces) and comments
console.log(itemList.children);   // Includes HTML elements only (Pure List Items)
```

- **5. Need to know**
  The Element Only Rule: রিয়েল লাইফ ডেভেলপমেন্টে ভুলেও কখনো nextSibling বা firstChild ব্যবহার করবেন না। সবসময় nextElementSibling বা firstElementChild ব্যবহার করবেন। অন্যথায় কোডের মধ্যকার সাধারণ একটি এন্টার বা স্পেসকেও (White-space) জাভাস্ক্রিপ্ট নোড হিসেবে ধরে আপনার পুরো লজিক বিগড়ে দেবে।

closest() Method Magic: এটি একটি লাইফ-সেভার মেথড। কোনো চাইল্ড এলিমেন্ট থেকে লাফ দিয়ে তার ওপরের কোনো স্পেসিফিক ক্লাস বা আইডির প্যারেন্টকে এক লাইনে খুঁজে বের করার জন্য closest() এর কোনো জুড়ি নেই।

HTMLCollection Loop Trap: children প্রপার্টি আমাদের যা রিটার্ন করে তা দেখতে অ্যারের মতো হলেও সেটি আসলে একটি HTMLCollection (এটি কোনো পিওর অ্যারে নয়)। তাই এর ওপর সরাসরি .map() বা .filter() মেথড চালানো যায় না। লুপ চালাতে হলে প্রথমে তাকে Array.from(element.children) লিখে আসল অ্যারেতে কনভার্ট করে নিতে হয়।

### • Dynamic Manipulation: querySelector, createElement

- **1. What?**
  Dynamic Manipulation (ডাইনামিক ম্যানিপুলেশন) মানে হলো HTML ফাইলটি এডিট না করেই, ওয়েবসাইট চলাকালীন সময়ে জাভাস্ক্রিপ্ট দিয়ে পেজের যেকোনো অংশ বদলে ফেলা।

querySelector() হলো একটি হাই-টেক সার্চ ইঞ্জিন, যা দিয়ে সিএসএস (CSS) স্টাইলে পেজের যেকোনো এলিমেন্টকে খুঁজে ধরা যায়।

createElement() হলো একটি ফ্যাক্টরি মেথড, যা দিয়ে মেমরির ভেতর সম্পূর্ণ নতুন কোনো HTML ট্যাগ (যেমন: নতুন বাটন বা প্যারাগ্রাফ) তৈরি করা যায়।

- **2. Why?**
  ফেসবুক বা ইউটিউবের মতো মডার্ন অ্যাপগুলোতে আপনি যখন কোনো কমেন্ট করেন, পুরো পেজ কিন্তু রিলোড হয় না। আপনার কমেন্টটি সাথে সাথে নিচে যোগ হয়ে যায়। এই যে ব্যাকএন্ড বা ইউজার ইনপুট থেকে ডেটা নিয়ে পেজের ভেতরে নতুন কন্টেন্ট তৈরি করা, পুরনো কন্টেন্ট এডিট করা বা ডিলিট করা—এই রিয়েল-টাইম ইন্টারঅ্যাকশনের জন্যই ডাইনামিক ম্যানিপুলেশন দরকার।

- **3. How?**
  পুরো প্রসেসটি মূলত ৩টি স্টেপে কাজ করে:

Target (খুঁজে বের করা): document.querySelector() বা querySelectorAll() ব্যবহার করে CSS সিলেক্টরের (যেমন: .class বা #id) মাধ্যমে টার্গেট এলিমেন্টকে সিলেক্ট করা হয়।

Create & Style (তৈরি ও সাজানো): document.createElement('tag') দিয়ে এলিমেন্টটি মেমরিতে তৈরি করে তার ভেতর innerText বা classList.add() দিয়ে ডেটা ও সিএসএস বসানো হয়।

Inject (পেজে পুশ করা): মেমরিতে তৈরি হওয়া এই নতুন এলিমেন্টটি কিন্তু সরাসরি স্ক্রিনে দেখা যায় না। একে স্ক্রিনে দেখানোর জন্য appendChild() বা append() মেথড দিয়ে পেজের কোনো এক্সিস্টিং প্যারেন্ট এলিমেন্টের ভেতরে ইনজেক্ট বা ঢুকিয়ে দিতে হয়।

- **4. Code Example**

```HTML
<!-- Initial HTML in the browser -->
<div id="cart-container">
  <h3>Your Shopping Cart</h3>
  <ul id="cart-list">
    <!-- Dynamic items will go here -->
  </ul>
</div>
<button id="add-item-btn">Add Laptop to Cart</button>
```

```JavaScript
// ==========================================
// 1. Selecting Elements (Targeting)
// ==========================================
const cartList = document.querySelector("#cart-list"); // Target by ID
const addItemBtn = document.querySelector("#add-item-btn"); // Target button

// ==========================================
// 2. Creating and Injecting Elements Dynamically
// ==========================================
function addNewProduct(productName, productPrice) {
  // Step A: Create the element in memory
  const newLI = document.createElement("li");

  // Step B: Set content and styling safely
  newLI.className = "cart-item priority-item"; // Adding CSS classes
  newLI.innerText = `${productName} - $${productPrice}`;

  // Optional: Create a delete button inside the list item
  const deleteBtn = document.createElement("button");
  deleteBtn.innerText = "Remove";
  deleteBtn.style.marginLeft = "10px";
  deleteBtn.onclick = function() {
    newLI.remove(); // Removes the element directly from the DOM tree
  };

  // Attach delete button inside the LI
  newLI.appendChild(deleteBtn);

  // Step C: Inject the complete LI into our DOM tree
  cartList.appendChild(newLI);
}

// Event trigger to test manipulation
addItemBtn.addEventListener("click", () => {
  addNewProduct("MacBook Pro", 1999);
});
```

- **5. Need to know**
  innerHTML vs innerText Security Trap: নতুন এলিমেন্টের ভেতর টেক্সট দেওয়ার সময় সবসময় innerText বা textContent ব্যবহার করবেন। ভুলেও ইউজারের দেওয়া কোনো ইনপুট সরাসরি innerHTML-এর ভেতর বসাবেন না। এতে হ্যাকাররা মেলিসিয়াস স্ক্রিপ্ট কোড ইনজেক্ট করে আপনার সাইট হ্যাক করতে পারে, যাকে XSS (Cross-Site Scripting) অ্যাটাক বলে।

querySelector vs getElementById: getElementById শুধু আইডি দিয়ে খোঁজে এবং এটি সামান্য দ্রুত কাজ করে। তবে মডার্ন ডেভেলপমেন্টে querySelector সবচেয়ে জনপ্রিয়, কারণ এটি দিয়ে আইডি, ক্লাস, চাইল্ড কম্বিনেশনসহ সব ধরনের জটিল CSS সিলেক্টর দিয়ে নিখুঁতভাবে এলিমেন্ট ধরা যায়।

querySelectorAll Returns a NodeList: যখন querySelectorAll() ব্যবহার করবেন, এটি কিন্তু কোনো একক এলিমেন্ট দেয় না, বরং একটি NodeList দেয়। এর ওপর লুপ চালাতে চাইলে আপনি সরাসরি .forEach() ব্যবহার করতে পারবেন।

### • Advanced Events: Event Listeners, Bubbling & Capturing

- **1. What?**
  Event (ইভেন্ট) হলো ব্রাউজারে ঘটে যাওয়া যেকোনো অ্যাকশন, যেমন: মাউস ক্লিক, কিবোর্ডের বাটন প্রেস বা পেজ স্ক্রল হওয়া।

addEventListener() হলো একটি রিসিভার বা ওয়াচম্যান, যা পেজের কোনো নির্দিষ্ট এলিমেন্টে বসানো থাকে এবং ইভেন্টটি ঘটার সাথে সাথে নির্দিষ্ট কোনো ফাংশন রান করায়।

Event Bubbling & Capturing হলো ইভেন্ট ঘটার পর তা DOM ট্রির ভেতর দিয়ে কীভাবে ওপর-নিচে যাতায়াত করে, তার নিখুঁত ট্রাফিক রুলস।

- **2. Why?**
  মডার্ন প্রজেক্টে আমাদের একই পেজে অনেক ধরনের ক্লিক বা ইউজার অ্যাকশন সামলাতে হয়। ধরুন, একটি শপিং কার্ট লিস্টের ভেতর ১০০টি আইটেম আছে। আপনি প্রতিটি ডিলিট বাটনের জন্য আলাদা আলাদা ১০০টি ইভেন্ট লিসেনার বসালে সাইটের মেমোরি জ্যাম হয়ে পারফরম্যান্স ধীর হয়ে যাবে।

ইভেন্ট বাবলিং-এর মেকানিজম জানা থাকলে আপনি ১০০টি বাটনে লিসেনার না বসিয়ে, তাদের কমন প্যারেন্ট বা বাবার ওপর মাত্র একটি ইভেন্ট লিসেনার বসিয়ে পুরো কাজ নিখুঁতভাবে হ্যান্ডেল করতে পারবেন। একে বলে Event Delegation।

- **3. How?**
  যখন আপনি একটি এলিমেন্টে ক্লিক করেন, ব্রাউজার কিন্তু সরাসরি শুধু ওই এলিমেন্টকেই ক্লিক করে না। ইভেন্টটি মূলত তিনটি ফেজে (Phase) কাজ করে:

Capturing Phase: ইভেন্টটি একদম ওপরের window থেকে শুরু হয়ে নিচের দিকে নেমে আসল টার্গেটে গিয়ে পৌঁছায়। (রিয়েল লাইফে এটি ডিফল্টভাবে অফ থাকে)।

Target Phase: ইভেন্টটি তার আসল টার্গেট এলিমেন্টে (যেমন আপনার ক্লিক করা বাটন) পৌঁছায় এবং কাজ করে।

Bubbling Phase: কাজ শেষে ইভেন্টটি পানির বুদবুদের (Bubble) মতো টার্গেট থেকে আবার ওপরের দিকে উঠতে থাকে এবং তার প্যারেন্ট, গ্র্যান্ডপ্যারেন্ট হয়ে একদম window পর্যন্ত পৌঁছায়। ওঠার পথে যদি কোনো প্যারেন্টেরও একই ইভেন্ট লিসেনার থাকে, তবে সেটিও অটোমেটিক ট্রিগার বা রান হয়ে যায়।

- **4. Code Example**

```HTML
<!-- Inside our HTML layout -->
<div id="grandparent" style="padding: 30px; background: lightgray;">
  Grandparent Box
  <div id="parent" style="padding: 20px; background: darkgray;">
    Parent Box
    <button id="child-btn">Click Me!</button>
  </div>
</div>
```

```JavaScript
const grandparent = document.querySelector("#grandparent");
const parent = document.querySelector("#parent");
const childBtn = document.querySelector("#child-btn");

// ==========================================
// 1. Event Bubbling in Action (Default)
// ==========================================
// If you click "Click Me!", you will see all 3 alerts rise upwards!

childBtn.addEventListener("click", (event) => {
  console.log("1. Child Button Clicked!");
  // To stop the bubble from rising up, we can use:
  // event.stopPropagation();
});

parent.addEventListener("click", () => {
  console.log("2. Parent Box Clicked due to bubbling!");
});

grandparent.addEventListener("click", () => {
  console.log("3. Grandparent Box Clicked due to bubbling!");
});


// ==========================================
// 2. Event Capturing (The Reverse Trick)
// ==========================================
// If you pass true as the 3rd argument, it listens during the downward journey!

/*
grandparent.addEventListener("click", () => {
  console.log("Captured at Grandparent first!");
}, true); // 'true' enables Capturing Phase
*/


// ==========================================
// 3. Event Delegation (The Senior Dev approach)
// ==========================================
// Instead of adding listeners to individual elements, handle it at the parent

grandparent.addEventListener("click", (event) => {
  // 'event.target' contains the exact element that was clicked
  if (event.target && event.target.id === "child-btn") {
    console.log("Delegation Success: Child button handled via Grandparent!");
  }
});
```

- **5. Need to know**
  event.target vs event.currentTarget: ইন্টারভিউতে এই দুটি গুলিয়ে ফেলার জন্য প্রায়ই প্রশ্ন করা হয়। event.target হলো সেই আসল স্পেসিফিক এলিমেন্ট যেটাতে ইউজার ক্লিক করেছেন। আর event.currentTarget হলো সেই এলিমেন্ট, যেটার গায়ে ইভেন্ট লিসেনারটি বর্তমানে ঝুলানো আছে।

event.stopPropagation(): যদি আপনি চান আপনার চাইল্ড বাটনে ক্লিক করলে শুধু চাইল্ডেরই কাজ হবে, সেই ইভেন্ট যেন বাবল হয়ে ওপরের প্যারেন্টদের ডিস্টার্ব না করে, তবে চাইল্ডের ফাংশনের ভেতর event.stopPropagation() লিখে বাবলিং ব্লক করে দিতে হবে।

event.preventDefault(): এটি আরেকটি লাইফ-সেভার মেথড। ব্রাউজারের কোনো ডিফল্ট আচরণ (যেমন: ফর্ম সাবমিট করলে পেজ রিলোড হওয়া বা লিঙ্কে ক্লিক করলে অন্য পেজে চলে যাওয়া) বন্ধ করার জন্য এটি ব্যবহার করা হয়।

### • BOM: Window Object, Screen, History & Location

- **1. What?**
  BOM (Browser Object Model) হলো ব্রাউজারের নিজস্ব আর্কিটেকচার। এটি জাভাস্ক্রিপ্টকে এইচটিএমএল পেজের বাইরে পুরো ব্রাউজার অ্যাপ্লিকেশনের সাথে যোগাযোগের সুযোগ করে দেয়। BOM-এর মূল কেন্দ্রবিন্দু হলো window অবজেক্ট। এর অধীনে ব্রাউজারের বিভিন্ন মেকানিজম যেমন স্ক্রিনের সাইজ জানার জন্য screen, ভিজিট করা পেজের রেকর্ড রাখার জন্য history এবং বর্তমান ইউআরএল ট্র্যাক বা পরিবর্তন করার জন্য location অবজেক্ট কাজ করে।

- **2. Why?**
  একটি মডার্ন ওয়েব অ্যাপ্লিকেশনে শুধুমাত্র পেজের ভেতরের কন্টেন্ট চেঞ্জ করলেই চলে না, ব্রাউজারের বিভিন্ন ফিচারের ওপরও কন্ট্রোল রাখতে হয়। যেমন: ইউজার পেমেন্ট শেষ করার পর তাকে অন্য পেজে রিডাইরেক্ট করা (location), কোনো ফর্মে ভুল সাবমিট হলে তাকে আগের পেজে ফেরত পাঠানো (history), অথবা ইউজারের মনিটরের রেজোলিউশন অনুযায়ী কোনো স্পেশাল পপআপ লেআউট লোড করানো (screen)—এই সব রিয়েল-লাইফ ব্রাউজার ম্যানেজমেন্টের জন্য BOM জানা প্রয়োজন।

- **3. How?**
  window অবজেক্ট: এটি ব্রাউজারের গ্লোবাল প্যারেন্ট অবজেক্ট। আপনি জাভাস্ক্রিপ্টে গ্লোবালি যা কিছু লেখেন, তার সবকিছুই আসলে এই window-এর প্রপার্টি।

window.location: এটি বর্তমান পেজের সম্পূর্ণ ইউআরএল (URL) ঠিকানার একটি অবজেক্ট। এর মাধ্যমে ইউআরএল-এর হোস্টনেম, পোর্ট, প্রোটোকল ইত্যাদি রিড করা যায় এবং এটি চেঞ্জ করে ইউজারকে অন্য ওয়েবসাইটে পাঠানো যায়।

window.history: ইউজার এই ট্যাবে আসার পর কোন কোন পেজ ব্রাউজ করেছেন, তার একটি সিকিউরড মেমোরি লিস্ট। নিরাপত্তার স্বার্থে জাভাস্ক্রিপ্ট সরাসরি পেজের নাম বা ইউআরএল দেখতে দেয় না, তবে কোড লিখে ইউজারকে ব্যাক বা ফরোয়ার্ডে পাঠানো যায়।

window.screen: এটি ইউজারের ফিজিক্যাল মনিটর বা মোবাইল স্ক্রিনের ডিসপ্লে প্রপার্টি (যেমন: ফুল স্ক্রিন চওড়া বা লম্বা কতটুকু) রিড করতে সাহায্য করে।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Window Object (The Global King)
// ==========================================
console.log(window.innerHeight); // Current inner height of the browser window (viewport)
console.log(window.innerWidth);  // Current inner width of the browser window

// Short-hand notation: window can be skipped
// window.alert("Hello"); is same as alert("Hello");


// ==========================================
// 2. Location Object (URL Control)
// ==========================================
console.log(location.href);     // Output: Full URL of the current page
console.log(location.hostname); // Output: e.g., "example.com"
console.log(location.pathname); // Output: e.g., "/dashboard/invoice"

// Redirecting the user programmatically
function redirectToLogin() {
  location.href = "https://example.com/login"; // Changes URL and loads new page
}

// Reloading the page
function refreshPage() {
  location.reload(); // Performs a page refresh
}


// ==========================================
// 3. History Object (Navigation Control)
// ==========================================
function goBackToPreviousPage() {
  history.back(); // Same as clicking the browser's left arrow (Back button)
}

function goForwardAPage() {
  history.forward(); // Same as clicking the browser's right arrow (Forward button)
}

function jumpThreePagesForward() {
  history.go(3); // Moves forward exactly 3 pages in history stack
}


// ==========================================
// 4. Screen Object (Physical Device Display)
// ==========================================
console.log(screen.width);      // Total width of the physical monitor/screen
console.log(screen.height);     // Total height of the physical monitor/screen
console.log(screen.colorDepth); // Color depth of the screen (bits per pixel)
```

- **5. Need to know**
  location.assign() vs location.replace(): ইউজারকে রিডাইরেক্ট করার সময় আপনি যদি location.href = 'url' বা location.assign('url') ব্যবহার করেন, তবে ব্রাউজার হিস্ট্রিতে পুরনো পেজের রেকর্ড থেকে যায় (ইউজার ব্যাক বাটন চাপলে আগের পেজে ফিরতে পারবেন)। কিন্তু যদি location.replace('url') ব্যবহার করেন, তবে এটি বর্তমান পেজের হিস্ট্রি পুরোপুরি মুছে নতুন পেজ বসিয়ে দেয় (ইউজার আর ব্যাক বাটন চেপে ফিরতে পারবেন না)। এটি পেমেন্ট গেটওয়ে বা লগআউট লজিকের জন্য অত্যন্ত গুরুত্বপূর্ণ।

Global Scope Pollution: জাভাস্ক্রিপ্টে কোনো কিওয়ার্ড (let, const, var) ছাড়া সরাসরি ভেরিয়েবল ডিক্লেয়ার করলে (যেমন: myGlobalVar = 10;), সেটি অটোমেটিক window.myGlobalVar হয়ে গ্লোবাল স্কোপ নষ্ট করে। একে বলা হয় Global Pollution, যা মডার্ন কোডিং স্ট্যান্ডার্ডে নিষিদ্ধ।

The Security Wall: নিরাপত্তার কারণে জাভাস্ক্রিপ্ট দিয়ে আপনি ইউজারের ব্রাউজার হিস্ট্রি লিস্টের ভেতর অন্য কোন কোন সাইটের নাম আছে তা দেখতে পারবেন না (history অবজেক্টের সিকিউরিটি রেস্ট্রিকশন)।

### • Browser Storage: LocalStorage & SessionStorage Engines

🌊 Chapter 2: Browser Storage: LocalStorage & SessionStorage Engines

- **1. What?**
  LocalStorage এবং SessionStorage হলো ব্রাউজারের দুটি বিল্ট-ইন মেমোরি ইঞ্জিন বা সেফ-ডিপোজিট বক্স। এদের কাজ হলো ইউজারের ব্রাউজারের ভেতরে সরাসরি ছোটখাটো ডেটা (যেমন: থিম সেটিংস, ইউজার টোকেন, বা শপিং কার্টের আইটেম) স্টোর বা জমা রাখা, যাতে পেজ রিফ্রেশ করলেও বা ব্রাউজার বন্ধ করলেও সেই ডেটা হারিয়ে না যায়।

- **2. Why?**
  আগে যেকোনো ডেটা সেভ করতে হলেই ব্যাকএন্ড সার্ভার বা ডাটাবেজে রিকোয়েস্ট পাঠাতে হতো, যা ছিল সময়সাপেক্ষ ও ব্যয়বহুল। এখন ক্লায়েন্ট-সাইড বা ব্রাউজার স্টোরেজ থাকার কারণে আমরা ইউজারের ছোটখাটো প্রিফারেন্স সার্ভারে না পাঠিয়ে সরাসরি তার ব্রাউজারেই রেখে দিতে পারি।

ইউজার ডার্ক মোড অন করলে পেজ রিফ্রেশ করার পরও যেন ডার্ক মোডই থাকে (LocalStorage)।

ইউজার একটি ওয়ান-টাইম ফর্ম বা ওটিপি (OTP) পেজে থাকাকালীন ট্যাবটি কেটে দিলে যেন সিকিউরিটি খাতিরে ডেটা মুছে যায় (SessionStorage)।

- **3. How?**
  এই দুটি ইঞ্জিনই Key-Value Pair (নাম এবং মান) আকারে ডেটা সেভ রাখে। এদের কাজের মেথডগুলো একদম এক, তবে পার্থক্য শুধু এদের স্থায়িত্ব বা Lifespan-এ:

window.localStorage: এর কোনো এক্সপায়ারি ডেট বা মেয়াদ নেই। আপনি কোড লিখে বা ইউজার নিজে ব্রাউজার ক্যাশ (Cache) ক্লিয়ার না করা পর্যন্ত এই ডেটা বছরের পর বছর ব্রাউজারে থেকে যাবে। এমনকি কম্পিউটার বন্ধ করে অন করলেও ডেটা অক্ষত থাকে।

window.sessionStorage: এর স্থায়িত্ব খুবই কম। যতক্ষণ ব্রাউজারের ওই নির্দিষ্ট ট্যাবটি (Tab) ওপেন আছে, ততক্ষণই এই ডেটা বেঁচে থাকবে। ট্যাবটি কেটে দেওয়া মাত্রই মেমোরি থেকে এই ডেটা চিরতরে ভ্যানিশ বা ডিলিট হয়ে যাবে।

গুরুত্বপূর্ণ নিয়ম: এই দুটি স্টোরেজই শুধুমাত্র String (টেক্সট) ডেটা চেনে। অবজেক্ট বা অ্যারে রাখতে চাইলে তাকে অবশ্যই JSON.stringify() দিয়ে টেক্সটে রূপান্তর করে নিতে হয়।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. LocalStorage Engine (Persistent Storage)
// ==========================================

// Setting data into LocalStorage
localStorage.setItem("themeMode", "dark");
localStorage.setItem("userLanguage", "en");

// Getting data from LocalStorage
const currentTheme = localStorage.getItem("themeMode");
console.log("Current Theme: " + currentTheme); // Output: dark

// Removing a specific item
localStorage.removeItem("userLanguage");

// Clearing all LocalStorage data at once
// localStorage.clear();


// ==========================================
// 2. Handling Complex Data (Objects/Arrays Trap)
// ==========================================

const employeeProfile = {
  id: 404,
  name: "Sabbir",
  role: "Developer"
};

// WRONG WAY: localStorage.setItem("user", employeeProfile); // Saves as "[object Object]"

// RIGHT WAY: Convert to JSON String before saving
localStorage.setItem("cachedUser", JSON.stringify(employeeProfile));

// Retrieving and converting back to JavaScript Object
const stringifiedData = localStorage.getItem("cachedUser");
const parsedUser = JSON.parse(stringifiedData);
console.log("Parsed User Name: " + parsedUser.name); // Output: Sabbir


// ==========================================
// 3. SessionStorage Engine (Tab-scoped Storage)
// ==========================================

// This data survives page refreshes, but dies when the tab is closed
sessionStorage.setItem("sessionToken", "secret_auth_998877");
console.log("Session Token: " + sessionStorage.getItem("sessionToken"));
```

- **5. Need to know**
  The Security Warning (ইন্টারভিউ স্পেশাল): ভুলেও কখনো LocalStorage বা SessionStorage-এর ভেতর ইউজারের ক্রেডিট কার্ডের তথ্য, পাসওয়ার্ড বা খুব বেশি সেনসিটিভ সিক্রেট টোকেন রাখবেন না। কারণ যেকোনো হ্যাকার মেলিসিয়াস স্ক্রিপ্ট রান করে (XSS অ্যাটাকের মাধ্যমে) এক লাইনের কোড console.log(localStorage) লিখে আপনার ইউজারের সব ডেটা চুরি করে নিতে পারে।

Storage Capacity: ব্রাউজার ভেদে এই স্টোরেজের ধারণক্ষমতা সাধারণত সর্বোচ্চ 5MB পর্যন্ত হয়ে থাকে। এর চেয়ে বেশি বড় সাইজের ডেটা বা ফাইল নিয়ে কাজ করতে চাইলে মডার্ন ব্রাউজারের IndexedDB নামক ক্লায়েন্ট-সাইড ডাটাবেজ ব্যবহার করতে হয়।

Same-Origin Policy: এই স্টোরেজগুলো ডোমেইন বা অরিজিন লকড। অর্থাৎ, google.com এর সেট করা লোকাল স্টোরেজ ডেটা কখনো facebook.com থেকে রিড করা সম্ভব নয়।

## 3. Dynamic JS (Asynchronous Architecture)

- **1. What?**
  Asynchronous Architecture (অ্যাসিনক্রোনাস আর্কিটেকচার) হলো জাভাস্ক্রিপ্টের একটি বিশেষ মাল্টি-টাস্কিং ক্ষমতা। জাভাস্ক্রিপ্ট স্বভাবগতভাবে Single-threaded (একক রৈখিক), অর্থাৎ সে একবারে মাত্র একটি কাজ করতে পারে। কিন্তু এই অ্যাসিনক্রোনাস আর্কিটেকচারের সুবাদে সে কোনো একটি দীর্ঘমেয়াদী কাজকে (যেমন: সার্ভার থেকে ডেটা আনা বা ইমেজ লোড হওয়া) ব্যাকগ্রাউন্ডে চালু রেখে, তার জন্য পেজকে আটকে না রেখে কোডের পরের লাইনগুলো এক্সিকিউট বা রান করে যেতে পারে।

- **2. Why?**
  ধরুন, আপনি একটি সোশ্যাল মিডিয়া অ্যাপে ঢুকলেন এবং অ্যাপটি সার্ভার থেকে ইউজারের হোম ফিড লোড করছে। সার্ভার থেকে ডেটা আসতে যদি ৫ সেকেন্ড সময় লাগে, আর জাভাস্ক্রিপ্ট যদি সিনক্রোনাস (Synchronous) বা একমুখী হতো, তবে ওই ৫ সেকেন্ড পুরো ওয়েবসাইট একদম ফ্রিজ বা হ্যাং হয়ে থাকত—আপনি কোনো মেসেজ টাইপ করতে পারতেন না বা অন্য কোনো বাটনে ক্লিকও হতো না। ইউজার এক্সপেরিয়েন্সকে সচল ও প্রাণবন্ত রাখার জন্যই এই আর্কিটেকচার অপরিহার্য।

- **3. How?**
  জাভাস্ক্রিপ্ট ইঞ্জিন (যেমন গুগলের V8) নিজে একা এই ব্যাকগ্রাউন্ডের কাজগুলো সামলাতে পারে না। সে ব্রাউজারের ভেতরের Browser Runtime Environment-এর সাহায্য নেয়। যখনই কোনো অ্যাসিনক্রোনাস কাজ আসে, জাভাস্ক্রিপ্ট তার মূল এক্সিকিউশন লাইন বা Call Stack থেকে সেটিকে ব্রাউজারের Web APIs (যেমন: টাইমার বা নেটওয়ার্ক রিকোয়েস্ট ইঞ্জিন) এর হাতে সঁপে দেয়। ব্যাকগ্রাউন্ডে কাজ শেষ হলে, সেই রেডি হওয়া ফাংশনগুলো একটি লাইনে বা Callback Queue-তে এসে দাঁড়ায়। এরপর Event Loop নামক ট্রাফিক পুলিশটি কল স্ট্যাক ফাঁকা হওয়া মাত্রই কিউ থেকে কাজগুলোকে এনে মেইন লাইনে এক্সিকিউট করে দেয়।

### • The Event Loop, Call Stack & Callback Queue

- **1. What?**
  Call Stack, Callback Queue, এবং Event Loop হলো জাভাস্ক্রিপ্ট ইঞ্জিনের ভেতরের সেই ৩টি মূল ইঞ্জিন বা পার্টস, যা দিয়ে জাভাস্ক্রিপ্ট তার সমস্ত অ্যাসিনক্রোনাস (Asynchronous) মাল্টি-টাস্কিং কাজ পরিচালনা করে।

Call Stack (কল স্ট্যাক): একটি খাড়া লাইনের মতো মেমোরি স্ট্রাকচার, যেখানে জাভাস্ক্রিপ্টের কোডগুলো একের পর এক এসে এক্সিকিউট বা রান হয়। এটি LIFO (Last In, First Out) নিয়মে চলে।

Callback Queue (কলব্যাক কিউ): একটি সাধারণ সিরিয়াল বা লাইনের মতো জায়গা, যেখানে ব্যাকগ্রাউন্ডের কাজ শেষ করে অ্যাসিনক্রোনাস ফাংশনগুলো (Callbacks) মেইন ট্র্যাকে ঢোকার জন্য সিরিয়ালি অপেক্ষা করে। এটি FIFO (First In, First Out) নিয়মে চলে।

Event Loop (ইভেন্ট লুপ): একটি অনবরত ঘুরতে থাকা অদৃশ্য চাকা বা ট্রাফিক পুলিশ, যার একমাত্র কাজ হলো কল স্ট্যাক এবং কলব্যাক কিউ-এর ওপর নজর রাখা।

- **2. Why?**
  জাভাস্ক্রিপ্ট হলো একটি Single-threaded ল্যাঙ্গুয়েজ, অর্থাৎ তার হাত মাত্র একটি এবং সে একবারে একটির বেশি কাজ করতে পারে না। এখন আপনি যদি চান ব্রাউজারে একটি ৫ সেকেন্ডের ফাইল ডাউনলোড ব্যাকগ্রাউন্ডে সচল থাকুক এবং একই সাথে ইউজার যেন পেজের অন্যান্য বাটনেও ক্লিক করতে পারেন—তবে জাভাস্ক্রিপ্টকে এই তিনটি মেকানিজমের ওপর নির্ভর করতে হয়। এই ট্রাফিক সিস্টেমটি না থাকলে একটি মাত্র ভারী কাজের জন্য পুরো ওয়েবসাইট হ্যাং বা ফ্রিজ হয়ে যেত।

- **3. How?**
  ১. যখনই কোনো সাধারণ ফাংশন কোডে রান হয়, সেটি সরাসরি Call Stack-এ গিয়ে এক্সিকিউট হয়ে সাথে সাথে বের হয়ে যায়।
  ২. কিন্তু যখনই কোনো অ্যাসিনক্রোনাস কাজ (যেমন: setTimeout বা API Request) আসে, কল স্ট্যাক তাকে ব্রাউজারের Web API (ব্যাকগ্রাউন্ড ইঞ্জিন)-এর হাতে ম্যারাথন দৌড়ের মতো তুলে দিয়ে নিজে ফাঁকা হয়ে যায়।
  ৩. ব্যাকগ্রাউন্ডে টাইমার বা নেটওয়ার্কের কাজ শেষ হওয়া মাত্রই, সেই কাজের ভেতরের কলব্যাক ফাংশনটি লাফ দিয়ে সরাসরি স্ট্যাকে যেতে পারে না। সে গিয়ে দাঁড়ায় Callback Queue বা টাস্ক কিউ-এর লাইনের পেছনে।
  ৪. এইবার Event Loop অ্যাকশনে আসে। সে সারাক্ষণ চেক করতে থাকে কল স্ট্যাকটি খালি হয়েছে কি না। যখনই মেইন স্ক্রিপ্টের সব কাজ শেষ হয়ে কল স্ট্যাক একদম ফাঁকা হয়ে যায়, ইভেন্ট লুপ সাথে সাথে কিউ-এর সামনে দাঁড়িয়ে থাকা প্রথম কলব্যাক ফাংশনটিকে তুলে নিয়ে কল স্ট্যাকের ভেতর পুশ বা ইনজেক্ট করে দেয় রান হওয়ার জন্য।

- **4. Code Example**

```JavaScript
console.log("🚀 Step 1: Start of the script"); // Goes straight to Call Stack and executes

// Asynchronous task sent to Web API background
setTimeout(() => {
  console.log("⏰ Step 2: Inside the 2-second timeout callback"); // Waited in Callback Queue
}, 2000);

// Another asynchronous task with ZERO delay
setTimeout(() => {
  console.log("⚡ Step 3: Inside the 0-millisecond timeout callback"); // Sent to Web API, then immediately to Callback Queue
}, 0);

console.log("🏁 Step 4: End of the script"); // Runs instantly in Call Stack

// ========================================================
// 🛠️ Expected Output Order in Console:
// ========================================================
// 🚀 Step 1: Start of the script
// 🏁 Step 4: End of the script
// ⚡ Step 3: Inside the 0-millisecond timeout callback (Even with 0ms, it MUST wait for Stack to clear!)
// ⏰ Step 2: Inside the 2-second timeout callback
```

- **5. Need to know**
  The 0ms Timeout Paradox: উপরের কোডে setTimeout-এর টাইম 0 মিলিজেকেন্ড দেওয়া সত্ত্বেও কিন্তু সে Step 4-এর আগে রান হতে পারেনি। কারণ জাভাস্ক্রিপ্টের কঠোর নিয়ম—যতক্ষণ পর্যন্ত কল স্ট্যাক ১০০% ফাঁকা না হবে, ইভেন্ট লুপ কোনো অবস্থাতেই কলব্যাক কিউ থেকে কোনো ফাংশনকে মেইন লাইনে ঢুকতে দেবে না।

The Heavy Loop Trap: আপনি যদি কল স্ট্যাকের ভেতর কোনো বিশাল বড় লুপ চালিয়ে রাখেন (যেমন: ১ থেকে ১০ কোটি পর্যন্ত গণনা), তবে ইভেন্ট লুপ স্তব্ধ হয়ে যাবে। কিউ-তে শত শত বাটন ক্লিকের কলব্যাক জমে থাকলেও সেগুলো স্ক্রিনে কাজ করবে না, কারণ মেইন থ্রেড বা কল স্ট্যাক তখন ব্লকড।

Microtask Queue (VIP Treatment): ইন্টারভিউতে সবচেয়ে বেশি ট্রিকি প্রশ্ন করা হয় এটি নিয়ে। জাভাস্ক্রিপ্টে অ্যাসিনক্রোনাস লাইনে দুটি কিউ থাকে। setTimeout-এর কলব্যাকগুলো যায় MacroTask (or Callback) Queue-তে, আর প্রমিজের (Promise, fetch, async/await) কাজগুলো যায় Microtask Queue-তে। ইভেন্ট লুপ সবসময় Microtask Queue-কে আগে ভিআইপি প্রায়োরিটি দেয়। তাই প্রমিজের কাজ সবসময় setTimeout-এর আগে এক্সিকিউট হবে।

### • Asynchronous JS: Callbacks & Callback Hell

- **1. What?**
  Callback Function (কলব্যাক ফাংশন) হলো এমন একটি সাধারণ ফাংশন, যাকে অন্য একটি ফাংশনের ভেতর প্যারামিটার বা আর্গুমেন্ট হিসেবে পাস করা হয় এবং মূল কাজটি শেষ হওয়ার পর তাকে ভেতর থেকে "কল ব্যাক" বা এক্সিকিউট করা হয়।

Callback Hell (কলব্যাক হেল) হলো জাভাস্ক্রিপ্টের একটি কুখ্যাত কোডিং অবস্থা। যখন একটি অ্যাসিনক্রোনাস কাজের ওপর ভিত্তি করে আরেকটি, তার ওপর ভিত্তি করে আরেকটি—এভাবে একের পর এক কলব্যাক ফাংশন নেস্টেড (একটির ভেতর আরেকটি) করা হয়, তখন কোডটি দেখতে ত্রিভুজের মতো বা ডানের দিকে বেঁকে যায়। একেই বলে কলব্যাক হেল বা Pyramid of Doom।

- **2. Why?**
  জাভাস্ক্রিপ্ট যেহেতু অ্যাসিনক্রোনাস (ব্যাকগ্রাউন্ডে কাজ ফেলে রেখে চলে যায়), তাই সার্ভার থেকে ডেটা আসার আগে যদি আপনি পরের লাইনে সেই ডেটা প্রিন্ট করতে চান, তবে undefined বা এরর পাবেন। ডেটা আসার পরই যেন কাজটি হয়, তা নিশ্চিত করার আদিমতম উপায় হলো কলব্যাক ফাংশন।

তবে রিয়েল-লাইফ প্রজেক্টে (যেমন: ১. ইউজার আইডি দিয়ে প্রোফাইল আনা -> ২. প্রোফাইল থেকে ইনভয়েস লিস্ট আনা -> ৩. ইনভয়েস থেকে স্পেসিফিক বিল ক্যালকুলেট করা) যখন একাধিক অ্যাসিনক্রোনাস কাজ একে অপরের ওপর নির্ভরশীল হয়, তখন কলব্যাক ব্যবহার করলে কোড এতটাই জটিল ও পঠন-অযোগ্য হয়ে পড়ে যে তা মেইনটেইন করা দুঃসাধ্য হয়ে দাঁড়ায়।

- **3. How?**
  সহজ কথায়, একটি ফাংশনকে অন্য ফাংশনের হাতে দিয়ে বলা হয়—"তোমার ব্যাকগ্রাউন্ডের কাজ শেষ হলে এই চিঠিটা (ফাংশনটা) খুলে রান করে দিও"।

যখন এই চিঠির ভেতর আবার আরেকটি কাজ, তার ভেতর আরেকটি চিঠি দেওয়া হয়, তখনই তৈরি হয় কলব্যাক হেল। এই হেলের প্রধান দুটি সমস্যা:

Readability Issue: কোড কোনোভাবেই সোজা লাইনে পড়া যায় না, শুধু ব্র্যাকেটের জটলা তৈরি হয় { { { } } }।

Inversion of Control (নিয়ন্ত্রণ হারানো): আপনি আপনার গুরুত্বপূর্ণ কলব্যাক ফাংশনটি অন্য একটি থার্ড-পার্টি ফাংশনের হাতে ছেড়ে দিচ্ছেন। সেই ফাংশনটি যদি কোনো বাগের কারণে আপনার কলব্যাককে দুইবার কল করে বা আদেও কল না করে, তবে আপনার অ্যাপে বড় ধরণের বাগ তৈরি হবে।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Simulating a Simple Callback Action
// ==========================================
function fetchUserData(userId, callback) {
  setTimeout(() => {
    console.log(`👤 Data retrieved for User: ${userId}`);
    const user = { id: userId, name: "Sabbir" };
    callback(user); // Execution of the callback function
  }, 1000);
}

fetchUserData(101, (userData) => {
  console.log(`Success! Welcome ${userData.name}`);
});


// ==========================================
// 2. Journey into Callback Hell (Pyramid of Doom)
// ==========================================
// Scenario: Login -> Get Orders -> Get Order Details -> Generate Invoice

function loginUser(email, callback) {
  setTimeout(() => {
    console.log("\n🔑 User logged in: " + email);
    callback({ userEmail: email });
  }, 1000);
}

function getUserOrders(email, callback) {
  setTimeout(() => {
    console.log("📦 Orders fetched for: " + email);
    callback(["Order #1", "Order #2", "Order #3"]);
  }, 1000);
}

function getOrderDetails(orderId, callback) {
  setTimeout(() => {
    console.log(`📄 Details retrieved for: ${orderId}`);
    callback({ item: "MacBook Pro", price: 1999 });
  }, 1000);
}

// --- The Hell Begins Here ---
loginUser("sabbir@example.com", function(user) {
  getUserOrders(user.userEmail, function(orders) {
    getOrderDetails(orders[0], function(details) {
      console.log(`💰 Grand Total for ${details.item} is $${details.price}`);

      // If we need to process payment now, we would wrap another callback here...
      // { { { { } } } } -> This shapes like a moving pyramid!

    });
  });
});
```

- **5. Need to know**
  The Escape Route: এই কলব্যাক হেল থেকে মুক্তি পাওয়ার জন্য এবং কোডকে আবার সোজা লাইনে ফিরিয়ে আনার জন্যই মডার্ন জাভাস্ক্রিপ্টে (ES6) জন্ম হয়েছে Promises এবং পরবর্তীতে Async/Await-এর।

Error Handling Trap: কলব্যাক হেলের অন্যতম বড় সমস্যা হলো এরর হ্যান্ডেলিং করা। প্রতিটি লেভেলের নেস্টেড ফাংশনের ভেতর আলাদা আলাদা করে এরর চেক করতে হয়, যা কোডকে আরও নোংরা ও জটিল বানিয়ে ফেলে।

Interview Tip: ইন্টারভিউতে হেলের কথা জিজ্ঞেস করলে সবসময় দুটি পয়েন্ট উল্লেখ করবেন—"Pyramid of Doom Structure" (কোড দেখতে কুৎসিত হওয়া) এবং "Inversion of Control" (থার্ড-পার্টি কোডের ওপর নিজের ফাংশনের কন্ট্রোল হারিয়ে ফেলা)।

### • Promises: Pending, Fulfilled, Rejected & Chaining

- **1. What?**
  Promise (প্রমিজ) হলো জাভাস্ক্রিপ্টের একটি বিশেষ অবজেক্ট, যা কোনো একটি অ্যাসিনক্রোনাস (Asynchronous) কাজের ভবিষ্যৎ ফলাফলের নিশ্চয়তা দেয়। সহজ কথায়, এটি বাস্তব জীবনের একটি প্রতিশ্রুতির মতো—একটি কাজ ব্যাকগ্রাউন্ডে চালু করার সময় জাভাস্ক্রিপ্ট আপনাকে একটি "Promise" প্যাকেট দিয়ে দেয়, যা পরবর্তীতে সফল বা ব্যর্থ হতে পারে।

- **2. Why?**
  এর আগের চ্যাপ্টারে আমরা যে Callback Hell বা কোডের ভেতর কোড ঢুকে কুৎসিত পিরামিড তৈরি হওয়ার সমস্যাটি দেখেছিলাম, তা থেকে চিরতরে মুক্তি পাওয়ার জন্য প্রমিজ তৈরি করা হয়েছে। প্রমিজ ব্যবহার করলে কোড আর ডানের দিকে বেঁকে একটার ভেতর আরেকটা ঢুকে যায় না, বরং সোজা লাইনে নিচ থেকে ওপরে পড়া যায়। এছাড়া এরর হ্যান্ডেলিং করাও অনেক সহজ হয়ে যায়।

- **3. How?**
  একটি প্রমিজ অবজেক্ট তৈরি করার পর সেটি মূলত ৩টি অবস্থার (States) মধ্য দিয়ে যায়:

Pending (অমীমাংসিত): প্রাথমিক অবস্থা। যখন ব্যাকগ্রাউন্ডের কাজটি মাত্র শুরু হয়েছে এবং এখনো কোনো রেজাল্ট আসেনি।

Fulfilled / Resolved (সফল): কাজটি সফলভাবে শেষ হয়েছে এবং কাঙ্ক্ষিত ডেটা বা রেজাল্ট চলে এসেছে। (এটি সম্পন্ন হলে প্রমিজের .then() মেথডটি অ্যাক্টিভেট হয়)।

Rejected (ব্যর্থ): কোনো কারণে কাজটি ব্যর্থ হয়েছে বা এরর (Error) এসেছে। (এটি সম্পন্ন হলে প্রমিজের .catch() মেথডটি অ্যাক্টিভেট হয়)।

Promise Chaining (প্রমিজ চেইনিং): একটি প্রমিজের কাজ শেষ হওয়ার পর .then() থেকে আরেকটি প্রমিজ বা ভ্যালু রিটার্ন করে তার নিচে আবার .then() বসিয়ে মেইন ট্রেনের বগির মতো এক লাইনে একের পর এক কাজ সাজানোর প্রক্রিয়াকে চেইনিং বলে।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Creating a Promise Engine
// ==========================================
function checkInventory(productName) {
  // Promise constructor takes a callback with resolve and reject control functions
  return new Promise((resolve, reject) => {
    console.log(`🛒 Checking warehouse stock for ${productName}...`);

    setTimeout(() => {
      const isAvailable = true; // Simulating stock availability

      if (isAvailable) {
        resolve({ status: "In-Stock", item: productName, price: 1200 }); // Moves state to Fulfilled
      } else {
        reject(`❌ Error: ${productName} is currently out of stock.`); // Moves state to Rejected
      }
    }, 1500);
  });
}


// ==========================================
// 2. Consuming and Chaining Promises (Bye Bye Callback Hell!)
// ==========================================

function processPayment(orderData) {
  return new Promise((resolve) => {
    setTimeout(() => {
      console.log(`💳 Payment processed for ${orderData.item} worth $${orderData.price}`);
      resolve({ orderId: "TXN-9988", ...orderData });
    }, 1000);
  });
}

// Executing the chain in a clean vertical line
checkInventory("Laptop"){
  .then((orderData) => {
    // This runs only if checkInventory calls resolve()
    console.log("📦 Step 1 Success: Item is ready!");
    return processPayment(orderData); // Returning a new promise passes it down the chain
  })
  .then((receipt) => {
    // This runs only if processPayment calls resolve()
    console.log(`🎉 Step 2 Success: Invoice generated! Order ID: ${receipt.orderId}`);
  })
  .catch((errorMessage) => {
    // Global Error Handler: If ANY step fails or throws an error above, execution jumps straight here
    console.error("⚠️ Chain Broken: " + errorMessage);
  })
  .finally(() => {
    // This always runs at the very end, regardless of success or failure
    console.log("🏁 Operation finished.");
  });
}
```

- **5. Need to know**
  The Global Catch Advantage: কলব্যাক হেলের মতো এখানে প্রতিটি ধাপে আলাদা করে এরর চেক করতে হয় না। চেইনের একদম নিচে মাত্র একটি .catch() বসিয়ে দিলেই চলে। চেইনের যেকোনো একটি লিঙ্কে (যেমন ইনভেন্টরি চেক বা পেমেন্ট) কোনো সমস্যা হলে মাঝখানের সব .then() স্কিপ করে কন্ট্রোল সরাসরি .catch()-এ চলে যায়।

Promise.all() (প্যারালাল সুপারপাওয়ার): যদি আপনার প্রজেক্টে এমন ৩টি এপিআই (API) কল থাকে যা একে অপরের ওপর নির্ভরশীল নয়, তবে সেগুলোকে আলাদা আলাদা করে ব্যাক-টু-ব্যাক কল না করে Promise.all([p1, p2, p3]) এর ভেতর ঢুকিয়ে একসাথে সমান্তরালভাবে (Parallel) রান করাতে পারেন। এতে টোটাল সময় অনেক বেঁচে যায়।

Immutability: প্রমিজের স্টেট একবার Fulfilled বা Rejected হয়ে গেলে তা আর কখনো পরিবর্তন করা যায় না। অর্থাৎ, একটি প্রমিজ লাইফে মাত্র একবারই ডেটা রিটার্ন করতে পারে।

### • Async/Await & Error Handling with Try-Catch

- **1. What?**
  Async/Await হলো জাভাস্ক্রিপ্টে অ্যাসিনক্রোনাস (Asynchronous) কোড লেখার সবচেয়ে আধুনিক, ক্লিন এবং পঠন-যোগ্য নিয়ম। এটি আসলে আলাদা কোনো নতুন টেকনোলজি নয়, বরং এটি Promise-এর ওপর তৈরি একটি সুন্দর প্রলেপ বা Syntactic Sugar।

async কিওয়ার্ডটি কোনো ফাংশনের শুরুতে বসিয়ে ব্রাউজারকে জানিয়ে দেওয়া হয় যে, এই ফাংশনের ভেতরে কিছু অ্যাসিনক্রোনাস কাজ আছে।

await কিওয়ার্ডটি শুধুমাত্র async ফাংশনের ভেতরে কোনো প্রমিজের সামনে বসানো হয়, যার মানে হলো—"ব্যাকগ্রাউন্ডের এই প্রমিজটির রেজাল্ট বা ডেটা না আসা পর্যন্ত এই লাইনে শান্ত হয়ে অপেক্ষা করো, তারপর পরের লাইনে যাও।"

- **2. Why?**
  প্রমিজ চেইনিং (.then().then().catch()) কলব্যাক হেল থেকে মুক্তি দিলেও, প্রজেক্ট বড় হওয়ার সাথে সাথে অবিরত ডট-দেন (.then) লিখতে লিখতে কোডটি জটিল এবং দেখতে কিছুটা যান্ত্রিক হয়ে পড়ে। async/await ব্যবহার করলে আপনার অ্যাসিনক্রোনাস কোডগুলো দেখতে হুবহু সাধারণ বা সিনক্রোনাস (Synchronous) কোডের মতো সোজা লাইনে চলে আসে। এর ফলে কোড পড়া, মেইনটেইন করা এবং ট্র্যাডিশনাল try-catch ব্লক দিয়ে এরর হ্যান্ডেল করা বাচ্চাদের খেলার মতো সহজ হয়ে যায়।

- **3. How?**
  ১. যেকোনো সাধারণ ফাংশনের নামের আগে async কিওয়ার্ডটি বসিয়ে দিন। মনে রাখবেন, একটি async ফাংশন ব্যাকগ্রাউন্ডে সবসময় নিজে একটি প্রমিজ রিটার্ন করে।
  ২. ফাংশনের ভেতরে যেখানেই কোনো প্রমিজ কল করবেন (যেমন: fetch() নেটওয়ার্ক রিকোয়েস্ট), তার ঠিক আগে await বসিয়ে দিন। এতে জাভাস্ক্রিপ্ট ওই প্রমিজ থেকে সরাসরি আসল ডেটা টেনে বের করে এনে আপনার ভেরিয়েবলে বসিয়ে দেবে (কোনো .then() লাগবে না)।
  ৩. পুরো কোডটিকে একটি try { ... } catch (error) { ... } ব্লকের ভেতর মুড়িয়ে দিন। যদি try ব্লকের ভেতরের কোনো await লাইনে কোনো প্রমিজ রিজেক্ট (Reject) হয় বা সিস্টেমে কোনো ক্র্যাশ ঘটে, জাভাস্ক্রিপ্ট সাথে সাথে কোড চালানো বন্ধ করে নিচের catch ব্লকে লাফ দেবে।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Simulating Promise-based Service
// ==========================================
function fetchProductData(productId) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const dbSuccess = true; // Set to false to test the catch block
      if (dbSuccess) {
        resolve({ id: productId, name: "Premium Headset", price: 150 });
      } else {
        reject("❌ Database Timeout: Failed to connect to server.");
      }
    }, 1500);
  });
}

// ==========================================
// 2. Modern Async/Await with Try-Catch
// ==========================================
async function checkoutProcess(id) {
  try {
    console.log("🚀 Initializing checkout system...");

    // JavaScript pauses on this line until the promise resolves!
    const product = await fetchProductData(id);

    // This line runs ONLY after the above line successfully finishes
    console.log(`📦 Product Located: ${product.name}`);

    const tax = product.price * 0.15;
    const finalAmount = product.price + tax;

    console.log(`🎉 Checkout Completed! Grand Total: $${finalAmount}`);
    return finalAmount;

  } catch (error) {
    // If ANY await fails or any programmatic error happens inside 'try', it lands here!
    console.error("⚠️ System Alert: Checkout halted.");
    console.error("Reason for failure:", error);
  } finally {
    // This block executes no matter what (success or crash)
    console.log("🏁 Closing secure connection channel.");
  }
}

// Executing our async engine
checkoutProcess(501);
```

- **5. Need to know**
  The Await Blocking Myth: মনে রাখবেন, await কিন্তু আপনার পুরো ব্রাউজার বা ওয়েবসাইটকে ফ্রিজ বা ব্লক করে দেয় না। সে শুধুমাত্র ওই নির্দিষ্ট async ফাংশনের ভেতরের এক্সিকিউশন লাইনটুকুকে সাময়িক পজ (Pause) করে রাখে। ব্রাউজার তার ব্যাকগ্রাউন্ডে অন্যান্য সব বাটন ক্লিক, অ্যানিমেশন বা স্ক্রলিং যথারীতি সচল রাখে।

The Uncaught Reference Error Trap: যদি আপনি async/await ব্যবহার করেন কিন্তু কোডকে try-catch-এর ভেতর না রাখেন, তবে কোনো প্রমিজ রিজেক্ট হলে ব্রাউজার কনসোলে বড় করে "Uncaught (in promise) Error" আসবে। এটি রিয়েল প্রজেক্টের অ্যাপ্লিকেশনকে ক্র্যাশ করিয়ে দিতে পারে। তাই সবসময় try-catch ব্যবহার করা বাধ্যতামূলক।

Top-Level Await: আগে await কিওয়ার্ডটি শুধুমাত্র async ফাংশনের ভেতরেই ব্যবহার করা যেত (বাইরে লিখলে এরর দিত)। তবে মডার্ন জাভাস্ক্রিপ্টে (ES Modules-এ) এখন ফাংশন ছাড়াও ফাইলের একদম টপ-লেভেলে সরাসরি await ব্যবহার করা যায়।

### • Engine Mechanics: Hoisting & Closures

- **1. What?**
  Hoisting (হয়স্টিং) এবং Closures (ক্লোজার) হলো জাভাস্ক্রিপ্ট ইঞ্জিনের একদম ভেতরের দুটি মৌলিক মেকানিজম বা আচরণ।

Hoisting: কোড রান হওয়ার ঠিক আগে জাভাস্ক্রিপ্ট ইঞ্জিন পুরো ফাইলটি একবার রিড করে সমস্ত ভেরিয়েবল এবং ফাংশন ডিক্লেয়ারেশনকে মেমরির একদম টপ বা ওপরে তুলে নেয়।

Closure: যখন একটি ফাংশন অন্য একটি ফাংশনের ভেতরে তৈরি হয়, তখন ভেতরের ফাংশনটি তার বাইরের প্যারেন্ট ফাংশনের ভেরিয়েবল ও স্কোপকে মনে রাখে এবং প্যারেন্ট ফাংশনটি এক্সিকিউট বা শেষ হয়ে যাওয়ার পরও সেই ডেটা অ্যাক্সেস করতে পারে।

- **2. Why?**
  জাভাস্ক্রিপ্টে কোড লেখার সময় অনেক সময় দেখা যায় কোনো ফাংশন ডিক্লেয়ার করার আগেই তাকে কল করা যাচ্ছে, আবার কোনো ভেরিয়েবলের বেলায় তা করলে undefined বা ReferenceError দিচ্ছে। এই রহস্যের সমাধান লুকিয়ে আছে হোইস্টিং-এ।

অন্যদিকে, মডার্ন জাভাস্ক্রিপ্টে ডেটা প্রাইভেসি (Data Privacy) বা কোনো ভেরিয়েবলকে গ্লোবাল স্কোপে নষ্ট না করে অবজেক্টের মতো সিকিউরড রাখার জন্য ক্লোজার একটি পাওয়ারফুল টুল হিসেবে কাজ করে। ইন্টারভিউতে জুনিয়র এবং সিনিয়র ডেভেলপারদের পার্থক্য করার জন্য এই দুটি টপিক সবচেয়ে বেশি জিজ্ঞেস করা হয়।

- **3. How?**
  Hoisting Mechanics: জাভাস্ক্রিপ্ট ইঞ্জিন কোড এক্সিকিউট করার আগে Execution Context তৈরি করে। এই ফেজে সে সব ডিক্লেয়ারেশন মেমরিতে সেট করে।

function ডিক্লেয়ারেশন পুরোপুরি হোইস্ট হয়, তাই একে যেকোনো জায়গায় কল করা যায়।

var হোইস্ট হয়ে মেমরিতে undefined হিসেবে জমা থাকে।

let এবং const হোইস্ট হলেও মেমরির একটি বিশেষ অন্ধকার জোন বা Temporal Dead Zone (TDZ)-এ আটকে থাকে, তাই ডিক্লেয়ার করার আগে এদের অ্যাক্সেস করতে গেলে এরর দেয়।

Closure Mechanics: জাভাস্ক্রিপ্টে প্রতিটি ফাংশনের একটি অদৃশ্য ব্যাকপ্যাক বা লেক্সিক্যাল এনভায়রনমেন্ট (Lexical Environment) থাকে। প্যারেন্ট ফাংশন যখন রান হয়ে রিটার্ন করে চলে যায়, চাইল্ড ফাংশনটি তার ওই ব্যাকপ্যাকে প্যারেন্ট ফাংশনের ভেরিয়েবলগুলো সাথে নিয়ে বেঁচে থাকে।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Hoisting Mechanics Under the Hood
// ==========================================

// Function Hoisting works perfectly
sayHello(); // Output: "Hello World!"

function sayHello() {
  console.log("Hello World!");
}

// var Hoisting returns undefined
console.log(score); // Output: undefined
var score = 100;

// let/const Hoisting falls into Temporal Dead Zone (TDZ)
// console.log(price); // ReferenceError: Cannot access 'price' before initialization
let price = 500;


// ==========================================
// 2. Closure Mechanics (Data Privacy Factory)
// ==========================================

function createBankWallet(ownerName) {
  // Private variable locked inside the parent scope
  let balance = 0;

  // Returning a child object with inner functions (Closures)
  return {
    deposit: function(amount) {
      balance += amount; // Remembers and updates the 'balance' variable
      console.log(`💰 ${ownerName} deposited $${amount}. Balance: $${balance}`);
    },
    withdraw: function(amount) {
      if (amount <= balance) {
        balance -= amount;
        console.log(`💸 ${ownerName} withdrew $${amount}. Balance: $${balance}`);
      } else {
        console.log("❌ Insufficient Funds!");
      }
    }
  };
}

// Generating a completely secure wallet instance
const myWallet = createBankWallet("Sabbir");

myWallet.deposit(500);  // Output: Sabbir deposited $500. Balance: $500
myWallet.withdraw(200); // Output: Sabbir withdrew $200. Balance: $300

// Verification of Data Privacy
console.log(myWallet.balance); // Output: undefined (Hacker cannot access 'balance' directly!)
```

- **5. Need to know**
  The Temporal Dead Zone (TDZ): let এবং const ভেরিয়েবলগুলো হোইস্ট হওয়ার পর থেকে শুরু করে যতক্ষণ না কোডে তাদের ডিক্লেয়ারেশনের লাইনে পৌঁছানো হচ্ছে, মাঝখানের এই কোড এরিয়াটুকুকে বলে TDZ। এই জোনের ভেতর ওই ভেরিয়েবলকে ডাকলে জাভাস্ক্রিপ্ট সরাসরি ক্র্যাশ করবে।

Memory Leak Warning: ক্লোজার অত্যন্ত শক্তিশালী হলেও সাবধানে ব্যবহার করতে হয়। যেহেতু ভেতরের ফাংশন বাইরের ভেরিয়েবলগুলোকে মেমরিতে ধরে রাখে, ব্রাউজারের গার্বেজ কালেক্টর (Garbage Collector) সহজে সেই মেমরি ক্লিন করতে পারে না। অপ্রয়োজনীয় ক্লোজার জমতে থাকলে অ্যাপ্লিকেশনের মেমরি লিক (Memory Leak) হতে পারে।

The Loop Trap: আগে var ব্যবহার করে লুপের ভেতর setTimeout চালালে ক্লোজারের অদ্ভুত আচরণের কারণে লুপের শেষ ভ্যালুটি বারবার প্রিন্ট হতো। মডার্ন জাভাস্ক্রিপ্টে ব্লক-স্কোপড let ব্যবহার করায় এই লুপের ট্র্যাপটি অটোমেটিক সলভ হয়ে গেছে।

## 4. Data Manipulation (Data Engines)

- **1. What?**
  Data Manipulation (ডেটা ম্যানিপুলেশন) হলো জাভাস্ক্রিপ্ট অ্যাপ্লিকেশনের ভেতরে থাকা কাঁচা ডেটা বা ইনফরমেশনকে (যেমন: অ্যারে ও অবজেক্ট) নিজের প্রয়োজন অনুযায়ী কাটছাঁট, ফিল্টার বা সাজিয়ে গুছিয়ে নেওয়ার প্রক্রিয়া। একে এক একটি Data Engine বলা যায়, কারণ ফ্রন্টএন্ডে ডেটা যেভাবে স্ক্রিনে দেখানো হবে কিংবা ব্যাকএন্ডে যেভাবে সেভ হবে—তার পুরো প্রসেসিং মেকানিজমটি এই ইঞ্জিনের দক্ষতার ওপর নির্ভর করে।

- **2. Why?**
  বাস্তব জীবনের যেকোনো প্রজেক্টেই (যেমন: ফেসবুকের নিউজফিড বা আমাজনের প্রোডাক্ট লিস্ট) ডাটাবেজ থেকে হাজার হাজার ডেটা অবজেক্টের একটি বড় স্তূপ চলে আসে। এখন আপনি যদি চান শুধু "আজকের দিনের অফার থাকা প্রোডাক্টগুলো" আলাদা করতে, দাম অনুযায়ী কম থেকে বেশি ক্রমানুসারে সাজাতে (sort), অথবা সব প্রোডাক্টের টোটাল প্রাইস এক লাইনে হিসাব করতে—তবে জাভাস্ক্রিপ্টের বিল্ট-ইন ডেটা ইঞ্জিন মেথডগুলো ছাড়া আপনি কোড লিখতে পারবেন না। এটি না জানলে সাধারণ ডেটা প্রসেস করতেই শত শত লাইনের জটিল লুপ লিখতে হবে, যা কোডের পারফরম্যান্স ধীর করে দেয়।

- **3. How?**
  জাভাস্ক্রিপ্ট মূলত অ্যারে এবং অবজেক্ট ম্যানিপুলেশনের জন্য অত্যন্ত শক্তিশালী এবং আধুনিক কিছু হাইয়ার-অর্ডার ফাংশন (Higher-Order Functions) এবং মেকানিজম সরবরাহ করে। এই মেকানিজমগুলো ব্যাকগ্রাউন্ডে অত্যন্ত অপ্টিমাইজড উপায়ে ডেটা প্রসেস করে। যেমন:

.map() দিয়ে পুরো ডেটা সেটকে এক নিমেষে রুপান্তর (Transform) করা যায়।

.filter() দিয়ে নির্দিষ্ট শর্তে ডেটা ছেঁকে আলাদা করা যায়।

.reduce() দিয়ে একটি বিশাল অ্যারের ডেটাকে হিসাব-নিকাশ করে একটি একক মানে (Single Value) নামিয়ে আনা যায়।

### • Array Mechanics: map(), filter(), reduce(), find(), slice(), splice()

- **1. What?**
  জাভাস্ক্রিপ্টে Array Mechanics হলো অ্যারে ডেটা প্রসেস এবং কাটছাঁট করার অত্যন্ত শক্তিশালী বিল্ট-ইন মেকানিজম।

.map(), .filter(), .reduce(), এবং .find() হলো হাইয়ার-অর্ডার মেথড (Higher-Order Methods), যা লুপ ছাড়াই অ্যারের ভেতরের উপাদানগুলো প্রসেস করতে পারে।

.slice() এবং .splice() হলো অ্যারের অংশবিশেষ কেটে আলাদা করা বা নতুন উপাদান ঢুকানোর মেকানিকাল টুলস।

- **2. Why?**
  ডাটাবেজ বা এপিআই (API) থেকে যখন কোনো ডেটা লিস্ট আসে, তা সরাসরি স্ক্রিনে বসানো যায় না। ধরুন একটি ইউজার লিস্ট থেকে আপনার শুধু সক্রিয় (Active) ইউজারদের আলাদা করা প্রয়োজন (.filter()), তাদের আইডির একটি নতুন অ্যারে দরকার (.map()), অথবা নির্দিষ্ট একটি ইউজারকে খোঁজা দরকার (.find())। এই মেথডগুলো ছাড়া এই কাজগুলো করতে গেলে আপনাকে ডজন ডজন লাইনের জটিল ও ক্লামজি for লুপ লিখতে হতো, যা মেইনটেইন করা অসম্ভব।

- **3. How?**
  এই মেথডগুলোকে মূলত ২টি ভাগে ভাগ করা যায়:

Immutable Methods (অরিজিনাল অ্যারে চেঞ্জ করে না): .map(), .filter(), .reduce(), .find(), এবং .slice()। এরা কাজ শেষে সম্পূর্ণ নতুন একটি অ্যারে বা মান রিটার্ন করে, মেইন অ্যারে যেমন ছিল তেমনই থাকে।

Mutable Methods (অরিজিনাল অ্যারে ধ্বংস বা পরিবর্তন করে): .splice()। এটি সরাসরি মূল অ্যারের ভেতরে গিয়ে অপারেশন চালায়, তাই এটি ব্যবহারে সাবধান থাকতে হয়।

- **4. Code Example**

```JavaScript
// ==========================================
// Sample Data: E-Commerce Products
// ==========================================
const products = [
  { id: 101, name: "Mouse", price: 25, category: "Electronics" },
  { id: 102, name: "Keyboard", price: 75, category: "Electronics" },
  { id: 103, name: "Backpack", price: 50, category: "Lifestyle" },
  { id: 104, name: "Monitor", price: 300, category: "Electronics" }
];

// ==========================================
// 1. .map() - Transforming Data (creates new array)
// ==========================================
const productNames = products.map(prod => prod.name);
console.log(productNames); // Output: ["Mouse", "Keyboard", "Backpack", "Monitor"]

// ==========================================
// 2. .filter() - Screening Data (creates new array)
// ==========================================
const electronics = products.filter(prod => prod.category === "Electronics");
console.log(electronics.length); // Output: 3 (Only electronic items passed the condition)

// ==========================================
// 3. .find() - Searching Data (returns single element or undefined)
// ==========================================
const expensiveProduct = products.find(prod => prod.price > 100);
console.log(expensiveProduct.name); // Output: "Monitor" (Stops searching after the first match)

// ==========================================
// 4. .reduce() - Accumulating Data (returns a single value)
// ==========================================
// Syntax: array.reduce((accumulator, currentValue) => ..., initialValue)
const totalPrice = products.reduce((total, prod) => total + prod.price, 0);
console.log(totalPrice); // Output: 450

// ==========================================
// 5. .slice() - Safe Cutting (Immutable)
// ==========================================
// Syntax: array.slice(startIndex, endIndexBefore)
const subList = productNames.slice(1, 3);
console.log(subList);      // Output: ["Keyboard", "Backpack"]
console.log(productNames); // Unchanged: ["Mouse", "Keyboard", "Backpack", "Monitor"]

// ==========================================
// 6. .splice() - Radical In-Place Modification (Mutable!)
// ==========================================
// Syntax: array.splice(startIndex, deleteCount, item1, item2, ...)
const rawNumbers = [10, 20, 30, 40, 50];
const deletedItems = rawNumbers.splice(1, 2, 99, 100); // deletes 20, 30 and injects 99, 100

console.log(deletedItems); // Output: [20, 30] (The items removed)
console.log(rawNumbers);   // Output: [10, 99, 100, 40, 50] (Original array is permanently modified!)
```

- **5. Need to know**
  The .find() vs .filter() Trap: ইন্টারভিউতে প্রায়ই জিজ্ঞেস করা হয় এই দুটির পার্থক্য। .filter() পুরো অ্যারে খুঁজে যতগুলো ম্যাচ পাবে সবগুলোর একটি নতুন Array দেবে (এমনকি একটি ম্যাচ পেলেও)। আর .find() প্রথম ম্যাচটি পাওয়া মাত্রই পুরো সার্চিং বন্ধ করে সরাসরি ওই Object বা উপাদানটি রিটার্ন করে। কোনো ম্যাচ না পেলে .filter() দেয় খালি অ্যারে [] আর .find() দেয় undefined।

The .reduce() Initial Value: .reduce() মেথড লেখার সময় লাস্টের initialValue (যেমন উপরে আমরা 0 দিয়েছি) দিতে কখনো ভুলবেন না। এটি না দিলে জাভাস্ক্রিপ্ট ডিফল্টভাবে অ্যারের প্রথম আইটেমকেই ইনিশিয়াল ভ্যালু ধরে নেয়, যা অবজেক্ট অ্যারের ক্ষেত্রে হিসাব এলোমেলো করে মারাত্মক বাগের জন্ম দেয়।

Chaining Superpower: যেহেতু .map() এবং .filter() নতুন অ্যারে রিটার্ন করে, আপনি এদেরকে এক লাইনে চেইন করতে পারেন। যেমন: products.filter(...).map(...)—প্রথমে ফিল্টার করে তারপর ম্যাপ করা। রিয়েল প্রজেক্টে এটি প্রচুর ব্যবহৃত হয়।

### • Object Deep-Dive: Object.keys(), Object.values(), Entries & Destructuring

- **1. What?**
  জাভাস্ক্রিপ্টে অবজেক্ট হলো ডেটা স্টোর করার মূল স্ট্রাকচার। Object Deep-Dive মেকানিজমগুলো আমাদের এই অবজেক্টের ভেতরের ডেটা নিয়ে সহজে খেলাধুলা করার সুবিধা দেয়।

Object.keys(): অবজেক্টের সবগুলোর চাবির (Properties/Keys) একটি অ্যারে দেয়।

Object.values(): অবজেক্টের ভেতরের সবগুলোর মানের (Values) একটি অ্যারে দেয়।

Object.entries(): চাবি এবং মান দুটোকে জোড়ায় জোড়ায় রূপান্তর করে একটি দ্বিমাত্রিক (2D) অ্যারে দেয়।

Destructuring (ডিপ্যাকিং): অবজেক্ট বা অ্যারের ভেতরের উপাদানগুলোকে টেনে বের করে এক লাইনে আলাদা আলাদা লোকাল ভেরিয়েবলে রূপান্তর করার একটি আধুনিক আর্কিটেকচার।

- **2. Why?**
  বাস্তব প্রজেক্টে ব্যাকএন্ড থেকে যখন কোনো সিঙ্গেল ইউজার অবজেক্ট আসে, তখন তার ওপর সরাসরি .map() বা .filter() চালানো যায় না, কারণ ম্যাপ শুধু অ্যারের ওপর কাজ করে। এখন আপনি যদি চান ইউজারের প্রোফাইলে কয়টি ইনফরমেশন আছে তা গুনতে, কিংবা অবজেক্টের ডেটাগুলোকে লুপ ঘুরিয়ে স্ক্রিনে দেখাতে—তবে এই মেথডগুলো দিয়ে অবজেক্টকে প্রথমে অ্যারে বানিয়ে নিতে হয়।

অন্যদিকে, কোনো অবজেক্ট থেকে নির্দিষ্ট ৩টি ডেটা বের করতে বারবার user.profile.name, user.profile.email লিখতে লিখতে কোড নোংরা হয়ে যায়। ডেসট্রাকচারিং কোডকে ক্লিন ও রিডেবল রাখতে সাহায্য করে।

- **3. How?**
  Static Reflection Methods: Object.keys/values/entries মেথডগুলো সরাসরি গ্লোবাল Object-এর ওপর অ্যাপ্লাই করতে হয় এবং ব্র্যাকেটের ভেতর টার্গেট অবজেক্টটিকে পাস করতে হয়।

Destructuring Syntax: এটি অবজেক্টের ডানপাশ থেকে ডেটা ভেঙে বামপাশের কার্লি ব্র্যাকেটের { } ভেতরের সমনামীয় ভেরিয়েবলে অ্যাসাইন বা পুশ করে দেয়। এর মাধ্যমে ভেরিয়েবলের নাম পরিবর্তন (Renaming) এবং ডিফল্ট ভ্যালু (Default Values) সেট করার সুবিধাও পাওয়া যায়।

- **4. Code Example**

```JavaScript
// ==========================================
// Sample Data: Secure User Profile
// ==========================================
const userProfile = {
  username: "sabbir404",
  email: "sabbir@example.com",
  role: "Lead Engineer",
  experience: 5
};

// ==========================================
// 1. Reflecting Object Structure to Arrays
// ==========================================

const keys = Object.keys(userProfile);
console.log(keys); // Output: ["username", "email", "role", "experience"]

const values = Object.values(userProfile);
console.log(values); // Output: ["sabbir404", "sabbir@example.com", "Lead Engineer", 5]

const entries = Object.entries(userProfile);
console.log(entries);
// Output: [ ["username", "sabbir404"], ["email", "sabbir@example.com"], ... ]


// ==========================================
// 2. Object Destructuring (The Modern Standard)
// ==========================================

// Extracting properties directly into local variables
const { username, role } = userProfile;
console.log(username); // Output: sabbir404
console.log(role);     // Output: Lead Engineer

// Advance Trick: Renaming and setting Default Value
const { email: userEmail, status = "Active" } = userProfile;
console.log(userEmail); // Output: sabbir@example.com (Renamed from 'email')
console.log(status);    // Output: Active (Assigned fallback default since 'status' was missing)


// ==========================================
// 3. Destructuring in Function Parameters (Clean Code)
// ==========================================

// Instead of passing the whole object and typing profile.name, destructure on the fly!
function displayBadge({ username, role }) {
  console.log(`🎖️ ${username} - ${role}`);
}

displayBadge(userProfile); // Output: 🎖️ sabbir404 - Lead Engineer
```

- **5. Need to know**
  The Undefined Destructuring Crash: অবজেক্ট ডেসট্রাকচার করার সময় খেয়াল রাখবেন মূল অবজেক্টটি যেন কোনোভাবে null বা undefined না হয়। যেমন: const { name } = fetchedData;—এখানে fetchedData যদি এপিআই থেকে না এসে ফাঁকা থাকে, তবে জাভাস্ক্রিপ্ট "TypeError: Cannot destructure property of 'undefined'" এরর দিয়ে পুরো অ্যাপ ক্র্যাশ করিয়ে দেবে। তাই প্রয়োজনে অপশনাল চেইনিং বা সেফটি চেক রাখা ভালো।

Ordering Doesn't Matter: অবজেক্ট ডেসট্রাকচারিং-এর সময় সিরিয়াল কোনো ব্যাপার না। আপনি { role, username } লিখলেও জাভাস্ক্রিপ্ট অবজেক্টের ভেতর থেকে সঠিক নামের চাবিটিই খুঁজে বের করবে। (তবে অ্যারে ডেসট্রাকচারিং-এর ক্ষেত্রে সিরিয়াল বা ইনডেক্স কঠোরভাবে মেনে চলতে হয়)।

Object.entries() inside for...of: অবজেক্টের চাবি এবং মান দুটো ধরে একসাথে লুপ চালানোর জন্য সবচেয়ে বেস্ট কম্বিনেশন হলো:

```JavaScript
for (const [key, value] of Object.entries(userProfile)) {
  console.log(`${key}: ${value}`);
}
```

### • Spread & Rest Operators (...)

🌊 Chapter 5: Spread & Rest Operators (...)

- **1. What?**
  Spread Operator এবং Rest Operator—দুটো দেখতে হুবহু এক হলেও (তিনটি ডট ...), জাভাস্ক্রিপ্ট ইঞ্জিনে এদের কাজ সম্পূর্ণ উল্টো। এরা মূলত সিচুয়েশনের ওপর ভিত্তি করে নিজেদের রূপ বদল করে।

Spread Operator (ছড়িয়ে দেওয়া): এটি কোনো একটি অ্যারে বা অবজেক্টের ভেতরের সব উপাদানকে টেনে বের করে চারদিকে ছড়িয়ে বা মুক্ত করে দেয়।

Rest Operator (একত্রিত করা): এটি আলাদা আলাদা বা ছড়িয়ে থাকা একাধিক উপাদানকে কুড়িয়ে এনে একটি নির্দিষ্ট অ্যারের ভেতর প্যাক বা বন্দি করে ফেলে।

- **2. Why?**
  মডার্ন জাভাস্ক্রিপ্টে ডেটা ম্যানেজমেন্ট আরও সহজ করার জন্য এদের ভূমিকা অপরিসীম।

কোনো অ্যারে বা অবজেক্টের মূল কপি নষ্ট না করে (Immutability বজায় রেখে) তার ক্লোন বা ডুপ্লিকেট তৈরি করতে এবং দুটি অ্যারেকে এক লাইনে জোড়া দিতে Spread অপারেটর চমৎকার কাজ করে।

কোনো ফাংশনে ইউজার কতগুলো প্যারামিটার পাঠাবেন তা যদি আগে থেকে নিশ্চিত না থাকে (যেমন একটি ক্যালকুলেটরে ইউজার ৫টি সংখ্যাও যোগ করতে পারেন, আবার ৫০টিও পারেন), তবে সেই আনলিমিটেড আর্গুমেন্টগুলোকে এক লাইনে হ্যান্ডেল করার জন্য Rest অপারেটর লাইফ সেভার হিসেবে কাজ করে।

- **3. How?**
  Spread Mechanics: যখন তিনটি ডট ... কোনো ভ্যালু অ্যাসাইনমেন্টের ডানপাশে, কোনো ফাংশন কলের ভেতরে কিংবা নতুন কোনো অ্যারে/অবজেক্ট লিটারেলের ভেতরে বসে, তখন সে Spread হিসেবে কাজ করে ডেটা খুলে দেয়।

Rest Mechanics: যখন তিনটি ডট ... কোনো ফাংশনের প্যারামিটার ডিফাইন করার জায়গায় কিংবা ডেসট্রাকচারিং-এর বামপাশে বসে, তখন সে বাকি সব এলিমেন্টকে চাটনি বানিয়ে একটি অ্যারেতে রূপান্তর করে। (মনে রাখবেন, Rest সবসময় সবার শেষে বসে)।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Spread Operator in Action (Unpacking)
// ==========================================

const localBrands = ["Nagad", "bKash"];
const globalBrands = ["Visa", "MasterCard"];

// Merging arrays easily without .concat()
const paymentGateways = [...localBrands, "Rocket", ...globalBrands];
console.log(paymentGateways);
// Output: ["Nagad", "bKash", "Rocket", "Visa", "MasterCard"]

// Safe Cloning (Avoiding Reference Copy Trap)
const originalUser = { name: "Sabbir", role: "Dev" };
const updatedUser = { ...originalUser, location: "Dhaka", role: "Lead Dev" };

console.log(updatedUser); // Output: { name: "Sabbir", role: "Lead Dev", location: "Dhaka" }
console.log(originalUser.role); // Unchanged: "Dev" (Immutability Success!)


// ==========================================
// 2. Rest Operator in Action (Packing)
// ==========================================

// Handling dynamic/unlimited arguments inside a function
function calculateTotalInvoice(customerName, ...prices) {
  // 'prices' converts all remaining individual numbers into a perfect array!
  const total = prices.reduce((sum, current) => sum + current, 0);
  console.log(`🧾 Customer: ${customerName} | Grand Total: $${total}`);
}

calculateTotalInvoice("Rakib", 10, 25, 45, 120);
// Output: 🧾 Customer: Rakib | Grand Total: $200


// ==========================================
// 3. Destructuring Combination (Spread & Rest Together)
// ==========================================

const scoreBoard = [98, 85, 72, 64, 40];

// Extracting top two, and packing the rest of the values
const [gold, silver, ...bronzeGroup] = scoreBoard;

console.log(gold);        // Output: 98
console.log(silver);      // Output: 85
console.log(bronzeGroup); // Output: [72, 64, 40] (Created via Rest)
```

- **5. Need to know**
  The Shallow Copy Trap (ইন্টারভিউ স্পেশাল): মনে রাখবেন, ... Spread operator দিয়ে যখন আপনি কোনো অবজেক্ট ক্লোন করেন, তা কিন্তু শুধুমাত্র Shallow Copy বা এক লেভেল পর্যন্ত ব্যাকআপ তৈরি করে। অবজেক্টের ভেতর যদি আরও কোনো নেস্টেড অবজেক্ট বা অ্যারে থাকে, তবে তাদের ভেতরের রেফারেন্স কিন্তু আগেরটাই থেকে যায়। গভীর বা ১০০% নিখুঁত ক্লোন করতে চাইলে structuredClone() মেথড ব্যবহার করা উচিত।

The "Last Child" Rule: ফাংশনের প্যারামিটারে বা ডেসট্রাকচারিং-এ Rest অপারেটরকে সবসময় একদম শেষে বসাতে হবে। যেমন: function myFunc(...items, lastItem) {}—এটি একটি মারাত্মক সিনট্যাক্স এরর (SyntaxError: Rest parameter must be last formal parameter) দিবে। কারণ ব্রাউজার বুঝতে পারে না বাকি ডেটা নেওয়ার পর লাস্ট আইটেম কোনটা হবে।

### • Optional Chaining (`?.`) & Nullish Coalescing (`??`)

🌊 Chapter 5: Optional Chaining (?.) & Nullish Coalescing (??)

- **1. What?**
  Optional Chaining (?.) এবং Nullish Coalescing (??) হলো মডার্ন জাভাস্ক্রিপ্টের (ES2020) দুটি অত্যন্ত জনপ্রিয় লাইফ-সেভার অপারেটর।

?. (অপশনাল চেইনিং) কোনো অবজেক্টের গভীরে থাকা কোনো প্রপার্টি বা মেথড অ্যাক্সেস করার আগে চেক করে যে সেই প্যারেন্ট অবজেক্টটি আসলেই বেঁচে আছে নাকি null/undefined হয়ে আছে।

?? (নালিশ কোয়ালেসিং) হলো একটি ডিফল্ট ভ্যালু অ্যাসাইন করার অপারেটর, যা শুধুমাত্র বামপাশের ভ্যালুটি null বা undefined হলে ডানপাশের ডিফল্ট ভ্যালুটিকে গ্রহণ করে।

- **2. Why?**
  বাস্তব জীবনের প্রজেক্টে এপিআই (API) থেকে ডেটা আসার সময় অনেক সময় কোনো কোনো ইউজারের প্রোফাইল পিকচার বা অ্যাড্রেস মিসিং থাকতে পারে। জাভাস্ক্রিপ্টের নিয়ম হলো—কোনো undefined জিনিসের ভেতর ডট দিয়ে গভীরের প্রপার্টি খুঁজতে গেলেই (undefined.city) সে সাথে সাথে বড়সড় এরর দিয়ে পুরো অ্যাপ্লিকেশন ক্র্যাশ করিয়ে দেয়। এই ক্র্যাশ বা "Cannot read properties of undefined" এরর থেকে বাঁচতে আগে বড় বড় if-else বা নোংরা অ্যান্ড (&&) কন্ডিশন লিখতে হতো। এই নতুন অপারেটর দুটি কোডকে এক লাইনে ও একদম ক্লিন রেখে অ্যাপ্লিকেশন ক্র্যাশ হওয়া ঠেকায়।

- **3. How?**
  Optional Chaining (?.): এটি ডট (.) অপারেটরের মতোই কাজ করে, তবে সে অ্যাক্সেস করার আগে বামপাশের জিনিসটিকে ভ্যালিডেশন চেক করে। যদি বামপাশের অংশটি null বা undefined হয়, তবে সে সামনে আর না এগিয়ে ওখানেই থেমে গিয়ে শান্তভাবে undefined রিটার্ন করে দেয়—কোনো এরর দেয় না।

Nullish Coalescing (??): এটি ট্র্যাডিশনাল অর (||) অপারেটরের মতো মনে হলেও এর একটি সূক্ষ্ম বিচারবুদ্ধি আছে। অর (||) অপারেটর 0 বা খালি স্ট্রিং "" কেও মিথ্যা বা ফালতু (Falsy) ভ্যালু মনে করে ডিলিট বা স্কিপ করে দেয়। কিন্তু ?? অপারেটর খুব ভালো করে জানে যে ইউজারের স্কোর 0 হতে পারে কিংবা নাম খালি "" হতে পারে। সে শুধুমাত্র এবং শুধুমাত্র null এবং undefined পেলেই ডানপাশের ব্যাকআপ বা ডিফল্ট ভ্যালুটিকে পুশ করে।

- **4. Code Example**

```JavaScript
// ==========================================
// Sample Data: API Response with Missing Fields
// ==========================================
const user1 = {
  name: "Sabbir",
  preferences: {
    theme: "dark",
    score: 0 // valid falsy value
  }
};

const user2 = {
  name: "Rakib"
  // preferences object is completely missing/undefined here!
};

// ==========================================
// 1. Optional Chaining (?.) preventing crashes
// ==========================================

// Old Ugly Way:
// const theme1 = user2.preferences && user2.preferences.theme;

// Modern Safe Way:
const theme1 = user1.preferences?.theme;
console.log(theme1); // Output: dark

const theme2 = user2.preferences?.theme;
console.log(theme2); // Output: undefined (Safe! No application crash)


// ==========================================
// 2. Nullish Coalescing (??) vs Logical OR (||) Trap
// ==========================================

// Scenario A: Checking user score (using ||)
const scoreWithOR = user1.preferences?.score || 50;
console.log(scoreWithOR);
// Output: 50 ❌ (Wrong! User actually scored 0, but || treated 0 as falsy and overwrote it!)

// Scenario B: Checking user score (using ??)
const scoreWithNullish = user1.preferences?.score ?? 50;
console.log(scoreWithNullish);
// Output: 0 ✓ (Perfect! It detects 0 as a valid number and doesn't jump to default)


// ==========================================
// 3. Combining Both for Ultimate Clean Code
// ==========================================

// If the user profile lacks an address or city, smoothly fall back to "Dhaka"
const userCity = user2.contact?.address?.city ?? "Dhaka";
console.log(userCity); // Output: Dhaka
```

- **5. Need to know**
  Function and Array Support: অপশনাল চেইনিং শুধু অবজেক্ট প্রপার্টিতেই নয়, ফাংশন বা অ্যারের ক্ষেত্রেও চমৎকার কাজ করে। যেমন: apiData?.methods?.download?.()—ডাউনলোড ফাংশনটি যদি এপিআই থেকে না-ও আসে, তবুও কোড ক্র্যাশ করবে না। অ্যারের ক্ষেত্রে: userList?.[0]।

Assignment Side Warning: মনে রাখবেন, ?. অপারেটর শুধুমাত্র ডেটা রিড বা পড়ার জন্য ব্যবহার করা যায়, ডেটা সেট বা লেখার জন্য এটি নিষিদ্ধ। যেমন: user2.preferences?.theme = "light"; লিখতে গেলে ব্রাউজার সরাসরি SyntaxError: Invalid left-hand side in assignment দিয়ে কোড আটকে দেবে।

The Guardrail Rule: ইন্টারভিউতে এদের মূল উদ্দেশ্য জিজ্ঞেস করলে এক কথায় বলবেন—"কোডবেসকে সেফ রাখা এবং null বা undefined জনিত আনকcaught টাইপ এরর (TypeError) ঠেকানো।"

### • String & Math Internals: split(), join(), replace() & Math Methods

🌊 Chapter 5: String & Math Internals: split(), join(), replace() & Math Methods

- **1. What?**
  String & Math Internals হলো টেক্সট প্রসেসিং এবং গাণিতিক হিসাব-নিকাশ করার জন্য জাভাস্ক্রিপ্টের নিজস্ব দুটি কোর সিস্টেম বা মেকানিজম।

split() এবং replace() হলো স্ট্রিং (টেক্সট) অবজেক্টের ইন্টারনাল মেথড, আর join() হলো অ্যারের মেথড (যা স্ট্রিং তৈরিতে সাহায্য করে)।

Math হলো জাভাস্ক্রিপ্টের একটি বিল্ট-ইন গ্লোবাল অবজেক্ট, যার অধীনে বিভিন্ন মেথড (যেমন: .round(), .ceil(), .floor(), .random()) গাণিতিক অ্যালগরিদম রান করতে ব্যবহৃত হয়।

- **2. Why?**
  বাস্তব জীবনে আপনি যখন কোনো ইনপুট ফর্ম বা ডেটা নিয়ে কাজ করবেন, তখন র-টেক্সটকে কেটে কুচোকুচো করা বা মডিফাই করা অত্যন্ত সাধারণ বিষয়। যেমন: ইউজারের দেওয়া একটি বাক্য থেকে স্পেসগুলো বাদ দিয়ে ইউআরএল ফ্রেন্ডলি স্লাগ (slug-url) তৈরি করা, কিংবা ইনপুটের ফোন নাম্বার থেকে ড্যাশ (-) চিহ্নগুলো বদলে ফেলা (replace)।

আবার ই-কমার্স অ্যাপ্লিকেশনে ডিসকাউন্ট দেওয়ার পর ট্যাক্স হিসাব করে দশমিকের সংখ্যাকে রাউন্ড ফিগার করা, কিংবা লটারির অ্যাপে ১ থেকে ১০০ এর মধ্যে একটি র‍্যান্ডম নাম্বার তৈরি করার জন্য Math ইঞ্জিনের সাহায্য নিতেই হবে।

- **3. How?**
  String.prototype.split(delimiter): একটি আস্ত স্ট্রিংকে ডিলিমিটার বা নির্দিষ্ট চিহ্নের (যেমন স্পেস, কমা) ওপর ভিত্তি করে ভেঙে টুকরো টুকরো করে একটি নিখুঁত Array বানিয়ে দেয়।

Array.prototype.join(separator): এটি ঠিক উল্টো কাজ করে। একটি অ্যারের সব উপাদানকে আঠা দিয়ে জোড়া লাগানোর মতো একটি নির্দিষ্ট ক্যারেক্টার দিয়ে যুক্ত করে একটি একক String বানিয়ে দেয়।

String.prototype.replace(target, replacement): স্ট্রিংয়ের ভেতরের নির্দিষ্ট কোনো শব্দ বা অক্ষরকে বদলে নতুন শব্দ বসিয়ে দেয়। (সবগুলো বদলাতে চাইলে মডার্ন replaceAll() বা Regular Expression ব্যবহার করা হয়)।

Math Engine: এটি কোনো ফাংশন নয়, একটি স্ট্যাটিক অবজেক্ট।

Math.floor() সবসময় দশমিকের নিচের পূর্ণসংখ্যায় নামিয়ে দেয় (যেমন ৩.৯ হয়ে যাবে ৩)।

Math.ceil() সবসময় দশমিকের ওপরের পূর্ণসংখ্যায় উঠিয়ে দেয় (যেমন ৩.১ হয়ে যাবে ৪)।

Math.random() প্রতিবার 0 থেকে শুরু করে 1-এর ঠিক আগ পর্যন্ত (0.999...) একটি র‍্যান্ডম দশমিক সংখ্যা জেনারেট করে।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. String Interlocking: Split, Modify & Join
// ==========================================
const rawTitle = "  Learn JavaScript  Data Engines  ";

// Cleaning and splitting by space
const wordsArray = rawTitle.trim().split(/\s+/);
console.log(wordsArray); // Output: ["Learn", "JavaScript", "Data", "Engines"]

// Rejoining the array with dashes to create a URL slug
const urlSlug = wordsArray.join("-").toLowerCase();
console.log(urlSlug); // Output: "learn-javascript-data-engines"


// ==========================================
// 2. Text Transformation with replace/replaceAll
// ==========================================
const announcement = "The price is $10 for the mouse and $10 for the pad.";

// .replace() only hits the FIRST match
const fixedFirst = announcement.replace("$10", "$15");
console.log(fixedFirst); // Output: "... $15 for the mouse and $10 for the pad."

// Modern .replaceAll() hits every match
const fixedAll = announcement.replaceAll("$10", "$20");
console.log(fixedAll); // Output: "... $20 for the mouse and $20 for the pad."


// ==========================================
// 3. Math Engine Operations (Precision Control)
// ==========================================
const systemWeight = 7.42;

console.log(Math.round(systemWeight)); // Output: 7 (Nearest integer)
console.log(Math.ceil(systemWeight));  // Output: 8 (Rounds UP)
console.log(Math.floor(systemWeight)); // Output: 7 (Rounds DOWN)

// Secret Recipe: Generating a Random Integer between 1 and 10
// Formula: Math.floor(Math.random() * max) + min
const luckyNumber = Math.floor(Math.random() * 10) + 1;
console.log("Your Random OTP Code: " + luckyNumber);
```

- **5. Need to know**
  Strings are Immutable: জাভাস্ক্রিপ্টে স্ট্রিং মেথডগুলো (replace(), toLowerCase(), ইত্যাদি) কখনোই মূল ভেরিয়েবলের টেক্সটকে সরাসরি পরিবর্তন করে না। এরা সবসময় একটি সম্পূর্ণ নতুন স্ট্রিং কপি রিটার্ন করে। তাই আপনাকে নতুন ভ্যালুটি অন্য ভেরিয়েবলে সেভ করে নিতে হবে।

The Empty Split Trick: আপনি যদি split("") এর ভেতর কোনো স্পেস বা ডিলিমিটার না দিয়ে খালি স্ট্রিং পাস করেন, তবে জাভাস্ক্রিপ্ট ওই টেক্সটের প্রতিটি অক্ষরকে (এমনকি স্পেসগুলোকেও) আলাদা করে একটি ক্যারেক্টার অ্যারে বানিয়ে দেবে। এটি কোনো শব্দ উল্টানোর (Reverse) জন্য বহুল ব্যবহৃত ট্রিক: str.split("").reverse().join("")।

Math.random() Security Risk: মনে রাখবেন, Math.random() কিন্তু ক্রিপ্টোগ্রাফিক্যালি ১০০% সুরক্ষিত বা পিওর র‍্যান্ডম নয় (এটি একটি সিউডো-র‍্যান্ডম অ্যালগরিদম)। তাই সিকিউরিটি টোকেন, ওটিপি (OTP) বা পাসওয়ার্ড জেনারেশনের মতো সেনসিটিভ কাজে এটি ব্যবহার করা বিপজ্জনক। সেক্ষেত্রে মডার্ন ব্রাউজারের crypto.getRandomValues() ব্যবহার করা উচিত।

### • Data Parsing: JSON.stringify() & JSON.parse()

- **1. What?**
  JSON.stringify() এবং JSON.parse() হলো জাভাস্ক্রিপ্টের দুটি প্রধান ইন্টারনাল গালভানোমিটার বা ডাটা পার্সিং মেথড।

JSON.stringify() জাভাস্ক্রিপ্টের যেকোনো অবজেক্ট বা অ্যারেকে একটি ইউনিভার্সাল টেক্সট ফরম্যাটে রূপান্তর করে, যাকে আমরা বলি JSON String।

JSON.parse() ঠিক এর উল্টো কাজ করে। এটি কোনো একটি বৈধ JSON String টেক্সটকে রিসিভ করে পুনরায় জাভাস্ক্রিপ্টের জীবন্ত অবজেক্ট বা অ্যারেতে রূপান্তর করে।

- **2. Why?**
  ইন্টারনেট বা নেটওয়ার্কের ভেতর দিয়ে যখন কোনো ডেটা এক কম্পিউটার থেকে অন্য কম্পিউটারে (যেমন: ব্রাউজার থেকে ব্যাকএন্ড সার্ভারে) পাঠানো হয়, তখন জাভাস্ক্রিপ্টের কাঁচা মেমরি অবজেক্ট সরাসরি তার লাইভ স্টেট নিয়ে ট্রাভেল করতে পারে না। নেটওয়ার্ক কেবল টেক্সট বা স্ট্রিং চেনে।

তাই সার্ভারে ডেটা পাঠাতে হলে অবজেক্টকে টেক্সট বানিয়ে (JSON.stringify()) পাঠাতে হয়। আবার সার্ভার থেকে যখন সেই টেক্সট রেসপন্স ব্রাউজারে ফেরত আসে, তখন তার ওপর ডট অপারেটর বা লুপ চালানোর জন্য তাকে পুনরায় আসল অবজেক্টে রূপান্তর (JSON.parse()) করতে হয়। এছাড়া আগের চ্যাপ্টারে দেখা LocalStorage-এ ডেটা রাখার জন্যও এই পার্সিং ইঞ্জিন দুটি বাধ্যতামূলক।

- **3. How?**
  JSON.stringify(object): এটি অবজেক্টের সব চাবি (Keys) এবং মানকে (Values) ডাবল কোটেশন "" চিহ্নের ভেতরে মুড়িয়ে একটি ফ্ল্যাট স্ট্রিংয়ে রূপান্তর করে।

JSON.parse(string): এটি নিখুঁতভাবে টেক্সটের ফরম্যাট এনালাইসিস করে ব্রাউজারের র‍্যামে (RAM) একটি নতুন জাভাস্ক্রিপ্ট অবজেক্ট মেমরি এলোকেট করে দেয়।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. The Serialization Journey (Object -> String)
// ==========================================
const clientConfiguration = {
  apiToken: "secure_ax901",
  isPremium: true,
  retryCount: 3,
  allowedMethods: ["GET", "POST"]
};

// Checking data type before conversion
console.log(typeof clientConfiguration); // Output: "object"

// Converting JavaScript Object to a JSON Text String
const networkPayload = JSON.stringify(clientConfiguration);

console.log(typeof networkPayload); // Output: "string"
console.log(networkPayload);
// Output: '{"apiToken":"secure_ax901","isPremium":true,"retryCount":3,"allowedMethods":["GET","POST"]}'


// ==========================================
// 2. The Deserialization Journey (String -> Object)
// ==========================================
const incomingServerResponse = '{"status":"success","code":200,"data":[]}';

// Converting JSON Text String back to JavaScript Object
const parsedObject = JSON.parse(incomingServerResponse);

console.log(typeof parsedObject); // Output: "object"
console.log(parsedObject.status);  // Output: "success" (Dot notation works again!)


// ==========================================
// 3. High-Tier Trick: Deep Cloning with JSON Engine
// ==========================================
const originalMatrix = { a: 1, nested: { b: 2 } };

// Quick dirty way to create a 100% separate deep copy (No shared reference)
const deepCloneMatrix = JSON.parse(JSON.stringify(originalMatrix));

deepCloneMatrix.nested.b = 99;
console.log(originalMatrix.nested.b); // Output: 2 (Safely untouched!)
```

- **5. Need to know**
  The Big Crash Trap (ইন্টারভিউ স্পেশাল): JSON.parse() মেথডটি খুবই কঠোর বা সেনসিটিভ। ইনপুট দেওয়া স্ট্রিংয়ে যদি সামান্যতম ভুল থাকে (যেমন লাস্টে একটা কমা বেশি থাকা, বা সিঙ্গেল কোটেশন ব্যবহার করা), তবে সে কোনো undefined রেজাল্ট দেবে না—সরাসরি "SyntaxError: Unexpected token..." এরর দিয়ে পুরো অ্যাপ্লিকেশন ক্র্যাশ করিয়ে দেবে। তাই রিয়েল-লাইফ প্রজেক্টে একে সবসময় একটি try-catch ব্লকের ভেতর রান করা উচিত।

The Data Eraser Phenomenon: জাভাস্ক্রিপ্ট অবজেক্টের ভেতর যদি কোনো function, undefined ভ্যালু, কিংবা Symbol থাকে, তবে JSON.stringify() করার সময় সেগুলো জাদুকরী উপায়ে সম্পূর্ণ গায়েব বা ডিলিট হয়ে যায়! কারণ অফিসিয়াল JSON স্ট্যান্ডার্ডে ফাংশন বা আনডিফাইন্ডের কোনো অস্তিত্ব নেই।

JSON formatting constraints: মনে রাখবেন, JSON স্ট্রিং-এর সব চাবি (Keys) অবশ্যই ডাবল কোটেশন "" এর ভেতর থাকতে হবে। সিঙ্গেল কোটেশন '' দিলে জাভাস্ক্রিপ্ট পার্সার তাকে রিজেক্ট করবে।

## 5. Engine & System Control (Execution Layer)

- **1. What?**
  Engine & System Control (এক্সিকিউশন লেয়ার) হলো জাভাস্ক্রিপ্টের এমন একটি হাই-লেভেল কন্ট্রোল রুম, যার কাজ হলো কোড রান হওয়ার সময় তার গতিবেগ, মেমোরি কনসাম্পশন এবং এক্সিকিউশন টাইম নিখুঁতভাবে নিয়ন্ত্রণ করা। এটি মূলত ব্রাউজার ইঞ্জিনের কার্যক্ষমতাকে ম্যানেজ করে এবং বড়সড় কোনো ইভেন্ট বা কাজের চাপ সামলানোর জন্য জাভাস্ক্রিপ্টকে একচেটিয়া ক্ষমতা দেয়।

- **2. Why?**
  বাস্তব জীবনের বড় প্রজেক্টে (যেমন: সার্চ বক্সে প্রতিবার টাইপ করার সাথে সাথে এপিআই থেকে ডেটা আনা, কিংবা পেজ স্ক্রল করার সাথে সাথে অ্যানিমেশন ট্র্রিগার হওয়া)—ইউজার খুব দ্রুত কাজ করলে জাভাস্ক্রিপ্ট ব্যাকগ্রাউন্ডে প্রতি সেকেন্ডে শত শত বার একই ফাংশন রান করা শুরু করে। এতে ব্রাউজার ও সার্ভার দুটোই ওভারলোডেড হয়ে ক্র্যাশ করতে পারে। এই এক্সিকিউশন লেয়ারের মেকানিজমগুলো না জানলে আপনি কোডের পারফরম্যান্স অপ্টিমাইজ করতে পারবেন না এবং আপনার তৈরি করা অ্যাপ্লিকেশনটি ভারী বা স্লো হয়ে পড়বে।

- **3. How?**
  এই লেয়ারটি ব্রাউজারের সিপিইউ (CPU) এবং র‍্যামের (RAM) ওপর কাজের চাপ কমানোর জন্য কিছু অ্যাডভান্সড লজিক এবং মেথড ব্যবহার করে। যেমন:

Debouncing ও Throttling মেকানিজম দিয়ে ঘন ঘন ট্র্রিগার হওয়া ইভেন্ট ফাংশনগুলোকে কৃত্রিমভাবে আটকে নির্দিষ্ট সময় পর পর চালনা করা হয়।

Memory Management এবং গার্বেজ কালেকশন (Garbage Collection) দিয়ে র‍্যামের ভেতরের অপ্রয়োজনীয় ডেটা ডিলিট করে মেমরি লিক হওয়া ঠেকানো হয়।

setInterval ও clearInterval দিয়ে কোডের কোনো নির্দিষ্ট অংশকে লুপের মতো টাইম-টেবিল মেনে কন্ট্রোল করা হয়।

### • Strict Mode ('use strict') & code safety

- **1. What?**
  Strict Mode (স্ট্রিক্ট মোড) হলো জাভাস্ক্রিপ্টের একটি বিশেষ সিকিউরিটি গার্ড বা কোড সেফটি মেকানিজম। কোডের শুরুতে বা কোনো ফাংশনের ভেতরে "use strict"; স্ট্রিংটি লিখে দিলে জাভাস্ক্রিপ্ট ইঞ্জিন তার "ডিফল্ট শিথিলতা" ত্যাগ করে অত্যন্ত কঠোর ও নিয়মতান্ত্রিক নিয়মে কোড অ্যানালাইসিস শুরু করে। সাধারণ মোডে জাভাস্ক্রিপ্ট যে ভুলগুলোকে নীরবে এড়িয়ে যেত বা মাফ করে দিত, স্ট্রিক্ট মোডে সেগুলোকে সরাসরি ক্ষমার অযোগ্য অপরাধ বা এরর (Error) হিসেবে গণ্য করা হয়।

- **2. Why?**
  জাভাস্ক্রিপ্ট তৈরি করার সময় এর ডিজাইনে বেশ কিছু লুজ-এন্ডস বা ত্রুটি ছিল, যার কারণে ভুল বানানে ভেরিয়েবল লিখলেও কোড রান হয়ে যেত এবং ব্যাকগ্রাউন্ডে গ্লোবাল ভেরিয়েবল তৈরি হয়ে মেমোরি নোংরা করত। প্রজেক্ট বড় হলে এই ছোট ছোট ভুলগুলো ট্র্যাক করা অসম্ভব হয়ে পড়ে এবং সিকিউরিটি লিক তৈরি হয়। স্ট্রিক্ট মোড অন করলে:

কোডের ভেতরের সাইলেন্ট এররগুলো (Silent Errors) কনসোলে সরাসরি ক্র্যাশ বা এরর হিসেবে ধরা পড়ে।

জাভাস্ক্রিপ্ট ইঞ্জিনের জন্য কোড অপ্টিমাইজ করা সহজ হয়, ফলে কোড আগের চেয়ে দ্রুত রান (Fast Execution) হয়।

ভবিষ্যতের মডার্ন জাভাস্ক্রিপ্ট সংস্করণের (ECMAScript) জন্য নিষিদ্ধ বা সংরক্ষিত কিছু শব্দ ব্যবহার করা লক করে কোডকে ফিউচার-প্রুফ করে।

- **3. How?**
  ফাইলের একদম প্রথম লাইনে (অথবা কোনো নির্দিষ্ট ফাংশনের প্রথম লাইনে) ওপরে কোটেশন চিহ্নের ভেতরে "use strict"; লিখে দিতে হয়। এটি অন করার পর জাভাস্ক্রিপ্ট ইঞ্জিন নিম্নলিখিত কঠোর নিয়মগুলো অ্যাপ্লাই করে:

No Undeclared Variables: let, const, বা var ছাড়া কোনো ভেরিয়েবল ডিক্লেয়ার করলে সরাসরি রিজেক্ট করবে।

No Duplicate Parameters: কোনো ফাংশনের প্যারামিটারে একই নাম দুইবার লিখলে (function test(a, a)) এরর দেবে।

Protected this keyword: গ্লোবাল স্কোপে সাধারণ ফাংশনের ভেতর this-এর মান ডিফল্টভাবে window অবজেক্ট না হয়ে সরাসরি undefined হয়ে যায়, যা সিকিউরিটির জন্য অত্যন্ত নিরাপদ।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Silent Failures in Normal Mode vs Strict Mode
// ==========================================

// Without Strict Mode: JavaScript automatically creates a global variable 'y'
function carelessCode() {
  y = 200; // No error! Spills into global scope
}
carelessCode();


// Enforcing Strict Mode
"use strict";

function secureCode() {
  try {
    // Attempting to assign value to an undeclared variable
    userName = "Sabbir";
  } catch (error) {
    console.error("⚠️ Safety Interception: Variable assignment failed.");
    console.error("Reason:", error.message);
    // Output: ReferenceError: userName is not defined
  }
}
secureCode();


// ==========================================
// 2. Strict Protection on Read-Only Properties
// ==========================================
const secureObject = {};
Object.defineProperty(secureObject, "readOnlyKey", { value: 42, writable: false });

try {
  // Trying to overwrite a protected read-only property
  secureObject.readOnlyKey = 99;
} catch (error) {
  console.error("\n⚠️ Safety Interception: Cannot modify protected data.");
  console.error("Reason:", error.message);
  // Output: TypeError: Cannot assign to read only property 'readOnlyKey'
}


// ==========================================
// 3. Eliminating Duplicate Parameters
// ==========================================
try {
  // This syntax will immediately cause a parsing crash under Strict Mode
  // function calculateVolume(width, width, height) { ... }
} catch (error) {
  // SyntaxError: Duplicate parameter name not allowed in this context
}
```

- **5. Need to know**
  The Global vs Local Scope: আপনি যদি ফাইলের একদম ওপরে "use strict"; লেখেন, তবে পুরো ফাইলের সব কোড স্ট্রিক্ট মোডে চলবে। কিন্তু আপনি যদি চান আপনার পুরো প্রজেক্টের শুধু একটি নির্দিষ্ট ফাংশন কঠোর নিয়মে চলুক, তবে শুধু সেই ফাংশনের বডির প্রথম লাইনে এটি লিখতে পারেন।

Modern Modules are Strict by Default: মডার্ন জাভাস্ক্রিপ্টে আপনি যখন ES Modules (import/export সিনট্যাক্স) বা ক্লাসেস (class) ব্যবহার করবেন, তখন জাভাস্ক্রিপ্ট ব্যাকগ্রাউন্ডে অটোমেটিক স্ট্রিক্ট মোড অন করে দেয়। সেখানে আপনাকে আলাদা করে আর কষ্ট করে "use strict"; লিখতে হয় না।

The Octal Literal Ban: স্ট্রিক্ট মোডে কোনো সংখ্যার শুরুতে শূন্য দিয়ে অক্টাল নাম্বার (let num = 010;) ডিক্লেয়ার করা সম্পূর্ণ নিষিদ্ধ। কারণ এটি ডেভেলপারদের অজান্তেই কনসোলে হিসাবের ওলটপালট ঘটিয়ে মারাত্মক লজিক্যাল বা্গের জন্ম দিত।

### • Value vs Reference (Shallow Copy vs Deep Copy)

- **1. What?**
  জাভাস্ক্রিপ্ট মেমোরিতে ডেটা কীভাবে স্টোর এবং অ্যাসাইন হয়, তার ওপর ভিত্তি করে একে ২ ভাগে ভাগ করা যায়:

Pass by Value (আদিম বা Primitive ডেটা): String, Number, Boolean, null, undefined ইত্যাদি ভেরিয়েবলে সরাসরি আসল মান (Value) স্টোর করে।

Pass by Reference (Structural ডেটা): Array এবং Object মেমোরিতে সরাসরি ডেটা না রেখে, র‍্যামের (RAM) একটি নির্দিষ্ট ঠিকানার লিঙ্ক বা রেফারেন্স (Reference) স্টোর করে।

কোড ডুপ্লিকেট করার সময় এই মেকানিজম থেকে ২ ধরণের কপি তৈরি হয়:

Shallow Copy (অগভীর কপি): মূল অবজেক্টের উপরের লেভেলটা ক্লোন করলেও, ভেতরের নেস্টেড অবজেক্টগুলোর আসল রেফারেন্স বা লিঙ্ক ভাঙতে পারে না।

Deep Copy (গভীর কপি): মূল অবজেক্ট এবং তার ভেতরে থাকা সমস্ত নেস্টেড অবজেক্টের লিঙ্ক সম্পূর্ণ ছিঁড়ে মেমোরিতে একদম স্বাধীন, নতুন একটি ক্লোন তৈরি করে।

- **2. Why?**
  জাভাস্ক্রিপ্ট প্রোজেক্টে সবচেয়ে বেশি আকস্মিক বা ট্রিকি বাগ আসে এই রেফারেন্স ট্র্যাপ থেকে। ধরুন আপনার কাছে একটি ইউজার অবজেক্ট আছে। আপনি চাচ্ছেন তার একটি কপি তৈরি করে কপির ভেতরে বয়স বা নাম চেঞ্জ করতে। কিন্তু আপনি যদি সাধারণ = চিহ্ন দিয়ে বা ভুল মেথড দিয়ে কপি করেন, তবে দেখা যাবে কপির সাথে সাথে আপনার ডাটাবেজ থেকে আসা মূল (Original) অবজেক্টের ডেটাও চেঞ্জ হয়ে গেছে! এই মারাত্মক মেমোরি ওভাররাইট ঠেকানোর জন্য এবং স্টেট ম্যানেজমেন্ট (যেমন React/Redux-এ Immutability) ঠিক রাখার জন্য এই মেকানিজমটি জানা লাইফ-সেভার।

- **3. How?**
  The Reference Link: যখন আপনি লিখবেন const clone = originalObj, জাভাস্ক্রিপ্ট কোনো নতুন অবজেক্ট বানায় না। সে কেবল আগের অবজেক্টের ঘরের চাবিটা নতুন ভেরিয়েবলের হাতে তুলে দেয়। ফলে যেকোনো একটাতে চেঞ্জ করলে দুটোর ওপরেই প্রভাব পড়ে।

Shallow Copy Techniques: Spread Operator (...)) কিংবা Object.assign() দিয়ে শ্যালো কপি করা হয়। এরা প্রথম লেভেলের ভ্যালুগুলোকে নতুন ঘর দিলেও, ভেতরে যদি আরেকটি অবজেক্ট {} বা অ্যারে [] থাকে, তবে সেটির লিঙ্ক আগেরটাই থেকে যায়।

Deep Copy Techniques: মডার্ন জাভাস্ক্রিপ্টের বিল্ট-ইন structuredClone() মেথড দিয়ে অথবা প্রাচীন JSON.parse(JSON.stringify()) ইঞ্জিন দিয়ে ডিপ কপি করা হয়, যা সব লেভেলের চেইন ভেঙে দেয়।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Primitive Value vs Object Reference
// ==========================================
let scoreA = 100;
let scoreB = scoreA; // Copying the actual value
scoreB = 200;
console.log(scoreA); // Output: 100 (Safely untouched)

const userA = { name: "Sabbir" };
const userB = userA; // Copying the Memory Reference Address
userB.name = "Rakib";
console.log(userA.name); // Output: "Rakib" (Oops! Original destroyed!)


// ==========================================
// 2. The Shallow Copy Illusion (...)
// ==========================================
const originalProfile = {
  title: "Engineer",
  skills: { primary: "JavaScript" } // Nested Object
};

// Performing a Shallow Copy using Spread
const shallowClone = { ...originalProfile };

shallowClone.title = "Lead Engineer"; // Level 1 modification
shallowClone.skills.primary = "Next.js";   // Nested Level 2 modification

console.log(originalProfile.title);          // Output: "Engineer" (Level 1 stayed safe!)
console.log(originalProfile.skills.primary); // Output: "Next.js" ❌ (Nested Level 2 mutated!)


// ==========================================
// 3. The Ultimate Rescue: Deep Copy
// ==========================================
const secureProfile = {
  title: "Engineer",
  skills: { primary: "JavaScript" }
};

// Performing a 100% Perfect Deep Copy
const deepClone = structuredClone(secureProfile);

deepClone.skills.primary = "Rust";

console.log(secureProfile.skills.primary); // Output: "JavaScript" ✓ (100% Secure!)
console.log(deepClone.skills.primary);     // Output: "Rust"
```

- **5. Need to know**
  The structuredClone() Superpower: মডার্ন জাভাস্ক্রিপ্টে ডিপ কপি করার জন্য structuredClone() হলো অফিশিয়াল স্ট্যান্ডার্ড। এটি আসার আগে সবাই JSON.parse(JSON.stringify()) ব্যবহার করত। কিন্তু জেএসন (JSON) ইঞ্জিনের সমস্যা হলো, অবজেক্টের ভেতর কোনো Date, RegExp বা Function থাকলে সেগুলোকে সে ডিলিট বা নষ্ট করে ফেলে। structuredClone() এগুলোকে নিখুঁতভাবে হ্যান্ডেল করতে পারে।

Performance Warning: ডিপ কপি করার সময় ব্রাউজার পুরো অবজেক্ট ট্রি-কে রিকার্সিভলি (পুনরাবৃত্তভাবে) স্ক্যান করে মেমোরিতে নতুন জায়গা বরাদ্দ করে। তাই অত্যন্ত বড় বা জাবদা সাইজের ডেটা অবজেক্টের ওপর ঘন ঘন ডিপ কপি চালালে অ্যাপ্লিকেশনের পারফরম্যান্স স্লো হয়ে যেতে পারে।

Comparison Trap: দুটি ভিন্ন অবজেক্টের ভেতরের ডেটা হুবহু এক হলেও জাভাস্ক্রিপ্ট তাদের === দিয়ে তুলনা করলে সবসময় false দেবে। কারণ জাভাস্ক্রিপ্ট ডেটা দেখে না, দেখে মেমোরির অ্যাড্রেস। দুটি ভিন্ন অবজেক্ট মানে মেমোরিতে দুটি ভিন্ন বাড়ি।

### • Module System: CommonJS (require) vs ES Modules (import/export)

- **1. What?**
  Module System (মডিউল সিস্টেম) হলো একটি বড় জাভাস্ক্রিপ্ট কোডবেসকে ছোট ছোট, স্বাধীন এবং রি-ইউজেবল ফাইলে ভাগ করে ম্যানেজ করার আর্কিটেকচার। জাভাস্ক্রিপ্টের ইতিহাসে মূলত দুটি প্রধান মডিউল সিস্টেম রাজত্ব করছে:

CommonJS (CJS): নোড জেএস (Node.js) এর ঐতিহ্যগত বা ডিফল্ট মডিউল সিস্টেম, যা কোড ইম্পোর্ট করার জন্য require() এবং এক্সপোর্ট করার জন্য module.exports ব্যবহার করে।

ES Modules (ESM): জাভাস্ক্রিপ্টের অফিশিয়াল এবং মডার্ন স্ট্যান্ডার্ড (ES6 থেকে আগত), যা ব্রাউজার এবং মডার্ন নোড জেএস উভয় জায়গাতেই চলে। এটি কোড ইম্পোর্টের জন্য import এবং এক্সপোর্টের জন্য export কিওয়ার্ড ব্যবহার করে।

- **2. Why?**
  আগেকার দিনে জাভাস্ক্রিপ্টে মডিউল সিস্টেম না থাকায় সব কোড একটিমাত্র বিশাল ফাইলে বা গ্লোবাল স্কোপে লিখতে হতো, যার ফলে ভেরিয়েবলের নাম এক হয়ে গিয়ে কোড ক্র্যাশ করত। মডিউল সিস্টেম ফাইলগুলোকে নিজস্ব স্কোপে লক করে দেয়।

আজকের দিনে CommonJS এবং ESM-এর পার্থক্য জানা অত্যন্ত জরুরি, কারণ আপনি যখন ফ্রন্টএন্ডে রিয়্যাক্ট বা নেক্সট জেএস (Next.js) নিয়ে কাজ করবেন, তখন সবকিছু import/export দিয়ে চলবে। আবার ব্যাকএন্ডে পুরোনো নোড প্রজেক্টে কাজ করতে গেলে হঠাৎ require() দেখতে পাবেন। এই দুই সিস্টেমের মেকানিজম না বুঝলে প্রজেক্ট কনফিগারেশনে হরহামেশাই এরর খাবেন।

- **3. How?**
  CommonJS (Synchronous Load): এটি রান-টাইমে (কোড চলাকালীন) কাজ করে এবং ফাইলগুলোকে এক এক করে সিনক্রোনাসলি (Synchronous) লোড করে। অর্থাৎ, ফাইল পুরোপুরি রিড না হওয়া পর্যন্ত পরের কোড এক্সিকিউট হয় না।

ES Modules (Static / Asynchronous Load): এটি কোড রান হওয়ার আগেই অর্থাৎ কম্পাইল বা পার্স টাইমে (Static Analysis) ফাইলের ডিপেনডেন্সি ট্রি তৈরি করে ফেলে এবং ফাইলগুলো অ্যাসিনক্রোনাসলি ব্যাকগ্রাউন্ডে লোড হতে পারে। এর ফলে মডার্ন বান্ডলারগুলো (যেমন Webpack, Vite) অপ্রয়োজনীয় বা অব্যবহৃত কোড স্বয়ংক্রিয়ভাবে ছেঁটে ফেলতে পারে, যাকে বলা হয় Tree Shaking।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. The CommonJS (CJS) Standard - Node.js Default
// ==========================================

// --- mathService.js (Exporting) ---
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;

module.exports = { add, subtract };

// --- app.js (Importing) ---
const { add, subtract } = require("./mathService.js");
console.log(add(10, 5)); // Output: 15


// ==========================================
// 2. The ES Modules (ESM) Standard - Modern Official
// ==========================================

// --- cryptoService.js (Exporting) ---
export const hashData = (data) => `hash_${data}`;
export default function encrypt(text) {
  return `secure_${text}`;
}

// --- main.js (Importing) ---
// Named imports inside { } and default import outside!
import encrypt, { hashData } from "./cryptoService.js";

console.log(hashData("hello")); // Output: hash_hello
console.log(encrypt("password")); // Output: secure_password
```

- **5. Need to know**
  The Interoperability Trap: আপনি চাইলেই একটি সাধারণ নোড জেএস ফাইলে সরাসরি import লিখতে পারবেন না। নোড জেএস-এ ESM ব্যবহার করতে চাইলে আপনার package.json ফাইলে "type": "module" যুক্ত করতে হবে অথবা ফাইলের এক্সটেনশন .js থেকে বদলে .mjs করতে হবে।

Dynamic Imports: CommonJS-এ if-else কন্ডিশনের ভেতরে ঢুকে কোনো ফাইল ডাইনামিকালি লোড করা খুব সহজ ছিল (যেমন: if(user.isAdmin) require('admin.js'))। ESM স্ট্যাটিক হওয়ায় শুরুতে এটি করা যেত না, তবে মডার্ন জাভাস্ক্রিপ্টে এখন ফাংশনের মতো করে import('./admin.js') ব্যবহার করে ডাইনামিক লোড করা যায়, যা একটি প্রমিজ (Promise) রিটার্ন করে।

The Special Missing Variables: যখনই আপনি নোড জেএস-কে ESM মোডে চালাবেন, তখন নোডের অত্যন্ত জনপ্রিয় দুটি গ্লোবাল ভেরিয়েবল **dirname (কারেন্ট ফোল্ডার পাথ) এবং **filename (কারেন্ট ফাইল পাথ) আর কাজ করবে না, সরাসরি এরর দেবে। ESM-এ এই পাথ বের করতে হলে import.meta.url ইঞ্জিন ব্যবহার করতে হয়।

### • Prototypes, Prototypal Inheritance & JavaScript Classes (OOP)

- **1. What?**
  জাভাস্ক্রিপ্টে Object-Oriented Programming (OOP) মূলত অন্য সব ট্র্যাডিশনাল ল্যাঙ্গুয়েজের (যেমন Java বা C++) মতো নয়। এর নিজস্ব একটি মেকানিজম আছে:

Prototype (প্রোটোটাইপ): জাভাস্ক্রিপ্টের প্রতিটি অবজেক্টের সাথে ব্যাকগ্রাউন্ডে একটি অদৃশ্য অবজেক্ট যুক্ত থাকে, যাকে প্রোটোটাইপ বলা হয়। এটি মূলত একটি শেয়ার্ড প্রপার্টি বা মেথডের ভল্ট (Vault)।

Prototypal Inheritance: যখন একটি অবজেক্ট অন্য একটি অবজেক্টের মেথড বা ক্ষমতা উত্তরাধিকার সূত্রে বা লিংক আকারে নিজের ভেতর অ্যাক্সেস করতে পারে, তাকে প্রোটোটাইপাল ইনহেরিটেন্স বলে।

JavaScript Classes: এটি কোনো আসল ক্লাস নয়, বরং প্রোটোটাইপের জটিল কোডকে সাধারণ মানুষের লেখার উপযোগী করার জন্য একটি সুন্দর প্রলেপ বা Syntactic Sugar।

- **2. Why?**
  ধরুন আপনার অ্যাপ্লিকেশনে ১ লক্ষ ইউজার অবজেক্ট তৈরি করতে হবে। প্রতিটি ইউজারের যদি নিজস্ব একটি login() ফাংশন থাকে, তবে মেমরিতে ১ লক্ষ বার একই ফাংশন ডুপ্লিকেট হয়ে ব্রাউজারের র‍্যাম (RAM) ক্র্যাশ করিয়ে দেবে।

প্রোটোটাইপ মেকানিজম ব্যবহার করলে সেই login() ফাংশনটি মেমরিতে মাত্র একবার তৈরি হবে এবং ১ লক্ষ ইউজারই একই মেমরি লিঙ্ক শেয়ার করে কাজ চালাতে পারবে। এটি জাভাস্ক্রিপ্ট অ্যাপ্লিকেশনের পারফরম্যান্স এবং মেমরি অপ্টিমাইজেশনের মূল চাবিকাঠি।

- **3. How?**
  The Prototype Chain: আপনি যখন কোনো অবজেক্টের প্রপার্টি খোঁজেন (যেমন user.login()), জাভাস্ক্রিপ্ট প্রথমে অবজেক্টের নিজের বডিতে সেটি খোঁজে। সেখানে না পেলে সে তার অদৃশ্য লিঙ্কের মাধ্যমে প্যারেন্ট **proto** (Prototype)-এ গিয়ে খোঁজে। এভাবে খুঁজতে খুঁজতে সে একদম টপ-লেভেল গ্লোবাল Object.prototype-এ পৌঁছায়। একেই বলে Prototype Chain।

Class Architecture: class কিওয়ার্ড দিয়ে আমরা কনস্ট্রাক্টর, মেথড এবং ইনহেরিটেন্স (extends) খুব সহজে ডিজাইন করতে পারি, যা পর্দার আড়ালে গিয়ে অটোমেটিক প্রোটোটাইপ চেইন তৈরি করে ফেলে।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Raw Prototypal Inheritance Under the Hood
// ==========================================

const humanMechanicalModel = {
  isAlive: true,
  walk: function() {
    console.log("🚶 Walking on two legs...");
  }
};

// Creating a new object that links directly to humanMechanicalModel as its prototype
const softwareEngineer = Object.create(humanMechanicalModel);

softwareEngineer.code = function() {
  console.log("💻 Writing clean JavaScript code...");
};

// Accessing own property
softwareEngineer.code(); // Output: Writing clean JavaScript code...

// Accessing prototype property via the Prototype Chain
softwareEngineer.walk(); // Output: 🚶 Walking on two legs...
console.log(softwareEngineer.isAlive); // Output: true


// ==========================================
// 2. Modern Standard: JavaScript Classes (OOP Syntactic Sugar)
// ==========================================

class Employee {
  // Constructor initiates object state
  constructor(name, salary) {
    this.name = name;
    this.salary = salary;
  }

  // This method automatically goes into the shared Prototype Memory!
  getDetails() {
    return `👤 Name: ${this.name} | Salary: $${this.salary}`;
  }
}

// Sub-class (Inheritance)
class Developer extends Employee {
  constructor(name, salary, primaryLanguage) {
    super(name, salary); // Calling the parent constructor
    this.primaryLanguage = primaryLanguage;
  }

  // Overriding/Extending capability
  introduce() {
    console.log(`${super.getDetails()} | Tech Stack: ${this.primaryLanguage}`);
  }
}

// Generating Live Object Instances
const dev1 = new Developer("Sabbir", 5000, "JavaScript");
const dev2 = new Developer("Rakib", 4500, "Rust");

dev1.introduce(); // Output: 👤 Name: Sabbir | Salary: $5000 | Tech Stack: JavaScript
dev2.introduce(); // Output: 👤 Name: Rakib | Salary: $4500 | Tech Stack: Rust

// Verification: Are methods actually duplicated in memory?
console.log(dev1.getDetails === dev2.getDetails); // Output: true ✓ (Shared single memory instance!)
```

- **5. Need to know**
  **proto** vs prototype Trap (ইন্টারভিউ ফ্লেভার): এটি জুনিয়র ডেভেলপারদের সবচেয়ে বড় কনফিউশন। **proto** হলো প্রতিটি তৈরি হওয়া লাইভ অবজেক্টের ভেতরের আসল পয়েন্টার বা লিঙ্ক যা তার প্যারেন্টকে নির্দেশ করে। আর ব্লুপ্রিন্ট বা মেকার ফাংশনের (যেমন Developer.prototype) ভেতরের অবজেক্ট হলো সেই জায়গা যেখানে নতুন তৈরি হওয়া বাচ্ছাদের মেথডগুলো জমা রাখা হয়।

The Shadowing Property: যদি প্রোটোটাইপ চেইনের প্যারেন্ট অবজেক্টে একটি মেথড থাকে (যেমন walk), এবং আপনি চাইল্ড অবজেক্টের ভেতরেও একই নামের আরেকটি মেথড (walk) তৈরি করেন, তবে চাইল্ডের মেথডটি প্যারেন্টের মেথডকে ঢেকে ফেলবে বা ওভাররাইড করবে। একে বলে প্রপার্টি শ্যাডোয়িং (Property Shadowing)।

Don't Mutate Native Prototypes: আপনি চাইলেই গ্লোবাল Array.prototype বা Object.prototype-এর ভেতরে নিজের কাস্টম মেথড ঢুকিয়ে জাভাস্ক্রিপ্ট ল্যাঙ্গুয়েজের মূল আচরণ পরিবর্তন করে দিতে পারেন। তবে রিয়েল-লাইফ প্রজেক্টে এটি করা মারাত্মক অপরাধ এবং নিষিদ্ধ (একে বলে Monkey Patching), কারণ এটি থার্ড-পার্টি লাইব্রেরির সাথে সাংঘর্ষিক হয়ে পুরো অ্যাপ ক্র্যাশ করাতে পারে।

### • Execution Context & 'this' keyword mechanics

🌊 Chapter 7: Execution Context & 'this' keyword mechanics

- **1. What?**
  জাভাস্ক্রিপ্ট ইঞ্জিন কীভাবে আপনার কোড রান করবে এবং কোনটার পর কোনটা এক্সিকিউট করবে, তার পুরো ব্যাকগ্রাউন্ড মেকানিজম বা পরিবেশকে বলা হয় Execution Context (এক্সিকিউশন কনটেক্সট)।
  অন্যদিকে, this হলো জাভাস্ক্রিপ্টের একটি বিশেষ ডাইনামিক কিওয়ার্ড বা পয়েন্টার, যা বর্তমান কোডটি মেমোরির ঠিক কোন অবজেক্ট বা কনটেক্সটের অধীনে রান হচ্ছে—তাকে নির্দেশ করে।

- **2. Why?**
  একই ফাংশনের ভেতরে this কিওয়ার্ডটি লিখলেও, ফাংশনটি কীভাবে এবং কোথা থেকে কল করা হচ্ছে তার ওপর ভিত্তি করে this-এর মান জাদুকরী উপায়ে বদলে যায়। কখনো এটি পুরো ব্রাউজার উইন্ডো (window), কখনো কোনো নির্দিষ্ট অবজেক্ট, আবার কখনো undefined দেখায়। এই মেকানিজমটি পরিষ্কার না জানলে মডার্ন অবজেক্ট-ওরিয়েন্টেড কোড বা ইভেন্ট হ্যান্ডলার লিখতে গেলে this হারিয়ে বা বদলে গিয়ে অদ্ভুত সব বাগের জন্ম দেয়।

- **3. How?**
  The Execution Context Engine: জাভাস্ক্রিপ্ট ফাইল রান হওয়ামাত্রই প্রথমে একটি Global Execution Context তৈরি হয়। এরপর প্রতিবার কোনো ফাংশন কল হলে তার জন্য একটি করে নতুন Function Execution Context তৈরি হয়ে মেমোরির Call Stack-এ জমা হয়। এই কনটেক্সটের দুটি প্রধান ধাপ থাকে:

Creation Phase: হোইস্টিং মেকানিজমে ভেরিয়েবল ও ফাংশনের মেমোরি এলোকেশন করা এবং this এর মান নির্ধারণ করা।

Execution Phase: কোড লাইনে লাইনে রান করা।

The Dynamic this Rules: this-এর মান সম্পূর্ণভাবে নির্ভর করে Call-site বা ফাংশনটি কীভাবে ডাকা হয়েছে তার ওপর:

Implicit Binding: অবজেক্টের ডট দিয়ে কল করলে ডটের বামপাশের অবজেক্টটিই হবে this (যেমন user.show())।

Explicit Binding: .call(), .apply(), অথবা .bind() মেথড ব্যবহার করে জোরপূর্বক this-এর মান নিজের মতো সেট করে দেওয়া যায়।

Arrow Functions Exception: অ্যারো ফাংশনের নিজস্ব কোনো this নেই। সে তার চারপাশের বা প্যারেন্ট স্কোপের (Lexical Scope) this-কে ধার করে নিয়ে কাজ চালায়।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. Implicit Binding (The Dot Rule)
// ==========================================
const commandCenter = {
  station: "Alpha-9",
  launch: function() {
    // 'this' points to commandCenter because it's left of the dot
    console.log(`🚀 Missile launched from station: ${this.station}`);
  }
};

commandCenter.launch(); // Output: ... station: Alpha-9


// ==========================================
// 2. The Lost 'this' & Explicit Binding (call, apply, bind)
// ==========================================
const externalRoutine = commandCenter.launch;

// Running standalone loses the context! 'this' becomes undefined or window
// externalRoutine(); // Output: ... station: undefined

const backupStation = { station: "Omega-X" };

// Forcefully shifting 'this' context using .call()
externalRoutine.call(backupStation); // Output: ... station: Omega-X

// Custom binding creating a permanent copy
const fixedLaunch = externalRoutine.bind(commandCenter);
fixedLaunch(); // Output: ... station: Alpha-9


// ==========================================
// 3. Arrow Function Lexical Behavior
// ==========================================
const smartDrone = {
  id: "DRONE-01",
  scan: function() {
    console.log(`📡 ${this.id} starting scan...`);

    // Traditional setTimeout loses 'this', but ARROW FUNCTION retains it lexically!
    setTimeout(() => {
      console.log(`🎯 Scan locked by: ${this.id}`);
    }, 500);
  }
};

smartDrone.scan();
// Output after 500ms: 🎯 Scan locked by: DRONE-01
```

- **5. Need to know**
  call() vs apply() vs bind(): ইন্টারভিউতে এদের পার্থক্য জানতে চাওয়া খুবই সাধারণ ঘটনা। .call() এবং .apply() দুটোই ফাংশনকে সাথে সাথে রান করিয়ে দেয়, তবে .call() অতিরিক্ত আর্গুমেন্ট নেয় কমা দিয়ে কমা দিয়ে (func.call(obj, arg1, arg2)) আর .apply() আর্গুমেন্ট নেয় একটি অ্যারে হিসেবে (func.apply(obj, [arg1, arg2]))। অন্যদিকে, .bind() ফাংশন সাথে সাথে রান করে না, সে ফিউচারে ব্যবহার করার জন্য একটি পার্মানেন্টলি কনটেক্সট-লকড নতুন ফাংশন কপি রিটার্ন করে।

The Global Trap & Strict Mode: আপনি যদি কোনো সাধারণ ফাংশনের ভেতর this লিখে তাকে গ্লোবালি কল করেন, তবে সাধারণ মোডে this দেখাবে গ্লোবাল window অবজেক্ট। কিন্তু কোডে যদি "use strict"; অন থাকে, তবে জাভাস্ক্রিপ্ট সিকিউরিটির স্বার্থে this-এর মান সরাসরি undefined করে দেবে।

Arrow Functions Cannot Be Constructors: যেহেতু অ্যারো ফাংশনের নিজস্ব কোনো this মেকানিজম বা প্রোটোটাইপ নেই, তাই এদের আগে কখনো new কিওয়ার্ড বসিয়ে অবজেক্ট তৈরি করা যায় না। করতে গেলে ব্রাউজার সরাসরি TypeError: ... is not a constructor এরর ছুঁড়ে মারবে।

### • Advanced Event Loop: Microtask Queue vs Macrotask Queue (Promises বনাম setTimeout-এর ভেতর কার পাওয়ার বেশি তা বোঝার জন্য)

- **1. What?**
  জাভাস্ক্রিপ্ট একটি Single-threaded ল্যাঙ্গুয়েজ, অর্থাৎ সে একবারে একটির বেশি কাজ করতে পারে না। কিন্তু ব্রাউজারের কল স্ট্যাক (Call Stack) যাতে ব্লক না হয়, সেজন্য সে ব্রাউজার ইঞ্জিনের (যেমন V8 Engine) সাহায্য নিয়ে কিছু কাজ ব্যাকগ্রাউন্ডে সরিয়ে রাখে। এই সিস্টেমটিই হলো Event Loop।

ইভেন্ট লুপের ভেতরে দুটি প্রধান কিউ (Queue) আছে:

Microtask Queue: এটি ভিআইপি (VIP) বা হাই-প্রায়োরিটি কিউ। সব ধরণের Promise রেজল্যুশন এবং async/await এখানে আসে।

Macrotask Queue (Callback Queue): এটি নরমাল প্রায়োরিটি কিউ। setTimeout, setInterval, এবং I/O অপারেশনগুলো এখানে এসে জমা হয়।

- **2. Why?**
  ইন্টারভিউতে বা রিয়েল প্রজেক্টে বাগ ফিক্সিং-এর সময় এটি বোঝা খুব জরুরি যে, কেন একই সময়ে setTimeout এবং Promise কল করা হলে Promise আগে রেজাল্ট দেয়। যদি আপনি বুঝতে না পারেন কোন টাস্ক আগে এক্সিকিউট হবে, তবে অ্যাপ্লিকেশনের স্টেট ম্যানেজমেন্ট বা এপিআই ডেটা হ্যান্ডলিং-এ আপনি মারাত্মক লজিক্যাল বা্গের মুখোমুখি হবেন।

- **3. How? (The Golden Rules)**
  ইভেন্ট লুপের নিয়মটি খুব সহজ কিন্তু শক্তিশালী:

Call Stack: প্রথমে মেইন স্ক্রিপ্টের সব কাজ শেষ করবে।

Microtask Priority: কল স্ট্যাক ফাঁকা হওয়ার সাথে সাথে ইভেন্ট লুপ সবার আগে Microtask Queue-এর সমস্ত কাজ (যেমন সব প্রমিজ) শেষ করবে। Microtask Queue যতক্ষণ না সম্পূর্ণ খালি হচ্ছে, ততক্ষণ সে Macrotask Queue-এর দিকে তাকাবেও না।

Macrotask Execution: Microtask Queue একদম ফাঁকা হয়ে যাওয়ার পর, ইভেন্ট লুপ Macrotask Queue থেকে শুধুমাত্র একটি (একটি মাত্র!) কাজ নিয়ে এসে কল স্ট্যাকে রান করবে। এরপর আবার চেক করবে Microtask Queue-তে কিছু জমেছে কি না।

- **4. Code Example**

```JavaScript
// ==========================================
// The Ultimate Event Loop Challenge
// ==========================================

console.log("1. Start"); // 1. Synchronous (Immediate)

setTimeout(() => {
  console.log("2. setTimeout (Macrotask)"); // 4. Macrotask (Lowest Priority)
}, 0);

Promise.resolve().then(() => {
  console.log("3. Promise (Microtask)"); // 3. Microtask (High Priority)
});

console.log("4. End"); // 2. Synchronous (Immediate)

/*
OUTPUT SEQUENCE:
1. Start
2. End
3. 3. Promise (Microtask)
4. 2. setTimeout (Macrotask)
*/
```

- **5. Need to know**
  The Microtask Starvation Trap: এটি খুবই গুরুত্বপূর্ণ একটি কনসেপ্ট। যদি আপনার Microtask Queue-র ভেতর আবার নতুন নতুন প্রমিজ তৈরি হতেই থাকে (অর্থাৎ লুপের মতো), তবে ইভেন্ট লুপ কখনোই Macrotask Queue-এর দিকে যেতে পারবে না। একে বলে "Microtask Starvation"। এর ফলে আপনার ব্রাউজারের স্ক্রিন বা ইউজার ইন্টারফেস (UI) রেন্ডারিং সম্পূর্ণ ফ্রিজ হয়ে যাবে এবং ইউজার ক্লিক বা স্ক্রল কিছুই করতে পারবে না।

setTimeout Delay Myth: setTimeout এ যদি 0ms টাইমও সেট করে দেন, তবুও সে সাথে সাথে রান হবে না। সে Macrotask Queue-তে গিয়ে অপেক্ষা করবে যতক্ষণ না বর্তমান কল স্ট্যাক এবং মাইক্রো-টাস্কগুলো শেষ হয়।

async/await Mechanics: await কিওয়ার্ডের পরের কোডগুলো পর্দার আড়ালে আসলে একটি Promise.then() এর ভেতরে ঢুকে যায়, তাই এগুলো সবসময় মাইক্রো-টাস্ক কিউতে জায়গা পায়।

### • Garbage Collection & Memory Leaks

🌊 Chapter 8: Garbage Collection & Memory Leaks

- **1. What?**
  Garbage Collection (GC) হলো জাভাস্ক্রিপ্ট ইঞ্জিনের (যেমন V8) একটি অটোমেটেড ক্লিনআপ বা ঝাড়ুদার সিস্টেম। এর কাজ হলো মেমরিতে (RAM) থাকা এমন সব অবজেক্ট বা ডেটা খুঁজে বের করা, যা বর্তমানে অ্যাপ্লিকেশনের কোনো কাজে আসছে না এবং সেগুলোকে ডিলিট করে মেমরি খালি করা।

আর Memory Leak হলো সেই অবস্থা যখন কোনো ডেটা বা অবজেক্ট আর দরকার নেই, কিন্তু ভুল কোডিংয়ের কারণে জাভাস্ক্রিপ্ট ইঞ্জিন মনে করে সেটি এখনো প্রয়োজন, তাই সেটিকে সে ডিলিট করতে পারে না। ফলে মেমোরি ধীরে ধীরে পূর্ণ হতে থাকে এবং একসময় অ্যাপ্লিকেশন স্লো হয়ে যায় বা ক্র্যাশ করে।

- **2. Why?**
  জাভাস্ক্রিপ্ট ডেভেলপারদের ম্যানুয়ালি মেমোরি ম্যানেজ করতে হয় না (অন্য অনেক ল্যাঙ্গুয়েজের মতো), তাই আমরা অনেক সময় কেয়ারলেস কোড লিখি। কিন্তু বড় প্রজেক্টে যদি আপনি ভুলবশত গ্লোবাল ভেরিয়েবলে ডেটা জমিয়ে রাখেন, তবে ব্রাউজার ট্যাবটি ইউজ করার সাথে সাথে স্লো হতে থাকবে। প্রফেশনাল লেভেলে কোড লিখতে গেলে মেমরি ম্যানেজমেন্ট বোঝাটা হলো একজন দক্ষ ও একজন সাধারণ ডেভেলপারের পার্থক্য।

- **3. How? (The Mechanism)**
  Mark-and-Sweep Algorithm: এটি জি.সি (GC)-এর প্রধান ইঞ্জিন।

Mark: ইঞ্জিন রুট (Root) অবজেক্ট (যেমন window বা global) থেকে যাত্রা শুরু করে এবং চেক করে কোন কোন অবজেক্টের কাছে সে পৌঁছাতে পারছে (Reachable)। পৌঁছাতে পারলে সেগুলোকে "Mark" করে।

Sweep: যে অবজেক্টগুলো "Mark" করা হয়নি (অর্থাৎ যেগুলোর সাথে কোনো কানেকশন নেই), সেগুলোকে সে মেমরি থেকে মুছে ফেলে।

Memory Leak-এর সাধারণ উৎস:

Unintended Global Variables: অজান্তেই কোনো ভেরিয়েবল var, let, const ছাড়া ডিক্লেয়ার করলে তা গ্লোবাল অবজেক্টে আটকে থাকে।

Forgotten Timers/Listeners: setInterval বা addEventListener সেট করে যদি কাজ শেষে clearInterval বা removeEventListener না করা হয়, তবে তারা আজীবন মেমরিতে টিকে থাকে।

Closures Improper Usage: একটি ফাংশনের ভেতর বিশাল বড় ডেটা অবজেক্ট ক্লোজারের মাধ্যমে আটকে রাখা, যা ফাংশন শেষ হওয়ার পরও মেমরি দখল করে রাখে।

- **4. Code Example**

```JavaScript
// ==========================================
// 1. The Memory Leak Scenario
// ==========================================
function setupLeak() {
  // Global variable created unintentionally
  largeDataArray = new Array(1000000).fill("DATA");

  // Forgotten setInterval (continues running even if not needed)
  setInterval(() => {
    console.log("Still monitoring...");
  }, 5000);
}

setupLeak();
// 'largeDataArray' and the interval will NEVER be garbage collected
// even if the user leaves the page component.


// ==========================================
// 2. The Solution (Best Practices)
// ==========================================
function setupClean() {
  // Use local scope with 'const'
  const largeData = new Array(1000000).fill("DATA");

  const timerId = setInterval(() => {
    console.log("Performing task...");
  }, 5000);

  // Return a cleanup function (standard in React/Node)
  return () => {
    clearInterval(timerId); // Memory released!
  };
}

const cleanup = setupClean();
// When the component/task is finished:
cleanup();
```

- **5. Need to know**
  The Closure Trap: ক্লোজার জাভাস্ক্রিপ্টের দারুণ পাওয়ার, কিন্তু এটি মেমরির শত্রুও হতে পারে। যদি একটি ফাংশন অন্য একটি বিশাল অবজেক্টকে রিফারেন্স হিসেবে ধরে রাখে, তবে ওই ফাংশনটি যতদিন মেমরিতে থাকবে, বিশাল অবজেক্টটিও ততদিন ডিলিট হবে না। কাজ শেষ হলে ওই ভেরিয়েবলগুলোকে null করে দিয়ে ইঞ্জিনকে সিগন্যাল দেওয়া যায় যে এটি এখন ডিলিট করা সম্ভব।

Chrome DevTools - Memory Tab: আপনি যদি সন্দেহ করেন আপনার অ্যাপে মেমরি লিক হচ্ছে, তবে ক্রোম ব্রাউজারের Inspect -> Memory ট্যাবে গিয়ে Heap Snapshot নিন। সেখানে আপনি দেখতে পাবেন মেমরিতে কোন অবজেক্টটি সবচেয়ে বেশি জায়গা দখল করে আছে এবং কারা তাকে আটকে রেখেছে।

WeakMap & WeakSet: মডার্ন জাভাস্ক্রিপ্টে এই দুটি স্ট্রাকচার মেমরি লিক ঠেকানোর জন্য বিশেষভাবে তৈরি। এগুলো অবজেক্টের ওপর খুব দুর্বল রেফারেন্স (Weak Reference) ধরে রাখে। যদি অবজেক্টটির অন্য কোনো রেফারেন্স না থাকে, তবে গার্বেজ কালেক্টর সাথে সাথে সেটিকে মেমরি থেকে ডিলিট করে দেয়। ক্যাশিং-এর ক্ষেত্রে এগুলোর ব্যবহার সবচেয়ে নিরাপদ।
