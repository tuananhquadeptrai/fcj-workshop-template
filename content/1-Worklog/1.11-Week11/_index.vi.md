---
title: "Worklog Tuần 11"
date: 2024-01-01
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu tuần 11:

* Xây dựng Unit Test và Integration Test cho xác thực Cognito/JWT.
* Phát triển GPS Proximity Service và cấu hình Anti-Fraud.
* Cập nhật CI để tự động chạy migration và toàn bộ test suite.

**Thời gian:** 29/06/2026 - 03/07/2026

### Nội dung đã học trong tuần này:
| Thứ | Nội dung học tập | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2 | Viết Unit Test cho CognitoIdentityProvider và helper sinh JWT phục vụ kiểm thử | 29/06/2026 | 29/06/2026 | <https://github.com/trustbite-team-tcc/trustbite-review-system> |
| 3 | Kiểm thử token hợp lệ, hết hạn, sai issuer, client_id, chữ ký và JWT bị cắt xén | 30/06/2026 | 30/06/2026 | <https://github.com/trustbite-team-tcc/trustbite-review-system> |
| 4 | Viết Integration Test cho đăng nhập và User Profile với user hợp lệ, SUSPENDED và không có token | 01/07/2026 | 01/07/2026 | <https://github.com/trustbite-team-tcc/trustbite-review-system> |
| 5 | Xây dựng GPS Proximity Service bằng công thức Haversine; kiểm tra input và cấu hình ngưỡng Anti-Fraud mặc định 200 m | 02/07/2026 | 02/07/2026 | <https://github.com/trustbite-team-tcc/trustbite-review-system> |
| 6 | Viết 13 test case GPS; nâng tổng số Unit Test đạt 80; cập nhật GitHub Actions chạy migration, test suite và cải tiến test factory | 03/07/2026 | 03/07/2026 | <https://github.com/trustbite-team-tcc/trustbite-review-system> |

### Kết quả đạt được tuần 11:

* Hoàn thiện kiểm thử xác thực JWT và các luồng đăng nhập, User Profile.
* Xây dựng GPS Proximity Service có validation và cấu hình Anti-Fraud linh hoạt.
* Đạt 80 Unit Test thành công và tự động hóa migration, test suite trên CI.
