# Buổi 1: Tổng quan về Node.js & Ôn tập JavaScript

## 1. Tổng quan về [Node.js](https://nodejs.org/en/)

### 1.1. Giới thiệu về Node.js

- Node.js là gì?
  - Node.js là môi trường chạy JavaScript phía server, được xây dựng dựa trên [V8 Engine](https://v8.dev/) của Google Chrome.
  - Node.js cho phép chạy JavaScript ngoài môi trường trình duyệt.
  - Node.js được sử dụng để xây dựng các ứng dụng web, server, tools, ...

- Tại sao chọn Node.js?
  - JavaScript là ngôn ngữ phổ biến nhất thế giới.
  - Cộng đồng lớn mạnh, nhiều thư viện hỗ trợ.
  - Cơ chế Non-blocking I/O giúp xử lý nhiều request cùng lúc.

- Ứng dụng của Node.js
  - Xây dựng backend API (RESTful API, GraphQL API, ...)
  - Xây dựng ứng dụng real-time (Chat, WebSocket, ...)

### 1.2. Cài đặt Node.js

- Truy cập trang chủ [Node.js](https://nodejs.org/en/), tải bản mới nhất để cài đặt.
- Kiểm tra Node.js và npm đã cài đặt thành công chưa:

  ```bash
  node -v
  npm -v
  ```

## 2. Ôn tập JavaScript (ES6+)

### Array

#### 1. Nhóm DUYỆT MẢNG (Iteration methods)

| Phương thức | Trả về    | Mục đích              | Thay đổi mảng gốc | Ví dụ                             |
| ----------- | --------- | --------------------- | ----------------- | --------------------------------- |
| forEach()   | undefined | duyệt từng phần tử    | Không             | arr.forEach(x => console.log(x))  |
| map()       | Array mới | biến đổi từng phần tử | Không             | arr.map(x => x \* 2)              |
| filter()    | Array mới | lọc phần tử           | Không             | arr.filter(x => x > 10)           |
| find()      | Phần tử   | tìm phần tử đầu tiên  | Không             | arr.find(x => x.id === 1)         |
| findIndex() | Number    | tìm vị trí phần tử    | Không             | arr.findIndex(x => x.id === 1)    |
| reduce()    | Bất kỳ    | gom thành 1 giá trị   | Không             | arr.reduce((sum,x) => sum + x, 0) |
| some()      | Boolean   | có ít nhất 1 đúng     | Không             | arr.some(x => x.role === "admin") |
| every()     | Boolean   | tất cả đúng           | Không             | arr.every(x => x.active)          |

---

#### 2. Nhóm THÊM / XÓA PHẦN TỬ (Mutation methods)

| Phương thức | Mục đích                                          | Syntax                                                | Giải thích                                                                                                                    | Ví dụ code                            | Kết quả trả về                  | Mảng sau khi chạy |
| ----------- | ------------------------------------------------- | ----------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- | ------------------------------- | ----------------- |
| `push()`    | Thêm 1 hoặc nhiều phần tử vào **cuối** mảng       | `array.push(item1, item2, ...)`                       | Thêm các phần tử vào cuối mảng. Trả về độ dài mới của mảng.                                                                   | `let arr = [1,2,3]; arr.push(5)`      | `4`                             | `[1, 2, 3, 5]`    |
| `pop()`     | Xóa phần tử **cuối** và trả về nó                 | `array.pop()`                                         | Không cần tham số. Xóa phần tử cuối cùng và trả về giá trị vừa xóa.                                                           | `let arr = [1,2,3]; arr.pop()`        | `3`                             | `[1, 2]`          |
| `unshift()` | Thêm 1 hoặc nhiều phần tử vào **đầu** mảng        | `array.unshift(item1, item2, ...)`                    | Thêm các phần tử vào đầu mảng. Trả về độ dài mới của mảng.                                                                    | `let arr = [1,2,3]; arr.unshift(0)`   | `4`                             | `[0, 1, 2, 3]`    |
| `shift()`   | Xóa phần tử **đầu** và trả về nó                  | `array.shift()`                                       | Không cần tham số. Xóa phần tử đầu tiên và trả về giá trị vừa xóa.                                                            | `let arr = [1,2,3]; arr.shift()`      | `1`                             | `[2, 3]`          |
| `splice()`  | Thêm / xóa / thay thế phần tử tại vị trí chỉ định | `array.splice(start, deleteCount, item1, item2, ...)` | `start`: vị trí bắt đầu<br>`deleteCount`: số phần tử muốn xóa (0 = không xóa)<br>`item1, item2...`: các phần tử muốn chèn vào | `let arr = [1,2,3]; arr.splice(1, 1)` | `[2]` (mảng các phần tử bị xóa) | `[1, 3]`          |

> **Lưu ý quan trọng**:  
> `push()`, `pop()`, `unshift()`, `shift()` và `splice()` đều **thay đổi trực tiếp** mảng gốc (mutable methods).  
> Nếu muốn giữ nguyên mảng cũ, hãy dùng các phương thức không mutable như `slice()`, `concat()`, `map()`, `filter()`.

---

#### 3. Nhóm SAO CHÉP / CẮT / GHÉP

| Phương thức  | Mục đích                        | Thay đổi gốc | Syntax                                                           | Giải thích                                                                                    | Ví dụ code                                                             | Kết quả trả về           | Mảng sau khi chạy             |
| ------------ | ------------------------------- | ------------ | ---------------------------------------------------------------- | --------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- | ------------------------ | ----------------------------- |
| `slice()`    | Cắt / copy một phần của mảng    | Không        | `array.slice(start, end)`                                        | `start`: vị trí bắt đầu (bao gồm)<br>`end`: vị trí kết thúc (không bao gồm). Trả về mảng mới. | `let arr = [1,2,3,4,5]; arr.slice(1, 4)`                               | `[2, 3, 4]`              | `[1, 2, 3, 4, 5]` (không đổi) |
| `concat()`   | Nối mảng hoặc giá trị vào mảng  | Không        | `array.concat(item1, item2, ...)`<br>hoặc `array.concat(array2)` | Nối các mảng/giá trị và trả về **mảng mới**. Không thay đổi mảng gốc.                         | `let arr = [1,2]; arr.concat([3,4])`                                   | `[1, 2, 3, 4]`           | `[1, 2]` (không đổi)          |
| Spread `...` | Copy mảng hoặc merge nhiều mảng | Không        | `[...array]`<br>`[...arr1, ...arr2]`                             | Toán tử spread (ES6). Dùng để copy nông hoặc merge mảng rất gọn.                              | `const newArr = [...[1,2,3]]`<br>`const merged = [...[1,2], ...[3,4]]` | `[1,2,3]`<br>`[1,2,3,4]` | Mảng gốc không đổi            |

> **Lưu ý**:  
> Các phương thức `slice()`, `concat()` và Spread `...` đều **không thay đổi mảng gốc** (immutable).  
> Đây là cách an toàn để tạo bản sao hoặc mảng mới.

---

#### 4. Nhóm TÌM KIẾM / KIỂM TRA

| Phương thức   | Mục đích         | Ví dụ              |
| ------------- | ---------------- | ------------------ |
| includes()    | kiểm tra tồn tại | arr.includes(5)    |
| indexOf()     | tìm index        | arr.indexOf(5)     |
| lastIndexOf() | tìm index cuối   | arr.lastIndexOf(5) |

---

#### 5. Nhóm SẮP XẾP / BIẾN ĐỔI

| Phương thức | Mục đích       | Thay đổi gốc | Ví dụ                    |
| ----------- | -------------- | ------------ | ------------------------ |
| sort()      | sắp xếp        | Có           | arr.sort((a,b) => a - b) |
| reverse()   | đảo ngược      | Có           | arr.reverse()            |
| join()      | array → string | Không        | arr.join(",")            |

---

#### 6. Static methods

| Phương thức     | Mục đích       | Ví dụ              |
| --------------- | -------------- | ------------------ |
| Array.isArray() | kiểm tra array | Array.isArray(arr) |

### Object

#### 1. Các phương thức Object quan trọng

| Phương thức         | Mục đích             | Ví dụ                                | Kết quả            |
| ------------------- | -------------------- | ------------------------------------ | ------------------ |
| Object.keys(obj)    | lấy danh sách key    | Object.keys({name:"Hoang",age:21})   | ["name","age"]     |
| Object.values(obj)  | lấy danh sách value  | Object.values({name:"Hoang",age:21}) | ["Hoang",21]       |
| Object.entries(obj) | lấy key và value     | Object.entries({name:"Hoang"})       | [["name","Hoang"]] |
| Object.assign()     | copy / merge object  | Object.assign({}, obj1, obj2)        | object mới         |
| hasOwnProperty()    | kiểm tra key tồn tại | obj.hasOwnProperty("name")           | true / false       |

---

#### 2. Duyệt Object

| Cách           | Ví dụ                                      |
| -------------- | ------------------------------------------ |
| for...in       | for (const key in obj) {}                  |
| Object.keys    | Object.keys(obj).forEach(key => {})        |
| Object.entries | Object.entries(obj).forEach(([k,v]) => {}) |

Ví dụ:

```js
const user = {
  name: "Hoang",
  age: 21,
};

Object.entries(user).forEach(([key, value]) => {
  console.log(key, value);
});
```

#### 3. Thêm / sửa / xóa field

| Mục đích | Ví dụ            |
| -------- | ---------------- |
| Thêm     | `obj.age = 21`   |
| Sửa      | `obj.age = 22`   |
| Xóa      | `delete obj.age` |

---

#### 4. Copy và merge (khuyên dùng spread)

| Mục đích | Ví dụ                                 |
| -------- | ------------------------------------- |
| Copy     | `const copy = { ...obj }`             |
| Merge    | `const newObj = { ...obj1, ...obj2 }` |
| Update   | `const updated = { ...obj, age: 22 }` |

---

### String

Danh sách các phương thức xử lý chuỗi (String) được sử dụng nhiều nhất trong JavaScript.

| Phương thức     | Mục đích                         | Ví dụ                         | Kết quả         |
| --------------- | -------------------------------- | ----------------------------- | --------------- |
| `length`        | Lấy độ dài chuỗi                 | `"hello".length`              | `5`             |
| `toUpperCase()` | Chuyển thành chữ hoa             | `"hello".toUpperCase()`       | `"HELLO"`       |
| `toLowerCase()` | Chuyển thành chữ thường          | `"HELLO".toLowerCase()`       | `"hello"`       |
| `trim()`        | Xóa khoảng trắng đầu & cuối      | `" hi ".trim()`               | `"hi"`          |
| `includes()`    | Kiểm tra chuỗi có chứa substring | `"hello".includes("el")`      | `true`          |
| `startsWith()`  | Kiểm tra bắt đầu bằng            | `"hello".startsWith("he")`    | `true`          |
| `endsWith()`    | Kiểm tra kết thúc bằng           | `"hello".endsWith("lo")`      | `true`          |
| `indexOf()`     | Tìm vị trí xuất hiện đầu tiên    | `"hello".indexOf("l")`        | `2`             |
| `lastIndexOf()` | Tìm vị trí xuất hiện cuối cùng   | `"hello".lastIndexOf("l")`    | `3`             |
| `slice()`       | Cắt chuỗi (hỗ trợ số âm)         | `"hello".slice(1,4)`          | `"ell"`         |
| `substring()`   | Cắt chuỗi                        | `"hello".substring(1,4)`      | `"ell"`         |
| `replace()`     | Thay thế chuỗi đầu tiên          | `"hello".replace("l","x")`    | `"hexlo"`       |
| `replaceAll()`  | Thay thế tất cả chuỗi            | `"hello".replaceAll("l","x")` | `"hexxo"`       |
| `split()`       | Tách chuỗi thành mảng            | `"a,b,c".split(",")`          | `["a","b","c"]` |
| `concat()`      | Nối chuỗi                        | `"Hello".concat(" World")`    | `"Hello World"` |
| `repeat()`      | Lặp lại chuỗi                    | `"hi".repeat(3)`              | `"hihihi"`      |

### Destructuring

Destructuring là cú pháp giúp lấy giá trị từ object hoặc array và gán vào biến một cách nhanh chóng.
Không destructuring:

```js
const user = {
  name: "Hoang",
  age: 21,
};

const name = user.name;
const age = user.age;
```

Dùng destructuring:

```js
const { name, age } = user;
```

#### 1.1 Object Destructuring

Cú pháp cơ bản

```js
const object = {
  key: value,
};

const { key } = object;
```

Ví dụ:

```js
const user = {
  name: "Hoang",
  age: 21,
};

const { name, age } = user;

console.log(name); // Hoang
```

Đổi tên biến

```js
const user = {
  name: "Hoang",
};

const { name: userName } = user;

console.log(userName);
```

Backend dùng khi tránh trùng tên biến.
Gán giá trị mặc định

```js
const user = {
  name: "Hoang",
};

const { name, age = 18 } = user;

console.log(age); // 18
```

Dùng khi field có thể không tồn tại.
Destructuring nhiều cấp (nested)

```js
const user = {
  address: {
    city: "Hanoi",
  },
};

const {
  address: { city },
} = user;

console.log(city);
```

#### 1.2 Array Destructuring

Cú pháp

```js
const arr = [1, 2, 3];

const [a, b, c] = arr;
```

### Spread Operator (...) với Array và Object

#### Array

| Mục đích          | Cú pháp            | Ví dụ                               | Kết quả   |
| ----------------- | ------------------ | ----------------------------------- | --------- |
| Copy array        | [...arr]           | const copy = [...[1,2,3]]           | [1,2,3]   |
| Thêm phần tử cuối | [...arr, item]     | const newArr = [...[1,2], 3]        | [1,2,3]   |
| Thêm phần tử đầu  | [item, ...arr]     | const newArr = [0, ...[1,2]]        | [0,1,2]   |
| Merge array       | [...arr1, ...arr2] | const result = [...[1,2], ...[3,4]] | [1,2,3,4] |

---

#### Object

| Mục đích           | Cú pháp                  | Ví dụ                              | Kết quả                |
| ------------------ | ------------------------ | ---------------------------------- | ---------------------- |
| Copy object        | {...obj}                 | const copy = {...{name:"Hoang"}}   | {name:"Hoang"}         |
| Thêm field         | {...obj, field:value}    | {...{name:"Hoang"}, age:21}        | {name:"Hoang", age:21} |
| Update field       | {...obj, field:newValue} | {...{age:20}, age:21}              | {age:21}               |
| Merge object       | {...obj1, ...obj2}       | {...{name:"Hoang"}, ...{age:21}}   | {name:"Hoang", age:21} |
| Override khi merge | {...obj, ...update}      | {...{name:"Old"}, ...{name:"New"}} | {name:"New"}           |

---

Ví dụ

Update user:

```js
const user = {
  name: "Hoang",
  age: 20,
};

const update = {
  age: 21,
};

const updatedUser = {
  ...user,
  ...update,
};
```
