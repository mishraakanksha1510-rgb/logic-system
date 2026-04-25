# Client — UI Layer

This directory contains the frontend application for the Identity Service. It provides the user-facing interface for registration, login, and profile management, as well as an admin dashboard for managing users, roles, and permissions.

---

## Responsibilities

- Render user-facing workflows (registration, login, password reset, profile management)
- Provide an admin dashboard for user and role administration
- Communicate with the API layer over HTTP/REST
- Handle client-side token storage, refresh logic, and session expiry

---

## Structure

```bash
client/
├── public/                  # Static assets (favicon, fonts, icons)
└── src/
    ├── pages/               # Top-level route pages
    │   ├── auth/            # Login, Register, Password Reset, Verify Email
    │   ├── profile/         # User profile view and edit
    │   └── admin/           # Admin dashboard (users, roles, permissions)
    ├── components/          # Reusable UI components
    ├── services/            # API client wrappers (auth, user, admin)
    ├── hooks/               # Custom React hooks
    ├── store/               # Client-side state management
    └── utils/               # Helpers (token parsing, validators, formatters)
```

---

## Key Pages

| Page                  | Path                  | Description                              |
|-----------------------|-----------------------|------------------------------------------|
| Register              | `/register`           | New user account creation form           |
| Login                 | `/login`              | Authentication form                      |
| Verify Email          | `/verify-email`       | Email verification landing page          |
| Password Reset        | `/password-reset`     | Request and confirm password reset       |
| Profile               | `/profile`            | View and edit the current user's profile |
| Admin — Users         | `/admin/users`        | List, search, and manage users           |
| Admin — Roles         | `/admin/roles`        | Create and manage roles and permissions  |

---

## Tech Stack

| Concern          | Technology              |
|------------------|-------------------------|
| Framework        | React / Next.js         |
| State Management | Context API / Zustand   |
| HTTP Client      | Axios / Fetch API       |
| Styling          | Tailwind CSS / CSS Modules |
| Testing          | Jest + React Testing Library |

---

## Getting Started

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Run tests
npm test

# Build for production
npm run build
```

---

## Environment Variables

| Variable            | Description                          |
|---------------------|--------------------------------------|
| `NEXT_PUBLIC_API_URL` | Base URL of the Identity Service API |

Create a `.env.local` file at the root of this directory and populate the variables above before running the application.

---

## Related

- [API Layer](../server/README.md) — Backend REST API
- [DB Layer](../db/README.md) — Database schema and migrations
- [Docs](../docs/README.md) — Architecture decisions and API specifications
