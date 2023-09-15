---
title: Cloud design principles that support operational excellence
description: Learn about industry principles that support operational excellence and can help you address common challenges in cloud workloads.   
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/15/2023
ms.topic: conceptual
---

# Operational Excellence design principles

At the core of this pillar are DevOps practices that assure workload quality through standardized workflows and team cohesion. This pillar defines operating procedures for development practices, observability, and release management. The goal is to minimize process variance, chances of human errors, and disruption to the users. To assess your operational health, start with simple questions:

-   Do you execute operations with discipline?

-   Are the users utilizing the workload with maximum predictability?

-   How do you learn from experience and collected data to drive continuous improvement?

Workload operations can often devolve into chaotic practices when there's no clear ownership or leadership. In this type of environment, teams often resort to methods that are executed with high effort and produce low outcomes, often leading to poor user experience. Those approaches only meet short-term goals. Long term benefits that can only be realized through continuous evaluation and strategic investments.

The design principle approaches aren't checklist items. They provide guidelines for operational strategies that must be considered to address the underlying causes and not just treat symptoms. Start with the recommended approaches, observe what works and what doesn't to identify areas of improvement. After you've set your strategy, continue to drive actions by using the Operational Excellence checklist.

The operational requirements of a workload are just as important as its business requirements. Efficient processes make sure the workload achieves business outcomes within the constraints of compliance, whether that's organizational or external. The key is to find repeatability with consistency.

The goal of the Operational Excellence pillar is to do the right thing, the right way, and solving the right problems, as a team.

That goal will make sure that workloads run reliably and predictably even during times of change. Inability to fulfill operational requirements can lead to failed deployments, inconsistent user experience, and added costs that could have been avoided through proper planning and streamlined execution.

## Embrace DevOps culture

|![Goal icon](../_images/goal.svg) Empower development and operations teams to continuously improve their system design and processes by working together with the mindset of collaboration, shared responsibility, and ownership.| 
|--| 

DevOps is a community of practice where diversity of perspective and skills drive toward one mission. Teams must foster a collaborative environment of shared knowledge instead of siloed learning. Shared functions must strive to overcome resource constraints.

A good DevOps culture thrives on shared responsibility. Keeping business focus in mind, both development and operations teams should align their goals and priorities with the expectations of their customers. The development team should involve the operations team in the feedback loop, so the improvements are driven upstream, and other teams benefit equally. Conversely, operations teams are responsible for making the development team successful in their business outcomes by sharing resources and feedback that are relevant to the workload.

At the same time, DevOps practices apply clear lines of ownership and accountability to each team. Regardless of where the application runs, the workload team is responsible for that application.

DevOps enables optimization of operational tasks so that they are effective but not burdensome. To reap the full benefit of DevOps, the culture should not only optimize processes through technology, but also have processes for people in the organization to promote transparent communication.

|Approach  |Benefit  |
|---------|---------|
|**Use common systems and tools** that promote a collaborative environment for communication and tracking progress.      |  Common tools and processes enable **transparent communication**. Both development and operations teams benefit from situational awareness across various environments, common support issues, and overall challenges and wins. <br><br> Teams will already know and be familiar with existing escalation paths in the event of an incident. <br><br>  Having a shared backlog will make priorities clear, whether that's working on new features or fixing bugs.   |
|Build a **continuous learning and experimentation mindset** throughout the development cycle.   <br><br> **Support knowledge sharing** across teams and maintain documentation for reuse. <br><br> Conduct blameless analysis and debrief post-release and/or post-incident reviews.  |  Through experimentation mechanisms, such as A/B testing and developing proof of concepts, you'll be able to encourage innovation while keeping your costs low.  <br><br> Shared knowledge transfers through collaboration that can make the team proficient in design approaches, tooling, and processes through real-life experiences. <br><br> Doing retrospectives help **identify areas for improvement** and celebrate success.      |
| **Adopt proven industry agile practices** focusing on action optimization.  <br><br> Look for **opportunities to "shift-left"** in operations for people and automated processes, deployment and quality assurance practices, and observability.    | Agile development practices will lead to shorter release lifecycle, which is an indicator of business value.  <br><br>  Detecting, resolving, and thereby preventing issues earlier in processes are often less intrusive to the process.  |
| **Set standards for all development and operational procedures and review and validate them at a regular cadence.** <br><br> This includes routine tasks, out-of-band processes, emergency drills and situations, choice of tooling, monitoring procedures, skilling plans, and even communication with stakeholders and customer disclosures. <br><br> **Be intentional and explicit about your decisions.**        | Standards add predictability to operations and makes processes and practices scalable. Validating standards is a great way of drawing points of improvement. <br><br> You'll be prepared for emergency and recovery situations by conducting regular drills. <br><br> You'll be able execute with precision and enable governance to **prevent anomalies** that can lead to risks.   |
|  **Leverage centralized operations team** with **specialized skills** and breadth of experience.   | There's a cost benefit to using shared resources both for operations and resources. <br><br>   While you own your workload, the centralized team can help you with cross-functional skills, such as incident management, proactive perspective on monitoring, and also allow you to outsource expertise with trust. |


