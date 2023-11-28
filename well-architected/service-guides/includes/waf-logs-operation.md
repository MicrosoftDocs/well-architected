---
author: bwren.md
ms.author: bwren
ms.service: azure-monitor
ms.topic: include
ms.date: 08/24/2023
---

### Design checklist

> [!div class="checklist"]
> - Design a workspace architecture with the minimal number of workspaces to meet your business requirements.
> - Use Infrastructure as Code (IaC) when managing multiple workspaces.
> - Use Log Analytics workspace insights to track the health and performance of your Log Analytics workspaces.
> - Create alert rules to be proactively notified of operational issues in the workspace.
> - Ensure that you have a well-defined operational process for data segregation.

### Configuration recommendations

| Recommendation | Benefit |
|:---|:---|
| Design a workspace strategy to meet your business requirements. | See [Design a Log Analytics workspace architecture](/azure/azure-monitor/logs/workspace-design) for guidance on designing a strategy for your Log Analytics workspaces including how many to create and where to place them.<br><br>A single or at least minimal number of workspaces will maximize your operational efficiency since it limits the distribution of your operational and security data, increasing your visibility into potential issues, making patterns easier to identify, and minimizing your maintenance requirements.<br><br>You may have requirements for multiple workspaces such as multiple tenants, or you may need workspaces in multiple regions to support your availability requirements. In these cases, ensure that you have appropriate processes in place to manage this increased complexity. |
| Use Infrastructure as Code (IaC) when managing multiple workspaces. | Use Infrastructure as Code (IaC) to define the details of your workspaces in [ARM](/azure/azure-monitor/logs/resource-manager-workspace), [BICEP](/azure/azure-monitor/logs/resource-manager-workspace), or [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/log_analytics_workspace.html). This allows you to you leverage your existing DevOps processes to deploy new workspaces and [Azure Policy](/azure/azure-monitor//azure/azure-monitor/governance/policy/overview) to enforce their configuration. |
| Use Log Analytics workspace insights to track the health and performance of your Log Analytics workspaces.  | [Log Analytics workspace insights](/azure/azure-monitor/logs/workspace-design) provides a unified view of the usage, performance, health, agents, queries, and change log for all your workspaces. Review this information on a regular basis to track the health and operation of each of your workspaces. |
| Create alert rules to be proactively notified of operational issues in the workspace. | Each workspace has an [operation table](/azure/azure-monitor/logs/monitor-workspace) that logs important activities affecting workspace. Create alert rules based on this table to be proactively notified when an operational issue occurs. You can use [recommended alerts for the workspace](/azure/azure-monitor/logs/log-analytics-workspace-health) to simplify the creation of the most critical alert rules. |
| Ensure that you have a well-defined operational process for data segregation. | You may have different requirements for different types of data stored in your workspace. Make sure that you clearly understand such requirements as data retention and security when [designing your workspace strategy](/azure/azure-monitor/logs/workspace-design) and configuring settings such as [permissions](/azure/azure-monitor/roles-permissions-security) and [archiving](/azure/azure-monitor/logs/data-retention-archive). You should also have a clearly defined process for occasionally [purging](/azure/azure-monitor/logs/personal-data-mgmt#exporting-and-deleting-personal-data) data with personal information that's accidentally collected. |

