# ব্যাকএন্ড স্ট্রাকচার: একটি সম্পূর্ণ রেফারেন্স নোট

> ব্যাকএন্ড প্রজেক্ট সাজানোর বিভিন্ন পদ্ধতি, প্রতিটির গঠন, কাজ, সুবিধা-অসুবিধা এবং ব্যবহারের ক্ষেত্র।

---

## ১. ব্যাকএন্ড স্ট্রাকচার কী এবং কেন দরকার

> প্রজেক্টের ফাইল ও ফোল্ডার কীভাবে সাজানো হবে এবং কোড কোন স্তরে কী কাজ করবে, তার পরিকল্পনাই হলো ব্যাকএন্ড স্ট্রাকচার।

ভালো স্ট্রাকচারের সুবিধা:

- কোড খুঁজে পাওয়া সহজ হয়
- নতুন ডেভেলপার দ্রুত প্রজেক্ট বুঝতে পারে
- বাগ ধরা ও ঠিক করা সহজ হয়
- নতুন ফিচার যোগ করা সহজ হয়
- একাধিক মানুষ একসাথে কাজ করতে পারে
- টেস্ট লেখা ও প্রজেক্ট বড় করা (scale) সহজ হয়

---

## ২. একটি request এর যাত্রাপথ

> প্রায় সব ব্যাকএন্ডে একটি request প্রায় একই পথে চলে। স্ট্রাকচারগুলোর পার্থক্য হলো এই ধাপগুলো কীভাবে ভাগ করা হয়েছে তাতে।

```
Client (Browser / Mobile App)
        ↓
Server Entry (index.js / server.js)
        ↓
App Setup (app.js)
        ↓
Routes          → which URL goes where
        ↓
Middlewares     → auth, validation, logging
        ↓
Controllers     → handle request / response
        ↓
Services        → business logic (optional layer)
        ↓
Models / DB     → data storage
        ↓
Response → Client
```

---

## ৩. স্ট্রাকচারের মূল উপাদানসমূহ

> বিভিন্ন স্ট্রাকচারে বারবার যে ফোল্ডার ও ফাইলগুলো দেখা যায়, সেগুলোর কাজ।

### Entry Point: `index.js` / `server.js`
> সার্ভার চালু করে এবং port এ শুনতে থাকে।

```js
const app = require("./app");
app.listen(5000, () => console.log("Server running on port 5000"));
```

### App Setup: `app.js`
> Express app তৈরি, global middleware, database connection ও route mount করার জায়গা।

```js
const express = require("express");
const app = express();

app.use(express.json());
app.use("/api", require("./src/routes/api"));

module.exports = app;
```

### Routes
> কোন URL ও HTTP method এ request এলে কোন controller চলবে, তা নির্ধারণ করে।

```js
router.get("/products", productController.getAll);
router.post("/products", authAdmin, productController.create);
```

### Controllers
> Request গ্রহণ করে, প্রয়োজনীয় কাজ করে এবং Response পাঠায়।

```js
exports.getAll = async (req, res) => {
  const products = await Product.find();
  res.json({ status: "success", data: products });
};
```

### Services
> Business logic আলাদা করে রাখার স্তর। Controller শুধু request/response সামলায়, আসল হিসাব-নিকাশ এখানে হয়।

```js
exports.calculateTotal = (items, coupon) => {
  const subtotal = items.reduce((sum, i) => sum + i.price * i.qty, 0);
  return coupon ? subtotal - coupon.discount : subtotal;
};
```

### Models
> ডাটাবেজের কাঠামো (schema) নির্ধারণ করে।

```js
const productSchema = new mongoose.Schema({
  title: String,
  price: Number,
  stock: Number,
});
module.exports = mongoose.model("Product", productSchema);
```

### Middlewares
> Request controller এ পৌঁছানোর আগে মাঝপথে যাচাই বা প্রক্রিয়াকরণ করে। যেমন authentication, file upload, logging।

