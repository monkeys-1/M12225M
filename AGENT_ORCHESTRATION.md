# 🤖 خطة تقسيم العمل على AI Agents - مشروع "كن شريك"

## 🧠 المبدأ: Micro-Agent Architecture

> **كل Agent يأخذ مهمة صغيرة واحدة ومحددة.**
> كلما صغرت المهمة: قلّت الأخطاء، زادت الدقة، سهلت المراجعة.

### كيف يعمل النظام

```text
┌──────────────────────────────────────────────────────┐
│                  أنت (المهندس المشرف)                 │
│          تختار Agent → تعطيه المهمة → تراجع          │
└───────────────────────┬──────────────────────────────┘
                        │
        ┌───────────────┼───────────────┐
        │               │               │
   ┌────┴────┐    ┌─────┴────┐   ┌─────┴────┐
   │ Backend │    │ Frontend │   │    QA    │
   │  Agent  │    │  Agent   │   │  Agent   │
   └─────────┘    └──────────┘   └──────────┘
   ┌─────────┐    ┌──────────┐   ┌──────────┐
   │Database │    │ Security │   │  DevOps  │
   │  Agent  │    │  Agent   │   │  Agent   │
   └─────────┘    └──────────┘   └──────────┘
```

### كيف تستخدم الـ Agents في VS Code

1. **افتح Agent Picker** في Copilot Chat (أو اكتب `@agent-name`)
2. **اختر الـ Agent** المناسب للمهمة (backend, frontend, etc.)
3. **أعطه رقم المهمة** مثلاً: "نفذ المهمة AUTH-003"
4. **راجع الكود** قبل الموافقة
5. **شغّل QA Agent** للمراجعة

---

## 🤖 الـ Agents المتاحة (6 Agents)

| Agent | الملف | الدور | الأدوات |
|-------|-------|-------|---------|
| **Backend** | `.github/agents/backend.agent.md` | APIs, services, business logic | read, edit, search, execute |
| **Frontend** | `.github/agents/frontend.agent.md` | UI components, pages, hooks | read, edit, search |
| **Database** | `.github/agents/database.agent.md` | Schemas, migrations, queries | read, edit, search, execute |
| **Security** | `.github/agents/security.agent.md` | مراجعة أمنية، تشفير، صلاحيات | read, search |
| **DevOps** | `.github/agents/devops.agent.md` | Docker, CI/CD, monitoring | read, edit, search, execute |
| **QA** | `.github/agents/qa.agent.md` | اختبارات، مراجعة جودة | read, edit, search, execute |

---

## 📋 المهام الصغيرة (Micro-Tasks)

### قواعد المهام
- **كل مهمة لها ID فريد** (MODULE-NNN)
- **كل مهمة تُنفذ بـ Agent واحد**
- **التبعيات واضحة** (يعتمد على = depends on)
- **معايير القبول محددة** لكل مهمة

---

## 🏁 المرحلة 0: التأسيس والبنية التحتية

> **الهدف**: مشروع يشتغل، بدون أي feature بعد

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| INIT-001 | إنشاء مشروع Next.js 15 مع TypeScript + App Router | DevOps | — | `npm run dev` يشتغل بدون أخطاء |
| INIT-002 | إعداد Tailwind CSS 4 مع دعم RTL | Frontend | INIT-001 | `dir="rtl"` يعمل + الألوان حسب الـ design tokens |
| INIT-003 | إعداد ESLint + Prettier + Git hooks | DevOps | INIT-001 | `npm run lint` يمر بدون أخطاء |
| INIT-004 | إعداد PostgreSQL schema structure (schema-per-service) | Database | — | الـ schemas الـ 8 موجودة + migration يشتغل |
| INIT-005 | إعداد Redis configuration | DevOps | — | الاتصال بـ Redis ناجح + health check |
| INIT-006 | إعداد Docker Compose للتطوير المحلي | DevOps | INIT-004, INIT-005 | `docker compose up` يشغل كل الخدمات |
| INIT-007 | إعداد CI pipeline (GitHub Actions) | DevOps | INIT-003 | PR يشغل lint + type-check + tests تلقائياً |
| INIT-008 | إعداد هيكل المجلدات (modules, components, hooks, etc.) | Backend | INIT-001 | كل المجلدات موجودة حسب Architecture doc |
| INIT-009 | إعداد i18n (عربي/إنجليزي) مع RTL auto-detection | Frontend | INIT-002 | تبديل اللغة يعمل + الاتجاه يتغير |
| INIT-010 | إعداد Providers (Auth, Theme, I18n, Store) | Frontend | INIT-009 | الـ providers مغلفة في AppProviders.tsx |

