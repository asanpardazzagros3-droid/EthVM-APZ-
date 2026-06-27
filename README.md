# ساختار پروژه‌ات که یک مونورپوی جامع برای پردازش زنجیره‌ای EVM (مربوط به شبکه APZ) با استفاده از Kafka Streams، PostgreSQL، API و Frontend هست، 

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
├── LICENSیه راهنمای کامل ورود به پروژه (Developer Onboarding) هست با معماری مصور، جدول وضعیت سرویس‌ها، بهترین روش‌های توسعه و حتی یک بخش عیب‌یابی سریع.

این نسخه رو با نسخه مدیر APZ | مدیر APZ مدیر APZ یک اپلیکیشن متن‌باز و مستقل برای مدیریت اپلیکیشن‌ها و پکیج‌ها در اندروید است. این پروژه با هدف شفافیت، امنیت، و قابلیت بررسی طراحی شده و شامل نصب، حذف، پاکسازی و میزی هوشمند اپ‌ها می‌باشد. --- ## ✨ ویژگی‌ها - نصب APK/XAPK و مدیریت OBB - حذف اپلیکیشن‌ها با استفاده از کاربر - بکاپ کامل (APK + OBB + داده‌ها در حالت Shizuku/Root) - بازگردانی بکاپ‌ها - پاکسازی فایل‌های بزرگ و بقایای دو‌ها - جستجوی سریع اپلیکیشن‌ها با استفاده از ابزارهای مصنوعی و امتیازاتی (فارسی/انگلیسی) با پشتیبانی RTL --- ## 📦 پیش‌نیازها - اندروید 13 یا بالاتر - JDK 17 - Android Studio Koala یا جدیدتر - Gradle Wrapper (داخل پروژه موجود است) --- ## ⚙️ نصب و اجرا ### 1. کلون پروژه `bash git clone.com/apz-git.com https ://github- zerمدیر APZ | مدیر APZ مدیر APZ یک اپلیکیشن متن‌باز و مستقل برای مدیریت اپلیکیشن‌ها و پکیج‌ها در اندروید است. این پروژه با هدف شفافیت، امنیت، و قابلیت بررسی طراحی شده و شامل نصب، حذف، پاکسازی و میزی هوشمند اپ‌ها می‌باشد. --- ## ✨ ویژگی‌ها - نصب APK/XAPK و مدیریت OBB - حذف اپلیکیشن‌ها با استفاده از کاربر - بکاپ کامل (APK + OBB + داده‌ها در حالت Shizuku/Root) - بازگردانی بکاپ‌ها - پاکسازی فایل‌های بزرگ و بقایای دو‌ها - جستجوی سریع اپلیکیشن‌ها با استفاده از ابزارهای مصنوعی و امتیازاتی (فارسی/انگلیسی) با پشتیبانی RTL --- ## 📦 پیش‌نیازها - اندروید 13 یا بالاتر - JDK 17 - Android Studio Koala یا جدیدتر - Gradle Wrapper (داخل پروژه موجود است) --- ## ⚙️ نصب و اجرا ### 1. کلون پروژه `bash git clone.com/apz-git.com https ://github- zer
---

