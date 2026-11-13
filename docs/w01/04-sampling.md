# 4. Sampling: temperature, top-k, top-p

**Một câu:** model không trả về một token, nó trả về điểm số cho mọi token; sampling là cách chọn một token từ đó, và là nguồn gốc của việc output dao động.

## Từ logits đến xác suất

Model cho ra logits (điểm số thô). Softmax đổi thành xác suất:

```
p_i = e^(z_i / T) / Σ e^(z_j / T)
```

`T` là temperature.

## Temperature

Ví dụ: 4 token có logits [3, 2, 1, 0].

| T | Xác suất | Nhận xét |
|---|---|---|
| 0.1 | [1.00, 0.00, 0.00, 0.00] | Gần như luôn chọn token đầu |
| 0.5 | [0.87, 0.12, 0.02, 0.00] | Nhọn |
| 1 | [0.64, 0.24, 0.09, 0.03] | Phân phối gốc |
| 2 | [0.46, 0.28, 0.17, 0.10] | Phẳng hơn |
| 10 | [0.29, 0.26, 0.24, 0.21] | Gần như đều |

Cách nhớ: chia cho T nhỏ **khuếch đại** khoảng cách giữa các logit; chia cho T lớn **thu nhỏ** khoảng cách.

- T → 0: nhọn, luôn chọn token điểm cao nhất (argmax, còn gọi là greedy). T = 0 đúng nghĩa là chia cho 0 nên được cài thành argmax.
- T lớn: phẳng, đa dạng hơn, nhưng token vô lý cũng có cơ hội → dễ lạc đề, sai.

## Top-k và top-p: cắt đuôi

Temperature bóp méo phân phối nhưng không loại token nào. Top-k và top-p loại hẳn phần đuôi.

**Top-k**: giữ k token xác suất cao nhất.

**Top-p (nucleus)**: xếp giảm dần, giữ **tập nhỏ nhất có tổng cộng dồn ≥ p**, bỏ phần còn lại, chuẩn hoá lại.

Ví dụ top-p = 0.9 với xác suất [0.5, 0.3, 0.15, 0.05]:

| Token | Xác suất | Cộng dồn | Giữ? | Sau chuẩn hoá |
|---|---|---|---|---|
| A | 0.50 | 0.50 | Có | 0.53 |
| B | 0.30 | 0.80 | Có | 0.32 |
| C | 0.15 | 0.95 | Có (vừa vượt 0.9) | 0.16 |
| D | 0.05 | 1.00 | Bỏ | 0 |

Điểm mạnh của top-p so với top-k: số token giữ lại **tự co giãn**.

| Tình huống | Phân phối | Top-p 0.9 giữ |
|---|---|---|
| Model chắc chắn ("Thủ đô Việt Nam là ...") | [0.97, 0.01, ...] | 1 token |
| Model phân vân ("Hôm nay tôi thấy ...") | [0.08, 0.07, 0.07, ...] | Vài chục token |

## Vì sao T = 0 vẫn không ra y hệt

1. **Floating-point trên GPU không tất định tuyệt đối.** Thứ tự cộng trong phép tính song song, phần cứng khác nhau, request được batch chung với request khác → logit lệch cỡ 1e-6.
2. **Hai logit gần bằng nhau** → lệch nhỏ đó đủ đổi token thắng.
3. **Autoregressive** → lệch một token, mọi token sau đi nhánh khác.

Ngoài ra nhà cung cấp có thể cập nhật model hoặc hạ tầng phía sau cùng một tên model.

## Hệ quả cho AI engineer

| Tác vụ | Gợi ý |
|---|---|
| Trích xuất, phân loại, sinh SQL, JSON | T thấp |
| Viết sáng tạo, brainstorm | T cao hơn |

- **Đừng thiết kế dựa trên giả định output lặp lại y hệt.** Cần ổn định: validate output theo schema, retry khi sai, cache câu trả lời nếu phải giống hệt.
- **Eval phải chạy nhiều lần** hoặc trên đủ nhiều case. Một lần chạy đúng không chứng minh gì.
- **Thường chỉ chỉnh một trong hai**: temperature hoặc top-p. Chỉnh cả hai khó đoán kết quả.
- Tham số nào được phép chỉnh tuỳ model và API; kiểm tra tài liệu chính thức.

## Hiểu nhầm hay gặp

- "T = 0 làm phân phối phẳng": ngược. T nhỏ → nhọn.
- "T = 0 là deterministic": gần đúng, không tuyệt đối.
- "T thấp thì hết hallucination": sai. T thấp chỉ làm model nhất quán; nếu token xác suất cao nhất là sai thì nó sai một cách nhất quán.
- "Top-p giữ token có xác suất trên p": sai. Tính theo tổng cộng dồn.

## Tự kiểm tra

1. Logits [2, 1]. Tính xác suất token đầu với T = 1 và T = 0.5.
2. Xác suất [0.6, 0.25, 0.1, 0.05], top-p = 0.8. Giữ những token nào?
3. Vì sao T thấp không chữa được hallucination?
