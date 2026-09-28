# Backend Code Conventions (Monorepo)

## 1. Technology Baseline

Trong kiến trúc Monorepo, các backend applications và data services sử dụng:

- **Framework**: NestJS, TypeScript.
- **Build & Task Runner**: Turborepo (`turbo`), `npm workspaces`.
- **Database & Persistence**: Tách riêng thành package nội bộ `@repo/database` (Prisma / TypeORM / Kysely tùy dự án).
- **Contracts & Shared Schemas**: `@repo/types` (chia sẻ DTOs, Envelopes, Domain Error Codes giữa Backend và Frontend).
- **Background Worker**: `apps/worker` xử lý tác vụ bất đồng bộ (BullMQ, Redis, Cron Jobs).
- **Tooling**: ESLint, Prettier, Husky, GitHub Actions.

---

## 2. Monorepo Backend Topology

```text
├── apps/
│   ├── api/                     # NestJS Main HTTP/REST API
│   │   ├── src/
│   │   │   ├── modules/         # Domain modules (auth, user, order, payment)
│   │   │   ├── common/          # Filters, Interceptors, Guards đặc thù của api
│   │   │   └── main.ts
│   │   └── package.json
│   └── worker/                  # NestJS Background Worker (Xử lý hàng đợi, gửi mail, batch job)
│       ├── src/
│       │   ├── processors/      # BullMQ queue processors
│       │   └── main.ts
│       └── package.json
├── packages/
│   ├── database/                # Database schema, migrations & ORM client instance
│   │   ├── prisma/              # schema.prisma & migrations/
│   │   ├── src/                 # Client wrapper, seeders
│   │   └── package.json
│   └── types/                   # Contracts, Request/Response DTOs, Zod schemas
│       ├── src/
│       │   ├── api/             # Success/Error Envelopes, Pagination DTOs
│       │   ├── auth/            # Auth DTOs
│       │   └── index.ts
│       └── package.json
```

---

## 3. Quản lý Database & Migrations trong Monorepo (`@repo/database`)

### Nguyên tắc tập trung hóa Persistence:
- **Không đặt Prisma schema hoặc TypeORM entities bên trong `apps/api`**: Việc đặt trong `apps/api` sẽ khiến `apps/worker` không tái sử dụng được schema hoặc dẫn đến duplicate model.
- Toàn bộ Schema và Migration phải cư trú tập trung tại `packages/database`.
- Cả `apps/api` và `apps/worker` đều khai báo dependency `"@repo/database": "*"` trong `package.json`.

### Quy tắc Migration:
1. **Thực thi qua Workspace CLI**:
   ```bash
   # Tạo migration mới
   npm run db:migrate:dev --workspace=@repo/database

   # Deploy migration trên môi trường staging/production qua Turborepo
   npx turbo run db:migrate
   ```
2. **Nguyên tắc Bất Biến (Immutability)**:
   - File migration một khi đã merge vào branch chung (`develop`, `staging`, `main`) **tuyệt đối không được sửa đổi**.
   - Muốn thay đổi cấu trúc bảng hoặc sửa lỗi, bắt buộc tạo một file migration mới để roll-forward.
3. **Đồng bộ PR**: Mọi PR có thay đổi database entity/schema **phải đi kèm** file migration tương ứng trong cùng PR.

---

## 4. Bảo mật: Chặn đứng Mass Assignment với Global ValidationPipe

Để ngăn chặn hoàn toàn nguy cơ kẻ tấn công tiêm các thuộc tính ngoài ý muốn (`role: "admin"`, `balance: 999999`), mọi ứng dụng NestJS trong monorepo **bắt buộc phải cấu hình `ValidationPipe` toàn cục với `whitelist`**:

```ts
// apps/api/src/main.ts
import { ValidationPipe } from '@nestjs/common';
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,              // Tự động loại bỏ mọi thuộc tính không khai báo trong DTO
      forbidNonWhitelisted: true,   // Báo lỗi 400 nếu client gửi lên field không hợp lệ
      transform: true,              // Tự động cast kiểu dữ liệu theo DTO
      transformOptions: {
        enableImplicitConversion: true,
      },
    }),
  );

  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

---

## 5. Cấu trúc Module-Based & Controller Rules

- **Module Ownership**: Mỗi business domain thuộc về một module tương ứng (`modules/order`, `modules/auth`).
- **Thin Controller**: Controller chỉ nhận request, validate DTO, gọi Service và trả kết quả. Tuyệt đối không query DB hoặc xử lý logic kinh doanh trong Controller.
  ```text
  HTTP Request → Controller → Service (Use Case) → Database Client (@repo/database)
  ```
- **Xử lý tác vụ nặng qua Worker**: Các tác vụ tốn thời gian (> 200ms) như gửi email, push notification, xử lý file ảnh/video, export excel **không được chạy unawaited trên thread của `apps/api`**. Bắt buộc đẩy job vào queue (Redis/BullMQ) để `apps/worker` xử lý ngầm.

---

## 6. Chuẩn hóa API Contract & Response Envelope

Tất cả API trả về cho Frontend phải tuân theo cấu trúc bao bọc (Envelope) thống nhất đã định nghĩa tại `@repo/types`:

### Standard Success Response (Wrap qua Interceptor):
```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "totalPages": 5
  }
}
```

### Standard Error Response (Wrap qua Exception Filter):
```json
{
  "success": false,
  "error": {
    "code": "ORDER_ALREADY_PAID",
    "message": "Đơn hàng này đã được thanh toán trước đó",
    "details": []
  }
}
```

- **Mã lỗi `code`**: Sử dụng `UPPER_SNAKE_CASE` định danh rõ ràng để Frontend map với từ điển ngôn ngữ (i18n) hoặc xử lý UI state tương ứng.

---

## 7. Environment & Boot-time Validation

- Mỗi app (`apps/api`, `apps/worker`) có file `.env.example` riêng biệt chứa đầy đủ các biến cần thiết.
- Khởi động ứng dụng phải có bước **Boot-time Validation** (dùng Zod hoặc `@nestjs/config` với Joi/class-validator). Nếu thiếu cấu hình bắt buộc, app phải crash ngay lập tức (fail-fast) kèm thông báo rõ ràng.
