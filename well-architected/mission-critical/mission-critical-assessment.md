---
title: Well-Architected assessment for mission-critical workloads on Azure
description: Check the readiness of your mission-critical workload by using an assessment review tool. 
author: calcof
ms.author: prwilk
ms.date: 03/15/2023
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - management-and-governance
  - web
products: Azure
ms.custom:
  - mission-critical
---

# Well-Architected assessment for mission-critical workloads

The [Assessment](/assessments/?mode=pre-assessment&id=23513bdb-e8a2-4f0b-8b6b-191ee1f52d34) is a review tool for self-assessing the readiness of your mission-critical workload in production. Working towards building a resilient mission critical architecture can be a complex process. The assessment is organized in a way that you'll be able to methodically check the alignment to the best practices for resiliency, reliability, and availability.

We recommend that the team doing the assessment is well versed in the architecture of the specific workload and has a strong understanding of cloud principles and patterns. These roles include, but aren't limited to, cloud architect, operators, DevOps engineer. 

The assessment is a set of questions based on the [mission-critical design methodology](/azure/architecture/framework/mission-critical/mission-critical-design-principles?branch=main) as a way of checking the foundational design choices of a workload’s architecture and the end-to-end operational approach.

[![Screenshot of the mission-critical review tool.](./images/assessment-review.png)](./images/assessment-review.png)

These questions are designed to help benchmark a workload’s maturity and alignment to the recommended approach for operating a mission-critical workload. The outcome of the assessment is the delivery of technical recommendations and documentation that provide guidance on how to implement a highly reliable solution on Azure. 

[![Screenshot of the guidance page in the mission-critical review tool.](./images/assessment-recommendations.png)](images/assessment-recommendations.png)

> [!div class="nextstepaction"]
> [Mission-critical review tool](/assessments/?mode=pre-assessment&id=23513bdb-e8a2-4f0b-8b6b-191ee1f52d34)

 
## Next steps

See these reference architecture that describe design choices for production-ready implementations. 

- [Baseline architecture of an internet-facing application](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-intro)
- [Baseline architecture of an internet-facing application with network controls](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-network-architecture)
