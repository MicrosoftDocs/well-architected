---
title: Microsoft Azure Well-Architected Framework
description: Learn about the five pillars of the Azure Well-Architected Framework and how they can produce a high- quality, stable, and efficient cloud architecture.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/15/2023
ms.topic: conceptual
---

# Microsoft Azure Well-Architected Framework pillars

The Azure Well-Architected Framework pillars drive architectural excellence at the fundamental level of a workload.

Use this matrix to familiarize yourself with the key concepts:

| Pillar | Workload concern | Apply the principles | Strike a balance
|--------|-------------|-------------------|-----------
| [**Reliability**](reliability/index.yml) | Resiliency, availability, recovery | Design for business requirements, resilience, recovery, and operations, while keeping it simple. <br> [Design principles](reliability/principles.md) | [Tradeoffs](reliability/tradeoffs.md) |
| [**Security**](security/index.yml) | Data protection, threat detection, and mitigation | Protect confidentiality, integrity, and availability. <br> [Design principles](security/principles.md)|[Tradeoffs](security/tradeoffs.md) |
| [**Cost Optimization**](cost-optimization/index.yml) | Cost modeling, budgets, reduce waste | Optimize on usage and rate utilization while keeping a cost-efficient mindset. <br> [Design principles](cost-optimization/principles.md) | [Tradeoffs](cost-optimization/tradeoffs.md) |
| [**Operational Excellence**](operational-excellence/index.yml) | Holistic observability, DevOps practices | Streamline operations with standards, comprehensive monitoring, and safe deployment practices. <br> [Design principles](operational-excellence/principles.md)|[Tradeoffs](operational-excellence/tradeoffs.md) |
| [**Performance Efficiency**](performance-efficiency/index.yml) | Scalability, load testing | Scale horizontally, test early and often, and monitor the health of the solution. <br>[Design principles](performance-efficiency/principles.md)|[Tradeoffs](performance-efficiency/tradeoffs.md) |

> [!IMPORTANT]
>
> The reference architectures available in the [**Azure Architecture Center**](/azure/architecture/browse/) are designed with the design principles in mind. The architecture articles describe a prescriptive path for applying the design principles and provide a holistic view on the [**Ten design principles for Azure applications**](/azure/architecture/guide/design-principles/).

## Assessment review tool

Assess your workload by using the core pillars to **identify and prioritize opportunities** for improving the posture of your workloads.

Start your assessment with the [**Azure Well-Architected Review**](/assessments/azure-architecture-review/).

:::image type="content" source="./_images/well-architected-review.png" alt-text="Screenshot of the Microsoft Azure Well-Architected Review." lightbox="./_images/well-architected-review.png":::

## Integrate with Azure recommendation services

- [**Azure Advisor**](/azure/advisor/) uses the **core pillars** as a basis for analyzing your resource configuration and usage telemetry and provides appropriate recommendations.

- [**Azure Advisor score**](/azure/advisor/azure-advisor-score) is a core feature of Azure Advisor that aggregates Advisor recommendations into a simple, actionable score. You can categorize the overall score into the core pillars of the Well-Architected Framework. Use the score to prioritize the actions that yield the biggest improvement.


### Microsoft Well-Architected Framework Pillars Design Principles Mind Map

