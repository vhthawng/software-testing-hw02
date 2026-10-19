# C15 — Kiểm tra backend sau kết quả Web

- **Ngày:** 2026-10-19, Asia/Bangkok; Postman CLI 1.75.0.
- **Môi trường:** backend đang chạy ở http://localhost:3000. Không restart hoặc reset database; tạo sản phẩm thử riêng qua API, sau đó xóa đúng các ID thử.
- **Phạm vi:** 5 test API bổ sung TC029–033 theo yêu cầu người dùng; không thay thế status Web TC002/012–015 và không chạy 12 API cũ khác.
- **Evidence:** [collection](c15_backend_recheck_collection.json), [CLI log](postman-cli.log), [response và snapshot JSON](c15_backend_results.json).

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
- Các response before/action/after được trích từ console JSON trong log gốc; JSON chỉ ghép lại dòng CLI bị wrap, không phải một lượt chạy khác.

## Chạy lại

~~~powershell
postman.cmd collection run test-cases/evidence/C15/c15_backend_recheck_collection.json
~~~

Backend phải sẵn sàng và có tài khoản Admin seed dùng trong collection. Collection tạo fixture riêng; nếu setup thất bại sẽ dừng. Kiểm tra mục cleanup cuối trước khi kết luận dữ liệu gốc đã được giữ nguyên. Lần chạy này cleanup verification đã Pass. Nếu dừng giữa chừng, dùng ID sản phẩm thử trong log để dọn, không reset database.
