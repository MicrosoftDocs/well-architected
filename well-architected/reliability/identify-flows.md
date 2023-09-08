---
title: Recommendations for identifying and rating flows
description: Learn how to identify and rate flows for your workload.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for identifying and rating flows

**Applies to: RE 01**

This guide describes the best practices for building a catalog of user and system flows for a proposed workload in ideation. By understanding and modelling your flows, you can ensure that you focus your reliability efforts on the parts of your workload that are most critical, and de-prioritize those flows that don't have stringent availability requirement Taking the time to build this catalog as one of the first steps in the process of designing your workload architecture will give you the details that you will use as the basis for your design decisions as they relate to reliability. In this series of reliability guides, the activities performed in this guide serve as a pre-requisite to all activities covered in the other guides. The design decisions covered in this guide extend out from the workload architecture design to the operational processes and procedures necessary to support the workload (like monitoring and alerting; testing; and business continuity/disaster recovery functions).

## Definitions

|Term  |Definition  |
|---------|---------|
|User flow     |  Workflow that an end-user of the system follows to utilize the workload       |
|System flow     |    Workflow that the system automatically follows to enable user flows or general workload functionality     |

## Key design strategy

The first step in designing a reliable workload takes place in ideation. Before you finalize your decision on an architectural design for your workload, you should take the opportunity to determine what the user and system flows for your workload will look like and then assign a measure of criticality to those flows. This exercise is one that you should undertake with business stakeholders to ensure that the business requirements are captured in the context of flows. Examples of user flows are the checkout experience for an ecommerce app, or the navigation component of an emergency response app. Examples of system flows are the back-end credit card processing flow for an ecommerce app or batch database updates for an ERP system. For each flow, discuss with stakeholders and identify the following:

-   Business process(es) this flow supports

-   The \'process owner\' - the individual responsible for making critical decisions

-   The business escalation path for issues like security concerns, urgent updates, and failovers

-   The technical escalation path for incidents affecting the health of the flow

-   Business expectations like throughput, availability, and business hours

-   Business impact if expectations are not met (for example: revenue impact and reputation impact)

-   Criticality and priority

Assigning criticality to your flows will help you with your eventual architectural design by identifying the components that will need higher levels of resiliency and security. To get started with thinking about criticality, you can use a simple *high, medium, low* rating system, based on the impact of a given flow being unavailable. Generally speaking, these criticality ratings can be defined as:

-   **High**: A flow that impacts the user experience, can have a monetary impact, or can have real-world human implications.

    -   The user flow examples given above are examples of high criticality as they can directly impact finances, user experience and human health and safety.

-   **Medium**: A flow that is necessary for the workload to fully function but does not directly affect the user.

    -   The system flow examples given above are examples of medium criticality as the flows can be retried after the issue has been resolved without disrupting the user experience.

-   **Low**: A flow that is not directly tied to the functionality of the workload

    -   Examples could include nightly log shipping to a monitoring system or a pop-up survey feature

## Azure facilitation

## Tradeoff

Ensure that stakeholders understand that higher expectations for reliability can potentially come at the expense of higher setup and operational costs as well as a higher management burden for operators.

## Example

To provide a more complete scenario and further illustrate the points discussed in the guidance, use the following example to help you in your efforts in defining flows.

In this scenario, we will use the Reliable Web Pattern [reference architecture](/azure/architecture/web-apps/guides/reliable-web-app/dotnet/plan-implementation) as the basis for our example flows.

:::image type="content" source="media/identify-flows/relecloud-flow.png" alt-text="Diagram that shows an example flow based on Relecloud." border="false" lightbox="media/identify-flows/relecloud-flow.png":::

### Workload

1.  Relecloud (eCommerce, line-of-business web app)

#### User Flows

1.  Create Upcoming Concerts: call center employees use the application to create an upcoming concert.

    -   Business process(es) this flow supports: while it supports the Purchasing Ticket process, it is in an asynchronous way making this not too critical.
    
    -   The Process owner: Sales Department.
    
    -   The business escalation path: Stakeholders
    
    -   The technical escalation path: Platform and/or Application teams
    
    -   Business expectations: This flow does not require high availability since concerts are created on a weekly basis, proactively.
    
    -   Business impact: If this flow does not meet the expectation of being available when a concert must be created it impacts revenue and reputation.
    
    -   Criticality: Medium

2.  Search Concerts: call center employees use the application to search for upcoming concerts

    -   Business process(es) this flow supports: while it supports the Purchasing Ticket process, application users can opt to simply list concerts if not available.
    
    -   The Process owner: UX (User Experience) Department.
    
    -   The business escalation path: Stakeholders
    
    -   The technical escalation path: Platform and/or Application teams
    
    -   Business expectations: This flow does not require high availability since concerts can be listed as well.
    
    -   Business impact: If this flow does not meet the expectation of being available when a concert must be searched it impacts the application's reputation, degrading the user's experience and user's time in call that can impact productivity.
    
    -   Criticality: Low

3.  Get Upcoming Concerts: call center employees use the application to list the upcoming concerts.

    -   Business process(es) this flow supports: it directly supports the Purchasing Ticket process.
    
    -   The Process owner: Sales Department.
    
    -   The business escalation path: Stakeholders
    
    -   The technical escalation path: Platform and/or Application teams
    
    -   Business expectations: This flow does require high availability since tickets cannot be purchased if it is not working properly.
    
    -   Business impact: If this flow does not meet the expectation of being available, it directly impacts revenue as well as reputation
    
    -   Criticality: High

4.  Purchase Ticket: call center employees use the application (Authentication and authorization) to buy tickets for an upcoming concert (Get Upcoming Concerts) on behalf of Relecloud customers.

    -   Business process(es) this flow supports: this is the core feature and flow of the application.
    
    -   The Process owner: Sales Department.
    
    -   The business escalation path: Stakeholders
    
    -   The technical escalation path: Platform and/or Application teams
    
    -   Business expectations: This flow does require high availability since tickets cannot be purchased if it is not working properly.
    
    -   Business impact: If this flow does not meet the expectation of being available, it directly impacts revenue as well as reputation
    
    -   Criticality: High

5.  Authentication and authorization: call center employees will need to secure sign-in into the application and be provided with proper roles so they can Purchase Tickets.

    -   Business process(es) this flow supports: it directly supports the Purchasing Ticket process since without this functionality call center users can't sign-in into the application to buy tickets.
    
    -   The Process owner: Sales Department.
    
    -   The business escalation path: Stakeholders
    
    -   The technical escalation path: Platform and/or Application teams
    
    -   Business expectations: This flow does require high availability since tickets cannot be purchased if it is not working properly.
    
    -   Business impact: If this flow does not meet the expectation of being available, it directly impacts revenue as well as reputation
    
    -   Criticality: High

#### System Flows

1.  Telemetry Collection: web application and api instances will collect and send information, errors, warning, and more to understand state changes in production system. This will help the operations team with anomalies detection, troubleshooting and profiling.

    -   Business process(es) this flow supports: No business processes are supported by this flow, but it provides highly important data for the operations team
    
    -   The Process owner: Operations team.
    
    -   The business escalation path: Operations team.
    
    -   The technical escalation path: Operations team.
    
    -   Business expectations: This flow requires as much redundancy and resiliency as possible and operations teams should strive to recover it from failure, otherwise they are blind in production.
    
    -   Business impact: If this flow does not meet the expected availability, there is a risk of missing issues in production that could lead to severe impacts
    
    -   Criticality: Medium

## Related links

Learn about determining [business criticality in cloud management](/azure/cloud-adoption-framework/manage/considerations/criticality)
