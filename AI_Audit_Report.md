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

## Artifact bước Domain

Cùng prompt A04 phía trên; Codex; ngày 2026-10-18. Output nguyên văn: [snapshot phân tích và card](audit/A04_02_domain_initial.md). 22 TC Draft, chưa thực thi và chưa được sinh viên audit; chưa gán verdict. Snapshot giữ bản đầu, chưa đại diện bản cuối sau self-review của AI.

## Artifact bước BVA và self-review

Cùng prompt A04 phía trên; Codex; ngày 2026-10-18. Output nguyên văn: [snapshot BVA và card](audit/A04_03_bva_initial.md). 24 TC Draft, chưa thực thi, chưa được sinh viên audit. Verdict và lý do theo bài giảng chưa được sinh viên điền. AI tự phát hiện payload của case sửa riêng tên/địa chỉ/email chưa đồng bộ với steps, đã sửa trước thực thi; xem khác biệt giữa snapshot 02 và 03. Không gán đây là phát hiện của sinh viên. Dữ liệu rỗng/null và GUI còn mở được ghi rõ ở analysis.

## Conversation audit before the current request

Các lượt trao đổi trước về lựa chọn feature, cấu trúc bài, Git, kiểm tra/cập nhật skill và thiết kế A04 được lưu trong [conversation_before_current.md](audit/conversation_before_current.md). File đó giữ prompt người dùng, output đã được truy xuất từ transcript hoặc artifact local, verdict và phần sửa của sinh viên. Các mục không có timestamp/export đầy đủ được đánh dấu `INCOMPLETE`; không suy diễn thời gian hay chép output tóm tắt thành raw output.

Các lượt làm bài mới được ghi append-only trong [conversation_log.md](audit/conversation_log.md), theo quy tắc của [work-flow](.agents/skills/work-flow/SKILL.md). Log bao gồm câu hỏi, phản biện, kiểm tra/cập nhật skill và artifact; lượt hiện tại được ghi vì người dùng yêu cầu lưu các chat tiếp theo.

## A04 spec-first review and correction

- **Prompt:** Người dùng yêu cầu review lại [analysis/A04_Profile.md](analysis/A04_Profile.md) và [test-cases/A04_Profile.md](test-cases/A04_Profile.md) theo các guardrail đã chỉnh.
- **Công cụ/skill:** Codex; [domain-testing](.agents/skills/domain-testing/SKILL.md) và [work-flow](.agents/skills/work-flow/SKILL.md).
- **Output và thay đổi:** [A04_04_spec_review.md](audit/A04_04_spec_review.md) ghi findings và before/after. Analysis mới tách input/context khỏi observable output, bỏ output classes và field code-only; test-cases mới còn 13 test, không nhân đôi Web/API.
- **Verdict hiện tại:** INCOMPLETE — AI self-review, Web execution và API execution đã thực hiện; sinh viên cung cấp actual/evidence nhưng review tổng thể và đối chiếu bài giảng còn pending.
- **Student review required:** kiểm tra lại phạm vi Web/API, quy ước BVA 2-point, expected reject/retention và dữ liệu fixture; xác nhận 7 Pass/6 Fail, phân loại TC-A04-002/004 và các bug draft trước khi chốt.

## A04 API execution

- **Prompt:** Người dùng yêu cầu dùng Postman CLI để chạy từ TC-A04-010 trở đi và đặt evidence dưới `test-cases/evidence/`.
- **Công cụ:** Postman CLI v1.75.0; collection [a04_api_postman_collection.json](test-cases/evidence/A04/a04_api_postman_collection.json); SUT local backend.
- **Output thực tế:** [a04_api_execution_summary.md](test-cases/evidence/A04/a04_api_execution_summary.md) và [postman-cli.log](test-cases/evidence/A04/postman-cli.log). Log đã redacted token.
- **Verdict:** TC-A04-010 Pass, TC-A04-011 Pass, TC-A04-012 Fail, TC-A04-013 Pass. BUG-A04-001 được ghi nháp tại [bug-reports/bugs.md](bug-reports/bugs.md), chưa đăng GitHub Issue.
- **Caveat:** Postman CLI không cho dùng `--output` khi chưa đăng nhập Postman Cloud; CLI reporter tích hợp được dùng và lưu đầy đủ. Sinh viên cần review actual/evidence trước khi nộp.

