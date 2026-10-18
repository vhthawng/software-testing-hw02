# Tiến độ HW02

MSSV: 23120355. Chỉ tạo Markdown trong workspace; chưa tạo thư mục nộp, PDF hoặc ZIP.

| Feature | Yêu cầu | Domain | BVA | Thực thi | Review sinh viên / AI gap |
|---|---|---|---|---|---|
| A04 | Draft đã lưu (662f7ba) | Draft 24 TC (d218bb0 là bước Domain) | Draft 8 TC chung Domain/BVA | Chưa chạy | Chưa review |
| B08 | Chưa bắt đầu | Chưa làm | Chưa làm | Chưa chạy | Chưa review |
| C15 | Chưa bắt đầu | Chưa làm | Chưa làm | Chưa chạy | Chưa review |
| D Mobile Register | Chưa bắt đầu | Chưa làm | Chưa làm | Chưa chạy | Chưa review |

Artifact: [A04 analysis](analysis/A04_Profile.md). Commit yêu cầu: 662f7ba. Test case: [A04](test-cases/A04_Profile.md). Commit Domain: d218bb0. Commit BVA theo message `A04: add boundary analysis and complete draft test cases`.

Các thay đổi workflow về cấu trúc nộp bài đã tồn tại trước bước A04, chưa commit và không gộp vào commit tính năng. Bài giảng chưa có; Audit quá khứ chưa đầy đủ. Không có bug tái hiện/Issue/ảnh trong bước này.

## Trạng thái A04 sau thiết kế

24 test duy nhất: 16 chỉ Domain, 8 cả Domain/BVA; 6 hợp lệ và 18 không hợp lệ. Đã chạy 0; Not Run 24. Chưa có bug tái hiện. Bản thiết kế là Draft, cần sinh viên review; GUI/security bổ sung và các lớp spec chưa rõ được ghi trong analysis. Không đánh giá A04 đã hoàn tất. Audit có snapshot theo từng bước; chưa có verdict sinh viên hoặc đầy đủ log các phiên cũ.

AI self-review đã đồng bộ payload các case sửa riêng tên/địa chỉ/email với bước thực hiện; đây không phải phản hồi đã được sinh viên xác nhận. Công việc tiếp theo: review yêu cầu/miền/biên, bổ sung phần cần thiết rồi thiết lập môi trường để chạy test và thu ảnh.
