---
title: "Tích hợp Cognito vào ứng dụng"
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Mục tiêu

Trong phần này, chúng ta tích hợp User Pool với backend Express và ứng dụng client của TrustBite. Backend xác minh access token bằng public key của Cognito; client thực hiện Authorization Code + PKCE và lưu token trong secure storage.

#### Nội dung

1. [Cấu hình backend](5.4.1-backend-config/)
2. [Xây dựng JWT middleware](5.4.2-jwt-middleware/)
3. [Tích hợp đăng nhập trên client](5.4.3-client-login/)
4. [Kiểm tra luồng xác thực](5.4.4-test-authentication/)

#### Luồng request được bảo vệ

```text
Client → Authorization: Bearer <access_token>
       → Express authentication middleware
       → Verify signature + exp + iss + client_id + token_use
       → Check user/account permissions
       → Controller → Service → Database
```
