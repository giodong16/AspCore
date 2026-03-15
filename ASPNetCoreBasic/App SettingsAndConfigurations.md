Trong ASP.NET Core, **App Settings và Configuration** là hệ thống quản lý các thông số cấu hình của ứng dụng (như chuỗi kết nối Database, API Key, hoặc các hằng số hệ thống) một cách linh hoạt, giúp bạn thay đổi hành vi ứng dụng mà không cần biên dịch (compile) lại code.

Đây là phần cực kỳ quan trọng để đảm bảo tính bảo mật và khả năng triển khai ứng dụng lên các môi trường khác nhau (Development, Staging, Production).

---

## 1. Các nguồn cấu hình (Configuration Providers)

ASP.NET Core rất mạnh mẽ vì nó có thể đọc cấu hình từ nhiều nguồn theo thứ tự ưu tiên:

1. **File `appsettings.json**`: Nguồn chính, dễ đọc, cấu trúc JSON.
2. **File `appsettings.{Environment}.json**`: Cấu hình riêng cho từng môi trường (ví dụ: `appsettings.Development.json`).
3. **Environment Variables (Biến môi trường)**: Thường dùng khi triển khai lên Docker hoặc Cloud (Azure, AWS).
4. **Command-line arguments**: Truyền trực tiếp khi chạy lệnh `dotnet run`.

> **Quy tắc ghi đè:** Nguồn nào được cấu hình sau sẽ ghi đè nguồn trước đó. Biến môi trường thường có độ ưu tiên cao nhất để phục vụ mục đích bảo mật trên server thật.

---

## 2. File `appsettings.json`

Đây là nơi bạn lưu trữ hầu hết các cấu hình.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=MyDb;Trusted_Connection=True;"
  },
  "MySettings": {
    "PageSize": 20,
    "ApiKey": "SECRET_KEY_123"
  }
}

```

---

## 3. Cách truy cập cấu hình trong Code

Có 2 cách chính để bạn lấy dữ liệu từ file cấu hình:

### Cách 1: Dùng trực tiếp `IConfiguration` (Mì ăn liền)

Cách này nhanh nhưng dễ gõ sai tên (Magic strings).

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _configuration;

    public HomeController(IConfiguration configuration)
    {
        _configuration = configuration;
    }

    public IActionResult Index()
    {
        var apiKey = _configuration["MySettings:ApiKey"];
        return Ok(apiKey);
    }
}

```

### Cách 2: Options Pattern (Chuẩn chuyên nghiệp)

Đây là cách "Pro" nhất: Ánh xạ JSON vào một Class C# để có gợi ý code (Intellisense) và dễ dàng kiểm tra dữ liệu.

1. **Tạo Class tương ứng:**

```csharp
public class MySettings {
    public int PageSize { get; set; }
    public string ApiKey { get; set; }
}

```

2. **Đăng ký trong `Program.cs`:**

```csharp
builder.Services.Configure<MySettings>(builder.Configuration.GetSection("MySettings"));

```

3. **Sử dụng qua `IOptions<T>`:**

```csharp
public class ProductService {
    private readonly MySettings _settings;
    public ProductService(IOptions<MySettings> options) {
        _settings = options.Value;
    }
}

```

---

## 4. Quản lý cấu hình theo môi trường (Environments)

Trong Backend, bạn sẽ gặp trường hợp:

* **Ở máy cá nhân (Development):** Dùng Database Local.
* **Ở Server (Production):** Dùng Database thật với mật khẩu cực kỳ bảo mật.

ASP.NET Core tự động nhận diện biến môi trường `ASPNETCORE_ENVIRONMENT`.

* Nếu là `Development`, nó sẽ nạp thêm file `appsettings.Development.json` để đè lên file gốc.

---

## 5. Lưu ý quan trọng về Bảo mật (Security)

1. **Tuyệt đối không đẩy Secrets lên Git:** Đừng bao giờ commit mật khẩu Database hay API Key lên GitHub.
2. **Sử dụng User Secrets:** Trong quá trình học/phát triển, hãy dùng công cụ **User Secrets** (`dotnet user-secrets init`). Dữ liệu này được lưu ở một thư mục riêng bên ngoài project trên máy bạn, Git sẽ không thấy được.
3. **Biến môi trường trên Server:** Khi triển khai (Deploy), hãy cấu hình các thông tin nhạy cảm qua Environment Variables của hosting (như Azure Key Vault hoặc Docker Secrets).

---

## 6. Tổng kết

* `IConfiguration`: Bộ não quản lý mọi cài đặt.
* `Options Pattern`: Cách lấy dữ liệu an toàn, sạch sẽ.
* `appsettings.{Env}.json`: Linh hoạt giữa các môi trường.
