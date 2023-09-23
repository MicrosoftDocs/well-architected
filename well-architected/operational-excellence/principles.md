---
title: Operational Excellence cloud design principles
description: Learn about industry principles that support operational excellence and help you address common challenges in cloud workloads.   
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/15/2023
ms.topic: conceptual
---

# Operational Excellence design principles

At the core of this pillar are DevOps practices that ensure workload quality through standardized workflows and team cohesion. This pillar defines operating procedures for development practices, observability, and release management. The goal is to minimize process variance, chances of human errors, and disruption to the users. To assess your operational health, start with these questions:

- Do you execute operations with discipline?
- Are customers using the workload with maximum predictability?
- How do you learn from experience and collected data to drive continuous improvement?

Workload operations can devolve into chaotic practices when there's no clear ownership or leadership. In this type of environment, teams often resort to methods that are executed with high effort and produce low outcomes, which leads to poor user experience. These approaches meet only short-term goals. Long-term benefits are realized through continuous evaluation and strategic investments.

The design principle approaches aren't checklist items. They provide guidelines for operational strategies that must be considered to address the underlying causes and not just treat symptoms. Start with the recommended approaches, and then observe what works and what doesn't to identify areas of improvement. After you've set your strategy, continue to drive actions by using the Operational Excellence checklist.

The operational requirements of a workload are as important as its business requirements. Efficient processes make sure the workload achieves business outcomes within the constraints of compliance, whether that's organizational or external. The key is to find repeatability with consistency.

The goals of the Operational Excellence pillar are to do the right thing, the right way, and to solve the right problems as a team.

If you meet these goals, workloads will run reliably and predictably even during times of change. Inability to fulfill operational requirements can lead to failed deployments, inconsistent user experience, and added costs that could have been avoided through proper planning and streamlined execution.

## Embrace DevOps culture

|![Goal icon](../_images/goal.svg) Empower development and operations teams to continuously improve their system design and processes by working together with the mindset of collaboration, shared responsibility, and ownership.|
|--|

DevOps is a community of practice where diversity of perspective and skills drives toward one mission. Teams must foster a collaborative environment of shared knowledge instead of siloed learning. Shared functions must strive to overcome resource constraints.

A good DevOps culture thrives on shared responsibility. Development and operations teams should align their goals and priorities with the expectations of their customers and keep business focus in mind. The development team should involve the operations team in the feedback loop so the improvements are driven upstream and other teams benefit equally. Conversely, operations teams are responsible for making the development team successful in their business outcomes by sharing resources and feedback that are relevant to the workload.

At the same time, DevOps practices apply clear lines of ownership and accountability to each team. Regardless of where the application runs, the workload team is responsible for that application.

DevOps optimizes operational tasks so that they're effective but not burdensome. To reap the full benefit of DevOps, the culture should not only optimize processes through technology, but also have processes for people in the organization to promote transparent communication.

|Approach  |Benefit  |
|---------|---------|
|**Use common systems and tools** that promote a collaborative environment for communication and tracking progress.      |  Common tools and processes enable **transparent communication**. Both development and operations teams benefit from situational awareness across various environments, common support issues, and overall challenges and wins. <br><br> Teams will already be familiar with existing escalation paths if there's an incident. <br><br>  A shared backlog makes priorities clear, whether they're working on new features or fixing bugs.   |
|Build a **continuous learning and experimentation mindset** throughout the development cycle.   <br><br> **Support knowledge sharing** across teams and maintain documentation for reuse. <br><br> Conduct blameless analysis and debrief post-release and/or post-incident reviews.  |  Through experimentation mechanisms, such as A/B testing and developing proofs of concept, you can encourage innovation while keeping costs low.  <br><br> Share knowledge through collaboration that makes the team proficient in design approaches, tooling, and processes. <br><br> Doing retrospectives after a project helps **identify areas for improvement** and celebrate success.      |
| **Adopt proven industry agile practices** that focus on action optimization.  <br><br> Look for **opportunities to "shift left"** in operations for people and automated processes, deployment and quality assurance practices, and observability.    | Agile development practices lead to shorter release lifecycles, which are an indicator of business value.  <br><br>  Detecting, resolving, and thereby preventing issues earlier is often less intrusive to the process.  |
| **Set standards for all development and operational procedures and review and validate them at a regular cadence**. <br><br> These procedures include routine tasks, out-of-band processes, emergency drills and situations, choice of tooling, monitoring procedures, skilling plans, and even communication with stakeholders and customer disclosures. <br><br> **Be intentional and explicit about your decisions**.        | Standards add predictability to operations and make processes and practices scalable. Validating standards is a great way of drawing points of improvement. <br><br> Be prepared for emergency and recovery situations by conducting regular drills. <br><br> Execute with precision and enable governance to **prevent anomalies** that lead to risks.   |
|  **Leverage centralized operations team** with **specialized skills** and breadth of experience.   | There's a cost benefit to using shared resources both for operations and resources. <br><br>   While you own your workload, the centralized team helps you with cross-functional skills, such as incident management, proactive perspective on monitoring, and outsourcing expertise with trust. |

