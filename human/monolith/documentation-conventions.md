# Documentation Conventions & Release Notes (Monolith)

Tài liệu này định nghĩa hệ thống **Technical Documentation**, **Release Notes** và quy trình **Changesets** chuẩn hóa cho các dự án độc lập (**Monolith / Single Repository**).

---

## 1. Triết lý: Tech Docs vs Specs vs Release Notes

Để tài liệu không bị trùng lặp, lỗi thời hoặc gây nhiễu, team phân định rạch ròi 3 loại tài liệu:

| Tiêu chí | Specs (Đặc tả yêu cầu) | Tech Docs (Tài liệu kỹ thuật) | Release Notes (Nhật ký phát hành) |
|---|---|---|---|
| **Câu hỏi trả lời** | *"Sản phẩm cần làm gì?"* | *"Hệ thống hiện tại chạy như thế nào?"* | *"Lần này thay đổi gì, tại sao và ảnh hưởng ai?"* |
| **Bản chất** | Yêu cầu nghiệp vụ, Acceptance Criteria, User story, Mockup mong muốn. | Hiện trạng source code, kiến trúc, data flow, module, API đang chạy thực tế. | Lịch sử thay đổi theo mốc thời gian / phiên bản release. |
| **Vòng đời** | Cố định theo Task/PRD (ít sửa sau khi đã bàn giao). | **Living Documentation** (Cập nhật liên tục mỗi khi code thay đổi). | Append-only (Ghi nhận một lần tại thời điểm release). |
| **Nơi lưu trữ** | **Bên ngoài repo** (Jira, Confluence, Figma). **TUYỆT ĐỐI KHÔNG LƯU TRONG REPO**. | **Trong repo**: `docs/tech/` | **Trong repo**: `docs/release-notes/` |

> 🚫 **QUY TẮC CỐT LÕI: KHÔNG LƯU SPECS TRONG CODEBASE**
> 
> Trong repository **chỉ lưu trữ Tài liệu Kỹ thuật (`docs/tech/`)** và **Nhật ký phát hành (`docs/release-notes/`)**. 
> - **Tuyệt đối không tạo thư mục lưu Specs (như `docs/specs/`, PRD, User Stories) bên trong repository**. 
> - Mọi đặc tả nghiệp vụ thuộc về các công cụ quản lý dự án (Jira, Confluence, Linear). 
> - Tech Docs chỉ tập trung mô tả **mã nguồn thực tế đang chạy** (Current Implementation).

---

## 2. Cấu trúc thư mục chuẩn (`docs/`) trong Monolith

Mọi dự án Monolith tổ chức tài liệu theo sơ đồ sau:

```text
docs/
├── tech/
│   ├── README.md                   # Bản đồ tra cứu nhanh toàn bộ hệ thống
│   ├── DOCUMENTATION-GUIDELINES.md # Hướng dẫn viết và duy trì tài liệu kỹ thuật
│   ├── architecture/               # Kiến trúc tổng thể, boundaries, data flow
│   ├── frontend/                   # (Nếu là FE repo) Cấu trúc màn hình, state, UI routing
│   ├── backend/                    # (Nếu là BE repo) Cấu trúc module, services, filters
│   ├── features/                   # Chi tiết kỹ thuật từng tính năng (auth, cart, billing)
│   ├── database/                   # Schema models, relations, migration policies
│   ├── technologies/               # Các thư viện/công nghệ thực tế sử dụng & lý do
│   ├── integrations/               # Tích hợp bên thứ 3 (Payment gateways, SMTP, OAuth)
│   └── decisions/                  # Architecture Decision Records (ADRs)
└── release-notes/
    ├── README.md                   # Mục lục lịch sử các phiên bản phát hành
    ├── TEMPLATE.md                 # Mẫu chuẩn khi tạo release note mới
    └── YYYY-MM-DD-vX.X.X.md        # File ghi nhận từng bản release cụ thể
```

---

## 3. Real-Time Documentation Rule (Quy tắc tài liệu thời gian thực)

Tech Docs là tài liệu sống (**Living Documentation**).

