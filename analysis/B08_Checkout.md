# Analysis: B08 — Checkout Web

| | |
|:---|:---|
| **Tính năng / nền tảng** | FR-08 Checkout Web; API bổ sung cho JWT và tính toàn vẹn tổng tiền |
| **Người thiết kế / ngày** | Codex hỗ trợ MSSV 23120355; tên sinh viên chưa cung cấp; 2026-10-18, Asia/Bangkok |
| **Test case chi tiết** | [B08_Checkout.md](../test-cases/B08_Checkout.md) |
| **Trạng thái** | Draft; chưa thực thi, chưa được sinh viên review |

## 1. Phạm vi và nguồn

| Nguồn | Phiên bản / trạng thái | Phần đã đọc | Phần thiếu |
|:---|:---|:---|:---|
| [EShop README](../eshop-sut/README.md) | v2.0, 2026-05-14; nguồn oracle | FR-06–09, FR-11, SEC-02; yêu cầu GUI chung | Không thiếu đoạn FR-08 |
| [API specification](../eshop-sut/api_specification.md) | Không ghi phiên bản | Authentication; Products; Cart & Orders | Không định nghĩa response checkout/error đầy đủ |
| [HW02](../2026.HW02.Domain%20Testing_En.md) | HW02-AI | Domain/BVA, audit, review, commit theo bước | Bài giảng chưa có trong workspace |
| Code SUT | Chỉ hỗ trợ bước thực thi | Checkout.jsx, CartContext.jsx; route cart/checkout/my-orders trong server.js | Chưa chạy SUT; không dùng code làm oracle |

**Trong phạm vi:** đăng nhập trước checkout; tổng tự tính và không sửa trực tiếp; hiển thị đủ sản phẩm; backend tự tính tiền; xóa giỏ sau thành công. API chỉ bổ sung kiểm tra không thể chứng minh bằng UI thông thường.

**Ngoài phạm vi:** coupon FR-09, state machine FR-10, Mobile, CRUD sản phẩm, toàn bộ GUI/accessibility, validation địa chỉ chưa được mô tả. Không thêm giới hạn giá/tổng/số lượng từ code hoặc database.

**Kỹ thuật:** EP và Single Fault cho xác thực và quan hệ tổng client/tổng giỏ; kiểm tra chức năng cho total control và thay đổi giỏ. BVA được đánh giá riêng ở mục 5; không gán nhãn BVA khi spec chưa cung cấp biên thích hợp.

Các mã R1–R5 dưới đây là nhãn truy vết nội bộ cho từng bullet FR-08, không phải mã yêu cầu mới của SUT.

## 2. Input, context và quy tắc

| Mã | Input / context | Loại | Kiểu, đơn vị | Ràng buộc | Nguồn | Explicit / Suy ra |
|:---|:---|:---|:---|:---|:---|:---|
| V1 | Phiên Web / JWT | Context | Đã đăng nhập; Bearer token | R1: chỉ người dùng đăng nhập được checkout; API cần JWT hợp lệ | FR-08 bullet 1; SEC-02 | Explicit |
| V2 | Giỏ hàng của U | Dữ liệu nền | Danh sách sản phẩm, giá và số lượng | R2: tổng tự tính từ giỏ; R3: hiển thị đầy đủ danh sách đặt mua | FR-08 bullet 2–3 | Explicit; công thức T = Σ(pᵢ × qᵢ) suy ra từ tổng tiền giỏ |
| V3 | total_amount client gửi | Input API / thử thao tác UI | Số tiền | R2: không sửa trực tiếp trên UI; R4: backend tự tính lại, không tin số client | FR-08 bullet 2, 4; API 4.3 | Explicit |
| V4 | shipping_address | Input API nền | Chuỗi | API mô tả field; FR-08 không nêu validation | API 4.3 | Explicit về field; không suy ra required/min/max |

**Nominal:** U role=user, JWT hợp lệ; không áp dụng coupon. Giỏ F1 có sản phẩm P giá 100000 ₫, số lượng 2, T=200000 ₫. Địa chỉ API cố định `123 Le Loi, TP.HCM`. U có email/mật khẩu riêng của người chạy; token không ghi vào tài liệu.

**Fixture F2:** P giá 100000 × 2 và Q khác P giá 50000 × 3; T=350000 ₫. Đây là dữ liệu kiểm thử chủ động chuẩn bị, không khẳng định là dữ liệu seed có sẵn. Nếu catalog không có giá này, tạo fixture bằng chức năng Admin với tài khoản Admin, ghi ID thật; nếu không chuẩn bị được thì Blocked. Không đổi oracle theo giá sai của SUT.

