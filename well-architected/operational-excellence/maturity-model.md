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

# [**Level 1: DevOps foundation**](#tab/level1)

![Goal icon](../_images/goal.svg) **Emphasize teamwork and unity in problem-solving to establish a strong foundation that creates consistent and stable operations in later stages.**

Establish a DevOps mindset at Level 1 to ensure the success of future strategies. Implement well-established DevOps methodologies to enhance process efficiency. Focus on building essential and common vocabulary, processes, and tools for stable operations.

#### &#10003; Encourage collaboration and foster a blameless culture 

Align team efforts with business needs while fostering a collaborative culture.

Workload operations are often managed by members from centralized teams, full-time staff dedicated to workload functionality, partners, or vendors. These individuals should function as a collective force, with mutual respect and acknowledgment for each other's expertise. If teams operate as independent parts, complexities and friction can occur. Independent teams undermine the goal of functioning as a single, efficient system that drives business outcomes.

To reduce an isolated sense of ownership, advocate for a unified approach to problem-solving. All efforts should cater to what the business needs. View both successes and failures as shared outcomes.

#### &#10003; Adopt standard collaboration methodologies and tools

Begin with industry-proven tools and software development lifecycle (SDLC) processes that suit your workload and enhance development efficiency. Don't diverge from proven methods and avoid custom methodologies because they often introduce higher friction.

Popular choices include Agile, Scrum, and Kanban boards. Most experienced developers, DevOps engineers, and product owners have familiarity with these tools, which minimizes the learning curve for new hires.

Initially, use established industry standards to incorporate standardization. Optimize processes later. Ensure that the tools that you select can grow with your needs, without requiring a switch to cutting-edge solutions prematurely. 

#### &#10003; Use infrastructure as code (IaC) as your primary deployment approach

Use a declarative approach as the standard for deployments to ensure consistency, repeatability, and long-term benefits like automation, self-documentation, and change history.

Prefer IaC deployments over portal deployments to avoid risks from inconsistent configurations and lack of testing. Avoid compiled languages or proprietary formats that are restricted to specific programs.

Start with a good foundation by using tools that Azure natively supports, like [Bicep](/azure/azure-resource-manager/bicep/overview) and [Terraform](/azure/developer/terraform/overview). Evaluate tools to ensure that they simplify your future journey. Ensure that the technology provider has good documentation and a reliable service support program.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Consider missed modernization opportunities as risks. For example, you should modernize tools and processes that you use in on-premises solutions. When you migrate to the cloud, these tools often require hard-to-manage custom scripts and can cause problems if you don't modernize them.
>
> To mitigate this risk, explore modern technology options and update on-premises processes.

One of the goals for adopting IaC is consistency. Make templates flexible enough to deploy across various environments. Use parameters, variables, and configuration files to modify resource settings for each environment. Abstract only the necessary settings, and avoid over-abstraction of settings that rarely change. Also, avoid overcomplicating solutions by relying on extensive template libraries. This practice can lead to maintenance challenges.

Establish a solid IaC foundation to create more opportunities for deployment and system management optimization in future levels. For example, you can add desired state configuration or GitOps.

#### &#10003; Prioritize security from the start

Prioritize security even at this early stage. Security measures are often based on segmentation, such as roles, resources, and networking, which introduces complexities. The team must acknowledge these complexities, build security measures early on, and plan on investing in security over time. This approach avoids deferring security implementations to later stages.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Development, support, and operations processes can create friction. Security efforts often face resistance, even though teams start strong with good intentions.
> 
> To mitigate the risk, add security tasks to backlogs. This practice ensures accountability within the team and makes progress trackable alongside development tasks.

Make tools and processes transparent to easily detect vulnerabilities through audits and peer reviews. Explore industry-standard tools that support vulnerability scanning and security controls, even if you don't fully implement them yet. 

Ensure that your tools and deployment practices use the same identity provider as your production environments to minimize different identity control planes.

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

