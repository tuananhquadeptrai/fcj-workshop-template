---
title: "Workshop"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Hướng dẫn tích hợp AWS & Bộ máy chống gian lận TrustBite

#### Tổng quan

Trong workshop này, chúng ta sẽ tìm hiểu thiết kế hệ thống, quy trình Harness, cách cấu hình hạ tầng AWS và triển khai thực tế bộ máy kiểm định hóa đơn OCR chống gian lận cho dự án **TrustBite Review System**.

Các bước thực hiện bao gồm cấu hình xác thực với Amazon Cognito, lưu trữ hóa đơn riêng tư trên Amazon S3, trích xuất văn bản tự động với AWS Textract, chạy thuật toán chấm điểm rủi ro gian lận kết hợp địa lý PostGIS và cuối cùng là đóng gói ứng dụng bằng Docker để chạy trên cụm serverless container AWS ECS Fargate.

{{% notice info %}}
Workshop sử dụng Region **Asia Pacific (Singapore) – ap-southeast-1**. Bạn có thể chọn Region khác nhưng phải thay giá trị Region, User Pool ID và App Client ID trong toàn bộ cấu hình.
{{% /notice %}}

#### Nội dung

1. [Tổng quan kiến trúc](5.1-Workshop-overview/)
2. [Quy trình Harness & Tài liệu chuẩn](5.2-Harness-workflow/)
3. [Chuẩn bị môi trường](5.3-Prerequisites/)
4. [Tạo và cấu hình Cognito User Pool](5.4-Cognito-user-pool/)
5. [Tích hợp Cognito vào ứng dụng](5.5-Application-integration/)
6. [Xử lý hóa đơn với S3 & AWS Textract](5.6-Receipt-OCR-S3/)
7. [Bộ máy chống gian lận & Chấm điểm rủi ro](5.7-Anti-Fraud-Engine/)
8. [Bảo mật và best practices](5.8-Security/)
9. [Dọn dẹp tài nguyên](5.9-Cleanup/)
10. [Triển khai hệ thống lên AWS](5.10-Deployment/)
11. [Ảnh chụp giao diện hệ thống](5.11-System-Screenshots/)

#### Kết quả mong đợi

- Hệ thống vận hành đúng chuẩn quy trình Harness và có living documentation rõ ràng.
- Người dùng có thể đăng ký và xác nhận tài khoản bằng email qua Cognito.
- Ảnh hóa đơn được tải lên S3 bảo mật và trích xuất OCR Textract tự động.
- Các bài đánh giá kèm hóa đơn được đánh giá rủi ro gian lận chính xác thông qua đối chiếu mã băm SHA-256, khoảng cách vị trí GPS (Haversine/PostGIS), và độ tương đồng tên nhà hàng (Levenshtein).
- Hệ thống backend được đóng gói và chạy ổn định dạng high-availability trên cụm ECS Fargate.
