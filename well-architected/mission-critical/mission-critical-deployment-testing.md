---
title: Deployment and testing for mission-critical workloads on Azure
description: This design area focuses on how to eradicate downtime and maintain application health for deployment operations, by providing key considerations and recommendations that are intended to inform the design of optimal CI/CD pipelines for a mission-critical application.
author: calcof
ms.author: calcof
ms.date: 02/28/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - devops
  - containers
  - management-and-governance
products: Azure
ms.custom:
  - mission-critical
---

# Deployment and testing for mission-critical workloads on Azure

Application outages are often caused by failed deployments or erroneous releases. This is why the design of Continuous Integration and Continuous Deployment (CI/CD) pipelines for deployment and testing methodologies play a critical role in the overall reliability of a mission-critical application.

Deployment and testing shouldn't be constrained to the delivery of planned application updates, but instead should form the basis for how all application and infrastructure operations are conducted to ensure consistent outcomes for mission-critical workloads. The variety of deployment contexts results in a frequent, and often daily, deployment cadence. Therefore using CI/CD pipelines to exhibit maximum reliability is highly recommended because they perform a critical operational function for a mission-critical application. The strategy should aspire for:

- **Rigorous pre-release testing**. Updates shouldn't introduce defects, vulnerabilities, or other factors that might jeopardize application health.
- **Transparent deployments**. All clients and users should be able to continue application interaction without interruption using a zero-downtime deployment approach.
- **Highly available operations**. Deployment and testing processes must themselves be highly available to support overall application reliability.
- **End-to-End automation**. Manual intervention in the technical execution of deployment and testing operations represents a significant reliability risk.
- **Consistent deployment process**. Same application artifacts and processes are used to deploy the infrastructure  and  application code across different environments.

