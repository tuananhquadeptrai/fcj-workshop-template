---
title: "Tích hợp đăng nhập trên client"
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

#### Luồng đăng nhập đề xuất

Mobile và SPA là public client nên sử dụng **Authorization Code + PKCE**, không dùng client secret và không nhúng AWS credentials vào ứng dụng.

1. Client sinh `code_verifier` ngẫu nhiên.
2. Client tạo `code_challenge = BASE64URL(SHA256(code_verifier))`.
3. Mở Cognito Managed Login tại authorization endpoint.
4. Người dùng đăng ký, xác nhận email hoặc đăng nhập.
5. Cognito redirect về callback URL cùng authorization code.
6. Client gửi code và `code_verifier` đến token endpoint.
7. Cognito trả về ID, access và refresh token.
8. Client lưu token trong secure storage và gửi access token đến API.

#### Ví dụ Flutter

Cài thư viện AppAuth và secure storage phù hợp với project:

```bash
flutter pub add flutter_appauth flutter_secure_storage
```

Ví dụ thực hiện đăng nhập:

```dart
final appAuth = FlutterAppAuth();

final result = await appAuth.authorizeAndExchangeCode(
  AuthorizationTokenRequest(
    appClientId,
    'com.trustbite.app://auth/callback',
    discoveryUrl:
      'https://cognito-idp.ap-southeast-1.amazonaws.com/'
      '$userPoolId/.well-known/openid-configuration',
    scopes: ['openid', 'email', 'profile'],
  ),
);

await secureStorage.write(
  key: 'access_token',
  value: result?.accessToken,
);
```

AppAuth tự tạo và xử lý PKCE. Callback URI `com.trustbite.app://auth/callback` phải được đăng ký trong Cognito App Client và cấu hình deep link trên Android/iOS.

#### Gọi protected API

```dart
final token = await secureStorage.read(key: 'access_token');

final response = await http.get(
  Uri.parse('$apiBaseUrl/api/v1/users/me'),
  headers: {'Authorization': 'Bearer $token'},
);
```

#### Đăng xuất

- Xóa token khỏi secure storage.
- Gọi logout endpoint của Cognito Managed Login với `client_id` và `logout_uri`.
- Khi cần vô hiệu hóa session ngay lập tức, revoke refresh token hoặc thực hiện global sign-out.

{{% notice warning %}}
Không lưu access token hoặc ID token trong `localStorage` của trình duyệt. Trên mobile, sử dụng Keychain/Keystore thông qua secure storage.
{{% /notice %}}
