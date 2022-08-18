---
title: Application platform considerations for sustainable workloads on Azure
description: This design area explores application platform and infrastructure considerations for sustainable workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/29/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - networking
products: Azure
ms.custom:
  - sustainability
---

# Application platform considerations for sustainable workloads on Azure

Designing and building sustainable workloads requires understanding the platform where you're deploying the applications. Review the considerations and recommendations in this section to know how to make better informed platform-related decisions around sustainability.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Platform and service updates

Keep platform and services up to date to leverage the latest performance improvements and optimizations.

### Design considerations

- Platform updates enable you to use the latest functionality and features to help increase efficiency. Running on outdated software can result in running a suboptimal workload with unnecessary performance issues. New software tends to be more efficient in general.

### Design recommendations

- Review platform and service updates regularly.
  - Upgrade to newer and more efficient services as they become available.
  - Consider backward compatibility and hardware reusability. An upgrade may not be the most efficient solution if the hardware or the OS isn't supported.
  - Make use of [Azure Automation Update Management](/azure/automation/update-management/manage-updates-for-vm) to ensure software updates are deployed to Azure VMs.

## Regional differences

The Microsoft Azure data centers are geographically spread across the planet and powered using different energy sources. Making decisions around where to deploy your workloads can significantly impact the emissions your solutions produce.

### Design considerations

- Learn about what Azure regions have a lower carbon footprint than others to make better-informed decisions about where and how our workloads process data.
  - Some regions on the planet are more carbon intense than others, so it's important to consider where we deploy our workloads and combine this with other business requirements.

### Design recommendations

- Deploy to low-carbon regions.
  - Use less carbon because the data centers where you deploy the workload are more likely to be powered by renewable and low-carbon energy sources.
  - Potential tradeoffs:
    - The effort and time it takes to move to a low-carbon region.
    - Migrating data between data centers may not be carbon efficient.
    - Consider the cost for new regions, including low-carbon regions, which may be more expensive.
    - If the workloads are latency sensitive, moving to a lower carbon region may not be an option.

- Process when the carbon intensity is low.
  - Optimizing workloads to run when knowing that the energy mix comes mostly from renewable energy sources.
  - For example, running specific workloads at night may be more beneficial in some regions.

- Choose data centers close to the customer.
  - Network traversal increases if the data center is a greater distance from consumers.

- Run [batch workloads](/azure/architecture/data-guide/big-data/batch-processing) during low carbon intensity periods.
  - Potential tradeoffs may include the effort and time it takes to move to a low-carbon region. Additionally, migrating data between data centers may not be carbon efficient, and the cost for new regions&mdash;including low-carbon regions&mdash;may be more expensive.

## Modernization

Consider these platform design decisions when choosing how to operate workloads. Leveraging managed services and highly optimized platforms in Azure helps build cloud-native applications that inherently contribute to a better sustainability posture.

### Design considerations

- Managed services are highly optimized and operate on more efficient hardware than other options, contributing to a lower carbon impact.

- Consider options for containerizing workloads to reduce unnecessary resource allocation and to utilize the deployed resources better.

- Think about the unused capacity in Azure data centers. Utilizing the otherwise wasted capacity&mdash;at significantly reduced prices&mdash;the workload contributes to a more sustainable platform design.

### Design recommendations

- Containerize workloads where applicable.
  - Deploying apps as containers allows for bin packing and getting more out of a VM, ultimately reducing the need for duplication of libraries on the host OS.
  - Removes the overhead of managing an entire VM and allows to get more apps deployed per physical machine. Containerization also optimizes server utilization rates and improves service reliability, lowering operational costs. Fewer servers are needed, and the existing servers can be better utilized.
  - Consider these tradeoffs: The benefit of containerization will only realize if the utilization is high. Additionally, provisioning an orchestrator such as [Azure Kubernetes Services](/azure/aks/) (AKS) or [Azure Red Had OpenShift](/azure/openshift/) (ARO) for only a few containers would likely lead to higher emissions overall.

- Evaluate moving to PaaS and serverless compute workloads.
  - Build a cloud-native app without managing the infrastructure, using a fully managed platform. The platform handles scaling, availability, and performance, ultimately optimizing the hardware efficiency.

- Use SPOT VMs where possible.
  - By utilizing [SPOT VMs](/azure/virtual-machines/spot-vms), you take advantage of unused capacity in Azure data centers while getting a significant discount on the VM.
  - Consider the tradeoff: When Azure needs the capacity back, the VMs get evicted. Learn more about the SPOT VM [eviction policy](/azure/virtual-machines/spot-vms#eviction-policy).

## Right sizing

Ensuring workloads use all the allocated resources helps deliver a more sustainable workload. Oversized services are a common cause of more carbon emissions.

### Design considerations

- Operating idle workloads will waste energy and contributes to an added carbon emission.

- It's not uncommon with oversized compute workloads where much of the capacity is never utilized, ultimately leading to a waste of energy.

### Design recommendations

- Turn off workloads outside of business hours.
  - Dev &amp; Testing workloads should be turned off when not used. Instead of leaving them running, consider shutting them off outside regular business hours.
  - Learn more about [starting/stopping VMs during off-hours](/azure/automation/automation-solution-vm-management).

- Utilize [auto-scaling](/azure/architecture/best-practices/auto-scaling) and bursting capabilities.
  - Consider that it may require tuning to prevent unnecessary scaling during short bursts of high demand, as opposed to a static increase in demand.
  - Consider the application architecture as part of scaling considerations. For example, logical components should scale independently to match the demand of that component, as opposed to scaling the entire application if only a portion of the components needs scaling.

## Next step

Review the design considerations for deployment and testing.

> [!div class="nextstepaction"]
> [Deployment and testing](sustainability-deployment-testing.md)
