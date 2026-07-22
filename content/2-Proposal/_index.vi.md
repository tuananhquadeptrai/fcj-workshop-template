---
title: "Bản đề xuất"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# TrustBite – Nền tảng đánh giá ẩm thực có xác minh
## Giải pháp chống gian lận đánh giá bằng AWS cho thị trường F&B Việt Nam

---

### 1. Tóm tắt điều hành

TrustBite (*"Trust in every bite"*) là nền tảng đánh giá ẩm thực tập trung vào **tính xác thực của đánh giá**. Thay vì cho phép bất kỳ ai gửi đánh giá tùy ý, TrustBite yêu cầu người dùng cung cấp bằng chứng thực tế — hóa đơn mua hàng — và xác minh qua các cơ chế chống gian lận tự động.

Hệ thống được xây dựng theo kiến trúc **monorepo** với ba thành phần chính:
- **Mobile App (Flutter)**: Ứng dụng chính dành cho người dùng cuối (iOS & Android).
- **Web Portal (Next.js)**: Admin portal quản lý hệ thống và merchant portal cho chủ quán.
- **Backend API (Node.js + Express)**: Xử lý nghiệp vụ, tích hợp AWS và quản lý dữ liệu.

Các dịch vụ AWS cốt lõi: **Amazon S3** (lưu trữ hóa đơn), **Amazon Textract** (OCR trích xuất thông tin hóa đơn), **Amazon Cognito** (xác thực người dùng), **Amazon SES** (email thông báo) và **Amazon Bedrock** (tóm tắt đánh giá bằng AI — giai đoạn tương lai).

---

### 2. Tuyên bố vấn đề

#### Vấn đề hiện tại

Thị trường đánh giá ẩm thực tại Việt Nam đang đối mặt với tình trạng **đánh giá giả tràn lan**:
- Đánh giá 5 sao ảo do chủ quán tự tạo hoặc thuê ngoài.
- Đánh giá 1 sao từ đối thủ cạnh tranh nhắm phá hoại.
- Người dùng không có cơ chế phân biệt đánh giá thật/giả.
- Các nền tảng hiện tại (Google Maps, Facebook) không có xác minh hóa đơn hay bằng chứng ghé thăm thực tế.

#### Giải pháp

TrustBite giải quyết vấn đề qua **ba lớp xác minh**:

1. **Xác minh hóa đơn (OCR)**: Người dùng tải hóa đơn → Amazon Textract trích xuất thông tin (tên quán, ngày giờ, số tiền) → So khớp với quán được đánh giá.
2. **Xác minh vị trí (GPS)**: Kiểm tra khoảng cách giữa vị trí người dùng và quán ăn (ngưỡng 200m, dùng công thức Haversine).
3. **Phát hiện trùng lặp (Hash)**: Tính SHA-256 hash của hóa đơn để chặn nộp lại hóa đơn đã dùng.

Kết quả xác minh được tổng hợp thành **Trust Score** — chỉ số tin cậy cho mỗi đánh giá.

#### Lợi ích

- **Người dùng**: Tìm quán ăn dựa trên đánh giá có bằng chứng thực tế, không lo bị lừa bởi đánh giá ảo.
- **Chủ quán trung thực**: Được bảo vệ khỏi đánh giá phá hoại; đánh giá có xác minh tăng uy tín.
- **Hệ sinh thái F&B**: Tạo môi trường cạnh tranh lành mạnh dựa trên chất lượng thực sự.

---

### 3. Kiến trúc giải pháp

#### Sơ đồ kiến trúc tổng thể

<img src="/images/5-Workshop/5.1-Workshop-overview/trustbite-aws-architecture.png" alt="Kiến trúc triển khai TrustBite thể hiện các dịch vụ mạng, điện toán, dữ liệu, định danh, bảo mật, giám sát và dịch vụ tích hợp trên AWS" style="display: block; width: auto; max-width: 100%; height: auto; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Kiến trúc triển khai TrustBite trên AWS. Nhấn vào sơ đồ để xem phiên bản kích thước đầy đủ.</em></p>

#### Dịch vụ AWS sử dụng

| Dịch vụ | Vai trò trong hệ thống |
| --- | --- |
| **Amazon S3** | Lưu trữ hóa đơn (private bucket, truy cập qua signed URL) |
| **Amazon Textract** | OCR trích xuất thông tin hóa đơn (tên quán, ngày giờ, tổng tiền) |
| **Amazon Cognito** | Xác thực người dùng, quản lý phiên đăng nhập |
| **Amazon SES** | Gửi email thông báo kết quả xác minh |
| **Amazon SNS** | Gửi OTP SMS cho xác thực số điện thoại |
| **Amazon RDS (PostgreSQL)** | Cơ sở dữ liệu chính (users, restaurants, reviews, verifications) |
| **Amazon ECS Fargate** | Chạy API container và OCR/Fraud worker |
| **Amazon ElastiCache (Redis)** | Queue xử lý bất đồng bộ (BullMQ), rate limiting OTP |
| **Amazon Bedrock** | Tóm tắt đánh giá bằng AI — giai đoạn tương lai (P2) |
| **AWS CloudFront** | CDN cho Admin/Merchant web portal |

