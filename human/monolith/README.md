# Monolith Engineering & Code Quality Guidelines

## Scope

Bộ tài liệu này là baseline chung cho các dự án độc lập (**Monolith / Single Repository**) Frontend (Next.js) và Backend (NestJS) của team.

Mục tiêu là thống nhất cách tổ chức source code, development, code review và Git workflow trong khi vẫn đủ linh hoạt cho các dự án outsource có technology hoặc yêu cầu khách hàng khác nhau.

Các rule trong pack này là **default baseline**. Project-specific requirements có thể override khi đã được thống nhất và documented trong project.

---

## Documents

| File | Nội dung |
|---|---|
| [`frontend-code-conventions.md`](frontend-code-conventions.md) | Coding conventions và architecture baseline cho Next.js / Frontend |
| [`backend-code-conventions.md`](backend-code-conventions.md) | Coding conventions và architecture baseline cho NestJS / Backend |
| [`architecture-principles.md`](architecture-principles.md) | Các nguyên tắc bổ sung cho security, maintainability và extensibility |
| [`git-flow.md`](git-flow.md) | Branching, development, PR, merge và release flow |
| [`git-rules.md`](git-rules.md) | Các Git rules bắt buộc, Sample GitHub PR Template và Checklist Rule |
| [`development-checklist.md`](development-checklist.md) | Checklist developer trước khi tạo/update PR |
| [`pr-review-checklist.md`](pr-review-checklist.md) | Checklist reviewer trước khi approve PR |
| [`documentation-conventions.md`](documentation-conventions.md) | Quy chuẩn Technical Documentation (`docs/tech/`), Release Notes và Changesets |

---

## Tooling Baseline

Các repository hiện tại sử dụng hoặc ưu tiên:

- `npm`
- `ESLint`
- `Prettier`
- `Husky`
- `GitHub Actions`
- `@changesets/cli`

---

## Priority

Code Quality được ưu tiên theo thứ tự:

1. **Security**
2. **Maintainability**
3. **Performance**
4. **Correctness và Testing**
EOF
