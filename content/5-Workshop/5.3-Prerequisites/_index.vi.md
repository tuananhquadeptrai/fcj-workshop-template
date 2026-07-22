---
title: "Chuẩn bị môi trường"
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Tài khoản và công cụ

- Tài khoản AWS có quyền tạo và quản lý Cognito User Pool.
- Node.js 20 trở lên và npm.
- Backend Express sử dụng Native ES Modules.
- Git, trình soạn thảo mã nguồn và công cụ gọi API như Postman hoặc `curl`.
- Một địa chỉ email có thể nhận mã xác nhận.

#### Giá trị sử dụng trong workshop

| Cấu hình | Giá trị mẫu |
| --- | --- |
| Region | `ap-southeast-1` |
| User Pool name | `trustbite-users-dev` |
| App Client name | `trustbite-mobile-dev` |
| Callback URL | `http://localhost:3000/auth/callback` |
| Sign-out URL | `http://localhost:3000/logout` |
| Protected API | `http://localhost:3001/api/v1/users/me` |

#### Chuẩn bị backend

Trong thư mục backend, cài đặt Express, biến môi trường và thư viện xác minh JWT do AWS khuyến nghị cho Node.js:

```bash
npm install express dotenv aws-jwt-verify
```

Tạo file `.env.example` để mô tả các biến cần thiết nhưng không chứa thông tin thật:

```dotenv
PORT=3001
COGNITO_REGION=ap-southeast-1
COGNITO_USER_POOL_ID=ap-southeast-1_xxxxxxxxx
COGNITO_APP_CLIENT_ID=xxxxxxxxxxxxxxxxxxxxxxxxxx
```

{{% notice warning %}}
Không commit `.env`, password, authorization code, access token, ID token hoặc refresh token lên Git.
{{% /notice %}}

#### Quyền IAM

Người thực hiện workshop cần quyền quản lý Cognito trong môi trường lab. Với production, hãy tạo policy giới hạn đúng User Pool và hành động cần thiết theo nguyên tắc least privilege.
