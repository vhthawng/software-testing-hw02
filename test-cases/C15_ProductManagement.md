# Test Cases: C15 — Quản lý sản phẩm Admin

- **Trạng thái:** 33 test: 10 Pass, 20 Fail, 3 Blocked, 0 Not Run. Web do người dùng báo cáo; 17 API có evidence Postman CLI.
- **Nguồn:** [FR-15, FR-12 và SEC-02/03](../eshop-sut/README.md); [API Products/Categories](../eshop-sut/api_specification.md).
- **Phân tích:** [C15_ProductManagement.md](../analysis/C15_ProductManagement.md).
- **Ngày:** 2026-10-19, Asia/Bangkok. Sinh viên chưa review.

## Chuẩn bị

Mở ứng dụng Web Admin tại địa chỉ dev server thực tế đang chạy (cổng mặc định Vite có thể thay đổi). Backend: http://localhost:3000. Đăng nhập bằng tài khoản Admin; với test API lấy token bằng POST /api/login. Các test quyền user dùng tài khoản role=user riêng, token còn hợp lệ; không giả mạo role hoặc ghi JWT vào báo cáo.

Dữ liệu chuẩn: tên **C15 Test Product**, giá **100000**, mô tả **San pham thu C15**, URL ảnh để trống, danh mục chọn từ danh sách có thật. Lấy ID/tên danh mục bằng GET /api/categories; không đoán ID=1 luôn tồn tại. Sản phẩm thử không gắn đơn/giỏ dùng chung. Khi cần tạo nhiều test, ghi riêng ID sản phẩm mới để cleanup đúng đối tượng.

Test sửa/xóa: chuẩn bị sản phẩm **C15 Original** giá **100000** và sản phẩm đối chứng **C15 Control** giá **200000**, dùng danh mục có thật. Ghi toàn bộ GET /api/products trước thao tác; so sánh theo ID sau thao tác. Mỗi test bắt đầu bằng trạng thái chuẩn riêng, không dựa vào test trước đã tạo/sửa thành công.

**Body chuẩn cho POST/PUT /api/products:**

~~~json
{
  "name": "C15 Test Product",
  "price": 100000,
  "description": "San pham thu C15",
  "imageUrl": "",
  "category_id": "THAY_BANG_ID_DANH_MUC_THAT"
}
~~~

Thay category_id bằng ID số thực tế trước khi gửi. Mọi test API dùng Content-Type: application/json; request Admin gửi Authorization: Bearer <token Admin hợp lệ>. Các card thiếu/sai token hoặc role=user thay header đúng như mô tả. GET /api/products là cách đối chiếu dữ liệu; không biến các test Web thành API khi chưa thao tác giao diện.

Tên biên dùng ASCII A; có thể sinh bằng Console JavaScript với "A".repeat(255) hoặc "A".repeat(256) rồi copy vào form, không sửa DOM để vượt giới hạn. Nếu UI không nhận đủ 256, ghi chiều dài thực tế; không kết luận backend đã xử lý 256. Giá 1 chỉ là giá dương mẫu, không phải minimum price được tự đặt ra.

Test âm: nếu UI từ chối dữ liệu không hợp lệ và dữ liệu lưu không đổi thì Pass. Không bắt buộc câu lỗi hay mã HTTP cụ thể. Nếu môi trường/fixture không chuẩn bị được thì Blocked, không giả actual. Cleanup chỉ sản phẩm test; không reset database dùng chung. Evidence khi có đặt trong test-cases/evidence/C15/.

**Lượt backend bổ sung 2026-10-19:** TC029–033 được thêm vì bạn yêu cầu gọi API trực tiếp để kiểm lại trường hợp FE chặn hoặc nút Sửa không hoạt động. Giữ nguyên ID/nền tảng/steps TC001–028. [Kết quả CLI](evidence/C15/c15_backend_recheck_summary.md).

**Lượt API còn lại 2026-10-19:** Đã chạy TC010, TC017–027: 1 Pass, 11 Fail. [Evidence](evidence/C15/c15_remaining_api_summary.md). Không còn API Not Run; giữ nguyên kết quả Web.

## 1. Tổng hợp

33 test: 23 chỉ Domain, 7 thuộc cả Domain/BVA, 3 kiểm tra chức năng. Kết quả: 10 Pass, 20 Fail, 3 Blocked, 0 Not Run. Đã hoàn thành 30 test (Pass + Fail); 3 Web bị chặn vì nút Sửa không hoạt động.

