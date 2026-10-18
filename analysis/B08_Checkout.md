# Analysis: B08 — Checkout Web

| | |
|:---|:---|
| **Tính năng / nền tảng** | FR-08 Checkout Web; API bổ sung cho JWT và tính toàn vẹn tổng tiền |
| **Người thiết kế / ngày** | Codex hỗ trợ MSSV 23120355; tên sinh viên chưa cung cấp; 2026-10-18, Asia/Bangkok |
| **Test case chi tiết** | [B08_Checkout.md](../test-cases/B08_Checkout.md) |
| **Trạng thái** | Draft; 4 Pass, 7 Fail, 1 Blocked, 0 Not Run; Web do người dùng báo cáo, API có CLI evidence; review cuối pending |

## 1. Phạm vi và nguồn

| Nguồn | Phiên bản / trạng thái | Phần đã đọc | Phần thiếu |
|:---|:---|:---|:---|
| [EShop README](../eshop-sut/README.md) | v2.0, 2026-05-14; nguồn oracle | FR-06–09, FR-11, SEC-02; yêu cầu GUI chung | Không thiếu đoạn FR-08 |
| [API specification](../eshop-sut/api_specification.md) | Không ghi phiên bản | Authentication; Products; Cart & Orders | Không định nghĩa response checkout/error đầy đủ |
| [HW02](../2026.HW02.Domain%20Testing_En.md) | HW02-AI | Domain/BVA, audit, review, commit theo bước | Bài giảng chưa có trong workspace |
| Code SUT | Chỉ hỗ trợ bước thực thi | Checkout.jsx, CartContext.jsx; route cart/checkout/my-orders trong server.js | 6 API đã chạy trên bản sao nguyên mã/database riêng; Web chưa chạy; không dùng code làm oracle |

**Trong phạm vi:** đăng nhập trước checkout; tổng tự tính và không sửa trực tiếp; hiển thị đủ sản phẩm; backend tự tính tiền; xóa giỏ sau thành công. API chỉ bổ sung kiểm tra không thể chứng minh bằng UI thông thường.

**Ngoài phạm vi:** coupon FR-09, state machine FR-10, Mobile, CRUD sản phẩm, toàn bộ GUI/accessibility, validation địa chỉ chưa được mô tả. Không thêm giới hạn giá/tổng/số lượng từ code hoặc database.

**Kỹ thuật:** EP và Single Fault cho xác thực và quan hệ tổng client/tổng giỏ; kiểm tra chức năng cho total control và thay đổi giỏ. BVA được đánh giá riêng ở mục 5; không gán nhãn BVA khi spec chưa cung cấp biên thích hợp.

Các mã R1–R5 dưới đây là nhãn truy vết nội bộ cho từng bullet FR-08, không phải mã yêu cầu mới của SUT.

**Quyết định bổ sung được người dùng xác nhận (2026-10-18):** D1: giỏ trống phải bị từ chối, báo lỗi và không tạo đơn. D2: client total khác tổng backend tính từ giỏ phải bị từ chối, báo lỗi và không tạo đơn. Đây là quyết định cho bài làm, không phải nguyên văn FR-08. Không ép câu lỗi/HTTP status cụ thể.

## 2. Input, context và quy tắc

| Mã | Input / context | Loại | Kiểu, đơn vị | Ràng buộc | Nguồn | Explicit / Suy ra |
|:---|:---|:---|:---|:---|:---|:---|
| V1 | Phiên Web / JWT | Context | Đã đăng nhập; Bearer token | R1: chỉ người dùng đăng nhập được checkout; API cần JWT hợp lệ | FR-08 bullet 1; SEC-02 | Explicit |
| V2 | Giỏ hàng của U | Dữ liệu nền | Danh sách sản phẩm, giá và số lượng | R2: tổng tự tính từ giỏ; R3: hiển thị đầy đủ danh sách đặt mua; D1: giỏ có ít nhất 1 dòng | FR-08 bullet 2–3; D1 | Explicit; công thức T = Σ(pᵢ × qᵢ) suy ra từ tổng tiền giỏ |
| V3 | total_amount client gửi | Input API / thử thao tác UI | Số tiền | R2: không sửa trực tiếp trên UI; R4: backend tự tính lại; D2: mismatch phải reject và báo lỗi | FR-08 bullet 2, 4; API 4.3; D2 | Explicit và bổ sung được xác nhận |
| V4 | shipping_address | Input API nền | Chuỗi | API mô tả field; FR-08 không nêu validation | API 4.3 | Explicit về field; không suy ra required/min/max |

