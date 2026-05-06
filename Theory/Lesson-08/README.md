# 🎯 Buổi 8: Quản lý State toàn cục (Global State) với useContext & useReducer

Chào mừng các bạn đến với Buổi 8! Khi dự án của bạn lớn lên, việc truyền dữ liệu qua lại giữa các Component bằng `props` sẽ trở thành một "cơn ác mộng". Hôm nay, chúng ta sẽ học cách tạo ra một "kho chứa dữ liệu dùng chung" cho toàn bộ ứng dụng.

---

## I. Vấn đề cốt lõi: Nỗi đau mang tên "Prop-drilling"

**Prop-drilling (Khoan xuyên tầng)** là tình trạng bạn có một State ở Component ông nội (`App`), nhưng Component cháu chắt (`Avatar`) lại cần dùng dữ liệu đó. Bạn bắt buộc phải truyền prop qua hàng loạt Component trung gian dù chúng không hề xài tới dữ liệu đó.

`App (có data)` 👉 truyền qua `Header` 👉 truyền qua `Navbar` 👉 truyền qua `UserMenu` 👉 tới `Avatar (mới xài data)`.

**Hậu quả:**

- Code cực kỳ rườm rà, khó đọc.
- Bất cứ khi nào State đổi, toàn bộ các Component trung gian bị re-render oan uổng.

👉 **Giải pháp:** Sử dụng **Global State (Trạng thái toàn cục)** thông qua `useContext`.

---

## II. `useContext`: Trạm phát sóng dữ liệu toàn cục

Hãy tưởng tượng Context API giống như một **Trạm phát thanh (Radio Station)**. Trạm phát thanh cứ phát sóng trên trời, ai có Radio (Component) chỉnh đúng tần số (`useContext`) thì sẽ nghe được, không cần quan tâm đứng ở đâu.

### 3 Bước sử dụng Context API:

**Bước 1: Tạo Context (Xây trạm phát sóng)**

```jsx
// Xây dựng 1 trạm phát sóng tên là ThemeContext
import { createContext } from "react";
export const ThemeContext = createContext();
```

**Bước 2: Provider (Phát sóng dữ liệu)**
Bọc cái "trạm phát sóng" này ra ngoài cùng các Component con, và nhét dữ liệu vào biến `value`.

```jsx
// src/App.jsx
import { useState } from "react";
import { ThemeContext } from "./ThemeContext";
import Header from "./Header"; // Không thèm truyền prop xuống Header nữa!

export default function App() {
  const [theme, setTheme] = useState("light");
  const toggleTheme = () => setTheme(theme === "light" ? "dark" : "light");

  return (
    // Bất kỳ Component nào nằm trong Provider đều có thể lấy được { theme, toggleTheme }
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      <Header />
    </ThemeContext.Provider>
  );
}
```

**Bước 3: Consumer (Thu sóng và xài)**
Tại Component cháu chắt, dùng `useContext` để lấy thẳng dữ liệu ra xài.

```jsx
// src/Avatar.jsx (Nằm sâu bên trong Header)
import { useContext } from "react";
import { ThemeContext } from "./ThemeContext";

export default function Avatar() {
  // Lấy thẳng dữ liệu từ Trạm phát sóng, không cần nhận qua Prop!
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <div style={{ background: theme === "light" ? "#fff" : "#000" }}>
      <p>Theme hiện tại: {theme}</p>
      <button onClick={toggleTheme}>Đổi Theme</button>
    </div>
  );
}
```

---

## III. `useReducer`: Quản lý logic State phức tạp

Khi State của bạn là một Object lớn, hoặc logic để thay đổi State đó quá phức tạp (ví dụ: giỏ hàng có thêm, sửa, xóa, tính tổng tiền), thì `useState` sẽ tỏ ra đuối sức. Lúc này ta dùng `useReducer`.

`useReducer` hoạt động giống hệt quy trình ở quầy giao dịch Ngân hàng:

