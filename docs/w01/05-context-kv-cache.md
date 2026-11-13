# 5. Context window và KV cache

**Một câu:** context window là toàn bộ những gì model nhìn thấy trong một request; KV cache là lý do sinh text không chậm thảm hoạ, và là nền của prompt caching.

## Context window

Là số token tối đa model xử lý trong một lần gọi, **gồm cả input lẫn output**:

```
system prompt + tool definitions + lịch sử hội thoại + tài liệu + câu hỏi + câu trả lời đang sinh
```

Model không có trí nhớ giữa các request. "Nhớ" hội thoại nghĩa là ứng dụng gửi lại toàn bộ lịch sử mỗi lượt.

Ví dụ một chatbot RAG, lượt thứ 10:

| Phần | Token |
|---|---|
| System prompt | 1.500 |
| Tool definitions | 2.000 |
| 9 lượt hội thoại trước | 6.000 |
| 5 chunk tài liệu | 4.000 |
| Câu hỏi | 50 |
| **Tổng input** | **13.550** |

Câu hỏi chỉ 50 token nhưng trả tiền cho 13.550 token input, **ở mỗi lượt**.

## Vừa context không có nghĩa là dùng tốt

- **Lost in the middle**: thông tin ở giữa context dài thường được dùng kém hơn ở đầu và cuối.
- **Nhiễu**: càng nhiều nội dung không liên quan, model càng dễ bám nhầm chi tiết sai.
- **Chi phí và latency**: tăng theo độ dài, ở mọi request.

Mức độ khác nhau giữa các model. Phải đo bằng eval.

Quy tắc: chỉ đưa vào thứ cần; phần quan trọng ở đầu hoặc cuối; câu hỏi đặt sau tài liệu.

## KV cache

Nhắc lại bài 3: sinh text là vòng lặp, mỗi vòng thêm một token. Và nhờ causal mask, token chỉ nhìn các token đứng trước.

Hệ quả: **K và V của token cũ không đổi khi thêm token mới.** Vậy thì lưu lại.

Ví dụ sinh token thứ 1000:

| | Không cache | Có KV cache |
|---|---|---|
| Phải tính | Q, K, V cho cả 1000 token | Q, K, V cho 1 token mới |
| Dùng lại | Không | K, V của 999 token cũ |
| Đánh đổi | Chậm | Tốn VRAM, tăng theo độ dài context |

Giống memoization: kết quả không đổi thì không tính lại.

## Vì sao output đắt hơn input

- **Input**: mọi token đã biết → xử lý song song trong một lượt chạy.
- **Output**: token n+1 phụ thuộc token n → một lượt chạy cho mỗi token, lần lượt. KV cache làm mỗi lượt rẻ đi, không làm giảm số lượt.

Giống batch insert 1000 dòng so với 1000 câu insert, mỗi câu chờ câu trước.

Từ đó có hai chỉ số latency:

| Chỉ số | Là gì | Phụ thuộc chủ yếu vào |
|---|---|---|
| TTFT (time to first token) | Thời gian tới token đầu tiên | Độ dài input |
| Tokens/s | Tốc độ sinh | Model, phần cứng |

Latency tổng ≈ TTFT + số token output ÷ tokens/s.

## Prompt caching

Nhà cung cấp giữ KV cache của **phần đầu prompt** giữa các request. Request sau có prefix giống hệt → bỏ qua phần tính đó → rẻ hơn, TTFT thấp hơn.

Vì token sau phụ thuộc token trước, cache chỉ dùng được cho **prefix khớp chính xác**. Giống Docker layer cache: đổi một layer đầu, mọi layer sau build lại.

| Thứ tự prompt | Kết quả |
|---|---|
| `[timestamp] [system prompt] [tài liệu] [câu hỏi]` | Timestamp đổi mỗi request → không cache được gì |
| `[system prompt] [tài liệu] [câu hỏi] [timestamp]` | System prompt và tài liệu được cache |

Quy tắc: **phần ổn định lên trước, phần thay đổi xuống cuối.**

```
1. System prompt          ← không đổi
2. Tool definitions       ← không đổi
3. Tài liệu dùng chung    ← ít đổi
4. Lịch sử hội thoại      ← chỉ dài thêm ở cuối
5. Câu hỏi mới, timestamp ← đổi mỗi request
```

Cache có thời hạn sống và cách tính giá riêng theo nhà cung cấp; xem tài liệu chính thức.

## Hệ quả cho AI engineer

- **Cắt output trước khi cắt input** nếu cần giảm latency: trả lời ngắn, structured output, không bắt model lặp lại input.
- **Thứ tự prompt là quyết định về chi phí**, không chỉ về chất lượng.
- **Đừng sửa giữa lịch sử hội thoại** (ví dụ tóm tắt lại lượt cũ mỗi request): mỗi lần sửa là mất cache từ điểm đó trở đi.
- **Theo dõi tỉ lệ cache hit** như theo dõi cache của bất kỳ hệ thống nào.

## Hiểu nhầm hay gặp

- "Model nhớ cuộc trò chuyện": không. Ứng dụng gửi lại lịch sử.
- "Context 200K thì cứ nhét đủ 200K": được phép, không có nghĩa là tốt.
- "Prompt caching là cache câu trả lời": sai. Nó cache phần tính toán của input; model vẫn sinh output mới.

## Tự kiểm tra

1. Bảng ví dụ chatbot: phần nào cache được, phần nào không?
2. Agent chèn kết quả tool mới vào **đầu** lịch sử thay vì cuối. Chuyện gì xảy ra?
3. Input 10.000 token, output 500 token. Muốn giảm latency tổng thì cắt bên nào hiệu quả hơn, vì sao?
