---
title: What's new in the Azure Well-Architected Framework
description: Find out about recent updates and new documentation in the Azure Well-Architected Framework.
author: ckittel
ms.author: chkittel
ms.date: 07/14/2025
ms.topic: whats-new
---

# What's new in the Azure Well-Architected Framework

Find out about recent changes in the Azure Well-Architected Framework.

## August 2025

### Updated article

- [Performance Efficiency design principles](./performance-efficiency/principles.md): We updated this article to make the guidance clearer and more actionable. Updates also simplify technical language and streamline recommendations to help teams align performance goals with business needs. These changes make it easier to plan, build, and maintain systems that perform reliably over time.
- [Architecture best practices for Azure SQL Database](./service-guides/azure-sql-database.md): We clarified how zone redundancy works in Azure SQL Database by specifying that both compute and storage components are distributed across two or three availability zones, rather than just spreading data. This update emphasizes that Azure selects the optimal zone configuration for resilience and that these zones are physically separate with independent infrastructure. The change provides a more complete picture of how zone redundancy ensures high availability.

## July 2025

### Updated articles

- [Responsible AI in Azure workloads](./ai/responsible-ai.md): We refreshed this article with new guidance for agentic AI systems, including safeguards for retrieval and autonomous agents, auditability, and human oversight. Content safety recommendations now include watermarking, metadata tagging, and clear disclosure of AI-generated media. Ethical updates emphasize the ability to contest AI decisions and ensure transparency in system changes. These additions support more responsible and secure deployment of advanced AI solutions.
- [Architecture best practices for Azure Local](./service-guides/azure-local.md): We updated this guidance to reflect the latest platform version (2311), refreshed documentation links, clarified pricing details, clarified role definitions, and simplified terminology. We also improved how we describe security, monitoring, and licensing features to make the guidance easier to follow and more relevant to current deployments.
- [Application design for AI workloads on Azure](./ai/application-design.md) We added a link to common AI agent orchestration patterns to help teams explore proven strategies before designing their own.

## June 2025

### Maturity models

This month, we introduced maturity models for the Azure Well-Architected Framework. Maturity models help you assess your current state and identify areas for improvement across the five pillars of the framework. Each model provides a structured approach to evaluate your workload's architecture and operations, enabling you to prioritize enhancements and track progress over time.

- [Reliability maturity model](./reliability/maturity-model.md)
- [Security maturity model](./security/maturity-model.md)
- [Cost Optimization maturity model](./cost-optimization/maturity-model.md)
- [Operational Excellence maturity model](./operational-excellence/maturity-model.md)
- [Performance Efficiency maturity model](./performance-efficiency/maturity-model.md)
- [Assessment](/assessments/af7d9889-8cb2-4b8b-b6bb-e5a2e2f2a59c)

### New article

- [Sustainable design for AI workloads on Azure](./sustainability/sustainable-ai-design.md): Find guidance about how to build AI workloads sustainably by incorporating environmental considerations into model design, data design, and operations phases. Learn how to reduce energy consumption and carbon footprint while maintaining performance and business requirements for AI workloads on Azure.

### Updated articles

