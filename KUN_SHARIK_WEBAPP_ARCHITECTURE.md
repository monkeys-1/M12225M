# 🚀 التقسيم النهائي لتطبيق "كن شريك" - WebApp Architecture

## 🧠 الهيكل العام للتطبيق

```text
┌─────────────────────────────────────────────────────────────────┐
│                    "كن شريك" - المنصة المتكاملة                 │
└─────────────────────────────────────────────────────────────────┘
                               │
       ┌───────────────────────┼───────────────────────┐
       │                       │                       │
┌──────┴───────┐         ┌─────┴─────┐           ┌─────┴─────┐
│   الواجهة    │         │   الخدمات  │           │   البيانات │
│   الأمامية   │         │   الأساسية │           │   والتخزين │
└──────┬───────┘         └─────┬─────┘           └─────┬─────┘
       │                       │                       │
┌──────┴───────┐         ┌─────┴─────┐           ┌─────┴─────┐
│   التجربة    │         │   الذكاء   │           │   الإدارة  │
│   البينية    │         │ الاصطناعي │           │   والتشغيل │
└──────────────┘         └───────────┘           └───────────┘
```

---

## 🏗️ هيكل الملفات والمجلدات

```text
kun-sharik-webapp/
├── src/
│   ├── app/                    # Next.js 15 App Router
│   ├── components/             # مكونات عامة قابلة لإعادة الاستخدام
│   ├── modules/                # وحدات العمل الأساسية
│   ├── lib/                    # أدوات و utilities
│   ├── hooks/                  # React hooks مخصصة
│   ├── types/                  # TypeScript definitions
│   ├── utils/                  # utilities functions
│   ├── styles/                 # global styles
│   ├── providers/              # React context providers
│   ├── middleware.ts            # Next.js middleware
│   ├── i18n/                   # الترجمة و RTL
│   └── constants/              # ثوابت التطبيق
├── public/                     # static assets
├── packages/                   # shared packages
├── tests/                      # اختبارات e2e و integration
├── deployments/                # deployment configurations
├── next.config.ts              # إعدادات Next.js
├── tailwind.config.ts          # إعدادات Tailwind CSS
├── tsconfig.json               # إعدادات TypeScript
├── .eslintrc.json              # إعدادات ESLint
├── .prettierrc                 # إعدادات التنسيق
└── package.json                # التبعيات والسكريبتات
```

---

## 🗂️ هيكل App Router (Next.js 15)

