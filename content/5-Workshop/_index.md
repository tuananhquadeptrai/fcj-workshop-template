---
title: "Workshop"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# AWS Integration & TrustBite Anti-Fraud Engine Workshop

#### Overview

In this workshop, we will explore the system architecture design, the Harness operational workflow, how to configure AWS Cloud infrastructure, and build the automated receipt-based anti-fraud review verification engine for the **TrustBite Review System**.

The steps cover integrating Amazon Cognito authentication, securing receipt image storage via Amazon S3, extracting document text automatically using AWS Textract OCR, analyzing fraud risk signals combined with PostGIS geography, and packaging the Node.js Express backend using Docker to run on serverless containers via AWS ECS Fargate.

{{% notice info %}}
This workshop uses the **Asia Pacific (Singapore) – ap-southeast-1** region. You may select a different region, but must update the region, User Pool ID, and App Client ID values accordingly across all configurations.
{{% /notice %}}

#### Content

1. [Architecture overview](5.1-Workshop-overview/)
2. [Harness Workflow & Standard Docs](5.2-Harness-workflow/)
3. [Prerequisites](5.3-Prerequisites/)
4. [Create and configure a Cognito User Pool](5.4-Cognito-user-pool/)
5. [Integrate Cognito with the application](5.5-Application-integration/)
6. [Receipt Processing with S3 & AWS Textract](5.6-Receipt-OCR-S3/)
7. [Anti-Fraud Engine & Risk Scoring](5.7-Anti-Fraud-Engine/)
8. [Security and best practices](5.8-Security/)
9. [Clean up](5.9-Cleanup/)
10. [Deploy the System to AWS](5.10-Deployment/)
11. [System Screenshots](5.11-System-Screenshots/)

#### Expected results

- The system operates strictly under the Harness workflow standards with living documentation.
- Users can register and confirm email accounts securely via Cognito.
- Receipt images are stored privately in S3 and processed through automated AWS Textract OCR.
- Reviews with receipts are assessed for fraud risk utilizing duplicate SHA-256 hash detection, GPS proximity comparisons (Haversine/PostGIS), and merchant name similarity checks (Levenshtein).
- The backend API is packaged and deployed with high-availability under AWS ECS Fargate.
