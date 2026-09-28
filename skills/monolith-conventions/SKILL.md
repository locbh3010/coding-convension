---
name: monolith-conventions
description: Coding, architecture, and quality standards for single/monolith Next.js and NestJS repositories. Use when writing, reviewing, refactoring, or designing code in a monolith repository.
---

# Monolith Coding & Architecture Conventions

This skill defines the non-negotiable coding and architecture standards for single/monolith repositories (Next.js Frontend and NestJS Backend).

---

## 1. Core Principles & Priority

Code Quality priority:
1. **Security**
2. **Maintainability**
3. **Performance**
4. **Correctness & Testing**

> Rule: *Prefer clear, predictable, and maintainable code over clever or unnecessarily complex code.*

---

## 2. Frontend Conventions (Next.js App Router)

- **Structure**: Feature-based directory organization:
  - `features/<feature-name>/`: Contains `components/`, `hooks/`, `services/`, `types/`, `utils/`, `index.ts`.
  - Generic root folders (`components/`, `hooks/`, `utils/`) are strictly for cross-feature generic logic.
- **Server Components vs Client Components**:
  - Server-First by default: Layouts and Pages fetch data on the server.
  - `'use client'` is restricted to interactive leaves (Buttons, Dropdowns, Forms).
- **Server Actions (`'use server'`) Security**:
  - Always verify authentication / session at the top of the action.
  - Always validate input arguments with a Zod schema before executing business logic. Never trust client payload.
- **State Tiering**:
  - **Server State**: TanStack Query (caching, synchronization, mutations).
  - **URL State**: `nuqs` for search params, filters, pagination.
  - **Local UI State**: `useState` / `useReducer`.
  - **Global Client State**: `zustand` (only for complex shared UI states like drawers/wizards). Do NOT use React Context for rapidly changing states.
- **Forms**: Use `react-hook-form` + `@hookform/resolvers/zod`.

---

## 3. Backend Conventions (NestJS)

- **Structure**: Domain/Feature Module-based organization:
  - `src/modules/<domain>/`: Contains `controllers/`, `services/`, `dto/`, `entities/`.
  - `src/common/`: Cross-cutting decorators, filters, interceptors, guards.
  - `src/configs/`: Application and infrastructure setup with boot-time validation.
- **Thin Controllers**:
  - Controllers only receive requests, bind/validate DTOs, invoke Services, and return responses.
  - Zero database access and zero complex business logic in Controllers.
- **Security & Mass Assignment Prevention**:
  - Mandatory global `ValidationPipe` with `whitelist: true, forbidNonWhitelisted: true, transform: true`.
- **Background Tasks**:
  - Async jobs > 200ms (emails, file processing, bulk exports) must be queued via BullMQ/Redis. Never run unhandled heavy promises in request handlers.

---

## 4. Standard Response Envelopes & Error Codes

All APIs communicating with clients must adhere to uniform envelopes:

### Success Response:
```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "totalPages": 5
  }
}
```

### Error Response:
```json
{
  "success": false,
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "User-friendly localized message",
    "details": []
  }
}
```
- `code`: Mandatory `UPPER_SNAKE_CASE` domain error identifier.

---

## 5. Database & Migrations

- **Migration Immutability**: Once a migration file is merged into `develop` or `main`, **it must NEVER be modified**. Always create a new migration to roll forward.
- **PR Pairing**: Any PR modifying entities/schema must include the corresponding CLI-generated migration file.
- **CLI Only**: Never run manual DDL queries directly on production/staging databases.

---

## 6. Git & Commit Conventions

- **Branch Naming**: `<prefix>/<task-id>-<short-description>` (e.g. `feature/phi-101-add-login`).
- **Commit Format**: Conventional Commits: `<type>(<scope>): <short-description>` (e.g. `feat(auth): add google oauth`).
- **Merge Strategy**: Always use **Squash and Merge** for feature branches into `develop`.
- **Hotfix Sync-back**: When merging a `hotfix/*` into `main`, immediately sync/merge back into `develop`.
- **Hygiene**: No `console.log`, no hardcoded secrets, no unverified `git add .`.
EOF
