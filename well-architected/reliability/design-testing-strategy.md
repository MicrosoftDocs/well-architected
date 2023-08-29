---
title: Recommendations for designing a reliability testing strategy
description: Learn how to design a reliability testing strategy.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing a reliability testing strategy

**Applies to: RE:08**

This guide describes the best practices for designing a reliability testing strategy focusing on validating and optimizing the reliability of your workloads.

### Definitions

|Term |Definition  |
|---------|---------|
|Availability | The amount of time that an application workload runs in healthy state without significant downtime |
|Resiliency | An application workload's ability to withstand and recover from failure modes |
|Recoverability | A synonym for resiliency |
|Chaos engineering | The practice of subjecting applications and services to real-world stresses and failures. The goal is to build and validate resilience to unreliable conditions and missing dependencies |
|Fault injection | The act of introducing an error to a system to test the resiliency of the system |

## Key design strategy 

Reliability testing focuses on the resiliency and availability of your workload, focusing on the critical flows that you have identified when designing your solution. This guide will focus on general testing guidance and guidance specific to fault injection and chaos engineering.

### General testing guidance

-   Regular testing should be performed as part of each major change and, if possible, on a regular basis to validate existing thresholds, targets, and assumptions. While most testing should be performed within the testing and staging environments, it\'s often beneficial to also run a subset of tests against the production system. Plan a 1:1 parity of key test environments with the production environment.

-   Automate testing where possible to ensure consistent test coverage and reproducibility. Automate common testing tasks and integrate them into your build processes. Manually testing software is tedious and susceptible to error, although manual explorative testing can also be conducted. In cases where you need to develop automated testing, manual testing can help you determine the scope of the tests to develop.

-   Adopt a shift-left testing approach to begin resiliency and availability testing early in the development cycle.

-   Agree on a simple-to-understand documentation format to make it easier for everyone to understand the process and the results from every regular test performed.

-   Share the documented results of testing with appropriate teams (operational teams, technology leadership, business stakeholders, and disaster recovery stakeholders). Results should inform the refinement of reliability targets (SLOs, SLAs, RTOs and RPOs).

-   Create a regular testing cadence for your backups, restoring the data to isolated systems to ensure that the backups are valid and that restores are functional.

    -   Document and share recovery time metrics with your disaster recovery stakeholders to ensure that expectations for recovery are set appropriately.