```text
src/app/
├── layout.tsx                  # Root Layout (RTL + Providers)
├── page.tsx                    # الصفحة الرئيسية / Landing
├── loading.tsx                 # مؤشر التحميل العام
├── error.tsx                   # صفحة الأخطاء العامة
├── not-found.tsx               # صفحة 404
├── globals.css                 # الأنماط العامة
│
├── (auth)/                     # مجموعة المصادقة (بدون layout مشترك)
│   ├── login/page.tsx
│   ├── register/page.tsx
│   ├── verify/page.tsx
│   └── forgot-password/page.tsx
│
├── (main)/                     # مجموعة التطبيق الرئيسي
│   ├── layout.tsx              # Layout مع Sidebar + Header
│   ├── dashboard/page.tsx
│   ├── feed/page.tsx
│   ├── messages/
│   │   ├── page.tsx
│   │   └── [conversationId]/page.tsx
│   ├── profile/
│   │   ├── page.tsx            # ملفي الشخصي
│   │   ├── edit/page.tsx
│   │   └── [userId]/page.tsx   # ملف مستخدم آخر
│   ├── network/page.tsx
│   └── groups/
│       ├── page.tsx
│       └── [groupId]/page.tsx
│
├── (investment)/               # مجموعة الاستثمار
│   ├── layout.tsx
│   ├── rounds/
│   │   ├── page.tsx
│   │   ├── create/page.tsx
│   │   └── [roundId]/page.tsx
│   ├── crowdfunding/
│   │   ├── page.tsx
│   │   └── [campaignId]/page.tsx
│   ├── funds/
│   │   ├── page.tsx
│   │   └── [fundId]/page.tsx
│   └── portfolio/page.tsx
│
├── (ownership)/                # مجموعة الملكية
│   ├── layout.tsx
│   ├── cap-table/
│   │   ├── page.tsx
│   │   └── [companyId]/page.tsx
│   ├── spv/
│   │   ├── page.tsx
│   │   └── [spvId]/page.tsx
│   └── distributions/page.tsx
│
├── (marketplace)/              # مجموعة السوق
│   ├── layout.tsx
│   ├── chambers/
│   │   ├── page.tsx
│   │   └── [chamberId]/page.tsx
│   ├── deals/
│   │   ├── page.tsx
│   │   └── [dealId]/page.tsx
│   └── data-rooms/
│       ├── page.tsx
│       └── [roomId]/page.tsx
│
├── (ip)/                       # مجموعة الملكية الفكرية
│   ├── layout.tsx
│   ├── registry/page.tsx
│   ├── patents/page.tsx
│   └── licensing/page.tsx
│
├── (accelerator)/              # مجموعة المسرعات
│   ├── layout.tsx
│   ├── programs/
│   │   ├── page.tsx
│   │   └── [programId]/page.tsx
│   ├── incubator/page.tsx
│   └── studio/page.tsx
│
├── admin/                      # لوحة الإدارة
│   ├── layout.tsx
│   ├── page.tsx
│   ├── users/page.tsx
│   ├── reports/page.tsx
│   ├── settings/page.tsx
│   └── moderation/page.tsx
│
└── api/                        # API Routes
    ├── auth/
    │   ├── login/route.ts
    │   ├── register/route.ts
    │   └── verify/route.ts
    ├── webhooks/
    │   ├── payment/route.ts
    │   └── notifications/route.ts
    ├── upload/route.ts
    └── search/route.ts
```

---

## 🌐 الترجمة ودعم RTL (i18n)

```text
src/i18n/
├── config.ts                   # إعدادات اللغات المدعومة
├── middleware.ts               # كشف اللغة تلقائياً
├── locales/
│   ├── ar/                     # العربية (اللغة الافتراضية)
│   │   ├── common.json
│   │   ├── auth.json
│   │   ├── investment.json
│   │   ├── dashboard.json
│   │   └── errors.json
│   └── en/                     # الإنجليزية
│       ├── common.json
│       ├── auth.json
│       ├── investment.json
│       ├── dashboard.json
│       └── errors.json
├── hooks/
│   ├── useTranslation.ts       # hook الترجمة
│   └── useDirection.ts         # hook اتجاه النص (RTL/LTR)
└── utils/
    ├── number-formatter.ts     # تنسيق الأرقام عربي/إنجليزي
    ├── currency-localizer.ts   # ريال سعودي + عملات أخرى
    └── date-localizer.ts       # هجري + ميلادي
```

---

## 🔒 Middleware (Next.js)

```text
src/middleware.ts
```

```typescript
// المسؤوليات:
// 1. التحقق من الجلسة (Auth Guard)
// 2. كشف اللغة وتوجيه RTL/LTR
// 3. حماية المسارات حسب الدور (investor, entrepreneur, admin)
// 4. Rate Limiting أساسي
// 5. إعادة التوجيه للصفحات المحمية
```

---

## 🎁 Providers (React Context)

```text
src/providers/
├── AppProviders.tsx            # تجميع كل الـ providers
├── AuthProvider.tsx            # سياق المصادقة والجلسة
├── ThemeProvider.tsx           # سياق التصميم (فاتح/داكن + RTL)
├── I18nProvider.tsx            # سياق الترجمة
├── NotificationProvider.tsx    # سياق الإشعارات الفورية
├── WebSocketProvider.tsx       # سياق الاتصال المباشر
└── StoreProvider.tsx           # سياق Redux/RTK Query
```

---

## 📦 الوحدات الأساسية (Core Modules)

### 1) وحدة المصادقة والهوية

```text
src/modules/auth/
├── components/
│   ├── LoginForm.tsx
│   ├── RegisterForm.tsx
│   ├── VerificationModal.tsx
│   └── AuthGuard.tsx
├── hooks/
│   ├── useAuth.ts
│   ├── usePermissions.ts
│   └── useSession.ts
├── services/
│   ├── auth-service.ts
│   ├── verification-service.ts
│   └── session-service.ts
└── types/
    ├── auth.types.ts
    └── user.types.ts
```

