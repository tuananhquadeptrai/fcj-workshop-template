---
title: "Quy trình Harness & Tài liệu chuẩn"
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

### 1. Tổng quan về mô hình Harness trong TrustBite
Dự án **TrustBite** được xây dựng trên một quy chuẩn vận hành và cộng tác chuẩn hóa gọi là **Harness**.

**Harness** là một tập hợp các quy tắc đặc tả và hạ tầng nằm giữa lập trình viên, AI Agent và mã nguồn sản phẩm. Mục tiêu của Harness là tự động hóa các ma sát quy trình, tài liệu hóa các quyết định kiến trúc quan trọng (Decision Records) và đảm bảo mọi tính năng được phát triển đều có bằng chứng nghiệm thu chạy thử rõ ràng (Validation Proof).

Mỗi tác vụ hoàn thành đều tạo ra 2 kết quả đầu ra:
1.  **Product Delta (Sản phẩm thay đổi):** Mã nguồn ứng dụng, file test, API endpoint, schema cơ sở dữ liệu.
2.  **Harness Delta (Quy trình tích lũy):** Nhật ký quyết định vận hành, báo cáo kết quả kiểm thử, hoặc các thay đổi chính sách giúp các tác vụ tương lai dễ dàng hơn.

---

### 2. Quy trình phân loại rủi ro (Feature Intake & Risk Lanes)
Dự án áp dụng quy trình phân loại rủi ro được định nghĩa trong `docs/FEATURE_INTAKE.md` để quyết định mức độ lập kế hoạch, viết mã và kiểm thử cần thiết:

| Phân nhóm rủi ro | Định nghĩa | Tiêu chuẩn Lập kế hoạch & Kiểm thử |
| --- | --- | --- |
| **Tiny (Rất thấp)** | Sửa lỗi giao diện nhỏ, cải thiện hiệu năng nhỏ, hoặc comment code. | Không cần lập kế hoạch phức tạp hay viết tài liệu nhiều. Xác minh nhanh cục bộ. |
| **Normal (Thường)** | Các tính năng nghiệp vụ thông thường (ví dụ: Tìm kiếm nhà hàng, phân trang đánh giá). | Yêu cầu tạo **Story Packet** và định nghĩa tiêu chí nghiệm thu rõ ràng. Bắt buộc viết Unit test. |
| **High-Risk (Cao)** | Thay đổi Schema DB, bảo mật/xác thực (Cognito), lưu trữ dữ liệu, hoặc tích hợp bên thứ ba. | Yêu cầu viết tài liệu **Architectural Decision Record (ADR)** trước khi thực hiện. Bắt buộc viết Integration/E2E test phủ ít nhất 90% các case rủi ro. |

---

### 3. Cơ sở dữ liệu vận hành cục bộ (Durable Layer & Harness CLI)
Để tránh việc theo dõi thủ công hoặc phân tán các file markdown, dự án sử dụng một cơ sở dữ liệu SQLite cục bộ (`harness.db`) cùng công cụ **Harness CLI** viết bằng Rust.

Các lệnh vận hành hàng ngày sử dụng các lệnh chính:
- **Khởi tạo môi trường Harness:**
  ```bash
  npm run harness -- init
  ```
- **Triển khai Schema cơ sở dữ liệu Harness:**
  ```bash
  npm run harness -- migrate
  ```
- **Kiểm tra Ma trận kiểm thử & Trạng thái nghiệm thu:**
  ```bash
  npm run harness -- query matrix
  ```
  *(Lệnh này truy vấn trực tiếp cơ sở dữ liệu để xuất ra trạng thái triển khai của từng Domain nghiệp vụ).*

---

### 4. Quy chuẩn Tài liệu Sống (Living Documentation)
Mọi tài liệu thiết kế của TrustBite đều tuân theo nguyên lý "Tài liệu sống" (Living Documentation) nằm trực tiếp trong kho mã nguồn, được chia thành hai nhóm chính:

#### 4.1. Thư mục `docs/` (Quy định cấu trúc kỹ thuật phát triển)
*   **[ARCHITECTURE.md](file:///C:/My_Project/docs/ARCHITECTURE.md):** Bản đồ kiến trúc chi tiết, quy định ranh giới phụ thuộc (Dependency Rules) giữa các lớp (Client UI -> API -> Service -> DB).
*   **[CONTEXT_RULES.md](file:///C:/My_Project/docs/CONTEXT_RULES.md):** Quy định thiết kế ngữ cảnh (Context Engineering). Tài liệu này hướng dẫn chính xác những gì AI Agent nên đọc trong từng pha (Intake, Planning, Implementation, Validation) để tối ưu hóa hiệu quả làm việc và tiết kiệm token.
*   **`docs/decisions/`:** Thư mục lưu trữ các quyết định kiến trúc lâu dài (Architecture Decision Records - ADR) theo định dạng Markdown tiêu chuẩn.
*   **`docs/stories/`:** Thư mục lưu trữ tài liệu mô tả nghiệp vụ và nghiệm thu chi tiết cho từng chặng phát triển.

#### 4.2. Thư mục `trustbite-docs/` (Kho tài liệu sản phẩm và nghiệp vụ của doanh nghiệp)
Được tổ chức rất khoa học theo các nhóm chuyên môn:
- **`01_Product_Management/`:** Project Charter, PRD, MVP Scope để nắm rõ mục tiêu sản phẩm.
- **`02_Business_Analysis/`:** Functional Spec, Business Rules, State Machines mô tả chi tiết trạng thái hệ thống.
- **`04_Software_Engineering/`:** API Spec, DB Schema, AWS Infrastructure Design mô tả hạ tầng đám mây.
- **`05_Security_Algorithms/`:** Anti-Fraud Specification (Mô tả chi tiết thuật toán kiểm tra hóa đơn OCR, khoảng cách GPS và phát hiện gian lận).
