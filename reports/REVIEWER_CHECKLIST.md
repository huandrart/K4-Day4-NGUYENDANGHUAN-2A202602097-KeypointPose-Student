# Reviewer checklist — Ngày 4: Keypoint & Pose

Người gán: Nguyễn Đăng Huân 
Người kiểm:  
Ngày: 16/09/2026


## 1. Checklist

| # | Mục kiểm | Trạng thái | Bằng chứng / việc cần làm |
| ---: | --- | --- | --- |
| 1 | Đủ người, mỗi skeleton đủ 17 điểm | Đạt về số lượng; chờ kiểm vị trí | 29/29 người ghép Gold, 493 trạng thái = 29 × 17. |
| 2 | Trái/phải và đường nối vai/hông đúng | Cần kiểm/sửa | train_13/your_person 3 có cảnh báo đảo trái/phải, OKS 0.6225. Không sửa chỉ vì đường nối cắt chéo nếu tư thế thật vặn người. |
| 3 | Không nối sang người khác | Hết cảnh báo tự động; chờ kiểm mắt | train_01/right_wrist hết cảnh báo nhầm người. |
| 4 | Khớp bị che dùng v=1 và có điểm | Chờ kiểm export/ảnh | Có 154 điểm v=1; tổng số không chứng minh từng tọa độ đúng. |
| 5 | v=0 chỉ ở khớp ngoài ảnh | Chờ kiểm | 28 điểm: right_wrist 1, left/right_knee 5/4, left/right_ankle 9/9. |
| 6 | Không dùng Hidden thay visibility | Chờ kiểm | Cần xem cờ CVAT và vị trí sau export. |
| 7 | COCO Keypoints 1.0 có 51 số/người | Chờ kiểm export cuối | Chưa có bản export cuối của người gán để xác nhận. |
| 8 | YOLO Pose 56 số/dòng; kpt_shape [17,3] | Chờ log cuối | Log đạt trước đây không thay cho kiểm sau export mới. |
| 9 | Visibility report và so hai người | Có báo cáo; thiếu so sánh | Markdown/JSON thống nhất: 20 ảnh, 29 người, v2/v1/v0=311/154/28; comparison=null. |
| 10 | Ca mơ hồ có guideline và ảnh mẫu | Có nội dung; thiếu ảnh đầy đủ | Bổ sung screenshot CVAT cho từng luật. |
| 11 | check_pose_labels.py chạy 0 lỗi | Chờ xác nhận lần cuối | Chạy lại trên nhãn cuối. |

## 2. Vấn đề cần theo dõi

Số người lấy từ JSON sau rework, không mặc định là ID CVAT.

| Ảnh | Người | Khớp | Vấn đề | Xử lý |
| --- | --- | --- | --- | --- |
| train_13 | your_person 3 ↔ gold_person 1 | Các cặp trái/phải | Cảnh báo đảo trái/phải, OKS 0.6225 | Kiểm hướng cơ thể, vai–hông–tay–chân; sửa cặp sai và chấm lại. |
| train_13 | your_person 3 | left_hip, right_hip, left_knee, left_ankle | Lệch nhẹ 28/23/20/31 px | Kiểm sau khi xử lý trái/phải. |
| train_15 | your_person 2 ↔ gold_person 1 | left_elbow, left_wrist | Lệch nhẹ 48/62 px | Lần đúng cánh tay, xác định tâm khớp trên ảnh. |
| train_01 | your_person 1 ↔ gold_person 2 | right_wrist | Cảnh báo cũ đã hết, OKS 0.8568 → 0.9269 | Xác nhận thuộc đúng cơ thể; không đổi chỉ để giống Gold. |
| Nhiều ảnh | Theo match trong JSON | nose | Nhiều cảnh báo lệch nhẹ | Kiểm từng ảnh, không kéo đồng loạt theo một hướng. |
| train_04/10/11 | Theo log cũ | Các điểm v=0 | Từng có cảnh báo; chưa có log mới để đóng | Kiểm ngoài ảnh hay bị che và chạy lại check. |

## 3. Lệnh kiểm bằng py trên Windows

Chạy ở thư mục gốc bài của người được kiểm:

```bat
py tools\check_pose_labels.py --images dataset\images\train --labels dataset\labels\train
py tools\visualize_pose.py --images dataset\images\train --labels dataset\labels\train --out outputs\vis_review
py tools\visibility_report.py --labels dataset\labels\train --out outputs\visibility_report.json --markdown reports\visibility_report.md
```

## 4. Kết luận

- Cảnh báo lặp lại nhiều nhất là lệch nhẹ (26); ưu tiên đảo trái/phải ở train_13 trước.
- Thiếu người đã giảm 1 → 0; nhầm người giảm 1 → 0; đảo trái/phải tăng 0 → 1.
- Chưa đủ ảnh và lịch sử thao tác để phân biệt nguyên nhân thao tác với guideline chưa rõ.
- File model có số liệu giống bản trước; cần xác nhận lần train dùng nhãn cuối.
- Chưa chốt đạt toàn bộ: cần kiểm train_13, log định dạng cuối, visualization và kiểm chéo.
- Ghi lỗi thực sự tìm được trong bài bạn cùng nhóm vào reports/review_partner.md; không dùng tự đánh giá thay kiểm chéo.

Quyết định reviewer: [Đạt]
