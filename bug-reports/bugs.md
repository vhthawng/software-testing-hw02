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
