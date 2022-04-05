---
title: Microsoft Azure Well-Architected Framework review - Azure Firewall
description: Provides architectural best practices for the Azure Firewall.
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.date: 04/05/2022
---

# Azure Well-Architected Framework review - Azure Firewall

This article provides architectural best practices for Azure Firewall. The guidance is based on the five pillars of architecture excellence:

- [Reliability](#reliability)
- [Security](#security)
- [Cost optimization](#cost-optimization)
- [Operational excellence](#operational-excellence)
- [Performance efficiency](#performance-efficiency)

We assume that you have working knowledge of Azure Firewall and are well versed with its features. For more information, see [Azure Firewall Standard features](/azure/firewall/features).

## Prerequisites

- Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.
- Use a reference architecture to review the considerations based on the guidance provided in this article. We recommend that you start with [Ref 1](/azure/architecture/reference-architectures) and [Ref 2](/azure/architecture).

## Reliability

In the cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Use the following information to minimize failed instances.

### Design checklist

As you make design choices for Azure Firewall, review the [Reliability design principles](../../resiliency/principles.md).

> [!div class="checklist"]
> - XXXXX

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
| XXX| XXX|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Security

Security is one of the most important aspects of any architecture. Azure Firewall provides features to employ both the principle of least privilege and defense-in-defense. We recommend you review the [Security design principles](../../security/security-principles.md).

### Design checklist

> [!div class="checklist"]
> - XXX

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for Security.

| Recommendation | Benefit |
|--------|----|
| XXX |XXX|

For more suggestions, see [Principles of the security pillar](/azure/architecture/framework/security/security-principles).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

- XXX

All built-in policy definitions related to Azure Networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. We recommend you review the [Cost optimization design principles](../../cost/principles.md).

### Design checklist

> [!div class="checklist"]
> - XXX

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for Cost optimization.

| Recommendation | Benefit |
|--------|----|
| XXX | XXX|

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Operational excellence

Monitoring and diagnostics are crucial. Not only can you measure performance statistics but also use metrics troubleshoot and remediate issues quickly. We recommend you review the [Operational excellence design principles](../../devops/principles.md).

### Design checklist

> [!div class="checklist"]
> - XXX

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for Operational excellence.

| Recommendation | Benefit |
|--------|----|
| XXX| XXX |

For more suggestions, see [Principles of the operational excellence pillar](/azure/architecture/framework/devops/principles).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. We recommend you review the [Performance efficiency principles](../../scalability/principles.md).

### Design checklist

> [!div class="checklist"]
> XXX

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for Performance efficiency.

| Recommendation | Benefit |
|--------|----|
| XXX | XXX|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Azure Firewall.

### Reliability

- XXX

## Additional resources

### Azure Architecture Center guidance

- XXX

## Next steps

- Deploy an Azure Firewall to see how it works: [Tutorial: Deploy and configure Azure Firewall and policy using the Azure portal](/azure/firewall/tutorial-firewall-deploy-portal-policy)