```js
module.exports = (req, res, next) => {
  const token = req.headers.token;
  if (!token) return res.status(401).json({ message: "Unauthorized" });
  next();
};
```

### Utility / Helpers
> বারবার ব্যবহৃত ছোট সহায়ক ফাংশন। যেমন token তৈরি, তারিখ ফরম্যাট, error class।

```js
exports.createToken = (payload) =>
  jwt.sign(payload, process.env.JWT_SECRET, { expiresIn: "7d" });
```

### Config
> Database connection, environment variable ও বিভিন্ন সেটিংস।

```js
module.exports = {
  port: process.env.PORT || 5000,
  mongoUri: process.env.MONGO_URI,
};
```

### Validators
> আগত data সঠিক কিনা যাচাই করার নিয়ম।

```js
const { body } = require("express-validator");
exports.createProduct = [
  body("title").notEmpty(),
  body("price").isNumeric(),
];
```

### Uploads
> ব্যবহারকারীর আপলোড করা ছবি, ডকুমেন্ট ইত্যাদি রাখার ফোল্ডার।

```
uploads/
├── products/
└── avatars/
```

### Environment ফাইল: `.env`
> গোপন তথ্য (password, secret key) কোডের বাইরে রাখার ফাইল। কখনো Git এ push করা উচিত নয়।

```
PORT=5000
MONGO_URI=mongodb://localhost:27017/mydb
JWT_SECRET=your_secret_key
```

---

## ৪. ব্যাকএন্ড স্ট্রাকচারের ধরনসমূহ

---

### ১) Flat Structure (সমতল স্ট্রাকচার)

> সব কোড একই ফোল্ডারে বা হাতে গোনা কয়েকটি ফাইলে। সবচেয়ে সহজ রূপ।

```
project/
├── index.js
├── routes.js
├── db.js
└── package.json
```

**ব্যবহার:** শেখার প্রজেক্ট, দ্রুত prototype, খুব ছোট API।
**সুবিধা:** শুরু করা সবচেয়ে সহজ, কোনো জটিলতা নেই।
**অসুবিধা:** প্রজেক্ট বড় হলে ফাইল এলোমেলো ও বোঝা কঠিন হয়ে যায়।

---

### ২) MVC / Layered Structure (স্তরভিত্তিক)

> কাজের ধরন অনুযায়ী ফোল্ডার ভাগ করা হয়: Model (data), Controller (logic), Route (পথ)। ব্যাকএন্ডে সবচেয়ে জনপ্রিয় শুরুর স্ট্রাকচার।

```
project/
├── app.js
├── index.js
└── src/
    ├── controllers/
    │   ├── user.controller.js
    │   └── product.controller.js
    ├── models/
    │   ├── user.model.js
    │   └── product.model.js
    ├── routes/
    │   └── api.js
    ├── middlewares/
    └── utility/
```

**ব্যবহার:** ছোট থেকে মাঝারি প্রজেক্ট, REST API, E-commerce, Blog।
**সুবিধা:** শেখা সহজ, সবাই চেনে, দ্রুত development।
**অসুবিধা:**
- প্রজেক্ট বড় হলে controller এ অনেক logic জমে ফাইল বিশাল হয়ে যায়
- একটি ফিচার বুঝতে একাধিক ফোল্ডারে ঘুরতে হয়
- Business logic ও request handling মিশে যায়

---

### ৩) Controller-Service-Repository Pattern

> MVC এর উন্নত রূপ। Business logic যায় **Service** এ, আর database এর কাজ যায় **Repository** তে। Controller শুধু request/response সামলায়।

```
src/
├── controllers/     # request / response only
├── services/        # business logic
├── repositories/    # database queries
├── models/
└── routes/
```

```js
// controller: only handles HTTP
exports.createOrder = async (req, res) => {
  const order = await orderService.create(req.body);
  res.status(201).json(order);
};

// service: business rules
exports.create = async (data) => {
  // check stock, calculate price, apply coupon
  return orderRepository.save(data);
};

// repository: database access
exports.save = (data) => Order.create(data);
```

