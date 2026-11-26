# Báo cáo Day 13 — Võ Khắc Xuân Nguyên, 2A202603122

## 1. Kết luận trước

Task `D13-2A202603122` có **90 cuboid trên 10 frame**. Trong đó **89 hộp** có tâm cách LiDAR tối đa 50 m. Phiếu tự kiểm ghi **0/90 hộp có cờ**. Frame ít hộp nhất là `s01f31` với 3 hộp. Hai frame đầu lần lượt có 6 và 3 hộp trong phạm vi 50 m, so với yêu cầu 8 và 5 hộp.
**Môi trường** Windows. Chrome, CVAT 2.81.0.
Trong CVAT, khung Perspective cho phép xoay, dời và nhìn từ trên xuống. Đặt cuboid bằng Shape, chỉnh tâm, chiều dài, chiều rộng và yaw ở Top, rồi kiểm chiều cao và đáy ở Side và Front. Bật Cuboid orientation để thấy mũi tên và dùng `preview` để xem hộp trên ảnh camera.

## 2. Đọc frame

| Frame | Hướng trên ảnh Top | Mặt đường | Khoảng cách xa nhất của hộp đã vẽ | Loại vật đã gán | Vật phía sau hoặc hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| `s01f09` |  −y | Thấp hơn LIDAR | xe bus ở phía trước bên phải | Car, Truck, Bus | Car ở sau trái |  |
| `s01f31` | −y | Thấp hơn LIDAR | Car ở phía sau bên trái | Car | Car ở phía sau bên phải |  |


| Frame xem lướt | Hướng trước | Dấu mốc để tìm lại trên CVAT |
|---|---|---|
| `s02f01` | −y | Car idx 6 là hộp xa nhất đã vẽ, ở 44,97 m. |
| `s02f25` | −y | Car idx 7 là hộp xa nhất đã vẽ, ở 34,90 m. |
| `s03f19` | −y | Bus idx 7 là hộp xa nhất đã vẽ, ở 42,11 m. |
| `s03f56` | −y | Car idx 8 là hộp xa nhất đã vẽ, ở 40,41 m. |
| `s04f03` | −y | Pedestrian idx 13 là hộp xa nhất đã vẽ, ở 31,91 m. |
| `s04f18` | −y | Car idx 10 là hộp xa nhất đã vẽ, ở 31,61 m. |
| `s05f00` | −y | Car idx 5 là hộp xa nhất đã vẽ, ở 16,74 m. |
| `s05f36` | −y | Car idx 5 là hộp xa nhất đã vẽ, ở 40,85 m. |

## 3. Ba cuboid có hướng dẫn

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| xe con gần(`t2-a-car.png`) | `s01f09`, 2 | Car | có | có | đã kiểm | 4,626 × 1,863 × 1,424 |
| xe buýt hoặc xe tải(`t2-b-bus-truck.png`) | `s02f01`, 11 | Truck | có | có | đã kiểm |12,121 × 2,700 × 3,219 | |
| người đi bộ hoặc xe hai bánh(`t2-c-ped-cyclist.png`) | `s02f25`, 23 | Cyclist | có | có | đã kiểm |2,050 × 1,200 × 1,356 | |


## 4. Bốn hiện tượng chất lượng

Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | 4 | dừng đèn đỏ xe trước che xe sau | các điểm point cloud xe ở sau bị mất theo phần bị che | gán theo ước lượng kích thước của xe |
| 2. Thưa ở xa | 9 | xe ở trước quá xa | chỉ thấy một số ít điểm ở đuôi xe | không gán |
| 3. Phản xạ thấp | 2A202603214 | Bề mặt vật thể sơn màu tối/đen bóng hoặc kính chắn gió hấp thụ/phản xạ lệch tia hồng ngoại LiDAR | Vùng kính xe bị rỗng điểm, thân xe chỉ hiện các đường viền điểm mảnh, số điểm thấp bất thường so với khoảng cách | dựa vào khung viền điểm còn lại và bảng kích thước để bao trọn toàn bộ kích thước thật của xe |
| 4. Điểm lạ | 2A202603214 | phản xạ từ mặt đường, gầm xe hoặc điểm thuộc vật cản sát bên cạn | Xuất hiện các điểm lơ lửng bất thường dưới gầm/trên nóc xe hoặc cụm điểm nhiễu dính sát ngay ngoài mép hộp | Phải kiểm tra cả 3 góc nhìn Top/Side/Front để loại bỏ điểm nhiễu ra khỏi hộp, giữ đáy hộp chạm mặt đường chuẩn |

