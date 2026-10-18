# Analysis: A04 — Quản lý hồ sơ cá nhân

| | |
|:---|:---|
| **Tính năng / nền tảng** | Hồ sơ cá nhân trên Web; API chỉ dùng cho các kiểm tra bảo mật được nêu rõ trong SEC-02/SEC-06 và kiểm tra endpoint /me. |
| **Người thiết kế / ngày** | Codex hỗ trợ MSSV 23120355; họ tên sinh viên chưa cung cấp; tạo/cập nhật 2026-10-18 (Asia/Bangkok). |
| **Test case chi tiết** | [test-cases/A04_Profile.md](../test-cases/A04_Profile.md) |
| **Trạng thái** | Draft — 13 test đã thực thi; sinh viên đã cung cấp actual/evidence, review tổng thể còn pending. |

## 1. Phạm vi và nguồn

**Tài liệu đã đọc**

| Nguồn | Phiên bản / trạng thái | Phần đã đọc | Phần chưa đọc / thiếu |
|:---|:---|:---|:---|
| [Đặc tả EShop](../eshop-sut/README.md) | v2.0, cập nhật 2026-05-14; nguồn yêu cầu | FR-04, SEC-02, SEC-06 và phần tổng quan | Các FR khác không dùng làm oracle cho A04 |
| [API specification](../eshop-sut/api_specification.md) | Không ghi phiên bản | Users 2.1–2.2 và quy định Bearer token | Không có contract cho id hoặc user_id trong body cập nhật |
| [Đề HW02](../2026.HW02.Domain%20Testing_En.md) | HW02-AI | Domain Testing, BVA, AI audit, Git và hồ sơ nộp | Bài giảng môn học chưa có trong workspace |
| Bản chạy thật | Web execution 2026-10-18; API execution bằng Postman CLI | TC-A04-001–009 có ảnh; TC-A04-010–013 có CLI log/summary | Không có ảnh TC-A04-004; runtime request details của Web chỉ ghi theo evidence được cung cấp |
| Code/UI SUT | Đọc tĩnh để biết cách thực thi | Route và cách gọi hiện tại | Không dùng implementation làm oracle; chưa xác nhận runtime |

- **Trong phạm vi:** cập nhật Họ Tên, Số điện thoại và Địa chỉ giao hàng mặc định; miền hợp lệ của Số điện thoại; email không được thay đổi qua giao diện; API cập nhật phải yêu cầu JWT hợp lệ; client không được đổi role; endpoint /me chỉ tác động tới tài khoản đang xác thực.
- **Ngoài phạm vi:** các trường hợp name/address rỗng hoặc null, trim/chuẩn hóa, Unicode digits, mã HTTP/message cụ thể và tấn công bằng target tùy ý vì spec/API contract chưa mô tả; các FR-21–24 khác, đổi mật khẩu, lịch sử đơn hàng và Mobile.
- **Kỹ thuật áp dụng:** Domain Testing (Equivalence Partitioning và Single Fault) cho các miền phone, xác thực và role; Boundary Value Analysis cho độ dài phone 10–11. API không được nhân đôi toàn bộ bộ test Web; chỉ có test API khi yêu cầu bảo mật hoặc ownership cần nó.

Spec là oracle. Code, API implementation và UI chỉ giúp chọn bước thực thi hoặc nhận diện dấu hiệu cần xác nhận.

## 2. Biến và quy tắc

### 2.1 Input và context

| Mã | Biến / context | Loại | Kiểu, đơn vị | Ràng buộc | Nguồn | Explicit / Suy ra |
|:---|:---|:---|:---|:---|:---|:---|
| V1 | name | Nhập trực tiếp | Chuỗi | Được phép cập nhật; FR-04 không nêu min/max hay định dạng | FR-04 | Explicit |
| V2 | phone | Nhập trực tiếp | Chuỗi chữ số | Bắt đầu bằng 0, dài 10–11 chữ số | FR-04 | Explicit |
| V3 | shipping_address | Nhập trực tiếp | Chuỗi | Được phép cập nhật; FR-04 không nêu min/max hay định dạng | FR-04 | Explicit |
| V4 | Tài khoản/JWT của request | Context | Tài khoản đã đăng nhập và Bearer token | API bảo mật yêu cầu JWT hợp lệ; /me lấy tài khoản từ token | FR-04, SEC-02, API Users | Explicit |
| V5 | role trong payload client | Input kiểm tra bảo mật | Chuỗi role, ví dụ admin | Client không được thay đổi role của tài khoản | SEC-06 | Explicit |

