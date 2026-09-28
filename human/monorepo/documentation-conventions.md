# Documentation Conventions & Release Notes (Monorepo)

Tài liệu này định nghĩa hệ thống **Technical Documentation**, **Release Notes** và quy trình **Changesets** chuẩn hóa cho các dự án **Monorepo** (sử dụng Turborepo và npm workspaces).

---

## 1. Triết lý: Tech Docs vs Specs vs Release Notes trong Monorepo

| Tiêu chí | Specs (Đặc tả yêu cầu) | Tech Docs (Tài liệu kỹ thuật) | Release Notes (Nhật ký phát hành) |
|---|---|---|---|
| **Câu hỏi cốt lõi** | *"Yêu cầu nghiệp vụ cần gì?"* | *"Monorepo hiện tại đang chạy như thế nào?"* | *"Apps và packages nào vừa thay đổi, tại sao?"* |
| **Phạm vi** | Mô tả tính năng từ góc nhìn người dùng/sản phẩm (PRD, Acceptance Criteria). | Hiện trạng kiến trúc, ranh giới `apps/` và `packages/`, data flow giữa FE và BE, schema DB. | Ghi nhận chi tiết từng đợt deploy/release của từng app hoặc package nội bộ. |
| **Tính cập nhật** | Cố định theo milestone. | **Living Documentation** (Cập nhật đồng thời trong PR làm thay đổi code). | Append-only theo từng lần release/version bump. |
| **Vị trí lưu** | Jira / Confluence / `docs/specs/` | `docs/tech/` | `docs/release-notes/` |

---

## 2. Cấu trúc thư mục chuẩn (`docs/`) trong Monorepo

Do Monorepo chứa nhiều ứng dụng và nhiều package nội bộ, cấu trúc tài liệu kỹ thuật phản ánh rõ ràng cấu trúc của workspace:

```text
docs/
├── tech/
│   ├── README.md                   # Sơ đồ và bản đồ tra cứu toàn bộ monorepo
│   ├── DOCUMENTATION-GUIDELINES.md # Quy chuẩn viết, duy trì và cập nhật tài liệu
│   ├── architecture/
│   │   ├── workspace-graph.md      # Biểu đồ quan hệ giữa apps và packages
│   │   └── turbo-pipelines.md      # Quy hoạch task pipelines và chiến lược caching
│   ├── apps/
│   │   ├── web.md                  # Hiện trạng kỹ thuật Next.js Customer Web
│   │   ├── admin.md                # Hiện trạng kỹ thuật Next.js Admin Dashboard
│   │   ├── api.md                  # Hiện trạng kỹ thuật NestJS Main API
│   │   └── worker.md               # Hiện trạng kỹ thuật NestJS Background Worker
│   ├── packages/
│   │   ├── ui.md                   # Design System, components và Tailwind tokens
│   │   ├── database.md             # Prisma/TypeORM schema, relations và migrations
│   │   ├── types.md                # Shared DTOs, Envelopes, Domain Error Codes
│   │   └── configs.md              # Shared ESLint, Prettier và TypeScript configs
│   ├── features/                   # Luồng kỹ thuật End-to-End xuyên suốt FE & BE (auth, order)
│   ├── technologies/               # Các công nghệ thực tế đang sử dụng và lý do
│   ├── integrations/               # Các tích hợp bên thứ 3 (OAuth, Payment, S3, Email)
│   └── decisions/                  # Architecture Decision Records (ADRs) của monorepo
└── release-notes/
    ├── README.md                   # Nhật ký tổng hợp các đợt phát hành
    ├── TEMPLATE.md                 # Mẫu chuẩn ghi nhận release note
    └── YYYY-MM-DD-vX.X.X.md        # File release note từng phiên bản
```

---

## 3. Real-Time Documentation Rule (Bắt buộc trong PR)

Tech Docs trong monorepo là tài liệu sống (**Living Documentation**).

> **BẮT BUỘC:** Nếu một PR làm thay đổi:
> 1. Kiến trúc hoặc pipeline trong `turbo.json`.
> 2. Ranh giới, contract hoặc API giữa các app/package (`@repo/types`, `@repo/database`).
> 3. Cách thức triển khai của bất kỳ app nào trong `apps/*` hoặc shared package trong `packages/*`.
>
> Thì **việc cập nhật file tương ứng trong `docs/tech/` là điều kiện tiên quyết để PR được Approve**.

