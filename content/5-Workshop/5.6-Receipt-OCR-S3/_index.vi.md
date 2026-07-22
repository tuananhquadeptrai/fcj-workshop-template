---
title: "Xử lý hóa đơn với S3 & AWS Textract"
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

### 1. Luồng xử lý hóa đơn trong TrustBite
Sau khi xác thực tài khoản thành công, người dùng chụp ảnh hóa đơn bữa ăn tải lên làm bằng chứng tin cậy cho bài đánh giá. Quá trình xử lý hóa đơn của TrustBite được thực hiện qua việc lưu trữ trên dịch vụ lưu trữ đối tượng **Amazon S3** và trích xuất chữ viết bất đồng bộ bằng dịch vụ trí tuệ nhân tạo **AWS Textract**.

```text
Trình duyệt / Mobile client ──(Tải ảnh hóa đơn)──> API Gateway / Express API
                                                        │
                                            [Lưu ảnh hóa đơn vào S3]
                                                        │
                                            [Đưa tác vụ xử lý vào BullMQ]
                                                        │
                                             [Worker chạy ngầm nhận việc]
                                                        │
                                             [Gọi AWS Textract trích xuất]
                                                        │
                                             [Cập nhật kết quả vào DB]
```

---

### 2. Tích hợp dịch vụ lưu trữ đối tượng Amazon S3
Mã nguồn xử lý tương tác lưu trữ S3 nằm tại `server/src/services/s3ReceiptStorageService.js`.

Dịch vụ này sử dụng thư viện **@aws-sdk/client-s3** để thực hiện tải ảnh hóa đơn lên bucket riêng tư, đồng thời hỗ trợ sinh đường dẫn truy cập có chữ ký ngắn hạn (Signed URL) để bảo mật:

*   **Tải hóa đơn lên S3 (Upload):**
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
*   **Sinh Signed URL (cho phép quản trị viên xem ảnh hóa đơn trong 15 phút):**
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

### 3. Trích xuất văn bản hóa đơn qua AWS Textract OCR
Khi có tác vụ trong hàng đợi xử lý hóa đơn (BullMQ), Worker chạy ngầm sẽ gọi dịch vụ OCR từ AWS Textract để trích xuất văn bản.

Đoạn mã cấu hình cuộc gọi nằm tại `server/src/services/ocrService.js` và tương tác với AWS Textract thông qua Command:

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
    FeatureTypes: ['TABLES', 'FORMS'], // Phân tích cấu trúc bảng và form
  });

  const response = await textractClient.send(command);
  return parseTextractResponse(response); // Hàm chuyển đổi cấu trúc khối của Textract thành text phẳng
}
```

Sau khi trích xuất, hệ thống ánh xạ (mapping) các khối văn bản thô để lấy các thuộc tính chính bao gồm:
*   **restaurantName**: Tên nhà hàng xuất hiện trên hóa đơn.
*   **receiptTime**: Ngày giờ giao dịch in trên hóa đơn.
*   **invoiceNo**: Số hóa đơn giao dịch.
*   **totalAmount**: Tổng tiền thanh toán.

> [!IMPORTANT]
> **Hướng dẫn chụp ảnh minh chứng:**
> Hãy chạy bộ unit test kiểm định riêng cho chức năng lưu trữ S3 và trích xuất Textract:
> ```bash
> npm run server:test:unit -- tests/unit/storage/objectStorage.test.js tests/unit/receipt/textractProvider.test.js
> ```
> * **Ảnh chụp màn hình 17:** Kết quả chạy test thành công hiển thị các test case xác định S3 Object Storage và AWS Textract Client đều vượt qua (**passed**).

<img src="/images/5-Workshop/5.6-Receipt-OCR-S3/17-s3-textract-unit-tests.png" alt="Kết quả unit test S3 Object Storage và AWS Textract của TrustBite đều vượt qua" style="width: 100%; max-width: 1200px; height: auto;">
