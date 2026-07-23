---
title: "Blog 3"
date: 2026-07-23
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# AgentCore Harness: Từ ý tưởng đến một AI Agent hoạt động chỉ với hai API

Để xây dựng một AI Agent, việc gọi mô hình ngôn ngữ thường không phải phần khó nhất.

Phần phức tạp nằm ở việc xây dựng vòng lặp xử lý của agent, kết nối công cụ, quản lý bộ nhớ, duy trì trạng thái phiên làm việc, kiểm soát danh tính, theo dõi hoạt động và triển khai môi trường thực thi an toàn.

Amazon Bedrock AgentCore Harness được AWS xây dựng nhằm đóng gói phần lớn các thành phần này thành một dịch vụ được quản lý. Thay vì phải tự kết nối từng thành phần, developer có thể tập trung nhiều hơn vào logic nghiệp vụ và khả năng của agent.

## Hai API chính

Một agent có thể được đưa vào hoạt động thông qua hai API chính:

- **`CreateHarness`:** Dùng để định nghĩa agent.
- **`InvokeHarness`:** Dùng để gửi yêu cầu và chạy agent.

Với `CreateHarness`, developer có thể khai báo model, system prompt, tools, skills, memory và các giới hạn thực thi. AgentCore sẽ quản lý vòng lặp orchestration phía sau.

Sau đó, ứng dụng chỉ cần sử dụng `InvokeHarness` để gửi yêu cầu của người dùng. Agent sẽ tự phân tích yêu cầu, lựa chọn công cụ phù hợp, thực thi công cụ và trả kết quả về cho ứng dụng.

## Ví dụ: AWS Support Agent

Ví dụ, chúng ta muốn xây dựng một AWS Support Agent có khả năng:

- Đọc log từ CloudWatch.
- Tìm kiếm tài liệu AWS.
- Phân tích nguyên nhân lỗi.
- Đề xuất hướng xử lý.
- Tạo support case khi cần thiết.

Trước đây, đội phát triển có thể phải tự viết agent loop, xử lý tool calling, quản lý session, xây container và triển khai runtime.

Với AgentCore Harness, chúng ta chủ yếu định nghĩa model, hướng dẫn hoạt động và các công cụ mà agent được phép sử dụng. Phần vòng lặp xử lý còn lại sẽ được AWS quản lý.

Mỗi phiên làm việc của agent có thể chạy trong một môi trường cô lập, với filesystem và shell riêng. Điều này cho phép agent đọc và ghi file, chạy lệnh hoặc thực thi code mà không dùng chung môi trường với các phiên khác.

## Kết nối công cụ bằng cấu hình

AgentCore Harness có thể kết nối với nhiều loại công cụ như:

- **AgentCore Gateway** để truy cập API và AWS Lambda.
- **Remote MCP server** để kết nối các công cụ hỗ trợ Model Context Protocol.
- **AgentCore Browser** để thao tác trên trình duyệt.
- **AgentCore Code Interpreter** để thực thi code.
- **Inline Function** cho những bước cần hệ thống bên ngoài hoặc con người phê duyệt.

Các công cụ này có thể được khai báo thông qua cấu hình, giúp giảm lượng glue code mà developer phải tự viết.

Một điểm đáng chú ý khác là model được tách khỏi logic của agent. Developer có thể cấu hình model mặc định cho Harness và lựa chọn model phù hợp cho từng yêu cầu, thay vì gắn toàn bộ hệ thống vào một model duy nhất.

## AgentCore Harness có thay thế AgentCore Runtime không?

Không hẳn.

**AgentCore Harness** phù hợp khi muốn xây dựng agent theo mô hình phổ biến: nhận yêu cầu, suy luận, gọi công cụ và trả về kết quả. Developer ưu tiên tốc độ phát triển và không muốn tự xây dựng toàn bộ orchestration loop.

**AgentCore Runtime** phù hợp hơn khi cần:

- Tự lựa chọn framework cho agent.
- Xây dựng graph hoặc workflow đặc biệt.
- Can thiệp sâu vào quá trình orchestration.
- Sử dụng logic và hook tùy chỉnh.
- Triển khai một kiến trúc không tuân theo agent loop thông thường.

Nói cách khác, Harness ưu tiên tốc độ phát triển và sự đơn giản, trong khi Runtime ưu tiên khả năng tùy chỉnh.

## Kết luận

AgentCore Harness không có nghĩa là xây dựng AI Agent hoàn toàn không cần code. Developer vẫn phải thiết kế system prompt, tools, dữ liệu, IAM role, quyền truy cập và cơ chế kiểm soát hành động của agent.

Tuy nhiên, khi orchestration, memory, môi trường thực thi, identity và observability được cung cấp dưới dạng dịch vụ quản lý, đội phát triển có thể dành nhiều thời gian hơn cho bài toán thực tế.

Thay vì phải tự xây dựng phần “khung xương” của agent, chúng ta có thể bắt đầu với hai API:

- `CreateHarness` để định nghĩa agent.
- `InvokeHarness` để đưa agent vào hoạt động.

Theo mọi người, một managed harness như thế này phù hợp nhất cho việc thử nghiệm nhanh, hay đã đủ khả năng để áp dụng cho các hệ thống production?

**Bài viết chính thức từ AWS:** [Amazon Bedrock AgentCore Harness is now generally available](https://aws.amazon.com/blogs/machine-learning/amazon-bedrock-agentcore-harness-is-now-generally-available-go-from-idea-to-production-grade-agent-in-minutes/)

`#AWS` `#AmazonBedrock` `#AgentCore` `#AgenticAI` `#GenerativeAI`