| TC ID | Nội dung | Dữ liệu chính | Kết quả mong đợi | Status |
|:---|:---|:---|:---|:---|
| TC-C15-001 | Web thêm và xem sản phẩm hợp lệ | Tên C15 Test Product; giá 100000; danh mục chọn từ danh sách. | Tạo một sản phẩm mới đúng dữ liệu; đọc lại được sau refresh. | Pass |
| TC-C15-002 | Web từ chối tên rỗng | Tên để trống; giá 100000; danh mục có thật. | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. | Pass |
| TC-C15-003 | Web chấp nhận tên dài 1 ký tự | Tên A (1 ký tự ASCII); giá 100000; danh mục có thật. | Tạo sản phẩm thành công; tên lưu đúng A, không mất ký tự. | Pass |
| TC-C15-004 | Web chấp nhận tên dài 255 ký tự | Tên là A lặp đúng 255 lần; giá 100000; danh mục có thật. | Sản phẩm được tạo; tên lưu đủ 255 ký tự, bằng chính chuỗi đã gửi. | Pass |
| TC-C15-005 | Web không lưu tên dài 256 ký tự | Tên là A lặp đúng 256 lần; giá 100000; danh mục có thật. | Không lưu tên dài 256. Nếu UI chặn hoặc cắt nhập ở 255, ghi rõ input thực tế và tên lưu ≤255; không kết luận backend đã được test với 256 nếu request không gửi đủ 256. | Fail |
| TC-C15-006 | Web từ chối giá để trống | Tên C15 Test Product; giá để trống; danh mục có thật. | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. | Fail |
| TC-C15-007 | Web từ chối giá bằng 0 | Tên C15 Test Product; giá 0; danh mục có thật. | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. | Fail |
| TC-C15-008 | Web chấp nhận giá dương 1 | Tên C15 Test Product; giá 1; danh mục có thật. Giá 1 là đại diện dương, không khẳng định là giá nhỏ nhất hệ thống. | Tạo sản phẩm với giá lưu bằng 1; tên và danh mục đúng dữ liệu gửi. | Pass |
| TC-C15-009 | Web từ chối giá âm | Tên C15 Test Product; giá -1; danh mục có thật. | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. | Fail |
| TC-C15-010 | API từ chối tạo sản phẩm thiếu danh mục | POST /api/products; JWT Admin hợp lệ; body chuẩn nhưng bỏ field category_id hoàn toàn. | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. | Fail |
| TC-C15-011 | Web lưu đúng danh mục được chọn | Tên C15 Test Product; giá 100000; chọn danh mục có thật khác lựa chọn mặc định. | Sản phẩm lưu đúng ID danh mục đã chọn, không tự đổi về danh mục mặc định. | Pass |
| TC-C15-012 | Web sửa đúng sản phẩm, giữ nguyên các sản phẩm khác | Đổi tên sản phẩm đích từ C15 Original thành C15 Updated; giữ giá 100000 và danh mục. | Chỉ sản phẩm đích có tên mới; giá/danh mục giữ nguyên. Mọi sản phẩm khác giữ đúng tên và dữ liệu cả trên UI ngay sau lưu lẫn sau refresh/backend. | Fail |
| TC-C15-013 | Web không cập nhật tên vượt 255 ký tự | Đổi name của sản phẩm đích thành "A".repeat(256); giữ giá/danh mục. | Không lưu tên dài 256; nếu gửi đủ chuỗi thì không cập nhật dữ liệu vi phạm. Nếu UI giới hạn đầu vào 255, ghi rõ chỉ kiểm được chặn nhập, không khẳng định đã gửi 256 xuống API. | Blocked |
| TC-C15-014 | Web từ chối cập nhật giá bằng 0 | Giữ tên C15 Original; đổi giá 100000 thành 0; giữ danh mục. | Không cập nhật giá thành 0; dữ liệu sản phẩm đích và sản phẩm khác giữ nguyên. | Blocked |
| TC-C15-015 | Web từ chối cập nhật tên thành rỗng | Xóa hết tên; giữ giá 100000 và danh mục. | Không lưu tên rỗng; sản phẩm đích giữ nguyên dữ liệu trước test. | Blocked |
| TC-C15-016 | Web xóa sản phẩm thử, giữ sản phẩm khác | Xóa duy nhất sản phẩm C15 Original theo ID đã ghi. | Sản phẩm bị xóa không còn trong danh sách; sản phẩm đối chứng và các ID khác vẫn giữ nguyên. Không bắt buộc phải xuất hiện dialog xác nhận. | Pass |
| TC-C15-017 | API từ chối danh mục không tồn tại | POST /api/products; token Admin; body chuẩn, category_id là số dương không nằm trong GET /api/categories. | Không tạo sản phẩm tham chiếu danh mục không tồn tại; không ép status/message cụ thể. | Fail |
| TC-C15-018 | API chặn thêm sản phẩm khi thiếu token | POST /api/products; không gửi Authorization. Body chuẩn: name=C15 Unauthorized Create; giá 100000; danh mục có thật. | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. | Fail |
| TC-C15-019 | API chặn thêm sản phẩm khi token sai | POST /api/products; Authorization: Bearer invalid.c15.token. Body chuẩn: name=C15 Unauthorized Create; giá 100000; danh mục có thật. | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. | Fail |
| TC-C15-020 | API chặn thêm sản phẩm khi token của user thường | POST /api/products; Authorization: Bearer <token hợp lệ của tài khoản role=user>. Body chuẩn: name=C15 Unauthorized Create; giá 100000; danh mục có thật. | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. | Fail |
| TC-C15-021 | API chặn sửa sản phẩm khi thiếu token | PUT /api/products/<ID sản phẩm thử>; không gửi Authorization. Body chuẩn, name=C15 Unauthorized Update; giá 100000; danh mục có thật. | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. | Fail |
| TC-C15-022 | API chặn sửa sản phẩm khi token sai | PUT /api/products/<ID sản phẩm thử>; Authorization: Bearer invalid.c15.token. Body chuẩn, name=C15 Unauthorized Update; giá 100000; danh mục có thật. | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. | Fail |
| TC-C15-023 | API chặn sửa sản phẩm khi token của user thường | PUT /api/products/<ID sản phẩm thử>; Authorization: Bearer <token hợp lệ của tài khoản role=user>. Body chuẩn, name=C15 Unauthorized Update; giá 100000; danh mục có thật. | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. | Fail |
| TC-C15-024 | API chặn xóa sản phẩm khi thiếu token | DELETE /api/products/<ID sản phẩm thử>; không gửi Authorization. Không body. | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. | Fail |
| TC-C15-025 | API chặn xóa sản phẩm khi token sai | DELETE /api/products/<ID sản phẩm thử>; Authorization: Bearer invalid.c15.token. Không body. | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. | Fail |
| TC-C15-026 | API chặn xóa sản phẩm khi token của user thường | DELETE /api/products/<ID sản phẩm thử>; Authorization: Bearer <token hợp lệ của tài khoản role=user>. Không body. | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. | Fail |
| TC-C15-027 | API Admin hợp lệ thêm, xem, sửa và xóa được sản phẩm | JWT Admin hợp lệ; tạo tên C15 API Product, giá 100000; sửa tên C15 API Updated; dùng danh mục có thật. | Admin thực hiện thành công thêm/xem/sửa/xóa; dữ liệu lưu đúng, chỉ sản phẩm thử bị tác động. Đây là đường đối chứng để các test từ chối không Pass do API hoàn toàn không hoạt động. | Pass |
| TC-C15-028 | Web Admin không cho user thường vào quản lý sản phẩm | Email/mật khẩu của tài khoản role=user hợp lệ; không sửa role trong token. | Không vào được màn hình/chức năng quản lý sản phẩm Admin. Không ép câu thông báo cụ thể. | Pass |
| TC-C15-029 | API từ chối tạo sản phẩm tên rỗng | POST /api/products; JWT Admin hợp lệ; name="", price=100000, description="C15 CLI supplementary test", imageUrl="", category_id lấy từ GET /api/categories. | Không tạo sản phẩm tên rỗng; danh sách products lưu không đổi. | Fail |
| TC-C15-030 | API sửa tên hợp lệ, giữ mọi sản phẩm khác | PUT /api/products/<ID sản phẩm thử mới>; JWT Admin; đổi name thành C15 CLI Updated kèm mã lần chạy, giữ giá 100000, mô tả/ảnh/danh mục như trước. | Sửa thành công đúng sản phẩm; mọi sản phẩm khác giữ nguyên dữ liệu. Không kết luận về nút Sửa hoặc hiển thị Web. | Pass |
| TC-C15-031 | API từ chối sửa tên thành 256 ký tự | PUT /api/products/<ID sản phẩm thử>; JWT Admin; name="A".repeat(256), price=100000, mô tả/ảnh/danh mục giữ nguyên. | Không lưu tên 256; dữ liệu trước test giữ nguyên. | Fail |
| TC-C15-032 | API từ chối sửa giá thành 0 | PUT /api/products/<ID sản phẩm thử>; JWT Admin; chỉ đổi price=0; giữ tên, description, imageUrl và category_id. | Không lưu giá 0; sản phẩm đích và mọi ID khác giữ nguyên dữ liệu trước test. | Fail |
| TC-C15-033 | API từ chối sửa tên thành rỗng | PUT /api/products/<ID sản phẩm thử>; JWT Admin; name="", price=100000, các field khác giữ nguyên. | Không lưu tên rỗng; dữ liệu trước test giữ nguyên. | Fail |

## 2. Chi tiết

