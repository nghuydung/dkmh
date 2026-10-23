# Tool đăng ký học phần HCMUTE

**Mới: v0.3 có chế độ API không cần đăng ký mẫu hoặc ánh xạ DOM.** Xem [hướng dẫn API v0.3](API-GUIDE.md). Chế độ DOM v0.2 vẫn giữ lại bên dưới. Cả hai chưa được kiểm thử đăng ký thật trên hệ thống trường.

Tiện ích Chrome/Edge để nhập nhiều **mã lớp học phần (LHP)**, đăng ký lần lượt và tiếp tục chờ lớp có chỗ. Mã được xử lý theo thứ tự nhập. Mã đã có trong bảng đã đăng ký sẽ được bỏ qua.

**Trạng thái:** bản 0.2, tham khảo video người dùng cung cấp, đã khảo sát giao diện nhưng chưa kiểm thử đăng ký trên hệ thống trường vì chưa có bảng lớp trong phiên khảo sát. Khi trường mở đăng ký, cần chọn đúng bảng và nút trên trang trước khi dùng. Không bảo đảm giữ được chỗ hoặc vượt qua điều kiện đăng ký của trường.

## Cài đặt

1. Mở `chrome://extensions` (Edge: `edge://extensions`).
2. Bật **Developer mode / Chế độ dành cho nhà phát triển**.
3. Chọn **Load unpacked / Tải tiện ích đã giải nén** → chọn thư mục **extension** trong dự án này. Nếu dùng file ZIP, giải nén trước.
4. Tải lại trang `https://dkmh.hcmute.edu.vn/Student/RegistScheduleUnit`. Bảng điều khiển xuất hiện góc phải. Dùng nút `−` để thu gọn.

Không cần Node, Python, API key hay cài thư viện để sử dụng. Tiện ích chỉ được nạp trên các trang `/Student/*` thuộc `dkmh.hcmute.edu.vn`, dùng quyền `storage` để lưu cấu hình và nhật ký cục bộ.

## Thiết lập khi trường mở đăng ký

Tự đăng nhập bằng trình duyệt. Mở trang Đăng ký học phần, chọn chương trình đào tạo và tải danh sách bình thường. Sau đó:

1. Trong **Thiết lập giao diện**, bấm **Chọn bảng lớp đang mở**, rồi bấm vào bảng trên trang.
2. Chọn **bảng đã đăng ký** tương tự. Bảng này dùng để xác minh mã đã đăng ký thành công, phải khác bảng lớp mở và phải chứa đầy đủ kết quả cần đối soát. Nếu bị phân trang/lọc, tăng số dòng hoặc bỏ bộ lọc trước.
3. Chọn **nút Đăng ký trong một dòng lớp**. Thao tác chọn bị tiện ích chặn, không gửi đăng ký. Có thể chọn nút trực tiếp ở từng dòng hoặc checkbox; nếu chọn checkbox, ánh xạ thêm nút Đăng ký chung.
4. Kiểm tra số thứ tự cột **Mã LHP** của mỗi bảng (tính từ 1). Giá trị mặc định là 2, lấy từ bảng tra cứu đã khảo sát; bảng đăng ký thực tế có thể khác.
5. Nếu có cột **số chỗ còn lại** là số nguyên, nhập thứ tự cột đó. Nếu không có, để **0**. Không dùng cột “Giới hạn”, sĩ số tối đa hoặc tỷ lệ kiểu `30/40`.
6. Chọn nút tải lại danh sách nếu cần. Mặc định tool tìm nút có chữ chính xác **Làm mới**.
7. Chỉ thiết lập tìm từng mã nếu ngay trang ĐĂNG KÝ có ô tìm theo **mã lớp học phần đầy đủ**. Chọn cả ô nhập và nút tìm. Tool không tự đổi loại bộ lọc và không dùng trang Tra cứu để đăng ký.

Nếu giao diện bị thay đổi, chọn lại thành phần. Tool kiểm tra tiêu đề bảng, mã lớp đầy đủ và nhãn nút trước khi bấm; không đoán nút hoặc API chưa được xác minh.

## Chạy hàng loạt và chờ chỗ

