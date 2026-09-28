# Git Rules & Commit Conventions (Monorepo)

## 1. Mandatory Rules trong Monorepo

- Không push trực tiếp vào `main`, `develop` hoặc `staging`.
- Merge vào protected branch bắt buộc thông qua Pull Request với cấu hình `Squash and merge`.
- Branch name phải chứa Scope (`feature/phi-101-web-checkout-flow`).
- Commit message phải tuân thủ Conventional Commits với **Scope bắt buộc là tên app/package**.
- Không commit secret, file `.env` hoặc credential của bất kỳ app/package nào.
- Không commit `console.log`, `debugger`, temporary code.
- **Cấm `git add .` một cách mù quáng**: Trong monorepo, `git add .` rất dễ stage nhầm các file thay đổi tạm thời ở app khác. Chỉ stage file thuộc workspace đang thực hiện task.
- Không commit file build outputs: `.next/`, `dist/`, `.turbo/`, `node_modules/`.

---

## 2. Commit Convention với Monorepo Scope

Mọi commit bắt buộc tuân theo định dạng:

```text
<type>(<scope>): <short-description>
```

### Các Scopes hợp lệ:
Scope bắt buộc phải là tên của một app hoặc package trong workspace:

- `web`: `apps/web`
- `admin`: `apps/admin`
- `api`: `apps/api`
- `worker`: `apps/worker`
- `ui`: `packages/ui`
- `database`: `packages/database`
- `types`: `packages/types`
- `configs`: `packages/configs`
- `repo`: Các thay đổi cấp độ repository (root `package.json`, `turbo.json`, `.github/`, docs chung)

### Ví dụ chuẩn:
```text
feat(web): add Apple Pay to checkout page
fix(api): prevent null pointer on user profile fetch
refactor(ui): extract input error message component
perf(database): add composite index for order query
chore(repo): upgrade turborepo to v2.1.0
test(worker): add integration tests for email retry queue
```

---

## 3. PR Size & Giới hạn Blast Radius

- **Kích thước PR lý tưởng**: < 400 dòng code thay đổi.
- **Nguyên tắc phân tách PR**:
  - Không gộp các thay đổi của hai ứng dụng độc lập vào cùng một PR (ví dụ: không gộp task sửa UI của `apps/web` với task tối ưu query của `apps/api` trừ khi cả hai cùng implement một tính năng end-to-end liên quan trực tiếp).
  - Khi thay đổi một shared package lớn (`@repo/ui` hoặc `@repo/database`), khuyến nghị tạo PR riêng cho shared package kèm test đầy đủ trước, sau đó mới tạo PR nâng cấp ở các app tiêu thụ.

---

## 4. Protected Branches & CI Policy

- `main` và `develop` là các nhánh được bảo vệ (Protected Branches).
- Điều kiện merge PR:
  1. Ít nhất 1 approval từ Reviewer (hoặc Tech Lead nếu thay đổi shared packages/root configs).
  2. Toàn bộ CI checks của Turborepo (`turbo run lint typecheck test build --filter=...[origin/develop]`) phải PASS.
  3. Tất cả review comments phải được resolve.
  4. Branch phải up-to-date với base branch trước khi merge.
EOF
