# C15 — Kết quả thực thi API

17 API: **2 Pass, 15 Fail**, qua hai lượt chạy ngày 2026-10-19. [Log CLI đầy đủ](postman-cli.log) giữ nguyên nội dung từng lượt, ngăn bằng tiêu đề LƯỢT 1/LƯỢT 2. Không chạy lại khi gộp.

## Lượt 1 — Kiểm backend sau kết quả Web

- **Ngày:** 2026-10-19, Asia/Bangkok; Postman CLI 1.75.0.
- **Môi trường:** backend đang chạy ở http://localhost:3000. Không restart hoặc reset database; tạo sản phẩm thử riêng qua API, sau đó xóa đúng các ID thử.
- **Phạm vi:** 5 test API bổ sung TC029–033 theo yêu cầu người dùng; không thay thế status Web TC002/012–015 và không chạy 12 API cũ khác.

| Test API mới | Liên quan Web | Status | Actual |
|:---|:---|:---|:---|
| TC-C15-029 | TC002 | Fail | POST tên rỗng trả 200; sản phẩm mới có name="" được lưu |
| TC-C15-030 | TC012 | Pass | PUT tên hợp lệ trả 200, đổi đúng sản phẩm; mọi sản phẩm khác giữ nguyên |
| TC-C15-031 | TC013 | Fail | PUT tên A lặp 256 trả 200; lưu đủ 256 ký tự |
| TC-C15-032 | TC014 | Fail | PUT giá 0 trả 200; giá 0 được lưu |
| TC-C15-033 | TC015 | Fail | PUT tên rỗng trả 200; name="" được lưu |

**TC002 Web Pass không có nghĩa backend có validation:** trình duyệt chặn trường tên rỗng; TC029 gọi thẳng API đã chứng minh backend vẫn lưu tên rỗng. **TC030 API Pass không chữa lỗi nút Sửa:** không xác minh được hiển thị/interaction của UI qua Postman, nên TC012 vẫn Fail, TC013–015 vẫn Blocked trên Web.

## Số liệu và cleanup

- 28 requests, 34 assertions, 4 assertion failures; exit code 1 do test nghiệp vụ Fail.
- Setup và cleanup assertions đều đạt; không có transport failure/script error.
- 5 sản phẩm thử (ID 18–22) đã được xóa. GET /api/products cuối có 14 sản phẩm, so sánh toàn bộ field theo ID khớp baseline trước chạy.
- Không ghi token/session thật vào evidence; không bật verbose hoặc log response login.
- Response/snapshot before/action/after nằm trong console JSON của log gốc.


## Lượt 2 — 12 API còn lại

Ngày 2026-10-19 (Asia/Bangkok); Postman CLI 1.75.0; backend đang chạy tại localhost:3000. Không restart/reset database.

12 test: **1 Pass, 11 Fail**. 60 requests, 77 assertions, 20 assertion failures; exit code 1 do kiểm tra nghiệp vụ thất bại, không có lỗi transport/script. Số assertion fail không phải số test fail.

| Test | Status | Quan sát |
|---|---|---|
| TC-C15-010 | Fail | Postman CLI 2026-10-19: POST, categoryMissing, HTTP 200; GET kiểm chứng vẫn tạo sản phẩm có category_id=null. Vi phạm expected từ chối và không ghi dữ liệu. |
| TC-C15-017 | Fail | Postman CLI 2026-10-19: POST, categoryInvalid, HTTP 200; GET kiểm chứng vẫn tạo sản phẩm có category_id=4 dù GET categories chỉ có 1, 2, 3. Vi phạm expected từ chối và không ghi dữ liệu. |
| TC-C15-018 | Fail | Postman CLI 2026-10-19: POST, không gửi Authorization, HTTP 200; GET kiểm chứng vẫn tạo sản phẩm ID 25. Vi phạm expected từ chối và không ghi dữ liệu. |
| TC-C15-019 | Fail | Postman CLI 2026-10-19: POST, token sai, HTTP 200; GET kiểm chứng vẫn tạo sản phẩm ID 26. Vi phạm expected từ chối và không ghi dữ liệu. |
| TC-C15-020 | Fail | Postman CLI 2026-10-19: POST, JWT hợp lệ của role=user, HTTP 200; GET kiểm chứng vẫn tạo sản phẩm ID 27. Vi phạm expected từ chối và không ghi dữ liệu. |
| TC-C15-021 | Fail | Postman CLI 2026-10-19: PUT, không gửi Authorization, HTTP 200; GET kiểm chứng ID 28 lưu tên C15 Unauthorized Update. Vi phạm expected từ chối và không ghi dữ liệu. |
| TC-C15-022 | Fail | Postman CLI 2026-10-19: PUT, token sai, HTTP 200; GET kiểm chứng ID 29 lưu tên C15 Unauthorized Update. Vi phạm expected từ chối và không ghi dữ liệu. |
| TC-C15-023 | Fail | Postman CLI 2026-10-19: PUT, JWT hợp lệ của role=user, HTTP 200; GET kiểm chứng ID 30 lưu tên C15 Unauthorized Update. Vi phạm expected từ chối và không ghi dữ liệu. |
| TC-C15-024 | Fail | Postman CLI 2026-10-19: DELETE, không gửi Authorization, HTTP 200; GET kiểm chứng ID 31 đã bị xóa, GET không còn sản phẩm đích. Vi phạm expected từ chối và không ghi dữ liệu. |
| TC-C15-025 | Fail | Postman CLI 2026-10-19: DELETE, token sai, HTTP 200; GET kiểm chứng ID 32 đã bị xóa, GET không còn sản phẩm đích. Vi phạm expected từ chối và không ghi dữ liệu. |
| TC-C15-026 | Fail | Postman CLI 2026-10-19: DELETE, JWT hợp lệ của role=user, HTTP 200; GET kiểm chứng ID 33 đã bị xóa, GET không còn sản phẩm đích. Vi phạm expected từ chối và không ghi dữ liệu. |
| TC-C15-027 | Pass | Postman CLI 2026-10-19: JWT Admin hợp lệ tạo ID 34, đọc lại đúng dữ liệu; PUT đổi tên đúng sản phẩm, các sản phẩm khác giữ nguyên; DELETE thành công và GET không còn ID 34. Catalog trở về đúng baseline. |

Danh mục thật: 1, 2, 3; kiểm ID 4 không tồn tại trước TC017. Đã xác nhận login Admin và user thường đúng role trước kiểm quyền. Chỉ thao tác sản phẩm fixture riêng; dọn ID 23–34. Catalog cuối khớp toàn bộ 14 sản phẩm baseline theo ID và các trường dữ liệu; không sửa sản phẩm gốc.

API Pass không thay thế các Web TC013–015 Blocked.


## Bản lưu phục vụ chạy lại

Collection và JSON trích từ log đã bỏ khỏi evidence để giảm file trùng. Có thể xem/khôi phục bản cũ bằng Git tại commit `23b6586`; tên file gốc được ghi trong lịch sử audit.
