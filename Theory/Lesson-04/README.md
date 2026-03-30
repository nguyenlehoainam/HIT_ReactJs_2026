# 🎯 Buổi 4: Giao tiếp Backend nâng cao & useRef

Chào mừng các bạn đến với Buổi 4! Trong buổi này, chúng ta sẽ làm quen với một Hook rất thú vị là `useRef` để thao tác trực tiếp với các phần tử HTML (DOM). Sau đó, chúng ta sẽ "nâng cấp" cách gọi API lên một tầm cao mới chuyên nghiệp hơn bằng thư viện `Axios` và tư duy tách biệt Service Layer.

---

## I. Tìm hiểu Hook `useRef`

`useRef` là một Hook của React cho phép bạn tham chiếu (reference) trực tiếp đến một phần tử DOM hoặc lưu trữ một giá trị có thể thay đổi (mutable value) mà **không làm Component bị re-render** khi giá trị đó thay đổi.

### 1. Thao tác trực tiếp với DOM
Mặc dù React sử dụng Virtual DOM và khuyến khích quản lý UI thông qua State, nhưng đôi khi bạn vẫn cần can thiệp trực tiếp vào thẻ HTML thật (Ví dụ: Tự động focus vào ô input khi vào trang, lấy kích thước của một thẻ, thao tác với video player...).

**Ví dụ: Tự động Focus vào ô Input**
```jsx
import { useRef } from 'react';

export default function FocusInput() {
  // 1. Khởi tạo một ref với giá trị null
  const inputRef = useRef(null);

  const handleFocus = () => {
    // 2. Truy cập vào thẻ HTML thông qua thuộc tính .current
    inputRef.current.focus();
    inputRef.current.style.backgroundColor = 'yellow';
  };

  return (
    <div>
      {/* 3. Gắn ref vào thẻ input */}
      <input ref={inputRef} type="text" placeholder="Nhập tên..." />
      <button onClick={handleFocus}>Focus vào ô này!</button>
    </div>
  );
}
```

### 2. Sự khác biệt giữa `useRef` và `useState`
- **useState:** Cập nhật state -> Component bị re-render -> Giao diện thay đổi.
- **useRef:** Cập nhật `ref.current` -> Component **KHÔNG** bị re-render -> Giao diện giữ nguyên. Do đó, `useRef` rất thích hợp để lưu các biến bộ đếm ngầm, ID của `setInterval`/`setTimeout`, hoặc lưu giá trị cũ trước khi render.

---

## II. Giao tiếp Backend chuyên nghiệp với Axios

Ở Buổi 3, chúng ta đã dùng hàm `fetch()` có sẵn của trình duyệt. Tuy nhiên, khi làm dự án lớn, người ta thường dùng thư viện `Axios` vì nó ngắn gọn hơn, tự động chuyển đổi dữ liệu sang JSON và cung cấp các tính năng mạnh mẽ như Interceptors.

### 1. Cài đặt Axios
Mở terminal và chạy lệnh:
```bash
npm install axios
```

### 2. Cấu hình Axios Instance
Thay vì phải gọi URL lặp đi lặp lại ở mỗi file (VD: `https://api.domain.com/users`, `https://api.domain.com/products`), chúng ta sẽ tạo một "bản sao" (Instance) cấu hình sẵn các thông số chung.

Tạo file `src/utils/axiosInstance.js`:
```javascript
import axios from 'axios';

// Khởi tạo một instance với các cấu hình mặc định
const axiosInstance = axios.create({
  baseURL: '[https://dummyjson.com](https://dummyjson.com)', // Base URL của API
  timeout: 5000, // Nếu API gọi lâu hơn 5 giây thì tự động ngắt
  headers: {
    'Content-Type': 'application/json',
  },
});

export default axiosInstance;
```

### 3. Thiết lập Interceptors (Người bảo vệ)
**Interceptors** cho phép bạn "chặn" và can thiệp vào Request (trước khi gửi đi) hoặc Response (khi nhận về từ Server) trên toàn cục.

Cập nhật file `axiosInstance.js` ở trên:
```javascript
// ... (code tạo instance ở trên)

// Thêm một Interceptor cho Request
axiosInstance.interceptors.request.use(
  (config) => {
    // Trước khi gửi đi, đính kèm Token (nếu có) vào header
    const token = localStorage.getItem('access_token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// Thêm một Interceptor cho Response
axiosInstance.interceptors.response.use(
  (response) => {
    // Bắt và xử lý chung dữ liệu thành công (bỏ qua các lớp bọc dư thừa của Axios)
    return response.data;
  },
  (error) => {
    // Bắt lỗi chung toàn hệ thống (VD: 401 Unauthorized -> Đẩy ra trang Login)
    if (error.response && error.response.status === 401) {
      alert("Phiên đăng nhập hết hạn, vui lòng đăng nhập lại!");
      // Logic logout ở đây...
    }
    return Promise.reject(error);
  }
);

export default axiosInstance;
```

---

## III. Tư duy tách biệt Service Layer

Nguyên tắc "vàng" trong React là: **Component chỉ nên lo việc hiển thị (UI) và nhận tương tác, KHÔNG NÊN chứa quá nhiều logic gọi API lộn xộn.**

### 1. Vấn đề của cách viết cũ (Bad Practice)
Viết thẳng `fetch` hoặc `axios` vào trong `useEffect` của Component sẽ làm file Component rất dài, khó bảo trì và khó tái sử dụng API đó ở Component khác.

### 2. Cách giải quyết: Tách Service Layer (Good Practice)
Chúng ta sẽ tạo một thư mục `src/services/` chứa các hàm chuyên gọi API, sau đó Component chỉ việc gọi hàm đó ra dùng.

**Bước 1: Tạo file `src/services/productService.js`**
```javascript
import axiosInstance from '../utils/axiosInstance';

export const productService = {
  // Hàm lấy danh sách tất cả sản phẩm
  getAllProducts: () => {
    return axiosInstance.get('/products'); // URL thực tế là: baseURL + /products
  },
  
  // Hàm lấy chi tiết 1 sản phẩm theo ID
  getProductById: (id) => {
    return axiosInstance.get(`/products/${id}`);
  }
};
```

**Bước 2: Sử dụng gọn gàng trong Component**
```jsx
import { useState, useEffect } from 'react';
import { productService } from '../services/productService'; // Import Service

export default function ProductList() {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    const fetchProducts = async () => {
      try {
        // Component bây giờ rất sạch sẽ, chỉ việc gọi Service
        const data = await productService.getAllProducts(); 
        setProducts(data.products);
      } catch (error) {
        console.error("Lỗi:", error);
      }
    };

    fetchProducts();
  }, []);

  return (
    // ... Render giao diện ...
  );
}
```
Nhờ cách này, nếu Backend đổi đường dẫn URL, bạn chỉ cần vào sửa duy nhất file `productService.js` thay vì phải đi mò tìm ở hàng chục file Component khác nhau!