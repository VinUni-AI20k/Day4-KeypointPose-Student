# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Vũ Trung Hiếu   Nhóm: ______   Ngày: 16/09/2026

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 288 / 178 / 27 |
| Thời gian trung bình mỗi ảnh | Chưa có dữ liệu công cụ |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. left_hip - 97%
2. right_hip - 90%
3. left_ear - 48%

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Các khớp hông có %v=1 cao vì thường bị thân người hoặc quần áo che, nên khó nhìn trực tiếp.
Tai có %v=1 cao hơn vai vì vị trí giải phẫu nhỏ và dễ bị tóc hoặc góc chụp che khuất.
Điều này phù hợp với các vị trí khó xác định trong ảnh, không chỉ là lỗi gán nhãn ngẫu nhiên.

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.952 | 0.9549 |
| OKS@0.50 | 1.000 | 1.000 |
| OKS@0.75 | 1.000 | 1.000 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 1 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 | 0 |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

- `train_04.jpg`, người #1, `left_wrist`: di chuyển điểm từ khoảng `(0.597531, 0.806302)` về `(0.504687, 0.789934)` theo vị trí đúng trên ảnh.
- Không có sửa thêm sau lần rework này.
- Không có sửa thêm sau lần rework này.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh.

## 3. Kiểm chéo

Bạn cùng nhóm: Chưa có dữ liệu

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| Chưa có dữ liệu | - | - | - | Chưa có bảng của bạn cùng nhóm |
| Chưa có dữ liệu | - | - | - | Chưa có bảng của bạn cùng nhóm |

Luật đã ghi trong `GUIDELINE_MINI.md`:

- Khi khớp còn trong khung nhưng bị quần áo, người khác hoặc vật thể che, vẫn đặt điểm theo vị trí giải phẫu ước lượng và chọn `v = 1`; chỉ chọn `v = 0` khi khớp thực sự nằm ngoài mép ảnh.

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | 0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | 0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` tăng từ 0.6853 lên 0.6908, chênh +0.0055. Kết quả cho thấy fine-tune có cải thiện rất nhẹ trên tập test, nhưng không phải mức tăng lớn; điều này phù hợp với chỉ 20 ảnh train, nên sự học thêm có giới hạn.

2. `box_mAP50-95` giảm từ 0.8119 xuống 0.8041 (-0.0078), trong khi `pose_mAP50-95` tăng +0.0055. Điều này cho thấy việc fine-tune không cải thiện mạnh cả hai nhiệm vụ cùng lúc; model có cải thiện ở pose nhưng đồng thời có xu hướng yếu hơn ở box detection ở một mức rất nhỏ.

3. Không có ảnh test nào được phân tích riêng trong báo cáo của repo; vì vậy không thể chỉ ra một ảnh sai cụ thể bằng bằng chứng trực quan. Để kết luận, cần có bảng lỗi per-image và ảnh dự đoán tương ứng.

4. `mean_oks` cuối cùng là 0.9549 và tất cả khớp có thể được xem là tốt; không có bằng chứng từ `outputs/eval_vs_gold.json` cho thấy một keypoint nào bị sai nghiêm trọng lên tới mức nào đó trên toàn bộ tập. Lỗi chấm chủ yếu là các cờ khác gold và vài lệch nhẹ, không phải trượt hẳn.

5. So sánh giữa nhãn của tôi và gold cho thấy phần lớn lỗi là do `co_khac_gold` và `gold_khong_gan_nhan` chứ không phải vị trí sai nhiều; do đó không thể kết luận ảnh gán tệ nhất và ảnh model dự đoán tệ nhất là cùng một ảnh mà không có phân tích per-image riêng.

## 5. Một rule evidence bạn đã dùng

Chọn một keypoint trong ảnh core mà bạn phải quyết định giữa `v=1` và `v=0`. Nêu ảnh, người,
khớp, bằng chứng nhìn thấy và lý do chọn trạng thái đó trong 3-5 câu.

Ở `train_01.jpg`, người #1, tôi chọn `left_hip` là `v=1` dù hông bị quần áo dài che và
không nhìn rõ bề mặt khớp. Vị trí vẫn nằm trong khung ảnh và có thể ước lượng theo phần
thân, nên phải đặt chấm và dùng `v=1`, không dùng `v=0`. Nếu chọn `v=0`, model sẽ học
rằng hông không cần dự đoán ở các tư thế tương tự.
