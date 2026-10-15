# Express.js: একটি সম্পূর্ণ রেফারেন্স নোট

> Express কী, কেন ব্যবহার হয়, এতে কী কী থাকে, কীভাবে কাজ করে এবং Express আমাদের কী কী সুবিধা দেয়।

---

## ১. Express কী

> Express হলো Node.js এর জন্য একটি হালকা ও জনপ্রিয় **web framework**, যা দিয়ে খুব সহজে সার্ভার, API ও ওয়েব অ্যাপ্লিকেশন বানানো যায়।

- Node.js এর উপরে তৈরি, নিজে আলাদা ভাষা নয়
- সংক্ষেপে বললে: Node এর `http` module কে সহজ ও গোছানো করে দেয়
- "Unopinionated" অর্থাৎ ফোল্ডার স্ট্রাকচার বা নিয়ম জোর করে চাপিয়ে দেয় না, আপনি নিজের মতো সাজাতে পারেন
- REST API বানানোর জন্য সবচেয়ে বেশি ব্যবহৃত Node.js framework

---

## ২. কেন Express ব্যবহার করব

> Express ছাড়া Node.js দিয়ে সার্ভার বানানো সম্ভব, কিন্তু অনেক বেশি কোড লিখতে হয়।

### Express ছাড়া (শুধু Node.js)

```js
const http = require("http");

const server = http.createServer((req, res) => {
  if (req.url === "/products" && req.method === "GET") {
    res.writeHead(200, { "Content-Type": "application/json" });
    res.end(JSON.stringify({ message: "All products" }));
  } else if (req.url === "/users" && req.method === "GET") {
    res.writeHead(200, { "Content-Type": "application/json" });
    res.end(JSON.stringify({ message: "All users" }));
  } else {
    res.writeHead(404);
    res.end("Not Found");
  }
});

server.listen(5000);
```

### Express দিয়ে

```js
const express = require("express");
const app = express();

app.get("/products", (req, res) => res.json({ message: "All products" }));
app.get("/users", (req, res) => res.json({ message: "All users" }));

app.listen(5000);
```

### Express এর সুবিধা

- কম কোডে বেশি কাজ
- সহজ routing
- Middleware সিস্টেম (যেকোনো কাজ মাঝপথে জুড়ে দেওয়া যায়)
- JSON, form data, cookie সহজে পড়া যায়
- হাজার হাজার ready-made package (npm ecosystem)
- বিশাল community ও প্রচুর শেখার উপকরণ

---

## ৩. ইনস্টল ও প্রথম সার্ভার

> প্রজেক্ট তৈরি থেকে সার্ভার চালু পর্যন্ত ধাপগুলো।

```bash
mkdir my-app
cd my-app
npm init -y
npm install express
```

```js
// index.js
const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.send("Hello Express!");
});

app.listen(5000, () => {
  console.log("Server running on http://localhost:5000");
});
```

```bash
node index.js
```

### Auto restart এর জন্য (development)

> ফাইল পরিবর্তন করলে সার্ভার নিজে থেকে restart হয়।

```bash
npm install -D nodemon
```

```json
{
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
  }
}
```

### ES Module ব্যবহার করতে চাইলে

```json
{
  "type": "module"
}
```

```js
import express from "express";
const app = express();
```

---

## ৪. Express আমাদের কী কী দেয়

> এক নজরে Express এর মূল বৈশিষ্ট্যগুলো।

| বৈশিষ্ট্য | কাজ |
|---|---|
| **Routing** | URL ও HTTP method অনুযায়ী কোড চালানো |
| **Middleware** | Request ও Response এর মাঝখানে কাজ করা |
| **Request object (`req`)** | আগত তথ্য পড়া (body, params, query, headers) |
| **Response object (`res`)** | উত্তর পাঠানো (json, html, file, redirect) |
| **Static file serving** | ছবি, CSS, JS ফাইল সরাসরি পরিবেশন |
| **Template engine support** | Dynamic HTML পাতা তৈরি (EJS, Pug) |
| **Error handling** | কেন্দ্রীয়ভাবে error সামলানো |
| **Router** | Route গুলো আলাদা ফাইলে ভাগ করা |
| **App settings** | অ্যাপের কনফিগারেশন |

