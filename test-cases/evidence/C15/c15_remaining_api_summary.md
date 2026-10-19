# C15 — API còn lại

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

[Collection](c15_remaining_api_collection.json) · [CLI log](c15_remaining_api_cli.log) · [JSON trích từ log](c15_remaining_api_results.json). Không ghi JWT thật. API Pass không thay thế các Web TC013–015 Blocked.
