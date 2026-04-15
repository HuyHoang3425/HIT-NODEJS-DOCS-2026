# Buổi 7: Authentication & Authorization

## 1. Authentication & Authorization

### 1.1 Authentication

- Authentication là quá trình xác thực danh tính của người dùng. Nó đảm bảo rằng người dùng là ai mà họ nói rằng họ là.
- Authentication thường được thực hiện thông qua việc yêu cầu người dùng cung cấp thông tin đăng nhập như tên người dùng và mật khẩu.
- Mục tiêu: Xác thực danh tính của người dùng.
- Quy trình:
  1. Người dùng cung cấp thông tin đăng nhập (username/password).
  2. Hệ thống kiểm tra thông tin đăng nhập với cơ sở dữ liệu:
     - Nếu thông tin hợp lệ, người dùng được xác thực và có thể truy cập vào hệ thống.
     - Nếu thông tin không hợp lệ, hệ thống từ chối quyền truy cập.

- Ví dụ:
  Facebook, Google: Người dùng đăng nhập bằng tài khoản email và mật khẩu.
  Điện thoại: Nhập mã PIN hoặc sử dụng vân tay để xác thực danh tính.

### 1.2 Authorization

- Authorization là quá trình xác định quyền truy cập của người dùng vào các tài nguyên hoặc chức năng trong hệ thống.
- Nó xác định người dùng có quyền làm gì trong hệ thống sau khi đã được xác thực.
- Mục tiêu: Xác định quyền truy cập của người dùng.
- Quy trình:
  1. Người dùng đã được xác thực.
  2. Hệ thống kiểm tra quyền truy cập của người dùng đối với tài nguyên hoặc chức năng cụ thể:
     - Nếu người dùng có quyền, họ có thể truy cập tài nguyên hoặc chức năng đó.
     - Nếu không, hệ thống từ chối quyền truy cập.

- Ví dụ:
  - Trong một trang quản trị website:
    - Admin: có thể truy cập.
    - User/Guest: không thể truy cập.

|             | Authentication                                                       | Authorization                                                         |
| ----------- | -------------------------------------------------------------------- | --------------------------------------------------------------------- |
| Định nghĩa  | Xác thực danh tính người dùng trước khi cung cấp quyền truy cập      | Xác định quyền truy cập của người dùng vào tài nguyên hoặc chức năng  |
| Mục tiêu    | Xác nhận danh tính người dùng, ngăn chặn truy cập trái phép          | Đảm bảo người dùng chỉ có quyền truy cập vào những gì họ được phép    |
| Quy trình   | So sánh thông tin đăng nhập với cơ sở dữ liệu                        | Cấp/từ chối quyền truy cập dựa trên vai trò hoặc quyền của người dùng |
| Phương pháp | Sử dụng thông tin đăng nhập (username/password), mã OTP, vân tay,... | Sử dụng vai trò (RBAC), quyền (PBAC)                                  |
| Ví dụ       | Đăng nhập vào tài khoản email, mạng xã hội                           | Quyền truy cập vào trang quản trị, chức năng cụ thể trong ứng dụng    |

## 2. Cơ chế xác thực với Json Web Token (JWT)

### 2.1 JWT là gì?

- JWT (JSON Web Token) là một tiêu chuẩn mở (RFC 7519) cho phép truyền tải thông tin giữa các bên dưới dạng đối tượng JSON.
- JWT thường được sử dụng để xác thực và truyền tải thông tin giữa client và server trong các ứng dụng web hiện đại.
- Hiểu đơn giản, JWT là một "vé" dạng mã hóa, chứa thông tin người dùng, được server cấp phát sau khi người dùng đăng nhập thành công. Client sẽ gửi "vé" này trong các yêu cầu tiếp theo để xác thực danh tính và quyền truy cập của mình.

### 2.2 Cấu trúc JWT

- JWT được chia thành 3 phần, mỗi phần được phân tách bằng dấu chấm (.):

  ```
  header.payload.signature
  ```

