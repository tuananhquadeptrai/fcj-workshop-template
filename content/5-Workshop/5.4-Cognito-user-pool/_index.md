---
title: "Create a Cognito User Pool"
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Objective

In this section, you create a Cognito User Pool for TrustBite, enable email confirmation, create a public App Client, and configure Managed Login with Authorization Code + PKCE.

#### Content

1. [Create the User Pool](5.3.1-create-user-pool/)
2. [Configure the App Client and Managed Login](5.3.2-configure-app-client/)

#### Resources

| Resource | Example name | Purpose |
| --- | --- | --- |
| User Pool | `trustbite-users-dev` | Stores and authenticates TrustBite users |
| App Client | `trustbite-mobile-dev` | Allows the mobile/web application to use OIDC flows |
| Cognito domain | A unique name you choose | Provides authorization, token, and Managed Login endpoints |

{{% notice note %}}
The User Pool manages users; the App Client represents an application that uses the pool. Do not confuse the User Pool ID with the App Client ID.
{{% /notice %}}
