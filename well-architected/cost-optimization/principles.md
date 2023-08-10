---
title: Cost optimization design principles
description: Learn about the cost optimization principles for your workload that can help you achieve business objectives and justify costs.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/15/2023
ms.topic: conceptual
categories:
  - management-and-governance
---

# Cost Optimization design principles

Architecture design is always driven by business goals and must **factor in return on investment (ROI) while keeping within financial constraints**. Typical areas of concern include:

-   Are the allocated budgets enough to meet the goals?
-   What is the spending pattern for the application and its operations? What are the priority areas?
-   How to maximize the investment in resources, by better utilization or reduction?

A cost-optimized workload isn't necessarily a low-cost workload. There are significant tradeoffs. Tactical approaches are reactive and can only reduce costs for short-term savings. To achieve long-term financial responsibility, you need to **build a strategy with prioritization, continuous monitoring, and repeatable processes** that focus on optimization.

The *principle approaches aren't checklist items*. They intend to guide you on optimization strategies that must be considered while designing and implementing workload architecture. Start with the recommended approaches and **justify the benefits** **for a set of business requirements**. After you've set your strategy, drive actions using the **Cost Optimization checklist** as your next step.

As you prioritize business requirements to align with technology needs, costs can be adjusted. However, expect a series of **tradeoffs in areas you want to optimize cost, such as security, scalability, resilience, and operability**. If the cost to address the challenges in those areas is high and these principles aren't applied properly, you might make risky choices in favor of a cheaper solution, ultimately impacting the organization's business goals and reputation.

## Develop cost-management discipline

|![Goal icon](../_images/goal.svg) *Develop a team culture that has the expertise to understand the financial aspects of budget, expenditure, reporting, and tracking costs.*| 
|--| 

Cost optimization is carried out at various levels of the organization. It's important to understand how your workload is aligned with organizational goals and FinOps practices. A view into the business units, resource organization, and centralized audit policies will allow adoption of a standardized financial system. 

|Approach|Benefit|   
|-|-|
| Develop a **cost model**. This is a fundamental exercise that is a prerequisite to setting up a financial tracking system.                                                                   | Cost model will help segment expenses and estimate and **forecast the Total Cost of Ownership** including infrastructure, support, implementation, and others. You'll be able to identify cost drivers early on and predict how any change, growth or shrinkage will impact overall spend with your projected business model. |
| Have an **effective and yet flexible accountability model** that's implemented through proper assignment of roles and responsibilities.             | As the architecture evolves, various roles will participate in decision making to final procurement. Clear accountability will help **enforce functional expectations** of each role, given a scope, drive clarity, and help generate reports with transparency at desired levels.      |
| Estimate **realistic budgets** that cover all non-negotiable functional and non-functional requirements, personnel and training costs, and processes that allow room for anticipated growth. | You'll be able to **set financial boundaries** and ways of checking your spend against the allocated budget. Another benefit is getting notifications when certain thresholds are crossed, preventing overspend at the tenant, resource, and other scopes applied to the budget. |
| Use **governance** and processes to implement the accountability model and budgets. | It's not enough to get notifications as that's reactionary. It's crucial to **have proactive governance** on actions that might lead to unnecessary expenditure that are beyond the budget.<br><br>Certain actions can bring improvement in the current state. Are the retention policies overly relaxed? Are scalability limits needed to drive responsible engineering.  |
| Build capabilities in the system that **capture and classify expense**.    | You'll be able to calculate the costs that **report on technical and business perspectives**, at different billing boundaries.<br><br>You'll also be able to conduct regular reviews and drive showback and chargeback processes.
| Plan on **training costs, hiring expenses, and cost of infrastructure needed** to grow skills as the workload matures.                                                                       | Investing in staffing will **complement existing skills** through full-time or vendor support. |
| Encourage **upstream communication** from architects and application owners.      | The cost of research is saved by acting on feedback, which should be considered as powerful as numeric data. You'll empower employees by taking their input to **drive realistic design changes** and business strategies.

## Design with a cost-efficiency mindset

|![Goal icon](../_images/goal.svg) *Only spend on what you need to achieve the highest ROI.*| 
|--| 

Every architectural decision has direct and indirect financial implications. Understand the cost associated with; build versus buy options, choice of technology, billing model and licensing, training, operations, and so on.

