# Test Cases: B08 — Thanh toán

- **Trạng thái:** 12 test: 4 Pass, 7 Fail, 1 Blocked; 0 Not Run. Kết quả Web do người dùng cung cấp, API do Postman CLI thực thi.
- **Yêu cầu gốc:** [FR-08 và SEC-02](../eshop-sut/README.md). [Cách gọi API](../eshop-sut/api_specification.md).
- **Phân tích và truy vết:** [analysis/B08_Checkout.md](../analysis/B08_Checkout.md).
- **Ngày cập nhật:** 2026-10-18, Asia/Bangkok.

## Chuẩn bị

Web: http://localhost:5173. Backend: http://localhost:3000.

Dùng tài khoản thử nghiệm có quyền user. Với test API, đăng nhập bằng POST /api/login để lấy token. Chỉ lưu token ở công cụ chạy test, không chép vào báo cáo.

Chuẩn bị một sản phẩm giá **100000 ₫**; riêng TC-B08-002 cần thêm sản phẩm khác giá **50000 ₫**. Đây là giá dữ liệu thử nghiệm, chưa khẳng định catalog có sẵn. Nếu chưa có, dùng tài khoản Admin tạo sản phẩm thử và ghi tên/ID thật. Nếu chưa chuẩn bị được thì ghi Blocked. Không áp dụng mã giảm giá trong các test này.

Test Web thêm sản phẩm bằng giao diện. Test API thêm bằng POST /api/cart với token hợp lệ, rồi kiểm tra GET /api/cart. Body thêm sản phẩm gồm id và name thật của sản phẩm đã chuẩn bị, price=100000, quantity=2. Giỏ Web và giỏ API phải được xác minh riêng.

Trước từng test có thao tác thanh toán, ghi danh sách mã đơn đang có bằng GET /api/orders/my-orders với token hợp lệ. Sau test, đọc lại để xác định có đơn mới hay không. Mỗi test phải bắt đầu bằng đúng giỏ mô tả trong card; nếu chưa dọn được giỏ API, dùng tài khoản thử nghiệm mới thay vì dựa vào kết quả test trước.

**Body cho các request POST /api/checkout:**

~~~json
{
  "total_amount": 200000,
  "shipping_address": "123 Le Loi, TP.HCM"
}
~~~

Thay total_amount bằng giá trị trong từng card. Dùng Content-Type: application/json. Test cần token hợp lệ gửi Authorization: Bearer <token lấy lúc đăng nhập>; TC006 bỏ header này, TC007 dùng token sai đã ghi trong card.

Hai quy tắc bổ sung đã được bạn xác nhận: **giỏ trống phải từ chối, báo lỗi và không tạo đơn; tổng tiền gửi lên khác tổng giỏ cũng phải từ chối, báo lỗi và không tạo đơn.** Không bắt buộc một câu lỗi hoặc mã HTTP cụ thể. Chưa thêm quy tắc làm tròn tiền.

**Lần chạy API 2026-10-18:** backend nguyên mã trong database tạm riêng. Chi tiết và response: [execution summary](evidence/B08/b08_api_execution_summary.md). Không phải bằng chứng thực thi UI Web.

**Kết quả Web 2026-10-18:** cập nhật theo báo cáo thực thi của bạn. Ảnh hiện có: TC003, TC004, TC011. TC005 ghi Blocked cho bước đổi số lượng trong giỏ; quan sát tổng tiền đúng qua cách thêm sản phẩm được giữ riêng trong Actual Result.

Rà soát 2026-10-19: cả 6 status API khớp evidence CLI (2 Pass, 4 Fail). Tổng giữ 4 Pass, 7 Fail, 1 Blocked; TC005 Web vẫn Blocked, không thay bằng kết quả đường thao tác khác. Không chạy lại trong lượt rà này.

## 1. Tổng hợp

12 test: 8 chỉ Domain Testing, 2 thuộc cả Domain Testing và BVA, 2 kiểm tra chức năng. Kết quả hiện tại: 4 Pass, 7 Fail, 1 Blocked, 0 Not Run. Đã hoàn thành 11 test (Pass + Fail); TC005 đã thử nhưng bị chặn tại bước đổi số lượng trong giỏ.

