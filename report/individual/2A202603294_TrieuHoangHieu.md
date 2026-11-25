# Báo cáo Day 13 — Triệu Hoàng Hiếu, 2A202603294

## 1. Kết luận trước

MSSV: **2A202603294**. Task: **D13-2A202603294**, job 27. Môi trường: Windows, Microsoft Edge, CVAT Server/UI **2.81.0**, đã kiểm tra trong About. Người kiểm chéo: Hà Huy Dũng, 2A202603271.

Task của tôi có **62 cuboid dạng Shape**: 52 Car, 4 Bus, 4 Cyclist và 2 Pedestrian. Số hộp theo frame: s01f22: 8; s01f45: 7; s02f04: 5; s02f26: 6; s03f07: 7; s03f33: 5; s04f02: 7; s04f34: 6; s05f10: 6; s05f39: 5. Bốn frame ít hộp nhất đều có 5 hộp. Bản nhãn ưu tiên vật có cơ sở từ cụm điểm và camera, chưa khẳng định bao phủ mọi vật trong vùng. Dữ liệu đã kiểm đủ 10 frame, mỗi frame có 7 camera.

Phiếu tự kiểm có **1/62 hộp bị cảnh báo**, tại s05f39 #5. Phần đáng tin hơn là nhận dạng xe gần và tương quan dài/rộng; phần còn yếu là kích thước phần khuất, hướng người đứng và đáy ở nơi mặt đường thay đổi. Nhãn đã lưu trong CVAT; **11 ảnh chụp bắt buộc đã lưu** và được đóng gói ZIP; phần kiểm chéo với học viên khác vẫn chưa thực hiện.

Thao tác CVAT: kéo chuột trái để xoay, chuột phải để dời, con lăn để phóng; nháy đúp chọn hộp rồi kiểm Top/Side/Front. Dùng Shape, chỉnh dài/rộng và yaw trong Top, chỉnh cao/đáy trong Side và Front. Bật Cuboid orientation, đối chiếu thêm ảnh từ preview; không xoay nghiêng ở Side/Front.

## 2. Đọc frame

Hệ VF: **x trái, y sau, z lên**. Hai ảnh Top được chụp từ cửa sổ Top quanh xe mẫu, nên **phía trước (−y) gần cạnh phải ảnh, +x gần hướng lên, +y gần cạnh trái**; ảnh s01f45 lệch khoảng 7° do yaw xe mẫu. Vạch đỏ ngắn là +X cục bộ của hộp, không phải +x VF; vạch xanh lá là +Y cục bộ. Ảnh chụp gốc được giữ nguyên, phần giải thích hướng đặt ở đây. Khung Perspective có thể xoay độc lập.

| Frame | Mặt đường và phạm vi đã đối chiếu | Vật trước/sau/hai bên | Cụm còn khó đọc |
|---|---|---|---|
| s01f22 | Đường gần tương đối phẳng, thấp hơn LiDAR khoảng 1,7–1,8 m; đã đối chiếu vật phía trước khoảng 26–28 m | Xe con gần bên trái, xe buýt xa hơn bên trái; có xe phía sau, xe hai bánh phía trước bên phải | Vùng sau xe buýt bị che; cụm nhỏ sát cây/lề chưa đủ để suy thành vật giao thông |
| s01f45 | Đường gần tương đối phẳng; đáy xe mẫu quanh −1,8 m; đã gán xe phía trước khoảng 22 m | Nhiều xe cùng chiều phía trước; có xe theo sau và bên trái | Cụm sát cột/cây bên phải; xe xa chỉ lộ một phần bề mặt |

Camera giúp phân biệt màu/thân xe, người đứng với người ngồi trên xe hai bánh, và đầu/đuôi khi cụm điểm không rõ. LiDAR cung cấp vị trí theo mét trong hệ cảm biến, độ cao so với đường và cấu trúc thưa/dày của điểm đo. Các thông tin bổ sung cho nhau; một ảnh đơn không tự cho khoảng cách tuyệt đối nếu thiếu giả thiết hoặc hiệu chuẩn.

Tám frame còn lại được đọc theo trục VF, không mặc định phía trước là cạnh trên của mọi cửa sổ:

| Frame | Điều cần lưu ý |
|---|---|
| s02f04 | Xe buýt và xe con gần nhau; không gộp hai vật thành một hộp |
| s02f26 | Hàng rào tạo cụm dài; tách người đứng gần nhà chờ khỏi công trình |
| s03f07 | Xe con và van nối nhau; không lấy điểm hàng rào làm thân xe |
| s03f33 | Có người bên lề, xe hai bánh phía trước, xe buýt xa phía sau |
| s04f02 | Nhiều làn và xe hai bên; kiểm hướng từng xe |
| s04f34 | Có người lái xe hai bánh bên phải; Cyclist bao cả người và xe |
| s05f10 | Giao lộ có xe cùng chiều và hướng khác; không suy yaw chỉ từ vị trí |
| s05f39 | Xe buýt chỉ lộ bề mặt sau/bên; một xe làn bên có cảnh báo đáy |

