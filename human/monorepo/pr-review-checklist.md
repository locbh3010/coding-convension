# Pre-Merge Self-Review Checklist (Dự án Cá nhân)

Checklist này dành cho bạn **tự rà soát (Self-Review)** trước khi bấm nút Merge vào nhánh chính (`main`), đóng vai trò như một tấm lưới an toàn giúp dự án cá nhân luôn duy trì chất lượng kỹ thuật cao.

---

## 1. Đánh giá Bán kính Tác động (Blast Radius)

- [ ] **Nhận diện vùng ảnh hưởng**:
  - *Chỉ sửa trong `apps/*`*: Rủi ro thấp, chỉ cần test app đó.
  - *Sửa trong `packages/ui`*: Đã mở xem giao diện có bị vỡ ở các app khác không?
  - *Sửa trong `packages/types`*: Đã chạy `turbo run typecheck` để chắc chắn không làm gãy contract giữa FE và BE chưa?
  - *Sửa trong `packages/database`*: Migration có an toàn không, có nguy cơ làm mất dữ liệu cũ không?

---

## 2. Rà soát Ranh giới Kiến trúc (Architecture Sanity Check)

- [ ] Không có file nào trong `packages/*` import từ `apps/*`.
- [ ] Không có app nào trong `apps/*` import trực tiếp từ app khác.
- [ ] Không có business logic nặng của một app cụ thể bị nhét nhầm vào `@repo/ui`.

---

## 3. Rà soát An ninh & Rò rỉ Secret

- [ ] Không có API Key của AI (Fal.ai, Replicate, OpenAI) hoặc Secret Stripe bị nhúng nhầm vào code client hoặc có tiền tố `NEXT_PUBLIC_`.
- [ ] File `.env` hoặc file credentials không bị stage nhầm vào git.
- [ ] Các endpoint nhạy cảm đều có Guard bảo vệ và validate DTO whitelist.

---

## 4. Tự Phê duyệt & Merge (Ship It)

Khi tất cả các tiêu chí trên đã thỏa mãn:
1. Thực hiện **Squash and Merge** vào nhánh chính.
2. Xóa nhánh tính năng để giữ repository gọn gàng.
3. Nếu có cập nhật database migration, tiến hành deploy migration trước khi khởi động phiên bản mới của app.
EOF
