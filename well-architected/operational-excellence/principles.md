---
title: Operational Excellence cloud design principles
description: Learn about industry principles that support operational excellence and help you address common challenges in cloud workloads.   
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 05/05/2025
ms.topic: concept-article
ms.update-cycle: 1095-days  
---

# Operational Excellence design principles

At the core of the Operational Excellence pillar are DevOps practices that **ensure workload quality through standardized workflows and team cohesion**. This pillar defines operating procedures for **development practices, observability, and release management**. The goal is to minimize process variance, chances of human error, and disruption to customers. To assess your operational health, start with these questions:

- Do you execute operations with discipline?
- Are customers using the workload with maximum predictability?
- How do you learn from experience and collected data to drive continuous improvement?

Workload operations can devolve into chaotic practices when there's no clear ownership or leadership. In this type of environment, teams often resort to methods that are executed with high effort and produce low outcomes, which leads to poor user experience. These approaches meet only short-term goals. Long-term benefits are realized through **continuous evaluation and strategic investments**.

The design principles provide guidelines for operational strategies that must be considered to **address the underlying causes and not just treat symptoms**. Start with the recommended approaches, and then observe what works and what doesn't to identify areas of improvement. After you set your strategy, continue to drive action by using the [**Operational Excellence checklist**](./checklist.md).

The operational requirements of a workload are as important as its business requirements. Efficient processes ensure the workload achieves business outcomes within the constraints of compliance, whether that compliance is organizational or external. The key is to find repeatability with consistency.

The goals of the Operational Excellence pillar are **to do the right thing, to do it the right way, and to solve the right problems as a team**.

If you meet these goals, workloads will run reliably and predictably even during times of change. **Inability to fulfill operational requirements can lead to failed deployments**, inconsistent user experience, and added costs that could have been avoided through proper planning and streamlined execution.

## Embrace DevOps culture

|![Goal icon](../_images/goal.svg) Empower development and operations teams to continuously improve their system design and processes by working together with a mindset of collaboration, shared responsibility, and ownership.|
|--|

DevOps is a community of practice where diversity of perspective and skills drives toward one mission. Teams must **foster a collaborative environment of shared knowledge** instead of siloed learning. Use shared functions to strive to overcome resource constraints.

A good DevOps culture thrives on shared responsibility. Development and operations teams should align their goals and priorities with the expectations of their customers and keep business focus in mind. The development team should involve the operations team in the feedback loop so the improvements are driven upstream and other teams benefit equally. Conversely, operations teams are responsible for making the development team successful in their business outcomes by sharing resources and feedback that are relevant to the workload.

At the same time, DevOps practices **apply clear lines of ownership and accountability to each team**. Regardless of where the application runs, the workload team is responsible for that application.

DevOps optimizes operational tasks so that they're effective but not burdensome. To reap the full benefit of DevOps, the culture should optimize processes through technology and have processes for people in the organization to promote transparent communication.

|Approach  |Benefits  |
|---------|---------|
|**Use common systems and tools** that promote a collaborative environment for communication and tracking progress.      |  Common tools and processes enable **transparent communication**. Both development and operations teams benefit from situational awareness across various environments, common support issues, and overall challenges and wins. <br><br> Teams will already be familiar with existing escalation paths if there's an incident. <br><br>  A shared backlog makes priorities, such as working on new features or fixing bugs, clear.   |
|Build a **continuous learning and experimentation mindset** throughout the development cycle.   <br><br> **Support knowledge sharing** across teams and maintain documentation for reuse. <br><br> Conduct blameless analysis and debrief post-release and/or post-incident reviews.  |  Through experimentation mechanisms, such as A/B testing and developing proofs of concept, you can encourage innovation while keeping costs low.  <br><br> Share knowledge through collaboration that makes the team proficient in design approaches, tooling, and processes. <br><br> Doing retrospectives after a project helps **identify areas for improvement** and celebrate success.      |
| **Adopt proven industry agile practices** that focus on action optimization.  <br><br> Look for **opportunities to "shift left"** in operations for manual and automated processes, deployment and quality assurance practices, and observability.    | Agile development practices lead to shorter release lifecycles, which are an indicator of business value.  <br><br>  Detecting, resolving, and thereby preventing issues earlier is often less intrusive to the process.  |
| **Set standards for all development and operational procedures and review and validate them at a regular cadence**. <br><br> These procedures include routine tasks, out-of-band processes, emergency drills and situations, choice of tooling, monitoring procedures, skilling plans, and even communication with stakeholders and customer disclosures. <br><br> **Be intentional and explicit about your decisions**.        | Standards add predictability to operations and make processes and practices scalable. Validating standards is a great way to draw points of improvement. <br><br> Be prepared for emergency and recovery situations by conducting regular drills. <br><br> Execute with precision and enable governance to **prevent anomalies** that lead to risks.   |
|  **Take advantage of centralized operations teams** with **specialized skills** and breadth of experience.   | There's a cost benefit to using shared resources both for operations and resources. <br><br>   Although you own your workload, the centralized team helps you with cross-functional skills, such as incident management, a proactive perspective on monitoring, and outsourcing expertise with trust. |

