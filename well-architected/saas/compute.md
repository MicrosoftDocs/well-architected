---
title: Compute for SaaS Workloads on Azure
description: Learn about choosing your compute hosting model, the operational aspects, and how to optimize the technology options to help you meet your service level agreements and objectives.
author: paolosalvatori
ms.author: prwilk
ms.date: 12/15/2023
ms.topic: conceptual
ms.collection: learn-startups
---

# Compute for SaaS workloads on Azure

Your SaaS application needs to run on a compute platform. Similar to other components in your architecture, it needs to meet the business requirements and be designed according to your business model. The choice of compute platform is a significant design decision. Customer isolation, performance, and resiliency are affected by your decisions, and your compute platform influences how your entire SaaS solution can scale and grow.
  
This article describes the considerations for choosing your hosting model, the operational aspects, and how to optimize the technology options to help you meet your service level agreements and objectives. 

## Select a compute platform

Choosing the right compute platform for your SaaS workload is important but can be overwhelming due to the many options available. The best platform depends on factors like application architecture, scale, performance needs, and tenant isolation model. What's optimal for one application might not be for another.

### Design considerations

- **Hosting model**. Azure offers various hosting models, primarily Infrastructure as a Service (IaaS) and Platform as a Service (PaaS), each with its own benefits and trade-offs. Evaluate the requirements your application and choose the most suitable model. 
    
    - **Infrastructure as a service (IaaS)**. Provides Virtual Machines (VMs) with full control over them, including networking and storage. However, it requires managing and patching, which can be operationally intensive. Examples include virtual machine scale sets and Azure Kubernetes Service (AKS) clusters.   
 
    - **Platform as a service (PaaS)**. Allows application deployment without managing the underlying infrastructure. It includes built-in features for automatic scaling and load balancing. Examples are Azure App Service and Azure Container Apps.
    
   PaaS services offer less control compared to IaaS, which can be problematic if your application needs specific configuration. For example, your application runs on an operating system that's not supported by the PaaS service. PaaS offerings from Azure include Azure App Service and Azure Container Apps.

- **Workload type**. Some platforms are specialized for specific workloads, while others are versatile. For instance, Azure App Service is tailored for web applications, whereas Azure Kubernetes Service (AKS) is more general-purpose, capable of hosting web apps, AI workloads, and batch compute tasks.

- **Development team expertise**. Large changes can be challenging if the team lacks experience with the new platform. Assess your team's skills and match them to your platform requirements. It's often better to start with a simpler platform and gradually evolve your architecture rather than jumping straight to a more advanced option.
  
  For example, if you're building a containerized application, start with Azure Container Apps for easier management. As your needs grow more complex, you can transition to Azure Kubernetes Service (AKS), gaining a better understanding of the platform over time.

- **Management overhead**. Compute platforms balance overhead and control. More management responsibility shifted away from your team means less control over the platform.

    For example, IaaS offers high control over VMs but comes with significant overhead. If your application is deployed in a customer's environment, you might have limited access for management operations. Assess if these tradeoffs are acceptable and feasible.
  
- **Performance requirements**. Understand your application's performance requirements by modeling CPU, memory, network (bandwidth and latency), GPU, and availability needs, considering future growth. Use this information to choose the appropriate compute resources, like the series and size of virtual machines. Specialized requirements might also require you to select specific regions that support particular virtual machine series.

- **Reliability requirements**. Consider the reliability features of your compute platform and ensure they meet your reliability targets. You might need to use specific service tiers to have multiple instances of your solution or deploy across availability zones for enhanced reliability.

    > Refer to [RE:04 Recommendations for defining reliability targets](/azure/well-architected/reliability/metrics). 

- **Application security and compliance**. Evaluate the security features and [compliance certifications](/azure/compliance/) of each compute platform to ensure they meet your needs. For example, if you need to monitor and filter outgoing traffic, you may need to choose specific compute services or tiers.

### Design recommendations

