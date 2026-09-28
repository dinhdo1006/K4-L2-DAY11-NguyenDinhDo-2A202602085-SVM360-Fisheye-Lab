# QA review · B1-dense

Mã khóa: 06B7-0660

| frame | object_ref | rule_id | nhận xét |
|---|---|---|---|
| adasind_001320.jpg | Car x221–244 y854–886 (không có số L trên overlay) | R01 | Box cao 32 px < H=40: xe nhỏ phía sau ThreeWheeler L6. Theo R01 vật thấp hơn H không box; box này thừa phạm vi. |
| adasind_012570.jpg | Bike x477–492 y954–983 (không có số L) | R01 | Box cao 29 px < H=40, nằm sau ThreeWheeler L4; ngoài phạm vi cần vẽ. |
| adasind_012570.jpg | Car x312–335 y922–961 (không có số L) | R01 | Box cao 39 px, sát ngưỡng H=40; cần soát lại trên ảnh gốc xem phần nhìn thấy có đạt 40 px không, nếu không thì bỏ. |
| adasind_036720.jpg | 5 box xa: Pedestrian x111–117, ThreeWheeler x131–151, Car x166–187, Car x225–261, Car x260–302 (y≈1019–1056) | R01 | Cả năm box cao 21–32 px < H=40 ở cuối đường; ngoài phạm vi. Vẽ nhiều box nhỏ không làm bài tốt hơn mà thêm box thừa. |
| adasind_001320.jpg | L4 Bike | R03 | Đúng luật: người lái + xe máy gộp một box `Bike`. |
| adasind_001320.jpg | L3 ThreeWheeler | R05 | `occluded=true` hợp lý vì bị người đi bộ L5 che một phần; `truncated` để false đúng vì không chạm vòng kính. |
| adasind_036720.jpg | L2 ThreeWheeler | R03 · R05 | Người ngồi trong xe ba bánh không box riêng — đúng R03. Xe bị vòng kính cắt ở mép phải, `truncated=true` đúng R05. |
| adasind_036720.jpg | L1 Car | R04 | Xe van trắng chở người gán `Car` — đúng ánh xạ R04. |
| cả 3 frame | ignore_region | R07 · R08 | Mỗi frame có 1 polygon `ego_body` bao tay/chân/tay lái ở góc dưới trái và 2 polygon `lens_border`; không thấy box nào nằm chủ yếu trong vùng ignore (R09). |

Ghi finding r2_qa: cell=L_only, rule_id có giá trị, why để trống.
