# Buổi 3: Template engine và mô hình MVC

## 1. [Template engine](https://expressjs.com/en/guide/using-template-engines.html)

### 1.1. Template engine là gì?

- Template engine là một công cụ cho phép nhúng dữ liệu động vào HTML để tạo ra giao diện web có thể thay đổi nội dung mà không cần chỉnh sửa nhiều dữ liệu HTML tĩnh.
- Trước khi xuất hiện template engine, thường sử dụng các chuỗi kết nối với nhau để chèn dữ liệu vào HTML, nhưng cách này làm code trở nên dài, khó bảo trì. Template engine giúp xử lý vấn đề này bằng cách cung cấp các cú pháp riêng biệt để kết hợp HTML tĩnh và dữ liệu động thông qua các biến.

### 1.2. Mục đích và lợi ích của Template engine

- Tách biệt code login và giao diện.
- Dễ dàng tái sử dụng: có thể sử dụng lại các mẫu giao diện mà không phải chỉnh sửa nhiều.
- Hiệu suất cao: Giảm tải xử lý bằng cách cho server biên dịch template trước khi gửi đến client.

## 2. [EJS](https://ejs.co/)

### 2.1. EJS là gì?

EJS (Embedded Javascript) là một template engine đơn giản nhưng mạnh, cho phép nhúng trực tiếp Javascript vào HTML thông qua các cú pháp như <%= %>, <% %>,...

### 2.2. Cài đặt EJS

```bash
npm install ejs
```

Thiết lập EJS làm template engine mặc định trong Express.js

```js
import path from "path";
import { fileURLToPath } from "url";

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

app.set("view engine", "ejs");
app.set("views", path.join(__dirname, "views"));
```

### 2.3. Sử dụng EJS để render trang web

index.js

```js
app.get("/", (req, res) => {
  res.render("index", {
    title: "Homepage",
    message: "Server Express.js đang chạy",
  });
});
```

views/index.ejs

```html
<!DOCTYPE html>
<html lang="vi">
  <head>
    <meta charset="UTF-8" />
    <title><%= title %></title>
  </head>
  <body>
    <h1><%= message %></h1>
  </body>
</html>
```

### 2.4. Các cú pháp cơ bản trong EJS

- `<%= variable %>`: Hiển thị giá trị của biến ra HTML
- `<%- variable %>`:
- `<% logic %>`: Viết Javascript (if, for, ...)
- `<% include('file') %>`: Import một template con

## 3. Static folder

### 3.1. Static folder là gì?

Static folder là thư mục chứa các file tĩnh như CSS, Javascript, hình ảnh, font chữ, video, audio, file tài nguyên khác để phục vụ trực tiếp cho client mà không cần qua xử lý từ server.

### 3.2. Cấu hình static folder trong Express.js

```js
app.use(express.static("public"));
```

Khi thiết lập như trên, Express.js sẽ tìm kiếm các file tĩnh trong thư mục `public` và trả về cho client mà không cần qua xử lý từ server.

Ví dụ: file `public/style.css` sẽ được truy cập qua đường dẫn `http://localhost:3000/style.css`.

## 4. Mô hình MVC

### 4.1. Mô hình MVC là gì?

- MVC (Model-View-Controller) là một kiến trúc phần mềm giúp tách biệt logic ứng dụng, giao diện và xử lý dữ liệu. Điều này giúp dễ dàng bảo trì, mở rộng và tái sử dụng code.

### 4.2. Các thành phần trong MVC

- Model: Quản lý dữ liệu, thường kết nối với database.
- View: Hiển thị giao diện người dùng.
- Controller: Xử lý logic, nhận request từ client, gọi model để lấy dữ liệu và trả về view.

### 4.3. Mô hình MVC trong Express.js

Cách tổ chức thư mục theo mô hình MVC trong Express.js:

```
project
├── controllers
│   └── homeController.js
├── models
│   └── userModel.js
├── public
│   ├── css
│   ├── img
│   └── js
├── routes
│   └── homeRoute.js
├── views
│   ├── home
│   │   └── index.ejs
│   └── layout.ejs
└── index.js
```

### 4.4. Cấu hình server (index.js)

```js
const express = require("express");
const app = express();
const homeRoutes = require("./routes/homeRoute");

app.set("view engine", "ejs");
app.set("views", "./views");
app.use(express.static("public"));

app.use("/", homeRoutes);

app.listen(3000, () => {
  console.log("Server is running on port 3000");
});
```

### 4.5. Xây dựng controller (controllers/homeController.js)

```js
const homePage = (req, res) => {
  res.render("home/index", {
    title: "Homepage",
    message: "Server Express.js đang chạy",
  });
};

module.exports = {
  homePage,
};
```

### 4.6. Xây dựng route (routes/homeRoute.js)

```js
const express = require("express");
const router = express.Router();
const homeController = require("../controllers/homeController");

router.get("/", homeController.homePage);

module.exports = router;
```
