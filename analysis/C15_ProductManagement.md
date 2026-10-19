# Analysis: C15 — Quản lý sản phẩm Admin

| | |
|:---|:---|
| **Tính năng / nền tảng** | FR-15 Product CRUD trên Web Admin; API bổ sung cho quyền ghi và dữ liệu danh mục không nhập được từ UI |
| **Người thiết kế / ngày** | Codex hỗ trợ MSSV 23120355; tên sinh viên chưa cung cấp; tạo 2026-10-19, Asia/Bangkok |
| **Test case chi tiết** | [C15_ProductManagement.md](../test-cases/C15_ProductManagement.md) |
| **Trạng thái** | 33 TC: 9 Pass, 9 Fail, 3 Blocked, 12 Not Run; Web do người dùng báo cáo, 5 API bổ sung có CLI evidence; review tổng thể pending |

## 1. Phạm vi và nguồn

| Nguồn | Phiên bản / trạng thái | Đã đọc | Thiếu / chưa xác nhận |
|:---|:---|:---|:---|
| [README SUT](../eshop-sut/README.md) | v2.0, 2026-05-14; oracle | FR-15; FR-12, SEC-02/03 cho quyền Admin | Không có max giá hoặc precision tiền |
| [API specification](../eshop-sut/api_specification.md) | Không ghi phiên bản | Products 3.1–3.3; Categories 3.4; login | Chưa có đầy đủ error/status/schema validation |
| [Đề HW02](../2026.HW02.Domain%20Testing_En.md) | HW02-AI | Domain/BVA, AI audit, Git theo bước | Bài giảng chưa có trong workspace |
| App.jsx Admin | Code hỗ trợ thao tác, không phải oracle | Tab Sản phẩm; form, Sửa, Xóa, đăng nhập | Web có báo cáo người dùng; API bổ sung đã chạy; AI chưa tự kiểm chứng UI |

Trong phạm vi: Admin thêm/xem/sửa/xóa sản phẩm; tên bắt buộc và ≤255 ký tự; giá bắt buộc và >0; danh mục bắt buộc có trong danh sách; cập nhật chỉ tác động sản phẩm được chọn. API ghi phải có JWT hợp lệ và role admin theo FR-12.

Ngoài phạm vi: import CSV FR-16, CRUD danh mục FR-14, Mobile, GUI toàn hệ thống, validation ảnh/mô tả chưa nêu; concurrency hoặc sản phẩm đã gắn vào đơn. Không lấy ràng buộc database/code làm yêu cầu.

Domain/EP cho tên, giá, danh mục, xác thực/quyền; BVA cho chiều dài tên. Có các test sửa/xóa và kiểm tra dữ liệu khác giữ nguyên. Không nhân đôi toàn bộ validation Web thành API. TC029–033 là bổ sung được người dùng yêu cầu để kiểm backend cho tên rỗng và sửa sản phẩm khi UI bị chặn; giữ riêng kết quả Web/API.

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

Không tự dùng các policy chốt cho B08 làm yêu cầu C15. Các gap không chặn test có oracle rõ. Status được cập nhật theo nguồn execution: Web do người dùng báo cáo; API bổ sung có log. Không chuyển test Web Blocked thành Pass/Fail theo API.

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

## 5. Boundary Value Analysis

Tên bắt buộc suy ra chiều dài tối thiểu 1; max 255 explicit trong FR-15. Dùng ASCII A để cách đếm byte/code point/UTF-16 không làm thay đổi các điểm đang kiểm. Độ mịn chiều dài là 1 ký tự. Chọn 2-point cho mỗi biên: một điểm on và một điểm off đổi kết quả hợp lệ, chờ đối chiếu thuật ngữ bài giảng.

| Boundary ID | Class liên quan | Quy tắc / nguồn | Đóng/mở | Độ mịn | On-point | Off-point | Expected | TC |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| B1 | C1, C2 | name không rỗng: L≥1; FR-15 | Đóng | 1 ký tự | A, L=1 | Chuỗi rỗng, L=0 | On chấp nhận; off không lưu tên rỗng | 003 on, 002 off |
| B2 | C1, C3 | L≤255; FR-15 | Đóng | 1 ký tự | A lặp 255 | A lặp 256 | On lưu đủ 255; off không lưu tên 256 | 004 on, 005 off |

