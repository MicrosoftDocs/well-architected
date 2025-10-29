---
title: Incident Management for SaaS Workloads on Azure
description: Learn about incident management considerations for implementing processes and tools that support a SaaS solution on Azure.
author: arsenvlad
ms.author: prwilk
ms.date: 11/04/2024
ms.topic: concept-article
ms.collection: learn-startups
---

# Incident management for SaaS workloads on Azure

Independent software vendors (ISVs) for software as a service (SaaS) solutions must operate the solution for their customers. Doing so requires an organizational setup and culture that handles unexpected production situations smoothly. As the architect, you must design management processes and tools accordingly. 

This article guides you in aligning your organization's culture, processes, and tools to support incident management of a production SaaS solution.

## Understand your responsibilities as a service provider

Operating a SaaS solution means that you are your customers' 24x7 IT and operations department. You need to be prepared with the right staffing, culture, processes, and tools.

### Design considerations

- **Take responsibility for 24x7x365 support.**  Operating a SaaS solution requires your organization to always be prepared for incident response. This preparation includes always having team members available because incidents can occur outside of business hours.

  *Live-site support* involves real-time monitoring and responding to incidents that affect system availability, security, performance, or deployment. You or your customers can detect those incidents. To handle such incidents, you need specific skills, including the ability to analyze and solve problems under pressure.

  Live-site support can be stressful, and it's important to support your team members. If the team is new to this responsibility, plan the transition carefully. Address concerns about on-call duties, compensation, and managing unavailability during incidents.

    > :::image type="icon" source="../_images/risk.svg"::: **Risk: Skilling and expectation management.** Not all engineers are suited for a 24x7x365 support role. When transitioning a pre-existing team to support a SaaS solution, ensure proper expectations are set and education opportunities are provided.

- **Institute a live-site culture.** Consider how you manage support cases and incidents and how escalations occur. The goal is to ensure that team members understand their responsibilities and have the necessary skills and tools to handle incidents.

  Startups and smaller organizations might have a lightweight plan for live-site problems. Engineers might initially serve as frontline support by responding to customer support cases. Mature organizations, or SaaS providers with enterprise customers, need more structured support and dedicated teams.


    > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Operational excellence and cost.** Managing live-site events can detract from development time for new features or bug fixes. If development velocity is a concern, consider hiring dedicated live-site resources.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Introduce a frontline team for handling support cases. <br><br> For complex cases, this team gathers the information that the engineering team needs for its investigation. A vendor can serve as your frontline support team and perform initial problem analysis and resolve simple problems. | You avoid overburdening the engineering team with incident handling responsibilities and dealing with interruptions to their regular duties. | 
|Invest in an on-call function for engineers to handle complex cases, investigate, and take action. <br><br> If possible, rotate on-call responsibilities among team members, with each engineer being on call for a few days at a time.| With well-defined responsibilities and escalation paths, you can quickly identify and address problems without disrupting your engineering workflow.|
| Procure tools that are specialized for incident management. <br><br> Ensure all responders have access to and understand how to use these tools effectively. <br><br> Select tools that can monitor system state, track customer-reported problems, identify problems, escalate to on-call engineers, manage unresponsive engineers, and enable making changes in production. |Having the right tools helps your on-call team quickly identify and resolve incidents while maintaining security and operational control. |
| Improve your monitoring, deployments, updates, and other regular management operations. | By investing in operational maturity, you reduce the likelihood of live-site problems. If problems do occur, having well-defined operations in place shortens resolution time. |

## Define your response plan

Acknowledge that incidents are inevitable and prepare for them by defining an incident response plan. This proactive approach prevents you from having to devise a response strategy during your first incident.

Plan ahead for major incidents, which typically affect your customers' ability to use your service. This preparation helps minimize stress and complexity when you manage incidents as they occur.

### Design considerations

- **Define the escalation path.** Make sure that teams understand the escalation process for support tasks. In many SaaS solutions, customers contact a frontline support team, which then communicates with the engineering team. Make sure that customers know who to interact with and why they shouldn't bypass these processes. Also, ensure your engineering team knows when and how to seek help from vendors, including support teams at Microsoft.

- **Define severity levels.** Different incidents vary in importance to you and your customers. How you handle a major production outage differs from how you address a minor bug. Define severity levels based on customer impact and set appropriate expectations and timelines for each level.

- **Document information that you need for triage.** Keeping documentation up to date is essential for effective incident response. This documentation includes the system's architectural layout, component-level details, owners, and key contacts. Inaccurate or outdated information can cause the incident response team to waste valuable time figuring out system operations, responsibilities, and the potential impact of the incident.

