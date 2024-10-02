---
title: Incident management for SaaS workloads on Azure
description: Learn about the incident management considerations for SaaS workloads.
author: johndowns
ms.author: jodowns
ms.date: 09/20/2024
ms.topic: conceptual
---

# Incident management for SaaS workloads on Azure

[!INCLUDE [header_file](includes/temporary-warning.md)]

Independent software vendors (ISVs) for SaaS solutions must operate the solution for their customers, requiring a different organizational setup and a culture that handles unexpected production situations smoothly. As the architect, design management processes and tooling accordingly. 

This article guides you in aligning your organization's culture, processes, and tools to support operating a production SaaS solution.

## Understand your responsibilities as a service provider

Operating a SaaS solution means acting as your customers' 24x7 IT and operations department. You need to be prepared with the right staffing, culture, processes, and tooling.

### Design considerations

- **Take responsibility for 24/7/365 support.**  Operating a SaaS solution requires your organization to be prepared for round-the-clock incident response. This includes having team members available at all times, because incidents can occur outside business hours. 

  *Live-site support* involves real-time monitoring and responding to incidents affecting system availability, security, performance, or deployment issues. Those incidents can be detected by you or by your customers. Handling such incidents requires specific skills, including the ability to analyze and resolve issues under pressure. Not all engineers may be suited for this role.

  Live-site support can be stressful and it's important to support your team members. If the team is new to this responsibility, plan the transition carefully, addressing concerns about on-call duties, compensation, and managing unavailability during incidents.

- **Institute a live-site culture**. Consider how you'll manage support cases and incidents, and how escalations occur. The goal is to ensure that team members understand their responsibilities and have the necessary skills and tools to handle incidents.

  Startups and smaller organizations might have a lightweight plan for live-site issues. Engineers might initially serve as frontline support, responding to customer support cases. Mature organizations with enterprise customers need more structured support with and dedicated teams.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Introduce a frontline layer for handling support cases. <br><br> A vendor can serve as your frontline support team, performing initial issue analysis and resolving simple problems. For complex cases, they gather necessary information to help the engineering team's investigation.| You'll prevent the engineering team from being overburdened with incident handling responsibilities and dealing with interruptions from their regular duties. | 
|Invest in *on-call function* to handle complex cases, investigate, and take action. <br><br> If possible, rotate on-call responsibilities among team members, with each engineer being on-call for a few days at a time.| With well-defined responsibilities and escalation paths, issues are quickly identified and addressed without disrupting your engineering workflow.|
| Procure tools specialized in incident management. <br><br> Ensure all incident responders have access to and understand how to use these tools effectively. <br><br> Select tools that have cababilities to monitor system state, track customer-reported issues, identify problems, escalate to on-call engineers, manage unresponsive engineers, and enable making changes in production. |Tooling enables your on-call team to quickly identify and resolve incidents while maintaining security and operational control. |
| Improve your monitoring, deployments, updates, and other regular management operations. | Investing in operational maturity reduces the likelihood of live-site issues. If issues do occur, having well-defined operations in place shortens resolution time by incorporating these processes into your recovery efforts. |

## Define your response plan

- **Define the escalation path.** Ensure teams understand the escalation process for support issues. In many SaaS solutions, customers contact frontline support, which then communicates with the engineering team. Make sure customers know who to interact with and why they shouldn't bypass processes. Also, ensure your engineering team knows when and how to seek help from vendors, including Microsoft's support team.

- **Define severity levels.** Different incidents vary in importance to you and your customers. Handling a major production outage differs from addressing a minor bug. Define severity levels based on customer impact and set appropriate expectations and timelines for each level.

- **Document information needed for triage**. Keeping documentation up to date is essential for effective incident response. This includes the system's architectural layout, component-level details, privacy or security classifications, owners, and key contacts. Inaccurate or outdated information can cause the bridge team to waste valuable time figuring out system operations, responsibilities, and the potential impact of the incident.

- **Plan for effective communication to customers.** Providing status updates is key in incident management. This helps your customers to understand the nature of an incident and also reduces the volume of support cases from customers who have similar issues.

| Recommendation | Benefit |
|---|---|
|Provide a clear incident reporting process to your customers, such as opening a support case with your frontline support team.|You'll ensure consistency in how you discover and respond to incidents, which reduces time to resolution and avoids information being lost or missed.|
|Publish architectural layout, component-level details, privacy or security classifications, owners, and key contacts.|The triage team will have the information readily available and wil be able to focus on investigations and assessing impact.|
|Use a commercial status page offering instead of building your own.|Creating your own can be time-consuming, and if hosted on your infrastructure, it might be inaccessible during an outage.|
 
