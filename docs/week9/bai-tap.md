# Buổi 9: Upload file (Multer & Cloudinary)

## 1. Giới thiệu Multer

### 1.1 Multer là gì?

- Multer là một middleware cho Express.js, giúp xử lý `multipart/form-data`, định dạng dữ liệu thường được sử dụng để upload file.
- Multer cho phép bạn dễ dàng nhận và xử lý file upload từ client, lưu trữ chúng vào hệ thống tệp hoặc cơ sở dữ liệu (thông qua dịch vụ lưu trữ bên thứ 3).
- Multer hỗ trợ nhiều tùy chọn cấu hình, cho phép bạn kiểm soát cách thức lưu trữ và xử lý file upload.

### 1.2 Khi nào nên sử dụng Multer?

- Khi bạn cần upload file từ client đến server trong ứng dụng web.
- Khi bạn muốn xử lý file upload một cách dễ dàng và hiệu quả trong ứng dụng Express.js.
- Khi bạn cần kiểm soát các thuộc tính của file upload như kích thước, loại file, tên file, v.v.

## 2. Cài đặt các package cần thiết

Bạn cần cài đặt `multer` để xử lý file. Nếu muốn lưu trữ lên Cloudinary, bạn sẽ cần cài đặt thêm `cloudinary` và `dotenv` (để đọc biến môi trường).

```bash
npm install multer
npm install cloudinary dotenv
```

_(Lưu ý: Để sử dụng cú pháp ES6 (`import`/`export`), hãy chắc chắn rằng bạn đã thêm `"type": "module"` vào file `package.json` của project)._

---

## 3. Upload file cơ bản (Lưu ở Local)

### 3.1. Tạo middleware Multer lưu file tại thư mục local

Tạo file `middlewares/upload.local.js`:

```javascript
import path from "path";
import multer from "multer";

// Giới hạn kích thước file upload (5MB)
const limitSize = 5 * 1024 * 1024;

const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    // Thư mục lưu trữ file upload (Lưu ý: Cần tạo sẵn thư mục 'uploads' ở root)
    cb(null, "uploads/");
  },
  filename: function (req, file, cb) {
    // Tạo tên file duy nhất để tránh bị ghi đè
    const uniqueSuffix = Date.now() + "-" + Math.round(Math.random() * 1e9);
    const ext = path.extname(file.originalname);
    cb(null, file.fieldname + "-" + uniqueSuffix + ext);
  },
});

const fileFilter = (req, file, cb) => {
  const allowedTypes = ["image/jpeg", "image/png", "image/gif", "image/webp"];
  if (!allowedTypes.includes(file.mimetype)) {
    const error = new Error("Chỉ cho phép upload file hình ảnh");
    error.status = 400;
    return cb(error, false);
  }
  cb(null, true);
};

export const uploadLocal = multer({
  storage,
  fileFilter,
  limits: {
    fileSize: limitSize,
  },
});
```

### 3.2. Sử dụng middleware trong Route

Trong file route của bạn (ví dụ: `routes/upload.route.js`):

```javascript
import express from "express";
import { uploadLocal } from "../middlewares/upload.local.js";

const router = express.Router();

router.post("/uploads", uploadLocal.single("file"), (req, res) => {
  if (!req.file) {
    return res.status(400).json({
      statusCode: 400,
      message: "Không có file nào được upload",
    });
  }

  res.status(200).json({
    statusCode: 200,
    message: "Upload file local thành công",
    data: {
      // req.file chứa thông tin file đã upload, bao gồm đường dẫn file (path) trong thư mục uploads
      file: req.file,
    },
  });
});

export default router;
```

---

## 4. Upload lên Cloudinary

Để upload thẳng file từ máy khách (client) lên Cloudinary mà không tốn công ghi file vào ổ cứng server, ta sẽ sử dụng `multer` với chế độ **Memory Storage** (lưu tạm vào RAM). Kế tiếp, ta đẩy trực tiếp file từ RAM (dạng buffer) thẳng vào luồng `upload_stream` của Cloudinary bằng phương thức `stream.end()`.

