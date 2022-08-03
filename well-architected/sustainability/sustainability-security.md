---
title: Security considerations for sustainable workloads on Azure
description: This design area explores security design considerations for sustainable workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/27/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - networking
products: Azure
ms.custom:
  - sustainability
---

# Security considerations for sustainable workloads on Azure

Designing sustainable workloads on Azure must encompass security, is a foundational principle through all phases of a project.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Cloud native security

### Design Considerations

### Design recommendations

- Right sizing security appliances like Azure Firewall and Web Application Firewall
- Use autoscaling for security appliances
- Use cloud native appliances vs NVAs

## Network architecture

### Design considerations

### Design recommendations

- Use firewalls in Azure rather than utilizing forced tunneling

## Mitigation

### Design considerations

### Design recommendations

- Use DDoS protection to mitigate the compute impact of a successful attack
- Use EDR to identify and shut down crypto mining
- Use AV in passive mode when third party AV apps are used to avoid high CPU usage



## Reporting

### Design considerations

### Design recommendations

- Use AV in passive mode when third party AV apps are used to avoid high CPU usage
- Tag security resources to record emissions impact of security resources
- Analyze cause of spikes in VM compute
- Archive log data to cold storage

## Next step
