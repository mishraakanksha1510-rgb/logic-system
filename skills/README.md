# Skills

This directory contains reusable skill definitions — structured, domain-specific knowledge and workflow guides used to inform development practices within this repository. Skills encapsulate proven patterns, conventions, and step-by-step processes relevant to the Identity Service.

---

## What Is a Skill?

A skill is a focused, self-contained document that describes how to approach a specific type of task within this codebase. Skills are not tutorials — they are concise references that establish consistent practices across the team.

Examples of skills this repo may define:

- How to author and apply a database migration
- How to write and register a new API endpoint
- How to define and enforce an RBAC permission
- How to write an ABAC policy
- How to structure integration tests for auth flows

---

## Structure

```bash
skills/
├── add-migration/           # How to create and apply a new DB migration
│   └── SKILL.md
├── add-endpoint/            # How to add a new versioned API endpoint
│   └── SKILL.md
├── define-role/             # How to create a role and bind permissions (RBAC)
│   └── SKILL.md
├── write-abac-policy/       # How to author and register an ABAC policy
│   └── SKILL.md
└── write-integration-test/  # How to write integration tests for auth flows
    └── SKILL.md
```

---

## Using a Skill

1. Navigate to the relevant skill directory
2. Read the `SKILL.md` file for the step-by-step guide
3. Follow the prescribed conventions — deviations should be discussed and, if accepted, reflected back into the skill document

---

## Adding a New Skill

When a recurring task or pattern is identified that would benefit from documentation:

1. Create a new subdirectory under `skills/` with a short, descriptive name (kebab-case)
2. Add a `SKILL.md` file following this structure:
   - **Purpose** — what task this skill covers
   - **When to use** — the triggers or scenarios that call for this skill
   - **Steps** — ordered, actionable instructions
   - **Examples** — concrete code or config snippets
   - **Pitfalls** — common mistakes to avoid

---

## Related

- [Root README](../README.md) — High-level service overview
- [Docs](../docs/README.md) — Architecture decisions and API specifications
