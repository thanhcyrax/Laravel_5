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

Chú ý phần **Admin API** ở menu ,vì đây là phần chính hướng dẫn sử dụng các API của Shopify.

### Authenticating with Shopify:

**Xác thực với Shopify** ,ta cần có các thông tin sau để xác nhận: 

* **{API key}** ,**{Whitelisted redirection URL(s)}**.
* **{Shop Domain}** link shop của khách hàng.
* **{scopes}** nhu cầu sử dụng API của App(Shopify sẽ yêu cầu ta khai báo các API cần sử dụng) xem thêm tại *https://help.shopify.com/api/getting-started/authentication/oauth/scopes*

Ta sẽ thiết lập cơ bản như sau.
```
'https://' . {Shop Domain} . '/admin/oauth/authorize?client_id=' . {API key} . '&scope=' . {scopes} . '&redirect_uri=' . {Whitelisted redirection URL(s)}
```

#### Ví dụ :

Ta có một shop tên **shop_test.com** có API key là **dswer435ewrf34tret4** ,Whitelisted redirection là https://shop_auth.com/auth ,scopes là **read_products,write_products** bao gồm lấy thông tin sản phẩm ,chỉnh sửa sản phẩm ...

```
https://shop_test.com/admin/oauth/authorize?client_id=dswer435ewrf34tret4&scope=read_products,write_products&redirect_uri=https://shop_auth.com/auth
```

### Get Access Token:

Sau khi xác thực thành công ta sẽ nhận được các biến giá trị theo kiểu "GET" và ta tiếp tục lấy **Access Token** có thể hiểu là một đoạn mã xác thực để sử dụng các API .Gồm các bước sau đây:

* **Xác minh yêu cầu :** ta cần phải xác nhận xem app của ta đã được Shopify xác nhận chưa ,ta cần có **API secret key** ,các biến trả về như **code** ,**shop**(thường sẽ là **Shop Domain**) ,**timestamp** ,**hmac**(nếu có) ,**signature**(nếu có) rồi xem ví dụ sau

```

//Nếu biến $hmac tồn tại
if (isset($hmac)) {
	//Truyền các biến trả về
    $queryString = http_build_query(array(
        'code' => $code,
        'shop' => $shop,
        'timestamp' => $timestamp
    ));
    $match = $hmac;
    //Mã hóa chuỗi có API_secret_key
    $calculated = hash_hmac('sha256', $queryString, $API_secret_key);
} 
//Ngược lại
else {
	//Truyền các biến trả về
    $queryString = http_build_query(array(
        'code' => $code,
        'shop' => $shop,
        'timestamp' => $timestamp
    ), null, '');
    $match = $signature;
    //Mã hóa chuỗi có API_secret_key
    $calculated = md5($API_secret_key . $queryString);
}
//So biến trả về ,"true" thì tiếp tục nếu là "false" thì xem lại 
return $calculated === $match;

```

