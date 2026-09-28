# Monorepo Engineering Guidelines (Dự án Cá nhân)

## Định hướng & Mục tiêu

Bộ tài liệu này là quy chuẩn kỹ thuật cho dự án **Monorepo Cá nhân (Personal / Indie Project)** sử dụng **Turborepo** và **npm workspaces**.

Khác với quy trình rườm rà của doanh nghiệp lớn (không Jira ticket, không approval đa tầng, không chờ đợi code review), bộ quy chuẩn này hướng đến:
- **Tốc độ & Tính tinh gọn (Agility & Velocity)**: Tối giản thủ tục hành chính, tập trung ship tính năng nhanh.
- **End-to-End Type Safety**: 1 developer kiểm soát toàn bộ từ Frontend (Next.js), Backend (NestJS), đến Database (`@repo/database`), đảm bảo đổi một chỗ là TypeScript báo đỏ ngay.
- **Sức mạnh Monorepo**: Tái sử dụng UI components (`@repo/ui`), data models (`@repo/types`), và chạy task siêu nhanh với Turborepo build cache.
- **Kiến trúc bền vững**: Đủ chuẩn để khi dự án lớn mạnh hoặc có thêm cộng sự tham gia, codebase vẫn cực kỳ sạch và dễ mở rộng.

---

## Danh mục Tài liệu

| File | Nội dung chính |
|---|---|
| [`turbo-conventions.md`](turbo-conventions.md) | Cấu hình Turborepo (`turbo.json`), pipeline tasks, cache hygiene và lệnh chạy nhanh |
| [`frontend-code-conventions.md`](frontend-code-conventions.md) | Chuẩn Next.js App Router, consume `@repo/ui`, Server Actions có auth/Zod, quản lý state |
| [`backend-code-conventions.md`](backend-code-conventions.md) | Chuẩn NestJS Module, tách `@repo/database`, Worker ngầm, ValidationPipe whitelist |
| [`architecture-principles.md`](architecture-principles.md) | 3 ranh giới bất biến giữa apps và packages, chống phantom dependencies, cô lập secret |
| [`git-flow.md`](git-flow.md) | Git flow tinh gọn cho solo dev, branch name theo scope (không task id), squash merge |
| [`git-rules.md`](git-rules.md) | Conventional Commits có scope (`feat(web):`, `fix(api):`), cấm stage nhầm app khác |
| [`development-checklist.md`](development-checklist.md) | Checklist tự kiểm tra code và chạy affected checks trước khi merge |
| [`pr-review-checklist.md`](pr-review-checklist.md) | Self-Review Checklist: Tự rà soát ranh giới kiến trúc, blast radius trước khi ship |
| [`documentation-conventions.md`](documentation-conventions.md) | Quy chuẩn Tech Docs (`docs/tech/`), Release Notes cá nhân và Changesets |

---

## Tooling Baseline

- **Package Manager**: `npm` (sử dụng **npm workspaces** mặc định, không cần cài thêm pnpm/yarn).
- **Monorepo Build System**: **Turborepo** (`turbo` by Vercel).
- **Apps**: `apps/web` (Next.js), `apps/admin` (Next.js nếu có), `apps/api` (NestJS), `apps/worker` (NestJS + BullMQ).
- **Packages**: `@repo/ui`, `@repo/database`, `@repo/types`, `@repo/configs`.
- **Quality**: ESLint, Prettier, Husky, Changesets.
EOF
