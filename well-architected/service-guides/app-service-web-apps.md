---
title: Architecture Best Practices for Azure App Service (Web Apps) 
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for the Web Apps feature of Azure App Service.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 09/12/2025
ms.topic: concept-article
ms.subservice: waf-service-guide
products:
  - azure-app-service
azure.category:
  - compute
---

# Architecture best practices for Azure App Service (Web Apps)

Azure App Service is a platform as a service (PaaS) that provides a fully managed hosting environment for building, deploying, and scaling web applications. As a PaaS solution, App Service abstracts the underlying infrastructure, which enables you to focus on your application development. The Web Apps feature of App Service runs your apps in the context of an App Service plan. This plan determines the resources, operating system, region, and pricing model (SKU) that's used to host your app.

This article assumes that as an architect, you've reviewed the [compute decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree) and chose App Service as the compute for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- App Service plans
- Web Apps

Other Azure offerings are associated with App Service, such as Azure Functions, Azure Logic Apps, and App Service Environment. Those offerings are out of scope for this article. App Service Environment is referenced occasionally to help clarify features or options of the core App Service offerings.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the tiers and features of App Service and its dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Prioritize user flows:** Not all flows are equally important. Identify the critical paths in your application and assign priorities to each flow to guide your design decisions. User flow design can influence which service tiers and the number of instances that you choose for an App Service plan and configuration.
>
>     For example, your application might include front-end and back-end tiers that communicate through a message broker. You might choose to segment the tiers in multiple web apps to allow for independent scaling, lifecycle management, and maintenance. If you place a large application in a single plan, it can result in memory or CPU problems that affect reliability.
>
>   You might need more instances on the front end for optimal performance on the UI side. However, the back end might not require the same number of instances.
>
> - **Anticipate potential failures:** Plan mitigation strategies for potential failures. The following table shows examples of failure mode analysis.
>
>   |Failure|Mitigation|
>   |---|---|
>   |Failure of underlying or abstracted App Service components| Have component redundancy in instances and dependencies. Monitor the health of instances, network performance, and storage performance.|
>   |Failure of external dependencies | Use design patterns such as the [Retry pattern](/azure/architecture/patterns/retry) and the [Circuit Breaker pattern](/azure/architecture/patterns/circuit-breaker). Monitor the external dependencies and set appropriate time-outs. |
>   |Failure because of traffic getting routed to unhealthy instances| Monitor instance health. Consider responsiveness, and avoid sending requests to unhealthy instances. |
>
>   For more information, see [Failure mode analysis for Azure applications](/azure/architecture/resiliency/failure-mode-analysis).
>
> - **Build redundancy:** Build redundancy in the application and supporting infrastructure. Spread instances across availability zones to improve fault tolerance. Traffic is routed to other zones if one zone fails. Deploy your application across multiple regions to help ensure that your app remains available, even if an entire region experiences an outage.
>
>   Build similar levels of redundancy in dependent services. For instance, application instances bind to blob storage. Consider configuring the associated storage account with zone-redundant storage if an application uses a zone-redundant deployment.
>
> - **Use multiple instances:** An immediate single-point-of-failure occurs if you run your app on only one instance. Allocate multiple instances to your app to help ensure high availability. If one instance fails, other instances can still handle incoming requests. Your app code should be able to handle multiple instances without synchronization issues when reading from data sources or writing to data sources.
>
>   Have redundancy in networking components. For example, use zone-redundant IP addresses and load balancers.
>
> - **Have a reliable scaling strategy:** Unexpected load on an application can make it unreliable. Consider the right scaling approach based on your workload characteristics. Horizontal scaling, or *scaling out*, allows you to add more instances to distribute the load. Vertical scaling, or *scaling up*, increases the capacity of an existing instance, such as CPU or memory. Be cautious of over-provisioning because adding unnecessary instances increases costs without tangible performance benefits.
>
>   You can sometimes scale up to handle the load. However, if the load continues to increase, scale out to new instances. Choose the automatic, or *autoscaling*, approach over the manual approach. Always maintain a buffer of extra capacity during scaling operations to prevent performance degradation.
>
>   The [App Service plan tier](/azure/app-service/overview-hosting-plans#how-does-my-app-run-and-scale) that you choose affects scaling in terms of the number of instances and the compute units.
>
>   Consider network protocol requirements when designing your scaling strategy, ensuring that both IPv4 and IPv6 traffic patterns are covered. App Service supports inbound IPv6 connectivity alongside IPv4. IPv6-only networks can overcome IPv4 address exhaustion issues. However, IPv6-only clients, require IPv4-to-IPv6 translation services when IPv6 is not enabled, creating potential network translation overhead. For more information, see [Inbound and outbound IP addresses in Azure App Service](/azure/app-service/overview-inbound-outbound-ips). 
>
> - **Ensure proper app initialization so that new instances warm up quickly and can receive requests.** Strive for stateless applications when possible. Reliably scaling state with new instances can increase complexity. Consider an external data store that you can scale independently if you need to store application state. Storing session state in memory can result in losing session state when there's a problem with the application or App Service. It also limits the possibility of spreading the load across other instances.
>
>    Regularly test your autoscaling rules. Simulate load scenarios to verify that your app scales as expected. You should log scaling events so that you can troubleshoot problems that might arise and optimize your scaling strategy over time.
>
>    App Service has a limitation on the number of instances within a plan, which can affect scaling reliability. To address this, one strategy is to deploy identical stamps, with each stamp running its own App Service plan instance and endpoint. It's essential that you front all stamps with an external load balancer to distribute traffic across them. Use Azure Application Gateway for single-zone deployments and Azure Front Door for multiple-region deployments. This approach is ideal for mission-critical applications when reliability is crucial. For more information, see [Mission-critical baseline with App Service](/azure/architecture/guide/networking/global-web-applications/mission-critical-app-service).
>
> - **Plan your recoverability:** Redundancy is crucial for business continuity. Fail over to another instance if one instance is unreachable. Explore automatic healing capabilities in App Service, such as automatic repair of instances.
>
>   Implement design patterns to handle graceful degradation for transient failures, such as network connectivity problems and large-scale events like regional outages. Consider the following design patterns:
>
>   - *The Bulkhead pattern* segments your application into isolated groups to prevent a failure from affecting the entire system.
>
>   - *The Queue-Based Load Leveling pattern* queues work items that serve as a buffer to smooth out traffic spikes.
>
>   - *The Retry pattern* handles transient failures because of network glitches, dropped database connections, or busy services.
>
>   - *The Circuit Breaker pattern* prevents an application from repeatedly trying to perform an operation that's likely to fail.
>
>   For more information, see [Architecture design patterns that support reliability](/azure/well-architected/reliability/design-patterns).
>
> - **Conduct reliability testing:** Conduct load testing to evaluate your application's reliability and performance under load. Test plans should include scenarios that validate your automated recovery operations.
>
>   Use fault injection to intentionally introduce failures and validate your self-healing and self-preservation mechanisms. For more information, see [Azure Chaos Studio fault and action library](/azure/chaos-studio/chaos-studio-fault-library).
>
>   App Service imposes resource limits on hosted apps. The App Service plan determines these limits. Make sure that your tests confirm that the app runs within those resource limits. For more information, see [App Service limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-app-service-limits).
>
> - **Use the health check feature to identify unresponsive workers:** App Service has built-in capabilities that periodically ping a specific path of your web application. The platform pings this path to determine whether your application is healthy and responds to requests. 
>
>   When your site is scaled out to multiple instances, App Service excludes any unhealthy instances from serving requests. This process improves your overall availability. 
>
>   Your app's health check path should poll the essential components of your application, such as your database, cache, or messaging service. This step helps ensure that the status returned by the [health check path](https://azure.github.io/AppService/2020/05/15/Robust-Apps-for-the-cloud.html#set-your-health-check-path) is an accurate picture of the overall health of your application.
>
> - **Use the auto-heal feature:** Sometimes your application might experience unexpected behaviors that a simple restart can solve. Use the auto-heal feature to define a *condition* that triggers auto-heal and the *action* that auto-heal initiates when that condition is met. For more information, see [App Service diagnostics overview](/azure/app-service/overview-diagnostics#auto-healing).
>
> - **App Service resiliency score report:** To review tailored best practice recommendations, see the [App Service diagnostics overview](/azure/app-service/overview-diagnostics).

### Configuration recommendations

| Recommendation|Benefit|
|-----------|-------- |
|(App Service) Choose the Premium v3 tier of an App Service plan for production workloads. <br><br> Set the maximum and minimum number of workers according to your capacity planning. For more information, see [App Service plan overview](/azure/app-service/overview-hosting-plans). |A Premium v3 App Service plan provides advanced scaling features and ensures redundancy if failures occur.|
|(App Service) [Enable zone redundancy](/azure/reliability/reliability-app-service#availability-zone-support).<br><br> Consider provisioning more than three instances to enhance fault tolerance. <br><br> Check regional support for zone redundancy because not all regions have this feature. | Your application can withstand failures in a single zone when multiple instances are spread across zones. Traffic automatically shifts to healthy instances in other zones and maintains application reliability if one zone is unavailable.|
|(Web Apps) Consider disabling the application request routing (ARR) affinity feature. ARR affinity creates sticky sessions that redirect users to the node that handled their previous requests.|Incoming requests are evenly distributed across all available nodes when you disable ARR affinity. Evenly distributed requests prevent traffic from overwhelming any single node. Requests can be seamlessly redirected to other healthy nodes if a node is unavailable. <br><br> Avoid session affinity to ensure that your App Service instance remains stateless. A stateless App Service instance reduces complexity and ensures consistent behavior across nodes. <br><br> Remove sticky sessions so that App Service can add or remove instances to scale horizontally.|
|(App Service) Do not use App Service's [back up and restore](/azure/app-service/manage-backup) functionality for linked databases. |Using native backup and restore tools for your linked databases provides a more reliable and tunable recovery solution for the state store of your web app.<br><br>Backing up linked databases through App Service is [deprecated](/azure/app-service/manage-backup#deprecation-of-linked-database-backups). |
|(Web Apps) [Define automatic healing rules](/azure/app-service/overview-diagnostics#auto-healing) based on request count, slow requests, memory limits, and other indicators that are part of your performance baseline. Consider this configuration as part of your scaling strategy. |Automatic healing rules help your application recover automatically from unexpected problems. The configured rules trigger healing actions when thresholds are breached. <br><br> Automatic healing enables automatic proactive maintenance. |
|(Web Apps) [Enable the health check feature](/azure/app-service/monitor-instances-health-check) and provide a path that responds to the health check requests. |Health checks can detect problems early. Then the system can automatically take corrective actions when a health check request fails. <br><br> The load balancer routes traffic away from unhealthy instances, which directs users to healthy nodes.|
|(Web Apps) [Enable **Always on**](/azure/app-service/webjobs-create?tabs=windowscode#continuous-vs-triggered-webjobs) when your web app is hosting a WebJob. | Web apps can time out after a period of HTTP inactivity. The Always on setting helps ensure that both continuous and triggered WebJobs run reliably. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around hosting on App Service.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review security baselines:** To enhance the security posture of your application that's hosted on an App Service plan, review the [security baseline for App Service](/security/benchmark/azure/baselines/app-service-security-baseline).
>
> - **Use the latest runtime and libraries:** Thoroughly test your application builds before you do updates to catch problems early and help ensure a smooth transition to the new version. App Service supports the [language runtime support policy](/azure/app-service/language-support-policy) for updating existing stacks and retiring end-of-support stacks.
>
> - **Create segmentation through isolation boundaries to contain breaches:** Apply identity segmentation. For example, implement role-based access control (RBAC) to assign specific permissions based on roles. Follow the principle of least privilege to limit access rights to only what's necessary. Also create segmentation at the network level. [Integrate App Service apps with an Azure virtual network](/azure/app-service/web-sites-integrate-with-vnet) for isolation and define network security groups (NSGs) to filter traffic.
>
>   App Service plans provide the App Service Environment tier that provides a high degree of isolation. With App Service Environment, you get dedicated compute and networking.
>
> - **Apply access controls on identities:** Restrict inward access to the web app and outward access from the web app to other resources. This configuration applies access controls on identities and helps maintain the workload's overall security posture.
>
>   Use Microsoft Entra ID for all authentication and authorization needs. Use built-in roles, such as a [Web Plan Contributor](/azure/role-based-access-control/built-in-roles/web-and-mobile#web-plan-contributor), [Website Contributor](/azure/role-based-access-control/built-in-roles/web-and-mobile#website-contributor), and a [generic Contributor, Reader, and Owner](/azure/role-based-access-control/built-in-roles#general).
>
> - **Apply network security controls:** Integrate your App Service with a virtual network to control the outbound traffic. Use private endpoints to control inbound traffic, limit access to your App Service instance from within your virtual network, and disable public internet access. For more information, see [Network routing](/azure/app-service/overview-vnet-integration#network-routing).
>
>   Deploy a web application firewall (WAF) to help protect against common vulnerabilities. Both Application Gateway and Azure Front Door have integrated WAF capabilities.
>
>   Configure the reverse proxy rules and network settings appropriately to achieve the desired level of security and control. For example, add NSG rules on the private endpoint subnet to only accept traffic from the reverse proxy.
>
>     Egress traffic from the application to other PaaS services should be over private endpoints. Consider placing a firewall component to restrict egress traffic to the public internet. Both approaches help prevent data exfiltration.
>
>   For more information, see [App Service networking features](/azure/app-service/networking-features).
>
> - **Encrypt data:** Help protect data in transit by using end-to-end Transport Layer Security (TLS). Use your [customer-managed keys](/azure/app-service/configure-encrypt-at-rest-using-cmk) for full encryption of data at rest.
>
>   Don't use legacy protocols such as TLS 1.0 and 1.1. Ensure that all web apps use HTTPS only and enforce TLS 1.2 or higher. The default minimum TLS version is 1.2. For more information, see [App Service TLS overview](/azure/app-service/overview-tls).
>
>   All of your App Service instances have a default domain name. Use a custom domain and secure that domain by using certificates.
>
> - **End-to-end TLS encryption:** End-to-end TLS encryption is available in premium App Service plans. This feature encrypts your traffic throughout the entire transaction, which minimizes the risk of traffic interception.
>
> - **Reduce the attack surface:** Remove default configurations that you don't need. For example, disable remote debugging, local authentication for Source Control Manager (SCM) sites, and basic authentication. Disable unsecured protocols like HTTP and File Transfer Protocol (FTP). Enforce configurations by using Azure policies. For more information, see [Azure policies](#azure-policies).
>
> - **Implement restrictive cross-origin resource sharing (CORS) policies:** Use restrictive CORS policies in your web app to only accept requests from the allowed domains, headers, and other criteria. Enforce CORS policies by using built-in Azure policy definitions.
>
> - **Use managed identities:** Enable [managed identities](/azure/app-service/overview-managed-identity) for your App Service instance to more securely access other Azure services without needing to manage credentials.
>
> - **Protect application secrets:** You need to handle sensitive information, like API keys or authentication tokens. Instead of hardcoding these secrets directly into your application code or configuration files, you can use Azure Key Vault references in app settings. When the application starts, App Service automatically retrieves the secret values from Key Vault by using the app's managed identity.
>
> - **Enable resource logs for your application:** Enable resource logs for your application to create comprehensive activity trails that provide valuable data during investigations that follow security incidents. For more information, see [Azure Monitor resource logs](/azure/app-service/monitor-app-service#azure-monitor-resource-logs).
>
>   Consider logging as part of your threat modeling process when you assess threats.

### Configuration recommendations

|Recommendation|Benefit|
|-----------|-------------- |
|(Web Apps) [Assign managed identities](/azure/app-service/overview-managed-identity) to the web app. To maintain isolation boundaries, don't share or reuse identities across applications. <br><br> Make sure that you [securely connect to your container registry](/azure/app-service/tutorial-custom-container) if you use containers for your deployment.|The application retrieves secrets from Key Vault to authenticate outward communication from the application. Azure manages the identity and doesn't require you to provision or rotate any secrets. <br><br> You have distinct identities for granularity of control. Distinct identities make revocation easy if an identity is compromised.|
|(Web Apps) Configure [custom domains](/azure/app-service/configure-ssl-bindings) for applications. <br><br> Disable HTTP and only accept HTTPS requests.|Custom domains enable secure communication through HTTPS by using TLS protocol, which helps ensure the protection of sensitive data and builds user trust.|
|(Web Apps) Evaluate whether [App Service built-in authentication](/azure/app-service/overview-authentication-authorization) is the right mechanism to authenticate users that access your application. App Service built-in authentication integrates with Microsoft Entra ID. This feature handles token validation and user identity management across multiple sign-in providers and supports OpenID Connect. With this feature, you don't have authorization at a granular level, and you don't have a mechanism to test authentication. |When you use this feature, you don't have to use authentication libraries in application code, which reduces complexity. The user is already authenticated when a request reaches the application.|
|(Web Apps) Configure the application for [virtual network integration](/azure/app-service/overview-vnet-integration). <br><br> Use [private endpoints for App Service apps](/azure/app-service/overview-private-endpoint). Block all public traffic. <br><br> Route the [container image pull through the virtual network](/azure/app-service/configure-vnet-integration-routing#container-image-pull) integration. All [outgoing traffic from the application](/azure/app-service/configure-vnet-integration-routing#configure-application-routing) passes through the virtual network.|Get the security benefits of using an Azure virtual network. For example, the application can securely access resources within the network. <br> <br> Add a private endpoint to help protect your application. Private endpoints limit direct exposure to the public network and allow controlled access through the reverse proxy.|
|(Web Apps) To implement hardening: <br><br> - [Disable basic authentication](/azure/app-service/configure-basic-auth-disable) that uses a username and password in favor of Microsoft Entra ID-based authentication. <br><br> - Turn off remote debugging so that inbound ports aren't opened.<br><br> - Enable [CORS policies](/azure/app-service/app-service-web-tutorial-rest-api) to tighten incoming requests. <br><br> - Disable protocols, such as [FTP](/azure/app-service/configure-common). |We don't recommend basic authentication as a secure deployment method. Microsoft Entra ID employs OAuth 2.0 token-based authentication, which provides numerous advantages and enhancements that address the limitations that are associated with basic authentication. <br><br> Policies restrict access to application resources, only allow requests from specific domains, and secure cross-region requests. |
|(Web Apps) [Always use Key Vault references as app settings](/azure/app-service/app-service-key-vault-references). <br> |Secrets are kept separate from your app's configuration. App settings are encrypted at rest. App Service also manages secret rotations. |
|(App Service) [Enable Microsoft Defender for Cloud for App Service](/azure/defender-for-cloud/tutorial-enable-app-service-plan).| Get real-time protection for resources that run in an App Service plan. Guard against threats and enhance your overall security posture.|
|(App Service) [Enable diagnostic logging](/azure/app-service/troubleshoot-diagnostic-logs) and add instrumentation to your app. The logs are sent to Azure Storage accounts, Azure Event Hubs, and Log Analytics. For more information about audit log types, see [Supported log types](/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types).|Logging captures access patterns. It records relevant events that provide valuable insights into how users interact with an application or platform. This information is crucial for accountability, compliance, and security purposes.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Web Apps and its environment.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Estimate the initial cost:** As part of your cost modeling exercise, use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator) to evaluate the approximate costs associated with various tiers based on the number of instances that you plan to run. Each App Service tier provides different compute options.
>
>   Continuously monitor the cost model to track expenditures.
>
> - **Evaluate the discounted options:** Higher tiers include dedicated compute instances. You can apply a reservation discount if your workload has a predictable and consistent usage pattern. Make sure that you analyze usage data to determine the type of reservation that suits your workload. For more information, see [Save costs with App Service reserved instances](/azure/cost-management-billing/reservations/prepay-app-service).
>
> - **Understand usage meters:** Azure charges an hourly rate, prorated to the second, based on your App Service plan's pricing tier. Charges apply to each scaled-out instance in your plan, based on the time that you allocate the VM instance. Pay attention to underused compute resources that might increase your costs as a result of overallocation because of suboptimal SKU selection, or poorly configured scale-in configuration.
>
>    Extra App Service features, like custom domain registration and custom certificates, might add costs. Other resources, such as virtual networks that isolate your solution or key vaults that protect workload secrets, can also integrate with your App Service resources and add costs. For more information, see [Understand the full billing model for App Service](/azure/app-service/overview-manage-costs#understand-the-full-billing-model-for-azure-app-service).
>
> - **Consider the tradeoffs between density and isolation:** You can use App Service plans to host multiple applications on the same compute, which saves costs with shared environments. For more information, see [Tradeoffs](#tradeoffs).
>
> - **Evaluate the effects of your scaling strategy on cost:** You must properly design, test, and configure for scaling out and for scaling in when you implement autoscaling. Establish precise maximum and minimum limits on autoscaling.
>
>   Proactively initialize the application for reliable scaling. For example, don't wait until the CPU reaches 95% usage. Instead, trigger scaling at around 65% to allow sufficient time for new instances to be allocated and initialized during the scaling process. However, this strategy might result in unused capacity.
>
>   We recommend that you combine and balance mechanisms for scaling up and scaling out. For example, an app can scale up for some time and then scale out as necessary. Explore high tiers that provide large capacity and efficient resource usage. Based on usage patterns, higher Premium tiers are often more cost effective because they're more capable.
>
> - **Optimize environment costs:** Consider using the basic tier or free tier to run pre-production environments. These tiers are low performance and low cost. If you use the basic tier or free tier, use governance to enforce the tier, constrain the number of instances and CPUs, restrict scaling, and limit log retention.
>
> - **Implement design patterns:** This strategy reduces the volume of requests that your workload generates. Consider using patterns like the [Backends for Frontends pattern](/azure/architecture/patterns/backends-for-frontends) and the [Gateway Aggregation pattern](/azure/architecture/patterns/gateway-aggregation) to minimize the number of requests and reduce costs.
>
> - **Regularly check data-related costs:** Extended data retention periods or expensive storage tiers can result in high storage costs. More expenses can accumulate because of bandwidth usage and prolonged retention of logging data.
>
>   Consider implementing caching to minimize data transfer costs. Start with local in-memory caching, and then explore distributed caching options to reduce the number of requests to the back-end database. Consider the bandwidth traffic costs that are associated with cross-region communication if your database is located in a different region.
>
> - **Optimize deployment costs:** Take advantage of deployment slots to optimize costs. The slot runs in the same compute environment as the production instance. Use them strategically for scenarios like blue-green deployments that switch between slots. This approach minimizes downtime and ensures smooth transitions.
>
>   Use deployment slots with caution. You can introduce problems, such as exceptions or memory leaks, that might affect the existing instances and new instances. Make sure that you thoroughly test changes. For more information about operational guidance, see [Operational Excellence](#operational-excellence).

### Configuration recommendations

| Recommendation|Benefit|
|-----------|------------------|
|(App Service) [Choose free tiers or basic tiers](/azure/app-service/overview-hosting-plans) for lower environments. We recommend these tiers for experimental use. Remove the tiers when you no longer need them.|The free tiers and basic tiers are budget-friendly compared to higher tiers. They provide a cost-effective solution for nonproduction environments that don't need the full features and performance of premium plans.|
|(App Service) Take advantage of discounts and explore preferred pricing for: <br><br> - Lower environments with [dev/test plans](https://azure.microsoft.com/pricing/offers/dev-test/). <br><br> - [Azure reservations](/azure/app-service/overview-manage-costs#azure-reservations) and [Azure savings plans](https://azure.microsoft.com/pricing/offers/savings-plan-compute/#Benefitsandfeatures) for dedicated compute that you provision in the Premium v3 tier and App Service Environment. <br><br> Use reserved instances for stable workloads that have predictable usage patterns. |Dev/test plans provide reduced rates for Azure services, which make them cost-effective for nonproduction environments. <br><br> Use reserved instances to prepay for compute resources and get significant discounts.|
|(Web Apps) [Monitor costs](/azure/app-service/overview-manage-costs#monitor-costs) that App Service resources incur. Run the cost analysis tool in the Azure portal. <br><br> [Create budgets and alerts](/azure/app-service/overview-manage-costs#create-budgets) to notify stakeholders.|You can identify cost spikes, inefficiencies, or unexpected expenses early on. This proactive approach helps you to provide budgetary controls to prevent overspending.|
|(App Service) Scale in when demand decreases. To scale in, define scale rules to [reduce the number of instances in Azure Monitor](/azure/azure-monitor/autoscale/autoscale-understanding-settings).|Prevent wastage and reduce unnecessary expenses.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Web Apps.

> [!div class="checklist"]
>
> - **Manage releases:** Use deployment slots to manage releases effectively. You can deploy your application to a slot, perform testing, and validate its functionality. After verification, you can seamlessly move the app to production. This process doesn't incur extra costs because the slot runs in the same virtual machine (VM) environment as the production instance.
>
>    Use the swap with preview (multiple-phase swap) feature. Swap with preview allows you to test the app in your staging slots against your production settings and also warm up the app. After doing your tests and warming up all the necessary paths, you can then complete the swap. Then the app will receive production traffic without restarting.
>
> - **Run automated tests:** Before you promote a release of your web app, thoroughly test its performance, functionality, and integration with other components. Use [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing). It integrates with Apache JMeter, a popular tool for performance testing. Explore automated tools for other types of testing, such as Phantom for functional testing.
>
> - **Automate deployments:** Use continuous integration and continuous deployment pipelines with Azure DevOps or GitHub Actions to automate deployments and reduce manual effort. For more information, see [Continuous deployment to App Service](/azure/app-service/deploy-continuous-deployment).
>
> - **Deploy immutable units:** Implement the [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp) to compartmentalize App Service into an immutable stamp. App Service supports the use of containers, which are inherently immutable. Consider [custom containers](/azure/app-service/configure-custom-container) for your App Service web app.
>
>    Each stamp represents a self-contained unit that you can quickly scale out or scale in. Units that are based on this stamp are temporary and stateless. Stateless design simplifies operations and maintenance. This approach is ideal for mission-critical applications. For more information, see [Mission-critical baseline with App Service](/azure/architecture/guide/networking/global-web-applications/mission-critical-app-service).
>
>   Use an infrastructure as code (IaC) technology, such as Bicep, to stamp out units with repeatability and consistency.
>
> - **Keep production environments safe:** Create separate App Service plans to run production and pre-production environments. Don't make changes directly in the production environment to help ensure stability and reliability. Separate instances allow flexibility in development and testing before you promote changes to production.
>
>   Use low environments to explore new features and configurations in an isolated manner. Keep development and test environments ephemeral.
>
> - **Manage certificates:** For custom domains, you need to manage TLS certificates.
>
>   Have processes in place to procure, renew, and validate certificates. Offload these processes to App Service if possible. If you use your own certificate, you must manage its renewal. Choose an approach that best aligns with your security requirements.

|Recommendation|Benefit|
|-----------|------- |
|(Web Apps) [Monitor the health of your instances](/azure/app-service/monitor-instances-health-check) and activate instance health probes. <br><br>Set up a specific path for handling health probe requests. |You can detect problems promptly and take necessary actions to maintain availability and performance.|
|(Web Apps) [Enable diagnostics logs](/azure/app-service/troubleshoot-diagnostic-logs) for the application and the instance. <br><br> Frequent logging can slow down the performance of the system, add to storage costs, and introduce risk if you have unsecure access to logs. Follow these best practices: <br><br> - Log the right level of information. <br><br> - Set retention policies. <br><br> - Keep an audit trail of authorized access and unauthorized attempts. <br><br>- Treat logs as data and apply data-protection controls. |Diagnostic logs provide valuable insights into your app's behavior. Monitor traffic patterns and identify anomalies.|
|(Web Apps) Take advantage of [App Service-managed certificates](/azure/app-service/configure-ssl-certificate#create-a-free-managed-certificate) to offload certification management to Azure. |App Service automatically handles processes like certificate procurement, certificate verification, certificate renewal, and importing certificates from Key Vault. Alternatively, upload your certificate to Key Vault and authorize the App Service resource provider to access it. |
|(App Service) [Validate app changes in the staging slot](/azure/app-service/deploy-staging-slots) before you swap it with the production slot. |Avoid downtime and errors. <br><br> Keeping the previously deployed production version in a slot allows you to revert to the last-known good state if you detect a problem after deployment.<br><br>Allows you to ensure that all instances are warmed up before being swapped into production to avoid transient cold-start related deployment concerns. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Web Apps.

> [!div class="checklist"]
>
> - **Identify and monitor performance indicators:** Set targets for the key indicators for the application, such as the volume of incoming requests, time that the application takes to respond to requests, pending requests, and errors in HTTP responses. Consider key indicators as part of the performance baseline for the workload.
>
>   Capture App Service metrics that form the basis of performance indicators. Collect logs to gain insights into resource usage and activities. Use application performance monitoring tools, such as Application Insights, to collect and analyze performance data from the application. For more information, see [App Service monitoring data reference](/azure/app-service/monitor-app-service-reference).
>
>   Include code-level instrumentation, transaction tracing, and performance profiling.
>
> - **Assess capacity:** Simulate various user scenarios to determine the optimal capacity that you need to handle expected traffic. Use Load Testing to understand how your application behaves under different levels of load.
>
> - **Choose the right tier:** Use dedicated compute for production workloads. Premium v3 tiers provide larger SKUs with increased memory and CPU capacity, more instances, and more features, such as zone redundancy. For more information, see [Premium v3 pricing tier](/azure/app-service/overview-hosting-plans#premium-v3-pricing-tier).
>
> - **Optimize your scaling strategy:** When possible, use [autoscaling](/azure/azure-monitor/autoscale/autoscale-overview) instead of manually adjusting the number of instances as application load changes. With autoscaling, App Service adjusts server capacity based on predefined rules or triggers. Make sure you do adequate performance testing and set the right rules for the right triggers.
>
>    If you prioritize simplicity during the initial setup, use an autoscaling option that requires only setting limits, without needing to define rules.
>
>   Have sufficient resources readily available to help ensure optimal performance. Allocate resources appropriately to maintain performance targets, such as response time or throughput. Consider overallocation of resources when necessary.
>
>    When you define autoscale rules, account for the time that it takes for your application to initialize. Consider this overhead when you make all scaling decisions.
>
> - **Use caching:** Retrieving information from a resource that doesn't change frequently and is expensive to access affects performance. Complex queries, including joins and multiple lookups, contribute to runtime. Perform caching to minimize the processing time and latency. Cache query results to avoid repeated round trips to the database or back end and reduce processing time for subsequent requests.
>
>   For more information about using local and distributed cache in the workload, see [Caching](/azure/architecture/best-practices/caching).
>
> - **Review the performance antipatterns:** To make sure that the web application performs and scales in accordance with your business requirements, avoid the [typical antipatterns](/azure/architecture/antipatterns/). The following table describes some antipatterns that App Service corrects.
>
>   |Antipattern|Description|
>   |------------------------------|-----------|
>   |[Busy Front End](/azure/architecture/antipatterns/busy-front-end/)|Resource-intensive tasks can increase the response times for user requests and cause high latency. <br><br>Move processes that consume significant resources to a separate back end. Use a message broker to queue resource-intensive tasks that the back end picks up to asynchronously process.|
>   |[No Caching](/azure/architecture/antipatterns/no-caching/)|Serve requests from an intermediate cache in front of the back-end database to reduce latency. |
>   |[Noisy Neighbor](/azure/architecture/antipatterns/noisy-neighbor/noisy-neighbor)| Multitenant systems share resources between tenants. The activity of one tenant can have a negative effect on another tenant's use of the system. App Service Environment provides a fully isolated and dedicated environment to run App Service apps.|

### Configuration recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|(App Service) [Enable the *Always On* setting](/azure/app-service/configure-common) when applications share a single App Service plan. App Service apps automatically unload when idle to save resources. The next request triggers a cold start, which can cause request time-outs. | The application is never unloaded with Always On enabled.|
|(Web Apps) [Consider using HTTP/2](/azure/app-service/configure-common) for applications to improve protocol efficiency. | Choose HTTP/2 over HTTP/1.1 because HTTP/2 fully multiplexes connections, reuses connections to reduce overhead, and compresses headers to minimize data transfer.|

## Tradeoffs

You might have to make design tradeoffs if you use the approaches in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: **Density and isolation**

- **Higher density:** Colocate multiple apps within the same App Service plan to minimize resources. All apps share resources like CPU and memory, which can save money and reduce operational complexity. This approach also optimizes resource usage. Apps can use idle resources from another app if load patterns change over time.

  Also consider the disadvantages, like resource contention. For example, spikes in usage or instability of an app can affect the performance of other apps. Incidents in one app can also permeate other apps within the shared environment, which can affect security. For critical applications that require high availability and performance, isolated environments like **App Service Environment v3** provide dedicated resources but at a higher cost. Consider using shared environments for noncritical workloads and isolated environments for mission-critical applications.

- **Higher isolation:** Isolation helps prevent interference. This strategy applies to security, performance, and even segregation of development, testing, and production environments.

  App Service Environment provides better control over security and data protection because each app can have its own security settings. Your environment can contain breaches because isolation limits the blast radius. Resource contention is minimized from a performance perspective. Isolation allows for independent scaling based on specific demand and individual capacity planning.

  As a disadvantage, this approach is more expensive and requires operational rigor.

:::image type="icon" source="../_images/trade-off.svg"::: **Reliable scaling strategy**

A well-defined scaling strategy helps ensure that your application can handle various loads without compromising performance. However, there are tradeoffs on cost. Scaling operations take time. When new resources are allocated, the application must be properly initialized before it can effectively process requests. You can overprovision resources (or prewarm instances) to provide a safety net. Without that extra capacity, during the initialization phase, there might be a delay in serving requests, which affects user experience. Autoscaling operations might trigger early enough to enable proper resource initialization by the time customers use the resources.

  As a disadvantage, overprovisioned resources cost more. You're charged per second for every instance, including prewarmed instances. Higher tiers include prewarmed instances. Determine whether capabilities with more expensive tiers are worth the investment.

:::image type="icon" source="../_images/trade-off.svg"::: **Building redundancy**

Redundancy provides resiliency but also incurs costs. Service-level objectives (SLOs) for your workload determine acceptable performance thresholds. It becomes wasteful if redundancy exceeds SLO requirements. Evaluate whether excess redundancy improves SLOs or adds unnecessary complexity.

  Also consider the disadvantages. For example, multiple-region redundancy provides high availability but adds complexity and cost because of data synchronization, failover mechanisms, and interregional communication. Determine whether zone redundancy can meet your SLOs.

## Azure policies

Azure provides an extensive set of built-in policies related to App Service and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Proper network controls are in place. For example, you can incorporate network segmentation by placing App Service in Azure Virtual Network through virtual network injection to have greater control over network configuration. The application doesn't have public endpoints and connects to Azure services through private endpoints.

- Identity controls are in place. For example, the application uses managed identities to authenticate itself against other resources. App Service built-in authentication (or Easy Auth) verifies incoming requests.

- Features such as remote debugging and basic authentication are disabled to reduce the attack surface.

For comprehensive governance, review the [Azure Policy built-in definitions for App Service](/azure/app-service/policy-reference) and other policies that might affect the security of the compute layer.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [App Service baseline architecture](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).

## Next steps

Consider the following articles as resources that demonstrate the recommendations highlighted in this article.

- Use the following reference architectures as examples of how to apply these recommendations to a workload.

  - If you haven't deployed a web app, see [Basic web application](/azure/architecture/web-apps/app-service/architectures/basic-web-app).
  - For a foundational architecture as your starting point for a production-grade deployment, see [Baseline highly available zone-redundant web application](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).
  
- Use the following product documentation to build your implementation expertise:

  - [App Service](/azure/app-service/)
  - [App Service plan](/azure/app-service/overview-hosting-plans)


<!-- Update 499998: App Service IPv6 Support added to Reliability pillar -->
