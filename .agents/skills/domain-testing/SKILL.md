---
name: domain-testing
description: Thiết kế hoặc review test case bằng Domain Testing, Equivalence Partitioning và Boundary Value Analysis cho form, API và quy tắc có ngưỡng. Xuất phân tích từng bước, ma trận truy vết và test case có thể thực thi.
---

# Domain Testing

Thiết kế test có căn cứ từ đặc tả, ưu tiên độ phủ và khả năng chẩn đoán. Mặc định dùng tiếng Việt và giữ thuật ngữ tiếng Anh ở lần xuất hiện đầu tiên.

## Căn cứ xác định hành vi đúng

- Spec/SRS và sửa đổi yêu cầu được người dùng xác nhận là nguồn cho expected result. Với EShop HW02, dùng `eshop-sut/README.md`, gồm cả quy tắc chung và yêu cầu liên quan.
- API docs giúp xác định cách gọi; code, DB và UI hỗ trợ thực thi và nhận diện sai lệch. Không lấy validation trong code làm quy tắc đúng, không sửa expected để khớp implementation.
- Spec rõ nhưng code/UI khác: ghi dấu hiệu lỗi và thiết kế test xác nhận. Đây không phải mâu thuẫn yêu cầu cần chờ chọn một bên. Đọc code chưa phải thực thi hoặc bằng chứng bug đã tái hiện.
- Hai nguồn yêu cầu mâu thuẫn hoặc spec thiếu: ghi nguồn, giả định/câu hỏi và test bị ảnh hưởng. Tiếp tục phần đã rõ. Không kết luận bug chắc chắn chỉ dựa trên giả định chưa xác nhận.
- Không tự thêm max length, giới hạn giá, độ chính xác, trim hoặc message nguyên văn từ thói quen hay implementation. Phân biệt explicit, suy ra có lập luận và giả định.

## Cách làm và output

Lưu phân tích và ma trận nháp vào workspace để review, không chỉ giữ trong chat. Tiếp nhận sửa đổi, giữ ID ổn định và ghi thay đổi. Không buộc xác nhận nhiều lần nếu đã được giao xuất report; ghi rõ phần chưa được review.

Đọc [report-template.md](references/report-template.md) khi tạo/cập nhật output. Mặc định mỗi feature có `analysis.md` và `test-cases.md` trong thư mục được chọn hoặc `features/<feature-id>/`. Có thể gộp nhưng phải giữ đủ phân tích từng bước, truy vết và card. Trả link file thực tế trong workspace.

## Quy trình

### 0. Đọc nguồn, xác định phạm vi

Đọc spec tính năng, yêu cầu chung, phân quyền và các tham chiếu liên quan. Ghi nguồn đã đọc/chưa có, phiên bản nếu biết và ưu tiên nguồn. Nếu có bài giảng, dùng đúng thuật ngữ/quy trình; nếu chưa có thì ghi chưa đối chiếu, không tuyên bố phù hợp hoàn toàn. Không bịa mã yêu cầu.

### 1. Trích xuất biến và quy tắc

Lập bảng đầu vào, đầu ra, trạng thái và dữ liệu nền: kiểu, đơn vị, ràng buộc, nguồn (tài liệu + mã/mục). Bao gồm token, role, ownership và tác dụng phụ nếu trong phạm vi. Phân biệt UI và API; thuộc tính HTML không áp dụng nguyên văn cho Mobile React Native.

### 2. Giả định và điểm chưa rõ

Bảng: `Mã | Nội dung | Nguồn | Trạng thái | TC bị ảnh hưởng`. Trạng thái: Đề xuất, Chưa rõ, Mâu thuẫn yêu cầu, Đã xác nhận. Quy tắc rõ trong spec ở bảng yêu cầu, không gọi là giả định. Giữ mã/lịch sử khi cập nhật. Hỏi phần thực sự cần, tiếp tục việc độc lập.

### 3. Phân vùng đầu vào

