# Label contract — Phố châu Á — BỔ SUNG v1.1 — 04034 - Đỗ Thị Kim Oanh

> Chỉ ghi phần bổ sung so với v1.0.

## 1. Câu hỏi mở đã chốt

| # | Câu hỏi | Quyết định | Ảnh ví dụ | case_id |
|---|---|---|---|---|
| 1 | Xe máy đỗ dày đặc gần camera nên gán từng vật hay ignore_region? | Gán từng motorbike nếu riêng từng xe vẫn rõ và box cao ít nhất 20 px. Dùng một ignore_region chỉ khi cụm xe đập nhau không thể tách ranh giới. | img_005, img_018, img_028 | CAL-02 |
| 2 | Người lái xích lô trong khung xe ba bánh nên nhập vào box xe không? | Không nhập. Dùng person cho người và three_wheeler cho cả khung xe, mui và thùng hàng. | img_010, img_027, img_033 | CAL-03 |
| 3 | Khi YOLOE tạo box tuk-tuk hoặc ba gác lỏng thì box cần bao đến đâu? | Box ôm khung xe nhìn thấy; người lái là box person riêng; hàng gắn liền xe thuộc xe, hàng nhô ra ngoài phần thân xe không mở rộng box. | img_013, img_020 | CAL-04 |

## 2. Ca khó mới

| Ảnh | Tình huống | Máy làm gì | Quyết định | Quy tắc mới |
|---|---|---|---|---|
| img_013, img_017, img_023 | Tuk-tuk có mui và người lái | YOLO26 gọi là car hoặc truck; YOLOE tạo three_wheeler với box lỏng | Phân loại là three_wheeler | Mục 2: xe ba bánh địa phương không ánh xạ sang lớp COCO |
| img_005, img_018, img_028 | Xe máy đỗ hoặc di chuyển sát nhau | Box máy có thể chồng nhau hoặc coi nhiều xe như một box | Gán riêng nếu ranh giới rõ; ignore_region nếu cụm không thể tách và vật ở dưới 20 px | Mục 4.1.6 |
| img_010 | Xích lô có người lái và hành khách trong khung xe | Box xe ba bánh bao cả người, làm khó box person riêng | Giữ person và three_wheeler là hai box riêng | Mục 4.1.4 và mục 5 |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do |
|---|---|---|
| v1.0 | Quy tắc label contract gốc | Bản giảng viên |
| v1.1 | Chốt xử lý tuk-tuk, cụm xe máy và người lái xích lô | CAL-01, CAL-02, CAL-03, CAL-04 |
