# 💻 Bài tập thực hành trên lớp - Buổi 6

**Chủ đề: Xây dựng Form Đăng Nhập "Bất bại" với RHF & Zod**

## 🎯 Mục tiêu

- Biết cách thiết lập một Schema (Bộ quy tắc) kiểm tra lỗi bằng Zod.
- Sử dụng thành thạo `useForm` và `register` để quản lý các ô input mà không cần dùng `useState`.
- Hiển thị được thông báo lỗi chính xác dưới mỗi ô input.

---

## 📝 Yêu cầu chi tiết

**Bước 1: Cài đặt thư viện**
Mở Terminal và cài đặt 3 thư viện cần thiết:

```bash
npm install react-hook-form zod @hookform/resolvers
```

**Bước 2: Xây dựng Zod Schema**
Tạo một component `LoginForm`. Bên ngoài component, khai báo `loginSchema` với các quy tắc sau:

1. `email`: Phải là chuỗi (string), định dạng email chuẩn, nếu sai báo lỗi "Vui lòng nhập đúng định dạng email!". Không được để trống.
2. `password`: Phải là chuỗi, độ dài tối thiểu 8 ký tự, nếu sai báo lỗi "Mật khẩu phải chứa ít nhất 8 ký tự!".

**Bước 3: Khởi tạo React Hook Form**
Sử dụng `useForm` kết hợp với `zodResolver(loginSchema)` để lấy ra `register`, `handleSubmit`, và `errors`.

**Bước 4: Dựng UI và Ghép nối**

1. Tạo một thẻ `<form>`.
2. Tạo 2 thẻ `<input>` cho Email và Password. Gắn `{...register('tên_trường')}` vào từng input tương ứng.
3. Bên dưới mỗi input, viết điều kiện: Nếu có lỗi ở trường đó, hiển thị thẻ `<p>` màu đỏ chứa nội dung lỗi.
4. Nút Submit. Khi form hợp lệ, in dữ liệu lấy được ra Console hoặc `alert` lên màn hình.

---
