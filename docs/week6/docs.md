# Buổi 6: Middleware, Error handling, Validation

## 1. [Middleware](https://expressjs.com/en/guide/using-middleware.html)

- Middleware là một hàm có quyền truy cập vào đối tượng yêu cầu (req), đối tượng phản hồi (res) và hàm tiếp theo trong chuỗi middleware của ứng dụng.

- Middleware có thể thực hiện các tác vụ như:
  - Thực thi bất kỳ đoạn mã nào.
  - Thay đổi đối tượng yêu cầu và phản hồi.
  - Kết thúc chu kỳ yêu cầu - phản hồi.
  - Gọi hàm tiếp theo trong chuỗi middleware.

- Nếu middleware hiện tại không kết thúc luồng yêu cầu - phản hồi, nó phải gọi hàm `next()` để chuyển quyền điều khiển cho middleware tiếp theo trong chuỗi. Nếu middleware hiện tại không gọi `next()`, yêu cầu sẽ bị treo và không bao giờ nhận được phản hồi.

- Middleware cơ bản nhận vào 3 hoặc 4 đối số:
  - `req`: Đối tượng yêu cầu (request).
  - `res`: Đối tượng phản hồi (response).
  - `next`: Hàm tiếp theo trong chuỗi middleware.
  - `err`: Đối tượng lỗi (nếu có).

  ```js
  const middleware = (req, res, next) => {
    // Thực hiện một số tác vụ
    console.log("Middleware được gọi");

    // Gọi hàm tiếp theo trong chuỗi middleware
    next(); // Nếu không gọi next(), yêu cầu sẽ bị treo
  };
  ```

- Cách hoạt động của middleware trong Expressjs:

  ```js
  app.get("/route", middleware1, middleware2, (req, res) => {
    // Xử lý yêu cầu
    res.send("Hello World");
  });
  ```

  - Khi một request đến, Express sẽ chạy qua lần lượt các middleware đã đăng ký cho route đó.
  - Middleware có thể:
    - Thay đổi `req` và `res`
    - Chặn request nếu điều kiện trong middleware không hợp lệ
    - Chuyển tiếp cho middleware tiếp theo bằng cách gọi `next()`

- Ví dụ về middleware:

  ```js
  const logger = (req, res, next) => {
    console.log(`${req.method} ${req.url}`);
    next();
  };

  app.use(logger); // Sử dụng middleware logger cho tất cả các route
  ```

- `app.use()` là gì?
  - `app.use()` là một phương thức trong Expressjs được sử dụng để đăng ký middleware cho ứng dụng.
  - Middleware có thể được áp dụng cho tất cả các route hoặc chỉ cho một route cụ thể.
  - Ví dụ:

    ```js
    app.use(logger); // Middleware sẽ được áp dụng cho tất cả các route

    app.use("/api", apiMiddleware); // Middleware chỉ áp dụng cho các route bắt đầu bằng /api
    ```

- Các loại middleware trong Expressjs:
  - **Application-level middleware**: Middleware được đăng ký cho toàn bộ ứng dụng. - Đăng ký bằng `app.use()` hoặc `app.METHOD()`.

    ```js
    app.use((req, res, next) => {
      console.log("Middleware cho toàn bộ ứng dụng");
      next();
    });

    app.use("/api", (req, res, next) => {
      console.log("Middleware cho route /api");
      next();
    });
    ```

  - **Router-level middleware**: Middleware được đăng ký cho một router cụ thể. - Dùng trong `express.Router()`.

    ```js
    const router = express.Router();

    router.use((req, res, next) => {
      console.log("Middleware cho router");
      next();
    });

    app.use("/api", router);
    ```

  - **Error-handling middleware**: Middleware được sử dụng để xử lý lỗi.
    - Có 4 tham số: `err`, `req`, `res`, `next`.
    - Được gọi khi có lỗi xảy ra trong middleware hoặc route handler.

    ```js
    app.use((err, req, res, next) => {
      console.error(err.stack);
      res.status(500).send("Something broke!");
    });
    ```

  - **Built-in middleware**: Middleware được cung cấp sẵn bởi Expressjs (ví dụ: `express.json()`, `express.urlencoded()`).
    - `express.json()`: Middleware để phân tích dữ liệu JSON trong body của request.
    - `express.urlencoded()`: Middleware để phân tích dữ liệu URL-encoded trong body của request.

    ```js
    app.use(express.json());
    app.use(express.urlencoded({ extended: true }));
    ```

  - **Third-party middleware**: Middleware được phát triển bởi cộng đồng (ví dụ: `morgan`, `cors`). - Cài thêm từ NPM
    - `morgan`: Middleware để ghi lại thông tin về các request đến ứng dụng.
    - `cors`: Middleware để xử lý CORS (Cross-Origin Resource Sharing).

    ```bash
    npm install morgan cors
    ```

    ```js
    const morgan = require("morgan");
    const cors = require("cors");

    app.use(morgan("dev"));
    app.use(cors());
    ```

