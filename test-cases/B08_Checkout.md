# Test Cases: B08 — Checkout Web

- **Trạng thái:** Draft, 10 Not Run; chưa có evidence hoặc bug được tái hiện.
- **Nguồn:** [FR-08, SEC-02](../eshop-sut/README.md), [API Cart & Orders](../eshop-sut/api_specification.md).
- **Phân tích:** [B08_Checkout.md](../analysis/B08_Checkout.md).
- **Tạo/cập nhật:** 2026-10-18, Asia/Bangkok. Codex hỗ trợ; sinh viên chưa review.

## Chuẩn bị dùng cho từng test

Base API `http://localhost:3000`, Web `http://localhost:5173`. U là tài khoản thử nghiệm role=user; đăng nhập `POST /api/login` với email/mật khẩu thử nghiệm thực tế để lấy JWT. Không ghi token thật vào tài liệu.

F1: sản phẩm P giá 100000 ₫, q=2. F2: P 100000 × 2 và Q 50000 × 3. Chọn sản phẩm có sẵn đúng giá; nếu cần, dùng Admin tạo fixture và ghi ID thật lúc chạy. Không giả định seed có những giá này. Trước mỗi TC, xác minh catalog bằng `GET /api/products`; nếu không có quyền dựng fixture thì Blocked.

**Web:** dựng giỏ qua giao diện, kiểm tên/giá/số lượng ngay trên giỏ trước khi mở checkout. **API:** dùng U riêng có giỏ API trống ban đầu; thêm `POST /api/cart`, JWT U, JSON `{"id":ID_P_THỰC_TẾ,"name":"TÊN_P_THỰC_TẾ","price":100000,"quantity":2}`; xác minh bằng `GET /api/cart`. ID/tên phải lấy từ sản phẩm thật đã chuẩn bị. Không dùng API cart để khẳng định đã dựng được giỏ Web. Không tự gửi thêm field `items`/`coupon_id` chỉ vì code có field đó.

Mỗi TC cần fixture độc lập: nếu API không có endpoint xóa giỏ được công bố, dùng tài khoản thử nghiệm mới cho TC kế tiếp; không checkout để “dọn giỏ” rồi giả định backend đã xóa. Snapshot `GET /api/orders/my-orders` trước checkout và so sánh ID sau; không coi đơn cũ có cùng total là bằng chứng thành công. Không reset database dùng chung.

Mọi test để trống coupon. Địa chỉ API cố định `123 Le Loi, TP.HCM`; không đánh giá validation địa chỉ ngoài spec. Cleanup dùng tài khoản/fixture riêng, ghi ID đơn thử nghiệm; nếu không có thao tác xóa được phép thì giữ và đánh dấu dữ liệu thử nghiệm, không sửa trực tiếp DB/SUT.

Evidence khi có lưu vào `test-cases/evidence/B08/` và liên kết tương đối từ card. Chưa có evidence thì để —. Không có trường Execution context riêng.

## 1. Tổng hợp

10 TC duy nhất: 8 chỉ Domain, 2 Functional, 0 BVA. Chưa thực thi: 10 Not Run. BVA chưa có ngưỡng được đặc tả; xem mục 5 của analysis trước khi tuyên bố đáp ứng đủ rubric.

