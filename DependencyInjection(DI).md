# 🚀 ASP.NET Core Knowledge Base: Dependency Injection (DI)

## 1. Tổng quan (The Concept)

**Dependency Injection (DI)** là một kỹ thuật trong lập trình giúp tách rời các phần phụ thuộc (dependencies) ra khỏi đối tượng sử dụng chúng. Thay vì một lớp tự khởi tạo các đối tượng nó cần, các đối tượng này sẽ được "bơm" vào từ bên ngoài (thường là qua Constructor).

* **Nguyên tắc:** Inversion of Control (IoC) - Đảo ngược quyền điều khiển.
* **Lợi ích:**
* **Loosely Coupled:** Code linh hoạt, dễ thay đổi.
* **Testability:** Dễ dàng viết Unit Test bằng cách sử dụng Mock objects.
* **Maintainability:** Dễ bảo trì và quản lý vòng đời đối tượng.



---

## 2. Ba loại Vòng đời Service (Service Lifetimes)

Đây là phần quan trọng nhất cần nắm vững khi làm việc với ASP.NET Core DI Container.

| Loại | Tên gọi | Đặc điểm | Trường hợp sử dụng |
| --- | --- | --- | --- |
| **Transient** | Tạm thời | Tạo mới mỗi khi được yêu cầu (`AddTransient`). | Các service nhẹ, không lưu trạng thái (Mapper, Validator). |
| **Scoped** | Phạm vi | Tạo một lần duy nhất trong cùng một HTTP Request (`AddScoped`). | **Phổ biến nhất.** Dùng cho DbContext (EF Core), Repositories. |
| **Singleton** | Đơn lẻ | Tạo một lần duy nhất và dùng mãi mãi cho đến khi tắt App (`AddSingleton`). | Caching, Configuration, Logger, Background Tasks. |

---

## 3. Ví dụ Minh Họa Chi Tiết

Giả sử chúng ta xây dựng một hệ thống tạo mã định danh (Unique ID) để phân biệt cách hoạt động của 3 loại vòng đời.

### Bước 1: Định nghĩa Interface và Class

```csharp
public interface IIdOperation { string GetId(); }

public interface ITransientOperation : IIdOperation { }
public interface IScopedOperation : IIdOperation { }
public interface ISingletonOperation : IIdOperation { }

public class IdOperation : ITransientOperation, IScopedOperation, ISingletonOperation
{
    private readonly string _id;
    public IdOperation() => _id = Guid.NewGuid().ToString()[^4..]; // Lấy 4 ký tự cuối của GUID
    public string GetId() => _id;
}

```

### Bước 2: Đăng ký trong `Program.cs`

```csharp
builder.Services.AddTransient<ITransientOperation, IdOperation>();
builder.Services.AddScoped<IScopedOperation, IdOperation>();
builder.Services.AddSingleton<ISingletonOperation, IdOperation>();

```

### Bước 3: Sử dụng trong Controller

Khi bạn Load lại trang web (F5), bạn sẽ thấy:

* **Transient ID:** Luôn thay đổi mỗi khi biến được gọi.
* **Scoped ID:** Giống nhau trong cùng 1 lần load trang, nhưng đổi khi F5.
* **Singleton ID:** Không bao giờ đổi cho dù F5 bao nhiêu lần.

```csharp
public class DemoController : ControllerBase
{
    private readonly ITransientOperation _transient;
    private readonly IScopedOperation _scoped;
    private readonly ISingletonOperation _singleton;

    public DemoController(
        ITransientOperation transient, 
        IScapedOperation scoped, 
        ISingletonOperation singleton)
    {
        _transient = transient;
        _scoped = scoped;
        _singleton = singleton;
    }
}

```

---

## 4. Quy tắc Vàng (Anti-patterns cần tránh)

> ⚠️ **Lỗi Captive Dependency:** Không bao giờ được tiêm một Service có vòng đời **ngắn** vào một Service có vòng đời **dài**.

* **❌ SAI:** Tiêm `Scoped` vào `Singleton`.
* *Hậu quả:* Scoped Service sẽ bị giữ lại mãi mãi bởi Singleton, dẫn đến lỗi dữ liệu cũ hoặc `ObjectDisposedException`.


* **✅ ĐÚNG:** Tiêm `Singleton` vào `Scoped`, hoặc `Transient` vào bất cứ đâu.

---

## 5. Checklist ghi nhớ

* [ ] Hiểu rõ sự khác biệt giữa `AddTransient`, `AddScoped`, và `AddSingleton`.
* [ ] Biết cách sử dụng **Constructor Injection**.
* [ ] Luôn sử dụng **Interface** để đăng ký Service để tăng tính trừu tượng.
* [ ] Cẩn thận khi sử dụng Singleton với các dữ liệu biến đổi (Stateful).