1. Nhập **học kỳ / đợt đăng ký**, ví dụ `HK2 2026–2027 · đợt 1`. Dùng tên khác cho đợt mới để tránh lẫn nhật ký. Cấu hình và nhật ký thuộc hồ sơ trình duyệt này; nếu đổi tài khoản, hãy dùng đợt có tên riêng cho tài khoản đó.
2. Dán mã LHP, mỗi mã một dòng. Cũng chấp nhận dấu phẩy/chấm phẩy. Đây phải là mã **lớp cụ thể**, không phải mã môn. Mã ví dụ trong bản mô phỏng không phải mã thật của trường.
3. Chu kỳ mặc định **30 giây**, nhỏ nhất 15 giây. `Dừng sau = 0` nghĩa là chờ đến khi xử lý hết hoặc bạn bấm Dừng. Có thể nhập giới hạn phút.
4. Hẹn giờ nếu cần; giờ nhập được hiểu là **giờ Việt Nam UTC+7**. Hẹn giờ chỉ có tác dụng sau khi bấm Bắt đầu, cấu hình đã hợp lệ và tab còn mở. Lúc chưa có bảng lớp, chưa thể bật hẹn giờ.
5. Bấm **Xem trước** để kiểm tra mã khớp đúng lớp và trạng thái. Xem trước không gửi đăng ký.
6. Bấm **Bắt đầu tự đăng ký**. Tool tự bấm theo danh sách, mỗi lần chỉ gửi một lớp; lớp đầy hoặc nút bị khóa sẽ chờ vòng sau. Nút Dừng luôn sẵn dùng.

Giữ tab mở, máy thức và mạng ổn định. Không thao tác đăng ký thủ công/song song trong khi tool đang chạy. Không có tiến trình chạy khi tắt trình duyệt, máy ngủ hoặc tải lại trang; sau khi tải lại phải bấm Bắt đầu lại. Trình duyệt có thể làm chậm bộ đếm ở tab nền, nên hẹn giờ không bảo đảm chính xác tới giây.

## Cách xử lý kết quả

- **Đã có mã trong bảng đã đăng ký:** xác nhận hoàn tất, không bấm lại.
- **Còn 0 chỗ / nút bị khóa / thông báo mới báo lớp đầy:** giữ trong danh sách chờ, thử ở vòng sau.
- **Không tìm thấy mã ở bảng hiện tại:** tiếp tục chờ. Tool chỉ đọc các dòng đang có trong DOM; không tự chuyển trang hoặc cuộn bảng ảo. Có thể ánh xạ bảng môn cha/nút mở lớp để mở từng môn. Cần hiển thị lớp hoặc cấu hình tìm mã phù hợp.
- **Trùng lịch, tiên quyết, vượt tín chỉ:** đánh dấu cần xử lý, không lặp yêu cầu lớp đó. Các mã khác vẫn tiếp tục.
- **Không rõ kết quả sau 20 giây / xuất hiện hộp thoại / mất đăng nhập:** dừng. Không tự xác nhận hộp thoại. Nếu yêu cầu đã gửi, nhật ký giữ `uncertain` và chặn gửi lại. Thông báo “thành công” chung không thay thế việc đối soát mã trong bảng đã đăng ký.
- **Bấm Dừng sau khi đã gửi:** không thể hủy yêu cầu đang chạy trên máy chủ. Kiểm tra bảng rồi bấm **Đối soát bảng đã đăng ký**. Chỉ xóa nhật ký đợt nếu đã chắc chắn không còn yêu cầu đang xử lý và mã chưa đăng ký.

Nhật ký gửi được lưu trước khi bấm; Web Locks ngăn hai tab cùng trình duyệt/origin chạy đồng thời. Nếu trình duyệt không hỗ trợ Web Locks hoặc không ghi được kho lưu, tool không bắt đầu gửi. Tool không đọc/lưu mật khẩu, cookie, token; không có máy chủ nhận dữ liệu, không tự hủy học phần và không vượt CAPTCHA/điều kiện học vụ. Thông báo lỗi mới chỉ được nhận diện từ các vùng alert/toast DOM phổ biến; thông báo khác sẽ dẫn tới dừng vì chưa rõ kết quả.


## Chức năng bổ sung theo video (0.2)