## Establish development standards

|![Goal icon](../_images/goal.svg) Optimize productivity by standardizing development practices, enforcing quality gates, and tracking progress and success through systematic change management.|
|--|

The development team is responsible for addressing workload issues prior to release with minimal friction. Be mindful of developer efficiency and optimize for fast turnaround cycles, from coding to testing results. Implement effective and right-sized processes that not only plan and standardize technical activities, but also drive consensus within the team and the stakeholders.

|Approach  |Benefit  |
|---------|---------|
|**Document workload features** and capture customer benefits.  <br><br>  Derive **scope and detailed functional and nonfunctional requirements** of the architecture. <br><br> **Create sizing estimation models** to report on scope and cost of the tasks involved.   |Good specifications **cut operational costs and chances of failure** by supporting more productive and streamlined development cycles. <br><br>  Developers understand the **technical design, goals, and completion criteria** before they start the coding cycle. <br><br>  Good documentation facilitates repeatable **communication** and **onboarding** of new team members.      |
|Use an **industry standard software development methodology that's appropriately tuned** for the needs of your workload and team size. <br><br> **Maintain a backlog that's shared among all roles**.      | Adoption of a well-known methodology **sets the rhythm of the project**. It removes process ambiguities by giving team members clear expectations and accountability. <br><br> By tracking against a common list, **tasks can be refined and prioritized** with standard practices. The project will have better chances of being delivered on time. <br><br> Standard methodologies help with **risk management**. With granular milestone reviews, developers can address potential issues before they become showstoppers.      |
|**Use unified source control** for all code, scripts, deployment templates, pipeline definitions, and related documentation.<br><br> The branching strategy must support friction-free release of independent and interdependent features, bug fixes, and hotfixes. <br><br>  Use shared knowledge across the organization to build your branching strategy and deployment processes.     | Proper use of source control is crucial in supporting **concurrent changes** and versioning.  <br><br> Maintain a repeatable workflow for releasing changes of various sizes and risks, conduct peer reviews as part of the process, and keep an audit trail.     |
|Have **quality assurance** processes that emphasize testing early in the development lifecycle. <br><br>  **Include all artifacts for planned test procedures**, including application components, infrastructure, and data plane operations that are part of a feature release or update. <br><br>  Treat artifacts as immutable when they're promoted through environments, gaining confidence each time they pass through a quality gate. <br><br>  Automate routine checks, where practical.   | Quality assurance makes sure that functional and nonfunctional requirements were met with confidence, which leads to positive customer impact. <br><br> Having test plans ensures quality and completeness while taking possible failure cases into consideration.<br><br>  With quality gates, you can enforce best practices to reduce risks. <br><br> Immutability brings confidence because it ensures the system that you test is exactly what you release.<br><br> Testing cycles efficiently block progress unless quality criteria are met.        |
|Drive consistency by using **style guides and tools**, which enforce **conventions**, and adopt a **common tool chain** for development, testing, and communication with stakeholders.  <br><br>**Technology standards for developers should** necessitate implementation **of patterns**, API design, **logging, exception handling, and more**.       | Consistency in code drives readability and easier maintenance. It also reduces complexity and enables code reuse. <br><br> Common tooling and conventions also help teams optimize processes without the need to address one-off choices.   |         |
|Consistently and deliberately insist on developer documentation of code as written.     |  Clear code documentation ensures logic and functionality is quickly and easily understood when old code needs to be revisited or development teams rotate.       |
|Report progress and trends to measure efficiency.      | Trends in bugs, failed updates, time to deploy, feedback loops, and other metrics are published and that drives improvements.         |

