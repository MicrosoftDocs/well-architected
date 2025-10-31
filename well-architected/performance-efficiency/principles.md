---
title: Performance Efficiency design principles
description: Learn about Performance Efficiency design principles that can help you achieve a state of performance that's aligned with the business objectives.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 07/27/2025
ms.topic: concept-article
ms.update-cycle: 1095-days  
---

# Performance Efficiency design principles

Performance efficiency is about effective use of workload resources. Without a good strategy, you might not be able to anticipate and meet user demands. You might have to resort to an approach of pre-provisioning capacity based on your long-term forecasting, which doesn't let you take full advantage of your cloud platform.

Performance efficiency is the ability of a workload to adapt to changing demands by scaling up to meet increased load without impacting user experience, and scaling down to conserve resources during low demand. Capacity plays a central role, but relying solely on pre-provisioned resources can lead to performance issues under high load and unnecessary costs during low usage.

Rather than treating performance as an afterthought, make it a core consideration from the start. Begin early, even if you don't have strict performance targets, then test and refine throughout development. This ongoing optimization, informed by real-world usage, helps prevent future issues and ensures consistent performance.

A well-planned strategy helps align resource capacity with business needs while minimizing waste. Once your approach is defined, use the [Performance Efficiency checklist](checklist.md) to validate your design. Without a proactive strategy, you risk relying on static forecasting and missing the full benefits of scalable cloud infrastructure.

## Negotiate realistic performance targets

|![Goal icon](../_images/goal.svg) The intended user experience is defined, and there's a strategy to develop a benchmark and measure targets against the pre-established business requirements.|
|--|

From a performance perspective, it's ideal to have well-defined performance targets to start your design process. To set those targets, you need to have a good understanding of the business requirements and the anticipated quality of service that the workload is expected to deliver. Define the expectations in collaboration with the business stakeholders. Instead of only focusing on technical metrics, determine the acceptable effects on the user experience for the key flows.  

There's a circular dependency. You can't measure what you haven't defined, and you can't define without measurement. So, it's also important to **measure the workload performance until you achieve a satisfactory definition of acceptable threshold** with collective agreement.

There's a strong correlation between performance and reliability targets, which help determine the quality of service in terms of performance, availability, and resilience. Without a clear definition, it's challenging to measure, alert for, and test performance. After you establish the targets and identify actual numbers through testing over time, you can implement automation for continuous testing against these targets.

Adhere to best practices in defining targets at the macro level, even if they're approximate or within a range.

|Approach|Benefits|
|-|-|
| Prepare for effective negotiation by understanding technical options, exploring design possibilities, and applying experimental results. <br><br>Use historical data to identify usage patterns and bottlenecks. Incorporate insights from market analysis, industry standards, and expert input to guide decisions.| You can make informed decisions based on practical insights.<br><br>The performance targets will be based on user experience that's based on what's feasible, industry best practices, and current market trends. |
| Align with business owners on user expectations and performance standards, considering investment levels. <br><br>Avoid diving into granular details at the initial stage of design, while keeping the broader business context and growth plans in mind. | You'll avoid misaligned assumptions, promote clarity and motivation within the team, and make informed design decisions on trade-offs. <br><br>It also ensures performance targets account for future needs, aligning current work with long-term business goals. |
| Prioritize critical flows in the architecture based on performance impact. <br><br>Define performance tolerance ranges for each flow, from ideal to unacceptable. Assess entry and exit points by considering usage frequency, importance, and complexity. | By prioritizing flows, you can focus your resources on critical areas that have the most effect on user and business outcomes.<br><br>By breaking down the system into its parts and dependencies, you understand each component's function and influence on performance. You also become aware of potential issues.<br><br>It helps establish a performance baseline and drive optimization. |
| Begin developing a performance model that considers usage patterns, business impact, and operational costs to calculate the initial performance targets. Use industry standards to define and measure key metrics, and assess demand and supply within business constraints while accounting for future growth. <br><br>Treat this as an iterative process and refine targets informed by real-world observations and metrics collected from the running solution, during testing and production. Prioritize test cases that yield meaningful utilization insights across the full application lifecycle.| A performance model helps in strategic resource planning and optimization, supports benchmarking through industry standards, and ensures performance goals remain adaptable and relevant over time. <br><br>Based on these evolving targets, you'll be able to perform accurate capacity planning and establish performance baselines that remain relevant throughout the solution lifecycle.|

## Design to meet capacity requirements

|![Goal icon](../_images/goal.svg) Provide enough supply to address anticipated demand.|
|--| 

It's important to proactively measure performance. Measuring performance involves **measuring baselines** and having a preliminary understanding of which components of the system are likely to pose challenges. You can achieve it without conducting a full performance test or through granular optimization. By taking these initial steps, you establish a foundation for effective performance management early in the development lifecycle.

Examine the system as a whole, rather than focusing on individual components. Avoid fine-tuning at this stage. Making granular performance improvements results in tradeoffs in other areas. As you progress through the lifecycle and begin user acceptance testing or move toward production, you can quickly identify which areas require further optimization.

