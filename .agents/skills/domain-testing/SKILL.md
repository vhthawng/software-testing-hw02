---
name: domain-testing
description: Thiết kế bộ test case gọn nhưng hiệu quả bằng Domain Testing (Phân vùng tương đương - Equivalence Partitioning, Phân tích giá trị biên - Boundary Value Analysis, Giả định lỗi đơn - Single Fault Assumption) và xuất ra Ma trận Test (Test Matrix) để review, sau đó xuất test report hoàn chỉnh dạng file .md (bảng tổng hợp + chi tiết từng test case). Hãy dùng skill này bất cứ khi nào người dùng yêu cầu thiết kế, review hoặc giảm số lượng test case cho ô nhập liệu, form, tham số, field của API, hàm có khoảng giá trị/giới hạn/danh sách giá trị cho phép, hoặc quy tắc nghiệp vụ có ngưỡng — kể cả khi họ không nhắc đến "domain testing". Kích hoạt với các cụm như "thiết kế test case", "phân vùng tương đương", "lớp tương đương", "giá trị biên", "on-point/off-point", "ma trận test", "test field chấp nhận từ A đến B", "giảm số lượng test case", "boundary value", "equivalence partitioning", "test matrix", hoặc khi yêu cầu chứa khoảng min/max, giới hạn độ dài, danh sách giá trị hợp lệ, validate số/ngày cần kiểm thử.
---

# Domain Testing

Biến một yêu cầu (field, tham số, quy tắc) thành một **Ma trận Test (Test Matrix)** nhỏ gọn nhưng vẫn bắt được những lỗi nghiêm trọng nhất. Chất lượng đầu ra phụ thuộc vào việc *mô hình hóa miền dữ liệu đầu vào* có đúng hay không, vì vậy hãy dồn công sức vào bước mô hình hóa trước, sau đó mới sinh test case một cách máy móc.

## Luồng làm việc hai pha

- **Pha 1 — Review (trong chat):** phân tích miền dữ liệu (giả định, biến, lớp tương đương, biên) và đề xuất Test Matrix để người dùng xem xét. Người dùng sẽ phản hồi và Claude điều chỉnh, lặp lại cho đến khi họ chốt. Pha này **không tạo file**.
- **Pha 2 — Report cuối (file `.md`):** khi người dùng chốt (hoặc yêu cầu "xuất report"/"output file"), tạo **test report** theo `references/report-template.md`. File này **không chứa** các phần phân tích EP/BVA; chỉ gồm bảng tổng hợp và các card test case chi tiết.

Nếu người dùng yêu cầu thẳng report mà chưa qua review, vẫn làm Pha 1 ngắn gọn (nêu giả định đề xuất), rồi xuất report và đánh dấu rõ giả định chưa xác nhận.

Mặc định trả lời bằng tiếng Việt; nếu người dùng viết tiếng Anh thì trả lời tiếng Anh. Luôn giữ thuật ngữ gốc tiếng Anh trong ngoặc ở lần xuất hiện đầu tiên (ví dụ: "lớp tương đương (equivalence class)") vì đội QA thường dùng cả hai.

## Ba trụ cột (và lý do của từng bước)

1. **Phân vùng tương đương (EP)** — Chia miền giá trị của mỗi biến thành các lớp (hợp lệ và không hợp lệ) mà chương trình được giả định xử lý giống nhau với mọi giá trị trong lớp. Chỉ cần test 1 giá trị đại diện cho mỗi lớp. Đây là bước giúp bộ test nhỏ lại.
2. **Phân tích giá trị biên (BVA)** — Lỗi tập trung ở ranh giới giữa các lớp vì lập trình viên dễ gõ nhầm `<` thành `<=`. Test giá trị nằm đúng trên biên (on-point) và giá trị sát bên kia điều kiện (off-point).
3. **Giả định lỗi đơn (Single Fault Assumption)** — Trong test case *không hợp lệ*, chỉ có đúng 1 biến sai, các biến còn lại giữ giá trị đúng. Nếu không, lỗi này có thể che lấp lỗi kia (masking effect) và không xác định được kiểm tra nào đã báo lỗi.

## Quy trình

Làm lần lượt theo thứ tự. Không nhảy thẳng đến bảng test khi chưa xong bước 1–2, vì phần lớn bộ test tệ đều xuất phát từ mô hình miền dữ liệu sai hoặc thiếu.

