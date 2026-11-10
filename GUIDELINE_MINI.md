# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe ô tô con dùng để chở người, thường có 4 bánh, kích thước nhỏ hoặc trung bình. | Sedan, hatchback, SUV, crossover. Phân biệt với `van` dựa vào hình dáng thân xe và không gian khoang chở người/hàng. |
| `truck` | Xe tải được thiết kế chủ yếu để vận chuyển hàng hóa, có thùng hàng hoặc kết cấu chuyên chở hàng. | Xe tải thùng, xe ben, xe đầu kéo. Phân biệt với `bus` vì chức năng chính là chở hàng thay vì chở nhiều hành khách. |
| `bus` | Xe khách hoặc xe buýt có kích thước lớn, được thiết kế để vận chuyển nhiều hành khách. | Xe buýt đô thị, xe khách đường dài. Thường có thân xe dài, nhiều cửa sổ và nhiều hàng ghế. |
| `van` | Xe dạng van có thân tương đối cao, khoang chở người hoặc hàng rộng, thường có thiết kế thân liền khối. | Xe van chở hàng, xe van chở khách cỡ nhỏ. Phân biệt với `car` bằng hình dạng thân xe và khoang chứa; phân biệt với `bus` bằng kích thước và thiết kế. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Bounding box phải bao quanh sát phần vật thể nhìn thấy được, hạn chế chứa nền không liên quan. Sử dụng hình chữ nhật với các cạnh bám theo điểm ngoài cùng của vật thể, không vẽ quá rộng hoặc quá nhỏ làm mất một phần vật thể.
- Vật thể bị che khuất: Vẫn gán nhãn nếu có thể nhận diện được lớp của vật thể. Đánh dấu `occluded` khi vật thể bị một vật thể khác che khuất một phần. Bounding box bao quanh phần nhìn thấy được, không tự suy đoán phần bị che khuất nếu guideline không yêu cầu.
- Vật thể bị cắt bởi rìa ảnh: Vẫn gán nhãn nếu có đủ thông tin để xác định lớp. Bounding box chỉ nằm trong phạm vi ảnh và bám sát phần vật thể nhìn thấy được. Không đánh dấu `occluded` chỉ vì vật thể bị cắt bởi rìa ảnh; sử dụng `hidden` nếu schema quy định thuộc tính này cho trường hợp vật thể không hiển thị đầy đủ do nằm ngoài khung hình.
- Vật thể rất nhỏ/mơ hồ: Chỉ gán nhãn khi có thể xác định tương đối chắc chắn vật thể thuộc một trong bốn lớp `car`, `truck`, `bus`, `van`. Với vật thể quá nhỏ, bị mờ hoặc không đủ đặc điểm phân biệt, không tự suy đoán lớp. Cần phóng to ảnh để kiểm tra trước khi quyết định gán nhãn hoặc bỏ qua.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_...` | Một ô tô nằm ở mép ảnh, chỉ nhìn thấy khoảng một nửa thân xe. | Gán nhãn `car`, vẽ bounding box quanh phần xe nhìn thấy trong ảnh. Đánh dấu `hidden` nếu schema sử dụng thuộc tính này cho vật thể bị cắt bởi rìa ảnh. | Vẫn nhận diện được lớp `car` dù xe không xuất hiện đầy đủ. Phần còn lại nằm ngoài khung hình, không phải bị vật thể khác che khuất nên không tự động đánh dấu `occluded`. |
| `drive_...` | Một ô tô phía sau xe buýt bị che khuất phần lớn thân xe, chỉ nhìn thấy một phần đầu hoặc đuôi xe. | Gán nhãn `car` nếu đủ đặc điểm nhận diện, vẽ box quanh phần nhìn thấy và đánh dấu `occluded`. | Xe bị một vật thể khác che khuất nên thuộc trường hợp `occluded`. Chỉ gán nhãn khi xác định được lớp, không suy đoán kích thước hoặc phần thân xe bị che. |
| `drive_...` | Một phương tiện ở xa có kích thước rất nhỏ và hình ảnh mờ, khó phân biệt giữa `car` và `van`. | Phóng to để kiểm tra. Nếu vẫn không đủ đặc điểm nhận diện thì bỏ qua, không gán nhãn. | Schema yêu cầu sử dụng đúng một trong bốn lớp đã định nghĩa. Khi không đủ thông tin để phân biệt, việc đoán lớp có thể gây sai lệch dữ liệu và làm giảm chất lượng annotation. |