#### Thiết kế thành phần

- **Mobile App (Flutter)**: Giao diện chính cho người dùng — tìm quán, xem đánh giá, gửi đánh giá + hóa đơn, xem Trust Score.
- **Backend API (Express ESM)**: Xử lý nghiệp vụ theo kiến trúc phân tầng (routes → controllers → services → models); xác thực JWT từ Cognito.
- **OCR/Fraud Worker**: Chạy bất đồng bộ qua BullMQ — tính hash hóa đơn, gọi Textract, so khớp OCR, tính điểm gian lận, cập nhật kết quả vào PostgreSQL.
- **Admin Portal (Next.js)**: Quản trị viên xem hàng đợi chờ xét duyệt, xem ảnh hóa đơn + kết quả OCR + khoảng cách GPS, đưa ra quyết định cuối.
- **PostgreSQL + PostGIS**: Lưu trữ toàn bộ dữ liệu sản phẩm — users, restaurants, reviews, receipt_verifications, badges, price_history; PostGIS hỗ trợ tính toán địa lý.

---

### 4. Triển khai kỹ thuật

#### Các giai đoạn triển khai

| Giai đoạn | Nội dung | Thời gian |
| --- | --- | --- |
| **Tuần 1** | Nghiên cứu kiến trúc, tìm hiểu codebase, thiết lập môi trường local (Docker Compose: PostgreSQL, Redis, LocalStack) và thiết kế DB Schema. | Tháng 1 |
| **Tuần 2** | Triển khai quy trình Harness, cấu hình dự án backend, khởi tạo Amazon Cognito User Pool. | Tháng 1 |
| **Tuần 3** | Tích hợp xác thực Cognito (JWT Middleware), xây dựng API danh mục nhà hàng và người dùng. | Tháng 1 |
| **Tuần 4** | Xây dựng luồng đăng bài đánh giá, tích hợp tải ảnh hóa đơn lên S3 và worker OCR trích xuất với AWS Textract. | Tháng 1 |
| **Tuần 5** | Phát triển bộ máy chống gian lận (so khớp SHA-256 hóa đơn, đối chiếu khoảng cách GPS Haversine, độ tương đồng tên quán Levenshtein) và thuật toán tính Trust Score. | Tháng 2 |
| **Tuần 6** | Hoàn thiện giao diện quản trị Admin Portal, kiểm thử tích hợp toàn diện và triển khai container lên AWS ECS Fargate. | Tháng 2 |

#### Yêu cầu kỹ thuật

**Backend:**
- Node.js (v20.9+) + Express với Native ES Modules.
- PostgreSQL + PostGIS (địa lý), Redis + BullMQ (queue bất đồng bộ).
- Kiến trúc phân tầng: routes → controllers → services → models.
- Xác thực JWT từ Amazon Cognito ở middleware layer.

**Frontend:**
- Flutter (Dart) cho mobile app — Android & iOS.
- Next.js (App Router) cho Admin/Merchant portal.

**AWS Integrations:**
- S3: Upload hóa đơn qua multipart hoặc signed URL.
- Textract: OCR bất đồng bộ, kết quả trả về qua worker.
- Cognito: OTP SMS flow + access token + refresh token.
- SES: Thông báo email kết quả xác minh.

**Anti-Fraud Rules:**

| Quy tắc | Ngưỡng |
| --- | --- |
| Trùng lặp hóa đơn | SHA-256 hash — reject nếu trùng |
| Độ khớp tên quán (OCR) | ≥ 80% similarity |
| Tuổi hóa đơn | ≤ 48 giờ kể từ ngày phát hành |
| Khoảng cách GPS | ≤ 200m (Haversine formula) |

---

### 5. Lộ trình & Mốc triển khai

*   **Tháng 1 (Tuần 1–4):** **Khởi tạo & Tích hợp nền tảng**
    *   Thiết lập môi trường local (Docker) & Thiết kế DB Schema.
    *   Triển khai quy trình Harness & Khởi tạo backend framework.
    *   Tích hợp xác thực Cognito & Xây dựng APIs quản lý người dùng/nhà hàng.
    *   Xây dựng luồng đăng tải hóa đơn lên S3 & OCR trích xuất tự động qua AWS Textract.
