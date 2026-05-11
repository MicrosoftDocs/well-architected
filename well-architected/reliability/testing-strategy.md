---
title: Architecture strategies for designing a reliability testing strategy
description: Learn how to design a reliability testing strategy that focuses on validating and optimizing the reliability of your workload.
author: simipaul
ms.author: simipaul
ms.reviewer: simipaul
ms.date: 05/11/2026
ms.topic: concept-article
---

# Architecture strategies for designing a reliability testing strategy

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|**RE:08**| **Test for resiliency and availability scenarios by applying the principles of chaos engineering.** Use reliability testing to verify that your workload can withstand faults, scale under demand, and recover within your defined targets. |
|---|---|

When you start testing with unit, integration, and security tests, you provide a foundation for reliability. By applying a reliability testing strategy, you extend this foundation to validate the resiliency and availability of the critical flows in your workload. 

The key strategies in this article build on the foundational testing practices described in [OE:09 Architecture strategies for testing](../operational-excellence/testing.md). We recommend reviewing that article first. The recommendations in this guide are scoped to reliability and focus on achieving resiliency and availability targets. 

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
| Failover | The process of switching to a secondary instance upon the failure of the primary instance. |
| Failback | The process in which you restore operations in the primary region after it's recovered. |
 
## Define reliability testing scope based on service types

When you define the scope of your reliability testing, consider the shared responsibility model of the services you use. Each service type (IaaS, PaaS, SaaS) has different reliability guarantees and different levels of control over failure handling. Your testing strategy should reflect these differences to focus on validating the aspects of reliability that you own.

**Match testing depth to your responsibility.** For infrastructure services (IaaS), your team owns most reliability decisions, so invest in thorough validation through chaos engineering and fault injection. For platform (PaaS) and software (SaaS) services, the provider manages much of the underlying reliability. Focus your testing on how your workload interacts with those services, such as how it responds to throttling, service degradation, or changes in load patterns.

**Account for mixed-service workloads.** When your workload spans multiple service types, testing responsibilities vary across components. For example, you might test failover of your infrastructure components during an availability zone outage, but rely on provider guarantees for a PaaS database that's designed for high availability. Identify where those boundaries are and make sure your testing strategy covers the gaps between them.

## Test against your reliability targets end-to-end

Your reliability targets, such as SLOs, RTOs, and RPOs, define the expected behavior of your workload under failure conditions. Use reliability targets as pass and fail criteria across complete critical flows, not just individual components.

**Validate recovery across the full flow.** A single component might restore within its RTO, but the overall recovery time of the flow can exceed your target when downstream dependencies also need to be restored. Your testing should account for the cumulative recovery time across all components in a critical flow to confirm that end-to-end recovery meets your targets.

## Build reliability scenarios from critical flows and failure modes

Reliability testing requires you to focus on the critical flows in your workload and the failure modes that can affect them. 

Structure your reliability tests around complete critical flows rather than individual components. Use your [failure mode analysis](failure-mode-analysis.md) to identify the most impactful failure scenarios and design tests that validate your resiliency and recovery strategies. 

**Prioritize by impact and likelihood.** Not every failure mode warrants the same testing investment. Focus first on scenarios that have the highest potential impact on your customers and the highest likelihood of occurring. Your failure mode analysis should inform this prioritization.

## Build reliability testing into your development process

Reliability issues are cheapest to fix when you catch them early. Embed reliability-focused testing into your development lifecycle so that failure handling is validated continuously, not just during dedicated testing phases.

**Design for failure scenarios upfront.** During the design phase, identify potential failure scenarios, such as retries, timeouts, circuit breakers, and graceful degradation, and incorporate them into your architecture. When these patterns are part of the design, developers can write targeted tests from the start rather than retrofitting them later.

**Test failure-handling logic in unit tests.** Validate retry logic, timeout behaviors, and exception handling within unit tests that run in your pipeline. These tests give fast feedback on whether your reliability patterns work as expected, before the code reaches higher environments.

## Validate core reliability scenarios

Your reliability testing should cover the scenarios that are most likely to cause downtime or degrade the user experience. The following sections help you think through each scenario and decide what your testing strategy should validate.

### Backup and restore

Your backup and restore testing strategy should validate that your data protection approach meets your recovery objectives.

**Establish a testing cadence.** Determine how frequently you need to test restores based on how often your backup configuration, data schema, or infrastructure changes. More frequent changes require more frequent restore testing.

**Set recovery targets.** Measure actual restore times against your RPO and RTO targets to confirm your backup strategy meets your recovery objectives.

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

### Self-preservation and recovery

Your testing should validate how your [self-healing and self-preservation design](self-preservation.md) responds to malfunctions, including whether your workload can continue operating in a reduced capacity when full recovery isn't immediate.

**Test automated recovery end-to-end.** Verify that health checks detect failures accurately, that automated remediation triggers as expected, and that the system restores to a healthy state within acceptable timeframes. 

**Validate manual recovery runbooks.** Automated recovery won't cover every scenario. Test manual runbooks under realistic conditions to make sure operators can execute them under pressure and within your recovery time targets.

**Validate graceful degradation behavior.** When a component fails, your workload should degrade gracefully rather than fail completely. Test that your workload can operate in a reduced mode, such as queuing requests for manual review and that the degraded experience is acceptable to users. Confirm that your team knows how to operate the workload in this state and how to restore full functionality.

### Disaster recovery (DR)

You should test your disaster recovery plans to respond to catastrophic failures and other major incidents that can cause significant downtime. Use a dedicated environment for DR testing to avoid impacting production workloads. For more information, see [disaster recovery plan](disaster-recovery.md).

