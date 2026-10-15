# Docker Images গাইড — MongoDB, Mongo Express, Redis, Redis UI ও Node

---

## ১. `mongo` — MongoDB ডেটাবেজ

**কী এটা:** MongoDB হলো একটি **NoSQL ডকুমেন্ট ডেটাবেজ**, যেখানে ডেটা JSON-এর মতো (BSON ফরম্যাটে) সংরক্ষিত হয়, টেবিল-রো-কলাম নয়।

- **কেন দরকার:** যখন ডেটার স্ট্রাকচার ফিক্সড নয় (flexible schema), দ্রুত প্রোটোটাইপিং দরকার, অথবা বড় পরিমাণ আনস্ট্রাকচার্ড/সেমি-স্ট্রাকচার্ড ডেটা নিয়ে কাজ করতে হয়।
- **কীভাবে কাজ করে:** এটি একটি সার্ভার হিসেবে রান হয় (ডিফল্ট পোর্ট `27017`), আর তোমার অ্যাপ ড্রাইভার (যেমন `mongoose`) দিয়ে এর সাথে কানেক্ট করে।

```bash
# title: run-mongo-container.sh

# Run a MongoDB container in the background
# -d = detached mode, -p = port mapping (host:container)
# -v = volume, so data persists even if the container is removed
docker run -d \
  --name mongo-db \
  -p 27017:27017 \
  -v mongo-data:/data/db \
  mongo:latest
```

```ts
// title: connect-mongodb-example.ts

// Connecting to MongoDB from a Node.js/TypeScript app using mongoose
import mongoose from "mongoose";

async function connectDB() {
  // "mongo-db" here is the container name (used inside Docker network)
  // or "localhost" if running from your host machine
  await mongoose.connect("mongodb://localhost:27017/myAppDB");
  console.log("MongoDB connected!");
}

connectDB();
```

---

## ২. `mongo-express` — MongoDB-এর জন্য ওয়েব UI

**কী এটা:** Mongo Express হলো একটি **হালকা ওয়েব-বেজড GUI**, যা দিয়ে ব্রাউজার থেকেই MongoDB-এর ডেটাবেজ, কালেকশন, ডকুমেন্ট দেখা ও ম্যানেজ করা যায় — টার্মিনালে কমান্ড না লিখে।

- **কেন দরকার:** ডেভেলপমেন্টের সময় ডেটা ভিজ্যুয়ালি দেখা, এডিট করা, বা ডিবাগ করা সহজ করার জন্য (Compass-এর মতোই, কিন্তু ব্রাউজার-বেজড)।
- **কীভাবে কাজ করে:** এটি নিজে ডেটা স্টোর করে না — শুধু MongoDB সার্ভারের সাথে কানেক্ট হয়ে একটি ইন্টারফেস দেখায়। তাই এটাকে অবশ্যই MongoDB কন্টেইনারের সাথে লিংক/নেটওয়ার্ক শেয়ার করতে হয়।

```bash
# title: run-mongo-express-container.sh

# Run mongo-express and connect it to the running mongo-db container
docker run -d \
  --name mongo-ui \
  -p 8081:8081 \
  -e ME_CONFIG_MONGODB_SERVER=mongo-db \
  --network=my-network \
  mongo-express:latest

# After this, open http://localhost:8081 in your browser
```

**নোট:** `mongo-db` কন্টেইনারটা যেন খুঁজে পায়, সেজন্য দুটো কন্টেইনারকে একই Docker network-এ থাকতে হবে (নিচে কম্পোজ উদাহরণে দেখানো হয়েছে)।

---

## ৩. `redis:alpine` — Redis (ইন-মেমরি ডেটা স্টোর)

**কী এটা:** Redis হলো একটি **ইন-মেমরি key-value স্টোর**, যা মূলত ক্যাশিং (caching), সেশন স্টোরেজ, রেট-লিমিটিং, বা রিয়েল-টাইম মেসেজিং (pub/sub) এর জন্য ব্যবহৃত হয়। `alpine` ট্যাগ মানে এটি হালকা (lightweight) Alpine Linux বেসের উপর বানানো, তাই সাইজ ছোট।

