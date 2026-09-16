# Mini guideline - nhóm:   |  người gán: laihoangduy2424  |  ngày: 2026-09-16

## 1. Luật bắt buộc

- Dùng đúng bộ 17 điểm COCO, đúng tên và đúng thứ tự.
- Mọi người trong ảnh đều có đủ 17 điểm; điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo cơ thể người, không theo phía trái/phải của ảnh.
- Khớp bị che nhưng còn trong khung: `v = 1` và vẫn đặt chấm ở vị trí ước lượng.
- Khớp đã ra ngoài mép ảnh: `v = 0`.
- Không dùng `Hidden` (`h`).

## 2. Luật của nhóm

| Tình huống | Luật áp dụng | Lý do |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Ước lượng vị trí giải phẫu theo trục thân và hai chân. Nếu hông còn trong khung nhưng bị quần áo che thì dùng `v = 1`. | Hông thường không có mốc bề mặt rõ; cần giữ quy tắc nhất quán thay vì bỏ điểm. |
| Tai bị tóc hoặc mũ che một phần | Nếu tâm tai vẫn nhìn rõ thì `v = 2`; nếu tai bị che đến mức chỉ có thể suy ra vị trí nhưng vẫn ở trong khung thì `v = 1`. | Phân biệt “nhìn thấy khớp” với “biết khớp ở đâu”. |
| Người bị cắt ở mép ảnh | Khớp còn nằm trong khung nhưng bị vật/người khác che dùng `v = 1`; khớp thực sự nằm ngoài biên ảnh dùng `v = 0`. | `v = 0` loại khớp khỏi phép chấm OKS nên chỉ dùng khi khớp đã ra ngoài khung. |
| Cổ tay nằm sau tay lái / sau thân mình | Nếu vị trí cổ tay còn trong ảnh và có thể suy ra từ cẳng tay thì đặt chấm ước lượng, `v = 1`. | Không xoá khớp bị che. |
| Hai người chồng lên nhau | Hoàn thành toàn bộ 17 điểm của một người trước rồi mới sang người tiếp theo; bám theo liên tục vai–khuỷu–cổ tay và hông–gối–cổ chân của đúng cơ thể. | Giảm nguy cơ `nham_nguoi` và kéo xương sang cơ thể bên cạnh. |
| Người nhỏ | Với 20 ảnh core hiện tại vẫn gán tất cả người đã được chọn trong bộ dữ liệu; không tự đặt thêm ngưỡng kích thước mới. | GUIDE cho biết bộ core đã chọn các người đủ lớn để gán. |

> Ảnh mẫu CVAT chưa có trong hai notebook được cung cấp. Trước khi nộp chính thức, chèn screenshot CVAT tương ứng cho các luật có occlusion/outside.

## 3. Ba ca mơ hồ đã gặp

### Ca 1 - ảnh `train_01`, người thứ `1`, khớp `left_ankle`

- **Mơ hồ:** `check_pose_labels.py` cảnh báo người nằm gọn trong ảnh nhưng có nhiều khớp `v=0`.
- **Quyết định:** giữ `left_ankle` ở `v=0` khi khớp thực sự nằm dưới mép ảnh.
- **Bằng chứng:** ảnh `train_01` cao 427 px, còn export COCO ghi `left_ankle` của người thứ nhất tại y ≈ 504 px; vị trí này đã nằm ngoài khung.
- **Nếu quyết ngược lại:** dùng `v=1` sẽ biến một khớp ngoài khung thành khớp bị che trong khung và làm sai ý nghĩa visibility.

### Ca 2 - ảnh `train_02`, người thứ `1`, vùng vai/hông

- **Mơ hồ:** checker báo thứ tự trái/phải của vai và hông ngược với thứ tự hai mắt theo trục x.
- **Quyết định:** không đổi nhãn chỉ vì thứ tự x; xác định trái/phải theo cơ thể và liên tục giải phẫu của từng chi.
- **Vì sao:** người xoay thân hoặc đứng nghiêng có thể làm thứ tự chiếu trên ảnh khác với thứ tự của mắt.
- **Nếu quyết ngược lại:** model có thể học sai left/right; khi augmentation lật ngang, `flip_idx` còn khuếch đại lỗi đó.

### Ca 3 - ảnh `train_06`, người thứ `1`, `left_ear` / `right_ear`

- **Mơ hồ:** các mốc mặt và tai không nhìn rõ hoàn toàn nhưng vẫn nằm trong khung.
- **Quyết định:** với khớp còn trong khung và chỉ bị che/khó thấy, giữ điểm ước lượng và dùng `v=1`.
- **Vì sao:** visibility report trước gold cho thấy tai là nhóm có tỷ lệ `v=1` cao nhất, phản ánh đây là tình huống occlusion lặp lại.
- **Nếu quyết ngược lại:** đổi sang `v=0` sẽ loại các khớp đó khỏi OKS và làm model không học được vị trí ước lượng của khớp bị che.

## 4. Sau khi so visibility report với bạn cùng nhóm

Hai notebook được cung cấp chưa chứa dữ liệu của bài bạn cùng nhóm, nên chưa thể điền phần so sánh `%v=1` một cách trung thực.

- Khớp lệch `%v=1` nhiều nhất: **chưa có dữ liệu so sánh**
- Nguyên nhân: **chưa có visibility report của bạn cùng nhóm**
- Luật mới sau khi thống nhất: **chờ kết quả kiểm chéo**

### Thống kê visibility của bản trước gold

Ba khớp có `%v=1` cao nhất:

1. `left_ear`: 59%
2. `right_ear`: 48%
3. `left_wrist`: 41%
