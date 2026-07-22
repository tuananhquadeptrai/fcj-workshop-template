---
title: "Deploy the System to AWS"
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

### 1. AWS Production Deployment Architecture Overview
To run **TrustBite** securely, reliably, and scalably on a production-like environment, we design a cloud infrastructure using AWS Managed Services.

Overall architecture deployment flow:
```text
Users ──> CloudFront ──> Application Load Balancer (ALB)
                                 │
                ┌────────────────┴────────────────┐
                ▼                                 ▼
    ECS Fargate (API Service)         ECS Fargate (OCR Worker)
                │                                 │
  ┌─────────────┼─────────────┬─────────────┐     │
  ▼             ▼             ▼             ▼     ▼
Cognito IDP   RDS Postgres  Redis (Queue)  S3 (Receipts) + Textract
```

---

### 2. Step 1: Create Database on Amazon RDS
The system utilizes PostgreSQL. In production, we automate backups and scaling using **Amazon RDS PostgreSQL**:

1. In the AWS Console, search for **RDS** and click **Create database**.
2. **Engine options**: Choose **PostgreSQL** (version v15 or v16 matching the local environment).
3. **Templates**: Select **Dev/Test** or **Free Tier** for testing.
4. **Settings**:
   - **DB instance identifier**: **trustbite-db-prod**.
   - **Master username**: **trustbite_admin**.
   - **Master password**: Enter a secure password.
5. **Connectivity**:
   - Select your system's VPC.
   - **Public access**: Select **No** (only allow internal connection from ECS Fargate tasks).
   - Configure the security group on port **5432** to only accept traffic from the Backend ECS Service security group.
6. Click **Create database**.

---

### 3. Step 2: Create Storage Bucket on Amazon S3
All user-uploaded receipts are stored securely in a private S3 bucket.

1. Navigate to the **Amazon S3 Console** and click **Create bucket**.
2. **Bucket name**: Enter a unique name, e.g., **trustbite-invoices-production**.
3. **AWS Region**: Select **ap-southeast-1**.
4. **Object Ownership**: Choose **ACLs disabled (recommended)**.
5. **Block Public Access settings for this bucket**: Check **Block all public access** to protect user invoice privacy.
6. Click **Create bucket**.
7. Set up **CORS** in the **Permissions** tab of the bucket:
   ```json
   [
     {
       "AllowedHeaders": ["*"],
       "AllowedMethods": ["PUT", "POST"],
       "AllowedOrigins": ["https://trustbite.example.com"],
       "ExposeHeaders": []
     }
   ]
   ```

> [!IMPORTANT]
> **Screenshot Checkpoint:**
> * **Screenshot 13:** The **Permissions** tab for the development receipt bucket shows **Block all public access: On**, confirming that the bucket and its objects are not publicly accessible.

<img src="/images/5-Workshop/5.10-Deployment/13-s3-bucket-private.png" alt="Amazon S3 Block Public Access enabled for the TrustBite development receipt bucket" style="width: 100%; max-width: 1200px; height: auto;">

---

### 4. Step 3: Package and Push Container Image to Amazon ECR
The Express backend application is containerized with Docker. We store container images on **Amazon Elastic Container Registry (ECR)**.

1. In **Amazon ECR**, click **Create repository**.
2. **Repository name**: Enter **trustbite-api-prod**.
3. Run the following commands locally to package and push the container image:
   ```bash
   # 1. Log in AWS CLI to ECR registry
   aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com

   # 2. Build the Docker image from /server folder
   docker build -t trustbite-api-prod ./server

   # 3. Tag the image for ECR repository
   docker tag trustbite-api-prod:latest <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com/trustbite-api-prod:latest

   # 4. Push the image to AWS ECR
   docker push <aws_account_id>.dkr.ecr.ap-southeast-1.amazonaws.com/trustbite-api-prod:latest
   ```

> [!IMPORTANT]
> **Screenshot Checkpoint:**
> * **Screenshot 14:** The **Images** tab of **trustbite-dev-api** shows successfully pushed images tagged with commit SHAs; the repository uses immutable tags instead of **latest**.

<img src="/images/5-Workshop/5.10-Deployment/14-ecr-api-images.png" alt="Commit SHA tagged Docker images in the trustbite-dev-api Amazon ECR repository" style="width: 100%; max-width: 1200px; height: auto;">

---

### 5. Step 4: Run Container Service on Amazon ECS Fargate
We use serverless containers via **Amazon ECS Fargate** to run the backend API and scale automatically without managing EC2 instances.

1. **Create ECS Cluster**: Under **Amazon ECS**, click **Create cluster**, name it **trustbite-cluster**, and click Create.
2. **Create Task Definition**:
   - Choose **Fargate** launch type.
   - Configure CPU: **0.5 vCPU**, Memory: **1 GB**.
   - Add Container: Name it **api-container**, point the Image URL to the ECR URI from Step 3.
   - Declare production environment variables:
     * **DATABASE_HOST** = **<rds_database_endpoint>**
     * **DATABASE_NAME** = **trustbite_db**
     * **AWS_COGNITO_USER_POOL_ID** = **<production_user_pool_id>**
     * **AWS_COGNITO_CLIENT_ID** = **<production_client_id>**
     * **AWS_S3_BUCKET_NAME** = **trustbite-invoices-production**
3. **Create ECS Service**:
   - Inside the cluster **trustbite-cluster**, create a new Service using Fargate launch type.
   - Select the task definition.
   - **Number of tasks**: Set to **2** (run 2 instances in parallel for high availability).
   - **Load balancing**: Attach an **Application Load Balancer (ALB)** to route port 80/443 public traffic to the tasks.
   - Click Create Service.

> [!IMPORTANT]
> **Screenshot Checkpoint:**
> * **Screenshot 15:** The AWS ECS Services page shows the three development services **trustbite-dev-api**, **trustbite-dev-web**, and **trustbite-dev-worker** as **Active** on **Fargate**, each with **1 / 1 Tasks running**.

<img src="/images/5-Workshop/5.10-Deployment/15-ecs-services-running.png" alt="Three TrustBite development services active on Amazon ECS with one of one Fargate task running" style="width: 100%; max-width: 1200px; height: auto;">

---

### 6. Step 5: Smoke Test and Verification
Once the Application Load Balancer (ALB) provides a public URL, we test the health endpoint to confirm backend connectivity to RDS and AWS dependencies.

Send a verification request:
```bash
curl -i http://trustbite-alb-prod-123456789.ap-southeast-1.elb.amazonaws.com/health
```

Expected result (HTTP **200 OK**):
```json
{
  "status": "OK",
  "database": "connected",
  "aws": "verified"
}
```