### Bước 0 — Nạp và đọc tài liệu của SUT (bắt buộc, làm trước mọi phân tích)

Mọi quy tắc trong test phải bắt nguồn từ tài liệu của hệ thống đang test (SUT), không từ trí nhớ hay phỏng đoán về cách sản phẩm "thường" hoạt động (ngay cả với sản phẩm phổ biến như OrangeHRM, mỗi bản/cấu hình có thể khác).

1. **Kiểm kê nguồn.** Liệt kê mọi nguồn có sẵn: yêu cầu/SRS/user story, tài liệu thiết kế, API docs/schema, mô tả DB, mockup/ảnh chụp màn hình, danh mục thông báo lỗi, test case hoặc bug cũ, và bản chạy thật của SUT nếu truy cập được. Nếu người dùng chỉ đưa ảnh màn hình hoặc một câu mô tả, nói rõ đó là toàn bộ nguồn.
2. **Đọc có mục tiêu nhưng không bỏ sót.** Đọc kỹ toàn bộ phần mô tả field/chức năng đang test. Sau đó tìm thêm các phần liên quan ở nơi khác: quy tắc validate chung (global rules), bảng thuật ngữ, danh mục message, ràng buộc DB/API, quy tắc phân quyền. Với tài liệu dài, dùng tìm kiếm từ khóa (tên field, "max", "min", "required", "format", "error") để lần theo tham chiếu chéo. Không chỉ đọc đoạn đầu tiên khớp.
3. **Trích nguồn cho từng quy tắc.** Với mỗi quy tắc rút ra, ghi nguồn cụ thể (tên tài liệu, mã yêu cầu, mục/trang/dòng). Phân loại: *nêu rõ* (explicit), *suy ra* (implied, ghi suy ra từ đâu), hoặc *không có trong tài liệu* (chuyển thành giả định ở Bước 2).
4. **Phát hiện mâu thuẫn và khoảng trống.** So khớp giữa các nguồn (spec và UI, spec và API, spec và DB). Mọi mâu thuẫn (ví dụ spec ghi tối đa 50 ký tự, cột DB là 30) và mọi hành vi không được mô tả (hành vi khi nhập sai, thông báo lỗi, bỏ trống) đều đưa vào bảng giả định với trạng thái `Mâu thuẫn` hoặc `Chưa rõ`, không tự chọn một bên.
5. **Báo cáo phạm vi đã đọc.** Nêu ngắn gọn đã đọc những tài liệu nào, phần nào, và phần nào chưa đọc/không truy cập được. Nếu thiếu tài liệu quan trọng, nói rõ và đề nghị người dùng bổ sung; nếu họ không có, tiếp tục nhưng đánh dấu các quy tắc không có nguồn là giả định.
6. **Không bịa mã yêu cầu.** Chỉ dùng mã (SRS-x.x, BR-x) có thật trong tài liệu. Nếu không có, ghi tên tài liệu và mục.

### Bước 1 — Trích xuất biến và quy tắc
Từ tài liệu đã đọc ở Bước 0, liệt kê mọi biến đầu vào (và đầu ra/trạng thái liên quan) gồm: tên, kiểu dữ liệu, đơn vị, quy tắc nêu trong yêu cầu, và **nguồn** của quy tắc đó. Bao gồm cả ràng buộc ngầm (giới hạn kiểu dữ liệu, bắt buộc/tùy chọn, có trim hay không, bộ ký tự, độ chính xác). Trích dẫn nguyên văn yêu cầu gốc kèm nguồn.

### Bước 2 — Giải quyết điểm mơ hồ trước khi mô hình hóa
Kiểm tra các chỗ thiếu: biên là bao gồm hay không bao gồm (inclusive/exclusive)? số nguyên hay thập phân? xử lý null/rỗng/khoảng trắng thế nào? hành vi báo lỗi cụ thể ra sao?
- Nếu người dùng có thể trả lời nhanh, hãy hỏi **một** câu gộp, ngắn gọn.
- Nếu không, ghi rõ danh sách "Giả định" và tiếp tục. Tuyệt đối không âm thầm chọn một cách hiểu — hiểu sai biên đóng/mở sẽ làm sai toàn bộ test biên.

