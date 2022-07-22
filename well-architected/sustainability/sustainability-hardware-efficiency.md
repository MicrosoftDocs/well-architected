---
title: Hardware efficiency considerations for sustainable workloads on Azure
description: This design area explores considerations for hardware efficient workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/22/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - networking
products: Azure
ms.custom:
  - sustainability
---

# Hardware efficiency considerations for sustainable workloads on Azure

[Intro paragraph]

## Design principles

|Design principle|Pillar|Considerations|
|---|---|---|
|[Autoscale components to optimize utilization](/azure/architecture/best-practices/auto-scaling)|Performance Efficiency|TBD|
|Automate CI/CD to scale worker agents as needed|Performance Efficiency|TBD|
|[Use managed video streaming services that use built-in compression](/azure/media-services/latest/encode-concept)|Performance Efficiency|TBD|
|[Enable storage compression](/azure/cdn/cdn-improve-performance)|Performance Efficiency|TBD|
|[Store only what is relevant. Implement policies to streamline the process](/azure/architecture/guide/design-principles/use-best-data-store)|Performance Efficiency|TBD|
|Ensure software backwards compatibility so it works on legacy hardware|Performance Efficiency|TBD|
|Keep TTL low to avoid idle resource|Performance Efficiency|TBD|
|Update legacy code, identify inefficient legacy code for modernization, make use of serverless, PaaS|Operational Excellence|TBD|
|Move to the cloud! Moving your VMs to the cloud will reduce carbon impact, modernization will further improve|Operational Excellence|TBD|
|Use automation to automatically shut infrastructure or resources down that are not needed to minimize environmental impact of idle resources|Operational Excellence|TBD|
|Classify data and move stale data to cold storage; delete data no longer needed|Operational Excellence|TBD|
|Right sizing security appliances (Azure Firewall, WAF)|Security|TBD|
|Use autoscaling for security appliances|Security|TBD|
|Use cloud native appliances vs NVAs|Security|TBD|
|Use firewalls in Azure rather than utilizing forced tunnelling|Security|TBD|
|Crypto mining - EDR to identify and shut down|Security|TBD|
|Spikes in VM compute - analysis of cause|Security|TBD|
|Smaller failure units|Reliability|TBD|
|Is running active/active a sustainability improvement (reduced network resources from CDNs, smaller failure units, less 'wasted' compute in booting/testing resources in passive regions|Reliability|TBD|
|[Reducing number of recovery points in place](/azure/backup/manage-recovery-points#impact-of-expired-recovery-points-for-items-in-soft-deleted-state)|Reliability|TBD|
|Correct sizing of Azure resources|Cost Optimization|TBD|
|Maximize the workloads on compute|Cost Optimization|TBD|
|Reduce compute - resizing, moving to serverless, rightsizing, spot instances|Cost Optimization|TBD|
|Move to serverless|Cost Optimization|TBD|
|Use spot VMs|Cost Optimization|TBD|
|Use PaaS over IaaS where possible|Cost Optimization|TBD|
|Delete zombie workloads|Cost Optimization|TBD|
|Reduce copies of data or backups where possible|Cost Optimization|TBD|

## Another H2

## More H2

## Next step
