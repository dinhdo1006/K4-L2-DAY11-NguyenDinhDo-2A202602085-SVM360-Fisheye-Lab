# Exit ticket

Đọc `docs/10-svm360-reading-vi.md` trước khi trả lời câu 1–2. Các câu về zone, `why`, rework, parking và sampling
đã nằm trong file tương ứng nên không hỏi lại ở đây.

1. Một vật ở vùng seam giữa hai camera thật xuất hiện với hai box khác nhau: đó là lỗi `DUPLICATE` hay cần một quy
   tắc riêng? Vì sao? **Không phải DUPLICATE mặc định.** Hai box nằm trên hai ảnh gốc của hai camera khác nhau, mỗi box đúng trong không gian ảnh của camera đó (có thể là `edge` ở camera này, `mid` ở camera kia). DUPLICATE chỉ đúng khi hai box cho cùng một vật trên **cùng một ảnh**. Ca seam cần quy tắc riêng: giữ cả hai box trên ảnh gốc, gắn cùng một ID đối tượng khi đã xác minh bằng timestamp + calibration, và policy output quyết định có hợp nhất trên BEV hay không.
2. Một vật đi qua nhiều frame trên cùng camera: khi nào giữ cùng track ID, khi nào thêm keyframe hoặc trạng thái
   Outside? Nêu bằng chứng sẽ cần trước khi nối track qua hai camera. Giữ cùng track ID khi vẫn là cùng một vật quan sát liên tục (kể cả bị che ngắn rồi xuất hiện lại ở vị trí hợp lý). Thêm keyframe khi hình học đổi lớn — ví dụ vật đi từ tâm ra rìa vòng kính và bị méo/cắt, hoặc box nội suy lệch khỏi vật. Đặt Outside khi vật ra khỏi trường nhìn hoặc đi vào vùng ignore (`lens_border`, `ego_body`), rồi mở lại cùng ID nếu vật quay lại. Trước khi nối track qua hai camera cần: timestamp đồng bộ, extrinsics/intrinsics của cả hai camera để chiếu về cùng hệ toạ độ, vị trí và vận tốc khớp tại seam, và policy output đích cho phép nối.
3. Nhìn lại cả buổi: một chỗ bạn tin nhãn mình đúng nhưng reference hoặc người soát nghĩ khác (dẫn frame/`object_ref`),
   bạn đã xử lý thế nào, và nếu làm lại slice này bạn sẽ đổi gì trong cách làm? Ở `adasind_258420.jpg` object `L6` (xe máy có người lái phía sau, x172–191, cao 54 px) chỉ mình vẽ, reference và model đều không có nên bị tính SPURIOUS. Mình không xoá để khớp số mà ghi finding E0_reference_defect, escalate cho data_ops (ticket 1, decision D2) kèm screenshot. Nếu làm lại, mình sẽ phóng to từng cụm vật nhỏ ở frame dày đặc trước khi vẽ để không sót người áo hồng R7, vẽ `ego_body` đúng bằng polygon ngay từ đầu, và export từ trang task để giữ tên `raw_fisheye`.
