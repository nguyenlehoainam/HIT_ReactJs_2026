# 💻 Bài tập thực hành trên lớp - Buổi 1
**Chủ đề: Xây dựng Thẻ thông tin cá nhân (Profile Card)**

## 🎯 Mục tiêu
- Tự tay khởi tạo dự án React bằng Vite.
- Áp dụng các quy tắc bắt buộc của JSX (chỉ trả về 1 thẻ gốc, thẻ tự đóng, `className`).
- Biết cách nhúng biến JavaScript vào trong giao diện React.

---

## 📝 Yêu cầu chi tiết

**Bước 1: Khởi tạo dự án**
Mở Terminal, tạo một dự án mới bằng Vite với tên `my-profile`, sau đó chạy dự án lên trình duyệt ở cổng `localhost:5173`.

**Bước 2: Dọn dẹp dự án**
Vào file `src/App.jsx`, xóa hết toàn bộ code mặc định của Vite. 

**Bước 3: Tạo Functional Component**
Viết lại một Functional Component `App` mới tinh. Bên trong component này, hãy khai báo 3 biến JavaScript sau (có thể thay bằng thông tin của chính bạn):
1. `fullName` (Chuỗi - Ví dụ: "Nguyễn Văn A")
2. `club` (Chuỗi - Ví dụ: "CLB Tin học HIT")
3. `avatarUrl` (Chuỗi - chứa 1 đường link ảnh bất kỳ trên mạng)

**Bước 4: Xây dựng giao diện bằng JSX**
Bên trong lệnh `return()`, hãy viết mã JSX để hiển thị một khối `div` có `className="profile-card"`. Khối này cần chứa:
- 1 thẻ `<img />` hiển thị ảnh đại diện (sử dụng biến `avatarUrl`). Nhớ quy tắc thẻ tự đóng!
- 1 thẻ `<h1>` hiển thị dòng chữ: "Xin chào, mình là [Tên của bạn]" (sử dụng biến `fullName`).
- 1 thẻ `<p>` hiển thị dòng chữ: "Thành viên của [Tên CLB]" (sử dụng biến `club`).
- 1 danh sách `<ul>` liệt kê 3 kỹ năng bạn muốn học trong khóa này (gõ text tĩnh, ví dụ: <li>ReactJS</li>, <li>Tailwind</li>...).