Given a set of requirements, optimize and make tradeoff decisions in relation to cost, which are still effective in addressing the cross-cutting concerns of the workload.

|Approach    | Benefit |
|-|-|
| **Measure the total cost** incurred by technology and automation choices, keeping in mind its impact on ROI. The design must work within the acceptable boundaries for all functional and non-functional requirements. The design must also be flexible to accommodate predicted evolution.<br><br> Factor in the cost of acquiring, training, and change management. | Applying the "just right" strategy will **prevent you from overengineering**, which might increase the cost of complexity.<br><br> Discarding alternatives that are expensive and lack business justification will provide budget buffer to spend in other areas.   <br><br>Designing beyond planned growth isn't recommended because it might sidetrack investments set for near-term design choices and tradeoff compensation.
| Establish the initial cost considering the billing models that are best suited to fulfill requirements.  | Refining cost estimates will help forecast against budget and identify the main cost drivers. Do the cost drivers help meet the business requirements?  <br><br> Only after you know the initial cost, can you readjust the choices and evaluate other cost-effective options. You'll uncover hidden costs that might go undetected if the design is purely a hypothetical state.   |
| Fine-tune the design by prioritizing services that can lower the overall cost, don't need additional investment, or don't have significant impact on functionality. Prioritization should account for the business model and technology choices that bring high ROI.                    | You'll be able to explore cheaper options that might allow for resource flexibility, dynamic scaling, or justify the use of existing investments. The prioritization parameters might factor in costs required for critical workloads, runtime, operations, and other costs that might help the team get the job done more efficiently. |
| Design your architecture to support cost guardrails.  |  Enforcement through governance policies or built-in application design patterns will prevent incidental or unapproved charges.|
| For workloads that are backed by Service Level Agreements (SLAs), weigh the pros and cons of reserving budget for penalties versus utilizing it for the implementation. Penalties can be avoided if the implementation is sound.   | Ensuring your design fulfils its intended utility and is kept up to date with commitments is a proactive approach that will reduce eventual liability risk.  <br><br> Negotiating realistic cost commitments or having a dedicated violation budget built with your product owner will make these benefits more achievable.   |

##  Use what you pay for

|![Goal icon](../_images/goal.svg) *Maximize the use of resources and operations that you've already invested in, to meet the negotiated functional and non-functional requirements of the solution.*| 
|--| 

Services have various levels of capabilities offered at pricing tiers. Situations where the purchased set of features remain underutilized should be avoided. Find ways to maximize your investment in that tier. Likewise, continuously evaluate billing models that better align to your usage after learning in production.

| Approach   | Benefit     |
|-|-|
| Evaluate if a selected **resource SKU offers** additional features that can help meet your performance, security, reliability, or operational targets.         | By taking advantage of features offered in the SKU selected in your design, you'll maximize use of what you've paid for and **avoid paying for unused features**.    |
| **Use consumption-based pricing** when practical.     | You'll pay for exactly what you use. This model might be more expensive than fully utilized pre-paid option. However, **if maximum utilization of pre-purchased compute isn't expected**, then consumption billing might be a better option.     |
| **Apply policies** to adhere to the design and design's upper and lower limits. | Governance will ensure only allowed regions, services and their budgeted quantity are provisioned. This will **reduce wastage and over-provisioning of resources**.     |
| **Prioritize deployment of active-active model** over active-only or active-passive, as part of your recovery plan if you've already paid for those resources. | If your design defaults to using active-passive, you might have **idle resources** that otherwise could have been used. Converting to active-active might achieve your load leveling and scale bursting requirements, without overspending. Or, if your recovery targets can be met with an active-only model, costs of those resources can be removed completely. |
| Regularly and rigorously **review deployments for unused resources** and data and decommission them.                                                           | Shutting down unused resources and deleting data when no longer needed will reduce wastage and **frees up funds to be invested elsewhere**. |
| Find additional **usage for resources that are committed** through discounted longer-term plans.     | **Pre-purchased resources, existing licenses, and other** **commitment-based discounted resources that are unused should be considered by your workload**. Already available resources could be used for tests, additional environments, or even addressing functional and non-functional requirements.  <br><br> Likewise, finding opportunities to trade plans to resources that are being used by your workload will return your workload to optimize those resource costs through the pre-commitment.   |
| Utilize **investments in your support plan**.         | Get your money's worth out of your support plan. Use it to **handle production issues or proactive reviews**. Fully engage with your Microsoft support model.