**Nominal:** U role=user, JWT hợp lệ; không áp dụng coupon. Giỏ F1 có sản phẩm P giá 100000 ₫, số lượng 2, T=200000 ₫. Địa chỉ API cố định `123 Le Loi, TP.HCM`. U có email/mật khẩu riêng của người chạy; token không ghi vào tài liệu.

**Fixture F2:** P giá 100000 × 2 và Q khác P giá 50000 × 3; T=350000 ₫. Đây là dữ liệu kiểm thử chủ động chuẩn bị, không khẳng định là dữ liệu seed có sẵn. Nếu catalog không có giá này, tạo fixture bằng chức năng Admin với tài khoản Admin, ghi ID thật; nếu không chuẩn bị được thì Blocked. Không đổi oracle theo giá sai của SUT.

| O ID | Observable postcondition | Căn cứ | Cách kiểm chứng |
|:---|:---|:---|:---|
| O1 | Checkout không được hoàn thành nếu không có phiên/JWT hợp lệ | R1, SEC-02 | Response và danh sách đơn trước/sau bằng token hợp lệ riêng |
| O2 | UI hiển thị đủ sản phẩm, số lượng; total bằng T và không sửa trực tiếp | R2–R3 | So sánh giỏ fixture và màn checkout; thao tác gõ/paste thông thường |
| O3 | Nếu tạo đơn, total lưu bằng T từ giỏ, không theo giá trị sửa của client | R4 | GET /api/orders/my-orders, đối chiếu đơn mới với snapshot và công thức độc lập |
| O5 | Giỏ trống hoặc total mismatch bị từ chối, có lỗi và không tạo đơn | D1, D2 | UI/response lỗi và snapshot orders trước/sau |
| O4 | Sau thành công, giỏ tương ứng được xóa | R5: FR-08 bullet 5 | Web: quay về giỏ trong cùng phiên; API: GET /api/cart bằng token U |

O1–O5 là oracle, không tạo input variable hoặc output class.

## 3. Spec gap và quyết định phương pháp

| Mã | Nội dung | Nguồn | Loại | Trạng thái | TC ảnh hưởng |
|:---|:---|:---|:---|:---|:---|
| G1 | Checkout giỏ trống | Người dùng xác nhận D1 | Đã chốt | Reject, báo lỗi, không tạo đơn; max tổng/số dòng không bổ sung | 011–012 |
| G2 | Độ chính xác/làm tròn tiền | FR-08 chưa mô tả | Spec gap | Chưa chốt; dùng phép tính chính xác, ±1 chỉ là bước lấy mẫu | 008–010 |
| G3 | Xử lý total mismatch | Người dùng xác nhận D2 | Đã chốt | Bắt buộc reject, báo lỗi, không tạo đơn; không chấp nhận ignore rồi tạo đơn | 009–010 |
| G4 | Chưa nêu validation địa chỉ, null/missing total, sai kiểu | FR-08/API | Spec gap | Không tạo lớp invalid theo phỏng đoán | Không có |
| G5 | Web và API cart có cách lưu khác nhau trong implementation | Code hỗ trợ thực thi | Quyết định phương pháp | Chuẩn bị và xác minh riêng từng giỏ; API setup không thay cho Web setup | Toàn bộ |
| G6 | Bài giảng chưa có; cần đối chiếu quy ước BVA | HW02 và quyết định D1 | Giới hạn phương pháp | Đã có B1 số dòng theo D1; BVA tiền chờ G2 | 008, 012 |

API body có total_amount không có nghĩa backend được tin giá trị đó. FR-08 quyết định oracle; API chỉ mô tả cách gọi. HTTP status/message/redirect cụ thể chỉ ghi actual nếu spec không bắt buộc.

