# Architecture & Code Quality Principles

Tài liệu này bổ sung các principles để source code dễ mở rộng, maintain và review. Đây là baseline; project-specific architecture có thể override khi đã được thống nhất.

## 1. Security First

Security là priority cao nhất trong code quality.

Các điểm cần mặc định kiểm tra:

- Trust boundary: mọi dữ liệu từ client/external system đều được xem là untrusted.
- Secrets không nằm trong source code.
- Authorization được kiểm tra, không chỉ authentication.
- Sensitive data chỉ được expose khi cần.
- Error/logging không leak secrets hoặc internal details.
- Dependencies và external integrations phải có lý do sử dụng rõ ràng.

## 2. Maintainability Before Cleverness

Ưu tiên:

```text
Readable > Clever
Simple > Over-engineered
Explicit > Implicit magic
Consistent > Personal preference
```

Code tốt không phải code có nhiều abstraction nhất; code tốt là code mà member khác có thể đọc, sửa và mở rộng với effort hợp lý.

## 3. Feature Ownership

Business logic nên thuộc về feature/module sở hữu nó.

Shared layer chỉ nên chứa code thực sự shared.

Tránh tạo:

```text
utils/
helpers/
services/
common/
```

như một nơi chứa mọi thứ không biết đặt ở đâu.

## 4. Dependency Direction

Ưu tiên dependency direction rõ ràng.

```text
Shared / Common
      ↓
Feature / Module
      ↓
Application Logic
      ↓
Infrastructure / External Services
```

Không để shared/common layer phụ thuộc ngược vào business feature cụ thể.

Tránh circular dependencies.

## 5. Abstraction Rule

Chỉ tạo abstraction khi nó giải quyết ít nhất một vấn đề thực tế:

- Reuse có ý nghĩa.
- Giảm coupling.
- Đơn giản hóa test.
- Che giấu infrastructure detail.
- Cho phép thay đổi implementation dễ hơn.

Không tạo abstraction chỉ để "trông clean".

## 6. Public API of a Feature/Module

Feature/module nên expose một public surface rõ ràng khi project có đủ complexity.

Ưu tiên import:

```ts
import { CartService } from '@/modules/cart'
```

thay vì chọc sâu:

```ts
import { CartService } from '@/modules/cart/internal/services/cart.service'
```

Không cần ép barrel export cho mọi folder nếu nó gây circular dependency hoặc build/bundle issue.

## 7. Configuration vs Constants

Phân biệt:

- `configs/` → configuration / initialization / environment-dependent setup.
- `constants/` → constant values của application/domain.
- `utils/` → generic reusable functions.
- `features/` / `modules/` → business logic.

Mọi biến môi trường phải có schema validation lúc khởi động (boot-time validation) để fail-fast khi thiếu cấu hình, tránh crash runtime bất ngờ. Đồng thời `.env.example` phải luôn phản ánh đủ các key cần thiết.

## 8. Validation at Boundaries

Validation nên nằm gần boundary nơi dữ liệu đi vào system.

Ví dụ:

```text
External Input
   ↓
Validation / Sanitization
   ↓
Business Logic
   ↓
Persistence / External Service
```

Không dựa vào validation ở UI như một security control cho backend.

## 9. Observability

Project nên có strategy phù hợp cho:

- Error logging.
- Request tracing / correlation ID nếu cần.
- Important business events.
- Health checks.

Không log sensitive data chỉ để "debug cho tiện".

## 10. Performance Principles

Không premature optimize.

Ưu tiên:

1. Correctness.
2. Security.
3. Maintainability.
4. Identify actual bottleneck.
5. Optimize với data/evidence phù hợp.

Các bottleneck cần đặc biệt chú ý:

- Repeated network requests.
- N+1 query.
- Unbounded collection.
- Expensive computation trong render/request.
- Unnecessary serialization/deserialization.
- Memory-heavy processing.

## 11. Testing Strategy

Không đặt mục tiêu "test mọi dòng code" một cách máy móc.

Test nên tập trung vào:

- Business-critical behavior.
- High-risk logic.
- Regression-prone areas.
- Important edge cases.
- Integration boundaries.

Project-specific testing strategy nên xác định unit/integration/e2e tùy risk và delivery requirement.

## 12. API / Contract Ownership

API contract là boundary quan trọng giữa các system.

Hệ thống nên chuẩn hóa cấu trúc dữ liệu giao tiếp (Response Envelope `{ success, data, error, meta }`) và Domain Error Codes nhất quán để Frontend/Client dễ xử lý logic và hiển thị lỗi.

Khi thay đổi:

- Request schema.
- Response schema.
- Error schema.
- Authentication behavior.
- Pagination.

phải xem xét backward compatibility và downstream impact.

Không assume breaking change chỉ ảnh hưởng service/repository hiện tại nếu API được consume bởi system khác.

## 13. Project-Specific Overrides

Nếu customer/project yêu cầu khác baseline này:

- Giữ baseline làm default.
- Document override trong project.
- Chỉ override phần cần thiết.
- Không silent deviation.