- Chọn chương trình đào tạo và kiểu đăng ký: theo kế hoạch, ngoài kế hoạch, ngoài chương trình. Để trống chương trình và chọn “Giữ kiểu đang chọn” nếu muốn thao tác trực tiếp trên trang trường. Nếu nhập chương trình, cần ánh xạ bộ chọn chương trình trong Thiết lập giao diện.
- Đọc bảng môn cha → mở môn → đọc bảng lớp. Cần ánh xạ bảng môn cha/nút mở lớp và đúng cột mã môn. Khi chạy theo luồng này, mỗi lớp cần có mã môn trong định dạng bên dưới.
- Bảng chọn lớp có lọc mã/giảng viên/lịch học. Chọn nhiều lớp cùng môn để tạo danh sách thay thế theo thứ tự chọn.
- Bảng theo dõi từng nhóm, trạng thái từng lớp, số lần gửi. Có nút đổi thứ tự nhóm và bỏ nhóm khỏi kế hoạch cục bộ khi dừng; bỏ khỏi kế hoạch không hủy đăng ký trên trường.
- Nạp/lưu kế hoạch JSON hoặc nạp TXT, xuất kết quả CSV. File kế hoạch không chứa cookie, mật khẩu hoặc ánh xạ giao diện.

### Nhập danh sách như video

```text
MATH|*|*|MATH_01|LT
MATH|GV B|Thứ 3*|MATH_02|LT
MATH|*|*|MATH_TH01|TH
```

Các mã trên là dữ liệu giả. Hai dòng đầu cùng mã môn và loại LT nên là hai lựa chọn thay thế: thử MATH_01 trước, nếu đầy thì thử MATH_02; chỉ cần một lớp thành công. Dòng TH là yêu cầu riêng. Bỏ cột thứ 5 thì mặc định LT. `*` hoặc bỏ trống giảng viên/lịch nghĩa là không lọc trường đó. Đối chiếu giảng viên/lịch dùng văn bản trong cột tương ứng của bảng trường; nếu không có cột để kiểm tra, tool dừng yêu cầu sửa đầu vào.

Cách ngắn chỉ dùng mã lớp:

```text
MATH_01 > MATH_02
PHY_01
```

Dòng đầu chọn một trong hai lớp, ưu tiên bên trái. Dòng sau là một yêu cầu độc lập. Nếu nhập mỗi mã một dòng, tool đăng ký tất cả các mã. Không đưa cùng mã lớp vào hai nhóm khác nhau.

### Chuyển nhóm

Nếu trang trường cung cấp chức năng Chuyển nhóm chuyên biệt, ánh xạ nút Chuyển nhóm tại dòng lớp cũ và nút chuyển vào lớp đích tại bảng lớp. Thêm mã lớp cũ ở cột thứ 6:

```text
MATH|*|*|MATH_NEW|LT|MATH_OLD
```

Tool chỉ xác nhận hoàn tất khi lớp mới có trong bảng đã đăng ký và lớp cũ không còn. Không tự hủy lớp cũ để đăng ký lại. Chưa hỗ trợ chuyển nhóm qua checkbox/nút gửi chung hoặc hộp thoại xác nhận bổ sung; nếu xuất hiện hộp thoại xác nhận, tool dừng để xử lý thủ công.

### Giới hạn cần kiểm tra khi mở đăng ký

Video minh họa một phiên bản website cũ. Bộ điều khiển DOM hiện tại cần được chọn và kiểm tra trên giao diện thực tế. Chỉ đọc các dòng có sẵn trong DOM, không tự gọi API chưa xác minh, không tải toàn bộ danh mục ngoài chương trình từ máy chủ. Muốn chọn môn ngoài chương trình, dùng tìm kiếm trên trang trường để hiển thị kết quả trước rồi đọc bảng. Nếu LT/TH cần gửi cùng một giao dịch hoặc bảng lớp thay đổi cấu trúc theo môn, cần điều chỉnh bộ điều khiển sau khi kiểm tra thực tế. Không mô phỏng giới hạn VIP/trả phí của dịch vụ trong video và không chuyển cookie sang dịch vụ đó.

## Kiểm tra bằng dữ liệu giả lập

Nhà phát triển có thể dùng Node 18+:

```text
npm test
npm run demo
```

Mở `http://127.0.0.1:8765/tests/fixture.html`, bấm **Chạy kiểm thử tích hợp**. Trang này chạy cùng mã nguồn của tool với kho lưu mô phỏng riêng. Không truy cập hệ thống trường. Không cần cài dependency. Dừng server bằng Ctrl+C.

## Ghi nhận khảo sát

Xem [RESEARCH.md](RESEARCH.md) để biết thành phần đã quan sát và phần còn cần xác minh khi mở đăng ký.
