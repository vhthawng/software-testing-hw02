# A04 review — spec-first correction

- **Ngày:** 2026-10-18 (Asia/Bangkok)
- **Phạm vi:** analysis/A04_Profile.md và test-cases/A04_Profile.md
- **Nguồn oracle:** README EShop v2.0, FR-04, SEC-02, SEC-06; API specification Users 2.1–2.2
- **Trạng thái:** AI self-review hoàn tất; sinh viên chưa review; chưa thực thi

## Findings từ bản trước

1. V8 “hồ sơ lưu và đọc lại” và V9 “kết quả từ chối, dữ liệu trước/sau” là observable output/postcondition. Chúng không phải input variable và không được lập equivalence class như C22–C28.
2. id và user_id không xuất hiện trong API contract cập nhật hồ sơ. Các test cố gắng thêm chúng là field code-only, vượt phạm vi spec/API.
3. Toàn bộ bộ test phone bị nhân đôi Web/API dù người dùng chọn feature Web. API chỉ cần xuất hiện ở các kiểm tra bảo mật hoặc ownership có căn cứ.
4. Empty, null, missing, trim, Unicode và status/message chưa được FR-04 đặc tả. Chúng phải là spec gap, không phải assumption hoặc class có expected.
5. Gạch ngang và khoảng trắng được gom vào một lớp non-digit vì spec chỉ phân biệt chữ số và điều kiện còn lại; không tự tách thành các lớp có hành vi khác nhau.
6. Email UI là một hành vi chức năng được oracle bởi FR-04, không phải một output variable.

## Thay đổi sau review

| Hạng mục | Trước | Sau |
|:---|:---|:---|
| Input/context | Có V8/V9 là output | Chỉ giữ V1–V5 là input/context |
| Oracle | C22–C28 output class | O1–O6 ghi riêng dưới postcondition và dùng trong expected |
| Ownership | Test id và user_id | Kiểm tra token U với endpoint /me, xác nhận W không đổi |
| Nền tảng | 14 test phone Web/API trùng nhau | Core test Web; API chỉ cho SEC-02, SEC-06 và /me |
| Spec gap | G1–G3 bị trộn vào phân vùng | G1–G5 được ghi rõ, không tạo class/expected khi chưa chốt |
| Bộ test | 24 test | 13 test duy nhất, giữ ID TC-A04-001–013 trong bản đã review |
| Trace/statistics | Đếm output class và test trùng | Trace input/class/boundary; bổ sung một test chức năng email vào thống kê kỹ thuật |

## Kết quả

Bản mới tách input/context, observable postcondition, spec gap, Domain Testing và BVA theo workflow/skill hiện tại. Tất cả test vẫn Not Run; chưa có Actual Result, evidence, Pass/Fail hoặc bug claim.