- [Design methodology for mission-critical workloads on Azure](./mission-critical/mission-critical-design-methodology.md): We refreshed this article by restructuring the design methodology to focus on core design fundamentals through a principle-based approach. The article now emphasizes designing for reliability objectives, end-to-end automation, zero-downtime deployments, fast failure detection and recovery, and evolving with Azure. We streamlined the content to be more concise and actionable while maintaining focus on essential design principles.
- [Mission-critical workloads](./mission-critical/mission-critical-overview.md): We restructured the mission-critical workloads overview article to improve user experience by adding a new **How to use this guidance?** section that provides step-by-step navigation instructions. We reorganized the content to improve the information hierarchy, moving from a reference-style presentation to a guided learning approach that walks users through the methodology, principles, and design areas systematically. We also renamed the **Illustrative examples** section to **Reference architecture examples** and added video content.
- [Design principles of a mission-critical workload](./mission-critical/mission-critical-design-principles.md): We updated the **Next step** section. Both the link destination and link text changed from "Cross-cutting concerns" to "Architecture pattern" while keeping the same descriptive text about reviewing cross-cutting concerns for mission-critical workloads.
- [Grounding data design for AI workloads on Azure](./ai/grounding-data-design.md): We refreshed our guidance on grounding data by making it clear that data can come from various sources, such as databases with vector indexes and external systems, not only traditional indexes. The updates reflect the benefits of larger context windows in newer models, clarifies previous terminology around fine-tuning data, and highlights the importance of validating grounding data through real-world queries. Other improvements include updated guidance about security trimming, support for multi-media embeddings, and new considerations for agentic solutions.

## May 2025

### Updated articles

- [Recommendations for designing a reliable scaling strategy](./reliability/scaling.md): Explore updated content including: Choosing the right technology for scaling; Automating scaling operations, including use of Infrastructure-as-Code; Selecting and optimizing "scale units"; Scaling data stores using sharding and partitioning, and optimizing partition strategies; Monitoring scaling operations and log analysis.
- [Design a data partitioning strategy](./design-guides/partition-data.md): This article can now be found under "Design guides."
- [Cost Optimization design principles](./cost-optimization/principles.md): Find actionable recommendations, such as treating different environments differently, using dynamic scaling, and collaborating with licensing teams. We added guidance on governance and cost guardrails and expanded examples and practical steps for budgeting, rate optimization, and maximizing resource utilization.
- [Operational Excellence cloud design principles](./operational-excellence/principles.md): We refreshed this article, consolidating guidance and updating safe deployment practices.

#### Service guides

This month, we made significant updates to some of our service guides. Here are the highlights:

- [Architecture Best Practices for Azure Database for PostgreSQL](./service-guides/postgresql.md): Find recommendations for features like high availability, geo-redundant backup, private networking, Microsoft Entra ID integration, cost modeling, automation, monitoring, and intelligent performance tuning.
- [Architecture Best Practices for Azure Cosmos DB for NoSQL](./service-guides/cosmos-db.md): Explore guidance updated to reflect current Azure Cosmos DB best practices, including new features, policy links, and recommendations for using Microsoft Entra ID, private endpoints, Azure Policy, and Azure Advisor.

## April 2025

### New articles

- [Architecture Best Practices for Azure Container Apps](./service-guides/azure-container-apps.md): Explore key recommendations and design checklists for implementing Azure Container apps effectively and securely. This guide covers design principles, strategies, and recommendations for achieving architectural goals, including security, performance, and cost optimization.

### Updated articles

- [Application Delivery Considerations for Azure Virtual Desktop Workloads](./azure-virtual-desktop/application-delivery.md): We made significant updates to refactor and refresh the guidance about Azure Virtual Desktop including updated recommendations and best practices.
- [Recommendations for designing a disaster recovery strategy](./reliability/disaster-recovery.md): We added a new section on Azure Backup facilitation and updated the content to include new Azure Backup features.

#### Service guides

This month, we made significant updates to some of our service guides. Here are the highlights:

- [Architecture Best Practices for Azure API Management](./service-guides/azure-api-management.md): We refactored the guidance for Azure API Management to improve clarity and usability. The updated content includes refreshed design principles, strategies, and recommendations for achieving architectural goals.
- [Architecture Best Practices for Azure NetApp Files](./service-guides/azure-netapp-files.md): We added new recommendations for cost optimization and operational excellence. Explore the fully updated content including new recommendations for configuring Azure NetApp Files to protect your workloads.
- [Architecture Best Practices for Azure SQL Database](./service-guides/azure-sql-database.md): We refreshed and expanded this guide to introduce new design considerations and recommendations, including guidance on using Azure SQL Database for secure data storage, managing database performance, and optimizing costs.
- [Architecture Best Practices for Azure Virtual Machines and Scale Sets](./service-guides/virtual-machines.md): We made updates to the guidance about Azure Virtual Machines and Scale Sets. Explore the fully updated content including new recommendations for configuring automatic recovery options to protect your workloads.

