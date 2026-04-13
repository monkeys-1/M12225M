# 🏗️ System Architecture Document - "كن شريك" (Kun Sharik)

## 📋 Document Control

| **البند** | **القيمة** |
|-----------|-----------|
| **Version** | 3.0 |
| **Last Updated** | April 2026 |
| **Status** | Approved |
| **Architecture Level** | Production-Ready, Phased Scale |
| **Review Cycle** | كل 3 أشهر |

---

## 🎯 المبادئ المعمارية

### المبادئ الأساسية

| المبدأ | الشرح | التطبيق |
|--------|-------|---------|
| **Start Simple, Scale Smart** | لا نبني لـ 10M مستخدم من اليوم الأول | Modular Monolith → Microservices تدريجياً |
| **Event-Driven** | استجابة فورية عبر الأحداث | Kafka لـ async، WebSocket لـ real-time |
| **AI-Ready** | بنية جاهزة للذكاء الاصطناعي | APIs موحدة + Data Pipeline جاهز |
| **Security by Design** | الأمان مدمج في كل طبقة | Zero Trust + mTLS + Encryption |
| **Cost-Conscious** | تحسين التكلفة مع كل قرار | Reserved instances + Auto-scaling |

### أهداف التصميم حسب المرحلة

```yaml
# المرحلة 1 (الإطلاق - Q1 2025)
phase_1:
  users: 10,000
  availability: 99.9%          # 8.7 ساعات downtime/سنة - واقعي ومقبول
  response_time: "<300ms P95"
  architecture: "Modular Monolith"
  estimated_cost: "$3,000-5,000/month"

# المرحلة 2 (النمو - Q3 2025)
phase_2:
  users: 100,000
  availability: 99.95%         # 4.3 ساعات downtime/سنة
  response_time: "<200ms P95"
  architecture: "Service-Oriented (SOA)"
  estimated_cost: "$10,000-15,000/month"

# المرحلة 3 (التوسع - 2026+)
phase_3:
  users: 1,000,000+
  availability: 99.99%         # 52 دقيقة downtime/سنة
  response_time: "<100ms P95"
  architecture: "Full Microservices"
  estimated_cost: "$30,000-50,000/month"
```

> **ملاحظة مهمة**: 99.999% (5 nines) تكلف أضعاف 99.99% بدون عائد يُذكر للمنصة في مراحلها الأولى. نبدأ بـ 99.9% ونتدرج.

---

## 🌐 الهيكل المعماري العام

