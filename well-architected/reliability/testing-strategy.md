---
title: Recommendations for designing a reliability testing strategy
description: Learn how to design a reliability testing strategy that focuses on validating and optimizing the reliability of your workload.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for designing a reliability testing strategy

**Applies to: RE 07**

This guide describes the recommendations for designing a reliability testing strategy to validate and optimize the reliability of your workload. Reliability testing focuses on the resiliency and availability of your workload, specifically the critical flows that you identify when you design your solution. This guide provides general testing guidance and guidance that's specific to fault injection and chaos engineering.

**Definitions**

| Term | Definition  |
|---------|---------|
| Availability | The amount of time that an application workload runs in a healthy state without significant downtime. |
| Resiliency | An application workload's ability to withstand and recover from failure modes. |
| Recoverability | A synonym for resiliency. |
| Chaos engineering | The practice of subjecting applications and services to real-world stresses and failures. The goal of chaos engineering is to build and validate resilience to unreliable conditions and missing dependencies. |
| Fault injection | The act of introducing an error to a system to test the resiliency of the system. |

## Key design strategies

### General testing guidance

- Routinely perform testing to validate existing thresholds, targets, and assumptions. When a major change occurs in your workload, run regular testing. Perform most testing in testing and staging environments. It's also beneficial to run a subset of tests against the production system. Plan a one-to-one parity of key test environments with the production environment.

- Automate testing to help ensure consistent test coverage and reproducibility. Automate common testing tasks and integrate them into your build processes. Manually testing software is tedious and susceptible to error, but you can conduct manual exploratory testing. For cases in which you need to develop automated testing, use manual testing to determine the scope of the tests to develop.

- Adopt a shift-left testing approach to perform resiliency and availability testing early in the development cycle.

- Adapt a simple documentation format, so it's easy for everyone to understand the process and the results of every regular test.

- Share the documented results with the appropriate teams, like operational teams, technology leadership, business stakeholders, and disaster recovery stakeholders. The results should inform the refinement of reliability targets, such as service-level objectives (SLOs), service-level agreements (SLAs), recovery time objectives (RTOs), and recovery point objectives (RPOs).

- Create a regular testing cadence for your backups. Restore the data to isolated systems to help ensure that the backups are valid and that restores are functional.

- Document and share recovery time metrics with your disaster recovery stakeholders to ensure that expectations for recovery are appropriate.

- Use industry standard [deployment testing procedures](/azure/well-architected/mission-critical/mission-critical-deployment-testing) to help ensure that you have an automated, predictable, and efficient deployment process.

- Test your workload's ability to withstand transient failures. For more information, see [Recommendations for handling transient faults](handle-transient-faults.md).

- Test your workload's ability to respond to changes in load patterns and spikes in usage. Use this information to help you test your [scaling strategy](scaling.md). For information about load and stress testing, see [Recommendations for testing](../performance-efficiency/test.md).

- Test how your workload handles failures in dependent services or other dependencies by using fault injection.

- Test and validate how your [self-healing and self-preservation design](self-preservation.md) responds to malfunctions. Test automated and manual recovery operations.

- Test your [disaster recovery plan](disaster-recovery.md) to respond to catastrophic failures and other major incidents.

- Test your workload's ability to degrade gracefully and minimize the blast radius of component malfunction by using fault injection.

### Take advantage of planned and unplanned outages

When your workload is offline due to planned maintenance or an unplanned outage, you have a unique opportunity to perform testing and improve your understanding of your workload. The following sections provide recommendations for each scenario.

#### Planned maintenance

When you have planned maintenance windows for updates or patches, you can test components and flows that aren't involved in the maintenance work. Perform tests without the potential risk of unexpectedly degrading the workload or taking it offline altogether. If you have enough time during your maintenance window, you can also test the components and flows that are involved in the maintenance after the maintenance work is complete.

#### Unplanned outage

Use every outage incident as an opportunity to learn more about your workload and improve its resiliency by following these steps, ordered by priority:

- Get the workload back online for your customers. To do so, you might perform a workaround for the issue, resolve the issue, or initiate the recovery processes.

- Determine the root cause of the outage and address it. If you can fix the root cause as part of the investigation, document the root cause and the measures that you took to fix it. If the issue requires taking an additional maintenance window at a later time, ensure that your mitigation measures can handle the expected load by thoroughly testing it. Ensure that you have set up sufficient monitoring to cover your mitigation measures.

- If applicable, look for the same issue, or configuration weaknesses that might be affected by similar issues, across all the components in your workload. Use this opportunity to proactively address those components. Consult your incident history to detect patterns of similar issues across your workload.

- Use your findings to improve your testing strategy. Ensure that you have successfully addressed the root cause and similar problems by directly testing the same failure.

### Fault-injection and chaos engineering guidance