## A04 Web execution

- **Prompt:** Người dùng cung cấp actual state cho TC-A04-001 đến TC-A04-009 và yêu cầu cập nhật test case, chuyển ảnh vào `test-cases/evidence/A04/`, xoá artifact dư.
- **Output thực tế:** TC-A04-001–005 được báo Fail; TC-A04-006–009 Pass. Ảnh `TC-A04-001.png`, `TC-A04-002.png`, `TC-A04-003.png`, `TC-A04-005.png` đến `TC-A04-009.png` đã chuyển vào [evidence/A04](test-cases/evidence/A04/). Không có file `TC-A04-004.png` trong workspace nên card ghi thiếu evidence, không tạo ảnh giả.
- **Bug drafts:** [BUG-A04-002](bug-reports/bugs.md) cho UI từ chối phone hợp lệ L=10/11; [BUG-A04-003](bug-reports/bugs.md) cho UI lưu phone không bắt đầu bằng 0. TC-A04-002/004 vẫn được giữ trạng thái Fail theo báo cáo của sinh viên, nhưng hành vi quan sát phù hợp expected invalid class theo FR-04; cần sinh viên xác nhận lại phân loại.
- **Cleanup:** Xoá `backend.stderr.log` rỗng và `backend.stdout.log` khỏi evidence/A04; giữ collection, summary, CLI log và ảnh thực tế.

## B08 — Domain design và đánh giá BVA

- **Prompt nguyên văn:** “Thực hiện cho feature B08, trigger các skills liên quan.”
- **Ngày/công cụ:** 2026-10-18, Asia/Bangkok; Codex; model/version không xác nhận. Dùng work-flow và domain-testing.
- **Nguồn:** FR-08 và SEC-02 trong README SUT, API Cart & Orders, đề HW02. Đọc code chỉ để chọn bước thực thi; chưa chạy SUT.
- **Output ban đầu:** [analysis/B08_Checkout.md](analysis/B08_Checkout.md), [test-cases/B08_Checkout.md](test-cases/B08_Checkout.md). Bản bất biến qua Git: `f2bad5b` (yêu cầu/scope), `c44688e` (Domain, đánh giá BVA, 10 card).
- **Kết quả thiết kế:** 6 lớp; 10 TC duy nhất: 8 Domain, 2 Functional, 0 BVA; 10 Not Run. Dùng T=Σ(pᵢ×qᵢ) làm oracle; sửa total client chỉ được reject hoặc tạo đơn đúng T.
- **Review AI:** ID/matrix/summary/card, 13 trường/card và link đã kiểm tra. BVA chưa có min/max/precision được đặc tả; T−1/T/T+1 chỉ là relational Domain probes, không tự thêm threshold từ coupon/code. Ghi rõ giới hạn với rubric HW.
- **Verdict sinh viên:** Chưa review/chưa gán VALID hoặc INVALID. Không có bug B08 được tái hiện hoặc evidence execution.
- **Gap:** chưa đối chiếu bài giảng; chưa thể kiểm chứng chất lượng test bằng runtime. Chưa có trường hợp sinh viên phát hiện AI bỏ sót test/bug B08 để ghi là gap thực tế. Xem [conversation log](audit/conversation_log.md) để biết chi tiết lượt này.

## B08 — Review G1/G3 được người dùng xác nhận

