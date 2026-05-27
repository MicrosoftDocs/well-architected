---
title: Architecture strategies for designing a reliability testing strategy
description: Learn how to design a reliability testing strategy that focuses on validating and optimizing the reliability of your workload.
author: simipaul
ms.author: simipaul
ms.reviewer: simipaul
ms.date: 05/27/2026
ms.topic: concept-article
---

# Architecture strategies for designing a reliability testing strategy

**Applies to this Azure Well-Architected Framework Reliability checklist recommendation:**

|**RE:08**| **Test for resiliency and availability scenarios by applying the principles of chaos engineering.** Use reliability testing to verify that your workload can withstand faults, scale under demand, and recover within your defined targets. |
|---|---|

Reliability testing catches architectural weaknesses before they cause outages. Without deliberate testing against failure scenarios, you can't know whether your resiliency patterns actually work or whether your workload recovers within its defined targets.

Use the strategies in this article to establish a testing cadence that regularly validates your workload against its failure modes. Evolve your testing as your architecture changes and incidents reveal new weaknesses.

By applying these strategies, you build confidence that your workload can withstand faults, scale under demand, and recover within your RTO and RPO targets. You also create a feedback loop that strengthens your reliability posture over time. 

You strengthen your workload’s reliability when you test for security risks that affect availability, performance issues that make the system unusable, and operational gaps that limit your incident response. The key strategies in this article build on the foundational testing practices described in [OE:09 Architecture strategies for testing](../operational-excellence/testing.md). Review that article first. The recommendations in this guide are scoped to reliability and focus on achieving confidence in your workload's ability to withstand faults and recover within your targets. 

The following table defines key reliability terms used throughout this article.

**Definitions**

| Term | Definition  |
|---------|---------|
| Availability | The amount of time that an application workload runs in a healthy state without significant downtime. |
| Chaos engineering | The practice that safely incorporates chaos testing into your team culture, engineering practices, and development lifecycle to drive continuous improvement to system resiliency.|
| Chaos testing | A controlled experiment that validates a specific resilience hypothesis about how a workload should behave under disruptive conditions. |
| Fault injection | A technique to deliberately introduce faults to a component, dependency, or system path. |
| Recoverability | The ability to restore normal operations after a disruption within agreed recovery time (RTO) and recovery point (RPO) targets. |
| Resiliency | The ability of a workload to withstand faults (such as transient errors, infrastructure outages, demand spikes) and continue operating within an acceptable user experience. |
| Failover | The process of switching to a secondary component upon the failure of the primary component. |
| Failback | The process in which you restore operations in the primary region after it's recovered. |
| Error budget | The maximum acceptable level of failure for a system over a defined period, derived from Service Level Objectives (SLOs). |
 
## Define reliability testing scope based on service types

When you define the scope of your reliability testing, consider the shared responsibility model of the services you use. Each service type (IaaS, PaaS, SaaS) comes with different reliability guarantees and different levels of control over failure handling. Focus your testing on the aspects of reliability that you own.

- **Match testing depth to your responsibility.** For infrastructure services (IaaS), your team owns most reliability decisions, so invest in thorough validation through chaos engineering and fault injection. For platform (PaaS) and software (SaaS) services, the provider manages much of the underlying reliability. Focus on how your workload interacts with those services, such as how it handles infrastructure failovers in PaaS, throttling, service degradation, or changes in load patterns.

- **Account for mixed-service workloads.** When your workload spans multiple service types, testing responsibilities vary across components. You might test failover of your VM-based infrastructure components during an availability zone outage, but rely on provider guarantees for a PaaS database that's designed for high availability. Identify where those boundaries are and make sure your testing covers the gaps between them.

## Test against your reliability targets end-to-end

Your reliability targets, such as SLOs, RTOs, and RPOs, define how your workload should behave under failure conditions. Use them as pass and fail criteria across complete critical flows, not just individual components.

- **Validate recovery across the full flow.** A single component might restore within its RTO, but the overall recovery time can exceed your target when downstream dependencies also need to restore. Account for total recovery time across the entire flow, including the time to detect the issue and respond.