**Bảng giả định có trạng thái.** Mỗi giả định có một mã (A1, A2…) và một trạng thái, để người dùng biết điều gì đến từ spec, điều gì do bạn đề xuất:
- `Từ spec`: nêu trực tiếp trong tài liệu (trích dẫn kèm nguồn).
- `Mâu thuẫn`: các nguồn nói khác nhau (nêu rõ nguồn nào nói gì); chờ người dùng quyết định.
- `Đề xuất của Claude`: spec không nói, bạn tự chọn một giá trị hợp lý (ví dụ giới hạn tên file 255 ký tự, nội dung thông báo lỗi). Phải gắn nhãn rõ và nêu test case nào bị ảnh hưởng nếu sai.
- `Đã xác nhận`: người dùng đã trả lời.
- `Chưa rõ`: chờ xác nhận.

**Khi người dùng trả lời các giả định (vòng lặp cập nhật):**
1. Giữ nguyên mã giả định và bảng cũ; chỉ đổi trạng thái sang `Đã xác nhận` và ghi nội dung đã chốt. Không viết lại bảng từ đầu hay làm mất giả định chưa được trả lời.
2. Diễn giải đúng từng câu trả lời ngắn gọn; nếu một câu trả lời còn nhập nhằng (ví dụ "nên phân biệt" có thể là bắt buộc hoặc chỉ cảnh báo), chọn cách hiểu hợp lý nhất, gắn nhãn `Đề xuất của Claude (diễn giải)` và hỏi lại đúng điểm đó.
3. Khi người dùng giao cho bạn quyết định ("cho bạn quyết định"), hãy quyết định, ghi nhãn `Đề xuất của Claude` và trình bày giá trị đã chọn (ví dụ bảng message lỗi M1, M2… để tham chiếu trong ma trận).
4. Cập nhật ma trận theo giả định mới: đổi on/off-point, đổi kết quả mong đợi, thêm/xóa lớp. Nêu ngắn gọn "Thay đổi so với bản trước" (đổi gì, vì sao) để người dùng rà soát nhanh.

### Bước 3 — Phân vùng từng biến
Lập bảng EP cho mỗi biến: mã lớp, mô tả, hợp lệ/không hợp lệ, giá trị đại diện. **Đánh số lớp liên tục xuyên suốt mọi biến đầu vào và đầu ra** (1, 2, 3…, không bắt đầu lại từ 1 cho mỗi biến) để mã lớp dùng được trong cột "Lớp phủ" của ma trận. Luôn cân nhắc các lớp không hợp lệ sau khi phù hợp: dưới min, trên max, sai kiểu/định dạng, null/rỗng, ký tự đặc biệt. Với miền không phải số (chuỗi, enum, ngày, tập hợp, field upload file) hãy đọc `references/domain-types.md`; field upload có mục riêng (mục 7) vì thực chất gồm nhiều biến độc lập.

### Bước 4 — Phân vùng đầu ra (output domain)
Đầu ra cũng có lớp và có biên; nhiều lỗi nằm ở đây (tính sai, chọn sai message, sai hiển thị) mà phân vùng đầu vào đơn thuần không bắt được. Với mỗi đầu ra đã liệt kê ở Bước 1:
1. **Phân loại đầu ra:** giá trị tính toán (số dư, tổng tiền, phí), thông báo/mã trạng thái, thay đổi trạng thái hoặc dữ liệu (bản ghi, số dư lưu), hiển thị (màu sắc, ẩn/hiện), tác dụng phụ (in biên lai, gửi email).
2. **Chia lớp theo ngưỡng của đầu ra** (ví dụ số dư sau rút: ≥ 0, âm nhưng trong hạn mức, vượt hạn mức) và theo từng message/trạng thái có thể xuất hiện. Các lớp này cũng được đánh số liên tục cùng bảng lớp ở Bước 3.
3. **Truy ngược về đầu vào:** với mỗi lớp và mỗi ngưỡng đầu ra, tìm giá trị đầu vào (kể cả dữ liệu nền) làm đầu ra rơi đúng vào lớp đó và vào hai phía ngưỡng. Ngưỡng của đầu ra thường là một **biên phụ thuộc** (hàm của nhiều biến): xem mục 7 của `references/boundary-rules.md`.
4. **Tính expected bằng công thức** và ghi công thức vào ma trận (ví dụ `1000.10 − 2000.05 = −999.95`); tính lại từng expected trước khi xuất. Không ước lượng.
5. **Chỉ gán lớp đầu ra cho test case khi quan sát được trong expected.** Lớp "không được xảy ra" (ví dụ số dư vượt hạn mức) không thể phủ bằng giá trị đầu ra; phủ bằng cách kiểm tra rằng yêu cầu bị chặn và dữ liệu không đổi. Ghi rõ đây là lớp "không đạt tới được" thay vì gán nhầm cho một test case không liên quan.

