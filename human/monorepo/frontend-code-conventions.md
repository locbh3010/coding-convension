# Frontend Code Conventions (Monorepo)

## 1. Technology Baseline

Trong hệ thống Monorepo, các ứng dụng Frontend sử dụng:

- **Framework**: Next.js (App Router), React, TypeScript.
- **Build System & Package Manager**: Turborepo (`turbo`), `npm workspaces`.
- **Shared Packages nội bộ**:
  - `@repo/ui`: Thư viện UI components dùng chung (Buttons, Modals, Form inputs, Design tokens).
  - `@repo/types`: Contracts, DTO types, Zod schemas đồng bộ giữa Frontend và Backend.
  - `@repo/configs`: Shared ESLint, Prettier, Tailwind và TypeScript configurations.
- **Data & State Management**:
  - **Server State**: TanStack Query (fetching, caching, synchronization, mutations).
  - **URL State**: `nuqs` (đồng bộ query parameters với URL).
  - **Global Client State**: Zustand (chỉ dành cho UI state phức tạp như drawer, multi-step wizard, cart local).

---

## 2. Monorepo App vs Package Boundary

### Nguyên tắc bất di bất dịch về ranh giới:
1. **Không import chéo giữa các ứng dụng**: `apps/web` **tuyệt đối không được import** trực tiếp bất kỳ file nào từ `apps/admin` hoặc `apps/api`.
2. **Khai báo dependency tường minh**: Mọi package nội bộ được import phải được khai báo trong `package.json` của app đó:
   ```json
   // apps/web/package.json
   {
     "dependencies": {
       "@repo/ui": "*",
       "@repo/types": "*"
     }
   }
   ```
3. **Cấu hình `transpilePackages` trong Next.js**: Để Next.js compile trực tiếp TypeScript và Tailwind từ internal packages:
   ```ts
   // apps/web/next.config.mjs
   /** @type {import('next').NextConfig} */
   const nextConfig = {
     transpilePackages: ['@repo/ui'],
   };
   export default nextConfig;
   ```

---

## 3. Cấu trúc ứng dụng Frontend (`apps/web`)

Mỗi ứng dụng Frontend bên trong `apps/` được tổ chức theo **Feature-Based Structure**:

```text
apps/web/
├── app/                         # Next.js App Router (Routes, Layouts, Pages)
│   ├── (auth)/
│   ├── (dashboard)/
│   ├── layout.tsx
│   └── page.tsx
├── components/                  # App-specific UI components (không chia sẻ ra ngoài)
│   └── providers.tsx
├── features/                    # Chia theo domain/feature của app
│   ├── cart/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── types/               # App-specific types (kế thừa từ @repo/types)
│   │   └── index.ts
│   └── checkout/
├── configs/                     # App-level config (http client, env, query-client)
├── hooks/                       # Generic hooks nội bộ của app
└── public/
```

### Feature Ownership:
- Logic của feature nào thì đặt trọn vẹn trong feature đó.
- Không đưa feature-specific components vào `@repo/ui` nếu chỉ có `apps/web` sử dụng. Chỉ đưa vào `@repo/ui` khi component thực sự mang tính design system (nút bấm, input, card chung) hoặc được dùng từ 2 apps trở lên.

---

## 4. Shared UI Package (`packages/ui`)

`packages/ui` là Design System dùng chung cho toàn bộ monorepo (ví dụ giữa `apps/web` và `apps/admin`):

```text
packages/ui/
├── src/
│   ├── components/
│   │   ├── button/
│   │   │   ├── button.tsx
│   │   │   └── index.ts
│   │   ├── modal/
│   │   └── input/
│   └── styles/                  # Tailwind base styles, tokens
├── package.json
└── tsconfig.json
```

