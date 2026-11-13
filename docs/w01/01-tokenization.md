# 1. Vocabulary và tokenization

**Một câu:** model không đọc chữ, nó đọc dãy số. Tokenizer là bước đổi text thành dãy số đó, và cách đổi quyết định chi phí, tốc độ và giới hạn context.

## Ba khái niệm

| Khái niệm | Là gì | Ví dụ |
|---|---|---|
| Token | Một mẩu text: có thể là cả từ, một phần từ, một ký tự, một dấu cách | `low`, `est`, ` the` |
| Vocabulary | Bảng cố định gồm mọi token model biết, mỗi token một số thứ tự (ID) | 50.000–200.000 dòng |
| Tokenizer | Chương trình cắt text thành token rồi tra ID | `"lowest"` → `["low","est"]` → `[5, 3]` |

Token ID chỉ là số thứ tự, giống primary key. ID 812 không "gần nghĩa" với ID 813. Nghĩa nằm ở embedding (bài 2).

## Vì sao không dùng luôn từ hoặc ký tự

| Cách cắt | Vấn đề |
|---|---|
| Theo từ | Vocabulary vô hạn: tên riêng, từ mới, lỗi chính tả → gặp từ lạ là bó tay |
| Theo ký tự | Không bao giờ gặp từ lạ, nhưng dãy quá dài → tốn compute, khó học |
| Theo subword (BPE) | Ở giữa: từ phổ biến là 1 token, từ hiếm được ghép từ vài mẩu |

Hầu hết LLM hiện nay dùng subword, phổ biến nhất là BPE (Byte Pair Encoding).

## BPE xây vocabulary thế nào

Thuật toán: bắt đầu từ ký tự đơn, lặp lại việc **gộp cặp đứng cạnh nhau xuất hiện nhiều nhất** thành một token mới.

Ví dụ với corpus nhỏ (`_` đánh dấu hết từ):

```
low ×5   lower ×2   newest ×6   widest ×3
```

Ban đầu mỗi từ là dãy ký tự: `l o w _`, `n e w e s t _`, ...

| Bước | Cặp nhiều nhất | Số lần | Token mới |
|---|---|---|---|
| 1 | `e` + `s` | 9 | `es` |
| 2 | `es` + `t` | 9 | `est` |
| 3 | `est` + `_` | 9 | `est_` |
| 4 | `l` + `o` | 7 | `lo` |
| 5 | `lo` + `w` | 7 | `low` |
| 6 | `n` + `e` | 6 | `ne` |

Sau 6 bước:

```
low    → [low, _]
lower  → [low, e, r, _]
newest → [ne, w, est_]
widest → [w, i, d, est_]
```

Điều cần thấy:

- `est_` thành một token vì xuất hiện nhiều (newest, widest). `low` cũng vậy.
- `lower` chưa được gộp hết vì `er` hiếm trong corpus này.
- Gặp từ chưa từng thấy như `lowest`, tokenizer vẫn cắt được: `[low, est_]`.

**Cái gì xuất hiện nhiều trong dữ liệu huấn luyện thì được token ngắn gọn.** Đây là chìa khoá cho phần tiếng Việt bên dưới.

Tokenizer thật chạy đúng thuật toán này, nhưng trên byte UTF-8 thay vì ký tự, với hàng chục nghìn bước gộp trên dữ liệu khổng lồ.

## Vì sao tiếng Việt tốn token hơn tiếng Anh

**1. Dữ liệu huấn luyện chủ yếu là tiếng Anh.** Từ tiếng Anh phổ biến được gộp thành 1 token. Âm tiết tiếng Việt xuất hiện ít hơn nhiều → ít lượt gộp hơn → hay bị cắt vụn.

**2. Ký tự có dấu tốn nhiều byte.** BPE bắt đầu từ byte:

| Text | Ký tự | Byte UTF-8 |
|---|---|---|
| `e` | 1 | 1 |
| `ế` | 1 | 3 |
| `research` | 8 | 8 |
| `nghiên cứu` | 10 | 13 |
| `I like this game` | 16 | 16 |
| `Tôi thích game này` | 18 | 21 |

Điểm xuất phát đã dài hơn, lại cần nhiều lượt gộp hơn mới thành 1 token. Tiếng Việt có rất nhiều tổ hợp nguyên âm + dấu (`ế, ề, ể, ễ, ệ, ...`), không phải tổ hợp nào cũng đủ phổ biến.

**3. Tiếng Việt viết theo âm tiết.** `research` là một từ liền; `nghiên cứu` là hai âm tiết cách nhau dấu cách → tối thiểu 2 token.

Tỉ lệ chênh cụ thể tuỳ tokenizer. Đây là bài tập 1: tự đo.

## Hệ quả cho AI engineer

- **Chi phí và giới hạn tính theo token**, không theo ký tự hay từ. Sản phẩm tiếng Việt: ước lượng lại ngân sách, đừng dùng quy đổi của tiếng Anh.
- **Mỗi model một tokenizer.** Cùng đoạn text, model khác nhau cho số token khác nhau. Đổi model → đếm lại.
- **Model kém ở việc cấp ký tự** (đếm chữ cái, đảo chuỗi) vì nó thấy token, không thấy ký tự.
- **Số, ID, mã** thường bị cắt vụn khó đoán: `20261113` có thể thành 3–4 token.
- **Dấu cách và hoa/thường tạo token khác nhau**: `game`, ` game`, `Game` thường là 3 ID riêng.

## Hiểu nhầm hay gặp

- "1 token = 1 từ": sai. Từ dài hoặc hiếm thành nhiều token.
- "Token ID mang nghĩa": sai. Chỉ là số thứ tự.
- "Tokenizer là một phần của mạng neural": sai. Nó là chương trình tất định chạy trước model, cùng input luôn cho cùng output.

## Tự kiểm tra

1. Vì sao BPE không bao giờ gặp "từ lạ"?
2. Corpus trên thêm `slower ×10` thì cặp nào nhiều khả năng được gộp sớm hơn?
3. Vì sao đếm ký tự rồi chia 4 là ước lượng tệ cho tiếng Việt?
