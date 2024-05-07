---
title: What's new in the Azure Well-Architected Framework
description: Find out about recent updates and new documentation in the Azure Well-Architected Framework.
author: ckittel
ms.author: chkittel
ms.date: 05/06/2024
ms.topic: conceptual
---

# What's new in the Azure Well-Architected Framework

Find out about recent changes in the Azure Well-Architected Framework.

## April 2024

### New articles

- [Choose compute and storage](./oracle-iaas/choose-compute-storage.md): Choose the correct combination of compute and storage for Azure IaaS and the location of application workloads relative to database services. Learn how to apply right-size compute and storage principles to Oracle workloads using the Azure infrastructure as a service (IaaS) model.
- [Design Oracle applications](./oracle-iaas/design-applications.md): Review the design area for Oracle applications and see how to apply these principles to Oracle on Azure IaaS workloads. Understand which functionalities each version of an application supports so that you can avoid problems during a migration to the cloud.
- [Create an Oracle workload on Azure](./oracle-iaas/get-started.md): Learn about best practices for an Oracle workload on Azure IaaS to help you create a performant, secure, and highly available solution.
- [Monitor your Oracle workload](./oracle-iaas/monitor-workloads.md): Learn how to use Azure Virtual Machines to monitor your Oracle workloads. Identify failures and abnormalities to ensure the health of your mission-critical workloads.
- [Optimize business continuity and disaster recovery](./oracle-iaas/optimize-business-continuity-disaster-recovery.md): Review the design area for BCDR practices. Oracle on Azure infrastructure as a service (IaaS) can fulfill the required resiliency objectives of the most demanding Oracle workloads. See how to apply these principles to Oracle on Azure IaaS workloads.
- [Optimize security for your Oracle workload](./oracle-iaas/optimize-security.md): Review security recommendations for the Azure control plane related to Oracle application workloads that are deployed on virtual machines (VMs) on Azure. Learn how to optimize security for Oracle workloads on Azure by using the defense-in-depth approach to employ a combination of various layer security measures and create a robust security posture.
- [Design principles for an Oracle workload on Azure](./oracle-iaas/review-design-principles.md): Review the design principles built upon the five pillars of architectural excellence: reliability, security, cost optimization, performance efficiency, and operation excellence. Gain insight into best practices for designing and implementing Oracle workloads on Azure IaaS.
- [Azure Well-Architected Framework - App Service (Web Apps)](./service-guides/app-service-web-apps.md): Review design considerations and recommendations about App Service (Web Apps). Azure App Service is a type of Platform-as-a-Service (PaaS) compute service that allows you to host your workload on the Azure platform. It's a fully managed service that abstracts the underlying compute and offloads the responsibility of building, deploying, and scaling to the platform.
- [Azure Well-Architected Framework perspective on Azure Blob Storage](./service-guides/azure-blob-storage.md): Review Azure Well-Architected Framework design considerations and configuration recommendations that are relevant to Azure Blob Storage. Azure Blob Storage is a cloud-based object storage solution that is optimized for storing massive amounts of unstructured data, such as text or binary data.

### Updated articles

- [Health modeling for workloads](./cross-cutting-guides/health-modeling.md): We made minor updates to remove deprecated terms and to clarify the guidance.

## March 2024

### New articles

- [Health modeling for workloads](./cross-cutting-guides/health-modeling.md): Use health modeling to improve workload reliability in Azure. Differentiate between healthy, degraded, and unhealthy states. Learn how to quantify application health and build your own health model.
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
  
## January 2024

In January we added two new articles, and we updated two articles.

### New articles

- In [Virtual Machines and scale sets](./service-guides/virtual-machines.md), find guidance about how to review your virtual machine and scale set workloads by using the Well-Architected Framework. Use the Azure Virtual Machines compute service to create and run virtual machines on the Azure platform. You can choose from different SKUs, operating systems, and configurations. 

- In [Optimize workload design using flows](./cross-cutting-guides/optimize-workload-using-flows.md), learn how to optimize workloads through structured flow design. Take a look at a three-step process for workload optimization, including defining flow structures, setting technical requirements, and designing flows to meet these specifications. As you work to align flows with business processes and use cases, find practical examples and recommendations in this article.

### New articles

