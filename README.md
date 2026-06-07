# 🗓️ Prisma Schema — DateTime Type গাইড

> **লক্ষ্য:** Prisma-তে `DateTime` field কীভাবে কাজ করে, কোন `@db.*` attribute কখন ব্যবহার করতে হয় — সহজভাবে শেখার জন্য।

---

## Schema

```prisma
generator client {
  provider = "prisma-client-js"
  output   = "../app/generated/prisma"
}

datasource db {
  provider = "postgresql"
}

model User {
  id   Int      @id @default(autoincrement())
  col1 DateTime
  col2 DateTime @db.Date
  col3 DateTime @db.Time(3)
  col4 DateTime @db.Timestamp(6)
  col5 DateTime @db.Timestamptz(6)
  col6 DateTime @db.Timetz(6)
}
```

---

## 🔍 প্রতিটি Column বিস্তারিত ব্যাখ্যা

### `col1` — `DateTime` (কোনো `@db.*` নেই)

```prisma
col1 DateTime
```

| বিষয় | তথ্য |
|------|------|
| **PostgreSQL Type** | `TIMESTAMP(3)` (default) |
| **সংরক্ষণ করে** | তারিখ + সময় |
| **Timezone** | ❌ নেই |
| **Precision** | milliseconds (3 decimal) |
| **কখন ব্যবহার** | সাধারণ তারিখ-সময় রাখতে হলে |

> 💡 **সহজ কথা:** সবচেয়ে সাধারণ DateTime। ডিফল্টে এটাই ব্যবহার হয়।

---

### `col2` — `@db.Date`

```prisma
col2 DateTime @db.Date
```

| বিষয় | তথ্য |
|------|------|
| **PostgreSQL Type** | `DATE` |
| **সংরক্ষণ করে** | শুধু তারিখ (YYYY-MM-DD) |
| **Timezone** | ❌ নেই |
| **সময়** | ❌ সংরক্ষিত হয় না |
| **কখন ব্যবহার** | জন্মতারিখ, ছুটির তারিখ ইত্যাদি |

> 💡 **সহজ কথা:** শুধু দিন/মাস/বছর দরকার হলে। সময়ের দরকার নেই।

---

### `col3` — `@db.Time(3)`

```prisma
col3 DateTime @db.Time(3)
```

| বিষয় | তথ্য |
|------|------|
| **PostgreSQL Type** | `TIME(3)` |
| **সংরক্ষণ করে** | শুধু সময় (HH:MM:SS.mmm) |
| **Timezone** | ❌ নেই |
| **Precision** | 3 = milliseconds পর্যন্ত |
| **কখন ব্যবহার** | দোকান খোলা/বন্ধের সময়, শিফট টাইম |

> 💡 **সহজ কথা:** শুধু সময় দরকার হলে, তারিখ ছাড়া। `(3)` মানে `.000` পর্যন্ত নির্ভুলতা।

---

### `col4` — `@db.Timestamp(6)`

```prisma
col4 DateTime @db.Timestamp(6)
```

| বিষয় | তথ্য |
|------|------|
| **PostgreSQL Type** | `TIMESTAMP(6)` |
| **সংরক্ষণ করে** | তারিখ + সময় |
| **Timezone** | ❌ নেই |
| **Precision** | 6 = microseconds পর্যন্ত |
| **কখন ব্যবহার** | Log রাখা, Event tracking |

> 💡 **সহজ কথা:** `col1` এর মতোই, কিন্তু আরও বেশি নির্ভুল (microseconds)। অনেক সূক্ষ্ম সময় ধরতে পারে।

---

### `col5` — `@db.Timestamptz(6)`

```prisma
col5 DateTime @db.Timestamptz(6)
```

| বিষয় | তথ্য |
|------|------|
| **PostgreSQL Type** | `TIMESTAMPTZ(6)` |
| **সংরক্ষণ করে** | তারিখ + সময় + Timezone |
| **Timezone** | ✅ **আছে** (UTC-তে store হয়) |
| **Precision** | 6 = microseconds পর্যন্ত |
| **কখন ব্যবহার** | আন্তর্জাতিক অ্যাপ, multi-timezone সিস্টেম |

