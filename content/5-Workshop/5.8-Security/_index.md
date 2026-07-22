---
title: "Security and best practices"
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

#### Security checklist

| Area | Recommendation |
| --- | --- |
| App Client | Mobile/SPA clients have no secret and use Authorization Code + PKCE |
| API token | Accept only access tokens and require `token_use=access` |
| JWT | Verify signature, `exp`, `iss`, `client_id`, and required scopes/groups |
| Token storage | Use Keychain/Keystore on mobile; do not store ID/access tokens in browser localStorage |
| Password | Use a strong policy; prefer passkeys or SRP for custom password forms |
| MFA | Enable TOTP or another MFA method appropriate to application risk |
| Refresh token | Revoke it at sign-out or when a session appears compromised |
| Public sign-up | Enable only when Internet users should be able to self-register |
| Administration | Apply IAM least privilege; never place AWS credentials in clients |
| Abuse protection | Apply rate limits, threat protection, and AWS WAF when appropriate |
| User attributes | Allow the App Client to read/write only required attributes |
| Logging | Never log passwords, authorization codes, or raw tokens |

#### Group-based authorization

Create groups such as `users`, `merchants`, and `admins`. The `cognito:groups` claim appears in tokens and can be read by middleware, but the backend should still reconcile business permissions with PostgreSQL.

Example admin middleware:

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

#### Required security tests

- Valid, expired, and truncated tokens.
- Invalid signature, issuer, User Pool, and App Client ID.
- ID token presented to an endpoint that requires an access token.
- Valid, `SUSPENDED`, and soft-deleted users.
- Insufficient scope or group membership.
- Malformed Bearer headers.

#### Official AWS references

- [Amazon Cognito User Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools.html)
- [Create an application in the Cognito Console](https://docs.aws.amazon.com/cognito/latest/developerguide/getting-started-user-pools-application.html)
- [Verify JSON Web Tokens](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-verifying-a-jwt.html)
- [User Pool security best practices](https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-security-best-practices.html)
- [`aws-jwt-verify`](https://github.com/awslabs/aws-jwt-verify)

{{% notice warning %}}
Cognito handles authentication, but the application remains responsible for authorization, account status, token protection, input validation, and data access control.
{{% /notice %}}
