# Pull Request Review Checklist

Checklist này dành cho **Reviewer** trước khi Approve PR.

Reviewer nên tập trung vào những thứ automation khó đảm bảo: **logic, architecture, security, maintainability và performance**.

Không review thủ công những vấn đề đã được CI/tooling kiểm tra đầy đủ trừ khi có dấu hiệu bất thường.

## 1. Requirement & Scope

- [ ] Change giải quyết đúng problem cần giải quyết.
- [ ] Scope rõ ràng, không có unrelated change hoặc refactor không cần thiết.
- [ ] PR description đủ thông tin để hiểu change.

## 2. Security — Priority 1

- [ ] Có nguy cơ expose secret / credential / sensitive data không?
- [ ] Có biến môi trường mới nào chưa cập nhật vào `.env.example` hoặc chưa được validate ở boot không?
- [ ] Authentication / authorization có được kiểm tra đúng boundary không?
- [ ] Input validation có đầy đủ cho risk level của feature không?
- [ ] Có khả năng injection, unsafe rendering, path traversal, insecure file handling hoặc tương tự không?
- [ ] Error handling có leak internal details không?
- [ ] Logging có ghi password, token, secret hoặc sensitive data không?
- [ ] Dependency / third-party integration có tạo risk rõ ràng không?

## 3. Maintainability — Priority 2

- [ ] Code có dễ đọc và dễ hiểu không?
- [ ] Responsibility của file/module/component có rõ ràng không?
- [ ] Business logic có nằm đúng Feature / Module không?
- [ ] Có logic bị đặt sai layer không?
- [ ] API response và error handling có tuân thủ đúng chuẩn envelope (`success`, `data`/`error`, `meta`) và domain error code không?
- [ ] Thay đổi database model có kèm migration không? Migration có tuân thủ nguyên tắc bất biến (không sửa migration đã merge) không?
- [ ] Có duplicate code hoặc abstraction không cần thiết không?
- [ ] Naming có phản ánh đúng intent không?
- [ ] Type có rõ ràng không? Có `any` không cần thiết không?
- [ ] Dependency direction có hợp lý không?
- [ ] Có circular dependency hoặc coupling khó kiểm soát không?
- [ ] Change có làm architecture khó mở rộng hoặc khó test hơn không?
- [ ] Error handling và edge cases có phù hợp không?

## 4. Performance — Priority 3

- [ ] Có unnecessary API / network call không?
- [ ] Có unnecessary database query hoặc N+1 không?
- [ ] Có fetch/select dữ liệu dư thừa không?
- [ ] Có unnecessary rendering / expensive computation không?
- [ ] Có ảnh hưởng rõ ràng đến latency, memory hoặc bundle size không?
- [ ] Performance trade-off có hợp lý với scope không?

## 5. Testing & Verification

- [ ] Có test phù hợp với mức độ risk của change không?
- [ ] Regression risk đã được xem xét chưa?
- [ ] Edge cases quan trọng đã được xử lý chưa?
- [ ] CI checks pass.
- [ ] Có evidence / screenshot / test result cần thiết chưa?

## 6. Documentation & Changesets

- [ ] **Living Tech Docs**: Nếu PR thay đổi architecture, endpoints, database hoặc feature logic, file tương ứng trong `docs/tech/` có được cập nhật đồng thời không? (Block nếu tài liệu bị lệch so với code).
- [ ] **Changeset**: PR có kèm file `.changeset/*.md` hợp lệ với mức độ SemVer chính xác không?
- [ ] **Release Notes**: Nếu đây là release PR, đã có file tương ứng trong `docs/release-notes/` chưa?

## 7. Review Outcome

### Comment severity

Ưu tiên phân biệt mức độ để feedback rõ ràng:

- **Blocker / Required:** phải sửa trước khi merge.
- **Suggestion:** đề xuất cải thiện, không nhất thiết block PR.
- **Question:** cần clarification trước khi kết luận.

Reviewer nên comment **cụ thể, giải thích lý do và hướng cải thiện khi có thể**.

### Approve criteria

PR có thể Approve khi:

- Requirement đạt.
- Không còn issue security hoặc correctness cần block.
- Architecture và code quality phù hợp với project baseline.
- Relevant tests/checks đã pass.
- Các required review comments đã được xử lý.
