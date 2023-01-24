---
title: Operational procedures for mission-critical workloads on Azure
description: This design area explores reliable and effective operations through DevOps processes, with automated deployment pipelines that are used to run versioned application and infrastructure code artifacts within a source repository.
author: calcof
ms.author: calcof
ms.date: 01/24/2023
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories: devops
products: azure
ms.custom:
  - mission-critical
---

# Operational procedures for mission-critical workloads on Azure

Reliable and effective operations must be based on the principles of *automation wherever possible* and *configuration as code*. This approach requires a substantial engineering investment in DevOps processes with automated pipelines used to deploy application and infrastructure code artifacts. There are significant benefits enabling consistent and accurate operational outcomes within minimal manual operational procedures.

This design area explores how to drive effective and consistent operational procedures.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload)

## DevOps processes

_DevOps_ combines development and operational processes and teams into a single engineering function. It encompasses the entire application lifecycle, using automation and DevOps tooling to conduct deployment operations in a fast, efficient, and reliable manner. DevOps processes support and sustain the concepts of continuous integration and continuous deployment (CI/CD), while fostering a culture of continuous improvement.

The DevOps team for a mission-critical application must be responsible for these tasks:
  - Create and manage application and infrastructure resources through CI/CD automation.
  - Application monitoring and observability.
  - Azure RBAC and identity management for application components.
  - Network management for application components.
  - Cost management for application resources.

_DevSecOps_ expands the DevOps model by integrating security monitoring, application audit, and quality assurance with development and operations throughout the application lifecycle. DevOps teams are needed for security sensitive and highly regulated scenarios, to ensure security is baked within the DNA of engineering team throughout the development lifecycle rather than a specific release stage/gate.

### Design considerations

- **Release and update process**. Manual processes for any change to application component or underlying infrastructure must be avoided because they might lead to inconsistent results. 

- **Dependencies on central IT teams**. DevOps processes can be difficult to apply when there are hard dependencies on centralized functions because it prevents end-to-end operations.

- **Identity and access management**. A DevOps team can consider various granular Azure RBAC roles for different technical functions, such as AppDataOps for database management. A zero-trust model should be applied across different DevOps roles.

### Design recommendations

- Define configuration settings and updates as code. Any change management must be done through  code to enable consistent release and update process, including tasks such as key or secret rotation and permission management. Use pipeline-managed update processes, such as with scheduled pipeline runs, over built-in auto-update mechanisms.

- Do not use central processes or provisioning pipelines for the instantiation or management of application resources. This will introduce external application dependencies and additional risk vectors, such as those associated with 'noisy neighbor' scenarios.

  If centralized provisioning processes are mandated, make sure that the availability requirements of the dependencies are fully aligned with mission-critical requirements. Central teams must provide  transparency so that holistic operationalization of the end-to-end application is achieved.

- Dedicate a proportion of engineering capacity within each sprint to drive fundamental platform improvements and bolster reliability. Allocating 20-40% of capacity is recommended. 

- Develop common engineering criteria and reference architectures/libraries that are aligned with core [design principles](mission-critical-design-principles.md). Enforce a consistent baseline configuration for reliability, security, and operations through a policy-driven approach using Azure Policy.

  > This common engineering criteria and associated artifacts, such as Azure Policies and Terraform for common design patterns, can also be used across other workloads within the broader application ecosystem for an organization.

- Apply a zero-trust model within critical application environments, using capabilities such as Azure AD Privileged Identity Management (PIM) to ensure consistent operations only occur through CI/CD processes or automated operational procedures. 

  Team members shouldn’t have standing write-access to any environment. There might be exceptions for  development environments for easier testing and debugging.

- Define emergency processes for Just-in-time access to production environments. Ensure 'break glass' accounts exist for serious issues with the authentication provider.

- Consider AIOps as a method to continually improve operational procedures and triggers.

## Application operations

The application design and platform recommendations influence operational procedures. There’s also operational capabilities provided by different Azure services, particularly when it comes to high availability and recovery. 

### Design considerations

- **Built-in operations of Azure services**. Azure services provide built-in (enabled by default) and configurable platform capabilities, such as zonal redundancy or geo-replication. The application’s reliability is dependent on those operational procedures. Certain configurable capabilities incur an additional cost, such as the multi-write deployment configuration for Azure Cosmos DB. Avoid building custom solutions unless absolutely necessary.

- **Operational access and execution time**.  Most of the required operations are exposed and accessible through the Azure ARM management APIs or through the Azure portal. However, certain operations require assistance from Support Engineers. For example, restore from a periodic backup of an Azure Cosmos DB database or the recovery of a deleted resource, can only be performed by Azure Support engineers through a support case. This dependency might impact the downtime of the application. For stateless resources, redeployment is preferred instead of waiting for the Support process to attempt recovery of the deleted resource.

