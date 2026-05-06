### Đề Bài: Xây dựng "Mini Giỏ Hàng" (Global Mini Cart)

**Mục tiêu:** 
Áp dụng `useContext` và `useReducer` để quản lý số lượng sản phẩm trong giỏ hàng. Thêm sản phẩm ở `ProductList` (Component con) nhưng số lượng phải tự động cập nhật trên `Navbar` (Component dùng chung).

**Yêu cầu:**
Tìm các dòng có chữ `// TODO` trong 3 file dưới đây và viết code hoàn thiện chức năng.

#### File 1: `src/contexts/CartContext.jsx` (Trạm phát sóng & Quầy giao dịch)
```jsx
import { createContext, useReducer } from "react";

// 1. Khởi tạo Context
export const CartContext = createContext();

// 2. Khởi tạo State mặc định (Giỏ hàng rỗng)
const initialState = { cart: [] };

// 3. Reducer xử lý logic
const cartReducer = (state, action) => {
  switch (action.type) {
    case "ADD_TO_CART":
      // TODO 1: Trả về state mới, thêm action.payload vào mảng cart hiện tại
      return {  }; 
      
    case "CLEAR_CART":
      // TODO 2: Trả về state mới với mảng cart rỗng
      return {  };
      
    default:
      return state;
  }
};

// 4. Provider bọc ngoài ứng dụng
export const CartProvider = ({ children }) => {
  // TODO 3: Gọi hook useReducer với cartReducer và initialState
  const [state, dispatch] = /* Viết code ở đây */;

  return (
    // TODO 4: Truyền state và dispatch vào value của Provider
    <CartContext.Provider value={{ /* Viết code ở đây */ }}>
      {children}
    </CartContext.Provider>
  );
};
```

#### File 2: `src/components/Navbar.jsx` (Nơi hiển thị tổng số lượng)
```jsx
import { useContext } from "react";
import { CartContext } from "../contexts/CartContext";

export default function Navbar() {
  // TODO 5: Lấy state và dispatch từ CartContext
  const { state, dispatch } = /* Viết code ở đây */;

  return (
    <nav style={{ display: 'flex', justifyContent: 'space-between', background: '#f8f9fa', padding: '10px 20px' }}>
      <h2>Shopee Fake</h2>
      <div>
        {/* TODO 6: Hiển thị độ dài của mảng cart */}
        <span style={{ marginRight: 15 }}>🛒 Giỏ hàng: {/* state.cart... */} món</span>
        
        {/* TODO 7: Gắn sự kiện onClick để dispatch action "CLEAR_CART" */}
        <button onClick={() => {}}>Xóa giỏ hàng</button>
      </div>
    </nav>
  );
}
```

#### File 3: `src/components/ProductList.jsx` (Nơi bấm nút mua hàng)
```jsx
import { useContext } from "react";
import { CartContext } from "../contexts/CartContext";

export default function ProductList() {
  // TODO 8: Lấy dispatch từ CartContext để gửi yêu cầu
  const { dispatch } = /* Viết code ở đây */;

  const handleAdd = (productName) => {
    // TODO 9: Gọi dispatch gửi action có type là "ADD_TO_CART" và payload là productName
    /* Viết code dispatch ở đây */
  };

  return (
    <div style={{ padding: 20 }}>
      <h3>Danh sách sản phẩm</h3>
      <button onClick={() => handleAdd("Bàn phím cơ")} style={{ marginRight: 10 }}>
        Mua Bàn phím cơ
      </button>
      <button onClick={() => handleAdd("Chuột Logitech")}>
        Mua Chuột Logitech
      </button>
    </div>
  );
}
```

> **Lưu ý cho lớp:** Các bạn cần bọc `<CartProvider>` ra ngoài cùng ở file `main.jsx` hoặc `App.jsx` thì app mới chạy được nhé!