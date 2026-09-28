# Pre-Merge & Handoff Self-Review Checklist (Dự án Cá nhân & Freelancer)

Checklist này dành cho bạn **tự rà soát (Self-Review)** trước khi merge code vào nhánh chính hoặc trước khi bàn giao (deploy / handover) sản phẩm cho khách hàng.

---

## 1. Rà soát Rủi ro & Tác động (Blast Radius)

- [ ] Thay đổi này có ảnh hưởng đến các tính năng đang hoạt động bình thường khác của ứng dụng không?
- [ ] Giao diện (UI) đã được kiểm tra trên cả màn hình Desktop và Mobile chưa?
- [ ] Nếu có thay đổi database, migration có nguy cơ làm mất dữ liệu người dùng cũ không?

---

## 2. Rà soát An ninh & Dữ liệu Nhạy cảm (Security Check)

- [ ] Kiểm tra lại `git diff` toàn bộ: Có bất kỳ API key, password database hoặc token bí mật nào bị commit nhầm không?
- [ ] Các input từ người dùng có nguy cơ bị injection hoặc XSS không?
- [ ] Quyền truy cập các trang nội bộ / admin đã được bảo vệ chặt chẽ chưa?

---

## 3. Rà soát Sự sạch sẽ của Mã nguồn (Code Sanity)

- [ ] Đã xóa sạch toàn bộ `console.log`, code test thử tạm thời chưa?
- [ ] Tên biến, tên hàm có rõ ràng, mạch lạc không?
- [ ] Các xử lý lỗi (try/catch, error state) đã có thông báo thân thiện cho người dùng chưa?

---

## 4. Kiểm tra Sẵn sàng Bàn giao (Client Handoff Readiness)

- [ ] File `.env.example` có đầy đủ tất cả các biến môi trường cần thiết để khách hàng hoặc người khác có thể tự setup được app không?
- [ ] Hướng dẫn cài đặt trong `README.md` có chính xác và chạy được từ đầu không?
- [ ] Đã ghi nhận bản phát hành vào `docs/release-notes/` để khách hàng biết phiên bản này đã làm được những gì.

---

## 5. Tự Phê duyệt & Merge (Ship It)

Khi tất cả các tiêu chí trên đã đạt:
1. Thực hiện **Squash and Merge** vào `main`.
2. Xóa nhánh tính năng để giữ repository gọn gàng.
3. Deploy lên môi trường thực tế (Vercel / VPS) và kiểm tra lại một lượt các luồng chính.
EOF
