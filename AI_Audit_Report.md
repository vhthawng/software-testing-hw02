# AI Audit Report — HW02 (đang làm)

“I use AI tools for the following tasks,”: phân tích FR-04, thiết kế Domain/BVA và ghi tài liệu. Công cụ: Codex trong phiên chat hiện tại; model/version không xác nhận. MSSV 23120355; họ tên/lớp chưa cung cấp.

## Phạm vi ghi nhận

Bắt đầu ghi từ yêu cầu A04 ngày 2026-10-18; thời gian ghi nhận đầu tiên 14:27:01 Asia/Bangkok (07:27:01 UTC), không phải thời gian chính xác gửi prompt. Chưa nhập toàn bộ trao đổi trước đây về lựa chọn feature và chỉnh skill; chưa có export đầy đủ tool trace/final chat. Không tuyên bố đây là audit hoàn chỉnh.

Prompt nguyên văn của tương tác:

> bắt đầu với features đầu tiêu A04..
> Trigger các skills liên quan

Skill áp dụng: [work-flow](.agents/skills/work-flow/SKILL.md), [domain-testing](.agents/skills/domain-testing/SKILL.md).

## Audit theo artifact

| Artifact / công cụ / ngày | Output AI ban đầu | Verdict sinh viên | Lý do theo bài giảng / chuẩn | Bản sinh viên sửa |
|---|---|---|---|---|
| A04 phân tích yêu cầu; Codex; 2026-10-18 | [Snapshot nguyên văn](audit/A04_01_requirements_initial.md) | Chưa review, chưa gán VALID/INVALID/INCOMPLETE | Chưa có bài giảng để dẫn chiếu | Chưa có |

Snapshot lưu nội dung AI tạo ban đầu; các thay đổi sau này không ghi đè snapshot. Nội dung file là output artifact, chưa thay thế bản export nguyên văn toàn bộ chat. Các bước trong cùng turn dùng chung prompt, không bịa prompt mới cho từng bước.

## Tổng kết và khai báo

Artifact đã được sinh viên audit: 0; chưa tính tỷ lệ đúng/sai. Chưa có kết luận trải nghiệm 80–150 từ hoặc chữ ký. Chưa thể điền khai báo “tôi đã review/chỉnh sửa” thay sinh viên. AI Critique 200–300 từ sẽ viết sau trải nghiệm thực tế, không tạo sẵn bằng kinh nghiệm giả.
