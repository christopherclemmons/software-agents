# Security Standards

## Purpose
Ensure all systems are built, deployed, and operated in a way that minimizes risk, protects data, and prevents unauthorized access.

Security is not a feature. It is a requirement at every layer of the system.

---

## Core Principles

### 1. Least Privilege
Every system, service, and user should have only the minimum permissions required.

- no broad permissions by default
- no wildcard access unless absolutely necessary
- access must be explicit and scoped

---

### 2. Zero Trust Mindset
Do not trust:

- client input
- internal network traffic
- other services without verification

Always validate and verify.

---

### 3. Defense in Depth
Security should exist at multiple layers:

- frontend validation
- backend validation
- database constraints
- infrastructure controls

Failure in one layer should not expose the system.

---

### 4. Secure by Default
Systems should be secure without requiring extra configuration.

- safe defaults
- deny by default, allow explicitly
- no debug or open access modes in production

---

### 5. Explicit Over Implicit
Security decisions must be clear and intentional.

- no hidden access rules
- no silent permission bypasses
- no assumptions about user identity

---

## Authentication Standards

## Purpose
Verify identity of users and systems.

### Rules

- use a trusted identity provider (e.g., Cognito, OAuth, etc.)
- never store plaintext passwords
- use secure token-based authentication (JWT or equivalent)
- validate tokens on every request
- enforce token expiration
- rotate signing keys periodically
- support logout or token invalidation where applicable

### Token Handling

- never trust tokens without validation
- verify signature, expiration, issuer, and audience
- do not expose tokens in logs
- store tokens securely on the client side

---

## Authorization Standards

## Purpose
Control what authenticated users can access.

### Rules

- enforce authorization at the backend
- do not rely on frontend checks
- implement role-based or permission-based access control
- validate access for every protected resource
- do not assume ownership without verification

### Examples

Bad:
- user sends userId → system trusts it

Good:
- system derives userId from token
- verifies ownership or permission

---

## Input Validation Standards

## Purpose
Prevent injection attacks and invalid data.

### Rules

- validate all external input
- treat all input as untrusted
- validate type, format, and range
- use DTOs or schema validation
- reject unexpected fields when appropriate

### Must Protect Against

- SQL injection
- command injection
- XSS (cross-site scripting)
- malformed payloads

---

## Output Encoding Standards

## Purpose
Prevent XSS and data leakage.

### Rules

- encode output rendered in UI
- sanitize user-generated content
- avoid rendering raw HTML unless explicitly required and sanitized
- ensure API responses do not expose sensitive data

---

## Data Protection Standards

## Purpose
Protect sensitive and personal data.

### Rules

- never store sensitive data unnecessarily
- encrypt data in transit (HTTPS required)
- encrypt sensitive data at rest where applicable
- mask or redact sensitive fields in logs
- follow least exposure principle

### Sensitive Data Examples

- passwords
- tokens
- API keys
- personal identifiable information (PII)
- payment information

---

## Secret Management Standards

## Purpose
Protect credentials and secrets.

### Rules

- never hardcode secrets in code
- store secrets in a secure manager (AWS Secrets Manager, SSM, etc.)
- rotate secrets regularly
- restrict access to secrets
- do not expose secrets in logs or error messages

---

## API Security Standards

### Rules

- require authentication for protected endpoints
- enforce authorization on every request
- validate all inputs
- rate limit endpoints where necessary
- avoid exposing internal implementation details
- return generic error messages for security-sensitive failures

---

## Rate Limiting and Abuse Protection

## Purpose
Prevent abuse and denial-of-service.

### Rules

- implement rate limiting for public endpoints
- throttle repeated failed authentication attempts
- detect abnormal usage patterns
- protect critical endpoints (login, registration, etc.)

---

## Logging and Monitoring Security

### Rules

- log authentication attempts (success and failure)
- log authorization failures
- log suspicious activity
- do not log sensitive data
- monitor for anomalies and repeated failures

---

## Infrastructure Security Standards

### Rules

- run services in private networks where possible
- restrict inbound traffic
- use security groups or firewall rules
- disable unnecessary ports
- enforce HTTPS everywhere
- protect public endpoints with WAF or equivalent where needed

---

## Database Security Standards

### Rules

- restrict direct database access
- use least privilege database roles
- do not expose database publicly
- validate all queries through application layer
- use parameterized queries (no string concatenation)

---

## Dependency Security

### Rules

- avoid untrusted or outdated libraries
- keep dependencies updated
- monitor for known vulnerabilities
- remove unused dependencies

---

## File Upload Security

If file uploads exist:

- validate file type and size
- scan files if necessary
- store files securely
- do not execute uploaded files
- avoid direct public exposure without controls

---

## Error Handling Security

### Rules

- do not expose stack traces in production
- do not reveal internal system details
- return safe, generic error messages
- log detailed errors internally only

---

## Session and Token Security

### Rules

- use secure cookies where applicable
- enforce HTTPS-only transmission
- set appropriate expiration
- prevent session fixation
- invalidate sessions when necessary

---

## Background Jobs and Queues

### Rules

- validate inputs before processing
- do not trust message payloads
- enforce permissions at processing time
- protect queues from unauthorized access

---

## Deployment Security

### Rules

- separate environments (dev, staging, prod)
- do not reuse production credentials in lower environments
- restrict access to production systems
- audit deployment actions
- ensure CI/CD pipelines do not leak secrets

---

## Access Control Standards

### Rules

- restrict access to production systems
- use role-based access for engineers
- audit access regularly
- remove unused access immediately
- require strong authentication (MFA where possible)

---

## Audit and Compliance Awareness

### Rules

- track critical actions (data changes, auth events)
- maintain audit logs where needed
- ensure traceability of changes
- support investigation of incidents

---

## Incident Response Expectations

When a security issue occurs:

- identify affected systems
- revoke compromised credentials
- contain the issue
- log and analyze the event
- implement fixes to prevent recurrence

---

## Review Checklist

Before considering a system secure:

- Are authentication and authorization enforced correctly?
- Are inputs validated?
- Are secrets managed securely?
- Is sensitive data protected?
- Are logs safe and useful?
- Are APIs protected?
- Is infrastructure locked down?
- Are dependencies safe?
- Are error messages safe?
- Is least privilege enforced?

---

## Anti-Patterns to Avoid

Do not:

- trust client input
- hardcode secrets
- expose internal errors
- skip authorization checks
- use overly broad permissions
- leave debug modes enabled
- rely only on frontend validation
- ignore failed login attempts
- expose sensitive data in logs

---

## Goal
Build systems that are secure by design, not patched after failure.

A secure system should:

- protect user data
- resist common attacks
- enforce access boundaries
- minimize blast radius
- remain safe under misuse or attack