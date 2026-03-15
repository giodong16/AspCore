Chào bạn, bước sang **ASP.NET Core MVC** là bạn đã chính thức bước vào việc xây dựng cấu trúc của một ứng dụng Web chuyên nghiệp. Đây là mô hình kinh điển nhưng vẫn cực kỳ mạnh mẽ để quản lý code Backend.

MVC là viết tắt của **Model - View - Controller**. Hãy tưởng tượng bạn đang quản lý một nhà hàng (hệ thống Backend của bạn):

---

## 1. Thành phần của MVC

### **Model (Dữ liệu)**

Đây là bộ phận quản lý dữ liệu và logic nghiệp vụ. Nó đại diện cho các bảng trong Database mà bạn vừa nghiên cứu.

* **Nhiệm vụ:** Lưu trữ, truy xuất và xử lý dữ liệu.
* **Ví dụ:** Một Class `Product.cs` chứa các thuộc tính `Id`, `Name`, `Price`.
* **Lưu ý:** Model không quan tâm giao diện trông như thế nào, nó chỉ quan tâm dữ liệu có đúng định dạng không.

### **View (Giao diện)**

Đây là những gì người dùng nhìn thấy trên màn hình. Trong ASP.NET Core, View sử dụng file `.cshtml` (kết hợp HTML và C# thông qua **Razor Engine**).

* **Nhiệm vụ:** Hiển thị dữ liệu từ Model gửi tới và nhận tương tác từ người dùng.
* **Lưu ý:** View nên "ngu ngơ" nhất có thể, không nên chứa logic xử lý phức tạp, chỉ dùng để hiển thị.

### **Controller (Điều khiển - Bộ não)**

Đây là nơi tiếp nhận yêu cầu (Request) từ người dùng, điều phối Model và chọn View để trả về.

* **Nhiệm vụ:** Tiếp nhận HTTP Request -> Gọi Model lấy dữ liệu -> Đẩy dữ liệu vào View -> Trả kết quả cho Client.
* **Ví dụ:** Khi bạn vào `/Product/Details/5`, Controller sẽ đi tìm sản phẩm số 5 và gọi View "Details" để hiện lên.

---

## 2. Luồng hoạt động (Request Life Cycle)

Khi người dùng nhập một địa chỉ Web, quy trình diễn ra như sau:

1. **Routing:** Hệ thống phân tích URL để biết cần gọi tới Controller nào.
2. **Action Execution:** Một phương thức (Action) trong Controller được thực thi.
3. **Model Interaction:** Controller gọi Model (thông qua EF Core) để lấy hoặc lưu dữ liệu.
4. **Result Return:** Controller trả về một `View()` kèm theo dữ liệu Model.
5. **Rendering:** Razor Engine trộn Model vào HTML và gửi về trình duyệt.

---

## 3. Cấu trúc thư mục chuẩn trong ASP.NET Core MVC

Khi bạn tạo một project MVC mới, Visual Studio sẽ sinh ra cấu trúc:

* **Controllers/**: Chứa các file `.cs` xử lý logic.
* **Models/**: Chứa các Class đại diện cho dữ liệu.
* **Views/**: Chứa các thư mục con tương ứng với từng Controller.
* **wwwroot/**: Chứa các file tĩnh (CSS, JS, Hình ảnh).
* **appsettings.json**: Chứa chuỗi kết nối Database (Connection String).

---

## 4. Ví dụ thực tế về một Action trong Controller

Giả sử bạn có một trang hiển thị danh sách sản phẩm:

```csharp
public class ProductController : Controller
{
    // Đây là một Action
    public IActionResult Index()
    {
        // 1. Giả lập lấy dữ liệu từ Model
        var products = new List<Product>
        {
            new Product { Id = 1, Name = "Laptop", Price = 1000 },
            new Product { Id = 2, Name = "Mouse", Price = 20 }
        };

        // 2. Trả về View và truyền danh sách products sang
        return View(products);
    }
}

```

---

## 5. Lưu ý khi làm việc với MVC (Dành cho người mới)

1. **Fat Model, Skinny Controller:** Hãy cố gắng giữ cho Controller của bạn "gầy" (ít code nhất có thể). Đẩy các logic tính toán phức tạp vào Model hoặc Service.
2. **Strongly Typed Views:** Luôn khai báo `@model IEnumerable<Product>` ở đầu file View để tận dụng tính năng gợi ý code (IntelliSense) của Visual Studio, tránh gõ sai tên thuộc tính.
3. **Separation of Concerns (SoC):** Đừng bao giờ viết truy vấn SQL trực tiếp trong Controller. Hãy dùng **Entity Framework** và tách ra tầng Repository nếu dự án lớn dần.

Trong ASP.NET Core Web API, **Middleware** đóng vai trò là những "trạm kiểm soát" nằm trên đường đi của Request và Response. Việc xử lý Middleware trong API có một chút khác biệt so với MVC truyền thống vì nó tập trung nhiều vào tính tự động, xử lý dữ liệu thô (JSON) và bảo mật.

### Dưới đây là chi tiết cách xử lý và các loại Middleware quan trọng nhất trong API:

---

### 1. Cách đăng ký Middleware (Pipeline)

Tất cả Middleware được cấu hình trong file `Program.cs`. Thứ tự đăng ký cực kỳ quan trọng vì Request sẽ đi qua các Middleware theo đúng thứ tự đó.

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// 1. Exception Handler (Nên để đầu tiên để bắt lỗi của các middleware sau)
app.UseExceptionHandler("/error");

// 2. HSTS & HTTPS Redirection
app.UseHttpsRedirection();

// 3. Static Files (Nếu API có chứa ảnh/file tĩnh)
app.UseStaticFiles();

// 4. Routing (Xác định endpoint)
app.UseRouting();

// 5. CORS (Cực kỳ quan trọng cho API)
app.UseCors("MyPolicy");

// 6. Authentication & Authorization
app.UseAuthentication();
app.UseAuthorization();

// 7. Map Controllers (Đích đến cuối cùng)
app.MapControllers();

app.Run();

```

---

### 2. Các Middleware "Sống còn" cho Web API

#### A. Global Exception Handling (Xử lý lỗi tập trung)

Thay vì dùng `try-catch` ở mọi Action, bạn dùng Middleware để bắt mọi lỗi `500 Internal Server Error`.

* **Cách dùng:** Viết một Middleware tùy chỉnh để khi code bị crash, nó sẽ trả về một cấu trúc JSON thống nhất (ví dụ: `{ "error": "Message", "code": 500 }`) thay vì trả về một trang HTML lỗi của server.

#### B. CORS (Cross-Origin Resource Sharing)

API thường được gọi từ các tên miền khác (ví dụ: Frontend chạy ở `localhost:3000`, API chạy ở `localhost:5000`). Trình duyệt sẽ chặn nếu không có Middleware này.

* **Lưu ý:** Chỉ nên cho phép các tên miền tin cậy (White-list) thay vì dùng `.AllowAnyOrigin()` để đảm bảo bảo mật.

#### C. Authentication & Authorization (Xác thực & Phân quyền)

Đối với REST API, Middleware này thường kiểm tra **JWT (JSON Web Token)** trong Header của Request.

* **Luồng xử lý:** Middleware đọc Token -> Giải mã -> Kiểm tra thời hạn -> Đẩy thông tin người dùng vào `HttpContext.User`.

---

### 3. Cách tạo Middleware Tùy chỉnh (Custom Middleware)

Đôi khi bạn cần xử lý riêng (ví dụ: Log lại thời gian chạy API, kiểm tra API Key).

**Cấu trúc một Custom Middleware:**

```csharp
public class MyCustomMiddleware
{
    private readonly RequestDelegate _next;

    public MyCustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // 1. Xử lý TRƯỚC khi Request đến Controller (Ví dụ: Kiểm tra API Key)
        if (!context.Request.Headers.ContainsKey("X-API-KEY"))
        {
            context.Response.StatusCode = 401;
            await context.Response.WriteAsJsonAsync(new { message = "Thiếu API Key" });
            return; // Ngắt đường ống, không cho đi tiếp
        }

        // 2. Chuyển sang Middleware tiếp theo
        await _next(context);

        // 3. Xử lý SAU khi Controller đã chạy xong (Ví dụ: Ghi log thời gian phản hồi)
    }
}

```

---

### 4. Lưu ý khi làm việc với Middleware trong API

1. **Đừng lạm dụng xử lý nặng:** Middleware chạy cho **mọi** Request. Nếu bạn viết code xử lý quá nặng (như truy vấn DB phức tạp) trong Middleware, toàn bộ API của bạn sẽ bị chậm lại đáng kể.
2. **Sử dụng `context.Response.HasStarted`:** Nếu bạn muốn ghi đè Response, hãy kiểm tra xem dữ liệu đã bắt đầu được gửi về Client chưa để tránh lỗi.
3. **Short-circuiting (Ngắt mạch):** Một Middleware có quyền không gọi `_next(context)`. Điều này thường dùng khi xác thực thất bại hoặc dữ liệu gửi lên vi phạm quy tắc an toàn.
4. **Middleware vs Filter:** * **Middleware:** Xử lý các vấn đề chung (CORS, Auth, Logging) cho toàn bộ ứng dụng.
* **Filters (như `[Authorize]`):** Xử lý logic sâu hơn, liên quan chặt chẽ đến MVC/API của Controller.



---
