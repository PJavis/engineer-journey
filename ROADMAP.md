# Lộ trình AI Engineer: ôn tập, học sâu, mở rộng

> Bản markdown của tài liệu lộ trình (Claude Docs, 12/11/2026). Đây là nguồn sự thật trong repo; khi sửa lộ trình, sửa file này.

## Tổng quan

Lộ trình kéo dài **21 tuần, 16/11/2026 → 11/04/2027**, khoảng **9–11 giờ/tuần**. Học theo hình chữ T: trục sâu là AI Engineer, mở rộng sang ML Engineer; thống kê thực nghiệm và system design chạy song song; data engineering gắn vào các tuần project cần đến. Mỗi giai đoạn khép lại bằng một phiên bản của cùng một project.

Mức độ học:

- **[Sâu]**: tự xây được, giải thích được, đo được
- **[Vừa]**: hiểu cơ chế, làm được ví dụ, biết khi nào dùng
- **[Lướt]**: đọc hiểu ý chính, đủ để trao đổi với người làm chuyên sâu

Một tuần mẫu:

| Hoạt động | Giờ/tuần | Ghi chú |
|---|---|---|
| Code project | 4–5 | Phần chính, kiến thức đi theo vấn đề gặp phải |
| Đọc, xem tài liệu | 2–3 | Chỉ đọc phần project đang cần |
| System design | 1–2 | Chỉ ở các tuần có bài, khoảng mỗi 2 tuần |
| Ôn tập, tự kiểm tra | 1 | Flashcard, câu hỏi cuối giai đoạn |
| Viết ghi chú tuần | 1 | Một trang: đã hiểu gì, còn mù mờ gì |

Nguyên tắc: không đọc hết giáo trình rồi mới làm. Gặp vấn đề trong project thì đào sâu đúng vấn đề đó, và mọi cải tiến đều phải đo bằng eval trước và sau.

## Giai đoạn 1: Ôn tập nền tảng (tuần 1–3, 16/11–06/12)

Biến những gì đã biết rời rạc thành hiểu cơ chế, kết thúc với project v0.

| Tuần | Chủ đề | Nội dung chính | Đầu ra |
|---|---|---|---|
| 1 (16/11–22/11) | LLM hoạt động thế nào [Sâu] | Transformer, attention, tokenization, sampling (temperature, top-p), context window, KV cache, embedding | Một trang tự giải thích: vì sao output dao động, vì sao tiếng Việt tốn token hơn (tự đếm token cùng nội dung Việt/Anh) |
| 2 (23/11–29/11) | Prompting và tool calling [Sâu] | System prompt, few-shot, structured output theo JSON schema, mô tả tool và schema tham số | Script gọi API trả JSON đúng schema, có validate, retry, log lỗi |
| 3 (30/11–06/12) | RAG và agent cơ bản [Sâu] | Chunking, embedding, hybrid search, rerank; vòng lặp tool-use; workflow khác agent ở đâu | Project v0 chạy đầu cuối, kèm 20 test case đầu tiên |

Tự kiểm tra cuối giai đoạn:

- [ ] Giải thích được attention bằng lời, không cần công thức
- [ ] Biết khi nào temperature thấp vẫn cho kết quả khác nhau
- [ ] Phân biệt được lúc nên dùng workflow và lúc cần agent, kèm ví dụ
- [ ] Kể được ít nhất 3 failure mode của RAG và cách phát hiện từng cái
- [ ] Viết được tool description khiến model gọi đúng tool ngay lần đầu

## Giai đoạn 2: Học sâu trục AI Engineer (tuần 4–12, 07/12–07/02)

Phần trọng tâm, 9 tuần. Eval học đầu tiên vì mọi module sau dùng nó để đo. Cuối giai đoạn: project v2 có eval, tracing, đã red-team.

