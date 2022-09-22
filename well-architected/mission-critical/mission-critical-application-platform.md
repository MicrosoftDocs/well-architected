---
title: Application platform considerations for mission-critical workloads on Azure
description: This design area explores requisite decision factors and provides recommendations related to the selection, design, and configuration of an appropriate application hosting platform for a mission critical application on Azure
author: calcof
ms.author: calcof
ms.date: 02/28/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
categories:
  - containers
  - web
products: Azure
ms.custom:
  - mission-critical
---

# Application platform considerations for mission-critical workloads on Azure

Azure provides many [compute services](/azure/architecture/guide/technology-choices/compute-decision-tree) for hosting highly available applications. The services differ in capability and complexity. We recommend that you fully understand the capability matrix with focus on:

- Non-functional requirements surrounding reliability, availability, performance, and security.
- Decision factors such as scalability, cost, operability, and complexity.

The selection of an appropriate application hosting platform is a critical decision that has impact on all other design areas. For example, the scale-limits of a particular service will have a key bearing on suitability and the overall application design in terms of scale-unit definitions. A mission-critical application can use more than one compute service in parallel to support multiple composite workloads and microservices with distinct platform requirements.

This design area explores the important decision factors and provides recommendations related to the selection, design, and configuration of an appropriate application hosting platform.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload)
>
> ![GitHub logo](./../_images/github.svg) [Mission-Critical open source project](http://github.com/azure/mission-critical)
>
> The [reference implementations](mission-critical-overview.md#illustrative-examples) are part of an open source project available on GitHub. The code assets illustrate the considerations and implementations for a selected compute service.

## Programming language selection

Selecting the right programming languages and frameworks is a critical design decision. Typically this decision is driven by the availability of development skills or by the use of standardized technologies within an organization. However, given the reliable aspirations, it's essential to also evaluate the performance and resilience aspects of different languages/frameworks as well as the capability differences within required Azure SDKs.

### Design considerations

- There are sometimes significant differences in the capabilities offered by Azure service SDKs in different languages, and this may influence the selection of an Azure service or programming language.
  - For instance, if Cosmos DB is a significant dependency, 'Go' may not be an appropriate development language since there's no first-party SDK.

- New features are typically added to the .NET and Java libraries first, and there can be a delay in feature availability for other [supported languages](https://azure.github.io/azure-sdk/).

- The application can use multiple programming languages or frameworks in parallel to support multiple composite workloads with distinct requirements.
  - However, significant technology sprawl should be avoided since it introduces management complexity and operational challenges.

### Design recommendations

- Evaluate all relevant Azure SDKs to confirm requisite capabilities are available for selected programming languages, ensuring alignment with non-functional requirements.

- Optimize the selection of programming languages and frameworks at the microservice level; embrace multiple technologies where appropriate.
  - Avoid extensive technology sprawl to prevent unnecessary operational complexity.

- Prioritize the .NET SDK to optimize reliability and performance since .NET Azure SDKs typically provide additional capabilities and receive new features first.

## Containerization

Containerization allows developers to create and deploy applications faster and more reliably by bundling application code together with related configuration files, libraries, and dependencies required for it to run. This single software package *container* runs on a shared kernel abstracted from the host operating system, and as a result is highly portable, capable of running consistently across different infrastructure platforms or cloud providers.

### Design considerations

- Containerization has become a major trend in software development since it provides measurable benefits for developers and operations teams as well as optimizing infrastructure utilization. More specifically, the benefits of containerizing application components include:
  - **Improved infrastructure utilization**: Containers don't include operating system images so require less system resources. Multiple containers can therefore be hosted on the same virtualized infrastructure, and this helps to optimize resource utilization by consolidating on fewer resources with higher container density.
  - **Portability**: Including all software dependencies within the container ensures that it will work across different operating systems regardless of runtimes or library versions. Containerized applications are therefore easier to move between application platforms due to the standardized container format.
  - **Faster scaling operations**: Containers are lightweight and don't suffer from the slow start-up and shutdown times afflicting virtual machines, and since container images are pre-built, the start-up activity can be minimized to focus only on bootstrapping the application.
  - **Simplified management**: The consistent portability and ephemeral nature of containers provides a simplified infrastructure management experience compared to traditional virtualized hosting.
  - **Agile development**: Containers support accelerated development, test, and production cycles through consistent operation and less overhead.
- The drawbacks of containerizing application components include:
  - **Complex monitoring**: Monitoring services can find it harder to access applications running inside a container. Third-party software is typically required to collect and store container state indicators, such as CPU usage or RAM usage.
  - **Security**: The hosting platform OS kernel is shared across multiple containers, creating a single point of attack. However, the risk of host VM access is limited since containers are isolated from the underlying operating system.

- Containerization has proven to be an excellent option for packaging applications across different development languages, providing an abstraction layer for application code and its dependencies to achieve separation from the underlying hosting platform.

- Containerization enables workloads to run on Azure without application code needing to be re-written.
  - Provides a good opportunity to modernize legacy applications without significant code change, and can therefore be suitable for 'lift and shift' scenarios depending on the considered application frameworks and external dependencies.

- While it's possible to store data within a running container's filesystem, the data will not persist when the container is recreated, so instead persistence is typically achieved by 'mounting' external storage.

### Design recommendations

- Containerize all application components, using container images as the primary model for application deployment packages.
- Prioritize Linux-based container runtimes when possible.
- Avoid persisting state/data within a container since containers should be immutable and replaceable with short lifecycles.
- Ensure that all relevant logs and metrics are gathered form the container, container host, and underlying cluster. Gathered logs and metrics should be sent to a unified data sink for further processing.

## Container Orchestration and Kubernetes

There are several Azure application platforms capable of effectively hosting containers:

- [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Azure Container Instances (ACI)](https://azure.microsoft.com/services/container-instances/)
- [Azure App Service](https://azure.microsoft.com/services/app-service/containers/)
- [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/)
- [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)

There are advantages and disadvantages associated with each of these Azure container platforms which should be analyzed in the context of business requirements to inform an optimal technical choice; each platform serves an optimal choice for certain scenarios. However, given the principles underpinning the  design methodology strive to optimize reliability, scalability, and performance, it's strongly recommended to prioritize the use of Azure Kubernetes Service.

Azure Kubernetes Service (AKS) is Microsoft Azure's native managed Kubernetes service which allows for rapid Kubernetes cluster provisioning without complex cluster administration activities, and enhances standard Kubernetes with a rich feature set that includes advanced networking and identity capabilities.

### Design considerations

For web and API based workload scenarios Azure App Services offers a feasible alternative to AKS, providing a low-friction container platform without the complexity of Kubernetes.

The considerations and recommendations within this section will therefore focus on optimal AKS usage as well as App Services for low-scale scenarios.

**Azure Kubernetes Service**

- There are many different container orchestrators, but Kubernetes has become the clear market leader and is best supported across the open source community and major cloud providers.
  - Kubernetes expertise is readily available within the employment market.
  - Kubernetes has a steep learning curve, so if development teams are new, it will require non-trivial engineering investment to set up and maintain a Kubernetes environment in a secure and reliable way.
  - Kubernetes as well as managed Kubernetes offerings like AKS are widely available and can address concerns reg. vendor lock-in.

- Default 'vanilla' Kubernetes requires significant configuration to ensure a suitable security posture for business-critical application scenarios. AKS addresses various security risks out of the box, such as support for private clusters, auditing and logging into Log Analytics, and hardened node images.

- AKS provides a [control plane](https://kubernetes.io/docs/concepts/overview/components/) that is managed by Microsoft. By default the control plane of AKS is provided free of charge, but without any guaranteed SLA. Customers only manage and pay for the worker nodes which form the cluster.

- The optional [AKS Uptime SLA](/azure/aks/uptime-sla) provides availability guarantees for the Kubernetes control plane. 99.95% availability of the Kubernetes API server endpoint for AKS Clusters that use Azure Availability Zones and 99.9% availability for AKS Clusters that don't use Azure Availability Zones.

- Some foundational configuration decisions have to be made upfront and can't be changed without re-deploying an AKS cluster.
  - Selection between public and private AKS clusters.
  - Enabling Azure Network Policy.
  - Azure AD integration and the use of Managed Identities for AKS instead of Service Principals.

- AKS supports [three minor versions of Kubernetes](/azure/aks/supported-kubernetes-versions) in alignment with the [Kubernetes community](/azure/aks/supported-kubernetes-versions?tabs=azure-cli#aks-kubernetes-release-calendar). When a new minor version is introduced, the oldest supported minor version and patch releases are retired.

- AKS supports [updating node images](/azure/aks/node-image-upgrade) to the newest OS and runtime versions without updating the Kubernetes version of the cluster or node pool. The AKS team releases new images on a weekly basis for Windows and Linux nodes.

- AKS supports different [auto-upgrade channels](/azure/aks/upgrade-cluster#set-auto-upgrade-channel) to automatically upgrade AKS clusters to newer versions of Kubernetes and/or newer node images once available. [Planned Maintenance](/azure/aks/planned-maintenance) can be used to define maintenance windows for these operations.

- AKS supports different network plugins. The [Azure CNI plugin](/azure/aks/concepts-network#compare-network-models) is required to enable certain capabilities within AKS, such as Windows-based node pools or Kubernetes Network Policies. It also supports [bring-your-own-cni](/azure/aks/use-byo-cni) for specific use cases.

- AKS differentiates between [system node pools](/azure/aks/use-system-pools#system-and-user-node-pools) and user node pools to separate system and workload services. User node pools can be [scaled down to 0](/azure/aks/scale-cluster#scale-user-node-pools-to-0) nodes if needed.

- The [AKS Stop/Start cluster feature](/azure/aks/start-stop-cluster) allows an AKS cluster in dev/test scenarios to be paused while  maintaining cluster configuration, saving time and cost compared to re-provisioning.

- Azure Monitor for containers (Container Insights) provides a seamless onboarding experience, enables various monitoring capabilities out of the box as well as more advanced scenarios via its built-in [Prometheus scraping](/azure/azure-monitor/insights/container-insights-prometheus-integration) support.

- AKS offers integration with Azure AD to enable the use of Managed Identities for AKS as well as for node pool and individual pods, Role Based Access Control (RBAC) using Azure AD credentials as well as [authentication with Azure Container Registry (ACR)](/azure/aks/cluster-container-registry-integration).

- [Azure Policy](/azure/aks/use-pod-security-on-azure-policy) can help to apply at-scale enforcements and safeguards to AKS clusters in a consistent centralized manner.
  - Azure Policy can control what functions pods are granted, and if running contradicts policy. This access is defined through built-in policies provided by the [Azure Policy Add-on for AKS](/azure/governance/policy/concepts/policy-for-kubernetes).

- AKS has certain [scale limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-kubernetes-service-limits), such as the number of nodes and number of node pools per cluster, as well as the number of clusters per subscription.

**Azure App Service**

- SNAT port exhaustion is a common failure scenario with Azure App Services, which can be predicted through load testing while monitoring ports using Azure Diagnostics.
  - SNAT ports are used when making outbound connections to public IP addresses.

- TCP port exhaustion is a further common failure scenario which occurs when the sum of outbound connections from a given worker exceeds the capacity. The number of available TCP ports depend on the size of the worker, as captured below in the following table:

    | Protocol  |Small (B1, S1, P1, I1)|Medium (B2, S2, P2, I2)|Large (B3, S3, P3, I3)|
    |---------|---------|---------|---------|
    |TCP ports|1920|3968|8064|

- Azure App Service has a default limit of 30 instances per App Service Plan.
  - This default limit can be increased by opening a support ticket, if the App Service routinely uses 15 or more instances.

- Per-app scaling can be enabled at the App Service Plan level to allow an application to scale independently from the App Service plan that hosts it. For example, an App Service Plan can be scaled to 10 instances, but an app can be set to use only 5.
  - Apps are allocated to available nodes using a best effort approach for an even distribution. While an even distribution isn't guaranteed, the platform will make sure that two instances of the same app will not be hosted on the same instance.

- There are a number of events that can lead App Service workers to restart, such as content deployment, App Settings changes, and Virtual Network integration configuration changes.

- App Service plan autoscale will scale-out run if any rule within the profile is met, but will only scale-in if all rules within the profile are met.

- The App Service Premium (v3) Plan has a 20% discount versus comparable Pv2 configurations.
  - Reserved Instance commitment (1Y, 3Y, Dev/Test) discounts are available for App Services running in the Premium v3 plan.

- Diagnostic logging provides the ability to ingest application and platform level logs into either Log Analytics, Azure Storage, or a third party tool via Event Hubs.

- Application performance monitoring with Application Insights provides deep insights into application performance.
  - For Linux Plans a code-based enablement (SDK) is required.
  - For Windows Plans a 'codeless deployment' approach is possible to quickly get insights without changing any code.

### Design recommendations

**Azure Kubernetes Service**

- Use Azure Kubernetes Service (AKS) as the primary application hosting platform where requirements allow.

- Deploy [AKS clusters across different Azure regions](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) as a scale-unit to maximize reliability and availability.

- Configure the use of AKS node pools to maximize reliability.
  - Use [Availability Zones](/azure/aks/availability-zones) to maximize resilience within an Azure region by distributing AKS control plane and agent nodes across physically separate datacenters.
    - Where co-locality latency requirements exist, either a VMSS-based AKS deployment within a single zone or [proximity placement groups](/azure/aks/reduce-latency-ppg) should be used to minimize inter-node latency.
  - Ensure the System node pool is isolated from application workloads.
  - Use dedicated node pools for infrastructure components and tools that require high resource utilization, such as Istio, to avoid noisy neighbor scenarios.
    - Alternatively, ensure special scale or load behavior is defined.
  - Separate distinct application workloads to dedicated node pools based on workload requirements, considering requirements for specialized infrastructure resources such as GPU, high memory VMs.
  - Avoid deploying large numbers of node pools to reduce additional management overhead.
  - Use [taints and tolerations](/azure/aks/operator-best-practices-advanced-scheduler#provide-dedicated-nodes-using-taints-and-tolerations) to provide dedicated nodes and limit resource intensive applications.
  - For high scale scenarios, consider the use of [Virtual Nodes](/azure/aks/virtual-nodes-cli) ([vKubelet](https://github.com/virtual-kubelet/virtual-kubelet)) with ACI for extensive and rapid scale.
  - Using [Azure Spot VMs](/azure/aks/spot-node-pool) isn't recommended for highly available workloads. However, you can consider this option for development and testing environments as a way to optimize cost.
  - Evaluate application affinity and anti-affinity requirements and configure the appropriate colocation of containers on nodes.

- Resources within the [node resource group](/azure/aks/faq#why-are-two-resource-groups-created-with-aks) ('MC_') should not be modified directly, only via the AKS API. The name of the node resource group can be chosen at [cluster creation time](/azure/aks/faq#can-i-provide-my-own-name-for-the-aks-node-resource-group) only or with assistance from Azure Support.

- Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically adjust the number of agent nodes in response to resource constraints.

- Utilize the [Horizontal pod autoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler) to adjust the number of pods in a deployment depending on CPU utilization or other selected metrics.

- Define [pod resource requests and limits](/azure/aks/developer-best-practices-resource-management#define-pod-resource-requests-and-limits) in application deployment manifests.

- Utilize the [AKS Uptime SLA](/azure/aks/uptime-sla) for production clusters to maximize Kubernetes API endpoint availability guarantees.

- Ensure proper selection of network plugin based on network requirements and cluster sizing. Prioritize the use of Azure CNI.
- Use [Azure](/azure/aks/use-network-policies) or Calico Network Policies to control traffic within the cluster. (requires Azure CNI)

- Harden the AKS cluster to remove critical security risks associated with Kubernetes deployments.
  - Use [Pod Identities](/azure/aks/operator-best-practices-identity#use-pod-identities) and [Secrets Store CSI Driver](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage) with [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) to protect secrets, certificates, and connection strings.
  - Use [Managed Identities](/azure/aks/use-managed-identity) to avoid having to manage and rotate service principal credentials.
  - Utilize [Azure Active Directory integration](/azure/aks/managed-aad) to take advantage of centralized account management and passwords, application access management, and identity protection.
  - Use Kubernetes RBAC with Azure Active Directory for [least privilege](/azure/aks/azure-ad-rbac), and minimize granting administrator privileges to protect configuration and secrets access.
  - Limit access to the [Kubernetes cluster configuration](/azure/aks/control-kubeconfig-access) file with Azure role-based access control.
  - Limit access to [actions that containers can perform](/azure/aks/developer-best-practices-pod-security#secure-pod-access-to-resources), provide the least number of permissions and avoid the use of root / privileged escalation.

- Apply configuration guidance provided within the [AKS security baseline](/security/benchmark/azure/baselines/aks-security-baseline).

- Utilize [Azure Monitor and Application Insights](/azure/azure-monitor/insights/container-insights-overview) to centrally collect metrics, logs, and diagnostics from AKS resources for troubleshooting purposes.
  - Enable and review [Kubernetes master node logs](/azure/aks/view-master-logs).
  - Configure the [scraping of Prometheus metrics](/azure/azure-monitor/insights/container-insights-prometheus-integration) with Azure Monitor for containers.

- Subscribe to the public [AKS Roadmap and Release Notes](https://github.com/azure/aks) on GitHub to stay up-to-date on upcoming changes, improvements, and most importantly Kubernetes version releases or the deprecation of old releases.

- Regularly upgrade to a supported version of Kubernetes.
  - Establish a governance process to check and upgrade as needed to not fall out of support.
  - Leverage the AKS Cluster auto-upgrade with Planned Maintenance.
  - Regularly process node image updates to remain current with new AKS images.

- Ensure AKS subscription [scale limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-kubernetes-service-limits) are appropriately considered when designing the AKS deployment model to ensure requisite scalability.

- Consider and apply the guidance provided within the [AKS checklist](https://www.the-aks-checklist.com/) to ensure alignment with Well-Architected best practice guidance.

- Store container images within [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).
  - Enable [geo-replication](/azure/aks/operator-best-practices-multi-region#enable-geo-replication-for-container-images) to replicate container images across all leveraged AKS regions.
  - Enable [Azure Defender for container registries](/azure/security-center/defender-for-container-registries-introduction) to provide vulnerability scanning for container images.
  - Authenticate using Azure AD to access Azure Container Registry.

  - Establish a consistent reliability and security baseline for AKS cluster and [pod](/azure/aks/use-pod-security-on-azure-policy) configurations using [Azure Policy](/azure/governance/policy/overview).
    - Use the [Azure Policy Add-on for AKS](/azure/governance/policy/concepts/policy-for-kubernetes) to control pod functions, such as root privileges, and disallow pods which don't conform to policy.
    - Policy assignments should be enforced at a subscription scope or higher to drive consistency across development teams.

  > [!NOTE]
  > 
  > When deploying into an Azure landing zone, be aware that the required Azure Policy to ensure consistent reliability and security should be provided by the landing zone implementation.
  > The mission-critical [reference implementations](mission-critical-overview.md#illustrative-examples) provide a suite of baseline policies to drive recommended reliability and security configurations.

**Azure App Service**

- For lower scale workload scenarios Azure App Services can provide a feasible alternative to AKS, without the complexities associated with administering Kubernetes.
  - Consider and plan for future scalability requirements and application growth so that a strategic technology decision can be applied from the start, avoiding future technical migration debt as the solution grows.
  - If the lack of requisite Kubernetes expertise presents an unacceptable delivery risk, consider Azure App Service as an alternative container platform.

- Leverage the container based deployment model for App Service Plans.

- Use Premium V3 plans with 2 or more worker instances for high availability.

- Use Linux App Service Plans to optimize performance and costs.

- Deploy App Service Plans in an [Availability Zone configuration](https://azure.github.io/AppService/2021/08/25/App-service-support-for-availability-zones.html) to ensure worker nodes are distributed across zones within a region.

- Deploy Azure App Service Plans across multiple regions as a scale unit, using multiple scale-units deployed within a single region to navigate the default limit of 30 instances per App Service Plan.
  - Consider opening a support ticket to increase the maximum number of workers to twice the instance count required to serve normal peak load.

- Evaluate the use of TCP and SNAT ports to avoid outbound connection errors.
  - Predictively detect SNAT port exhaustion through load testing while monitoring ports using Azure Monitor, and if SNAT errors occur, it's necessary to either scale across more/larger workers, or implement coding practices to help preserve and re-use SNAT ports, such as connection pooling or the lazy loading of resources.

  - It's recommended not to exceed 100 simultaneous outbound connections to a public IP Address per worker, and to avoid communicating with downstream services via public IP addresses when a [Private Endpoint](/azure/private-link/private-endpoint-overview) or [Service Endpoint](/azure/virtual-network/virtual-network-service-endpoints-overview) could be used.

- Avoid unnecessary worker restarts.
  - Make changes within a deployment slot other than the slot currently configured to accept production traffic. After workers are recycled and warmed up, a 'swap' can be performed without unnecessary down time.

- Enable [AutoHeal](https://azure.github.io/AppService/2021/04/21/Announcing-Autoheal-for-Azure-App-Service-Linux.html) to automatically recycle unhealthy workers.

- Enable [Health Check](https://aka.ms/appservicehealthcheck) to identify non-responsive workers.
  - While any health check is better than none at all, the logic behind endpoint tests should assess all critical downstream dependencies to ensure overall health.

- Enable [AutoScale](/azure/azure-monitor/platform/autoscale-get-started?toc=/azure/app-service/toc.json) to ensure adequate resources are available to service requests.
  - Use a scale-out and scale-in rule combination to ensure auto-scale can take action to both scale-out and scale-in.
  - Understand the behavior of multiple scaling rules in a single profile.

- Enable [Diagnostic Logging](/Azure/app-service/troubleshoot-diagnostic-logs) to provide insight into application and platform behavior.

- Enable [Application Insights Alerts](/Azure/azure-monitor/app/azure-web-apps) to be made aware of fault conditions.

- Review [Azure App Service diagnostics](/azure/app-service/overview-diagnostics) to ensure common problems are addressed.
  - It's a good practice to regularly review service-related diagnostics and recommendations, taking action as appropriate.

- Evaluate [per-app scaling](/azure/app-service/manage-scale-per-app) for high-density hosting on Azure App Service Plans.

## Serverless compute

Serverless computing provides compute resources on-demand and eliminates the need to manage infrastructure all together; the cloud provider automatically provisions, scales, and manages resources required to run deployed application code.

Microsoft Azure provides several serverless compute platforms:

- [Azure Functions](/azure/azure-functions/functions-overview): Application logic is implemented as distinct blocks of code ("functions") which run in response to events, such as an HTTP request or queue message, with each function scaling as necessary to meet demand.
- [Azure Logic Apps](/azure/logic-apps/logic-apps-overview): Platform for creating and running automated workflows that integrate various apps, data sources, services and systems. Similar to Azure Functions, there are built-in triggers for event-driven processing but instead of deploying application code, Logic Apps can be composed using a graphical user interface which supports code blocks like conditionals and loops.
- [Azure API Management](https://azure.microsoft.com/services/api-management/): Publish, secure, transform, maintain, and monitor APIs using the Consumption tier.
- [Power Apps & Power Automate](/powerapps/powerapps-overview): Provides a 'low-code/no-code' development experience, with simple workflow logic and integrations configurable through connections in a user interface. Developed Power Apps can subsequently be deployed to a Microsoft 365 tenant and consumed from either a web browser or the Power Apps mobile client.

In the context of a reliable application platform, serverless technologies provide a near-zero friction development and operational experience, which can be highly valuable for simple business process scenarios. However, this relative simplicity comes at the cost of flexibility in terms of scalability, reliability, and performance, which is likely unacceptable for most business-critical application scenarios.

The design methodology positions serverless technologies as an alternative platform for simple business process scenarios which don't share the same stringent business requirements as critical system flows. The design considerations and recommendations within this section focus on optimal Azure Function and Azure Logic Apps usage as alternative platforms for non-critical workflow scenarios.

### Design considerations

**Azure Functions**

- In most cases Azure Functions don't require additional code to call external services or to enable external events trigger function execution since these can be achieved with Azure Function [Bindings](/azure/azure-functions/functions-triggers-bindings).
  - Azure Functions supports multiple triggers, such as the HTTP trigger, and bindings for Azure Services, such as Azure Cosmos DB, Azure Service Bus and Azure Blob Storage.

- There are 3 [hosting plans](/azure/azure-functions/functions-scale) available for Azure Functions:
  - *Consumption* is the fully serverless pay-per-use option, with instances dynamically added and removed based on the number of incoming events; underlying compute resources are charged only when running.
  - *Premium* uses a Premium SKU App Service plan to host functions and allows the configuration of compute instance size. Additionally,  possible to set up a number of pre-warmed instances to eliminate cold starts.
    - There will always be at least one billed instance in the Premium plan.
  - *Dedicated* is the least serverless option as it's tied to a provisioned App Service plan or App Service Environment. Autoscale can be enabled, but scale operations are slower than with the Consumption and Premium plans.

- Fully serverless hosting options, which help optimize costs by de-provisioning allocated resources when workloads are not running, may incur "cold start" delays, especially for applications comprised of many files, such as Node.js or PHP applications.

- [Azure Function Premium](/azure/azure-functions/functions-premium-plan#region-max-scale-out) has a limit of 100 instances in the Windows tier and 20 instances in the Linux tier.

**Azure Logic Apps**

- There are 3 [deployment modes](/azure/logic-apps/single-tenant-overview-compare) available for Azure Logic Apps:
  - *Consumption* is the fully serverless pay-per-use model, with Azure managing the infrastructure which is shared across multiple tenants.
    - A single logic app can have only one workflow.
  - *Consumption (ISE)* uses the dedicated Integration Service Environment (ISE) to privately host logic apps.
    - A single logic app can have only one workflow.
  - *Standard* uses the containerized single-tenant Azure Logic Apps runtime based on Azure Functions.
    - Each logic app can have multiple stateful and stateless workflows.

- Similar to Azure Functions, there are built-in triggers for event-driven processing, however, instead of deploying application code Logic Apps can be composed using a graphical user interface which supports blocks like conditionals, loops etc.

- With the standard deployment model, default limits can be modified, however, some limits have upper maximums.
  - For consumption plans Azure manages the default configuration limits, but some values can be changed through the creation of a support ticket.

### Design recommendations

**Azure Functions**

- Consider Azure Functions for simple business process scenarios which don't have the same stringent business requirements as business-critical system flows.
  - Low-critical scenarios can also be hosted as separate containers within AKS to drive consistency, provided affinity and anti-affinity requirements are fully considered when collocating containers on nodes.

- Azure Functions should perform distinct operations that run as fast as possible.
  - This makes them very flexible and highly scalable, ensuring they will work well in event-driven workloads with short-lived processes.

- Use the Premium Function Linux hosting plan to maximize reliability and performance while maintaining the serverless promise.

- Take a scale-unit approach to navigate the resource limit of 20 Linux nodes.

- When using the HTTP trigger to expose an external endpoint, protect the HTTP endpoint from common external attack vectors using a Web Application Firewall (WAF).

- For internal workloads, consider the use of Service Endpoints or Private Endpoints to restrict access to private Virtual Networks.
  - If required, use Private Endpoints to mitigate data exfiltration risks, such as malicious admin scenarios.

- Treat Azure Functions code just like any other code; subject it to code scanning tools that  integrate it with CI/CD pipelines.

**Azure Logic Apps**

- Use the standard deployment mode to ensure a single tenant deployment and mitigate 'noisy neighbor' scenarios.

## Asynchronous messaging

The recommendation for a loosely coupled microservice architecture relies heavily on asynchronous messaging to provide requisite integration between application components through a well-defined message bus. Azure provides several native messaging services which can be used to facilitate asynchronous message interfaces, including **Azure Event Hub**, **Azure Service Bus**, **Azure Storage Queues** and **Azure Event Grid**.

This section will therefore explore key decision factors when selecting an appropriate Azure message service, and how to optimally configure each service in the context of a mission-critical workload scenario to maximize reliability.

### Design considerations

- More than one type of messaging service can be used by an application for different workload scenarios.

- There are many factors to consider when selecting an optimal messaging service for a specific scenario.
  - [Asynchronous Messaging on Azure](/azure/architecture/guide/technology-choices/messaging)
  - [Azure Storage Queues vs Azure Service Bus Queues](/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)
  - [Comparison between Event Grid, Event Hubs and Service Bus](/azure/event-grid/compare-messaging-services)
  - [Azure Messaging Services -How to Choose the Right Messaging Technology in Azure](https://medium.com/walmartglobaltech/azure-messaging-services-how-to-choose-the-right-messaging-technology-in-azure-eab610b5e986)

**Azure Event Hub**

- Azure Event Hubs are designed as a **event streaming** service to scale to multi million messages per second.

- Event Hubs supports Availability Zones (AZs) for zonal redundancy within supported regions in all pricing tiers.

- Azure Event Hubs Namespaces support configurable failover replication between regions, however, only configuration metadata is replicated, but **not messages themselves**. Configuration that is being replicated includes: Event Hubs (inside a Namespace), Consumer Groups, Namespace Authorization Rules, and Event Hubs Authorization Rules.

- Azure Event Hubs can be configured to [capture raw data into Azure Blob Storage or Azure Data Lake Storage](/azure/event-hubs/event-hubs-capture-overview)

- Data can be processed from Event Hubs by a client using the [Event Processor SDK](/azure/event-hubs/event-processor-balance-partition-load), or by [Azure Stream Analytics](/azure/event-hubs/process-data-azure-stream-analytics).

- Event Hubs can be deployed as a dedicated-tier Event Hubs cluster for high throughput and a 99.99% SLA. The Basic and Standard tiers for a single tenant provide a 99.95% SLA.

- Event Hubs provide an Apache Kafka-compatible [messaging interface](/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview).

See [Azure Event Hubs quotas and limits](/azure/event-hubs/event-hubs-quotas) for more details.

**Azure Service Bus**

- Azure Service Bus provides **reliable asynchronous message delivery** that requires polling.

- Service Bus supports Availability Zones (AZs) for zonal redundancy within supported regions in Premium tier.

- Service Bus supports messages sizes of 256 KB in Basic and Standard tier and 1 MB in Premium tier.

- Service Bus offers a SLA of 99.9% on send and receive operations.

**Azure Storage Queues**

- Azure Storage Queues provide a simple messaging solution, which can be communicated with using a REST API.

- Storage queues don't guarantee message ordering.

- Throttling will occur if this maximum throughput limit is reached.

- Through the geo-replication feature of Azure Storage Accounts, Storage Queues can be configured to asynchronously replicate to another region.

- Storage Queues provide the same SLA as their underlying Storage Accounts. A 99.99% SLA on read requests for RA-GRS and 99.9% for write requests.

See [Scalability and performance targets for Queue Storage](/azure/storage/queues/scalability-targets) for more details about service limitations.

**Azure Event Grid**

- Azure Event Grid is designed as a **event distribution** service for reactive programming.

- Event Grid integrates with many Azure services as event sources. For example, Event Grid can be configured to react to status changes on Azure resources.

- Event Grid provides a SLA of 99.99% for message publishing.

See [Azure Event Grid quotas and limits](/azure/event-grid/quotas-limits) for more details.

### Design recommendations

- Prioritize the use of Event Hubs for scenarios which require high throughput and which can work with message ordering on a partition-basis.

- Use Service Bus for scenarios requiring a higher QoS and message guarantee by implementing two phase commits.
  - Use Service Bus Premium in a zonal redundant configuration to provide high-availability within a region.
  
- Consider Storage Queues when message geo-replication is required provided the message size is less than 64 KB.
  - Use an AZ-redundant tier for the underlying Storage Account (ZRS or GZRS).

- Use Event Grid for scenarios where services need to react to changes in another service/component.

## Constrained Migrations using IaaS

Many mission-critical applications already consider an existing on-premises deployment, which often utilize virtualization technologies and redundant hardware to provide high levels of reliability. Often these scenarios will encompass business constraints which hinder the overall applicability of cloud modernization to fully align with cloud-native mission-critical architecture pattern. More specifically, many applications will adopt a phased cloud adoption approach, with initial cloud deployments considering virtualization and Azure Virtual Machines as the primary application hosting model.

This section will therefore focus on the optimal usage of Azure Virtual Machines and associated services in order to maximise the reliability of the application platform, highlighting key aspects of the mission-critical design methodology which transpose cloud-native and IaaS migration scenarios.

### Design Considerations

- The use of IaaS Virtual Machines massively increases the operational costs, compared to PaaS services, by bringing back the responsibility to manage the virtual machine, the operating system, rolling out software packages and updates and a lot more.

- IaaS VMs do, compared to PaaS services, not provide zone-redundancy, geo-distribution and other capabilities out-of-the-box. Same is true for DevOps processes like rolling out software packages, dependencies. The use of PaaS services (or containers) should be prioritized when possible.

- Virtual Machines can be deployed into Availability Zones to achieve higher resiliency and reliability, while still remaining individual VMs. This can be used for example for Domain Controllers and other stateful workloads that cannot scale horizontally.

- The use of IaaS Virtual Machines can be required in certain scenarios:
  - The available PaaS services do not provide the required performance or level of control.
  - No PaaS service pendant is available.
  - A certain workload requires operating system access, specific drivers or network and system configuration or is not supported to run in containers.
  - Lack of support for 3rd-party workloads.

### Design Recommendations

The general recommendations are:

- Reduce the use of IaaS Virtual Machines to the least minimum required.
- Prioritize the use of PaaS services or Containers when possible.
- Replace IaaS dependencies with PaaS when possible to reduce operational complexity and cost.

When IaaS VMs are required:

- Identify and right-size the VM sku sizes used.
- Avoid any manual operations and implement proper processes to deploy and rollout changes.
- Make sure that operational processes for deployment, updates, backup and recovery are in place and properly tested.
- Prioritize the use of managed images and use automated processes and tools like cloud-init to customize.
- Consider using Virtual Machine Scale Sets (VMSS) for scalability and zone-redundancy.
- Prioritize stateless workloads that allow horizontal scale instead of vertical scale.
- Do not address individual virtual machines directly, use load balancers in front when possible.

## Next step

Review the considerations for the data platform.
> [!div class="nextstepaction"]
> [Data platform](./mission-critical-data-platform.md)