> 💡 **সহজ কথা:** সবচেয়ে নিরাপদ choice যদি ব্যবহারকারীরা বিভিন্ন দেশ থেকে আসে। UTC-তে সেভ করে, দেখানোর সময় লোকাল টাইমে কনভার্ট করে।

---

### `col6` — `@db.Timetz(6)`

```prisma
col6 DateTime @db.Timetz(6)
```

| বিষয় | তথ্য |
|------|------|
| **PostgreSQL Type** | `TIMETZ(6)` |
| **সংরক্ষণ করে** | শুধু সময় + Timezone |
| **Timezone** | ✅ **আছে** |
| **Precision** | 6 = microseconds পর্যন্ত |
| **কখন ব্যবহার** | Timezone-সহ শুধু সময় দরকার হলে |

> ⚠️ **সতর্কতা:** PostgreSQL নিজেই `TIMETZ` ব্যবহার না করার পরামর্শ দেয়। বেশিরভাগ ক্ষেত্রে `Timestamptz` বেশি কার্যকর।

---

## ✅ Schema Validate করার কমান্ড

```bash
npx prisma validate
```

> এই কমান্ড চালালে Prisma চেক করে schema-তে কোনো ভুল আছে কিনা।

---

## 📊 একনজরে তুলনা টেবিল

| Column | PostgreSQL Type | তারিখ | সময় | Timezone | Precision |
|--------|----------------|-------|------|----------|-----------|
| `col1` | `TIMESTAMP(3)` | ✅ | ✅ | ❌ | milliseconds |
| `col2` | `DATE` | ✅ | ❌ | ❌ | দিন |
| `col3` | `TIME(3)` | ❌ | ✅ | ❌ | milliseconds |
| `col4` | `TIMESTAMP(6)` | ✅ | ✅ | ❌ | microseconds |
| `col5` | `TIMESTAMPTZ(6)` | ✅ | ✅ | ✅ | microseconds |
| `col6` | `TIMETZ(6)` | ❌ | ✅ | ✅ | microseconds |

---

## 🎯 Precision (নির্ভুলতা) বোঝার সহজ উপায়

```
Time(3)  → HH:MM:SS.123        ← milliseconds (3 ঘর)
Time(6)  → HH:MM:SS.123456     ← microseconds (6 ঘর)
```

| Precision | মানে | উদাহরণ |
|-----------|------|--------|
| `(3)` | milliseconds | `12:30:45.999` |
| `(6)` | microseconds | `12:30:45.999999` |

> 💡 বেশি সংখ্যা = বেশি নির্ভুল। সাধারণ কাজে `(3)` যথেষ্ট, সিস্টেম লগে `(6)` দরকার।

---

## 🧭 কোনটা কখন ব্যবহার করবে?

```
জন্মতারিখ / ছুটির দিন?
  → @db.Date

শুধু সময় (কোনো তারিখ নেই)?
  → @db.Time(3)

তারিখ + সময় দরকার, timezone নেই?
  → DateTime (default) অথবা @db.Timestamp(6)

আন্তর্জাতিক অ্যাপ / timezone দরকার?
  → @db.Timestamptz(6)  ✅ সবচেয়ে ভালো
```

---

## ⚡ Quick Reference

```prisma
// শুধু তারিখ
birthday   DateTime @db.Date

// তারিখ + সময় (সাধারণ)
createdAt  DateTime @default(now())

// উচ্চ নির্ভুলতা + Timezone (recommended)
updatedAt  DateTime @db.Timestamptz(6)

// শুধু সময়
openTime   DateTime @db.Time(3)
```

---

> 📌 **মনে রাখো:** বেশিরভাগ প্রজেক্টে `DateTime` (default) অথবা `@db.Timestamptz(6)` ব্যবহার করলেই চলে।
