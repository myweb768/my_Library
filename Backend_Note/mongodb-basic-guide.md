# MongoDB শেখার এবং ব্যবহার করার গাইড

এই ফাইলে MongoDB কী, কীভাবে চালু/বন্ধ করতে হয়, কীভাবে ডেটাবেসে ঢুকে কাজ করতে হয় — সবকিছু ধাপে ধাপে বাংলায় বোঝানো হয়েছে। কমান্ডগুলো ইংরেজিতে (যেভাবে টার্মিনালে লিখতে হয়), আর ব্যাখ্যা বাংলায়।

---

## MongoDB কী?

MongoDB হলো একটা **NoSQL ডেটাবেস**। সাধারণ SQL ডেটাবেস (MySQL, PostgreSQL) যেখানে টেবিল আর রো (row) আকারে ডেটা রাখে, সেখানে MongoDB ডেটা রাখে **JSON-এর মতো ডকুমেন্ট (document)** আকারে। যেমন:

```json
{
  "name": "Panshak Solomon",
  "email": "panshak@example.com",
  "invoices": 12
}
```

এই একেকটা জিনিসকে বলে **document**, আর অনেকগুলো document একসাথে থাকলে সেটাকে বলে **collection** (SQL-এর "টেবিল"-এর মতো), আর অনেকগুলো collection একসাথে থাকলে সেটা হয় একটা **database**।

```
Database (যেমন: invoicedb)
   └── Collection (যেমন: users)
          └── Document (যেমন: একজন user-এর তথ্য)
```

---

## ১. MongoDB সার্ভিস চালু/বন্ধ/রিস্টার্ট করা

MongoDB একটা ব্যাকগ্রাউন্ড সার্ভিস (`mongod`) হিসেবে চলে। প্রথমেই এটা চালু আছে কিনা নিশ্চিত করতে হবে।

```bash
# চালু করা
sudo systemctl start mongod

# বন্ধ করা
sudo systemctl stop mongod

# রিস্টার্ট করা (কনফিগ বদলালে দরকার হয়)
sudo systemctl restart mongod

# স্ট্যাটাস দেখা (চলছে কিনা)
sudo systemctl status mongod

# কম্পিউটার বুট হলেই যেন mongod নিজে থেকে চালু হয়
sudo systemctl enable mongod
```

**টিপস:** প্রতিবার কম্পিউটার রিস্টার্ট দেওয়ার পর MongoDB আবার চালু আছে কিনা চেক করে নেওয়া ভালো অভ্যাস — `sudo systemctl status mongod` দিয়ে।

---

## ২. `mongosh` — MongoDB-এর সাথে কথা বলার টুল

`mongosh` (MongoDB Shell) হলো একটা কমান্ড-লাইন টুল, এইটা দিয়ে সরাসরি ডেটাবেসে ঢুকে ডেটা দেখা, যোগ করা, বদলানো, মোছা যায়।

```bash
mongosh
```

এটা চালালে একটা প্রম্পট আসবে, দেখতে অনেকটা এমন:
```
test>
```

এখান থেকে সব কমান্ড এই প্রম্পটের ভিতরে লিখতে হবে।

---

## ৩. Database নিয়ে কাজ করা

### সব database-এর লিস্ট দেখা

```js
show dbs
```

### কোনো database-এ ঢোকা (না থাকলে নতুন তৈরি হবে)

```js
use invoicedb
```

> **গুরুত্বপূর্ণ:** MongoDB-তে `use` করলেই database তৈরি হয়ে যায় না। যতক্ষণ না তার ভিতরে অন্তত একটা document ইনসার্ট করা হয়, ততক্ষণ database-টা `show dbs`-এ দেখাবে না। এটা স্বাভাবিক আচরণ।

### বর্তমানে কোন database-এ আছি সেটা দেখা

```js
db
```

---

## ৪. Collection নিয়ে কাজ করা

### সব collection-এর লিস্ট দেখা (বর্তমান database-এ)

```js
show collections
```

### নতুন collection তৈরি করা (ম্যানুয়ালি, সাধারণত দরকার হয় না)

```js
db.createCollection("users")
```

> সাধারণত আলাদা করে collection বানানো লাগে না — যখন প্রথম document ইনসার্ট করবে, MongoDB নিজে থেকেই collection বানিয়ে নেবে।

---

## ৫. Document ইনসার্ট (যোগ) করা — Create

