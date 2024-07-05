---
title: AI workload operations on Azure
description: AI workload operations including deployment on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-ai
---

# AI workload operations on Azure

- End-to-end deployment strategies
- Considerations for user experience monitoring
- Gathering user feedback based on AI model output
- Gathering data on effectiveness of the AI application
- Minimizing downtime
- Optimizing for global redundancy of the entire workload
- RBAC
## Zahra's (SME & area lead) seed material

Workload operations encompass the ongoing activities required to ensure that AI workloads function optimally in a production environment. This involves monitoring and logging to track performance and health, managing infrastructure to support scalable and reliable AI services, and ensuring security and compliance to protect data and adhere to regulations. Additionally, it includes automating operational tasks to enhance efficiency, setting up robust backup and disaster recovery plans, and continuously optimizing performance to meet business goals. Effective workload operations ensure that AI applications remain reliable, secure, and cost-efficient, while also being responsive to changing demands and potential issues.
### Monitoring and Logging

- **Azure Monitor Overview**
  - Overview: [Azure Monitor overview - Azure Monitor | Microsoft Learn](https://learn.microsoft.com/en-us/azure/azure-monitor/overview)
  - Logs and Metrics: [Azure Monitor Logs - Azure Monitor | Microsoft Learn](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/data-platform-logs-metrics)

- **Application Insights for AI**
  - Application Insights: [Application Insights overview - Azure Monitor | Microsoft Learn](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview)

- **Monitoring Azure OpenAI Service**
  - Setting up and configuring diagnostic settings for monitoring Azure OpenAI Service: [Monitoring Azure OpenAI Service](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/monitoring)
### Infrastructure Management
- **Azure Resource Manager (ARM) Templates**
  - ARM Templates: [Azure Resource Manager Templates](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/overview)
  
- **Azure Kubernetes Service (AKS) for AI**
  - AKS Best Practices: [Azure Kubernetes Service Best Practices](https://learn.microsoft.com/en-us/azure/aks/best-practices)
  
- **Azure Functions for Serverless AI**
  - Azure Functions Overview: [Azure Functions Overview](https://learn.microsoft.com/en-us/azure/azure-functions/functions-overview)

### Security and Compliance
- **Azure Policy and Compliance**
  - Azure Policy: [Azure Policy Overview](https://learn.microsoft.com/en-us/azure/governance/policy/overview)
  - Compliance Manager: [Compliance Manager Overview](https://learn.microsoft.com/en-us/microsoft-365/compliance/compliance-manager-overview)
  
- **Secure AI Workspaces**
  - Secure AML Workspace: [Secure AI Workspace](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-secure-workspace-vnet)

### Backup and Disaster Recovery
- **Azure Backup**
  - Azure Backup: [Azure Backup Overview](https://learn.microsoft.com/en-us/azure/backup/backup-overview)
  
- **Disaster Recovery for AI**
  - Azure Site Recovery: [Azure Site Recovery Overview](https://learn.microsoft.com/en-us/azure/site-recovery/site-recovery-overview)

### Performance Optimization
- **Azure Advisor for Optimization**
  - Azure Advisor: [Azure Advisor Overview](https://learn.microsoft.com/en-us/azure/advisor/advisor-overview)
  
- **Optimizing AI Workloads**
  - AI Workloads Optimization: [Manage resources and quotas - Azure Machine Learning](https://learn.microsoft.com/en-us/azure/machine-learning/resource-sku-optimization)
  
- **Azure Well-Architected Framework**
  - Guidelines for designing, building, and optimizing workloads on Azure, including principles for operational excellence: [Azure Well-Architected Framework](https://learn.microsoft.com/en-us/azure/well-architected/)

### Incident Management and Troubleshooting
- **Azure Service Health**
  - Azure Service Health: [Azure Service Health Overview](https://learn.microsoft.com/en-us/azure/service-health/service-health-overview)
  
- **Troubleshooting with Log Analytics**
  - Log Analytics: [Log Analytics Overview](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-overview)
  
- **Understanding Azure OpenAI Service Deployment Types**
  - Overview of deployment options for Azure OpenAI, including standard and provisioned deployments, to manage latency and throughput: [Azure OpenAI Service Deployment Types](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/deployment)

### Automation of Operational Tasks
- **Azure Automation**
  - Azure Automation: [Azure Automation Overview](https://learn.microsoft.com/en-us/azure/automation/automation-intro)
  
- **Azure Logic Apps**
  - Logic Apps Overview: [Logic Apps Overview](https://learn.microsoft.com/en-us/azure/logic-apps/logic-apps-overview)

### Cost Management
- **Azure Cost Management**
  - Cost Management: [Get started with your updated Azure billing account - Microsoft Cost Management](https://learn.microsoft.com/en-us/azure/cost-management-billing/cost-management-billing-overview)
  
- **Budgeting and Forecasting**
  - Budgeting: [Tutorial - Create and manage budgets - Microsoft Cost Management](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/budgets-create)




## Hubert's (SME & area lead) seed material

TODO

## Jose's (Azure Patterns & Practices engineering) seed material

TODO

## Chad's (Azure Patterns & Practices engineering) seed material

TODO
