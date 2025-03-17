---
title: Operational Excellence Maturity Model
description: Understand the maturity model levels of the Operational Excellence pillar.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 01/21/2024  
ms.topic: conceptual
---

# Operational Excellence maturity model

<!-- Introductory paragraph 
Required. Lead with a light intro that describes what the article covers.
-->

[add your introductory paragraph]

[add art]

:::image type="content" source="../_images/operational-excellence.svg" alt-text="Example alt-text.":::

# [**Level 1 - DevOps foundation**](#tab/level1)

![Goal icon](../_images/goal.svg) **Emphasize teamwork and unity in problem solving to establish a strong foundation that allows for consistent and stable operations in later stages.**


Establish a DevOps mindset at Level 1 to ensure the success of future strategies. Implement well-established DevOps methodologies to enhance process efficiency, and focus on building the essential and shared vocabulary, processes, and tools needed for stable operations.


#### &#10003; Encourage collaboration and foster a blameless culture 

Align team efforts with business needs while fostering a collaborative culture.

Workload operations are often managed by members from centralized teams, full-time staff dedicated to workload functionality, third parties, or vendors. They should function as a collective force, with mutual respect and acknowledgment for each other's expertise. There are complexities and friction when teams try to operate as independent parts, which undermine the goal of functioning as a single efficient system, driving towards business outcomes.

Advocate for a unified approach to problem-solving, reducing a siloed sense of ownership. All efforts should cater to what the business needs. Both successes and failures should be regarded as shared outcomes.

#### &#10003; Adopt standard collaboration methodologies and tools

Get started with industry-proven tools and software development lifecycle (SDLC)  processes that are relevant to your workload that aim to make development cycles efficient. Avoid reinventing the wheel and steer clear of custom methodologies, as they often introduce higher friction.

Popular choices include Agile, Scrum, and Kanban boards. These tools are familiar to most experienced developers, DevOps engineers, and product owners, minimizing the learning curve for new hires.

Initially, start incorporating the idea of standardization using established industry standards; process optimization can come later. Ensure that the tools you select can grow with your needs, without requiring a switch to cutting-edge solutions prematurely. 


#### &#10003; Plan to use Infrastructure-as-Code (IaC) as your primary deployment approach

Use a declarative approach as the standard for deployments to ensure consistency, repeatability, and long-term benefits like automation, self-documentation, and change history.

Prefer IaC deployments over portal deployments to avoid risks from inconsistent configurations and lack of testing. Also, avoid compiled languages or proprietary formats limited to specific programs.

Start with a good foundation by using tools that are natively supported on Azure, like [Bicep](/azure/azure-resource-manager/bicep/overview) and [Terraform](/azure/developer/terraform/overview). Evaluate tools to ensure they simplify the future journey, and the technology provider has a good documentation and service support program.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Missed opportunities for modernizing should be considered as risks, such as tools and processes used in on-premises solutions. When migrating to the cloud, those tools often require hard-to-manage custom scripts and can cause issues. 
>
> To mitigate the risk, explore modern technology options, update on-premises processes.

One of the goals for adopting IaC is consistency. The templates should be flexible enough to deploy across various environments. Utilize parameters, variables, and configuration files to modify resource settings for each environment, abstracting only the necessary settings while avoiding over-abstraction of rarely changing settings. Also, avoid the trap of overcomplicating solutions by building or relying on extensive template libraries to prevent maintenance challenges.

Getting a solid foundation of IaC established will open up additional opportunities for deployment and system management optimization in future levels, such as using desired state configuration or GitOps.

#### &#10003; Make security a Day-0 initiative

Prioritize security even at this early stage. Security measures are often based on the strategy of segmentation (roles, resources, networking, and more), which introduces complexities. The team must acknowledge those complexities and make sure security measures are built in at this stage and plan on investing in them over time. This approach avoids deferring security implementations to later stages.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: There's a risk of friction in development, support, and operations processes. Security efforts often face resistance, even though teams start strong with good intentions. 
> 
> To mitigate the risk, add the security work in backlogs to have accountability on the team and make progress trackable alongside development tasks. 

Make tools and processes transparent so that vulnerabilities can be easily detected through audits, and peer reviews. Explore industry-standard tools that support vulnerability scanning and security controls, even if not yet fully implemented. 

//TODO for Security: Your tools and deployment practices use the same identity provider as your production environments to avoid issues with duplicated identities and integration problems across different platforms. Minimize the different identity control planes.


# [**Level 2 - Standardization**](#tab/level2)

![Goal icon](../_images/goal.svg) **Standardize foundational processes, which streamline decision-making responsibilities, and define the requirements for deployment and monitoring the system.**

At Level 2, the team should adopt a more structured approach and focusing development activities on the core functionality of the workload. Establishing consistency early on will help minimize operational burdens in later stages.

#### &#10003; Define team roles and decision-making responsibilities 

