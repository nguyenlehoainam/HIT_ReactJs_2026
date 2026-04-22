# 💻 Bài Tập Thực Hành: Ứng Dụng "Danh Bạ Người Dùng" (15 - 20 Phút)

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

**1. MainLayout.jsx**

```jsx
import { NavLink, Outlet } from "react-router-dom";

export default function MainLayout() {
  return (
    <div>
      <nav
        style={{ padding: "10px", background: "#eee", marginBottom: "20px" }}
      >
        <NavLink to="/" style={{ marginRight: "15px" }}>
          Trang Chủ
        </NavLink>
        <NavLink to="/users">Danh Sách Người Dùng</NavLink>
      </nav>
      {/* Nội dung thay đổi sẽ chui vào Outlet */}
      <main style={{ padding: "20px" }}>
        <Outlet />
      </main>
    </div>
  );
}
```

**2. UserList.jsx**

```jsx
import { Link } from "react-router-dom";
import useFetch from "../hooks/useFetch";

export default function UserList() {
  const { data, loading, error } = useFetch(
    "https://jsonplaceholder.typicode.com/users",
  );

  if (loading) return <p>Đang tải danh sách...</p>;
  if (error) return <p>Lỗi: {error}</p>;

  return (
    <div>
      <h2>Danh bạ</h2>
      <ul>
        {data?.map((user) => (
          <li key={user.id} style={{ marginBottom: "10px" }}>
            {user.name} - <Link to={`/users/${user.id}`}>Xem chi tiết</Link>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

**3. UserDetail.jsx**

```jsx
import { useParams, useNavigate } from "react-router-dom";
import useFetch from "../hooks/useFetch";

export default function UserDetail() {
  const { id } = useParams(); // Lấy ID từ URL
  const navigate = useNavigate(); // Dùng để quay lại

  const {
    data: user,
    loading,
    error,
  } = useFetch(`https://jsonplaceholder.typicode.com/users/${id}`);

  if (loading) return <p>Đang tìm thông tin người dùng...</p>;
  if (error) return <p>Lỗi: {error}</p>;

  return (
    <div>
      <h2>Hồ sơ của: {user?.name}</h2>
      <p>
        <strong>Email:</strong> {user?.email}
      </p>
      <p>
        <strong>Điện thoại:</strong> {user?.phone}
      </p>
      <p>
        <strong>Website:</strong> {user?.website}
      </p>

      <button onClick={() => navigate("/users")} style={{ marginTop: "20px" }}>
        ⬅ Quay lại danh sách
      </button>
    </div>
  );
}
```

**4. App.jsx**

```jsx
import { Routes, Route } from "react-router-dom";
import MainLayout from "./components/MainLayout";
import Home from "./pages/Home";
import UserList from "./pages/UserList";
import UserDetail from "./pages/UserDetail";

export default function App() {
  return (
    <Routes>
      <Route path="/" element={<MainLayout />}>
        <Route index element={<h1>Chào mừng đến với Danh bạ App!</h1>} />
        <Route path="users" element={<UserList />} />
        <Route path="users/:id" element={<UserDetail />} />
        <Route path="*" element={<h1>404 - Lạc đường rồi bạn ơi!</h1>} />
      </Route>
    </Routes>
  );
}
```
