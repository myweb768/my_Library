# MongoDB A to Z — Complete Developer Reference

A practical, command-by-command guide to MongoDB: setup, CRUD, operators, aggregation, indexing, admin, and project structure.

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Installation & Connection](#2-installation--connection)
3. [Database Commands](#3-database-commands)
4. [Collection Commands](#4-collection-commands)
5. [CRUD — Create](#5-crud--create)
6. [CRUD — Read (Find)](#6-crud--read-find)
7. [Query Operators](#7-query-operators)
8. [Logical Operators](#8-logical-operators)
9. [Element Operators](#9-element-operators)
10. [Array Operators](#10-array-operators)
11. [Evaluation Operators](#11-evaluation-operators)
12. [CRUD — Update](#12-crud--update)
13. [Update Operators](#13-update-operators)
14. [CRUD — Delete](#14-crud--delete)
15. [Cursor Methods (Sort, Limit, Skip, Projection)](#15-cursor-methods)
16. [Indexing](#16-indexing)
17. [Aggregation Framework](#17-aggregation-framework)
18. [Aggregation Stages](#18-aggregation-stages)
19. [Aggregation Accumulators](#19-aggregation-accumulators)
20. [Relationships (Embedding vs Referencing)](#20-relationships)
21. [Transactions](#21-transactions)
22. [Indexes Management](#22-indexes-management)
23. [User & Role Management](#23-user--role-management)
24. [Backup & Restore](#24-backup--restore)
25. [Replication & Sharding (Basics)](#25-replication--sharding-basics)
26. [Useful Admin/Server Commands](#26-useful-adminserver-commands)
27. [Node.js Project Folder Structure](#27-nodejs-project-folder-structure)
28. [Best Practices](#28-best-practices)

---

## 1. Introduction

MongoDB is a **NoSQL, document-oriented database**. Data is stored as **BSON** (Binary JSON) documents inside **collections**, which live inside a **database**. There's no fixed schema — each document in a collection can have different fields.

```
Database → Collection → Document → Field
   (mydb)  → (users)   → {_id, name} → name
```

---

## 2. Installation & Connection

```bash
# Check installed version
mongod --version

# Start the MongoDB server (daemon)
mongod

# Connect using the Mongo Shell (new shell)
mongosh

# Connect to a specific host/port/db
mongosh "mongodb://localhost:27017/mydb"

# Connect to Atlas (cloud)
mongosh "mongodb+srv://cluster0.mongodb.net/mydb" --username myUser
```
Description: `mongod` runs the database server; `mongosh` is the interactive shell client used to run all commands below.

---

## 3. Database Commands

```bash
show dbs
```
Description: Lists all databases on the server.

```bash
use mydb
```
Description: Switches to (or creates, on first write) a database called `mydb`.

```bash
db
```
Description: Shows the name of the database currently in use.

```bash
db.dropDatabase()
```
Description: Deletes the current database entirely.

```bash
db.stats()
```
Description: Shows storage/statistics info about the current database.

---

## 4. Collection Commands

```bash
show collections
```
Description: Lists all collections in the current database.

```bash
db.createCollection("users")
```
Description: Explicitly creates a new empty collection named `users`.

```bash
db.users.drop()
```
Description: Deletes the `users` collection and all its documents.

```bash
db.users.renameCollection("customers")
```
Description: Renames the `users` collection to `customers`.

---

## 5. CRUD — Create

```bash
db.users.insertOne({ name: "Rafi", age: 25 })
```
Description: Inserts a single document into the `users` collection.

```bash
db.users.insertMany([
  { name: "Rafi", age: 25 },
  { name: "Nabila", age: 22 }
])
```
Description: Inserts multiple documents at once.

---

## 6. CRUD — Read (Find)

```bash
db.users.find()
```
Description: Returns all documents in the collection.

```bash
db.users.find({ age: 25 })
```
Description: Returns documents where `age` equals 25.

```bash
db.users.findOne({ name: "Rafi" })
```
Description: Returns only the first matching document.

```bash
db.users.find().pretty()
```
Description: Prints results in a human-readable, indented format.

```bash
db.users.countDocuments({ age: { $gt: 20 } })
```
Description: Counts documents matching a condition.

---

## 7. Query Operators

| Operator | Meaning | Example |
|---|---|---|
| `$eq` | Equal to | `{ age: { $eq: 25 } }` |
| `$ne` | Not equal to | `{ age: { $ne: 25 } }` |
| `$gt` | Greater than | `{ age: { $gt: 18 } }` |
| `$gte` | Greater than or equal | `{ age: { $gte: 18 } }` |
| `$lt` | Less than | `{ age: { $lt: 30 } }` |
| `$lte` | Less than or equal | `{ age: { $lte: 30 } }` |
| `$in` | Matches any value in array | `{ age: { $in: [20, 25, 30] } }` |
| `$nin` | Matches none of the values | `{ age: { $nin: [20, 25] } }` |

```bash
db.users.find({ age: { $gte: 18, $lte: 30 } })
```
Description: Finds users whose age is between 18 and 30 (inclusive).

---

## 8. Logical Operators

| Operator | Meaning |
|---|---|
| `$and` | All conditions must be true |
| `$or` | At least one condition true |
| `$not` | Negates a condition |
| `$nor` | None of the conditions true |

```bash
db.users.find({ $or: [ { age: 25 }, { name: "Nabila" } ] })
```
Description: Finds users who are 25 years old OR named Nabila.

```bash
db.users.find({ $and: [ { age: { $gt: 18 } }, { city: "Dhaka" } ] })
```
Description: Finds users older than 18 AND living in Dhaka.

---

## 9. Element Operators

```bash
db.users.find({ email: { $exists: true } })
```
Description: Finds documents that have an `email` field.

```bash
db.users.find({ age: { $type: "int" } })
```
Description: Finds documents where `age` is of type integer.

---

## 10. Array Operators

| Operator | Meaning |
|---|---|
| `$all` | Array contains all specified values |
| `$elemMatch` | At least one array element matches all conditions |
| `$size` | Array has exact length |

```bash
db.users.find({ hobbies: { $all: ["reading", "coding"] } })
```
Description: Finds users whose `hobbies` array contains both values.

```bash
db.users.find({ scores: { $elemMatch: { $gte: 80, $lt: 90 } } })
```
Description: Finds users with at least one score between 80–89.

```bash
db.users.find({ hobbies: { $size: 3 } })
```
Description: Finds users with exactly 3 hobbies.

---

## 11. Evaluation Operators

```bash
db.users.find({ name: { $regex: "^R", $options: "i" } })
```
Description: Finds users whose name starts with "R" (case-insensitive).

```bash
db.users.find({ $expr: { $gt: ["$spent", "$budget"] } })
```
Description: Compares two fields of the same document (`spent > budget`).

```bash
db.users.find({ $text: { $search: "developer" } })
```
Description: Full-text search on fields covered by a text index.

---

## 12. CRUD — Update

```bash
db.users.updateOne({ name: "Rafi" }, { $set: { age: 26 } })
```
Description: Updates the first matching document's `age` field.

```bash
db.users.updateMany({ city: "Dhaka" }, { $set: { verified: true } })
```
Description: Updates all matching documents.

```bash
db.users.replaceOne({ name: "Rafi" }, { name: "Rafi", age: 30, city: "Dhaka" })
```
Description: Replaces the entire matched document with a new one.

```bash
db.users.updateOne({ name: "Rafi" }, { $set: { age: 40 } }, { upsert: true })
```
Description: Updates if found, otherwise inserts a new document (`upsert`).

---

## 13. Update Operators

| Operator | Meaning | Example |
|---|---|---|
| `$set` | Set/update a field value | `{ $set: { age: 26 } }` |
| `$unset` | Remove a field | `{ $unset: { age: "" } }` |
| `$inc` | Increment a number | `{ $inc: { age: 1 } }` |
| `$rename` | Rename a field | `{ $rename: { "old": "new" } }` |
| `$push` | Add item to an array | `{ $push: { hobbies: "gaming" } }` |
| `$pull` | Remove item(s) from an array | `{ $pull: { hobbies: "gaming" } }` |
| `$addToSet` | Add to array only if not present | `{ $addToSet: { tags: "vip" } }` |
| `$pop` | Remove first (-1) or last (1) array item | `{ $pop: { hobbies: 1 } }` |

```bash
db.users.updateOne({ name: "Rafi" }, { $inc: { age: 1 }, $push: { hobbies: "gaming" } })
```
Description: Increments `age` by 1 and appends "gaming" to the `hobbies` array in one call.

---

## 14. CRUD — Delete

```bash
db.users.deleteOne({ name: "Rafi" })
```
Description: Deletes the first document matching the filter.

```bash
db.users.deleteMany({ city: "Dhaka" })
```
Description: Deletes all documents matching the filter.

```bash
db.users.deleteMany({})
```
Description: Deletes every document in the collection (collection itself remains).

---

## 15. Cursor Methods

```bash
db.users.find().sort({ age: 1 })
```
Description: Sorts results by `age` ascending (`-1` for descending).

```bash
db.users.find().limit(5)
```
Description: Returns only the first 5 matching documents.

```bash
db.users.find().skip(10)
```
Description: Skips the first 10 matching documents (used for pagination).

```bash
db.users.find({}, { name: 1, age: 1, _id: 0 })
```
Description: **Projection** — returns only `name` and `age` fields, hides `_id`.

```bash
db.users.find().sort({ age: -1 }).skip(10).limit(5)
```
Description: Common pagination pattern — sort, skip, then limit.

---

## 16. Indexing

```bash
db.users.createIndex({ name: 1 })
```
Description: Creates an ascending index on `name` to speed up queries.

```bash
db.users.createIndex({ email: 1 }, { unique: true })
```
Description: Creates a unique index — prevents duplicate emails.

```bash
db.users.getIndexes()
```
Description: Lists all indexes on the collection.

```bash
db.users.dropIndex("name_1")
```
Description: Removes a specific index by name.

```bash
db.users.find({ name: "Rafi" }).explain("executionStats")
```
Description: Shows how MongoDB executes a query (useful to check if an index is used).

---

## 17. Aggregation Framework

```bash
db.users.aggregate([
  { $match: { city: "Dhaka" } },
  { $group: { _id: "$city", total: { $sum: 1 } } }
])
```
Description: A **pipeline** — documents pass through stages (`$match` → `$group`) transformed at each step.

---

## 18. Aggregation Stages

| Stage | Purpose |
|---|---|
| `$match` | Filters documents (like `find`) |
| `$group` | Groups documents and computes aggregates |
| `$project` | Reshapes/selects fields |
| `$sort` | Sorts documents |
| `$limit` | Limits number of documents |
| `$skip` | Skips documents |
| `$lookup` | Joins with another collection |
| `$unwind` | Deconstructs an array field into separate documents |
| `$count` | Counts documents in the pipeline |

```bash
db.orders.aggregate([
  { $lookup: {
      from: "users",
      localField: "userId",
      foreignField: "_id",
      as: "userInfo"
  }}
])
```
Description: Joins `orders` with `users` collection (like a SQL JOIN).

```bash
db.users.aggregate([{ $unwind: "$hobbies" }])
```
Description: Splits a document with an array field into one document per array element.

---

## 19. Aggregation Accumulators

| Accumulator | Meaning |
|---|---|
| `$sum` | Total sum |
| `$avg` | Average value |
| `$min` / `$max` | Minimum / maximum value |
| `$push` | Collects values into an array |
| `$first` / `$last` | First / last value in group |

```bash
db.orders.aggregate([
  { $group: { _id: "$customerId", totalSpent: { $sum: "$amount" }, avgOrder: { $avg: "$amount" } } }
])
```
Description: Calculates total and average order amount per customer.

---

## 20. Relationships

```
Embedding (denormalized)              Referencing (normalized)
------------------------              -------------------------
{                                     users: { _id: 1, name: "Rafi" }
  _id: 1,                             orders: { _id: 101, userId: 1 }
  name: "Rafi",
  orders: [ {..}, {..} ]
}
```
Description: **Embed** related data when it's read together often and small in size; **reference** (store an `_id`) when data is large, shared, or updated independently — join later with `$lookup`.

---

## 21. Transactions

```bash
const session = db.getMongo().startSession()
session.startTransaction()
try {
  db.accounts.updateOne({ _id: 1 }, { $inc: { balance: -100 } }, { session })
  db.accounts.updateOne({ _id: 2 }, { $inc: { balance: 100 } }, { session })
  session.commitTransaction()
} catch (e) {
  session.abortTransaction()
}
```
Description: Ensures multiple operations either **all succeed or all fail together** (ACID transaction).

---

## 22. Indexes Management

```bash
db.users.createIndex({ name: "text", bio: "text" })
```
Description: Creates a text index for full-text search across multiple fields.

```bash
db.users.createIndex({ location: "2dsphere" })
```
Description: Creates a geospatial index for location-based queries.

```bash
db.users.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 })
```
Description: TTL index — auto-deletes documents 1 hour after `createdAt`.

---

## 23. User & Role Management

```bash
use admin
db.createUser({
  user: "admin",
  pwd: "securePass123",
  roles: [ { role: "root", db: "admin" } ]
})
```
Description: Creates an admin user with full privileges.

```bash
db.createUser({
  user: "appUser",
  pwd: "pass123",
  roles: [ { role: "readWrite", db: "mydb" } ]
})
```
Description: Creates a user limited to read/write access on `mydb`.

```bash
db.dropUser("appUser")
```
Description: Removes a user.

---

## 24. Backup & Restore

```bash
mongodump --db mydb --out /backup/
```
Description: Exports (backs up) the entire `mydb` database to a folder.

```bash
mongorestore --db mydb /backup/mydb
```
Description: Restores a database from a `mongodump` backup.

```bash
mongoexport --db mydb --collection users --out users.json
```
Description: Exports a single collection to a JSON file.

```bash
mongoimport --db mydb --collection users --file users.json
```
Description: Imports a JSON/CSV file into a collection.

---

## 25. Replication & Sharding (Basics)

```bash
rs.initiate()
```
Description: Initializes a **replica set** (a group of mongod instances that maintain the same data set for high availability).

```bash
rs.status()
```
Description: Shows the current state of the replica set members.

```bash
sh.enableSharding("mydb")
sh.shardCollection("mydb.users", { userId: 1 })
```
Description: **Sharding** splits large collections across multiple servers (horizontal scaling) using a shard key.

---

## 26. Useful Admin/Server Commands

```bash
db.serverStatus()
```
Description: Detailed info about the running server (connections, memory, ops).

```bash
db.currentOp()
```
Description: Shows currently running operations.

```bash
db.killOp(<opid>)
```
Description: Terminates a long-running operation.

```bash
db.users.validate()
```
Description: Checks a collection's data and index structures for corruption.

---

## 27. Node.js Project Folder Structure

A typical structure for a Node.js + Express + MongoDB (Mongoose) backend:

```
my-mongo-app/
├── src/
│   ├── config/
│   │   └── db.js              # MongoDB connection setup
│   ├── models/
│   │   ├── User.js            # Mongoose schema/model
│   │   └── Order.js
│   ├── controllers/
│   │   ├── userController.js  # Business logic
│   │   └── orderController.js
│   ├── routes/
│   │   ├── userRoutes.js      # Express route definitions
│   │   └── orderRoutes.js
│   ├── middlewares/
│   │   ├── auth.js            # JWT/session auth
│   │   └── errorHandler.js
│   ├── utils/
│   │   └── validators.js
│   └── app.js                 # Express app setup
├── .env                        # DB_URI, PORT, secrets
├── .gitignore
├── package.json
└── server.js                  # Entry point — starts the server
```
Description: Separates concerns — `models` define schema, `controllers` hold logic, `routes` map URLs to controllers, `config` isolates the DB connection.

```javascript
// src/config/db.js
const mongoose = require("mongoose");

const connectDB = async () => {
  await mongoose.connect(process.env.DB_URI);
  console.log("MongoDB connected");
};

module.exports = connectDB;
```
Description: Standard Mongoose connection helper imported once in `server.js`.

---

## 28. Best Practices

- **Design schema around query patterns** — MongoDB is not relational; model data the way you'll read it.
- **Index fields used in `find`, `sort`, and `$lookup`** — but avoid over-indexing (slows writes).
- **Prefer embedding for 1-to-few relationships**, referencing for 1-to-many/many-to-many.
- **Use `$project` early in aggregation pipelines** to reduce data size before heavy stages.
- **Always validate input** before writing (schema validation or app-level, e.g. Joi/Zod).
- **Use transactions sparingly** — only when multi-document atomicity is truly required.
- **Never store secrets in code** — keep connection strings in `.env`.
- **Paginate with `skip`+`limit` for small datasets; use range-based (`_id` cursor) pagination for large ones** — `skip` gets slow on big collections.
- **Back up regularly** with `mongodump`/Atlas snapshots.

---

*End of reference. Use `db.collection.help()` in `mongosh` anytime for command help on a specific collection.*
