# Monorepo Development Checklist

Checklist này dành cho **Developer** trước khi tạo hoặc cập nhật Pull Request trong hệ thống **Monorepo**.

---

## 1. Requirement & Monorepo Scope

- [ ] Hiểu rõ Task và phạm vi tác động (chỉ ảnh hưởng `apps/*` hay tác động cả `packages/*`).
- [ ] Không sửa đổi các app/package không liên quan trong cùng một PR.
- [ ] Tên branch tuân thủ format có scope: `<prefix>/<task-id>-<scope>-<short-description>`.

---

## 2. Dependency & Workspace Integrity (Chống Phantom Dependencies)

- [ ] Mọi package được import đều đã khai báo trực tiếp trong `package.json` của app/package tương ứng (không phụ thuộc vào hoisting lên root).
- [ ] Không có circular dependencies giữa các package nội bộ.
- [ ] `packages/*` không import bất kỳ file nào từ `apps/*`.
- [ ] `apps/*` không import trực tiếp từ một app khác.
- [ ] Nếu thêm package ngoài mới, chỉ cài đặt vào đúng workspace cần dùng (`npm i <pkg> --workspace=<path>`), không cài vào root trừ tooling repo.

---

## 3. Security — Priority 1

- [ ] Không commit file `.env` hoặc để lộ credentials của Backend sang Frontend hoặc shared packages.
- [ ] Các biến môi trường mới đã được khai báo vào file `.env.example` của app tương ứng và có boot-time validation.
- [ ] Next.js Server Actions có kiểm tra authentication/session và validate input bằng Zod schema.
- [ ] NestJS Controllers có DTO validation; ứng dụng đã cấu hình `ValidationPipe` whitelist chống Mass Assignment.
- [ ] Không truyền dữ liệu nhạy cảm qua client bundle hoặc log server.

---

## 4. Turborepo & Build Cache Integrity

- [ ] Lệnh build toàn bộ affected packages chạy thành công cục bộ:
  ```bash
  npx turbo run build --filter=...[origin/develop]
  ```
- [ ] `typecheck` và `lint` đều pass trên mọi package bị ảnh hưởng:
  ```bash
  npx turbo run lint typecheck --filter=...[origin/develop]
  ```
- [ ] Nếu thêm task mới hoặc thay đổi đường dẫn build, `turbo.json` đã cập nhật đúng `outputs` và `dependsOn`.

---

## 5. Database & Shared Contracts (Nếu có)

- [ ] Thay đổi database schema nằm trong `packages/database` và đi kèm file migration tương ứng.
- [ ] File migration tuân thủ nguyên tắc bất biến (không sửa migration cũ đã merge).
- [ ] Contract API (`@repo/types`) được cập nhật đồng bộ, và cả Frontend lẫn Backend đều compile TypeScript thành công.

---

## 6. Git & PR Hygiene

- [ ] Commit messages tuân thủ đúng format có scope: `<type>(<scope>): <short-description>` (ví dụ: `feat(web): ...`, `fix(api): ...`).
- [ ] Kiểm tra `git status` và `git diff --staged` để chắc chắn không stage nhầm file từ các app khác.
- [ ] PR description chỉ rõ các app/package chịu ảnh hưởng và hướng dẫn test cụ thể.

---

## 7. Documentation & Changesets (Monorepo)

- [ ] **Living Tech Docs**: Nếu PR thay đổi ranh giới app/package, pipeline `turbo.json`, API contract (`@repo/types`) hoặc database schema (`@repo/database`), file tương ứng trong `docs/tech/` (`apps/`, `packages/`, `architecture/`) đã được cập nhật đồng bộ.
- [ ] **Multi-package Changeset**: Đã chạy `npx changeset`, tích chọn đúng tất cả các app/package bị thay đổi và chọn mức SemVer (`patch`/`minor`/`major`) tương ứng.
- [ ] **Release Notes**: Nếu PR là release milestone, đã tạo/cập nhật file trong `docs/release-notes/` kèm breakdown chi tiết từng app/package.
EOF
