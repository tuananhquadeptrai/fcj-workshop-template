---
title: "Tổng quan kiến trúc"
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### 1. Kiến trúc tổng thể hệ thống TrustBite
Hệ thống **TrustBite** được xây dựng theo mô hình **Modular Monolith** phía backend để tối ưu hóa hiệu quả triển khai, kết hợp cùng các dịch vụ Cloud Managed Services của AWS để đạt tính mở rộng và bảo mật tốt nhất.

Dưới đây là sơ đồ kiến trúc tổng thể các thành phần của TrustBite:

<img src="/images/5-Workshop/5.1-Workshop-overview/trustbite-aws-architecture.png" alt="Sơ đồ kiến trúc triển khai TrustBite trên AWS" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

---

### 2. Thành phần và vai trò trong hệ thống
*   **Mobile App (Flutter + Dart):** Ứng dụng client chính của người dùng cuối. Hỗ trợ đăng nhập qua Cognito, duyệt tìm quán ăn theo vị trí/bản đồ, viết review và tải hóa đơn lên để kiểm định.
*   **Admin Portal (Next.js):** Cổng quản trị dành cho kiểm duyệt viên để rà soát hàng đợi hóa đơn nghi vấn, duyệt claim nhà hàng, xem audit log hệ thống.
*   **Express API Node.js (Backend Monolith):** Cung cấp toàn bộ REST API nghiệp vụ. Được cấu hình xử lý native ES modules gọn nhẹ.
*   **Redis & BullMQ:** Hàng đợi tin nhắn. Chuyển các tác vụ tốn thời gian xử lý như OCR hóa đơn, phân tích chống gian lận sang worker chạy ngầm độc lập.
*   **PostgreSQL + PostGIS:** Cơ sở dữ liệu quan hệ chính. Sử dụng extension **PostGIS** để lưu trữ và truy vấn khoảng cách không gian (Spatial Queries).
*   **AWS Managed Services:**
    *   **Amazon Cognito:** Đảm nhận toàn bộ quy trình đăng ký, đăng nhập và xác thực token.
    *   **Amazon S3:** Lưu trữ ảnh hóa đơn riêng tư của người dùng.
    *   **AWS Textract:** Trích xuất văn bản hóa đơn tự động.

---

### 3. Kiến trúc xác thực người dùng (Amazon Cognito Integration)
Hành trình xác thực người dùng trong workshop được thực hiện qua luồng OIDC tiêu chuẩn:

```text
Người dùng ──> Mobile/Web Client ──> Cognito Managed Login (Authorization Code + PKCE)
                                                │
Backend API <── (Gửi Bearer Access Token) ── Client nhận Tokens (Access, ID, Refresh)
     │
     └──> [Xác minh JWT bằng Cognito JWKS] ──> Truy cập API được bảo vệ
```

#### Phân biệt Xác thực (Authentication) & Phân quyền (Authorization):
*   **Authentication (Ai đang truy cập?):** Do Amazon Cognito đảm nhận. Cognito xác minh thông tin đăng nhập và phát hành JWT (JSON Web Token).
*   **Authorization (Được phép làm gì?):** Do Express Backend của TrustBite xử lý. Sau khi giải mã và xác minh chữ ký của JWT bằng bộ khóa công khai JWKS của Cognito, backend sẽ đọc thông tin vai trò (**USER**, **MERCHANT**, **ADMIN**) và kiểm tra quyền tương ứng trước khi xử lý dữ liệu.

> [!WARNING]
> Tuyệt đối không dùng ID token để bảo vệ REST API. Hệ thống backend TrustBite chỉ chấp nhận các access token hợp lệ có thuộc tính **token_use** bằng **access**.
