# Manage Security Operations

## Exam Preparation

Source: <https://learn.microsoft.com/en-gb/shows/exam-readiness-zone/preparing-for-az-500-manage-security-operations-4-of-4>

## Overview

Manage Security Operations = 25% – 30% (see "Skills measured" in the exam overview)

```plaintext
Skills measured as of October 31, 2023

4.1 Plan, implement, and manage governance for security

4.2 Manage security posture by using Microsoft Defender for Cloud

4.3 Configure and manage threat protection by using Microsoft Defender for Cloud

4.4 Configure and manage security monitoring and automation solutions
```

## 4.1 Plan, Implement, and Manage Governance for Security

- **Create, Assign, and Interpret Security Policies and Initiatives in Azure Policy**
  - "Control how people create resources in Azure"
    - Azure Policy does both governance _and_ security
  - "A policy id a setting"
    - E.g. can only deploy VMs to UK South / a VM can only be a certain SKU / and so forth
  - "An initiative is a collection of policy settings"
    - E.g. (you could create one called) "VM Creation Initiative"
  - Use the built-in _security policy_ definitions
    - _Or_ create custom ones
  - Create in the ARM Portal via the "Security policy" page
  - To assign policies use Portal / PowerShell / Azure CLI
  - The default initiative automatically assigned to every subscription in MS Defender for Cloud is _MS Cloud Security Benchmark_
  - Recommendations are displayed on the "recommendations" page _if_ (your) environment doesn't follow the policies!

- **Configure Security Settings by Using Azure Blueprints**
  - "All about deploying resources for a _subscription_"
  - The following resources are supported as artifacts inside a blueprint
    - 1 - Resource Groups
    - 2 - ARM Template
    - 3 - Policy Assignment
      - NOTE: This is _security_related_
    - 4 - Role Assignment
      - NOTE: This is _security_related_
  - Can use a blueprint for a new sub _or_ to modify an existing sub

- **Deploy Security by Using a Landing Zone**
  - "...talking about _migration_ environments"
  - "Secure landing zone means security controls are in place _before_ resources are deployed"
    - "Plan with security in mind"
  - Adopt IaC + integrate security controls natively into process
  - Deployments of ARM & ALZ Bicep modules require access at tenant root (/) scope
  - ALZ accelerators deploy ARM templates within the Portal experience
    - Pre-provisioned code with tools & controls for security baseline
  - Bicep templates offer the same capabilities as ARM templates
    - Define infrastructure through specified parameters / variables / expressions / modules
      - To deliver ALZ conceptual architecture using modular approach, use ALZ Bicep Modules

- **Create and Configure an Azure Key Vault**
  - What can be stored in a Key vault, eh?
    - Keys
      - "Think key pairs"
    - Secrets
      - Simple secrets e.g. decryption passwords
    - Certificates
      - Enterprise certificate management

- **Recommend When to Use a Dedicated HSM**
  - Only supported by Standard SKU Key Vault
    - _Not_ by basic SKU
  - Can be used to support the following
    - FIPS 140-2 Level-3 compliance
      - E.g. for government organisations
    - Single tenancy of cryptographic storage device
    - Full admin control + sole access to device for admin purposes
    - High application performance
    - Unique cloud-based offerings
  - Best for "lift & shift" scenarios that require direct & sole access to HSM devices
    - _Not fit_ for scenarios such as MS cloud services that support encryption with customer-managed keys
      - Where these keys are not integrated with Dedicated HSM

- **Configure Access to Azure Key Vault**
  - Vault access policies
    - ARM Portal
      - Configure under "Principal" pane
    - Azure CLI
      - `az keyvault set-policy`
    - Azure PowerShell
      - `Set-AzKeyVaultAccessPolicy`
  - RBAC
    - One place to manage all permissions across all Key Vault instances
    - Can set permissions at different scope levels
      - Management group / subscription / resource group / resource
    - Can have separate permissions for individual keys / secrets / certificates

- **Manage Certificates, Secrets, and Keys**
  - Manage certificates
    - X.509 certificate management
      - Key vault policy can manage certificate _lifecycle_
        - Provide contact information for _notifications_
  - Manage secrets
    - Granular isolation of secrets
    - Can store credential information a secret (value)
    - Rotate secrets <= 60 days (see below)
    - Monitor access via Key Vault logging
  - Manage keys
    - Encryption keys
      - Platform-managed _or_ customer-managed
      - Azure Key Vault / Dedicated HSM
        - Manage keys in t' cloud
          - Different use cases
            - E.g. FIPS compliance mandates Dedicated HSM

- **Configure Key Rotation**
  - Can update keys and secrets _without affecting (your) application_
  - Use the following approaches
    - As part of a manual process
    - Programmatically with REST API
    - With an Azure Automation script