| TC ID | Title | Requirement | Technique | Dữ liệu chính | Expected | Status | Bug |
|:---|:---|:---|:---|:---|:---|:---|:---|
| TC-B08-001 | Web hiển thị một dòng giỏ và tính đúng tổng | R2–R3 | Domain | F1: P giá 100000, q=2; T=200000; U đăng nhập; không coupon | Hiển thị sản phẩm P với số lượng 2; tổng thanh toán bằng 200000 ₫. | Not Run | — |
| TC-B08-002 | Web checkout nhiều sản phẩm và xóa giỏ sau thành công | R1–R5 | Domain | F2: P 100000 × 2, Q 50000 × 3; T=350000; U đăng nhập; không coupon | Đủ cả P/Q; tổng bằng 100000 × 2 + 50000 × 3 = 350000. Checkout hợp lệ thành công; đơn mới lưu total_amount=350000; giỏ Web sau thành công rỗng. | Not Run | — |
| TC-B08-003 | Web không cho guest hoàn thành checkout | R1 | Domain | Guest không có token; F1 nếu ứng dụng cho guest giữ giỏ; không coupon | Guest không hoàn thành checkout. Chặn truy cập, yêu cầu đăng nhập hoặc từ chối submit đều phù hợp; không ép URL redirect/message cụ thể. | Not Run | — |
| TC-B08-004 | Web không cho sửa trực tiếp tổng thanh toán | R2 | Functional | U; F1; T=200000; thử gõ/paste 1 vào total; không sửa DOM | Thao tác UI thông thường không sửa được tổng tiền thanh toán; tổng vẫn bằng 200000. Việc cho phép sửa trực tiếp đã vi phạm R2, kể cả backend có thể tính lại. | Not Run | — |
| TC-B08-005 | Web tính lại tổng khi giỏ thay đổi | R2 | Functional | U; ban đầu F1 q(P)=2; sau đó q(P)=3; T mới=300000 | Checkout phản ánh q(P)=3; total mới bằng 100000 × 3 = 300000, không giữ tổng cũ 200000. | Not Run | — |
| TC-B08-006 | API thiếu JWT không checkout được | R1, SEC-02 | Domain | POST /api/checkout; không Authorization; JSON {"total_amount":200000,"shipping_address":"123 Le Loi, TP.HCM"} | Request không hoàn thành checkout; không có đơn mới của U từ request thiếu JWT. Status/message chỉ ghi actual, không ép 401 theo spec chưa nêu. | Not Run | — |
| TC-B08-007 | API JWT sai không checkout được | R1, SEC-02 | Domain | POST /api/checkout; Authorization: Bearer invalid.b08.token; JSON {"total_amount":200000,"shipping_address":"123 Le Loi, TP.HCM"} | JWT sai không cho hoàn thành checkout; không có đơn mới của U từ request. Không ép status/message cụ thể. | Not Run | — |
| TC-B08-008 | API total đúng giỏ và xóa giỏ sau thành công | R4–R5 | Domain | POST /api/checkout; Bearer JWT U; JSON {"total_amount":200000,"shipping_address":"123 Le Loi, TP.HCM"} | Checkout nominal thành công; total_amount đơn mới bằng 200000 và giỏ API của U rỗng. HTTP thành công đơn lẻ chưa đủ để đánh Pass nếu tiền/giỏ sai. | Not Run | — |
| TC-B08-009 | API không tin total client thấp hơn giỏ | R4 | Domain | POST /api/checkout; Bearer JWT U; JSON {"total_amount":199999,"shipping_address":"123 Le Loi, TP.HCM"}; T=200000 | Backend từ chối request mismatch hoặc tạo đơn bằng T=200000 do tự tính lại. Không được lưu 199999. Nếu từ chối, ghi rõ không đi qua nhánh thành công; đường nominal được kiểm riêng ở TC008. | Not Run | — |
| TC-B08-010 | API không tin total client cao hơn giỏ | R4 | Domain | POST /api/checkout; Bearer JWT U; JSON {"total_amount":200001,"shipping_address":"123 Le Loi, TP.HCM"}; T=200000 | Backend từ chối request mismatch hoặc tạo đơn bằng T=200000 do tự tính lại. Không được lưu 200001. Không chấp nhận response thành công với total sai. | Not Run | — |

## 2. Chi tiết

