---
title: Application platform considerations for mission-critical workloads on Azure
description: This design area explores requisite decision factors and provides recommendations related to the selection, design, and configuration of an appropriate application hosting platform for a mission critical application on Azure
author: calcof
ms.author: prwilk
ms.date: 12/15/2022
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

Azure provides many compute services for hosting highly available applications. The services differ in capability and complexity. We recommend that you make choices based on:

- Non-functional requirements for reliability, availability, performance, and security.
- Decision factors such as scalability, cost, operability, and complexity.

The choice of application hosting platform is a critical decision that impacts all other design areas. For example, legacy or proprietary development software might not run in PaaS or containerized. This requirement will impact the choice of compute platform. So, a mission-critical application can use more than one compute service to support multiple composite workloads and microservices, each with distinct requirements.

This design area provides recommendations related to the compute selection, design, and configuration options. We recommend that you familiarize yourself with the [Compute decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree).

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload)

## Global distribution of platform resources

A typical pattern for a mission critical workload will include global resources and regional resources.

Azure services, which aren't constrained to a particular Azure region, are deployed or configured as global resources. Some use cases include distributing traffic across multiple regions, storing permanent state for the whole application, and caching global static data. When accommodating both [scale-unit architecture](mission-critical-application-design.md#scale-unit-architecture) with [global distribution](mission-critical-application-design.md#global-distribution), consider how resources are optimally distributed or replicated across Azure regions.

Other resources are deployed regionally. These resources that are deployed as part of deployment stamp typically corresponds to a scale unit. However, a region can have more than one stamp, a stamp can have more than one unit. The reliability of regional resources is crucial as they're responsible for running the main workload.

This image shows the high-level design. A user accesses the application through a central global entry point that then redirects requests to a suitable regional deployment stamp.

![Mission-critical online architecture](./images/mission-critical-high-level-architecture.png)

### Design considerations

The mission-critical design methodology necessitates a multi-region deployment. This model ensures regional fault tolerance, so that application availability remains even when an entire region goes down. When designing a multi-region application, consider different deployment strategies, such as active-active and active-passive, alongside application requirements, because there are significant trade-offs between each approach. For mission-critical workloads, active-active model is highly recommended.

Not every workload supports or requires multiple regions running simultaneously. Precise application requirements should be weighed against these trade-offs to inform an optimal design decision. For certain application scenarios with lower reliability targets, active-passive or sharding, can be suitable alternatives.

