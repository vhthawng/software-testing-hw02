# A04 API execution summary — TC-A04-010 đến TC-A04-013

- **Công cụ:** Postman CLI v1.75.0
- **SUT:** `eshop-sut/backend`, `http://localhost:3000`
- **Ngày chạy:** 2026-10-18 (Asia/Bangkok)
- **Log CLI:** [postman-cli.log](postman-cli.log) — response token đã được redacted.

## Kết quả theo test case

| TC ID | Kết quả | Actual quan sát được | Evidence trong log |
|:---|:---|:---|:---|
| TC-A04-010 | **Pass** | PUT thiếu `Authorization` trả `401 Unauthorized` với `{"error":"Unauthorized"}`; GET bằng token U hợp lệ cho thấy snapshot U không đổi. | `postman-cli.log`, mục `TC-A04-010` |
| TC-A04-011 | **Pass** | PUT với `Bearer invalid.a04.token` trả `403 Forbidden` với `{"error":"Forbidden"}`; GET bằng token U hợp lệ cho thấy snapshot U không đổi. | `postman-cli.log`, mục `TC-A04-011` |
| TC-A04-012 | **Fail** | PUT thêm `role=admin` trả `200` và `{"message":"Profile updated"}`; GET sau đó trả `role: "admin"`, vi phạm invariant SEC-06. Collection cleanup đã khôi phục fixture U về `name=Test User`, `phone=null`, `shipping_address=null`, `role=user`. | `postman-cli.log`, mục `TC-A04-012` |
| TC-A04-013 | **Pass** | PUT bằng token U trả `200`; GET U cho thấy name/phone/shipping_address mới, email/role giữ nguyên; GET W giữ nguyên snapshot. | `postman-cli.log`, mục `TC-A04-013` |

## Thống kê lần chạy

- 14 requests, 14 test scripts, 15 assertions.
- 1 assertion failed: `TC-A04-012: stored role is not admin`.
- Kết quả test case duy nhất: **3 Pass, 1 Fail**.
- `--output` không được dùng vì Postman CLI yêu cầu đăng nhập Postman Cloud cho tùy chọn đó; CLI reporter tích hợp đã được lưu đầy đủ vào log trên.

## Ghi chú an toàn

Collection dùng tài khoản seed của SUT để chạy local. Token trong CLI log đã được thay bằng `<REDACTED>` trước khi lưu evidence.


Collection cũ có trong Git tại commit `23b6586`; thư mục evidence chỉ giữ summary, log CLI và ảnh. Backend stdout/stderr không còn trong workspace, không dùng làm evidence.
