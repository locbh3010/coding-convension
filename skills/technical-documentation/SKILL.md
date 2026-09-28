---
name: technical-documentation
description: Standards for authoring and maintaining Technical Documentation (docs/tech/), Release Notes (docs/release-notes/), and automated Changesets. Enforces the Real-Time Documentation rule.
---

# Technical Documentation, Release Notes & Changesets Skill

This skill defines the standards for creating, updating, and maintaining living technical documentation and release management artifacts.

---

## 1. Distinction: Tech Docs vs Specs vs Release Notes

| Area | Purpose | Content | Storage Location |
|---|---|---|---|
| **Specs** | What the product *requires* | Business rules, user stories, acceptance criteria | **OUTSIDE repo** (Jira, Confluence, Linear). **NEVER store specs in the repository**. |
| **Tech Docs** (`docs/tech/`) | How the system is *actually implemented* | Architecture, data flow, module boundaries, error handling, constraints | **Inside repo**: `docs/tech/` (Living docs) |
| **Release Notes** (`docs/release-notes/`) | What changed, when, why, and impact | Metadata, bug fixes, features, migrations, env vars, Changeset ID | **Inside repo**: `docs/release-notes/` (Append-only) |

> **[HARD RULE] NEVER STORE SPECS IN THE REPOSITORY**
> 
> The codebase must only store **Technical Documentation (`docs/tech/`)** and **Release Notes (`docs/release-notes/`)**.
> - Do NOT create `docs/specs/` or commit PRD / User Stories / Acceptance Criteria inside the repository.
> - Specs belong strictly to external PM tools (Jira, Confluence, Linear, Notion, Figma).
> - Tech Docs describe the **Current Implementation** (how code actually runs).

---

## 2. Real-Time Documentation Rule

> **MANDATORY**: Whenever source code changes in a way that affects architecture, directory structure, feature implementation, API contracts, database schemas, or technical decisions, **the corresponding Tech Doc in `docs/tech/` must be updated within the same Pull Request**.

- Outdated documentation is worse than no documentation.
- Reviewers must block PRs if code behavior changed without updating documentation.

---

## 3. Directory Layout (`docs/`)

```text
docs/
├── tech/
│   ├── README.md                   # System navigation & component map
│   ├── architecture/               # High-level architecture, topology, data flow
│   ├── features/                   # Implementation details of key features (auth, cart)
│   ├── database/                   # Schemas, relations, indexing, migration policies
│   ├── technologies/               # Standardized libraries, rationale, and caveats
│   ├── integrations/               # 3rd-party services (payments, storage, OAuth)
│   └── decisions/                  # Architecture Decision Records (ADRs)
└── release-notes/
    ├── README.md                   # Archive index of releases
    ├── TEMPLATE.md                 # Standard release note template
    └── YYYY-MM-DD-vX.X.X.md        # Per-release changelog record
```

---

## 4. How to Write a Feature Technical Document

When documenting a feature (`docs/tech/features/<feature-name>.md`), adhere to this structure:
1. **Responsibility**: What technical concerns this feature handles (and what it explicitly does not).
2. **Components & Modules**: Concrete file paths in Frontend and Backend.
3. **Data Flow**: Step-by-step trace from UI trigger to database mutation. Use **Mermaid sequence diagrams** where helpful.
4. **State Management & Caching**: TanStack Query keys, stores, cache invalidation.
5. **Security & Authorization**: Guard usage, role checks, validation rules.
6. **Edge Cases & Known Constraints**: Concurrency, rate limits, known performance ceilings.

---

## 5. Changesets Workflow (`@changesets/cli`)

Changesets must be treated as a first-class part of the engineering/release workflow:

### Developer Step:
```bash
npx changeset
```
1. Select modified packages (in Monorepo) or version bump type (in Monolith): `patch`, `minor`, or `major`.
2. Write a clear summary of the change.
3. Commit the resulting `.changeset/*.md` file into the PR.

### Release Automation:
- In CI/CD on the main branch:
  ```bash
  npx changeset version
  ```
  Automatically bumps `package.json` versions and updates `CHANGELOG.md`.

---

## 6. Release Note Canonical Format

Each deployment must create a record in `docs/release-notes/YYYY-MM-DD-vX.X.X.md`:
- **Metadata**: Date, version tag, Jira tasks, author, PR link, Changeset ID.
- **Summary**: 1-2 sentence overview of release goal.
- **What Changed**: Features, Bug fixes (with root cause), Refactors.
- **Blast Radius & Impact**: Affected apps/packages, breaking changes, user-facing UI changes.
- **Deployment Checklist**: New environment variables (`.env.example`), required DB migrations to execute, cache clearing tasks.
- **Verification**: Test evidence, CI status.
EOF