|Approach|Benefit|
|-|-|
| Evaluate dynamic scaling needs for the prioritized flows based on usage requirements. Understand expected demand patterns and determining how elastic each flow must be to meet those requirements.| You're able to define scalability requirements on existing components that need more capacity and the areas where you need extra components to distribute load.|
| Choose the right resources and rightsize them across the technology stack, which enables you to meet performance goals. <br><br>Consider features that can fulfill the scalability requirements.| The overall system will and perform as per the defined targets. You can use built-in features that automatically scale when needed. It also helps you avoid overprovisioning, saving costs while still handling demand changes. |
| Do capacity planning based on the performance model and the capability of selected resources. <br> <br>Use predictive modeling techniques to forecast anticipated changes in capacity.| The system will be able to meet performance goals while staying prepared for future demand. Predictive modeling helps you plan ahead, avoiding resource shortages or overprovisioning, which improves reliability and cost efficiency. |
| Implement proof of concept and validate proposed design choices to meet technical requirements. |A proof of concept helps validate if the design can meet performance targets and anticipated load. |


## Achieve and sustain performance

|![Goal icon](../_images/goal.svg) Protect against performance degradation while the system is in use and as it evolves.|
|--| 

Development isn't a one-time effort. It's an ongoing process. Expect changes in performance as features change. There's variance in user patterns and profiles, even changes from optimizations in other Azure Well-Architected pillars. Any change can strain workload resources.

**Safeguard the system from sliding back on performance targets.** Test the system's performance in production with real load and simulate that load with automated testing prior to production. In both cases, you should have monitoring practices in place for verification purposes.

Keep in mind that performance targets vary over time, in response to changes. Update the performance model based on tested and monitored metrics. Clearly indicate increased, reduced, or no effect on the performance of the flows.

Always be ready to renegotiate and reset expectations with business stakeholders.

|Approach|Benefit|
|-|-|
| Define your performance testing strategy. <br><br> Conduct various types of tests including manual, low-effort tests to revisit benchmarks. <br><br>Add regular performance tests to your pipelines using tools that work well with them.| You can ensure that resources are allocated effectively and validate metrics according to capacity planning. <br><br> Automated routine performance tests help consistently evaluate key factors like latency, stress, and load capacity, making it easier to detect issues early and keep performance steady over time. |
| Formalize performance tests as quality gates. | These checkpoints make sure each stage of deployment meets performance standards before moving forward. They help catch issues early and allows you to make quality decisions. For example, blocking a release if performance falls below expectations. |
| Set up a performance monitoring process that tracks both end-to-end business transactions and technical metrics like CPU, latency, and requests per second. <br><br>Make sure you use real and synthetic transactions in production.<br><br>Set up monitoring alerts on performance regressions. | By monitoring all parts of the system, you get clear visibility and can quickly detect problems in both the infrastructure and application. It helps you fine-tune resources to maintain performance standards and also track progress or spot issues early.  |
| Review performance test results and monitoring data meticulously as usage increases, and data accumulates with the system in production.<br><br>Prioritize actions that address performance degradation and add them to the backlog for planned execution. | Using data to track and compare performance trends helps you make informed optimization decisions and catch issues early, before they impact user experience. <br><br>It also ensures you're not over-optimizing a system that's already at capacity. |
|Explore design patterns that can fine tune performance across the technology stack, considering the application and the underlying compute and data layers. |You can address bottlenecks and implement compensating controls to decrease latency and system load. |
| Build coding skills with a focus on performance and follow standards that promote efficient coding patterns. | Well-written, high-performing code makes testing faster by reducing issues and helps avoid rework while keeping code consistent. |


## Optimize for long-term improvement

|![Goal icon](../_images/goal.svg) Improve system efficiency within the defined performance targets to increase workload value.|
|--|

Initial performance targets aim to deliver a reasonable user experience within known constraints. As the system evolves, **reassess those targets using real production data** to better understand usage patterns, platform changes, and potential gains, ensuring that optimization efforts are well-timed and effective. It's often best to delay major optimizations until this data is available to avoid premature decisions.

Performance tuning is a continuous cycle of monitoring, optimizing, testing, and deploying. Efficiency improvements can reduce resource use, sometimes leading to overprovisioning. This extra capacity can then be used to improve reliability, reduce costs, or support new features without adding infrastructure.

|Approach|Benefit|
|-|-|
| Set aside dedicated time for performance optimization as a regular practice throughout the development lifecycle.  | In a performance-driven culture, this approach reinforces accountability, where the team actively monitors and continuously improves system performance. |
| Revisit nonfunctional requirements and establish new targets by analyzing historical trends in production to enhance the architecture with improved design patterns and components. | New designs and components, like caching or a CDN, can optimize the system and improve the user experience.|
| Get current and stay current with technology innovations that can improve performance. <br><br>Take advantage of the new versions released for the dependent frameworks and libraries.<br><br>Similarly, use the new features for platform resources as they're updated and patched. | Performance targets provide justification for adopting new technology. <br><br>Code that might have been slow in the past can become faster with these updates. You also want to be aware of how certain updates negatively affect performance. |

## Next steps

> [!div class="nextstepaction"]
> [Performance Efficiency checklist](checklist.md)
