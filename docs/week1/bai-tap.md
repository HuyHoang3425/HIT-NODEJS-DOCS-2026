# Bài tập

## Array

```js
const users = [
  {
    id: 1,
    name: "Nguyen Van A",
    age: 21,
    role: "user",
    email: "a@gmail.com",
    address: {
      city: "Hanoi",
      district: "Cau Giay",
    },
  },
  {
    id: 2,
    name: "Tran Van B",
    age: 30,
    role: "admin",
    email: "b@gmail.com",
    address: {
      city: "HCM",
      district: "District 1",
    },
  },
  {
    id: 3,
    name: "Le Van C",
    age: 25,
    role: "user",
    email: "c@gmail.com",
    address: {
      city: "Hanoi",
      district: "Dong Da",
    },
  },
];
```

1. In ra tất cả tên user
2. Tạo array chứa email của users
3. Lấy danh sách user có tuổi > 24
4. Tìm user có role = admin
5. Tìm index của user id = 3
6. Tính tổng tuổi của tất cả user
7. Kiểm tra có user nào là admin không
8. Kiểm tra tất cả user có email không

## Object

9. Lấy tất cả key của user đầu tiên
10. Lấy tất cả value của user đầu tiên
11. Duyệt object user ở Hà Nội và in theo mẫu

```js
  name: Nguyen Van A
  age: 21
  role: user
```

## String

13. Tạo slug từ tên
14. thêm slug vào các user

```js
  Nguyen Van A => nguyen-van-a
```

```js
const products = [
  {
    id: 1,
    name: "Clean Code",
    price: 30,
    category: "book",
    stock: 10,
  },
  {
    id: 2,
    name: "JavaScript Guide",
    price: 25,
    category: "book",
    stock: 5,
  },
  {
    id: 3,
    name: "Laptop Dell XPS",
    price: 1500,
    category: "laptop",
    stock: 2,
  },
  {
    id: 4,
    name: "Wireless Mouse",
    price: 20,
    category: "accessory",
    stock: 50,
  },
];

const customers = [
  {
    id: 1,
    name: "Hoang",
    email: "hoang@gmail.com",
    city: "Hanoi",
  },
  {
    id: 2,
    name: "Linh",
    email: "linh@gmail.com",
    city: "Danang",
  },
  {
    id: 3,
    name: "Minh",
    email: "minh@yahoo.com",
    city: "Hanoi",
  },
];

const orders = [
  {
    id: 1,
    customerId: 1,
    items: [
      { productId: 1, quantity: 2 },
      { productId: 4, quantity: 1 },
    ],
    status: "completed",
  },
  {
    id: 2,
    customerId: 2,
    items: [{ productId: 3, quantity: 1 }],
    status: "pending",
  },
  {
    id: 3,
    customerId: 1,
    items: [{ productId: 2, quantity: 1 }],
    status: "completed",
  },
  {
    id: 4,
    customerId: 3,
    items: [
      { productId: 1, quantity: 1 },
      { productId: 4, quantity: 3 },
    ],
    status: "completed",
  },
];
```

1. Lấy danh sách tên tất cả sản phẩm
2. Lấy danh sách product có category = "book"
3. Lấy danh sách customer ở Hanoi
4. Tìm product có price cao nhất
5. Lấy tất cả orders completed
6. Tính tổng số sản phẩm đã bán
7. Tính tổng doanh thu
8. Tính tổng tiền mỗi order

```js
[
  { orderId: 1, total: 80 },
  { orderId: 2, total: 1500 },
  { orderId: 3, total: 25 },
  { orderId: 4, total: 90 },
];
```

9. Tìm customer mua nhiều tiền nhất