## Evolve operations with observability

|![Goal icon](../_images/goal.svg) Gain visibility into the system, derive insight, and make data-driven decisions.|
|--|

Build a culture that continuously improves quality by monitoring the workload and taking all the pillars of the Well-Architected Framework into consideration. Enable the team and stakeholders to make both short-term and long-term decisions across many facets by providing the data, statistics, and trends to enable decision making. Learn from your monitoring data and drive improvements.

Monitoring operations is key in proactive maintenance of the application, quality and security assurance, capacity planning, and product management.

A crucial aspect of monitoring is application health modeling that helps you anticipate issues before they become incidents and impact customer experience. Efficient monitoring reduces reactive cycles spent on incident management.

|Approach   | Benefit   |
|---------|---------|
|**Build a monitoring system with its own stack and flows**. <br><br> Treat the monitoring system as a dimension of the workload that's decoupled from its utility. The stack must cover all layers including infrastructure, application health, and build and release processes. <br><br> **Capturing or sampling business data** is out of scope for observability implementations. | Decoupling monitoring and workload stacks **separates functional requirements and observability requirements** and makes independent evolution possible. Changes in code shouldn't impact monitoring and vice versa. <br><br> Because observability requirements are separate from functional requirements, **business data** **will not be disrupted** by monitoring configuration changes or outages.  |
|**Drive consistency** in the collection process for each **type of data source**. <br><br> Standardize instrumentation in code by using industry standards for telemetry, collection of infrastructure metrics, and tooling. | You want consistency to avoid variance in sensing and measurement because familiarity across similar resources **reduces time spent correlating and analyzing data**. You'll have a holistic perspective to anticipate issues. <br><br>  |
|**Emit telemetry** from application code that correlates the key points of the execution flow and gives an end-to-end view at different levels of granularity.        | Prioritize actions based on the severity level and understand the context given its verbosity. This information is crucial for troubleshooting purposes.  |
|**Own the responsibility of emitting and collecting data**, even when data sinks are shared by multiple teams and managed by central teams.      | By localizing monitoring data to the workload environment, the team can access logs and metrics to address workload concerns. |
|Collect **just enough data** and retain it for **just enough time**. <br><br>Consider the cost tradeoffs associated with storing logging data. | Being intentional about collection helps you **optimize financial and operational costs** associated with collecting more data than needed.<br><br> **Minimize the noise and avoid intensive computation** during analysis and reduce the cost of storing data that is no longer needed.|
|**Make a distinction between the use of different monitoring signals**: profiles, logs, metrics, and traces. Use each for the right purpose. <br><br> Prioritize the **use of metrics to trigger actions** that rely on numeric measurements. <br><br> Use **profiles to get lower-level visibility**, such as memory allocation, into the system. <br><br>Reserve the **use of logs and traces to provide context** for flows and dependencies.       | By using the signals for the right purposes, you can prevent inefficient implementation of the monitoring system.<br><br> For example, using logs for actions requires parsing. You might be able to achieve the same goals faster with metrics.   |
|**Aggregate and visualize data** in dashboards to present monitoring data catered to audiences, keeping the business context in mind. <br><br> Use **situational dashboards** for surfacing data to drive awareness among the stakeholders.<br><br> **Use operational dashboards and workbooks** with drill-down capabilities for operator activities like incident response. Frequently refresh and provide granular data. | With visualizations, you can analyze trends, track against business targets, and manage incidents.<br><br> Dashboards that are tailored to the interest of the customer make interpretation relevant and accelerate time to detection and action.|
|**Make alerts actionable** by notifying the accountable roles with standardized descriptions and severity levels. Provide information that's collated from various sources and track deviations from business targets. <br><br> **Trigger alerts only for incidents that require action**. <br><br> **Strive for proactive** and thought-provoking alerts that initiate actions before a degraded state becomes a failure.| Alerts bring attention to significant events as defined by the organization. <br><br>A good alert system identifies actions and severity and provides just enough data to drive clarity and purpose. **Operators can start on remediation without delay**. |

