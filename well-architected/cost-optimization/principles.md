---
title: Cost Optimization design principles
description: Learn about cost optimization design principles that can help you achieve business objectives and justify costs.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/15/2023
ms.topic: conceptual
categories:
  - management-and-governance
---

# Cost Optimization design principles

Architecture design is always driven by business goals and must **factor in return on investment (ROI) and financial constraints**. Typical questions to consider include:

-   Do the allocated budgets enable you to meet your goals?
-   What's the spending pattern for the application and its operations? What are priority areas?
-   How will you maximize the investment in resources, by better utilization or by reduction?

A cost-optimized workload isn't necessarily a low-cost workload. There are significant tradeoffs. Tactical approaches are reactive and can reduce costs only in the short term. To achieve long-term financial responsibility, you need to **create a strategy with prioritization, continuous monitoring, and repeatable processes** that focuses on optimization.

The **principle approaches aren't checklist items**. Their intended purpose is to provide optimization strategies that you need to consider when you design and implement your workload architecture. Start with the recommended approaches and **justify the benefits for a set of business requirements**. After you set your strategy, drive actions by using the [Cost Optimization checklist](checklist.md) as your next step.

As you prioritize business requirements to align with technology needs, you can adjust costs. However, you should expect a series of **tradeoffs in areas in which you want to optimize cost, such as security, scalability, resilience, and operability**. If the cost of addressing the challenges in those areas is high and these principles aren't applied properly, you might make risky choices in favor of a cheaper solution, ultimately affecting your organization's business goals and reputation.

## Develop cost-management discipline

|![Goal icon](../_images/goal.svg) Build a team culture that has awareness of budget, expenses, reporting, and cost tracking.| 
|--| 

Cost optimization is conducted at various levels of the organization. It's important to understand how your workload is aligned with organizational goals and FinOps practices. A view into the business units, resource organization, and centralized audit policies allows you to adopt a standardized financial system.

|Approach|Benefit|   
|-|-|
| Develop a **cost model**. This fundamental exercise is a prerequisite to setting up a financial tracking system.                                                                   | A cost model helps segment expenses and estimate and **forecast the total cost of ownership**, including infrastructure, support, and implementation. It enables you to identify cost drivers early and predict how any change, growth, or shrinkage will affect overall spending in your projected business model. |
| Have an **effective but flexible accountability model** that's implemented with properly assigned roles and responsibilities.             | As the architecture evolves, various roles participate in decision making. Clear accountability helps **enforce the functional expectations** of each role (given a scope), drive clarity, and generate reports with transparency at desired levels.      |
| Estimate **realistic budgets** that cover all non-negotiable functional and nonfunctional requirements, personnel and training costs, and processes that provide for anticipated growth. | You'll be able to **set financial boundaries** and establish ways to check your spending against the allocated budget. You'll also get notifications when certain thresholds are exceeded, which prevents overspending at the tenant scope, resource scope, and other scopes that are applied to the budget. |
| Use **governance** and processes to implement the accountability model and budgets. | It's not enough to get notifications, because that's reactionary. **Proactive governance** can help you avoid actions that might lead to unnecessary expenditure that's beyond the budget.<br><br>Certain actions can improve the current state. Are retention policies too relaxed? Do you need scalability limits to ensure responsible engineering?  |
| Build capabilities in the system that **capture and classify expense**.    | You'll be able to calculate the costs that **reveal technical and business perspectives** at different billing boundaries.<br><br>You'll also be able to conduct regular reviews and drive showback and chargeback processes.
| Plan on **training costs, hiring expenses, and the cost of infrastructure needed** to augment skills as the workload matures. | Investing in staffing **complements existing skills** through full-time or vendor support. |
| Encourage **upstream communication** from architects and application owners.      | Research costs are reduced when you act on feedback, which should be considered as meaningful as numeric data. You'll empower employees by using their input to **drive realistic design changes** and business strategies.

## Design with a cost-efficiency mindset

|![Goal icon](../_images/goal.svg) Spend only on what you need to achieve the highest return on your investments.| 
|--| 

Every architectural decision has direct and indirect financial implications. Understand the costs associated with build versus buy options, technology choices, the billing model and licensing, training, operations, and so on.

Given a set of requirements, optimize and make tradeoff decisions, in relation to costs, that still effectively address the cross-cutting concerns of the workload.

