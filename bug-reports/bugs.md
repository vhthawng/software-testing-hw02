# Bug reports

## Classification note

Rà soát 2026-10-19: TC-A04-002 (L=9) và TC-A04-004 (L=12) đổi từ Fail theo nhãn báo cáo ban đầu sang **Pass**, vì quan sát từ chối và không gửi request cập nhật phù hợp expected FR-04. Không gán bug cho hai test âm này; lịch sử báo cáo ban đầu giữ trong audit.

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

## BUG-C15-001 — Lưu được tên sản phẩm vượt 255 ký tự

| Trường | Nội dung |
|:---|:---|
| Trạng thái | Web do người dùng báo cáo; API sửa đã tái hiện bằng CLI; bug nháp chưa đăng Issue |
| Requirement | FR-15: tên sản phẩm tối đa 255 ký tự |
| Test cases | TC-C15-005 (Web tạo mới), TC-C15-031 (API sửa) |
| Môi trường | 2026-10-19; Web theo người dùng, backend http://localhost:3000; Postman CLI 1.75.0 |
| Preconditions / Data | Admin hợp lệ, giá 100000, danh mục có thật; name gồm 256 ASCII A cho API sửa |
| Steps | 1. Tạo sản phẩm thử riêng có tên hợp lệ.<br>2. PUT /api/products/<ID thử> bằng JWT Admin, full body chuẩn chỉ đổi name thành A lặp 256.<br>3. GET /api/products đọc lại name và đếm độ dài. |
| Expected | Không lưu tên dài 256; dữ liệu hợp lệ trước đó giữ nguyên |
| Actual | Web TC005: người dùng báo sản phẩm được tạo với tên đủ 256. CLI TC031: HTTP 200, ID 20 lưu đủ 256 ký tự |
| Evidence | [CLI log](../test-cases/evidence/C15/postman-cli.log), [JSON](../test-cases/evidence/C15/c15_backend_results.json), [summary](../test-cases/evidence/C15/c15_backend_recheck_summary.md); Web có báo cáo người dùng, chưa có ảnh C15 trong workspace |
| Ảnh hưởng | Dữ liệu sản phẩm vi phạm giới hạn tên; cả tạo Web và cập nhật API có đường lưu sai |
| Cleanup / Issue | Sản phẩm CLI đã dọn, catalog cuối khớp baseline; chưa có screenshot/GitHub Issue |

## BUG-C15-002 — Web tạo được sản phẩm khi giá để trống

| Trường | Nội dung |
|:---|:---|
| Trạng thái | Người dùng đã tái hiện Web; bug nháp, AI chưa tự chạy UI |
| Requirement | FR-15: giá bắt buộc |
| Test case | TC-C15-006 |
| Preconditions / Data | Admin đăng nhập, tên/danh mục hợp lệ; ô giá trống |
| Steps | 1. Mở form thêm sản phẩm.<br>2. Điền tên, danh mục hợp lệ và để giá trống.<br>3. Lưu sản phẩm, kiểm kết quả tạo. |
| Expected | Không tạo sản phẩm khi thiếu giá |
| Actual | Người dùng báo sản phẩm được tạo với giá để trống; không tự suy ra đã được chuyển thành số 0 |
| Evidence | Báo cáo trong [conversation log](../audit/conversation_log.md), Entry 019; chưa có ảnh TC006; không có CLI riêng cho giá trống trong lượt này |
| Ảnh hưởng | Bỏ qua trường bắt buộc giá; dữ liệu không đủ theo FR-15 |
| Screenshot / Issue | Chưa có ảnh, chưa đăng GitHub Issue |

## BUG-C15-003 — Lưu được giá không dương

