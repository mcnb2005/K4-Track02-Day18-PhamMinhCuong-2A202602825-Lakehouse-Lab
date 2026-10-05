# Reflection

Anti-pattern hệ thống dữ liệu AI dễ gặp nhất theo tôi là **“data lake biến thành data swamp”**: dữ liệu được ghi liên tục nhưng thiếu hợp đồng schema, catalog, version và thông tin nguồn gốc.

Với dữ liệu LLM observability, mỗi request có prompt, response, model version, latency, token usage và chi phí. Nếu producer tự ý đổi tên hoặc đổi kiểu cột, pipeline downstream có thể vẫn chạy nhưng tạo dashboard sai. Khi dữ liệu huấn luyện không ghim table version, thí nghiệm cũng không thể tái lập và khó xác định mẫu nào đã ảnh hưởng đến model.

Tôi sẽ giảm rủi ro bằng Bronze–Silver–Gold: Bronze giữ dữ liệu gốc, Silver chuẩn hóa và khử trùng lặp, Gold chỉ chứa metric đã kiểm tra. Schema enforcement chặn dữ liệu sai; schema evolution phải được bật có chủ đích. Catalog lưu owner, partition spec và lineage; mỗi training run ghi table, code và policy version. Compaction, snapshot expiry và orphan cleanup phải chạy định kỳ, kèm số liệu trước/sau.

Tôi dùng Codex để hỗ trợ thiết lập, chạy kiểm thử, tổng hợp bằng chứng và biên tập; chi tiết tại [AI_USAGE.md](AI_USAGE.md).