- **কেন দরকার:** ডেটাবেজে বারবার একই কোয়েরি না চালিয়ে, ফ্রিকোয়েন্টলি অ্যাক্সেস হওয়া ডেটা মেমরিতে রেখে অ্যাপকে অনেক দ্রুত করা যায়।
- **কীভাবে কাজ করে:** ডিফল্ট পোর্ট `6379`-এ চলে, ডেটা RAM-এ রাখে (persistence অপশনাল)।

```bash
# title: run-redis-container.sh

# Run Redis container with Alpine base image (lightweight)
docker run -d \
  --name redis-cache \
  -p 6379:6379 \
  redis:alpine
```

```ts
// title: connect-redis-example.ts

// Connecting to Redis from Node.js using the "redis" npm package
import { createClient } from "redis";

const client = createClient({
  url: "redis://localhost:6379",
});

async function cacheExample() {
  await client.connect();

  // Store a value in cache with an expiry of 60 seconds
  await client.set("user:1001", JSON.stringify({ name: "Rifat" }), {
    EX: 60,
  });

  // Read it back
  const cached = await client.get("user:1001");
  console.log("Cached data:", cached);
}

cacheExample();
```

---

## ৪. Redis UI (যেমন `redis-commander` বা `RedisInsight`)

তোমার লিস্টে আলাদাভাবে কোনো Redis UI ইমেজ নেই, কিন্তু যেহেতু তুমি এটা জিজ্ঞেস করেছো — এটা সাধারণত এভাবে ব্যবহার হয়:

**কী এটা:** Mongo Express-এর মতোই, এটি Redis-এর জন্য একটি ব্রাউজার-বেজড ভিজ্যুয়াল টুল, যেখানে key-value পেয়ার, TTL (expiry), ডেটা টাইপ ইত্যাদি দেখা যায়।

- **জনপ্রিয় অপশন:** `rediscommander/redis-commander` অথবা Redis-এর নিজস্ব `redislabs/redisinsight`।
- **কেন দরকার:** টার্মিনালে `redis-cli` কমান্ড না চালিয়ে, চোখে দেখে ডেটা যাচাই করা।

```bash
# title: run-redis-commander-container.sh

# Pull and run redis-commander, pointing it to the redis-cache container
docker run -d \
  --name redis-ui \
  -p 8082:8081 \
  -e REDIS_HOSTS=local:redis-cache:6379 \
  --network=my-network \
  rediscommander/redis-commander:latest

# Open http://localhost:8082 in your browser
```

---

## ৫. `node:20-alpine` — Node.js রানটাইম (অ্যাপ চালানোর জন্য)

**কী এটা:** এটি Node.js ভার্সন ২০-এর হালকা (Alpine-based) ইমেজ, যেখানে তোমার JavaScript/TypeScript অ্যাপ রান করা হয়।

- **কেন দরকার:** তোমার লোকাল মেশিনে Node ইনস্টল না করেও, বা ভিন্ন ভার্সন কনফ্লিক্ট এড়িয়ে, কন্টেইনারের ভেতরে নির্দিষ্ট Node ভার্সনে অ্যাপ চালানো যায়। প্রোডাকশনেও একই ইমেজ ব্যবহার করলে "আমার মেশিনে তো চলছিল" সমস্যা কমে।
- **কীভাবে কাজ করে:** এটি একটি বেস ইমেজ — তুমি নিজের `Dockerfile` লিখে এর উপর তোমার অ্যাপের কোড, `node_modules` ইত্যাদি বসাও।

```dockerfile
# title: Dockerfile-for-ts-app

# Use Node 20 Alpine as the base image (small size, fast build)
FROM node:20-alpine

# Set working directory inside the container
WORKDIR /app

# Copy package files first (leverages Docker layer caching)
COPY package*.json ./
RUN npm install

# Copy the rest of the source code
COPY . .

# Build TypeScript into JavaScript (if applicable)
RUN npm run build

# Expose the app's port
EXPOSE 3000

# Command to run the app
CMD ["node", "dist/index.js"]
```