**Giá:** mốc 0 là biên mở của price>0; TC007 kiểm đúng mốc 0 phải bị từ chối, TC008 kiểm đại diện dương 1, TC009 kiểm đại diện âm -1. Vì spec chưa nêu độ mịn/precision, không gọi 1 là giá dương gần 0 nhất, không coi tập giá chỉ là số nguyên và chưa tuyên bố bộ này phủ BVA giá 2/3-point đầy đủ. Các test giá vẫn có oracle rõ theo dấu của số, nhãn Domain. BVA tên B1/B2 đã có on/off đầy đủ trong phạm vi UI.

**Biên bị chặn tại UI:** nếu ô tên chỉ nhận 255 ký tự, TC005 kiểm được việc ngăn nhập vượt max. Ghi actual form/request thật; không tính là coverage backend nhận 256. TC013 khi sửa cũng ghi rõ đường chặn nếu có. Không tự thêm API duplicate mọi validation; độ phủ backend tên dài là thiếu sót đã biết nếu UI chặn.

Danh mục và role là tập giá trị rời rạc không thứ tự, không có min/max số từ ID danh mục. Không coi ID nhỏ nhất/lớn nhất là biên nghiệp vụ.

## 6. Test Matrix

Tên/giá/danh mục chuẩn nằm ở mục 2. Mỗi dòng là một TC duy nhất; dữ liệu trong card ghi đầy đủ bằng lời dễ chạy. Test âm chỉ đổi field/quyền nêu ở dòng đó, giữ các field khác hợp lệ.

