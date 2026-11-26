# Báo cáo Day 13 — Doãn Thị Phương Thảo, 2A202603255

## 1. Kết luận trước

- Học viên Doãn Thị Phương Thảo, mã 2A202603255. Task cá nhân `D13-2A202603255` đã có Job và mở được giao diện 3D.
- Môi trường Windows 10, Chrome, CVAT Server 2.74.1. Script dùng SDK 2.83.0 và báo lệch phiên bản khi tạo task.
- Lệnh `dry` xác nhận đủ 10 frame, mỗi frame có 7 ảnh camera. Danh sách gồm `s01f00`, `s01f57`, `s02f29`, `s02f45`, `s03f08`, `s03f51`, `s04f14`, `s04f47`, `s05f08`, `s05f24`.
- Lần chạy `sync` đầu tiên xuất **54 cuboid**. Frame `s01f00` có **9 hộp**. Chín frame còn lại cùng có **5 hộp/frame**, đúng mức tối thiểu của đề. Phiếu tự kiểm đánh dấu **3/54 hộp**.
- Em đã dùng camera để đối chiếu lớp và hướng của vật. Phần cần kiểm kỹ nhất là độ cao đáy hộp, vì cả ba cảnh báo đều liên quan đến chênh lệch với mặt đường.

Trong khung 3D, kéo chuột trái để xoay, kéo chuột phải để dời cảnh và dùng con lăn để thu phóng. Để đặt cuboid, chọn lớp, chọn **Shape** rồi nháy đúp vào cụm điểm. Dùng **Top** để căn tâm, chiều dài, chiều rộng và yaw, rồi dùng **Side** và **Front** để căn chiều cao và đáy hộp. Bật **Cuboid orientation** để kiểm hướng và dùng `preview` để xem hình chiếu trên ảnh camera.

## 2. Đọc frame

Hệ trục VF đặt gốc tại LiDAR. Phía trước xe thu dữ liệu là `−y`, phía sau là `+y`, bên trái là `+x` và phía trên là `+z`. Theo tài liệu VF, cao độ mặt đường tham chiếu khoảng −1,7 m.

Ảnh minh chứng cần lưu gồm `t1-s01f00-3d.png`, `t1-s01f00-top.png`, `t1-s01f57-3d.png` và `t1-s01f57-top.png`. Hai ảnh nhìn từ trên cần đánh dấu phía trước, `+x` và `+y`.

| Frame | Hướng trên ảnh nhìn từ trên | Mặt đường | Vật xa nhất nhận ra (~m) | Loại vật thấy | Vật sau và hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f00 | Trước xe ở dưới ảnh, +x sang phải, +y lên trên | Thấy các vòng quét mặt đường quanh tâm. | bus - 33.49m | Xe con, xe buýt, người đi bộ theo Bảng A | Xe con trắng xuất hiện ở góc nhìn bên của cam05 và cam06. | Dải ít điểm ngay sau xe gần tâm, nghi bóng che |
| s01f57 | Trước xe ở dưới ảnh, +x sang phải, +y lên trên | Thấy các dải điểm chạy dọc phần đường. [Xác nhận độ dốc tại chỗ] | car - 12.80 | Nhiều xe con trước xe thu dữ liệu trong cam01 và cam03 | Cam05 thấy một xe con ở cạnh xe thu dữ liệu. | Hai vệt điểm tách rời gần tâm, chưa rõ nguồn |

Camera cho thấy màu trắng của xe con, vạch sơn phân làn và chi tiết đầu hoặc đuôi xe. LiDAR cho thấy vị trí tương đối của các cụm điểm quanh gốc quét, sự chênh cao giữa vật và mặt đường khi nhìn ngang, và mật độ điểm khác nhau giữa các cụm. 

| Frame xem lướt | Phía trước trên ảnh nhìn từ trên | Điều lạ nếu có |
|---|---|---|
| s02f29 | Cạnh dưới ảnh (`−Y`) | — |
| s02f45 | Cạnh dưới ảnh (`−Y`) | — |
| s03f08 | Cạnh dưới ảnh (`−Y`) | — |
| s03f51 | Cạnh dưới ảnh (`−Y`) | — |
| s04f14 | Cạnh dưới ảnh (`−Y`) | — |
| s04f47 | Cạnh dưới ảnh (`−Y`) | Tự kiểm báo đáy hộp `Cyclist #3` cao hơn mặt đường ước tính **0,56 m**, nghi hộp lơ lửng. |
| s05f08 | Cạnh dưới ảnh (`−Y`) | — |
| s05f24 | Cạnh dưới ảnh (`−Y`) | — |

