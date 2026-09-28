# Frontend Code Conventions

## 1. Technology Baseline

Frontend hiện tại sử dụng:

- Next.js
- React
- TypeScript
- npm
- ESLint
- Prettier
- Husky
- GitHub Actions
- TanStack Query
- nuqs

Project có thể bổ sung technology khác tùy theo yêu cầu khách hàng.

---

## 2. Feature-Based Structure

Ưu tiên tổ chức source code theo **feature/domain** thay vì gom toàn bộ code theo technical type.

```text
apps/
components/
├── providers/
│   ├── providers.tsx
│   └── index.ts
├── component-a/
│   ├── component-a.tsx
│   └── index.ts
features/
├── auth/
├── cart/
├── ...
configs/
hooks/
utils/
constants/
```

### Feature ownership

Business logic thuộc feature nào thì ưu tiên đặt trong feature đó.

```text
features/
└── cart/
    ├── components/
    ├── hooks/
    ├── services/
    ├── types/
    ├── utils/
    └── index.ts
```

Không đưa feature-specific logic vào `hooks/`, `utils/` hoặc `components/` ở root chỉ để tiện import.

Chỉ đưa code vào shared/root layer khi code đó thực sự generic và được nhiều feature sử dụng.

---

## 3. `configs/`

`configs/` chứa application configuration và initialization setup.

Ví dụ:

```text
configs/
├── query-client.ts
├── http.ts
├── env.ts
└── index.ts
```

Có thể chứa:

- TanStack Query client configuration
- HTTP client configuration
- Environment configuration
- Application-level setup

Không đặt business logic hoặc feature-specific utility vào `configs/`.

`configs/` không phải folder miscellaneous.

---

## 4. File Naming

### Components

```text
modal.tsx              → Modal
user-card.tsx          → UserCard
```

Tên file phản ánh component chính được export.

### Hooks

```text
use-debounce.ts        → useDebounce
use-cart.ts            → useCart
use-add-to-cart.ts     → useAddToCart
```

### General files

Dùng `kebab-case` cho tên file nếu không có convention riêng của framework/tool.

```text
api-client.ts
format-currency.ts
query-client.ts
user-profile.tsx
```

---

## 5. Single Responsibility

Một file nên có **một trách nhiệm chính / một concern chính**.

Tránh gom tất cả vào một file:

```text
❌ component
❌ type / interface
❌ hooks
❌ constants
❌ validation
❌ business logic
```

Thay vào đó, tách theo responsibility.

Ví dụ:

```text
features/cart/
├── components/cart-item.tsx
├── hooks/use-cart.ts
├── hooks/use-add-to-cart.ts
├── types/cart.ts
├── constants/cart.ts
└── utils/calculate-cart-total.ts
```

Không tách file chỉ để làm project phức tạp hơn. Chỉ extract khi việc tách giúp code rõ ràng, reusable hoặc dễ maintain hơn.

---

## 6. Component Rules

Component nên tập trung vào rendering và UI composition.

Không nhồi business logic lớn trực tiếp vào component.

Ưu tiên:

```text
Component
   ↓
Hook / Feature Logic
   ↓
Service / Server Function
   ↓
External API
```

Khi logic có thể reuse hoặc khiến component khó đọc, extract thành custom hook, utility hoặc service phù hợp.

Ưu tiên hook nhỏ theo responsibility:

```text
use-cart
use-add-to-cart
use-remove-from-cart
```

thay vì một hook lớn xử lý mọi loại cart operation khi điều đó làm tăng complexity.

---

## 7. TypeScript Conventions

- Hạn chế tối đa `any`.
- Không dùng `any` nếu có thể mô tả type một cách hợp lý.
- Khi bắt buộc phải dùng `any`, phải có lý do rõ ràng và giới hạn scope sử dụng.
- Ưu tiên `type` cho type aliases, object shapes, unions và compositions.
- Không dùng `interface` chỉ vì thói quen; chỉ dùng khi semantics hoặc khả năng declaration merging / extension thực sự phù hợp.

### Enum vs Union

