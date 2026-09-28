# Monorepo Development Checklist (Dự án Cá nhân)

Checklist này dành cho bạn tự kiểm tra nhanh trước khi merge code vào nhánh chính trong **Monorepo Cá nhân**.

---

## 1. Phạm vi & Tên nhánh

- [ ] Tên nhánh ngắn gọn, đúng format có scope: `<type>/<scope>-<short-description>` (ví dụ: `feat/web-auth`, `fix/api-jwt`).
- [ ] Không commit dính các thay đổi thử nghiệm ở các app không liên quan.

---

## 2. Ranh giới Gói & Dependencies (Chống Lỗi Hoisting)

- [ ] Thư viện bên ngoài được cài vào đúng workspace của app sử dụng nó (`npm i <pkg> --workspace=apps/<app>`), không cài vào root.
- [ ] Mọi thư viện được import đều đã khai báo trực tiếp trong `package.json` của app đó (không dùng lậu dependency của app khác do npm hoisting).
- [ ] `packages/*` không import bất kỳ file nào từ `apps/*`.
- [ ] `apps/*` không import chéo nhau.

---

## 3. An ninh & Bảo vệ Secret

- [ ] Không vô tình commit `.env` hoặc để lộ Secret của Backend (Database URL, Stripe Secret, AI API Keys) sang Frontend hoặc shared packages.
- [ ] Biến môi trường mới đã được ghi chú vào `.env.example` của app tương ứng.
- [ ] Next.js Server Actions có kiểm tra authentication và validate Zod schema.
- [ ] NestJS Controllers có DTO validation và được bảo vệ bởi ValidationPipe whitelist.

---

## 4. Kiểm tra Nhanh với Turborepo (Trước khi Merge)

Chạy lệnh kiểm tra tự động cho các phần bị ảnh hưởng:
```bash
# 1. Typecheck & Lint
npx turbo run lint typecheck --filter=...[origin/main]

# 2. Build thử nghiệm
npx turbo run build --filter=...[origin/main]
```
- [ ] Cả 2 lệnh trên đều PASS 100%, không có lỗi đỏ.

---

## 5. Database & API Contract (Nếu có)

- [ ] Nếu sửa model database (`packages/database`), đã chạy CLI sinh migration tương ứng:
  ```bash
  npm run db:migrate:dev --workspace=@repo/database
  ```
- [ ] Nếu sửa API payload, đã cập nhật schema trong `@repo/types` và cả Frontend lẫn Backend đều compile TypeScript thành công.

---

## 6. Git Hygiene & Changeset

- [ ] `git status` và `git diff --staged` sạch sẽ, chỉ chứa file của tính năng đang làm.
- [ ] Commit message có scope: `<type>(<scope>): <short-description>`.
- [ ] Nếu thay đổi kiến trúc hoặc logic lớn, đã cập nhật nhanh tài liệu tương ứng trong `docs/tech/`.
- [ ] Đã chạy `npx changeset` nếu đây là thay đổi tính năng/bugfix cần theo dõi version.
EOF
