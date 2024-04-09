# Orphaned Resources in Azure Resource Manager

## Objective

As a cloud security engineer, I want to identify any orphaned resources in my Azure subscription.

Or to put it more broadly, identify any unused resources that aren't fulfilling any useful purpose and may be incurring unneccessary costs.

## Overview

Unused resources within an Azure subscription are typically referred to as "orphaned resources".

These resources may exist for a number of different reasons, but here's a couple of likely explanations.

- A resource has been (intentionally) deleted but a supporting / dependent resource has been left behind
  - E.g. an Azure Virtual Machine is deleted but its managed disk is missed
- A resource is created on an ad hoc basis for testing or troubleshooting
  - It's not used but it's unclear who owns it or why it's there, so it gets left in place "just in case"

It's good practice to identify orphaned resources and remove them, at least on a periodic basis.  

Some of these resources may be incurring costs just by being there (e.g. managed disks or public IP addresses) whilst, more generally, any orphaned resources will clutter the subscription and make it harder to verify that only "official" (business-sanctioned) resources are in place.

To identify these resources, you can use the Azure Resource Graph Explorer service to run Kusto Query Language (KQL) queries against the relevant scope (e.g. the subscription) and the results of these queries can be published to a shared dashboard to give visibility to interested users / groups.

```plaintext
Fun fact!  
All the views of resources (e.g. dashboard tiles) in Azure Resource Manager are generated via KQL queries against Resource Graph.  
I didn't realise this until I went deeper into this topic.
```

## Caveats

- This document leans on the organisation / project team having adopted Infrastructure as Code for the deployment of Azure resources
  - The techniques documented are not directly dependent on IaC, but one of its significant benefits is that it establishes a solid baseline for "what's meant to be there"
    - If you go hunting for orphaned resources in a "free style" subscription where everything is deployed manually or via any other kind of unregulated approach, it will be trickier to make an informed decision about what's surplus to requirements and what can / should be safely removed
- The deliberate intent of Azure Resource Graph is to run against everything that the user (account) context is authorised to view
  - This means that if you want to explicitly retrict the results returned to a specific subscription, you'll need to hardcode this filter into the query
    - This is what I've been doing so you can get a working example from my KQL snippets below
      - Why would this be useful?  Well, if you want to target your queries and dashboards (see below) at a specific subscription, you may want to sidestep the reliance on users selecting this subscription (if it's not already in their default subscription filter)

## Challenges

_Highlight any factors that will require special consideration / extra work._

## What to Do

The first step is to put together the KQL queries for the types of orphaned resources that you want to look for.

### Empty Resource Groups

```kql
// Last updated : 2024-04-09
// List resource groups that do not contain any resources (including hidden types)
// Source: https://github.com/dolevshor/azure-orphan-resources/blob/main/Queries/orphan-resources-queries.md#resource-groups

resourcecontainers
 | where type == "microsoft.resources/subscriptions/resourcegroups"
 | where subscriptionId == "subscription_id_a" // Subscription A
    or  subscriptionId == "subscription_id_b" // Subscription B
 | extend rgAndSub = strcat(resourceGroup, "--", subscriptionId)
 | join kind=leftouter (
     resources
     | extend rgAndSub = strcat(resourceGroup, "--", subscriptionId)
     | summarize count() by rgAndSub
 ) on rgAndSub
 | where isnull(count_)
 | extend Details = pack_all()
 | project ResourceGroup=['id'], subscriptionId, location
 | order by ResourceGroup asc
```

### Resource Groups Not Deployed By IaC

```kql
// Last updated : 2024-04-09
// List resource groups that don't have the "DeployedBy" tag
// The presence of this tag typically indicates a resource deployed via IaC ("Terraform") or the ARM Portal ("Manual") or automatically by Azure Resource Manager when a service instance is first provisioned ("Azure")

resourcecontainers  
| where type == "microsoft.resources/subscriptions/resourcegroups"
| where subscriptionId == "subscription_id_a" // Subscription A"
    or  subscriptionId == "subscription_id_b" // Subscription B
| where name !has "NetworkWatcherRG" // Exclude any RGs created automatically by Azure
    and name !has "dashboards" 
    and name !has "AzureBackupRG_" 
| where tags !contains 'DeployedBy' 
| project ResourceGroup=['id'], subscriptionId, location
```

### Resources Not Deployed By IaC

A query that returns resources that _do not_ have the tag value that denotes an IaC deployment.

```kql
// Last updated : 2024-04-09
// List resources that don't have the "DeployedBy" tag
// The presence of this tag typically indicates a resource deployed via IaC ("Terraform") or the ARM Portal ("Manual") or automatically by Azure Resource Manager when a service instance is provisioned ("Azure")

resources 
| where subscriptionId == "subscription_id_a" // Subscription A"
    or  subscriptionId == "subscription_id_b" // Subscription B
| where type !has "microsoft.compute/virtualmachines/extensions" // Exclude hidden resources
    and type !has "Data collection rule"
    and type !has "Network Watcher"
| where name !contains "AzureBackup_"  // Exclude resources from Azure Backup & ASR
    and name !contains "-ASRReplica"
| where tags !contains "DeployedBy"
| project ['id'], type, resourceGroup, subscriptionId , location
| order by ['type'] asc 
```

## Acknowledgements

_Useful sources of information that may not be credited elsewhere in this document._

### Primary Sources

- <https://github.com/dolevshor/azure-orphan-resources>
  - Can use this Workbook as-is or fillet out the queries that are relevant to the Azure services being used
- <https://learn.microsoft.com/en-us/azure/networking/fundamentals/resource-graph-samples?tabs=azure-cli>

### Secondary Sources

- <https://www.errush.org/blogposts/finding-orphaned-resources/>
  - This is a decent "how to" with some background information on "why"
- <https://www.geeksforgeeks.org/microsoft-azure-handling-orphaned-nsgs/>
