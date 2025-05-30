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

Members from centralized teams, full-time staff dedicated to workload functionality, partners, or vendors often manage workload operations. These individuals should function as a collective force, with mutual respect and acknowledgment for each other's expertise. If teams operate as independent parts, complexities and friction can occur. Independent teams undermine the goal of functioning as a single, efficient system that drives business outcomes.

To reduce an isolated sense of ownership, advocate for a unified approach to problem-solving. All efforts should cater to the needs of the business. View both successes and failures as shared outcomes.

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

# [**Level 2: Standardize foundational processes**](#tab/level2)

![Goal icon](../_images/goal.svg) **Standardize foundational processes. This approach streamlines decision-making responsibilities and defines the requirements for system deployment and monitoring.**

At Level 2, the team should adopt a more structured approach and focus development activities on the core functionality of the workload. Establishing consistency early on helps minimize operational burdens in later stages.

#### &#10003; Define team roles and decision-making responsibilities 

Adopt a product mindset. Instead of viewing the workload as integration of tools, technologies, or job functions, treat it as a cohesive product with a clear focus on the end goal. In Level 2, apply a more structured approach where each role is clearly defined and respected.

Expertise within the team often varies. This diversity can be useful in distributing decision-making among various job functions. For example, specific team members might excel at making technical decisions, while other team members might be experts in defining business outcomes to remain competitive in the ecosystem.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Some workload teams adopt a consensus-driven culture, where they commit to tasks only when everyone agrees. This culture promotes inclusivity, but it often stifles initiatives when full consensus isn't achieved.

Ensure a well-structured decision-making process by using the following principles:

- Designate a directly responsible individual to ensure that decision-making is distributed among team members and aligned with their areas of expertise, instead of being centralized with one person.

  Document who the decision-makers are, and include this information in the onboarding materials for new employees.

- Consider adopting a decision-making methodology that clearly defines specific roles and responsibilities. Be mindful that these approaches can create division and shift focus away from product goals. Establish checks and balances to prevent siloed decision-making and reduce friction.

#### &#10003; Strive to make improvements, no matter how small

Fostering a continuous improvement mindset means making decisions today with the understanding that they can be refined tomorrow.

Delaying changes can cause the team to miss present improvement opportunities. Avoid overthinking and indecision. Striving for a perfect solution might hinder small yet meaningful progress. Focus on making improvements now while continuously seeking ways to improve.

Technical debt is a strategic tool in development for capturing short-term decisions. It can serve as a motivator for incremental updates, which prevents unnecessary accumulation. Treat technical debt as a recurring task in the backlog.

#### &#10003; Standardize foundational processes

Different classes of workloads have unique process requirements tailored to their specific characteristics. For example, AI workloads rely on machine learning operations and generative AI operations to drive data pipelines to the model. Mission-critical workloads prioritize real-time monitoring dashboards that site reliability engineers can quickly act on.

Within a workload class, strive for standardization to enhance consistency and reduce operational burden. For AI workloads that include both discriminative and generative models, standardize the processes around data operations. These operations include data access, cleansing, and transformation before it's used to train models or ground generative AI models.

Standardization is recommended for the following use cases:

| Process | Benefit |
| :---| :---|
| Problem tracking and management | Facilitates better communication across roles, helps in prioritization, and is needed for historical analysis of past problems |
| Communication tools and processes, especially to handle incidents | Minimizes the risk of miscommunication and improves coordination among team members to resolve problems faster |
| Code styles, resource naming conventions, and documentation standards | Enhances code readability and maintainability by establishing guidelines |
| Testing procedures | Ensures that all changes go through a selected set of tests, which provides quality assurance |
| Continuous integration and continuous deployment | Ensures automated testing, integration, and deployment of code changes, which results in more reliable releases |

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Continuous improvement and innovation often occur when a team slightly deviates from established standards to explore better approaches. These deviations should be encouraged but structured. For example, hosting innovation days allows the team to focus on pre-selected improvement projects, which fosters fresh ideas and experimentation.