Mỗi lớp có ID duy nhất trong feature, biến, điều kiện, valid/invalid/chưa rõ, đại diện và nguồn. Chọn đại diện mỗi lớp đạt tới được. Đọc [domain-types.md](references/domain-types.md) khi có chuỗi, enum, ngày, tập hợp hoặc upload; không áp dụng máy móc các lớp mẫu khi spec không quy định.

### 4. Phân vùng đầu ra

Xác định kết quả tính toán, thông báo, trạng thái, dữ liệu lưu và tác dụng phụ theo spec. Truy ngược về đầu vào/dữ liệu nền để đạt mỗi lớp. Tính expected bằng công thức cụ thể; không dùng kết quả SUT làm oracle cho chính nó. Đầu ra bị cấm kiểm tra bằng việc ngăn hành vi và giữ dữ liệu đúng, không gán độ phủ như đã tạo ra đầu ra bị cấm.

### 5. Boundary Value Analysis

Đọc [boundary-rules.md](references/boundary-rules.md). Mỗi biên có ID, nguồn, biến/quan hệ, giá trị đóng/mở, độ mịn, on/off-point và lựa chọn 2-point/3-point. Ưu tiên bài giảng/người dùng; nếu thiếu, nêu quy ước và lý do, không coi mặc định của skill là yêu cầu môn học. Enum không thứ tự không có biên số.

Biên phụ thuộc: chọn dữ liệu nền, tính biên cụ thể rồi chọn điểm. Ghi cách đếm độ dài khi Unicode gây khác biệt. Không tự coi giá là số nguyên hoặc bước nhỏ nhất là 1 đồng. Với checkout, tổng tiền đúng phải tính từ giỏ hàng; không suy ra mọi client total dương đều hợp lệ hay tự đặt max total.

### 6. Ma trận và độ phủ

- Bắt đầu nominal hợp lệ. Test âm ưu tiên một điều kiện sai (Single Fault Assumption), còn lại hợp lệ; giải thích ngoại lệ do quan hệ phụ thuộc.
- Có thể gộp lớp hợp lệ khi từng kết quả quan sát được. Nhiều biên độc lập nên đổi từng biến quanh nominal; nếu gộp phải giải thích.
- Loại trùng nhưng không hy sinh độ phủ; không khẳng định bộ test tối thiểu toàn cục khi chưa chứng minh.
- Test bổ sung về trạng thái, quyền, persistence hoặc tương tác ghi đúng kỹ thuật; không gắn BVA khi không có biên.
- Matrix: `TC ID | Requirement | Technique | Dữ liệu đầy đủ | Class IDs | Boundary/điểm | Validity | Expected`.
- Truy vết yêu cầu/lớp/điểm biên tới TC; chỉ gán lớp thật sự được kiểm tra. Ghi phần chưa phủ/không đạt tới được và lý do. Test thuộc cả Domain và BVA chỉ tính một lần.

### 7. Xuất và review

Lưu giải thích từng bước vào analysis, bảng tổng hợp/card vào test-cases; liên kết hai chiều. Phân biệt phần Domain và BVA dù dùng chung test. Giữ expected tách actual; chỉ ghi thực thi khi có kết quả thật, ghi nguồn nếu do người dùng cung cấp.

## Checklist

- Quy tắc có nguồn, spec làm oracle; giả định không biến thành yêu cầu.
- Phân tích Domain/BVA được lưu file và giải thích cách chọn lớp/điểm.
- Lớp đạt tới được và điểm biên trong phạm vi có test hoặc ghi rõ thiếu sót.
- Expected tính đúng, on/off-point đúng phía, độ mịn có căn cứ.
- Matrix, summary, card, truy vết khớp ID/số lượng; link hoạt động.
- Chưa chạy giữ Not Run; không tự tạo actual, bằng chứng hoặc Pass/Fail.

## Giới hạn

EP/BVA không bảo đảm phát hiện mọi lỗi trong lớp, tương tác hoặc trình tự. Nêu kỹ thuật bổ sung phù hợp, không dùng giới hạn này để bỏ qua yêu cầu chức năng trong phạm vi.
