# 💻 Bài tập về nhà - Buổi 5
**Chủ đề: Tối ưu hóa Hiệu năng với `useMemo` & `useCallback`**

## 🎯 Mục tiêu
- Hiểu được nguyên nhân vì sao một Component bị re-render mất kiểm soát.
- Biết cách dùng `useMemo` để "nhớ" kết quả của một phép tính nặng.
- Biết cách dùng `useCallback` kết hợp `React.memo` để tránh re-render Component con.

---

## 📝 Yêu cầu chi tiết

**Bước 1: Chuẩn bị Component Con (Nút Thanh Toán)**
1. Tạo một Functional Component tên là `CheckoutButton`, nhận vào một prop là hàm `onCheckout`.
2. Bên trong Component này, đặt một lệnh `console.log(" Nút Thanh Toán vừa bị re-render!");` để theo dõi.
3. **Quan trọng:** Bọc Component này bằng `React.memo(...)` trước khi export để nó chỉ re-render khi props thay đổi.

**Bước 2: Xây dựng Component Cha (`App`)**
1. Khai báo 2 state: `keyword` (chữ) và `cartCount` (số, mặc định là 0).
2. Tạo một ô `<input>` để người dùng nhập `keyword`. (Mỗi khi gõ, thẻ `App` sẽ bị re-render).
3. Tạo nút "Thêm vào giỏ" để tăng `cartCount` lên 1.

**Bước 3: Thực hành `useMemo` (Tránh tính toán lại)**
1. Giả lập một mảng dữ liệu khổng lồ (VD: 10,000 phần tử).
2. Dùng `.filter()` đếm xem có bao nhiêu số chẵn trong mảng đó. Nhét thêm lệnh `console.log("⚙️ Đang chạy vòng lặp 10,000 lần...");` vào trước khi return.
3. **Yêu cầu:** Bọc toàn bộ phép tính này vào `useMemo` để khi bạn gõ text vào ô `keyword`, vòng lặp 10,000 lần kia KHÔNG bị chạy lại.

**Bước 4: Thực hành `useCallback` (Tránh tạo lại hàm)**
1. Khai báo hàm `handleCheckout` (hiển thị alert thông báo tổng số hàng trong giỏ `cartCount`).
2. Gắn hàm này vào prop `onCheckout` của `CheckoutButton`.
3. **Yêu cầu:** Bọc hàm này bằng `useCallback`. Test thử: khi gõ text vào ô `keyword`, chữ `" Nút Thanh Toán vừa bị re-render!"` không được phép hiện lên console.

---

## IV. Giải ngố: Ý nghĩa luồng hoạt động & Tại sao phải code như vậy?

Khi mới học React, chúng ta thường nghĩ: *"Cứ đổi State là giao diện tự cập nhật, quá nhàn!"*. Nhưng sự "nhàn" đó đi kèm với một cái giá rất đắt về hiệu năng nếu bạn không hiểu rõ luồng hoạt động dưới đây.

### 1. Phân tích "Hiệu ứng Domino" của React (Nỗi đau)
Mặc định, React hoạt động theo nguyên tắc **Domino (Thác nước)**. 
Khi bạn gõ một chữ vào ô input (làm thay đổi state `keyword`), Component `App` sẽ bị kích hoạt re-render. Khi `App` re-render, nó sẽ bắt **TẤT CẢ** những thứ bên trong nó phải chạy lại từ đầu:
- Vòng lặp đếm 10,000 số chẵn bị ép chạy lại (dù mảng số không hề thay đổi).
- Hàm `handleCheckout` bị xóa đi và tạo lại thành một hàm mới tinh trong bộ nhớ.
- Component `<CheckoutButton />` bị ép vẽ lại (dù nút này chả liên quan gì đến cái ô input kia).

👉 **Hậu quả:** Trang web bị giật lag, gõ phím bị khựng (delay) vì máy tính bận xử lý những phép tính thừa thãi.

### 2. Sự cứu rỗi từ `useMemo` và `useCallback` (Giải pháp & Điểm lợi)

Thông qua bài tập này, chúng ta đã biến Component `App` thành một cỗ máy tối ưu hóa với 2 Hook:

| Hook | Áp dụng vào đâu? | Điểm lợi thu được (Ăn tiền ở đây!) |
| :--- | :--- | :--- |
| **`useMemo`** <br/> *(Nhớ Giá Trị)* | Phép tính đếm 10,000 số chẵn. | **Chống giật lag (Anti-Freeze):** Thay vì phải đếm lại 10,000 lần mỗi khi bạn gõ 1 phím, `useMemo` đã "chụp hình" lại kết quả cũ. Khi bạn gõ phím, React sẽ bỏ qua vòng lặp này và lấy thẳng kết quả cũ ra dùng. Trải nghiệm gõ phím trở nên mượt mà ngay lập tức. |
| **`useCallback`** <br/> *(Nhớ Hàm)* | Hàm `handleCheckout` truyền xuống nút bấm. | **Bảo vệ Component Con:** Bằng cách giữ nguyên địa chỉ bộ nhớ của hàm `handleCheckout`, Component con (`CheckoutButton` đã được bọc `memo`) sẽ nhận ra: *"À, cái prop này vẫn là hàm cũ, không có gì mới cả!"*. Nhờ đó, nó nằm im và từ chối re-render vô lý, giúp tiết kiệm tài nguyên vẽ giao diện. |

**💡 Bài học rút ra:** Không phải lúc nào cũng lạm dụng `useMemo` và `useCallback` (vì bản thân chúng cũng tốn một chút RAM để ghi nhớ). Hãy chỉ dùng chúng khi bạn phải đối mặt với **Dữ liệu lớn (Heavy Calculation)** hoặc khi cần **Truyền hàm xuống Component con (Prevent Child Re-render)**!
---