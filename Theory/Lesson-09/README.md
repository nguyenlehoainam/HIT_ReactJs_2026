# Lý thuyết Redux Toolkit (RTK) - Redux Hiện đại

## 🎯 Tổng quan

Redux Toolkit (RTK) là bộ công cụ chính thức để viết logic Redux một cách hiệu quả. RTK được thiết kế để giải quyết các vấn đề phổ biến của Redux truyền thống:

- Quá nhiều boilerplate code (code lặp đi lặp lại)
- Cấu hình store phức tạp
- Cần cài đặt nhiều thư viện (packages) bổ sung
- Cập nhật state bất biến (immutable updates) khó viết

## 📦 Cài đặt Thư viện (Installation)

Để bắt đầu sử dụng Redux Toolkit trong dự án React, bạn cần cài đặt 2 thư viện chính:

- `@reduxjs/toolkit`: Chứa phần lõi của RTK (configureStore, createSlice, thunk,...).
- `react-redux`: Thư viện cung cấp các hooks (`useSelector`, `useDispatch`) giúp kết nối Redux store với React components.

Sử dụng công cụ quản lý package tương ứng của dự án (NPM, Yarn hoặc pnpm) để cài đặt:

**Sử dụng NPM:**

```bash
npm install @reduxjs/toolkit react-redux
```

## 🔄 Sơ đồ Luồng Redux (Redux Flow Diagram)

```text
┌─────────────┐    dispatch(action)   ┌─────────────┐
│   UI/View   │ ────────────────────► │   Action    │
│ (Component) │                       │ (createSlice)│
└─────────────┘                       └─────────────┘
       ▲                                      │
       │ useSelector                          ▼
       │                            ┌─────────────┐
       │                            │   Reducer   │
       │                            │ (createSlice)│
       │                            └─────────────┘
       │                                      │
       │                                      ▼
┌─────────────┐                       ┌─────────────┐
│   Store     │ ◄──────────────────── │    State    │
│(configureStore)│                    │ (Đã cập nhật)│
└─────────────┘                       └─────────────┘
```

## 🆚 RTK vs Redux Truyền thống

| Tiêu chí                         | Redux Truyền thống             | Redux Toolkit                         |
| :------------------------------- | :----------------------------- | :------------------------------------ |
| **Code lặp lại (Boilerplate)**   | Nhiều code lặp lại             | Tối thiểu code lặp lại                |
| **Cài đặt Store**                | Phức tạp, nhiều cấu hình       | `configureStore()` đơn giản           |
| **Reducers**                     | Viết tay, dễ xảy ra lỗi        | `createSlice()` tự động tạo           |
| **Tính bất biến (Immutability)** | Dùng spread operators phức tạp | Tích hợp sẵn Immer, viết code dễ dàng |
| **DevTools**                     | Cần cấu hình thêm              | Tự động kích hoạt                     |
| **Logic Bất đồng bộ (Async)**    | Cần cài riêng Redux-thunk/saga | Tích hợp sẵn `createAsyncThunk`       |

## 🏗️ Các Khái niệm Cốt lõi (Core Concepts)

### 1. `configureStore()`

- Thay thế cho `createStore()`.
- Tự động cài đặt middleware.
- Tích hợp sẵn Redux DevTools.

_(`src/App/Store.jsx`)_

```javascript
import { configureStore } from "@reduxjs/toolkit";
import shopReducer from "../features/shop/shopSlice";

// Khởi tạo store tập trung
export const Store = configureStore({
  reducer: {
    shop: shopReducer,
  },
});
```

### 2. `createSlice()`

- Tạo reducer và actions cùng một lúc.
- Tích hợp thư viện Immer hỗ trợ cập nhật state bất biến một cách an toàn mà không cần dùng spread operator (`...`).
- Tự động tạo action creators.

_(`src/features/shop/shopSlice.jsx`)_

```javascript
import { createSlice } from "@reduxjs/toolkit";

const shopSlice = createSlice({
  name: "shop",
  initialState: {
    products: [], // Danh sách sản phẩm từ API
    cart: [], // Giỏ hàng của user
    status: "idle", // 'idle' | 'loading' | 'succeeded' | 'failed'
    error: null,
  },
  // REDUCERS ĐỒNG BỘ: Thêm/Xóa khỏi giỏ hàng
  reducers: {
    addToCart: (state, action) => {
      // payload là toàn bộ object sản phẩm
      const product = action.payload;
      const existingItem = state.cart.find((item) => item.id === product.id);

      if (existingItem) {
        // 💡 Nhờ có Immer, ta có thể viết code trực tiếp thay đổi state (mutate)
        existingItem.quantity += 1;
      } else {
        state.cart.push({ ...product, quantity: 1 });
      }
    },
    removeFromCart: (state, action) => {
      // payload là ID của sản phẩm cần xóa
      state.cart = state.cart.filter((item) => item.id !== action.payload);
    },
  },
});

// Tự động export các actions để dùng trong component
export const { addToCart, removeFromCart } = shopSlice.actions;

// Export reducer để gắn vào configureStore
export default shopSlice.reducer;
```

### 3. `createAsyncThunk()`

- Xử lý các tác vụ bất đồng bộ (ví dụ: gọi API).
- Tự động tạo các actions theo vòng đời: `pending` (đang chờ), `fulfilled` (thành công), `rejected` (thất bại).

