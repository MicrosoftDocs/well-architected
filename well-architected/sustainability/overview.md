---
title: Sustainable workloads on Azure
description: Overview of sustainable workloads on Azure that are highly reliable.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 06/25/2026
ms.topic: concept-article
---

# Sustainable workloads on Azure

Sustainability for workloads in the cloud is a shared responsibility between the cloud provider and the organization. Azure workloads are carbon neutral and use 100% renewable energy. Microsoft also protects more land. By running a workload in Azure, you're already on the path to sustainability compared to running it on-premises, which can be between 50-98% less carbon efficient.

As a cloud solution architect, your design decisions directly shape how efficiently a workload uses cloud resources. Without deliberate design choices, a workload can consume avoidable energy over time and become an environmental burden as it scales.

This guidance helps you design sustainability into your architecture from the start. It focuses on the key design and operational choices you make in real systems and how to navigate them while maintaining reliability, security, and performance. You learn how common architectural decisions influence resource consumption, where inefficiencies typically emerge, and how to reduce waste without compromising system quality.

## What is a sustainable workload?

Sustainability has become a critical cloud architecture concern as hyperscale datacenters continue to expand, AI workloads drive unprecedented energy demand, and organizations accumulate always-on infrastructure that often operates far below capacity. Modern cloud environments frequently suffer from sprawl: idle virtual machines, oversized Kubernetes clusters, excessive telemetry, and unnecessary data replication all consume energy without delivering meaningful business value.

A sustainable workload is designed to minimize unnecessary resource consumption while still meeting business, reliability, security, and performance requirements. Sustainability isn't a separate architectural discipline; it's closely tied to the same engineering decisions that shape resilient, secure, and efficient systems. Workloads that are optimized for utilization, scalability, and operational efficiency are typically more sustainable because they reduce wasted compute, storage, and network resources.

Sustainability strongly overlaps with the Cost Optimization pillar of the Azure Well-Architected Framework. Right-sizing infrastructure, using autoscaling effectively, and eliminating idle or redundant resources all improve cloud efficiency while also reducing unnecessary energy consumption and carbon emissions. In many cases, the first step toward sustainability is simply removing waste from the system.

:::image type="content" source="./images/sustainability-shared-responsibility-cloud-efficiency.png" alt-text="Diagram showing how to achieve cloud efficiency." border="false" lightbox="./images/sustainability-shared-responsibility-cloud-efficiency.png":::

> [!IMPORTANT]
> Sustainability extends beyond cost efficiencies. Every design decision, whether functional or non-functional, can impact carbon emission. 
>
> A workload can be inexpensive to operate while still being environmentally inefficient. Excessive telemetry collection, poorly optimized application flows, unnecessary data movement, inefficient APIs, or over-engineered resiliency patterns can all contribute to the environmental footprint of a workload even if infrastructure costs appear acceptable.

## What are the common challenges?

Designing sustainable workloads is often difficult because sustainability introduces a new set of architectural tradeoffs that extend beyond traditional concerns like cost, reliability, and performance. While many sustainability improvements align with cloud efficiency, they also require organizations to rethink how applications are designed, operated, and measured.

| Challenge and description | Sustainability impact                                                                              |
| --- | -------------------------------------------------------------------------------------------------- |
| **Balancing sustainability with reliability.** Highly resilient systems often require redundancy, replication, and excess capacity through multiregion deployments or active-active architectures. | Increased infrastructure usage and energy consumption from overprovisioned or always-on resources. |
| **Limited visibility into emissions.** Many organizations lack tooling and telemetry to measure workload emissions or correlate architectural decisions to environmental impact. | Difficult to track improvements or validate whether optimization efforts are reducing emissions.   |
| **Overprovisioning and idle infrastructure.** Workloads are frequently designed for peak demand instead of actual utilization, resulting in idle VMs, oversized AKS clusters, and unused resources. | Unnecessary compute, storage, and network consumption increases both cost and carbon emissions.    |
| **Sustainability tradeoffs in performance optimization.** Improving latency and throughput can require additional caching, replication, memory, or compute resources. | Performance gains may come at the expense of higher operational energy consumption.                |
| **Excessive observability and telemetry.** Large volumes of logs, traces, metrics, and long retention periods generate significant storage and processing overhead. | Increased storage, networking, and compute usage from operational telemetry.                       |
| **AI and data growth.** AI workloads, vector databases, and rapidly growing datasets consume significant GPU, storage, and networking resources. | High compute intensity and uncontrolled data growth can dramatically increase workload emissions.  |
| **Organizational and cultural challenges.** Sustainability is often treated separately from engineering and platform operations. | Lack of ownership and governance can slow down sustainability adoption. Without continuous efforts in evolving sustainability, cloud sprawl and inefficiencies naturally accumulate over time.           |

## Sustainability as a well-architected outcome

Sustainability is not separate from the Azure Well-Architected Framework. In many cases, sustainable workloads are the direct result of applying Well-Architected principles consistently across the entire workload lifecycle. Efficient systems consume fewer resources, generate less operational waste, and typically perform better both financially and operationally.