Designing a monitoring system involves choosing what to monitor and understanding the importance of those metrics who will use them.

Start by collecting logs and metrics from all components in the workload. Take advantage of platform-provided monitoring tools, which are integrated with the services and bring you insights into with little configuration. Securely store this data in a dependable storage solution that can be queried for analysis.

> :::image type="icon" source="../_images/risk.svg"::: Avoid collecting excessive data, as it can create noise and increase costs. Start with basic metrics like CPU, memory utilization, and storage usage. Gradually add useful application health metrics over time.

Based on the initial analysis, work with the stakeholders to define what healthy and unhealthy states mean for the workload. You'll use this information in the later stage to develop a health model that accurately reflect that health status.

> :::image type="icon" source="../_images/risk.svg"::: Your monitoring pipeline can be seen as a way to gather business metrics, such as chargebacks, transaction SLAs, capacity assurances, and sales totals. Keep workload health metrics separate from business metrics.
>
> Collect business metrics as an application feature rather than through monitoring configurations. Monitoring data streams can be sampled and are not usually recoverable in a disaster. Treat business-critical data as workload data and keep it separate from workload health signals.

# [**Level 3 - Go-live proficiency**](#tab/level3)

![Goal icon](../_images/goal.svg) **Reduce the risk of deployment errors that could lead to downtime. If down time does occur, SREs are ready to focus on critical issues without wasting time on gathering metrics for analysis.**

In the earlier levels, you've standardized the software development life cycle (SDLC) and made key decisions about deployment methods, testing, and telemetry collection.

Now, at Level 3, the operations should mature to boost confidence in deployment. Testing becomes a go-live requirement to ensure safe and stable deployments. The deployment processes also evolve, embracing automated pipelines to build and deploy workloads to production. To minimize the blast radius of risks, proper segmentation is maintained between foundational resources and application code.

Monitoring practices also mature. The monitoring system is extended to implement a health model that turns operational knowledge into actionable insights. Alerts are streamlined to fit the business context, so irrelevant alerts don't cause false alarms and lead to distrust in the monitoring system.

#### &#10003; Use separate environments for promoting releases

At this level, it's important to establish _release promotion_ as a formal change control protocol before going live. This process involves moving proposed changes through various stages with quality gates. Each stage involves thorough testing, and changes only move forward if they pass these checks and are approved. 

Create distinct environments for different stages like Dev/Test, QA, UAT, and Production. This ensures thorough validation before moving to the next stage. The idea is to manage risks in the lower environments because errors should be caught early to minimize the blast radius.

#### &#10003; Perform sufficient testing

Testing is a non-negotiable strategy at this level to reduce risks associated with changes. Every change is a potential risk and should be tested accordingly. Decide on a strategy where testing is prioritized for the critical parts, which are likely to get affected by the change.

- **Define test cases**. Create test cases for application code, infrastructure templates, and configuration.

- **Conduct various types of tests**. Perform different types of tests in each environment. Start testing high-risk changes in low-risk environments. Gradually move high-risk changes to higher-risk environments as confidence grows.

- **Set up separate testing environments**. Create distinct environments for different testing activities. For example, if you're doing UI testing, create a dedicated environment separate from development and production.

Ideally, keep development and test environments as close to the production environment. Make sure test data is consistent with the data used in production, even if it's sample data. Also, the resources should be similar. For instance, it might be okay to have small test databases with only a few megabytes of data, but it's also important to test your feature with a near-production-size database containing several terabytes to see how it behaves under realistic conditions.  

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: While it's important to keep these environments close, there are trade-offs to consider. Deploying the full scale of the production environment in development isn't feasible. 
>
> Find balance between being close to the production environment and having a cost-efficient non-production environment. For example, consider creating ephemeral Dev/Test environments, which are torn down after a test pass. 
>
> Size the environments based on the test case; unit testing might only need the same versions of libraries and OS on a smaller environment, while resiliency testing might require a pre-production environment with the same service SKUs. Performance testing could even need a production-scale test deployment.

