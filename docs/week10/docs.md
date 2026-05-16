# Buổi 9: Mối quan hệ giữa các Model. Truy vấn nâng cao

## 1. Mối quan hệ giữa các Model (MongoDB, Mongoose)

### 1.1 Mối quan hệ 1-1

- Mối quan hệ 1-1 là mối quan hệ giữa hai bảng, trong đó mỗi bản ghi trong bảng này chỉ liên kết với một bản ghi duy nhất trong bảng kia.
- Ví dụ: Một người dùng chỉ có một địa chỉ giao hàng duy nhất.
- Để thiết lập mối quan hệ 1-1 trong Mongoose, bạn có thể sử dụng `ref` để tham chiếu đến model khác.

```javascript
import mongoose from 'mongoose';
const { Schema } = mongoose;

const userSchema = new Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  address: {
    type: Schema.Types.ObjectId,
    ref: 'Address',
  },
}, { timestamps: true });

const addressSchema = new Schema({
  street: String,
  city: String,
  state: String,
  zip: String,
}, { timestamps: true });

const User = mongoose.model('User', userSchema);
const Address = mongoose.model('Address', addressSchema);

export { User, Address };
```

### 1.2 Mối quan hệ 1-n

- Mối quan hệ 1-n là mối quan hệ giữa hai bảng, trong đó mỗi bản ghi trong bảng này có thể liên kết với nhiều bản ghi trong bảng kia.
- Ví dụ: Một người dùng có thể có nhiều bài viết.
- Để thiết lập mối quan hệ 1-n trong Mongoose, bạn có thể sử dụng `ref` để tham chiếu đến model khác.

```javascript
import mongoose from 'mongoose';
const { Schema } = mongoose;

const userSchema = new Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
}, { timestamps: true });

const postSchema = new Schema({
  title: { type: String, required: true },
  content: { type: String, required: true },
  author: {
    type: Schema.Types.ObjectId,
    ref: 'User',
    required: true,
  },
}, { timestamps: true });

const User = mongoose.model('User', userSchema);
const Post = mongoose.model('Post', postSchema);

export { User, Post };
```

### 1.3 Mối quan hệ n-n

- Mối quan hệ n-n là mối quan hệ giữa hai bảng, trong đó mỗi bản ghi trong bảng này có thể liên kết với nhiều bản ghi trong bảng kia và ngược lại.
- Ví dụ: Một bài viết có thể có nhiều thẻ và một thẻ có thể thuộc về nhiều bài viết.
- Để thiết lập mối quan hệ n-n trong Mongoose, bạn có thể sử dụng `ref` để tham chiếu đến model khác và sử dụng mảng để lưu trữ nhiều giá trị.

```javascript
import mongoose from 'mongoose';
const { Schema } = mongoose;

const postSchema = new Schema({
  title: { type: String, required: true },
  content: { type: String, required: true },
  tags: [
    {
      type: Schema.Types.ObjectId,
      ref: 'Tag',
    },
  ],
}, { timestamps: true });

const tagSchema = new Schema({
  name: { type: String, required: true, unique: true },
}, { timestamps: true });

const Post = mongoose.model('Post', postSchema);
const Tag = mongoose.model('Tag', tagSchema);

export { Post, Tag };
```

### 1.4 Mối quan hệ n-n với bảng trung gian

- Mối quan hệ n-n với bảng trung gian là mối quan hệ giữa hai bảng, trong đó mỗi bản ghi trong bảng này có thể liên kết với nhiều bản ghi trong bảng kia và ngược lại, nhưng có một bảng trung gian để lưu trữ mối quan hệ này.
- Ví dụ: Một người dùng có thể tham gia nhiều nhóm và một nhóm có thể có nhiều người dùng.
- Để thiết lập mối quan hệ n-n với bảng trung gian trong Mongoose, bạn có thể tạo một model mới để lưu trữ mối quan hệ này.

