---
description: "Generate a new NestJS API endpoint with controller, service, DTOs, Zod validation, auth guard, and tests"
---

# New API Endpoint

Create a new API endpoint for the Kun Sharik platform.

## Requirements
- **Resource**: ${input:Resource name (e.g., investment-rounds)}
- **HTTP Method**: ${input:HTTP method (GET/POST/PUT/PATCH/DELETE)}
- **Module**: ${input:Module name (e.g., investment)}
- **Auth Required**: ${input:Auth level (public/authenticated/admin)}

## Generate These Files

### 1. DTO (`src/modules/{module}/dto/{resource}.dto.ts`)
- Zod schema for input validation
- Infer TypeScript type from schema
- Include all required fields with proper types
- Use `Decimal` for any monetary fields

### 2. Controller (`src/modules/{module}/controllers/{resource}.controller.ts`)
- Apply appropriate auth guard
- Validate input with Zod DTO
- Cursor-based pagination for list endpoints
- Structured error responses: `{ error, code, details? }`

### 3. Service (`src/modules/{module}/services/{resource}.service.ts`)
- Business logic separated from controller
- Parameterized queries only
- Use `Decimal.js` for financial calculations
- Emit Kafka events for state changes

### 4. Test (`src/modules/{module}/__tests__/{resource}.test.ts`)
- Happy path test
- Validation error test
- Auth guard test
- Edge case tests

## Checklist
- [ ] Zod validation on all inputs
- [ ] Auth guard applied
- [ ] Cursor-based pagination (if list endpoint)
- [ ] Decimal.js for money (if financial)
- [ ] Parameterized SQL queries
- [ ] Audit trail for mutations
- [ ] Unit test with 80%+ coverage
