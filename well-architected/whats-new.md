---
title: What's new in the Azure Well-Architected Framework
description: Find out about recent updates and new documentation in the Azure Well-Architected Framework.
author: ckittel
ms.author: chkittel
ms.date: 10/08/2025
ms.topic: whats-new
---

# What's new in the Azure Well-Architected Framework

Find out about recent changes in the Azure Well-Architected Framework.

## September 2025

### New articles

- [Architecture best practices for Azure Databricks](./service-guides/azure-databricks.md): Find recommendations for designing and operating Azure Databricks workloads by using Well-Architected Framework principles. Learn how to apply reliability, security, cost optimization, operational excellence, and performance efficiency to Spark, Delta Lake, Unity Catalog, and MLflow solutions.
- [Architecture strategies for using availability zones and regions](./design-guides/regions-availability-zones.md): Learn how to choose between deploying workloads across Azure availability zones or regions to meet reliability, resiliency, cost, and performance goals. This guide explains the differences between locally redundant, zonal (pinned), zone-redundant, and multi-region deployments, and describes the trade-offs for each approach. It provides practical recommendations for aligning deployment strategies with business requirements like risk tolerance, data residency, user location, budget, and complexity. Example scenarios and guidance for combining multi-zone and multi-region architectures are included for mission-critical solutions.
- [Develop a disaster recovery plan for multi-region deployments](./design-guides/disaster-recovery.md): Find recommendations for building a disaster recovery (DR) plan for multi-region Azure deployments. This guide explains key terminology, how to classify workloads by criticality, and how to align recovery strategies with business impact. It covers DR planning essentials like communication plans, runbooks, and escalation paths, and provides practical advice for optimizing recovery costs. Step-by-step actions and validation methods are included for backup and restore, active-passive (cold and warm standby), and active-active deployments. Regular testing and continuous improvement are emphasized to ensure business continuity.

### Updated articles

