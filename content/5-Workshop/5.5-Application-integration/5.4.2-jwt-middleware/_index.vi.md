---
title: "Xây dựng JWT middleware"
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

#### Bước 1: Tạo authentication middleware

Tạo `src/middlewares/requireAuth.js`:

```javascript
import { accessTokenVerifier } from "../auth/cognitoVerifier.js";

export async function requireAuth(req, res, next) {
  const authorization = req.get("authorization") || "";
  const [scheme, token] = authorization.split(" ");

  if (scheme !== "Bearer" || !token) {
    return res.status(401).json({
      error: "UNAUTHORIZED",
      message: "A valid Bearer access token is required.",
    });
  }

  try {
    const payload = await accessTokenVerifier.verify(token);

    req.auth = {
      userId: payload.sub,
      username: payload.username,
      clientId: payload.client_id,
      scopes: (payload.scope || "").split(" ").filter(Boolean),
      groups: payload["cognito:groups"] || [],
    };

    return next();
  } catch {
    return res.status(401).json({
      error: "INVALID_ACCESS_TOKEN",
      message: "The access token is invalid or expired.",
    });
  }
}
```

Không trả chi tiết lỗi chữ ký hoặc claim cho client vì thông tin này có thể hỗ trợ attacker.

#### Bước 2: Bảo vệ route

Tạo `src/routes/userRoutes.js`:

```javascript
import { Router } from "express";
import { requireAuth } from "../middlewares/requireAuth.js";

export const userRouter = Router();

userRouter.get("/me", requireAuth, async (req, res) => {
  res.json({
    id: req.auth.userId,
    username: req.auth.username,
    groups: req.auth.groups,
  });
});
```

#### Bước 3: Kiểm tra trạng thái nghiệp vụ

Sau khi JWT hợp lệ, backend vẫn cần đọc người dùng từ PostgreSQL và từ chối tài khoản có trạng thái `SUSPENDED` hoặc đã bị xóa. JWT hợp lệ chỉ chứng minh Cognito đã xác thực người dùng, không thay thế toàn bộ authorization nghiệp vụ.

#### Các điều kiện được verifier kiểm tra

- JWT đúng cấu trúc và chưa hết hạn.
- Chữ ký hợp lệ với Cognito JWKS.
- `iss` thuộc đúng User Pool.
- `client_id` thuộc đúng App Client.
- `token_use` bằng `access`.

{{% notice warning %}}
Decode JWT không đồng nghĩa với xác minh JWT. Không tin bất kỳ claim nào trước khi kiểm tra chữ ký và các claim bắt buộc.
{{% /notice %}}
