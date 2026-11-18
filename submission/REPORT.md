# Báo cáo Day 08 — 04034 - Đỗ Thị Kim Oanh

> Báo cáo được viết dựa trên sản phẩm trong repository, kết quả thực thi script và quan sát trên ảnh mẫu.

## 1. Model và recipe

| Lớp | Model + cỡ | Ngưỡng | Vì sao |
|---|---|---|---|
| person | YOLO26-m | 0.35 | Recall đạt 94% trên 16 vật gold |
| bicycle | YOLO26-m | mặc định 0.3 | Chỉ có 3 vật gold, mẫu quá ít |
| motorbike | YOLO26-m | 0.10 | Recall đạt 90% trên 10 vật gold |
| car | YOLO26-m | mặc định 0.3 | Không ngưỡng nào đạt recall 90%; recall cao nhất 86% |
| bus | YOLO26-m | mặc định 0.3 | Chỉ 1 vật gold |
| truck | YOLO26-m | mặc định 0.3 | Không có vật truck trong 4 ảnh gold |
| three_wheeler | YOLOE-s + YOLO26-m | 0.15 | Recall 100%; 7 vật gold, precision 78% |

Lệnh merge đã dùng:

`python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "person=0.35,motorbike=0.1,three_wheeler=0.15" --conf 0.3 --iou 0.6 --split batch,gold --out work/prelabel/merged_review/pre.coco.json`

Phiên bản SAM chính: SAM 2.1 base, trọng số sam2.1_b.pt. Phiên bản này cân bằng giữa độ bao sát mask, tốc độ và RAM trên Colab T4.

YOLO26 không thể khắc phục bằng đổi ngưỡng đối với tuk-tuk, xích lô và ba gác vì ba loại này nằm ngoài 80 lớp COCO. Hướng sửa là YOLOE với prompt tuk-tuk/cyclo/cargo tricycle, sau đó dùng quy tắc three_wheeler thắng car/truck khi IoU đạt 0.6.

Điểm tin cậy không đồng nghĩa nhãn đúng: YOLO26 vẫn có thể tạo box điểm cao cho truck hoặc car sai vùng tuk-tuk. Ngược lại, vật dễ nhận dạng ở gần camera có thể giữ box ở điểm thấp hơn nhưng vẫn có khớp vật thật.

Giới hạn: ngưỡng được chọn trên chính 4 ảnh gold nên precision/recall trên gold là lạc quan. Class bicycle/bus/truck ít mẫu nên kết quả yếu.

## 2. AI error profile

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted | 603 | 100.0% | 603 box máy |
| edited | 0 | 0.0% | 603 box máy |
| deleted | 0 | 0.0% | 603 box máy |
| added | 0 | 0.0% | 603 đối tượng sau duyệt |

Lớp person chiếm nhiều nhất: 250 box được accepted. Motorbike có 239 box. Không phát hiện deleted/added theo review_stats hiện tại.

So với gold: precision 49.4% và recall 93.2%. Mẫu precision là 41 TP + 42 FP; mẫu recall là 41 TP + 3 FN trong 44 vật gold.

## 3. Thiên kiến tự động hóa và ảnh bẫy

Bắt được 12/12 bẫy, đạt 100%: missing 3/3, extra 3/3, wrong_class 3/3 và loose 3/3. Không có loại bẫy nào lọt.

Accepted 100% nhưng cần giải thích cùng bắt bẫy: số accepted cao là kết quả sau khi loại vùng ảnh nhân tạo quanh bẫy khỏi profile. Điều này cho thấy nhãn review không có thay đổi tự nhiên trong batch hiện có; bắt bẫy cho bằng chứng riêng rằng người review đã phát hiện cả missing, extra, wrong_class và loose.

## 4. Máy có đáng dùng không?

Thời gian gán gold: 25 phút cho 4 ảnh, tương đương khoảng 6.25 phút/ảnh. Thời gian review batch: 95 phút cho 24 ảnh, khoảng 4 phút/ảnh. Tốc độ chỉ mang tính ước lượng vì Colab dùng T4 còn CVAT chạy local.

Máy đáng dùng làm pre-label cho vùng person/motorbike/three_wheeler nhưng không đáng dùng nếu lấy nhãn máy làm nhãn cuối mà không review. Precision trên gold chỉ 49.4% do nhiều box thừa trong 4 ảnh.