- [Architecture pattern for mission-critical workloads on Azure](./mission-critical/mission-critical-architecture-pattern.md): We simplified the baseline architecture section by removing extra lines, card outlines, and clickable links on diagrams.
- [Architecture strategies for designing for redundancy](./reliability/redundancy.md): We updated headings in the redundancy documentation to improve clarity and consistency. We also expanded the redundancy guidance, added new recommendations for zone and region-level redundancy, and clarified trade-offs for different approaches.
- [Health modeling for workloads](./design-guides/health-modeling.md): We added a section about Azure Monitor health models. It describes how they simplify health modeling with built-in alerting, visualizations, and easy integration. We also included a screenshot and updated related links to reference Azure Monitor health models.
- [Reliability design principles](./reliability/principles.md): We clarified workload scope and team commitments, added clear instructions for requirements and solution boundaries, highlighted reliability for user flows, emphasized time horizons and dependencies, and fixed wording in the resilience section.
- [Architecture best practices for Azure App Service (Web Apps)](./service-guides/app-service-web-apps.md): We updated the guide to include IPv6 support considerations for scaling in App Service and clarified related network protocol guidance. We also updated the Azure policies section to specify how recommendations can be audited and improved the policy link description.
- [Architecture best practices for Azure Machine Learning](./service-guides/azure-machine-learning.md): We refreshed the guidance for Machine Learning, added new recommendations for reliability, security, and cost optimization, and improved tips for operational excellence.
- [Architecture best practices for Azure Blob Storage](./service-guides/azure-blob-storage.md): We updated the Azure Blob Storage guidance to clarify that reading data from the secondary region during a primary outage requires RA-GRS or RA-GZRS. We emphasized designing applications to minimize downtime, emphasized shared access signature (SAS) expiration policies, added Azure Storage Actions for automated life cycle management, highlighted data residency policy consistency, and included a new **Trade-offs** section about cost, performance, and redundancy.
- [Architecture strategies for designing and creating a monitoring system](./operational-excellence/observability.md): We expanded the observability guidance with new recommendations for network monitoring, alerting, and diagnostics, and clarified best practices for operational excellence.
- [Architecture strategies for designing a reliable monitoring and alerting strategy](./reliability/monitoring-alerting-strategy.md): We refreshed the monitoring and alerting strategy guidance, added new recommendations for monitoring network traffic to improve reliability, and expanded best practices for using Azure tools in incident response.
- [Architecture best practices for Azure Firewall](./service-guides/azure-firewall.md): We updated our Azure Firewall guidance by adding recommendations for Resource Health monitoring, explicit proxy configuration, customer-controlled maintenance, and change tracking to improve resiliency, security, and operational efficiency. We also enhanced our cost optimization steps by correcting the documentation link for log transformations and clearly stating that logs should be filtered before they enter the `AZFW` tables.
- [Architecture best practices for Azure Monitor Application Insights](./service-guides/application-insights.md): We clarified language and streamlined instructions throughout the Application Insights service guide by tightening definitions, removing redundancy, and emphasizing key principles in reliability, security, cost optimization, performance efficiency, and Azure policies.
- [Architecture best practices for Azure API Management](./service-guides/azure-api-management.md): We updated the guidance for API Management, added new recommendations for reliability, security, and cost optimization, and clarified best practices for monitoring and alerting.
- [Architecture best practices for Azure Application Gateway v2](./service-guides/azure-application-gateway.md): We updated the guidance for Azure Application Gateway v2, added new recommendations for AI-powered threat analysis and response, and included links to pillar-specific recommendations in Azure Advisor.
- [Architecture best practices for Azure Virtual Network](./service-guides/virtual-network.md): We clarified checklist recommendations to focus on Azure Virtual Network, emphasized centralized IP address management, and promoted integrated diagnostics with Azure Network Watcher VM Network Troubleshooter.
- [Architecture best practices for Azure files](./service-guides/azure-files.md): We revised the Azure Files guide to clarify terminology, update redundancy and billing model details, add SSD file share and metadata caching guidance, and include total cost of ownership (TCO) resources. We also added guidance about how to use the Azure File Sync Arc Extension for hybrid environments.
- [Test and evaluate AI workloads on Azure](./ai/test.md): We refreshed the guidance for testing and evaluating AI workloads, added new recommendations for validation methods, and clarified best practices for continuous improvement.
- [Architecture best practices for Azure SQL Database](./service-guides/azure-sql-database.md): We clarified how zone redundancy works in Azure SQL Database, emphasized optimal zone configuration for resilience, and explained how physical separation ensures high availability.
- [Architecture best practices for Azure Traffic Manager](./service-guides/azure-traffic-manager.md): We updated the guidance to clarify design principles for global load balancing, added recommendations to improve health probing and failover practices, and enhanced tips for monitoring and troubleshooting Azure Traffic Manager profiles.
- [Architecture best practices for Azure Event Hubs](./service-guides/event-hubs.md): We added a recommendation to enable geo replication for Event Hubs to support solution-wide availability and disaster recovery. We also added guidance for custom dead-letter queues. We emphasized checkpointing for consumers and clarified recommendations for zone redundancy and geo disaster-recovery with updated links and switched the namespace query example from SQL to Kusto.
- [Service Bus and reliability](./service-guides/service-bus/reliability.md): We expanded the reliability guidance for Service Bus by clarifying exception handling practices, adding recommendations for batch processing, retry policies, and dead-letter queue handling. We also updated recommendations for high availability and geo-disaster recovery, and included guidance for duplicate detection.

## August 2025

### Updated articles

