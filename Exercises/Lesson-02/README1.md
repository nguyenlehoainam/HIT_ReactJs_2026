# 💻 Bài tập thực hành trên lớp - Buổi 2
**Chủ đề: Xây dựng Danh sách Thành viên CLB với tính năng "Thả tim"**

## 🎯 Mục tiêu
- Biết cách tách Component con và truyền dữ liệu thông qua **Props**.
- Quản lý trạng thái tương tác của người dùng bằng **useState**.
- Sử dụng hàm **map()** để render danh sách dữ liệu có chứa thuộc tính **key**.

---

## 📝 Yêu cầu chi tiết

**Bước 1: Chuẩn bị dữ liệu (Mock Data)**
Trong file `App.jsx`, hãy khai báo một mảng chứa danh sách các thành viên. Mỗi thành viên cần có `id`, `name`, và `role`.

**Bước 2: Tạo Component con `MemberCard`**
Tạo một Functional Component tên là `MemberCard`.
- Component này sẽ nhận vào các **props**: `name` và `role`.
- Bên trong `MemberCard`, khai báo một state là `likes` với giá trị khởi tạo là `0` (sử dụng `useState`).
- Giao diện của `MemberCard` gồm:
  - Thẻ `<h3>` hiển thị tên thành viên.
  - Thẻ `<p>` hiển thị chức vụ.
  - Một nút `<button>`: Hiển thị dòng chữ "Bình chọn ({likes})". Khi click vào nút này, số lượt bình chọn sẽ tăng lên 1.

**Bước 3: Hiển thị danh sách (Rendering List)**
Trong Component chính `App`:
- Sử dụng hàm `map()` để duyệt qua mảng dữ liệu thành viên đã tạo ở Bước 1.
- Trả về Component `<MemberCard />` cho mỗi phần tử. 
- **Bắt buộc:** Phải truyền đúng prop `key` (sử dụng `id` của thành viên) và các props `name`, `role` cho `MemberCard`.

---