## Establish development standards

|![Goal icon](../_images/goal.svg) Optimize productivity by standardizing development practices, enforcing quality gates, and tracking progress and success through systematic change management.|
|--|

The development team is responsible for addressing workload issues prior to release with minimal friction. Be mindful of developer efficiency and **optimize for fast turnaround cycles**, from coding to testing results. Implement effective and right-sized processes that plan and standardize technical activities and also drive consensus within the team and the stakeholders.

|Approach  |Benefits  |
|---------|---------|
|**Document workload features** and capture customer benefits.  <br><br>  Derive **scope and detailed functional and nonfunctional requirements** of the architecture. <br><br> **Create sizing estimation models** to report on scope and cost of the tasks involved.   | Good specifications **cut operational costs and chances of failure** by supporting more productive and streamlined development cycles. <br><br>  Developers understand the **technical design, goals, and completion criteria** before they start the coding cycle. <br><br>  Good documentation facilitates repeatable **communication** and **onboarding** of new team members.      |
|Use an **industry standard software development methodology that's appropriately tuned** for the needs of your workload and team size. <br><br> **Maintain a backlog that's shared among all roles**.      | Adoption of a well-known methodology **sets the rhythm of the project**. It removes process ambiguities by giving team members clear expectations and accountability. <br><br> By tracking against a common list, **tasks can be refined and prioritized** with standard practices. The project will have better chances of being delivered on time. <br><br> Standard methodologies help with **risk management**. With granular milestone reviews, developers can address potential issues before they become showstoppers.      |
|**Use unified source control** for all code, scripts, deployment templates, pipeline definitions, and related documentation.<br><br> The branching strategy must support friction-free release of independent and interdependent features, bug fixes, and hotfixes. <br><br>  Use shared knowledge across the organization to build your branching strategy and deployment processes.     | Proper use of source control is crucial in supporting **concurrent changes** and versioning.  <br><br> Maintain a repeatable workflow for releasing changes of various sizes and risks, conduct peer reviews as part of the process, and keep an audit trail.     |
|Have **quality assurance** processes that emphasize testing early in the development lifecycle. <br><br>  **Include all artifacts for planned test procedures**, including application components, infrastructure, and data plane operations that are part of a feature release or update. <br><br>  Treat artifacts as immutable when they're promoted through environments, gaining confidence each time they pass through a quality gate. <br><br>  Where practical, automate routine checks.   | Quality assurance ensures that functional and nonfunctional requirements were met with confidence, which leads to positive customer impact. <br><br> Having test plans ensures quality and completeness and takes possible failure cases into consideration.<br><br>  With quality gates, you can enforce best practices to reduce risks. <br><br> Immutability brings confidence because it ensures the system that you test is exactly what you release.<br><br> Testing cycles efficiently block progress unless quality criteria are met.        |
|Drive consistency by using **style guides and tools**, which enforce **conventions**, and adopt a **common tool chain** for development, testing, and communication with stakeholders.  <br><br>**Technology standards for developers should** necessitate implementation **of patterns**, API design, **logging, exception handling, and other processes**.       | Consistency in code drives readability and easier maintenance. It also reduces complexity and enables code reuse. <br><br> Common tooling and conventions also help teams optimize processes without the need to address one-off choices.   |
|Consistently and deliberately insist on developer documentation of code as its written.     |  Clear code documentation ensures that logic and functionality are easily understood when old code needs to be revisited or when development teams rotate.       |
|**Report progress and trends** to measure efficiency.      | Trends in bugs, failed updates, time to deploy, feedback loops, and other metrics are published, and that drives improvements.         |

## Evolve operations with observability