- [Workload team personas for AI workloads](./ai/personas.md): We have updated this article to reflect the latest advances in generative AI, with a focus on agentic solutions. We added a section about agentic personas to introduce the agentic personas and their role in AI workloads. We also expanded the example personas to include both human and automated agent roles in development and operations. The persona templates and examples now reflect dynamic, cross-system, and just-in-time access needs, emphasizing governance, accountability, and the unique requirements of agentic systems.
- [Architecture best practices for Log Analytics](./service-guides/azure-log-analytics.md): We expanded and restructured the guidance for Log Analytics workspaces and introduced detailed recommendations, checklists, and best practices for the Well-Architected Framework pillars.
- [Performance Efficiency design principles](./performance-efficiency/principles.md): We made the guidance clearer and more actionable. We simplified technical language and streamlined recommendations to help teams align performance goals with business needs. These changes make it easier to plan, build, and maintain systems that perform reliably over time.
- [Architecture design diagrams](./architect-role/design-diagrams.md): We updated the guidance about diagramming practices and architecture diagram types by adding new recommendations for clarity, accessibility, version control, and layered visuals. We also introduced new diagram categories and provided more detailed descriptions of diagram purposes and best practices.
- [Architecture strategies for implementing automation](./operational-excellence/automate-tasks.md): We added Azure tools that you can use to automate tasks for your workload. Learn about automated management capabilities for networking services, including Azure Firewall customer-controlled maintenance, Azure Firewall fully qualified domain name (FQDN) filtering in destination network address translation (DNAT) rules, and Azure Front Door managed certificates.
- [Architecture strategies for networking and connectivity](./security/networking.md): We added Azure Network Security Perimeter to the list of Azure services that you can use to add defense-in-depth capabilities to your network.
- [Architecture strategies for building a segmentation strategy](./security/segmentation.md): We added another network segmentation pattern: PaaS isolation. We recommend using Azure Network Security Perimeter with this pattern.
- [Architecture best practices for Azure Firewall](./service-guides/azure-firewall.md): We added Microsoft Security Copilot as a tool for threat investigation and analysis and information about ingestion-time transformation in Log Analytics to help you reduce costs. We also added configuration recommendations for the Operational Excellence pillar.
- [Architecture best practices for Azure Front Door](./service-guides/azure-front-door.md): We made changes to emphasize the use of AI-powered security capabilities, including Security Copilot integration for web application firewall event analysis. We also added guidance about managed wildcard Transport Layer Security (TLS) certificates.
- [Architecture best practices for Azure Kubernetes Service (AKS)](./service-guides/azure-kubernetes-service.md): We added guidance for AKS clusters, including recommendations for HTTP proxy support, custom certificate authority integration, and Azure CNI static block allocation to improve compliance, security, and network management.
- [Architecture best practices for Azure Virtual Network](./service-guides/virtual-network.md): We added recommendations for using Network Security Perimeter for PaaS service isolation, centralized IP address management with Azure Virtual Network Manager, and integrated diagnostics with Network Watcher VM Network Troubleshooter.
- [Architecture best practices for Azure Application Gateway v2](./service-guides/azure-application-gateway.md): We removed the specific examples and feature comparisons between Azure Front Door and Application Gateway. The guidance now focuses on using WAF policies and locking down Application Gateway to receive traffic only from Azure Front Door. We also clarified language and updated references in the guide. We improved section headings and checklists to make guidance more accurate and easier to follow.
- [Architecture best practices for Azure SQL Database](./service-guides/azure-sql-database.md): We added sharding guidance, clarified zone redundancy setup, and introduced automated backup recommendations to strengthen reliability and recovery for Azure SQL Database. We also updated article to improve cost optimization and performance recommendations, added advice to use native SQL functions, and noted the latest Azure updates.
- [Architecture best practices for Azure Disk Storage](./service-guides/azure-disk-storage.md): We added a section that describes the design trade-offs that you might have to make if you use the approaches in the pillar checklists, including guidance about performance versus cost for Azure Premium SSD, Azure Ultra Disk Storage, Azure Standard SSD, and Azure Standard HDD. We also added recommendations for just-in-time capacity provisioning and dynamic disk expansion without downtime.
- [Complete an Azure Well-Architected Review assessment](./design-guides/implementing-recommendations.md): We added a tip about selecting **Core Well-Architected Review** in the Azure Well-Architected Review assessment. We also added a section about specialized Well-Architected review assessments for specific technologies and workloads.
- [Azure Well-Architected Framework workloads](.//workloads.md): We expanded the definition of workloads to include custom code and AI models, emphasized architectural practices like decomposing workloads and addressing technical debt, and added sections about the organization of workload teams, dependencies, budgeting, and continuous improvement. We also added guidance about shared responsibilities and governance within cloud environments.

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

- [Architecture decision record (ADR)](./architect-role/architecture-decision-record.md): We refreshed the guidance on what an ADR should include, including consistent elements like problem statements, options considered, and decision outcomes. Explore updates including a new section on suggested characteristics of an individual record with guidelines for maintaining consistent and useful ADRs.

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
