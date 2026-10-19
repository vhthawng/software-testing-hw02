# Analysis: C15 — Quản lý sản phẩm Admin

| | |
|:---|:---|
| **Tính năng / nền tảng** | FR-15 Product CRUD trên Web Admin; API bổ sung cho quyền ghi và dữ liệu danh mục không nhập được từ UI |
| **Người thiết kế / ngày** | Codex hỗ trợ MSSV 23120355; tên sinh viên chưa cung cấp; tạo 2026-10-19, Asia/Bangkok |
| **Test case chi tiết** | [C15_ProductManagement.md](../test-cases/C15_ProductManagement.md) |
| **Trạng thái** | Draft; chưa thực thi, sinh viên chưa review |

## 1. Phạm vi và nguồn

| Nguồn | Phiên bản / trạng thái | Đã đọc | Thiếu / chưa xác nhận |
|:---|:---|:---|:---|
| [README SUT](../eshop-sut/README.md) | v2.0, 2026-05-14; oracle | FR-15; FR-12, SEC-02/03 cho quyền Admin | Không có max giá hoặc precision tiền |
| [API specification](../eshop-sut/api_specification.md) | Không ghi phiên bản | Products 3.1–3.3; Categories 3.4; login | Chưa có đầy đủ error/status/schema validation |
| [Đề HW02](../2026.HW02.Domain%20Testing_En.md) | HW02-AI | Domain/BVA, AI audit, Git theo bước | Bài giảng chưa có trong workspace |
| App.jsx Admin | Code hỗ trợ thao tác, không phải oracle | Tab Sản phẩm; form, Sửa, Xóa, đăng nhập | Chưa chạy UI/API C15 |

Trong phạm vi: Admin thêm/xem/sửa/xóa sản phẩm; tên bắt buộc và ≤255 ký tự; giá bắt buộc và >0; danh mục bắt buộc có trong danh sách; cập nhật chỉ tác động sản phẩm được chọn. API ghi phải có JWT hợp lệ và role admin theo FR-12.

Ngoài phạm vi: import CSV FR-16, CRUD danh mục FR-14, Mobile, GUI toàn hệ thống, validation ảnh/mô tả chưa nêu; concurrency hoặc sản phẩm đã gắn vào đơn. Không lấy ràng buộc database/code làm yêu cầu.

Domain/EP cho tên, giá, danh mục, xác thực/quyền; BVA cho chiều dài tên. Có các test sửa/xóa và kiểm tra dữ liệu khác giữ nguyên. Không nhân đôi toàn bộ validation Web thành API.

## 2. Input/context và quy tắc

| Mã | Biến / context | Loại | Kiểu, đơn vị | Quy tắc | Nguồn | Explicit / Suy ra |
|:---|:---|:---|:---|:---|:---|:---|
| V1 | name | Nhập trực tiếp | Chuỗi, số ký tự | Bắt buộc; tối đa 255 ký tự | FR-15 | Explicit; độ dài tối thiểu 1 suy ra từ không rỗng |
| V2 | price | Nhập trực tiếp | Số tiền | Bắt buộc; >0 | FR-15 | Explicit |
| V3 | category_id | Lựa chọn / payload | ID danh mục | Bắt buộc thuộc danh sách hiện có | FR-15; API 3.3–3.4 | Explicit |
| V4 | JWT / role | Context | JWT hợp lệ; admin hoặc user | Web Admin chỉ dành cho admin; POST/PUT/DELETE sản phẩm cần JWT hợp lệ và role admin | FR-12, SEC-02/03 | Explicit |
| V5 | ID sản phẩm được sửa/xóa | Context route | ID từ danh sách có thật | Sửa đúng đối tượng; sản phẩm khác giữ nguyên | FR-15; API 3.3 | Explicit |
| V6 | description / imageUrl | Dữ liệu nền API/form | Chuỗi | API mô tả field; không nêu định dạng hoặc độ dài bắt buộc | API 3.3 | Explicit về field, không thêm validation |

Dữ liệu chuẩn để chạy độc lập: đăng nhập Admin; tên `C15 Test Product`, giá 100000, chọn danh mục có thật lấy từ GET /api/categories hoặc dropdown. Mô tả `San pham thu C15`, imageUrl chuỗi rỗng; đây là giá trị chuẩn bị test, không khẳng định là dữ liệu seed. Nếu trường ảnh bị chặn vì quy tắc chưa nêu, ghi rõ vướng mắc thay vì suy ra một yêu cầu mới.

ID danh mục/sản phẩm phải lấy từ dữ liệu thật lúc chạy. Test sửa dùng một sản phẩm thử riêng và ít nhất một sản phẩm đối chứng có tên/giá khác; lưu toàn bộ danh sách trước thao tác để so sánh theo ID, không theo vị trí dòng. Không reset DB dùng chung để tạo fixture.