Ba quy tắc giảm thiên kiến:

1. Quét ảnh trước khi nhìn nhãn máy; sau đó mới bật hoặc tải annotation để kiểm.
2. Kiểm riêng missing, extra, wrong_class và loose thay vì chỉ nhìn overall accuracy.
3. Đối chiếu hai con số: accepted/added và gold precision/recall; không kết luận máy tốt chỉ dựa vào accepted cao.

## 5. Mask SAM cho 3 vật

| Ảnh | SAM | Số click | Người lái/hàng hoá | Lỗi quan sát | File |
|---|---|---|---|---|---|
| img_010.jpg | SAM 2.1 base | 3 positive, 2 negative | Mask ban đầu bao người lái và khung xe; cần click âm để loại phần không thuộc vật chính | Mask phủ bóng và phần nền ngoài khung xe nếu prompt không đủ | submission/masks/cyclo_img010.jpg |
| img_013.jpg | SAM 2.1 base | 3 positive, 2 negative | Mask chứa người lái và hộp xe; cần quyết định bằng guideline | Nhánh mask mở rộng sang người, hàng hoặc nền | submission/masks/tuktuk_img013.jpg |
| img_013.jpg | SAM 2.1 base, mask tinh chỉnh | 4 positive, 3 negative | Mask tinh chỉnh vẫn bao người lái nếu người nằm trong khung tuk-tuk | Cần loại bóng, nền và thanh xe không thuộc vật | submission/masks/tuktuk_img013_refined.jpg |

SAM 2.1 được chọn vì chạy ổn trên Colab T4, có click/box prompt và cân bằng tốc độ/chất lượng. Mask tuktuk tinh chỉnh bám khung xe tốt hơn bản đầu nhưng vẫn có nguy cơ bao người lái, hàng hoá và bóng đổ.

Theo guideline mục 4.2, polygon nên chỉ theo phần nhìn thấy của vật; bóng đổ không thuộc vật. Ở tuk-tuk, người lái là person riêng nên mask xe không nhất thiết bao người lái. Quy tắc này giúp chọn refined mask nhưng guideline vẫn cần bổ sung rõ mức bao phủ cho hàng gắn vào xe.

## 6. Calibration

Có 4 quyết định trong calibration_decisions.csv:

- CAL-01: tuk-tuk, ba gác và xích lô là three_wheeler.
- CAL-02: cụm xe máy gần camera chỉ dùng ignore_region nếu không thể tách từng xe.
- CAL-03: người lái xích lô là person và xe ba bánh là three_wheeler.
- CAL-04: box tuk-tuk được tinh chỉnh quanh khung xe nhìn thấy.

Quy tắc quan trọng nhất là CAL-01 vì đây là điểm khác biệt chính giữa closed-set COCO và open-vocabulary. Guideline v1.1 đã ghi đầy đủ các quyết định và ảnh ví dụ.

Nguyên nhân gốc: guideline ban đầu còn mơ hồ ở cụm xe máy, người lái trong xe ba bánh và hàng nhô ra; kết hợp đó với sai ánh xạ của máy nên cần calibration ở cả hai phần.

## 7. QC và quyết định

Mẫu QC: 5 ảnh ngẫu nhiên, seed 42, 125 đối tượng.

Lỗi theo đối tượng: 0/125 = 0.0%. Lỗi theo ảnh: 0/5 = 0.0%, không vượt ngưỡng Release 2%. Không ghi nhận loại lỗi, không có ảnh ví dụ lỗi.

Giới hạn: 0 lỗi trong 5 ảnh cho cận trên Wilson khoảng 43% cho tỉ lệ ảnh lỗi thật. Mẫu này không chứng minh được ngưỡng 2%.

Script kiểm được tên ảnh, lớp taxonomy, box, số đo review và file COCO. Script không kiểm được độ ôm sát pixel, hiện tượng cả người lẫn máy cùng bỏ sót hoặc người có tuân thủ checklist thật trong suốt quá trình duyệt.

Quyết định: Release, ngày 2026-11-18. Theo cây quyết định, không có guideline-gap hoặc data-problem, không có lỗi hệ thống, bắt bẫy đạt 12/12. Tuy nhiên Release này chỉ áp dụng cho release candidate hiện tại và cần mở rộng mẫu QC trước khi triển khai thực tế.

## 8. Ghi chú cá nhân
