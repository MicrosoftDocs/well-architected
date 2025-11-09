---
title: Incident response design guide for Azure workloads
description: Learn how to design comprehensive incident response processes for Azure workloads, including detection, containment, mitigation, and recovery strategies.
author: PageWriter-MSFT 
ms.author: prwilk 
ms.date: 11/08/2025
ms.topic: conceptual
ms.update-cycle: 1095-days  
---

# Incident management (IcM) design for Azure workloads

An _incident_ is an unplanned event that disrupts, degrades, or threatens to disrupt the normal operation of a system, resulting in customer or business impact. Incidents exist on a spectrum, from transient or localized disruptions to widespread events or disasters.Your incident management (IcM) strategy should scale with severity, from rapid mitigation for minor incidents to coordinated cross-team efforts for major events. Response can also vary depending on the cause, whether hardware or infrastructure failures, resource limits, human errors like failed deployments or misconfigurations, or external factors such as security attacks. Examples include data breaches, regulatory violations, malware, or identity compromises.

Despite the cause, incident response whether operational or security, follows the same playbook: detect, triage, mitigate, communicate, and review. As the IcM architect, don't just focus on dashboards and runbooks. Design operations where people, processes, and tools work together under pressure to restore systems efficiently and without chaos.

This article focuses on the phases of IcM, which includes preparation, active incident response, post-incident review, and ongoing improvement. It also includes example to illustrate the design practices. Before you begin, take some time to review the key strategies and pick the ones that make sense for your business. See [OE:08 Architecture strategies for designing an incident management (IcM) process](../operational-excellence/incident-response.md) for details.

This article doesn't cover disasters that require specialized recovery efforts. Details on handling such scenarios can be found in [Develop a disaster recovery plan for multi-region deployments](./disaster-recovery.md).

## Phase 1: Prepare

Before an incident happens, set up the foundation for effective response by designing observability, defining clear roles and processes, and preparing the tools and resources your teams will need. Document the incident response plan and keep it updated and reviewed regularly.

1. Define the incident data your monitoring stack must provide for the entire workload.

    - Collect end-to-end telemetry, including infrastructure and applications.

    - Enable structured logging for all components to support triage and investigation, and send logs to data sinks for analysis. If required, also forward them to centrally managed sinks.

    - Create dashboards based on the workload health model, showing the metrics and signals your team is responsible for monitoring.

    - Configure actionable alerts that trigger notifications only when thresholds are exceeded and a potential incident is detected. Route alerts automatically to the right teams. For example, tier-1 support gets all alerts, while security engineers receive only security-related ones.

    For more information, see [Recommendations for designing and creating an observability framework](../operational-excellence/observability.md).

1. Define key roles to ensure accountability, decision-making, and effective follow-up during and after incidents. Use these examples as a starting point and adapt them to fit your team structure. 

    | Role | Responsibilities |
    |------|-----------------|
    | **Incident Response Manager** | Owns the incident from detection through resolution and root cause analysis. Ensures processes are followed, decisions are made, and the right people are informed. |
    | **Postmortem Leader** | Leads post-incident reviews, captures lessons learned, produces actionable reports, and ensures findings are applied. |
    | **On-Call Staff** | Follow defined escalation paths and fulfill responsibilities for different incident types, ensuring timely involvement of the right teams. |

1. Define triage procedures to determine the next steps. 

    - Define categories for types of incidents (operational, security, performance, deployment) and severity levels (Critical, High, Medium, Low) applicable to your workload.

    - Document how to evaluate the severity, impact, and urgency of the incident. Consider customer impact, affected systems, and business-critical functions. Based on the severity level, the team will activate the disaster recovery plan for cases that meet disaster-level thresholds, or follow the standard response plan for less severe incidents.

    - Create guidelines for selecting mitigation strategies based on incident severity, such as isolation, rollback, configuration changes, workarounds. 

    - Specify which teams or individuals are responsible for handling the incident based on type and severity. Make sure to include escalation paths for when initial responders cannot resolve the issue.

    - Outline what data must be collected during triage (logs, metrics, user reports, alerts) to support investigation and decision-making. Also include who should have access to that data.

    > [!IMPORTANT]
    > Lock down your emergency credentials, or break-glass accounts, with clear rules: who can use them, when, and exactly how. Pair that with emergency drills and track every run. Define how frequently the team should practice drills. During an incident there's no time to figure it out on the fly. Drills provide adequate practice and refinement opportunities.

1. Define communication processes. Document clear communication and escalation plans so tier-1 support can quickly reach the right teams. Specify appropriate communication channels for internal and external stakeholders, and include on-call schedules and contact details.

1. Define the purpose of an IcM tool and the activities it should capture within a simple workflow: create, acknowledge, mitigate, resolve. The tool's goal is to give teams visibility, track progress, maintain accountability, and ensure consistent incident handling around the clock, centralizing all activities to support live site management and on-call rotations.
            
1. Define containment procedures to stop an incident from spreading or causing further damage while  investigations are in progess. Include information like:

    - Define how to isolate affected systems or services to prevent propagation. For example, disconnecting a compromised server, throttling traffic, or disabling a failing microservice.

    - Include guidelines for limiting the incident's reach to specific users, regions, or components.

    - Identify which roles have authority to take containment actions.

1. Define the criteria that officially mark an incident as closed.

    - Include clear resolution factors. Typically, this means systems and services are operating within SLAs, performance and reliability have returned to acceptable levels, and any immediate mitigation actions have been successfully completed.

    - Include validation checks to ensure the issue is fully resolved. This involves confirming through monitoring tools that the impact has ended and verifying that affected users are no longer experiencing disruption.

    - Include communication requirements when closing an incident. Notify relevant stakeholders, including internal teams, support staff, and if needed, impacted users, and provide a summary of actions taken along with any ongoing work.

    - Include thorough documentation when closing an incident. Record all details in the incident management system, covering the trigger, containment steps, triage decisions, and the final resolution. This documentation should be considered as handoff for root cause analysis (RCA) and postmortem to capture lessons learned.

    > [!IMPORTANT]
    > Design your operations so that only the designated authority, such as the Incident Response Manager, can close an incident. Enforce strict checklists to block premature closure. Skipping steps can leave hidden issues unresolved, turning a "closed" incident into a repeat disaster.

