---
title: "Cấu hình App Client"
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Bước 1: Kiểm tra App Client

1. Trong User Pool, chọn **App clients**.
2. Mở `trustbite-mobile`.
3. Xác nhận App Client không có client secret.
4. Ghi lại **Client ID**.

<img src="/images/5-Workshop/5.4-Cognito-user-pool/05-cognito-mobile-client-details.png" alt="Chi tiết App Client trustbite-mobile không có client secret" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Điểm kiểm tra: App Client <code>trustbite-mobile</code> là public client và trường Client secret hiển thị dấu “-”.</em></p>

#### Bước 2: Tạo Cognito domain

1. Mở menu **Domain** hoặc **Managed login**.
2. Chọn Cognito domain prefix duy nhất, ví dụ `trustbite-dev-<mã-của-bạn>`.
3. Lưu domain đầy đủ:

```text
https://trustbite-dev-<mã-của-bạn>.auth.ap-southeast-1.amazoncognito.com
```

<img src="/images/5-Workshop/5.4-Cognito-user-pool/06-cognito-domain.png" alt="Cognito domain dành cho Managed Login trong Region Singapore" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Cognito domain đã được gán cho Managed Login của User Pool.</em></p>

#### Bước 3: Cấu hình OAuth 2.0

Trong App Client, cấu hình:

| Thuộc tính | Giá trị |
| --- | --- |
| Allowed callback URL | `http://localhost:3000/auth/callback`, `trustbite://auth/callback` |
| Allowed sign-out URL | `http://localhost:3000` |
| OAuth grant | Authorization code grant |
| OpenID Connect scopes | `openid`, `email`, `phone`, `profile` |
| Identity provider | Cognito user pool |

<img src="/images/5-Workshop/5.4-Cognito-user-pool/07-cognito-oauth-settings.png" alt="Cấu hình callback URL, sign-out URL, Authorization Code Grant và OpenID Connect scopes của Cognito" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Điểm kiểm tra: Managed Login đang hoạt động với callback cho web và mobile, Authorization Code Grant và các scope OIDC đã chọn.</em></p>

Managed Login dùng Authorization Code. Ứng dụng phải sinh `code_verifier` và `code_challenge` mới cho mỗi request PKCE.

#### Bước 4: Tạo URL đăng nhập thử nghiệm

```text
https://<domain>/oauth2/authorize
  ?response_type=code
  &client_id=<app_client_id>
  &redirect_uri=http%3A%2F%2Flocalhost%3A3000%2Fauth%2Fcallback
  &scope=openid+email+profile
  &code_challenge=<base64url_sha256_of_verifier>
  &code_challenge_method=S256
```

Sau khi đăng nhập thành công, Cognito chuyển trình duyệt về callback URL cùng tham số `code`. Client đổi code và `code_verifier` tại `/oauth2/token` để nhận token.

#### Bước 5: Lưu cấu hình

Cập nhật `.env` của backend:

```dotenv
COGNITO_REGION=ap-southeast-1
COGNITO_USER_POOL_ID=<user_pool_id>
COGNITO_APP_CLIENT_ID=<app_client_id>
```

{{% notice warning %}}
Callback URL phải khớp chính xác với URL đã đăng ký, bao gồm protocol, port, path và dấu `/` cuối nếu có.
{{% /notice %}}
