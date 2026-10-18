# Tiến độ HW02

MSSV: 23120355. Chỉ tạo Markdown trong workspace; chưa tạo thư mục nộp, PDF hoặc ZIP.

| Feature | Yêu cầu | Domain | BVA | Thực thi | Review sinh viên / AI gap |
|---|---|---|---|---|---|
| A04 | FR-04, SEC-02, SEC-06; bản spec-first đã cập nhật | 8 test chỉ Domain | 4 test thuộc cả Domain/BVA, 0 chỉ BVA | 13 đã chạy: 7 Pass, 6 Fail; 0 Not Run | AI self-review hoàn tất; sinh viên đã cung cấp actual/evidence, review cuối pending |
| B08 | Chưa bắt đầu | Chưa làm | Chưa làm | Chưa chạy | Chưa review |
| C15 | Chưa bắt đầu | Chưa làm | Chưa làm | Chưa chạy | Chưa review |
| D Mobile Register | Chưa bắt đầu | Chưa làm | Chưa làm | Chưa chạy | Chưa review |

Artifact: [A04 analysis](analysis/A04_Profile.md). Commit A04 hoàn tất: `581b52b`; skill/workflow cập nhật: `92bd123`. Test case: [A04](test-cases/A04_Profile.md). Review record: [A04_04_spec_review](audit/A04_04_spec_review.md). Bug drafts: [bug-reports/bugs.md](bug-reports/bugs.md). Evidence: [A04 evidence](test-cases/evidence/A04/). Conversation audit: [conversation_log](audit/conversation_log.md).

## Trạng thái A04 sau spec-first review

13 test duy nhất: 8 chỉ Domain, 4 thuộc cả Domain/BVA và 1 test chức năng bổ sung cho email UI. Không có test chỉ BVA. TC-A04-001 đến TC-A04-013 đã thực thi: Web TC-A04-001–009 có 4 Pass, 5 Fail theo báo cáo; API TC-A04-010–013 có 3 Pass, 1 Fail; tổng 7 Pass, 6 Fail, 0 Not Run. Evidence: [A04 evidence](test-cases/evidence/A04/). BUG-A04-001, BUG-A04-002 và BUG-A04-003 là bản nháp; chưa đăng Issue. V8/V9 đã chuyển thành observable postcondition; id/user_id và các lớp spec gap đã loại khỏi thiết kế. API chỉ còn các kiểm tra SEC-02, SEC-06 và endpoint /me theo contract.

Sinh viên cần đối chiếu bài giảng, review phân loại TC-A04-002/004 và xác nhận evidence/bug draft trước khi chốt.

## Chuẩn bị feature tiếp theo: B08 Checkout

- **Nguồn cần đọc:** [FR-08 trong EShop README](eshop-sut/README.md), [Cart & Orders API](eshop-sut/api_specification.md).
- **Phạm vi spec:** người dùng phải đăng nhập; tổng tiền lấy từ giỏ hàng và backend tự tính lại, không tin `total_amount` do client gửi; sau checkout thành công giỏ hàng được xóa.
- **API liên quan để đối chiếu:** `GET /api/cart`, `POST /api/cart`, `POST /api/checkout`.
- **Trạng thái:** Chưa thiết kế Domain/BVA, chưa tạo `analysis/B08_Checkout.md` hoặc `test-cases/B08_Checkout.md`, chưa thực thi.
- **Bước kế tiếp:** đọc đầy đủ FR-08/API contract và gọi `domain-testing` để lập input/context, postcondition, spec gap, Domain/BVA và test matrix theo spec.

