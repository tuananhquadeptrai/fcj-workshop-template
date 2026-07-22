---
title: "Clean up"
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

#### Workshop summary

You completed the TrustBite authentication flow with Amazon Cognito:

- Created a User Pool, App Client, and Cognito domain.
- Enabled registration and email confirmation.
- Used Authorization Code + PKCE for mobile/web clients.
- Verified access tokens in the Express backend.
- Protected APIs and tested invalid-token scenarios.
- Applied token security and authorization practices.

#### Delete the User Pool

If this is a lab environment that is no longer required:

1. Open the Amazon Cognito Console in the correct Region.
2. Select `trustbite-users-dev`.
3. Confirm that no real applications or users depend on it.
4. Choose **Delete user pool**.
5. Enter the User Pool name to confirm deletion.

Deleting the User Pool removes its users, App Clients, domain, and related configuration. This action cannot be undone.

#### Clean up the local application

- Delete access, ID, and refresh tokens from secure storage.
- Remove real values from `.env`; keep `.env.example` if it is useful documentation.
- Stop the backend and unused local services.
- Remove lab callback URLs from production configurations.
- Check Git history to confirm that no token or secret was committed.

#### Final checks

- The old Cognito domain should stop working after resource deletion completes.
- The backend should fail to start when required environment variables are missing.
- Tokens issued by the deleted User Pool should no longer be trusted by the backend.

{{% notice warning %}}
Never delete a production User Pool or a resource used by a real application. Back up required data and verify dependencies before deletion.
{{% /notice %}}
