# Git Flow & Collaboration

> Tài liệu này là bản **consolidated** từ các tài liệu Git hiện có của team: Branching Strategy, Commit Convention, Pull Request & Merge và Git Compliance.

## 1. Standard Flow

```text
Jira Task
   ↓
Create Branch
   ↓
Development
   ↓
Self-test / Local Checks
   ↓
Commit
   ↓
Push
   ↓
Create PR
   ↓
GitHub Actions / CI
   ↓
Code Review
   ↓
Fix Review Comments (if any)
   ↓
Approval
   ↓
Merge
   ↓
Post-merge Verification (if applicable)
   ↓
Delete Branch
   ↓
Task Done
```

## 2. Branch Types

| Branch | Purpose |
|---|---|
| `main` | Stable / production code |
| `develop` | Integration branch for active development |
| `staging` | Integration/UAT branch when project uses it |
| `feature/*` | New feature |
| `bugfix/*` | Non-urgent bug fix during development |
| `hotfix/*` | Urgent production fix |
| `release/*` | Release preparation when project uses release branches |

Project có thể không sử dụng toàn bộ branch type. Branch model phải được xác định trong repository/project documentation.

## 3. Branch Naming

Format baseline:

```text
<prefix>/<task-id>-<short-description>
```

Examples:

```text
feature/phi-123-add-login-api
bugfix/phi-456-fix-npe-login
hotfix/phi-789-fix-prod-build
```

Tên branch nên ngắn, rõ nghĩa và trace được về task khi có thể.

## 4. Create Branch

1. Xác định Task/Jira ID.
2. Xác định branch type.
3. Update base branch từ remote.
4. Tạo branch đúng convention.
5. Push branch lên remote khi workflow của project yêu cầu.
6. Liên kết branch với Jira Task.

Ví dụ baseline:

```bash
git checkout develop
git pull
git checkout -b feature/phi-123-add-login-api
git push -u origin feature/phi-123-add-login-api
```

Hotfix có thể branch từ `main` tùy repository flow.

## 5. Development & Commit

Trong quá trình development:

- Chỉ thay đổi những file liên quan đến Task.
- Chạy local checks trước khi push.
- Husky checks phải được giữ và không bypass nếu không có lý do được team chấp nhận.
- Commit message phải theo `git-rules.md`.

## 6. Pull Request Flow

PR nên được tạo sau khi:

- Development hoàn tất ở mức có thể review.
- Self-test đã thực hiện.
- Lint / typecheck / build / test phù hợp đã pass.
- Husky checks pass.

PR description nên có:

- Mục tiêu / problem.
- Nội dung thay đổi chính.
- Jira Task.
- Testing / verification.
- Screenshot / evidence nếu applicable.

## 7. CI & Review

Sau khi tạo hoặc update PR:

1. GitHub Actions / CI chạy các checks đã cấu hình.
2. Reviewer tiến hành review.
3. Nếu có required changes, developer update code.
4. CI chạy lại.
5. Reviewer re-check và Approve khi đạt yêu cầu.

## 8. Merge

Baseline:

- Không merge khi required CI checks fail.
- Không merge khi required approval chưa đủ.
- Không merge khi còn required review changes.
- Merge vào đúng target branch.

Main/develop/staging phải được protected theo policy của repository.

## 9. After Merge

Khi phù hợp với project:

- Verify staging sau merge vào `develop`.
- Deploy production theo release flow khi merge vào `main`.
- Xóa feature/bugfix branch sau khi merge.
- Đảm bảo Jira Task được cập nhật đúng trạng thái.
