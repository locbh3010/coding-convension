---
name: monorepo-conventions
description: Coding, architecture, and workspace management standards for Monorepo repositories using Turborepo and npm workspaces. Use when developing, reviewing, or configuring monorepo applications and packages.
---

# Monorepo Coding & Architecture Conventions (Turborepo + npm Workspaces)

This skill defines the standards for multi-package and multi-application repositories managed via **Turborepo** and **npm workspaces**.

---

## 1. Workspace Topology

```text
├── apps/
│   ├── web/                     # Next.js customer web application
│   ├── admin/                   # Next.js admin dashboard
│   ├── api/                     # NestJS backend HTTP API
│   └── worker/                  # NestJS background queue worker
├── packages/
│   ├── ui/                      # Shared UI components & design tokens (@repo/ui)
│   ├── database/                # Schema (Prisma/TypeORM) & migrations (@repo/database)
│   ├── types/                   # Contracts, DTOs & Zod schemas (@repo/types)
│   └── configs/                 # Shared ESLint, Prettier, TSConfig (@repo/configs)
├── package.json                 # Root workspace manifest
└── turbo.json                   # Turborepo task pipeline configuration
```

---

## 2. Hard Boundary Rules

1. **Packages Never Import Apps**: `packages/*` must never import anything from `apps/*`.
2. **Apps Never Import Apps**: `apps/web` must never import from `apps/admin` or `apps/api`. Any shared logic must live in `packages/*`.
3. **No Circular Dependencies**: Circular dependencies between workspace packages are strictly prohibited.
4. **Secret Isolation**: Backend secrets (`DATABASE_URL`, `JWT_SECRET`) must never exist in root `.env` or shared packages (`@repo/ui`, `@repo/types`). Only `NEXT_PUBLIC_*` variables are allowed on client bundles.

---

## 3. Anti-Phantom Dependencies

- `npm workspaces` hoists dependencies to root `node_modules`.
- **Hard Rule**: Every app and package **must explicitly declare every direct dependency it imports** in its own `package.json`.
- Never rely on root hoisting. Phantom dependencies break Turborepo cache calculation and cause isolated Docker/CI builds to fail.

---

## 4. Turborepo Configuration & Caching (`turbo.json`)

- **Topological Dependencies (`^build`)**:
  - Always use `"dependsOn": ["^build"]` for build/typecheck tasks to guarantee internal package dependencies are compiled first.
- **Cache Inputs & Outputs**:
  - Declare outputs explicitly: Next.js (`[".next/**", "!.next/cache/**"]`), TS packages (`["dist/**"]`).
  - Declare env variables that affect build output in `globalEnv` or task-level `env`.
- **No-Cache Rule**:
  - Dev/watch tasks (`dev`, `start:dev`) must set `"cache": false` and `"persistent": true`.

---

## 5. Centralized Database Package (`@repo/database`)

- Database schemas and migration files **must reside exclusively in `packages/database`**, never inside `apps/api`.
- Both `apps/api` and `apps/worker` depend on `"@repo/database": "*"`.
- Migrations are executed via package CLI:
  ```bash
  npm run db:migrate:dev --workspace=@repo/database
  # or in CI:
  npx turbo run db:migrate
  ```
- **Migration Immutability**: Merged migration files must never be modified. Always roll forward.

---

## 6. Git Flow & Monorepo Scopes (Personal Projects)

- **Branch Naming**: Scope-based without enterprise task IDs:
  `<type>/<scope>-<short-description>` (e.g. `feat/web-checkout`, `fix/api-jwt`, `chore/ui-button`, `feat/worker-ai-pipeline`).
- **Conventional Commits**: Scope must be an app/package name:
  - `feat(web): add cart drawer`
  - `fix(api): handle token expiration`
  - `refactor(ui): update button tokens`
  - `chore(database): add index to order table`
- **Blast Radius Awareness**:
  - Changes to `apps/*`: Low risk (isolated).
  - Changes to `packages/types` or `packages/database`: Critical risk. Must run `turbo run typecheck test` across all consuming apps.
- **Affected-Only Check**:
  ```bash
  npx turbo run lint typecheck test build --filter=...[origin/main]
  ```
- **Self-Merge Strategy**: Always use **Squash and Merge** into `main` to preserve a clean, linear git history.