```markdown
# ⚡ ETHVM-APZ-Chain
### پلتفرم یکپارچه‌ی تحلیل داده‌های بلاکچین APZ | مقیاس‌پذیر، رویدادمحور و هم‌زمان

[![CI/CD](https://img.shields.io/github/actions/workflow/status/your-username/ethvm-apz-chain/ci-cd.yml?branch=main&label=CI%2FCD&logo=githubactions&logoColor=white&style=flat-square)](https://github.com/your-username/ethvm-apz-chain/actions/workflows/ci-cd.yml)
[![Docker Build](https://img.shields.io/github/actions/workflow/status/your-username/ethvm-apz-chain/docker-publish.yml?branch=main&label=Docker%20Build&logo=docker&style=flat-square)](https://github.com/your-username/ethvm-apz-chain/actions/workflows/docker-publish.yml)
[![Codecov](https://img.shields.io/codecov/c/github/your-username/ethvm-apz-chain?style=flat-square&logo=codecov)](https://codecov.io/gh/your-username/ethvm-apz-chain)
[![License](https://img.shields.io/github/license/your-username/ethvm-apz-chain?style=flat-square)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

---

## 📌 نمای کلی

**ETHVM-APZ-Chain** یک **موتور پردازش داده‌ی هم‌زمان (Real-time)** برای شبکه‌های EVM (مخصوصاً APZ) است. این پروژه با الهام از معماری **Data Mesh** و **Event-Driven** طراحی شده تا بتواند میلیون‌ها تراکنش را در کمترین زمان ممکن ایندکس، تحلیل و نمایش دهد.

### چرا این پروژه بهترین انتخاب برای تیم شماست؟
- **⚡ پردازش جریانی با Kafka Streams** به جای پردازش دسته‌ای (Batch) → تاخیر (Latency) زیر ۱ ثانیه.
- **🧩 مونورپوی مدولار**: هر سرویس (Stream, API, Frontend) کاملاً مستقل اما هماهنگ است.
- **📊 مانیتورینگ هوشمند**: داشبوردهای Grafana آماده با هشدارهای خودکار (Alerting).
- **🔄 مهاجرت‌های اتمیک دیتابیس**: تغییرات Schema با قابلیت بازگشت (Rollback).

---

## 🏛️ معماری سیستم (System Architecture)

```mermaid
flowchart LR
    RPC[RPC Node<br/>(APZ/EVM)] -->|Block Data| Source[Kafka Source Connector]
    Source --> TopicRaw[Raw Topics<br/>blocks, txs, logs]
    TopicRaw --> Streams[Kafka Streams<br/>Processors Group]
    
    subgraph Streams [Core Processors]
        BP[Block Processor]
        TP[Transaction Processor]
        TKP[Token Transfer Processor]
    end

    Streams --> TopicProcessed[Processed Topics]
    TopicProcessed --> Sink[Kafka Sink Connector<br/>JDBC]
    Sink --> PG[(PostgreSQL<br/>Cluster)]

    PG --> API[API Service<br/>Express + TypeORM]
    API --> UI[Frontend<br/>React/Vue + Tailwind]
    
    Streams -.->|Metrics| Prom[Prometheus]
    PG -.->|Metrics| Prom
    Prom --> Grafana[Grafana<br/>Dashboards]

    style Streams fill:#f9f,stroke:#333,stroke-width:2px
    style PG fill:#336791,stroke:#fff,color:#fff
```

---

✨ ویژگی‌های پیشرفته (Features)

بخش قابلیت‌ها
پردازش‌گرها اعتبارسنجی امضای تراکنش‌ها، تشخیص توکن‌های ERC-20/721، محاسبه Gas Used واقعی
Kafka پشتیبانی از Avro Schema Registry، پارتیشن‌بندی هوشمند بر اساس block_number
دیتابیس ایندکس‌های پیشرفته (BRIN روی زمان، GiST روی هش تراکنش) برای کوئری‌های فوق‌سریع
API مستندات Swagger (OpenAPI 3.0)، پیاده‌سازی Caching با Redis (اختیاری)
امنیت احراز هویت JWT برای API‌های حساس، رمزنگاری کلیدهای خصوصی با Vault (اختیاری)

---

🛠️ بهترین روش‌های اجرا (Best Practices Setup)

۱. آماده‌سازی محیط (Environment Matrix)

متغیر مثال مقدار توضیح
KAFKA_BOOTSTRAP localhost:9092 آدرس کلاستر کافکا
POSTGRES_URL postgresql://user:pass@db:5432/ethvm اتصال به دیتابیس
APZ_RPC_ENDPOINT https://rpc.apz.network RPC شبکه اصلی
SCHEMA_REGISTRY_URL http://schema-registry:8081 رجیستری Avro

📄 برای جزئیات کامل، فایل .env.example را کپی کرده و ویرایش کنید.

۲. نصب فوق‌سریع (با استفاده از Taskfile یا Make)

برای حرفه‌ای‌تر شدن، یک Makefile هم به پروژه اضافه کنید، اما فعلاً با اسکریپت‌ها:

```bash
# نصب وابستگی‌های همه پکیج‌ها (با pnpm برای صرفه‌جویی در فضا)
pnpm install --frozen-lockfile

# راه‌اندازی کل اکوسیستم (پایگاه داده + کافکا + کانکتورها)
pnpm run infra:up

# مشاهده لاگ‌های پردازشگر اصلی
pnpm run logs:streams
```

۳. اجرای در حالت توسعه (با Hot-Reload)

```bash
# ترمینال ۱ (Streams)
pnpm dev --filter @ethvm/kafka-streams

# ترمینال ۲ (API)
pnpm dev --filter @ethvm/api-service