| TC ID | Nội dung kiểm tra | Dữ liệu chính | Kết quả mong đợi | Trạng thái |
|:---|:---|:---|:---|:---|
| TC-B08-001 | Web hiển thị đúng sản phẩm và tổng tiền | Một sản phẩm × 2; tổng đúng 200000 ₫. | Hiển thị đúng sản phẩm và 200000 ₫ | Pass |
| TC-B08-002 | Web thanh toán nhiều sản phẩm và xóa giỏ | Sản phẩm thứ nhất: 100000 ₫ × 2. Sản phẩm thứ hai: 50000 ₫ × 3. Tổng đúng 350000 ₫. | Đơn 350000 ₫, giỏ rỗng sau thành công | Fail |
| TC-B08-003 | Web chặn thanh toán khi chưa đăng nhập | Không có phiên đăng nhập hoặc token trong cửa sổ thử nghiệm. | Không thanh toán được khi chưa đăng nhập | Pass |
| TC-B08-004 | Web không cho sửa trực tiếp tổng tiền | Tổng đúng 200000 ₫; thử sửa thành 1 bằng thao tác nhập thông thường. | Không sửa trực tiếp được tổng tiền | Fail |
| TC-B08-005 | Web tính lại tổng khi thay đổi số lượng | Đổi số lượng từ 2 thành 3; tổng mới phải là 300000 ₫. | Tổng đổi thành 300000 ₫ | Blocked |
| TC-B08-006 | API từ chối thanh toán khi thiếu token | POST /api/checkout; không gửi header Authorization; body như bên dưới với total_amount=200000. | Từ chối, không tạo đơn | Pass |
| TC-B08-007 | API từ chối thanh toán khi token sai | POST /api/checkout; Authorization: Bearer invalid.b08.token; body với total_amount=200000. | Từ chối, không tạo đơn | Pass |
| TC-B08-008 | API thanh toán giỏ có một dòng sản phẩm | POST /api/checkout; token hợp lệ; body với total_amount=200000. Một dòng sản phẩm có số lượng 2 vẫn được tính là một dòng giỏ. | Đơn 200000 ₫, giỏ API rỗng | Fail |
| TC-B08-009 | API từ chối tổng tiền gửi lên thấp hơn tổng giỏ | POST /api/checkout; token hợp lệ; body với total_amount=199999. | Từ chối, báo lỗi, không tạo đơn | Fail |
| TC-B08-010 | API từ chối tổng tiền gửi lên cao hơn tổng giỏ | POST /api/checkout; token hợp lệ; body với total_amount=200001. | Từ chối, báo lỗi, không tạo đơn | Fail |
| TC-B08-011 | Web chặn thanh toán giỏ trống | Giỏ không có sản phẩm; tổng bằng 0. | Chặn, báo giỏ trống, không tạo đơn | Fail |
| TC-B08-012 | API từ chối thanh toán giỏ trống | POST /api/checkout; token hợp lệ; body với total_amount=0. Tổng gửi lên khớp giỏ trống để chỉ kiểm tra lỗi giỏ trống. | Từ chối, báo lỗi, không tạo đơn | Fail |

## 2. Chi tiết

