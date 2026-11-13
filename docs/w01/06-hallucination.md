# 6. Hallucination

**Một câu:** model được huấn luyện để viết tiếp cho hợp lý, không phải để nói đúng; hallucination là lúc "hợp lý" và "đúng" tách nhau.

## Gốc rễ

Mục tiêu huấn luyện là dự đoán token tiếp theo. Model giỏi việc đó sẽ sinh ra text **trôi chảy và có vẻ đúng**. Không có bước nào trong cơ chế kiểm tra "mình có biết điều này không".

Câu đúng và câu bịa được sinh ra bằng cùng một quá trình:

```
"Thủ đô của Việt Nam là"        → "Hà Nội"       (gặp nhiều trong dữ liệu → đúng)
"Doanh thu Q3 của công ty X là" → "12,4 triệu"   (chưa từng thấy → vẫn sinh một con số hợp dạng)
```

Ở câu thứ hai, model biết sau cụm đó thường là một con số tiền, nên sinh ra con số. Hợp lý về dạng, sai về nội dung.

## Các yếu tố làm nặng thêm

| Yếu tố | Vì sao |
|---|---|
| Kiến thức hiếm hoặc sau ngày cắt dữ liệu | Model không có thông tin, vẫn có mẫu câu để viết tiếp |
| Huấn luyện thưởng cho việc trả lời | Câu trả lời hữu ích được đánh giá cao hơn "tôi không biết" → thiên về đoán |
| Prompt ép phải trả lời | "Hãy cho biết con số chính xác" → model sinh một con số |
| Context nhiễu hoặc mâu thuẫn | Model bám nhầm đoạn sai |
| Chi tiết cụ thể | Tên, số, ngày, trích dẫn, URL, tên hàm API: dễ sinh đúng dạng, sai nội dung |

## Hai loại, cách xử lý khác nhau

| Loại | Mô tả | Ví dụ |
|---|---|---|
| Sai so với thực tế | Bịa điều không có thật | Trích một paper không tồn tại |
| Sai so với context (unfaithful) | Tài liệu đã cho nói A, model trả lời B | RAG trả lời khác với chunk được cấp |

Loại thứ hai đo được: so câu trả lời với context. Đó là metric **faithfulness** ở tuần 6–7.

## Cách giảm

Không có cách xoá hẳn. Chỉ có khoanh vùng và phát hiện.

| Cách | Cơ chế |
|---|---|
| Grounding (RAG) | Đưa nguồn vào context, yêu cầu chỉ trả lời từ nguồn |
| Cho phép nói không biết | Ghi rõ trong prompt: không đủ thông tin thì nói không đủ |
| Bắt trích dẫn | Mỗi ý kèm đoạn nguồn → kiểm chứng được, bằng người hoặc bằng code |
| Dùng tool cho việc cần chính xác | Tính toán, tra dữ liệu, ngày giờ: gọi tool, đừng để model tự nhớ |
| Validate output | Schema, kiểm tra ID có tồn tại, SQL có chạy được |
| Eval | Đo tỉ lệ sai trên bộ test trước và sau mỗi thay đổi |

## Hệ quả cho AI engineer

- **Coi mọi output là chưa kiểm chứng.** Thiết kế hệ thống quanh việc kiểm tra, không quanh niềm tin.
- **Giọng tự tin không phải tín hiệu.** Model viết câu sai cũng trôi chảy như câu đúng.
- **Temperature thấp không chữa được**: chỉ làm model sai một cách nhất quán.
- **Phần lớn "hallucination" trong RAG là lỗi retrieval**: model không được cấp đúng tài liệu nên phải đoán. Kiểm tra retrieval trước khi đổ cho model.

## Hiểu nhầm hay gặp

- "Hallucination là bug sẽ được sửa": nó là hệ quả của cách model hoạt động. Giảm được, không loại hẳn.
- "Model lớn hơn thì hết": ít hơn, không hết.
- "Có RAG thì hết": RAG đổi bài toán thành retrieval đúng + trả lời bám nguồn. Cả hai vẫn có thể sai.

## Tự kiểm tra

1. Vì sao model dễ bịa tên hàm API hơn là bịa cú pháp vòng lặp for?
2. Bot RAG trả lời sai. Kể 2 nguyên nhân không nằm ở model.
3. Viết một câu trong system prompt để giảm việc model đoán khi thiếu thông tin.
