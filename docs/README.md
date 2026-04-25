# Docs

This directory contains all supplementary documentation for the Identity Service — architecture decision records (ADRs), API specifications, workflow diagrams, and design notes. It serves as the long-form reference for understanding why and how the system is built.

---

## Contents

```bash
docs/
├── adr/                     # Architecture Decision Records
│   ├── ADR-001-jwt-strategy.md
│   ├── ADR-002-rbac-model.md
│   └── ADR-003-abac-policy-engine.md
├── api/                     # API specifications (OpenAPI / Swagger)
│   └── openapi.yaml
├── diagrams/                # System and workflow diagrams
│   ├── architecture-overview.png
│   ├── registration-flow.png
│   ├── login-flow.png
│   └── rbac-model.png
└── decisions/               # Informal design notes and exploratory docs
```

---

## Architecture Decision Records (ADRs)

ADRs capture significant technical decisions, their context, and the reasoning behind them. Each ADR follows this structure:

- **Title** — short, imperative description of the decision
- **Status** — `Proposed` | `Accepted` | `Deprecated` | `Superseded`
- **Context** — the situation that prompted the decision
- **Decision** — what was decided
- **Consequences** — the trade-offs and implications

| ADR   | Title                               | Status   |
|-------|-------------------------------------|----------|
| ADR-001 | JWT strategy for session management | Proposed |
| ADR-002 | RBAC model and permission structure | Proposed |
| ADR-003 | ABAC policy engine selection        | Proposed |

---

## API Specification

The API is documented using the [OpenAPI 3.0](https://swagger.io/specification/) standard. The spec is maintained in `api/openapi.yaml` and can be rendered locally using Swagger UI or Redoc.

```bash
# Serve the API spec locally using Redoc (requires npx)
npx @redocly/cli preview-docs api/openapi.yaml
```

---

## Diagrams

Diagrams are stored as image files alongside their source (e.g., draw.io XML, Mermaid `.md` files) where applicable, so they can be updated as the system evolves.

---

## Contributing to Docs

- Add a new ADR when a significant architectural decision is made — even if the outcome seems obvious
- Keep diagrams up to date when workflows or data models change
- Prefer plain text and Markdown over binary formats where possible

---

## Related

- [Root README](../README.md) — High-level service overview
- [Server Layer](../server/README.md) — API implementation
- [DB Layer](../db/README.md) — Database schema and migrations