## Deploy with confidence

|![Goal icon](../_images/goal.svg) Reach the desired state of deployment with predictability.|
|--|

Build a workload supply chain that enables you to consistently reach the goal of predictability in all of your environments, across the workload's hosting platforms, applications, data, and configuration resources. The deployment mechanism must be capable of automation, testing, monitoring, and versioning. It should be modularized and ready to execute on demand. It shouldn't be represented as a monolithic end-to-end process. The supply chain isn't necessarily for faster execution, but to achieve consistency and self-documentation over multiple iterations.

The workload team is accountable for the supply chain as it relates to their own workload.

|Approach| Benefit    |
|---------|---------|
|**Use Infrastructure as Code (IaC)** to define the repeatable aspects of the supply chain that are production ready. <br><br> Prefer declarative approaches over imperative methods.       | Declarative IaC technologies are designed with automation and reusability in mind. You can offload infrastructure deployments from individuals into tooling and achieve consistent quality. <br><br> From an infrastructure perspective, having fewer technology choices removes variance in tooling and makes configuration drift easy to detect. Maintenance will be easier. In addition, when the choice is aligned with the existing skill set, the team can easily adopt it. |
|**Prepare the team to use the chosen IaC technology**. Learn about its extensibility model, capabilities, and limitations. <br><br> Take advantage of specialization within the team and shared knowledge in the organization.| Upskilling increases productivity and fosters an environment of collaboration through shared learning.<br><br> You can fill gaps with training instead of hiring.   |
|**Follow software recommendations** for IaC development and maintenance. <br><br>Modularize in moderation. Avoid custom or low-value abstractions. <br><br>  Follow a layered approach to reflect different lifecycles. Form foundational layers where the lower layers stay constant and the upper layers change as needed.<br><br> Deployment artifacts, such as application binaries, IaC templates, and parameters, are part of the attack surface. Apply assurances, such as secret management, access control, and other principles of the Security pillar. | Artifacts experience the same level of engineering rigor as application code. Quality controls through peer reviews and testing give you confidence in deployment.<br><br> A layered approach provides ease of maintenance and creates boundaries that establish clear lines of responsibility.<br><br>Adding security controls to artifacts helps harden the system during the deployment process. |
|Develop a **common deployment** manifest that's used across all environments. Use that manifest as the default mechanism, whether that's for greenfield, incremental workload updates, or disaster recovery purposes.        | Remove the overhead of maintaining multiple assets. <br><br> If there's a disaster, recovery will be quick and reliable because you'll deploy a tried and tested manifest instead of wasting effort creating an improvised environment.|
|Strive for **immutable and ephemeral infrastructure** that's deployed through IaC automation.   | Prohibit configuration drift and make the deployment idempotent. <br><br> It removes significant operational burdens, such as patching. It also benefits core validation scenarios, such as blue-green infrastructure deployments.|

> [!NOTE]
> Reduce the scope of portal usage to only non-repeating investigatory tasks.

## Automate for efficiency

|![Goal icon](../_images/goal.svg) Replace repetitive manual tasks with software automation that completes them quicker, with greater consistency and accuracy, and reduces risks.|
|--|

The workload might have workflows with processes that involve team members doing mundane, repetitive, and time-consuming tasks that don't actually need human intellect. Depending on the frequency, you can spend considerable time on these efforts, investing more as the workload grows. Also, these processes are often error-prone due to human input.

Through automation, you save time, effort, and money, and you avoid mistakes.

