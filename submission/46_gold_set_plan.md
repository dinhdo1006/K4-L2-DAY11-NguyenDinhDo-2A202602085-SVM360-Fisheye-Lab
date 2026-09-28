# Đề xuất gold set theo camera — tình huống giả lập

**Đầu bài:** 50.000 frame từ bốn camera SVM, ngân sách chọn 200 frame để review/gold. Đây là tình huống trên slide,
**không phải** 50.000 frame có trong repo. Phân bổ đúng 200 ở `45_sampling_plan.csv` cho bốn camera, mỗi camera có
normal và hard slice. “Gold set” ở đây là **kế hoạch tạo** reference sau kiểm chứng, không phải teaching reference
ADASIND hoặc nhãn bạn vừa vẽ. Nếu cần, dùng `notebooks/day11-svm360-colab.ipynb` để thử tổng phân bổ; notebook
không làm thay phần lý do.

| camera_id | Hard case cần chọn | Vì sao dễ sai | Annotation space / calibration cần giữ | Cách review trước khi gọi là gold |
|---|---|---|---|---|
| front | Cụm xe máy, người đi bộ và ThreeWheeler dày đặc ở xa, cao 40–90 px; người lái xe hai bánh; ngược sáng | Vật chồng nhau sát ngưỡng H dễ bị bỏ sót hoặc vẽ thêm (như 258420: R7 bị sót, L6 chỉ một bên thấy); rider dễ bị tách khỏi xe | Box trên ảnh fisheye gốc (không undistort); intrinsics + vòng kính của camera front; version rules | Hai annotator vẽ độc lập, người thứ ba phân xử mọi cặp IoU<0,5 hoặc khác class; ca vật sát H phải phóng ảnh ≥300% |
| rear | Xe áp sát khi lùi, vật thấp ngay sau xe, ban đêm có đèn pha | Méo mạnh ở tâm-đáy ảnh; ego_body/bumper chiếm đáy dễ bị box nhầm | Polygon `ego_body` riêng của rear; vòng kính rear; timestamp để khớp với cảm biến lùi | Soát riêng ignore_region trước box; kiểm không box nào ≥50% trong ignore (R09) |
| left | Vật ở seam front-left/rear-left, người trên vỉa hè bị vòng kính cắt | Một vật có thể có hai box trên hai camera; `truncated`/`edge_zone` dễ lẫn (như 310008 lệch edge_zone với reference) | Extrinsics left so với front/rear; vùng seam đã hiệu chuẩn; rule `edge_zone` v1.1.0 | Review theo cặp camera tại seam với timestamp đồng bộ; attribute phân xử theo rule đã chốt, không theo đa số |
| right | Xe vượt ở seam front-right, vật bị gương che, ngược sáng | Che khuất một phần + méo rìa làm box lỏng; occluded vs truncated lẫn nhau | Extrinsics right; mask gương/thân xe; version rules | Như left; thêm kiểm occluded/truncated theo R05 cho mọi vật chạm rìa |

- Khi nào cần refresh gold set (đổi camera, calibration hoặc rule): khi thay camera/ống kính hoặc vị trí gắn (vòng kính và ego_body đổi), khi calibration intrinsics/extrinsics được đo lại, khi rules đổi version (ví dụ thêm R05b `edge_zone` ở v1.1.0 → phải soát lại attribute của toàn bộ gold), hoặc khi model/QA liên tục báo cùng một loại bất đồng ở một camera. Reference hiện tại (teaching reference ADASIND) chỉ là bản sửa tay một camera, đã có ca nghi sai (ticket 1) nên không được dùng làm gold.
- Một ca seam/cross-camera cần policy và evidence trước khi ghép hai box: một xe máy đi qua góc trước-trái xuất hiện đồng thời ở camera front (vùng edge) và left (vùng mid) với hai box khác kích thước. Trước khi coi là "cùng một vật" hoặc xoá một box cần: timestamp đồng bộ hai camera, calibration extrinsics để chiếu hai box về cùng hệ toạ độ (BEV), và policy output (giữ box ở cả hai ảnh gốc hay chỉ một box trên BEV). Người soát quyết định policy trước khi tính DUPLICATE.
- Vì sao peer agreement hoặc quality report trên ảnh một camera chưa chứng minh gold set đúng cho cả bốn camera: hai người đồng ý với nhau vẫn có thể cùng sai theo một quy ước (như cùng bỏ `edge_zone` khi rule thiếu), và quality report chỉ đo độ khớp với một reference có thể sai. ADASIND chỉ có một camera nên không có seam, không có rear/side, không kiểm được calibration giữa camera; đồng thuận trên đó không nói gì về ca cross-camera.
