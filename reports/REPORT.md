# Báo cáo Ngày 4 — Keypoint & Pose

Họ tên: Nguyễn Đăng Huân
Ngày: 16/09/2026



## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh | 20 |
| Số skeleton | 29 |
| Tổng số keypoint | 493 = 29 × 17 |
| v=2 / v=1 / v=0 | 311 / 154 / 28 |
| Trung bình khớp v>0 mỗi người | 16.03 |
| Thời gian trung bình mỗi ảnh | Chưa ghi nhận |


Các khớp có tỷ lệ v=1 cao nhất:

1. left_ear: 19/29, khoảng 66%.
2. right_ear: 16/29, khoảng 55%.
3. Đồng hạng right_wrist và right_knee: 10/29, khoảng 34%.

Tai là nhóm được gán occluded nhiều nhất, nhưng bảng đếm không cho biết nguyên nhân che khuất cụ thể ở từng ảnh. Các tình huống đã gặp gồm cổ tay bị vật đang cầm che và người phía sau bị mờ/chồng lấp. Hay bị che không đồng nghĩa với khó xác định vị trí giải phẫu nhất; cần xem ảnh để kết luận. Mờ đơn thuần không phải lý do chọn v=1.

## 2. Chấm với Gold và rework



| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| Số người Gold | 29 | 29 |
| Ghép được | 28 | 29 |
| Thiếu người | 1 | 0 |
| Thừa người | 0 | 0 |
| OKS trung bình | 0.9054 | 0.8973 |
| OKS@0.50 | 0.9655 | 1.0000 |
| OKS@0.75 | 0.9655 | 0.9655 |
| dao_trai_phai | 0 | 1 |
| nham_nguoi | 1 | 0 |
| xoa_khop_bi_che | 0 | 0 |
| lech_nhe | 22 | 26 |
| co_khac_gold | 53 | 55 |
| gold_khong_gan_nhan | 67 | 73 |

Loại lỗi không xuất hiện được ghi 0. Đây là số cảnh báo của công cụ, không chứng minh mọi điểm còn lại đều đúng.

### Thay đổi có bằng chứng

| Ảnh / đối tượng | Trước | Sau | Nhận xét |
| --- | --- | --- | --- |
| train_01, gold_person 2 ↔ your_person 1, right_wrist | Nhầm người; OKS skeleton 0.8568 | Hết cảnh báo; OKS 0.9269 | Cải thiện được xác nhận; JSON không chứa tọa độ để mô tả chính xác thao tác kéo điểm. |
| train_13, gold_person 1 | Thiếu skeleton | Ghép your_person 3, OKS 0.6225 | Đã bổ sung người; còn cảnh báo đảo trái/phải. |
| train_03, gold_person 1 | OKS 0.9203 | OKS 0.9170 | Thay đổi nhỏ, chưa có lịch sử thao tác để giải thích. |
| train_07, gold_person 1 | OKS 0.9264 | OKS 0.9071 | Cần đối chiếu nhãn nếu muốn xác định nguyên nhân. |

Thứ tự người trong export có thể thay đổi. Dùng cặp ghép Gold để đối chiếu; không mặc định your_person là ID CVAT.

OKS trung bình giảm 0.0081, trong khi OKS@0.50 tăng 0.0345. Trung bình các skeleton ghép được khớp với mean OKS trong hai JSON. Sau sửa có thêm skeleton OKS 0.6225 vào tập tính trung bình; vì vậy không thể nhìn mean giảm rồi kết luận toàn bộ nhãn kém đi.

### Lỗi đảo trái/phải còn lại

train_13.jpg, your_person 3 ↔ gold_person 1: công cụ báo đổi các cặp trái/phải sẽ làm OKS tăng rõ. Chưa có ảnh train_13 để xác nhận tư thế, độ khó hoặc nguyên nhân thao tác. Cần kiểm hướng cơ thể, vai–hông và chuỗi tay/chân rồi sửa đúng giải phẫu, export, convert và chấm lại.

“Cờ khác Gold” và “Gold không gán khớp này” không bị trừ điểm theo hướng dẫn lab. Không đổi luật lớp chỉ để bắt chước visibility của Gold.

## 3. Kiểm chéo

Bạn cùng nhóm/reviewer: .

visibility_report.json có comparison=null: chưa có kết quả so sánh với bạn cùng nhóm.

| Khớp lệch nhiều nhất | Tôi | Bạn cùng nhóm | Độ lệch | Nguyên nhân |
| --- | --- | --- | --- | --- |
| Chưa xác định | — | — | — | Chưa có bảng so sánh |

Guideline đã làm rõ: mờ không đồng nghĩa với bị che; khớp bị che trong khung dùng v=1 và vẫn đặt điểm; trái/phải theo cơ thể; hoàn thành từng người. Chưa xác nhận đây là kết quả thống nhất với reviewer.

## 4. Model

Nguồn: eval_model (1).json. Giữ nguyên thang 0–1.

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | 0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | 0.0000 |
| box_mAP50 | 0.9785 | 0.9600 | -0.0185 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

Các số liệu giống bản eval_model.json đã gửi trước rework. JSON không có mã lần chạy hoặc phiên bản dataset, nên chưa xác nhận đã train trên bộ 29 skeleton mới nhất. Nếu nhãn thay đổi sau lần train, cần chạy lại notebook trên bộ cuối.

### Năm câu hỏi cuối notebook

