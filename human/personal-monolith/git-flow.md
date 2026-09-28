# Git Flow & Collaboration (Dự án Cá nhân & Freelancer)

> Quy trình Git Flow tinh gọn, tốc độ cao dành cho **Developer cá nhân hoặc Freelancer** làm việc trên các repository độc lập (Next.js hoặc NestJS).

---

## 1. Luồng Phát triển Tinh gọn (Solo / Freelance Flow)

Không rườm rà thủ tục vé task Jira, không chờ đợi code review từ cấp trên. Quy trình hướng đến việc **ship nhanh, code an toàn và lịch sử commit sạch sẽ**:

```text
Yêu cầu / Tính năng cần làm
   ↓
Tạo Short-lived Branch (ngắn hạn)
   ↓
Phát triển & Test cục bộ (npm run lint && npm run test)
   ↓
Commit (Conventional Commits chuẩn hóa)
   ↓
Self-Review (Tự rà soát lại diff)
   ↓
Squash & Merge vào main (hoặc develop)
   ↓
Xóa branch & Deploy / Bàn giao
```

---

## 2. Quy ước Đặt tên Nhánh Tinh gọn (Branch Naming)

Không sử dụng Task ID của Jira. Tên nhánh ngắn gọn, phản ánh trực tiếp tính năng hoặc lỗi đang xử lý:

### Format:
```text
<type>/<short-description>
```

### Các Prefix thông dụng:
- `feat/`: Thêm tính năng mới (ví dụ: `feat/google-login`, `feat/stripe-checkout`)
- `fix/`: Sửa lỗi (ví dụ: `fix/cart-quantity-zero`, `fix/login-token-refresh`)
- `refactor/`: Tối ưu hóa mã nguồn (ví dụ: `refactor/extract-auth-hook`)
- `chore/`: Cập nhật cấu hình, thư viện (ví dụ: `chore/upgrade-next15`)

### Ví dụ chuẩn:
```text
feat/add-user-avatar-upload
feat/integrate-vnpay-payment
fix/prevent-duplicate-order-submission
chore/setup-eslint-prettier
```

---

## 3. Quản lý Nhánh Chính

Tùy vào quy mô dự án hoặc yêu cầu từ khách hàng:

### Mô hình 1: 1 Nhánh Duy nhất (`main`) — Khuyến nghị cho dự án cá nhân
- Mọi nhánh tính năng sau khi test xong đều merge trực tiếp vào `main`.
- `main` luôn là bản ổn định nhất và kết nối thẳng với hệ thống auto-deploy (Vercel, Railway, Render, VPS).
- Đơn giản tối đa, không tốn thời gian chuyển nhánh qua lại.

### Mô hình 2: 2 Nhánh (`develop` + `main`) — Dành cho dự án có môi trường Staging/UAT cho khách test
- `develop`: Nơi merge các tính năng hàng ngày để khách hàng xem thử trên staging.
- `main`: Chỉ merge từ `develop` khi khách hàng đã nghiệm thu để deploy production.
- **Nếu có hotfix trực tiếp trên `main`**: Merge ngay ngược lại vào `develop` để tránh mất code.

---

## 4. Chiến lược Merge: Luôn dùng "Squash and Merge"

Để repository của bạn trông chuyên nghiệp nhất trong mắt khách hàng và đối tác:

- **Bắt buộc dùng Squash and Merge**: Gộp toàn bộ các commit nhỏ lẻ ("fix bug", "test thử", "update css") thành **1 commit duy nhất mang thông điệp rõ ràng** trên nhánh chính.
- Khi bàn giao repo cho khách hàng, lịch sử git sẽ vô cùng sạch đẹp và đáng tin cậy.
EOF
