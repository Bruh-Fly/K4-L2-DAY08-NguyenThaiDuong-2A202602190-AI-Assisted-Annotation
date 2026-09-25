# Báo cáo Lab Ngày 08: Học chủ động cho bộ phát hiện xe

Họ và tên: Nguyễn Thái Dương

Công cụ gán nhãn đã dùng: CVAT

## 1. Dữ liệu và cách chia tập

Camera đứng yên nên một chiếc xe có thể xuất hiện trong nhiều ảnh liên tiếp. Vì vậy, tập học và tập kiểm thử cần cách nhau theo thời gian. Nếu chia ngẫu nhiên, ảnh của cùng một chiếc xe hoặc cùng một cảnh có thể xuất hiện ở cả hai tập. Khi đó AI đã thấy gần như cùng cảnh trong lúc học và điểm kiểm thử sẽ đẹp hơn khả năng thực tế.

## 2. Mô hình khởi đầu lạnh

| Vòng | Mô hình | Ảnh train | Box train | AP50 | Δ AP50 so cold start | P@0.25 | R@0.25 | F1 | R small | R medium | R large |
| ---: | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 0 | yolov8n cold start (COCO car+bus+truck) | 0 | 0 | 0.771 | — | 0.925 | 0.489 | 0.640 | 0.182 | 0.547 | 0.561 |

Trong ảnh `frame_150.jpg` ở `compare_round0.jpg`, mô hình cold start chỉ khớp 10 box, có 2 box sai và bỏ sót 10 box so với nhãn tham chiếu; một số xe trên đường không có box khớp. Recall theo kích thước cho thấy mô hình tìm được xe nhỏ kém hơn nhiều: 0.182, so với 0.547 ở xe vừa và 0.561 ở xe lớn. Tuy vậy, nhãn dùng để chấm cũng do máy tạo và chưa được người xem từng box, nên có thể nhãn tham chiếu sai chứ không phải mô hình sai.

## 3. Chiến lược chọn mẫu

Mỗi ảnh có một điểm gồm ba phần: một nửa điểm đến từ mức AI không chắc, ba phần mười đến từ số box AI còn lưỡng lự, và hai phần mười đến từ độ khác biệt về thời gian so với ảnh khác. Trong cùng một lô, hai ảnh phải cách nhau ít nhất 2 giây để tránh chọn nhiều ảnh gần như cùng một cảnh từ camera cố định. Trong `SELECTION.md`, tôi đã nêu `frame_0182.jpg`, `frame_0369.jpg` và `frame_0099.jpg` là các ảnh được chọn để xem; `frame_0372.jpg` là ảnh điểm cao nhưng gần trùng thời điểm và cảnh với `frame_0369.jpg`. Điểm cao không có nghĩa sửa ảnh đó sẽ làm AI giỏi hơn.

## 4. Các vòng học chủ động

| Vòng | Mô hình | Ảnh train | Box train | AP50 | Δ AP50 so cold start | P@0.25 | R@0.25 | F1 | R small | R medium | R large |
| ---: | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 0 | yolov8n cold start (COCO car+bus+truck) | 0 | 0 | 0.771 | — | 0.925 | 0.489 | 0.640 | 0.182 | 0.547 | 0.561 |
| 1 | yolov8n fine-tune vòng 1..1 | 12 | 335 | 0.746 | -0.025 | 0.988 | 0.206 | 0.341 | 0.000 | 0.199 | 0.585 |

Trong vòng 1, tôi giữ nguyên 162 box, chỉnh 3, xóa 4 và thêm 170 box. So với cold start, AP50 giảm từ 0.771 xuống 0.746, tức giảm 0.025. Ở `frame_150.jpg`, số box khớp giảm từ 10 xuống 4; số box sai giảm từ 2 xuống 0 nhưng số box bỏ sót tăng từ 10 lên 16. Kết quả trên ảnh này xấu đi dù mô hình không còn box sai.

Ba loại thông tin cần phân biệt: trước khi xem pre-label, tôi tự đếm 24 xe trong `frame_0099.jpg` và ghi hai vị trí AI có thể bỏ sót trong `BLIND_SCAN.md`; trong `REVIEW_LOG.csv`, tôi ghi việc thêm nhãn cho nhiều xe ở vùng tối của ảnh này; sau khi train, mô hình vòng 1 đạt AP50 0.746 và recall xe nhỏ bằng 0.000 trên tập kiểm thử. Đây là quan sát độc lập, thay đổi nhãn do tôi rà, và kết quả mô hình sau train; chúng không thay thế cho nhau.

## 5. Kết luận và giới hạn

Vòng 1 có AP50 thấp hơn cold start 0.025, nên tôi dừng train thêm để rà lại nhãn đã sửa và cách huấn luyện trước khi quyết định vòng tiếp theo. Hai trường hợp còn yếu là xe nhỏ ở xa, vì recall xe nhỏ của vòng 1 bằng 0.000, và xe bị cắt ở mép ảnh, nơi box có thể khó xác định đầy đủ thân xe. Rà thêm ảnh và sửa box tốn thời gian; cũng cần tránh chọn hai ảnh sát nhau vì chúng có thể gần như cùng một cảnh.

Kết quả kiểm thử chỉ dựa trên 20 ảnh; xe quá nhỏ bị bỏ qua, và nhãn tham chiếu chưa được người kiểm tra thủ công từng box. Vì vậy, điểm số chưa đủ để kết luận mô hình sẽ hoạt động tốt trên dữ liệu khác. Khi AP50 giảm, tôi sẽ kiểm tra lại các box đã giữ, chỉnh, xóa hoặc thêm trước khi cho AI học thêm.