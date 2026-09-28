# Monorepo Architecture & Code Quality Principles

Tài liệu này định nghĩa các nguyên tắc kiến trúc cốt lõi áp dụng cho hệ thống **Monorepo** (sử dụng Turborepo và npm workspaces).

---

## 1. Security & Secret Isolation

Trong Monorepo chứa cả Frontend và Backend, nguy cơ lớn nhất là **rò rỉ secret của Backend sang bundle của Frontend hoặc package dùng chung**.

### Nguyên tắc kiểm soát:
- **Tuyệt đối không để Backend Secret trong Root Environment**: Các biến nhạy cảm như `DATABASE_URL`, `JWT_SECRET`, `PAYMENT_API_KEY` chỉ được khai báo trong file `.env` của `apps/api` hoặc `apps/worker`.
- **Ranh giới `@repo/ui` và `@repo/types`**:
  - `@repo/ui` và `@repo/types` là shared packages, tuyệt đối không truy cập `process.env`.
  - Không nhúng secret hay private keys vào bất kỳ file nào trong thư mục `packages/`.
- **Next.js Prefix Discipline**: Chỉ các biến có tiền tố `NEXT_PUBLIC_` mới được phép xuất hiện trên client-side code của `apps/web`.

---

## 2. Dependency Direction & Ranh giới Workspace

Kiến trúc Monorepo tuân thủ nghiêm ngặt chiều phụ thuộc một chiều (Unidirectional Dependency Graph):

```text
    ┌───────────────────────────┐
    │     packages/configs      │ (eslint, tsconfig, prettier)
    └─────────────┬─────────────┘
                  │
    ┌─────────────▼─────────────┐
    │      packages/types       │ (contracts, DTOs, domain models)
    └──────┬─────────────┬──────┘
           │             │
┌──────────▼───────┐  ┌──▼──────────────────┐
│   packages/ui    │  │  packages/database  │
└──────────┬───────┘  └──┬──────────────────┘
           │             │
┌──────────▼───────┐  ┌──▼──────────────────┐
│   apps/web       │  │  apps/api           │
│   apps/admin     │  │  apps/worker        │
└──────────────────┘  └─────────────────────┘
```

### 3 Điều cấm kỵ (Hard Rules):
1. **Packages không bao giờ phụ thuộc vào Apps**: `packages/*` tuyệt đối không được import bất kỳ thứ gì từ `apps/*`.
2. **Apps không bao giờ import chéo Apps**: `apps/web` không bao giờ được import từ `apps/admin` hay `apps/api`. Mọi code cần chia sẻ bắt buộc phải được tách thành một package trong `packages/`.
3. **Cấm Circular Dependencies giữa các Packages**: Không được phép để `@repo/ui` phụ thuộc vào `@repo/database` hoặc ngược lại.

---

## 3. Chống Phantom Dependencies (Bảo toàn tính toàn vẹn gói)

### Vấn đề Phantom Dependency:
`npm workspaces` tự động hoisting các package phụ thuộc lên thư mục `node_modules` ở root. Điều này có thể khiến một app (ví dụ: `apps/web`) vô tình import được một thư viện (ví dụ: `dayjs`) chỉ vì `apps/api` đã cài đặt nó, dù `apps/web/package.json` chưa hề khai báo.

### Hậu quả:
- Phá vỡ tính năng tính toán build cache của Turborepo.
- Khi deploy độc lập qua Docker hoặc CI/CD, build sẽ crash vì thiếu thư viện.

### Quy tắc thực thi:
- Mỗi app/package **phải tự khai báo đầy đủ mọi dependency mà nó import trực tiếp** vào `package.json` của chính nó.
- Chạy linter kiểm tra dependency (`eslint-plugin-import` hoặc tool check dependency) định kỳ trong CI pipeline.

---

## 4. Turborepo Caching & Hermeticity (Tính khép kín của tác vụ)

Để Turborepo có thể tái sử dụng cache hiệu quả và an toàn:

- **Tính bất biến và tiền định (Deterministic)**: Với cùng một mã nguồn và biến môi trường, tác vụ build phải luôn tạo ra cùng một kết quả.
- **Khai báo đầy đủ Inputs/Outputs**:
  - Không ghi output ra ngoài thư mục của package (ví dụ: app không được compile thẳng vào thư mục root).
  - Khai báo chính xác các file tạo ra trong `turbo.json` (`outputs: [".next/**", "dist/**"]`).
- **Không side-effects qua biên giới**: Một task khi chạy trong `packages/ui` không được sửa đổi file của `apps/web`.

---

## 5. End-to-End Type Safety & Contract Ownership

- **Single Source of Truth**: `@repo/types` là trung tâm định nghĩa hợp đồng dữ liệu giữa Frontend và Backend.
- Khi Backend thay đổi API contract:
  1. Cập nhật DTO / schema trong `@repo/types`.
  2. Chạy `npm run typecheck` thông qua Turborepo (`npx turbo run typecheck`).
  3. Turborepo sẽ kiểm tra toàn bộ `apps/web`, `apps/admin`, `apps/api`. Mọi điểm bị ảnh hưởng sẽ báo lỗi TypeScript ngay lập tức.
- Giảm thiểu tối đa lỗi runtime giữa client và server khi release.

---

## 6. Centralized Tooling Baseline (`packages/configs`)

Để duy trì phong cách code đồng nhất trên toàn bộ monorepo:
- Các cấu hình ESLint, Prettier và TypeScript base được đóng gói tại `@repo/configs`.
- Mỗi app/package chỉ cần extend cấu hình base và bổ sung các rule đặc thù riêng:
  ```json
  // apps/web/tsconfig.json
  {
    "extends": "@repo/configs/tsconfig/nextjs.json",
    "compilerOptions": {
      "baseUrl": "."
    }
  }
  ```
EOF
