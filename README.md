<h1 align="center">Backend Developer</h1>
<p align="center"><strong>Backend Developer</strong> — Python / C# • OAuth2 / OIDC • Distributed systems & event pipelines</p>
<p align="center">FastAPI • ASP.NET Core • PostgreSQL • Redis • Kafka • RabbitMQ • Docker • Kubernetes</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python" />
  <img src="https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white" alt="FastAPI" />
  <img src="https://img.shields.io/badge/C%23-512BD4?style=for-the-badge&logo=csharp&logoColor=white" alt="C#" />
  <img src="https://img.shields.io/badge/.NET-512BD4?style=for-the-badge&logo=dotnet&logoColor=white" alt=".NET" />
  <img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" alt="PostgreSQL" />
  <img src="https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white" alt="Redis" />
  <img src="https://img.shields.io/badge/Apache_Kafka-231F20?style=for-the-badge&logo=apachekafka&logoColor=white" alt="Kafka" />
  <img src="https://img.shields.io/badge/RabbitMQ-FF6600?style=for-the-badge&logo=rabbitmq&logoColor=white" alt="RabbitMQ" />
  <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker" />
  <img src="https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white" alt="Kubernetes" />
</p>

---

## About

Backend engineer with hands-on experience across two stacks: **Python / FastAPI** for async, security-sensitive services, and **C# / ASP.NET Core** for strictly-typed line-of-business APIs. Most of my recent work has been around **identity, authorization, and domain modelling** — designing systems where the contract between services and the failure paths matter as much as the happy-path code.

---

## Key Projects

### [Aegis](https://github.com/SabirDzh/Aegis) — OIDC/OAuth2 Authorization Server

A standards-oriented IAM core built as a security-first modular monolith on **Python 3.11 / FastAPI / SQLAlchemy async / PostgreSQL / Redis**.

What is actually implemented and verifiable in the repo:

- **OIDC/OAuth2 protocol surface:** Authorization Code + PKCE (`S256` mandatory), Client Credentials, Refresh Token Rotation with **token family tracking** and reuse detection. Endpoints: `/.well-known/openid-configuration`, `/oauth2/jwks`, `/authorize`, `/token`, `/introspect`, `/revoke`, `/userinfo`.
- **JWT signing:** RS256 with rotating key material, `kid` in header, JWKS publication, server-side `jti` tracking for one-time action tokens.
- **Credential security:** Argon2id with tuned memory/time/parallelism and opportunistic rehash on successful login. Default-deny route protection, anti-enumeration on auth flows, audit events with correlation IDs.
- **Architecture:** modular monolith split into 7 bounded contexts (`auth`, `identity`, `oauth`, `policy`, `sessions`, `audit`, `ops`), each with its own API / service / domain / persistence layers. Multitenancy posture in place (`tenant_id` on core tables) without forcing rewrites later.
- **Data plane:** PostgreSQL as source of truth, Redis for ephemeral / replay / hot revocation hints. UUIDv7 for new IDs, Alembic migrations only.
- **Self-service & admin APIs:** device/session management (`/v1/me/devices`), revoke-others, plus admin API for users, clients, roles, scopes, role bindings, and key lifecycle.
- **CI/CD:** GitHub Actions pipeline runs `ruff`, `mypy`, Alembic migration check, unit + integration tests, `pip-audit`, and `gitleaks` secrets scan on every PR. Staging CD builds and pushes images to **GHCR**, deploys to a VPS over SSH via Docker Compose, runs a `/health/ready` smoke check, and **automatically rolls back to the previous image tag** when smoke fails.

### [EduMeet](https://github.com/SabirDzh/EduMeet) — Student ↔ Teacher Consultation Platform (C# / .NET 10)

Backend API for online education consultations built on **ASP.NET Core (.NET 10) / Entity Framework Core / PostgreSQL / Redis / Docker**, with **Supabase Auth** as the identity provider. ~21k lines of C# and a dedicated integration test project (`Api.Tests`) with 18 test suites covering authorization, OpenAPI contract surface, and per-controller flows.