---

## ৫. Routing (রাউটিং)

> কোন URL এ কোন HTTP method দিয়ে request এলে কোন কোড চলবে, তা ঠিক করা।

### HTTP Method গুলো

```js
app.get("/products", handler);       // read data
app.post("/products", handler);      // create data
app.put("/products/:id", handler);   // replace whole data
app.patch("/products/:id", handler); // update part of data
app.delete("/products/:id", handler);// delete data
app.all("/secret", handler);         // any method
```

| Method | কাজ | CRUD |
|---|---|---|
| GET | তথ্য পড়া | Read |
| POST | নতুন তথ্য তৈরি | Create |
| PUT | সম্পূর্ণ তথ্য প্রতিস্থাপন | Update |
| PATCH | আংশিক তথ্য পরিবর্তন | Update |
| DELETE | তথ্য মুছে ফেলা | Delete |

### Route Parameters (URL এর অংশ)

> URL এর ভেতরের পরিবর্তনশীল অংশ ধরার জন্য।

```js
// GET /users/42
app.get("/users/:id", (req, res) => {
  res.json({ userId: req.params.id }); // "42"
});

// GET /users/42/orders/7
app.get("/users/:userId/orders/:orderId", (req, res) => {
  const { userId, orderId } = req.params;
  res.json({ userId, orderId });
});
```

### Query String (? এর পরের অংশ)

> ফিল্টার, সার্চ, পেজিনেশনের জন্য।

```js
// GET /products?category=phone&page=2
app.get("/products", (req, res) => {
  const { category, page } = req.query;
  res.json({ category, page });
});
```

### Route Chaining

> একই URL এ একাধিক method এক জায়গায় লেখা।

```js
app
  .route("/products")
  .get((req, res) => res.send("Get all"))
  .post((req, res) => res.send("Create one"));
```

### express.Router (Route ভাগ করা)

> সব route এক ফাইলে না রেখে বিষয় অনুযায়ী আলাদা ফাইলে ভাগ করা।

```js
// routes/product.routes.js
const express = require("express");
const router = express.Router();

router.get("/", (req, res) => res.send("All products"));
router.get("/:id", (req, res) => res.send("One product"));
router.post("/", (req, res) => res.send("Create product"));

module.exports = router;
```

```js
// app.js
const productRoutes = require("./routes/product.routes");
app.use("/api/products", productRoutes);
// GET /api/products      → All products
// GET /api/products/5    → One product
```

---

## ৬. Request Object (`req`)

> ক্লায়েন্ট যা পাঠিয়েছে তার সব তথ্য `req` এ থাকে।

| Property | কী থাকে | উদাহরণ |
|---|---|---|
| `req.params` | URL parameter | `/users/:id` → `req.params.id` |
| `req.query` | Query string | `?page=2` → `req.query.page` |
| `req.body` | পাঠানো data (parser লাগে) | JSON বা form data |
| `req.headers` | HTTP header | `req.headers.authorization` |
| `req.cookies` | Cookie (cookie-parser লাগে) | `req.cookies.token` |
| `req.method` | HTTP method | `"GET"`, `"POST"` |
| `req.url` / `req.originalUrl` | অনুরোধ করা URL | `"/api/users?x=1"` |
| `req.path` | শুধু path অংশ | `"/api/users"` |
| `req.ip` | ক্লায়েন্টের IP | `"::1"` |
| `req.file` / `req.files` | আপলোড করা ফাইল (multer লাগে) | |

```js
app.post("/login", (req, res) => {
  const { email, password } = req.body;
  const token = req.headers.authorization;
  console.log(email, password, token);
  res.send("ok");
});
```

---