| Recommendation | Benefit |
| --- | --- |
|  Evaluate the compute performance requirements by estimating CPU, memory, network, and GPU scale dimensions. <br><br>Perform load testing to gather more accurate data to inform your modeling exercise. | This will help in selecting the appropriate sizing for your compute platform and scale appropriately when the load on the system increases.|
| Evaluate your team's proficiency and start with the least complex platform that meets your needs or one they're already familiar with. | You'll avoid overburdening your team by choosing a compute platform they're familiar with, ensuring smoother operations. |
| Be flexible in your design. Aim for a solution that you can iterate on over time to adapt to evolving business and technical requirements.| It allows for easier adaptation to changes and improvements over time, responding effectively to evolving business and technical needs.|
| Evaluate the total cost of ownership (TCO), including costs of operating the solution. | You'll have a clear understanding of costs, which is crucial in planning your pricing model and ensuring cost-effective operations. |
| Assess whether you need to use specific compute platforms based on your technology stack. Some compute platforms are better suitable for certain programming languages, tooling, and even operating systems. Strive to use platforms that natively support your technology choices. | You'll avoid the cost of redesigning your architecture, which might include migrating to a new platform.|
| Evaluate the reliability features of the platform and factor in cloud service provider's guarantees into your service level objectives (SLOs). | You can reduce  the risk of localized data center outages by planning for reliability features and using availability zones if available.|

## Tenancy model and isolation

Your SaaS business model will drive whether you host resources for customers or manage them in the customer's environment. Most SaaS providers host resources on behalf of their customers, allowing flexibility in compute platform design. Isolate customer workloads effectively to optimize cost efficiency without compromising customer experience or performance.

### Design considerations

- **Plan your tenancy model**. Your tenancy model determines resource sharing between customers and is influenced by your business and pricing models. Single-tenant models have higher costs per customer compared to fully multitenant models. Your pricing should reflect these differences.
  
- **Customer requirements**. Some customers might have specific needs for data residency, performance guarantees, or security compliance. If these requirements need higher isolation levels than usual, consider how to reflect the increased costs in your business model.
  
- **Noisy neighbor problem**. Be aware of the noisy neighbor problem when sharing resources among tenants, with compute resources being the most affected. For more information, see [Noisy neighbor antipattern](/azure/architecture/antipatterns/noisy-neighbor/noisy-neighbor).

  When choosing a tenancy model, balance the cost savings of resource sharing with the need to guarantee customer performance. Over-sharing resources or allowing excessive consumption can degrade the customer experience. 

### Design recommendations

| Recommendation | Benefit |
| --- | --- |
| Evaluate the isolation features of the compute platform to ensure it meets your tenancy model requirements. | You'll avoid rework by verifying critical configuration upfront. |
| Enforce your isolation model. <br><br>Be cautious with shared resources like local disk caches, system memory, and external caches, as they can unintentionally leak data between tenants if not managed properly. <br><br> For high isolation requirements, enforce isolation within the compute platform and in the application. | Strong isolation reduces the risk of cross-tenant data leakage, a serious security incident.|
| Implement resource governance and monitoring, with visibility of customer-level metrics. <br><br> Proactively monitor each customer's resource consumption to detect and mitigate any noisy neighbor issues. | You will prevent issues from impacting other customers by monitoring resource consumption and mitigating issues early.|

## Configure for scalability and cost efficiency

Your customers can use your application product with different performance profiles. They expect the application to handle increasing user demands, large-scale data, and complex workloads without compromising speed and performance. Your system architecture must ensure scalability and optimal performance, whether managing hundreds or millions of users, while balancing performance needs against costs.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Performance and cost.** Improving performance typically involves adding resources, which increases costs. Review workloads holistically to identify which resources offer the most benefit for the extra cost. For instance, isolating your most important customer on dedicated infrastructure might be worth the additional expense to avoid performance issues from other workloads.

For more information about cost management, see [Billing and cost management for SaaS workloads on Azure](./billing-cost-management.md).

### Design considerations

