# Secure Compute, Storage, and Databases

## Exam Preparation

Source: <https://learn.microsoft.com/en-gb/shows/exam-readiness-zone/preparing-for-az-500-secure-compute-storage-and-databases-3-of-4/>

## Overview

Secure Compute, Storage, and Databases = 20% – 25% (see "Skills measured" in the exam overview)

```plaintext
Skills measured as of October 31, 2023

3.1 Plan and implement advanced security for compute

3.2 Plan and implement security for storage

3.3 Plan and implement security for Azure SQL Database and SQL Managed Instance
```

## 3.1 Plan and Implement Advanced Security for Compute

- **Plan and Implement Remote Access to Public Endpoints Including Azure Bastion**
  - The benefits of Azure Bastions service
    - Remote session over TLS
    - Firewall traversal for RDP / SSH
    - No public IP required for the target VM
  - Bastion is available in two SKUs
    - Basic
    - Standard
  - Avoid exposing RDP / SSH ports over t' Internet
    - Deploy bastion hosts on the public side of the perimeter network
  - Bastion understands VNet peering!

- **Configure Network Isolation for Azure Kubernetes Service (AKS)**
  - Cluster isolation can be configured in two ways
    - 1 - Logical isolation
      - High pod density
      - Additional security features to block exploits
        - E.g. Kubernetes RBAC for nodes
      - Only trust hypervisor when running hostile multi-tenant workloads
    - 2 - Physical isolation
      - Low pod density
      - Adds _management and financial overhead_
        - Need to run more AKS hosts
      - Use _only_ for hostile multi-tenant workloads
        - For other scenarios, it's recommended to use logical isolation

- **Secure and Monitor AKS**
  - Use Microsoft Defender for Containers to protect AKS
    - Environment hardening
      - Asses cluster continuously
        - Visibility into misconfigurations
        - Guidelines to help mitigate identified threats
  - Vulnerability assessment
    - Vulnerability assessment & management tools are stored in Azure Container Registries (ACRs) & run in AKS
  - Run-time threat protection for nodes & clusters
    - Threat protection for clusters & Linux nodes
      - Generate security alerts for suspicious activities

- **Configure Authentication for AKS**
  - AAD authentication for AKS clusters with OpenID Connect
  - AKS-managed AAD Integration on existing Kubernetes RBAC-enabled clusters
  - Upgrade to _AKS-managed AAD Integration_ if using legacy AAD integration
  - Use _kubelogin_ to access cluster
    - Non-interactive service principal sign-in
  - Use Conditional Access to control access whilst integrating AAD with AKS cluster
  - Use PIM for JIT requests for cluster access control
  - There are limitations!
    - Think in terms of "design decisions"
      - You can disable AKS-managed AAD Integration
      - You can't change an AKS-managed AAD cluster to legacy AAD
      - AKS-managed AAD integration does not support clusters that are not Kubernetes RBAC-enabled

- **Configure Security Monitoring for Azure Container Instances (ACIs)**
  - Azure Monitor provides the following metrics for container groups & individual containers
    - CPU usage (millicores)
    - Memory usage (bytes)
    - Network bytes received (per sec)
    - Network bytes transmitted (per sec)
  - Gather Azure Monitor metrics using the following methods
    - ARM Portal
      - Visit "Overview" page for the container group
    - Azure CLI
      - Get the container group ID via `az container show`
  - NOTE: Currently Azure Monitor metrics are only available for Linux containers

- **Configure Security Monitoring for Azure Container Apps (ACAs)**
  - Monitor & scan container images
    - IMPORTANT: Solutions to scan container images in a private registry (e.g. ACR) & identify potential vulnerabilities
      - Solutions include MS Defender for Cloud integrated Qualys scanner
        - Also Twistlock
          - And Aqua Security
  - Monitor container activity & access
    - To identify suspicious or malicious activity
      - Via _continuous monitoring_
    - Use container monitoring solutions provided by Azure
      - E.g. Container Insights
  - Monitor container resource activity
    - E.g. files & other resources accessed by containers
    - Use Azure Monitor to collect metrics / activity logs / diagnostic logs
    - Review metrics for performance statistics for different resources
      - And for the OS inside the VM
        - TODO: Check what this means

- **Manage Access to ACR**
  - What is ACR?
    - A Docker registry service
    - Private & hosted in Azure
    - Build - store - manage images
    - Push & pull with Docker CLI or Azure CLI
  - Access with AAD
  - Use RBAC to assign permissions
  - Automate using Azure DevOps

- **Configure Disk Encryption Including Azure Disk Encryption (ADE), Encryption as Host, and Confidential Disk Encryption**
  - "Everything at rest in Azure is encrypted in one way or another"
    - This is also known as "Storage Service Encryption (SSE)"
  - ADE sits on top of SSE
    - It encrypts the OS
      - BitLocker for Windows
      - dm-crypt for Linux
  - Keys / secrets can be stored in customer Key vault & retrieved by VM e.g. on boot