## ৭. Response Object (`res`)

> ক্লায়েন্টকে উত্তর পাঠানোর সব উপায়।

```js
res.send("Hello");                       // text or html
res.json({ name: "Rahim" });             // JSON response
res.status(201).json({ created: true }); // set status + json
res.sendStatus(404);                     // status with default text
res.redirect("/login");                  // redirect to another URL
res.sendFile(path.join(__dirname, "index.html")); // send a file
res.download("./files/report.pdf");      // force file download
res.render("home", { title: "Home" });   // render template
res.cookie("token", "abc", { httpOnly: true }); // set cookie
res.clearCookie("token");                // remove cookie
res.set("X-Custom", "value");            // set header
res.end();                               // end without data
```

> **মনে রাখবেন:** একটি request এ শুধু একবারই response পাঠানো যায়। দুইবার পাঠালে error আসে।

### গুরুত্বপূর্ণ HTTP Status Code

| Code | অর্থ | কখন |
|---|---|---|
| 200 | OK | সফল |
| 201 | Created | নতুন কিছু তৈরি হয়েছে |
| 204 | No Content | সফল কিন্তু ফেরত দেওয়ার কিছু নেই |
| 400 | Bad Request | ভুল ইনপুট |
| 401 | Unauthorized | লগইন করা নেই |
| 403 | Forbidden | অনুমতি নেই |
| 404 | Not Found | খুঁজে পাওয়া যায়নি |
| 409 | Conflict | ডুপ্লিকেট বা সংঘর্ষ |
| 422 | Unprocessable Entity | validation ব্যর্থ |
| 429 | Too Many Requests | অতিরিক্ত request |
| 500 | Internal Server Error | সার্ভারের ভুল |

---

## ৮. Middleware (মিডলওয়্যার)

> Express এর প্রাণ। Request আসার পর এবং Response যাওয়ার আগে মাঝপথে যে ফাংশনগুলো চলে, সেগুলোই middleware।

### Middleware কীভাবে কাজ করে

```
Request
   ↓
Middleware 1 (logger)       → next()
   ↓
Middleware 2 (body parser)  → next()
   ↓
Middleware 3 (auth check)   → next()
   ↓
Route Handler               → res.json()
   ↓
Response
```

### Middleware এর গঠন

```js
const logger = (req, res, next) => {
  console.log(req.method, req.url);
  next(); // pass control to the next middleware
};

app.use(logger);
```

- `req`: আগত request
- `res`: উত্তর পাঠানোর object
- `next`: পরের middleware এ যাওয়ার ফাংশন

> **সতর্কতা:** `next()` না ডাকলে এবং response ও না পাঠালে request আটকে থাকবে (hang করবে)।

### Middleware এর কাজ

- Request log করা
- Body পড়া (JSON, form)
- Authentication ও authorization যাচাই
- Input validation
- File upload
- CORS ও security header
- Error handling

### Middleware এর ধরন

#### ক) Application-level Middleware
> পুরো অ্যাপে প্রযোজ্য।

```js
app.use(express.json());
app.use(logger);
```

#### খ) Route-level Middleware
> শুধু নির্দিষ্ট route এ প্রযোজ্য।

```js
const authAdmin = (req, res, next) => {
  if (req.headers.role !== "admin") {
    return res.status(403).json({ message: "Forbidden" });
  }
  next();
};

app.delete("/products/:id", authAdmin, (req, res) => {
  res.send("Deleted");
});
```

#### গ) Router-level Middleware
> একটি Router এর সব route এ প্রযোজ্য।

```js
const router = express.Router();
router.use(authUser);
router.get("/profile", profileHandler);
```

#### ঘ) Built-in Middleware
> Express এর সাথেই আসে।

```js
app.use(express.json());                         // parse JSON body
app.use(express.urlencoded({ extended: true })); // parse form data
app.use(express.static("public"));               // serve static files
```

