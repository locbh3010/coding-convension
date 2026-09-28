---
name: personal-monolith-conventions
description: Coding, architecture, and git standards for personal, solo, and freelancer projects in single/monolith Next.js and NestJS repositories. Focuses on speed, maintainability, and clean code without corporate bureaucracy.
---

# Personal Monolith Conventions (Solo & Freelancer Projects)

This skill defines the coding, architecture, and workflow standards for **personal, solo, and freelance projects** built as single/monolith repositories (Next.js or NestJS).

---

## 1. Core Principles

- **Speed & Practicality**: No Jira tickets, no task-id requirements, no corporate approval bureaucracy.
- **Maintainability & Client-Ready Quality**: Clean code, thin controllers, feature-based directories, and clear technical documentation so the codebase is easy to hand off or revisit after months.
- **Strict Safety Controls**: Zero secrets in source code, boot-time environment validation, NestJS `ValidationPipe` whitelist against mass assignment, Next.js Server Actions with auth & Zod validation, and database migration immutability.

---

## 2. Frontend Conventions (Next.js App Router)

- **Structure**: Feature-based directory organization (`features/<feature-name>/`).
  - Keep components, hooks, services, and types together by feature.
- **Server Components vs Client Components**:
  - Server-First by default.
  - `'use client'` only on interactive leaf components (Buttons, Modals, Form inputs).
- **Server Actions (`'use server'`) Security**:
  - Always verify session/authentication at the start of the function.
  - Always validate input arguments with Zod schemas.
- **State Tiering**:
  - **Server State**: TanStack Query (caching, synchronization).
  - **URL State**: `nuqs` for search params and filters.
  - **Local State**: `useState` / `useReducer`.
  - **Global Client State**: `zustand` (only for complex shared UI states).
- **Forms**: `react-hook-form` + `@hookform/resolvers/zod`.

---

## 3. Backend Conventions (NestJS)

- **Structure**: Domain/Feature Module organization (`src/modules/<domain>/`).
- **Thin Controllers**:
  - Controllers only receive requests, bind DTOs, invoke Services, and return responses.
  - No database logic or business rules inside Controllers.
- **Mass Assignment Prevention**:
  - Mandatory global `ValidationPipe` with `whitelist: true, forbidNonWhitelisted: true, transform: true`.
- **Response Envelopes**:
  - Success: `{ success: true, data: ..., meta: ... }`
  - Error: `{ success: false, error: { code, message, details } }`

---

## 4. Database & Migrations

- **Migration Immutability**: Merged migration files must NEVER be modified. Always create a new migration to roll forward (prevents corrupting production or client databases).
- **CLI Only**: Run schema changes through migration tools (Prisma / TypeORM), never direct manual SQL alterations.

---

## 5. Git Flow for Solo Developers & Freelancers

- **Branch Naming**: Concise and scope-focused without task IDs:
  `<type>/<short-description>` (e.g. `feat/google-login`, `fix/cart-quantity`, `chore/setup-tailwind`).
- **Conventional Commits**:
  - `feat: add user profile editing`
  - `fix: correct price calculation on checkout`
  - `refactor: extract reusable card component`
- **Merge Strategy**: Always use **Squash and Merge** into `main` to maintain a clean, linear git history.
- **Pre-Merge Self-Check**: Verify local lint and build pass before merging (`npm run lint && npm run build`).

---

## 6. Technical Documentation

- **No Specs in Repository**: Specs, user stories, client chats, and wireframes belong outside the codebase (Notion, Figma, Trello).
- **Living Tech Docs**: Important technical implementations and architecture are recorded in `docs/tech/`.
- **Release Notes**: Handover notes and version changelogs are maintained in `docs/release-notes/`.
EOF
