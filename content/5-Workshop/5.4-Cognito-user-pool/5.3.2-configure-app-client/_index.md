---
title: "Configure the App Client"
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Step 1: Verify the App Client

1. In the User Pool, choose **App clients**.
2. Open `trustbite-mobile`.
3. Confirm that it has no client secret.
4. Record the **Client ID**.

<img src="/images/5-Workshop/5.4-Cognito-user-pool/05-cognito-mobile-client-details.png" alt="trustbite-mobile App Client details showing no client secret" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Checkpoint: <code>trustbite-mobile</code> is a public client and the Client secret field displays “-”.</em></p>

#### Step 2: Create a Cognito domain

1. Open **Domain** or **Managed login**.
2. Choose a unique domain prefix such as `trustbite-dev-<your-id>`.
3. Save the complete domain:

```text
https://trustbite-dev-<your-id>.auth.ap-southeast-1.amazoncognito.com
```

<img src="/images/5-Workshop/5.4-Cognito-user-pool/06-cognito-domain.png" alt="Cognito domain for Managed Login in the Singapore Region" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>The Cognito domain is assigned to the User Pool's Managed Login.</em></p>

#### Step 3: Configure OAuth 2.0

Configure the App Client:

| Setting | Value |
| --- | --- |
| Allowed callback URL | `http://localhost:3000/auth/callback`, `trustbite://auth/callback` |
| Allowed sign-out URL | `http://localhost:3000` |
| OAuth grant | Authorization code grant |
| OpenID Connect scopes | `openid`, `email`, `phone`, `profile` |
| Identity provider | Cognito user pool |

<img src="/images/5-Workshop/5.4-Cognito-user-pool/07-cognito-oauth-settings.png" alt="Cognito callback URLs, sign-out URL, Authorization Code Grant, and OpenID Connect scopes" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Checkpoint: Managed Login is available with web and mobile callbacks, Authorization Code Grant, and the selected OIDC scopes.</em></p>

Managed Login uses the Authorization Code flow. The application must generate a new `code_verifier` and `code_challenge` for every PKCE request.

#### Step 4: Build a test authorization URL

```text
https://<domain>/oauth2/authorize
  ?response_type=code
  &client_id=<app_client_id>
  &redirect_uri=http%3A%2F%2Flocalhost%3A3000%2Fauth%2Fcallback
  &scope=openid+email+profile
  &code_challenge=<base64url_sha256_of_verifier>
  &code_challenge_method=S256
```

After sign-in, Cognito redirects to the callback URL with a `code`. The client exchanges the code and `code_verifier` at `/oauth2/token` for tokens.

#### Step 5: Save the configuration

Update the backend `.env`:

```dotenv
COGNITO_REGION=ap-southeast-1
COGNITO_USER_POOL_ID=<user_pool_id>
COGNITO_APP_CLIENT_ID=<app_client_id>
```

{{% notice warning %}}
The callback URL must exactly match the registered URL, including protocol, port, path, and any trailing slash.
{{% /notice %}}
