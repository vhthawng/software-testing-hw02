# Test Cases: A04 — Hồ sơ cá nhân Web

- **Trạng thái thiết kế:** Draft; 13 test đã thực thi (7 Pass, 6 Fail); sinh viên đã cung cấp actual/evidence Web và API, review tổng thể còn pending.
- **Nguồn yêu cầu:** [FR-04, SEC-02, SEC-06](../eshop-sut/README.md).
- **API contract:** [Users 2.1–2.2](../eshop-sut/api_specification.md).
- **Phân tích:** [analysis/A04_Profile.md](../analysis/A04_Profile.md).
- **Tạo/cập nhật:** 2026-10-18 (Asia/Bangkok).

## Chuẩn bị và quy ước

Backend chạy tại localhost:3000; Web chạy tại localhost:5173/profile. Dùng tài khoản U riêng có role=user, email và dữ liệu nền do người chạy chuẩn bị; test ownership cần thêm W khác U. Snapshot name, phone, shipping_address, email và role trước mỗi test. Token thật chỉ dùng lúc chạy, không ghi vào tài liệu. Nếu không tạo được fixture hoặc môi trường, ghi Blocked và lý do; không đổi expected để làm test chạy được.

Các test Web dùng thao tác trên giao diện rồi refresh hoặc đọc lại để kiểm tra persistence. Các test API chỉ là kiểm tra SEC-02/SEC-06 hoặc endpoint /me; không phải bản sao của toàn bộ test Web. Message, HTTP status và cách reject toàn body/partial update chỉ là Actual Result trừ khi spec quy định.

## 1. Tổng hợp

Tổng cộng 13 test duy nhất: 8 chỉ Domain, 1 kỹ thuật chức năng, 4 thuộc cả Domain và BVA, 0 chỉ BVA. Đã thực thi 13 test: 7 Pass, 6 Fail.

| TC ID | Title | Requirement | Technique | Dữ liệu chính | Expected | Status | Bug |
|:---|:---|:---|:---|:---|:---|:---|:---|
| TC-A04-001 | Phone hợp lệ tại biên dưới trên Web | FR-04 | Cả hai | 0912345678, L=10 | Lưu và đọc lại đúng phone. | Fail | BUG-A04-002 |
| TC-A04-002 | Phone dưới biên dưới trên Web | FR-04 | Cả hai | 091234567, L=9 | Không commit phone. | Fail | — |
| TC-A04-003 | Phone hợp lệ tại biên trên trên Web | FR-04 | Cả hai | 09123456789, L=11 | Lưu và đọc lại đúng phone. | Fail | BUG-A04-002 |
| TC-A04-004 | Phone trên biên trên trên Web | FR-04 | Cả hai | 091234567890, L=12 | Không commit phone. | Fail | — |
| TC-A04-005 | Phone không bắt đầu bằng 0 | FR-04 | Domain | 1912345678 | Không commit phone. | Fail | BUG-A04-003 |
| TC-A04-006 | Phone chứa non-digit | FR-04 | Domain | 09123456a1 | Không commit phone. | Pass | — |
| TC-A04-007 | Cập nhật họ tên | FR-04 | Domain | Nguyễn Thị Bình | Lưu đúng name, trường khác giữ nguyên. | Pass | — |
| TC-A04-008 | Cập nhật địa chỉ giao hàng | FR-04 | Domain | 45 Nguyễn Huệ, Quận 1, TP.HCM | Lưu đúng address, name/phone giữ nguyên. | Pass | — |
| TC-A04-009 | Email không sửa được qua UI | FR-04 | Functional | Thử nhập email mới | Email lưu giữ nguyên. | Pass | — |

| TC-A04-010 | API thiếu JWT | SEC-02 | Domain | PUT /api/users/me không Authorization | Không cập nhật. | Pass | — |
| TC-A04-011 | API JWT không hợp lệ | SEC-02 | Domain | Bearer invalid.a04.token | Không cập nhật. | Pass | — |
| TC-A04-012 | API không cho client đổi role | SEC-06 | Domain | Thêm role=admin | Role lưu không thành admin. | Fail | BUG-A04-001 |
| TC-A04-013 | API /me chỉ cập nhật U | FR-04, API Users | Domain | Token U, body đổi name U, W khác U | U đổi; W giữ nguyên. | Pass | — |