- **Header**: Chứa thông tin về loại token và thuật toán mã hóa được sử dụng. Ví dụ:

  ```json
  {
    "alg": "HS256",
    "typ": "JWT"
  }
  ```

- **Payload**: Chứa thông tin mà bạn muốn truyền tải. Đây có thể là thông tin người dùng, quyền truy cập, thời gian hết hạn, v.v. Ví dụ:

  ```json
  {
    "id": 1,
    "sub": "1234567890",
    "iat": 1516239022
  }
  ```

- **Signature**: Được tạo ra bằng cách mã hóa `header` và `payload` với một khóa bí mật (secret key) và thuật toán đã chỉ định trong header. Ví dụ:

  ```
  HMACSHA256(
      base64UrlEncode(header) + "." +
      base64UrlEncode(payload),
      your-256-bit-secret
  )
  ```

### 2.3 Quy trình xác thực với JWT

1. Người dùng gửi thông tin đăng nhập (`username`/`password`) đến server.
2. Server xác thực thông tin đăng nhập.
3. Nếu thông tin hợp lệ, server tạo JWT với thông tin người dùng và gửi lại cho client.
4. Client lưu JWT (thường là trong `localStorage`, `cookie` hoặc `sessionStorage`).
5. Trong các yêu cầu tiếp theo, client gửi JWT trong header của yêu cầu (thường là `Authorization` header).
   ```
   Authorization: Bearer <token>
   ```
6. Server kiểm tra JWT:
   - Nếu JWT hợp lệ, server cho phép truy cập vào tài nguyên.
   - Nếu JWT không hợp lệ hoặc đã hết hạn, server từ chối quyền truy cập.

## 3. Xây dựng API xác thực với JWT

### 3.1 Cài đặt các thư viện cần thiết

```bash
npm install jsonwebtoken
```

### 3.2 Đăng ký người dùng

`/controllers/auth.controller.js`

```javascript
const register = catchAsync(async (req, res) => {
  const { email } = req.body;

  const isExist = await User.findOne({ email });

  if (isExist) {
    throw new ApiError(httpStatus.CONFLICT, "Người dùng đã tồn tại");
  }

  const user = await User.create(req.body);

  res.status(httpStatus.CREATED).json({
    statusCode: httpStatus.CREATED,
    message: "Tạo người dùng thành công.",
    data: {
      user,
    },
  });
});
```

### 3.3 Đăng nhập người dùng

`/controllers/auth.controller.js`

```javascript
const login = catchAsync(async (req, res) => {
  const { email, password } = req.body;

  const user = await User.findOne({ email });
  if (!user) {
    throw new ApiError(httpStatus.UNAUTHORIZED, "Sai thông tin đăng nhập");
  }

  const isPasswordMatch = await bcrypt.compare(password, user.password);
  if (!isPasswordMatch) {
    throw new ApiError(httpStatus.UNAUTHORIZED, "Sai thông tin đăng nhập");
  }

  const token = jwt.sign({ id: user._id }, process.env.JWT_SECRET, {
    expiresIn: "1h",
  });

  res.status(httpStatus.OK).json({
    statusCode: httpStatus.OK,
    message: "Đăng nhập thành công.",
    data: {
      token,
    },
  });
});
```

Tách chức năng của jwt

`utils/jwt.js`

```javascript
const jwt = require("jsonwebtoken");

const generateToken = (userId) => {
  return jwt.sign({ id: userId }, process.env.JWT_SECRET, {
    expiresIn: process.env.JWT_EXPIRES_IN,
  });
};
```

### 3.4 Middleware xác thực JWT

- Middleware này sẽ được sử dụng trong các route cần xác thực người dùng.

`utils/jwt.js`

```javascript
const extractToken = (req) => {
  const authHeader = req.headers.authorization;
  if (!authHeader || !authHeader.startsWith("Bearer ")) {
    return null;
  }
  const token = authHeader.split(" ")[1];
  return token;
};

const verifyToken = (token) => {
  const payload = jwt.verify(token, process.env.JWT_SECRET);
  return payload;
};
```

