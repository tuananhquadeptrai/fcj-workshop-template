---
title: "Event 3"
date: 2026-06-06
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

{{% notice warning %}}
Note: The information below is for reference purposes only. Please do not copy it verbatim into your report, including this warning.
{{% /notice %}}

# Summary Report: "First Cloud Journey Meetup 06/06/2026"

### Event Overview

The **First Cloud Journey Meetup 06/06/2026** was a community knowledge-sharing event that collected several technical and soft-skill presentations from different speakers. The materials shared in the Google Drive folder covered cloud-native development, cybersecurity, AWS-based game networking, GraphRAG, career growth from IT infrastructure to Cloud/DevOps, and effective teamwork.

### Event Objectives

- Share practical knowledge from community speakers and FCJ members.
- Introduce real-world technical topics related to cloud, AI, security, DevOps, and teamwork.
- Help participants connect different technology domains, from application deployment to security monitoring and AI-powered knowledge systems.
- Encourage self-learning, discussion, and career orientation for students and junior engineers.

### Sessions and Main Topics

#### Docker - A containerization technology

Speaker **Bảo Huỳnh** introduced Docker as a lightweight containerization technology. The session compared virtualization and containerization, explained why virtual machines can be heavy for small applications, and showed how containers package an application with its dependencies so it can run consistently across environments.

Important points from this session included:

- Containers are lighter than virtual machines because they do not require a full guest operating system.
- Docker images and Dockerfiles define how an application environment is built.
- Docker layer caching helps speed up image builds when unchanged layers can be reused.
- Docker is useful for CI/CD pipelines, microservices architectures, development environments, cloud-native applications, and legacy application modernization.

#### Combining AWS WAF with Machine Learning for Cyber Attack Detection on AWS

Speaker **Lê Hoàng Gia Đại** presented a security-focused architecture that combines **AWS WAF** with a **Machine Learning-based Network Intrusion Detection System (NIDS)**. The session explained how AWS WAF protects web applications from common HTTP/HTTPS exploits such as SQL injection, XSS, bot traffic, brute force attacks, and anomalous requests.

The presentation also emphasized that signature-based and rule-based protection is not always enough for modern threats, especially zero-day attacks, hybrid attacks, spoofing techniques, and unusual behavior patterns. To address this, the speaker introduced a machine learning NIDS trained with the **CSE-CIC-IDS2018** dataset.

Key technical highlights:

- AWS WAF can protect CloudFront, Application Load Balancer, API Gateway, and Cognito.
- A NIDS monitors traffic, analyzes behavior, detects attacks, generates alerts, and integrates with firewalls or SIEM systems.
- Data preprocessing is critical: merging CSV files, cleaning invalid labels, handling missing values, removing negative or infinite values, and balancing attack classes.
- The AWS architecture included services such as EC2, Application Load Balancer, AWS WAF, S3, Kinesis Data Firehose, Lambda, Security Hub, GuardDuty, Inspector, SNS, IAM, Config, CloudWatch, and SSM.
- The main lesson was that ML-based detection complements AWS WAF by improving visibility into unknown or evolving attack patterns.

#### Multiplayer in the Cloud: Connecting Godot Clients with AWS WebSockets

Speaker **Nguyễn Quốc Bảo** demonstrated how to connect Godot game clients using **AWS WebSocket APIs**. The session compared several multiplayer networking approaches, including UDP/ENet, WebSocket, and HTTP polling, then explained why WebSocket is suitable for turn-based games, lobbies, chat, and reliable real-time communication.

The AWS architecture included:

- **Amazon API Gateway WebSocket API** for connection handling and route keys.
- **AWS Lambda** with Node.js 20 for connection, disconnection, matchmaking, and message processing logic.
- **Amazon DynamoDB** for storing connection IDs, player status, opponent IDs, choices, and timestamps.
- **Amazon CloudWatch** for logs and monitoring.

The Godot client used `WebSocketPeer` to connect to the WebSocket URL, send JSON messages, poll network events every frame, and react to server responses such as `waiting_for_opponent`, `match_found`, `result`, and `opponent_disconnected`.

The session also discussed challenges such as stale WebSocket connections, DynamoDB scan cost, and the stateless nature of Lambda. For future improvements, the speaker compared the current WebSocket + Lambda approach with AWS GameLift for games that need dedicated servers and persistent in-memory game state.

#### AWS Neptune for Building a Graph Knowledge Base for GraphRAG

Speaker **Việt Phát** presented **GraphRAG** and explained how it can improve traditional Retrieval-Augmented Generation by adding relationship awareness and multi-hop reasoning. The talk introduced how RAG retrieves relevant passages from a knowledge base at runtime and injects them into a prompt, then showed why graph-based retrieval is useful when questions depend on relationships between multiple entities.

Two solution routes were discussed:

- **Fully managed route:** Amazon Bedrock Knowledge Bases handles chunking, entity extraction, embedding generation, and GraphRAG orchestration, while Amazon Neptune Analytics acts as the graph engine for graph storage, relationship discovery, and constructed graph data.
- **Custom route:** A custom processing pipeline such as LlamaIndex prepares data and constructs a knowledge graph, while Amazon Neptune stores the graph and supports multi-hop traversal and Cypher queries.

The main takeaway was that GraphRAG can make AI answers more grounded when the knowledge domain depends heavily on relationships, dependencies, and connected facts.

#### From IT Helpdesk to Senior Sysadmin and Cloud/DevOps

Speaker **Vinh Trần** shared a practical career journey from IT Helpdesk to Senior System Administrator and the first steps toward Cloud/DevOps. The session focused on real experiences, challenges, and self-learning instead of only technical theory.

Important career lessons included:

- Helpdesk experience builds troubleshooting skills, communication with end users, and a problem-solving mindset.
- Learning Linux, networking, and hands-on lab environments can become a turning point toward system administration.
- A system administrator needs to think beyond servers: provisioning, maintenance, networking, patching, monitoring, documentation, and incident prevention.
- Cloud and DevOps introduce a new mindset: AWS, elastic scaling, pay-as-you-go services, Infrastructure as Code with Terraform, version control, CI/CD, Docker, automation, and collaboration with developers.
- Practical experience and real projects are often stronger than certificates alone.

#### The Art of Effective Teamwork

Speaker **Trương Huy Phước** presented the topic of effective teamwork. The session highlighted four golden rules:

- Clear and shared goals.
- Right person, right place.
- Open communication and active listening.
- Personal accountability.

The presentation also introduced digital collaboration tools such as Trello, ClickUp, Google Workspace, Slack, and Discord. This session helped connect technical work with soft skills, reminding participants that successful projects require not only tools and architecture, but also coordination, responsibility, and communication.

### Key Takeaways

#### Technical Knowledge

- Docker helps simplify application packaging and deployment across different environments.
- AWS WAF provides an important security layer, but modern security systems can be strengthened with behavior-based ML detection.
- API Gateway WebSocket, Lambda, and DynamoDB can form a practical serverless architecture for simple multiplayer game flows.
- GraphRAG with Amazon Bedrock and Amazon Neptune is useful for knowledge systems that require relationship-aware retrieval.
- Cloud and DevOps skills build naturally from strong foundations in Linux, networking, automation, and system operations.

#### Soft Skills and Career Growth

- Effective teamwork requires shared goals, clear roles, open communication, and personal accountability.
- Career development is a long-term process built from consistent practice, hands-on labs, projects, and learning from real work.
- Community meetups are valuable because they expose participants to different perspectives in technology, security, AI, game development, infrastructure, and teamwork.

### Applying to My Internship

After joining this meetup, I could connect several topics back to my internship work:

- Use Docker concepts to understand how applications can be packaged and deployed consistently.
- Think more carefully about security monitoring, especially combining preventive controls such as WAF with detection systems such as NIDS.
- Study serverless architecture patterns with API Gateway, Lambda, DynamoDB, and CloudWatch for event-driven application flows.
- Apply GraphRAG thinking when designing AI systems that need relationship-aware knowledge retrieval.
- Improve teamwork by clarifying goals, communicating actively, and taking responsibility for assigned tasks.

### Source Materials

- [Google Drive folder - Meetup 06/06/2026](https://drive.google.com/drive/folders/1EgyIOIjUsgAMugJDcKKQy-AYbWkrHrLX)
- [Bảo Huỳnh - Docker - A containerization technology](https://drive.google.com/drive/folders/1qpRFpmfOwb-AHbrw4APRmiTSpfjZY_Dd)
- [Lê Hoàng Gia Đại - Combining AWS WAF with Machine Learning for Cyber Attack Detection on AWS](https://drive.google.com/drive/folders/1Fkoq-nKJCotiwd6GWsSMsuKcBSPQTXC7)
- [Nguyễn Quốc Bảo - Multiplayer in the Cloud: Connecting Godot Clients with AWS WebSockets](https://drive.google.com/drive/folders/1-wlxBy6Eg5bOEYTjuS8zaB6MYbPRC9yw)
- [Việt Phát - AWS Neptune for Building a Graph Knowledge Base for GraphRAG](https://drive.google.com/drive/folders/10ypP0w7-FU-9V3LchKt8BtFMZygQKAQy)
- [Vinh Trần - From IT Helpdesk to Senior Sysadmin and Cloud/DevOps](https://drive.google.com/drive/folders/14r_vXgYASaiKVLNfyItGhPRFOc1KnMg9)
- [Trương Phước - Cách làm việc nhóm hiệu quả](https://drive.google.com/drive/folders/1MNMCLw125EnCiFSffPqGtrhJCnRM_5Yw)

> Overall, this meetup helped me broaden my view of technology beyond one narrow area. I learned how cloud-native development, security, AI knowledge systems, game networking, infrastructure careers, and teamwork can support each other in real projects.
