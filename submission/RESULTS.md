# Kết quả và diễn giải

Các số dưới đây lấy từ output của tám notebook đã thực thi trong `submission/notebooks/`.

| Notebook | Kết quả chính | Diễn giải |
|---|---|---|
| NB1 | Schema sai bị chặn; `tier` được thêm; 2 nhóm tier | Delta enforcement ngăn ghi sai kiểu, còn evolution chỉ xảy ra khi bật `schema_mode="merge"`. |
| NB2 | 200 → 55 files; speedup 7.8×; pruning 55× | Compaction giảm overhead mở file; Z-order làm min/max stats cô lập `user_id` mục tiêu vào một file. |
| NB3 | MERGE 100K; 5 versions; RESTORE; 0 dòng `score < 0` | RESTORE tạo transaction mới và giữ lịch sử, giúp quay lại trạng thái tốt mà không xóa audit trail. |
| NB4 | 200,000 Bronze → 190,052 Silver; Gold 8 ngày × 3 model | Dedup loại 9,948 bản ghi; Gold tạo metric latency, lỗi và chi phí phục vụ phân tích. |
| NB5 | Pruning 10×; field ID 4 giữ nguyên; 2 partition specs | Hidden partitioning giảm file scan khi lọc `ts`; field ID và partition evolution tránh rewrite dữ liệu cũ. |
| NB6 | Compaction 18×; skip 90%; 3 orphans đã xóa; Iceberg còn 3 snapshots | Maintenance phải ghép expiry với orphan sweep; chỉ giảm snapshot metadata chưa chắc đã thu hồi file vật lý. |
| NB7 | Amplification 200×; int8 nhỏ 5.8×; recall@10 0.904; fidelity 1.000 | Quantization giảm mạnh dung lượng nhưng vẫn giữ đúng chủ đề; external index cũ vẫn trả 8 tài liệu đã xóa nên phải nhận delete events. |
| NB8 | Replay 1,578 steps đúng version; 5 lượt → 1 catalog read; 4 bucket trainable | Pin version giúp tái lập; cache giảm round-trip; `UNCLASSIFIED` bị loại khỏi tập train theo quy tắc minh họa của lab. |

## Cổng kiểm tra

- `scripts/verify_lite.py`: 9/9 PASS
- `pytest`: 24/24 PASS
- `scripts/run_all.py`: 8/8 PASS