#### &#10003; Buy tools instead of building custom tools

Standardized processes include the necessary tools for effective implementation. At this level, prioritize off-the-shelf solutions instead of custom-built solutions, which you can reconsider later for specialized use cases.

Day-to-day tools for workloads include development, testing, monitoring, and deployment tools. Purchased tools streamline workflows and ensure consistency. This consistency allows teams to focus on delivering features without the complexity of developing and maintaining custom solutions.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** When considering tools, there's often a tendency to overemphasize the tool's extensibility and future potential instead of its core functionality. At this stage, focus on tools that are practical, address your current problems, and fit your current workflow.

#### &#10003; Adopt automation across the workload

As you develop a new or existing workload, seek opportunities to integrate automation. Designing a new workload with automation in mind from the start makes future adoption seamless. Similarly, incorporating automation into existing workloads, or *brownfield* workloads, early in their life cycle helps you gain efficiency and maintain consistency over time.

To streamline adoption, use mature, familiar off-the-shelf tools that are compatible with your cloud platform instead of building solutions from scratch. Explore native automation tools from your cloud provider to simplify the design. For example, many Azure services support autoscaling for performance and failover capabilities for disaster recovery. When you assess non-Microsoft tools, factor in your team's expertise and any relevant business standards.

The following areas can benefit from automation:

- Routine operational tasks, like monitoring and alerting, and update management

- Software development life cycle tasks, like deployments and testing

- Workload performance optimizations, like resource scaling

- Security and governance mechanisms, like scans and policy enforcement

- Backup and recovery activities

- Cost optimizations, like resource deallocations and shutdowns

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** In the early stages of your workload development, be careful about focusing too much on building or integrating automation because it can divert attention from delivering the workload to production. Take a measured approach to ensure that your workload is manageable while maintaining your development velocity.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** If a task can be done infrequently, efficiently, and safely by humans, it might not be worth automating. For example, automating the annual refresh of a certificate might not justify the investment of development cycles.

#### &#10003; Extend your infrastructure-as-code experience to configure your infrastructure and manage your applications

In Level 1, the focus is on adopting infrastructure-as-code (IaC) tools to deploy infrastructure and pipelines for application code. At Level 2, extend that practice to include configuration and management of that deployed infrastructure and applications.

Use a desired state configuration approach to bootstrap your resources and avoid configuration drift. Different tasks and platforms require different automation tools. For example, Ansible is suitable for managing desired state configuration for virtual machines (VMs), while a GitOps solution, such as Flux, is suitable for Kubernetes clusters.

Determine the right level of automation for your post-deployment tasks to minimize your operational burden while keeping the design simple. Tasks like installing certificates, OS configurations, and database seeding are all good options for automating. Also, consider extending your automation to include deploying and configuring your app on newly deployed VMs or container hosts.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Avoid unnecessary tool sprawl. Developers or development teams that use different approaches and technology can result in a fractured tooling ecosystem. Standardize on a select number of tools for your workload that meet your requirements, and ensure that your workload team is trained on those tools. Likewise, be selective about adopting organizational standards for tooling. If your organization suggests tools that add excessive risk to your workload, evaluate alternative tools that are more suitable.

#### &#10003; Define your workload's deployment strategy

A deployment strategy is a critical component of operational excellence. A well-designed deployment strategy ensures that services remain available to users by reducing or eliminating downtime during updates or changes. Gain consensus from stakeholders on how and when changes are deployed to production. Consider the following points:

- **Define tolerated downtime.** Determine if the workload can support downtime without causing significant problems or financial loss. Clearly specify if zero downtime is a requirement for routine deployments.

- **Establish deployment frequency.** Determine deployment frequency based on feature development. Agree on a schedule, whether it's daily, weekly, quarterly, or another suitable approach. When possible, prioritize smaller, more frequent deployments if they align with your scenario.

- **Plan for emergency deployments.** Develop a plan for implementing procedures that manage emergency deployments, such as security hotfixes. This approach ensures that team members understand their responsibilities and can act quickly when needed.

