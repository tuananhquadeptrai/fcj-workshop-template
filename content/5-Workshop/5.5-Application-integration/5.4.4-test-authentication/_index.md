---
title: "Test the authentication flow"
weight: 4
chapter: false
pre: " <b> 5.5.4. </b> "
---

#### Step 1: Register an account

1. Open Managed Login.
2. Sign up with an email address and a valid password.
3. Check the mailbox and enter the confirmation code.
4. In the Cognito Console, open **Users** and confirm that the status is `CONFIRMED`.

<img src="/images/5-Workshop/5.4-Cognito-user-pool/09-cognito-confirmed-users.png" alt="Cognito user list with verified email addresses and Confirmed, Enabled status" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Checkpoint: the test accounts have verified email addresses and are both Confirmed and Enabled.</em></p>

#### Step 2: Sign in and obtain an access token

Sign in through the client or Managed Login. After Authorization Code + PKCE completes, obtain the access token from secure storage or a safe debugging tool.

Never include a real token in documentation or screenshots.

#### Step 3: Call the API without a token

```bash
curl -i http://localhost:3001/api/v1/users/me
```

Expected result:

```http
HTTP/1.1 401 Unauthorized
```

#### Step 4: Call the API with a valid access token

```bash
curl -i \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  http://localhost:3001/api/v1/users/me
```

Expected result:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{"id":"<cognito-sub>","username":"<user>","groups":[]}
```

#### Step 5: Test failure cases

| Case | Expected result |
| --- | --- |
| Missing Authorization header | `401 UNAUTHORIZED` |
| ID token used instead of access token | `401 INVALID_ACCESS_TOKEN` |
| Expired access token | `401 INVALID_ACCESS_TOKEN` |
| One character in the token is modified | `401 INVALID_ACCESS_TOKEN` |
| Token from a different User Pool | `401 INVALID_ACCESS_TOKEN` |
| Token with a different App Client ID | `401 INVALID_ACCESS_TOKEN` |
| Valid JWT for a `SUSPENDED` user | `403` from business authorization |

#### Step 6: Add integration tests

Mock the verifier or use a trusted-local identity only in the test environment. The test suite should confirm that missing and invalid tokens are rejected and verified payloads are accepted.

{{% notice note %}}
Use HTTP `401` when authentication is missing or invalid. Use HTTP `403` when the user is authenticated but lacks permission for the action.
{{% /notice %}}
