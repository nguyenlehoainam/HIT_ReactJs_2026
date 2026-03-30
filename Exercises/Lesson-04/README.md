# 💻 Bài tập thực hành trên lớp - Buổi 4 
**Chủ đề: Nút bấm truyền cảm hứng (Random Quote) & Tự động Focus**

## 🎯 Mục tiêu
- Hiểu và dùng được `useRef` để tương tác trực tiếp với DOM (tự động focus).
- Cài đặt thư viện `axios` và biết cách viết tách biệt logic gọi API sang một file Service.

---

## 📝 Yêu cầu chi tiết

**Bước 1: Khởi động với `useRef`**
1. Mở Terminal và cài đặt axios: `npm install axios`
2. Trong Component `App`, tạo một ô `<input type="text" placeholder="Gõ gì đó..." />` và một nút `<button>Lấy trích dẫn mới</button>`.
3. Khai báo `useRef` và gắn vào ô input.
4. Dùng `useEffect` (mảng rỗng) để ô input tự động nhấp nháy con trỏ chuột (focus) ngay khi vừa F5 tải trang.

**Bước 2: Tạo Service Layer siêu tốc**
1. Tạo thư mục `src/services/` và file `quoteService.js`.
2. Import `axios` trực tiếp vào file này (chưa cần cấu hình Instance phức tạp).
3. Viết hàm `getRandomQuote` gọi API tới URL: `https://dummyjson.com/quotes/random` và trả về kết quả.

**Bước 3: Hiển thị lên màn hình**
1. Trong `App.jsx`, khai báo state `quote` (khởi tạo là `null`).
2. Viết hàm `handleFetchQuote`: Khi bấm nút, gọi `quoteService.getRandomQuote()` và lưu kết quả vào state.
3. In câu trích dẫn và tên tác giả ra màn hình.

---

## 💡 Gợi ý Code

**1. File `src/services/quoteService.js`**
```javascript
import axios from 'axios';

// Tách riêng logic gọi API ra một nơi
export const quoteService = {
  getRandomQuote: async () => {
    // Gọi thẳng URL, Axios sẽ tự parse JSON
    const response = await axios.get('[https://dummyjson.com/quotes/random](https://dummyjson.com/quotes/random)');
    return response.data; 
  }
};
```

**2. File `src/App.jsx`**
```jsx
import { useState, useEffect, useRef } from 'react';
import { quoteService } from './services/quoteService';

export default function App() {
  const [quote, setQuote] = useState(null);
  const inputRef = useRef(null); // 1. Khai báo ref

  // 2. Tự động focus lúc mới vào trang
  useEffect(() => {
    inputRef.current.focus(); 
  }, []);

  // 3. Hàm xử lý khi bấm nút
  const handleFetchQuote = async () => {
    try {
      const data = await quoteService.getRandomQuote();
      setQuote(data); // Lưu object chứa câu quote vào state
      
      // Bonus: Bấm nút xong lấy lại focus cho ô input luôn
      inputRef.current.focus(); 
    } catch (error) {
      console.error("Lỗi:", error);
    }
  };

  return (
    <div style={{ padding: '30px', fontFamily: 'sans-serif' }}>
      <h2>Lấy Trích Dẫn Ngẫu Nhiên 🎲</h2>
      
      <div>
        <input 
          ref={inputRef} // Gắn ref vào thẻ
          type="text" 
          placeholder="Trỏ chuột tự động vào đây..." 
          style={{ padding: '8px', width: '250px', marginRight: '10px' }}
        />
        <button onClick={handleFetchQuote} style={{ padding: '8px 16px' }}>
          Lấy câu mới
        </button>
      </div>

      {/* Ràng buộc điều kiện: Có data mới hiển thị */}
      {quote && (
        <div style={{ marginTop: '20px', padding: '15px', background: '#f4f4f4', borderRadius: '8px' }}>
          <p style={{ fontStyle: 'italic', fontSize: '18px' }}>"{quote.quote}"</p>
          <p style={{ fontWeight: 'bold', textAlign: 'right' }}>- {quote.author}</p>
        </div>
      )}
    </div>
  );
}
```