|![Goal icon](../_images/goal.svg) Gain visibility into the system, derive insight, and make data-driven decisions.|
|--|

Build a culture that continuously **improves quality by monitoring the workload** and taking all the pillars of the Azure Well-Architected Framework into consideration. Enable the team and stakeholders to make both short-term and long-term decisions across many facets by providing the necessary data, statistics, and trends. Learn from your data and drive improvements.

Operations built for the purposes of observability are key in proactive maintenance of the application, quality and security assurance, capacity planning, and product management.

A crucial aspect of monitoring is application **using health modeling to help you anticipate issues before they become incidents** and affect customer experience. Efficient monitoring reduces reactive cycles spent on incident management.

|Approach  |Benefits  |
|---------|---------|
|**Build a monitoring system with its own stack and flows**. <br><br> Treat the monitoring system as a dimension of the workload that's decoupled from its utility. The stack must cover all layers, including infrastructure, application health, and build and release processes. <br><br> **Capturing or sampling business data** is out of scope for observability implementations. | Decouple monitoring and workload stacks to **separate functional requirements and observability requirements** and make independent evolution possible. Changes in code shouldn't affect monitoring, and vice versa. <br><br> Because observability requirements are separate from functional requirements, **business data** **won't be disrupted** by monitoring configuration changes or outages.  |
|**Drive consistency** in the collection process for each **type of data source**. <br><br> Standardize instrumentation in code by using industry standards for telemetry, collection of infrastructure metrics, and tooling. | Consistency prevents variance in sensing and measurement because familiarity across similar resources **reduces time spent correlating and analyzing data**. You have a holistic perspective to anticipate issues. <br><br>  |
|**Emit telemetry** from application code that correlates the key points of the execution flow and gives an end-to-end view at different levels of granularity.        | Prioritize actions based on the severity level, and understand the context given its verbosity. This information is crucial for troubleshooting purposes.  |
|**Own the responsibility of emitting and collecting data**, even when data sinks are shared by multiple teams and managed by central teams.      | By localizing monitoring data to the workload environment, the team can access logs and metrics to address workload concerns. |
|Collect **just enough data** and retain it for **just enough time**. <br><br>Consider the cost tradeoffs associated with logging and storing data. | Intentional data collection helps you **optimize financial and operational costs** associated with collecting more data than you need.<br><br> **Minimize the noise and avoid intensive computation** during analysis, and reduce the cost of storing data that you no longer need.|
|**Make a distinction between the different monitoring signals**: profiles, logs, metrics, and traces. Use each signal for the right purpose. <br><br> Prioritize the **use of metrics to trigger actions** that rely on numeric measurements. <br><br> Use **profiles to get lower-level visibility**, such as memory allocation, into the system. <br><br>Reserve the **use of logs and traces to provide context** for flows and dependencies.       | By using the signals for the right purposes, you can prevent inefficient implementation of the monitoring system.<br><br> For example, using logs for actions requires parsing. You might be able to achieve the same goals faster with metrics.   |
|**Aggregate and visualize data** in dashboards to present monitoring data that's catered to audiences and keeps the business context in mind. <br><br> Use **situational dashboards** for surfacing data to drive awareness among the stakeholders.<br><br> **Use operational dashboards and workbooks** with drill-down capabilities for operator activities like incident response. Frequently refresh the dashboards and provide granular data. | With visualizations, you can analyze trends, track against business targets, and manage incidents.<br><br> Dashboards that are tailored to the interest of the customer make interpretation relevant and accelerate time to detection and action.|
|**Make alerts actionable** by notifying the accountable roles with standardized descriptions and severity levels. Provide information that's collated from various sources and track deviations from business targets. <br><br> **Trigger alerts only for incidents that require action**. <br><br> **Strive for proactive** and thought-provoking alerts that initiate actions before a degraded state becomes a failure.| Alerts bring attention to significant events as defined by the organization. <br><br>A good alert system identifies actions and severity and provides just enough data to drive clarity and purpose. **Operators can start on remediation without delay**. |

## Automate for efficiency

|![Goal icon](../_images/goal.svg) **Replace repetitive manual tasks with software automation** that completes them quicker, with greater consistency and accuracy, and reduces risks.|
|--|

The workload might have workflows with processes that involve team members doing mundane, repetitive, and time-consuming tasks that don't actually need human intellect. Depending on the frequency, you might spend considerable time on these efforts, investing more time as the workload grows. Also, these processes are often error-prone due to human input.

