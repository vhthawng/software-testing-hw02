# Output phân tích và test case

Tên file là quy ước đề xuất. Thay dữ liệu mẫu bằng dữ liệu thực khi xuất report.

## analysis.md

Dùng mẫu dưới đây khi xuất analysis. Thay nội dung trong dấu `<…>` bằng dữ liệu thực; không để placeholder trong báo cáo hoàn chỉnh.

```markdown
# Analysis: <Tính năng / field>

| | |
|:---|:---|
| **Tính năng / nền tảng** | <màn hình, field; web/API/mobile; build hoặc môi trường> |
| **Người thiết kế / ngày** | <tên>, <ngày tạo>, <ngày cập nhật gần nhất> |
| **Test case chi tiết** | [test-cases.md](test-cases.md) |

## 1. Phạm vi và nguồn

**Tài liệu đã đọc**

| Nguồn | Phiên bản / trạng thái (Draft, Reviewed) | Phần đã đọc | Phần chưa đọc / thiếu |
|:---|:---|:---|:---|
| <SRS, API docs, mockup, bản chạy thật…> | | | |

- **Trong phạm vi:** <biến, đầu ra được phân tích>
- **Ngoài phạm vi:** <nội dung loại ra và lý do>
- **Kỹ thuật áp dụng:** Domain Testing (EP + BVA + Single Fault). <1 đến 2 câu: vì sao phù hợp>. Kỹ thuật bổ sung nên dùng cho phần chưa phủ: <pairwise / state-transition / security…>

## 2. Biến và quy tắc

| Mã | Biến / đầu ra | Loại (nhập trực tiếp / dữ liệu nền / đầu ra) | Kiểu, đơn vị | Ràng buộc | Nguồn | Explicit / Suy ra |
|:---|:---|:---|:---|:---|:---|:---|
| V1 | | | | | <tài liệu + mục> | |

**Nominal (dữ liệu nền hợp lệ):** <giá trị của mọi biến; mọi test mặc định dùng bộ này, chỉ đổi biến đang test>

## 3. Giả định và câu hỏi mở

| Mã | Nội dung | Nguồn | Trạng thái | TC bị ảnh hưởng nếu sai |
|:---|:---|:---|:---|:---|
| A1 | | | Từ spec / Đề xuất / Đã xác nhận / Mâu thuẫn / Chưa rõ | |

*Message lỗi đề xuất (nếu spec không nêu):*

| Mã | Tình huống | Message | Trạng thái |
|:---|:---|:---|:---|
| M1 | | | Đề xuất |

## 4. Domain Testing: phân vùng tương đương

**Cách chia lớp:** <nêu cách chia theo thuộc tính nào, vì sao; đã cân nhắc Splitting Principle ở đâu>.

**Quan hệ phụ thuộc và Single Fault:** <biến nào phụ thuộc biến nào; cách giữ giá trị khi test (ví dụ Confirm = Password khi test giá trị Password); chỗ nào đành vi phạm lỗi đơn và vì sao>.

Mã lớp đánh số liên tục, gồm cả đầu vào và đầu ra.

| Class ID | Biến / đầu ra | Điều kiện | Validity | Đại diện / cách đạt | Nguồn |
|:---|:---|:---|:---|:---|:---|
| C1 | V1 | | Hợp lệ / Không hợp lệ | | |
| C20 | Đầu ra: <tên> | | | <đầu vào nào đưa đầu ra vào lớp này> | |

*Lớp "không đạt tới được" (nếu có) ghi rõ trong bảng và xử lý ở mục 7.*

## 5. Boundary Value Analysis

**Cách xác định biên:** <biên đóng/mở theo toán tử trong quy tắc; biên phụ thuộc tính từ dữ liệu nền nào>.

**Độ mịn:** <±1 / ±0.01 / ±1 ngày…>.

**2-point hay 3-point:** <chọn loại nào và lý do>.

| Boundary ID | Class ID liên quan | Quy tắc / nguồn | Đóng / mở | Độ mịn | On-point | Off-point | Expected (kèm công thức) | Lý do chọn |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| B1 | C2, C3 | | | | | | | |

## 6. Test Matrix

Chữ **đậm** là giá trị khác nominal (Single Fault). Test hợp lệ được gộp nhiều lớp; test không hợp lệ chỉ có một giá trị sai.

| TC ID | Requirement / nguồn | Technique (Domain / BVA / Cả hai) | Dữ liệu đầy đủ | Class IDs | Boundary / điểm | Validity | Expected (kèm công thức) |
|:---|:---|:---|:---|:---|:---|:---|:---|
| TC-XXX-001 | | | | | | | |

## 7. Truy vết và độ phủ

| Requirement / Class / Boundary point | TC IDs | Trạng thái (Đã thiết kế / Chưa phủ / Không đạt tới được) | Lý do |
|:---|:---|:---|:---|
| C1 | | | |
| B1 (on) | | | |

**Thống kê (không đếm đôi):**

| Chỉ số | Số lượng |
|:---|:---|
| Tổng test case duy nhất | N |
| Chỉ Domain | x |
| Chỉ BVA | y |
| Thuộc cả hai | z |
| Hợp lệ / Không hợp lệ | h / i |

Kiểm tra: N = x + y + z. **Mức giảm:** <số tổ hợp đầy đủ ước tính> → N test case.

**Thiếu sót đã biết:** <lớp hoặc biên chưa phủ và lý do>.

## 8. Giới hạn và kỹ thuật bổ sung

<Các phần Domain Testing không bắt được: tương tác giữa biến, trình tự/trạng thái, bảo mật sâu… kèm kỹ thuật đề xuất>

## 9. Review và thay đổi

| Phiên bản | Ngày | Thay đổi | Lý do | TC bị ảnh hưởng | Trạng thái review |
|:---|:---|:---|:---|:---|:---|
| v1 | | Bản đầu | | | Chưa review |
```

