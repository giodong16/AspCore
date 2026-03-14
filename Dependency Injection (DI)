---

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