## 4. Domain Testing: phân vùng tương đương

**Cách chia:** V1 theo điều kiện xác thực; V3 theo quan hệ với T độc lập tính từ V2. Tách thiếu JWT và JWT sai vì hai cách vi phạm SEC-02 cần kiểm riêng. Tách total thấp/cao để phát hiện cả thiếu thu và thu quá. Không coi mọi số dương là total hợp lệ: cùng một số có thể đúng với giỏ này, sai với giỏ khác.

| Class ID | Input/context | Điều kiện | Validity | Đại diện | Nguồn |
|:---|:---|:---|:---|:---|:---|
| C1 | V1 | Phiên/JWT hợp lệ | Hợp lệ | U đã đăng nhập | R1, SEC-02 |
| C2 | V1 | Không có phiên/JWT | Không hợp lệ | Guest Web; bỏ Authorization API | R1, SEC-02 |
| C3 | V1 | JWT không hợp lệ | Không hợp lệ | Bearer invalid.b08.token | SEC-02 |
| C4 | V3 | Client total = T | Nhất quán với giỏ; backend vẫn phải tự tính | 200000 với F1 | R4 |
| C5 | V3 | Client total < T | Không hợp lệ, phải reject và báo lỗi | 199999 với F1 | R4, D2 |
| C6 | V3 | Client total > T | Không hợp lệ, phải reject và báo lỗi | 200001 với F1 | R4, D2 |
| C7 | V2 | Giỏ có 0 dòng | Không hợp lệ, phải reject | Giỏ trống, T=0 | D1 |
| C8 | V2 | Giỏ có ít nhất 1 dòng | Hợp lệ về điều kiện không trống; các điều kiện khác vẫn phải thỏa | F1, 1 dòng P × 2 | D1; FR-08 |

C4–C6 chia quan hệ total với T; D2 xác nhận mismatch phải reject. C7/C8 chia giỏ trống/không trống theo D1; không thêm max. F1/F2 là biến thể fixture.

**Single Fault:** test JWT giữ F1, total=T, address cố định. Test sửa total giữ JWT hợp lệ và giỏ F1; chỉ đổi V3. Khi giỏ đổi, T và total gửi đúng phải đổi theo, vì đây là quan hệ phụ thuộc. Mỗi lần checkout có thể tiêu thụ giỏ nên phải dựng lại fixture cho từng TC, không chạy liên tiếp trên giỏ đã xóa.

## 5. Boundary Value Analysis

D1 đã được người dùng chốt: giỏ phải có ít nhất 1 dòng. Số dòng n là số nguyên, không phụ thuộc độ chính xác tiền G2.

| Boundary ID | Class ID | Quy tắc / nguồn | Đóng/mở | Độ mịn | On-point | Off-point | Expected | TC |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| B1 | C7, C8 | n ≥ 1, D1 | Đóng | 1 dòng | n=1, F1 (P × 2 là 1 dòng) | n=0, giỏ trống | On: checkout hợp lệ thành công; off: reject, báo lỗi, không tạo đơn | 008 on, 012 off |

Dùng 2-point cho B1. TC008/012 cùng kiểm API: JWT hợp lệ, địa chỉ nominal, total khớp giỏ tương ứng; chỉ đổi điều kiện không trống. Giỏ rỗng dùng total=0 đúng tổng Σ rỗng, tránh đồng thời vi phạm D2. TC011 bổ sung giỏ trống trên Web.

D2 xác nhận điều kiện client total = T. Có thể nghiên cứu biên quan hệ tại T, nhưng G2 chưa chốt độ chính xác/tolerance, nên chưa gán thêm BVA tiền cho T−1/T/T+1. Đây vẫn là đại diện Domain; bước 1 ₫ là lựa chọn dữ liệu, không phải đơn vị nhỏ nhất đã được spec quy định. T=200000 từ 100000 × 2; client gửi 199999/200001 đều phải reject, báo lỗi và không tạo đơn theo D2.

