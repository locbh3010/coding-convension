# Documentation & Release Conventions (Dự án Cá nhân)

Tài liệu này định nghĩa hệ thống **Technical Documentation**, **Release Notes** và quy trình **Changesets** tinh gọn cho **Monorepo Cá nhân** (Turborepo + npm workspaces).

---

## 1. Triết lý: Tech Docs vs Specs vs Release Notes

| Tiêu chí | Specs (Đặc tả ý tưởng/sản phẩm) | Tech Docs (Tài liệu kỹ thuật) | Release Notes (Nhật ký phát hành) |
|---|---|---|---|
| **Câu hỏi cốt lõi** | *"Mình muốn sản phẩm làm được gì?"* | *"Monorepo hiện tại đang chạy như thế nào?"* | *"Apps và packages nào vừa được cập nhật, tại sao?"* |
| **Bản chất** | Ý tưởng, checklist tính năng, user flow, wireframe phác thảo. | Hiện trạng kiến trúc thực tế, ranh giới `apps/` và `packages/`, data flow giữa FE và BE, schema DB. | Ghi nhận chi tiết từng đợt deploy/release của từng app hoặc package nội bộ. |
| **Tính cập nhật** | Đọc tham khảo khi lên ý tưởng. | **Living Documentation** (Cập nhật đồng thời khi sửa code). | Append-only theo từng lần release/version bump. |
| **Nơi lưu trữ** | **Bên ngoài repo** (Notion, Linear, GitHub Issues, Figma). **TUYỆT ĐỐI KHÔNG LƯU TRONG REPO**. | **Trong repo**: `docs/tech/` | **Trong repo**: `docs/release-notes/` |

> **[QUY TẮC CỐT LÕI] KHÔNG LƯU SPECS TRONG CODEBASE**
> 
> Trong repository **chỉ lưu trữ Tài liệu Kỹ thuật (`docs/tech/`)** và **Nhật ký phát hành (`docs/release-notes/`)**. 
> - **Tuyệt đối không lưu trữ Specs, PRD, User Stories bên trong repository**. 
> - Mọi ý tưởng nghiệp vụ hãy lưu tại công cụ ghi chú cá nhân (Notion, Linear, Apple Notes). 
> - Tech Docs chỉ tập trung mô tả **mã nguồn thực tế đang chạy** (Current Implementation).

---

## 2. Cấu trúc Thư mục Kỹ thuật Chuẩn (`docs/`) trong Monorepo

```text
docs/
├── tech/
│   ├── README.md                   # Sơ đồ tổng quan toàn bộ monorepo
│   ├── DOCUMENTATION-GUIDELINES.md # Hướng dẫn viết và duy trì tài liệu
│   ├── architecture/
│   │   ├── workspace-graph.md      # Biểu đồ quan hệ giữa apps và packages
│   │   └── turbo-pipelines.md      # Cấu hình tasks và caching trong turbo.json
│   ├── apps/
│   │   ├── web.md                  # Hiện trạng Next.js Customer Web
│   │   ├── admin.md                # Hiện trạng Next.js Admin Dashboard (nếu có)
│   │   ├── api.md                  # Hiện trạng NestJS Main API
│   │   └── worker.md               # Hiện trạng NestJS Background Worker (BullMQ)
│   ├── packages/
│   │   ├── ui.md                   # Shared UI components và design tokens
│   │   ├── database.md             # Prisma schema, quan hệ và migrations
│   │   ├── types.md                # Shared DTOs, API envelopes, validation schemas
│   │   └── configs.md              # Shared ESLint và TypeScript configs
│   ├── features/                   # Luồng kỹ thuật các tính năng lớn (gen-image, billing)
│   ├── technologies/               # Các công nghệ thực tế đang sử dụng
│   ├── integrations/               # Tích hợp bên thứ 3 (Fal.ai, Stripe, R2/S3)
│   └── decisions/                  # Ghi nhận các quyết định kiến trúc lớn (ADRs)
└── release-notes/
    ├── README.md                   # Nhật ký tổng hợp các đợt phát hành
    ├── TEMPLATE.md                 # Mẫu ghi nhận release note
    └── YYYY-MM-DD-vX.X.X.md        # File release note từng phiên bản
```

---

## 3. Real-Time Documentation Rule (Viết ngay khi sửa code)

Dù là dự án cá nhân, việc giữ tài liệu đúng với thực tế sẽ giúp chính bạn không bị quên khi quay lại dự án sau 2 tuần hoặc 2 tháng:

> **Quy tắc:** Nếu bạn sửa pipeline trong `turbo.json`, thay đổi API contract trong `@repo/types`, hoặc thay đổi cách thức xử lý worker, hãy cập nhật nhanh 1-2 đoạn trong `docs/tech/` ngay trong nhánh đó trước khi merge.

---

## 4. Tự động hóa Versioning & Changelog với Changesets

Sử dụng `@changesets/cli` kết hợp **Turborepo** và **npm workspaces**:

### 4.1. Cách tạo Changeset khi hoàn thành tính năng:
```bash
npx changeset
```
1. Chọn app/package đã sửa (ví dụ: `apps/web` và `@repo/ui`).
2. Chọn mức SemVer: `patch` (sửa lỗi), `minor` (tính năng mới), `major` (đổi lớn).
3. Viết 1 câu tóm tắt những gì vừa làm.
4. Commit file `.changeset/*.md` vào git.

### 4.2. Tự động hóa khi Release:
Khi muốn bump version và sinh CHANGELOG:
```bash
npm run version-packages # Tự động chạy changeset version
```
Changeset sẽ tự động bump version trong `package.json` của các app/package tương ứng và tạo `CHANGELOG.md` sạch sẽ.

---

## 5. Mẫu Release Note Tinh gọn cho Dự án Cá nhân

Khi deploy một phiên bản mới lên server/production, tạo một file trong `docs/release-notes/`:

```markdown
# Release Note: [Version / Scope] — [YYYY-MM-DD]

## 1. Tổng quan
- **Ngày phát hành**: YYYY-MM-DD
- **Mục tiêu**: Bổ sung tính năng tạo ảnh bằng model Flux trên `apps/web` và worker.

## 2. Chi tiết theo từng App & Package
### `apps/web` (v1.1.0)
- Thêm Prompt Workbench với slider chỉnh CFG và Aspect Ratio.
- Hiển thị tiến trình gen ảnh realtime qua Server-Sent Events.

### `apps/api` (v1.1.0)
- Thêm endpoint trừ credit nguyên tử trước khi đưa job vào queue.

### `apps/worker` (v1.1.0)
- Tích hợp Fal.ai API pipeline, tự động upload ảnh kết quả lên Cloudflare R2.

### `packages/database` (v1.0.1)
- Migration: `20260928_add_image_generations.sql`.

## 3. Thao tác Triển khai (Deployment Checklist)
- [ ] Chạy migration database: `npx turbo run db:migrate`.
- [ ] Thêm biến môi trường mới vào server: `FAL_KEY`, `R2_SECRET_ACCESS_KEY`.
```
EOF