| TC ID | Requirement / nguồn | Technique | Dữ liệu / context | Class IDs | Boundary / điểm | Validity | Expected |
|:---|:---|:---|:---|:---|:---|:---|:---|
| TC-C15-001 | FR-15: Admin thêm/xem sản phẩm | Domain | Tên C15 Test Product; giá 100000; danh mục chọn từ danh sách. | C1, C4, C8, C11 | — | Hợp lệ | Tạo một sản phẩm mới đúng dữ liệu; đọc lại được sau refresh. |
| TC-C15-002 | FR-15: tên bắt buộc | Domain + BVA | Tên để trống; giá 100000; danh mục có thật. | C2 | B1 off, 0 ký tự | Không hợp lệ | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. |
| TC-C15-003 | FR-15: tên bắt buộc, tối đa 255 ký tự | Domain + BVA | Tên A (1 ký tự ASCII); giá 100000; danh mục có thật. | C1 | B1 on, 1 ký tự | Hợp lệ | Tạo sản phẩm thành công; tên lưu đúng A, không mất ký tự. |
| TC-C15-004 | FR-15: tối đa 255 ký tự | Domain + BVA | Tên là A lặp đúng 255 lần; giá 100000; danh mục có thật. | C1 | B2 on, 255 ký tự | Hợp lệ | Sản phẩm được tạo; tên lưu đủ 255 ký tự, bằng chính chuỗi đã gửi. |
| TC-C15-005 | FR-15: tối đa 255 ký tự | Domain + BVA | Tên là A lặp đúng 256 lần; giá 100000; danh mục có thật. | C3 | B2 off, 256 ký tự | Không hợp lệ | Không lưu tên dài 256. Nếu UI chặn hoặc cắt nhập ở 255, ghi rõ input thực tế và tên lưu ≤255; không kết luận backend đã được test với 256 nếu request không gửi đủ 256. |
| TC-C15-006 | FR-15: giá bắt buộc | Domain | Tên C15 Test Product; giá để trống; danh mục có thật. | C5 | — | Không hợp lệ | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. |
| TC-C15-007 | FR-15: giá phải >0 | Domain | Tên C15 Test Product; giá 0; danh mục có thật. | C6 | Mốc giá 0; chưa gán đủ BVA giá | Không hợp lệ | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. |
| TC-C15-008 | FR-15: giá phải >0 | Domain | Tên C15 Test Product; giá 1; danh mục có thật. Giá 1 là đại diện dương, không khẳng định là giá nhỏ nhất hệ thống. | C4 | — | Hợp lệ | Tạo sản phẩm với giá lưu bằng 1; tên và danh mục đúng dữ liệu gửi. |
| TC-C15-009 | FR-15: giá phải >0 | Domain | Tên C15 Test Product; giá -1; danh mục có thật. | C7 | — | Không hợp lệ | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. |
| TC-C15-010 | FR-15: danh mục bắt buộc | Domain | POST /api/products; JWT Admin hợp lệ; body chuẩn nhưng bỏ field category_id hoàn toàn. | C9, C11 | — | Không hợp lệ | Không tạo sản phẩm với dữ liệu vi phạm; danh sách lưu giữ nguyên. Không ép HTTP status hoặc câu lỗi cụ thể. |
| TC-C15-011 | FR-15: danh mục thuộc danh sách hiện có | Domain | Tên C15 Test Product; giá 100000; chọn danh mục có thật khác lựa chọn mặc định. | C8 | — | Hợp lệ | Sản phẩm lưu đúng ID danh mục đã chọn, không tự đổi về danh mục mặc định. |
| TC-C15-012 | FR-15: chỉ sản phẩm được sửa thay đổi | Functional | Đổi tên sản phẩm đích từ C15 Original thành C15 Updated; giữ giá 100000 và danh mục. | C1, C4, C8, C11 | — | Hợp lệ | Chỉ sản phẩm đích có tên mới; giá/danh mục giữ nguyên. Mọi sản phẩm khác giữ đúng tên và dữ liệu cả trên UI ngay sau lưu lẫn sau refresh/backend. |
| TC-C15-013 | FR-15: tên tối đa 255 ký tự khi sửa | Domain | Đổi name của sản phẩm đích thành "A".repeat(256); giữ giá/danh mục. | C3 | — | Không hợp lệ | Không lưu tên dài 256; nếu gửi đủ chuỗi thì không cập nhật dữ liệu vi phạm. Nếu UI giới hạn đầu vào 255, ghi rõ chỉ kiểm được chặn nhập, không khẳng định đã gửi 256 xuống API. |
| TC-C15-014 | FR-15: giá >0 khi sửa | Domain | Giữ tên C15 Original; đổi giá 100000 thành 0; giữ danh mục. | C6 | — | Không hợp lệ | Không cập nhật giá thành 0; dữ liệu sản phẩm đích và sản phẩm khác giữ nguyên. |
| TC-C15-015 | FR-15: tên bắt buộc khi sửa | Domain | Xóa hết tên; giữ giá 100000 và danh mục. | C2 | — | Không hợp lệ | Không lưu tên rỗng; sản phẩm đích giữ nguyên dữ liệu trước test. |
| TC-C15-016 | FR-15: Admin xóa sản phẩm | Functional | Xóa duy nhất sản phẩm C15 Original theo ID đã ghi. | C11 | — | Hợp lệ | Sản phẩm bị xóa không còn trong danh sách; sản phẩm đối chứng và các ID khác vẫn giữ nguyên. Không bắt buộc phải xuất hiện dialog xác nhận. |
| TC-C15-017 | FR-15: danh mục phải thuộc danh sách có sẵn | Domain | POST /api/products; token Admin; body chuẩn, category_id là số dương không nằm trong GET /api/categories. | C10, C11 | — | Không hợp lệ | Không tạo sản phẩm tham chiếu danh mục không tồn tại; không ép status/message cụ thể. |
| TC-C15-018 | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ | Domain | POST /api/products; không gửi Authorization. Body chuẩn: name=C15 Unauthorized Create; giá 100000; danh mục có thật. | C12 | — | Không hợp lệ | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| TC-C15-019 | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ | Domain | POST /api/products; Authorization: Bearer invalid.c15.token. Body chuẩn: name=C15 Unauthorized Create; giá 100000; danh mục có thật. | C13 | — | Không hợp lệ | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| TC-C15-020 | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ | Domain | POST /api/products; Authorization: Bearer <token hợp lệ của tài khoản role=user>. Body chuẩn: name=C15 Unauthorized Create; giá 100000; danh mục có thật. | C14 | — | Không hợp lệ | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| TC-C15-021 | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ | Domain | PUT /api/products/<ID sản phẩm thử>; không gửi Authorization. Body chuẩn, name=C15 Unauthorized Update; giá 100000; danh mục có thật. | C12 | — | Không hợp lệ | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| TC-C15-022 | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ | Domain | PUT /api/products/<ID sản phẩm thử>; Authorization: Bearer invalid.c15.token. Body chuẩn, name=C15 Unauthorized Update; giá 100000; danh mục có thật. | C13 | — | Không hợp lệ | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| TC-C15-023 | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ | Domain | PUT /api/products/<ID sản phẩm thử>; Authorization: Bearer <token hợp lệ của tài khoản role=user>. Body chuẩn, name=C15 Unauthorized Update; giá 100000; danh mục có thật. | C14 | — | Không hợp lệ | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| TC-C15-024 | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ | Domain | DELETE /api/products/<ID sản phẩm thử>; không gửi Authorization. Không body. | C12 | — | Không hợp lệ | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| TC-C15-025 | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ | Domain | DELETE /api/products/<ID sản phẩm thử>; Authorization: Bearer invalid.c15.token. Không body. | C13 | — | Không hợp lệ | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| TC-C15-026 | FR-12, SEC-02/03: API ghi sản phẩm chỉ cho JWT Admin hợp lệ | Domain | DELETE /api/products/<ID sản phẩm thử>; Authorization: Bearer <token hợp lệ của tài khoản role=user>. Không body. | C14 | — | Không hợp lệ | Request bị từ chối; không có sản phẩm mới nếu POST, sản phẩm đích không đổi nếu PUT, không bị xóa nếu DELETE. Mọi sản phẩm khác giữ nguyên; không ép HTTP status/message. |
| TC-C15-027 | FR-15 CRUD và FR-12: quyền Admin hợp lệ | Domain | JWT Admin hợp lệ; tạo tên C15 API Product, giá 100000; sửa tên C15 API Updated; dùng danh mục có thật. | C1, C4, C8, C11 | — | Hợp lệ | Admin thực hiện thành công thêm/xem/sửa/xóa; dữ liệu lưu đúng, chỉ sản phẩm thử bị tác động. Đây là đường đối chứng để các test từ chối không Pass do API hoàn toàn không hoạt động. |
| TC-C15-028 | FR-12: phân hệ Admin chỉ dành cho admin | Domain | Email/mật khẩu của tài khoản role=user hợp lệ; không sửa role trong token. | C14 | — | Không hợp lệ | Không vào được màn hình/chức năng quản lý sản phẩm Admin. Không ép câu thông báo cụ thể. |
| TC-C15-029 | FR-15; backend bổ sung theo yêu cầu người dùng | Domain + BVA | POST /api/products; JWT Admin hợp lệ; name="", price=100000, description="C15 CLI supplementary test", imageUrl="", category_id lấy từ GET /api/categories. | C2, C4, C8, C11 | B1 off, 0 ký tự | Không hợp lệ | Không tạo sản phẩm tên rỗng; danh sách products lưu không đổi. |
| TC-C15-030 | FR-15; backend bổ sung theo yêu cầu người dùng | Functional | PUT /api/products/<ID sản phẩm thử mới>; JWT Admin; đổi name thành C15 CLI Updated kèm mã lần chạy, giữ giá 100000, mô tả/ảnh/danh mục như trước. | C1, C4, C8, C11 | — | Hợp lệ | Sửa thành công đúng sản phẩm; mọi sản phẩm khác giữ nguyên dữ liệu. Không kết luận về nút Sửa hoặc hiển thị Web. |
| TC-C15-031 | FR-15; backend bổ sung theo yêu cầu người dùng | Domain + BVA | PUT /api/products/<ID sản phẩm thử>; JWT Admin; name="A".repeat(256), price=100000, mô tả/ảnh/danh mục giữ nguyên. | C3, C11 | B2 off, 256 ký tự | Không hợp lệ | Không lưu tên 256; dữ liệu trước test giữ nguyên. |
| TC-C15-032 | FR-15; backend bổ sung theo yêu cầu người dùng | Domain | PUT /api/products/<ID sản phẩm thử>; JWT Admin; chỉ đổi price=0; giữ tên, description, imageUrl và category_id. | C6, C11 | — | Không hợp lệ | Không lưu giá 0; sản phẩm đích và mọi ID khác giữ nguyên dữ liệu trước test. |
| TC-C15-033 | FR-15; backend bổ sung theo yêu cầu người dùng | Domain + BVA | PUT /api/products/<ID sản phẩm thử>; JWT Admin; name="", price=100000, các field khác giữ nguyên. | C2, C11 | B1 off, 0 ký tự | Không hợp lệ | Không lưu tên rỗng; dữ liệu trước test giữ nguyên. |

