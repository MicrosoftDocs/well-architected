---
title: Architecture strategies for designing a reliability testing strategy
description: Learn how to design a reliability testing strategy that focuses on validating and optimizing the reliability of your workload.
author: simipaul
ms.author: simipaul
ms.reviewer: simipaul
ms.date: 04/13/2026
ms.topic: concept-article
---

# Architecture strategies for designing a reliability testing strategy

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|**RE:08**| **Test for resiliency and availability scenarios by applying the principles of chaos engineering.** Ensure that your graceful degradation implementation and scaling strategies are effective by performing reliability testing. |
|---|---|

When you start testing with unit, integration, and security tests, you provide a foundation for reliability. You extend this foundation to validate and optimize the resiliency and availability of the critical flows in your workload by applying reliability testing strategies.

The key strategies in this article build on the foundational testing practices described in [OE:09 Architecture strategies for testing](../operational-excellence/testing.md). We recommend reviewing that article first. The recommendations in this guide are scoped to reliability and focus on achieving resilience and availability targets. 

The following table defines key reliability terms used throughout this article.

**Definitions**

| Term | Definition  |
|---------|---------|
| Availability | The amount of time that an application workload runs in a healthy state without significant downtime. |
| Chaos engineering | The practice of subjecting applications and services to real-world stresses and failures. The goal of chaos engineering is to build and validate resilience to unreliable conditions and missing dependencies. |
| Chaos testing | A testing technique that involves injecting failure to see how the system behaves. |
| Fault injection | The act of introducing an error to a system to test the resiliency of the system. |
| Recoverability | The ability to restore normal operations after a disruption within agreed recovery time (RTO) and recovery point (RPO) targets. |
| Resiliency | The ability of a workload to withstand faults (transient errors, infrastructure outages, demand spikes) and continue operating within an acceptable user experience. |
| Failover |  |
| Failback |  |

//TODO: Chaos engineering, fault injection testing and chaos testing clarification
 
## Define reliability testing scope based on service types

Your reliability testing strategy should account for the shared responsibility model. The level of testing you need depends on how much control you have over each component's reliability behavior.

**Match testing depth to your responsibility.** For infrastructure services (IaaS), your team owns most reliability decisions, so invest in thorough validation through chaos engineering and fault injection. For platform (PaaS) and software (SaaS) services, the provider manages much of the underlying reliability. Focus your testing on how your workload interacts with those services, such as how it responds to throttling, service degradation, or changes in load patterns.

**Test at multiple levels.** Design your testing strategy to cover both individual components and end-to-end flows. A component might be highly available on its own, but the interaction between components can introduce reliability risks that only surface at the system level.

**Account for mixed-service workloads.** When your workload spans multiple service types, testing responsibilities vary across components. For example, you might test failover of your infrastructure components during an availability zone outage, but rely on provider guarantees for a PaaS database that's designed for high availability. Identify where those boundaries are and make sure your testing strategy covers the gaps between them.

## Validate core reliability scenarios

Your reliability testing should cover each critical scenario that can affect your workload's availability and recoverability. Prioritize these scenarios based on your [failure mode analysis](failure-mode-analysis.md) and the potential impact on your customers.

### Backup and restore

Your backup and restore testing strategy should validate that your data protection approach meets your recovery objectives.

**Establish a testing cadence.** Determine how frequently you need to test restores based on how often your backup configuration, data schema, or infrastructure changes. More frequent changes require more frequent restore testing.

**Set recovery targets.** Design tests to measure actual restore times against the RPO and RTO targets which can make sure that your backup strategy meets your recovery objectives.

**Don't assume backup completeness.** Backups can be misconfigured to capture only a subset of your data. Your testing strategy should include validation of data integrity and completeness, not just whether a restore operation succeeds.

**Test in isolation.** Plan to validate restores in an environment separate from production so you can run thorough checks without causing interuptions to live workloads.

### Transient faults

Transient failures, such as momentary network interruptions, brief service unavailability, and connection timeouts, are common reliability risks. Your transient fault testing should validate that your workload handles these failures without impacting users. For more information, see [Recommendations for handling transient faults](handle-transient-faults.md).

**Focus on what you own.** Scope your testing to components that you build and control. If your SDKs or platform services handle retries and circuit breaking automatically, focus your testing on how your application behaves when those built-in mechanisms are exhausted, such as when all retries fail or a circuit breaker opens.

**Validate fault-handling configurations.** Your testing strategy should verify that retry policies, circuit breaker thresholds, and timeout values configurations produce the expected behavior under realistic failure conditions.

