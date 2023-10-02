---
title: Recommendations for establishing a security baseline
description: Learn about hardening resources recommendations for security. Reduce the attack surface and increase attackers' costs in the remaining area to limit the opportunities for malicious actors to exploit vulnerabilities.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for establishing a security baseline

**Applies to this Well-Architected Framework Security checklist recommendation:** 

|[SE:01](checklist.md)| Establish a security baseline aligned to compliance requirements, industry standards, and platform recommendations. Regularly measure your workload architecture and operations against the baseline to sustain or improve your security posture over time.| 
|---|---| 

A security baseline is a document that specifies your organization's security requirements and expectations across a range of areas. A good security baseline helps you to keep your data and systems secure, comply with regulatory requirements, minimize risk of inadvertent oversight, and reduce the likelihood of breaches and subsequent business impact. Security baselines should be published widely throughout your organization so that all stakeholders are aware of your expectations. 

This guide provides recommendations about setting a security baseline that's based on internal factors, such as business requirements, risks, and asset evaluation, and external factors such as industry benchmarks and regulatory standards.

**Definitions** 

| Term | Definition |
|---|---|
| | |
| | |


## Key design strategies

Security baseline is a structured document that defines a set of security criteria and capabilities that the workload must fulfill in order to increase security. In a more mature form, a baseline could extended to include a set of policies that are used to set guardrails. 

The baseline should be considered the standard for measuring your security posture. The goal should always be full attainment while keeping a broad scope.

Your security baseline must never be a ground-up effort. Industry standards, compliance (external or internal) or regulatory requirements, regional requirements, and the cloud platform benchmarks are the main drivers for the baseline. Examples include, Center for Internet Security (CIS) Controls, National Institute of Standards and Technology (NIST), and platform driven standards such as Microsoft cloud security benchmark (MCSB). All must be considered as a starting point for your baseline. Build that foundation by incorporating security requirements from the business requirements.