## Manage incidents methodically 

Major incidents are typically those that affect your customers' ability to use your service. It's important to plan for incidents ahead of time, which helps you to minimize the stress and complexity of dealing with these situations.

### Design considerations

- **Assing incident severity.** Different incidents have different levels of importance to you and to your customers. The way you handle a major production outage is likely to be very different to the way you handle a bug with minimal impact on your customer base.

  Define severity levels based on factors like the level of impact that they have on your customers, and set appropriate expectations and timelines for each level.

- **Try to stay calm and think clearly.** Incidents can be stressful, and often there's ambiguity around what's happening. Also, there are often multiple stakeholders putting demands on your attention and asking for information.

  Ensure there's a clear process for who takes the lead within an incident. Triage incidents as best you can, while acknowledging that you might have to operate with imperfect information. Try to remain in control of the situation.
  
  Organizational leaders can help by shielding the team members who are actively investigating or mitigating an incident.


- **Plan for effective communication to customers.** Proactive messaging, like a publicly accessible service status page, helps your customers to understand the nature of an incident and also reduces the volume of support cases from customers who have similar issues.

  Communicate in a timely manner. Provide information that customers will need, such as the estimated resolution time. Providing frequent updates as you progress towards a resolution will help maintain customer trust. 
  
  Instead of building your own status page, use a commercial offering. Building your own can be time-consuming, and if you host it on your own infrastructure, the status page might be inaccessible during an outage, too.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Acknowledge that incidents *will* occur, and that you need to prepare for them. Define an incident response plan, including incident severity levels. | You'll avoid trying to plan your response strategy while you're in the middle of your first incident. |
| Ensure your team has the access they need to systems like logs, and access to make production changes through a secure and controlled process. | You'll reduce the time required to restore operations by ensuring that your team isn't wasting time unnecessarily. |
| During an incident, prioritize recovery over discovery. | It's tempting to look for a root cause, but when an incident occurs your first goal is to restore operations as quickly as possible and to minimize the disruption to your customers. You might be able to recover by routing around an affected component or rolling back an update, even if you don't understand what happened yet. |
| Procure or design a solution for proactive communication during outages. | Timely, clear, and frequent updates give your customers confidence and reduces the burden on your frontline support team. |
| Designate a communications manager during an active incident. This might be a single designated person, or you might rotate the responsibility between incidents.|By having one voice for your engineering team, you'll centralize the conversations you have and reduce distractions to other team members. You'll also prevent different information from reaching customers or stakeholders during a chaotic incident.|
| Ensure you have a mission-critical support plan for vendors like Microsoft. | In the event of an outage, you need responsive communications with your platform vendors like Microsoft to help to determine where a problem is and to reduce the duration of the outage. |

## Analyze incidents and communicate with customers

After you've recovered from an incident, it's important to review what happened and how you can learn from it. Any incident should result in remediation actions, which might include technical changes as well as changes to your process or training.

### Design considerations

- **Learn from incidents.** While outages are unfortunate, they provide an opportunity to learn and improve. Conduct thorough reviews after a production incident, and learn any lessons you can.

  Usually there are multiple causes for a major incident. Whether a technical component or human error is the apparent cause of an outage, consider whether there are other layers of your solution that could have failed too. For example, could operational processes have prevented or detected the issue before it propagated?

- **Communicate with your customers.** Many ISVs provide post-incident communications to their customers. In particular, enterprise customers often expect high-quality post-incident communications.

  Be transparent, and provide sufficient information to help your customers understand the issue and how you mitigated it. However, be conscious of the security and integrity of your solution. Don't share too much internal detail about your solution architecture or components.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Create a process to perform internal post-incident reviews.<br><br>Focus on identifying the reasons that contributed to the problem. Consider technical reasons, how your processes might have contributed to the outage, and how you responded to the incident.<br><br>Make a structured plan to address any items that need remediation, and put clear accountability in place with timelines. Look for similar patterns elsewhere in your solution that might also be at risk of the same issue. | Internal post-incident reviews help you to learn from production outages, and to minimize the risk of similar issues happening again. |
| Publish customer-facing post-incident reviews.<br><br>Customers don't need the same level of detail that you use for your internal review, but provide sufficient detail to help customers understand the issue and how you mitigated it. Don't give away unnecessary internal details or your system architecture.<br><br>Post-incident communications should always be written and published by humans, and should be reviewed by technical and non-technical stakeholders for accuracy and clarity. | You'll help to restore customer confidence, and you'll assure your customers that you have learned from the issue and are taking actions to address any problems you've identified. |
