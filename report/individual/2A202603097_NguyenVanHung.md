# Báo cáo Day 13 — Nguyễn Văn Hùng, 2A202603097

## 1. Kết luận trước

Task cá nhân `D13-2A202603097` trên Windows, dùng trình duyệt Brave. CVAT cục bộ đang chạy phiên bản 2.74.1. MSSV bạn kiểm chéo là `2A202603152`.

| Frame | s01f23 | s01f51 | s02f15 | s02f52 | s03f19 | s03f43 | s04f08 | s04f38 | s05f07 | s05f25 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Số cuboid | 8 | 7 | 8 | 5 | 6 | 5 | 7 | 5 | 5 | 5 |

Bảng A có 61 cuboid gồm 54 Car, 1 Bus, 2 Truck và 4 Cyclist. Năm frame có 5 cuboid, bằng mức tối thiểu của đề. Chưa ghi lại lý do từng frame dừng ở mức đó. Lần tự kiểm ban đầu có 3 cuboid bị gắn cờ. Sau khi chỉnh cả ba, phiếu tự kiểm hiện có **0/61 cuboid bị gắn cờ**. Vị trí đáy của ba hộp mẫu có thể định lượng từ độ hở với mặt đường quanh hộp. Hướng của Cyclist và hộp xa ít điểm cần đối chiếu thêm với cảnh.

- Trong khung 3D, kéo chuột trái để xoay đến góc nhìn từ trên xuống, kéo chuột phải để dời cảnh và lăn con lăn để đổi mức thu phóng.
- Chọn **Cuboid → Shape**, nháy đúp để đặt hộp rồi chỉnh các mặt qua Top, Side và Front.
- Bật **Appearance → Cuboid orientation** để xem vạch đỏ chỉ hướng. Dùng `preview` để đối chiếu hộp với ảnh từ các camera.

## 2. Đọc frame (Bước 2)

Trong hệ trục VF, phía trước xe là −y, +x hướng sang trái xe, +y hướng ra sau và +z hướng lên. Trên ảnh nhìn từ trên xuống, cạnh phía trước nằm đối diện hướng +y. Cam01 dùng để đối chiếu hướng trước. Cạnh cụ thể trên từng ảnh phụ thuộc vào góc xoay khung 3D khi chụp.

| Frame và ảnh | Mặt đường quanh hộp | Vật xa nhất đã gán | Vật đã gán và vị trí | Chi tiết chưa xác nhận |
|---|---|---|---|---|
| `s01f23` · `t1-s01f23-3d.png`, `t1-s01f23-top.png` | Khoảng 1,71–1,83 m dưới LiDAR theo phiếu tự kiểm. Chưa xác định độ dốc toàn cảnh. | Car #5, 19,55 m, trước bên phải | 5 Car, 1 Bus, 2 Cyclist. Bus #3 cách 13,75 m ở sau bên trái. | Cạnh trước trên ảnh và vị trí cụm chưa đọc được. |
| `s01f51` · `t1-s01f51-3d.png`, `t1-s01f51-top.png` | Khoảng 1,80–1,90 m dưới LiDAR theo phiếu tự kiểm. Chưa xác định độ dốc toàn cảnh. | Car #4, 28,98 m, sau bên trái | 7 Car. Car #1 cách 7,02 m ở gần chính giữa phía sau. | Cạnh trước trên ảnh và vị trí cụm chưa đọc được. |

Khi đối chiếu hai frame, camera giúp kiểm tra màu và bề mặt, nhận ra đầu và đuôi xe, và đọc chi tiết ngoại hình để phân biệt các loại vật. LiDAR cho số đo khoảng cách tới tâm hộp, độ cao mặt đường quanh hộp và số điểm trong từng cuboid. Góc giữa hai camera có thể tạo vùng không có ảnh đối chiếu, nên vị trí của các hộp phía sau và hai bên được đọc từ tọa độ LiDAR.

Tám frame còn lại đều dùng −y làm hướng trước. Vị trí cạnh trước trên ảnh chưa được ghi lại. Bảng A cho thấy:

| Frame | Chi tiết từ cuboid |
|---|---|
| `s02f15` | Hai Car phía trước có yaw gần 90° và 270°. |
| `s02f52` | Car #2 cao hơn mặt đường ước lượng quanh hộp 0,32 m. |
| `s03f19` | Car #3 cách 25,16 m và có 147 điểm. |
| `s03f43` | Có hai Cyclist ở sau bên phải. |
| `s04f08` | Truck #4 ở sau bên trái, cách 20,23 m. |
| `s04f38` | Truck #4 ở sau bên trái, cách 15,76 m. |
| `s05f07` | Hai Car phía trước có yaw gần 90° và 270°. |
| `s05f25` | Car #5 ở trước bên trái, cách 28,00 m. |

