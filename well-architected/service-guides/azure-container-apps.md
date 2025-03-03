---
title: Azure Well-Architected Framework review for Azure Container Apps (ACA)
description: Provides a template for a Well-Architected Framework (WAF) article that is specific to Azure Container Apps (ACA).
author: igorjnzl
ms.author: igorjovovic
ms.topic: conceptual
ms.date: 01/16/2024
ms.product: azure-container-apps
azure.category:
  - containers
---

# Well-Architected Framework perspective on Azure Container Apps

This article provides architectural best practices for Azure Container Apps (ACA). The guidance is based on the five pillars of architecture excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency


We assume you've reviewed the [Compute Decision Tree](/azure/architecture/guide/technology-choices/compute-decision-tree) and chose Container Apps as the compute platform for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

## Prerequisites

Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.

Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope.

Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for Azure Container Apps and its dependencies. Instead, they list the key recommendations mapped to the design perspectives.

Foundational architecture that demonstrates the key recommendations: [Baseline highly available, zone-redundant web application architecture](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).

## Reliability

In the cloud, failures can happen. Instead of trying to prevent failures entirely, the goal is to architect for resiliency, ensuring that your application can continue to function and recover quickly. Reliability focuses on ensuring your application can recover from failures and continue to operate effectively. This section outlines best practices and design considerations to enhance the reliability of Azure Container Apps, ensuring high availability and fault tolerance in your deployments.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

In the **design checklist** and **list of recommendations** below, callouts are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
> - **Use availability zones.** Deploy production and critical workloads across multiple availability zones to ensure resilience and minimize the impact of zone failures.  
> - **Implement horizontal auto-scaling.** Configure auto-scaling using scale rules based on HTTP requests, TCP connections, or custom metrics (e.g., CPU, memory, Azure Service Bus, Azure Event Hubs, Apache Kafka, Redis) to dynamically manage loads and maintain high availability during peak usage times.
> - **High Availability.** For ingress-exposed (HTTP or TCP) apps, use at least three replicas to ensure availability. To minimize cold starts, configure a minimum number of always-ready replicas.
> - **Configure health probes.** Set up startup, readiness, and liveness probes for all services to monitor and maintain application health.  
> - **Enable monitoring and alerts.** Activate monitoring tools and set up alerts to detect and respond to reliability-impacting events promptly (e.g., Azure Monitor, OpenTelemetry).  
> - **Choose appropriate sku configurations.** Select an environment SKU that aligns with the resource and performance requirements of your container apps.
> - **Horizontal Auto-Scaling.** Ensure your container apps can still serve requests within your SLO under high-load.

### Recommendations

