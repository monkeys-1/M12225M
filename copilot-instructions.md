# Kun Sharik - Project Guidelines

## Project Overview
"كن شريك" (Kun Sharik) is an investment partnership platform built with Next.js 15 + TypeScript. It connects entrepreneurs and investors through smart, secure partnerships with Arabic-first UX.

## Architecture
- **Frontend**: Next.js 15 App Router, React 19, TypeScript 5.4, Tailwind CSS 4, Zustand, TanStack Query v5
- **Backend**: NestJS 10, PostgreSQL 16 (schema-per-service), Redis 7, Apache Kafka, Elasticsearch 8
- **Deployment**: ECS Fargate (Phase 1) → EKS (Phase 2), AWS Middle East (Bahrain)

## Code Style
- TypeScript strict mode — never use `any`
- All financial calculations use `Decimal.js` — never use `float` for money
- Store monetary values as `NUMERIC(18,4)` in PostgreSQL
- Use `Money { amount: Decimal, currency: "SAR" | "USD", precision: 4 }` type
- Cursor-based pagination, not offset-based
- Structured error responses: `{ error: string, code: string, details?: unknown }`

## Conventions
- **File naming**: kebab-case for files, PascalCase for components
- **Module structure**: each module has `components/`, `hooks/`, `services/`, `types/`, `__tests__/`
- **API routes**: Next.js Route Handlers in `src/app/api/`
- **State**: Zustand for client state, TanStack Query for server state
- **Forms**: Zod schemas for validation
- **i18n**: Arabic is the default language, RTL-first design
- **Dates**: Support both Hijri and Gregorian calendars
- **Currency**: Format with SAR symbol and Arabic numerals when in Arabic mode

## Security Rules
- Passwords: Argon2id hashing only
- Auth: JWT access tokens (15min) + refresh tokens (7 days) in httpOnly cookies
- Rate limiting on all auth endpoints (5 attempts / 15 minutes)
- Input validation on every API endpoint (Zod)
- Parameterized queries only — never concatenate SQL strings
- Data Room files: AES-256 encryption at rest
- Audit trail for all financial operations

## Database Schema Strategy
PostgreSQL uses schema-per-service isolation:
- `auth` — users, sessions, tokens
- `social` — profiles, posts, messages, groups
- `investment` — rounds, campaigns, funds
- `ownership` — cap tables, distributions, SPVs
- `marketplace` — chambers, deals, data rooms
- `payment` — transactions, wallets
- `ip` — patents, trademarks, licenses
- `admin` — moderation, audit logs

## Key Reference Files
- [AGENT_ORCHESTRATION.md](AGENT_ORCHESTRATION.md) — Micro-task definitions with IDs (AUTH-001, INV-001, etc.)
- [KUN_SHARIK_SYSTEM_ARCHITECTURE.md](KUN_SHARIK_SYSTEM_ARCHITECTURE.md) — System architecture decisions
- [KUN_SHARIK_WEBAPP_ARCHITECTURE.md](KUN_SHARIK_WEBAPP_ARCHITECTURE.md) — Frontend folder structure
- [KUN_SHARIK_PRD.md](KUN_SHARIK_PRD.md) — Product requirements

## Build and Test
```bash
npm run dev          # Development server
npm run build        # Production build
npm run lint         # ESLint + Prettier check
npm run type-check   # TypeScript compilation check
npm run test         # Jest unit tests
npm run test:e2e     # Playwright E2E tests
```