```text
┌─────────────────────────────────────────────────────────────────┐
│                         طبقة العميل                             │
│   Web App (Next.js 15) • Mobile (React Native) • Admin Panel   │
└──────────────────────────────┬──────────────────────────────────┘
                               │
┌──────────────────────────────┴──────────────────────────────────┐
│                     طبقة Edge & CDN                             │
│   Cloudflare CDN • Edge Functions • WAF • DDoS Protection      │
└──────────────────────────────┬──────────────────────────────────┘
                               │
┌──────────────────────────────┴──────────────────────────────────┐
│                     طبقة API Gateway                            │
│   Kong/AWS API GW • Rate Limiting • Auth • Request Validation  │
└──────────────────────────────┬──────────────────────────────────┘
                               │
┌──────────────────────────────┴──────────────────────────────────┐
│                   طبقة الخدمات الأساسية                         │
│                                                                 │
│   ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐          │
│   │  Auth &   │ │  Social  │ │Investment│ │Ownership │          │
│   │ Identity  │ │  Graph   │ │  Engine  │ │ Manager  │          │
│   └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘          │
│        │            │            │            │                  │
│   ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐          │
│   │Marketplace│ │   IP &   │ │Accelera-│ │  Notifi- │          │
│   │ & Deals  │ │ Licensing│ │   tor    │ │  cation  │          │
│   └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘          │
│        │            │            │            │                  │
│   ┌──────────┐ ┌──────────┐ ┌──────────┐                       │
│   │ Payment  │ │  Search  │ │  Admin   │                       │
│   │ Gateway  │ │  Engine  │ │ Service  │                       │
│   └──────────┘ └──────────┘ └──────────┘                       │
└──────────────────────────────┬──────────────────────────────────┘
                               │
┌──────────────────────────────┴──────────────────────────────────┐
│                    طبقة الأحداث والرسائل                        │
│   Apache Kafka • Event Store • Dead Letter Queue               │
└──────────────────────────────┬──────────────────────────────────┘
                               │
┌──────────────────────────────┴──────────────────────────────────┐
│                     طبقة البيانات                               │
│                                                                 │
│   ┌────────────┐  ┌────────────┐  ┌────────────┐               │
│   │ PostgreSQL │  │   Redis    │  │Elasticsearch│               │
│   │ (Primary)  │  │  (Cache)   │  │  (Search)   │               │
│   └────────────┘  └────────────┘  └────────────┘               │
│                                                                 │
│   ┌────────────┐  ┌────────────┐                               │
│   │    S3      │  │  ClickHouse│                               │
│   │ (Storage)  │  │ (Analytics)│                               │
│   └────────────┘  └────────────┘                               │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ المكدس التقني (Tech Stack)

### القرارات التقنية مع المبررات

> **قاعدة**: لكل تقنية مبرر واحد واضح. لا تكرار في الأدوار.

#### Frontend

| التقنية | الدور | لماذا هذا الاختيار؟ |
|---------|-------|---------------------|
| Next.js 15 | الإطار الأساسي | App Router + RSC + SSR/SSG + API Routes |
| TypeScript 5.4 | اللغة | Type safety + أفضل DX |
| Tailwind CSS 4 | التصميم | Utility-first + أسرع تطوير + RTL support |
| Zustand | State المحلي | أبسط من Redux، أخف، كافي للمرحلة |
| TanStack Query v5 | Server State | Caching + sync + optimistic updates |
| Socket.io Client | Real-time | WebSocket مع fallback تلقائي |
| Playwright | E2E testing | أسرع وأدق من Cypress |

#### Backend

| التقنية | الدور | لماذا هذا الاختيار؟ |
|---------|-------|---------------------|
| NestJS 10 | الإطار الأساسي | TypeScript + modular + DI + أقرب لـ microservices |
| PostgreSQL 16 | قاعدة البيانات الأساسية | ACID + JSONB + full-text search + proven |
| Redis 7 | Cache + Sessions + Pub/Sub | أسرع، يغني عن Memcached |
| Apache Kafka | Event streaming | At-least-once delivery + ordering + replay |
| Elasticsearch 8 | البحث المتقدم | Full-text Arabic search + analytics |
| S3 / Cloudflare R2 | تخزين الملفات | Object storage + CDN integration |
| ClickHouse | التحليلات | Columnar DB للتقارير السريعة |

> **تم حذف**: CockroachDB (PostgreSQL كافي)، Memcached (Redis يغطي دوره)، RabbitMQ (Kafka كافي)، MongoDB (PostgreSQL JSONB يغطي الحاجة)، Neo4j (PostgreSQL recursive CTEs + later if needed)

#### AI/ML (المرحلة 2+)

| التقنية | الدور | المرحلة |
|---------|-------|---------|
| Python + FastAPI | خدمات ML | Phase 2 |
| PyTorch | تدريب النماذج | Phase 2 |
| MLflow | تتبع التجارب | Phase 2 |
| ONNX Runtime | نشر النماذج | Phase 3 |

> **تم حذف**: TensorFlow (PyTorch كافي + أسهل)، Kubeflow + Seldon + Triton (سابق لأوانه - نضيفهم عند الحاجة الفعلية)

---

## 🧩 تفصيل الخدمات (11 خدمة)

### خريطة الخدمات والتبعيات

```text
                    ┌─────────────┐
                    │  API Gateway │
                    └──────┬──────┘
                           │
         ┌─────────────────┼─────────────────┐
         │                 │                 │
    ┌────┴────┐      ┌────┴────┐      ┌────┴────┐
    │  Auth   │◄────►│ Social  │      │ Search  │
    │ Service │      │ Service │      │ Service │
    └────┬────┘      └────┬────┘      └─────────┘
         │                │
    ┌────┴────┐      ┌────┴────┐
    │ Profile │      │  Feed   │
    │ Service │      │ Service │
    └─────────┘      └─────────┘
         
    ┌─────────┐      ┌─────────┐      ┌─────────┐
    │Invest-  │◄────►│Ownership│◄────►│ Payment │
    │  ment   │      │ Service │      │ Gateway │
    └────┬────┘      └─────────┘      └─────────┘
         │
    ┌────┴────┐      ┌─────────┐      ┌─────────┐
    │Marketplace│    │   IP    │      │Accelera-│
    │ Service │      │ Service │      │   tor   │
    └─────────┘      └─────────┘      └─────────┘

    ─────── Cross-cutting ───────
    ┌─────────┐      ┌─────────┐
    │ Notifi- │      │  Admin  │
    │ cation  │      │ Service │
    └─────────┘      └─────────┘
```

### 1. Identity & Auth Service

```typescript
// المسؤوليات: المصادقة، التحقق، إدارة الجلسات
interface AuthService {
  // التسجيل مع التحقق متعدد المراحل
  register(data: RegisterDTO): Promise<User>;
  
  // دخول مع MFA
  login(credentials: LoginDTO): Promise<AuthTokens>;
  
