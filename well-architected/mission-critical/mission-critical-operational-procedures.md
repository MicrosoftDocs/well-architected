---
title: Operational procedures for mission-critical workloads on Azure
description: Learn about implementing reliable and effective operations by using DevOps processes. Use automated deployment pipelines to run versioned application and infrastructure code artifacts within a source repository.
author: calcof
ms.author: prwilk
ms.date: 01/26/2023
ms.topic: concept-article
---

# Operational procedures for mission-critical workloads on Azure

Reliable and effective operations must be based on the principles of *automation wherever possible* and *configuration as code*. This approach requires a substantial engineering investment in DevOps processes. Automated pipelines are used to deploy application and infrastructure code artifacts. The benefits of this approach include consistent and accurate operational outcomes with minimal manual operational procedures.

This design area explores how to implement effective and consistent operational procedures.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload).

## DevOps processes

The DevOps team for a mission-critical application must be responsible for these tasks:
  - Creation and management of application and infrastructure resources via CI/CD automation.
  - Application monitoring and observability.
  - Azure role-based access control (RBAC) and identity management for application components.
  - Network management for application components.
  - Cost management for application resources.

*DevSecOps* integrates security monitoring, audits, and quality assurance into the DevOps lifecycle. For mission-critical workloads, align your DevSecOps practices with the [Security pillar guidance](../security/overview.md) and apply the [Zero Trust model](mission-critical-security.md#alignment-with-the-zero-trust-model) across all operational procedures.

### Design considerations

- **Release and update process**. Avoid manual processes for any change to application components or underlying infrastructure. Manual processes can lead to inconsistent results. 

- **Dependencies on central IT teams**. DevOps processes can be difficult to apply when there are hard dependencies on centralized functions because these dependencies prevent end-to-end operations.

- **Identity and access management**. DevOps teams can consider granular Azure RBAC roles for distinct technical functions. Apply a zero-trust model across DevOps roles.

### Design recommendations

- Manage configuration settings and updates as code. For details, see [Infrastructure as code deployments](mission-critical-deployment-testing.md#infrastructure-as-code-deployments). Enforce change management through code for routine operations like key or secret rotation and permissions management. Use auditable update mechanisms for recurring updates.

- Avoid centralized provisioning dependencies that can introduce noisy neighbor risk. For details, see [Scale-unit architecture](mission-critical-application-design.md#scale-unit-architecture). If centralized provisioning dependencies are unavoidable, align the dependency's availability requirements with mission-critical requirements. Require transparency from central teams on their change management and incident response processes.

- Dedicate a proportion of engineering capacity during each sprint to driving fundamental platform improvements and bolstering reliability. We recommend that you allocate 20-40 percent of capacity to these improvements. 

- Develop common engineering criteria, reference architectures, and libraries that are aligned with core [design principles](mission-critical-design-principles.md). Enforce a consistent baseline configuration for reliability, security, and operations via a policy-driven approach that uses Azure Policy.

  You can also use the common engineering criteria and associated artifacts, like Azure policies and Terraform resources for common design patterns, across other workloads within your organization's broader application ecosystem.

- Apply a zero-trust model in critical application environments. Use technologies like Microsoft Entra Privileged Identity Management to ensure that operations are consistent and occur only through CI/CD processes or automated operational procedures.

  Team members shouldn't have standing write access to any environment. You might want to make exceptions in development environments to enable easier testing and debugging.

- Define emergency processes for just-in-time access to production environments. Ensure that break glass accounts exist in case of serious problems with the authentication provider.

- Use the [health model](../design-guides/health-modeling.md) to continually improve operational procedures and triggers. For guidance on building monitoring systems, see the [monitoring design guide](../design-guides/monitoring.md).

## Application operations

The [application design](mission-critical-application-design.md) and [platform](mission-critical-application-platform.md) recommendations influence operational procedures. For security monitoring and threat detection, see the [Security pillar monitoring guidance](../security/monitor-threats.md). For incident response procedures, see [Security incident response](../security/incident-response.md).

### Design considerations

- **Built-in operations of Azure services**. Azure services provide built-in (enabled by default) and configurable platform capabilities, like zone redundancy and geo-replication. An application's reliability depends on these operations. Certain configurable capabilities incur an additional cost, like the multi-write deployment configuration for Azure Cosmos DB. Avoid building custom solutions unless you absolutely need to.

- **Operational access and execution time**. Most required operations are exposed and accessible through the Azure Resource Manager API or the Azure portal. However, certain operations require assistance from support engineers. For example, some Azure Cosmos DB periodic backup restore scenarios require Azure support via a support case. This dependency might affect the downtime of the application. For stateless resources, we recommend that you redeploy instead of waiting for support engineers to try to recover deleted resources.

### Design recommendations

- Automate failover procedures. For an active/active model, use a [health model](../design-guides/health-modeling.md) and automated scale operations to ensure that no failover intervention is required. For an active/passive model, ensure that failover procedures are automated or at least codified within pipelines.

- Prioritize the use of Azure-native autoscaling for services that support it. For services that don't support native autoscaling, use automated operational processes to scale services. Use scale units with multiple services to achieve scalability.

- Use platform-native capabilities for backup and restore, ensuring that they're aligned with your RTO/RPO and data retention requirements. Define a strategy for long-term backup retention as needed.

- Use built-in capabilities for SSL certificate management and renewal, like those provided by [Azure Front Door](../service-guides/azure-front-door.md).

- For external teams, establish a recovery process for resources that require assistance. For example, if the data platform is incorrectly modified or deleted, the recovery methods should be well understood, and a recovery process should be in place. Similarly, establish procedures to manage decommissioned container images in the registry.

- Practice recovery operations in advance, on non-production resources and data, as part of standard business continuity preparations.

- Identify critical alerts and define target audiences and systems. Define clear channels to reach appropriate stakeholders. Send only actionable alerts to avoid white noise and prevent operational stakeholders from ignoring alerts and missing important information. Implement continuous improvement to optimize alerting and remove observed white noise. For observability guidance, see the [Application Insights](../service-guides/application-insights.md) and [Log Analytics](../service-guides/azure-log-analytics.md) service guides.

- Apply Azure Policy to enforce operational capabilities and a reliable configuration baseline. For details, see [Policy-driven governance](mission-critical-security.md#policy-driven-governance).

- Avoid the use of resource locks on ephemeral regional resources. Instead, rely on the appropriate use of RBAC and CI/CD pipelines to control operational updates. You can apply resource locks to prevent the deletion of long-lived global resources.

## Monitoring and health modeling

> [!NOTE]
> For comprehensive health modeling guidance, see the [Health modeling design guide](../design-guides/health-modeling.md). For instrumentation and monitoring systems guidance, see the [monitoring design guide](../design-guides/monitoring.md).

Mission-critical workloads require a unified observability strategy that spans global, regional, and stamp-level resources. Collect all observability data from  application and infrastructure components into a [unified data sink](../design-guides/monitoring.md), which can be a [Log Analytics workspace](../service-guides/azure-log-analytics.md), so the operations team can correlate how the workload is performing and identify issues failures across the entire environment. Use [Application Insights](../service-guides/application-insights.md) with [distributed tracing](/dotnet/core/diagnostics/distributed-tracing-concepts) for end-to-end request tracking.

There might not be a single team that knows what "healthy" means for ever involved component. So it's crucial to define health models covering the mission criticial areas of you workload. A [health model](../design-guides/health-modeling.md) will map signal from your monitring sources into a combined health status. It translates the deep technical details into a simple status of "healthy", "degraded", or "unhealthy" and allows to set up alerts for when health degrades.

## Update management

Mission-critical design strongly endorses the principle of ephemeral stateless application resources. If you apply this principle, you can typically perform an update by using a new deployment and standard delivery pipelines. For general operational update guidance, see [Standardizing operations](../operational-excellence/formalize-operations-tasks.md).

### Design recommendations

- Update the Kubernetes version in Azure Kubernetes Service (AKS) regularly, especially given that support for older versions isn't sustained. Also update components that run on Kubernetes, like cert-manager and the Azure Key Vault CSI, and align them with the Kubernetes version in AKS. For configuration details, see the [Kubernetes Service service guide](../service-guides/azure-kubernetes-service.md).

- Avoid manual operational changes to update components. Consider the use of manual changes only in emergency situations. Ensure that you have a process for reconciling any manual changes back into the source repository to avoid drift and issue recurrence.

- Establish an automated procedure for [removing old versions of images from Azure Container Registry](/azure/container-registry/container-registry-auto-purge).

## Secret management

Key, secret, and certificate expirations are a common cause of application outage. For general secret management guidance, see [Protecting application secrets](../security/application-secrets.md) and [Data encryption strategies](../security/encryption.md).

For mission-critical workloads, the choice of when secrets are retrieved (deployment-time, startup, or runtime) directly affects availability. A runtime dependency on the secret management solution means that Key Vault outages can block application restarts and scale-out operations.

### Design recommendations

- When possible, use Microsoft Entra authentication instead of connection strings or keys, together with Azure managed identities.

- Deploy Key Vault instances as part of a regional stamp to mitigate the potential effect of a failure to a single deployment stamp. Use a separate instance for global resources. For information about those resources, see the typical [architecture pattern](mission-critical-architecture-pattern.md) for mission-critical workloads.

- Implement coding patterns to ensure that secrets are re-retrieved when an authorization failure occurs at runtime.

- Apply a fully automated key-rotation process that runs periodically within the solution.

 - Use the [key near expiry notification in Azure Key Vault](/azure/key-vault/keys/how-to-configure-key-rotation#configure-key-near-expiry-notification) to get alerts about upcoming expirations.

## IaaS-specific considerations when using VMs

For mission-critical workloads, PaaS services are preferred over IaaS VMs because they offload operational burden to the platform. If you need to use VMs, see the [Virtual Machines service guide](../service-guides/virtual-machines.md) for configuration guidance.

### Design recommendations

- Prioritize the use of [Virtual Machine Scale Sets](../service-guides/virtual-machines.md) rather than individual VMs to enable scale, autoscale, and zone redundancy.

- Avoid manual operations on VMs and implement proper processes to deploy and roll out changes. Automate provisioning by using infrastructure-as-code solutions like Bicep or Terraform.

- Ensure that operational processes for deployment of VMs, updates, and backup and recovery are in place and properly tested. To test for resiliency, inject faults into the application, note failures, and mitigate those failures.

## Next step

Review the architecture pattern for mission-critical application scenarios:

> [!div class="nextstepaction"]
> [Architecture pattern](mission-critical-architecture-pattern.md)
