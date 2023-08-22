---
title: Security design principles
description: Security design principles describe a securely architected system hosted on cloud or on-premises datacenters (or a combination of both).
author: PageWriter-MSFT
ms.author: martinek
ms.date: 02/10/2022
ms.topic: conceptual
ms.custom:
  - article
---

# Security design principles

A Well-Architected workload must be built with a zero-trust mindset. A secure workload is **resilient to attacks** and has inter-related security **assurances** **of confidentiality, integrity, and availability** (also known as the *CIA triad*) in addition to meeting business goals. Any security incident has the potential to become a major breach that damages the brand and reputation of the workload or organization. To measure the security efficacy of your overall strategy for the workload, start with these questions:

-   Do your defensive investments provide meaningful cost and friction for attackers to compromise your workload?

-   Will your security measures be effective in restricting the blast radius of an incident?

-   Do you understand how this workload could be valuable for an attacker to control? The impact on business if this workload and its data is stolen, unavailable, or tampered with?

-   Can the workload and operations quickly detect, respond, and recover from disruptions?

As you design your system, use the Microsoft Zero Trust model as the compass to mitigate security risks:

-   **Verify explicitly** so that **only trusted identities** perform **intended and allowed actions**, which originate from **expected locations**. This makes it harder for attackers to impersonate legitimate users and accounts.

-   **Use least-privilege access** for the **right identities** with the **right set of permissions** for the **right duration**, and to the **right assets**. Limiting permissions keeps attackers from abusing permissions that legitimate users don't even need.

-   **Assume breach** of security controls and design compensating controls that **limit risk and damage** if a primary layer of defense fails. This helps you defend better by thinking like an attacker who is interested in success (regardless of how they get it).

Security isn't a one-time effort, and you must use this guidance on a recurring basis. Continuously improve your defenses and security knowledge to keep your workload safe from attackers who are constantly getting access to innovative attack vectors as they are invented and added to automated attack kits.

The **design principle approaches aren't checklist items to be done once**. They intend to guide an ongoing security mindset to help you continuously improve the security posture of your workload as the attackers continuously get better at their attacks. These principles should guide the security of your architecture, design choices, and operational processes. Start with the recommended approaches and **justify the benefits for a set of security requirements**. After you've set your strategy, drive actions using the **Security checklist** as your next step.

If these principles aren't applied properly, a negative impact on business operations and revenue is to be expected. Some consequences might be obvious, such as penalties for regulatory workloads. Others may not be so obvious and could lead to ongoing security issues before detection.

Many mission-critical workloads consider security as the primary concern alongside reliability, given non-reliability impacting attack vectors, such as data exfiltration. Security and reliability can pull the workload in opposite directions because security focused-design can introduce additional points of failure and increase operational complexity. Often the impact of security on reliability is indirect through operational constraints. Consider trade-offs between security and reliability carefully.

By following these principles, you can improve security effectiveness, harden workload assets, and build trust with your users.

## Plan your security readiness 

|![Goal icon](../_images/goal.svg) *Drive toward adopting and implementing security practices in architectural design decisions and operations with minimum friction.*|
|--| 

As the workload owner, you have a shared responsibility with the organization in protecting assets. Create a **security readiness plan** that's aligned with business priorities because it will lead to well-defined processes, adequate investments, and appropriate accountabilities. The plan should provide the workload requirements to the organization, who also share responsibility in protecting assets. Security plans should factor into your strategy for reliability, health modeling, and self-preservation design choices.

Apart from the organizational assets, the workload itself needs to be protected from intrusion and exfiltration attacks. The plan should be factored into all facets of Zero-Trust as well as the CIA triad.

Functional and non-functional requirements, budget constraints, and other considerations shouldn't restrict security investments and dilute assurances, but at the same time those security investments need to be engineered and planned with those constraints and restrictions in mind.