### Quy tắc xây dựng `@repo/ui`:
- **Chỉ chứa UI thuần (Presentational & Headless)**: Tuyệt đối không chứa business logic, không gọi API, không import service từ các app.
- **Client Component Directive**: Các component có state (`useState`, `useEffect`, `framer-motion`) phải có chỉ thị `'use client';` ở đầu file để tương thích an toàn với Next.js Server Components.
- **Export rõ ràng**: Khai báo `exports` trong `package.json` hoặc cung cấp barrel export tối ưu.

---

## 5. Next.js App Router & Boundary Bảo mật

### Quy tắc Server Components & Client Components:
- **Server-First mặc định**: Các page và layout mặc định là Server Components để giảm bundle size gửi về client và render nhanh hơn.
- **Lá (Leaves) Client Components**: Chỉ đưa `'use client'` xuống các component nhỏ nhất cần tương tác (`Button`, `Dropdown`, `FormInput`). Không biến cả layout hoặc page lớn thành Client Component.

### An toàn cho Server Actions (`'use server'`):
- Server Actions thực chất là **Public HTTP POST Endpoints**.
- Bắt buộc kiểm tra **Authentication/Session** ngay đầu hàm Server Action.
- Bắt buộc validate dữ liệu đầu vào bằng Zod schema từ `@repo/types` trước khi thực thi nghiệp vụ:
  ```ts
  // ❌ NGUY HIỂM: Tin tưởng client gửi id và role
  export async function updateUserAction(formData: FormData) {
    'use server';
    await db.user.update(...);
  }

  // ✅ CHUẨN: Xác thực session và validate input
  export async function updateUserAction(input: unknown) {
    'use server';
    const session = await auth();
    if (!session) throw new UnauthorizedException();

    const data = UpdateUserSchema.parse(input);
    return await userService.update(session.user.id, data);
  }
  ```

---

## 6. Form Handling & Schema Validation

- **Thư viện chuẩn**: Sử dụng `react-hook-form` kết hợp `@hookform/resolvers/zod`.
- **Tái sử dụng Schema từ `@repo/types`**: Form validation schema trên Frontend phải kế thừa hoặc sử dụng trực tiếp từ schema định nghĩa ở `@repo/types`, đảm bảo khi Backend thay đổi contract, Frontend sẽ báo lỗi TypeScript ngay lúc build.

---

## 7. State Management Guidelines

| Loại State | Giải pháp | Quy tắc sử dụng |
|---|---|---|
| **Server State** | TanStack Query | Quản lý dữ liệu từ backend, caching, revalidation, optimistic updates |
| **URL State** | `nuqs` | Bộ lọc (filters), phân trang (page, limit), tìm kiếm (search query), tabs |
| **Local UI State** | `useState`, `useReducer` | Trạng thái đóng/mở dropdown, hover, form input tức thời |
| **Global Client State** | `zustand` | Trạng thái phức tạp xuyên suốt app (Drawer giỏ hàng, Auth modal, Theme, Stepper) |

*Không dùng React Context làm Global State Store cho các state biến động nhanh vì sẽ gây re-render cascade toàn bộ ứng dụng.*

---

## 8. TypeScript & Shared Contracts

- Tận dụng triệt để `@repo/types` để đảm bảo **End-to-End Type Safety** giữa Next.js và NestJS.
- Hạn chế tối đa `any`. Sử dụng `unknown` kết hợp type guards hoặc Zod parse khi nhận dữ liệu không chắc chắn từ bên ngoài.
- Dùng `type` cho data shapes, DTOs, union types. Dùng `interface` khi cần kế thừa hoặc declaration merging.

---

## 9. Performance & Core Web Vitals

- **Hình ảnh**: Bắt buộc dùng `next/image` thay vì thẻ `<img>` thuần để tự động tối ưu format (WebP/AVIF) và responsive sizing.
- **Font chữ**: Sử dụng `next/font` (Google Fonts hoặc Local Fonts) để zero-layout-shift và tự động self-host.
- **Dynamic Imports**: Sử dụng `next/dynamic` cho các component nặng (Chart, Rich Text Editor, Map) để giảm kích thước Initial Bundle.