## Establish development standards

|![Goal icon](../_images/goal.svg) Optimize productivity by standardizing development practices, enforcing quality gates, and tracking progress and success through systematic change management.| 
|--| 

The development team is responsible for addressing workload issues prior to release with minimum friction. Be mindful of developer efficiency and optimize for fast turnaround cycles, from coding to testing results. Implement effective and right-sized processes that not only plan and standardize technical activities, but also drive consensus within the team and the stakeholders.


|Approach  |Benefit  |
|---------|---------|
|**Document workload features** and capture the user benefits.  <br><br>  Derive **scope, and detailed functional and non-functional requirements** of the architecture. <br><br> **Create sizing estimation models** to report on scope and cost of the tasks involved.   |Good specifications **cut operational costs and chances of failure** by supporting more productive and streamlined development cycles. <br><br>  Developers will get a good understanding of the **technical design, goals, and completion criteria**, before starting the coding cycle. <br><br>  Good documentation will also help facilitate repeatable **communication** and **onboarding** of new team members.      |
|Use an **industry standard software development methodology that's appropriately tuned** for the needs of your workload and team size. <br><br> **Maintain a backlog that's shared among all roles.**      | Adoption of a well-known methodology will **set the rhythm of the project**. It will remove process ambiguities, giving clear expectations and accountabilities among the team members. <br><br> By tracking against a common list, **tasks can be refined and prioritized** using standard practices. The project will have better chances of being delivered on time. <br><br> Standard methodologies can help with **risk management**. With granular milestone reviews, developers can address potential issues before they become showstoppers.      |
|**Use unified source control** for all code, scripts, deployment templates, pipeline definitions, and related documentation.<br><br> The branching strategy must support friction-free release of independent and interdependent features, bug fixes, and hotfixes. <br><br>  Use shared knowledge across the organization to build your branching strategy and deployment processes.     | Proper use of source control is crucial in supporting **concurrent changes** and versioning.  <br><br> You'll be able to maintain a repeatable workflow for releasing changes of various sizes and risks, conduct peer reviews as part of the process, and keep an audit trail.     |
|Have **quality assurance** processes that emphasize testing early in the development life cycle. <br><br>  **Include all artifacts for planned test procedures** including application components, infrastructure, and data plane operations that are part of a feature release or update. <br><br>  Treat artifacts as immutable when they are promoted through environments, gaining confidence each time they pass through a quality gate. <br><br>  Automate routine checks, where practical.   | Quality assurance makes sure that functional and non-functional requirements were met with confidence, leading to positive user impact. <br><br> Having test plans will ensure quality and completeness taking into consideration the possible failure cases.<br><br>  Through quality gates you can enforce best practices to reduce risks. <br><br> Immutability brings confidence because it will ensure the system that you test is exactly what you release.<br><br> Testing cycles will be efficient by blocking progress unless quality criteria are met.        |
|Drive consistency by using **style guides and tools**, which enforce **conventions**, and adopt a **common tool chain** for development, testing, and communication with stakeholders.  <br><br>**Technology standards for developers should** necessitate implementation **of patterns**, API design, **logging, exception handling, and more**.       | Consistency in code will drive readability and easier maintenance. It also reduces complexity and enables code reuse. <br><br> Common tooling and conventions also helps teams optimize processes without needing to address one-off choices.   |         |
|Consistently and deliberately insist on developer documentation of code as written.     |  Clear code documentation ensures logic and functionality is quickly and easily understood when old code needs to be revisited or development teams rotate.       |
|Report progress and trends to measure efficiency.      | Trends in bugs, failed updates, time to deploy, feedback loops, and other metrics will be published and that will drive improvements.         |