Adopt a product mindset. Rather than viewing the workload as integration of tools, technologies, or even job functions, consider them as a cohesive product with focus on the end goal. In Level 2, a more structured approach is recommended where each role is clearly defined and respected.

Often, there's varied expertise within the team. This can be useful in distributing decision-making among various job functions. For example, certain team members might excel at making technical decisions, while others might be experts in defining business outcomes to remain competitive in the ecosystem.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Certain workload teams adopt a consensus-driven culture, where tasks are committed only when everyone is in agreement. While this culture promotes inclusivity, it often stifles initiatives if 100% consensus isn't reached. 

To maintain momentum and clarity in decision-making, 

- Designate a directly responsible individual (DRI) who ensures decision-making is distributed among different members and aligned to their areas of expertise, rather than centralized with one person.

  Document members who are responsible for making decisions. This documentation should be included in onboarding materials for new employees.

- Consider adopting a decision-making methodology that clearly identifies specific roles and responsibilities. Be mindful that such methodologies can create divisions and divert focus from the product direction. Establish checks and balances to prevent siloed decision-making and reduces friction.

#### &#10003; Strive to make improvements, no matter how small

Fostering a continuous improvement mindset means making decisions today, knowing they can be refined tomorrow. 

Procrastinating changes can lead the team to miss out on improvement opportunities in the present. Avoid overthinking and indecision. Striving for a perfect solution might hinder the micro improvements that are still relevant and moving the needle towards a better solution. Focus on making improvements now and always look for ways to do better.

Technical debt is a strategic tool in development to capture these short-term decisions. It can be a forcing function to motivate the team for incremental updates to avoid accumulation. Address technical debt as a regularly recurring task in the backlog.

#### &#10003; Standardize foundational processes

Different classes of workloads have individualistic needs in terms of processes that cater to their specific characteristics. For example, AI workloads involve MLOps and GenAIOps processes that drive the data pipeline to the model. Mission-critical workloads focus on real-time monitoring dashboards that SREs can quickly act on.

Within a class of workload, aim for standardization to boost consistency and minimize operational burden. In the AI workload example, if it includes both discriminative and generative AI models, standardize the processes around DataOps, such as how data is accessed, cleansed, and transformed before it's used to train models or for grounding generative AI models.

Here are some use cases where standardization is recommended:

|Process| Benefit|
|---|---|
|Issue tracking and management.| Facilitates better communication across roles, helps in prioritization, and is needed for historical analysis of past issues.|
|Communication tools and processes, especially to handle incidents.|Minimizes the risk of miscommunication and there's improved coordination among team members to resolve issues faster. |
|Code styles, resource naming conventions, and documentation standards.|Established guidelines enhances code readability and maintenanbility.|
|Testing procedures.| Ensures that all changes go through a selected set of tests, which provides quality assurance.|
|Continuous Integration/Continuous Deployment (CI/CD).|Ensures automated testing, integration, and deployment of code changes, leading to more reliable releases.|

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Continuous improvement and innovation often occur when a team deviates slightly from established standards and discovers a better approach. Those deviations should be encouraged but conducted with structure. For example, encourage innovation days, where the team focuses on pre-selected improvement projects to have fresh ideas and experimentation.

#### &#10003; Prefer buying tools over building custom tools

With standardized processes come the tools necessary to implement them effectively. At this level, use off-the-shelf tools rather than creating custom solutions, which can be reconsidered at later stages for specialized use cases.

Day-to-day tools for workloads include development, testing, monitoring, and deployment tools. Purchased tools streamline workflows and ensure consistency, allowing teams focus on delivering features without the complexity of developing and maintaining custom solutions.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: When considering tools, there's often a tendency to overemphasize the tool's extensibility and future potential rather than its core functionality. At this stage, focus on tools that are practical, address your current problems, and fit your current workflow.


#### &#10003; Adopt automation across the workload

As you develop your new or existing workload, look for opportunities to integrate automation. Designing a new workload to support automation from the start will set you up to easily adopt it as your development continues. Likewise, finding ways to integrate automation into existing ("brownfield") workloads early in the workload's lifecycle will help you gain efficiencies and consistency moving forward. To help in your adoption, use mature, familiar off-the-shelf tools that are compatible with your cloud platform rather than building solutions yourself. Be sure to explore native automation tools provided by your cloud provider to simplify the design. For example, many Azure services support automatic scaling for performance, or failover abilities for disaster recovery. When evaluating third-party tools, consider your team's expertise and any business standards that you must comply with.

Areas that can benefit from automation include:

- Routine operational tasks, like monitoring and alerting, and update management

- Software development lifecycle tasks, like deployments and testing

- Workload performance optimizations, like resource scaling

- Security and governance mechanisms, like scans and policy enforcement

- Backup and recovery activities

- Cost optimizations, like resource deallocations and shutdowns

> :::image type="icon" source="../_images/risk.svg"::: In the early stages of your workload development, be cautious about focusing too much on building or integrating automation as it can distract from getting the workload to production. Take a measured approach to ensure that your workload is manageable while maintaining your development velocity.

