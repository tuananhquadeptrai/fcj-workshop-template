---
title: "Receipt Processing with S3 & AWS Textract"
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

### 1. Receipt Processing Flow in TrustBite
After successful authentication, users upload an image of their receipt as evidence for their food review. TrustBite processes this receipt image through storage on **Amazon S3** and asynchronous text extraction using **AWS Textract**.

```text
Browser/Mobile app ──(Upload receipt image)──> API Gateway / Express API
                                                    │
                                         [Save receipt to S3]
                                                    │
                                         [Enqueue background task]
                                                    │
                                           [OCR Worker Node]
                                                    │
                                        [AWS Textract Service]
                                                    │
                                     [Update Postgres database]
```

---

### 2. Implementing S3 Receipt Storage Service
The code handling S3 storage integration is located at `server/src/services/s3ReceiptStorageService.js`.

It utilizes the **@aws-sdk/client-s3** library to upload receipt images to a private bucket, and generates short-term signed URLs for secure access:

*   **Upload receipt object to S3:**
    ```javascript
    export async function uploadReceiptObject(key, buffer, contentType) {
      const command = new PutObjectCommand({
        Bucket: awsConfig.s3.bucketName,
        Key: key,
        Body: buffer,
        ContentType: contentType,
      });
      await s3Client.send(command);
    }
    ```
*   **Generate Signed URL (allows administrators to view the receipt for 15 minutes):**
    ```javascript
    export async function getReceiptSignedUrl(key) {
      const command = new GetObjectCommand({
        Bucket: awsConfig.s3.bucketName,
        Key: key,
      });
      return await getSignedUrl(s3Client, command, { expiresIn: 900 });
    }
    ```

---

### 3. Extracting Receipt Text via AWS Textract OCR
When a task is picked up from the receipt processing queue (BullMQ), a background worker calls AWS Textract to perform OCR.

The code coordinating the AWS Textract API call is implemented at `server/src/services/ocrService.js` using the AWS SDK:

```javascript
import { AnalyzeDocumentCommand } from '@aws-sdk/client-textract';

export async function extractReceiptText(bucketName, objectKey) {
  const command = new AnalyzeDocumentCommand({
    Document: {
      S3Object: {
        Bucket: bucketName,
        Name: objectKey,
      },
    },
    FeatureTypes: ['TABLES', 'FORMS'],
  });

  const response = await textractClient.send(command);
  return parseTextractResponse(response);
}
```

Once extracted, the backend maps the raw text blocks to retrieve key attributes:
*   **restaurantName**
*   **receiptTime**
*   **invoiceNo**
*   **totalAmount**

> [!IMPORTANT]
> **Screenshot Checkpoint:**
> Run the unit tests verifying S3 storage and AWS Textract clients:
> ```bash
> npm run server:test:unit -- tests/unit/storage/objectStorage.test.js tests/unit/receipt/textractProvider.test.js
> ```
> * **Screenshot 17:** Successful unit test execution showing S3 Object Storage and AWS Textract provider tests passed.

<img src="/images/5-Workshop/5.6-Receipt-OCR-S3/17-s3-textract-unit-tests.png" alt="Successful TrustBite S3 Object Storage and AWS Textract unit test results" style="width: 100%; max-width: 1200px; height: auto;">
