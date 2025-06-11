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

Level 5 is not a checklist—its a long-term investment in people, processes, and platforms.
institutionalizing operational excellence with dedicated, skilled teams who measure their impact through the resilience and agility of the systems they build.


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

#### &#10003; Set up source control processes

Based on the scale of the application, decide how to structure source code. For larger systems, each team should have its own processes for building and deploying components they are responsible for. They should have clearly defined interfaces that allow for component discoverability and sharing with other parts of the system. Select a source control technology and set up processes to ensure team members don't interfere with each other's work.

Similarly, a single deployment pipeline might be more effective for smaller scale applications. This simplifies coordination and might also be better for reliability. However, it can be challenging to update or migrate specific parts of the system. 

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

# [**Level 2: Process standardization**](#tab/level2)

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

Start by collecting logs and metrics from all components in the workload. Take advantage of platform-provided monitoring tools. These tools are integrated with the services and provide functional and operational insights with little configuration. Securely store this data in a dependable storage solution that can be queried for analysis.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Avoid collecting excessive data because it can create noise and increase costs. Start with basic metrics like CPU, memory usage, and storage usage. Add useful application health metrics over time.

Based on the initial analysis, work with the stakeholders to define what both healthy and unhealthy states mean for the workload. You use this information in later stages to develop a health model that accurately reflects that health status.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Your monitoring pipeline serves as a tool for collecting business metrics, including chargebacks, transaction service-level agreements, capacity assurances, and sales totals. Maintain a clear distinction between workload health metrics and business metrics.
>
> Collect business metrics as an application feature instead of through monitoring configurations. Monitoring data streams can be sampled and aren't usually recoverable in a disaster. Treat business-critical data as workload data and keep it separate from workload health signals.

# [**Level 3: Release readiness**](#tab/level3)

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

#### &#10003; Set up your incident management process

When your workload is in production, it's normal to deal with incidents like platform outages, data corruption, and more. The key is to handle these incidents effectively within the targets you've set for your workload. So, before going to production, have a structured support plan as the first step in incident management.

- Formalize the support operations by defining who is on call, what their duties are, and how handoffs between on-call personnel are managed. Ideally, there shouldn't be gaps in on-call rotation, and everyone knows who is responsible for handling incidents at any given time. 

- For each type of incident, make sure you have a well-defined playbook or response process. For example, you can use the results from your failure mode analysis (FMA) or security baseline.

- Use the health model to run basic tests on incident management readiness, where the operator monitors the impact of a simulated issue while a developer on call may need to troubleshoot problems.

# [**Level 4: Change management**](#tab/level4)

![Goal icon](../_images/goal.svg) **Ensure the system meets the quality standards promised to its users and prevent violation of service-level agreements (SLAs).**

Until now, the workload team focused on building features and getting the system into production. Here, the focus shifts from building to maintaining and improving a live system. Now that real users rely on it, the priority is change management through efficient Day-2 operations like triage, maintenance, upgrades, and troubleshooting. 

The main strategy is to use real-world experience to drive improvements in those operations. Also, testing becomes a non-negotiable practice. It must be built into every part of development; from fixing bugs to adding features and even refining incident response. Without it, serious issues go undetected until they are already in production.

At this level, technical debt is a real concern. Sometimes teams go live with less-than-ideal implementations, which can complicate maintenance. Teams should analyze the complexity of maintaining these implementations and focus on reducing it.


#### &#10003; Use safe deployment practices

Post production, there will be mainly three categories of changes: routine updates, new feature updates, emergency updates. Use safe deployment practices to keep the system stable during changes. Regardless of the type of change, treat every change as a potential point of failure for the workload's users.

Integrate these strategies in your change control process:

- **Validate continuously and comprehensively**. Test early and often throughout the development lifecycle and as the change moves through various environments. Ideally, every time an artifact changes, create tests focused on those changes. Then, run the entire test suite to validate flows end-to-end. Test results serve as validation data, but business stakeholders should still approve changes.

  > :::image type="icon" source="../_images/trade-off.svg"::: Tradeoff: Running the entire test suite builds confidence in deployments but may not be practical for all changes due to time and cost. Balance thorough testing with cost considerations. Tailor the approval process based on the impact of changes. Minor changes should have a leaner process, while significant changes, like new features, require thorough review.

  This level is appropriate for adopting advanced operational concepts, such as regional failovers. The goal is to fully automate these processes, focusing on self-healing for most scenarios. These processes must also be tested extensively.

