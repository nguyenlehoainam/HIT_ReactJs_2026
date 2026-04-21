# 🎯 Buổi 7: Custom Hooks - Đóng gói logic & Phân tách UI - React Router

Chào mừng các bạn đến với Buổi 8! Trong buổi này, chúng ta sẽ học cách nâng cấp bản thân từ một "người thợ lắp ráp" thành một "kỹ sư thiết kế" bằng cách tự tạo ra các Hooks cho riêng mình (Custom Hooks).

---

## I. Bản chất của Custom Hook

Để hiểu tại sao chúng ta cần Custom Hook, hãy tưởng tượng một **Component** trong React (ví dụ: `TrangChu.jsx` hay `GioHang.jsx`) là một **Ông Giám Đốc**. 
Nhiệm vụ duy nhất và quan trọng nhất của ông Giám Đốc này là: **Giao tiếp với khách hàng** (Tức là hiển thị giao diện UI lên màn hình qua phần `return <div>`).

Nhưng khi mới code, chúng ta thường bắt Giám Đốc làm mọi thứ: tự đi lấy dữ liệu (`useEffect`), tự ghi chép sổ sách (`useState`), tự kiểm tra lỗi (Validation). Hậu quả là file code dài đến 500 dòng, cực kỳ khó đọc.

👉 **Custom Hook chính là dàn "Trợ lý đắc lực":**
Thay vì để Giám đốc làm hết, ta tạo ra các cô/cậu trợ lý (Custom Hooks). Cô `useFetch` chuyên lo gọi điện lấy dữ liệu, anh `useForm` chuyên lo giấy tờ. Khi cần, Giám Đốc chỉ cần hô: *"Cô useFetch, lấy cho tôi danh sách User!"*. Mã nguồn Component sẽ lập tức ngắn gọn, sạch sẽ, chuẩn Clean Code.

---

## II. Sự khác biệt giữa Hàm JS bình thường và Custom Hook

Nhiều bạn thắc mắc: *"Thế tạo một hàm JS bình thường là `layDuLieu()` rồi gọi ra cũng được mà?"*

Đây là điểm khác biệt cốt lõi:
- **Hàm JS bình thường (Helper function):** Chỉ biết nhận đầu vào và tính toán ra đầu ra (ví dụ: hàm format ngày tháng). Nó **KHÔNG THỂ** nhớ dữ liệu (`useState`) và **KHÔNG THỂ** biết khi nào giao diện sinh ra hay chết đi (`useEffect`).
- **Custom Hook:** Về bản chất nó vẫn là một hàm JS, nhưng nó **CÓ ĐẶC QUYỀN** được sử dụng các "siêu năng lực" của React (được phép xài các Hook khác ở bên trong nó). 

---

## III. Ba quy tắc "Bất di bất dịch" khi viết Custom Hook

Để tạo ra một Custom Hook, bạn bắt buộc phải tuân thủ 3 luật lệ sau:

1. **Tên bắt buộc phải bắt đầu bằng chữ `use`:**
   Bạn phải đặt tên là `useDongHo`, `useKiemTraLoi`... Nếu bạn đặt tên là `layDuLieu` mà bên trong xài `useState`, React sẽ báo lỗi đỏ màn hình. Chữ `use` là "giấy phép" để React cấp siêu năng lực cho hàm của bạn.
2. **Tái sử dụng LOGIC, tuyệt đối không xài chung TRẠNG THÁI (STATE):**
   Nếu bạn gọi `useGioHang` ở component **Nút Mua Hàng** và gọi lại nó ở component **Thanh Menu**, React sẽ tạo ra **2 giỏ hàng hoàn toàn độc lập**. Custom Hook giúp bạn copy "công thức nấu ăn", chứ không phải xài chung một "nồi canh".
3. **Tự do quyết định đầu ra (Return):**
   Bạn có thể trả về một mảng `[value, setValue]`, một object `{ data, loading, error }`, hoặc thậm chí chỉ trả về một chuỗi văn bản. Mọi quyền quyết định đều ở bạn.

---

## IV. Khi nào nên và KHÔNG nên dùng Custom Hook?

### ✅ NÊN DÙNG KHI:
1. **Dấu hiệu Copy-Paste:** Bạn nhận ra mình vừa bôi đen copy một đoạn `useEffect` và `useState` từ Component A sang Component B. (VD: logic click ra ngoài để đóng menu, logic cuộn trang).
2. **Component phình to:** Khi một component vượt quá 150-200 dòng, phần logic tính toán nằm lộn xộn che khuất cả phần UI. 

