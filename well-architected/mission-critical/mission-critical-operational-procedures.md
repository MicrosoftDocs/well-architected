---
title: Operational procedures for mission-critical workloads on Azure
description: Learn about implementing reliable and effective operations by using DevOps processes. Use automated deployment pipelines to run versioned application and infrastructure code artifacts within a source repository.
author: calcof
ms.author: prwilk
ms.date: 01/26/2023
ms.topic: conceptual
categories: devops
ms.custom:
  - mission-critical
---

# Operational procedures for mission-critical workloads on Azure

Reliable and effective operations must be based on the principles of *automation wherever possible* and *configuration as code*. This approach requires a substantial engineering investment in DevOps processes. Automated pipelines are used to deploy application and infrastructure code artifacts. The benefits of this approach include consistent and accurate operational outcomes with minimal manual operational procedures.

This design area explores how to implement effective and consistent operational procedures.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload).

## DevOps processes

_DevOps_ combines development and operational processes and teams into a single engineering function. It encompasses the entire application lifecycle and uses automation and DevOps tooling to conduct deployment operations in a fast, efficient, and reliable way. DevOps processes support and sustain continuous integration and continuous delivery (CI/CD) while fostering a culture of continuous improvement.

The DevOps team for a mission-critical application must be responsible for these tasks:
  - Creation and management of application and infrastructure resources via CI/CD automation.
  - Application monitoring and observability.
  - Azure role-based access control (RBAC) and identity management for application components.
  - Network management for application components.
  - Cost management for application resources.

_DevSecOps_ expands the DevOps model by integrating security monitoring, application audits, and quality assurance with development and operations throughout the application lifecycle. DevOps teams are needed for security-sensitive and highly regulated scenarios to ensure that security is incorporated throughout the development lifecycle rather than at a specific release stage or gate.

### Design considerations

- **Release and update process**. Avoid manual processes for any change to application components or underlying infrastructure. Manual processes can lead to inconsistent results. 

- **Dependencies on central IT teams**. DevOps processes can be difficult to apply when there are hard dependencies on centralized functions because these dependencies prevent end-to-end operations.

- **Identity and access management**. DevOps teams can consider granular Azure RBAC roles for various technical functions, like AppDataOps for database management. Apply a zero-trust model across DevOps roles.

### Design recommendations

- Define configuration settings and updates as code. Apply change management through  code to enable consistent release and update processes, including tasks like key or secret rotation and permissions management. Use pipeline-managed update processes, like scheduled pipeline runs, rather than built-in auto-update mechanisms.

- Don't use central processes or provisioning pipelines for the instantiation or management of application resources. Doing so introduces external application dependencies and additional risk vectors, like those associated with noisy neighbor scenarios.

  If you need to use centralized provisioning processes, ensure that the availability requirements of the dependencies are fully aligned with mission-critical requirements. Central teams must provide transparency so that holistic operationalization of the end-to-end application is achieved.

- Dedicate a proportion of engineering capacity during each sprint to driving fundamental platform improvements and bolstering reliability. We recommend that you allocate 20-40 percent of capacity to these improvements. 

- Develop common engineering criteria, reference architectures, and libraries that are aligned with core [design principles](mission-critical-design-principles.md). Enforce a consistent baseline configuration for reliability, security, and operations via a policy-driven approach that uses Azure Policy.

  You can also use the common engineering criteria and associated artifacts, like Azure policies and Terraform resources for common design patterns, across other workloads within your organization's broader application ecosystem.

- Apply a zero-trust model in critical application environments. Use technologies like Azure AD Privileged Identity Management to ensure that operations are consistent and occur only through CI/CD processes or automated operational procedures. 

  Team members shouldn't have standing write access to any environment. You might want to make exceptions in development environments to enable easier testing and debugging.

- Define emergency processes for just-in-time access to production environments. Ensure that break glass accounts exist in case of serious problems with the authentication provider.

- Consider using AIOps to continually improve operational procedures and triggers.

## Application operations

The [application design](mission-critical-application-design.md) and [platform](mission-critical-application-platform.md) recommendations influence operational procedures. There are also operational capabilities provided by various Azure services, particularly for high availability and recovery.

### Design considerations

- **Built-in operations of Azure services**. Azure services provide built-in (enabled by default) and configurable platform capabilities, like zonal redundancy and geo-replication. An application's reliability depends on these operations. Certain configurable capabilities incur an additional cost, like the multi-write deployment configuration for Azure Cosmos DB. Avoid building custom solutions unless you absolutely need to.

- **Operational access and execution time**. Most required operations are exposed and accessible through the Azure Resource Manager API or the Azure portal. However, certain operations require assistance from support engineers. For example, a restore from a periodic backup of an Azure Cosmos DB database, or the recovery of a deleted resource, can be performed only by Azure support engineers via a support case. This dependency might affect the downtime of the application. For stateless resources, we recommend that you redeploy instead of waiting for support engineers to try to recover deleted resources.