- **Implement versioning for your APIs**. Handle breaking changes to your data model carefully to make sure changes are backward compatible. A versioning strategy for your API will make sure existing systems continue to run smoothly after changes are rolled out. Retrospective versioning can be difficult, so start early.

- **Roll out incremental updates**.  By Level 3, you've already standardized deployment processes using automated pipelines for all environments. At Level 4 maturity, the workload is newly in production and focus should be on mastering incremental updates to their workload, including release cycles.

  Deploy small, frequent updates to make validation easier for a small footprint of changes. Fully automate validation tasks like load testing, deploying to test environments, A/B testing. 

  > [!NOTE]
  >  Safe deployment patterns, like Canary and Blue-Green deployments, offer flexibility and reliability through side-by-side deployments. For example, in Blue-Green deployments, a new environment is built, traffic is shifted, and the old environment is decommissioned.  Other deployment techniques are feature flags, dark launches, which allow testing in production before rolling out changes to all users. This capability is available with certain Azure services such as Azure App Service, where updates can be rolled out by (gradually) swapping between deployment slots.

- **Recover from deployment errors**. Expect updates to fail. With incremental updates, troubleshooting becomes faster if faced with an issue. In such situations, stop the system to prevent further damage and push changes to remediate the issue. Restoring from backups is acceptable if it doesn't disrupt continuity. The goal is to move forward to a stable version rather than strictly opting for rollback procedures.

#### &#10003; Optimize build operations

Level 3 recommends having separate deployment cycles for different layers of the architecture, based on their rate of change. At the very least keep infrastructure and code pipelines.

Now that the workload has been in production, revisit that layering approach. If possible, further decouple architectural components to allow more flexibility in their release cadence. This minimizes delays and failures on individual components. Also, run tests and long processes as parallel jobs to save time and boost developer productivity.

#### &#10003; Validate incident response processes

In Level 3, you established an on-call support system with playbooks to define responses for incidents. However, having a playbook is just the first step; now that the workload is in production, you must validate and improve the effectiveness of your incident management process and also develop a mature communication plan. Here are some considerations: 

- **Test responses to incidents**. Include responses from technology, people, and processes. We recommend that you run game days to introduce realism in your validation process. These are planned events where faults are introduced to test the team's ability to detect and fix issues. It ensures they have the right tools, resources, and procedures in place. Chaos engineering can be a valuable tool in this process. It allows you to introduce controlled disruptions and observe the outcomes. Alternatively, manual methods like disabling backends on a global load balancer or performing a database failover can also be used to test the response.

- **Develop a communication plan**. Clearly define communication responsibilities for the workload team, support teams, and emergency response personnel. Standardizing cadence and format for sending internal status updates to business stakeholders fosters an environment of transparency and trust. In certain cases, like security breaches, responsible disclosure to end users is required. Clarify the appropriate type and level of information to share in these external communications.

- **Conduct postmortems**. Treat every incident as an opportunity to learn from production. This process should be used to identify weaknesses in deployment and development processes and to commit to making improvements in the system.


#### &#10003; Optimize operations using monitoring data from production

By Level 4, you should have advanced monitoring to emit, correlate, and analyze metrics with a business context. Now, improve its fidelity by learning from production. Use monitoring data to refine processes that were built on best guesses. Here are some prime examples:

- The primary focus in Level 3 was developing a health model for the workload, At this level, set realistic objectives and service level indicators (SLIs) and fine tuning the alerting system. 

- As part of Day-2 operations, efforts to minimize configuration drift should be prioritized. Without this, the runtime might deviate significantly over time. To start, establish a snapshot of the known-good configuration state. Next, use observability metrics from production to compare current behavior to that snapshot, ensuring the system remains aligned with its intended state.

- This level is appropriate for introducing feedback loops. This process can help you understand how the system behaves under certain stressors and predict the impact of new features. System telemetry guides these feedback loops, providing essential insights for forecasting future changes to the workload and designing solutions to address potential problems in advance. This data can also be used to help you prioritize technical debt.

