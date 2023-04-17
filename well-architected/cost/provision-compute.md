---
title: Compute
description: Get cost estimates for compute hosting models such as IaaS, PaaS, or FaaS. Predict cost estimates using the Pricing Calculator in Azure.
author: martinekuan
ms.author: martinek
ms.date: 04/18/2023
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-functions
  - azure
  - azure-cost-management
ms.custom:
  - article
  - internal-intro
---

# Compute cost estimates

*Compute* refers to the hosting model for the computing resources that your application runs on:

- *Infrastructure as a service* (IaaS) lets you provision individual virtual machines (VMs) along with the associated networking and storage components. Then you deploy whatever software and applications you want onto those VMs. This model is the closest to a traditional on-premises environment, except that Microsoft manages the infrastructure. You still manage the individual VMs.

- *Platform as a service* (PaaS) provides a managed hosting environment, where you can deploy your application without needing to manage VMs or networking resources. Azure App Service is a PaaS service.

- *Functions as a service* (FaaS) goes even further in removing the need to worry about the hosting environment. In a FaaS model, you deploy your code and the service automatically runs it. Azure Functions is a FaaS service.

## Hosting models

Whether you're hosting model is IaaS, PaaS, or FaaS, each resource requires you to evaluate the tradeoffs that can affect your cost. To learn more about hosting models, see [Understand the hosting models](/azure/architecture/guide/technology-choices/compute-decision-tree#understand-the-hosting-models).

### What are the cost implications to consider for choosing a hosting model?

If your application can be broken down into short pieces of code, a FaaS hosting model might be the best choice. You're charged only for the time it takes to run your code. For example, [Azure Functions](/azure/azure-functions/functions-overview) is a FaaS service that processes events with serverless code. Azure Functions allows you to run short pieces of code, called *functions*, without worrying about application infrastructure. Use one of the three Azure Functions pricing plans to fit your need. For more information, see [Introduction to Azure Functions](/azure/azure-functions/functions-overview#scenarios)

If you want to deploy a larger or more complex application, PaaS might be the better choice. With PaaS, your application is always running, as opposed to FaaS, where your code runs only when needed. Since PaaS uses more resources, the price increases.

If you're migrating your infrastructure from on-premises to Azure, IaaS greatly reduces and optimizes infrastructure costs and salaries for IT staff who are no longer needed to manage the infrastructure. Since IaaS uses more resources than PaaS and FaaS, your cost could be highest.

### What are the main cost drivers for Azure services?

You're charged differently for each service depending on your region, licensing plan (such as, [Azure Hybrid Benefit for Windows Server](/azure/virtual-machines/windows/hybrid-use-benefit-licensing)), number and type of instances you need, operating system, lifespan, and other parameters required by the service. Assess the need for each compute service by using the flowchart in [Choose a candidate service](/azure/architecture/guide/technology-choices/compute-decision-tree#choose-a-candidate-service).