Không thêm max tổng/max dòng. B1 có nguồn D1 bổ sung được xác nhận, không ghi nhầm thành nguyên văn FR-08; quy ước BVA còn cần đối chiếu bài giảng.

## 6. Test Matrix

F1/F2, địa chỉ và U được định nghĩa ở mục 2. Chữ đậm là điều kiện khác nominal. Mỗi API case dùng giỏ API riêng đã xác minh và snapshot orders trước khi gửi.

| TC ID | Requirement | Technique | Dữ liệu đầy đủ | Class IDs | Boundary | Validity | Expected |
|:---|:---|:---|:---|:---|:---|:---|:---|
| TC-B08-001 | R2–R3 | Domain | Web; U; F1; không coupon; T=200000 | C1, C4 | — | Hợp lệ | P × 2 hiển thị đủ; total 200000 |
| TC-B08-002 | R1–R5 | Domain | Web; U; **F2**; T=350000; không coupon | C1, C4 | — | Hợp lệ | Đủ P/Q; total 350000; checkout thành công; đơn đúng tiền; giỏ Web rỗng |
| TC-B08-003 | R1 | Domain | Web; **guest**; chuẩn bị F1 nếu UI cho giữ giỏ | C2 | — | Không hợp lệ | Không hoàn thành checkout |
| TC-B08-004 | R2 | Functional | Web; U; F1; **thử gõ/paste 1 vào total** | — | — | Thao tác bị cấm | Total không sửa được; vẫn 200000 |
| TC-B08-005 | R2 | Functional | Web; U; F1, sau đó **q(P)=3** qua giỏ | — | — | Hợp lệ | Mở lại checkout: T=300000 |
| TC-B08-006 | R1, SEC-02 | Domain | API F1; total=200000; address nominal; **thiếu JWT** | C2, C4 | — | Không hợp lệ | Không checkout thành công; không tạo đơn cho U |
| TC-B08-007 | R1, SEC-02 | Domain | API F1; total=200000; address nominal; **JWT sai** | C3, C4 | — | Không hợp lệ | Không checkout thành công; không tạo đơn cho U |
| TC-B08-008 | R4–R5, D1 | Domain + BVA | API; JWT U; F1; total=200000; address nominal | C1, C4, C8 | B1 on, n=1 | Nhất quán | Thành công; đơn total=200000; giỏ API rỗng |
| TC-B08-009 | R4, D2 | Domain | API; JWT U; F1; **total=199999**; address nominal | C1, C5 | — | Không nhất quán | Reject, báo lỗi, không tạo đơn theo D2 |
| TC-B08-010 | R4, D2 | Domain | API; JWT U; F1; **total=200001**; address nominal | C1, C6 | — | Không nhất quán | Reject, báo lỗi, không tạo đơn theo D2 |
| TC-B08-011 | D1 | Domain | Web; U; giỏ trống; không coupon | C1, C7 | — | Không hợp lệ | Từ chối, báo lỗi, không tạo đơn |
| TC-B08-012 | D1 | Domain + BVA | API; JWT U; giỏ trống; total=0; address nominal | C1, C4, C7 | B1 off, n=0 | Không hợp lệ | Từ chối, báo lỗi, không tạo đơn |

## 7. Truy vết và độ phủ

| Requirement / Class | TC IDs | Trạng thái | Lý do |
|:---|:---|:---|:---|
| R1 | 002, 003, 006, 007 | Đã thiết kế | Web guest và API JWT riêng |
| R2 | 001, 002, 004, 005 | Đã thiết kế | TC005 có test nhưng execution bị chặn; đường thêm sản phẩm có tổng cập nhật đúng theo người dùng |
| R3 | 001, 002 | Đã thiết kế | Một và nhiều sản phẩm |
| R4 | 002, 008, 009, 010 | Đã thiết kế | Đúng, thấp và cao hơn T |
| R5 | 002, 008 | Đã thiết kế | Giỏ Web và giỏ API sau thành công |
| SEC-02 | 006, 007 | Đã thiết kế | JWT thiếu/sai |
| C1 | 001, 002, 008, 009, 010 | Đã thiết kế | Token/phiên hợp lệ |
| C2 | 003, 006 | Đã thiết kế | Thiếu xác thực |
| C3 | 007 | Đã thiết kế | JWT sai |
| C4 | 001, 002, 006, 007, 008 | Đã thiết kế | Total bằng T |
| C5 | 009 | Đã thiết kế | Total thấp |
| C6 | 010 | Đã thiết kế | Total cao |
| D1, C7 | 011, 012 | Đã thiết kế | Giỏ trống bị chặn |
| C8, B1 on | 008 | Đã thiết kế | 1 dòng, thành công |
| B1 off | 012 | Đã thiết kế | 0 dòng, reject |
| D2 | 009, 010 | Đã thiết kế | Reject mismatch |
| G2: precision/rounding | — | Chưa chốt | Không tự thêm đơn vị tiền nhỏ nhất |

