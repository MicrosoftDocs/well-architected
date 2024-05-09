---
title: Azure Well-Architected Framework perspective of the Web Apps feature of Azure App Service 
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for the Web Apps feature of Azure App Service.
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

# Azure Well-Architected Framework perspective of the Web Apps feature of Azure App Service

Azure App Service is a platform as a service (PaaS) compute solution that you can use to host your workload on the Azure platform. It's a fully managed service that abstracts the underlying compute and offloads the responsibility of building, deploying, and scaling to the platform. An app service always runs in an App Service plan. The service plan that you choose determines the region in which the workload runs, the compute configurations, and the operating system. Multiple billing models are available for App Service.

This article assumes that as an architect, you reviewed the [compute decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree) and chose App Service as the compute for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a _design checklist_ that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are _recommendations_ on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for the Web Apps feature of Azure App Service and their dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments.
>
> Foundational architecture that demonstrates the key recommendations: [App Service baseline architecture](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).

##### Technology scope

This review focuses on the interrelated decisions for the following Azure resources:

- App Service plans
- The Web Apps feature of App Service

Other Azure offerings are associated with App Service, such as Azure Functions, Azure Logic Apps, and App Service Environment. Those offerings are out of scope for this article. App Service Environment is referenced occasionally to help clarify features or options of the core App Service offerings.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

##### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the tiers and features of App Service and its dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Prioritize user flows**: Not all flows are equally critical. Assign priorities to each flow to guide your design decisions. User flow design can influence which service tiers and instances that you choose for an App Service plan and configuration.
>
>     For example, your application might include front-end and back-end tiers that communicate through a message broker. You might choose to segment the tiers in multiple web apps to allow for independent scaling, lifecycle management, and maintenance. Placing a large application in a single plan can lead to memory or CPU problems and affect reliability.
>
>   You might need more instances on the front end for optimal performance on the UI side. However, the back end might not require the same number of instances.
>
> - **Anticipate potential failures**: Plan mitigation strategies for potential failures. The following table shows examples of failure mode analysis.
>
>   |Failure|Mitigation|
>   |---|---|
>   |Failure of underlying or abstracted App Service components| Have component redundancy in instances and dependencies. Monitor the health of instances, network performance, and storage performance.|
>   |Failure of external dependencies | Use design patterns such as the [Retry pattern](/azure/architecture/patterns/retry) and the [Circuit Breaker pattern](/azure/architecture/patterns/circuit-breaker). Monitor the external dependencies and set appropriate timeouts. |
>   |Failure due to traffic getting routed to unhealthy instances| Monitor instance health. Consider responsiveness, and avoid sending requests to unhealthy instances. |
>
>   For more information, see [Failure mode analysis for Azure applications](/azure/architecture/resiliency/failure-mode-analysis).
>
> - **Build redundancy**: Build redundancy in the application and supporting infrastructure. Spread instances across availability zones to improve fault tolerance. Route traffic to other zones if one zone fails. Deploy your application across multiple regions to ensure that your app remains available, even if an entire region experiences an outage.
>
>   Build similar levels of redundancy in dependent services. For instance, application instances bind to blob storage. Consider configuring the associated storage account with zone-redundant storage (ZRS) if an application uses a zone-redundant deployment.
>
>   Have redundancy in networking components. For example, use zone-redundant IP addresses and load balancers.
>
> - **Have a reliable scaling strategy**: Unexpected load on an application can make it unreliable. Consider the right scaling approach based on your workload characteristics. You can sometimes scale up to handle the load. However, if the load continues to increase, scale out to new instances. Prefer automatic scaling over manual approaches. Always maintain a buffer of extra capacity during scaling operations to prevent performance degradation.
>
>   The [App Service plan tier](/azure/app-service/overview-hosting-plans#how-does-my-app-run-and-scale) that you choose affects scaling in terms of the number of instances and the compute units.
>
>   Ensure proper app initialization so that new instances warm up quickly and can receive requests.
>
>   Strive for stateless applications whenever possible. Reliably scaling state with new instances can increase complexity. Consider an external data store that you can scale independently if you need to store application state. Storing session state in memory can result in losing session state when there's a problem with the application or App Service. It also limits the possibility of spreading the load across other instances.
>
>   Regularly test your autoscaling rules. Simulate load scenarios to verify that your app scales as expected. You should log scaling events so that you can troubleshoot problems that might arise and optimize your scaling strategy over time.
>
>   App Service has a limitation on the number of instances within a plan, which can affect scaling reliability. One strategy is to use identical deployment stamps, each running App Service plan instance with its own endpoint. It's essential that you front all stamps with an external load balancer to distribute traffic across them. Use Azure Application Gateway for single node deployments and Azure Front Door for multi-regional deployments. This approach is ideal for mission-critical applications where reliability is crucial. For more information, see [Mission-critical baseline with App Service](/azure/architecture/guide/networking/global-web-applications/mission-critical-app-service).
>
>   An App Service plan distributes traffic across instances and monitors their health. Note that the external load balancer might not immediately detect if one instance fails.
>
> - **Plan your recoverability**: Redundancy is crucial for business continuity. Fail over to another instance if one instance is unreachable. Explore automatic healing capabilities in App Service, such as automatic repair of instances.
>
>   Implement design patterns to handle graceful degradation for both transient failures, such as network connectivity problems, and large-scale events like regional outages. Consider the following design patterns:
>
>   - *The Bulkhead pattern* segments your application into isolated groups to prevent a failure from affecting the entire system.
>
>   - *The Queue-Based Load Leveling pattern* queues work items that serve as a buffer to smooth out traffic spikes.
>
>   - *The Retry pattern* handles transient failures due to network glitches, dropped database connections, or busy services.
>
>   - *The Circuit Breaker pattern* prevents an application from repeatedly trying to perform an operation that's likely to fail.
>
>   You can use WebJobs to run background tasks in your web app. To run those tasks reliably, ensure that the app that hosts your job runs continuously on a schedule or based on event-driven triggers.
>
>   For more information, see [Reliability patterns](/azure/well-architected/reliability/design-patterns).
>
> - **Conduct reliability testing**: Conduct load testing to evaluate your application's reliability and performance under load. Test plans should include scenarios that validate your automated recovery operations.
>
>   Use fault injection to intentionally introduce failures and validate your self-healing and self-preservation mechanisms. Explore the [fault library provided by Azure Chaos Studio](/azure/chaos-studio/chaos-studio-fault-library).
>
>   App Service imposes resource limits on hosted apps. The App Service plan determines these limits. Make sure that your tests confirm that the app runs within those resource limits. For more information, see [Azure subscription and service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits#app-service-limits).
>
> - **Use health probes to identify unresponsive workers**: App Service has built-in capabilities that periodically ping a specific path of your web application. Unresponsive instances are removed from the load balancer and replaced with a new instance.

##### Recommendations

|Service or plan | Recommendation|Benefit|
|------------------------------|-----------|-------- |
|App Service plan | Choose the Premium tier of an App Service plan for production workloads. <br><br> Set the maximum and minimum number of workers according to your capacity planning. For more information, see [App Service plan overview](/azure/app-service/overview-hosting-plans). |A premium App Service plan offers advanced scaling features and ensures redundancy if failures occur.|
|App Service plan | [Enable zone redundancy](/azure/reliability/reliability-app-service#availability-zone-support).<br><br> Consider provisioning more than three instances to enhance fault tolerance. <br><br> Check regional support for zone redundancy because not all regions offer this feature. | Your application can withstand failures in a single zone when multiple instances are spread across zones. Traffic automatically shifts to healthy instances in other zones and maintains application reliability if one zone is unavailable.|
|App Service | Consider disabling the application request routing (ARR) affinity feature. ARR affinity creates sticky sessions that redirect users to the node that handled their previous requests.|Incoming requests are evenly distributed across all available nodes when you disable ARR affinity. Evenly distributed requests prevent traffic from overwhelming any single node. Requests can be seamlessly redirected to other healthy nodes if a node is unavailable. <br><br> Avoid session affinity to ensure that your App Service instance remains stateless. A stateless App Service reduces complexity and ensures consistent behavior across nodes. <br><br> Remove sticky sessions so that App Service can add or remove instances to scale horizontally.|
|App Service | [Define automatic healing rules](/azure/app-service/overview-diagnostics#auto-healing) based on request count, slow requests, memory limits, and other indicators that are part of your performance baseline. Consider this configuration as part of your scaling strategy. |Automatic healing rules help your application recover automatically from unexpected problems. The configured rules trigger healing actions when thresholds are breached. <br><br> Automatic healing enables automatic proactive maintenance. |
|App Service |[Enable the health check feature](/azure/app-service/monitor-instances-health-check) and provide a path that responds to the health check requests. |Health checks can detect problems early. Then the system can automatically take corrective actions when a health check request fails. <br><br> The load balancer routes traffic away from unhealthy instances, which directs users to healthy nodes.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around hosting on App Service.

##### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review security baselines**: To enhance the security posture of your application that's hosted on an App Service plan, review the [security baseline for App Service](/security/benchmark/azure/baselines/app-service-security-baseline).
>
> - **Use the latest runtime and libraries**: Thoroughly test your application builds before you do updates to catch problems early and ensure a smooth transition to the new version. App Service supports the [language runtime support policy](/azure/app-service/language-support-policy) for updating existing stacks and retiring end-of-support stacks.
>
> - **Create segmentation through isolation boundaries to contain breach**: Apply identity segmentation. For example, implement role-based access control (RBAC) to assign specific permissions based on roles. Follow the principle of least privilege to limit access rights to only what's necessary. Also create segmentation at the network level. [Inject App Service apps in an Azure virtual network](/azure/app-service/web-sites-integrate-with-vnet) for isolation and define network security groups (NSGs) to filter traffic.
>
>   App Service plans offer the App Service Environment tier that provides a high degree of isolation. With App Service Environment, you get dedicated compute and networking.
>
> - **Apply access controls on identities**: Restrict both inward access to the web app and outward access from the web app to other resources. This configuration applies access controls on identities and helps maintain the workload's overall security posture.
>
>   Use Microsoft Entra ID for all authentication and authorization needs. Use built-in roles, such as a [Web Plan Contributor](/azure/role-based-access-control/built-in-roles/web-and-mobile#web-plan-contributor), [Website Contributor](/azure/role-based-access-control/built-in-roles/web-and-mobile#website-contributor), and a [generic Contributor, Reader, and Owner](/azure/role-based-access-control/built-in-roles#general).
>
> - **Control network traffic to and from the application**: Don't expose application endpoints to the public internet. Instead, add a private endpoint on the web app that's placed in a dedicated subnet. Front your application with a reverse proxy that communicates with that private endpoint. Consider using Application Gateway or Azure Front Door for that purpose.
>
>   Deploy a web application firewall (WAF) to protect against common vulnerabilities. Both Application Gateway and Azure Front Door have integrated WAF capabilities.
>
>   Configure the reverse proxy rules and network settings appropriately to achieve the desired level of security and control. For example, add NSG rules on the private endpoint subnet to only accept traffic from the reverse proxy.
>
>     Egress traffic from the application to other PaaS services should be over private endpoints. Consider placing a firewall component to restrict egress traffic to the public internet. Both approaches prevent data exfiltration.
>
>   For a comprehensive view, see [App Service networking features](/azure/app-service/networking-features).
>
> - **Encrypt data**: Protect data in transit with end-to-end Transport Layer Security (TLS). Use your customer-managed keys for full encryption of data at rest. For more information, see [Encryption at rest using customer-managed keys](/azure/app-service/configure-encrypt-at-rest-using-cmk).
>
>   Don't use legacy protocols such as TLS 1.0 and 1.1. App Service enables 1.2 by default. For more information, see [App Service TLS overview](/azure/app-service/overview-tls).
>
>   All instances of your App Service have a default domain name. Use a custom domain and secure that domain with certificates.
>
> - **Reduce the attack surface**: Remove default configurations that you don't need. For example, disable remote debugging, local authentication for Source Control Manager (SCM) sites, and basic authentication. Disable unsecure protocols like HTTP and File Transfer Protocol (FTP). Enforce configurations through Azure policies. For more information, see [Azure policies](#azure-policies).
>
>   **Implement restrictive cross-origin resource sharing (CORS) policies**: Use restrictive CORS policies in your web app to only accept requests from the allowed domains, headers, and other criteria. Enforce CORS policies with built-in Azure policy definitions.
>
> - **Protect application secrets**: You need to handle sensitive information, like API keys or authentication tokens. Instead of hardcoding these secrets directly into your application code or configuration files, you can use Azure Key Vault references in app settings. When the application starts, App Service automatically retrieves the secret values from Key Vault by using the app's managed identity.
>
> - **Enable resource logs for your application**: Enable resource logs for your application to create comprehensive activity trails that provide valuable data during investigations that follow security incidents.
>
>   Consider logging as part of your threat modeling process when you assess threats.

##### Recommendations

|Service or plan |Recommendation|Benefit|
|------------------------------|-----------|-------------- |
|App Service | [Assign managed identities](/azure/app-service/overview-managed-identity) to the web app. To maintain isolation boundaries, don't share or reuse identities across applications. <br><br> Make sure that you [securely connect to your container registry](/azure/app-service/tutorial-custom-container) if you use containers for your deployment.|The application retrieves secrets from Key Vault to authenticate outward communication from the application. Azure manages the identity and doesn't require you to provision or rotate any secrets. <br><br> You have distinct identities for granularity of control. Distinct identities make revocation easy if an identity is compromised.|
|App Service | Configure [custom domains](/azure/app-service/configure-ssl-bindings) for applications. <br><br> Disable HTTP and only accept HTTPS requests.|Custom domains enable secure communication through HTTPS by using the Secure Sockets Layer (SSL) or TLS protocol, which ensures the protection of sensitive data and builds user trust.|
|App Service |Evaluate whether [App Service built-in authentication](/azure/app-service/overview-authentication-authorization) is the right mechanism to authenticate users that access your application. App Service built-in authentication integrates with Microsoft Entra ID. This feature handles token validation and user identity management across multiple sign-in providers and supports OpenID Connect. With this feature, you don't have authorization at a granular level, and you don't have a mechanism to test authentication. |When you use this feature, you don't have to use authentication libraries in application code, which reduces complexity. The user is already authenticated when a request reaches the application.|
|App Service | Configure the application for [virtual network integration](/azure/app-service/overview-vnet-integration). <br><br> Use [private endpoints for App Service apps](/azure/app-service/overview-private-endpoint). Block all public traffic. <br><br> Route the [container image pull through the virtual network](/azure/app-service/configure-vnet-integration-routing#container-image-pull) integration. All [outgoing traffic from the application](/azure/app-service/configure-vnet-integration-routing#configure-application-routing) passes through the virtual network.|Get the security benefits of using an Azure virtual network. For example, the application can securely access resources within the network. <br> <br> Add a private endpoint to help protect your application. Private endpoints limit direct exposure to the public network and allow controlled access through the reverse proxy.|
|App Service | To implement hardening: <br> - [Disable basic authentication](/azure/app-service/configure-basic-auth-disable) that uses a username and password in favor of Microsoft Entra ID-based authentication. <br> - Turn off remote debugging so that inbound ports aren't opened.<br> - Enable [CORS policies](/azure/app-service/app-service-web-tutorial-rest-api) to tighten incoming requests. <br> - Disable protocols, such as [FTP](/azure/app-service/configure-common). |We don't recommend basic authentication as a secure deployment method. Microsoft Entra ID employs OAuth 2.0 token-based authentication, which offers numerous advantages and enhancements that address the limitations that are associated with basic authentication. <br><br> Policies restrict access to application resources, only allow requests from specific domains, and secure cross-region requests. |
|App Service | [Always use Key Vault references as app settings](/azure/app-service/app-service-key-vault-references). <br> |Secrets are kept separate from your app's configuration. App settings are encrypted at rest. App Service also manages secret rotations. |
|App Service plan |[Enable Microsoft Defender for Cloud for App Service](/azure/defender-for-cloud/tutorial-enable-app-service-plan).| Get real-time protection for resources that run in an App Service plan. Guard against threats and enhance your overall security posture.|
|App Service plan |[Enable diagnostic logging](/azure/app-service/troubleshoot-diagnostic-logs) and add instrumentation to your app. The logs are sent to Azure Storage accounts, Azure Event Hubs, and Log Analytics. For more information about audit log types, see [Supported log types](/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types).|Logging captures access patterns. It records relevant events that provide valuable insights into how users interact with an application or platform. This information is crucial for accountability, compliance, and security purposes.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to your web apps and the environment in which they run.

##### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Estimate the initial cost**: As part of your cost modeling exercise, use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator) to evaluate the approximate costs associated with various tiers based on the number of instances that you plan to run. Each App Service tier offers different compute options.
>
>   Continuously monitor the cost model to track expenditures.
>
> - **Evaluate the discounted options**: Higher tiers include dedicated compute instances. You can apply a reservation discount if your workload has a predictable and consistent usage pattern. Make sure that you analyze usage data to determine the type of reservation that suits your workload. For more information, see [Save costs with App Service reserved instances](/azure/cost-management-billing/reservations/prepay-app-service).
>
> - **Understand usage meters**: Azure charges an hourly rate, prorated to the second, based on your App Service plan's pricing tier. Charges apply to each scaled-out instance in your plan, based on the time that you allocate the VM instance. Pay attention to underused compute resources that might increase your costs as a result of overallocation due to suboptimal SKU selection, or poorly configured scale-in configuration.
>
>    Extra App Service features, like custom domain registration and custom certificates, might add costs. Other resources, like virtual networks to isolate your solution or key vaults to protect workload secrets, that integrate with your App Service resources can also add costs. For more information, see [App Services billing model](/azure/app-service/overview-manage-costs#understand-the-full-billing-model-for-azure-app-service).
>
> - **Consider the tradeoffs between density and isolation**: You can use App Service plans to host multiple applications on the same compute, which saves costs with shared environments. For more information, see [Tradeoffs](#tradeoffs).
>
> - **Evaluate the effect of your scaling strategy on cost**: You must properly design, test, and configure for scaling out and for scaling in when you implement autoscaling. Establish precise maximum and minimum limits on autoscaling.
>
>   Proactively initialize the application for reliable scaling. For example, don't wait until the CPU reaches 95% usage. Instead, trigger scaling at around 65% to allow sufficient time for new instances to be allocated and initialized during the scaling process. However, this strategy might lead to unused capacity.
>
>   We recommend that you combine and balance mechanisms for scaling up and scaling out. For example, an app can scale up for some time and then scale out as necessary. Explore high tiers that offer large capacity and efficient resource usage. Based on usage patterns, higher Premium tiers are often more cost effective because they're more capable.
>
> - **Optimize environment costs**: Consider the Basic or Free tier to run pre-production environments. These tiers are low performance and low cost. If you use the Basic or Free tier, use governance to enforce the tier, constrain the number of instances and CPUs, restrict scaling, and limit log retention.
>
> - **Implement design patterns**: This strategy reduces the volume of requests that your workload generates. Consider using patterns like [the Backends for Frontends pattern](/azure/architecture/patterns/backends-for-frontends) and [the Gateway Aggregation pattern](/azure/architecture/patterns/gateway-aggregation), which can minimize the number of requests and reduce costs.
>
> - **Regularly check data-related costs**: Extended data retention periods or expensive storage tiers can lead to high storage costs. More expenses can accumulate due to both bandwidth usage and prolonged retention of logging data.
>
>   Consider implementing caching to minimize data transfer costs. Start with local in-memory caching, and then explore distributed caching options to reduce the number of requests to the back-end database. Consider the bandwidth traffic costs that are associated with cross-region communication if your database is located in a different region.
>
> - **Optimize deployment costs**: Take advantage of deployment slots to optimize costs. The slot runs in the same compute environment as the production instance. Use them strategically for scenarios like blue-green deployments that switch between slots. This approach minimizes downtime and ensures smooth transitions.
>
>   Use deployment slots with caution. You can introduce problems, such as exceptions or memory leaks, that might affect both the existing instances and new instances. Make sure that you thoroughly test changes. For operational guidance, see [Operational Excellence](#operational-excellence).

##### Recommendations

|Service or plan | Recommendation|Benefit|
|------------------------------|-----------|------------------|
|App Service plan |[Choose Free or Basic tiers](/azure/app-service/overview-hosting-plans) for lower environments. We recommend these tiers for experimental use. Remove the tiers when you no longer need them.|The Free and Basic tiers are budget-friendly compared to higher tiers. They provide a cost-effective solution for nonproduction environments that don't need the full features and performance of premium plans.|
|App Service plan | Take advantage of discounts and explore preferred pricing for: <br> - Lower environments with [dev/test plans](https://azure.microsoft.com/pricing/offers/dev-test/). <br> - [Azure reservations](/azure/app-service/overview-manage-costs#azure-reservations) and [Azure savings plans](https://azure.microsoft.com/pricing/offers/savings-plan-compute/#Benefitsandfeatures) for dedicated compute that you provision in the Premium V3 tier and App Service Environment. <br><br> Use reserved instances for stable workloads that have predictable usage patterns. |Dev/test plans provide reduced rates for Azure services, which makes them cost-effective for nonproduction environments. <br><br> Use reserved instances to prepay for compute resources and get significant discounts.|
|App Service | [Monitor costs](/azure/app-service/overview-manage-costs#monitor-costs) that App Service resources incur. Run the cost analysis tool in the Azure portal. <br><br> [Create budgets and alerts](/azure/app-service/overview-manage-costs#create-budgets) to notify stakeholders.|You can identify cost spikes, inefficiencies, or unexpected expenses early on. This proactive approach helps you to provide budgetary controls to prevent overspending.|
|App Service plan |Scale in when demand decreases. To scale in, define scale rules to [reduce the number of instances in Azure Monitor](/azure/azure-monitor/autoscale/autoscale-understanding-settings).|Prevent wastage and reduce unnecessary expenses.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

##### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Web Apps.

> [!div class="checklist"]
>
> - **Manage releases**: Use deployment slots to manage releases effectively. You can deploy your application to a slot, perform testing, and validate its functionality. After verification, you can seamlessly move the app to production. This process doesn't incur extra costs because the slot runs in the same virtual machine (VM) environment as the production instance.
>
> - **Run automated tests**: Before you promote a release of your web app, thoroughly test its performance, functionality, and integration with other components. Use [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing), which integrates with Apache JMeter, a popular tool for performance testing. Explore automated tools for other types of testing, such as Phantom for functional testing.
>
> - **Deploy immutable units**: Implement the [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp) to compartmentalize App Service into an immutable stamp. App Service supports the use of containers, which are inherently immutable. Consider [custom containers](/azure/app-service/configure-custom-container) for your App Service web app.
>
>    Each stamp represents a self-contained unit that you can quickly scale out or scale in. Units that are based on this stamp are temporary and stateless. Stateless design simplifies operations and maintenance. This approach is ideal for mission-critical applications. For an example, see [Mission-critical baseline with App Service](/azure/architecture/guide/networking/global-web-applications/mission-critical-app-service).
>
>   Use an infrastructure as code (IaC) technology, such as Bicep, to stamp out units with repeatability and consistency.
>
> - **Keep production environments safe**: Create separate App Service plans to run production and pre-production environments. Don't make changes directly in the production environment to ensure stability and reliability. Separate instances allow flexibility in development and testing before you promote changes to production.
>
>   Use low environments to explore new features and configurations in an isolated manner. Keep development and test environments ephemeral.
>
> - **Manage certificates**: For custom domains, you need to manage TLS certificates.
>
>   Have processes in place to procure, renew, and validate certificates. Offload these processes to App Service if possible. If you use your own certificate, you must manage its renewal. Choose an approach that best aligns with your security requirements.

|Service or plan|Recommendation|Benefit|
|------------------------------|-----------|------- |
|App Service | [Monitor the health of your instances](/azure/app-service/monitor-instances-health-check) and activate instance health probes. <br><br>Set up a specific path for handling health probe requests. |You can detect problems promptly and take necessary actions to maintain availability and performance.|
|App Service | [Enable diagnostics logs](/azure/app-service/troubleshoot-diagnostic-logs) for the application and the instance. <br><br> Frequent logging can slow down the performance of the system, add to storage costs, and introduce risk if you have unsecure access to logs. Follow these best practices: <br> - Log the right level of information. <br> - Set retention policies. <br> - Keep an audit trail of authorized access and unauthorized attempts. <br>- Treat logs as data and apply data-protection controls. |Diagnostic logs provide valuable insights into your app's behavior. Monitor traffic patterns and identify anomalies.|
|App Service | Take advantage of [App Service managed certificates](/azure/app-service/configure-ssl-certificate#create-a-free-managed-certificate) to offload certification management to Azure. |App Service automatically handles processes like certificate procurement, certificate verification, certificate renewal, and importing certificates from Key Vault. Alternatively, upload your certificate to Key Vault and authorize the App Service resource provider to access it. |
|App Service plan | [Validate app changes in the staging slot](/azure/app-service/deploy-staging-slots) before you swap it with the production slot. |Avoid downtime and errors. <br><br> Quickly revert to the last-known good state if you detect a problem after a swap.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for Web Apps.

> [!div class="checklist"]
>
> - **Identify and monitor performance indicators**: Set targets for the key indicators for the application, such as the volume of incoming requests, time that the application takes to respond to requests, pending requests, and errors in HTTP responses. Consider key indicators as part of the performance baseline for the workload.
>
>   Capture App Service metrics that form the basis of performance indicators. Collect logs to gain insights into resource usage and activities. Use application performance monitoring (APM) tools, such as Application Insights, to collect and analyze performance data from the application. For more information, see [App Service monitoring data reference](/azure/app-service/monitor-app-service-reference).
>
>   Include code-level instrumentation, transaction tracing, and performance profiling.
>
> - **Assess capacity**: Simulate various user scenarios to determine the optimal capacity that you need to handle expected traffic. Use Load Testing to understand how your application behaves under different levels of load.
>
> - **Select the right tier**: Use dedicated compute for production workloads. Premium tiers offer larger SKUs with increased memory and CPU capacity, more instances, and more features, such as zone redundancy. For more information, see [Premium V3 pricing tier](/azure/app-service/overview-hosting-plans#premium-v3-pricing-tier).
>
> - **Optimize your scaling strategy**: When possible, use [autoscaling](/azure/azure-monitor/autoscale/autoscale-overview) instead of manually adjusting the number of instances as application load changes. With autoscaling, App Service adjusts server capacity based on predefined rules or triggers. Make sure you do adequate performance testing and set the right rules for the right triggers.
>
>    If you prioritize simplicity during the initial setup, use an autoscaling option that doesn't require you to define rules and you only have to set limits.
>
>   Have sufficient resources readily available to ensure optimal performance. Allocate resources appropriately to maintain performance targets, such as response time or throughput. Consider overallocation of resources when necessary.
>
>    When you define autoscale rules, account for the time that it takes for your application to initialize. Consider this overhead when you make all scaling decisions.
>
> - **Use caching**: Retrieving information from a resource that doesn't change frequently and is expensive to access affects performance. Complex queries, including joins and multiple lookups, contribute to runtime. Perform caching to minimize the processing time and latency. Cache query results to avoid repeated round trips to the database or back end and reduce processing time for subsequent requests.
>
>   For more information about using local and distributed cache in the workload, see [Caching](/azure/architecture/best-practices/caching).
>
> - **Review the performance antipatterns**: To make sure the web application performs and scales in accordance with your business requirements, avoid the [typical antipatterns](/azure/architecture/antipatterns/). Here are some antipatterns that App Service corrects.
>
>   |Antipattern|Description|
>   |------------------------------|-----------|
>   |[Busy Front End](/azure/architecture/antipatterns/busy-front-end/)|Resource-intensive tasks can increase the response times for user requests and cause high latency. <br>Move processes that consume significant resources to a separate back end. Use a message broker to queue resource-intensive tasks that the back end picks up to asynchronously process.|
>   |[No Caching](/azure/architecture/antipatterns/no-caching/)|Serve requests from an intermediate cache in front of the back-end database to reduce latency. |
>   |[Noisy Neighbor](/azure/architecture/antipatterns/noisy-neighbor/noisy-neighbor)| Multitenant systems share resources between tenants. The activity of one tenant can have a negative effect on another tenant's use of the system. App Service Environment provides a fully isolated and dedicated environment to run App Service apps.|

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|[Enable the *Always On* setting](/azure/app-service/configure-common) when applications share a single App Service plan. App Service apps automatically unload when idle to save resources. The next request triggers a cold start, which can cause request timeouts. | The application is never unloaded with Always On enabled.|
|[Consider using HTTP/2](/azure/app-service/configure-common) for applications to improve protocol efficiency. | Choose HTTP/2 over HTTP/1.1 because HTTP/2 fully multiplexes connections, reuses connections to reduce overhead, and compresses headers to minimize data transfer.|

## Tradeoffs

You might have to make design tradeoffs if you use the approaches in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: **Density**

Colocate multiple apps within the same App Service plan to minimize resources. All apps share resources like CPU and memory, which can save money and reduce operational complexity. This approach also optimizes resource usage. Apps can use idle resources from another app if load patterns change over time.

  Also consider the disadvantages. For example, spikes in usage or instability of an app can affect the performance of other apps. Incidents in one app can also permeate to other apps within the shared environment, which can affect security.

:::image type="icon" source="../_images/trade-off.svg"::: **Isolation**

Isolation helps to avoid interference. This strategy applies to security, performance, and even segregation of development, testing, and production environments.

  App Service Environment provides better control over security and data protection because each app can have its own security settings. Your environment can contain breaches because isolation limits the blast radius. Resource contention is minimized from a performance perspective. Isolation allows for independent scaling based on specific demand and individual capacity planning.

  As a disadvantage, this approach is more expensive and requires operational rigor.

:::image type="icon" source="../_images/trade-off.svg"::: **Reliable scaling strategy**

A well-defined scaling strategy ensures that your application can handle various loads without compromising performance. However, there are tradeoffs on cost. Scaling operations take time. When new resources are allocated, the application must be properly initialized before it can effectively process requests. You can overprovision resources (prewarm instances) to provide a safety net. Without that extra capacity, during the initialization phase, there might be a delay in serving requests, which affects user experience. Autoscaling operations might trigger early enough to enable proper resource initialization by the time customers use the resources.

  As a disadvantage, overprovisioned resources cost more. You're charged per second for every instance, including prewarmed instances. Higher tiers include prewarmed instances. Determine whether capabilities with more expensive tiers are worth the investment.

:::image type="icon" source="../_images/trade-off.svg"::: **Building redundancy**

Redundancy offers resiliency but also incurs costs. Service level objectives (SLOs) for your workload determine acceptable performance thresholds. It becomes wasteful if redundancy exceeds SLO requirements. Evaluate whether excess redundancy improves SLOs or adds unnecessary complexity.

  Also consider the disadvantages. For example, multi-region redundancy provides high availability but adds complexity and cost due to data synchronization, failover mechanisms, and inter-region communication. Determine if zone redundancy can meet your SLOs.

## Azure policies

Azure provides an extensive set of built-in policies related to App Service and its dependencies. A set of Azure policies can audit some of the preceding recommendations. For example, you can check whether:

- Proper network controls are in place. For example, you can incorporate network segmentation by placing App Service in Azure Virtual Network through virtual network injection to have greater control over network configuration. The application doesn't have public endpoints and connects to Azure services through private endpoints.

- Identity controls are in place. For example, the application uses managed identities to authenticate itself against other resources. App Service built-in authentication verifies incoming requests.

- You can disable features, such as remote debugging and basic authentication, to reduce the attack surface.

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

  - If you've never deployed a web app, see [Basic web application](/azure/architecture/web-apps/app-service/architectures/basic-web-app).
  
  - For a foundational architecture as your starting point for a production-grade deployment, see [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).
  
- Use the following product documentation to build your implementation expertise:

  - [App Service](/azure/app-service/)
  
  - [App Service plan](/azure/app-service/overview-hosting-plans)
