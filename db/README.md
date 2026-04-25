# DB — Database Layer

This directory contains everything related to persistent storage for the Identity Service — schema definitions, versioned migrations, and seed data for local development and testing.

---

## Responsibilities

- Define and version the database schema via migrations
- Provide seed scripts for bootstrapping local and test environments
- Document the entity model and relationships
- Act as the single source of truth for the data structure of the service

---

## Structure

```bash
db/
├── migrations/              # Ordered, versioned schema migration files
│   ├── V001__create_users.sql
│   ├── V002__create_roles_and_permissions.sql
│   ├── V003__create_user_roles.sql
│   ├── V004__create_refresh_tokens.sql
│   ├── V005__create_audit_logs.sql
│   └── V006__create_policies.sql
├── seeds/                   # Seed data for development and testing
│   ├── 001_default_roles.sql
│   └── 002_admin_user.sql
└── schemas/                 # Human-readable entity definitions and ERD source
```

---

## Entity Overview

### `users`

The core identity record for every registered account.

| Column           | Type        | Constraints                                        |
|------------------|-------------|----------------------------------------------------|
| `id`             | UUID        | PRIMARY KEY, default `gen_random_uuid()`           |
| `email`          | VARCHAR     | UNIQUE, NOT NULL                                   |
| `password_hash`  | TEXT        | NOT NULL                                           |
| `display_name`   | VARCHAR     |                                                    |
| `status`         | ENUM        | `PENDING`, `ACTIVE`, `SUSPENDED`, `DEACTIVATED`    |
| `email_verified` | BOOLEAN     | DEFAULT false                                      |
| `created_at`     | TIMESTAMPTZ | DEFAULT now()                                      |
| `updated_at`     | TIMESTAMPTZ | DEFAULT now()                                      |

### `roles` *(Phase 2)*

Named groupings of permissions that can be assigned to users.

| Column        | Type    | Constraints          |
|---------------|---------|----------------------|
| `id`          | UUID    | PRIMARY KEY          |
| `name`        | VARCHAR | UNIQUE, NOT NULL     |
| `description` | TEXT    |                      |

### `permissions` *(Phase 2)*

Granular action-on-resource definitions associated with roles.

| Column     | Type    | Constraints |
|------------|---------|-------------|
| `id`       | UUID    | PRIMARY KEY |
| `action`   | VARCHAR | NOT NULL    |
| `resource` | VARCHAR | NOT NULL    |

### `user_roles` *(Phase 2)*

Join table binding users to roles.

| Column      | Type | Constraints                         |
|-------------|------|-------------------------------------|
| `user_id`   | UUID | FK → `users.id`, NOT NULL           |
| `role_id`   | UUID | FK → `roles.id`, NOT NULL           |
| `assigned_at` | TIMESTAMPTZ | DEFAULT now()              |

### `refresh_tokens`

Server-side storage for refresh tokens, enabling revocation.

| Column       | Type        | Constraints                  |
|--------------|-------------|------------------------------|
| `id`         | UUID        | PRIMARY KEY                  |
| `user_id`    | UUID        | FK → `users.id`, NOT NULL    |
| `token_hash` | TEXT        | NOT NULL                     |
| `expires_at` | TIMESTAMPTZ | NOT NULL                     |
| `revoked`    | BOOLEAN     | DEFAULT false                |
| `created_at` | TIMESTAMPTZ | DEFAULT now()                |

### `audit_logs`

Immutable record of all significant identity events.

| Column      | Type        | Constraints              |
|-------------|-------------|--------------------------|
| `id`        | UUID        | PRIMARY KEY              |
| `user_id`   | UUID        | FK → `users.id`          |
| `event`     | VARCHAR     | NOT NULL                 |
| `metadata`  | JSONB       |                          |
| `ip_address`| INET        |                          |
| `created_at`| TIMESTAMPTZ | DEFAULT now()            |

### `policies` *(Phase 3)*

ABAC policy definitions evaluated at request time.

| Column       | Type    | Constraints             |
|--------------|---------|-------------------------|
| `id`         | UUID    | PRIMARY KEY             |
| `name`       | VARCHAR | UNIQUE, NOT NULL        |
| `effect`     | ENUM    | `ALLOW`, `DENY`         |
| `conditions` | JSONB   | NOT NULL                |

---

## Migration Tooling

Migrations are managed with [Flyway](https://flywaydb.org/) or [golang-migrate](https://github.com/golang-migrate/migrate), depending on the chosen API runtime. Migration files follow the naming convention:

```
V{version}__{description}.sql
```

### Running Migrations

```bash
# Apply all pending migrations
flyway migrate

# Check migration status
flyway info

# Rollback last applied migration (if reversible)
flyway undo
```

---

## Seeding

Seed scripts are intended for local development and CI environments only and must never be run against production.

```bash
# Run seed scripts (example using psql)
psql $DATABASE_URL -f seeds/001_default_roles.sql
psql $DATABASE_URL -f seeds/002_admin_user.sql
```

---

## Local Setup

```bash
# Start a local PostgreSQL instance via Docker
docker compose up -d postgres

# Apply migrations
flyway migrate -url=$DATABASE_URL
```

---

## Related

- [Server Layer](../server/README.md) — API that queries this database
- [Docs](../docs/README.md) — Architecture decisions and data model diagrams