- **Configure Backup and Recovery of Secrets, Certificates, and Keys**
  - "Can't backup an entire Key Vault"
    - So have to backup the different entities as required
  - Soft delete
    - "The recycle bin for Key Vault"

## 4.2 Manage Security Posture by Using Microsoft Defender for Cloud

- **Identify and Remediate Security Risks by Using MS Defender for Cloud Secure Score and Inventory**
  - "There's the free version and the paid-for version"
    - Free version offers the following stuff out-the-box
  - Defender for Cloud continually assesses cross-cloud resources for security issues
  - (Your) Secure Score (a percentage value) is an aggregation of all findings that reflect (your) current security situation
  - To increase (your) security
    - Review the recommendations page
    - Remediate by implementing the instructions given
  - NOTE: Other features of MS Defender for Cloud are more proactive so they cost money...

- **Assess Compliance Against Security Frameworks and Microsoft Defender for Cloud**
  - Regulatory compliance dashboard shows overall _compliance score_
    - Plus number of passing versus failing _assessments_ for each standard
      - "It's leveraging Azure Policy initiatives to do this"

- **Add Industry and Regulatory Standards to Microsoft Defender for Cloud**
  - Open the "Security policy" page & select "Add more standards"
    - Add industry standards such as
      - Regulatory standards
      - AWS regulatory standards
      - GCP regulatory standards

- **Add Custom Initiatives to Microsoft Defender for Cloud**
  - Open the "Security policy" page & select "Add a custom initiative"
    - Click "Create new" & configure the policies & parameters

- **Connect Hybrid Cloud and Multi-cloud Environments to Microsoft Defender for Cloud**
  - Connect hybrid cloud environments
    - "Think on-premises"
      - Connect non-Azure computers like so
        - Using Azure Arc-enabled servers
          - Recommended approach
        - From Defender for Cloud's pages in the ARM Portal
  - Connect multi-cloud environments
    - Native cloud connector
      - Recommended approach
    - Use a classic connector

- **Identify and Monitor External-facing Assets**
  - Use Microsoft Defender External Attack Surface Management
    - Monitor Internet-exposed assets with a global network that graphs online relationships!
      - Provides continuous visibility _beyond the firewall_
        - Discovering unmanaged resources
        - Providing multi-cloud visibility
        - Identifying exposed weaknesses
      - Has capabilities such as
        - Real-time inventory
        - Attack surface visibility
        - Exposure detection & prioritisation
        - Integrated threat protection

## 4.3 Configure and Manage Threat Protection by Using Microsoft Defender for Cloud

- **Enable Workload Protection Services in Microsoft Defender for Cloud**
  - Enable _enhanced security_ features on a single _subscription_
    - In Defender for Cloud, go to "Environment settings"
      - Select subscription or workspace to protect
        - Select "Enable all" to enable all the various _plans_
          - E.g. Defender for Key Vault / Defender for Servers / and so on
            - NOTE: These plans cost money!
  - Enable enhanced security on _multiple subscriptions_
    - In Defender for Cloud, go to "Getting started"
      - On "Upgrade" tab, select subscriptions / workspaces as required
        - Select "Upgrade"
  - NOTE: Need Global Administrator / Security Administrator roles to enable enhanced security

- **Configure Microsoft Defender for Servers**
  - Includes automatic, native integration with Microsoft Defender for Endpoint
    - Enable Microsoft Defender for Servers then enable Microsoft Defender for Endpoint unified integration
      - This provisions the Microsoft Defender for Endpoint _agent_ on all supported machines in the subscription
  - Choose from the following
    - Plan 1 - includes Defender for Endpoint integration, automatic provisioning & lower licensing cost
    - Plan 2 - includes everything in Plan 1
      - Plus all other enhanced security features
  - "Protect Linux & Windows VMs in ARM / AWS / GCP / on-premises"

- **Configure Microsoft Defender for Azure SQL Database**
  - Enable Defender for Azure SQL Database at two levels
    - 1 - At the subscription level
      - This is the recommended approach
    - 2 - At the resource level
  - Configure Defender for Azure SQL Database settings
    - From "Security" area of the server or Managed Instance, select "Defender for Cloud"
      - If Defender for Azure SQL Database is enabled, a "Configure" link will be available
        - Click this link to edit the settings!

- **Manage and Respond to Security Alerts in Microsoft Defender for Cloud**
  - Manage security alerts
    - From Defender for Cloud "Overview" page, select "Security alerts"
      - Select the relevant filters
        - Or add filters with the "Add filter" option!
  - Respond to security alerts
    - Select the alert then select "View full details"
      - Use "Alert details" + "Take action" tabs to investigate & take actions
        - E.g. mitigate the threat