  // التحقق من الهوية (KYC)
  verifyIdentity(userId: string, docs: KYCDocuments): Promise<VerificationResult>;
  
  // إدارة الصلاحيات (RBAC)
  checkPermission(userId: string, resource: string, action: string): Promise<boolean>;
  
  // تجديد الـ token
  refreshToken(refreshToken: string): Promise<AuthTokens>;
}

// أنواع المستخدمين والأدوار
enum UserRole {
  ENTREPRENEUR = "entrepreneur",    // رائد أعمال
  INVESTOR = "investor",            // مستثمر
  FUND_MANAGER = "fund_manager",    // مدير صندوق
  ADVISOR = "advisor",              // مستشار
  ADMIN = "admin",                  // مدير النظام
  SUPER_ADMIN = "super_admin"       // مدير أعلى
}
```

**قرارات تقنية:**
- JWT (Access Token: 15 دقيقة) + Refresh Token (7 أيام) في httpOnly cookie
- Argon2id لتشفير كلمات المرور (أقوى من bcrypt)
- Rate limiting: 5 محاولات دخول / 15 دقيقة
- OTP عبر SMS (Twilio) + Email (SendGrid)

### 2. Social Graph Service

```typescript
interface SocialService {
  // إدارة الاتصالات
  sendConnectionRequest(fromId: string, toId: string): Promise<void>;
  acceptConnection(requestId: string): Promise<Connection>;
  
  // الشبكة الاجتماعية
  getNetwork(userId: string, depth: number): Promise<NetworkGraph>;
  getSuggestedConnections(userId: string): Promise<User[]>;
  
  // الفيد (المنشورات)
  createPost(data: PostDTO): Promise<Post>;
  getFeed(userId: string, cursor: string): Promise<PaginatedFeed>;
  
  // المجموعات
  createGroup(data: GroupDTO): Promise<Group>;
  joinGroup(userId: string, groupId: string): Promise<void>;
}
```

**قرارات تقنية:**
- Feed مبني على Fan-out-on-write لأقل من 100K مستخدم، يتحول لـ Fan-out-on-read عند التوسع
- PostgreSQL recursive CTEs للعلاقات الاجتماعية (كافي حتى 500K مستخدم، بعدها Neo4j)
- Cursor-based pagination للفيد (أفضل من offset)

### 3. Investment Engine Service

```typescript
interface InvestmentService {
  // الجولات الاستثمارية
  createRound(data: RoundDTO): Promise<InvestmentRound>;
  updateRoundStatus(roundId: string, status: RoundStatus): Promise<void>;
  
  // التمويل الجماعي
  createCampaign(data: CampaignDTO): Promise<Campaign>;
  contribute(campaignId: string, amount: Money): Promise<Contribution>;
  
  // الصناديق
  createFund(data: FundDTO): Promise<Fund>;
  calculateNAV(fundId: string): Promise<Money>;
  
  // التقييم
  generateValuation(companyId: string): Promise<Valuation>;
  generateTermSheet(roundId: string): Promise<Document>;
}

// الدقة المالية - استخدام Decimal.js
type Money = {
  amount: Decimal;          // لا float أبداً!
  currency: "SAR" | "USD" | "EUR";
  precision: 4;             // 4 منازل عشرية
};
```

**قرارات تقنية:**
- **Decimal.js** لكل الحسابات المالية (لا `float` أبداً)
- **PostgreSQL NUMERIC** لتخزين المبالغ
- **Saga Pattern** للمعاملات المالية الموزعة
- **Event Sourcing** لسجل كامل لكل تغيير مالي
- **Dual-entry bookkeeping** للدقة المحاسبية

### 4. Ownership & Cap Table Service

```typescript
interface OwnershipService {
  // جدول الحصص
  createCapTable(companyId: string): Promise<CapTable>;
  addShareClass(capTableId: string, data: ShareClassDTO): Promise<ShareClass>;
  
  // الاستحقاق (Vesting)
  createVestingSchedule(data: VestingDTO): Promise<VestingSchedule>;
  calculateVestedShares(scheduleId: string, date: Date): Promise<Decimal>;
  
  // التوزيعات
  calculateDividends(companyId: string, amount: Money): Promise<Distribution[]>;
  executePayout(distributionId: string): Promise<PayoutResult>;
  
  // SPV
  createSPV(data: SPVDTO): Promise<SPV>;
  manageGovernance(spvId: string): Promise<GovernanceRules>;
}
```

### 5. Marketplace & Deals Service

```typescript
interface MarketplaceService {
  // غرف الصناعات
  createChamber(data: ChamberDTO): Promise<Chamber>;
  joinChamber(userId: string, chamberId: string): Promise<void>;
  
