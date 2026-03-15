Trong lộ trình Backend, nếu Database là "linh hồn" thì HTTP/HTTPS chính là "mạch máu". Đây là giao thức giúp Client (Trình duyệt, Mobile App) giao tiếp được với Server ASP.NET của bạn.
Dưới đây là những kiến thức cốt lõi bạn cần nắm vững.
1. Mô hình Request - Response
Mọi giao tiếp trên Web đều bắt đầu bằng một Request (Yêu cầu) từ Client và kết thúc bằng một Response (Phản hồi) từ Server.
A. Cấu trúc một HTTP Request
Khi bạn gọi một API trong ASP.NET, Client sẽ gửi:
 * URL (Endpoint): Địa chỉ tài nguyên (vídụ: /api/products).
 * Method (Động từ): Hành động muốn thực hiện (GET, POST, PUT, DELETE).
 * Headers: Thông tin bổ sung (Kiểu dữ liệu Content-Type, token xác thực Authorization).
 * Body: Dữ liệu gửi lên (thường là JSON khi dùng POST/PUT).
B. Cấu trúc một HTTP Response
Server xử lý xong sẽ trả về:
 * Status Code: Mã trạng thái (Thành công, Lỗi, Không tìm thấy).
 * Headers: Thông tin server, kiểu dữ liệu trả về.
 * Body: Dữ liệu kết quả (Danh sách sản phẩm, thông báo lỗi).
2. Các HTTP Methods phổ biến (CRUD)
Dân Backend ASP.NET Core dùng các động từ này để định nghĩa hành động cho API:

| Method | Ý nghĩa | Hành động tương ứng |
|---|---|---|
| GET | Lấy dữ liệu | Đọc (Read) |
| POST | Tạo mới dữ liệu | Thêm (Create) |
| PUT | Cập nhật toàn bộ dữ liệu | Sửa (Update) |
| PATCH | Cập nhật một phần dữ liệu | Sửa (Update) |
| DELETE | Xóa dữ liệu | Xóa (Delete) |