`/middlewares/auth.middleware.js`

```javascript
const auth = catchAsync(async (req, res, next) => {
  const token = extractToken(req);
  if (!token) {
    throw new ApiError(httpStatus.UNAUTHORIZED, "Vui lòng đăng nhập");
  }

  const payload = verifyToken(token);

  const { id } = payload;
  const user = await User.findById(id);
  if (!user) {
    throw new ApiError(httpStatus.UNAUTHORIZED, "Người dùng không tồn tại");
  }

  req.user = user;
  next();
});
```

Ví dụ:
`/routes/user.route.js`

```javascript
const auth = require("../middlewares/auth.middleware");

router.get("/profile", auth, (req, res) => {
  res.status(httpStatus.OK).json({
    statusCode: httpStatus.OK,
    message: "Lấy thông tin người dùng thành công.",
    data: {
      user: req.user,
    },
  });
});
```

### 3.5 Middleware phân quyền theo vai trò

- Middleware này sẽ được sử dụng để kiểm tra xem người dùng có quyền truy cập vào tài nguyên hay không.
- Ví dụ: chỉ cho phép người dùng có vai trò `admin` truy cập vào một số route nhất định.

`/middlewares/auth.middleware.js`

```javascript
const adminRoute = catchAsync(async (req, res, next) => {
  if (req.user.role !== "admin") {
    throw new ApiError(
      httpStatus.FORBIDDEN,
      "Bạn không có quyền truy cập vào tài nguyên này",
    );
  }
  next();
});
```

`/routes/user.route.js`

```javascript
const auth = require("../middlewares/auth.middleware");
const adminRoute = require("../middlewares/auth.middleware");

router.get("/admin", auth, adminRoute, (req, res) => {
  res.status(httpStatus.OK).json({
    statusCode: httpStatus.OK,
    message: "Lấy thông tin người dùng thành công.",
    data: {
      user: req.user,
    },
  });
});
```

### 3.5 AccessToken và RefreshToken

Trong hệ thống xác thực hiện đại, **AccessToken** và **RefreshToken** thường được sử dụng để tăng tính bảo mật và cải thiện trải nghiệm người dùng khi đăng nhập.

#### 3.5.1 AccessToken là gì?

AccessToken là mã xác thực được cấp cho người dùng sau khi đăng nhập thành công. Token này chứa thông tin định danh người dùng và quyền truy cập hệ thống.

- Thời gian sống ngắn (ví dụ: 15 phút, 1 giờ).
- Được gửi kèm trong mỗi request tới API.
- Dùng để xác minh người dùng đã đăng nhập.

Ví dụ gửi AccessToken trong Header:

```http
Authorization: Bearer <access_token>
```

#### 3.5.2 RefreshToken là gì?

RefreshToken là mã dùng để cấp lại AccessToken mới khi AccessToken hết hạn mà người dùng không cần đăng nhập lại.

Thời gian sống dài hơn AccessToken (ví dụ: 7 ngày, 30 ngày).
Thường lưu trong HttpOnly Cookie hoặc nơi an toàn.
Không dùng trực tiếp để gọi API nghiệp vụ.

#### 3.5.3 Quy trình hoạt động

Người dùng đăng nhập bằng tài khoản và mật khẩu.
Server kiểm tra thông tin hợp lệ.
Server trả về:
AccessToken
RefreshToken
Client dùng AccessToken để gọi API.
Khi AccessToken hết hạn:
Client gửi RefreshToken lên server.
Server kiểm tra RefreshToken.
Nếu hợp lệ, cấp AccessToken mới.
Người dùng tiếp tục sử dụng hệ thống mà không cần đăng nhập lại.

#### 3.5.4 Ưu điểm

Tăng bảo mật do AccessToken có thời gian sống ngắn.
Giảm số lần đăng nhập lại.
Hỗ trợ quản lý phiên đăng nhập hiệu quả.
Có thể thu hồi RefreshToken khi cần.

