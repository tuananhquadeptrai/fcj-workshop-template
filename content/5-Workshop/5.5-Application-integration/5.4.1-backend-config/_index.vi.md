---
title: "Cấu hình backend"
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

#### Bước 1: Cấu hình biến môi trường

Tạo `.env` trong backend:

```dotenv
PORT=3001
COGNITO_REGION=ap-southeast-1
COGNITO_USER_POOL_ID=ap-southeast-1_AbCdEf123
COGNITO_APP_CLIENT_ID=1example23456789
```

Không hard-code các giá trị theo môi trường trực tiếp trong source code.

#### Bước 2: Kiểm tra cấu hình khi khởi động

Tạo `src/config/cognito.js`:

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

#### Bước 3: Tạo JWT verifier

Tạo `src/auth/cognitoVerifier.js`:

```javascript
import { CognitoJwtVerifier } from "aws-jwt-verify";
import { cognitoConfig } from "../config/cognito.js";

export const accessTokenVerifier = CognitoJwtVerifier.create({
  userPoolId: cognitoConfig.userPoolId,
  tokenUse: "access",
  clientId: cognitoConfig.clientId,
});
```

Verifier tự tải JWKS từ User Pool, chọn public key theo `kid`, kiểm tra chữ ký và cache key để tái sử dụng.

#### Bước 4: Khởi tạo Express

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
Khởi tạo verifier một lần ở cấp module để tái sử dụng JWKS cache; không tạo verifier mới cho từng request.
{{% /notice %}}