-   Use industry standard deployment testing [procedures](https://learn.microsoft.com/azure/well-architected/mission-critical/mission-critical-deployment-testing) to ensure that you have an automated, predictable, and efficient deployment process.

-   Test the workload's ability to withstand transient failures -- refer to the transient fault handling guide (link to re06-transient-fault-handling) in this series for guidance.

-   Test the ability to respond to changes in load patterns and spikes in usage. This will help you test your scaling strategy (link to re05-scaling). Refer to the performance testing guide (link to PE:06) for recommendations on load and stress testing.

-   Test handling of failures in dependent services or other dependencies through fault injection

-   Test and validate your self-healing and self-preservation design (link to re06-self-preservation) to respond to malfunctions. Test automated and manual recovery operations.

-   Test your disaster recovery plan (link to re08-disaster-recovery) to respond to catastrophic failures and other major incidents.

-   Test your workload's ability to degrade gracefully and minimize the blast radius of component malfunction through fault injection.

### Taking advantage of planned and unplanned outages

When your workload is offline due to planned maintenance or an unplanned outage, you have a unique opportunity to perform testing and improve your understanding of your workload. Take advantage of these scenarios by following these recommendations:

#### Planned maintenance

When you have planned maintenance windows for updates or patches, you can test components and flows not involved with that maintenance work without fear of unexpectedly degrading the workload or taking it offline altogether. You can also test those components and flows involved in the maintenance after your maintenance work has been completed, if your maintenance window has sufficient time allocated.

**Unplanned outage**

Use every outage incident as an opportunity to learn more about your workload and improve your resiliency by following these steps (ordered by priority):

-   Get the workload back online for your customers. This can mean that you have a workaround for the issue, you can resolve the issue, or you have initiated your recovery processes.

-   Determine the root cause of the outage and address it. If you can fix the root cause as part of this investigation, document the root cause and the measures taken to fix it. If the issue is something that will require taking an additional maintenance window at a later time, ensure that your mitigation measures can handle the expected load by thoroughly testing it and ensure that you have setup sufficient monitoring to cover your mitigation measures.

-   If applicable, look for the same issue (or configuration weaknesses that could be impacted by similar issues) across all of the components in your workload. Use this opportunity to address those components proactively. Consult your incident history to detect patterns of similar issues across your workload.

-   With the learnings you have achieved through these steps, you can improve your testing strategy to ensure that you have addressed the root cause and similar issues successfully by directly testing the same failure.

### Fault injection and chaos engineering guidance

Fault injection testing follows the principles of chaos engineering by highlighting the workload's ability to react to component failures and should be applied in a holistic manner: testing should happen in pre-production and production environments and should be applied to infrastructure and application layers. Use the failure mode analysis (link to RE:02) learnings that you have gathered as a pre-requisite to your fault-injection testing strategy to ensure that you are only testing faults that you have prioritized and have mitigation designs that address them. The key guidelines of chaos engineering are:

-   **Be proactive.** Don't wait for failures to happen. Try to anticipate failures by conducting chaos experiments to discover and fix issues before they affect your production environment.

-   **Embrace failure.** Accept and learn from the failures that occur in your system. See failures as a natural part of complex systems and use them as opportunities to learn and improve your system's reliability.

-   **Break the system.** Deliberately inject faults or stress into your system to test its resilience. Simulate real-world failures or disruptions to test and improve your workload's recovery capabilities.

-   **Identify and address single points of failure early.** Consult and update your failure mode analysis (link to re02-failure-mode-analysis) as you test to validate and address faults in your documentation. Apply reliability approaches like redundancy and segmentation to increase your workload's availability and minimize downtime.

-   **Install guardrails and graceful mitigation.** Implement safety measures, such as patterns like \*\*circuit breakers\*\*, and \*\*throttling\*\* to increase availability, and implement graceful degradation approaches that enable business continuity during failures.

-   **Minimize the blast radius.** Implement fault isolation strategies to ensure that, even if a failure occurs, its scope is limited, and the system can continue to function with minimal impact to your users.

-   **Build immunity.** Use chaos engineering experiments to improve your workload's ability to prevent and recover from failures.

Chaos engineering should be an integral part of workload team culture and an ongoing practice, not a short-term tactical effort in response to a single outage. Follow this standard method when designing your chaos experiments:

-   Start with a hypothesis.

-   Measure baseline behavior.

-   Inject a fault or faults.

-   Monitor the resulting behavior.

-   Document the process and observations.

-   Identify and act on the result.

Periodically validating your process, architecture choices, and code has multiple benefits: you can spot technical debt early, integrate new technologies and adapt to changing requirements. By conducting fault-injection experiments, you can confirm that monitoring is in place and alerts are set up, validate your process to assign a directly responsible individual (DRI) who takes ownership of the incident, and ensure that your documentation and investigation processes are up to date. Use the following recommendations and considerations to optimize your chaos testing strategy:

-   Challenge system assumptions. Through your testing, you are looking to improve not only the resilience of the workload, but also your workload design strategies. Look for opportunities to inject faults into components and flows that you assume to be reliable based on past experiences as your past experiences may not be valid in your new workload.

-   Validate change (topology, platform, resources). Without thorough testing, including fault injection testing, you may have an incomplete picture of your workload after changes have been made. For example, you may have inadvertently introduced new dependencies or broken existing dependencies in ways that aren't immediately apparent.

-   Use service-level agreement (SLA) buffers. Ensure that you are limiting your chaos testing to stay within your SLAs to avoid potential reputation and/or financial impacts from outages. Your flow and component recovery targets will help you define the scope of your testing.

-   Establish an error budget as an investment in chaos and fault injection. Your error budget is the difference between achieving 100% of the service-level objective (SLO) and achieving the agreed-upon SLO.

-   Stop the experiment when it goes beyond scope. Unknown results are an expected outcome of chaos experiments. Strive to achieve balance between collecting substantial result data and affecting as few production users as possible.

-   Work closely with development teams to ensure the relevance of the injected failures. Use past incidents or issues as a guide. Examine dependencies and evaluate the results when those dependencies are removed.

-   Identify and document previously undiscovered dependencies between different components within your workload that were revealed through chaos testing.

-   Adjust recovery plans as necessary to account for dependency discoveries made during chaos testing.

-   Use learnings from your experiments and tests as a basis for new experiments and tests. As unexpected behaviors arise, this can inform you about new tests that target those behaviors directly and give you the opportunity to design remediation strategies for them.

## Azure facilitation

[Azure Chaos Studio Preview](https://azure.microsoft.com/services/chaos-studio) is a managed service that uses chaos engineering to help you measure, understand, and improve your cloud application and service resilience.

## Tradeoff

Fault injection testing in production can be disruptive and can potentially cause unintended downtime. Be transparent with stakeholders about this possibility and take careful consideration to ensure that you have safeguards in place to terminate experiments and roll-back plans to quickly reverse the failures that you have introduced.

To guard against causing unintended outages in production, ensure that you have planned for sufficient redundancy (link to re04-redundancy) and your stakeholders understand that there is a cost tradeoff for this.

## Related links

[Test apps for availability and resiliency](https://learn.microsoft.com/azure/well-architected/resiliency/testing)
