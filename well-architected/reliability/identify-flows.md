---
title: Recommendations for identifying and rating flows
description: Learn how to create a catalog of user and system flows for your workload to better understand the basis for your design decisions as they relate to reliability.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for identifying and rating flows

**Applies to: RE 01**

This guide describes the recommendations for creating a catalog of user and system flows for your workload. Understand and model your flows to ensure that your reliability efforts benefit the most critical parts of your workload. Deprioritize the flows that don't have stringent availability requirements.

Create this catalog as you start to design your workload architecture so you can better understand the basis for your design decisions as they relate to reliability.

This guide serves as a prerequisite for the other reliability guides in this series. The following sections provide recommendations for workload design decisions and operational processes and procedures that support the workload, like monitoring and alerting, testing, business continuity, and disaster recovery functions.

**Definitions**

| Term | Definition |
|---------|---------|
| User flow | The sequence that a user follows to accomplish a task. |
| System flow | The flow of information and processes within a system. The system automatically follows this flow to enable user flows or workload functionality. |

## Key design strategies

Before you choose the architectural design for your workload, determine what the user and system flows for your workload look like. Assign a measure of criticality to those flows. Perform this exercise with business stakeholders to ensure that you capture the business requirements in the context of flows. Examples of a user flow include an ecommerce app checkout experience or the navigation component of an emergency response app. Examples of a system flow include the back-end credit card processing flow for an ecommerce app or batch database updates for an enterprise resource planning (ERP) system. For each flow, identify the:

- Business processes that the flow supports.

- Process owner, or the individual that's responsible for making critical decisions.

- Business escalation path for problems, like security concerns, urgent updates, and failovers.

- Technical escalation path for incidents that affect the health of the flow.

- Business expectations, like throughput, availability, and business hours.

- Business consequences if expectations aren't met, for example the effect on revenue or reputation.

- Criticality and priority.

Assign criticality to your flows to help you design your architecture. Identify the components that need higher levels of resiliency and security. To get started with criticality, use a *high*, *medium*, and *low* rating system that's based on the potential consequences when a given flow is unavailable. These criticality ratings are defined as:

- **High**: A flow that affects the customer experience, finances, or human health or safety.

- **Medium**: A flow that's necessary for the workload to fully function but doesn't directly affect the customer. For example, a flow might have a medium criticality rating if you can retry the flow after you resolve the problem without disrupting the customer experience.

- **Low**: A flow that's not directly tied to the functionality of the workload. For example, a nightly log transfer to a monitoring system or a pop-up survey feature might have a low criticality rating.

## Azure facilitation
<!--Looks like this article might not be complete. This section was blank. Leaving just in case.-->

## Tradeoffs

Ensure that stakeholders understand that higher expectations for reliability sometimes coincide with higher setup costs, operational costs, and management burden for operators.

## Example

The following example provides a complete scenario and illustrates important points to help you define your flows.

This scenario uses the [reliable web app pattern reference architecture](/azure/architecture/web-apps/guides/reliable-web-app/dotnet/plan-implementation) as the basis for the example flows.

:::image type="content" source="media/identify-flows/relecloud-flow.png" alt-text="Diagram that shows an example flow that's based on Relecloud." border="false" lightbox="media/identify-flows/relecloud-flow.png":::

### Workload
<!--There are comments here in the original Sharepoint doc. It sounds like this section is a placeholder for future ideas. Leaving it just in case.-->
Relecloud (eCommerce, line-of-business web app)

#### User flows

The following user flows comprise the example architecture.

##### User flow 1: Create upcoming concerts

Call center employees use the application to create an upcoming concert.

- **Business processes that this flow supports**: This flow supports the *purchasing ticket* process, but in an asynchronous way that makes it less critical.

- **Process owner**: The sales department.

- **Business escalation path**: Stakeholders.

- **Technical escalation path**: Platform or application teams.

- **Business expectations**: This flow doesn't require high availability because concerts are proactively created on a weekly basis.

- **Business consequences**: If this flow isn't available when a call center employee needs to create a concert, it affects revenue and reputation.

- **Criticality**: Medium.

##### User flow 2: Search concerts

Call center employees use the application to search for upcoming concerts.

- **Business processes that this flow supports**: This flow supports the *purchasing ticket* process, but call center employees can opt to list concerts if the search function isn't available.

- **Process owner**: The UX (user experience) department.

- **Business escalation path**: Stakeholders.

- **Technical escalation path**: Platform or application teams.

- **Business expectations**: This flow doesn't require high availability because call center employees can also list concerts.

- **Business consequences**: If this flow isn't available when a call center employee searches for a concert, it affects the application's reputation. The call center employee's experience might degrade and it might affect productivity because the employee spends more time on a call.

- **Criticality**: Low.

##### User flow 3: Get a list of the concerts

Call center employees use the application to get a list of concerts.

- **Business processes that this flow supports**: This flow directly supports the *purchasing ticket* process.

- **Process owner**: The sales department.

- **Business escalation path**: Stakeholders.

- **Technical escalation path**: Platform or application teams.

- **Business expectations**: This flow requires high availability because call center employees can't purchase tickets if this flow isn't working properly.

- **Business consequences**: If this flow isn't available, it directly affects revenue and reputation.

- **Criticality**: High.

##### User flow 4: Purchase ticket

Call center employees use the application (the *authentication and authorization* process) to buy tickets for an upcoming concert (the *list upcoming concerts* process) on behalf of Relecloud customers.

- **Business processes that this flow supports**: This flow is the core feature and flow of the application.

- **Process owner**: The sales department.

- **Business escalation path**: Stakeholders.

- **Technical escalation path**: Platform or application teams.

- **Business expectations**: This flow requires high availability because customers can't purchase tickets if this flow isn't working properly.

- **Business consequences**: If this flow isn't available, it directly affects revenue and reputation.

- **Criticality**: High.

##### User flow 5: Authentication and authorization

Call center employees securely sign in to the application and are provided with proper roles so that they can purchase tickets on behalf of Relecloud customers.

- **Business processes that this flow supports**: This flow directly supports the *purchasing ticket* process. Without this functionality, call center employees can't sign in to the application to buy tickets.

- **Process owner**: The sales department.

- **Business escalation path**: Stakeholders.

- **Technical escalation path**: Platform or application teams.

- **Business expectations**: This flow requires high availability because call center employees can't purchase tickets if this flow isn't working properly.

- **Business consequences**: If this flow isn't available, it directly affects revenue and reputation.

- **Criticality**: High.

### System flow: Collect telemetry

To understand state changes in the production system, web application and API instances are used to collect and send information, errors, and warnings. This data helps the operations team perform anomaly detection, troubleshooting, and profiling.

- **Business processes that this flow supports**: This flow doesn't support any business processes, but it provides important data for the operations team.

- **Process owner**: The operations team.

- **Business escalation path**: The operations team.

- **Technical escalation path**: The operations team.

- **Business expectations**: This flow requires as much redundancy and resiliency as possible. The operations team should strive to recover this flow from failure, so they don't miss important information and warnings.

- **Business consequences**: If this flow doesn't meet the expected availability, there's a risk of missing problems in production, which can cause serious consequences.

- **Criticality**: Medium

## Related links

Learn about determining [business criticality in cloud management](/azure/cloud-adoption-framework/manage/considerations/criticality).