Fault-injection testing follows the principles of chaos engineering by highlighting the workload's ability to react to component failures. Perform fault-injection testing in pre-production and production environments. Apply testing to infrastructure and application layers. Apply the information that you learned [Recommendations for performing failure mode analysis](failure-mode-analysis.md) to ensure that you test only faults that you prioritize and that you have mitigation strategies that address faults. The key guidelines of chaos engineering are:

- **Be proactive.** Don't wait for failures to happen. Try to anticipate failures by conducting chaos experiments to discover and fix issues before they affect your production environment.

- **Embrace failure.** Accept and learn from the failures that occur in your system. See failures as a natural part of complex systems and use them as opportunities to learn and improve your system's reliability.

- **Break the system.** Deliberately inject faults or stress into your system to test its resilience. Simulate real-world failures or disruptions to test and improve your workload's recovery capabilities.

- **Identify and address single points of failure early.** As you test, consult and update your [failure mode analysis](failure-mode-analysis.md) to validate and address faults in your documentation. Apply reliability approaches, like redundancy and segmentation, to increase your workload's availability and minimize downtime.

- **Install guardrails and graceful mitigation.** Implement safety measures, like the Circuit Breaker pattern or the Throttling pattern, to increase availability. Implement graceful degradation approaches that enable business continuity during failures.

- **Minimize the blast radius.** Implement fault isolation strategies to help ensure that, even if a failure occurs, its scope is limited. The system continues to function with minimal effect on your customers.

- **Build immunity.** Use chaos engineering experiments to improve your workload's ability to prevent and recover from failures.

Chaos engineering is an integral part of workload team culture and an ongoing practice, not a short-term tactical effort in response to a single outage. Follow this standard method when you design your chaos experiments:

1. Start with a hypothesis.
1. Measure baseline behavior.
1. Inject a fault or faults.
1. Monitor the resulting behavior.
1. Document the process and observations.
1. Identify and act on the result.

Periodically validate your process, architecture choices, and code to quickly detect technical debt, integrate new technologies, and adapt to changing requirements.

When you conduct fault-injection experiments, you:

- Confirm that monitoring is in place and alerts are set up.
- Validate your process of assigning a directly responsible individual (DRI) to take ownership of an incident.
- Ensure that your documentation and investigation processes are up to date.

Integrate the following recommendations and considerations to optimize your chaos testing strategy:

- Challenge system assumptions. With testing, you try to improve the resiliency of your workload and your workload design strategies. Look for opportunities to inject faults into components and flows that you assume are reliable based on past experiences. They might not be reliable in your new workload.

- Validate change, such as the topology, platform, and resources. Without thorough testing, including fault-injection testing, you might have an incomplete picture of your workload after changes are made. For example, you might inadvertently introduce new dependencies or broken existing dependencies in ways that aren't immediately apparent.

- Use SLA buffers. Limit chaos testing to stay within your SLAs and avoid potential reputation or financial effects from outages. Your flow and component recovery targets help define the scope of your testing.

- Establish an error budget as an investment in chaos and fault injection. Your error budget is the difference between achieving 100 percent of the SLO and achieving the agreed upon SLO.

- Stop the experiment if it goes beyond scope. Unknown results are an expected outcome of chaos experiments. Strive to achieve balance between collecting substantial result data and affecting as few production users as possible.

- Work closely with development teams to ensure the relevance of the injected failures. Use past incidents or issues as a guide. Examine dependencies and evaluate the results when you remove those dependencies.

- Identify and document previously undiscovered dependencies between different components within your workload that are revealed through chaos testing.

- Adjust recovery plans as necessary to account for dependencies that are discovered during chaos testing.

- Use the results from your experiments and tests as the basis for new experiments and tests. As unexpected behaviors arise, new tests might target those behaviors directly and give you the opportunity to design remediation strategies for them.

## Azure facilitation

[Azure Test Plans](https://learn.microsoft.com/en-us/azure/devops/test/overview?view=azure-devops) is an easy-to-use, browser-based test management solution that provides all the capabilities required for planned manual testing, user acceptance testing, exploratory testing, and gathering feedback from stakeholders.

[Azure Chaos Studio Preview](https://azure.microsoft.com/services/chaos-studio) is a managed service that uses chaos engineering to help you measure, understand, and improve your cloud application and service resilience.

## Tradeoffs

Fault-injection testing in production can be disruptive and can potentially cause downtime. Be transparent with stakeholders about this possibility and ensure that you have safeguards in place to terminate experiments and roll back plans to quickly reverse the failures that you introduce.

To guard against unintended outages in production, ensure that you plan for sufficient [redundancy](redundancy.md) and that your stakeholders understand the cost tradeoff.

## Related links

- [Backup and disaster recovery for Azure applications](../resiliency/backup-and-recovery.md)
- [Checklist for reliability testing](test-checklist.md)
- [Test applications for availability and resiliency](../resiliency/testing.md)