### Bước 5 — Chọn giá trị biên
Với mỗi biên có thứ tự (của đầu vào **và** của đầu ra), áp dụng chọn on-point/off-point; với biên phụ thuộc (biên là một biểu thức của biến khác) tính giá trị biên từ dữ liệu nền cụ thể trước khi chọn điểm. Đọc `references/boundary-rules.md` để nắm quy tắc biên đóng/mở và cách chọn 2-point hay 3-point. Mặc định dùng **2-point** (on + off) cho mỗi biên; dùng **3-point** (dưới/trên biên/trên nữa) với logic tài chính hoặc an toàn, và phải nói rõ đã dùng loại nào.

### Bước 6 — Kết hợp giữa các biến và lập bộ test hai tầng
Làm theo hai tầng để vừa bảo đảm phủ đủ, vừa giữ bộ test nhỏ:

**Tầng 1 — Phủ từng lớp (bảng nháp, để kiểm tra nội bộ):** với mỗi lớp (đầu vào, đầu ra, biên) chọn ít nhất một giá trị đại diện; đảm bảo không còn lớp nào chưa có giá trị.

**Tầng 2 — Gộp thành bộ tối thiểu (đây là ma trận đưa ra review):**
- **Test case hợp lệ:** gộp **nhiều lớp hợp lệ vào cùng một test case** miễn là các lớp đó không loại trừ nhau (khác biến, không mâu thuẫn). Một test case hợp lệ thường phủ đồng thời lớp đầu vào của nhiều biến và một lớp đầu ra, ví dụ TC phủ lớp 2, 10, 17. Bắt đầu từ một test case nominal, thêm test case hợp lệ khác chỉ để phủ các lớp/biên hợp lệ còn lại.
- **Test case không hợp lệ:** **không gộp**. Mỗi test case chỉ chứa một giá trị sai, các biến khác ở giá trị nominal hợp lệ (Single Fault Assumption). Một test case không hợp lệ chỉ phủ lớp sai đó và lớp đầu ra tương ứng (message lỗi).
- **Cột "Lớp phủ"** liệt kê **tất cả** mã lớp mà test case thật sự phủ (kể cả lớp đầu ra), ví dụ `1, 6, 16`. Mỗi lớp phải xuất hiện ít nhất một lần; mỗi mã trong cột phải quan sát được trong expected (xem Bước 4, mục 5).
- **Biến phụ thuộc nhau** (ví dụ `ngày_bắt_đầu <= ngày_kết_thúc`, `Amount` so với `balance + credit limit`): không áp dụng Single Fault một cách máy móc vì đổi một giá trị có thể làm đổi tính hợp lệ của giá trị khác. Mô hình hóa quan hệ tường minh và đọc mục "Biến tương tác" và mục 7 trong `references/boundary-rules.md`.
- Bảng tầng 1 là công cụ làm việc; chỉ đưa ma trận tầng 2 ra review, kèm bảng truy vết ngắn "lớp → test case" ở phần tóm tắt độ phủ.

### Bước 7 — Xuất kết quả

**Pha 1 (review, trong chat)** dùng cấu trúc:

```
# Domain Testing — <tính năng/field>
## 0. Tài liệu đã đọc (nguồn, phần đã đọc, phần chưa đọc/thiếu)
## 1. Giả định (có mã, nguồn và trạng thái) & câu hỏi còn mở
## 2. Biến & quy tắc (kèm nguồn)
## 3. Các lớp tương đương (mỗi biến một bảng)
## 4. Giá trị biên (bảng: biên, loại đóng/mở, on-point, off-point)
## 5. Ma trận Test
## 6. Tóm tắt độ phủ & thay đổi so với bản trước
```

Bảng giả định có cột: `Mã | Nội dung | Nguồn | Trạng thái | Ảnh hưởng nếu sai`. Nếu spec không nêu thông báo lỗi, thêm bảng message đề xuất (M1, M2…) và tham chiếu mã message trong cột kết quả mong đợi.