Find updates to the following articles in the Operational Excellence pillar:

- In [Recommendations for implementing automation](operational-excellence/automate-tasks.md), find information about how to use Azure Update Manager to help you manage and govern updates for virtual machines. You can monitor Windows and Linux update compliance across your workload. You can also use Update Manager to make real-time updates or schedule them within a defined maintenance window.

- In [Recommendations for enabling automation in a workload](operational-excellence/enable-automation.md), find a new section about using Azure Monitoring Agent for change tracking and inventory. Automate drift detection, the inventory-running services, and installed packages on the virtual machines in your workload.  

## December 2023

In December, we added a workload and updated recommendations for two Well-Architected Framework pillars.

### New article

We added a new workload for workload owners, technical stakeholders, and business stakeholders. This documentation is appropriate for roles that are accountable for designing, building, and maintaining a solution for running applications and desktops in a cloud environment. Use the [Azure Virtual Desktop workloads](./azure-virtual-desktop/overview.md) documentation as your go-to resource for optimizing the way you operate applications and desktops in Azure Virtual Desktop. 

### Updated articles

Updated recommendations for the Reliability pillar:

- [Recommendations for handling transient faults](reliability/handle-transient-faults.md)
- [Recommendations for developing background jobs](reliability/background-jobs.md)
- [Recommendations for designing a disaster recovery strategy](reliability/disaster-recovery.md)
- [Recommendations for designing for redundancy](reliability/redundancy.md)

Updated recommendations for the Operational Excellence pillar:

- [Recommendations for implementing automation](operational-excellence/automate-tasks.md)
- [Recommendations for designing an emergency response strategy](operational-excellence/emergency-response.md)
- [Recommendations for enabling automation in a workload](operational-excellence/enable-automation.md)
- [Recommendations for using infrastructure as code](operational-excellence/infrastructure-as-code-design.md)
- [Recommendations for designing a deployment failure mitigation strategy](operational-excellence/mitigation-strategy.md)
- [Recommendations for safe deployment practices](operational-excellence/safe-deployments.md)

## November 2023

The Azure Well-Architected Framework completed a significant content refresh across all five pillars. We're breaking from our standard "What's new" format this month because the changes that launched with [Microsoft Ignite 2023](https://ignite.microsoft.com/) go beyond bullet points.

### The core pillars of architecture have been restructured

All five pillars of the Well-Architected Framework now follow a common structure that consists exclusively of design principles, design review checklists, tradeoffs, recommendation guides, and cloud design patterns.

- **Design principles**. Presents goal-oriented principles that build a foundation for the workload. Each principle includes a set of recommended approaches and the benefits of taking those approaches. The principles for each pillar changed in terms of content and coverage.

  - [Reliability design principles](./reliability/principles.md)
  - [Security design principles](./security/principles.md)
  - [Cost Optimization design principles](./cost-optimization/principles.md)
  - [Operational Excellence design principles](./operational-excellence/principles.md)
  - [Performance Efficiency design principles](./performance-efficiency/principles.md)

- **Design review checklists**. Lists roughly codified recommendations that drive action. Use the checklists during the design phase of your new workload and to evaluate brownfield workloads.

  - [Design review checklist for Reliability](./reliability/checklist.md)
  - [Design review checklist for Security](./security/checklist.md)
  - [Design review checklist for Cost Optimization](./cost-optimization/checklist.md)
  - [Design review checklist for Operational Excellence](./operational-excellence/checklist.md)
  - [Design review checklist for Performance Efficiency](./performance-efficiency/checklist.md)

- **Tradeoffs**. Describes tradeoffs with other pillars. Many design decisions force a tradeoff. It's vital to understand how achieving the goals of one pillar might make achieving the goals of another pillar more challenging.

  - [Reliability tradeoffs](./reliability/tradeoffs.md)
  - [Security tradeoffs](./security/tradeoffs.md)
  - [Cost Optimization tradeoffs](./cost-optimization/tradeoffs.md)
  - [Operational Excellence tradeoffs](./operational-excellence/tradeoffs.md)
  - [Performance Efficiency tradeoffs](./performance-efficiency/tradeoffs.md)