1. **Pose mAP50-95 thay đổi bao nhiêu?** Sự thay đổi: Chỉ số pose_mAP50_95 tăng từ 0.6853 (baseline) lên 0.6908 (sau fine-tune), tức là tăng +0.0055 (+0.55%).
Giải thích:
Mặc dù tập train chỉ có vỏn vẹn 20 ảnh, mô hình vẫn ghi nhận một sự cải thiện nhỏ về độ chính xác vị trí các điểm (qua chỉ số mAP50-95).
Dạy được gì: 20 ảnh của bạn cung cấp các tư thế hoặc bối cảnh thực tế đặc thù (góc máy, trang phục, hoặc điều kiện ánh sáng đặc thù của tập dữ liệu này) mà tập dữ liệu COCO khổng lồ chưa tối ưu hóa tốt.
Làm hỏng gì (nếu có): Do tập dữ liệu quá nhỏ (20 ảnh), mô hình có nguy cơ bị overfit nhẹ vào các chi tiết nhiễu hoặc tư thế cá biệt trong tập train của bạn, dẫn đến việc nhận diện bounding box (box_mAP50 giảm từ 0.9785 xuống 0.9600) bị kéo giảm một chút do phân phối ảnh train quá hẹp.
2. **Box và pose mAP chênh bao nhiêu?**Chênh lệch (ở mô hình sau fine-tune):
box_mAP50_95 đạt 0.8041
pose_mAP50_95 đạt 0.6908
Chênh lệch là 0.1133 (khoảng 11.3%).
Kết luận: Mô hình tìm người dễ hơn tìm khớp. Việc phát hiện vùng chứa một người (bounding box) đơn giản hơn vì người có đặc điểm hình thể chung dễ nhận biết. Trong khi đó, việc xác định chính xác vị trí của từng khớp trong số 17 khớp (đặc biệt khi bị che khuất, tự che khuất, hoặc xoay người) đòi hỏi độ chính xác ở mức pixel cực kỳ cao.
3. **Một ảnh test model đoán sai:** Dựa trên log đoán của model ở Mục 5:
Ở ảnh test_03.jpg và test_09.jpg, model nhận diện được tới 2 người.
Ở ảnh test_02.jpg, model nhận diện được 2 người trong khi thực tế có thể có người bị che khuất một phần hoặc vật thể gây nhầm lẫn.
Lỗi điển hình: "Nhầm người / Gán nhầm khớp" (ID Switch / Misassociation) hoặc "Trượt hẳn" (Miss/False Positive) khi model cố vẽ khung xương lên một vật thể không phải người hoặc người ở quá xa, hoặc gán nhầm tay của người này sang người kia khi hai người đứng sát nhau.
4. **Ảnh có OKS thấp nhất giữa nhãn và model, ai đúng?** Ảnh có OKS thấp nhất: train_15 với OKS chỉ đạt 0.511.
Ai đúng và dựa vào đâu:
Xem lại cảnh báo ở bước 1: train_15.txt bị cảnh báo: "left_hip/right_hip nằm ngược chiều so với hai mắt - dấu hiệu đảo trái/phải".
Điều này chứng tỏ mô hình đúng và nhãn của bạn gán sai (bị đảo ngược khớp Trái/Phải). OKS cực thấp (0.511) xuất hiện do bạn gán nhầm bên hông trái thành hông phải (hoặc ngược lại), trong khi mô hình đã được học quy luật giải phẫu chuẩn nên đoán đúng vị trí Trái/Phải thực tế.
5. **Ảnh gán tệ nhất có trùng ảnh model đoán tệ nhất?** Kết luận: Đúng vậy, những ảnh bạn gán tệ nhất (bị lệch hoặc đảo khớp như train_15, train_11, train_13 có cảnh báo ở bước 1) cũng chính là những ảnh model có OKS thấp nhất hoặc lệch số lượng người (train_15 OKS 0.511, train_13 lệch số người giữa model và bạn).
Điều này nói lên rằng: Các bức ảnh này là ảnh khó (Hard Cases). Chúng thường có các đặc điểm như: góc chụp nghiêng, người bị che khuất một phần (occlusion), tư thế không chuẩn, hoặc có vật thể gây nhầm lẫn. Sự bất đồng nhất quán giữa nhãn của bạn và phán đoán của model là chỉ dấu rõ ràng nhất để tìm ra các mẫu dữ liệu lỗi cần được làm sạch lại (Data Cleaning).


## 5. Một rule evidence đã dùng

Ở train_01, tay hai người cùng đỡ pizza nằm gần nhau nên dễ gán nhầm cổ tay. Với right_wrist của your_person 1 trong kết quả đánh giá, cần lần từ đúng vai qua khuỷu đến chỗ nối cẳng tay–bàn tay. Nếu tâm cổ tay bị pizza hoặc tay người khác che nhưng vẫn nằm trong ảnh, đặt điểm ước lượng và dùng v=1, không dùng v=0. Nếu tâm khớp quan sát được thì dùng v=2. Cảnh báo nhầm người đã biến mất sau rework; JSON không cho biết cờ thực tế cuối cùng nên cần đối chiếu export để xác nhận.

## 6. Các mục còn cần trước khi chốt bài

- Kiểm cảnh báo đảo trái/phải train_13/your_person 3 trước lỗi lệch nhẹ.
- Chạy kiểm định dạng và visualize trên export cuối; log cũ không xác nhận bản mới.
- Nếu sửa nhãn, cập nhật visibility, Gold và model trên cùng phiên bản.
- Điền họ tên, nhóm, thời gian gán, reviewer và kết quả kiểm chéo.
- Bổ sung ảnh minh họa guideline và ảnh model/kết quả từng ảnh cho câu 3–5.
