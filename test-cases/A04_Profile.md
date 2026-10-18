# Test Cases: A04 — Hồ sơ cá nhân Web/API

- Trạng thái thiết kế: Draft; sinh viên chưa review.
- Nguồn: [FR-04 và SEC-02/06](../eshop-sut/README.md); [API Users](../eshop-sut/api_specification.md).
- Phân tích: [A04_Profile.md](../analysis/A04_Profile.md).
- Tạo/cập nhật: 2026-10-18 (Asia/Bangkok). Chưa thực thi, chưa có runtime/build xác nhận.

## Chuẩn bị và quy ước

Backend localhost:3000, Web localhost:5173/profile. Dùng tài khoản U riêng role=user (có thể dùng test@eshop.com / Test1234! trên DB demo riêng) và user W khác U chuẩn bị trong fixture. Không dùng tài khoản thật. Trước mỗi case thiết lập snapshot U: name=Nguyễn Văn An, phone=0987654321, shipping_address=123 Lê Lợi, Quận 1, TP.HCM; giữ email/role=user. Chuẩn bị bằng fixture được kiểm soát hoặc API hợp lệ; không sửa source SUT. Nếu setup không thể hoàn thành, ghi Blocked và nguyên nhân, không sửa expected. ID/token lấy lúc chạy, không ghi token thật vào bài. Đọc U/W bằng GET /api/users/me với token tương ứng. Khi cần role lưu, đăng nhập lại để lấy dữ liệu mới; không chỉ xem JWT cũ. Mỗi card nhắc lại giá trị cần thiết.

## 1. Tổng hợp

Tổng: 22 test duy nhất; tất cả Not Run. Bảng này chưa phải kết quả kiểm thử.

