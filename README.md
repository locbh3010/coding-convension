# Engineering & Code Quality Standards

Repository này là bộ quy chuẩn kỹ thuật (Engineering Guidelines & AI Skills) dùng làm **baseline chuẩn hóa cho toàn bộ các dự án sau này** của team và cá nhân.

Tất cả quy chuẩn được phân loại thành 2 nhóm đối tượng sử dụng:
1. **Dành cho Developer (Human Handbook)**: Nằm trong thư mục `human/`.
2. **Dành cho AI Coding Agents**: Nằm trong thư mục `skills/` (sử dụng được cho Claude Code, Antigravity, Codex CLI).

---

## Danh mục Quy chuẩn Kỹ thuật (`human/`)

### 1. [Personal Monolith Guidelines](human/personal-monolith/README.md) (`human/personal-monolith/`)
Quy chuẩn dành cho **Developer cá nhân hoặc Freelancer** làm việc trên các repository độc lập:
- Next.js Frontend hoặc NestJS Backend.
- Tinh gọn tối đa, **không Jira ticket, không branch task id**.
- Tập trung vào tốc độ, code sạch, dễ bàn giao cho khách hàng (Client Handoff Ready) và bảo mật cốt lõi.

### 2. [Enterprise / Team Monolith Guidelines](human/monolith/README.md) (`human/monolith/`)
Quy chuẩn dành cho **Dự án Nhóm / Doanh nghiệp / Outsource** độc lập:
- Quy trình phối hợp đa thành viên: Jira task workflow, branch có task ID, quy trình Code Review và Phê duyệt chặt chẽ.

### 3. [Personal Monorepo Guidelines](human/monorepo/README.md) (`human/monorepo/`)
Quy chuẩn dành cho **Monorepo Cá nhân** (sử dụng **Turborepo** và **npm workspaces**):
- Tối ưu cho 1 dev xây dựng sản phẩm lớn (Full-stack Next.js web + NestJS API + Worker + Database package).
- Tinh gọn không Jira, commit và branch có scope (`feat(web):`, `fix(api):`), End-to-End Type Safety, dễ scale.

---

## AI Agent Skills ([`skills/`](skills/README.md))

Toàn bộ quy chuẩn trên được đóng gói thành các **AI Agent Skills chuẩn hóa** (Agent Skills format) để copy trực tiếp vào dự án:
- `skills/personal-monolith-conventions/`: Kỹ năng code monolith cho cá nhân/freelance.
- `skills/monolith-conventions/`: Kỹ năng code monolith cho team/doanh nghiệp.
- `skills/monorepo-conventions/`: Kỹ năng code monorepo (Turborepo + npm workspaces).
- `skills/technical-documentation/`: Kỹ năng viết Tech Docs (`docs/tech/`), Release Notes và Changesets.
EOF