---
Trong lộ trình trở thành Backend Developer chuyên nghiệp, **Mocking** là kỹ năng phân biệt giữa một người "biết viết code" và một người "biết viết code có thể bảo trì".

---

# 🎭 Mocking trong Unit Test (ASP.NET Core)

## 1. Mocking là gì?

**Mocking** là kỹ thuật tạo ra các đối tượng "giả" (Mock objects) để mô phỏng hành vi của các đối tượng "thật".

Trong Unit Test, mục tiêu của chúng ta là kiểm tra **duy nhất một đơn vị code** (ví dụ: một hàm trong Service). Nếu hàm đó gọi đến Database hoặc gửi Email, chúng ta không muốn Unit Test thực sự kết nối DB hay gửi mail thật. Lúc này, ta dùng Mock để thay thế.

---

## 2. Tại sao phải Mock? (Kiến thức "Cần và Đủ")

* **Tính cô lập (Isolation):** Đảm bảo test logic của Service A mà không bị ảnh hưởng bởi lỗi của Service B.
* **Tốc độ:** Test chạy cực nhanh vì không phải chờ kết nối mạng hay ổ đĩa.
* **Kiểm soát kịch bản:** Bạn có thể bắt Mock trả về lỗi (Exception) để xem code của bạn xử lý lỗi có đúng không (điều mà thực tế rất khó tự tạo ra).
* **Tính độc lập:** Test có thể chạy trên máy bất kỳ mà không cần cài đặt SQL Server hay cấu hình API Key.

---

## 3. Ví dụ minh họa thực tế

Giả sử bạn có `OrderService` dùng để tính tổng tiền và lưu vào Database. Bạn muốn test logic tính tiền.

### Thành phần thật:

```csharp
public interface IOrderRepository {
    bool SaveOrder(Order order);
}

public class OrderService {
    private readonly IOrderRepository _repo;
    public OrderService(IOrderRepository repo) => _repo = repo;

    public string PlaceOrder(decimal price, int quantity) {
        if (quantity <= 0) return "Số lượng không hợp lệ";
        
        var total = price * quantity;
        var success = _repo.SaveOrder(new Order { Total = total });
        
        return success ? "Thành công" : "Lỗi lưu trữ";
    }
}

```

### Cách viết Unit Test dùng thư viện Moq (Phổ biến nhất trong .NET):

```csharp
[Fact]
public void PlaceOrder_ShouldReturnSuccess_WhenRepoSavesFine() {
    // 1. Arrange (Khởi tạo Mock)
    var mockRepo = new Mock<IOrderRepository>();
    
    // Thiết lập: Khi hàm SaveOrder được gọi với bất kỳ đối tượng Order nào, hãy trả về 'true'
    mockRepo.Setup(repo => repo.SaveOrder(It.IsAny<Order>())).Returns(true);

    var service = new OrderService(mockRepo.Object);

    // 2. Act (Thực hiện hành động)
    var result = service.PlaceOrder(100, 2);

    // 3. Assert (Kiểm tra kết quả)
    Assert.Equal("Thành công", result);
    // Kiểm tra xem hàm SaveOrder có thực sự được gọi đúng 1 lần không
    mockRepo.Verify(repo => repo.SaveOrder(It.IsAny<Order>()), Times.Once());
}

```

---

## 4. Các thuật ngữ cần nhớ khi đi phỏng vấn

| Thuật ngữ | Ý nghĩa dễ hiểu |
| --- | --- |
| **Setup** | Định nghĩa trước: "Nếu tao gọi hàm A, mày hãy trả về giá trị B". |
| **Verify** | Kiểm tra xem: "Hàm A có thực sự được gọi hay chưa? Gọi mấy lần?". |
| **Returns** | Giá trị giả mà bạn muốn nhận được. |
| **Throws** | Bắt Mock ném ra một lỗi (Exception) để test phần `try-catch`. |

---

## 5. Mối quan hệ mật thiết giữa DI và Mocking

Bạn sẽ thấy: **Nếu không có Dependency Injection (DI), bạn không thể Mock.**

* Vì `OrderService` nhận `IOrderRepository` qua Constructor (DI), nên trong lúc Test, ta mới có "khe hở" để nhét `mockRepo.Object` vào thay vì dùng Repo thật.
* Đây chính là lý do vì sao DI được đặt ở đầu Roadmap: Nó là nền tảng để viết code có thể kiểm thử (Testable Code).

---

## 6. Checklist khi thực hành Mocking

* [ ] Chỉ Mock những thứ nằm ngoài quyền kiểm soát của hàm đang test (DB, API bên thứ 3, File system).
* [ ] Không Mock các logic tính toán thuần túy.
* [ ] Luôn sử dụng Interface để Mock.
* [ ] `Verify` là bước quan trọng để đảm bảo code không chỉ chạy đúng kết quả mà còn chạy đúng quy trình.

---
