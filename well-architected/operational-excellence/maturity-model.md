---
title: Operational Excellence Maturity Model
description: Understand the maturity model levels of the Operational Excellence pillar.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 02/04/2026 
ms.topic: concept-article
ms.update-cycle: 1095-days  
---

# Operational Excellence maturity model

The journey of Operational Excellence is one of continuous improvement, where each stage builds on the last to drive greater efficiency and effectiveness across workload design, implementation, and support.

At its core, it's about streamlining key practices like deployment, monitoring, testing, and automation. The journey begins with a strong foundation: a shared vocabulary, standardized practices, and a DevOps mindset that encourages collaboration and stability. From there, standardization introduces consistency and predictability into processes. As teams grow more proficient, individual tasks evolve into integrated workflows, supported by production-ready capabilities such as automated testing, intelligent monitoring, and continuous integration.

When systems go live in production, operations become even more advanced. Teams are equipped to manage change quickly and reliably, meeting quality benchmarks and implementing feature requests from product owners with confidence.

The most mature stage is all about optimization and innovation. Here, teams operate at scale, continuously adapting systems in real time to meet evolving business needs and technological shifts. However, this isn't a fixed destination; it's a dynamic mindset of always improving, always adapting.

The model is structured into five distinct maturity levels, each with a primary goal and a set of core strategies. For meaningful productivity gains, start evaluating where AI can be embedded into your operations from the very beginning. Use the tabbed views below to explore each level. Be sure to also review the highlighted tradeoffs and associated risks as you progress.

# [:::image type="icon" source="../_images/ai.svg"::: **AI opportunities**](#tab/ai-opportunities)

![Goal icon](../_images/goal.svg) **Modernize operations by deliberately embedding AI-driven tools that reduce manual, error-prone toil and deliver measurable gains while pragmatically balancing cost, risk, and time to value.**

Evaluate your operational paths end to end to identify where AI can be deliberately embedded to reduce manual toil, improve consistency, and unlock measurable productivity gains without compromising risk, cost, or time to value.

#### Buy: Off-the-shelf GenAI solutions

Off-the-shelf GenAI tools have built-in AI capabilities. They can be broadly categorized by intent. One is generic, interactive assistance tools like GitHub Copilot, which are context-dependent and can be used for a variety of tasks. These tools require little to no setup and are commonly used for knowledge work through prompt-driven chat. The other category is purpose-built tools and agents such as deployment agents, SRE agents, which are designed for specific functions. They can be integrated for developer productivity via IDE and CLI assistants. 

There are also Azure services that have integrated AI features, which can come with additional costs.

#### Build: GenAI with custom implementation

Custom GenAI embeds AI directly into operational and development workflows tailored to a specific workload. Custom agents can pull context from tickets, code repositories, metrics, and monitoring systems to produce insights that reflect the current state of operations and act within defined boundaries.

More advanced implementations can generate and validate code or infrastructure against internal standards, route work based on expertise or availability, and apply custom ML models for specialized predictions. This approach enables deeper automation and tighter alignment with organizational processes, but it requires ongoing investment in engineering, data quality, governance, security, and maintenance.


#### **AI capabilities**
The following are some of the most common and approachable AI capabilities used in practice, but this list is not exhaustive. Use this as inspiration to evaluate where in your operations you can inject AI for productivity gains. 

> [!NOTE] 
>
> Adoption should follow a deliberate progression: begin with focused use cases such as summarization or content generation, then, as team competency matures, expose system functions as AI-enabled tools and introduce agentic interfaces for task-based reasoning, ultimately evolving toward multi-agent systems where specialized agents collaborate across integrated data and systems to tackle complex analysis and workflows.