|Approach    | Benefit |
|-|-|
| **Measure the total cost** incurred by technology and automation choices, taking into account the impact on ROI. The design must work within the acceptable boundaries for all functional and nonfunctional requirements. The design must also be flexible to accommodate predicted evolution.<br><br> Factor in the cost of acquisition, training, and change management. | Implementing a balanced approach that takes ROI into account **prevents overengineering**, which might increase costs.<br><br> Discarding alternatives that are expensive and lack business justification provides buffer in your budget that you can spend in other areas.   <br><br>We don't recommend that you design beyond planned growth because doing so might divert investments that are allocated for near-term design choices and tradeoff compensation.
| Establish the initial cost, using the billing models that are best suited to fulfill your requirements.  | Refining cost estimates will help you forecast how costs compare to the budget and identify the main cost drivers. Do the cost drivers help meet the business requirements?  <br><br> You need to know the initial cost before you can readjust your choices and evaluate other cost-effective options. You'll uncover hidden costs that might go undetected if the design was in a purely hypothetical state.   |
| Fine-tune the design by prioritizing services that can reduce the overall cost, don't need additional investment, or don't have a significant impact on functionality. Prioritization should account for the business model and technology choices that bring high ROI.   | You'll be able to explore cheaper options that might enable resource flexibility or dynamic scaling, or you might justify the use of existing investments. The prioritization parameters might factor in costs that are required for critical workloads, runtime, and operations, and other costs that might help the team work more efficiently. |
| Design your architecture to support cost guardrails.  |  Enforcement via governance policies or built-in application design patterns can prevent incidental or unapproved charges.|
| For workloads that are backed by service-level agreements (SLAs), weigh the pros and cons of reserving budget for penalties versus using it for implementation. You can avoid penalties if your implementation is sound.   | Ensuring that your design fulfills its intended function and meets commitments is a proactive approach that reduces eventual risks of liability.  <br><br> Negotiating realistic cost commitments or working with your product owner to create a dedicated violation budget makes these goals more achievable.   |

## Design for usage optimization

|![Goal icon](../_images/goal.svg) Maximize the use of resources and operations. Apply them to the negotiated functional and nonfunctional requirements of the solution.| 
|--| 

Services and offerings provide various capabilities and pricing tiers. After you purchase a set of features, avoid underutilizing them. Find ways to maximize your investment in the tier. Likewise, continuously evaluate billing models to find those that better align to your usage, based on current production workloads.

| Approach   | Benefit     |
|-|-|
| Evaluate whether your chosen **resource SKUs provide** additional features that can help you meet performance, security, reliability, or operational targets.         | By taking advantage of features offered by the SKU that you selected for your design, you can maximize the use of what you paid for and **avoid paying for unused features**.    |
| **Use consumption-based pricing** when it's practical.     | You'll pay for exactly what you use. This option might be more expensive than a fully utilized prepaid option. However, **if you don't expect to fully utilize pre-purchased compute**, consumption billing might be a better choice.     |
| **Apply policies** to comply with the design and the design's upper and lower limits. | Governance ensures that only allowed regions and services and their budgeted quantity are provisioned. This governance **reduces waste and the over-provisioning of resources**.     |
| **Prioritize deployment of active-active models** over active-only or active-passive models, as part of your recovery plan, if you already paid for the resources. | If your design defaults to using active-passive models, you might have **idle resources** that could otherwise be used. Converting to active-active might enable you to meet your load leveling and scale bursting requirements without overspending. If you can meet your recovery targets with an active-only model, the costs of those resources can be removed completely. |
| Regularly and rigorously **review deployments for unused resources** and data and decommission them. | Shutting down unused resources and deleting data when you no longer need it reduces waste and **frees up funds so you can invest them elsewhere**. |
| Find additional **uses for resources that you committed to** in discounted longer-term plans.| Consider **pre-purchased resources, existing licenses, and other commitment-based discounted resources that are unused**. You can save money by using these resources. You can use these resources for tests, additional environments, or even addressing functional and nonfunctional requirements.  <br><br> Likewise, finding opportunities to utilize committed plans for resources that your workload is using will enable your workload to optimize those resource costs via the precommitment.   |
| Take advantage of your **investment in your support plan**. | Using your support plan to **handle production problems or for proactive reviews** will help you get your money's worth. Fully engage with your Microsoft support model.

