# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên:Lương Quốc Việt
- MSSV:2A202603127
- Email:26ai.vietlq@vinuni.edu.vn
- Lớp:2A
- Link repo bài làm:https://github.com/23010769-sketch/K5-L2-LuongQuocViet2A202603127-Day02-ObjectDetection.git
- Link Colab đã chạy:https://colab.research.google.com/drive/1uYY77c-IBnQqpVe3jqGIxoU306DOaTEC?hl=vi#scrollTo=APOGOa2t9Ax2

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.
Bài thực hành sử dụng 4 lớp phương tiện:
- car (0): Xe ô tô con, sedan, SUV và hatchback.
- truck (1): Xe tải dùng để vận chuyển hàng hóa.
- bus (2): Xe buýt và xe khách.
- van (3): Xe van, xe chở hàng hoặc chở khách cỡ nhỏ.
Quy tắc gán nhãn:
- Bounding box phải bao sát phương tiện.
- Phương tiện bị che khuất vẫn được gán nhãn nếu xác định được lớp.
- Phương tiện ở mép ảnh được gán nhãn theo phần nhìn thấy.
- Phương tiện quá nhỏ hoặc không thể xác định lớp cần được xem xét thủ công.
Các trường hợp mơ hồ được ghi lại trong GUIDELINE_MINI.md.

## 3. Annotation và self-QC

- Số file nhãn train:50/50 file.
- Kết quả checker và lỗi đã xử lý:ĐẠT, 50/50 file nhãn hợp lệ.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt:drive_001.jpg, drive_002.jpg, drive_003.jpg, drive_004.jpg, drive_005.jpg, drive_006.jpg, drive_007.jpg, drive_008.jpg, drive_009.jpg, drive_010.jpg.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:Đã kiểm tra các trường hợp phương tiện bị che khuất, phương tiện có kích thước nhỏ và các đối tượng dễ nhầm lẫn giữa car, truck, bus và van. Kiểm tra vị trí bounding box, độ bao sát đối tượng và tính chính xác của nhãn lớp. Các trường hợp chưa rõ được đối chiếu với GUIDELINE_MINI.md để xác định cách gán nhãn phù hợp.
- Commit khóa nhãn ban đầu:4569dbf

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 433 | 432 |
| FP | 392 | 384 |
| FN | 220 | 221 |
| Precision | 0.5248 | 0.5294 |
| Recall | 0.6631 | 0.6616 |
| F1 | 0.5859 | 0.5882 |
| mean_iou_of_matches | 0.7808 | 0.7807 |
| passed | false | false |

