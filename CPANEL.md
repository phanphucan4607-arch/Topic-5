# **Lý thuyết**
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


# 🟢 GIAI ĐOẠN 1: CHUẨN BỊ VÀ ĐÓNG GÓI (TẠI VPS)


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

**Khởi tạo Database cho Laravel**
Ở bước trước bạn đã tạo Database cho WordPress rồi, bây giờ ta làm nốt cho Laravel và gom về một User quản lý cho tiện.

Vào MySQL® Databases: Quay lại trang chủ cPanel -> chọn MySQL® Databases.

Tạo Database cho Laravel:

   + Tại ô New Database, gõ: ladb

   + Nhấn Create Database -> Kết quả sẽ là: wpphucan_ladb.

   + Nhấn Go Back.

Tạo User quản trị chung 

    Kéo xuống phần Add New User:

    Username: admin_db

    Password: Nhập mật khẩu: [BEYC@xi@m2u.i,Y
   
  
    Nhấn Create User -> Go Back.

<img width="994" height="529" alt="image" src="https://github.com/user-attachments/assets/e8a44206-6289-488b-96b9-bc6c0a9e3f33" />


**Cấp quyền cho Laravel:**

  quay lại ở phần Add User To Database
  user: wpphucan_admin_db
  data: wpphucan_ladb
Nhấn Add -> Tích vào ALL PRIVILEGES -> Nhấn Make Changes.

**Bây giờ  hãy vào phpMyAdmin**
Chọn Database wpphucan_ladb bên cột trái.

Chọn tab Import.

Chọn file laravel_db_vps.sql (file 55KB) từ máy Ubuntu lên.

Nhấn Import

<img width="994" height="529" alt="image" src="https://github.com/user-attachments/assets/4e0691d3-7da4-4197-a050-623a36009882" />


**Triển khai mã nguồn (File Manager)**

Upload Mã nguồn WordPress (Century Auto)

1. Cho WordPress:

    Vào thư mục public_html.

    Upload file wp_source_vps.zip.

    Extract (Giải nén) ngay tại đó.

    🚨 Lưu ý quan trọng (Xử lý thư mục con): Sau khi giải nén, nếu các file nằm thụt vào trong một thư mục (ví dụ public_html/wp.phucan.../), bạn phải:

        Vào trong thư mục đó, nhấn Select All.

        Nhấn Move và xóa phần đuôi thư mục đi, chỉ để lại /public_html.

        Đảm bảo file index.php của WordPress phải nằm ngay trong public_html.


Cho Laravel

    Vào thư mục laravel.phucan.vietnix.tech.

    Upload file laravel_source_vps.zip.

    Extract tại đây. Đảm bảo sau khi giải nén, bạn thấy thư mục public, app, vendor... nằm ngay trong này.
   
   <img width="1147" height="528" alt="image" src="https://github.com/user-attachments/assets/11eee7f3-aba2-4637-ac24-d735f0b35b36" />


Cấu hình Laravel

   tại mục laravel.phucan.vietnix.tech tìm file .env
   
   tại mục Nếu không thấy, bấm Settings ở góc phải trên -> chọn Show Hidden Filesaravel.phucan.vietnix.tech tìm file .env
   lamf teen biến sao cho phù hợp 
   
<img width="523" height="322" alt="image" src="https://github.com/user-attachments/assets/0ced3139-d592-463c-a0c2-18c88b2bddc2" />

**Lý do lỗi: Sau khi giải nén, code WordPress bị chui sâu vào thư mục con (ví dụ: public_html/var/www/...) nên máy chủ không tìm thấy file index.php ở cửa chính.**

   + Vào File Manager -> thư mục public_html.

   + Đi sâu vào các thư mục con cho đến khi thấy các thư mục: wp-admin, wp-content, wp-includes.

   + Nhấn Select All -> Nhấn Move.

   + Tại ô đường dẫn, xóa hết các thư mục con, chỉ để lại: /public_html. Nhấn Move Files.

   + Kết quả: File index.php phải nằm ngay dưới /public_html.

**Chuyển giao chứng chỉ (di cu SSL thủ công)**
Đây là phần quan trọng để web hiện "ổ khóa xanh" mà không cần chờ Let's Encrypt cấp mới.

    Bước 1 (Tại VPS): Dùng lệnh cat để đọc file chứng chỉ cũ:

        cat /etc/letsencrypt/live/wp.phucan.vietnix.tech/fullchain.pem (Đây là CRT)

        cat /etc/letsencrypt/live/wp.phucan.vietnix.tech/privkey.pem (Đây là KEY) 
    Bước 2 (Tại cPanel): Tìm mục SSL/TLS -> Manage SSL Sites.

   + Chọn Domain tương ứng.

   + Copy nội dung file fullchain dán vào ô Certificate (CRT).

   + Copy nội dung file privkey dán vào ô Private Key (KEY).

   + Nhấn Install Certificate.
    
## **Tái thiết lập "Hồn" (Fix lỗi Database)**

Đây là chỗ bạn dễ nhầm nhất. Phải làm đủ 3 bước nhỏ này:
**Bước 1: Tạo "Cái hộp" chứa dữ liệu (Create Database)**

  +  Vào MySQL® Databases.

  +  Tại Create New Database, gõ: wpdb -> Nhấn Create.

  +  Bây giờ bạn có database tên là: wpphucan_wpdb

**Bước 2: Cấp "Chìa khóa" (Add User to Database)**

    Kéo xuống mục Add User To Database.

    User: Chọn wpphucan_admin_db.

    Database: Phải chọn đúng wpphucan_wpdb.

    Nhấn Add -> Tích vào ALL PRIVILEGES -> Nhấn Make Changes.

**Bước 3: Đổ dữ liệu vào (Import SQL)**

    Vào phpMyAdmin -> Chọn database wpphucan_wpdb bên cột trái.

    Chọn tab Import -> Chọn file wp_db.sql từ máy tính.

    Kéo xuống nhấn Import (hoặc Go). Khi hiện bảng màu xanh là thành công.


## **Kiểm tra**
https://laravel.phucan.vietnix.tech   https://wp.phucan.vietnix.tech

<img width="1811" height="726" alt="image" src="https://github.com/user-attachments/assets/760887f9-b244-4f5b-af88-42d2a6c6c1e8" />


<img width="1811" height="726" alt="image" src="https://github.com/user-attachments/assets/b87fb9f1-b522-4d8c-900a-e041541e1c65" />


<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a7de590a-d364-4112-806a-ac64554705dd" />

_Việc thực hiện lệnh Ping và nhận về IP 103.200.23.68 cho cả hai sub-domain chứng minh rằng cơ chế Local DNS Resolution (Phân giải tên miền cục bộ) qua file /etc/hosts đã hoạt động chính xác.

Máy tính đã ưu tiên đọc cấu hình trong file hosts trước khi truy vấn DNS công cộng. Điều này cho phép quản trị viên kiểm thử website trên môi trường mới (Hosting) mà không làm gián đoạn truy cập của người dùng thực tế đang sử dụng hệ thống cũ (VPS)_



### 🛡️ So sánh Backup Wizard vs JetBackup (Hệ thống Sao lưu & Khôi phục)

| Đặc điểm | 🛠️ Backup Wizard (Mặc định) | 🚀 JetBackup (Nâng cao) |
| :--- | :--- | :--- |
| **Bản chất** | Công cụ chính chủ của cPanel | Plugin chuyên dụng (Vietnix cài thêm) |
| **Cách vận hành** | 🖱️ **Thủ công:** Phải tự tay bấm chạy | 🤖 **Tự động:** Chạy theo lịch trình (Daily/Weekly) |
| **Phương thức** | **Full Backup:** Nén toàn bộ (Nặng) | **Incremental:** Chỉ nén phần thay đổi (Nhẹ/Nhanh) |
| **Lưu trữ** | 📁 Lưu tại chỗ (Ngay trên Hosting) | ☁️ Lưu tách biệt (Remote Storage - An toàn hơn) |
| **Khôi phục** | 🔄 Phải upload file backup từ máy lên | ⚡ **Self-Service:** Chọn ngày và bấm Restore |
| **Độ ưu tiên** | Dùng khi muốn dọn nhà (Migration) | Dùng để "cứu bồ" khi lỡ tay xóa nhầm code |

---

### 💡 Bài học rút ra (Lessons Learned)

Sau khi thực hiện bài Lab di cư (Migration) từ VPS sang cPanel Hosting, em rút ra các điểm mấu chốt sau:

1.  **Về cấu trúc (Structure):** Laravel và WordPress có cấu trúc thư mục khác nhau. Việc xác định đúng **Document Root** (`/public` cho Laravel) là yếu tố sống còn để vận hành và bảo mật Framework. 
2.  **Về bảo mật (Security):** Không bao giờ được để lộ file `.env`. Sử dụng `.htaccess` kết hợp với cấu hình Server (LiteSpeed) tạo ra lớp bảo vệ 403 Forbidden giúp ngăn chặn rò rỉ thông tin Database. 
3.  **Về mạng (Networking):** File **hosts** là công cụ tuyệt vời để kiểm thử website trên môi trường mới trước khi chính thức trỏ DNS toàn cầu, giúp tránh gián đoạn dịch vụ của khách hàng. 
4.  **Về dữ liệu (Data):** Quy trình "Xác (Source code) - Hồn (Database) - Mạch máu (Config)" phải được thực hiện tuần tự. Việc Import Database thành công và sửa đúng thông số kết nối là bước quan trọng nhất để website "sống" lại.




<img width="1530" height="821" alt="image" src="https://github.com/user-attachments/assets/a5cdb6e0-58f5-43b8-a472-42ad3b62fdb1" />


