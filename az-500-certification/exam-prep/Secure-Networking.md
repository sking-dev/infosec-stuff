# Secure Networking

## Exam Preparation

Source: <https://learn.microsoft.com/en-us/shows/exam-readiness-zone/preparing-for-az-500-secure-networking-2-of-4>

## Overview

Secure Networking = 22% of exam marks (20% – 25% according to "Skills measured" in the exam overview)

```plaintext
Skills measured as of October 31, 2023

2.1 Plan and implement security for virtual networks

2.2 Plan and implement security for private access to Azure resources

2.3 Plan and implement security for public access to Azure resources
```

## 2.1 Plan and Implement Security for Virtual Networks

- **Plan and implement Network Security Groups (NSG) and Application Security Groups (ASG)**
  - NSG
    - Layer 4 (packet filter)
    - Any number of rules (subject to subscription limits)
    - Security rule: source / destination / port / protocol
    - Rules evaluated based on five-tuple information
      - 1 - Source
      - 2 - Source port
      - 3 - Destination
      - 4 - Destination port
      - 5 - Protocol
    - NOTE: The default (ARM-generated) rules are applied last
      - These rules cannot be modified
    - Modifying an NSG rule only impacts new connections
  - ASG
    - "A bucket of vNICs"
      - Used to make rules inside NSGs
    - Reuse security policy at scale
      - Without manual maintenance of IP addresses
        - Rules that specify ASG as source or destination are only applied to vNICs that are members of the ASG
    - ASGs have limitations
      - E.g. all vNICS assigned to an ASG must exist in the same vNet as the first vNIC added to that ASG

- **Plan and Implement User-Defined Routes (UDRs)**
  - ARM creates system routes ("behind the scenes")
  - Customers create UDRs which supersede system routes
    - Can specify the following next hops in a UDR
      - Virtual appliance
      - Virtual network gateway
      - Virtual network
      - Internet
      - None
    - NOTE: Cannot specify VNet peering or Virtual Network Service Endpoint as next hop

- **Plan and Implement VNet Peering or VPN Gateway**
  - A VNet peering is between regions
    - A _global VNet peering_ is used for more than one region
  - VNet peering is faster to set up
    - A VPN gateway takes ~ 45 mins
  - A VNet peering is a private connection
  - A VPN gateway gives an _encrypted connection_
    - NOTE: Use in compliance contexts

- **Plan and Implement Virtual WAN including Secured Virtual Hub**
  - Use vWAN to connect to resources in Azure over an IPsec / IKE v1 / IKE v2 VPN connection

- **Secure VPN Connectivity including Point-to-Site and Site-to-Site**
  - Secure hybrid access through Azure AD Application Proxy
    - Can use site-to-site VPN
      - Or Express Route
        - Or point-to-site VPN
  - Secure hybrid access through Azure AD partner integrations
    - Pre-built solutions that support conditional access policies per application
      - E.g. Kemp / Strata
    - On-premises apps authenticate against AAD

- **Configure Firewall Settings on PaaS Resources**
  - Azure native
    - Azure Firewall
      - Layer 7 application filter
    - Web Application Firewall (WAF) in Application Gateway
      - OWASP Top Ten
  - Third party
    - E.g. next generation firewall (NGFW)
  - Express Route can be terminated outside firewall
    - or inside firewall (recommended)

- **Monitor Network Security by Using Network Watcher including NSG Flow Logging**
  - Network Watcher = a network monitoring tool
    - VPN traffic
    - Packet sniffing
    - Next hop (kinda like traceroute)
    - Connectivity (kinda like ping)
  - NSG flow logs = how the traffic is being processed (or not) by the rules in an NSG

## 2.2 Plan and Implement Security for Private Access to Azure Resources

- **Plan and Implement VNet Service Endpoints**
  - "Make services a known entity on a network"
  - Traffic moves across the Azure backbone
    - VNet 1 --> VNet 2
  - NOTE: "The entire Azure service"
    - E.g. applies to _all_ your Azure SQL instances
  - Controllable via _service endpoint policy_

- **Plan and Implement Private Endpoints**
  - NOTE: Use for single instances of a service
    - E.g. private endpoint for SQL
      - Gives a database a private IP on its VNet
        - Then connect to the database via _Private Link_

- **Plan and Implement Private Link Services**
  - REVISE: The steps required to configure an application to run behind a standard Load Balancer then create a Private Link Service and attach it to the LB's front-end IP configuration...

- **Plan and Implement Network Integration for Azure App Service and Azure Functions**
  - Gateway-required VNet integration
    - For traffic to other regions
      - Versus _regional VNet integration_
        - Traffic in the same region

- **Plan and Implement Network security Configurations for App Service Environment (ASE)**
  - REVISE: What is the use case(s) for an ASE?

- **Plan and Implement Network Security Configurations for Azure SQL Managed Instance**
  - REVISE: Confirm the components that are required

## 2.3 Plan and Implement Security for Public Access to Azure Resources

- **Plan and Implement Transport Layer Security (TLS) to Applications including Azure App Service and API Management**
  - TLS protects applications with the following Good Stuff
    - Strong authentication
    - Message privacy and integrity
    - Algorithm flexibility
    - Ease of deployment and use
    - Interoperability between systems
    - Perfect Forward Secrecy (PFS)

- **Plan, Implement, and Manage Azure Firewall including Azure Firewall Manager and Firewall Policies**
  - What can Azure Firewall do?
    - Application FQDN filtering rules
    - Network traffic filtering rules
    - FQDN tags
    - Outbound SNAT
    - Inbound DNAT support
    - NOTE: Installs on a separate firewall subnet
    - Static public IP address
    - Forced tunnelling
      - Push all incoming Internet traffic to a specific next hop
        - E.g. an on-premises device
      - NOTE: This requires the use of UDRs

- **Plan and Implement Azure Application Gateway**
  - Layer 7
  - Regionally based
    - An AGW can only operate per (single) region
  - Can route traffic to the following
    - Virtual Machines
    - On-premises hosts
    - Virtual Machine Scales Sets (VMSS)
    - Azure App Service
    - External app services
  - Can include Web Application Firewall (WAF)
    - Recommended
      - But customers are free to use other third party tools

- **Plan and Implement Azure Front Door including Content Delivery Network (CDN)**
  - NOTE: Layer 7 _global_ routing
    - Can span regions
  - REVISE: What is use case(s)?

- **Plan and Implement Web Application Firewall (WAF)**
  - OWASP Core Rule Sets 3.1 / 3.0 / 2.29
    - "OWASP Top Ten"
  - NOTE: Global WAF policy
  - Supports the following services
    - Azure Front Door
    - Application Gateway
    - CDN (in preview)

- **Recommend When to Use Azure DDoS Protection Standard**
  - Basic service tier is FOC to all customers
    - On an individual subscription basis
  - Standard tier is intended for _multiple subscriptions_
    - Can be managed via _policies_
      - This results in improved visibility across more than one subscription
