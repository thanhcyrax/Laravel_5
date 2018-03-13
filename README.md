# Create Shopify App
Trang chủ [Shopify](https://developers.shopify.com/) dành cho developers.

## Create Account And Login
**Tạo tài khoản dành cho developer ,không phải tài khoản customer**

Tạo tài khoản developers tại [Shopify](https://developers.shopify.com/)

Sau khi **Login** ta được chuyển qua trang *https://partners.shopify.com/******/development_stores* và chú ý hai phần là **Development stores** và **Apps** ở phần menu trái.

### Development stores

Dùng để tạo các shop mẫu để demo là chính ,nhấn **Create store** để tạo shop.

**Ví dụ:** khi bạn vừa tạo một shop tên "shopify_test" thì nó sẽ có 1 số thuộc tính cơ bản sau :

* shopify_test.myshopify.com : là tên link của shop.

* shopify_test.myshopify.com/admin : để vào trang quản trị của shop ,nơi **tạo product** ,**cài app** ,**xóa app**...

#### Lưu ý :
* Chú ý **link của shop** để có thể demo trên ứng dụng.

### Apps

Dùng để tạo một ứng dụng và kết nối ứng dụng của ta với Shopify ,nhấn **Create app** để tạo ứng dụng.

Khi tạo ứng dụng thành công ta vào ứng dụng đó và vào phần **App info** nơi điền thông tin của ứng dụng.

**Chú ý các phần sau:**

* **App URL:** link lấy thông tin của shop về cho ứng dụng của ta ,thông thường chỉ cần tên link của shop để lấy thông tin.

* **Whitelisted redirection URL(s):** link xử lý thông tin khi người dùng đã cài app thành công.

* **API key:** key dùng để xác nhận ứng dụng để cấu hình trong file '.env'.

* **API secret key:** key ẩn dùng để xác nhận ứng dụng để cấu hình trong file '.env'.

#### Lưu ý :
* Tất cả các link ứng dụng mà ta khai báo cho Shopify đều là link dạng **https** ,có thể sử dụng [ngrok](https://dashboard.ngrok.com/user/login) để setup 1 link chạy **https** hoặc dùng cách khác tùy ý.

## API :

Vào trang *https://help.shopify.com/api/reference* để xem các Document của Shopify.

Chú ý phần **Admin API** ,vì đây là phần chính hướng dẫn sử dụng các API của Shopify.

### Authenticating with Shopify:

**Xác thực với Shopify** ,ta cần có các thông tin sau để xác nhận: 

* **{API key}** ,**{Whitelisted redirection URL(s)}**.
* **{Shop Domain}** link shop của khách hàng.
* **{scopes}** nhu cầu sử dụng API của App.

Ta sẽ thiết lập cơ bản như sau.
```
'https://' . {Shop Domain} . '/admin/oauth/authorize?client_id=' . {API key} . '&scope=' . {scopes} . '&redirect_uri=' . {Whitelisted redirection URL(s)};
```