## 7. Truy vết và độ phủ

Các ID test trong bảng dưới có prefix TC-C15-. Trạng thái Đã thiết kế không đồng nghĩa đã chạy hay Pass.

| Requirement / Class / Boundary point | TC IDs | Trạng thái | Lý do |
|:---|:---|:---|:---|
| FR-15 thêm/xem | 001, 027 | Đã thiết kế | Web lưu/đọc lại; API đối chứng hợp lệ |
| FR-15 tên bắt buộc/max | 002–005, 013, 015 | Đã thiết kế | Create biên 0/1/255/256, edit vi phạm |
| FR-15 giá bắt buộc/dương | 006–009, 014 | Đã thiết kế | Trống, 0, dương, âm; edit giá 0 |
| FR-15 danh mục bắt buộc/có sẵn | 010, 011, 017 | Đã thiết kế | Thiếu field, chọn danh mục thật, ID không tồn tại |
| FR-15 sửa chỉ đúng đối tượng | 012, 027 | Đã thiết kế | So toàn bộ ID khác trên UI/backend |
| FR-15 xóa | 016, 027 | Đã thiết kế | Xóa sản phẩm thử riêng |
| FR-12 API POST có quyền Admin | 018–020, 027 | Đã thiết kế | JWT thiếu/sai/user và đối chứng Admin |
| FR-12 API PUT có quyền Admin | 021–023, 027 | Đã thiết kế | JWT thiếu/sai/user và đối chứng Admin |
| FR-12 API DELETE có quyền Admin | 024–026, 027 | Đã thiết kế | JWT thiếu/sai/user và đối chứng Admin |
| FR-12 Web Admin | 028; thao tác Admin 001 | Đã thiết kế | Chặn user thường, Admin thao tác được |
| C1 | 001, 003, 004, 012, 027 | Đã thiết kế | Có đại diện trong card |
| C2 | 002, 015 | Đã thiết kế | Có đại diện trong card |
| C3 | 005, 013 | Đã thiết kế | Có đại diện trong card |
| C4 | 001, 008, 012, 027 | Đã thiết kế | Có đại diện trong card |
| C5 | 006 | Đã thiết kế | Có đại diện trong card |
| C6 | 007, 014 | Đã thiết kế | Có đại diện trong card |
| C7 | 009 | Đã thiết kế | Có đại diện trong card |
| C8 | 001, 011, 012, 027 | Đã thiết kế | Có đại diện trong card |
| C9 | 010 | Đã thiết kế | Có đại diện trong card |
| C10 | 017 | Đã thiết kế | Có đại diện trong card |
| C11 | 001, 010, 012, 016, 017, 027 | Đã thiết kế | Có đại diện trong card |
| C12 | 018, 021, 024 | Đã thiết kế | Có đại diện trong card |
| C13 | 019, 022, 025 | Đã thiết kế | Có đại diện trong card |
| C14 | 020, 023, 026, 028 | Đã thiết kế | Có đại diện trong card |
| B1 on / off | 003 / 002 | Đã thiết kế | L=1 / 0 |
| B2 on / off | 004 / 005 | Đã thiết kế | L=255 / 256; ghi giới hạn UI nếu input bị chặn |
| BVA giá đủ on/off gần nhất | — | Chưa xác định | Precision/độ mịn G2 chưa có |

