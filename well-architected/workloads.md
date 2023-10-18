---
title: Azure Well-Architected Framework workloads
description: Learn about the Well-Architected Framework workloads, which are application resources, data, and infrastructure that achieve business outcomes.
author: ckittel
ms.author: chkittel
ms.date: 11/15/2023
ms.topic: conceptual
---

# Azure Well-Architected Framework workloads

In the context of the Well-Architected Framework, the term _workload_ refers to a collection of application resources, data, and supporting infrastructure that function together to achieve defined business outcomes. A workload consists of components and also development and operational procedures.

Architects design workloads, and a workload team implements them. A workload is designed and implemented to achieve functional and nonfunctional business requirements. Workloads can be classified into many types.

Typical criteria for workload classification are the:

- Utility, characteristics, and usage patterns of a workload, such as web applications, batch processing, and real-time analytics.

- Key influential drivers, such as technology platforms or alignment with an industry.

- Intended target audience. Examples of solutions with different audiences are internal line-of-business applications within enterprises, a purchased independent software vendor (ISV) solution, or a multitenant software as a service (SaaS) solution for public use.

Workloads that are in the same class can share similarities including their target audience, compliance requirements, and technology stacks. The five pillars of the Well-Architected Framework, their principles, checklists, and tradeoffs are relevant for all workload classes.

The workload guidance of the Well-Architected Framework describes common priorities and tradeoffs as they pertain to specific workload classes. In the workload guidance, the pillar guidance applies to technical design principles and design areas that represent the priorities of a workload. Follow the recommendations to help set up a successful workload and to align it with the Well-Architected Framework guidance.

## What's a Well-Architected Framework workload?

For any workload, the design and operations of the workload have to contend with the five architectural pillars, which are Reliability, Security, Cost Optimization, Operational Excellence, and Performance Efficiency.

|:::image type="icon" source="./_images/goal.svg"::: To create a successful workload, develop it in accordance with the Well-Architected Framework principles, which are based on the following ideals.|
|---|

A Well-Architected Framework workload:

- Has functional and nonfunctional requirements that are defined and prioritized to achieve a goal.
- Is designed so you can achieve those requirements by using resources and incorporating design patterns and tradeoffs.
- Is built and operated to the specifications of a design and purpose.
- Is measured by how adequately it achieves its purpose.
- Can adapt as its purpose is refined or changed.
- Is just as reliable as it needs to be.
- Is just as secure as it needs to be.
- Delivers a sufficient return on investment.
- Is developed and operated responsibly.
- Accomplishes its purpose within an acceptable time period.

A workload team in collaboration with central teams of an organization must create a workload with the preceding characteristics. The following sections describe these teams and their functions.  

## A workload team

Create a workload team that has team members with a wide range of technical and business disciplines. The primary focus of all team members should be the success of the workload.

|Examples of workload team members are:|&nbsp;|
|---| ---|
|Application security engineers <br>Business stakeholders<br>Cloud developer or software engineers <br>Cloud solution architects <br>Data scientists or analysts <br>Database administrators <br>|DevOps engineers <br>Infrastructure engineers <br>Product managers or owners <br>Quality assurance (QA) engineers <br>Support team members <br>|

## Centralized teams and stakeholders

Centralized teams often support the workload team. They provide support functions and apply governance for many or all cloud workloads within an organization. Centralized teams focus on organizational success, which is achieved in part by the success of the organization's workloads. They provide services, guidance, and guardrails for workloads.

|Examples of centralized teams and team members are:|&nbsp;|
|---| ---|
|A cloud center of excellence board<br> A cloud platform team<br> Business intelligence analysts<br>Business stakeholders<br> Cybersecurity analysts<br>Database administrators<br>Enterprise architects<br>|Finance analysts<br>Infrastructure engineers<br>Legal and compliance officers<br>Network engineers<br>Procurement specialists<br>Project managers<br>|

A Well-Architected Framework workload team focuses on workload outcomes. They coordinate with and benefit from the specialized support from centralized team members.

## The shared responsibility model

A workload needs to be deployed and used in order to deliver value. As part of the workload team, you have a responsibility to design, implement, and deploy your workload in a way that creates value to your organization.

Workloads exist within the context of your organization. An organization often has regulated governing and authority roles. Your workload team has the responsibility to design, implement, and deploy a workload within the foundation of your organization.

In accordance with the Cloud Adoption Framework for Azure, standardize your workload's cloud resources. Rigorously apply standardization to provide a governed platform to help with onboarding workload teams. Apply this governance in accordance with your organization's cloud operating model.  You can use Azure landing zones to help you perform standardization. Azure landing zones can be platform landing zones or application landing zones. Deploy your workload in the application landing zone.

### Integrate Azure landing zones

Your organization might have a rigorously formalized cloud platform offering that fully aligns with Azure landing zones. Or your organization might have a different adoption strategy or no implementation. If there's no implementation, workload teams are nearly fully autonomous entities.

For any platform and governance that your organization uses, you must apply the principles of the Well-Architected Framework to your workloads. The Well-Architected Framework often references Azure landing zones, but it isn't dependent on a specific platform implementation. The Well-Architected Framework pillars, principles, checklists, and guides are for all cloud platforms and most workload types.