##  Evolve operations with observability

|![Goal icon](../_images/goal.svg) Gain visibility into the system, derive insight, and make data-driven decisions.| 
|--| 

Build a culture that continuously improves quality by monitoring the workload, taking into consideration all the pillars of the Well-Architected Framework. Enable the team and stakeholders to make both short-term and long-term decisions across many facets, providing the data, statistics, and trends to enable decision making. Learn from your monitoring data and drive improvements.

Monitoring operations is key in proactive maintenance of the application, quality and security assurance, capacity planning, and product management.

A crucial aspect of monitoring is application health modeling without which you won't be able to anticipate issues before they become incidents and impact user experience. Efficient monitoring will reduce reactive cycles spent on incident management.

Learn from your monitoring data and drive improvements.


|Approach   | Benefit   |
|---------|---------|
|**Build a monitoring system with its own stack and flows.** <br><br> Treat the monitoring system as a dimension of the workload that's decoupled from its utility. The stack must cover all layers including infrastructure, application health, and build and release processes. <br><br> **Capturing or sampling business data** is out of scope for observability implementations. | Decoupling monitoring and workload stacks allows for **separation of functional requirements and observability requirements** making independent evolution possible. Changes in code shouldn't impact monitoring and vice versa. <br><br> Because observability requirements are separate from functional requirements, **business data** **will not be disrupted** by monitoring configuration changes or outages.  |
|**Drive consistency** in the collection process for each **type of data source**. <br><br> Standardize instrumentation in code by using industry standards for telemetry, collection of infrastructure metrics, and tooling. | You want consistency to avoid variance in sensing and measurement because familiarity across similar resources **will reduce time in correlating and analyzing data**. You'll have a holistic perspective needed to anticipate issues. <br><br>  |
|**Emit telemetry** from application code that corelates the key points of the execution flow and gives an end-to-end view at different levels of granularity.        | You'll be able to prioritize actions based on the severity level and understand the context given its verbosity. This information is crucial for troubleshooting purposes.  |
|**Own the responsibility of emitting and collecting data,** even when data sinks are shared by multiple teams and managed by central teams.      | By localizing monitoring data to the workload environment, the team will be able to access logs and metrics to address workload concerns. |
|Collect **just enough data** and retain it for **just enough time**. <br><br>Consider the cost tradeoffs associated with storing logging data. | Being intentional about collection will help you **optimize financial and operational costs** associated with collecting more data than needed.<br><br> You'll be able to **minimize the noise and avoid intensive computation** during analysis. <br><br> Also, you'll be able to reduce the cost of storing data that is no longer needed.|
|**Make a distinction between the use of different monitoring signals**: profiles, logs, metrics, and traces. Use each for the right purpose. <br><br> Prioritize the **use of metrics to trigger actions** that rely on numeric measurements. <br><br> Use **profiles to get lower-level visibility** into the system such as memory allocation. <br><br>Reserve the **use of logs and traces to provide context** for flows and dependencies.       | You'll be able to use the signals for the right purposes and prevent inefficient implementation of the monitoring system.<br><br> For example, using logs for actions will require parsing. You might be able to achieve the same goals faster with metrics.   |
|**Aggregate and visualize data** in dashboards to present monitoring data catered to audiences, keeping in mind the business context. <br><br> Use **situational dashboards** for surfacing data to drive awareness among the stakeholders.<br><br> **Use operational dashboards and workbooks** with drilldown capabilities for operator activities, such as incident response. Frequently refresh and provide granular data. | With visualizations, you'll be able to do trend analysis, track against business targets, and incident management.<br><br> Dashboards that are tailored to the interest of the user, make interpretation relevant and accelerate time to detection and action.|
|**Make alerts actionable** by notifying the accountable roles with standardized descriptions and severity levels. Provide information that's collated from various sources and track deviations from business targets. <br><br> **Trigger alerts only for incidents that require action.** <br><br> **Strive for proactive** and thought-provoking alerts that initiate actions before a degraded state becomes a failure.| Alerts bring attention to significant events as defined by the organization. <br><br>Having a good alerting system that identifies actions, severity, and provides just enough data will drive clarity and purpose. **Operators can start on remediation without delay**. |

