---
title: "Bộ máy chống gian lận & Chấm điểm rủi ro"
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

### 1. Tổng quan bộ máy phòng chống gian lận
Điểm đổi mới sáng tạo cốt lõi trong đề xuất thiết kế của hệ thống **TrustBite** là quy trình chấm điểm rủi ro tự động (Automated Risk Scoring) dựa trên phân tích hóa đơn để xác định độ tin cậy của bài đánh giá ẩm thực.

Hệ thống thiết lập **ba lớp phòng thủ chính**, tính toán cộng dồn điểm rủi ro (Risk Points) để đưa ra quyết định duyệt tự động hoặc chuyển sang phê duyệt thủ công:

---

### 2. Lớp phòng thủ 1: Kiểm tra mã băm trùng lặp hóa đơn (SHA-256)
Hệ thống ngăn chặn hành vi người dùng gian lận bằng cách tải lên cùng một hình ảnh hóa đơn cũ cho nhiều bài đánh giá khác nhau.

Trước khi tiến hành OCR, Backend thực hiện tính toán mã băm **SHA-256** của tệp ảnh hóa đơn và đối chiếu dữ liệu trong PostgreSQL:
```javascript
import crypto from 'node:crypto';

export function computeFileHash(buffer) {
  return crypto.createHash('sha256').update(buffer).digest('hex');
}
```
*   **Quy tắc:** Nếu phát hiện mã băm (hash) đã tồn tại trong cơ sở dữ liệu, hóa đơn sẽ bị chấm điểm rủi ro cao nhất (**+100 điểm rủi ro**) và bài đánh giá tự động rơi vào trạng thái từ chối (**REJECTED**).

---

### 3. Lớp phòng thủ 2: Đối chiếu khoảng cách GPS (Haversine & PostGIS)
Hệ thống đối sánh tọa độ GPS của người dùng tại thời điểm đăng bài đánh giá với tọa độ vị trí thực tế của nhà hàng đã được xác minh trong cơ sở dữ liệu.

Khoảng cách hình cầu giữa hai điểm được tính toán theo công thức **Haversine** tại `server/src/services/receiptVerificationScoring.js`:

```javascript
const EARTH_RADIUS_METERS = 6371000;

export function haversineMeters(lat1, lng1, lat2, lng2) {
  const dPhi = toRad(lat2 - lat1);
  const dLambda = toRad(lng2 - lng1);
  const phi1 = toRad(lat1);
  const phi2 = toRad(lat2);

  const a = Math.sin(dPhi / 2) ** 2 + Math.cos(phi1) * Math.cos(phi2) * Math.sin(dLambda / 2) ** 2;
  return 2 * EARTH_RADIUS_METERS * Math.asin(Math.min(1, Math.sqrt(a)));
}
```
*   **Quy tắc chấm điểm:**
    *   Khoảng cách **<= 200m**: Hợp lệ (**+0 điểm rủi ro**).
    *   Khoảng cách **> 200m** (đăng bài ngay tại quán trong vòng 1 giờ): **+40 điểm rủi ro**.
    *   Khoảng cách **> 200m** (đăng bài từ xa sau 1 giờ): **+10 điểm rủi ro**.
    *   Người dùng tắt định vị / từ chối GPS: **+30 điểm rủi ro**.

#### Tối ưu hóa truy vấn không gian trên PostgreSQL với PostGIS:
Trong thiết kế cơ sở dữ liệu thực tế, các bảng **restaurants** và **restaurant_branches** được cấu hình kiểu dữ liệu địa lý **GEOGRAPHY(Point, 4326)** và lập chỉ mục **GIST** để thực hiện tìm kiếm quán ăn xung quanh vị trí người dùng siêu tốc:
```sql
CREATE INDEX idx_restaurants_geo ON restaurants USING Gist(geo);
```
Truy vấn SQL sử dụng hàm **ST_DWithin** và **ST_Distance** giúp tính toán khoảng cách không gian địa lý chính xác và tận dụng index hiệu quả:
```sql
SELECT id, name, ST_Distance(geo, ST_MakePoint($1, $2)::geography) as distance
FROM restaurants
WHERE ST_DWithin(geo, ST_MakePoint($1, $2)::geography, $3);
```