### 2) وحدة الملفات الشخصية والشبكات

```text
src/modules/profile/
├── components/
│   ├── ProfileEditor.tsx
│   ├── ProfileView.tsx
│   ├── ConnectionsList.tsx
│   └── NetworkGraph.tsx
├── hooks/
│   ├── useProfile.ts
│   ├── useConnections.ts
│   └── useNetwork.ts
├── services/
│   ├── profile-service.ts
│   ├── connection-service.ts
│   └── recommendation-service.ts
└── types/
    └── profile.types.ts
```

### 3) وحدة التواصل الاجتماعي

```text
src/modules/social/
├── components/
│   ├── Feed/
│   │   ├── PostCard.tsx
│   │   ├── CommentSection.tsx
│   │   └── ReactionBar.tsx
│   ├── Messaging/
│   │   ├── ChatInterface.tsx
│   │   ├── MessageList.tsx
│   │   └── ConversationList.tsx
│   └── Groups/
│       ├── GroupCard.tsx
│       ├── GroupAdmin.tsx
│       └── MembershipManager.tsx
├── hooks/
│   ├── useFeed.ts
│   ├── useMessaging.ts
│   └── useGroups.ts
├── services/
│   ├── feed-service.ts
│   ├── messaging-service.ts
│   └── group-service.ts
└── types/
    └── social.types.ts
```

### 4) وحدة الاستثمار والتمويل

```text
src/modules/investment/
├── components/
│   ├── InvestmentRounds/
│   │   ├── RoundCreator.tsx
│   │   ├── RoundDashboard.tsx
│   │   └── TermSheetGenerator.tsx
│   ├── Crowdfunding/
│   │   ├── CampaignList.tsx
│   │   ├── CampaignCreator.tsx
│   │   └── ContributionManager.tsx
│   └── Funds/
│       ├── FundDashboard.tsx
│       ├── LPManager.tsx
│       └── PerformanceAnalytics.tsx
├── hooks/
│   ├── useInvestmentRounds.ts
│   ├── useCrowdfunding.ts
│   └── useFunds.ts
├── services/
│   ├── round-service.ts
│   ├── crowdfunding-service.ts
│   └── fund-service.ts
└── types/
    └── investment.types.ts
```

### 5) وحدة إدارة الملكية والجداول

```text
src/modules/ownership/
├── components/
│   ├── CapTable/
│   │   ├── TableEditor.tsx
│   │   ├── EquityCalculator.tsx
│   │   └── VestingManager.tsx
│   ├── SPV/
│   │   ├── SPVCreator.tsx
│   │   ├── SPVAdmin.tsx
│   │   └── GovernanceManager.tsx
│   └── Distribution/
│       ├── DividendCalculator.tsx
│       ├── PayoutManager.tsx
│       └── TaxCalculator.tsx
├── hooks/
│   ├── useCapTable.ts
│   ├── useSPV.ts
│   └── useDistributions.ts
├── services/
│   ├── captable-service.ts
│   ├── spv-service.ts
│   └── distribution-service.ts
└── types/
    └── ownership.types.ts
```

### 6) وحدة غرف الصناعات والصفقات

```text
src/modules/marketplace/
├── components/
│   ├── Chambers/
│   │   ├── ChamberList.tsx
│   │   ├── ChamberAdmin.tsx
│   │   └── IndustryForum.tsx
│   ├── Deals/
│   │   ├── DealMarketplace.tsx
│   │   ├── DealCreator.tsx
│   │   └── NegotiationRoom.tsx
│   └── DataRooms/
│       ├── DataRoomManager.tsx
│       ├── DocumentViewer.tsx
│       └── AccessController.tsx
├── hooks/
│   ├── useChambers.ts
│   ├── useDeals.ts
│   └── useDataRooms.ts
├── services/
│   ├── chamber-service.ts
│   ├── deal-service.ts
│   └── dataroom-service.ts
└── types/
    └── marketplace.types.ts
```

