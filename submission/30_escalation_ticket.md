# Escalation ticket

## Ticket 1

- **Frame:** `adasind_258420.jpg` (slice B4-edge), object `L6` — Bike x172–191, y794–848 (cao 54 px), zone mid. Cell `L_only`: chỉ nhãn của mình có; teaching reference (R) và model (M) đều không có box ở đây.
- **Ảnh chụp:** `submission/screenshots/258420_far_L_R_M.png` (box cyan hẹp ở giữa ảnh, sau xe máy lớn phía trước: một xe máy có người lái áo tối, thấy đầu người lái tới bánh xe).
- **Expected impact:** Nếu reference thật sự bỏ sót, box đúng của annotator bị tính SPURIOUS (FP), kéo precision của `Bike` xuống 0,80 và làm sai chẩn đoán "annotator vẽ thừa" ở zone mid. Cùng frame còn một ca reference box hẹp hơn phần nhìn thấy (L1+R5, xe con trắng) — hai ca cho thấy frame dày đặc 258420 có thể chưa được soát kỹ trong reference.
- **Owner:** `data_ops` (người giữ teaching reference)
- **Recommendation:** Người soát thứ hai mở ảnh gốc 258420 ở 300–400%, xác nhận vật ở x172–191 là xe máy có người lái cao ≥ 40 px. Nếu đúng: thêm box `Bike` vào reference và sửa R5 ôm đủ xe con (x≈213–277), ghi version reference mới. Nếu không: ghi lý do (ví dụ phần nhìn thấy < H) để annotator xoá box ở round sau. Trong lúc chờ, finding giữ `action=escalate`, decision log `status=escalated`; không sửa ngầm bản đã khóa.
