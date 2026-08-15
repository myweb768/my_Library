my-corporate-app/
├── src/
│   ├── config/             # ডাটাবেজ পোর্ট, এআই এপিআই কি (Environment variables)
│   ├── controllers/        # রিকোয়েস্ট ও রেসপন্স হ্যান্ডলার
│   ├── middlewares/        # Auth check, Error handler, Logging
│   ├── models/             # Mongoose schemas (Database blueprints)
│   ├── routes/             # এক্সপ্রেস রাউটস (v1, v2 এভাবে ভাগ করা)
│   ├── services/           # মেইন বিজনেস লজিক এবং AI Integration এর কাজ
│   ├── utils/              # কমন ফাংশন (যেমন: ডেট ফরম্যাট, মেইল সেন্ডার)
│   ├── app.js              # এক্সপ্রেস অ্যাপ কনফিগারেশন
│   └── server.js           # সার্ভার স্টার্ট করার মেইন ফাইল (Entry point)
├── tests/                  # কোড টেস্ট করার জন্য (Unit & Integration tests)
├── .env
└── package.json