---

## 🔐 المرحلة 1: المصادقة والهوية

> **الهدف**: مستخدم يقدر يسجل ويدخل ويخرج

### Backend

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| AUTH-001 | إنشاء جدول `auth.users` مع indexes | Database | INIT-004 | الجدول موجود + UUID + soft delete + indexes |
| AUTH-002 | إنشاء جدول `auth.sessions` + `auth.refresh_tokens` | Database | AUTH-001 | العلاقات صحيحة + TTL columns |
| AUTH-003 | خدمة تشفير كلمات المرور (Argon2id) | Backend | INIT-008 | التشفير والتحقق يعملان + timing-safe comparison |
| AUTH-004 | API endpoint: `POST /api/auth/register` | Backend | AUTH-001, AUTH-003 | تسجيل ناجح + validation + duplicate check |
| AUTH-005 | API endpoint: `POST /api/auth/login` | Backend | AUTH-002, AUTH-003 | JWT access (15min) + refresh token (7d) في httpOnly cookie |
| AUTH-006 | API endpoint: `POST /api/auth/refresh` | Backend | AUTH-005 | Token rotation يعمل + old token invalidated |
| AUTH-007 | API endpoint: `POST /api/auth/logout` | Backend | AUTH-005 | الجلسة تُحذف + cookies تُمسح |
| AUTH-008 | خدمة OTP (إرسال رمز تحقق) | Backend | AUTH-004 | OTP يُرسل عبر email + Rate limit: 5/15min |
| AUTH-009 | API endpoint: `POST /api/auth/verify-otp` | Backend | AUTH-008 | التحقق يعمل + OTP ينتهي بعد 10 دقائق |
| AUTH-010 | نظام الصلاحيات RBAC (roles + permissions) | Backend | AUTH-001 | 6 أدوار معرّفة + middleware يتحقق |
| AUTH-011 | Next.js Middleware للمسارات المحمية | Backend | AUTH-005, AUTH-010 | المسارات المحمية ترجع 401 بدون token |
| AUTH-012 | Rate Limiting: 5 محاولات دخول / 15 دقيقة | Backend | AUTH-005 | المحاولة السادسة ترجع 429 |

### Frontend

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| AUTH-013 | صفحة التسجيل `(auth)/register/page.tsx` | Frontend | AUTH-004 | الحقول + validation + API call + feedback |
| AUTH-014 | صفحة الدخول `(auth)/login/page.tsx` | Frontend | AUTH-005 | الحقول + "تذكرني" + رسائل خطأ واضحة |
| AUTH-015 | مكوّن VerificationModal (إدخال OTP) | Frontend | AUTH-009 | 6 خانات + countdown + إعادة إرسال |
| AUTH-016 | `useAuth` hook (login, logout, user state) | Frontend | AUTH-005, AUTH-007 | الحالة تتحدث تلقائياً + redirect بعد login |
| AUTH-017 | مكوّن AuthGuard (حماية الصفحات) | Frontend | AUTH-016 | الصفحات المحمية ترجع للـ login |
| AUTH-018 | صفحة نسيت كلمة المرور | Frontend | AUTH-008 | إرسال رابط + إعادة تعيين |

### QA

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| AUTH-019 | Unit tests لخدمات المصادقة | QA | AUTH-003→AUTH-012 | تغطية > 80% للـ auth services |
| AUTH-020 | E2E test: سيناريو تسجيل → تحقق → دخول → خروج | QA | AUTH-013→AUTH-018 | السيناريو الكامل يمر |
| AUTH-021 | Security review: OWASP Auth checklist | Security | AUTH-001→AUTH-018 | لا ثغرات في: SQL injection, XSS, CSRF, timing attacks |

