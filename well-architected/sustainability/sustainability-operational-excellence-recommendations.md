---
title: Operational Excellence recommendations for sustainable workloads on Azure
description: Consolidated Operational Excellence-focused recommendations mapped from sustainable workload design areas on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 06/25/2026
ms.topic: concept-article
---

# Operational Excellence recommendations for sustainable workloads on Azure

A key challenge in workload design is balancing sustainability goals with operational and user experience expectations. While efficiency improvements are often driven by Operational Excellence practices such as monitoring, testing, and continuous optimization, these changes can introduce perceived trade-offs in performance, reliability, or development velocity. As a result, sustainability initiatives may create friction between stakeholders and development teams when impacts are not clearly understood or aligned with business priorities. Without shared visibility into operational data and a collaborative decision-making approach, sustainability objectives can be seen as conflicting with delivery goals. To mitigate this risk, it is important to actively engage stakeholders early, communicate expected performance and operational trade-offs transparently, and use foundational Operational Excellence practices to provide evidence-based insights that align environmental objectives with business and user requirements.

This article examines sustainability through the lens of the Operational Excellence pillar and answers these design questions:

- How do you prevent operational processes from becoming a source of waste?
- How do you operationalize sustainability as part of continuous cloud operations?
- How do you reduce repeated operational work across deployment, monitoring, governance, and maintenance activities?
- How do you ensure operational decisions remain efficient as workloads evolve?
- How do you create feedback loops that continuously improve sustainability outcomes?

## Monitor and measure sustainability

Many inefficiencies are hidden because workloads are not profiled deeply enough to expose limitations and inefficient usage patterns. 

Measurement gaps also make it difficult to validate whether sustainability optimizations are producing meaningful results. Carbon reduction efforts frequently rely on assumptions or indirect estimates when emissions targets, operational baselines, and workload-specific metrics are not clearly defined.

Operational feedback loops can also become fragmented across teams and environments. Sustainability metrics, cost signals, application performance indicators, and operational objectives are often tracked independently, making it difficult to evaluate tradeoffs or detect regressions before inefficient changes reach production.