As a general practice, fine-tune the monitoring stack based on observability data and patterns seen in production. For instance:

- Adjust logging levels to balance visibility and noise to capture activities on the critical paths.

- Amplify the important alerts ones while suppressing the irrelevant ones.


#### &#10003; Automate maintenance

At Level 3, automation efforts were primarily focused on deploying to production. By this level, teams have significantly reduced manual effort by automating build, test, and  deployment processes to production through continuous integration/continuous deployment (CI/CD) pipelines. In addition to quality gates, certain approvals might also be conducted through automated workflows.

At Level 4, operational automation should be informed by real-world production experience and addressing technical debt. 

Here are some Day-2 automation examples:

|Process | Benefit|
|---|---|
|Automate rotation of certificates, API keys, and other secrets.| Automation guarantees timely rotations, eliminating the need for manual intervention, which saves time and reduces the likelihood of human error.|
|Automate routine maintenance of infrastructure. |Routine infrastructure maintenance requires extensive testing and coordination. Automation can expedite these tasks, reducing manual effort and minimizing risks.|
|Automate emergency response process.|Without proper automation, people might resort to hasty, uncoordinated actions during an emergency release, potentially leading to further issues.|
|Automate scaling of resources on load spikes and downs.| Autoscaling ensures that resources are allocated dynamically based on demand.  This leads to more efficient use of resources because when demand decreases, resources are de-allocated, without excessive operational overhead. |
|Automate data retrieval and delivery.|Reduces the time and effort required to fulfill data requests sent by users. Instead of manually accessing databases, scripts are trigged for accessing the database, retrieving relevant data, and sending it to user.|
|Automate the creation of developer environments given a specific criteria.|This approach ensures that environments are consistently created to facilitate safe changes in the workload, as part of the team's Day-2 operations.|

> [!NOTE]
> When setting a strategy for deployment automation, begin with known and predictable tasks, accounting for common failure points. After those are automated, extend automation to address unforeseen issues, some of which may require manual intervention. For example, start by automating routine tasks like infrastructure updates, because they are more manageable. Then tackle emergency hot fixes as they may involve unknown failure scenarios.
>
> For example, the team routinely deploys the workload using controlled exposure to all users across all geographies, which might take days to be fully deployed. They also want to deploy hot fixes sooner by skipping certain steps. The automation process should account for those expedited deployments.

The primary goal is to identify repetitive, human-driven tasks that might have been overlooked in earlier stages due to deadlines. However, the aim is not to automate everything. Automation should be guided by return on investment. Prefer using existing technologies and knowledge rather than starting with new tools. If lightweight tooling is necessary, consider their lifecycle and maintenance. 

#### &#10003; Bring efficiency by aligning tools and processes with organizational standards

At Level 4 maturity, focus on gaining operational efficiencies by evaluating engineering assets and processes. Identify which assets are necessary but not core to your business.

For those, look for ways to:

- Use shared tools already available in your organization.
- Consider third-party software for specific tasks, like data conversion.

The main benefit is that pre-built assets come with support channels, and can replace custom solutions you developed, reducing the team's operational burden of team-created solutions. Assess how well these resources can meet your needs and address any remaining gaps.

Explore these areas of the workload:

- **Evaluate your custom code**.  Instead of writing custom code for tasks like parsing, evaluate existing open source solutions that are industry standards. Using these solutions can reduce the need for code maintenance and result in a smaller code base. Explore options within your organization. There might be existing libraries that can be integrated into your workload to handle basic tasks, such as authentication. 

- **Evaluate your tool chain**. Assess areas where you can rely on other teams using similar tools. Adjust your use of libraries, templates, and modules accordingly. Additionally, align Infrastructure as Code (IaC) tools across the organization to streamline operations.

