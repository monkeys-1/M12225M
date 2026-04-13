---
description: "Generate a new PostgreSQL migration with schema-per-service isolation, proper types, and rollback support"
---

# New Database Migration

Create a new PostgreSQL migration for the Kun Sharik platform.

## Requirements
- **Schema**: ${input:Schema name (auth/social/investment/ownership/marketplace/payment/ip/admin)}
- **Table**: ${input:Table name (snake_case, plural)}
- **Operation**: ${input:create-table/add-column/add-index/modify-column}

## Migration Rules

### File Naming
`YYYYMMDD_HHMMSS_{description}.ts`

### Schema Isolation
Every table belongs to its schema:
```sql
CREATE TABLE {schema}.{table_name} (...)
```

### Required Columns (for new tables)
```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
deleted_at TIMESTAMPTZ  -- soft delete
```

### Financial Columns
- Use `NUMERIC(18,4)` — NEVER `FLOAT` or `DOUBLE`
- Always pair with `currency CHAR(3) NOT NULL DEFAULT 'SAR'`
- Add `CHECK (amount >= 0)` where applicable

### Index Rules
- Every foreign key gets an index
- Name format: `idx_{table}_{columns}`
- Use partial indexes for status filters
- GIN indexes for JSONB and full-text search

### Migration Structure
```typescript
export async function up(queryRunner: QueryRunner): Promise<void> {
  // Forward migration
}

export async function down(queryRunner: QueryRunner): Promise<void> {
  // Rollback — must fully reverse `up`
}
```

## Checklist
- [ ] Correct schema prefix
- [ ] NUMERIC(18,4) for money (not FLOAT)
- [ ] Currency column alongside amount
- [ ] Indexes on foreign keys
- [ ] Reversible down migration
- [ ] created_at / updated_at columns
- [ ] Soft delete support (deleted_at)
