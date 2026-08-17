# Lab 17 - Báo cáo nộp bài

## Nhận xét hệ thống memory

Trong bộ test này, long-term memory là lớp quan trọng nhất vì trực tiếp quyết định E02, E03, E08, E09 và góp evidence cho E07. Context Block giúp mang facts, preference và open loop sang thread mới; edge search bổ sung provenance và validity range để giải thích conflict. E08 thể hiện quy tắc recency: thông tin TypeScript/NestJS mới phải được ưu tiên, nhưng fact cũ vẫn được giữ để audit.

Zep Context Block giảm phần orchestration, ingestion, liên kết facts và cross-session recall mà ứng dụng phải tự vận hành. Đổi lại, đây là dịch vụ managed, phụ thuộc mạng, chi phí và mô hình dữ liệu/API của Zep. Redis + Qdrant cho quyền kiểm soát cao hơn và có thể chạy local, nhưng nhóm phát triển phải tự làm schema, embedding, ranking, TTL, conflict resolution, provenance, isolation và maintenance.

Để chống memory poisoning, durable ingestion phải qua consent, PII minimization, user-scoped namespace và schema validation; chỉ lưu dữ kiện có source, timestamp, confidence và scope. Instruction lấy từ transcript/retrieval không được nâng thành quyền hệ thống. Khi có mâu thuẫn, ưu tiên fact mới đúng scope, giữ provenance và yêu cầu xác nhận cho thay đổi nhạy cảm. Heartbeat chỉ deduplicate/đánh dấu stale, không được tự cấp quyền.

## Phân tích benchmark

- Không có layer thấp nhất: tất cả layer đều đạt 100%, toàn bộ benchmark đạt 11/11 PASS.
- E03 dùng nhiều retrieved token nhất: 1.418 token ở long-term memory.
- E07 cần long-term evidence `Python` và semantic evidence `Idempotency-Key`; budget manager ghép hai lớp theo thứ tự ưu tiên.
- Token reduction trung bình là 14,19%. No-memory có thể giảm token mạnh vì gần như không retrieve gì, nhưng điều đó không chứng minh chất lượng: baseline chỉ đạt 2/11 do thiếu evidence.

E10 cho thấy compaction phải giữ state, decision, TODO và constraint. Sliding window đã loại raw turn cũ nhưng durable note vẫn giữ `REVIEW-DEADLINE-1600`, `Friday`, `16:00`; buffer đơn thuần sẽ tăng token tuyến tính và cuối cùng vượt context budget.
