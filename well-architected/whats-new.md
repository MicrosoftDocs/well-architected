---
title: What's new in the Azure Well-Architected Framework
description: Find out about recent updates and new documentation in the Azure Well-Architected Framework.
author: ckittel
ms.author: chkittel
ms.date: 02/05/2025
ms.topic: whats-new
---

# What's new in the Azure Well-Architected Framework

Find out about recent changes in the Azure Well-Architected Framework.

## January 2025

### Updated articles

- [How to use the Azure Well-Architected Framework documentation](.//what-is-well-architected-framework.md): We added a new section on adopting a phased learning process, emphasizing the importance of improving workload quality iteratively.

#### Service guides

This month, we made significant updates to many of our service guides. Here are some highlights:

- [Azure App Service (Web Apps)](./service-guides/app-service-web-apps.md): We refreshed and expanded this guide to cover all Well-Architected pillars. We also introduced new design considerations and recommendations, including guidance on redundancy, scaling strategies, health checks, managed identities, network security, cost monitoring, and deployment best practices.
- [Azure Functions](./service-guides/azure-functions-security.md): We refreshed and expanded this guide to cover all Well-Architected pillars. We also introduced new design considerations and recommendations, including guidance on the use of managed identities, network security controls, cost monitoring, CI/CD pipelines, and autoscaling.
- [Azure Kubernetes Service (AKS)](./service-guides/azure-kubernetes-service.md): We refreshed this guide to improve the structure and presentation of the content, and to reflect the latest best practices for configuring Azure Kubernetes Service (AKS) to protect your workloads and data, optimize costs, and improve operational efficiency.
- [Azure Load Balancer](./service-guides/azure-load-balancer.md): We refreshed and expanded this guide to cover all Well-Architected pillars. We also introduced new design considerations and recommendations, including guidance on optimizing your load balancing configurations, using the Global tier to load balance across Azure regions, and how to improve operational efficiency.
- [Azure Service Fabric](./service-guides/azure-service-fabric-well-architected-framework.md): We refreshed this guide to improve the structure and presentation of the content and introduced new guidance to help you optimize your Service Fabric workloads.
- [Azure Traffic Manager](./service-guides/azure-traffic-manager.md): We refreshed and expanded this guide to cover all Well-Architected pillars. We also introduced new design considerations and recommendations, including guidance on load balancing across regions, enhancing DNS security, and using diagnostic logs and traffic view dashboards to optimize and troubleshoot Traffic Manager profiles.
- [Azure Virtual Network](./service-guides/virtual-network.md): We refreshed and expanded this guide to cover all Well-Architected pillars. We also introduced new design considerations and recommendations, including guidance on adding redundancy, segmenting networks for security, and monitoring traffic patterns. Use infrastructure as code in your deployments, optimize network traffic, and leverage Azure Network Watcher for performance insights.

## December 2024

### New articles

- [Azure Well-Architected Framework Perspective on Azure Disk Storage](./service-guides/azure-disk-storage.md): Find new design considerations and configuration recommendations for optimizing Azure Disk Storage within the Azure Well-Architected Framework. Explore guidance on key areas such as reliability, security, cost optimization, operational excellence, and performance efficiency, as well as strategies and best practices to enhance storage management for Azure Virtual Machines.

### Azure IoT Hub workload guidance retirement

- This month, we announced the deprecation of Azure IoT Hub workload documentation in the repo. The content was outdated and no longer aligned to the Azure Well-Architected Framework.

## November 2024

### New articles

- [Well-Architected Framework Perspective on Azure Monitor Application Insights](./service-guides/application-insights.md): Explore design considerations and configuration recommendations for Azure Monitor Application Insights. Azure Monitor Application Insights is an extensible Application Performance Management (APM) service that helps you monitor the performance and usage of your live web applications. It provides real-time insights into your application's performance and user behavior, enabling you to detect and diagnose issues and understand what users actually do with your app.

#### New workload: AI on Azure

This month, we introduced new guidance for designing AI workloads on Azure. This documentation is appropriate for roles that are accountable for designing, building, and maintaining a solution for running AI workloads in a cloud environment. Use the [AI Workloads on Azure](./ai/index.yml) documentation as your go-to resource to build and optimize AI solutions on Azure.

- [AI Workloads on Azure](./ai/get-started.md)
- [Design Methodology for AI Workloads on Azure](./ai/design-methodology.md)
- [Design Principles for AI Workloads on Azure](./ai/design-principles.md)
- [Application Design for AI Workloads on Azure](./ai/application-design.md)
- [Application Platform for AI Workloads on Azure](./ai/application-platform.md)
- [Design Training Data for AI Workloads on Azure](./ai/training-data-design.md)
- [Grounding Data Design for AI Workloads on Azure](./ai/grounding-data-design.md)
- [Data Platform for AI Workloads on Azure](./ai/data-platform.md)
- [MLOps and GenAIOps for AI Workloads on Azure](./ai/mlops-genaiops.md)
- [AI Workload Operations on Azure](./ai/operations.md)
- [Test and Evaluate AI Workloads on Azure](./ai/test.md)
- [Responsible AI in Azure Workloads](./ai/responsible-ai.md)
- [Workload Team Personas Involved in AI Workloads](./ai/personas.md)
- [AI Workload Assessment](./ai/assessment.md)

#### New workload: Software as a service (SaaS) on Azure

This month, we added a new workload for SaaS on Azure. This documentation provides actionable and authoritative guidance that applies Well-Architected best practices as the technical foundation for building and operating a SaaS solution on Azure at-scale. Use the [SaaS Workloads on Azure](./saas/index.yml) documentation to build scalable, performant, reliable, and secure SaaS solutions.

- [SaaS Workloads](./saas/get-started.md)
- [Design Methodology for SaaS Workloads on Azure](./saas/design-methodology.md)
- [Design Principles of SaaS Workloads on Azure](./saas/design-principles.md)
- [Billing and Cost Management for SaaS Workloads on Azure](./saas/billing-cost-management.md)
- [Governance for SaaS Workloads on Azure](./saas/governance.md)
- [Resource Organization for SaaS Workloads on Azure](./saas/resource-organization.md)
- [Identity and Access Management for SaaS Workloads on Azure](./saas/identity-access.md)
- [Compute for SaaS Workloads on Azure](./saas/compute.md)
- [Networking for SaaS Workloads on Azure](./saas/networking.md)
- [Data for SaaS Workloads on Azure](./saas/data.md)
- [DevOps Practices for SaaS Workloads on Azure](./saas/devops.md)
- [Incident Management for SaaS Workloads on Azure](./saas/incident-management.md)
- [Assessment Review Tool for SaaS Workloads on Azure](./saas/assessment.md)

## October 2024

### Updated articles

- [Architecture decision record (ADR)](./architect-role/architecture-decision-record.md): We refreshed the guidance on what an ADR should include, including consistent elements like problem statements, options considered, and decision outcomes. Explore updates including a new section on suggested characteristics of an individual record with guidelines for maintaining consistent and useful ADRs

- [Azure Well-Architected Framework perspective on Azure Application Gateway v2](./service-guides/azure-application-gateway.md): We made significant updates to the guidance about Azure Application Gateway v2. Find important notes and links to additional resources for Azure Application Gateway configurations. Explore enhanced content with specific design principles, strategies, and recommendations for achieving architectural goals.

- [Azure Well-Architected Framework perspective on Azure ExpressRoute](./service-guides/azure-expressroute.md): We made significant updates to provide more comprehensive and structured guidance on Azure ExpressRoute, enhancing and expanding upon best practices, design principles, and optimization strategies. The detailed checklists and recommendations help in better planning and implementation, ensuring improved reliability, security, and cost efficiency.

We reviewed all tradeoff and design pattern articles for alignment with the content structure and to ensure that the guidance is up to date. Tradeoffs are an essential part of the Well-Architected Framework, as they help you understand the implications of design decisions on other pillars. Design patterns are reusable solutions to common problems that you might encounter when designing a workload. They help you understand how to design your workload to meet the goals of the Well-Architected Framework. Check out the updated articles:

- [Cloud design patterns that support cost optimization](./cost-optimization/design-patterns.md)
- [Cost Optimization tradeoffs](./cost-optimization/tradeoffs.md)
- [Cloud design patterns that support reliability](./reliability/design-patterns.md)
- [Reliability tradeoffs](./reliability/tradeoffs.md)
- [Cloud design patterns that support security](./security/design-patterns.md)
- [Security tradeoffs](./security/tradeoffs.md)
- [Cloud design patterns that support operational excellence](./operational-excellence/design-patterns.md)
- [Operational Excellence tradeoffs](./operational-excellence/tradeoffs.md)
- [Cloud design patterns that support performance efficiency](./performance-efficiency/design-patterns.md)
- [Performance Efficiency tradeoffs](./performance-efficiency/tradeoffs.md)

## September 2024

### New articles

- [Azure Well-Architected Framework perspective on Azure NetApp Files](./service-guides/azure-netapp-files.md): Explore design considerations and configuration recommendations for Azure NetApp Files. Azure NetApp Files is a fully managed file share service that supports the Server Message Block (SMB) protocol and Network File System (NFS) protocol. Learn how you can use Azure NetApp Files for file sharing, high-performance computing, home directories, and databases.

### Updated articles

- [Azure Well-Architected Framework perspective on Azure Firewall](./service-guides/azure-firewall.md): We made significant updates to the guidance about Azure Firewall. Explore the fully updated content including new recommendations for configuring Azure Firewall to protect your workloads.

## August 2024

### Updated articles

- [Recommendations for defining reliability targets](./reliability/metrics.md): We made significant updates, including new guidance on composite SLOs. The updated content links to guidance about how to focus on realistic expectations and build a health model to define system states.

We added guidance about ensuring that the VMs in your backup environment run on [OSs that have supportability](/azure/virtual-machines/enable-nvme-interface):

- [Operations considerations for Azure VMware Solution workloads](azure-vmware/operations.md)
- [Optimize business continuity and disaster recovery](oracle-iaas/optimize-business-continuity-disaster-recovery.md)

### Hybrid retirement

- This month, we announced the deprecation of Hybrid documentation in the repo. The content was outdated and no longer aligned to the Azure Well-Architected Framework.

## July 2024

### New articles

- [Azure Well-Architected Framework perspective on Azure Local](./service-guides/azure-local.md): Explore design considerations and configuration recommendations for Azure Local. Azure Local is a hyperconverged infrastructure (HCI) solution that hosts virtualized Windows and Linux workloads and their storage in a hybrid on-premises environment. Learn how you can use Azure Local and Azure Arc capabilities to keep business systems and application data on-premises to address data sovereignty, regulation and compliance, and latency requirements.

### Updated articles

- [Workload architecture design specification](./architect-role/architecture-design-specification.md): Learn about best practices for creating functional and technical specifications and the importance of consistency in your workload design documentation.
- [Architecture design diagrams](./architect-role/design-diagrams.md): We updated the guidance to include Microsoft Entra ID architecture icons.

Find updated guidance on using flexible virtual machine scale sets instead of availability sets for deployment across multiple zones:

- [Application platform considerations for mission-critical workloads on Azure](./mission-critical/mission-critical-application-platform.md)
- [Application delivery considerations for Azure Virtual Desktop workloads](./azure-virtual-desktop/application-delivery.md)

### Carrier Grade retirement

- This month, we announced the deprecation of the Carrier Grade documentation. The content was outdated and no longer relevant to the Azure Well-Architected Framework.

## June 2024

### Updated articles

- [Recommendations for defining reliability targets](./reliability/metrics.md): Find updated SLO and SLA guidance to help you set and measure reliability targets for your workload.

- [Design methodology for sustainability workloads on Azure](./sustainability/sustainability-design-methodology.md): Learn how Azure carbon optimization can help you reduce the carbon footprint of your workload by providing granular emissions data.

## May 2024

### New articles

- [Azure Well-Architected Framework perspective on Azure Files](./service-guides/azure-files.md): Explore design considerations and recommendations for Azure Files and Azure File Sync. Azure Files is a fully managed file share service that supports the SMB protocol and Network File System (NFS) protocol. Azure File Sync is a service that enables you to centralize your organization's file shares in Azure Files, while keeping the flexibility, performance, and compatibility of an on-premises file server.

### Updated articles

- [Security considerations for Azure VMware Solution workloads](./azure-vmware/security.md): Learn about using resource locks to prevent accidental deletions or changes.

## April 2024

### New articles

This month, we added two new service guides and new documentation about Oracle workloads on Azure infrastructure as a service (IaaS).

**Service guides**

- [Azure Well-Architected Framework perspective on App Service](./service-guides/app-service-web-apps.md): Review design considerations and recommendations for App Service. Azure App Service is a type of platform as a service (PaaS) compute service that allows you to host your workload on the Azure platform.
- [Azure Well-Architected Framework perspective on Azure Blob Storage](./service-guides/azure-blob-storage.md): Review design considerations and configuration recommendations that are relevant to Azure Blob Storage. Azure Blob Storage is a cloud-based object storage solution that is optimized for storing massive amounts of unstructured data, such as text or binary data.

**Oracle workloads on Azure**

Find new articles and updated guidance about Oracle workloads on Azure. Explore best practices to help you create a performant, secure, and highly available solution. To get started, see [Oracle workloads on Azure infrastructure as a service (IaaS)](./oracle-iaas/index.yml).

## March 2024

### New articles

- [Health modeling for workloads](./design-guides/health-modeling.md): Use health modeling to improve workload reliability in Azure. Differentiate between healthy, degraded, and unhealthy states. Learn how to quantify application health and build your own health model.
- [Azure Well-Architected Framework review for Log Analytics](./service-guides/azure-log-analytics.md): Learn about the architectural recommendations for Log Analytics workspaces in Azure Monitor. These workspaces are the primary log and metric sink for a large portion of the monitoring data. Workspaces support multiple features in Azure Monitor, including ad-hoc queries, visualizations, and alerts.

### Updated articles

- [Recommendations for standardizing tools and processes](./operational-excellence/tools-processes.md): Find new guidance to implement standards for naming and tagging your resources.

## February 2024

### New articles

- [Azure Well-Architected Framework perspective on Azure Front Door](./service-guides/azure-front-door.md): Find design considerations and configuration recommendations for Azure Front Door. Azure Front Door is a global load balancer and content delivery network that routes HTTP and HTTPS traffic.
- [Azure Well-Architected Framework perspective on Azure OpenAI](./service-guides/azure-openai.md): Find design considerations and configuration recommendations for Azure OpenAI. This service provides REST API access to the OpenAI large language models (LLMs), adding capabilities for Azure networking and security.
- [Azure Well-Architected Framework perspective on Azure Machine Learning](./service-guides/azure-machine-learning.md): Find design considerations and configuration recommendations for Azure Machine Learning. This service provides a cloud-based environment you can use to train, deploy, automate, manage, and track machine learning models. 

### Updated articles

- [Architecture design diagrams](./architect-role/design-diagrams.md): We expanded the guidance for using official icons and service names to include examples of links to icons for Microsoft services.
- [Azure Well-Architected Framework review for Azure Kubernetes Service (AKS)](./service-guides/azure-kubernetes-service.md): We updated the design checklist and the recommendations for AKS configuration. Find information about Azure Spot Virtual Machines, Cluster Autoscaler, Node Autoprovision, and the AKS Cost Analysis add-on.
- [Recommendations for securing a development lifecycle](./security/secure-development-lifecycle.md): We streamlined and clarified guidance about the security design patterns that the application code should implement. 
- [Encryption strategy recommendations](./security/encryption.md): We added information about confidential computing and explained how it fits into key design strategies.
