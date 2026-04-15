# 🎯 Buổi 6: Xử lý Form với React Hook Form + Zod (Nâng cao)

Chào mừng các bạn đến với Buổi 6! Hôm nay, chúng ta sẽ giải quyết triệt để một trong những bài toán "đau đầu" nhất trong React: Quản lý Form và Kiểm tra dữ liệu hợp lệ (Validation) một cách chuyên nghiệp nhất.

---

## I. Vấn đề của Form truyền thống trong React

Thông thường, khi mới học React, chúng ta quản lý Form bằng `useState` (Controlled Components).
- **Nhược điểm chí mạng:** Mỗi khi bạn gõ một phím vào ô Input, hàm `setState` chạy làm **toàn bộ Component bị re-render**. Nếu Form có 20 trường dữ liệu (ví dụ: Form đăng ký CV, Form điền địa chỉ giao hàng), trang web sẽ trở nên vô cùng giật lag. 
- Đồng thời, việc tự viết code kiểm tra lỗi (email thiếu @, mật khẩu quá ngắn, xác nhận mật khẩu không khớp...) bằng các câu lệnh `if/else` lồng nhau rất dài dòng và cực kỳ khó bảo trì.

👉 **Giải pháp tiêu chuẩn công nghiệp:** Sử dụng thư viện **React Hook Form (RHF)** để tối ưu hiệu năng, kết hợp với **Zod** để xác thực dữ liệu.

---

## II. React Hook Form (RHF): Tối ưu hiệu năng

RHF là một thư viện quản lý form siêu nhẹ và hiệu năng cực cao. Bí mật của RHF là nó sử dụng cơ chế **Uncontrolled Components** (dựa trên `useRef` ở dưới nền) nên khi bạn gõ phím, Component **KHÔNG BAO GIỜ** bị re-render vô lý.

### 1. Cài đặt
```bash
npm install react-hook-form
```

### 2. "Mổ xẻ" các hàm cốt lõi của RHF

Để sử dụng, ta gọi Hook `useForm()`. Hook này trả về một object chứa rất nhiều phương thức mạnh mẽ.

#### a. `register(name, options)`
Đây là linh hồn của RHF. Nó dùng để "đăng ký" một thẻ HTML (như `<input>`, `<select>`, `<textarea>`) vào bộ nhớ của RHF.
- `name`: (Bắt buộc) Chuỗi tên của trường dữ liệu (VD: `"email"`, `"password"`).
- `options`: (Tùy chọn) Một object chứa các quy tắc validation tích hợp sẵn của HTML5.

**Cú pháp sử dụng:**
```jsx
// Gắn vào thẻ input bằng Spread Operator (...)
<input {...register("email", { required: true, maxLength: 50 })} />
```
*Lưu ý: RHF hỗ trợ sẵn các rules như `required`, `min`, `max`, `minLength`, `maxLength`, `pattern` (Regex). Tuy nhiên, vì chúng ta sẽ dùng Zod nên thường phần `options` này ta sẽ bỏ trống.*

#### b. `handleSubmit(onSubmit, onError)`
Bọc ngoài hàm xử lý gửi form của bạn. Nó đảm nhiệm 2 việc:
1. Tự động gọi `e.preventDefault()` để chặn hành vi load lại trang.
2. Kiểm tra lỗi (Validation). Nếu KHÔNG có lỗi, nó gọi hàm `onSubmit(data)`. Nếu CÓ lỗi, nó chặn lại và gọi hàm `onError`.

**Cú pháp sử dụng:**
```jsx
const onSubmit = (data) => console.log("Dữ liệu hợp lệ:", data);

<form onSubmit={handleSubmit(onSubmit)}> ... </form>
```

#### c. `formState` (Trạng thái của Form)
Cung cấp toàn bộ thông tin về tình trạng hiện tại của Form. Các thuộc tính hay dùng:
- `errors`: Object chứa thông báo lỗi của các trường chưa hợp lệ.
- `isSubmitting`: Trả về `true` khi hàm `onSubmit` (nếu là async) đang chạy. Dùng để làm hiệu ứng loading cho nút Submit.
- `isDirty`: Trả về `true` nếu người dùng đã chỉnh sửa bất kỳ ô input nào so với giá trị mặc định ban đầu.
- `isValid`: Trả về `true` nếu toàn bộ form không có lỗi nào.

**Cú pháp sử dụng:**
```jsx
const { formState: { errors, isSubmitting } } = useForm();

{errors.email && <p>{errors.email.message}</p>}
<button disabled={isSubmitting}>Gửi dữ liệu</button>
```

#### d. Các hàm tiện ích khác (reset, watch, setValue)
- `reset(values)`: Xóa trắng form hoặc reset form về một tập hợp giá trị mặc định nào đó.
- `watch(name)`: Theo dõi và lấy giá trị của một input theo thời gian thực (Lưu ý: Dùng `watch` sẽ kích hoạt re-render, nên dùng hạn chế).
- `setValue(name, value)`: Chủ động gán giá trị cho một ô input bằng code JS.

