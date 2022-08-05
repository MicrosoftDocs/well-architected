---
title: Application platform considerations for sustainable workloads on Azure
description: This design area explores application platform and infrastructure considerations for sustainable workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/29/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - networking
products: Azure
ms.custom:
  - sustainability
---

# Application platform considerations for sustainable workloads on Azure

[Intro paragraph]

## Operate in the cloud

### Design considerations

### Design recommendations

|Design principle|Pillar|SGF Pillar|Considerations|
|---|---|---|---|
|Move to the cloud|Operational Excellence|Hardware Efficiency|TBD|
|Correct sizing of Azure resources|Cost Optimization|Hardware Efficiency|TBD|
|Delete zombie workloads|Cost Optimization|Hardware Efficiency|TBD|
|Use automation to automatically shut down infrastructure or resources that aren't needed to minimize environmental impact of idle resources|Operational Excellence|Hardware Efficiency|TBD|
|Keep TTL low to avoid idle resource|Performance Efficiency|Hardware Efficiency|TBD|
|[Restartability - amount of processing to recover from failure](/azure/backup/manage-recovery-points#impact-of-expired-recovery-points-for-items-in-soft-deleted-state)|Reliability|Energy Efficiency|TBD|

## Stay updated

### Design considerations

### Design recommendations

|Design principle|Pillar|SGF Pillar|Considerations|
|---|---|---|---|
|Keep OS, language runtimes and libraries up to date for latest efficiency gains|Operational Excellence|Energy Efficiency|TBD|
|Review platform updates regularly, and upgrade to newer/more efficient services as they become available|Operational Excellence|Energy Efficiency|TBD|
|[Update services, OS, software libraries to gain performance efficiencies](/azure/architecture/hybrid/azure-update-mgmt)|Performance Efficiency|Energy Efficiency|TBD|

## Application availability

### Design considerations

### Design recommendations

|Design principle|Pillar|SGF Pillar|
|---|---|---|
|Smaller failure units|Reliability|hardware efficiency|

## Next step

Review the design considerations for deployment and testing.

> [!div class="nextstepaction"]
> [Deployment and testing](sustainability-deployment-testing.md)