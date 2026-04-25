# Server — API Layer

This directory contains the backend API for the Identity Service. It is responsible for all business logic related to user authentication, authorization, session management, and access control policy enforcement.

---

## Responsibilities

- Expose a versioned REST API consumed by the UI layer and any external services
- Authenticate users and issue short-lived JWT access tokens with refresh token rotation
- Enforce RBAC and ABAC authorization rules on protected endpoints
- Validate all incoming requests at the API boundary
- Emit audit log entries for all significant identity events
- Communicate with the DB layer via an ORM / query builder

---

## Structure

```bash
server/
├── src/
│   ├── controllers/         # Request handlers — map routes to service calls
│   ├── services/            # Core business logic (auth, user, role, policy)
│   ├── middleware/          # Auth guards, input validation, rate limiting, logging
│   ├── models/              # Data transfer objects and internal schemas
│   ├── routes/              # Route definitions and versioned API grouping
│   └── config/              # Environment configuration and constants
└── tests/
    ├── unit/                # Unit tests for services and utilities
    └── integration/         # Integration tests for API endpoints
```

---

## API Overview

All endpoints are versioned under `/api/v1`.

### Auth & User Endpoints

| Method   | Path                                  | Auth Required | Description                      |
|----------|---------------------------------------|:-------------:|----------------------------------|
| `POST`   | `/api/v1/auth/register`               | No            | Register a new user              |
| `POST`   | `/api/v1/auth/login`                  | No            | Authenticate and receive tokens  |
| `POST`   | `/api/v1/auth/logout`                 | Yes           | Revoke current session           |
| `POST`   | `/api/v1/auth/refresh`                | No            | Refresh access token             |
| `POST`   | `/api/v1/auth/verify-email`           | No            | Verify email address             |
| `POST`   | `/api/v1/auth/password-reset/request` | No            | Initiate password reset          |
| `POST`   | `/api/v1/auth/password-reset/confirm` | No            | Complete password reset          |
| `GET`    | `/api/v1/users/me`                    | Yes           | Get current user profile         |
| `PUT`    | `/api/v1/users/me`                    | Yes           | Update current user profile      |
| `DELETE` | `/api/v1/users/me`                    | Yes           | Deactivate current account       |

### Admin Endpoints *(Phase 2+)*

| Method   | Path                                    | Auth Required | Description           |
|----------|-----------------------------------------|:-------------:|-----------------------|
| `GET`    | `/api/v1/admin/users`                   | Admin         | List all users        |
| `GET`    | `/api/v1/admin/users/:id`               | Admin         | Get user by ID        |
| `POST`   | `/api/v1/admin/roles`                   | Admin         | Create a role         |
| `POST`   | `/api/v1/admin/users/:id/roles`         | Admin         | Assign role to user   |
| `DELETE` | `/api/v1/admin/users/:id/roles/:roleId` | Admin         | Revoke role from user |

---

## Auth Strategy

- **Access tokens** — short-lived JWTs (default: 15 minutes), signed and verified server-side
- **Refresh tokens** — longer-lived, stored server-side, rotated on every use
- **Token revocation** — supported via server-side token registry; all sessions can be invalidated on logout or deactivation

---

## Tech Stack

| Concern       | Technology               |
|---------------|--------------------------|
| Runtime       | Node.js / Go             |
| Framework     | Express / Fastify / Gin  |
| Auth          | JWT (jsonwebtoken / jwt) |
| Validation    | Zod / Joi / go-validator |
| ORM / Query   | Prisma / sqlx            |
| Testing       | Jest / Go testing        |

---

## Getting Started

```bash
# Install dependencies
npm install

# Start development server (with hot reload)
npm run dev

# Run tests
npm test

# Build for production
npm run build
```

---

## Environment Variables

| Variable            | Description                            |
|---------------------|----------------------------------------|
| `PORT`              | Port the server listens on             |
| `DATABASE_URL`      | PostgreSQL connection string           |
| `JWT_SECRET`        | Secret key for signing access tokens   |
| `JWT_REFRESH_SECRET`| Secret key for signing refresh tokens  |
| `JWT_EXPIRY`        | Access token expiry (e.g., `15m`)      |
| `EMAIL_SERVICE_URL` | URL of the email dispatch service      |

Create a `.env` file at the root of this directory and populate the variables above before running the server.

---

## Related

- [Client Layer](../client/README.md) — Frontend application
- [DB Layer](../db/README.md) — Database schema and migrations
- [Docs](../docs/README.md) — Architecture decisions and API specifications
