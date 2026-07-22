---
title: "Create the User Pool"
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### Step 1: Open Amazon Cognito

1. Sign in to the AWS Management Console.
2. Change the Region to **Asia Pacific (Singapore) – ap-southeast-1**.
3. Open **Amazon Cognito** and choose **User pools**.
4. Choose **Create user pool**.

#### Step 2: Define the application

1. Select the application type for a mobile or Single Page Application.
2. Name the application `trustbite-mobile-dev`.
3. Select **Email** as the sign-in identifier.
4. Do not create a client secret for a mobile/SPA public client because it cannot protect the secret.

<img src="/images/5-Workshop/5.4-Cognito-user-pool/03-cognito-sign-in.png" alt="Cognito User Pool configured for email and password sign-in" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Checkpoint: the User Pool accepts email and password sign-in; the current lab configuration uses No MFA.</em></p>

#### Step 3: Configure sign-up and confirmation

1. Enable self-service sign-up because TrustBite allows public user registration.
2. Make email a required attribute.
3. Enable Cognito-assisted verification so Cognito sends email confirmation codes.
4. Use a strong password policy: at least 8 characters with uppercase, lowercase, number, and special characters.
5. The illustrated lab configuration uses **No MFA**; production should prefer TOTP, passkeys, or an MFA policy that matches its security requirements.

<img src="/images/5-Workshop/5.4-Cognito-user-pool/04-cognito-sign-up.png" alt="Cognito self-service sign-up and email verification configuration" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Checkpoint: Cognito sends email verification messages automatically and self-service sign-up is enabled.</em></p>

#### Step 4: Create the resources

1. Review the configuration.
2. Choose **Create application** or **Create user pool**, depending on the console experience.
3. Open the new User Pool overview.
4. Record the **User Pool ID**, for example `ap-southeast-1_AbCdEf123`.

<img src="/images/5-Workshop/5.4-Cognito-user-pool/01-cognito-user-pool-overview.png" alt="Cognito User Pool overview in the Asia Pacific Singapore Region" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>The User Pool is available in the Asia Pacific (Singapore) Region.</em></p>

#### Step 5: Verify the configuration

Confirm that:

- Email is the sign-in identifier.
- Self-service sign-up is enabled.
- Email verification is enabled.
- The App Client exists.
- Password and MFA settings match the lab environment (the screenshot currently shows **No MFA**).

<img src="/images/5-Workshop/5.4-Cognito-user-pool/02-cognito-app-clients.png" alt="Cognito App Client list with trustbite-mobile and trustbite-admin-web" style="width: 100%; max-width: 1200px; display: block; margin: 1.5rem auto; border-radius: 6px; box-shadow: 0 4px 20px rgba(0,0,0,0.08);" />

<p style="text-align: center; margin-top: -0.75rem;"><em>Checkpoint: the User Pool contains separate App Clients for the mobile application and admin web portal.</em></p>

{{% notice warning %}}
Enable public self-sign-up only when anyone on the Internet should be able to register. For internal applications, disable it and use an administrative provisioning workflow.
{{% /notice %}}