## 3. Ba cuboid có hướng dẫn (Bước 3)

Ba dòng dưới đây dùng vật mẫu từ bảng A trên `s01f23`. Ảnh minh chứng tương ứng theo loại gồm `t2-a-car.png`, `t2-b-bus-truck.png` và `t2-c-ped-cyclist.png`. Chưa xác nhận ảnh Cyclist chụp hộp #6 hay #7.

| Vật mẫu | Frame, idx, class | Hướng hộp từ yaw | Đáy so với đường quanh hộp | Dài × rộng × cao và bảng 4.3 |
|---|---|---|---|---|
| Xe con gần nhất trên frame đầu | `s01f23` #2, Car | 269,4°, gần hướng trước −y | +0,06 m, gần mặt đường | 4,514 × 1,630 × 1,482 m. Rộng dưới mốc 1,70 m của sedan/hatchback. |
| Xe buýt | `s01f23` #3, Bus | 270,5°, gần hướng trước −y | +0,12 m, gần mặt đường | 12,434 × 2,938 × 3,059 m. Rộng vượt mốc 2,60 m của xe buýt thường. |
| Xe hai bánh | `s01f23` #6, Cyclist | 18,0°, gần hướng +x. Cần đối chiếu đầu xe trên ảnh. | +0,17 m, gần mặt đường | 2,322 × 0,700 × 1,628 m. Nếu là xe máy có người, chiều dài vượt mốc 2,30 m khoảng 0,022 m. |

Bảng A ghi hướng hình học của hộp. Xác nhận mũi tên trùng với đầu xe và kiểm đủ sáu mặt cần xem lại Top, Side, Front và ảnh camera. Phần hiệu chuẩn bằng task chung được bỏ qua. Chưa có ghi chú riêng về các vật đã bỏ qua vì `UNCERTAIN_CLASS` hoặc `LOW_VISIBILITY`.

## 4. Bốn hiện tượng chất lượng (Bước 4)

| Hiện tượng | Frame và tên ảnh | Dấu hiệu trên point cloud và giả thuyết | Hệ quả khi gán nhãn |
|---|---|---|---|
| Bóng che | `s01f23` · `t4-1-bong-che.png` | Dấu hiệu cần tìm là vùng thưa hoặc trống phía sau vật cản, trong khi hai bên vẫn có điểm. Giả thuyết là vật phía trước chặn tia LiDAR. | Khi có vật bị che một phần, bật `occluded` và bao phần suy luận được từ hình dạng và ảnh. |
| Thưa ở xa | `s01f51` · `t4-2-thua-o-xa.png` | Car #4 cách 28,98 m và có 119 điểm. Khoảng cách lớn có thể làm số tia chạm vật giảm. | Vẽ khi quan sát được khoảng 15–20% thể tích hoặc có đủ căn cứ từ camera và hình dạng. Trường hợp còn lại ghi `LOW_VISIBILITY`. |
| Phản xạ thấp hoặc ít điểm bất thường | `s03f19` · `t4-3-phan-xa-thap.png` | Hai Car #3 và #6 có khoảng cách 25,16 m và 26,61 m, với 147 và 463 điểm. Bề mặt phản xạ yếu là một giả thuyết. Góc nhìn hoặc che khuất cũng có thể làm chênh lệch số điểm. | So sánh các vật cùng tầm, xem ảnh camera và căn theo phần hình học có cơ sở trước khi quyết định vẽ. |
| Điểm lạ | `s05f25` · `t4-4-diem-la.png` | Dấu hiệu cần tìm là điểm rời hoặc cụm không có cấu trúc của vật, chẳng hạn điểm nằm dưới mặt đường. Nhiễu đo hoặc phản xạ nhiều đường là giả thuyết. | Bỏ qua cụm điểm rời khi thiếu bằng chứng cho thấy đó là vật thể. |

Cả bốn ảnh lấy từ các frame của task cá nhân `D13-2A202603097`. Hai ví dụ định lượng lấy từ bảng A và phiếu tự kiểm.

## 5. Số từ cuboid của tôi (Bước 5)

Số đo lấy từ `D13-2A202603097-bangA.csv`. Số điểm và độ hở đáy lấy từ `D13-2A202603097-tukiem.csv`. Mốc đường tham khảo của bài là z ≈ −1,7 m. Khi có `ground_gap_m`, cột này cho biết độ hở với đường ước lượng quanh từng hộp.

