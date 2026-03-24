# 💻 Bài tập thực hành trên lớp - Buổi 3
**Chủ đề: Fetch API danh sách người dùng & Trang trí giao diện (Styling)**

## 🎯 Mục tiêu
- Nắm vững cách sử dụng `useEffect` với mảng phụ thuộc rỗng `[]` để gọi API ngay khi Component vừa Mounting.
- Kết hợp `useState` để quản lý dữ liệu trả về và trạng thái "Đang tải" (Loading).
- Thực hành làm đẹp giao diện sử dụng các thư viện CSS (SASS, Tailwind hoặc CSS thuần).
- Hiểu được cách xử lý khi cấu trúc dữ liệu API trả về thay đổi.

---

## 📝 Yêu cầu chi tiết

**Bước 1: Chuẩn bị State**
Trong Component `UserList`, hãy khai báo 2 state:
1. `users`: Dùng để lưu mảng danh sách người dùng (Khởi tạo là mảng rỗng `[]`).
2. `isLoading`: Dùng để lưu trạng thái chờ API phản hồi (Khởi tạo là `true`).

**Bước 2: Gọi API với useEffect**
- Sử dụng `useEffect` để gọi API lấy danh sách người dùng từ URL: `https://jsonplaceholder.typicode.com/users`
- **Yêu cầu bắt buộc:** API chỉ được gọi đúng 1 lần duy nhất khi load trang (Sử dụng Dependency Array hợp lý).
- Khi có dữ liệu trả về (lưu ý: API này trả về trực tiếp một mảng), hãy cập nhật state `users` và chuyển `isLoading` thành `false`.

**Bước 3: Hiển thị giao diện (Render)**
- Nếu `isLoading` đang là `true`, hiển thị dòng chữ: *"Đang tải dữ liệu, vui lòng chờ..."*.
- Nếu đã có dữ liệu, dùng hàm `map()` để render danh sách người dùng. Mỗi thẻ người dùng (User Card) cần hiển thị:
  - Ảnh đại diện: Sử dụng dịch vụ tạo ảnh tự động từ tên `https://ui-avatars.com/api/?name=[Tên_Người_Dùng]&background=random`.
  - Tên đầy đủ (`name`).
  - Email (`email`).

**Bước 4: Styling (Trang trí)**
- Sử dụng CSS, SASS hoặc Tailwind CSS để chia danh sách người dùng thành dạng lưới (Grid) hoặc Flexbox sao cho mỗi thẻ người dùng nằm gọn gàng, có viền, đổ bóng và cách đều nhau.