#### ঙ) Third-party Middleware
> npm থেকে ইনস্টল করা (পরের অংশে বিস্তারিত)।

```js
const cors = require("cors");
app.use(cors());
```

#### চ) Error-handling Middleware
> চারটি parameter থাকে। সবার শেষে লিখতে হয়।

```js
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(err.status || 500).json({ message: err.message });
});
```

### Middleware এর ক্রম গুরুত্বপূর্ণ

> Middleware উপর থেকে নিচে ক্রমানুসারে চলে। তাই সঠিক জায়গায় লিখতে হয়।

```js
app.use(express.json());      // 1. first, parse body
app.use(logger);              // 2. log requests
app.use("/api", routes);      // 3. routes
app.use(notFoundHandler);     // 4. 404 handler
app.use(errorHandler);        // 5. error handler (always last)
```

---

## ৯. Body Parsing (Request Body পড়া)

> ক্লায়েন্ট যে data পাঠায় তা পড়তে parser লাগে। না থাকলে `req.body` খালি বা `undefined` থাকে।

```js
app.use(express.json());                         // for JSON: { "name": "Rahim" }
app.use(express.urlencoded({ extended: true })); // for HTML form: name=Rahim
```

```js
app.post("/users", (req, res) => {
  console.log(req.body); // { name: "Rahim" }
  res.status(201).json(req.body);
});
```

---

## ১০. Static Files (স্থির ফাইল পরিবেশন)

> ছবি, CSS, JavaScript, PDF ইত্যাদি ফাইল সরাসরি ব্রাউজারে দেখানোর জন্য।

```js
const path = require("path");

app.use(express.static("public"));
// public/logo.png → http://localhost:5000/logo.png

app.use("/uploads", express.static(path.join(__dirname, "uploads")));
// uploads/photo.jpg → http://localhost:5000/uploads/photo.jpg
```

---

## ১১. জনপ্রিয় Third-party Middleware ও Package

> Express নিজে ছোট। বাকি সুবিধা এই package গুলো থেকে আসে।

| Package | কাজ |
|---|---|
| `cors` | অন্য domain থেকে request অনুমতি দেওয়া |
| `helmet` | নিরাপত্তা সংক্রান্ত HTTP header যোগ করা |
| `morgan` | Request এর log দেখানো |
| `cookie-parser` | Cookie পড়া |
| `dotenv` | `.env` ফাইল থেকে environment variable লোড |
| `multer` | ফাইল আপলোড |
| `express-validator` | Input যাচাই |
| `express-rate-limit` | অতিরিক্ত request আটকানো |
| `compression` | Response ছোট (gzip) করা |
| `jsonwebtoken` | JWT token তৈরি ও যাচাই |
| `bcrypt` | পাসওয়ার্ড hash করা |
| `mongoose` | MongoDB এর সাথে কাজ |
| `nodemon` | Development এ auto restart |

```bash
npm install cors helmet morgan cookie-parser dotenv
```

```js
require("dotenv").config();
const express = require("express");
const cors = require("cors");
const helmet = require("helmet");
const morgan = require("morgan");
const cookieParser = require("cookie-parser");

const app = express();

app.use(helmet());
app.use(cors());
app.use(morgan("dev"));
app.use(express.json());
app.use(cookieParser());
```

---

## ১২. Error Handling (ত্রুটি সামলানো)

> সার্ভারে সমস্যা হলে ক্লায়েন্টকে সুন্দরভাবে জানানো এবং সার্ভার যেন বন্ধ না হয়।

### ১) Custom Error Class

```js
class AppError extends Error {
  constructor(message, status) {
    super(message);
    this.status = status;
  }
}
module.exports = AppError;
```

### ২) Error throw করা

```js
app.get("/products/:id", async (req, res, next) => {
  try {
    const product = await Product.findById(req.params.id);
    if (!product) throw new AppError("Product not found", 404);
    res.json(product);
  } catch (err) {
    next(err); // send to error middleware
  }
});
```

