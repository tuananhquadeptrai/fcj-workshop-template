---
title: "Proposal"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# TrustBite – Verified Food Review Platform
## An Anti-Fraud Review Solution Powered by AWS for Vietnam's F&B Market

---

### 1. Executive Summary

TrustBite (*"Trust in every bite"*) is a food review platform focused on **review authenticity**. Rather than allowing anyone to submit reviews freely, TrustBite requires users to provide real-world evidence — receipts — and verifies them through automated anti-fraud mechanisms.

The system is built as a **monorepo** with three main components:
- **Mobile App (Flutter)**: The primary application for end users (iOS & Android).
- **Web Portal (Next.js)**: Admin portal for system management and merchant portal for restaurant owners.
- **Backend API (Node.js + Express)**: Business logic processing, AWS integration, and data management.

Core AWS services: **Amazon S3** (receipt storage), **Amazon Textract** (OCR for receipt information extraction), **Amazon Cognito** (user authentication), **Amazon SES** (email notifications), and **Amazon Bedrock** (AI-powered review summarization — future phase).

---

### 2. Problem Statement

#### The Problem

Vietnam's food review market is plagued by **fake reviews**:
- Fake 5-star reviews created or purchased by restaurant owners.
- 1-star reviews from competitors aimed at sabotage.
- Users have no way to distinguish real from fake reviews.
- Existing platforms (Google Maps, Facebook) lack receipt verification or proof-of-visit mechanisms.

#### The Solution

TrustBite addresses this through **three verification layers**:

1. **Receipt Verification (OCR)**: Users upload a receipt → Amazon Textract extracts information (restaurant name, date/time, amount) → Cross-referenced against the reviewed restaurant.
2. **Location Verification (GPS)**: Checks the distance between user's location and the restaurant (200m threshold using the Haversine formula).
3. **Duplicate Detection (Hash)**: Computes SHA-256 hash of the receipt to block reuse of previously submitted receipts.

Verification results are aggregated into a **Trust Score** — a confidence indicator for each review.

#### Benefits

- **Users**: Find restaurants based on evidence-backed reviews, protected from fake ratings.
- **Honest Restaurant Owners**: Protected from malicious reviews; verified reviews strengthen credibility.
- **F&B Ecosystem**: Creates a fair competitive environment based on genuine quality.

---

### 3. Solution Architecture

#### Overall Architecture Diagram

<img src="/images/5-Workshop/5.1-Workshop-overview/trustbite-aws-architecture.png" alt="TrustBite deployment architecture showing the AWS networking, compute, data, identity, security, monitoring, and provider services" style="display: block; width: auto; max-width: 100%; height: auto; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>TrustBite deployment architecture on AWS. Click the diagram to view the full-size version.</em></p>

#### AWS Services Used

| Service | Role in System |
| --- | --- |
| **Amazon S3** | Receipt storage (private bucket, access via signed URL) |
| **Amazon Textract** | OCR to extract receipt information (restaurant name, date/time, total amount) |
| **Amazon Cognito** | User authentication, session management |
| **Amazon SES** | Email notifications for verification results |
| **Amazon SNS** | OTP SMS delivery for phone number authentication |
| **Amazon RDS (PostgreSQL)** | Primary database (users, restaurants, reviews, verifications) |
| **Amazon ECS Fargate** | Runs API container and OCR/Fraud worker |
| **Amazon ElastiCache (Redis)** | Async processing queue (BullMQ), OTP rate limiting |
| **Amazon Bedrock** | AI-powered review summarization — future phase (P2) |
| **AWS CloudFront** | CDN for Admin/Merchant web portal |

#### Component Design

- **Mobile App (Flutter)**: Main UI for end users — find restaurants, read reviews, submit reviews + receipts, view Trust Score.
- **Backend API (Express ESM)**: Business logic in layered architecture (routes → controllers → services → models); validates JWT from Cognito.
- **OCR/Fraud Worker**: Runs asynchronously via BullMQ — computes receipt hash, calls Textract, matches OCR results, computes fraud score, updates results in PostgreSQL.
- **Admin Portal (Next.js)**: Administrators review pending verification queue, view receipt images + OCR results + GPS distance, and make final decisions.
- **PostgreSQL + PostGIS**: Stores all product data — users, restaurants, reviews, receipt_verifications, badges, price_history; PostGIS supports geospatial calculations.

---

### 4. Technical Implementation

#### Implementation Phases

| Phase | Content | Timeline |
| --- | --- | --- |
| **Week 1** | Architecture research, codebase familiarization, local environment setup (Docker Compose: PostgreSQL, Redis, LocalStack), and DB schema design. | Month 1 |
| **Week 2** | Harness workflow deployment, backend framework setup, and Amazon Cognito User Pool creation. | Month 1 |
| **Week 3** | Cognito authentication integration (JWT Middleware), building restaurant APIs and user management. | Month 1 |
| **Week 4** | Review submission flow, integrating receipt image upload to S3, and asynchronous OCR extraction worker with AWS Textract. | Month 1 |
| **Week 5** | Anti-fraud engine verification implementation (duplicate SHA-256 hash checks, GPS proximity Haversine checks, Levenshtein merchant name matching), and Trust Score calculations. | Month 2 |
| **Week 6** | Completing Admin Portal UI integration, end-to-end integration testing, and container deployment on AWS ECS Fargate. | Month 2 |

