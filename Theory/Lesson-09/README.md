# Lý thuyết Redux Toolkit (RTK) - Redux Hiện đại

## 🎯 Tổng quan
Redux Toolkit (RTK) là bộ công cụ chính thức để viết logic Redux một cách hiệu quả. RTK được thiết kế để giải quyết các vấn đề phổ biến của Redux truyền thống:
* Quá nhiều boilerplate code (code lặp đi lặp lại)
* Cấu hình store phức tạp
* Cần cài đặt nhiều thư viện (packages) bổ sung
* Cập nhật state bất biến (immutable updates) khó viết

## 📦 Cài đặt Thư viện (Installation)
Để bắt đầu sử dụng Redux Toolkit trong dự án React, bạn cần cài đặt 2 thư viện chính:
* `@reduxjs/toolkit`: Chứa phần lõi của RTK (configureStore, createSlice, thunk,...).
* `react-redux`: Thư viện cung cấp các hooks (`useSelector`, `useDispatch`) giúp kết nối Redux store với React components.

Sử dụng công cụ quản lý package tương ứng của dự án (NPM, Yarn hoặc pnpm) để cài đặt:

**Sử dụng NPM:**
```bash
npm install @reduxjs/toolkit react-redux
```

**Sử dụng Yarn:**
```bash
yarn add @reduxjs/toolkit react-redux
```

**Sử dụng pnpm:**
```bash
pnpm add @reduxjs/toolkit react-redux
```

## 🔄 Sơ đồ Luồng Redux (Redux Flow Diagram)

```text
┌─────────────┐    dispatch(action)    ┌─────────────┐
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
│(configureStore)│                     │ (Đã cập nhật)│
└─────────────┘                       └─────────────┘
```

## 🆚 RTK vs Redux Truyền thống

| Tiêu chí | Redux Truyền thống | Redux Toolkit |
| :--- | :--- | :--- |
| **Code lặp lại (Boilerplate)** | Nhiều code lặp lại | Tối thiểu code lặp lại |
| **Cài đặt Store** | Phức tạp, nhiều cấu hình | `configureStore()` đơn giản |
| **Reducers** | Viết tay, dễ xảy ra lỗi | `createSlice()` tự động tạo |
| **Tính bất biến (Immutability)** | Dùng spread operators phức tạp | Tích hợp sẵn Immer, viết code dễ dàng |
| **DevTools** | Cần cấu hình thêm | Tự động kích hoạt |
| **Logic Bất đồng bộ (Async)** | Cần cài riêng Redux-thunk/saga | Tích hợp sẵn `createAsyncThunk` |

## 🏗️ Các Khái niệm Cốt lõi (Core Concepts)

### 1. `configureStore()`
* Thay thế cho `createStore()`.
* Tự động cài đặt middleware.
* Tích hợp sẵn Redux DevTools.
* Hỗ trợ preloaded state (state khởi tạo sẵn).

```javascript
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from '../features/counter/counterSlice';
import userReducer from '../features/user/userSlice';

// Khởi tạo store tập trung
export const store = configureStore({
  reducer: {
    counter: counterReducer,
    user: userReducer,
  },
});
```

### 2. `createSlice()`
* Tạo reducer và actions cùng một lúc.
* Tích hợp thư viện Immer hỗ trợ cập nhật state bất biến (immutable updates) một cách an toàn mà không cần dùng spread operator (`...`).
* Tự động tạo action creators.

```javascript
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => {
      // 💡 Nhờ có Immer, ta có thể viết code trực tiếp thay đổi state (mutate)
      // Immer sẽ ngầm tự động xử lý tính bất biến (immutability) ở phía dưới
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload; // action.payload chứa dữ liệu truyền vào
    },
  },
});

// Tự động export các actions để dùng trong component
export const { increment, decrement, incrementByAmount } = counterSlice.actions;

// Export reducer để gắn vào configureStore
export default counterSlice.reducer;
```

### 3. `createAsyncThunk()`
* Xử lý các tác vụ bất đồng bộ (ví dụ: gọi API).
* Tự động tạo các actions theo vòng đời: `pending` (đang chờ), `fulfilled` (thành công), `rejected` (thất bại).
* Tích hợp sẵn bộ xử lý lỗi (error handling).