## Detection and assessment

### Act on observability triggers

When you have a well-designed observability platform that monitors for anomalies and automatically alerts on them, you can quickly detect issues and determine their severity. If the issue is deemed an emergency, the plan can be initiated.

In some cases, the support team isn't notified via the observability platform. Customers might report issues to support by using support team communication avenues. Or they might reach out to people that they regularly work with, like account executives or VPs. No matter how the support team is notified, they should always follow the same steps to validate the issue and determine the severity. Deviation from the response plan can add stress and confusion.

### Implement detection mechanisms

To quickly identify issues with workloads, you need robust testing and observability practices. Consider implementing the following detection capabilities:

- **Application performance management tools**: Use tools that can correlate user issues with system changes and provide real-time performance insights.
- **Enhanced logging and instrumentation**: Enable detailed logging that can assist with rapid diagnosis during incident response.
- **Automated monitoring and alerting**: Implement monitoring that can automatically detect anomalies and alert appropriate teams with sufficient context.
- **User feedback channels**: Ensure support staff is trained on how to escalate user-reported issues and route them properly according to your incident response plan. Be ready to respond to user-reported issues immediately, especially during high-risk operations or deployments.

## Decision-making and planning

### Assess incident severity and impact

Once an issue is detected, quickly assess its severity and potential impact. Use your predefined severity classifications to determine the appropriate response level. Consider factors such as:

- Number of users affected
- Business functions impacted
- Security implications
- Regulatory or compliance concerns
- Customer trust and reputation impact

### Select appropriate mitigation strategy

Based on the incident assessment, choose the most appropriate mitigation approach. Your decision should consider multiple factors:

- **Infrastructure type**: Mutable vs. immutable infrastructure affects rollback and update strategies
- **Available bypass mechanisms**: Feature flags, configuration toggles, or routing changes that can quickly isolate problems
- **Fix complexity**: Simple configuration changes vs. complex code modifications require different approaches
- **Business criticality**: Mission-critical workloads may require more conservative approaches
- **Data complexity**: Consider the impact of any data changes that might complicate rollback or recovery procedures
- **System dependencies**: Evaluate how mitigation affects interconnected services and components
- **Resource availability**: Assess whether you have sufficient infrastructure capacity and expertise for different mitigation approaches
- **Regulatory compliance**: Consider any compliance requirements that may limit mitigation options
- **Recovery time objectives**: Balance mitigation speed against thoroughness based on business requirements

Include appropriate approval processes in your decision-making and codify them in a clear decision tree. Define authorization levels for different types of mitigation decisions based on impact scope and business criticality.

## Containment

The first step in issue remediation is to contain the issue to protect the rest of your workload. A containment strategy depends on the type of issue, but it usually involves removing the affected component from the workload flow paths. For example, you might shut down a resource or remove it from network routing paths.

System administrators, engineers, and senior developers should work together to design containment strategies. The containment should limit the blast radius of issues and maintain workload functionality in a degraded state until the issue is resolved. If an affected component needs to be accessible to perform triage, it's vital that its access to the rest of the workload is blocked. As much as possible, you should only access the component via a path that's separated from the workload and the rest of the systems.

## Mitigation implementation

After successful containment, implement the selected mitigation strategy. Common mitigation approaches include:

- **Isolation and rollback**: Revert systems to the last-known-good configuration state when recent changes caused the incident. Ensure the entire workload team agrees on the meaning of *last known good* and consider the complexity of rollback operations, especially when data changes are involved.
- **Traffic redirection and failover**: Redirect traffic away from affected systems to healthy alternatives. This approach quickly restores service availability for infrastructure failures, performance degradation, security incidents, or regional outages.
- **Bypass and workaround solutions**: Use feature flags, configuration changes, or alternative code paths to bypass problematic components. Clearly understand tradeoffs and obtain stakeholder approval for operating in a degraded state.
- **Emergency fixes and patches**: Address issues quickly with targeted fixes following accelerated but safe practices. Balance speed with safety by maintaining essential quality gates while shortening validation periods.

## Triage and resolution

After you successfully contain the issue and implement initial mitigation, begin detailed triage work. The steps that you follow during triage depend on the type of issue. The team for a certain area of workload support should create procedures for incidents that are related to their team. For example, security teams should triage security issues, and they should follow scripts that they develop.

It's important that teams follow well-defined scripts as they work through their triage efforts. These scripts should be step-by-step processes that include rollback processes to undo changes that are ineffective or can cause other issues. Use off-the-shelf log aggregation and analysis tools to efficiently investigate issues that require deep analysis.

After the issue is resolved, follow well-defined processes to safely bring the affected component back into the workload flow paths.

## Communication management

### Standardize status updates during incidents

It's important to have clearly defined communication responsibilities to help minimize chaos during incidents. These responsibilities should establish how the workload team engages with support teams, stakeholders, and emergency response team personnel, like the emergency response manager.

Standardize the cadence that the workload team follows for providing status updates. Ensure that stakeholders are aware of this standard so that they know when to expect updates. Consider implementing:

- **Regular update intervals**: Establish specific timeframes for status updates based on incident severity
- **Communication channels**: Define which channels to use for different audiences (internal teams, external stakeholders, customers)
- **Message templates**: Create standard formats for different types of communications to ensure consistency and completeness
- **Escalation thresholds**: Define when to escalate communications to higher levels of management

If the workload team needs to communicate directly with end users, clarify the type of information and level of detail that are appropriate for sharing with users. Also communicate to the workload team any other requirements that apply to these cases.

## Post-incident activities

### Generate RCA incident reports

