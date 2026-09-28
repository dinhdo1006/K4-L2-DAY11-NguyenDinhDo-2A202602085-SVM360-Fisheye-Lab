# Báo cáo cá nhân — Day 11 SVM360 Fisheye Lab

- **Học viên:** Nguyễn Đình Đô — 2A202602085
- **Nhóm (check chéo 3 người):** Minh (B3-mid), Đô (B4-edge), Đức Anh (B1-dense) — chia bằng `lab11.py mode --members minh,do,ducanh --self do`
- **Slice của tôi:** B4-edge — `adasind_236370.jpg`, `adasind_258420.jpg`, `adasind_310008.jpg`
- **Vòng check chéo:** tôi soát bài đã khoá của Nguyễn Đức Anh (B1-dense); bài của tôi cũng do Nguyễn Đức Anh soát.

## 1. Vai Annotator — gán nhãn slice B4-edge

**Quy trình:** tạo task `Day11 · ADASIND · B4-edge · raw_fisheye`, import prefill (4 box ở frame 1 + 2 `lens_border` mỗi frame), soát/sửa prefill, vẽ thêm vật cao ≥ 40 px, vẽ `ego_body`, rồi export nháp → `draft` → `selfqc` → sửa → export lại. Qua **5 bản nháp** mới khoá.

**Bản khoá:** mã `ABA9-43ED` (`submission/r1_craft/lock.txt`) — 22 box, 9 polygon (6 `lens_border`, 3 `ego_body`); prefill giữ 3, sửa 1, tự vẽ mới 18.

**Lỗi self-QC đã phát hiện và sửa qua các bản nháp:**

| Vấn đề | Luật | Cách sửa |
|---|---|---|
| Thiếu `ego_body` ở cả 3 frame; lần đầu vẽ nhầm bằng **polyline** thay vì polygon nên tool không nhận | R07 | Vẽ lại bằng polygon `ignore_region`, `reason=ego_body` bao chân/tay/sàn xe máy góc dưới trái |
| 258420: ba box `Bike` chỉ ôm xe, không ôm người lái → cao < 40 px | R03, R01 | Kéo cạnh trên lên đầu người lái (người lái + xe = một box) |
| 258420: bỏ sót ThreeWheeler ở mép trái (x6–71) | R01 | Thêm box |
| 310008: hai box Pedestrian gần như trùng (IoU > 0,7) | — | Không phải trùng mà là 2 người đứng chồng; thu hẹp box người phía sau (x17–46, `occluded=true`) |

**Còn lại khi khoá:** một box `Bike` cao 33 px ở 258420 (x231–260) chưa rõ là người ngồi sau xe hay xe riêng — giữ nguyên và ghi finding/decision D1 thay vì sửa ngầm; cảnh báo "tên task thiếu raw_fisheye" do export từ job (decision D6). Phần K12 không làm, đã ghi `degrade k12`.

## 2. Vai QA — soát mù bài của Đức Anh (B1-dense)

- Nhận export đã khoá, chạy `lab11.py qa --slice B1-dense --code 06B7-0660`. Lần đầu **mã không khớp** vì file nhận được là bản export sau khi khoá (28 box so với 27 box trong lock) → yêu cầu đúng bản khoá, Đức Anh khoá lại và gửi mã mới; không đoán mã.
- Soát chỉ bằng luật, chưa mở reference. Kết quả trong `submission/r2_qa/qa_review.md` và 4 dòng `round=r2_qa` trong `findings.csv`:
  - **Lỗi chính: 8 box dưới ngưỡng H=40 (R01)** — 001320 Car 32 px; 012570 Bike 29 px và Car 39 px (sát ngưỡng); 036720 năm box xa 21–32 px.
  - **Làm đúng:** gộp rider vào `Bike` (R03), không box người ngồi trong ThreeWheeler (R03), van → `Car` (R04), `occluded`/`truncated` đúng nghĩa (R05), đủ `ego_body` + 2 `lens_border` mỗi frame (R07, R08).
  - Không thấy vật ≥ 40 px bị bỏ sót.

## 3. Nhận QA từ bạn trong nhóm

