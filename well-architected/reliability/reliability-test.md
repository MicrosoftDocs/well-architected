---
title: Architecture strategies for designing a reliability testing strategy
description: Learn how to design a reliability testing strategy that focuses on validating and optimizing the reliability of your workload.
author: simipaul
ms.author: simipaul
ms.reviewer: simipaul
ms.date: 05/12/2026
ms.topic: concept-article
---

# Architecture strategies for designing a reliability testing strategy

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|**RE:08**| **Test for resiliency and availability scenarios by applying the principles of chaos engineering.** Use reliability testing to verify that your workload can withstand faults, scale under demand, and recover within your defined targets. |
|---|---|

Reliability testing validates that your workload can operate consistently under defined conditions. A well-designed reliability testing strategy helps you identify weaknesses in your architecture, verify your resiliency patterns, and build confidence that your workload performs as expected under adverse conditions.

By applying these strategies, you can catch reliability weaknesses before they reach production, confirm that your workload recovers within its defined targets, and continuously improve your testing as your architecture evolves and incidents reveal new failure modes.

The key strategies in this article build on the foundational testing practices described in [OE:09 Architecture strategies for testing](../operational-excellence/testing.md). Review that article first. The recommendations in this guide are scoped to reliability and focus on achieving resiliency and availability targets. 

The following table defines key reliability terms used throughout this article.

**Definitions**

| Term | Definition  |
|---------|---------|
| Availability | The amount of time that an application workload runs in a healthy state without significant downtime. |
| Chaos engineering | The practice that safely incorporates chaos testing into your team culture, engineering practices, and development lifecycle to drive continuous improvement to system resiliency.|
| Chaos testing | A controlled experiment that validates a specific resilience hypothesis about how a workload should behave under disruptive conditions. |
| Fault injection | A technique to deliberately introduce faults to a component, dependency, or system path. |
| Recoverability | The ability to restore normal operations after a disruption within agreed recovery time (RTO) and recovery point (RPO) targets. |
| Resiliency | The ability of a workload to withstand faults (transient errors, infrastructure outages, demand spikes) and continue operating within an acceptable user experience. |
| Failover | The process of switching to a secondary instance upon the failure of the primary instance. |
| Failback | The process in which you restore operations in the primary region after it's recovered. |
| Error budget | An error budget is the maximum acceptable level of failure for a system over a defined period, derived from Service Level Objectives (SLOs). |
 
## Define reliability testing scope based on service types

When you define the scope of your reliability testing, consider the shared responsibility model of the services you use. Each service type (IaaS, PaaS, SaaS) has different reliability guarantees and different levels of control over failure handling. Your testing strategy should reflect these differences to focus on validating the aspects of reliability that you own.

- **Match testing depth to your responsibility.** For infrastructure services (IaaS), your team owns most reliability decisions, so invest in thorough validation through chaos engineering and fault injection. For platform (PaaS) and software (SaaS) services, the provider manages much of the underlying reliability. Focus your testing on how your workload interacts with those services, such as how it responds to throttling, service degradation, or changes in load patterns.

- **Account for mixed-service workloads.** When your workload spans multiple service types, testing responsibilities vary across components. For example, you might test failover of your infrastructure components during an availability zone outage, but rely on provider guarantees for a PaaS database that's designed for high availability. Identify where those boundaries are and make sure your testing strategy covers the gaps between them.

## Test against your reliability targets end-to-end

Your reliability targets, such as SLOs, RTOs, and RPOs, define the expected behavior of your workload under failure conditions. Use reliability targets as pass and fail criteria across complete critical flows, not just individual components.

- **Validate recovery across the full flow.** A single component might restore within its RTO, but the overall recovery time of the flow can exceed your target when downstream dependencies also need to be restored. Your testing should account for the cumulative recovery time across all components.

- **Define test scope with SLOs and error budgets.** Limit chaos testing to stay within your SLOs. Your error budget represents the investment you can make in fault injection. Use your flow recovery targets to define the boundaries of each test.

## Build reliability scenarios from critical flows and failure modes

Reliability testing requires you to focus on the critical flows in your workload and the failure modes that can affect them. Use your [failure mode analysis](failure-mode-analysis.md) to identify the most impactful failure scenarios and create tests that validate your resiliency and recovery strategies. 

**Prioritize by impact and likelihood.** Not every failure mode warrants the same testing investment. Focus first on scenarios that have the highest potential impact on your customers and the highest likelihood of occurring. Your failure mode analysis should inform this prioritization.

## Validate your fault-tolerance and recovery mechanisms

Your reliability testing should cover the scenarios that are most likely to cause downtime or degrade the user experience. Use these sections to identify the testing strategies for fault-tolerance and recovery.