```javascript
import mongoose from 'mongoose';
const { Schema } = mongoose;

const userSchema = new Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
}, { timestamps: true });

const groupSchema = new Schema({
  name: { type: String, required: true },
  members: [
    {
      type: Schema.Types.ObjectId,
      ref: 'User',
    },
  ],
}, { timestamps: true });

const User = mongoose.model('User', userSchema);
const Group = mongoose.model('Group', groupSchema);

export { User, Group };
```

## 2. Truy vấn nâng cao

Dưới đây là bảng tổng hợp các toán tử phổ biến trong Mongoose/MongoDB giúp bạn thực hiện các truy vấn nâng cao một cách dễ dàng:

| Toán tử | Ý nghĩa | Ví dụ |
| :--- | :--- | :--- |
| `$eq` | Bằng | `{ age: { $eq: 18 } }` |
| `$ne` | Khác | `{ age: { $ne: 18 } }` |
| `$gt` | Lớn hơn | `{ age: { $gt: 18 } }` |
| `$gte` | Lớn hơn hoặc bằng | `{ age: { $gte: 18 } }` |
| `$lt` | Nhỏ hơn | `{ age: { $lt: 18 } }` |
| `$lte` | Nhỏ hơn hoặc bằng | `{ age: { $lte: 18 } }` |
| `$in` | Nằm trong list | `{ role: { $in: ["ADMIN", "USER"] } }` |
| `$or` | Điều kiện OR | `{ $or: [{ age: 18 }, { role: "ADMIN" }] }` |
| `$and` | Điều kiện AND | `{ $and: [{ age: { $gt: 18 } }, { active: true }] }` |
| `$exists` | Kiểm tra field tồn tại | `{ phone: { $exists: true } }` |
| `$regex` | Tìm kiếm chuỗi | `{ name: { $regex: "hoang", $options: "i" } }` |

### 2.1. Lọc dữ liệu

- Sử dụng `find()` để lọc dữ liệu theo điều kiện.

```javascript
// Hàm mẫu: Lọc những User có độ tuổi lớn hơn 18
const getAdultUsers = async () => {
  try {
    const users = await User.find({ age: { $gt: 18 } });
    return users;
  } catch (error) {
    console.error('Lỗi khi lọc dữ liệu:', error);
    throw error;
  }
};
```

### 2.2. Tìm kiếm dữ liệu

- Sử dụng `find()` để tìm kiếm dữ liệu theo điều kiện.

```javascript
// Hàm mẫu: Tìm kiếm User có tên chứa "John" (không phân biệt hoa thường)
const searchUsersByName = async (keyword) => {
  try {
    const users = await User.find({ 
      name: { $regex: keyword, $options: 'i' } 
    });
    return users;
  } catch (error) {
    console.error('Lỗi khi tìm kiếm:', error);
    throw error;
  }
};
```

### 2.3. Sắp xếp dữ liệu

- Sử dụng `sort()` để sắp xếp dữ liệu theo điều kiện.

```javascript
// Hàm mẫu: Lấy danh sách User và sắp xếp theo tuổi giảm dần (-1) hoặc tăng dần (1)
const getSortedUsers = async () => {
  try {
    const users = await User.find().sort({ age: -1 });
    return users;
  } catch (error) {
    console.error('Lỗi khi sắp xếp:', error);
    throw error;
  }
};
```

### 2.4. Phân trang

- Sử dụng `skip()` và `limit()` để phân trang dữ liệu.

```javascript
// Hàm mẫu: Phân trang danh sách User một cách tối ưu
const getUsersByPage = async (page = 1, limit = 10) => {
  try {
    const skip = (page - 1) * limit;
    
    // Sử dụng Promise.all để chạy 2 truy vấn song song (lấy dữ liệu & tổng số)
    const [users, totalUsers] = await Promise.all([
      User.find().skip(skip).limit(limit),
      User.countDocuments()
    ]);

    const totalPages = Math.ceil(totalUsers / limit);

    return {
      data: users,
      pagination: {
        totalItems: totalUsers,
        totalPages,
        currentPage: page,
        itemsPerPage: limit
      }
    };
  } catch (error) {
    console.error('Lỗi khi phân trang:', error);
    throw error;
  }
};
```

## 3. Deploy ứng dụng lên Render