| Recommendation | Benefit |
|--------|----|
| **Enable Container Apps zone redundancy feature.** Replicas are automatically distributed across the zones in the region; traffic is load balanced among the replicas. | In the event of zone failure, traffic is automatically routed to the replicas in the remaining zones. |
| **Implement resilient recovery with cross-region deployment.** Deploy Azure Container Apps across multiple regions and utilize Azure Front Door or Azure Traffic Manager for traffic management to ensure high availability and business continuity. | In the event of a regional outage, traffic can be automatically redirected to a secondary region, minimizing downtime and data loss. This proactive approach to disaster recovery enhances the resilience of your applications, providing a seamless user experience even during high-impact events. |
| **Store your Azure Container Apps environment configurations as infrastructure as code (IaC).** Ensure that deployment pipelines are set up to redeploy the environment to another region in case of a regional outage. | This approach ensures critical data and configurations can be quickly restored and redeployed in another region, enhancing disaster recovery capabilities and minimizing downtime during regional failures. |
| **Resource quotas.** Define resource quotas and limits. | Prevents resource contention, ensuring fair allocation and avoiding performance degradation. Use monitoring over time to observe actual utilization of resources and adjust quotas/limits accordingly. |
| **Deployment strategies.** Use revisions for blue-green or canary deployments. | Minimizes downtime and reduces risk during releases by enabling safe rollouts and quick rollbacks. Labels can be used on revisions to facilitate sharing of revisions, e.g., for UAT or limited previews. This requires the use of [tagging and versioning container images appropriately](/azure/container-registry/container-registry-image-tag-version)|
| **Use volume mounts for stateful applications to store data outside of the container.** For enhanced data resilience, utilize Azure Zone-Redundant Storage (ZRS) to ensure high availability and durability of your data. | Ensures data persistence and integrity across container restarts and failures. By leveraging ZRS, you can protect against data loss due to zonal failures, providing a robust solution for critical stateful applications. |
| **Implement liveness, readiness, and startup probes for your container apps.** Properly configuring these probes ensures that your container apps are running smoothly and ready to handle traffic. Incorrect probe configurations can lead to unintended restarts or downtime, so it's important to follow best practices. |  
| **Liveness probes.** detect and restart containers that are in a failed state. Recommended settings: `failureThreshold: 3`, `periodSeconds: 10`, `timeoutSeconds: 5`, `successThreshold: 1`, and `initialDelaySeconds: 10` (adjust as needed per app).  |
| **Readiness probes.** ensure only healthy containers receive traffic. Recommended settings: `failureThreshold: 60`, `periodSeconds: 1`, `timeoutSeconds: 1`, `successThreshold: 1`, and `initialDelaySeconds: 5` (adjust per app).  |
| **Startup probes.** prevent premature restarts by allowing slow-starting apps to initialize properly. Recommended settings: `failureThreshold: 60`, `periodSeconds: 1`, `timeoutSeconds: 1`, `successThreshold: 1`, and `initialDelaySeconds: 0` (adjust per app).  |
| **Leverage Azure Container Apps' built-in observability features.** Use built-in observability tools such as log streaming, container console, Azure Monitor metrics, and alerts to ensure proactive monitoring and efficient debugging. | Azure Container Apps provides deep observability support, including integration with the .NET Aspire dashboard and Java metrics, enabling enhanced insights for these key ecosystems. Additionally, leverage the OpenTelemetry (OTel) collector for comprehensive distributed tracing and metrics collection. These features improve application reliability by allowing quick identification and resolution of issues. |
| **Implement service discovery resiliency policies.** Configure resiliency policies such as retries, timeouts, and circuit breakers to proactively prevent, detect, and recover from service request failures. | This enhances the reliability of your container apps by ensuring smoother and more resilient inter-service communication. |
| **Implement horizontal auto-scaling.** Configure auto-scaling using scale rules based on HTTP requests, TCP connections, or custom metrics (e.g., CPU, memory, Azure Service Bus, Azure Event Hubs, Apache Kafka, Redis). | Container Apps deployment will be able to dynamically manage loads and maintain high availability during peak usage times.

For more suggestions, see [Principles of the reliability pillar](/azure/well-architected/resiliency/principles).

## Security

Security is a critical aspect of any cloud architecture, and ensuring the protection of your applications and data is paramount. This section provides best practices and recommendations for securing Azure Container Apps. By implementing robust security measures, you can safeguard your applications against threats, ensure compliance with regulatory requirements, and maintain the integrity and confidentiality of your data. Security encompasses a range of practices, including authentication, network isolation, encryption, and identity management, all designed to create a secure and resilient application environment.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around hosting on Container Apps.

### Design checklist

> [!div class="checklist"]
> - **Use managed identities with Microsoft Entra ID.** Ensure container apps use managed identities for secure, credential-free access to Azure resources.  
> - **Deploy private container apps environments.** Use private environments and internal ingress mode for isolation from the public internet.  
> - **Use Azure Key Vault.** Securely store sensitive configuration values and secrets in Azure Key Vault to protect them from unauthorized access.  
> - **Secure CI/CD pipelines.** Implement container-aware scanning in your CI/CD pipelines to detect vulnerabilities and ensure the integrity of your container images. Refer to the Container Secure Supply Chain documentation for details on this topic.  
> - **Enable mTLS (Mutual TLS).** Use mutual TLS to authenticate and encrypt traffic between services, enhancing security by ensuring both parties are verified.
> - **Control egress traffic.** Integrate your container apps environment into a custom virtual network with user-defined routes to secure outbound traffic.  
> - **Enforce HTTPS.** Configure Envoy proxy to redirect all HTTP traffic to HTTPS (default is allowInsecure: false).  
> - **Enable logging options.** Capture detailed logs for monitoring and auditing.  
> - **Use lean base images.** Reduce the attack surface by using minimal base images (e.g., Alpine, Ubuntu Chiseled).  
> - **Secure container images.** Consider enabling Microsoft Defender for Containers to scan images in ACR for vulnerabilities.

