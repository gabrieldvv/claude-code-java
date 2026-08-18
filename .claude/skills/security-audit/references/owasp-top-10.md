# OWASP Top 10 & Security Checklist

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

---

## Security Checklist

### Code Review

- [ ] Input validated with allowlist patterns
- [ ] SQL queries use parameters (no concatenation)
- [ ] Output encoded for context (HTML, JS, URL)
- [ ] Authorization checked at service layer
- [ ] No hardcoded secrets
- [ ] Passwords hashed with BCrypt/Argon2
- [ ] Sensitive data not logged
- [ ] CSRF protection enabled (for browser apps)

### Configuration

- [ ] HTTPS enforced
- [ ] Security headers configured
- [ ] Debug/dev features disabled in production
- [ ] Default credentials changed
- [ ] Error messages don't leak internal details

### Dependencies

- [ ] No known vulnerabilities (OWASP check)
- [ ] Dependencies up to date
- [ ] Unnecessary dependencies removed
