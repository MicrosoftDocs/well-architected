---
title: Architecture Best Practices for Azure Container Apps
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure Container Apps.
author: igorjnzl
ms.author: igorjovovic
ms.topic: concept-article
ms.date: 04/05/2025
ms.product: azure-container-apps
azure.category:
  - containers
---

# Architecture best practices for Azure Container Apps

Azure Container Apps is a fully managed serverless container service that runs microservices and containerized applications on Azure. It provides built-in autoscaling, including scale to zero, and supports multiple programming languages and frameworks. Container Apps integrates with Azure Kubernetes Service (AKS) for advanced networking and monitoring. It also provides seamless deployment and management of containerized applications without the need to manage underlying infrastructure. It supports both HTTP-based and event-driven applications, so it's ideal for building modern, scalable, and resilient cloud-native applications.

This article assumes that as an architect, you've reviewed the [compute decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree) and chose Container Apps as the compute platform for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:  

- Container Apps

:::image type="content" source="./_images/container-apps.svg" alt-text="Diagram that shows the Container Apps architecture." lightbox="./_images/container-apps.svg" border="false":::

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the performance and reliability of your applications. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Choose appropriate SKU configurations.** Select an environment SKU that aligns with the resource and performance requirements of your container apps.
> - **Build redundancy to improve resiliency.** For ingress-exposed (HTTP or Transmission Control Protocol (TCP)) apps, use at least three replicas to help ensure availability. To minimize cold starts, configure a minimum number of always-ready replicas.
>
>   Use availability zones as part of your resiliency strategy to increase availability when you deploy to a single region. Many Azure regions provide availability zones. The zones are positioned close enough to ensure low-latency connections among them, but far enough apart to minimize the risk of local outages affecting multiple zones.
>
>   For critical workloads, deploy Container Apps environments across multiple regions and use [Azure Front Door](/azure/frontdoor/front-door-overview) or [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) for traffic management. These services help ensure high availability and business continuity. If a regional outage occurs, you can automatically redirect traffic to a secondary region to minimize downtime and data loss.
> - **Implement horizontal autoscaling.** Configure autoscaling by using scale rules that are based on HTTP requests, TCP connections, or custom metrics, such as CPU and memory thresholds. You can define custom metrics in Azure Service Bus, Azure Event Hubs, Apache Kafka, and Azure Managed Redis. Use autoscaling to dynamically manage loads and maintain high availability during peak usage times.
>
>   Ensure that your container apps can still serve requests within your service-level objective (SLO) under high load.
> - **Monitor reliability and overall health indicators of the container app.** Collect logs and metrics to monitor health, identify performance and reliability trends, and troubleshoot problems. For more information about designing the reliability and health monitoring solution for your workload, see [Health modeling for workloads](/azure/well-architected/design-guides/health-modeling).
>
>   *Implement monitoring tooling and alerts.* Activate monitoring tools, like Azure Monitor and OpenTelemetry. Set up alerts to promptly detect and respond to events that affect reliability.
>
>   *Configure health probes.* Set up startup, readiness, and liveness probes for all services to monitor and maintain application health.  
>
> - **Configure self-healing mechanisms to automatically restart unhealthy container instances.** Automatic restarts increase the reliability and availability of your applications. They help ensure quick recovery from failures without manual intervention. Use health probes to detect failing containers and configure resiliency policies to automatically handle retries and circuit breakers.

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
| Enable Container Apps [availability zone support](/azure/reliability/reliability-azure-container-apps#availability-zone-support) to automatically distribute replicas across the zones in a region. Traffic is load balanced among the replicas. | If a zone failure occurs, traffic is automatically routed to the replicas in the remaining zones. |
| Define [resource quotas](/azure/container-apps/quotas) and limits. | Prevent resource contention to help ensure fair allocation and avoid performance degradation. Use monitoring over time to observe actual resource usage and adjust quotas and limits accordingly.|
| Use [volume mounts](/azure/container-apps/storage-mounts#azure-files) to store data outside the container in stateful applications. For enhanced data resilience, use Azure zone-redundant storage (ZRS) to help ensure high availability and durability of your data. | Ensure data persistence and integrity across container restarts and failures. Use ZRS to protect against data loss from zonal failures, which provides a robust solution for critical stateful applications. |
| Implement liveness, readiness, and startup [health probes](/azure/container-apps/health-probes) for your container apps.<br><br>Liveness probes detect and restart containers that are in a failed state.<br>Recommended settings: `failureThreshold: 3`, `periodSeconds: 10`, `timeoutSeconds: 5`, `successThreshold: 1`, and `initialDelaySeconds: 10`<br><br>Readiness probes ensure that only healthy containers receive traffic.<br>Recommended settings: `failureThreshold: 60`, `periodSeconds: 1`, `timeoutSeconds: 1`, `successThreshold: 1`, and `initialDelaySeconds: 5`<br><br>Startup probes prevent premature restarts by allowing slow-starting apps to initialize properly.<br>Recommended settings: `failureThreshold: 60`, `periodSeconds: 1`, `timeoutSeconds: 1`, `successThreshold: 1`, and `initialDelaySeconds: 0`| Proper probe configuration helps ensure that your container apps run smoothly and can handle traffic. Improper probe configurations can lead to unintended restarts or downtime.|  
| Use the Container Apps built-in [observability features](/azure/container-apps/log-options), such as log streaming, container console, and Azure Monitor metrics and alerts, to ensure proactive monitoring and efficient debugging. | Container Apps provides deep observability support, including integration with the .NET Aspire dashboard and Java metrics. These tools enable enhanced insights for key ecosystems. You can also use the OpenTelemetry collector for comprehensive distributed tracing and metrics collection. These features improve application reliability because you can quickly identify and resolve problems. |
| Implement service discovery [resiliency policies](/azure/container-apps/service-discovery-resiliency), such as retries, time-outs, and circuit breakers, to proactively prevent, detect, and recover from service request failures. | Enhance the reliability of your container apps by ensuring smoother and more resilient inter-service communication. |
| Implement horizontal [autoscaling](/azure/container-apps/scale-app) by using scale rules that are based on HTTP requests, TCP connections, or custom metrics, such as CPU and memory thresholds. You can define custom metrics in Service Bus, Event Hubs, Apache Kafka, and Azure Managed Redis. | The workload can dynamically manage loads and maintain high availability during peak usage times.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Container Apps.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review security baselines.** To enhance the security posture of your workload, review the [security baseline for Container Apps](/security/benchmark/azure/baselines/azure-container-apps-security-baseline).
> - **Integrate with Microsoft Entra ID for [identity and access management](/azure/well-architected/security/identity-access#the-role-of-an-identity-provider).** Use [role-based access control (RBAC)](/azure/aks/azure-ad-rbac) with Microsoft Entra ID for least privilege access.
>
>   *Use managed identities with Microsoft Entra ID* for secure, credential-free access to Azure resources.  
> - **Implement segmentation and network controls.** Deploy private container apps environments and use internal ingress mode for isolation from the public internet.  
>
>   *Control egress traffic.* To prevent data exfiltration, integrate your container apps environment into a custom virtual network that has user-defined routes to help secure outbound traffic.  
>
> - **Maintain a hardened workload's software supply chain.** Implement container-aware scanning in your secure continuous integration and continuous delivery (CI/CD) pipelines. This feature detects vulnerabilities and helps ensure the integrity of your container images. For more information, see [Containers Secure Supply Chain](/azure/security/container-secure-supply-chain/articles/container-secure-supply-chain-implementation/containers-secure-supply-chain-overview).
>
>   *Reduce the attack surface.* Harden container images and remove unused components. Use lean and minimal base images like Alpine and Chiselled Ubuntu images.
>
>   *Integrate with Microsoft Defender.* Use Microsoft Defender for Containers to scan images in Azure Container Registry.
> - **Encrypt data at rest and data in transit.** Use modern industry-standard methods to guard confidentiality and integrity.
>
>   *Use Azure Key Vault.* Store sensitive configuration values and secrets in Key Vault to protect them from unauthorized access.  
>
>   *Enable mutual Transport Layer Security (mTLS).* Use mTLS to authenticate and encrypt traffic between services. This feature verifies both parties, which enhances security.
>
>   *Enforce HTTPS.* Configure Envoy proxy to redirect all HTTP traffic to HTTPS. The default configuration for Envoy is `allowInsecure: false`.  
> - **Implement a security monitoring strategy.** Capture detailed logs for monitoring and auditing. Send system and console logs to a Log Analytics workspace, Event Hubs, or a non-Microsoft solution for monitoring and auditing purposes. Scrub sensitive data from logs. Console logs originate from the `stderr` and `stdout` streams in the app.

### Configuration recommendations

| Recommendation | Benefit |
|----------------|---------|
| Use [managed identities](/azure/container-apps/managed-identity) to access Microsoft Entra ID protected resources. Assign a managed identity to your container app to access Microsoft Entra ID protected Azure resources, such as a storage account or key vault, without having to use keys or passwords. | Centralize identity management and eliminate the need for manual credential management. Simplify secure access to Azure resources. |
| Deploy container apps in a [private network](/azure/container-apps/networking) to integrate them into an existing virtual network. Use features such as private application connectivity, network security group (NSG) attachment, and resource communication via a private IP address. | Provide isolation from the public internet and enable secure communication between container apps and other network resources.|
| Use [Key Vault](/azure/container-apps/key-vault-certificates-manage) to manage certificates and app secrets with enhanced security. Key Vault keeps certificates and other app secrets separate from your app's configuration. It also provides an access audit log that details when certificates and secrets are updated, retrieved, and rotated. | Help protect sensitive information, ensure compliance, and support secure secret management by using logging and certificate rotation capabilities. |
| Use [Azure Application Gateway with Web Application Firewall](/azure/container-apps/waf-app-gateway) enabled to help secure HTTP and HTTPS traffic when you publish your container app via a reverse proxy. Web Application Firewall scans incoming HTTP traffic for potential Open Web Application Security Project (OWASP) attacks. | Enhance security by protecting against common web vulnerabilities and providing centralized traffic management. |
| Authenticate with Microsoft Entra ID to [Container Registry](/azure/container-apps/managed-identity-image-pull) to avoid the use of administrative credentials. You can control access via RBAC. |  Enable granular access control via RBAC for container image management, which helps ensure secure and credential-free authentication. |
| Use [NSG rules to help secure traffic](/azure/container-apps/firewall-integration) that accesses your internal container apps endpoint. NSG rules provide more granular control of which virtual networks can communicate with your container apps. | Enhance network security by restricting access to only trusted networks, which minimizes attack surfaces. |
| Control outbound traffic by using [user-defined network routes](/azure/container-apps/user-defined-routes). Control how your containers communicate with resources outside of the container environment. You can route traffic to Azure Firewall, Azure NAT Gateway, or a non-Microsoft appliance. | Ensure controlled and secure outbound traffic flow, which supports advanced routing and inspection policies. |
| Configure [logging options](/azure/container-apps/log-options) to send logs to a Log Analytics workspace, Event Hubs, or a non-Microsoft solution.| Support centralized monitoring, diagnostics, and auditing while ensuring compliance with sensitive data handling policies.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Container Apps and its environment.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Select appropriate pricing plans.** Choose the most cost-effective pricing plans for your container apps based on workload requirements and expected usage patterns.
>
>   Take advantage of the [Azure savings plan for compute](https://azure.microsoft.com/pricing/offers/savings-plan-compute) by committing to a fixed hourly rate for one or three years. Use a savings plan to save up to 17% compared to pay-as-you-go pricing. Optimize your budget and reduce overall expenses for long-term, predictable workloads.
>
> - **Optimize workload component costs.** Regularly review and adjust CPU and memory allocations to match the needs of your applications. This practice prevents over-provisioning and minimizes costs.  
>
>   *Use managed disk tiers.* When you use volume mounts, select appropriate managed disk tiers and sizes to optimize storage costs for persistent data. Only pay for what you need.  
>
>   *Use streamlined and optimized container images* to improve startup times and resource efficiency, which reduces storage and network costs.  
> - **Optimize scaling costs.** Configure autoscaling policies to automatically scale down resources during low-demand periods and scale up during peak times. This practice ensures efficient resource usage.  
> - **Optimize networking costs.** Optimize network paths to minimize data transfer costs, especially for applications that have high bandwidth volumes.  
> - **Use cost management tools.** Use Microsoft Cost Management tools to track and analyze spending, set budgets, create cost alerts, and implement consistent tagging across resources. 
>
>    These tools provide detailed visibility into cloud spending, help identify cost-saving opportunities, ensure adherence to budgetary constraints, and allow for granular tracking and reporting of costs associated with specific workloads, applications, and environments.  

### Configuration recommendations

| Recommendation | Benefit |
|----------------|---------|
| Regularly review and adjust CPU, memory allocations, and other [metrics](/azure/container-apps/metrics#available-metrics) to align with the actual needs of your applications.| Prevent over-provisioning and reduce unnecessary costs by ensuring that resources are rightsized for workloads. |
| Implement scale-to-zero [autoscaling](/azure/container-apps/scale-app) rules for apps that don't need to continuously run.| Eliminate costs during periods of inactivity, which ensures that you only pay for resources when you need them. This approach significantly reduces expenses for apps that have variable or infrequent usage patterns. |
| Choose the appropriate [managed disk tiers](/azure/container-apps/storage-mounts) for stateful applications. Base your selection on storage performance and capacity needs, and consider using reserved disks for predictable workloads.| Ensure that you only pay for the necessary storage performance, which prevents excess costs from over-provisioned storage. Reserved disks can provide significant cost savings for long-term storage requirements by offering discounts compared to pay-as-you-go pricing.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Container Apps.

> [!div class="checklist"]
>
> - **Implement an infrastructure as code (IaC) deployment approach.** Implement template-based deployments by using tools like Bicep or Terraform. Ensure that all deployments are repeatable, traceable, and stored in a source code repository.
>
> - **Automate infrastructure and workload deployments.** Use standard software solutions to manage, integrate, and automate the deployment of your workloads.
>
>   *Set up deployment pipelines* to redeploy the environment to another region if a regional outage occurs. This approach helps you quickly restore and redeploy critical data and configurations in another region, which enhances disaster recovery capabilities and minimizes downtime during regional failures.
>
>   *Build automated processes* to set up your environments with necessary configurations and deployments by using CI/CD pipelines.
> - **Implement a comprehensive monitoring strategy.** Configure diagnostics settings to capture logs, metrics, and diagnostics data. Use tools like Azure Monitor and Application Insights to track application health and performance, identify trends in performance and reliability, and troubleshoot problems.
>
>   *Emit telemetry for workloads.* Design your workloads to emit telemetry data, including liveliness and readiness statuses, to facilitate monitoring and troubleshooting.  
>
>   *Monitor performance metrics.* Continuously monitor key performance metrics such as CPU, memory, and network usage to identify opportunities for cost optimization and operational efficiencies.
> - **Implement chaos engineering.** Apply chaos engineering practices by using tools such as Azure Chaos Studio to identify potential reliability problems within your Container Apps environment. Conduct experiments to ensure that your applications can withstand unexpected failures. Conduct performance testing by using tools such as Azure Load Testing to make sure scaling rules work as expected without disruptions to clients.  
> - **Implement consistent resource tagging** across all container apps and other workload resources. Consistent tagging facilitates efficient resource management, cost tracking, and automation.
> - **Enforce workload governance.** Azure Policy helps ensure consistent compliance with organizational standards, automates policy enforcement, and provides centralized visibility and control over your workload's resources.

### Configuration recommendations

| Recommendation | Benefit |
|----------------|---------|
| Store your Container Apps environment configurations as [IaC](/azure/templates/microsoft.app/containerapps#bicep-resource-definition), and set up deployment pipelines to redeploy the environment to another region if a regional outage occurs. | Ensure that you can quickly restore and redeploy critical data and configurations in another region, which enhances disaster recovery capabilities and minimizes downtime during regional failures. |  
| Use [revisions](/azure/container-apps/revisions-manage) to implement blue-green deployments or canary deployments.<br><br>Revisions require that you [tag and version container images appropriately](/azure/container-registry/container-registry-image-tag-version). You can use labels on revisions to facilitate sharing, for example for user acceptance testing or limited previews. | Minimize downtime and reduce risk during releases by enabling safe rollouts and quick rollbacks.|
| [Configure Azure Monitor](/azure/container-apps/log-options) and Application Insights. | Track the performance and health of your container apps and provide detailed insights into application performance and reliability. Use these insights to proactively detect and resolve problems. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for Container Apps.

> [!div class="checklist"]
>
> - **Develop a detailed capacity plan** to ensure that your container apps have sufficient resources to handle varying loads while avoiding over-provisioning. A capacity plan helps optimize cost and performance.
>
>    Regularly update your plan to document the correct resource allocations, autoscale settings, and failover strategies for your container apps. This approach ensures that you can quickly restore and redeploy critical data and configurations in another region, which enhances disaster recovery capabilities and minimizes downtime during regional failures.
> - **Enable autoscaling.** Configure autoscaling policies to automatically adjust the number of container instances based on real-time demand, which ensures optimal performance during peak and off-peak times.  
> - **Optimize resource allocation.** Continuously monitor and adjust CPU and memory allocations based on performance metrics to ensure efficient resource usage and prevent over-provisioning.  
> - **Conduct load testing.** Perform regular load testing to evaluate the performance and scalability of your container apps under different conditions. Testing helps ensure that container apps can handle expected traffic levels.  
> - **Separate workloads.** Deploy critical and sensitive workloads in separate Container Apps environments to avoid noisy neighbor problems. Distribute workloads across multiple environments to ensure that critical applications have dedicated resources. This method also ensures that the performance demands of less-critical applications don't affect critical applications.  

### Configuration recommendations

| Recommendation | Benefit |
|----------------|---------|
| Configure [autoscaling](/azure/container-apps/scale-app) policies to automatically adjust the number of container instances in response to resource demands. | Help maintain application performance and cost-efficiency. Ensure that resources are available when needed and conserved when not needed. Use Azure Load Testing to do load testing experiments, and adjust autoscaling policies as needed. |
| Use the [workload profiles](/azure/container-apps/workload-profiles-overview) dedicated tier for applications that require predictable performance and guaranteed resource allocation. | Provide dedicated resources for critical applications, which ensures consistent performance and reduces the risk of resource contention. |
| Use custom scaling [metrics](/azure/container-apps/metrics#available-metrics), such as metrics based on application-specific data, to drive autoscaling decisions. | Ensure that scaling actions are based on relevant workload demands, which improves the efficiency and responsiveness of your container apps. |

## Azure policies

Azure provides an extensive set of built-in policies related to Container Apps and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- **Diagnostics settings should be enabled:** Enable logging by category group for Container Apps environments (`microsoft.app/managedenvironments`) to send information to Storage. This setting ensures that Storage consistently collects logs and metrics for monitoring, troubleshooting, and compliance.

- **Authentication for Container Apps should be enabled:** Enable authentication to prevent anonymous HTTP requests, and authenticate requests via tokens before they reach a Container Apps environment.

- **Container Apps environments should use network injection:** Configure Container Apps environments with virtual network injection to isolate them from the public internet, enable network integration with resources on-premises or other Azure virtual networks, and achieve granular control over network traffic.

- **Public network access should be disabled:** Disable public network access to improve security by exposing the Container Apps environment through an internal load balancer. This approach blocks internet access to all container apps within the environment.

- **External network access should be disabled:** Enforce internal-only ingress to ensure that inbound communication for container apps is limited to callers within the Container Apps environment.

- **HTTPS should be enforced:** Ensure that container apps are only accessible over HTTPS to protect data in transit from network layer eavesdropping attacks.

- **Managed identities should be enabled:** Require managed identities for Container Apps environments to securely authenticate to any resource that supports Microsoft Entra ID authentication.

For comprehensive governance, review the [Azure Policy built-in definitions for Container Apps](/azure/container-apps/policy-reference) and other policies that might affect the security of the networks.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Microservices with Azure Container Apps](/azure/architecture/example-scenario/serverless/microservices-with-container-apps).

## Next steps

- [Deploy your first container app by using the Azure CLI](/azure/container-apps/get-started)
- [Architecture design patterns that support reliability](/azure/well-architected/reliability/design-patterns)
- [Mission-critical workloads](/azure/well-architected/mission-critical/mission-critical-overview)
- [Site reliability engineering resources](/azure/site-reliability-engineering/)
- [Container Apps overview](/azure/container-apps/overview)
- [Container Apps landing zone accelerator](/azure/cloud-adoption-framework/scenarios/app-platform/container-apps/landing-zone-accelerator)


