---
title: Azure Well-Architected Framework perspective on App Service (Web Apps)
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure App Service (Web Apps).
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/25/2024
ms.topic: conceptual
ms. service: waf
ms. subservice: waf-service-guide
products:
  - azure-app-service
categories:
  - compute
---

# Azure Well-Architected Framework perspective on App Service (Web Apps)

Azure App Service is a platform as a service (PaaS) compute service that you can use to host your workload on the Azure platform. It's a fully managed service that abstracts the underlying compute and offloads the responsibility of building, deploying, and scaling to the platform. An app service always runs in an App Service plan. The service plan you choose determines the region in which the workload runs, the compute configurations, and the operating system. There are multiple billing models available for App Service.

This article assumes that as an architect, you've reviewed the [compute decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree) and chosen App Service as the compute for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a _design checklist_ that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are _recommendations_ on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for App Service for Web Apps and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments.
>
> Foundational architecture that demonstrates the key recommendations: [App Service baseline architecture](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).

##### Technology scope

This review focuses on the interrelated decisions for the following Azure resources:

- App Service plan
- App Service for Web Apps

There are other Azure offerings associated with App Service, such as Azure Functions and Azure Logic Apps. Those offerings are out of scope for this article. App Service Environments (ASE) is also out of scope. ASE is referenced occasionally to help clarify features or options of the core App Service offerings.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

##### Design checklist

Start your design strategy based on the [**design review checklist for Reliability**](../reliability/checklist.md) and determine its relevance to your business requirements while keeping in mind the tiers and features of App Service and its dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]

