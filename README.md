# engineer-journey

Nhật ký học AI Engineer, 21 tuần (16/11/2026 → 11/04/2027). Lộ trình chi tiết: [ROADMAP.md](ROADMAP.md).

## Tiến độ

| Tuần | Chủ đề | System design | Trạng thái |
|---|---|---|---|
| 1 (16/11–22/11) | LLM hoạt động thế nào | — | ⏳ |
| 2 (23/11–29/11) | Prompting và tool calling | Rate limiter, cache, queue | ⬜ |
| 3 (30/11–06/12) | RAG và agent cơ bản → **v0** | — | ⬜ |
| 4 (07/12–13/12) | Evaluation (1/2) | Scale ×10 | ⬜ |
| 5 (14/12–20/12) | Evaluation (2/2) | — | ⬜ |
| 6 (21/12–27/12) | RAG nâng cao (1/2) | Eval tự chạy khi đổi prompt | ⬜ |
| 7 (28/12–03/01) | RAG nâng cao (2/2) | — | ⬜ |
| 8 (04/01–10/01) | Context engineering | RAG 1 triệu tài liệu | ⬜ |
| 9 (11/01–17/01) | Agent production (1/2) | — | ⬜ |
| 10 (18/01–24/01) | Agent production (2/2) → **v1** | Nền tảng agent tác vụ dài | ⬜ |
| 11 (25/01–31/01) | LLMOps | — | ⬜ |
| 12 (01/02–07/02) | Bảo mật và an toàn → **v2** | LLM gateway | ⬜ |
| 13 (08/02–14/02) | Đệm / Tết | — | ⬜ |
| 14 (15/02–21/02) | ML truyền thống (1/2) | — | ⬜ |
| 15 (22/02–28/02) | ML truyền thống (2/2) | Chatbot 1 triệu user | ⬜ |
| 16 (01/03–07/03) | Fine-tuning (1/3) | — | ⬜ |
| 17 (08/03–14/03) | Fine-tuning (2/3) | RAG multi-tenant | ⬜ |
| 18 (15/03–21/03) | Fine-tuning (3/3) | — | ⬜ |
| 19 (22/03–28/03) | Inference và serving (1/2) | Tự host hay API | ⬜ |
| 20 (29/03–04/04) | Inference và serving (2/2) → **v3** | — | ⬜ |
| 21 (05/04–11/04) | Tổng kết, write-up | Project v3 ×100 | ⬜ |

Ký hiệu: ⬜ chưa bắt đầu · ⏳ đang học · ✅ đạt đầu ra · ⚠️ chưa đạt, dời sang tuần sau

## Cấu trúc

```
ROADMAP.md        lộ trình đầy đủ (nguồn sự thật)
sessions/         nhật ký từng buổi học với Claude — YYYY-MM-DD-wNN-<chu-de>.md
notes/            ghi chú kiến thức theo chủ đề, viết bằng lời của mình
flashcards/       câu hỏi ôn tập, import vào Anki (TSV: front<TAB>back)
system-design/    bài system design mỗi 2 tuần
project/          code project xuyên suốt (v0 → v3)
  evals/          eval set + kết quả mỗi lần chạy
reviews/          tự kiểm tra cuối giai đoạn, phỏng vấn thử
templates/        khuôn cho session, note, system design
```

## Cách học cùng Claude

1. Đầu buổi: mở Claude Code trong repo, nói "tuần N, phần X". Claude đọc `README.md`, session gần nhất trong `sessions/` (nhất là mục **Còn mơ hồ**) rồi bắt đầu từ đó.
2. Cuối buổi: tạo file session từ `templates/session.md`, cập nhật bảng tiến độ, commit.

## Quy tắc dữ liệu

Repo private. Không commit dữ liệu thật của công ty, dữ liệu người chơi, API key hay credential. Dùng `.env` (đã ignore) cho secret.
