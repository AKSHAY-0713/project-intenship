# Prioritized Hardening Checklist

Use the status column during implementation. Do not mark a control complete until it has been implemented and evidence has been reviewed.

| Priority | Control | Verification / evidence | Status |
|---|---|---|---|
| P0 - Critical | Enforce HTTPS for all traffic | HTTP redirects to HTTPS; TLS configuration reviewed | Not started |
| P0 - Critical | Hash passwords with Argon2id (or bcrypt); unique salts handled by library | Code/config review; tests confirm no plaintext password storage | Not started |
| P0 - Critical | Enforce server-side authentication and object-level authorization | Tests prove unauthenticated and cross-user access are denied | Not started |
| P0 - Critical | Use parameterized queries / safe ORM APIs | Static/code review; injection tests | Not started |
| P1 - High | Secure session cookies: Secure, HttpOnly, SameSite | Inspect Set-Cookie headers in test environment | Not started |
| P1 - High | Rotate session ID after login and privilege changes | Automated session-fixation test | Not started |
| P1 - High | Idle and absolute session expiry; server-side logout/revocation | Expiry and logout tests | Not started |
| P1 - High | Rate-limit login and password-reset endpoints | Repeated-request test; confirm safe 429 behavior | Not started |
| P1 - High | Generic login and reset responses; prevent account enumeration | Compare responses for existing/non-existing accounts | Not started |
| P1 - High | CSRF protection for cookie-authenticated state changes | CSRF test on profile update | Not started |
| P2 - Medium | Validate input and encode output by context | Validation and XSS tests | Not started |
| P2 - Medium | Store secrets in environment/secret manager, never Git | Repository secret scan and deployment review | Not started |
| P2 - Medium | Least-privilege database and service accounts | Permission review | Not started |
| P2 - Medium | Security audit events; redact credentials and tokens | Log review and alert test | Not started |
| P2 - Medium | Patch dependencies and scan for known vulnerabilities | Dependency scan report | Not started |
| P3 - Low | Add security headers (CSP, frame restrictions, nosniff, referrer policy) | Header scan; CSP tested in report-only first | Not started |
| P3 - Low | Backup and restore procedure; monitor availability | Restore exercise and alert evidence | Not started |

## Suggested implementation order
1. Close P0 items before production exposure.
2. Complete P1 session, rate-limit, enumeration, and CSRF controls.
3. Complete P2 operational controls and evidence.
4. Tune P3 defense-in-depth controls and monitor continuously.