---

## 👤 المرحلة 1.5: الملفات الشخصية

> **الهدف**: مستخدم يقدر يعدّل ملفه الشخصي ويرفع صورة

### Backend

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| PROF-001 | إنشاء جدول `social.profiles` + `social.companies` | Database | AUTH-001 | العلاقات مع users صحيحة + JSONB for metadata |
| PROF-002 | API: `GET/PUT /api/profile/:id` | Backend | PROF-001 | CRUD يعمل + owner-only editing |
| PROF-003 | API: `POST /api/upload/avatar` مع S3 | Backend | INIT-008 | رفع صور (max 5MB, jpg/png فقط) + resize |
| PROF-004 | API: `GET /api/profile/:id/connections` | Backend | PROF-001 | Cursor-based pagination |

### Frontend

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| PROF-005 | صفحة الملف الشخصي `(main)/profile/page.tsx` | Frontend | PROF-002 | عرض كل البيانات + responsive |
| PROF-006 | صفحة التعديل `(main)/profile/edit/page.tsx` | Frontend | PROF-002, PROF-003 | نموذج تعديل + رفع صورة + validation |
| PROF-007 | `useProfile` hook | Frontend | PROF-002 | fetch + cache + optimistic updates |
| PROF-008 | صفحة ملف مستخدم آخر `(main)/profile/[userId]/page.tsx` | Frontend | PROF-002 | عرض + زر "اتصال" |

### QA

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| PROF-009 | Unit tests + E2E للملف الشخصي | QA | PROF-001→PROF-008 | تغطية > 80% + E2E يمر |

---

## 💬 المرحلة 2: النظام الاجتماعي

> **الهدف**: فيد + تعليقات + رسائل + مجموعات

### Backend

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| SOC-001 | إنشاء جداول `social.posts`, `social.comments`, `social.reactions` | Database | PROF-001 | الجداول + indexes + cascading deletes |
| SOC-002 | API: `POST/GET /api/feed` مع cursor pagination | Backend | SOC-001 | Feed مرتب بالوقت + cursor-based + limit 20 |
| SOC-003 | API: `POST/DELETE /api/posts/:id/comments` | Backend | SOC-001 | إضافة/حذف تعليق + owner check |
| SOC-004 | API: `POST /api/posts/:id/reactions` | Backend | SOC-001 | Like/Unlike toggle + reaction count |
| SOC-005 | إنشاء جداول `social.conversations`, `social.messages` | Database | PROF-001 | العلاقات صحيحة + message status |
| SOC-006 | WebSocket server للرسائل الفورية | Backend | SOC-005 | رسالة تُرسل وتوصل في < 500ms |
| SOC-007 | API: `GET /api/conversations` + `GET /api/messages/:convId` | Backend | SOC-005 | قائمة محادثات + رسائل مع pagination |
| SOC-008 | إنشاء جداول `social.groups`, `social.memberships` | Database | PROF-001 | Group CRUD + membership roles |
| SOC-009 | API: `CRUD /api/groups` + join/leave | Backend | SOC-008 | إنشاء/تعديل/حذف + عضوية |

### Frontend

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| SOC-010 | صفحة الفيد `(main)/feed/page.tsx` | Frontend | SOC-002 | Infinite scroll + pull to refresh |
| SOC-011 | مكوّن PostCard + CommentSection + ReactionBar | Frontend | SOC-002→SOC-004 | عرض منشور كامل مع تفاعلات |
| SOC-012 | صفحة الرسائل `(main)/messages/page.tsx` | Frontend | SOC-007 | قائمة محادثات + اختيار محادثة |
| SOC-013 | مكوّن ChatInterface + MessageList | Frontend | SOC-006 | رسائل فورية + scroll to bottom + typing indicator |
| SOC-014 | `useFeed` + `useMessaging` hooks | Frontend | SOC-002, SOC-006 | Hooks تعمل مع TanStack Query + WebSocket |
| SOC-015 | صفحة المجموعات `(main)/groups/page.tsx` | Frontend | SOC-009 | عرض + بحث + انضمام |

