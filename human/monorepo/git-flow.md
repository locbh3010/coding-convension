# Git Flow & Collaboration (Monorepo)

> Quy trình Git Flow chuẩn hóa cho hệ thống Monorepo sử dụng **Turborepo** và **npm workspaces**.

---

## 1. Standard Flow

```text
Jira Task
   ↓
Create Branch (với scope rõ ràng)
   ↓
Development
   ↓
Local Verification (Turborepo affected checks)
   ↓
Commit (Conventional Commits with Monorepo Scope)
   ↓
Push & Create PR
   ↓
GitHub Actions (Turborepo Affected-only CI & Remote Cache)
   ↓
Code Review (Đánh giá Blast Radius)
   ↓
Approval
   ↓
Squash & Merge vào develop
   ↓
Post-merge / Sync-back (nếu là hotfix)
   ↓
Delete Branch
```

---

## 2. Branch Naming Convention với Scope

Trong Monorepo, tên branch bắt buộc chứa **Scope** (tên app hoặc package chịu ảnh hưởng) để các thành viên nhận biết ngay phạm vi thay đổi:

### Format:
```text
<prefix>/<task-id>-<scope>-<short-description>
```

### Các Scope tiêu biểu:
- `web`: `apps/web`
- `admin`: `apps/admin`
- `api`: `apps/api`
- `worker`: `apps/worker`
- `ui`: `packages/ui`
- `db`: `packages/database`
- `types`: `packages/types`
- `repo`: Toàn bộ repository hoặc root tooling

### Ví dụ chuẩn:
```text
feature/phi-101-web-checkout-flow
fix/phi-102-api-jwt-validation
refactor/phi-103-ui-modal-component
chore/phi-104-db-add-order-index
chore/phi-105-repo-update-turbo-v2
```

---

## 3. Quản lý Blast Radius (Bán kính ảnh hưởng) trong PR

Trong Monorepo, mức độ rủi ro của PR phụ thuộc vào vị trí file thay đổi:

| Vùng thay đổi | Mức độ Blast Radius | Yêu cầu kiểm thử & Review |
|---|---|---|
| **Chỉ thay đổi trong `apps/*`** | **Thấp** (Isolated) | Chỉ cần test và verify app đó. Không ảnh hưởng các app khác. |
| **Thay đổi trong `packages/ui`** | **Trung bình** | Phải verify giao diện trên cả `apps/web` và `apps/admin`. |
| **Thay đổi trong `packages/types`** | **Cao** | Bắt buộc chạy `npx turbo run typecheck` trên toàn bộ monorepo. |
| **Thay đổi trong `packages/database`** | **Rất cao** (Critical) | Phải kiểm tra migration, tính tương thích ngược, verify cả `api` và `worker`. |
| **Thay đổi Root configs / `turbo.json`** | **Toàn diện** | Cần Tech Lead review và full CI pass toàn bộ repository. |

---

## 4. Tối ưu CI/CD với Turborepo (Affected-only Pipeline)

Để CI/CD chạy nhanh và tiết kiệm tài nguyên, GitHub Actions không build lại toàn bộ repository mà sử dụng cờ lọc của Turborepo:

```bash
# Chỉ lint, typecheck, test và build các app/package bị thay đổi hoặc phụ thuộc vào code thay đổi
npx turbo run lint typecheck test build --filter=...[origin/develop]
```

- Khi một PR chỉ sửa `apps/web`, CI sẽ bỏ qua `apps/api` và `apps/worker`.
- Khi một PR sửa `@repo/ui`, CI sẽ tự động build `@repo/ui` cùng các app tiêu thụ nó (`apps/web`, `apps/admin`).

---

## 5. Chiến lược Merge (Merge Strategy)

1. **Feature / Bugfix vào `develop`**:
   - **Bắt buộc dùng `Squash and merge`**.
   - Commit message của PR khi squash phải tuân thủ chuẩn Conventional Commits (ví dụ: `feat(web): add checkout flow (#101)`).
   - Giúp lịch sử git của `develop` luôn phẳng, sạch và dễ revert khi cần.
2. **Release từ `develop` vào `main`**:
   - Dùng **Merge Commit** (hoặc Fast-Forward) để lưu vết lịch sử release version.

---

## 6. Cơ chế Hotfix Sync-back (Bắt buộc)

Khi phát sinh sự cố production và xử lý qua `hotfix/*`:

1. Tạo branch `hotfix/phi-xxx-<scope>-<desc>` từ `main`.
2. Sửa lỗi, test và merge vào `main` (sau khi Tech Lead approve).
3. **Ngay sau khi merge vào `main`, bắt buộc sync-back vào `develop`**:
   ```bash
   git checkout develop
   git pull origin develop
   git merge origin/main
   git push origin develop
   ```
   *Quy tắc này nhằm loại trừ nguy cơ mất code hotfix khi đợt release tiếp theo từ `develop` ghi đè lên `main`.*
EOF
