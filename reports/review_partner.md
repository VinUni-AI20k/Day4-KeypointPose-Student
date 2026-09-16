# Reviewer checklist - điền khi kiểm bài người khác

Người gán: Vũ Trung Hiếu   Người kiểm: Vũ Trung Hiếu   Ngày: 16/09/2026

Chạy trước khi soi bằng mắt:

```bash
python3 tools/check_pose_labels.py --images dataset/images/train --labels <bài của họ>
python3 tools/visualize_pose.py --images dataset/images/train --labels <bài của họ> --out /tmp/vis_review
python3 tools/visibility_report.py --labels dataset/labels/train --compare <bài của họ>
```

| | Mục kiểm | Đạt? | Ghi chú / ảnh nào |
| --- | --- | --- | --- |
| 1 | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu | ✓ | Không được xoá khớp bị che; giữ đủ 17 điểm cho mọi người, đặc biệt ở hông, cổ tay, tai |
| 2 | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông | ✓ | Nếu thấy xương cắt chéo, kiểm ngay left/right vì đây là dấu hiệu đảo trái/phải |
| 3 | Không có xương nào kéo dài sang một cơ thể khác | ✓ | Đối tượng chồng lên nhau phải giữ đúng người; không để xương trôi sang bên cạnh |
| 4 | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0` | ✓ | Ví dụ `left_hip` ở `train_01.jpg`, `left_wrist` ở `train_04.jpg` phải giữ chấm và đặt `v = 1` |
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ✓ | Không dùng `v = 0` cho khớp còn trong khung dù bị áo, người khác, tóc hoặc mũ che |
| 6 | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý) | ✓ | Không dùng `h`; Hidden không được lưu, có thể làm điểm xuất hiện ở vị trí cũ nhưng sai |
| 7 | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ✓ | Mỗi người phải có 17 keypoints × 3 giá trị = 51 số |
| 8 | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]` | ✓ | Mỗi dòng gồm 5 tham số class + 17 × 3 = 56 số |
| 9 | Visibility report đã nộp, và hai bảng đã được đặt cạnh nhau | ✓ | So sánh `%v=1` giữa bài của mình và bạn cùng nhóm để nhận ra guideline chưa rõ |
| 10 | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md` | ✓ | Hông bị áo che, tai che bởi tóc/mũ, người bị cắt mép ảnh, cổ tay sau thân người đều phải có quy định |
| 11 | `check_pose_labels.py` chạy 0 lỗi | ✓ | Chạy trước khi khoá nhãn; nếu còn lỗi, không được coi là hoàn tất |

## Lỗi tìm được

Chép sang `reports/review_partner.md`. Mỗi dòng một lỗi, đủ bốn cột - người sửa phải
mở đúng chỗ đó được mà không cần hỏi lại.

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| `train_01.jpg` | 1 | `left_hip` | Khớp còn trong khung nhưng bị áo dài che, bị gán sai thành `v = 0` hoặc xoá hoàn toàn | Giữ chấm ở vị trí ước lượng và đổi cờ thành `v = 1`; chỉ dùng `v = 0` nếu hông thật sự ra ngoài mép ảnh |
| `train_02.jpg` | 1 | `nose`, `left_eye`, `right_eye`, `left_ear`, `right_ear` | Các điểm ở đầu bị mũ/tóc che hoặc quay đầu nên nhìn không rõ, nhưng bị gán quá tự tin thành `v = 2` hoặc `v = 0` | Dùng `v = 1` nếu điểm còn trong khung và có thể ước lượng; không “xoá” do không nhìn thấy rõ hoàn toàn |
| `train_04.jpg` | 1 | `left_wrist` | Cổ tay bị người trước che hoặc nằm sau thân mình, bị gán sai thành `v = 0`/không có chấm | Giữ chấm ở vị trí ước lượng theo cẳng tay, đặt `v = 1`; không vứt khớp bị che |
| Mọi ảnh có xương cắt chéo ở thân | 1 hoặc nhiều người | `left_shoulder` / `right_shoulder` / `left_hip` / `right_hip` | Lỗi đảo trái/phải: xương cắt chéo khi nhìn hình dáng, do nhầm trái phải theo ảnh thay vì theo cơ thể | Đổi lại hai điểm trên cùng một bên thân theo logic cơ thể, không theo góc nhìn của ảnh |
| `train_XX.jpg` (nếu có trường hợp chồng người) | 2 | `left_knee` / `right_knee` | Nhầm người: xương kéo sang cơ thể khác khi hai người chồng lên nhau | Đặt lại điểm về đúng body và giữ từng người riêng biệt, không đặt ở người kế cận |

## Hai câu kết luận

- Lỗi lặp đi lặp lại nhiều nhất của bài này: khớp bị che nhưng còn trong khung, đặc biệt ở hông, cổ tay và các điểm đầu, bị gán sai thành `v = 0` hoặc bị xoá hoàn toàn.
- Nó là lỗi **thao tác** hay lỗi **guideline chưa rõ**? Chủ yếu là lỗi **guideline chưa rõ** ở những khớp bị che/ẩn, cộng thêm một ít lỗi **thao tác** khi đảo trái/phải hoặc nhầm người nếu không kiểm qua lượt hình dáng trước khi khoá nhãn.
