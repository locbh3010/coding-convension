# Git Rules & Conventions

## 1. Mandatory Rules

- Không push trực tiếp vào `main`, `develop` hoặc `staging` khi repository đã áp dụng branch protection/PR flow.
- Merge vào protected branch phải thông qua PR/MR theo policy của repository.
- Branch phải tuân thủ naming convention.
- Commit message phải tuân thủ commit convention.
- Không commit secret, credential, token hoặc sensitive configuration.
- Không commit `console.log`, `debugger`, temporary code hoặc dead code.
- Chỉ stage/commit file liên quan trực tiếp đến Task.
- Không commit toàn bộ working tree một cách mù quáng khi chưa kiểm tra `git status` / `git diff`.
- Husky hooks phải được giữ hoạt động.
- CI checks phải pass trước khi merge theo repository policy.

## 2. Branch Convention

```text
<prefix>/<task-id>-<short-description>
```

Supported baseline:

```text
feature/
bugfix/
hotfix/
release/
chore/
```

Examples:

```text
feature/phi-123-add-login-api
bugfix/phi-456-fix-npe-login
hotfix/phi-789-fix-prod-build
```

## 3. Commit Convention

Baseline prefix:

| Prefix | Purpose |
|---|---|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation |
| `style` | Formatting / non-functional style changes |
| `refactor` | Refactor without intended behavior change |
| `test` | Add/update tests |
| `chore` | Maintenance/tooling |

Recommended format:

```text
<type>(<scope>): <short-description>
```

Examples:

```text
feat(auth): add Google login
fix(api): handle null login response
docs: update local setup guide
refactor(cart): extract price calculation
test(auth): add login failure cases
```

Task/Jira ID nên được traceable từ commit hoặc PR theo convention của project.

## 4. Commit Hygiene

Trước khi commit:

```bash
git status
git diff
git diff --staged
```

Review staged content để đảm bảo chỉ commit những gì thuộc Task.

Tránh dùng `git add .` một cách mù quáng khi working tree chứa nhiều thay đổi không liên quan.

Sau commit có thể kiểm tra:

```bash
git log -1
git status
```

## 5. Update Branch

Developer nên giữ branch gần với target/base branch để giảm conflict lớn.

Tùy project policy, có thể dùng rebase hoặc merge từ base branch.

Không rewrite shared history đã được người khác dùng nếu không cần thiết.

Đặc biệt thận trọng với:

```bash
git push --force
```

Không force push protected/shared branch.

## 6. Pull Request & GitHub Conventions (Sample & Checklist Rule)

### 6.1. Quy tắc PR Bắt buộc
- PR phải trỏ đúng source branch và target branch (`feature/*` → `develop`, `hotfix/*` → `main`).
- Bắt buộc gắn Jira Task link tương ứng trong tiêu đề và nội dung.
- Tiêu đề PR phải tuân theo chuẩn Conventional Commits: `<type>(<scope>): <short-description>`.
- Bắt buộc cập nhật tài liệu kỹ thuật trong `docs/tech/` nếu PR có thay đổi kiến trúc/logic/API.
- Bắt buộc chạy `npx changeset` nếu PR có thay đổi code runtime.
- Phải pass 100% các automated CI checks và có ít nhất 1 approval từ Reviewer.

### 6.2. Sample GitHub Pull Request Template
Mọi repository Monolith của team nên thiết lập file `.github/pull_request_template.md` theo mẫu chuẩn hóa dưới đây để developer sử dụng khi mở PR:

```markdown
### Jira Task
- Link Task: [PHI-XXX](https://jira.company.com/browse/PHI-XXX)

---

### Mô tả Thay đổi (Description)
<!-- Tóm tắt ngắn gọn mục tiêu của PR: Giải quyết bài toán gì hoặc thêm tính năng nào? -->

---

### Loại Thay đổi (Type of Change)
- [ ] `feat`: Tính năng mới
- [ ] `fix`: Sửa lỗi
- [ ] `refactor`: Tái cấu trúc mã nguồn (không đổi logic bên ngoài)
- [ ] `perf`: Tối ưu hiệu năng
- [ ] `chore`: Cập nhật cấu hình, dependencies, tooling

---

### Standardize Tech Doc & Changeset (Bắt buộc)
- [ ] **Living Tech Doc**: Đã cập nhật tài liệu kỹ thuật tương ứng trong `docs/tech/` nếu PR làm thay đổi kiến trúc, data flow, API contract hoặc logic nghiệp vụ.
- [ ] **Không lưu Specs**: Đã kiểm tra và đảm bảo TUYỆT ĐỐI KHÔNG lưu specs, PRD, user stories vào repository (chỉ lưu tại Jira/Confluence).
- [ ] **Changeset**: Đã chạy `npx changeset` và commit file `.changeset/*.md` (bắt buộc đối với thay đổi runtime, fix bug hoặc tính năng mới).

---

### Hướng dẫn Triển khai & Triệu chứng Kỹ thuật
- [ ] **Environment Variables**: Có thêm biến môi trường mới không? (Nếu có: đã cập nhật `.env.example` và boot-time validation).
- [ ] **Database Migration**: Có thay đổi database model không? (Nếu có: đã đính kèm file migration và tuân thủ tính bất biến).
- [ ] **Breaking Changes**: Có thay đổi nào làm gãy API contract giữa FE và BE không?

---

### Checklist Rule Tự Kiểm tra & Bằng chứng
- [ ] `npm run lint` PASS không có lỗi.
- [ ] `npm run build` PASS trên máy cục bộ không lỗi type/syntax.
- [ ] Đã self-test các kịch bản chính và edge cases.
- [ ] Đính kèm bằng chứng (Ảnh chụp màn hình / Video / Test log):
  <!-- Đính kèm ảnh hoặc log tại đây -->
```

### 6.3. Checklist Rule Nghiệm thu PR (Reviewer Gates)
Reviewer chỉ Approve PR khi đã tick đủ các chốt chặn:
1. **Tech Doc Parity Rule**: Diff của code phải đi kèm diff của `docs/tech/` tương ứng (nếu có đổi logic/contract). Nếu code đổi mà doc không đổi → **Block PR**.
2. **Zero Specs Rule**: Không có file PRD, acceptance criteria nào được commit vào repo.
3. **Changeset Rule**: File `.changeset/*.md` có mặt và chọn đúng mức SemVer (`patch`, `minor`, `major`).
4. **Migration Immutability Rule**: File migration mới không sửa đè lên migration cũ đã merge.
5. **Quality Gate**: Toàn bộ CI checks (lint, test, build) đều xanh 100%.

## 7. Branch Protection

Repository nên bật branch protection cho các branch chính theo model của project.

Baseline:

- Require PR.
- Require review approval theo project policy.
- Require required CI checks.
- Restrict direct push.
- Restrict force push.
- Không cho xóa protected branch.

## 8. Release & Tagging

Nếu project dùng release branches/tags:

- Release branch/tag phải trace được về release/version.
- Dùng Semantic Versioning khi project áp dụng semantic versioning.
- Release process phải được documented riêng nếu có customer-specific deployment/release requirements.

## 9. Exceptions

Mọi exception đối với Git rules nên:

- Có lý do rõ ràng.
- Giới hạn scope.
- Được Tech Lead / owner của repository chấp nhận khi cần.
- Được document nếu exception mang tính lâu dài.
