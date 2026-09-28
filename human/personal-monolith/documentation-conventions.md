# Documentation & Release Conventions (Dự án Cá nhân & Freelancer)

Tài liệu này định nghĩa hệ thống **Technical Documentation** và **Release Notes** tinh gọn cho các dự án độc lập (**Monolith**) cá nhân hoặc freelancer.

---

## 1. Triết lý: Specs vs Tech Docs vs Release Notes

| Tiêu chí | Specs (Đặc tả yêu cầu / Ý tưởng) | Tech Docs (Tài liệu kỹ thuật) | Release Notes (Nhật ký bàn giao / phát hành) |
|---|---|---|---|
| **Câu hỏi cốt lõi** | *"Khách hàng cần gì / Ý tưởng là gì?"* | *"Ứng dụng hiện tại đang chạy như thế nào?"* | *"Phiên bản này vừa thay đổi gì, cần chú ý gì khi deploy?"* |
| **Bản chất** | Tin nhắn trao đổi, brief của khách, file Figma, ghi chú tính năng. | Hiện trạng kiến trúc thực tế, cấu trúc `features/` hoặc `modules/`, data flow, database schema. | Lịch sử bàn giao tính năng theo ngày hoặc theo version release. |
| **Tính cập nhật** | Xem lại khi làm tính năng. | **Living Documentation** (Cập nhật đồng thời khi sửa code). | Append-only (Ghi một lần tại thời điểm release/bàn giao). |
| **Nơi lưu trữ** | **Bên ngoài repo** (Notion, Figma, Email khách, Trello). **TUYỆT ĐỐI KHÔNG LƯU TRONG REPO**. | **Trong repo**: `docs/tech/` | **Trong repo**: `docs/release-notes/` |

> **[QUY TẮC CỐT LÕI] KHÔNG LƯU SPECS TRONG CODEBASE**
> 
> Trong repository **chỉ lưu trữ Tài liệu Kỹ thuật (`docs/tech/`)** và **Nhật ký phát hành (`docs/release-notes/`)**. 
> - **Tuyệt đối không lưu trữ Specs, PRD, User Stories, biên bản họp khách hàng bên trong repository**. 
> - Mọi tài liệu nghiệp vụ hãy lưu tại công cụ quản lý công việc riêng (Notion, Trello, Google Drive). 
> - Tech Docs chỉ tập trung mô tả **mã nguồn thực tế đang chạy** (Current Implementation).

---

## 2. Cấu trúc Thư mục Kỹ thuật Chuẩn (`docs/`)

```text
docs/
├── tech/
│   ├── README.md                   # Tổng quan hệ thống và các thành phần chính
│   ├── architecture/               # Sơ đồ luồng dữ liệu, request flow
│   ├── features/                   # Chi tiết kỹ thuật các tính năng phức tạp (auth, payment)
│   ├── database/                   # Schema database, các bảng quan trọng và lưu ý
│   ├── technologies/               # Danh sách công nghệ/thư viện đang dùng và lý do
│   └── integrations/               # Tích hợp bên ngoài (Stripe, S3, OAuth, SMTP)
└── release-notes/
    ├── README.md                   # Nhật ký tổng hợp các đợt phát hành
    ├── TEMPLATE.md                 # Mẫu ghi nhận release note
    └── YYYY-MM-DD-vX.X.X.md        # File release note từng phiên bản
```

---

## 3. Real-Time Documentation Rule (Viết ngắn gọn khi sửa code)

Dù làm một mình, việc ghi chép lại những điểm kỹ thuật quan trọng sẽ cứu bạn khi quay lại dự án sau vài tháng:

> **Quy tắc:** Khi bạn thêm một tính năng phức tạp, đổi logic database hoặc thêm tích hợp cổng thanh toán mới, hãy dành 2 phút viết tóm tắt vào `docs/tech/features/<tên-tính-năng>.md` trước khi merge vào `main`.

---

## 4. Mẫu Release Note Gọn gàng (Dùng cho Bàn giao Khách hàng)

Khi deploy hoặc bàn giao một đợt cập nhật cho khách hàng, tạo một file trong `docs/release-notes/`:

```markdown
# Release Note: [Version / Tên đợt cập nhật] — [YYYY-MM-DD]

## 1. Tổng quan Bản Phát hành
- **Ngày hoàn thành**: YYYY-MM-DD
- **Phiên bản**: vX.X.X
- **Mục tiêu**: Bàn giao tính năng thanh toán VNPay và cải thiện giao diện giỏ hàng.

## 2. Chi tiết các Thay đổi
### Tính năng Mới (Features)
- Tích hợp cổng thanh toán VNPay (hỗ trợ quét mã QR và thẻ ATM).
- Thêm trang lịch sử đơn hàng cho người dùng.

### Sửa lỗi & Tối ưu (Bug Fixes & Refactoring)
- Sửa lỗi không hiển thị hình ảnh trên trình duyệt Safari.
- Tối ưu tốc độ tải trang chủ (giảm 40% dung lượng bundle).

## 3. Hướng dẫn Triển khai & Cấu hình (Deployment Checklist)
- [ ] Bổ sung biến môi trường vào server: `VNPAY_TMN_CODE`, `VNPAY_HASH_SECRET`.
- [ ] Chạy migration database: `npx prisma migrate deploy` (hoặc TypeORM tương đương).
- [ ] File migration liên quan: `20260928_add_payment_records.sql`.
```
EOF
