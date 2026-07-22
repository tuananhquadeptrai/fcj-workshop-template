---
title: "Blog 2"
date: 2026-07-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Ways to Control Web Application Access on AWS with Amazon Cognito

While learning about Amazon Cognito to build an authentication system for my internship project, I found that AWS offers several ways to protect web applications. At first, I was unsure whether to use an ALB, API Gateway, or CloudFront.

Building a login feature alone is not enough. More importantly, only authenticated users should be able to access the system's APIs and resources. Without an appropriate access-control mechanism, APIs may be accessed without authorization and compromise application security.

Instead of building an entire authentication and authorization system from scratch, AWS provides services that simplify the process. Amazon Cognito is a popular option for managing users and supporting modern authentication standards such as OAuth 2.0 and OpenID Connect.

After reviewing the AWS documentation, I identified three common architectural patterns for protecting web applications. Each pattern has its own advantages and suits a different type of system.

## How does OAuth 2.0 work?

All three patterns use Amazon Cognito to authenticate users through OAuth 2.0.

The flow is straightforward:

1. The user accesses the application.
2. The system redirects the user to the Amazon Cognito sign-in page.
3. After a successful sign-in, Cognito issues an access token (or JWT).
4. Requests sent to the backend include this token.
5. An AWS service or the backend validates the token before granting access to a resource.

As a result, the backend does not need to manage the sign-in session itself and can rely on the authentication result from Cognito.

## Pattern 1: Application Load Balancer with Amazon Cognito

This is a simple pattern that is easy to implement for traditional web applications.

In this pattern, the Application Load Balancer (ALB) handles user authentication. When a user first accesses the application, the ALB automatically redirects them to the Amazon Cognito sign-in page. After a successful sign-in, the ALB receives the authentication information from Cognito, creates a session cookie, and maintains the sign-in state.

On subsequent requests, the ALB checks the cookie before forwarding the request to the backend. This means the backend does not need to handle sign-in or token validation.

### Advantages

* Keeps the backend simpler because it does not authenticate users directly.
* Is easy to implement for traditional web applications.
* Reduces the workload on the server.

### Limitations

* Depends on session cookies.
* Is not the optimal choice for API-based systems or mobile applications.

## Pattern 2: Amazon API Gateway with Amazon Cognito

If an application uses a REST API or microservices architecture, API Gateway is often a more suitable choice.

In this pattern, the user signs in with Amazon Cognito to receive an access token. The token is sent in the `Authorization` header with each API call. API Gateway validates the JWT before forwarding the request to the backend.

If the token is invalid or expired, API Gateway rejects the request before it reaches downstream services.

### Advantages

* Works well with REST APIs and HTTP APIs.
* Supports direct JWT authentication.
* Reduces the authentication workload on the backend.
* Scales well as the system evolves toward a microservices architecture.

### Limitations

* Requires the user or client application to manage the access token.
* Can require more initial configuration than the ALB pattern.

## Pattern 3: CloudFront with AWS Lambda

This pattern provides stronger security controls and is often used when an application serves users across multiple regions.

CloudFront receives the user's request first. Before the request is forwarded to the backend, AWS Lambda is invoked to validate the access token.

If the token is valid, CloudFront forwards the request to the Application Load Balancer. CloudFront can also add a custom header so the ALB can verify that the request actually passed through CloudFront, helping prevent direct access to the backend.

### Advantages

* Strengthens security.
* Validates requests at an edge location.
* Combines access control with CloudFront acceleration.

### Limitations

* Is more complex to implement.
* Requires the AWS Lambda function to be deployed in `us-east-1`.
* Costs more and requires more management than the other two patterns.

## Comparing the three patterns

For a traditional website, an Application Load Balancer integrated with Amazon Cognito is usually the simplest option to implement.

For systems that provide APIs to web or mobile applications, API Gateway is a better fit because it validates JWTs directly and integrates well with microservices architectures.

CloudFront with AWS Lambda is suitable for larger systems that need global performance optimization and stronger security controls.

## Conclusion

After exploring these three patterns, I found that AWS provides a comprehensive set of solutions for authentication and access control without requiring teams to build an entire security system from scratch.

Depending on the application's scale and architecture, we can choose an ALB, API Gateway, or CloudFront with AWS Lambda to meet its requirements. Using AWS managed services not only reduces development effort but also improves security, scalability, and system reliability.