```mermaid	
mindmap
    root((Pillars))        
        Reliability(Reliability)
            DesignPrinciples(Design Principles)
                Design for business requirements["**Design for business requirements:**
                Gather business requirements with a focus on the intended utility of the workload."]
                Design for resilience["**Design for resilience:**
                The workload must continue to operate with full or reduced functionality."]
                Design for recovery["**Design for recovery:**
                The workload must be able to anticipate and recover from most failures, of all magnitudes, with minimal disruption to the user experience and business objectives."]
                Design for operations["**Design for operations:**
                Shift left in operations to anticipate failure conditions."]
                Keep it simple["**Keep it simple:**
                Avoid overengineering the architecture design, application code, and operations."]
        Security(Security)
            DesignPrinciples(Design Principles)
                Plan your security readiness["**Plan your security readiness:**
                Strive to adopt and implement security practices in architectural design decisions and operations with minimal friction."]
                Design to protect confidentiality["**Design to protect confidentiality:**
                Prevent exposure to privacy, regulatory, application, and proprietary information through access restrictions and obfuscation techniques."]
                Design to protect integrity["**Design to protect integrity:**
                Prevent corruption of design, implementation, operations, and data to avoid disruptions that can stop the system from delivering its intended utility or cause it to operate outside the prescribed limits. The system should provide information assurance throughout the workload lifecycle."]
                Design to protect availability["**Design to protect availability:**
                Prevent or minimize system and workload downtime and degradation in the event of a security incident by using strong security controls. You must maintain data integrity during the incident and after the system recovers."]
                Sustain and evolve your security posture["**Sustain and evolve your security posture:**
                 Incorporate continuous improvement and apply vigilance to stay ahead of attackers who are continuously evolving their attack strategies."]       
        CostOptimization(Cost Optimization)
            DesignPrinciples(Design Principles)
                Develop cost-management discipline["**Develop cost-management discipline:**
                Build a team culture that has awareness of budget, expenses, reporting, and cost tracking."]
                Design with a cost-efficiency mindset["**Design with a cost-efficiency mindset:**
                Spend only on what you need to achieve the highest return on your investments."]
                Design for usage optimization["**Design for usage optimization:**
                Maximize the use of resources and operations. Apply them to the negotiated functional and nonfunctional requirements of the solution."]
                Design for rate optimization["**Design for rate optimization:**
                Increase efficiency without redesigning, renegotiating, or sacrificing functional or nonfunctional requirements."]
                Monitor and optimize over time["**Monitor and optimize over time:**
                Continuously right-size investment as your workload evolves with the ecosystem."]
        OperationalExcellence(Operational Excellence)
            DesignPrinciples(Design Principles)
               Embrace DevOps culture["**Embrace DevOps culture:**
               Empower development and operations teams to continuously improve their system design and processes by working together with a mindset of collaboration, shared responsibility, and ownership."]
               Establish development standards["**Establish development standards:**
               Optimize productivity by standardizing development practices, enforcing quality gates, and tracking progress and success through systematic change management."]
               Evolve operations with observability["**Evolve operations with observability:**
                Gain visibility into the system, derive insight, and make data-driven decisions."]
               Deploy with confidence["**Deploy with confidence:**
               Reach the desired state of deployment with predictability."]
               Automate for efficiency["**Automate for efficiency:**
                Replace repetitive manual tasks with software automation that completes them quicker, with greater consistency and accuracy, and reduces risks."]
               Adopt safe deployment practices["**Adopt safe deployment practices:**
               Implement guardrails in the deployment process to minimize the effect of errors or unexpected conditions."]
        PerformanceEfficiency(Performance Efficiency)        
            DesignPrinciples(Design Principles)
               Negotiate realistic performance targets["**Negotiate realistic performance targets:**
               The intended user experience is defined, and there's a strategy to develop a benchmark and measure targets against the pre-established business requirements."]
               Design to meet capacity requirements["**Design to meet capacity requirements:**
               Provide enough supply to address anticipated demand."]
               Achieve and sustain performance["**Achieve and sustain performance:**
                Protect against performance degradation while the system is in use and as it evolves."]
               Improve efficiency through optimization["**Improve efficiency through optimization:**
                Improve system efficiency within the defined performance targets to increase workload value."]    
```

## Microsoft Well-Architected Framework Pillars Tradeoffs Mind Map

