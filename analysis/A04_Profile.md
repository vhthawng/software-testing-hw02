# Analysis: A04 — Quản lý hồ sơ cá nhân

| | |
|:---|:---|
| **Tính năng / nền tảng** | Hồ sơ cá nhân; Web / API; localhost:5173 và localhost:3000; chưa chạy SUT, build chưa xác nhận |
| **Người thiết kế / ngày** | Codex hỗ trợ MSSV 23120355; họ tên sinh viên chưa cung cấp; tạo/cập nhật 2026-10-18 (Asia/Bangkok) |
| **Test case chi tiết** | [A04_Profile.md](../test-cases/A04_Profile.md) |
| **Trạng thái** | Draft — chưa được sinh viên review |

## 1. Phạm vi và nguồn

**Tài liệu đã đọc**

| Nguồn | Phiên bản / trạng thái | Phần đã đọc | Phần chưa đọc / thiếu |
|:---|:---|:---|:---|
| [Đặc tả EShop](../eshop-sut/README.md) | v2.0, 2026-05-14; nguồn yêu cầu | FR-04, FR-21–24, SEC-02/04/06 và tổng quan | Không dùng code để thay thế spec |
| [API docs](../eshop-sut/api_specification.md) | Không ghi phiên bản | Authentication, Users 2.1–2.2 | Chỉ dùng xác định giao thức |
| [Profile.jsx](../eshop-sut/frontend-web/src/pages/Profile.jsx), [App.jsx](../eshop-sut/frontend-web/src/App.jsx), [server.js](../eshop-sut/backend/server.js) | Working tree hiện tại, chỉ đọc tĩnh | Route /profile, form cập nhật, GET/PUT /api/users/me, JWT middleware | Chưa thực thi, chưa kiểm chứng runtime |
| [Đề HW02](../2026.HW02.Domain%20Testing_En.md) | HW02-AI | Quy trình, kết quả bắt buộc và Git | Deadline xem Moodle |
| Bài giảng | Chưa có trong workspace đã tìm | Chưa đối chiếu | Cần đối chiếu kỹ thuật trước chốt bài |
| Bản chạy thực | Chưa thực thi | Không | Chưa có ảnh/log/version runtime |

Spec là oracle cho expected. Sai lệch code/UI là dấu hiệu cần thực thi xác nhận, không phải lý do đổi spec. Mã V/C/B/A/TC dưới đây là mã phân tích nội bộ; FR và SEC là mã gốc.

- **Trong phạm vi:** cập nhật họ tên, SĐT, địa chỉ; email chỉ đọc qua UI; JWT, ownership, role; persistence của thay đổi được chấp nhận.
- **Ngoài phạm vi:** lịch sử/hủy đơn dù nằm cùng trang (FR-10/11); đổi mật khẩu (FR-03); phân hệ Mobile; toàn bộ bảo mật và toàn bộ GUI của ứng dụng.
- **Kỹ thuật áp dụng:** Domain Testing (EP + BVA + Single Fault). SĐT có miền ký tự/tiền tố và ngưỡng độ dài rõ; token/ownership/role có lớp điều kiện quyền truy cập. Bổ sung kiểm thử chức năng, phân quyền và GUI cho yêu cầu không có biên số.
- **GUI liên quan:** FR-21 ngôn ngữ, nút xanh, một h1 và Tab; FR-22 dấu * cho trường thực sự bắt buộc, email type=email, vị trí lỗi. Chưa có quy định trường nào bắt buộc của form profile; không suy ra từ HTML required. SEC-04 hiển thị an toàn cần test bổ sung riêng.

## 2. Biến và quy tắc

