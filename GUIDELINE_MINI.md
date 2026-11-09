# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` |Ô tô con dùng để chở người|Sedan, SUV, taxi, pickup|
| `truck` | Xe tải chuyên vận chuyển hàng hóa| Xe tải thùng, xe ben, đầu kéo|
| `bus` |Xe chở nhiều hành khách|Xe buýt, xe khách, minibus|
| `van` |Xe có thân dạng hộp, chở người hoặc hàng|Xe van, minivan, xe giao hàng nhỏ|

## Quy tắc chung

- Box bám sát vật thể nghĩa là:Vẽ khung chữ nhật ôm sát phần nhìn thấy của phương tiện, hạn chế lấy nền thừa.
- Vật thể bị che khuất:Chỉ vẽ box bao quanh phần nhìn thấy, không tự ước lượng phần bị che.
- Vật thể bị cắt bởi rìa ảnh:Vẽ box đến sát mép ảnh, chỉ bao quanh phần phương tiện còn nhìn thấy.
- Vật thể rất nhỏ/mơ hồ:Chỉ gán nhãn khi có thể xác định tương đối chắc chắn lớp phương tiện, không đoán nhãn.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_...` |Một xe có thân cao, dạng hộp, khó phân biệt với SUV|Nếu xe có kiểu dáng SUV thì gán car. Nếu có kiểu dáng xe van thì gán van |Dựa vào thiết kế và hình dạng tổng thể của phương tiện|
| `drive_...` |Một xe tải bị phương tiện khác che mất một phần|Gán truck và vẽ bounding box quanh phần nhìn thấy|Tránh tự ước lượng phần không xuất hiện trong ảnh|
| `drive_...` |Một phương tiện ở rất xa, không nhìn rõ loại xe|Không gán nhãn nếu không thể xác định chắc chắn lớp|Tránh tạo nhãn sai ảnh hưởng đến chất lượng dữ liệu|