- **Ngày:** 2026-10-18, Asia/Bangkok; Codex; dùng domain-testing và work-flow.
- **D1 (G1):** người dùng chốt giỏ trống phải từ chối, báo lỗi và không tạo đơn. Không thêm max tổng/số dòng. Thêm TC-B08-011 (Web) và TC-B08-012 (API).
- **D2 (G3):** người dùng chốt total client khác tổng backend phải từ chối, báo lỗi và không tạo đơn. Cập nhật TC009/010; không còn chấp nhận ignore rồi tạo đơn đúng tiền.
- **BVA:** D1 cung cấp biên số dòng n≥1. B1 dùng 2-point: n=1 ở TC008, n=0 ở TC012; q=2 của P vẫn chỉ là 1 dòng giỏ. Total=0 cho giỏ trống để không đồng thời vi phạm D2.
- **G2:** chưa chốt precision/rounding. Giải thích bằng phép tính giả sử 10.123×3=30.369: lưu 30.369, làm tròn 30.37 hoặc 30 cần policy riêng, không tự chọn. Đây là ví dụ giải thích, không khẳng định catalog chấp nhận giá đó. Bộ hiện tại dùng fixture tính chính xác bằng số nguyên.
- **Output:** [analysis](analysis/B08_Checkout.md), [test cases](test-cases/B08_Checkout.md); 12 TC duy nhất: 8 chỉ Domain, 2 Domain/BVA, 2 Functional; 12 Not Run. Không có kết quả SUT mới.
- **Nguồn:** D1/D2 là quyết định bổ sung do người dùng xác nhận trong chat, không nguyên văn FR-08. Lịch sử nhận định cũ được giữ ở audit/Git để truy vết.

## B08 — Gap về cách trình bày do sinh viên phát hiện

Người dùng phản hồi test case khó hiểu vì quá nhiều mã R1/F1 và ký hiệu nội bộ. AI đã ưu tiên cấu trúc truy vết nhưng chưa tối ưu hướng dẫn cho người chạy test. Đã viết lại [test cases](test-cases/B08_Checkout.md) bằng giá trị và tên điều kiện cụ thể; mã C/B chỉ giữ trong trường truy vết. Giữ 12 ID, expected và trạng thái Not Run. Đây là phản hồi về khả năng đọc/thực thi tài liệu, không phải bug SUT hoặc test bị bỏ sót.

## B08 — Thực thi API bằng Postman CLI

- **Prompt nguyên văn:** “Chạy các test case đó bằng cli đi.”
- **Ngày/công cụ:** 2026-10-18, Asia/Bangkok; Codex và Postman CLI 1.75.0.
- **Môi trường:** backend chưa chạy; startup database.js có DROP TABLE. AI tạo bản sao nguyên mã server.js/database.js trong thư mục tạm, dùng node_modules hiện có, chạy database riêng trên localhost:3000. Hash nguồn bản sao khớp workspace; không reset database gốc. Sau thu thập evidence đã dừng backend thử nghiệm.
- **Collection:** tự tạo sản phẩm giá 100000 qua API Admin, đọc catalog xác minh, mỗi TC có user/giỏ mới và snapshot orders trước/sau. Setup assertion có fail-fast để tránh kết quả sai do fixture không đạt. Không dùng kết quả SUT làm oracle tổng tiền.
- **Kết quả:** TC006 Pass (401, không đơn), TC007 Pass (403, không đơn); TC008 Fail (đơn đúng 200000 nhưng giỏ không xóa); TC009/010 Fail (lưu 199999/200001 thay vì từ chối); TC012 Fail (giỏ trống tạo đơn 0).
- **Số liệu thật:** 57 requests, 67 assertions, 10 assertions failed; exit code 1. Tính theo test case: 2 Pass, 4 Fail, còn 6 Web Not Run. Không nhầm 10 assertions failed thành 10 TC fail.
- **Artifacts:** [summary](test-cases/evidence/B08/b08_api_execution_summary.md), [CLI log](test-cases/evidence/B08/postman-cli.log), [response/snapshot JSON](test-cases/evidence/B08/b08_api_results.json), [collection](test-cases/evidence/B08/b08_api_postman_collection.json); cập nhật [test cases](test-cases/B08_Checkout.md), analysis và progress.
- **Bug drafts:** BUG-B08-001 giỏ không xóa; BUG-B08-002 client total được tin và lưu sai; BUG-B08-003 giỏ trống tạo đơn. Xem [bugs.md](bug-reports/bugs.md). Chưa có screenshot/GitHub Issue; không tự đăng ngoài workspace.
- **Verdict:** execution có evidence; sinh viên chưa review kết quả cuối. TC012 đánh theo quyết định bổ sung được xác nhận, phân biệt với FR-08 gốc. Chưa chạy Web, chưa suy ra kết quả UI.