Design a repeatable deployment system that can be automated to minimize errors and ensure consistency. Include provisions for rollback to restore the system to a functional state if errors occur in the last deployment.

#### &#10003; Design the workload monitoring stack

Designing a monitoring system requires you to select what to monitor and understand the importance of those metrics for users.

Start by collecting logs and metrics from all components in the workload. Take advantage of platform-provided monitoring tools. These tools are integrated with the services and provide insights into functional and operational requirements with little configuration. Securely store this data in a dependable storage solution that can be queried for analysis.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Avoid collecting excessive data because it can create noise and increase costs. Start with basic metrics like CPU, memory usage, and storage usage. Add useful application health metrics over time.

Based on the initial analysis, work with the stakeholders to define what both healthy and unhealthy states mean for the workload. You use this information in later stages to develop a health model that accurately reflects that health status.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Your monitoring pipeline serves as a tool for collecting business metrics, including chargebacks, transaction service-level agreements, capacity assurances, and sales totals. Maintain a clear distinction between workload health metrics and business metrics.
>
> Collect business metrics as an application feature instead of through monitoring configurations. Monitoring data streams can be sampled and aren't usually recoverable in a disaster. Treat business-critical data as workload data and keep it separate from workload health signals.

# [**Level 3: Achieve go-live proficiency**](#tab/level3)

![Goal icon](../_images/goal.svg) **Reduce the risk of deployment errors that could result in downtime. If down time does occur, ensure that site reliability engineers can focus on critical problems without wasting time on collecting metrics for analysis.**

In the earlier levels, you standardize the software development life cycle and make key decisions about deployment methods, testing, and telemetry collection.

At Level 3, the operations should mature to boost confidence in deployment. Testing becomes a go-live requirement to ensure safe and stable deployments. The deployment processes also evolve, embracing automated pipelines to build and deploy workloads to production. To minimize the blast radius of risks, proper segmentation is maintained between foundational resources and application code.

Monitoring practices also mature. The monitoring system is extended to implement a health model that turns operational knowledge into actionable insights. Alerts are streamlined to fit the business context, so irrelevant alerts don't cause false alarms and cause distrust in the monitoring system.

#### &#10003; Use separate environments for promoting releases

At this level, it's important to establish *release promotion* as a formal change control protocol before going live. This process progresses proposed changes through various stages with quality gates. Each stage undergoes thorough testing, and changes advance only if they pass these checks and receive approval.

Create distinct environments for different stages like dev/test, quality assurance, user acceptance testing (UAT), and production. This approach ensures thorough validation before moving to the next stage. The idea is to manage risks in the lower environments because errors should be caught early to minimize the blast radius.

#### &#10003; Perform sufficient testing

At this level, testing is a non-negotiable strategy to reduce risks associated with changes. Each change is a potential risk and should be tested accordingly. Decide on a strategy where testing is prioritized for the critical parts that the changes are likely to affect. Use the following steps to ensure a robust testing strategy:

- **Define test cases.** Create test cases for application code, infrastructure templates, and configuration.

- **Conduct various types of tests.** Perform different types of tests in each environment. Start testing high-risk changes in low-risk environments. Gradually move high-risk changes to higher-risk environments as confidence grows.

- **Set up separate testing environments.** Create distinct environments for different testing activities. For example, if you do UI testing, create a dedicated environment separate from both development and production environments.

Ideally, keep development and test environments as similar to the production environment as possible. Ensure that test data closely matches production data, even if it's only a sample. Resources should also be comparable. For example, small test databases that have a few megabytes of data might be sufficient in some cases, but it's essential to validate features by using a near production-size database that contains several terabytes to assess real-world performance.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** It's important to keep these environments close, but there are trade-offs to consider. Deploying the full scale of the production environment in development isn't feasible.
>
> Find balance between proximity to the production environment and having a cost-efficient non-production environment. For example, consider creating ephemeral dev/test environments, which are torn down after a test pass.
>
> Size environments according to the test case. Unit testing might only require matching library and OS versions in a smaller setup. Resiliency testing might need a pre-production environment with identical service SKUs. Performance testing might demand a production-scale deployment.