### Backup and restore

Your backup and restore testing should validate that your data protection approach meets your recovery objectives.

- **Establish a testing cadence.** Determine how frequently you need to test restores based on how often your backup configuration, data schema, or infrastructure changes. More frequent changes require more frequent restore testing.

- **Set recovery targets.** Measure actual restore times against your RPO and RTO targets to confirm your backup strategy meets your recovery objectives.

- **Don't assume backup completeness.** Backups can be misconfigured to capture only a subset of your data. Your testing should include validation of data integrity and completeness, not just whether a restore operation succeeds.

- **Test in isolation.** Plan to validate restores in an environment separate from production so you can run thorough checks without causing interruptions to live workloads.

### Transient faults

Transient failures, such as momentary network interruptions, brief service unavailability, and connection timeouts, are common reliability risks. Your transient fault testing should validate that your workload handles these failures without impacting users. For more information, see [Recommendations for handling transient faults](handle-transient-faults.md).

- **Focus on what you own.** If your SDKs or platform services handle retries and circuit breaking automatically, focus your testing on how your application behaves when those built-in mechanisms are exhausted, such as when all retries fail or a circuit breaker opens.

- **Validate fault-handling configurations.** Your testing should verify that retry policies, circuit breaker thresholds, and timeout values configurations produce the expected behavior under realistic failure conditions.

- **Test the boundary between transient and persistent failure.** Your tests should validate that your workload transitions gracefully from retry behavior to fallback or degradation when a failure persists beyond expected thresholds.

- **Account for transient faults from resiliency features.** Good resiliency design, such as zone redundancy, often produces transient faults during normal failover operations. For example, if you have a zone-redundant database and one zone experiences an outage, your workload might see transient connection failures as traffic shifts to a healthy zone.

### Load and scaling response

Your scaling testing strategy should validate that your workload maintains reliability during demand changes, both sudden spikes and gradual increases. For more information, see [scaling strategy](scaling.md). For load and stress testing guidance, see [Recommendations for testing](../performance-efficiency/performance-test.md).

- **Test both scale-out and scale-in.** Your testing should cover both scale-out and scale-in scenarios. With this approach, you can verify that new capacity comes online quickly enough and that scaling down doesn't drop requests or leave orphaned resources.

- **Account for scaling lag.** There's always a delay between a scaling trigger and new capacity being ready. Your testing should determine whether your workload can handle demand during that gap, or whether you need to pre-provision additional capacity.

### Dependency failures

Your workload likely depends on services outside your direct control, such as third-party APIs, managed platform services, or shared internal services. Your testing should validate that your workload can handle failures in those dependencies without significant disruption to users.

- **Categorize dependencies by criticality.** Not all dependencies warrant the same testing investment. Prioritize testing for dependencies that are in your critical flows and that lack built-in redundancy or fallback paths.

- **Test fallback behavior for each dependency.** When a dependency becomes unavailable, your workload should fall back to an alternative path rather than failing entirely. Test that each dependency's fallback activates correctly and that unrelated functionality continues to work.

- **Account for partial and cascading failures.** Dependencies rarely fail in binary ways. Don't just test complete outages. Your testing should cover latency increases, intermittent errors, and partial data availability.

- **Validate isolation and blast radius containment.** Make sure your testing verifies that a single dependency failure doesn't cascade to unrelated functionality. 

### Self-preservation and recovery

Your testing should validate how your [self-healing and self-preservation design](self-preservation.md) responds to malfunctions, including whether your workload can continue operating in a reduced capacity when full recovery isn't immediate.

- **Test automated recovery end-to-end.** Verify that health checks detect failures accurately, that automated remediation triggers as expected, and that the system restores to a healthy state within acceptable timeframes. 

- **Validate manual recovery runbooks.** Automated recovery won't cover every scenario. Test manual runbooks under realistic conditions to make sure operators can execute them under pressure and within your recovery time targets.

- **Validate graceful degradation behavior.** When a component fails, your workload should degrade gracefully rather than fail completely. Test that your workload can operate in a reduced mode, such as queuing requests for manual review and that the degraded experience is acceptable to users. Confirm that your team knows how to operate the workload in this state and how to restore full functionality.

### Disaster recovery (DR)

Test your disaster recovery plans to ensure they address catastrophic failures and other major incidents that can cause significant downtime. Use a dedicated environment for DR testing to avoid impacting production workloads. For more information, see [disaster recovery plan](disaster-recovery.md).

- **Test full failover and failback.** Testing individual pieces in isolation can miss coordination failures that only appear during a real switchover. Validate the complete failover sequence, including DNS switchover, data replication consistency, and client reconnection. Also test failback to the primary region, which is often more complex than the initial switchover. 