## Deploy with confidence

|![Goal icon](../_images/goal.svg) Reach the desired state of deployment with predictability.| 
|--| 

Build a workload supply chain that enables you to consistently reach the goal of predictability in all of your environments, across the workload's hosting platforms, applications, data, and configuration resources. The deployment mechanism must be capable of automation, testing, monitoring, versioning, and ready to be executed on demand. It should be modularized and not be represented as a monolithic end-to-end process. It isn't necessarily for faster execution, but to achieve consistency and self-documentation over multiple iterations.

The workload team is accountable for the supply chain as it relates to their own workload.


|Approach| Benefit    |
|---------|---------|
|**Use Infrastructure as Code (IaC)** to define the repeatable aspects of the supply chain that's production ready. <br><br> Prefer declarative approaches over imperative methods.       | Declarative IaC technologies are designed with automation and reusability in mind. You'll be able to offload infrastructure deployments from individuals into tooling, achieving consistency with quality. <br><br> From an infrastructure perspective, choice of fewer technologies will remove variance in tooling and configuration drift can be easily detected. Maintenance will be easier. In addition, when the choice is aligned with the existing skillset, the team will easily adopt it. |
|**Prepare the team to use the chosen IaC technology.** Learn about its extensibility model, capabilities, and limitations. <br><br> Take advantage of specialization within the team and shared knowledge in the organization.| With upskilling, you'll be able to increase productivity and foster an environment of collaboration through shared learning.<br><br> You'll fill gaps with training instead of hiring.   |
|**Follow software recommendations** for IaC development and maintenance. <br><br>Modularize in moderation. Avoid custom or low-value abstractions. <br><br>  Follow a layered approach to reflect different lifecycles. Foundational layers where the lower layers stay constant and upper layers are changed as needed.<br><br> Deployment artifacts, such as application binaries IaC templates and parameters are part of the attack surface; apply assurances, such as secret management, access control, and other principles of the security pillar. | Artifacts will experience the same level of engineering rigor as application code. Quality controls through peer reviews and testing will give you confidence in deployment.<br><br> A layered approach provides ease of maintenance and creates boundaries that can help establish clear lines of responsibility.<br><br>Adding security controls to artifacts will help harden the system during the deployment process. |
|Develop a **common deployment** manifest that is used across all environments. Use that manifest as the default mechanism, whether that's for greenfield, incremental workload updates, or disaster recovery purposes.        | You'll remove the overhead of maintaining multiple assets. <br><br> In the event of a disaster, recovery will be quick and reliable because you'll deploy a tried-and-tested manifest instead of wasting effort in creating an ad-hoc environment.|
|Strive for **immutable and ephemeral infrastructure** that's deployed through IaC automation, where possible.   | You'll prohibit configuration drift and make the deployment idempotent. <br><br> It removes significant operational burdens, such as patching. It also benefits core validation scenarios, such as blue green infrastructure deployments.|


> [!NOTE] 
> Reduce the scope of portal usage to only non-repeating investigatory tasks.

## Automate for efficiency

|![Goal icon](../_images/goal.svg) Replace repetitive manual tasks with software automation that can complete them quicker, with greater consistency and accuracy, and reduce risks.| 
|--| 

The workload might have workflows with processes that involve team members doing mundane, repetitive, and time-consuming tasks that don't actually need human intellect. Depending on the frequency, you can spend considerable time on these efforts and in worst case scenarios, make more investment as the workload grows. Also, these processes are often error-prone due to human input.

Through automation, you can save time, effort, money and avoid mistakes.


