---
title: Recommendations for monitoring and threat detection
description: Learn how to get information about events, capture information about the workload, and gain awareness of suspicious activities. 
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for monitoring and threat detection

**Applies to: SE 07**

The process of monitoring is fundamentally about getting information about events that have already occurred. Security monitoring is a practice of capturing information at different altitudes of the workload (infrastructure, application, operations) to gain awareness of suspicious activities. The goal is to predict an incident and learn from past events. Monitoring data provides the basis of post-incident analysis of what transpired, helping in incident response and forensic investigations.

Monitoring is an Operational Excellence concept that's applied across all Well-Architected pillars. This guide provides recommendations only from Security perspective. General concepts of monitoring, such as code instrumentation, data collection, analysis, are out of scope for this guide. For information about core monitoring concepts, see [Recommendations for designing and building an observability framework](../operational-excellence/observability.md)

**Definitions** 

| Term | Definition |
|---|---|
| Threat intelligence | |
| Threat detection | |
| Threat prevention | |
| Threat protection | |
| Audit logs | |


## Key design strategies

The main theme of security monitoring is threat detection. The primary objective is to prevent potential security breaches and maintain a secure environment. However, it's equally important to recognize that not all threats can be preemptively blocked. In such instances, monitoring also serves as a mechanism to identify the cause of a security incident that has occurred despite the prevention efforts. 

Monitoring can be approached from various perspectives: 

- **Monitor at various altitudes.** Observing from various _altitudes_ is getting information about user flows, data access, identity, networking, and even the operating system. Each of these areas offers unique insights that can help _identify deviations from expected behaviors_ established against the security baseline. Conversely, continuously monitoring a system and applications over time can _help establish that baseline posture_. For example, you might typically see around 1000 sign-in attempts in your identity system every hour. If your monitoring detects a spike of 50,000 sign-in attempts in a short period, that might indicate an attacker is trying to gain access to your system.

- **Monitor at various scopes of impact**. It's critical to _observe the application and the platform_. If an application user accidentally gets escalated privileges or because of a security breach, and performs actions beyond their designated scope, the impact might just be confined to what actions other users can do. However, if an internal entity compromises a database, the extent of the potential damage is uncertain. 

  If a compromise occurs on the Azure resource side, the impact could be global, affecting all entities that interact with that resource. Therefore, there might be a significant difference in the blast radius or impact scope between these two scenarios. 

- **Use specialized monitoring tools**. It's critical to invest in _specialized tools_ that can continuously look for anomalous behavior which can be indicative of an attack. Most of these tools have _threat intelligence capabilities_ that can do predictive analysis from a large volume of data and known threats. Most tools aren't stateless and have a deep understanding of telemetry with security context.

The tools need to be platform-integrated or at least be platform-aware to get deep signals from the platform and predict with high fidelity. They must be able to generate alerts in a timely manner with enough information to conduct proper triage. Having too many diverse tools can lead to complexity. 

- **Use monitoring for incident response**. Aggregated data, transformed into actionable intelligence, _enables swift and effective reactions_ to these incidents. Monitoring _helps in post-incident activities_. The goal is to collect enough data to analyze and understand what happened. The process of monitoring captures information on past events, for enhancing our reactive capabilities and potentially predicting future incidents.

These sections provide recommended practices keeping in mind the preceding monitoring perspectives.

### Capture data to keep a trail of activities

The objective is to maintain a **comprehensive audit trail** of events that are interesting from a security perspective. Logging is the most common way to capture access patterns, which must done for application as well as the platform. 

For an audit trail, it's essential to **establish the 'what', 'when', and 'who' associated with actions**. This means identifying the specific timeframes when those actions were performed. This should be covered in your threat modeling. To counteract a repudiation threat, one should establish strong logging and auditing systems, which will result in a  record of activities and transactions.

Here are some use cases based on common altitudes:

#### Application user flows

The application should be designed to provide runtime visibility when events occur.  **Identify critical points within your application and establish logging against these points**. For instance, when a user logs into the application, capture the user's identity, source location, and other relevant information. It's important to acknowledge any escalation in user privileges, the actions performed by the user, and whether the user accessed sensitive information in a secure data store. Keep track of activities against the user and the user session.

To facilitate this, code should be **instrumented through structured logging**. This allows for easy and uniform querying and filtering of the logs.

> [!IMPORTANT]
> It's crucial to enforce responsible logging to maintain confidentiality and integrity of your system. Secrets or sensitive data must not appear in logs.  Be aware of leaking PII data, GDPR, and other compliance requirements for capturing this log data.