The service-level agreements (SLAs) to your customers might dictate that you have to issue RCA reports within a certain time period after the incident is resolved. The incident owner should create the RCA reports. If that's not possible, another person who worked closely with the incident owner can create the RCA reports. This strategy ensures an accurate accounting of the incident.

Typically, organizations have a defined RCA template with guidelines about how information is presented and what kinds of information can or can't be shared. If you need to create your own template and guidelines, ensure that they are reviewed and approved by stakeholders.

### Hold incident postmortems

An impartial individual should lead blameless postmortems. In postmortem sessions, everyone shares their findings from an incident. Each team that was involved in the incident response should be represented by individuals that worked on the incident. Those individuals should come to the session prepared with examples of the areas that were successful and areas that can be improved.

The session isn't a forum for assigning blame for the incident or issues that might have come up during the response. The postmortem leader should leave the session with a clear list of action items that focus on improvement, such as:

- **Improvements to the response plan**: Processes or procedures might need to be reevaluated and rewritten to better capture appropriate actions.

- **Improvements to the observability platform**: Thresholds might need to be reevaluated to catch the specific type of incident earlier, or new monitoring might need to be implemented to catch behavior that wasn't accounted for.

- **Improvements to the workload**: The incident might expose a vulnerability in the workload that must be addressed as a permanent remediation.

Postmortems should follow all incidents, without exception. Every incident is an opportunity for learning. Postmortems can help you identify weaknesses in your response processes, deployment practices, and infrastructure configurations, among many other possibilities.

Postmortems should always be blameless so that individuals who are involved in the incident feel safe when they share their perspectives on what can be improved. Postmortem leaders should follow up with plans for implementing the improvements that have been identified and adding these plans to the workload backlog.

## Operationalization and automation

### Design for automated response capabilities

Ensure that your incident response capabilities can be automated where appropriate and safe. Consider implementing:

- **Automated response triggers**: Configure monitoring systems to automatically initiate containment procedures for well-defined incident types while maintaining human oversight for critical decisions.
- **Pipeline parameterization**: Ensure that your deployment and recovery pipelines can accept distinct versions as parameters so that you can easily deploy last-known-good configurations or implement fixes rapidly.
- **Consistent data plane management**: Maintain consistency with the management and data planes during any recovery operations. Keys, secrets, database state data, and configurations that are specific to resources and policies all need to be in scope and accounted for.
- **Infrastructure scaling automation**: Implement automated scaling capabilities that can adjust resources during incident response to handle traffic redirection or increased load.
- **Self-healing mechanisms**: Where appropriate and safe, implement automated responses to common incident patterns, but always with proper monitoring and override capabilities.

### Test incident response capabilities

Test your entire incident response strategy frequently. Like emergency response plans and disaster recovery plans, your incident response plan is only successful if your team is trained on it and practices it regularly. Consider implementing:

- **Regular response drills**: Simulate different types of incidents to test both technical capabilities and team coordination. Create drill instructions and maintain records of when drills have been performed to track preparedness and identify areas for improvement.
- **Chaos engineering**: Use controlled fault injection to test system resilience and response procedures under realistic conditions.
- **Tabletop exercises**: Practice decision-making and communication without affecting production systems. These exercises help teams understand their roles and responsibilities during different incident scenarios.
- **Cross-team coordination exercises**: Practice coordination between different teams (security, operations, development, communications) to ensure smooth handoffs and clear communication during actual incidents.
- **Escalation pathway testing**: Validate that escalation procedures work correctly and that on-call staff can be reached and respond appropriately.

Test deployments and changes thoroughly in lower environments to detect potential issues before they reach production. This proactive approach helps prevent incidents while also providing opportunities to practice response procedures in a controlled setting.

## Considerations

An overly aggressive response strategy can lead to false alarms or unnecessary escalations. Balance the need for rapid response with the cost of disrupting normal operations.

Similarly, aggressively implementing automatic scaling or other self-healing actions to respond to threshold breaches can lead to unnecessary expenditures and management burden. You might not know the exact thresholds to set for alerting and automatic actions like scaling. Perform testing in lower environments and in production to help you determine the right thresholds for your requirements.

Support team members need to be able to perform their normal duties and also support emergencies. You might need to increase head count to help ensure that the support team is properly staffed and able to carry out all required duties.

Being able to fall back typically means that you need sufficient infrastructure capacity to handle two versions of your workload configuration at the same time. Your workload teams also need to be able to support two versions in production at the same time.

Being able to roll back effectively might involve refactoring elements of your workload. For example, you might need to decouple functions or change your data model.

## Example incident type: Deployment failure response

This section provides guidance for handling deployment failures as a specific type of incident. Like other workload incidents, deployment failures are an inevitable part of a workload lifecycle. With this mindset, you can be proactive by having a well-designed, intentional strategy for dealing with deployment failures. Such a strategy enables your workload team to efficiently mitigate failures with as little impact as possible on your end users.

The absence of such a plan can lead to chaotic and potentially detrimental responses to issues, which can significantly affect team and organizational cohesion, customer trust, and finances.

Occasionally, despite the maturity of your development practices, deployment issues occur. Using safe deployment practices and operating a robust workload supply chain can help you minimize the frequency of failed deployments. But you also need to design a standardized strategy to handle failed deployments when they happen.

When you use a progressive exposure deployment model as your standard practice:

- You have the right foundation for minimizing the effects on your customers or internal users when deployments fail.
- You can mitigate issues efficiently.

A deployment failure mitigation strategy is composed of five broad phases:

1. Detection: To respond to a failed deployment, you must first detect the failure. Detection can take several forms, like failed smoke tests, user reported issues, or alerts that your monitoring platform generates.

1. Decision: You must decide what the best mitigation strategy is for the specific failure type.

1. Mitigation: You perform the identified mitigation action. The mitigation can take the form of a fallback, rollback, roll forward, or the use of a runtime configuration to bypass the offending function.

