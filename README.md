# Engineering & Code Quality Standards

Repository này là bộ quy chuẩn kỹ thuật (Engineering Guidelines & AI Skills) dùng làm **baseline chuẩn hóa cho toàn bộ các dự án sau này** của team.

Tất cả quy chuẩn được phân loại thành 2 nhóm đối tượng sử dụng:
1. **Dành cho Developer (Human)**: Nằm trong thư mục `human/` (phân chia Monolith và Monorepo).
2. **Dành cho AI Coding Agents**: Nằm trong thư mục `skills/` (sử dụng được cho Claude Code, Antigravity, Codex CLI).

---

## 📂 Cấu trúc Repository

### 1. [Monolith Guidelines](human/monolith/README.md) (`human/monolith/`)
Bộ quy chuẩn kỹ thuật dành cho các dự án độc lập (**Single / Monolith Repo**):
- Next.js Frontend hoặc NestJS Backend.
- Coding conventions, phân tầng kiến trúc, Git Flow, checklist review và quy chuẩn viết Technical Docs + Release Notes.

### 2. [Monorepo Guidelines](human/monorepo/README.md) (`human/monorepo/`)
Bộ quy chuẩn kỹ thuật dành cho các dự án đa ứng dụng (**Monorepo**):
- Sử dụng **npm workspaces** và **Turborepo** của Vercel.
- Ranh giới `apps/` và `packages/`, chống phantom dependencies, quản lý task caching, Git Flow có scope, Technical Docs và Changesets.

### 3. [AI Agent Skills](skills/README.md) (`skills/`)
Bộ kỹ năng chuẩn hóa (Agent Skills standard) cô đọng từ toàn bộ quy chuẩn trên:
- **Dễ dàng sao chép**: Chỉ cần copy thư mục `skills/` vào dự án bất kỳ (hoặc thư mục skill của AI tương ứng).
- **Tương thích đa nền tảng**: Hoạt động đồng bộ trên **Claude Code**, **Google Antigravity**, và **Codex CLI**.
- **Tự động tuân thủ**: Giúp các AI coding assistant tự động viết code, đặt tên branch, commit, viết tech doc và kiểm tra bảo mật đúng 100% chuẩn của team.
