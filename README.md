# Setup Laravel

Tài liệu [Laravel 5](https://laravel.com/docs/5.6).

## Download

Có thể tải Laravel từ trang chủ [Laravel 5](https://laravel.com/docs/5.6) **hoặc** clone về từ Git.
#### Lưu ý :
* **Trước khi down:** Kiểm tra kĩ yêu cầu trước khi down ,đặc biệt là phiên bản **PHP>7.0.0** và phải có [composer](https://getcomposer.org/).
* **Sau khi down:** Khi tải xong kiểm tra thư mục "vendor" xem có hay không. Nếu không mở **command** trỏ về thư mục gốc và chạy lệnh "composer install".

## Setup Run Local
*Các Setup này là để hỗ trợ chạy App Shopify là chính*

Sau khi hoàn tất các bước trên ,vào thư mục gốc tạo file ".env" và copy toàn bộ nội dung của file ".env.example" rồi chép vào file '.env' vừa mới tạo.

### Các phần cần chỉnh sửa và cần lưu ý trong file ".env"

```
/*Cấu hình cơ bản*/
APP_NAME="App Shopify"                                       /*Tên app*/
APP_ENV=local          
APP_KEY=base64:8CBToHlNhX+1pjdDi20AD6DOzHco0k4wNCJ33Vo9ZiM=
APP_DEBUG=true
APP_LOG_LEVEL=debug
APP_URL=https://d3138cfa.ngrok.io                            /*Tên link của app*/

/*Cấu hình database ,ví dụ sau đây là cấu hình theo XAMPP*/
DB_CONNECTION=mysql   /*Loại database*/
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=shopifyDB /*Tên database*/
DB_USERNAME=root      /*Tài khoản*/
DB_PASSWORD=          /*Mật khẩu*/

BROADCAST_DRIVER=log
CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_DRIVER=database

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

/*Cấu hình mail ,ví dụ sau đây là cấu hình theo mail GOOGLE(G-Mail)*/
MAIL_DRIVER=smtp          //Loại mail
MAIL_HOST=smtp.gmail.com  //server gửi mail
MAIL_PORT=465             //port của mail
MAIL_USERNAME=            //Tài khoản mail
MAIL_PASSWORD=            //Mật khẩu mail 
MAIL_ENCRYPTION=ssl       //Định dạng mã hóa

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=

/*Cấu hình kết nối shopify ,lấy 2 key từ shopify*/
SHOPIFY_API_KEY=a522a1cf575540ea6ae1df4c62937f74     /*API key*/
SHOPIFY_SECRET_KEY=00829b3d3039397d7810e5b3b9c98682  /*API secret key*/
```

#### Lưu ý :
* **Sau khi xong các bước trên:** Kiểm tra trước kết nối database bằng cách mở **command** trỏ về thư mục gốc và gõ lệnh "php artisan migrate" .Trường hợp bị lỗi thì thử lệnh "php artisan config:clear" rồi chạy lại lệnh "php artisan migrate" ,nếu không được thì kiểm tra lại hoặc tìm cách khác.

# Create Shopify App
Trang chủ [Shopify](https://developers.shopify.com/) dành cho developers.
##
