# Reflection

Trong hệ thống xử lý nhật ký quan sát mô hình LLM (LLM Observability) mà tôi quan tâm, hệ thống dễ vướng phải Anti-pattern **"Vấn đề file quá nhỏ" (Small Files Problem)** nhất.

**Lý do:**
Các request tương tác với LLM và hành vi của Agent diễn ra liên tục theo thời gian thực (streaming data). Khi ta liên tục đổ (append) các payload nhỏ (chỉ khoảng vài trăm byte) thẳng xuống Data Lakehouse ở lớp Bronze, nó sẽ sinh ra hàng ngàn file Parquet li ti. Hệ quả là làm tăng đột biến kích thước metadata, làm vô hiệu hóa khả năng Data Skipping (do phân mảnh quá mức) và gây nghẽn cổ chai (bottleneck) I/O khi thực hiện phân tích tổng hợp ở lớp Gold.

**Cách phòng tránh:**
Cần phải thiết lập các Data Maintenance Jobs chạy định kỳ theo lịch trình, cụ thể là áp dụng lệnh `OPTIMIZE` (để gộp các file nhỏ thành các file lớn ~256MB) và `Z-ORDER` (để gom nhóm dữ liệu theo chiều truy vấn như `user_id` hoặc `agent_version`). Nhờ vậy, hiệu suất hệ thống sẽ được duy trì ổn định.

---
**Khai báo sử dụng AI:** 
Tôi có sử dụng công cụ trợ lý AI (Antigravity/Claude) để giải thích các khái niệm trong bài lab, tìm lỗi (debug), chạy test ngầm các file Python tự động và phác thảo dàn ý cho bài luận này. Mọi thao tác truy xuất dữ liệu và hiểu bản chất kỹ thuật đều do tôi chủ động kiểm chứng.
