# Hồ sơ HW02

Đối chiếu lại đề gốc khi dùng; tên file dưới đây là đề xuất. Theo yêu cầu hiện tại, workflow chỉ tạo báo cáo .md, không tạo PDF. Các artifact như Git log .txt, ảnh, script và ZIP vẫn giữ định dạng phù hợp. Đề gốc yêu cầu thêm PDF cho báo cáo chính, AI Audit và AI Critique; ghi nhận riêng phần này ngoài phạm vi hiện tại, không chặn hoàn thành Markdown hoặc tự tạo PDF.

| Artifact | Nội dung cần có |
|---|---|
| report.md | 4 feature mỗi pool một; giải thích từng bước Domain/BVA, test case, kết quả, bằng chứng và AI gap. Liên kết đầy đủ tới analysis, test case và bằng chứng trong Markdown. |
| bug-reports/bugs.md | Bug, ảnh và link GitHub Issues nhóm; mỗi issue có screenshot. Chưa tái hiện/chưa đăng ghi đúng trạng thái. |
| AI_Audit_Report.md | Phụ lục khai báo dùng AI; mỗi interaction có công cụ, ngày giờ, prompt, output. Thiếu dữ liệu phải ghi rõ. |
| AI_Critique.md | Đoạn 200–300 từ về AI sai/thiếu/bias, nguyên nhân, bài học; sinh viên review. |
| git_commit_log.txt | Lịch sử thật, commit theo bước/feature. |
| README.md | Bảng tự đánh giá; số feature, test thiết kế/executed/pass/fail/chưa chạy; số bug; video demo. |
| Skill và video | Khuyến khích, 10 điểm rubric; nộp skill cùng YouTube demo end-to-end một feature hoàn chỉnh nếu làm. Không tạo link giả. |
| Hỗ trợ | Test data, script, ảnh, video/link và phụ lục thực tế. |

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
