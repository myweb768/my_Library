# AWS + LocalStack Learning Syllabus (Offline, Modern Web Dev Track)

> লক্ষ্য: MERN/Node.js + TypeScript ভিত্তিক ওয়েব ডেভেলপারদের জন্য, যারা LocalStack দিয়ে ফ্রি-তে AWS প্র্যাকটিস করতে চায় এবং ভবিষ্যতে real AWS-এ deploy করার জন্য প্রস্তুত হতে চায়।

---

## কেন AWS শিখবো?

- বেশিরভাগ কোম্পানি production app cloud (AWS/GCP/Azure) এ চালায়, local server এ না।
- নিজে সার্ভার maintain করতে হয় না — scaling, backup, security অনেকটাই managed।
- Job market এ cloud skill (বিশেষ করে AWS) সবচেয়ে বেশি demand করা হয়।
- Modern architecture (serverless, microservices) বোঝার জন্য AWS practical knowledge দরকার।

---

## LocalStack কেন?

- AWS account ছাড়াই, বিনা খরচে, নিজের কম্পিউটারে AWS emulate করে শেখা যায়।
- ভুল করলে বিল আসার ভয় নেই।
- একই commands/SDK code যেটা LocalStack এ কাজ করে, সেটাই সামান্য পরিবর্তনে real AWS এ deploy করা যায়।
- **সীমাবদ্ধতা:** Community (ফ্রি) version এ মাত্র ~১০টা core service পাওয়া যায়। ElastiCache, RDS, DocumentDB, API Gateway, CloudFront ইত্যাদির মতো সার্ভিস Pro plan ছাড়া পাওয়া যায় না।

---

## Phase 0 — Setup & Tooling (✅ প্রায় শেষ)

| কাজ | টুল |
|---|---|
| AWS CLI ইনস্টল | `aws` |
| LocalStack CLI ইনস্টল ও চালু করা | `lstk` |
| awslocal wrapper ইনস্টল | `awslocal` (pipx দিয়ে) |
| Visual dashboard | LocalStack Web App (`https://app.localstack.cloud`) |
| Resource status দেখা | `lstk status` |

---

## Phase 1 — S3 (Object/File Storage)

**কী শিখবো:**
- Bucket তৈরি, list, delete করা
- ফাইল upload/download/delete (`cp`, `ls`, `rm`)
- Folder-like structure (key/prefix দিয়ে path তৈরি)
- Public vs private access, presigned URL
- Node.js AWS SDK দিয়ে programmatically upload করা

**কেন শিখবো:**
- প্রোডাক্ট ইমেজ, প্রোফাইল ছবি, ভিডিও, PDF ইত্যাদি রাখার জন্য এটাই সবচেয়ে বেশি ব্যবহৃত সার্ভিস।
- MongoDB/DynamoDB তে শুধু ফাইলের path/URL রাখা হয়, আসল ফাইল S3 তে থাকে।

**Practice Project:** Product image upload feature (তোমার MongoDB+TS প্রজেক্টে)

---

## Phase 2 — DynamoDB (NoSQL Database)

**কী শিখবো:**
- Table তৈরি, partition key/sort key কনসেপ্ট
- Item put/get/update/delete (CRUD)
- Query vs Scan এর পার্থক্য
- MongoDB এর সাথে তুলনা (schema-less হলেও structure ভিন্ন)

**কেন শিখবো:**
- Serverless architecture এ সবচেয়ে বেশি ব্যবহৃত ডাটাবেস।
- Lambda এর সাথে সবচেয়ে ভালো ইন্টিগ্রেশন।

**Practice Project:** সিম্পল Todo/Order tracking table

---

## Phase 3 — Lambda (Serverless Functions)

**কী শিখবো:**
- Function লেখা ও deploy করা (Node.js runtime)
- Event-driven trigger (S3 upload হলে বা DynamoDB তে change হলে অটো-রান)
- Environment variables, timeout, memory কনফিগারেশন
- Local এ test করা (`lstk` এর মাধ্যমে)

**কেন শিখবো:**
- সার্ভার ছাড়াই কোড রান করার ধারণা (serverless)।
- Background job, scheduled task, event processing এর জন্য দরকার।