- **Policy enforcement**. Azure Policy provides a framework for enforcing and auditing security and reliability baselines to ensure compliance with common engineering criteria for mission-critical applications. More specifically, Azure Policy forms a key part of the Azure Resource Manager control plane, supplementing RBAC by restricting the actions that authorized users can perform. You can use Azure Policy to enforce vital security and reliability conventions across platform services.

- **Modification and deletion of resources**. You can [lock Azure resources](/azure/azure-resource-manager/management/lock-resources) to prevent them from being modified or deleted. However, locks introduce management overhead in deployment pipelines. For most resources, we recommend a robust RBAC process with tight restrictions rather than resource locks.

### Design recommendations

- Automate failover procedures. For an active/active model, use a health model and automated scale operations to ensure that no failover intervention is required. For an active/passive model, ensure that failover procedures are automated or at least codified within pipelines.

- Prioritize the use of Azure-native autoscaling for services that support it. For services that don't support native autoscaling, use automated operational processes to scale services. Use scale units with multiple services to achieve scalability.

- Use platform-native capabilities for backup and restore, ensuring that they're aligned with your RTO/RPO and data retention requirements. Define a strategy for long-term backup retention as needed.

- Use built-in capabilities for SSL certificate management and renewal, like those provided by Azure Front Door.

- For external teams, establish a recovery process for resources that require assistance. For example, if the data platform is incorrectly modified or deleted, the recovery methods should be well understood, and a recovery process should be in place. Similarly, establish procedures to manage decommissioned container images in the registry.

- Practice recovery operations in advance, on non-production resources and data, as part of standard business continuity preparations.

- Identify critical alerts and define target audiences and systems. Define clear channels to reach appropriate stakeholders. Send only actionable alerts to avoid white noise and prevent operational stakeholders from ignoring alerts and missing important information. Implement continuous improvement to optimize alerting and remove observed white noise.

- Apply policy-driven governance and Azure Policy to ensure the appropriate use of operational capabilities and a reliable configuration baseline across all application services.

- Avoid the use of resource locks on ephemeral regional resources. Instead, rely on the appropriate use of RBAC and CI/CD pipelines to control operational updates. You can apply resource locks to prevent the deletion of long-lived global resources.

## Update management

Mission-critical design strongly endorses the principle of ephemeral stateless application resources. If you apply this principle, you can typically perform an update by using a new deployment and standard delivery pipelines.

### Design considerations

- **Alignment with Azure roadmaps**. Align your workload with Azure roadmaps so that platform resources and runtime dependencies are updated regularly.