  // الصفقات
  createDeal(data: DealDTO): Promise<Deal>;
  submitOffer(dealId: string, offer: OfferDTO): Promise<Offer>;
  
  // غرف البيانات (Data Rooms)
  createDataRoom(dealId: string): Promise<DataRoom>;
  grantAccess(roomId: string, userId: string, level: AccessLevel): Promise<void>;
  trackActivity(roomId: string): Promise<ActivityLog[]>;
}
```

**قرارات تقنية:**
- Data Room files مشفرة AES-256 + watermarking
- View-only mode بدون تحميل (PDF.js rendering)
- Audit trail كامل لكل وصول

### 6. IP & Licensing Service

```typescript
interface IPService {
  // تسجيل الملكية الفكرية
  registerIP(data: IPDTO): Promise<IPRecord>;
  filePatent(data: PatentDTO): Promise<PatentApplication>;
  
  // تكامل مع الهيئة السعودية (SAIP)
  submitToSAIP(applicationId: string): Promise<SAIPSubmission>;
  trackStatus(submissionId: string): Promise<SAIPStatus>;
  
  // الترخيص
  createLicense(data: LicenseDTO): Promise<License>;
  calculateRoyalties(licenseId: string, period: DateRange): Promise<Money>;
}
```

### 7. Payment Gateway Service

```typescript
interface PaymentService {
  // المدفوعات
  processPayment(data: PaymentDTO): Promise<PaymentResult>;
  refund(paymentId: string, amount?: Money): Promise<RefundResult>;
  
  // المحافظ
  getWalletBalance(userId: string): Promise<Money>;
  transfer(from: string, to: string, amount: Money): Promise<TransferResult>;
  
  // الامتثال
  generateInvoice(paymentId: string): Promise<Invoice>;
  calculateVAT(amount: Money): Promise<TaxBreakdown>;
}
```

**قرارات تقنية:**
- تكامل مع: مدى (MADA)، Apple Pay، مدفوعات SADAD
- PCI DSS Level 1 compliance
- Idempotency keys لمنع الدفع المزدوج
- Webhook retries مع exponential backoff

### 8-11. خدمات أخرى

| الخدمة | المسؤولية الأساسية |
|--------|-------------------|
| **Notification Service** | Push + Email + SMS + In-app notifications مع قوالب عربية |
| **Search Service** | بحث عربي متقدم عبر Elasticsearch مع Arabic analyzer |
| **Accelerator Service** | إدارة برامج التسريع والحاضنات والاستوديوهات |
| **Admin Service** | لوحة إدارة + تقارير + إشراف على المحتوى + إدارة المستخدمين |

---

## 📡 معمارية الأحداث (Event-Driven Architecture)

### أنواع الأحداث

```typescript
// Domain Events - أحداث الأعمال
namespace DomainEvents {
  // المصادقة
  type UserRegistered = { userId: string; role: UserRole; timestamp: Date };
  type UserVerified = { userId: string; verificationLevel: "basic" | "full" };
  
  // الاستثمار
  type RoundCreated = { roundId: string; companyId: string; targetAmount: Decimal };
  type InvestmentMade = { investorId: string; roundId: string; amount: Decimal };
  type RoundClosed = { roundId: string; totalRaised: Decimal };
  
  // المدفوعات
  type PaymentProcessed = { paymentId: string; amount: Decimal; status: "success" | "failed" };
  type PayoutCompleted = { distributionId: string; recipients: number; totalAmount: Decimal };
  
  // الاجتماعي
  type ConnectionEstablished = { user1: string; user2: string };
  type PostPublished = { postId: string; authorId: string; type: PostType };
}
```

### تصميم Kafka

```yaml
kafka_topology:
  cluster:
    brokers: 3                   # المرحلة 1 (يزيد حسب الحاجة)
    replication_factor: 3        # نسخ لكل partition
    min_in_sync_replicas: 2      # ضمان عدم فقدان البيانات
  
  topics:
    # أحداث حرجة (retention طويل)
    financial_events:
      partitions: 12
      retention: "365 days"      # سنة كاملة للامتثال
      cleanup_policy: "compact"
    
    # أحداث اجتماعية (retention متوسط)
    social_events:
      partitions: 24
      retention: "30 days"
      cleanup_policy: "delete"
    
    # إشعارات (retention قصير)
    notification_events:
      partitions: 6
      retention: "7 days"
      cleanup_policy: "delete"
  
  # Dead Letter Queue
  dlq:
    enabled: true
    max_retries: 5
    retry_backoff: "exponential"  # 1s, 2s, 4s, 8s, 16s
    alert_threshold: 100          # تنبيه بعد 100 رسالة فاشلة
