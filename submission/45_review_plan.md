# Kế hoạch review từ lỗi quan sát được

Từ `findings.csv` và `zone_table.md`, chọn **hai lát cắt của bài ADASIND một camera** cần review trước. Bảng này
giải thích dữ liệu thật bạn vừa làm; nó không thay cho kế hoạch bốn camera giả lập ở `45_sampling_plan.csv`.

| Lát cắt / frame | Số ca và loại lỗi | Vì sao review trước | Bằng chứng cần giữ |
|---|---|---|---|
| Frame dày đặc, vật nhỏ sát H=40 ở zone mid (258420) | 5 khác biệt L-R: 1 MISSING (R7), 3 SPURIOUS (L1, L6, L9), 1 BOX_GEOMETRY (L1+R5); thêm 1 box 33px dưới H | Toàn bộ lỗi của L dồn vào frame này; 2/3 SPURIOUS có dấu hiệu reference sai (E0) nên cần người soát thứ hai trước khi tính là lỗi annotator | `screenshots/258420_far_L_R_M.png`, `r1_craft/compare.html`, ticket 1, decision D1–D3, D5 |
| Vật sát rìa vòng kính trái — attribute `edge_zone` (310008, 258420) | 6 cặp `mismatching_attributes`, tất cả chỉ khác `edge_zone` | Không phải lỗi hình học nhưng lặp lại có hệ thống; rules v1.0.0 thiếu định nghĩa nên mọi annotator sẽ lệch theo cách riêng | `screenshots/310008_edge_zone_pedestrians.png`, `r3_diag/local_quality_conflicts.csv`, `20_guideline_patch.md`, decision D4 |

Giới hạn của kết luận từ ba frame ADASIND: chỉ 3 frame, 20 box reference, một camera; gần như mọi khác biệt nằm ở một frame nên không đủ để nói zone mid khó hơn edge cho cả dataset. Teaching reference là bản sửa tay có thể sai (đã thấy 2 ca nghi E0), nên số precision/recall chỉ là độ khớp với reference, không phải tỷ lệ lỗi thật. Zone bán kính là vị trí trên ảnh, không nói vật gần hay xa xe.

## Chuyển sang kế hoạch bốn camera giả lập

Cách soát độ phủ của 200 frame ở `45_sampling_plan.csv` (kể cả tránh đếm nhiều frame liền nhau trong cùng cảnh
như nhiều ca độc lập), và vì sao kế hoạch đó chỉ giúp tìm ca cần soi, chưa đo được tỷ lệ lỗi: chọn frame theo từng `camera_id` và từng loại normal/hard, rồi lấy mẫu phân tầng theo **clip/cảnh** (tối đa 1–2 frame mỗi clip, cách nhau ≥ 2 giây) để 200 frame là 200 tình huống khác nhau chứ không phải 20 frame liên tiếp của một cảnh. Mỗi ô ghi số clip, điều kiện ánh sáng/thời tiết, mật độ và số vật ở seam để kiểm độ phủ; ô nào thiếu thì bổ sung trước khi review. Hard slice được chọn có chủ đích theo rủi ro (như bài học từ 258420: vật nhỏ dày đặc, vật sát rìa kính, người lái xe hai bánh), nên tỷ lệ lỗi trên đó bị thổi phồng so với toàn bộ 50.000 frame; muốn ước lượng tỷ lệ lỗi thật cần thêm một mẫu ngẫu nhiên riêng có trọng số.