### QA

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| SOC-016 | Unit tests للفيد والرسائل | QA | SOC-001→SOC-015 | تغطية > 80% |
| SOC-017 | E2E: نشر منشور → تعليق → إرسال رسالة | QA | SOC-010→SOC-013 | السيناريو الكامل يمر |
| SOC-018 | Performance test: الفيد مع 1000 منشور | QA | SOC-002 | Response < 300ms |

---

## 💰 المرحلة 3: الاستثمار والتمويل

> **الهدف**: جولات استثمارية + تمويل جماعي + صناديق
>
> ⚠️ **قاعدة حرجة**: كل الحسابات المالية بـ `Decimal.js` + `NUMERIC(18,4)` في PostgreSQL. لا `float` أبداً!

### Backend

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| INV-001 | إنشاء جداول `investment.rounds`, `investment.contributions` | Database | AUTH-001 | NUMERIC(18,4) لكل المبالغ + indexes |
| INV-002 | إعداد Decimal.js + Money type مشترك | Backend | INIT-008 | `Money { amount: Decimal, currency, precision: 4 }` |
| INV-003 | API: `CRUD /api/rounds` (إنشاء جولة استثمارية) | Backend | INV-001, INV-002 | CRUD + status machine (draft→open→closed) |
| INV-004 | API: `POST /api/rounds/:id/invest` (المشاركة في جولة) | Backend | INV-003 | Idempotency key + min/max validation |
| INV-005 | خدمة حساب التقييم (pre-money, post-money, dilution) | Backend | INV-002 | الحسابات دقيقة لـ 4 منازل عشرية |
| INV-006 | خدمة توليد Term Sheet (PDF) | Backend | INV-003 | PDF يتولد مع كل البيانات |
| INV-007 | إنشاء جداول `investment.campaigns` (التمويل الجماعي) | Database | AUTH-001 | NUMERIC + target + deadline + status |
| INV-008 | API: `CRUD /api/crowdfunding` | Backend | INV-007, INV-002 | Campaign lifecycle + contribution tracking |
| INV-009 | إنشاء جداول `investment.funds`, `investment.lp_positions` | Database | AUTH-001 | Fund + LP positions + NAV tracking |
| INV-010 | API: `CRUD /api/funds` + NAV calculation | Backend | INV-009, INV-002 | NAV calculation دقيق + LP reporting |

### Frontend

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| INV-011 | صفحة الجولات `(investment)/rounds/page.tsx` | Frontend | INV-003 | قائمة + فلاتر + بحث |
| INV-012 | صفحة إنشاء جولة `(investment)/rounds/create/page.tsx` | Frontend | INV-003, INV-005 | نموذج multi-step + حاسبة تقييم |
| INV-013 | صفحة تفاصيل جولة `(investment)/rounds/[roundId]/page.tsx` | Frontend | INV-004 | تفاصيل + مستثمرين + progress bar + زر "استثمر" |
| INV-014 | صفحة التمويل الجماعي `(investment)/crowdfunding/page.tsx` | Frontend | INV-008 | حملات + progress + contribute |
| INV-015 | صفحة المحفظة `(investment)/portfolio/page.tsx` | Frontend | INV-003, INV-008 | ملخص استثمارات + أداء |
| INV-016 | `useInvestmentRounds` + `useCrowdfunding` hooks | Frontend | INV-003, INV-008 | Hooks مع real-time updates |

### QA

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| INV-017 | **اختبارات الدقة المالية** (الأهم!) | QA | INV-002→INV-010 | `0.1 + 0.2 === 0.3` ✓ + لا floating point errors |
| INV-018 | E2E: إنشاء جولة → استثمار → إغلاق | QA | INV-011→INV-016 | السيناريو الكامل يمر |
| INV-019 | Security review: المعاملات المالية | Security | INV-003→INV-010 | Idempotency + race conditions + authorization |