#### Identity and access monitoring

Maintain a thorough **record of access patterns for the application and modifications to platform resources**. Have robust activity logs and threat detection mechanisms, particularly for identity-related activities, because attackers often attempt to manipulate identities for unauthorized access.

Implement comprehensive logging by **using all available data points**, for example, including client IP address, to differentiate between regular user activity and potential threats from unexpected locations. All logging events should be timestamped that's issued by the server.

**Record all resource access activities**, capturing who is doing what and when. Instances of privilege escalation are a significant data point that should be logged. Actions related to account creation or deletion by the application must also be recorded. This extends to application secrets; monitor who accessed the secrets and when they were rotated.

While logging successful actions is important, **recording failures is necessary from a security perspective**. Document any violations, such as instances where a user attempted an action but encountered an authorization failure, access attempts for non-existent resources, or other actions that seem suspicious.

#### Network monitoring

Monitoring network packets, their sources, destinations, and structures gives visibility into access patterns at the network level.

Your segmentation design should **enable observation points at the boundaries** to monitor what crosses them and log that data. For instance, subnets with Network Security Groups (NSGs) that generate flow logs. Also, firewall logs that shows the flows that were allowed or denied. 

For inbound connection requests, there are access logs. These logs record the source IP addresses initiating requests, the type of request (GET, POST), and all other information that is part of inbound requests.

Capturing DNS flows is a significant requirement for many organizations. For instance, DNS logs can help identify which user or device initiated a particular DNS query. By correlating DNS activity with user/device authentication logs, activities can be tracked down to individual clients. This responsibility often extends to the workload team, especially if they deploy anything that makes DNS requests as part of its operation. DNS traffic analysis is a key aspect of platform security observability.

It's important to monitor unexpected DNS requests or if they're directed towards known command and control endpoints.

> ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: Logging all network activities can result in large amount of data**. Every request from a layer 3 perspective can be recorded in a flow log, which includes every transaction crossing a subnet boundary. Unfortunately, it's not possible to capture only adverse events because they can only be identified post-occurrence. Make strategic decisions about the type of events to capture and for what duration. Without such considerations, managing that data can become overwhelming. There's also a tradeoff on the cost of storing that data.

Because of the tradeoffs, you should consider whether the benefit of network monitoring to your workload is sufficient to justify the costs. If you have a web application solution with a high request volume and your system makes extensive use of managed Azure resources, the cost might outweigh the benefits. On the other hand, if you have a solution that is designed to use virtual machines with a variety of ports and applications, it might be important to capture and analyze network logs.

### Capture system changes

To maintain the integrity of the system you should have an accurate and up-to-date record of the system state. If there are changes, this record can be used to promptly address any issues that arise.

**Build processes should also emit telemetry**. Understanding the security context of events is key - knowing what triggered the build process, who triggered it, and when it was triggered can provide valuable insights.

Track **when resources were created and when they were decommissioned**. This information must be extracted from the platform. This provides valuable insights for resource management and accountability.

**Drift in resource configuration** must be monitored. Any modification to an existing resource needs to be documented. Also keep track of changes that couldn't complete as part of a rollout to a fleet of resources. Logs must capture the specifics of the change and the exact time it occurred. 

Have a comprehensive view from a patching perspective to know whether the system is up-to-date and secure. **Routine update processes need to be monitored** to verify that they have been executed as planned. A security patching process that couldn't complete, should be considered as a vulnerability. You should also maintain an inventory detailing the patch level states or any other granularity required.

**Change detection also applies to the operating system**. This involves tracking whether new services have been added or existing services have been turned off. It also includes monitoring for the addition of new users to the system. There are tools that are specifically designed to target an operating system. They help with context-less monitoring in the sense that they don't target the functionality of the workload. For instance, File Integrity Monitoring is a critical tool that allows us to track changes in our system files. 

Alerts should be set up for these changes, particularly if they aren't expected to change often. 

> [!IMPORTANT] 
> Roll out to production should ensure that alerts are configured on anomalous activity is detected on the application resources and build process.

As part of your test plans, **include validation of logging and alerting** as prioritized test cases. 

### Store, aggregate, and analyze data

The data collected from these monitoring activities must be stored in data sinks where it can be thoroughly **examined, normalized, and correlated**. Security data should be persisted outside the system's own data stores. Monitoring sinks, whether they are localized or central, must outlive the data sources. The **sinks cannot be ephemeral** because sinks are the source for intrusion detection systems.

