# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: ______   Nhóm: ______   Ngày: 2026-09-16

## 1. Nhãn của tôi

Số liệu phần này lấy từ visibility report **trước khi nhận gold** trong `Untitled13.ipynb`.

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 334 / 129 / 30 |
| Thời gian trung bình mỗi ảnh | Chưa có dữ liệu trong notebook |

Ba khớp có `%v=1` cao nhất:

1. `left_ear`: 59%
2. `right_ear`: 48%
3. `left_wrist`: 41%

Tai và cổ tay là các vị trí có nhiều trường hợp khó quan sát trực tiếp. Tuy nhiên, `%v=1` chỉ phản ánh tần suất khớp bị che; nó không phản ánh toàn bộ độ khó định vị giải phẫu. Ví dụ hông có thể phải ước lượng theo trục thân và chân dù tỷ lệ `v=1` thấp hơn tai.

## 2. Chấm với gold

Hai notebook được cung cấp không chứa output của `tools/evaluate_pose_annotations.py` hoặc `outputs/eval_vs_gold.json`. Vì vậy các số dưới đây được để trống thay vì ước lượng.

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | — | — |
| OKS@0.50 | — | — |
| OKS@0.75 | — | — |
| Lỗi `dao_trai_phai` | — | — |
| Lỗi `nham_nguoi` | — | — |
| Lỗi `xoa_khop_bi_che` | — | — |

**Tôi đã sửa gì giữa hai lần chạy:** chưa có log đánh giá gold trong dữ liệu được cung cấp nên chưa thể ghi chính xác ảnh/người/keypoint đã rework.

**Lỗi đảo trái/phải:** chưa thể kết luận từ hai notebook. Output trước gold chỉ có các **cảnh báo heuristic** ở một số ảnh; cảnh báo không đồng nghĩa với lỗi gold-confirmed.

## 3. Kiểm chéo

Hai notebook không chứa bài hoặc visibility report của bạn cùng nhóm. Phần kiểm chéo vì vậy chưa thể điền mà không bịa dữ liệu.

| Khớp | Bạn | Họ | Lệch | Nguyên nhân |
| --- | ---: | ---: | ---: | --- |
| — | — | — | — | Chưa có dữ liệu bạn cùng nhóm |

Luật sử dụng trong lúc chờ kiểm chéo: khớp còn trong khung nhưng bị che dùng `v=1`; khớp thực sự ngoài biên ảnh dùng `v=0`; trái/phải xác định theo cơ thể người.

## 4. Model

Notebook `day4_pose_finetune_yolo26.ipynb` chạy với GPU Tesla T4, `yolo26n-pose.pt`, `imgsz=640`, `batch=8`, tối đa 80 epoch và `patience=30`. Early stopping dừng ở epoch 39; kết quả tốt nhất được ghi nhận ở epoch 9.

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

1. **`pose_mAP50-95` thay đổi bao nhiêu?**  
   Tăng `+0.0055`, từ `0.6853` lên `0.6908`. Mức tăng nhỏ phù hợp với bối cảnh chỉ có 20 ảnh train: fine-tune chỉ điều chỉnh nhẹ model pretrained cho tập nhãn hiện tại, không đủ dữ liệu để xem đây là cải thiện sản phẩm tổng quát.

2. **`box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm người hay tìm khớp dễ hơn?**  
   Sau fine-tune, `box_mAP50-95 = 0.8041` và `pose_mAP50-95 = 0.6908`, chênh `0.1133`. Trên tập test này, model tìm và định vị người tốt hơn việc đặt chính xác toàn bộ 17 keypoint.

3. **Một ảnh test model đoán sai:**  
   Ở `test_07`, hình prediction cho thấy model kéo các keypoint phần thân dưới xuống khu vực phía dưới quầy dù phần chân không quan sát được. Có thể gọi đây là lỗi **trượt hẳn** ở các keypoint thân dưới. Nhãn test của `test_07` cũng đặt nhiều khớp chân ở `v=0`, cho thấy phần đó không có ground-truth hữu dụng trong khung.

4. **Ảnh nào có OKS thấp nhất giữa nhãn của tôi và model? Ai đúng?**  
   Trong phép so model với nhãn train, giá trị thấp nhất là `train_15 = 0.712`. Chỉ từ con số này chưa thể nói model hay nhãn đúng; notebook cũng nêu rõ OKS thấp chỉ chứng minh hai bên bất đồng. Cần đối chiếu ảnh và kết quả gold để quyết định.

5. **Ảnh tôi gán tệ nhất có cũng là ảnh model đoán tệ nhất không?**  
   Chưa thể trả lời vì hai notebook không có kết quả per-image của `eval_vs_gold.json`. Sau khi có file đó, lấy ảnh có OKS nhãn-vs-gold thấp nhất và so với `train_15`, `train_14`, `train_03` là các ảnh có OKS model-vs-nhãn thấp nhất.

### Các ca model và nhãn bất đồng nhiều

| Ảnh | OKS model vs nhãn |
| --- | ---: |
| train_15 | 0.712 |
| train_14 | 0.732 |
| train_03 | 0.748 |
| train_16 | 0.844 |
| train_12 | 0.857 |

Notebook còn ghi nhận khác số người ở `train_10` (`model 2 / bạn 1`) và `train_03` (`model 4 / bạn 2`). Đây là tín hiệu để xem lại prediction, không tự động coi nhãn là sai.

## 5. Một rule evidence đã dùng

Ở `train_01`, người thứ nhất, `left_ankle` được đặt `v=0`. Ảnh cao 427 px, trong khi export COCO ghi tọa độ y của `left_ankle` khoảng 504 px, tức là khớp đã nằm ngoài mép dưới của ảnh. Vì vậy đây là trường hợp **outside** và `v=0` phù hợp hơn `v=1`; cảnh báo “người nằm gọn giữa ảnh” của checker cần được xác minh bằng chính vị trí keypoint chứ không dùng như kết luận tự động.

## 6. Kiểm tra tính nhất quán giữa hai notebook