| Tuần | Chủ đề | Nội dung chính | Đầu ra |
|---|---|---|---|
| 4–5 (07/12–20/12) | Evaluation [Sâu] | Golden dataset, metric theo tác vụ, LLM-as-judge và thiên lệch (vị trí, độ dài, tự ưu ái), hiệu chỉnh judge với nhãn người, regression test cho prompt | Eval harness ≥ 50 test case, báo cáo mức đồng thuận judge vs nhãn của bạn |
| 6–7 (21/12–03/01) | RAG nâng cao [Sâu] | Chunking, metadata filter, hybrid search + rerank, query rewriting, đo retrieval (recall@k, MRR) tách khỏi generation (faithfulness) | So sánh ≥ 3 cấu hình retrieval bằng eval harness, chọn theo số liệu |
| 8 (04/01–10/01) | Context engineering [Sâu] | Prompt caching, thứ tự context, nén và tóm tắt, long context vs RAG, memory ngắn/dài hạn | Giảm chi phí hoặc latency mỗi request mà eval không giảm |
| 9–10 (11/01–24/01) | Agent production [Sâu] | Thiết kế tool, MCP, planning, giới hạn bước và chi phí, human-in-the-loop, phân quyền read-only/execute, multi-agent khi nào đáng | Project v1: agent có giới hạn bước, có duyệt trước hành động ghi |
| 11 (25/01–31/01) | LLMOps [Sâu] | Tracing từng bước, versioning prompt, fallback model, rate limit, theo dõi chi phí, TTFT và latency | Dashboard chi phí, latency, tỉ lệ lỗi |
| 12 (01/02–07/02) | Bảo mật và an toàn [Sâu] | Prompt injection trực tiếp và gián tiếp (qua dữ liệu tool), rò rỉ dữ liệu qua tool, PII, guardrail | Project v2: danh sách tấn công đã thử, kết quả, biện pháp chặn |

**Tuần 13 (08/02–14/02)**: tuần đệm, trùng Tết. Không học mới, chỉ ôn giai đoạn 1–2 hoặc bù phần chậm.

Tự kiểm tra cuối giai đoạn:

- [ ] Thiết kế được bộ eval cho một tác vụ mới trong một buổi
- [ ] Giải thích được vì sao không tin ngay điểm số của LLM-as-judge
- [ ] Chỉ ra được lỗi nằm ở retrieval hay generation bằng số liệu
- [ ] Liệt kê được các đường prompt injection vào agent của chính mình
- [ ] Ước tính được chi phí mỗi request và chỗ nào có thể giảm

## Giai đoạn 3: Mở rộng sang ML Engineer (tuần 14–21, 15/02–11/04)

Hiểu phần bên dưới model đủ để tự quyết khi nào fine-tune và tự host. Đầu ra: project v3, model nhỏ fine-tune cho tác vụ hẹp, so sánh sòng phẳng với model lớn qua API.

| Tuần | Chủ đề | Nội dung chính | Đầu ra |
|---|---|---|---|
| 14–15 (15/02–28/02) | ML truyền thống và nền deep learning [Vừa] | Gradient boosting, train/val/test, data leakage, metric phân loại; backprop, loss, optimizer ở mức cơ chế | Model phân loại/dự đoán trên dataset công khai, báo cáo đánh giá đúng cách |
| 16–18 (01/03–21/03) | Fine-tuning [Vừa; dữ liệu và đánh giá: Sâu] | SFT, LoRA/QLoRA, chuẩn bị dữ liệu, DPO, distillation, khi nào fine-tune thật sự đáng | Model nhỏ open-weight fine-tune cho một tác vụ của project |
| 19–20 (22/03–04/04) | Inference và serving [Vừa] | Quantization, vLLM, batching, KV cache, TTFT và throughput, chi phí tự host vs API | Project v3: serve model fine-tune, so sánh chất lượng, latency, chi phí với model lớn |
| 21 (05/04–11/04) | Tổng kết | Write-up project, cập nhật portfolio, chọn hướng đào sâu tiếp | Bài viết kỹ thuật về toàn bộ project |

