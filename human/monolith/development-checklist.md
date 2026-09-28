# Development Checklist

Checklist này dành cho **Developer** trước khi tạo PR hoặc update PR.

Không phải mọi checkbox đều cần áp dụng cho mọi loại change. Chỉ tick những mục phù hợp với scope của Task.

## 1. Requirement & Scope

- [ ] Requirement / Acceptance Criteria đã được hiểu rõ.
- [ ] Implementation đúng scope của Task.
- [ ] Không đưa unrelated change vào cùng PR nếu không cần thiết.
- [ ] Đã xác định các assumption hoặc limitation quan trọng.

## 2. Security — Priority 1

- [ ] Không hardcode secret, credential, token hoặc sensitive configuration.
- [ ] Biến môi trường mới đã được khai báo vào `.env.example`, có boot-time validation; không commit file `.env` thực tế.
- [ ] Input từ user/client đã được validate tại boundary phù hợp.
- [ ] Authentication / authorization được xử lý đúng nếu feature có yêu cầu.
- [ ] Không expose sensitive data trong API response, log hoặc client bundle.
- [ ] Không thêm unsafe HTML / eval / dynamic code execution hoặc pattern tương tự nếu không thực sự cần và không có biện pháp bảo vệ.
- [ ] File upload / external URL / user-provided content đã được xử lý an toàn nếu applicable.
- [ ] Error message không expose internal implementation hoặc sensitive information.
- [ ] Không thêm dependency đáng ngờ hoặc không cần thiết.

## 3. Maintainability — Priority 2

- [ ] Developer đã self-review toàn bộ change.
- [ ] File/module có responsibility rõ ràng.
- [ ] Business logic được đặt đúng Feature / Module.
- [ ] Component / Controller không chứa business logic quá lớn.
- [ ] API response và error format tuân thủ đúng chuẩn envelope (`success`, `data`/`error`, `meta`) và domain error code.
- [ ] Thay đổi database model/schema có kèm file migration tương ứng; tuyệt đối không sửa file migration đã merge.
- [ ] Naming rõ ràng và thống nhất.
- [ ] Không có duplicate logic không cần thiết.
- [ ] Không tạo abstraction chỉ để tránh vài dòng code nếu abstraction làm code khó hiểu hơn.
- [ ] Hạn chế `any`; type được mô tả rõ ràng.
- [ ] Function có quá nhiều parameters đã được xem xét chuyển sang object parameter.
- [ ] Không tạo unnecessary coupling hoặc circular dependency.
- [ ] Không để debug code, `console.log`, `debugger`, dead code hoặc commented-out code không cần thiết.
- [ ] Documentation/comment được cập nhật khi behavior hoặc architecture thay đổi đáng kể.

## 4. Performance — Priority 3

- [ ] Không có API call / database query không cần thiết.
- [ ] Không fetch / select dữ liệu dư thừa.
- [ ] Collection lớn đã có pagination hoặc strategy phù hợp nếu applicable.
- [ ] Không tạo unnecessary render / computation / subscription.
- [ ] Không introduce obvious N+1 hoặc repeated expensive operation.
- [ ] Client-side work đã được xem xét để giảm khi server-side phù hợp.

## 5. Testing & Verification

- [ ] Relevant tests đã được thêm / cập nhật nếu cần.
- [ ] Edge cases quan trọng đã được kiểm tra.
- [ ] Đã manual test / visual verification nếu change yêu cầu.
- [ ] `lint` pass.
- [ ] `typecheck` pass nếu project có.
- [ ] `build` pass nếu project có yêu cầu.
- [ ] Husky checks pass.
- [ ] GitHub Actions / CI pass trước khi merge.

## 6. Git & PR Readiness

- [ ] Branch đúng naming convention.
- [ ] Commit đúng convention.
- [ ] Chỉ stage/commit các file liên quan.
- [ ] PR liên kết đúng Jira Task.
- [ ] PR description mô tả rõ mục tiêu và nội dung change.
- [ ] Có test/evidence/screenshot khi phù hợp.
- [ ] PR không chứa unrelated changes.

## 7. Documentation & Changeset

- [ ] **Real-time Tech Docs**: Nếu thay đổi làm ảnh hưởng kiến trúc, API contract, database hoặc logic hệ thống, file tương ứng trong `docs/tech/` đã được cập nhật ngay trong PR này.
- [ ] **Changeset**: Đã chạy `npx changeset` để sinh file `.changeset/*.md` với mức độ SemVer (`patch`/`minor`/`major`) phù hợp (nếu có thay đổi code runtime).
- [ ] **Release Notes**: Đã cập nhật `docs/release-notes/` nếu PR thuộc đợt phát hành/release version mới.