- **Plan for effective communication to customers.** Providing status updates is key in incident management. Status updates help your customers understand the nature of an incident and also reduce the volume of support cases from customers who experience similar problems.

### Design recommendations

| Recommendation | Benefit |
|---|---|
|Provide a clear incident reporting process, such as opening a support case with your frontline support team, to your customers.|You ensure consistency in how you discover and respond to incidents, which reduces time to resolution and prevents information from being lost or overlooked.|
|Document the architectural layout, component-level details, privacy or security classifications, owners, and key contacts.|The triage team has the information readily available and can focus on investigations and assessing impact.|
|Make sure that your incident response team can access the necessary assets and systems, such as logs. They also need to be able to make production changes through a secure and controlled process. | You restore operations more quickly by ensuring that your team isn't wasting time. |
|Use a commercial status page instead of building your own.|Save time by using a commercial status page. A status page hosted by another organization also remains accessible to customers during an outage on your system.|
 
## Manage incidents methodically 

Adhering to the defined plan is crucial to avoid improvisation during response time. This approach helps minimize the stress and complexity of managing these situations.

### Design considerations

- **Assign incident severity.** Use your incident response plan to determine the incident severity. Customers are often frustrated during incidents. It's important that you understand the impact they're seeing so that you can prioritize. Communicate the severity of the incident clearly so that customers have realistic expectations.

- **Stay calm and think clearly.** Incidents can be stressful and ambiguous, with multiple stakeholders demanding attention. Have a clear process for who takes the lead within an incident. Triage incidents as best as you can while acknowledging that you might have to operate with imperfect information. Try to remain in control of the situation.
  
  Organizational leaders can help by shielding the team members who are actively investigating or mitigating an incident.

- **Communicate status to your customers.** Update the status page to publish just enough information. Communicate promptly and provide necessary information like estimated resolution times. Give customers frequent updates to maintain their trust.
  
### Design recommendations

| Recommendation | Benefit |
|---|---|
| During an incident, prioritize recovery over discovery. <br><br>When an incident occurs, prioritize restoring operations quickly to minimize disruption to your customers.| You might be able to recover by routing around an affected component or by rolling back an update, even if you don't understand what caused the problem yet. |
| Provide timely, clear, and frequent updates during outages. | You can instill customer confidence and reduce the burden on your frontline support team. |
| Designate a communications manager during an active incident. This manager might be a single person, or you might rotate the responsibility among team members between incidents.|By having one voice for your engineering team, you centralize conversations and reduce distractions to other team members. You also prevent conflicting information from reaching customers or stakeholders during a chaotic incident.|
| Ensure that you have a mission-critical support plan for vendors like Microsoft. | If an outage occurs, you need responsive communications with your platform vendors like Microsoft to help you determine where a problem is and to shorten the duration of the outage. |

## Conduct post-incident reviews

After you recover from an incident, review and analyze what happened to learn from it. Implement remediation actions, which might include technical changes, process adjustments, or more training.

### Design considerations

- **Learn from incidents.**  Outages offer valuable learning opportunities. Conduct thorough reviews after incidents to identify lessons and implement improvements. Major incidents often have multiple causes. Evaluate whether other layers of your solution, such as operational processes, might prevent or detect the problem before it escalates. Also, look for similar patterns elsewhere in your solution that might also be at risk of the same problem.

- **Communicate with your customers.** Many ISVs provide post-incident communications, especially for enterprise customers who expect high-quality updates. Be transparent and provide enough information for customers to understand the problem and mitigation steps. However, to maintain security and integrity, avoid sharing excessive internal details about your solution architecture or components.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Create a process to perform internal post-incident reviews.<br><br>Focus on identifying the reasons that contributed to the problem. Consider technical causes, how your processes might have contributed to the outage, and how you responded to the incident. | Internal post-incident reviews help you learn from production outages and minimize the risk of similar problems happening again. |
| Make a structured plan to address any items that need remediation. Include clear accountability and timelines.|Clear accountability helps you ensure that each role meets its functional expectations, enhances clarity, and allows for transparent reporting at the desired levels.|
| Publish customer-facing post-incident reviews.<br><br>Provide customers with enough detail to understand the problem and mitigation steps without revealing unnecessary internal details or system architecture. <br><br>Post-incident communications should always be written and published by humans. Technical and nontechnical stakeholders should review the communications for accuracy and clarity. | This approach helps maintain customers' confidence and assures them that you learned from the incident and are addressing the identified problems. |


## Next step

After reviewing the design areas, proceed to the assessment tool to evaluate your design.

> [!div class="nextstepaction"]
> [Assessment review tool for SaaS workloads on Azure](./assessment.md)