---

## 📊 المرحلة 3.5: الملكية وجداول الحصص

> **الهدف**: Cap table + vesting + توزيعات

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| OWN-001 | إنشاء جداول `ownership.cap_tables`, `ownership.share_classes` | Database | INV-001 | NUMERIC + share classes + vesting |
| OWN-002 | API: `CRUD /api/cap-table/:companyId` | Backend | OWN-001, INV-002 | عرض/تعديل جدول الحصص |
| OWN-003 | خدمة حساب Equity (ownership %, dilution) | Backend | OWN-001, INV-002 | الحسابات دقيقة + مجموع الحصص = 100% دائماً |
| OWN-004 | خدمة Vesting Schedule (cliff, monthly, quarterly) | Backend | OWN-001 | Vesting calculator + cliff handling |
| OWN-005 | إنشاء جداول `ownership.distributions` | Database | OWN-001 | Distribution records + payout status |
| OWN-006 | خدمة حساب التوزيعات (أرباح + dividends) | Backend | OWN-005, INV-002 | التوزيع حسب النسب + pro-rata calculation |
| OWN-007 | إنشاء جداول `ownership.spv` | Database | OWN-001 | SPV entity + governance rules |
| OWN-008 | API: `CRUD /api/spv` | Backend | OWN-007 | SPV lifecycle management |
| OWN-009 | صفحة جدول الحصص `(ownership)/cap-table/page.tsx` | Frontend | OWN-002 | جدول تفاعلي + pie chart |
| OWN-010 | مكوّن EquityCalculator + VestingManager | Frontend | OWN-003, OWN-004 | حاسبة + جدول استحقاق مرئي |
| OWN-011 | صفحة التوزيعات `(ownership)/distributions/page.tsx` | Frontend | OWN-006 | قائمة توزيعات + حالة الدفع |
| OWN-012 | اختبارات دقة: مجموع الحصص = 100% دائماً | QA | OWN-002, OWN-003 | 20+ سيناريو مالي يمر |
| OWN-013 | اختبارات Vesting: cliff + acceleration | QA | OWN-004 | كل حالات الاستحقاق تُحسب صحيحاً |

---

## 💳 المرحلة 4: المدفوعات

> **الهدف**: نظام دفع آمن مع مدى + VAT

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| PAY-001 | إنشاء جداول `payment.transactions`, `payment.wallets` | Database | AUTH-001 | Event sourcing + idempotency keys |
| PAY-002 | تكامل بوابة الدفع (مدى MADA) | Backend | PAY-001, INV-002 | دفع واسترجاع يعملان + webhook handling |
| PAY-003 | نظام المحافظ الإلكترونية | Backend | PAY-001 | إيداع + سحب + رصيد + تاريخ |
| PAY-004 | حاسبة ضريبة القيمة المضافة (ZATCA) | Backend | INV-002 | 15% VAT + إعفاءات + فوترة إلكترونية |
| PAY-005 | Idempotent payment processing | Backend | PAY-002 | نفس الـ idempotency key لا يعالج مرتين |
| PAY-006 | Webhook endpoint للإشعارات من بوابة الدفع | Backend | PAY-002 | Webhook signature verification + replay protection |
| PAY-007 | Security review: PCI DSS checklist | Security | PAY-001→PAY-006 | لا تخزين أرقام بطاقات + تشفير |
| PAY-008 | E2E: دفع → تأكيد → إيصال | QA | PAY-002 | السيناريو الكامل مع mock gateway |

---

## 🏪 المرحلة 5: السوق والصفقات