**Test the boundary between transient and persistent failure.** Your tests should validate that your workload transitions gracefully from retry behavior to fallback or degradation when a failure persists beyond expected thresholds.

**Account for transient faults from resiliency features.** Good resiliency design, such as zone redundancy, often produces transient faults during normal failover operations. For example, if you have a zone-redundant database and one zone experiences an outage, your workload might see transient connection failures as traffic shifts to a healthy zone.

### Load and scaling response

Your scaling testing strategy should validate that your workload maintains reliability during demand changes, both sudden spikes and gradual increases. For more information, see [scaling strategy](scaling.md). For load and stress testing guidance, see [Recommendations for testing](../performance-efficiency/performance-test.md).

**Test both scale-out and scale-in.** Your testing should cover both scale-out and scale-in scenarios. With this approach, you can verify that new capacity comes online quickly enough and that scaling down doesn't drop requests or leave orphaned resources.

**Account for scaling lag.** There's always a delay between a scaling trigger and new capacity being ready. Your testing should determine whether your workload can handle demand during that gap, or whether you need to pre-provision additional capacity.

### Dependency failures

Your workload likely depends on services outside your direct control, such as third-party APIs, managed platform services, or shared internal services. Your testing should validate that your workload can handle failures in those dependencies without significant disruption to users.

**Categorize dependencies by criticality.** Not all dependencies warrant the same testing investment. Prioritize testing for dependencies that are in your critical flows and that lack built-in redundancy or fallback paths.

**Define acceptable degradation boundaries.** Determine which capabilities your workload must preserve during a dependency failure versus which can be temporarily unavailable. These boundaries inform what your tests should validate.

**Account for partial and cascading failures.** Dependencies rarely fail in binary ways. Don't just test complete outages. Your testing strategy should cover latency increases, intermittent errors, and partial data availability.

**Validate isolation and blast radius containment.** Make sure your testing verifies that a single dependency failure doesn't cascade to unrelated functionality. 

### Self-healing and recovery

Your test should validate how your [self-healing and self-preservation design](self-preservation.md) responds to malfunctions. 

**Test automated recovery end-to-end.** Verify that health checks detect failures accurately, that automated remediation triggers as expected, and that the system restores to a healthy state within acceptable timeframes. 

**Validate manual recovery runbooks.** Automated recovery won't cover every scenario. Test manual runbooks under realistic conditions to make sure operators can execute them under pressure and within your recovery time targets.

### Disaster recovery (DR)

You should test your disaster recovery plans to respond to catastrophic failures and other major incidents that can cause significant downtime. Use a dedicated environment for DR testing to avoid impacting production workloads. For more information, see [disaster recovery plan](disaster-recovery.md).

**Test full failover and failback.** Validate the complete failover sequence, including DNS switchover, data replication consistency, and client reconnection. Testing individual pieces in isolation can miss coordination failures that only appear during a real switchover. Also test failback to the primary region, which is often more complex than the initial switchover. 

**Measure against your targets.** Use your RTO and RPO as pass and fail criteria for every DR test. If you don't meet your targets, analyze the gaps and update your DR plan accordingly.

**Validate people and process.** DR testing should confirm that operators understand the recovery procedures, can locate runbooks quickly, and have the necessary access and permissions to execute them. 

### ToDo table top excercises

## Take advantage of planned and unplanned outages

When your workload is offline due to planned maintenance or an unplanned outage, you have a unique opportunity to perform testing and improve your understanding of your workload. The following sections provide recommendations for each scenario.

### Planned maintenance

When you have planned maintenance windows for updates or patches, you can test components and flows that aren't involved in the maintenance work. Perform tests without the potential risk of unexpectedly degrading the workload or taking it offline altogether. If you have enough time during your maintenance window, you can also test the components and flows that are involved in the maintenance after the maintenance work is complete.

### Unplanned outage

Use every outage incident as an opportunity to learn more about your workload and improve its resiliency by following these steps, ordered by priority:

- Get the workload back online for your customers. To do so, you might perform a workaround for the issue, resolve the issue, or initiate the recovery processes.

- Determine the root cause of the outage and address it. If you can fix the root cause as part of the investigation, document the root cause and the measures that you took to fix it. If the issue requires taking an additional maintenance window at a later time, ensure that your mitigation measures can handle the expected load by thoroughly testing it. Ensure that you have set up sufficient monitoring to cover your mitigation measures.

- If applicable, look for the same issue, or configuration weaknesses that might be affected by similar issues, across all the components in your workload. Use this opportunity to proactively address those components. Consult your incident history to detect patterns of similar issues across your workload.