## March 2025

### Updated articles

- [Architecture Best Practices for Azure Front Door](./service-guides/azure-front-door.md): We added details on deployment strategies and the importance of caching static content to the design checklist. Recommendations were updated to include new links and advice on managing traffic, health probes, and optimizing caching.
- [Design review checklist for Operational Excellence](./operational-excellence/checklist.md): We simplified the design review checklist for Operational Excellence to make the recommendations more concise, focused, and actionable.

## February 2025

### Updated articles

- [Recommendations for self-healing and self-preservation](./reliability/self-preservation.md): We updated this guide for easier readability by streamlining several sections. We also moved the guides for [handling transient faults](./design-guides/handle-transient-faults.md) and [developing background jobs](./design-guides/background-jobs.md) into the design guide area to make the Reliability pillar easier to use.
- [Design review checklist for Reliability](./reliability/checklist.md): We simplified the design review checklist for Reliability to make the recommendations more concise, focused, and actionable.

#### Service guides

This month, we made significant updates to some of our service guides. Here are the highlights:

- [Azure Front Door](./service-guides/app-service-web-apps.md): We improved content clarity by rephrasing various sections and adding more detailed explanations. We updated links and references to other relevant Azure documentation and removed redundant or outdated information. We aligned content with the latest Azure Front Door features and best practices.
- [Azure IoT Hub](./service-guides/azure-iot-hub.md): We refreshed and expanded this guide to cover all Well-Architected pillars. We also introduced new design considerations and recommendations, including guidance on using Azure IoT Hub for secure device-to-cloud communication, managing device identities, and optimizing costs.

## January 2025

### Updated articles

- [How to use the Azure Well-Architected Framework documentation](.//what-is-well-architected-framework.md): We added a new section on adopting a phased learning process, emphasizing the importance of improving workload quality iteratively.

#### Service guides

This month, we made significant updates to many of our service guides. Here are some highlights:

- [Azure App Service (Web Apps)](./service-guides/app-service-web-apps.md): Added and updated several design considerations and recommendations, including guidance on redundancy, taking advantage of the App Service auto-heal feature, and leveraging the App Service Resiliency Score Report.
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

- [Architecture design patterns that support cost optimization](./cost-optimization/design-patterns.md)
- [Cost Optimization tradeoffs](./cost-optimization/tradeoffs.md)
- [Architecture design patterns that support reliability](./reliability/design-patterns.md)
- [Reliability tradeoffs](./reliability/tradeoffs.md)
- [Architecture design patterns that support security](./security/design-patterns.md)
- [Security tradeoffs](./security/tradeoffs.md)
- [Architecture design patterns that support operational excellence](./operational-excellence/design-patterns.md)
- [Operational Excellence tradeoffs](./operational-excellence/tradeoffs.md)
- [Architecture design patterns that support performance efficiency](./performance-efficiency/design-patterns.md)
- [Performance Efficiency tradeoffs](./performance-efficiency/tradeoffs.md)

## September 2024

### New articles

- [Azure Well-Architected Framework perspective on Azure NetApp Files](./service-guides/azure-netapp-files.md): Explore design considerations and configuration recommendations for Azure NetApp Files. Azure NetApp Files is a fully managed file share service that supports the Server Message Block (SMB) protocol and Network File System (NFS) protocol. Learn how you can use Azure NetApp Files for file sharing, high-performance computing, home directories, and databases.

### Updated articles

- [Azure Well-Architected Framework perspective on Azure Firewall](./service-guides/azure-firewall.md): We made significant updates to the guidance about Azure Firewall. Explore the fully updated content including new recommendations for configuring Azure Firewall to protect your workloads.
