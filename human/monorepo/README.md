# Monorepo Engineering & Code Quality Guidelines

## Scope

Bộ tài liệu này là baseline quy chuẩn kỹ thuật cho các dự án **Monorepo** của team, kết hợp cả Frontend (Next.js), Backend (NestJS), và các shared packages.

Mục tiêu là thống nhất cách tổ chức workspace, chia sẻ mã nguồn (shared code), quản lý task pipeline, kiểm soát dependency graph và tối ưu hóa build cache bằng **Turborepo** và **npm workspaces**.

Các rule trong pack này là **default baseline**. Project-specific requirements có thể override khi đã được thống nhất và documented trong project.

---

## Documents

| File | Nội dung |
|---|---|
| `turbo-conventions.md` | Cấu hình Turborepo (`turbo.json`), pipeline orchestration, task dependencies và cache hygiene |
| `frontend-code-conventions.md` | Coding conventions cho Next.js apps trong monorepo và cách consume `@repo/ui`, `@repo/types` |
| `backend-code-conventions.md` | Coding conventions cho NestJS apps trong monorepo, chia sẻ `@repo/database`, contracts |
| `architecture-principles.md` | Nguyên tắc boundary giữa `apps/` và `packages/`, chống phantom dependencies và circular dependency |
| `git-flow.md` | Quy trình nhánh, selective CI/CD (affected-only checks), và release flow cho từng package/app |
| `git-rules.md` | Quy ước commit scopes theo monorepo (`feat(web)`, `fix(api)`, `chore(ui)`), PR size controls |
| `development-checklist.md` | Checklist developer trước khi tạo PR trong môi trường monorepo |
| `pr-review-checklist.md` | Checklist reviewer kiểm soát blast radius và shared package breaking changes |
| `documentation-conventions.md` | Quy chuẩn Technical Documentation (`docs/tech/`), Release Notes và Changesets với Turborepo |

---

## Tooling Baseline

Repository Monorepo sử dụng bộ công cụ chuẩn:

- **Package Manager**: `npm` (sử dụng tính năng **npm workspaces**).
- **Monorepo Build System**: **Turborepo** (`turbo` by Vercel) quản lý pipeline execution và caching.
- **Languages & Frameworks**: TypeScript, Next.js (App Router), NestJS.
- **Code Quality & Git Hooks**: ESLint, Prettier, Husky, lint-staged, commitlint.
- **CI/CD**: GitHub Actions (tận dụng Turbo Remote Cache và `--filter=...[origin/develop]`).

---

## Workspace Structure Baseline

Mọi dự án Monorepo tuân theo cấu trúc phân định rạch ròi giữa **Applications** và **Shared Packages**:

```text
├── apps/
│   ├── web/                     # Next.js user-facing web application
│   ├── admin/                   # Next.js internal admin dashboard
│   ├── api/                     # NestJS backend HTTP API
│   └── worker/                  # NestJS background job worker (nếu có)
├── packages/
│   ├── ui/                      # Shared UI components & design tokens
│   ├── configs/                 # Shared configs (eslint, tsconfig, prettier)
│   ├── database/                # Database schema (Prisma/TypeORM) & migrations
│   ├── types/                   # Shared DTO contracts, API envelopes & validation schemas
│   └── utils/                   # Generic cross-platform utilities
├── package.json                 # Root workspace manifest
├── turbo.json                   # Turborepo task pipeline configuration
```

---

## Priority

Code Quality trong Monorepo được ưu tiên theo thứ tự:

1. **Security & Boundary Isolation** (Không leak secrets giữa các app, không phá vỡ boundaries giữa apps và packages).
2. **Maintainability & Dependency Health** (Không phantom dependencies, không circular workspace coupling).
3. **Build & Cache Performance** (Pipeline deterministic, cache-hit tối đa qua Turborepo).
4. **Correctness & Testing** (Selective testing cho affected packages và full integration test khi chạm shared packages).