| O ID | Observable postcondition | Căn cứ | Cách kiểm chứng |
|:---|:---|:---|:---|
| O1 | Checkout không được hoàn thành nếu không có phiên/JWT hợp lệ | R1, SEC-02 | Response và danh sách đơn trước/sau bằng token hợp lệ riêng |
| O2 | UI hiển thị đủ sản phẩm, số lượng; total bằng T và không sửa trực tiếp | R2–R3 | So sánh giỏ fixture và màn checkout; thao tác gõ/paste thông thường |
| O3 | Nếu tạo đơn, total lưu bằng T từ giỏ, không theo giá trị sửa của client | R4 | GET /api/orders/my-orders, đối chiếu đơn mới với snapshot và công thức độc lập |
| O4 | Sau thành công, giỏ tương ứng được xóa | R5: FR-08 bullet 5 | Web: quay về giỏ trong cùng phiên; API: GET /api/cart bằng token U |

O1–O4 là oracle, không tạo input variable hoặc output class.

## 3. Spec gap và quyết định phương pháp

| Mã | Nội dung | Nguồn | Loại | Trạng thái | TC ảnh hưởng |
|:---|:---|:---|:---|:---|:---|
| G1 | Chưa nêu checkout giỏ trống, min/max tổng, max số dòng | FR-08 | Spec gap | Ngoài bộ chính; không tự tạo expected reject | Không có |
| G2 | Chưa nêu độ chính xác/làm tròn tiền | FR-08 | Spec gap | Dùng fixture có phép tính chính xác; ±1 chỉ là bước lấy mẫu | 008–010 |
| G3 | Chưa nêu reject toàn request hay ignore total_amount khi bị sửa | FR-08 | Spec gap | Chỉ ép invariant O3; reject hoặc tính lại đều phù hợp | 009–010 |
| G4 | Chưa nêu validation địa chỉ, null/missing total, sai kiểu | FR-08/API | Spec gap | Không tạo lớp invalid theo phỏng đoán | Không có |
| G5 | Web và API cart có cách lưu khác nhau trong implementation | Code hỗ trợ thực thi | Quyết định phương pháp | Chuẩn bị và xác minh riêng từng giỏ; API setup không thay cho Web setup | Toàn bộ |
| G6 | Bài giảng chưa có; FR-08 không nêu ngưỡng số phù hợp BVA | HW02/FR-08 | Giới hạn phương pháp | Mục 5 giải thích; chưa tuyên bố đáp ứng đầy đủ BVA homework | Toàn bộ |

API body có total_amount không có nghĩa backend được tin giá trị đó. FR-08 quyết định oracle; API chỉ mô tả cách gọi. HTTP status/message/redirect cụ thể chỉ ghi actual nếu spec không bắt buộc.

## 4. Domain Testing: phân vùng tương đương

**Cách chia:** V1 theo điều kiện xác thực; V3 theo quan hệ với T độc lập tính từ V2. Tách thiếu JWT và JWT sai vì hai cách vi phạm SEC-02 cần kiểm riêng. Tách total thấp/cao để phát hiện cả thiếu thu và thu quá. Không coi mọi số dương là total hợp lệ: cùng một số có thể đúng với giỏ này, sai với giỏ khác.

| Class ID | Input/context | Điều kiện | Validity | Đại diện | Nguồn |
|:---|:---|:---|:---|:---|:---|
| C1 | V1 | Phiên/JWT hợp lệ | Hợp lệ | U đã đăng nhập | R1, SEC-02 |
| C2 | V1 | Không có phiên/JWT | Không hợp lệ | Guest Web; bỏ Authorization API | R1, SEC-02 |
| C3 | V1 | JWT không hợp lệ | Không hợp lệ | Bearer invalid.b08.token | SEC-02 |
| C4 | V3 | Client total = T | Nhất quán với giỏ; backend vẫn phải tự tính | 200000 với F1 | R4 |
| C5 | V3 | Client total < T | Không nhất quán; không được dùng làm tiền lưu | 199999 với F1 | R4 |
| C6 | V3 | Client total > T | Không nhất quán; không được dùng làm tiền lưu | 200001 với F1 | R4 |

C4–C6 là cách tách quan hệ phục vụ kiểm tra tính toàn vẹn; không phải đặc tả validation body bắt buộc reject C5/C6. Không tạo lớp theo kích thước giỏ khi spec chưa nêu giới hạn. F1/F2 là biến thể fixture để kiểm tra phép nhân/cộng và hiển thị đầy đủ danh sách.

**Single Fault:** test JWT giữ F1, total=T, address cố định. Test sửa total giữ JWT hợp lệ và giỏ F1; chỉ đổi V3. Khi giỏ đổi, T và total gửi đúng phải đổi theo, vì đây là quan hệ phụ thuộc. Mỗi lần checkout có thể tiêu thụ giỏ nên phải dựng lại fixture cho từng TC, không chạy liên tiếp trên giỏ đã xóa.

## 5. Boundary Value Analysis

**Kết quả phân tích biên:** FR-08 không nêu miền số [min,max], ngưỡng tiền hay giới hạn số lượng/dòng. Vì vậy không có biên đóng/mở và độ mịn được đặc tả để áp dụng BVA chuẩn cho các field của B08.