| Mã | Biến / đầu ra | Loại | Kiểu, đơn vị | Ràng buộc | Nguồn | Explicit / Suy ra |
|:---|:---|:---|:---|:---|:---|:---|
| V1 | name | Nhập trực tiếp | Chuỗi | Cho phép cập nhật họ tên; không có min/max ở FR-04 | FR-04 | Explicit |
| V2 | phone | Nhập trực tiếp | Chuỗi chữ số; độ dài ký tự | Bắt đầu bằng 0, dài 10–11 chữ số | FR-04 | Explicit |
| V3 | shipping_address | Nhập trực tiếp | Chuỗi | Cho phép cập nhật địa chỉ mặc định; không có min/max | FR-04 | Explicit |
| V4 | email | Dữ liệu nền / UI | Chuỗi | Không cho thay đổi qua giao diện | FR-04 | Explicit |
| V5 | Authorization | Dữ liệu nền | JWT hoặc thiếu/sai | Cập nhật hồ sơ yêu cầu đăng nhập / JWT hợp lệ | FR-04, SEC-02 | Explicit |
| V6 | Subject/token và target yêu cầu | Dữ liệu nền | ID tài khoản | Chỉ sửa hồ sơ chính mình; payload không được chuyển đối tượng sang user khác | FR-04 | Explicit; cách thử qua payload là thiết kế |
| V7 | role trong payload / DB | Nhập qua API / đầu ra | user, admin | Không tự đổi role qua profile API | FR-04, SEC-06 | Explicit |
| V8 | Hồ sơ lưu và đọc lại | Đầu ra | name/phone/address/email/role | Update được chấp nhận phải lưu đúng; refresh/GET dùng kiểm chứng | FR-04 | Suy ra từ “cập nhật” |
| V9 | Kết quả từ chối, dữ liệu trước/sau | Đầu ra | Response và snapshot | Input vi phạm không được lưu trái spec; sửa bị cấm không được có hiệu lực | FR-04, SEC-02/06 | Suy ra từ ràng buộc |

**Nominal (dữ liệu nền hợp lệ):** user U = tài khoản test riêng có role=user; name=`Nguyễn Văn An`, phone=`0912345678`, shipping_address=`123 Lê Lợi, Quận 1, TP.HCM`; email và role giữ nguyên; token hợp lệ của U; không có id/user_id/role trong payload cơ sở. Chuẩn bị user W khác U cho test ownership. Không giả định ID cố định. Chụp snapshot U/W trước mỗi test. Payload API là JSON chuỗi, không chuyển SĐT thành số để tránh mất số 0 đầu.

## 3. Giả định và câu hỏi mở

| Mã | Nội dung | Nguồn | Trạng thái | TC bị ảnh hưởng nếu sai |
|:---|:---|:---|:---|:---|
| A1 | FR-04 chưa nêu phone/name/address có bắt buộc không; null/missing/rỗng/chỉ khoảng trắng | FR-04 không quy định | Chưa rõ | Chưa dùng nhóm này để chốt Pass/Fail |
| A2 | Không có min/max tên/địa chỉ, trim, chuẩn hóa +84 hoặc dấu phân cách SĐT | FR-04 | Chưa rõ | Chỉ chọn tên/địa chỉ bình thường; định dạng có khoảng trắng cần phân biệt trim và nội dung |
| A3 | Không có message/HTTP status cụ thể khi lỗi, hoặc reject/ignore field role/target bị cấm | FR-04, SEC-06 | Chưa rõ | Kiểm invariant dữ liệu/quyền; không ép một chuỗi/status |
| A4 | “Chữ số” chưa nói rõ Unicode; dùng ASCII 0–9 cho bộ chính | FR-04; lựa chọn dữ liệu | Đề xuất | Unicode digits để kiểm thử thăm dò sau |
| A5 | Email chỉ cấm sửa qua UI; chưa đủ căn cứ kết luận mọi thay đổi email qua API đều là lỗi FR-04 | FR-04 | Chưa rõ | Không tạo expected API email bị cấm từ suy đoán |
| A6 | Chưa có bài giảng; chọn biên độ dài đóng và quy ước on/off của skill, cần đối chiếu | Quy trình môn học / skill | Đề xuất | BVA cần sinh viên review |
| A7 | Người review, trình duyệt/build và thông tin W chưa được cung cấp | Trạng thái hiện tại | Chưa rõ | Điền trước khi chạy; không bịa |

*Message lỗi đề xuất:* không đặt câu chữ riêng. Chỉ yêu cầu phản hồi phù hợp và dữ liệu không vi phạm; nguyên văn/vị trí chỉ chốt khi spec có quy định tương ứng.

