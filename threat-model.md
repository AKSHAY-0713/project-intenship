# Threat Model

## 1. System overview
The system is a small web application that allows a visitor to create an account, authenticate, view and update their profile, and request a password reset.

### Assets to protect
| Asset | Why it matters |
|---|---|
| User credentials and password hashes | Account takeover and credential reuse risk |
| Session cookies / tokens | Possession can grant account access |
| Personal profile data | Privacy and identity misuse |
| Application data in the database | Confidentiality and integrity |
| Password-reset tokens | Can be used to take over accounts |
| Application secrets and signing keys | Can compromise sessions or integrations |
| Service availability | Users need reliable access |

### Actors
- **Unauthenticated visitor:** can register, log in, and request password reset.
- **Authenticated user:** can access and update their own profile.
- **Administrator/operator:** manages deployment, configuration, and user support.
- **Attacker:** may be an unauthenticated internet user, malicious account holder, or someone with a stolen session.
- **External dependency:** email delivery provider and hosting/platform services.

## 2. Data flows and trust boundaries
1. Browser sends HTTPS requests to the web application.
2. Application validates input and enforces authentication/authorization.
3. Application reads/writes user and session-related data in the database.
4. Password-reset workflow sends a time-limited link through the email provider.
5. Operators deploy code and manage secrets through a restricted administrative channel.

Trust boundaries:
- **TB1 — Internet to application:** all client input is untrusted.
- **TB2 — Application to database:** only the application service identity should connect; database must not be public.
- **TB3 — Application to email provider:** external API credentials and returned content must be protected and validated.
- **TB4 — Operator to production:** privileged access must use MFA, least privilege, and audit logging.

See `threat-model.mmd` for the diagram.

## 3. STRIDE threat analysis

| STRIDE category | Example threat | Security impact | Recommended controls |
|---|---|---|---|
| Spoofing | Attacker guesses, reuses, or steals credentials/session | Account takeover | Strong password hashing (Argon2id or bcrypt), MFA for privileged users, secure session cookies, session rotation after login |
| Tampering | User changes another user's profile ID in a request | Unauthorized data modification | Server-side object-level authorization on every request; parameterized queries; input validation |
| Repudiation | User denies changing an email/profile field | Disputes cannot be investigated | Timestamped audit events for sensitive actions; protect logs from alteration; avoid logging secrets |
| Information disclosure | SQL injection or verbose error reveals records or stack traces | Exposure of personal data and internals | Parameterized queries/ORM, generic client errors, restricted database permissions, encrypted transport |
| Denial of service | Login or password-reset endpoints are flooded | Service becomes unavailable; brute force | Per-account and per-IP rate limits, request size limits, timeouts, monitoring, upstream protection |
| Elevation of privilege | Normal user accesses admin route or changes role field | Full application compromise | Deny-by-default authorization, server-controlled roles, MFA for admins, least privilege, security tests |

## 4. Abuse cases
1. **Credential stuffing:** attacker tries passwords leaked from another service.  
   Mitigate with rate limits, breached-password checks where feasible, MFA, and user alerts.
2. **Account enumeration:** attacker learns which email addresses are registered from login/reset responses.  
   Use consistent response text and similar response timing.
3. **IDOR:** user changes `/users/123` to `/users/124` and reads another profile.  
   Enforce ownership checks on the server for every object access.
4. **Session theft:** attacker obtains a session cookie through malware or an insecure device.  
   Use HTTPS, `Secure`, `HttpOnly`, and appropriate `SameSite` cookie attributes; rotate and expire sessions; provide logout/revocation.
5. **Injection:** attacker submits crafted input to a database-backed field.  
   Use parameterized queries, validation, safe output encoding, and least-privileged database access.
6. **Password-reset abuse:** attacker repeatedly requests resets or reuses an old token.  
   Use random, single-use, short-lived tokens; store token hashes; rate-limit requests; invalidate after use.

## 5. Security requirements
- Enforce HTTPS; redirect HTTP to HTTPS and enable HSTS after confirming HTTPS works.
- Store passwords only as salted, adaptive hashes (prefer Argon2id; use bcrypt if appropriate). Never store plaintext or reversible encryption.
- Validate input on the server and encode output for its context.
- Enforce authorization on every protected operation, not only in the UI.
- Use parameterized database queries and least-privilege database accounts.
- Set session cookies `Secure`, `HttpOnly`, and `SameSite=Lax` or stricter where compatible.
- Rotate session identifiers after authentication and privilege changes; expire idle and absolute sessions.
- Protect state-changing requests against CSRF when cookie-based authentication is used.
- Return generic authentication and password-reset errors.
- Centralize security logging and alert on suspicious activity without recording passwords, tokens, or session IDs.
- Keep dependencies and runtime patched; store secrets outside source control.

## 6. Residual risk and validation
Rate limiting can affect legitimate users behind shared networks; tune thresholds and provide safe recovery. MFA reduces but does not eliminate phishing risk. Logging must be reviewed to ensure it contains useful event metadata but no secrets. Validate controls with code review, automated tests, dependency scanning, and a deployment configuration review.
