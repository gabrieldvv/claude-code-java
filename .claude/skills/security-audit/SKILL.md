---
name: security-audit
description: Java security checklist covering OWASP Top 10, input validation, injection prevention, and secure coding. Works with Spring, Quarkus, Jakarta EE, and plain Java. Use when reviewing code security, before releases, or when user asks about vulnerabilities. This is the code-level security checklist; do NOT use for architecture-level threat/coupling exposure (use architecture-review's security scope), dependency CVE/version audits (use maven-dependency-audit), or general code review (use java-code-review).
---

# Security Audit Skill

Security checklist for Java applications based on OWASP Top 10 and secure coding practices.

## When to Use
- Security code review
- Before production releases
- User asks about "security", "vulnerability", "OWASP"
- Reviewing authentication/authorization code
- Checking for injection vulnerabilities

---

## OWASP Top 10 Quick Reference

| # | Risk | Java Mitigation |
|---|------|-----------------|
| A01 | Broken Access Control | Role-based checks, deny by default |
| A02 | Cryptographic Failures | Use strong algorithms, no hardcoded secrets |
| A03 | Injection | Parameterized queries, input validation |
| A04 | Insecure Design | Threat modeling, secure defaults |
| A05 | Security Misconfiguration | Disable debug, secure headers |
| A06 | Vulnerable Components | Dependency scanning, updates |
| A07 | Authentication Failures | Strong passwords, MFA, session management |
| A08 | Data Integrity Failures | Verify signatures, secure deserialization |
| A09 | Logging Failures | Log security events, no sensitive data |
| A10 | SSRF | Validate URLs, allowlist domains |

Full OWASP Top 10 detail and the complete audit checklist live in [references/owasp-top-10.md](references/owasp-top-10.md).

---

## How to Use This Skill

Identify which security concern the code under review touches, then load only the relevant reference file(s) below. Do not load everything at once — pull in detail on demand. For a full pre-release audit, work through the checklist in `references/owasp-top-10.md` and load the topic references as each item comes up.

## References

Load only the relevant reference for the concern at hand:

- [references/input-validation.md](references/input-validation.md) — load this when reviewing input validation: Bean Validation (JSR 380), custom validators, allowlist vs blocklist.
- [references/injection.md](references/injection.md) — load this when checking for injection flaws: SQL injection prevention (JPA, native, JDBC), XSS prevention (output encoding, CSP), and CSRF protection.
- [references/authn-authz.md](references/authn-authz.md) — load this when reviewing authentication/authorization: password storage (BCrypt/Argon2), service-layer authorization checks, Spring Security annotations.
- [references/secrets-and-hardening.md](references/secrets-and-hardening.md) — load this when reviewing secrets management, secure deserialization, dependency security, security headers, or security event logging.
- [references/owasp-top-10.md](references/owasp-top-10.md) — load this for the full OWASP Top 10 reference table and the complete security checklist (code review, configuration, dependencies).

---

## Related Skills

- `java-code-review` - General code review
- `maven-dependency-audit` - Dependency vulnerability scanning
- `logging-patterns` - Secure logging practices