### TC-B08-001 — Web hiển thị đúng sản phẩm và tổng tiền

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-001; Web hiển thị đúng sản phẩm và tổng tiền |
| Requirement / Purpose | FR-08: danh sách sản phẩm và tổng tiền tự tính |
| Technique | Domain Testing |
| Domain / Boundary | C1, C4; xem [phân tích](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Đã đăng nhập. Thêm một sản phẩm giá 100000 ₫, số lượng 2 vào giỏ trên Web. |
| Test data | Một sản phẩm × 2; tổng đúng 200000 ₫. |
| Steps | 1. Mở trang thanh toán.<br>2. Kiểm tra tên sản phẩm, số lượng và tổng tiền. |
| Expected Result | Hiển thị đúng sản phẩm, số lượng 2 và tổng tiền 200000 ₫. |
| Actual Result | Theo người dùng thực thi Web: hiển thị đúng sản phẩm, số lượng 2 và tổng tiền 200000 ₫. |
| Status | Pass |
| Evidence | — (kết quả do người dùng báo cáo; chưa có ảnh TC-B08-001 trong workspace) |
| Bug ID / Issue URL | — |
| Cleanup | Ghi mã đơn thử nghiệm nếu có; dọn giỏ thử bằng thao tác được hỗ trợ. Nếu không dọn được thì dùng tài khoản thử nghiệm khác cho lần chạy sau. |

### TC-B08-002 — Web thanh toán nhiều sản phẩm và xóa giỏ

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-002; Web thanh toán nhiều sản phẩm và xóa giỏ |
| Requirement / Purpose | FR-08: thanh toán, tính tiền, hiển thị sản phẩm và xóa giỏ |
| Technique | Domain Testing |
| Domain / Boundary | C1, C4; xem [phân tích](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Đã đăng nhập. Giỏ Web có hai sản phẩm khác nhau như dữ liệu bên dưới. Ghi danh sách mã đơn hiện có. |
| Test data | Sản phẩm thứ nhất: 100000 ₫ × 2. Sản phẩm thứ hai: 50000 ₫ × 3. Tổng đúng 350000 ₫. |
| Steps | 1. Mở trang thanh toán, kiểm tra đủ hai sản phẩm và số lượng.<br>2. Kiểm tra tổng tiền bằng 350000 ₫.<br>3. Nhấn Xác Nhận Thanh Toán một lần.<br>4. Đọc danh sách đơn hàng và xác định đơn vừa tạo bằng mã đơn mới.<br>5. Quay lại giỏ trong cùng phiên trình duyệt. |
| Expected Result | Thanh toán thành công; đơn mới lưu tổng tiền 350000 ₫; giỏ hàng không còn sản phẩm. |
| Actual Result | Theo người dùng thực thi Web: thanh toán thành công, đơn mới lưu 350000 ₫, nhưng giỏ vẫn còn sản phẩm. |
| Status | Fail |
| Evidence | — (kết quả do người dùng báo cáo; chưa có ảnh TC-B08-002 trong workspace) |
| Bug ID / Issue URL | [BUG-B08-004](../bug-reports/bugs.md) |
| Cleanup | Ghi mã đơn thử nghiệm nếu có; dọn giỏ thử bằng thao tác được hỗ trợ. Nếu không dọn được thì dùng tài khoản thử nghiệm khác cho lần chạy sau. |

### TC-B08-003 — Web chặn thanh toán khi chưa đăng nhập

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-003; Web chặn thanh toán khi chưa đăng nhập |
| Requirement / Purpose | FR-08: chỉ người đã đăng nhập được thanh toán |
| Technique | Domain Testing |
| Domain / Boundary | C2; xem [phân tích](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Mở cửa sổ riêng chưa đăng nhập. Nếu Web cho phép, thêm một sản phẩm giá 100000 ₫ với số lượng 2 vào giỏ. |
| Test data | Không có phiên đăng nhập hoặc token trong cửa sổ thử nghiệm. |
| Steps | 1. Thử mở trang thanh toán từ giỏ hàng và bằng URL /checkout.<br>2. Nếu trang cho phép, nhấn xác nhận thanh toán.<br>3. Ghi lại điểm bị chặn và thông báo. |
| Expected Result | Không thanh toán thành công. Chuyển sang đăng nhập, chặn trang hoặc từ chối xác nhận đều phù hợp; không bắt buộc một URL hay câu thông báo cụ thể. |
| Actual Result | Theo người dùng thực thi Web: hiển thị dialog "Bạn cần đăng nhập để thanh toán!", không thanh toán thành công và chuyển sang đăng nhập. |
| Status | Pass |
| Evidence | [TC-B08-003.png](evidence/B08/TC-B08-003.png) |
| Bug ID / Issue URL | — |
| Cleanup | Ghi mã đơn thử nghiệm nếu có; dọn giỏ thử bằng thao tác được hỗ trợ. Nếu không dọn được thì dùng tài khoản thử nghiệm khác cho lần chạy sau. |

### TC-B08-004 — Web không cho sửa trực tiếp tổng tiền

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-004; Web không cho sửa trực tiếp tổng tiền |
| Requirement / Purpose | FR-08: tổng thanh toán không được sửa trực tiếp |
| Technique | Functional |
| Domain / Boundary | —; xem [phân tích](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Đã đăng nhập. Giỏ Web có một sản phẩm giá 100000 ₫, số lượng 2. |
| Test data | Tổng đúng 200000 ₫; thử sửa thành 1 bằng thao tác nhập thông thường. |
| Steps | 1. Mở trang thanh toán.<br>2. Thử chọn vùng tổng tiền bằng chuột và phím Tab.<br>3. Nếu có ô nhập, thử gõ hoặc dán số 1; thử nút tăng/giảm nếu có.<br>4. Quan sát giá trị; chưa xác nhận thanh toán. |
| Expected Result | Không sửa được tổng tiền bằng thao tác UI thông thường; tổng vẫn là 200000 ₫. Nếu sửa được thì test Fail, kể cả backend có thể tính lại. |
| Actual Result | Theo người dùng thực thi Web: sửa được tổng tiền trực tiếp trên giao diện thanh toán. |
| Status | Fail |
| Evidence | [TC-B08-004.png](evidence/B08/TC-B08-004.png) |
| Bug ID / Issue URL | [BUG-B08-005](../bug-reports/bugs.md) |
| Cleanup | Ghi mã đơn thử nghiệm nếu có; dọn giỏ thử bằng thao tác được hỗ trợ. Nếu không dọn được thì dùng tài khoản thử nghiệm khác cho lần chạy sau. |

### TC-B08-005 — Web tính lại tổng khi thay đổi số lượng

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-005; Web tính lại tổng khi thay đổi số lượng |
| Requirement / Purpose | FR-08: tổng tiền được tính tự động từ giỏ hàng |
| Technique | Functional |
| Domain / Boundary | —; xem [phân tích](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Đã đăng nhập. Giỏ Web có một sản phẩm giá 100000 ₫, số lượng 2. |
| Test data | Đổi số lượng từ 2 thành 3; tổng mới phải là 300000 ₫. |
| Steps | 1. Mở trang thanh toán, ghi nhận tổng 200000 ₫.<br>2. Quay lại giỏ và đổi số lượng sản phẩm thành 3.<br>3. Mở lại trang thanh toán.<br>4. Kiểm tra số lượng và tổng tiền. |
| Expected Result | Hiển thị số lượng 3 và tổng tiền 100000 × 3 = 300000 ₫. |
| Actual Result | Không thực hiện được bước đổi số lượng trực tiếp trong giỏ vì UI không hỗ trợ. Người dùng thử đường khác: quay về trang chính thêm cùng sản phẩm; giỏ hiển thị hai dòng (ví dụ sản phẩm A × 2 và A × 1), tổng tiền khi mở checkout vẫn cập nhật. Đây là quan sát bổ sung về đường thêm sản phẩm, chưa đủ để gán Pass/Fail cho đúng các bước test hiện tại; không khẳng định giá trị mới 300000 ₫ khi báo cáo chưa nêu số cụ thể. |
| Status | Blocked |
| Evidence | — (quan sát do người dùng báo cáo; chưa có ảnh TC-B08-005 trong workspace) |
| Bug ID / Issue URL | — (vướng chức năng giỏ hàng FR-07; chưa gán lỗi tính tổng của B08) |
| Cleanup | Ghi mã đơn thử nghiệm nếu có; dọn giỏ thử bằng thao tác được hỗ trợ. Nếu không dọn được thì dùng tài khoản thử nghiệm khác cho lần chạy sau. |

### TC-B08-006 — API từ chối thanh toán khi thiếu token

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-006; API từ chối thanh toán khi thiếu token |
| Requirement / Purpose | FR-08 và SEC-02: thanh toán cần xác thực |
| Technique | Domain Testing |
| Domain / Boundary | C2, C4; xem [phân tích](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Chuẩn bị giỏ API có một sản phẩm giá 100000 ₫, số lượng 2. Ghi danh sách đơn trước test bằng token hợp lệ; chỉ request thanh toán bỏ token. |
| Test data | POST /api/checkout; không gửi header Authorization; body như bên dưới với total_amount=200000. |
| Steps | 1. Gửi request thanh toán không có Authorization.<br>2. Ghi response và mã HTTP.<br>3. Dùng token hợp lệ đọc lại danh sách đơn, so sánh với trước test. |
| Expected Result | Request bị từ chối, không tạo đơn cho tài khoản thử nghiệm. Không bắt buộc mã HTTP hay câu lỗi cụ thể. |
| Actual Result | Postman CLI: HTTP 401, {"error":"Unauthorized"}. Không có đơn mới sau request; giỏ vẫn còn sản phẩm ban đầu. |
| Status | Pass |
| Evidence | [Log CLI](evidence/B08/postman-cli.log); [response/snapshot JSON](evidence/B08/b08_api_results.json); [summary](evidence/B08/b08_api_execution_summary.md) |
| Bug ID / Issue URL | — |
| Cleanup | User/giỏ/đơn thử nghiệm nằm trong database tạm riêng; backend thử nghiệm được dừng sau khi lưu evidence. Không reset database workspace. |

### TC-B08-007 — API từ chối thanh toán khi token sai

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-007; API từ chối thanh toán khi token sai |
| Requirement / Purpose | FR-08 và SEC-02: token phải hợp lệ |
| Technique | Domain Testing |
| Domain / Boundary | C3, C4; xem [phân tích](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Chuẩn bị giỏ API có một sản phẩm giá 100000 ₫, số lượng 2. Ghi danh sách đơn trước test bằng token hợp lệ riêng. |
| Test data | POST /api/checkout; Authorization: Bearer invalid.b08.token; body với total_amount=200000. |
| Steps | 1. Gửi request thanh toán với token sai trong dữ liệu trên.<br>2. Ghi response và mã HTTP.<br>3. Dùng token hợp lệ đọc danh sách đơn và so sánh với trước test. |
| Expected Result | Request bị từ chối, không tạo đơn cho tài khoản thử nghiệm. Không bắt buộc mã HTTP hay câu lỗi cụ thể. |
| Actual Result | Postman CLI: HTTP 403, {"error":"Forbidden"}. Không có đơn mới sau request; giỏ vẫn còn sản phẩm ban đầu. |
| Status | Pass |
| Evidence | [Log CLI](evidence/B08/postman-cli.log); [response/snapshot JSON](evidence/B08/b08_api_results.json); [summary](evidence/B08/b08_api_execution_summary.md) |
| Bug ID / Issue URL | — |
| Cleanup | User/giỏ/đơn thử nghiệm nằm trong database tạm riêng; backend thử nghiệm được dừng sau khi lưu evidence. Không reset database workspace. |

### TC-B08-008 — API thanh toán giỏ có một dòng sản phẩm

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-008; API thanh toán giỏ có một dòng sản phẩm |
| Requirement / Purpose | FR-08: lưu đúng tiền và xóa giỏ; quyết định bổ sung: giỏ phải có sản phẩm |
| Technique | Domain Testing + BVA |
| Domain / Boundary | C1, C4, C8; B1 on-point: 1 dòng sản phẩm; xem [phân tích](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Đăng nhập và chuẩn bị giỏ API có đúng một dòng: sản phẩm giá 100000 ₫, số lượng 2. Ghi danh sách đơn trước test. |
| Test data | POST /api/checkout; token hợp lệ; body với total_amount=200000. Một dòng sản phẩm có số lượng 2 vẫn được tính là một dòng giỏ. |
| Steps | 1. Kiểm tra GET /api/cart có đúng sản phẩm và số lượng đã chuẩn bị.<br>2. Gửi request thanh toán.<br>3. Đọc danh sách đơn, tìm đơn mới và kiểm tra total_amount.<br>4. Gọi GET /api/cart lại. |
| Expected Result | Thanh toán thành công; đơn mới có total_amount=200000; giỏ API rỗng. |
| Actual Result | Postman CLI: HTTP 200, tạo đơn ID 1 với total_amount=200000. GET /api/cart sau thành công vẫn còn sản phẩm giá 100000 × 2; không đạt yêu cầu xóa giỏ. |
| Status | Fail |
| Evidence | [Log CLI](evidence/B08/postman-cli.log); [response/snapshot JSON](evidence/B08/b08_api_results.json); [summary](evidence/B08/b08_api_execution_summary.md) |
| Bug ID / Issue URL | [BUG-B08-001](../bug-reports/bugs.md) |
| Cleanup | User/giỏ/đơn thử nghiệm nằm trong database tạm riêng; backend thử nghiệm được dừng sau khi lưu evidence. Không reset database workspace. |

### TC-B08-009 — API từ chối tổng tiền gửi lên thấp hơn tổng giỏ

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-009; API từ chối tổng tiền gửi lên thấp hơn tổng giỏ |
| Requirement / Purpose | FR-08 và quyết định bổ sung của bạn: lệch tổng tiền phải báo lỗi, không tạo đơn |
| Technique | Domain Testing |
| Domain / Boundary | C1, C5; xem [phân tích](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Đăng nhập. Chuẩn bị giỏ API có một sản phẩm giá 100000 ₫, số lượng 2; tổng đúng 200000 ₫. Ghi danh sách đơn trước test. |
| Test data | POST /api/checkout; token hợp lệ; body với total_amount=199999. |
| Steps | 1. Kiểm tra giỏ API và tự tính tổng đúng 200000 ₫.<br>2. Gửi request với total_amount=199999.<br>3. Ghi response và thông báo lỗi.<br>4. Đọc danh sách đơn và so sánh mã đơn trước/sau. |
| Expected Result | Backend từ chối request, báo lỗi và không tạo đơn. Tự sửa thành 200000 rồi tạo đơn cũng là Fail theo quyết định đã chốt. |
| Actual Result | Postman CLI: HTTP 200, message Checkout successful; tạo đơn ID 2 với total_amount=199999 dù tổng giỏ là 200000. Không từ chối và không báo lỗi. |
| Status | Fail |
| Evidence | [Log CLI](evidence/B08/postman-cli.log); [response/snapshot JSON](evidence/B08/b08_api_results.json); [summary](evidence/B08/b08_api_execution_summary.md) |
| Bug ID / Issue URL | [BUG-B08-002](../bug-reports/bugs.md) |
| Cleanup | User/giỏ/đơn thử nghiệm nằm trong database tạm riêng; backend thử nghiệm được dừng sau khi lưu evidence. Không reset database workspace. |

### TC-B08-010 — API từ chối tổng tiền gửi lên cao hơn tổng giỏ

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-010; API từ chối tổng tiền gửi lên cao hơn tổng giỏ |
| Requirement / Purpose | FR-08 và quyết định bổ sung của bạn: lệch tổng tiền phải báo lỗi, không tạo đơn |
| Technique | Domain Testing |
| Domain / Boundary | C1, C6; xem [phân tích](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Đăng nhập. Chuẩn bị giỏ API có một sản phẩm giá 100000 ₫, số lượng 2; tổng đúng 200000 ₫. Ghi danh sách đơn trước test. |
| Test data | POST /api/checkout; token hợp lệ; body với total_amount=200001. |
| Steps | 1. Kiểm tra giỏ API và tự tính tổng đúng 200000 ₫.<br>2. Gửi request với total_amount=200001.<br>3. Ghi response và thông báo lỗi.<br>4. Đọc danh sách đơn và so sánh mã đơn trước/sau. |
| Expected Result | Backend từ chối request, báo lỗi và không tạo đơn. Tự sửa thành 200000 rồi tạo đơn cũng là Fail theo quyết định đã chốt. |
| Actual Result | Postman CLI: HTTP 200, message Checkout successful; tạo đơn ID 3 với total_amount=200001 dù tổng giỏ là 200000. Không từ chối và không báo lỗi. |
| Status | Fail |
| Evidence | [Log CLI](evidence/B08/postman-cli.log); [response/snapshot JSON](evidence/B08/b08_api_results.json); [summary](evidence/B08/b08_api_execution_summary.md) |
| Bug ID / Issue URL | [BUG-B08-002](../bug-reports/bugs.md) |
| Cleanup | User/giỏ/đơn thử nghiệm nằm trong database tạm riêng; backend thử nghiệm được dừng sau khi lưu evidence. Không reset database workspace. |

### TC-B08-011 — Web chặn thanh toán giỏ trống

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-011; Web chặn thanh toán giỏ trống |
| Requirement / Purpose | Quyết định bổ sung của bạn: giỏ trống phải báo lỗi và không tạo đơn |
| Technique | Domain Testing |
| Domain / Boundary | C1, C7; xem [phân tích](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Đã đăng nhập. Xóa hết sản phẩm trong giỏ Web; kiểm tra giỏ thực sự trống. Ghi danh sách đơn trước test. |
| Test data | Giỏ không có sản phẩm; tổng bằng 0. |
| Steps | 1. Thử mở trang thanh toán từ giỏ và bằng URL /checkout.<br>2. Nếu UI cho phép, nhấn xác nhận; nếu đã chặn thì ghi lại thông báo.<br>3. Đọc danh sách đơn để kiểm tra không có đơn mới. |
| Expected Result | Không thanh toán được; có thông báo lỗi hoặc giải thích giỏ trống; không tạo đơn. |
| Actual Result | Theo người dùng thực thi Web: giỏ trống vẫn thanh toán 0 ₫ và hiển thị thanh toán thành công. Lịch sử đơn có đơn 0 ₫ không có sản phẩm. |
| Status | Fail |
| Evidence | [TC-B08-011.png](evidence/B08/TC-B08-011.png) |
| Bug ID / Issue URL | [BUG-B08-003](../bug-reports/bugs.md) |
| Cleanup | Ghi mã đơn thử nghiệm nếu có; dọn giỏ thử bằng thao tác được hỗ trợ. Nếu không dọn được thì dùng tài khoản thử nghiệm khác cho lần chạy sau. |

### TC-B08-012 — API từ chối thanh toán giỏ trống

| Trường | Nội dung |
|:---|:---|
| ID / Title | TC-B08-012; API từ chối thanh toán giỏ trống |
| Requirement / Purpose | Quyết định bổ sung của bạn: giỏ trống phải báo lỗi và không tạo đơn |
| Technique | Domain Testing + BVA |
| Domain / Boundary | C1, C4, C7; B1 off-point: 0 dòng sản phẩm; xem [phân tích](../analysis/B08_Checkout.md) |
| Preconditions / Setup | Đăng nhập bằng tài khoản có giỏ API trống; xác minh GET /api/cart trả danh sách rỗng. Ghi danh sách đơn trước test. |
| Test data | POST /api/checkout; token hợp lệ; body với total_amount=0. Tổng gửi lên khớp giỏ trống để chỉ kiểm tra lỗi giỏ trống. |
| Steps | 1. Kiểm tra giỏ API trống.<br>2. Gửi request thanh toán với total_amount=0.<br>3. Ghi response và thông báo lỗi.<br>4. Đọc danh sách đơn và so sánh mã đơn trước/sau. |
| Expected Result | Backend từ chối request, báo lỗi và không tạo đơn. |
| Actual Result | Postman CLI: GET /api/cart trước test trả []; checkout trả HTTP 200, message Checkout successful; tạo đơn ID 4 với total_amount=0. Không từ chối giỏ trống. |
| Status | Fail |
| Evidence | [Log CLI](evidence/B08/postman-cli.log); [response/snapshot JSON](evidence/B08/b08_api_results.json); [summary](evidence/B08/b08_api_execution_summary.md) |
| Bug ID / Issue URL | [BUG-B08-003](../bug-reports/bugs.md) |
| Cleanup | User/giỏ/đơn thử nghiệm nằm trong database tạm riêng; backend thử nghiệm được dừng sau khi lưu evidence. Không reset database workspace. |

Các mã lớp và biên trong dòng Domain / Boundary chỉ dùng liên kết với analysis. Người chạy có thể làm theo Preconditions, Test data, Steps và Expected Result mà không cần tra các mã này. Evidence thực tế đặt trong test-cases/evidence/B08/ rồi điền link vào card.