1. **State:** Số dư tài khoản hiện tại.
2. **Action (Phiếu yêu cầu):** Tờ giấy ghi rõ "Tôi muốn làm gì?" (Rút tiền, nạp tiền) và "Số lượng bao nhiêu?".
3. **Reducer (Nhân viên ngân hàng):** Người duy nhất được phép lấy Phiếu yêu cầu, xử lý và sửa lại Số dư tài khoản.
4. **Dispatch (Hành động nộp phiếu):** Bạn đưa phiếu cho nhân viên.

### Ví dụ: Bộ đếm số với useReducer

```jsx
import { useReducer } from "react";

// 1. Khởi tạo State mặc định
const initialState = { count: 0 };

// 2. Định nghĩa hàm Reducer (Nhân viên xử lý)
// Nhận vào State cũ và Phiếu yêu cầu (action), trả ra State mới.
const reducer = (state, action) => {
  switch (action.type) {
    case "TANG_LEN":
      return { count: state.count + action.payload }; // payload là dữ liệu đính kèm
    case "GIAM_XUONG":
      return { count: state.count - 1 };
    case "RESET":
      return { count: 0 };
    default:
      return state;
  }
};

export default function Counter() {
  // 3. Khởi tạo Hook
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <h2>Số đếm: {state.count}</h2>

      {/* 4. Dùng dispatch để "Gửi phiếu yêu cầu" */}
      <button onClick={() => dispatch({ type: "TANG_LEN", payload: 5 })}>
        Tăng 5
      </button>

      <button onClick={() => dispatch({ type: "GIAM_XUONG" })}>Giảm 1</button>
    </div>
  );
}
```

---

## IV. Đỉnh cao kiến trúc: Kết hợp `useContext` + `useReducer`

Khi làm dự án thực tế (Ví dụ: Tính năng Đăng nhập - Auth User), chúng ta thường kết hợp 2 tính năng này lại:

- `useReducer`: Để quản lý logic Login, Logout phức tạp một cách chặt chẽ.
- `useContext`: Để mang cái State đó đi phân phát cho mọi nơi (Header đổi chữ "Đăng nhập" thành "Tên User", chặn vào trang ẩn...).

**Đây chính là tư duy cốt lõi của thư viện Redux danh tiếng!**

### Thực hành: Xây dựng AuthProvider dùng chung toàn App

**1. Tạo file `src/contexts/AuthContext.jsx`**

```jsx
import { createContext, useReducer } from "react";

// Khởi tạo Context
export const AuthContext = createContext();

// Khởi tạo State và Reducer
const initialState = { user: null, isAuth: false };

const authReducer = (state, action) => {
  switch (action.type) {
    case "LOGIN":
      return { user: action.payload, isAuth: true };
    case "LOGOUT":
      return { user: null, isAuth: false };
    default:
      return state;
  }
};

// Tạo một Provider bọc ngoài cùng
export const AuthProvider = ({ children }) => {
  const [state, dispatch] = useReducer(authReducer, initialState);

  return (
    // Phân phát cả State và hàm Dispatch cho toàn bộ App
    <AuthContext.Provider value={{ state, dispatch }}>
      {children}
    </AuthContext.Provider>
  );
};
```

**2. Gắn Provider vào gốc rễ dự án (VD: `main.jsx` hoặc `App.jsx`)**

```jsx
import { AuthProvider } from "./contexts/AuthContext";

export default function App() {
  return (
    <AuthProvider>
      <Navbar />
      <Dashboard />
    </AuthProvider>
  );
}
```

**3. Bất kỳ component nào cũng lấy ra dùng được:**

```jsx
// src/components/Navbar.jsx
import { useContext } from "react";
import { AuthContext } from "../contexts/AuthContext";

export default function Navbar() {
  const { state, dispatch } = useContext(AuthContext);

  const handleLogin = () => {
    // Gọi API... lấy được data user
    const fakeUser = { name: "Admin Tèo" };
    dispatch({ type: "LOGIN", payload: fakeUser }); // Gửi lệnh Login
  };

  return (
    <nav>
      {state.isAuth ? (
        <>
          <span>Xin chào, {state.user.name}</span>
          <button onClick={() => dispatch({ type: "LOGOUT" })}>
            Đăng xuất
          </button>
        </>
      ) : (
        <button onClick={handleLogin}>Đăng nhập</button>
      )}
    </nav>
  );
}
```