#### 3.5.5 Ví dụ

Model Token dùng để lưu refreshToken

```js
const mongoose = require("mongoose");

const tokenSchema = new mongoose.Schema(
  {
    userId: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "User",
      required: true,
    },
    refreshToken: {
      type: String,
      required: true,
    },
    expireAt: {
      type: Date,
      // Tài liệu sẽ tự động bị xóa sau 60 giây kể từ thời điểm ghi trong biến expireAt
      expires: 60,
    },
  },
  {
    timestamps: true,
  },
);

const Token = mongoose.model("Token", tokenSchema);

module.exports = Token;
```

Đăng nhập

```js
// [POST] /auth/login
const login = catchAsync(async (req, res) => {
  const { email, password } = req.body;

  const user = await userModel
    .findOne({ email })
    .select("fullname email password role _id");

  if (!user) {
    throw new ApiError(
      StatusCodes.UNAUTHORIZED,
      "Email hoặc mật khẩu không hợp lệ.",
    );
  }

  const match = await bcrypt.compare(password, user.password);
  if (!match) {
    throw new ApiError(
      StatusCodes.UNAUTHORIZED,
      "Email hoặc mật khẩu không hợp lệ.",
    );
  }

  // Tạo token
  const accessToken = jwt.generateAccessToken({ userId: user.id });
  const refreshToken = jwt.generateRefreshToken({ userId: user.id });

  // Lưu token vào db
  await tokenModel.create({
    userId: user.id,
    refreshToken,
    expireAt: new Date(),
  });

  // Thiết lập Cookie
  res.cookie("refreshToken", refreshToken, {
    httpOnly: true, // Chỉ server mới có thể truy cập token này
    secure: env.server.nodeEnv === "production", // Chỉ gửi cookie qua kết nối HTTPS trong production
    sameSite: env.server.nodeEnv === "production" ? "none" : "lax", // None khi 2 domain khác nhau, lax khi cùng domain
    maxAge: 15 * 24 * 60 * 60 * 1000,
  });

  const userResponse = user.toObject();
  delete userResponse.password;

  res.status(StatusCodes.OK).json(
    response(StatusCodes.OK, "Đăng nhập thành công.", {
      user: userResponse,
      accessToken,
    }),
  );
});
```

RefreshToken

```js
// [POST] /auth/refresh-token
const refreshToken = catchAsync(async (req, res) => {
  // Lấy token từ cookie hoặc body
  const tokenValue = req.cookies?.refreshToken || req.body.refreshToken;

  if (!tokenValue) {
    throw new ApiError(
      StatusCodes.UNAUTHORIZED,
      "Không tìm thấy refresh token.",
    );
  }

  const tokenDoc = await tokenModel.findOne({ refreshToken: tokenValue });
  if (!tokenDoc) {
    throw new ApiError(
      StatusCodes.UNAUTHORIZED,
      "Token hết hạn hoặc không tồn tại.",
    );
  }

  let payload;
  try {
    payload = jwt.verifyRefreshToken(tokenValue);
  } catch (err) {
    if (err.name === "TokenExpiredError") {
      await tokenModel.deleteOne({ refreshToken: tokenValue });
      throw new ApiError(
        StatusCodes.UNAUTHORIZED,
        "Phiên đăng nhập hết hạn, vui lòng đăng nhập lại.",
      );
    }
    throw new ApiError(StatusCodes.UNAUTHORIZED, "Token không hợp lệ.");
  }

  if (!payload) {
    throw new ApiError(StatusCodes.UNAUTHORIZED, "Token không hợp lệ.");
  }

  if (tokenDoc.userId.toString() !== payload.userId) {
    throw new ApiError(StatusCodes.UNAUTHORIZED, "Token không khớp với user.");
  }

  // Tạo Access Token mới
  const accessToken = jwt.generateAccessToken({ userId: payload.userId });

  res.status(StatusCodes.OK).json(
    response(StatusCodes.OK, "Refresh token thành công.", {
      accessToken,
    }),
  );
});
```