```

### أنماط التكامل

```text
┌─────────┐    Event     ┌──────────┐    Event     ┌──────────┐
│Investment│───────────►  │  Kafka   │───────────►  │  Payment │
│ Service  │  "round.    │  Broker  │  consumed    │  Service │
│          │  created"   │          │  by          │          │
└──────────┘             └──────────┘             └────┬─────┘
                              │                        │
                              │                   ┌────┴─────┐
                              │                   │Event:    │
                              │                   │"payment. │
                              │                   │processed"│
                              ▼                   └────┬─────┘
                         ┌──────────┐                  │
                         │Notifica- │◄─────────────────┘
                         │  tion    │
                         │ Service  │
                         └──────────┘
```

---

## 🗄️ تصميم قاعدة البيانات

### PostgreSQL - Schema Strategy

```sql
-- كل خدمة لها schema منفصل (Schema-per-Service)
CREATE SCHEMA auth;        -- بيانات المصادقة
CREATE SCHEMA social;      -- الشبكة الاجتماعية
CREATE SCHEMA investment;  -- الاستثمار والتمويل
CREATE SCHEMA ownership;   -- الملكية والحصص
CREATE SCHEMA marketplace; -- السوق والصفقات
CREATE SCHEMA payment;     -- المدفوعات
CREATE SCHEMA ip;          -- الملكية الفكرية
CREATE SCHEMA admin;       -- الإدارة

-- مثال: جدول المستخدمين
CREATE TABLE auth.users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    phone VARCHAR(20) UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    role VARCHAR(50) NOT NULL DEFAULT 'entrepreneur',
    verification_level VARCHAR(20) DEFAULT 'none',
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    deleted_at TIMESTAMPTZ          -- Soft delete
);

