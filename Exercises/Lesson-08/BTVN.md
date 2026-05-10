# 📝 BÀI TẬP VỀ NHÀ: NÂNG CẤP "MINI GIỎ HÀNG" (PRO CART)

**Mục tiêu:** Nắm vững cách thao tác với mảng/object trong `useReducer` (tránh mutate state trực tiếp) và thực hành tính toán dữ liệu phái sinh (derived state) từ Context.

---

### 🎯 Các yêu cầu nâng cấp (Level Up):

1. **Dữ liệu chuẩn hóa:** Sản phẩm được thêm vào giỏ không còn là chuỗi (string) mà là Object: `{ id, name, price, quantity }`.
2. **Cộng dồn số lượng:** Nếu bấm "Mua" một sản phẩm đã có trong giỏ, không được thêm dòng mới mà phải **tăng thuộc tính `quantity`** của sản phẩm đó lên 1.
3. **Tính tổng tiền:** Hiển thị tổng số tiền của toàn bộ giỏ hàng trên Navbar.
4. **Xóa từng món:** Thêm nút "Xóa" cho từng sản phẩm cụ thể bên trong giỏ hàng.

---

### 💻 SOURCE CODE THỰC HÀNH (Tìm và hoàn thành các `// TODO`)

#### 1. File: `src/contexts/CartContext.jsx` (Nâng cấp Reducer)

```jsx
import { createContext, useReducer } from "react";

export const CartContext = createContext();

const initialState = {
  cart: [], // Mảng chứa các object: { id, name, price, quantity }
};

const cartReducer = (state, action) => {
  switch (action.type) {
    case "ADD_TO_CART": {
      const productToAdd = action.payload;

      // TODO 1: Kiểm tra xem sản phẩm (productToAdd) đã có trong state.cart chưa
      // Gợi ý: dùng findIndex() dựa trên id
      const existingItemIndex = -1; // Thay đổi dòng này

      if (existingItemIndex >= 0) {
        // TODO 2: Nếu ĐÃ CÓ - Tăng quantity của sản phẩm đó
        // Lưu ý: Phải clone mảng và clone object để tránh mutate state trực tiếp
        return { ...state }; // Thay đổi logic trả về
      } else {
        // TODO 3: Nếu CHƯA CÓ - Thêm object mới vào mảng cart kèm theo thuộc tính quantity: 1
        return { ...state }; // Thay đổi logic trả về
      }
    }

    case "REMOVE_ITEM": {
      // TODO 4: Lấy id từ action.payload, dùng hàm filter() để loại bỏ sản phẩm
      return { ...state }; // Thay đổi logic trả về
    }

    case "CLEAR_CART":
      return { cart: [] };

    default:
      return state;
  }
};

export const CartProvider = ({ children }) => {
  const [state, dispatch] = useReducer(cartReducer, initialState);

  return (
    <CartContext.Provider value={{ state, dispatch }}>
      {children}
    </CartContext.Provider>
  );
};
```

#### 2. File: `src/components/ProductList.jsx` (Dữ liệu mẫu)

```jsx
import { useContext } from "react";
import { CartContext } from "../contexts/CartContext";

const PRODUCTS = [
  { id: 1, name: "Bàn phím cơ Aula F87 Pro", price: 1000000 },
  { id: 2, name: "Chuột Logitech G102", price: 400000 },
  { id: 3, name: "Màn hình Dell Ultrasharp", price: 5500000 },
];

export default function ProductList() {
  const { dispatch } = useContext(CartContext);

  const handleAdd = (product) => {
    // TODO 5: Dispatch action "ADD_TO_CART" với payload là object product
  };

  return (
    <div style={{ padding: 20 }}>
      <h3>🛒 Danh sách sản phẩm</h3>
      <div style={{ display: "flex", gap: "15px" }}>
        {PRODUCTS.map((product) => (
          <div
            key={product.id}
            style={{
              border: "1px solid #ddd",
              padding: "15px",
              borderRadius: "8px",
            }}
          >
            <h4>{product.name}</h4>
            <p>Giá: {product.price.toLocaleString()}đ</p>
            <button onClick={() => handleAdd(product)}>Thêm vào giỏ</button>
          </div>
        ))}
      </div>
    </div>
  );
}
```

#### 3. File: `src/components/Navbar.jsx` (Tính toán & Hiển thị)

```jsx
import { useContext } from "react";
import { CartContext } from "../contexts/CartContext";

export default function Navbar() {
  const { state, dispatch } = useContext(CartContext);

  // TODO 6: Tính tổng số lượng TẤT CẢ sản phẩm (Dùng reduce trên state.cart)
  const totalItems = 0;

  // TODO 7: Tính tổng số tiền (price * quantity của từng item rồi cộng lại)
  const totalPrice = 0;

  return (
    <nav
      style={{
        background: "#f8f9fa",
        padding: "15px 20px",
        borderBottom: "1px solid #ddd",
      }}
    >
      <div
        style={{
          display: "flex",
          justifyContent: "space-between",
          alignItems: "center",
        }}
      >
        <h2 style={{ color: "#ee4d2d" }}>Shopee Fake Pro</h2>
        <div>
          <span style={{ marginRight: 20, fontWeight: "bold" }}>
            🛒 Giỏ hàng: {totalItems} món | Tổng: {totalPrice.toLocaleString()}đ
          </span>
          <button onClick={() => dispatch({ type: "CLEAR_CART" })}>
            Xóa tất cả
          </button>
        </div>
      </div>

      {/* Hiển thị chi tiết giỏ hàng */}
      {state.cart.length > 0 && (
        <div
          style={{
            marginTop: "15px",
            background: "#fff",
            padding: "15px",
            border: "1px solid #eee",
          }}
        >
          {state.cart.map((item) => (
            <div
              key={item.id}
              style={{
                display: "flex",
                justifyContent: "space-between",
                padding: "5px 0",
              }}
            >
              <span>
                {item.name} (x{item.quantity})
              </span>
              <div>
                <span style={{ marginRight: "20px" }}>
                  {(item.price * item.quantity).toLocaleString()}đ
                </span>
                {/* TODO 8: Gọi action "REMOVE_ITEM" truyền vào payload là item.id */}
                <button
                  onClick={() => {}}
                  style={{ color: "red", border: "none", cursor: "pointer" }}
                >
                  Xóa
                </button>
              </div>
            </div>
          ))}
        </div>
      )}
    </nav>
  );
}
```

---

### 🔥 THỬ THÁCH NÂNG CAO (Bonus Task)

_Yêu cầu: Giỏ hàng không bị mất khi Refresh trang (F5)._

- **Gợi ý 1:** Sử dụng `useEffect` trong `CartProvider` để `localStorage.setItem("cart", JSON.stringify(state.cart))` mỗi khi `state.cart` thay đổi.
- **Gợi ý 2:** Chỉnh sửa `initialState` để đọc dữ liệu từ `localStorage.getItem("cart")` khi ứng dụng khởi chạy.

---

**Lưu ý:** Đừng quên bọc ứng dụng trong `<CartProvider>` tại file `main.jsx`!