| O ID | Postcondition / oracle | Nguồn | Cách kiểm chứng |
|:---|:---|:---|:---|
| O1 | Thêm hợp lệ tạo đúng sản phẩm, đọc lại đúng dữ liệu | FR-15 CRUD | UI sau lưu và GET /api/products |
| O2 | Input vi phạm không tạo/lưu dữ liệu trái ràng buộc | FR-15 | Danh sách trước/sau; khi sửa đọc lại ID cũ |
| O3 | Sửa chỉ thay đổi sản phẩm đích; các sản phẩm khác giữ nguyên cả hiển thị lẫn dữ liệu lưu | FR-15 | So UI ngay sau sửa, sau refresh và GET list theo ID |
| O4 | Xóa làm sản phẩm đích không còn trong danh sách; sản phẩm đối chứng vẫn còn | FR-15 CRUD | UI và GET list; không ép GET ID trả 404 |
| O5 | Request ghi thiếu JWT/sai JWT/role user không có hiệu lực | FR-12, SEC-02/03 | Response, danh sách và dữ liệu trước/sau |

O1–O5 là kết quả quan sát, không tạo output class giả.

## 3. Spec gap và quyết định phương pháp

| Mã | Nội dung | Nguồn | Loại | Cách xử lý | TC liên quan |
|:---|:---|:---|:---|:---|:---|
| G1 | Chưa nêu trim/whitespace-only, tên trùng, cách đếm Unicode | FR-15 | Spec gap | Tên biên dùng ASCII A, không tự ép unique/trim | 002–005, 013, 015 |
| G2 | Giá chưa nêu precision, rounding hoặc max | FR-15 | Spec gap | Giá 1 là mẫu dương, không khẳng định nhỏ nhất; 0 có oracle reject rõ; không thêm max | 006–009, 014 |
| G3 | Không nêu missing/null/sai kiểu ở từng field API ngoài yêu cầu bắt buộc | FR-15/API | Spec gap | Chỉ thử tạo thiếu category_id vì field bắt buộc; không suy ra nghĩa PUT một phần hay cách xử lý mọi null | 010 |
| G4 | UI có thể không có lựa chọn danh mục trống | Code hỗ trợ setup | Quyết định phương pháp | Test thiếu/ID không tồn tại chạy API thay vì sửa DOM | 010, 017 |
| G5 | Chưa nêu lỗi HTTP/message, dialog xác nhận xóa, hard/soft-delete | FR-15/API | Spec gap | Không ép câu lỗi/status hoặc yêu cầu dialog; kiểm invariant dữ liệu | Test âm, 016 |
| G6 | Bài giảng chưa có | HW02 | Giới hạn phương pháp | Dùng BVA 2-point cho tên, cần sinh viên đối chiếu | 002–005 |

Không tự dùng các policy chốt cho B08 làm yêu cầu C15. Các gap không chặn test có oracle rõ. Mọi status còn Not Run cho đến khi có execution thật.

## 4. Domain Testing: phân vùng tương đương

Chia tên theo bắt buộc/max, giá theo bắt buộc và dấu, danh mục theo có trong danh sách, JWT/role theo FR-12. Splitting Principle dùng để tách giá 0/âm và token thiếu/sai vì đại diện các điều kiện cần kiểm riêng. Không tách ký tự Unicode hay giá cực đại nếu spec không định nghĩa predicate đó.

| Class ID | Input/context | Điều kiện | Validity | Đại diện | Nguồn |
|:---|:---|:---|:---|:---|:---|
| C1 | name | 1–255 ký tự | Hợp lệ | C15 Test Product; A; A lặp 255 | FR-15 |
| C2 | name | Rỗng, 0 ký tự | Không hợp lệ | Chuỗi rỗng | FR-15 |
| C3 | name | >255 ký tự | Không hợp lệ | A lặp 256 | FR-15 |
| C4 | price | >0 | Hợp lệ | 100000; 1 | FR-15 |
| C5 | price | Không nhập | Không hợp lệ | Ô giá trống | FR-15 bắt buộc |
| C6 | price | =0 | Không hợp lệ | 0 | FR-15 |
| C7 | price | <0 | Không hợp lệ | -1 | FR-15 |
| C8 | category_id | Thuộc danh sách hiện có | Hợp lệ | ID thật đọc từ categories | FR-15 |
| C9 | category_id | Không cung cấp khi tạo | Không hợp lệ | POST thiếu category_id | FR-15 bắt buộc |
| C10 | category_id | Không thuộc danh sách | Không hợp lệ | ID dương đã xác minh không tồn tại | FR-15 |
| C11 | JWT/role | JWT hợp lệ, role admin | Hợp lệ | Token Admin lấy lúc chạy | FR-12 |
| C12 | JWT | Không gửi | Không hợp lệ | Bỏ Authorization | FR-12, SEC-02 |
| C13 | JWT | Token sai | Không hợp lệ | invalid.c15.token | FR-12, SEC-02 |
| C14 | role | Token hợp lệ, role user | Không hợp lệ đối với Admin | Token user thật | FR-12, SEC-03 |

Single Fault: test tên/giá/danh mục giữ những field còn lại chuẩn và Admin hợp lệ. Test quyền giữ body chuẩn và ID tồn tại. Test hợp lệ có thể đồng thời kiểm nhiều trường; test sửa đối chứng kiểm toàn bộ ID khác vì FR-15 nói các sản phẩm khác giữ nguyên. Không coi response/snapshot là biến đầu vào.