## 2. [Error handling](https://expressjs.com/en/guide/error-handling.html)

- Xử lý lỗi trong Expressjs là một phần quan trọng để đảm bảo ứng dụng hoạt động ổn định và cung cấp thông tin hữu ích cho người dùng.
- Vì sao cần xử lý lỗi?
  - Khi có lỗi xảy ra (do code sai, request không hợp lệ, hoặc hệ thống có lỗi), chúng ta cần:
    - Thông báo cho người dùng biết có lỗi xảy ra.
    - Ghi lại thông tin lỗi để phân tích sau này.
    - Đảm bảo ứng dụng không bị treo hoặc dừng lại.

- Middleware xử lý lỗi luôn có 4 tham số: `err`, `req`, `res`, `next`.
- Tạo và sử dụng Error handler middleware trong Expressjs:

  ```js
  // middlewares/error.middleware.js
  const httpStatus = require("http-status-codes");

  const errorHandler = (err, req, res, next) => {
    const statusCode = err.status || httpStatus.INTERNAL_SERVER_ERROR;
    const message = err.message || "Đã xảy ra lỗi không xác định.";
    res.status(statusCode).json({
      statusCode,
      message,
    });
  };

  module.exports = errorHandler;

  // index.js
  const express = require("express");
  const errorHandler = require("./middlewares/error.middleware");

  const app = express();
  app.use(express.json());
  app.use(express.urlencoded({ extended: true }));

  app.use(errorHandler); // Đăng ký middleware xử lý lỗi

  app.listen(3000, () => {
    console.log("Server is running on port 3000");
  });
  ```

- Sử dụng `try-catch` để xử lý lỗi trong route handler:

  ```js
  const getUsers = async (req, res, next) => {
    try {
      const users = await User.find();
      res.status(200).json(users);
    } catch (error) {
      next(error); // Gọi middleware xử lý lỗi
    }
  };
  ```

- Việc sử dụng `next(error)` trong `catch` block sẽ chuyển lỗi đến middleware xử lý lỗi, giúp chúng ta không phải viết lại mã xử lý lỗi trong từng route handler.
- Nhưng nếu sử dụng `async/await` trong route controller và gặp lỗi, sẽ phải dùng `try/catch` và gọi `next(error)` thủ công. Điều này sẽ lặp đi lặp lại nhiều lần, gây rối code.
- Để giải quyết vấn đề này, có thể sử dụng một hàm để xử lý lỗi bất đồng bộ (asynchronous error handling) và tự động gọi `next(error)`.

  ```js
  // utils/catchAsync.js
  const catchAsync = (fn) => (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch((error) => next(error));
  };

  module.exports = catchAsync;
  ```

- Cách hoạt động của `catchAsync`:
  - Nhận vào một hàm `fn` (route handler).
  - Trả về một hàm mới nhận vào `req`, `res`, `next`.
  - Sử dụng `Promise.resolve()` để đảm bảo nếu `fn` trả về một Promise, nó sẽ được xử lý đúng cách.
  - Nếu có lỗi xảy ra, gọi `next(error)` để chuyển đến middleware xử lý lỗi.

- Sử dụng `catchAsync` trong route handler:

  ```js
  const catchAsync = require("./utils/catchAsync");
  const User = require("./models/user.model");

  const getUsers = catchAsync(async (req, res, next) => {
    const users = await User.find();
    res.status(200).json(users);
  });
  ```

- Bằng cách này, chúng ta có thể dễ dàng xử lý lỗi trong các route handler mà không cần phải viết lại mã xử lý lỗi nhiều lần.

- Xây dựng custom error class:

  ```js
  //  utils/ApiError.js
  const httpStatus = require("http-status-codes");

  class ApiError extends Error {
    constructor(statusCode = httpStatus.BAD_REQUEST, message = "") {
      super(message);
      this.statusCode = statusCode;
    }
  }

  module.exports = ApiError;
  ```

