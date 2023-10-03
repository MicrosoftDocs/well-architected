---
title: Design review checklist for Security
description: Use this checklist for Security to identify the best infrastructure and application design for your workload.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/14/2023
ms.topic: conceptual
---

# Design review checklist for Security

This checklist presents a set of recommendations as a way for you to have a secure workload that’s aligned with the Microsoft Zero Trust Model. If you haven’t checked the boxes and considered the tradeoffs, the design could be at risk. Careful consideration of all the points covered in the checklist will give you confidence in success.

|&nbsp;|Code|Recommendation|
|---|---|---|
|&#9744; | [SE:01]()|**Establish a security baseline** aligned to compliance requirements, industry standards, and platform recommendations. Regularly measure your workload architecture and operations against the baseline to sustain or improve your security posture over time.
|&#9744; | [SE:02](./secure-development-lifecycle.md) <br> [SE:02](./threat-model.md)|**Maintain a secure development lifecycle** through a hardened, mostly automated, and auditable software supply chain. Evaluate secure design by using threat modeling to safeguard against security-defeating implementations.
|&#9744; | [SE:03](./data-classification.md)|**Classify and consistently apply sensitivity and information type labels** on all workload data and systems involved in data processing. Use classification to influence workload design, implementation, and security prioritization.|
|&#9744; | [SE:04](./segmentation.md)|**Create intentional segmentation and perimeters** in your architecture design and the workload’s footprint on the platform. The segmentation strategy must include networks, roles and responsibilities, workload identities, and resource organization.|
|&#9744; | [SE:05](./identity-access.md)|**Implement strict, conditional, and auditable identity and access management (IAM)** across all workload users, team members, and system components. Limit access exclusively to _as-necessary_. Use modern industry standards for all authentication and authorization implementations. Nonidentity based access is restricted and rigorously audited.|
|&#9744; | [SE:06](./networking.md)|**Isolate, filter, and control network traffic** across both ingress and egress flows. Apply defense in depth principles by using localized network controls at all available network boundaries across both east-west and north-south traffic.|
|&#9744; | [SE:07](./encryption.md)|**Encrypt data by using modern industry-standard methods** to guard confidentiality and integrity. Align encryption scope with data classifications; prioritize native platform encryption methods.|
|&#9744; | [SE:08](./hardening.md)|**Harden all workload components** by reducing extraneous surface area and tightening configuration, to increase attacker cost.|
|&#9744; | [SE:09](./application-secrets.md)|**Protect application secrets** by hardening their storage, restricting access and manipulation, and auditing those actions. Execute a reliable and regular rotation process that has the ability to do ad-hoc rotations for emergencies.|
|&#9744; | [SE:10](./monitoring.md)|**Have a holistic monitoring strategy** that relies on modern threat detection mechanisms that can be integrated with the platform. Mechanisms should reliably alert for triage and feed signals into existing SecOps processes.|
|&#9744; | [SE:11](./testing.md)|**Establish a comprehensive testing regimen** that combines various testing approaches to prevent security issues, validate threat prevention implementations, and test threat detection mechanisms.|
|&#9744; | [SE:12](./incident-response.md)|**Define and test effective incident response procedures** that cover a spectrum of incidents, from localized to disaster recovery. Procedures must state clear ownership for execution.|

## Next steps

> [!div class="nextstepaction"]
> [Tradeoffs for Security](tradeoffs.md)