## Design for rate optimization

|![Goal icon](../_images/goal.svg) Increase efficiency without redesigning, renegotiating, or sacrificing functional or nonfunctional requirements.| 
|--| 

Take advantage of opportunities to optimize the utility and costs of your existing resources and operations. If you don't, you unnecessarily spend money without any added ROI.

| Approach    |Benefit    |
|-|-|
| Optimize by committing and pre-purchasing to **take advantage of discounts** offered on resource types that aren't expected to change over time and for which costs and utilization are predictable.  <br><br> Also, work with your licensing team to influence future purchase agreement programs and renewals. | Microsoft offers reduced rates for predictable and long-term commitment to specific resources and resource categories. Resources **cost less during the usage period** and can be amortized over the period. <br><br> By keeping your licensing team aware of the current and predicted investment by resource, you can help them **right-size commitments** when your organization signs the agreement. In some cases, these projections and commitments could influence your organization's price sheet, which benefits your workload's cost and also other teams that use the same technology. |
| Find ways to reduce licensing costs by evaluating **alternatives that don't require additional licensing**. Consider options like hybrid use and pre-production subscription pricing. | You'll be able to **reduce licensing costs** for services, operating systems, and tools by taking advantage of options that give you usage rights to the same or comparable technologies at a lower cost.    |
| Switch to **fixed-price billing** instead of consumption-based billing for a resource when its utilization is high and predictable and a comparable **SKU** or **billing option is available**.      | When **utilization is high and predictable**, the fixed-price model usually costs less and often supports more features. Using it could increase your ROI.    |
| **Use centralized resources** that are provided by your organization, and share the cost with other teams.   | Shared resources often have higher capacity to support multiple workloads, and **costs are distributed across teams**. Taking a dependency on shared resources can save money, as long as the functionality of your workload isn't compromised.<br><br> Showback and chargeback are other potential benefits. |
| **Deploy to regions** that cost less.| If you can still meet functional and nonfunctional requirements, you can save money by **selecting a region that has favorable pricing** for your resources.         |
| **Co-locate usage** with other resources, workloads, and even teams. <br><br>  **Prefer services that make it easier to achieve higher density.**<br><br>   Consider the potential tradeoffs, especially on security boundaries. | You'll be able to save costs by optimizing hardware utilization.<br><br>   **As density increases, the amount of resources that you need to run a workload decreases**. This decrease reduces cost per unit and the cost of management. |

##  Monitor and optimize over time

|![Goal icon](../_images/goal.svg) Continuously right-size investment as your workload evolves with the ecosystem.| 
|--| 

What was important yesterday might not be important today. As you learn through evaluation of production workloads, expect changes in architecture, business requirements, processes, and even team structure. Your software development lifecycle (SDLC) practices might need to evolve. External factors might also change, like the cloud platform, its resources, and your agreements.

You should carefully assess the impact of all changes on cost. Monitor changes and the ROI trend on a regular cadence, and evaluate whether you need to adjust functional and nonfunctional requirements.
 
| Approach      | Benefit   |
|-|-| 
| By using your cost tracking system, continuously evaluate and optimize the costs of resources, data, and paid support. Are there **underutilized resources that can be retired, replaced, rebuilt, or refactored**? | You'll reduce costs by **avoiding paying for resources that aren't fully utilized**. Understanding pricing metrics can help you make decisions that are more aligned with your cost model. It can also prevent unwarranted billing. Resizing or removing underutilized resources, or even changing SKUs, can reduce costs. <br><br>You might also be able to save some costs by evaluating the use of your support contract and right-sizing it. | 
| **Continuously adjust architecture** design decisions, resources, code, and workflows based on ROI data.     | Regular reviews of metrics, performance data, billing reports, and feature usage might lead to **fine-tuning that can reduce costs**.|
| **Treat different SDLC environments differently**, and deploy the right number of environments.<br><br> Production environments should be your main cost driver.| You can save money by understanding that **not all environments need to simulate production**. Nonproduction environments can have different features, SKUs, instance counts, and even logging. <br><br> You also can save costs by creating pre-production environments on-demand and removing them when you no longer need them.

## Next steps

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)