-- مثال: جدول الاستثمارات مع دقة مالية
CREATE TABLE investment.rounds (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    company_id UUID NOT NULL REFERENCES auth.companies(id),
    round_type VARCHAR(50) NOT NULL,   -- seed, series_a, etc.
    target_amount NUMERIC(18,4) NOT NULL,
    raised_amount NUMERIC(18,4) DEFAULT 0,
    currency VARCHAR(3) DEFAULT 'SAR',
    status VARCHAR(30) DEFAULT 'draft',
    opens_at TIMESTAMPTZ,
    closes_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Indexes للأداء
CREATE INDEX idx_users_email ON auth.users(email);
CREATE INDEX idx_users_role ON auth.users(role) WHERE is_active = true;
CREATE INDEX idx_rounds_status ON investment.rounds(status, closes_at);
```

### Redis - استراتيجية التخزين المؤقت

```yaml
redis_strategy:
  sessions:
    prefix: "session:"
    ttl: "24h"
    max_memory: "2GB"
  
  cache:
    prefix: "cache:"
    ttl: "5m - 1h"
    invalidation: "event-based"   # يتم المسح عند تغيير البيانات
    patterns:
      - "cache:user:{id}": "5m"
      - "cache:feed:{userId}": "2m"
      - "cache:round:{id}": "10m"
      - "cache:search:{query}": "1m"
  
  rate_limiting:
    prefix: "rl:"
    algorithm: "sliding_window"
  
  real_time:
    prefix: "ws:"
    usage: "Pub/Sub for WebSocket events"
```

### استراتيجية النسخ الاحتياطي

```yaml
backup_strategy:
  postgresql:
    full_backup: "يومياً الساعة 3:00 صباحاً (توقيت السعودية)"
    incremental: "كل 6 ساعات"
    wal_archiving: "مستمر (Point-in-Time Recovery)"
    retention: "30 يوم"
    testing: "استعادة تجريبية أسبوعياً"
    storage: "S3 مع تشفير AES-256"
  
  redis:
    rdb_snapshot: "كل ساعة"
    aof_persistence: "everysec"
  
  elasticsearch:
    snapshots: "يومياً"
    retention: "14 يوم"
  
  files:
    s3_versioning: "مفعّل"
    cross_region_replication: "المرحلة 2+"
```

---

## 🚀 معمارية النشر (Deployment)

### استراتيجية البنية التحتية

```yaml
# المرحلة 1: Simple & Cost-Effective
phase_1_infrastructure:
  provider: "AWS Middle East (Bahrain)"
  compute:
    type: "ECS Fargate"          # Serverless containers - لا حاجة لـ K8s بعد
    services: 4-6 containers
    autoscaling: "2-8 tasks per service"
  
  database:
    postgresql: "RDS db.r6g.large (Multi-AZ)"
    redis: "ElastiCache r6g.large"
    elasticsearch: "OpenSearch t3.medium.search × 2"
  
  storage:
    s3: "Standard + Intelligent Tiering"
    cloudfront: "Global CDN"
  
  estimated_cost: "$3,500/month"

# المرحلة 2: Scale Up
phase_2_infrastructure:
  compute:
    type: "EKS (Kubernetes)"     # الانتقال لـ K8s عند الحاجة
    nodes: "10-15 (mixed instances)"
    autoscaling: "Cluster Autoscaler + HPA"
  
  database:
    postgresql: "RDS db.r6g.xlarge + Read Replica"
    redis: "ElastiCache Cluster Mode (3 shards)"
  
  estimated_cost: "$12,000/month"

# المرحلة 3: Full Scale
phase_3_infrastructure:
  multi_region: true
  regions: ["me-south-1 (Bahrain)", "eu-west-1 (Ireland)"]
  compute:
    type: "EKS"
    nodes: "30-50 per region"
  
  database:
    postgresql: "Aurora PostgreSQL (Global Database)"
    redis: "ElastiCache Global Datastore"
  
  estimated_cost: "$35,000/month"
```

### CI/CD Pipeline

```yaml
pipeline:
  # الأدوات
  source: "GitHub"
  ci: "GitHub Actions"
  cd: "ArgoCD"                   # GitOps-based deployment
  registry: "AWS ECR"
  secrets: "AWS Secrets Manager"
  
  # المراحل
  stages:
    1_code_quality:
      - lint: "ESLint + Prettier"
      - type_check: "tsc --noEmit"
      - unit_tests: "Jest (minimum 80% coverage)"
      - duration: "~3 minutes"
    
    2_build:
      - docker_build: "Multi-stage Dockerfile"
      - image_tag: "git SHA + semantic version"
      - duration: "~5 minutes"
    
    3_security_scan:
      - sast: "CodeQL"
      - sca: "Snyk (dependency vulnerabilities)"
      - container_scan: "Trivy"
      - secrets_scan: "TruffleHog"
      - duration: "~4 minutes"
    
    4_integration_tests:
      - api_tests: "Supertest"
      - e2e_tests: "Playwright (critical paths)"
      - duration: "~10 minutes"
    
    5_deploy:
      - staging: "Auto-deploy on merge to develop"
      - production:
          strategy: "Canary (10% → 50% → 100%)"
          rollback: "Auto-rollback if error rate > 1%"
          approval: "Manual for major releases"
    
    6_post_deploy:
      - smoke_tests: "Critical path validation"
      - monitoring: "5-minute alert window"
      - notification: "Slack + Email"

  # Rollback Strategy
  rollback:
    automatic_triggers:
      - error_rate: "> 1% for 5 minutes"
      - latency_p95: "> 500ms for 5 minutes"
      - health_check_failures: "> 3 consecutive"
    procedure:
      - revert_to: "Previous stable image"
      - database: "Migration rollback script"
      - cache: "Invalidate affected keys"
      - notification: "Alert on-call engineer"
```

### بيئات النشر

```yaml
environments:
  development:
    url: "dev.kunsharik.com"
    auto_deploy: "on push to feature/*"
    database: "shared dev instance"
    data: "seed data"
  
  staging:
    url: "staging.kunsharik.com"
    auto_deploy: "on merge to develop"
    database: "copy of production schema"
    data: "anonymized production data"
  
  production:
    url: "kunsharik.com"
    deploy: "manual approval required"
    database: "production (Multi-AZ)"
    monitoring: "24/7 alerts"
```

---

## 🔐 معمارية الأمان

### نموذج Zero-Trust

```text
┌─────────────────────────────────────────────────┐
│                  طبقة الحافة                     │
│  Cloudflare WAF • DDoS Protection • Bot Mgmt   │
└──────────────────────┬──────────────────────────┘
                       │
┌──────────────────────┴──────────────────────────┐
│                 طبقة الشبكة                      │
│  VPC Isolation • Security Groups • NACLs        │
│  Private Subnets • VPN for Admin Access         │
└──────────────────────┬──────────────────────────┘
                       │
┌──────────────────────┴──────────────────────────┐
│                طبقة التطبيق                      │
│  mTLS between services • JWT validation         │
│  RBAC + ABAC • Input validation • CORS          │
└──────────────────────┬──────────────────────────┘
                       │
┌──────────────────────┴──────────────────────────┐
│                طبقة البيانات                     │
│  AES-256 at rest • TLS 1.3 in transit           │
│  Column-level encryption • Data masking         │
│  Tokenization for PII                           │
└─────────────────────────────────────────────────┘
```

### إدارة الأسرار (Secret Management)

```yaml
secrets_management:
  tool: "AWS Secrets Manager"
  rotation:
    database_passwords: "كل 90 يوم (تلقائي)"
    api_keys: "كل 180 يوم"
    tls_certificates: "تلقائي عبر ACM"
    jwt_signing_keys: "كل 30 يوم"
  
  access:
    principle: "Least privilege"
    audit: "كل وصول مسجّل"
    emergency: "Break-glass procedure موثق"
```

### حماية البيانات الشخصية

```yaml
data_protection:
  classification:
    public: "معلومات عامة (اسم الشركة، وصف المشروع)"
    internal: "بيانات المستخدم العادية"
    confidential: "بيانات مالية، عقود"
    restricted: "بيانات KYC، أرقام هوية"
  
  pii_handling:
    storage: "مشفرة AES-256 + tokenized"
    access: "Role-based + audit logged"
    retention: "حسب نوع البيان (1-7 سنوات)"
    deletion: "حذف فعلي عند الطلب (GDPR/PDPL)"
    anonymization: "للتحليلات والتقارير"

  gdpr_pdpl_compliance:
    right_to_access: "API endpoint /me/data-export"
    right_to_delete: "API endpoint /me/data-deletion"
    consent_management: "granular consent per purpose"
    data_breach_notification: "خلال 72 ساعة"
```

### الامتثال والتراخيص

| الجهة | المتطلب | الحالة |
|-------|---------|--------|
| هيئة السوق المالية (CMA) | ترخيص التمويل الجماعي | مطلوب قبل الإطلاق |
| هيئة الزكاة والضريبة (ZATCA) | الفوترة الإلكترونية + ضريبة القيمة المضافة | مطلوب |
| SDAIA (PDPL) | حماية البيانات الشخصية | مطلوب |
| PCI DSS Level 1 | معالجة المدفوعات | مطلوب للمدفوعات المباشرة |
| ISO 27001 | أمن المعلومات | المرحلة 2 |
| SOC 2 Type II | ضوابط الخدمات | المرحلة 3 |

---

## 📊 المراقبة والتنبيهات

### Observability Stack

```yaml
observability:
  # المقاييس (Metrics)
  metrics:
    tool: "Prometheus + Grafana"
    retention: "90 يوم"
    dashboards:
      - system: "CPU, Memory, Disk, Network"
      - application: "Request rate, Error rate, Latency (RED)"
      - business: "Signups, Transactions, Revenue"
  
  # السجلات (Logs)
  logging:
    tool: "Loki (via Grafana Stack)"    # أبسط وأرخص من ELK
    format: "Structured JSON"
    levels: "ERROR → WARN → INFO → DEBUG"
    retention: "30 يوم (hot) + 1 سنة (cold/S3)"
    sensitive_data: "مفلترة تلقائياً (PII masking)"
  
  # التتبع (Tracing)
  tracing:
    tool: "Jaeger + OpenTelemetry"
    sampling: "100% للأخطاء، 10% عادي"
    correlation: "Trace ID في كل request"
  
  # التنبيهات (Alerts)
  alerting:
    tool: "Grafana Alerting + PagerDuty"
    channels:
      critical: "PagerDuty → SMS → Phone call"
      warning: "Slack #alerts channel"
      info: "Email digest يومي"
```

### SLAs و SLOs

```yaml
slo_definitions:
  availability:
    target: "99.9% (المرحلة 1)"
    measurement: "successful requests / total requests"
    window: "30 يوم متحرك"
    error_budget: "43.2 دقيقة/شهر"
  
  latency:
    api_p50: "<100ms"
    api_p95: "<300ms"
    api_p99: "<1000ms"
    page_load_lcp: "<2.5s"
  
  error_rate:
    target: "<0.1%"
    measurement: "5xx responses / total responses"
  
  # قواعد التنبيه
  alert_rules:
    - name: "High Error Rate"
      condition: "error_rate > 1% for 5 minutes"
      severity: "critical"
      action: "Page on-call + auto-rollback"
    
    - name: "High Latency"
      condition: "p95 > 500ms for 10 minutes"
      severity: "warning"
      action: "Slack notification"
    
    - name: "Database Connections"
      condition: "connections > 80% of max"
      severity: "warning"
      action: "Scale up + Slack notification"
    
    - name: "Disk Usage"
      condition: "disk_usage > 85%"
      severity: "warning"
      action: "Cleanup + alert"
    
    - name: "Payment Failure Rate"
      condition: "payment_failures > 5% for 5 minutes"
      severity: "critical"
      action: "Page on-call + pause processing"
```

### On-Call و Incident Response

```yaml
on_call:
  rotation: "أسبوعي، شخصان (primary + backup)"
  escalation:
    level_1: "On-call engineer (5 دقائق)"
    level_2: "Team lead (15 دقيقة)"
    level_3: "CTO (30 دقيقة)"
  
  incident_classification:
    P1_critical: "المنصة معطلة بالكامل أو تسريب بيانات"
    P2_major: "خدمة أساسية معطلة (مدفوعات، مصادقة)"
    P3_minor: "خدمة ثانوية متأثرة (إشعارات، بحث)"
    P4_low: "مشكلة تجميلية أو أداء بسيط"
  
  post_incident:
    blameless_postmortem: "خلال 48 ساعة"
    action_items: "مع مسؤول وموعد نهائي"
    knowledge_base: "توثيق في Runbook"
```

---

## 💰 تقدير التكاليف

### تكلفة البنية التحتية الشهرية

```yaml
# المرحلة 1 (الإطلاق)
phase_1_costs:
  compute:
    ecs_fargate: "$800"
  database:
    rds_postgresql: "$400"
    elasticache_redis: "$200"
    opensearch: "$300"
  storage:
    s3: "$50"
    cloudfront: "$100"
  messaging:
    msk_kafka: "$500"
  monitoring:
    grafana_cloud: "$200"
  security:
    cloudflare_pro: "$200"
    secrets_manager: "$50"
  other:
    route53: "$50"
    ses_email: "$50"
    misc: "$100"
  # ─────────────────────
  total: "~$3,000/month"

# المرحلة 2 (النمو)
phase_2_costs:
  total: "~$12,000/month"
  major_additions:
    - "EKS cluster"
    - "Read replicas"
    - "Multi-AZ everything"
    - "Enhanced monitoring"

# المرحلة 3 (التوسع)
phase_3_costs:
  total: "~$35,000-50,000/month"
  major_additions:
    - "Multi-region"
    - "Aurora Global Database"
    - "Dedicated ML infrastructure"
```

---

## 🔄 API Versioning Strategy

```yaml
api_versioning:
  strategy: "URL prefix versioning"
  format: "/api/v{major}"
  current: "/api/v1"
  
  rules:
    - "Breaking changes = new major version"
    - "Additive changes = same version"
    - "Deprecated endpoints = 6 months notice"
    - "Maximum 2 versions supported simultaneously"
  
  headers:
    - "X-API-Version: 1"
    - "Sunset: Sat, 01 Jan 2027 00:00:00 GMT"
    - "Deprecation: true"
```

---

## 📈 استراتيجية التوسع (Scaling Strategy)

### التوسع التدريجي

```yaml
scaling_triggers:
  phase_1_to_2:
    trigger: "10K DAU أو response time > 300ms consistently"
    actions:
      - "Migrate from ECS to EKS"
      - "Add PostgreSQL read replicas"
      - "Enable Redis cluster mode"
      - "Split monolith into 4-6 services"
  
  phase_2_to_3:
    trigger: "100K DAU أو single-region latency issues"
    actions:
      - "Enable multi-region deployment"
      - "Migrate to Aurora Global Database"
      - "Add dedicated ML infrastructure"
      - "Full microservices (11+ services)"

database_scaling_path:
  step_1: "Single PostgreSQL RDS (handles 10K users easily)"
  step_2: "Add read replicas (handles 100K users)"
  step_3: "Aurora PostgreSQL (handles 500K users)"
  step_4: "Aurora Global + Citus sharding (handles 1M+ users)"
```

---

## 🔮 خطة التطور التقني

```yaml
roadmap:
  2025_Q1:
    - "إطلاق MVP (Modular Monolith on ECS)"
    - "PostgreSQL + Redis + Basic Kafka"
    - "CI/CD pipeline كامل"
    - "Monitoring أساسي"
  
  2025_Q2:
    - "التمويل الجماعي + المدفوعات"
    - "Arabic full-text search (Elasticsearch)"
    - "Push notifications"
    - "Data Room مع تشفير"
  
  2025_Q3:
    - "بداية فصل الخدمات (SOA)"
    - "AI: توصيات الشراكات (Phase 1)"
    - "Mobile app (React Native)"
    - "ISO 27001 preparation"
  
  2025_Q4:
    - "Kubernetes migration (EKS)"
    - "Advanced analytics (ClickHouse)"
    - "AI: تقييم المخاطر"
    - "SAIP integration"
  
  2026:
    - "Full microservices architecture"
    - "Multi-region deployment"
    - "Blockchain: سجلات ملكية"
    - "Advanced AI: autonomous matching"
    - "SOC 2 Type II certification"
```

---

## 📝 Approval & Sign-off

| **Role** | **Name** | **Signature** | **Date** |
|----------|----------|---------------|----------|
| Chief Architect | | | |
| DevOps Lead | | | |
| Security Officer | | | |
| Product Manager | | | |

---

**Document History:**
- Version 1.0: Initial architecture draft
- Version 2.0: Added AI integration and multi-cloud strategy
- Version 3.0: Pragmatic rewrite - phased approach, cost estimates, removed over-engineering

**Next Review:** July 2025