| Boundary ID | Quy tắc xem xét | Đóng/mở | Độ mịn | On/off-point | Quyết định |
|:---|:---|:---|:---|:---|:---|
| Không gán ID | total backend = Σ(pᵢ × qᵢ) | Quan hệ bằng nhau, không phải giới hạn chấp nhận total client | Spec chưa nêu | Không tuyên bố có off-point sát nhất | Kiểm bằng Domain quan hệ C4–C6 |
| Không gán ID | Min/max tổng hoặc giỏ trống | Chưa có trong FR-08 | Chưa có | Không xác định | Không tạo expected từ giả định |

**2-point/3-point:** không áp dụng BVA 2/3-point khi chưa có biên hợp lệ. Bộ ba T−1/T/T+1 trong TC008–010 là lấy mẫu quan hệ, không phải ba test BVA. Bước 1 ₫ là lựa chọn dữ liệu chính xác cho fixture; không khẳng định hệ thống chỉ nhận tiền nguyên hoặc đây là số thực sát T nhất.

T=200000 được tính độc lập từ 100000 × 2. Với client total=199999 hoặc 200001, nếu SUT tạo đơn thì total lưu vẫn phải bằng 200000. Nếu SUT từ chối do mismatch thì vẫn phù hợp invariant R4; TC008 riêng kiểm tra đường thành công nominal. Một lần chạy với total đúng không thể tự chứng minh backend tính lại, nên cần cả TC009 và TC010.

**Giới hạn với HW:** đề yêu cầu BVA cho từng feature; phần này ghi trung thực kết quả đánh giá khả năng áp dụng, chưa tuyên bố B08 đáp ứng đầy đủ tiêu chí BVA. Cần đối chiếu bài giảng hoặc yêu cầu bổ sung được giảng viên xác nhận nếu muốn mở rộng BVA; không lấy ngưỡng coupon FR-09 của teammate để lấp khoảng trống.

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
| TC-B08-008 | R4–R5 | Domain | API; JWT U; F1; total=200000; address nominal | C1, C4 | — | Nhất quán | Thành công; đơn total=200000; giỏ API rỗng |
| TC-B08-009 | R4 | Domain | API; JWT U; F1; **total=199999**; address nominal | C1, C5 | — | Không nhất quán | Reject hoặc tạo đơn total=200000; không lưu 199999 |
| TC-B08-010 | R4 | Domain | API; JWT U; F1; **total=200001**; address nominal | C1, C6 | — | Không nhất quán | Reject hoặc tạo đơn total=200000; không lưu 200001 |

## 7. Truy vết và độ phủ

| Requirement / Class | TC IDs | Trạng thái | Lý do |
|:---|:---|:---|:---|
| R1 | 002, 003, 006, 007 | Đã thiết kế | Web guest và API JWT riêng |
| R2 | 001, 002, 004, 005 | Đã thiết kế | Phép tính, read-only và thay đổi fixture |
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
| BVA min/max, rounding | — | Chưa xác định | Spec không có ngưỡng/độ mịn |

TC IDs dạng số trong bảng này có prefix TC-B08-. Truy vết là độ phủ thiết kế, không phải kết quả thực thi.

| Chỉ số | Số lượng |
|:---|:---|
| Tổng TC duy nhất | 10 |
| Chỉ Domain | 8 |
| Chỉ BVA | 0 |
| Cả Domain/BVA | 0 |
| Chỉ kỹ thuật bổ sung | 2 |
| Hợp lệ / Không hợp lệ xác thực / Mismatch total / Thao tác bị cấm | 4 / 3 / 2 / 1 |
| Đã thực thi / Pass / Fail / Not Run | 0 / 0 / 0 / 10 |

N=8+0+0+2=10. Không định lượng mức giảm tổ hợp: miền tiền/giỏ không có tập giá trị hữu hạn được đặc tả. Mismatch không ép thành invalid request phải bị reject. Chưa có bug runtime hoặc evidence; không đọc code rồi gán Fail.

## 8. Giới hạn và kỹ thuật bổ sung

Cần bổ sung concurrency/idempotency, checkout nhiều lần, thay đổi giá đồng thời bằng state/concurrency testing khi có mục tiêu và oracle được chốt. Coupon, tồn kho, phí vận chuyển, validation địa chỉ không tự thêm vào phạm vi FR-08. Giới hạn BVA ở mục 5 là thiếu căn cứ spec, không có nghĩa bỏ qua yêu cầu BVA trong rubric.

## 9. Review và thay đổi

| Phiên bản | Ngày | Thay đổi | Lý do | TC ảnh hưởng | Review |
|:---|:---|:---|:---|:---|:---|
| v0.1 | 2026-10-18 | Scope, input/context, oracle và gaps | Spec-first | Toàn bộ | Chưa được sinh viên review |
| v0.2 | 2026-10-18 | 6 lớp, 10 TC, matrix/trace; đánh giá BVA | Không tạo ngưỡng tiền hoặc field code-only | 001–010 | AI tự kiểm tra nhất quán; sinh viên chưa review |
