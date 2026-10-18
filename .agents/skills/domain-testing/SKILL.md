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
- Hai nguồn yêu cầu mâu thuẫn: ghi rõ mâu thuẫn và test bị ảnh hưởng. Khi spec không mô tả một quy tắc, coi đó là **spec gap/ngoài phạm vi**, không biến nó thành assumption, lớp hợp lệ/không hợp lệ hoặc expected result. Chỉ ghi assumption khi người dùng hoặc nhóm thực sự chốt một cách hiểu để tiếp tục.
- Không tự thêm max length, giới hạn giá, độ chính xác, trim hoặc message nguyên văn từ thói quen hay implementation. Phân biệt explicit, suy ra có lập luận và giả định.

## Cách làm và output

Lưu phân tích và ma trận nháp vào workspace để review, không chỉ giữ trong chat. Tiếp nhận sửa đổi, giữ ID ổn định và ghi thay đổi. Không buộc xác nhận nhiều lần nếu đã được giao xuất report; ghi rõ phần chưa được review.

Đọc [report-template.md](references/report-template.md) khi tạo/cập nhật output. Mặc định mỗi feature có `analysis.md` và `test-cases.md` trong thư mục được chọn hoặc `features/<feature-id>/`. Có thể gộp nhưng phải giữ đủ phân tích từng bước, truy vết và card. Trả link file thực tế trong workspace.

## Quy trình

### 0. Đọc nguồn, xác định phạm vi

Đọc spec tính năng, yêu cầu chung, phân quyền và các tham chiếu liên quan. Ghi nguồn đã đọc/chưa có, phiên bản nếu biết và ưu tiên nguồn. Nếu có bài giảng, dùng đúng thuật ngữ/quy trình; nếu chưa có thì ghi chưa đối chiếu, không tuyên bố phù hợp hoàn toàn. Không bịa mã yêu cầu.

### 1. Trích xuất input, context và quy tắc

Lập bảng **biến đầu vào** và **context** dùng để chọn miền: kiểu, đơn vị, ràng buộc, nguồn (tài liệu + mã/mục). Đầu vào gồm field người dùng, payload được spec mô tả và điều kiện phiên/quyền khi chúng là yêu cầu. Không biến persistence, response, snapshot, message hoặc “dữ liệu trước/sau” thành input variable.

Ghi riêng các **observable output/postcondition** như kết quả thành công, trạng thái lưu, trường không đổi hoặc hành vi bị chặn. Chúng là oracle của test; chỉ mô hình hóa chúng thành output domain khi spec nêu rõ các lớp, ngưỡng hoặc trạng thái đầu ra. Phân biệt UI và API; thuộc tính HTML không áp dụng nguyên văn cho Mobile React Native.

### 2. Spec gap, mâu thuẫn và quyết định phương pháp

Bảng: `Mã | Nội dung | Nguồn | Loại | Trạng thái | TC bị ảnh hưởng`. Loại gồm `Spec gap`, `Mâu thuẫn yêu cầu` và `Quyết định phương pháp`; không gọi một quy tắc bị bỏ trống là assumption. Với spec gap, ghi “không thiết kế expected cho đến khi được chốt” hoặc đưa ra ngoài phạm vi. Chỉ dùng `Đã xác nhận` sau khi người dùng/nhóm xác nhận. Giữ mã/lịch sử khi cập nhật; tiếp tục phần độc lập.

### 3. Phân vùng input/context

Chỉ lập equivalence class cho input/context có predicate hoặc danh sách giá trị từ spec. Mỗi lớp có ID duy nhất, điều kiện, valid/invalid, đại diện và nguồn. Không tạo class cho “rỗng/null/trim/max/Unicode” nếu spec không nói cách xử lý. Đọc [domain-types.md](references/domain-types.md) khi cần, nhưng không áp dụng máy móc các lớp mẫu.

### 4. Oracle và output domain (chỉ khi spec mô tả)

Ghi observable output/postcondition trong expected: ví dụ “cập nhật được lưu”, “email vẫn giữ nguyên” hoặc “hồ sơ khác không bị thay đổi”, nếu đó là hệ quả trực tiếp của yêu cầu. Chỉ chia output thành class khi spec định nghĩa các output khác nhau, ngưỡng hoặc state machine. Nếu spec chỉ yêu cầu một invariant, không tạo output class giả và không đưa invariant vào bảng input.

