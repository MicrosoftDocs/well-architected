---
title: Initial cost estimate
description: Learn best practices and strategies for modeling workloads and estimating initial costs for cloud migrations.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 04/19/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-optimization
products:
  - azure-kubernetes-service
  - azure
  - azure-cost-management
ms.custom:
  - article
  - internal-intro
---

# Estimate the initial cost

It's difficult to know your costs before deploying a workload to the cloud. If you use methods for on-premises estimation or you directly map on-premises assets to cloud resources, estimates are inaccurate. For example, if you build your own datacenter, your costs might appear comparable to cloud. Most on-premises estimates don't account for costs like cooling, electricity, IT and facilities labor, security, and disaster recovery.

Here are some best practices:

- Use proof-of-concept deployments to help refine cost estimates.
- Choose the right resources that can handle workload performance. For example, cheaper virtual machines might initially show a lower cost but be more expensive to maintain a certain performance level.
- Accurately reflect the cost associated with right storage type.
- Add hidden costs, such as networking expenses for large data downloads.

## Migration workloads

Quantify the cost of running your business in Azure by calculating total cost ownership (TCO) and the return on investment (ROI). Compare those metrics to existing on-premises equivalents.

It's difficult to know costs before migrating to the cloud.

Using on-premises calculation might not accurately reflect the cost of cloud resources. Here are some challenges:

- On-premises TCO might not accurately account for hidden expenses. These expenses include under-utilization of purchased hardware or network maintenance costs like labor and equipment failure.
- Cloud TCO might not accurately account for a drop in the organization's operational labor hours. Cloud provider's infrastructure, platform management services, and other operational efficiencies are included in the cloud service pricing. Especially at a smaller scale, the cloud provider's services don't result in reduction of IT head count.
- ROI might not accurately account for new organizational benefits because of cloud capabilities. It's hard to quantify improved collaboration, reduced time to service customers, and fast scaling with minimal or no downtime.
- ROI might not accurately account for the business process re-engineering required to fully adopt cloud benefits. In some cases, this re-engineering might not occur at all, leaving an organization in a state where they use new technology inefficiently.

Azure provides the following tools to determine cost.