**Practice Project:** ছবি upload হলে thumbnail তৈরি করা (S3 trigger → Lambda)

---

## Phase 4 — SQS & SNS (Messaging / Notification)

**কী শিখবো:**
- SQS: queue তৈরি, message পাঠানো/গ্রহণ করা (async processing)
- SNS: topic তৈরি, subscribe/publish (broadcast notification)
- Queue vs Topic এর পার্থক্য কখন কোনটা ব্যবহার করবো

**কেন শিখবো:**
- Order confirmation, email/SMS notification, background processing এর জন্য দরকার।
- Microservices এর মধ্যে communication এর standard পদ্ধতি।

**Practice Project:** Order তৈরি হলে SQS তে message পাঠানো → Lambda সেটা process করে "confirmation" simulate করা

---

## Phase 5 — API Gateway

**কী শিখবো:**
- REST API তৈরি করে Lambda function কে HTTP endpoint হিসেবে expose করা
- Route/method (GET, POST, PUT, DELETE) সেটআপ করা
- Request/response কনফিগারেশন

**কেন শিখবো:**
- Lambda নিজে থেকে HTTP handle করতে পারে না — API Gateway এটাকে ব্রাউজার/ফ্রন্টএন্ড থেকে কল করার মতো একটা URL/endpoint বানিয়ে দেয়।
- সম্পূর্ণ serverless backend বানানোর জন্য এটাই মিসিং পিস (Lambda + DynamoDB + API Gateway = ফুল backend, EC2/Express ছাড়াই)।

**Practice Project:** আগে বানানো DynamoDB CRUD Lambda function গুলোকে API Gateway দিয়ে public endpoint বানানো, Postman দিয়ে টেস্ট করা।

---

## Phase 6 — SES (Simple Email Service)

**কী শিখবো:**
- ইমেইল পাঠানোর জন্য verified sender সেটআপ
- কোড থেকে ইমেইল পাঠানো (order confirmation, OTP, welcome email)

**কেন শিখবো:**
- বাস্তব প্রজেক্টে (e-commerce, auth flow) ইমেইল পাঠানো লাগেই — SES এটার জন্য standard AWS সার্ভিস।

**Practice Project:** ইউজার সাইনআপ করলে welcome email পাঠানো (LocalStack এ email আসলে পাঠাবে না, কিন্তু log এ দেখা যাবে সঠিকভাবে call হয়েছে কিনা)।

---

## Phase 7 — Secrets Manager & SSM Parameter Store

**কী শিখবো:**
- Secret/credential secure ভাবে রাখা (JWT secret, DB URI)
- কোড থেকে fetch করে ব্যবহার করা (hardcode এড়ানো)

**কেন শিখবো:**
- `.env` ফাইলে secret রাখা production এ risky — বাস্তব প্রজেক্টে এভাবেই secret manage হয়।

---

## Phase 8 — IAM (Identity & Access Management)

**কী শিখবো:**
- User, Role, Policy এর concept
- কোন service কোন service কে access করতে পারবে সেটা নিয়ন্ত্রণ করা

**কেন শিখবো:**
- Security এর ভিত্তি — practically এটা ছাড়া কোনো real AWS প্রজেক্ট কাজ করে না।

---

## Full Free (Community) Service List — ক্যাটাগরি অনুযায়ী

