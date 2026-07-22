---
title: "Blog 2"
date: 2026-07-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Các cách kiểm soát quyền truy cập ứng dụng Web trên AWS với Amazon Cognito

Trong quá trình tìm hiểu Amazon Cognito để xây dựng hệ thống xác thực cho dự án thực tập, mình thấy AWS có khá nhiều cách để bảo vệ ứng dụng web. Ban đầu mình khá bối rối vì không biết nên dùng ALB, API Gateway hay CloudFront.

Việc xây dựng chức năng đăng nhập thôi là chưa đủ. Điều quan trọng hơn là đảm bảo chỉ những người dùng đã được xác thực mới có thể truy cập vào các API và tài nguyên của hệ thống. Nếu không có cơ chế kiểm soát phù hợp, các API có thể bị truy cập trái phép, gây ảnh hưởng đến tính bảo mật của ứng dụng.

Thay vì tự xây dựng toàn bộ hệ thống xác thực và phân quyền, AWS cung cấp nhiều dịch vụ giúp đơn giản hóa quá trình này. Trong đó, Amazon Cognito là dịch vụ phổ biến để quản lý người dùng và hỗ trợ các chuẩn xác thực hiện đại như OAuth 2.0 và OpenID Connect.

Sau khi tìm hiểu tài liệu của AWS, mình nhận thấy có ba mô hình kiến trúc thường được sử dụng để bảo vệ ứng dụng web. Mỗi mô hình đều có ưu điểm riêng và phù hợp với từng loại hệ thống khác nhau.

## OAuth 2.0 hoạt động như thế nào?

Điểm chung của cả ba mô hình là đều sử dụng Amazon Cognito để xác thực người dùng thông qua OAuth 2.0.

Quy trình hoạt động khá đơn giản:

1. Người dùng truy cập vào ứng dụng.
2. Hệ thống chuyển hướng đến trang đăng nhập của Amazon Cognito.
3. Sau khi đăng nhập thành công, Cognito cấp Access Token (hoặc JWT).
4. Các yêu cầu gửi đến backend sẽ mang theo token này.
5. Dịch vụ AWS hoặc backend sẽ kiểm tra token trước khi cho phép truy cập tài nguyên.

Nhờ vậy, backend không cần tự quản lý phiên đăng nhập mà chỉ cần tin tưởng vào kết quả xác thực từ Cognito.

## Mô hình 1: Application Load Balancer kết hợp Amazon Cognito

Đây là mô hình đơn giản và dễ triển khai đối với các ứng dụng web truyền thống.

Trong mô hình này, Application Load Balancer (ALB) sẽ đảm nhận việc xác thực người dùng. Khi người dùng truy cập ứng dụng lần đầu, ALB sẽ tự động chuyển hướng sang trang đăng nhập của Amazon Cognito. Sau khi đăng nhập thành công, ALB nhận thông tin xác thực từ Cognito, tạo session cookie và lưu trạng thái đăng nhập.

Ở những lần truy cập tiếp theo, ALB chỉ cần kiểm tra cookie trước khi chuyển request đến backend. Điều này giúp backend không phải xử lý việc đăng nhập hay xác minh token.

### Ưu điểm

* Backend đơn giản hơn vì không cần tự xác thực người dùng.
* Dễ triển khai cho các ứng dụng web truyền thống.
* Giảm khối lượng công việc của server.

### Hạn chế

* Phụ thuộc vào session cookie.
* Không phải lựa chọn tối ưu cho các hệ thống API hoặc mobile app.

## Mô hình 2: Amazon API Gateway kết hợp Amazon Cognito

Nếu ứng dụng được xây dựng theo kiến trúc REST API hoặc microservices thì API Gateway thường là lựa chọn phù hợp hơn.

Ở mô hình này, người dùng sẽ đăng nhập với Amazon Cognito để nhận Access Token. Mỗi lần gọi API, token sẽ được gửi trong header `Authorization`. API Gateway sẽ kiểm tra tính hợp lệ của JWT trước khi chuyển request đến backend.

Nếu token không hợp lệ hoặc đã hết hạn, request sẽ bị từ chối ngay tại API Gateway mà không cần đi vào hệ thống phía sau.

### Ưu điểm

* Phù hợp với REST API và HTTP API.
* Hỗ trợ xác thực JWT trực tiếp.
* Giảm tải cho backend.
* Dễ mở rộng khi hệ thống phát triển theo kiến trúc microservices.

### Hạn chế

* Cần người dùng hoặc ứng dụng client tự quản lý Access Token.
* Cấu hình ban đầu có thể phức tạp hơn ALB.

## Mô hình 3: CloudFront kết hợp AWS Lambda

Đây là mô hình có mức bảo mật cao hơn và thường được sử dụng khi ứng dụng phục vụ người dùng ở nhiều khu vực.

CloudFront sẽ nhận request đầu tiên từ người dùng. Trước khi request được chuyển đến backend, AWS Lambda sẽ được kích hoạt để kiểm tra Access Token.

Nếu token hợp lệ, CloudFront tiếp tục chuyển request đến Application Load Balancer. Ngoài ra, CloudFront còn có thể thêm một custom header để ALB biết rằng request thực sự đi qua CloudFront, từ đó hạn chế việc truy cập trực tiếp vào backend.

### Ưu điểm

* Tăng cường bảo mật.
* Kiểm tra request ngay tại Edge Location.
* Kết hợp khả năng tăng tốc truy cập của CloudFront.

### Hạn chế

* Triển khai phức tạp hơn.
* AWS Lambda phải được triển khai tại khu vực `us-east-1`.
* Chi phí và việc quản lý cũng cao hơn hai mô hình còn lại.

## So sánh ba mô hình

Nếu ứng dụng chỉ là một website thông thường, Application Load Balancer kết hợp Amazon Cognito là lựa chọn đơn giản và dễ triển khai nhất.

Đối với các hệ thống cung cấp API cho web hoặc ứng dụng di động, API Gateway sẽ phù hợp hơn nhờ khả năng xác thực JWT trực tiếp và tích hợp tốt với kiến trúc microservices.

Trong khi đó, CloudFront kết hợp AWS Lambda phù hợp với các hệ thống lớn cần vừa tối ưu hiệu năng, vừa tăng cường bảo mật và phục vụ người dùng trên phạm vi toàn cầu.

## Kết luận

Qua việc tìm hiểu ba mô hình trên, mình nhận thấy AWS đã cung cấp khá đầy đủ các giải pháp để xử lý bài toán xác thực và kiểm soát quyền truy cập mà không cần tự xây dựng toàn bộ hệ thống bảo mật từ đầu.

Tùy theo quy mô và kiến trúc của ứng dụng, chúng ta có thể lựa chọn ALB, API Gateway hoặc CloudFront kết hợp AWS Lambda để đáp ứng yêu cầu thực tế. Việc tận dụng các dịch vụ được quản lý bởi AWS không chỉ giúp giảm công sức phát triển mà còn nâng cao tính bảo mật, khả năng mở rộng và độ ổn định của hệ thống.