Ảnh đã lưu: t1-s01f22-3d.jpg, t1-s01f22-top.jpg, t1-s01f45-3d.jpg, t1-s01f45-top.jpg. Khoảng cách trên là phạm vi đã đối chiếu, không phải khẳng định giới hạn xa nhất nhìn thấy trong toàn cảnh.

## 3. Ba cuboid mẫu

Idx dưới đây là số trong **Bảng A của bản xuất cuối**, không phải ID toàn cục trong danh sách Objects. Lần Save/xuất mới có thể thay thứ tự idx. Trong ba ảnh mẫu, ID CVAT lần lượt là 1 (Car), 3 (Bus), 35 (Pedestrian); Bảng A lần lượt là s01f22 #7, s01f22 #4, s03f33 #2.

| Vật | Frame, idx | Class | Hướng, đáy và kích thước |
|---|---|---|---|
| Xe con gần | s01f22, #7 | Car | Yaw 269,9°; đáy −1,745 m; l/w/h = 4,270/1,790/1,570 m, trong khoảng sedan của bảng 4.3 |
| Xe buýt | s01f22, #4 | Bus | Yaw 269,9°; đáy −1,750 m; l/w/h = 11,400/2,600/3,300 m. Đã kiểm hai mép trong Top và chỉnh rộng từ 2,70 xuống 2,60 m; nằm trong khoảng tham khảo xe buýt thường |
| Người bên lề | s03f33, #2 | Pedestrian | Đáy −1,590 m; cao hơn đường lân cận khoảng 0,12 m; l/w/h = 0,650/0,600/1,700 m, trong khoảng người lớn. Đã đối chiếu thêm cam08: hướng mặt chưa đủ rõ để xác nhận góc chính xác, nên giữ yaw 269,9° và ghi giới hạn này |

Các hộp đã được đối chiếu hình chiếu camera và hình học LiDAR, chỉnh tay ở cửa sổ Top, Side và Front. Ảnh đã lưu: t2-a-car.jpg, t2-b-bus-truck.jpg, t2-c-ped-cyclist.jpg.

**Chưa có kết quả hiệu chuẩn với lớp**, nên chưa kết luận mức lệch giữa các học viên. Ví dụ bỏ qua: cụm thấp/thưa sát lề ở s01f22 gần vùng xe hai bánh phía trước, có thể là hạ tầng/vật nhỏ (UNCERTAIN_CLASS/LOW_VISIBILITY); cụm cao dạng cột ở s03f33 không được gán thành người chỉ vì hình dáng đứng.

## 4. Bốn hiện tượng chất lượng

Nguồn là frame của task cá nhân. Nguyên nhân là **giả thuyết**; bốn ảnh minh chứng đã lưu dưới dạng JPG trong thư mục images.

| Hiện tượng | Frame / ảnh cần bổ sung | Quan sát và giả thuyết | Hệ quả khi gán |
|---|---|---|---|
| Bóng che | s01f22 / t4-1-bong-che.jpg | Xe buýt gần che không gian bên trái; ít điểm phía sau vật lớn có thể do tia không tới được | Không xem vùng không có điểm là đường trống; chỉ suy phần thân có căn cứ, bật occluded khi xác nhận bị che |
| Thưa ở xa | s03f33 / t4-2-thua-o-xa.jpg | Xe #4 khoảng 33,53 m ít điểm hơn nhiều xe gần; mật độ góc của tia có thể góp phần | Đối chiếu camera/hình dạng; không kéo hộp theo điểm rời; bỏ qua nếu thiếu cơ sở |
| Phản xạ thấp / ít điểm bất thường | s01f45 / t4-3-phan-xa-thap.jpg | Kính và thân tối màu có vùng điểm không đều; vật liệu, góc tới, che khuất là các giả thuyết cần phân biệt | Không kết luận thiếu điểm là thiếu thân xe; không nới hộp theo quầng intensity |
| Điểm lạ | s02f26 / t4-4-diem-la.jpg | Mặt cắt Side có điểm tách biệt phía dưới dải đường, ở bên trái-phía dưới hộp xe đang chọn. Có thể do phản xạ nhiều đường hoặc đo nhiễu; không suy ra vật thật từ cụm này | Không gán điểm lẻ thành vật; không hạ đáy hộp theo điểm bất thường |

## 5. Số đo và tự kiểm

