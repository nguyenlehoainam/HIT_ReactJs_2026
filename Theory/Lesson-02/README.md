# 🎯 Buổi 2: Luồng dữ liệu và Tương tác UI (State, Props & Lists)

Chào mừng các bạn đến với buổi 2! Hôm nay chúng ta sẽ tìm hiểu cách React ghi nhớ thông tin (State), cách các Component giao tiếp với nhau (Props) và cách hiển thị một danh sách dữ liệu lên giao diện.

---

## I. Quản lý trạng thái với `useState`

`useState` là một React Hook cho phép bạn thêm một **state variable** (biến trạng thái) vào trong component. 
- **State** là bộ nhớ của component, chứa các thông tin có thể thay đổi trong quá trình người dùng tương tác. Khi state thay đổi, React sẽ tự động cập nhật (re-render) lại giao diện.

### 1. Cú pháp cơ bản
```jsx
import { useState } from 'react';

const [state, setState] = useState(initialState);
```
- **initialState**: Giá trị khởi tạo ban đầu của state (chỉ dùng cho lần render đầu tiên).
- **useState trả về một mảng gồm 2 phần tử**:
  1. `state`: Giá trị hiện tại của state.
  2. `setState`: Hàm dùng để cập nhật giá trị mới cho state và kích hoạt quá trình re-render.

### 2. Những lưu ý sống còn khi dùng `useState`
- Chỉ được gọi Hook ở phần trên cùng (top-level) của Component. Không gọi trong vòng lặp hay câu lệnh điều kiện (if/else).
- Trong chế độ Strict Mode (mặc định khi tạo app mới), React có thể gọi hàm cập nhật 2 lần ở môi trường dev để giúp bạn tìm lỗi.
- **Quan trọng:** Hàm `setState` hoạt động theo cơ chế bất đồng bộ. Nó sẽ không làm thay đổi giá trị của state hiện tại ngay lập tức sau khi gọi, mà sẽ lên lịch cho lần render tiếp theo.

### 3. Ví dụ thực hành
**Ví dụ 1: Bộ đếm (Counter)**
Mỗi lần nhấn nút, số đếm sẽ tăng thêm 1 đơn vị.

```jsx
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Bạn đã nhấn vào đây {count} lần
    </button>
  );
}
```

**Ví dụ 2: Ô nhập văn bản (Text Field)**
Khi thay đổi nội dung trong ô input, dòng chữ bên dưới cũng thay đổi theo (Two-way data binding cơ bản).

```jsx
import { useState } from 'react';

export default function TextField() {
  const [text, setText] = useState('hello');

  return (
    <div>
      <input 
        value={text} 
        onChange={(e) => setText(e.target.value)} 
      />
      <p>Nội dung ô input: "{text}"</p>
      <button onClick={() => setText('')}>Đặt lại</button>
    </div>
  );
}
```

---

## II. Truyền dữ liệu với Props


- **Props** (viết tắt của Properties) là những thông tin được truyền từ Component cha xuống Component con.
- Khác với State, Props là **chỉ đọc (read-only)**. Component con không được phép tự sửa đổi Props mà nó nhận được.

### 1. Cách truyền và nhận Props
Giả sử chúng ta có component `Profile` (cha) muốn truyền dữ liệu cho component `Avatar` (con).

```jsx
// Component Cha truyền props
export default function Profile() {
  return (
    <Avatar
      person={{ name: 'Lin Lanying', imageId: '1bX5QH6' }}
      size={100}
    />
  );
}

// Component Con nhận và sử dụng props thông qua Destructuring
function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={`https://i.imgur.com/${person.imageId}.jpg`}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}
```

### 2. Prop đặc biệt: `children`
`children` là một prop đặc biệt dùng để truyền các nội dung (có thể là text, thẻ HTML, hoặc các component khác) nằm giữa thẻ đóng và thẻ mở của một component.

```jsx
// Tạo một component Card nhận children
function Card({ children }) {
  return (
    <div className="card-container">
      {children}
    </div>
  );
}

// Sử dụng component Card
export default function App() {
  return (
    <Card>
      <h2>Tiêu đề thẻ</h2>
      <p>Nội dung này chính là prop children được truyền vào Card.</p>
    </Card>
  );
}
```

---

## III. Render danh sách (Rendering Lists)

Trong thực tế, bạn sẽ hiếm khi viết tay từng thẻ HTML lặp đi lặp lại. Thay vào đó, chúng ta sẽ quản lý dữ liệu bằng mảng (Array) và dùng hàm `map()` của JavaScript để hiển thị chúng lên UI.

### 1. Cách dùng hàm `map()` hiển thị mảng
```jsx
const fruits = ['Táo', 'Chuối', 'Cam'];

export default function FruitList() {
  return (
    <ul>
      {fruits.map((fruit, index) => (
        <li key={index}>{fruit}</li>
      ))}
    </ul>
  );
}
```

### 2. Tầm quan trọng của prop `key`
Khi render một danh sách, React yêu cầu bạn phải cung cấp một prop tên là `key` cho phần tử ngoài cùng bên trong hàm `map()`.
- **Tại sao cần `key`?** Cung cấp `key` giúp React xác định chính xác phần tử nào đã thay đổi, được thêm vào hoặc bị xóa đi trong danh sách, từ đó tối ưu hóa việc cập nhật lại Virtual DOM.
- **Lưu ý:** `key` phải là duy nhất giữa các anh chị em trong cùng một mảng (thường dùng ID từ cơ sở dữ liệu). Hạn chế dùng `index` làm `key` nếu danh sách đó có thể bị thay đổi thứ tự.