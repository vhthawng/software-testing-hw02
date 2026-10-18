# Bug reports

## Classification note

The execution report marks TC-A04-002 (L=9) and TC-A04-004 (L=12) as **Fail**. Their observed rejection and absence of an update request match the current FR-04 expected result for invalid phone lengths, so no product bug is assigned to those two cases until the student confirms the intended status classification.

## BUG-A04-001 — `/api/users/me` cho phép client tự đổi `role`

| Trường | Nội dung |
|:---|:---|
| ID / trạng thái | BUG-A04-001 — Draft, chưa đăng GitHub Issue |
| Requirement | SEC-06: client không được đổi trường `role` qua cập nhật hồ sơ |
| Test case | [TC-A04-012](../test-cases/A04_Profile.md#tc-a04-012--api-không-cho-client-đổi-role) |
| Môi trường | EShop SUT backend local, `http://localhost:3000`; Postman CLI v1.75.0; 2026-10-18 |
| Preconditions | Tài khoản U (`role=user`) đăng nhập thành công, có JWT hợp lệ |
| Dữ liệu | `PUT /api/users/me`; body gồm các trường hồ sơ hợp lệ và thêm `role: "admin"` |
| Expected | Request bị từ chối hoặc trường `role` bị bỏ qua; sau đó role của U vẫn là `user` |
| Actual | PUT trả `200` với `{"message":"Profile updated"}`. GET `/api/users/me` sau đó trả `role: "admin"`. |
| Ảnh hưởng | Vi phạm SEC-06 và cho phép user tự nâng quyền; cần ưu tiên xử lý trước khi phát hành. |
| Bằng chứng | [a04_api_execution_summary.md](../test-cases/evidence/A04/a04_api_execution_summary.md), [postman-cli.log](../test-cases/evidence/A04/postman-cli.log) |
| Screenshot / Issue | Chưa có screenshot UI; chưa đăng GitHub Issue. CLI log là bằng chứng thực thi API hiện có. |
| Cleanup | Collection đã PUT lại fixture U thành `name=Test User`, `phone=null`, `shipping_address=null`, `role=user`; test sau đó không dựa trên trạng thái bị sửa. |


## BUG-A04-002 — Web UI rejects valid phone lengths

| Trường | Nội dung |
|:---|:---|
| ID / trạng thái | BUG-A04-002 — Draft, chưa đăng GitHub Issue |
| Requirement | FR-04: phone bắt đầu bằng `0`, dài 10–11 chữ số |
| Test case | TC-A04-001 (L=10), TC-A04-003 (L=11) |
| Môi trường | EShop Web `/profile`; 2026-10-18 |
| Dữ liệu | `0912345678` và `09123456789` |
| Expected | UI chấp nhận giá trị hợp lệ, gửi cập nhật và đọc lại đúng phone |
| Actual | UI hiển thị `Số điện thoại không hợp lệ. Vui lòng nhập đúng 9-10 chữ số.`; không có request cập nhật xuống API |
| Ảnh hưởng | Người dùng không thể lưu hai giá trị phone hợp lệ tại các biên FR-04 |
| Bằng chứng | [TC-A04-001.png](../test-cases/evidence/A04/TC-A04-001.png), [TC-A04-003.png](../test-cases/evidence/A04/TC-A04-003.png) |
| Screenshot / Issue | Có ảnh local; chưa đăng GitHub Issue |

## BUG-A04-003 — Web UI chấp nhận phone không bắt đầu bằng 0

| Trường | Nội dung |
|:---|:---|
| ID / trạng thái | BUG-A04-003 — Draft, chưa đăng GitHub Issue |
| Requirement | FR-04: phone phải bắt đầu bằng `0` |
| Test case | [TC-A04-005](../test-cases/A04_Profile.md#tc-a04-005--phone-không-bắt-đầu-bằng-0) |
| Môi trường | EShop Web `/profile`; 2026-10-18 |
| Dữ liệu | `1912345678` |
| Expected | UI từ chối, không gửi cập nhật và không lưu giá trị không hợp lệ |
| Actual | UI hiển thị `Cập nhật thành công`; có request cập nhật backend nhưng không có POST request riêng theo quan sát; refresh vẫn giữ `1912345678` |
| Ảnh hưởng | Giá trị phone vi phạm prefix của FR-04 được lưu vào hồ sơ |
| Bằng chứng | [TC-A04-005.png](../test-cases/evidence/A04/TC-A04-005.png) |
| Screenshot / Issue | Có ảnh local; chưa đăng GitHub Issue |

## BUG-B08-001 — API không xóa giỏ sau thanh toán thành công

| Trường | Nội dung |
|:---|:---|
| Trạng thái | Đã tái hiện bằng Postman CLI; bug Markdown nháp, chưa đăng GitHub Issue |
| Requirement | FR-08: sau thanh toán thành công, giỏ hàng được xóa |
| Test case | TC-B08-008 trong [B08 test cases](../test-cases/B08_Checkout.md) |
| Môi trường | 2026-10-18; Postman CLI 1.75.0; localhost:3000; backend nguyên mã, database thử nghiệm riêng |
| Preconditions / Data | User thử nghiệm đăng nhập, giỏ API có đúng một sản phẩm giá 100000, quantity=2; tổng 200000 |
| Steps | 1. GET /api/cart xác minh giỏ.<br>2. POST /api/checkout với JWT hợp lệ, body {"total_amount":200000,"shipping_address":"123 Le Loi, TP.HCM"}.<br>3. GET /api/orders/my-orders và GET /api/cart bằng token đó. |
| Expected | Tạo đơn đúng 200000; giỏ API rỗng sau thành công |
| Actual | HTTP 200, tạo đơn ID 1 đúng 200000; GET /api/cart vẫn trả sản phẩm giá 100000 × 2 |
| Ảnh hưởng | Giỏ cũ còn sau checkout, người dùng có thể nhầm chưa đặt hoặc đặt lại các sản phẩm đó |
| Evidence | [CLI log](../test-cases/evidence/B08/postman-cli.log), [JSON snapshot](../test-cases/evidence/B08/b08_api_results.json), [summary](../test-cases/evidence/B08/b08_api_execution_summary.md) |
| Screenshot / Issue | Chưa có screenshot, chưa đăng GitHub Issue |
| Cleanup | Dữ liệu nằm trong database tạm; dừng backend thử nghiệm sau khi lưu evidence |

## BUG-B08-002 — API lưu tổng tiền do client sửa thấp/cao hơn giỏ

| Trường | Nội dung |
|:---|:---|
| Trạng thái | Đã tái hiện bằng Postman CLI; bug Markdown nháp, chưa đăng GitHub Issue |
| Requirement | FR-08: backend tự tính tổng từ giỏ, không tin client total; bổ sung do người dùng xác nhận: mismatch phải reject, báo lỗi, không tạo đơn |
| Test cases | TC-B08-009 và TC-B08-010 trong [B08 test cases](../test-cases/B08_Checkout.md) |
| Môi trường | 2026-10-18; Postman CLI 1.75.0; localhost:3000; backend nguyên mã, database riêng |
| Preconditions / Data | Mỗi TC dùng user mới, JWT hợp lệ; giỏ riêng có một sản phẩm giá catalog 100000 × 2, tổng đúng 200000; snapshot orders ban đầu rỗng |
| Steps | 1. GET /api/cart xác minh giỏ và tự tính 200000.<br>2. POST /api/checkout với JWT hợp lệ, shipping_address="123 Le Loi, TP.HCM", total_amount=199999.<br>3. GET /api/orders/my-orders kiểm tiền lưu.<br>4. Với user/giỏ riêng khác, lặp lại gửi total_amount=200001. |
| Expected | Mismatch bị từ chối, có lỗi, không tạo đơn; backend không lưu tiền giả mạo |
| Actual | Cả hai trả HTTP 200 với Checkout successful. TC009 tạo đơn ID 2 lưu 199999; TC010 tạo đơn ID 3 lưu 200001; cả hai khác tổng giỏ 200000 |
| Ảnh hưởng | Tính toàn vẹn tiền đơn hàng bị vi phạm; client có thể làm số tiền lưu thấp hoặc cao hơn tổng giỏ |
| Evidence | [CLI log](../test-cases/evidence/B08/postman-cli.log), [JSON snapshot](../test-cases/evidence/B08/b08_api_results.json), [summary](../test-cases/evidence/B08/b08_api_execution_summary.md) |
| Screenshot / Issue | Chưa có screenshot, chưa đăng GitHub Issue |
| Cleanup | Tài khoản/đơn nằm trong database tạm; dừng backend thử nghiệm sau khi lưu evidence |

## BUG-B08-003 — Thanh toán giỏ trống vẫn tạo đơn (API và Web)

| Trường | Nội dung |
|:---|:---|
| Trạng thái | Đã tái hiện bằng Postman CLI; bug Markdown nháp, chưa đăng GitHub Issue |
| Requirement | Quyết định bổ sung giỏ trống do người dùng xác nhận: phải từ chối, báo lỗi, không tạo đơn; FR-08 gốc chưa quy định riêng trường hợp này |
| Test case | TC-B08-011 (Web) và TC-B08-012 (API) trong [B08 test cases](../test-cases/B08_Checkout.md) |
| Môi trường | 2026-10-18; Postman CLI 1.75.0; localhost:3000; backend nguyên mã, database riêng |
| Preconditions / Data | User mới có JWT hợp lệ; GET /api/cart trả []; orders trước test rỗng. Total gửi 0 khớp tổng giỏ trống, chỉ vi phạm điều kiện giỏ không trống |
| Steps | 1. Xác minh giỏ trống.<br>2. POST /api/checkout với JWT hợp lệ và body {"total_amount":0,"shipping_address":"123 Le Loi, TP.HCM"}.<br>3. GET /api/orders/my-orders. |
| Expected | Từ chối request, có lỗi và không tạo đơn |
| Actual | HTTP 200 với Checkout successful, tạo đơn ID 4 có total_amount=0 |
| Ảnh hưởng | Phát sinh đơn không có sản phẩm và tổng tiền 0 trái policy đã chốt |
| Evidence | [CLI log](../test-cases/evidence/B08/postman-cli.log), [JSON snapshot](../test-cases/evidence/B08/b08_api_results.json), [summary](../test-cases/evidence/B08/b08_api_execution_summary.md) |
| Screenshot / Issue | Có ảnh Web [TC-B08-011.png](../test-cases/evidence/B08/TC-B08-011.png); chưa có screenshot API, chưa đăng GitHub Issue |
| Cleanup | Đơn thử nằm trong database tạm; dừng backend thử nghiệm sau khi lưu evidence |

**Bổ sung Web cho BUG-B08-003 (người dùng thực thi, 2026-10-18):** giỏ không có sản phẩm, xác nhận thanh toán vẫn hiển thị thành công; lịch sử có đơn 0 ₫ không có sản phẩm. Expected: chặn, báo lỗi và không tạo đơn theo policy đã chốt. Evidence: [TC-B08-011.png](../test-cases/evidence/B08/TC-B08-011.png). Đây là bổ sung đường tái hiện Web cho cùng yêu cầu; chưa suy ra hai nền tảng có chung nguyên nhân.

## BUG-B08-004 — Giỏ Web còn sản phẩm sau thanh toán thành công

| Trường | Nội dung |
|:---|:---|
| Trạng thái | Người dùng đã tái hiện Web; bug Markdown nháp, chưa đăng GitHub Issue |
| Requirement | FR-08: sau thanh toán thành công, giỏ hàng được xóa |
| Test case | TC-B08-002 trong [B08 test cases](../test-cases/B08_Checkout.md) |
| Môi trường | Web EShop; kết quả người dùng cung cấp 2026-10-18; chưa có build/browser cụ thể |
| Preconditions / Data | Đã đăng nhập; sản phẩm 100000 ₫ × 2 và sản phẩm khác 50000 ₫ × 3; tổng 350000 ₫ |
| Steps | 1. Mở checkout và xác nhận thanh toán.<br>2. Kiểm đơn mới lưu 350000 ₫.<br>3. Quay lại giỏ trong cùng phiên. |
| Expected | Đơn đúng 350000 ₫; giỏ không còn sản phẩm sau thành công |
| Actual | Thanh toán thành công, đơn mới đúng 350000 ₫; giỏ vẫn còn sản phẩm theo báo cáo người dùng |
| Evidence | Báo cáo thực thi trong [conversation log](../audit/conversation_log.md), Entry 016; chưa có ảnh TC-B08-002 trong workspace |
| Ảnh hưởng | Giỏ Web giữ lại hàng đã thanh toán, có thể khiến người dùng đặt lặp |
| Liên quan | BUG-B08-001 là đường API cùng triệu chứng; giữ riêng vì chưa xác nhận nguyên nhân chung |
| Screenshot / Issue | Chưa có screenshot, chưa đăng GitHub Issue |

## BUG-B08-005 — Web cho sửa trực tiếp tổng tiền thanh toán

| Trường | Nội dung |
|:---|:---|
| Trạng thái | Người dùng đã tái hiện Web; bug Markdown nháp, chưa đăng GitHub Issue |
| Requirement | FR-08: tổng tiền được tính tự động và không cho sửa trực tiếp |
| Test case | TC-B08-004 trong [B08 test cases](../test-cases/B08_Checkout.md) |
| Môi trường | Web EShop; kết quả người dùng cung cấp 2026-10-18; chưa có build/browser cụ thể |
| Preconditions / Data | Đã đăng nhập; giỏ có sản phẩm 100000 ₫ × 2, tổng 200000 ₫ |
| Steps | 1. Mở trang thanh toán.<br>2. Thử nhập hoặc sửa trực tiếp vùng tổng tiền bằng thao tác UI thông thường. |
| Expected | Không sửa trực tiếp được tổng tiền |
| Actual | Người dùng sửa được tổng tiền từ giao diện; chưa ghi giá trị sau sửa cụ thể vì báo cáo chưa nêu |
| Evidence | [TC-B08-004.png](../test-cases/evidence/B08/TC-B08-004.png) |
| Ảnh hưởng | Tổng tiền trên giao diện bị người dùng thay đổi trái FR-08; backend còn có BUG-B08-002 riêng |
| Screenshot / Issue | Có ảnh Web local; chưa đăng GitHub Issue |

**Quan sát phụ thuộc của TC-B08-005:** người dùng không đổi số lượng ngay trong giỏ được; thêm lại cùng sản phẩm từ trang chính tạo hai dòng ×2 và ×1, checkout vẫn cập nhật tổng. Điều khiển số lượng và gộp sản phẩm thuộc FR-07; ghi nhận để theo dõi, chưa đếm thành bug B08 hoặc gán Fail cho phép tính tổng B08. Test theo bước gốc hiện Blocked.