- **Define test scope with SLOs and error budgets.** Your error budget represents the investment you can make in fault injection. Limit chaos testing to stay within your SLOs and use your flow recovery targets to define the boundaries of each test.

## Build reliability scenarios from critical flows and failure modes

Start with the critical flows in your workload and the failure modes that can affect them. Use your [failure mode analysis](failure-mode-analysis.md) to identify the most impactful failure scenarios and build tests that validate your resiliency and recovery strategies.

**Prioritize by impact and likelihood.** Not every failure mode warrants the same testing investment. Focus first on scenarios with the highest potential user impact and the highest likelihood of occurring. Let your failure mode analysis drive this prioritization.

## Validate your fault-tolerance and recovery mechanisms

Focus on scenarios that are most likely to cause downtime or degrade the user experience. Use the strategies in this section to build tests that validate your workload's ability to handle faults and recover effectively.

### Backup and restore

Your backup and restore testing should validate that your data protection approach meets your recovery objectives.

- **Establish a testing cadence.** Determine how frequently you need to test restores based on how often your backup configuration, data schema, or infrastructure changes. More frequent changes require more frequent restore testing.

- **Set recovery targets.** Measure actual restore times against your RPO and RTO targets to confirm your backup strategy meets your recovery objectives.

- **Don't assume backup completeness.** Backups can be misconfigured to capture only a subset of your data. Validate data integrity and completeness, not just whether a restore operation succeeds.

- **Test in isolation.** Validate restores in an environment separate from production so you can run thorough checks without interrupting live workloads.

### Transient faults

Transient failures, such as momentary network interruptions, brief service unavailability, and connection timeouts, are common reliability risks. Validate that your workload handles these failures without impacting users. For more information, see [Recommendations for handling transient faults](handle-transient-faults.md).

- **Focus on what you own.** If your SDKs or platform services handle retries and circuit breaking automatically, test what happens when those built-in mechanisms are exhausted, such as when all retries fail or a circuit breaker opens.

- **Validate fault-handling configurations.** Evaluate your workload’s fault-handling configurations. Verify that retry policies, circuit breaker thresholds, and timeout values behave as expected under realistic failure conditions.

- **Test the boundary between transient and persistent failure.** Validate that your workload transitions gracefully from retry behavior to fallback or degradation when a failure persists beyond expected thresholds.

- **Account for transient faults from resiliency features.** Zone redundancy and similar designs often produce transient faults during normal failover operations. For example, a zone-redundant database might cause transient connection failures as traffic shifts to a healthy zone during an outage. Test whether your workload can handle these expected transient faults without significant user impact.

### Load and scaling response

Validate that your workload maintains reliability during demand changes, both sudden spikes and gradual increases. For more information, see [scaling strategy](scaling.md). For load and stress testing guidance, see [Recommendations for testing](../performance-efficiency/performance-test.md).

- **Test both scale-out and scale-in.** Verify that new capacity comes online quickly enough and that scaling in doesn't drop requests or leave orphaned resources.

- **Account for scaling lag.** There's always a delay between meeting the conditions to trigger scaling and having new capacity ready. Determine whether your workload can handle demand during that gap or whether you need to pre-provision extra capacity.

### Dependency failures

Your workload likely depends on services outside your direct control, such as third-party APIs, managed platform services, or shared internal services. Validate that your workload handles failures in those dependencies without significant disruption to users.

- **Categorize dependencies by criticality.** Not all dependencies warrant the same testing investment. Prioritize testing for dependencies that are in your critical flows and that lack built-in redundancy or fallback paths.

- **Test fallback behavior for each dependency.** When a dependency becomes unavailable, your workload should fall back to an alternative path or behavior rather than failing entirely. Confirm that each fallback activates correctly and that unrelated functionality continues to work.

- **Account for partial and cascading failures.** Dependencies rarely fail in binary ways. Don't just test complete outages. Cover latency increases, intermittent errors, and partial data availability.

- **Validate isolation and blast radius containment.** Verify that a single dependency failure doesn't cascade to unrelated functionality.

### Self-preservation and recovery

Validate how your [self-healing and self-preservation design](self-preservation.md) responds to malfunctions, including whether your workload can continue operating in a reduced capacity when full recovery isn't immediate.

