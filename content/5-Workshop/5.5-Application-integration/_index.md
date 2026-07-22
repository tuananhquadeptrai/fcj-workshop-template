---
title: "Integrate Cognito with the Application"
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Objective

In this section, you integrate the User Pool with the TrustBite Express backend and client application. The backend verifies access tokens with Cognito public keys; the client uses Authorization Code + PKCE and stores tokens securely.

#### Content

1. [Configure the backend](5.4.1-backend-config/)
2. [Build JWT middleware](5.4.2-jwt-middleware/)
3. [Integrate client sign-in](5.4.3-client-login/)
4. [Test the authentication flow](5.4.4-test-authentication/)

#### Protected request flow

```text
Client → Authorization: Bearer <access_token>
       → Express authentication middleware
       → Verify signature + exp + iss + client_id + token_use
       → Check user/account permissions
       → Controller → Service → Database
```
