---
title: Architecture Best Practices for Azure Container Apps
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure Container Apps.
author: igorjnzl
ms.author: igorjovovic
ms.topic: conceptual
ms.date: 01/16/2024
ms.product: azure-container-apps
azure.category:
  - containers
---

# Architecture best practices for Azure Container Apps

Azure Container Apps is a fully managed serverless container service that runs microservices and containerized applications on Azure. It provides built-in autoscaling, including scale to zero, and supports multiple programming languages and frameworks. Container Apps integrates with Azure Kubernetes Service (AKS) for advanced networking and monitoring. It also provides seamless deployment and management of containerized applications without the need to manage underlying infrastructure. It supports both HTTP-based and event-driven applications, so it's ideal for building modern, scalable, and resilient cloud-native applications.

This article assumes that as an architect, you've reviewed the [compute decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree) and chose Container Apps as the compute platform for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
> Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for [Azure offering] and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments. 
>
> Foundational architecture that demonstrates the key recommendations:  
> [Container Apps baseline architecture](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:  

- Container Apps

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

In the cloud, failures can happen. Instead of trying to prevent failures entirely, the goal is to architect for resiliency, ensuring that your application can continue to function and recover quickly. Reliability focuses on ensuring your application can recover from failures and continue to operate effectively. This section outlines best practices and design considerations to enhance the reliability of Container Apps, ensuring high availability and fault tolerance in your deployments.

### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the performance and reliability of your applications. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Choose appropriate sku configurations.** Select an environment SKU that aligns with the resource and performance requirements of your container apps.
> - **Build redundancy to improve resiliency.** For ingress-exposed (HTTP or TCP) apps, use at least three replicas to ensure availability. To minimize cold starts, configure a minimum number of always-ready replicas.
>
>   Use availability zones as part of your resiliency strategy to increase availability when you deploy to a single region. Many Azure regions provide availability zones. The zones are close enough to have low-latency connections among them, but far enough apart to reduce the likelihood that local outages will affect more than one zone.
>
>   For critical workloads, deploy Container Apps across multiple regions and utilize [Azure Front Door](/azure/frontdoor/front-door-overview) or [Azure Traffic Manager](/azure/aks/operator-best-practices-multi-region#use-azure-traffic-manager-to-route-traffic) for traffic management to ensure high availability and business continuity. In the event of a regional outage, traffic can be automatically redirected to a secondary region, minimizing downtime and data loss.
> - **Implement horizontal auto-scaling.** Configure auto-scaling using scale rules based on HTTP requests, TCP connections, or custom metrics (e.g., CPU, memory, Azure Service Bus, Azure Event Hubs, Apache Kafka, Redis) to dynamically manage loads and maintain high availability during peak usage times.
>
>   Ensure your container apps can still serve requests within your SLO under high-load.
> - **Configure self-healing mechanisms to automatically restart unhealthy container instances.** Automatic restarts increase the reliability and availability of your applications by ensuring quick recovery from failures without manual intervention. This includes setting up health probes (liveness and readiness checks) to detect failing containers and using resiliency policies to automatically handle retries and circuit breakers.
> - **Monitor reliability and overall health indicators of the container app.** Collect logs and metrics to monitor health, identify performance and reliability trends, and troubleshoot problems. Review the Well-Architected [Health modeling for workloads](/azure/well-architected/design-guides/health-modeling) guide for help designing the reliability and health monitoring solution for your workload.
>
> - **Configure health probes.** Set up startup, readiness, and liveness probes for all services to monitor and maintain application health.  
>
> - **Enable monitoring and alerts.** Activate monitoring tools and set up alerts to detect and respond to reliability-impacting events promptly (e.g., Azure Monitor, OpenTelemetry).  

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Enable Container Apps [avaliability zones support](/azure/reliability/reliability-azure-container-apps#availability-zone-support) to automatically distribute replicas across the zones in a region. Traffic is load balanced among the replicas. | In the event of zone failure, traffic is automatically routed to the replicas in the remaining zones. |
| Define [resource quotas](/azure/container-apps/quotas) and limits. | Prevents resource contention, ensuring fair allocation and avoiding performance degradation. Use monitoring over time to observe actual utilization of resources and adjust quotas/limits accordingly.|
| Use [volume mounts](/azure/container-apps/storage-mounts#azure-files) to store data outside of the container in stateful applications. For enhanced data resilience, utilize Azure Zone-Redundant Storage (ZRS) to ensure high availability and durability of your data. | Ensures data persistence and integrity across container restarts and failures. By leveraging ZRS, you can protect against data loss due to zonal failures, providing a robust solution for critical stateful applications. |
| Implement liveness, readiness, and startup [health probes](/azure/container-apps/health-probes?) for your container apps.</br></br>Liveness probes detect and restart containers that are in a failed state.</br>Recommended settings: `failureThreshold: 3`, `periodSeconds: 10`, `timeoutSeconds: 5`, `successThreshold: 1`, and `initialDelaySeconds: 10`</br></br>Readiness probes ensure only healthy containers receive traffic.</br>Recommended settings: `failureThreshold: 60`, `periodSeconds: 1`, `timeoutSeconds: 1`, `successThreshold: 1`, and `initialDelaySeconds: 5`</br></br>Startup probes prevent premature restarts by allowing slow-starting apps to initialize properly.</br>Recommended settings: `failureThreshold: 60`, `periodSeconds: 1`, `timeoutSeconds: 1`, `successThreshold: 1`, and `initialDelaySeconds: 0`| Properly configuring these probes ensures that your container apps are running smoothly and ready to handle traffic. Incorrect probe configurations can lead to unintended restarts or downtime.|  
| Leverage Container Apps' built-in [observability features](/azure/container-apps/log-options) such as log streaming, container console, Azure Monitor metrics, and alerts to ensure proactive monitoring and efficient debugging. | Container Apps provides deep observability support, including integration with the .NET Aspire dashboard and Java metrics, enabling enhanced insights for these key ecosystems. Additionally, leverage the OpenTelemetry (OTel) collector for comprehensive distributed tracing and metrics collection. These features improve application reliability by allowing quick identification and resolution of issues. |
| Implement service discovery [resiliency policies](/azure/container-apps/service-discovery-resiliency) such as retries, timeouts, and circuit breakers to proactively prevent, detect, and recover from service request failures. | This enhances the reliability of your container apps by ensuring smoother and more resilient inter-service communication. |
| Implement horizontal [auto-scaling](/azure/container-apps/scale-app) using scale rules based on HTTP requests, TCP connections, or custom metrics (e.g., CPU, memory, Azure Service Bus, Azure Event Hubs, Apache Kafka, Redis). | Container Apps deployment will be able to dynamically manage loads and maintain high availability during peak usage times.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Container Apps.

Security is a critical aspect of any cloud architecture, and ensuring the protection of your applications and data is paramount. This section provides best practices and recommendations for securing Container Apps. By implementing robust security measures, you can safeguard your applications against threats, ensure compliance with regulatory requirements, and maintain the integrity and confidentiality of your data. Security encompasses a range of practices, including authentication, network isolation, encryption, and identity management, all designed to create a secure and resilient application environment.

### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review security baselines.** To enhance the security posture of your workload, review the [security baseline for Container Apps](/security/benchmark/azure/baselines/azure-container-apps-security-baseline).
> - **Integrate with  Microsoft Entra ID for [identity and access mangement](/azure/well-architected/security/identity-access#the-role-of-an-identity-provider).** Use role-based access control (RBAC) with Microsoft Entra ID for [least privilege access](/azure/aks/azure-ad-rbac).
>
> - **Use managed identities with Microsoft Entra ID** for secure, credential-free access to Azure resources.  
> - **Implement segmentation and network controls.** Deploy private container apps environments and internal ingress mode for isolation from the public internet.  
>
> - **Control egress traffic.** To prevent data exfiltration, integrate your container apps environment into a custom virtual network with user-defined routes to secure outbound traffic.  
>
> - **Maintain a hardened workload's software supply chain.** Implement container-aware scanning in your secure CI/CD pipelines to detect vulnerabilities and ensure the integrity of your container images. Refer to the Container Secure Supply Chain documentation for details on this topic.  
>
> - **Reduce the attack surface.** by hardening container images and removing unused components.Use lean and minimal base images (e.g., Alpine, Ubuntu Chiseled).
>
> - **Integrate with Microsoft Defender.** Use Microsoft Defender for Containers to scan images in ACR.
> - **Encrypt data** using modern industry-standard methods to guard confidentiality and integrity.
>
> - **Use Azure Key Vault.** Securely store sensitive configuration values and secrets in Azure Key Vault to protect them from unauthorized access.  
>
> - **Enable mTLS (Mutual TLS).** Use mutual TLS to authenticate and encrypt traffic between services, enhancing security by ensuring both parties are verified.
>
> - **Enforce HTTPS.** Configure Envoy proxy to redirect all HTTP traffic to HTTPS (default is allowInsecure: false).  
> - **Implement a security monitoring strategy.** Capture detailed logs for monitoring and auditing. Send system and console logs to a log analytics workspace, event hub, or partner solution for monitoring and auditing purposes. Scrub sensitive data from logs as console logs originate from stderr and stdout in the app.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Use [managed identities](/azure/container-apps/managed-identity) to access other Entra ID protected resources. Assigning a managed identity to your container app allows you to access other Entra protected Azure resources, such as a storage account or key vault without having to use keys/passwords. | Centralizes identity management and eliminates the need for manual credential management. Simplifies secure access to Azure resources. |
| Deploy Container Apps in a [private network](/azure/container-apps/networking) to enable into an existing virtual network. Features are available such as using private application connectivity, NSG attachment, and communicating with resources using a private IP. | Provides isolation from the public internet and enables secure communication between container apps and other network resources.|
| Use [Azure Key Vault](/azure/container-apps/key-vault-certificates-manage) to securely manage certificates and app secrets. Certificates and other app secrets are kept separate from your app's configuration, and Key Vault provides an access audit log of when they are updated, retrieved, and rotated. | Protects sensitive information, ensures compliance, and supports secure secret management with logging and certificate rotation capabilities. |
| Use [Application Gateway with WAF](/azure/container-apps/waf-app-gateway) enabled to secure HTTP(S) traffic to publish your container app to its consumers via a reverse proxy. Web application firewall scans incoming HTTP traffic for potential OWASP attacks. | Enhances security by protecting against common web vulnerabilities and providing centralized traffic management. |
| Authenticate with Microsoft Entra ID to [Azure Container Registry](/azure/container-apps/managed-identity-image-pull) to avoid the use of administrative credentials. You can control access via role-based access control. | Ensures secure and credential-free authentication, with granular access control via RBAC for container image management. |
| Use [NSG rules to secure traffic](/azure/container-apps/firewall-integration) accessing your internal container apps endpoint. Using NSG rules allows more granular control of which virtual networks can communicate with your container apps. | Enhances network security by restricting access to only trusted networks, minimizing attack surfaces. |
| Control outbound traffic with [user-defined network routes](/azure/container-apps/user-defined-routes) to control routing of how your container(s) communicate to resources outside of the container environment. You can route traffic to Azure Firewall, NAT gateway, or a 3<sup>rd</sup> party appliance. | Ensures controlled and secure outbound traffic flow, supporting advanced routing and inspection policies. |
| Configure [logging options](/azure/container-apps/log-options) to send logs to a log analytics workspace, event hub, or partner solution.| Supports centralized monitoring, diagnostics, and auditing while ensuring compliance with sensitive data handling policies.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to [Azure offering] and its environment.

With Container Apps, cost optimization can be achieved by carefully selecting and configuring resources to avoid unnecessary expenses while maintaining the performance and reliability of your applications.

### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.'

> [!div class="checklist"]
>
> - **Select appropriate pricing plans.** Choose the most cost-effective pricing plans for your container apps based on workload requirements and expected usage patterns.
>
>   Take advantage of the Azure savings plan for compute for Container Apps by committing to a fixed hourly spend for 1 or 3 years. With a savings plan you can achieve cost savings of up to 17% compared to pay-as-you-go pricing, optimizing your budget and reducing overall expenses for long-term, predictable workloads.
>
> - **Optimize workload components costs.** Regularly review and adjust CPU and memory allocations to match the needs of your applications, avoiding over-provisioning and minimizing costs.  
>
> - **Use managed disk tiers.** When using volume mounts, select appropriate managed disk tiers and sizes to optimize storage costs for persistent data, ensuring you only pay for what you need.  
>
> - **Use streamlined and optimized container images.** Use streamlined and optimized container images to reduce storage and network costs, improving startup times and resource efficiency.  
> - **Optimize scaling costs.** Configure auto-scaling policies to automatically scale in resources during low-demand periods and scale out during peak times, ensuring efficient resource usage.  
> - **Optimize networking costs.** Optimize network paths to minimize data transfer costs, especially for applications with high bandwidth volumes.  
> - **Leverage cost management tools.** Utilize Azure Cost Management and Billing tools to track and analyze spending, set budgets, create cost alerts, and implement consistent tagging across all resources. 
>
>    These tools provide detailed visibility into cloud spending, helps identify cost-saving opportunities, ensures adherence to budgetary constraints, and allows for granular tracking and reporting of costs associated with specific workloads, applications, and environments.  

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Regularly review and adjust CPU, memory allocations, and other [metrics](/azure/container-apps/metrics#available-metrics) to align with the actual needs of your applications.| Prevents over-provisioning and reduces unnecessary costs by ensuring resources are right-sized for workloads. |
| Implement scale-to-zero [auto-scaling](/azure/container-apps/scale-app) rules for apps that don't need to be on 24/7.| Eliminates costs during periods of inactivity, ensuring you only pay for resources when needed. This approach significantly reduces expenses for apps with variable or infrequent usage patterns. |
| Choose the appropriate [managed disk tiers](/azure/container-apps/storage-mounts) for stateful applications. Base your selection on storage performance and capacity needs, and consider using reserved disks for predictable workloads.| Ensures you only pay for the necessary storage performance, avoiding excess costs from over-provisioned storage. Reserved disks can provide significant cost savings for long-term storage requirements by offering discounts compared to pay-as-you-go pricing.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

By implementing these strategies, you can streamline operations, automate routine tasks, and enhance the overall stability and reliability of your applications. Operational excellence involves continuous improvement, proactive monitoring, and leveraging automation to reduce manual intervention, ultimately leading to a more robust and resilient application environment.

#### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Container Apps.

> [!div class="checklist"]
>
> - **Implement an infrastructure as code (IaC) deployment approach.** Implement template-based deployments using tools like Bicep or Terraform. Ensure all deployments are repeatable, traceable, and stored in a source code repository.
>
> - **Automate infrastructure and workload deployments.** Use standard software solutions to manage, integrate, and automate the deployment of your workloads.
>
>   Ensure that deployment pipelines are set up to redeploy the environment to another region in case of a regional outage. This approach ensures critical data and configurations can be quickly restored and redeployed in another region, enhancing disaster recovery capabilities and minimizing downtime during regional failures.
>
>   Build automated processes to bootstrap your environments with necessary configurations and deployments using CI/CD pipelines.
> - **Implement a comprehensive monitoring strategy.** Configure diagnostics settings to capture logs, metrics, and diagnostics data. Use tools like Azure Monitor and Application Insights to track application health and performance,  and identify trends in performance and reliability, and troubleshoot problems.
>
> - **Emit telemetry for workloads.** Design your workloads to emit telemetry data, including liveliness and readiness statuses, to facilitate monitoring and troubleshooting.  
>
> - **Monitor performance metrics.** Continuously monitor key performance metrics such as CPU, memory, and network usage to identify opportunities for cost optimization and operational efficiencies.
> - **Implement chaos engineering.** Apply chaos engineering practices using tools such as Azure Chaos Studio to identify potential reliability issues within your Container Apps environment. Conduct experiments to ensure your applications can withstand unexpected failures. Conduct performance testing using tools such as Azure Load Testing to ensure scaling rules work as expected without disruptions to clients.  
> - **Implement consistent resource tagging** across all Container Apps and other workload resources. Consistent tagging facilitates efficient resource management, cost tracking, and automation.
> - **Enforce workload governance.** Azure Policy helps ensure consistent compliance with organizational standards, automates policy enforcement, and provides centralized visibility and control over your workload's resources.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Store your Container Apps environment configurations as [infrastructure as code (IaC)](/azure/templates/microsoft.app/containerapps#bicep-resource-definition) and ensure that deployment pipelines are set up to redeploy the environment to another region in case of a regional outage. | This approach ensures critical data and configurations can be quickly restored and redeployed in another region, enhancing disaster recovery capabilities and minimizing downtime during regional failures. |  
| Use [revisions](/azure/container-apps/revisions-manage) to implement blue-green or canary deployments.</br></br>This requires the use of [tagging and versioning container images appropriately](/azure/container-registry/container-registry-image-tag-version). Labels can be used on revisions to facilitate sharing of revisions, e.g., for UAT or limited previews. | Minimizes downtime and reduces risk during releases by enabling safe rollouts and quick rollbacks.|
| [Configure Azure Monitor](/azure/container-apps/log-options) and Application Insights. | To track the performance and health of your container apps provide detailed insights into application performance and reliability, allowing for proactive issue detection and resolution. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

By leveraging the right tools and techniques, you can enhance the responsiveness, throughput, and overall efficiency of your applications. Performance efficiency involves continuous monitoring, scaling strategies, and resource optimization to ensure that your applications deliver a high-quality experience to users, even during peak usage times.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for Container Apps.

> [!div class="checklist"]
>
> - **Develop a detailed capacity plan** to ensure your container apps have sufficient resources to handle varying loads while avoiding over-provisioning, leading to cost savings and optimal performance.
>
>    Regularly update your detailed capacity planning to document the correct resource allocations, auto scale settings, and failover strategies for your container apps. This approach ensures critical data and configurations can be quickly restored and redeployed in another region, enhancing disaster recovery capabilities and minimizing downtime during regional failures.
> - **Enable auto-scaling.** Configure auto-scaling policies to automatically adjust the number of container instances based on real-time demand, ensuring optimal performance during peak and off-peak times.  
> - **Optimize resource allocation.** Continuously monitor and adjust CPU and memory allocations based on performance metrics to ensure efficient resource utilization and prevent over-provisioning.  
> - **Conduct load testing.** Perform regular load testing to evaluate the performance and scalability of your container apps under different conditions, ensuring they can handle expected traffic levels.  
> - **Separate workloads.** Deploy critical and sensitive workloads in separate Container Apps environments to avoid noisy neighbour issues. By carving out workloads across multiple environments, you can ensure that critical applications have dedicated resources and are not impacted by the performance demands of less critical applications.  

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Configure [auto-scaling](/azure/container-apps/scale-app) policies to automatically adjust the number of container instances in response to resource demands. | Dynamically scaling resources up or down as needed helps maintain application performance and cost-efficiency, ensuring that resources are available when needed and conserved when not. Verify with load testing experiments (e.g., Azure Load Testing) and adjust auto-scaling policies as needed. |
| Use the [Workload Profiles](/azure/container-apps/workload-profiles-overview) dedicated tier for applications that require predictable performance and guaranteed resource allocation. | Provides dedicated resources for critical applications, ensuring consistent performance and reducing the risk of resource contention. |
| Use custom scaling [metrics](/azure/container-apps/metrics#available-metrics), such as those based on application-specific data, to drive auto-scaling decisions. | Ensures scaling actions are based on relevant workload demands, improving the efficiency and responsiveness of your container apps. |

## Azure policies

Azure provides an extensive set of built-in policies related to Container Apps and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- **Diagnostics settings should be enabled:** Enable logging by category group for Container Apps environments (`microsoft.app/managedenvironments`) to Storage. This ensures that logs and metrics are consistently collected for monitoring, troubleshooting, and compliance.

- **Authentication for Container Apps should be enabled:** Ensure authentication is enabled to prevent anonymous HTTP requests and authenticate requests with tokens before they reach the Container App.

- **Container App should use network injection:** Configure Container Apps environments with virtual network injection to isolate them from the public internet, enable network integration with resources on-premises or other Azure virtual networks, and achieve granular control over network traffic.

- **Public network access should be disabled:** Disable public network access to improve security by exposing the Container Apps environment through an internal load balancer, preventing internet access to all Container Apps within the environment.

- **External network access should be disabled:** Enforce internal-only ingress to ensure inbound communication for Container Apps is limited to callers within the Container Apps environment.

- **HTTPS should be enforced:** Ensure Container Apps are only accessible over HTTPS to protect data in transit from network layer eavesdropping attacks.

- **Managed identities should be enabled:** Require managed identities for Container Apps to securely authenticate to any resource that supports Azure AD authentication.

For comprehensive governance, review the [Azure Policy built-in definitions for Container Apps](/azure/container-apps/policy-reference) and other policies that might affect the security of the networks.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. 

For more information, see [Azure Advisor](/azure/advisor).

## Next steps

- Deploy your first container app using the Azure CLI [Quickstart: Deploy your first container app with containerapp up](/azure/container-apps/get-started?tabs=bash)

### Azure Architecture Center guidance

- [Building resilient and reliable Azure applications](/azure/architecture/framework/resiliency/overview)
- [Error handling for resilient cloud applications](/azure/architecture/framework/resiliency/app-design-error-handling)
- [Cloud application resiliency patterns](/azure/architecture/patterns/category/resiliency)
- [Cloud application availability patterns](/azure/architecture/patterns/category/availability)
- [What is a mission-critical workload?](/azure/well-architected/mission-critical/mission-critical-overview)
- [Site reliability engineering resources](/azure/site-reliability-engineering/)
- [Container Apps overview](/azure/container-apps/overview)

### Cloud Adoption Framework guidance

- [Container Apps Landing Zone Accelerator](/azure/cloud-adoption-framework/scenarios/app-platform/container-apps/landing-zone-accelerator)


