# Kế Hoạch — hướng dẫn triển khai

## 1. Đưa lên Vercel
Upload **toàn bộ thư mục này** (index.html, app.js, styles.css, sw.js, manifest.json, vercel.json, các icon) lên Vercel như cũ.
Không cần build, không cần npm. Sau khi deploy, mở link bằng trình duyệt **một lần khi có mạng** để app tự lưu bản offline.

## 2. Kiểm tra Firebase Rules (rất quan trọng)
Firebase Console → Firestore Database → Rules. Nếu bạn tạo ở "test mode", rules tự **hết hạn sau 30 ngày** và app sẽ báo "Lỗi quyền Firebase".
Dán rules sau rồi Publish:

    rules_version = '2';
    service cloud.firestore {
      match /databases/{database}/documents {
        match /todo_app/my_shared_tasks {
          allow read, write: if true;
        }
      }
    }

(Chỉ mở đúng 1 tài liệu này. Ai biết đường dẫn vẫn đọc/ghi được — nếu cần riêng tư hơn hãy thêm đăng nhập.)

## 3. Cài về máy
- **Android (Chrome):** mở link → nút "📲 Cài App" hoặc menu ⋮ → *Cài đặt ứng dụng*.
- **iPhone/iPad (Safari):** Chia sẻ → *Thêm vào MH chính*.
- **Máy tính (Chrome/Edge):** biểu tượng cài đặt ở thanh địa chỉ, hoặc nút "📲 Cài App".
Không cần APK hay file setup riêng.

## 4. Khi cập nhật code
Sửa file → deploy lại → **tăng số `VERSION` trong sw.js** (ví dụ v5 → v6) để các máy đã cài nhận bản mới.

## 5. Cách đồng bộ hoạt động
- Mọi thay đổi lưu ngay trên máy → dùng offline bình thường.
- Có mạng: app kéo dữ liệu từ Firestore, **gộp** với dữ liệu trên máy (không bên nào ghi đè bên nào), rồi đẩy bản gộp lên. Tự lặp mỗi 20 giây khi đang mở.
- Huy hiệu cạnh tên app: Đã đồng bộ / Đang đồng bộ / Ngoại tuyến · chờ đẩy lên / Lỗi quyền Firebase. Bấm vào để đồng bộ ngay.