Reviewer có trách nhiệm kiểm tra diff của `docs/tech/` tương ứng với diff của source code trong cùng PR.

---

## 4. Quy trình Changesets trong Monorepo (Tích hợp Turborepo + npm workspaces)

Trong monorepo, `@changesets/cli` quản lý việc bump version độc lập hoặc đồng bộ cho từng app và package.

### 4.1. Cấu hình Chuẩn (`.changeset/config.json`)
```json
{
  "$schema": "https://unpkg.com/@changesets/config/schema.json",
  "changelog": "@changesets/cli/changelog",
  "commit": false,
  "fixed": [],
  "linked": [],
  "access": "restricted",
  "baseBranch": "develop",
  "updateInternalDependencies": "patch",
  "ignore": []
}
```
- `"updateInternalDependencies": "patch"`: Tự động bump patch các app tiêu thụ khi một internal package (`@repo/ui`, `@repo/types`) có version mới.

### 4.2. Developer Workflow với Changeset
Khi developer hoàn thành code trong branch:
```bash
npx changeset
```
1. **Chọn package chịu ảnh hưởng**: Dùng phím mũi tên và Space để tích chọn đúng app/package đã sửa (ví dụ: `apps/web` và `@repo/ui`).
2. **Chọn loại SemVer**: `patch` (sửa lỗi), `minor` (tính năng mới tương thích ngược), `major` (breaking change).
3. **Viết tóm tắt thay đổi**: Mô tả ngắn gọn, rõ ràng.
4. **Commit file `.changeset/*.md`** được sinh ra vào cùng Pull Request.

### 4.3. Pipeline Tự động hóa Release qua Turborepo
Trong root `package.json`:
```json
{
  "scripts": {
    "version-packages": "changeset version",
    "release": "turbo run build && changeset publish"
  }
}
```
- Khi merge vào `main`, CI chạy `npm run version-packages` để tự động cập nhật `package.json` của từng app/package và tạo `CHANGELOG.md` riêng cho từng workspace.

---

## 5. Quy chuẩn Monorepo Release Notes

Mỗi đợt phát hành lên Staging hoặc Production phải có một file ghi nhận trong `docs/release-notes/` theo mẫu:

```markdown
# Release Note: [Version / Scope] — [YYYY-MM-DD]

## 1. Tổng quan Release
- **Ngày phát hành**: YYYY-MM-DD
- **Target Branch**: `main` (hoặc `staging`)
- **Jira Milestones**: [PHI-Sprint-14]
- **Tóm tắt**: Cập nhật tính năng thanh toán trên `apps/web` và tối ưu API trên `apps/api`.

## 2. Chi tiết theo từng App & Package (Workspace Breakdown)
### `apps/web` (v1.2.0)
- **Feat**: Thêm phương thức thanh toán VNPay và Momo.
- **Fix**: Sửa lỗi crash khi load danh sách giỏ hàng rỗng.

### `apps/api` (v2.1.0)
- **Feat**: Thêm endpoint webhook xử lý thanh toán từ VNPay.
- **Breaking**: Thay đổi format response của `/api/orders` (đã đồng bộ trong `@repo/types`).

### `packages/database` (v1.1.0)
- **Migration**: Chạy file migration `20260928_add_payment_transactions.sql`.

## 3. Quản lý Rủi ro & Tác động Kỹ thuật (Technical Blast Radius)
- **Database Migration Required**: CÓ (phải chạy `npx turbo run db:migrate` trước khi deploy app).
- **Environment Variables mới**:
  - `apps/api`: Bổ sung `VNPAY_SECRET_KEY`, `VNPAY_TMN_CODE`.
  - `apps/web`: Bổ sung `NEXT_PUBLIC_VNPAY_HOST`.
- **Breaking Changes & Downstream Impact**: Không ảnh hưởng bên ngoài.

## 4. Traceability & Bằng chứng
- **Related PRs**: #102, #105
- **Changesets**: `.changeset/proud-foxes-jump.md`
- **CI / Build Verification**: Turborepo build cache PASS 100%.
```
EOF