## 5. Số từ cuboid của tôi

Lấy từ `submission/D13-<MÃ SỐ>-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f09, 1, Car | −1,052 m | 1,424 m | −1,764 m | 5,11 m | 5,21 m | 268,0° | Gần chạm đường. Đáy cao hơn mặt đường ước lượng 0,03 m. |
| s02f01, 1, Truck | −0,209 m | 3,219 m | −1,818 m | 6,22 m | 6,22 m | 270,1° | Gần chạm đường. Đáy cao hơn mặt đường ước lượng 0,03 m. |
| s02f25, 5, Cyclist | −0,921 m | 1,356 m | −1,599 m | 24,67 m | 24,69 m | 90,1° | Đáy cao hơn mặt đường ước lượng 0,14 m |

(a) cuboid **gần nhất** và **xa nhất**: `dist_m` và r của từng cái. Gần nhất là Car ở s04f03, idx 1, với dist_m = 4,47 m và r = 4,55 m. Xa nhất là Car ở s02f01, idx 6, với dist_m = 44,97 m và r = 44,99 m.
(b) Kích thước l, w, h của một người đi bộ (hoặc xe hai bánh) và một xe con so với bảng 4.3: chiều nào ngoài khoảng, vì sao. 
    Cyclist ở s02f25, idx 5 đo 2,050 × 1,200 × 1,356 m. Nếu đây là xe máy có người lái, chiều dài nằm trong khoảng chuẩn, chiều rộng vượt 0,25 m so với cận trên 0,95 m và chiều cao thấp hơn 0,044 m so với cận dưới 1,40 m.
(c) `yaw_deg` của một xe cùng chiều phía trước và một xe ngược chiều (nếu có): có gần 270° và 90° không.
    Car ở s01f31, idx 1 nằm phía trước xe thu dữ liệu và có yaw_deg = 269,9°, lệch 0,1° so với hướng cùng chiều 270°. Car ở s04f18, idx 12 có yaw_deg = 90,1°, lệch 0,1° so với hướng ngược chiều 90°. Cả hai hộp đều gần hướng tham chiếu tương ứng.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s01f09, 1, Car | 5,11 m | 2567 |
| s04f18, 1, Car | 12,86 m | 1105 |
| s04f03, 4, Car | 24,82 m | 222 |
| s02f25, 7, Car | 34,90 m | 71 |

Trong bốn mẫu Car, xe ở 34,90 m xa gấp 6,83 lần xe ở 5,11 m, còn số điểm giảm từ 2.567 xuống 71, tức còn khoảng 2,8%. Xu hướng này phù hợp với đường cong trên slide. Hai mẫu gần có 2.567 và 1.105 điểm, cao hơn mức tham chiếu 1.968 và 718 điểm. Hai mẫu xa có 222 và 71 điểm, gần với mức tham chiếu 214 và 72 điểm.
Không có cảnh báo nào được giữ nguyên.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Đơn vị mét giúp mô hình học vị trí và kích thước vật trong cùng một hệ tọa độ dù kích thước trên ảnh thay đổi theo góc nhìn. Khi vật chỉ có vài điểm, quy tắc về mức quan sát giúp giảm nhãn suy đoán sai trong dữ liệu huấn luyện.
