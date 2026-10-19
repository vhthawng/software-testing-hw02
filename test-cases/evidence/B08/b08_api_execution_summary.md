# B08 — Kết quả thực thi API bằng Postman CLI

- **Ngày:** 2026-10-18, Asia/Bangkok.
- **Công cụ:** Postman CLI 1.75.0; chạy collection local, không cần đăng nhập Postman Cloud.
- **Môi trường:** http://localhost:3000; bản sao nguyên mã `server.js` và `database.js`, database riêng trong thư mục tạm. Không khởi động backend gốc vì startup có thao tác xóa/tạo lại bảng.
- **Dữ liệu:** sản phẩm thử ID 6, giá catalog 100000 ₫; số lượng 2; tổng đúng 200000 ₫. Mỗi TC có user mới và giỏ riêng. TC012 có giỏ trống, tổng 0.
- **Log gốc:** [postman-cli.log](postman-cli.log). Không bật verbose; không ghi response login/JWT vào log.

## Kết quả

| Test case | Status | Actual | Bug |
|:---|:---|:---|:---|
| TC-B08-006 | Pass | Thiếu token: HTTP 401, error Unauthorized; không có đơn mới | — |
| TC-B08-007 | Pass | Token sai: HTTP 403, error Forbidden; không có đơn mới | — |
| TC-B08-008 | Fail | HTTP 200; tạo đơn ID 1 đúng 200000, nhưng GET /api/cart vẫn còn sản phẩm giá 100000 × 2 | BUG-B08-001 |
| TC-B08-009 | Fail | HTTP 200, Checkout successful; tạo đơn ID 2 lưu 199999 dù tổng giỏ là 200000 | BUG-B08-002 |
| TC-B08-010 | Fail | HTTP 200, Checkout successful; tạo đơn ID 3 lưu 200001 dù tổng giỏ là 200000 | BUG-B08-002 |
| TC-B08-012 | Fail | HTTP 200, Checkout successful; giỏ trống vẫn tạo đơn ID 4, total_amount=0 | BUG-B08-003 |

TC009/010 vi phạm FR-08 về backend tự tính tiền, đồng thời vi phạm quyết định bổ sung phải reject mismatch. TC012 được đánh Fail theo quyết định giỏ trống do người dùng xác nhận, không ghi nhầm là câu nguyên văn trong FR-08.

## Số liệu

- 57 requests; 67 assertions; 10 assertion failures; Postman exit code 1.
- Không có request transport failure, setup failure hoặc test-script error.
- 6 test case đã thực thi: **2 Pass, 4 Fail**. Số assertion thất bại không phải số test case thất bại.
- 6 test Web (001–005, 011) chưa chạy; không dùng API thay thế kết quả UI.

## Nguồn và giới hạn

Log CLI giữ console response/snapshot của lần chạy. Collection/JSON trích xuất cũ có trong Git tại commit `23b6586`. Không chạy lại hoặc thay đổi status khi dọn evidence.

Source hashes của bản sao đã chạy:

- server.js (SHA-256): `E2263811A1690A63A7DBB4446C3FD33FB87F50D073D9F8CDFAB7083A52F5E8ED`
- database.js (SHA-256): `50012F35C2CF0776DD837482D76ADF701F0EE40CDCB9D80143278FCCDE034717`

Database thử nghiệm riêng; backend thử nghiệm được dừng sau khi thu evidence. Database workspace không bị reset hoặc ghi bởi lần chạy này. Status Web cập nhật sau lượt API được lưu trong test case; đoạn Web chưa chạy bên trên chỉ mô tả thời điểm chạy API. Chưa có screenshot/GitHub Issue cho các bug API.