Các cột của Ma trận Test: `TC ID | Biến đang test | Giá trị đầu vào (tất cả biến) | Lớp phủ (mã lớp, gồm cả đầu ra) | Hợp lệ/Không hợp lệ | Kết quả mong đợi (kèm công thức tính nếu là giá trị tính toán)`.
- In đậm biến bị thay đổi để thấy ngay nguyên tắc lỗi đơn.
- Kết quả mong đợi phải cụ thể (thông báo lỗi, mã trạng thái, hành vi được chấp nhận). Nếu spec không nói, ghi "theo spec - cần xác nhận" và đưa vào Bước 1, không tự bịa.
- Mỗi lớp (đầu vào và đầu ra) và mỗi giá trị biên phải xuất hiện trong ít nhất 1 test case (truy vết); thêm bảng "lớp → test case" ngắn để người review kiểm tra độ phủ. Kết thúc bằng tóm tắt định lượng, ví dụ: "3 biến, 11 lớp, 8 điểm biên → 14 test case so với ~N tổ hợp đầy đủ".
- Kết thúc phản hồi bằng các điểm còn cần xác nhận (nếu có) và nhắc người dùng có thể yêu cầu xuất report khi đã chốt.

**Pha 2 (report cuối)**: đọc `references/report-template.md` và làm theo đúng. Tóm tắt các quy tắc chính:
- Một file `.md` cho mỗi field/tính năng; lưu vào `/mnt/user-data/outputs/` rồi dùng `present_files`.
- **Không đưa vào report** các bảng EP, bảng biên, bảng giả định, mã lớp (E1, S2…) hay mã giả định. Mọi thứ cần thiết cho tester phải nằm trong card (Purpose, Setup, Steps, Expected).
- Gồm đúng hai phần: bảng tổng hợp mọi test case, và các card chi tiết theo template.
- Mục `Nguồn yêu cầu` và `Purpose` của report trích đúng tài liệu/mã yêu cầu đã đọc ở Bước 0 (không bịa mã).
- Chỉ đưa vào report các test case người dùng đã chấp nhận ở Pha 1; phản ánh mọi điều chỉnh của họ.

## Checklist chất lượng (chạy trước khi trả lời)
- [ ] (Bước 0) Đã kiểm kê và đọc tài liệu SUT; mỗi quy tắc có nguồn; mâu thuẫn và khoảng trống đã vào bảng giả định; nêu rõ phần chưa đọc/thiếu.
- [ ] Mỗi biên được gắn nhãn đóng/mở; on-point và off-point nằm đúng phía.
- [ ] Không test case không hợp lệ nào chứa quá một giá trị sai (trừ khi có lý do cho biến phụ thuộc).
- [ ] Mỗi lớp (kể cả lớp đầu ra) và mỗi biên truy vết được tới ≥1 test case; lớp gán cho test case đều quan sát được trong expected.
- [ ] Đầu ra đã được phân vùng (Bước 4); mọi expected dạng tính toán đã được tính lại bằng công thức.
- [ ] Biên phụ thuộc đã được tính ra giá trị cụ thể từ dữ liệu nền (không để dạng biểu thức).
- [ ] Giá trị phù hợp kiểu dữ liệu (số nguyên thì không có off-point thập phân; số thập phân dùng bước nhỏ nhất cho phép).
- [ ] Đã nêu rõ giả định; mỗi giả định có mã và trạng thái (`Từ spec` / `Đề xuất của Claude` / `Đã xác nhận` / `Chưa rõ`).
- [ ] Không bịa kết quả mong đợi; giá trị do mình đề xuất (giới hạn, message) đều được gắn nhãn.
- [ ] Sau khi người dùng xác nhận giả định, ma trận đã được cập nhật và có phần "Thay đổi so với bản trước".
- [ ] Mức giảm được định lượng, không chỉ khẳng định suông.
- [ ] (Pha 2) Report không chứa phân tích domain testing; số dòng trong bảng tổng hợp bằng số card chi tiết; ID khớp nhau; mỗi card tự đứng được (tester không cần xem tài liệu khác).

## Giới hạn đã biết (nói thẳng với người dùng)
Domain testing giả định hành vi đồng nhất trong một lớp và lỗi là lỗi đơn, thường nằm ở biên. Kỹ thuật này **không** tìm được: lỗi do tương tác giữa các biến (dùng pairwise/combinatorial testing), lỗi theo trạng thái/trình tự (dùng state-transition testing), và lỗi nằm giữa lớp. Khi tính năng rõ ràng có các yếu tố này, hãy nhắc và gợi ý kỹ thuật bổ sung trong một dòng.