1. Communication: Stakeholders and affected end users must be made aware of the status as you detect and work through the issue as required by your emergency response plan.

1. Postmortem: Blameless postmortems provide opportunities for the workload team to identify areas for improvement and create plans to apply learnings.

The following sections provide detailed recommendations for these phases. These sections assume that you detect an issue after you deploy your changes to one or more groups of users or systems but before you update all groups in your rollout plan.

### Design failure-detection mechanisms

To quickly identify issues with deployments, you need robust testing and observability practices as they relate to deployments. To help detect anomalies quickly, you can complement your workload monitoring and alerting by taking the following steps:

- Use an application performance management tool.
- Enable logging through instrumentation.

Smoke testing and other quality testing should happen at each phase of your rollout. Successful tests in one deployment group shouldn't influence decisions to test in subsequent groups.

You can implement telemetry that correlates user issues with a deployment phase. Then you can quickly identify which rollout group a particular user is associated with. This approach is especially important for progressive exposure deployments, because you might have multiple configurations running across your user base at any given point in the deployment.

You should be ready to respond to user-reported issues immediately. Whenever practical, deploy your rollout phase during working hours, when you have a full support team available. Ensure support staff is trained on how to escalate deployment issues for proper routing. Escalations should align with your workload emergency response plan.

### Decide on the mitigation strategy

Deciding on an appropriate mitigation strategy for a given deployment issue involves considering many factors, including:

- The type of progressive exposure model that you use. For example, you might use a blue-green model or a canary model.

    If you use a blue-green model, falling back is more practical than rolling back. You can easily shift traffic back to the stack that runs the configuration that's not updated. You can then fix the issue in the problematic environment and try your deployment again at an appropriate time.

- The methods that you have at your disposal for bypassing the issue. Examples include the use of feature flags, environmental variables, or any other type of runtime configuration property that you can toggle on and off.

   Sometimes you can easily bypass an issue by turning off a feature flag or toggling a setting. In this case, you might be able to:

  - Proceed with the rollout.
  - Avoid falling back.
  - Roll back while you fix the offending code.

- The level of effort that's required to implement a fix in the code.

  If the level of effort to fix the code is low, rolling forward by implementing a hot fix is the right choice for certain scenarios.

- The level of criticality for the affected workload.

  Business-critical workloads should always be deployed in a side-by-side manner, like in a blue-green model, to achieve zero-downtime deployments. As a result, falling back is the preferable mitigation strategy for these types of workloads.

- The type of infrastructure that the workload uses—mutable or immutable.

  If your workload is designed to use mutable infrastructure, rolling forward can make sense, because it involves updating infrastructure in place. Conversely, rolling back or falling back can make sense when you use immutable infrastructure.

No matter which choices you make, you should include appropriate approvals in your decision-making process and codify them in your decision tree.

### Implement the mitigation strategy

- **Rollback**: In a rollback scenario, you revert updated systems to the last-known-good configuration state.

  It's important for the entire workload team to agree about the meaning of *last known good*. This expression refers to the last state of the workload that was healthy before the deployment began, which isn't necessarily the prior application version.

  Rolling back can be complex, especially as it relates to data changes. Schema changes can make rolling back risky. Implementing them safely can require considerable planning.

  As a general recommendation, schema updates should be additive. They shouldn't be replacement changes—records shouldn't be replaced with new records. Instead, older records should be deprecated and should coexist with new records until it's safe to remove the deprecated records.

- **Fallback**: In a fallback scenario, the updated systems are removed from the production traffic routing. All traffic is directed to the stack that isn't updated. This low-risk strategy provides a way for you to address the issues in your code without introducing further disruptions.

  With canary deployments, falling back might not be straightforward or even possible, depending on your infrastructure and app design. If you need to perform scaling to handle load on systems that aren't updated, perform that scaling before you fall back.

- **Bypass the offending function**: If you can bypass the issue by using feature flags or another type of runtime configuration property, you might decide that continuing with the rollout is the right strategy for a given issue.

   You should clearly understand the tradeoff of bypassing the function, and you should be able to communicate that tradeoff to stakeholders. Stakeholders should approve the go-forward plan.

   Stakeholders need to determine the length of time that's tolerable for operating in a degraded state. They also need to weigh that determination against your estimate of the time that's needed to fix the offending code and deploy it.

- **Emergency deployment (hot fix)**: If you can address the issue mid-rollout, a hot fix might be the most practical mitigation strategy.

   Like any other code, hot fixes need to go through your safe deployment practices. But with a hot fix, the timeline is considerably accelerated. You need to use a code promotion strategy throughout your environments and check hot fix code at all quality gates.

   You might need to dramatically shorten bake times, and you might need to modify tests to accelerate them. Ensure that you can run full tests on the updated code as soon as possible after deployment. Automating quality assurance testing to a high degree helps make testing efficient in these scenarios.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoffs**:
>
> - Being able to fall back typically means that you need sufficient infrastructure capacity to handle two versions of your workload configuration at the same time. Your workload teams also need to be able to support two versions in production at the same time.
>
> - Being able to roll back effectively might involve refactoring elements of your workload. For example, you might need to decouple functions or change your data model.

### Standardize status updates during deployment incidents

Standardize the cadence that the workload team follows for providing status updates during deployment incidents. Ensure that stakeholders are aware of this standard so that they know when to expect updates about deployment progress and any issues encountered.

If the workload team needs to communicate directly with end users about deployment-related service impacts, clarify the type of information and level of detail that are appropriate for sharing with users. Also communicate to the workload team any other requirements that apply to deployment incident communications.

### Conduct deployment incident postmortems

Postmortems should follow all failed deployments, without exception. Every failed deployment is an opportunity for learning. Postmortems can help you identify weaknesses in your deployment and development processes, evaluate deployment pipeline effectiveness and whether the mitigation strategy selection process worked effectively. 

You also might identify infrastructure scaling response issues or specific weaknesses in code promotion, testing strategies, or safe deployment practices that contributed to the failure.

