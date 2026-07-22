---
title: "Dọn dẹp tài nguyên"
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

#### Tổng kết workshop

Bạn đã hoàn thành luồng xác thực TrustBite bằng Amazon Cognito:

- Tạo User Pool, App Client và Cognito domain.
- Bật đăng ký và xác nhận email.
- Sử dụng Authorization Code + PKCE cho mobile/web.
- Xác minh access token tại Express backend.
- Bảo vệ API và kiểm tra các trường hợp token không hợp lệ.
- Áp dụng các nguyên tắc bảo mật token và authorization.

#### Dọn dẹp User Pool

Nếu đây chỉ là môi trường lab và không còn sử dụng:

1. Mở Amazon Cognito Console tại đúng Region.
2. Chọn User Pool `trustbite-users-dev`.
3. Kiểm tra không có ứng dụng hoặc người dùng thật phụ thuộc vào User Pool.
4. Chọn **Delete user pool**.
5. Nhập tên User Pool để xác nhận xóa.

Xóa User Pool sẽ xóa người dùng, App Client, domain và cấu hình liên quan. Thao tác này không thể hoàn tác.

#### Dọn dẹp ứng dụng local

- Xóa access, ID và refresh token khỏi secure storage.
- Xóa các giá trị thật khỏi `.env`; giữ `.env.example` nếu cần tài liệu cấu hình.
- Dừng backend và các dịch vụ local không còn sử dụng.
- Xóa callback URL thử nghiệm khỏi cấu hình production.
- Kiểm tra Git history để chắc chắn không có token hoặc secret bị commit.

#### Kiểm tra cuối

- Truy cập Cognito domain cũ phải không còn hoạt động sau khi tài nguyên bị xóa hoàn toàn.
- Backend không khởi động nếu thiếu biến môi trường bắt buộc.
- Token đã phát hành từ User Pool bị xóa không còn được backend tin cậy.

{{% notice warning %}}
Không xóa User Pool production hoặc tài nguyên đang được ứng dụng thật sử dụng. Luôn sao lưu dữ liệu cần thiết và xác nhận dependency trước khi xóa.
{{% /notice %}}