Nguồn: submission/D13-2A202603294-bangA.csv và submission/D13-2A202603294-tukiem.csv. Bảng A được tạo bằng hàm xuất nguyên bản từ bản nhãn **đã lưu và xuất khỏi CVAT**; phiếu tự kiểm sinh bằng lệnh selfcheck. Đã chạy preview. Dist_m là khoảng cách ngang; r tính thêm z.

| Frame, idx, class | z (m) | h (m) | z đáy (m) | dist_m (m) | r (m) | yaw (°) |
|---|---:|---:|---:|---:|---:|---:|
| s01f22, #7, Car | −0,960 | 1,570 | −1,745 | 3,16 | 3,298 | 269,9 |
| s01f22, #4, Bus | −0,100 | 3,300 | −1,750 | 6,88 | 6,879 | 269,9 |
| s03f33, #2, Pedestrian | −0,740 | 1,700 | −1,590 | 11,35 | 11,370 | 269,9 |

Ba đáy trên gần dải đường/lề tại vị trí tương ứng; dùng mặt đường cục bộ thay vì ép mọi đáy về một số. Hộp **gần nhất: s02f04 #5, Car**, dist_m = **2,99 m**, r = **3,158 m**. Hộp **xa nhất: s03f33 #5, Bus**, dist_m = **49,26 m**, r = **49,259 m**. Chênh lệch nhỏ giữa dist_m và r hiển thị ở xe buýt là do làm tròn.

Xe con s01f22 #7 và người s03f33 #2 có ba chiều trong khoảng tham khảo ở mục 3. Xe cùng chiều phía trước s01f45 #5 có yaw **267,0°**, gần 270°; xe ngược chiều s04f34 #2 có yaw **91,1°**, gần 90°.

| Cuboid Car | dist_m (m) | points_in |
|---|---:|---:|
| s01f22 #7 | 3,16 | 3217 |
| s01f45 #7 | 8,87 | 1519 |
| s01f45 #5 | 15,82 | 421 |
| s01f45 #6 | 22,01 | 97 |

Trong s01f45, so xe #7 với #6: khoảng cách tăng **2,48 lần**, số điểm còn khoảng **6,4%**. Xu hướng giảm tương tự slide, nhưng các mức ví dụ 1968/718/214/72 điểm không phải ngưỡng cứng: còn phụ thuộc kích thước, góc nhìn, vật liệu và che khuất. Đây là các xe khác nhau, không phải thí nghiệm có kiểm soát trên một xe.

Hai chỉnh sửa khi rà soát: s01f22 #4 được căn lại tâm và kiểm lại chiều rộng theo hai mép thân (giá trị cuối 2,60 m); s05f39 #1 được tăng chiều dài và căn tâm dọc để bao mặt trước còn thiếu. Các chỉnh sửa nằm trong bản nhãn đã nhập, lưu và xuất lại.

Cảnh báo giữ lại: **s05f39 #5**, đáy cao hơn ước lượng đường cục bộ **0,45 m**. Đã kiểm lại trong Side và lưu ảnh đối chiếu riêng ở work/check-s05f39-bottom.jpg: mặt cắt điểm đặt đáy quanh −1,705 m; vùng lấy mẫu quanh hộp có thể gồm đường thấp hơn ở làn khác. Đây là lý do tạm giữ để xem lại, chưa đủ để khẳng định hộp đúng. Không chỉnh CSV hoặc chỉnh hộp chỉ để tắt cờ.

**Kiểm chéo với Hà Huy Dũng (2A202603271):** kiểm s03f06 idx 1 dist_m = 3,09 m — khớp; s02f09 idx 2 (Bus) length = 11,47 m — khớp.

## 6. Ý nghĩa của nhãn, sử dụng AI và điều chưa chắc

Nhãn 3D dùng mét để mô hình học vị trí và kích thước trong không gian cảm biến; kích thước pixel thay đổi theo khoảng cách/phối cảnh. Vật thưa điểm cần quy tắc riêng vì bề mặt đo được không đại diện đầy đủ thể tích thật; suy đoán thiếu căn cứ tạo sai số có hệ thống cho mô hình.

- Công cụ: **Antigravity (AI assistant)**.
- Yêu cầu chính: hỗ trợ cài đặt môi trường, cấu hình CVAT, và soạn báo cáo.
- Cách sử dụng: toàn bộ cuboid được tự vẽ tay trên CVAT; AI không tham gia vẽ, chỉnh sửa hay nhập nhãn. Số liệu trong báo cáo lấy từ bản xuất CVAT và phiếu tự kiểm do lệnh `sync` sinh ra.

Điều chưa chắc: phần thân suy luận ở xa/bị khuất, góc hướng mặt chính xác của người đứng và cảnh báo đáy tại s05f39 #5. Đã kiểm lại chiều rộng xe buýt và lưu đủ 11 ảnh. Chưa ghi nhận lỗi phát hiện sau hạn.
