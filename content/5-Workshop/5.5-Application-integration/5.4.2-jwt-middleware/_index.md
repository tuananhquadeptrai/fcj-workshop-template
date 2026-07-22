---
title: "Build JWT middleware"
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

#### Step 1: Create authentication middleware

Create `src/middlewares/requireAuth.js`:

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

Do not expose signature or claim failure details to clients because they can help an attacker.

#### Step 2: Protect a route

Create `src/routes/userRoutes.js`:

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

#### Step 3: Check business status

After JWT validation, the backend must still load the user from PostgreSQL and reject accounts that are `SUSPENDED` or deleted. A valid JWT proves that Cognito authenticated the user; it does not replace business authorization.

#### Conditions verified

- The JWT is well-formed and not expired.
- Its signature matches the Cognito JWKS.
- `iss` belongs to the expected User Pool.
- `client_id` belongs to the expected App Client.
- `token_use` is `access`.

{{% notice warning %}}
Decoding a JWT is not verification. Never trust claims before validating the signature and required claims.
{{% /notice %}}