> **الهدف**: غرف صناعات + صفقات + غرف بيانات

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| MKT-001 | إنشاء جداول `marketplace.chambers`, `marketplace.deals` | Database | AUTH-001 | Chambers + deals + offers |
| MKT-002 | API: `CRUD /api/chambers` | Backend | MKT-001 | إنشاء/إدارة غرف الصناعات |
| MKT-003 | API: `CRUD /api/deals` + offer submission | Backend | MKT-001 | صفقات + عروض + التفاوض |
| MKT-004 | إنشاء جداول `marketplace.data_rooms`, `marketplace.room_access` | Database | MKT-001 | Documents + access levels + audit log |
| MKT-005 | API: `CRUD /api/data-rooms` مع تشفير AES-256 | Backend | MKT-004 | رفع/تحميل مشفر + access control |
| MKT-006 | نظام Audit Trail لغرف البيانات | Backend | MKT-005 | كل وصول مسجّل: من، متى، ماذا شاف |
| MKT-007 | صفحة غرف الصناعات `(marketplace)/chambers/page.tsx` | Frontend | MKT-002 | قائمة + فلاتر + انضمام |
| MKT-008 | صفحة الصفقات `(marketplace)/deals/page.tsx` | Frontend | MKT-003 | عرض + إنشاء + تقديم عرض |
| MKT-009 | صفحة غرفة البيانات `(marketplace)/data-rooms/[roomId]/page.tsx` | Frontend | MKT-005 | عرض مستندات + view-only (PDF.js) + لا تحميل |
| MKT-010 | Security review: Data Room encryption + access | Security | MKT-005, MKT-006 | تشفير AES-256 ✓ + audit trail ✓ + no data leaks |

---

## 🧠 المرحلة 5.5: الملكية الفكرية

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| IP-001 | إنشاء جداول `ip.patents`, `ip.trademarks`, `ip.licenses` | Database | AUTH-001 | CRUD tables + status tracking |
| IP-002 | API: `CRUD /api/ip/patents` + `CRUD /api/ip/trademarks` | Backend | IP-001 | Patent/trademark lifecycle |
| IP-003 | خدمة SAIP integration (stub) | Backend | IP-002 | API client + mock responses |
| IP-004 | API: `CRUD /api/ip/licenses` + royalty calculator | Backend | IP-001, INV-002 | License management + royalty حسابات دقيقة |
| IP-005 | صفحات الملكية الفكرية `(ip)/registry/page.tsx` | Frontend | IP-002 | عرض + تسجيل + تتبع حالة |
| IP-006 | Unit tests + E2E | QA | IP-001→IP-005 | تغطية > 80% |

---

## 🚀 المرحلة 6: المسرعات والحاضنات

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| ACC-001 | إنشاء جداول `accelerator.programs`, `accelerator.cohorts` | Database | AUTH-001 | Programs + cohorts + mentors |
| ACC-002 | API: `CRUD /api/programs` + cohort management | Backend | ACC-001 | Program lifecycle + cohort enrollment |
| ACC-003 | API: mentor network + matching | Backend | ACC-001, PROF-001 | Mentor assignment + availability |
| ACC-004 | صفحة البرامج `(accelerator)/programs/page.tsx` | Frontend | ACC-002 | قائمة برامج + تقديم + متابعة |
| ACC-005 | صفحة تفاصيل `(accelerator)/programs/[programId]/page.tsx` | Frontend | ACC-002 | Timeline + mentors + cohort progress |
| ACC-006 | Unit tests + E2E | QA | ACC-001→ACC-005 | تغطية > 80% |

---

## 🔔 المرحلة 7: الإشعارات والبحث والإدارة

### الإشعارات

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| NOTIF-001 | خدمة الإشعارات (in-app + email + push) | Backend | AUTH-001 | إشعارات تُرسل عند كل event مهم |
| NOTIF-002 | مكوّن NotificationCenter + NotificationBell | Frontend | NOTIF-001 | Badge count + dropdown + mark as read |
| NOTIF-003 | صفحة تفضيلات الإشعارات | Frontend | NOTIF-001 | تحكم granular: email on/off, push on/off |

### البحث

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| SRCH-001 | إعداد Elasticsearch مع Arabic analyzer | DevOps | INIT-006 | Index يدعم بحث عربي |
| SRCH-002 | API: `GET /api/search?q=` (unified search) | Backend | SRCH-001 | بحث في: مستخدمين + شركات + جولات + صفقات |
| SRCH-003 | مكوّن GlobalSearch + SearchResults | Frontend | SRCH-002 | بحث فوري + فلاتر + نتائج مجمعة |

