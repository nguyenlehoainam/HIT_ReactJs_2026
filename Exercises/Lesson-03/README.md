# 📝 Bài tập về nhà - Buổi 3
**Chủ đề: Xây dựng Giao diện E-commerce, Quản lý Giỏ hàng & Tính năng Dark/Light Mode**

## 🎯 Mục tiêu bài tập
- Thực hành gọi API thực tế bằng Hook `useEffect` (Chỉ gọi 1 lần khi Mounting).
- Quản lý trạng thái phức tạp với `useState`: Lưu danh sách sản phẩm, quản lý dữ liệu **Giỏ hàng** và trạng thái **Giao diện (Theme)**.
- Can thiệp vào DOM bên ngoài React (Side Effect) để thay đổi class giao diện (Dark/Light).
- Truyền dữ liệu giữa các Component thông qua `Props`.
- Thực hành dựng giao diện và Responsive cơ bản bằng **Tailwind CSS** hoặc **SASS**.

---

## 🎨 Tài nguyên
- **Giao diện tham khảo:** `https://nguyenlehoainam.github.io/Web-Private/Week11/BTVN/index.html`
- **API sử dụng:** `https://dummyjson.com/products` (API này sẽ trả về một object, danh sách sản phẩm nằm trong trường `products`).

---

## 📝 Yêu cầu chi tiết

### Nhiệm vụ 1: Header & Chế độ Dark/Light Mode
1. Xây dựng **Header**: Chứa Logo HIT bên trái và Nút chuyển đổi (Toggle Button) Dark/Light bên phải.
2. Khai báo state `theme` (giá trị khởi tạo có thể là `'light'`). Khi click vào nút Toggle, state này sẽ chuyển đổi qua lại giữa `'light'` và `'dark'`.
3. Sử dụng `useEffect` để bắt sự thay đổi của biến `theme`: Mỗi khi `theme` thay đổi, hãy dùng mã JS thuần (`document.body.classList.add/remove`) để gắn thêm/xóa class `dark` ở thẻ `body`. 
4. *(Nâng cao - Không bắt buộc)*: Sử dụng `localStorage` bên trong `useEffect` để lưu lại lựa chọn theme của người dùng. F5 lại trang vẫn giữ nguyên theme.

### Nhiệm vụ 2: Fetch API Danh sách Sản phẩm
1. Khai báo state `products` và dùng `useEffect` (với dependency rỗng `[]`) để gọi hàm `fetch()` lấy dữ liệu từ URL: `https://dummyjson.com/products` ngay lúc component Mounting.
2. Render danh sách sản phẩm ra khu vực bên trái màn hình dưới dạng Grid (3 cột hoặc 4 cột tùy kích thước màn hình).
3. Mỗi **ProductCard** bao gồm:
   - Ảnh sản phẩm: Dùng trường `thumbnail`. Căn giữa ảnh.
   - Danh mục: Dùng trường `category`. (Chữ nhỏ, in hoa, màu xám).
   - Tên sản phẩm: Dùng trường `title`. (Chữ đậm. Lưu ý đổi sang màu sáng nếu ở chế độ Dark Mode).
   - Giá tiền: Dùng trường `price`. **Lưu ý:** Giá từ API là USD, các bạn cần dùng JS nhân với tỷ giá (VD: `price * 25000`), sau đó format hiển thị theo chuẩn VND (VD: `34.990.000 đ`).
   - Nút **"🛒 Thêm vào giỏ"** (Viền xanh, chữ xanh, có bo góc).
4. *(Nâng cao - Không bắt buộc)*: danh sách hiển thị 9 sản phẩm, có thêm 1 nút loadmore để hiển thị thêm 9 sản phẩm nữa.

### Nhiệm vụ 3: Chức năng Giỏ hàng (Cart)
1. Xây dựng khu vực **"Giỏ hàng của bạn"** cố định ở bên phải màn hình (hoặc nằm dưới cùng nếu xem trên Mobile).
2. Khai báo state `cartItems` (dạng mảng) ở Component cha để lưu các sản phẩm người dùng đã chọn.
3. Khi click nút "Thêm vào giỏ" ở ProductCard, truyền function qua `Props` để đẩy sản phẩm đó vào `cartItems`. *(Gợi ý: Cần kiểm tra xem sản phẩm đã có trong giỏ chưa, nếu có rồi thì tăng số lượng `quantity`, nếu chưa thì thêm mới vào mảng).*
4. Khu vực Giỏ hàng cần hiển thị:
   - **Trạng thái rỗng:** Nếu `cartItems` trống, hiển thị Icon giỏ hàng và chữ "Chưa có sản phẩm nào".
   - Danh sách các món hàng đã thêm (Hiển thị Tên rút gọn, Số lượng và Tổng tiền của món đó).
   - Nút **"THANH TOÁN NGAY"** nổi bật.

---

## 🚀 Yêu cầu nộp bài
- **Deadline:** 18h00, Ngày 01/04/2026.
- **Hình thức nộp:** - 1 Link Github chứa Source Code
                     - 1 Link Deploy sản phẩm thực tế chạy được (Vercel, Netlify).
- **Lưu ý Styling:** Nếu dùng Tailwind CSS, hãy tận dụng prefix `dark:` (ví dụ: `bg-white dark:bg-gray-800 text-black dark:text-white`) để xử lý giao diện cực kỳ mượt mà nhé!