- **Policy enforcement**. Azure Policy provides a framework to enforce and audit security and reliability baselines, ensuring continued compliance with common engineering criteria for a mission-critical application. More specifically, Azure Policy forms a key part of the Azure Resource Manager (ARM) control plane, supplementing RBAC by restricting what actions authorized users can perform, and can be used to enforce vital security and reliability conventions across utilized platform services.

- **Modification and deletion of resources**. Azure [resources can be locked](/azure/azure-resource-manager/management/lock-resources) to prevent them from being modified or deleted. However, locks introduce management overhead within deployment pipelines. For most resources, a robust RBAC process with tight restrictions, is preferable over locking resources.

### Design recommendations

- Automate failover procedures. For an active-active, use a health model and automated scale-operations to ensure no failover intervention is required. For an active-passive model, ensure failover procedures are automated or at least codified within pipelines.

- Prioritize the use of Azure-native autoscaling for services that support this capability. For services that don’t, have automated operational processes to scale services. Use scale-units with multiple services to achieve scalability.

- Use platform-native capabilities for backup and restore, ensuring they're aligned with RTO/RPO and data retention requirements. Define a strategy for long-term backup retention if required.

- Use built-in capabilities for SSL certificate management and renewal, such as those offered by Azure Front Door.

- Establish a recovery process for resources that require assistance for external teams. For example, if the data platform is incorrectly modified or deleted, the possible ways of recovery should be well understood with recovery process in place. Similarly, establish procedures to manage decommissioned container images in the registry.

- Practice recovery operations in advance, on non-production resources and data, as part of standard business continuity preparations.

- Identify critical alerts and define target audiences and systems, with clear channels to reach them. Send only sending actionable alerts avoiding white noise, to prevent operational stakeholders for ignoring alerts and missing important information. Leverage continuous improvement to optimize alerting and remove observed 'white-noise'.

- Apply policy-driven governance and Azure Policy to ensure the appropriate use of operational capabilities and a reliable configuration baseline across all application services.

- Avoid the use of resource locks on ephemeral regional resources, and instead rely on the appropriate use of Role Based Access Control (RBAC) and CI/CD pipelines to control operational updates. Resource locks can be applied to prevent the deletion of long-lived global resources

## Update management of resources
Mission critical design strongly endorses the principle of ephemeral stateless application resources, meaning that updates can typically be performed through a new deployment and standard delivery pipelines.

### Design considerations

- **Alignment with Azure roadmaps**. The workload must be aligned with Azure roadmaps so that platform resources and runtime dependencies are updated regularly. Similarly, 

