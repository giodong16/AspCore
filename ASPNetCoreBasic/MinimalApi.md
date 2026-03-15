Trong lộ trình tiến tới sự tinh gọn và hiệu năng cao của .NET 8/9/10, **Minimal APIs** là một bước ngoặt lớn. Nếu MVC là một "nhà hàng" đầy đủ các phòng ban, thì Minimal APIs giống như một "xe đẩy thức ăn nhanh": cực kỳ nhanh, gọn và chỉ chứa những gì thực sự cần thiết.

---

## 1. Minimal APIs là gì?

Minimal APIs được giới thiệu từ .NET 6, cho phép bạn tạo ra các HTTP API với code tối thiểu. Thay vì phải tạo Controller, kế thừa class và định nghĩa các Attribute phức tạp, bạn có thể viết API ngay trong file `Program.cs`.

**So sánh sự khác biệt:**

* **Web API (Controller-based):** Phải tạo file `Controller`, dùng `[ApiController]`, `[Route]`,...
* **Minimal APIs:** Chỉ cần 1 dòng code để định nghĩa 1 Endpoint.

---

## 2. Cấu trúc cơ bản của một Minimal API

Dưới đây là tất cả những gì bạn cần để chạy một API lấy danh sách sản phẩm:

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// Định nghĩa Endpoint GET
app.MapGet("/products", () => new[] 
{
    new { Id = 1, Name = "Laptop", Price = 1000 },
    new { Id = 2, Name = "Phone", Price = 500 }
});

app.Run();

```

---

## 3. Cách xử lý các thành phần trong Minimal APIs

### A. Routing & Methods

Bạn sử dụng các phương thức `MapGet`, `MapPost`, `MapPut`, `MapDelete` trực tiếp từ đối tượng `app`.

```csharp
app.MapPost("/products", (Product product) => {
    // Lưu sản phẩm vào DB
    return Results.Created($"/products/{product.Id}", product);
});

```

### B. Dependency Injection (DI)

Trong Controller, bạn dùng Constructor Injection. Trong Minimal APIs, bạn truyền Service trực tiếp vào tham số của Lambda expression.

```csharp
app.MapGet("/users/{id}", (int id, IUserService userService) => 
    userService.GetUserById(id));

```

### C. Parameter Binding (Gắn kết tham số)

Minimal APIs rất thông minh, nó tự hiểu:

* Nếu tham số có trong URL (`{id}`) -> Lấy từ **Route**.
* Nếu là kiểu dữ liệu phức tạp (`Product`) -> Lấy từ **Body (JSON)**.
* Nếu có tiền tố `[FromQuery]` -> Lấy từ **Query String**.

---

## 4. Tại sao và Khi nào nên dùng Minimal APIs?

### Ưu điểm:

* **Hiệu năng (Performance):** Nhanh hơn Controller-based API vì giảm bớt các lớp xử lý trung gian của MVC.
* **Cực gọn:** Phù hợp cho các dự án Microservices nhỏ hoặc các API đơn giản.
* **Học nhanh:** Rất thân thiện với những người mới chuyển từ Node.js (Express) hay Python (FastAPI) sang.

### Nhược điểm:

* **Khó quản lý khi phình to:** Nếu bạn có 50-100 API trong một file `Program.cs`, code sẽ trở thành "mớ bòng bong". Tuy nhiên, bạn có thể tách chúng ra các Module (Extension Methods).
* **Thiếu một số tính năng MVC:** Một số Filters đặc thù của MVC sẽ khó áp dụng hơn.

---

## 5. Lưu ý khi sử dụng (Trường hợp thường dùng)

1. **Sử dụng cho Microservices:** Khi mỗi dịch vụ chỉ có vài Endpoint, Minimal APIs là lựa chọn số 1.
2. **Sử dụng cho Webhook:** Các API chỉ dùng để nhận thông báo từ bên thứ 3 (như Stripe, PayPal).
3. **Tổ chức code:** Hãy sử dụng kỹ thuật **MapGroup** để nhóm các API liên quan lại với nhau:
```csharp
var products = app.MapGroup("/products");
products.MapGet("/", GetAllProducts);
products.MapGet("/{id}", GetProductById);

```



---

## Tổng kết lộ trình của bạn

Đến đây, bạn đã nắm được cả 2 trường phái: **MVC** (truyền thống, chặt chẽ) và **Minimal APIs** (hiện đại, tinh gọn). Với một ứng dụng lớn, người ta vẫn dùng Controller; nhưng với xu hướng Cloud Native hiện nay, Minimal APIs đang dần chiếm ưu thế.
