---
title: Azure Well-Architected Framework - App Service (Web Apps)
description: Design considerations and recommendations about App Service (Web Apps).
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 02/25/2024
ms.topic: conceptual
products:
  - azure-app-service
categories:
  - compute
---

# Azure Well-Architected Framework perspective on App Service (Web Apps)

Azure App Service is a type of Platform-as-a-Service (PaaS) compute service that allows you to host your workload on the Azure platform. It's a fully managed service that abstracts the underlying compute and offloads the responsibility of building, deploying, and scaling to the platform. An app service always runs in an App Service plan. The choice of plan defines the region in which the workload runs, the type of operating system, and compute configurations, with various billing models.

This article assumes that as an architect, you've reviewed the [**compute decision tree**](/azure/architecture/guide/technology-choices/compute-decision-tree) and chosen App Service as the compute to run your workload. 

> [!IMPORTANT]
>
> **How to use this guide**
>
> The guidance in this article provides architectural recommendations that are mapped to the principles of the [**Azure Well-Architected Framework pillars**](../pillars.md).
>
> Each section has a _design checklist_ that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
> Also included are _recommendations_ on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure App Service for Web Apps and their dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments. 
>
> Foundational architecture that demonstrates the key recommendations: [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).


##### Technology scope

This review focuses on the interrelated decisions for these Azure resources.  

- Azure App Service Plan
- Azure App Service for Web Apps

There are other Azure offerings associated with App Service, such as Functions and Logic Apps. Those offerings are out of scope for this article.


## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

##### Design checklist

