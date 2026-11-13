# 3. Attention và Transformer

**Một câu:** attention cho mỗi token nhìn sang các token khác và lấy về thông tin nó cần, biến vector "tra bảng" thành vector hiểu ngữ cảnh.

## Vấn đề attention giải quyết

Sau bước embedding, "nó" có cùng một vector trong mọi câu:

- "Con mèo đuổi con chuột vì **nó** đói" → nó = mèo
- "Tôi bỏ cái áo vì **nó** rách" → nó = áo

Vector tĩnh không phân biệt được. Cần một cơ chế để "nó" kéo thông tin từ token liên quan về.

## Cơ chế: Query, Key, Value

Mỗi token tự sinh ra ba vector từ vector của chính nó (nhân với ba ma trận đã học):

| Vector | Vai trò | Ví dụ, nói bằng lời |
|---|---|---|
| Query (Q) | Tôi đang tìm gì | "nó": tìm danh từ chỉ con vật có thể đói |
| Key (K) | Tôi chứa gì | "mèo": danh từ, con vật, chủ ngữ |
| Value (V) | Thông tin tôi đưa nếu được chọn | "mèo": nội dung về con mèo |

Ba bước:

1. **Chấm điểm**: lấy Q của "nó" nhân vô hướng với K của từng token → điểm khớp.
2. **Chuẩn hoá**: softmax biến điểm thành trọng số, tổng bằng 1.
3. **Trộn**: vector mới của "nó" = tổng các V nhân trọng số.

```
Attention(Q, K, V) = softmax(Q·Kᵀ / √d) · V
```

## Ví dụ tính tay

Vector 2 chiều (d = 2). Q của "nó" = [1, 0].

| Token | K | Q·K | chia √2 | trọng số sau softmax |
|---|---|---|---|---|
| mèo | [2, 0] | 2 | 1.41 | 0.51 |
| chuột | [1, 1] | 1 | 0.71 | 0.25 |
| đuổi | [0, 1] | 0 | 0 | 0.12 |
| vì | [0, 0] | 0 | 0 | 0.12 |

Vector mới của "nó" = 0.51 × V(mèo) + 0.25 × V(chuột) + 0.12 × V(đuổi) + 0.12 × V(vì).

"nó" giờ mang phần lớn thông tin của "mèo".

## Liên tưởng: hash map mềm

| | Hash map | Attention |
|---|---|---|
| So khớp | Key phải bằng đúng | Đo độ giống giữa query và mọi key |
| Trả về | Một value | Trung bình có trọng số của mọi value |

## Vì sao chia √d

Vector càng nhiều chiều thì tích vô hướng càng lớn. Điểm lớn → softmax dồn gần hết về một token → gradient gần 0, model khó học. Chia √d giữ điểm ở khoảng vừa phải.

## Từ attention đến Transformer

Một lớp Transformer gồm hai phần:

1. **Attention**: token trao đổi thông tin với nhau.
2. **Feed-forward (MLP)**: mỗi token tự xử lý thông tin vừa nhận, không nhìn token khác.

Thêm bốn chi tiết:

- **Multi-head**: chạy nhiều attention song song, mỗi head học một kiểu quan hệ (head này nối đại từ với danh từ, head kia nối động từ với chủ ngữ...). Kết quả ghép lại.
- **Nhiều lớp chồng nhau**: vài chục lớp. Lớp thấp bắt quan hệ đơn giản (cú pháp), lớp cao bắt quan hệ trừu tượng hơn.
- **Positional encoding**: attention tự nó không biết thứ tự ("mèo đuổi chuột" và "chuột đuổi mèo" là cùng một tập token). Thông tin vị trí phải được cộng thêm vào.
- **Causal mask**: khi sinh text, token chỉ được nhìn các token **đứng trước** nó. Đây là nền của KV cache (bài 5).

## Từ vector ra token tiếp theo

Sau lớp cuối, lấy vector của token cuối cùng, nhân với một ma trận để ra **logits**: một điểm số cho mỗi token trong vocabulary. Bước sampling (bài 4) chọn một token từ đó.

Sinh text = lặp: chạy model → chọn 1 token → nối vào cuối → chạy lại. Gọi là **autoregressive**.

## Hệ quả cho AI engineer

- **Chi phí attention tăng theo bình phương độ dài**: mỗi token nhìn mọi token trước nó. Context dài gấp đôi → attention tốn gấp bốn.
- **Input chạy song song, output chạy tuần tự**: input đã biết hết nên xử lý một lượt; output phải sinh từng token. Vì vậy output token đắt và chậm hơn.
- **Trọng số attention bị chia cho nhiều token hơn khi context dài**: thêm tài liệu nhiễu là thêm cạnh tranh cho phần quan trọng.

## Hiểu nhầm hay gặp

- "Attention là xác suất hai từ đứng cạnh nhau": sai, đó là n-gram. Attention nối được hai token cách nhau hàng nghìn vị trí.
- "Q là câu hỏi của người dùng": sai. Mỗi token đều có Q, K, V riêng.
- "Trọng số attention giải thích được model nghĩ gì": chỉ một phần. Có hàng trăm head qua vài chục lớp, nhìn một head không kết luận được.

## Tự kiểm tra

1. Trong ví dụ trên, đổi K của "chuột" thành [3, 0]. "nó" sẽ nghiêng về token nào?
2. Bỏ positional encoding thì model mất khả năng gì?
3. Vì sao causal mask làm cho K, V của token cũ không đổi khi thêm token mới?