**Sơ đồ luồng hoạt động:**

```text
Client
  ↓
multer (RAM - buffer)
  ↓
Cloudinary (stream.end)
```

### 4.1. Cấu hình Cloudinary

Đầu tiên, bạn cần đăng ký tài khoản miễn phí trên [Cloudinary](https://cloudinary.com/) và lấy thông tin cấu hình ở mục Dashboard: `Cloud Name`, `API Key`, `API Secret`.

Thêm các biến môi trường này vào file `.env`:

```env
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
```

Tạo file cấu hình `configs/cloudinary.config.js`:

```javascript
import { v2 as cloudinary } from "cloudinary";
import dotenv from "dotenv";

dotenv.config();

cloudinary.config({
  cloud_name: process.env.CLOUDINARY_CLOUD_NAME,
  api_key: process.env.CLOUDINARY_API_KEY,
  api_secret: process.env.CLOUDINARY_API_SECRET,
});

export default cloudinary;
```

### 4.2. Tạo middleware Multer Memory

Cấu hình Multer để giữ file trên RAM (dưới dạng Buffer) thay vì lưu vào ổ cứng local.

Tạo file `middlewares/upload.memory.js`:

```javascript
import multer from "multer";

const storage = multer.memoryStorage();

export const uploadMemory = multer({
  storage,
  limits: {
    fileSize: 5 * 1024 * 1024, // Giới hạn 5MB
  },
});
```

### 4.3. Tạo middleware đẩy ảnh lên Cloudinary

Sử dụng `stream.end()` để đẩy Buffer nhận được từ bước trên thẳng lên Cloudinary.

Tạo file `middlewares/uploadCloud.js`:

```javascript
import cloudinary from "../configs/cloudinary.config.js";

const uploadImage = async (req, res, next) => {
  try {
    // Nếu không có file được tải lên thì bỏ qua, đi đến controller tiếp theo
    if (!req.file) return next();

    // Viết hàm trả về Promise bọc lấy luồng upload của Cloudinary
    const streamUpload = () => {
      return new Promise((resolve, reject) => {
        const stream = cloudinary.uploader.upload_stream(
          { folder: "uploads" }, // Tên thư mục lưu trên Cloudinary
          (error, result) => {
            if (error) reject(error);
            else resolve(result);
          }
        );

        // Bơm trực tiếp buffer vào stream của Cloudinary và kết thúc quá trình truyền
        stream.end(req.file.buffer);
      });
    };

    // Chờ quá trình upload hoàn tất
    const result = await streamUpload();

    // Gắn URL an toàn của ảnh vừa up vào req.body để lưu vào Database sau đó
    req.body.image = result.secure_url;

    next();
  } catch (err) {
    next(err);
  }
};

export default uploadImage;
```

### 4.4. Sử dụng trong Route

Kết hợp `uploadMemory` (chụp file vào RAM) và `uploadImage` (middleware đẩy RAM lên Cloud) vào trong Route để tạo thành một luồng hoàn chỉnh.

Cập nhật lại file `routes/upload.route.js`:

```javascript
import express from "express";
import { uploadMemory } from "../middlewares/upload.memory.js";
import uploadImage from "../middlewares/uploadCloud.js";

const router = express.Router();

// Sử dụng uploadMemory.single("file") để lấy file vào req.file.buffer
// Sau đó uploadImage sẽ đẩy buffer đó lên Cloudinary và lấy link gắn ở req.body.image
router.post("/cloud", uploadMemory.single("file"), uploadImage, (req, res) => {
  // Nếu middleware uploadImage không đính kèm được url ảnh (do lỗi hoặc không có file)
  if (!req.body.image) {
    return res.status(400).json({
      statusCode: 400,
      message: "Không có file nào được upload",
    });
  }

  res.status(200).json({
    statusCode: 200,
    message: "Upload file thẳng lên Cloudinary thành công",
    data: {
      imageUrl: req.body.image, // Lấy link ảnh từ req.body
    },
  });
});

export default router;
```