### ৩) 404 Handler (কোনো route না মিললে)

```js
app.use((req, res, next) => {
  res.status(404).json({ message: "Route not found" });
});
```

### ৪) Central Error Handler (সবার শেষে)

```js
app.use((err, req, res, next) => {
  res.status(err.status || 500).json({
    success: false,
    message: err.message || "Something went wrong",
  });
});
```

### Async Error এর বিষয়ে

> Express 5 এ async function এর error নিজে থেকেই error middleware এ চলে যায়। Express 4 এ এজন্য `try/catch` ও `next(err)` অথবা একটি wrapper লাগে।

```js
// helper for Express 4
const asyncHandler = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);

app.get("/users", asyncHandler(async (req, res) => {
  const users = await User.find();
  res.json(users);
}));
```

---

## ১৩. Database এর সাথে সংযোগ

> Express নিজে database দেয় না। যেকোনো database এর package জুড়ে ব্যবহার করতে হয়।

### MongoDB (Mongoose)

```bash
npm install mongoose
```

```js
const mongoose = require("mongoose");

mongoose
  .connect(process.env.MONGO_URI)
  .then(() => console.log("Database connected"))
  .catch((err) => console.error(err));
```

```js
// model
const userSchema = new mongoose.Schema({
  name: String,
  email: { type: String, unique: true },
});
const User = mongoose.model("User", userSchema);

// CRUD in routes
app.get("/users", async (req, res) => res.json(await User.find()));
app.post("/users", async (req, res) => res.status(201).json(await User.create(req.body)));
app.put("/users/:id", async (req, res) =>
  res.json(await User.findByIdAndUpdate(req.params.id, req.body, { new: true }))
);
app.delete("/users/:id", async (req, res) => {
  await User.findByIdAndDelete(req.params.id);
  res.sendStatus(204);
});
```

### অন্যান্য Database

| Database | ব্যবহারযোগ্য package |
|---|---|
| MongoDB | `mongoose`, `mongodb` |
| MySQL | `mysql2`, `sequelize`, `prisma` |
| PostgreSQL | `pg`, `sequelize`, `prisma`, `knex` |
| SQLite | `sqlite3`, `better-sqlite3` |
| Redis | `redis`, `ioredis` |

---

## ১৪. Authentication (পরিচয় যাচাই)

> ব্যবহারকারী কে তা যাচাই করা। সবচেয়ে প্রচলিত পদ্ধতি JWT (JSON Web Token)।

```bash
npm install jsonwebtoken bcrypt
```

### Register (পাসওয়ার্ড hash করে সংরক্ষণ)

```js
const bcrypt = require("bcrypt");

app.post("/register", async (req, res) => {
  const { email, password } = req.body;
  const hashed = await bcrypt.hash(password, 10);
  const user = await User.create({ email, password: hashed });
  res.status(201).json({ id: user._id });
});
```

### Login (token তৈরি)

```js
const jwt = require("jsonwebtoken");

app.post("/login", async (req, res) => {
  const { email, password } = req.body;
  const user = await User.findOne({ email });
  if (!user) return res.status(401).json({ message: "Invalid credentials" });

  const match = await bcrypt.compare(password, user.password);
  if (!match) return res.status(401).json({ message: "Invalid credentials" });

  const token = jwt.sign({ id: user._id }, process.env.JWT_SECRET, {
    expiresIn: "7d",
  });
  res.json({ token });
});
```

### Auth Middleware (token যাচাই)

```js
const jwt = require("jsonwebtoken");

module.exports = (req, res, next) => {
  const header = req.headers.authorization; // "Bearer <token>"
  if (!header) return res.status(401).json({ message: "No token" });

  try {
    const token = header.split(" ")[1];
    req.user = jwt.verify(token, process.env.JWT_SECRET);
    next();
  } catch {
    res.status(401).json({ message: "Invalid token" });
  }
};
```

```js
app.get("/profile", authMiddleware, (req, res) => {
  res.json({ userId: req.user.id });
});
```

