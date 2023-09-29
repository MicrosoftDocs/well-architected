---
title: Design review checklist for Cost Optimization 
description: Use this checklist to assess and verify the completeness of your design for cost optimization.   
author: stephen-sumner 
ms.author: ssumner 
ms.date: 11/15/2023 
ms.topic: conceptual
---
# Design review checklist for Cost Optimization

This checklist presents a set of recommendations for your system to achieve a high return on investment (ROI), based on the business value it is delivering. Cost optimization balances actual costs vs. perceived value, team efficiency, focus, and effort, while obtaining negotiated functional and non-functional requirements of the workload.

Every workload has direct and indirect costs, and every workload is designed to deliver value. If you haven’t checked the boxes and considered the tradeoffs, the design might not be making the best use of your investment of time and money. Careful consideration of all the points covered in the checklist will give you confidence in success.

Remember that cost optimization is a continuous process where you optimize costs of workload as well as align with broader governance discipline of cost management. What was important today might not be important tomorrow; what options and features were offered today by your platform or technology choices might be different tomorrow. Take time to learn from production and non-production environments, be aware of platform changes, and apply your learnings to your workload as well as their applicable dependencies.

| &nbsp;| Code  |Recommendation |
|---|---|---|
| &#9744; | [CO:&nbsp;01](create-culture-financial-responsibility.md) | **Create a culture of financial responsibility.** Personnel should receive training that keeps technical skills sharp. The work environment should foster creativity and fiscal accountability. Investments should be made in tooling and implementing automation.|
| &#9744; | [CO:&nbsp;02](cost-model.md) | **Create and maintain a cost model.** The cost model should estimate the initial cost, run rates, and ongoing costs. A budget should be negotiated that covers the cost model and has a buffer for unplanned spending.|
| &#9744; | [CO:&nbsp;03](collect-review-cost-data.md) | **Collect and review cost data.** Data collection should capture daily costs. Cost reports should include incurred costs (metered), pre-paid costs (amortized), trends, and forecasts. Stakeholders should regularly review spending against the budget and cost model. Automated alerts should trigger notifications at key thresholds and anomaly detection should indicate deviations from trend baselines. |
| &#9744; | [CO:&nbsp;04](set-spending-guardrails.md) | **Setting spending guardrails.** Guardrails should include release gates, governance policies, resource limits, and access controls. There should be a preference for platform automation over manual processes. |
| &#9744; | [CO:&nbsp;05](get-the-best-rates.md) | **Get the best rates from providers.** You should find and use the best rates on cloud resources and licenses. Cost savings reviews should be regular. Cost reviews should include regional pricing, pricing tiers, pricing models (consumption, fixed), reservations, savings plans, license portability, corporate purchasing plans, price sheets, and pre-commitments.|
| &#9744; | [CO:&nbsp;06](align-usage-to-billing-increments.md)| **Align usage to billing increments.** You should understand component billing increments (meters) and align usage accordingly. You should align usage to workload design or workload design to usage. A proof-of-concept should validate billing knowledge and design choices for major cost drivers and reveal ways to align billing and usage.|
| &#9744; | [CO:&nbsp;07](eliminate-waste.md) | **Eliminate waste.** Workloads components and application features should have no excess. Prepaid reservations and saving plans should be fully used. |
| &#9744; | [CO:&nbsp;08](optimize-environment-costs.md) | **Optimize environment costs.** Spending should align with the priority of pre-production, production, operations, and disaster recovery environments. Consider the availability, licensing, operating hours/conditions, and security required for each environment. Non-production environments should emulate the production environment with strategic tradeoffs.|
| &#9744; | [CO:&nbsp;09](optimize-flow-costs.md) | **Optimize flow costs.** The cost of each flow should align with flow priority. Prioritizing flows should consider the features, functionality, and non-functional requirements of each flow. Optimizing flow spend often requires strategic compromises.|
| &#9744; | [CO:&nbsp;10](optimize-data-costs.md) | **Optimize data costs.** Data costs should align with data priority. Data optimization should include automatic data tiering and data retention policies.|
| &#9744; | [CO:&nbsp;11](optimize-code.md) | **Optimize code.** Evaluate and modify code to meet functional and non-functional requirements with fewer or cheaper resources. |
| &#9744; | [CO:&nbsp;12](optimize-scaling.md) | **Optimize scaling.** Evaluate alternative scaling within your scale units. Scaling optimization should consider alternative scaling configurations and align with the cost model. Considerations should include utilization against the inherit limits of every instance, resource, and scale unit boundary. Use strategies for controlling demand and supply. |
| &#9744; | [CO: 13](optimize-personnel-time.md) | **Optimize personnel time.** The time personnel spend on tasks should align with the priority of the task. The goal is to reduce the time spent on tasks without degrading the outcome. Optimization efforts should include minimizing noise, reducing build times, high fidelity debugging, and production mocking.|
| &#9744; | [CO: 14](consolidation.md) | **Consolidate resources and responsibility.** Look within the workload for ways to consolidate resources and increase density. Outside the workload, use existing centralized resources and services that allow you to consolidate workload responsibilities.|
