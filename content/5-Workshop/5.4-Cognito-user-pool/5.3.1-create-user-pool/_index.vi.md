---
title: "Tạo User Pool"
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### Bước 1: Mở Amazon Cognito

1. Đăng nhập AWS Management Console.
2. Chuyển Region sang **Asia Pacific (Singapore) – ap-southeast-1**.
3. Tìm **Amazon Cognito** và chọn **User pools**.
4. Chọn **Create user pool**.

#### Bước 2: Định nghĩa ứng dụng

1. Chọn application type phù hợp với mobile hoặc Single Page Application.
2. Đặt tên ứng dụng `trustbite-mobile-dev`.
3. Chọn **Email** làm sign-in identifier.
4. Không tạo client secret cho mobile/SPA vì public client không thể bảo vệ secret.

<img src="/images/5-Workshop/5.4-Cognito-user-pool/03-cognito-sign-in.png" alt="Cấu hình đăng nhập bằng email và mật khẩu của Cognito User Pool" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Điểm kiểm tra: User Pool cho phép đăng nhập bằng email và mật khẩu; cấu hình lab hiện tại đang để No MFA.</em></p>

#### Bước 3: Cấu hình đăng ký và xác nhận

1. Bật self-service sign-up vì TrustBite cho phép người dùng tự đăng ký.
2. Chọn email là thuộc tính bắt buộc.
3. Bật Cognito-assisted verification để Cognito gửi mã xác nhận email.
4. Giữ chính sách password đủ mạnh: tối thiểu 8 ký tự, có chữ hoa, chữ thường, số và ký tự đặc biệt.
5. Cấu hình lab minh họa đang để **No MFA**; production nên ưu tiên TOTP, passkey hoặc chính sách MFA phù hợp với yêu cầu bảo mật.

<img src="/images/5-Workshop/5.4-Cognito-user-pool/04-cognito-sign-up.png" alt="Cấu hình tự đăng ký và xác minh địa chỉ email trong Cognito" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Điểm kiểm tra: Cognito tự gửi thông báo xác minh email và self-service sign-up đang được bật.</em></p>

#### Bước 4: Tạo tài nguyên

1. Kiểm tra lại cấu hình.
2. Chọn **Create application** hoặc **Create user pool** tùy giao diện Console.
3. Mở phần overview của User Pool vừa tạo.
4. Ghi lại **User Pool ID**, ví dụ `ap-southeast-1_AbCdEf123`.

<img src="/images/5-Workshop/5.4-Cognito-user-pool/01-cognito-user-pool-overview.png" alt="Trang tổng quan Cognito User Pool trong Region Asia Pacific Singapore" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>User Pool đã được tạo tại Region Asia Pacific (Singapore).</em></p>

#### Bước 5: Kiểm tra cấu hình

Trong User Pool, xác nhận:

- Sign-in identifier là email.
- Self-service sign-up đang bật.
- Email verification đang bật.
- App Client đã được tạo.
- Password policy và trạng thái MFA đúng với môi trường lab (ảnh minh họa hiện là **No MFA**).

<img src="/images/5-Workshop/5.4-Cognito-user-pool/02-cognito-app-clients.png" alt="Danh sách App Client trustbite-mobile và trustbite-admin-web trong Cognito" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Điểm kiểm tra: User Pool có hai App Client dành cho ứng dụng mobile và cổng quản trị web.</em></p>

{{% notice warning %}}
Chỉ bật public self-sign-up khi ứng dụng thực sự cho phép bất kỳ người dùng Internet nào đăng ký. Với ứng dụng nội bộ, hãy tắt self-sign-up và tạo người dùng bằng quy trình quản trị.
{{% /notice %}}