- [Microsoft Azure Total Cost of Ownership Calculator](https://azure.microsoft.com/pricing/tco/calculator/) to reflect all costs.
    > For migration projects, the TCO Calculator might help, because it populates some common costs but it lets you modify the cost assumptions.
- [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator) to assess costs of the services you plan to use in your solution.
- [Azure Migrate](/azure/migrate/migrate-services-overview) to evaluate your organization's current workloads in on-premises datacenters. Azure Migrate suggests an Azure replacement solution with virtual machine sizes based on your workload. It also provides a cost estimate.

## Example estimate for a microservices workload

Consider this [scenario](/azure/architecture/microservices/design/index#scenario) as an example. Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) to estimate the initial cost before the workload deploys. The cost calculates per month or for 730 hours.

In this example, we've chosen the microservices pattern. As the container orchestrator, one of the options could be [Azure Kubernetes Service](/azure/aks/) (AKS) that manages a cluster of pods. Choose NGINX ingress controller because it's a well-known controller for such workloads.

> The example uses current prices and is subject to change. The example shows prices and calculations for illustrative purposes only.

### Compute

For AKS, there's no charge for cluster management.

For AKS agent nodes, there are many instance sizes and SKUs options. The example workload is expected to follow a long running pattern and you can commit to three years. An instance that's eligible for [reserved instances](/azure/cost-management-billing/manage/understand-vm-reservation-charges#services-that-get-vm-reservation-discounts) would be a good choice. Lower the cost by choosing the **3-year reserved plan**.

The workload needs two virtual machines. One is for the backend services and the other is for the utility services.

The B12MS instance with two virtual machines is sufficient for this initial estimation. We can lower cost by choosing reserved instances.

**Estimated Total: $327.17 per month with upfront payment of $11,778.17**.

### Application gateway

For this scenario, consider the **Standard_v2 Tier** of Azure Application Gateway because of the autoscaling capabilities and performance benefits. Also, choose consumption-based pricing, which calculates by capacity units (CU). Each capacity unit calculates based on compute, persistent connections, or throughput. For Standard_v2 SKU, each compute unit can handle approximately 50 connections per second with RSA 2048-bit key TLS certificate. For this workload, estimate 10 capacity units.

**Estimated Total: $248.64 per month.**

### Load balancer

NGINX ingress controller deploys a load balancer that routes internet traffic to the ingress. Approximately 15 load balancer rules are needed. NAT rules are free. The main cost driver is the amount of data processed inbound and outbound independent of rules. The estimated traffic is 1 TB (inbound and outbound).

**Estimated Total: $96.37 per month.**

### Bandwidth

The estimated outbound traffic is 2 TB. The first 5 GB/month are free in Zone 1, which includes North America, Europe, and Australia. Between 5 GB and 10 TB /month is charged $0.087 per GB.

**Estimated Total: $177.74 per month**

### External data source

Because the schema-on read nature of the data handled by the workload, choose Azure Cosmos DB as the external data store. By using the [Azure Cosmos DB capacity calculator](https://cosmos.azure.com/capacitycalculator/), you can calculate the throughput to reserve.

![Azure Cosmos DB capacity calculator](../_images/cosmosdb-capacity.png)

#### Cost variables

- For lower latency, in this scenario, enable geo-replication by using the **Multi-regions writes** feature. By default, Azure Cosmos DB uses one region for writes and the rest for reads.

- Default choices in consistency model of **Session** and indexing policy to **Automatic**. Automatic indexing makes Azure Cosmos DB indexes all properties in all items for flexible and efficient queries. Custom indexing policy lets you include and exclude properties from the index for lower write request units (RUs) and storage size. Uploading a custom indexing policy can help you reduce costs.

- Total data store isn't a significant cost driver. Here it's set to 500 GB.

- The throughput is variable indicating peaks. The percentage of time at peak is set to 10%.

- The item size is an average of 90k. Using the capacity calculator, you can upload sample json files. The sample json files include your document's data structure, the average document's size, and the number of reads and writes per second. These variables have the largest effect on cost because you use them to calculate the throughput. The throughput values appear in the image.

Now, use those values in the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/).

![Azure pricing calculator for Azure Cosmos DB](../_images/cosmosdb-price.png)

The average throughput based on these settings is 20,000 RUs per second, which is the minimum throughput required for a **3-year reserved capacity** plan.

Here's the total cost for three years using the reserved plan:

$1,635.20 Average per month ($58,867.20 charged upfront)

You save $700.00 by choosing the three-year reserved capacity over the pay-as-you-go price.

### CI/CD pipelines

With Azure DevOps, _Basic plan_ is included for Visual Studio Enterprise, Professional, Test Professional, and MSDN Platforms subscribers. And there's no charge for adding or editing work items and bugs and viewing dashboards, backlogs, and Kanban boards for stakeholders.

The Basic plan license for five users is free.

#### More services

For Microsoft Hosted Pipelines, the **Free** tier includes one parallel CI/CD job with 1,800 minutes (30 hours) per month. But you can select the **Paid** tier and have one CI/CD parallel job ($40.00) in this tier. Each parallel CI/CD job includes unlimited minutes.

> For this stage of cost estimation, the self-hosted pipelines isn't required because the workload doesn't have custom software that runs in your build process and which isn't included in the Microsoft-hosted option.

Azure Artifacts is a service you can use to create package feeds to publish and consume Maven, npm, NuGet, Python, and universal packages. Azure Artifacts bills on a consumption basis, and is free up to 2 GB of storage. For this scenario, we estimate 56 GB in artifacts ($56.00)

Azure DevOps offers a cloud-based solution for load testing your apps. Load tests are measured and billed in virtual user minutes (VUMs). For this scenario, estimate a 200,000 VUMs ($72.00).

**Estimated Total: $168.00 per month**