| TC ID | Title | Requirement | Technique | Dữ liệu chính | Expected | Status | Bug |
|:---|:---|:---|:---|:---|:---|:---|:---|
| TC-A04-001 | SĐT hợp lệ 10 chữ số qua Web | FR-04 | Domain | 0912345678 | Chấp nhận cập nhật U; đọc lại sau refresh/GET cho thấy name, phone, shipping_address đúng payload, email và role giữ nguyên. | Not Run | — |
| TC-A04-002 | SĐT dưới 10 chữ số qua Web | FR-04 | Domain | 091234567 | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. | Not Run | — |
| TC-A04-003 | SĐT trên 11 chữ số qua Web | FR-04 | Domain | 091234567890 | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. | Not Run | — |
| TC-A04-004 | SĐT không bắt đầu bằng 0 qua Web | FR-04 | Domain | 1912345678 | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. | Not Run | — |
| TC-A04-005 | SĐT chứa chữ cái qua Web | FR-04 | Domain | 091234567a | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. | Not Run | — |
| TC-A04-006 | SĐT chứa dấu gạch ngang qua Web | FR-04 | Domain | 09123-5678 | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. | Not Run | — |
| TC-A04-007 | SĐT chứa khoảng trắng bên trong qua Web | FR-04 | Domain | 09123 5678 | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. | Not Run | — |
| TC-A04-008 | SĐT hợp lệ 10 chữ số qua API | FR-04 | Domain | 0912345678 | Chấp nhận cập nhật U; đọc lại sau refresh/GET cho thấy name, phone, shipping_address đúng payload, email và role giữ nguyên. | Not Run | — |
| TC-A04-009 | SĐT dưới 10 chữ số qua API | FR-04 | Domain | 091234567 | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. | Not Run | — |
| TC-A04-010 | SĐT trên 11 chữ số qua API | FR-04 | Domain | 091234567890 | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. | Not Run | — |
| TC-A04-011 | SĐT không bắt đầu bằng 0 qua API | FR-04 | Domain | 1912345678 | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. | Not Run | — |
| TC-A04-012 | SĐT chứa chữ cái qua API | FR-04 | Domain | 091234567a | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. | Not Run | — |
| TC-A04-013 | SĐT chứa dấu gạch ngang qua API | FR-04 | Domain | 09123-5678 | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. | Not Run | — |
| TC-A04-014 | SĐT chứa khoảng trắng bên trong qua API | FR-04 | Domain | 09123 5678 | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. | Not Run | — |
| TC-A04-015 | Cập nhật riêng họ tên có dấu | FR-04 | Domain | name | Họ tên U được lưu đúng Nguyễn Thị Bình; phone và địa chỉ giữ nguyên snapshot. Refresh vẫn giữ tên mới. | Not Run | — |
| TC-A04-016 | Cập nhật riêng địa chỉ mặc định | FR-04 | Domain | address | Địa chỉ U được lưu đúng 45 Nguyễn Huệ, Quận 1, TP.HCM; name và phone giữ nguyên snapshot. Refresh vẫn giữ địa chỉ mới. | Not Run | — |
| TC-A04-017 | Email không sửa được qua giao diện | FR-04 | Domain | email | Không thể sửa email bằng thao tác UI thông thường (chỉ đọc/disabled); sau cập nhật trường cho phép và refresh, email U vẫn bằng snapshot. | Not Run | — |
| TC-A04-018 | Từ chối cập nhật khi thiếu JWT | FR-04, SEC-02 | Domain | missing | Không cập nhật hồ sơ U hoặc W; trả phản hồi từ chối xác thực. Kiểm lại bằng token hợp lệ, dữ liệu giữ nguyên; không ép mã HTTP cụ thể. | Not Run | — |
| TC-A04-019 | Từ chối cập nhật khi JWT không hợp lệ | FR-04, SEC-02 | Domain | invalidtoken | Không cập nhật U hoặc W; từ chối xác thực với token sai. Đọc lại bằng token hợp lệ cho thấy snapshot không đổi. | Not Run | — |
| TC-A04-020 | Không cho tự đổi role qua payload | FR-04, SEC-06 | Domain | role | Role lưu của U vẫn là user sau request và đăng nhập lại. Cho phép reject toàn request hoặc bỏ qua role và cập nhật trường được phép; không chấp nhận role=admin. Không dùng role trong JWT cũ làm bằng chứng duy nhất. | Not Run | — |
| TC-A04-021 | Không cập nhật user khác qua field id | FR-04 | Domain | target: id | Hồ sơ W giữ nguyên snapshot khi dùng token U. Có thể từ chối request hoặc bỏ qua target rồi cập nhật U; không được cập nhật W. Đọc lại W bằng token W. | Not Run | — |
| TC-A04-022 | Không cập nhật user khác qua field user_id | FR-04 | Domain | target: user_id | Hồ sơ W giữ nguyên snapshot khi dùng token U. Có thể từ chối request hoặc bỏ qua target rồi cập nhật U; không được cập nhật W. Đọc lại W bằng token W. | Not Run | — |

## 2. Chi tiết

