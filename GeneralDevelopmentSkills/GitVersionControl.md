Trong lộ trình trở thành Backend Developer, **Git & Version Control** không chỉ là công cụ quản lý code, mà là "tấm thẻ thông hành" để bạn làm việc trong các môi trường chuyên nghiệp.

Dưới đây là các kiến thức trọng tâm về Git mà bạn cần nắm vững, được chia theo cấp độ từ cơ bản đến nâng cao.

---

## 1. Luồng hoạt động của Git (Git Workflow)

Bạn cần hiểu 3 vùng dữ liệu quan trọng của Git để tránh việc mất code:

1. **Working Directory:** Thư mục chứa code bạn đang chỉnh sửa trực tiếp trên máy.
2. **Staging Area (Index):** "Nhà chờ", nơi bạn đánh dấu những file sẽ được đưa vào bản cập nhật tiếp theo.
3. **Local Repository:** Kho lưu trữ trên máy đã được đóng gói (commit).
4. **Remote Repository:** Kho lưu trữ trên server (GitHub, GitLab, Azure DevOps).

---

## 2. Các câu lệnh "Gối đầu giường"

Đừng cố nhớ tất cả, hãy bắt đầu với những lệnh này:

* **Khởi tạo & Kết nối:**
* `git init`: Khởi tạo project mới.
* `git clone [url]`: Tải một dự án có sẵn về máy.
* `git remote add origin [url]`: Kết nối code ở máy với server.


* **Làm việc hàng ngày:**
* `git status`: Kiểm tra xem file nào đã sửa, file nào chưa lưu.
* `git add .`: Đưa tất cả thay đổi vào Staging Area.
* `git commit -m "Thông điệp"`: Lưu lại thay đổi kèm ghi chú.
* `git push origin [branch]`: Đẩy code lên server.
* `git pull`: Lấy code mới nhất từ server về máy.



---

## 3. Làm việc với Nhánh (Branching) - Cốt lõi của Backend

Khi làm dự án ASP.NET, bạn không bao giờ code thẳng vào nhánh `main` hay `master`.

* **`git branch [tên_nhánh]`**: Tạo nhánh mới (ví dụ: `feature/login`, `fix/bug-api`).
* **`git checkout [tên_nhánh]`**: Chuyển sang nhánh khác để làm việc.
* **`git merge [tên_nhánh]`**: Gộp code từ nhánh tính năng vào nhánh chính.

> **Mẹo chuyên nghiệp:** Hãy tuân thủ quy tắc đặt tên nhánh: `feature/` cho tính năng mới, `bugfix/` cho sửa lỗi, và `hotfix/` cho lỗi khẩn cấp trên server.

---

## 4. Xử lý xung đột (Merge Conflicts)

Đây là "nỗi sợ" của người mới nhưng là chuyện thường tình của dân Backend. Xung đột xảy ra khi 2 người cùng sửa một dòng code trên cùng một file.

* **Cách xử lý:** Git sẽ đánh dấu vùng bị xung đột. Bạn phải mở file đó ra, chọn lấy code của bạn, code của đồng nghiệp, hoặc giữ cả hai, sau đó `add` và `commit` lại.

---

## 5. Git nâng cao cho Backend Developer

Khi bạn đã quen tay, hãy tìm hiểu thêm:

* **`.gitignore`**: Một file cực kỳ quan trọng cho dân .NET. Nó giúp Git bỏ qua các file rác như thư mục `bin/`, `obj/`, hay các file cấu hình cá nhân `.user`.
* *Tip:* Bạn có thể lên [gitignore.io](https://www.toptal.com/developers/gitignore) và gõ "VisualStudio" để lấy mẫu chuẩn.


* **Git Rebase**: Cách để làm sạch lịch sử commit, giúp sơ đồ nhánh trông thẳng và đẹp hơn.
* **Git Stash**: "Tạm cất" những code đang viết dở khi bạn cần chuyển nhánh gấp để fix lỗi khác mà chưa muốn commit.

---

## 6. Gắn kết với StyleCop & Cấu hình máy của bạn

Như bạn đã tìm hiểu về **StyleCop** và **.editorconfig**, khi dùng Git:

* Đảm bảo file `.editorconfig` luôn được commit đầu tiên.
* Trước khi `push` code, hãy chạy lệnh **Code Cleanup** (`Ctrl + K, Ctrl + E`) để đảm bảo code đẩy lên server là code "sạch" nhất, không bị đồng nghiệp phàn nàn về format.