---

## III. Zod: Lá chắn bảo vệ dữ liệu (Validation)

**Zod** là thư viện Schema Declaration & Validation ưu việt nhất hiện nay (Đặc biệt khi dùng chung với TypeScript). Nó giúp bạn định nghĩa ra một "Bộ luật" (Schema), ép dữ liệu phải tuân theo bộ luật đó mới được đi tiếp.

### 1. Cài đặt Resolver
Để RHF và Zod có thể "nói chuyện" với nhau, ta cần một trình biên dịch trung gian gọi là resolver.
```bash
npm install zod @hookform/resolvers
```

### 2. Các phương thức kiểm tra thường gặp của Zod

Ta định nghĩa Schema bằng object: `z.object({ ... })`. Dưới đây là các cú pháp thiết lập "luật" hay dùng:

#### Đối với Kiểu Chuỗi (String)
```javascript
import { z } from "zod";

const stringSchema = z.object({
  // Bắt buộc nhập, nếu trống báo lỗi
  username: z.string().min(1, "Không được để trống"), 
  
  // Giới hạn độ dài
  password: z.string().min(6, "Tối thiểu 6 ký tự").max(20, "Tối đa 20 ký tự"), 
  
  // Kiểm tra định dạng Email chuẩn
  email: z.string().email("Email không đúng định dạng"), 
  
  // Kiểm tra URL
  website: z.string().url("Link web không hợp lệ"), 
  
  // Tùy biến bằng Regex (VD: Chỉ chứa chữ và số)
  code: z.string().regex(/^[a-zA-Z0-9]+$/, "Chỉ chấp nhận chữ và số"),
});
```

#### Đối với Kiểu Số (Number)
Lưu ý: Thẻ `<input type="number">` của HTML luôn trả về dữ liệu dạng String. Zod cung cấp hàm `coerce` để tự động ép kiểu từ String sang Number trước khi kiểm tra.
```javascript
const numberSchema = z.object({
  // Ép kiểu sang số, tối thiểu là 18
  age: z.coerce.number().min(18, "Bạn phải đủ 18 tuổi"), 
  
  // Số nguyên (không có thập phân)
  quantity: z.coerce.number().int("Vui lòng nhập số nguyên"), 
});
```

#### Đối với Xác nhận mật khẩu (Refine - So sánh 2 trường)
Đây là tính năng "ăn tiền" nhất của Zod. Làm sao để kiểm tra "Nhập lại mật khẩu" có khớp với "Mật khẩu" không? Ta dùng `.refine()`.

```javascript
const registerSchema = z.object({
  password: z.string().min(6),
  confirmPassword: z.string()
})
// Bọc ngoài object để có thể truy cập 2 trường cùng lúc
.refine((data) => data.password === data.confirmPassword, {
  message: "Mật khẩu nhập lại không khớp!",
  path: ["confirmPassword"], // Chỉ định lỗi sẽ hiển thị ở ô confirmPassword
});
```

---

## IV. Kết hợp hoàn chỉnh RHF và Zod

Dưới đây là một bộ form hoàn chỉnh, kết hợp toàn bộ sức mạnh của RHF và Zod.

```jsx
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import * as z from "zod";

// 1. Tạo Schema với Zod
const mySchema = z.object({
  email: z.string()
    .min(1, "Vui lòng nhập Email")
    .email("Email chưa đúng định dạng"),
  age: z.coerce.number() // Ép kiểu
    .min(18, "Phải trên 18 tuổi"),
});

export default function MyForm() {
  // 2. Khởi tạo Hook
  const { 
    register, 
    handleSubmit, 
    reset,
    formState: { errors } 
  } = useForm({
    resolver: zodResolver(mySchema), // Kết nối Zod
  });

  // 3. Hàm Xử lý khi Form Hợp Lệ
  const onSubmit = (data) => {
    console.log("Dữ liệu an toàn gửi lên Server:", data);
    reset(); // Gửi xong thì xóa trắng form
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      
      {/* Khối Input Email */}
      <div>
        <label>Email:</label>
        <input {...register("email")} placeholder="Nhập email..." />
        {/* Hiển thị lỗi Zod trả về */}
        {errors.email && <p style={{color: 'red'}}>{errors.email.message}</p>}
      </div>

      {/* Khối Input Tuổi */}
      <div>
        <label>Tuổi:</label>
        {/* type="number" nhưng Zod vẫn sẽ coerce sang Number thật */}
        <input type="number" {...register("age")} placeholder="Nhập tuổi..." />
        {errors.age && <p style={{color: 'red'}}>{errors.age.message}</p>}
      </div>
      
      <button type="submit">Xác nhận</button>
    </form>
  );
}
```