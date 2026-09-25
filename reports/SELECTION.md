# Vì sao chọn lô này?

Trong 50 dòng đứng đầu `outputs/selection_round1.csv`, chọn năm frame bạn sẽ ưu tiên nếu chỉ có
ngân sách rà năm ảnh. Ghi tên, điểm, thời điểm, thứ tự và lý do; tối thiểu một quyết định phải xét
ảnh gần trùng hoặc trường hợp model không dự đoán được box: Nếu chỉ được rà 5 ảnh, tôi chọn frame_0182.jpg (hạng 1, điểm 0.9591), frame_0369.jpg (hạng 2, điểm 0.9324), frame_0380.jpg (hạng 3, điểm 0.9170), frame_0326.jpg (hạng 4, điểm 0.9155) và frame_0331.jpg (hạng 5, điểm 0.9154). Đây là năm ảnh đứng đầu theo điểm. Tôi cũng lưu ý frame_0372.jpg (hạng 6) chỉ cách frame_0369.jpg 1,2 giây và có cảnh giao thông gần giống; vì vậy, nếu phải giới hạn số ảnh, tôi ưu tiên lấy thêm một cảnh khác.

Ba frame thuộc lô 12 ảnh model chọn và bằng chứng trong CSV/ảnh contact sheet: Trong 12 ảnh AI đã chọn, tôi xem frame_0182.jpg, frame_0369.jpg và frame_0099.jpg. Contact sheet cho thấy cả ba là cảnh giao thông ban đêm với nhiều xe. CSV ghi lần lượt 28, 43 và 29 box, cùng 18, 16 và 14 box không chắc chắn; đây là những ảnh đáng rà vì có nhiều đối tượng và nhiều dự đoán cần xem lại.

Một frame có điểm cao nhưng không chọn hoặc một frame có điểm thấp vẫn nên xem, và lý do: frame_0372.jpg có điểm 0.9101, cao hơn một số ảnh được chọn, nhưng không nằm trong 12 ảnh AI đưa ra. Ảnh này cách frame_0369.jpg chỉ 1,2 giây và cho thấy cảnh đường cùng luồng xe gần như tương tự, nên rà thêm có thể lặp lại công sức mà cung cấp ít cảnh mới.


Điều phép chọn này chưa chứng minh về chất lượng mô hình: 
Điểm số và số box chưa chắc chắn giúp giải thích vì sao các ảnh được đưa ra để rà, nhưng không chứng minh rằng sửa chúng sẽ làm mô hình nhận diện xe tốt hơn trên dữ liệu khác. Muốn kết luận về chất lượng mô hình cần đánh giá trên dữ liệu và tiêu chí phù hợp.
