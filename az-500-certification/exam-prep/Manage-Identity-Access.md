# Manage Identity and Access

## Exam Preparation

Source: <https://learn.microsoft.com/en-gb/shows/exam-readiness-zone/preparing-for-az-500-manage-identity-and-access-1-of-4>

## Overview

Manage Identity and Access = 28% of exam marks (25% - 30% according to "Skills measured" in the exam overview)

Be aware of Microsoft terminology!

E.g. "Azure Active Directory (AAD") has become "Microsoft Entra ID".

```plaintext
Skills measured as of October 31, 2023

1.1 Manage identities in Microsoft Entra ID

1.2 Manage authentication by using Microsoft Entra ID

1.3 Manage authorization by using Microsoft Entra ID

1.4 Manage application access in Microsoft Entra ID
```

## 1.1 Manage Identities in Microsoft Entra ID

- External identities
  - AAD B2B = "guest users"
  - AAD B2C = "customers"

- Key concepts
  - Conditional access policies for B2B **and** B2C (see 1.3)
    - "Zero Trust Initiative"
  - Multi Factor Authentication (MFA)
  - Identity Protection

- AAD Identity Protection
  - "All about risk"
    - "User risk" vs "sign-in risk"
      - "Who is signing in?" vs "Where is sign-in coming from?"
        - For "Where", consider concepts like "impossible travel"

## 1.2 Manage Authentication by using Microsoft Entra ID

- MFA + conditional access policy
  - Enable AAD MFA for **groups** of users
    - Policy conditions that **prompt** for MFA

- Passwordless Authentication
  - "Something you have + something you are" **or** "something you know"
    - "Have": device / phone / key
    - "Are": biometric
    - "Know": PIN
  - REVISE: What choices are available?

- Implement password protection
  - Supports AAD **and** ADDS
  - REVISE: What steps to implement for on-premises ADDS?

- Implement Single Sign-on (SSO)
  - AAD can act as directory solution for (other) cloud solutions in an organisation's environment
  - (Follows) **industry standards**

- Integrate SSO and identity providers
  - SaaS applications within AAD
  - Etc...

- Authentication and verification methods in AAD
  - "Primary Authentication (PA)"
  - "Secondary Authentication (SA)"
    - "SSPR" = Self Service Password Reset

  | Service                     | PA | SA         |
  |-----------------------------|----|------------|
  | Windows Hello for Business  | Y  | MFA        |
  | Microsoft Authenticator App | Y  | MFA + SSPR |
  | FIDO2 Security Key          | Y  | MFA        |

## 1.3 Manage Authorization by Using Microsoft Entra ID

- Configure Azure role permissions for management groups / subscriptions / resource groups / resources
  - Four main roles
    - Owner
    - Contributor
    - Reader
    - User Access Administrator

- Follow Azure role assignment steps
  - Five steps via ARM Portal or Azure CLI / PowerShell
    - 1 Determine who needs access
    - 2 Select appropriate role
    - 3 Identify needed scope
    - 4 Check prerequisites
    - 5 Assign role

- Create and assign custom roles including Azure and AAD roles
  - Via ARM Portal / PowerShell / Microsoft Graph API

- Implement and manage Microsoft Entra Permissions Management
  - REVISE: What is this?

- Configure AAD Privileged Identity Management (PIM)
  - Directory-based roles + subscription-based roles
  - Time-based **and** approval-based
  - "Active assignment" (permanent) **versus** "Eligible assignment"

- Configure role management and access reviews using Microsoft Entra Identity Governance  
  - REVISE: What is this?
  - NOTE: AAD Premium P2 license required

- Implement conditional access
  - "Identity management is the new control plane"
  - Three main objectives
    - 1 Allow you in
    - 2 Require MFA before you get in
    - 3 Block access if necessary

## 1.4 Manage Application Access in Microsoft Entra ID

- Manage access to Enterprise Applications in AAD
  - App registration
    - Becomes part of the directory

- Configure app registration permission scopes
  - REVISE: Get more details

- Manage app registration permission consent
  - Application permissions
  - How to define them
  - How to grant them
    - Consent flows in the app

- Manage and use service principals
  - Three types
    - 1 Application
    - 2 Managed identity
      - 2.1 System MI
        - Assign to a single resource
      - 2.2 User MI
        - Managed by Azure
          - Assign to multiple resources
    - 3 Legacy
      - For legacy apps

- Recommend when to use and configure authentication for AAD Application Proxy
  - "Think on-premises..."
  - SAML / OpenID --> Kerberos
    - "On-premises integration"
