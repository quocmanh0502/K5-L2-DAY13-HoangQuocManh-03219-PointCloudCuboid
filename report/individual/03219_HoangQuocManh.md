# Báo cáo Day 13 — Hoàng Quốc Mạnh, 03219

## 1. Kết luận trước
Mã số 03219, task `D13-03219`, 10 frame: `s01f00`, `s01f51`, `s02f10`, `s02f39`, `s03f23`, `s03f56`, `s04f17`, `s04f43`, `s05f25`, `s05f46`. Windows 11, Chrome, CVAT 2.82.0 (bản cài từ Ngày 12). Bạn ngồi cạnh: TODO(học viên) MSSV.

Tôi vẽ **119 cuboid** (92 Car, 13 Cyclist, 6 Bus, 6 Pedestrian, 2 Truck). Frame ít nhất là `s05f46` với 6 cuboid: cảnh thoáng, ít xe trong 50 m. `selfcheck` cờ **37/119** cuboid; phần lớn là chiều rộng xe con 2,3–2,8 m (hộp bao dư) và 10 hộp có đáy lệch mặt đường quá 0,27 m. Tôi tin nhất vào **vị trí hộp nhìn từ trên xuống** (preview khớp xe trên cả 7 camera). Phần yếu nhất là **hướng**: ban đầu 111 hộp chưa xoay (yaw ≈ 0°/180°, length < width); tôi đã sửa bằng script (xem §6b), nhưng hướng mặc định 270° chưa được kiểm từng xe trên camera, nên xe ngược chiều hoặc đỗ quay đầu có thể đang ngược.