|Approach      |Benefit              |
|-|-|
| Use segmentation as a strategy to plan security boundaries in the workload environment, processes, and team structure to isolate access and function.<br><br> Your segmentation strategy should be driven by business requirements. It can be based on criticality of components, division of labor, privacy concerns, and other factors.| You'll be able to **minimize operational** **friction** by defining roles and establishing **clear lines of responsibility**. This exercise will also help **identify the level of access** for each role, especially critical impact accounts. <br><br> Through isolation, you'll be able to **limit exposure of sensitive flows** to only those roles and assets that need access, which would otherwise inadvertently lead to information flow disclosure.<br><br> To summarize, you'll be able to **right-size security efforts** based on the needs of each segment. |
| Continuously build skills through role-based security training that meets the requirements of organization and use cases of the workload. | A highly skilled team can design, implement, and monitor **security controls that remain effective** against attackers, who constantly look for new ways to exploit the system.  <br><br> Organization-wide training typically focuses on developing a broader skill set around securing the common elements. However, with role-based training, you'll be focusing on **development of deep expertise** in the platform offerings and security features that address workload concerns.<br><br> Both altitudes are necessary to defend against adversaries through **good design and effective operations**. |
| **Make sure that there's an incident response plan** for your workload.<br><br>    Use industry frameworks that define the set the standard operating procedure for preparedness, detection, containment, mitigation, and post-incident activity.    | At the time of crisis, confusion must be avoided. <br><br> By having a well-documented plan, responsible roles can **focus on execution** without wasting time on uncertain actions. Also, a comprehensive plan will make sure **all remediation requirements are fulfilled**.  |
| **Strengthen your security posture by understanding the security compliance requirements** imposed by influences outside the workload team, such as organizational policies, regulatory compliance, and industry standards. | Being clear about the compliance requirements will help you to **design for the right security assurances** and **prevent non-compliance** issues, which could lead to penalties. <br><br> All materials available as part of industry standards can form the baseline and influence your choice of tools, policies, security safeguards, guidelines, risk management approaches, and training.  <br><br> Acknowledging that the workload adheres to compliance, you'll be able to **instill confidence** in your user base.    
| **Define and enforce team-level security standards** across the lifecycle and operations of the workload.  <br><br>    **Strive for consistent practices** in coding, gated approvals, release management, data protection and retention, and more.| Defining good security practices can **minimize negligence** and the surface area for potential errors. The team will **optimize efforts and the outcome will be predictable** because the variants in approaches will be reduced. <br><br> By observing security standards over time, you'll be able to **identify opportunities for improvement, which can involve automation**, which will streamline efforts further and with consistency.  |
| Align your incident response with the **Security Operation Center (SOC) centralized function** within the organization.      | By centralizing incident response functions, you'll be able to take advantage of the specialized IT professionals who will be able detect incidents events in real time for addressing potential threats as quick as possible.       |

## Design to protect confidentiality

|![Goal icon](../_images/goal.svg) *Prevent exposure to privacy, regulatory, application, and proprietary information through access restrictions and obfuscation techniques*.| 
|--| 

Workload data can be classified by user, usage, configuration, compliance, intellectual property, and more. That data cannot be shared or accessed beyond the established trust boundaries. Implementation to protect confidentiality should focus on access controls, maintaining opacity, and keeping an audit trail of activities, pertaining to data and the system.

| Approach    | Benefit   |
|-|-|
| Have **strong access controls** that grant access only on a need-to-know basis.                                                    | *Least privilege*.  <br><br> The workload will be protected from *unauthorized access* and prohibited activities. Even when access is from trusted identities, the **access permissions** and **exposure time will be minimized** because communication path is opened for a limited period.     |
| **Classify data based on its type, sensitivity, and potential risk**. Assign a confidentiality level for each. <br><br>     Include system components that are in-scope for the identified level.               | *Verify explicitly*.  <br><br> Based on the evaluation, you'll also be able to right-size security measures. <br><br> You'll be able to identify data and components that have a **high potential impact** and/or exposure to risk. This exercise will bring **clarity and agreement** on the information protection strategy. |  
| Safeguard your data at rest, in transit, and while processing through **encryption**, based on the assigned confidentiality level. | *Assume breach*. <br><br> Even if an attacker gets access, they **won't be able to read properly encrypted** sensitive data. <br><br> Sensitive data includes configuration information that's used to gain further access inside the system. By encrypting data, you'll be able to **contain risks**.  |
| **Guard against exploits** that might cause unwarranted exposure of information.                                                   | *Verify explicitly*.    <br><br> It's crucial to minimize vulnerabilities in authentication & authorization implementations, code, configurations, operations, and stemming from the social habits of the systems' users. <br><br> By having up to date security measures in place you'll be able to **block known security vulnerabilities** from entering the system. Also, **mitigate new vulnerabilities** that may appear over time with routine operations throughout the development cycle, continuously improving security assurances. |
| **Guard against data exfiltration** resulting from malicious or inadvertent access to data.     | *Assume breach*.      <br> <br>You'll be able to contain blast radius by **blocking unauthorized data transfer**. Additionally, controls applied to networking, identity, and encryption will protect data at various layers.     |
| **Maintain the level of confidentiality** as data flows through various components of the system.     | *Assume breach*.  <br><br> By enforcing confidentiality levels throughout the system, you'll be able to provide a consistent level of hardening end-to-end. This will **prevent vulnerabilities** that might result from moving data to a lower security tier.                                                                       |
| Maintain an **audit trail** that includes all types of access activities.    | *Assume breach*.    <br><br> Audit logs support **faster detection and recovery** in case of incidents and aid in ongoing security monitoring.       |

