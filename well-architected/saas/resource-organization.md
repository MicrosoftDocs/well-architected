---
title: Resource Organization for SaaS Workloads on Azure
description: Learn strategies for how to choose the right Azure regions for your resources and develop a resource organization strategy to support the growth and evolution of your SaaS solution.
author: MikeBazMSFT
ms.author: prwilk
ms.date: 11/04/2025
ms.topic: concept-article
ms.collection: learn-startups
---

# Resource organization for SaaS workloads on Azure

When you create a software as a service (SaaS) solution, the way you organize Azure resources can significantly affect your operational processes, security, and compliance with regulatory standards. 

This article discusses strategies for choosing the right Azure regions for your resources and developing a resource organization strategy to support the growth and evolution of your SaaS solution.

## Region selection and multi-region solutions

The first step in planning your architecture is to choose the region where your environment will exist. Azure resources are deployed into regions, which are physical locations of Azure datacenters. Select regions that meet your customers' data residency requirements and your needs for performance, service availability, and scale. Consider deploying your SaaS solution into multiple regions to support global customers and enhance resiliency. 

### Design considerations

- **Evaluate regions that are optimized for production.** Some Azure regions offer more services than others. Also, many Azure regions support availability zones to withstand datacenter failures, which are an important part of a resiliency strategy. You should use regions that have availability zones, and deploy production components into multiple availability zones. To view available services in each region, see [Products available by region](https://azure.microsoft.com/explore/global-infrastructure/products-by-region/). 

    > Refer to [Recommendations for using availability zones and regions](../design-guides/regions-availability-zones.md).

- **Design according to data residency requirements.** There might be requirements about where customer data can be stored, often because of data sovereignty laws. Requirements can range from broad geographic areas, like Europe, to specific countries or regions. Azure defines geographies based on geopolitical boundaries, and you can typically use any region within a defined geography to meet data residency requirements.

    For information about Azure region support, see [Azure geographies](https://azure.microsoft.com/explore/global-infrastructure/geographies/).
  
- **Minimize latency.** Choose regions that are close to your customers and users. For global customers, deploy multiple instances in different regions. Alternatively, use a global load balancer to route traffic to regions that are closer to users and accelerate network traffic. [Azure Front Door](/azure/frontdoor/front-door-overview) is a common choice in those scenarios.

- **Deploy across multiple regions.** Multi-region deployments offer higher resiliency and support low-latency connections for global customers. This design can also help you meet complex data residency requirements.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: A multi-region design can be complex and costly because of redundancy across regions. It also increases operational costs. 

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Use Azure-recommended regions for your deployments. Recommended regions are listed in the Azure portal when creating a new resource. <br><br>  Ensure that the regions you use support availability zones. | You'll be able to support a more scalable and resilient solution design. |
| Start with the minimum number of regions necessary to meet your needs, and expand as required. | This approach helps you prevent over-complicating your design and operations. |
| Clarify your customers' data residency needs early. | Deploying into a new region can take time. The sooner you know about any constraints that your customers impose, the more likely you are to meet their needs. |

## Resource organization

You can organize Azure resources in various ways. You can use a combination of resource groups, subscriptions, management groups, and Microsoft Entra tenants to group or separate your resources. Different resource organization strategies provide different sets of tradeoffs for manageability, complexity, security, and many other factors. When you create a SaaS solution, you need to consider how to organize your resources. This decision is informed by your tenancy model. Resource organization is perhaps the most fundamental part of properly architecting, deploying, and managing a SaaS solution.

To learn more about how Azure resources can be organized, see [Organize your Azure resources effectively](/azure/cloud-adoption-framework/ready/azure-setup-guide/organize-resources).

### Design considerations

- **Understand landing zones.** A landing zone is the foundational setup of your cloud footprint. It includes access controls, billing strategies, and governance procedures. Your SaaS solution should be flexible, scalable, and consistently configured across subscriptions to allow for easy adjustments as needs change.
  
    An Azure landing zone is the recommended base configuration of an Azure environment. For landing zone recommendations that are specific to independent software vendor (ISV) organizations, see [ISV considerations for Azure landing zones](/azure/cloud-adoption-framework/ready/landing-zone/isv-landing-zone).

- **Decide which resources to share.** Determine which components to share and which to deploy individually for customers. Sharing resources reduces costs and management but can lead to tradeoffs for isolation, security, and performance. It's common to have a mix of shared and per-customer components. At minimum, identity and billing components are typically shared among customers.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Sharing resources reduces costs and management but add complexities for isolation, security, and performance. 

    Don't use shared production resources in your pre-production stages and environments. Your resource organization strategy needs to accommodate parallel deployments for your resources, even if you share resources in production.

- **Plan for Azure resource quotas.** Understand the quotas and limits for Azure resources, like the number of resources per subscription or region, and request limits within a time window. Choose a strategy to adapt your resource organization if you approach these limits.

    For more information about Azure quotas and how they affect SaaS solutions, see [Resource limits](/azure/architecture/guide/multitenant/approaches/resource-organization#resource-limits).

- **Apply the Deployment Stamps pattern.** The [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp) is beneficial in many ways. It enables you to deploy dedicated resources for a specific customer. You can also use it to deploy identical resources across multiple Azure regions, either customer-specific or shared. Additionally, with a stamp, you can deploy resources into a customer's own Azure environment, which is useful when they need to own the resources. You accomplish this type of deployment via Azure Marketplace by using templatized resources.

- **Support your operational processes.** Effective resource organization affects team operations. For example, a consistent naming and tagging strategy helps in diagnostics, resource management, and cost management. A good tagging and naming strategy helps you quickly search for resources that are used for specific customers and distinguish between production and non-production environments.

    SaaS solutions require rigorous customer lifecycle management, including onboarding new customers and offboarding the customers that leave. Your resource organization strategy should facilitate the easy creation and removal of customer resources and the reconfiguration of shared resources. 

- **Publish through Azure Marketplace.** If your business model and offering is compatible with Azure Marketplace, consider publishing your solution there. You can use Azure Marketplace regardless of whether you have an existing SaaS solution or are converting a single-tenant solution into a multitenant SaaS.

    Azure Marketplace provides support for billing, onboarding, and offboarding and simplifies management and customer access to your application. If you use Azure Marketplace, you don't need to manage sensitive billing information or payment providers. 

    If you deploy to customer environments, you can use Azure Marketplace to deploy [Managed Applications](/azure/azure-resource-manager/managed-applications), which provide a range of capabilities that enable you to deploy and manage resources within a customer's Azure subscription.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Review Azure quotas and limits for resources that you deploy. If you're approaching limits, choose a strategy for continuing to grow, such as by deploying additional resources, following [bin packing](/azure/architecture/guide/multitenant/approaches/resource-organization) guidance, or using multiple Azure subscriptions. | This approach enables you to scale and grow while staying within limits. |
| Define resource lifetimes and lifecycles. Distinguish between shared and customer-specific resources. | This strategy enables you to improve your operational efficiency by predefining your processes and improve your cost efficiency by removing resources that you no longer need. |
| Consider publishing your solution through the Azure Marketplace, especially if you deploy into customer environments. | Azure Marketplace provides a range of services, including management of billing. |
| Take advantage of tooling, like [Mona for SaaS](https://github.com/microsoft/mona-saas), to optimize integration with Azure Marketplace.|  Reusable code modules and templates can reduce the time and effort needed for deployment and integration. Using these tools can help you manage solutions that are available on Azure Marketplace.|

## Additional resources

Multitenancy is a core business methodology for designing SaaS workloads. These articles provide more information about resource organization:

- [Azure resource organization in multitenant solutions](/azure/architecture/guide/multitenant/approaches/resource-organization)
- [Tenant lifecycle considerations in a multitenant solution](/azure/architecture/guide/multitenant/considerations/tenant-lifecycle)
- [Architectural approaches for a multitenant solution: Deployment stamps](/azure/architecture/guide/multitenant/approaches/overview#deployment-stamps-pattern)

## Community links

- [Video on designing a landing zone for SaaS](https://aka.ms/ISVLandingZones)

## Next step

Learn about the design considerations for identity and access management in your SaaS workload environment.

> [!div class="nextstepaction"]
> [Design area: Identity and access management for SaaS workloads on Azure](./identity-access.md)
