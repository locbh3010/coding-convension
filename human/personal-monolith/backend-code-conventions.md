# Backend Code Conventions

## 1. Technology Baseline

Backend hiện tại sử dụng:

- NestJS
- TypeScript
- npm
- ESLint
- Prettier
- Husky
- GitHub Actions

Database / ORM / ODM có thể thay đổi theo từng project outsource. Vì vậy document này chỉ định nghĩa **architecture và coding principles**, không ép TypeORM, Prisma, Mongoose hay database cụ thể.

---

## 2. Module-Based Structure

Backend tổ chức theo **module / feature**.

```text
modules/
├── auth/
├── cart/
├── user/
└── ...
configs/
common/
```

Mỗi module sở hữu business logic của domain tương ứng.

Ví dụ:

```text
modules/cart/
├── controllers/
├── services/
├── dto/
├── types/
├── utils/
└── ...
```

Structure chi tiết có thể thay đổi theo độ phức tạp của module.

---

## 3. `common/`

`common/` chỉ chứa các concern dùng chung hoặc cross-cutting.

Ví dụ:

```text
common/
├── decorators/
├── guards/
├── interceptors/
├── filters/
├── pipes/
└── helpers/
```

Không đặt business logic của một module cụ thể vào `common/` chỉ để reuse một lần.

Ví dụ:

```text
[BAD] common/cart-price.ts

[GOOD] modules/cart/utils/calculate-price.ts
```

---

## 4. `configs/`

`configs/` chứa application/infrastructure configuration và initialization setup.

Ví dụ:

```text
configs/
├── env.ts
├── app.ts
├── database.ts
├── prisma.ts
├── auth.ts
└── index.ts
```

Có thể chứa:

- Environment configuration
- Database configuration
- ORM/ODM initialization
- Application configuration
- Authentication configuration
- External HTTP client configuration

Không đặt business logic hoặc feature-specific helper vào `configs/`.

Chỉ tạo file technology-specific khi project thực sự sử dụng technology đó.

### Environment Configuration Rules

- **Bắt buộc `.env.example`**: Khi thêm biến môi trường mới, bắt buộc cập nhật vào `.env.example` với giá trị placeholder hoặc mô tả định dạng.
- **Không commit file env thực tế**: `.env`, `.env.local`, `.env.*.local` phải nằm trong `.gitignore`.
- **Boot-time Validation**: Toàn bộ biến môi trường phải được validate ngay khi ứng dụng khởi động (sử dụng Zod, Joi hoặc class-validator). Nếu thiếu biến bắt buộc hoặc sai format, app phải crash ngay lập tức (fail-fast) kèm thông báo rõ ràng, không để xảy ra lỗi runtime trong quá trình xử lý request.

---

## 5. Controller Rules

Controller phải **thin**.

Controller nên tập trung vào:

- Nhận request.
- Bind / validate input.
- Gọi application/business layer phù hợp.
- Trả response.

Không đặt business logic lớn hoặc database access trực tiếp trong controller.

```text
HTTP Request
    ↓
Controller
    ↓
Service / Use Case
    ↓
Repository / Data Access
    ↓
Database / External Service
```

---

## 6. Service / Business Logic

Service hoặc use-case layer chịu trách nhiệm cho business logic.

Một service không nên trở thành "god service" chứa toàn bộ logic của nhiều domain khác nhau.

Khi complexity tăng, tách logic theo responsibility hoặc use case.

```text
services/
├── create-order.service.ts
├── cancel-order.service.ts
└── calculate-order-total.service.ts
```

Tên và structure thực tế có thể thay đổi theo project.

---

## 7. DTO & Input Validation

- DTO dùng để mô tả request contract ở application boundary.
- Validate input trước khi đưa vào business logic.
- Không assume dữ liệu từ client là trusted.
- Không dùng database entity/model như request DTO một cách mặc định nếu việc này làm lẫn transport contract với persistence model.

Validation strategy có thể dùng package/framework phù hợp với project.

---

## 8. TypeScript Conventions

- Hạn chế tối đa `any`.
- Ưu tiên `type` khi phù hợp.
- `interface` chỉ dùng khi semantics của interface hoặc extensibility thực sự cần thiết.
- `enum` và union type được lựa chọn theo runtime requirement.
- Function có nhiều input nên ưu tiên object parameter.

```ts
// [BAD]
createUser(name, email, age, role)

// [GOOD]
createUser({
  name,
  email,
  age,
  role,
})
```

---

## 9. File Responsibility

Một file nên có **một responsibility / concern chính**.

Tránh một file đồng thời chứa:

- DTO
- type
- constant
- validation
- business logic
- database access
- utility

Tách các concern khi việc tách giúp code dễ đọc, test và maintain hơn.

Không tách file một cách máy móc nếu code quá nhỏ và việc tách làm tăng complexity.

---

## 10. Dependency Direction

Module boundary phải rõ ràng.

- `common/` không phụ thuộc business logic của một module cụ thể.
- Một module không tự ý import internal implementation của module khác nếu không cần.
- Hạn chế circular dependency.
- Shared contracts nên có một public entry point rõ ràng.

Khi module A cần functionality của module B, ưu tiên phụ thuộc vào public service/contract của B thay vì chọc sâu vào internal files của B.

---

## 11. Database & ORM/ODM