| Chỉ số | Số lượng |
|:---|:---|
| Tổng test duy nhất | 33 |
| Chỉ Domain | 23 |
| Chỉ BVA | 0 |
| Cả Domain/BVA | 7 |
| Chỉ kỹ thuật chức năng bổ sung | 3 |
| Hợp lệ / Không hợp lệ | 9 / 24 |
| Đã hoàn thành (Pass + Fail) / Pass / Fail | 18 / 9 / 9 |
| Blocked / Not Run | 3 / 12 |

33=23+0+7+3. Đã thử 21 TC: 18 hoàn thành Pass/Fail và 3 Blocked; 12 API ban đầu vẫn Not Run. Mức giảm tổ hợp không định lượng vì tập tên/giá không có tổng số hữu hạn được đặc tả; không khẳng định bộ tối thiểu toàn cục.

**Kết quả và phần bổ sung:** Web TC002 Pass tại FE; API TC029 Fail chứng minh backend không chặn cùng giá trị rỗng. TC012 Web Fail do không mở được sửa; TC013–015 Web Blocked; TC030 API Pass chỉ chứng minh update dữ liệu đúng, không thay thế kiểm UI tức thời của TC012. TC031/032/033 Fail vì backend lưu tên 256/giá 0/tên rỗng. Bằng chứng: [CLI summary](../test-cases/evidence/C15/c15_backend_recheck_summary.md).