| Trường | Nội dung |
|:---|:---|
| Trạng thái | Web do người dùng báo cáo; API cập nhật giá 0 đã tái hiện CLI; bug nháp |
| Requirement | FR-15: giá phải >0 |
| Test cases | TC-C15-007 (Web giá 0), TC-C15-009 (Web giá âm), TC-C15-032 (API sửa giá 0) |
| Môi trường | 2026-10-19; backend đang chạy localhost:3000; CLI 1.75.0; Web theo người dùng |
| Preconditions / Data | Admin; sản phẩm thử riêng hợp lệ giá 100000; chỉ đổi price=0 trong API |
| Steps | 1. PUT /api/products/<ID thử> bằng JWT Admin và full body chuẩn, price=0.<br>2. GET /api/products đọc lại giá.<br>3. Đường Web: tạo mới với giá 0 hoặc âm theo TC007/009. |
| Expected | Không tạo/sửa sản phẩm thành giá 0 hoặc âm |
| Actual | Người dùng báo tạo được giá 0 và giá âm trên Web. CLI TC032 trả HTTP 200, ID 21 lưu price=0 |
| Evidence | [CLI log](../test-cases/evidence/C15/postman-cli.log), [JSON](../test-cases/evidence/C15/c15_backend_results.json); chưa có ảnh Web C15 trong workspace |
| Ảnh hưởng | Giá sản phẩm vi phạm miền dương |
| Cleanup / Issue | Dọn đúng sản phẩm CLI; catalog ban đầu bảo toàn; chưa có screenshot/GitHub Issue |

## BUG-C15-004 — Nút Sửa trên Web Admin không mở được thao tác sửa

| Trường | Nội dung |
|:---|:---|
| Trạng thái | Theo báo cáo người dùng; chưa tự tái hiện UI; bug nháp |
| Requirement | FR-15: Admin được sửa sản phẩm |
| Test case / phụ thuộc | TC-C15-012 Fail; TC013/014/015 Blocked vì không tới được bước nhập dữ liệu sửa |
| Môi trường | Web Admin, báo cáo 2026-10-19; browser/build chưa cung cấp |
| Preconditions / Data | Admin đã đăng nhập, danh sách có sản phẩm thử để sửa |
| Steps | 1. Mở tab Sản phẩm.<br>2. Bấm Sửa trên dòng sản phẩm thử.<br>3. Quan sát thao tác/form sửa. |
| Expected | Mở/chọn đúng sản phẩm để Admin nhập thay đổi và lưu |
| Actual | Người dùng báo bấm Sửa không trigger event/thao tác mong đợi, không test tiếp được cập nhật trên UI |
| Đối chiếu backend | TC030 API sửa hợp lệ Pass, mọi ID khác không đổi; điều này không bác bỏ lỗi UI và không xác nhận hiển thị Web ngay sau lưu |
| Evidence | Báo cáo người dùng tại [conversation log](../audit/conversation_log.md), Entry 019; [API đối chiếu](../test-cases/evidence/C15/c15_backend_recheck_summary.md). Chưa có screenshot/video nút Sửa |
| Ảnh hưởng | Chặn đường sửa Web và các test validation khi sửa |
| Screenshot / Issue | Chưa có ảnh, chưa đăng GitHub Issue; không suy ra nguyên nhân code từ báo cáo UI |

## BUG-C15-005 — Backend cho tạo và sửa tên sản phẩm thành rỗng

| Trường | Nội dung |
|:---|:---|
| Trạng thái | Đã tái hiện Postman CLI; bug nháp |
| Requirement | FR-15: tên bắt buộc |
| Test cases | TC-C15-029 (POST), TC-C15-033 (PUT). TC002 Web chặn tên rỗng vẫn Pass tại FE |
| Môi trường | 2026-10-19; backend đang chạy localhost:3000; Postman CLI 1.75.0 |
| Preconditions / Data | Admin JWT hợp lệ; name="", price=100000, mô tả/ảnh chuẩn, category_id có thật |
| Steps | 1. Snapshot products trước test.<br>2. POST /api/products với full body có name="".<br>3. GET list kiểm sản phẩm mới.<br>4. Với sản phẩm thử riêng có tên hợp lệ, PUT full body chỉ đổi name="".<br>5. GET list kiểm name. |
| Expected | Không tạo hoặc lưu tên rỗng; dữ liệu trước thao tác giữ nguyên |
| Actual | POST trả 200 tạo ID 18 có name=""; PUT trả 200, ID 22 lưu name="" |
| Evidence | [CLI log](../test-cases/evidence/C15/postman-cli.log), [JSON](../test-cases/evidence/C15/c15_backend_results.json), [summary](../test-cases/evidence/C15/c15_backend_recheck_summary.md) |
| Ảnh hưởng | Có thể bỏ qua validation FE bằng API, lưu sản phẩm thiếu tên |
| Cleanup / Issue | Đã xóa các fixture CLI, so sánh cuối khớp catalog gốc; chưa có screenshot/GitHub Issue |

