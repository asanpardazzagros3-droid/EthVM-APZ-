# ساختار پروژه‌ات که یک مونورپوی جامع برای پردازش زنجیره‌ای EVM (احتمالاً مربوط به شبکه APZ) با استفاده از Kafka Streams، PostgreSQL، API و Frontend هست، 

---

```markdown
# 🚀 ETHVM-APZ-Chain

[![CI/CD](https://github.com/your-username/ethvm-apz-chain/actions/workflows/ci-cd.yml/badge.svg)](https://github.com/your-username/ethvm-apz-chain/actions/workflows/ci-cd.yml)
[![Docker](https://github.com/your-username/ethvm-apz-chain/actions/workflows/docker-publish.yml/badge.svg)](https://github.com/your-username/ethvm-apz-chain/actions/workflows/docker-publish.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**پلتفرم مانیتورینگ و تحلیل داده‌های بلاکچین APZ** – یک راه‌حل مقیاس‌پذیر مبتنی بر Event-Driven برای ایندکس کردن، پردازش و نمایش تراکنش‌ها و توکن‌های شبکه‌های EVM.

## 🧭 معرفی (Overview)

پروژه `ethvm-apz-chain` یک معماری مدرن و ماژولار برای دریافت داده‌های خام از بلاکچین (EVM)، پردازش آن‌ها با **Kafka Streams**، ذخیره‌سازی در **PostgreSQL** و ارائه از طریق **REST API** و **داشبورد بصری** است. این پروژه با رویکرد **Monorepo** طراحی شده تا تیم‌های توسعه بتوانند به راحتی روی مؤلفه‌های مختلف (Streaming، Backend، Frontend) کار کنند.

### ✨ ویژگی‌های کلیدی
- **پردازش بی‌درنگ (Real-time)**: دریافت و پردازش بلاک‌ها، تراکنش‌ها و نقل‌وانتقالات توکن با استفاده از Kafka Streams.
- **مقیاس‌پذیری بالا**: جداسازی کامل اجزاء با استفاده از Kafka و Docker.
- **ذخیره‌سازی بهینه**: schema.sql بهینه برای ذخیره داده‌های زنجیره‌ای با قابلیت مهاجرت (Migration).
- **مانیتورینگ پیشرفته**: ترکیب **Prometheus + Grafana** برای مانیتورینگ همواره‌فعال پردازش‌ها و عملکرد سیستم.
- **رابط کاربری تعاملی**: نمایش آمار، تراکنش‌ها و وضعیت شبکه APZ.
- **CI/CD خودکار**: استقرار مداوم با استفاده از GitHub Actions.

## 🏗 معماری و ساختار (Architecture)

```

Blockchain (EVM/APZ) 
│
▼
Kafka (Source Connector) 
│
▼
Kafka Streams (Processors: Block, Transaction, Token) 
│
▼
Kafka (Sink Connector) 
│
▼
PostgreSQL (Database) 
│
├──► API Service (Node.js/Express)
│         │
│         ▼
│    Frontend (React/Vue)
│
└──► Prometheus + Grafana (Monitoring)

```

### 📂 ساختار دایرکتوری (Project Tree)

- **`.github/workflows/`**: پایپ‌لاین‌های CI/CD و ساخت ایمیج‌های Docker.
- **`configs/`**: فایل‌های تنظیمات **Kafka Connect** (سورس و سینک)، **Prometheus** و **Grafana**.
- **`docker/`**: فایل‌های کامپوز جداگانه برای راه‌اندازی Kafka، سرویس‌های جانبی و مانیتورینگ.
- **`packages/`**: هسته اصلی پروژه (Monorepo).
  - **`kafka-streams/`**: پردازشگرهای اصلی (نوشته شده با TypeScript) شامل `apz-transaction-processor`، `apz-token-processor` و `apz-block-processor` به همراه اسکیماهای Avro.
  - **`api-service/`**: سرویس RESTful برای ارتباط با دیتابیس و ارائه داده به فرانت‌اند.
  - **`frontend/`**: داشبورد گرافیکی کاربران.
  - **`common/`**: تایپ‌ها و ابزارهای مشترک بین پکیج‌ها.
- **`database/`**: فایل‌های مهاجرت (Migration)، دیتای اولیه (Seeder) و `schema.sql`.
- **`scripts/`**: اسکریپت‌های کمکی برای استقرار، راه‌اندازی Kafka و مانیتورینگ.

## 🛠 پیش‌نیازها (Prerequisites)

- **Node.js** (v18 یا بالاتر)
- **Docker** و **Docker Compose** (برای اجرای Kafka، Postgres و ...)
- **pnpm** یا **npm** (مدیریت پکیج‌های مونورپو)
- **GCC** و ابزارهای build (برای برخی از دیپندنسی‌های Kafka)

## 🚀 شروع سریع (Getting Started)

### 1. کلون کردن پروژه
```bash
git clone https://github.com/your-username/ethvm-apz-chain.git
cd ethvm-apz-chain
```

2. تنظیم متغیرهای محیطی

فایل .env.example را به .env کپی کرده و مقادیر موردنیاز (مانند آدرس Kafka، PostgreSQL و RPC Node) را تنظیم کنید:

```bash
cp .env.example .env
```

3. نصب وابستگی‌ها

```bash
pnpm install
# یا
npm install
```

4. راه‌اندازی زیرساخت با Docker

با یک کامند، Kafka، Zookeeper، PostgreSQL، Prometheus و Grafana را بالا بیاورید:

```bash
docker-compose -f docker/kafka-cluster.yml -f docker/monitoring.yml -f docker/services.yml up -d
```

5. تنظیم Kafka Connectors

کانکتورهای منبع (Source) و مقصد (Sink) را ثبت کنید:

```bash
bash scripts/kafka-setup.sh
```

(این اسکریپت فایل‌های configs/kafka-connect/*.json را به Kafka Connect API ارسال می‌کند.)

6. اجرای مهاجرت دیتابیس

```bash
npm run migrate --workspace=database
```

7. اجرای سرویس‌ها (توسعه)

برای اجرای هر سه پکیج به حالت توسعه (Development):

```bash
# ترمینال 1: پردازشگرهای Kafka Streams
npm run dev --workspace=packages/kafka-streams