| Truy vết bổ sung | TC IDs | Trạng thái | Lý do |
|:---|:---|:---|:---|
| FR-15 tên bắt buộc; C2, B1 off | 029, 033 | Đã thiết kế và chạy API | Tạo/sửa tên rỗng, khác nền tảng Web002/015 |
| FR-15 tên ≤255; C3, B2 off | 031 | Đã thiết kế và chạy API | PUT gửi đủ 256 trực tiếp |
| FR-15 giá >0; C6 | 032 | Đã thiết kế và chạy API | PUT giá 0 |
| FR-15 sửa chỉ đúng đích; C1/C4/C8/C11 | 030 | Đã thiết kế và chạy API | Tất cả ID khác giữ nguyên |

## 8. Giới hạn và kỹ thuật bổ sung

Chưa kiểm tất cả tổ hợp nhiều lỗi, mọi validation backend qua API, cập nhật đồng thời, price precision, tên Unicode/whitespace hoặc xóa sản phẩm đã gắn đơn/giỏ. Chưa có test sửa danh mục thành ID không tồn tại; TC017 hiện chỉ kiểm tạo mới. Nếu cần mở rộng, ghi test bổ sung theo mục tiêu và giữ oracle từ spec, không tự thêm policy.

GET sản phẩm là API công khai trong contract; không tự yêu cầu JWT Admin cho GET /api/products chỉ vì nó được UI Admin gọi. Các request ghi phải kiểm đủ cả JWT và role trên từng method. Có 5 bug C15 nháp từ Web người dùng báo cáo và CLI runtime. Không kết luận lỗi nút Sửa từ code tĩnh; TC012 dựa trên báo cáo người dùng.

## 9. Review và thay đổi

| Phiên bản | Ngày | Thay đổi | Lý do | TC ảnh hưởng | Review |
|:---|:---|:---|:---|:---|:---|
| v0.1 | 2026-10-19 | Scope, input/context, oracle và spec gaps | Đọc FR-15/FR-12 và API | Toàn bộ | Sinh viên chưa review |
| v0.2 | 2026-10-19 | 14 lớp và 28 card ban đầu | Single Fault, CRUD và phân quyền từng method | 001–028 | AI thiết kế; sinh viên chưa review |
| v0.3 | 2026-10-19 | BVA tên 0/1/255/256, matrix/trace và giới hạn precision giá | Không tự đặt bước tiền nhỏ nhất hoặc ID biên danh mục | 002–005, 007–009 | AI kiểm tra nhất quán; chưa thực thi |

| v0.4 | 2026-10-19 | Nhập 16 Web theo báo cáo; thêm và chạy API029–033 | Người dùng yêu cầu kiểm BE khi FE chặn tên rỗng hoặc không mở được sửa | 001–009, 011–016, 028–033 | 9 Pass, 9 Fail, 3 Blocked, 12 Not Run; cần review kết quả cuối |