---

## ১৫. File Upload (ফাইল আপলোড)

> ছবি বা ডকুমেন্ট আপলোডের জন্য `multer` ব্যবহার হয়।

```bash
npm install multer
```

```js
const multer = require("multer");

const storage = multer.diskStorage({
  destination: "uploads/",
  filename: (req, file, cb) => cb(null, Date.now() + "-" + file.originalname),
});
const upload = multer({ storage });

// single file
app.post("/avatar", upload.single("avatar"), (req, res) => {
  res.json({ file: req.file.filename });
});

// multiple files
app.post("/gallery", upload.array("photos", 5), (req, res) => {
  res.json({ count: req.files.length });
});
```

---

## ১৬. Input Validation (ইনপুট যাচাই)

> ব্যবহারকারীর পাঠানো data সঠিক কিনা দেখা। কখনো সরাসরি বিশ্বাস করা উচিত নয়।

```bash
npm install express-validator
```

```js
const { body, validationResult } = require("express-validator");

app.post(
  "/register",
  body("email").isEmail().withMessage("Invalid email"),
  body("password").isLength({ min: 6 }).withMessage("Min 6 characters"),
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(422).json({ errors: errors.array() });
    }
    res.send("Valid");
  }
);
```

---

## ১৭. Template Engine (Server-side HTML)

> সার্ভার থেকেই dynamic HTML পাতা তৈরি করে পাঠানো। (API বানালে সাধারণত লাগে না)

```bash
npm install ejs
```

```js
app.set("view engine", "ejs");
app.set("views", "./views");

app.get("/", (req, res) => {
  res.render("home", { title: "Home", users: ["Rahim", "Karim"] });
});
```

```html
<!-- views/home.ejs -->
<h1><%= title %></h1>
<ul>
  <% users.forEach(u => { %>
    <li><%= u %></li>
  <% }) %>
</ul>
```

জনপ্রিয় engine: `EJS`, `Pug`, `Handlebars`।

---

## ১৮. App Settings

> Express অ্যাপের কনফিগারেশন।

```js
app.set("port", 5000);
app.set("view engine", "ejs");
app.set("trust proxy", 1);   // when behind a proxy (nginx, cloud)

console.log(app.get("port")); // 5000

app.locals.siteName = "My Shop"; // available in all templates
```

---

## ১৯. সম্পূর্ণ ছোট প্রজেক্টের উদাহরণ

> সব কিছু একসাথে জুড়লে একটি বাস্তব অ্যাপ কেমন হয়।

### ফোল্ডার স্ট্রাকচার

```
my-app/
├── .env
├── .gitignore
├── package.json
├── index.js
├── app.js
└── src/
    ├── config/
    │   └── db.js
    ├── controllers/
    │   └── user.controller.js
    ├── middlewares/
    │   ├── auth.js
    │   └── errorHandler.js
    ├── models/
    │   └── user.model.js
    └── routes/
        └── user.routes.js
```

### `index.js`

```js
require("dotenv").config();
const app = require("./app");
const connectDB = require("./src/config/db");

connectDB().then(() => {
  app.listen(process.env.PORT, () =>
    console.log(`Server running on port ${process.env.PORT}`)
  );
});
```

### `app.js`

```js
const express = require("express");
const cors = require("cors");
const helmet = require("helmet");
const morgan = require("morgan");

const userRoutes = require("./src/routes/user.routes");
const errorHandler = require("./src/middlewares/errorHandler");

const app = express();

app.use(helmet());
app.use(cors());
app.use(morgan("dev"));
app.use(express.json());

app.use("/api/users", userRoutes);

app.use((req, res) => res.status(404).json({ message: "Route not found" }));
app.use(errorHandler);

module.exports = app;
```

### `src/routes/user.routes.js`

```js
const router = require("express").Router();
const auth = require("../middlewares/auth");
const ctrl = require("../controllers/user.controller");

router.post("/register", ctrl.register);
router.post("/login", ctrl.login);
router.get("/profile", auth, ctrl.profile);

module.exports = router;
```

