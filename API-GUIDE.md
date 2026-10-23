# Bản 0.3 — chế độ gửi request API

Chế độ này dành cho cả lần đăng ký đầu tiên, không yêu cầu đăng ký mẫu hay lịch sử đăng ký. Đã triển khai theo mã JavaScript công khai của trường và kiểm tra bằng API giả lập. **Chưa gửi đăng ký thật và chưa xác minh tương thích với phản hồi tài khoản thực tế.** Không bảo đảm giành được chỗ hoặc hoạt động khi máy chủ ngừng phục vụ.

## Cài / cập nhật

Giải nén ZIP v0.3. Trong `chrome://extensions` hoặc `edge://extensions`, bật Developer mode, Load unpacked và chọn thư mục `extension`. Nếu đã cài thư mục này, bấm Reload tiện ích rồi tải lại trang trường. Bản mới cần quyền truy cập `dangkyapi.hcmute.edu.vn` để gọi API trường. Không cần cài Node khi sử dụng.

## Chuẩn bị trước ngày mở

1. Tự đăng nhập tại `https://dkmh.hcmute.edu.vn/Student/RegistScheduleUnit`.
2. Mở mục **⚡ Đăng ký bằng API · dùng từ lần đầu** ở đầu bảng tool. Đây là kế hoạch riêng, không dùng các ô mã lớp của chế độ DOM phía dưới.
3. Bấm **Tải chương trình từ API**, chọn đúng chương trình và kiểu đăng ký KH/NKH/NCT được trường cho phép.
4. Nhập tên học kỳ/đợt và kế hoạch theo định dạng bên dưới. Chuẩn bị mã môn và mã lớp đầy đủ từ danh sách/tra cứu của trường; tool không tự chọn ngẫu nhiên lớp TH/BT còn thiếu.
5. Bấm **Xem trước API** khi hệ thống cho phép đọc danh sách, để kiểm tra lớp và các chuỗi phụ thuộc. Xem trước không đăng ký. Trước khi mở, mục này có thể báo đợt chưa mở.
6. Bấm **Chạy API**. Nếu đợt chưa mở, tool chờ và kiểm tra lại theo chu kỳ. Để `Dừng sau = 0` nếu muốn chờ đến khi hoàn tất hoặc bấm Dừng. Giới hạn phút bao gồm cả thời gian chờ mở đợt.

Giữ tab mở và máy thức. Không cần chọn bảng/nút như chế độ DOM. Tool không tự tiếp tục khi tải lại tab/đóng trình duyệt. Nếu hết đăng nhập, tự đăng nhập lại rồi bấm Chạy; các lần gửi trước sẽ được đối soát. Không đăng ký thủ công song song hoặc chạy trên hồ sơ trình duyệt/máy khác trong lúc tool đang xử lý.

## Định dạng kế hoạch API

```text
MATH|MATH_LT01+MATH_TH01 > MATH_LT02+MATH_TH02
PHY|PHY_01
LAB|LAB_LT01+LAB_TH01+LAB_BT01
```

Đây là mã giả minh họa. Mỗi dòng có dạng **Mã môn|Phương án 1 > Phương án 2**. Trong mỗi phương án, nối các lớp phụ thuộc bằng `+`, theo thứ tự lớp gốc → thực hành → bài tập. Mỗi môn chỉ xuất hiện một dòng. Một lớp không có lớp con chỉ cần một mã. Các phương án phải là chuỗi cha–con hợp lệ do API trả về. Lớp TH/BT bắt buộc phải được chỉ định, không tự suy đoán. Có thể dùng dòng bắt đầu `#` làm ghi chú.

Tool lấy các đối tượng lớp gốc từ API làm payload. Cả chuỗi LT/TH/BT được gửi trong một request cho môn đó. Các môn độc lập được xử lý tuần tự để đối soát điều kiện và hạn chế lỗi 429; chưa hỗ trợ gửi nhiều môn độc lập song song. Lớp ưu tiên đầy thì xét phương án kế tiếp. Nếu mọi phương án đầy hoặc xung đột, tiếp tục chờ vòng sau. Có thể bấm Dừng để chỉnh kế hoạch.

## Khi web lag / lỗi mạng

- Request đọc bị lỗi tạm thời hoặc 429: thử lại tối đa 4 lần sau lần đầu với thời gian chờ tăng dần; tôn trọng Retry-After, tối đa chờ 60 giây mỗi lượt đọc. Nếu vẫn lỗi thì dừng và báo.
- Đã gửi đăng ký nhưng mất phản hồi: không gửi lại request đăng ký. Đọc bảng kết quả API để đối soát; nếu đã có đầy đủ lớp thì ghi thành công.
- Chưa xác minh được: lưu `pending`/`uncertain`, dừng cả kế hoạch và chặn gửi lại cho môn đó ở lần chạy sau. Nếu lớp xuất hiện sau đó, chạy lại sẽ tự nhận diện thành công. Không có nút xóa nhật ký API trên giao diện để tránh vô tình gửi lặp; nếu kết quả mãi chưa rõ, cần kiểm tra với hệ thống trường trước khi xử lý nhật ký.
- Máy chủ sập hoàn toàn: không có cách gửi thành công cho đến khi máy chủ hoạt động lại. API tách biệt giúp không phụ thuộc việc bấm nút trên giao diện, nhưng vẫn phụ thuộc máy chủ API.

## Phiên đăng nhập và giới hạn

Tiện ích đọc token từ phiên đăng nhập trên trang trường và gửi trực tiếp tới đúng API trường. Không lưu token trong cấu hình, không xuất token vào kế hoạch/log. Nhật ký tách theo giá trị băm của ID tài khoản, chương trình, ID đợt và tên đợt do người dùng nhập. Đổi tài khoản khi đang chạy sẽ làm tool dừng. API worker chỉ cho phép các thao tác định sẵn; không có thao tác hủy, chuyển nhóm hoặc gọi URL tùy ý trong chế độ API 0.3.

Nếu cấu trúc phản hồi thay đổi, thiếu dữ liệu, thiếu điều kiện LT/TH/BT, chưa được phép đăng ký hoặc gặp xung đột, tool dừng/chờ thay vì bỏ qua kiểm tra của trường. Chuyển nhóm vẫn dùng chế độ DOM v0.2 bên dưới khi đã ánh xạ nút chuyên biệt.