Dẫn tới `outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Python 3.13.18; chạy CPU (Intel Xeon CPU @ 2.20GHz), không dùng GPU; Ultralytics 8.4.208; Torch 2.14.1+cpu.
- Các tham số thay đổi so với notebook nếu có và lý do: Không thay đổi tham số chính so với notebook: `yolo26n.pt`, 50 epoch, `imgsz=640`, `batch=-1`, `seed=20260909`, `device=cpu`; dùng 50 ảnh train và 10 ảnh eval/validation.
- Đường dẫn best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (checkpoint trong phiên Colab; file `best.pt` không nằm trong gói asset đã nộp).

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.5794 |
| Recall | 0.4935 |
| mAP50 | 0.5570 |
| mAP50–95 | 0.3821 |

Hai folder kết quả đã được đặt cạnh báo cáo: [eval_predictions](./assets/eval_predictions/) và [yolo26_runs](./assets/yolo26_runs/). Ví dụ, ở [drive_051.jpg](./assets/eval_predictions/visuals/drive_051.jpg), xe buýt trong nhãn thật không được dự đoán đúng lớp; mô hình chỉ hiển thị các box `car`, phù hợp với recall bus thấp.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp `bus` có recall thấp nhất; ma trận nhầm lẫn chuẩn hóa cho thấy recall bus gần 0. [drive_051.jpg](./assets/eval_predictions/visuals/drive_051.jpg) là ví dụ: xe buýt trong nhãn thật bị bỏ sót hoặc không được nhận diện đúng lớp.
2. Cần kiểm tra độ khít và vị trí của bounding box: box có bao sát vật thể không, có lệch tâm hoặc quá rộng/hẹp không. mAP50 chỉ cần IoU từ 0.5, còn mAP50–95 yêu cầu nhiều ngưỡng IoU cao hơn nên sẽ giảm khi box chưa chính xác.
3. Các lỗi class liên quan guideline là nhầm giữa `car`, `truck`, `bus` và `van`. Dữ kiện là bản rà soát đã sửa rõ các trường hợp như `drive_002` car→van, `drive_005` car→truck và `drive_009` car→bus; ngoài ra ghi chú rà soát nêu các ca mơ hồ về mapping ID ở `drive_014`, `drive_047` và `drive_048` cần giảng viên xác nhận.
4. Không dùng 10 ảnh eval để train để giữ đánh giá công bằng và tránh rò rỉ dữ liệu; nếu train trên chúng, mAP sẽ bị lạc quan và không còn đo khả năng tổng quát. Trong notebook, 10 ảnh này được dùng làm tập validation trong quá trình train và để chọn `best.pt`, nên không phải test độc lập.

## 6. Khó khăn và cách xử lý

Trong quá trình thực hiện bài thực hành Object Detection, tôi gặp một số khó khăn liên quan đến việc sử dụng CVAT, xuất dữ liệu nhãn, kiểm tra chất lượng annotation, huấn luyện mô hình YOLO và quản lý mã nguồn bằng GitHub.

Bước/thời điểm

Lỗi quan sát

Cách đã thử

Kết quả/còn thiếu

Tạo Project trên CVAT

Hệ thống báo "You do not have permission to perform this action".

Kiểm tra tài khoản, chuyển từ Organization sang Personal workspace.

Đã tạo được Task để gán nhãn.

Tạo Task và tải ảnh

Ban đầu không có quyền tạo Task trong Organization.

Chuyển sang Personal workspace và tải 50 ảnh từ tập train.

Tạo thành công Task gồm 50 ảnh.

Gán nhãn phương tiện

Một số phương tiện nhỏ, bị che khuất hoặc dễ nhầm lẫn giữa car và van.

Sử dụng CVAT để kiểm tra bounding box, đối chiếu với quy tắc trong GUIDELINE_MINI.md.

Hoàn thành quá trình gán nhãn; cần tiếp tục kiểm tra các trường hợp mơ hồ.

Xuất nhãn YOLO

Ban đầu chương trình checker báo thiếu 50 file nhãn.

Xuất dữ liệu bằng định dạng Ultralytics YOLO Detection 1.0 và đưa các file TXT vào dataset/labels/train/.

Checker báo ĐẠT, 50/50 file nhãn hợp lệ.

Đánh giá Gold QC

Chương trình đánh giá yêu cầu đường dẫn student labels và gold labels; ban đầu chưa tìm thấy thư mục gold.

Kiểm tra cấu trúc thư mục và chạy lại evaluate_annotations.py với các tham số phù hợp.

Chạy được chương trình đánh giá, nhưng kết quả passed = false.

Rework annotation

Một số chỉ số chất lượng nhãn chưa đạt ngưỡng đánh giá.

Rà soát kết quả đánh giá và thực hiện bước rework.

F1 tăng từ 0.5859 lên 0.5882, nhưng kết quả vẫn chưa đạt.

Huấn luyện YOLO26

Quá trình huấn luyện trên Google Colab mất thời gian và có nhiều giai đoạn xử lý.

Theo dõi tiến trình train và đánh giá, kiểm tra thư mục kết quả.

Đã tạo checkpoint best.pt và các kết quả đánh giá.

Lưu kết quả Colab

Ban đầu chưa xác định được vị trí các file kết quả cần tải.

Kiểm tra thư mục outputs, tải day2_colab_outputs.zip và giải nén.

Đã đưa kết quả vào report/individual/assets/.

Quản lý GitHub

Ban đầu chạy lệnh Git ở thư mục không phải repository.

Chuyển Terminal về đúng thư mục dự án và kiểm tra git status, git log.

Đã kiểm tra được lịch sử commit và đồng bộ mã nguồn.

Nhận xét: Khó khăn lớn nhất là bảo đảm dữ liệu nhãn đúng cấu trúc YOLO và có chất lượng tốt. Việc checker báo 50/50 file hợp lệ chỉ xác nhận cấu trúc dữ liệu, không đồng nghĩa toàn bộ bounding box và class đều chính xác. Kết quả Gold QC cho thấy bộ nhãn vẫn cần được cải thiện.

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): phần nào đã có kết quả, phần nào còn thiếu hoặc chưa kiểm được. Không tự nhận hoàn thành bước chưa chạy.
Dựa trên các công việc đã thực hiện và kết quả thu được, tôi tự đánh giá tiến độ bài thực hành như sau:

Nội dung

Kết quả thực hiện

Tự đánh giá

Chuẩn bị repository GitHub

Đã fork repository và làm việc trên nhánh main.

Đã thực hiện

Xây dựng schema nhãn

Sử dụng 4 lớp car, truck, bus, van và ghi quy tắc trong GUIDELINE_MINI.md.

Đã thực hiện

Gán nhãn 50 ảnh train

Đã tạo 50 file nhãn YOLO.

Đã thực hiện

Kiểm tra cấu trúc nhãn

Checker xác nhận 50/50 file hợp lệ.

Đạt kiểm tra cấu trúc

Self-QC

Đã có bước rà soát annotation; cần xác nhận danh sách ảnh và lỗi cụ thể đã kiểm tra.

Cần bổ sung minh chứng

Gold QC

Đã chạy chương trình đánh giá với gold labels.

Chưa đạt ngưỡng QC

Rework

Có kết quả đánh giá trước và sau rework.

Đã thực hiện, cần cải thiện

Huấn luyện YOLO26

Đã chạy notebook trên Google Colab và tạo best.pt.

Đã thực hiện

Đánh giá mô hình

Đã tạo thư mục vehicle_eval và file model_eval_metrics.json.

Cần ghi đầy đủ chỉ số vào báo cáo

Lưu kết quả

Đã tải và giải nén kết quả Colab vào thư mục assets.

Đã thực hiện

Báo cáo cá nhân

Đã tổng hợp quá trình thực hiện, khó khăn và kết quả.

Đang hoàn thiện

Đánh giá kết quả Gold QC:

Precision sau rework: 0.5294.

Recall sau rework: 0.6616.

F1 sau rework: 0.5882.

Mean IoU sau rework: 0.7807.

Trạng thái đánh giá: passed = false.

Kết quả cho thấy chất lượng annotation đã có cải thiện nhẹ về Precision và F1 nhưng vẫn chưa đáp ứng đầy đủ tiêu chí Gold QC.

Điểm đã hoàn thành: Tạo bộ dữ liệu gán nhãn 50 ảnh, kiểm tra cấu trúc YOLO, chạy đánh giá Gold QC, thực hiện rework, huấn luyện mô hình YOLO26 và lưu kết quả thực nghiệm.

Điểm còn hạn chế: Chất lượng annotation chưa đạt ngưỡng QC; cần tiếp tục rà soát các đối tượng bị bỏ sót, nhầm lớp hoặc bounding box chưa chính xác. Đồng thời cần hoàn thiện số liệu đánh giá mô hình và minh chứng trực quan trong báo cáo.

Hướng cải thiện: Tiếp tục kiểm tra những ảnh có nhiều phương tiện, vật thể nhỏ hoặc bị che khuất; đối chiếu với guideline để sửa nhãn nhất quán. Sau khi điều chỉnh, chạy lại chương trình đánh giá để xác định mức độ cải thiện.

## 8. Công cụ AI và tài liệu tham khảo

Ghi công cụ đã dùng, dùng ở bước nào và phần đã tự kiểm tra; không dùng thì ghi rõ. Liệt kê nguồn ngoài repo được sử dụng. Không ghi mật khẩu/token hoặc đính kèm gold train.
8.1. Công cụ sử dụng

Công cụ

Mục đích sử dụng

CVAT

Gán nhãn bounding box cho 50 ảnh phương tiện và xuất nhãn YOLO.

Google Colab

Thực thi notebook Python, huấn luyện và đánh giá mô hình YOLO26.

Python

Kiểm tra cấu trúc dữ liệu nhãn, đánh giá annotation và xử lý kết quả.

Ultralytics YOLO

Huấn luyện mô hình Object Detection và đánh giá hiệu suất phát hiện phương tiện.

Visual Studio Code

Quản lý mã nguồn, chỉnh sửa notebook, file Markdown và kiểm tra kết quả.

Git và GitHub

Quản lý phiên bản, lưu trữ mã nguồn và kết quả bài thực hành.

ChatGPT

Hỗ trợ giải thích lỗi, hướng dẫn thao tác CVAT, GitHub, Colab và trình bày báo cáo.

Codex

Hỗ trợ chỉnh sửa nội dung báo cáo và tổng hợp kết quả đánh giá.

8.2. Phạm vi sử dụng AI và tự kiểm tra

Trong quá trình thực hiện, tôi sử dụng ChatGPT và Codex để hỗ trợ tìm hiểu công cụ, giải thích thông báo lỗi, hướng dẫn thao tác và xây dựng nội dung báo cáo.

Các kết quả thực nghiệm được đối chiếu với dữ liệu thực tế, bao gồm:

Kết quả kiểm tra 50/50 file nhãn từ check_yolo_labels.py.

Kết quả Gold QC từ outputs/annotation_evaluation.json.

Kết quả huấn luyện và đánh giá mô hình từ Google Colab.

Checkpoint best.pt và các file kết quả được xuất từ notebook.

Lịch sử commit và các file được lưu trữ trong GitHub.

AI được sử dụng với vai trò hỗ trợ, không thay thế cho việc kiểm tra chất lượng annotation và đánh giá mô hình. Những kết quả chưa đạt hoặc chưa được xác minh vẫn được ghi nhận trong báo cáo.

8.3. Tài liệu tham khảo

CVAT Documentation: https://docs.cvat.ai/ — Tài liệu hướng dẫn gán nhãn ảnh và xuất dữ liệu annotation.

Ultralytics Documentation: https://docs.ultralytics.com/ — Tài liệu về Object Detection, huấn luyện và đánh giá mô hình YOLO.

PyTorch Documentation: https://pytorch.org/docs/stable/ — Tài liệu thư viện Deep Learning PyTorch.

Google Colab: https://colab.research.google.com/ — Môi trường thực thi notebook Python.

Git Documentation: https://git-scm.com/doc — Tài liệu quản lý phiên bản mã nguồn.

Repository bài thực hành: https://github.com/23010769-sketch/K5-L2-LuongQuocViet2A202603127-Day02-ObjectDetection

8.4. Cam kết

Tôi trình bày các kết quả dựa trên quá trình thực hiện bài thực hành và các file dữ liệu đã tạo. Những hạn chế về chất lượng annotation và kết quả Gold QC được ghi nhận, không tự nhận đã đạt những tiêu chí chưa được chương trình đánh giá xác nhận.

Báo cáo không chứa mật khẩu, access token hoặc dữ liệu gold train đính kèm.