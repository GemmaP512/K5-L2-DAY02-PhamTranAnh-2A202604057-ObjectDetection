# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- **Họ tên:** Phạm Trần Anh
- **MSSV:** 2A202604057
- **Email:** 26ai.anhpt8@vinuni.edu.vn
- **Lớp:** K5 — Level 2, AI Thực chiến VinUni
- **Link repo bài làm:** https://github.com/GemmaP512/K5-L2-DAY02-PhamTranAnh-2A202604057-ObjectDetection
- **Link Colab đã chạy:** https://colab.research.google.com/github/GemmaP512/K5-L2-DAY02-PhamTranAnh-2A202604057-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb

## 2. Schema và mini guideline

Mini guideline: [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

| ID | Lớp | Nguyên tắc nhận diện |
| --- | --- | --- |
| 0 | `car` | Ô tô con, taxi, sedan, SUV; pickup theo quy tắc schema đã chọn |
| 1 | `truck` | Xe tải, xe vận tải hàng hóa |
| 2 | `bus` | Xe buýt hoặc xe chở khách thuộc lớp bus |
| 3 | `van` | Xe van, minivan |

Mỗi xe hợp lệ có một box riêng, ôm sát phần thân xe nhìn thấy; khi xe bị che một phần hoặc cắt bởi mép ảnh, không đoán phần nằm ngoài vùng nhìn thấy. Không gán người, xe máy, xe đạp, biển báo hoặc vật thể ngoài schema. Với xe quá mờ hoặc quá nhỏ để xác định lớp, không tự đoán.

**Ba tình huống đã thảo luận khi rà soát (chỉ ghi nhận những quan sát đã có, không khẳng định đây là ba quyết định được lưu trong CVAT):**

| Ảnh | Tình huống thực tế | Quyết định cuối | Lý do theo guideline |
| --- | --- | --- | --- |
| `drive_049` | Phương tiện thân cao gần dải phân cách, khó phân biệt `car` và `van` | Đã đối chiếu tọa độ nhãn: student gán `van`, Gold gán `car`; chưa xác nhận thao tác đổi class trong CVAT | Cần đối chiếu hình dạng phương tiện và định nghĩa hai lớp, không suy ra đúng/sai chỉ từ tên lớp |
| `drive_049` | Các phương tiện bị cắt ở cạnh dưới khung hình | Đã xem xét nguyên tắc vẽ box tới mép ảnh; chưa có bản ghi xác nhận sửa riêng từng box | GUIDE yêu cầu box không vượt ảnh, chỉ bao vùng quan sát được |
| `drive_003` | Cảnh đông phương tiện, nhiều xe nhỏ và che khuất; box có nguy cơ trùng hoặc không sát | Đã rà soát qua ảnh chụp và phân tích FP/FN; chưa xác nhận danh sách box đã thay đổi | Chỉ dán xe nhận diện chắc lớp, mỗi xe một box riêng, không ôm thêm xe lân cận |

Trường hợp cần làm rõ: phân biệt `car` và `van` ở phương tiện thân cao, cũng như quyết định đối với xe rất nhỏ ở xa.

## 3. Annotation và self-QC

- **Công cụ:** CVAT Local trên MacBook (Docker).
- **Tập train:** 50 ảnh, định dạng xuất `Ultralytics YOLO Detection 1.0`.
- **Thư mục nhãn:** `dataset/labels/train/` (50 tệp `.txt`).
- **Kiểm tra cấu trúc:** chạy `python3 tools/check_yolo_labels.py`; kết quả thực tế: **“ĐẠT cấu trúc: 50/50 file nhãn hợp lệ. Chờ ground truth để chấm chất lượng.”**
- **Danh sách ít nhất 10 ảnh đã tự rà bằng mắt:** Chưa có danh sách xác nhận 10 ảnh đã tự kiểm tra; bằng chứng hội thoại có các ảnh `drive_003`, `drive_049`.
- **Lỗi phát hiện và cách xử lý:** đã xem xét các vấn đề box không khớp, phương tiện bị che khuất, xe nhỏ ở xa và khả năng nhầm `car`/`van`. Đã rà soát, chỉnh sửa một số ảnh trên CVAT; người học xác nhận đã sửa nhãn trên CVAT, nhưng chưa cung cấp nhật ký box thay đổi hoặc kết quả xuất lại để xác nhận chi tiết.
- **Commit khóa nhãn ban đầu:** `86b53b4` trên nhánh `main` (đã push lên GitHub).

Checker xác nhận định dạng và tính hợp lệ của tệp, không đồng nghĩa tất cả box đều đúng.

## 4. Gold QC và rework

Sử dụng `gold_train_labels/` do giảng viên phát để đánh giá với script chính thức, ngưỡng ghép IoU = 0.50. Thư mục Gold Set chỉ dùng cục bộ và **không đưa lên GitHub**.

| Chỉ số | Trước sửa (đã đo) | Sau sửa nếu có |
| --- | ---: | ---: |
| TP | 416 | Chưa có số liệu |
| FP | 393 | Chưa có số liệu |
| FN | 237 | Chưa có số liệu |
| Precision | 0.5142 | Chưa có số liệu |
| Recall | 0.6371 | Chưa có số liệu |
| F1 | 0.5691 | Chưa có số liệu |
| mean_iou_of_matches | 0.8080 | Chưa có số liệu |
| passed | `false` | Chưa xác nhận |

Kết quả chấm sau cùng trong repo: [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json).

**Phân tích lần đánh giá ban đầu:** Mean IoU = 0.8080 đã vượt ngưỡng 0.70, nhưng F1 = 0.5691 dưới ngưỡng 0.80. Có 393 box của học viên chưa ghép được (FP) và 237 box Gold chưa được ghép (FN). Các box chưa ghép có thể do thừa, sai lớp, lệch tọa độ hoặc bỏ sót; không thể kết luận nguyên nhân chỉ từ số lượng FP/FN.

**Lượt rework:** đã kiểm tra hình ảnh và điều chỉnh nhãn trong CVAT, trong đó có ảnh `drive_049`. Ở bản đánh giá ban đầu, `drive_049` có 6 Gold box, 23 box học viên và 4 box khớp. Các ảnh `drive_049`, `drive_003` đã được thảo luận trong phiên rà soát; người học xác nhận có sửa trên CVAT. Chưa có bản diff nhãn sau sửa nên không ghi những box cụ thể là đã bị xóa hay đổi lớp..

**Trạng thái CP5 sau rework:** Chưa có JSON đánh giá sau rework trong tài liệu được cung cấp. Lần đo đã xác nhận: F1 = 0.5691 và Mean IoU = 0.8080, `passed=false`. Không tuyên bố đã đạt CP5.. Không thay đổi Gold Set hoặc ghi đè lịch sử commit khóa nhãn ban đầu.

## 5. Kết quả YOLO

- **Nền tảng:** Google Colab; GPU NVIDIA Tesla T4 (CUDA:0).
- **Runtime:** Python 3.13.16; PyTorch 2.14.1+cu130; Ultralytics 8.4.208.
- **Mô hình:** YOLO26n.
- **Dữ liệu:** 50 ảnh train; 10 ảnh eval dùng để validation và đánh giá, không dùng để cập nhật trọng số trong train.
- **Thay đổi notebook:** chỉnh cell chuẩn bị repository để khắc phục lỗi `google.colab.auth.user_token` không tồn tại, dùng URL GitHub Public; Không có bằng chứng về thay đổi khác đối với tham số train..
- **Checkpoint tốt nhất trong phiên Colab:** `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (không nộp `.pt`).

| Chỉ số trên 10 ảnh eval | Giá trị |
| --- | ---: |
| Precision | 0.4101 |
| Recall | 0.2298 |
| mAP50 | 0.2719 |
| mAP50–95 | 0.2016 |

**Kết quả theo lớp** (113 đối tượng trong 10 ảnh eval):

| Lớp | Instances | Precision | Recall | mAP50 | mAP50–95 |
| --- | ---: | ---: | ---: | ---: | ---: |
| `car` | 94 | 0.833 | 0.636 | 0.738 | 0.523 |
| `truck` | 5 | 0.673 | 0.200 | 0.239 | 0.205 |
| `bus` | 2 | 0.000 | 0.000 | 0.00786 | 0.00393 |
| `van` | 12 | 0.134 | 0.0833 | 0.103 | 0.0742 |

**Kết quả và ảnh minh họa (đường dẫn tương đối từ báo cáo):**

- [Thư mục 10 ảnh eval có dự đoán](assets/eval_predictions/visuals/)
- [Kết quả train (results.csv và log)](assets/yolo26_runs/vehicle_annotation/)
- [Kết quả đánh giá model, confusion matrix, các đường cong](assets/yolo26_runs/vehicle_eval/)

**Bằng chứng hình ảnh đã cung cấp:** ảnh chụp màn hình kết quả Colab và hai ảnh chụp CVAT là bằng chứng thao tác/đánh giá annotation; chưa có file ảnh dự đoán thuộc 10 ảnh eval trong phiên trao đổi.

![Bảng kết quả YOLO trên Colab](assets/evidence/colab_metrics.png)

![Rà soát annotation drive_003 trên CVAT](assets/evidence/cvat_drive_003.png)

![Rà soát annotation drive_049 trên CVAT](assets/evidence/cvat_drive_049.png)

**Đối tượng quan sát trên ảnh eval:** Chưa có ảnh dự đoán cụ thể kèm tên ảnh eval; không gán tên ảnh hoặc mô tả lỗi phát hiện chưa kiểm chứng.

### Câu 1. Lớp nào có recall thấp nhất? Chỉ ra một ảnh eval minh họa.

Lớp **`bus`** có Recall thấp nhất (**0.000**), trên 2 bus trong 2 ảnh của tập eval; không có bus nào được tính là phát hiện đúng ở ngưỡng đánh giá. Kế tiếp là `van` (0.0833) và `truck` (0.200). Do số bus eval rất ít, kết quả này nhạy với từng dự đoán. **Ảnh eval minh họa:** Chưa xác định được tên ảnh chứa `bus` từ dữ liệu đã cung cấp. Metrics xác nhận Recall của `bus` = 0.000; không gán sai tên ảnh.

### Câu 2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?

Cần kiểm tra độ chính xác định vị box: cạnh box có quá rộng/hẹp, lệch tâm, không ôm sát phần thân xe nhìn thấy hoặc không nhất quán so với nhãn chuẩn hay không. Box có thể đạt IoU ≥ 0.50 nhưng trượt ở các ngưỡng cao hơn. Trong kết quả này, cả mAP50 = 0.2719 lẫn mAP50–95 = 0.2016 đều thấp; vì vậy không thể quy toàn bộ vấn đề cho độ sát box, mà còn phải xem lỗi bỏ sót và phân lớp.

### Câu 3. Có lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ?

Kết quả cho thấy Recall của `car` (0.636) cao hơn đáng kể `truck` (0.200), `van` (0.0833) và `bus` (0.000). Cần rà lại những trường hợp khó phân biệt `car`–`van`, `van`–`bus`, `truck`–`van`, cùng xe nhỏ/bị che khuất. Trong vòng rà soát ảnh `drive_049`, có một trường hợp đáng chú ý giữa `car` và `van`. Tuy nhiên bảng metrics **chưa chứng minh mô hình đã nhầm cụ thể lớp nào sang lớp nào**; phải xem confusion matrix và ảnh eval để kết luận.

### Câu 4. Vì sao không dùng 10 ảnh eval để train dù chúng có nhãn?

Nếu sử dụng ảnh eval làm dữ liệu train, mô hình sẽ học trực tiếp từ chính những ảnh dùng để đánh giá, dẫn đến rò rỉ dữ liệu và kết quả có thể quá lạc quan. Trong notebook này, 10 ảnh eval có vai trò validation, được dùng để theo dõi kết quả và lựa chọn `best.pt`, **không phải tập test độc lập**. Phải giữ nguyên cách chia 50 train / 10 eval theo hướng dẫn.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
| --- | --- | --- | --- |
| Chuẩn bị CVAT local | Lỗi kết nối server trên MacBook | Kiểm tra Docker/CVAT rồi mở lại Task | Đã gán nhãn được 50 ảnh |
| CVAT annotation | Khó xử lý xe bị che, cắt rìa và phân lớp `car`/`van` | Đối chiếu `GUIDE.md`, rà soát box | Đã export nhãn; cần ghi các case cụ thể |
| Export/CP3 | File ZIP chứa `train.txt` ngoài 50 TXT nhãn | Chỉ lấy `labels/train/*.txt`; chạy checker | Đạt cấu trúc 50/50 |
| CP5 | F1 ban đầu 0.5691; FP/FN cao | Xem báo cáo `per_image`, rà soát và sửa trong CVAT | Chưa nhận được số đo lại sau chỉnh sửa |
| Colab | `google.colab.auth` không có `user_token` | Sửa cell chuẩn bị, clone repo Public qua HTTPS | Notebook đã train và evaluate YOLO26n |
| Model | Recall thấp ở `bus`, `van`, `truck` | Đối chiếu metrics từng lớp | Đã phân tích khả năng thiếu mẫu/mất cân bằng; chưa kết luận nguyên nhân từ ảnh |

## 7. Tự đánh giá theo rubric

| Tiêu chí | Điểm tối đa | Bằng chứng và tình trạng |
| --- | ---: | --- |
| Hoàn thành và định dạng YOLO | 10 | Checker xác nhận 50/50 file hợp lệ |
| Đầy đủ vật thể | 20 | Có 50 file nhãn, nhưng lần QC đầu còn FN |
| Độ bám sát và hình học box | 20 | Mean IoU ban đầu 0.808; cần phân biệt với chất lượng toàn bộ box |
| Phân lớp nhất quán | 15 | Đã dùng bốn class; còn nghi vấn nhầm lớp ở một số ảnh |
| Mini guideline và quyết định mơ hồ | 10 | Có guideline; file guideline trên GitHub còn trống ở thời điểm kiểm tra; báo cáo này ghi 3 tình huống đã thảo luận, chưa chứng thực quyết định cuối |
| Gold-set annotation QC | 15 | Có JSON đánh giá và rework; F1 ban đầu chưa đạt 0.80, chưa có số đo sau sửa được cung cấp |
| Pipeline YOLO và phân tích | 10 | Colab đã train/evaluate, có metrics và phân tích; cần bổ sung ảnh minh họa và link Colab |

**Tổng điểm tối đa:** 100. Điểm thực tế do giảng viên đánh giá. Không tự nhận đạt tiêu chí F1 khi chưa có kết quả rework xác nhận. Kết quả mAP thấp không tự động đồng nghĩa bài bị trừ điểm nếu giữ đúng split và phân tích nguyên nhân theo rubric.

## 8. Công cụ AI và tài liệu tham khảo

- **CVAT Local:** Tự tạo và rà soát annotation, xuất bộ nhãn YOLO Detection.
- **Python / script của VinUni:** Kiểm tra tính hợp lệ 50 nhãn và đánh giá Gold QC.
- **Google Colab, Ultralytics YOLO:** Huấn luyện YOLO26n, đánh giá trên 10 ảnh eval và xuất metrics/hình dự đoán.
- **ChatGPT:** Hỗ trợ giải thích schema, FP/FN/IoU, hướng dẫn Git, CVAT và khắc phục lỗi khởi tạo Colab; số liệu dùng trong báo cáo lấy từ log thực tế đã chạy.

**Nguồn tham khảo:**

1. `GUIDE.md` — VinUni AI Thực chiến, Day 2.
2. `CHECKPOINTS.md`, `RULES.md`, `SUBMISSION.md`, `RUBRIC.md` — repository bài Lab Day 2.
3. [CVAT Documentation](https://docs.cvat.ai/).
4. [Ultralytics Documentation](https://docs.ultralytics.com/).

**Lưu ý nộp bài:** Không đưa `gold_train_labels/`, token, mật khẩu hoặc file checkpoint `.pt` vào repository GitHub.


**Tình trạng hoàn thiện bằng chứng:** Bản báo cáo được điền từ các kết quả đã chia sẻ; email, link Colab cá nhân, danh sách 10 ảnh self-QC, file eval prediction minh họa và kết quả CP5 sau rework chưa được cung cấp. Những phần này được công khai là chưa xác nhận, không tự suy diễn.