3. HTTP Status Codes (Mã trạng thái)
Bạn phải thuộc lòng các nhóm mã này để "nói chuyện" với Frontend:
 * 2xx (Thành công): 200 OK, 201 Created.
 * 3xx (Điều hướng): 301 Moved Permanently, 304 Not Modified.
 * 4xx (Lỗi phía Client): 400 Bad Request (Dữ liệu sai), 401 Unauthorized (Chưa đăng nhập), 403 Forbidden (Không có quyền), 404 Not Found.
 * 5xx (Lỗi phía Server): 500 Internal Server Error (Code C# của bạn bị crash).
4. Sự khác biệt giữa HTTP và HTTPS
Đây là câu hỏi phỏng vấn cực kỳ phổ biến:
 * S (Secure): HTTPS là HTTP cộng thêm một lớp bảo mật gọi là SSL/TLS.
 * Mã hóa: HTTP gửi dữ liệu dưới dạng văn bản thuần túy (Plain text). Nếu bạn gửi mật khẩu qua HTTP, hacker có thể "ngửi" thấy dễ dàng. HTTPS mã hóa dữ liệu đó trước khi gửi.
 * Cổng kết nối (Port): HTTP dùng cổng 80, HTTPS dùng cổng 443.
 * Chứng chỉ: HTTPS yêu cầu server phải có chứng chỉ số (SSL Certificate).
5. Áp dụng trong ASP.NET Core
Khi bạn code Backend với ASP.NET, bạn sẽ gặp các khái niệm này dưới dạng:
 * Attributes: [HttpGet], [HttpPost], [Route].
 * Middleware: app.UseHttpsRedirection(); (Tự động chuyển hướng từ http sang https).
 * Status Codes: Trả về return Ok(data); hoặc return BadRequest();.
6. Tại sao kiến thức này quan trọng với bạn?
 * Gỡ lỗi (Debug): Khi API không chạy, bạn nhìn vào Status Code để biết lỗi do mình (5xx) hay do Frontend gửi sai (4xx).
 * Bảo mật: Bạn sẽ hiểu tại sao cần dùng Token (JWT) trong Header để bảo mật API.
 * Hiệu suất: Hiểu về HTTP giúp bạn tối ưu hóa việc truyền tải dữ liệu (ví dụ dùng nén Gzip hoặc HTTP/2, HTTP/3).
#### Để hiểu cách một HTTP Request "du hành" trong ASP.NET Core, bạn cần nắm được khái niệm Middleware và Request Pipeline. Hãy tưởng tượng nó giống như một dây chuyền sản xuất trong nhà máy: sản phẩm (Request) đi qua từng trạm, mỗi trạm làm một nhiệm vụ xong mới chuyển cho trạm kế tiếp.
1. Request Pipeline & Middleware là gì?
Middleware là những đoạn code nhỏ nằm giữa Client và Controller. Khi một Request gửi đến, nó phải đi xuyên qua một "đường ống" chứa các Middleware này theo thứ tự.
Quy trình diễn ra như sau:
 * Client gửi một Request (ví dụ: GET /api/products).
 * Kestrel Server (Web server tí hon của .NET) tiếp nhận Request.
 * Request đi qua các Middleware (Chiều đi):
   * Logging: Ghi lại thời gian Request đến.
   * HTTPS Redirection: Kiểm tra nếu là HTTP thì bắt chuyển sang HTTPS.
   * Authentication: Kiểm tra xem ông này là ai? Có đăng nhập chưa?
   * Authorization: Ông này có quyền vào xem danh sách sản phẩm không?
 * Routing & Endpoint: Sau khi qua hết các "cửa khẩu", hệ thống xác định xem Request này thuộc về Controller nào (ví dụ: ProductController).
 * Controller xử lý: Code C# của bạn chạy, lấy dữ liệu từ Database và trả về kết quả.
 * Response đi ngược lại qua các Middleware (Chiều về): Các Middleware có thể chỉnh sửa Response trước khi nó tới tay Client.
2. Sơ đồ luồng (Pipeline Diagram)

Client  --->  [ Middleware 1 ]  --->  [ Middleware 2 ]  --->  [ Controller ]
                                                                    |
                                                                (Xử lý xong)
                                                                    |
Client  <---  [ Middleware 1 ]  <---  [ Middleware 2 ]  <---  [ Trả về kết quả ]

3. Các thành phần quan trọng trong ASP.NET Core Backend
A. File Program.cs
Đây là nơi bạn "lắp ráp" dây chuyền Middleware. Thứ tự sắp xếp cực kỳ quan trọng:
 * app.UseAuthentication(); (Xác thực) phải nằm trước app.UseAuthorization(); (Phân quyền). Vì bạn phải biết người đó là ai trước khi quyết định họ có quyền làm gì.
B. Dependency Injection (DI)
Trong lúc xử lý Request, ASP.NET Core sử dụng DI để cung cấp các dịch vụ cần thiết (như Database Context, Email Service).
 * Có 3 loại vòng đời (Lifetime):
   * Transient: Tạo mới mỗi lần gọi.
   * Scoped: Tạo một lần cho mỗi Request (Dân Backend dùng cái này nhiều nhất cho Database).
   * Singleton: Tạo một lần duy nhất từ lúc bật server đến lúc tắt.
4. Tại sao bạn cần biết điều này?
 * Xử lý lỗi tập trung: Bạn có thể viết một Middleware chuyên đi "bắt lỗi" (Exception Handling). Thay vì viết try-catch ở mọi Controller, bạn chỉ cần một Middleware ở đầu ống để bắt mọi lỗi phát sinh trong dự án.
 * Bảo mật: Bạn chặn những Request xấu ngay từ "cửa ngõ" Middleware thay vì để nó đi sâu vào làm tốn tài nguyên xử lý của Controller.
 * Tối ưu hóa: Bạn có thể thêm Middleware nén dữ liệu (Response Compression) để API trả về nhanh hơn.
