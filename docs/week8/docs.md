# Buổi 8: Phân quyền trong Express.js (RBAC & ABAC)

Chào các bạn! Trong các buổi trước, chúng ta đã tìm hiểu về xác thực (Authentication) với JWT. Hôm nay, chúng ta sẽ bước sang một khái niệm quan trọng không kém để bảo vệ hệ thống: **Phân quyền (Authorization)**.

Bài giảng này sẽ giúp các bạn hiểu sâu về hai mô hình phân quyền phổ biến nhất hiện nay là RBAC và ABAC, đồng thời biết cách áp dụng chúng vào thực tế dự án Express.js.

---

## 1. Mở đầu: Authentication vs Authorization

Khi làm việc với bảo mật web, chúng ta thường gặp hai khái niệm thường xuyên đi đôi với nhau nhưng mang ý nghĩa hoàn toàn khác biệt:

- **Authentication (Xác thực - Bạn là ai?):** Quá trình xác minh danh tính của người dùng.
  - _Ví dụ:_ Bạn dùng thẻ học sinh để chứng minh bạn là sinh viên của trường. Hoặc bạn dùng Username/Password để đăng nhập vào hệ thống.
- **Authorization (Phân quyền - Bạn được làm gì?):** Quá trình kiểm tra xem người dùng (đã xác thực) có quyền thực hiện một hành động cụ thể hay không.
  - _Ví dụ:_ Bạn có thẻ học sinh (đã Authentication), bạn được phép vào thư viện (Authorization). Nhưng bạn không được phép vào phòng giáo viên (Không có quyền Authorization).

Trong hệ thống thực tế: Authentication xảy ra trước (Login -> lấy Token). Sau đó, mỗi khi user gọi API, chúng ta sẽ kiểm tra Authorization (Token đó có quyền xoá bài viết không?).

---

## 2. RBAC (Role-Based Access Control)

### 2.1. Khái niệm RBAC

**RBAC (Kiểm soát truy cập dựa trên vai trò)** là một phương pháp phân quyền trong đó quyền truy cập hệ thống được gán cho các "vai trò" (role) thay vì gán trực tiếp cho từng "người dùng" (user). Sau đó, người dùng sẽ được gán các vai trò tương ứng.

RBAC = Phân quyền theo vai trò
Ví dụ:

```js
User A → role: user
User B → role: admin
```

```js
admin → full quyền
user → chỉ xem
```

### 2.2. Luồng hoạt động (Flow)

`User` -> `thuộc về Role` -> `Role có các Permission (Quyền)` -> `Thực hiện hành động`

### 2.3. Thiết kế Database cho RBAC

Để triển khai RBAC một cách linh hoạt, ta thường thiết kế 4 bảng:

1. `users`: Lưu thông tin người dùng.
2. `roles`: Lưu danh sách các vai trò (ví dụ: Admin, User, Manager).

```js
// models/Role.js
import mongoose from "mongoose";

const roleSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    unique: true,
  },

  permissions: [
    {
      type: mongoose.Schema.Types.ObjectId,
      ref: "Permission",
    },
  ],
});

export default mongoose.model("Role", roleSchema);
```

3. `permissions`: Lưu danh sách các quyền cụ thể (ví dụ: `CREATE_POST`, `DELETE_USER`).

```js
// models/Permission.js
import mongoose from "mongoose";

const permissionSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    unique: true,
  },
});

export default mongoose.model("Permission", permissionSchema);
```

**Middleware kiểm tra Permission**

```js
const checkPermission = (requiredPermission) => {
  return (req, res, next) => {
    const userPermissions = req.user.permissions; // VD: ['READ_USER', 'CREATE_POST']

    if (!userPermissions.includes(requiredPermission)) {
      return res.status(403).json({ message: "Không đủ quyền!" });
    }
    next();
  };
};
```

## 3. ABAC (Attribute-Based Access Control)

### 3.1. Khái niệm ABAC

**ABAC (Kiểm soát truy cập dựa trên thuộc tính)** là mô hình kiểm tra quyền dựa trên việc đánh giá các thuộc tính (attribute) thay vì vai trò cứng nhắc.

ABAC = Phân quyền dựa trên thuộc tính (điều kiện)
Ví dụ:

- Chỉ sửa bài của mình
- Nhân viên chỉ xem dữ liệu phòng ban của mình

### 3.2. Thành phần chính của ABAC

1. **User (Subject):** Thuộc tính của người thực hiện (tuổi, phòng ban, ID, chức vụ).
2. **Resource (Object):** Thuộc tính của tài nguyên bị tác động (chủ sở hữu tài nguyên, trạng thái bài viết, ngày tạo).
3. **Action:** Hành động đang muốn thực hiện (read, update, delete).
4. **Context (Environment):** Thuộc tính môi trường (thời gian trong ngày, địa chỉ IP, thiết bị).

## 4. So sánh RBAC vs ABAC

| Tiêu chí            | RBAC (Role-Based)                                                         | ABAC (Attribute-Based)                                                                          |
| :------------------ | :------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------- |
| **Cơ sở cấp quyền** | Dựa trên Vai trò (Role)                                                   | Dựa trên Thuộc tính (Attribute)                                                                 |
| **Độ phức tạp**     | Đơn giản, dễ cài đặt và quản lý ban đầu.                                  | Phức tạp, cần viết nhiều logic kiểm tra.                                                        |
| **Tính linh hoạt**  | Kém linh hoạt (cứng nhắc theo nhóm).                                      | Rất linh hoạt (kiểm tra điều kiện động, linh động).                                             |
| **Ví dụ**           | "Chỉ Admin mới được xoá bài".                                             | "Chỉ tác giả bài viết mới được xoá bài đó".                                                     |
| **Khi nào dùng?**   | Các hệ thống quản trị nội bộ đơn giản, phân cấp rõ ràng (CMS, Dashboard). | Các hệ thống có dữ liệu cá nhân hoá cao, yêu cầu bảo mật chi tiết (Mạng xã hội, App ngân hàng). |

---

## 5. Kết hợp RBAC + ABAC: Sức mạnh tối thượng

Trong thực tế, không dự án lớn nào chỉ dùng thuần tuý 1 cái. Chúng ta luôn kết hợp cả hai.

_Ví dụ:_

- **User bình thường** chỉ có thể sửa bài viết của chính họ (ABAC).
- **Admin** có quyền sửa TẤT CẢ bài viết của mọi người (RBAC override).

**Code minh hoạ kết hợp:**

```javascript
const canEditPost = async (req, res, next) => {
  const postId = req.params.id;
  const user = req.user;

  const post = await Post.findById(postId);
  if (!post) return res.status(404).json({ message: "Post not found" });

  // RBAC: Nếu là Admin => Cho qua luôn
  if (user.role === "Admin") {
    req.post = post;
    return next();
  }

  // ABAC: Nếu không phải Admin, kiểm tra xem có phải tác giả không
  if (post.authorId.toString() === user.id.toString()) {
    req.post = post;
    return next();
  }

  // Từ chối
  return res
    .status(403)
    .json({ message: "Bạn không có quyền chỉnh sửa bài viết này!" });
};
```
