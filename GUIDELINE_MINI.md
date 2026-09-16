# GUIDELINE MINI — Gán nhãn person COCO-17

Người gán: Nguyễn Đăng Huân
Ngày: 16/09/2026

> Tổng hợp sau rework từ GUIDE/README và các ca đã trao đổi. Các luật thực hành cần reviewer xác nhận, chưa giả định đã có kiểm chéo.

## 1. Phạm vi và luật bắt buộc

Gán mọi người trong 20 ảnh core train, một lớp person. Không sửa test, không dùng test hoặc Gold để train.

- Giữ đủ 17 keypoint mỗi skeleton; không xóa điểm bị che.
- Trái/phải theo cơ thể người, không theo màn hình.
- Khớp nhìn thấy và xác định được: v=2.
- Khớp bị che nhưng còn trong ảnh: đặt tọa độ ước lượng, chọn Occluded, v=1.
- Khớp ngoài mép ảnh: chọn Outside, v=0; không đặt điểm nhìn thấy giả ở mép.
- Không dùng Hidden (h) thay visibility.
- Hoàn thành một người trước khi chuyển người khác. Đổi cờ đúng keypoint, tránh đổi cả skeleton.

Thứ tự:

```text
0 nose
1 left_eye       2 right_eye
3 left_ear       4 right_ear
5 left_shoulder  6 right_shoulder
7 left_elbow     8 right_elbow
9 left_wrist    10 right_wrist
11 left_hip     12 right_hip
13 left_knee    14 right_knee
15 left_ankle   16 right_ankle
```

## 2. Luật tình huống

| Tình huống | Cách quyết | Căn cứ / minh họa cần giữ |
| --- | --- | --- |
| Hông bị áo/quần dài che | Nếu tâm khớp không quan sát được, suy từ thân–đùi, đặt điểm và v=1 theo quy ước lab. Không đặt theo túi quần/gấu áo. | Cần ảnh CVAT mẫu. Nhãn hiện có nhiều hông v=2 nên phải kiểm tính nhất quán. |
| Tai bị tóc/mũ che | Đủ phần nhìn thấy để định vị: v=2. Phải suy vì che khuất: đặt ước lượng, v=1. | Cần ảnh tai thấy được và bị che. |
| Người bị crop | Xét từng khớp: ngoài ảnh mới v=0; bị che trong ảnh là v=1. | Cần ảnh mép khung với khớp bị cắt. |
| Cổ tay sau chai/tay lái/thân | Theo cẳng tay đến tâm cổ tay. Tâm bị che: v=1; thấy được: v=2. Cầm vật không tự động là occluded. | Ảnh người đàn ông cầm chai đã trao đổi, chưa xác định tên train. |
| Mờ/out-focus | Vẫn quan sát và định vị được: v=2. Không tự chuyển mờ thành v=1. Nếu không định vị được, ghi ca cần reviewer quyết. | Ảnh người phía sau người mặc vest đã trao đổi. |
| Hai người chồng nhau/cùng giữ vật | Lần vai → khuỷu → cổ tay đúng cơ thể; khớp bị người khác che trong ảnh: đặt ước lượng, v=1. | train_01, hai người giữ pizza. |
| Người nhỏ | Không tự đặt ngưỡng bỏ người trong core; kiểm đủ người trước khi chỉnh điểm chi tiết. | train_13 từng thiếu một người, nay đủ ba match Gold. |

Cần chèn screenshot CVAT tương ứng từng luật khi nộp. Ảnh đã trao đổi chưa đủ bộ minh họa và một số chưa xác định tên train; không tự gán tên ảnh hoặc ID.

## 3. Các ca mơ hồ đã gặp

### Ca 1 — Người đàn ông cầm chai, khớp cổ tay

- Nhận diện: ảnh người mặc áo khoác, xe đạp và chai. Tên train, ID và bên cổ tay cần bổ sung từ export.
- Mơ hồ: vật che tâm cổ tay hay chỉ che bàn tay?
- Quyết định: tâm thấy được dùng v=2; bị che và phải suy thì đặt điểm, dùng v=1. Không dùng v=0 khi còn trong ảnh.
- Nếu quyết ngược: visibility không nhất quán; v=0 có thể loại điểm khỏi giám sát tọa độ tùy pipeline.

### Ca 2 — Người phía sau bị out-focus

- Nhận diện: người phía sau người mặc vest; tên train và ID cần bổ sung.
- Mơ hồ: khó nhìn vì mờ hay vì bị che?
- Quyết định: xét từng khớp. Nhìn thấy dù mờ là v=2; bị người phía trước che và phải suy là v=1.
- Nếu quyết ngược: bảng visibility phản ánh độ nét thay vì tình trạng che khuất.

### Ca 3 — train_01, right_wrist, your_person 1 theo JSON

- Mơ hồ: hai người đỡ pizza, cổ tay gần nhau; công cụ báo gần cổ tay người khác.
- Quyết định: lần đúng vai–khuỷu–cổ tay, không xác định chủ thể chỉ theo khoảng cách gần nhất.
- Kết quả: sau rework hết cảnh báo nhầm người; OKS match gold_person 2 tăng 0.8568 → 0.9269.
- Không suy your_person 1 chắc chắn là người phụ nữ hoặc ID 1 CVAT; phải đối chiếu export.
- Nếu quyết ngược: khớp thuộc người bên cạnh dù tổng thể skeleton vẫn trông hợp lý.

### Ca 4 — train_13, skeleton bổ sung

- Trước: gold_person 1 chưa có match.
- Sau: ghép your_person 3, OKS 0.6225; còn cảnh báo đảo trái/phải.
- Quyết định: kiểm hướng cơ thể và các cặp left/right theo giải phẫu, không theo màn hình.
- Trạng thái: chờ kiểm/sửa, chưa tuyên bố xử lý xong.
- Nếu giữ nhãn đảo: model nhận mục tiêu trái/phải sai.

## 4. Visibility và kiểm chéo

Bộ hiện tại: 20 ảnh, 29 skeleton; v2/v1/v0 = 311/154/28.

- left_ear: 19/29 occluded (~66%); right_ear: 16/29 (~55%).
- right_wrist và right_knee đồng hạng 10/29 (~34%).
- 28 điểm v=0 ở cổ tay phải, gối và cổ chân; phải kiểm từng ảnh, không tự đổi hết sang v=1.

## 5. Kiểm cuối và phiên bản

1. Kiểm đủ người, trái/phải và điểm sang nhầm cơ thể trước lệch nhẹ.
2. Export COCO → convert YOLO Pose → check → visualize → visibility.
3. Chấm Gold sau rework, giữ riêng kết quả trước sửa.
4. Không sửa cờ theo Gold máy móc: Gold v=0 bị loại khỏi OKS theo README; luật lớp vẫn yêu cầu v=1 cho khớp bị che trong khung.
5. Nếu nhãn đổi sau train, chạy model trên bộ cuối và giữ kết quả cùng phiên bản.
