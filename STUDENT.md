# Thông tin học viên

- Họ và tên: Đỗ Thị Kim Oanh
- MSHV: 2A202604034
- Lớp (L2 / L3 / khác): L2
- Tài khoản GitHub: DoThiKimOanhB22DCCN606
- Repo bài nộp (URL): https://github.com/DoThiKimOanhB22DCCN606/K5-L23-DAY08-202604034-DoThiKimOanh-AIPrelabel
- Commit chốt bài (hash): commit HEAD trước khi nộp bài
- Máy: Windows 11; không GPU NVIDIA, chạy mô hình nặng trên Google Colab T4
- CVAT (Docker): Docker Compose; CVAT đã chạy thành công ở môi trường local

## Kết quả tóm tắt

- Máy: accepted / edited / deleted: 603 / 0 / 0; mẫu số: 603 box máy
- Added: 0; mẫu số: 603 đối tượng sau duyệt
- So với gold: precision / recall của máy: 49.4% / 93.2%; 41 TP, 42 FP, 3 FN, 44 đối tượng gold
- Bắt bẫy: 12/12 = 100%
- Thời gian: gán gold 25 phút/ảnh 4 ảnh; duyệt batch 95 phút cho 24 ảnh, khoảng 4 phút/ảnh
- Lỗi QC theo đối tượng: 0/125 = 0.0%; theo ảnh: 0/5 = 0.0%
- Quyết định: Release — nhãn batch đạt QC theo mẫu và bắt đủ 12/12 bẫy, với giới hạn mẫu 5 ảnh