Tự kiểm tra cuối giai đoạn:

- [ ] Giải thích được LoRA tiết kiệm tài nguyên nhờ đâu
- [ ] Nêu được 3 trường hợp nên fine-tune và 3 trường hợp không nên
- [ ] Phát hiện được data leakage trong một pipeline huấn luyện
- [ ] Tính được model 7B ở 4-bit cần khoảng bao nhiêu VRAM và vì sao

## Song song

### Thống kê thực nghiệm [Vừa] — tuần 1–12, 1–2 giờ/tuần

Câu hỏi trung tâm: prompt mới đúng hơn 3 case trên 50 case là cải tiến thật hay nhiễu?

1. Phân phối, phương sai, khoảng tin cậy, bootstrap
2. Kiểm định giả thuyết, hiểu đúng p-value, cỡ mẫu và power
3. A/B test: chọn metric, lỗi peeking, nhiều phép so sánh cùng lúc
4. Suy luận nhân quả cơ bản: confounder, tương quan ≠ nhân quả

Đầu ra: bootstrap khoảng tin cậy cho kết quả eval, áp dụng từ tuần 6 mỗi khi so sánh hai cấu hình.

### System design [Sâu] — mỗi 2 tuần một bài, 1,5–2 giờ/bài

Tự thiết kế trước, rồi Claude đóng vai người phỏng vấn hỏi xoáy vào điểm nghẽn, chi phí, failure mode. Lưu vào `system-design/`.

| Tuần | Đề bài | Trọng tâm |
|---|---|---|
| 2 (23/11–29/11) | Rate limiter, cache và queue cho một API | Ôn nền: caching, xử lý bất đồng bộ, idempotency |
| 4 (07/12–13/12) | Scale một dịch vụ lên gấp 10 lần | Ôn nền: scale ngang, sharding, tính nhất quán |
| 6 (21/12–27/12) | Hệ thống eval tự chạy mỗi khi đổi prompt | Lưu kết quả, so sánh phiên bản, chặn regression |
| 8 (04/01–10/01) | RAG cho 1 triệu tài liệu, cập nhật hằng ngày | Ingestion tăng dần, index, latency truy vấn |
| 10 (18/01–24/01) | Nền tảng agent chạy tác vụ dài, có duyệt của người | Job bất đồng bộ, timeout, retry khi gọi tool, trạng thái |
| 12 (01/02–07/02) | LLM gateway cho nhiều team | Định tuyến model, fallback, quota, theo dõi chi phí |
| 15 (22/02–28/02) | Chatbot hỗ trợ 1 triệu người dùng, ngân sách cố định | Semantic cache, chọn model theo bước, streaming |
| 17 (08/03–14/03) | RAG multi-tenant | Cách ly dữ liệu giữa khách hàng, phân quyền tool |
| 19 (22/03–28/03) | Serving model fine-tune: tự host hay gọi API | GPU, batching, autoscale, điểm hoà vốn chi phí |
| 21 (05/04–11/04) | Thiết kế lại project v3 cho quy mô gấp 100 lần | Bài tổng hợp toàn bộ lộ trình |

### Data engineering [Vừa] — gắn vào tuần project

| Tuần | Điểm data engineering | Đầu ra |
|---|---|---|
| 3 | Pipeline ingestion cho RAG: parse, chunk, embed, ghi index | Ingestion chạy lại nhiều lần vẫn cho cùng kết quả |
| 6–7 | Cập nhật index tăng dần khi tài liệu thêm/sửa/xoá (giống CDC); dedup, metadata; vector DB (HNSW, filter metadata, đánh đổi recall/tốc độ) | Index đồng bộ với nguồn mà không build lại toàn bộ |
| 11 | Versioning eval set, prompt, dữ liệu index | Mỗi lần chạy eval ghi lại version của cả ba |
| 14–18 | Dữ liệu huấn luyện: chia tập không leakage, pipeline sinh dữ liệu fine-tune, lineage | Dataset fine-tune có version, tái tạo được |