### Operationalize mitigation processes

Ensure that your deployment pipeline can accept distinct versions as parameters so that you can easily deploy last-known-good configurations.

Maintain consistency with the management and data planes as you roll back or roll forward. Keys, secrets, database state data, and configurations that are specific to resources and policies all need to be in scope and accounted for.

For example, pay attention to the design of your infrastructure scaling in your last-known-good deployment. Determine whether you need to adjust that configuration when you redeploy your code.

Prefer small, frequent changes over infrequent, large changes so that the delta between your new and last-known-good deployments is small.

Perform a failure mode analysis (FMA) on your continuous integration and continuous delivery (CI/CD) pipelines to help identify issues that can complicate mitigations. Like your workload as a whole, your pipelines can be analyzed to identify areas that might be problematic when you attempt a given mitigation type.

Use automated rollback functionality carefully:

- Some CI/CD tools like Azure DevOps have automatic rollback functionality that's built in. Consider using this functionality if it provides tangible value to you.
- You should adopt automatic rollback functionality only after you test your pipeline thoroughly and regularly. Ensure that your pipeline is mature enough to introduce extra issues if an automatic rollback is triggered.
- You need to trust that the automation deploys only necessary changes and that it's triggered only when necessary. Design your triggers carefully to meet these requirements.

Use platform-provided capabilities during rollbacks. For example, backups and point-in-time restores can help with storage and data rollbacks.

If you use virtual machines (VMs) to host your application, it can be helpful to restore your environment to a checkpoint that's immediately before an incident.

Test your entire deployment failure mitigation strategy frequently. Like emergency response plans and disaster recovery plans, your deployment failure plan is only successful if your team is trained on it and practices it regularly.

Chaos engineering and fault injection testing can be effective techniques for testing your deployment mitigation strategy.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Support team members need to be able to perform their normal duties and also support emergencies. You might need to increase head count to help ensure that the support team is properly staffed and able to carry out all required duties.

Test deployments thoroughly when you deploy to lower development environments. This practice helps you detect bugs and misconfigurations before they get to production.

## Example incident type: Security incident

This guide describes the recommendations for implementing a security incident response for a workload. If there's a security compromise to a system, a systematic incident response approach helps to reduce the time that it takes to identify, manage, and mitigate security incidents. These incidents can threaten the confidentiality, integrity, and availability of software systems and data.

Most enterprises have a central security operation team (also known as Security Operations Center (SOC), or SecOps). The responsibility of the security operation team  is to rapidly detect, prioritize, and triage potential attacks. The team also monitors security-related telemetry data and investigates security breaches.

:::image type="content" source="images/incident-response/incident-response.png" alt-text="Conceptual art that shows collaborative approach to mitigate potential and realized risk.":::

However, you also have a responsibility to protect your workload. It's important that any communication, investigation, and hunting activities are a collaborative effort between workload team and SecOps team.

This guide provides recommendations for you and your workload team to help you rapidly detect, triage, and investigate attacks.

**Definitions**

| Term | Definition |
|---|---|
|Alert |A notification that contains information about an incident. |
|Alert fidelity|The accuracy of the data that determines an alert. High-fidelity alerts contain the security context that's needed to take immediate actions. Low-fidelity alerts lack information or contain noise.|
|False positive|An alert that indicates an incident that didn't happen. |
|Incident |An event that indicates unauthorized access to a system.|
|Incident response| A process that detects, responds to, and mitigates risks that are associated with an incident.|
|Triage|An incident response operation that analyzes security issues and prioritizes their mitigation.  |


You and your team perform incident response operations when there's a signal or alert for a potential compromise. High-fidelity alerts contain ample security context that makes it easy for analysts to make decisions. High-fidelity alerts result in a low number of false positives. This guide assumes that an alerting system filters low-fidelity signals and focuses on high-fidelity alerts that might indicate a real incident.

### Designate incident notification contacts

Security alerts need to reach the appropriate people on your team and in your organization. Establish a designated point of contact on your workload team to receive incident notifications. These notifications should include as much information as possible about the resource that's compromised and the system. The alert must include the next steps, so your team can expedite actions.

We recommend that you log and manage incident notifications and actions by using specialized tooling that keeps an audit trail. By using standard tools, you can preserve evidence that might be required for potential legal investigations. Look for opportunities to implement automation that can send notifications based on the responsibilities of accountable parties. Keep a clear chain of communication and reporting during an incident.

Take advantage of security information event management (SIEM) solutions and security orchestration automated response (SOAR) solutions that your organization provides. Alternatively, you can procure incident management tools and encourage your organization to standardize them for all workload teams.

### Investigate with a triage team

The team member that receives an incident notification is responsible for setting up a triage process that involves the appropriate people based on the available data. The triage team, often called the *bridge team*, must agree on the mode and process of communication. Does this incident require asynchronous discussions or bridge calls? How should the team track and communicate the progress of investigations?  Where can the team access incident assets?

Incident response is a crucial reason to keep documentation up to date, like the architectural layout of the system, information at a component level, privacy or security classification, owners, and key points of contact. If the information is inaccurate or outdated, the bridge team wastes valuable time trying to understand how the system works, who's responsible for each area, and what the effect of the event might be.

For further investigations, involve the appropriate people. You might include an incident manager, security officer, or workload-centric leads. To keep the triage focused, exclude people that are outside of the scope of the problem. Sometimes separate teams investigate the incident. There might be a team that initially investigates the issue and tries to mitigate the incident, and another specialized team that might perform forensics for a deep investigation to ascertain wide issues. You can quarantine the workload environment to enable the forensics team to do their investigations. In some cases, the same team might handle the entire investigation.

In the initial phase, the triage team is responsible for determining the potential vector and its effect on the confidentiality, integrity, and availability (also called the *CIA*) of the system.

Within the categories of CIA, assign an initial severity level that indicates the depth of the damage and the urgency of remediation. This level is expected to change over time as more information is discovered in the levels of triage.