## 4. Domain Testing: phân vùng tương đương

**Cách chia lớp:** tách điều kiện phone theo độ dài, prefix, chữ và hai nhóm ký tự phân cách; không gộp tất cả thành “phone sai”. Đây là Splitting Principle để giữ các lỗi validation có thể khác nhau. Chia token, role và target theo điều kiện quyền; chia đầu ra theo dữ liệu cần kiểm chứng. Dữ liệu mơ hồ tách riêng và chưa dùng làm oracle lỗi. Bảng phân vùng theo thuộc tính, không phải tích Descartes của mọi biến.

**Quan hệ phụ thuộc và Single Fault:** phone negative giữ đầu 0 và độ dài hợp lệ trừ đúng chiều đang thử. Case thiếu/sai token giữ payload hợp lệ. Role và target chỉ thêm một field bị cấm, token U vẫn hợp lệ. Chỉ đổi một trường tên/địa chỉ khi kiểm cập nhật riêng. Không ép user W và U cùng ID. Mỗi nhóm phone chạy riêng Web/API để tránh UI che validation backend.

| Class ID | Biến / đầu ra | Điều kiện | Validity | Đại diện / cách đạt | Nguồn |
|:---|:---|:---|:---|:---|:---|
| C1 | V2 | 0 đầu, ASCII digits, độ dài 10–11 | Hợp lệ | 0912345678 | FR-04 |
| C2 | V2 | 0 đầu, digits, độ dài 1–9 | Không hợp lệ | 091234567 | FR-04 |
| C3 | V2 | 0 đầu, digits, độ dài ≥12 | Không hợp lệ | 091234567890 | FR-04 |
| C4 | V2 | digits, dài 10–11, đầu khác 0 | Không hợp lệ | 1912345678 | FR-04 |
| C5 | V2 | 0 đầu, dài 10–11, chứa chữ | Không hợp lệ | 091234567a | FR-04 |
| C6 | V2 | 0 đầu, dài 10–11, chứa dấu phân cách | Không hợp lệ | 09123-5678 | FR-04 |
| C7 | V2 | 0 đầu, dài 10–11, khoảng trắng nội bộ | Không hợp lệ | 09123 5678 | FR-04 |
| C8 | V1 | Tên thông thường có dấu, không rỗng | Hợp lệ | Nguyễn Thị Bình | FR-04 |
| C9 | V3 | Địa chỉ thông thường không rỗng | Hợp lệ | 45 Nguyễn Huệ, Quận 1, TP.HCM | FR-04 |
| C10 | V4 | Thao tác sửa email qua UI | Không hợp lệ | Thử gõ email khác | FR-04 |
| C11 | V5 | JWT đúng, user tồn tại | Hợp lệ | Token U lấy lúc chạy | FR-04 |
| C12 | V5 | Không gửi JWT | Không hợp lệ | Bỏ Authorization | FR-04, SEC-02 |
| C13 | V5 | JWT sai cú pháp/chữ ký | Không hợp lệ | Bearer invalid.a04.token | FR-04, SEC-02 |
| C14 | V6 | Subject U và đối tượng cập nhật U | Hợp lệ | Không có target trong payload | FR-04 |
| C15 | V6 | Payload id=user W khác U | Không hợp lệ | id=W.id | FR-04 |
| C16 | V6 | Payload user_id=user W khác U | Không hợp lệ | user_id=W.id | FR-04 |
| C17 | V7 | Không yêu cầu đổi role | Hợp lệ | Không gửi role | FR-04 |
| C18 | V7 | Yêu cầu user tự nâng role | Không hợp lệ | role=admin | FR-04, SEC-06 |
| C19 | V1/V3 | Rỗng/thiếu/null, giới hạn/trim chưa có spec | Chưa rõ | Không gán TC chốt lỗi | FR-04 |
| C20 | V2 | Phone rỗng/thiếu/null, Unicode digits | Chưa rõ | Chưa có quyết định yêu cầu | FR-04 |
| C21 | V4 | Sửa email trực tiếp qua API | Chưa rõ | FR-04 chỉ nói UI | FR-04 |
| C22 | V8 | Cập nhật hợp lệ được lưu đúng | Hợp lệ | Đọc lại sau request hợp lệ | FR-04 |
| C23 | V9 | Phone sai không được lưu | Hợp lệ (đầu ra) | Request phone invalid, đọc lại snapshot | FR-04 |
| C24 | V9 | Không JWT đúng thì không update | Hợp lệ (đầu ra) | Request thiếu/sai token | FR-04, SEC-02 |
| C25 | V7/V9 | Role không bị thay đổi trái phép | Hợp lệ (đầu ra) | Gửi role=admin, đọc lại role lưu | FR-04, SEC-06 |
| C26 | V6/V9 | User W không bị cập nhật bởi U | Hợp lệ (đầu ra) | Gửi target khác subject, đọc W | FR-04 |
| C27 | V4/V9 | Email không đổi qua UI | Hợp lệ (đầu ra) | Thử sửa email và refresh | FR-04 |
| C28 | V8/V9 | Role/target trái phép thực sự được lưu | Đầu ra bị cấm — không đạt tới được nếu đúng spec | Kiểm bảo vệ C25/C26, không gán độ phủ C28 | FR-04 |

