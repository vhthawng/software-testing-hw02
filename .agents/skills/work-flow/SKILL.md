---
name: work-flow
description: Điều phối làm và chuẩn bị bài nộp HW02 Domain Testing trên EShop, gồm AI Audit, review và gap analysis, Git commit theo bước, thực thi và bug report, tổng hợp báo cáo Markdown và ZIP. Dùng khi tiếp tục hoặc kiểm tra tiến độ HW02; gọi domain-testing cho thiết kế kỹ thuật.
---

# Work-flow làm HW02

Skill điều phối bài làm; dùng [domain-testing](../domain-testing/SKILL.md) cho phân tích miền, BVA và test case. Không chép AI Audit/Git vào skill thiết kế.

## Nguồn và trạng thái

- Đọc `2026.HW02.Domain Testing_En.md` cho quy trình/hồ sơ; `eshop-sut/README.md` là spec nghiệp vụ. Bài giảng nếu có quyết định thuật ngữ/quy trình kiểm thử; ghi rõ phần chưa đối chiếu.
- Kiểm tra repo, working tree và artifact; tiếp tục việc đã làm, giữ chỉnh sửa người dùng.
- Phạm vi đã chọn: A04/FR-04 Web Profile, B08/FR-08 Web Checkout, C15/FR-15 Admin Product CRUD, D Mobile Register thuộc FR-20 với quy tắc FR-01. Không tự đổi. Chưa đủ thông tin nhóm thì không khẳng định đã không trùng.
- Khi thực sự bắt đầu làm bài, lưu `WORK_PROGRESS.md`: feature, bước, artifact, review/execution, commit thật và việc thiếu. Tên này là quy ước địa phương.

## Vị trí file trong giai đoạn làm bài

Lưu artifact trực tiếp dưới workspace hiện tại `D:/university/software-testing/HW02/`; chỉ tạo file/thư mục khi bước tương ứng cần đến. Chưa tạo thư mục nộp bài hoặc bản sao đóng gói trong lúc thiết kế/thực thi.

- File chung tại root: `README.md`, `report.md`, `AI_Critique.md`, `AI_Audit_Report.md`, `git_commit_log.txt` và `WORK_PROGRESS.md`.
- Test case: `test-cases/A04_Profile.md`, `test-cases/B08_Checkout.md`, `test-cases/C15_ProductManagement.md`, `test-cases/D_MobileRegister.md`.
- Phân tích tương ứng: `analysis/A04_Profile.md`, `analysis/B08_Checkout.md`, `analysis/C15_ProductManagement.md`, `analysis/D_MobileRegister.md`. Đây là đường dẫn output truyền cho domain-testing, thay cho mặc định features/<feature-id>/. Điều chỉnh link trong template theo tên/vị trí thực tế.
- Bug ở `bug-reports/bugs.md`; bằng chứng theo `evidence/A04/`, `evidence/B08/`, `evidence/C15/`, `evidence/D_MobileRegister/`.
- Skill tiếp tục được chỉnh tại `.agents/skills/`; chỉ sao chép vào `skills/` của thư mục nộp ở giai đoạn cuối. Không sửa mã nguồn SUT để làm test pass.
- Nếu root đã có file, đọc và tích hợp nội dung cần thiết, không ghi đè nội dung không liên quan.

## Chu trình mỗi tính năng

1. Ghi yêu cầu, phạm vi, nguồn. Gọi domain-testing và lưu artifact ở từng mốc: phân tích yêu cầu; miền và test Domain; biên và test BVA; thực thi; bug report; review AI/chỉnh sửa. Không làm xong toàn bộ rồi tạo lịch sử giả.
2. Giữ bản AI ban đầu và thay đổi sau review. Có thể xuất nháp; không tự ghi sinh viên đã review hoặc bịa phản hồi của họ. Chỉ chờ khi quyết định thực sự chặn bước phụ thuộc.
3. Chạy trên đúng nền tảng; D cần bằng chứng Mobile, API không thay thế kiểm thử UI Mobile. Ghi actual và evidence khi thực thi. Chưa chạy để Not Run/Blocked phù hợp, ghi lý do; không cần thêm một trường Execution context riêng trong từng test card. Đọc code chỉ là dấu hiệu cần xác nhận.
4. Bug Markdown có ID, requirement, môi trường, preconditions/data/steps, expected/actual, ảnh, TC và ảnh hưởng. Đề yêu cầu GitHub Issues nhóm kèm screenshot từng issue. Chuẩn bị nội dung/ảnh trước; skill không tự cấp quyền đăng ra ngoài. Chỉ đăng khi có chỉ dẫn cho phép; chưa có thì giữ nháp, đánh dấu chưa đăng.
5. AI gap analysis mỗi feature: AI ban đầu thiếu/sai gì, ai phát hiện, phần sửa/bổ sung, nguyên nhân có căn cứ (prompt/công cụ/độ phức tạp). Phân biệt nguyên nhân biết và suy đoán; không tạo lỗi AI giả.

