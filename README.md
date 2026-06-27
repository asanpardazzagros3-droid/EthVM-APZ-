


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
