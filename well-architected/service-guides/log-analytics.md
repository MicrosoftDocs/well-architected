---
title: Provides a template for a Well-Architected Framework (WAF) article that is specific to Log Analytics workspaces.
description: Provides a template for a Well-Architected Framework (WAF) article specific to Log Analytics workspaces in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2023
ms.reviewer: bwren
---

# Azure Well-Architected Framework review - Log Analytics
This article provides architectural best practices for [Log Analytics workspaces](/azure/azure-monitor/logs/log-analytics-workspace-overview) in [Azure Monitor](/azure/azure-monitor/overview). The guidance is based on the five pillars of architecture excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency

We assume that you understand system design principles, have working knowledge of Azure Monitor, and are well versed with its features. For more information, see [Azure Monitor](/azure/azure-monitor).

## Reliability
[Reliability](/azure/well-architected/resiliency/overview) refers to the ability of a system to recover from failures and continue to function. Instead of trying to prevent failures altogether in the cloud, the goal is to minimize the effects of a single failing component. Use the following information to minimize failure of your Log Analytics workspaces and to protect the data they collect.

[!INCLUDE [well-architected-framework-logs-reliability](includes/well-architected-framework-logs-reliability.md)]


## Security
[Security](/azure/well-architected/security/overview) is one of the most important aspects of any architecture. Azure Monitor provides features to employ both the principle of least privilege and defense-in-depth. Use the following information to maximize the security of your Log Analytics workspaces and ensure that only authorized users access collected data.

[!INCLUDE [well-architected-framework-logs-security](includes/well-architected-framework-logs-security.md)]


## Cost optimization
[Cost optimization](/azure/well-architected/cost/overview) refers to ways to reduce unnecessary expenses and improve operational efficiencies. You can significantly reduce your cost for Azure Monitor by understanding your different configuration options and opportunities to reduce the amount of data that it collects. See [Azure Monitor Logs cost calculations and options](/azure/azure-monitor/cost-usage) to understand how data charges are calculated for your Log Analytics workspaces.

[!INCLUDE [well-architected-framework-logs-cost](includes/well-architected-framework-logs-cost.md)]


## Operational excellence
[Operational excellence](/azure/well-architected/devops/overview) refers to operations processes required keep a service running reliably in production. Use the following information to minimize the operational requirements for supporting Log Analytics workspaces.

[!INCLUDE [well-architected-framework-logs-operation](includes/well-architected-framework-logs-operation.md)]


## Performance efficiency
[Performance efficiency](/azure/well-architected/scalability/overview) is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. Use the following information to ensure that your Log Analytics workspaces and log queries are configured for maximum performance.

[!INCLUDE [well-architected-framework-logs-performance](includes/well-architected-framework-logs-performance.md)]

## Next step

- [Get best practices for a complete deployment of Azure Monitor](/azure/azure-monitor/best-practices).
