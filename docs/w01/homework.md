# Bài tập tuần 1

Làm sau khi đọc 6 bài. Ghi kết quả vào `notes/01-llm-fundamentals.md`, bằng lời của mình. Kẹt chỗ nào thì ghi vào mục "Còn mơ hồ" của file session.

## Bài 1 — Đếm token Việt/Anh (thực hành, 30 phút)

Chọn 3 đoạn văn, mỗi đoạn có bản tiếng Việt và bản tiếng Anh cùng nội dung (một đoạn hội thoại, một đoạn kỹ thuật, một đoạn có số và mã ID).

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install tiktoken
```

```python
import tiktoken

enc = tiktoken.get_encoding("o200k_base")

def show(text):
    ids = enc.encode(text)
    print(len(ids), [enc.decode([i]) for i in ids])

show("Tôi thích game này")
show("I like this game")
```

Mẩu token tiếng Việt in ra có thể hiện ký tự lỗi `�`: một ký tự có dấu bị cắt giữa các byte. Đó chính là hiện tượng cần quan sát.

Ghi lại:

- Bảng: số ký tự, số byte, số token, tỉ lệ Việt/Anh cho từng đoạn
- Từ tiếng Việt nào bị cắt vụn nhất? Vì sao?
- `game`, ` game`, `Game` có cùng token ID không?
- Một số dài như `20261113` thành mấy token?

Đây là tokenizer của OpenAI. Tokenizer của model khác cho số khác; nếu dùng model khác, đếm bằng công cụ chính thức của model đó và so sánh.

## Bài 2 — Tự cài BPE (thực hành, 45 phút)

Viết hàm Python khoảng 30 dòng: nhận corpus và số bước gộp, in ra cặp được gộp ở mỗi bước.

- Chạy với corpus trong bài 1 (`low ×5, lower ×2, newest ×6, widest ×3`), so với bảng trong tài liệu.
- Chạy với 10 câu tiếng Việt tự chọn. Token nào hình thành đầu tiên?

## Bài 3 — Tính tay (15 phút)

1. Logits [2, 1, 0]. Tính xác suất với T = 1 và T = 0.5.
2. Xác suất [0.4, 0.3, 0.2, 0.1], top-p = 0.75. Giữ token nào, xác suất sau chuẩn hoá là bao nhiêu?
3. cos giữa [1, 2] và [2, 1].
4. Q = [1, 1]; K của ba token lần lượt [2, 0], [0, 2], [1, 1]. Tính trọng số attention (d = 2).

## Bài 4 — Giải thích bằng lời (viết, 30 phút)

Mỗi câu 3–5 dòng, không dùng công thức, viết như giải thích cho đồng nghiệp backend:

1. Attention làm gì?
2. Vì sao output dao động kể cả khi temperature = 0?
3. Vì sao output token đắt hơn input token?
4. Vì sao model bịa mà vẫn tự tin?

## Bài 5 — Tình huống thiết kế (viết, 20 phút)

Chatbot hỗ trợ người chơi, tiếng Việt. Mỗi request gồm: system prompt 2.000 token, FAQ 8.000 token, thời gian hiện tại, lịch sử hội thoại, câu hỏi.

1. Sắp xếp thứ tự các phần để tận dụng prompt caching. Giải thích.
2. Người chơi hỏi "gói VIP_30 có được hoàn tiền không". Vector search trả về chunk nói về gói VIP_3. Vì sao, và sửa thế nào?
3. Bot trả lời một mức giá không có trong FAQ. Kể 3 biện pháp, xếp theo thứ tự bạn sẽ thử.

## Nộp bài

Commit `notes/01-llm-fundamentals.md` và code bài 1, 2 (đặt trong `project/` hoặc `notes/code/`). Buổi sau mở Claude Code, nói "chấm bài tập tuần 1".