---

### 4. Lớp phòng thủ 3: Đối chiếu tên quán trên hóa đơn qua thuật toán Levenshtein
Tên nhà hàng được trích xuất từ văn bản hóa đơn qua AWS Textract sẽ được so sánh với tên nhà hàng đăng ký trong cơ sở dữ liệu. Vì hóa đơn thực tế có thể bị mờ, rách hoặc Textract nhận diện thiếu ký tự, hệ thống sử dụng thuật toán tính khoảng cách tương đồng **Levenshtein** thay vì so khớp chuỗi tuyệt đối.

1.  **Chuẩn hóa văn bản:** Loại bỏ dấu tiếng Việt, chuyển chữ thường, loại bỏ hậu tố doanh nghiệp phổ biến (co ltd, ltd, branch, restaurant,...), xóa ký tự đặc biệt và khoảng trắng thừa:
    ```javascript
    export function normalizeMerchantName(name) {
      return name
        .normalize('NFD').replace(/[\u0300-\u036f]/g, '') // Bỏ dấu tiếng Việt
        .replace(/đ/gi, 'd')
        .toLowerCase()
        .replace(/\bco\.?\s*,?\s*ltd\.?\b/g, ' ') // Bỏ hậu tố
        .replace(/[^a-z0-9\s]/g, ' ') // Bỏ ký tự đặc biệt
        .replace(/\s+/g, ' ').trim();
    }
    ```
2.  **Tính độ tương đồng chuỗi Levenshtein (Levenshtein similarity):** Trả về tỷ lệ phần trăm tương đồng từ 0% đến 100%:
    *   Độ tương đồng **80% - 100%**: Khớp cao (**+0 điểm rủi ro**).
    *   Độ tương đồng **60% - 79%**: Khớp trung bình (**+25 điểm rủi ro**).
    *   Độ tương đồng **< 60%**: Khớp kém (**+60 điểm rủi ro**).
    *   Không đọc được tên quán trên hóa đơn: **+50 điểm rủi ro**.

---

### 5. Thuật toán tổng hợp điểm rủi ro & Ra quyết định tự động
Sau khi tính toán các tín hiệu (bao gồm cả tuổi hóa đơn và hành vi tài khoản), hàm **scoreReceipt** cộng tổng điểm rủi ro và hàm **decideFromScore** đưa ra quyết định:

*   **Tổng điểm 0 - 30 (Rủi ro thấp):** Tự động duyệt xác minh bài đánh giá (**VERIFIED**).
*   **Tổng điểm 31 - 60 (Rủi ro trung bình):** Chuyển vào hàng đợi chờ duyệt thủ công (**PENDING_ADMIN_REVIEW**).
*   **Tổng điểm 61 - 99 (Rủi ro khá):** Cho phép hiển thị công khai bài viết nhưng dán nhãn độ tin cậy thấp (**REFERENCE_ONLY**).
*   **Tổng điểm từ 100 (Gian lận):** Từ chối bài đánh giá và khóa hiển thị (**REJECTED**).

> [!IMPORTANT]
> **Hướng dẫn chụp ảnh minh chứng:**
> Chạy bộ kiểm thử đơn vị dành riêng cho các quy tắc chống gian lận và thuật toán khoảng cách:
> ```bash
> npm run server:test:unit -- tests/unit/receipt/receiptVerificationScoring.test.js
> ```
> * **Ảnh chụp màn hình 18:** Kết quả chạy test thành công hiển thị các case kiểm thử thuật toán khoảng cách Haversine, chuẩn hóa tên quán, tính khoảng cách Levenshtein và chấm điểm rủi ro đều vượt qua (**passed**).

<img src="/images/5-Workshop/5.7-Anti-Fraud-Engine/18-anti-fraud-unit-tests.png" alt="Kết quả unit test các quy tắc chống gian lận và chấm điểm rủi ro của TrustBite đều vượt qua" style="width: 100%; max-width: 1200px; height: auto;">