- **Automatic detection of updates**. Set up processes to monitor and automatically detect updates. Use tools like [GitHub Dependabot](https://github.com/dependabot).

- **Testing and validation**. Test and validate new versions of packages, components, and dependencies in a production context before any release. New versions might contain breaking changes. 

- **Runtime dependencies**. Treat runtime dependencies like you would any other change to the application. Older versions might introduce security vulnerabilities and might have a negative effect on performance. Monitor the application runtime environment and keep it up to date.

- **Component hygiene and housekeeping**. Decommission or remove resources that aren't used. For example, monitor container registries and delete old image versions that you aren't using.

### Design recommendations

- Monitor these resources and keep them up to date:

   - The application hosting platform. For example, you need to update the Kubernetes version in Azure Kubernetes Service (AKS) regularly, especially given that support for older versions isn't sustained. You also need to update components that run on Kubernetes, like cert-manager and the Azure Key Vault CSI, and align them with the Kubernetes version in AKS.
  - External libraries and SDKs (.NET, Java, Python). 
  - Terraform providers.

- Avoid manual operational changes to update components. Consider the use of manual changes only in emergency situations. Ensure that you have a process for reconciling any manual changes back into the source repository to avoid drift and issue recurrence.

- Establish an automated procedure for [removing old versions of images from Azure Container Registry](/azure/container-registry/container-registry-auto-purge).

## Secret management

Key, secret, and certificate expirations are a common cause of application outage. Secret management for a mission-critical application must provide the needed security and offer an appropriate level of availability to align with your maximum-reliability requirements. You need to perform key, secret, and certificate rotation on a regular basis by using a managed service or as part of update management, and apply processes for code and configuration changes.

Many Azure services support Azure Active Directory (Azure AD) authentication instead of relying on connection strings / keys. Using Azure AD greatly reduces operational overhead. If you do use a secret management solution, it should integrate with other services, support zonal and regional redundancy, and provide integration with Azure AD for authentication and authorization. Key Vault provides these features. 

### Design considerations

There are three common approaches to secret management. Each approach reads secrets from the secret store and injects them into the application at a different time.

- **Deployment-time retrieval**. The advantage to this approach is that the secret management solution needs to be available only at deployment time because there aren't direct dependencies after that time. Examples include injecting secrets as environment variables into a Kubernetes deployment or into a Kubernetes secret.

  Only the deployment service principal needs to be able to access secrets, which simplifies RBAC permissions within the secret management system. 

  There are, however, disadvantages to this approach. It introduces RBAC complexity in DevOps tooling with regard to controlling service principal access and in the application with regard to protecting retrieved secrets. Also, the security benefits of the secret management solution aren't applied because this approach relies only on access control in the application platform.

  To implement secret updates or rotation, you need to perform a full redeployment.

- **Application-startup retrieval**. In this approach, secrets are retrieved and injected at application startup. The benefit is that you can easily update or rotate secrets. You don't need to store secrets on the application platform. A restart of the application is required to fetch the latest value. 
  
   Common storage choices include [Azure Key Vault Provider for Secrets Store CSI Driver](https://azure.github.io/secrets-store-csi-driver-provider-azure) and [akv2k8s](https://akv2k8s.io). A native Azure solution, [Key Vault referenced app settings](/azure/app-service/app-service-key-vault-references), is also available.

  A disadvantage of this approach is that it creates a runtime dependency on the secret management solution. If the secret management solution experiences an outage, application components already running *might* be able to continue serving requests. Any restart or scale-out operation would likely result in failure.

- **Runtime retrieval**. Retrieving secrets at runtime from within the application itself is the most secure approach because even the application platform never has access to secrets. The application needs to authenticate itself to the secret management system.

	However, for this approach, application components require a direct dependency and a connection to the secret management system. This makes it harder to test components individually and usually necessitates the use of an SDK.

### Design recommendations

- When possible, use Azure AD authentication to connect to services instead of using connection strings or keys. Use this authentication method together with Azure managed identities so you don't need to store secrets on the application platform.

- Take advantage of the expiry setting in Key Vault, and [configure alerting](/azure/key-vault/general/event-grid-tutorial) for upcoming expirations. Perform all key, secret, and certificate updates by using the standard release process.

- Deploy Key Vault instances as part of a regional stamp to mitigate the potential effect of a failure to a single deployment stamp. Use a separate instance for global resources. For information about those resources, see the typical [architecture pattern](mission-critical-architecture-pattern.md) for mission-critical workloads. 

- To avoid the need to manage service principal credentials or API keys, use managed identities instead of service principals to access Key Vault whenever possible.

- Implement coding patterns to ensure that secrets are re-retrieved when an authorization failure occurs at runtime.

- Apply a fully automated key-rotation process that runs periodically within the solution.

 - Use the [key near expiry notification in Azure Key Vault](/azure/key-vault/keys/how-to-configure-key-rotation#configure-key-near-expiry-notification) to get alerts about upcoming expirations.

## IaaS-specific considerations when using VMs

If you need to use IaaS VMs, some of the procedures and practices described earlier in this document might differ. The use of VMs provides more flexibility in configuration options, operating systems, driver access, low-level operating system access, and the kinds of software that you can install. The disadvantages are increased operational costs and the responsibility for tasks that are usually performed by the cloud provider when you use PaaS services.

### Design considerations

- Individual VMs don't provide high availability, zone redundancy, or geo-redundancy.
- Individual VMs aren't automatically updated after you deploy them. For example, a deployed SQL Server 2019 on Windows Server 2019, won't automatically get updated to a newer release. 
- Services running in a VM need special treatment and additional tooling if you want to deploy and configure them via infrastructure as code.
- Azure periodically updates its platform. These updates might require VM reboots. Updates that require a reboot are usually announced in advance. See [Maintenance for virtual machines in Azure](/azure/virtual-machines/maintenance-and-updates) and [Handling planned maintenance notifications](/azure/virtual-machines/maintenance-notifications).

### Design recommendations

- Avoid manual operations on VMs and implement proper processes to deploy and roll out changes. 
  - Automate the provisioning of Azure resources by using infrastructure-as-code solutions like Azure Resource Manager (ARM) templates, Bicep, Terraform, or other solutions.

- Ensure that operational processes for deployment of VMs, updates, and backup and recovery are in place and properly tested. To test for resiliency, inject faults into the application, note failures, and mitigate those failures.

- Ensure that strategies are in place to roll back to the last known healthy state if a newer version doesn't function correctly.

- Create frequent backups for stateful workloads, ensure that backup tasks work effectively, and implement alerts for failed backup processes.

- Monitor VMs and detect for failures. The raw data for monitoring can come from a variety of sources. Analyze the causes of problems.

- Ensure that scheduled backups run as expected and that periodic backups are created as needed. You can use [Backup center](/azure/backup/backup-center-overview) to get insights.

- Prioritize the use of Virtual Machine Scale Sets rather than VMs to enable capabilities like scale, autoscale, and zone redundancy.

- Prioritize the use of standard images from Azure Marketplace rather than custom images that need to be maintained.

- Use [Azure VM Image Builder](/azure/virtual-machines/image-builder-overview) or other tools to automate build and maintenance processes for customized images as needed.

Beyond these specific recommendations, apply best practices for operational procedures for mission-critical application scenarios as appropriate.

## Next step

Review the architecture pattern for mission-critical application scenarios:

> [!div class="nextstepaction"]
> [Architecture pattern](mission-critical-architecture-pattern.md)
