# Turborepo Conventions & Pipeline Configuration

Tài liệu này định nghĩa các nguyên tắc và cấu hình chuẩn khi sử dụng **Turborepo** (`turbo`) kết hợp với **npm workspaces**.

---

## 1. Root `package.json` & Workspaces Setup

Tất cả các apps và packages phải được quản lý thông qua trường `workspaces` trong file `package.json` ở root:

```json
{
  "name": "my-monorepo",
  "private": true,
  "workspaces": [
    "apps/*",
    "packages/*"
  ],
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev --parallel",
    "lint": "turbo run lint",
    "typecheck": "turbo run typecheck",
    "test": "turbo run test",
    "clean": "turbo run clean && rm -rf node_modules",
    "format": "prettier --write \"**/*.{ts,tsx,md,json}\""
  },
  "devDependencies": {
    "turbo": "^2.0.0",
    "prettier": "^3.0.0"
  },
  "engines": {
    "node": ">=20.0.0",
    "npm": ">=10.0.0"
  }
}
```

### Nguyên tắc quản lý dependencies:
- **Root `devDependencies`**: Chỉ chứa các tool áp dụng toàn bộ repository như `turbo`, `prettier`.
- **Không cài runtime dependencies ở root**: Bất kỳ package nào chạy ở runtime (`react`, `@nestjs/core`, `zod`, `axios`) **phải được cài vào đúng `package.json` của app hoặc package sử dụng nó**.
- **Cú pháp cài đặt qua npm workspaces**:
  ```bash
  # Cài đặt dependency cho một app cụ thể
  npm install zustand --workspace=apps/web

  # Cài đặt dependency giữa các package nội bộ
  npm install @repo/ui --workspace=apps/web
  ```

---

## 2. Chuẩn hóa `turbo.json`

File `turbo.json` ở root định nghĩa dependency graph giữa các task và chính sách lưu cache:

```json
{
  "$schema": "https://turbo.build/schema.json",
  "ui": "tui",
  "globalDependencies": [
    "**/.env.*local",
    "tsconfig.json"
  ],
  "globalEnv": [
    "NODE_ENV",
    "CI"
  ],
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "inputs": [
        "$TURBO_DEFAULT$",
        ".env*"
      ],
      "outputs": [
        ".next/**",
        "!.next/cache/**",
        "dist/**"
      ]
    },
    "typecheck": {
      "dependsOn": ["^build"],
      "outputs": []
    },
    "lint": {
      "dependsOn": ["^build"],
      "outputs": []
    },
    "test": {
      "dependsOn": ["^build"],
      "inputs": [
        "$TURBO_DEFAULT$",
        "jest.config.*",
        "vitest.config.*"
      ],
      "outputs": [
        "coverage/**"
      ]
    },
    "dev": {
      "cache": false,
      "persistent": true
    },
    "clean": {
      "cache": false
    }
  }
}
```

---

## 3. Quy tắc Task Dependencies (`dependsOn`)

### Topological Dependency (`^build`):
- `^build` (có dấu caret `^`) chỉ thị Turborepo rằng: **phải build toàn bộ internal packages mà app phụ thuộc trước** khi build app đó.
- Ví dụ: `apps/web` phụ thuộc `@repo/ui`. Khi chạy `turbo run build`, Turborepo sẽ build `@repo/ui` xong rồi mới build `apps/web`.
- Mọi task `build`, `lint`, `typecheck` cần compiled outputs của dependencies phải khai báo `"dependsOn": ["^build"]`.

### Same-package Dependency:
- Nếu một task trong cùng một package phụ thuộc vào task khác của chính nó (ví dụ: `deploy` cần `build` trước), sử dụng:
  ```json
  "deploy": {
    "dependsOn": ["build"]
  }
  ```

---

## 4. Quản lý Cache Inputs & Outputs

Turborepo xác định cache hit dựa trên hash của Task Inputs. Nếu output không được khai báo đủ, các task phụ thuộc phía sau sẽ bị thiếu file khi khôi phục từ cache.

### Quy tắc Outputs:
- **Next.js apps**: Phải khai báo `".next/**", "!.next/cache/**"`. Loại trừ `.next/cache` để tránh kích thước cache bị phình to vô ích.
- **NestJS / Shared TypeScript packages**: Khai báo `"dist/**"`.
- **Test coverage**: Khai báo `"coverage/**"`.

### Cấm Cache (`"cache": false`):
- Các tác vụ dev server có watcher (`dev`, `start:dev`) **tuyệt đối không được bật cache** và phải đánh dấu `"persistent": true`.
- Các task clean dọn dẹp file không cache.

---

## 5. Quản lý Environment Variables với Turborepo

Một trong những nguyên nhân phổ biến nhất khiến Next.js / NestJS build cache bị sai lệch là **undeclared environment variables**.

### Quy tắc kiểm soát env:
1. **`globalEnv`**: Dành cho các biến môi trường ảnh hưởng đến kết quả build của toàn bộ mọi app/package (ví dụ: `NODE_ENV`, `VERCEL_ENV`).
2. **Task-level `env`**: Biến chỉ áp dụng cho một task cụ thể.
3. **Next.js Public Variables**: Mọi biến bắt đầu bằng `NEXT_PUBLIC_*` được tự động include vào build hash của Next.js app, nhưng nếu build script phụ thuộc vào biến secret phía server, bắt buộc phải khai báo trong `turbo.json`:
   ```json
   "build": {
     "env": [
       "DATABASE_URL",
       "JWT_SECRET",
       "API_BASE_URL"
     ]
   }
   ```
4. **Không cache nhầm biến local**: File `.env.local` phải được gitignore và nằm trong `globalDependencies` để khi dev thay đổi env local, Turborepo tự động bust cache.

---

## 6. Lệnh thực thi & Filter Syntax chuẩn

Developer không chuyển đổi thư mục (`cd`) thủ công để chạy lệnh. Sử dụng cờ `--filter` của Turborepo từ root:

| Nhu cầu | Lệnh thực thi | Ý nghĩa |
|---|---|---|
| Chạy dev tất cả apps | `npm run dev` | Khởi động song song toàn bộ dev server |
| Chạy dev riêng web | `npx turbo run dev --filter=web` | Chỉ khởi động `apps/web` |
| Chạy dev riêng API | `npx turbo run dev --filter=api` | Chỉ khởi động `apps/api` |
| Build app kèm theo mọi dependency | `npx turbo run build --filter=...web` | Build cả các package mà `web` phụ thuộc |
| Build những package bị ảnh hưởng bởi PR | `npx turbo run build --filter=...[origin/develop]` | Dành cho CI: chỉ build code đã thay đổi so với `develop` |
| Chạy lint cho shared UI | `npx turbo run lint --filter=@repo/ui` | Chỉ lint package `@repo/ui` |

---

## 7. Package Naming Convention (`@repo/*`)

Tất cả các shared packages trong thư mục `packages/` phải được đặt scope đồng nhất để phân biệt với npm package bên ngoài:

```json
// packages/ui/package.json
{
  "name": "@repo/ui",
  "version": "0.0.1",
  "private": true
}
```

```json
// packages/database/package.json
{
  "name": "@repo/database",
  "version": "0.0.1",
  "private": true
}
```

- Tiền tố thống nhất: `@repo/<package-name>`.
- Mọi internal package mặc định đặt `"private": true` để tránh vô tình publish lên public npm registry.