### TC-A04-001 — SĐT hợp lệ 10 chữ số qua Web

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT hợp lệ 10 chữ số qua Web. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C1, C22, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Đăng nhập U, mở /profile; ghi snapshot.<br>2. Nhập phone **0912345678**, giữ name/address nominal.<br>3. Nhấn Cập nhật; ghi phản hồi và request nếu có.<br>4. Refresh trang và GET /api/users/me bằng token U; đối chiếu snapshot. |
| Expected Result | Chấp nhận cập nhật U; đọc lại sau refresh/GET cho thấy name, phone, shipping_address đúng payload, email và role giữ nguyên. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-002 — SĐT dưới 10 chữ số qua Web

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT dưới 10 chữ số qua Web. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C2, C23, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"091234567","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Đăng nhập U, mở /profile; ghi snapshot.<br>2. Nhập phone **091234567**, giữ name/address nominal.<br>3. Nhấn Cập nhật; ghi phản hồi và request nếu có.<br>4. Refresh trang và GET /api/users/me bằng token U; đối chiếu snapshot. |
| Expected Result | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-003 — SĐT trên 11 chữ số qua Web

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT trên 11 chữ số qua Web. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C3, C23, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"091234567890","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Đăng nhập U, mở /profile; ghi snapshot.<br>2. Nhập phone **091234567890**, giữ name/address nominal.<br>3. Nhấn Cập nhật; ghi phản hồi và request nếu có.<br>4. Refresh trang và GET /api/users/me bằng token U; đối chiếu snapshot. |
| Expected Result | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-004 — SĐT không bắt đầu bằng 0 qua Web

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT không bắt đầu bằng 0 qua Web. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C4, C23, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"1912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Đăng nhập U, mở /profile; ghi snapshot.<br>2. Nhập phone **1912345678**, giữ name/address nominal.<br>3. Nhấn Cập nhật; ghi phản hồi và request nếu có.<br>4. Refresh trang và GET /api/users/me bằng token U; đối chiếu snapshot. |
| Expected Result | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-005 — SĐT chứa chữ cái qua Web

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT chứa chữ cái qua Web. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C5, C23, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"091234567a","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Đăng nhập U, mở /profile; ghi snapshot.<br>2. Nhập phone **091234567a**, giữ name/address nominal.<br>3. Nhấn Cập nhật; ghi phản hồi và request nếu có.<br>4. Refresh trang và GET /api/users/me bằng token U; đối chiếu snapshot. |
| Expected Result | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-006 — SĐT chứa dấu gạch ngang qua Web

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT chứa dấu gạch ngang qua Web. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C6, C23, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"09123-5678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Đăng nhập U, mở /profile; ghi snapshot.<br>2. Nhập phone **09123-5678**, giữ name/address nominal.<br>3. Nhấn Cập nhật; ghi phản hồi và request nếu có.<br>4. Refresh trang và GET /api/users/me bằng token U; đối chiếu snapshot. |
| Expected Result | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-007 — SĐT chứa khoảng trắng bên trong qua Web

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT chứa khoảng trắng bên trong qua Web. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C7, C23, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"09123 5678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Đăng nhập U, mở /profile; ghi snapshot.<br>2. Nhập phone **09123 5678**, giữ name/address nominal.<br>3. Nhấn Cập nhật; ghi phản hồi và request nếu có.<br>4. Refresh trang và GET /api/users/me bằng token U; đối chiếu snapshot. |
| Expected Result | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-008 — SĐT hợp lệ 10 chữ số qua API

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT hợp lệ 10 chữ số qua API. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C1, C22, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Ghi snapshot U.<br>2. PUT http://localhost:3000/api/users/me với Content-Type: application/json, Authorization: Bearer token U hợp lệ (lấy lúc chạy), payload ở Test data.<br>3. Ghi response; GET /api/users/me bằng token U hợp lệ. |
| Expected Result | Chấp nhận cập nhật U; đọc lại sau refresh/GET cho thấy name, phone, shipping_address đúng payload, email và role giữ nguyên. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-009 — SĐT dưới 10 chữ số qua API

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT dưới 10 chữ số qua API. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C2, C23, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"091234567","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Ghi snapshot U.<br>2. PUT http://localhost:3000/api/users/me với Content-Type: application/json, Authorization: Bearer token U hợp lệ (lấy lúc chạy), payload ở Test data.<br>3. Ghi response; GET /api/users/me bằng token U hợp lệ. |
| Expected Result | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-010 — SĐT trên 11 chữ số qua API

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT trên 11 chữ số qua API. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C3, C23, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"091234567890","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Ghi snapshot U.<br>2. PUT http://localhost:3000/api/users/me với Content-Type: application/json, Authorization: Bearer token U hợp lệ (lấy lúc chạy), payload ở Test data.<br>3. Ghi response; GET /api/users/me bằng token U hợp lệ. |
| Expected Result | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-011 — SĐT không bắt đầu bằng 0 qua API

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT không bắt đầu bằng 0 qua API. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C4, C23, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"1912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Ghi snapshot U.<br>2. PUT http://localhost:3000/api/users/me với Content-Type: application/json, Authorization: Bearer token U hợp lệ (lấy lúc chạy), payload ở Test data.<br>3. Ghi response; GET /api/users/me bằng token U hợp lệ. |
| Expected Result | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-012 — SĐT chứa chữ cái qua API

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT chứa chữ cái qua API. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C5, C23, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"091234567a","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Ghi snapshot U.<br>2. PUT http://localhost:3000/api/users/me với Content-Type: application/json, Authorization: Bearer token U hợp lệ (lấy lúc chạy), payload ở Test data.<br>3. Ghi response; GET /api/users/me bằng token U hợp lệ. |
| Expected Result | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-013 — SĐT chứa dấu gạch ngang qua API

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT chứa dấu gạch ngang qua API. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C6, C23, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"09123-5678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Ghi snapshot U.<br>2. PUT http://localhost:3000/api/users/me với Content-Type: application/json, Authorization: Bearer token U hợp lệ (lấy lúc chạy), payload ở Test data.<br>3. Ghi response; GET /api/users/me bằng token U hợp lệ. |
| Expected Result | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-014 — SĐT chứa khoảng trắng bên trong qua API

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; SĐT chứa khoảng trắng bên trong qua API. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C7, C23, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"09123 5678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Ghi snapshot U.<br>2. PUT http://localhost:3000/api/users/me với Content-Type: application/json, Authorization: Bearer token U hợp lệ (lấy lúc chạy), payload ở Test data.<br>3. Ghi response; GET /api/users/me bằng token U hợp lệ. |
| Expected Result | Không lưu SĐT không hợp lệ; phone của U sau đọc lại giữ snapshot trước test. Không ép nguyên văn message hoặc mã HTTP. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-015 — Cập nhật riêng họ tên có dấu

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; Cập nhật riêng họ tên có dấu. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C8, C22, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Thị Bình","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`. Riêng case này phone gửi là 0987654321 theo snapshot, không thay phone.  |
| Steps | 1. Đăng nhập U, mở /profile; ghi snapshot.<br>2. Chỉ đổi tên thành Nguyễn Thị Bình, giữ phone/address snapshot.<br>3. Nhấn Cập nhật; ghi phản hồi và request nếu có.<br>4. Refresh trang và GET /api/users/me bằng token U; đối chiếu snapshot. |
| Expected Result | Họ tên U được lưu đúng Nguyễn Thị Bình; phone và địa chỉ giữ nguyên snapshot. Refresh vẫn giữ tên mới. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-016 — Cập nhật riêng địa chỉ mặc định

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; Cập nhật riêng địa chỉ mặc định. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C9, C22, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"45 Nguyễn Huệ, Quận 1, TP.HCM"}`. Riêng case này phone gửi là 0987654321 theo snapshot, không thay phone.  |
| Steps | 1. Đăng nhập U, mở /profile; ghi snapshot.<br>2. Chỉ đổi địa chỉ thành 45 Nguyễn Huệ, Quận 1, TP.HCM, giữ name/phone snapshot.<br>3. Nhấn Cập nhật; ghi phản hồi và request nếu có.<br>4. Refresh trang và GET /api/users/me bằng token U; đối chiếu snapshot. |
| Expected Result | Địa chỉ U được lưu đúng 45 Nguyễn Huệ, Quận 1, TP.HCM; name và phone giữ nguyên snapshot. Refresh vẫn giữ địa chỉ mới. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-017 — Email không sửa được qua giao diện

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; Email không sửa được qua giao diện. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C10, C27, C11, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.  Email thử nhập: a04.changed@example.com; phone giữ snapshot 0987654321. |
| Steps | 1. Đăng nhập U, mở /profile; ghi snapshot.<br>2. Thử focus/gõ/paste a04.changed@example.com vào email bằng thao tác thông thường, không sửa DOM. Sau đó đổi tên thành Nguyễn Thị Bình để thử lưu.<br>3. Nhấn Cập nhật; ghi phản hồi và request nếu có.<br>4. Refresh trang và GET /api/users/me bằng token U; đối chiếu snapshot. |
| Expected Result | Không thể sửa email bằng thao tác UI thông thường (chỉ đọc/disabled); sau cập nhật trường cho phép và refresh, email U vẫn bằng snapshot. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-018 — Từ chối cập nhật khi thiếu JWT

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04, SEC-02; Từ chối cập nhật khi thiếu JWT. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C12, C24, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Ghi snapshot U.<br>2. PUT http://localhost:3000/api/users/me với Content-Type: application/json, không gửi Authorization, payload ở Test data.<br>3. Ghi response; GET /api/users/me bằng token U hợp lệ. |
| Expected Result | Không cập nhật hồ sơ U hoặc W; trả phản hồi từ chối xác thực. Kiểm lại bằng token hợp lệ, dữ liệu giữ nguyên; không ép mã HTTP cụ thể. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-019 — Từ chối cập nhật khi JWT không hợp lệ

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04, SEC-02; Từ chối cập nhật khi JWT không hợp lệ. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C13, C24, C14, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM"}`.   |
| Steps | 1. Ghi snapshot U.<br>2. PUT http://localhost:3000/api/users/me với Content-Type: application/json, Authorization: Bearer invalid.a04.token, payload ở Test data.<br>3. Ghi response; GET /api/users/me bằng token U hợp lệ. |
| Expected Result | Không cập nhật U hoặc W; từ chối xác thực với token sai. Đọc lại bằng token hợp lệ cho thấy snapshot không đổi. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-020 — Không cho tự đổi role qua payload

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04, SEC-06; Không cho tự đổi role qua payload. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C18, C25, C11, C14; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM","role":"admin"}`.   |
| Steps | 1. Ghi snapshot U.<br>2. PUT http://localhost:3000/api/users/me với Content-Type: application/json, Authorization: Bearer token U hợp lệ (lấy lúc chạy), payload ở Test data.<br>3. Ghi response; GET /api/users/me bằng token U hợp lệ.<br>4. Đăng nhập lại U và kiểm role lưu thực tế. |
| Expected Result | Role lưu của U vẫn là user sau request và đăng nhập lại. Cho phép reject toàn request hoặc bỏ qua role và cập nhật trường được phép; không chấp nhận role=admin. Không dùng role trong JWT cũ làm bằng chứng duy nhất. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U bằng fixture kiểm soát; nếu bị nâng role, reset role=user trước test khác; không dùng tài khoản bị đổi quyền cho test sau. |