| Approach    | Benefit   |
|---------|---------|
|**Evaluate all workflows** against criteria that's at the right level of complexity, effort, frequency, accuracy, timeliness, and lifespan. <br><br> **Automate workflows** based on that evaluation and prioritize the workflows with the highest expected returns.<br><br>**Remove redundant workflows** or add value to justify human effort. | You can reinvest freed team capacity in higher value work and increase productivity and consistency.<br><br> Building an inventory of workflows ensures that you automate the right tasks. Removing redundant tasks reduces complexity and errors. |
|Be explicit about your decision when evaluating whether to **build custom tooling or buy software**.  <br><br> Reserve building automation for highly specialized and high value work.   | By buying off-the-shelf software and taking advantage of the support contract, you save on maintenance costs. <br><br> By building software, you have more control and can cater to use cases that are unique to your team and workload. However, there's a cost impact. <br><br> Choice of tooling brings a level of standardization to your operations. With training, you can achieve a uniform level of readiness for adoption. |
|Design your workload components to **support automation capabilities**.     | Avoid the situation where lack of automation in your system design promotes the anti-pattern of repetitive tasks, slows down growth, and starts accumulating technical debt. |
|Treat all **automation as a critical dependency** of your workload. Adapt to the workload's expected growth. <br><br>  Your automation tooling is an integral part of your workload, and it should adhere to the five Well-Architected Framework pillars.     | Design your automation component to withstand risks, such as security threats. With applied best practices, you can avoid implementation sprawl.<br><br>The workload will continue to operate with a high-level guarantee if this dependency is kept functional and safe. |
|**Automate at-scale** by exploring options beyond your workload. <br><br>**Favor a "design once, run everywhere"** model by providing templates and frameworks to onboard new projects and promote reuse of existing designs and implementations. | Employ tried and tested methods and reduce chances of failure. |

## Adopt safe deployment practices

|![Goal icon](../_images/goal.svg) Implement guardrails in the deployment process to minimize the impact of errors or unexpected conditions.|
|--|

During the development cycle, workload artifacts go through numerous changes as they get implemented and tested and bugs are fixed.

The deployment process must follow a standard operating procedure. Any change must be deployed with the same level of rigor. This principle applies equally to code, configuration, and all related artifacts. The key is to apply safe practices as early as possible so that you have predictability in production. Even if errors reach the customers, recovery changes should be rolled out as soon as possible.

|Approach  |Benefit  |
|---------|---------|
|**Standardize the process to deploy any change by using automated deployment processes, such as pipelines**. <br><br> All environments must use pipelines. <br><br> Classify assets and versions per environment to make them **easily** traceable and identifiable.        | **Consistent deployment methods reduce** issues caused by process errors and variance and allow you to focus your effort on the workload concerns. <br><br>Standardization ensures that the deployment is completed safely, reliably, and with repeatability. <br><br> Classification makes it easy to view logs of previous deployment and issues that might have occurred. You might be able to use that information to expedite the rollback roll-forward operations.|
|Deploy **small incremental updates** at a regular cadence. | Frequent, well-tested, small updates make **validation of the release easier**. <br><br>Troubleshoot faster with **minimal customer impact** due to a smaller footprint. |
|**Test updates rigorously by using different mechanisms** throughout the development lifecycle.    | **Catch issues in the early** stages of development. Iterative fixes and consistent deployment practices cause issues to taper off by the time the update is ready for production.|
|**Roll out updates gradually with due diligence**. <br><br>  Use deployment models that give you the control to **progressively increase the number of instances and customers** until the update is safely adopted by all.       | Test each update in a controlled manner so that issues are fixed early in production. Avoid impacting your entire customer base by a faulty update.<br><br> Test whether the update is backward and forward compatible.       |
| Have a mitigation strategy to quickly **recover from deployment failures**.  <br><br> The strategy should cover decision making on **rolling back or forward** based on the criticality of the issue.<br><br> Have **well-defined processes and automated systems** that can rapidly roll out fixes by using the standard deployment pipelines.| Reduce the duration of potential impact.<br><br> Restore the system back to the previous working version or roll forward to a version that has fixes, which have been thoroughly tested. |
| Have a fallback plan **that resets the system** to a working state in case of emergency and to recover from unexpected failures. Use this strategy sparingly, only when necessary and with approval. <br><br>Strive to improve the plan over time. | You can fast-track high-priority fixes, such as security remediation.  <br><br>  The accelerated pipeline might not have all the checks of your standard operating procedures, but you'll get customers to a safe version in the fastest way possible, which outweighs lower impact faults.      |