### 7) وحدة الملكية الفكرية

```text
src/modules/ip/
├── components/
│   ├── IPRegistry/
│   │   ├── IPDashboard.tsx
│   │   ├── PatentManager.tsx
│   │   └── TrademarkRegistry.tsx
│   ├── SAIPIntegration/
│   │   ├── SAIPConnector.tsx
│   │   ├── SubmissionManager.tsx
│   │   └── StatusTracker.tsx
│   └── Licensing/
│       ├── LicenseManager.tsx
│       ├── RoyaltyCalculator.tsx
│       └── AgreementGenerator.tsx
├── hooks/
│   ├── useIPManagement.ts
│   ├── useSAIPIntegration.ts
│   └── useLicensing.ts
├── services/
│   ├── ip-service.ts
│   ├── saip-service.ts
│   └── licensing-service.ts
└── types/
    └── ip.types.ts
```

### 8) وحدة المسرعات والاستوديوهات

```text
src/modules/accelerator/
├── components/
│   ├── Accelerator/
│   │   ├── ProgramManager.tsx
│   │   ├── CohortDashboard.tsx
│   │   └── MentorNetwork.tsx
│   ├── Incubator/
│   │   ├── IncubatorManager.tsx
│   │   ├── ResourceAllocator.tsx
│   │   └── ProgressTracker.tsx
│   └── TechStudio/
│       ├── StudioManager.tsx
│       ├── ProjectDashboard.tsx
│       └── DevelopmentTracker.tsx
├── hooks/
│   ├── useAccelerator.ts
│   ├── useIncubator.ts
│   └── useTechStudio.ts
├── services/
│   ├── accelerator-service.ts
│   ├── incubator-service.ts
│   └── techstudio-service.ts
└── types/
    └── accelerator.types.ts
```

### 9) وحدة الإشعارات

```text
src/modules/notifications/
├── components/
│   ├── NotificationCenter.tsx
│   ├── NotificationItem.tsx
│   ├── NotificationPreferences.tsx
│   └── PushPermissionPrompt.tsx
├── hooks/
│   ├── useNotifications.ts
│   └── usePushSubscription.ts
├── services/
│   ├── notification-service.ts
│   └── push-service.ts
└── types/
    └── notification.types.ts
```

### 10) وحدة البحث المتقدم

```text
src/modules/search/
├── components/
│   ├── GlobalSearch.tsx
│   ├── SearchResults.tsx
│   ├── FilterPanel.tsx
│   └── SavedSearches.tsx
├── hooks/
│   ├── useSearch.ts
│   └── useSearchHistory.ts
├── services/
│   └── search-service.ts
└── types/
    └── search.types.ts
```

### 11) وحدة لوحة الإدارة

```text
src/modules/admin/
├── components/
│   ├── AdminDashboard.tsx
│   ├── UserManagement.tsx
│   ├── ContentModeration.tsx
│   ├── SystemSettings.tsx
│   ├── ReportsGenerator.tsx
│   └── AuditLog.tsx
├── hooks/
│   ├── useAdmin.ts
│   └── useModeration.ts
├── services/
│   ├── admin-service.ts
│   └── moderation-service.ts
└── types/
    └── admin.types.ts
```

---

## 🎨 مكونات الواجهة العامة

### Layout Components

```text
src/components/layout/
├── MainLayout.tsx
├── DashboardLayout.tsx
├── AuthLayout.tsx
├── Sidebar/
│   ├── MainSidebar.tsx
│   ├── AdminSidebar.tsx
│   └── InvestorSidebar.tsx
├── Header/
│   ├── MainHeader.tsx
│   ├── NotificationBell.tsx
│   └── UserMenu.tsx
└── Footer/
    ├── MainFooter.tsx
    └── LegalFooter.tsx
```

### Shared Components

