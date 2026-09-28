# Rework delta

| zone | matched before | matched after | missing before | missing after | spurious before | spurious after |
|---|---:|---:|---:|---:|---:|---:|
| center | 4 | 4 | 0 | 0 | 0 | 0 |
| mid | 7 | 8 | 2 | 1 | 3 | 4 |
| edge | 7 | 7 | 0 | 0 | 0 | 0 |

## Findings action=rework
- adasind_258420.jpg R7 MISSING: đã sửa
- adasind_258420.jpg R7+M12 MISSING: đã sửa

## Giải thích

- **Cải thiện thật:** zone mid matched 7 → 8, missing 2 → 1 nhờ thêm box `Pedestrian` cho người đi bộ áo hồng `R7` ở 258420 (x94–108, y788–844) — đúng finding P1 `E1_annotator_error`, `action=rework`.
- **Spurious mid 3 → 4 không phải lỗi mới:** trong rework, box `Bike` x231–260 (người áo hồng ngồi trên xe máy phía sau xe L4) được kéo cạnh trên lên đầu người lái theo R03, cao 33 → 57 px. Trước đó box dưới H=40 nên matcher bỏ qua; nay vào phạm vi nhưng reference không có box ở đây nên bị tính SPURIOUS. Model cũng thấy một người tại đúng chỗ này (M7, cao 51 px), nên đây là ca bất đồng với reference (xem finding M7 và decision D1), không phải box bịa thêm.
- **Không đổi:** missing còn lại ở mid (xe con trắng `R5`, IoU < 0,5 với L1) và các ca `L6`, `L9` được giữ theo decision D2, D5 và finding E0/E5; không sửa để làm đẹp số.
- **Giới hạn:** chỉ 3 frame, số thay đổi 1–2 box; kết quả trước/sau đo độ khớp với teaching reference (có ca nghi sai), không phải tỷ lệ lỗi thật.