**ব্যবহার:** জটিল business logic আছে এমন প্রজেক্ট।
**সুবিধা:**
- Controller ছোট ও পরিষ্কার থাকে
- Logic পুনরায় ব্যবহার করা যায়
- Unit test লেখা সহজ
- Database বদলালে শুধু Repository বদলাতে হয়

**অসুবিধা:** ফাইল সংখ্যা বাড়ে, ছোট প্রজেক্টে বাড়তি কাজ মনে হতে পারে।

---

### ৪) Feature-based / Modular Structure (ফিচারভিত্তিক)

> কাজের ধরন নয়, **ফিচার** অনুযায়ী ভাগ। প্রতিটি ফিচারের controller, model, route, service সব এক ফোল্ডারে।

```
src/
├── modules/
│   ├── product/
│   │   ├── product.controller.js
│   │   ├── product.service.js
│   │   ├── product.model.js
│   │   ├── product.routes.js
│   │   └── product.validator.js
│   ├── cart/
│   │   └── ...
│   └── invoice/
│       └── ...
├── config/
├── middlewares/
└── utils/
```

**ব্যবহার:** বড় প্রজেক্ট, টিম ওয়ার্ক, বহু ফিচারযুক্ত অ্যাপ।
**সুবিধা:**
- একটি ফিচারের সব কিছু এক জায়গায়
- আলাদা আলাদা ডেভেলপার আলাদা module এ কাজ করতে পারে
- কোনো ফিচার মুছতে বা সরাতে চাইলে পুরো ফোল্ডার সরালেই হয়
- পরে Microservices এ ভাঙা সহজ

**অসুবিধা:** Shared code কোথায় রাখা হবে তা আগে থেকে ভাবতে হয়।

---

### ৫) Clean Architecture / Hexagonal (Ports & Adapters)

> Business logic কে framework, database ও বাইরের সব কিছু থেকে সম্পূর্ণ আলাদা রাখা হয়। নির্ভরতার দিক সবসময় ভেতরের দিকে।

```
src/
├── domain/              # entities, core business rules
│   └── order.entity.js
├── application/         # use cases
│   └── createOrder.usecase.js
├── infrastructure/      # database, email, payment, external APIs
│   ├── database/
│   └── services/
└── interfaces/          # controllers, routes, CLI
    └── http/
```

```
Dependency direction:

interfaces → application → domain
infrastructure → application → domain
(inner layers never depend on outer layers)
```

**ব্যবহার:** দীর্ঘমেয়াদি, জটিল ও বড় enterprise সিস্টেম।
**সুবিধা:**
- Framework বা database বদলালেও core logic অপরিবর্তিত থাকে
- টেস্ট করা সবচেয়ে সহজ
- Business rule খুব পরিষ্কারভাবে আলাদা থাকে

**অসুবিধা:** শেখা কঠিন, অনেক boilerplate, ছোট প্রজেক্টে অপ্রয়োজনীয়ভাবে জটিল।

---

### ৬) Monolith (একক অ্যাপ্লিকেশন)

> পুরো ব্যাকএন্ড একটিই অ্যাপ্লিকেশন, একসাথে build ও deploy হয়। MVC, Modular সবই Monolith এর ভেতরে হতে পারে।

```
my-app/
└── src/
    ├── auth/
    ├── product/
    ├── cart/
    └── payment/
(all inside one codebase, one deployment)
```

**ব্যবহার:** বেশিরভাগ প্রজেক্টের শুরু, স্টার্টআপ, ছোট থেকে মাঝারি টিম।
**সুবিধা:** Development, debug ও deploy সহজ, খরচ কম।
**অসুবিধা:** অনেক বড় হলে আলাদা অংশ আলাদাভাবে scale করা যায় না, একটি অংশের সমস্যা পুরো অ্যাপে প্রভাব ফেলতে পারে।