Nguyễn Đức Anh soát bản khoá B4-edge (mã `ABA9-43ED`) bằng luật, chưa xem reference; nhận xét nằm trong `qa_review.md` ở repo của Đức Anh. Mỗi nhận xét tôi đối chiếu lại trên ảnh gốc ở pha chẩn đoán: ca có căn cứ thì đưa vào rework, ca tôi giữ nhãn thì ghi lý do trong `findings.csv` và `40_decision_log.csv`.

## 4. Vai Diagnostician — đối chiếu với reference và model

Sau khi khoá và soát mù xong mới mở reference (`r1_craft/reference.txt`).

**Số đo (`r3_diag/local_quality.md`, IoU ≥ 0,5):** TP = 18, FP = 3, FN = 2; precision micro 0,857, recall 0,900; mean IoU của cặp khớp 0,836. Class yếu nhất là `Car` (precision/recall 0,50 — chỉ 2 box nên dao động mạnh). Frame 236370 và 310008 khớp 100 %; mọi khác biệt dồn vào 258420 (zone mid). IoU sweep: ở 0,3 zone mid của tôi khớp 8/9, ở 0,7 còn 5/9 → vài box vật nhỏ lệch vài pixel là đổi kết quả.

**Chẩn đoán (33 dòng `findings.csv`, `triage` hợp lệ):**

| Ca | Phân loại | Hành động |
|---|---|---|
| 258420 `R7` — người đi bộ áo hồng cao 51 px, reference và model đều thấy, tôi bỏ sót | E1_annotator_error, P1 | rework |
| 258420 `L6` — xe máy có người lái cao 54 px, chỉ tôi vẽ | E0_reference_defect, P1 | escalate (ticket 1) |
| 258420 `L1`/`R5` — xe con trắng, box reference hẹp hơn phần nhìn thấy; model khớp với tôi | E0_reference_defect, P2 | keep_with_reason |
| 258420 `L9` — người cầm ô sát ThreeWheeler, chưa rõ trong hay ngoài xe | E5_unresolved, P2 | keep_with_reason |
| `edge_zone` khác reference ở 310008/258420 (6 cặp) | E2_guideline_gap, P2 | đề xuất rule R05b (v1.1.0) |
| Model gán Truck/Car/Bus cho ThreeWheeler, tách người lái khỏi xe máy (lặp ở cả 3 frame) | E4_model_domain, P2–P3 | keep_with_reason, báo ai_team |

Bàn giao: `20_guideline_patch.md`, `30_escalation_ticket.md`, `40_decision_log.csv` (6 quyết định, 1 escalated), `10_error_card.md`, ảnh minh chứng trong `screenshots/`.

## 5. Rework và C0

- **Rework (mã `44D7-5B3F`):** thêm box `Pedestrian` cho `R7` ở 258420 → zone mid matched 7 → 8, missing 2 → 1. Spurious mid tăng 3 → 4 vì box `Bike` x231–260 được kéo ôm người lái (33 → 57 px) nên vào phạm vi mà reference không có; giải thích trong `rework/delta.md`, decision D1.
- **C0 (mã `906A-D837`):** vẽ 3 box, khớp cả 3 với reference nhưng **bỏ sót 3 xe đỗ ven đường** (xe đạp dựng, hai xe ba bánh đỗ) → 3 finding `calib` E1. Bài học áp dụng cho slice chính: vật đứng yên cũng phải box nếu cao ≥ 40 px.

## 6. Tự nhìn lại

- **Điều học được:** lỗi của tôi không nằm ở vùng méo rìa (zone edge khớp 7/7) mà ở **frame dày đặc, vật nhỏ sát ngưỡng H**. Số đo thấp không tự động là lỗi người: 2/3 box "thừa" của tôi có bằng chứng reference sai, nên phải soi ảnh trước khi kết luận.
- **Check chéo giúp gì:** soát bài Đức Anh giúp tôi thấy lỗi R01 (vẽ vật quá nhỏ) rất dễ mắc khi cố "vẽ đủ"; việc đòi đúng bản khoá cho thấy vì sao cần mã khoá — bản trong CVAT có thể đã đổi sau khi khoá.
- **Nếu làm lại:** phóng to từng cụm vật ở frame dày đặc trước khi vẽ; dùng đúng công cụ polygon cho `ignore_region` ngay từ đầu; export từ trang task để giữ tên `raw_fisheye`; làm C0 trước slice chính đúng thứ tự lab.
