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

Security gates often introduce friction into development, support, and operations processes. Experiencing that friction from the start allows the workload team to adapt and looks for ways to optimize over time.

Make tools and processes transparent so that vulnerabilities can be easily detected through audits, and peer reviews. Explore industry-standard tools that support vulnerability scanning and security controls, even if not yet fully implemented. 

//TODO for Security: Your tools and deployment practices use the same identity provider as your production environments to avoid issues with duplicated identities and integration problems across different platforms. Minimize the different identity control planes.

# [Level 2](#tab/level2)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Standardization

<!-- No more than 5 H4 headings per tab -->

#### Example heading 

<!-- No more than 100 words under each H4 heading. -->

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