### Nghiên cứu [Lướt] — khoảng một bài mỗi 2 tuần

- Scaling laws và vì sao model lớn hơn thường tốt hơn
- RLHF, RLAIF và cách model được căn chỉnh
- Reasoning model và test-time compute
- Mixture of Experts
- Interpretability ở mức khái niệm

## Project xuyên suốt

Giữ một bài toán thật suốt 21 tuần: baseline → đo → cải tiến → đo lại → vận hành.

- **Hướng A**: trợ lý phân tích dữ liệu bằng ngôn ngữ tự nhiên. Hỏi tiếng Việt, agent sinh SQL trên dữ liệu analytics, chạy read-only, giải thích kết quả. Mạnh ở agent, tool, bảo mật, eval có đáp án rõ.
- **Hướng B**: trợ lý tra cứu tài liệu thiết kế game. RAG trên tài liệu thiết kế và config, trả lời có trích dẫn. Mạnh ở RAG, chunking tài liệu có bảng biểu, faithfulness.

| Phiên bản | Hạn | Tiêu chí hoàn thành |
|---|---|---|
| v0: baseline | 06/12 | Chạy đầu cuối, 20 test case, đã ghi điểm baseline |
| v1: agent | 24/01 | Giới hạn bước và chi phí, hành động ghi phải được duyệt |
| v2: production-ready | 07/02 | Eval ≥ 50 case, tracing, dashboard chi phí/latency, đã red-team |
| v3: model nhỏ | 04/04 | Một tác vụ chạy bằng model fine-tune, có bảng so sánh với model lớn |

Nếu dùng dữ liệu công ty: chỉ quyền read-only, không đưa dữ liệu người chơi vào API bên ngoài khi chưa được phép.

## Nhịp ôn tập

| Nhịp | Thời lượng | Làm gì |
|---|---|---|
| Hằng ngày | 10–15 phút | Flashcard (Anki) cho khái niệm và con số quan trọng, mỗi tuần thêm thẻ từ ghi chú |
| Cuối tuần | 1 giờ | Một trang ghi chú tuần; giải thích một khái niệm khó như đang dạy người khác |
| Tuần 4, 8, 13, 21 | 1 buổi | Làm lại câu tự kiểm tra các giai đoạn trước; câu nào chưa trơn thì đưa vào tuần sau |
| Cuối giai đoạn | 1 buổi | Quiz tổng hợp + phỏng vấn thử với Claude |

Dấu hiệu cần chậm lại: hai tuần liền không đạt đầu ra, hoặc ghi chú tuần chủ yếu chép lại tài liệu.

## Tài liệu tham khảo

Tìm theo tên để có bản mới nhất; với công cụ và model cụ thể, ưu tiên tài liệu chính thức tại thời điểm học.

| Mảng | Tài liệu | Dùng ở |
|---|---|---|
| Tổng quan AI Engineering | Chip Huyen, *AI Engineering* (O'Reilly) | Cả lộ trình |
| Nền tảng LLM | Andrej Karpathy, *Neural Networks: Zero to Hero*; Jay Alammar, *The Illustrated Transformer* | Tuần 1 |
| Agent | Anthropic, *Building effective agents*; tài liệu Model Context Protocol | Tuần 3, 9–10 |
| Eval | Các bài viết về eval của Hamel Husain | Tuần 4–5 |
| Bảo mật | OWASP Top 10 for LLM Applications | Tuần 12 |
| ML system | Chip Huyen, *Designing Machine Learning Systems* | Tuần 14–15 |
| Fine-tuning | Hugging Face LLM Course; tài liệu PEFT | Tuần 16–18 |
| Serving | Tài liệu vLLM | Tuần 19–20 |
| Thống kê thực nghiệm | Kohavi, Tang, Xu, *Trustworthy Online Controlled Experiments* | Song song tuần 1–12 |
