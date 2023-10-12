---
title: Azure Well-Architected workloads
description: Learn about the class of workloads that are well-architected as per the design principles of the framework.
author: ckittel
ms.author: chkittel
ms.date: 10/02/2023
ms.topic: conceptual
---


# Azure Well-Architected workloads

The term _workload_ in the context of Azure Well-Architected Framework refers to a collection of application resources, data, and supporting infrastructure that function together towards defined business outcomes. It's not just about the components that have been deployed as part of the workload; it also involves the development and operational procedures. 

Workloads are designed by architects and implemented by a workload team to achieve its functional and non-functional business requirements.
Workloads can be classified into types based on a variety of perspectives. 

Typical criteria for classification are:

- Their utility, characteristics, and usage patterns; such as web applications, batch processing, and real-time analytics.
- The key influential drivers; such as specific technology platforms or alignment with an industry.
- Their intended target audience, which can range from internal line-of-business applications within enterprises, to a purchased Independent Software Vendor (ISV) solution, or even a multi-tenant SaaS solution for public use.

Workloads within the same class share similarities including their target audiences, compliance requirements, and in some cases even similar technology stacks. The five pillars of the Well-Architected Framework, their principles, checklists, and tradeoffs are, by intent, designed to be agnostic to the class of workload.

The workload layer of Well-Architected Framework focuses on the common priorities and tradeoffs as they pertain to a specific class of workload. In workload guidance, you’ll notice agnostic pillar guidance being applied to the technical design principles and design areas that represent the priorities of workload. The recommendations will set up the workload for success from a Well-Architected perspective.

## What is a Well-Architected workload
No matter the complexity, resource requirements, or categorization of the workload, design and operations of the workload will always be forced to contend with the five architectural pillars of Reliability, Security, Cost Optimization, Operational Excellence, and Performance Efficiency. A Well-Architected workload has the following traits:

- has functional and non-function requirements defined and prioritized to achieve a meaningful purpose.
- is intentionally designed to achieve those requirements and purpose by strategic use of available resources through design patterns and tradeoffs.
- is built to the specification of the design and purpose.
- is operated to the specification of the design and purpose.
- is measured on its obtainment of purpose.
- can adapt as the purpose of the workload is refined or changed.

|:::image type="icon" source="./_images/goal.svg"::: Building a workload that’s based on Well-Architected Framework principles will lead to success because it’s founded in these ideals. Building a workload that's based on Well-Architected Framework principles will lead to success because it's founded in these ideals.|
|---|
|&#9745; just as reliable as it needs to be <br>&#9745;	just as secure as it needs to be<br>&#9745;	delivers a sufficient return on investment<br>&#9745;	is developed and operated responsibly<br>&#9745; accomplishes its purpose within an acceptable period|


The ideals above must emerge from a collaboration between the workload team and central teams of an organization. These teams and their typical functions are described in the following sections.  

## Workload team

The workload team is built on strong collaboration among a wide range of technical and business disciplines, all focused on the success of the workload as their primary objective. These workload-aligned disciplines come together to form the workload team. Examples of workload team members are:  

|Team member examples|&nbsp;|
|---| ---|
|Application security engineers <br>Business stakeholders<br>Cloud developer/software engineers <br>Cloud solution architects <br>Data scientists/analysts <br>Database administrators <br>|DevOps engineers <br>Infrastructure engineers <br>Product manager/owner <br>Quality Assurance (QA) engineers <br>Support team <br>|

## Central teams and stakeholders

The members of that workload team are often supported by centralized teams that provide  supporting functions and apply governance consistently spanning many or all cloud workloads within the organization. Their focus is organizational success, which comes in part through the success of the organization’s workloads. They partner to provide services, guidance, and guardrails to workloads to help accomplish that. Examples of such organizational teams are: 

|Team member examples|&nbsp;|
|---| ---|
|Business intelligence analysts<br>Business stakeholders<br>Cloud center of excellence board<br> Cloud platform team<br>Cybersecurity analysts<br>Database administrators<br>Enterprise architects<br>|Finance analysts<br>Infrastructure engineers<br>Legal/compliance officers<br>Network engineers<br>Procurement specialists<br>Project managers<br>|

A Well-Architected workload team focuses on workload outcomes while coordinating with and benefiting from the specialized support offered by centralized team members, using approved platforms and approaches that also benefit the organization.

## Shared responsibility model

Ultimately for a workload to deliver value it needs to be deployed and used. As part of the workload team, you have a _responsibility_ to design, implement, deploy, and achieve this utilization; as such, autonomy must be afforded to act on those responsibilities. However, workloads exist within the context of their organization, which often has directives set through key governing and authority roles. That means that a workload team also has the _responsibility_ to design, implement, deploy, and achieve this same utilization but within the foundation set by its organization.

The Cloud Adoption Framework for Azure has established that democratization of cloud resources to workload teams is essential, but this must be conducted with a rigorous approach to providing a governed platform in which workload teams onboard into. How an organization approaches this governance is rooted in the organization’s adopted cloud operating model. Azure landing zones are a core enabler of workload democratization in common operating models. Azure landing zones consist of the platform landing zone and then application landing zones. The workload is deployed in the application landing zone.

#### Azure landing zone integration

The Well-Architected Framework acknowledges that your organization may have rigorously formalized its cloud platform offering, fully aligning with Azure landing zones or maybe on a completely different adoption strategy. Likewise, your organization might have no such implementation, leaving workload teams nearly fully autonomous entities. 

No matter what platform and governance your workload finds itself under, applying principles of Well-Architected is still a requirement. The Well-Architected Framework will often reference Azure landing zones, but the architecture framework isn't inherently dependent on a specific platform implementation; as its pillars, principles, checklists, and guides are mostly cloud platform agnostic; much like they are mostly workload type agnostic.