---

### ৭) Microservices

> প্রতিটি ফিচার আলাদা ছোট সার্ভিস। আলাদা codebase, আলাদা deploy, আলাদা database। সার্ভিসগুলো API বা message দিয়ে কথা বলে।

```
services/
├── auth-service/
│   ├── src/
│   └── Dockerfile
├── product-service/
│   ├── src/
│   └── Dockerfile
├── order-service/
│   ├── src/
│   └── Dockerfile
├── payment-service/
│   ├── src/
│   └── Dockerfile
└── api-gateway/
```

```
Client → API Gateway → Auth Service
                     → Product Service
                     → Order Service → Payment Service
```

**ব্যবহার:** বড় scale, বড় টিম, যেখানে আলাদা অংশ আলাদাভাবে বাড়াতে হয় (Netflix, Amazon ধরনের সিস্টেম)।
**সুবিধা:**
- প্রতিটি সার্ভিস আলাদাভাবে scale করা যায়
- একটি সার্ভিস নষ্ট হলে বাকিরা চলতে থাকে
- আলাদা সার্ভিসে আলাদা প্রযুক্তি ব্যবহার করা যায়

**অসুবিধা:**
- Infrastructure ও deployment জটিল
- Service এর মধ্যে যোগাযোগ ও data consistency কঠিন
- Debug ও monitoring কঠিন
- ছোট টিমের জন্য অতিরিক্ত বোঝা

---

### ৮) Serverless Architecture

> নিজে সার্ভার না চালিয়ে ছোট ছোট function ক্লাউডে চালানো হয়। Request এলে function চলে, না এলে খরচ নেই। (AWS Lambda, Vercel Functions, Cloudflare Workers)

```
functions/
├── getProducts.js
├── createOrder.js
├── sendEmail.js
└── processPayment.js
```

```js
// example: a single serverless function
exports.handler = async (event) => {
  const products = await getProductsFromDB();
  return { statusCode: 200, body: JSON.stringify(products) };
};
```

**ব্যবহার:** হালকা API, অনিয়মিত traffic, webhook, scheduled কাজ।
**সুবিধা:** সার্ভার ম্যানেজ করতে হয় না, নিজে থেকেই scale হয়, যত ব্যবহার তত খরচ।
**অসুবিধা:** Cold start এর দেরি, কোম্পানির প্ল্যাটফর্মের উপর নির্ভরতা, দীর্ঘ সময়ের কাজে উপযুক্ত নয়।

---

### ৯) Event-Driven Architecture

> Service গুলো একে অপরকে সরাসরি call না করে **event** প্রকাশ করে। যাদের দরকার তারা সেই event শুনে নিজের কাজ করে। (Kafka, RabbitMQ, Redis Pub/Sub)

```js
// publisher: order service
eventBus.emit("order.placed", order);

// subscribers: other services react independently
eventBus.on("order.placed", sendConfirmationEmail);
eventBus.on("order.placed", updateStock);
eventBus.on("order.placed", notifyWarehouse);
```

```
Order Service → [ Event Bus ] → Email Service
                              → Inventory Service
                              → Notification Service
```

**ব্যবহার:** Background কাজ, notification, real-time সিস্টেম, বড় microservices।
**সুবিধা:** Service গুলো একে অপরের উপর কম নির্ভরশীল, নতুন subscriber যোগ করা সহজ।
**অসুবিধা:** কাজের ধারাবাহিকতা অনুসরণ ও debug করা কঠিন।

---

### ১০) Monorepo

> একাধিক অ্যাপ বা প্যাকেজ (backend, frontend, shared code) একটিই repository তে রাখা হয়।

```
repo/
├── apps/
│   ├── api/
│   ├── web/
│   └── admin/
└── packages/
    ├── shared-types/
    └── ui/
```