- **Evaluate your processes**. Identify centralized processes that can take on tasks you may have implemented yourself, such as security scanning. Instead of managing your own quarantine process for NuGet packages, leverage the organization's existing security team's process by informing them of the modules used in your workload.

  Supportability is another key area. Early on, development teams often handle support themselves, monitoring metrics and fixing live issues. At this stage, consider setting up dedicated roles like on-call engineers. If your organization has a shared support team, use it to reduce the support load on developers. 

  > [!NOTE]
  > If possible, transition day-to-day support to external vendors. The vendors don't have deep context like the development or the architects involved in getting the workload to production. Before handing off to a vendor, make sure the the system is stable in production and management tasks for the vendors are clearly defined. For the vendor team to be successful, they should have key elements in place, defined thresholds of the health model that represent Health, Unhealthy, and Degraded states. They should be also be trained in playbooks, tools, and other troubleshooting information. In case they are unable to identify causes, there should well-defined pathways for escalating and routing problems to the workload team.  

#### &#10003; Manage technical debt at a regular cadence

Technical debt is the result of shortcuts taken during development to meet deadlines, leading to less-than-ideal implementations. Teams should work on reducing this debt by analyzing maintenance complexity and time. If not addressed, systems can become more complex and harder to maintain or scale, slowing down innovation as developers spend more time fixing issues instead of working on new features.

Here are some tactical recommendations for handling technical debt:

- Track technical debt alongside feature work.

- Reserve capacity in every sprint for addressing technical debt, separate from feature development. Occasionally, dedicate entire sprints to addressing technical debt.

- For new features, if you plan on incurring new technical debt, add its proposed resolution to the backlog right away.

Technical debt is a normal part of development and an opportunity for improvement. As new features are added, debt will accumulate. Balance the effort of paying off old debt with the new debt from developing new features.

# [**Level 5: Future adaptability**](#tab/level5)

![Goal icon](../_images/goal.svg) **Keep improving, keep adapting without ever assuming you're done.**

Your journey through Levels 1 to 4 established a well-architected workload on Azure. Your design helped set up operational practices like monitoring, testing, deployment, and automation, moving towards production readiness. Once in production, the focus shifted to stabilizing operations and safely managing changes to protect user experience.

In the last stage, maturity means operating the workload at scale, keeping it running and up to date. It's also about being proficient at identifying areas where the current design has reached its limit and preparing for changes in business requirements. Acknowledge that assumptions about constraints can become outdated as the ecosystem evolves. Staying static can lead to regression because the environment, practices, and tools change. Without investing in level 5 approaches, you might find your workload falling behind.

Level 5 isn't the end or a technical checkpoint; it's a mindset shift to modernize people, processes, tools, and technology. Operations are treated with the same rigor, investment, and innovation as the core application.

#### &#10003; Spot rearchitecture opportunities based on observed growth and future potential

Workload architectures are designed with intentional constraints and have limited lifespans.  Up to Level 4, your architecture effectively meets business requirements. Now, evaluate how the system handles new usage patterns, technologies, growth, and operational challenges.

For example, what works for thousands of users may fail at tens of thousands. Increased data volume can cause atomicity issues, and higher demands for performance, and compliance can push your architecture to its limits. That pressure can prevent new features and creating scaling bottlenecks.

This is the time to recognize tipping points and *identify areas where you might need to rearchitect*. Here are some common areas:

- Your *initial choice of tools, frameworks, and platform services* may have suited your business needs. However, as your system evolves, these tools might become rigid or tightly coupled, libraries may lack extensiblity, and platform services might reach end-of-life, breaking existing dependencies. 

  Explore tools and services in the wider ecosystem, with strong support and community adoption. Choose a modular, loosely coupled architecture for easier replacement or upgrades.

- In the early stages, the workload team may *manage the entire technical stack*. While this works initially, it can lead to increased pressure and operational overhead as the system grows. To reduce this burden, consider offloading responsibilities to specialized teams, like those focused on networking, security, or observability. Their expertise allows the core workload team to focus on delivering product value.

- Managing *customer-dedicated (single-tenant) instances* can introduce cost and operational overhead. Consider that to be a strong signal that a multi-tenant architecture may be necessary. This requires broader architectural and operational investment. For example, handling tenant onboarding, data access isolation, and so on.

