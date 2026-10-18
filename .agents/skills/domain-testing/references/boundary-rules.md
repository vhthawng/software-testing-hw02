# Quy tắc giá trị biên (đọc ở Bước 4, 5 và khi có biến phụ thuộc)

## Mục lục
1. Định nghĩa on-point / off-point
2. Biên đóng và biên mở
3. 2-point và 3-point
4. Chọn giá trị "sát nhất" (độ mịn)
5. Biến tương tác
6. Ví dụ minh họa
7. Biên phụ thuộc và biên của đầu ra: ví dụ có số (rút tiền)

## 1. Định nghĩa
- **Biên (boundary)**: giá trị mà tại đó điều kiện trong code đổi kết quả (ví dụ `tuổi >= 18`).
- **On-point**: giá trị nằm ĐÚNG trên biên.
- **Off-point**: giá trị gần nhất nằm ở phía KIA của điều kiện, tức là thay đổi nhỏ nhất làm đảo kết quả.
- **In-point**: giá trị nằm sâu bên trong lớp (đại diện EP).
- **Out-point**: giá trị nằm sâu bên ngoài lớp.

## 2. Biên đóng và biên mở (phần dễ sai nhất)
Off-point được xác định theo *điều kiện*, không đơn giản là "số liền trước".

| Điều kiện | Loại biên | On-point | Kết quả on-point | Off-point | Kết quả off-point |
|---|---|---|---|---|---|
| `x >= 18` | đóng | 18 | đúng | 17 | sai |
| `x > 18`  | mở   | 18 | sai | 19 | đúng |
| `x <= 100`| đóng | 100 | đúng | 101 | sai |
| `x < 100` | mở   | 100 | sai | 99 | đúng |

Quy tắc kiểm tra nhanh: on-point và off-point phải cho **kết quả ngược nhau**. Nếu không, bạn đã chọn sai off-point. Đây chính là cách bắt lỗi nhầm `<` với `<=`.

Khoảng `[min, max]` có hai biên → test cả hai. Khoảng mở hai đầu `(min, max)` cũng có hai biên. Quy tắc một phía (`x >= 0`) chỉ có một biên.

## 3. 2-point và 3-point
- **2-point (mặc định)**: on-point + off-point cho mỗi biên. Đủ để phát hiện lỗi đổi toán tử.
- **3-point**: dưới / đúng / trên giá trị biên. Dùng cho tiền, an toàn, tuân thủ quy định, hoặc khi spec mơ hồ về biên bao gồm hay không. Tốn thêm khoảng 1,5 lần.
Luôn nói rõ đã dùng loại nào.

## 4. Độ mịn ("giá trị sát nhất" là bao nhiêu)
- Số nguyên: ±1.
- Số thập phân p chữ số: ±10^-p (ví dụ giá 2 chữ số thập phân → ±0,01).
- Ngày: ±1 ngày (hoặc ±1 đơn vị độ chính xác lưu trữ: giây, ms).
- Độ dài chuỗi: ±1 ký tự.
- Số thực dấu phẩy động không nêu độ chính xác: hỏi lại, hoặc giả định một độ chính xác và ghi rõ.
Khi liên quan, thêm các cực trị của kiểu dữ liệu thành lớp riêng (int32 max 2147483647 và tràn 2147483648, chuỗi rỗng, độ dài tối đa của cột DB).

## 5. Biến tương tác
Single Fault Assumption hoạt động tốt khi các biến độc lập. Khi có quy tắc liên kết giữa các biến:
- **Quy tắc quan hệ** (`bắt_đầu <= kết_thúc`, `min <= max`): coi quan hệ là một biên riêng. Giữ một biến ở giá trị nominal hợp lệ, dịch biến còn lại qua biên quan hệ (on-point `bắt_đầu == kết_thúc`, off-point `bắt_đầu = kết_thúc + 1 đơn vị`). Mỗi test vẫn chỉ đổi một *kết quả quan hệ*, dù hai giá trị thô có thể khác nominal. Ghi chú điều này trong ma trận.
- **Khoảng có điều kiện** (giới hạn phụ thuộc phân loại): phân vùng theo biến điều khiển trước, rồi áp dụng BVA bên trong từng phân vùng riêng. Không gộp các khoảng của các phân vùng khác nhau.
- **Ràng buộc tổng** (`a + b <= 100`): chọn điểm nằm trên đường ràng buộc (on) và một đơn vị vượt qua (off); đổi một biến và chỉ bù biến kia khi cần thiết.
- Khi tương tác nhiều, hãy nói rõ và khuyến nghị bổ sung pairwise/decision-table testing.

## 6. Ví dụ minh họa
Yêu cầu: Tuổi (số nguyên) từ 18 đến 60 bao gồm hai đầu; Thu nhập (thập phân, 2 chữ số) phải > 0 và <= 10.000,00.

