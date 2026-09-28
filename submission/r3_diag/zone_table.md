# Zone table (slice của bạn)

Lệnh `python3 lab11.py model` tự ghi bảng số (cùng cách đếm với `r1_craft/compare.md` và `model_compare.md`); chạy lại lệnh sẽ cập nhật bảng và giữ nguyên phần nhận xét. Bạn chỉ viết mục Nhận xét.

| Zone | n_ref | L missing | L spurious | M missing (`LR_noM` + `R_only`) | M thừa (`LM_noR` + `M_only`) | Lỗi L chính (`what`) |
|---|---:|---:|---:|---:|---:|---|
| center | 4 | 0 | 0 | 2 | 2 | — |
| mid | 9 | 2 | 3 | 3 | 8 | SPURIOUS (2) |
| edge | 7 | 0 | 0 | 1 | 1 | — |

## Nhận xét

- Zone nào người (L) và model (M) gãy nhiều nhất, dẫn số ở bảng trên: **mid** gãy nhiều nhất cho cả hai phía — L có 2 missing + 3 spurious trên 9 box reference, M có 3 missing + 8 thừa. Tất cả khác biệt của L đều nằm ở frame 258420 (cụm xe máy, người đi bộ và xe ba bánh ở xa, cao 40–90 px). Zone edge (7 ref) L khớp đủ, dù đây là vùng méo nhất; center khớp 4/4.
- Giả thuyết vì sao (méo fisheye, box lỏng, thiếu `ego_body`, ...) và giới hạn của slice ba frame: lỗi của L ở mid không do méo mà do **mật độ và kích thước nhỏ** — các vật ở 258420 chồng lên nhau, sát ngưỡng H=40, nên dễ bỏ sót (R7 người áo hồng) hoặc vẽ vật mà reference không vẽ (L6 xe máy phía sau, L9 người cầm ô). Hai trong ba "spurious" của L có bằng chứng là reference bỏ sót/box hẹp (E0, xem screenshot `258420_far_L_R_M.png`), không chắc là lỗi người. Lỗi của M mang tính hệ thống: gán Truck/Car/Bus cho ThreeWheeler (R04) và tách người lái khỏi xe máy (R03) — đây là khác biệt quy ước của model đóng băng, không phải do zone. Giới hạn: chỉ 3 frame, 20 box reference, gần như mọi lỗi dồn vào một frame, nên không thể kết luận "mid khó hơn edge" cho cả dataset; zone bán kính chỉ là vị trí trên ảnh, không nói vật gần hay xa xe.
