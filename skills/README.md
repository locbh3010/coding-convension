# AI Agent Skills (Claude Code, Antigravity, Codex)

Thư mục này chứa bộ **AI Agent Skills chuẩn hóa** (theo chuẩn mở Agent Skills với file `SKILL.md` và YAML frontmatter), được trích xuất trực tiếp từ bộ quy chuẩn kỹ thuật của team.

Mục đích: **Chỉ cần sao chép thư mục `skills/` (hoặc skill cần dùng) vào dự án mới, các AI coding assistants sẽ tự động hiểu và tuân thủ 100% quy chuẩn kỹ thuật**.

---

## 📦 Danh mục Skills

| Skill Name | Thư mục | Mục đích & Khi nào sử dụng |
|---|---|---|
| `personal-monolith-conventions` | [`personal-monolith-conventions/SKILL.md`](personal-monolith-conventions/SKILL.md) | **Dự án Cá nhân / Freelance (Monolith)**: Không Jira, branch name ngắn gọn (`feat/auth`), self-review checklist, squash merge, clean code dễ bàn giao. |
| `monolith-conventions` | [`monolith-conventions/SKILL.md`](monolith-conventions/SKILL.md) | **Dự án Doanh nghiệp / Team (Monolith)**: Có Jira task id, quy trình review đa tầng, Next.js FE / NestJS BE standards. |
| `monorepo-conventions` | [`monorepo-conventions/SKILL.md`](monorepo-conventions/SKILL.md) | **Dự án Monorepo (Turborepo + npm workspaces)**: Cấu trúc `apps/` & `packages/`, 3 ranh giới bất biến, chống phantom dependencies, commit có scope (`feat(web):`). |
| `technical-documentation` | [`technical-documentation/SKILL.md`](technical-documentation/SKILL.md) | **Tài liệu Kỹ thuật & Release Notes**: Chuẩn viết `docs/tech/`, `docs/release-notes/`, quy tắc Real-Time Living Doc, cấm lưu specs trong repo. |

---

## 🚀 Hướng dẫn Đồng bộ vào Dự án Mới

### 1. Dùng cho Claude Code CLI
Sao chép thư mục `skills/` vào thư mục cấu hình của Claude Code trong dự án:
```bash
# Trong dự án mới của bạn:
mkdir -p .claude/skills
cp -r /path/to/code-convensions/skills/* .claude/skills/
```
Claude Code sẽ tự động nhận diện và nạp các kỹ năng này khi khởi động.

---

### 2. Dùng cho Google Antigravity (CLI & IDE)
Sao chép vào thư mục `.agents/skills/` của workspace hoặc thư mục cấu hình cá nhân:
```bash
# Trong dự án mới của bạn:
mkdir -p .agents/skills
cp -r /path/to/code-convensions/skills/* .agents/skills/
```
Antigravity sẽ lập tức lập chỉ mục các skills này và kích hoạt tự động theo context công việc.

---

### 3. Dùng cho OpenAI Codex CLI
Codex CLI hỗ trợ đọc thư mục `skills/` ngay tại root dự án:
```bash
# Trong dự án mới của bạn:
cp -r /path/to/code-convensions/skills .
```
Khi chạy prompt hoặc review, Codex sẽ tự động tuân thủ các quy tắc trong `SKILL.md`.
EOF
