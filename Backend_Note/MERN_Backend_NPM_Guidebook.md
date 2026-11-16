# 📘 MERN Backend NPM Package Guidebook

> কোন package কী কাজে লাগে, কেন লাগে, আর কীভাবে ব্যবহার করতে হয় — এক জায়গায়।
> Stack: **Node.js + Express + MongoDB + Redis**

---

## 📑 সূচিপত্র

1. [প্রজেক্ট সেটআপ ও ফোল্ডার স্ট্রাকচার](#1-প্রজেক্ট-সেটআপ-ও-ফোল্ডার-স্ট্রাকচার)
2. [Core Packages](#2-core-packages)
3. [Authentication ও Security](#3-authentication-ও-security)
4. [Validation](#4-validation)
5. [Redis ও Caching](#5-redis-ও-caching)
6. [Background Jobs ও Queue](#6-background-jobs-ও-queue)
7. [File Upload ও Media](#7-file-upload-ও-media)
8. [Email ও Notification](#8-email-ও-notification)
9. [Real-time (Socket.io)](#9-real-time-socketio)
10. [Payment](#10-payment)
    - [10.1 SSLCommerz সম্পূর্ণ সেটআপ](#101-sslcommerz-সম্পূর্ণ-সেটআপ-গাইড)
11. [Logging ও Error Handling](#11-logging-ও-error-handling)
12. [Utility Packages](#12-utility-packages)
13. [API Documentation](#13-api-documentation)
14. [Dev Tools ও Testing](#14-dev-tools-ও-testing)
15. [Production ও Deployment](#15-production-ও-deployment)
16. [অতিরিক্ত গুরুত্বপূর্ণ Packages (আগের লিস্টে ছিল না)](#16-অতিরিক্ত-গুরুত্বপূর্ণ-packages)
17. [⚠️ Deprecated / এড়িয়ে চলার Packages](#17-️-deprecated--এড়িয়ে-চলার-packages)
18. [Best Practices Checklist](#18-best-practices-checklist)
19. [Quick Install Cheat Sheet](#19-quick-install-cheat-sheet)

**🚀 PART 2 — Advanced**

20. [Advanced Monolith](#20-advanced-monolith-বড়-ও-ভরসাযোগ্য-backend)
21. [Microservice Architecture](#21-microservice-architecture)
22. [SaaS Product Backend](#22-saas-product-backend)
23. [Testing, Docker, CI/CD](#23-testing-docker-cicd)
24. [কোন ধরনের Project এ কী লাগে](#24-কোন-ধরনের-project-এ-কী-লাগে-decision-table)
25. [📅 Practice Roadmap (দিনে ২-৩টা)](#25--practice-roadmap-দিনে-২-৩টা-করে)

---

## 1. প্রজেক্ট সেটআপ ও ফোল্ডার স্ট্রাকচার

```bash
mkdir my-backend && cd my-backend
npm init -y
```

`package.json` এ ES Module চালু করতে চাইলে: `"type": "module"` যোগ করো।

### প্রস্তাবিত ফোল্ডার স্ট্রাকচার

```
my-backend/
├── src/
│   ├── config/          # db.js, redis.js, env.js
│   ├── controllers/     # request handle করে
│   ├── services/        # business logic
│   ├── models/          # mongoose schema
│   ├── routes/          # route definition
│   ├── middlewares/     # auth, error, validate, rateLimit
│   ├── validations/     # zod/joi schema
│   ├── utils/           # helper function, ApiError, catchAsync
│   ├── jobs/            # bullmq queue ও worker
│   ├── sockets/         # socket.io logic
│   ├── app.js           # express app setup
│   └── server.js        # server listen + db connect
├── .env
├── .env.example
├── .gitignore
└── package.json
```

> **নিয়ম:** Route → Controller → Service → Model। Controller-এ business logic লিখো না।

---

## 2. Core Packages

### `express`
- **কাজ:** Node.js এর web framework। Route, middleware, request/response handle করে।
- **কখন লাগে:** প্রতিটি backend প্রজেক্টে।

```bash
npm i express
```

```js
// src/app.js
import express from "express";
const app = express();

app.use(express.json());                        // JSON body পড়ার জন্য
app.use(express.urlencoded({ extended: true })); // form data পড়ার জন্য

app.get("/", (req, res) => res.send("API running"));

export default app;
```

```js
// src/server.js
import app from "./app.js";
app.listen(process.env.PORT || 5000, () => console.log("Server started"));
```

---

### `mongoose`
- **কাজ:** MongoDB এর ODM। Schema, validation, query, relation (populate) সহজ করে।

```bash
npm i mongoose
```

```js
// src/config/db.js
import mongoose from "mongoose";

export const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI);
    console.log("MongoDB connected");
  } catch (err) {
    console.error(err);
    process.exit(1);
  }
};
```

```js
// src/models/user.model.js
import mongoose from "mongoose";

const userSchema = new mongoose.Schema(
  {
    name: { type: String, required: true, trim: true },
    email: { type: String, required: true, unique: true, lowercase: true },
    password: { type: String, required: true, select: false },
    role: { type: String, enum: ["user", "admin"], default: "user" },
  },
  { timestamps: true }
);

export default mongoose.model("User", userSchema);
```

**টিপস:**
- `select: false` দিলে query তে password আসবে না।
- `timestamps: true` দিলে `createdAt`/`updatedAt` অটো তৈরি হয়।
- বারবার search হওয়া field-এ `index: true` দাও।

---

### `dotenv`
- **কাজ:** `.env` ফাইল থেকে secret/config লোড করে (DB URL, JWT secret ইত্যাদি)।

```bash
npm i dotenv
```

```
# .env
PORT=5000
MONGO_URI=mongodb://127.0.0.1:27017/mydb
JWT_SECRET=superlongrandomsecret
JWT_EXPIRES_IN=7d
REDIS_URL=redis://127.0.0.1:6379
CLIENT_URL=http://localhost:5173
```

```js
import "dotenv/config"; // সবার আগে, server.js এর প্রথম লাইনে
```

> ⚠️ `.env` কখনো git-এ push করো না। `.gitignore` এ রাখো, আর `.env.example` বানিয়ে রাখো।
> (Node 20+ এ `node --env-file=.env server.js` দিয়েও dotenv ছাড়া চলে।)

---

### `cors`
- **কাজ:** Frontend (React) অন্য domain/port থেকে API call করতে দেয়।

```bash
npm i cors
```

```js
import cors from "cors";

app.use(
  cors({
    origin: process.env.CLIENT_URL, // শুধু তোমার frontend
    credentials: true,              // cookie পাঠাতে চাইলে
  })
);
```

---

### `cookie-parser`
- **কাজ:** `req.cookies` এ cookie পড়তে দেয়। JWT কে httpOnly cookie তে রাখলে লাগে।

```bash
npm i cookie-parser
```

```js
import cookieParser from "cookie-parser";
app.use(cookieParser());

// cookie set
res.cookie("token", token, {
  httpOnly: true,   // JS দিয়ে পড়া যাবে না (XSS safe)
  secure: process.env.NODE_ENV === "production", // শুধু HTTPS
  sameSite: "strict",
  maxAge: 7 * 24 * 60 * 60 * 1000,
});

// cookie পড়া
const token = req.cookies.token;
```

---

### `compression`
- **কাজ:** Response gzip করে ছোট করে, ফলে API দ্রুত হয়।

```bash
npm i compression
```

```js
import compression from "compression";
app.use(compression());
```

---

## 3. Authentication ও Security

### `jsonwebtoken` (JWT)
- **কাজ:** Login এর পর token তৈরি ও verify করে।

```bash
npm i jsonwebtoken
```

```js
import jwt from "jsonwebtoken";

// তৈরি
const token = jwt.sign({ id: user._id, role: user.role }, process.env.JWT_SECRET, {
  expiresIn: process.env.JWT_EXPIRES_IN,
});

// verify
const decoded = jwt.verify(token, process.env.JWT_SECRET);
```

**Auth middleware:**

```js
// src/middlewares/auth.js
import jwt from "jsonwebtoken";
import User from "../models/user.model.js";

export const protect = async (req, res, next) => {
  try {
    const token =
      req.cookies.token || req.headers.authorization?.split(" ")[1];
    if (!token) return res.status(401).json({ message: "Not authorized" });

    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = await User.findById(decoded.id);
    if (!req.user) return res.status(401).json({ message: "User not found" });
    next();
  } catch {
    res.status(401).json({ message: "Invalid or expired token" });
  }
};

export const authorize = (...roles) => (req, res, next) => {
  if (!roles.includes(req.user.role))
    return res.status(403).json({ message: "Forbidden" });
  next();
};

// ব্যবহার: router.delete("/:id", protect, authorize("admin"), deleteUser);
```

**Best practice:** Access token ছোট মেয়াদের (15m), Refresh token বড় মেয়াদের (7d), refresh token DB/Redis এ রাখো।

---

### `bcrypt` / `bcryptjs`
- **কাজ:** Password hash করে। কখনো plain password save করবে না।
- `bcrypt` দ্রুত (native), `bcryptjs` pure JS (install সমস্যা কম)।

```bash
npm i bcryptjs
```

```js
import bcrypt from "bcryptjs";

const hashed = await bcrypt.hash(password, 12);       // সেভ করার সময়
const ok = await bcrypt.compare(password, user.password); // login এ
```

**Mongoose pre-save hook:**

```js
userSchema.pre("save", async function (next) {
  if (!this.isModified("password")) return next();
  this.password = await bcrypt.hash(this.password, 12);
  next();
});
```

---

### `helmet`
- **কাজ:** Security HTTP header সেট করে (XSS, clickjacking ইত্যাদি থেকে সুরক্ষা)।

```bash
npm i helmet
```

```js
import helmet from "helmet";
app.use(helmet());
```

---

### `express-rate-limit`
- **কাজ:** একই IP থেকে বেশি request আটকায় (brute-force, DDoS কমায়)।

```bash
npm i express-rate-limit
```

```js
import rateLimit from "express-rate-limit";

app.use(rateLimit({ windowMs: 15 * 60 * 1000, max: 100 }));

// login এর জন্য আলাদা কড়া limit
export const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  message: { message: "Too many login attempts, try later" },
});
```

> একাধিক server চালালে `rate-limit-redis` দিয়ে Redis এ count রাখো।

---

### `express-mongo-sanitize`
- **কাজ:** `$` ও `.` সহ malicious input সরায় — NoSQL injection ঠেকায়।
- উদাহরণ: login এ `{"email": {"$gt": ""}}` পাঠিয়ে bypass করার চেষ্টা।

```bash
npm i express-mongo-sanitize
```

```js
import mongoSanitize from "express-mongo-sanitize";
app.use(mongoSanitize());
```

> Express 5 এ সমস্যা হলে `zod` দিয়ে input validate করাই বেশি নিরাপদ।

---

### `hpp`
- **কাজ:** HTTP Parameter Pollution ঠেকায় (`?sort=a&sort=b` জাতীয় duplicate param)।

```bash
npm i hpp
```

```js
import hpp from "hpp";
app.use(hpp());
```

---

### `passport` (ঐচ্ছিক)
- **কাজ:** Google/Facebook/GitHub social login।
- সাথে strategy লাগে: `passport-google-oauth20`, `passport-facebook` ইত্যাদি।
- বিকল্প: `google-auth-library` দিয়ে সরাসরি Google ID token verify করা (সহজ)।

---

## 4. Validation

Input কখনো বিশ্বাস করবে না। Controller এ যাওয়ার আগেই validate করো।

### `zod` (সবচেয়ে জনপ্রিয়)

```bash
npm i zod
```

```js
// src/validations/user.validation.js
import { z } from "zod";

export const registerSchema = z.object({
  body: z.object({
    name: z.string().min(2),
    email: z.string().email(),
    password: z.string().min(6),
  }),
});
```

```js
// src/middlewares/validate.js
export const validate = (schema) => (req, res, next) => {
  const result = schema.safeParse({
    body: req.body,
    query: req.query,
    params: req.params,
  });
  if (!result.success)
    return res.status(400).json({
      message: "Validation failed",
      errors: result.error.issues.map((i) => ({
        field: i.path.join("."),
        message: i.message,
      })),
    });
  next();
};

// ব্যবহার: router.post("/register", validate(registerSchema), register);
```

### বিকল্প
| Package | বৈশিষ্ট্য |
|---|---|
| `joi` | পুরনো ও stable, প্রচুর tutorial |
| `express-validator` | middleware chain style |
| `validator` | শুধু string check (isEmail, isURL) |

---

## 5. Redis ও Caching

### Redis কেন?
- দ্রুত cache (বারবার DB hit কমায়)
- Session/refresh token store
- Rate limiting
- Queue (BullMQ)
- OTP/ verification code (expire সহ)

### `ioredis` (প্রস্তাবিত) বা `redis`

```bash
npm i ioredis
```

```js
// src/config/redis.js
import Redis from "ioredis";
export const redis = new Redis(process.env.REDIS_URL);

redis.on("connect", () => console.log("Redis connected"));
redis.on("error", (e) => console.error("Redis error", e));
```

**Cache middleware উদাহরণ:**

```js
export const cache = (seconds = 60) => async (req, res, next) => {
  const key = `cache:${req.originalUrl}`;
  const cached = await redis.get(key);
  if (cached) return res.json(JSON.parse(cached));

  const originalJson = res.json.bind(res);
  res.json = (data) => {
    redis.set(key, JSON.stringify(data), "EX", seconds);
    return originalJson(data);
  };
  next();
};

// router.get("/products", cache(120), getProducts);
```

**OTP উদাহরণ:**

```js
await redis.set(`otp:${email}`, "483920", "EX", 300); // ৫ মিনিট
const otp = await redis.get(`otp:${email}`);
await redis.del(`otp:${email}`);
```

> ⚠️ Data বদলালে (create/update/delete) পুরনো cache মুছতে ভুলো না (`redis.del(key)`)।

### `express-session` + `connect-redis`
- **কাজ:** Cookie-session based auth চাইলে session Redis এ রাখে। JWT ব্যবহার করলে দরকার নেই।

### `rate-limit-redis`
- **কাজ:** `express-rate-limit` এর counter Redis এ রাখে (multi-server এ দরকার)।

---

## 6. Background Jobs ও Queue

### `bullmq`
- **কাজ:** ভারী/ধীর কাজ (email পাঠানো, image process, report) background এ চালায় যাতে API দ্রুত response দেয়। **Redis লাগে।**

```bash
npm i bullmq
```

```js
// src/jobs/email.queue.js
import { Queue, Worker } from "bullmq";

const connection = { host: "127.0.0.1", port: 6379 };

export const emailQueue = new Queue("email", { connection });

// Worker (আলাদা process হলে ভালো)
new Worker(
  "email",
  async (job) => {
    await sendEmail(job.data.to, job.data.subject, job.data.html);
  },
  { connection }
);

// Controller এ ব্যবহার
await emailQueue.add("welcome", { to: user.email, subject: "Welcome", html: "..." }, {
  attempts: 3,
  backoff: { type: "exponential", delay: 3000 },
});
```

### `node-cron`
- **কাজ:** নির্দিষ্ট সময়ে কাজ চালায় (প্রতিদিন রাত ১২টায় cleanup ইত্যাদি)।

```bash
npm i node-cron
```

```js
import cron from "node-cron";
cron.schedule("0 0 * * *", () => console.log("Every midnight"));
// ফরম্যাট: মিনিট ঘন্টা দিন মাস সপ্তাহ
```

---

## 7. File Upload ও Media

### `multer`
- **কাজ:** `multipart/form-data` (file upload) handle করে।

```bash
npm i multer
```

```js
import multer from "multer";

const upload = multer({
  storage: multer.memoryStorage(), // RAM এ রাখে, তারপর cloud এ পাঠাও
  limits: { fileSize: 5 * 1024 * 1024 }, // 5MB
  fileFilter: (req, file, cb) => {
    if (!file.mimetype.startsWith("image/"))
      return cb(new Error("Only images allowed"));
    cb(null, true);
  },
});

router.post("/avatar", upload.single("avatar"), uploadAvatar);
// একাধিক: upload.array("photos", 5)
```

### `cloudinary`
- **কাজ:** Image/video cloud এ রাখে, URL দেয়, resize/optimize করে।

```bash
npm i cloudinary
```

```js
import { v2 as cloudinary } from "cloudinary";

cloudinary.config({
  cloud_name: process.env.CLOUD_NAME,
  api_key: process.env.CLOUD_KEY,
  api_secret: process.env.CLOUD_SECRET,
});

// memory buffer থেকে upload
const uploadToCloud = (buffer, folder = "uploads") =>
  new Promise((resolve, reject) => {
    cloudinary.uploader
      .upload_stream({ folder }, (err, result) => (err ? reject(err) : resolve(result)))
      .end(buffer);
  });

const result = await uploadToCloud(req.file.buffer);
console.log(result.secure_url, result.public_id);
```

### `sharp`
- **কাজ:** Image resize, compress, format convert (jpg → webp)। অনেক দ্রুত।

```bash
npm i sharp
```

```js
import sharp from "sharp";
const optimized = await sharp(req.file.buffer)
  .resize(800)
  .webp({ quality: 80 })
  .toBuffer();
```

### `@aws-sdk/client-s3`
- **কাজ:** AWS S3 তে file রাখা। বড় প্রজেক্টে common।
- সাথে `@aws-sdk/s3-request-presigner` নিলে frontend থেকে সরাসরি upload এর signed URL বানানো যায়।
- বিকল্প (সস্তা): Cloudflare R2 (S3 compatible), Backblaze B2।

---

## 8. Email ও Notification

### `nodemailer`
- **কাজ:** SMTP দিয়ে email পাঠায়।

```bash
npm i nodemailer
```

```js
import nodemailer from "nodemailer";

const transporter = nodemailer.createTransport({
  host: process.env.SMTP_HOST,
  port: 587,
  auth: { user: process.env.SMTP_USER, pass: process.env.SMTP_PASS },
});

export const sendEmail = (to, subject, html) =>
  transporter.sendMail({ from: '"My App" <no-reply@myapp.com>', to, subject, html });
```

> Gmail ব্যবহার করলে **App Password** লাগবে। Production এ Resend, SendGrid, Brevo, Amazon SES ভালো।

### `twilio`
- **কাজ:** SMS/WhatsApp/OTP পাঠানো। বাংলাদেশে লোকাল SMS gateway (BulkSMSBD, SSL Wireless) প্রায়ই সস্তা — তাদের REST API `axios` দিয়ে call করা যায়।

### অন্যান্য
- `firebase-admin`: Push notification (FCM)
- `web-push`: Browser push notification
- `ejs` / `handlebars`: Email template

---

## 9. Real-time (Socket.io)

- **কাজ:** Live chat, notification, live tracking — দুই দিকের real-time communication।

```bash
npm i socket.io
```

```js
// src/server.js
import http from "http";
import { Server } from "socket.io";
import app from "./app.js";

const server = http.createServer(app);
const io = new Server(server, { cors: { origin: process.env.CLIENT_URL } });

io.on("connection", (socket) => {
  console.log("Connected:", socket.id);

  socket.on("join", (userId) => socket.join(userId));      // private room
  socket.on("message", (data) => io.to(data.to).emit("message", data));
  socket.on("disconnect", () => console.log("Disconnected"));
});

server.listen(5000);
```

> একাধিক server চালালে `@socket.io/redis-adapter` লাগবে।

---

## 10. Payment

### `stripe`

```bash
npm i stripe
```

```js
import Stripe from "stripe";
const stripe = new Stripe(process.env.STRIPE_SECRET_KEY);

const session = await stripe.checkout.sessions.create({
  mode: "payment",
  line_items: [{ price_data: { currency: "usd", product_data: { name: "Product" }, unit_amount: 2000 }, quantity: 1 }],
  success_url: `${process.env.CLIENT_URL}/success`,
  cancel_url: `${process.env.CLIENT_URL}/cancel`,
});
```

> ⚠️ Payment confirm করবে **webhook** দিয়ে, frontend redirect দেখে না। Webhook এর জন্য `express.raw({ type: "application/json" })` লাগে।

### বাংলাদেশের জন্য
- `sslcommerz-lts`: SSLCommerz (নিচে পূর্ণাঙ্গ সেটআপ)
- bKash/Nagad: official REST API, `axios` দিয়ে integrate করতে হয়

---

## 10.1 SSLCommerz সম্পূর্ণ সেটআপ গাইড

> SSLCommerz বাংলাদেশের জনপ্রিয় payment gateway। একটাতেই bKash, Nagad, Rocket, VISA/Mastercard, Internet Banking সব পাওয়া যায়।

### 🔄 পুরো Flow একনজরে

```
Frontend            Backend                  SSLCommerz
   │  1. Pay Now      │                          │
   │ ───────────────► │  2. Order তৈরি (pending) │
   │                  │  3. sslcz.init() ──────► │
   │                  │ ◄─── GatewayPageURL ──── │
   │ ◄── URL ──────── │                          │
   │  4. User redirect হয়ে payment করে ────────► │
   │                  │ ◄─ 5. POST success/fail/cancel
   │                  │ ◄─ 6. POST IPN (server-to-server)
   │                  │  7. validate(val_id) ──► │
   │                  │  8. Order = paid         │
   │ ◄─ 9. Frontend এর success page এ redirect ─ │
```

> **সবচেয়ে গুরুত্বপূর্ণ নিয়ম:** Payment সফল হয়েছে কিনা সেটা **কখনো frontend/redirect দেখে ধরবে না**। সবসময় backend থেকে SSLCommerz এর `validate` API দিয়ে নিশ্চিত হও।

---

### ধাপ ১: Sandbox Account খোলা

1. যাও: **https://developer.sslcommerz.com/registration/**
2. Sandbox account তৈরি করো (ফ্রি)
3. Email এ **Store ID** ও **Store Password** আসবে
4. পরে live যেতে হলে SSLCommerz এর merchant panel থেকে ট্রেড লাইসেন্স/ডকুমেন্ট দিয়ে আলাদা live account নিতে হয়

> Sandbox এ আসল টাকা কাটে না। সব টেস্ট এখানেই করো।

---

### ধাপ ২: Install ও Environment

```bash
npm i sslcommerz-lts uuid
```

```
# .env
SSLCZ_STORE_ID=testbox
SSLCZ_STORE_PASSWORD=qwerty
SSLCZ_IS_LIVE=false

# তোমার backend এর public URL (local এ ngrok লাগবে, নিচে দেখো)
SERVER_URL=http://localhost:5000
CLIENT_URL=http://localhost:5173
```

> ⚠️ উপরের `testbox/qwerty` শুধু উদাহরণ। নিজের sandbox এর Store ID ও Password বসাও।
> Live এ গেলে `SSLCZ_IS_LIVE=true` আর live credentials দিতে হবে।

---

### ধাপ ৩: Order Model

```js
// src/models/order.model.js
import mongoose from "mongoose";

const orderSchema = new mongoose.Schema(
  {
    user: { type: mongoose.Schema.Types.ObjectId, ref: "User", required: true },
    items: [
      {
        product: { type: mongoose.Schema.Types.ObjectId, ref: "Product" },
        name: String,
        price: Number,
        quantity: Number,
      },
    ],
    totalAmount: { type: Number, required: true },
    currency: { type: String, default: "BDT" },

    transactionId: { type: String, unique: true, required: true }, // আমাদের তৈরি tran_id
    paymentStatus: {
      type: String,
      enum: ["pending", "paid", "failed", "cancelled", "refunded"],
      default: "pending",
    },
    valId: String,        // SSLCommerz এর validation ID
    bankTranId: String,
    cardType: String,     // bKash, VISA, ইত্যাদি
    paidAt: Date,

    shippingAddress: {
      address: String,
      city: String,
      postcode: String,
      country: { type: String, default: "Bangladesh" },
      phone: String,
    },
  },
  { timestamps: true }
);

export default mongoose.model("Order", orderSchema);
```

---

### ধাপ ৪: SSLCommerz Config

```js
// src/config/sslcommerz.js
import SSLCommerzPayment from "sslcommerz-lts";

export const sslcz = new SSLCommerzPayment(
  process.env.SSLCZ_STORE_ID,
  process.env.SSLCZ_STORE_PASSWORD,
  process.env.SSLCZ_IS_LIVE === "true"
);
```

> ESM এ `import SSLCommerzPayment from "sslcommerz-lts"` কাজ না করলে `import pkg from "sslcommerz-lts"; const SSLCommerzPayment = pkg.default || pkg;` ব্যবহার করো।
> CommonJS এ: `const SSLCommerzPayment = require("sslcommerz-lts");`

---

### ধাপ ৫: Payment শুরু করা (Init)

```js
// src/controllers/payment.controller.js
import { v4 as uuidv4 } from "uuid";
import Order from "../models/order.model.js";
import Product from "../models/product.model.js";
import { sslcz } from "../config/sslcommerz.js";

export const initPayment = async (req, res, next) => {
  try {
    const { items, shippingAddress } = req.body;
    const user = req.user;

    // ✅ দাম কখনো frontend থেকে নেবে না — DB থেকে হিসাব করো
    let totalAmount = 0;
    const orderItems = [];
    for (const item of items) {
      const product = await Product.findById(item.productId);
      if (!product) return res.status(404).json({ message: "Product not found" });
      totalAmount += product.price * item.quantity;
      orderItems.push({
        product: product._id,
        name: product.name,
        price: product.price,
        quantity: item.quantity,
      });
    }

    const transactionId = `TXN_${uuidv4()}`;

    // Order তৈরি (pending)
    await Order.create({
      user: user._id,
      items: orderItems,
      totalAmount,
      transactionId,
      shippingAddress,
    });

    const data = {
      total_amount: totalAmount,
      currency: "BDT",
      tran_id: transactionId, // unique হতেই হবে
      success_url: `${process.env.SERVER_URL}/api/v1/payment/success/${transactionId}`,
      fail_url: `${process.env.SERVER_URL}/api/v1/payment/fail/${transactionId}`,
      cancel_url: `${process.env.SERVER_URL}/api/v1/payment/cancel/${transactionId}`,
      ipn_url: `${process.env.SERVER_URL}/api/v1/payment/ipn`,
      shipping_method: "Courier",
      product_name: "Order Payment",
      product_category: "General",
      product_profile: "general",

      // Customer তথ্য (সব required)
      cus_name: user.name,
      cus_email: user.email,
      cus_add1: shippingAddress.address,
      cus_city: shippingAddress.city,
      cus_postcode: shippingAddress.postcode,
      cus_country: "Bangladesh",
      cus_phone: shippingAddress.phone,

      // Shipping তথ্য (shipping_method "NO" না হলে required)
      ship_name: user.name,
      ship_add1: shippingAddress.address,
      ship_city: shippingAddress.city,
      ship_postcode: shippingAddress.postcode,
      ship_country: "Bangladesh",
    };

    const apiResponse = await sslcz.init(data);

    if (!apiResponse?.GatewayPageURL) {
      return res.status(502).json({ message: "Payment session তৈরি করা যায়নি" });
    }

    res.json({ success: true, url: apiResponse.GatewayPageURL });
  } catch (err) {
    next(err);
  }
};
```

---

### ধাপ ৬: Success / Fail / Cancel / IPN Handler

SSLCommerz এই URL গুলোতে **POST** request পাঠায়। তাই `express.urlencoded({ extended: true })` চালু থাকতেই হবে।

```js
// একই controller ফাইলে

// Validation এর common function (success ও IPN দুটোতেই লাগে)
const verifyAndMarkPaid = async (valId, transactionId) => {
  const order = await Order.findOne({ transactionId });
  if (!order) return { ok: false, reason: "Order not found" };

  // Idempotency: আগেই paid হলে আবার process করো না
  if (order.paymentStatus === "paid") return { ok: true, order };

  // SSLCommerz এর সার্ভার থেকে নিশ্চিত হও
  const validation = await sslcz.validate({ val_id: valId });

  const isValidStatus =
    validation.status === "VALID" || validation.status === "VALIDATED";

  // ✅ তিনটা জিনিস মেলাতে হবে: status, tran_id, amount
  const isSameTran = validation.tran_id === order.transactionId;
  const isSameAmount =
    Number(validation.amount) === Number(order.totalAmount) ||
    Number(validation.currency_amount) === Number(order.totalAmount);

  if (!isValidStatus || !isSameTran || !isSameAmount) {
    order.paymentStatus = "failed";
    await order.save();
    return { ok: false, reason: "Validation mismatch" };
  }

  order.paymentStatus = "paid";
  order.valId = valId;
  order.bankTranId = validation.bank_tran_id;
  order.cardType = validation.card_type;
  order.paidAt = new Date();
  await order.save();

  return { ok: true, order };
};

// 1) Success — user কে ফেরত পাঠায়
export const paymentSuccess = async (req, res) => {
  const { tranId } = req.params;
  const { val_id } = req.body;

  try {
    const result = await verifyAndMarkPaid(val_id, tranId);
    if (result.ok) {
      return res.redirect(`${process.env.CLIENT_URL}/payment/success?tran_id=${tranId}`);
    }
    return res.redirect(`${process.env.CLIENT_URL}/payment/fail?tran_id=${tranId}`);
  } catch (err) {
    return res.redirect(`${process.env.CLIENT_URL}/payment/fail?tran_id=${tranId}`);
  }
};

// 2) Fail
export const paymentFail = async (req, res) => {
  const { tranId } = req.params;
  await Order.findOneAndUpdate(
    { transactionId: tranId, paymentStatus: "pending" },
    { paymentStatus: "failed" }
  );
  res.redirect(`${process.env.CLIENT_URL}/payment/fail?tran_id=${tranId}`);
};

// 3) Cancel
export const paymentCancel = async (req, res) => {
  const { tranId } = req.params;
  await Order.findOneAndUpdate(
    { transactionId: tranId, paymentStatus: "pending" },
    { paymentStatus: "cancelled" }
  );
  res.redirect(`${process.env.CLIENT_URL}/payment/cancel?tran_id=${tranId}`);
};

// 4) IPN — user browser বন্ধ করে দিলেও SSLCommerz সরাসরি server কে জানায়
export const paymentIPN = async (req, res) => {
  try {
    const { val_id, tran_id, status } = req.body;
    if (!val_id || !tran_id) return res.status(400).send("Invalid IPN");

    if (status === "VALID" || status === "VALIDATED") {
      await verifyAndMarkPaid(val_id, tran_id);
    }
    res.status(200).send("IPN received");
  } catch (err) {
    res.status(500).send("IPN error");
  }
};
```

**IPN কেন দরকার?** User payment করার পর browser বন্ধ করে দিলে বা net চলে গেলে success URL হিট নাও হতে পারে। IPN হলো SSLCommerz → তোমার server এর সরাসরি যোগাযোগ, তাই payment হারিয়ে যায় না।

---

### ধাপ ৭: Routes

```js
// src/routes/payment.routes.js
import express from "express";
import { protect } from "../middlewares/auth.js";
import {
  initPayment,
  paymentSuccess,
  paymentFail,
  paymentCancel,
  paymentIPN,
} from "../controllers/payment.controller.js";

const router = express.Router();

// শুধু init এ login লাগবে
router.post("/init", protect, initPayment);

// নিচের গুলো SSLCommerz কল করে — protect দেওয়া যাবে না!
router.post("/success/:tranId", paymentSuccess);
router.post("/fail/:tranId", paymentFail);
router.post("/cancel/:tranId", paymentCancel);
router.post("/ipn", paymentIPN);

export default router;
```

```js
// app.js
import paymentRoutes from "./routes/payment.routes.js";
app.use("/api/v1/payment", paymentRoutes);
```

> ⚠️ `success/fail/cancel/ipn` route এ **auth middleware** ও **CSRF** দিও না। আর `express-rate-limit` এর limit এ যেন আটকে না যায় সেটা খেয়াল রাখো।
> Cross-site POST থেকে cookie না আসায় এখানে `req.user` থাকবে না, তাই transaction ID দিয়েই order খুঁজতে হয়।

---

### ধাপ ৮: Frontend (React)

```jsx
// Checkout.jsx
import axios from "axios";

const handlePay = async () => {
  try {
    const { data } = await axios.post(
      `${import.meta.env.VITE_API_URL}/api/v1/payment/init`,
      { items: cartItems, shippingAddress },
      { withCredentials: true }
    );
    window.location.href = data.url; // SSLCommerz এর payment page এ নিয়ে যায়
  } catch (err) {
    alert(err.response?.data?.message || "Payment শুরু করা যায়নি");
  }
};
```

```jsx
// PaymentSuccess.jsx  (route: /payment/success)
import { useSearchParams } from "react-router-dom";

export default function PaymentSuccess() {
  const [params] = useSearchParams();
  const tranId = params.get("tran_id");
  return <h2>✅ Payment সফল! Transaction: {tranId}</h2>;
}
```

> Success page এ শুধু মেসেজ দেখাও। চাইলে `GET /api/v1/orders/:tranId` দিয়ে backend থেকে আসল order status এনে দেখাতে পারো।

---

### ধাপ ৯: Local এ টেস্ট করার সমস্যা (ngrok)

SSLCommerz এর server তোমার `localhost` এ **IPN পাঠাতে পারে না**। (Browser redirect কাজ করবে, কারণ সেটা তোমার browser থেকেই হয়।) IPN টেস্ট করতে public URL লাগবে:

```bash
npx ngrok http 5000
# পাওয়া URL: https://abcd-1234.ngrok-free.app
```

তারপর `.env` তে:
```
SERVER_URL=https://abcd-1234.ngrok-free.app
```

আর SSLCommerz sandbox merchant panel এ **IPN Listener URL** সেট করো:
`https://abcd-1234.ngrok-free.app/api/v1/payment/ipn`

---

### ধাপ ১০: Sandbox এ টেস্ট কার্ড

Payment page এ এই test credential গুলো কাজ করে (sandbox এ):

| ধরন | নম্বর | Expiry | CVV |
|---|---|---|---|
| VISA | `4111111111111111` | যেকোনো ভবিষ্যৎ তারিখ | `111` |
| Mastercard | `5111111111111111` | যেকোনো ভবিষ্যৎ তারিখ | `111` |

- Mobile banking (bKash ইত্যাদি) sandbox এর test number ও OTP SSLCommerz এর ডকুমেন্টেশন পেজে দেওয়া থাকে।
- OTP চাইলে সাধারণত `111111` চলে।

> Test credential বদলাতে পারে, তাই না চললে নিচের official ডকুমেন্টেশন দেখো।

---

### Extra: Transaction Query ও Refund

```js
// Transaction এর অবস্থা দেখা (IPN মিস হলে manual check)
const result = await sslcz.transactionQueryByTransactionId({ tran_id: "TXN_xxx" });
console.log(result.element[0].status);

// Refund শুরু করা
const refund = await sslcz.initiateRefund({
  refund_amount: 500,
  refund_remarks: "Customer requested",
  bank_tran_id: order.bankTranId,   // success এর সময় সেভ করা ছিল
  refe_id: `REF_${Date.now()}`,
});

// Refund এর অবস্থা
const status = await sslcz.refundQuery({ refund_ref_id: refund.refund_ref_id });
```

> Refund এর জন্য merchant panel এ refund permission থাকা লাগতে পারে।

---

### 🔒 SSLCommerz Security Checklist

- [ ] Amount সবসময় **DB থেকে হিসাব** করা, frontend থেকে নয়
- [ ] `tran_id` প্রতিবার **unique** (uuid ব্যবহার)
- [ ] Success ও IPN দুটোতেই `validate` API call করা হচ্ছে
- [ ] Validation এ **status + tran_id + amount** তিনটাই মেলানো হচ্ছে
- [ ] Order আগেই `paid` থাকলে দ্বিতীয়বার process হচ্ছে না (idempotency)
- [ ] Store ID/Password `.env` এ, git এ নেই
- [ ] Live এ **HTTPS** URL ব্যবহার (success/fail/cancel/ipn সব)
- [ ] Callback route এ auth middleware নেই
- [ ] `pending` order অনেকক্ষণ পড়ে থাকলে cron job দিয়ে query করে status ঠিক করা (`node-cron` + `transactionQueryByTransactionId`)

### 🚀 Live এ যাওয়ার আগে

1. SSLCommerz merchant account approve করাও (ডকুমেন্ট জমা দিয়ে)
2. `.env` তে live Store ID/Password ও `SSLCZ_IS_LIVE=true`
3. সব callback URL ও `SERVER_URL` **HTTPS** domain এ বদলাও
4. Live merchant panel এ IPN URL সেট করো
5. ছোট amount (যেমন ১০ টাকা) দিয়ে একটা আসল টেস্ট করো

### ❗ সাধারণ সমস্যা ও সমাধান

| সমস্যা | কারণ ও সমাধান |
|---|---|
| `GatewayPageURL` আসছে না | Store ID/Password ভুল, বা required field (cus_*, ship_*) খালি। `apiResponse.failedreason` print করে দেখো |
| `Duplicate tran_id` | একই `tran_id` দুইবার ব্যবহার হয়েছে, প্রতিবার নতুন uuid বানাও |
| Success URL এ `req.body` খালি | `express.urlencoded({ extended: true })` নেই |
| IPN আসছে না | Localhost এ আছো (ngrok লাগবে), অথবা panel এ IPN URL সেট করা নেই |
| Validation `INVALID_TRANSACTION` | `val_id` ভুল বা payment আসলে সম্পন্ন হয়নি |
| Amount mismatch | Currency আলাদা (USD card), `currency_amount` ও `amount` দুটোই চেক করো |
| ESM import error | উপরের ধাপ ৪ এর নোট দেখো |

### 📚 Official রেফারেন্স

- Developer Portal ও ডকুমেন্টেশন: https://developer.sslcommerz.com
- NPM package: https://www.npmjs.com/package/sslcommerz-lts

> Field নাম, test credential বা নিয়ম বদলাতে পারে। Live এ যাওয়ার আগে একবার official ডকুমেন্টেশন মিলিয়ে নিও।

---

## 11. Logging ও Error Handling

### `morgan`
- **কাজ:** প্রতিটি HTTP request console এ log করে।

```bash
npm i morgan
```

```js
import morgan from "morgan";
if (process.env.NODE_ENV === "development") app.use(morgan("dev"));
```

### `winston` বা `pino`
- **কাজ:** Production-grade logging (file, level, JSON format)।
- `pino` বেশি দ্রুত, `winston` বেশি flexible।

```js
import winston from "winston";

export const logger = winston.createLogger({
  level: "info",
  format: winston.format.combine(winston.format.timestamp(), winston.format.json()),
  transports: [
    new winston.transports.File({ filename: "logs/error.log", level: "error" }),
    new winston.transports.File({ filename: "logs/combined.log" }),
    new winston.transports.Console(),
  ],
});
```

### Error Handling Pattern (package ছাড়াই সবচেয়ে ভালো)

```js
// src/utils/ApiError.js
export class ApiError extends Error {
  constructor(statusCode, message) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
  }
}

// src/utils/catchAsync.js  (try-catch বারবার লেখা বাঁচায়)
export const catchAsync = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);
```

```js
// src/middlewares/error.js  (সবার শেষে use করো)
export const notFound = (req, res, next) =>
  next(new ApiError(404, `Route ${req.originalUrl} not found`));

export const errorHandler = (err, req, res, next) => {
  let status = err.statusCode || 500;
  let message = err.message || "Server Error";

  if (err.name === "ValidationError") { status = 400; }
  if (err.code === 11000) { status = 409; message = "Duplicate value"; }
  if (err.name === "CastError") { status = 400; message = "Invalid ID"; }
  if (err.name === "JsonWebTokenError") { status = 401; message = "Invalid token"; }

  res.status(status).json({
    success: false,
    message,
    ...(process.env.NODE_ENV === "development" && { stack: err.stack }),
  });
};

// app.js এর শেষে:
// app.use(notFound);
// app.use(errorHandler);
```

**Controller এ ব্যবহার:**

```js
export const getUser = catchAsync(async (req, res) => {
  const user = await User.findById(req.params.id);
  if (!user) throw new ApiError(404, "User not found");
  res.json({ success: true, data: user });
});
```

> `express-async-errors` package দিয়েও async error auto-catch করা যায়। **Express 5** এ এটা built-in, আলাদা package লাগে না।
> `http-errors` / `http-status`: status code constant (`httpStatus.NOT_FOUND`)।

---

## 12. Utility Packages

| Package | কাজ | উদাহরণ |
|---|---|---|
| `lodash` | array/object helper | `_.pick(obj, ["a","b"])` |
| `dayjs` | তারিখ/সময় | `dayjs().add(7,"day").format("YYYY-MM-DD")` |
| `uuid` | unique ID | `import { v4 } from "uuid"; v4()` |
| `nanoid` | ছোট unique ID | `nanoid(10)` |
| `slugify` | URL slug | `slugify("My Post", { lower: true })` → `my-post` |
| `axios` | বাইরের API call | `await axios.get(url)` |
| `crypto` (built-in) | random token, hash | `crypto.randomBytes(32).toString("hex")` |

> Node 18+ এ `fetch` built-in, তাই ছোট কাজে `axios` না লাগলেও চলে।
> `moment` ব্যবহার করো না — deprecated, `dayjs` বা `date-fns` নাও।

---

## 13. API Documentation

### `swagger-ui-express` + `swagger-jsdoc`
- **কাজ:** তোমার API এর interactive documentation (`/api-docs`)। Frontend dev বা টিম এর জন্য দরকারি।

```bash
npm i swagger-ui-express swagger-jsdoc
```

```js
import swaggerUi from "swagger-ui-express";
import swaggerJsdoc from "swagger-jsdoc";

const spec = swaggerJsdoc({
  definition: { openapi: "3.0.0", info: { title: "My API", version: "1.0.0" } },
  apis: ["./src/routes/*.js"],
});

app.use("/api-docs", swaggerUi.serve, swaggerUi.setup(spec));
```

> বিকল্প: Postman collection export করে শেয়ার করা।

---

## 14. Dev Tools ও Testing

### `nodemon`
- ফাইল save করলে server auto restart।
- Node 18.11+ এ `node --watch src/server.js` দিয়েও হয়।

```json
"scripts": {
  "dev": "nodemon src/server.js",
  "start": "node src/server.js"
}
```

### TypeScript সেটআপ

```bash
npm i -D typescript tsx @types/node @types/express @types/cors @types/cookie-parser @types/jsonwebtoken @types/bcryptjs @types/multer
npx tsc --init
```

```json
"scripts": {
  "dev": "tsx watch src/server.ts",
  "build": "tsc",
  "start": "node dist/server.js"
}
```

### `eslint` + `prettier`
- Code এর ভুল ধরা ও স্টাইল ঠিক রাখা।
```bash
npm i -D eslint prettier eslint-config-prettier
```

### `husky` + `lint-staged`
- Git commit এর আগে অটো lint/format চালায়।

### Testing: `jest` / `vitest` + `supertest` + `mongodb-memory-server`

```bash
npm i -D vitest supertest mongodb-memory-server
```

```js
import request from "supertest";
import app from "../src/app.js";

describe("Auth API", () => {
  it("should register a user", async () => {
    const res = await request(app)
      .post("/api/auth/register")
      .send({ name: "Rahim", email: "rahim@test.com", password: "123456" });

    expect(res.statusCode).toBe(201);
  });
});
```

- `supertest`: server চালু না করেই API test
- `mongodb-memory-server`: আসল DB না ছুঁয়ে memory তে temporary MongoDB

---

## 15. Production ও Deployment

| Package/Tool | কাজ |
|---|---|
| `pm2` | process manager, crash হলে auto restart, cluster mode |
| `cross-env` | Windows/Linux এ একইভাবে `NODE_ENV` সেট |
| `@sentry/node` | production error tracking |
| `prom-client` | metrics/monitoring |
| Docker | container এ deploy |

```bash
npm i -g pm2
pm2 start src/server.js --name my-api -i max
pm2 logs
pm2 save && pm2 startup
```

**Health check route** (load balancer/uptime monitor এর জন্য):

```js
app.get("/health", (req, res) => res.json({ status: "ok", uptime: process.uptime() }));
```

**Graceful shutdown:**

```js
process.on("SIGTERM", async () => {
  server.close(async () => {
    await mongoose.connection.close();
    await redis.quit();
    process.exit(0);
  });
});
```

---

## 16. অতিরিক্ত গুরুত্বপূর্ণ Packages

| Package | কাজ |
|---|---|
| `mongoose-paginate-v2` | pagination সহজ করে |
| `mongoose-aggregate-paginate-v2` | aggregation এ pagination |
| `http-status` | status code constant |
| `cross-env` | env variable cross-platform |
| `google-auth-library` | Google login verify |
| `firebase-admin` | push notification, Firebase auth |
| `i18next` | বহুভাষা সাপোর্ট |
| `ejs` / `handlebars` / `mjml` | email template |
| `pdfkit` / `puppeteer` | PDF তৈরি |
| `exceljs` / `xlsx` | Excel import/export |
| `csv-parser` / `papaparse` | CSV পড়া |
| `qrcode` | QR code তৈরি |
| `otplib` | 2FA / TOTP |
| `ua-parser-js` | device/browser detect |
| `geoip-lite` | IP থেকে location |
| `graphql` + `@apollo/server` | GraphQL API |
| `agenda` / `bull-board` | job dashboard |
| `@socket.io/redis-adapter` | multi-server socket |
| `rate-limiter-flexible` | advanced rate limiting |
| `jsdom` + `dompurify` | HTML sanitize (XSS রোধ) |

**Pagination উদাহরণ (package ছাড়াই):**

```js
const page = Number(req.query.page) || 1;
const limit = Number(req.query.limit) || 10;

const [data, total] = await Promise.all([
  Product.find().sort("-createdAt").skip((page - 1) * limit).limit(limit),
  Product.countDocuments(),
]);

res.json({ data, page, totalPages: Math.ceil(total / limit), total });
```

---

## 17. ⚠️ Deprecated / এড়িয়ে চলার Packages

| ব্যবহার করো না | কেন | বদলে নাও |
|---|---|---|
| `body-parser` | Express এ built-in | `express.json()` |
| `moment` | maintenance mode | `dayjs`, `date-fns` |
| `request` | deprecated | `axios`, `fetch` |
| `csurf` | deprecated | `csrf-csrf` বা SameSite cookie |
| `xss-clean` | deprecated | `zod` validation + `dompurify` |
| `node-uuid` | পুরনো | `uuid` |
| `bcrypt` (Windows এ সমস্যা) | build error | `bcryptjs` |

---

## 18. Best Practices Checklist

**Security**
- [ ] `helmet`, `cors` (নির্দিষ্ট origin), `rate-limit` চালু
- [ ] Password `bcrypt` দিয়ে hash (salt rounds 10–12)
- [ ] JWT secret লম্বা ও random, `.env` এ
- [ ] Token httpOnly cookie তে (localStorage এর চেয়ে নিরাপদ)
- [ ] সব input validate (zod/joi)
- [ ] Error response এ stack trace production এ দেখাবে না
- [ ] `npm audit` নিয়মিত চালাও

**Performance**
- [ ] `compression` চালু
- [ ] Mongo তে দরকারি field এ index
- [ ] `.lean()` ব্যবহার (শুধু read করলে): `User.find().lean()`
- [ ] `select()` দিয়ে শুধু দরকারি field আনো
- [ ] বারবার চাওয়া data Redis এ cache
- [ ] ভারী কাজ BullMQ queue তে
- [ ] সবসময় pagination

**Code Quality**
- [ ] Controller / Service / Model আলাদা
- [ ] Centralized error handler
- [ ] `.env.example` আছে
- [ ] API versioning: `/api/v1/...`
- [ ] ধারাবাহিক response format: `{ success, message, data }`
- [ ] Git commit এ `.env` ও `node_modules` নেই

**Standard Response Format:**

```js
// সফল
{ "success": true, "message": "User created", "data": { } }

// ব্যর্থ
{ "success": false, "message": "Email already exists" }
```

---

## 19. Quick Install Cheat Sheet

**Basic**
```bash
npm i express mongoose dotenv cors cookie-parser compression helmet jsonwebtoken bcryptjs zod morgan express-rate-limit
npm i -D nodemon
```

**Full-featured**
```bash
npm i express mongoose dotenv cors cookie-parser compression helmet jsonwebtoken bcryptjs zod morgan express-rate-limit express-mongo-sanitize hpp ioredis sslcommerz-lts rate-limit-redis bullmq node-cron multer cloudinary sharp nodemailer socket.io winston dayjs uuid slugify axios swagger-ui-express swagger-jsdoc mongoose-paginate-v2 http-status
npm i -D nodemon eslint prettier vitest supertest mongodb-memory-server
```

**TypeScript এর জন্য অতিরিক্ত**
```bash
npm i -D typescript tsx @types/node @types/express @types/cors @types/cookie-parser @types/jsonwebtoken @types/bcryptjs @types/multer @types/morgan @types/compression
```

---

## 🧩 পূর্ণাঙ্গ app.js নমুনা (সব একসাথে)

```js
import express from "express";
import cors from "cors";
import helmet from "helmet";
import morgan from "morgan";
import compression from "compression";
import cookieParser from "cookie-parser";
import mongoSanitize from "express-mongo-sanitize";
import hpp from "hpp";
import rateLimit from "express-rate-limit";

import authRoutes from "./routes/auth.routes.js";
import { notFound, errorHandler } from "./middlewares/error.js";

const app = express();

// Security
app.use(helmet());
app.use(cors({ origin: process.env.CLIENT_URL, credentials: true }));
app.use(rateLimit({ windowMs: 15 * 60 * 1000, max: 200 }));

// Parsers
app.use(express.json({ limit: "10kb" }));
app.use(express.urlencoded({ extended: true }));
app.use(cookieParser());

// Sanitize
app.use(mongoSanitize());
app.use(hpp());

// Utilities
app.use(compression());
if (process.env.NODE_ENV === "development") app.use(morgan("dev"));

// Routes
app.get("/health", (req, res) => res.json({ status: "ok" }));
app.use("/api/v1/auth", authRoutes);

// Errors (সবার শেষে)
app.use(notFound);
app.use(errorHandler);

export default app;
```

---

*সবশেষে: সব package একসাথে install করার দরকার নেই। প্রজেক্টের যতটুকু দরকার ততটুকুই নাও — অপ্রয়োজনীয় package মানে বাড়তি security ঝুঁকি ও ধীর build।* 🚀


---
---

# 🚀 PART 2 — Advanced: Monolith, Microservice, SaaS

> এই অংশ Part 1 এর পরের ধাপ। Part 1 এর সব বুঝে থাকলে এখান থেকে শুরু করো।
> শেষে (সেকশন 25) আছে **দিনে ২-৩টা করে practice করার রোডম্যাপ**।

---

## 20. Advanced Monolith (বড় ও ভরসাযোগ্য backend)

### 20.1 Environment Validation (`zod`)

`.env` এ কিছু ভুল বা missing থাকলে server **চালুই হবে না** — production এ অদ্ভুত bug এর হাত থেকে বাঁচায়।

```js
// src/config/env.js
import "dotenv/config";
import { z } from "zod";

const schema = z.object({
  NODE_ENV: z.enum(["development", "test", "production"]).default("development"),
  PORT: z.coerce.number().default(5000),
  MONGO_URI: z.string().min(1),
  REDIS_URL: z.string().min(1),
  JWT_SECRET: z.string().min(32, "JWT_SECRET কমপক্ষে ৩২ অক্ষরের হতে হবে"),
  CLIENT_URL: z.string().url(),
});

const parsed = schema.safeParse(process.env);
if (!parsed.success) {
  console.error("❌ Invalid env:", parsed.error.flatten().fieldErrors);
  process.exit(1);
}

export const env = parsed.data;
// এখন সব জায়গায় process.env এর বদলে env.PORT, env.MONGO_URI ব্যবহার করো
```

বিকল্প: `envalid` package।

---

### 20.2 MongoDB Transaction (টাকা/stock/order এর কাজে বাধ্যতামূলক)

**সমস্যা:** দুইটা কাজ (যেমন A এর wallet থেকে টাকা কাটা ও B তে যোগ করা) একটা হয়ে অন্যটা fail করলে data নষ্ট হয়।
**সমাধান:** Transaction — হয় সব হবে, নয় কিছুই হবে না।

```js
import mongoose from "mongoose";

export const transferMoney = async (fromId, toId, amount) => {
  const session = await mongoose.startSession();
  try {
    await session.withTransaction(async () => {
      const from = await Wallet.findOneAndUpdate(
        { _id: fromId, balance: { $gte: amount } }, // পর্যাপ্ত balance আছে কিনা
        { $inc: { balance: -amount } },
        { session, new: true }
      );
      if (!from) throw new Error("Insufficient balance");

      await Wallet.updateOne({ _id: toId }, { $inc: { balance: amount } }, { session });
      await Transaction.create([{ from: fromId, to: toId, amount }], { session });
    });
  } finally {
    session.endSession();
  }
};
```

⚠️ **Replica Set ছাড়া transaction চলে না।** MongoDB Atlas এ default থাকে। Local এ চালাতে:

```yaml
# docker-compose.yml
services:
  mongo:
    image: mongo:7
    command: ["--replSet", "rs0", "--bind_ip_all"]
    ports: ["27017:27017"]
    healthcheck:
      test: >
        mongosh --quiet --eval
        "try { rs.status().ok } catch { rs.initiate({_id:'rs0',members:[{_id:0,host:'localhost:27017'}]}).ok }"
      interval: 5s
      retries: 10
    volumes: [mongo_data:/data/db]
volumes:
  mongo_data:
```

Connection string: `mongodb://localhost:27017/mydb?replicaSet=rs0&directConnection=true`

**টিপস:** Transaction এর ভেতরে প্রতিটি query তে `{ session }` দিতে ভুলো না। আর সম্ভব হলে atomic operator (`$inc`, `findOneAndUpdate` condition সহ) ব্যবহার করো, সেটাই সবচেয়ে সহজ ও দ্রুত।

---

### 20.3 Idempotency Key (একই request দুইবার এলে দুইবার কাজ না হওয়া)

Payment/order এ user দুইবার click করলে বা net retry করলে double charge হতে পারে।

```js
// src/middlewares/idempotency.js
import { redis } from "../config/redis.js";

export const idempotency = async (req, res, next) => {
  const key = req.headers["idempotency-key"];
  if (!key) return res.status(400).json({ message: "Idempotency-Key header দরকার" });

  const redisKey = `idem:${req.user._id}:${key}`;

  // আগে সম্পন্ন হয়ে থাকলে সেই response ই ফেরত দাও
  const cached = await redis.get(redisKey);
  if (cached) {
    const { status, body } = JSON.parse(cached);
    return res.status(status).json(body);
  }

  // একই সময়ে দুইটা request ঠেকাতে lock (NX = শুধু না থাকলে সেট)
  const locked = await redis.set(`${redisKey}:lock`, "1", "EX", 30, "NX");
  if (!locked) return res.status(409).json({ message: "Request processing হচ্ছে" });

  const originalJson = res.json.bind(res);
  res.json = (body) => {
    redis.set(redisKey, JSON.stringify({ status: res.statusCode, body }), "EX", 86400);
    redis.del(`${redisKey}:lock`);
    return originalJson(body);
  };
  next();
};

// router.post("/orders", protect, idempotency, createOrder);
```

Frontend: প্রতিটি "Pay" click এ `crypto.randomUUID()` দিয়ে key বানিয়ে header এ পাঠাও, retry তে একই key।

---

### 20.4 Structured Logging + Request ID (`pino`, `pino-http`, `AsyncLocalStorage`)

Production এ `console.log` দিয়ে debug করা প্রায় অসম্ভব। প্রতিটি request এর একটা ID থাকলে সব log একসাথে খুঁজে পাওয়া যায়।

```bash
npm i pino pino-http
npm i -D pino-pretty
```

```js
// src/utils/requestContext.js
import { AsyncLocalStorage } from "node:async_hooks";
export const requestContext = new AsyncLocalStorage();
export const getRequestId = () => requestContext.getStore()?.requestId;
```

```js
// src/utils/logger.js
import pino from "pino";
import { getRequestId } from "./requestContext.js";

export const logger = pino({
  level: process.env.NODE_ENV === "production" ? "info" : "debug",
  redact: ["req.headers.authorization", "req.headers.cookie", "password", "*.password"], // secret লুকাও
  mixin: () => ({ requestId: getRequestId() }), // প্রতিটি log এ requestId অটো
  transport:
    process.env.NODE_ENV !== "production"
      ? { target: "pino-pretty" }
      : undefined,
});
```

```js
// src/middlewares/requestId.js
import { randomUUID } from "node:crypto";
import { requestContext } from "../utils/requestContext.js";

export const requestId = (req, res, next) => {
  const id = req.headers["x-request-id"] || randomUUID();
  res.setHeader("x-request-id", id);
  requestContext.run({ requestId: id }, next);
};
```

```js
// app.js
import pinoHttp from "pino-http";
app.use(requestId);
app.use(pinoHttp({ logger, genReqId: (req, res) => res.getHeader("x-request-id") }));
```

এখন যেকোনো জায়গায় `logger.info({ orderId }, "Order created")` লিখলে requestId সহ log হবে।

---

### 20.5 Health Check ও Graceful Shutdown

Deploy এর সময় চলমান request মাঝপথে কেটে যেন না যায়।

```js
// src/server.js
import http from "node:http";
import mongoose from "mongoose";
import app from "./app.js";
import { redis } from "./config/redis.js";
import { logger } from "./utils/logger.js";

const server = http.createServer(app);

// Liveness: process বেঁচে আছে?
app.get("/health/live", (req, res) => res.json({ status: "ok" }));

// Readiness: DB ও Redis তৈরি?
app.get("/health/ready", async (req, res) => {
  const mongoOk = mongoose.connection.readyState === 1;
  const redisOk = redis.status === "ready";
  const ok = mongoOk && redisOk;
  res.status(ok ? 200 : 503).json({ mongo: mongoOk, redis: redisOk });
});

const shutdown = async (signal) => {
  logger.info(`${signal} received, shutting down...`);
  server.close(async () => {
    await mongoose.connection.close();
    await redis.quit();
    process.exit(0);
  });
  // ১০ সেকেন্ডে না থামলে জোর করে বন্ধ
  setTimeout(() => process.exit(1), 10_000).unref();
};

process.on("SIGTERM", () => shutdown("SIGTERM"));
process.on("SIGINT", () => shutdown("SIGINT"));

process.on("unhandledRejection", (err) => logger.error({ err }, "Unhandled rejection"));
process.on("uncaughtException", (err) => {
  logger.fatal({ err }, "Uncaught exception");
  process.exit(1);
});
```

বিকল্প package: `@godaddy/terminus`।

---

### 20.6 Soft Delete

Data সরাসরি মুছে না ফেলে `deletedAt` সেট করা, যাতে ভুল হলে ফেরত আনা যায় (SaaS এ প্রায় বাধ্যতামূলক)।

```js
// src/models/plugins/softDelete.js
export function softDeletePlugin(schema) {
  schema.add({ deletedAt: { type: Date, default: null, index: true } });

  ["find", "findOne", "findOneAndUpdate", "countDocuments", "updateMany", "updateOne"].forEach((op) => {
    schema.pre(op, function () {
      if (!this.getOptions().withDeleted) this.where({ deletedAt: null });
    });
  });

  schema.methods.softDelete = function () {
    this.deletedAt = new Date();
    return this.save();
  };
  schema.methods.restore = function () {
    this.deletedAt = null;
    return this.save();
  };
}

// ব্যবহার
// projectSchema.plugin(softDeletePlugin);
// await project.softDelete();
// await Project.find({}, null, { withDeleted: true }); // মুছাগুলোসহ
```

রেডিমেড package: `mongoose-delete`।

---

### 20.7 Refresh Token Rotation (Redis সহ, নিরাপদ auth)

```js
// src/services/token.service.js
import jwt from "jsonwebtoken";
import crypto from "node:crypto";
import { redis } from "../config/redis.js";

const hash = (t) => crypto.createHash("sha256").update(t).digest("hex");

export const issueTokens = async (user) => {
  const accessToken = jwt.sign({ id: user._id, role: user.role }, process.env.JWT_SECRET, { expiresIn: "15m" });
  const refreshToken = crypto.randomBytes(48).toString("hex");

  // Redis এ hash রাখো (আসল token না), ৭ দিন
  await redis.set(`rt:${hash(refreshToken)}`, String(user._id), "EX", 7 * 24 * 3600);
  return { accessToken, refreshToken };
};

export const rotateRefresh = async (oldToken) => {
  const key = `rt:${hash(oldToken)}`;
  const userId = await redis.get(key);
  if (!userId) throw new Error("Invalid refresh token"); // reuse হলে এখানেই আটকে যাবে

  await redis.del(key); // পুরনোটা সঙ্গে সঙ্গে বাতিল (rotation)
  const user = await User.findById(userId);
  return issueTokens(user);
};

export const revokeRefresh = (token) => redis.del(`rt:${hash(token)}`); // logout
```

**কেন rotation?** Refresh token চুরি হলেও একবার ব্যবহারের পর অকেজো হয়ে যায়।

---

### 20.8 Dependency Injection ও Layered Architecture

বড় codebase এ service গুলো সরাসরি import না করে বাইরে থেকে inject করলে test করা সহজ হয়।

```js
// সহজ ও package-ছাড়া পদ্ধতি: factory function
export const makeOrderService = ({ orderRepo, paymentGateway, emailQueue }) => ({
  async createOrder(data) {
    const order = await orderRepo.create(data);
    await emailQueue.add("order-confirm", { orderId: order.id });
    return order;
  },
});

// Test এ fake পাঠানো যায়
const service = makeOrderService({
  orderRepo: { create: async (d) => ({ id: 1, ...d }) },
  paymentGateway: {},
  emailQueue: { add: async () => {} },
});
```

বড় প্রজেক্টে: `awilix` (DI container), অথবা পুরো structure সহ **NestJS** (Express এর উপরে তৈরি, TypeScript, built-in DI/validation/guards)।

**Layer নিয়ম:**
```
Route → Controller → Service → Repository → Model
(HTTP)   (req/res)    (business)  (DB query)   (schema)
```
- Controller শুধু request পড়ে ও response দেয়
- Service এ কোনো `req/res` থাকবে না
- Repository শুধু DB এর সাথে কথা বলে

---

### 20.9 Monolith এর Quick Install

```bash
npm i pino pino-http @godaddy/terminus mongoose-delete cache-manager
npm i -D pino-pretty
```


---

## 21. Microservice Architecture

> ⚠️ **আগে ভাবো:** ছোট/মাঝারি প্রজেক্টে microservice ভুল সিদ্ধান্ত — complexity অনেক বাড়ে। প্রথমে **modular monolith** (একটা app, কিন্তু ভেতরে আলাদা module) বানাও। যখন আলাদা scaling, আলাদা টিম বা আলাদা deploy সত্যিই দরকার হবে, তখনই ভাঙো।

### 21.1 কখন Microservice, কখন Monolith?

| প্রশ্ন | Monolith | Microservice |
|---|---|---|
| টিম সাইজ | ১–৮ জন | ৮+ জন, একাধিক টিম |
| Deploy | সব একসাথে | প্রতিটি আলাদা |
| Scaling | পুরো app | যে অংশে লোড শুধু সেটা |
| Complexity | কম | অনেক বেশি (network, retry, tracing) |
| Data consistency | সহজ (transaction) | কঠিন (saga, eventual consistency) |
| শুরুর জন্য | ✅ | ❌ |

### 21.2 উদাহরণ Architecture (E-commerce)

```
                    ┌─────────────┐
   Client ────────► │ API Gateway │
                    └──────┬──────┘
        ┌──────────────┬───┴──────────┬──────────────┐
        ▼              ▼              ▼              ▼
  ┌──────────┐  ┌────────────┐  ┌───────────┐  ┌──────────────┐
  │   User   │  │  Product   │  │   Order   │  │ Notification │
  │ Service  │  │  Service   │  │  Service  │  │   Service    │
  └────┬─────┘  └─────┬──────┘  └─────┬─────┘  └──────┬───────┘
       │ MongoDB      │ MongoDB       │ MongoDB       │
       └──────────────┴───────┬───────┴───────────────┘
                              ▼
                  RabbitMQ / Kafka (event bus)
```

**Monorepo structure:**

```
shop-platform/
├── services/
│   ├── gateway/
│   ├── user-service/
│   ├── product-service/
│   ├── order-service/
│   └── notification-service/
├── packages/
│   └── shared/            # logger, error classes, event names, types
├── proto/                 # gRPC .proto ফাইল
├── docker-compose.yml
├── pnpm-workspace.yaml
└── turbo.json
```

**মূল নিয়ম:**
- ✅ **Database per service** — এক service অন্যের DB সরাসরি ছুঁবে না
- ✅ যোগাযোগ শুধু API/gRPC/event দিয়ে
- ✅ প্রতিটি service আলাদা deploy করা যায়
- ❌ Shared database (এটা "distributed monolith", সবচেয়ে খারাপ অবস্থা)

---

### 21.3 API Gateway (`http-proxy-middleware`)

Client শুধু gateway চেনে। Gateway auth, rate limit করে তারপর সঠিক service এ পাঠায়।

```bash
npm i express http-proxy-middleware helmet cors express-rate-limit jsonwebtoken
```

```js
// services/gateway/src/app.js
import express from "express";
import { createProxyMiddleware } from "http-proxy-middleware";
import jwt from "jsonwebtoken";

const app = express();

// Gateway তে auth: token verify করে user info header এ দিয়ে দেয়
const authenticate = (req, res, next) => {
  const token = req.headers.authorization?.split(" ")[1];
  if (!token) return res.status(401).json({ message: "Unauthorized" });
  try {
    const user = jwt.verify(token, process.env.JWT_SECRET);
    req.headers["x-user-id"] = user.id;
    req.headers["x-user-role"] = user.role;
    next();
  } catch {
    res.status(401).json({ message: "Invalid token" });
  }
};

const proxy = (pathFilter, target) =>
  createProxyMiddleware({ pathFilter, target, changeOrigin: true });

// Public route
app.use(proxy("/api/auth", process.env.USER_SERVICE_URL));

// Protected route
app.use("/api/products", authenticate);
app.use("/api/orders", authenticate);
app.use(proxy("/api/products", process.env.PRODUCT_SERVICE_URL));
app.use(proxy("/api/orders", process.env.ORDER_SERVICE_URL));

app.listen(8080);
```

> ⚠️ **Gateway তে `express.json()` দিও না** proxy এর আগে, নাহলে body খেয়ে ফেলে proxy তে request আটকে যায়।
> ⚠️ ভেতরের service গুলো **public internet এ খোলা রাখবে না** — শুধু gateway দেখতে পাবে (Docker network)। আর `x-user-id` header শুধু gateway থেকে এলেই বিশ্বাস করো।

Production এ **Nginx**, **Traefik** বা **Kong** ব্যবহার করা বেশি common।

---

### 21.4 Async যোগাযোগ: RabbitMQ (`amqplib`)

**কেন async?** Order করলে email/SMS/stock update — এগুলোর জন্য order service কে অপেক্ষা করতে হবে না। Event publish করলেই হলো, যার দরকার সে শুনবে।

```bash
npm i amqplib
```

```js
// packages/shared/src/rabbit.js
import amqp from "amqplib";

let channel;

export const connectRabbit = async (url) => {
  const conn = await amqp.connect(url);
  channel = await conn.createChannel();
  await channel.assertExchange("events", "topic", { durable: true });

  conn.on("close", () => { console.error("RabbitMQ closed"); process.exit(1); });
  return channel;
};

export const publishEvent = (routingKey, payload) => {
  channel.publish(
    "events",
    routingKey,                                   // যেমন "order.created"
    Buffer.from(JSON.stringify(payload)),
    { persistent: true, contentType: "application/json", messageId: payload.eventId }
  );
};

export const subscribe = async (queueName, pattern, handler) => {
  await channel.assertQueue(queueName, {
    durable: true,
    deadLetterExchange: "events.dlx",             // বারবার fail হলে এখানে যায়
  });
  await channel.bindQueue(queueName, "events", pattern);
  channel.prefetch(10);

  channel.consume(queueName, async (msg) => {
    if (!msg) return;
    try {
      await handler(JSON.parse(msg.content.toString()), msg);
      channel.ack(msg);
    } catch (err) {
      console.error("Handler failed", err);
      channel.nack(msg, false, false);            // requeue=false → DLQ তে
    }
  });
};
```

```js
// order-service: event পাঠানো
import { randomUUID } from "node:crypto";
publishEvent("order.created", { eventId: randomUUID(), orderId: order.id, userId, total });

// notification-service: শোনা
subscribe("notification.order", "order.*", async (event) => {
  await sendEmail(event.userId, "Order confirmed", `Order ${event.orderId}`);
});
```

**মনে রাখো:**
- Message **at-least-once** আসে, মানে একই event দুইবার আসতে পারে। তাই **consumer অবশ্যই idempotent** হতে হবে (`eventId` DB তে রেখে আগে দেখা হয়েছে কিনা চেক করো)।
- Dead Letter Queue (DLQ) রাখো, না হলে fail হওয়া message হারিয়ে যায়।

**RabbitMQ vs Kafka vs NATS vs BullMQ:**

| টুল | কখন নেবে |
|---|---|
| **BullMQ** | একটা app এর ভেতরে background job (Redis আছে বলে সহজ) |
| **RabbitMQ** | service-to-service event, routing দরকার, সাধারণ choice |
| **Kafka** (`kafkajs`) | অনেক বেশি volume, event replay, analytics/streaming |
| **NATS** (`nats`) | হালকা ও খুব দ্রুত messaging |

---

### 21.5 Sync যোগাযোগ: gRPC

REST এর চেয়ে দ্রুত ও strongly-typed। যখন একটা service এর **এখনই** অন্য service এর উত্তর দরকার (যেমন order করার সময় user ও product তথ্য)।

```bash
npm i @grpc/grpc-js @grpc/proto-loader
```

```proto
// proto/user.proto
syntax = "proto3";
package user;

service UserService {
  rpc GetUser (GetUserRequest) returns (UserReply);
}
message GetUserRequest { string id = 1; }
message UserReply { string id = 1; string name = 2; string email = 3; }
```

```js
// user-service: server
import grpc from "@grpc/grpc-js";
import protoLoader from "@grpc/proto-loader";

const def = protoLoader.loadSync("proto/user.proto", { keepCase: true, defaults: true });
const proto = grpc.loadPackageDefinition(def).user;

const server = new grpc.Server();
server.addService(proto.UserService.service, {
  GetUser: async (call, callback) => {
    const user = await User.findById(call.request.id).lean();
    if (!user) return callback({ code: grpc.status.NOT_FOUND, message: "User not found" });
    callback(null, { id: String(user._id), name: user.name, email: user.email });
  },
});

server.bindAsync("0.0.0.0:50051", grpc.ServerCredentials.createInsecure(), (err) => {
  if (err) throw err;
  console.log("gRPC server on 50051");
});
```

```js
// order-service: client
const client = new proto.UserService("user-service:50051", grpc.credentials.createInsecure());

export const getUser = (id) =>
  new Promise((resolve, reject) => {
    client.GetUser({ id }, { deadline: Date.now() + 2000 }, (err, res) =>
      err ? reject(err) : resolve(res)
    );
  });
```

> `deadline` (timeout) সবসময় দাও। না দিলে অন্য service আটকে গেলে তোমার service ও ঝুলে থাকবে।
> Production এ `createInsecure()` এর বদলে TLS/mTLS ব্যবহার করো।

---

### 21.6 Resilience: Circuit Breaker, Retry, Timeout

একটা service পড়ে গেলে যেন পুরো system না পড়ে।

```bash
npm i opossum p-retry
```

```js
import CircuitBreaker from "opossum";
import pRetry from "p-retry";

// Retry (শুধু temporary error এ, exponential backoff সহ)
const fetchUserWithRetry = (id) =>
  pRetry(() => getUser(id), {
    retries: 3,
    minTimeout: 200,
    factor: 2,
    onFailedAttempt: (e) => console.warn(`Attempt ${e.attemptNumber} failed`),
  });

// Circuit Breaker: বারবার fail হলে কিছুক্ষণ call বন্ধ রাখে
const breaker = new CircuitBreaker(fetchUserWithRetry, {
  timeout: 3000,                  // ৩ সেকেন্ডে উত্তর না এলে fail
  errorThresholdPercentage: 50,   // ৫০% fail হলে circuit খোলে (call বন্ধ)
  resetTimeout: 10_000,           // ১০ সেকেন্ড পর আবার চেষ্টা
});

breaker.fallback(() => ({ id: null, name: "Unknown", email: null })); // বিকল্প উত্তর
breaker.on("open", () => console.warn("⚠️ Circuit OPEN"));
breaker.on("close", () => console.info("Circuit CLOSED"));

const user = await breaker.fire("user_123");
```

**তিনটা সুরক্ষা:** Timeout (বেশিক্ষণ অপেক্ষা না করা) → Retry (সাময়িক ভুলে আবার চেষ্টা) → Circuit Breaker (বারবার fail হলে থামা)।

> `p-retry` ESM-only package, তাই `"type": "module"` লাগবে।

---

### 21.7 Outbox Pattern (DB save ও event publish একসাথে নিশ্চিত করা)

**সমস্যা:** Order DB তে save হলো, কিন্তু event publish করার আগেই server crash করল → notification কখনো যাবে না।
**সমাধান:** Event টাও একই transaction এ DB তে `outbox` collection এ রাখো, আলাদা worker সেটা publish করবে।

```js
// order-service
export const createOrder = async (data) => {
  const session = await mongoose.startSession();
  try {
    let order;
    await session.withTransaction(async () => {
      [order] = await Order.create([data], { session });
      await Outbox.create(
        [{
          eventId: randomUUID(),
          routingKey: "order.created",
          payload: { orderId: order.id, userId: data.userId, total: data.total },
          status: "pending",
        }],
        { session }
      );
    });
    return order;
  } finally {
    session.endSession();
  }
};

// Outbox publisher (loop বা cron এ চলে)
export const publishOutbox = async () => {
  const events = await Outbox.find({ status: "pending" }).sort("createdAt").limit(50);
  for (const e of events) {
    try {
      publishEvent(e.routingKey, { ...e.payload, eventId: e.eventId });
      e.status = "sent";
      e.sentAt = new Date();
      await e.save();
    } catch (err) {
      e.attempts = (e.attempts || 0) + 1;
      await e.save();
    }
  }
};
setInterval(publishOutbox, 2000);
```

---

### 21.8 Saga Pattern (একাধিক service এর transaction)

Microservice এ একটা DB transaction সব service এ চলে না। তাই প্রতিটি ধাপের একটা **ক্ষতিপূরণ (compensating) কাজ** রাখা হয়।

**Choreography উদাহরণ (event দিয়ে):**

| ধাপ | Service | সফল হলে event | ব্যর্থ হলে |
|---|---|---|---|
| 1 | Order | `order.created` (status: pending) | — |
| 2 | Inventory | `stock.reserved` | `stock.failed` → Order cancel |
| 3 | Payment | `payment.completed` | `payment.failed` → Stock ফেরত (`stock.release`) + Order cancel |
| 4 | Order | status = confirmed | — |

```js
// payment-service: ব্যর্থ হলে ক্ষতিপূরণ event
subscribe("payment.stock-reserved", "stock.reserved", async (e) => {
  try {
    await chargeCustomer(e.orderId, e.total);
    publishEvent("payment.completed", { eventId: randomUUID(), orderId: e.orderId });
  } catch {
    publishEvent("payment.failed", { eventId: randomUUID(), orderId: e.orderId });
  }
});

// inventory-service: payment.failed শুনে stock ফেরত দেয়
subscribe("inventory.release", "payment.failed", async (e) => {
  await releaseStock(e.orderId);
});
```

---

### 21.9 Distributed Lock (`redlock`)

একই কাজ দুই instance একসাথে করলে সমস্যা (যেমন একই cron job দুই server এ চলা)।

```bash
npm i redlock
```

```js
import Redlock from "redlock";
import { redis } from "./redis.js";

const redlock = new Redlock([redis], { retryCount: 3, retryDelay: 200 });

export const withLock = async (resource, ttlMs, fn) => {
  const lock = await redlock.acquire([`locks:${resource}`], ttlMs);
  try {
    return await fn();
  } finally {
    await lock.release();
  }
};

// ব্যবহার: সব server এর মধ্যে শুধু একজন এটা চালাবে
cron.schedule("0 0 * * *", () =>
  withLock("daily-report", 60_000, generateDailyReport).catch(() => {})
);
```

---

### 21.10 Observability: Logs + Metrics + Traces

| স্তম্ভ | টুল | কাজ |
|---|---|---|
| **Logs** | `pino` + Loki/ELK | কী ঘটল |
| **Metrics** | `prom-client` + Prometheus + Grafana | কতবার/কত দ্রুত |
| **Traces** | OpenTelemetry + Jaeger | একটা request কোন service ঘুরল |

**Metrics:**

```bash
npm i prom-client
```

```js
import client from "prom-client";
client.collectDefaultMetrics();

const httpDuration = new client.Histogram({
  name: "http_request_duration_seconds",
  help: "HTTP request duration",
  labelNames: ["method", "route", "status"],
  buckets: [0.05, 0.1, 0.3, 0.5, 1, 2, 5],
});

app.use((req, res, next) => {
  const end = httpDuration.startTimer();
  res.on("finish", () => end({ method: req.method, route: req.route?.path || req.path, status: res.statusCode }));
  next();
});

app.get("/metrics", async (req, res) => {
  res.set("Content-Type", client.register.contentType);
  res.end(await client.register.metrics());
});
```

**Tracing (OpenTelemetry):**

```bash
npm i @opentelemetry/sdk-node @opentelemetry/auto-instrumentations-node @opentelemetry/exporter-trace-otlp-http
```

```js
// tracing.js  (সবার আগে load হবে)
import { NodeSDK } from "@opentelemetry/sdk-node";
import { getNodeAutoInstrumentations } from "@opentelemetry/auto-instrumentations-node";
import { OTLPTraceExporter } from "@opentelemetry/exporter-trace-otlp-http";

const sdk = new NodeSDK({
  serviceName: process.env.SERVICE_NAME || "order-service",
  traceExporter: new OTLPTraceExporter({ url: "http://jaeger:4318/v1/traces" }),
  instrumentations: [getNodeAutoInstrumentations()], // express, mongodb, redis, http সব অটো
});
sdk.start();
```

```bash
# চালানোর সময়
node --import ./tracing.js src/server.js
```

Jaeger UI তে (`localhost:16686`) একটা request এর পুরো যাত্রা দেখা যায়।

**Correlation ID:** Gateway যে `x-request-id` বানায় সেটা প্রতিটি service-to-service call এ header এ পাঠাও (সেকশন 20.4 এর মতো `AsyncLocalStorage` দিয়ে), তাহলে সব service এর log একটা request এর সাথে মেলানো যায়।

---

### 21.11 Service-to-Service Security

- ভেতরের service গুলো Docker private network এ রাখো, publicly expose করো না
- Gateway বাদে অন্য service শুধু gateway এর IP/network থেকে request নেবে
- Service-to-service call এ **internal JWT** বা shared secret header (`x-internal-key`) ব্যবহার করো
- gRPC তে mTLS
- প্রতিটি service এর আলাদা DB user ও আলাদা secret

---

### 21.12 Docker: Dockerfile ও docker-compose

```dockerfile
# services/order-service/Dockerfile
FROM node:22-alpine AS base
WORKDIR /app

FROM base AS deps
COPY package*.json ./
RUN npm ci --omit=dev

FROM base AS runner
ENV NODE_ENV=production
COPY --from=deps /app/node_modules ./node_modules
COPY src ./src
COPY package.json ./
USER node
EXPOSE 4003
CMD ["node", "src/server.js"]
```

```yaml
# docker-compose.yml
services:
  gateway:
    build: ./services/gateway
    ports: ["8080:8080"]
    environment:
      USER_SERVICE_URL: http://user-service:4001
      PRODUCT_SERVICE_URL: http://product-service:4002
      ORDER_SERVICE_URL: http://order-service:4003
      JWT_SECRET: ${JWT_SECRET}
    depends_on: [user-service, product-service, order-service]

  user-service:
    build: ./services/user-service
    environment:
      MONGO_URI: mongodb://user-db:27017/users
      RABBIT_URL: amqp://rabbitmq
    depends_on: [user-db, rabbitmq]

  order-service:
    build: ./services/order-service
    environment:
      MONGO_URI: mongodb://order-db:27017/orders?replicaSet=rs0
      RABBIT_URL: amqp://rabbitmq
      REDIS_URL: redis://redis:6379
    depends_on: [order-db, rabbitmq, redis]

  # প্রতি service এর নিজস্ব DB (user-db, product-db, order-db)
  user-db:
    image: mongo:7
    volumes: [user_data:/data/db]
  order-db:
    image: mongo:7
    command: ["--replSet", "rs0", "--bind_ip_all"]
    volumes: [order_data:/data/db]

  redis:
    image: redis:7-alpine
  rabbitmq:
    image: rabbitmq:3-management
    ports: ["15672:15672"]   # UI: http://localhost:15672
  jaeger:
    image: jaegertracing/all-in-one
    ports: ["16686:16686"]
    environment: { COLLECTOR_OTLP_ENABLED: "true" }

volumes:
  user_data:
  order_data:
```

```bash
docker compose up --build
```

> `.dockerignore` এ `node_modules`, `.env`, `.git` রাখো।
> `.env` কখনো image এ ঢুকিও না, runtime এ environment দিয়ে পাঠাও।

---

### 21.13 Microservice এর Package তালিকা

```bash
# Gateway
npm i http-proxy-middleware
# Messaging
npm i amqplib            # RabbitMQ
npm i kafkajs            # Kafka (দরকার হলে)
npm i nats               # NATS (দরকার হলে)
# gRPC
npm i @grpc/grpc-js @grpc/proto-loader
# Resilience
npm i opossum p-retry axios-retry redlock
# Observability
npm i prom-client @opentelemetry/sdk-node @opentelemetry/auto-instrumentations-node @opentelemetry/exporter-trace-otlp-http
# Monorepo (global বা devDependency)
npm i -D turbo
```


---

## 22. SaaS Product Backend

> SaaS মানে একই app অনেক customer (company/team) ব্যবহার করে, প্রত্যেকের data আলাদা, আর তারা মাসিক/বার্ষিক টাকা দেয়। নিচের ১০টা জিনিস প্রায় প্রতিটি SaaS এ লাগে।

### 22.1 Multi-Tenancy (SaaS এর হৃদয়)

**Tenant** = একজন customer (company/workspace)। একজনের data অন্যজন যেন কখনো না দেখে — এটাই সবচেয়ে বড় নিরাপত্তা ঝুঁকি।

| পদ্ধতি | কীভাবে | সুবিধা | অসুবিধা |
|---|---|---|---|
| **Shared DB, tenantId field** | প্রতি document এ `tenantId` | সবচেয়ে সহজ ও সস্তা | query তে `tenantId` ভুলে গেলে data leak |
| **Database per tenant** | প্রতি tenant এর আলাদা DB | সবচেয়ে নিরাপদ, backup আলাদা | connection ও খরচ বেশি |
| **Collection per tenant** | প্রতি tenant এর আলাদা collection | মাঝামাঝি | অনেক tenant হলে অসুবিধা |

শুরুতে **tenantId পদ্ধতি** নাও। নিচে এমনভাবে বানাচ্ছি যাতে `tenantId` ভুলে যাওয়ার সুযোগই না থাকে।

**ধাপ ১: Models**

```js
// Tenant (company/workspace)
const tenantSchema = new mongoose.Schema({
  name: { type: String, required: true },
  slug: { type: String, unique: true, required: true },   // subdomain: acme.myapp.com
  plan: { type: String, enum: ["free", "pro", "business"], default: "free" },
  stripeCustomerId: String,
  subscriptionStatus: { type: String, default: "none" },
}, { timestamps: true });

// Membership (একজন user একাধিক tenant এ থাকতে পারে, প্রতিটিতে আলাদা role)
const membershipSchema = new mongoose.Schema({
  user: { type: mongoose.Schema.Types.ObjectId, ref: "User", required: true },
  tenant: { type: mongoose.Schema.Types.ObjectId, ref: "Tenant", required: true },
  role: { type: String, enum: ["owner", "admin", "member", "viewer"], default: "member" },
}, { timestamps: true });
membershipSchema.index({ user: 1, tenant: 1 }, { unique: true });
```

**ধাপ ২: Tenant Context (`AsyncLocalStorage`)**

```js
// src/utils/tenantContext.js
import { AsyncLocalStorage } from "node:async_hooks";

export const tenantStorage = new AsyncLocalStorage();
export const getTenantId = () => tenantStorage.getStore()?.tenantId;
```

**ধাপ ৩: Middleware (কোন tenant এর request তা নির্ণয়)**

```js
// src/middlewares/tenant.js
import { tenantStorage } from "../utils/tenantContext.js";
import Membership from "../models/membership.model.js";

// protect middleware এর পরে চলবে
export const resolveTenant = async (req, res, next) => {
  // ক) header থেকে: x-tenant-id   (বা খ) subdomain থেকে: req.hostname.split(".")[0])
  const tenantId = req.headers["x-tenant-id"];
  if (!tenantId) return res.status(400).json({ message: "Tenant দরকার" });

  // user সত্যিই ওই tenant এর সদস্য কিনা — এটা না দেখলে যে কেউ যেকোনো tenant এর data পাবে!
  const membership = await Membership.findOne({ user: req.user._id, tenant: tenantId });
  if (!membership) return res.status(403).json({ message: "এই workspace এ তোমার access নেই" });

  req.tenantId = tenantId;
  req.membership = membership;

  tenantStorage.run({ tenantId }, next);
};
```

**ধাপ ৪: Mongoose Plugin (সব query তে অটো tenantId)**

```js
// src/models/plugins/tenant.plugin.js
import mongoose from "mongoose";
import { getTenantId } from "../../utils/tenantContext.js";

export function tenantPlugin(schema) {
  schema.add({
    tenantId: { type: mongoose.Schema.Types.ObjectId, ref: "Tenant", required: true, index: true },
  });

  const mustHaveTenant = () => {
    const t = getTenantId();
    if (!t) throw new Error("Tenant context নেই — query আটকানো হলো");
    return t;
  };

  // সব read/update/delete query তে tenant filter বসাও
  [
    "find", "findOne", "findOneAndUpdate", "findOneAndDelete",
    "updateOne", "updateMany", "deleteOne", "deleteMany", "countDocuments",
  ].forEach((op) => {
    schema.pre(op, function () {
      this.where({ tenantId: mustHaveTenant() });
    });
  });

  // নতুন document এ অটো tenantId
  schema.pre("save", function () {
    if (this.isNew && !this.tenantId) this.tenantId = mustHaveTenant();
  });

  // Aggregation এও
  schema.pre("aggregate", function () {
    this.pipeline().unshift({
      $match: { tenantId: new mongoose.Types.ObjectId(mustHaveTenant()) },
    });
  });
}

// ব্যবহার
// projectSchema.plugin(tenantPlugin);
// projectSchema.index({ tenantId: 1, name: 1 }, { unique: true }); // unique সবসময় tenant সহ!
```

**ধাপ ৫: Route এ**

```js
router.use(protect, resolveTenant);   // এর নিচের সব route tenant-safe
router.get("/projects", async (req, res) => {
  const projects = await Project.find();   // অটো শুধু এই tenant এর
  res.json({ data: projects });
});
```

**গুরুত্বপূর্ণ সতর্কতা:**
- `Model.insertMany()` ও `bulkWrite()` তে plugin এর `pre("save")` চলে না, এগুলোতে হাতে `tenantId` দাও
- `unique` index সবসময় `{ tenantId: 1, field: 1 }` compound হতে হবে (নাহলে দুই company একই নাম/email ব্যবহার করতে পারবে না)
- Redis cache key তেও tenant রাখো: `cache:${tenantId}:projects`
- File upload এর path: `tenants/${tenantId}/...`
- Background job এ `tenantId` payload এ পাঠাও এবং worker এ `tenantStorage.run()` দিয়ে context সেট করো
- **Tenant isolation এর জন্য আলাদা test লেখো**: Tenant A এর token দিয়ে Tenant B এর data চাইলে ফাঁকা/403 আসতে হবে

---

### 22.2 Subscription Billing (Stripe)

```bash
npm i stripe
```

**Plan config (একটা জায়গায়):**

```js
// src/config/plans.js
export const PLANS = {
  free:     { priceId: null,                         limits: { projects: 3,  members: 2,  apiCalls: 1000 } },
  pro:      { priceId: process.env.STRIPE_PRICE_PRO,      limits: { projects: 50, members: 10, apiCalls: 50000 } },
  business: { priceId: process.env.STRIPE_PRICE_BUSINESS, limits: { projects: Infinity, members: 100, apiCalls: 500000 } },
};
```

**Checkout (subscription শুরু):**

```js
export const createCheckout = async (req, res) => {
  const { plan } = req.body;
  const tenant = await Tenant.findById(req.tenantId);

  // Stripe customer না থাকলে বানাও
  if (!tenant.stripeCustomerId) {
    const customer = await stripe.customers.create({
      email: req.user.email,
      name: tenant.name,
      metadata: { tenantId: String(tenant._id) },
    });
    tenant.stripeCustomerId = customer.id;
    await tenant.save();
  }

  const session = await stripe.checkout.sessions.create({
    mode: "subscription",
    customer: tenant.stripeCustomerId,
    line_items: [{ price: PLANS[plan].priceId, quantity: 1 }],
    success_url: `${process.env.CLIENT_URL}/billing?success=1`,
    cancel_url: `${process.env.CLIENT_URL}/billing?canceled=1`,
    metadata: { tenantId: String(tenant._id), plan },
    subscription_data: { metadata: { tenantId: String(tenant._id), plan } },
  });

  res.json({ url: session.url });
};
```

**Customer Portal (card বদল, cancel, invoice — Stripe নিজেই UI দেয়):**

```js
export const openPortal = async (req, res) => {
  const tenant = await Tenant.findById(req.tenantId);
  const session = await stripe.billingPortal.sessions.create({
    customer: tenant.stripeCustomerId,
    return_url: `${process.env.CLIENT_URL}/billing`,
  });
  res.json({ url: session.url });
};
```

**Webhook (সবচেয়ে গুরুত্বপূর্ণ অংশ):**

```js
// app.js — express.json() এর ⚠️ আগে বসাতে হবে (raw body লাগে)
app.post(
  "/api/v1/billing/webhook",
  express.raw({ type: "application/json" }),
  billingWebhook
);
app.use(express.json());
```

```js
export const billingWebhook = async (req, res) => {
  let event;
  try {
    event = stripe.webhooks.constructEvent(
      req.body,
      req.headers["stripe-signature"],
      process.env.STRIPE_WEBHOOK_SECRET
    );
  } catch (err) {
    return res.status(400).send(`Webhook Error: ${err.message}`);
  }

  // একই event দুইবার এলে বাদ দাও (idempotency)
  const seen = await redis.set(`stripe:evt:${event.id}`, "1", "EX", 7 * 86400, "NX");
  if (!seen) return res.json({ received: true, duplicate: true });

  switch (event.type) {
    case "checkout.session.completed": {
      const s = event.data.object;
      await Tenant.findByIdAndUpdate(s.metadata.tenantId, {
        plan: s.metadata.plan,
        subscriptionStatus: "active",
      });
      break;
    }
    case "invoice.paid":
      // মেয়াদ বাড়াও, রসিদ email পাঠাও
      break;
    case "invoice.payment_failed": {
      // grace period দাও, user কে email করো
      const inv = event.data.object;
      await Tenant.findOneAndUpdate({ stripeCustomerId: inv.customer }, { subscriptionStatus: "past_due" });
      break;
    }
    case "customer.subscription.updated": {
      const sub = event.data.object;
      await Tenant.findOneAndUpdate(
        { stripeCustomerId: sub.customer },
        { subscriptionStatus: sub.status, plan: sub.metadata.plan }
      );
      break;
    }
    case "customer.subscription.deleted":
      await Tenant.findOneAndUpdate(
        { stripeCustomerId: event.data.object.customer },
        { plan: "free", subscriptionStatus: "canceled" }
      );
      break;
  }

  res.json({ received: true });
};
```

**Local টেস্ট (Stripe CLI):**

```bash
stripe login
stripe listen --forward-to localhost:5000/api/v1/billing/webhook
# এটা যে whsec_... দেয় সেটাই STRIPE_WEBHOOK_SECRET
stripe trigger invoice.payment_failed
```

**সতর্কতা:**
- Plan **শুধু webhook দিয়ে** বদলাবে, success page redirect দেখে না
- Stripe API version অনুযায়ী event/object এর field (যেমন billing period এর তারিখ) বদলাতে পারে, তাই আসল field নাম official docs থেকে মিলিয়ে নিও
- Free trial: `subscription_data: { trial_period_days: 14 }`
- Tax/VAT নিজে সামলাতে না চাইলে **Paddle** বা **Lemon Squeezy** (Merchant of Record, তারা tax নিজেই দেয়)

**বাংলাদেশে SSLCommerz দিয়ে recurring:** Auto-recurring সীমিত, তাই সাধারণ পদ্ধতি:
1. Payment সফল হলে `Subscription { tenant, plan, currentPeriodEnd }` সেভ করো
2. `node-cron` প্রতিদিন চালিয়ে মেয়াদ শেষের ৩/১ দিন আগে email/SMS reminder
3. মেয়াদ শেষে plan `free` এ নামাও, user "Renew" চাপলে নতুন SSLCommerz payment

---

### 22.3 Plan Limit ও Usage Metering

**Limit check middleware:**

```js
// src/middlewares/planLimit.js
import { PLANS } from "../config/plans.js";

export const checkLimit = (resource, countFn) => async (req, res, next) => {
  const tenant = await Tenant.findById(req.tenantId).lean();
  const limit = PLANS[tenant.plan].limits[resource];
  const current = await countFn(req);

  if (current >= limit) {
    return res.status(402).json({
      message: `${tenant.plan} প্ল্যানে সর্বোচ্চ ${limit}টি ${resource} করা যায়। Upgrade করো।`,
      code: "PLAN_LIMIT_REACHED",
    });
  }
  next();
};

// router.post("/projects", checkLimit("projects", () => Project.countDocuments()), createProject);
```

**API usage গোনা (Redis দিয়ে):**

```js
export const trackUsage = async (req, res, next) => {
  const month = new Date().toISOString().slice(0, 7);          // 2026-10
  const key = `usage:${req.tenantId}:${month}:apiCalls`;
  const used = await redis.incr(key);
  if (used === 1) await redis.expire(key, 40 * 86400);

  const limit = PLANS[req.tenant.plan].limits.apiCalls;
  if (used > limit) return res.status(429).json({ message: "মাসিক API limit শেষ" });
  next();
};
```

**Per-plan rate limit (`rate-limiter-flexible`):**

```bash
npm i rate-limiter-flexible
```

```js
import { RateLimiterRedis } from "rate-limiter-flexible";

const limiters = {
  free:     new RateLimiterRedis({ storeClient: redis, keyPrefix: "rl_free", points: 60,   duration: 60 }),
  pro:      new RateLimiterRedis({ storeClient: redis, keyPrefix: "rl_pro",  points: 600,  duration: 60 }),
  business: new RateLimiterRedis({ storeClient: redis, keyPrefix: "rl_biz",  points: 3000, duration: 60 }),
};

export const planRateLimit = async (req, res, next) => {
  try {
    await limiters[req.tenant.plan].consume(req.tenantId);
    next();
  } catch (rej) {
    res.set("Retry-After", Math.ceil(rej.msBeforeNext / 1000));
    res.status(429).json({ message: "Too many requests" });
  }
};
```

---

### 22.4 Permission: RBAC ও Fine-grained (`@casl/ability`)

শুধু `role === "admin"` চেক বড় SaaS এ যথেষ্ট না। "Member শুধু **নিজের** তৈরি project edit করতে পারবে" — এমন নিয়মের জন্য CASL।

```bash
npm i @casl/ability
```

```js
// src/permissions/ability.js
import { AbilityBuilder, createMongoAbility } from "@casl/ability";

export function defineAbilityFor(membership, userId) {
  const { can, cannot, build } = new AbilityBuilder(createMongoAbility);

  switch (membership.role) {
    case "owner":
      can("manage", "all");                              // সবকিছু
      break;
    case "admin":
      can("manage", "all");
      cannot("delete", "Tenant");                        // workspace মুছতে পারবে না
      cannot("manage", "Billing");
      break;
    case "member":
      can("read", "Project");
      can("create", "Project");
      can(["update", "delete"], "Project", { createdBy: userId });   // শুধু নিজেরটা
      break;
    case "viewer":
      can("read", "all");
      break;
  }
  return build();
}
```

```js
// src/middlewares/can.js
import { subject } from "@casl/ability";
import { defineAbilityFor } from "../permissions/ability.js";

export const can = (action, subjectName, loader) => async (req, res, next) => {
  const ability = defineAbilityFor(req.membership, String(req.user._id));

  if (loader) {
    // নির্দিষ্ট document এর উপর অনুমতি (যেমন update/delete)
    const doc = await loader(req);
    if (!doc) return res.status(404).json({ message: "Not found" });
    if (!ability.can(action, subject(subjectName, doc.toObject()))) {
      return res.status(403).json({ message: "Forbidden" });
    }
    req.resource = doc;
  } else if (!ability.can(action, subjectName)) {
    return res.status(403).json({ message: "Forbidden" });
  }
  next();
};

// ব্যবহার
// router.post("/projects", can("create", "Project"), createProject);
// router.delete("/projects/:id", can("delete", "Project", (req) => Project.findById(req.params.id)), deleteProject);
```

---

### 22.5 API Key System (Customer এর নিজস্ব integration এর জন্য)

**নিয়ম:** আসল key শুধু **একবার** দেখাও, DB তে শুধু **hash** রাখো (password এর মতো)।

```js
// src/services/apiKey.service.js
import crypto from "node:crypto";
import ApiKey from "../models/apiKey.model.js";

const sha256 = (s) => crypto.createHash("sha256").update(s).digest("hex");

export const createApiKey = async (tenantId, name) => {
  const raw = `sk_live_${crypto.randomBytes(24).toString("hex")}`;
  await ApiKey.create({
    tenantId,
    name,
    prefix: raw.slice(0, 12),          // UI তে দেখানোর জন্য: sk_live_a1b2...
    keyHash: sha256(raw),
  });
  return raw;                          // এই একবারই user কে দেখাও
};

// middleware
export const apiKeyAuth = async (req, res, next) => {
  const raw = req.headers["x-api-key"];
  if (!raw) return res.status(401).json({ message: "API key দরকার" });

  const key = await ApiKey.findOne({ keyHash: sha256(raw), revokedAt: null });
  if (!key) return res.status(401).json({ message: "Invalid API key" });

  ApiKey.updateOne({ _id: key._id }, { lastUsedAt: new Date() }).exec();   // fire & forget
  req.tenantId = String(key.tenantId);
  tenantStorage.run({ tenantId: req.tenantId }, next);
};
```

---

### 22.6 Invitation (টিম সদস্য যোগ)

```js
// Invite পাঠানো
export const inviteMember = async (req, res) => {
  const { email, role } = req.body;
  const token = crypto.randomBytes(32).toString("hex");

  await Invitation.create({
    tenantId: req.tenantId,
    email,
    role,
    tokenHash: crypto.createHash("sha256").update(token).digest("hex"),
    expiresAt: new Date(Date.now() + 7 * 86400 * 1000),
    invitedBy: req.user._id,
  });

  await emailQueue.add("invite", {
    to: email,
    link: `${process.env.CLIENT_URL}/accept-invite?token=${token}`,
  });
  res.json({ message: "Invite পাঠানো হয়েছে" });
};

// Accept করা
export const acceptInvite = async (req, res) => {
  const tokenHash = crypto.createHash("sha256").update(req.body.token).digest("hex");
  const invite = await Invitation.findOne({
    tokenHash, acceptedAt: null, expiresAt: { $gt: new Date() },
  });
  if (!invite) return res.status(400).json({ message: "Invite invalid বা মেয়াদোত্তীর্ণ" });
  if (invite.email !== req.user.email) return res.status(403).json({ message: "এই invite তোমার জন্য না" });

  await Membership.create({ user: req.user._id, tenant: invite.tenantId, role: invite.role });
  invite.acceptedAt = new Date();
  await invite.save();
  res.json({ message: "যোগ দিয়েছ!" });
};
```

Free plan এর `members` limit এখানেও check করতে ভুলো না।

---

### 22.7 Two-Factor Authentication (2FA)

```bash
npm i otplib@12 qrcode
```

> এখানে `otplib` v12 এর API দেখানো হয়েছে। নতুন major version এ API বদলাতে পারে, তাই version pin করা বা docs দেখা ভালো।

```js
import { authenticator } from "otplib";
import QRCode from "qrcode";

// Setup: secret ও QR code
export const setup2FA = async (req, res) => {
  const secret = authenticator.generateSecret();
  const otpauth = authenticator.keyuri(req.user.email, "MyApp", secret);
  const qr = await QRCode.toDataURL(otpauth);

  // secret এখনই "pending" হিসেবে রাখো (encrypt করে), verify হলে চালু
  await User.findByIdAndUpdate(req.user._id, { twoFactorTempSecret: secret });
  res.json({ qr });        // user Google Authenticator এ scan করবে
};

// Verify ও চালু
export const verify2FA = async (req, res) => {
  const user = await User.findById(req.user._id).select("+twoFactorTempSecret");
  const valid = authenticator.check(req.body.code, user.twoFactorTempSecret);
  if (!valid) return res.status(400).json({ message: "ভুল কোড" });

  user.twoFactorSecret = user.twoFactorTempSecret;
  user.twoFactorEnabled = true;
  user.twoFactorTempSecret = undefined;
  await user.save();
  res.json({ message: "2FA চালু হয়েছে" });
};
```

Login এ password সঠিক হলে, `twoFactorEnabled` থাকলে আগে আংশিক token (`pending2fa: true`) দাও, কোড verify হলে তবেই আসল token। Backup code (একবার ব্যবহারযোগ্য, hash করা) রাখতে ভুলো না। `twoFactorSecret` DB তে encrypt করে রাখা ভালো।

---

### 22.8 Audit Log

কে, কখন, কী বদলাল। Enterprise customer ও debugging দুটোর জন্যই দরকার।

```js
const auditSchema = new mongoose.Schema({
  tenantId: { type: mongoose.Schema.Types.ObjectId, index: true },
  actor: { type: mongoose.Schema.Types.ObjectId, ref: "User" },
  action: String,                     // "project.deleted"
  resource: { type: String },         // "Project"
  resourceId: String,
  changes: mongoose.Schema.Types.Mixed,   // { before, after }
  ip: String,
  userAgent: String,
}, { timestamps: { createdAt: true, updatedAt: false } });
auditSchema.index({ createdAt: 1 }, { expireAfterSeconds: 60 * 60 * 24 * 365 });  // ১ বছর পর অটো মুছবে

export const audit = (req, action, resource, resourceId, changes) =>
  AuditLog.create({
    tenantId: req.tenantId,
    actor: req.user?._id,
    action, resource, resourceId, changes,
    ip: req.ip,
    userAgent: req.headers["user-agent"],
  }).catch((e) => logger.error({ e }, "audit failed"));   // audit fail হলে মূল কাজ যেন না থামে

// await audit(req, "project.deleted", "Project", id, { before: project });
```

---

### 22.9 Outgoing Webhooks (Customer কে event পাঠানো)

Customer তার server এর URL দেয়, কিছু ঘটলে তুমি সেখানে POST করো।

```js
// src/jobs/webhook.queue.js
import { Queue, Worker } from "bullmq";
import crypto from "node:crypto";

export const webhookQueue = new Queue("webhooks", { connection: redis });

export const dispatchWebhook = async (tenantId, event, data) => {
  const endpoints = await WebhookEndpoint.find({ tenantId, events: event, active: true });
  for (const ep of endpoints) {
    await webhookQueue.add(
      "deliver",
      { endpointId: ep.id, event, data, eventId: randomUUID() },
      { attempts: 6, backoff: { type: "exponential", delay: 5000 } }
    );
  }
};

new Worker("webhooks", async (job) => {
  const ep = await WebhookEndpoint.findById(job.data.endpointId).select("+secret");
  const body = JSON.stringify({ id: job.data.eventId, type: job.data.event, data: job.data.data });
  const timestamp = Math.floor(Date.now() / 1000);

  // HMAC signature, যাতে customer নিশ্চিত হতে পারে request টা আমাদের
  const signature = crypto.createHmac("sha256", ep.secret).update(`${timestamp}.${body}`).digest("hex");

  const res = await fetch(ep.url, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "X-Webhook-Timestamp": String(timestamp),
      "X-Webhook-Signature": `sha256=${signature}`,
    },
    body,
    signal: AbortSignal.timeout(10_000),
  });
  if (!res.ok) throw new Error(`Endpoint responded ${res.status}`);   // throw করলে BullMQ retry করে
}, { connection: redis, concurrency: 10 });
```

নিজে না বানিয়ে **`svix`** ব্যবহার করলে retry, signature, customer dashboard সব পাওয়া যায়।
**SSRF সতর্কতা:** Customer এর দেওয়া URL যেন `localhost`/ভেতরের IP (`169.254.x.x`, `10.x.x.x`) না হয়, এটা validate করো।

---

### 22.10 Search (`meilisearch`)

Mongo `$text` এ typo tolerance নেই। Meilisearch ছোট-মাঝারি SaaS এর জন্য সহজ ও দ্রুত।

```bash
npm i meilisearch
```

```js
import { MeiliSearch } from "meilisearch";
const meili = new MeiliSearch({ host: process.env.MEILI_HOST, apiKey: process.env.MEILI_KEY });

const index = meili.index("projects");
await index.updateFilterableAttributes(["tenantId"]);     // একবার সেট করতে হয়
await index.updateSearchableAttributes(["name", "description"]);

// Mongo তে save হলে index এও পাঠাও (job queue দিয়ে ভালো)
await index.addDocuments([{ id: String(p._id), tenantId: String(p.tenantId), name: p.name, description: p.description }]);

// Search (সবসময় tenant filter সহ!)
const result = await index.search(req.query.q, {
  filter: `tenantId = "${req.tenantId}"`,
  limit: 20,
});
```

বড় scale এ `@elastic/elasticsearch`।

---

### 22.11 Feature Flags

নতুন feature কিছু customer কে আগে দেখানো, অথবা plan অনুযায়ী feature চালু/বন্ধ।

```js
// সহজ নিজস্ব পদ্ধতি
const FLAGS = {
  "new-dashboard": { enabled: true, plans: ["pro", "business"], percentage: 100 },
  "ai-assistant":  { enabled: true, plans: ["business"],        percentage: 25 },
};

export const isEnabled = (flag, tenant) => {
  const f = FLAGS[flag];
  if (!f?.enabled || !f.plans.includes(tenant.plan)) return false;
  // tenantId থেকে স্থায়ী ভাবে ০–৯৯ সংখ্যা বের করে rollout %
  const bucket = parseInt(crypto.createHash("md5").update(String(tenant._id)).digest("hex").slice(0, 8), 16) % 100;
  return bucket < f.percentage;
};
```

ready-made: `@growthbook/growthbook`, `unleash-client`, PostHog feature flags।

---

### 22.12 Error Tracking ও Analytics

```bash
npm i @sentry/node posthog-node
```

```js
// instrument.js — অন্য সব import এর আগে load করতে হবে
import * as Sentry from "@sentry/node";
Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 0.1,
});
```

```js
// app.js — সব route এর পরে, নিজের errorHandler এর আগে
import * as Sentry from "@sentry/node";
Sentry.setupExpressErrorHandler(app);
app.use(notFound);
app.use(errorHandler);
```

```js
// Product analytics
import { PostHog } from "posthog-node";
const posthog = new PostHog(process.env.POSTHOG_KEY, { host: "https://us.i.posthog.com" });
posthog.capture({ distinctId: String(user._id), event: "project_created", properties: { plan: tenant.plan } });
```

> Sentry এর setup ধাপ version অনুযায়ী বদলাতে পারে, তাই চালু করার সময় official docs দেখে নিও।

---

### 22.13 GDPR / Data Export / Account Delete

- **Export:** `POST /me/export` → BullMQ job সব data JSON/CSV করে S3 তে রাখে → signed link email করে
- **Account delete:** সাথে সাথে মুছো না। ৩০ দিন soft delete (undo এর সুযোগ), তারপর cron দিয়ে hard delete
- **Tenant delete:** সব collection, S3 file, Meilisearch index, Stripe subscription cancel — সব মোছার একটা checklist/job রাখো
- Privacy policy, cookie consent, data retention (audit log এর TTL index এর মতো) ঠিক রাখো

---

### 22.14 Admin Panel ও অন্যান্য

| Package | কাজ |
|---|---|
| `adminjs` | দ্রুত admin dashboard (Mongoose সহ) |
| `bull-board` | BullMQ job দেখা/retry |
| `@socket.io/redis-adapter` | একাধিক server এ socket |
| `resend` + `react-email` / `mjml` | সুন্দর transactional email |
| `i18next` | বহুভাষা |
| `exceljs`, `pdfkit` | report export |

---

### 22.15 SaaS এর Quick Install

```bash
npm i stripe @casl/ability rate-limiter-flexible meilisearch otplib@12 qrcode @sentry/node posthog-node bullmq
```

---

## 23. Testing, Docker, CI/CD

### 23.1 Test Pyramid

```
        ▲  E2E (কম)         → পুরো flow, ধীর
       ▲▲▲ Integration (মাঝারি) → API + আসল DB (supertest + testcontainers)
     ▲▲▲▲▲ Unit (বেশি)      → ছোট function, দ্রুত (vitest/jest)
```

### 23.2 Integration Test (আসল Mongo ও Redis দিয়ে — `testcontainers`)

```bash
npm i -D vitest supertest testcontainers @testcontainers/mongodb @faker-js/faker
```

```js
// test/setup.js
import { MongoDBContainer } from "@testcontainers/mongodb";
import mongoose from "mongoose";
import { beforeAll, afterAll, afterEach } from "vitest";

let container;

beforeAll(async () => {
  container = await new MongoDBContainer("mongo:7").start();
  await mongoose.connect(container.getConnectionString(), { directConnection: true });
}, 120_000);

afterEach(async () => {
  for (const c of Object.values(mongoose.connection.collections)) await c.deleteMany({});
});

afterAll(async () => {
  await mongoose.disconnect();
  await container.stop();
});
```

```js
// test/tenant-isolation.test.js
import request from "supertest";
import { it, expect } from "vitest";
import app from "../src/app.js";

it("Tenant A এর user Tenant B এর project দেখতে পারবে না", async () => {
  const a = await createTenantWithUser("A");
  const b = await createTenantWithUser("B");
  await createProject(b, "Secret");

  const res = await request(app)
    .get("/api/v1/projects")
    .set("Authorization", `Bearer ${a.token}`)
    .set("x-tenant-id", a.tenantId);

  expect(res.body.data).toHaveLength(0);

  // অন্য tenant এর ID দিয়ে চেষ্টা
  const hack = await request(app)
    .get("/api/v1/projects")
    .set("Authorization", `Bearer ${a.token}`)
    .set("x-tenant-id", b.tenantId);
  expect(hack.status).toBe(403);
});
```

> Testcontainers চালাতে Docker লাগে। Docker না থাকলে `mongodb-memory-server` ব্যবহার করো (Part 1 এ আছে)। তবে transaction test এ replica set লাগে।

**External API mock:** `msw` বা `nock` দিয়ে Stripe/SSLCommerz/email এর আসল call বন্ধ রাখো।

### 23.3 Load Test (`k6`)

```js
// load/health.js
import http from "k6/http";
import { check, sleep } from "k6";

export const options = {
  stages: [
    { duration: "30s", target: 50 },    // ধীরে ৫০ user এ
    { duration: "1m",  target: 50 },    // ধরে রাখো
    { duration: "10s", target: 0 },
  ],
  thresholds: {
    http_req_duration: ["p(95)<500"],   // ৯৫% request ৫০০ms এর মধ্যে
    http_req_failed: ["rate<0.01"],     // ১% এর কম fail
  },
};

export default function () {
  const res = http.get("http://localhost:5000/api/v1/products");
  check(res, { "status 200": (r) => r.status === 200 });
  sleep(1);
}
```

```bash
k6 run load/health.js
```

### 23.4 CI/CD (GitHub Actions)

```yaml
# .github/workflows/ci.yml
name: CI
on:
  push: { branches: [main] }
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      mongo:
        image: mongo:7
        ports: ["27017:27017"]
      redis:
        image: redis:7
        ports: ["6379:6379"]
    env:
      MONGO_URI: mongodb://localhost:27017/test
      REDIS_URL: redis://localhost:6379
      JWT_SECRET: test_secret_test_secret_test_secret_123
      CLIENT_URL: http://localhost:5173
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 22, cache: npm }
      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm audit --audit-level=high

  docker:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USER }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - uses: docker/build-push-action@v6
        with:
          push: true
          tags: yourname/my-api:latest
```

### 23.5 Commit Quality

```bash
npm i -D husky lint-staged @commitlint/cli @commitlint/config-conventional
npx husky init
```

```json
"lint-staged": { "*.js": ["eslint --fix", "prettier --write"] }
```

Commit message নিয়ম: `feat: add stripe webhook`, `fix: tenant filter in aggregate`।

---

## 24. কোন ধরনের Project এ কী লাগে (Decision Table)

| Feature / Package | ছোট Monolith (blog, portfolio) | বড় Monolith (e-commerce, LMS) | SaaS | Microservice |
|---|:-:|:-:|:-:|:-:|
| express, mongoose, dotenv, cors | ✅ | ✅ | ✅ | ✅ |
| JWT + bcrypt + helmet + rate-limit | ✅ | ✅ | ✅ | ✅ (gateway তে) |
| zod validation | ✅ | ✅ | ✅ | ✅ |
| multer + cloudinary/S3 | ⭕ | ✅ | ✅ | ⭕ |
| Redis cache | ⭕ | ✅ | ✅ | ✅ |
| BullMQ (job) | ❌ | ✅ | ✅ | ✅ |
| Env validation, pino logging, health check | ⭕ | ✅ | ✅ | ✅ |
| Mongo Transaction | ❌ | ✅ (payment/stock) | ✅ | ⭕ (service এর ভেতরে) |
| Idempotency | ❌ | ✅ (payment) | ✅ | ✅ (বাধ্যতামূলক) |
| Payment (SSLCommerz/Stripe) | ❌ | ✅ | ✅ (Subscription) | ✅ |
| **Multi-tenancy** | ❌ | ❌ | ✅ **(সবার আগে)** | ⭕ |
| CASL permission | ❌ | ⭕ | ✅ | ⭕ |
| Plan limit / usage metering | ❌ | ❌ | ✅ | ⭕ |
| API key, Webhook out | ❌ | ❌ | ✅ | ⭕ |
| Audit log, Soft delete | ❌ | ⭕ | ✅ | ⭕ |
| Socket.io | ⭕ | ⭕ (chat/live) | ⭕ | ⭕ |
| Search (Meilisearch) | ❌ | ✅ | ✅ | ⭕ |
| Sentry | ⭕ | ✅ | ✅ | ✅ |
| Docker + CI/CD | ⭕ | ✅ | ✅ | ✅ (বাধ্যতামূলক) |
| API Gateway | ❌ | ❌ | ❌ | ✅ |
| RabbitMQ/Kafka, gRPC | ❌ | ❌ | ❌ | ✅ |
| Circuit breaker, Saga, Outbox | ❌ | ❌ | ❌ | ✅ |
| OpenTelemetry, Prometheus | ❌ | ⭕ | ⭕ | ✅ |

✅ = দরকার · ⭕ = প্রয়োজন হলে · ❌ = দরকার নেই

---

## 25. 📅 Practice Roadmap (দিনে ২-৩টা করে)

> **পদ্ধতি:** প্রতিটি টপিকের জন্য একটা ছোট আলাদা repo/ফোল্ডার বানাও।
> ১) পড়ো → ২) নিজে হাতে লেখো (copy-paste না) → ৩) ইচ্ছে করে ভেঙে দেখো (ভুল token, double click, Redis বন্ধ) → ৪) নিজের ভাষায় ৫ লাইনের নোট লেখো।
> একদিনে ২-৩টা করলেই **~৪ সপ্তাহে** সব হয়ে যাবে।

### 🗓️ সপ্তাহ ১ — মজবুত Monolith

| দিন | টপিক (২-৩টা) | মিনি টাস্ক |
|---|---|---|
| ১ | Env validation (20.1) + Pino logging ও requestId (20.4) | `.env` ভুল দিলে server চালু হবে না, প্রতিটি log এ requestId দেখাও |
| ২ | Health check ও graceful shutdown (20.5) + Soft delete (20.6) | `/health/ready` বানাও, Ctrl+C তে ঠিকমতো বন্ধ করো |
| ৩ | Mongo replica set ও Transaction (20.2) | Docker এ replica set চালিয়ে wallet transfer বানাও, মাঝপথে error ফেলে rollback দেখো |
| ৪ | Idempotency (20.3) + Redis lock | Pay বাটন ১০ বার চাপলেও একবারই order হয় |
| ৫ | Refresh token rotation (20.7) | পুরনো refresh token দিয়ে আবার চাইলে reject হচ্ছে কিনা দেখো |
| ৬ | Layered architecture ও DI (20.8) | একটা feature Route→Controller→Service→Repo তে ভাঙো, service এর unit test লেখো |
| ৭ | **রিভিউ ও ছোট প্রজেক্ট** | এই সব দিয়ে একটা mini e-commerce API (order + payment mock) |

### 🗓️ সপ্তাহ ২ — SaaS Core

| দিন | টপিক | মিনি টাস্ক |
|---|---|---|
| ৮ | Multi-tenancy: models, context, plugin (22.1) | দুইটা tenant, একে অন্যের data দেখতে পায় না |
| ৯ | Tenant isolation test (23.2) + Membership/role | "Tenant A দিয়ে B এর data চাও → 403" test লেখো |
| ১০ | Stripe Checkout + Customer Portal (22.2) | Test mode এ subscription কেনো |
| ১১ | Stripe Webhook + Stripe CLI (22.2) | `stripe trigger` দিয়ে সব event টেস্ট, duplicate event আটকাও |
| ১২ | Plan limits ও usage (22.3) | Free এ ৩টা project এর বেশি করতে দিও না |
| ১৩ | CASL permission (22.4) | "member শুধু নিজের project মুছবে" |
| ১৪ | **রিভিউ ও SaaS mini project** | Project-management SaaS: tenant + billing + role |

### 🗓️ সপ্তাহ ৩ — SaaS Advanced

| দিন | টপিক | মিনি টাস্ক |
|---|---|---|
| ১৫ | API key (22.5) + Invitation (22.6) | key একবারই দেখায়, invite link ৭ দিনে expire |
| ১৬ | 2FA (22.7) | Google Authenticator দিয়ে login |
| ১৭ | Audit log (22.8) + Outgoing webhook (22.9) | HMAC signature যাচাইয়ের receiver ও বানাও |
| ১৮ | Meilisearch (22.10) + Feature flags (22.11) | tenant-filtered search |
| ১৯ | Sentry + PostHog (22.12) + Admin/bull-board | ইচ্ছে করে error ফেলে Sentry তে দেখো |
| ২০ | SSLCommerz দিয়ে manual recurring (22.2 শেষ অংশ) | cron দিয়ে মেয়াদ শেষের reminder |
| ২১ | **রিভিউ** | SaaS mini project এ সব যোগ করো |

### 🗓️ সপ্তাহ ৪ — Microservice ও DevOps

| দিন | টপিক | মিনি টাস্ক |
|---|---|---|
| ২২ | Docker + docker-compose (21.12) | নিজের app কে Docker এ চালাও |
| ২৩ | Gateway (21.3) + ২টা service | user-service ও product-service gateway দিয়ে |
| ২৪ | RabbitMQ (21.4) | order-service event পাঠায়, notification-service শোনে, DLQ টেস্ট |
| ২৫ | gRPC (21.5) | order-service gRPC তে user-service থেকে user আনে |
| ২৬ | Circuit breaker + Retry (21.6) | user-service বন্ধ করে দেখো order-service ঝুলে যায় কিনা |
| ২৭ | Outbox (21.7) + Saga (21.8) | publish এর আগে service kill করে দেখো event হারায় না |
| ২৮ | OpenTelemetry + Prometheus (21.10) | Jaeger এ একটা request এর পুরো যাত্রা দেখো |
| ২৯ | GitHub Actions CI/CD (23.4) + k6 load test (23.3) | PR এ অটো test চলে |
| ৩০ | **Capstone** | নিচের project |

### 🏆 Capstone প্রজেক্ট আইডিয়া

1. **Modular Monolith E-commerce:** transaction, idempotent payment (SSLCommerz), Redis cache, BullMQ email
2. **Multi-tenant SaaS (Project Management/Helpdesk/Invoice):** tenant isolation, Stripe subscription, CASL, API key, webhook
3. **Microservice Order System:** gateway + user/product/order/notification, RabbitMQ + Outbox + Saga, tracing

### ✅ নিজেকে যাচাই করার প্রশ্ন

যদি নিচের প্রশ্নগুলোর উত্তর নিজে দিতে পারো, তাহলে সেই স্তর তোমার হয়ে গেছে:

- Transaction কেন replica set ছাড়া চলে না? Atomic operator কখন transaction এর চেয়ে ভালো?
- Idempotency key না থাকলে কী ঘটতে পারে? Webhook consumer idempotent কেন হতে হয়?
- Multi-tenancy তে `unique` index কেন `tenantId` সহ হতে হবে? `insertMany` কেন আলাদা সতর্কতা চায়?
- Payment সফল হলো তা frontend redirect দেখে ধরা কেন ভুল?
- Outbox pattern না থাকলে কোন সমস্যা হয়? Saga তে ব্যর্থ হলে কী ঘটে?
- Circuit breaker, retry ও timeout কীভাবে একসাথে কাজ করে?
- At-least-once delivery তে duplicate message কীভাবে সামলাবে?

---

*Part 1 ছিল "কোন package কী কাজে লাগে", Part 2 হলো "বড় সিস্টেম কীভাবে বানাতে হয়"। একসাথে এই দুই অংশ বুঝলে monolith, SaaS বা microservice — যেকোনো ধরনের project handle করার ভিত্তি তৈরি হয়ে যায়। রোজ ২-৩টা করে এগোলেই চলবে। 🚀*