### Recommendations

Explore the following table of recommendations to optimize your Azure Container Apps configuration for security.

| Recommendation | Benefit |
|----------------|---------|
| **Use managed identities from Entra ID to access other Entra ID protected resources.** Assigning a managed identity to your container app allows you to access other Entra protected Azure resources, such as a storage account or key vault without having to use keys/passwords. | Centralizes identity management and eliminates the need for manual credential management. Simplifies secure access to Azure resources. |
| **Deploy Container Apps with an internal environment.** Allows integration into an existing virtual network. Features are available such as using private application connectivity, NSG attachment, and communicating with resources using a private IP. | Provides isolation from the public internet and enables secure communication between container apps and other network resources. |
| **Use Azure Key Vault to securely store sensitive configuration and app settings.** Secrets are kept separate from your app's configuration, and Key Vault provides an access audit log of when they are updated, retrieved, and rotated. | Protects sensitive information, ensures compliance, and supports secure secret management with logging and rotation capabilities. |
| **Use Application Gateway with WAF enabled to secure HTTP(S) traffic.** Allows a reverse proxy to publish your container app to its consumers. Web application firewall scans incoming HTTP traffic for potential OWASP attacks. | Enhances security by protecting against common web vulnerabilities and providing centralized traffic management. |
| **Authenticate with Microsoft Entra ID to Azure Container Registry.** Using Managed Identities for authentication avoids the use of administrative credentials. You can control access via role-based access control. | Ensures secure and credential-free authentication, with granular access control via RBAC for container image management. |
| **Use NSG rules to secure traffic accessing your internal container apps endpoint.** Using NSG rules allows more granular control of which virtual networks can communicate with your container apps. | Enhances network security by restricting access to only trusted networks, minimizing attack surfaces. |
| **Control outbound traffic with user-defined network routes.** Provides the ability to control routing of how your container(s) communicate to resources outside of the container environment. You can route traffic to Azure Firewall, NAT gateway, or a 3<sup>rd</sup> party appliance. | Ensures controlled and secure outbound traffic flow, supporting advanced routing and inspection policies. |
| **Configure logging options to send logs to a log analytics workspace, event hub, or partner solution.** Sending system and console logs to available destinations allows capturing of logs for monitoring and auditing purposes. Scrub sensitive data from logs as console logs originate from stderr and stdout in the app. | Supports centralized monitoring, diagnostics, and auditing while ensuring compliance with sensitive data handling policies. |

For more suggestions, see [Principles of the security pillar](/azure/well-architected/security/security-principles). Also refer to the [Azure Security Baseline For Container Apps](/security/benchmark/azure/baselines/azure-container-apps-security-baseline)

## Cost Optimization

Cost optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements. With Azure Container Apps, cost optimization can be achieved by carefully selecting and configuring resources to avoid unnecessary expenses while maintaining the performance and reliability of your applications. This section provides best practices and recommendations to help you optimize costs effectively. By understanding and implementing these strategies, you can ensure that your containerized applications are both cost-effective and scalable, allowing you to focus resources on innovation and growth.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals.  Furthermore, go to the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and select **Azure Container Apps** from the available products. You can test different configuration and payment plans in the calculator.

### Design checklist

