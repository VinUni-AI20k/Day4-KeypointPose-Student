# Mini guideline - nhóm: ______  |  người gán: Vũ Trung Hiếu  |  ngày: 16/09/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Hầu hết là chọn bị che vì mặc quần áo, chỉ chọn nhìn thấy khi điểm đó rõ ràng là khớp (bị gập)| ![Ảnh mẫu hông](reports/th1.png) |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Nhìn thấy, còn nếu che hoàn toàn thì để bị che | ![Ảnh mũ bảo hiểm](reports/th2.png)|
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các điểm ở phần thân người không nhìn thấy thì để `v = 0` | ![Ảnh mẫu người ở mép](reports/th3.png)|
| Cổ tay nằm sau tay lái / sau thân mình | Không nhìn thấy, cảm giác tương đối, để `v = 1`| ![Ảnh mẫu tay](reports/th4.png) |
| Hai người chồng lên nhau | Nếu bị che một phần thì các điểm bị che để `v = 1`, các điểm không bị che `v = 2`, còn bị che hoàn toàn thì không thêm người| ![Ảnh mẫu người](reports/th5.png) |
| Người nhỏ đến mức nào thì không gán nữa | Chưa gặp tình huống này| |

Với mỗi luật, chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu, không phải
một câu văn chung chung.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_01.jpg`, người thứ `1`, khớp `left_hip`

- Mơ hồ ở chỗ nào: không xác định được có nhìn thấy không
- Bạn quyết thế nào: cho `v = 1`
- Vì sao: ước lượng mang tính tương đối, không xác định được có đúng là khớp nối không do không bị gập rõ ràng
- Nếu người khác quyết ngược lại thì model học sai cái gì: model sẽ học hông là `v = 0` hoặc không cần dự đoán ở vị trí đó, dù hông vẫn còn trong khung ảnh.

### Ca 2 - ảnh `train_02.jpg`, người thứ `1`, khớp `nose, eyes, ears`

- Mơ hồ ở chỗ nào: không biết là nên để `v = 2` hay `v = 1`
- Bạn quyết thế nào: để tất cả 5 điểm là `v = 1`
- Vì sao: do người trong ảnh quay đầu lại, mũ bảo hiểm che hết nên không xác định được những điểm này, chỉ xác định mang tính tương đối
- Nếu người khác quyết ngược lại thì model học sai cái gì: model sẽ học tọa độ mắt, tai và mũi như các điểm nhìn thấy rõ, dẫn đến dự đoán quá tự tin ở các tư thế quay đầu hoặc bị mũ che.

### Ca 3 - ảnh `train_04.jpg`, người thứ `1`, khớp `left_wrist`

- Mơ hồ ở chỗ nào: bị người đằng trước che
- Bạn quyết thế nào: đặt `v = 1`
- Vì sao: người phía trước che, xác định vị trí tương đối
- Nếu người khác quyết ngược lại thì model học sai cái gì: model sẽ học cổ tay nằm ngoài ảnh hoặc không tồn tại, thay vì học vị trí cổ tay bị che nhưng vẫn suy ra được từ cẳng tay.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: Chưa có dữ liệu của bạn cùng nhóm để so sánh.
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Chưa thể kết luận vì bảng đối chiếu hiện có 0 file và 0 skeleton.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: Khi khớp còn trong khung nhưng bị quần áo, người khác hoặc vật thể che, vẫn đặt điểm theo vị trí giải phẫu ước lượng và chọn `v = 1`; chỉ chọn `v = 0` khi khớp thực sự nằm ngoài mép ảnh.