```javascript
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

// Tạo async thunk để fetch dữ liệu từ API
export const fetchUser = createAsyncThunk(
  'user/fetchUser',
  async (userId) => {
    const response = await fetch(`[https://jsonplaceholder.typicode.com/users/$](https://jsonplaceholder.typicode.com/users/$){userId}`);
    return await response.json();
  }
);

const userSlice = createSlice({
  name: 'user',
  initialState: { data: null, status: 'idle', error: null },
  reducers: {},
  // Xử lý các action được tạo ra bởi createAsyncThunk
  extraReducers: (builder) => {
    builder
      .addCase(fetchUser.pending, (state) => {
        state.status = 'loading'; // API đang gọi
      })
      .addCase(fetchUser.fulfilled, (state, action) => {
        state.status = 'succeeded'; // API gọi thành công
        state.data = action.payload; // Lưu dữ liệu trả về
      })
      .addCase(fetchUser.rejected, (state, action) => {
        state.status = 'failed'; // API lỗi
        state.error = action.error.message;
      });
  },
});

export default userSlice.reducer;
```

### 4. RTK Query
* Giải pháp mạnh mẽ để Fetch data (lấy dữ liệu) và caching (lưu bộ nhớ đệm).
* Tránh việc phải viết quá nhiều `createAsyncThunk` cho các API đơn giản.
* Tự động sinh ra các custom React Hooks (nếu dùng React).

```javascript
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';

// Khởi tạo một service RTK Query
export const pokemonApi = createApi({
  reducerPath: 'pokemonApi', // Tên state lưu trong store
  baseQuery: fetchBaseQuery({ baseUrl: '[https://pokeapi.co/api/v2/](https://pokeapi.co/api/v2/)' }),
  endpoints: (builder) => ({
    getPokemonByName: builder.query({
      query: (name) => `pokemon/${name}`,
    }),
  }),
});

// RTK Query tự động generate một Hook dựa trên tên endpoint
// Có thể import hook này trực tiếp vào component để dùng luôn!
export const { useGetPokemonByNameQuery } = pokemonApi;
```

## 📊 Thực hành Tốt nhất cho Cấu trúc State (State Structure Best Practices)

```javascript
// ✅ Tốt: Cấu trúc state được chuẩn hóa (Normalized state structure)
{
  auth: {
    user: { id: 1, name: "John" },
    isAuthenticated: true,
    loading: false
  },
  todos: {
    items: { 1: { id: 1, text: "Learn RTK" } },
    filter: "all",
    loading: false
  },
  ui: {
    theme: "dark",
    sidebarOpen: false
  }
}

// ❌ Chưa tốt: Cấu trúc lồng ghép, chưa chuẩn hóa (Nested, denormalized structure)
{
  user: {
    profile: {
      todos: [
        { id: 1, text: "Learn RTK", user: { id: 1, name: "John" } }
      ]
    }
  }
}
```

## 🤔 Khi nào nên sử dụng RTK?

**✅ Nên sử dụng khi:**
* Ứng dụng có state phức tạp, nhiều components cần chia sẻ state chung.
* Cần luồng cập nhật state dễ dự đoán (predictable).
* Team lớn, dự án cần tính nhất quán cao.
* Cần tính năng gỡ lỗi xuyên thời gian (time-travel debugging).
* Phải xử lý nhiều tác vụ bất đồng bộ (async operations) phức tạp.

**❌ Không cần thiết khi:**
* Ứng dụng quá đơn giản, ít state toàn cục.
* Dữ liệu chỉ cần quản lý nội bộ trong từng component (local component state).
* Cần làm bản nguyên mẫu (prototype) nhanh chóng.
* Team nhỏ, yêu cầu dự án đơn giản.

## 🔧 Lưu ý về Hiệu suất (Performance Considerations)

**Các kỹ thuật tối ưu hóa:**
* **Selector Memoization (Ghi nhớ Selector):** Sử dụng `useSelector` kết hợp với thư viện `reselect` để tránh render lại không cần thiết.
* **Component Memoization (Ghi nhớ Component):** Bọc các components bằng `React.memo`.
* **State Normalization (Chuẩn hóa State):** Giữ cấu trúc state phẳng (flat structure) thay vì lồng ghép quá sâu.
* **Selective Subscriptions (Đăng ký chọn lọc):** Components chỉ nên dùng `useSelector` để lấy những state mà chúng thực sự cần.
* **RTK Query Caching:** Tận dụng khả năng tự động lưu cache và vô hiệu hóa cache của RTK Query để giảm tải server và tối ưu trải nghiệm UI.