- **Layered architecture with thin controllers:** `Api → Services → Repositories → DbContext`. Centralised validation via FluentValidation, explicit DTO records, typed `ServiceResult` returns mapped into consistent `ProblemDetails` responses.
- **Explicit domain boundaries.** Teacher *availability slots* are managed independently of *consultation bookings* — booking does not mutate availability directly. Rescheduling is modelled as `reschedule-requests` with its own status machine instead of a side-effect PATCH. Onboarding flows (teacher applications, subject requests) live as separate resources with their own status enums.
- **Security baseline that is actually wired up:** Supabase-issued JWTs with both `Authorization: Bearer` and secure HTTP-only cookies, role-based authorization (`RoleRequirement` + custom `RoleAuthorizationHandler`), policy-based rate limiting, HSTS with `preload` + `IncludeSubDomains` + 365-day max-age, dedicated `SecurityHeadersMiddleware`, hCaptcha verification on sensitive endpoints.
- **Observability:** Serilog structured logging enriched with service / environment / host / per-request context, dedicated request-timing middleware.
- **Production deploy on every `main` push:** GitHub Actions builds the image, pushes to **GHCR** tagged by commit SHA (`sha-<sha>`), SSHes into the production VPS, runs a one-shot `migrate` compose service, restarts `postgres` / `redis` / `api`, hits a health endpoint, and rolls back by re-deploying the previous SHA on failure. Custom domain + TLS certificate, SMTP wired in for transactional email.

---

## Core Skills

### Backend

- **Languages:** Python 3.11+, C# / .NET 10 (ASP.NET Core Web API).
- **Frameworks:** FastAPI (async I/O, dependency injection, Pydantic v2, SqlAlchemy, Alembic), ASP.NET Core (EF Core, FluentValidation, controllers + DTO records, ProblemDetails, Serilog).
- **API design:** REST + OpenAPI, OIDC/OAuth2 (Auth Code + PKCE, Client Credentials, refresh rotation), JWT/JWKS, GraphQL.
- **Architecture:** modular monolith with bounded contexts, layered/clean architecture, explicit service boundaries, separation of availability/booking/scheduling concerns, idempotent handlers, default-deny security posture, `ProblemDetails` error contracts.

### Identity & Authorization

- Building IAM from primitives: Argon2id password hashing, RS256 JWT signing with `kid` rotation and JWKS, refresh token rotation with family/reuse detection, one-time action tokens (`verify_email`, `reset_password`) tracked server-side by hashed `jti`.
- Integrating with managed IdPs: **Supabase Auth** in ASP.NET Core (Bearer JWT + secure HTTP-only cookies, role-based policy handlers); familiar with the role Keycloak / ZITADEL play in the same niche.
- Hardening the edge: policy-based rate limiting, HSTS preload, security headers middleware, hCaptcha verification on sensitive endpoints.

### Data

- **PostgreSQL:** schema design, indexes, migrations (Alembic, EF Core migrations), UUIDv7, query tuning.
- **MySQL:** schema design and migrations.
- **Redis:** caching, rate limiting, pub/sub, distributed locks, ephemeral state and hot revocation hints.

### Messaging & Async

- **Brokers:** Apache Kafka, RabbitMQ.
- **Patterns:** producers/consumers, retries with backoff, dead-letter queues, idempotent handlers, in-process background workers (cleanup of expired artifacts, audit flush/retry, key lifecycle).

### Infrastructure & DevOps

- **Containers & orchestration:** Docker, Docker Compose, Kubernetes; multi-stage Dockerfiles; image registries (GHCR, Docker Hub).
- **VPS / deployment:** end-to-end backend deployment on VPS/VDS — Docker Compose stacks, GitHub Actions CI/CD over SSH, smoke checks and automatic rollback on health-check failure.
- **DNS & ingress:** A/CNAME records for nginx reverse proxies; SPF / DKIM / DMARC records for outbound transactional email and deliverability.
- **CI/CD pipelines:** GitHub Actions for lint, type check, migrations, unit + integration tests, dependency audit (`pip-audit`), secrets scanning (`gitleaks`), Docker Buildx image build/push to GHCR with SHA-tagged immutable artifacts, SSH deploy with migration job + health check + SHA-based rollback.

---

## Contact

- Telegram: [@SABIR_DZH](https://t.me/SABIR_DZH)
- Email: [SabirDzhabrailov.111@gmail.com](mailto:SabirDzhabrailov.111@gmail.com)

> Open to backend roles and substantive collaborations — feel free to reach out.