---

## ৬. `mongodb_with_ts-app` — তোমার নিজের কাস্টম ইমেজ

এটি সম্ভবত উপরের `Dockerfile` দিয়ে তুমি নিজেই বিল্ড করেছো (`docker build -t mongodb_with_ts-app .`) — এটা তোমার TypeScript অ্যাপ, যা MongoDB-এর সাথে কানেক্ট হয়ে কাজ করে। এটি Node Alpine-এর উপর ভিত্তি করে বানানো একটি অ্যাপ্লিকেশন ইমেজ, যা ready-to-run কন্টেইনার হিসেবে থাকে।

---

## ৭. সবগুলো একসাথে — `docker-compose.yml`

বাস্তবে প্রতিটা ইমেজ আলাদাভাবে `docker run` না করে, **Docker Compose** দিয়ে একসাথে ম্যানেজ করাই সহজ ও স্ট্যান্ডার্ড পদ্ধতি:

```yaml
# title: docker-compose.yml

version: "3.8"

services:
  # MongoDB database service
  mongo-db:
    image: mongo:latest
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db

  # Web UI for MongoDB
  mongo-ui:
    image: mongo-express:latest
    ports:
      - "8081:8081"
    environment:
      ME_CONFIG_MONGODB_SERVER: mongo-db
    depends_on:
      - mongo-db

  # Redis cache service
  redis-cache:
    image: redis:alpine
    ports:
      - "6379:6379"

  # Web UI for Redis
  redis-ui:
    image: rediscommander/redis-commander:latest
    ports:
      - "8082:8081"
    environment:
      REDIS_HOSTS: "local:redis-cache:6379"
    depends_on:
      - redis-cache

  # Your custom TypeScript app
  app:
    image: mongodb_with_ts-app:latest
    ports:
      - "3000:3000"
    environment:
      MONGO_URL: mongodb://mongo-db:27017/myAppDB
      REDIS_URL: redis://redis-cache:6379
    depends_on:
      - mongo-db
      - redis-cache

# Named volume for persisting MongoDB data
volumes:
  mongo-data:
```

```bash
# title: run-docker-compose.sh

# Start all services together in the background
docker compose up -d

# Stop and remove all containers (data volume stays intact)
docker compose down
```

---

## ৮. দ্রুত তুলনা টেবিল

| ইমেজ | কাজ | পোর্ট (ডিফল্ট) | UI আছে কি? |
|---|---|---|---|
| `mongo` | ডেটাবেজ (NoSQL) | 27017 | না (কমান্ড লাইন / ড্রাইভার) |
| `mongo-express` | MongoDB-এর ব্রাউজার UI | 8081 | হ্যাঁ |
| `redis:alpine` | ইন-মেমরি ক্যাশ/স্টোর | 6379 | না (CLI দিয়ে অ্যাক্সেস) |
| Redis UI (redis-commander) | Redis-এর ব্রাউজার UI | 8081→8082 | হ্যাঁ |
| `node:20-alpine` | Node.js রানটাইম (বেস ইমেজ) | নির্ভর করে অ্যাপের উপর | না |
| `mongodb_with_ts-app` | তোমার নিজের কাস্টম অ্যাপ | নির্ভর করে অ্যাপের উপর | অ্যাপ অনুযায়ী |

---

### সংক্ষেপে

- **MongoDB** = ডেটা রাখার জায়গা (persistent storage)
- **Mongo Express** = সেই ডেটা চোখে দেখার/এডিট করার UI
- **Redis** = দ্রুত অ্যাক্সেসের জন্য অস্থায়ী (in-memory) ডেটা/ক্যাশ
- **Redis UI** = Redis-এর ডেটা দেখার UI
- **Node Alpine** = তোমার অ্যাপ চালানোর হালকা রানটাইম বেস
- **Docker Compose** = এই সবকিছুকে একসাথে, এক কমান্ডে চালানোর সবচেয়ে সহজ উপায়
