# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Khi temperature tăng từ 0.0 lên 1.5, câu trả lời có xu hướng đa dạng và sáng tạo hơn. Ở temperature 0.0, phản hồi khá ổn định, trực tiếp và ít thay đổi; trong khi 1.0–1.5 có cách diễn đạt phong phú hơn nhưng cũng dễ xuất hiện thông tin kém nhất quán hoặc ít chính xác hơn.


### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ chọn temperature khoảng 0.2–0.3 cho chatbot hỗ trợ khách hàng. Với loại ứng dụng này, tính chính xác, nhất quán và khả năng bám sát thông tin quan trọng hơn sự sáng tạo, nhưng vẫn nên để temperature lớn hơn 0 một chút để câu trả lời tự nhiên hơn.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Mỗi ngày có 10.000 × 3 = 30.000 request, tương đương khoảng 10,5 triệu output token/ngày. Với giá output GPT-4o là $10/1M token và GPT-4o-mini là $0.60/1M token, chi phí output tương ứng khoảng $105/ngày và $6,3/ngày, tức GPT-4o đắt hơn khoảng 16,7 lần. GPT-4o phù hợp với các tác vụ phức tạp cần suy luận và độ chính xác cao, chẳng hạn phân tích tài liệu hoặc giải quyết yêu cầu khách hàng phức tạp. GPT-4o-mini phù hợp với FAQ, phân loại yêu cầu, tóm tắt ngắn hoặc chatbot có lượng request lớn để tối ưu chi phí.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Với persona giáo viên tiểu học, model giải thích blockchain bằng từ ngữ đơn giản, câu ngắn và thường sử dụng ví dụ gần gũi như một cuốn sổ mà nhiều người cùng giữ bản sao. Với persona chuyên gia tài chính, phản hồi dài và chuyên sâu hơn, sử dụng các thuật ngữ như distributed ledger, consensus mechanism, cryptography và smart contract. Điều này cho thấy system prompt có thể định hướng rõ cách model lựa chọn từ vựng, mức độ chi tiết, ví dụ và đối tượng người đọc. Vì vậy, cùng một câu hỏi nhưng model có thể tạo ra cách trả lời rất khác nhau tùy persona được thiết lập.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Tôi thử với một đoạn văn tiếng Việt khoảng 100 từ. Công thức số từ / 0.75 ước lượng khoảng 133 token, trong khi tiktoken cho số token cao hơn đáng kể, khoảng 170–190 token tùy đoạn văn, tức chênh khoảng 30–40%. Nguyên nhân là token không tương ứng trực tiếp với một từ; tiếng Việt có dấu, ký tự Unicode và nhiều từ ghép được viết thành nhiều âm tiết cách nhau bằng khoảng trắng nên tokenizer có thể chia một từ hoặc cụm từ thành nhiều token. Vì vậy, ước lượng dựa trên số từ chỉ mang tính tương đối và nên dùng tokenizer thực tế khi cần tính chi phí chính xác.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất đối với chatbot hoặc các tác vụ sinh nội dung dài vì người dùng có thể nhìn thấy phản hồi xuất hiện ngay từng phần thay vì phải chờ toàn bộ kết quả được tạo xong, từ đó giảm cảm giác độ trễ. Ngược lại, non-streaming phù hợp với những tác vụ cần nhận kết quả hoàn chỉnh trước khi xử lý tiếp, chẳng hạn sinh JSON có cấu trúc, phân loại văn bản, xử lý batch hoặc khi backend cần kiểm tra toàn bộ output trước khi trả về cho người dùng.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff tăng dần thời gian chờ sau mỗi lần request thất bại, giúp giảm áp lực lên API khi hệ thống đang quá tải và tăng khả năng request tiếp theo thành công. Nếu hàng nghìn client đều retry sau một khoảng delay cố định như 1 giây, chúng có thể gửi request lại gần như cùng lúc, tạo ra một đợt tải lớn mới gọi là thundering herd, khiến API tiếp tục quá tải. Trong thực tế có thể kết hợp exponential backoff với một khoảng thời gian ngẫu nhiên (jitter) để các client không retry cùng thời điểm.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Tôi chọn persona là một trợ lý AI hỗ trợ học tập về lập trình và trí tuệ nhân tạo. System prompt: "Bạn là trợ lý AI hỗ trợ sinh viên học lập trình và trí tuệ nhân tạo. Hãy trả lời bằng tiếng Việt, ngắn gọn, rõ ràng và chính xác. Khi giải thích khái niệm kỹ thuật, hãy ưu tiên ví dụ thực tế và đoạn code đơn giản khi cần. Nếu không chắc chắn về thông tin, hãy nói rõ thay vì tự tạo ra câu trả lời." Tôi yêu cầu "trả lời ngắn gọn, rõ ràng" để tránh các phản hồi quá dài và giúp người học dễ tìm được ý chính. Tôi cũng chỉ định "nếu không chắc chắn hãy nói rõ" nhằm giảm nguy cơ model tạo ra thông tin sai nhưng trình bày như một sự thật.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất của trợ lý hiện tại là không có bộ nhớ dài hạn, nên chỉ có thể dựa vào một số lượt hội thoại gần nhất và có thể mất ngữ cảnh khi cuộc trò chuyện kéo dài. Một cải thiện cụ thể là lưu lịch sử hội thoại vào database và sử dụng cơ chế tóm tắt hoặc vector search để truy xuất những thông tin liên quan từ các cuộc trao đổi trước. Khi nhận câu hỏi mới, hệ thống có thể lấy những phần lịch sử phù hợp và đưa chúng vào context trước khi gọi LLM, giúp trợ lý duy trì ngữ cảnh tốt hơn mà không cần gửi toàn bộ lịch sử.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