Các lớp C19–21 chưa có expected đầy đủ; C28 bị cấm là mục tiêu kiểm ngăn chặn, không phải kết quả “đạt được” để tính coverage.

## 5. Boundary Value Analysis

**Cách xác định biên:** FR-04 yêu cầu 10 ≤ L ≤ 11, hai biên đóng L=10 và L=11. Phone luôn là chuỗi ASCII, bắt đầu bằng 0 và chỉ có digits trong nhóm này để cô lập độ dài.

**Độ mịn:** ±1 chữ số; dữ liệu ASCII nên cách đếm không phụ thuộc grapheme/code unit.

**2-point hay 3-point:** chọn 2-point theo quy ước on/off của skill, chưa đối chiếu bài giảng. Hai cặp là (10,9) và (11,12). Với miền hợp lệ chỉ có 10/11, tập hợp bốn giá trị cũng chính là hợp của hai bộ 3-point (9,10,11) và (10,11,12), nhưng báo cáo chỉ gắn nhãn lựa chọn 2-point. Không phát minh biên min/max tên hoặc địa chỉ; prefix là điều kiện tập ký tự, không gọi là BVA số.

| Boundary ID | Class ID liên quan | Quy tắc / nguồn | Đóng / mở | Độ mịn | On-point | Off-point | Expected | Lý do chọn |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| B1 | C1, C2 | FR-04: L ≥ 10 | Đóng | 1 chữ số | 0912345678 (10) | 091234567 (9) | On: chấp nhận và lưu; Off: không lưu phone sai | Kiểm nhầm ≥ thành > hoặc nhận dưới min |
| B2 | C1, C3 | FR-04: L ≤ 11 | Đóng | 1 chữ số | 09123456789 (11) | 091234567890 (12) | On: chấp nhận và lưu; Off: không lưu phone sai | Kiểm nhầm ≤ thành < hoặc nhận vượt max |

Không có công thức tiền; expected về độ dài tính trực tiếp từ số ký tự các chuỗi cụ thể. L=10/11 không phải hai lớp EP khác nhau theo quy tắc gốc, nhưng cần cả hai điểm BVA. Chạy từng điểm trên Web và API riêng.

## 6. Test Matrix

Chữ đậm trong mô tả bước chỉ biến khác nominal; JSON dưới đây là dữ liệu đầy đủ. Các case tên/địa chỉ/email giữ phone snapshot 0987654321 để không vô tình đổi thêm trường. Single Fault xét điều kiện sai, không chỉ số field.