## Design to protect integrity

|![Goal icon](../_images/goal.svg) *Prevent corruption of design, implementation, operations, and data to avoid disruptions that can stop the system from delivering its* *intended utility* *or operate outside the prescribed limits*. *The system should provide information assurance throughout the workload lifecycle*.| 
|--| 

The key is to have controls that prevent tampering of business logic, flows, deployment processes, data, and even the lower stack components, such as the operating system and boot sequence. Lack of integrity may introduce vulnerabilities, which will lead to breach in confidentiality and availability.

| Approach  | Benefit   |
|-|-|
| **Have strong access controls that authenticate and authorize access to the system.** <br><br>**Minimize access based on privilege, scope, and time.**   | *Least privilege*.  <br><br>  Depending on the intensity of the controls, you'll be able to **prevent or reduce risks from unapproved modifications**. This will make sure data is consistent and trustworthy.   Minimizing access will limit the extent of corruption.           |
| **Continuously protect and detect vulnerabilities in your supply chain** to block attackers from injecting software faults into the infrastructure, build system, tools, libraries, and other dependencies.<br><br> Supply chain should scan for vulnerabilities during **build time and runtime**. | *Assume breach*.  <br><br> Knowing the origin of the software and verifying its authenticity throughout the lifecycle will **provide predictability**. You'll be able to **know the vulnerabilities well in advance** so that you can proactively remediate, and the system remains secure in production. |
| **Establish trust and verify by using cryptography techniques**, such as code signing, certificates, and encryption. <br><br>   Protect those mechanisms by allowing reputable decryption.    | *Verify explicitly*, *Least privilege.*     <br><br> You'll have assurance that changes to the data or access to the system **was verified by a trusted source**.  <br><br> Even if encrypted data is intercepted in transit by a malicious actor, they won't be able to unlock or decipher the contents. Digital signatures can be used to ensure that the data has not been tampered with during transmission       |
| **Make backup data immutable and encrypted**, when data is replicated or transferred.  | *Verify explicitly.*<br><br> You'll be able to recover data with confidence in that backup **data wasn't changed at rest**, inadvertently or maliciously.    |
|**Avoid or mitigate system implementations that allow usage of your workload to operate outside** the **intended limits and purpose**.  | *Verify explicitly.* <br><br>      When your system has strong safeguards that check if usage aligns with its intended limits and purposes, the scope for potential abuse or tampering is reduced for your compute, networking, and data stores.     |

## Design to protect availability

|![Goal icon](../_images/goal.svg) *Prevent and, or minimize system and workload downtime and degradation in the face of a security incident with strong security controls that are hardened to compromise.* *Data integrity must be maintained during the incident and after the system has recovered.*| 
|--| 

Architecture choices for availability and security are a balancing act. The system should have availability guarantees to make sure that users have access to the data and that data is reachable. From a security perspective, users should operate within the allowed access scope and the data must be trusted. Security controls should block bad actors, but not block legitimate users from accessing the system and data. Over indexing on security shouldn't cause unavailability situations.

| Approach  | Benefit      |
|-|-|
| **Prevent compromised identities from misusing access** to gain control of the system.<br><br>   Check for **overly pervasive scope and time limits** to minimize risk exposure.   | *Least privilege*.     <br><br> This strategy will **mitigate the risks of excessive, unnecessary, or misused access permissions** on crucial resources, such as unauthorized modifications and even deletion of resources. Take advantage of the platform-provided just-in-time (JIT), just-enough-access (JEA), and time-based security modes to make sure standing permissions wherever possible. |
| Use security controls and design patterns to **prevent attacks and code flaws from causing resource exhaustion** and blocking access, leading to unavailability of services. | *Verify explicitly*.   <br><br>The **system won't experience downtime** due to malicious intent, such as distributed denial-of-service (DDoS). As a result, users won't be impacted by outage.    |
| Have **preventative measures for attack vectors that exploit vulnerabilities** of application code, networking protocols, identity systems, malware protection, and others.  | *Assume breach*.  <br><br>It's key to have code scanners, apply the latest security patches, update software, and protect the system with strong antimalware on an ongoing basis. <br><br> You'll able to reduce the attack surface ensuring business continuity.   |
| **Prioritize** security controls on the **critical components and flows** in the system that are susceptible to risks.       | *Assume breach*, *Verify explicitly*.  <br><br>    Regular detection and prioritization exercises can help **apply security expertise to the critical aspects** of the system. You'll be able to focus on the most likely and damaging threats and start your risk mitigation in areas that need the most attention.    |
| Apply at least the same level of **security rigor on your recovery resources and processes** as the primary environment, for security controls and frequency of backup.      | *Assume breach*. <br><br> In disaster recovery, you should have a preserved safe system state available. You'll be able to fail over to a secured secondary system or location and restore backups that won't introduce a threat.  <br><br> A well-design process will prevent a situation where a security incident hinders the recovery process. For example, corrupted backup data or encrypted data that cannot be deciphered.     |