TC IDs dạng số trong bảng này có prefix TC-B08-. Truy vết là độ phủ thiết kế, không phải kết quả thực thi.

| Chỉ số | Số lượng |
|:---|:---|
| Tổng TC duy nhất | 12 |
| Chỉ Domain | 8 |
| Chỉ BVA | 0 |
| Cả Domain/BVA | 2 |
| Chỉ kỹ thuật bổ sung | 2 |
| Hợp lệ / Không hợp lệ / Thao tác bị cấm | 4 / 7 / 1 |
| Đã hoàn thành (Pass + Fail) / Pass / Fail | 11 / 4 / 7 |
| Blocked / Not Run | 1 / 0 |

N=8+0+2+2=12. Không định lượng mức giảm tổ hợp: miền tiền/giỏ không có tập giá trị hữu hạn được đặc tả. Mismatch phải reject theo D2. Đã có runtime evidence cho 6 API: [summary](../test-cases/evidence/B08/b08_api_execution_summary.md). Web được cập nhật theo báo cáo người dùng: TC001/003 Pass, TC002/004/011 Fail, TC005 Blocked do không đổi số lượng trong giỏ được. Có 5 bug B08 nháp (001–005). Tổng 4 Pass, 7 Fail, 1 Blocked; 12 test đã được thử, 11 hoàn thành Pass/Fail.

## 8. Giới hạn và kỹ thuật bổ sung

Cần bổ sung concurrency/idempotency, checkout nhiều lần, thay đổi giá đồng thời bằng state/concurrency testing khi có mục tiêu và oracle được chốt. Coupon, tồn kho, phí vận chuyển, validation địa chỉ không tự thêm vào phạm vi FR-08. B1 có căn cứ D1; chưa mở rộng BVA tiền khi G2 còn mở.

## 9. Review và thay đổi

| Phiên bản | Ngày | Thay đổi | Lý do | TC ảnh hưởng | Review |
|:---|:---|:---|:---|:---|:---|
| v0.1 | 2026-10-18 | Scope, input/context, oracle và gaps | Spec-first | Toàn bộ | Chưa được sinh viên review |
| v0.2 | 2026-10-18 | 6 lớp, 10 TC, matrix/trace; đánh giá BVA | Không tạo ngưỡng tiền hoặc field code-only | 001–010 | AI tự kiểm tra nhất quán; sinh viên chưa review |
| v0.3 | 2026-10-18 | Áp dụng D1/D2; thêm TC011/012, B1; giữ G2 mở | Người dùng chốt policy G1/G3 | 008–012 | Policy đã xác nhận; test chưa thực thi |

| v0.4 | 2026-10-18 | Chạy 6 API bằng Postman CLI: 2 Pass, 4 Fail; thu log/JSON và bug drafts | Runtime evidence trên backend nguyên mã, database tạm riêng | 006–010, 012 | Kết quả thật; sinh viên cần review |

| v0.5 | 2026-10-18 | Nhập kết quả Web: 2 Pass, 3 Fail, 1 Blocked; thêm bug Web và ảnh có thật | Báo cáo của người dùng; giữ riêng quan sát đường thêm sản phẩm của TC005 | 001–005, 011 | Chưa gán TC005 Pass/Fail vì bước đổi số lượng không thực hiện được |