## Fulfill requirements

Throughout the Well-Architected Framework, such as the core pillars and the workload guidance, recommendations coincide with the obligation of the workload. Recommendations don't usually imply the team member or team that facilitates those obligations. You can determine who should perform each action. Perform workload-level mapping to determine your team's roles and responsibilities related to the topology, workload type, and criticality.

The direct workload team handles most workload requirements. Some requirements are handled as a joint effort with centralized teams. For example, the implementation choices might be based on guardrails that are set by a centralized team. Or a centralized team might exclusively handle the implementation choices.

Your workload team must build a working relationship with other teams to help codeliver on workload goals. If you outsource components or responsibilities, you must successfully deliver on those obligations.

## Learn the constraints

Centralized teams support diverse workloads based on the team's core capabilities and core infrastructure. To provide this support on an organizational scale, the centralized team might implement uniformity and constraints on the service offered or the infrastructure. As you design your workload, it's critical that you understand those constraints and, where possible, partner with enterprise architects that know those constraints. Learn from prior implementations as much as possible.

Every platform governance implementation is different, but the following constraints are common for many workloads:

- Cloud resource allowlists
- Cloud resource configuration mandates
- Cloud resource regional allowlists and cross-premises connectivity availability
- Limited or no platform support outside of business hours
- Patching requirements
- Specific hub-spoke implementation, which drives Domain Name System (DNS) and private endpoint implementations
- Supply chain control requirements

## Explicitly communicate requirements

If your workload requirement is faced with a constraint or a service-level agreement (SLA) that doesn't clearly define a core capability or infrastructure offering, treat that situation as a risk. To address this risk, your workload team must provide clarity to the other teams about how the concern affects the workload. You might have to change the workload requirements, design, or implementation, or change the infrastructure offering.

When you understand the platform team's obligations related to organizational directives and your workload team's obligations, you can communicate workload requirements with realistic expectations and recommendations.

### Communicate common workload requirements

Every platform partnership is different, but the following areas are common shared responsibility conversation topics:

- Compliance and legal requirements
- Networking specifics, such as the need for static ingress or egress IP addresses
- Observability requirements to provide effective live site triage
- Performance requirements, such as network throughput, cloud resource availability, or regional availability
- Public internet access expectations from an egress and ingress perspective
- Service-level objectives (SLOs) or SLAs that are offered to the workload's users
- Technical support availability

## Look for unified wins

Shared responsibility isn't just about tradeoffs, constraints, and compromise. Platform teams often have highly specialized skills and dedicated budgets that can augment beyond what an individual workload team can sustain. Consider the following examples.

**Security specialists.** Your workload might have a secure development lifecycle. As a centralized security team performs secure development tasks at-scale across your organization, they might perform routine penetration testing that's above and beyond your efforts. They might also help with planning and performing an incident response strategy.

**Enterprise architecture guidance.** You can save time and effort if you align with an enterprise architecture team's patterns and practices because the team has already streamlined the processes. You can also prevent rework if a solution isn't possible within the partnership without negotiation.

**Big-ticket expenditures.** Platform teams often host components or services that are too expensive or too extensively managed for an individual workload team. Platform teams can afford these components and services because they divide the cost across workloads.

  Often these services or centralized platforms are offered as mere showback, so they help keep the workload cost optimized. And when they're offered as chargeback, they're often cheaper due to economies of scale because of the centralization.

Platform teams often provide self-service options to workload teams for various activities. For example:

- Providing a documentation repository for self-guided education.
- Onboarding to cost management via specific resource tagging.
- Offering subscriptions via a formal subscription-vending process.

Explore self-service options that might be suitable for your workload.

## Share successes and challenges

Shared responsibility with other teams also means sharing successes and challenges of a workload. When your workload meets its obligations and obtains the intended value, share that with your partnering teams. Tell them how they contributed to the workload's success. When your workload isn't meeting its obligations, share what isn't working and collaborate and recalibrate to get back on track.

Platform teams also have obligations and success criteria. You should expect your partners to tell you whether your workload is a good citizen of an offering or if it's at risk of being a noisy neighbor.

## Continuous improvement

A theme across all Well-Architected Framework pillars is continuous improvement. Adopt a progressive mindset. You might deal with new approaches to existing problems, adopt new technology, address new requirements, or operate under new constraints. As your workload improves over time, expect the same mindset from your partnering teams. However, every improvement opportunity also means changes, and should be supported by a proper management process.

Workload teams have an obligation to communicate with platform teams about proposed changes to workload requirements that might have an effect on the platform team's services. Likewise, platform teams have an obligation to include their workload partners in change control processes, and clearly communicate the impactful platform changes. Establish a regular communication cadence with partners to learn about and share how a product evolves.

## Grounded in organizational success

Workloads have many expectations, like user expectations, shareholder expectations, regulatory body expectations, employee expectations, center of excellence expectations, and chief experience officer expectations. Expectations can set the directional compass spinning. The Well-Architected Framework provides clarity related to the design and implementation by offering explicit architectural decision rationalization that achieves a successful outcome. Develop a successful workload, and share in that success with your organization.