- **Recommendation guides**. Every design review checklist recommendation is associated with one or more guides. They explain the key strategies to fulfill that recommendation. They also include how Azure can facilitate workload design to help achieve that recommendation. Some of these guides are new, and others are refreshed versions of guides that covered a similar concept.

   The recommendation guides include tradeoffs along with risks.
  - This icon indicates a tradeoff: :::image type="icon" source="./_images/trade-off.svg":::
  - This icon indicates a risk: :::image type="icon" source="./_images/risk.svg":::

- **Cloud design patterns**. Build your design on proven, common architecture patterns. The Azure Architecture Center maintains the [Cloud Design Patterns](/azure/architecture/patterns/) catalog. Each pillar includes descriptions of the cloud design patterns that are relevant to the goals of the pillar and how they support the pillar.

### Well-Architected Framework assessments

The [Well-Architected Review assessment](/assessments/azure-architecture-review/) was refreshed. Specifically, the "Core Well-Architected Review" option now aligns to the new content structure in the Well-Architected Framework. Every question in every pillar maps to the design review checklist for that pillar. All choices for the questions correlate to the recommendation guides for the related checklist item.

> [!IMPORTANT]
> **Backwards compatibility notice.** The first new milestone on existing Core Well-Architected Review assessment sessions will not be prefilled with your prior responses due to the assessment refresh. You will be able to access prior milestones, but when you create the first new milestone, you will need to reassess the workload against the new questions and choices.

No other assessments were changed as part of this refresh.

### Thematic changes

In addition to the changes in structure and consistency, you should note some thematic changes within the content. See the following key examples of these changes.

- **Workloads are more than technology**. The scope of the Well-Architected Framework is your workload. The principles and the guides provide recommendations for **people and processes** of the workload team along with technical guidance.

- Workloads exist within the context of the organization. The Well-Architected Framework frequently addresses **workload responsibility to organizational expectations**. The Well-Architected Framework calls out the benefits and tradeoffs of organizational influence.

- Prior to this refresh, the guidance was more focused on the infrastructure than on the application running on that infrastructure. Now, every pillar has **developer-centric content**.

- **Specific service configuration has been minimized**. The Well-Architected Framework pillar content is design content, not implementation content. Before the refresh, the Well-Architected Framework interwove Azure service-specific guidance and design guidance. Now, the Well-Architected Framework limits service-specific content to dedicated sections in the recommendation guides. The Well-Architected Framework service guides continue to exist to serve as the primary source for service-centric perspectives.

> [!IMPORTANT]
> As part of this restructuring, many pages have been added, moved, removed, or changed. Redirection is in place where possible, but we're aware that many existing links to the Well-Architected Framework might no longer point to the same content.

### What didn't change?

- The Well-Architected Framework continues to put a **healthy burden on workload teams**. It ensures that workload teams know **what decisions need to be made** and what risks, benefits, and tradeoffs are associated with those decisions.

- The Well-Architected Framework provides recommendations to **help you make informed and justified decisions**. The Well-Architected Framework doesn't know your business requirements or constraints, so it can't make decisions for you.

- The [Well-Architected Framework workloads](./workloads.md) and the [Well-Architected Framework service guides](./service-guides/index.yml) didn't undergo significant changes with this refresh. New workloads and service guides are ongoing additions to the Well-Architected Framework. Keep checking this page for updates.

## October 2023

- Updated service guide: [Azure Well-Architected Framework review - Azure Cosmos DB for NoSQL](./service-guides/cosmos-db.md)
- Updated service guide: [Azure Well-Architected Framework review - Azure Firewall](./service-guides/azure-firewall.md)

## September 2023

- New Reliability pillar guide: [Recommendations for using availability zones and regions](./reliability/regions-availability-zones.md)
- Updated service guide: [Azure Well-Architected Framework review - Azure Database for PostgreSQL](service-guides/postgresql.md)

## August 2023

- New Well-Architected Framework workload: [Azure VMware Solution workloads](./azure-vmware/index.yml)
- Updated service guide: [Azure Well-Architected Framework review - Virtual Machines](./service-guides/virtual-machines-review.md)

## Related links

- [What's new in Azure Architecture Center](/azure/architecture/changelog)
- [What's new in the Microsoft Cloud Adoption Framework for Azure](/azure/cloud-adoption-framework/get-started/whats-new)
