# Git Flow & Collaboration (Dự án Cá nhân)

> Quy trình Git Flow tinh gọn, tốc độ cao dành cho **Developer cá nhân (Solo / Indie Dev)** quản lý Monorepo bằng **Turborepo** và **npm workspaces**.

---

## 1. Luồng Làm việc Tinh gọn (Solo Flow)

Không rườm rà Jira ticket, không chờ đợi xét duyệt đa tầng. Quy trình tập trung vào việc **giữ git history sạch, code không bị regression và ship nhanh**:

```text
Tính năng / Bug cần làm
   ↓
Tạo Short-lived Branch (theo scope)
   ↓
Code & Test cục bộ (Turborepo affected checks)
   ↓
Commit (Conventional Commits với Scope)
   ↓
Push & Tạo PR (hoặc Self-merge)
   ↓
CI tự động chạy (Affected-only build & test)
   ↓
Self-Review (Rà soát checklist cá nhân)
   ↓
Squash & Merge vào main/develop
   ↓
Xóa nhánh & Deploy
```

---

## 2. Quy ước Đặt tên Nhánh Tinh gọn (Branch Naming)

Không dùng Task ID của Jira. Tên nhánh ngắn gọn, phản ánh rõ **loại thay đổi** và **app/package chịu tác động**:

### Format:
```text
<type>/<scope>-<short-description>
```

### Các Prefix (`<type>`):
- `feat/`: Tính năng mới
- `fix/`: Sửa lỗi
- `refactor/`: Tái cấu trúc mã nguồn
- `chore/`: Nâng cấp package, config, tooling

### Các Scope (`<scope>`):
- `web`: `apps/web`
- `admin`: `apps/admin`
- `api`: `apps/api`
- `worker`: `apps/worker`
- `ui`: `packages/ui`
- `db`: `packages/database`
- `types`: `packages/types`
- `repo`: Toàn bộ monorepo (root `package.json`, `turbo.json`)

### Ví dụ chuẩn cho dự án cá nhân:
```text
feat/web-prompt-workbench
feat/api-credit-deduction
feat/worker-fal-ai-handler
fix/api-token-expiration
refactor/ui-gallery-masonry
chore/db-add-generation-indexes
chore/repo-upgrade-turbo-v2
```

---

## 3. Quản lý Nhánh Chính (Branch Model)

Tùy vào quy mô và nhu cầu deploy preview, chọn 1 trong 2 mô hình sau:

### Lựa chọn A: 1 Nhánh Duy nhất (`main`) — Khuyến nghị cho dự án cá nhân tốc độ cao
- Mọi nhánh tính năng merge trực tiếp vào `main`.
- `main` luôn ở trạng thái production-ready và tự động deploy (Vercel / Cloud Run).
- Rất nhanh, không cần sync-back giữa các nhánh.

### Lựa chọn B: 2 Nhánh (`develop` + `main`) — Dành cho dự án có môi trường Staging/Preview
- `develop`: Nơi tích hợp code đang phát triển hàng ngày.
- `main`: Chỉ merge từ `develop` khi sẵn sàng release production.
- **Nếu có `hotfix/*` merge vào `main`**: Tự merge ngay lại vào `develop` để tránh mất code.

---

## 4. Tối ưu Thời gian với Turborepo Filter

Một mình làm cả Frontend lẫn Backend, bạn không muốn mỗi lần gõ commit phải chờ build toàn bộ cả repo. Hãy tận dụng Turborepo Filter:

```bash
# Chỉ test và build những gì vừa sửa so với nhánh chính
npx turbo run lint typecheck test build --filter=...[origin/main]

# Chỉ chạy dev app đang code dở
npx turbo run dev --filter=web
```

---

## 5. Chiến lược Merge: Luôn dùng "Squash and Merge"

Dù là dự án cá nhân, **tuyệt đối không dùng merge commit thông thường vào nhánh chính**.

- **Bắt buộc dùng Squash and Merge**: Gộp toàn bộ 5-10 commit vụn vặt trong quá trình dev ("fix typo", "test css") thành **1 commit duy nhất chuẩn Conventional Commits** trên nhánh chính.
- Kết quả: Lịch sử git của bạn luôn đẹp như một cuốn sách, cực kỳ dễ tìm lại lỗi hoặc revert khi cần.
EOF