Networking logs can be verbose and take up storage. **Explore different tiers within storage systems**. Logs can naturally transition to colder storage over time. This approach is beneficial as older flow logs are typically not actively used and are only needed on-demand. This method ensures efficient storage management while making sure historical data can be accessed, when required.

The flows of your workload are typically a composite of multiple logging sources. Monitoring data must be **analyzed intelligently across all those sources**. For instance, your firewall will only block traffic that reaches it. If you have a Network Security Group (NSG) that has already blocked certain traffic, this would not be visible to the firewall. To reconstruct sequence of events, it's necessary to aggregate data from all components that are in flow, and then aggregate data from all flows. It's useful particularly in a post-incident response scenario where you are trying to understand what transpired. Accurate timekeeping is essential. For security purposes, it's critical to have all systems use a network time source so they're always in sync.

A system like Security Information and Event Management (SIEM), allows you to **consolidate security data in a central location** where it can be correlated across various services. It has **built in threat detection** mechanisms. These systems have the capability to **connect to external feeds** to obtain threat intelligence data. Microsoft, for instance, publishes threat intelligence data that can be utilized. Additionally, you have the option to buy threat intelligence feeds from other providers such as Anomali and FireEye. These feeds can provide valuable insights and enhance your security posture. For threats insights from Microsoft, see <https://www.microsoft.com/en-us/security/business/security-insider/>. 

A SIEM system can **generate alerts** based on correlated and normalized data. This becomes a significant resource during the incident response process.

> ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: SIEM systems can be expensive, complex, and require specialized skills. However, in the absence of such an intelligent system, you might need to correlate data on your own. This can be a time-consuming and complex process.

SIEM systems are usually managed by central teams in the organization. If your organization hasn't invested in one yet, consider advocating for this service. This could potentially alleviate the burden of manual log analysis and correlation, allowing for more efficient and effective security management.

There are some cost-effective options provided by Microsoft. Many of Microsoft Defender products provide the alerting functionality of a SIEM system, although without the data aggregation feature. 

By combining several smaller tools, you can emulate some functions of a SIEM system. However, it's important to note that these makeshift solutions may not be able to perform correlation analysis. While these alternatives can be useful, they may not fully replace the functionality of a dedicated SIEM system.

### Detect abuse

**Be proactive in threat detection** and be vigilant for signs of abuse, such as identity brute force attacks on an SSH component or an RDP endpoint. While external threats might generate a lot of noise, especially if the application is exposed to the internet, **internal threats are often a greater concern**. An unexpected brute force attack from a trusted network source or an inadvertent misconfiguration, for instance, should be investigated immediately.

**Keep up with your hardening practices**. Monitoring isn't a substitute for proactively hardening your environment. A larger surface area is prone to more attacks.  Tighten controls as much as practice. Detect and disable unused accounts, remove unused ports, user a web application firewall,  and so on. For more information about hardening techniques, see [Recommendations on security hardening](hardening.md).

**Signature-based detection** can inspect a system in detail. It involves looking for signs or correlations between activities that may indicate a potential attack. A detection mechanism may identify certain characteristics that are indicative of a specific type of attack. It may not always be possible to directly detect the command-and-control mechanism of an attack. However, there are often hints or patterns associated with a particular command and control process. For instance, it might exhibit a certain flow rate from a request perspective, or it might frequently access domains with specific endings.

Detect **anomalous user access patterns** so that you identify and investigate deviations from expected patterns. This involves comparing current user behavior with past behavior to spot any anomalies. While this may not be feasible manually, threat intelligence tools can be trained to perform this task. Invest in **User and Entity Behavior Analytics (UEBA) tools** that collect and analyze user behavior from monitoring data. These tools can often do predictive analysis that map suspicious behaviors to potential types of attack.

**Detect threats in pre-deployment and post-deployment stages**. During the pre-deployment phase, incorporate vulnerability scanning into pipelines and take necessary actions based on the results. Post-deployment, continue to conduct vulnerability scanning. This can be achieved through tools like Defender for Containers, which scans container images. The results should be included in the collected data. For information about secure development practices, see Recommendations on 

Take advantage of the platform-provided detection mechanisms and measures. For instance, Azure Firewall can analyze traffic and block connections to untrusted destinations. Azure provides ways to detect and protect against Distributed Denial of Service (DDoS) attacks. 