| Approach    | Benefit   |
|---------|---------|
|**Evaluate all workflows** using a selection criteria that's at the right level of complexity, effort, frequency, accuracy, timeliness, lifespan. <br><br> **Automate workflows** based on that evaluation, prioritizing those with the highest expected returns.<br><br>**Remove redundant workflows** or add value to justify human effort. | You'll be able to reinvest any freed team capacity to higher value work, increasing productivity and consistency.<br><br> Building an inventory of workflows will make sure you automate the right tasks. Removing redundant tasks will reduce complexity and errors on tasks. |
|Be explicit about your decision when evaluating whether to **build custom tooling or buy software.**  <br><br> Reserve building automation for highly specialized and high value work.   | By buying off-the-shelf software, you'll save on maintenance costs by taking advantage of the support contract. <br><br> By building software, you have more control and can cater to use cases that are unique to your team and workload. However, there's a cost impact. <br><br> Choice of tooling can bring a level of standardization to your operations. With training, you'll achieve a uniform level of readiness for adoption. |
|Design your workload components to **support automation capabilities.**     | You'll be able to avoid the situation where lack of automation in your system design promotes the antipattern of repetitive tasks, slows down growth, and starts accumulating technical debt. |
|Treat all **automation as a critical dependency** of your workload. Adapt to the workload's expected growth. <br><br>  Be aware that your automation tooling is an integral part of your workload; make sure that it also adheres to the five Well-Architected pillars.     | Your automation component will be designed to withstand the risks, such as security threats. With applied best practices, you'll be able to avoid implementation sprawl.<br><br>The workload will continue to operate with a high-level guarantee if this dependency is kept functional and safe. |
|**Automate at-scale** by exploring options beyond your workload. <br><br>**Favor a "design once, run everywhere"** model by providing templates and frameworks to onboard new projects and promote reuse existing designs and implementations. | You will be able to employ tried and tested methods and reduce chances of failure. | 



## Adopt safe deployment practices

|![Goal icon](../_images/goal.svg) Implement guardrails in the deployment process to minimize the impact of errors or unexpected conditions.| 
|--|

During the development cycle, workload artifacts will go through numerous changes as they get implemented, bugs are fixed, tested, and so on.

The deployment process must follow a standard operating procedure. Any change must be deployed with the same level of rigor. This principle applies equally to code, configuration, and all related artifacts. The key is to apply safe practices as early as possible so that you have predictability in production. Even if errors reach the users, recovery changes should be rolled out as soon as possible.


|Approach  |Benefit  |
|---------|---------|
|**Standardize the process to deploy any change by using automated deployment processes, such as pipelines.** <br><br> All environments must use pipelines. <br><br> Classify assets and versions per environment to **easily** traceable and identifiable.        | *Consistent deployment methods reduce* issues caused by process errors and variance. You can focus your effort on the workload concerns. <br><br>Standardization will ensure that the deployment is completed safely, reliably, and with repeatability. <br><br> Classification makes it easy to view logs of previous deployment and issues that might have occurred. You might be able to use that information to expedite the rollback roll-forward operations.|
|Deploy **small incremental updates** at a regular cadence. | Frequent, well-tested, small-sized updates will make *validation of the release easier*. <br><br>Faster troubleshooting with *minimal user impact* due to a smaller footprint. |
|**Test updates rigorously by using different mechanisms** throughout the development lifecycle.    | You'll be able to *catch issues in the early* stages of development. Iterative fixes and consistent deployment practices will cause issues to taper off by the time the update is ready for production.|
|**Roll out updates gradually with due diligence.** <br><br>  Use deployment models that give you the control to **progressively increase the number of instances and the users** until the update is safely adopted by all.       | Test each update in a controlled manner so that issues are fixed early in production. You will avoid the situation where your entire user base is impacted by a faulty update.<br><br> You'll also be able to test whether the update is backward and forward compatible.       |
| Have a mitigation strategy to quickly **recover from deployment failures.**  <br><br> The strategy should cover decision making on **rolling back or forward,** based on the criticality of the issue.<br><br> Have **well-defined processes and automated systems** that can rapidly roll out fixes by using the standard deployment pipelines.| You'll be able to reduce the duration of potential impact.<br><br> Also, you can restore the system back to the previous working version or roll forward to a version that has fixes, which have been thoroughly tested. |
| Have a fallback plan **that resets the system** to a working state in case of emergency and to recover from unexpected failures. <br><br>Strive to improve the plan over time so that Use this strategy sparingly, only when necessary and with approval. | You'll be able to fast-track high-priority fixes, such as security remediations.  <br><br>  The accelerated pipeline might not have all the checks of your standard operating procedures, but you'll be able to get the users to a safe version in the fastest way possible outweighing lower impact faults.      |