- Use your findings to improve your testing strategy. Ensure that you have successfully addressed the root cause and similar problems by directly testing the same failure.

## Use fault injection and chaos engineering

Fault-injection testing follows the principles of chaos engineering by highlighting the workload's ability to react to component failures. Perform fault-injection testing in pre-production and production environments. Apply testing to infrastructure and application layers. Apply the information that you learned [Recommendations for performing failure mode analysis](failure-mode-analysis.md) to ensure that you test only faults that you prioritize and that you have mitigation strategies that address faults. The key guidelines of chaos engineering are:

- **Be proactive.** Don't wait for failures to happen. Try to anticipate failures by conducting chaos experiments to discover and fix issues before they affect your production environment.

- **Embrace failure.** Accept and learn from the failures that occur in your system. See failures as a natural part of complex systems and use them as opportunities to learn and improve your system's reliability.

- **Break the system.** Deliberately inject faults or stress into your system to test its resilience. Simulate real-world failures or disruptions to test and improve your workload's recovery capabilities.

- **Identify and address single points of failure early.** As you test, consult and update your [failure mode analysis](failure-mode-analysis.md) to validate and address faults in your documentation. Apply reliability approaches, like redundancy and segmentation, to increase your workload's availability and minimize downtime.

- **Install guardrails and graceful mitigation.** Implement safety measures, like the Circuit Breaker pattern or the Throttling pattern, to increase availability. Implement graceful degradation approaches that enable business continuity during failures.

- **Minimize the blast radius.** Implement fault isolation strategies to help ensure that, even if a failure occurs, its scope is limited. The system continues to function with minimal effect on your customers.

- **Build immunity.** Use chaos engineering experiments to improve your workload's ability to prevent and recover from failures.

Chaos engineering is an integral part of workload team culture and an ongoing practice, not a short-term tactical effort in response to a single outage. Follow this standard method when you design your chaos experiments:

1. Start with a hypothesis. Each experiment should have a clear goal, like testing a given flow's ability to withstand the loss of a particular component.
1. Measure baseline behavior. Ensure that you have consistent reliability and performance metrics for the flow and components involved in a given experiment to compare with the degraded state when running your experiment.
1. Inject a fault or faults. The experiment should intentionally target specific components that can be recovered quickly and you should have an informed expectation of the effect that the fault injection will cause to help control the experiment's blast radius.
1. Monitor the resulting behavior. Gather telemetry about the individual flow components and the end-to-end flow behavior that the experiment targets to properly understand the effects of the fault. Compare the metrics that you gather with the baseline metrics for a full picture of the fault injection results.
1. Document the process and observations. Keeping detailed records of your experiments will inform the future decisions about the workload design, ensuring that you address the gaps that have been revealed over time.
1. Identify and act on the result. Plan for remediation steps that can be added to your workload backlog as improvements. Ensure that design improvement plans are reviewed and tested in nonproduction environments according to the same processes as other deployments.

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

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Fault-injection testing in production can be disruptive and can potentially cause downtime. Be transparent with stakeholders about this possibility and ensure that you have safeguards in place to terminate experiments and roll back plans to quickly reverse the failures that you introduce. To guard against unintended outages in production, ensure that you plan for sufficient [redundancy](redundancy.md) and that your stakeholders understand the cost tradeoff.

## Azure facilitation

[Azure Test Plans](/azure/devops/test/overview) is a browser-based test management solution that provides all the capabilities required for planned manual testing, user acceptance testing, exploratory testing, and gathering feedback from stakeholders.

[Azure Chaos Studio](/azure/chaos-studio/chaos-studio-overview) is a managed service that uses chaos engineering to help you measure, understand, and improve your cloud application and service resilience. 

[Connection monitor](/azure/network-watcher/connection-monitor-overview) is a feature of [Azure Network Watcher](/azure/network-watcher/network-watcher-overview) that you can use for synthetic monitoring and real-time testing of network connectivity across both Azure and hybrid environments. In chaos engineering scenarios, connection monitor can be used to inject faults into targeted network components and continuously measure key metrics such as latency and packet loss. This capability allows teams to observe how disruptions affect network reliability, both for individual flow components and across end-to-end network paths. To assess the impact of faults and identify areas for improvement, compare connection monitor telemetry with baseline metrics.

## Related links

- [Backup and disaster recovery for Azure applications](../resiliency/backup-and-recovery.md)
- [Checklist for reliability testing](checklist.md)
- [Test applications for availability and resiliency](../resiliency/testing.md)

## Reliability checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Reliability checklist](checklist.md)
