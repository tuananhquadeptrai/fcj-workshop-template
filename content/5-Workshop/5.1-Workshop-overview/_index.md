---
title: "Architecture Overview"
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### 1. Overall TrustBite System Architecture
The **TrustBite** backend is built as a **Modular Monolith** to optimize development and deployment overhead, combined with AWS Managed Services to achieve enterprise-grade scalability and security.

Below is the overall architecture diagram of TrustBite components:

<img src="/images/5-Workshop/5.1-Workshop-overview/trustbite-aws-architecture.png" alt="TrustBite deployment architecture on AWS" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

---

### 2. Components and System Roles
*   **Mobile App (Flutter + Dart):** The main client application for end-users. Handles Cognito authentication, map/list restaurant browsing, review creation, and receipt uploading.
*   **Admin Portal (Next.js):** The administration portal for operators to review pending receipt verification queues, approve restaurant claims, and view system audit logs.
*   **Node.js Express API (Backend Monolith):** Serves all business REST APIs, containerized using native ES modules.
*   **Redis & BullMQ:** Message queueing system. Offloads long-running tasks like receipt OCR and anti-fraud analysis to a separate background worker process.
*   **PostgreSQL + PostGIS:** The primary relational database. Leverages the **PostGIS** spatial extension to store location coordinates and run spatial distance queries.
*   **AWS Managed Services:**
    *   **Amazon Cognito:** User directory and identity provider managing sign-up, sign-in, and JWT token issuance.
    *   **Amazon S3:** Private object storage for receipt images.
    *   **AWS Textract:** Document analysis service extracting raw receipt text.

---

### 3. User Authentication Architecture (Amazon Cognito Integration)
User authentication is executed using standard OpenID Connect (OIDC) flow:

```text
User ──> Mobile/Web Client ──> Cognito Managed Login (Authorization Code + PKCE)
                                          │
Backend API <── (Send Bearer Access Token) ── Client receives tokens (Access, ID, Refresh)
     │
     └──> [Verify JWT with Cognito JWKS] ──> Access Protected API
```

#### Authentication vs. Authorization:
*   **Authentication (Who is accessing?):** Managed by Amazon Cognito. Cognito validates user credentials and issues JSON Web Tokens (JWTs).
*   **Authorization (What are they allowed to do?):** Handled by the TrustBite Express backend. After decoding and validating the JWT signature using Cognito's JWKS public keys, the backend inspects scopes/roles (**USER**, **MERCHANT**, **ADMIN**) before resolving data requests.

> [!WARNING]
> Do not use ID tokens to protect REST APIs. The TrustBite backend only accepts access tokens with **token_use** set to **access**.