## BUG-C15-006 — Backend cho tạo sản phẩm thiếu danh mục hoặc tham chiếu danh mục không tồn tại

| Trường | Nội dung |
|---|---|
| Trạng thái | Đã tái hiện Postman CLI; bug nháp, chưa đăng Issue |
| Requirement | FR-15: danh mục bắt buộc và phải hợp lệ |
| Test cases | TC-C15-010, TC-C15-017 |
| Môi trường | 2026-10-19; localhost:3000; Postman CLI 1.75.0 |
| Preconditions / Data / Steps | Đăng nhập Admin; GET categories xác nhận 1/2/3 có thật và 4 không có. POST body chuẩn lần lượt bỏ category_id và đặt category_id=4; GET products kiểm dữ liệu lưu. |
| Expected | Từ chối và không ghi dữ liệu vi phạm; không ép mã HTTP/câu lỗi cụ thể |
| Actual | Cả hai HTTP 200; ID 23 lưu category_id=null; ID 24 lưu category_id=4. |
| Evidence | [Summary](../test-cases/evidence/C15/c15_remaining_api_summary.md), [CLI log](../test-cases/evidence/C15/c15_remaining_api_cli.log), [JSON](../test-cases/evidence/C15/c15_remaining_api_results.json) |
| Ảnh hưởng | Lưu sản phẩm thiếu hoặc sai liên kết danh mục. |
| Cleanup / Screenshot / Issue | Đã dọn fixture riêng; catalog 14 sản phẩm gốc giữ nguyên toàn bộ dữ liệu; chưa có screenshot hoặc GitHub Issue |

## BUG-C15-007 — API cho thêm, sửa, xóa sản phẩm khi không có quyền Admin

| Trường | Nội dung |
|---|---|
| Trạng thái | Đã tái hiện Postman CLI; bug nháp, chưa đăng Issue |
| Requirement | FR-12, SEC-02/03: chỉ Admin hợp lệ được ghi sản phẩm |
| Test cases | TC-C15-018–026 |
| Môi trường | 2026-10-19; localhost:3000; Postman CLI 1.75.0 |
| Preconditions / Data / Steps | Gọi POST/PUT/DELETE products với từng trường hợp không Authorization, token sai, JWT role=user hợp lệ. Mỗi lần dùng sản phẩm thử riêng; GET trước/sau kiểm dữ liệu. |
| Expected | Từ chối và không ghi dữ liệu vi phạm; không ép mã HTTP/câu lỗi cụ thể |
| Actual | Cả 9 thao tác trả HTTP 200 và có hiệu lực: POST tạo ID 25–27; PUT đổi tên ID 28–30; DELETE xóa ID 31–33. TC027 Admin CRUD Pass làm đối chứng. |
| Evidence | [Summary](../test-cases/evidence/C15/c15_remaining_api_summary.md), [CLI log](../test-cases/evidence/C15/c15_remaining_api_cli.log), [JSON](../test-cases/evidence/C15/c15_remaining_api_results.json) |
| Ảnh hưởng | Người không có quyền có thể thay đổi catalog sản phẩm. |
| Cleanup / Screenshot / Issue | Đã dọn fixture riêng; catalog 14 sản phẩm gốc giữ nguyên toàn bộ dữ liệu; chưa có screenshot hoặc GitHub Issue |