```text
src/components/shared/
├── UI/
│   ├── buttons/
│   │   ├── PrimaryButton.tsx
│   │   ├── SecondaryButton.tsx
│   │   └── IconButton.tsx
│   ├── forms/
│   │   ├── InputField.tsx
│   │   ├── SelectField.tsx
│   │   └── DatePicker.tsx
│   ├── modals/
│   │   ├── BaseModal.tsx
│   │   ├── ConfirmationModal.tsx
│   │   └── Drawer.tsx
│   └── data-display/
│       ├── DataTable.tsx
│       ├── Card.tsx
│       └── Badge.tsx
├── Navigation/
│   ├── Breadcrumbs.tsx
│   ├── Tabs.tsx
│   └── Pagination.tsx
└── Feedback/
    ├── LoadingSpinner.tsx
    ├── Toast.tsx
    └── ErrorBoundary.tsx
```

---

## 🔧 الأدوات والـ Utilities

### Custom Hooks

```text
src/hooks/
├── useApi.ts                # إدارة API calls
├── useForm.ts               # إدارة النماذج
├── usePagination.ts         # إدارة التقسيم
├── useSearch.ts             # إدارة البحث
├── useFilters.ts            # إدارة الفلاتر
├── useUpload.ts             # إدارة الرفع
├── useWebSocket.ts          # إدارة WebSocket
├── useRealTime.ts           # تحديثات فورية
├── useDebounce.ts           # تأخير البحث/الإدخال
└── useInfiniteScroll.ts     # التمرير اللانهائي
```

### Utility Functions

```text
src/utils/
├── api/
│   ├── api-client.ts
│   ├── error-handler.ts
│   └── request-interceptor.ts
├── form/
│   ├── validation.ts
│   ├── formatter.ts
│   └── schema-generator.ts
├── date/
│   ├── date-formatter.ts
│   ├── date-calculator.ts
│   └── calendar-utils.ts
├── finance/
│   ├── currency-formatter.ts
│   ├── investment-calculator.ts
│   └── tax-calculator.ts
├── file/
│   ├── file-validator.ts
│   ├── file-converter.ts
│   └── document-generator.ts
└── security/
    ├── encryption.ts
    └── permission-checker.ts
```

> **ملاحظة**: تم نقل `audit-logger` إلى `src/lib/monitoring/logging/` لتجنب التكرار.

### Global Types

```text
src/types/
├── global.d.ts              # تعريفات عامة
├── api.types.ts             # أنواع الـ API المشتركة
├── user.types.ts            # أنواع المستخدم الأساسية
├── common.types.ts          # أنواع مشتركة (Pagination, Response, etc.)
├── enums.ts                 # جميع الـ enums (UserRole, Status, etc.)
└── environment.d.ts         # أنواع متغيرات البيئة
```

### Constants

```text
src/constants/
├── routes.ts                # جميع مسارات التطبيق
├── api-endpoints.ts         # نقاط نهاية الـ API
├── roles.ts                 # أدوار المستخدمين وصلاحياتهم
├── config.ts                # إعدادات ثابتة
└── regex-patterns.ts        # أنماط التحقق (سجل تجاري، جوال سعودي، etc.)
```

---

## 🗄️ إدارة الحالة (State Management)

### Global State

```text
src/store/
├── slices/
│   ├── authSlice.ts
│   ├── userSlice.ts
│   ├── notificationSlice.ts
│   ├── uiSlice.ts
│   └── configSlice.ts
├── hooks.ts
└── index.ts
```

### API State Management

```text
src/lib/rtk-query/
├── api/
│   ├── authApi.ts
│   ├── userApi.ts
│   ├── investmentApi.ts
│   └── socialApi.ts
├── hooks/
│   ├── useAuthQuery.ts
│   ├── useUserMutation.ts
│   └── useInvestmentQuery.ts
└── types/
    └── api.types.ts
```

---

## 🎨 التصميم والأنماط

### Theme System

```text
src/styles/
├── theme/
│   ├── colors.ts
│   ├── typography.ts
│   ├── spacing.ts
│   ├── breakpoints.ts
│   └── components.ts
├── globals.css
├── utilities.css
└── animations.css
```

### Design Tokens

```text
src/styles/tokens/
├── color-tokens.ts
├── size-tokens.ts
├── font-tokens.ts
├── shadow-tokens.ts
└── animation-tokens.ts
```

---

## 📱 الاستجابة والتكيف

### Responsive Design

