# docs — tài liệu đọc

Tài liệu Claude soạn cho từng tuần: ngắn, có ví dụ, đọc xong làm được bài tập. Khác với `notes/` (ghi chú bằng lời của mình).

Con số trong ví dụ đều tự tính được bằng tay hoặc vài dòng Python. Chi tiết về model/công cụ cụ thể: đối chiếu tài liệu chính thức.

## Tuần 1 — LLM hoạt động thế nào

Đọc theo thứ tự, mỗi bài 10–15 phút:

1. [Vocabulary và tokenization](w01/01-tokenization.md)
2. [Embedding](w01/02-embedding.md)
3. [Attention và Transformer](w01/03-attention-transformer.md)
4. [Sampling: temperature, top-k, top-p](w01/04-sampling.md)
5. [Context window và KV cache](w01/05-context-kv-cache.md)
6. [Hallucination](w01/06-hallucination.md)

Sau đó: [Bài tập tuần 1](w01/homework.md)

## Toàn cảnh: một request đi qua model thế nào

```
"Con mèo đói"
   │ 1. tokenizer: cắt text thành token, đổi thành ID
   ▼
[812, 4401, 977]
   │ 2. embedding: mỗi ID tra ra một vector
   ▼
3 vector
   │ 3. N lớp Transformer: attention trộn thông tin giữa các token
   ▼
3 vector đã có ngữ cảnh
   │ 4. lấy vector cuối → điểm (logit) cho mọi token trong vocabulary
   ▼
logits (dài bằng vocabulary)
   │ 5. sampling: temperature, top-p → chọn 1 token
   ▼
token mới → nối vào cuối → lặp lại từ bước 2 cho tới khi dừng
```

Mỗi bài đọc ứng với một bước trong sơ đồ này.
