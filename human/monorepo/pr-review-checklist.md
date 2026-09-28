# Monorepo Pull Request Review Checklist

Checklist này dành cho **Reviewer** trước khi Approve PR trong hệ thống **Monorepo**.

Reviewer tập trung vào việc bảo vệ ranh giới kiến trúc (Boundaries), tính toàn vẹn của dependency graph và kiểm soát Blast Radius.

---

## 1. Kiểm soát Blast Radius & Phạm vi tác động

- [ ] **Xác định rõ Blast Radius**:
  - Nếu PR chỉ chạm vào một app trong `apps/*`: Rủi ro thấp, tập trung review business logic của app đó.
  - Nếu PR chạm vào `packages/ui`: Đã kiểm tra UI không bị vỡ trên các app tiêu thụ (`web`, `admin`) chưa?
  - Nếu PR chạm vào `packages/types` hoặc `packages/database`: Đã xác nhận tính tương thích ngược và full typecheck toàn repo chưa?
- [ ] PR có bị gộp quá nhiều thay đổi không liên quan giữa các app độc lập không?

---

## 2. Kiểm tra Ranh giới Kiến trúc (Boundary Violations)

- [ ] **Không vi phạm chiều phụ thuộc**:
  - Có file nào trong `packages/*` import từ `apps/*` không? (Tuyệt đối không được phép).
  - Có app nào trong `apps/*` import chéo từ app khác không?
- [ ] **Chống Phantom Dependencies**:
  - Các thư viện mới được import có được khai báo tường minh trong `package.json` của workspace đó không?
- [ ] **Tránh rò rỉ Business Logic vào Shared Packages**:
  - `@repo/ui` có bị nhồi logic gọi API hoặc business của một app cụ thể không?

---

## 3. An ninh & Cách ly Secret

- [ ] Có bất kỳ biến môi trường bí mật nào của Backend bị đưa nhầm vào `packages/*` hoặc expose ra `NEXT_PUBLIC_*` của Frontend không?
- [ ] Server Actions mới trong Next.js có kiểm tra session/auth và parse Zod schema không?
- [ ] NestJS Controllers có sử dụng DTO validation và được bảo vệ bởi ValidationPipe whitelist không?
- [ ] Có commit nhầm file `.env` hoặc credentials cục bộ không?

---

## 4. Database & Shared Contracts

- [ ] Nếu có thay đổi database model:
  - Có file migration đi kèm trong cùng PR không?
  - File migration có tuân thủ nguyên tắc bất biến (không sửa migration cũ đã merge) không?
- [ ] Thay đổi trong `@repo/types` có làm vỡ type ở các app consuming không?

---

## 5. Turborepo Caching & CI Status

- [ ] CI pipeline của Turborepo (`turbo run lint typecheck test build`) đã PASS toàn bộ affected packages chưa?
- [ ] Có thay đổi nào trong `turbo.json` làm hỏng cache pipeline không?

---

## 6. Documentation & Changesets (Monorepo)

- [ ] **Living Tech Docs Verification**: Diff của `docs/tech/` có phản ánh đúng và đủ các thay đổi trong code (`apps/`, `packages/`, contracts) không? (Block nếu thiếu cập nhật tài liệu kỹ thuật).
- [ ] **Changeset Multi-package Accuracy**: File `.changeset/*.md` có chọn đúng danh sách app/package bị sửa đổi và mức SemVer hợp lý không?
- [ ] **Release Notes Completeness**: Nếu là Release PR, đã có breakdown chi tiết cho từng app/package và hướng dẫn migration chưa?

---

## 7. Tiêu chí Phê duyệt (Approve Criteria)

PR chỉ được Approve khi:
1. Không còn vi phạm boundary hoặc phantom dependency.
2. An ninh và Secret Isolation được bảo toàn.
3. Toàn bộ CI checks của Turborepo pass.
4. Đối với thay đổi trong `packages/database` hoặc `packages/types`, đã có sự đồng thuận từ Tech Lead hoặc đại diện cả Frontend và Backend.
EOF