> [!div class="checklist"]
> - **Select appropriate pricing plans.** Choose the most cost-effective pricing plans for your container apps based on workload requirements and expected usage patterns. [Refer to plan decision tree](#).  
> - **Optimize resource allocation.** Regularly review and adjust CPU and memory allocations to match the needs of your applications, avoiding over-provisioning and minimizing costs.  
> - **Enable auto-scaling.** Configure auto-scaling policies to automatically scale in resources during low-demand periods and scale out during peak times, ensuring efficient resource usage.  
> - **Use managed disk tiers.** When using volume mounts, select appropriate managed disk tiers and sizes to optimize storage costs for persistent data, ensuring you only pay for what you need.  
> - **Optimize image sizes.** Use streamlined and optimized container images to reduce storage and network costs, improving startup times and resource efficiency.  
> - **Review networking costs.** Optimize network paths to minimize data transfer costs, especially for applications with high bandwidth volumes.  
> - **Leverage cost management tools.** Utilize Azure Cost Management and Billing tools to track and analyze spending, set budgets, and create cost alerts to stay within budget and identify cost-saving opportunities.  

### Recommendations

Explore the following table of recommendations to optimize your Azure Container Apps configuration for cost.

| Recommendation | Benefit |
|----------------|---------|
| **Regularly review and adjust CPU and memory allocations to align with the actual needs of your applications.** | Prevents over-provisioning and reduces unnecessary costs by ensuring resources are right-sized for workloads. |
| **Implement auto-scaling policies, specifically scale-to-zero where apps don't need to be on 24/7.** | Eliminates costs during periods of inactivity, ensuring you only pay for resources when needed. This approach significantly reduces expenses for apps with variable or infrequent usage patterns. |
| **Use streamlined and optimized container images.** | Reduces storage and network costs, and improves startup times, leading to better resource utilization and cost savings. |
| **Use Azure Cost Management and billing tools to monitor spending, set budgets, create cost alerts, and implement consistent tagging across all resources.** | Provides detailed visibility into cloud spending, helps identify cost-saving opportunities, ensures adherence to budgetary constraints, and allows for granular tracking and reporting of costs associated with specific workloads, applications, and environments. |
| **For stateful applications, choose the appropriate managed disk tiers based on storage performance and capacity needs, and consider using reserved disks for predictable workloads.** | Ensures you only pay for the necessary storage performance, avoiding excess costs from over-provisioned storage. Reserved disks can provide significant cost savings for long-term storage requirements by offering discounts compared to pay-as-you-go pricing. |
| **Take advantage of the Azure savings plan for compute for Azure Container Apps by committing to a fixed hourly spend for 1 or 3 years.** | Achieves cost savings of up to 17% compared to pay-as-you-go pricing, optimizing your budget and reducing overall expenses for long-term, predictable workloads. |

### Cloud efficiency

Making workloads more [sustainable and cloud efficient](../sustainability/sustainability-get-started.md), requires combining efforts around **cost optimization**, **reducing carbon emissions**, and **optimizing energy consumption**. Optimizing the application's cost is the initial step in making workloads more sustainable.

## Operational Excellence

Operational excellence focuses on maintaining efficient operations, minimizing downtime, and ensuring that your applications are reliable and performant. This section provides best practices and recommendations for managing Azure Container Apps effectively. By implementing these strategies, you can streamline operations, automate routine tasks, and enhance the overall stability and reliability of your applications. Operational excellence involves continuous improvement, proactive monitoring, and leveraging automation to reduce manual intervention, ultimately leading to a more robust and resilient application environment.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

#### Design checklist

> [!div class="checklist"]
> - **Use automation and IaC.** Implement template-based deployments using tools like Bicep or Terraform. Ensure all deployments are repeatable, traceable, and stored in a source code repository.  
> - **Automate app deployments.** Build automated processes to bootstrap your environments with necessary configurations and deployments using CI/CD pipelines.  
> - **Enable diagnostics and logging.** Configure diagnostics settings to capture logs, metrics, and diagnostics data. Use tools like Azure Monitor and Application Insights to track application health and performance.  
> - **Emit telemetry for workloads.** Design your workloads to emit telemetry data, including liveliness and readiness statuses, to facilitate monitoring and troubleshooting.  
> - **Implement chaos engineering.** Apply chaos engineering practices using tools such as Azure Chaos Studio to identify potential reliability issues within your Azure Container Apps environment. Conduct experiments to ensure your applications can withstand unexpected failures. Conduct performance testing using tools such as Azure Load Testing to ensure scaling rules work as expected without disruptions to clients.  
> - **Enforce governance using Azure Policy.** Use Azure Policy to enforce governance across your environments, ensuring compliance with organizational standards and best practices.  
> - **Monitor performance metrics.** Continuously monitor key performance metrics such as CPU, memory, and network usage to identify opportunities for cost optimization and operational efficiencies.

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| **Implement consistent resource tagging across all Container Apps and associated resources.** | Facilitates efficient resource management, cost tracking, and automation. |
| **Configure Azure Monitor and Application Insights.** | To track the performance and health of your container apps provide detailed insights into application performance and reliability, allowing for proactive issue detection and resolution. |
| **Deploy Container Apps across multiple Azure regions.** | To maximize availability and ensure business continuity. | Enhances resilience and provides disaster recovery capabilities by distributing workloads globally. |
| **Use Azure Policy**  | To enforce governance and compliance across your container apps environments. | Ensures adherence to organizational standards and best practices, enhancing security and reliability. |
| **Configure self-healing mechanisms.** | To automatically restart unhealthy container instances. | Increases the reliability and availability of your applications by ensuring quick recovery from failures without manual intervention. This includes setting up health probes (liveness and readiness checks) to detect failing containers and using Azure Container Apps' new resiliency policies to automatically handle retries and circuit breakers. By implementing these self-healing mechanisms, you can reduce downtime and maintain smooth operation of your applications even in the face of unexpected issues. |

## Performance Efficiency

Performance efficiency focuses on ensuring your applications can scale to meet demand and perform optimally under varying loads. This section provides best practices and recommendations for optimizing the performance of Azure Container Apps. By leveraging the right tools and techniques, you can enhance the responsiveness, throughput, and overall efficiency of your applications. Performance efficiency involves continuous monitoring, scaling strategies, and resource optimization to ensure that your applications deliver a high-quality experience to users, even during peak usage times.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

> [!div class="checklist"]
> - **Perform capacity planning.** Regularly conduct detailed capacity planning exercises to determine the appropriate resource allocations, auto scale settings, and failover strategies for your container apps.  
> - **Enable auto-scaling.** Configure auto-scaling policies to automatically adjust the number of container instances based on real-time demand, ensuring optimal performance during peak and off-peak times.  
> - **Optimize resource allocation.** Continuously monitor and adjust CPU and memory allocations based on performance metrics to ensure efficient resource utilization and prevent over-provisioning.  
> - **Conduct load testing.** Perform regular load testing to evaluate the performance and scalability of your container apps under different conditions, ensuring they can handle expected traffic levels.  
> - **Separate workloads.** Deploy critical and sensitive workloads in separate Azure Container Apps environments to avoid noisy neighbour issues. By carving out workloads across multiple environments, you can ensure that critical applications have dedicated resources and are not impacted by the performance demands of less critical applications.  

### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| **Develop a detailed capacity plan and continually review and revise it based on observed resource utilization.** | Ensures your container apps have sufficient resources to handle varying loads while avoiding over-provisioning, leading to cost savings and optimal performance. |
| **Configure auto-scaling policies to automatically adjust the number of container instances in response to resource demands.** | Dynamically scaling resources up or down as needed helps maintain application performance and cost-efficiency, ensuring that resources are available when needed and conserved when not. Verify with load testing experiments (e.g., Azure Load Testing) and adjust auto-scaling policies as needed. |
| **Use the Workload Profiles Dedicated Tier for applications that require predictable performance and guaranteed resource allocation.** | Provides dedicated resources for critical applications, ensuring consistent performance and reducing the risk of resource contention. |
| **Use custom scaling metrics, such as those based on application-specific data, to drive auto-scaling decisions.** | Ensures scaling actions are based on relevant workload demands, improving the efficiency and responsiveness of your container apps. |

## Azure policies

Azure provides a comprehensive set of built-in policies for Container Apps and their dependencies. These policies can audit and enforce best practices to enhance the security, performance, and manageability of your environments. For example, you can check whether:

- **[Ensure volume mounts are configured](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDefinitionBlade/definitionId/VolumeMountPolicyExampleID)**: In cases where persistent storage is required, Container Apps environments should be configured with volume mounts to ensure data persistence and integrity. This protects critical application data across restarts and enables stateful application scenarios.

- **[Enable diagnostics settings](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDefinitionBlade/definitionId/DiagnosticsSettingsPolicyExampleID)**: Enable logging by category group for Container Apps environments (`microsoft.app/managedenvironments`) to Storage. This ensures that logs and metrics are consistently collected for monitoring, troubleshooting, and compliance.

- **[Enable authentication for Container Apps](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDefinitionBlade/definitionId/AuthenticationPolicyExampleID)**: Ensure authentication is enabled to prevent anonymous HTTP requests and authenticate requests with tokens before they reach the Container App.

- **[Container App should use network injection](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDefinitionBlade/definitionId/NetworkInjectionPolicyExampleID)**: Configure Container Apps environments with virtual network injection to isolate them from the public internet, enable network integration with resources on-premises or other Azure virtual networks, and achieve granular control over network traffic.

- **[Disable public network access](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDefinitionBlade/definitionId/PublicNetworkAccessPolicyExampleID)**: Disable public network access to improve security by exposing the Container Apps environment through an internal load balancer, preventing internet access to all Container Apps within the environment.

- **[Disable external network access](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDefinitionBlade/definitionId/InternalOnlyIngressPolicyExampleID)**: Enforce internal-only ingress to ensure inbound communication for Container Apps is limited to callers within the Container Apps environment.

- **[Enforce HTTPS](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDefinitionBlade/definitionId/EnforceHTTPSPolicyExampleID)**: Ensure Container Apps are only accessible over HTTPS to protect data in transit from network layer eavesdropping attacks.

- **[Managed identity should be enabled](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDefinitionBlade/definitionId/ManagedIdentityPolicyExampleID)**: Require managed identity for Container Apps to securely authenticate to any resource that supports Azure AD authentication.

- **[Enforce tagging for cost management](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDefinitionBlade/definitionId/TaggingPolicyExampleID)**: Require tags on all resources (e.g., environment, owner, cost-center) to ensure detailed cost tracking and management.

- **[Enable diagnostics and monitoring](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDefinitionBlade/definitionId/DiagnosticsAndMonitoringPolicyExampleID)**: Enforce the configuration of diagnostic settings to ensure logs and metrics are consistently collected and monitored, providing visibility into application performance and health.

For comprehensive governance, review the [Azure Policy built-in definitions](/azure/container-apps/policy-reference) and other policies that might affect networking.

## Additional resources

### Azure Architecture Center guidance

- [Building Resilient and Reliable Azure Applications](/azure/architecture/framework/resiliency/overview)
- [Error Handling for Resilient Cloud Applications](/azure/architecture/framework/resiliency/app-design-error-handling)
- [Cloud Application Resiliency Patterns](/azure/architecture/patterns/category/resiliency)
- [Cloud Application Availability Patterns](/azure/architecture/patterns/category/availability)
- [What is a mission-critical workload?](/azure/well-architected/mission-critical/mission-critical-overview)
- [Site Reliability Engineering Resources](/azure/site-reliability-engineering/)
- [Azure Container Apps overview | Microsoft Learn](/azure/container-apps/overview)

### Cloud Adoption Framework guidance

- [Azure Container Apps Landing Zone Accelerator](/azure/cloud-adoption-framework/scenarios/app-platform/container-apps/landing-zone-accelerator)

## Next steps

- Deploy your first container app using the Azure CLI [Quickstart: Deploy your first container app with containerapp up](/azure/container-apps/get-started?tabs=bash)