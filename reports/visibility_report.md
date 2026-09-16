# Visibility report

- Thư mục nhãn: `Day4-KeypointPose-Student/dataset/labels/train`
- 20 ảnh, 29 skeleton, trung bình 15.97 khớp có v > 0 mỗi người
- Tổng: v=2 334 | v=1 129 | v=0 30

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 22 | 7 | 0 | 24% |
| 1 | left_eye | 19 | 10 | 0 | 34% |
| 2 | right_eye | 20 | 9 | 0 | 31% |
| 3 | left_ear | 12 | 17 | 0 | 59% |
| 4 | right_ear | 15 | 14 | 0 | 48% |
| 5 | left_shoulder | 27 | 2 | 0 | 7% |
| 6 | right_shoulder | 28 | 1 | 0 | 3% |
| 7 | left_elbow | 22 | 7 | 0 | 24% |
| 8 | right_elbow | 23 | 6 | 0 | 21% |
| 9 | left_wrist | 17 | 12 | 0 | 41% |
| 10 | right_wrist | 20 | 8 | 1 | 28% |
| 11 | left_hip | 24 | 5 | 0 | 17% |
| 12 | right_hip | 23 | 5 | 1 | 17% |
| 13 | left_knee | 15 | 8 | 6 | 28% |
| 14 | right_knee | 17 | 6 | 6 | 21% |
| 15 | left_ankle | 15 | 6 | 8 | 21% |
| 16 | right_ankle | 15 | 6 | 8 | 21% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
