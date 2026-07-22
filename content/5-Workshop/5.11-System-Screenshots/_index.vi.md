---
title: "Ảnh chụp giao diện hệ thống"
weight: 11
chapter: false
pre: " <b> 5.11. </b> "
---

### 1. Tổng quan giao diện Admin Portal
Cổng quản trị của hệ thống **TrustBite** được xây dựng trên nền tảng **Next.js** kết hợp **TailwindCSS**, mang lại giao diện hiện đại, trực quan và hỗ trợ quản trị viên vận hành các chức năng quản lý, đánh giá và giám sát hệ thống.

Dưới đây là các màn hình chính của Admin Portal kèm ảnh chụp từ môi trường triển khai thực tế.

---

### 2. Giao diện Tổng quan (Dashboard Overview)
Trang điều hành trung tâm hiển thị tình trạng hoạt động của API, tổng số nhà hàng, số mô-đun đã kết nối và các mô-đun đang chờ API. Khu vực bên dưới mô tả mức độ sẵn sàng của từng chức năng quản trị.

Giao diện được phân chia rõ ràng theo các nhóm chức năng ở thanh menu bên trái:

* **Chính:** Tổng quan Dashboard.
* **Quản lý:** Nhà hàng và Người dùng.
* **Tin cậy:** Đánh giá và Xác minh.
* **Hệ thống:** Nhật ký và Giám sát.

<img src="/images/5-Workshop/5.11-System-Screenshots/19-admin-dashboard.png" alt="Trang điều hành trung tâm TrustBite hiển thị trạng thái API, số nhà hàng và mức độ kết nối của các mô-đun quản trị" style="width: 100%; max-width: 1200px; height: auto; display: block; margin: 1.5rem auto;">

> [!IMPORTANT]
> **Ảnh chụp màn hình 19:** Dashboard trung tâm của Admin Portal cho thấy API đang trực tuyến, tổng số nhà hàng và trạng thái của các mô-đun quản trị.

---

### 3. Giao diện Quản lý Nhà hàng (Restaurant Management)
Trang quản lý nhà hàng cho phép quản trị viên mở hồ sơ chi tiết và cập nhật các thông tin vận hành, bao gồm:

* Tên, địa chỉ, số điện thoại và trạng thái nhà hàng.
* Hai trường **Vĩ độ (Latitude)** và **Kinh độ (Longitude)** dùng cho đối chiếu vị trí GPS. Trong ảnh minh chứng, hai trường này đang để trống và có thể được bổ sung khi hoàn thiện dữ liệu nhà hàng.
* Mô tả, danh mục và lý do chỉnh sửa.
* Ảnh đại diện cùng thư viện ảnh chi tiết của nhà hàng.

<img src="/images/5-Workshop/5.11-System-Screenshots/20-restaurant-management.png" alt="Hộp thoại chi tiết nhà hàng TrustBite với thông tin cơ bản, trường vĩ độ và kinh độ đang để trống, cùng khu vực quản lý ảnh nhà hàng" style="width: 100%; max-width: 1200px; height: auto; display: block; margin: 1.5rem auto;">

> [!IMPORTANT]
> **Ảnh chụp màn hình 20:** Hộp thoại chỉnh sửa chi tiết nhà hàng hiển thị các trường thông tin, tọa độ GPS đang để trống và khu vực quản lý ảnh đại diện, ảnh chi tiết.

---

### 4. Giao diện Quản lý Người dùng (User Management)
Trang quản lý người dùng hiển thị danh sách tài khoản và cho phép mở hồ sơ chi tiết của từng người dùng. Trong hộp thoại chi tiết, quản trị viên có thể:

* Xem và cập nhật họ tên, số điện thoại và ngày sinh.
* Theo dõi trạng thái hoạt động, hạng và điểm kinh nghiệm (EXP).
* Nhập lý do quản trị trước khi lưu thay đổi liên quan đến hồ sơ hoặc trạng thái.
* Khóa tài khoản khi cần thiết; thao tác này thu hồi phiên hoạt động và vô hiệu hóa hiệu lực của refresh token theo mô tả trên giao diện.

<img src="/images/5-Workshop/5.11-System-Screenshots/21-user-account-management.png" alt="Hộp thoại chi tiết tài khoản người dùng TrustBite với thông tin hồ sơ, trạng thái hoạt động và chức năng khóa tài khoản" style="width: 100%; max-width: 1200px; height: auto; display: block; margin: 1.5rem auto;">

> [!IMPORTANT]
> **Ảnh chụp màn hình 21:** Giao diện chi tiết người dùng phục vụ chỉnh sửa hồ sơ, kiểm tra trạng thái và khóa tài khoản.

---

### 5. Giao diện Quản lý Đánh giá (Review Management)
Trang quản lý đánh giá tổ chức nội dung theo từng nhà hàng. Quản trị viên có thể tìm kiếm và chọn nhà hàng ở danh sách bên trái, sau đó theo dõi các đánh giá công khai ở khu vực nội dung chính.

Màn hình cung cấp:

1. Tổng số đánh giá phù hợp, điểm trung bình hiện tại và điểm tin cậy của nhà hàng.
2. Điểm tổng của từng đánh giá và điểm theo các tiêu chí như món ăn, giá cả, phục vụ và không gian.
3. Trạng thái của đánh giá, chẳng hạn **Tham khảo**, cùng ngày đăng và thống kê phản hồi.
4. Bộ lọc trạng thái để quản trị viên lựa chọn nhóm đánh giá cần xem.

<img src="/images/5-Workshop/5.11-System-Screenshots/22-review-management.png" alt="Trang quản lý đánh giá TrustBite hiển thị danh sách nhà hàng, điểm tổng hợp, điểm theo tiêu chí, trạng thái đánh giá và bộ lọc" style="width: 100%; max-width: 1200px; height: auto; display: block; margin: 1.5rem auto;">

> [!IMPORTANT]
> **Ảnh chụp màn hình 22:** Giao diện quản lý đánh giá theo nhà hàng với điểm tổng hợp, điểm chi tiết, trạng thái **Tham khảo** và bộ lọc trạng thái.

---
