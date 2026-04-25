# Identity Service

A full-stack service for user registration and lifecycle management, built with a clean separation of concerns across three layers: **UI**, **API**, and **DB**. The service is designed to start simple and evolve progressively toward fine-grained access control using **RBAC** (Role-Based Access Control) and **ABAC** (Attribute-Based Access Control).

---

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Project Structure](#project-structure)
- [Core Features](#core-features)
  - [Phase 1 — Basic User Management](#phase-1--basic-user-management)
  - [Phase 2 — Role-Based Access Control (RBAC)](#phase-2--role-based-access-control-rbac)
  - [Phase 3 — Attribute-Based Access Control (ABAC)](#phase-3--attribute-based-access-control-abac)
- [User Workflows](#user-workflows)
  - [Registration Flow](#registration-flow)
  - [Login Flow](#login-flow)
  - [Profile Management Flow](#profile-management-flow)
  - [Account Deactivation Flow](#account-deactivation-flow)
- [Tech Stack](#tech-stack)
- [Data Models](#data-models)
- [API Endpoints](#api-endpoints)
- [Security Considerations](#security-considerations)
- [Roadmap](#roadmap)

---

## Overview

The Identity Service acts as the single source of truth for user identity within a system. It handles:

- **Who you are** — user registration, authentication, and profile management
- **What you can do** — authorization via roles and permissions (RBAC)
- **Under what conditions** — fine-grained policy enforcement based on attributes (ABAC)

The service is intentionally scoped to identity and access concerns only, making it composable with other services via standard protocols (e.g., JWT tokens, OAuth2 flows).

---

## Architecture

The service is structured as a three-tier application:

```bash
┌─────────────────────────────────────┐
│              UI Layer               │
│  (Web interface for user-facing     │
│   workflows and admin dashboard)    │
└───────────────┬─────────────────────┘
                │ HTTP / REST
┌───────────────▼─────────────────────┐
│             API Layer               │
│  (Business logic, authentication,   │
│   authorization, token management)  │
└───────────────┬─────────────────────┘
                │ ORM / Query Layer
┌───────────────▼─────────────────────┐
│              DB Layer               │
│  (Persistent storage for users,     │
│   roles, permissions, audit logs)   │
└─────────────────────────────────────┘
```

Each layer has clear responsibilities and can be developed, tested, and scaled independently.

---

## Project Structure

```bash
identity-service/
├── ui/                        # Frontend (user-facing + admin)
│   ├── public/
│   └── src/
│       ├── pages/             # Registration, Login, Profile, Admin
│       ├── components/
│       └── services/          # API client wrappers
│
├── api/                       # Backend API service
│   ├── src/
│   │   ├── controllers/       # Request handlers
│   │   ├── services/          # Business logic
│   │   ├── middleware/        # Auth, validation, rate-limiting
│   │   ├── models/            # Data transfer objects / schemas
│   │   └── routes/            # Route definitions
│   └── tests/
│
├── db/                        # Database layer
│   ├── migrations/            # Schema versioned migrations
│   ├── seeds/                 # Initial/test data
│   └── schemas/               # Entity definitions
│
├── docs/                      # ADRs, API specs, workflow diagrams
└── README.md
```

---

## Core Features

### Phase 1 — Basic User Management

The foundation of the service. Covers the full lifecycle of a user account.

| Feature              | Description                                            |
|----------------------|--------------------------------------------------------|
| User Registration    | Create a new account with email and password           |
| Email Verification   | Verify ownership of the provided email address         |
| Authentication       | Secure login with hashed credentials and JWT issuance  |
| Session Management   | Token refresh, revocation, and expiry handling         |
| Profile Management   | View and update user profile details                   |
| Password Reset       | Initiate and complete a password reset via email       |
| Account Deactivation | Soft-delete or deactivate an account                   |
| Audit Logging        | Record all significant identity events                 |

---

### Phase 2 — Role-Based Access Control (RBAC)

Extends the service to support coarse-grained authorization. Users are assigned roles, and roles are associated with permissions.

**Core concepts:**

- **Role** — a named set of permissions (e.g., `admin`, `moderator`, `viewer`)
- **Permission** — a specific action on a resource (e.g., `user:delete`, `report:read`)
- **Role Assignment** — binding a user to one or more roles

**Capabilities added in this phase:**

- Create, update, and delete roles
- Define permissions and associate them with roles
- Assign and revoke roles for users
- Enforce role-based guards on API endpoints
- Admin UI for role and permission management

**Example role hierarchy:**

```bash
admin
 └── can do everything

moderator
 └── user:read
 └── user:suspend

viewer
 └── user:read
```

---

### Phase 3 — Attribute-Based Access Control (ABAC)

Builds on top of RBAC to support fine-grained, policy-driven access control. Access decisions are made by evaluating policies against a set of attributes from the subject (user), resource, action, and environment.

**Core concepts:**

- **Subject attributes** — properties of the user (e.g., `department`, `clearance_level`, `account_status`)
- **Resource attributes** — properties of what is being accessed (e.g., `owner`, `sensitivity`, `region`)
- **Environment attributes** — contextual factors (e.g., `time_of_day`, `ip_address`, `request_origin`)
- **Policy** — a rule that evaluates attribute conditions to allow or deny access

**Example policy:**

> A user may update a profile **only if** they are the owner of that profile, **or** they hold the `admin` role, **and** the request originates from a trusted network.

**Capabilities added in this phase:**

- Define and manage ABAC policies
- Evaluate policies at request time via a policy engine
- Combine RBAC roles as subject attributes within ABAC policies
- Audit trail enriched with evaluated policy context

---

## User Workflows

### Registration Flow

```bash
User fills registration form
        │
        ▼
API validates input (email format, password strength, uniqueness)
        │
        ▼
Password hashed and user record persisted
        │
        ▼
Verification email dispatched
        │
        ▼
User clicks verification link
        │
        ▼
Account marked as verified and active
```

---

### Login Flow

```bash
User submits credentials
        │
        ▼
API retrieves user record by email
        │
        ▼
Password compared against stored hash
        │
     ┌──┴──┐
   match  no match
     │        │
     ▼        ▼
Issue JWT  Return 401
(access + refresh tokens)
     │
     ▼
Tokens returned to client
```

---

### Profile Management Flow

```bash
Authenticated user requests profile update
        │
        ▼
API validates JWT and extracts user identity
        │
        ▼
Request body validated against allowed fields
        │
        ▼
DB record updated
        │
        ▼
Audit event logged
        │
        ▼
Updated profile returned
```

---

### Account Deactivation Flow

```bash
User (or admin) initiates deactivation
        │
        ▼
Confirmation required (re-authentication or admin approval)
        │
        ▼
Account status set to DEACTIVATED (soft delete)
        │
        ▼
All active sessions revoked
        │
        ▼
Notification dispatched to user's email
```

---

## Tech Stack

> Specific technology choices will be finalized as development progresses. The following represents the intended direction.

| Layer            | Technology                                   |
|------------------|----------------------------------------------|
| UI               | React / Next.js                              |
| API              | Node.js / Go                                 |
| Auth             | JWT (access tokens) + refresh token rotation |
| DB               | PostgreSQL                                   |
| ORM / Query      | Prisma / sqlx                                |
| Migrations       | Flyway / golang-migrate                      |
| Containerization | Docker + Docker Compose                      |
| Testing          | Jest (UI), integration tests (API)           |

---

## Data Models

### User

| Field            | Type      | Notes                                           |
|------------------|-----------|-------------------------------------------------|
| `id`             | UUID      | Primary key                                     |
| `email`          | string    | Unique, indexed                                 |
| `password_hash`  | string    | bcrypt hashed                                   |
| `display_name`   | string    |                                                 |
| `status`         | enum      | `PENDING`, `ACTIVE`, `SUSPENDED`, `DEACTIVATED` |
| `email_verified` | boolean   |                                                 |
| `created_at`     | timestamp |                                                 |
| `updated_at`     | timestamp |                                                 |

### Role *(Phase 2)*

| Field         | Type   | Notes       |
|---------------|--------|-------------|
| `id`          | UUID   | Primary key |
| `name`        | string | Unique      |
| `description` | string |             |

### Permission *(Phase 2)*

| Field      | Type   | Notes               |
|------------|--------|---------------------|
| `id`       | UUID   | Primary key         |
| `action`   | string | e.g., `user:delete` |
| `resource` | string | e.g., `user`        |

### Policy *(Phase 3)*

| Field        | Type   | Notes                    |
|--------------|--------|--------------------------|
| `id`         | UUID   | Primary key              |
| `name`       | string |                          |
| `effect`     | enum   | `ALLOW`, `DENY`          |
| `conditions` | JSON   | Attribute-based rule set |

---

## API Endpoints

### Auth & User

| Method     | Path                                  | Description                      |
|------------|---------------------------------------|----------------------------------|
| `POST`     | `/api/v1/auth/register`               | Register a new user              |
| `POST`     | `/api/v1/auth/login`                  | Authenticate and receive tokens  |
| `POST`     | `/api/v1/auth/logout`                 | Revoke current session           |
| `POST`     | `/api/v1/auth/refresh`                | Refresh access token             |
| `POST`     | `/api/v1/auth/verify-email`           | Verify email address             |
| `POST`     | `/api/v1/auth/password-reset/request` | Initiate password reset          |
| `POST`     | `/api/v1/auth/password-reset/confirm` | Complete password reset          |
| `GET`      | `/api/v1/users/me`                    | Get current user profile         |
| `PUT`      | `/api/v1/users/me`                    | Update current user profile      |
| `DELETE`   | `/api/v1/users/me`                    | Deactivate current account       |

### Admin *(Phase 2+)*

| Method   | Path                                     | Description           |
|----------|------------------------------------------|-----------------------|
| `GET`    | `/api/v1/admin/users`                    | List all users        |
| `GET`    | `/api/v1/admin/users/:id`                | Get user by ID        |
| `POST`   | `/api/v1/admin/roles`                    | Create a role         |
| `POST`   | `/api/v1/admin/users/:id/roles`          | Assign role to user   |
| `DELETE` | `/api/v1/admin/users/:id/roles/:roleId`  | Revoke role from user |

---

## Security Considerations

- Passwords stored using **bcrypt** with a sufficient cost factor
- Access tokens are **short-lived** (e.g., 15 minutes); refresh tokens are rotated on use
- Refresh tokens are **stored server-side** and can be revoked
- All sensitive operations produce **audit log entries**
- Email verification required before full account activation
- Rate limiting applied to auth endpoints to mitigate brute-force attacks
- Input validation enforced at the API boundary on all incoming requests
- HTTPS enforced in all non-local environments

---

## Roadmap

- [ ] **Phase 1** — Core user registration, authentication, and profile management
- [ ] **Phase 1** — Email verification and password reset flows
- [ ] **Phase 1** — Session management with refresh token rotation
- [ ] **Phase 1** — Audit logging for identity events
- [ ] **Phase 2** — Role and permission management
- [ ] **Phase 2** — RBAC enforcement on API endpoints
- [ ] **Phase 2** — Admin dashboard for user and role management
- [ ] **Phase 3** — ABAC policy engine integration
- [ ] **Phase 3** — Policy authoring UI
- [ ] **Phase 3** — Combined RBAC + ABAC evaluation