## AI Audit và Critique

- Tạo/cập nhật audit trong lúc làm bài: tên công cụ/model nếu biết, ngày giờ/múi giờ người dùng, prompt nguyên văn, output thực sự truy xuất được và khai báo tác vụ dùng AI.
- Lưu review và bản trước/sau, hoặc link tới phiên bản bất biến trong repo. Link tới file đang bị ghi đè không đủ giữ lịch sử tương tác.
- Không tuyên bố thu được toàn bộ chat nếu công cụ không cung cấp. Tương tác cũ/output/thời gian thiếu phải ghi rõ và yêu cầu export/bổ sung khi cần; không suy diễn thành log thật.
- Critique là đoạn 200–300 từ dựa trên trải nghiệm thật: sai/thiếu/bias, nguyên nhân, bài học. Bản AI hỗ trợ cần sinh viên review; không bịa trải nghiệm. Critique toàn bài không thay thế gap analysis từng feature.

## Conversation capture — bắt buộc cho mọi lượt làm bài

- Mỗi lượt chat liên quan đến HW02 phải được ghi vào file append-only `audit/conversation_log.md`, gồm prompt người dùng, output của AI, ngày/giờ theo Asia/Bangkok, công cụ/model nếu biết, skill được áp dụng, artifact/link tạo hoặc sửa, verdict và phần sinh viên cần review. Bao gồm cả câu hỏi làm rõ, phản biện, kiểm tra skill, cập nhật skill và quyết định phạm vi; không bỏ qua vì đó là “trao đổi” thay vì test case.
- Ghi prompt và output nguyên văn khi transcript/export cho phép. Nếu chỉ truy xuất được tóm tắt hoặc artifact, ghi rõ `INCOMPLETE` và lý do; không biến bản tóm tắt thành raw output. Không tự bịa timestamp, prompt, verdict hoặc phản hồi sinh viên.
- Sau khi hoàn thành các thay đổi của lượt hiện tại và trước khi trả lời cuối, append entry của lượt đó vào log. Entry phải ghi rõ nếu có phần chưa thể lưu. Không đưa yêu cầu của người dùng vào audit chỉ khi người dùng yêu cầu loại trừ cụ thể; nếu không có yêu cầu loại trừ thì ghi cả lượt hiện tại.
- `AI_Audit_Report.md` phải liên kết tới conversation log và tóm tắt phạm vi đầy đủ. File lịch sử cũ như `audit/conversation_before_current.md` được giữ nguyên để truy vết, nhưng các lượt mới phải ghi vào `audit/conversation_log.md`.

## Git theo bước

- Dùng repo hiện có; kiểm tra root và status, không tạo repo lồng.
- Mỗi bước thực sự hoàn thành của từng feature có commit theo đề. Xem diff, stage đường dẫn cụ thể và kiểm tra staged diff. Không commit thay đổi không liên quan; tôn trọng quyền ghi Git/sandbox.
- Message nêu feature/bước, ví dụ `A04: analyze input domains`. Không amend/rebase/reset lịch sử hoặc tạo commit rỗng để giả tiến độ.
- Xuất log thật: `git log --reverse --date=iso-strict --pretty=fuller --stat | Out-File -Encoding utf8 git_commit_log.txt`. Nêu phạm vi nếu repo có lịch sử ngoài bài. Sinh lại sau commit nội dung cuối; tránh vòng lặp cố đưa commit của chính log vào log.

## Hoàn thiện bài nộp

Đọc [submission-checklist.md](references/submission-checklist.md) khi tổng hợp. Theo yêu cầu hiện tại của người dùng, chỉ tạo báo cáo Markdown (.md), không tạo/chuyển đổi PDF hoặc cài công cụ xuất PDF. Báo cáo chính liên kết đầy đủ tới phân tích, test case và bằng chứng; kiểm tra bảng, ảnh và liên kết Markdown. Giữ git_commit_log.txt, ảnh, script và các artifact hỗ trợ đúng định dạng của chúng. Số liệu/link phải khớp nguồn thật; không tuyên bố hoàn tất khi thiếu review, bằng chứng, Issue hoặc tài liệu bắt buộc.

Đề gốc vẫn yêu cầu PDF cho báo cáo chính, AI Audit và AI Critique; ghi nhận đây là phần ngoài phạm vi tạo file hiện tại, không coi yêu cầu đề đã thay đổi và không chặn hoàn tất phần Markdown vì chưa có PDF. Không tự xuất PDF trừ khi người dùng đổi yêu cầu.

Chỉ đóng ZIP khi được giao chuẩn bị nộp, dùng MSSV/điểm thực tế. Không tự nộp Moodle hoặc chấm điểm thay sinh viên như đã xác nhận. Lần gọi skill để tư vấn/sửa skill không có nghĩa bắt đầu cả bài, tạo hàng loạt artifact hoặc đăng Issue.