**Nominal (dữ liệu nền hợp lệ):** dùng tài khoản U riêng có role user, name Nguyễn Văn An, phone 0987654321, shipping_address 123 Lê Lợi, Quận 1, TP.HCM; email và role được ghi snapshot trước mỗi test. Token thật chỉ dùng lúc chạy và không ghi vào tài liệu. Test ownership chuẩn bị thêm W khác U.

### 2.2 Observable outputs / postconditions

Các mục dưới đây là oracle quan sát, không phải input variable và không lập equivalence class riêng cho chúng.

| Mã | Observable output / postcondition | Căn cứ |
|:---|:---|:---|
| O1 | Khi dữ liệu hợp lệ được chấp nhận, name/phone/address mới được lưu và đọc lại đúng giá trị gửi. | FR-04, ý nghĩa của “cập nhật” |
| O2 | Email không thể thay đổi bằng thao tác giao diện; email lưu vẫn giữ nguyên. | FR-04 |
| O3 | Giá trị phone ngoài miền hợp lệ không được commit vào hồ sơ; message/status cụ thể chưa được đặc tả. | FR-04 |
| O4 | Request cập nhật API không có JWT hợp lệ không được thực hiện. | SEC-02 |
| O5 | Gửi role=admin từ client không làm role lưu của U thành admin. | SEC-06 |
| O6 | PUT /api/users/me bằng token U chỉ làm thay đổi hồ sơ U; hồ sơ W không bị thay đổi. | FR-04 và endpoint /me trong API contract |

## 3. Spec gap, mâu thuẫn và quyết định phương pháp

| Mã | Nội dung | Nguồn | Loại | Trạng thái | TC bị ảnh hưởng |
|:---|:---|:---|:---|:---|:---|
| G1 | Không nêu name/address có bắt buộc hay không; không nêu cách xử lý empty, null, missing hoặc whitespace-only. | FR-04 | Spec gap | Chưa chốt; không tạo class/expected | Không có |
| G2 | Không nêu trim, chuẩn hóa +84, Unicode digits hay cách đếm ký tự đặc biệt. | FR-04 | Spec gap | Chưa chốt; bộ chính dùng ASCII digits để kiểm tra rule đã viết | TC-A04-001–006 |
| G3 | Không nêu HTTP status, message, vị trí lỗi hoặc reject toàn bộ/partial update khi có lỗi. | FR-04, SEC-02, SEC-06 | Spec gap | Chỉ kiểm invariant; không ép giá trị cụ thể | TC-A04-002–006, TC-A04-010–012 |
| G4 | FR-04 yêu cầu chỉ cập nhật hồ sơ của chính mình nhưng API contract chỉ công bố /me, không có target id/user_id. | FR-04, API Users | Quyết định phương pháp | Kiểm tra U/W qua /me; không tạo payload field không có trong contract | TC-A04-013 |
| G5 | Bài giảng Domain/BVA chưa được cung cấp trong workspace. | HW02 | Spec gap | Dùng quy ước 2-point và ghi rõ để sinh viên đối chiếu | B1, B2 |

Không gọi G1–G3 là assumption và không biến chúng thành lớp hợp lệ/không hợp lệ.

## 4. Domain Testing: phân vùng tương đương

**Cách chia lớp:** mỗi lớp xuất phát từ predicate có trong FR-04/SEC-02/SEC-06. Phone được tách theo độ dài, tiền tố và thuộc tính “toàn bộ là chữ số”; không tách riêng gạch ngang/khoảng trắng vì spec chỉ phân biệt digits và non-digits. Name/address chỉ có lớp nominal vì spec không đưa predicate lỗi. V4 và V5 là context/input bảo mật có quy tắc explicit.

**Quan hệ phụ thuộc và Single Fault:** mọi test phone giữ prefix 0 và thuộc tính digits hợp lệ khi chỉ kiểm tra độ dài; khi kiểm tra prefix hoặc non-digit thì giữ độ dài 10. Test thiếu/sai token giữ body hợp lệ. Test role chỉ thêm field role vào body chuẩn. Test ownership dùng token U và đọc W riêng; không đưa target field không có trong API contract.

