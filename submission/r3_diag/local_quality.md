# Đối chiếu chất lượng cục bộ — rectangle

Teaching reference, không phải gold set đã phê duyệt; không có điểm đạt tự động.
Nguồn: export r1_craft đã khóa SHA256 `aba943edb61c6dacb5910742b769265654b115621cb5094b3d39f86374bb1b74`; slice `B4-edge`.
Ghép hình học greedy một-một theo IoU ≥ 0.50, rồi so class; H ≥ 40 px.
Box trái nằm chủ yếu trong ignore_region reference không tính. Polygon, polyline, track không được chấm.
Đây là phép tính offline của lab, không phải báo cáo hay kết quả tương đương CVAT Premium.

Frame được tính: adasind_236370.jpg, adasind_258420.jpg, adasind_310008.jpg. Frame thiếu trong export: không.
TP=18; FP=3; FN=2; số lần đối chiếu=23; mean IoU của TP=0.836.

| Chỉ số | Micro | Macro | Nhãn thấp nhất |
|---|---:|---:|---:|
| accuracy | 0.783 | 0.957 | 0.913 |
| precision | 0.857 | 0.838 | 0.500 |
| recall | 0.900 | 0.878 | 0.500 |
| jaccard | 0.783 | 0.787 | 0.333 |
| dice | 0.878 | 0.856 | 0.500 |

| Nhãn | TP | FP | FN | Accuracy | Precision | Recall | Jaccard | Dice |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Bike | 4 | 1 | 0 | 0.957 | 0.800 | 1.000 | 0.800 | 0.889 |
| Car | 1 | 1 | 1 | 0.913 | 0.500 | 0.500 | 0.333 | 0.500 |
| Pedestrian | 8 | 1 | 1 | 0.913 | 0.889 | 0.889 | 0.800 | 0.889 |
| ThreeWheeler | 4 | 0 | 0 | 1.000 | 1.000 | 1.000 | 1.000 | 1.000 |
| Truck | 1 | 0 | 0 | 1.000 | 1.000 | 1.000 | 1.000 | 1.000 |

| Frame | TP | FP | FN | Accuracy | Precision | Recall |
|---|---:|---:|---:|---:|---:|---:|
| adasind_236370.jpg | 7 | 0 | 0 | 1.000 | 1.000 | 1.000 |
| adasind_258420.jpg | 6 | 3 | 2 | 0.545 | 0.667 | 0.750 |
| adasind_310008.jpg | 5 | 0 | 0 | 1.000 | 1.000 | 1.000 |

Confusion matrix: hàng = teaching reference; cột = export đã khóa.
`<missing>` là thiếu box; `<extra>` là box thừa. Xem `local_quality_confusion.csv`.

| Reference \ Export | Bike | Car | Pedestrian | ThreeWheeler | Truck | <missing> |
|---|---:|---:|---:|---:|---:|---:|
| Bike | 4 | 0 | 0 | 0 | 0 | 0 |
| Car | 0 | 1 | 0 | 0 | 0 | 1 |
| Pedestrian | 0 | 0 | 8 | 0 | 0 | 1 |
| ThreeWheeler | 0 | 0 | 0 | 4 | 0 | 0 |
| Truck | 0 | 0 | 0 | 0 | 1 | 0 |
| <extra> | 1 | 1 | 1 | 0 | 0 | 0 |

Chi tiết xung đột trong `local_quality_conflicts.csv`; dữ liệu máy đọc trong `local_quality.json`.
Mismatching label đóng góp một FP cho class vẽ và một FN cho class reference; attribute khác được báo riêng.
Micro accuracy đếm mỗi cặp ghép sai class là một lần đối chiếu; Jaccard đếm cả FP và FN.
Macro/worst bỏ nhãn không xuất hiện ở cả hai phía; chỉ số không có mẫu là N/A.