In the discovery phase, it's important to determine an immediate course of action and communication plans. Are there any changes to the running state of the system? How can the attack be contained to stop further exploitation? Does the team need to send out internal or external communication, such as a responsible disclosure? Consider detection and response time. You might be legally obligated to report some types of breaches to a regulatory authority within a specific time period, which is often hours or days.

If you decide to shut down the system, the next steps lead to the workload's disaster recovery (DR) process.

If you don't shut down the system, determine how to remediate the incident without affecting the functionality of the system.

### Recover from an incident

Treat a security incident like a disaster. If the remediation requires complete recovery, use proper DR mechanisms from a security perspective. The recovery process must prevent chances of recurrence. Otherwise, recovery from a corrupted backup reintroduces the issue. Redeploying a system with the same vulnerability leads to the same incident. Validate failover and failback steps and processes.

If the system remains functioning, assess the effect on the running parts of the system. Continue to monitor the system to ensure that other reliability and performance targets are met or readjusted by implementing proper degradation processes. Don't compromise privacy due to mitigation.

Diagnosis is an interactive process until the vector, and a potential fix and fallback, is identified. After diagnosis, the team works on remediation, which identifies and applies the required fix within an acceptable period.

Recovery metrics measure how long it takes to fix an issue. In the event of a shutdown, there might be an urgency regarding the remediation times. To stabilize the system, it takes time to apply fixes, patches, and tests, and deploy updates. Determine containment strategies to prevent further damage and the spread of the incident. Develop eradication procedures to completely remove the threat from the environment.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: There's a tradeoff between reliability targets and remediation times. During an incident, it's likely that you don't meet other nonfunctional or functional requirements. For example, you might need to disable parts of your system while you investigate the incident, or you might even need to take the entire system offline until you determine the scope of the incident. Business decision-makers need to explicitly decide what the acceptable targets are during the incident. Clearly specify the person that's accountable for that decision.

### Learn from an incident

An incident uncovers gaps or vulnerable points in a design or implementation. It's an improvement opportunity that's driven by lessons in technical design aspects, automation, product development processes that include testing, and the effectiveness of the incident response process. Maintain detailed incident records, including actions taken, timelines, and findings.

We highly recommended that you conduct structured post-incident reviews, such as root-cause analysis and retrospectives. Track and prioritize the outcome of those reviews, and consider using what you learn in future workload designs.

Improvement plans should include updates to security drills and testing, like business continuity and disaster recovery (BCDR) drills. Use security compromise as a scenario for performing a BCDR drill. Drills can validate how the documented processes work. There shouldn't be multiple incident response playbooks. Use a single source that you can adjust based on the size of the incident and how widespread or localized the effect is. Drills are based on hypothetical situations. Conduct drills in a low-risk environment, and include the learning phase in the drills.

Conduct post-incident reviews, or postmortems, to identify weaknesses in the response process and areas for improvement. Based on the lessons you learn from the incident, update the incident response plan (IRP) and the security controls.

### Define a communication plan

Implement a communication plan to notify users of a disruption and to inform internal stakeholders about the remediation and improvements. Other people in your organization need to be notified of any changes to the workload's security baseline to prevent future incidents.

Generate incident reports for internal use and, if necessary, for regulatory compliance or legal purposes. Also, adopt a standard format report (a document template with defined sections) that the SOC team uses for all incidents. Ensure that every incident has a report associated with it before you close the investigation.

## Example incident type: Live performance issue

This guide describes the best practices for responding to live performance issues. Live performance issues refer to real-time challenges and bottlenecks that can hinder the optimal functioning of a workload. Addressing these issues promptly not only facilitates the immediate detection and rectification of performance hiccups but also ensures that the workload consistently meets its performance benchmarks. Failing to address them can lead to complications, including slowdowns, crashes, and system unresponsiveness, and degrade the user experience. They can also prevent users from completing their tasks efficiently, and, in turn, tarnish the reputation of the organization.

**Definitions**

| Term | Definition|
| --- | --- |
| Data correlation | Aligning logs, metrics, and events from various parts of your workload to pinpoint underlying causes. |
| Root cause analysis | A process for identifying the underlying factors that are responsible for a problem. |
| Self-healing | The ability to automatically repair issues without human intervention. |
| Self-prevention | Implementations within a workload to prevent potential issues and failures. |


When you experience a live performance issue, you need to be prepared with the right data and a plan to respond to the issue. This plan should include clear lines of communication and responsibilities. The primary objective is to implement solutions that facilitate a quick return to regular operations and provide insights from the incident. Integrating preventive measures into your workflow is a pivotal strategy. The goal is to either prevent the same issue from happening again or lessen its effects on performance if it isn't preventable.

### Prepare for issues

The ideal response to live-site performance issues is precise and fast. Precision and speed in performance remediation require preparation. To effectively respond to live performance issues, it's crucial to monitor key performance metrics, identify the root cause of the issues, and implement appropriate solutions or optimizations. To take these steps, you might need to analyze workload logs, conduct performance testing, optimize code or configurations, and scale resources. The following examples outline a few critical areas of preparation:

- *Have accurate architecture diagrams.* Your architecture diagrams should include all components and show how they interact. Visual representation can help identify bottlenecks and single points of failure that can lead to performance degradation or unavailability. Ideally, you catch and remove these issues before they cause problems, but having an up-to-date diagram can help you pinpoint issues in high-stress moments.

- *Check data access.* Data and logs from monitoring processes are critical for responding to performance issues in real time and conducting root cause analyses. But it's important to maintain the integrity and confidentiality of the data. Responding to live-site performance issues often requires access to underlying data that might not be normally accessible. You need to ensure that personnel have access to the data that they need when issues arise. But you should only grant time-restricted, least-privilege access, and you should limit that access to authorized personnel.

