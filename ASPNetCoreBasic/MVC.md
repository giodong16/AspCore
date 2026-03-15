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