_(`src/features/shop/shopSlice.jsx`)_

```javascript
import { createSlice, createAsyncThunk } from "@reduxjs/toolkit";

// ASYNC THUNK: Gọi API lấy danh sách sản phẩm
export const fetchProducts = createAsyncThunk(
  "shop/fetchProducts",
  async () => {
    // Sử dụng FakeStoreAPI để lấy dữ liệu mẫu
    const response = await fetch("https://fakestoreapi.com/products?limit=4");
    return await response.json();
  },
);

const shopSlice = createSlice({
  name: "shop",
  initialState: { products: [], cart: [], status: "idle", error: null },
  reducers: {
    /* ... addToCart, removeFromCart ... */
  },
  // EXTRA REDUCERS: Xử lý trạng thái của Async Thunk
  extraReducers: (builder) => {
    builder
      .addCase(fetchProducts.pending, (state) => {
        state.status = "loading"; // API đang gọi
      })
      .addCase(fetchProducts.fulfilled, (state, action) => {
        state.status = "succeeded"; // API gọi thành công
        state.products = action.payload; // Lưu dữ liệu trả về
      })
      .addCase(fetchProducts.rejected, (state, action) => {
        state.status = "failed"; // API lỗi
        state.error = action.error.message;
      });
  },
});

export default shopSlice.reducer;
```

### 4. Sử dụng Store trong Component (với `useSelector` và `useDispatch`)

_(`src/App.jsx`)_

```javascript
import { useEffect } from "react";
import { useSelector, useDispatch } from "react-redux";
import { fetchProducts, addToCart, removeFromCart } from "./features/shop/shopSlice";

function App() {
  const dispatch = useDispatch();

  // Trích xuất state từ Redux store
  const { products, cart, status } = useSelector((state) => state.shop);

  // Gọi API (Async Thunk) khi load component
  useEffect(() => {
    if (status === "idle") {
      dispatch(fetchProducts());
    }
  }, [status, dispatch]);

  return (
      // ... render UI ...
      <button onClick={() => dispatch(addToCart(product))}>Thêm vào giỏ</button>
      <button onClick={() => dispatch(removeFromCart(item.id))}>Xóa</button>
  );
}
```

### 5. RTK Query (Kiến thức mở rộng)

_Giải pháp mạnh mẽ để Fetch data (lấy dữ liệu) và caching. Hiện tại dự án của bạn đang dùng `createAsyncThunk`, nhưng nếu chuyển sang dùng RTK Query cho hệ thống Shop thì code sẽ trông như sau:_

```javascript
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";

// Khởi tạo một service RTK Query để lấy sản phẩm
export const shopApi = createApi({
  reducerPath: "shopApi",
  baseQuery: fetchBaseQuery({ baseUrl: "https://fakestoreapi.com/" }),
  endpoints: (builder) => ({
    getProducts: builder.query({
      query: (limit = 4) => `products?limit=${limit}`,
    }),
  }),
});

// RTK Query tự động generate một Hook dựa trên tên endpoint
export const { useGetProductsQuery } = shopApi;
```

## 📊 Thực hành Tốt nhất cho Cấu trúc State (State Structure Best Practices)

```javascript
// ✅ Tốt: Cấu trúc state được chia nhỏ khoa học (Như trong dự án của bạn)
{
  shop: {
    products: [
      { id: 1, title: "Fjallraven - Foldsack", price: 109.95 }
    ],
    cart: [
      { id: 1, title: "Fjallraven - Foldsack", price: 109.95, quantity: 2 }
    ],
    status: "succeeded",
    error: null
  }
}

// ❌ Chưa tốt: Cấu trúc lồng ghép, chưa chuẩn hóa (Nested, denormalized structure)
{
  shop: {
    data: {
      usersCart: [
        { id: 1, title: "Fjallraven - Foldsack", productDetails: { price: 109.95 } }
      ]
    }
  }
}
```

## 🤔 Khi nào nên sử dụng RTK?

**✅ Nên sử dụng khi:**

- Ứng dụng có state phức tạp (như giỏ hàng, thông tin user), nhiều components cần chia sẻ state chung.
- Cần luồng cập nhật state dễ dự đoán (predictable).
- Team lớn, dự án cần tính nhất quán cao.
- Phải xử lý nhiều tác vụ bất đồng bộ (async operations như gọi API).

**❌ Không cần thiết khi:**

- Ứng dụng quá đơn giản, dữ liệu chỉ cần quản lý nội bộ trong từng component bằng `useState`.
- Cần làm bản nguyên mẫu (prototype) nhanh chóng.

## 🔧 Lưu ý về Hiệu suất (Performance Considerations)

**Các kỹ thuật tối ưu hóa:**

- **Selector Memoization:** Sử dụng `useSelector` kết hợp với thư viện `reselect` để tránh render lại không cần thiết.
- **Component Memoization:** Bọc các components bằng `React.memo`.
- **State Normalization:** Giữ cấu trúc state phẳng (flat structure) thay vì lồng ghép quá sâu.
- **Selective Subscriptions:** Components chỉ nên dùng `useSelector` để lấy những state mà chúng thực sự cần (như việc tách riêng `products` và `cart` thay vì gọi chung `state.shop`).
