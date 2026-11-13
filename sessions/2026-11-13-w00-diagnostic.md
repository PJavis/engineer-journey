# Tuần 00 · Buổi 0 · 13/11/2026 · Chẩn đoán nền tảng LLM

## Mục tiêu buổi học

- Đo mức hiểu hiện tại về các chủ đề tuần 1 trước khi học chính thức (16/11)

## Đã hiểu (viết bằng lời của mình)

> Claude ghi tạm từ câu trả lời trong buổi. Viết lại bằng lời của mình khi ôn.

- Nhớ công thức attention: softmax(QK/√d)·V
- Đổi token ở đầu prompt thì cache phía sau mất hết, vì token sau phụ thuộc token trước
- Temperature cao → phân phối phẳng hơn → output đa dạng hơn
- Không có attention thì "nó" không biết mình chỉ cái gì
- Câu khẳng định và phủ định có cosine similarity cao; rerank giúp xử lý

## Còn mơ hồ / câu hỏi mở

> Buổi sau bắt đầu từ mục này.

- [ ] Temperature: ban đầu hiểu ngược (T→0 là phẳng). Cần tự tính lại softmax với T = 0.1, 1, 10
- [ ] Top-p: nhầm thành "ngưỡng xác suất". Đúng là cộng dồn; cần phân biệt top-p / top-k / temperature
- [ ] Hallucination: quy cho reward kiểu RL; chưa nắm gốc là mục tiêu next-token prediction
- [ ] Context window: trả lời theo số token còn lại; chưa biết lost in the middle
- [ ] Tokenization: chưa biết, mới nghe giảng. Cần tự đếm token Việt/Anh
- [ ] KV cache: chưa biết, mới nghe giảng. Cần tự giải thích lại vì sao output token đắt hơn input

## Claude đã chỉ ra (hiểu nhầm, chỗ cần đọc thêm)

- Token ID không mang nghĩa; nghĩa nằm ở embedding. Token ≠ từ
- Attention không phải xác suất hai từ cạnh nhau (đó là n-gram). Mỗi token tự sinh Q, K, V; attention là lookup mềm
- T = 0 là argmax, vẫn không deterministic tuyệt đối (floating-point trên GPU, logit gần bằng nhau, lệch dây chuyền)
- Embedding bắt chủ đề, không bắt phủ định / con số / mã định danh → cần hybrid search + rerank
- Vừa context window ≠ model dùng tốt mọi phần
- Output đắt hơn input: input cũng qua mọi layer (không chỉ embedding); khác biệt là input chạy song song một lượt, output mỗi token một lượt tuần tự

## Việc tiếp theo

- [ ] Đọc 6 bài trong `docs/w01/`, làm `docs/w01/homework.md`
- [ ] Đọc The Illustrated Transformer (Jay Alammar)
- [ ] Tự đếm token cùng một đoạn Việt/Anh, điền bảng trong `notes/01-llm-fundamentals.md`
- [ ] Ôn `flashcards/w01.tsv`