## 3. Ba cuboid mẫu

| Vật | Ảnh | Frame, idx | Class | Yaw đúng? | Đáy chạm đường? | Đối chiếu ba góc và ảnh | So với bảng 4.3 |
|---|---|---|---|---|---|---|---|
| Xe con gần nhất | `t2-a-car.png` | `s01f00 #1` | `Car` | Vạch đỏ dọc thân xe. Cần xác nhận nó chỉ về đầu xe. | Đáy trông sát mặt đường trong ô Front. | Ảnh mẫu có đủ Top, Side, Front và ảnh camera. | **4,608 × 1,869 × 1,282 m**. Dài và rộng trong khoảng sedan. Cao thấp hơn cận dưới **0,118 m**. |
| Xe buýt | `t2-b-bus-truck.png` | `s01f00 #4` | `Bus` | Vạch đỏ dọc thân xe. Cần xác nhận đầu xe trên camera. | Tự kiểm báo đáy thấp hơn mặt đường ước tính **0,41 m**. | Đã xem Top, Side, Front. Cần xác nhận camera thấy đúng xe. | **13,302 × 3,024 × 3,568 m**. Cả ba chiều vượt mức tham chiếu của xe buýt thường. |
| Người đi bộ hoặc xe hai bánh | `t2-c-ped-cyclist.png` | `s01f00`, xem các lựa chọn dưới đây | Theo hộp đã chụp | Kiểm hướng đi hoặc hướng mặt với `Pedestrian`, hướng đầu xe với `Cyclist`. | Điền theo hộp đã chụp. | Điền theo hộp đã chụp. | Chọn đúng dòng kích thước dưới đây. |

## 4. Bốn hiện tượng chất lượng

| Hiện tượng | Frame, ảnh, nguồn | Nguyên nhân giả thuyết | Dấu hiệu cần xác nhận trên point cloud | Ảnh hưởng khi gán |
|---|---|---|---|---|
| Bóng che | s01f00, task cá nhân, t4-1-bong-che.png | Xe gần có thể chắn tia LiDAR | Các vòng điểm bị ngắt thành một dải tối phía sau hộp đỏ gần tâm, theo hướng từ LiDAR qua xe | Đối chiếu camera trước khi suy ra phần bị khuất |
| Thưa ở xa | s01f00, task cá nhân, t4-2-thua-o-xa.png | Khoảng cách giữa các tia tăng theo cự ly | Hộp đỏ ngoài cùng trong cặp phía trên chứa cụm điểm trông rời hơn cụm sát gốc LiDAR | Kiểm tra biên và hướng trước khi căn hộp |
| Ít điểm bất thường | s01f00, task cá nhân, t4-3-phan-xa-thap.png | Có thể do phản xạ yếu, góc quét hoặc che khuất | Phần trên cụm điểm trong hộp đỏ giữa ảnh thưa hơn phần dưới | Xem thêm góc ngang và camera trước khi suy ra kích thước |
| Điểm lạ | s01f57, task cá nhân, t4-4-diem-la.png | Chưa rõ nguồn | Hai vệt điểm ngắn gần gốc LiDAR, tách khỏi các cụm xe trong góc nhìn đang xét | Đối chiếu camera và góc ngang trước khi đưa các điểm này vào hộp |

## 5. Số liệu và tự kiểm

Lấy số đo từ Bảng A và số điểm từ cột `points_in` trong phiếu tự kiểm. So độ cao đáy hộp với mặt đường tại vị trí vật.

| Frame, idx, lớp | z (m) | h (m) | z đáy (m) | dist_m (m) | r (m) | yaw (°) | Nhận xét đáy |
|---|---:|---:|---:|---:|---:|---:|---|
| `s01f00 #4 Bus` | −0,157 | 3,568 | −1,942 | 33,49 | 33,49 | 357,7 | Lần đầu báo đáy thấp hơn mặt đường ước tính 0,41 m. Lần tự kiểm mới không còn cờ ở hộp này. |
| `s01f00 #8 Pedestrian` | −1,329 | 1,971 | −2,315 | 22,31 | 22,35 | 89,3 | Đáy thấp hơn mặt đường ước tính 0,37 m, nghi hộp bị chìm. |
| `s04f47 #3 Cyclist` | −0,575 | 1,299 | −1,225 | 16,05 | 16,06 | 270,9 | Đáy cao hơn mặt đường ước tính 0,56 m, nghi hộp bị lơ lửng. |