Truy ngược input để đạt output class chỉ khi class đó tồn tại trong spec. Tính expected bằng công thức cụ thể; không dùng kết quả SUT làm oracle cho chính nó. Đầu ra bị cấm được kiểm tra bằng invariant không xảy ra, không gọi là class đã đạt tới.

### 5. Boundary Value Analysis

Đọc [boundary-rules.md](references/boundary-rules.md). Mỗi biên có ID, nguồn, biến/quan hệ, giá trị đóng/mở, độ mịn, on/off-point và lựa chọn 2-point/3-point. Ưu tiên bài giảng/người dùng; nếu thiếu, nêu quy ước và lý do, không coi mặc định của skill là yêu cầu môn học. Enum không thứ tự không có biên số.

Biên phụ thuộc: chọn dữ liệu nền, tính biên cụ thể rồi chọn điểm. Ghi cách đếm độ dài khi Unicode gây khác biệt. Không tự coi giá là số nguyên hoặc bước nhỏ nhất là 1 đồng. Với checkout, tổng tiền đúng phải tính từ giỏ hàng; không suy ra mọi client total dương đều hợp lệ hay tự đặt max total.

### 6. Ma trận và độ phủ

- Bắt đầu nominal hợp lệ. Test âm ưu tiên một điều kiện sai (Single Fault Assumption), còn lại hợp lệ; giải thích ngoại lệ do quan hệ phụ thuộc.
- Có thể gộp lớp hợp lệ khi từng kết quả quan sát được. Nhiều biên độc lập nên đổi từng biến quanh nominal; nếu gộp phải giải thích.
- Loại trùng nhưng không hy sinh độ phủ; không khẳng định bộ test tối thiểu toàn cục khi chưa chứng minh.
- Test bổ sung về trạng thái, quyền, persistence hoặc tương tác ghi đúng kỹ thuật; không gắn BVA khi không có biên.
- Giữ đúng phạm vi feature và nền tảng người dùng chọn. Không nhân đôi toàn bộ test Web sang API (hoặc ngược lại) chỉ vì API tồn tại; thêm API/security test riêng khi yêu cầu, mục tiêu kiểm thử hoặc người dùng yêu cầu cần nó.
- Không tạo input field từ code-only payload (`id`, `user_id`, field nội bộ…) nếu spec/API contract không mô tả field đó. Yêu cầu security ghi rõ trong spec có thể tạo test bổ sung, nhưng phải gắn nhãn security/API thay vì trộn vào miền form chính.
- Matrix: `TC ID | Requirement | Technique | Dữ liệu đầy đủ | Class IDs | Boundary/điểm | Validity | Expected`.
- Truy vết yêu cầu/lớp/điểm biên tới TC; chỉ gán lớp thật sự được kiểm tra. Ghi phần chưa phủ/không đạt tới được và lý do. Test thuộc cả Domain và BVA chỉ tính một lần.

### 7. Xuất và review

Lưu giải thích từng bước vào analysis, bảng tổng hợp/card vào test-cases; liên kết hai chiều. Phân biệt phần Domain và BVA dù dùng chung test. Giữ expected tách actual; chỉ ghi thực thi khi có kết quả thật, ghi nguồn nếu do người dùng cung cấp.

## Checklist

- Quy tắc có nguồn, spec làm oracle; giả định không biến thành yêu cầu.
- Bảng input/context không chứa postcondition, snapshot, response hoặc message; các mục đó nằm trong oracle/expected.
- Mọi class có predicate hoặc danh sách giá trị từ spec; spec gap không bị biến thành class hay expected.
- Test chỉ thuộc nền tảng/API được chọn; không nhân đôi Web/API tự động.
- Phân tích Domain/BVA được lưu file và giải thích cách chọn lớp/điểm.
- Lớp đạt tới được và điểm biên trong phạm vi có test hoặc ghi rõ thiếu sót.
- Expected tính đúng, on/off-point đúng phía, độ mịn có căn cứ.
- Matrix, summary, card, truy vết khớp ID/số lượng; link hoạt động.
- Chưa chạy giữ Not Run; không tự tạo actual, bằng chứng hoặc Pass/Fail.

## Giới hạn

EP/BVA không bảo đảm phát hiện mọi lỗi trong lớp, tương tác hoặc trình tự. Nêu kỹ thuật bổ sung phù hợp, không dùng giới hạn này để bỏ qua yêu cầu chức năng trong phạm vi.