### لوحة الإدارة

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| ADM-001 | API: admin endpoints (users, reports, moderation) | Backend | AUTH-010 | SUPER_ADMIN + ADMIN فقط |
| ADM-002 | صفحة الإدارة `admin/page.tsx` | Frontend | ADM-001 | Dashboard + إحصائيات + إدارة مستخدمين |
| ADM-003 | نظام مراجعة المحتوى (moderation queue) | Backend | ADM-001 | Report + review + action (warn/ban/delete) |

---

## 🎨 المرحلة 8: المكونات المشتركة والتصميم

> **يمكن تنفيذها بالتوازي مع المراحل الأخرى**

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| UI-001 | Design tokens (colors, typography, spacing) | Frontend | INIT-002 | Tokens file + Tailwind config |
| UI-002 | مكونات الأزرار (Primary, Secondary, Icon) | Frontend | UI-001 | 3 أنماط + sizes + loading state |
| UI-003 | مكونات النماذج (Input, Select, DatePicker) | Frontend | UI-001 | Validation + error messages + RTL |
| UI-004 | مكونات الـ Modals (Base, Confirmation, Drawer) | Frontend | UI-001 | Open/close + keyboard trap + a11y |
| UI-005 | مكونات عرض البيانات (DataTable, Card, Badge) | Frontend | UI-001 | Sort + filter + pagination + responsive |
| UI-006 | مكونات التنقل (Breadcrumbs, Tabs, Pagination) | Frontend | UI-001 | Active state + RTL + responsive |
| UI-007 | مكونات الـ Feedback (Spinner, Toast, ErrorBoundary) | Frontend | UI-001 | Loading states + error recovery |
| UI-008 | Layout: MainLayout + DashboardLayout + AuthLayout | Frontend | UI-001, INIT-010 | Sidebar + Header + responsive |
| UI-009 | Responsive hooks (useBreakpoint, useDeviceType) | Frontend | INIT-001 | Hooks تعمل مع SSR |
| UI-010 | مكونات تكيفية (ResponsiveContainer, LazyLoader) | Frontend | UI-009 | Code splitting + lazy loading |

---

## ⚙️ المرحلة 9: البنية التحتية والنشر

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| OPS-001 | Dockerfile (multi-stage build) | DevOps | INIT-001 | Image < 200MB + non-root user |
| OPS-002 | docker-compose.yml للإنتاج | DevOps | OPS-001, INIT-006 | كل الخدمات + health checks |
| OPS-003 | GitHub Actions: CI pipeline كامل | DevOps | INIT-007 | lint → type-check → test → build → scan |
| OPS-004 | GitHub Actions: CD pipeline (staging) | DevOps | OPS-003 | Auto-deploy on merge to develop |
| OPS-005 | GitHub Actions: CD pipeline (production) | DevOps | OPS-004 | Manual approval + canary deployment |
| OPS-006 | Security scanning (CodeQL + Snyk + Trivy) | DevOps | OPS-003 | Scan يشتغل في كل PR |
| OPS-007 | Environment configs (.env.dev, .env.staging, .env.prod) | DevOps | INIT-001 | كل البيئات معرّفة + no secrets in repo |
| OPS-008 | إعداد Prometheus + Grafana | DevOps | OPS-002 | Metrics collection + 3 dashboards |
| OPS-009 | إعداد Loki (logging) | DevOps | OPS-002 | Structured JSON logs + 30 days retention |
| OPS-010 | إعداد alert rules | DevOps | OPS-008 | Error rate > 1% → alert + latency > 500ms → alert |
| OPS-011 | Backup strategy: PostgreSQL WAL + daily snapshots | DevOps | INIT-004 | Automated backup + restore test script |

---

## 🧪 المرحلة 10: الاختبارات الشاملة