[Availability Zones](/azure/availability-zones/az-overview#availability-zones) (AZ) allows highly available regional deployments across different data centers within a region. Nearly all Azure services are available in either a zonal configuration where service is pinned to a specific zone. The services are also zone-redundant where the platform automatically ensures the service spans across zones and can withstand a zone outage. These configurations allow for fault-tolerance up to a datacenter level.

### Design considerations

- **Regional and zonal capabilities**. Not all services or capabilities are available in every Azure region. There might be service availability implications depending on the selected deployment regions. Also,  [Availability Zones](/azure/availability-zones/az-region) aren't available in every region.

- **Regional pairs**. Azure regions are grouped into [regional pairs](/azure/best-practices-availability-paired-regions) consisting of two regions within the same geography. Some Azure services use paired regions to ensure business continuity and to protect against data loss. For example, Azure Geo-redundant Storage (GRS) replicates data to a secondary paired region automatically, ensuring that data is durable if the primary region isn't recoverable. If an outage affects multiple Azure regions, at least one region in each pair will be prioritized for recovery.

- **Data consistency**. For consistency challenges, consider using globally distributed data store, stamped regional architecture, a partially active-active deployment. In a partial deployment, some components are active across all regions while others are located centrally within the primary region.

- **Safe deployment**. The [Azure Safe Deploy Practice (SDP)](https://azure.microsoft.com/blog/advancing-safe-deployment-practices) ensures all code and configuration changes (planned maintenance) to the Azure platform undergo a phased roll-out, with health analyzed in case any degradation is detected during the release. After the Canary and Pilot phases have successfully completed, platform updates are serialized across regional pairs, ensuring that only one region in each pair is updated at a time.

- **Platform capacity**. Like any cloud provider, Azure has a finite amount of resources. Unavailability can be a result of capacity limitations in regions. If there's a regional outage, there will be an increase in demand for resources as the workload seeks to recover within the paired region. This outage might create a capacity challenge where supply temporarily does not satisfy demand.

### Design recommendations

- Deploy the solution within a minimum of two Azure regions to protect against regional outages. Select deployment regions that have the required capabilities and characteristics needed by the workload. The capabilities should achieve performance and availability targets, while fulfilling data residency and retention requirements.

  For example, some data compliance requirements might constrain the number of available regions and potentially force design compromises. In such cases, extra investment in operational wrappers is highly recommended to predict, detect, and respond to failures. Suppose you're constrained to a geography with two regions, in which only one region supports Availability Zones (3 + 1 datacenter model). Create a secondary deployment pattern using fault domain isolation to allow for both regions to be deployed in an active configuration, ensuring the primary region houses multiple deployment stamps.

  If suitable Azure regions don't all offer requisite capabilities, be prepared to compromise on the consistency of regional deployment stamps to prioritize geographical distribution and maximize reliability. If only a single Azure region is suitable, multiple deployment stamps ('regional scale-units') should be deployed within the selected region to mitigate some risk, using Availability Zones to provide datacenter-level fault tolerance. However, such a significant compromise in geographical distribution will drastically constrain the attainable composite SLA and overall reliability.

  > [!IMPORTANT]
  > For scenarios targeting a >= 99.99% SLO, a minimum of three deployment regions is recommended to maximize the composite SLA and overall reliability. Calculate the [composite SLA](/azure/architecture/framework/resiliency/business-metrics#composite-slas) for all user flows. Ensure the composite SLA is in-line with business targets.

- For high-scale application scenarios with significant volumes of traffic, design the solution to scale across multiple regions to navigate potential capacity constraints within a single region. Additional regional deployment stamps will achieve a greater composite SLA. The use of global resources will constrain the increase in composite SLA from adding further regions.

- Define and validate the recovery point objectives (RPO) and recovery time objectives (RTO).

- Within a single geography, prioritize the use of regional pairs to benefit from SDP serialized rollouts for planned maintenance, and regional prioritization for unplanned maintenance.

- Geographically co-locate Azure resources with users to minimize network latency and maximize end-to-end performance.

  - Technical solutions such as a Content Delivery Network (CDN) or edge caching can also be used to drive optimal network latency for distributed user bases. For more information, see [Global traffic routing](./mission-critical-networking-connectivity.md#global-traffic-routing), [Application delivery services](./mission-critical-networking-connectivity.md#application-delivery-services), and [Caching and static content delivery](./mission-critical-networking-connectivity.md#caching-and-static-content-delivery).

- Align current service availability with product roadmaps when selecting deployment regions; not all services may be available in every region on day 1.

## Containerization

A container has application code with related configuration files, libraries, and dependencies required for it to run. Containerization provides an abstraction layer for application code and its dependencies and achieves separation from the underlying hosting platform. This single software package is highly portable and capable of running consistently across different infrastructure platforms or cloud providers. Developers don’t need to rewrite code, deploy applications faster, and more reliably. Typical use cases are 'lift and shift' scenarios depending on the chosen application frameworks and external dependencies.

> [!IMPORTANT]
> Containers should be considered as the primary model for mission-critical application packages. It improves infrastructure utilization because multiple containers can be hosted on the same virtualized infrastructure. Also, because all software is included in the container, the application can be moved across different operating systems regardless of runtimes or library versions. A benefit is simplified management operations compared to traditional virtualized hosting.
> Mission-critical applications need to scale fast to avoid performance bottlenecks. Because container images are pre-built, startup can be minimized to only on bootstrapping the application, allowing rapid scalability.

### Design considerations
  
- **Monitoring**. Monitoring services can find it harder to access applications in containers. Third-party software is typically required to collect and store container state indicators, such as CPU usage or RAM usage.

- **Security**. The hosting platform OS kernel is shared across multiple containers, creating a single point of attack. However, the risk of host VM access is limited since containers are isolated from the underlying operating system.

- **State**. While it's possible to store data within a running container's filesystem, the data won't persist when the container is recreated. Instead, persist data by mounting external storage or using an external database.

### Design recommendations

- Containerize all application components, using container images as the primary model for application deployment packages.

- Prioritize Linux-based container runtimes when possible. The images are more lightweight, and new features for linux nodes/containers are released frequently.

- Make containers immutable and replaceable with short lifecycles.

- Ensure that all relevant logs and metrics are gathered from the container, container host, and underlying cluster. Send the gathered logs and metrics to a unified data sink for further processing and analysis.

- Store container images in [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) with [geo-replication](/azure/aks/operator-best-practices-multi-region#enable-geo-replication-for-container-images) to replicate container images across all regions. Enable [Azure Defender for container registries](/azure/security-center/defender-for-container-registries-introduction) to provide vulnerability scanning for container images. Make sure access to the registry is managed by Azure Active Directory.

## Container orchestration

There are several Azure application platforms capable of effectively hosting containers.
There are advantages and disadvantages associated with each of these Azure container platforms. Each option can be the optimal choice for certain scenarios. Compare the options in the context of business requirements. However, always optimize reliability, scalability, and performance. For information, see these articles:

- [Compute decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree)
- [Container option comparisons](/azure/container-apps/compare-options#container-option-comparisons).

> [!IMPORTANT]
> [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) should be considered as the primary application hosting platform where requirements allow. [Azure App Service](https://azure.microsoft.com/services/app-service/containers/) is a feasible alternative to AKS, providing a low-friction container platform without the complexity of Kubernetes.
>    The following sections explore these two options.

### Azure Kubernetes Service

As a managed Kubernetes service, Azure Kubernetes Service (AKS) allows for quick cluster provisioning without complex cluster administration activities and offers a rich feature set that includes advanced networking and identity capabilities. For the complete set of recommendations, see [Azure Well-Architected Framework review - Azure Kubernetes Service (AKS)]( /azure/architecture/framework/services/compute/azure-kubernetes-service/azure-kubernetes-service).

> [!IMPORTANT]
> Some foundational configuration decisions can't be changed without re-deploying an AKS cluster. For example, selection between public and private AKS clusters, enabling Azure Network Policy, Azure AD integration and the use of Managed Identities for AKS instead of Service Principals.

#### Design considerations and recommendations

##### Reliability

AKS manages the native Kubernetes control plane. If the control plane is unavailable, the workload will experience downtime. Take advantage of the reliability features offered by AKS.

- Deploy [AKS clusters across different Azure regions](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) as a scale-unit to maximize reliability and availability. Use [Availability Zones](/azure/aks/availability-zones) to maximize resilience within an Azure region by distributing AKS control plane and agent nodes across physically separate datacenters. However, if there’s colocation latency, you can do AKS deployment within a single zone or use [proximity placement groups](/azure/aks/reduce-latency-ppg) to minimize inter-node latency.

- Use the [AKS Uptime SLA](/azure/aks/uptime-sla) for production clusters to maximize Kubernetes API endpoint availability guarantees.

##### Scalability

Consider AKS [scale limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-kubernetes-service-limits), such as the number of node, node pools per cluster, clusters per subscription.

- If scale limits are reached, take advantage of the [scale-unit strategy](mission-critical-application-design.md#scale-unit-architecture) and deploy more units with clusters.

- Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically adjust the number of agent nodes in response to resource constraints.

- Utilize the [Horizontal pod autoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler) to adjust the number of pods in a deployment depending on CPU utilization or other selected metrics.

- For high scale and burst scenarios, consider the use of [Virtual Nodes](/azure/aks/virtual-nodes-cli) for extensive and rapid scale.

- Define [pod resource requests and limits](/azure/aks/developer-best-practices-resource-management#define-pod-resource-requests-and-limits) in application deployment manifests. Without this configuration, you might experience performance issues.

##### Isolation

Maintain boundaries between the infrastructure used by the workload and system tools. Sharing infrastructure might lead to high resource utilization and noisy neighbor situations.

- Use separate node pools for system and workload services. Dedicated node pools for workload components should be based on requirements for specialized infrastructure resources such as GPU, high memory VMs. In general, avoid deploying large numbers of node pools to reduce unnecessary management overhead.

- Use [taints and tolerations](/azure/aks/operator-best-practices-advanced-scheduler#provide-dedicated-nodes-using-taints-and-tolerations) to provide dedicated nodes and limit resource intensive applications.
- Evaluate application affinity and anti-affinity requirements and configure the appropriate colocation of containers on nodes.

##### Security

Default 'vanilla' Kubernetes requires significant configuration to ensure a suitable security posture for mission-critical scenarios. AKS addresses various security risks out of the box. The features include for private clusters, auditing and logging into Log Analytics, hardened node images, and the use of managed identities.

- Apply configuration guidance provided within the [AKS security baseline](/security/benchmark/azure/baselines/aks-security-baseline).

- Use the AKS features to handle cluster identity and access management to reduce operational overhead and apply consistent access management.

- Use managed identities instead of service principals to avoid management and rotation credentials. You can add [managed identities](/azure/aks/use-managed-identity) at the cluster level. At the pod level, you can use managed identities through [Azure AD workload identity](/azure/aks/workload-identity-overview).

- Utilize [Azure Active Directory integration](/azure/aks/managed-aad) for centralized account management and passwords, application access management, and identity protection. Use Kubernetes RBAC with Azure Active Directory for [least privilege](/azure/aks/azure-ad-rbac), and minimize granting administrator privileges to protect configuration and secrets access. Also, limit access to the [Kubernetes cluster configuration](/azure/aks/control-kubeconfig-access) file with Azure role-based access control. Limit access to [actions that containers can perform](/azure/aks/developer-best-practices-pod-security#secure-pod-access-to-resources), provide the least number of permissions and avoid the use of root / privileged escalation.

##### Upgrades

Clusters and nodes need to be upgraded regularly. AKS supports [Kubernetes versions](/azure/aks/supported-kubernetes-versions) aligned with the release cycle of native Kubernetes.

- Subscribe to the public [AKS Roadmap and Release Notes](https://github.com/azure/aks) on GitHub to stay up-to-date on upcoming changes, improvements, and most importantly Kubernetes version releases or the deprecation of old releases.

- Consider and apply the guidance provided within the [AKS checklist](https://www.the-aks-checklist.com/) to ensure alignment with Well-Architected best practice guidance.

- AKS supports different [ways to update nodes and/or clusters](/azure/aks/upgrade-cluster) in a manual or automated way. [Planned Maintenance](/azure/aks/planned-maintenance) can be used to define maintenance windows for these operations. New images are released weekly. AKS also supports [auto-upgrade channels](/azure/aks/upgrade-cluster#set-auto-upgrade-channel) to automatically upgrade AKS clusters to newer versions of Kubernetes and/or newer node images once available.

##### Networking

Evaluate the network plugins that best fit your use case. Do you need granular control of traffic between pods? Azure supports kubenet, [Azure CNI](/azure/aks/concepts-network#compare-network-models), and also [bring-your-own-cni](/azure/aks/use-byo-cni) for specific use cases.

Prioritize the use of Azure CNI after assessing network requirements and the size of the cluster. Azure CNI enables the use of [Azure](/azure/aks/use-network-policies) or Calico Network Policies for controlling traffic within the cluster.

##### Monitoring
Your observability options should be able to capture logs and metrics from running pods. Also gather information from Kubernetes Metrics API to monitor the health of running resources and workloads. 

- Use [Azure Monitor and Application Insights](/azure/azure-monitor/insights/container-insights-overview) to centrally collect metrics, logs, and diagnostics from AKS resources for troubleshooting purposes.

- Enable and review [Kubernetes master node logs](/azure/aks/view-master-logs).

- Configure the [scraping of Prometheus metrics](/azure/azure-monitor/insights/container-insights-prometheus-integration) with Azure Monitor for containers. Azure Monitor for containers (Container Insights) provides a seamless onboarding experience, enables various monitoring capabilities out of the box and more advanced scenarios via its built-in [Prometheus scraping](/azure/azure-monitor/insights/container-insights-prometheus-integration) support.

##### Governance

Apply policies to enforce centralized safeguards to AKS clusters in a consistent manner. Policy assignments should be applied at a subscription scope or higher to drive consistency across development teams.

- Control what functions pods are granted by using Azure Policy, and if running contradicts policy. This access is defined through built-in policies provided by the [Azure Policy Add-on for AKS](/azure/governance/policy/concepts/policy-for-kubernetes).

- Establish a consistent reliability and security baseline for AKS cluster and [pod](/azure/aks/use-pod-security-on-azure-policy) configurations using [Azure Policy](/azure/governance/policy/overview).
 
- Use the [Azure Policy Add-on for AKS](/azure/governance/policy/concepts/policy-for-kubernetes) to control pod functions, such as root privileges, and disallow pods that don't conform to policy.

> [!NOTE]
> 
> When deploying into an Azure landing zone, be aware that the required Azure Policy to ensure consistent reliability and security should be provided by the landing zone implementation.

> The mission-critical [reference implementations](mission-critical-overview.md#illustrative-examples) provide a suite of baseline policies to drive recommended reliability and security configurations.

### Azure App Service

For web and API based workload scenarios, [Azure App Service](https://azure.microsoft.com/services/app-service/containers/) can be a feasible alternative to AKS, providing a low-friction container platform without the complexity of Kubernetes. For complete set of recommendations, see [Reliability considerations for App Services](/azure/architecture/framework/services/compute/azure-app-service/reliability) and [Operational Excellence for App Services](/azure/architecture/framework/services/compute/azure-app-service/operational-excellence).

#### Design considerations and recommendations

##### Reliability

Evaluate the use of TCP and SNAT ports. TCP connections are used for all outbound connections; but, SNAT ports are used when making outbound connections to public IP addresses. SNAT port exhaustion is a common failure scenario that can be predicted by load testing while monitoring ports using Azure Diagnostics. Predictively detect SNAT port exhaustion through load testing. Monitoring ports using Azure Monitor. If SNAT errors occur, it's necessary to either scale across more/larger workers, or implement coding practices to help preserve and reuse SNAT ports, such as connection pooling or the lazy loading of resources.

TCP port exhaustion another failure scenario that occurs when the sum of outbound connections from a given worker exceeds the capacity. The number of available TCP ports depend on the size of the worker. Recommendations are provided in [TCP and SNAT ports](/azure/architecture/framework/services/compute/azure-app-service/reliability#tcp-and-snat-ports).

##### Scalability

Plan for future scalability requirements and application growth so that recommendations can be applied from the start, avoiding future technical migration debt as the solution grows.

- Enable Autoscale to ensure adequate resources are available to service requests. Evaluate per-app scaling for high density hosting on Azure App Service.

- Azure App Service has a default, soft limit of [instances per App Service Plan](/azure/app-service/overview-hosting-plans#should-i-put-an-app-in-a-new-plan-or-an-existing-plan).

- Apply autoscale rules. App Service plan will scale out if any rule within the profile is met but will only scale in if all rules within the profile are met. Use a scale-out and scale-in rule combination to ensure auto-scale can take action to both scale out and scale in. Understand the behavior of multiple scaling rules in a single profile.

- Per-app scaling can be enabled at the App Service Plan level to allow an application to scale independently from the App Service plan that hosts it. Apps are allocated to available nodes using a best effort approach for an even distribution. While an even distribution isn't guaranteed, the platform will make sure that two instances of the same app won't be hosted on the same instance.

##### Monitoring

Monitor application behavior and get access to relevant logs and metrics is crucial to making sure the application is working as expected.

- Diagnostic logging provides the ability to ingest application and platform level logs into either Log Analytics, Azure Storage, or a third-party tool via Event Hubs.

- Application performance monitoring with Application Insights provides deep insights into application performance.
Mission-critical applications must have the ability to self-heal if there are failures. Enable [AutoHeal](https://azure.github.io/AppService/2021/04/21/Announcing-Autoheal-for-Azure-App-Service-Linux.html) to automatically recycle unhealthy workers.

Appropriate health checks must be used to assess all critical downstream dependencies to ensure overall health. Enabling [Health Check](https://aka.ms/appservicehealthcheck) to identify non-responsive workers is highly recommended.

##### Deployment

To work around the default limit of instances per App Service Plan, deploy Azure App Service Plans in multiple scale-units in a single region.  Also deploy App Service Plans in an [Availability Zone configuration](https://azure.github.io/AppService/2021/08/25/App-service-support-for-availability-zones.html) to ensure worker nodes are distributed across zones within a region. Consider opening a support ticket to increase the maximum number of workers to twice the instance count required to serve normal peak load.

## Serverless compute

Serverless computing provides resources on-demand and eliminates the need to manage infrastructure. The cloud provider automatically provisions, scales, and manages resources required to run deployed application code. Microsoft Azure provides several serverless compute platforms:

- [Azure Functions](/azure/azure-functions/functions-overview): Application logic is implemented as distinct blocks of code or _functions_, which run in response to events, such as an HTTP request or queue message, with each function scaling as necessary to meet demand.

- [Azure Logic Apps](/azure/logic-apps/logic-apps-overview): Best suited for creating and running automated workflows that integrate various apps, data sources, services and systems. Similar to Azure Functions, there are built-in triggers for event-driven processing but instead of deploying application code, Logic Apps can be composed using a graphical user interface, which supports code blocks like conditionals and loops.

- [Azure API Management](https://azure.microsoft.com/services/api-management/): Publish, secure, transform, maintain, and monitor APIs using the Consumption tier.

- [Power Apps & Power Automate](/powerapps/powerapps-overview): Provides a 'low-code/no-code' development experience, with simple workflow logic and integrations configurable through connections in a user interface.

For mission-critical applications, serverless technologies provide a near-zero friction development and operational experience, which can be highly valuable for simple business use cases. However, this simplicity comes at the cost of flexibility in terms of scalability, reliability, and performance, which is unacceptable for most mission-critical application scenarios.

The following sections provide design considerations and recommendations for Azure Function and Azure Logic Apps as alternative platforms for non-critical workflow scenarios.

### Azure Functions

Mission-critical workloads will have critical and non-critical system flows. While non-critical components can be hosted as separate containers in AKS, applying full set of considerations can bring more complexity than needed. Azure Functions is a viable choice for flows that don't have the same stringent business requirements as critical system flows. they're well-suited for event-driven flows with short-lived processes because functions perform distinct operations that run as fast as possible.

#### Design considerations and recommendations

Choose an [Azure Functions hosting option](/azure/azure-functions/functions-scale) that's applicable to reliability tier selected for the application. Premium SKU App Service plan is recommended because it allows configuration of the compute instance size. **Dedicated** is the least serverless option with the option to autoscale, though these scale operations are slower when compared to other plans. Prioritize the use of the Azure Functions Premium hosting plan to maximize reliability and performance.

There are some security considerations. When using the HTTP trigger to expose an external endpoint, protect the HTTP endpoint from common external attack vectors using a Web Application Firewall (WAF).

For restricting access to private virtual networks, Private Endpoints are recommended. They can also be used to mitigate data exfiltration risks, such as malicious admin scenarios.

Azure Functions code must be subject it code scanning tools that must be integrated with CI/CD pipelines.

### Azure Logic Apps

Similar to Azure Functions, there are built-in triggers for event-driven processing, however, instead of deploying application code Logic Apps can be composed using a graphical user interface, which supports blocks like conditionals, loops, and other constructs.

There are multiple [deployment modes](/azure/logic-apps/single-tenant-overview-compare) available. *Standard* is recommended to ensure a single tenant deployment and mitigate noisy neighbor scenarios. It uses the containerized single-tenant Azure Logic Apps runtime based on Azure Functions. In this mode, the logic app can have multiple stateful and stateless workflows. Be aware of the upper configuration limits.

## Constrained migrations using IaaS

Many applications with existing on-premises deployments use virtualization technologies and redundant hardware to provide mission-critical levels of reliability. Modernization is often hindered by business constraints that prevent full alignment with the cloud-native baseline (north-star) architecture pattern recommended for mission-critical workloads. So, many applications adopt a phased approach, with initial cloud deployments using virtualization and Azure Virtual Machines as the primary application hosting model. The use of IaaS Virtual Machines can be required for certain scenarios:

- Available PaaS services don't provide the required performance or level of control.
- The workload requires operating system access, specific drivers, or network and system configurations.
- The workload does not support running in containers.
- Lack of support for third-party workloads.

This section focuses on best usage of Azure Virtual Machines and associated services in order to maximize the reliability of the application platform. It highlights key aspects of the mission-critical design methodology, which transpose cloud-native and IaaS migration scenarios.

### Design considerations

- The use of IaaS Virtual Machines significantly increases operational costs compared to PaaS services, through management responsibility of the virtual machine and the operating system. Managing virtual machines necessitates the frequent roll-out of software packages and updates.

- Azure provides certain capabilities to increase the availability of Virtual Machines, options are:
  - [Availability Sets](/azure/virtual-machines/availability-set-overview) can be used to protect against network, disk, and power failures by distributing virtual machines across up to fault domains and update domains.
  - [Availability zones](/azure/availability-zones/az-overview) can be used to achieve even higher levels of reliability by distributing VMs across physically separated data center within a region.
  - [Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/overview) provide functionality to automatically scale the number of virtual machines along with capabilities to monitor instance health and automatically repair [unhealthy instances](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs).

### Design recommendations

> [!IMPORTANT]
> - Prioritize the use of PaaS services and Containers where possible to reduce operational complexity and cost. Only use IaaS Virtual Machines when required.

- [Right-size VM sku sizes](/azure/virtual-machines/sizes) to ensure effective resource utilization.

- Deploy three or more Virtual Machines across [Availability zones](/azure/availability-zones/az-overview) to achieve data center level fault tolerance.
  - If you're deploying commercial off-the-shelf software, consult with the software vendor and test adequately before deploying into production.

- For workloads that can't be deployed across Availability Zones, use [Availability Sets](/azure/virtual-machines/availability-set-overview) with three or more VMs.
  - Availability Sets should only be considered if Availability Zones don't comply with workload requirements, such as for 'chatty' workloads with low latency requirements.

- Prioritize the use of Virtual Machine Scale Sets for scalability and zone-redundancy. This point is particularly important for workloads with varying load. For instance, number of active users or requests per second.
  
- Don't access individual virtual machines directly, use load balancers in front when possible.

- To protect against regional outages, deploy application virtual machines across multiple Azure regions.
  - Refer to the [networking and connectivity design area](/azure/architecture/framework/mission-critical/mission-critical-networking-connectivity#global-traffic-routing) for further details about how to optimally route traffic between active deployment region.

- For workloads which don't support multi-region active-active deployments, consider active-passive by using hot/warm standby virtual machines for regional failover.

- Prioritize the use of standard images from the Azure Marketplace over custom images that need to be maintained.

- Implement automated processes to deploy and roll out changes to virtual machines, avoiding any manual intervention. See [IaaS considerations](./mission-critical-operational-procedures.md#iaas-specific-considerations-when-using-virtual-machines) in the [Operational procedures](./mission-critical-operational-procedures.md) design area for more.

- Implement chaos experiments to inject application faults into virtual machine components while observing the mitigation of faults. For more information, see [Continuous validation and testing](./mission-critical-deployment-testing.md#continuous-validation-and-testing).

- Monitor virtual machines and ensure diagnostic logs and metrics are ingested into a [unified data sink](/azure/architecture/framework/mission-critical/mission-critical-health-modeling#unified-data-sink-for-correlated-analysis).

Follow and apply security practices for mission-critical application scenarios, when applicable, and the [Security best practices for IaaS workloads in Azure](/azure/security/fundamentals/iaas).

## Next step

Review the considerations for the data platform.
> [!div class="nextstepaction"]
> [Data platform](./mission-critical-data-platform.md)
