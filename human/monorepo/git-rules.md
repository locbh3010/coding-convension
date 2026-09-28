# Git Rules & Commit Conventions (Dự án Cá nhân)

## 1. Các Quy tắc Bắt buộc trong Monorepo Cá nhân

- **Không commit thẳng code chưa test vào `main`**: Nên tạo nhánh ngắn hạn (`feat/...`), chạy test cục bộ rồi squash merge.
- **Scope bắt buộc trong Commit**: Mọi commit phải chỉ rõ app hoặc package chịu ảnh hưởng (ví dụ: `feat(web): ...`, `fix(api): ...`).
- **Cấm `git add .` một cách mù quáng**: Trong monorepo, bạn rất dễ sửa thử một file bên `apps/api` trong khi đang làm tính năng cho `apps/web`. Luôn kiểm tra `git status` và chỉ stage các file thuộc app đang làm.
- **Không bao giờ commit file nhạy cảm**: `.env`, `.env.local`, API keys, Stripe secrets của Backend.
- **Không commit build outputs**: `.next/`, `dist/`, `.turbo/`, `node_modules/`.

---

## 2. Commit Convention Chuẩn (Có Monorepo Scope)

Mọi commit tuân theo định dạng:

```text
<type>(<scope>): <short-description>
```

### Các Scopes hợp lệ:
- `web`: `apps/web`
- `admin`: `apps/admin`
- `api`: `apps/api`
- `worker`: `apps/worker`
- `ui`: `packages/ui`
- `database`: `packages/database`
- `types`: `packages/types`
- `configs`: `packages/configs`
- `repo`: Cấu hình cấp repository (root `package.json`, `turbo.json`, CI)

### Ví dụ thực tế:
```text
feat(web): add image generation prompt input
feat(api): implement atomic credit deduction endpoint
feat(worker): integrate Fal.ai Flux model pipeline
fix(api): handle token expiration on refresh
refactor(ui): extract masonry gallery card component
perf(database): add composite index for user generation queries
chore(repo): update Turborepo configuration
```

---

## 3. Tiêu chí Tự Merge (Self-Merge Criteria)

Vì là dự án cá nhân, bạn là người tạo và tự merge code. Để tránh tự làm gãy hệ thống:

1. **Turborepo Affected Checks PASS**:
   ```bash
   npx turbo run lint typecheck test build --filter=...[origin/main]
   ```
2. **Không có circular dependencies hoặc boundary violations** (`packages` không import `apps`).
3. **Database Migration đã đi kèm**: Nếu có sửa Prisma schema thì phải có file migration tương ứng.
4. **Squash and Merge**: Gộp sạch commit khi merge vào nhánh chính.
EOF
