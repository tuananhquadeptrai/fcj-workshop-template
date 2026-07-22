---
title: "Configure the backend"
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

#### Step 1: Configure environment variables

Create the backend `.env`:

```dotenv
PORT=3001
COGNITO_REGION=ap-southeast-1
COGNITO_USER_POOL_ID=ap-southeast-1_AbCdEf123
COGNITO_APP_CLIENT_ID=1example23456789
```

Do not hard-code environment-specific values in source code.

#### Step 2: Validate configuration at startup

Create `src/config/cognito.js`:

```javascript
const required = [
  "COGNITO_REGION",
  "COGNITO_USER_POOL_ID",
  "COGNITO_APP_CLIENT_ID",
];

for (const key of required) {
  if (!process.env[key]) {
    throw new Error(`Missing required environment variable: ${key}`);
  }
}

export const cognitoConfig = Object.freeze({
  region: process.env.COGNITO_REGION,
  userPoolId: process.env.COGNITO_USER_POOL_ID,
  clientId: process.env.COGNITO_APP_CLIENT_ID,
});
```

#### Step 3: Create the JWT verifier

Create `src/auth/cognitoVerifier.js`:

```javascript
import { CognitoJwtVerifier } from "aws-jwt-verify";
import { cognitoConfig } from "../config/cognito.js";

export const accessTokenVerifier = CognitoJwtVerifier.create({
  userPoolId: cognitoConfig.userPoolId,
  tokenUse: "access",
  clientId: cognitoConfig.clientId,
});
```

The verifier downloads the User Pool JWKS, selects the public key by `kid`, validates the signature, and caches keys for reuse.

#### Step 4: Initialize Express

```javascript
import "dotenv/config";
import express from "express";
import { userRouter } from "./routes/userRoutes.js";

const app = express();
app.use(express.json());
app.use("/api/v1/users", userRouter);

app.listen(process.env.PORT || 3001);
```

{{% notice note %}}
Create the verifier once at module level so its JWKS cache is reused; do not create a new verifier for every request.
{{% /notice %}}
