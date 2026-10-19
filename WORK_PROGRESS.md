# Tiến độ HW02

MSSV: 23120355. Chỉ tạo Markdown trong workspace; chưa tạo thư mục nộp, PDF hoặc ZIP.

| Feature | Yêu cầu | Domain | BVA | Thực thi | Review sinh viên / AI gap |
|---|---|---|---|---|---|
| A04 | FR-04, SEC-02, SEC-06; bản spec-first đã cập nhật | 8 test chỉ Domain | 4 test thuộc cả Domain/BVA, 0 chỉ BVA | 13 đã chạy: 7 Pass, 6 Fail; 0 Not Run | AI self-review hoàn tất; sinh viên đã cung cấp actual/evidence, review cuối pending |
| B08 | FR-08, SEC-02; giỏ trống/mismatch đã chốt | 8 chỉ Domain + 2 Functional | 2 Domain/BVA: B1 số dòng 0/1 | 4 Pass, 7 Fail, 1 Blocked; 0 Not Run | 5 bug nháp; TC005 vướng FR-07; G2 còn mở |
| C15 | FR-15; FR-12, SEC-02/03 cho quyền ghi | 23 chỉ Domain + 3 Functional | 7 Domain/BVA; tên 0/1/255/256 | 9 Pass, 9 Fail, 3 Blocked, 12 Not Run | 5 bug nháp; UI sửa lỗi, API sửa hợp lệ Pass |
| D Mobile Register | Chưa bắt đầu | Chưa làm | Chưa làm | Chưa chạy | Chưa review |

Artifact: [A04 analysis](analysis/A04_Profile.md). Commit A04 hoàn tất: `581b52b`; skill/workflow cập nhật: `92bd123`. Test case: [A04](test-cases/A04_Profile.md). Review record: [A04_04_spec_review](audit/A04_04_spec_review.md). Bug drafts: [bug-reports/bugs.md](bug-reports/bugs.md). Evidence: [A04 evidence](test-cases/evidence/A04/). Conversation audit: [conversation_log](audit/conversation_log.md).

## Trạng thái A04 sau spec-first review

13 test duy nhất: 8 chỉ Domain, 4 thuộc cả Domain/BVA và 1 test chức năng bổ sung cho email UI. Không có test chỉ BVA. TC-A04-001 đến TC-A04-013 đã thực thi: Web TC-A04-001–009 có 4 Pass, 5 Fail theo báo cáo; API TC-A04-010–013 có 3 Pass, 1 Fail; tổng 7 Pass, 6 Fail, 0 Not Run. Evidence: [A04 evidence](test-cases/evidence/A04/). BUG-A04-001, BUG-A04-002 và BUG-A04-003 là bản nháp; chưa đăng Issue. V8/V9 đã chuyển thành observable postcondition; id/user_id và các lớp spec gap đã loại khỏi thiết kế. API chỉ còn các kiểm tra SEC-02, SEC-06 và endpoint /me theo contract.

Sinh viên cần đối chiếu bài giảng, review phân loại TC-A04-002/004 và xác nhận evidence/bug draft trước khi chốt.

## B08 Checkout — thiết kế ban đầu

- [Analysis](analysis/B08_Checkout.md): input/context, postconditions, spec gap, 8 lớp, BVA applicability, matrix và traceability.
- [Test cases](test-cases/B08_Checkout.md): 12 TC (6 Web, 6 API): 8 chỉ Domain, 2 Domain/BVA và 2 Functional. API: 2 Pass, 4 Fail; Web theo người dùng: 2 Pass, 3 Fail, 1 Blocked. Tổng 4 Pass, 7 Fail, 1 Blocked, 0 Not Run. Có 5 bug nháp.
- Nguồn: FR-08, SEC-02 trong EShop README và API Cart & Orders. Không áp dụng coupon trong fixture.
- Commit yêu cầu/scope: `f2bad5b`; thiết kế Domain và đánh giá BVA: `c44688e`. Đây là bản AI ban đầu bất biến để review, không cần nhân bản thành nhiều file audit.
- BVA: B1 dùng D1 giỏ không trống, n≥1; TC008 on n=1 và TC012 off n=0. Precision/rounding tiền G2 còn mở; chưa gán BVA tiền.
- Kiểm tra cập nhật: 12 ID khớp matrix/summary/card; kết quả 4 Pass, 7 Fail, 1 Blocked, 0 Not Run; link evidence hợp lệ.
- Việc kế tiếp: review 5 bug nháp và bổ sung evidence còn thiếu; TC005 không đổi số lượng trong giỏ được nên Blocked, dù đường thêm sản phẩm đã quan sát tổng cập nhật đúng. G2 rounding vẫn mở.

- Review mới: D1 yêu cầu giỏ trống reject/báo lỗi/không tạo đơn; D2 yêu cầu total mismatch reject/báo lỗi/không tạo đơn. Đây là yêu cầu bổ sung người dùng xác nhận, không nguyên văn FR-08; max không bổ sung.

- API execution: [summary](test-cases/evidence/B08/b08_api_execution_summary.md), [collection](test-cases/evidence/B08/b08_api_postman_collection.json), [bug drafts](bug-reports/bugs.md). Backend nguyên mã chạy trên database tạm riêng; database workspace được bảo toàn. Web đã cập nhật theo báo cáo của người dùng; TC005 Blocked.

## C15 — Quản lý sản phẩm Admin

- [Analysis](analysis/C15_ProductManagement.md): yêu cầu, input/context, postconditions, 14 lớp, BVA, 33-row matrix và truy vết.
- [Test cases](test-cases/C15_ProductManagement.md): 33 test, gồm 16 Web và 17 API (5 API mới được yêu cầu kiểm riêng). Dữ liệu/steps viết cụ thể, mã phân tích chỉ để ở trường truy vết.
- 23 chỉ Domain, 7 Domain+BVA, 3 Functional. Web: 8 Pass, 5 Fail, 3 Blocked; API bổ sung: 1 Pass, 4 Fail; 12 API cũ Not Run. Tổng 9 Pass, 9 Fail, 3 Blocked, 12 Not Run; có 5 bug C15 nháp.
- Commit scope/yêu cầu: 02231a1; commit Domain/cards ban đầu: 657bf69. Dùng lịch sử Git giữ output ban đầu, không tạo nhiều bản sao audit.
- BVA tên dùng ASCII: 0/1 và 255/256. Giá 0 phải reject; giá 1 là đại diện dương, chưa tự coi là đơn vị nhỏ nhất vì spec chưa nêu precision.
- Quyền ghi API kiểm POST/PUT/DELETE, mỗi method có JWT thiếu/sai/token user thường; Admin hợp lệ có đường đối chứng CRUD riêng. Sửa sản phẩm kiểm cả UI ngay sau lưu và backend/refresh cho tất cả ID khác.
- Đã kiểm 33 ID khớp matrix/summary/card, 13 trường/card, thống kê khớp; Web do người dùng báo cáo, API bổ sung có CLI log/JSON.
- Việc kế tiếp: sinh viên review; chuẩn bị danh mục/sản phẩm thử đúng card; chạy Web hoặc giao chạy các API bằng CLI. Không đổi trạng thái khi chưa có kết quả thật.

- [C15 backend recheck](test-cases/evidence/C15/c15_backend_recheck_summary.md): 28 requests, 34 assertions, 4 failed; dữ liệu catalog ban đầu giữ nguyên sau cleanup.
