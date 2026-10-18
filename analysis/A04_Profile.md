# Analysis: A04 — Quản lý hồ sơ cá nhân

| | |
|:---|:---|
| **Tính năng / nền tảng** | Hồ sơ cá nhân; Web / API; localhost:5173 và localhost:3000; chưa chạy SUT, build chưa xác nhận |
| **Người thiết kế / ngày** | Codex hỗ trợ MSSV 23120355; họ tên sinh viên chưa cung cấp; tạo/cập nhật 2026-10-18 (Asia/Bangkok) |
| **Test case chi tiết** | Chưa thiết kế ở bước phân tích yêu cầu |
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

Chưa thiết kế ở bước phân tích yêu cầu.

## 5. Boundary Value Analysis

Chưa thiết kế ở bước phân tích yêu cầu.

## 6. Test Matrix

Chưa thiết kế ở bước phân tích yêu cầu.

## 7. Truy vết và độ phủ

Chưa có test case; không tuyên bố độ phủ.

## 8. Giới hạn và kỹ thuật bổ sung

EP/BVA không chứng minh toàn bộ bảo mật, tương tác hay trình tự. Bổ sung authorization/functional/GUI; test XSS, multi-session và các dữ liệu mơ hồ sau khi review phạm vi.

## 9. Review và thay đổi

| Phiên bản | Ngày | Thay đổi | Lý do | TC bị ảnh hưởng | Trạng thái review |
|:---|:---|:---|:---|:---|:---|
| v0.1 | 2026-10-18 | Trích yêu cầu, nguồn, nominal và điểm chưa rõ | Bước đầu A04 | Chưa có TC | Chưa review |