### একটা document যোগ করা

```js
db.users.insertOne({ name: "Rahim", email: "rahim@example.com", age: 25 })
```

### একসাথে অনেকগুলো document যোগ করা

```js
db.users.insertMany([
  { name: "Karim", email: "karim@example.com", age: 30 },
  { name: "Fatima", email: "fatima@example.com", age: 22 }
])
```

---

## ৬. Document খোঁজা (দেখা) — Read

### সব document দেখা

```js
db.users.find()
```

### সুন্দর করে (readable format-এ) দেখা

```js
db.users.find().pretty()
```

### শর্ত দিয়ে খোঁজা (filter)

```js
db.users.find({ name: "Rahim" })
```

### একটামাত্র document খোঁজা

```js
db.users.findOne({ email: "rahim@example.com" })
```

### শর্ত দিয়ে খোঁজা — তুলনামূলক অপারেটর (comparison)

```js
// age 25 এর বেশি যাদের
db.users.find({ age: { $gt: 25 } })

// age 25 এর কম যাদের
db.users.find({ age: { $lt: 25 } })
```

| অপারেটর | মানে |
|---|---|
| `$gt` | greater than (এর চেয়ে বেশি) |
| `$lt` | less than (এর চেয়ে কম) |
| `$gte` | greater than or equal |
| `$lte` | less than or equal |
| `$ne` | not equal (সমান না) |

---

## ৭. Document আপডেট (পরিবর্তন) করা — Update

### একটা document আপডেট করা

```js
db.users.updateOne(
  { name: "Rahim" },
  { $set: { age: 26 } }
)
```

### একাধিক document আপডেট করা

```js
db.users.updateMany(
  { age: { $lt: 25 } },
  { $set: { status: "young" } }
)
```

---

## ৮. Document মোছা — Delete

### একটা document মোছা

```js
db.users.deleteOne({ name: "Rahim" })
```

### একাধিক document মোছা

```js
db.users.deleteMany({ age: { $lt: 20 } })
```

### পুরো collection খালি করা

```js
db.users.drop()
```

### পুরো database মুছে ফেলা

```js
db.dropDatabase()
```

> ⚠️ **সতর্কতা:** `drop()` আর `dropDatabase()` স্থায়ীভাবে ডেটা মুছে দেয়, ফেরত আনা যায় না। প্রোডাকশন ডেটাতে সাবধানে ব্যবহার করবে।

---

## ৯. `mongosh` থেকে বের হওয়া

```js
exit
```

অথবা `Ctrl + D` চাপলেও বেরিয়ে আসবে।

---

## ১০. Node.js প্রজেক্ট (Mongoose) থেকে কানেক্ট করা

তোমার Accountill প্রজেক্টের মতো Node.js/Express অ্যাপে Mongoose দিয়ে MongoDB-এর সাথে কানেক্ট করার নিয়ম:

```js
import mongoose from 'mongoose'

mongoose.connect(process.env.DB_URL)
  .then(() => console.log('MongoDB connected'))
  .catch((error) => console.log(error.message))
```

`.env` ফাইলে:
```
DB_URL=mongodb://127.0.0.1:27017/invoicedb
```

এখানে `invoicedb` হলো তোমার database-এর নাম — চাইলে যেকোনো নাম দিতে পারো, MongoDB নিজে থেকে বানিয়ে নেবে।

---

## GUI দিয়ে দেখতে চাইলে — MongoDB Compass

টার্মিনালে কমান্ড লিখে লিখে কাজ করা কষ্টকর মনে হলে, **MongoDB Compass** নামে একটা ফ্রি GUI অ্যাপ আছে যেটা দিয়ে মাউস দিয়ে ক্লিক করেই ডেটা দেখা, এডিট করা যায়। এটা ইনস্টল করতে চাইলে বলো, ইনস্টল করার কমান্ড দিয়ে দেব।

---

## Quick Reference (এক নজরে সব কমান্ড)

```bash
# সার্ভিস
sudo systemctl start mongod
sudo systemctl status mongod

# শেল-এ ঢোকা
mongosh
```

```js
// database
show dbs
use mydb

// collection
show collections

// CRUD
db.users.insertOne({ name: "Test" })
db.users.find()
db.users.updateOne({ name: "Test" }, { $set: { name: "Updated" } })
db.users.deleteOne({ name: "Updated" })

// বের হওয়া
exit
```