### TC-C15-001 — Web thêm và xem sản phẩm hợp lệ

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-001; Web thêm và xem sản phẩm hợp lệ |
| Requirement / Purpose | FR-15: Admin thêm/xem sản phẩm |
| Technique | Domain |
| Domain / Boundary | C1, C4, C8, C11; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin, mở tab Sản phẩm và form thêm mới. Chọn danh mục hiện có; tên chuẩn C15 Test Product, giá 100000, mô tả San pham thu C15, URL ảnh để trống. Ghi danh sách sản phẩm trước test. |
| Test data | Tên C15 Test Product; giá 100000; danh mục chọn từ danh sách. |
| Steps | 1. Điền dữ liệu chuẩn.<br>2. Nhấn Lưu sản phẩm.<br>3. Kiểm sản phẩm mới trên danh sách và đọc GET /api/products.<br>4. Refresh trang, kiểm lại tên, giá và danh mục theo ID mới. |
| Expected Result | Tạo một sản phẩm mới đúng dữ liệu; đọc lại được sau refresh. |
| Actual Result | Người dùng test Web: tạo sản phẩm đúng dữ liệu, đọc lại được sau refresh. |
| Status | Pass |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-002 — Web từ chối tên rỗng

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-002; Web từ chối tên rỗng |
| Requirement / Purpose | FR-15: tên bắt buộc |
| Technique | Domain + BVA |
| Domain / Boundary | C2; B1 off, 0 ký tự; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin, mở tab Sản phẩm và form thêm mới. Chọn danh mục hiện có; tên chuẩn C15 Test Product, giá 100000, mô tả San pham thu C15, URL ảnh để trống. Ghi danh sách sản phẩm trước test. |
| Test data | Tên để trống; giá 100000; danh mục có thật. |
| Steps | 1. Xóa hết tên, không nhập khoảng trắng.<br>2. Nhấn Lưu sản phẩm.<br>3. Ghi phản hồi UI và đối chiếu danh sách trước/sau. |
| Expected Result | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. |
| Actual Result | Người dùng test Web: form FE chặn tên rỗng. Đây là kết quả chặn nhập trên Web; backend được kiểm riêng ở TC-C15-029 và không đạt. |
| Status | Pass |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-003 — Web chấp nhận tên dài 1 ký tự

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-003; Web chấp nhận tên dài 1 ký tự |
| Requirement / Purpose | FR-15: tên bắt buộc, tối đa 255 ký tự |
| Technique | Domain + BVA |
| Domain / Boundary | C1; B1 on, 1 ký tự; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin, mở tab Sản phẩm và form thêm mới. Chọn danh mục hiện có; tên chuẩn C15 Test Product, giá 100000, mô tả San pham thu C15, URL ảnh để trống. Ghi danh sách sản phẩm trước test. |
| Test data | Tên A (1 ký tự ASCII); giá 100000; danh mục có thật. |
| Steps | 1. Nhập tên A.<br>2. Lưu sản phẩm.<br>3. Đọc lại tên theo ID mới trên UI và GET /api/products. |
| Expected Result | Tạo sản phẩm thành công; tên lưu đúng A, không mất ký tự. |
| Actual Result | Người dùng test Web: tạo thành công; tên lưu đúng A, không mất ký tự. |
| Status | Pass |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-004 — Web chấp nhận tên dài 255 ký tự

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-004; Web chấp nhận tên dài 255 ký tự |
| Requirement / Purpose | FR-15: tối đa 255 ký tự |
| Technique | Domain + BVA |
| Domain / Boundary | C1; B2 on, 255 ký tự; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin, mở tab Sản phẩm và form thêm mới. Chọn danh mục hiện có; tên chuẩn C15 Test Product, giá 100000, mô tả San pham thu C15, URL ảnh để trống. Ghi danh sách sản phẩm trước test. |
| Test data | Tên là A lặp đúng 255 lần; giá 100000; danh mục có thật. |
| Steps | 1. Tạo chuỗi bằng JavaScript "A".repeat(255), chỉ dùng để sinh dữ liệu.<br>2. Dán tên vào form và lưu.<br>3. Đọc lại name theo ID mới qua GET /api/products và đếm độ dài. |
| Expected Result | Sản phẩm được tạo; tên lưu đủ 255 ký tự, bằng chính chuỗi đã gửi. |
| Actual Result | Người dùng test Web: tên được lưu đủ 255 ký tự, bằng chuỗi gửi. |
| Status | Pass |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-005 — Web không lưu tên dài 256 ký tự

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-005; Web không lưu tên dài 256 ký tự |
| Requirement / Purpose | FR-15: tối đa 255 ký tự |
| Technique | Domain + BVA |
| Domain / Boundary | C3; B2 off, 256 ký tự; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin, mở tab Sản phẩm và form thêm mới. Chọn danh mục hiện có; tên chuẩn C15 Test Product, giá 100000, mô tả San pham thu C15, URL ảnh để trống. Ghi danh sách sản phẩm trước test. |
| Test data | Tên là A lặp đúng 256 lần; giá 100000; danh mục có thật. |
| Steps | 1. Tạo chuỗi bằng "A".repeat(256).<br>2. Dán vào form, ghi chiều dài thực tế ô tên nhận được.<br>3. Thử lưu và đọc lại danh sách/name. |
| Expected Result | Không lưu tên dài 256. Nếu UI chặn hoặc cắt nhập ở 255, ghi rõ input thực tế và tên lưu ≤255; không kết luận backend đã được test với 256 nếu request không gửi đủ 256. |
| Actual Result | Người dùng test Web: tạo được sản phẩm với tên đủ 256 ký tự, bằng chuỗi gửi. |
| Status | Fail |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | [BUG-C15-001](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-006 — Web từ chối giá để trống

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-006; Web từ chối giá để trống |
| Requirement / Purpose | FR-15: giá bắt buộc |
| Technique | Domain |
| Domain / Boundary | C5; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin, mở tab Sản phẩm và form thêm mới. Chọn danh mục hiện có; tên chuẩn C15 Test Product, giá 100000, mô tả San pham thu C15, URL ảnh để trống. Ghi danh sách sản phẩm trước test. |
| Test data | Tên C15 Test Product; giá để trống; danh mục có thật. |
| Steps | 1. Điền tên/danh mục hợp lệ, xóa toàn bộ giá.<br>2. Lưu và kiểm danh sách trước/sau. |
| Expected Result | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. |
| Actual Result | Người dùng test Web: tạo được sản phẩm khi ô giá để trống. Không tự thay actual thành giá 0 vì báo cáo không nêu chuyển đổi đó. |
| Status | Fail |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | [BUG-C15-002](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-007 — Web từ chối giá bằng 0

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-007; Web từ chối giá bằng 0 |
| Requirement / Purpose | FR-15: giá phải >0 |
| Technique | Domain |
| Domain / Boundary | C6; Mốc giá 0; chưa gán đủ BVA giá; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin, mở tab Sản phẩm và form thêm mới. Chọn danh mục hiện có; tên chuẩn C15 Test Product, giá 100000, mô tả San pham thu C15, URL ảnh để trống. Ghi danh sách sản phẩm trước test. |
| Test data | Tên C15 Test Product; giá 0; danh mục có thật. |
| Steps | 1. Nhập giá 0, giữ tên/danh mục hợp lệ.<br>2. Lưu và kiểm dữ liệu lưu. |
| Expected Result | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. |
| Actual Result | Người dùng test Web: tạo được sản phẩm giá 0 ₫. |
| Status | Fail |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | [BUG-C15-003](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-008 — Web chấp nhận giá dương 1

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-008; Web chấp nhận giá dương 1 |
| Requirement / Purpose | FR-15: giá phải >0 |
| Technique | Domain |
| Domain / Boundary | C4; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin, mở tab Sản phẩm và form thêm mới. Chọn danh mục hiện có; tên chuẩn C15 Test Product, giá 100000, mô tả San pham thu C15, URL ảnh để trống. Ghi danh sách sản phẩm trước test. |
| Test data | Tên C15 Test Product; giá 1; danh mục có thật. Giá 1 là đại diện dương, không khẳng định là giá nhỏ nhất hệ thống. |
| Steps | 1. Nhập giá 1 và lưu.<br>2. Đọc lại sản phẩm theo ID mới. |
| Expected Result | Tạo sản phẩm với giá lưu bằng 1; tên và danh mục đúng dữ liệu gửi. |
| Actual Result | Người dùng test Web: tạo thành công sản phẩm giá 1 ₫. |
| Status | Pass |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-009 — Web từ chối giá âm

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-009; Web từ chối giá âm |
| Requirement / Purpose | FR-15: giá phải >0 |
| Technique | Domain |
| Domain / Boundary | C7; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin, mở tab Sản phẩm và form thêm mới. Chọn danh mục hiện có; tên chuẩn C15 Test Product, giá 100000, mô tả San pham thu C15, URL ảnh để trống. Ghi danh sách sản phẩm trước test. |
| Test data | Tên C15 Test Product; giá -1; danh mục có thật. |
| Steps | 1. Nhập giá -1 và lưu.<br>2. Kiểm danh sách trước/sau. |
| Expected Result | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. |
| Actual Result | Người dùng test Web: tạo thành công sản phẩm giá âm theo báo cáo. |
| Status | Fail |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | [BUG-C15-003](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-010 — API từ chối tạo sản phẩm thiếu danh mục

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-010; API từ chối tạo sản phẩm thiếu danh mục |
| Requirement / Purpose | FR-15: danh mục bắt buộc |
| Technique | Domain |
| Domain / Boundary | C9, C11; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Ghi danh sách trước test; tên C15 Test Product, giá 100000, mô tả San pham thu C15 và imageUrl="". |
| Test data | POST /api/products; JWT Admin hợp lệ; body chuẩn nhưng bỏ field category_id hoàn toàn. |
| Steps | 1. Gửi POST /api/products bằng token Admin với body không có category_id.<br>2. Ghi response.<br>3. GET /api/products so sánh với trước test. |
| Expected Result | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. |
| Actual Result | Postman CLI 2026-10-19: POST, categoryMissing, HTTP 200; GET kiểm chứng vẫn tạo sản phẩm có category_id=null. Vi phạm expected từ chối và không ghi dữ liệu. |
| Status | Fail |
| Evidence | [CLI summary](evidence/C15/c15_remaining_api_summary.md), [log](evidence/C15/c15_remaining_api_cli.log), [JSON](evidence/C15/c15_remaining_api_results.json) |
| Bug ID / Issue URL | [BUG-C15-006](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-011 — Web lưu đúng danh mục được chọn

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-011; Web lưu đúng danh mục được chọn |
| Requirement / Purpose | FR-15: danh mục thuộc danh sách hiện có |
| Technique | Domain |
| Domain / Boundary | C8; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin, mở tab Sản phẩm và form thêm mới. Chọn danh mục hiện có; tên chuẩn C15 Test Product, giá 100000, mô tả San pham thu C15, URL ảnh để trống. Ghi danh sách sản phẩm trước test. Cần ít nhất hai danh mục thật; nếu chưa có thì chuẩn bị danh mục thử bằng quyền Admin. |
| Test data | Tên C15 Test Product; giá 100000; chọn danh mục có thật khác lựa chọn mặc định. |
| Steps | 1. Ghi ID/tên hai danh mục từ GET /api/categories.<br>2. Chọn danh mục thứ hai trong dropdown.<br>3. Lưu và đọc category_id của ID sản phẩm mới. |
| Expected Result | Sản phẩm lưu đúng ID danh mục đã chọn, không tự đổi về danh mục mặc định. |
| Actual Result | Người dùng test Web: lưu đúng ID danh mục đã chọn, không đổi về danh mục mặc định. |
| Status | Pass |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-012 — Web sửa đúng sản phẩm, giữ nguyên các sản phẩm khác

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-012; Web sửa đúng sản phẩm, giữ nguyên các sản phẩm khác |
| Requirement / Purpose | FR-15: chỉ sản phẩm được sửa thay đổi |
| Technique | Functional |
| Domain / Boundary | C1, C4, C8, C11; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin. Tạo sản phẩm thử riêng tên C15 Original, giá 100000 và danh mục có thật; ghi ID. Có ít nhất một sản phẩm khác tên C15 Control, giá 200000. Lưu toàn bộ danh sách sản phẩm trước test theo ID. |
| Test data | Đổi tên sản phẩm đích từ C15 Original thành C15 Updated; giữ giá 100000 và danh mục. |
| Steps | 1. Bấm Sửa đúng dòng C15 Original.<br>2. Đổi tên thành C15 Updated rồi lưu.<br>3. Kiểm ngay tên mọi sản phẩm khác trên UI trước khi refresh.<br>4. GET /api/products, đối chiếu tất cả ID khác với bản trước.<br>5. Refresh Admin và đối chiếu lại. |
| Expected Result | Chỉ sản phẩm đích có tên mới; giá/danh mục giữ nguyên. Mọi sản phẩm khác giữ đúng tên và dữ liệu cả trên UI ngay sau lưu lẫn sau refresh/backend. |
| Actual Result | Người dùng test Web: bấm nút Sửa không kích hoạt thao tác như mong đợi, không thực hiện được sửa qua UI. Backend được kiểm riêng ở TC-C15-030: update hợp lệ Pass; API không thay thế kiểm chứng hiển thị UI. |
| Status | Fail |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | [BUG-C15-004](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-013 — Web không cập nhật tên vượt 255 ký tự

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-013; Web không cập nhật tên vượt 255 ký tự |
| Requirement / Purpose | FR-15: tên tối đa 255 ký tự khi sửa |
| Technique | Domain |
| Domain / Boundary | C3; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin. Tạo sản phẩm thử riêng tên C15 Original, giá 100000 và danh mục có thật; ghi ID. Có ít nhất một sản phẩm khác tên C15 Control, giá 200000. Lưu toàn bộ danh sách sản phẩm trước test theo ID. |
| Test data | Đổi name của sản phẩm đích thành "A".repeat(256); giữ giá/danh mục. |
| Steps | 1. Bấm Sửa sản phẩm đích.<br>2. Dán chuỗi 256 ký tự, ghi số ký tự form nhận được.<br>3. Thử lưu, đọc lại sản phẩm và đối chiếu trước/sau. |
| Expected Result | Không lưu tên dài 256; nếu gửi đủ chuỗi thì không cập nhật dữ liệu vi phạm. Nếu UI giới hạn đầu vào 255, ghi rõ chỉ kiểm được chặn nhập, không khẳng định đã gửi 256 xuống API. |
| Actual Result | Bị chặn ở bước mở sửa trên Web vì nút Sửa không hoạt động theo báo cáo; chưa gửi được tên 256 qua đường UI. TC-C15-031 kiểm API riêng cho cùng ràng buộc. |
| Status | Blocked |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | [BUG-C15-004](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-014 — Web từ chối cập nhật giá bằng 0

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-014; Web từ chối cập nhật giá bằng 0 |
| Requirement / Purpose | FR-15: giá >0 khi sửa |
| Technique | Domain |
| Domain / Boundary | C6; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin. Tạo sản phẩm thử riêng tên C15 Original, giá 100000 và danh mục có thật; ghi ID. Có ít nhất một sản phẩm khác tên C15 Control, giá 200000. Lưu toàn bộ danh sách sản phẩm trước test theo ID. |
| Test data | Giữ tên C15 Original; đổi giá 100000 thành 0; giữ danh mục. |
| Steps | 1. Sửa đúng sản phẩm, chỉ đổi giá thành 0.<br>2. Lưu, refresh/GET danh sách để đối chiếu. |
| Expected Result | Không cập nhật giá thành 0; dữ liệu sản phẩm đích và sản phẩm khác giữ nguyên. |
| Actual Result | Bị chặn ở bước mở sửa trên Web vì nút Sửa không hoạt động; chưa thử được lưu giá 0 qua UI. TC-C15-032 kiểm API riêng. |
| Status | Blocked |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | [BUG-C15-004](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-015 — Web từ chối cập nhật tên thành rỗng

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-015; Web từ chối cập nhật tên thành rỗng |
| Requirement / Purpose | FR-15: tên bắt buộc khi sửa |
| Technique | Domain |
| Domain / Boundary | C2; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin. Tạo sản phẩm thử riêng tên C15 Original, giá 100000 và danh mục có thật; ghi ID. Có ít nhất một sản phẩm khác tên C15 Control, giá 200000. Lưu toàn bộ danh sách sản phẩm trước test theo ID. |
| Test data | Xóa hết tên; giữ giá 100000 và danh mục. |
| Steps | 1. Sửa đúng sản phẩm và xóa hết name.<br>2. Thử lưu.<br>3. Refresh/GET danh sách kiểm dữ liệu. |
| Expected Result | Không lưu tên rỗng; sản phẩm đích giữ nguyên dữ liệu trước test. |
| Actual Result | Bị chặn ở bước mở sửa trên Web vì nút Sửa không hoạt động; chưa thử được lưu tên rỗng qua UI. TC-C15-033 kiểm API riêng. |
| Status | Blocked |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | [BUG-C15-004](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-016 — Web xóa sản phẩm thử, giữ sản phẩm khác

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-016; Web xóa sản phẩm thử, giữ sản phẩm khác |
| Requirement / Purpose | FR-15: Admin xóa sản phẩm |
| Technique | Functional |
| Domain / Boundary | C11; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng nhập Admin. Tạo sản phẩm thử riêng tên C15 Original, giá 100000 và danh mục có thật; ghi ID. Có ít nhất một sản phẩm khác tên C15 Control, giá 200000. Lưu toàn bộ danh sách sản phẩm trước test theo ID. |
| Test data | Xóa duy nhất sản phẩm C15 Original theo ID đã ghi. |
| Steps | 1. Bấm Xóa đúng dòng sản phẩm thử; nếu UI có xác nhận thì xác nhận.<br>2. Kiểm danh sách và GET /api/products.<br>3. Refresh và kiểm sản phẩm đối chứng vẫn có. |
| Expected Result | Sản phẩm bị xóa không còn trong danh sách; sản phẩm đối chứng và các ID khác vẫn giữ nguyên. Không bắt buộc phải xuất hiện dialog xác nhận. |
| Actual Result | Người dùng test Web: sản phẩm bị xóa không còn trong danh sách; sản phẩm đối chứng và các ID khác giữ nguyên. |
| Status | Pass |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-017 — API từ chối danh mục không tồn tại

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-017; API từ chối danh mục không tồn tại |
| Requirement / Purpose | FR-15: danh mục phải thuộc danh sách có sẵn |
| Technique | Domain |
| Domain / Boundary | C10, C11; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Dùng dữ liệu thử cô lập. Đọc danh sách category; chọn max ID hiện tại + 1 và xác minh ID đó chưa tồn tại ngay trước test. Ghi danh sách products trước test. |
| Test data | POST /api/products; token Admin; body chuẩn, category_id là số dương không nằm trong GET /api/categories. |
| Steps | 1. Gửi POST với tên/giá hợp lệ và category_id không tồn tại.<br>2. Đọc response và GET /api/products sau test. |
| Expected Result | Không tạo sản phẩm tham chiếu danh mục không tồn tại; không ép status/message cụ thể. |
| Actual Result | Postman CLI 2026-10-19: POST, categoryInvalid, HTTP 200; GET kiểm chứng vẫn tạo sản phẩm có category_id=4 dù GET categories chỉ có 1, 2, 3. Vi phạm expected từ chối và không ghi dữ liệu. |
| Status | Fail |
| Evidence | [CLI summary](evidence/C15/c15_remaining_api_summary.md), [log](evidence/C15/c15_remaining_api_cli.log), [JSON](evidence/C15/c15_remaining_api_results.json) |
| Bug ID / Issue URL | [BUG-C15-006](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-018 — API chặn thêm sản phẩm khi thiếu token

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-018; API chặn thêm sản phẩm khi thiếu token |
| Requirement / Purpose | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ |
| Technique | Domain |
| Domain / Boundary | C12; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Có một sản phẩm thử đích nếu là PUT/DELETE; ghi ID và toàn bộ danh sách trước test. Chuẩn bị dữ liệu body hợp lệ để chỉ vi phạm xác thực/quyền. Có token Admin riêng để đối chiếu/cleanup nếu cần. |
| Test data | POST /api/products; không gửi Authorization. Body chuẩn: name=C15 Unauthorized Create; giá 100000; danh mục có thật. |
| Steps | 1. Ghi danh sách products trước test.<br>2. Gửi POST /api/products với xác thực mô tả ở dữ liệu.<br>3. Ghi response.<br>4. GET /api/products, so sánh toàn bộ dữ liệu theo ID với trước test. |
| Expected Result | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| Actual Result | Postman CLI 2026-10-19: POST, không gửi Authorization, HTTP 200; GET kiểm chứng vẫn tạo sản phẩm ID 25. Vi phạm expected từ chối và không ghi dữ liệu. |
| Status | Fail |
| Evidence | [CLI summary](evidence/C15/c15_remaining_api_summary.md), [log](evidence/C15/c15_remaining_api_cli.log), [JSON](evidence/C15/c15_remaining_api_results.json) |
| Bug ID / Issue URL | [BUG-C15-007](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-019 — API chặn thêm sản phẩm khi token sai

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-019; API chặn thêm sản phẩm khi token sai |
| Requirement / Purpose | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ |
| Technique | Domain |
| Domain / Boundary | C13; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Có một sản phẩm thử đích nếu là PUT/DELETE; ghi ID và toàn bộ danh sách trước test. Chuẩn bị dữ liệu body hợp lệ để chỉ vi phạm xác thực/quyền. Có token Admin riêng để đối chiếu/cleanup nếu cần. |
| Test data | POST /api/products; Authorization: Bearer invalid.c15.token. Body chuẩn: name=C15 Unauthorized Create; giá 100000; danh mục có thật. |
| Steps | 1. Ghi danh sách products trước test.<br>2. Gửi POST /api/products với xác thực mô tả ở dữ liệu.<br>3. Ghi response.<br>4. GET /api/products, so sánh toàn bộ dữ liệu theo ID với trước test. |
| Expected Result | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| Actual Result | Postman CLI 2026-10-19: POST, token sai, HTTP 200; GET kiểm chứng vẫn tạo sản phẩm ID 26. Vi phạm expected từ chối và không ghi dữ liệu. |
| Status | Fail |
| Evidence | [CLI summary](evidence/C15/c15_remaining_api_summary.md), [log](evidence/C15/c15_remaining_api_cli.log), [JSON](evidence/C15/c15_remaining_api_results.json) |
| Bug ID / Issue URL | [BUG-C15-007](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-020 — API chặn thêm sản phẩm khi token của user thường

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-020; API chặn thêm sản phẩm khi token của user thường |
| Requirement / Purpose | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ |
| Technique | Domain |
| Domain / Boundary | C14; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Có một sản phẩm thử đích nếu là PUT/DELETE; ghi ID và toàn bộ danh sách trước test. Chuẩn bị dữ liệu body hợp lệ để chỉ vi phạm xác thực/quyền. Có token Admin riêng để đối chiếu/cleanup nếu cần. |
| Test data | POST /api/products; Authorization: Bearer <token hợp lệ của tài khoản role=user>. Body chuẩn: name=C15 Unauthorized Create; giá 100000; danh mục có thật. |
| Steps | 1. Ghi danh sách products trước test.<br>2. Gửi POST /api/products với xác thực mô tả ở dữ liệu.<br>3. Ghi response.<br>4. GET /api/products, so sánh toàn bộ dữ liệu theo ID với trước test. |
| Expected Result | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| Actual Result | Postman CLI 2026-10-19: POST, JWT hợp lệ của role=user, HTTP 200; GET kiểm chứng vẫn tạo sản phẩm ID 27. Vi phạm expected từ chối và không ghi dữ liệu. |
| Status | Fail |
| Evidence | [CLI summary](evidence/C15/c15_remaining_api_summary.md), [log](evidence/C15/c15_remaining_api_cli.log), [JSON](evidence/C15/c15_remaining_api_results.json) |
| Bug ID / Issue URL | [BUG-C15-007](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-021 — API chặn sửa sản phẩm khi thiếu token

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-021; API chặn sửa sản phẩm khi thiếu token |
| Requirement / Purpose | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ |
| Technique | Domain |
| Domain / Boundary | C12; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Có một sản phẩm thử đích nếu là PUT/DELETE; ghi ID và toàn bộ danh sách trước test. Chuẩn bị dữ liệu body hợp lệ để chỉ vi phạm xác thực/quyền. Có token Admin riêng để đối chiếu/cleanup nếu cần. |
| Test data | PUT /api/products/<ID sản phẩm thử>; không gửi Authorization. Body chuẩn, name=C15 Unauthorized Update; giá 100000; danh mục có thật. |
| Steps | 1. Ghi danh sách products trước test.<br>2. Gửi PUT /api/products/<ID sản phẩm thử> với xác thực mô tả ở dữ liệu.<br>3. Ghi response.<br>4. GET /api/products, so sánh toàn bộ dữ liệu theo ID với trước test. |
| Expected Result | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| Actual Result | Postman CLI 2026-10-19: PUT, không gửi Authorization, HTTP 200; GET kiểm chứng ID 28 lưu tên C15 Unauthorized Update. Vi phạm expected từ chối và không ghi dữ liệu. |
| Status | Fail |
| Evidence | [CLI summary](evidence/C15/c15_remaining_api_summary.md), [log](evidence/C15/c15_remaining_api_cli.log), [JSON](evidence/C15/c15_remaining_api_results.json) |
| Bug ID / Issue URL | [BUG-C15-007](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-022 — API chặn sửa sản phẩm khi token sai

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-022; API chặn sửa sản phẩm khi token sai |
| Requirement / Purpose | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ |
| Technique | Domain |
| Domain / Boundary | C13; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Có một sản phẩm thử đích nếu là PUT/DELETE; ghi ID và toàn bộ danh sách trước test. Chuẩn bị dữ liệu body hợp lệ để chỉ vi phạm xác thực/quyền. Có token Admin riêng để đối chiếu/cleanup nếu cần. |
| Test data | PUT /api/products/<ID sản phẩm thử>; Authorization: Bearer invalid.c15.token. Body chuẩn, name=C15 Unauthorized Update; giá 100000; danh mục có thật. |
| Steps | 1. Ghi danh sách products trước test.<br>2. Gửi PUT /api/products/<ID sản phẩm thử> với xác thực mô tả ở dữ liệu.<br>3. Ghi response.<br>4. GET /api/products, so sánh toàn bộ dữ liệu theo ID với trước test. |
| Expected Result | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| Actual Result | Postman CLI 2026-10-19: PUT, token sai, HTTP 200; GET kiểm chứng ID 29 lưu tên C15 Unauthorized Update. Vi phạm expected từ chối và không ghi dữ liệu. |
| Status | Fail |
| Evidence | [CLI summary](evidence/C15/c15_remaining_api_summary.md), [log](evidence/C15/c15_remaining_api_cli.log), [JSON](evidence/C15/c15_remaining_api_results.json) |
| Bug ID / Issue URL | [BUG-C15-007](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-023 — API chặn sửa sản phẩm khi token của user thường

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-023; API chặn sửa sản phẩm khi token của user thường |
| Requirement / Purpose | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ |
| Technique | Domain |
| Domain / Boundary | C14; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Có một sản phẩm thử đích nếu là PUT/DELETE; ghi ID và toàn bộ danh sách trước test. Chuẩn bị dữ liệu body hợp lệ để chỉ vi phạm xác thực/quyền. Có token Admin riêng để đối chiếu/cleanup nếu cần. |
| Test data | PUT /api/products/<ID sản phẩm thử>; Authorization: Bearer <token hợp lệ của tài khoản role=user>. Body chuẩn, name=C15 Unauthorized Update; giá 100000; danh mục có thật. |
| Steps | 1. Ghi danh sách products trước test.<br>2. Gửi PUT /api/products/<ID sản phẩm thử> với xác thực mô tả ở dữ liệu.<br>3. Ghi response.<br>4. GET /api/products, so sánh toàn bộ dữ liệu theo ID với trước test. |
| Expected Result | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| Actual Result | Postman CLI 2026-10-19: PUT, JWT hợp lệ của role=user, HTTP 200; GET kiểm chứng ID 30 lưu tên C15 Unauthorized Update. Vi phạm expected từ chối và không ghi dữ liệu. |
| Status | Fail |
| Evidence | [CLI summary](evidence/C15/c15_remaining_api_summary.md), [log](evidence/C15/c15_remaining_api_cli.log), [JSON](evidence/C15/c15_remaining_api_results.json) |
| Bug ID / Issue URL | [BUG-C15-007](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-024 — API chặn xóa sản phẩm khi thiếu token

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-024; API chặn xóa sản phẩm khi thiếu token |
| Requirement / Purpose | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ |
| Technique | Domain |
| Domain / Boundary | C12; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Có một sản phẩm thử đích nếu là PUT/DELETE; ghi ID và toàn bộ danh sách trước test. Chuẩn bị dữ liệu body hợp lệ để chỉ vi phạm xác thực/quyền. Có token Admin riêng để đối chiếu/cleanup nếu cần. |
| Test data | DELETE /api/products/<ID sản phẩm thử>; không gửi Authorization. Không body. |
| Steps | 1. Ghi danh sách products trước test.<br>2. Gửi DELETE /api/products/<ID sản phẩm thử> với xác thực mô tả ở dữ liệu.<br>3. Ghi response.<br>4. GET /api/products, so sánh toàn bộ dữ liệu theo ID với trước test. |
| Expected Result | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| Actual Result | Postman CLI 2026-10-19: DELETE, không gửi Authorization, HTTP 200; GET kiểm chứng ID 31 đã bị xóa, GET không còn sản phẩm đích. Vi phạm expected từ chối và không ghi dữ liệu. |
| Status | Fail |
| Evidence | [CLI summary](evidence/C15/c15_remaining_api_summary.md), [log](evidence/C15/c15_remaining_api_cli.log), [JSON](evidence/C15/c15_remaining_api_results.json) |
| Bug ID / Issue URL | [BUG-C15-007](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-025 — API chặn xóa sản phẩm khi token sai

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-025; API chặn xóa sản phẩm khi token sai |
| Requirement / Purpose | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ |
| Technique | Domain |
| Domain / Boundary | C13; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Có một sản phẩm thử đích nếu là PUT/DELETE; ghi ID và toàn bộ danh sách trước test. Chuẩn bị dữ liệu body hợp lệ để chỉ vi phạm xác thực/quyền. Có token Admin riêng để đối chiếu/cleanup nếu cần. |
| Test data | DELETE /api/products/<ID sản phẩm thử>; Authorization: Bearer invalid.c15.token. Không body. |
| Steps | 1. Ghi danh sách products trước test.<br>2. Gửi DELETE /api/products/<ID sản phẩm thử> với xác thực mô tả ở dữ liệu.<br>3. Ghi response.<br>4. GET /api/products, so sánh toàn bộ dữ liệu theo ID với trước test. |
| Expected Result | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| Actual Result | Postman CLI 2026-10-19: DELETE, token sai, HTTP 200; GET kiểm chứng ID 32 đã bị xóa, GET không còn sản phẩm đích. Vi phạm expected từ chối và không ghi dữ liệu. |
| Status | Fail |
| Evidence | [CLI summary](evidence/C15/c15_remaining_api_summary.md), [log](evidence/C15/c15_remaining_api_cli.log), [JSON](evidence/C15/c15_remaining_api_results.json) |
| Bug ID / Issue URL | [BUG-C15-007](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-026 — API chặn xóa sản phẩm khi token của user thường

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-026; API chặn xóa sản phẩm khi token của user thường |
| Requirement / Purpose | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ |
| Technique | Domain |
| Domain / Boundary | C14; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Có một sản phẩm thử đích nếu là PUT/DELETE; ghi ID và toàn bộ danh sách trước test. Chuẩn bị dữ liệu body hợp lệ để chỉ vi phạm xác thực/quyền. Có token Admin riêng để đối chiếu/cleanup nếu cần. |
| Test data | DELETE /api/products/<ID sản phẩm thử>; Authorization: Bearer <token hợp lệ của tài khoản role=user>. Không body. |
| Steps | 1. Ghi danh sách products trước test.<br>2. Gửi DELETE /api/products/<ID sản phẩm thử> với xác thực mô tả ở dữ liệu.<br>3. Ghi response.<br>4. GET /api/products, so sánh toàn bộ dữ liệu theo ID với trước test. |
| Expected Result | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| Actual Result | Postman CLI 2026-10-19: DELETE, JWT hợp lệ của role=user, HTTP 200; GET kiểm chứng ID 33 đã bị xóa, GET không còn sản phẩm đích. Vi phạm expected từ chối và không ghi dữ liệu. |
| Status | Fail |
| Evidence | [CLI summary](evidence/C15/c15_remaining_api_summary.md), [log](evidence/C15/c15_remaining_api_cli.log), [JSON](evidence/C15/c15_remaining_api_results.json) |
| Bug ID / Issue URL | [BUG-C15-007](../bug-reports/bugs.md) |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-027 — API Admin hợp lệ thêm, xem, sửa và xóa được sản phẩm

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-027; API Admin hợp lệ thêm, xem, sửa và xóa được sản phẩm |
| Requirement / Purpose | FR-15 CRUD và FR-12: quyền Admin hợp lệ |
| Technique | Domain |
| Domain / Boundary | C1, C4, C8, C11; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Ghi danh sách products/category và ít nhất một sản phẩm đối chứng. Chỉ thao tác sản phẩm mới của test; không xóa sản phẩm dùng chung. |
| Test data | JWT Admin hợp lệ; tạo tên C15 API Product, giá 100000; sửa tên C15 API Updated; dùng danh mục có thật. |
| Steps | 1. POST /api/products với body chuẩn, ghi ID mới từ response hoặc chênh lệch danh sách.<br>2. GET /api/products kiểm đúng sản phẩm mới.<br>3. PUT /api/products/<ID mới> với full body chuẩn, đổi tên C15 API Updated.<br>4. GET list kiểm tên mới và các ID khác không đổi.<br>5. DELETE /api/products/<ID mới> bằng cùng token Admin.<br>6. GET list xác minh sản phẩm thử không còn, các ID khác vẫn nguyên. |
| Expected Result | Admin thực hiện thành công thêm/xem/sửa/xóa; dữ liệu lưu đúng, chỉ sản phẩm thử bị tác động. Đây là đường đối chứng để các test từ chối không Pass do API hoàn toàn không hoạt động. |
| Actual Result | Postman CLI 2026-10-19: JWT Admin hợp lệ tạo ID 34, đọc lại đúng dữ liệu; PUT đổi tên đúng sản phẩm, các sản phẩm khác giữ nguyên; DELETE thành công và GET không còn ID 34. Catalog trở về đúng baseline. |
| Status | Pass |
| Evidence | [CLI summary](evidence/C15/c15_remaining_api_summary.md), [log](evidence/C15/c15_remaining_api_cli.log), [JSON](evidence/C15/c15_remaining_api_results.json) |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-028 — Web Admin không cho user thường vào quản lý sản phẩm

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-028; Web Admin không cho user thường vào quản lý sản phẩm |
| Requirement / Purpose | FR-12: phân hệ Admin chỉ dành cho admin |
| Technique | Domain |
| Domain / Boundary | C14; —; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Đăng xuất Admin hoặc dùng cửa sổ mới để không còn phiên Admin trước đó. |
| Test data | Email/mật khẩu của tài khoản role=user hợp lệ; không sửa role trong token. |
| Steps | 1. Mở ứng dụng Admin.<br>2. Đăng nhập bằng tài khoản user thường.<br>3. Thử mở phần Sản phẩm nếu UI cho phép. |
| Expected Result | Không vào được màn hình/chức năng quản lý sản phẩm Admin. Không ép câu thông báo cụ thể. |
| Actual Result | Người dùng test Web: UI hiển thị thông báo "BẠN KHÔNG PHẢI LÀ ADMIN", từ chối đăng nhập tài khoản user thường. |
| Status | Pass |
| Evidence | Báo cáo người dùng trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh C15 tương ứng trong workspace. |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID sản phẩm test; khôi phục dữ liệu sản phẩm thử nếu cần hoặc xóa bằng quyền Admin. Không sửa/xóa đối tượng dùng chung; nếu thao tác sai làm ảnh hưởng sản phẩm khác thì lưu evidence trước khi phục hồi. |

### TC-C15-029 — API từ chối tạo sản phẩm tên rỗng

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-029; API từ chối tạo sản phẩm tên rỗng |
| Requirement / Purpose | FR-15: validation name/price và chỉ sửa đúng sản phẩm; bổ sung backend theo yêu cầu người dùng |
| Technique | Domain + BVA |
| Domain / Boundary | C2, C4, C8, C11; B1 off, 0 ký tự; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Admin đăng nhập. Đọc toàn bộ products trước test; không dùng sản phẩm người dùng làm đích. Có danh mục thật. |
| Test data | POST /api/products; JWT Admin hợp lệ; name="", price=100000, description="C15 CLI supplementary test", imageUrl="", category_id lấy từ GET /api/categories. |
| Steps | 1. Gửi POST với tên rỗng và các field còn lại hợp lệ.<br>2. GET /api/products kiểm danh sách và tên sản phẩm mới nếu có.<br>3. Sau lưu evidence, xóa riêng sản phẩm của test nếu backend tạo sai. |
| Expected Result | Không tạo sản phẩm tên rỗng; danh sách products lưu không đổi. |
| Actual Result | Postman CLI: HTTP 200, tạo sản phẩm ID 18 và GET list trả name="". Backend không chặn tên rỗng, dù FE của TC002 đã chặn. |
| Status | Fail |
| Evidence | [CLI log](evidence/C15/postman-cli.log); [JSON results](evidence/C15/c15_backend_results.json); [summary](evidence/C15/c15_backend_recheck_summary.md) |
| Bug ID / Issue URL | [BUG-C15-005](../bug-reports/bugs.md) |
| Cleanup | Sản phẩm test ID 18–22 đã được xóa; assertion cuối xác nhận catalog khớp baseline 14 sản phẩm. Không restart server hoặc reset DB. |

### TC-C15-030 — API sửa tên hợp lệ, giữ mọi sản phẩm khác

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-030; API sửa tên hợp lệ, giữ mọi sản phẩm khác |
| Requirement / Purpose | FR-15: validation name/price và chỉ sửa đúng sản phẩm; bổ sung backend theo yêu cầu người dùng |
| Technique | Functional |
| Domain / Boundary | C1, C4, C8, C11; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Tạo sản phẩm thử riêng qua API Admin, đọc lại đủ dữ liệu chuẩn và snapshot toàn bộ list theo ID; giữ các sản phẩm có sẵn làm đối chứng chỉ đọc. |
| Test data | PUT /api/products/<ID sản phẩm thử mới>; JWT Admin; đổi name thành C15 CLI Updated kèm mã lần chạy, giữ giá 100000, mô tả/ảnh/danh mục như trước. |
| Steps | 1. PUT full body hợp lệ, chỉ đổi name của sản phẩm thử.<br>2. GET /api/products kiểm đích đổi đúng name, các field khác giữ nguyên.<br>3. So sánh mọi ID khác với snapshot trước; dọn riêng sản phẩm thử. |
| Expected Result | Sửa thành công đúng sản phẩm; mọi sản phẩm khác giữ nguyên dữ liệu. Không kết luận về nút Sửa hoặc hiển thị Web. |
| Actual Result | Postman CLI: HTTP 200, ID 19 đổi đúng tên; kiểm target và mọi sản phẩm khác đều Pass. Nút Sửa Web của TC012 vẫn Fail theo báo cáo. |
| Status | Pass |
| Evidence | [CLI log](evidence/C15/postman-cli.log); [JSON results](evidence/C15/c15_backend_results.json); [summary](evidence/C15/c15_backend_recheck_summary.md) |
| Bug ID / Issue URL | — |
| Cleanup | Sản phẩm test ID 18–22 đã được xóa; assertion cuối xác nhận catalog khớp baseline 14 sản phẩm. Không restart server hoặc reset DB. |

### TC-C15-031 — API từ chối sửa tên thành 256 ký tự

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-031; API từ chối sửa tên thành 256 ký tự |
| Requirement / Purpose | FR-15: validation name/price và chỉ sửa đúng sản phẩm; bổ sung backend theo yêu cầu người dùng |
| Technique | Domain + BVA |
| Domain / Boundary | C3, C11; B2 off, 256 ký tự; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Tạo và đọc lại sản phẩm thử riêng tên hợp lệ, giá 100000, danh mục thật; snapshot toàn bộ list. |
| Test data | PUT /api/products/<ID sản phẩm thử>; JWT Admin; name="A".repeat(256), price=100000, mô tả/ảnh/danh mục giữ nguyên. |
| Steps | 1. PUT full body chỉ đổi name thành 256 ký tự ASCII A.<br>2. GET list kiểm name và độ dài, so sánh snapshot.<br>3. Dọn riêng sản phẩm thử sau evidence. |
| Expected Result | Không lưu tên 256; dữ liệu trước test giữ nguyên. |
| Actual Result | Postman CLI: HTTP 200; ID 20 được lưu tên A lặp đủ 256 ký tự. Web TC013 vẫn Blocked, đây là execution API riêng. |
| Status | Fail |
| Evidence | [CLI log](evidence/C15/postman-cli.log); [JSON results](evidence/C15/c15_backend_results.json); [summary](evidence/C15/c15_backend_recheck_summary.md) |
| Bug ID / Issue URL | [BUG-C15-001](../bug-reports/bugs.md) |
| Cleanup | Sản phẩm test ID 18–22 đã được xóa; assertion cuối xác nhận catalog khớp baseline 14 sản phẩm. Không restart server hoặc reset DB. |

### TC-C15-032 — API từ chối sửa giá thành 0

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-032; API từ chối sửa giá thành 0 |
| Requirement / Purpose | FR-15: validation name/price và chỉ sửa đúng sản phẩm; bổ sung backend theo yêu cầu người dùng |
| Technique | Domain |
| Domain / Boundary | C6, C11; mốc giá 0, chưa gán đủ BVA giá; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Sản phẩm thử riêng giá 100000 và dữ liệu hợp lệ; đọc lại/snapshot toàn bộ list trước test. |
| Test data | PUT /api/products/<ID sản phẩm thử>; JWT Admin; chỉ đổi price=0; giữ tên, description, imageUrl và category_id. |
| Steps | 1. PUT full body với price=0.<br>2. GET list kiểm giá đích và toàn bộ danh sách.<br>3. Dọn riêng sản phẩm thử sau evidence. |
| Expected Result | Không lưu giá 0; sản phẩm đích và mọi ID khác giữ nguyên dữ liệu trước test. |
| Actual Result | Postman CLI: HTTP 200; ID 21 lưu price=0. Web TC014 vẫn Blocked, API kiểm riêng đã Fail. |
| Status | Fail |
| Evidence | [CLI log](evidence/C15/postman-cli.log); [JSON results](evidence/C15/c15_backend_results.json); [summary](evidence/C15/c15_backend_recheck_summary.md) |
| Bug ID / Issue URL | [BUG-C15-003](../bug-reports/bugs.md) |
| Cleanup | Sản phẩm test ID 18–22 đã được xóa; assertion cuối xác nhận catalog khớp baseline 14 sản phẩm. Không restart server hoặc reset DB. |

### TC-C15-033 — API từ chối sửa tên thành rỗng

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-C15-033; API từ chối sửa tên thành rỗng |
| Requirement / Purpose | FR-15: validation name/price và chỉ sửa đúng sản phẩm; bổ sung backend theo yêu cầu người dùng |
| Technique | Domain + BVA |
| Domain / Boundary | C2, C11; B1 off, 0 ký tự; xem [analysis](../analysis/C15_ProductManagement.md) |
| Preconditions / Setup | Sản phẩm thử riêng có tên hợp lệ, giá 100000, danh mục thật; đọc lại và snapshot list trước test. |
| Test data | PUT /api/products/<ID sản phẩm thử>; JWT Admin; name="", price=100000, các field khác giữ nguyên. |
| Steps | 1. PUT full body chỉ đổi name thành chuỗi rỗng.<br>2. GET list kiểm tên đích và so snapshot.<br>3. Dọn riêng sản phẩm thử sau evidence. |
| Expected Result | Không lưu tên rỗng; dữ liệu trước test giữ nguyên. |
| Actual Result | Postman CLI: HTTP 200; ID 22 lưu name="". Web TC015 vẫn Blocked, API kiểm riêng đã Fail. |
| Status | Fail |
| Evidence | [CLI log](evidence/C15/postman-cli.log); [JSON results](evidence/C15/c15_backend_results.json); [summary](evidence/C15/c15_backend_recheck_summary.md) |
| Bug ID / Issue URL | [BUG-C15-005](../bug-reports/bugs.md) |
| Cleanup | Sản phẩm test ID 18–22 đã được xóa; assertion cuối xác nhận catalog khớp baseline 14 sản phẩm. Không restart server hoặc reset DB. |
