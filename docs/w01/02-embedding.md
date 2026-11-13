# 2. Embedding

**Một câu:** embedding đổi mỗi token ID thành một vector, và trong không gian vector đó, thứ gì nghĩa gần nhau thì nằm gần nhau.

## Cơ chế: tra bảng

Model có một bảng (embedding matrix): mỗi dòng ứng với một token trong vocabulary, mỗi dòng là một vector dài d số (d thường từ vài trăm đến vài nghìn).

```
ID 812  → [ 0.12, -0.80,  0.33, ... ]   "mèo"
ID 4401 → [ 0.10, -0.75,  0.41, ... ]   "chó"
ID 977  → [-0.62,  0.20, -0.15, ... ]   "thuế"
```

Giống hệt `SELECT vector FROM embeddings WHERE id = 812`. Không có phép tính nào.

Các con số trong bảng không ai viết tay. Chúng được **học** trong lúc huấn luyện: token nào hay xuất hiện trong ngữ cảnh giống nhau thì vector bị kéo lại gần nhau.

## Đo "gần" bằng cosine similarity

Cosine similarity = cos của góc giữa hai vector. Chỉ quan tâm hướng, không quan tâm độ dài.

```
cos(a, b) = (a · b) / (|a| × |b|)
```

Ví dụ 2 chiều, tính tay được:

| Cặp | a | b | a·b | cos |
|---|---|---|---|---|
| Cùng hướng | [1, 1] | [2, 2] | 4 | 1.0 |
| Vuông góc | [1, 0] | [0, 1] | 0 | 0.0 |
| Ngược hướng | [1, 0] | [-1, 0] | -1 | -1.0 |
| Lệch nhẹ | [1, 0] | [1, 1] | 1 | 0.71 |

## Hai loại embedding, đừng lẫn

| | Token embedding | Sentence/text embedding |
|---|---|---|
| Ở đâu | Lớp đầu tiên bên trong LLM | Model embedding riêng (dùng cho RAG) |
| Input | 1 token | Cả câu hoặc đoạn |
| Output | 1 vector cho mỗi token | 1 vector cho cả đoạn |
| Có ngữ cảnh? | Không: "nó" luôn ra cùng một vector | Có: đã qua attention rồi mới gộp |

Khi làm RAG, bạn dùng loại thứ hai: nén cả chunk thành một vector để tìm kiếm.

## Giới hạn: embedding bắt chủ đề, không bắt logic

Nén cả câu vào một vector thì giữ được "câu này nói về cái gì", mất nhiều chi tiết "câu này khẳng định điều gì".

| Cặp câu | Similarity | Vì sao |
|---|---|---|
| "Tôi thích game này" / "Tôi không thích game này" | Cao | Cùng chủ đề, chỉ khác một chữ "không" |
| "Doanh thu tăng 5%" / "Doanh thu tăng 50%" | Cao | Con số gần như không đổi hướng vector |
| "Lỗi ở item_10234" / "Lỗi ở item_10243" | Cao | Mã ID là token vụn, không mang nghĩa |
| "Cách hồi máu" / "Làm sao tăng HP" | Cao | Đúng mong muốn: khác chữ, cùng nghĩa |

Ba dòng đầu là lý do RAG chỉ dùng vector search hay trả về tài liệu sai.

## Hệ quả cho AI engineer

- **Hybrid search**: vector search (bắt nghĩa) + BM25 (bắt đúng chữ: mã ID, tên riêng, con số).
- **Reranker**: model đọc query và tài liệu **cùng lúc** nên bắt được phủ định và chi tiết. Chậm, nên chỉ chạy trên top-k kết quả.
- **Điểm similarity không phải xác suất đúng.** 0.85 không có nghĩa là "85% liên quan". Ngưỡng phải đo trên dữ liệu của mình.
- **Không trộn vector của hai model embedding khác nhau.** Mỗi model một không gian riêng. Đổi model → embed lại toàn bộ index.

## Hiểu nhầm hay gặp

- "Similarity cao = cùng ý": sai. Cùng chủ đề, có thể ngược ý.
- "Embedding hiểu câu": nó nén câu. Chi tiết nào không đổi hướng vector thì coi như mất.

## Tự kiểm tra

1. Tính cos giữa [3, 4] và [4, 3].
2. User hỏi "event tháng 11 có giảm giá gói VIP không". Chunk nào có thể bị trả về nhầm, vì sao?
3. Vì sao reranker bắt được chữ "không" còn embedding thì kém?
