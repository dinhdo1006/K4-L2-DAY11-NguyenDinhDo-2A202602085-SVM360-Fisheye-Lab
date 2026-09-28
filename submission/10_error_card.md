# Error analysis card

## Zone × block

| zone | block | what | count |
|---|---|---|---:|
| center | B4 | MISSING | 2 |
| center | B4 | SPURIOUS | 2 |
| edge | B4 | ATTRIBUTE | 2 |
| edge | B4 | MISSING | 1 |
| edge | B4 | SPURIOUS | 1 |
| mid | B4 | BOX_GEOMETRY | 1 |
| mid | B4 | MISSING | 5 |
| mid | B4 | SPURIOUS | 12 |
| unknown | B1 | SPURIOUS | 4 |

## Top defects
- SPURIOUS: 19 (ví dụ frame adasind_001320.jpg)
- MISSING: 8 (ví dụ frame adasind_258420.jpg)
- ATTRIBUTE: 2 (ví dụ frame adasind_310008.jpg)

## Phân tích của bạn

Hai bảng trên do `python3 lab11.py card` tính từ `findings.csv`; chạy lại lệnh sẽ cập nhật bảng và giữ nguyên mục này. Viết cho lỗi nổi bật nhất, dẫn frame/`object_ref`.

- Nguyên nhân khả dĩ (`why`) và vì sao bạn nghĩ vậy: lỗi nổi bật nhất là **SPURIOUS ở zone mid (12 dòng)**, nhưng tách ra thì phần lớn không phải lỗi của annotator. 10/12 là box thừa của model (M_only/LM_noR): model gán Truck/Car/Bus cho ThreeWheeler (M4, M8, M11 ở 258420; M6, M7 ở 310008) và tách người lái khỏi xe máy (M5, M10 ở 258420; M2 ở 236370) → `E4_model_domain`, lặp lại ở 3 frame nên là mẫu hệ thống, không phải một box lệch. Phía annotator ở 258420: `L6` (xe máy phía sau, cao 54 px) và `L1` (xe con trắng) có bằng chứng reference bỏ sót/box hẹp → `E0_reference_defect`; `L9` (người cầm ô sát ThreeWheeler) chưa đủ bằng chứng → `E5_unresolved`. Lỗi thật của mình là MISSING `R7` (người áo hồng cao 51 px, R và M đều thấy) → `E1_annotator_error`, do frame dày đặc, vật nhỏ sát H và không phóng to từng cụm. 4 dòng SPURIOUS zone `unknown`/B1 là finding QA cho bài B1-dense của bạn khác (box dưới H, R01).
- Cách sửa và ai nhận việc (`owner`): `annotator` (mình) thêm box Pedestrian cho R7 ở round rework (P1). `data_ops` xác minh L6 và sửa R5 trong reference theo ticket 1 (escalate). `ai_team` không sửa nhãn người theo model; ghi nhận model cần ánh xạ ThreeWheeler và quy ước rider (R03/R04) trước khi dùng làm pre-label. `guideline` chốt rule `edge_zone` (R05b, v1.1.0) cho 2 dòng ATTRIBUTE.
- Bằng chứng (ảnh trong `screenshots/`, dòng findings, rule): `screenshots/258420_far_L_R_M.png` (L cyan, R cam, M hồng cho cụm xe ở 258420), `screenshots/310008_edge_zone_pedestrians.png`; findings `r3_diag` 258420 `R7+M12` (R01), `L6` (R01, escalate), `L1+M6`/`R5` (R02), các dòng `M_only` (R03/R04), `L4+R3+M1` ATTRIBUTE (R05); `r3_diag/local_quality.md` (TP=18, FP=3, FN=2, precision Car 0,50 là class yếu nhất); `30_escalation_ticket.md`; `40_decision_log.csv` D2–D5.
