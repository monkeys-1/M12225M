---
description: "Run a security review on a file or module checking OWASP Top 10, auth, input validation, SQL injection, and financial security"
---

# Security Review

Perform a security audit on the specified code.

## Target
- **File/Module**: ${input:Path to file or module to review}

## Review Checklist

### OWASP Top 10
1. **Injection** — SQL, NoSQL, command injection
2. **Broken Auth** — session management, credential handling
3. **Sensitive Data** — encryption, data exposure
4. **XXE** — XML parsing vulnerabilities
5. **Broken Access Control** — authorization bypass
6. **Misconfiguration** — default credentials, verbose errors
7. **XSS** — input sanitization, output encoding
8. **Insecure Deserialization** — object manipulation
9. **Vulnerable Components** — outdated dependencies
10. **Insufficient Logging** — audit trail gaps

### Kun Sharik Specific
- [ ] No `any` type in TypeScript
- [ ] No string concatenation in SQL (parameterized only)
- [ ] Zod validation on all inputs
- [ ] No hardcoded secrets or API keys
- [ ] Auth guard on every endpoint
- [ ] No `float`/`number` for money (use Decimal.js)
- [ ] Rate limiting on auth endpoints
- [ ] No sensitive data in logs
- [ ] CSRF protection on mutations
- [ ] Audit trail for financial operations
- [ ] AES-256 for Data Room files
- [ ] Argon2id for password hashing

## Output Format
```markdown
## 🔴 CRITICAL (must fix before merge)
- [File:Line] Issue description → Fix recommendation

## 🟡 WARNING (should fix)
- [File:Line] Issue description → Recommendation

## 🟢 PASSED
- List of checks that passed

## 📊 Summary
- Total issues: X (Y critical, Z warnings)
- Risk level: HIGH/MEDIUM/LOW
```