| TC ID | Requirement / nguồn | Technique | Dữ liệu đầy đủ | Class IDs | Boundary / điểm | Validity | Expected |
|:---|:---|:---|:---|:---|:---|:---|:---|
| TC-A04-001 | FR-04 | Cả hai | Web; `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C1, C22 | B1 on (10) | valid | Chấp nhận cập nhật U; đọc lại sau refresh/GET cho thấy name, phone, shipping_address đúng payload, email và role giữ nguyên. |
| TC-A04-002 | FR-04 | Cả hai | Web; `{"name":"Nguyễn Văn An","phone":"091234567","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C2, C23 | B1 off (9) | invalid | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| TC-A04-003 | FR-04 | Cả hai | Web; `{"name":"Nguyễn Văn An","phone":"091234567890","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C3, C23 | B2 off (12) | invalid | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| TC-A04-004 | FR-04 | Domain | Web; `{"name":"Nguyễn Văn An","phone":"1912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C4, C23 | — | invalid | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| TC-A04-005 | FR-04 | Domain | Web; `{"name":"Nguyễn Văn An","phone":"091234567a","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C5, C23 | — | invalid | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| TC-A04-006 | FR-04 | Domain | Web; `{"name":"Nguyễn Văn An","phone":"09123-5678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C6, C23 | — | invalid | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| TC-A04-007 | FR-04 | Domain | Web; `{"name":"Nguyễn Văn An","phone":"09123 5678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C7, C23 | — | invalid | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| TC-A04-008 | FR-04 | Cả hai | API; `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C1, C22 | B1 on (10) | valid | Chấp nhận cập nhật U; đọc lại sau refresh/GET cho thấy name, phone, shipping_address đúng payload, email và role giữ nguyên. |
| TC-A04-009 | FR-04 | Cả hai | API; `{"name":"Nguyễn Văn An","phone":"091234567","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C2, C23 | B1 off (9) | invalid | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| TC-A04-010 | FR-04 | Cả hai | API; `{"name":"Nguyễn Văn An","phone":"091234567890","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C3, C23 | B2 off (12) | invalid | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| TC-A04-011 | FR-04 | Domain | API; `{"name":"Nguyễn Văn An","phone":"1912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C4, C23 | — | invalid | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| TC-A04-012 | FR-04 | Domain | API; `{"name":"Nguyễn Văn An","phone":"091234567a","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C5, C23 | — | invalid | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| TC-A04-013 | FR-04 | Domain | API; `{"name":"Nguyễn Văn An","phone":"09123-5678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C6, C23 | — | invalid | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| TC-A04-014 | FR-04 | Domain | API; `{"name":"Nguyễn Văn An","phone":"09123 5678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C7, C23 | — | invalid | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| TC-A04-015 | FR-04 | Domain | Web; `{"name":"Nguyễn Thị Bình","phone":"0987654321","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C8, C22 | — | valid | Họ tên U được lưu đúng Nguyễn Thị Bình; phone và địa chỉ giữ nguyên snapshot. Refresh vẫn giữ tên mới. |
| TC-A04-016 | FR-04 | Domain | Web; `{"name":"Nguyễn Văn An","phone":"0987654321","shipping_address":"45 Nguyễn Huệ, Quận 1, TP.HCM"}`; token=U hợp lệ | C9, C22 | — | valid | Địa chỉ U được lưu đúng 45 Nguyễn Huệ, Quận 1, TP.HCM; name và phone giữ nguyên snapshot. Refresh vẫn giữ địa chỉ mới. |
| TC-A04-017 | FR-04 | Domain | Web; `{"name":"Nguyễn Thị Bình","phone":"0987654321","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ; thử gõ a04.changed@example.com trên UI | C10, C27 | — | invalid | Không thể sửa email bằng thao tác UI thông thường (chỉ đọc/disabled); sau cập nhật trường cho phép và refresh, email U vẫn bằng snapshot. |
| TC-A04-018 | FR-04, SEC-02 | Domain | API; `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=thiếu | C12, C24 | — | invalid | Không cập nhật hồ sơ U hoặc W; trả phản hồi từ chối xác thực. Kiểm lại bằng token hợp lệ, dữ liệu giữ nguyên; không ép mã HTTP cụ thể. |
| TC-A04-019 | FR-04, SEC-02 | Domain | API; `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=invalid.a04.token | C13, C24 | — | invalid | Không cập nhật U hoặc W; từ chối xác thực với token sai. Đọc lại bằng token hợp lệ cho thấy snapshot không đổi. |
| TC-A04-020 | FR-04, SEC-06 | Domain | API; `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM","role":"admin"}`; token=U hợp lệ | C18, C25 | — | invalid | Role lưu của U vẫn là user sau request và đăng nhập lại. Cho phép reject toàn request hoặc bỏ qua role và cập nhật trường được phép; không chấp nhận role=admin. Không dùng role trong JWT cũ làm bằng chứng duy nhất. |
| TC-A04-021 | FR-04 | Domain | API; `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM","id":"W.id (thay bằng ID thực)"}`; token=U hợp lệ | C16, C26 | — | invalid | Hồ sơ W giữ nguyên snapshot khi dùng token U. Có thể từ chối request hoặc bỏ qua target rồi cập nhật U; không được cập nhật W. Đọc lại W bằng token W. |
| TC-A04-022 | FR-04 | Domain | API; `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM","user_id":"W.id (thay bằng ID thực)"}`; token=U hợp lệ | C16, C26 | — | invalid | Hồ sơ W giữ nguyên snapshot khi dùng token U. Có thể từ chối request hoặc bỏ qua target rồi cập nhật U; không được cập nhật W. Đọc lại W bằng token W. |
| TC-A04-023 | FR-04 | Cả hai | Web; `{"name":"Nguyễn Văn An","phone":"09123456789","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C1, C22 | B2 on (11) | valid | Chấp nhận cập nhật U; đọc lại sau refresh/GET cho thấy name, phone, shipping_address đúng payload, email và role giữ nguyên. |
| TC-A04-024 | FR-04 | Cả hai | API; `{"name":"Nguyễn Văn An","phone":"09123456789","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`; token=U hợp lệ | C1, C22 | B2 on (11) | valid | Chấp nhận cập nhật U; đọc lại sau refresh/GET cho thấy name, phone, shipping_address đúng payload, email và role giữ nguyên. |

## 7. Truy vết và độ phủ

Đây là độ phủ thiết kế, không phải kết quả chạy. C19–21 là khoảng trống spec; không đưa vào mẫu số “đã phủ”. C28 là đầu ra bị cấm.

| Requirement / Class / Boundary point | TC IDs | Trạng thái | Lý do |
|:---|:---|:---|:---|
| C1 | TC-A04-001, TC-A04-008, TC-A04-023, TC-A04-024 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C2 | TC-A04-002, TC-A04-009 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C3 | TC-A04-003, TC-A04-010 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C4 | TC-A04-004, TC-A04-011 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C5 | TC-A04-005, TC-A04-012 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C6 | TC-A04-006, TC-A04-013 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C7 | TC-A04-007, TC-A04-014 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C8 | TC-A04-015 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C9 | TC-A04-016 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C10 | TC-A04-017 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C11 | — | Không đạt tới được | Chưa rõ yêu cầu, không tự đặt expected |
| C12 | TC-A04-018 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C13 | TC-A04-019 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C14 | — | Không đạt tới được | Chưa rõ yêu cầu, không tự đặt expected |
| C15 | — | Không đạt tới được | Chưa rõ yêu cầu, không tự đặt expected |
| C16 | TC-A04-021, TC-A04-022 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C17 | — | Không đạt tới được | Chưa rõ yêu cầu, không tự đặt expected |
| C18 | TC-A04-020 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C19 | — | Chưa phủ | Chưa rõ yêu cầu, không tự đặt expected |
| C20 | — | Chưa phủ | Chưa rõ yêu cầu, không tự đặt expected |
| C21 | — | Chưa phủ | Chưa rõ yêu cầu, không tự đặt expected |
| C22 | TC-A04-001, TC-A04-008, TC-A04-015, TC-A04-016, TC-A04-023, TC-A04-024 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C23 | TC-A04-002, TC-A04-003, TC-A04-004, TC-A04-005, TC-A04-006, TC-A04-007, TC-A04-009, TC-A04-010, TC-A04-011, TC-A04-012, TC-A04-013, TC-A04-014 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C24 | TC-A04-018, TC-A04-019 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C25 | TC-A04-020 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C26 | TC-A04-021, TC-A04-022 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C27 | TC-A04-017 | Đã thiết kế | Dữ liệu/expected kiểm tra lớp này |
| C28 | — | Không đạt tới được | Theo spec phải bị ngăn; test C25/C26 bảo vệ invariant |
| B1 on (10) | TC-A04-001, TC-A04-008 | Đã thiết kế | Web/API |
| B1 off (9) | TC-A04-002, TC-A04-009 | Đã thiết kế | Web/API |
| B2 on (11) | TC-A04-023, TC-A04-024 | Đã thiết kế | Web/API |
| B2 off (12) | TC-A04-003, TC-A04-010 | Đã thiết kế | Web/API |
| FR-04: cập nhật name/address | TC-A04-015, TC-A04-016 | Đã thiết kế | Sửa từng trường, refresh |
| FR-04: phone | TC-A04-001–014, TC-A04-023–024 | Đã thiết kế | Lớp format và biên |
| FR-04: email UI | TC-A04-017 | Đã thiết kế | Không suy rộng sang API |
| FR-04 / SEC-02: xác thực | TC-A04-018, TC-A04-019 | Đã thiết kế | Thiếu và sai token |
| FR-04 / SEC-06: role | TC-A04-020 | Đã thiết kế | Kiểm role lưu, không chỉ JWT cũ |
| FR-04: ownership | TC-A04-021, TC-A04-022 | Đã thiết kế | id/user_id khác U |
| FR-21 / FR-22 / SEC-04 liên quan profile | — | Chưa phủ | Cần vòng GUI và hiển thị an toàn bổ sung |

**Thống kê (không đếm đôi):**

| Chỉ số | Số lượng |
|:---|:---|
| Tổng test case duy nhất | 24 |
| Chỉ Domain | 16 |
| Chỉ BVA | 0 |
| Thuộc cả hai | 8 |
| Hợp lệ / Không hợp lệ | 6 / 18 |
| Đã thực thi / Pass / Fail | 0 / 0 / 0 |
| Not Run | 24 |

N = 16 + 0 + 8 = 24. **Mức giảm:** Không định lượng vì không có không gian dữ liệu hữu hạn đã chốt.

**Thiếu sót đã biết:** chưa chốt các lớp C19–21; chưa có GUI/security bổ sung, token hết hạn/thu hồi (spec không chốt expiry), field target khác id/user_id, concurrency. Ownership hiện chỉ kiểm hai cách gửi target phổ biến, không chứng minh toàn bộ khả năng truy cập chéo. Test âm khác lớp vẫn có thể nhiều lỗi thực thi, cần xác nhận runtime.

Test chi tiết: [A04_Profile.md](../test-cases/A04_Profile.md).

## 8. Giới hạn và kỹ thuật bổ sung

EP/BVA không chứng minh toàn bộ bảo mật, tương tác hay trình tự. Bổ sung authorization/functional/GUI; test XSS, multi-session và các dữ liệu mơ hồ sau khi review phạm vi.

## 9. Review và thay đổi

| Phiên bản | Ngày | Thay đổi | Lý do | TC bị ảnh hưởng | Trạng thái review |
|:---|:---|:---|:---|:---|:---|
| v0.1 | 2026-10-18 | Trích yêu cầu, nguồn, nominal và điểm chưa rõ | Bước đầu A04 | Chưa có TC | Chưa review |

| v0.2 | 2026-10-18 | Thiết kế lớp, ma trận và 22 card Domain | Tách Web/API và negative đơn | Toàn bộ TC hiện tại | Chưa review |

| v0.3 | 2026-10-18 | Bổ sung B1/B2 và 2 TC 11 chữ số; 8 TC gắn cả Domain/BVA; sửa payload test đơn trường | Đủ bốn giá trị 9/10/11/12 trên Web/API, đồng bộ dữ liệu | TC-A04-001–003,008–010,015–017,023–024 | AI self-review; sinh viên chưa review |