> - **Prioritize user flows**: Not all flows are equally critical. Assign priorities to each flow to guide your design decisions. User flow design will influence the choice of service tiers and instances on the App Service plan and configurations.
>
>     For example, your application might include frontend and backend tiers that communicate through a message broker. You might choose to segment them in multiple Web Apps to allow for independent scaling, lifecycle management, and maintenance. Placing a large application in a single plan can lead to memory or CPU problems and affect reliability.
>
>   You might need additional instances on the frontend for optimal performance on the UI side. However, the backend might not require the same number of instances.
>
> - **Anticipate potential failures**: Plan mitigation strategies for potential failures. Here are some examples of failure mode analysis:
>
>   |Failure|Mitigation|
>   |---|---|
>   |Failure of underlying or abstracted components of App Service.| Have component redundancy in instances and dependencies. Monitor the health of instances, network, and storage performance.|
>   |Failure of external dependencies. | Use design patterns such as retry mechanisms, circuit breakers, and others. Monitor those dependencies and set appropriate timeouts. |
>   |Failure due to traffic getting routed to unhealthy instances.| Monitor instance health. Consider responsiveness, and avoid sending requests to unhealthy instances. |  
>
>   For more information, see [Failure mode analysis for Azure applications](/azure/architecture/resiliency/failure-mode-analysis).
>
> - **Build redundancy**: Build redundancy in the application and supporting infrastructure. Spread instances across availability zones to improve fault tolerance. Traffic can be routed to other zones if one zone fails. Deploy your application across multiple regions to ensure that your app remains accessible, even if an entire region experiences an outage.
>
>   Build similar levels of redundancy in dependent services. For instance, the application instances bind to blob storage. Consider configuring the associated storage account with ZRS if the application uses zone-redundant deployment (ZRS).
>
>   Have redundancy in networking components. For example, use zone-redundant IP addresses and load balancers.
>
> - **Have a reliable scaling strategy**: Unexpected load on the application can cause it to become unreliable. Consider the right scaling approach based on your workload characteristics. You might be able to handle the load by scaling up. However, if the load continues to increase, scale out to new instances. Prefer automatic scaling over manual approaches. Always maintain a buffer of extra capacity during scaling operations to prevent performance degradation.  
>
>   The choice of [App Service plan tier](/azure/app-service/overview-hosting-plans#how-does-my-app-run-and-scale) affects scaling in terms of number of instances and the compute units.

>   Ensure proper app initialization so that new instances are warmed up quickly and can receive requests.
>
>   Strive for stateless applications whenever possible. Reliably scaling state with new instances can increase complexity. Consider an external data store that can be scaled independently if you need to store application state. Storing session state in memory can result in losing session state when there's a problem with the application or App Service. It also limits the possibility of spreading the load across other instances.
>
>   Regularly test your autoscaling rules. Simulate load scenarios to verify that your app scales as expected. Log scaling events so that you can troubleshoot problems that might arise and optimize your scaling strategy over time.
>
>   App Service has a limitation on the number of instances within a plan, which can affect scaling reliability. One strategy is to use identical deployment stamps, each running App Service plan instance with its own endpoint. It's essential that you front all stamps with an external load balancer to distribute traffic across them. Use Azure Application Gateway for single node deployments and Azure Front Door for multi-regional deployments. This approach is ideal for mission-critical applications where reliability is crucial. For more information, see [Mission-critical baseline with App Service](/azure/architecture/guide/networking/global-web-applications/mission-critical-app-service).
>
>   The App Service plan distributes traffic across instances and monitors their health. Note that the external load balancer might not immediately detect if one instance fails.
>
>  - **Plan your recoverability**: Redundancy is a crucial for business continuity. Fail over to another instance if one becomes unreachable. Explore Auto Healing capabilities offered by App Service, such as automatic repair of instances.
>
>     Implement design patterns to handle graceful degradation for both transient failures, such as network connectivity problems, and large-scale events like regional outages.
>     - Bulkhead pattern segments your application into isolated groups to prevent a failure from affecting the entire system.
>     - Queue-Based Load Leveling pattern queues work items that serve as a buffer to smooth out traffic spikes.
>     - Retry patterns handles transient failures caused by network glitches, dropped database connections, or busy services.
>     - Circuit Breaker pattern to prevent an application from repeatedly trying to perform an operation that's likely to fail.
>    Web Jobs allows you to run background tasks in your Web App. To run those tasks reliably, ensure that the app hosting your job is set to run continuously, on a schedule, or based on event-driven triggers.
>
>    For more information, see [Reliability patterns](/azure/well-architected/reliability/design-patterns).
>
> - **Conduct reliability testing**: Conduct load testing to evaluate your application's reliability and performance under load. Test plans should include scenarios that validate your automated recovery operations.
>
>   Use fault injection to intentionally introduce failures and validate your self-healing and self-preservation mechanisms. Explore the [fault library provided by Azure Chaos Studio](/azure/chaos-studio/chaos-studio-fault-library).
>
>    App Service imposes resource limits on hosted apps. These limits are determined by the associated App Service plan. Make sure that your tests confirm that the app runs within those resource limits. For more information, see [Azure subscription and service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits?branch=main#app-service-limits).
>
> - **Use health probes to identify unresponsive workers**: App Service has built-in capabilities that periodically ping a specific path of your web application. Unresponsive instances are removed from the load balancer and replaced with a new instance.

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|(App Service plan) Choose the Premium tier of App Service plan for production workloads. <br><br> Set the maximum and minimum number of workers according to your capacity planning. <br><br>For more information, see [App Service plan overview](/azure/app-service/overview-hosting-plans). |Premium App Service plan offers advanced scaling features and ensures redundancy if failures occur.|
|(App Service plan) [Enable zone redundancy](/azure/reliability/reliability-app-service#availability-zone-support).<br><br> Consider provisioning more than three instances to enhance fault tolerance. <br><br> Check regional support for zone redundancy because not all regions offer this feature. | Your application can withstand failures in a single zone when multiple instances are spread across zones. Traffic automatically shifts to healthy instances in other zones and maintains application reliability if one zone becomes unavailable.|
|(App Service) Consider disabling Application Request Routing (ARR) Affinity. ARR Affinity creates sticky sessions that are used to redirect users to the node that handled their previous requests.|Incoming requests are evenly distributed across all available nodes when you disable ARR Affinity. Evenly distributed requests prevent traffic from overwhelming any single node. Requests can be seamlessly redirected to other healthy nodes if a node is unavailable. <br> Your App Service remains stateless by avoiding session affinity. A stateless App Service reduces complexity and ensures consistent behavior across nodes. <br> Removing sticky sessions also helps App Service to scale horizontally by adding or removing instances.|
|(App Service) [Define auto-healing rules](/azure/app-service/overview-diagnostics#auto-healing) based on request count, slow request, memory limit, and other indicators that are considered as part of your performance baseline. <br> Consider this configuration as part of your scaling strategy. <br> |Auto-healing rules help your application to recover automatically from unexpected problems. The configured rules trigger healing actions when thresholds are breached. <br> Auto-healing enables automatic proactive maintenance. |
|(App Service) [Enable Health Check](/azure/app-service/monitor-instances-health-check) and provide a path that responds to the health check requests. <br> |Health checks can detect problems early. Then the system can automatically take corrective actions when a health check request fails. <br> The load balancer routes traffic away from unhealthy instances, which directs users to healthy nodes.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around hosting on App Service.

##### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review security baselines**: To enhance the security posture of your application hosted on an App Service plan, review the security baselines for [App Service](/security/benchmark/azure/baselines/app-service-security-baseline)
>
> - **Use the latest runtime and libraries**: Thoroughly test your application builds before updating to catch problems early and ensure a smooth transition to the new version. App Service supports [language runtime support policy](/azure/app-service/language-support-policy) for updating existing stacks and retiring end-of-support stacks.
>
> - **Create segmentation through isolation boundaries to contain breach**: Apply _segmentation of identity_. For example, implement role-based access control (RBAC) to assign specific permissions based on roles. Follow the principle of least privilege (PoLP) to limit access rights to only what's necessary. Also create _segmentation at the network level_. [Inject App Service Apps in an Azure virtual network](/azure/app-service/web-sites-integrate-with-vnet) for isolation and define network security groups (NSGs) to filter traffic.
>
>   App Service plan offers the App Service Environment (ASE) tier that provides a higher degree of isolation. With ASE, you get dedicated compute and network.  
>
> - **Apply access controls on identities**: Restrict both _inward access to the Web App_ and _outward access from the Web App_ to other resources. This applies access controls on identities and helps maintain the workload's overall security posture.
>
>   Use Microsoft Entra ID for all authentication and authorization needs. Use built-in roles, such as [Web plan contributor](/azure/role-based-access-control/built-in-roles/web-and-mobile#web-plan-contributor), [Website contributor](/azure/role-based-access-control/built-in-roles/web-and-mobile#website-contributor), and [Generic: contributor, reader, owner](/azure/role-based-access-control/built-in-roles#general).
>
> - **Control network traffic to and from the application**: Don't expose application endpoints to the public internet. Instead add a private endpoint on the Web App that's placed in a dedicated subnet. Front your application with a reverse proxy that communicates with that private endpoint. Consider using Azure Application Gateway or Front Door for that purpose.
>
>   Deploy web application firewall (WAF) to protect against common vulnerabilities. Both Application Gateway and Front Door have integrated WAF capabilities.
>
>   Configure the reverse proxy rules and network settings appropriately to achieve the desired level of security and control. For example, add NSG rules on the private endpoint subnet to only accept traffic from the reverse proxy.
>
>     Egress traffic from the application to other PaaS services should be over private endpoints. Consider placing a firewall component to restrict egress traffic to the public internet. Both approaches prevent data exfiltration.
>
>   For a comprehensive view, see [App Service networking features](/azure/app-service/networking-features).
>
> - **Encrypt data**: Protect data in transit with end-to-end TLS. Use your customer-managed keys for full encryption of data at rest. For more information, see [Encryption at rest using customer-managed keys](/azure/app-service/configure-encrypt-at-rest-using-cmk).
>
>   Don't use legacy protocols such as TLS 1.0 and 1.1. App Service enables 1.2 by default. For more information, see [App Service TLS overview](/azure/app-service/overview-tls).
>
>   All instances of your App Service are assigned a default domain name. Use a custom domain and secure that domain with certificates.
>
> - **Reduce the attack surface**: Remove default configurations that you don't need. For example, disable remote debugging, local authentication for SCM sites, basic authentication, and so on. Disable unsecure protocols like HTTP, FTP, and others. Enforce configurations through Azure policies. For more information, see [Azure Policies](#azure-policies).
>
>   **Implement restrictive Cross-Origin Resource Sharing (CORS) policies**: Use restrictive CORS policies in your web application to only accept requests from the allowed domains, headers, and other criteria. Enforce CORS policies with built-in Azure policy definitions.
>
> - **Protect application secrets**: You often need to handle sensitive information, like API keys or authentication tokens. Instead of hardcoding these secrets directly into your application code or configuration files, you can use Key Vault references in app settings. When the application starts, App Service automatically retrieves the secret values from Key Vault by using the app's managed identity.
>
> - **Enable resource logs for your application**: Enable resource logs for your application to create comprehensive activity trails that provide valuable data during investigations that follow security incidents.
>
>   Consider logging as part of your threat modeling process when assessing threats.

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|(App Service) [Assign managed identity](/azure/app-service/overview-managed-identity) to the Web App. <br><br> To maintain isolation boundaries, don't share or reuse identities across applications. <br><br> Make sure that you [connect securely to your container registry](/azure/app-service/tutorial-custom-container) if you deploy using containers.|Outward communication from the application is authenticated, such as the application retrieving secrets from Key Vault. Azure manages the identity and doesn't require you to provision or rotate any secrets. <br> Create distinct identities for granularity of control. Distinct identities make revocation easy in case an identity is compromised.|
|(App Service) Configure [custom domains](/azure/app-service/configure-ssl-bindings) for applications. <br><br> Disable HTTP and only accept HTTPS requests.|Custom domains enable secure communication through HTTPS (SSL/TLS), which ensures the protection of sensitive data and builds user trust.|
|(App Service) Evaluate whether [EasyAuth](/azure/app-service/overview-authentication-authorization) is the right mechanism to authenticate users that access your application. </br> EasyAuth is a feature of App Service that's integrated with Microsoft Entra ID. EasyAuth handles token validation and user identity management across multiple sign-in providers and supports OpenID Connect. <br><br> With this feature, you lose the capability to do authorization at a granular level. There's no mechanism to test authentication. |This feature removes the need and complexity of using authentication libraries in application code. The user is already authenticated when a request reaches the application.|
|(App Service) Configure the application for [virtual network integration](/azure/app-service/overview-vnet-integration). <br><br> Use [private endpoints for App Service apps](/azure/app-service/overview-private-endpoint). Block all public traffic. <br><br> Make sure the [container image is pulled through the virtual network](/azure/app-service/configure-vnet-integration-routing#container-image-pull) integration.<br><br> All [outgoing traffic from the application](/azure/app-service/configure-vnet-integration-routing#configure-application-routing) passes through the virtual network.|Brings the security benefits of using an Azure Virtual Network, such as the application can securely access resources within the network. <br> <br> Add a private endpoint to help protect your application. Private endpoints limit direct exposure to the public network and allow controlled access through the reverse proxy.|
|(App Service) Provide hardening by: <br> - [Disable basic authentication](/azure/app-service/configure-basic-auth-disable) that uses username/password in favor of Microsoft Entra ID-based authentication. <br> - Turn off remote debugging so that inbound ports aren't opened.<br> - Enable [CORS policies](/azure/app-service/app-service-web-tutorial-rest-api) to tighten incoming requests. <br> - Disable protocols, such as [FTP](/azure/app-service/configure-common). |Basic authentication isn't recommended as a secure deployment method. Microsoft Entra employs OAuth 2.0 token-based authentication, which offers numerous advantages and enhancements that address the limitations associated with basic authentication. <br><br> The policies restrict access to application resources, only allows requests from specific domains, secures cross-region requests, and so on. |
|(App Service)[Always use Key Vault references as app settings](/azure/app-service/app-service-key-vault-references). <br> |Secrets are kept separate from your app's configuration. App settings are encrypted at rest. App Service also manages secret rotations. |
|(App Service plan) [Enable Defender for Cloud and App Service](/azure/defender-for-cloud/tutorial-enable-app-service-plan).| Provides real-time protection to resources running in App Service plan against threats and enhances our overall security posture.|
|(App Service) [Enable diagnostic logging](/azure/app-service/troubleshoot-diagnostic-logs) and add instrumentation to your app. <br> The logs are sent to Storage Accounts, Event Hubs and Log Analytics.<br> For more information about audit log types, see [Supported log types](/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types).|Logging is a common way to capture access patterns. It records relevant events that provide valuable insights into how users interact with an application or platform. This information is crucial for accountability, compliance, and security purposes.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to your web app and the environment in which they run.

##### Design checklist

Start your design strategy based on the [**design review checklist for Cost Optimization**](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Estimate the initial cost**: As part of your cost modeling exercise, use the [pricing calculator](https://azure.microsoft.com/pricing/calculator) to evaluate the approximate costs associated with various tiers based on the number of instances you plan to run. Each App Service tier offers different compute options.

>   Continuously monitor the cost model to track expenditure.
>
> - **Evaluate the discounted options**: Higher tiers include dedicated compute instances. You can apply the reservation discount if your workload has a predictable and consistent usage pattern. Make sure you analyze usage data to choose the type of reservation. For more information, see [Save costs with App Service reserved instances](/azure/cost-management-billing/reservations/prepay-app-service).
>
> - **Have a good understanding of the usage meters**: You’re charged an hourly rate, prorated to the second, based on your App Service plan’s pricing tier. Charges apply to each scaled-out instance in your plan, based on the time the VM instance is allocated. Pay attention to underutilized compute resources that might be increasing your costs as result of overallocation due to suboptimal SKU selection, or poorly configured scale-in configuration.
>
>    You can be charged for additional App Services features, like custom domain registration and custom certificates, and for other resources that might be created together with your App Service resources, like a virtual network to isolate your solution, or a Key Vault to protect workload secrets. For more information, see [App Services billing model](/azure/app-service/overview-manage-costs#understand-the-full-billing-model-for-azure-app-service).
>
> - **Consider the tradeoffs between density and isolation**: You can use App Service plans to host multiple applications on the same compute, which save costs with shared environments. For more information, see [Tradeoffs](#tradeoffs).
>
> - **Evaluate the effect of your scaling strategy on cost**: It's crucial to properly design, test, and configure for scaling out and for scaling in when you implement autoscaling. Be precise about maximum and minimum limits on autoscaling.
>
>   Initialize the application proactively for reliable scaling. For example, instead of waiting until the CPU reaches 95% usage, trigger scaling at around 65% to allow for sufficient time for new instances to be allocated and initialized during the scaling process. However, this strategy might lead to unused capacity.
>
>   We recommended that you combine and balance scaling up and scaling out mechanisms. For example, the app can scale up for some time and then scale out as necessary. It's worth exploring higher tiers that offer larger capacity and efficient resource usage. Based on usage patterns, higher Premium tiers might be more cost effective because they are more capable.
>
> - **Optimize environment costs**: Consider Basic or Free tier for running pre-production environments. These are low-performance tiers that are less expensive. If these tiers are suitable, use governance to enforce those tiers, constrain the number of instances and CPUs, restrict scaling, and limit log retention, other possible configurations.
>
> - **Implement design patterns**: This strategy reduces the volume of requests that your workload generates. Consider using patterns like [Backend for frontends](/azure/architecture/patterns/backends-for-frontends) and [Gateway aggregation](/azure/architecture/patterns/gateway-aggregation), which can reduce costs by minimizing the number of requests.
>
> - **Regularly check costs related to data**: Extending data retention periods or opting for more expensive storage tiers might lead to higher storage costs. More expenses can accumulate due to both bandwidth usage and prolonged retention of logging data.
>
>   Consider implementing caching to minimize data transfer costs. Start with local in-memory caching, and then explore distributed caching options to reduce the number of requests to the backend database. Be mindful of the bandwidth traffic costs associated with cross-region communication if your database is located in a different region.
>
> - **Optimize deployment costs**: By taking advantage of deployment slots, you can optimize costs because the slot runs in the same compute environment as the production instance. Use them strategically for scenarios like blue-green deployments, where you switch between slots to minimize downtime and ensure smooth transitions.
>
>   Use deployment slots with caution. You can introduce problems, such as exceptions or memory leaks, can affect both the existing and new instances. Make sure changes are thoroughly tested. For operational guidance, see [Operational Excellence](#operational-excellence).

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|(App Service plan) [Choose Free or Basic tiers](/azure/app-service/overview-hosting-plans) for lower environments. <br> These tiers are recommended for experimental use. Remove them when no longer needed.|The Free and Basic tiers are more budget-friendly compared to higher tiers. They provide a cost-effective solution for nonproduction environments where you don't need the full features and performance of premium plans.|
|(App Service plan) Take advantage of discounts and explore these preferred pricing for: <br> - Lower environments with [dev/test plans](https://azure.microsoft.com/pricing/offers/dev-test/). <br> - [Azure reservations](/azure/app-service/overview-manage-costs#azure-reservations) and [Azure Savings Plan](https://azure.microsoft.com/pricing/offers/savings-plan-compute/#Benefitsandfeatures) for dedicated compute provisioned in Premium V3 and App Service Environment (ASE) v2. <br><br> Reserved instances are ideal for stable workloads with predictable usage patterns. |Dev/Test plans provide reduced rates for Azure services, making them cost-effective for nonproduction environments. <br> Reserved instances allow you to prepay for compute resources with significant discounts.|
|[Monitor cost](/azure/app-service/overview-manage-costs#monitor-costs) incurred by App Service resources. Run the Cost Analysis tool in Azure portal. <br><br> [Create budgets and alerts](/azure/app-service/overview-manage-costs#create-budgets) to notify stakeholders.|Monitoring allows you to identify cost spikes, inefficiencies, or unexpected expenses early on. This proactive approach helps you to provide budgetary controls to prevent overruns.|
|(App Service plan) Scale in when demand decreases. <br><br> To scale-in, define scale rules to [reduce the number of instances in Azure Monitor](/azure/azure-monitor/autoscale/autoscale-understanding-settings).|By scaling in, you prevent wastage and reduce unnecessary expenses.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

##### Design checklist

Start your design strategy based on the [**design review checklist for Operational Excellence**](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to web apps.

> [!div class="checklist"]

> - **Release Management**: Deployment slots allow you to manage releases effectively. You can deploy your application to a slot, perform testing, and validate its functionality. Once verified, you can seamlessly swap it into production. Importantly, this process doesn't incur additional costs because the slot runs in the same Virtual Machine (VM) environment as the production instance.
>
> - **Run automated tests**: Before promoting a release of your web application, thoroughly test its performance, functionality, integration with other components, and so on. Utilize [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing), which integrates with JMeter, a popular tool for performance testing. Explore automated tools for other types of testing, such as Phantom for functional testing.
>
> - **Deploy immutable units**: Implement the [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp) that compartmentalizes App Service into an immutable stamp. App Service supports the use of containers, which are inherently immutable. Consider your app services web app with [custom containers](/azure/app-service/configure-custom-container).
>
>    Each stamp represents a self-contained unit that can be quickly scaled out or scaled in. Units based on this stamp are temporary and stateless. Stateless design simplifies operations and maintenance. This approach is ideal for mission-critical applications and is demonstrated in the [Mission-critical baseline with App Service](/azure/architecture/guide/networking/global-web-applications/mission-critical-app-service).
>
>   Use an Infrastructure-as-Code (IaC) technology, such as Bicep to stamp out units with repeatability and consistency.
>
> - **Keep production environments safe**: Create separate App Service plans for running production and pre-production environments. Avoid making changes directly in the production environment to ensure stability and reliability. Separate instances allow flexibility in development and testing before promoting changes to production.
>
>   Lower environments are recommended for exploration of new features and configurations in an isolated manner. Keep development and test environments ephemeral.
>
> - **Certificate management**: For custom domains, you need to manage Transport Layer Security (TLS) certificates.
>
>   Have processes in place that procure, renew, and validate certificates. Offloading this to App Service is preferred. You're responsible for independently managing its renewal if you use your own certificate. Choose an approach that best aligns with your security requirements.

|Recommendation|Benefit|
|------------------------------|-----------|
|(App Service) [Monitor the health of your instances](/azure/app-service/monitor-instances-health-check?tabs=dotnet) and activate instance health probes. <br><br>Set up a specific path for handling health probe requests. |You can detect problems promptly and take necessary actions to maintain agreed upon availability and performance.|
|(App Service) [Enable diagnostics logs](/azure/app-service/troubleshoot-diagnostic-logs) for the application and the instance. <br><br> Frequent logging can slow down the performance of the system, add to storage costs, and can be risky if access to logs isn't secured. Follow these best practices: <br> - Log the right level of information. <br>- Set retention policies. <br> - Keep audit trail of authorized access and unauthorized attempts. <br>- Treat logs as data and apply data protection controls. |Diagnostic logs provide valuable insights into your app’s behavior. Useful for monitoring traffic patterns and identifying anomalies.|
|(App Service) Take advantage of [App Service managed certificates](/azure/app-service/configure-ssl-certificate#create-a-free-managed-certificate) that offloads certification management to Azure. |App Service automatically handles processes like certificate procurement, certificate verification, certificate renewal, and importing certificates from Azure Key Vault. Alternatively, upload your certificate to key vault and authorize the App Service resource provider to access it. |
|(App Service plan) [Validate app changes in the staging slot](/azure/app-service/deploy-staging-slots) before swapping it with the production slot. |You can test different versions of code and configuration, and swap them with production without downtime or errors. <br><br> You can quickly revert to the last known good state by swapping back if a problem is detected after a swap.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [**design review checklist for Performance Efficiency**](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for web apps.

> [!div class="checklist"]

> - **Identify and monitor the performance indicators**: Set targets for the key indicators for the application, such as the volume of incoming requests, time taken by the application to respond to requests, pending requests, errors in HTTP responses, and others. Key indicators must be considered as part of the performance baseline for the workload.
>
>   Capture App Service metrics that form the basis of performance indicators. Make sure to collect logs to gain insights into resource usage and activities. Use Application Performance Monitoring (APM) tools, such as Application Insights that collect collect and analyze performance data from the application. For more information, see [App Service monitoring data reference](/azure/app-service/monitor-app-service-reference).
>
>   Include code-level instrumentation, transaction tracing, and performance profiling.
>
> - **Capacity assessment**: By simulating various user scenarios, you can determine the optimal capacity needed to handle expected traffic. Use Azure Load Testing to understand how your application behaves under different levels of load.
>
> - **Select the right tier**: Dedicated compute is recommended for production workloads. Premium tiers offer larger SKUs with increased memory and CPU capacity, more instances, and features such as zone redundancy. For capabilities of higher tiers, see [Premium V3 pricing tier](/azure/app-service/overview-hosting-plans#premium-v3-pricing-tier).
>
> - **Optimize your scaling strategy**: Always opt for [automatic ways of scaling](/azure/azure-monitor/autoscale/autoscale-overview) instead of manually adjusting the number of instances as application load changes. With automatic scaling, App Service adjusts server capacity based on predefined rules or triggers. Make sure you do adequate performance testing and you then set the right rules for the right triggers.
>
>   There's another automatic scaling option that doesn't require you to define rules and only set limits. This is a good option for users who prioritize simplicity and initial setup.
>
>   Sufficient resources should be readily available to ensure optimal performance. Allocate resources appropriately to maintain performance targets, such as response time or throughput. Consider overallocation of resources when necessary.
>
>    When defining auto-scale rules, account for the time it takes for your application to initialize. Scaling decisions should consider this initialization overhead.
>
> - **Use caching**:  Retrieving information from a resource that doesn't change frequently and is expensive to access affects performance. Complex queries, including joins and multiple lookups, contribute to execution time. Caching aims to minimize the processing time and latency. By caching query results, you avoid repeated round trips to the database or backend. Caching query results also reduces processing time for subsequent requests.
>
>   For more information about using local and distributed cache in the workload, see [Caching](/azure/architecture/best-practices/caching).
>
> - **Review the performance antipatterns**: To make sure the web application performs and scales as your business requirements, avoid the [typical antipatterns](/azure/architecture/antipatterns/). Here are some antipatterns that are corrected by App Service.
>
>   |Antipattern|Description|
>   |------------------------------|-----------|
>   |[Busy Front End antipattern](/azure/architecture/antipatterns/busy-front-end/)|Resource-intensive tasks can increase the response times for user requests and cause high latency. <br>Move processes that consume significant resources to a separate back end. Use a message broker to queue resource-intensive tasks that the backend picks up asynchronously processing.|
>   |[No Caching antipattern](/azure/architecture/antipatterns/no-caching/)|Reduce latency by serving requests from an intermediate cache in front of the backend database. |
>   |[Noisy Neighbor](/azure/architecture/antipatterns/noisy-neighbor/noisy-neighbor)| Multi-tenant systems share resources between tenants, the activity of one tenant can have a negative effect on another tenant's use of the system. The App Service Environment (ASE) tier provides a fully isolated and dedicated environment for running App Service apps.|

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|(App Service) [Enable Always On](/azure/app-service/configure-common) when applications share a single App Service plan. <br><br> App Service apps automatically unload when idle to save resources, the next request triggers a cold start, potentially causing request timeouts. | The application is never unloaded with Always On enabled.|
|(App Service) [Consider using HTTP/2](/azure/app-service/configure-common) for applications to improve protocol efficiency. |HTTP/2 improves over HTTP/1.1 by fully multiplexing connections, reusing connections to reduce overhead, and compressing headers to minimize data transfer.|

## Tradeoffs

There are design tradeoffs with the approaches described in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: **Density and isolation**.

- **Higher density**: By colocating multiple apps within the same App Service plan, you minimize the resources. All apps share CPU, memory, and so on. This can lead to cost savings and reduced operational complexity. Resource usage is also optimized. Idle resources from one app can be used by another if apps have different load patterns over time.

  There are also disadvantages. Spikes in usage or instability of an app can affect the performance of other apps. Security is also a concern. Because incidents in one app can permeate to other apps within the shared environment.

- **Higher isolation**: Isolation is intended to avoid interference. This strategy applies to security, performance, and even segregation of development, testing, and production environment.

  The App Service Environment (ASE) tier gives you better control over security and data protection as each app can have its own security settings. There's better containment of breaches because isolation limits the blast radius. Resource contention is minimized from a performance perspective. Isolation allows for independent scaling based on specific demand and individual capacity planning.

  This approach is more expensive and requires operational rigor.

:::image type="icon" source="../_images/trade-off.svg"::: **Reliable scaling strategy**.

A well-defined scaling strategy ensures that your application can handle varying loads without compromising performance. However, there are tradeoffs on cost.

Scaling operations take time. When new resources are allocated, the application must be properly initialized before it can effectively process requests. **Overprovisioning resources (prewarm instances)** provides a safety net. Without that extra capacity, during the initialization phase, there might be a delay in serving requests, affecting user experience. The triggers for auto scaling operations must signal early enough to enable proper resource initialization by the time the resources are used.

Overprovisioning leads to **higher costs**. You're charged per second for every instance, including prewarmed instances. Higher tiers include prewarmed instances. Determine whether capabilities offered with more expensive tiers are worth the investment.  

:::image type="icon" source="../_images/trade-off.svg"::: **Building redundancy**.

Redundancy offers resilience while also incurring costs.

Service level objectives (SLOs) for your workload determine acceptable performance thresholds. It becomes wasteful if redundancy exceeds SLO requirements. Evaluate whether excess redundancy significantly improves SLOs or merely adds unnecessary complexity.

For example, multi-region redundancy provides high availability. However, it's complex and costly due to data synchronization, failover mechanisms, and inter-region communication. Evaluate if your SLOs can be met with zonal redundancy.

## Azure policies

Azure provides an extensive set of built-in policies related to App Service and the dependencies. A set of Azure Policies can audit some of the preceding recommendations. For example, you can check if:

- Proper network controls are in place. For example, network segmentation is done by placing App Service in Azure Virtual Network through virtual network injection to have greater control over network configuration. The application doesn't have public endpoints and connects to Azure services through private endpoints.

- Identity controls are in place. For example, the application uses managed identities to authenticate itself against other resources. Incoming requests are verified by using App Service Authentication (EasyAuth).

- Attack surface is reduced by disabling features such as remote debugging and basic authentication.

For comprehensive governance, review the [Azure Policy built-in definitions](/azure/app-service/policy-reference) and other policies that might affect the security of the compute layer.

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your web application instances.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Next steps

Consider the following articles as resources that demonstrate the recommendations highlighted in this article.

- Use these reference architectures as examples of how to apply these recommendations to a workload.

  - Start with [Basic web application](/azure/architecture/web-apps/app-service/architectures/basic-web-app) if you've never deployed a Web Apps.
  
  - Foundational architecture as your starting point for a production-grade deployment is described in [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).
  
- Build implementation expertise by using product documentation:

  - [App Service](/azure/app-service/)
  
  - [App Service plan](/azure/app-service/overview-hosting-plans)
