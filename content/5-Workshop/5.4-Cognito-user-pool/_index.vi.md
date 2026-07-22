---
title: "Tạo Cognito User Pool"
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Mục tiêu

Trong phần này, chúng ta tạo một Cognito User Pool cho TrustBite, bật xác nhận email, tạo public App Client và cấu hình Managed Login với Authorization Code + PKCE.

#### Nội dung

1. [Tạo User Pool](5.3.1-create-user-pool/)
2. [Cấu hình App Client và Managed Login](5.3.2-configure-app-client/)

#### Tài nguyên sẽ tạo

| Tài nguyên | Tên mẫu | Mục đích |
| --- | --- | --- |
| User Pool | `trustbite-users-dev` | Lưu và xác thực người dùng TrustBite |
| App Client | `trustbite-mobile-dev` | Cho phép ứng dụng mobile/web thực hiện OIDC flow |
| Cognito domain | Tên duy nhất do bạn chọn | Cung cấp authorization, token và Managed Login endpoints |

{{% notice note %}}
User Pool là nơi quản lý người dùng; App Client đại diện cho một ứng dụng sử dụng User Pool. Không nhầm User Pool ID với App Client ID.
{{% /notice %}}