> **BẮT BUỘC:** Nếu một thay đổi mã nguồn làm cho tài liệu hiện tại trở nên sai lệch, thiếu sót hoặc gây hiểu lầm, thì **việc cập nhật Tech Doc là một phần bắt buộc của chính Pull Request đó**.

- Không tách việc cập nhật tài liệu thành một task "để làm sau".
- Reviewer có quyền **Block PR** nếu code thay đổi kiến trúc/logic/API nhưng Tech Doc tương ứng không được cập nhật trong cùng PR.

---

## 4. Quy trình Changesets trong Monolith

Changeset là công cụ tự động hóa việc theo dõi thay đổi phiên bản (Versioning) và tạo CHANGELOG dựa trên chuẩn SemVer.

### 4.1. Thiết lập Tooling

Dự án sử dụng `@changesets/cli`:

```bash
npm install -D @changesets/cli
npx changeset init
```

### 4.2. Khi nào bắt buộc tạo Changeset?

- **Bắt buộc**: Bất kỳ PR nào có thay đổi logic runtime, sửa bug (`fix`), thêm tính năng (`feat`), hoặc breaking changes (`major`).
- **Không bắt buộc**: Thay đổi thuần túy nội dung docs, comment, format style, cập nhật cấu hình CI không ảnh hưởng mã nguồn.

### 4.3. Cách Developer tạo Changeset

Trước khi commit và tạo PR:

```bash
npx changeset
```

1. Chọn mức độ thay đổi (`patch` / `minor` / `major` theo SemVer).
2. Nhập tóm tắt thay đổi (sẽ được tự động đưa vào `CHANGELOG.md` khi release).
3. Commit file markdown được sinh ra trong `.changeset/` vào cùng PR.

### 4.4. Quy trình Release với Changeset

Khi chuẩn bị release version mới trên branch `main`:

```bash
# 1. Tự động bump version trong package.json và cập nhật CHANGELOG.md
npx changeset version

# 2. Review thay đổi, commit và tạo Release PR
git commit -am "chore: release version vX.X.X"

# 3. Tạo Release Notes tương ứng trong docs/release-notes/
```

---

## 5. Quy chuẩn Release Notes

Mỗi đợt phát hành lên môi trường Staging/Production phải có một file ghi nhận trong `docs/release-notes/` theo mẫu:

```markdown
# Release Note: [Version / Identifier] — [YYYY-MM-DD]

## 1. Tổng quan (Summary)
- **Ngày phát hành**: YYYY-MM-DD
- **Phiên bản**: vX.X.X
- **Jira Tasks / Issues**: [PHI-101], [PHI-102]
- **Mục tiêu chính**: [Tóm tắt 1-2 câu lý do của bản release]

## 2. Chi tiết thay đổi (What Changed & Why)
### Features mới
- **[Tên tính năng]**: Mô tả thay đổi và nguyên nhân kinh doanh/kỹ thuật.

### Bug Fixes
- **[Vấn đề khắc phục]**: Nguyên nhân gốc (root cause) và giải pháp.

## 3. Phạm vi ảnh hưởng (Affected Areas)
- **Modules / Màn hình**: [Danh sách module hoặc màn hình chịu ảnh hưởng]
- **Tác động người dùng (User-facing)**: [Có đổi UI/UX hay workflow của user không]
- **Tác động kỹ thuật (Technical)**: [Database migration, env vars mới, thay đổi API contract]

## 4. Hướng dẫn Triển khai & Cấu hình (Migration & Config)
- **Biến môi trường mới**: `NEW_ENV_KEY` (đã mô tả trong `.env.example`).
- **Database Migration**: Cần chạy migration file: `20260928_add_user_status.sql`.
- **Thao tác thủ công (nếu có)**: [Chạy seed script, xóa Redis cache key X].

## 5. Bằng chứng kiểm thử & Traceability
- **PR liên quan**: #45, #48
- **Changeset ID**: `.changeset/fresh-lions-sing.md`
- **Kết quả Test**: Unit test pass 100%, E2E pass trên staging.
```

EOF
