---
title: "Event 3"
date: 2026-06-06
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

{{% notice warning %}}
Lưu ý: Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng không sao chép nguyên văn vào bài báo cáo của bạn, kể cả warning này.
{{% /notice %}}

# Bài thu hoạch: "First Cloud Journey Meetup 06/06/2026"

### Tổng quan sự kiện

**First Cloud Journey Meetup 06/06/2026** là một buổi chia sẻ kiến thức cộng đồng, bao gồm nhiều bài trình bày kỹ thuật và kỹ năng mềm từ các speaker khác nhau. Nội dung trong thư mục Google Drive tập trung vào cloud-native development, cybersecurity, game networking trên AWS, GraphRAG, định hướng nghề nghiệp từ IT infrastructure sang Cloud/DevOps và kỹ năng làm việc nhóm hiệu quả.

### Mục tiêu của sự kiện

- Chia sẻ kiến thức thực tế từ các speaker và thành viên cộng đồng First Cloud Journey.
- Giới thiệu nhiều chủ đề liên quan đến cloud, AI, bảo mật, DevOps và teamwork.
- Giúp người tham dự kết nối nhiều mảng công nghệ khác nhau, từ triển khai ứng dụng đến giám sát bảo mật và xây dựng hệ thống AI có tri thức.
- Khuyến khích tinh thần tự học, thảo luận và định hướng nghề nghiệp cho sinh viên hoặc kỹ sư mới bắt đầu.

### Nội dung chính của các bài chia sẻ

#### Docker - A containerization technology

Speaker **Bảo Huỳnh** giới thiệu Docker như một công nghệ containerization giúp đóng gói ứng dụng cùng các dependency cần thiết để ứng dụng có thể chạy nhất quán trên nhiều môi trường khác nhau. Bài chia sẻ so sánh virtualization và containerization, giải thích vì sao virtual machine thường nặng hơn container, đặc biệt khi triển khai các ứng dụng nhỏ hoặc nhiều microservices.

Một số điểm nổi bật:

- Container nhẹ hơn virtual machine vì không cần một hệ điều hành guest đầy đủ cho từng ứng dụng.
- Docker image và Dockerfile mô tả cách xây dựng môi trường chạy ứng dụng.
- Docker layer cache giúp tăng tốc quá trình build image khi các layer không thay đổi.
- Docker phù hợp với CI/CD pipelines, microservices, môi trường development/testing, cloud-native applications và hiện đại hóa ứng dụng cũ.

#### Combining AWS WAF with Machine Learning for Cyber Attack Detection on AWS

Speaker **Lê Hoàng Gia Đại** trình bày kiến trúc bảo mật kết hợp **AWS WAF** với **Machine Learning-based Network Intrusion Detection System (NIDS)**. Bài trình bày giải thích cách AWS WAF bảo vệ website, API và ứng dụng khỏi các web exploit phổ biến ở tầng HTTP/HTTPS như SQL Injection, XSS, bot traffic, brute force và các request bất thường.

Bên cạnh đó, speaker nhấn mạnh rằng cơ chế bảo vệ dựa trên rule hoặc signature không còn đủ trước các tấn công hiện đại như zero-day, hybrid attack, spoofing hoặc các hành vi bất thường chưa có mẫu định nghĩa sẵn. Vì vậy, bài chia sẻ giới thiệu hướng dùng machine learning để xây dựng NIDS, huấn luyện với bộ dữ liệu **CSE-CIC-IDS2018**.

Các nội dung kỹ thuật đáng chú ý:

- AWS WAF có thể bảo vệ CloudFront, Application Load Balancer, API Gateway và Cognito.
- NIDS dùng để giám sát traffic, phân tích hành vi, phát hiện tấn công, cảnh báo real-time và tích hợp với firewall hoặc SIEM.
- Tiền xử lý dữ liệu là bước rất quan trọng: merge nhiều file CSV, làm sạch label lỗi, xử lý missing values, loại bỏ giá trị âm hoặc vô cực và cân bằng các class tấn công.
- Kiến trúc AWS sử dụng các dịch vụ như EC2, Application Load Balancer, AWS WAF, S3, Kinesis Data Firehose, Lambda, Security Hub, GuardDuty, Inspector, SNS, IAM, Config, CloudWatch và SSM.
- Bài học chính là ML-based detection có thể bổ sung cho AWS WAF, giúp tăng khả năng quan sát và phát hiện các mẫu tấn công mới.

#### Multiplayer in the Cloud: Connecting Godot Clients with AWS WebSockets

Speaker **Nguyễn Quốc Bảo** demo cách kết nối các Godot client thông qua **AWS WebSocket APIs**. Bài chia sẻ so sánh một số hướng networking cho multiplayer như UDP/ENet, WebSocket và HTTP polling, sau đó giải thích vì sao WebSocket phù hợp với game turn-based, lobby, chat và các luồng giao tiếp real-time đáng tin cậy.

Kiến trúc AWS gồm:

- **Amazon API Gateway WebSocket API** để quản lý connection và route key.
- **AWS Lambda** dùng Node.js 20 để xử lý connect, disconnect, matchmaking và message logic.
- **Amazon DynamoDB** để lưu connectionId, trạng thái player, opponentId, lựa chọn của người chơi và timestamp.
- **Amazon CloudWatch** để ghi log và giám sát.

Ở phía client, Godot dùng `WebSocketPeer` để kết nối đến WebSocket URL, gửi JSON message, poll network events trong mỗi frame và phản hồi các message từ server như `waiting_for_opponent`, `match_found`, `result` hoặc `opponent_disconnected`.

Bài chia sẻ cũng nêu ra các thách thức như stale connection, chi phí scan DynamoDB và tính stateless của Lambda. Ở phần mở rộng, speaker so sánh mô hình WebSocket + Lambda hiện tại với AWS GameLift cho những game cần dedicated server và persistent in-memory game state.

#### AWS Neptune for Building a Graph Knowledge Base for GraphRAG

Speaker **Việt Phát** trình bày về **GraphRAG** và cách GraphRAG cải thiện Retrieval-Augmented Generation truyền thống bằng khả năng hiểu quan hệ và suy luận nhiều bước. Bài chia sẻ giải thích RAG là kỹ thuật lấy thông tin liên quan từ knowledge base tại runtime rồi đưa vào prompt để LLM tạo câu trả lời có ngữ cảnh hơn.

Hai hướng triển khai được đề cập:

- **Fully managed route:** Amazon Bedrock Knowledge Bases xử lý chunking, entity extraction, embedding generation và GraphRAG orchestration; Amazon Neptune Analytics đóng vai trò graph engine để lưu graph, khám phá relationship và quản lý graph data.
- **Custom route:** Sử dụng pipeline tự xây dựng như LlamaIndex để chuẩn bị dữ liệu và tạo knowledge graph; Amazon Neptune lưu graph, hỗ trợ multi-hop traversal và Cypher query.

Bài học rút ra là GraphRAG đặc biệt hữu ích với các hệ thống tri thức có nhiều quan hệ giữa entity, dependency và connected facts.

#### From IT Helpdesk to Senior Sysadmin and Cloud/DevOps

Speaker **Vinh Trần** chia sẻ hành trình nghề nghiệp thực tế từ IT Helpdesk lên Senior System Administrator và những bước đầu chuyển hướng sang Cloud/DevOps. Nội dung tập trung vào kinh nghiệm làm việc, thử thách, tự học và các bài học thực tế thay vì chỉ nói lý thuyết.

Một số bài học quan trọng:

