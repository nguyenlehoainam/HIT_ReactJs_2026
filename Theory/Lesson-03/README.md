# 🎯 Buổi 3: Vòng đời Component, Side Effects và Styling

Chào mừng các bạn đến với buổi học số 3! Hôm nay chúng ta sẽ đi sâu vào "tuổi thọ" của một Component, cách xử lý các tác vụ bên ngoài (Side Effects) như gọi API, và làm đẹp cho dự án với các thư viện CSS hiện đại.

---

## I. Vòng đời của Component (Component Lifecycle)

Bất kỳ một Component nào trong React cũng đều trải qua một "vòng đời" từ lúc sinh ra cho đến lúc mất đi. Trong Functional Component, vòng đời này được chia làm 3 giai đoạn chính:

1. **Mounting (Sinh ra):** Lúc Component được chèn vào DOM lần đầu tiên. Đây là lúc lý tưởng để gọi API lấy dữ liệu khởi tạo.
2. **Updating (Cập nhật):** Lúc Component bị re-render do State hoặc Props thay đổi.
3. **Unmounting (Chết đi):** Lúc Component bị xóa khỏi DOM. Giai đoạn này dùng để "dọn dẹp" (clear timer, hủy bỏ kết nối...).


---

## II. Xử lý Side Effects với `useEffect`

**Side Effect** là những hành động can thiệp ra bên ngoài phạm vi của một hàm (ví dụ: gọi API lấy dữ liệu từ server, thao tác trực tiếp với DOM, cài đặt setTimeout/setInterval). Trong React, chúng ta quản lý Side Effects bằng Hook `useEffect`.

### 1. Cú pháp cơ bản
```jsx
import { useEffect } from 'react';

useEffect(() => {
  // Thực thi hành động (Effect)
  
  return () => {
    // Dọn dẹp (Cleanup function)
  };
}, [dependencyArray]);
```

### 2. Dependency Array (Mảng phụ thuộc) - "Linh hồn" của useEffect
Cách bạn truyền mảng phụ thuộc sẽ quyết định lúc nào `useEffect` được chạy:

- **Trường hợp 1: Không có mảng phụ thuộc**
  Chạy lại sau *MỖI LẦN* component re-render (Rất ít dùng, dễ gây lặp vô hạn).
  ```jsx
  useEffect(() => {
    console.log("Tôi chạy mỗi khi component render hoặc re-render");
  });
  ```

- **Trường hợp 2: Mảng rỗng `[]`**
  Chỉ chạy *ĐÚNG 1 LẦN DUY NHẤT* khi component Mounting. Thường dùng để gọi API lúc mới vào trang.
  ```jsx
  useEffect(() => {
    console.log("Tôi chỉ chạy 1 lần duy nhất lúc component sinh ra");
  }, []);
  ```

- **Trường hợp 3: Mảng có chứa biến `[deps]`**
  Chạy lần đầu lúc Mounting và chạy lại *MỖI KHI* các biến trong mảng bị thay đổi.
  ```jsx
  useEffect(() => {
    console.log(`Tôi chạy lại vì biến count đã thay đổi thành: ${count}`);
  }, [count]);
  ```

### 3. Gọi API cơ bản với useEffect
```jsx
import { useState, useEffect } from 'react';

export default function UserList() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    // Hàm gọi API
    fetch('[https://jsonplaceholder.typicode.com/users](https://jsonplaceholder.typicode.com/users)')
      .then(response => response.json())
      .then(data => setUsers(data));
      
  }, []); // Mảng rỗng: Chỉ gọi API 1 lần khi load trang

  return (
    <ul>
      {users.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}
```

### 4. Cleanup Function (Hàm dọn dẹp)
Chạy khi component Unmounting hoặc ngay trước khi effect của lần render tiếp theo chạy. Rất quan trọng để tránh rò rỉ bộ nhớ (memory leak).
```jsx
useEffect(() => {
  const timer = setInterval(() => console.log('Đang đếm...'), 1000);

  // Cleanup function: Xóa bộ đếm khi component bị hủy
  return () => {
    clearInterval(timer);
  };
}, []);
```

---

## III. Giới thiệu Styling trong React

Bên cạnh cách viết file `.css` truyền thống, dự án React hiện đại thường kết hợp các công cụ Styling mạnh mẽ hơn.

### 1. SASS (Syntactically Awesome Style Sheets)
SASS là một bộ tiền xử lý CSS, cho phép bạn viết CSS có cấu trúc, biến, vòng lặp và lồng nhau (nesting).
- **Cài đặt:** `npm install sass`
- **Cách dùng:** Chỉ cần đổi đuôi file `.css` thành `.scss` và import bình thường.
```scss
/* file styles.scss */
$primary-color: #3498db;

.card {
  background-color: white;
  
  .title { // Nesting (Lồng code)
    color: $primary-color;
    font-weight: bold;
  }
}
```

### 2. Tailwind CSS
Tailwind CSS là một thư viện Utility-first CSS, cung cấp sẵn các class nhỏ lẻ để bạn xây dựng giao diện trực tiếp ngay trong mã HTML/JSX mà không cần rời sang file CSS.
- Thay vì viết: `<div className="my-card">` và sang file CSS code `padding, margin, background...`
- Dùng Tailwind: `<div className="p-4 m-2 bg-white rounded-lg shadow-md">`
- *Lưu ý: Chúng ta sẽ có hướng dẫn setup Tailwind CSS chi tiết ở phần thực hành.*