Gần nhất: s01f00 #1 Car, khoảng cách ngang 3,37 m, khoảng cách 3D 3,50 m.
Xa nhất: s02f45 #5 Car, khoảng cách ngang 40,20 m, khoảng cách 3D 40,21 m.
Kích thước theo bảng 4.3: s01f00 #1 Car có kích thước 4,608 × 1,869 × 1,282 m. So với nhóm sedan/hatchback, dài và rộng nằm trong khoảng tham chiếu, cao thấp hơn cận dưới 0,118 m. s01f00 #8 Pedestrian có kích thước 0,441 × 0,739 × 1,971 m. So với nhóm người lớn, dài và rộng nằm trong khoảng, cao hơn cận trên 0,021 m.
Hướng xe: s01f00 #1 Car có yaw 268,3°, lệch 1,7° so với hướng tham chiếu 270°, phù hợp với hướng cùng chiều nếu camera xác nhận vạch đỏ chỉ về đầu xe. Chưa xác định được mẫu ô tô ngược chiều từ các dòng đã gửi.
Tự kiểm: Sau lần sync mới, tổng vẫn là 54 cuboid và số hộp có cảnh báo giảm từ 3 xuống 2. Kết quả này xác nhận hộp Bus #4 hết cờ. Phần ghi hai hộp đã sửa cần điền theo thao tác bạn thực sự làm trong CVAT.

| Frame, idx, lớp | `dist_m` (m) | `points_in` |
|---|---:|---:|
| `s01f00 #1 Car` | 3,4 | 2.949 |
| `s01f00 #6 Car` | 12,6 | 702 |
| `s01f00 #7 Car` | 23,4 | 398 |
| `s02f45 #5 Car` | 40,2 | 40 |

Trong bốn cuboid xe con được đối chiếu, số điểm LiDAR trong hộp giảm khi khoảng cách tăng. Xe ở 40,2 m cách LiDAR khoảng 12 lần so với xe ở 3,4 m và chỉ có 40 điểm, tương đương khoảng 1,4% số điểm của xe gần. Kết quả cùng xu hướng với đường cong trong slide. Cụ thể, xe ở 12,6 m có 702 điểm, gần mức trung vị 718 điểm của nhóm 10–20 m. Xe ở 23,4 m có 398 điểm, cao hơn mức trung vị 214 điểm của nhóm 20–30 m.

Sau lần chạy sync mới, tổng số cuboid vẫn là 54 và số hộp có cảnh báo giảm từ 3 xuống 2. Hộp s01f00 #4 Bus đã hết cảnh báo. Hai hộp còn cờ là s01f00 #8 Pedestrian có đáy thấp hơn mặt đường ước tính 0,37 m và s04f47 #3 Cyclist có đáy cao hơn mặt đường ước tính 0,56 m.

Hộp đã sửa: s01f00 #4 Bus — em chỉnh lại đáy cuboid trong CVAT. Sau khi chạy lại sync, hộp này không còn bị cảnh báo.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Nhãn 3D đo bằng mét để mô hình học vị trí và kích thước thật của vật trong không gian LiDAR. Vật thưa điểm cần quy tắc riêng vì khi chỉ thấy một phần nhỏ, biên, kích thước và hướng hộp rất dễ bị suy đoán sai.

- **Công cụ** · ChatGPT, Claude 
- **Câu hỏi** · Cách chuẩn bị dữ liệu, tạo task CVAT, đọc frame và viết báo cáo theo mẫu.
- **Cách sử dụng** · Dùng hướng dẫn để thực hiện từng bước và soạn bản nháp từ kết quả PowerShell và quan sát ban đầu. Điền số đo từ CSV sau khi kiểm hộp trong CVAT.

Điều cần xác nhận thêm là vị trí của các hướng trên ảnh nhìn từ trên xuống và cách ghép các cụm điểm thưa với vật trong ảnh camera.