For links to the preceding assets, see [Related links](#related-links).

The baseline should be created by gaining consensus among business and technical leaders. It shouldn't be restricted to technical controls but also include the operational aspects of managing and maintaining the security posture. In that regard, the document also serves as the organization's commitment of investment towards security of the workload. The security baseline document should be distributed widely within your organization to ensure there's awareness about the security posture of the workload.

As the workload grows and the ecosystem evolves, the baseline must intentionally keep up with those the changes to make sure the fundamental controls are still effective. 

Creating a baseline should be a methodical process. Here are some recommendations about the process:

- **Asset inventory**. Identify stakeholders of workload assets and the security objectives for those assets. In the asset inventory, classify by security requirements and criticality. For information about data assets, see [Recommendations on data classification](data-classification.md). 

- **Risk assessment**. Identity potential risks associated with each asset and prioritize them.

- **Compliance requirements**. Baseline any regulatory or compliance for those assets and apply industry best practices. 

- **Configuration standards**. For each asset, define and document specific security configurations and settings.  If possible, templatize or find a repeatable, automated way to apply the settings consistently across the environment.

- **Access Control and Authentication**. Specify the role-based access control (RBAC) and multi-factor authentication (MFA) requirements. Document what "just enough access" means at the asset level. Always start with the principle of least privilege.

- **Patch management**. Apply latest versions on all the resource types to strengthen against attack.

- **Documentation and communication**. Document all configurations, policies and procedures. and communicate to the relevant stakeholders. 

- **Enforcement and accountability**. Establish clear enforcement mechanisms and consequences for non-compliance with the security baseline. Hold individuals and teams accountable for maintaining security standards.

- **Continuously monitor**. Assessing the effectiveness of the security baseline through observability and make improvements overtime.

### Composition of a baseline

Here are some common categories that should be part of a baseline. This isn't an exhaustive list but intended to provide you with an overview of the scope of the document.

#### Architecture components

The baseline must have prescriptive recommendations for the main components of the workload. These usually include technical controls for networking, identity, compute, and data. Reference the security baselines provided by the platform and add the missing controls to the architecture.

#### Development processes

The baseline must have recommendations about system classification, approved set of resource types, tracking those resources, and enforcing policies for using or configuring resources. 

The development team should have a clear understanding of the scope of security checks. For example, threat modeling should be a requirement in making sure potential threats are identified in code and in deployment pipelines. Be specific about static checks and vulnerability scanning in your pipeline and how regularly they must perform those scans.  

For more information, see Recommendations on [threat analysis](threat-model.md).

The development process should also set standards on various testing methodologies and their cadence. For more information, see [Recommendations on security testing](testing.md).

#### Operations

The baseline must set standards on using threat detection capabilities and raising alerts on anomalous activities that indicate actual incidents. This must include all layers of the workload including all the endpoints that are reachable from hostile networks.

The baseline must have recommendations for setting up incident response processes including communication and a recovery plan, and which of those processes could be automated to expedite detection and analysis. For examples, see [Security baselines for Azure overview | Microsoft Learn](/security/benchmark/azure/security-baselines-overview).

The incident response should also include a recovery plan and the requirements for that plan, such as affordances for regularly keeping and protecting backups. 

Data breach plans are often recommended by industry standards and recommendations provided by the platform. The team will have a comprehensive plan to follow when a breach is discovered. Also, check with your organization to see if there's coverage through cyberinsurance.

#### Training

Develop and maintain a security training program to ensure the workload team is equipped with the appropriate skills to support the security goals and requirements. The team needs fundamental security training but use what you can from your organization to support specialized roles.  Having role-based security training compliance and drilling participation is part of your security baseline.

### Use the baseline

The baseline should be used to drive initiatives, such as:

- **Preparedness toward design decisions**. Ideally, the security baseline should exist and be published before the architecture design process. You'll ensure team members are fully aware of your organization's expectations early, which avoids costly rework caused by a lack of clarity. The baseline criteria can be used as workload requirements that the organization has committed to. You'll be able to design and validate controls against those constraints.  

- **Measure your design**. Grade the current decisions against the current baseline. The baseline sets actual thresholds for criteria. Document any deviations that are deferred or deemed long-term acceptable. 

- **Drive improvements**. While baseline sets attainable goals, there will be gaps along the way. Prioritize those gaps in your backlog and remediate based on prioritization. 

- **Track your progress against the baseline**. Continuous monitoring of security measures against a set baseline is essential. Trend analysis is a good way of reviewing security progress over time and can reveal consistent deviations from the baseline. Use automation as much as possible pulling data from various sources, internal and external, to address current issues and prepare for future threats.

- **Help set guardrails**. Where possible, criteria in the baseline should have related guardrails established to enforce required security configurations, technologies, operations, based on internal factors (business requirements, risks, and asset evaluation) and external factors (benchmarks, regulatory standards, and threat environment). This helps to minimize the risk of inadvertent oversight, and the risk of punitive fines from noncompliance. 

Explore custom Azure Policies or use built-in initiatives like CIS benchmarks or Azure Security Benchmark to enforce security configurations and compliance requirements. Consider creating Azure Policies and initiatives out of baselines. 

### Evaluate the baseline regularly

Continuously improve security standards incrementally towards the ideal state to ensure continual risk reduction. Conduct periodic reviews to ensure that the system is up-to-date and in compliance with external influences. Any change to the baseline must be formal, agreed upon, and executed through proper change management processes.

Measure the system against the new baseline prioritize remediations based on their relevance and impact to the workload. 

Ensure that the security posture does not degrade naturally over time by instituting auditing and monitoring compliance with organizational standards.

## Azure facilitation

The Microsoft cloud security benchmark (MCSB) is a comprehensive security best practice framework that must be used as a starting point for your security baseline in addition to other resources that provide input to your baseline. 

For more information, see [Microsoft cloud security benchmark introduction | Microsoft Learn](/security/benchmark/azure/introduction).

Use The Microsoft Defender for Cloud (MDC) regulatory compliance dashboard to track those baselines, to get alerted if a pattern outside of a baseline is detected. For more information, see [The regulatory compliance dashboard - Microsoft Defender for Cloud | Microsoft Learn](/azure/defender-for-cloud/update-regulatory-compliance-packages).

Other features that help in establishing and improving the baseline:

- [Create custom Azure security policies - Microsoft Defender for Cloud | Microsoft Learn](/azure/defender-for-cloud/custom-security-policies?pivots=azure-portal)

- [Understanding security policies, initiatives, and recommendations - Microsoft Defender for Cloud | Microsoft Learn](/azure/defender-for-cloud/security-policy-concept)

- [Regulatory compliance checks - Microsoft Defender for Cloud | Microsoft Learn](/azure/defender-for-cloud/regulatory-compliance-dashboard)

## Organizational alignment

Cloud Adoption Framework provides guidance for central teams about establishing a baseline with a suggested template:

- [Security Baseline discipline overview - Cloud Adoption Framework | Microsoft Learn](/azure/cloud-adoption-framework/govern/security-baseline/)

- [Security Baseline discipline template - Cloud Adoption Framework | Microsoft Learn](/azure/cloud-adoption-framework/govern/security-baseline/template)

## Related links

- [Microsoft Compliance | Microsoft Learn](/compliance/)

- [Security baselines for Azure overview | Microsoft Learn](/security/benchmark/azure/security-baselines-overview)

- [What is Incident Response? Plan and Steps | Microsoft Security](https://www.microsoft.com/security/business/security-101/what-is-incident-response)

## Community resources

- [CIS Microsoft Azure Foundations Benchmark latest](https://www.cisecurity.org/benchmark/azure/)

- [Cybersecurity Framework | NIST](https://www.nist.gov/cyberframework)

## Security checklist

Refer to the complete set of recommendations. 

[Security checklist](checklist.md)