# Bài tập về nhà Tuần 5

## 1.tạo một Model todo với các thông tin gợi ý sau

```js
{
  _id: ObjectId,
  title: String,
  description: String,
  status: String, // "pending" | "completed"
  dueDate: Date,
  userId: ObjectId, // ref User
  createdAt: Date,
  updatedAt: Date
}
```

## 2. Xây dựng các API

- getTodos: Lấy danh sách tất cả các todo
- getTodoById: Lấy thông tin chi tiết của một todo theo ID
- createTodo: Tạo mới một todo
- updateTodo: Cập nhật thông tin của một todo
- deleteTodo: Xóa một todo
