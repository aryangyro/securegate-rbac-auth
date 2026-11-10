# SecureGate — Role-Based Authentication & Access Control System

SecureGate is a desktop application, backed by a secure REST API, that controls who can sign in to an organisation's internal records portal and what each signed-in user is allowed to see and do. The demo scenario is a small company's employee records system with four roles: Administrator, HR Manager, Employee and Auditor.

## What it does

- **Secure login** — salted password hashing with Argon2id, account lockout after 5 failed attempts, and optional TOTP two-factor authentication.
- **Role-Based Access Control (RBAC)** — enforced on the server by a permission decorator on every endpoint, deny by default, following least privilege. The GUI hides buttons a role cannot use, but the server is the only authority.
- **Session management** — short-lived JWT access tokens (15 minutes) and rotating refresh tokens (8 hours), with server-side revocation on logout.
- **Encryption** — sensitive fields (salary, national ID, phone, MFA secrets) encrypted with AES-256-GCM, with the key held outside the database.
- **Tamper-evident audit log** — every security event is logged, with entries chained by HMAC-SHA256 so an edited or deleted entry is detected. The Auditor role can review and verify the log.

## Roles

| Role | Permitted actions |
| --- | --- |
| Administrator | Manage users and roles; cannot read salary data unless also HR |
| HR Manager | Create, view and edit all employee records, including salary |
| Employee | View and update only their own profile (contact details) |
| Auditor | Read-only access to the audit log and integrity check; no record access |

## Architecture

Three tiers, with every request checked by the server:

1. **Presentation tier** — a PyQt6 desktop GUI that talks to the server only over HTTPS.
2. **Application tier** — a Flask REST API with a security middleware pipeline: rate limiter → input validator → token verifier → RBAC policy check → business logic → audit logger.
3. **Data tier** — a SQLite database accessed only through SQLAlchemy ORM, with sensitive fields encrypted and keys stored outside the database.

Design principles: least privilege, deny by default, defence in depth, complete mediation (every request checked), fail securely, and separation of duties.

## Technology stack

Python 3.12, PyQt6, Flask 3, Flask-Limiter, SQLite + SQLAlchemy, argon2-cffi, PyJWT, pyotp, qrcode, cryptography (AES-256-GCM, HMAC-SHA256), Marshmallow, pytest.

## Threat model

A STRIDE analysis covers spoofing (brute force / credential stuffing), tampering (JWT role claims, audit log edits), repudiation, information disclosure (database theft, interception), denial of service (login flooding), and elevation of privilege (IDOR, calling admin endpoints as an Employee). Each threat has a risk rating and a planned mitigation.

## Testing

Unit and integration tests with pytest, access-control matrix tests (every role calls every endpoint), and attack simulations: brute-force login, SQL injection and XSS payloads, tampered and expired JWTs, replayed refresh tokens, IDOR, and log tampering. Bandit for static analysis and pip-audit for vulnerable dependencies.

## Work log

- **2026-11-10** — Project proposal completed: architecture and data-flow design, RBAC permission matrix, STRIDE threat model, 6-phase build plan, and technology stack. Next step is the working prototype.