- Helpdesk giúp rèn luyện khả năng troubleshooting dưới áp lực, giao tiếp với end user và tư duy giải quyết vấn đề.
- Học Linux, Networking và tự xây dựng lab là bước ngoặt để chuyển sang System Administrator.
- Sysadmin cần tư duy rộng hơn việc quản lý server: provisioning, maintenance, networking, patching, monitoring, documentation và phòng tránh incident.
- Cloud và DevOps mở ra mindset mới: AWS, elastic scaling, pay-as-you-go, Infrastructure as Code với Terraform, version control, CI/CD, Docker, automation và collaboration với developer.
- Kinh nghiệm thực tế và project thật thường có giá trị rất lớn bên cạnh certification.

#### The Art of Effective Teamwork

Speaker **Trương Huy Phước** trình bày về nghệ thuật làm việc nhóm hiệu quả. Bài chia sẻ nhấn mạnh 4 nguyên tắc:

- Mục tiêu rõ ràng và được chia sẻ chung.
- Đúng người, đúng vị trí.
- Giao tiếp cởi mở và lắng nghe chủ động.
- Trách nhiệm cá nhân.

Bài trình bày cũng giới thiệu các công cụ hỗ trợ cộng tác như Trello, ClickUp, Google Workspace, Slack và Discord. Đây là phần giúp kết nối kiến thức kỹ thuật với kỹ năng mềm, nhắc nhở rằng một dự án thành công không chỉ cần công nghệ tốt mà còn cần phối hợp, trách nhiệm và giao tiếp hiệu quả.

### Những gì học được

#### Kiến thức kỹ thuật

- Docker giúp đóng gói và triển khai ứng dụng nhất quán trên nhiều môi trường.
- AWS WAF là lớp bảo vệ quan trọng, nhưng hệ thống bảo mật hiện đại nên kết hợp thêm detection dựa trên hành vi và machine learning.
- API Gateway WebSocket, Lambda và DynamoDB có thể tạo thành kiến trúc serverless phù hợp cho các luồng multiplayer đơn giản.
- GraphRAG với Amazon Bedrock và Amazon Neptune hữu ích cho những hệ thống cần truy xuất tri thức dựa trên quan hệ.
- Kỹ năng Cloud/DevOps có thể phát triển từ nền tảng Linux, networking, automation và system operations.

#### Kỹ năng mềm và định hướng nghề nghiệp

- Teamwork hiệu quả cần mục tiêu chung, vai trò rõ ràng, giao tiếp mở và trách nhiệm cá nhân.
- Phát triển nghề nghiệp là một quá trình dài, cần luyện tập đều đặn, làm lab, xây dựng project và học từ công việc thực tế.
- Meetup cộng đồng rất hữu ích vì giúp người tham dự tiếp cận nhiều góc nhìn về công nghệ, bảo mật, AI, game development, infrastructure và teamwork.

### Ứng dụng vào quá trình thực tập

Sau khi tham gia meetup này, em có thể liên hệ các kiến thức học được với quá trình thực tập:

- Sử dụng tư duy Docker để hiểu cách ứng dụng được đóng gói và triển khai ổn định hơn.
- Quan tâm nhiều hơn đến giám sát bảo mật, đặc biệt là kết hợp preventive control như WAF với detection system như NIDS.
- Tìm hiểu thêm về serverless architecture pattern với API Gateway, Lambda, DynamoDB và CloudWatch.
- Áp dụng tư duy GraphRAG khi thiết kế hệ thống AI cần truy xuất tri thức dựa trên quan hệ.
- Cải thiện teamwork bằng cách làm rõ mục tiêu, giao tiếp chủ động và có trách nhiệm với phần việc được giao.

> Tổng thể, meetup này giúp em mở rộng góc nhìn về công nghệ thay vì chỉ tập trung vào một mảng nhỏ. Em hiểu hơn cách cloud-native development, security, AI knowledge systems, game networking, infrastructure career path và teamwork có thể hỗ trợ lẫn nhau trong các dự án thực tế.