Không áp dụng rule "luôn dùng enum" hoặc "hạn chế union" một cách tuyệt đối.

- Dùng `enum` khi cần một nhóm named constants có runtime value rõ ràng.
- Dùng union type khi chỉ cần compile-time constraint và không cần runtime object.

Ví dụ:

```ts
type Status = 'pending' | 'success' | 'failed'
```

hoặc:

```ts
enum OrderStatus {
  PENDING = 'PENDING',
  SUCCESS = 'SUCCESS',
  FAILED = 'FAILED',
}
```

Chọn cách phù hợp với use case, không chọn chỉ vì convention.

---

## 8. Function Parameters & Return Values

Khi function nhận nhiều parameters có quan hệ với nhau, ưu tiên object parameter.

```ts
// ❌
createUser(name, email, age, role)

// ✅
createUser({
  name,
  email,
  age,
  role,
})
```

Điều này giúp call site dễ đọc hơn và thuận tiện mở rộng API của function.

Tương tự, khi function cần trả nhiều giá trị có quan hệ với nhau, ưu tiên một object rõ nghĩa thay vì tuple hoặc nhiều return riêng lẻ nếu object giúp code dễ đọc hơn.

---

## 9. State, Query & Data Fetching

### Query State

Sử dụng `nuqs` cho query state khi state cần được đồng bộ với URL.

Không tự triển khai lại query-string parsing bằng native methods khi `nuqs` đã phù hợp với use case.

### Server-first

Ưu tiên tận dụng server-side capabilities của Next.js.

- Hạn chế gọi API trực tiếp từ client nếu có thể xử lý an toàn và hợp lý trên server.
- Ưu tiên Server Components / Server Functions / server-side data access theo architecture của project.
- Client-side fetching chỉ nên dùng khi thực sự cần client interaction hoặc data lifecycle ở browser.
- Socket / realtime là trường hợp đặc biệt và có thể cần client-side communication.

### TanStack Query

Sử dụng TanStack Query để quản lý **server state**, bao gồm fetching, caching, synchronization và mutations.

Không sử dụng TanStack Query như một general-purpose client state manager.

---

## 10. Constants

Tên constant sử dụng `UPPER_SNAKE_CASE`.

```ts
const MAX_RETRY_COUNT = 3
const DEFAULT_PAGE_SIZE = 20
```

Không biến `constants/` thành nơi chứa mọi giá trị literal. Constant nên có ý nghĩa và được reuse khi cần.

---

## 11. Imports & Dependencies

- Tránh circular dependency.
- Tránh import từ deep internal path khi package/module đã cung cấp public entry point.
- Ưu tiên `index.ts` làm public API của feature/module khi điều đó giúp giảm coupling.
- Không tạo barrel export quá lớn nếu nó gây circular dependency hoặc ảnh hưởng bundle/build không cần thiết.

---

## 12. Error & Async Handling

- Không silently swallow errors.
- Error state phải được xử lý ở layer phù hợp.
- Không hiển thị raw internal error hoặc sensitive information cho end user.
- Không tạo loading / retry / error logic lặp lại ở nhiều component nếu có thể centralize hợp lý.

---

## 13. Performance Baseline

- Tránh unnecessary client rendering.
- Tránh unnecessary API calls.
- Chỉ fetch dữ liệu cần thiết.
- Tránh duplicate fetching khi có thể reuse server state hoặc cache.
- Tránh expensive computation trong render.
- Sử dụng memoization / lazy loading / virtualization khi có lý do thực tế, không lạm dụng trước khi có nhu cầu.

---

## 14. Security Baseline

- Không hardcode secret, token hoặc credential.
- Không expose sensitive information qua client bundle.
- Validate và sanitize input tại boundary phù hợp.
- Không tin tưởng dữ liệu từ client.
- Authentication và authorization phải được kiểm tra ở server-side boundary cần thiết.
- Không render / inject untrusted HTML nếu không có lý do chính đáng và biện pháp bảo vệ phù hợp.

Project có requirement security sâu hơn phải có project-specific security guideline.