#### &#10003; Automate testing and other quality checks, as much as practical

Automation is great for ensuring consistency and quickly spotting unexpected configuration changes. Figure out which changes can be checked through automated testing. For example, security scans are perfect for automation when moving to a developer environment. Keep in mind that not all tests need to be automated, and some cannot be automated. For instance, user acceptance testing (UAT) requires comparing the implementation with users' expectations and getting their approval.

  > [!NOTE]
  > Even in a highly automated environment, decision-makers are needed. The extent of human involvement may differ, but someone is always accountable for transition to the next environment, by defining the rules for automated checks or doing manual reviews. In later environments where risk tolerance is low, manual checks may be needed.

A variety of testing tools are available to automate and streamline different types of testing, such as Azure Load Testing and Azure Pipelines for orchestrating automated testing.

#### &#10003; Set up approval processes

As your deployment moves through different environments, it's important to use testing and approval processes to validate the changes. This validation should become more mature and rigorous over time. For example, when moving from a developer workstation to a shared developer environment, basic security scans and peer reviews are usually enough. But later, you might need to involve business stakeholders.

- **Approval for regular releases**. When it comes to regular releases, moving from staging to production requires a different set of criteria. Decisions about releases, like moving to production, need approval from stakeholders or clear documentation, or both. The workload team should define who is part of the approval process and their responsibilities. In some regulatory cases, auditors might also be involved in the decision-making process. These roles and responsibilities should have been established in Level 2.

- **Have a separate process for hotfixes**. For critical situations like deploying security patches, you may need an ad hoc deployment process. Create an emergency process to speed up these high-priority fixes. This process could involve approval from only key stakeholders and technical members. Alternatively, develop a pipeline that bypasses some approvals for quicker deployment.

  Balance skipped steps with the risk to the business or customers. High investment and low risk tests can be skipped in emergencies, while high risk and low investment tests should always be run. The designated responsible individual (DRI) should make the final decisions with input from key stakeholders and technical decision makers.

#### &#10003; Implement automated deployments

Have separate deployment cycles for different layers based on their expected rate of change. In some cases, it may be necessary to combine these cycles, depending on their interdependencies and downtime requirements. However, in most cases, strive for granularity by controlling each layer independently with least privilege, and that changes in one layer don't impact others. 

For example, networking infrastructure changes should be less frequent than application code changes. Those changes should be managed separately through a streamlined process with quality control.

To do so, build deployment pipelines that are aligned with the workload layers. Run tests on Infrastructure as Code (IaC) assets in a controlled environment before deploying them to production.

#### &#10003; Develop a health model

With a basic monitoring system in place, now combine business context with monitoring data to quantify the overall health status of workload components and the overall status. This exercise, known as _health modeling_, involves contextualizing monitoring values from infrastructure and applications with business context. 

- **Set context to system-observed data**. Setting thresholds is an important part of health modeling. Give numeric values context so that they relevant to the workload. For example, while high CPU utilization at 70% to 90% might generally indicate **Unhealthy** status in one workload, but it could be **Healthy** for another workload that efficiently uses available resources.

- **Alert on changes**. Changes in these values indicate shifts in health status and should prompt action from responsible DRIs. Therefore, alerting is another core component of health modeling. Avoid turning on standard metrics and sending all alerts to a support center. Instead, raise alerts based on changes in your health model. The information contained in the alerts must be meaningful and actionable, notifying the right teams about specific issues that need investigation or corrective action.

- **Visualize your health model**. Use visualization tools from your monitoring platform to easily share workload health signals with different stakeholders. Some stakeholders may only care about specific statistics, like application availability, while operations teams need to see all health signals. Setting up different dashboards can help provide each team with the information they need.

Over time, develop a strategy to track and analyze historical workload health trends.

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