#### &#10003; Automate testing and other quality checks when possible

Automation is great for ensuring consistency and quickly spotting unexpected configuration changes. Determine which changes can be checked through automated testing. For example, security scans are perfect for automation when you move to a developer environment. Not all tests need to be automated, and some tests can't be automated. For instance, UAT requires comparing the implementation with users' expectations and getting their approval.

  > [!NOTE]
  > Even in a highly automated environment, decision-makers are essential. The level of human involvement might vary, but someone is always responsible for transitioning to the next environment. This process can include defining rules for automated checks or conducting manual reviews. In later environments that have lower risk tolerance, manual checks might be necessary.

Various testing tools are available to automate and streamline different types of testing, such as Azure Load Testing and Azure Pipelines for orchestrating automated testing.

#### &#10003; Set up approval processes

As your deployment moves through different environments, it's important to use testing and approval processes to validate the changes. This validation should mature and become more rigorous over time. For example, when you move from a developer workstation to a shared developer environment, basic security scans and peer reviews are usually sufficient. But later, you might need to include business stakeholders. Consider the following guidelines to ensure a structured and efficient deployment process:

- **Approval for regular releases:** When it comes to regular releases, moving from staging to production requires a different set of criteria. Decisions about releases, like moving to production, need approval from stakeholders, clear documentation, or both. The workload team should define who is part of the approval process and their responsibilities. In some regulatory cases, auditors might also be included in the decision-making process. Establish these roles and responsibilities in Level 2.

- **Have a separate process for hotfixes:** For critical situations like deploying security patches, you might need an improvised deployment process. Create an emergency process to accelerate these high-priority fixes. This process could include approval from only key stakeholders and technical members. Alternatively, you can develop a pipeline that bypasses some approvals for quicker deployment.

  Consider the risk to the business or customers when determining which steps to skip. High-investment and low-risk tests can be skipped in emergencies, while high-risk and low-investment tests should always be run. The directly responsible individual (DRI) should make the final decisions with input from key stakeholders and technical decision-makers.

#### &#10003; Implement automated deployments

Maintain separate deployment cycles for different layers based on their expected rate of change. In some cases, combining cycles might be necessary, depending on interdependencies and downtime requirements. However, in most cases, strive for granularity by independently managing each layer with least privilege. Ensure that changes in one layer don't affect the others.

For example, networking infrastructure changes should be less frequent than application code changes. Manage these changes separately through a streamlined process via quality control. To create a process, build deployment pipelines that are aligned with the workload layers. Run tests on infrastructure-as-code assets in a controlled environment before you deploy them to production.

#### &#10003; Develop a health model

After you have a basic monitoring system in place, combine business context with monitoring data to quantify the overall health status of workload components and the overall status. This exercise, known as *health modeling*, involves contextualizing monitoring values from infrastructure and applications with business context. To build an effective health model, consider the following key principles:

- **Give system-observed data context.** Setting thresholds is an important part of health modeling. To ensure that thresholds are meaningful to your workload, provide numeric values with context. For example, while high CPU usage between 70% and 90% might generally indicate an **unhealthy** status in one workload, it could be **healthy** for another workload that efficiently uses available resources.

- **Alert on changes.** Changes in these values indicate shifts in health status and should prompt action from DRIs. Therefore, alerting is another core component of health modeling. Avoid turning on standard metrics and sending all alerts to a support center. Instead, raise alerts based on changes in your health model. The information contained in the alerts must be meaningful and actionable, notifying the right teams about specific problems that need investigation or corrective action.

- **Visualize your health model.** Use visualization tools from your monitoring platform to easily share workload health signals with different stakeholders. Some stakeholders might require specific statistics, like application availability. And operations teams need access to all health signals. Setting up different dashboards can help provide each team with the information that they need.

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