| Class ID | Biến / context | Điều kiện | Validity | Đại diện / cách đạt | Nguồn |
|:---|:---|:---|:---|:---|:---|
| C1 | V2 phone | Bắt đầu 0, toàn ASCII digits, dài 10–11 | Hợp lệ | 0912345678 (10) | FR-04 |
| C2 | V2 phone | Bắt đầu 0, toàn digits, dài 1–9 | Không hợp lệ | 091234567 (9) | FR-04 |
| C3 | V2 phone | Bắt đầu 0, toàn digits, dài từ 12 trở lên | Không hợp lệ | 091234567890 (12) | FR-04 |
| C4 | V2 phone | Toàn digits, dài 10–11 nhưng không bắt đầu 0 | Không hợp lệ | 1912345678 | FR-04 |
| C5 | V2 phone | Có ít nhất một ký tự không phải digit | Không hợp lệ | 09123456a1 | FR-04 |
| C6 | V1 name | Giá trị chuỗi nominal được gửi ở trường name | Hợp lệ | Nguyễn Thị Bình | FR-04 |
| C7 | V3 shipping_address | Giá trị chuỗi nominal được gửi ở trường address | Hợp lệ | 45 Nguyễn Huệ, Quận 1, TP.HCM | FR-04 |
| C8 | V4 token | JWT hợp lệ của U | Hợp lệ | Token U lấy lúc chạy | SEC-02 |
| C9 | V4 token | Không gửi JWT | Không hợp lệ | Bỏ Authorization | SEC-02 |
| C10 | V4 token | JWT không hợp lệ | Không hợp lệ | Bearer invalid.a04.token | SEC-02 |
| C11 | V5 role | Không gửi role từ client | Hợp lệ | Body theo API contract | SEC-06 |
| C12 | V5 role | Client gửi role khác với role được cấp | Không hợp lệ | Thêm role: admin | SEC-06 |

Các trường hợp G1–G3 không có class vì spec chưa định nghĩa predicate. Không có output class; O1–O6 chỉ là postcondition để đánh giá expected.

## 5. Boundary Value Analysis

**Cách xác định biên:** với độ dài L của phone, FR-04 quy định 10 ≤ L ≤ 11; hai biên đều đóng. Prefix 0 và toàn digits được giữ cố định để cô lập biến độ dài.

**Độ mịn:** ±1 chữ số.

**2-point hay 3-point:** dùng 2-point (on-point và off-point) vì bài giảng chưa có trong workspace; B1 và B2 cùng tạo ra bốn giá trị 9/10/11/12. Sinh viên cần đối chiếu lại quy ước này với bài giảng.

| Boundary ID | Class ID liên quan | Quy tắc / nguồn | Đóng / mở | Độ mịn | On-point | Off-point | Expected (kèm công thức) | Lý do chọn |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| B1 | C1, C2 | L ≥ 10 theo FR-04 | Đóng | 1 chữ số | 0912345678 (L=10) | 091234567 (L=9) | On: phone thuộc miền hợp lệ; off: phone không được commit. | Kiểm nhầm > thành ≥ |
| B2 | C1, C3 | L ≤ 11 theo FR-04 | Đóng | 1 chữ số | 09123456789 (L=11) | 091234567890 (L=12) | On: phone thuộc miền hợp lệ; off: phone không được commit. | Kiểm nhầm < thành ≤ |

## 6. Test Matrix

Các test âm chỉ thay một điều kiện sai; các trường còn lại giữ nominal. API security là phần bổ sung có căn cứ từ SEC-02/SEC-06, không phải nhân đôi test Web.

