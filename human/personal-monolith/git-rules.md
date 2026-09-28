# Git Rules & Commit Conventions (Dự án Cá nhân & Freelancer)

## 1. Các Quy tắc Bắt buộc

- **Không commit thẳng code bừa bãi vào `main`**: Nên tạo nhánh ngắn hạn (`feat/...`), kiểm tra chạy thử rồi squash merge.
- **Commit có thông điệp rõ ràng**: Tuân thủ chuẩn Conventional Commits.
- **Tuyệt đối không commit file nhạy cảm**: `.env`, `.env.local`, API keys, Database passwords, Payment secrets của khách hàng.
- **Không commit mã nguồn rác**: `console.log`, `debugger`, dead code hoặc commented-out code thừa thãi.
- **Kiểm tra trước khi commit**: Luôn chạy `git status` và `git diff` trước khi commit. Tránh thói quen gõ `git add .` khi chưa xem lại file đã sửa.
- **Không commit thư mục build**: `dist/`, `.next/`, `node_modules/`.

---

## 2. Commit Convention Chuẩn (Conventional Commits)

```text
<type>(<scope>): <short-description>
```
hoặc dạng rút gọn:
```text
<type>: <short-description>
```

### Các Prefix thông dụng:
| Prefix | Ý nghĩa | Ví dụ |
|---|---|---|
| `feat` | Tính năng mới | `feat(auth): add Google login button` |
| `fix` | Sửa lỗi | `fix(cart): prevent negative quantity value` |
| `docs` | Cập nhật tài liệu | `docs: update setup guide in README` |
| `style` | Định dạng, giao diện CSS | `style(ui): adjust navbar padding on mobile` |
| `refactor`| Tối ưu code không đổi tính năng | `refactor: extract user validation helper` |
| `perf` | Tối ưu hiệu năng | `perf: add index for order created_at` |
| `chore` | Nâng cấp package, config | `chore: update dependencies to latest` |

---

## 3. Tiêu chí Tự Merge (Self-Merge Checklist)

Trước khi thực hiện **Squash and Merge** vào `main`:

1. Code đã chạy thử trên máy cục bộ không phát sinh lỗi (`npm run dev`).
2. `npm run lint` và `npm run build` đều PASS, không báo lỗi đỏ.
3. Không có secrets hoặc credentials bị stage nhầm vào git.
4. Nếu có chỉnh sửa database, đã có file migration tương ứng đi kèm.
EOF