## Pay less for what you use

|![Goal icon](../_images/goal.svg) *Lower costs to a practical level, without redesigning, renegotiating, or sacrificing functional and non-functional requirements.*| 
|--| 

There are ways to optimize the utility and costs of your existing resources and operations. Without this effort, you're unnecessarily paying more without any additional ROI.

| Approach    |Benefit    |
|-|-|
| Optimize by committing and pre-purchasing to **take advantage of discounts** offered on resource types that aren't expected change over time and the cost and utilization is predictable.  <br><br> Also, work with your licensing team to influence future purchase agreement programs and renewals. | Microsoft offers reduced rates for predictable and long-term commitment to specific resources and resource categories through discount programs. Resources will **cost less during the usage period** and can be amortized over the period. <br><br> Keeping the licensing team aware of the current and predicted investment by resource, will help them **make "right sized" commitments** when the organization is signing their agreement. In some cases, these projections and commitments could influence the organization's price sheet, which not only benefits your workload's cost but also other teams using the same technology. |
| Find ways to reduce licensing costs by evaluating **alternatives that don't require additional licensing**. Explore options such as hybrid use, pre-production subscription pricing, and others. | You'll be able to **reduce licensing costs** for services, operating systems, and tools that give you usage rights to the same or comparable technology at a lower cost.    |
| Switch to **fixed-price billing model** instead of consumption-based billing for a resource when its utilization is high, predictable, and a comparable **SKU** or **billing option is available**.      | When **utilization is high and predictable** this fixed-price model usually has lower cost and often supports more features which could positively impact the ROI.     |
| **Use centralized resources**, provided by the organization, and share the cost with other teams.   | Shared resources often have higher capacity to support multiple workloads and **costs are distributed across those teams**. Taking dependency on shared resources can be considered a cheaper option as long as the functionality of your workload isn't compromised.<br><br> Showback and chargeback are other potential benefits. |
| Choose your **deployment to alternate regions** that offer lower price.            | When functional and non-functional requirements can still be met, you can save costs by **selecting a region that has favorable pricing** for your resources.         |
| **Co-locate usage** with other resources, workloads, and even teams. <br><br>  **Prefer services that make it easier to achieve higher density.**<br><br>   Consider the potential tradeoffs, especially on security boundaries. | The **higher the density, the lesser resources are needed** to run the workload, which decreases cost per unit and also lowers cost of management. <br><br> You'll be able to save costs based on optimal hardware utilization.  

##  Monitor and optimize over time

|![Goal icon](../_images/goal.svg) *Continuously "right-size" investment as the workload evolves with the ecosystem.*| 
|--| 

What was important yesterday might not be important today. As you learn from production, expect changes in architecture, business requirements, processes, and even team structure. Your software development lifecycle (SDLC) practices may need to evolve. External factors might also change, such as the cloud platform, its resources, and agreements.

The impact of all changes on cost should be carefully assessed. Monitor changes and the ROI trend at a regular cadence and evaluate whether adjustments in functional and non-functional requirements are needed.
 
| Approach      | Benefit   |
|-|-| 
| By using your cost tracking system, continuously evaluate and optimize costs on resources, data, and paid support. Are there **underutilized resources that can be retired, replaced, rebuilt, or refactored**? | You'll reduce costs by **not paying for resources that aren't fully utilized**. By understanding the pricing meters, you might make decisions that's more aligned with your cost model. It can also prevent unwarranted billing situations. Resizing, removing underutilized resources, and even changing SKUs will reduce cost. <br><br>There might be some savings in evaluating and right-sizing the usage of your support contract. | 
| **Continuously adjust architecture** design decisions, resources, code, and workflows based on the ROI data.     | By investing time into regularly reviewing metrics, performance data, billing reports, and feature usage might lead to **fine-tuning that can reduce cost**.|
| **Treat different SDLC environments differently** and have just enough of them.      | Production environments should be your main cost driver. **Not all environments need to simulate production**, including features, SKU, instance count, and even logging. <br><br> Save costs by creating pre-production environment on-demand and remove them when no longer necessary. 