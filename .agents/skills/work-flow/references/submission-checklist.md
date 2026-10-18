# Hồ sơ HW02

Đối chiếu lại đề gốc khi dùng; tên file dưới đây là đề xuất. Theo yêu cầu hiện tại, workflow chỉ tạo báo cáo .md, không tạo PDF. Các artifact như Git log .txt, ảnh, script và ZIP vẫn giữ định dạng phù hợp. Đề gốc yêu cầu thêm PDF cho báo cáo chính, AI Audit và AI Critique; ghi nhận riêng phần này ngoài phạm vi hiện tại, không chặn hoàn thành Markdown hoặc tự tạo PDF.

| Artifact | Nội dung cần có |
|---|---|
| report.md | 4 feature mỗi pool một; giải thích từng bước Domain/BVA, test case, kết quả, bằng chứng và AI gap. Liên kết đầy đủ tới analysis, test case và bằng chứng trong Markdown. |
| bug-reports/bugs.md | Bug, ảnh và link GitHub Issues nhóm; mỗi issue có screenshot. Chưa tái hiện/chưa đăng ghi đúng trạng thái. |
| AI_Audit_Report.md | Phụ lục khai báo dùng AI; mỗi interaction có công cụ, ngày giờ, prompt, output. Thiếu dữ liệu phải ghi rõ. |
| `audit/conversation_log.md` | Log append-only của mọi lượt chat liên quan đến bài: prompt, output, skill/tool, timestamp, artifact, verdict và phần review; các mục thiếu raw transcript phải ghi `INCOMPLETE`. |
| AI_Critique.md | Đoạn 200–300 từ về AI sai/thiếu/bias, nguyên nhân, bài học; sinh viên review. |
| git_commit_log.txt | Lịch sử thật, commit theo bước/feature. |
| README.md | Bảng tự đánh giá; số feature, test thiết kế/executed/pass/fail/chưa chạy; số bug; video demo. |
| Skill và video | Khuyến khích, 10 điểm rubric; nộp skill cùng YouTube demo end-to-end một feature hoàn chỉnh nếu làm. Không tạo link giả. |
| Hỗ trợ | Test data, script, ảnh, video/link và phụ lục thực tế. |

## Cấu trúc thư mục nộp ở giai đoạn cuối

Chỉ tạo thư mục này khi hoàn thiện/đóng gói. Hiện tại các file được làm trực tiếp trong workspace, không nằm dưới thư mục này. Giữ các mục PDF trong kế hoạch, nhưng không tạo file PDF rỗng, giả hoặc tự chuyển đổi; nếu người dùng cung cấp PDF thật thì giữ lại khi đóng gói.

```text
23120355_HW02_AI_DomainTesting_090/
├── README.md
├── report.md
├── report.pdf                  # Dự kiến; không generate
├── AI_Critique.md
├── AI_Critique.pdf             # Dự kiến; không generate
├── AI_Audit_Report.md
├── AI_Audit_Report.pdf         # Dự kiến; không generate
├── git_commit_log.txt
├── analysis/                  # Phụ lục phân tích Domain/BVA
│   ├── A04_Profile.md
│   ├── B08_Checkout.md
│   ├── C15_ProductManagement.md
│   └── D_MobileRegister.md
├── test-cases/
│   ├── A04_Profile.md
│   ├── B08_Checkout.md
│   ├── C15_ProductManagement.md
│   └── D_MobileRegister.md
├── bug-reports/
│   └── bugs.md
├── evidence/
│   ├── A04/
│   ├── B08/
│   ├── C15/
│   └── D_MobileRegister/
└── skills/                    # Sao chép các skill đã dùng/xây dựng
```

`090` là giá trị trong tên thư mục người dùng đưa, không chứng nhận điểm đã tự đánh giá. Nếu người dùng điều chỉnh điểm cuối cùng, cập nhật tên trước đóng gói. Sao chép artifact từ workspace, giữ nguyên bản làm việc, kiểm tra liên kết tương đối sau sao chép. Nếu thiếu PDF thật, ghi rõ thiếu và không tuyên bố ZIP đã đủ hồ sơ theo đề gốc.

## Tổng hợp

- Mỗi TC ID là một test duy nhất dù thuộc Domain và BVA.
- Ghi riêng Pass/Fail/Blocked/Skipped/Not Run. Nêu định nghĩa executed (thông thường Pass + Fail hoàn tất) và cách tính chưa chạy để tổng khớp, không giấu trạng thái.
- Nhiều test có thể cùng một bug; test âm xử lý đúng vẫn Pass.
- Rubric: A 25, B 25, C 25, D Mobile 15, Agent Skills 10. Bảng có điểm tối đa và điểm sinh viên tự đánh giá.

## Đóng gói

- ZIP: `<StudentID>_HW02_AI_DomainTesting_<SelfAssessedGrade>.zip`, điểm 3 chữ số 000–100.
- Markdown có đủ phân tích/card hoặc liên kết tới các file tương ứng; bảng/ảnh hiển thị đúng, link local hoạt động sau giải nén. Không xuất PDF trong workflow hiện tại.
- Không đóng node_modules/cache/token thật hoặc .git nếu không được yêu cầu riêng; log văn bản là phần Git bắt buộc theo đề.
- Deadline xem Moodle; không nhận trễ; thiếu tài liệu bắt buộc bị 0; sao chép kể cả prompt bị 0 cho hai bên. Phân biệt hoàn tất phần Markdown được giao với đủ hồ sơ theo đề gốc; khi tổng kết, ghi rõ PDF chưa được tạo theo phạm vi người dùng yêu cầu.
