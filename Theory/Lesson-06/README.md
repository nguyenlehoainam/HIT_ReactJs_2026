# 🎯 Buổi 6: Xử lý Form với React Hook Form + Zod

Chào mừng các bạn đến với Buổi 7! Hôm nay, chúng ta sẽ giải quyết triệt để một trong những bài toán "đau đầu" nhất trong React: Quản lý Form và Kiểm tra dữ liệu hợp lệ (Validation).

---

## I. Vấn đề của Form truyền thống trong React

Thông thường, khi mới học React, chúng ta quản lý Form bằng `useState` (Controlled Components).
- **Nhược điểm:** Mỗi khi bạn gõ một phím vào ô Input, state thay đổi làm toàn bộ Component bị **re-render**. Nếu Form có 20 trường dữ liệu (ví dụ: Form đăng ký CV), trang web sẽ trở nên vô cùng giật lag. Đồng thời, việc tự viết code kiểm tra lỗi (email thiếu @, mật khẩu quá ngắn...) bằng các câu lệnh `if/else` rất dài dòng và khó bảo trì.

👉 **Giải pháp:** Sử dụng thư viện **React Hook Form (RHF)** kết hợp với **Zod**.

---

## II. React Hook Form (RHF) là gì?

RHF là một thư viện quản lý form hiệu năng cao. Nó sử dụng cơ chế *Uncontrolled Components* (dựa trên `useRef` ở dưới nền) nên khi bạn gõ phím, Component **KHÔNG** bị re-render.

### 1. Cài đặt
```bash
npm install react-hook-form
```

### 2. Các hàm cốt lõi của RHF
Chúng ta sẽ sử dụng hook `useForm()` để lấy ra các công cụ cần thiết:
- `register`: Dùng để "đăng ký" một thẻ `<input>` vào hệ thống quản lý của RHF.
- `handleSubmit`: Bọc ngoài hàm xử lý gửi form của bạn. Nó sẽ tự động chặn hành vi reload trang mặc định.
- `formState: { errors }`: Chứa toàn bộ thông báo lỗi của form.

---

## III. Kiểm tra dữ liệu (Validation) với Zod

**Zod** là một thư viện giúp bạn định nghĩa ra một "Bộ quy tắc" (Schema) cho dữ liệu. RHF sẽ nhìn vào bộ quy tắc này để biết khi nào thì báo lỗi.

### 1. Cài đặt
Để Zod có thể "nói chuyện" được với RHF, ta cần cài thêm bộ phân giải (resolver).
```bash
npm install zod @hookform/resolvers
```

### 2. Cú pháp cơ bản của Zod
Thay vì tự viết if/else, ta định nghĩa bộ quy tắc rất "người":
```javascript
import { z } from "zod";

const loginSchema = z.object({
  email: z.string().email("Email không hợp lệ!"), // Bắt buộc là chuỗi và đúng chuẩn email
  password: z.string().min(6, "Mật khẩu phải có ít nhất 6 ký tự"),
});
```

### 3. Ghép nối RHF và Zod
```jsx
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

// 1. Tạo Schema
const schema = z.object({
  username: z.string().min(3, "Tên quá ngắn"),
});

export default function MyForm() {
  // 2. Khởi tạo form với resolver
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: zodResolver(schema),
  });

  const onSubmit = (data) => console.log("Dữ liệu hợp lệ:", data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {/* 3. Đăng ký input */}
      <input {...register("username")} placeholder="Nhập tên..." />
      
      {/* 4. Hiển thị lỗi */}
      {errors.username && <p style={{color: 'red'}}>{errors.username.message}</p>}
      
      <button type="submit">Gửi</button>
    </form>
  );
}
```