Cách dùng CVAT: kéo chuột trái để xoay, chuột phải để dời, con lăn để phóng; xoay khung 3D tới khi nhìn thẳng xuống để có ảnh nhìn từ trên xuống. Vẽ cuboid bằng nút cuboid → label → **Shape**, nháy đúp lên cụm điểm, rồi chỉnh ở Top (tâm, dài, rộng, yaw) và Side/Front (đáy, chiều cao). Bật **Appearance → Cuboid orientation** để thấy vạch đỏ `+X`; xem cuboid trên ảnh camera bằng `python scripts/lab.py preview` (ảnh ở `work/xem/`).

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f00-3d`, `t1-s01f00-top`, `t1-s01f51-3d`, `t1-s01f51-top`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f00 | TODO(học viên) | TODO(học viên) | TODO(học viên) | Car, Bus, Pedestrian, Cyclist | Hàng xe con phía sau cách 13–22 m (cam09); xe con sát bên trái ~3,4 m; xe buýt bên phải ~34 m | TODO(học viên) |
| s01f51 | TODO(học viên) | TODO(học viên) | TODO(học viên) | Car, Pedestrian | Xe con sau-trái ~6,5 m và ~13 m; xe con phía sau ~6,8 m | TODO(học viên) |

TODO(học viên): 3 điều **chỉ camera** cho biết và 3 điều **chỉ LiDAR** cho biết, lấy từ chính hai frame trên.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f10 | TODO(học viên) | |
| s02f39 | TODO(học viên) | |
| s03f23 | TODO(học viên) | |
| s03f56 | TODO(học viên) | |
| s04f17 | TODO(học viên) | |
| s04f43 | TODO(học viên) | |
| s05f25 | TODO(học viên) | |
| s05f46 | TODO(học viên) | |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f00, 1 | Car | yaw 270,0°, dọc thân xe, chỉ về phía trước | Có: z đáy −1,678 m, khoảng hở 0,16 m | Có, preview cam05/cam06 | l 4,59 m, h 1,45 m trong khoảng sedan; w 1,94 m nhỉnh hơn 1,90 m |
| (b) xe buýt | s01f00, 7 | Bus | yaw 357,1°: chưa kiểm chắc đầu xe trên cam07 | Có: z đáy −1,849 m, khoảng hở −0,04 m | Có, preview cam07/cam08 | **Không**: l 15,69 m (xe buýt thường 10,0–12,5 m), w 3,34 m (2,50–2,60 m): hộp bao dư |
| (c) người đi bộ | s01f00, 9 | Pedestrian | yaw 171,3°, theo hướng đi đoán từ camera | Có: z đáy −1,889 m, khoảng hở 0,05 m | Có, preview cam01 | Có: 0,85 × 0,58 × 1,91 m trong khoảng người lớn |

TODO(học viên): vòng hiệu chuẩn: cuboid của bạn lệch so với các bạn ở điểm nào, cả lớp chốt cách hiểu guideline nào?

Vật bỏ qua hoặc chưa chắc: `s01f51` idx 10 (bên phải ~10 m) gán Pedestrian nhưng rộng 1,94 m, có thể là người đang lái xe hai bánh (UNCERTAIN_CLASS). Các xe trên 50 m (8 hộp, ví dụ `s04f43` idx 10 ở 75,2 m với 21 điểm) vẽ thêm nhưng ngoài phạm vi chấm (UNCERTAIN_SCOPE / LOW_VISIBILITY).

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | TODO(học viên) / `t4-1-bong-che` | Giả thuyết: TODO(học viên) | TODO(học viên) | Bật occluded, cuboid bao phần suy luận được |
| 2. Thưa ở xa | TODO(học viên) / `t4-2-thua-o-xa` | Giả thuyết: TODO(học viên) | TODO(học viên) | Vẽ nếu thấy ~15–20% thể tích; không thì bỏ qua, ghi LOW_VISIBILITY |
| 3. Phản xạ thấp / ít điểm bất thường | TODO(học viên) / `t4-3-phan-xa-thap` | Giả thuyết: TODO(học viên) | TODO(học viên) | Ghi giả thuyết, không khẳng định; không nới hay thu hộp theo intensity |
| 4. Điểm lạ | TODO(học viên) / `t4-4-diem-la` | Giả thuyết: TODO(học viên) | TODO(học viên) | Không gán thành vật |

## 5. Số từ cuboid của tôi (Bước 5)
Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f00, 1, Car | −0,954 m | 1,449 m | −1,678 m | 3,39 m | 3,52 m | 270,0° | Chạm đường |
| s01f00, 4, Car | −0,349 m | 1,612 m | −1,155 m | 17,40 m | 17,40 m | 270,0° | **Lơ lửng**: cao hơn −1,7 m khoảng 0,55 m (phiếu tự kiểm: hở 0,53 m). Chưa sửa |
| s05f46, 2, Car | −1,045 m | 1,709 m | −1,899 m | 7,50 m | 7,57 m | 275,1° | **Hơi chìm**: thấp hơn mặt đường quanh xe 0,34 m. Đã sửa từ −0,71 m nhưng chưa đủ |

(a) Cuboid **gần nhất**: `s01f00` idx 1 (Car), `dist_m` = 3,39 m, r = 3,52 m. Cuboid **xa nhất**: `s04f43` idx 10 (Car), `dist_m` = 75,20 m, r = 75,21 m: ngoài 50 m nên không được chấm. Ở xa, r ≈ `dist_m` vì z (−1,12 m) rất nhỏ so với khoảng cách ngang.

(b) Người đi bộ `s02f10` idx 14: 0,70 × 0,53 × 1,71 m, cả ba chiều trong khoảng người lớn (0,4–0,9 × 0,5–0,9 × 1,50–1,95 m). Xe con `s01f51` idx 3: 4,73 × 1,96 × 1,45 m. Dài và cao trong khoảng sedan, rộng 1,96 m hơi vượt 1,90 m: hộp bao rộng hơn thân xe một chút (có thể gồm gương).

(c) Xe cùng chiều phía trước `s01f51` idx 3 (trước, 9,2 m): `yaw_deg` = 270,0°, đúng mức ≈ 270°. Xe ngược chiều: trong Bảng A **không có hộp nào ở ≈ 90°**. Hướng mặc định 270° do script đặt chưa được kiểm từng xe, nên tôi không khẳng định là không có xe ngược chiều.

Mật độ theo khoảng cách (4 cuboid xe con):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s01f00, 1, Car | 3,39 m | 3083 |
| s01f00, 3, Car | 12,85 m | 701 |
| s04f17, 6, Car | 27,05 m | 207 |
| s01f00, 14, Car | 39,70 m | 83 |

Quy luật: xa gấp khoảng 3,8 lần (3,4 → 12,9 m) thì số điểm còn khoảng 1/4,4; xa gấp khoảng 12 lần (3,4 → 39,7 m) thì còn khoảng 1/37. Số điểm giảm gần theo 1/d². Kết quả khớp đường cong ở slide: 701 điểm ở 10–20 m (slide 718), 207 ở 20–30 m (slide 214), 83 ở 30–40 m (slide 72).

Tự kiểm: đã sửa sau `preview` / `selfcheck`:
- `s02f10` idx 5, 7, 10: gán **Car** nhưng dài 13,11 m, cao 3,21 m → đổi sang **Bus** sau khi xem camera.
- `s05f46` idx 2 (Car): đáy chìm 0,71 m → nâng lên, còn chìm 0,34 m.
- 114 hộp Car/Bus/Truck/Cyclist có length < width và yaw ≈ 0°/180° → đổi chỗ dài/rộng, xoay 90° về yaw ≈ 270° (xem §6b).

Cảnh báo giữ nguyên: chiều rộng xe con 2,30–2,80 m (khoảng 20 hộp) và Bus/Truck 3,15–3,62 m. Hộp đúng vị trí nhưng bao dư; tôi hết thời gian thu hẹp. Các cờ đáy còn lại (`s01f00` idx 4 và 12, `s02f39` idx 5, `s03f23` idx 1 và 2, `s03f56` idx 7, `s04f43` idx 8, `s05f46` idx 6) chưa sửa vì cùng lý do. Đây là lỗi thật, không phải cờ nhầm.

TODO(học viên): đổi kiểm: MSSV người đã kiểm chéo với bạn, hai con số nào được đối chiếu với bảng A, khớp hay lệch.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
(a) Model 3D dự đoán vị trí, kích thước và hướng của vật trong không gian thật để xe lập kế hoạch đi. Vì vậy nhãn phải đo bằng mét trong hệ toạ độ LiDAR; pixel đổi theo khoảng cách và góc camera nên không cho model biết vật cách bao xa hay lớn bao nhiêu. Vật thưa điểm, ví dụ xe ở 40 m chỉ còn khoảng 80 điểm so với khoảng 3000 điểm ở 3 m, cần quy tắc riêng (ngưỡng 15–20% thể tích, LOW_VISIBILITY). Nếu người gán đoán bừa kích thước và hướng từ vài điểm, model sẽ học nhãn nhiễu và tự tin sai ở vùng xa.

(b) Có dùng AI: Claude Code (Anthropic).
- Hỏi chính: hướng dẫn các bước làm lab, đọc Bảng A và phiếu tự kiểm để tìm hộp lỗi, cách sửa 111 hộp chưa xoay.
- Cách dùng kết quả: Claude chỉ ra phần lớn hộp có length < width và yaw ≈ 0°. Tôi dùng script do Claude viết (`work/fix_yaw.py`, gọi CVAT API trên chính task `D13-03219`, không sửa CSV) để đổi chỗ dài/rộng và xoay 90° về yaw 270°. Bảng A vẫn sinh bằng `python scripts/lab.py sync`. Claude cũng soạn phần số liệu của báo cáo từ Bảng A và phiếu tự kiểm; tôi đối chiếu lại.

(c) Điều còn chưa chắc:
- Hướng đầu xe của các hộp do script đặt mặc định 270° chưa được kiểm từng xe trên camera. Xe ngược chiều hoặc xe đỗ quay đầu có thể đang bị ngược.
- Khoảng 20 xe con bao rộng hơn thân xe, làm giảm độ khớp.
- Lỗi ban đầu là tôi chỉ kéo cạnh hộp cho dài theo thân xe mà không xoay vạch `+X`. Hình nhìn từ trên xuống vẫn đúng nên tôi không nhận ra cho đến khi đọc cờ "length < width" trong `selfcheck`.