Không ép database convention chung cho mọi project.

Khi project sử dụng TypeORM / Prisma / Mongoose hoặc technology khác, cần bổ sung project-specific rule cho:

- Schema/model naming.
- Migration.
- Query patterns.
- Transaction.
- Indexing.
- Relation loading.
- Pagination.

Baseline chung:

- Không đưa raw query hoặc database logic vào Controller.
- Tránh N+1 query.
- Không fetch dữ liệu không cần thiết.
- Transaction phải được dùng khi business operation yêu cầu atomicity.
- Schema/data validation phải phù hợp với business contract.

### Migration Conventions

- **Tự động hóa qua CLI**: Mọi thay đổi schema database phải được tạo và thực thi qua migration CLI (TypeORM, Prisma migrate, v.v.), không thao tác trực tiếp trên database server.
- **Naming Convention**: File migration phải tuân theo format timestamp và mô tả ngắn: `<timestamp>-<task-id>-<short-description>.[ts|sql]`.
- **Nguyên tắc Bất Biến (Immutability)**: File migration một khi đã merge vào branch chung (`develop`, `staging`, `main`) **tuyệt đối không được sửa đổi**. Nếu muốn thay đổi schema hoặc sửa lỗi, bắt buộc tạo một file migration mới để roll forward.
- **Đồng bộ PR**: Mọi PR có thay đổi database entity/model/schema **phải đi kèm** file migration tương ứng trong cùng PR.
- **Rollback / Down Migration**: Khi ORM/công cụ hỗ trợ, migration phải cung cấp kịch bản rollback (`down` method hoặc SQL tương đương) để phục vụ revert khi cần.

---
## 12. Error Handling

- Dùng exception/error type phù hợp với application context.
- Không trả raw internal error, stack trace hoặc infrastructure details cho client production.
- Log internal details ở server-side khi cần, nhưng không log secret hoặc sensitive data.
- Chuẩn hóa lỗi thông qua Exception Filter (`common/filters/http-exception.filter.ts`).

### Standard Error Envelope

Mọi response lỗi trả về client phải tuân thủ format thống nhất:

```json
{
  "success": false,
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Chi tiết lỗi thân thiện với người dùng",
    "details": []
  }
}
```

- **HTTP Status Code**: Thể hiện đúng tầng giao thức (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 422 Unprocessable Entity, 500 Internal Server Error).
- **`code` (Domain Error Code)**: Định danh lỗi cụ thể theo định dạng `UPPER_SNAKE_CASE` (ví dụ: `INVALID_CREDENTIALS`, `CART_ITEM_OUT_OF_STOCK`, `ORDER_ALREADY_PAID`). Frontend căn cứ mã này để xử lý UI/i18n.
- **`details`**: Mảng chứa danh sách lỗi chi tiết (thường dùng cho validation errors dạng `{ field, message }`).
- **Internal Error (500)**: Chỉ trả message chung "Internal server error" cho client. Stack trace và nguyên nhân kỹ thuật chỉ được log ở server kèm correlation/request ID.

---

## 13. Security Baseline

- Không hardcode secret / credential.
- Environment secret phải nằm ngoài source code.
- Authentication và authorization phải được kiểm tra ở server-side.
- Validate input tại boundary.
- Không tin tưởng dữ liệu từ client.
- Không trả sensitive fields nếu response không yêu cầu.
- Cân nhắc rate limiting, abuse protection và payload limits cho endpoint phù hợp.
- File upload phải có validation về type, size, filename và storage policy nếu feature hỗ trợ upload.
- Không log password, access token, refresh token, secret key hoặc sensitive personal data.

---

## 14. Performance Baseline

- Tránh unnecessary database queries.
- Tránh N+1.
- Chỉ select / load fields cần thiết.
- Pagination cho collections lớn.
- Cân nhắc caching khi có use case rõ ràng.
- Không tối ưu premature; ưu tiên correctness và maintainability trước, sau đó tối ưu bottleneck thực tế.

---

## 15. API Contract

API request/response phải rõ ràng, nhất quán và predictable.

### Standard Success Envelope

Tất cả response thành công (2xx) trả về client nên tuân thủ format thống nhất (khuyến nghị tự động wrap qua NestJS Interceptor `common/interceptors/transform.interceptor.ts`):

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

- **`success`**: `true` khi request thành công.
- **`data`**: Chứa payload kết quả (object hoặc array). Controller chỉ cần return payload `data` hoặc `{ data, meta }`, Interceptor sẽ wrap envelope bên ngoài.
- **`meta`**: Thông tin bổ sung như phân trang (pagination), tổng số bản ghi (bỏ qua nếu response là single resource).

### Pagination Convention

- Query parameters chuẩn hóa:
  - `page`: Số trang hiện tại (1-indexed, mặc định: 1).
  - `limit`: Số bản ghi mỗi trang (mặc định: 20, tối đa: 100 để tránh cạn kiệt tài nguyên).
- Đối với collection lớn hoặc dữ liệu realtime nhiều biến động, ưu tiên sử dụng cursor-based pagination (`cursor`, `limit`).

Khi dự án outsource có yêu cầu riêng biệt từ khách hàng (như GraphQL hoặc contract format cố định khác), team có thể override và ghi rõ trong tài liệu dự án.
