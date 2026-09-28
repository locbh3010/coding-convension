# Monolith Engineering Guidelines (Dự án Cá nhân & Freelancer)

## Định hướng & Mục tiêu

Bộ tài liệu này là quy chuẩn kỹ thuật dành riêng cho **Developer cá nhân hoặc Freelancer** làm việc trên các dự án độc lập (**Single / Monolith Repository**) với Next.js (Frontend) hoặc NestJS (Backend).

Khi làm việc độc lập hoặc nhận dự án outsource/freelance, bạn không cần quy trình hành chính phức tạp của doanh nghiệp lớn (không Jira ticket, không branch gắn task id, không chờ đợi code review từ Tech Lead). Thay vào đó, bộ quy chuẩn này tập trung vào:

- **Tốc độ & Tính thực dụng (High Velocity)**: Quy trình tinh gọn, không rào cản hành chính, tập trung vào việc tạo ra sản phẩm chất lượng cao trong thời gian ngắn nhất.
- **Dễ dàng Bàn giao (Client Handoff Ready)**: Codebase được tổ chức mạch lạc, có tài liệu kỹ thuật thực tế (`docs/tech/`) và nhật ký release (`docs/release-notes/`), giúp khách hàng hoặc developer tiếp quản đánh giá rất cao tính chuyên nghiệp.
- **Maintainability lâu dài**: Sau 3 - 6 tháng quay lại dự án (để fix bug hoặc nâng cấp tính năng cho khách), bạn vẫn hiểu ngay cấu trúc mà không mất thời gian đọc lại từng dòng code.
- **Bảo mật & Ổn định cốt lõi**: Giữ trọn các chốt chặn an toàn: DTO validation whitelist, Server Actions auth + Zod, tính bất biến của Database Migrations (không bao giờ làm hỏng database của khách).

---

## Danh mục Quy chuẩn

| File | Nội dung chính |
|---|---|
| [`frontend-code-conventions.md`](frontend-code-conventions.md) | Coding conventions Next.js App Router, Feature-based structure, Server Actions an toàn, state tiering |
| [`backend-code-conventions.md`](backend-code-conventions.md) | Coding conventions NestJS Module, Thin Controller, DTO ValidationPipe whitelist, Envelope responses |
| [`architecture-principles.md`](architecture-principles.md) | Nguyên tắc kiến trúc: Security First, Maintainability over Cleverness, Boot-time Env validation |
| [`git-flow.md`](git-flow.md) | Git Flow tinh gọn cho Freelancer/Solo dev, branch name không task id, squash merge |
| [`git-rules.md`](git-rules.md) | Conventional Commits rõ nghĩa, quy tắc staging sạch sẽ, tiêu chí tự merge |
| [`development-checklist.md`](development-checklist.md) | Checklist tự kiểm tra code và dependencies trước khi merge |
| [`pr-review-checklist.md`](pr-review-checklist.md) | Pre-Merge Self-Review Checklist: Tự rà soát rủi ro trước khi bàn giao / deploy |
| [`documentation-conventions.md`](documentation-conventions.md) | Quy chuẩn Tech Docs (`docs/tech/`), Release Notes cho khách hàng và Changesets |

---

## Tooling Baseline

- `npm`
- `ESLint` & `Prettier`
- `Husky` (pre-commit checks nhẹ)
- `@changesets/cli` (tùy chọn để quản lý phiên bản bàn giao)
EOF