- *Set automatic alerts.* Alerts can help you identify and address issues as soon as they occur. Alerts should generate notifications when workload performance deviates from performance baselines. Over time, you should tweak alert configurations to avoid generating too many or too few notifications. The monitoring solutions that you use need to collect enough data to generate alerts. These alerts should align with performance targets and established baselines. You should avoid generating alerts on issues that are relevant to your goals. Examples of alerts include degradations in CPU usage, memory, response times, and database performance.

### Create a triage plan

Creating a triage plan involves devising a structured approach to identify, escalate, analyze, prioritize, and communicate live-site performance issues. A triage plan is a strategy for responding to live performance issues. It ensures that performance disruptions are addressed promptly and effectively, with clear roles and procedures. Most performance issues don't merit disaster recovery protocols, but they can affect workload functionality enough to require triage planning. A well-documented triage plan ensures all team members are aligned and can act swiftly, minimizing the impact on users and workloads. A triage plan should include the following components:

- *Identification and monitoring*: Implement a system to identify and monitor performance issues in real time. You should have a list of the contact information of people who are capable of making decisions or escalating issues to higher levels. The plan should also identify roles and responsibilities. It needs to document which accounts gain access to protected information and for how long.

- *Escalation process*: Define a clear escalation process to ensure that performance issues are escalated to the appropriate teams or individuals in a timely manner. The process definition should include contact information and guidelines for escalating issues.

- *Root cause analysis*: Develop a process for conducting a root cause analysis to identify the underlying cause of each performance issue. The process should involve analyzing logs and performance metrics and conducting diagnostic tests to pinpoint the source of each problem.

- *Prioritization*: Establish a prioritization framework to determine the severity of performance issues and prioritize them based on their effect on the workload and users.

- *Communication*: Create a communication plan to keep stakeholders informed about the status of performance issues and the progress of their resolution. Consider regular updates, status reports, and clear communication channels.

- *Documentation*: Document the triage plan, including all its steps, processes, and best practices. This documentation should be easily accessible to the team members who are involved in responding to performance issues.

### Develop methods to identify and resolve issues

Resolving live performance issues involves identifying and addressing any factors that can cause performance degradation or inefficiencies in a live workload. Data that you collect during monitoring is invaluable when you investigate and resolve performance-related incidents. This data provides a historical record of performance metrics. When you have monitoring data available, you can analyze root causes and identify contributing factors. You should use all relevant monitoring data to understand and fix each performance issue.

#### Use root cause analysis

Root cause analysis requires hypothesis testing. After you review monitoring data, you should list potential causes of the performance issue and test them. To conduct a root cause analysis on a live performance issue, you can follow these steps:

1. *Gather information.* Collect as much information as possible about the performance issue. Examples include error messages, logs, performance metrics, and any other relevant data.

1. *Define the problem.* Clearly define the problem by identifying the symptoms and the effect that the problem has on the workload or users.

1. *Investigate potential causes.* Narrow down the scope of the analysis by identifying the specific component or area of the workload where the performance issue is occurring. Identify potential causes of the performance issue based on the gathered information. This process can involve analyzing code, configuration settings, infrastructure, or external dependencies.

1. *Correlate data.* Dive deeper into the collected data to identify patterns, anomalies, or correlations that might contribute to the performance issue. Data correlation is key to identifying performance issues and causes. It can involve reviewing logs, analyzing performance metrics, and conducting tests.

1. *Test hypotheses.* Formulate hypotheses based on the potential causes that you identify. Conduct tests to validate or refute your hypotheses. You should use a test environment to see whether you can replicate the error.

1. *Implement solutions.* Once you identify a root cause, develop and implement solutions to address the performance issue.

1. *Monitor and validate.* After you implement the solutions, continuously monitor the workload to ensure that the performance issue is resolved. Validate the effectiveness of the solutions by monitoring performance metrics and user feedback.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: The steps of a root cause analysis, such as identifying possible causes, testing hypotheses, and documenting the analysis, can be time consuming. To correlate performance issues, you also need to collect and store data. The required time and infrastructure can add significant work to the operations teams and cost to the workload.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: If you perform a root cause analysis without proper security guardrails, there's a risk that you expose sensitive information when you provide access to logs and data.

#### Engage vendor support

Vendor support can be an essential step when you deal with ongoing performance issues. Vendors have the expertise, tools, resources, and experience to help fix issues with their products. Your support agreement with your supplier determines the level of support a vendor provides.

It's often best to work in parallel with vendors. You should create a plan to have some team members collaborate with vendor support while others continue to triage and fix performance issues. Vendor support teams can also make suggestions on how to help prevent and automate responses to similar events.

You need to have contact information available for your personnel. Vendors might also need access to data to effectively engage in problem-solving. You need to have a plan in place for authenticating and authorizing external or guest accounts to access monitoring data.

### Learn from findings

After you fix a live-site performance issue, you need to review what happened. The goal is to learn from performance issues, not just identify problems. The best way to learn is through documentation. Document each issue and explain how to fix it. If a vendor helped, work with the vendor to enhance your documentation, train your team, and modify your workload accordingly.

The documentation should indicate how to prevent each problem from happening again. One way to avoid recurring problems is to introduce automation to respond to common issues. Automation should add self-healing and self-prevention qualities to a workload. Along with the automation, you can create refined alerts that help you respond early to performance issue indicators.

## Azure facilitation

### Azure facilitation - Incident response

[Azure Monitor](/azure/azure-monitor/overview) is a comprehensive solution for collecting, analyzing, and responding to monitoring data from cloud and on-premises environments. It includes a robust alerting platform that you can configure for [automatic notifications and other actions](/azure/azure-monitor/alerts/action-groups), like automatic scaling and other self-healing mechanisms.

Use Monitor to integrate machine learning. Automate and optimize incident triage and proactive measures. For more information, see [AIOps and machine learning in Monitor](/azure/azure-monitor/logs/aiops-machine-learning).

[Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) is a robust analytics tool that's built into Monitor. You can use Log Analytics to run queries against aggregated logs and gain insights about your workload.