Start your design strategy based on the [**design review checklist for Reliability**](../reliability/checklist.md) and determine its relevance to your business requirements while keeping in mind the tiers and features of App Service and their dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Prioritize user flows**. Not all flows are equally critical. Assign priorities to each flow to guide your design decisions. User flow design will influence the choice of service tiers and instances on the App Service Plan and configurations. 
>
>     For example, your application might include a frontend and backend tiers that communicate through a message broker. You might choose to segment them in multiple Web Apps to allow for independent scaling, lifecycle management, and maintanenance. Also, placing a large application in a single plan can lead to memory of CPU issues, which will ultimately impact reliability.
>
>   You may need additional instances on the frontend for optimal performance on the UI side. However, the backend may not require the same number of instances. 
>
> - **Anticipate potential failures and have corresponding mitigation strategies**. Here are some examples of failure mode analysis:
>
>   |Failure|Mitigation|
>   |---|---|
>   |Failure of underlying  or abstracted components of App Service.| Have component redundancy in instances and dependencies. Monitor the health of instances, network, and storage performance.|
>   |Failure of external dependencies. | Use design patterns such as retry mechanisms, circuit breakers, and others. Monitor those dependencies and set appropriate timeouts.
>   |Failure due to traffic getting routed to unhealthy instances.| Monitor instance health taking into consideration responsiveness and avoid sending requests to unhealthy instances. |  
>
>   For more information, see  [Failure mode analysis for Azure applications](/azure/architecture/resiliency/failure-mode-analysis).
>
> - **Build redundancy** in the application and supporting infrastructure. Spread instances across availability zones to improve fault tolerance. If one zone fails, traffic can be routed to other zones. Deploying your application across multiple regions ensures that even if an entire region experiences an outage, your app remains accessible. 
>
>   Build similar level of redundancy in dependent services. For instance,  the application instances bind to blob storage. If the application uses zone-redundant deployment (ZRS), consider configuring the associated storage account with ZRS as well.
> 
>   Have redundancy in networking components. For example, use zone-redundant IP addresses and load balancers.
>
> - **Have a reliable scaling strategy**. Unexpected load on the application can cause it to become unreliable. Consider the right scaling approach based on your workload characteristics. You might be able to handle the load by scaling up. However, if the load continues to increase, scale out to new instances. Prefer automatic scaling over manual approaches. Always maintain a buffer of extra capacity during scaling operations to prevent performance degradation.  
>
>   The choice of [App Service plan tier has a direct impact on scaling](/azure/app-service/overview-hosting-plans#how-does-my-app-run-and-scale) in terms of number of instances and the compute units. 
> 
>   Ensure proper app initialization, so that new instances are warmed up quickly and can start receiving requests.
>
>   Strive for stateless applications, as much as possible. Reliably scaling state with new instances can add complexity. If you need to store application state, consider an external data store that can be scaled independently. Storing session state in memory can result in losing session state when there's a problem with the application or App Service. It also limits the possibility of spreading the load over other instances.
>
>   Regularly test your autoscaling rules. Simulate load scenarios to verify that your app scales as expected. Also log scaling events so you can troubleshoot issues that may arise and optimize your scaling strategy over time.
>
>   Azure App Service has a limitation on the number of instances within a plan, affecting reliable scaling. One strategy is to use identical deployment stamps, each running App Service Plan instance with its own endpoint. So, it's essential to front all stamps with an external load balancer to distribute traffic among them. For single region deployments, consider Azure Application Gateway (for multi regional, Azure Front Door is recommended). This approach is ideal for mission-critical applications where reliability is crucial. It's demonstrated in the [Mission-critical baseline with App Service](/azure/architecture/guide/networking/global-web-applications/mission-critical-app-service).
>
>   The App Service Plan distributes traffic among instances and monitors their health. Note that if one instance fails, the external load balancer might not immediately detect it. 
>
>  - **Plan your recoverability**. Redundancy is a crucial for business continuity. Fail over to another instance if one becomes unreachable. Also, explore autohealing capabitlites offered by App Service, such as auto repair of instances. 
>
>     Implement design patterns to handle graceful degradation for both transient failures, such as network connectivity issues, and large-scale events like regional outages. For example, 
>     - Bulkhead pattern segments your application into isolated groups to  prevent a failure from affecting the entire system.
>     - Queue-Based Load Leveling pattern queues work items that serve as a buffer to smooth out traffic spikes.
>     - Retry patterns handles transient failures caused by network glitches, dropped database connections, or busy services.
>     - Circuit Breaker pattern to prevent an application from repeatedly trying to execute an operation that's likely to fail.
>    Web Jobs allow you to run background tasks in your Web App. To run those tasks reliably, ensure that the app hosting your job is set to run continuously, on a schedule, or based on event-driven triggers.
>
>     For more information, see [Reliability patterns](/azure/well-architected/reliability/design-patterns).
>
> - **Conduct reliability testing**. Conduct load testing to evaluate your application's reliability and performance under load. Test plans should include scenarios that validate your automated recovery operations.
>
>   Use fault injection to intentionally introduce failures and validate your self-healing and self-preservation mechanisms. Explore the [fault library provided by Azure Chaos Studio](/azure/chaos-studio/chaos-studio-fault-library). 
>
>    Azure App Service imposes resource limits on hosted apps. These limits are determined by the associated App Service plan. Make sure your tests validate that the app runs within those limits, For more information, [Azure subscription and service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits?branch=main#app-service-limits).
>
> - **Use health probes to identify unresponsive workers**. App Service has built-in capabilities that periodically ping a specific path of your web application. If an instance is unresponsive, it's removed from the load balancer and replaced with a new instance.

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|(App Service Plan) Choose the Premium tier of App Service Plan for production workloads. <br><br> Set the maximum and minimum number of workers according to your capacity planning. <br><br>For more information, see[ Azure App Service plan overview](/azure/app-service/overview-hosting-plans). |Premium App Service Plan offers advanced scaling features and ensures redundancy if failures occur.|
|(App Service Plan) [**Enable zone redundancy**](/azure/reliability/reliability-app-service#availability-zone-support).<br><br> Consider provisioning more than three instances to enhance fault tolerance. <br><br> Check regional support for zone redundancy because not all regions offer this feature. | With multiple instances spread across zones, your application can withstand failures in a single zone. If one zone becomes unavailable, traffic automatically shifts to healthy instances in other zones, maintaining application reliability.|
|(App Service) **Consider disabling Application Request Routing (ARR) Affinity**. ARR Affinity creates sticky sessions, which is used to redirect users to the same node that handled their previous requests.|By disabling ARR Affinity, incoming requests are evenly distributed across all available nodes preventing any single node from being overwhelmed by traffic. If a node is unavailable, requests can be seamlessly redirected to other healthy nodes. <br> By avoiding session affinity, your App Service remains stateless, reducing complexity and ensuring consistent behavior across nodes. <br> Removing sticky sessions also helps App Service to easily scale horizontally by adding or removing instances.|
|(App Service) [**Define autohealing rules**](/azure/app-service/overview-diagnostics#auto-healing) based on request count, slow request, memory limit, and other indicators that are considered as part of your performance baseline. <br>Consider this configuration as part of your scaling strategy. <br> |Autohealing rules allow your application to recover automatically from unexpected issues. The configured rules trigger healing actions when thresholds are breached. <br> Autohealing enables proactive maintenance that's handled automatically. |
|(App Service) [**Enable Health Check**](/azure/app-service/monitor-instances-health-check) and provide a path that responds to the health check requests. <br> |Health checks allow early detection of issues. When a health check request fails, the system can automatically take corrective actions. <br> The load balancer can route traffic away from unhealthy instances, ensuring that users are directed to healthy nodes.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around hosting on App Service.

##### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review the security baselines** for [App Service](/security/benchmark/azure/baselines/app-service-security-baseline) to enhance the security posture of your application hosted on an App Service plan.
>
> - **Use the latest runtime and libraries** in your application. Prior to updating, thoroughly test your application builds to catch issues early and ensure a smooth transition to the new version. App Service supports [language runtime support policy](/azure/app-service/language-support-policy) for updating existing stacks and retiring end-of-support stacks.
>
> - **Create segmentation through isolation boundaries to contain breach**. Apply _segmentation of identity_, for example, implement role-based access control (RBAC) to assign specific permissions based on roles. Follow the principle of least privilege to limit access rights to only what's necessary. Also create _segmentation at the network level_. For example, [inject App Service Apps in an Azure Virtual Network](/azure/app-service/web-sites-integrate-with-vnet) for isolation and define network security groups to filter traffic.
>
>   App Service Plan offers the App Service Environment (ASE) tier that provides a higher degree of isolation. With ASE, you get dedicated compute and network.  
>
> - **Apply access controls on identities** to restrict both _inward access to the Web App_ and _outward access from the Web App_ to other resources. This helps maintain the overall workload's security posture. 
>
>   _Use Microsoft Entra ID_ for all authentication and authorization needs. Use built-in roles, such as  [Web Plan Contributor](/azure/role-based-access-control/built-in-roles/web-and-mobile#web-plan-contributor), [	Website Contributor](/azure/role-based-access-control/built-in-roles/web-and-mobile#website-contributor), [Generic: Contributor, Reader, Owner](/azure/role-based-access-control/built-in-roles#general).
> 
> - **Control network traffic to and from the application**. Don't expose application endpoints to the public internet. Instead add a private endpoint on the Web App, that's placed in a dedicated subnet. Front your application with a reverse proxy that communicates with that private endpoint. Consider using Azure Application Gateway or Front Door for that purpose.
>
>   Deploy web application firewall (WAF) to protect against common vulnerabilities. Both Application Gateway and Front Door have integrated WAF capabilities.
>
>   Configure the reverse proxy rules and network settings appropriately to achieve the desired level of security and control. For example, add network security group (NSG) rules on the private endpoint subnet to only accept traffic from the reverse proxy.
>
>     Egress traffic from the application to other PaaS services should be over _private endpoints_. Consider placing a firewall component to restrict egress traffic to the public internet. Both approaches prevent data exfiltration.
>
>   For a comprehensive view, see [App Service networking features](/azure/app-service/networking-features).
>
> - **Encrypt data**. Protect data in transit with end-to-end TLS. Use full encryption for data at rest by using your keys. For more information, see [Encryption at rest using customer-managed keys](/azure/app-service/configure-encrypt-at-rest-using-cmk).
>
>   Don't use legacy protocols such as TLS 1.0 and 1.1. By default, 1.2 is enabled by App Service. For more information, see [Azure App Service TLS overview](/azure/app-service/overview-tls).
>   
>   All instances of your App Service are assigned a default domain name. Use a custom domain and secure that domain with certificates. 
>   
> - **Reduce the attack surface**. Remove default configurations that you don't need. For example, disable remote debugging, local authentication for SCM sites, basic authentication, and so on. Also disable protocols like HTTP, FTP, and others that aren't secure. Enforce configurations through Azure policies. For more information, see [Azure Policies](#azure-policies).
>
>   Implement **restrictive Cross-Origin Resource Sharing (CORS) policies** in your web application that only allows it to accept requests from the allowed domains, headers, and other criteria. Enforce CORS policies with built-in Azure policy definitions.   
>
> - **Protect application secrets**. You often need to handle sensitive information like API keys, or authentication tokens. Instead of hardcoding these secrets directly into your application code or configuration files, you can use Key Vault references in app settings. When the application starts, App Service automatically retrieves the secret values from Key Vault using the app's managed identity. 
>
> - **Enable resource logs for our application**. By doing so, you can create comprehensive activity trails that provide valuable during investigations following security incidents.
>
>   When assessing threats, consider logging as part of your threat modeling process.


##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|(App Service) [**Assign managed identity**](/azure/app-service/overview-managed-identity) to the Web App. <br><br> Don't share or reuse identities among applications to maintain isolation boundaries. <br><br>If you're deploying using containers, make sure that you [connect securely to your container registry](/azure/app-service/tutorial-custom-container).|Outward communication from the application is authenticated, such as the application retrieving secrets from Key Vault. The identity is managed by Azure and doesn't require you to provision or rotate any secrets. <br> Creating distinct identities allows for granularity of control. It also makes revocation easy in case an identity is compromised.|
|(App Service) Configure [custom domains](/azure/app-service/configure-ssl-bindings) for application. <br><br> Disable HTTP and only accept HTTPS requests.|Custom domains enable secure communication through HTTPS (SSL/TLS), ensuring the protection of sensitive data and building user trust.|
|**Evaluate whether [Easy Auth](/azure/app-service/overview-authentication-authorization)** is the right mechanism to authenticate users accessing your application. </br> It's a feature of App Service that's integrated with Microsoft Entra ID and handles token validation and user identity management across multiple login providers and also supports OpenID Connect. <br><br> Be aware that with this feature, you lose the capability to do authorization at a granular level. Also, there's no mechanism to test authentication. |This feature removes the need and complexity of using authentication libraries in application code. When a request reaches the application, the user is already authenticated.|
|(App Service) Configure the application for [vNet integration](/azure/app-service/overview-vnet-integration). <br><br>  **[Use private endpoints for App Service apps](/azure/app-service/overview-private-endpoint)**. Block all traffic from the public traffic. <br><br>Make sure [container image is pulled through the virtual network](/azure/app-service/configure-vnet-integration-routing#container-image-pull) integration.<br><br> All [outgoing traffic from the application](/azure/app-service/configure-vnet-integration-routing#configure-application-routing) passes through the virtual network.|Brings the security benefits of using an Azure Virtual Network, such as the application can securely access resources within the network. <br> <br> Adding a private endpoint helps protect your application by limiting direct exposure to the public network and allowing controlled access through the reverse proxy.|
|(App Service) Provide hardening by: <br> - [**Disabling basic authentication**](/azure/app-service/configure-basic-auth-disable) that uses username/password in favor of Microsoft Entra ID-based authentication. <br> - **Turning off remote debugging** so that inbound ports aren't opened.<br> - Enable [CORS policies](/azure/app-service/app-service-web-tutorial-rest-api) to tighten incoming requests. <br> - Disable protocols, such as [FTP](/azure/app-service/configure-common). |Basic authentication isn't recommended as a secure deployment method. Microsoft Entra employs OAuth 2.0 token-based authentication, offering numerous advantages and enhancements that address the limitations associated with basic authentication. <br><br> The policies restrict access to application resources, only allows requests from specific domains, secures cross-region requests, and so on. |
|(App Service)[**Always use Key Vault references as app settings**](/azure/app-service/app-service-key-vault-references). <br> |Secrets are kept separate from your app's configuration. App settings are encrypted at rest. Also, secret rotations are managed by App Service. |
|[**Enable Defender for Cloud and App Service**](/azure/defender-for-cloud/tutorial-enable-app-service-plan).| Provides real-time protection to resources running in App Service Plan against threats and enhances our overall security posture.|
|[**Enable diagnostic logging**](/azure/app-service/troubleshoot-diagnostic-logs) and add instrumentation to your app. <br> The logs are sent to Storage Accounts, Event Hubs and Log Analytics.<br>  For information about audit log types, see [Supported log types](/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types).|Logging is a common way to capture access patterns. It records relevant events that provide valuable insights into how users interact with an application or platform. This information is crucial for accountability, compliance, and security purposes.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to your web app and the environment in which they run.

##### Design checklist

Start your design strategy based on the [**design review checklist for Cost Optimization**](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Estimate the initial cost**. As part of your cost modeling exercise, use the [pricing calculator](https://azure.microsoft.com/pricing/calculator) to evaluate the approximate costs associated with various tiers based on the number of instances you plan to run. Each App Service tier offers different compute options. 
>   Continuously monitor the cost model to track expenditure.
>
> - **Evaluate the discounted options**. Higher tiers include dedicated compute instances. You can apply the reservation discount if your workload has a predictable and consistent usaeg pattern. To make the right choice on the type of reservation, make sure you analyze usage data. For more information, see [Save costs with Azure App Service reserved instances](/azure/cost-management-billing/reservations/prepay-app-service).
>
> - **Have a good understanding of the usage meters**. To Do. 
>
> - **Consider the tradeoffs between density and isolation**. App Service Plans allow you to host multiple applications on the same compute, sharing environments to save costs. For guidance, see [Tradeoffs](#tradeoffs).
>
> - **Evaluate the impact of your scaling strategy on cost**. When implementing autoscaling, it's crucial to properly design, test, and configure not only for scaling out but also for scaling in. So, be precise about maximum and minimum limits on autoscaling.
>
>   For reliable scaling, initialize the application proactively. For example, rather than waiting until the CPU reaches 95% utilization, trigger scaling at around 65%, to allow for sufficient time for new instances to be allocated and initialized during the scaling process. However, that might lead to  unused capacity.
>
>   It's recommended that you combine and balance scaling up and scaling out mechanisms. For example, the app can scale up for some time and then scale out as necessary. It's worth exploring higher tiers because they offer larger capacity and efficient resource utilization. Based on usage patterns, higher Premium tiers might be cost effective because they are more capable.
>
> - **Optimize environment costs.**  Consider Basic or Free tier for running preproduction environments. These are low performance tiers that are less expensive. If these tiers are suitable, use governance to enforce those tiers, constrain the number of instances and CPUs, restrict scaling, and limit log retention, other possible configurations. 
>
> - **Implement design patterns.** This strategy reduces the volume of requests your workload generates. Consider using patterns like [Backend for Frontends](/azure/architecture/patterns/backends-for-frontends) and [Gateway Aggregation](/azure/architecture/patterns/gateway-aggregation), which can reduce costs by minimizing the number of requests.
>
> - **Regularly check costs related to data**. Extending data retention periods or opting for more expensive storage tiers may lead to higher storage costs. Additionally, expenses can accumulate due to both bandwidth usage and prolonged retention of logging data.
>
>   Consider implementing caching to minimize data transfer costs. Start with local in-memory caching, and then explore distributed caching options to reduce the number of requests to the backend database. Additionally, if your database is located in a different region, be mindful of the bandwidth traffic costs associated with cross-region communication.
>
> - **Optimize deployment costs**. By leveraging deployment slots, you can optimize costs because the slot runs in the same compute environment as the production instance. Use them strategically for scenarios like blue-green deployments, where you switch between slots to minimize downtime and ensure smooth transitions.
>
>   Use deployment slots  with caution. You can introduce issues, such as exceptions or memory leaks, can impact both the existing and new instances. Make sure changes are thoroughly tested. For operational guidance, see [Operational Excellence](#operational-excellence).



##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|**[Choose Free or Basic tiers](/azure/app-service/overview-hosting-plans) for lower enviroments**. <br> These tiers are recommended for experimental use. Remove them when no longer needed.|The Free and Basic tiers are more budget-friendly compared to higher tiers. They provide a cost-effective solution for nonproduction environments where you don't need the full features and performance of premium plans.|
|(App Service Plan) Allow for independent scaling of applications by enabling [per-app scaling](/azure/app-service/manage-scale-per-app). <br><br> Each app runs in its own isolated process, preventing resource contention.| Instead of provisioning resources for the highest peak load across all apps, you allocate resources based on actual usage patterns. By avoiding over-provisioning, you save costs.|
|Take advantage of discounts and explore these preferred pricing for : <br> - Lower environments with [dev/test plans](https://azure.microsoft.com/pricing/offers/dev-test/). <br> - [Azure reservations](/azure/app-service/overview-manage-costs#azure-reservations) and [Azure Savings Plan](https://azure.microsoft.com/pricing/offers/savings-plan-compute/#Benefitsandfeatures) for dedicated computed provisioned in Premium V3. <br><br> Reserved instances are ideal for stable workloads with predictable usage patterns. |Dev/Test plans provide reduced rates for Azure services, making them cost-effective for non-production environments. <br> Reserved instances allow you to prepay for compute resources with significant discounts.|
|[**Monitor cost**](/azure/app-service/overview-manage-costs#monitor-costs) incurred by App Service resources. Run the Cost Analysis tool in Azure portal. <br><br> [**Create budgets and alerts**](/azure/app-service/overview-manage-costs#create-budgets)  to notify stakeholders.|Monitoring allows you to identify cost spikes, inefficiencies, or unexpected expenses early on. This proactive approach helps you to provide budgetary controls to prevent overruns.|
|Scale in when demand decreases. <br><br> To scale-in, define scale rules to [reduce the number of instances in Azure Monitor](/azure/azure-monitor/autoscale/autoscale-understanding-settings).|By scaling in, you prevent wastage and reduce unnecessary expenses.|


## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

##### Design checklist

Start your design strategy based on the [**design review checklist for Operational Excellence**](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to web apps.

> [!div class="checklist"]
> - **Release Management**. Deployment slots allow you to manage releases effectively. You can deploy your application to a slot, perform testing, and validate its functionality. Once verified, you can seamlessly swap it into production. Importantly, this process doesn't incur additional costs because the slot runs in the same Virtual Machine (VM) environment as the production instance.
>
> - **Run automated tests**. Before promoting a release of your web application, thoroughly test its performance, functionality, integration with other components, and so on. Utilize [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing), which integrates with JMeter, a popular tool for performance testing. Explore automated tools for other types of testing, such as Phantom for functional testing.
>
> - **Deploy immutable units**. Implement the [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp) that compartmentalizes App Service into an immutable stamp. App Service supports the use of containers, which are inherently immutable. Consider your app services web app with [custom containers](/azure/app-service/configure-custom-container).
>
>    Each stamp represents a self-contained unit that can be quickly scaled out or scaled in. Units based on this stamp are temporary and stateless. Stateless design simplifies operations and maintenance. This approach is ideal for mission-critical applications and is demonstrated in the [Mission-critical baseline with App Service](/azure/architecture/guide/networking/global-web-applications/mission-critical-app-service).
>
>   Use an Infrastructure-as-Code (IaC) technology, such as Bicep to stamp out units with repeatability and consistency. 
>
> - **Keep production environments safe**. Create separate App Service Plans for running production and preproduction environments. Avoid making changes directly in the production environment to ensure stability and reliability. Separate instances allow flexibility in development and testing before promoting changes to production. 
>
>   Lower environments are recommended for exploration of new features and configurations in an isolated manner. Keep development and test environments ephemeral. 
>
> - **Certificate management**. For custom domains, you need to manage Transport Layer Security (TLS) certificates. 
> 
>   Have processes in place that procure, renew, validate certificates. Offloading this to Azure App Service is prefered. If you prefer to use your own certificate, you'll be responsible for managing its renewal independently. Choose an approach that best aligns with your security requirements.


|Recommendation|Benefit|
|------------------------------|-----------|
|(App Service) [**Monitor the health of your instances**](/azure/app-service/monitor-instances-health-check?tabs=dotnet) and activate instance health probes. <br><br>Set up a specific path for handling health probe requests. |You can detect issues promptly and take necessary actions to maintain agreed upon availability and performance.|
|(App Service) [**Enable diagnostics logs**](/azure/app-service/troubleshoot-diagnostic-logs) for the application and the instance. <br><br> Frequent logging can slow down the performance of the system, add to storage costs, and can be risky if access to logs isn't secured. Follow these best practices: <br> - Log the right level of information. <br>- Set retention policies. <br> - Keep audit trail of authorized access and unauthorized attempts. <br>- Treat logs as data and apply data protection controls. |Diagnostic logs provide valuable insights into your app’s behavior. Useful for monitoring traffic patterns and identifying anomalies.|
|(App Service) Take advantage of [App Service managed certificates](/azure/app-service/configure-ssl-certificate#create-a-free-managed-certificate) that offloads certification management to Azure. |Processes like certificate procurement, verification, renewal, importing certifcates from Azure Key Vault, and other processes are handled automatically by App Service. Alternatively, upload your certificate to key vault and authorize the App Service resource provider to access it. |
|(App Service Plan) [**Validate app changes in the staging slot**](/azure/app-service/deploy-staging-slots) before swapping it with the production slot. <br><br> Autoswap? |You can test different versions of code and configuration, and swap them with production without downtime or errors. <br><br> If an issue is detected after a swap, you can quickly revert to the last known good state by swapping back. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [**design review checklist for Performance Efficiency**](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for web apps.

> [!div class="checklist"]
> - **Identify and monitor the performance indicators**. Set targets for the key indicators for the application, such as the volume of incoming requests, time taken by the application to respond to requests, pending requests, errors in HTTP responses, and others. Those indicators must be considered as part of the performance baseline for the workload. 
>
>   Capture App Service metrics that form the basis of performance indicators. Also, collect logs to gain insights into resource usage and activities. Use Application Performance Monitoring (APM) tools, such as Application Insights that collect collect and analyze performance data from the application. For more information, see [Azure App Service monitoring data reference](/azure/app-service/monitor-app-service-reference).
>
>   Include code-level instrumentation, transaction tracing, and performance profiling.
>
> - **Capacity assessment**. By simulating various user scenarios, you can determine the optimal capacity needed to handle expected traffic. Use Azure Load Testing to understand how your application behaves under different levels of load.
>
> - **Select the right tier**. Dedicated compute is recommended for production workloads. Premium tiers offer larger SKUs with increased memory and CPU capacity, more instances, and features such as zone redundancy. For capabilities of higher tiers, see [Premium V3 pricing tier](/azure/app-service/overview-hosting-plans#premium-v3-pricing-tier).
>
> - **Optimize your scaling strategy**. Always opt for [automatic ways of scaling](/azure/azure-monitor/autoscale/autoscale-overview) instead of manually adjusting the number of instances as application load changes. With automatic scaling, App Service adjusts server capacity based on predefined rules or triggers. Make sure you do adequate performance testing and you then set the right rules for the right triggers. 
> 
>   There's another automatic scaling option that doesn't require you to define rules and only set limits. This is a good option for users who prioritize simplicity and initial setup.
>
>   To ensure optimal performance, sufficient resources should be readily available. To maintain performance targets, such as response time or throughput, allocate resources appropriately. Consider overallocating resources when necessary. 
>
>    When defining auto-scale rules, account for the time it takes for your application to initialize. Scaling decisions should consider this initialization overhead.
>
> - **Use caching**.  Retrieving information from a resource that doesn't change frequently and is expensive to access impacts performance. Complex queries, including joins and multiple lookups, contribute to execution time. Caching aims to minimize the processing time and latency. By caching query results, you avoid repeated round trips to the database or backend. Also, reduces processing time for subsequent requests.
>
>   For more information about using local and distributed cache in the workload, see [Caching](/azure/architecture/best-practices/caching).
>
> - **Review the performance antipatterns**. To make sure the web application performs and scales as your business requirements, avoid the [typical antipatterns](/azure/architecture/antipatterns/). Here are some antipatterns that are corrected by App Service.
>
>   |Antipattern|Description|
>   |------------------------------|-----------|
>   |[Busy Front End antipattern](/azure/architecture/antipatterns/busy-front-end/)|Resource-intensive tasks can increase the response times for user requests and cause high latency.  <br>Move processes that consume significant resources to a separate back end. Use a message broker to queue resource-intensive tasks that the backend picks up asynchronously processing.|
>   |[No Caching antipattern](/azure/architecture/antipatterns/no-caching/)|Reduce latency by serving requets from an intermediate cache in front of the backend database. |
>   |[Noisy Neighbor](/azure/architecture/antipatterns/noisy-neighbor/noisy-neighbor)| Multitenant systems share resources between tenants, the activity of one tenant can have a negative impact on another tenant's use of the system. The App Service Environment (ASE) tier provides a fully isolated and dedicated environment for running App Service apps.|

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|(App Service) [**Enable Always On**](/azure/app-service/configure-common) when applications share a single App Service Plan. <br><br> Azure App Service apps automatically unload when idle to save resources, the next request would trigger a cold start, potentially causing request timeouts. |With Always On enabled the application is never unloaded.|
|(App Service) [**Consider using HTTP/2**](/azure/app-service/configure-common) for applications to improve protocol efficiency. |HTTP/2 improves over HTTP/1.1 by fully multiplexing connections, reusing connections to reduce overhead, and compressing headers to minimize data transfer.|

## Tradeoffs

There are design tradeoffs with the approaches described in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: **Density and isolation**.

- **Higher density**. By colocating multiple apps within the same App Service Plan, you minimize the resources. All apps share CPU, memory, and so on. This can lead to cost savings and reduced operational complexity. Resource utilization is also optimized. If apps have different load patterns over time, idle resources from one app can be used by another.

  There are also disadvantages. Spikes in utilization or instability of an app can affect the performance of other apps. Security is also a concern. Because incidents in one app can permeate to other apps within the shared environment.

- **Higher isolation**. Isolation is intended to avoid interference. This strategy applies to security, performance, and even segregation of development, testing, and production environment.

  The App Service Environment (ASE) tier gives you better control over security and data protection as each app can have its own security settings. There's better containment of breaches because isolation limits the blast radius. From a performance perspective, resource contention is minimized. Also, isolation allows for independent scaling based on specific demand and individual capacity planning. 

  Consequently, this approach is more expensive and requires operational rigor.


:::image type="icon" source="../_images/trade-off.svg"::: **Reliable scaling strategy**.

A well-defined scaling strategy ensures that your application can handle varying loads without compromising performance. However, there are tradeoffs on cost.

Scaling operations take time. When new resources are allocated, the application must be properly initialized before it can effectively process requests. **Overprovisioning resources (prewarm instances)** provides a safety net. Without that extra capacity, during the initialization phase, there might be a delay in serving requests, impacting user experience. The triggers for auto scaling operations must signal early enough to enable proper resource initialization by the time the resources are used.

Overprovisioning leads to **higher costs**. You're charged per second for every instance, including prewarmed instances. Higher tiers include prewarmed instances. Determine whether capabilities offered with more expensive tiers are worth the investment.  

:::image type="icon" source="../_images/trade-off.svg"::: **Building redundancy**.

Redundancy offers resilience while also incurring costs. 

Service Level Objectives (SLOs) for your workload determine acceptable performance thresholds. **If redundancy exceeds what's necessary to meet SLOs, it becomes wasteful**. Evaluate whether additional redundancy significantly improves SLOs or merely adds unnecessary complexity.

For example, multi-region redundancy provides high availability. However, it's complex and costly due to data synchronization, failover mechanisms, and inter-region communication. Evaluate if your SLOs can be met with zonal redundancy.


## Azure policies

Azure provides an extensive set of built-in policies related to App Service and the dependencies. Some of the preceding recommendations can be audited through a set of Azure Policies. For example, you can check if:

- Proper network controls are in place. For example, network segmentation is done by placing App Service in Azure Virtual Network through VNet injection to have greater control over network configuration. The application doesn't have public endpoints and connects to Azure services through private endpoints.

- Identity controls are in place. For example, the application uses managed identities to authenticate itself against other resources. Incoming requests are verified using App Service Authentication (Easy Auth). 

- Attack surface is reduced by disabling features such as remote debugging and basic authentication.

For comprehensive governance, review the [Azure Policy built-in definitions](/azure/app-service/policy-reference) and other policies that might impact the security of the compute layer.


## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your web application instances.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Next steps

Consider these articles as resources that demonstrate the  recommendations highlighted in this article.

- Use these reference architectures as examples of how these recommendations can be applied to a workload.
  - If you have never deployed a Web Apps, start with [Basic web application](/azure/architecture/web-apps/app-service/architectures/basic-web-app).
  - Foundational architecture as your starting point for a production-grade deployment is described in [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).
- Build implementation expertise using product documentation: 
  - [Azure App Service](/azure/app-service/)
  - [Azure App Service plan](/azure/app-service/overview-hosting-plans)
