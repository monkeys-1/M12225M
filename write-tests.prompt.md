---
description: "Generate comprehensive tests for an existing module or file with Jest, Testing Library, and proper coverage"
---

# Write Tests

Generate tests for an existing file or module in the Kun Sharik platform.

## Target
- **File/Module**: ${input:Path to file or module to test}
- **Test Type**: ${input:unit/integration/e2e}

## Testing Rules

### Unit Tests (Jest)
```typescript
describe('ModuleName', () => {
  describe('functionName', () => {
    it('should handle the happy path', () => {});
    it('should reject invalid input', () => {});
    it('should handle edge case', () => {});
  });
});
```

### What to Test
1. **Happy path** — normal expected behavior
2. **Validation errors** — invalid inputs, missing fields
3. **Auth failures** — unauthorized access attempts
4. **Edge cases** — empty arrays, zero amounts, max values
5. **Financial precision** — Decimal.js calculations, rounding
6. **Concurrent operations** — race conditions for financial ops
7. **RTL rendering** — Arabic/English layout switching (for components)

### Mocking Strategy
- Mock at service boundaries (HTTP clients, database, external APIs)
- Use factory functions: `createTestUser()`, `createTestInvestmentRound()`
- Inject dependencies — don't mock internal implementation

### Financial Tests
- Verify calculations use Decimal.js, not float
- Test rounding: `Decimal.ROUND_HALF_UP`
- Test currency: SAR and USD
- Verify audit trail creation

### Coverage Target
- Minimum **80%** line coverage
- **100%** for financial calculation functions
- **100%** for auth/security functions

## Output
Generate the test file at the conventional location:
- Unit: `src/modules/{module}/__tests__/{filename}.test.ts`
- Integration: `tests/integration/{module}.test.ts`
- E2E: `tests/e2e/{flow}.spec.ts`
