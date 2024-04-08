# Orphaned Resources in Azure Resource Manager

## Objective

As a cloud security engineer, I want to identify any orphaned resources in my Azure subscription.

Or to put it more broadly, any resources that aren't fulfilling a useful purpose and may be incurring unneccessary costs.

## Overview

Unused resources in an Azure subscription are typically referred to as "orphaned resources".

These resources may exist for a number of different reasons, but here are a couple of likely explanations.

- A resource has been (intentionally) deleted but a supporting / dependent resource has been left behind
  - E.g. an Azure Virtual Machine is deleted but its managed disk is missed
- A resource is created on an ad hoc basis for testing or troubleshooting
  - It's not used but it's unclear who owns it or why it's there, so it gets left in place "just in case"

It's good practice to identify orphaned resources and remove them, at least on a periodic basis.  

Some of these resources may be incurring costs just by being there (e.g. managed disks or public IP addresses) whilst, more generally, any orphaned resources will clutter the subscription and make it harder to verify that only legitimate (planned) resources are in place.

## Caveats

- This document relies on using Infrastructure as Code for deployment of Azure resources
  - The techniques documented are not directly dependent on IaC but one of its significant benefits is that it establishes a baseline for "what's meant to be there"
    - If you go hunting for orphaned resources in a "free style" subscription where everything is deployed manually or via any other type of unregulated approach, it will be trickier to make an informed decision about what is surplus to requirements and can be safely removed
- The deliberate intent of Azure Resource Graph is to run against everything that the user (account) is authorised to view
  - This means that if you want to explicitly retrict the results returned to a specific subscription, you'll need to hardcode this filter into the query
    - This is what I've done so you can get a working example from my KQL snippets below

## Challenges

_Highlight any factors that will require special consideration / extra work._

## What to Do

```plaintext
Fun fact!  
All the views of resources (e.g. dashboard tiles) in ARM are generated via KQL queries against Resource Graph.  
I didn't realise this until I went deeper into this topic.
```

### Empty Resource Groups

### Resource Groups Not Deployed By IaC

### Resources Not Deployed By IaC

A query that returns resources that _do not_ have the tag value that denotes an IaC deployment.

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