| Cuboid | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw | Nhận xét |
|---|---:|---:|---:|---:|---:|---:|---|
| `s01f23` #2 Car | −0,989 m | 1,482 m | −1,730 m | 4,20 m | 4,32 m | 269,4° | Đáy gần đường, độ hở +0,06 m. |
| `s01f23` #3 Bus | −0,106 m | 3,059 m | −1,636 m | 13,75 m | 13,75 m | 270,5° | Đáy gần đường, độ hở +0,12 m. |
| `s01f23` #6 Cyclist | −0,798 m | 1,628 m | −1,612 m | 15,74 m | 15,76 m | 18,0° | Đáy gần đường, độ hở +0,17 m. |

- **Gần nhất** là `s03f43` #1 Car, với `dist_m` 3,44 m và r 3,55 m. **Xa nhất** là `s01f51` #4 Car, với `dist_m` 28,98 m và r 29,00 m.
- **Kích thước.** Car #2 ở mục 3 hẹp hơn mốc sedan 0,070 m. Hộp có thể ôm thiếu mép bên hoặc xe thực tế hẹp. Cyclist #6 dài hơn mốc xe máy 0,022 m, có thể do căn mép trước/sau hoặc kích thước thực của xe. Cả hai giả thuyết cần kiểm lại trên khung Top.
- **Yaw.** Trên `s02f15`, Car #4 ở phía trước có yaw 271,3°, lệch 1,3° so với hướng cùng chiều. Car #2 cũng ở phía trước, có yaw 90,6°, lệch 0,6° so với hướng ngược chiều. So sánh này dựa trên hướng hộp đã ghi.

| Car | Khoảng cách ngang | Điểm trong hộp |
|---|---:|---:|
| `s01f23` #2 | 4,20 m | 3.010 |
| `s01f51` #3 | 13,23 m | 885 |
| `s01f23` #5 | 19,55 m | 452 |
| `s01f51` #4 | 28,98 m | 119 |

Trong bốn mẫu này, xe cách 19,55 m ở xa gấp khoảng 4,7 lần xe cách 4,20 m và còn khoảng 15% số điểm. Xu hướng giảm phù hợp với số trung vị tham khảo trên slide là khoảng 1.968 điểm ở 0–10 m, 718 ở 10–20 m, 214 ở 20–30 m và 72 ở 30–40 m. Số điểm từng hộp còn phụ thuộc vào kích thước, góc nhìn và độ che khuất.

**Tự kiểm và kiểm chéo.** Nội dung kiểm chéo với bạn MSSV `2A202603152` gồm ba cuboid từng bị cảnh báo. Bảng dưới đây ghi số trước khi sửa từ log `sync` và số sau khi sửa từ bảng A và phiếu tự kiểm.

| Cuboid | Trước khi sửa | Sau khi sửa |
|---|---|---|
| `s01f23` #7 Cyclist | Dài 2,53 m, vượt khoảng tự kiểm | Rút chiều dài xuống 2,300 m. |
| `s02f15` #3 Car | Đáy cao hơn mặt đường 1,45 m, rộng 2,39 m, cao 1,00 m | Giảm độ hở đáy còn +0,21 m, thu chiều rộng còn 2,200 m và tăng chiều cao lên 1,553 m. |
| `s02f15` #8 Car | Rộng 2,36 m, vượt khoảng tự kiểm | Thu chiều rộng còn 2,200 m. |

Các số sau khi sửa trong bảng khớp với bản xuất hiện tại. Phiếu tự kiểm sau khi sửa có **0 cờ trên 61 cuboid**.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Mô hình dùng nhãn 3D theo mét để học vị trí và kích thước thật trong hệ LiDAR, vì hình chiếu của cùng một vật thay đổi với khoảng cách và góc camera. Với vật thưa điểm, quy tắc về mức quan sát giúp mô hình học từ những hộp có đủ căn cứ hình học và giảm nhiễu do các hộp được suy đoán quá mức.

- **Công cụ AI** — ChatGPT (Codex).
- **Câu hỏi chính** — Kiểm tra hồ sơ nộp còn thiếu và điền báo cáo từ bảng A, phiếu tự kiểm và hướng dẫn lab.
- **Cách dùng kết quả** — Đối chiếu số liệu và các chỉnh sửa, tính khoảng cách 3D, so kích thước với bảng 4.3 và xác định mã frame trên ảnh bằng nhận dạng chữ tại chỗ.

Các điểm còn chưa chắc gồm hướng thực của Cyclist #6, mép ngang của Bus #3 và mức ảnh hưởng của góc nhìn đến những Car ít điểm ở xa.
