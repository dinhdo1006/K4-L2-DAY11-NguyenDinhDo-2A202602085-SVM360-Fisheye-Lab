# Tự soát

- adasind_258420.jpg L4: chiều cao < H (xem lại phạm vi)
- Tên task thiếu raw_fisheye

## Cảnh báo đã xử lý qua các bản nháp
- Thiếu `ego_body` ở cả 3 frame: bản đầu vẽ nhầm bằng polyline; đã vẽ lại bằng polygon `ignore_region`, `reason=ego_body` bao chân/tay/sàn xe máy góc dưới trái ở 236370, 258420, 310008.
- 258420: ba box `Bike` (x123–159, x172–191, x190–241) chưa ôm người lái → kéo cạnh trên lên đầu người lái theo R03; thêm `ThreeWheeler` bị bỏ sót ở mép trái (x6–71) và `Pedestrian` cầm ô (x286–301).
- 310008: cảnh báo "hai box cùng class IoU > 0.7" là hai người đứng chồng nhau, không phải trùng; đã thu hẹp box người áo trắng phía sau (x17–46, `occluded=true`), giữ box người áo đen phía trước (x25–80).

## Cảnh báo còn lại khi khoá
- 258420 box `Bike` x231–260, y816–849 cao 33 px (< H=40): người áo hồng sau xe máy phía trước, chưa xác định là xe riêng hay người ngồi sau. Giữ nguyên trong bản khoá và ghi thành finding để xử lý ở P4/P5 (R01).
- "Tên task thiếu raw_fisheye": export từ job nên XML chỉ có `<job>`, không có tên task; task trong CVAT là task raw_fisheye của slice B4-edge, format CVAT for images 1.1.

## Checklist thủ công
- [x] Phạm vi H=40 và vật cần vẽ
- [x] lens_border và ego_body
- [x] Class sáu nhãn
- [x] Rider và Bike
- [x] Geometry trên ảnh fisheye gốc
- [x] truncated và occluded
- [x] Vật thiếu hoặc box trùng
- [x] ignore_region có reason
- [x] Tên task raw_fisheye và export CVAT 1.1 (export CVAT for images 1.1; tên task không nằm trong XML vì export từ job — decision D6)

## Fill ratio (K12)
chưa vẽ polygon K12 (degrade)
