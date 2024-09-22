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

One of the responsibilities of being a SaaS vendor is to operate your solution on behalf of your customers. It's not sufficient to design and build a solution - you also need to manage it in production. Operating a SaaS solution requires your organization to be set up differently than you might have traditionally, and to take on responsibilities that you might not expect.

This article helps you to consider how your organization's culture, processes, and tooling can support operating a production SaaS solution.

## Understand your responsibilities as a service provider

When you operate SaaS, you become your customers' 24x7 IT and operations department. You need to be ready for that, in terms of staffing, culture, processes, and tooling.

### Design considerations

- **Take responsibility for 24/7/365 support.** Operating SaaS means your organization needs to be set up for responding to incidents, which might happen at any time. You have to have team members available to respond to these incidents. For many SaaS solutions, that responsibility extends beyond business hours, and often means someone needs to be on call 24 hours a day, 7 days a week, 365 days a year.

  *Live-site support* involves real-time monitoring of your production solution, and responding to any incidents or outages whether they're detected by you or a customer. Incidents might affect your system's availability, security, or performance, or they might be problems with deployments of regular updates.
  
  Live-site support requires specific skill sets. It should be done by people who have the skills to analyze the situation and take actions to resolve a situation. Incident response also requires the ability to think clearly and diagnose complex issues while working under pressure. Some engineers may excel at day-to-day engineering tasks but might not be well-suited for this type of work.

  Live-site support can be stressful, and it's important to support your team members who perform incident response.

  If you're new to live-site support, consider how you'll manage the transition. If your team didn't expect to have on-call responsibilities as part of their roles, consider how you'll handle any concerns they might have, including on their compensation and how you'll manage vacations.

- **Identify responsibilities and escalation paths.** Consider how you'll manage support cases and incidents, and how escalations occur.

  In smaller organizations, your engineers might also act as *frontline support*, which means they are the first to respond to support cases opened by customers. This approach can work for a brief period of time, but it rapidly becomes unsustainable as your engineering team finds they're getting interrupted from their regular duties.

  It's common to introduce a frontline support layer to receive support cases. You might use a vendor as your frontline support team. This team typically performs an initial analysis of each issue, and resolves simple issues without escalating to the engineering team at all. For more complex cases, they can collect the necessary information to simplify the engineering team's investigation.

  You might need to introduce the idea of an *on-call engineer*, who is a designated representative of your engineering team who can receive complex cases, investigate, and take action. In larger ISVs, it's common to rotate the on-call responsibilities around the team, with each engineer being on-call for a few days at a time.

  Plan your escalation process to ensure issues are identified and acted upon quickly without interrupting your engineering flow.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Institute a live-site culture in your organization. <br><br> Startups and smaller ISVs might have a lightweight plan for live-site issues, while mature organizations or SaaS providers with enterprise customers (even if the SaaS provider themselves are small) need more structured support and dedicated teams.| A live-site culture ensures that your team members understand their responsibilities, and means you provide them with the necessary skills and tools to support them. |
| Procure or develop tooling to help with incident management, including understanding the state of the system, tracking issues reported by customers, identifying where problems might be happening, escalating to an on-call engineer, handling situations where an engineer doesn't respond in a timely manner, and enabling engineers to make changes to production systems.<br><br>Ensure that anyone who responds to incidents has access to these tools and understand how to use them effectively.|Tooling helps those responding to incidents (your *on-call team*) to quickly identify and resolve problems, while maintaining security and operational control. |
| Invest in good practices for monitoring, deployments, updates, and other regular management operations. | You'll reduce the likelihood of live-site issues occurring by investing in your operational maturity. If an issue does occur, then having those fundamental operations already defined reduces your resolution time because you can use those processes as part of your recovery. |

## Manage incidents as they occur

Major incidents are typically those that affect your customers' ability to use your service. It's important to plan for incidents ahead of time, which helps you to minimize the stress and complexity of dealing with these situations.

### Design considerations

- **Define incident severity.** Different incidents have different levels of importance to you and to your customers. The way you handle a major production outage is likely to be very different to the way you handle a bug with minimal impact on your customer base.

  Define severity levels based on factors like the level of impact that they have on your customers, and set appropriate expectations and timelines for each level.

- **Try to stay calm and think clearly.** Incidents can be stressful, and often there's ambiguity around what's happening. Also, there are often multiple stakeholders putting demands on your attention and asking for information.

  Ensure there's a clear process for who takes the lead within an incident. Triage incidents as best you can, while acknowledging that you might have to operate with imperfect information. Try to remain in control of the situation.
  
  Organizational leaders can help by shielding the team members who are actively investigating or mitigating an incident.

- **Define your escalation processes.** Ensure that your teams are clear about how problems are escalated to different levels of support.

  For many SaaS solutions, the end customer communicates with your frontline support team, who in turn communicates with your engineering team. If you have this type of structure, ensure customers understand who they should interact with, and why it's important that they don't circumvent the processes.

  Ensure that your engineering team understands when and how to communicate with any vendors they might need assistance from, including Microsoft's support team.

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