These recommendations provide practical ways to achieve the [Measure and track carbon impact](./sustainability-design-methodology.md#measure-and-track-carbon-impact) mindset. 

| Recommendation                                                                                                                 | Sustainability Benefit                                                                                                     |
| ------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------- |
| Establish a baseline of current carbon emissions for all workloads and services.                                               | Provides a reference point for measuring improvement and demonstrating sustainability progress over time.                  |
| Use emissions monitoring tools such as the [Azure Carbon Optimizer](/azure/carbon-optimization/) to track environmental impact. | Enables data-driven decision-making and helps identify the largest sources of emissions.                                   |
| Define carbon reduction targets for the workload and individual workload components.                                      | Creates clear sustainability objectives and drives accountability for emissions reduction.                                 |
| Track sustainability using a combination of factors, like performance, carbon emissions, or proxies (like [Cost Optimization efforts](./sustainability-cost-optimization-recommendations.md)). Develop a measurable metric, such as [Software Carbon Intensity (SCI) score](https://github.com/Green-Software-Foundation/sci) to track progress.  <br><br>Progress should also be tracked through SLOs, SLAs, or other performance indicators.| Provides a holistic view of workload efficiency and balances environmental and business outcomes.                          |
| Record and assess the impact of all sustainability-related changes and optimizations.                                          | Creates an evidence-based approach to identifying the most effective emissions reduction strategies.                       |


## Use policy to drive behavior to support sustainability

Sustainability objectives may be applied inconsistently across workloads, subscriptions, and teams when governance controls are not in place. Without defined standards, teams may deploy oversized, underutilized, or inefficient resources that increase both costs and carbon emissions. 

Sustainability practices that rely on manual oversight can be difficult to enforce at scale, resulting in uneven adoption and missed optimization opportunities. Embed sustainability objectives into governance processes to ensure that cloud efficiency practices are applied consistently across the organization. Policy-based controls can guide deployment and operational decisions. For example, policies can help in:

- Restricting oversized resources.
- Enforcing resource lifecycle management.
- Requiring tagging for sustainability reporting.
- Preventing deployment of non-approved architectures.
- Encouraging efficient service selections.

| Recommendation                                                                                                          | Sustainability Benefit                                                                                                                                    |
| ----------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Follow organizational governance to enforce sustainability outcomes in workload teams.      | Establishes consistent sustainability practices and reduces variability in resource deployment decisions.                                                 |
| Use existing cost governance controls and policies as part of sustainability initiatives.                          | Reduces unnecessary resource consumption, lowering both operational costs and carbon emissions.                                                           |
| Utilize [built-in Azure Policy controls](/azure/governance/policy/samples/built-in-policies) to enforce efficient resource configurations and operational standards.          | Promotes ongoing cloud efficiency and helps prevent wasteful resource usage.                                                                              |
| Create a dedicated sustainability-focused Azure policy initiative. For example, create a new Azure Policy initiative for "Sustainability"..                     | Embeds sustainability considerations into workload design, deployment, and operational processes.                                                         |
| Implement data lifecycle management policies to ensure that only necessary data is collected, retained, and processed. <br><br>Implement policies using [Microsoft Purview](/azure/purview/overview) to classify and apply retention rules that automatically delete data after defined time periods, ensuring only relevant information is stored. Complement this with [Azure Monitor by using Data Collection Rules (DCRs)](/azure/azure-monitor/essentials/data-collection-rule-overview) to control what telemetry is collected, how it is transformed, and where it is routed. | Reduces storage requirements, data transfer volumes, and processing overhead, helping to lower both operational costs and the environmental impact associated with managing large volumes of data.                                                              |
| Regularly review policy compliance and identify opportunities for further optimization.                                 | Enables continuous improvement and helps maintain long-term sustainability performance.                                                                   |


## Build culture around sustainability

Ensure that sustainability knowledge is continuously shared across teams and that employees understand how their daily decisions can influence carbon emissions, resource consumption, and operational efficiency.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Sustainability initiatives can struggle to gain momentum when knowledge and accountability are concentrated within a small group of individuals rather than embedded across the organization. Without broader awareness of sustainable software engineering principles, teams may miss opportunities to reduce emissions and improve efficiency. Valuable lessons and successful optimization practices can remain isolated within individual teams, limiting their overall impact. To mitigate this risk, organizations should embed sustainability knowledge, accountability, and decision-making across teams rather than relying on a small group of specialists. Foster a culture of collaboration, continuous learning, and shared ownership, so that sustainability objectives become integrated into everyday workload decisions.

In addition, if sustainability goals are not clearly communicated, measured, and recognized, they may be perceived as secondary to other business priorities. Over time, this can result in sustainability considerations being inconsistently incorporated into workload planning, design, and operational decisions, reducing the organization's ability to achieve meaningful and sustained environmental outcomes.

| Recommendation                                                                                                                              | Sustainability Benefit                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Establish a sustainability community or center of excellence responsible for promoting cloud efficiency and green software practices.       | Creates organizational ownership, drives awareness, and helps ensure sustainability goals are consistently applied across workloads. |
| Provide ongoing sustainability and green software training for architects, developers, and operations teams.                                | Improves sustainability knowledge and enables teams to make more environmentally responsible design and operational decisions.       |
| Define sustainability policies, targets, and expectations, and communicate them regularly across the organization.                          | Aligns teams around common objectives and encourages accountability for sustainability outcomes.                                     |
| Create a centralized repository for sustainability guidance, best practices, and lessons learned.                                           | Accelerates adoption of proven optimization approaches and reduces duplication of effort.                                            |
| Encourage regular knowledge-sharing sessions, workshops, or internal webinars focused on sustainability.                                    | Promotes continuous learning and helps scale successful sustainability initiatives across teams.                                     |
| Include sustainability metrics and objectives as part of workload governance, performance reviews, or key performance indicators (KPIs).    | Embeds sustainability into decision-making processes and encourages long-term focus on emissions reduction.                          |
| Recognize and reward teams that achieve sustainability goals through incentives, gamification, or sustainability certifications and badges. | Encourages participation, drives continuous improvement, and fosters a culture of environmental responsibility.                      |
| Promote carbon-aware and carbon-optimized application design practices across all workload teams.                                           | Reduces emissions over time and supports continuous improvement in cloud efficiency and sustainability performance.                  |


## Related guidance

- [Operational Excellence pillar](/azure/well-architected/operational-excellence/)