#### Technical Requirements

**Backend:**
- Node.js (v20.9+) + Express with Native ES Modules.
- PostgreSQL + PostGIS (geospatial), Redis + BullMQ (async queue).
- Layered architecture: routes → controllers → services → models.
- JWT validation from Amazon Cognito at the middleware layer.

**Frontend:**
- Flutter (Dart) for mobile app — Android & iOS.
- Next.js (App Router) for Admin/Merchant portal.

**AWS Integrations:**
- S3: Receipt upload via multipart or signed URL.
- Textract: Async OCR processing, results returned via worker.
- Cognito: OTP SMS flow + access token + refresh token.
- SES: Email notification for verification results.

**Anti-Fraud Rules:**

| Rule | Threshold |
| --- | --- |
| Duplicate receipt | SHA-256 hash — reject if duplicate |
| Restaurant name match (OCR) | ≥ 80% similarity |
| Receipt age | ≤ 48 hours from issue date |
| GPS distance | ≤ 200m (Haversine formula) |

---

### 5. Timeline & Milestones

*   **Month 1 (Weeks 1–4):** **Platform Integration & Initialization**
    *   Local environment setup (Docker Compose) & DB schema design.
    *   Harness workflow deployment & backend framework setup.
    *   Cognito authentication integration & building user/restaurant APIs.
    *   Integrating receipt uploads to S3 & automated OCR extraction via AWS Textract.
*   **Month 2 (Weeks 5–6):** **Anti-Fraud Engine & AWS Deployment**
    *   Implementing anti-fraud verification logic & automated Trust Score calculation.
    *   Completing Admin Portal UI integration.
    *   End-to-end integration testing & Docker container deployment on AWS ECS Fargate.
*   **Post-MVP:** **Feature Expansion**
    *   AI-powered review summarization utilizing Amazon Bedrock.
    *   Building the Merchant Portal for restaurant owners.
    *   Publishing applications to App Store & Google Play.

**Checkpoints:**
- **Week 2**: Stable local environment, database schema initialized, and Harness workflow completed.
- **Week 4**: User signup/login via Cognito functional, review posting, and S3 OCR receipt upload integrated.
- **Week 6**: Anti-fraud automated Trust Score engine working accurately, Admin Portal processing reviews smoothly, and AWS ECS Fargate deployment complete.

---

### 6. Budget Estimation

**Estimated monthly AWS costs (staging/MVP environment):**

| Service | Est. Cost/Month | Notes |
| --- | --- | --- |
| Amazon RDS (PostgreSQL db.t3.micro) | ~$15 | Single-AZ, 20 GB storage |
| Amazon ECS Fargate (API + Worker) | ~$10 | 0.25 vCPU, 0.5 GB RAM per task |
| Amazon S3 | ~$1 | ~10 GB receipts, lifecycle policy |
| Amazon Textract | ~$5 | ~500 pages OCR ($1.50/1,000 pages) |
| Amazon ElastiCache (Redis t3.micro) | ~$12 | Cache + queue |
| Amazon Cognito | ~$0 | Free tier: 50,000 MAU |
| Amazon SES | ~$1 | ~10,000 emails |
| Amazon SNS (SMS OTP) | ~$5 | ~500 OTP SMS |
| **Total** | **~$49/month** | Staging environment |

> **During development**: LocalStack simulates S3, Textract, Cognito, SES locally → AWS costs are nearly $0 during local development.

---

### 7. Risk Assessment

#### Risk Matrix

| Risk | Impact | Probability | Mitigation Strategy |
| --- | --- | --- | --- |
| Textract OCR inaccuracy (blurry receipts, unusual formats) | High | Medium | Queue as `PENDING_ADMIN_REVIEW` for manual admin review |
| Users bypassing GPS (fake location) | Medium | Medium | GPS is a supplementary factor, not mandatory; combined with OCR matching |
| Sudden spike in Textract costs | Medium | Low | Upload rate limiting, async processing, only OCR valid files |
| User receipt data exposure | High | Low | S3 private bucket, access only via short-lived signed URLs |
| Database migration causing data loss | High | Low | Test migrations on staging first, rollback scripts ready |

#### Contingency Plans

- **OCR Provider**: Textract can be replaced by another OCR provider if needed (architecture separates the provider layer).
- **Manual Verification**: Admin portal supports manual review when automated verification fails.
- **LocalStack**: Local environment does not depend on real AWS → development continues uninterrupted.

---

### 8. Expected Outcomes

**Technical Achievements:**
- End-to-end automated review verification system: receipt upload → OCR → Trust Score.
- Complete Backend API with layered architecture, JWT authentication, rate limiting.
- Flutter mobile app allowing users to find restaurants, view Trust Scores, and submit verified reviews.
- CI/CD pipeline automating build, test, and deployment to staging.

**Long-term Value:**
- Platform can be extended with AI summarization (Bedrock/Claude) and merchant portal features.
- Monorepo architecture enables the team to develop 3 components in parallel (mobile, web, server).
- PostgreSQL schema with PostGIS supports advanced geospatial features (nearby restaurants, review heatmaps).