- **Test automated recovery end-to-end.** Evaluate your health models to ensure they include the right checks. Verify that those checks detect failures accurately, trigger automated remediation as expected, and return the system to a healthy state within acceptable timeframes.

- **Validate manual recovery runbooks.** Automated recovery won't cover every scenario. Test manual runbooks under realistic conditions to make sure operators can execute them under pressure and within your recovery time targets.

- **Validate graceful degradation behavior.** When a component fails, your workload should degrade gracefully rather than fail completely. Test that it can operate in a reduced mode, such as queuing requests for manual review, and that the degraded experience is acceptable to users. Confirm that your team knows how to operate the workload in this state and how to restore full functionality.

### Incidents and disaster recovery (DR)

When an incident or disaster occurs, your ability to quickly detect it and respond effectively is critical. Test your plans and processes to make sure they address catastrophic failures and major incidents. Use a dedicated environment for DR testing to avoid impacting production workloads. For more information, see [disaster recovery plan](disaster-recovery.md).

- **Validate incident detection mechanisms.** Simulate incidents to verify that monitoring logs capture the necessary information and that alerts trigger appropriately. For example, test how quickly increased request failure rates are detected and how often monitoring data is sampled.

- **Test incident management processes.** Confirm that your team can follow incident response procedures effectively. For more information, see [incident response](../operational-excellence/incident-response.md#test-the-plan).

- **Test full failover and failback.** Testing individual pieces in isolation can miss coordination failures that only appear during a real switchover. Validate the complete failover sequence, including DNS switchover, restoring backups and data replication consistency, and client reconnection. Also test failback to the primary deployment, which is often more complex than the initial switchover.

- **Validate capacity in the failover environment.** Determine that your failover environment has enough pre-provisioned capacity to handle traffic immediately upon failover without collapsing under load. Test that the environment can sustain operations while scaling mechanisms activate and validate your scaling approach. For more information, see [Load and scaling response](#load-and-scaling-response).

- **Measure against your targets.** If a DR test doesn't meet your RTO or RPO, analyze the gaps and update your DR plan accordingly.

- **Validate people and process.** DR testing should verify communication channels, confirm that operators have the necessary access and permissions to execute recovery procedures, and ensure they can quickly find DR-specific runbooks under pressure.

#### Test and assess your plan with tabletop exercises

Tabletop exercises help you find gaps in your reliability testing before a real incident exposes them. By simulating failure scenarios with your team, you can identify untested conditions and validate that your response procedures work as expected.

- **Simulate realistic incidents.** Walk through a failure scenario, such as a regional outage or a corrupted deployment, and have your team describe the steps they'd take to detect, respond to, and recover from it. These discussions often reveal assumptions about system behavior that haven't been validated through testing.

- **Turn findings into test cases.** Use the gaps and unknowns that surface during the exercise to create new reliability tests. If the team discovers that no one knows how the workload behaves when a specific dependency fails, that's a scenario to add to your testing strategy.

## Take advantage of planned and unplanned outages

When your workload is offline due to planned maintenance or an unplanned outage, you have a unique opportunity to perform testing and improve your understanding of your workload. 

### Planned maintenance

During maintenance windows for updates or patches, test components and flows that aren't involved in the maintenance work. You can run tests without the risk of unexpectedly degrading the workload or taking it offline. If you have enough time, also test the components involved in the maintenance after the work is complete.

### Unplanned outage

Every unplanned outage is an opportunity to strengthen your reliability testing strategy. After you restore service and complete your post incident review, use your findings to improve your tests.

- **Test your mitigation measures.** If you applied a workaround or temporary fix, validate that it can handle expected load before the permanent fix is in place.

- **Scan for similar weaknesses.** Review other components for the same configuration or design patterns that contributed to the outage. Add tests for those components before they fail the same way.

## Combine different types of tests

When you run different types of tests together, you can reveal reliability issues that aren't visible when each test runs in isolation. A workload might handle a specific load under normal conditions, but the same load causes failures when you introduce a fault. 

- **Reuse existing test infrastructure.** If you already have infrastructure and a test harness for load testing, use it to run chaos tests simultaneously. Combining load and fault injection in the same test run shows how your workload behaves under realistic conditions, where demand and failures occur at the same time.

- **Start in non-production environments.** Begin reliability testing in lower-risk environments where you can safely explore failure modes. Move to production testing only after you've exhausted insights from non-production environments and have safeguards in place to limit blast radius and roll back quickly.

## Use fault injection and chaos engineering

Fault injection and chaos engineering help you build confidence in your workload's resiliency by deliberately introducing failures and observing the response. Make sure you have mitigation strategies in place before running experiments.

- **Run chaos experiments regularly.** Evaluate your [test scope](#define-reliability-testing-scope-based-on-service-types). Inject faults into components and flows that you assume are reliable. As your architecture changes, new failure modes emerge and previous assumptions might no longer hold. Run experiments on a regular cadence to catch regressions, validate new dependencies, and confirm that recent changes didn't introduce weaknesses. 

- **Use your failure mode analysis to focus experiments.** Each experiment should target a specific fault or set of faults and have a clear hypothesis, such as testing a given flow's ability to withstand the loss of a particular component. As experiments reveal new failure modes or undiscovered dependencies, update your failure mode analysis to keep it current.

- **Contain blast radius during experiments.** Target components you can recover quickly and set informed expectations about the effect of each fault injection. If an experiment goes beyond scope or produces unexpected results, stop it. Balance collecting meaningful data with minimizing the effect on users.

- **Measure against baselines.** Establish consistent reliability and performance metrics for the flows and components in each experiment. Compare degraded state metrics against these baselines to understand the full effect of the fault and determine whether your resiliency design meets its targets.

- **Feed results back into your testing strategy.** Use experiment outcomes to drive new tests, update recovery plans, and inform remediation backlog items. As unexpected behaviors arise, create targeted tests for those behaviors and design remediation strategies.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Fault-injection testing in production can be disruptive and can potentially cause downtime. Be transparent with stakeholders about this possibility. Put safeguards in place so you can stop experiments and roll back quickly, and stay flexible about when to run tests or avoid them during sensitive periods. To guard against unintended outages, plan for sufficient [redundancy](redundancy.md) and make sure your stakeholders understand the cost tradeoff.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Reliability testing can expand coverage across many failure modes, but you should stop once it no longer provides meaningful value. If you already have a backlog of known reliability issues, prioritize fixing those problems instead of adding more tests.

## Azure facilitation

[Azure Test Plans](/azure/devops/test/overview) is a browser-based test management solution that provides all the capabilities required for planned manual testing, user acceptance testing, exploratory testing, and gathering feedback from stakeholders.

[Azure Chaos Studio](/azure/chaos-studio/chaos-studio-overview) is a managed service that uses chaos testing to help you measure, understand, and improve your cloud application and service resilience. 

[Azure App Testing](/azure/app-testing/overview-what-is-azure-app-testing) is a service that allows you to run functional tests with Playwright Workspaces and performance tests using Azure Load Testing to identify issues in their applications.

[Azure Service Health](/azure/service-health/service-health-planned-maintenance) has a planned maintenance pane which is a dedicated section in the Azure portal that keeps you informed about upcoming maintenance activities. It highlights events that can affect your Azure resources, helping you prepare in advance.

[Connection monitor](/azure/network-watcher/connection-monitor-overview) is a feature of [Azure Network Watcher](/azure/network-watcher/network-watcher-overview) that you can use for synthetic monitoring and real-time testing of network connectivity across both Azure and hybrid environments. In chaos engineering scenarios, connection monitor can be used to inject faults into targeted network components and continuously measure key metrics such as latency and packet loss. This capability allows teams to observe how disruptions affect network reliability, both for individual flow components and across end-to-end network paths. To assess the impact of faults and identify areas for improvement, compare connection monitor telemetry with baseline metrics.

## Related links

- [Backup and disaster recovery for Azure applications](../resiliency/backup-and-recovery.md)
- [Checklist for reliability testing](checklist.md)
- [Test applications for availability and resiliency](../resiliency/testing.md)

## Reliability checklist  

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Reliability checklist](checklist.md)