## Fulfilling requirements

Throughout the Well-Architected Framework, be it in the core pillars or through workload-centric guidance, recommendations are delivered in terms that articulate the obligation of the workload, but do not usually imply what team members or even what team executes on those obligations. To determine "who" is responsible for "what", do a workload-level mapping exercise to account for your team’s roles and responsibility with respect to topology, workload type, and critically its organizational platform alignment expectations.

Most workload requirements are exclusively handled by the direct workload team, with the granted autonomy and the direct responsibility of workload success. Some requirements will need to be handled as a joint effort with centralized teams. That can be implementation choices under the guardrails set by centralized teams, or in some cases even be exclusively handled by centralized teams.

It's absolutely required that the workload team build a working relationship with all teams they partner with that will be co-delivering on the obligations of the workload. Anytime a workload outsources components or responsibilities, the result must not result in a failure to deliver on those obligations.

## Learn the constraints

Centralized teams support a portfolio of diverse workloads through core capabilities and core infrastructure. To provide this support on an organizational scale, uniformity and constraints may have been intentionally placed on the service offered or infrastructure. As you design your workload, it's critical that you understand those constraints and, where possible, partner with enterprise architects that know those constraints, seeking guidance from prior implementations.

While every platform governance implementation will be different, here are some constraints that workload teams might find themselves faced with.

- Cloud resource allowlists
- Cloud resource configuration mandates
- Cloud resource regional allowlists and cross-premises connectivity availability
- Patching requirements
- Supply chain control requirements
- Specific hub-spoke implementation, driving DNS and Private Endpoint implementations
- Limited or no platform support outside of business hours

## Communicate requirements, explicitly

When your workload requirement is faced with a constraint or a core capability or infrastructure offering isn't clearly defined with an SLA, then this situation must be treated as a risk. Addressing this risk requires the workload team to provide clarity to the teams it partners with on all relevant aspects of the concern. The outcome must be a change in either the workload requirements, design, or implementation or the outcome must be a change of the partner’s offering.

By having a bi-directional understanding of the platform team’s obligations for organizational directives and your workload team’s obligations, you will be able to communicate workload requirements with realistic expectations and recommendations.

#### Common workload requirements to communicate

While every platform partnership will be different, here are some common areas that often find themselves involved in shared responsibility conversations:

- Compliance/legal requirements
- Performance requirements such as network throughput, cloud resource availability, or regional availability
- SLO/SLAs offered to the workload’s users
- Observability requirements to provide effective live-site triage
- Networking specifics such as the need for static ingress or egress IP addresses
- Public Internet access expectations from both an egress and ingress perspective
- Technical support availability

## Looks for unified wins

Shared responsibility isn't just about tradeoffs, constraints, or compromise. Platform teams often have highly specialized skills and dedicated budgets that can augment beyond what an individual workload team could individually sustain. Some examples of this are:

- **Security specialists**. Your workload may have a secure development lifecycle, a centralized security team might be able to perform routine black-box penetration testing above and beyond your efforts as they perform these tasks at-scale across the organization. They may also be able to help with incident response planning and execution.
- **Enterprise architecture guidance**. Aligning to patterns and practices set forth by enterprise architecture teams often can prevent workload teams from solving problems that have already been solved or prevent rework when a solution is not possible within the partnership without negotiation.
- **Big-ticket expenditures**. Platform teams often host components or services that are often untenable from a pricing or management perspective at the individual workload team level. They can afford to do so by amortizing the cost of the component or service across its consuming workloads.

Often these services or centralized platforms are offered as mere showback, so they help keep the workload cost optimized. And when they are offered as chargeback, they are often cheaper due to economies of scale obtained from the centralization.

Another win is that platform teams often provide self-service options to workload teams for various activities. Some examples of this might be:

- Providing a documentation repository for self-guided education.
- Onboarding to cost management though specific resource tagging.
- Offering subscription obtainment through a formal subscription vending process.

These self-service options should be explored for their suitability for your workload’s obligations.

## Share in success, share in pain

Shared responsibility with other teams also means sharing successes and challenges of a workload. When your workload meets its obligations and obtaining the value set forth, share that with teams you partnered with. Make sure they understand how they are contributing to the workload’s success. When your workload isn't meeting its obligations, involve your partners to share what isn't working and collaborate and re-calibrate on getting back to obtainment of value.

Likewise, platform teams also have obligations and success criteria. You should expect to hear from your partners about how your workload is a good citizen of the offering or if it’s at risk of turning into a noisy neighbor.

## Things change and evolve

A horizontal theme across all Well-Architected Framework pillars is continuous improvement. Dealing with new approaches to existing problems, adopting new technology, addressing new requirements, operating under new constraints, are all aspects of adopting a progressive mindset. As your workload improves over time, expect the same from the teams you partner with. However, every improvement opportunity also means changes, and should be supported by a proper management process.

Workload teams have an obligation to communicate with platform teams about any proposed changes to workload requirements that might have an impact on their services rendered. Likewise, platform teams have an obligation to include their workload partners in change control processes, and clearly communicating the impactful platform changes.  Establish a regular communication cadence with partners to learn about and share how each is evolving their product.

## Grounded in organizational success

Workloads are surrounded by expectations - user expectations, shareholder expectations, regulatory body expectations, employee expectations, center of excellence expectations, CxO expectations – and the list goes on. Expectations can set the directional compass spinning, but being Well-Architected takes those signals and forces clarity in design and implementation through explicit architectural decision rationalization to reach an outcome that you and your customers love. Build a workload loved by its team and its customers; and share in that success with your organization.