| ক্যাটাগরি | Service | কাজ |
|---|---|---|
| App Integration | **API Gateway** | Lambda/backend কে HTTP endpoint হিসেবে expose করা |
| App Integration | **SQS** | Async message queue |
| App Integration | **SNS** | Notification broadcast (multi-subscriber) |
| App Integration | **Step Functions** | একাধিক Lambda কে workflow এ সাজানো |
| App Integration | **EventBridge** | Event-driven trigger (event ঘটলে অন্য service কল করা) |
| App Integration | **Application Auto Scaling** | Resource load অনুযায়ী auto scale করা |
| Compute | **EC2** | Virtual server ভাড়া নেওয়া |
| Compute | **Lambda** | Serverless function |
| Management/Governance | **CloudFormation** | Infrastructure as Code |
| Management/Governance | **CloudWatch / CloudWatch Logs** | Monitoring ও logging |
| Management/Governance | **SSM (Systems Manager)** | Parameter store, server management |
| Business Applications | **SES** | Email পাঠানো |
| Security/Identity | **Secrets Manager** | Credential/secret সংরক্ষণ |
| Security/Identity | **KMS** | Encryption key ম্যানেজমেন্ট |
| Security/Identity | **IAM** | Access/permission নিয়ন্ত্রণ |
| Security/Identity | **Certificate Manager** | SSL/TLS certificate |
| Storage | **S3** | File/object storage |
| Machine Learning | **Transcribe** | Speech-to-text |
| Database | **DynamoDB** | NoSQL managed database |
| Analytics | **Kinesis** | Real-time data streaming |
| Analytics | **Data Firehose** | Streaming data delivery (S3 তে) |
| Analytics | **Route 53** | DNS management |
| Analytics | **OpenSearch Service** | Search engine (product/content search) |
| Analytics | **Redshift** | Data warehouse/analytics |

> নোট: Cognito, ECR, AppConfig, CodeCommit, RDS, ElastiCache, CloudFront — এগুলোর "Base" ট্যাগ থাকলেও LocalStack Community licensing এ সীমিত সাপোর্ট থাকতে পারে; ব্যবহারের আগে `awslocal <service> help` দিয়ে verify করে নেওয়া ভালো।

---

## যেগুলো Community (ফ্রি) LocalStack এ একেবারেই পাবে না (Ultimate/Pro লাগবে)

| Service | কী কাজে লাগে | বিকল্প (শেখার জন্য) |
|---|---|---|
| ElastiCache | Redis/Memcached managed cache | নিজের Docker Redis ব্যবহার চালিয়ে যাও |
| RDS | SQL Database (Postgres/MySQL) managed | নিজের Docker Postgres/MySQL |
| DocumentDB | MongoDB-compatible | নিজের Docker MongoDB |
| CloudFront | CDN | পরে real AWS free-tier account দিয়ে শিখতে পারো |
| SageMaker | ML model training/deploy | পরে real AWS free-tier account |
| Cost Explorer | খরচ analysis | সরাসরি real AWS account এ দরকার হবে তখন |
| Neptune, Timestream | Graph DB, Time-series DB | পরে দরকার হলে real AWS |
| Athena, Glue, Kafka | Big data query/ETL/streaming | পরে দরকার হলে real AWS |
| MQ, MWAA, EKS | Message broker, Airflow, Kubernetes | পরে দরকার হলে real AWS |

---

## সাজেস্টেড Order (কোন phase আগে করবো)

1. **S3** (সবচেয়ে সহজ, তাৎক্ষণিক ব্যবহারযোগ্য) ✅
2. **DynamoDB** (ডেটা মডেলিং শেখা)
3. **Lambda** (serverless ধারণা)
4. **SQS/SNS** (async architecture)
5. **API Gateway** (Lambda কে HTTP endpoint বানানো — পুরো backend serverless করা)
6. **SES** (email পাঠানো)
7. **Secrets Manager**
8. **IAM** (সব শেষে, কারণ এটা conceptual বেশি, hands-on কম)

---

## দরকারি কমান্ড চিট-শিট

```bash
# LocalStack শুরু/বন্ধ
lstk start --persist
lstk stop
lstk status

# S3
awslocal s3 mb s3://bucket-name
awslocal s3 cp file.jpg s3://bucket-name/folder/file.jpg
awslocal s3 ls s3://bucket-name/folder/

# DynamoDB
awslocal dynamodb create-table --table-name Users \
  --attribute-definitions AttributeName=id,AttributeType=S \
  --key-schema AttributeName=id,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST
awslocal dynamodb scan --table-name Users

# Lambda
awslocal lambda create-function --function-name myFunc ...
awslocal lambda invoke --function-name myFunc output.json

# SQS
awslocal sqs create-queue --queue-name my-queue
awslocal sqs send-message --queue-url <url> --message-body "hello"
```

---

## Reference Links

- LocalStack Docs: https://docs.localstack.cloud
- LocalStack Web App: https://app.localstack.cloud
- AWS CLI Docs: https://docs.aws.amazon.com/cli/