Consider the tradeoffs that affect your cost by creating different estimates using the Pricing Calculator. If your application consists of multiple workloads, we recommend that you evaluate each workload separately. Refer to [Consider limits and costs](/azure/architecture/guide/technology-choices/compute-decision-tree#consider-limits-and-cost) to perform a more detailed evaluation on service limits, cost, SLAs, and regional availability.

### Are there payment options for Virtual Machines (VMs) to help meet my budget?

The business requirements of your workload drive your best choice. If you have higher SLA requirements, those requirements increase overall costs. You might need more VMs to ensure uptime and connectivity. Other factors that affect cost are region, operating system, and the number of instances you choose. Your cost also depends on the workload life span. For more information, see [Virtual machines](./optimize-vm.md) and [Use Azure Spot Virtual Machines](/azure/virtual-machines/windows/spot-vms).

- *Pay as you go* lets you pay for compute capacity by the second, with no long-term commitment or upfront payments. This option allows you to increase or decrease compute capacity on demand. It's appropriate for applications with short-term, spiky, or unpredictable workloads that can't be interrupted. You can also start or stop usage at any time, resulting in paying only for what you use.

- *Reserved Virtual Machine Instances* lets you purchase a VM for one or three years in a specified region in advance. It's appropriate for applications with steady-state usage. You might save more money compared to pay-as-you-go pricing.

- *Savings plans* let you commit to one or three years, across all regions, for a fixed hourly dollar amount across compute services.

- *Spot pricing* lets you purchase unused compute capacity at major discounts. If your workload can tolerate interruptions, and the execution time is flexible, using spot pricing for VMs can significantly reduce the cost of running your workload in Azure.

- *Dev-Test pricing* offers discounted rates on Azure to support your ongoing development and testing. Dev-Test allows you to quickly create consistent development and test environments through a scalable, on-demand infrastructure. This approach allows you to spin up what you need, when you need it, and explore scenarios before going into production. To learn more about Azure Dev-Test reduced rates, see [Azure Dev/Test Pricing](https://azure.microsoft.com/pricing/dev-test/).

For details on available sizes and options for the Azure VMs that you can use to run your apps and workloads, see [Sizes for virtual machines in Azure](/azure/virtual-machines/sizes). For details on specific Azure VM types, see [Virtual Machine series](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

### Do I pay extra to run large-scale parallel and high-performance computing batch jobs?

Use [Azure Batch](/azure/batch/batch-technical-overview) to run large-scale parallel and high-performance computing (HPC) batch jobs in Azure. You can save on VM cost because multiple apps can run on one VM. Configure your workload with either the Low-priority tier, which is the cheapest option, or the Standard tier, which provides better CPU performance. There's no cost for the Azure Batch service. Charges accrue for the underlying resources that run your batch workloads.

## Use PaaS as an alternative to buying VMs

When you use the IaaS model, you have final control over the VMs. It might appear to be a cheaper option at first, but when you add operational and maintenance costs, the cost increases. When you use the PaaS model, these extra costs are included in the pricing. In some cases, PaaS services can be a cheaper than managing VMs on your own. For help with finding areas in the architecture where it might be natural to incorporate PaaS options, see [Managed services](./design-paas.md).

### How can I cut costs with hosting my web apps in PaaS?

If you host your web apps in PaaS, choose an App Service plan to run your apps. The plan defines the set of compute resources on which your app runs. If you have more than one app, they run using the same resources. This situation is where you see the most significant cost savings, because you don't incur cost for VMs.

If your apps are event-driven with a short-lived process using a microservices architecture style, we recommend using Azure Functions. Execution time and memory for a single function execution determine your cost. For pricing details, see [Azure Functions pricing](https://azure.microsoft.com/pricing/details/functions/).

### Is it more cost-effective to deploy development testing on a PaaS or IaaS hosting model?

If your development testing (dev-test) is built on Azure managed services such as Azure DevOps, Azure SQL Database, Azure Cache for Redis, and Application Insights, the cheapest solution might be using the PaaS hosting model. You don't incur the cost and maintenance of hardware. If your dev-test is built on Azure managed services such as Azure DevOps, Azure DevTest Labs, VMs, and Application Insights, you need to add the cost of the VMs. This cost can greatly increase your cost. For details on evaluating, see [Azure Dev/Test Pricing](https://azure.microsoft.com/pricing/dev-test/).

## A special case of PaaS - Containers

### How can I get the best cost savings for a containerized workload that requires full orchestration?

Your business requirements might require that you store container images to provide fast, scalable retrieval, and network-close deployment of container workloads. Although there are choices as to how you run them, we recommend that you use Azure Kubernetes Service (AKS) to set up instances with a minimum of three nodes. AKS reduces the complexity and operational overhead of managing Kubernetes by offloading much of that responsibility to Azure. There's no charge for AKS cluster management. Any other costs are minimal. The containers themselves have no effect on cost. You pay only for per-second billing and custom machine sizes.

### Can I save money if my containerized workload doesn't need full orchestration?

Your business requirements might not require full orchestration. If not, and you're using App Service containers, we recommend that you use one of the App Service plans. Choose the appropriate plan based on your environment and workload.

There's no charge to use SNI-based TLS/SSL. Standard and Premium service plans include the right to use one IP TLS/SSL at no extra charge. Free and shared service plans don't support TLS/SSL. You can purchase the right to use more TLS/SSL connections. In all cases, the TLS/SSL certificate itself must be purchased separately. To learn more about each plan, see [App Services pricing](https://azure.microsoft.com/pricing/details/app-service/windows/).

### Where's the savings if my workload is event driven with a short-lived process?

In this example, Service Fabric might be a better choice than AKS. The biggest difference between the two is that AKS works only with Docker applications using Kubernetes. Service Fabric works with microservices and supports many different runtime strategies. Service Fabric can deploy Docker and Windows Server containers. Like AKS, Service Fabric is built for the microservice and event-driven architectures. AKS is strictly a service orchestrator and handles deployments, whereas Service Fabric also offers a development framework that allows building stateful/stateless applications.

## Predict cost estimates using the pricing calculator

Use the Pricing Calculator to create your total cost estimate. After you run your initial scenario, you might find that your plan is beyond the scope of your budget. You can adjust the overall cost and create various cost scenarios to make sure your needs are met before you commit to purchasing.

> [!NOTE]
> The costs in this example are based on the current price and are subject to change. The calculation is for illustration purposes only. It shows the Collapsed view of the cost in this estimate.

![Azure Pricing Calculator - Collapsed view](../_images/pricing-calc-collapsed.png)

:::image type="content" source="../_images/pricing-calc-collapsed.png" alt-text="Screenshot shows the Azure pricing calculator in collasped view with illustration values only." lightbox="../_images/pricing-calc-collapsed.png":::

> [!TIP]
> You can start building your cost estimate at any time and re-visit it later. The changes are saved until you modify or delete your estimate.

## Next steps

- [Provisioning cloud resources to optimize cost](./provision-checklist.md)
- [Virtual machines in Azure](/azure/virtual-machines/)
- [Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#a-series)
- [Pricing calculator](https://azure.microsoft.com/pricing/calculator/)