## 2. Chi tiết

### TC-A04-001 — Phone hợp lệ tại biên dưới trên Web

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-001; Phone hợp lệ tại biên dưới trên Web |
| Requirement / Purpose | FR-04 yêu cầu phone bắt đầu bằng 0 và dài 10–11 chữ số; kiểm tra L=10. |
| Technique | Domain Testing + BVA |
| Domain / Boundary | C1; B1 on-point (L=10); [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | U đã đăng nhập; mở /profile; snapshot phone hiện tại. |
| Test data | name/address nominal; phone = 0912345678 (10 ASCII digits, bắt đầu 0). |
| Steps | 1. Nhập phone 0912345678, giữ các trường khác nominal.<br>2. Nhấn nút cập nhật.<br>3. Refresh trang hoặc đọc lại hồ sơ bằng API GET với token U.<br>4. Ghi response, actual và evidence. |
| Expected Result | Phone được chấp nhận và giá trị lưu đọc lại là 0912345678; các trường không đổi giữ nguyên. |
| Actual Result | UI hiển thị dialog "Số điện thoại không hợp lệ. Vui lòng nhập đúng 9-10 chữ số."; không có request cập nhật xuống API. Giá trị 10 chữ số hợp lệ theo FR-04 nhưng bị từ chối. |
| Status | Fail |
| Evidence | [TC-A04-001.png](evidence/A04/TC-A04-001.png) |
| Bug ID / Issue URL | [BUG-A04-002](../bug-reports/bugs.md) |
| Cleanup | Khôi phục snapshot U. |

### TC-A04-002 — Phone dưới biên dưới trên Web

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-002; Phone dưới biên dưới trên Web |
| Requirement / Purpose | FR-04; kiểm tra giá trị L=9 ở ngoài biên dưới. |
| Technique | Domain Testing + BVA |
| Domain / Boundary | C2; B1 off-point (L=9); [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | U đã đăng nhập; snapshot phone hợp lệ trước test. |
| Test data | phone = 091234567 (9 digits); name/address nominal. |
| Steps | 1. Nhập phone 091234567.<br>2. Nhấn cập nhật.<br>3. Refresh hoặc GET hồ sơ U.<br>4. Ghi phản hồi và dữ liệu lưu. |
| Expected Result | Giá trị 9 digits không được commit; phone lưu vẫn là giá trị snapshot trước test. Message/status cụ thể không được spec quy định. |
| Actual Result | UI hiển thị dialog "Số điện thoại không hợp lệ. Vui lòng nhập đúng 9-10 chữ số."; không có request cập nhật xuống API. Status Fail theo báo cáo thực thi; hành vi từ chối phù hợp expected FR-04 cho L=9, cần rà soát lại cách gán trạng thái. |
| Status | Fail |
| Evidence | [TC-A04-002.png](evidence/A04/TC-A04-002.png) |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U. |

### TC-A04-003 — Phone hợp lệ tại biên trên trên Web

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-003; Phone hợp lệ tại biên trên trên Web |
| Requirement / Purpose | FR-04; kiểm tra L=11. |
| Technique | Domain Testing + BVA |
| Domain / Boundary | C1; B2 on-point (L=11); [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | U đã đăng nhập; snapshot hồ sơ trước test. |
| Test data | phone = 09123456789 (11 digits); name/address nominal. |
| Steps | 1. Nhập phone 09123456789.<br>2. Nhấn cập nhật.<br>3. Refresh hoặc GET hồ sơ U.<br>4. Ghi actual và evidence. |
| Expected Result | Phone được chấp nhận và đọc lại đúng 09123456789. |
| Actual Result | UI hiển thị dialog "Số điện thoại không hợp lệ. Vui lòng nhập đúng 9-10 chữ số."; không có request cập nhật xuống API. Giá trị 11 chữ số hợp lệ theo FR-04 nhưng bị từ chối. |
| Status | Fail |
| Evidence | [TC-A04-003.png](evidence/A04/TC-A04-003.png) |
| Bug ID / Issue URL | [BUG-A04-002](../bug-reports/bugs.md) |
| Cleanup | Khôi phục snapshot U. |

### TC-A04-004 — Phone trên biên trên trên Web

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-004; Phone trên biên trên trên Web |
| Requirement / Purpose | FR-04; kiểm tra giá trị L=12 ở ngoài biên trên. |
| Technique | Domain Testing + BVA |
| Domain / Boundary | C3; B2 off-point (L=12); [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | U đã đăng nhập; snapshot phone hợp lệ trước test. |
| Test data | phone = 091234567890 (12 digits); name/address nominal. |
| Steps | 1. Nhập phone 091234567890.<br>2. Nhấn cập nhật.<br>3. Refresh hoặc GET hồ sơ U.<br>4. Ghi phản hồi và dữ liệu lưu. |
| Expected Result | Giá trị 12 digits không được commit; phone lưu vẫn là snapshot trước test. |
| Actual Result | UI hiển thị dialog "Số điện thoại không hợp lệ. Vui lòng nhập đúng 9-10 chữ số."; không có request cập nhật xuống API. Status Fail theo báo cáo thực thi; hành vi từ chối phù hợp expected FR-04 cho L=12, cần rà soát lại cách gán trạng thái. |
| Status | Fail |
| Evidence | — (chưa có file ảnh TC-A04-004.png) |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U. |

### TC-A04-005 — Phone không bắt đầu bằng 0

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-005; Phone không bắt đầu bằng 0 |
| Requirement / Purpose | FR-04; kiểm tra tiền tố bắt buộc. |
| Technique | Domain Testing |
| Domain / Boundary | C4; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | U đã đăng nhập; phone snapshot hợp lệ; name/address nominal. |
| Test data | phone = 1912345678 (10 digits nhưng bắt đầu 1). |
| Steps | 1. Nhập phone 1912345678.<br>2. Nhấn cập nhật.<br>3. Refresh hoặc GET hồ sơ U. |
| Expected Result | Phone không bắt đầu 0 không được commit; giá trị lưu giữ snapshot. |
| Actual Result | UI hiển thị dialog "Cập nhật thành công"; có request cập nhật gửi về backend nhưng không có POST request riêng theo quan sát; refresh vẫn giữ 1912345678. Phone không bắt đầu bằng 0 nhưng vẫn được commit. |
| Status | Fail |
| Evidence | [TC-A04-005.png](evidence/A04/TC-A04-005.png) |
| Bug ID / Issue URL | [BUG-A04-003](../bug-reports/bugs.md) |
| Cleanup | Khôi phục snapshot U. |

### TC-A04-006 — Phone chứa non-digit

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-006; Phone chứa non-digit |
| Requirement / Purpose | FR-04; phone phải là chữ số. |
| Technique | Domain Testing |
| Domain / Boundary | C5; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | U đã đăng nhập; phone snapshot hợp lệ; name/address nominal. |
| Test data | phone = 09123456a1 (10 ký tự, có chữ a). |
| Steps | 1. Nhập phone 09123456a1.<br>2. Nhấn cập nhật.<br>3. Refresh hoặc GET hồ sơ U. |
| Expected Result | Phone chứa non-digit không được commit; giá trị lưu giữ snapshot. |
| Actual Result | UI hiển thị dialog "Số điện thoại không hợp lệ. Vui lòng nhập đúng 9-10 chữ số."; phone chứa non-digit không được commit và giá trị lưu giữ snapshot. |
| Status | Pass |
| Evidence | [TC-A04-006.png](evidence/A04/TC-A04-006.png) |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U. |

### TC-A04-007 — Cập nhật họ tên

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-007; Cập nhật họ tên |
| Requirement / Purpose | FR-04 cho phép người dùng cập nhật Họ Tên. |
| Technique | Domain Testing |
| Domain / Boundary | C6; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | U đã đăng nhập; name snapshot được ghi lại; phone/address nominal. |
| Test data | name = Nguyễn Thị Bình; phone/address giữ nominal. |
| Steps | 1. Nhập name Nguyễn Thị Bình.<br>2. Nhấn cập nhật.<br>3. Refresh hoặc GET hồ sơ U.<br>4. Đối chiếu các trường. |
| Expected Result | Name lưu là Nguyễn Thị Bình; phone và address giữ nguyên snapshot. |
| Actual Result | UI hiển thị dialog "Cập nhật thành công"; name lưu là Nguyễn Thị Bình; phone và address giữ nguyên snapshot. |
| Status | Pass |
| Evidence | [TC-A04-007.png](evidence/A04/TC-A04-007.png) |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U. |

### TC-A04-008 — Cập nhật địa chỉ giao hàng

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-008; Cập nhật địa chỉ giao hàng |
| Requirement / Purpose | FR-04 cho phép người dùng cập nhật địa chỉ giao hàng mặc định. |
| Technique | Domain Testing |
| Domain / Boundary | C7; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | U đã đăng nhập; address snapshot được ghi lại; name/phone nominal. |
| Test data | shipping_address = 45 Nguyễn Huệ, Quận 1, TP.HCM; name/phone giữ nominal. |
| Steps | 1. Nhập địa chỉ mới.<br>2. Nhấn cập nhật.<br>3. Refresh hoặc GET hồ sơ U.<br>4. Đối chiếu các trường. |
| Expected Result | Address lưu đúng giá trị gửi; name và phone giữ nguyên snapshot. |
| Actual Result | UI hiển thị dialog "Cập nhật thành công"; address lưu đúng giá trị gửi; name và phone giữ nguyên snapshot. |
| Status | Pass |
| Evidence | [TC-A04-008.png](evidence/A04/TC-A04-008.png) |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U. |

### TC-A04-009 — Email không sửa được qua UI

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-009; Email không sửa được qua UI |
| Requirement / Purpose | FR-04 cấm thay đổi email qua giao diện. |
| Technique | Functional test theo oracle FR-04 |
| Domain / Boundary | Postcondition O2; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | U đã đăng nhập tại /profile; ghi email snapshot. |
| Test data | Thử nhập email khác vào trường email bằng thao tác UI thông thường; không sửa DOM/source. |
| Steps | 1. Focus trường email và thử gõ/paste email mới.<br>2. Thay đổi một trường được phép để có thao tác cập nhật hợp lệ.<br>3. Nhấn cập nhật và refresh.<br>4. Đọc lại email. |
| Expected Result | Email không thể thay đổi bằng thao tác UI thông thường; email lưu sau refresh vẫn bằng snapshot. |
| Actual Result | Email field bị disabled trên UI, không cho thay đổi hoặc focus; email sau refresh vẫn bằng snapshot. |
| Status | Pass |
| Evidence | [TC-A04-009.png](evidence/A04/TC-A04-009.png) |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U. |

### TC-A04-010 — API thiếu JWT

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-010; API thiếu JWT |
| Requirement / Purpose | SEC-02 yêu cầu API có tính bảo mật phải cần JWT hợp lệ. |
| Technique | Domain Testing |
| Domain / Boundary | C9; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend sẵn sàng; U có snapshot và token hợp lệ riêng để đọc lại. |
| Test data | PUT http://localhost:3000/api/users/me; body gồm name/phone/shipping_address hợp lệ; không gửi Authorization. |
| Steps | 1. Gửi request thiếu header Authorization.<br>2. Ghi response/status thực tế.<br>3. GET hồ sơ bằng token U hợp lệ để kiểm tra dữ liệu. |
| Expected Result | Request thiếu JWT không được thực hiện và dữ liệu U không bị đổi. Status/message chỉ ghi nhận, không ép theo spec chưa nêu. |
| Actual Result | 401 Unauthorized (`{"error":"Unauthorized"}`); GET bằng token U hợp lệ xác nhận snapshot U không đổi. |
| Status | Pass |
| Evidence | [a04_api_execution_summary.md](evidence/A04/a04_api_execution_summary.md); [postman-cli.log](evidence/A04/postman-cli.log) |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U nếu cần. |

### TC-A04-011 — API JWT không hợp lệ

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-011; API JWT không hợp lệ |
| Requirement / Purpose | SEC-02; kiểm tra token không hợp lệ. |
| Technique | Domain Testing |
| Domain / Boundary | C10; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend sẵn sàng; có token U hợp lệ riêng để đọc lại. |
| Test data | PUT http://localhost:3000/api/users/me; body hợp lệ; Authorization = Bearer invalid.a04.token. |
| Steps | 1. Gửi request với token sai.<br>2. Ghi response/status.<br>3. GET hồ sơ bằng token U hợp lệ. |
| Expected Result | Request với JWT không hợp lệ không được thực hiện; snapshot U giữ nguyên. |
| Actual Result | 403 Forbidden (`{"error":"Forbidden"}`); GET bằng token U hợp lệ xác nhận snapshot U không đổi. |
| Status | Pass |
| Evidence | [a04_api_execution_summary.md](evidence/A04/a04_api_execution_summary.md); [postman-cli.log](evidence/A04/postman-cli.log) |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U nếu cần. |

### TC-A04-012 — API không cho client đổi role

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-012; API không cho client đổi role |
| Requirement / Purpose | SEC-06 cấm API cập nhật hồ sơ cho phép client đổi trường role. |
| Technique | Domain Testing |
| Domain / Boundary | C8, C12; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Backend sẵn sàng; U role=user; token U hợp lệ; ghi snapshot role và dữ liệu. |
| Test data | PUT /api/users/me với body API chuẩn thêm role=admin; các trường còn lại nominal. |
| Steps | 1. Gửi request với token U hợp lệ và role=admin.<br>2. Ghi response/status.<br>3. GET hồ sơ U và đăng nhập lại nếu cần để đọc role lưu. |
| Expected Result | Role lưu không trở thành admin. Reject toàn request hoặc bỏ qua role đều chấp nhận được nếu invariant này giữ; status/message không ép cụ thể. |
| Actual Result | PUT trả 200 với `{"message":"Profile updated"}`; GET sau đó cho thấy `role: "admin"`, vi phạm invariant SEC-06. Cleanup đã khôi phục role về `user`. |
| Status | Fail |
| Evidence | [a04_api_execution_summary.md](evidence/A04/a04_api_execution_summary.md); [postman-cli.log](evidence/A04/postman-cli.log) |
| Bug ID / Issue URL | [BUG-A04-001](../bug-reports/bugs.md) |
| Cleanup | Nếu role bị đổi, phục hồi role=user trước test khác và ghi bug/evidence. |

### TC-A04-013 — API /me chỉ cập nhật tài khoản U

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-A04-013; API /me chỉ cập nhật tài khoản U |
| Requirement / Purpose | FR-04 yêu cầu người dùng chỉ cập nhật hồ sơ của chính mình; API contract dùng /me, không có target field. |
| Technique | Domain Testing |
| Domain / Boundary | C8, C11; postcondition O6; [analysis](../analysis/A04_Profile.md) |
| Preconditions / Setup | Có U và W khác nhau; có token U/W; ghi snapshot đầy đủ của cả hai. |
| Test data | PUT /api/users/me bằng token U; body API chuẩn đổi name U thành Nguyễn Thị Bình, phone/address nominal, không gửi role. |
| Steps | 1. Gửi request bằng token U.<br>2. GET /api/users/me bằng token U và token W riêng.<br>3. So sánh với snapshot trước test. |
| Expected Result | U được cập nhật theo body; W giữ nguyên toàn bộ snapshot. Không suy ra test target id/user_id vì các field đó không nằm trong API contract. |
| Actual Result | PUT trả 200; GET U cho thấy name/phone/shipping_address mới, email/role giữ nguyên; GET W giữ nguyên snapshot. |
| Status | Pass |
| Evidence | [a04_api_execution_summary.md](evidence/A04/a04_api_execution_summary.md); [postman-cli.log](evidence/A04/postman-cli.log) |
| Bug ID / Issue URL | — |
| Cleanup | Khôi phục snapshot U và W. |