- **Measure against your targets.** If a DR test doesn't meet your RTO or RPO, analyze the gaps and update your DR plan accordingly.

- **Validate people and process.** DR testing should confirm that operators have the necessary access and permissions to execute recovery procedures and can locate DR-specific runbooks quickly under pressure.

#### Test and assess your DR plan with tabletop exercises

Tabletop exercises help you find gaps in your reliability testing strategy before a real incident exposes them. By simulating failure scenarios with your team, you can identify untested conditions and validate that your response procedures work as expected.

- **Simulate realistic incidents.** Walk through a failure scenario, such as a regional outage or a corrupted deployment, and have your team describe the steps they'd take to detect, respond to, and recover from it. These discussions often reveal assumptions about system behavior that haven't been validated through testing.

- **Turn findings into test cases.** Use the gaps and unknowns that surface during the exercise to create new reliability tests. If the team discovers that no one knows how the workload behaves when a specific dependency fails, that's a scenario to add to your testing strategy.

## Take advantage of planned and unplanned outages

When your workload is offline due to planned maintenance or an unplanned outage, you have a unique opportunity to perform testing and improve your understanding of your workload. 

### Planned maintenance

When you have planned maintenance windows for updates or patches, you can test components and flows that aren't involved in the maintenance work. Perform tests without the potential risk of unexpectedly degrading the workload or taking it offline altogether. If you have enough time during your maintenance window, you can also test the components and flows that are involved in the maintenance after the maintenance work is complete.

### Unplanned outage

Every unplanned outage is an opportunity to strengthen your reliability testing strategy. After you restore service and complete your root cause analysis, use your findings to improve your tests.

- **Test your mitigation measures.** If you applied a workaround or temporary fix, validate that it can handle expected load before the permanent fix is in place.

- **Scan for similar weaknesses.** Review other components in your workload for the same configuration or design patterns that contributed to the outage. Add tests for those components before they fail the same way.

## Turn incidents and drills into regression test cases 

Every production incident and DR drill is an opportunity to learn about how your workload fails. Use the specific failure scenarios you experienced to create regression test cases that reproduce the original failure so you can confirm the fix holds over time and catch regressions early.

## Combine different types of tests

When you run different types of tests together, you can reveal reliability issues that aren't visible when each test runs in isolation. A workload might handle a specific load under normal conditions, but the same load causes failures when you introduce a fault. 

- **Reuse existing test infrastructure.** If you already have infrastructure and a test harness for load testing, use it to run chaos tests simultaneously. Combining load and fault injection in the same test run shows how your workload behaves under realistic conditions, where demand and failures occur at the same time.

- **Start in non-production environments.** Reliability testing should begin in lower-risk environments where you can safely explore failure modes. Move to production testing only after you've exhausted the insights available from non-production environments and have appropriate safeguards in place to limit blast radius and roll back quickly.

## Use fault injection and chaos engineering

Fault injection and chaos engineering help you build confidence in your workload's resiliency by deliberately introducing failures and observing how the system responds. Make sure you have mitigation strategies in place before running experiments.

- **Run chaos experiments regularly.** Look for opportunities to inject faults into components and flows that you assume are reliable. As your architecture changes, new failure modes emerge and previous assumptions might no longer hold. Run experiments on a regular cadence to catch regressions, validate new dependencies, and confirm that recent changes didn't introduce weaknesses.

- **Use your failure mode analysis to focus experiments.** Each experiment should target a specific fault from your failure mode analysis and have a clear hypothesis, such as testing a given flow's ability to withstand the loss of a particular component. As experiments reveal new failure modes or undiscovered dependencies, update your failure mode analysis to keep it current.

- **Contain blast radius during experiments.** Target specific components that you can recover quickly and set informed expectations about the effect of each fault injection. If an experiment goes beyond scope or produces unexpected results, stop it. Balance collecting meaningful data with minimizing the effect on users.

- **Measure against baselines.** Establish consistent reliability and performance metrics for the flows and components involved in each experiment. Compare degraded state metrics against these baselines to understand the full effect of the fault and determine whether your resiliency design meets its targets.

- **Feed results back into your testing strategy.** Use experiment outcomes to drive new tests, update recovery plans, and inform remediation backlog items. As unexpected behaviors arise, create targeted tests for those behaviors and design remediation strategies.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Fault-injection testing in production can be disruptive and can potentially cause downtime. Be transparent with stakeholders about this possibility and ensure that you have safeguards in place to terminate experiments and roll back quickly. To guard against unintended outages, plan for sufficient [redundancy](redundancy.md) and make sure your stakeholders understand the cost tradeoff.

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