## Azure facilitation

Azure Monitor provides observability across your entire environment. You automatically get platform metrics, activity logs, and diagnostics logs from most of your Azure resources with no configuration. The activity logs provide detailed diagnostic and auditing information.

> [!NOTE] 
> Platform logs are not available indefinitely. You'll need to keep them so that you can review them later for auditing purposes or offline analysis. Use Azure Storage Accounts for long-term/archival storage. In Azure Monitor, specify a retention period when you enable diagnostic setting for your resources.

 Set up alerts based on predefined or custom metrics and logs to get notifications when specific security-related events or anomalies are detected.

For more information, see [Azure Monitor documentation - Azure Monitor | Microsoft Learn](/azure/azure-monitor/).

Microsoft Defender for Cloud is has built-in capabilities  for threat detection. It operates on collected data, analyzes logs. Because it's aware of the types of logs generated, it can have built-in rules to make informed decisions. For instance, it checks lists of potentially compromised IP addresses and generate alerts.

Enable built-in threat protection services for Azure resources. For example, enable Microsoft Defender for Azure resources, such as virtual machines, databases, and containers, to detect and protect against known threats.

Defender for Cloud has Cloud Workload Protection Platform (CWPP) capabilities for threat detection of all workload resources.

For more information, see [What is Microsoft Defender for Cloud? - Microsoft Defender for Cloud | Microsoft Learn](/azure/defender-for-cloud/defender-for-cloud-introduction).

Alerts generated by Defender can also feed into SIEM systems. Microsoft Sentinel is the native offering. It uses AI and machine learning to detect and respond to security threats in real-time. It provides a centralized view of security data and facilitates proactive threat hunting and investigation.

For more information, see [What is Microsoft Sentinel? | Microsoft Learn](/azure/sentinel/overview).

Sentinel can also use threat intelligence feeds from various sources. For information, see [Threat intelligence integration in Microsoft Sentinel | Microsoft Learn](/Azure/sentinel/threat-intelligence-integration).

Sentinel has capabilities to analyze user behavior from monitoring data. For information, [Identify advanced threats with User and Entity Behavior Analytics (UEBA) in Microsoft Sentinel | Microsoft Learn](/azure/sentinel/identify-threats-with-entity-behavior-analytics).

From an Azure perspective, Defender and Sentinel work in tandem, despite some overlap in functionality. This collaboration enhances the overall security posture by ensuring comprehensive threat detection and response.

#### Networking

Review all logs (including raw traffic) from your network devices.

Security group logs – [flow logs](/azure/network-watcher/network-watcher-nsg-flow-logging-portal) and diagnostic logs

Azure Network Watcher

Take advantage of the [packet capture](/azure/network-watcher/network-watcher-alert-triggered-packet-capture) feature to set alerts and gain access to real-time performance information at the packet level.

Packet capture tracks traffic in and out of virtual machines. It gives you the capability to run proactive captures based on defined network anomalies including information about network intrusions.

For an example, see [Scenario: Get alerts when VM is sending you more TCP segments than usual](/azure/network-watcher/network-watcher-alert-triggered-packet-capture).

#### Identity

Monitor identity-related risk events on potentially compromised identities and remediate those risks. Review the reported risk events in these ways:

Azure AD reporting. For information, see [users at risk security report](/azure/active-directory/reports-monitoring/concept-user-at-risk) and the [risky sign-ins security report](/azure/active-directory/reports-monitoring/concept-risky-sign-ins).

Use the reporting capabilities of [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection).

Use the Identity Protection risk events API to get programmatic access to security detections by using Microsoft Graph. See [riskDetection](/graph/api/resources/riskdetection) and [riskyUser](/graph/api/resources/riskyuser) APIs.

Azure AD uses adaptive machine learning algorithms, heuristics, and known compromised credentials (username/password pairs) to detect suspicious actions that are related to your user accounts. These username/password pairs come from monitoring public and dark web and by working with security researchers, law enforcement, security teams at Microsoft, and others.

#### Azure DevOps pipelines

DevOps practices are for change management of the workload through continuous integration, continuous delivery (CI/CD). Make sure you add security validation in the pipelines. Follow the guidance described in [Learn how to add continuous security validation to your CI/CD pipeline](/azure/devops/migrate/security-validation-cicd-pipeline).

## Next steps

We recommend that you review the Security checklist to explore other concepts.

> [!div class="nextstepaction"]
> [Security checklist](checklist.md)