- **Recommend Security Configurations for Azure API Management**
  - "What APIs are allowed to use our environments?"
  - Use the Azure security baseline for API management
    - <https://learn.microsoft.com/en-us/security/benchmark/azure/baselines/api-management-security-baseline>
      - Network Security
        - NS-1
        - NS-2
      - Identity Management
        - IM-1
        - IM-3
      - Privileged Access
        - PA-1
        - PA-7
      - Data Protection
        - DP-1
        - DP-2
      - Asset Management
        - AM-2
      - Logging & Threat Detection
        - LT-1
        - LT-4
      - Backup & Recovery
        - BR-1

## 3.2 Plan and Implement Security for Storage

- **Configure Access Control forStorage accounts**
  - "Know this table!"
    - <https://learn.microsoft.com/en-us/azure/storage/common/authorize-data-access>
      - Just need to know Azure Blobs /  Azure Files (SMB) / Azure Files (REST)
        - The latter is the service for API access
  - "How do we access our different storage environments?"
    - Access & authorisation
      - Can include anonymous
        - NOTE: Anonymous for Blobs only
  - "Every storage request must be authorised!"
  - Storage Account Key versus Shared Access Signature
    - SAK for everything in a SA
    - SAS for a specific resource within an SA

- **Manage Lifecycle for Storage Account Access Keys**
  - Management plane
    - "Functional... for building stuff"
      - Creating resources in Azure
  - Data plane
    - Access things inside resources directly when storage related
      - NOTE: Apps require this level of access to work with SA Keys

- **Select and Configure an Appropriate Method for Access to Azure Files**
  - Identity-based authentication for Windows file shares over SMB via the following methods
    - NOTE: Only one method allowed per storage account
      - On-premises AD DS authentication
        - On-premises AD DS-joined or AAD DS-joined
      - AAD DS Authentication
        - Cloud-based AAD DS-joined Windows VMs
      - AAD Kerberos for hybrid identities
        - Via the Internet from hybrid AAD-joined VMs

- **Select and Configure an Appropriate Method for Access to Azure Blob Storage**
  - Ensure (you have) specific permissions to authorise the access
  - Authenticate with account access key _or_ AAD account
    - Can switch method when uploading blob to SA
      - When using the ARM Portal, that is

- **Select and Configure an Appropriate Method for Access to Azure Tables**
  - "Think of (Azure Tables as) no SQL database integration points"
  - Access to a table is a two step process
    - 1 - Identity authenticated
      - OAuth 2.0 token returned
    - 2 - Token passed to Tables service
      - Used to authorise the access
  - Use Azure built-in roles
    - Or define custom roles to access table data
  - Resource scope
        - Recommended to grant only the narrowest possible scope
  - Built-in roles include
    - Storage Table Data Contributor
    - Storage Table Data Reader
  - NOTE: Only roles explicitly defined for data access will allow an SPN to access table data

- **Select and configure an Appropriate method for Access to Azure Queues**
  - Authenticate with account access key _or_ AAD user account
    - NOTE: AAD user account (also) covers SPNs & Managed Identities

- **Select and Configure Appropriate Methods for Protecting Against Data Security Threats**
  - Basic data protection
    - Enable container soft delete
    - Save blob state at regular intervals
    - Use ARM lock on SA to protect against deletion / configuration changes
  - Data protection options in Azure storage
    - ARM lock
    - Container soft delete
    - Immutability policy on container
    - Point-in-time restore
    - Immutability policy on blob version
    - Blob versioning
    - Blob snapshot
    - Blob soft delete
    - Roll-your-own solution for copying data to a second SA

- **Configure Bring Your Own Key (BYOK)**
  - Bring your own custom-managed key
    - Versus using a Microsoft-managed key
  - A customer-managed key must be stored in Azure Key Vault!

## 3.3 Plan and Implement Security for Azure SQL Database and SQL Managed Instance

- **Enable Database Authentication by Using Azure Active Directory, Part of Microsoft Entra**
  - There's SQL Authentication (local username + password)
  - And then there's AAD authentication
    - To use as an alternative to SQL authentication
  - Helps stop proliferation of users IDs across database servers
  - Helps to support SSO
  - Allows password rotation in a single place
  - Customers can manage database permissions using external (AAD) groups

- **Enable Database Auditing**
  - Retain audit trail of selected events
  - Report on database activity & analyse results
  - Configure policies for server _or_ per database
  - Configure audit log destination
  - NOTE: A new server policy applies to all existing & newly created databases

- **Plan and Implement Dynamic Data Masking**
  - Masks sensitive data for non-privileged users
    - E.g. credit card numbers / telephone numbers
      - Only see the last four digits
  - Administrators are excluded
    - Other exclusions can be added
  - Rules are used to apply the masking logic
    - Several formats are available