```mermaid
mindmap
    root((Pillars))        
        Reliability(Reliability)
            Tradeoffs(Tradeoffs)
                Reliability tradeoffs with Security["`**Reliability tradeoffs with Security**`"]
                     Tradeoff: Increased workload surface area. The Security pillar prioritizes a reduced and contained surface area to minimize attack vectors and reduce the management of security controls.["`**Tradeoff: Increased workload surface area.** The Security pillar prioritizes a reduced and contained surface area to minimize attack vectors and reduce the management of security controls.`"]
                     Tradeoff: Security control bypass. The Security pillar recommends that all controls remain active in both normal and stressed systems.["`**Tradeoff: Security control bypass.** The Security pillar recommends that all controls remain active in both normal and stressed systems.`"]
                     Tradeoff: Old software versions. The Security pillar encourages a *get current, stay current* approach to vendor security patches.["`**Tradeoff: Old software versions.** The Security pillar encourages a *get current, stay current* approach to vendor security patches.`"]
                Reliability tradeoffs with Cost Optimization["`**Reliability tradeoffs with Cost Optimization**`"]
                    Tradeoff: Increased implementation redundancy or waste. A cost-optimized workload minimizes underutilized resources and avoids over-provisioning resources.["`**Tradeoff: Increased implementation redundancy or waste.** A cost-optimized workload minimizes underutilized resources and avoids over-provisioning resources.`"]
                    Tradeoff: Increased investment in operations that aren't aligned with functional requirements. One approach to cost optimization is evaluating the value that's provided by any deployed solution.["`**Tradeoff: Increased investment in operations that aren't aligned with functional requirements.** One approach to cost optimization is evaluating the value that's provided by any deployed solution.`"]
                Reliability tradeoffs with Operational Excellence["`**Reliability tradeoffs with Operational Excellence**`"]
                    Tradeoff: Increased operational complexity. Operational Excellence, like Reliability itself, prioritizes simplicity.["`**Tradeoff: Increased operational complexity.** Operational Excellence, like Reliability itself, prioritizes simplicity.`"]
                    Tradeoff: Increased effort to generate team knowledge and awareness. The Operational Excellence pillar recommends keeping and maintaining a documentation repository for procedures and topologies. ["`**Tradeoff: Increased effort to generate team knowledge and awareness.** The Operational Excellence pillar recommends keeping and maintaining a documentation repository for procedures and topologies.`"]
                Reliability tradeoffs with Performance Efficiency["`**Reliability tradeoffs with Performance Efficiency**`"]
                    Tradeoff: Increased latency. Performance Efficiency requires a system to achieve performance targets for user and data flows.["`**Tradeoff: Increased latency.** Performance Efficiency requires a system to achieve performance targets for user and data flows.`"]
                    Tradeoff: Increased over-provisioning. The Performance Efficiency pillar discourages over-provisioning, instead recommending the use of just enough resources to satisfy demand.["`**Tradeoff: Increased over-provisioning.** The Performance Efficiency pillar discourages over-provisioning, instead recommending the use of just enough resources to satisfy demand.`"]
        Security(Security)
            Tradeoffs(Tradeoffs)
                Security tradeoffs with Reliability["`**Security tradeoffs with Reliability**`"]
                    Tradeoff: Increased complexity. The Reliability pillar prioritizes simplicity and recommends that points of failure are minimized.["`**Tradeoff: Increased complexity.** The Reliability pillar prioritizes simplicity and recommends that points of failure are minimized.`"]
                    Tradeoff: Increased critical dependencies. The Reliability pillar recommends minimizing critical dependencies. A workload that minimizes critical dependencies, especially external ones, has more control over its points of failure.["`**Tradeoff: Increased critical dependencies.** The Reliability pillar recommends minimizing critical dependencies. A workload that minimizes critical dependencies, especially external ones, has more control over its points of failure.`"]
                    Tradeoff: Increased complexity of disaster recovery. A workload must reliably recover from all forms of disaster.["`**Tradeoff: Increased complexity of disaster recovery.** A workload must reliably recover from all forms of disaster.`"]
                    Tradeoff: Increased rate of change. A workload that experiences runtime change is exposed to more risk of reliability impact due to that change.["`**Tradeoff: Increased rate of change.** A workload that experiences runtime change is exposed to more risk of reliability impact due to that change.`"]
                Security tradeoffs with Cost Optimization["`**Security tradeoffs with Cost Optimization**`"]
                    Tradeoff: Additional infrastructure. One approach to cost optimizing a workload is to look for ways to reduce the diversity and number of components and increase density.["`**Tradeoff: Additional infrastructure.** One approach to cost optimizing a workload is to look for ways to reduce the diversity and number of components and increase density.`"]
                    Tradeoff: Increased demand on infrastructure. The Cost Optimization pillar prioritizes driving down demand on resources to enable the use of cheaper SKUs, fewer instances, or reduced consumption.["`**Tradeoff: Increased demand on infrastructure.** The Cost Optimization pillar prioritizes driving down demand on resources to enable the use of cheaper SKUs, fewer instances, or reduced consumption.`"]
                    Tradeoff: Increased process and operational costs. Personnel process costs are part of the overall total cost of ownership and are factored into a workload's return on investment. Optimizing these costs is a recommendation of the Cost Optimization pillar.["`**Tradeoff: Increased process and operational costs.** Personnel process costs are part of the overall total cost of ownership and are factored into a workload's return on investment. Optimizing these costs is a recommendation of the Cost Optimization pillar.`"]
                Security tradeoffs with Operational Excellence["`**Security tradeoffs with Operational Excellence**`"]
                    Tradeoff: Complications in observability and serviceability. Operational Excellence requires architectures to be serviceable and observable. The most serviceable architectures are those that are the most transparent to everyone involved.["`**Tradeoff: Complications in observability and serviceability.** Operational Excellence requires architectures to be serviceable and observable. The most serviceable architectures are those that are the most transparent to everyone involved.`"]
                    Tradeoff: Decreased agility and increased complexity. Workload teams measure their velocity so that they can improve the quality, frequency, and efficiency of delivery activities over time. Workload complexity factors into the effort and risk involved in operations["`**Tradeoff: Decreased agility and increased complexity.** Workload teams measure their velocity so that they can improve the quality, frequency, and efficiency of delivery activities over time. Workload complexity factors into the effort and risk involved in operations`"]
                    Tradeoff: Increased coordination efforts. A team that minimizes external points of contact and review can control their operations and timeline more effectively.["`**Tradeoff: Increased coordination efforts.** A team that minimizes external points of contact and review can control their operations and timeline more effectively.`"]             
                Security tradeoffs with Performance Efficiency["`**Security tradeoffs with Performance Efficiency**`"]
                    Tradeoff: Increased latency and overhead. A performant workload reduces latency and overhead.["`**Tradeoff: Increased latency and overhead.** A performant workload reduces latency and overhead.`"]
                    Tradeoff: Increased chance of misconfiguration. Reliably meeting performance targets depends on predictable implementations of the design.["`**Tradeoff: Increased chance of misconfiguration.** Reliably meeting performance targets depends on predictable implementations of the design.`"]
        Cost Optimization["Cost Optimization`"]
            Tradeoffs(Tradeoffs)
                Cost Optimization tradeoffs with Reliability["`**Cost Optimization tradeoffs with Reliability**`"]
                    Tradeoff: Reduced resiliency. A workload incorporates resiliency measures to attempt to avoid and withstand specific types and quantities of malfunction.["`**Tradeoff: Reduced resiliency.** A workload incorporates resiliency measures to attempt to avoid and withstand specific types and quantities of malfunction.`"]
                    Tradeoff: Limited recovery strategy. A workload that's reliable has a tested incident response and recovery plan for disaster scenarios.["`**Tradeoff: Limited recovery strategy.** A workload that's reliable has a tested incident response and recovery plan for disaster scenarios.`"]
                    Tradeoff: Increased complexity. A workload that uses straightforward approaches and avoids unnecessary or overengineered complexity is generally easier to manage in terms of reliability.["`**Tradeoff: Increased complexity.** A workload that uses straightforward approaches and avoids unnecessary or overengineered complexity is generally easier to manage in terms of reliability.`"]
                Cost Optimization tradeoffs with Security["`**Cost Optimization tradeoffs with Security**`"]
                    Tradeoff: Reduced security controls. Security controls are established across multiple layers, sometimes redundantly, to provide defense in depth.["`**Tradeoff: Reduced security controls.** Security controls are established across multiple layers, sometimes redundantly, to provide defense in depth.`"]
                    Tradeoff: Increased workload surface area. The Security pillar prioritizes a reduced and contained surface area to minimize attack vectors and the management of security controls.["`**Tradeoff: Increased workload surface area.** The Security pillar prioritizes a reduced and contained surface area to minimize attack vectors and the management of security controls.`"]
                    Tradeoff: Removed segmentation. The Security pillar prioritizes strong segmentation to support the application of targeted security controls and to control the blast radius.["`**Tradeoff: Removed segmentation.** The Security pillar prioritizes strong segmentation to support the application of targeted security controls and to control the blast radius.`"]
                Cost Optimization tradeoffs with Operational Excellence["`**Cost Optimization tradeoffs with Operational Excellence**`"]
                    Tradeoff: Compromised software development lifecycle SDLC capacities. A workload's SDLC process provides rigor, consistency, specificity, and prioritization to change management in a workload.["`**Tradeoff: Compromised software development lifecycle capacities.** A workload's SDLC process provides rigor, consistency, specificity, and prioritization to change management in a workload.`"]
                    Tradeoff: Reduced observability. Observability is necessary to help ensure that a workload has meaningful alerting and successful incident response.["`**Tradeoff: Reduced observability.** Observability is necessary to help ensure that a workload has meaningful alerting and successful incident response.`"]
                    Tradeoff: Deferred maintenance. Workload teams are expected to keep code, tooling, software packages, and operating systems patched and up to date in a timely and orderly way.["`**Tradeoff: Deferred maintenance.** Workload teams are expected to keep code, tooling, software packages, and operating systems patched and up to date in a timely and orderly way.`"]
                Cost Optimization tradeoffs with Performance Efficiency["`**Cost Optimization tradeoffs with Performance Efficiency**`"]
                    Tradeoff: Underprovisioned or underscaled resources. A performance-efficient workload has enough resources to serve demand but doesn't have excessive unused overhead, even when usage patterns fluctuate.["`**Tradeoff: Underprovisioned or underscaled resources.** A performance-efficient workload has enough resources to serve demand but doesn't have excessive unused overhead, even when usage patterns fluctuate.`"]
                    Tradeoff: Lack of optimization over time. Evaluating the effects of changes in functionality, changes in usage patterns, new technologies, and different approaches on the workload is one way to try to increase efficiency.["`**Tradeoff: Lack of optimization over time.** Evaluating the effects of changes in functionality, changes in usage patterns, new technologies, and different approaches on the workload is one way to try to increase efficiency.`"]
        Operational Excellence["Operational Excellence"]
            Tradeoffs(Tradeoffs)
                Operational Excellence tradeoffs with Reliability["`**Operational Excellence tradeoffs with Reliability**`"]
                    Tradeoff: Increased complexity. Reliability prioritizes simplicity, because simple design minimizes misconfiguration and reduces unexpected interactions.["`**Tradeoff: Increased complexity.** Reliability prioritizes simplicity, because simple design minimizes misconfiguration and reduces unexpected interactions.`"]
                    Tradeoff: Increased potentially destabilizing activities. The Reliability pillar encourages the avoidance of activities or design choices that can destabilize a system and lead to disruptions, outages, or malfunctions["`**Tradeoff: Increased potentially destabilizing activities.** The Reliability pillar encourages the avoidance of activities or design choices that can destabilize a system and lead to disruptions, outages, or malfunctions.`"]
                Operational Excellence tradeoffs with Security["`**Operational Excellence tradeoffs with Security**`"]
                    Tradeoff: Increased surface area. The Security pillar recommends a reduced workload surface area in terms of components and exposure to operations. This reduction minimizes attack vectors and produces a smaller scope for security control and testing.["`**Tradeoff: Increased surface area.** The Security pillar recommends a reduced workload surface area in terms of components and exposure to operations. This reduction minimizes attack vectors and produces a smaller scope for security control and testing.`"]
                    Tradeoff: Increased desire for transparency. A secure workload is based on designs that protect the confidentiality of data that flows through the components of the system.["`**Tradeoff: Increased desire for transparency.** A secure workload is based on designs that protect the confidentiality of data that flows through the components of the system.`"]
                    Tradeoff: Reduced segmentation. A key security approach for isolating access and function is to design a strong segmentation strategy. This design is implemented through resource isolation and identity controls.["`**Tradeoff: Reduced segmentation.** A key security approach for isolating access and function is to design a strong segmentation strategy. This design is implemented through resource isolation and identity controls.`"]                    
                Operational Excellence tradeoffs with Cost Optimization["`**Operational Excellence tradeoffs with Cost Optimization**`"]
                     Tradeoff: Increased resource spending. A major cost driver for a workload is the cost of its resources. Deploying fewer resources, right-sizing resources, and reducing consumption generally helps keep costs low.["`**Tradeoff: Increased resource spending.** A major cost driver for a workload is the cost of its resources. Deploying fewer resources, right-sizing resources, and reducing consumption generally helps keep costs low.`"]
                     Tradeoff: Decreased focus on delivery activities. Workload team members deliver increased workload value by efficiently performing tasks that are aligned to their capabilities.["`**Tradeoff: Decreased focus on delivery activities.** Workload team members deliver increased workload value by efficiently performing tasks that are aligned to their capabilities.`"]
                     Tradeoff: Increased tooling demands and diversity. The Cost Optimization pillar recommends the reduction of tooling sprawl, consolidation of vendors, and a right-sized approach to all tooling purchases.["`**Tradeoff: Increased tooling demands and diversity.** The Cost Optimization pillar recommends the reduction of tooling sprawl, consolidation of vendors, and a right-sized approach to all tooling purchases.`"]
                Operational Excellence tradeoffs with Performance Efficiency["`**Operational Excellence tradeoffs with Performance Efficiency**`"]
                    Tradeoff: Increased resource utilization. The Performance Efficiency pillar recommends the allocation of as much of the available compute and network as possible to the requirements of the workload.["`**Tradeoff: Increased resource utilization.** The Performance Efficiency pillar recommends the allocation of as much of the available compute and network as possible to the requirements of the workload.`"]
                    Tradeoff: Increased latency. To create performant workloads, teams look for ways to reduce the time and resources that workloads consume to perform their tasks.["`**Tradeoff: Increased latency.** To create performant workloads, teams look for ways to reduce the time and resources that workloads consume to perform their tasks.`"]
        Performance Efficiency("Performance Efficiency")
            Tradeoffs(Tradeoffs)
                Performance Efficiency tradeoffs with Reliability["`**Performance Efficiency tradeoffs with Reliability**`"]
                    Tradeoff: Reduced replication and increased density. A cornerstone of reliability is ensuring resilience by using replication and limiting the blast radius of malfunctions.["`**Tradeoff: Reduced replication and increased density.** A cornerstone of reliability is ensuring resilience by using replication and limiting the blast radius of malfunctions.`"]
                    Tradeoff: Increased complexity. Reliability prioritizes simplicity.["`**Tradeoff: Increased complexity.** Reliability prioritizes simplicity.`"]
                    Tradeoff: Testing and observation on active environments. Avoiding the unnecessary use of production systems is a self-preservation approach for reliability.["`**Tradeoff: Testing and observation on active environments.** Avoiding the unnecessary use of production systems is a self-preservation approach for reliability.`"]                  
                Performance Efficiency tradeoffs with Security["`**Performance Efficiency tradeoffs with Security**`"]
                    Tradeoff: Reduction of security controls. Security controls are established across multiple layers, sometimes redundantly, to provide defense in depth["`**Tradeoff: Reduction of security controls.** Security controls are established across multiple layers, sometimes redundantly, to provide defense in depth.`"]
                    Tradeoff: Increased workload surface area. Security prioritizes a reduced and contained surface area to minimize attack vectors and reduce the management of security controls.["`**Tradeoff: Increased workload surface area.** Security prioritizes a reduced and contained surface area to minimize attack vectors and reduce the management of security controls.`"]
                    Tradeoff: Removing segmentation. The Security pillar prioritizes strong segmentation to enable fine-grained security controls and reduce blast radius.["`**Tradeoff: Removing segmentation.** The Security pillar prioritizes strong segmentation to enable fine-grained security controls and reduce blast radius.`"]                   
                Performance Efficiency tradeoffs with Cost Optimization["`**Performance Efficiency tradeoffs with Cost Optimization**`"]
                    Tradeoff: Too much supply for demand. Both Cost Optimization and Performance Efficiency prioritize having just enough supply to serve demand.["`**Tradeoff: Too much supply for demand.** Both Cost Optimization and Performance Efficiency prioritize having just enough supply to serve demand.`"]
                    Tradeoff: More components. One cost optimization technique is to consolidate with a smaller number of resources by increasing density, removing duplication, and co-locating functionality.["`**Tradeoff: More components.** One cost optimization technique is to consolidate with a smaller number of resources by increasing density, removing duplication, and co-locating functionality.`"]
                    Tradeoff: Increased investment on items that aren't aligned with functional requirements. One approach to cost optimization is evaluating the value provided by any solution that's deployed.["`**Tradeoff: Increased investment on items that aren't aligned with functional requirements.** One approach to cost optimization is evaluating the value provided by any solution that's deployed.`"]                    
                Performance Efficiency tradeoffs with Operational Excellence["`**Performance Efficiency tradeoffs with Operational Excellence**`"]
                    Tradeoff: Reduced observability. Observability is necessary to provide a workload with meaningful alerting and help ensure successful incident response.["`**Tradeoff: Reduced observability.** Observability is necessary to provide a workload with meaningful alerting and help ensure successful incident response.`"]
                    Tradeoff: Increased complexity in operations. A complex environment has more complex interactions and a higher likelihood of a negative impact from routine, ad hoc, and emergency operations.["`**Tradeoff: Increased complexity in operations.** A complex environment has more complex interactions and a higher likelihood of a negative impact from routine, ad hoc, and emergency operations.`"]
                    Tradeoff: Culture stress. Operational Excellence is rooted in a culture of blamelessness, respect, and continuous improvement.["`**Tradeoff: Culture stress.** Operational Excellence is rooted in a culture of blamelessness, respect, and continuous improvement.`"]
                
```