- Sử dụng custom error class trong middleware xử lý lỗi:

  ```js
  // middlewares/error.middleware.js
  const httpStatus = require("http-status-codes");

  const errorHandler = (err, req, res, next) => {
    const statusCode = err.statusCode || httpStatus.INTERNAL_SERVER_ERROR;
    const message = err.message || "Đã xảy ra lỗi không xác định.";
    res.status(statusCode).json({
      statusCode,
      message,
    });
  };

  module.exports = errorHandler;
  ```

- Sử dụng custom error class trong route handler:

  ```js
  const httpStatus = require("http-status-codes");

  const User = require("./models/user.model");
  const ApiError = require("./utils/ApiError");
  const catchAsync = require("./utils/catchAsync");

  const getUsers = catchAsync(async (req, res, next) => {
    const users = await User.find();
    if (!users) {
      throw new ApiError(httpStatus.NOT_FOUND, "Không tìm thấy người dùng");
    }
    res.status(httpStatus.OK).json(users);
  });
  ```

- Bằng cách này, chúng ta có thể dễ dàng tạo ra các lỗi tùy chỉnh với mã trạng thái và thông điệp cụ thể, giúp việc xử lý lỗi trở nên rõ ràng và dễ quản lý hơn.

## 3. [Validation](https://joi.dev/)

- Validation là quá trình kiểm tra và xác thực dữ liệu đầu vào để đảm bảo rằng nó đáp ứng các yêu cầu và định dạng mong muốn trước khi được xử lý hoặc lưu trữ.
- Việc xác thực dữ liệu đầu vào là rất quan trọng để đảm bảo rằng ứng dụng hoạt động đúng cách và không gặp phải lỗi do dữ liệu không hợp lệ.
- Validation có thể được thực hiện ở nhiều cấp độ khác nhau:
  - **Client-side validation**: Kiểm tra dữ liệu trên trình duyệt trước khi gửi đến server. - Sử dụng HTML5 validation hoặc JavaScript.
  - **Server-side validation**: Kiểm tra dữ liệu trên server trước khi xử lý hoặc lưu trữ. - Sử dụng các thư viện như Joi, express-validator, ... để thực hiện.
  - **Database-level validation**: Kiểm tra dữ liệu khi lưu trữ vào cơ sở dữ liệu. - Sử dụng các tính năng của cơ sở dữ liệu (như unique constraint, foreign key constraint, ...).

- Validation có thể được thực hiện cho nhiều loại dữ liệu khác nhau:
  - Chuỗi (string)
  - Số (number)
  - Ngày tháng (date)
  - Địa chỉ email (email)
  - Số điện thoại (phone number)
  - Mật khẩu (password)
  - Các trường tùy chỉnh khác

- Joi là một thư viện JavaScript được sử dụng để xác thực và kiểm tra dữ liệu đầu vào. Nó cho phép định nghĩa các quy tắc xác thực cho các kiểu dữ liệu khác nhau và cung cấp thông báo lỗi chi tiết khi dữ liệu không hợp lệ.

- Cài đặt Joi:

  ```bash
  npm install joi
  ```

- Tạo schema validation với Joi:

  ```js
  const Joi = require("joi");

  const createUser = Joi.object({
    fullname: Joi.string().min(3).max(30).required(),
    email: Joi.string().email().required(),
    password: Joi.string().min(6).max(20).required(),
  });
  ```

- Sử dụng trong route:

  ```js
  app.post(
    "/api/users",
    catchAsync(async (req, res, next) => {
      const { error } = createUser.validate(req.body);
      if (error) {
        return next(new ApiError(httpStatus.BAD_REQUEST, error.message));
      }

      const user = await User.create(req.body);
      res.status(httpStatus.CREATED).json(user);
    }),
  );
  ```

- Viết middleware validate giúp tối ưu hóa code:

  ```js
  // middlewares/validate.middleware.js
  const httpStatus = require("http-status");

  const validate = (schema) => (req, res, next) => {
    for (const key in schema) {
      const value = req[key];
      const { error } = schema[key].validate(value, {
        abortEarly: false,
      });

      if (error) {
        const { details } = error;
        const messages = details.map((detail) => detail.message).join(",");

        return res.status(httpStatus.BAD_REQUEST).json({
          message: messages,
          code: httpStatus.BAD_REQUEST,
        });
      }
    }

    next();
  };

  module.exports = validate;
  ```

- Middleware validate sẽ nhận vào một schema và kiểm tra dữ liệu trong `req.body`, `req.query`, `req.params` theo schema đã định nghĩa. Nếu có lỗi, middleware sẽ trả về thông báo lỗi với mã trạng thái 400 (BAD REQUEST). Nếu không có lỗi, middleware sẽ gọi `next()` để tiếp tục xử lý request.