| ID | المهمة | Agent | يعتمد على | معايير القبول |
|----|--------|-------|-----------|---------------|
| QA-001 | إعداد test infrastructure (Jest + Playwright + MSW) | QA | INIT-001 | Test runner يشتغل + mocks جاهزة |
| QA-002 | E2E: المسار الكامل (تسجيل → استثمار → دفع) | QA | المراحل 1-4 | السيناريو الكامل يمر |
| QA-003 | Load test: 1000 concurrent users | QA | OPS-002 | Response < 300ms under load |
| QA-004 | Security audit: OWASP Top 10 scan | Security | كل المراحل | لا ثغرات Critical أو High |
| QA-005 | Accessibility audit (WCAG 2.1 AA) | QA | المرحلة 8 | لا أخطاء a11y حرجة |
| QA-006 | Cross-browser testing (Chrome, Safari, Firefox) | QA | المرحلة 8 | لا أخطاء visual أو وظيفية |

---

## 📊 ملخص الأرقام

| البند | العدد |
|-------|-------|
| **إجمالي المهام** | ~105 مهمة |
| **مهام Backend** | ~40 |
| **مهام Frontend** | ~35 |
| **مهام Database** | ~15 |
| **مهام QA/Security** | ~15 |
| **مهام DevOps** | ~15 |
| **المراحل** | 10 |

---

## 🔄 ترتيب التنفيذ والتبعيات

```text
المرحلة 0 (التأسيس)
    │
    ├── المرحلة 8 (UI مشتركة) ──── يبدأ بالتوازي
    │
    ▼
المرحلة 1 (المصادقة)
    │
    ▼
المرحلة 1.5 (الملفات الشخصية)
    │
    ▼
المرحلة 2 (الاجتماعي)
    │
    ▼
المرحلة 3 (الاستثمار) ◄── الأهم مالياً
    │
    ├── المرحلة 3.5 (الملكية)
    │
    ▼
المرحلة 4 (المدفوعات)
    │
    ├── المرحلة 5 (السوق)
    ├── المرحلة 5.5 (الملكية الفكرية)
    ├── المرحلة 6 (المسرعات)
    │
    ▼
المرحلة 7 (إشعارات + بحث + إدارة)
    │
    ▼
المرحلة 9 (البنية التحتية) ──── يبدأ مبكراً بالتوازي
    │
    ▼
المرحلة 10 (اختبارات شاملة)
```

---

## ✅ معايير القبول العامة

### لكل مهمة:
- [ ] TypeScript strict mode بدون `any`
- [ ] الحسابات المالية بـ `Decimal.js` فقط (لا `float`)
- [ ] Input validation على كل API endpoint
- [ ] Error handling مع رسائل واضحة
- [ ] اختبارات (unit على الأقل)
- [ ] RTL support لكل مكوّن UI

### لكل مرحلة:
- [ ] Code review بواسطة Security Agent
- [ ] E2E test للمسار الأساسي
- [ ] لا أخطاء TypeScript
- [ ] الأداء ضمن SLA (< 300ms P95)

---

## 🎯 كيف تشتغل مع كل Agent

### 1. Backend Agent
```
"نفذ المهمة AUTH-004: أنشئ API endpoint POST /api/auth/register
- الجدول: auth.users (موجود من AUTH-001)
- Validation: email, password (8+ chars), role
- Password: Argon2id hashing
- Response: userId + 201 status
- Error: 409 لو email موجود"
```

### 2. Frontend Agent
```
"نفذ المهمة AUTH-013: أنشئ صفحة التسجيل
- المسار: src/app/(auth)/register/page.tsx
- الحقول: الاسم، الإيميل، كلمة المرور، نوع الحساب
- Validation: Zod schema
- API: POST /api/auth/register
- After success: redirect to verify OTP"
```

### 3. QA Agent
```
"نفذ المهمة AUTH-021: راجع أمنياً كل كود المصادقة
- OWASP Auth checklist
- SQL injection في كل query
- XSS في كل input
- CSRF protection
- Timing attacks في password comparison"
```

### 4. Database Agent
```
"نفذ المهمة INV-001: أنشئ جداول الاستثمار
- Schema: investment
- Tables: rounds, contributions
- كل المبالغ: NUMERIC(18,4)
- UUID primary keys
- Indexes على status + created_at"
```