| TC ID | Requirement / nguồn | Technique | Dữ liệu đầy đủ | Class IDs | Boundary / điểm | Validity | Expected |
|:---|:---|:---|:---|:---|:---|:---|:---|
| TC-A04-001 | FR-04 | Cả hai | Web; phone 0912345678; name/address nominal | C1 | B1 on (L=10) | valid | Cập nhật được và đọc lại đúng phone; các trường không đổi giữ nguyên. |
| TC-A04-002 | FR-04 | Cả hai | Web; phone 091234567 (9 digits); name/address nominal | C2 | B1 off (L=9) | invalid | Phone 9 digits không được commit; message/status không ép cụ thể. |
| TC-A04-003 | FR-04 | Cả hai | Web; phone 09123456789; name/address nominal | C1 | B2 on (L=11) | valid | Cập nhật được và đọc lại đúng phone. |
| TC-A04-004 | FR-04 | Cả hai | Web; phone 091234567890 (12 digits); name/address nominal | C3 | B2 off (L=12) | invalid | Phone 12 digits không được commit; message/status không ép cụ thể. |
| TC-A04-005 | FR-04 | Domain | Web; phone 1912345678; name/address nominal | C4 | — | invalid | Phone không bắt đầu 0 không được commit. |
| TC-A04-006 | FR-04 | Domain | Web; phone 09123456a1; name/address nominal | C5 | — | invalid | Phone chứa non-digit không được commit. |
| TC-A04-007 | FR-04 | Domain | Web; name Nguyễn Thị Bình; phone/address nominal | C6 | — | valid | Name mới được lưu và đọc lại đúng; phone/address giữ nguyên. |
| TC-A04-008 | FR-04 | Domain | Web; address 45 Nguyễn Huệ, Quận 1, TP.HCM; name/phone nominal | C7 | — | valid | Address mới được lưu và đọc lại đúng; name/phone giữ nguyên. |
| TC-A04-009 | FR-04 | Functional | Web; thử nhập email khác vào trường email | — | — | negative action | Email không thể thay đổi qua thao tác UI thông thường và giá trị lưu giữ nguyên. |
| TC-A04-010 | SEC-02 | Domain | API PUT /api/users/me; body hợp lệ; không có Authorization | C9 | — | invalid | API không thực hiện cập nhật khi thiếu JWT hợp lệ; không ép status/message. |
| TC-A04-011 | SEC-02 | Domain | API PUT /api/users/me; body hợp lệ; Authorization Bearer invalid.a04.token | C10 | — | invalid | API không thực hiện cập nhật với JWT không hợp lệ; không ép status/message. |
| TC-A04-012 | SEC-06 | Domain | API PUT /api/users/me; body chuẩn thêm role: admin; token U hợp lệ | C8, C12 | — | invalid | Role lưu của U không trở thành admin; reject toàn body hoặc bỏ qua role đều phù hợp nếu invariant này giữ. |
| TC-A04-013 | FR-04, API Users | Domain | API PUT /api/users/me; token U hợp lệ; đổi name của U; W là user khác | C8, C11 | — | valid | U được cập nhật theo body; đọc W bằng token W cho thấy W không đổi. |

## 7. Truy vết và độ phủ

Đây là độ phủ thiết kế; trạng thái thực thi cập nhật ở phần thống kê sau lần chạy Web và Postman CLI.

| Requirement / Class / Boundary point | TC IDs | Trạng thái | Lý do |
|:---|:---|:---|:---|
| FR-04: cập nhật phone hợp lệ | TC-A04-001, TC-A04-003 | Đã thiết kế | Hai biên đóng 10 và 11 |
| FR-04: phone ngắn hơn miền | TC-A04-002 | Đã thiết kế | B1 off |
| FR-04: phone dài hơn miền | TC-A04-004 | Đã thiết kế | B2 off |
| FR-04: phone bắt đầu bằng 0 | TC-A04-005 | Đã thiết kế | C4 |
| FR-04: phone toàn digits | TC-A04-006 | Đã thiết kế | C5 |
| FR-04: cập nhật name | TC-A04-007 | Đã thiết kế | C6 |
| FR-04: cập nhật address | TC-A04-008 | Đã thiết kế | C7 |
| FR-04: email không đổi qua UI | TC-A04-009 | Đã thiết kế | Postcondition O2 |
| SEC-02: JWT hợp lệ bắt buộc | TC-A04-010, TC-A04-011 | Đã thiết kế | C9, C10 |
| SEC-06: client không đổi role | TC-A04-012 | Đã thiết kế | C12 và postcondition O5 |
| FR-04/API /me: chỉ tài khoản U bị đổi | TC-A04-013 | Đã thiết kế | Postcondition O6 |
| C1 | TC-A04-001, TC-A04-003 | Đã thiết kế | Đại diện hai điểm on |
| C2 | TC-A04-002 | Đã thiết kế | Đại diện B1 off |
| C3 | TC-A04-004 | Đã thiết kế | Đại diện B2 off |
| C4 | TC-A04-005 | Đã thiết kế | Prefix sai, L=10 |
| C5 | TC-A04-006 | Đã thiết kế | Non-digit, L=10 |
| C6 | TC-A04-007 | Đã thiết kế | Name nominal |
| C7 | TC-A04-008 | Đã thiết kế | Address nominal |
| C8 | TC-A04-012, TC-A04-013 | Đã thiết kế | Token U hợp lệ |
| C9 | TC-A04-010 | Đã thiết kế | Thiếu token |
| C10 | TC-A04-011 | Đã thiết kế | Token sai |
| C11 | TC-A04-013 | Đã thiết kế | Body không có role |
| C12 | TC-A04-012 | Đã thiết kế | Role injection |
| G1–G3: empty/null/trim/status/message | — | Chưa phủ | Spec gap, chưa được chốt |
| Target tùy ý id/user_id | — | Không đạt tới được | Không có trong API contract; không tạo test bằng field code-only |

