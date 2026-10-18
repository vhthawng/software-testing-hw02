# Template test report (Pha 2: đọc khi xuất file .md cuối cùng)

Report là tài liệu để tester thực thi và để người review đối chiếu, nên **chỉ gồm**: thông tin chung, bảng tổng hợp, và card chi tiết từng test case. Không đưa phân tích domain testing (lớp tương đương, bảng biên, bảng giả định) vào file.

## Cấu trúc file

```markdown
# Test Report: <Tính năng / Field>

| | |
|:---|:---|
| **Tính năng** | <tên màn hình + field> |
| **Nguồn yêu cầu** | <tên tài liệu + mã/mục đã đọc ở Bước 0 (ví dụ "SRS v1.2, mục 4.2"); nếu chỉ có giao diện thì ghi "Giao diện màn hình …"; không bịa mã> |
| **Ngày tạo** | <YYYY-MM-DD> |
| **Dữ liệu nền (mặc định)** | <các giá trị nominal của form, ghi một lần> |

## 1. Tổng hợp test case

**Tổng: N test case** (X hợp lệ, Y không hợp lệ)

| ID | Title | Dữ liệu chính | Kết quả mong đợi | Status |
|:---|:---|:---|:---|:---|
| TC-XXX-001 | Verify ... | `...` | Chấp nhận / Từ chối: "…" | Chưa chạy |

## 2. Chi tiết test case

### TC-XXX-001 — Verify ...

| Trường thông tin | Nội dung chi tiết |
|:---|:---|
| **ID / Title** | **TC-XXX-001** — Verify ... |
| **Purpose** | ... |
| **Setup** | ... |
| **Steps** | **1.** ...<br>**2.** ...<br>**3.** ... |
| **Expected Result** | **1.** ...<br>**2.** ...<br>**3.** ... |
| **Status / Bug ID** | ☐ Pass &nbsp;&nbsp; ☐ Fail &nbsp;&nbsp; ☐ Blocked &nbsp;&nbsp; ☐ Skipped *(Bug ID nếu Fail: ______)* |
```

## Quy tắc từng trường

- **ID**: `TC-<MÃ FIELD>-<số 3 chữ số>`, duy nhất, xếp hợp lệ trước, không hợp lệ sau, nhóm tương tự đứng cạnh nhau.
- **Title**: cú pháp `Verify <function> <condition>`, viết tiếng Anh, nêu rõ điều kiện (ví dụ `Verify password is rejected when length is 7 characters`). Với test hợp lệ dùng `is accepted`.
- **Purpose**: một đến hai câu: trích quy tắc kèm nguồn (mã yêu cầu hoặc tên tài liệu + mục đã đọc ở Bước 0), hoặc điều người dùng đã xác nhận. Nếu quy tắc là giả định chưa xác nhận, ghi "(giả định, cần xác nhận)". Không bịa mã yêu cầu.
- **Setup**: điều kiện tiên quyết để chạy độc lập (trang, trạng thái, dữ liệu có sẵn, giá trị các field khác). Ghi đủ để mỗi card tự đứng được, nhưng ngắn.
- **Steps**: đánh số, ngắn gọn (≤ 15 bước, thường 3 đến 5). Dữ liệu nhập **cụ thể và đặt trong dấu `code`**; giá trị khác dữ liệu nền (giá trị đang test) **in đậm** để thấy ngay đó là điểm duy nhất bị thay đổi. Ký tự khó gõ (tab, NBSP, xuống dòng) mô tả bằng tên/mã Unicode và hướng dẫn tạo bằng cách dán từ file text. Chuỗi rất dài mô tả bằng công thức kèm tổng độ dài, ví dụ "`Aa@1` + 996 ký tự `x` (tổng 1000 ký tự)".
- **Expected Result**: đánh số, kiểm chứng được. Với lỗi: ghi **nguyên văn message** (đặt trong dấu nháy) và vị trí hiển thị; thêm hệ quả (không tạo bản ghi, dữ liệu đã nhập không mất). Với thành công: nêu hành vi quan sát được (chuyển trang, bản ghi được tạo, đăng nhập được). Nếu kết quả chưa chắc chắn, ghi rõ chấp nhận được các phương án nào thay vì chọn bừa.
- **Status / Bug ID**: để trống cho tester (các ô ☐). Ở bảng tổng hợp ghi `Chưa chạy`.

## Quy ước
- Bảng tổng hợp và card phải khớp nhau về ID, số lượng và thứ tự.
- Cột "Dữ liệu chính" ở bảng tổng hợp chỉ ghi giá trị đang được test (không lặp toàn bộ dữ liệu nền).
- Message lỗi trong report là message do người dùng chốt hoặc do Claude đề xuất ở Pha 1; nếu spec có sẵn thì dùng nguyên văn spec.
- Ngôn ngữ: phần mô tả theo ngôn ngữ người dùng (mặc định tiếng Việt); Title theo cú pháp tiếng Anh.