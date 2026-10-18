# Phân vùng cho miền không phải số và miền đặc biệt (đọc ở Bước 3)

## Mục lục
1. Chuỗi / văn bản
2. Enum / dropdown
3. Ngày và giờ
4. Tập hợp (danh sách, multi-select)
5. Boolean và field tùy chọn
6. Phân vùng phía đầu ra
7. Upload file (ảnh, tài liệu, đính kèm)

## 1. Chuỗi
Phân vùng theo từng thuộc tính độc lập, mỗi test chỉ đổi một thuộc tính (lỗi đơn):
- Độ dài: rỗng, 1 ký tự, min, max, max+1. Dùng on/off-point cho giới hạn độ dài.
- Nhóm ký tự: chữ cái, chữ số, khoảng trắng (đầu/cuối/giữa), Unicode/dấu (quan trọng với tiếng Việt: "Nguyễn", "Đặng"), emoji, ký tự đặc biệt, ký tự meta của SQL/HTML (chỉ như lớp kiểm tra validate đầu vào).
- Định dạng: tính hợp lệ theo mẫu (email, số điện thoại, mã bưu chính): mỗi quy tắc bị vi phạm là một lớp không hợp lệ (thiếu @, hai @, thiếu domain…).
- Chuỗi chỉ gồm khoảng trắng, null và rỗng là các lớp riêng nếu spec xử lý khác nhau.

## 2. Enum / dropdown
- Mỗi giá trị cho phép một đại diện nếu hành vi khác nhau theo giá trị; nếu cùng hành vi thì gộp nhóm.
- Không hợp lệ: giá trị ngoài danh sách, để trống/chưa chọn, khác hoa-thường (nếu phân biệt), giá trị bị chỉnh sửa (API).
- Không có biên trừ khi danh sách có thứ tự.

## 3. Ngày và giờ
- Miền có thứ tự → BVA với đơn vị = độ chính xác lưu trữ (thường là ngày).
- Các lớp đặc biệt: ngày nhuận (29/2 hợp lệ hay không tùy năm), cuối tháng (30/31), chuyển năm, đổi giờ mùa hè (DST), mốc múi giờ (23:59:59 và 00:00:00), quá khứ/hôm nay/tương lai.
- Sai định dạng và ngày không tồn tại (31/4) là các lớp không hợp lệ.

## 4. Tập hợp
- Kích thước: rỗng, 1, min, max, max+1.
- Nội dung: tất cả hợp lệ, một phần tử không hợp lệ (lỗi đơn), trùng lặp (nếu liên quan).
- File upload: xem mục 7.

## 5. Boolean và field tùy chọn
- Boolean: true / false (+ thiếu/null với API).
- Field tùy chọn: có và hợp lệ, bỏ qua, có nhưng rỗng, có nhưng không hợp lệ.

## 6. Phân vùng phía đầu ra
Xem **Bước 4** trong `SKILL.md` và mục 7 trong `boundary-rules.md` (ví dụ có số). Tóm tắt: đầu ra cũng được chia lớp và có biên (bậc thuế, mức chiết khấu, đạt/không đạt, số dư theo ngưỡng); chọn đầu vào bằng cách truy ngược để đầu ra rơi đúng từng lớp và hai phía ngưỡng; chỉ gán lớp đầu ra cho test case khi quan sát được trong expected.

## 7. Upload file (ảnh, tài liệu, đính kèm)
Một field upload thực chất là **nhiều biến độc lập gộp trong một control**. Hãy tách thành các biến sau, mỗi test chỉ làm sai một biến (lỗi đơn). Nominal hợp lệ: một file đúng định dạng, dung lượng khoảng nửa giới hạn, tên ASCII ngắn, 1 file.

### 7.1. Dung lượng
- Đây là biên số nguyên theo **byte**. Với "up to X MB", biên đóng: on = X, off = X + 1 byte.
- **Phải chốt 1MB là bao nhiêu byte** (1.048.576 theo hệ nhị phân hay 1.000.000 theo hệ thập phân). Đây là giả định hay làm lệch cả on/off-point nên luôn đưa vào danh sách giả định (Bước 2).
- Lớp cần có: 0 byte (rỗng), 1 byte hoặc file hợp lệ nhỏ nhất (biên min), vùng hợp lệ, on-point max, off-point max, cực lớn (vd 10× giới hạn).
- Cách tạo file đúng số byte: lấy file hợp lệ rồi thêm byte đệm (`truncate`, `dd`), kiểm tra lại bằng `ls -l`, và xác nhận file vẫn mở được.

### 7.2. Định dạng: tách ba thuộc tính
- **Phần mở rộng** (`.jpg`, `.png`, `.gif`): đại diện cho từng định dạng được phép; thêm các lớp không hợp lệ: ảnh thật nhưng định dạng khác (`.bmp`), không phải ảnh (`.txt`, `.pdf`), không có đuôi, đuôi kép (`photo.jpg.exe`).
- **Hoa/thường của đuôi**: `JPG`, `Png`. Hành vi tùy hệ thống nên **phải xác nhận** có phân biệt hay không, không tự đoán. Nếu đã chốt "phân biệt" thì đây là lớp không hợp lệ; nếu "không phân biệt" thì là lớp hợp lệ.
- **Biến thể đuôi không có trong spec** (như `.jpeg` khi spec chỉ ghi `jpg`): mặc định coi là không hợp lệ theo nguyên tắc "không liệt kê thì không chấp nhận", nhưng vẫn ghi vào giả định để xác nhận.
- **Nội dung thực** so với đuôi: file `.txt` đổi tên thành `.jpg` là lớp riêng (hệ thống có kiểm tra magic bytes/nội dung hay chỉ kiểm tra đuôi).

### 7.3. Kích thước ảnh (pixel)
- Nếu spec chỉ ghi "recommended" thì **không phải quy tắc validate**: mọi kích thước đều hợp lệ. Vẫn chọn các đại diện (đúng kích thước khuyến nghị, rất nhỏ như 1×1, rất lớn, khác tỷ lệ) để quan sát hiển thị (méo, cắt, co giãn), và ghi rõ đây là kết quả *quan sát*, không tính fail.
- Chỉ áp dụng BVA cho rộng/cao khi spec có giới hạn bắt buộc.

### 7.4. Tên file
- Độ dài (biên tối đa thường là 255 ký tự gồm cả đuôi nhưng **spec thường không nêu**: đánh dấu là đề xuất), ký tự Unicode/dấu tiếng Việt, khoảng trắng, ký tự đặc biệt (`#`, `%`, `&`), ký tự đường dẫn (`../`) cho kiểm tra bảo mật.

### 7.5. Số lượng file
- Không chọn (nếu field tùy chọn: hợp lệ), 1 file, nhiều hơn giới hạn (≥ 2 với field chỉ nhận 1). Test cả hộp thoại chọn file và kéo-thả vì hai đường vào này có thể validate khác nhau. Khi test nhiều file, cho cả hai file đều hợp lệ để chỉ có lỗi số lượng.

### 7.6. Phần ngoài phạm vi (nêu trong "Giới hạn")
Trình tự thao tác (chọn → hủy → chọn lại → thay ảnh), tương tác với các field khác của form khi Save, polyglot file/EXIF độc hại, tốc độ upload và mất kết nối giữa chừng: cần state-transition hoặc security/performance testing.