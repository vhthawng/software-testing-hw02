# Tiến độ HW02

MSSV: 23120355. Chỉ tạo Markdown trong workspace; chưa tạo thư mục nộp, PDF hoặc ZIP.

| Feature | Yêu cầu | Domain | BVA | Thực thi | Review sinh viên / AI gap |
|---|---|---|---|---|---|
| A04 | FR-04, SEC-02, SEC-06; bản spec-first đã cập nhật | 8 test chỉ Domain | 4 test thuộc cả Domain/BVA, 0 chỉ BVA | 13 đã chạy: 7 Pass, 6 Fail; 0 Not Run | AI self-review hoàn tất; sinh viên đã cung cấp actual/evidence, review cuối pending |
| B08 | FR-08 R1–R5, SEC-02; spec-first | 8 Domain + 2 Functional; 10 TC | Đã đánh giá: chưa có ngưỡng spec để gán BVA chuẩn | 0 đã chạy; 10 Not Run | AI kiểm tra ID/card/link; sinh viên chưa review |
| C15 | Chưa bắt đầu | Chưa làm | Chưa làm | Chưa chạy | Chưa review |
| D Mobile Register | Chưa bắt đầu | Chưa làm | Chưa làm | Chưa chạy | Chưa review |

Artifact: [A04 analysis](analysis/A04_Profile.md). Commit A04 hoàn tất: `581b52b`; skill/workflow cập nhật: `92bd123`. Test case: [A04](test-cases/A04_Profile.md). Review record: [A04_04_spec_review](audit/A04_04_spec_review.md). Bug drafts: [bug-reports/bugs.md](bug-reports/bugs.md). Evidence: [A04 evidence](test-cases/evidence/A04/). Conversation audit: [conversation_log](audit/conversation_log.md).

## Trạng thái A04 sau spec-first review

13 test duy nhất: 8 chỉ Domain, 4 thuộc cả Domain/BVA và 1 test chức năng bổ sung cho email UI. Không có test chỉ BVA. TC-A04-001 đến TC-A04-013 đã thực thi: Web TC-A04-001–009 có 4 Pass, 5 Fail theo báo cáo; API TC-A04-010–013 có 3 Pass, 1 Fail; tổng 7 Pass, 6 Fail, 0 Not Run. Evidence: [A04 evidence](test-cases/evidence/A04/). BUG-A04-001, BUG-A04-002 và BUG-A04-003 là bản nháp; chưa đăng Issue. V8/V9 đã chuyển thành observable postcondition; id/user_id và các lớp spec gap đã loại khỏi thiết kế. API chỉ còn các kiểm tra SEC-02, SEC-06 và endpoint /me theo contract.

Sinh viên cần đối chiếu bài giảng, review phân loại TC-A04-002/004 và xác nhận evidence/bug draft trước khi chốt.

## B08 Checkout — thiết kế ban đầu

- [Analysis](analysis/B08_Checkout.md): input/context, postconditions, spec gap, 6 lớp, BVA applicability, matrix và traceability.
- [Test cases](test-cases/B08_Checkout.md): 10 TC (5 Web, 5 API), 8 chỉ Domain và 2 Functional. 0 executed, 10 Not Run; chưa có evidence hay bug runtime B08.
- Nguồn: FR-08, SEC-02 trong EShop README và API Cart & Orders. Không áp dụng coupon trong fixture.
- Commit yêu cầu/scope: `f2bad5b`; thiết kế Domain và đánh giá BVA: `c44688e`. Đây là bản AI ban đầu bất biến để review, không cần nhân bản thành nhiều file audit.
- BVA: FR-08 không nêu min/max/precision; T−1/T/T+1 là dữ liệu Domain theo quan hệ tổng, không gán nhãn BVA. Chưa khẳng định đáp ứng đầy đủ tiêu chí BVA HW; cần đối chiếu bài giảng/căn cứ bổ sung được xác nhận.
- Kiểm tra tài liệu đã đạt: 10 ID khớp matrix/summary/card, đủ 13 trường/card, 10 Not Run, link local hợp lệ. Đây là kiểm tra artifact, không phải thực thi SUT.
- Việc kế tiếp: sinh viên review expected/fixture và giới hạn BVA; chuẩn bị sản phẩm P/Q đúng giá, tài khoản và giỏ độc lập; chạy Web và API rồi cập nhật actual/evidence.