## Sustain and evolve your security posture

|![Goal icon](../_images/goal.svg) *Your continuous improvements and vigilance helps to stay ahead of attackers who are continuously evolving their attack strategies*.| 
|--| 

Security assurances must not decay with time. Security operations must also be continually improved so that new disruptions are handled more efficiently. The improvements should strive to align with the phases defined by industry standards. It will lead to better preparedness, reduced time to detect, effective containment and mitigation. Continuous improvement should be based on the lessons learned from past incidents.

It's important to measure your security posture, enforce policies to maintain that posture, and regularly validate your security mitigations and compensating controls in order to continuously improve your security posture in the face of evolving threats.

| Approach| Benefit      |
|-|-|
| **Build and maintain a comprehensive asset inventory** that has classified information about resources, location, dependencies, owners, and other metadata relevant to security.<br><br> Inventory should be **automated** to derive data from the system, as much as possible. | Having a well-organized inventory will provide a **holistic view of the environment**, which will place you in a position of advantage against attackers, especially during post-incident activities.  <br><br> It will also create a business rhythm to drive communication, posture upkeep of the critical components, and decommissioning orphaned resources.    |
| **Perform threat modeling** to identify and mitigate potential threats.    | You'll have a **report of attack vectors** prioritized by their severity level. You'll be able to identify threats and vulnerabilities quickly and set up counter measures.                                                                                    |
| Regularly **capture data to quantify current state** against your established security baseline and **set priorities for remediations**. <br><br>  Take advantage of the platform-provided features for **security posture management** and **enforcing compliance** imposed by external and internal influences.  | You want accurate reports that bring clarity and consensus on the focus areas. You'll be able to immediately **execute technical remediations** starting with the highest priority items. Also, **identify gaps**, which serve as opportunities for improvement. <br><br>Enforcement will safeguard against violations and prevent regressions thereby preserving your security posture.  |
| **Run periodic security tests** conducted by experts external to the workload team, who attempt to ethically hack the system.  <br><br>   Perform routine and integrated **vulnerability scanning** to detect exploits in infrastructure, dependencies, and application    | It's critical that you validate the security defenses by **simulating real-world attacks** through techniques such as penetration testing.   <br><br>   Threats can be introduced as part of your change management. By integrating scanners into the deployment pipelines, you'll be able automatically spot vulnerabilities and even quarantine usage until marked as safe.            |
| **Detect, respond, and recover** with swift and effective security operations.   | The primary benefit is the ability to **preserve or restore the security assurances of the CIA triad** during and after an attack.    <br><br> You want to be alerted as soon as a threat is detected so that you can start your investigations and take appropriate actions.       |
| **Conduct post-incident activities** such as root-cause analysis, post-mortem, and incident reports.    | Such activities will give insight into the impact of the breach and resolution measures, which will drive improvements in defenses and operations.   |
| **Get current, stay current**.   <br><br>   Stay up to date on updates, patching, and security fixes.    <br><br>     Continuously evaluate the current system and improve it based on audit reports, benchmarking, and lessons from testing activities. Consider automation, as appropriate.  <br><br>    Use threat intelligence powered by security analytics for dynamic detection of threats.      <br><br>           Review workload conformance to the best practices of security development lifecycle (SDL), at regular intervals.     | You'll be able to ensure the **security posture doesn't degrade naturally over time**.     <br><br> By integrating learnings from real world attacks and testing activities, you'll be able to withstand attackers who continuously improve, exploit new categories of vulnerabilities.  <br><br>
<br><br>Automation of repetitive tasks *decrease the chance of human error* that can create risk.<br><br>| SDL reviews will bring clarity around security features. Through standardization, you can maintain an inventory of workload assets and their security reports covering origin, usage, operational weaknesses, and other factors.                             |

## Next steps