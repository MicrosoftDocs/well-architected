---
title: Resource organization for SaaS workloads for ISVs
description: Learn about the resource organization considerations for SaaS workloads.
author: MikeBazMSFT
ms.author: micbaz
ms.date: 09/30/2024
ms.topic: conceptual
---

# Resource organization for SaaS workloads for ISVs

[!INCLUDE [header_file](includes/temporary-warning.md)]

When building SaaS, the organization of Azure resources can significantly affect your operational processes, security, and compliance with regulatory standards. 

This article discusses strategies for choosing the right Azure regions for your resources and developing a resource organization strategy to support the growth and evolution of your SaaS solution.

## Region selection and multi-region solutions

The first step in planning your architecture is to choose the region where your environment will exist. Azure resources are deployed into regions, which are physical locations of Azure datacenters. Select regions that meet your customers' data residency requirements and your needs for performance, service availability, and scale. Consider deploying your SaaS solution into multiple regions to support global customers and enhance resiliency. 

### Design considerations

- **Evaluate regions that are optimized for production.** Some Azure regions offer a wider range of services. To see available services in each region, see [Products available by region](https://azure.microsoft.com/explore/global-infrastructure/products-by-region/). 

    Also, many Azure regions support [availability zones](/azure/reliability/reliability-guidance-overview), which are an important part of a resiliency strategy. You should use regions with availablity zones, and you should deploy production components into multiple availabilty zones.

    To learn more about how to use regions and availability zones, see [RE:05 Recommendations for using availability zones and regions](../reliability/regions-availability-zones.md).
If you work with a Microsoft representative, let them know your resource needs so they can ensure your regions are ready for your workload.

- **Design according to data residency requirements.** There might be requirements about where customer data can be stored, often due to data sovereignty laws. Requirements can range from broad geographic areas, like Europe, to specific countries or regions. Azure defines geographies based on geopolitical boundaries, and you can typically use any region within a defined geography to meet data residency requirements.

    For information about Azure region suppport, see [Azure geographies](https://azure.microsoft.com/explore/global-infrastructure/geographies/).
  
- **Minimize latency.** Aim for regions close to your customers and users. For global customers, deploy multiple instances in different regions. Or, use a global load balancer to route traffic to regions closer to users and accelerate network traffic. Consider using [Azure Front Door](/azure/frontdoor/front-door-overview) to distribute your traffic globally.

- **Deploy across multiple regions.** Multi-region deployments offer higher resiliency and support low-latency connections for global customers. This design can also meet complex data residency requirements.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: A multi-region design can be complex and costly due to redundancy across regions. There's also added costs to operations. 

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Use Azure-recommended regions for your deployments. <br><br> Recommended regions are listed in the Azure portal when creating a new resource. <br><br>  Ensure that the regions you use support availability zones. | You'll be able to support a more scalable and resilient solution design. |
| Start with the minimum number of regions necessary to meet your needs, and expand as required. | You'll avoid over-complicating your design and operations. |
| Clarify your customers' data residency needs early. | Deploying into a new region can take time. The sooner you know about any constraints your customers impose, the more likely you'll be able to meet their needs. |

## Resource organization

Azure resources can be organized in different ways. You can use a combination of resoure groups, subscriptions, management groups, and Microsoft Entra tenants to group or separate your resources. Different resource organization strategies provide different sets of tradeoffs around manageability, complexity, security, and many other factors. When you build SaaS, you need to consider how you'll organize your resources, which is informed by your tenancy model. Resource organization is perhaps the most fundamental part of properly architecting, deploying, and managing a SaaS solution.

To learn more about how Azure resources can be organized, see [Azure fundamental concepts](/azure/cloud-adoption-framework/ready/considerations/fundamental-concepts).

### Design considerations

- **Understand landing zones.** A landing zone is the foundational setup of your cloud footprint, including access controls, billing strategies, and governance procedures. Your SaaS solution should be flexible, scalable, and consistently configured across subscriptions, allowing for easy adjustments as needs change.
  
    An Azure landing zone is the recommended base configuration of an Azure environment. For landing zone recommendations specific to independent software vendor (ISV) organizations, review the [ISV considerations for Azure landing zones](/azure/cloud-adoption-framework/ready/landing-zone/isv-landing-zone).

- **Decide which resources to share.** Determine which components to share and which to deploy per customer. Sharing reduces costs and management but can bring tradeoffs for isolation, security, and performance.  It's common to have a mix of shared and per-customer components. At minimum, identity and billing components are typically shared between customers.

    Ensure shared production resources are not used in pre-production stages. Your resource organization strategy needs to accommodate parallel deployments for your resources, even if you share resources in production.

- **Plan for Azure resource quotas.** Understand the quotas and limits for Azure resources, such as the number of resources per subscription or region and request limits within a time window. Plan your resource organization to adapt if you approach these limits.

    For more information about Azure quotas and how they affect SaaS solutions, see [Resource limits](/azure/architecture/guide/multitenant/approaches/resource-organization#resource-limits).

- **Apply the Deployment Stamps pattern.** The [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp) is beneficial in many ways. It enables you to deploy dedicated resources for a specific customer. You can also use it to deploy identical resources across multiple Azure regions, either customer-specific or shared. Additionally, with a stamp, you can deploy resources into a customer's own Azure environment, especially when they need to own the resources. This can be done via the Azure Marketplace with templatized resources.

    To learn more about applying the Deployment Stamps patterns for SaaS, see [Architectural approaches for a multitenant solution](/azure/architecture/guide/multitenant/approaches/overview#deployment-stamps-pattern).

- **Support your operational processes.** Organizing resources effectively impacts team operations. For example, consistent naming and tagging strategy helps in diagnostics, resource management, and cost management. A good tagging and naming strategy helps in quickly searching for resources used for specific customers and distinguish between production and non-production environments.

    SaaS solutions requires rigorous customer lifecycle management, including onboarding new customers and offboarding the customers that leave. Your resource organization strategy should facilitate the easy creation and removal of customer resources and the reconfiguration of shared resources. For more information about managing lifecycles, see [Tenant lifecycle considerations in a multitenant solution](/azure/architecture/guide/multitenant/considerations/tenant-lifecycle).

- **Publish through Azure Marketplace.** If your business model and offering is compatible with Azure Marketplace, consider publishing your solution to it. You can use the Azure Marketplace whether you have an existing SaaS solution or are converting a single-tenant solution into a multitenant SaaS.

    Azure Marketplace provides support for billing, onboarding, and offboarding, and simplifies management and customer access to your application. It removes the need for you to manage sensitive billing information and payment providers. 

For more information about resource organization for SaaS solutions, see [Azure resource organization in multitenant solutions](/azure/architecture/guide/multitenant/approaches/resource-organization).

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Review Azure quotas and limits for resources you deploy. If you're approaching limits, decide on a strategy to continue to grow, such as by deploying additional resources, following [bin packing](/azure/architecture/guide/multitenant/approaches/resource-organization) guidance, or using multiple Azure subscriptions. | You'll be able to scale and grow while staying within hard limits. |
| Define resource lifetimes and lifecycles. Distinguish between shared and customer-specific resources. | You'll improve your operational efficiency by predefining your processes, and you'll improve your cost efficiency by removing resources you no longer need. |
| Take advantage of tooling to optimize integration with Azure Marketplace, such as [Mona SaaS](https://github.com/microsoft/mona-saas).|  Reusable code modules and templates can reduce the time and effort needed for deployment and integration. This can help your solutions available on Marketplace.|

## Community links

- Video on designing a landing zone for SaaS: [ISV Considerations for Azure Landing Zones](https://aka.ms/ISVLandingZones).
