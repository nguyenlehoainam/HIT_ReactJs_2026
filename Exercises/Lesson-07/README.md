# 💻 Bài Tập Thực Hành: Ứng Dụng "Danh Bạ Người Dùng" 

**Mục tiêu:** Áp dụng Custom Hook `useFetch` để lấy dữ liệu và thiết lập hệ thống định tuyến (Routing) cơ bản với Layout, Dynamic Route.

## 📝 Yêu Cầu Ứng Dụng

Xây dựng một Mini App có các tính năng sau:

1. **Layout chung:** Có một thanh Menu (Navbar) luôn hiển thị ở mọi trang gồm 2 link: **Trang Chủ** và **Danh Sách Người Dùng**.
2. **Trang Chủ (`/`):** Hiển thị lời chào mừng đơn giản.
3. **Trang Danh Sách Người Dùng (`/users`):** Sử dụng Hook `useFetch` đã học để gọi API và in ra danh sách người dùng. Mỗi người dùng có một nút "Xem chi tiết".
4. **Trang Chi Tiết Người Dùng (`/users/:id`):** Nhận `id` từ URL, dùng `useFetch` để gọi dữ liệu của đúng người đó và hiển thị tên, email, số điện thoại.
5. **Trang 404:** Hiển thị khi người dùng nhập sai đường dẫn.

_API sử dụng:_

- Lấy danh sách: `https://jsonplaceholder.typicode.com/users`
- Lấy 1 người dùng: `https://jsonplaceholder.typicode.com/users/:id`

---

## 🛠️ Hướng Dẫn Từng Bước (Gợi ý cho học viên)

### Bước 1: Chuẩn bị Custom Hook

- Tạo file `src/hooks/useFetch.js` và copy đoạn code `useFetch` từ phần lý thuyết vào.

### Bước 2: Tạo Layout với `<Outlet />`

- Tạo file `src/components/MainLayout.jsx`.
- Sử dụng `<NavLink>` để làm Menu và `<Outlet />` làm nơi hiển thị nội dung các trang con.

### Bước 3: Tạo các trang (Pages)

- **Home.jsx:** Giao diện chào mừng.
- **UserList.jsx:** Gọi `useFetch` lấy danh sách. Map dữ liệu ra thẻ `<li>`. Dùng `<Link to={'/users/' + user.id}>` để làm nút xem chi tiết.
- **UserDetail.jsx:** Dùng `useParams()` để lấy `id` từ thanh địa chỉ. Tiếp tục dùng `useFetch` kết hợp với `id` đó để gọi API lấy thông tin chi tiết. Dùng `useNavigate()` để làm nút "Quay lại".

### Bước 4: Lắp ráp Router tại `App.jsx`

- Thiết lập `<Routes>` và `<Route>`.
- Áp dụng Nested Route (Route lồng nhau) cho Layout.

---