### ❌ KHÔNG NÊN DÙNG KHI:
1. **Logic quá đơn giản:** Đừng biến một hàm tính tổng 2 số thành `useTinhTong()`. Nếu không cần dùng tới `useState` hay `useEffect`, hãy dùng hàm JS bình thường.
2. **Muốn xài chung dữ liệu toàn cục:** Nhớ Quy tắc số 2, Custom Hook không chia sẻ state. Nếu bạn muốn thông tin User sau khi đăng nhập có mặt ở mọi nơi, hãy dùng Context API hoặc Redux (Sẽ học ở buổi sau).

---

## V. THỰC HÀNH: Tự tay xây dựng 3 Custom Hook phổ biến nhất

### Thực hành 1: `useToggle` (Đóng gói logic đóng/mở)

Mọi tính năng Ẩn/Hiện Modal, Mở/Đóng Sidebar đều dùng chung logic bật tắt `true/false`. Ta sẽ đóng gói nó lại.

**File: `src/hooks/useToggle.js`**
```javascript
import { useState } from 'react';

export default function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);

  const toggle = () => {
    setValue((prev) => !prev);
  };

  // Trả về mảng giống hệt phong cách của useState
  return [value, toggle];
}
```

**Sử dụng trong Component:**
```jsx
import useToggle from './hooks/useToggle';

export default function App() {
  // Rất gọn gàng!
  const [isModalOpen, toggleModal] = useToggle(false);
  const [isSidebarOpen, toggleSidebar] = useToggle(true);

  return (
    <div>
      <button onClick={toggleSidebar}>Mở Sidebar</button>
      <button onClick={toggleModal}>Mở Modal</button>
      
      {isModalOpen && <p>Nội dung Modal đây!</p>}
    </div>
  );
}
```

---

### Thực hành 2: `useFetch` (Đóng gói logic gọi API)

Thay vì đi đâu cũng phải viết lại bộ 3 state: `data`, `loading`, `error` và khối `useEffect` dài ngoằng, ta giao hết cho "cô trợ lý" `useFetch`.

**File: `src/hooks/useFetch.js`**
```javascript
import { useState, useEffect } from 'react';

export default function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    setLoading(true);
    const fetchData = async () => {
      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error("Lỗi mạng!");
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    fetchData();
  }, [url]);

  // Trả về Object
  return { data, loading, error };
}
```

**Sử dụng trong Component:**
```jsx
import useFetch from './hooks/useFetch';

export default function UserList() {
  // Code UI bây giờ sạch bong, không còn dấu vết của fetch()
  const { data, loading, error } = useFetch('[https://jsonplaceholder.typicode.com/users](https://jsonplaceholder.typicode.com/users)');

  if (loading) return <p>Đang tải...</p>;
  if (error) return <p>Lỗi: {error}</p>;

  return <ul>{data.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

---

### Thực hành 3: `useLocalStorage` (Đóng gói lưu trữ bộ nhớ)

Dùng để làm Dark Mode hoặc giỏ hàng. Hook này đảm bảo khi F5 lại trang web, dữ liệu vẫn được lấy ra từ ổ cứng trình duyệt.

**File: `src/hooks/useLocalStorage.js`**
```javascript
import { useState } from "react";

export default function useLocalStorage(key, initialValue) {
  // Khởi tạo state bằng cách đọc từ LocalStorage
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });

  // Hàm cập nhật state đồng thời lưu đè vào LocalStorage
  const setValue = (value) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
}
```

**Sử dụng trong Component:**
```jsx
import useLocalStorage from './hooks/useLocalStorage';

export default function ThemeSwitcher() {
  // Hoạt động y hệt useState, nhưng F5 không bị mất giá trị!
  const [theme, setTheme] = useLocalStorage('app-theme', 'light');

  return (
    <div style={{ background: theme === 'dark' ? '#333' : '#fff' }}>
      <h1>Giao diện đang là: {theme}</h1>
      <button onClick={() => setTheme('dark')}>Dark Mode</button>
      <button onClick={() => setTheme('light')}>Light Mode</button>
    </div>
  );
}
```

# 📘 React Router (v6+) - Từ Cơ Bản Đến Nâng Cao

Chào mừng bạn đến với lộ trình học **React Router**. Tài liệu này sẽ giúp bạn nắm vững cách xây dựng hệ thống điều hướng cho ứng dụng Single Page Application (SPA) một cách bài bản nhất.

---

## 🚀 I. Tổng Quan & Tư Duy SPA

### 1. Tại sao cần React Router?
Trong web truyền thống (Multi-Page App), mỗi khi chuyển trang, trình duyệt sẽ tải lại toàn bộ (chớp trắng màn hình). 

Với **React Router**:
* **Không tải lại trang:** Chỉ có các Component cần thiết được thay đổi.
* **Tốc độ:** Trải nghiệm người dùng cực kỳ mượt mà.
* **Đồng bộ URL:** Đảm bảo thanh địa chỉ trình duyệt luôn khớp với nội dung đang hiển thị.

### 2. Cài đặt
Mở terminal tại thư mục dự án và chạy:
```bash
npm install react-router-dom
```

---

## 🏗️ II. Thiết Lập Cấu Trúc Cơ Bản

### 1. Kích hoạt Router (`main.jsx`)
Bạn cần bọc ứng dụng trong `<BrowserRouter>` để kích hoạt khả năng điều hướng.

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

### 2. Định nghĩa sơ đồ Route (`App.jsx`)
Sử dụng thẻ `<Routes>` và `<Route>` để ánh xạ địa chỉ URL với Component.

```jsx
import { Routes, Route, NavLink } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';