```text
src/hooks/use-responsive/
├── useBreakpoint.ts
├── useOrientation.ts
├── useDeviceType.ts
└── useTouchSupport.ts
```

### Adaptive Components

```text
src/components/adaptive/
├── ResponsiveContainer.tsx
├── MobileFirst.tsx
├── ConditionalRender.tsx
└── LazyLoader.tsx
```

---

## 🚀 النشر والتوزيع

### Deployment Configuration

```text
deployments/
├── vercel/
│   ├── vercel.json
│   ├── build-config.js
│   └── route-config.js
├── docker/
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── nginx.conf
├── scripts/
│   ├── build.sh
│   ├── deploy.sh
│   └── migrate.sh
└── environments/
    ├── .env.development
    ├── .env.staging
    └── .env.production
```

### CI/CD Configuration

```text
.github/
├── workflows/
│   ├── ci.yml
│   ├── cd.yml
│   ├── tests.yml
│   └── security.yml
└── actions/
    ├── build-action/
    ├── test-action/
    └── deploy-action/
```

---

## 📊 المراقبة والتحليل

```text
src/lib/monitoring/
├── error-tracking/
│   ├── sentry-setup.ts
│   ├── error-boundary.tsx
│   └── error-handler.ts
├── performance/
│   ├── performance-monitor.ts
│   ├── vitals-tracker.ts
│   └── speed-insights.ts
├── analytics/
│   ├── google-analytics.ts
│   ├── event-tracker.ts
│   └── user-behavior.ts
└── logging/
    ├── logger.ts
    ├── audit-logger.ts
    └── activity-logger.ts
```

---

## 📦 الحزم المشتركة (Shared Packages)

```text
packages/
├── ui/                      # مكتبة المكونات المشتركة (Design System)
│   ├── src/
│   ├── package.json
│   └── tsconfig.json
├── utils/                   # دوال مساعدة مشتركة
│   ├── src/
│   ├── package.json
│   └── tsconfig.json
├── types/                   # أنواع TypeScript مشتركة
│   ├── src/
│   ├── package.json
│   └── tsconfig.json
└── config/                  # إعدادات مشتركة (ESLint, TS, Tailwind)
    ├── eslint/
    ├── typescript/
    └── tailwind/
```

---

## 🧪 هيكل الاختبارات

```text
tests/
├── e2e/                     # اختبارات End-to-End (Cypress/Playwright)
│   ├── auth.spec.ts
│   ├── investment-flow.spec.ts
│   ├── profile.spec.ts
│   └── fixtures/
├── integration/             # اختبارات التكامل
│   ├── api/
│   └── services/
└── setup/
    ├── test-utils.tsx       # أدوات الاختبار المشتركة
    ├── mocks/               # بيانات وهمية
    │   ├── handlers.ts
    │   └── server.ts
    └── jest.setup.ts
```

> كل وحدة (module) تحتوي على مجلد `__tests__/` داخلي لاختبارات الوحدة:

```text
src/modules/auth/__tests__/
├── LoginForm.test.tsx
├── useAuth.test.ts
└── auth-service.test.ts
```

---

## 📐 ملخص الأرقام

| **البند** | **العدد** |
|-----------|----------|
| الوحدات الأساسية (Modules) | 11 |
| مكونات Layout | 10 |
| مكونات مشتركة (Shared) | 15 |
| Custom Hooks | 10+ |
| Utility Categories | 6 |
| صفحات App Router | 30+ |
| API Routes | 6+ |

---

## ✅ الخلاصة

هذا الهيكل يضمن تطبيقاً قابلاً للصيانة والتوسع، مع:
- **فصل واضح للاهتمامات** بين 11 وحدة مستقلة
- **دعم كامل للعربية** مع RTL وتقويم هجري وتنسيق عملات
- **App Router مفصّل** مع Route Groups لكل مجموعة وظيفية
- **هيكل اختبارات شامل** (Unit + Integration + E2E)
- **Middleware مركزي** للمصادقة والصلاحيات واللغة
- **Providers منظمة** لإدارة السياق عبر التطبيق
- **حزم مشتركة** قابلة لإعادة الاستخدام عبر مشاريع متعددة