### TC-B08-001 — Web hiển thị một dòng giỏ và tính đúng tổng

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-001; Web hiển thị một dòng giỏ và tính đúng tổng |
| Requirement / Purpose | R2–R3 theo FR-08; SEC-02 nếu có; kiểm tra web hiển thị một dòng giỏ và tính đúng tổng |
| Technique | Domain |
| Domain / Boundary | C1, C4; BVA: không áp dụng; [analysis](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Giỏ Web riêng của U đúng F1; xác minh tên/giá/số lượng trước khi mở checkout. Dựng fixture và tài khoản độc lập theo phần Chuẩn bị; nếu thiếu fixture ghi Blocked. |
| Test data | F1: P giá 100000, q=2; T=200000; U đăng nhập; không coupon |
| Steps | 1. Mở /checkout từ giỏ F1.<br>2. Đối chiếu danh sách với F1.<br>3. Tính 100000 × 2 độc lập.<br>4. Ghi tổng hiển thị; chưa submit. |
| Expected Result | Hiển thị sản phẩm P với số lượng 2; tổng thanh toán bằng 200000 ₫. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID đơn tạo ra nếu có; cô lập tài khoản/giỏ thử nghiệm; khôi phục fixture Web nếu phù hợp, không reset DB dùng chung. |

### TC-B08-002 — Web checkout nhiều sản phẩm và xóa giỏ sau thành công

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-002; Web checkout nhiều sản phẩm và xóa giỏ sau thành công |
| Requirement / Purpose | R1–R5 theo FR-08; SEC-02 nếu có; kiểm tra web checkout nhiều sản phẩm và xóa giỏ sau thành công |
| Technique | Domain |
| Domain / Boundary | C1, C4; BVA: không áp dụng; [analysis](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Dựng giỏ Web F2 cho U; snapshot đơn qua GET /api/orders/my-orders bằng JWT U; ghi ID các đơn đã có. Dựng fixture và tài khoản độc lập theo phần Chuẩn bị; nếu thiếu fixture ghi Blocked. |
| Test data | F2: P 100000 × 2, Q 50000 × 3; T=350000; U đăng nhập; không coupon |
| Steps | 1. Mở /checkout.<br>2. Kiểm P/Q, số lượng và tổng.<br>3. Nhấn Xác Nhận Thanh Toán một lần.<br>4. GET /api/orders/my-orders bằng JWT U, xác định đơn mới theo chênh lệch ID.<br>5. Quay về giỏ trong cùng phiên Web, kiểm giỏ/badge; không dùng refresh làm bằng chứng duy nhất. |
| Expected Result | Đủ cả P/Q; tổng bằng 100000 × 2 + 50000 × 3 = 350000. Checkout hợp lệ thành công; đơn mới lưu total_amount=350000; giỏ Web sau thành công rỗng. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID đơn tạo ra nếu có; cô lập tài khoản/giỏ thử nghiệm; khôi phục fixture Web nếu phù hợp, không reset DB dùng chung. |

### TC-B08-003 — Web không cho guest hoàn thành checkout

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-003; Web không cho guest hoàn thành checkout |
| Requirement / Purpose | R1 theo FR-08; SEC-02 nếu có; kiểm tra web không cho guest hoàn thành checkout |
| Technique | Domain |
| Domain / Boundary | C2; BVA: không áp dụng; [analysis](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Mở browser session riêng không đăng nhập. Thử chuẩn bị giỏ F1; giữ JWT hợp lệ riêng của U chỉ để kiểm đơn nếu cần, không đưa vào phiên guest. Dựng fixture và tài khoản độc lập theo phần Chuẩn bị; nếu thiếu fixture ghi Blocked. |
| Test data | Guest không có token; F1 nếu ứng dụng cho guest giữ giỏ; không coupon |
| Steps | 1. Truy cập /checkout bằng guest, cả qua điều hướng mua hàng và URL trực tiếp.<br>2. Nếu có trang checkout, thử xác nhận.<br>3. Ghi redirect/thông báo hoặc request và kết quả.<br>4. Kiểm không có checkout thành công. Nếu bị chặn từ trước checkout, ghi rõ điểm chặn; không cần vượt auth để ép chạy. |
| Expected Result | Guest không hoàn thành checkout. Chặn truy cập, yêu cầu đăng nhập hoặc từ chối submit đều phù hợp; không ép URL redirect/message cụ thể. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID đơn tạo ra nếu có; cô lập tài khoản/giỏ thử nghiệm; khôi phục fixture Web nếu phù hợp, không reset DB dùng chung. |

### TC-B08-004 — Web không cho sửa trực tiếp tổng thanh toán

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-004; Web không cho sửa trực tiếp tổng thanh toán |
| Requirement / Purpose | R2 theo FR-08; SEC-02 nếu có; kiểm tra web không cho sửa trực tiếp tổng thanh toán |
| Technique | Functional |
| Domain / Boundary | —; BVA: không áp dụng; [analysis](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Giỏ Web F1 của U; mở checkout; snapshot tổng đang hiển thị. Dựng fixture và tài khoản độc lập theo phần Chuẩn bị; nếu thiếu fixture ghi Blocked. |
| Test data | U; F1; T=200000; thử gõ/paste 1 vào total; không sửa DOM |
| Steps | 1. Thử focus vùng tổng tiền bằng chuột và Tab.<br>2. Nếu có input, thử gõ, paste 1 và dùng spinner nếu có.<br>3. Quan sát total; không dùng DevTools sửa DOM, không submit với dữ liệu sai. |
| Expected Result | Thao tác UI thông thường không sửa được tổng tiền thanh toán; tổng vẫn bằng 200000. Việc cho phép sửa trực tiếp đã vi phạm R2, kể cả backend có thể tính lại. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID đơn tạo ra nếu có; cô lập tài khoản/giỏ thử nghiệm; khôi phục fixture Web nếu phù hợp, không reset DB dùng chung. |

### TC-B08-005 — Web tính lại tổng khi giỏ thay đổi

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-005; Web tính lại tổng khi giỏ thay đổi |
| Requirement / Purpose | R2 theo FR-08; SEC-02 nếu có; kiểm tra web tính lại tổng khi giỏ thay đổi |
| Technique | Functional |
| Domain / Boundary | —; BVA: không áp dụng; [analysis](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Dựng giỏ Web F1 của U; snapshot trước đổi; không coupon. Dựng fixture và tài khoản độc lập theo phần Chuẩn bị; nếu thiếu fixture ghi Blocked. |
| Test data | U; ban đầu F1 q(P)=2; sau đó q(P)=3; T mới=300000 |
| Steps | 1. Mở checkout, ghi tổng 200000.<br>2. Quay về giỏ, đổi q(P) từ 2 thành 3 bằng thao tác được UI hỗ trợ.<br>3. Mở lại checkout từ giỏ.<br>4. So sánh danh sách và total với giỏ đã đổi. |
| Expected Result | Checkout phản ánh q(P)=3; total mới bằng 100000 × 3 = 300000, không giữ tổng cũ 200000. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID đơn tạo ra nếu có; cô lập tài khoản/giỏ thử nghiệm; khôi phục fixture Web nếu phù hợp, không reset DB dùng chung. |

### TC-B08-006 — API thiếu JWT không checkout được

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-006; API thiếu JWT không checkout được |
| Requirement / Purpose | R1, SEC-02 theo FR-08; SEC-02 nếu có; kiểm tra api thiếu jwt không checkout được |
| Technique | Domain |
| Domain / Boundary | C2, C4; BVA: không áp dụng; [analysis](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Giỏ API F1 của U đã xác minh GET /api/cart; snapshot orders bằng JWT U hợp lệ riêng. Không gửi JWT ở request checkout. Dựng fixture và tài khoản độc lập theo phần Chuẩn bị; nếu thiếu fixture ghi Blocked. |
| Test data | POST /api/checkout; không Authorization; JSON {"total_amount":200000,"shipping_address":"123 Le Loi, TP.HCM"} |
| Steps | 1. Gửi POST /api/checkout với JSON trên, bỏ Authorization hoàn toàn.<br>2. Ghi response/status.<br>3. GET /api/orders/my-orders bằng JWT U hợp lệ để đối chiếu snapshot. |
| Expected Result | Request không hoàn thành checkout; không có đơn mới của U từ request thiếu JWT. Status/message chỉ ghi actual, không ép 401 theo spec chưa nêu. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID đơn tạo ra nếu có; cô lập tài khoản/giỏ thử nghiệm; khôi phục fixture Web nếu phù hợp, không reset DB dùng chung. |

### TC-B08-007 — API JWT sai không checkout được

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-007; API JWT sai không checkout được |
| Requirement / Purpose | R1, SEC-02 theo FR-08; SEC-02 nếu có; kiểm tra api jwt sai không checkout được |
| Technique | Domain |
| Domain / Boundary | C3, C4; BVA: không áp dụng; [analysis](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Giỏ API F1 của U; snapshot orders bằng JWT U hợp lệ riêng; chỉ JWT trong checkout bị đổi thành chuỗi sai. Dựng fixture và tài khoản độc lập theo phần Chuẩn bị; nếu thiếu fixture ghi Blocked. |
| Test data | POST /api/checkout; Authorization: Bearer invalid.b08.token; JSON {"total_amount":200000,"shipping_address":"123 Le Loi, TP.HCM"} |
| Steps | 1. Gửi POST /api/checkout với body và token sai trên.<br>2. Ghi response/status.<br>3. GET /api/orders/my-orders bằng JWT U thật và đối chiếu snapshot. |
| Expected Result | JWT sai không cho hoàn thành checkout; không có đơn mới của U từ request. Không ép status/message cụ thể. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID đơn tạo ra nếu có; cô lập tài khoản/giỏ thử nghiệm; khôi phục fixture Web nếu phù hợp, không reset DB dùng chung. |

### TC-B08-008 — API total đúng giỏ và xóa giỏ sau thành công

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-008; API total đúng giỏ và xóa giỏ sau thành công |
| Requirement / Purpose | R4–R5 theo FR-08; SEC-02 nếu có; kiểm tra api total đúng giỏ và xóa giỏ sau thành công |
| Technique | Domain |
| Domain / Boundary | C1, C4; BVA: không áp dụng; [analysis](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Giỏ API F1 của U đã xác minh; snapshot orders; không coupon. Mỗi lần chạy dùng fixture mới dù test trước đã thành công. Dựng fixture và tài khoản độc lập theo phần Chuẩn bị; nếu thiếu fixture ghi Blocked. |
| Test data | POST /api/checkout; Bearer JWT U; JSON {"total_amount":200000,"shipping_address":"123 Le Loi, TP.HCM"} |
| Steps | 1. Tính T=100000 × 2=200000 độc lập.<br>2. POST /api/checkout với JWT U hợp lệ và body trên.<br>3. GET /api/orders/my-orders, đối chiếu đơn mới theo snapshot/ID thực tế.<br>4. GET /api/cart bằng JWT U. |
| Expected Result | Checkout nominal thành công; total_amount đơn mới bằng 200000 và giỏ API của U rỗng. HTTP thành công đơn lẻ chưa đủ để đánh Pass nếu tiền/giỏ sai. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID đơn tạo ra nếu có; cô lập tài khoản/giỏ thử nghiệm; khôi phục fixture Web nếu phù hợp, không reset DB dùng chung. |

### TC-B08-009 — API không tin total client thấp hơn giỏ

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-009; API không tin total client thấp hơn giỏ |
| Requirement / Purpose | R4 theo FR-08; SEC-02 nếu có; kiểm tra api không tin total client thấp hơn giỏ |
| Technique | Domain |
| Domain / Boundary | C1, C5; BVA: không áp dụng; [analysis](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Giỏ API F1 của U đã xác minh; snapshot orders; giữ JWT/body còn lại giống TC008, chỉ đổi total. Không dựa vào trạng thái sau TC008. Dựng fixture và tài khoản độc lập theo phần Chuẩn bị; nếu thiếu fixture ghi Blocked. |
| Test data | POST /api/checkout; Bearer JWT U; JSON {"total_amount":199999,"shipping_address":"123 Le Loi, TP.HCM"}; T=200000 |
| Steps | 1. Xác minh F1 qua GET /api/cart; tính T=200000.<br>2. POST /api/checkout với total_amount=199999.<br>3. Ghi response, GET /api/orders/my-orders bằng JWT U; đối chiếu đơn mới và tiền lưu. |
| Expected Result | Backend từ chối request mismatch hoặc tạo đơn bằng T=200000 do tự tính lại. Không được lưu 199999. Nếu từ chối, ghi rõ không đi qua nhánh thành công; đường nominal được kiểm riêng ở TC008. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID đơn tạo ra nếu có; cô lập tài khoản/giỏ thử nghiệm; khôi phục fixture Web nếu phù hợp, không reset DB dùng chung. |

### TC-B08-010 — API không tin total client cao hơn giỏ

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-010; API không tin total client cao hơn giỏ |
| Requirement / Purpose | R4 theo FR-08; SEC-02 nếu có; kiểm tra api không tin total client cao hơn giỏ |
| Technique | Domain |
| Domain / Boundary | C1, C6; BVA: không áp dụng; [analysis](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Giỏ API F1 của U đã xác minh; snapshot orders; giữ JWT/body còn lại giống TC008, chỉ đổi total. Không dựa vào trạng thái sau TC009. Dựng fixture và tài khoản độc lập theo phần Chuẩn bị; nếu thiếu fixture ghi Blocked. |
| Test data | POST /api/checkout; Bearer JWT U; JSON {"total_amount":200001,"shipping_address":"123 Le Loi, TP.HCM"}; T=200000 |
| Steps | 1. Xác minh F1 qua GET /api/cart; tính T=200000.<br>2. POST /api/checkout với total_amount=200001.<br>3. Ghi response, GET /api/orders/my-orders bằng JWT U; đối chiếu đơn mới và tiền lưu. |
| Expected Result | Backend từ chối request mismatch hoặc tạo đơn bằng T=200000 do tự tính lại. Không được lưu 200001. Không chấp nhận response thành công với total sai. |
| Actual Result | Chưa thực thi |
| Status | Not Run |
| Evidence | — |
| Bug ID / Issue URL | — |
| Cleanup | Ghi ID đơn tạo ra nếu có; cô lập tài khoản/giỏ thử nghiệm; khôi phục fixture Web nếu phù hợp, không reset DB dùng chung. |

