# Development Checklist (Dự án Cá nhân & Freelancer)

Checklist này dành cho bạn **tự kiểm tra nhanh** trước khi hoàn thành một tính năng hoặc trước khi merge code vào nhánh chính.

---

## 1. Yêu cầu & Tính đúng đắn

- [ ] Tính năng hoạt động đúng như mong đợi và đáp ứng nhu cầu của người dùng/khách hàng.
- [ ] Đã thử nghiệm các trường hợp biên (nhập sai dữ liệu, để trống ô, click nút nhiều lần).
- [ ] Tên nhánh ngắn gọn, rõ nghĩa: `<type>/<short-description>` (không cần task ID).

---

## 2. Bảo mật Cốt lõi (Tuyệt đối không bỏ qua)

- [ ] Không hardcode secret, API key, token hoặc mật khẩu trong source code.
- [ ] Biến môi trường mới đã được khai báo vào `.env.example` kèm mô tả rõ ràng; file `.env` thực tế đã được gitignore.
- [ ] **Next.js**: Server Actions (`'use server'`) có xác thực session và parse dữ liệu bằng Zod schema.
- [ ] **NestJS**: Controller có DTO validation; ứng dụng đã bật Global `ValidationPipe` whitelist chống Mass Assignment.
- [ ] Không leak dữ liệu nhạy cảm của khách hàng qua client bundle hoặc log server.

---

## 3. Kiến trúc & Clean Code

- [ ] **Next.js**: Business logic nằm trong `features/<feature-name>/`, không nhồi nhét code vào component hiển thị.
- [ ] **NestJS**: Controller thin, logic nghiệp vụ nằm trong Service.
- [ ] Không lạm dụng `any`; TypeScript types được định nghĩa rõ ràng.
- [ ] Đã xóa sạch `console.log`, `debugger`, code thử nghiệm tạm thời.

---

## 4. Cơ sở dữ liệu & Migrations (Nếu có)

- [ ] Mọi thay đổi schema database đều được thực hiện qua file Migration (Prisma / TypeORM).
- [ ] Tuân thủ nguyên tắc bất biến: Tuyệt đối không sửa đổi file migration cũ đã chạy trên production của khách hàng. Luôn tạo migration mới để roll-forward.

---

## 5. Kiểm tra Tự động Cục bộ (Local Verification)

- [ ] `npm run lint` PASS, không có warning hoặc error nghiêm trọng.
- [ ] `npm run build` PASS, ứng dụng biên dịch thành công không lỗi syntax/type.

---

## 6. Tài liệu & Bàn giao (Docs & Release Notes)

- [ ] Nếu thay đổi kiến trúc hoặc endpoint mới, cập nhật nhanh tài liệu tương ứng trong `docs/tech/`.
- [ ] Cập nhật nhật ký phát hành trong `docs/release-notes/` hoặc chạy `npx changeset` nếu dự án có quy trình bàn giao theo phiên bản.
EOF