### `src/controllers/user.controller.js`

```js
const User = require("../models/user.model");

exports.profile = async (req, res, next) => {
  try {
    const user = await User.findById(req.user.id).select("-password");
    res.json(user);
  } catch (err) {
    next(err);
  }
};
```

---

## ২০. Express 4 বনাম Express 5

> বর্তমানে Express 5 হলো নতুন প্রধান সংস্করণ। পুরনো টিউটোরিয়ালে Express 4 এর কোড দেখা যেতে পারে।

| বিষয় | Express 4 | Express 5 |
|---|---|---|
| Async error | নিজে `try/catch` বা wrapper লাগে | Rejected promise নিজে থেকে error handler এ যায় |
| Node.js ভার্সন | পুরনো ভার্সনেও চলে | নতুন Node.js ভার্সন লাগে |
| Wildcard route | `app.get("*", ...)` | নাম দিতে হয়, যেমন `app.get("/*splat", ...)` |
| `req.body` (parser ছাড়া) | `{}` | `undefined` |

> নতুন প্রজেক্ট শুরু করলে Express 5 ব্যবহার করাই ভালো। ইনস্টল করার সময় ভার্সন দেখে নিন: `npm install express`।

---

## ২১. Express এর সীমাবদ্ধতা

> Express সবকিছু নিজে করে না। কিছু বিষয় আপনাকেই ঠিক করতে হয়।

- ফোল্ডার স্ট্রাকচারের কোনো নিয়ম নেই, নিজেকে ঠিক করতে হয়
- Database, validation, authentication বিল্ট-ইন নেই, package লাগে
- বড় প্রজেক্টে নিয়ম না মানলে কোড এলোমেলো হয়ে যায়
- TypeScript এর জন্য আলাদা type package লাগে (`@types/express`)

> যাদের বেশি নিয়মকানুন ও কাঠামো দরকার, তারা **NestJS** ব্যবহার করে (Express এর উপরেই তৈরি)। আরও বেশি গতি চাইলে **Fastify** একটি বিকল্প।

---

## ২২. ভালো অভ্যাস

> Express প্রজেক্টে এই নিয়মগুলো মানলে কোড নিরাপদ ও পরিষ্কার থাকে।

- `.env` ফাইলে secret রাখুন, Git এ push করবেন না
- `helmet` ব্যবহার করে security header দিন
- `express-rate-limit` দিয়ে অতিরিক্ত request আটকান
- সব input validate করুন
- পাসওয়ার্ড সবসময় `bcrypt` দিয়ে hash করুন
- Error handler সবার শেষে রাখুন
- Controller ছোট রাখুন, বড় logic আলাদা service এ নিন
- Production এ error এর `stack` ক্লায়েন্টকে দেখাবেন না
- Route গুলো `express.Router` দিয়ে ভাগ করুন
- API এ version রাখুন

```js
app.use("/api/v1/users", userRoutes);
```

---

## ২৩. দ্রুত Cheat Sheet

> সবচেয়ে বেশি লাগে এমন জিনিসের সংক্ষিপ্ত তালিকা।

```js
// setup
const express = require("express");
const app = express();
app.use(express.json());

// routes
app.get("/path", (req, res) => {});
app.post("/path", (req, res) => {});
app.put("/path/:id", (req, res) => {});
app.delete("/path/:id", (req, res) => {});

// read data
req.params.id      // /users/:id
req.query.page     // ?page=2
req.body           // JSON body
req.headers.authorization

// send response
res.json({});
res.status(404).json({});
res.send("text");
res.redirect("/url");

// middleware
app.use((req, res, next) => { next(); });

// router
const router = express.Router();
app.use("/api/users", router);

// error handler (last)
app.use((err, req, res, next) => {
  res.status(500).json({ message: err.message });
});

// start
app.listen(5000);
```