# ترمینال 2: API
npm run dev --workspace=packages/api-service

# ترمینال 3: Frontend
npm run dev --workspace=packages/frontend
```

8. دسترسی به سرویس‌ها

· Frontend: http://localhost:3000
· API Swagger/Docs: http://localhost:4000/api-docs
· Grafana: http://localhost:3001 (ورود: admin/admin)
· Prometheus: http://localhost:9090

🐳 استقرار با Docker (Production)

برای ساخت و اجرای کل سیستم به صورت یکپارچه:

```bash
docker-compose -f docker-compose.yml up -d --build
```

تصاویر Docker بر اساس Dockerfileهای موجود در هر پکیج ساخته می‌شوند.

📊 مانیتورینگ و هشدار (Monitoring)

پروژه از Prometheus برای جمع‌آوری متریک‌های اپلیکیشن (میزان تاخیر Kafka، تعداد تراکنش‌ها، وضعیت پردازش‌گرها) و Grafana برای مصورسازی استفاده می‌کند.

· داشبورد از پیش‌تنظیم شده در configs/grafana/dashboard.yml قرار دارد.
· اسکریپت scripts/monitoring.sh وضعیت کلی سرویس‌ها را بررسی می‌کند.

🧪 تست و توسعه (Development Guidelines)

· کد نویسی: از TypeScript استفاده شده و فرمت‌کننده Prettier و Lint در پکیج‌ها فعال است.
· اسکیماها: هرگونه تغییر در داده‌های Kafka مستلزم به‌روزرسانی فایل‌های .avsc در پکیج kafka-streams است.
· Migration: برای تغییر در ساختار دیتابیس، فایل جدیدی در database/migrations ایجاد کنید.

🤝 مشارکت (Contributing)

1. Fork کنید.
2. برچسب feature/your-feature یا fix/your-bug ایجاد کنید.
3. Commit های خود را با پیام‌های معنادار ثبت کنید.
4. Pull Request بفرستید.

📜 لایسنس

این پروژه تحت مجوز MIT منتشر شده است - برای جزئیات بیشتر فایل LICENSE را مشاهده کنید.

---

ساخته شده با ❤️ برای جامعه APZ

```

---

### 💡 نکات تکمیلی برای استفاده از این Readme:
1. **آدرس مخزن** رو در بخش `your-username` اصلاح کن.
2. اگر از **pnpm** استفاده نمی‌کنی، دستوراتش رو به `npm` تغییر بده.
3. فرانت‌اند رو اگر با **React** یا **Vue** خاصی داری، در بخش معرفی ذکر کن (من فعلاً کلی نوشتم).
4. حتماً فایل `.env.example` رو در روت پروژه بساز تا کاربران بتونن کپی‌اش کنن.

اگر نیاز به تغییر یا توضیح بیشتری در بخش خاصی داری، به من بگو! موفق باشی. 🚀

ethvm-apz-chain/
├── 📁 .github/
│   └── 📁 workflows/
│       ├── ci-cd.yml
│       └── docker-publish.yml
├── 📁 configs/
│   ├── kafka-connect/
│   │   ├── apz-source-connector.json
│   │   └── postgres-sink-connector.json
│   ├── prometheus/
│   │   └── prometheus.yml
│   └── grafana/
│       └── dashboard.yml
├── 📁 docker/
│   ├── kafka-cluster.yml
│   ├── monitoring.yml
│   └── services.yml
├── 📁 packages/
│   ├── 📁 kafka-streams/
│   │   ├── 📁 src/
│   │   │   ├── 📁 processors/
│   │   │   │   ├── apz-transaction-processor.ts
│   │   │   │   ├── apz-token-processor.ts
│   │   │   │   └── apz-block-processor.ts
│   │   │   ├── 📁 schemas/
│   │   │   │   ├── transaction.avsc
│   │   │   │   ├── block.avsc
│   │   │   │   └── token-transfer.avsc
│   │   │   ├── 📁 utils/
│   │   │   │   ├── kafka-config.ts
│   │   │   │   └── metrics.ts
│   │   │   └── index.ts
│   │   ├── package.json
│   │   ├── tsconfig.json
│   │   └── Dockerfile
│   ├── 📁 api-service/
│   │   ├── 📁 src/
│   │   │   ├── 📁 controllers/
│   │   │   ├── 📁 services/
│   │   │   ├── 📁 models/
│   │   │   └── server.ts
│   │   └── package.json
│   ├── 📁 frontend/
│   │   ├── 📁 src/
│   │   │   ├── 📁 components/
│   │   │   ├── 📁 views/
│   │   │   └── main.ts
│   │   └── package.json
│   └── 📁 common/
│       ├── 📁 types/
│       ├── 📁 utils/
│       └── package.json
├── 📁 database/
│   ├── migrations/
│   ├── seeders/
│   └── schema.sql
├── 📁 scripts/
│   ├── deploy.sh
│   ├── monitoring.sh
│   └── kafka-setup.sh
├── .env.example
├── docker-compose.yml
├── package.json
├── README.md
├── LICENSE
└── .gitignore
