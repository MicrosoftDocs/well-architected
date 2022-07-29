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

## Design for energy efficiency

By making your applications and infrastructure more energy efficient, you can reduce the amount of carbon emitted into the atmosphere.

### Design considerations

### Design recommendations

- Use DDoS protection to mitigate the compute impact of a successful attack
- To avoid high CPU usage, consider using antivirus in passive mode when third-party antivirus apps are used.

## Improving hardware efficiency

### Design Considerations

- Azure Firewall is a cloud-native and intelligent network firewall security service that provides the best of breed threat protection for your cloud workloads running in Azure.
- Azure Web Application Firewall is a cloud-native service that protects your web apps from common web-hacking techniques, including SQL injection and security vulnerabilities like cross-site scripting.
- Deploying an Azure Firewall or Azure Web Application Firewall on the wrong tier may result in unnecessary cost and waste of energy.
- Automatically scaling security appliances in Azure to fit the current workload will help reduce emissions, and ultimately improve hardware efficiency.

### Design recommendations

- Right sizing security appliances like Azure Firewall and Web Application Firewall
- Use autoscaling for security appliances
- Use cloud native appliances vs NVAs
- Use firewalls in Azure rather than utilizing forced tunneling
- Crypto mining - EDR to identify and shut down
- Spikes in VM compute - analysis of the cause

## Measuring sustainability

### Design considerations

### Design recommendations

- Tag security resources to record emissions impact of security resources

## Next step