**Thống kê (không đếm đôi):**

| Chỉ số | Số lượng |
|:---|:---|
| Tổng test case duy nhất | 13 |
| Chỉ Domain | 8 |
| Chỉ BVA | 0 |
| Thuộc cả hai | 4 |
| Kỹ thuật bổ sung-only | 1 |
| Hợp lệ / Không hợp lệ hoặc negative action | 5 / 8 |
| Đã thực thi / Pass / Fail | 13 / 7 / 6 |
| Not Run | 0 |

Kiểm tra: N = 8 + 0 + 4 + 1 = 13. Mức giảm chưa định lượng vì miền name/address và trạng thái UI chưa có tập giá trị hữu hạn được đặc tả.

**Thiếu sót đã biết:** chưa có test cho các spec gap G1–G3, GUI requirements khác của FR-21/22, bảo mật hiển thị SEC-04, token hết hạn/thu hồi và các tương tác đồng thời. Các phần này cần kỹ thuật bổ sung hoặc quyết định phạm vi trước khi thiết kế.

## 8. Giới hạn và kỹ thuật bổ sung

EP/BVA kiểm tra miền và ngưỡng, nhưng không chứng minh toàn bộ tương tác, trạng thái, GUI accessibility hoặc bảo mật sâu. Có thể bổ sung state/authorization testing, GUI inspection, security/XSS testing và pairwise sau khi spec hoặc mục tiêu kiểm thử chốt rõ.

## 9. Review và thay đổi

| Phiên bản | Ngày | Thay đổi | Lý do | TC bị ảnh hưởng | Trạng thái review |
|:---|:---|:---|:---|:---|:---|
| v0.1 | 2026-10-18 | Bản phân tích cũ có output V8/V9 trong bảng biến, class id/user_id, và nhân đôi Web/API | Bản nháp ban đầu chưa áp dụng đầy đủ guardrail spec-first | Toàn bộ | Đã nhận diện trong AI self-review; sinh viên chưa review |
| v0.2 | 2026-10-18 | Tách input/context khỏi observable output; loại output class C22–C28; bỏ id/user_id; gom non-digit; giữ API chỉ cho security/ownership; giảm còn 13 TC | Áp dụng domain-testing skill đã chỉnh và dùng spec/API contract làm nguồn | TC-A04-001–013 | AI self-review; sinh viên chưa review

| v0.3 | 2026-10-18 | Chạy TC-A04-010–013 bằng Postman CLI; ghi nhận 3 Pass, 1 Fail và BUG-A04-001; cập nhật traceability thực thi/evidence | Có kết quả API thực tế; SEC-06 không đạt với role injection | TC-A04-010–013 | AI cập nhật; sinh viên chưa review |

| v0.4 | 2026-10-18 | Ghi nhận actual Web TC-A04-001–009, chuyển ảnh vào evidence/A04 và thêm BUG-A04-002/003 | Kết quả thực thi do sinh viên cung cấp; TC-A04-002/004 được báo Fail dù hành vi quan sát phù hợp expected invalid class, cần review phân loại | TC-A04-001–009 | AI cập nhật từ báo cáo sinh viên; review tổng thể pending |
