---
title: "Bảo mật và best practices"
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

#### Checklist bảo mật

| Hạng mục | Khuyến nghị |
| --- | --- |
| App Client | Mobile/SPA không dùng client secret; dùng Authorization Code + PKCE |
| Token API | Chỉ chấp nhận access token và kiểm tra `token_use=access` |
| JWT | Xác minh chữ ký, `exp`, `iss`, `client_id` và scope/group cần thiết |
| Lưu token | Mobile dùng Keychain/Keystore; không lưu ID/access token trong browser localStorage |
| Password | Dùng policy mạnh; ưu tiên passkey hoặc SRP nếu tự xây dựng form password |
| MFA | Bật TOTP hoặc MFA phù hợp với rủi ro của ứng dụng |
| Refresh token | Revoke khi đăng xuất hoặc khi phát hiện session đáng ngờ |
| Public sign-up | Chỉ bật khi ứng dụng cho phép người dùng Internet tự đăng ký |
| Quyền quản trị | IAM least privilege; không đặt AWS credentials trong client |
| Chống lạm dụng | Rate limit, threat protection và AWS WAF khi cần |
| Dữ liệu người dùng | Chỉ cho App Client đọc/ghi các attribute thực sự cần thiết |
| Logging | Không ghi password, authorization code hoặc raw token vào log |

#### Phân quyền với group

Có thể tạo các Cognito group như `users`, `merchants`, `admins`. Claim `cognito:groups` xuất hiện trong token và được middleware đọc, nhưng backend vẫn phải đối chiếu quyền nghiệp vụ trong PostgreSQL.

Ví dụ middleware kiểm tra admin:

```javascript
export function requireAdmin(req, res, next) {
  if (!req.auth?.groups.includes("admins")) {
    return res.status(403).json({
      error: "FORBIDDEN",
      message: "Administrator access is required.",
    });
  }

  return next();
}
```

#### Các test bảo mật cần có

- Token hợp lệ, token hết hạn và token bị cắt xén.
- Sai chữ ký, issuer, User Pool và App Client ID.
- Dùng ID token tại endpoint yêu cầu access token.
- Người dùng hợp lệ, người dùng `SUSPENDED` và người dùng đã soft delete.
- Scope hoặc group không đủ quyền.
- Header Bearer sai định dạng.

#### Tài liệu AWS chính thức

- [Amazon Cognito User Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools.html)
- [Tạo ứng dụng trong Cognito Console](https://docs.aws.amazon.com/cognito/latest/developerguide/getting-started-user-pools-application.html)
- [Xác minh JSON Web Token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-verifying-a-jwt.html)
- [Security best practices cho User Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-security-best-practices.html)
- [`aws-jwt-verify`](https://github.com/awslabs/aws-jwt-verify)

{{% notice warning %}}
Cognito xử lý authentication nhưng ứng dụng vẫn chịu trách nhiệm về authorization, trạng thái tài khoản, bảo vệ token, validation input và kiểm soát truy cập dữ liệu.
{{% /notice %}}