### Cách điền analysis

- Spec là nguồn cho expected; code/UI/API implementation chỉ hỗ trợ thực thi và đối chiếu. Ghi rõ nguồn ưu tiên tại mục 1.
- Trạng thái `Từ spec` là nội dung đã biết, không phải giả định chưa xác nhận; tham chiếu quy tắc ở mục 2 để tránh nhập nhằng. `Mâu thuẫn` ở mục 3 chỉ dành cho mâu thuẫn giữa các nguồn yêu cầu.
- Message đề xuất chỉ là minh họa, không dùng để đánh Fail vì SUT dùng câu khác nếu spec không bắt buộc nguyên văn.
- Splitting Principle: tách lớp khi có điều kiện/con đường xử lý theo yêu cầu khác nhau; nêu căn cứ chia lớp. Đối chiếu cách gọi/áp dụng trong bài giảng nếu có, không suy ra đặc tả từ các nhánh code.
- Khi dùng 3-point, bổ sung cột `Điểm thứ ba` hoặc dòng ghi điểm còn lại, expected và truy vết riêng. Không chỉ liệt kê on/off rồi tuyên bố đã phủ 3-point.
- Single Fault xét điều kiện bị vi phạm, không chỉ số ô khác nominal; biến phụ thuộc có thể phải đổi đồng thời và phải giải thích. Test hợp lệ được gộp khi từng kết quả quan sát được.
- Nếu có test kỹ thuật bổ sung, thêm nhãn Technique và dòng `Chỉ kỹ thuật bổ sung` vào thống kê, điều chỉnh đẳng thức tổng. Validity chưa rõ phải thống kê riêng, không ép vào hợp lệ/không hợp lệ.
- Mức giảm chỉ tính khi có cơ sở và tập tổ hợp hữu hạn được mô tả rõ; nếu không, ghi `Không định lượng`, không bịa số tổ hợp hoặc tuyên bố tối thiểu.
- Tên người thiết kế, ngày, trạng thái nguồn và review phải có căn cứ; chưa biết ghi chưa cung cấp, không tự điền sinh viên đã review.

## test-cases.md

Thông tin đầu file: feature, nền tảng/môi trường, nguồn yêu cầu, ngày tạo/cập nhật thực tế và link analysis.md.

Bảng tổng hợp:

| TC ID | Title | Requirement | Technique | Dữ liệu chính | Expected | Status | Bug |
|---|---|---|---|---|---|---|---|

Mỗi test có card:

| Trường | Nội dung cần điền |
|---|---|
| ID / Title | TC-<FEATURE>-001; tên rõ điều kiện |
| Requirement / Purpose | Quy tắc, nguồn và mục tiêu |
| Technique | Domain Testing / BVA / cả hai / kỹ thuật bổ sung |
| Domain / Boundary | Class ID, Boundary ID và điểm nếu có; link analysis |
| Preconditions / Setup | Tài khoản/quyền, trạng thái, dữ liệu nền để chạy độc lập |
| Test data | Giá trị cụ thể và các biến liên quan |
| Steps | Bước đánh số; API có method/path/payload/trạng thái xác thực |
| Expected Result | Hành vi đúng theo spec, công thức nếu có |
| Actual Result | Chưa thực thi, hoặc quan sát thực tế |
| Status | Not Run / Pass / Fail / Blocked / Skipped |
| Execution context | Ngày, nền tảng/build, người/công cụ chạy; chưa chạy để — |
| Evidence | Link ảnh/log/video thực tế; chưa có để — |
| Bug ID / Issue URL | Chưa có để — |
| Cleanup | Khôi phục dữ liệu/trạng thái nếu cần |

## Quy tắc

- ID duy nhất, ổn định xuyên matrix/summary/card; feature key ví dụ A04, B08, C15, D-REGISTER. Đây là mã test nội bộ, không phải mã yêu cầu.
- Card đủ dữ liệu thực thi độc lập. Chuỗi dài có công thức tạo và độ dài; ký tự khó nhập có mã Unicode/cách tạo.
- Chỉ bắt buộc nguyên văn message, HTTP status, vị trí lỗi, redirect hoặc persistence khi có quy định hay suy luận đã ghi rõ. Message đề xuất không làm oracle kết luận fail vì ứng dụng dùng câu khác.
- Phần chưa rõ ghi giả định và invariant chắc chắn. Ví dụ checkout chưa quy định reject/ignore client total thì không ép response duy nhất, nhưng số tiền lưu phải do backend tính đúng từ giỏ.
- Không đưa token phiên thật vào tài liệu công khai. Expected và actual luôn tách; đọc code không đủ đánh Pass/Fail. Test âm bị từ chối đúng vẫn Pass.
- Bảng tổng hợp/card/matrix khớp ID, số lượng, trạng thái. Không đếm đôi test thuộc cả Domain và BVA.