### TC-A04-021 — Không cập nhật user khác qua field id

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; Không cập nhật user khác qua field id. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C15, C26, C11, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. W khác U, đã có snapshot W và token W. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM","id":"W.id (thay bằng ID thực)"}`.   |
| Steps | 1. Ghi snapshot U và W.<br>2. PUT http://localhost:3000/api/users/me với Content-Type: application/json, Authorization: Bearer token U hợp lệ (lấy lúc chạy), payload ở Test data; thay W.id bằng ID thực dạng số.<br>3. Ghi response; GET /api/users/me bằng token U hợp lệ và token W riêng để đọc W. |
| Expected Result | Hồ sơ W giữ nguyên snapshot khi dùng token U. Có thể từ chối request hoặc bỏ qua target rồi cập nhật U; không được cập nhật W. Đọc lại W bằng token W. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U và W bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |

### TC-A04-022 — Không cập nhật user khác qua field user_id

| Trường | Nội dung |
|:---|:---|
| Requirement / Purpose | FR-04; Không cập nhật user khác qua field user_id. Nguồn README là oracle. |
| Technique | Domain |
| Domain / Boundary | C16, C26, C11, C17; không có điểm biên trong bước Domain; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend/Web sẵn sàng; U role=user, name=Nguyễn Văn An, phone=0987654321, address=123 Lê Lợi, Quận 1, TP.HCM; email giữ nguyên. W khác U, đã có snapshot W và token W. Chụp snapshot trước test và lấy token U hợp lệ; case thiếu/sai token vẫn giữ token hợp lệ riêng để đọc kiểm chứng sau. Chưa có token/fixture thì Blocked. |
| Test data | `{"name":"Nguyễn Văn An","phone":"0912345678","shipping_address":"123 Lê Lợi, Quận 1, TP.HCM","user_id":"W.id (thay bằng ID thực)"}`.   |
| Steps | 1. Ghi snapshot U và W.<br>2. PUT http://localhost:3000/api/users/me với Content-Type: application/json, Authorization: Bearer token U hợp lệ (lấy lúc chạy), payload ở Test data; thay W.id bằng ID thực dạng số.<br>3. Ghi response; GET /api/users/me bằng token U hợp lệ và token W riêng để đọc W. |
| Expected Result | Hồ sơ W giữ nguyên snapshot khi dùng token U. Có thể từ chối request hoặc bỏ qua target rồi cập nhật U; không được cập nhật W. Đọc lại W bằng token W. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Execution context | — |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U và W bằng fixture kiểm soát; không để kết quả case này ảnh hưởng case tiếp theo. |
