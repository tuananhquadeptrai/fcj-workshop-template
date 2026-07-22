---
title: "Kiểm tra luồng xác thực"
weight: 4
chapter: false
pre: " <b> 5.5.4. </b> "
---

#### Bước 1: Đăng ký tài khoản

1. Mở Managed Login.
2. Chọn đăng ký và nhập email, password hợp lệ.
3. Kiểm tra hộp thư và nhập mã xác nhận.
4. Trong Cognito Console, mở **Users** và xác nhận trạng thái người dùng là `CONFIRMED`.

<img src="/images/5-Workshop/5.4-Cognito-user-pool/09-cognito-confirmed-users.png" alt="Danh sách người dùng Cognito đã xác minh email và có trạng thái Confirmed, Enabled" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Điểm kiểm tra: các tài khoản thử nghiệm đã xác minh email, có trạng thái Confirmed và đang Enabled.</em></p>

#### Bước 2: Đăng nhập và lấy access token

Đăng nhập qua client hoặc Managed Login. Sau khi Authorization Code + PKCE hoàn tất, lấy access token từ secure storage hoặc công cụ debug an toàn.

Không đưa token thật vào tài liệu hoặc ảnh chụp màn hình.

#### Bước 3: Gọi API không có token

```bash
curl -i http://localhost:3001/api/v1/users/me
```

Kết quả mong đợi:

```http
HTTP/1.1 401 Unauthorized
```

#### Bước 4: Gọi API với access token hợp lệ

```bash
curl -i \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  http://localhost:3001/api/v1/users/me
```

Kết quả mong đợi:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{"id":"<cognito-sub>","username":"<user>","groups":[]}
```

#### Bước 5: Kiểm tra các trường hợp lỗi

| Trường hợp | Kết quả mong đợi |
| --- | --- |
| Không có header Authorization | `401 UNAUTHORIZED` |
| Dùng ID token thay access token | `401 INVALID_ACCESS_TOKEN` |
| Access token hết hạn | `401 INVALID_ACCESS_TOKEN` |
| Token bị sửa một ký tự | `401 INVALID_ACCESS_TOKEN` |
| Token từ User Pool khác | `401 INVALID_ACCESS_TOKEN` |
| Token có App Client ID khác | `401 INVALID_ACCESS_TOKEN` |
| JWT hợp lệ nhưng user bị `SUSPENDED` | `403` theo authorization nghiệp vụ |

#### Bước 6: Viết integration test

Mock verifier hoặc sử dụng trusted-local identity chỉ trong môi trường test. Bộ test cần xác nhận middleware từ chối token thiếu, token sai và cho phép payload đã xác minh hợp lệ.

{{% notice note %}}
HTTP `401` dùng khi chưa xác thực hoặc token không hợp lệ. HTTP `403` dùng khi đã xác thực nhưng không có quyền thực hiện hành động.
{{% /notice %}}