* **Lấy Access Token :** Sau khi thành công ở bước trên thì ta qua bước này ,ta sẽ lấy **AccessToken** dưới dạng 1 chuỗi (String) ,các tài nguyên cần có **ShopDomain** ,**API key** ,**API secret key** ... đặc biệt là phải hiểu **RESTful API** để sử dụng **RESTful API** một cách đơn giản nhất ta xài thư viện [Guzzle](http://docs.guzzlephp.org/en/stable/) (gõ lệnh "composer require guzzlehttp/guzzle" để cài)

```

    //Gọi thư viện
	$client = new Client();
	//Tạo mảng để truyền dữ liệu lên Shopify
	$data = array(
	    'client_id' => env('SHOPIFY_API_KEY'),
	    'client_secret' => env('SHOPIFY_SECRET_KEY'),
	    'code' => $code
	);
    //Truyền theo kiếu "POST"
    $response = $client->post("https://" . $this->_shopDomain . "/admin/oauth/access_token", ['form_params' => $data]);
    //Nếu thành công thì đọc dữ liệu trả về và mã hóa chuỗi theo khóa "getContents" (đây là khóa mặc định)
    $response = json_decode($response->getBody()->getContents());
    //Lấy biến access_token
    return $response->access_token;

```

#### Lưu ý :
* Các bước trên phải thực hiện tuần tự ,nếu sai thì nên dừng lại ngay không nên tiếp tục.
* Hai giá trị **Shop Domain** và **AccessToken** là hai giá trị quan trọng cần được lưu trữ và sử dụng xuyên suốt toàn bộ dự án.
* 1 **Shop Domain** chỉ có duy nhất 1 **AccessToken** và ngược lại.

### Use API:
Vào trang https://help.shopify.com/api/reference ,phần **Admin API** để xem tất cả các API do shopify cung cấp.
API của Shopify được viết theo **RESTful API** nên sẽ chia thành 4 kiểu chính ,**GET** ,**POST** ,**PUT** ,**DELETE** xem các mẫu sau. 

* **GET:** Các tài nguyên cần là **ShopDomain** ,**AccessToken** ,**url**(Tên link tương ứng với API mình sẽ gọi) ,**data**(Gía trị truyền vào)

```
    
    //Gọi thư viện
    $client = new Client();
    //Truyền theo kiểu GET
    $response = $client->request('GET', "https://$shopDomain/admin/$url",
        [
            'headers' => [
                'Content-Type' => 'application/json',
                'X-Shopify-Access-Token' => $accessToken
            ],
            'query' => $data
        ]);
    //Gía trị truyền về nếu thành công và ta sẽ mặc định là như vậy
    return json_decode($response->getBody()->getContents());

```

* **POST:** Các tài nguyên cần là **ShopDomain** ,**AccessToken** ,**url**(Tên link tương ứng với API mình sẽ gọi) ,**data**(Gía trị truyền vào)

```

    //Gọi thư viện
    $client = new Client();
    //Truyền theo kiểu POST
    $response = $client->request(
        'POST', "https://$shopDomain/admin/$url",
        [
            'headers' => [
                'Content-Type' => 'application/json',
                'X-Shopify-Access-Token' => $accessToken
            ],
            'body' => json_encode($data)
        ]);
    //Gía trị truyền về nếu thành công và ta sẽ mặc định là như vậy
    return json_decode($response->getBody()->getContents());

```

* **PUT:** Các tài nguyên cần là **ShopDomain** ,**AccessToken** ,**url**(Tên link tương ứng với API mình sẽ gọi) ,**data**(Gía trị truyền vào)

```

    //Gọi thư viện
    $client = new Client();
    //Truyền theo kiểu PUT
	$response = $client->request(
	    'PUT',
	    "https://$shopDomain/admin/$url",
	    [
	        'headers' => [
	            'Content-Type' => 'application/json',
	            'X-Shopify-Access-Token' => $accessToken
	        ],
	        'body' => json_encode($data)
	    ]);
    //Gía trị truyền về nếu thành công và ta sẽ mặc định là như vậy
	return json_decode($response->getBody()->getContents());

```

* **DELETE:** Các tài nguyên cần là **ShopDomain** ,**AccessToken** ,**url**(Tên link tương ứng với API mình sẽ gọi) 

```

	//Gọi thư viện
	$client = new Client();
    //Truyền theo kiểu DELETE
    $response = $client->request('DELETE', "https://$shopDomain/admin/$url",
        [
            'headers' => [
                'Content-Type' => 'application/json',
                'X-Shopify-Access-Token' => $accessToken
            ]
        ]);
    //Gía trị truyền về nếu thành công và ta sẽ mặc định là như vậy
    return json_decode($response->getBody()->getContents());

```

#### Lưu ý :
* Chỉ gọi API khi có **AccessToken** 
* Thường xuyên sử dụng "try {} catch () {}" trong các hàm gọi API để đảm bảo tính chính xác.
* Khi gọi dữ liệu từ API về thì những dữ liệu quan trọng sẽ lưu trực tiếp vào cơ sở dữ liệu của ta ,tránh việc gọi API liên tục và thường xuyên.  

### Attention API and Example:

Các API cần chú ý vì chúng thường xuất hiện trong các dự án của ta.

* **[Product](https://help.shopify.com/api/reference/product):** Xử lý các Product của khách hàng như thêm ,xóa ,sửa ... để đồng bộ dữ liệu với app của ta. Ví dụ sau để lấy tất cả thông tin sản phẩm của một Shop ,tài nguyên cần có **ShopDomain** ,**AccessToken** ,**url** ,**data** và kiểu lấy API là **GET**

```
    
    //Tên API
    $url = 'products.json';
    //Dữ liệu truyền vào
    $data = [
		'limit'            => 10,                                               // Số lượng sản phẩm trả về
		'page'             => 1,                                                // Số trang(định dạng phận trang) mặc định là 1.
		'fields'           => 'id, title, handle, image, shop_id, setting_id',  // Chỉ định các trường muốn lấy của 1 Product
		'published_status' => 'any'                                             // Hiển thị tất cả các sản phẩm
    ];
    //Gọi thư viện
    $client = new Client();
    //Truyền theo kiểu GET
    $response = $client->request('GET', "https://$shopDomain/admin/$url",
        [
            'headers' => [
                'Content-Type' => 'application/json',
                'X-Shopify-Access-Token' => $accessToken
            ],
            'query' => $data
        ]);
    //Gía trị truyền về nếu thành công và ta sẽ mặc định là như vậy
    return json_decode($response->getBody()->getContents());

```

* **[Web Hook](https://help.shopify.com/api/reference/webhook):** Xử lý đổng bộ ,ví dụ như khách hàng xóa sản phẩm bên họ thì mình sẽ bắt sự kiện xóa luôn dữ liệu bên mình. Xem ví dụ sau về việc thêm 1 **Web Hook** để bắt sự kiện ,tài nguyên cần có **ShopDomain** ,**AccessToken** ,**url** ,**data** và kiểu lấy API là **POST**

```
    //Tên API
    $url = 'webhooks.json';
    //Dữ liệu truyền vào
    $data = [
		'topic'            => 'products/delete',                // Sự kiện này xảy ra khi khách hàng xóa Product bên họ.
		'address'          => 'http://delete.product.com/',     // Link bắt sự kiện của App
		'format'           => 'json',                           // Kiểu dữ liệu json
    ];
    //Gọi thư viện
    $client = new Client();
    //Truyền theo kiểu POST
    $response = $client->request(
        'POST', "https://$shopDomain/admin/$url",
        [
            'headers' => [
                'Content-Type' => 'application/json',
                'X-Shopify-Access-Token' => $accessToken
            ],
            'body' => json_encode($data)
        ]);
    //Gía trị truyền về nếu thành công và ta sẽ mặc định là như vậy
    return json_decode($response->getBody()->getContents());

```

* **[Recurring Application Charge](https://help.shopify.com/api/reference/recurringapplicationcharge):** Yêu cầu thanh toán của ta tới khách hàng và đây sẽ là phí thường niên nên nó sẽ tự động thu khi hết hạn. Xem ví dụ sau về việc thêm 1 **Recurring Application Charge** để bắt sự kiện ,tài nguyên cần có **ShopDomain** ,**AccessToken** ,**url** ,**data** và kiểu lấy API là **POST**

```
    //Tên API
    $url = 'recurring_application_charges.json';
    //Dữ liệu truyền vào
    $data = [
		"name": "Super Duper Plan",                          // Tên khoản phí
	    "price": 10.0,                                       // Đơn giá và nó sẽ tự động thu mỗi tháng
	    "return_url": "http://super-duper.shopifyapps.com",  // Link trả về khi khách hàng đồng ý thanh toán
	    "test": true                                         // Bật chế độ test để test thử dự án
    ];
    //Gọi thư viện
    $client = new Client();
    //Truyền theo kiểu POST
    $response = $client->request(
        'POST', "https://$shopDomain/admin/$url",
        [
            'headers' => [
                'Content-Type' => 'application/json',
                'X-Shopify-Access-Token' => $accessToken
            ],
            'body' => json_encode($data)
        ]);
    //Gía trị truyền về nếu thành công và ta sẽ mặc định là như vậy
    return json_decode($response->getBody()->getContents());

```





