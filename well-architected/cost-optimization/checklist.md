---
title: Design review checklist for Cost Optimization 
description: Learn about recommendations that help you balance actual costs versus perceived value, team efficiency, focus, effort, and help you meet your requirements.
author: stephen-sumner 
ms.author: ssumner 
ms.date: 11/15/2023 
ms.topic: conceptual
---
# Design review checklist for Cost Optimization

This checklist presents a set of recommendations about cost optimization for your workload to help you achieve a high return on investment (ROI) based on the business value that your workload delivers. Cost optimization balances actual costs versus perceived value, team efficiency, focus, and effort, while meeting the defined functional and nonfunctional requirements of your workload.

Every workload has direct and indirect costs, and every workload is designed to deliver value. If you don't incorporate the recommendations in this article and consider the tradeoffs, your design might not make the best use of your time and money. Carefully consider the points covered in the following checklist to instill confidence in your design's success.

Cost optimization is a continuous process in which you optimize workload costs and align your workload with the broader governance discipline of cost management. What's important today might not be important tomorrow. Technology choices or options and features that your platform offers today might be different. Learn from production and nonproduction environments, be aware of platform changes, and apply your findings to your workload and your workload's dependencies.

|&nbsp; | Code  |Recommendation |
|---|---|---|
| &#9744; | [CO:01](create-culture-financial-responsibility.md) | **Create a culture of financial responsibility.** Regularly train personnel so technical skills remain sharp. Foster creativity and fiscal accountability in the work environment. Invest in tooling and implementing automation.|
| &#9744; | [CO:02](cost-model.md) | **Create and maintain a cost model.** A cost model should estimate the initial cost, run rates, and ongoing costs. Negotiate a budget that covers a cost model and has a buffer for unplanned spending.|
| &#9744; | [CO:03](collect-review-cost-data.md) | **Collect and review cost data.** Data collection should capture daily costs. In cost reports, include incurred costs (metered), prepaid costs (amortized), trends, and forecasts. Stakeholders should regularly review spending against the budget and cost model. Automate alerts to trigger notifications at key thresholds and detect anomalies to indicate deviations from trend baselines. |
| &#9744; | [CO:04](set-spending-guardrails.md) | **Set spending guardrails.** Guardrails should include release gates, governance policies, resource limits, and access controls. Prioritize platform automation over manual processes. |
| &#9744; | [CO:05](get-best-rates.md) | **Get the best rates from providers.** You should find and use the best rates for cloud resources and licenses. Regularly review cost savings. Cost reviews should include regional pricing, pricing tiers, pricing models (consumption or fixed), reservations, savings plans, license portability, corporate purchasing plans, price sheets, and precommitments.|
| &#9744; | [CO:06](align-usage-to-billing-increments.md)| **Align usage to billing increments.** You should understand billing increments (meters) and align resource usage to those increments. Modify the service to align with billing increments, or modify resource usage to aling with billing increments. Consider using a proof-of-concept to validate billing knowledge and design choices for major cost drivers and to reveal ways to align billing and resource usage.|
| &#9744; | [CO:07](eliminate-waste.md) | **Eliminate waste.** Use only what you need in workload components and application features. When possible, use prepaid reservations and savings plans. |
| &#9744; | [CO:08](optimize-environment-costs.md) | **Optimize environment costs.** Align spending to prioritize preproduction, production, operations, and disaster recovery environments. For each environment, consider the required availability, licensing, operating hours and conditions, and security. Nonproduction environments should emulate the production environment. Implement strategic tradeoffs into nonproduction environments.|
| &#9744; | [CO:09](optimize-flow-costs.md) | **Optimize flow costs.** Align the cost of each flow with flow priority. When you prioritize flows, consider the features, functionality, and nonfunctional requirements of each flow. Optimizing flow spend often requires strategic compromises.|
| &#9744; | [CO:10](optimize-data-costs.md) | **Optimize data costs.** Align data costs with data priority. Data optimization should include automatic data tiering and data retention policies.|
| &#9744; | [CO:11](optimize-code.md) | **Optimize code.** Evaluate and modify code to meet functional and nonfunctional requirements with fewer or cheaper resources. |
| &#9744; | [CO:12](optimize-scaling.md) | **Optimize scaling.** Evaluate alternative scaling within your scale units. Consider alternative scaling configurations, and align with the cost model. Considerations should include utilization against the inherit limits of every instance, resource, and scale unit boundary. Use strategies for controlling demand and supply. |
| &#9744; | [CO:13](optimize-personnel-time.md) | **Optimize personnel time.** Align the time personnel spends on tasks with the priority of the task. The goal is to reduce the time spent on tasks without degrading the outcome. Optimization efforts should include minimizing noise, reducing build times, high fidelity debugging, and production mocking.|
| &#9744; | [CO:14](consolidation.md) | **Consolidate resources and responsibility.** Look within the workload for ways to consolidate resources and increase density. Outside the workload, use existing centralized resources and services that enable you to consolidate workload responsibilities.|

## Next steps

We recommend that you review the Cost Optimization tradeoffs to explore other concepts.

> [!div class="nextstepaction"]
> [Cost Optimization tradeoffs](tradeoffs.md)
