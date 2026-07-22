---
title: "Integrate client sign-in"
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

#### Recommended sign-in flow

Mobile applications and SPAs are public clients. Use **Authorization Code + PKCE**, do not use a client secret, and never embed AWS credentials in the application.

1. The client generates a random `code_verifier`.
2. It creates `code_challenge = BASE64URL(SHA256(code_verifier))`.
3. It opens Cognito Managed Login at the authorization endpoint.
4. The user registers, confirms email, or signs in.
5. Cognito redirects to the callback URL with an authorization code.
6. The client sends the code and `code_verifier` to the token endpoint.
7. Cognito returns ID, access, and refresh tokens.
8. The client stores tokens securely and sends the access token to the API.

#### Flutter example

Add AppAuth and secure storage packages appropriate for the project:

```bash
flutter pub add flutter_appauth flutter_secure_storage
```

Example sign-in:

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

AppAuth generates and handles PKCE. Register `com.trustbite.app://auth/callback` in the Cognito App Client and configure it as an Android/iOS deep link.

#### Call the protected API

```dart
final token = await secureStorage.read(key: 'access_token');

final response = await http.get(
  Uri.parse('$apiBaseUrl/api/v1/users/me'),
  headers: {'Authorization': 'Bearer $token'},
);
```

#### Sign out

- Delete tokens from secure storage.
- Call the Cognito Managed Login logout endpoint with `client_id` and `logout_uri`.
- Revoke the refresh token or perform global sign-out when the session must be invalidated immediately.

{{% notice warning %}}
Do not store access or ID tokens in browser `localStorage`. On mobile, use Keychain/Keystore through secure storage.
{{% /notice %}}
