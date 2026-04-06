# 🎯 Buổi 5: Tối ưu Hiệu năng & Xử lý lỗi hệ thống

Chào mừng các bạn đến với Buổi 5! Trong buổi này, chúng ta sẽ học cách làm cho ứng dụng React chạy mượt mà hơn bằng cách kiểm soát quá trình render lại (re-render), đồng thời học cách xử lý lỗi thanh lịch để web không bị "chết trắng" khi có lỗi xảy ra.

---

## I. Bản chất của Re-render trong React

**Re-render** là quá trình React chạy lại code của một Component để cập nhật giao diện mới. Một Component sẽ bị re-render trong 3 trường hợp chính:

1. **State thay đổi:** Khi bạn gọi hàm `setState`.
2. **Props thay đổi:** Khi dữ liệu truyền từ cha xuống con bị thay đổi.
3. **Component Cha bị re-render:** ĐÂY LÀ NGUYÊN NHÂN CHÍNH GÂY CHẬM APP! Theo mặc định, khi cha re-render, TOÀN BỘ các component con bên trong cũng sẽ bị re-render theo, bất kể props của chúng có đổi hay không.

---

## II. Tối ưu hóa với `useMemo` và `useCallback`

Để ngăn chặn việc tính toán lại những thứ không cần thiết khi Component bị re-render, chúng ta sử dụng bộ đôi Hook tối ưu hóa.

### 1. `useMemo`: Ghi nhớ một "Giá trị"

Dùng để "cache" (lưu nháp) kết quả của một phép tính nặng. Nếu các biến phụ thuộc (dependency array) không đổi, React sẽ lấy kết quả cũ ra dùng thay vì tính lại từ đầu.

- **Khi nào dùng:** Lọc một danh sách (array) khổng lồ, tính toán các biểu thức toán học phức tạp.

```jsx
import { useMemo, useState } from 'react';

export default function ExpensiveComponent({ items }) {
  const [keyword, setKeyword] = useState('');

  // Thay vì .filter trực tiếp (sẽ chạy lại mỗi khi gõ phím), ta bọc trong useMemo
  const filteredItems = useMemo(() => {
    console.log("Đang lọc danh sách nặng...");
    return items.filter(item => item.name.includes(keyword));
  }, [items, keyword]); // Chỉ tính lại khi items hoặc keyword thay đổi

  return ( /* ... giao diện ... */ );
}
```

### 2. `useCallback`: Ghi nhớ một "Hàm"

Mỗi lần Component re-render, các hàm (function) định nghĩa bên trong nó sẽ được tạo lại bằng một địa chỉ bộ nhớ mới. Việc này khiến Component con nhận hàm đó qua Props hiểu nhầm là Props bị đổi -> Con cũng re-render. `useCallback` giúp giữ nguyên địa chỉ bộ nhớ của hàm đó.

- **Khi nào dùng:** Khi cần truyền một function từ Cha xuống Con qua Props, KẾT HỢP với việc Component Con đã được bọc bằng `React.memo`.

```jsx
import { useState, useCallback, memo } from "react";

// Bọc component Con bằng memo để chặn re-render nếu props không đổi
const ChildButton = memo(({ onClick }) => {
  console.log("Nút Con bị render lại!");
  return <button onClick={onClick}>Bấm tôi</button>;
});

export default function Parent() {
  const [text, setText] = useState("");

  // Giữ nguyên hàm này không bị tạo lại mỗi khi Parent re-render do gõ text
  const handleClick = useCallback(() => {
    alert("Đã bấm!");
  }, []);

  return (
    <div>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <ChildButton onClick={handleClick} />
    </div>
  );
}
```

---