How to use this guidance:

✔ Start with the **Design methodology**, which outlines the rationale and recurring themes across technical and operational areas.  Revisit this methodology when you face uncertain choices to stay aligned with the workload's overall goals, including efficiency and sustainability objectives.

✔ Proceed to the **Well-Architected Framework pillar recommendations**, where architecture decisions are made through the lens of efficiency, optimization, and disciplined operations. Sustainability naturally follows. 

| Well-Architected pillar | Sustainability relationship |
| --- | --- |
| [Reliability](sustainability-reliability-recommendations.md) | Reliable systems must balance resiliency with efficiency. Highly redundant architectures, active-active deployments, and excessive failover capacity can increase environmental impact if they're not carefully designed. Sustainable reliability focuses on achieving resiliency objectives without unnecessary overprovisioning. |
| [Security](sustainability-security-recommendations.md) | Security also influences sustainability. Identity-first architectures, managed security services, and efficient governance controls can reduce infrastructure complexity and operational overhead. Conversely, fragmented tooling, duplicated security platforms, and excessive data retention can significantly increase resource consumption. |
| [Cost Optimization](sustainability-cost-optimization-recommendations.md) | The strongest overlap exists between sustainability and Cost Optimization. Right-sizing infrastructure, autoscaling workloads, eliminating idle resources, and reducing unnecessary storage or network usage all improve cloud efficiency while also lowering environmental impact. Removing waste from a workload is often the first and most effective sustainability improvement. |
| [Operational Excellence](sustainability-operational-excellence-recommendations.md) | Operations have a significant impact on sustainability. Excessive telemetry collection, overly verbose logging, inefficient deployment pipelines, and unmanaged operational sprawl can increase storage, networking, and compute consumption over time. Sustainable operations require disciplined observability strategies, automation, governance, and continuous optimization processes. |
| [Performance Efficiency](sustainability-performance-efficiency-recommendations.md) | Efficient applications typically consume fewer compute resources to perform the same work. Optimized APIs, caching strategies, asynchronous processing, efficient database access patterns, and event-driven architectures all reduce unnecessary processing overhead. Performance optimization is often sustainability optimization. |

Microsoft is committed to helping you optimize your Azure carbon emissions. Additional material can be found here: [Carbon optimization in Azure](/azure/carbon-optimization/).


## Next step

Review the sustainability design methodology.

> [!div class="nextstepaction"]
> [Design methodology](sustainability-design-methodology.md)


## Related resources

At Microsoft, we align with industry standards on greenhouse gas (GHG) emissions with the [Greenhouse Gas Protocol](https://ghgprotocol.org/). Review these additional resources:

- Explore [Microsoft Cloud for Sustainability](https://www.microsoft.com/sustainability/cloud) to extend your solution capabilities. This article series applies to *any* solution you build or operate on Azure.

- Read [The Carbon Benefits of Cloud Computing: a Study of the Microsoft Cloud](https://www.microsoft.com/download/details.aspx?id=56950) for evidence that Azure can be more energy- and carbon-efficient than on-premises solutions.

- Use [Azure Carbon Optimization](/azure/carbon-optimization/overview) service within Azure that shows the precalculated equivalent carbon emissions for all subscriptions and resources and workload-specific suggestions on how to reduce emissions.

- [Emissions Impact Dashboard for Azure](https://appsource.microsoft.com/en-us/product/power-bi/coi-sustainability.emissions_impact_dashboard): A Microsoft AppSource listing for a Power BI solution that helps Azure customers calculate and track cloud-related carbon emissions.

- Have a good understanding of how Microsoft datacenters are sustainable by design by reading [Understanding Microsoft Datacenters](https://news.microsoft.com/datacenters/).

- Learn about [Advancing sustainability](https://www.microsoft.com/corporate-responsibility/sustainability) by understanding Microsoft's corporate responsibility sustainability hub, covering its climate commitments, reported progress, investments, and broader sustainability programs.

- Discover self-paced learning content on digital sustainability from Microsoft at [Sustainability Microsoft Learn Collection](/collections/5pmf232x788ez).

## Community resources

- [How do I start a sustainability community in my organization?](https://devblogs.microsoft.com/sustainable-software/how-do-i-start-a-sustainability-community/): A Microsoft Sustainable Software blog post with practical guidance for starting and growing a sustainability community inside an organization.

- [GitHub Climate Action Plan for Developers](https://github.com/social-impact/focus-areas/environmental-sustainability/climate-action-plan-for-developers)

- [Greenhouse Gas Protocol](https://ghgprotocol.org/): The official site for the Greenhouse Gas Protocol, which publishes widely used standards, guidance, and tools for measuring and managing greenhouse gas emissions.

- [Software Carbon Intensity (SCI) Specification](https://github.com/Green-Software-Foundation/sci): The GitHub repository for the Green Software Foundation's SCI specification, which defines how to calculate a carbon intensity score for software.

