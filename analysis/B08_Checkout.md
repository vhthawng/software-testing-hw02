# Analysis: B08 — Checkout Web

| | |
|:---|:---|
| **Tính năng / nền tảng** | FR-08 Checkout Web; API bổ sung cho JWT và tính toàn vẹn tổng tiền |
| **Người thiết kế / ngày** | Codex hỗ trợ MSSV 23120355; tên sinh viên chưa cung cấp; 2026-10-18, Asia/Bangkok |
| **Test case chi tiết** | [B08_Checkout.md](../test-cases/B08_Checkout.md) |
| **Trạng thái** | Draft; chưa thực thi, chưa được sinh viên review |

## 1. Phạm vi và nguồn

| Nguồn | Phiên bản / trạng thái | Phần đã đọc | Phần thiếu |
|:---|:---|:---|:---|
| [EShop README](../eshop-sut/README.md) | v2.0, 2026-05-14; nguồn oracle | FR-06–09, FR-11, SEC-02; yêu cầu GUI chung | Không thiếu đoạn FR-08 |
| [API specification](../eshop-sut/api_specification.md) | Không ghi phiên bản | Authentication; Products; Cart & Orders | Không định nghĩa response checkout/error đầy đủ |
| [HW02](../2026.HW02.Domain%20Testing_En.md) | HW02-AI | Domain/BVA, audit, review, commit theo bước | Bài giảng chưa có trong workspace |
| Code SUT | Chỉ hỗ trợ bước thực thi | Checkout.jsx, CartContext.jsx; route cart/checkout/my-orders trong server.js | Chưa chạy SUT; không dùng code làm oracle |

**Trong phạm vi:** đăng nhập trước checkout; tổng tự tính và không sửa trực tiếp; hiển thị đủ sản phẩm; backend tự tính tiền; xóa giỏ sau thành công. API chỉ bổ sung kiểm tra không thể chứng minh bằng UI thông thường.

**Ngoài phạm vi:** coupon FR-09, state machine FR-10, Mobile, CRUD sản phẩm, toàn bộ GUI/accessibility, validation địa chỉ chưa được mô tả. Không thêm giới hạn giá/tổng/số lượng từ code hoặc database.

**Kỹ thuật:** EP và Single Fault cho xác thực và quan hệ tổng client/tổng giỏ; kiểm tra chức năng cho total control và thay đổi giỏ. BVA được đánh giá riêng ở mục 5; không gán nhãn BVA khi spec chưa cung cấp biên thích hợp.

Các mã R1–R5 dưới đây là nhãn truy vết nội bộ cho từng bullet FR-08, không phải mã yêu cầu mới của SUT.

## 2. Input, context và quy tắc

| Mã | Input / context | Loại | Kiểu, đơn vị | Ràng buộc | Nguồn | Explicit / Suy ra |
|:---|:---|:---|:---|:---|:---|:---|
| V1 | Phiên Web / JWT | Context | Đã đăng nhập; Bearer token | R1: chỉ người dùng đăng nhập được checkout; API cần JWT hợp lệ | FR-08 bullet 1; SEC-02 | Explicit |
| V2 | Giỏ hàng của U | Dữ liệu nền | Danh sách sản phẩm, giá và số lượng | R2: tổng tự tính từ giỏ; R3: hiển thị đầy đủ danh sách đặt mua | FR-08 bullet 2–3 | Explicit; công thức T = Σ(pᵢ × qᵢ) suy ra từ tổng tiền giỏ |
| V3 | total_amount client gửi | Input API / thử thao tác UI | Số tiền | R2: không sửa trực tiếp trên UI; R4: backend tự tính lại, không tin số client | FR-08 bullet 2, 4; API 4.3 | Explicit |
| V4 | shipping_address | Input API nền | Chuỗi | API mô tả field; FR-08 không nêu validation | API 4.3 | Explicit về field; không suy ra required/min/max |

**Nominal:** U role=user, JWT hợp lệ; không áp dụng coupon. Giỏ F1 có sản phẩm P giá 100000 ₫, số lượng 2, T=200000 ₫. Địa chỉ API cố định `123 Le Loi, TP.HCM`. U có email/mật khẩu riêng của người chạy; token không ghi vào tài liệu.

**Fixture F2:** P giá 100000 × 2 và Q khác P giá 50000 × 3; T=350000 ₫. Đây là dữ liệu kiểm thử chủ động chuẩn bị, không khẳng định là dữ liệu seed có sẵn. Nếu catalog không có giá này, tạo fixture bằng chức năng Admin với tài khoản Admin, ghi ID thật; nếu không chuẩn bị được thì Blocked. Không đổi oracle theo giá sai của SUT.

| O ID | Observable postcondition | Căn cứ | Cách kiểm chứng |
|:---|:---|:---|:---|
| O1 | Checkout không được hoàn thành nếu không có phiên/JWT hợp lệ | R1, SEC-02 | Response và danh sách đơn trước/sau bằng token hợp lệ riêng |
| O2 | UI hiển thị đủ sản phẩm, số lượng; total bằng T và không sửa trực tiếp | R2–R3 | So sánh giỏ fixture và màn checkout; thao tác gõ/paste thông thường |
| O3 | Nếu tạo đơn, total lưu bằng T từ giỏ, không theo giá trị sửa của client | R4 | GET /api/orders/my-orders, đối chiếu đơn mới với snapshot và công thức độc lập |
| O4 | Sau thành công, giỏ tương ứng được xóa | R5: FR-08 bullet 5 | Web: quay về giỏ trong cùng phiên; API: GET /api/cart bằng token U |

O1–O4 là oracle, không tạo input variable hoặc output class.

## 3. Spec gap và quyết định phương pháp

| Mã | Nội dung | Nguồn | Loại | Trạng thái | TC ảnh hưởng |
|:---|:---|:---|:---|:---|:---|
| G1 | Chưa nêu checkout giỏ trống, min/max tổng, max số dòng | FR-08 | Spec gap | Ngoài bộ chính; không tự tạo expected reject | Không có |
| G2 | Chưa nêu độ chính xác/làm tròn tiền | FR-08 | Spec gap | Dùng fixture có phép tính chính xác; ±1 chỉ là bước lấy mẫu | 008–010 |
| G3 | Chưa nêu reject toàn request hay ignore total_amount khi bị sửa | FR-08 | Spec gap | Chỉ ép invariant O3; reject hoặc tính lại đều phù hợp | 009–010 |
| G4 | Chưa nêu validation địa chỉ, null/missing total, sai kiểu | FR-08/API | Spec gap | Không tạo lớp invalid theo phỏng đoán | Không có |
| G5 | Web và API cart có cách lưu khác nhau trong implementation | Code hỗ trợ thực thi | Quyết định phương pháp | Chuẩn bị và xác minh riêng từng giỏ; API setup không thay cho Web setup | Toàn bộ |
| G6 | Bài giảng chưa có; FR-08 không nêu ngưỡng số phù hợp BVA | HW02/FR-08 | Giới hạn phương pháp | Mục 5 giải thích; chưa tuyên bố đáp ứng đầy đủ BVA homework | Toàn bộ |

API body có total_amount không có nghĩa backend được tin giá trị đó. FR-08 quyết định oracle; API chỉ mô tả cách gọi. HTTP status/message/redirect cụ thể chỉ ghi actual nếu spec không bắt buộc.
