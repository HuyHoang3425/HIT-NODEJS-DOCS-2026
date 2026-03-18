# Buổi 2: Giới thiệu Express.js cơ bản

## 1. API và RESTful API

### 1.1. API (Application Programming Interface)

- API là giao diện lập trình ứng dụng, cho phép các ứng dụng tương tác với nhau.
- API giúp các ứng dụng frontend (React, Angular, Vue, ...) tương tác, trao đổi dữ liệu với các ứng dụng backend (Node.js, Java, Python, ...).
- API hoạt động dựa trên mô hình Client-Server, trong đó:
  - Client (trình duyệt, ứng dụng di động, ...) gửi yêu cầu đến Server.
  - Server: xử lý yêu cầu, trả về dữ liệu cho Client. (dữ liệu có thể là JSON, XML, HTML, ...)

  ![Client-Server model](./client-server-model.avif)

### 1.2. RESTful API

- REST (Representational State Transfer) là một kiến trúc thiết kế API giúp ứng dụng giao tiếp với nhau thông qua giao thức HTTP.
- RESTful API là API tuân thủ theo các nguyên tắc của REST.
- Đặc điểm của RESTful API:
  - Sử dụng HTTP Method (GET, POST, PUT, PATCH, DELETE) để thực hiện các thao tác CRUD (Create, Read, Update, Delete) trên dữ liệu.
  - Sử dụng URL để xác định tài nguyên (resource) cần thao tác.
    - `GET /users`: Lấy danh sách người dùng.
    - `POST /users`: Tạo mới người dùng.
    - `GET /users/:id`: Lấy thông tin người dùng theo ID.
    - `PUT /users/:id`: Cập nhật thông tin người dùng theo ID.
    - `DELETE /users/:id`: Xóa người dùng theo ID.
  - Sử dụng JSON làm định dạng dữ liệu chính.
    ```javascript
    {
        "id": 1,
        "name": "Nguyen Van A",
        "email": "nguyenvana@gmail.com",
        "phone": "0123456789",
        "address": "Ha Noi",
    }
    ```
  - [Ngoài ra các bạn có thể tham khảo ở link này](https://viblo.asia/p/tim-hieu-restful-api-va-cac-tieu-chuan-viet-api-hieu-qua-aAY4q7qpLPw)

### 1.3. API Làm việc như thế nào

#### A. HTTP Request – Yêu cầu từ Client gửi lên Server

Một **HTTP Request** thường gồm các thành phần chính sau:

#### 1. URL / Endpoint

#### 2. HTTP Method

#### 3. Headers

- Chứa thông tin bổ sung cho request
- Server dùng để hiểu cách xử lý request

```http
Content-Type: application/json
Authorization: Bearer token123
```

#### 4. Body

Là nơi client gửi dữ liệu lên server

#### 5. Params (URL Params)

- Là biến nằm trong URL
- Dùng để xác định resource cụ thể
  Ví dụ:

```http
GET /users/1
```

=> 1 chính là param

#### 6. Query (Query String)

- Là dữ liệu thêm vào URL sau dấu ?
- Dùng để lọc, tìm kiếm, phân trang
  Ví dụ:

```http
GET /users?name=hoang&page=1
```

Gồm:

```http
name=hoang
page=1
```

## 2. HTTP Method và HTTP Status Code

### 2.1. [HTTP Method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)

- [HTTP Method](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) (phương thức HTTP) là các phương thức được sử dụng để giao tiếp giữa client và server.
- Mỗi phương thức có một chức năng riêng, phù hợp với mục đích sử dụng.
- Các phương thức HTTP phổ biến:
  - GET: Lấy dữ liệu
  - POST: Gửi dữ liệu mới
  - PUT: Cập nhật toàn bộ dữ liệu
  - PATCH: Cập nhật một phần dữ liệu
  - DELETE: Xóa dữ liệu
  - HEAD, OPTIONS, CONNECT, TRACE

### 2.2. [HTTP Status Code](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

- [HTTP Status Code](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) (mã trạng thái HTTP) là mã số trả về từ server để thông báo kết quả của yêu cầu.
- Mỗi mã trạng thái có một ý nghĩa riêng, giúp client hiểu được kết quả của yêu cầu.
- Các mã trạng thái HTTP phổ biến:
  - 1xx (Informational): Thông tin
  - 2xx (Successful): Thành công
    - 200 OK: Yêu cầu thành công
    - 201 Created: Tạo mới thành công
    - 204 No Content: Không có nội dung trả về
  - 3xx (Redirection): Chuyển hướng
    - 301 Moved Permanently: Chuyển hướng vĩnh viễn
    - 302 Found: Chuyển hướng tạm thời
  - 4xx (Client errors): Lỗi từ phía client
    - 400 Bad Request: Yêu cầu không hợp lệ
    - 401 Unauthorized: Không được phép truy cập
    - 403 Forbidden: Bị từ chối truy cập
    - 404 Not Found: Không tìm thấy
  - 5xx (Server errors): Lỗi từ phía server
    - 500 Internal Server Error: Lỗi không xác định từ phía server
    - 502 Bad Gateway: Server trung gian không hợp lệ

## 3. Package Manager của Node.js (npm, yarn, pnpm,...)

### 3.1. Tổng quan về Package Manager

- Package Manager (trình quản lý gói) là công cụ cài đặt, quản lý các package trong ứng dụng Node.js.
- Lợi ích của Package Manager:
  - Cài đặt các package cần thiết cho ứng dụng.
  - Quản lý phiên bản package linh hoạt.
  - Tự động cài đặt các package phụ thuộc (dependencies).
- Các Package Manager phổ biến:
  - npm (Node Package Manager) - trình quản lý gói mặc định của Node.js.
  - yarn
  - pnpm

### 3.2. [npm (Node Package Manager)](https://www.npmjs.com/)

- Kiểm tra phiên bản npm:

  ```bash
  node -v # kiểm tra phiên bản Node.js
  npm -v # kiểm tra phiên bản npm
  ```

- Khởi tạo dự án với npm:

  ```bash
  npm init
  npm init -y # Khởi tạo dự án với các cài đặt mặc định
  ```

- Cài đặt package với npm:

  ```bash
  npm install <package-name>
  npm install <package-name> --save # Cài đặt và lưu vào dependencies
  npm install <package-name> --save-dev # Cài đặt và lưu vào devDependencies
  npm install <package-name> <package-name> # Cài đặt nhiều package cùng lúc
  ```

- Gỡ cài đặt package:

  ```bash
  npm uninstall <package-name>
  npm uninstall <package-name> --save # Gỡ cài đặt và xóa khỏi dependencies
  npm uninstall <package-name> --save-dev # Gỡ cài đặt và xóa khỏi devDependencies
  ```

## 4. Giới thiệu về Express.js

### 4.1. Express.js là gì?

- Express.js là một framework Node.js giúp xây dựng ứng dụng web dễ dàng, nhanh chóng.
- Express.js giúp tạo server backend một cách nhanh chóng mà không cần phải viết nhiều code phức tạp.
- Là nền tảng cho việc xây dựng RESTful API, ứng dụng web, ứng dụng real-time, ...

### 4.2. Cài đặt và thiết lập dự án Express.js

- Khởi tạo dự án Node.js:

  ```bash
  mkdir express-app # Tạo thư mục dự án
  cd express-app # Di chuyển vào thư mục dự án
  npm init # Khởi tạo dự án với npm
  ```

- Cài đặt Express.js:

  ```bash
  npm install express
  ```

- Tạo file `index.js`:

  ```javascript
  const express = require("express");

  const app = express();
  const port = 3000;

  app.get("/", (req, res) => {
    res.send("Hello World!");
  });

  app.listen(port, () => {
    console.log(`Example app listening on port ${port}`);
  });
  ```

- Chạy ứng dụng:

  ```bash
  node index.js
  ```

- Mở trình duyệt và truy cập vào `http://localhost:3000/` để kiểm tra kết quả.

### 4.3. Cài đặt Nodemon

- Nodemon là một công cụ giúp tự động khởi động lại ứng dụng Node.js khi có thay đổi trong mã nguồn.

- Cài đặt Nodemon:

  ```bash
  npm install nodemon --save-dev
  ```

- Sử dụng Nodemon:

  ```bash
  nodemon index.js
  ```

- Mở trình duyệt và truy cập vào `http://localhost:3000/` để kiểm tra kết quả.
