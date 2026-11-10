# Work Log

## 2026-11-10 — Project proposal done

Finished the proposal for SecureGate, a role-based authentication and access control system. The proposal covers the design, the RBAC permission matrix, the STRIDE threat model, the security features (authentication, sessions, input validation, encryption, hashing/integrity, API security and logging), the technology stack, and a 6-phase build plan from design through testing to documentation.

The hardest part was keeping the scope realistic: the system has to be a working prototype, not a full product, so single sign-on, mobile clients, cloud deployment and full ABAC policies were deliberately left out as future work.

Next: build the prototype — Flask API with the middleware pipeline, Argon2id login, JWT access/refresh tokens, and the RBAC decorator on every endpoint.
