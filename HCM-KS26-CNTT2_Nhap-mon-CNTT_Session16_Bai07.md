Bài 7: Gỡ Lỗi Luồng Yêu Cầu Kết Quả Xét Nghiệm Và Sai Lệch Mã Trạng Thái HTTP

Phần 1:

4 điểm sai lệch:

* Sai lệch về vai trò của DNS

nhận định sai:Trình duyệt gửi trực tiếp URL tới Web Server mà không cần thông qua DNS để phân giải cổng mạng

* Sai lệch về quy trình thiết lập kết nối TCP/TLS

nhận định sai:Tải mã nguồn ngay lập tức mà không cần bắt tay TCP/TLS vì giao thức HTTPS tự động mở không điều kiện

* Hiểu sai lệch về mã phản hồi HTTP

nhận định sai: Máy chủ cơ sở dữ liệu sập nguồn khiến server trả về mã 404 Not Found, và kết luận với bệnh nhân rằng hồ sơ đã bị xóa vĩnh viễn

* Hiểu sai về việc thao tác DOM trên Tab Elements

nhận định sai:Gõ chữ trực tiếp vào thẻ <span> trên Chrome DevTools làm cho dữ liệu gốc trên Database máy chủ được sửa chuẩn xác thành công



Phân tích mã HTTP (500 vs 404) và hậu quả tâm lý đối với bệnh nhân:

\-Vì sao lỗi sập CSDL phải trả về mã 500 thay vì 404:

500 Internal Server Error: là việc máy chủ nhận A được yêu cầu (Request) từ trình duyệt, nhưng lỗi phía máy chủ server B bên kia bị như (quá tải tài nguyên ,mất kết nối cơ sở dữ liệu, lỗi code hệ thống backend) và trả về thông báo lỗi hệ thống 500 đến máy chủ A

\-Hậu quả tai hại đối với tâm lý bệnh nhân nếu báo sai thành 404 là:

* Bệnh nhân sẽ hoảng loạn và cú sốc tâm lý khi kết quả xét nghiệm máu ,hồ sơ bệnh bị biến mất vĩnh viễn ,làm mất lòng tin của người dùng về uy tín CarePulse
* Nguy cơ khiếu nại pháp lý về bảo mật dữ liệu của người dùng ,và về thông tin bị sai thì việc trấn an bệnh nhân rằng hệ thống đang lỗi thì Trưởng bộ phận chăm sóc khách hàng đã nhận bảng giải trình bị sai của bạn lập trình viên thực tập thì sẽ khiến CarePulse giải quyết vấn đề sẽ khó khăn hơn vì "thông báo sai sự thật" và sẽ phải chịu trách nhiệm pháp lý.

Phần 2: Sửa lỗi \& Hoàn thiện giải pháp:

Bước 1: DNS Lookup (Phân giải tên miền)

* gửi chuỗi tên miền carepulse.vn tới hệ thống máy chủ phân giải tên miền (DNS Server) để dịch thành địa chỉ IP chính xác của máy chủ CarePulse giúp xác định đúng nơi cần kết nối

Bước 2: TCP/TLS Handshake

* Thực hiện TCP 3-way Handshake để thiết lập kênh truyền an toàn giữa trình duyệt và máy chủ,TLS Handshake thì nhằm thỏa thuận mã hóa dữ liệu, đảm bảo an toàn cho thông tin y tế của bệnh nhân theo chuẩn bảo mật y tế

Bước 3: HTTP Request \& Response

* thông báo cho bệnh nhân rằng máy chủ CarePulse đang gặp sự cố kỹ thuật 500 Internal Server Error và tài liệu của bệnh nhân vẫn còn nguyên vẹn

Bước 4: Rendering Pipeline

Khi máy chủ khôi phục về mã 200 OK, với mã nguồn HTML/JSON, trình duyệt nhận được , phân tích xây dựng DOM Tree thì sẽ đưa kết quả xét nghiệm máu trực quan lên màn hình cho bệnh nhân

\-Giải thích hành động sửa chữ trên Tab Elements không có giá trị lưu trữ trên máy chủ CarePulse:

* phân tách giữa Client-side và Server-side: Client-side là nằm trên máy bệnh nhân Trình duyệt tải mã HTML/JS từ server về và lưu trữ cấu trúc giao diện dưới dạng DOM Tree nằm trong RAM máy của bệnh nhân Thao tác chỉnh sửa trên DevTools chỉ thay đổi trên máy của bệnh nhân không thể thay đổi được giao diện CarePulse (Server-side) là nằm hệ thống lưu trữ chuyên dụng đặt tại trung tâm dữ liệu của CarePulse.
* sửa chữ trên Tab Elements không có giá trị vì nó tạo ra một cấu trúc cây là DOM và lưu trữ bản sao cấu trúc đó trực tiếp trên bộ nhớ RAM tạm thời của máy tính cá nhân không phải của CarePulse và khi sửa trên Tab Elements thì chỉ thay đổi trên máy của người đó không thay đổi được máy chủ CarePulse.