**ব্যবহার:** একাধিক অ্যাপ একই code শেয়ার করলে (যেমন backend, customer site ও admin panel)।
**সুবিধা:** Code শেয়ার সহজ, একসাথে পরিবর্তন করা যায়।
**অসুবিধা:** Repository বড় হয়, tooling (Turborepo, Nx) শিখতে হয়।

---

## ৫. তুলনামূলক সারাংশ

| স্ট্রাকচার | জটিলতা | প্রজেক্টের আকার | মূল বৈশিষ্ট্য |
|---|---|---|---|
| Flat | ⭐ | খুব ছোট | সব এক জায়গায় |
| MVC / Layered | ⭐⭐ | ছোট থেকে মাঝারি | কাজের ধরন অনুযায়ী ভাগ |
| Controller-Service-Repository | ⭐⭐⭐ | মাঝারি | Logic আলাদা স্তরে |
| Feature-based / Modular | ⭐⭐⭐ | মাঝারি থেকে বড় | ফিচার অনুযায়ী ভাগ |
| Clean / Hexagonal | ⭐⭐⭐⭐ | বড় | Core logic সম্পূর্ণ স্বাধীন |
| Monolith | ⭐⭐ | সব আকার | একটি অ্যাপ, একটি deploy |
| Microservices | ⭐⭐⭐⭐⭐ | অনেক বড় | আলাদা আলাদা সার্ভিস |
| Serverless | ⭐⭐⭐ | ছোট থেকে মাঝারি | Function ভিত্তিক |
| Event-Driven | ⭐⭐⭐⭐ | বড় | Event দিয়ে যোগাযোগ |
| Monorepo | ⭐⭐⭐ | যেকোনো | একটি repo তে একাধিক অ্যাপ |

---

## ৬. স্ট্রাকচার বেছে নেওয়ার নিয়ম

> প্রজেক্টের আকার ও প্রয়োজন অনুযায়ী ধাপে ধাপে এগোনোই সবচেয়ে ভালো পথ। শুরুতেই জটিল স্ট্রাকচার নেওয়া উচিত নয়।

```
Learning / Prototype   →  Flat
Small project          →  MVC / Layered
Growing logic          →  + Service layer
Large project / team   →  Feature-based Modules
Very complex domain    →  Clean / Hexagonal
Huge scale             →  Microservices + Event-Driven
Light / occasional API →  Serverless
```

### বেছে নেওয়ার আগে নিজেকে প্রশ্ন করুন

- প্রজেক্ট কত বড় হবে?
- টিমে কতজন কাজ করবে?
- Business logic কতটা জটিল?
- আলাদা অংশ আলাদাভাবে scale করার দরকার আছে কি?
- ভবিষ্যতে database বা framework বদলানোর সম্ভাবনা আছে কি?

---

## ৭. সাধারণ ভালো অভ্যাস

> যে স্ট্রাকচারই বেছে নিন, এই নিয়মগুলো সব ক্ষেত্রে কাজে লাগে।

- **এক ফাইল, এক দায়িত্ব:** একটি ফাইল শুধু একটি কাজ করবে
- **একই নামকরণ রীতি:** সব ফাইলে একই style রাখুন

```
user.controller.js
user.model.js
user.service.js
```

- **গোপন তথ্য কোডে নয়:** `.env` ব্যবহার করুন এবং `.gitignore` এ যোগ করুন

```
# .gitignore
node_modules
.env
uploads
```

- **Error handling এক জায়গায়:** একটি কেন্দ্রীয় error middleware রাখুন

```js
app.use((err, req, res, next) => {
  res.status(err.status || 500).json({ message: err.message });
});
```

- **API version রাখুন:** ভবিষ্যতে পরিবর্তনে পুরনো client নষ্ট হয় না

```
/api/v1/products
/api/v2/products
```

- **Input যাচাই করুন:** ব্যবহারকারীর data কখনো সরাসরি বিশ্বাস করবেন না
- **README লিখুন:** প্রজেক্ট চালানোর নিয়ম ও ফোল্ডার ব্যাখ্যা রাখুন
