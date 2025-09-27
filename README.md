# API Xác thực với Passport.js (Local Strategy)

Đây là một dự án backend mẫu, trình bày cách xây dựng một hệ thống xác thực người dùng hoàn chỉnh bằng **Passport.js** với chiến lược xác thực cục bộ (`local strategy`). Dự án sử dụng Node.js, Express, và MongoDB.

Luồng hoạt động dựa trên session được quản lý bởi Passport. Khi đăng nhập thành công, Passport sẽ tạo một session, lưu thông tin người dùng vào đó và gửi một cookie về cho client để duy trì trạng thái đăng nhập.

## Các công nghệ và tính năng chính

-   **Framework:** Express.js
-   **Cơ sở dữ liệu:** MongoDB với Mongoose ODM.
-   **Xác thực:** Sử dụng **Passport.js** và **passport-local** cho chiến lược username/password.
-   **Quản lý Session:** Tích hợp `express-session` để Passport quản lý trạng thái đăng nhập.
-   **Bảo mật:**
    -   Mật khẩu được băm (hash) an toàn bằng `bcryptjs`.
    -   Passport tự động xử lý việc tạo và xác minh session một cách an toàn.
-   **API:** Cung cấp các endpoint cho Đăng ký, Đăng nhập, Đăng xuất và truy cập tài nguyên được bảo vệ.

## Yêu cầu

-   [Node.js](https://nodejs.org/) (phiên bản 16.x trở lên)
-   npm (đi kèm với Node.js)
-   [MongoDB](https://www.mongodb.com/try/download/community) phải được cài đặt và đang chạy trên máy của bạn.

## Cài đặt & Khởi chạy

1.  **Thiết lập cấu trúc dự án:**
    Tạo một thư mục dự án và đặt các file `app.js`, `models/User.js`, `routes/auth.js`, và `config/passport.js` vào đúng cấu trúc thư mục của chúng.

2.  **Mở Terminal hoặc Command Prompt:**
    Di chuyển vào thư mục gốc của dự án.

3.  **Cài đặt các dependency:**
    Chạy lệnh sau để cài đặt tất cả các thư viện cần thiết:
    ```bash
    npm install express mongoose express-session passport passport-local bcryptjs
    ```

4.  **Chắc chắn rằng MongoDB đang chạy:**
    Hãy đảm bảo dịch vụ MongoDB của bạn đã được khởi động.

5.  **Khởi động máy chủ:**
    ```bash
    node app.js
    ```
    Bạn sẽ thấy thông báo: `MongoDB connected` và `Server running on http://localhost:3000`.

## Mô tả các API Endpoints

Tất cả các endpoint đều có tiền tố là `/auth`.

| Phương thức | Endpoint | Mô tả | Yêu cầu Body (JSON) |
| :--- | :--- | :--- | :--- |
| **POST** | `/auth/register` | Đăng ký một tài khoản người dùng mới. | `{ "username": "...", "password": "..." }` |
| **POST** | `/auth/login` | Đăng nhập người dùng bằng `passport-local` strategy. | `{ "username": "...", "password": "..." }` |
| **GET** | `/auth/profile` | Truy cập thông tin người dùng (được bảo vệ). | (Không có) |
| **GET** | `/auth/logout` | Đăng xuất người dùng, hủy session. | (Không có) |

---

## Hướng dẫn Test bằng Postman

### 1. Đăng ký tài khoản (`/auth/register`)

-   **Method:** `POST`
-   **URL:** `http://localhost:3000/auth/register`
-   **Body:** Chọn `raw` và `JSON`. Nhập:
    ```json
    {
        "username": "passportuser",
        "password": "password123"
    }
    ```
-   **Kết quả:** `{ "message": "User registered successfully" }`.

### 2. Đăng nhập (`/auth/login`)

-   **Method:** `POST`
-   **URL:** `http://localhost:3000/auth/login`
-   **Body:** Sử dụng tài khoản bạn vừa tạo:
    ```json
    {
        "username": "passportuser",
        "password": "password123"
    }
    ```
-   **Kết quả:**
    -   Bạn sẽ nhận được thông báo `{ "message": "Logged in successfully", "user": { ... } }`.
    -   Kiểm tra tab **Cookies** trong Postman, bạn sẽ thấy một cookie mới tên là `connect.sid`. Postman sẽ tự động quản lý cookie này cho bạn.

### 3. Truy cập route được bảo vệ (`/auth/profile`)

-   **Method:** `GET`
-   **URL:** `http://localhost:3000/auth/profile`
-   **Kết quả:**
    -   Do Postman tự động gửi kèm cookie `connect.sid`, request sẽ được xác thực thành công qua hàm `req.isAuthenticated()`.
    -   Bạn sẽ nhận được thông tin của người dùng: `{ "message": "Profile data", "user": { ... } }`.

### 4. Đăng xuất (`/auth/logout`)

-   **Method:** `GET`
-   **URL:** `http://localhost:3000/auth/logout`
-   **Kết quả:**
    -   Bạn sẽ nhận được thông báo `{ "message": "Logged out" }`.
    -   Cookie `connect.sid` sẽ bị xóa. Nếu bạn gọi lại API `/auth/profile`, bạn sẽ nhận được lỗi `401 Unauthorized`.