Các biên:
- Tuổi: `>=18` đóng → on 18, off 17; `<=60` đóng → on 60, off 61.
- Thu nhập: `>0` mở → on 0,00, off 0,01; `<=10000` đóng → on 10000,00, off 10000,01.

Nominal hợp lệ: Tuổi 30, Thu nhập 5000,00.

| TC | Tuổi | Thu nhập | Phủ | Hợp lệ? |
|---|---|---|---|---|
| 1 | 30 | 5000,00 | nominal hợp lệ | Có |
| 2 | 18 | 0,01 | Tuổi min on, Thu nhập min (phía hợp lệ) | Có |
| 3 | 60 | 10000,00 | Tuổi max on, Thu nhập max on | Có |
| 4 | **17** | 5000,00 | Tuổi min off | Không |
| 5 | **61** | 5000,00 | Tuổi max off | Không |
| 6 | 30 | **0,00** | Thu nhập min on (biên mở → không hợp lệ) | Không |
| 7 | 30 | **10000,01** | Thu nhập max off | Không |
| 8 | **abc** | 5000,00 | Tuổi sai kiểu | Không |
| 9 | 30 | **-5,00** | Thu nhập âm | Không |

## 7. Biên phụ thuộc và biên của đầu ra: ví dụ có số (rút tiền)
**Biên phụ thuộc** là biên mà giá trị không cố định mà là một biểu thức của biến khác. Cách làm: (1) chọn dữ liệu nền cụ thể, (2) tính giá trị biên từ dữ liệu đó, (3) lấy on-point và off-point theo độ mịn, (4) tính expected bằng công thức. Đổi dữ liệu nền thì phải tính lại biên.

Tình huống: rút tiền với số dư (balance) `1000.10`, hạn mức tín dụng (credit limit) `3000.00`, độ mịn 0,01. Quy tắc (giả định, phải xác nhận ở Bước 0): Amount phải `> 0`; được rút nếu `Amount <= balance + credit limit`; nếu vượt thì báo "Insufficient Funds" và số dư không đổi. Số dư sau rút = `balance − Amount`; số dư âm hiển thị màu đỏ.

Các biên của **Amount** (đầu vào), tính từ dữ liệu nền:

| Biên | Giá trị biên | Loại | Điểm test | Expected (công thức) |
|---|---|---|---|---|
| Amount > 0 | 0.00 | mở | 0.00 | Invalid Input |
| | | | 0.01 | `1000.10 − 0.01 = 1000.09` |
| Amount so với balance (số dư về 0) | 1000.10 | đóng (số dư ≥ 0) | 1000.09 | `1000.10 − 1000.09 = 0.01` |
| | | | 1000.10 | `1000.10 − 1000.10 = 0.00` |
| | | | 1000.11 | `1000.10 − 1000.11 = −0.01` (hiển thị đỏ) |
| Amount so với balance + credit limit | 4000.10 | đóng (`<=`) | 4000.09 | `−2999.99` (đỏ) |
| | | | 4000.10 | `−3000.00` (đỏ) |
| | | | 4000.11 | Insufficient Funds, số dư vẫn `1000.10` |

Ba điểm quanh mỗi biên phụ thuộc (3-point) phù hợp ở đây vì liên quan tiền. Hai biên 1000.10 và 4000.10 đều **phụ thuộc dữ liệu nền**: nếu số dư là 500.00 thì biên đổi thành 500.00 và 3500.00.

Các lớp của **đầu ra "số dư sau rút"** (đánh số liên tục cùng bảng lớp):

| Lớp đầu ra | Điều kiện | Cách đạt được | Ghi chú |
|---|---|---|---|
| O1 | số dư sau ≥ 0 | Amount ≤ 1000.10 | không đỏ |
| O2 | −3000.00 ≤ số dư sau < 0 | 1000.10 < Amount ≤ 4000.10 | hiển thị đỏ |
| O3 | số dư sau < −3000.00 | **không đạt tới được**: Amount > 4000.10 bị chặn | kiểm tra "bị chặn và số dư không đổi", không gán O3 cho test case nào |

Ngưỡng đầu ra: −0.01 / 0.00 (O1/O2) và −3000.00 / (không đạt được −3000.01) cho ra cùng các điểm Amount đã liệt kê ở bảng trên; nhờ truy ngược này, mỗi test về đầu ra trùng với một test về biên đầu vào, nên có thể gộp.

Lưu ý nhỏ: nếu spec có hai điều kiện báo "Insufficient Funds" chồng lấn nhau (ví dụ `Amount > balance` và `Amount > balance + credit limit`), đó là **mâu thuẫn** cần xác nhận ở Bước 0 trước khi chọn biên, vì nó quyết định số dư 1000.11 là rút được hay bị chặn.