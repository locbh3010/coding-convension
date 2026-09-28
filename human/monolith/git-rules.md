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

## 6. Pull Request Rules

PR phải:

- Có đúng source branch và target branch.
- Gắn Jira Task tương ứng.
- Có description rõ ràng.
- Có testing / verification.
- Có screenshot/evidence khi UI hoặc behavior cần chứng minh.
- Pass required CI checks.
- Có required reviewer approval.
- Không còn required review comments.

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