- Rethink your deployment strategy to scale predictably and handle failure isolation and performance at scale. Explore proven practices like the Deployment Stamps pattern.

  - **Scale unit**. A scale unit is a logical group of resources that can be scaled together to handle increased load while being monitored independently. These units are automatically deployed as repeatable blocks based on threshold criteria and can be replicated as needed. 
  
    However, this approach can lead to over-scaling certain services, resulting in added costs.

    For more information, see [Scale-unit architecture](../mission-critical/mission-critical-application-design.md#scale-unit-architecture)

  - **Immutable environments** Instead of updating a deployment stamp in place, tear down the old stamp and redeploy it with changes. This requires side-by-side deployment models like Blue-Green. To adopt this practice, pipelines should be ready to automatically deploy a stamp to handle hyperscaling or replacing unhealthy units. 

    Your Level 4 safe deployment practices should prepare you for this. There shouldn't be any regression, and users should transition to the new stamp smoothly. 

#### &#10003; Use automation to further reduce friction

Transitioning from Level 4 to Level 5 isn't just about increasing automation; it's about using automation to reduce friction and deciding when these tasks should be executed. 

Here are some examples:

- **Automated creation of local developer environments**. Provide a standardized experience where each developer workstation is configured with the same set of tools, library versions, and other developer-centric assets. That way all developers, experienced or new, have access to a consistent development environment. It also facilitates better collaboration, knowledge sharing, and onboarding. Use virtualized development environments to make this process easier.

- **Automated alert triage and resolution workflows**. Use automation to categorize and prioritize alerts based on predefined rules, and trigger resolution workflows. For high-priority alerts, the system can notify the relevant team and start a resolution process, speeding up response times.

- **Automated incident remediation**. Implement self-healing scrpts to automatically restart services or shift workloads when issues are detected. For example, if a web server crashes, a script can restart it or redirect traffic to a backup server, ensuring minimal downtime.

- **Automated resource utilization**. As maturity in other pillars progress, you'll likely need to introduce automation to address their goals. For example, you can schedule non-critical resources and environments to be unavailable during off-peak hours to save costs and optimize resource usage.

- **Automated tenant management**. Streamline the onboarding and offboarding of tenants in multi-tenant environments. When a new tenant signs up, automation can create user accounts, provision resources, and configure settings.


#### &#10003; Create developer environments per feature or change

Day-2 operations focus on implementing safe changes. Each change goes through different environments set up for specific purposes like development, testing, security, and others. By following Level 4 guidances, it's assumed that you've automated the creation of those environments.

At Level 5, consider moving away from having many long-standing environments for different purposes and start creating separate, automated, temporary environments for things like feature development or testing. Each environment should follow the lifecycle of the associated code branch. When a feature branch is created, automatically set up an environment with sample data, load tests, and cloud resources to simulate production. After the work is complete or changes are made, discard that environment. This approach prevents the parallel development of other features from interfering with the current feature, and can help reduce cost. 

#### &#10003; Share knowledge, contribute to organizational maturity

Unlike Levels 1 to 4, which focus on looking internally at production readiness and managing workload change, Level 5 is an opportunity for workload team to share their designs, successes, failures with other workloads across the organization. Workload teams do not need to experience failure themselves in order to learn how to avoid it if they can pre-emptively learn it from others who have gone before them.

This involves transitioning from Operational Excellence principles at the individual workload level to the organization investing in centralized operations. These centralized teams consist of dedicated engineering groups that build deployment tools with built-in guardrails, automation, observability, and testing.

Your workload team to share techniques, tools, and insights that have worked for you to peer workload teams. Start with teams that your workload takes dependencies on, then move to teams that take dependencies on your workload. Also, learn from those teams as you share experiences and outcomes.

You should see increased knowledge sharing through shared platforms, documentation, and community engagement.


#### &#10003; Enable self-service capabilities to your workload's various job functions

As you experience repetitive, ad-hoc tasks within the workload's routine development responsibilities; evaluate if those tasks should instead be provided as a scripted solution that the team members can responsibly invoke as needed. Build and maintain self-service capabilities for these ad-hoc tasks to provide agility and autonomy to the team.

Start with high effort or error prone ad-hoc tasks that will be cost effective to deliver to the workload team as a self-service solution. The solution must optimize the time of the team members and build consistency into those tasks, yielding a return on investment over the life of the self-service solution.

Follow the approach and capabilities described in [Start your platform engineering journey](/platform-engineering/journey).


### Next steps

TBD

