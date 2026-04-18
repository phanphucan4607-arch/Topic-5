<img width="975" height="395" alt="image" src="https://github.com/user-attachments/assets/3c09eb65-6978-452b-be69-7d0d50d26b91" /># **Lý thuyết**
Upload 2 source code Wordpress và Laravel đã cung cấp tại Topic 4 (⁠Topic 4 -  Reverse Proxy⁠) vào CPanel với 2 domain tương ứng đã cấp trước đó (chủ động thêm domain còn lại vào hosting và trỏ domain về IP Hosting với file hosts)

Upload  lại cert SSL từ VPS đã tạo trước đó

Thông tin truy cập Cpanel https://host68.vietnix.vn:2083/
 wpphucan | CRzGkvRF7yMyFmFxHE

cPanel là hệ thống quản trị web hosting trên nền tảng Linux phổ biến nhất hiện nay, cung cấp giao diện đồ họa đơn giản và các công cụ tự động hóa để quản lý hosting. 
Mục đích chính của cPanel

Đơn giản hóa việc quản lý Hosting: Giúp người dùng cuối (ngay cả khi không am hiểu kỹ thuật sâu) có thể dễ dàng quản lý các dịch vụ lưu trữ web, tên miền, email, và cơ sở dữ liệu thông qua giao diện đồ họa, thay vì sử dụng các dòng lệnh phức tạp trên Linux.
Quản lý dữ liệu: Cho phép tải lên, tạo mới, chỉnh sửa, xóa, nén/giải nén tệp tin website dễ dàng thông qua File Manager.
Quản lý Email: Tạo và quản lý tài khoản email theo tên miền riêng.
Quản lý Cơ sở dữ liệu: Quản lý MySQL/MariaDB (thường dùng cho WordPress, website thương mại điện tử).
Bảo mật: Cung cấp công cụ bảo mật như quản lý IP, SSL/TLS, chặn địa chỉ IP, và bảo mật thư mục.
Cài đặt tự động: Hỗ trợ cài đặt nhanh các mã nguồn phổ biến (như WordPress, Joomla, Drupal) thông qua các công cụ như Softaculous
   

Tóm lại, cPanel đóng vai trò là cầu nối giao diện thân thiện giữa người dùng và máy chủ, giúp quản trị website, email và database một cách nhanh chóng và hiệu quả. 

🎯 1. Mục đích của Topic 5

Mục đích chính là thực hiện Di cư hệ thống (Migration). cần chứng minh khả năng di chuyển toàn bộ dữ liệu từ môi trường máy chủ tự quản trị (VPS) sang môi trường lưu trữ dùng chung (cPanel/Shared Hosting). Việc này kiểm tra kỹ năng xử lý sự tương thích giữa hai môi trường khác nhau và khả năng quản lý chứng chỉ bảo mật thủ công.
🔄 2. Luồng xử lý (Workflow)

Luồng công việc được chia làm 3 giai đoạn chính:

Giai đoạn 1: Trích xuất dữ liệu (Tại VPS)

    Dữ liệu: Sử dụng lệnh mysqldump để xuất Database thành file .sql.

    Bảo mật: Sử dụng lệnh cat để lấy nội dung file chứng chỉ (fullchain.pem) và khóa bí mật (privkey.pem).

Giai đoạn 2: Trung chuyển (Tại Máy tính cá nhân)

    Tải về: Sử dụng lệnh scp (hoặc các công cụ hỗ trợ) để đưa file .sql từ VPS về máy tính.

    Chuẩn bị: Tập hợp sẵn bộ mã nguồn (Source code) gốc mà giáo viên đã cung cấp từ đầu bài Lab.

Giai đoạn 3: Tái thiết lập (Tại cPanel)

    Khởi tạo: Tạo Database và User mới thông qua công cụ MySQL Database Wizard.

    Triển khai: * Upload mã nguồn lên File Manager và giải nén.

        Import file .sql vào phpMyAdmin.

    Cấu hình: Chỉnh sửa file wp-config.php hoặc .env để cập nhật thông tin Database mới và thêm đoạn mã xử lý giao thức chạy song song (HTTP/HTTPS).

    Kích hoạt SSL: Dán nội dung Cert/Key vào mục SSL/TLS của cPanel để khôi phục HTTPS.

✅ 3. Kiểm tra kết quả

Sử dụng file hosts trên Windows để trỏ tên miền về địa chỉ IP của Hosting mới. Nếu truy cập website hiển thị đầy đủ nội dung, có ổ khóa xanh (SSL) và trả về mã 200 OK cho cả hai bản HTTP/HTTPS là hoàn tất yêu cầu.


🟢 GIAI ĐOẠN 1: CHUẨN BỊ VÀ ĐÓNG GÓI (TẠI VPS)


Nén Code (Gói đồ đạc vào thùng Zip)

1. Đăng nhập vào VPS:
Mở Terminal trên Ubuntu của bạn và gõ
```
zip -r wp_source_vps.zip /var/www/wp.phucan.vietnix.tech/
zip -r laravel_source_vps.zip /var/www/laravel.phucan.vietnix.tech/
```
**Xuất Database**
đối với data wordperss
```
mysqldump -u root -p phucan_wp_db > /root/wp_db_vps.sql
```
đối với laravel
```
mysqldump -u root -p phucan_laravel_db > /root/laravel_db_vps.sql
```
Kiểm tra trước khi tải
Chạy lệnh này để đảm bảo 4 file đã nằm đúng chỗ và có dung lượng

<img width="883" height="291" alt="image" src="https://github.com/user-attachments/assets/c002dfd1-684c-40ab-9207-2bcde04fb330" />

Hiện tượng: Nếu thấy 4 file này hiện ra với dung lượng vài Megabyte (M) là bạn đã đóng gói thành công.

**Tải các file từ VPS về máy Ubuntu (an@an)**

🚨 CỰC KỲ QUAN TRỌNG: Bạn phải mở một Tab Terminal mới trên máy Ubuntu (dòng lệnh bắt đầu bằng an@an:~$), không được gõ trong tab đang SSH root.

**Kéo 2 file Code (.zip) về:**
```
scp root@221.132.21.141:/var/www/*.zip ~/Downloads/
```
**Kéo 2 file Database (.sql) về:**
```
scp root@221.132.21.141:/root/*.sql ~/Downloads/
```
<img width="798" height="365" alt="image" src="https://github.com/user-attachments/assets/d1b57818-675a-406d-a4b1-36ac582d0538" />

quay về trang chủ Cpanel 
Tạo tên miền mới: * Nhấn nút Create A New Domain.
<img width="975" height="395" alt="image" src="https://github.com/user-attachments/assets/87a436b7-8ea7-4004-8e46-cb57889cf608" />


**Điền thông số (Cực kỳ quan trọng)**
        Domain: Gõ chính xác laravel.phucan.vietnix.tech
        
        Share document root BỎ DẤU TÍCH ở ô này.
        
        Document Root Hệ thống sẽ hiện ra laravel.phucan.vietnix.tech. Bạn hãy sửa/gõ thêm vào cuối để thành:
        👉 laravel.phucan.vietnix.tech/public
        
        Nhấn Submit.
        
<img width="975" height="823" alt="image" src="https://github.com/user-attachments/assets/e3ab0a1b-cf28-42d2-b1b8-4fe3f68bd756" />

Giải thích: Laravel chỉ cho phép truy cập từ thư mục public để bảo vệ các file hệ thống quan trọng bên trong. Nếu thiếu đuôi /public, web của bạn sẽ không chạy được hoặc bị lộ mã nguồn.

