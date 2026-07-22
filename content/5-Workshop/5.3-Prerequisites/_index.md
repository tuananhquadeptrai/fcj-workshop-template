---
title: "Prerequisites"
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Account and tools

- An AWS account with permission to create and manage Cognito user pools.
- Node.js 20 or later and npm.
- An Express backend using Native ES Modules.
- Git, a code editor, and an API client such as Postman or `curl`.
- An email address that can receive confirmation codes.

#### Workshop values

| Setting | Example value |
| --- | --- |
| Region | `ap-southeast-1` |
| User Pool name | `trustbite-users-dev` |
| App Client name | `trustbite-mobile-dev` |
| Callback URL | `http://localhost:3000/auth/callback` |
| Sign-out URL | `http://localhost:3000/logout` |
| Protected API | `http://localhost:3001/api/v1/users/me` |

#### Prepare the backend

Install Express, environment variable support, and the AWS-recommended JWT verification library for Node.js:

```bash
npm install express dotenv aws-jwt-verify
```

Create `.env.example` to document required variables without including real values:

```dotenv
PORT=3001
COGNITO_REGION=ap-southeast-1
COGNITO_USER_POOL_ID=ap-southeast-1_xxxxxxxxx
COGNITO_APP_CLIENT_ID=xxxxxxxxxxxxxxxxxxxxxxxxxx
```

{{% notice warning %}}
Never commit `.env`, passwords, authorization codes, access tokens, ID tokens, or refresh tokens to Git.
{{% /notice %}}

#### IAM permissions

The workshop operator needs Cognito management permissions in the lab environment. In production, limit permissions to the required actions and user pool under the principle of least privilege.
