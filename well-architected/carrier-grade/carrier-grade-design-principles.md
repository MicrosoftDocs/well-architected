---
title: Design principles for carrier-grade workloads on Azure
description: Understand the design principles of building a carrier-grade application on Microsoft Azure.
author: mikedell73
ms.author: mikedell
ms.date: 07/05/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.industry: telecommunications
products: 
- azure-app-service
- azure-traffic-manager
- cosmos-db
ms.custom:
  - carrier-grade
---

# Design principles of a carrier-grade workload

Carrier-grade key design points include:

- [Fault tolerance](carrier-grade-design-area-fault-tolerance.md)
- [Considered data model](carrier-grade-design-area-data-model.md)
- [Built-in health modeling for management and monitoring](carrier-grade-design-area-health-modeling.md)
- [Extensive testing and validation](carrier-grade-design-area-testing.md)

The [carrier-grade design methodology](carrier-grade-design-methodology.md) is supported by five design principles. These principles serve as a road map for subsequent design decisions across the critical design areas. We highly recommend that you get to know these principles to better understand their effects and the trade-offs associated with non-adherence.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected carrier-grade workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [What is a carrier-grade workload](carrier-grade-get-started.md#what-is-a-carrier-grade-workload)?

The following carrier-grade design principles resonate and extend the quality pillars of the Azure Well-Architected Framework:

- [Reliability](/azure/architecture/framework/#reliability)
- [Security](/azure/architecture/framework/security/)
- [Cost Optimization](/azure/architecture/framework/cost/)
- [Operational Excellence](/azure/architecture/framework/devops/)
- [Performance Efficiency](/azure/architecture/framework/scalability/)

## Reliability

**_Maximum reliability_** - Fundamental pursuit of the most reliable solution, ensuring tradeoffs are properly understood.

|Design principle|Considerations|
|---|---|
|[**Design for failure**](/azure/architecture/framework/resiliency/principles#design-for-failure)| Failure is impossible to avoid in a highly distributed multi-tenant cloud environment like Azure. By anticipating failures and cascading repercussions, from individual components to entire Azure regions, a solution can be designed and developed in a resilient manner.|
|[**Observe application health**](/azure/architecture/framework/resiliency/principles#observe-application-health)|Before issues affecting application reliability can be mitigated, they must first be detected. By monitoring the operation of an application relative to a known healthy state, it's possible to detect or even predict reliability issues. This detection allows for swift remedial action.|
|[**Drive automation**](/azure/architecture/framework/resiliency/principles#drive-automation)|One of the leading causes of application downtime is human error, whether due to the deployment of insufficiently tested software or misconfiguration. To minimize the possibility and effects of human errors, it's vital to strive for automation in all aspects of a cloud solution to improve reliability, automated testing, deployment, and management.|
|[**Design for self-healing**](/azure/architecture/framework/resiliency/principles#design-for-self-healing)|Self healing describes a system's ability to deal with failures automatically through pre-defined remediation protocols connected to failure modes within the solution. It's an advanced concept that requires a high level of system maturity with monitoring and automation, but should be an aspiration from inception to maximize reliability.|
|[**Perform chaos testing**](/azure/architecture/framework/resiliency/chaos-engineering)|By constantly applying stress or faults on components, you can help expose issues early, before small problems are compounded by many other factors.|
|[**Perform disaster recovery testing**](/azure/architecture/framework/resiliency/backup-and-recovery#failover-and-failback-testing)|Test the process of restoring application functionality in the wake of a catastrophic loss. Create and test a disaster recovery plan regularly using key failure scenarios.|

## Performance Efficiency

**_Sustainable performance and scalability_** - Design for scalability across the end-to-end solution without performance bottlenecks.

|Design principle|Considerations|
|---|---|
|[**Design for scale-out**](/azure/architecture/framework/resiliency/principles#design-for-scale-out)|Scale-out is a concept that focuses on a system's ability to respond to demand through horizontal growth. As traffic grows, more resource units are added in parallel instead of increasing the size of the existing resources. A systems ability to handle expected and unexpected traffic increases through scale-units is essential to overall performance and reliability by further reducing the effects of a single resource failure.|
|[**Continuous validation and testing**](/azure/architecture/framework/scalability/performance-test)|Automated testing should be performed within continuous integration and continuous deployment (CI/CD) processes to drive continuous validation for each application change. Load testing against a performance baseline, ideally with synchronized chaos experimentation, should be included to validate existing thresholds, targets, and assumptions. This testing helps quickly identify risks to resiliency and availability. Such testing should be conducted within staging and testing environments, but also optionally within short-lived development environments to drive validation as early as possible within the engineering cycle. Run a subset of tests against the production environment, particularly along with a blue/green deployment model to validate new deployment stamps before receiving production traffic.|
|[**Baseline performance and identify bottlenecks**](/azure/architecture/framework/scalability/test-tools#identify-baselines-and-goals-for-performance)|Performance testing with detailed data from every system component allows for the identification of bottlenecks within the system. These bottlenecks include components that must be scaled in relation to other components. The component information should be incorporated into the capacity model.|
|[**Reduce overhead with managed compute services**](/azure/architecture/guide/design-principles/managed-services)|Using managed compute services and containerized architectures reduces the ongoing administrative and operational overhead of designing, operating, and scaling applications. Shifting infrastructure deployment and maintenance to the managed service provider reduces this overhead.|

## Operational Excellence

**_Operations by design_** - Engineered to last with robust and assertive operational management.

|Design principle|Considerations|
|---|---|
|[**Loosely coupled components**](/azure/architecture/framework/devops/principles#use-loosely-coupled-architecture)|Loose coupling enables independent and on-demand testing, deployments, and updates to components of the application while minimizing inter-team dependencies for support, services, resources, or approvals.|
|[**Optimize build and release process**](/azure/architecture/framework/devops/principles#optimize-build-and-release-processes)|Fully automated build and release processes reduce the friction and increase the velocity of deploying updates, bringing repeatability, and consistency across environments. Automation shortens the feedback loop from developers pushing changes to getting automated near instantaneous insights on code quality, test coverage, security, and performance, which increases developer productivity and team velocity.|
|[**Understand operational health**](/azure/architecture/framework/devops/principles#understand-operational-health)|Full diagnostic instrumentation of all components and resources enables ongoing observability of logs, metrics and traces, and enables health modeling to quantify application health in the context of availability and performance requirements.|
|[**Rehearse recovery and practice failure**](/azure/architecture/framework/devops/principles#rehearse-recovery-and-practice-failure)|Business Continuity (BC) and Disaster Recovery (DR) planning and practice drills are essential and should be conducted periodically. Learnings from drills can iteratively improve plans and procedures to maximize resiliency, if there's unplanned downtime.|
|[**Embrace continuous operational improvement**](/azure/architecture/framework/devops/principles#embrace-continuous-operational-improvement)|Prioritize routine improvement of the system and user experience. To prioritize, use a health model to understand and measure operational efficiency with feedback mechanisms. This model enables application teams to understand and address gaps in an iterative manner.|

## Security

**_Always secure_** - Design for end-to-end security to maintain application stability and ensure availability.

|Design principle|Considerations|
|---|---|
|[**Monitor the security of the entire solution and plan incident responses**](/azure/architecture/framework/security/security-principles#monitor-system-security-plan-incident-response)|Correlate security and audit events to model application health and identify active threats. Establish automated and manual procedures to respond to incidents using Security Information and Event Management (SIEM) tooling for tracking.|
|[**Model and test against potential threats**](/azure/architecture/framework/devops/principles#optimize-build-and-release-processes)|Ensure appropriate resource hardening. Establish procedures to identify and mitigate known threats, using penetration testing to verify threat mitigation, and static code analysis and code scanning.|
|[**Identify and protect endpoints**](/azure/architecture/framework/devops/principles#understand-operational-health)|Monitor and protect the network integrity of internal and external endpoints through security capabilities and appliances, such as firewalls or web application firewalls. Use industry standard approaches to protect against common attack vectors like Distributed Denial-Of-Service (DDoS) attacks, such as SlowLoris.|
|[**Protect against code level vulnerabilities**](/azure/architecture/framework/devops/principles#rehearse-recovery-and-practice-failure)|Identify and mitigate code-level vulnerabilities, such as cross-site scripting or SQL injection. Incorporate security patches into operational lifecycles for all parts of the codebase, including dependencies.|
|[**Automate and use least privilege**](/azure/architecture/framework/devops/principles#embrace-continuous-operational-improvement)|Drive automation to minimize the need for human interaction and implement least privilege across both the application and control plane to protect against data exfiltration and malicious actor scenarios.|

## Share nothing

_Share nothing_ is a common and straightforward approach to achieve high availability. Use this approach when an application can be serviced by multiple, distinct elements, which are interchangeable. The individual elements must have a well-understood availability metric, but it doesn't need to be high. However, the elements must be combined in a way to remain independent, with no shared infrastructure or dependencies.

To share nothing is often impossible. To start from the position that nothing _should_ be shared, and only add in the most minimal possible set of shared dependencies, can result in an optimal solution.

### System example

Given a single system that has six hours of downtime per year (around `3.5*9s`), a solution that combines four systems where the periods of downtime are uncorrelated will experience less than `30s` of downtime per year. As soon as those four systems rely on a common service, such as global DNS, their downtime is no longer uncorrelated. The resulting downtime will be higher.

## Tradeoffs

There are obvious cost tradeoffs associated with introducing greater reliability, which should be carefully considered in the context of workload requirements.

Maximizing reliability can affect the overall financial cost of the solution. For example, the duplication of resources and the distribution of resources across regions to achieve high availability has clear cost implications. To avoid excess costs, don't over-engineer or over-provision beyond the relevant business requirements.

Consider the added cost associated with engineering investment in fundamental reliability concepts. These costs include embracing:

- Infrastructure as code
- Deployment automation
- Chaos engineering

Expenses come at a cost for both time and effort, which could be invested elsewhere to deliver new application functionality and features.

## Next step

Review the architecture pattern associated with carrier-grade workloads.

> [!div class="nextstepaction"]
> [Architecture pattern](carrier-grade-architecture-pattern.md)