Through automation, you save time, effort, and money, and you avoid mistakes.

|Approach  |Benefits  |
|---------|---------|
|**Evaluate all workflows** against criteria that's at the right level of complexity, effort, frequency, accuracy, timeliness, and lifespan. <br><br> **Automate workflows** based on that evaluation and prioritize the workflows with the highest expected returns.<br><br>**Remove redundant workflows** or add value to justify human effort. | You can reinvest team capacity in higher value work and increase productivity and consistency.<br><br> Building an inventory of workflows ensures that you automate the right tasks. Removing redundant tasks reduces complexity and errors. |
|Be explicit about your decision when you evaluate whether to **build custom tooling or buy software**.  <br><br> Reserve building automation for highly specialized and high-value work.   | By buying off-the-shelf software and taking advantage of the support contract, you save on maintenance costs. <br><br> By building software, you have more control and can cater to use cases that are unique to your team and workload. However, there's a cost impact. <br><br> Choice of tooling brings a level of standardization to your operations. With training, you can achieve a uniform level of readiness for adoption. |
|Design your workload components to **support automation capabilities**.     | Avoid the situation where lack of automation in your system design promotes the anti-pattern of repetitive tasks, slows down growth, and starts accumulating technical debt. |
|Treat all **automation as a critical dependency** of your workload. Adapt to the workload's expected growth. <br><br>  Your automation tooling is an integral part of your workload, and it should adhere to the five Well-Architected Framework pillars.     | Design your automation component to withstand risks, such as security threats. With applied best practices, you can avoid implementation sprawl.<br><br>The workload will continue to operate with a high-level guarantee if this dependency is kept functional and safe. |
|**Automate at-scale** by exploring options beyond your workload. <br><br>**Favor a "design once, run everywhere"** model by providing templates and frameworks to onboard new projects and promote reuse of existing designs and implementations. | Employ tried and tested methods and reduce chances of failure. |

## Adopt safe deployment practices

|![Goal icon](../_images/goal.svg) Achieve consistency in deployments by using guardrails that reduce the effect of errors and unexpected conditions.|
|--|

Build an automated and modular workload supply chain to ensure consistent, predictable, and repeatable deployments across all environments. Applying safe practices early ensures confidence in production and enables quick recovery if issues reach customers.

All changes, whether code, configuration, or artifacts, must be deployed with the same level of rigor. Testing, monitoring, and versioning are common practices to achieve consistency.

|Approach  |Benefits  |
|---------|---------|
|**Use Infrastructure as Code (IaC)** to define the desired state of all infrastructure.  <br><br> Use a modular and layered approach but avoid unnecessary abstractions. <br><br>Align layers with lifecycle needs, keeping foundational layers stable.|IaC enables deployment automation and consistency and serves as self-documentation that can be used for tracing. IaC artifacts become part of your software development lifecycle, which enables testing and quality review processes. <br><br>IaC also helps detect and mitigate configuration drift. |
|**Prefer small, incremental updates** that are deployed frequently. | Smaller updates simplify validation by reducing the number of concurrent errors. When multiple defective changes are released simultaneously, they can significantly increase the blast radius.|
|Deploy every code and infrastructure change by using **automated pipelines** across all environments. | Consistent deployment methods reduce errors and variance making deployments reliable and repeatable. The deployment process documents itself, and each run creates a record of activities.|
|**Test updates rigorously** throughout the development lifecycle, in preproduction and production environments.    | Early testing catches issues sooner, allows for iterative fixes and reduces problems by the time the update is ready for production. <br><br>Having multiple pre-production environments enables various types of testing, boosting confidence in a successful production release.|
|**Roll out new features** using deployment patterns that allow for progressive exposure and gradual adoption by users. <br><br> Test for backward and forward compatibility.      | Controlled rollout of updates reduces the risk of widespread issues from defects. Gradually increasing exposure helps ensure compatibility and stability, building confidence in the release.      |
|Be ready with compensating actions to **recover from faulty deployments or critical defects** in production. <br><br>Use **automation backed by testing** to roll out fixes. <br><br>For emergency updates, have an expedited process that's pre-approved by stakeholders.| Having a mitigation plan reduces the duration of potential impact. <br><br>You can quickly deploy urgent fixes, like security patches, to get users a safe version faster. |


## Next steps

We recommend that you review the Operational Excellence checklist to explore other concepts. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 