> [!div class="checklist"]
>
> - [**Summarization**](#-summarization-agents). AI tools that read and condense information from documents, reports, logs, or conversations, producing concise summaries, key points, or actionable insights.
> - [**Recommendations**](#-recommendation-agents).  AI tools that analyze multiple data sources together to detect patterns and provide context-aware recommendations for operational decisions.
> - [**Artifact generation**](#-artifact-generation-agents).  AI tools that convert written requirements into executable code, infrastructure definitions, and automated tests while adhering to defined standards.
> - [**Policy validation**](#-policy-validation-agents).  AI tools that review code, configurations, and workflows against policies, standards, and design documents to enforce compliance.
> - [**Optimization actions**](#-action-optimization-agents).  AI tools that use insights across artifacts to route work and take action on decisions.

> [!CAUTION] 
> 
>   Safeguards is not hypothetical when involving agents. One unchecked model, one misapplied automation, or one over-permissive access setting can propagate errors, leak sensitive data, or compromise operational integrity at scale. 
> 
>   To protect sensitive data, all platforms must enforce strict PII masking and security trimming. Users see only the outputs they are authorized to access. This means AI output may be incomplete, but full visibility comes at the cost of potential exposure. 
> 
>  Human review remains a must, especially for architectural, security, and operational concerns. Reviews should focus on intent, risk, and fit with organizational standards rather than low-level syntax. Feedback from reviews should be captured to continuously improve prompts, templates, and standards.


#### &#10003; Summarization agents

Summarization agents typically use a simple, Copilot-style architecture with straightforward retrieval and response generation, making them relatively easy to implement and operate.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Summarization carries inherent correctness risk, particularly when synthesizing across multiple documents. While errors cannot be fully eliminated, operational risk can be reduced through explainability and incremental navigation. Systems should clearly indicate what content has been summarized and allow users to drill into the source material for validation.

Inference costs can accumulate over time. Route straightforward requests to smaller, lower-cost models, and reserve more advanced models for complex multi-document synthesis, accepting the additional orchestration this may require. Provide concise initial summaries and allow users to drill down into supporting details and source content.

Data management introduces additional hidden costs. Actively manage the data lifecycle to prevent index bloat caused by outdated documents or redundant versions. When historical context is necessary, retain prior content through deliberate versioning rather than uncontrolled duplication.

Direct user feedback is valuable. Capture input on summary quality and usefulness, and use it to evaluate model routing decisions, index effectiveness, and the impact of caching or preprocessing strategies.

##### Examples

- [OE:01 DevOps culture](./devops-culture.md#establish-clear-roles-and-responsibilities). Extract structured elements such as action items, owners, deadlines, and risk statements from unstructured documents.
- [OE:08 Incident response](./incident-response.md#turn-rca-findings-into-system-improvements). Summarize incidents, postmortems, security findings, and audit reports to quickly understand scope, impact, and outcomes

#### &#10003; Recommendation agents

AI agents that provide recommendations rely on reasoning-oriented models capable of analyzing multiple data sources. These models must have sufficient analytical depth to support cross-source correlation rather than relying on lightweight or purely generative approaches.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** While broader scope can add value, cross-referenced sources may be misweighted or misaligned with the original intent; over-reliance on such AI-generated responses risks amplifying errors and potentially compounding the problem with iterative calls. 
>
>   They typically increase per-request cost and inference latency. Minimize external calls by favoring fewer, richer queries over many fine-grained ones. Accessing and correlating multiple external sources at runtime can be expensive, so parallelize data access and, where feasible, preload data into shared indexes.

Working with multiple sources adds integration complexity. Errors in a single source can propagate through the recommendation pipeline. Apply validation and security guardrails when combining inputs. When low latency is required, query sources in parallel. Preprocess steps that do not depend on the specific request, such as classification, enrichment, and lookups. Cache intermediate results and frequently used features to reduce repeated computation.

Treat recommendation engines as decision-support systems rather than black boxes. Explainability is central to building trust and operational reliability. Systems should provide clear rationales for recommendations, highlighting key signals and contributing data sources. Consider including confidence indicators (for example, 0–100%) to help downstream systems or users gauge reliability.

##### Examples

- [OE:06 Designing a workload supply chain](./workload-supply-chain.md#incorporate-comprehensive-types-of-testing). Find customer-focused edge cases and scenarios that are  hard to detect and often overlooked to include in your test suite.
- [OE:08 Incident management](./incident-response.md#allocate-sufficient-resources-for-incident-response-infrastructure-processes-and-staff). Validate vendor transition plans by having AI simulate the vendor support team using only the provided documentation, playbooks, health models, and escalation paths. The simulation highlights gaps and hidden dependencies before the handoff.
- [OE:09 Implementing automation](./automate-tasks.md#integrate-automation-into-your-workloa). Evaluate automation code, telemetry, and incident data to recommend which automations should be improved, retired, or expanded.

#### &#10003; Artifact generation agents

AI agents can assist in generating code, infrastructure definitions, and tests, but their outputs may become part of a production workload. Code generation is inherently non-deterministic, and translating natural-language requirements into executable artifacts can produce results that diverge from the original intent. For this reason, clear ownership, explicit controls, and integration into existing engineering practices are essential. AI is most effective where the problem space is well understood and variation is limited, such as repetitive or standardized coding tasks, and guardrails should be applied to guide its outputs.

Selecting the right models is critical. Use models suited for code generation and tool execution, and combine them where appropriate. A reasoning model can help with system analysis, planning, or decomposition, a code-focused model can generate the artifacts themselves, and additional models can support testing or deployment steps.

Generation should be grounded in templates, reference implementations, coding guidelines, and examples that reflect organizational and industry standards. Clear standards help detect drift and enforce consistency. By using templates, AI output is more predictable. 

Like most agents, code generators may draw from multiple sources. All outputs should be treated as untrusted until validated. Apply least-privilege principles to limit tool execution permissions and scope. Agents should never deploy or modify production resources without explicit, gated approval.

Integrate generated artifacts into the standard developer lifecycle. This includes pull requests, code reviews, automated testing, and security scanning. Apply the same rigor as for human-authored code, including dependency checks and infrastructure-as-code scanning, to ensure reliability and compliance.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** . Human review is part of the cost model and must be factored into ROI. Automate validation as much as possible using linters, unit and integration tests, static analysis, and policy checks to reduce review effort and catch common issues early.

##### Examples

- [OE:02 Standardize operations](./formalize-operations-tasks.md#document-standards-and-treat-it-as-a-living-asset). Generate code and document artifacts that adhere to organization standards, and keep standards documentation updated as assets evolve. 
- [OE:07 Design a monitoring system](./observability.md#analyze-and-visualize-to-support-actionable-decisions). Generate integrated dashboards configurations that align engineering metrics with business outcomes by automatically selecting the right metrics across sources.
- [OE:10 Automation design](./enable-automation.md#automate-bootstrapping). Autonomously monitor production environments for configuration drift, infer the intended state, and update bootstrapping definitions to keep systems aligned over time. 

#### &#10003; Policy validation agents

AI agents can assist in reviewing and validating assets against policies and standards. Their role is to support decisions, flag deviations, and enforce compliance, while humans retain final oversight.

Validation begins with careful evaluation and testing before rollout. Standards should be versioned, and each asset should clearly reference the applicable policy, ensuring traceability. As policies evolve, maintenance overhead must be considered, and validation processes updated accordingly. Where possible, batch and parallelize reviews, and focus incremental checks on changes rather than rescanning all assets.

Cost and performance require a careful balance. Consider the amount of historical data needed to make accurate predictions against the impact on storage, processing, and latency. Too little data reduces reliability, while too much increases cost.

Security remains a key factor. Access to validation outputs should be restricted to authorized users, such as security reviewers, ensuring sensitive information is protected.

Effectiveness is measured, not assumed. Use dashboards to track metrics such as issues detected versus issues in production, false positives, and coverage. Feed these insights back into the validation logic, prompts, and operational processes, continuously refining the agent's contribution.

##### Examples

- [OE:03 Formalize development practices](./formalize-development-practices.md#standardize-how-development-work-is-recorded).  Validate work descriptions and acceptance criteria against organizational templates to enforce a consistent quality bar. 
- [OE:04 Standardize tools and processes](./tools-processes.md#implement-standards-for-addressing-technical-debt). Review the codebase to enforce quality bar and organizational standards and recording gaps as technical debt.
- [OE:05 Use infrastructure as code](./infrastructure-as-code-design.md#enforce-security-in-iac-code).Enforce security standards by reviewing code, IaC templates, and designs to detect insecure patterns, policy violations, and weak configurations.

#### &#10003; Action optimization agents

Action optimization agents extend beyond analysis and recommendations by taking direct operational actions. Because their outputs can modify systems or processes, these agents require careful design, oversight, and integration into workflows.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Security is a primary concern. Agents should ideally operate within a human-in-the-loop workflow, where proposed actions are reviewed and approved before execution in production. Access to tools and systems should follow the principle of least privilege, limiting the agent to only the permissions needed to perform its tasks. Detailed auditing is essential, capturing what actions were proposed, who approved them, and execution logs for traceability.

Implement guardrails that enforce a minimal blast radius, keeping the scope of each change limited. Tool executions should be idempotent to allow safe retries, and the system should include validation and rollback mechanisms. Checkpoints, backups, or other recovery strategies can support safe correction of unintended changes.

##### Examples

- [OE:08 Incident management](./incident-response.md#build-monitoring-capabilities-for-rapid-detection). As soon as an alert fires, automatically gather context, correlate data, and perform initial triage. Engineers start with a clear incident picture instead of manual data collection.
- [OE:09 Implementing automation](./automate-tasks.md#areas-to-implement-automation). Continuously optimize low‑risk production settings, such as cache sizes and timeout values, within human‑defined boundaries, using values inferred from analysis of monitoring data. 
- [OE:11 Safe deployment practices](./safe-deployments.md#adopt-a-progressive-exposure-model). Automate your progressive exposure deployment strategy by autonomously identifing the optimum rollout timing, and the right target segment and percentages for your canary deployments.

# [**Level 1: DevOps foundation**](#tab/level1)

![Goal icon](../_images/goal.svg) **Emphasize teamwork and unity in problem-solving to establish a strong foundation that creates consistent and stable operations in later stages.**

Establish a DevOps mindset at Level 1 to ensure the success of future strategies. Implement well-established DevOps methodologies to enhance process efficiency. Focus on building essential and common vocabulary, processes, and tools for stable operations.

**Key strategies**
> [!div class="checklist"]
>
> - [Encourage collaboration and foster a blameless culture](#-encourage-collaboration-and-foster-a-blameless-culture)
> - [Adopt standard collaboration methodologies and tools](#-adopt-standard-collaboration-methodologies-and-tools)
> - [Set up source control processes](#-set-up-source-control-processes)
> - [Use infrastructure as code (IaC) as your primary deployment approach](#-use-infrastructure-as-code-iac-as-your-primary-deployment-approach)
> - [Prioritize security from the start](#-prioritize-security-from-the-start)

#### &#10003; Encourage collaboration and foster a blameless culture 

Align team efforts with business needs while fostering a collaborative culture.

Members from centralized teams, full-time staff dedicated to workload functionality, partners, or vendors often manage workload operations. These individuals should function as a collective force, with mutual respect and acknowledgment for each other's expertise. If teams operate as independent parts, complexities and friction can occur. Independent teams undermine the goal of functioning as a single, efficient system that drives business outcomes.

To reduce an isolated sense of ownership, advocate for a unified approach to problem-solving. All efforts should cater to the needs of the business. View both successes and failures as shared outcomes.

#### &#10003; Adopt standard collaboration methodologies and tools

Begin with industry-proven tools and software development lifecycle (SDLC) processes that suit your workload and enhance development efficiency. Don't diverge from proven methods and avoid custom methodologies because they often introduce higher friction.

Popular choices include Agile, Scrum, and Kanban boards. Most experienced developers, DevOps engineers, and product owners have familiarity with these tools, which minimizes the learning curve for new hires.

Initially, use established industry standards to incorporate standardization. Optimize processes later. Ensure that the tools that you select can grow with your needs, without requiring a switch to cutting-edge solutions prematurely. 

#### &#10003; Set up source control processes

Based on the scale of the application, decide how to structure source code. For larger systems, each team should have its own processes for building and deploying components they're responsible for. They should have clearly defined interfaces that allow for component discoverability and sharing with other parts of the system. Select a source control technology and set up processes to ensure team members don't interfere with each other's work.

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

**Key strategies**
> [!div class="checklist"]
>
> - [Define team roles and decision-making responsibilities](#-define-team-roles-and-decision-making-responsibilities)
> - [Strive to make improvements, no matter how small](#-strive-to-make-improvements-no-matter-how-small)
> - [Standardize foundational processes](#-standardize-foundational-processes)
> - [Buy tools instead of building custom tools](#-buy-tools-instead-of-building-custom-tools)
> - [Adopt automation across the workload](#-adopt-automation-across-the-workload)
> - [Extend your infrastructure-as-code experience to configure your infrastructure and manage your applications](#-extend-your-infrastructure-as-code-experience-to-configure-your-infrastructure-and-manage-your-applications)
> - [Define your workload's deployment strategy](#-define-your-workloads-deployment-strategy)
> - [Design the workload monitoring stack](#-design-the-workload-monitoring-stack)

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

**Key strategies**
> [!div class="checklist"]
>
> - [Use separate environments for promoting releases](#-use-separate-environments-for-promoting-releases)
> - [Perform sufficient testing](#-perform-sufficient-testing)
> - [Automate testing and other quality checks when possible](#-automate-testing-and-other-quality-checks-when-possible)
> - [Set up approval processes](#-set-up-approval-processes)
> - [Implement automated deployments](#-implement-automated-deployments)
> - [Develop a health model](#-develop-a-health-model)
> - [Set up your incident management process](#-set-up-your-incident-management-process)

#### &#10003; Use separate environments for promoting releases

At this level, it's important to establish *release promotion* as a formal change control protocol before going live. This process progresses proposed changes through various stages with quality gates. Each stage undergoes thorough testing, and changes advance only if they pass these checks and receive approval.

Create distinct environments for different stages like dev/test, quality assurance, user acceptance testing (UAT), and production. This approach ensures thorough validation before moving to the next stage. The idea is to manage risks in the lower environments because errors should be caught early to minimize the blast radius.

#### &#10003; Perform sufficient testing

At this level, decide on a strategy where testing is prioritized for the critical parts of the system. Use the following steps to ensure a robust testing strategy:

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

After you've a basic monitoring system in place, combine business context with monitoring data to quantify the overall health status of workload components and the overall status. This exercise, known as *health modeling*, involves contextualizing monitoring values from infrastructure and applications with business context. To build an effective health model, consider the following key principles:

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

![Goal icon](../_images/goal.svg) **Ensure that the system meets the quality standards promised to users and prevents violations of service-level agreements.**

At previous levels, the workload team focuses on building features and getting the system into production. At this level, the focus shifts from building features to maintaining and improving a live system. With real users now relying on it, the priority becomes change management through efficient day-2 operations such as triage, maintenance, upgrades, and troubleshooting.

The main strategy is to use real-world experience to improve operations. Testing also becomes a non-negotiable practice to reduce risks associated with changes. You must integrate testing into every part of development, from fixing bugs to adding features and refining incident response. Without it, serious problems might go undetected until they reach production.

At this level, technical debt becomes a real concern. Implementations that are less than ideal might go live, which can complicate maintenance. Teams should analyze the maintenance burden and focus on reducing it.

**Key strategies**
>
> [!div class="checklist"]
>
> - [Use safe deployment practices](#-use-safe-deployment-practices)
> - [Optimize build operations](#-optimize-build-operations)
> - [Validate incident response processes](#-validate-incident-response-processes)
> - [Optimize operations by using monitoring data from production](#-optimize-operations-by-using-monitoring-data-from-production)
> - [Automate maintenance](#-automate-maintenance)
> - [Improve efficiency by aligning tools and processes with organizational standards](#-improve-efficiency-by-aligning-tools-and-processes-with-organizational-standards)
> - [Manage technical debt at a regular cadence](#-manage-technical-debt-at-a-regular-cadence)

#### &#10003; Use safe deployment practices

After production, the three key types of changes typically include routine updates, new feature updates, and emergency updates. Use safe deployment practices to keep the system stable during these changes. Regardless of the type of change, treat every change as a potential point of failure for the workload's users.

Integrate the following strategies into your change control process:

- **Validate continuously and comprehensively.** Test early and often throughout the development life cycle and as changes progress through different environments. Ideally, each time an artifact changes, create tests focused on those changes. Then run the full test suite to validate flows end-to-end. Test results provide validation data, but business stakeholders should still approve these changes.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Running the entire test suite builds confidence in deployments. However, it might not be practical for all changes because of time and cost. Balance thorough testing with cost considerations. Tailor the approval process based on the impact of changes. Minor changes should have a simplified procedure, while significant changes, like new features, require thorough review.

  At this level, you can adopt advanced operational concepts such as regional failovers. The goal is to fully automate these processes, with a focus on self-healing in most scenarios. These processes must also be tested extensively.

- **Implement versioning for your APIs.** Manage changes to your data model carefully to ensure backward compatibility. An API versioning strategy helps existing systems continue to run smoothly after changes are deployed. Retrospective versioning can be difficult, so establish a strategy early.

- **Roll out incremental updates.** By Level 3, deployment processes are standardized by using automated pipelines across all environments. At Level 4 maturity, the workload is in production. The focus shifts to refining incremental updates, including managing release cycles.

  Deploy small, frequent updates to simplify validation for a small set of changes. Automate validation tasks like load testing, deployment to test environments, and A/B testing.

  > [!NOTE]
  > Safe deployment patterns, like canary and blue-green deployments, provide flexibility and reliability through side-by-side deployments. For example, in blue-green deployments, a new environment is built, traffic is shifted, and the old environment is decommissioned. Other deployment techniques include feature flags and dark launches. These approaches allow testing in production before changes are rolled out to all users. This capability is available with specific Azure services such as Azure App Service, where updates can be rolled out by gradually swapping between deployment slots.

- **Recover from deployment errors.** Expect some updates to fail. With incremental updates, troubleshooting becomes faster when problems occur. If a failure occurs, stop the system to prevent further damage and implement changes to fix the problem. Restoring from backups is acceptable if it maintains continuity. The goal is to move forward to a stable version instead of relying solely on rollback procedures.

#### &#10003; Optimize build operations

At Level 3, you should have separate deployment cycles for different layers of the architecture based on their rate of change. At a minimum, keep infrastructure and code pipelines.

Now that the workload is in production, revisit the layering approach. If possible, further decouple architectural components to enable more flexible release cadences. This approach reduces delays and minimizes failures in individual components. Also, run tests and long-running processes as parallel jobs to save time and enhance developer productivity.

#### &#10003; Validate incident response processes

At Level 3, you establish an on-call support system with playbooks to define responses to incidents. However, having a playbook is only the first step. Now that the workload is in production, you must validate and enhance the effectiveness of your incident management process and develop a robust communication plan. Consider the following practices:

- **Test responses to incidents.** Incorporate responses from technology, people, and processes. To introduce realism into your validation efforts, we recommend that you run game days. Game days are planned events where faults are introduced to test the team's ability to detect and resolve problems. This approach ensures that the team has the right tools, resources, and procedures in place. Chaos engineering is another valuable technique that introduces controlled disruptions to observe the outcomes. Alternatively, manual methods such as disabling back ends on a global load balancer or performing a database failover can also be used to test the response.

- **Develop a communication plan.** Clearly define communication responsibilities across the workload team, support teams, and emergency response personnel. Standardizing the cadence and format of internal status updates to business stakeholders fosters transparency and trust. In specific scenarios, such as security breaches, responsible disclosure to end users is required. Ensure that the appropriate type and level of information are clearly defined in these external communications.

- **Conduct an incident review.** Treat every incident as an opportunity to learn from production. Use this process to identify weaknesses in your deployment and development processes, and commit to making system improvements.

#### &#10003; Optimize operations by using monitoring data from production

At Level 4, advanced monitoring should emit, correlate, and analyze metrics within a business context. At this level, improve its accuracy by learning from production. Use monitoring data to refine processes that were built on best guesses. Consider the following key examples:

- The primary focus in Level 3 is developing a health model for the workload. At Level 4, fine-tune the alerting system and set realistic objectives and service-level indicators.

- As part of day-2 operations, minimizing configuration drift should be a key priority. Without this focus, the runtime environment might gradually diverge from its intended state. Begin by capturing a snapshot of the known-good configuration. Then take advantage of observability metrics from production to compare current behavior against that baseline. This approach ensures ongoing alignment with the intended system state.

- This level is ideal for introducing feedback loops to better understand how the system behaves under specific stressors and to predict the impact of new features. System telemetry guides these feedback loops by providing key insights that help forecast workload changes and shape proactive solutions to potential problems. You can also use this data to help you prioritize technical debt.

As a general practice, fine-tune the monitoring stack based on observability data and patterns in production. Consider the following practices:

- Adjust logging levels to balance visibility and noise to capture activities on the critical paths.

- Amplify the important alerts while suppressing the irrelevant ones.

#### &#10003; Automate maintenance

At Level 3, automation efforts primarily focus on deploying to production. By Level 4, teams have significantly reduced manual work by automating build, test, and deployment processes by using continuous integration and continuous delivery pipelines. Like with quality gates, specific approvals might also be managed through automated workflows.

At Level 4, operational automation should be driven by real-world production experience and focused on addressing technical debt.

Consider the following day-2 automation examples.

| Process | Benefit |
|---|---|
| Automate rotation of certificates, API keys, and other secrets. | Automation guarantees timely rotations, eliminating the need for manual intervention, which saves time and reduces the likelihood of human error. |
| Automate routine maintenance of infrastructure. | Routine infrastructure maintenance requires extensive testing and coordination. Automation can expedite these tasks, reducing manual effort and minimizing risks. |
| Automate emergency response process. | Without proper automation, people might resort to hasty, uncoordinated actions during an emergency release, potentially leading to further problems. |
| Automate scaling of resources on load spikes and drops. | Autoscaling ensures that resources are allocated dynamically based on demand. This allocation results in more efficient use of resources because when demand decreases, resources are deallocated, without excessive operational overhead. |
| Automate data retrieval and delivery. | This approach reduces the time and effort required to fulfill data requests sent by users. Instead of manually accessing databases, scripts are triggered for accessing the database, retrieving relevant data, and sending it to the user. |
| Automate the creation of developer environments based on specific criteria. | This approach ensures that environments are consistently created to facilitate safe changes in the workload, as part of the team's day-2 operations. |

> [!NOTE]
> When you develop a deployment automation strategy, start with known and predictable tasks. Factor in common points of failure. After these points are automated, extend coverage to handle unforeseen problems, some of which might require manual intervention. For example, start by automating routine tasks like infrastructure updates because they're more manageable. Then tackle emergency hot fixes because they might include unknown failure scenarios.
>
> For example, a team might routinely deploy a workload by using controlled exposure to users across all geographies. This process might take several days to complete. They also need the ability to deploy hot fixes sooner by skipping specific steps. The automation process should account for those expedited deployments.

The primary goal is to identify repetitive, human-driven tasks that might have been overlooked in earlier stages because of deadlines. But you shouldn't automate everything. Return on investment should guide automation. Prefer using existing technologies and knowledge instead of starting with entirely new tools. If lightweight tooling is needed, evaluate its life cycle and maintenance requirements.

#### &#10003; Improve efficiency by aligning tools and processes with organizational standards

At Level 4 maturity, focus on gaining operational efficiency by evaluating engineering assets and processes. Identify which assets are essential but not core to your business.

For these assets, consider the following points:

- Use shared tools already available in your organization.

- Consider non-Microsoft software for specific tasks, like data conversion.

Prebuilt assets come with support channels and can replace custom solutions. This approach reduces the operational burden of your team-created solutions. Evaluate how well these resources meet your needs and identify any remaining gaps.

Explore the following areas of the workload:

- **Evaluate your custom code.** Instead of writing custom code for tasks like parsing, evaluate open-source solutions that are considered industry standard. Using these tools can reduce the need for code maintenance and result in a smaller code base. Explore options already available within your organization. There might be existing libraries that you can integrate into your workload to handle routine tasks such as authentication.

- **Evaluate your tool chain.** Assess areas where you can rely on other teams that use similar tools. Adjust your use of libraries, templates, and modules accordingly. Align infrastructure-as-code tools across the organization to streamline operations.

- **Evaluate your processes.** Identify centralized processes that can do tasks that you might have implemented yourself, such as security scanning. Instead of managing your own quarantine process for NuGet packages, use the organization's existing security team's process by informing them of the modules used in your workload.

  Supportability is another key area. Early on, development teams often handle support themselves by monitoring metrics and fixing live problems. At this stage, consider setting up dedicated roles like on-call engineers. If your organization has a shared support team, use it to reduce the support load on developers.

  > [!NOTE]
  > If possible, transition day-to-day support to external vendors. Vendors don't have deep context like the development team or the architects who bring the workload to production. Before you hand off tasks to a vendor, make sure that the system is stable in production and clearly define management tasks. Vendors need key elements to succeed. Define thresholds in the health model that represent *Healthy*, *Unhealthy*, and *Degraded* states. Train vendors on playbooks, tools, and other troubleshooting resources. If they can't identify causes, set up well-defined pathways for escalating and routing problems to the workload team.

#### &#10003; Manage technical debt at a regular cadence

Technical debt is the result of shortcuts that you take during development to meet deadlines, which can result in implementations that are less than ideal. Teams should work on reducing this debt by analyzing maintenance complexity and time. If technical debt isn't addressed, systems can become more complex and harder to maintain or scale. This complexity slows innovation as developers spend more time fixing problems instead of working on new features.

Consider the following tactical recommendations for handling technical debt:

- Track technical debt alongside feature work.

- Reserve capacity in every sprint for addressing technical debt, separate from feature development. Occasionally, dedicate entire sprints to addressing technical debt.

- Add the proposed resolution to the backlog right away if you plan to incur new technical debt for new features.

Technical debt is a normal part of development and an opportunity for improvement. As new features are added, debt accumulates. Balance the effort of paying off old debt with the new debt from developing new features.

# [**Level 5: Future adaptability**](#tab/level5)

![Goal icon](../_images/goal.svg) **Keep improving and adapting, but never assume that you're done.**

You establish a well-architected workload on Azure as you progress through Levels 1 to 4. Your design sets up operational practices like monitoring, testing, deployment, and automation, which prepares the system for production. After the system is in production, you focus on stabilizing operations and managing changes to protect the user experience.

In the final stage, maturity means operating the workload at scale, which keeps it reliable and up-to-date. It also requires proficiency in identifying areas where the current design has reached its limits and preparing for changes in business requirements. Assumptions about constraints can become outdated as the ecosystem evolves. Staying static might result in regression because the environments, practices, and tools continue to evolve. Without investment in Level 5 approaches, your workload risks falling behind.

Level 5 isn't an end goal or a technical checkpoint. It's a mindset shift focused on modernizing culture, processes, tools, and technology. Operations are treated with the same rigor, investment, and innovation that the workload's application receives.

**Key strategies**
>
> [!div class="checklist"]
>
> - [Spot rearchitecture opportunities based on observed growth and future potential](#-spot-rearchitecture-opportunities-based-on-observed-growth-and-future-potential)
> - [Use automation to further reduce friction](#-use-automation-to-further-reduce-friction)
> - [Create developer environments for each feature or change](#-create-developer-environments-for-each-feature-or-change)
> - [Share knowledge and contribute to organizational maturity](#-share-knowledge-and-contribute-to-organizational-maturity)
> - [Enable self-service capabilities for your workload's various job functions](#-enable-self-service-capabilities-to-your-workloads-various-job-functions)

#### &#10003; Spot rearchitecture opportunities based on observed growth and future potential

Workload architectures are intentionally designed with constraints and have limited lifespans. Through Level 4, your architecture likely meets business requirements effectively. At this level, evaluate how the system handles new usage patterns, technologies, growth, and operational challenges.

For example, a solution that works for thousands of users might fail when scaled to tens of thousands. Increased data volume can introduce atomicity problems, while greater demands for performance and compliance can push your architecture to its limits. These pressures can prevent the delivery of new features and create scaling bottlenecks.

At this level, recognize tipping points and *identify areas where you might need to rearchitect*. Consider the following areas:

- Your *initial choice of tools, frameworks, and platform services* might have suited your business needs. However, as your system evolves, these tools might become rigid or tightly coupled, libraries might lack extensibility, and platform services might reach end-of-life, which breaks existing dependencies.

  Explore tools and services within the broader ecosystem that provide strong support and widespread community adoption. Select a modular, loosely coupled architecture for easier replacement or upgrades.

- At earlier levels, the workload team might *manage the entire technical stack*. This approach can be effective initially, but it often results in increased pressure and operational overhead as the system scales. To ease this burden, consider offloading responsibilities to specialized teams, like those focused on networking, security, or observability. Their expertise enables the core workload team to focus on delivering product value.

- Managing *customer-dedicated (or single-tenant) instances* can increase both cost and operational overhead. This challenge often signals the need to adopt a multitenant architecture. This shift requires broader architectural and operational investments, such as handling tenant onboarding and data access isolation.

- Rethink your deployment strategy to scale predictably and handle failure isolation and performance at scale. Explore proven practices like the Deployment Stamps pattern.

  - **A scale unit** is a logical group of resources that scale together to handle increased load while being monitored independently. These units are automatically deployed as repeatable blocks based on defined thresholds and can be replicated when needed.
  
    However, this approach can lead to over scaling specific services, which results in added costs.

    For more information, see [Scale-unit architecture](../mission-critical/mission-critical-application-design.md#scale-unit-architecture).

  - **Immutable environments** are deployment setups where systems are never modified after deployment. Instead of updating a deployment stamp in place, tear down the old stamp and redeploy it with the necessary changes. This approach requires side-by-side deployment models such as blue-green deployments. To adopt this practice, pipelines must be prepared to automatically deploy a new stamp to handle hyperscaling or replace unhealthy units.

    Your Level 4 safe deployment practices should prepare you for this transition. There should be no regression, and users should transition to the new stamp smoothly.

#### &#10003; Use automation to further reduce friction

Transitioning from Level 4 to Level 5 isn't only about increasing automation. It's also about using automation to reduce friction and deciding when these tasks should run.

Consider the following examples:

- **Automated creation of local developer environments:** Provide a standardized experience by configuring each developer workstation with the same set of tools, library versions, and other development assets. This consistency gives all developers access to a uniform environment, regardless of experience level. It also facilitates better collaboration, knowledge sharing, and onboarding. Use virtualized development environments to simplify this process.

- **Automated alert triage and resolution workflows:** Use automation to categorize and prioritize alerts based on predefined rules, and trigger resolution workflows. For high-priority alerts, the system can notify the relevant team and start the resolution process to speed up response times.

- **Automated incident remediation:** Implement self-healing scripts that automatically restart services or shift workloads when problems are detected. For example, if a web server crashes, a script can restart the service or reroute traffic to a backup server to minimize downtime.

- **Automated resource usage:** As maturity in other pillars progresses, you likely need to introduce automation to support their goals. For example, you can schedule noncritical resources and environments during off-peak hours to reduce costs and optimize resource usage.

- **Automated tenant management:** Streamline the onboarding and offboarding of tenants in multitenant environments. When a new tenant signs up, automation can create user accounts, provision resources, and configure settings.

#### &#10003; Create developer environments for each feature or change

Day-2 operations focus on implementing safe changes. Each change goes through different environments that are set up for specific purposes such as development, testing, and security. By following Level 4 guidance, it's assumed that you've automated the creation of those environments.

Unlike Levels 1 to 4, which focus on looking internally at production readiness and managing workload change, Level 5 is an opportunity for workload team to share their designs, successes, failures with other workloads across the organization. Workload teams don't need to experience failure themselves in order to learn how to avoid it if they can preemptively learn it from others who have gone before them.

#### &#10003; Share knowledge and contribute to organizational maturity

Levels 1 through 4 focus on internal readiness and managing workload change. Level 5 is different because it creates an opportunity for workload teams to share their designs, successes, and failures with other teams across the organization. A team doesn't have to experience failure firsthand if it can learn from others who have experienced similar challenges.

This shift involves transitioning from Operational Excellence principles at the individual workload level to the organization that invests in centralized operations. These centralized teams are composed of dedicated engineering groups that build deployment tools equipped with guardrails, automation, observability, and testing.

Your workload team should share techniques, tools, and insights that have been effective with peer teams. Begin by engaging with teams that your workload depends on. Then connect with teams that depend on your workload. As you share experiences and outcomes, learn from those teams. You should see increased knowledge sharing through shared platforms, documentation, and community engagement.

#### &#10003; Enable self-service capabilities to your workload's various job functions

As you experience repetitive, unplanned tasks within the workload's routine development responsibilities, evaluate whether they should instead be delivered as scripted solutions that team members can responsibly invoke when needed. Build and maintain self-service capabilities for these unplanned tasks to enhance team agility and autonomy.

Start with time-consuming or error-prone unplanned tasks that are cost-effective to deliver to the workload team as a self-service solution. The solution must optimize team members' time and build consistency into these tasks. This approach results in a return on investment over the lifespan of the self-service solution.

Follow the approach and capabilities described in [Start your platform engineering journey](/platform-engineering/journey).

---

### Next steps

- Review the [Operational Excellence design review checklist](checklist.md) to get details on the recommendations.
