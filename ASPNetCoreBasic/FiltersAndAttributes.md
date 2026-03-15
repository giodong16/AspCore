Trong lộ trình ASP.NET Core, nếu **Middleware** là những trạm kiểm soát cho toàn bộ ứng dụng, thì **Filters** là những trạm kiểm soát "đặc quyền" dành riêng cho các Action trong Controller hoặc Minimal APIs.

Chúng cho phép bạn chạy mã trước hoặc sau các giai đoạn cụ thể trong luồng xử lý yêu cầu của MVC/API.

---

## 1. Filters là gì?

Filters (Bộ lọc) giúp bạn xử lý các tác vụ lặp đi lặp lại (Cross-cutting concerns) như:

* Kiểm tra quyền truy cập.
* Log dữ liệu đầu vào/đầu ra.
* Xử lý lỗi (Exception).
* Lưu bộ nhớ đệm (Caching).

### Vị trí của Filters trong Pipeline:

Filters chạy **bên trong** Action Invoker. Tức là sau khi Middleware đã xác định được Request này sẽ vào Controller nào, Filters mới bắt đầu làm việc.

---

## 2. 5 Loại Filter chính (Theo thứ tự thực thi)

### A. Authorization Filters

* **Nhiệm vụ:** Kiểm tra xem người dùng có quyền thực hiện yêu cầu này không.
* **Đặc điểm:** Chạy đầu tiên. Nếu thất bại, nó sẽ ngắt mạch (short-circuit) ngay lập tức.
* **Ví dụ:** Attribute `[Authorize]`.

### B. Resource Filters

* **Nhiệm vụ:** Chạy ngay sau Authorization. Rất hữu ích để xử lý Caching (trả về kết quả ngay mà không cần chạy qua các bước sau).
* **Interface:** `IResourceFilter`.

### C. Action Filters

* **Nhiệm vụ:** Chạy ngay **trước** và **sau** khi một Action được thực thi. Đây là loại được dùng nhiều nhất.
* **Trường hợp dùng:** Kiểm tra `ModelState.IsValid`, log tham số đầu vào.
* **Interface:** `IActionFilter`.

### D. Exception Filters

* **Nhiệm vụ:** Chỉ chạy khi có ngoại lệ (lỗi) xảy ra trong quá trình thực thi Action.
* **Trường hợp dùng:** Ghi log lỗi vào Database hoặc định dạng lại thông báo lỗi trả về cho Client.
* **Interface:** `IExceptionFilter`.

### E. Result Filters

* **Nhiệm vụ:** Chạy trước và sau khi kết quả (ActionResult) được thực thi (ví dụ: ngay trước khi View được render hoặc JSON được gửi đi).
* **Interface:** `IResultFilter`.

---

## 3. Attributes là gì?

Attributes (Thuộc tính) là các thẻ đánh dấu đặt trong dấu ngoặc vuông `[...]` trên các Class hoặc Method. Trong ASP.NET Core, Attributes thường được dùng để áp dụng Filters hoặc cấu hình Routing.

### Các Attributes phổ biến:

* **Routing:** `[Route("api/[controller]")]`, `[HttpGet]`, `[HttpPost]`.
* **Validation:** `[Required]`, `[StringLength(100)]`, `[EmailAddress]`.
* **Behavior:** `[ApiController]`, `[AllowAnonymous]`, `[Authorize]`.

---

## 4. Cách tạo một Custom Filter (Dạng Attribute)

Bạn có thể tự tạo bộ lọc riêng cho dự án của mình. Ví dụ: Một bộ lọc để log thời gian thực thi của Action.

```csharp
public class LogExecutionTimeFilter : Attribute, IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        // Chạy TRƯỚC khi Action thực thi
        Console.WriteLine($"Bắt đầu Action: {context.ActionDescriptor.DisplayName}");
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        // Chạy SAU khi Action thực thi xong
        Console.WriteLine("Kết thúc Action.");
    }
}

// Cách sử dụng:
[LogExecutionTimeFilter]
public IActionResult GetDetails() => Ok();

```

---

## 5. Lưu ý khi sử dụng & So sánh với Middleware

| Đặc điểm | Middleware | Filters |
| --- | --- | --- |
| **Phạm vi** | Toàn bộ ứng dụng (Global). | Chỉ trong MVC / Web API. |
| **Ngữ cảnh** | Biết về `HttpContext`. | Biết về `ActionDescriptor`, `ModelState`. |
| **Thứ tự** | Chạy trước Filters. | Chạy sau Middleware. |
| **Trường hợp dùng** | Authentication, Logging chung, Static files. | Validation, Authorization theo Action, Exception xử lý riêng cho API. |

### Lưu ý "vàng":

1. **Ưu tiên Built-in Filters:** Hãy dùng những gì Microsoft đã xây dựng sẵn (`[Authorize]`, `[ResponseCache]`) trước khi nghĩ đến việc tự viết.
2. **Global vs Local:** Bạn có thể đăng ký Filter cho toàn dự án trong `Program.cs` (`builder.Services.AddControllers(options => options.Filters.Add<MyFilter>())`) hoặc chỉ áp dụng cho 1 Action/Controller bằng Attribute.