> :::image type="icon" source="../_images/trade-off.svg"::: If there are tasks that humans can do infrequently, efficienctly, and safely, they may not be worth automating. For example, you may need to refresh a certificate once every year. This task may not be worth investing development cycles in to automate.

#### &#10003; Extend your infrastructure-as-code (IaC) experience to configure your infrastructure and manage your applications

In Level 1, the focus is on adopting IaC tools to deploy infrastructure and pipelines for application code. At Level 2, extend that practice to include configuration and management of that deployed infrastructure and applications.

Use a desired state configuration approach to bootstrap your resources and avoid configuration drift on them. Different tasks and platforms require different automation tools. For example, Ansible is suitable for managing desired state configuration for VMs, while a GitOps solution, such as Flux, is for Kubernetes clusters.

Determine the right level of automation for your post-deployment tasks to minimize your operational burden while keeping the design simple. Tasks like installing certificates, OS configurations, and database seeding are all good candidates for automating through a IaC mindset. You can also consider extending your automation to include deploying and configuring your app on freshly deployed VMs or container hosts.

> :::image type="icon" source="../_images/risk.svg"::: Avoid unnecessary tool sprawl. Having different developers or development teams using different approaches and technology can lead to a fractured tooling ecosystem. Standardize on a select number of tools for your workload that meet your requirements and ensure that your workload team is trained on those tools. Likewise, be selective about adopting organizational standards for tooling. If your organization suggests tools that adds untenable risk to your workload, evaluate alternative tools that are a better fit.

#### &#10003; Define your workload's deployment strategy

Deployment strategy is a critical component of Operational Excellence. A well-designed deployment strategy ensures that services remain available to users by reducing or eliminating downtime during updates or changes. Gain consensus from stakeholders on how and when changes are deployed to production.

Start with these points:

- What is the tolerated downtime? Determine if the workload can support downtime deployments without causing significant issues or financial loss. Clearly specify if zero downtime is a requirement for routine deployments.

- How often should changes be deployed? Based on feature development, agree on the deployment frequency, whether it's a specific schedule such as daily, weekly, or quarterly or based on other factor. Prefer smaller deployments, occurring more frequently if supported by your scenario.

- How should emergency deployments be handled? Have a plan for executing procedures that manage emergency deployments, such as security hot fixes, ensuring team members know their responsibilities.

Design a repeatable deployment system that can be automated to minimize errors and ensure consistency. Include provisions for rollback to restore the system to a functional state if errors occur in the last deployment.

#### &#10003; Design the workload monitoring stack

Designing a monitoring stack is about making deliberate choices about what to monitor and understanding the significance of those metrics and who will use them. Your appraoch to monitoring should be from the perspective of workload health. Determine with stakeholders what healthy and unhealthy means for your workload and build your platform around metrics that can accurately determine whether the workload is healthy or unhealthy.

> :::image type="icon" source="../_images/risk.svg"::: Avoid gathering too much data. While it might be tempting to enable full logging for all of your resources, it will quickly become a detriment. You will have too much noise to efficiently parse and your costs will increase rapidly as your storage requirements grow. Initially, narrow your collection strategy to basic metrics like CPU and memory utilization and tracking your storage usage. Evaluate which additional application health metrics are useful and slowly integrate them over time.

When setting thresholds for metrics, you need to understand what levels should drive action from response DRIs. For example, while high CPU utilization might generally indicate a problem in one workload, it could be normal for a workload that efficiently uses available resources.

Alerting is a core component of monitoring. Simply turning on standard monitoring metrics and routing all alerts to a support center can lead to panic and false alarms, leading to distrust in the monitoring platform. For that reason, alerts should be meaningful and actionable. Alerts should notify appropriate teams that a specific resource or component is experiencing a specific type of issue, and that investigation or corrective action should begin.

To account for gradual issues that may not be surfaced by your monitoring and alerting, like a slowly increasing database query time, develop a strategy to track and analyze historical trends.

Use visualization tools supported by your monitoring platform to give easy access to workload health signals to different stakeholders. Some stakeholders may only be interested in particular statistics, like application availability, while operations teams should see all health signals at once, so setting up different dashboards can help get different teams the information they need.

> :::image type="icon" source="../_images/risk.svg"::: Your monitoring pipeline can be easily seen as a way to gather business metrics, such as for charge backs, transaction SLAs, capacity assurances, financial trend metrics such as sales totals. Do not mix workload health metrics with business metrics. Make collection of business metrics an application feature, not a monitoring configuration. Monitoring data streams are subject to sampling, monitoring data stores are not typically recoverable in a disaster. Treat business critical data as workload data, not comingled with workload health signals used to evaluate your implementation or activate operations team.



# [Level 3](#tab/level3)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Automation

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

# [Level 4](#tab/level4)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: 

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

# [Level 5](#tab/level5)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: 

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

---

### Next steps
<!-- Provide at least one next step and no more than three. Include some 
context so the customer can determine why they would click the link.
-->