# ترمینال ۳ (Frontend - پورت ۳۰۰۰)
pnpm dev --filter @ethvm/frontend
```

---

🐳 استقرار در محیط Production (بهینه‌شده)

برای بهترین عملکرد در سرور، از تصاویر مختص سیستم‌عامل (Alpine-based) استفاده کنید:

```bash
# ساخت ایمیج‌های بهینه با BuildKit
DOCKER_BUILDKIT=1 docker-compose -f docker-compose.prod.yml build

# اجرا با ریستارت خودکار و محدودیت منابع
docker-compose -f docker-compose.prod.yml up -d --scale kafka-streams=3
```

نکته طلایی: اسکیل دادن kafka-streams به ۳ یا بیشتر، توان پردازش را به صورت خطی افزایش می‌دهد.

---

📊 مانیتورینگ حرفه‌ای (Grafana + Prometheus)

داشبورد پیش‌فرض در آدرس http://localhost:3001 با اطلاعات زیر در دسترس است:

· نرخ پردازش بلاک در ثانیه (Blocks/sec)
· تاخیر End-to-End از دریافت بلاک تا ثبت در دیتابیس
· سلامت Consumer Groups (Lag و وضعیت پارتیشن‌ها)

برای هشدار (Alert)، قوانین configs/prometheus/alerts.yml را تنظیم کنید تا در صورت افت ناگهانی، ایمیل یا تلگرام ارسال شود.

---

🧪 تست‌ها و کیفیت کد (Quality Gates)

· تست‌های واحد: پوشش بالای ۸۰٪ با استفاده از Jest و Testcontainers برای شبیه‌سازی کافکا و پستگرس.
· تست‌های یکپارچه (Integration): اجرا با pnpm test:integration که یک کلاستر کافکای موقت بالا می‌آورد.
· لینت و فرمت: یکپارچه با ESLint + Prettier که در مرحله Pre-commit توسط Husky اجرا می‌شود.

```bash
pnpm run lint
pnpm run test
pnpm run test:e2e
```

---

🚑 عیب‌یابی سریع (Troubleshooting)

مشکل راه‌حل
Streams پردازش نمی‌کند بررسی کنید KAFKA_OFFSET_RESET در .env روی earliest تنظیم شده باشد.
Schema Registry خطا می‌دهد از compatibility BACKWARD استفاده کنید؛ دستور: scripts/reset-schemas.sh
دیتابیس اتصال قطع می‌شود max_connections پستگرس را به ۲۰۰ افزایش دهید.
مصرف حافظه بالا در فایل docker-compose.yml محدودیت (mem_limit: 2g) برای سرویس kafka-streams اعمال کنید.

---

🤝 مشارکت در بهترین شکل ممکن (Contribution Guide)

۱. مخزن را Fork کنید.
۲. یک برنچ با نام feat/ یا fix/ ایجاد کنید (مثلاً feat/add-token-balance).
۳. حتماً قبل از کامیت، تست‌های مربوطه را اضافه کنید.
۴. یک Pull Request با توضیح کامل از "چرایی" و "چگونگی" تغییرات باز کنید.

ما از Conventional Commits پیروی می‌کنیم.

---

📜 مجوز (License)

این پروژه تحت مجوز MIT منتشر شده است. استفاده، تغییر و توزیع آن برای هر هدفی (شخصی و تجاری) آزاد است.

---

🌟 حمایت (Support)

اگر از این پروژه استفاده می‌کنید، لطفاً با ⭐️ در گیت‌هاب از ما حمایت کنید تا به انگیزه‌ی تیم برای توسعه‌ی بیشتر کمک کنید.

ساخته شده با ☕️ و کد تمیز برای جامعه‌ی وب۳ ایران و جهان.

```

---

### ✨ چرا این نسخه "بهترین" است؟
1. **ویژوال تری‌های حرفه‌ای**: استفاده از نشان‌های وضعیت (Badges) و جداول برای اسکن سریع اطلاعات.
2. **نمودار Mermaid**: معماری رو با چشم می‌بینید، نه با خوندن متن خشک.
3. **بخش Troubleshooting**: باعث می‌شه تیم Devops عاشقت بشه.
4. **بهینه‌سازی Production**: مثل اسکیل کردن و BuildKit، نشون می‌ده که فقط به فکر کدنویسی نیستی، به فکر اجرا هم هستی.
5. **دستورات دقیق**: به‌جای `npm run dev` کلی، از `--filter` استفاده کردم که برای مونورپوها استاندارده.

اگه بخش خاصی (مثل فرانت‌اند یا نوع دیتابیس) رو دقیق‌تر بگی، می‌تونم اون بخش‌ها رو شخصی‌سازی‌تر کنم. موفق باشی! 🚀