- **Automatic detection of updates**. Set up processes to monitor and automatically detect updates, such as [GitHub's Dependabot](https://github.com/dependabot).

- **Testing and validation**. Newer versions might contain breaking changes. New versions of packages, components, and dependencies must be properly tested and validated in a production context before any release.

- **Runtime dependencies**. These components must be treated as any other change to the application. Older versions might introduce security vulnerabilities and may have a negative impact on performance. Application runtime environment should be monitored and kept up to date.

- **Component hygiene and housekeeping**. Resources that aren’t used must be decommissioned or removed. For example, container registries will need regular housekeeping to delete old image versions that aren't used anymore.

### Design recommendations

- These resources must be monitored and kept up to date:

- Application hosting platform. For example, Kubernetes version within AKS needs to be updated regularly, especially given support for older versions isn't sustained. Components running on K8s also need to be updated, such as cert-manager and Key Vault-csi and aligned with the k8s version within AKS.
  - External libraries, SDKs(.NET, Java, Python). 
  - Terraform providers.

- Manual operational changes to update components should be avoided and only considered by emergency exception. Ensure a process exists to reconcile any manual changes back into the source repository, avoiding drift and issue recurrence.

- Establish an automated housekeeping procedure to [remove old image versions from Azure Container Registry](/azure/container-registry/container-registry-auto-purge).
## Secret management

Key, secret, and certificate expirations are common causes of application outage. Secret management for a mission-critical application must provide the needed security and offer an appropriate level of availability to align with maximum reliability aspirations. Key, secret, and certificate rotation must be performed on a regular basis through a managed service or as part of update management with process applied for code and configuration changes.

Many Azure services already support Azure AD authentication instead of relying on connection strings / keys. Doing so greatly reduces the operational overhead. If a secret management solution is used, it should integrate with other services, support zonal and regional redundancy, and offer integration with Azure Active Directory for authentication and authorization. Azure Key Vault supports all features. 

### Design considerations
There are three common approaches based on when secrets are read from the selected secret store and injected into the application.

- **Deployment-Time Retrieval**. The advantage is that the secret management solution only needs to be available at deployment time because there aren’t direct dependencies after this point. For example, injecting secrets as environment variables into a Kubernetes deployment or into a Kubernetes secret.

  Only the deployment service principal needs to be able to access secrets, which simplifies RBAC permissions within the secret management system. 

  However, there are some tradeoffs. It introduces additional RBAC considerations within DevOps tooling around controlling service principal access and the application in terms of protecting retrieved secrets.  Also, the security benefits of the secret management solution aren't utilized because this approach relies only on access control in the application platform.

  Secret updates or rotation will require a full redeployment in order to take effect.

- **Application start-up retrieval**. In this approach, secrets are retrieved and injected at startup. The benefit is that secrets can be updated or rotated easily. A restart of the application is required to fetch the latest value. 
  
  Secrets are stored in memory, and you don’t need to store them on the application platform. For example, a common choice is [CSI SecretStore driver for KeyVault](https://azure.github.io/secrets-store-csi-driver-provider-azure/) and [akv2k8s](https://akv2k8s.io/).
 	A native Azure solution [Azure Key Vault referenced App Settings](/azure/app-service/app-service-key-vault-references) is also available.

  A downside is that it creates a runtime dependency on the secret management solution. If the secret management solution experiences an outage, application components already running **may** be able to continue serving requests, however, any restart or scale-out operations will likely result in failure.

- **Runtime retrieval**. Retrieving secrets at runtime from within the application itself serves as the most secure approach because even the application platform never has access to secrets. The application needs to authenticate itself to the secret management system.

	However, application components require a direct dependency and a connection to the secret management system. This makes it harder to test components individually and usually requires the use of an SDK.

### Design recommendations

- Use Azure AD authentication, when possible, to connect to other services instead of using connection strings or keys. Use this in conjunction with Azure Managed Identities to remove the need for any secrets to be stored on the application platform.

- Take advantage of the expiry setting in Azure Key Vault and have [alerting configured](/azure/key-vault/general/event-grid-tutorial) for upcoming expirations.  All key, secret, and certificate updates should be performed using the standard release process.

- Deploy Azure Key Vault instances as part of a regional stamp to mitigate the potential impact of a failure to a single deployment stamp. Use a separate instance for global resources. For information about those resources, see the typical [architecture pattern](mission-critical-architecture-pattern.md) for mission-critical workloads. 

- Use Managed identities instead of service principals to access Key Vault whenever possible to avoid management of Service Principal credentials or API keys.

- Retrieve secrets at application startup, not during deployment time or at runtime.

- Implement coding patterns so when an authorization failure occurs at runtime, secrets are re-retrieved.

- Apply a fully automated key-rotation process that runs periodically within the solution.

 - Use [key near expire notification](/azure/key-vault/keys/how-to-configure-key-rotation#configure-key-near-expiry-notification) to get alerted on upcoming expiration.


## IaaS specific considerations when using Virtual Machines

In scenarios where the use of IaaS Virtual Machines is required, some of the procedures and practices described above might differ. The use of Virtual Machines provides more flexibility in regards to configuration options, selection of operating systems, access to drivers and low-level operating system access as well as to the kind of software that can be installed. The price for that are increased operational costs and responsibility for tasks that are usually done by the cloud provider when using PaaS services.

### Design considerations

- Individual VMs do not provide high availability, zone or geo-redundancy.
- Individual VMs are not automatically updated once deployed.
- Services running inside a VM need special treatment and additional tooling to be deployed and configured via Infra-as-Code.
- Azure periodically updates its platform and might require a reboot. These reboots are usually announced in advance. See [Maintenance for virtual machines in Azure](/azure/virtual-machines/maintenance-and-updates) and [Handling planned maintenance notifications](/azure/virtual-machines/maintenance-notifications).

### Design recommendations

- Avoid any manual operations on virtual machines and implement proper processes to deploy and rollout changes. 
  - Automate the provisioning of Azure resources using Infrastructure-as-Code solutions, such as Azure Resource Manager templates, Bicep, Terraform, or other third-party solutions.

- Make sure that operational processes for deployment of virtual machines, updates, backup and recovery are in place and properly tested. To test for resiliency inject fault in application and take a note of failure and mitigate those failures.

- Ensure that strategies are in place to rollback changes to last known healthy state in case a newer version is not functioning correctly.

- Take frequent backups for stateful workloads and ensure backup tasks are working effectively and alert on failed backup processes.

- Monitor virtual machines and detect for failure. The raw data of for monitoring can come from variety of sources. Ensure that monitoring is configured and analyze the cause of problems.

- Analyze that scheduled backups are running as expected and that periodic backups are taken (if needed). Azure [Backup center](/azure/backup/backup-center-overview) can be used to gain more insights.

- Prioritize the use of Virtual Machine Scale Sets over VMs to enable capabilities like scale, autoscale and provide zone-redundancy.

- Prioritize the use of standard images from the Azure Marketplace over custom images that need to be maintained.

- Use [Azure VM Image Builder](/azure/virtual-machines/image-builder-overview) or other tools to automate build and maintenance processes for customized images if required.

Besides these specifics, apply best practices for operational procedures for mission-critical application scenarios when applicable.

## Next step

Review the cross-cutting concerns for mission-critical application scenarios.

> [!div class="nextstepaction"]
> [Architecture pattern](mission-critical-architecture-pattern.md)