This design area focuses on how to eradicate downtime and maintain application health for deployment operations, providing key considerations and recommendations intended to inform the design of optimal CI/CD pipelines for a mission-critical application.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload)
>
> ![GitHub logo](./../_images/github.svg) [Mission-Critical open source project](http://github.com/azure/mission-critical)
>
> The [reference implementations](mission-critical-overview.md#illustrative-examples) are part of an open source project available on GitHub. The code assets illustrate considerations and recommendations for acheiving optimal CI/CD pipelines for a mission-critical application.

## Demo: Continuous validation with Azure Load Test and Azure Chaos Studio

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Y50k]

## Application environments

Before considering deployment processes and associated tooling, it's important to evaluate the application environments required to appropriately validate and stage deployment operations. These environment types will most likely differ in terms of requisite capabilities and longevity. Some environments might reflect production on a permanent basis, others may be short lived with a reduced level of complexity. These environments should be staged during the engineering and release cycle in order to ensure deployment operations are fully tested before released into the production environment.

This section explores the key considerations and recommendations for application environments in a mission-critical context, covering key design objectives such as developer agility and separation of concerns.

### Design Considerations

**Development environments**

- Development environments will typically not share the same reliability, capacity, and security requirements as the production environment.

- Given the reduced scale, reliability, and security requirements of a development environment, they can more easily coexist within a single subscription.

- It's likely that engineering teams will require multiple development environments to support the completion of parallel feature development.

- Development environments must be available when required, but need not exist permanently and typically only exist for short periods of time. Keeping environments short lived saves costs, and prevents configuration drift from the code base. Also, development environments often share the lifecycle of a feature branch.

- Development environments can also encompass the development of Infrastructure-as-Code (IaC) artifacts such as Terraform or Azure Resource Manager (ARM) templates.

**Staging Environments**

- Staging environments can vary depending on their intended function within the release cycle. They  typically resemble the requirements of the production environment for reliability, capacity, and security.

- Staging environments can be used for various purposes, but the focus is testing and validation, with a multitude of test cycles considered. Such as,
  - Load and performance testing.
  - Chaos testing.
  - Integration and build verification testing.
  - User acceptance testing.
  - Security and penetration testing.

- Different test functions can be performed within the same environment, and in some cases this will be required. For example, for chaos testing to provide meaningful results, the application must first be placed under load to be able to understand how the application responds to injected faults. Chaos testing and load testing are therefore typically performed in parallel.

- During the early development cycles and in absence of a production load, a constant synthetic user load against an environment should be used to provide realistic metrics and with valuable health modeling input.

**Production environments**

- Some applications may consider multiple different production environments to cater to different clients, users, or business functionality.

### Design recommendations

- Ensure all environments reflect the production environment as much as possible, with simplifications applied for lower environments as necessary.

- Keep production environments separate from lower environments into a dedicated subscription. This helps to ensure resource utilization in lower-environments doesn't impact production quotas, and to provide a clear governance boundary and separation of concerns. Depending on the scale requirements of the application, multiple production subscriptions might be needed to serve as scale-units.

- Separate development environments within a distinct subscription context, with all development environments sharing the same subscription.
  - Ensure that there's an automated process to deploy code from a feature branch to a development environment.
  - Treat development environments as ephemeral, sharing the lifecycle of the associated feature branch.

- Define the number of staging environments and their purpose within the development and release cycle.

- Avoid sharing components between environments. Possible exceptions are downstream security appliances like firewalls, or source locations for synthetic test data.

- Ensure at least one staging environment is fully reflective of production to enable production-like testing and validation.
  - Capacity within this pre-production environment can flex based on the execution of test activities.
  - Use of a constant synthetic user load generation is required to provide a realistic test case for changes on one of the pre-production environments.
    - The [Mission Critical Online](https://github.com/Azure/Mission-Critical-Online) reference implementation provides an example [user load generator](https://github.com/Azure/Mission-Critical-Online/blob/main/src/testing/userload-generator/README.md).

![Mission Critical Azure Subscription Organization](./images/mission-critical-subscription-organization.png)

## Ephemeral blue/green deployments

A blue/green deployment approach requires a minimum of two identical deployment contexts, where an existing deployment (blue) is actively serving user traffic, and a new secondary deployment (green) is established and made ready to receive traffic.

- After the new deployment is completed and tested, traffic is gradually switched from the blue deployment to the green.
- If the load transfer is successful, that new deployment becomes the new 'active' production environment and the old, now 'inactive' deployment can be decommissioned.
- If there are issues within the new deployment environment, the deployment can be aborted and traffic can either remain in the old 'active' deployment, or be directed back to it.
- This provides a clear fallback plan and minimizes potential for reliability issues, such as having to cut production traffic to rectify faulty deployment issues.

To achieve zero interruptions while performing deployments to a mission-critical application, it's strongly recommended to adopt a blue/green deployment approach for production environments with ephemeral resources. 

This allows new application code or resources to be deployed and tested in a new parallel environment, with traffic only transitioned once ready in a phased process before subsequently decommissioning the old environment.

### Design Considerations

A blue/green deployment can be implemented at either an application level or at the infrastructure level.

- **Application Level**: New code is deployed to a staging location within the existing infrastructure.
  - For example, Azure App Service provides this capability through secondary deployment slots that can be swapped after the deployment, while in AKS this can be achieved using a separate pod deployment on each node and updating the service definition.
  - This approach incurs less costs and is faster than a full infrastructure level blue/green deployment.

- **Infrastructure Level**: A deployment containing all infrastructure *and* application components within a deployment scope.
  - New Azure resources are established before subsequently deploying application code to the new infrastructure. When the new deployment has been fully tested and validated, traffic can be transitioned through a phased process, and the old infrastructure can then be decommissioned when appropriate.
  - The advantage of this approach is that all changes within the deployment scope are fully deployed and tested in production before traffic is transitioned between the environments. Also, this approach provides a much safer approach for any infrastructure-level changes within a release.
  - Individual deployments may take longer to complete using this methodology since it takes longer to deploy the infrastructure and application than deploying the application in isolation.
  - There's an additional cost associated with an infrastructure based approach, since two deployment contexts must exist side by side until the deployment is fully complete.

  >[!IMPORTANT]
  > This infrastructure blue/green approach allows all changes within a deployment scope, both to the infrastructure and application, to be achieved with zero downtime and maximum confidence. In addition all compatibilities with downstream dependencies such as Azure platform, resource providers or IaC modules can be validated.

- The blue and green environments can be long living and reused for each deployment, or treated as is recommended to deploy a new infrastructure for each new deployment.

- At an infrastructure level, the orchestration of user traffic between the blue and green environments can be controlled using a global load balancer, such as Azure Front Door.

### Design recommendations

- Utilize a blue/green deployment approach to release all production changes.
  - Prioritize an infrastructure level approach in order to achieve zero-downtime deployments and provide one consistent deployment strategy for any kind of changes (application-level and/or infrastructure-level). Use a global load balancer to orchestrate the automated transition of user traffic between the blue and green environments.
  - Add a green backend endpoint and using a low traffic volume/weight, such as 10%.
  - After verifying that the low traffic volume on green is being managed as expected with a maintained application health, the traffic can be gradually increased in increments until it reaches 100%.
  - While increasing traffic, a short ramp-up period should be applied to catch faults which may not come to light immediately.
  - After all traffic has been migrated to the new green environment, remove the blue backend from global load balancer service.
  - Decommission the old and inactive blue environment.
  - Repeat the process for the next deployment with blue and green reversed.

- While blue and green environments can be reused, it's strongly recommended to deploy new infrastructure for each new deployment. Treat each regional deployment stamp as ephemeral with a lifecycle tied to that of a single release.

- Decommission the old and inactive 'blue' environment, ensuring that any connections established while this environment was active are also closed and any queues are drained before removing associated resources. This will save costs relative to maintaining secondary production infrastructure and will ensure new environments are free of configuration drift.

- To prevent downtime, the process to control the transition of traffic between environments should be fully automated.

- Phase the transition of traffic between the blue/green environments to minimize client and user exposure whilst confidence is established in the new environment.

- Allow for a short ramp-up period when transitioning traffic between blue/green environments in order to catch faults which may not come to light immediately.

### Example - Zero-downtime deployment

Achieving zero-downtime deployments is a fundamental goal of a mission-critical application. However, it's a complex issue that requires significant engineering investment and greatly influences the overall design. It's important to invest effort up-front to define and plan deployment processes, to drive key design decisions such as whether to treat resources as ephemeral.

The [Mission-Critical Online](https://github.com/Azure/Mission-Critical-Online) and [Azure Mission-Critical Connected](https://github.com/Azure/Mission-Critical-Connected) reference implementations serve as practical examples for these concepts and recommendations, to establish an optimized zero-downtime deployment approach as represented in the illustration below.

![Zero-Downtime DevOps Pipeline Reference](./images/mission-critical-zero-downtime-pipeline.png "Zero-Downtime DevOps Pipeline Reference")

## Infrastructure-as-Code deployments

Infrastructure-as-Code (IaC) treats infrastructure definitions as source code that is version controlled alongside other application artifacts. Using IaC ensures code consistency across environments and eliminates the risk of human error during automated deployments, and providing traceability and rollback.

The recommended approach for infrastructure-level blue/green deployment is to use IaC, with fully automated and consistent infrastructure deployments.

### Design Considerations

- Typically a mission-critical IaC repository has two resource definitions:
  - Global Resources: those that are deployed once within the solution, such as Azure Front Door and Azure Cosmos DB.
  - Regional (*Stamp*) Resources: those that are deployed multiple times and into different regions, such as AKS, Event Hubs and Storage.

### Design Recommendations

- Apply the concept of IaC and ensure all Azure resources are defined in declarative templates and maintained in a source control repository from where they can be deployed automatically using CI/CD pipelines.

- Define infrastructure artifacts as declarative templates, and not as imperative scripts.

- Ensure the deployment of both infrastructure and application components are fully automated.

- Prohibit manual operations against production and lower environments. Only exception should be fully independent developer environments.

## DevOps Tooling

There are a myriad of different products and services that can provide the necessary DevOps capabilities to effectively deploy and manage a mission-critical application; Microsoft provides two Azure-native toolsets through GitHub _Actions_ and Azure DevOps (ADO) _Pipelines_.

The appropriate and effective use of used deployment tooling is critical to ensure overall reliability for an application, particularly because DevOps processes provide such a significant function within the overall application design. For example, failover and scale operations may depend on automation provided by DevOps tooling. Deployment tooling must therefore be implemented in a reliable and highly available manner, with engineering teams understanding the application impact if the deployment service, or parts of it, become unavailable.

This section focuses on the optimal use of GitHub Actions and Azure Pipelines and decision factors influencing the optimal selection of DevOps tooling.

### Design considerations

- The capabilities of GitHub _Actions_ and Azure DevOps (ADO) _Pipelines_ are largely overlapping.

- Different technologies can be used simultaneously to utilize the best features different technologies in parallel.
  - A common approach is to hold code repositories in GitHub.com or [GitHub AE](https://docs.github.com/en/github-ae@latest/admin/overview/about-github-ae) whilst using the deployment pipelines in ADO.
  - It should be noted that the use of multiple technologies adds an element of complexity and impacts the risk landscape.

**Azure Pipelines**

- Azure Pipelines provides highly mature deployment pipelines, including features like gates and approvals.

- ADO instances are hosted in a single Azure region that is chosen [at organization-level](/azure/devops/organizations/accounts/change-organization-location).
  - Data is replicated across regions but only for Disaster Recovery purposes.
  - Hosted build agents are utilized from the same region as the ADO instance.

- In the context of mission-critical aspiration for maximum reliability, the dependency on a single Azure region represents an operational risk. For example, consider a scenario where traffic is spread over West Europe and North Europe, with West Europe hosting the ADO instance. If West Europe experiences an outage, the ADO instance would also be affected. While North Europe would automatically now handle all application traffic, the ability to deploy additional scale-units to North Europe, in order to provide a consistent failover experience, would be prohibited which may result in a severely degraded application experience until the issue is resolved.

**GitHub Actions**

- GitHub.com is well known and adopted by developers and used for many open source projects.

- GitHub.com instances are also hosted in a single Azure region.
  - Data is replicated across regions but only for Disaster Recovery purposes.

- A private and dedicated [GitHub AE](https://docs.github.com/en/github-ae@latest/admin/overview/about-github-ae) offering is available in a limited public preview.

- GitHub Actions is still a fairly new service, but is already well suited for build-related tasks (Continuous Integration).

- GitHub Actions is less mature when it comes to deployment tasks (Continuous Deployment).
  - Templating and reuse of pipeline steps is limited.
  - Gates and approval options are limited.
  - Missing options to control pipeline execution, such as the exclusion of specific stages.

### Design recommendations

- Define an availability SLA for deployment tooling and ensure alignment with broader application reliability requirements.

- In a multi-region scenario with an active-passive or active-active application deployment configuration, ensure that failover orchestration and scaling operations can continue to function even if the primary region hosting deployment toolsets becomes unavailable.

## Branching strategy

Branching strategies are a fundamental aspect of application source control, and while there are many valid approaches to apply branching, there are several key aspects that should be considered in the context of a mission-critical application scenario to ensure maximum reliability for mission-critical workloads.

### Design considerations

- Developers will carry out their work in _feature/*_ and _fix/*_ branches and these are the entry points for changes.

- Restrictions can be applied to branches as part of the branching strategy, such as only allowing administrators to create release branches, or enforcing naming conventions for branches.

- There might be rare occasions where a hotfix is urgently required and applied directly into an existing production environment. Examples of potential hotfixes include critical security updates or remediation of issues breaking the user experience. Typically, these hotfixes are created on a _fix/*_ branch and merged into the release branch. It's essential that the change is brought into _main_ as soon as practical so that is part of future releases and also avoids any reoccurrence of the issue. This process must only be used for small changes addressing urgent issues and with restraint.

### Design recommendations

- Prioritize the use of [GitHub for source control](https://docs.github.com/en/code-security/supply-chain-security/understanding-your-software-supply-chain/about-supply-chain-security).

> [!IMPORTANT]
> Create a branching strategy that details _feature_ work and _releases_ as a minimum, using branch policies and permissions to ensure the strategy is appropriately enforced.

- When feature branch changes are pushed to _origin_, trigger an automated testing process to validate the legitimacy of code contributions before any Pull Request (PR) can be completed.
  - Ensure any PR requires the review of at least one other team member before merging.

- It's recommended to treat the _main_ branch as a continuously forward moving and stable branch, primarily used for integration testing.
  - Ensure changes are only made to _main_ via PRs, using a branch policy to prohibit direct commits.
  - Every time a PR is merged into _main_, it should automatically kick off a deployment against an integration environment.
  - _main_ should be considered stable and safe to create a release from at any given time.

- It's recommended to consider the use of dedicated _release/*_ branches, created from the _main_ branch and used to deploy to Production environments.
  - _release/*_ branches should remain in the repository and can be used to patch a release.

- Define and document a hotfix process and apply it only when needed.
  - Create hotfixes in a _fix/*_ branch for subsequent merging into the release branch and deployment to production.
  - Ensure any changes are brought into _main_ as soon as practical so that they reflected in all future releases to avoid reoccurrence of the issue.
  - A hotfix process should only be used with restraint for small changes addressing urgent issues; almost all operational issues should follow the standard operating procedure and CI/CD DevOps processes.

## Container registry

Container registries are a key aspect for any containerized application, providing hosting for container images deployed to container runtime environments, such as AKS. There's a wide variety of container registry technologies available that predominantly rely on the Docker-provided format and standards for both push and pull operations.

This section will therefore examine the optimal configuration of container registries, focusing on the native Azure Container Registry service, while also exploring the trade-offs associated with centralized and federated deployment models.

### Design considerations

- Because most container registry solutions rely on the Docker-provided format and standards for both push and pull operations, they're broadly compatible and mostly interchangeable.

- Container registries can sometimes be deployed either as a centralized service that is shared and consumed by numerous applications within an organization, or a separate application component dedicated to a specific application workload.

- Some application scenarios will require public container images be replicated within a private container registry to limit egress traffic, increase availability, or avoid potential throttling.

**Public Registries - Docker Hub**

- Container images stored on Docker Hub, or other public registries, exist outside of Azure and a given virtual network. This isn't necessarily a problem, but in certain scenarios can lead to various potential issues where service unavailability, throttling and data exfiltration are concerned.

**Azure Container Registry (ACR)**

- [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/) provides an Azure-native service with a range of features including geo-replication, Azure AD authentication, automated container building, and patching using ACR tasks.

- ACR supports high availability through [Geo-replication](/azure/container-registry/container-registry-geo-replication#considerations-for-high-availability) to multiple configured regions, providing resiliency against regional outage. If a region becomes unavailable, the other regions will continue to serve image requests, and when the region returns to health the ACR will recover and replicate changes to it.
  - This capability also provides registry colocation within each configured region, reducing network latency and cross-region data transfer costs.

- Within Azure regions that provide Availability Zone support, the [Premium ACR tier supports Zone Redundancy](/azure/container-registry/zone-redundancy) to protect against zonal failure.

- [Tagged ACR images are mutable by default](/azure/container-registry/container-registry-image-lock#scenarios), meaning that the same tag can be used on multiple images pushed to the registry.
  - In production scenarios, this may lead to unpredictable behavior that could impact application uptime.

- ACR supports [locking an image version or a repository](/azure/container-registry/container-registry-image-lock) to prevent changes or deletes.
  - Image Locking mitigates multiple failure scenarios and also protects against a previously deployed image *version* being changed in-place, which would introduce the risk that same-version deployments may have different results (before and after such a change).
  - Locking Container Images doesn't protect against the ACR instance being deleted, but [Azure Resource Locks](/azure/azure-resource-manager/management/lock-resources) can be used to achieve this.

- ACR in Premium tier also offers support to restrict a container registry to a given set of virtual networks and subnets through [Private Endpoints](/azure/container-registry/container-registry-private-link).

### Design recommendations

- For a mission-critical application, employ container registry instances that are dedicated to the application workload. Avoid taking a dependency on a centralized service unless availability and reliability requirements are in full alignment with the application.

- When using container registries outside Azure, ensure that the provided SLA is aligned with the reliability and security targets. Take special note of throttling limits in cases such as when relying on Docker Hub.

- Use Azure Container Registry to host container images.

**Azure Container Registry (ACR)**

- Treat container registries as 'global resources' with a sustained lifecycle ('long-living'). Consider a single global container registry per environment, such as the use of a global production registry.

- Configure geo-replication to all considered deployment regions in order to remove regional dependencies and optimize latency.
  - Images should be hosted geographically as close as possible to the consuming compute resources, within the same Azure regions.
  - Prioritize regions with Availability Zone support to take advantage of zonal redundancy capabilities.

- Use Azure AD integrated authentication to push and pull images instead of relying on access keys.
  - For optimal security, fully disable the use of the admin access key.

## Secret management

Secret management is a key technical domain in the context of both security and reliability, since the secret management solution for a mission-critical application must provide requisite security and also offer an appropriate level of availability to align with maximum reliability aspirations.

### Design considerations

- There's a multitude of key and secret management solutions available that can be used on Azure.

- Azure Key Vault provides a fully managed Azure-native PaaS solution.
  - Provides native integration with Azure services out-of-the-box.
  - Supports Availability Zone deployments and multi-region redundancy.
  - Offers direct integration with Azure AD for authentication and authorization.

- Many Azure services already support Azure AD authentication instead of relying on connection strings / keys. Doing so greatly reduces the need to managed secrets in the first place.

There are three common approaches applied to define at what point secrets must be read from the selected secret store and injected into the application:

**Deployment-Time Retrieval**

- Retrieving secrets at deployment time provides the advantage that the secret management solution only need to be available at deployment time, since there are no direct dependencies after this point. For example, injecting secrets as environment variables into a Kubernetes deployment or into a Kubernetes secret.

- Only the deployment service principal needs to be able to access secrets, which simplifies RBAC permissions within the secret management system. It does, however, introduce additional RBAC considerations within DevOps tooling around controlling service principal access and the application in terms of protecting retrieved secrets.

- This method introduces a trade-off since the security benefits of the secret management solution aren't being utilized since this design approach relies solely on the access control within the application platform to keep secrets safe.

- Secret updates or rotation will require a full redeployment in order to take effect.

**Application start-up retrieval**

- Retrieving and inject secrets at application startup provides the benefit that secrets can more easily be updated or rotated. A restart of the application is required to fetch the latest value.

- This method ensures that secrets do not need to be stored on the application platform but can be held in memory only.
  - For AKS, available implementations for this approach include the [CSI SecretStore driver for KeyVault](https://azure.github.io/secrets-store-csi-driver-provider-azure/) and [akv2k8s](https://akv2k8s.io/).
  - A native Azure solution [Azure Key Vault referenced App Settings](/azure/app-service/app-service-key-vault-references) is also available.

- The disadvantage of this approach is that it creates a runtime dependency to the secret management solution. If the secret management solution experiences an outage, application components already running **may** be able to continue serving requests, however, any restart or scale-out operations will likely result in failure.

**Runtime retrieval**

- Retrieving secrets at runtime from within the application itself serves as the most secure approach since even the application platform never has access to secrets.

- Application components require a direct dependency and a connection to the secret management system. This makes it harder to test components individually and usually requires the use of an SDK.

- The application itself needs to be able to authenticate to the secret management system. For AKS the latter can be achieved using [Pod-managed Identities](/azure/aks/use-azure-ad-pod-identity), but that is currently (as of August 2021) still in preview.

### Design recommendations

- Where possible, use Azure AD authentication to connect to other services instead of using connection strings or keys. Use this in conjunction with Azure Managed Identities to remove the need for any secrets to be stored on the application platform.

- Use Azure Key Vault to store all application secrets.

- Azure Key Vault instances should be deployed as part of a regional stamp to mitigate the potential impact of a failure to a single deployment stamp. Global resources, such as Front Door (for certificate storage, if needed), should use a separate Azure Key Vault instance dedicated to global resources, rather than using one of the regional Key Vault instances.

- Use Managed identities instead of service principals to access Key Vault whenever possible.

- Secrets should be retrieved at application startup, not during deployment time or at runtime.

- Implement coding patterns so when an authorization failure occurs at runtime, secrets are re-retrieved.

- Apply a fully automated key-rotation process that runs periodically within the solution.
  - Use [key near expire notification](/azure/key-vault/keys/how-to-configure-key-rotation#configure-key-near-expiry-notification) to get alerted on upcoming expiration.

## Continuous validation and testing

As previously stated, testing is a fundamental activity for any mission-critical solution, to fully validate the health of both the application code and infrastructure. More specifically, to satisfy desired standards for reliability, performance, availability, security, quality, and scale, testing must be well defined and applied as a core component of the application design and DevOps methodologies.

Testing is a key concern for both the local developer experience ("[Inner Loop](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)") and the complete DevOps lifecycle ("[Outer Loop](/dotnet/architecture/containerized-lifecycle/docker-devops-workflow/docker-application-outer-loop-devops-workflow)"), which captures when developed code begins release pipeline processes on its journey to a production environment.

The scope of this section focuses on testing conducted within the outer loop for a product release, considering various test scenarios, such as unit, build, static, security, integration, regression, UX, performance, capacity and failure injection (chaos). The order of conducted tests is also a critical consideration due to various dependencies, such as the need to have a running application environment.

### Design considerations

- With high degrees of deployment automation, automated testing is essential to validate application or infrastructure changes in a timely and repeatable manor.

- The purpose of testing is ultimately to detect errors and issues before they reach production environments, and there are various methods that are required to holistically achieve this goal.

**Unit testing**

- Unit testing is intended to confirm that application business logic works as expected. Improve confidence in the overall effect of code changes.

- Unit testing is typically considered as part of the Inner Loop and as such isn't a primary focus for this section.

**Smoke testing**

- Smoke testing is used to identify whether infrastructure and application components are available and act as expected. A smoke test focuses on functionality rather than performance under load. Typically only a single virtual user session is tested.

- Common smoke testing scenarios include; interrogating the HTTPS endpoint of a web application, querying a database, and simulating a user flow in the application.

- The outcome of a smoke test should be that the system responds with expected values and behavior.

**UI testing**

- UI Testing validates that application user interfaces are deployed and functioning as expected.
  - UI testing is similar to smoke testing but it's focused on user interface interactions.

- UI automation tools can and should be used to drive automation.
  - During a UI test, a script will mimic a realistic user scenario and follow a series of steps to execute actions and achieve an intended outcome.

**Load testing**

- Load testing is designed to validate scalability and application operation under load through rapid and/or gradual increase in application test load, until a threshold/limit's reached. Load tests are typically designed around a particular user flow or scenario, in order to verify that application requirements are satisfied under a defined load.

- Azure services have different soft and hard limits associated with scalability, and load testing can reveal if a system faces a risk of exceeding them during the expected production load.

- Load testing can be used to fine-tune auto-scaling capabilities for services that provide automated scalability (that is to set appropriate measured thresholds). For services that do not provide native auto-scaling, established automated operational procedures can also be fine-tuned through load testing.

**Stress testing**

- Stress testing is a type of negative testing, which applies activities aimed at overloading existing resources in order to understand where solution limits exist, and to ensure the systems ability to recover gracefully.

- During a stress tests it's essential to monitor all components of the system in order to identify potential bottlenecks.

- Every component of the system unable to appropriately scale can turn into a limitation, such as active/passive network components or databases. It's important to understand their limits so that effort can be applied to mitigate potential impact.

- Unlike load testing, stress tests do not adhere to a realistic usage pattern, but aim to identify performance and scale limits.

- An alternative approach is to limit (or scale down) the computing resources of the system and monitor how it behaves under load and whether it's able to recover.

**Performance testing**

- Performance testing combines aspects of *load* and *stress testing* to validate performance under load, and establish benchmark behaviors for application operation.

**Failure injection (chaos) testing**

- Chaos testing introduces artificial failures to the system to validate how the system reacts and the effectiveness of resiliency measures, operational procedures and mitigations.

- A mission-critical application should be resilient to infrastructure and application failures, so introducing faults in the application and underlying infrastructure and observing how the application behaves is essential to achieve confidence in the solutions redundancy mechanisms and validate that it can indeed operate as an 'always on' application.

  Shutting down infrastructure components, purposely degrading performance, or introducing application faults are examples of test scenarios, which can be used to verify that the application is going to react as expected in situations when they occur for real.

- [Azure Chaos Studio](https://azure.microsoft.com/services/chaos-studio/) provides an Azure-native chaos experimentation suite of tools to easily conduct chaos experiments and inject faults within Azure services and application components.
  - Provides built-in chaos experiments for common fault scenarios, providing a growing set of 'behind the curtain' experiments for underlying and abstracted components of Azure services.
  - Supports custom experiments targeting infrastructure and application components.

**Security (penetration) testing**

- Penetration testing is used to ensures that an application and its environment satisfy an expected security posture.

- Penetration tests will probe the application an environment for security vulnerabilities.

- Security testing can encompass the end-to-end software supply chain and package dependencies, with scanning and monitoring for known Common Vulnerabilities and Exposures (CVE).

### Design recommendations

- All testing of both infrastructure and application components should be fully automated to ensure consistency.

- All test artifacts should be treated as code and maintained within the source control system and version controlled along with other application code artifacts.

- The results of the tests should be captured and analyzed as both individual test results and aggregated for assessing trends over time. Test results should be continually evaluated for accuracy and coverage.

- The availability of test infrastructure should be aligned with the SLA for deployment and testing cycles.

- Use PaaS CI/CD orchestration platforms, such as Azure DevOps or GitHub Actions, to orchestrate and execute tests where possible.

- Execute smoke tests as part of every deployment.

- Run extensive load tests, along with stress and chaos testing, as part of every deployment to validate application performance and operability is maintained.
  - Use load profiles that are reflective of real peak usage patterns.
  - Run chaos experiments and failure injection tests at the same time as load tests.

- If database interactions are required for load or smoke tests (that is to create new records), use test accounts with reduced privileges and make test data separable from real user content.

- Tests with shorter execution times should generally run earlier in the cycle where possible to increase testing efficiency.

- Scan and monitor the end to end software supply chain and package dependencies for known CVEs.
  - Use [Dependabot](https://docs.github.com/en/code-security/supply-chain-security/keeping-your-dependencies-updated-automatically/about-dependabot-version-updates) for GitHub repositories to ensure the repository automatically keeps up-to-date with the latest releases of packages and applications it depends on.

## AI for DevOps

AIOps methodologies can be applied within CI/CD pipelines to supplement traditional testing approaches, providing capabilities to detect likely regressions or degradations, and allowing deployments to be preemptively stopped to prevent potential negative impact.

### Design considerations

- CI/CD pipelines and DevOps processes will expose a wide variety of telemetry for machine learning models, from test results and deployment outcomes, to operational data of test components from composite deployment stages.
  - CI/CD pipelines will include various types of automated testing, such as unit, smoke, performance, load, and chaos tests.

- Changes in a deployment will need to be stored in a manner suitable for automated analysis and correlation to deployment outcomes.

- Traditional data processing approaches such as Extract, Transform, and Load (ETL) may not be able to scale throughput to keep up with growth of deployment telemetry and application observability data.
  - Modern analytics approaches that do not require ETL and data movement, such as data virtualization, can be used to enable ongoing analysis by AIOps models.

### Design recommendations

- Define what DevOps process data will be collected and how it will be analyzed.
  - Expose application observability data from staged test environments and the production environment for analysis and correlation within AIOps models.
  - Gather deployment telemetry from DevOps processes, such as test execution metrics and time series data of changes within each deployment.

- Adopt the [MLOps Workflow](https://azure.microsoft.com/services/machine-learning/mlops/).

- Develop analytical models that are context-aware and dependency-aware to provide predictions along with automated feature engineering to address schema and behavior changes.

- Operationalize models by registering and deploying the best trained models within deployment pipelines.

## Next step

Review the security considerations.

> [!div class="nextstepaction"]
> [Security](./mission-critical-Security.md)
