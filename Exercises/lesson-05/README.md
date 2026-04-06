# 💻 Bài tập thực hành trên lớp - Buổi 5

**Chủ đề: Tối ưu Danh sách & Bắt lỗi Giao diện**

## 🎯 Mục tiêu

- Phân biệt và ứng dụng được `useMemo` và `useCallback` vào đúng tình huống.
- Trải nghiệm việc dùng `React.memo` để tránh re-render Component con.

---

## 📝 Yêu cầu chi tiết

**Bước 1: Chuẩn bị dữ liệu & Component lỗi**

1. Trong `App`, tạo một nút bấm "Cố tình tạo lỗi".
2. Dùng một state `hasError` (mặc định `false`). Khi bấm nút trên, chuyển state thành `true`.
3. Viết điều kiện: nếu `hasError == true` thì quăng ra một lỗi: `throw new Error("Sập rồi!")`. Hãy thử chạy và xem ErrorBoundary hoạt động.

**Bước 2: Tối ưu với useMemo**

1. Tạo một danh sách số ngẫu nhiên rất dài (VD: mảng 10,000 phần tử).
2. Tạo một input để nhập con số.
3. Dùng `.filter()` để tìm các số trong mảng trùng với số ở input. Cố tình nhét lệnh `console.log("Đang lọc...")` vào đây.
4. **Yêu cầu:** Dùng `useMemo` bọc phép tính lọc này lại để nó không bị chạy vô cớ nếu bạn thêm một ô input gõ Text khác (không liên quan gì đến mảng số).

**Bước 3: Tối ưu với useCallback**

1. Tạo một component con tên là `ExpensiveButton` và bọc nó trong `React.memo(...)`.
2. Component con này nhận 1 prop là hàm `onAddToCart`.
3. Trong component `App`, định nghĩa hàm `handleAddToCart` và dùng `useCallback` bọc nó lại, sau đó truyền xuống `ExpensiveButton`.

---