*   **Tháng 2 (Tuần 5–6):** **Bộ máy chống gian lận & Triển khai AWS**
    *   Hoàn thiện bộ máy kiểm định chống gian lận & Tính toán điểm tin cậy Trust Score.
    *   Tích hợp giao diện quản trị Admin Portal.
    *   Kiểm thử tích hợp toàn diện (E2E) & Triển khai Docker lên AWS ECS Fargate.
*   **Sau MVP (Post-MVP):** **Mở rộng tính năng**
    *   Tóm tắt bài đánh giá tự động bằng AI qua Amazon Bedrock.
    *   Xây dựng cổng thông tin cho Chủ quán (Merchant Portal).
    *   Phát hành ứng dụng lên các kho ứng dụng (App Store / Google Play).

**Mốc kiểm tra:**
- **Tuần 2**: Thiết lập môi trường local ổn định, khởi tạo database schema và hoàn tất Harness workflow.
- **Tuần 4**: Người dùng đăng ký/đăng nhập qua Cognito thành công, tạo bài đánh giá và tải hóa đơn lên S3 OCR hoàn chỉnh.
- **Tuần 6**: Bộ máy chống gian lận tự động chấm điểm Trust Score hoạt động chính xác, Admin Portal duyệt hóa đơn thông suốt, hoàn tất deploy lên AWS ECS Fargate.

---

### 6. Ước tính ngân sách

**Chi phí AWS hàng tháng (ước tính cho môi trường staging/MVP):**

| Dịch vụ | Ước tính/tháng | Ghi chú |
| --- | --- | --- |
| Amazon RDS (PostgreSQL db.t3.micro) | ~$15 | Single-AZ, 20 GB storage |
| Amazon ECS Fargate (API + Worker) | ~$10 | 0.25 vCPU, 0.5 GB RAM mỗi task |
| Amazon S3 | ~$1 | ~10 GB hóa đơn, lifecycle policy |
| Amazon Textract | ~$5 | ~500 trang OCR (1,5 USD/1000 trang) |
| Amazon ElastiCache (Redis t3.micro) | ~$12 | Cache + queue |
| Amazon Cognito | ~$0 | Free tier: 50,000 MAU |
| Amazon SES | ~$1 | ~10,000 email |
| Amazon SNS (SMS OTP) | ~$5 | ~500 OTP SMS |
| **Tổng** | **~$49/tháng** | Môi trường staging |

> **Giai đoạn phát triển**: Dùng LocalStack để mô phỏng S3, Textract, Cognito, SES → chi phí AWS gần bằng $0 trong quá trình phát triển local.

---

### 7. Đánh giá rủi ro

#### Ma trận rủi ro

| Rủi ro | Mức ảnh hưởng | Xác suất | Chiến lược giảm thiểu |
| --- | --- | --- | --- |
| OCR Textract không chính xác (hóa đơn mờ, định dạng lạ) | Cao | Trung bình | Xếp vào hàng đợi `PENDING_ADMIN_REVIEW` để admin xét thủ công |
| Người dùng bypass GPS (fake location) | Trung bình | Trung bình | GPS là yếu tố bổ sung, không phải bắt buộc; kết hợp với OCR matching |
| Chi phí Textract tăng đột biến | Trung bình | Thấp | Rate limiting upload, xử lý bất đồng bộ, chỉ OCR file hợp lệ |
| Lộ hóa đơn cá nhân của người dùng | Cao | Thấp | S3 private bucket, chỉ truy cập qua signed URL ngắn hạn |
| Database migration gây mất dữ liệu | Cao | Thấp | Test migration trên staging trước, rollback script sẵn sàng |

#### Kế hoạch dự phòng

- **OCR provider**: Có thể thay Textract bằng provider OCR khác nếu cần (kiến trúc đã tách layer).
- **Xác minh thủ công**: Admin portal hỗ trợ xét duyệt thủ công khi tự động thất bại.
- **LocalStack**: Môi trường local không phụ thuộc AWS thật → phát triển không bị gián đoạn khi mạng có vấn đề.

---

### 8. Kết quả kỳ vọng

**Cải tiến kỹ thuật:**
- Hệ thống xác minh đánh giá tự động hoạt động end-to-end: upload hóa đơn → OCR → Trust Score.
- API Backend hoàn chỉnh với kiến trúc phân tầng, JWT authentication, rate limiting.
- Mobile app Flutter có thể tìm quán, xem Trust Score và gửi đánh giá có xác minh.
- CI/CD pipeline tự động hóa build, test và deploy lên staging.

**Giá trị dài hạn:**
- Nền tảng có thể mở rộng thêm tính năng AI summarization (Bedrock/Claude) và merchant portal.
- Kiến trúc monorepo cho phép team phát triển song song 3 thành phần (mobile, web, server).
- Database schema với PostGIS hỗ trợ các tính năng địa lý nâng cao trong tương lai (tìm quán gần đây, heatmap đánh giá).