- **Horizontal and vertical scaling strategies**. Horizontal and vertical scaling approaches are both viable for handling increased load. The approach you use depends on your application's ability to scale across multiple instances.
    - Horizontal scaling involves adding more instances of compute nodes. Your architecture will need a load balancer to distribute incoming traffic across multiple servers or instances.
    - Vertical scaling involves increasing resources (CPU, memory) on a single server. This is suitable for stateful applications that don't support require external state stores like caches. Scaling vertically may cause brief service interruptions and has a resource limit on a single server.
  > Refer to [PE:05 Recommendations for scaling and partitioning](/azure/well-architected/performance-efficiency/scale-partition#choose-a-scaling-strategy). 
  
- **Autoscaling**. Systems need to efficiently handle varying levels of demand. As user traffic increases, your application resources need to scale up to maintain performance; when demand drops, resources scale down to control costs without impacting user experience. Factors like CPU utilization, time of day, or incoming requests guide these adjustments. Autoscaling ensures balanced performance and cost, mitigating the effect of high demand on other tenants.
    > Refer to [RE:06 Recommendations for reliable scaling](/azure/well-architected/reliability/scaling).

- **Capacity planning and compute allocation**. Onboarding new customers to your SaaS workload consumes resource capacity. Even if you scale vertically or horizontally, you'll eventually hit limits in your solution's scalability, such as networking or storage constraints.

  > [!NOTE]
  > The [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp) allows you to deploy multiple independent instances of your solution, providing an additional scaling dimension. It's crucial to understand the capacity of each stamp to determine when to deploy more. This concept is also known as [*bin packing*](/azure/architecture/guide/multitenant/approaches/resource-organization#bin-packing).

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Prefer horizontal scaling over vertical scaling. Horizontal scaling is often less complex, more reliable, and more cost effective than vertical scaling. | Horizontal scaling is often simpler, more reliable, and cost-effective allowing you to scale to a higher degree than vertical scaling.|
| Perform load testing. | Simulating usage can help identify bottlenecks and scaling thresholds before deploying to production.|
| Define the scaling threshold for deploying a new stamp instead of scaling horizontally or vertically. <br><br>For cost-effective scaling without performance loss, condense your tenants onto as few resources as possible.| You'll be better prepared to handle growth beyond your current infrastructure. |
| Implement autoscaling, where possible. Set autoscale rules to reflect your application's load accurately.| You will optimize performance and cost by increasing and decreasing resources as needed. |
| Monitor and evaluate customer usage patterns. | You'll know when to adjust your infrastructure to boost performance or optimize costs.|
| Implement caching mechanisms, where possible.| You'll reduce the potential processing load on the compute layer.|
| Use [cost alerts](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending).| Warnings are useful in detecting high usage and control cost issues early.|
| Use [Azure reservations](/azure/cost-management-billing/reservations/save-compute-costs-reservations) for customers that have long-term commmitments and have guaranteed compute utilization for that entire period.| You'll maximize your cost efficiency on your reserved capacity.|

## Design for resiliency

Resiliency of your compute layer  plays a large part in your overall resiliency strategy. Your application should tolerate and recover from common failures automatically and seamlessly, without user impact.

### Design considerations

- **Reliability requirements**.  Set clear reliability requirements. These include internal targets, known as service-level objectives (SLOs), and customer commitments, known as service-level agreements (SLAs), often specifying uptime targets like 99.9% per month.
  > Refer to [RE:04 Recommendations for defining reliability targets](/azure/well-architected/reliability/metrics).
  
- **Deployment strategy**. Cloud resources are deployed in specific geographic regions. In Azure, availability zones are isolated datacenter sets within a region. For resiliency, deploy applications across multiple availability zones. Deploying across regions or cloud providers further enhances resiliency but adds cost and operational complexity.
  > Refer to [RE:05 Recommendations for using availability zones and regionss](/azure/well-architected/reliability/regions-availability-zones)
  
- **Stateful workloads**. Deploying resilient applications typically involves running redundant copies in different locations. This can be challenging for stateful workloads, which need to maintain session state. Aim to build stateless workloads when possible.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Handle [transient faults](../reliability/handle-transient-faults.md) in your application. | You'll increase your availability by quickly recovering from minor issues. |
| Prefer stateless applications. If your application needs to be stateful, use an external state storage like a cache to store the state.| You will prevent state loss caused by an instance being unavailable, such as during erroneous load balancing or if an instance is unavailable during an outage.|
| Use availability zones.| You'll increase your resiliency by mitigating localized datacenter outages. |
| Design a multi-regional architecture when there's business justification to do so. | You'll meet high uptime requirements and support users in different regions. |
| Perform [chaos engineering](../reliability/testing-strategy.md). | You will better understand where your points of failure are and can correct them before an outage occurs.|
| Limit the use of components that are shared by multiple stamps. | You will eliminate single points of failure and will reduce the potential area of impact for an outage.|

## Additional resources

Multitenancy is a core business methodology for designing SaaS workloads. These articles provide more information about compute platform considerations:
- [Architectural approaches for compute in multitenant solutions](/azure/architecture/guide/multitenant/approaches/compute)
- [Tenancy models](/azure/architecture/guide/multitenant/considerations/tenancy-models)

## Next step

Learn about the networking considerations for SaaS workloads.

> [!div class="nextstepaction"]
> [Design area: Networking for SaaS workloads on Azure](./networking.md)