function App() {
  return (
    <div>
      {/* Thanh Menu sử dụng NavLink để tự động thêm class 'active' */}
      <nav className="navbar">
        <NavLink to="/" end>Trang Chủ</NavLink>
        <NavLink to="/about">Giới Thiệu</NavLink>
      </nav>

      {/* Khu vực nội dung thay đổi theo URL */}
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="*" element={<h1>404 - Không tìm thấy trang!</h1>} />
      </Routes>
    </div>
  );
}
```

---

## 🧬 III. Các Khái Niệm Nâng Cao Phải Biết

### 1. Dynamic Routes (Đường dẫn động) & `useParams`
Dùng khi bạn muốn hiển thị thông tin dựa trên một ID cụ thể (Ví dụ: chi tiết sản phẩm).

* **Khai báo:** `<Route path="/product/:id" element={<ProductDetail />} />`
* **Sử dụng:**

```jsx
import { useParams } from 'react-router-dom';

function ProductDetail() {
  const { id } = useParams(); // Lấy giá trị 'id' từ thanh URL
  return <h1>Đang xem sản phẩm có ID là: {id}</h1>;
}
```

### 2. Nested Routes & `<Outlet />` (Định tuyến lồng nhau)
Dùng để tạo Layout chung (như Dashboard có Sidebar cố định, chỉ thay đổi nội dung bên phải).

```jsx
// App.jsx
<Route path="/admin" element={<AdminLayout />}>
  <Route path="users" element={<UserList />} />
  <Route path="settings" element={<Settings />} />
</Route>

// AdminLayout.jsx
import { Outlet } from 'react-router-dom';

const AdminLayout = () => (
  <div className="layout">
    <aside>Sidebar Menu</aside>
    <main>
      <Outlet /> {/* Nơi các trang con (UserList/Settings) sẽ hiển thị */}
    </main>
  </div>
);
```

### 3. Điều hướng bằng Code với `useNavigate`
Dùng khi bạn muốn chuyển trang tự động sau một hành động (như sau khi bấm nút Đặt hàng).

```jsx
import { useNavigate } from 'react-router-dom';

function OrderButton() {
  const navigate = useNavigate();

  const handleOrder = () => {
    // Xử lý logic...
    alert("Đặt hàng thành công!");
    navigate('/thanks'); // Chuyển sang trang cám ơn
  };

  return <button onClick={handleOrder}>Mua ngay</button>;
}
```

---

## 🛠️ IV. Bảng Tổng Kết Nhanh

| Công cụ | Mô tả | Cách dùng chính |
| :--- | :--- | :--- |
| **`BrowserRouter`** | Bộ não quản lý lịch sử | Bọc ngoài cùng `App`. |
| **`Link` / `NavLink`** | Thay thế thẻ `<a>` | Điều hướng giữa các trang. |
| **`useParams`** | Hook lấy tham số URL | Lấy ID sản phẩm, slug bài viết. |
| **`useNavigate`** | Hook chuyển hướng | Dùng trong hàm xử lý logic. |
| **`Outlet`** | Vị trí hiển thị Route con | Làm Layout (Header/Footer chung). |

---

## 💡 Mẹo nhỏ cho người mới
1. **Dùng `NavLink` cho Menu:** Nó giúp bạn dễ dàng làm nổi bật link đang được chọn (active) bằng CSS.
2. **Luôn có Route `*`:** Điều này giúp bắt các URL sai và hiển thị trang 404 thay vì để màn hình trắng.
3. **Thứ tự Route:** React Router v6 rất thông minh, nó sẽ tự chọn Route khớp nhất thay vì chỉ chọn cái đầu tiên nó thấy.

---