Microsoft offers Azure-related incident readiness training. For more information, see [Introduction to Azure incident readiness](/training/technical-support/intro-to-azure-incident-readiness/) and
[Incident readiness](/services-hub/unified/health/incident-readiness).

#### Related links

- [Recommendations for designing and creating an observability framework](observability.md)
- [Recommendations for designing a reliable monitoring and alerting strategy](../reliability/monitoring-alerting-strategy.md)
- [Recommendations for self-healing and self-preservation](../reliability/self-preservation.md)

Microsoft offers Azure-related incident readiness training. For more information, see [Introduction to Azure incident readiness](/training/technical-support/intro-to-azure-incident-readiness/) and [Incident readiness](/services-hub/unified/health/incident-readiness).

### Azure facilitation - Deployment incident

- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) provides build and release services to support CI/CD of your applications.

- [Azure Test Plans](/azure/devops/test/overview) is a browser-based test management solution that's easy to use. This solution offers capabilities that are required for planned manual testing, user acceptance testing, and exploratory testing. Azure Test Plans also provides a way for you to gather feedback from stakeholders.

- [Azure Monitor](/azure/azure-monitor/overview) is a comprehensive monitoring solution for collecting, analyzing, and responding to monitoring data from your cloud and on-premises environments. Monitor includes a robust alerting platform. You can configure that system for [automatic notifications and other actions](/azure/azure-monitor/alerts/action-groups), like autoscaling and other self-healing mechanisms.

- [Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extension of Monitor that provides application performance monitoring (APM) features.

- [Azure Logic Apps](/azure/logic-apps/manage-logic-apps-with-azure-portal) is a cloud-based platform for running automated workflows that integrate apps, data, services, and systems. You can use Logic Apps to create a new version of your application whenever an update is made to it. Azure maintains a history of the versions and can revert or promote any previous version.

- Many Azure database services provide point-in-time restore functionality that can help you when you need to roll back:

  - [Azure SQL Database](/azure/azure-sql/database/recovery-using-backups)

  - [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/point-in-time-restore)

  - [Azure Cosmos DB](/azure/cosmos-db/continuous-backup-restore-introduction)

  - [Azure Database for MySQL](/azure/mysql/flexible-server/concepts-backup-restore)

  - [Azure Database for PostgreSQL](/azure/postgresql/flexible-server/concepts-backup-restore)

- [Azure Chaos Studio Preview](/azure/chaos-studio/chaos-studio-overview) is a managed service that uses chaos engineering to help you measure, understand, and improve your cloud application and service resilience.

#### Related links

- [Progressive experimentation with feature flags](/devops/operate/progressive-experimentation-feature-flags)
- [Recommendations for designing and creating an observability framework](observability.md)
- [Recommendations for designing an emergency response strategy](emergency-response.md)
- [Recommendations for designing a reliability testing strategy](../reliability/testing-strategy.md)
- [Recommendations for designing a workload development supply chain](workload-supply-chain.md)
- [Recommendations for performing failure mode analysis](../reliability/failure-mode-analysis.md)
- [Recommendations for safe deployment practices](safe-deployments.md)

### Azure facilitation - Security incident

**Microsoft Sentinel** is an SIEM and SOAR solution. It's a single solution for alert detection, threat visibility, proactive hunting, and threat response. For more information, see [What's Microsoft Sentinel?](/azure/sentinel/overview)

Ensure that the Azure enrollment portal includes administrator contact information so security operations can be notified directly via an internal process. For more information, see [Update notification settings](/azure/cost-management-billing/manage/ea-portal-administration#update-notification-settings).

To learn more about establishing a designated point of contact that receives Azure incident notifications from Microsoft Defender for Cloud, see [Configure email notifications for security alerts](/azure/security-center/security-center-provide-security-contact-details).

#### Organizational alignment

Cloud Adoption Framework for Azure provides guidance about incident response planning and security operations. For more information, see [Security operations](/azure/cloud-adoption-framework/secure/overview).

#### Related links

- [Automatically create incidents from Microsoft security alerts](/azure/sentinel/create-incidents-from-alerts)
- [Conduct end-to-end threat hunting by using the hunts feature](/azure/sentinel/hunts)
- [Configure email notifications for security alerts](/azure/security-center/security-center-provide-security-contact-details)
- [Incident response overview](/security/operations/incident-response-overview)
- [Microsoft Azure incident readiness](/services-hub/unified/health/ir-azure)
- [Navigate and investigate incidents in Microsoft Sentinel](/azure/sentinel/investigate-incidents)
- [Security control: Incident response](/security/benchmark/azure/mcsb-incident-response)
- [SOAR solutions in Microsoft Sentinel](/azure/sentinel/automation)
- [Training: Introduction to Azure incident readiness](/training/technical-support/intro-to-azure-incident-readiness/)
- [Update Azure portal notification settings](/azure/cost-management-billing/manage/ea-portal-administration#update-notification-settings)
- [What's an SOC?](https://www.microsoft.com/security/business/security-101/what-is-a-security-operations-center-soc)
- [What's Microsoft Sentinel?](/azure/sentinel/overview)

### Azure facilitation - Live performance issue incident

**Developing methods to identify and resolve issues**: Azure provides several tools to help you respond to live performance issues:

- Azure Monitor is a comprehensive monitoring solution that provides insights into the performance and health of your applications and infrastructure. Monitor offers features such as metrics, logs, alerts, and dashboards to help you monitor and diagnose performance issues.

- Application Insights is an application performance management (APM) service that helps developers and DevOps professionals monitor live applications. It automatically detects performance anomalies, collects application-level logs and events, and provides analytics tools to diagnose issues.

- Log Analytics is a service that collects and analyzes log data from various sources, including applications, virtual machines, and Azure resources. When you use Log Analytics, you can query and analyze log data to gain insights into the performance and behavior of your applications.

#### Related links

- [Recommendations for self-healing and self-preservation](../reliability/self-preservation.md)