**Test full failover and failback.** Testing individual pieces in isolation can miss coordination failures that only appear during a real switchover. Validate the complete failover sequence, including DNS switchover, data replication consistency, and client reconnection. Also test failback to the primary region, which is often more complex than the initial switchover. 

**Measure against your targets.** If a DR test doesn't meet your RTO or RPO, analyze the gaps and update your DR plan accordingly.

**Validate people and process.** DR testing should confirm that operators understand the recovery procedures, can locate runbooks quickly, and have the necessary access and permissions to execute them. 

#### Use tabletop exercises to discover testing gaps

Tabletop exercises help you find gaps in your reliability testing strategy before a real incident exposes them. By simulating failure scenarios with your team, you can identify untested conditions and validate that your response procedures work as expected.

**Simulate realistic incidents.** Walk through a failure scenario, such as a regional outage or a corrupted deployment, and have your team describe the steps they'd take to detect, respond to, and recover from it. These discussions often reveal assumptions about system behavior that haven't been validated through testing.

**Turn findings into test cases.** Use the gaps and unknowns that surface during the exercise to create new reliability tests. If the team discovers that no one knows how the workload behaves when a specific dependency fails, that's a scenario to add to your testing strategy.

## Take advantage of planned and unplanned outages

When your workload is offline due to planned maintenance or an unplanned outage, you have a unique opportunity to perform testing and improve your understanding of your workload. The following sections provide recommendations for each scenario.

### Planned maintenance

When you have planned maintenance windows for updates or patches, you can test components and flows that aren't involved in the maintenance work. Perform tests without the potential risk of unexpectedly degrading the workload or taking it offline altogether. If you have enough time during your maintenance window, you can also test the components and flows that are involved in the maintenance after the maintenance work is complete.

### Unplanned outage

Use every outage incident as an opportunity to learn more about your workload and improve its resiliency by following these steps, ordered by priority:

- Get the workload back online for your customers. To do so, you might perform a workaround for the issue, resolve the issue, or initiate the recovery processes.

- Determine the root cause of the outage and address it. If you can fix the root cause as part of the investigation, document the root cause and the measures that you took to fix it. If the issue requires taking an additional maintenance window at a later time, ensure that your mitigation measures can handle the expected load by thoroughly testing it. Ensure that you have set up sufficient monitoring to cover your mitigation measures.

- If applicable, look for the same issue, or configuration weaknesses that might be affected by similar issues, across all the components in your workload. Use this opportunity to proactively address those components. Consult your incident history to detect patterns of similar issues across your workload.

## Turn incidents and drills into regression test cases 

Every production incident and DR drill is an opportunity to learn about how your workload fails and to improve your testing strategy. Use the specific failure scenarios you experienced to create regression test cases. When you fix a reliability issue, add a test that reproduces the original failure so you can confirm the fix holds over time and catch regressions early. 

## Combine different types of tests

When you run different types of tests together, you can reveal reliability issues that aren't visible when each test runs in isolation. A workload might handle a specific load under normal conditions, but the same load causes failures when you introduce a fault. 

**Reuse existing test infrastructure.** If you already have infrastructure and a test harness for load testing, use it to run chaos tests simultaneously. Combining load and fault injection in the same test run shows how your workload behaves under realistic conditions, where demand and failures occur at the same time.

**Start in non-production environments.** Reliability testing should begin in lower-risk environments where you can safely explore failure modes. Move to production testing only after you've exhausted the insights available from non-production environments and have appropriate safeguards in place to limit blast radius and roll back quickly.

## Use fault injection and chaos engineering

Fault injection and chaos engineering build confidence in your workload's resiliency by deliberately introducing failures and observing how the system responds. Ensure you have mitigation strategies in place before running experiments.

**Treat chaos engineering as an ongoing practice.** Chaos engineering is a cultural commitment, not a one-time response to an outage. Build it into your team's regular cadence so that experiments run continuously as your workload evolves. Periodically revisit your architecture choices and test assumptions to detect regressions, new dependencies, and technical debt.

**Challenge assumptions about reliable components.** Look for opportunities to inject faults into components and flows that you assume are reliable based on past experience. Those assumptions might not hold in your current workload or after recent changes. Topology changes, platform updates, and new resource configurations can introduce dependencies or break existing ones in ways that aren't immediately apparent.

**Use your failure mode analysis to focus experiments.** Each experiment should target a specific fault from your failure mode analysis and have a clear hypothesis, such as testing a given flow's ability to withstand the loss of a particular component. As experiments reveal new failure modes or undiscovered dependencies, update your failure mode analysis to keep it current.

**Scope experiments using SLA buffers and error budgets.** Limit chaos testing to stay within your SLAs and avoid reputation or financial effects from outages. Your error budget, the difference between 100 percent of the SLO and the agreed-upon SLO, represents the investment you can make in fault injection. Use your flow and component recovery targets to define the boundaries of each experiment.

**Contain blast radius during experiments.** Target specific components that can be recovered quickly and set informed expectations about the effect of each fault injection. If an experiment goes beyond scope or produces unexpected results, stop it. Balance collecting meaningful data with minimizing the effect on users.

**Measure against baselines.** Establish consistent reliability and performance metrics for the flows and components involved in each experiment. Compare degraded-state metrics against these baselines to understand the full effect of the fault and determine whether your resiliency design meets its targets.

**Feed results back into your testing strategy.** Use experiment outcomes to drive new tests, update recovery plans, and inform remediation backlog items. As unexpected behaviors arise, create targeted tests for those behaviors and design remediation strategies.

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
