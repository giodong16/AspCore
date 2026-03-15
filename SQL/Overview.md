Để nắm chắc phần này, bạn cần nhìn nhận nó dưới góc độ một người làm Backend: Làm sao để dữ liệu An toàn - Nhanh - Chính xác.
1. SQL Basic (Cơ bản nhưng cực kỳ quan trọng)
Đây là công cụ để bạn "nói chuyện" với Database.
 * Cấu trúc câu lệnh tiêu biểu (JOIN):
   SELECT p.ProductName, c.CategoryName
FROM Products p
INNER JOIN Categories c ON p.CategoryId = c.Id
WHERE p.Price > 500
ORDER BY p.Price DESC;

 * Trường hợp dùng: Lấy dữ liệu sản phẩm kèm tên danh mục để hiển thị lên Website.
 * Lưu ý: * Luôn sử dụng Alias (như p, c ở trên) để câu lệnh ngắn gọn và tránh trùng tên cột.
   * Tránh dùng SELECT *, hãy chỉ định rõ các cột bạn cần để tiết kiệm băng thông và RAM cho Server.
2. Database Design (Thiết kế hệ thống)
Thiết kế tốt giúp code C# của bạn nhàn hơn rất nhiều.
 * Lý thuyết (Chuẩn hóa - Normalization):
   * Tránh dư thừa: Đừng lưu tên khách hàng ở mọi đơn hàng, hãy lưu CustomerId và để thông tin tên ở bảng Customers.
 * Trường hợp dùng: Thiết kế bảng cho trang thương mại điện tử, mạng xã hội.
 * Lưu ý: Đôi khi trong Backend thực tế, người ta chấp nhận "Phản chuẩn hóa" (tạo ra một chút dư thừa) để tăng tốc độ đọc dữ liệu, tránh phải JOIN quá nhiều bảng làm chậm hệ thống.
3. Constraints (Ràng buộc - "Bộ lọc" dữ liệu)
Đây là tuyến phòng thủ đầu tiên ngăn chặn dữ liệu sai.
 * Cấu trúc câu lệnh:
   ALTER TABLE Users
ADD CONSTRAINT CHK_Age CHECK (Age >= 18);

ALTER TABLE Users
ADD CONSTRAINT DF_Status DEFAULT 'Active' FOR Status;

 * Các loại thường dùng:
   * Primary Key: Không thể thiếu để định danh.
   * Foreign Key: Ngăn chặn việc xóa một "Danh mục" khi vẫn còn "Sản phẩm" thuộc về nó.
 * Lưu ý: Hãy đặt tên cho Constraint (như CHK_Age) thay vì để SQL tự đặt tên ngẫu nhiên. Điều này giúp bạn dễ dàng sửa đổi hoặc xóa nó sau này khi cần.
4. Stored Procedure (Thủ tục - Logic tại Database)
Thay vì gửi 10 câu lệnh SQL từ C#, bạn chỉ gửi 1 lệnh gọi Procedure.
 * Cấu trúc câu lệnh:
   CREATE PROCEDURE GetProductsByCategory
    @CatId INT
AS
BEGIN
    SELECT * FROM Products WHERE CategoryId = @CatId AND IsActive = 1;
END;

 * Trường hợp dùng: Các nghiệp vụ phức tạp như "Tính lương nhân viên" hoặc "Chốt đơn hàng" cần thực hiện nhiều bước kiểm tra trong Database.
 * Lưu ý: Stored Procedure giúp chống SQL Injection rất tốt. Tuy nhiên, đừng viết quá nhiều logic nghiệp vụ (Business Logic) ở đây vì nó sẽ khó quản lý phiên bản (Version Control) bằng Git hơn là code C#.
5. Trigger (Kích hoạt tự động)
"Cái bẫy" tự động thực thi khi có biến động dữ liệu.
 * Cấu trúc câu lệnh:
   CREATE TRIGGER trg_UpdateStock
ON OrderDetails
AFTER INSERT
AS
BEGIN
    UPDATE Products
    SET StockQuantity = StockQuantity - i.Quantity
    FROM Products p
    INNER JOIN inserted i ON p.Id = i.ProductId;
END;

 * Trường hợp dùng: * Tự động cập nhật số lượng tồn kho khi có đơn hàng mới.
   * Ghi log lịch sử thay đổi giá (Auditing).
 * Lưu ý cực quan trọng: * Trigger chạy ngầm. Nếu nó bị lỗi, câu lệnh INSERT/UPDATE của bạn cũng sẽ thất bại mà đôi khi bạn không hiểu tại sao.
   * Sử dụng bảng inserted và deleted (bảng tạm của SQL) để lấy dữ liệu vừa thay đổi.
Tổng kết lưu ý:
 * Học SQL Server Management Studio (SSMS): Đây là công cụ đắc lực nhất để thực hành các lệnh trên.
 * Đừng lạm dụng Trigger: Nếu có thể xử lý ở code C# (tầng Application), hãy ưu tiên xử lý ở đó để dễ debug hơn.
 * Ưu tiên Index: Khi bảng lên tới hàng triệu dòng, không có Index thì mọi câu lệnh SELECT đều trở thành "thảm họa" về tốc độ.
