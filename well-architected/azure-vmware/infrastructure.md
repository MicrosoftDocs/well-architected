---
title: Infrastructure considerations for Azure VMware Solution workloads
description: Understand Azure VMware Solution infrastructure concepts. See how to design for resilience, security, scalability, automation, and disaster recovery.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 08/16/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Infrastructure and provisioning considerations for Azure VMware Solution workloads

This article discusses the infrastructure design area of an Azure VMware Solution offering, which refers to the foundational layer. This layer supports the compute, storage, and networking capabilities that you need to run VMware vSphere workloads efficiently and reliably. This article also explains how to use the VMware software-defined data center (SDDC) stack for resilience, security, scalability, automation, and disaster recovery. The VMware SDDC stack includes:

- VMware ESXi
- VMware vCenter Server
- VMware NSX-T Data Center
- VMware vSAN
- VMware HCX (optional)
- VMware SRM (optional)

## Calculate business targets

*Impact: Reliability, Performance efficiency, Operational excellence*

Availability targets such as service-level agreements (SLAs) for Azure VMware Solution applications should be defined for your platform and in place.

Use recovery targets like a recovery time objective (RTO) to identify how long Azure VMware Solution can be unavailable. Use a recovery point objective (RPO) to identify how much data loss is acceptable during a disaster.

Consider the following points when you design your solution:

- **Critical path dependencies**. Not all components of a solution are equally critical. Clearly differentiate between the dependencies that can take down the system and the dependencies that might lead to a degraded experience. The design should harden the resiliency of critical components to minimize the impact of outages.

- **Scaling out and scaling in on demand**. The environment should be able to expand and contract based on load. Handle these operations through automation. User input should be kept to a minimum to avoid typical errors caused by humans.

##### Recommendations

- Establish an SLA that's appropriate for your platform.
- Define RTO and RPO targets.
- Design critical components to be resilient.
- Use automation for scaling in and out.

## Consider workload resources and scale requirements

*Impact: Performance efficiency, Operational excellence*

Before you deploy a workload, you need to have a general understanding of the components that are required to support Azure VMware Solution. To achieve this understanding, you need to carefully consider your workload's characteristics, resources, and scale requirements. These factors make up the overall cluster design. Also, ensure that Azure VMware Solution is the right choice for your infrastructure deployment. There are scenarios where migrating workloads to an infrastructure as a service (IaaS) or platform as a service (PaaS) solution in Azure is more cost-effective and performant than migrating to Azure VMware Solution.

##### Recommendations

- Assess Azure native solutions before moving to Azure VMware Solution.
- Use the [Azure VMware Solution deployment planning guide checklist](/azure/azure-vmware/plan-private-cloud-deployment) and read the [Azure VMware Solution documentation](/azure/azure-vmware).
- Set up criteria to determine which workloads to move to Azure VMware Solution and which to move to Azure native solutions. Consider costs, the ability to reassign IP capacity, and usage patterns in these criteria.

## Use thick and thin provisioning

*Impact: Performance efficiency*

When you provision infrastructure in the SDDC, the primary focus is on the hosts, which are the underlying compute and storage for the virtual machines (VMs). In Azure VMware Solution, you can choose either thin provisioning or thick provisioning for the virtual disks of your VMs. The choice that you make depends on your specific storage requirements, performance considerations, and anticipated VM growth. Thin provisioning optimizes storage usage by allocating storage on demand. This approach minimizes unused space. In contrast, from the start, thick provisioning uses the full amount of storage space that you allocate and reserve for each VM.

##### Recommendations

- If storage efficiency is a priority and you want to minimize unused storage consumption, use thin provisioning.
- If your application requires consistent and high-performance storage access, consider thick provisioning.

## Plan capacity and resource usage

*Impact: Performance efficiency*

Before you deploy an application in Azure VMware Solution, it's crucial to ensure proper sizing and capacity planning. Specifically, consider scalability requirements, growth projections, and performance considerations.

##### Recommendations

- Before you migrate to Azure, use [Azure Migrate](https://learn.microsoft.com/azure/migrate/how-to-create-azure-vmware-solution-assessment) to get insights into resource usage and recommendations for sizing.
- Analyze resource utilization patterns over a specific time frame to help establish baseline usage, identify peak periods, and predict resource spikes.
- Create a dependency map that outlines the components on your critical path. Actively maintain the map, and regularly check it for changes in the solution.

## Select regions

*Impact: Performance efficiency, Security*

To ensure that users are near your solution, carefully consider which regions to select. Having users physically close to your peering location minimizes latency and helps you meet requirements. For example, if you use VMware HCX, there's a requirement that the roundtrip latency must be less than 150 ms.

It's also important to consider regulatory requirements during region selection. Regulatory requirements and data residency restrictions can vary among regions. When you use Azure geo-replicated storage, you can take advantage of paired regions. Under this concept, each Azure region is paired with another region within the same geography. Data that's replicated resides within the same geography as its pair for tax and law enforcement jurisdiction purposes.

##### Recommendations

- Select a region that's close to your users or other Azure services to help minimize latency.
- Understand the cloud [shared responsibility](https://azure.microsoft.com/resources/shared-responsibility-for-cloud-computing/) model for industry or region-based regulatory compliance.
- Ensure that your data remains in the correct geopolitical zone when you use Azure data services.

## Use a scalable design

*Impact: Reliability, Performance efficiency*

When you size for an application, size the VM to handle the workload at peak performance. During an outage, the application should also be able to operate with reduced functionality or degraded performance. To prepare for a failure event, design for resilience. Design your system to respond to outages and deliver reliability even when regional, zonal, service, or component failures impact critical application functionality. Vertical scaling is the ability of a VM to add resources to individual hosts. Vertical scaling requires picking the right SKU, powering the host down, and adding resources from a VMware ESXi host that has those resources available.

The downtime that's associated with vertical scaling can disrupt your business, so consider horizontal scaling in your workload design. Horizontal scaling is the ability to dynamically span your workload across multiple VMs. Horizontal scaling typically involves using VMware vSphere features like resource allocation settings, VM templates, cloning, or dynamic resource allocation techniques. For example, if you want to distribute traffic across three separate VMs, place those VMs on three separate hosts for high availability.

Clustering is concerned with creating logical groupings of hosts to provide advanced management and availability features. After you provision your hosts, you can create and configure VMware vSphere clusters within the Azure VMware Solution environment to manage VMs and provide compute capabilities.

##### Recommendations

- Use the [custom GitHub autoscale add-on for Azure VMware Solution](https://github.com/Azure/azure-vmware-solution/tree/main/avs-autoscale) to define performance metrics to use for scale-in or scale-out operations in Azure VMware Solution cluster nodes.

- Use placement policy affinity rules to help ensure resource availability. Configuring affinity rules gives administrators control over VM placement. The VMs can then be distributed according to specific requirements, performance considerations, availability needs, or licensing constraints.

## Implement high availability

*Impact: Reliability, Performance efficiency*

When you deploy VMs with high availability or clustering within Azure VMware Solution, we recommend that you create anti-affinity rules to keep the VMs apart and on separate hosts. A stretched cluster primarily relates to computing resource distribution across fault domains or availability zones. Another aspect to consider is the latency of connecting to workloads. Some workloads might not be sensitive to latency.

##### Recommendations

- Use stretched clusters for high availability.
- Colocate the application and service tiers by ensuring that the application, database, and storage tiers are in the same availability zone.
- Choose the Azure region for deploying an Azure VMware Solution cluster carefully. Consider the proximity to your users or other resources, network connectivity options, and latency requirements.

## Use affinity rules

*Impact: Reliability*

If one host experiences an issue or failure, anti-affinity rules enforce distribution across multiple hosts. This distribution helps to limit the impact of the failure and maintain the availability of applications and services.

##### Recommendations

- When a low-latency communication path is required between VMs, use placement policy affinity rules to keep the VMs on the same host.
- Use placement policy VM-VM affinity when VMs that support your application need fault tolerance, or when you want to optimize host performance through resource distribution.
- For VMs that are deployed with high availability or clustering within Azure VMware Solution, create placement policy VM-VM anti-affinity rules to keep those VMs apart and on separate hosts.

## Deploy VMware vSAN

*Impact: Reliability, Performance efficiency*

To design a well-architected Azure VMware Solution with storage, you need to plan for adequate data protection and redundancy.

An Azure VMware Solution virtual storage area network (vSAN) uses local storage resources from the VMware ESXi hosts within an Azure VMware Solution cluster to create a distributed, shared-storage infrastructure. The vSAN provisioning must adequately meet current and future storage needs. You can use storage area network (SAN) storage policies to define the characteristics and behaviors of the storage that your VMs use. You can use policies to configure data protection, performance, and space efficiency settings according to the specific requirements of your workloads. The default storage policy in Azure VMware Solution is redundant. As a result, if your machines require copying data to extra vSAN nodes, create another policy to ensure that the data meets your enhanced redundancy requirements.

##### Recommendations

- Determine your failures to tolerate (FTT) level, or your failure tolerance, based on your desired resiliency.
- Choose a redundant array of independent disks (RAID) configuration that helps ensure data availability and protection against host and disk failures.

## Use Azure NetApp Files

*Impact: Reliability, Performance efficiency*

When you plan to exceed the storage in your private cloud, Azure NetApp Files in Azure VMware Solution is another solution that expands disk allocation and provides a high-performance, low-latency, scalable storage platform. Azure NetApp Files dynamically adjusts the storage capacity and performance tiers based on your workload needs. As a result, the Azure VMware Solution environment can scale as your storage requirements grow.

Ensure that Azure services such as Azure NetApp Files that interact with Azure VMware Solution are in the same zone that Azure VMware Solution is deployed in. If all or part of the application is highly sensitive to latency, it might mandate component colocation. In that case, the applicability of multi-region and multi-zone strategies is limited. But colocation reduces latency, so applications respond more quickly. An example is when you use a datastore that's based on Azure NetApp Files where colocation is critical for disk expansion.

##### Recommendations

- Consider using Azure NetApp Files or a third-party solution as an extra datastore for Azure VMware Solution.
- Take an application assessment to help determine your optimal combination of Azure VMware Solution nodes and external storage solutions like Azure NetApp Files.

##### Recommendations

- Have a procedure in place to help ensure no data is lost during a node replacement process.

## Establish your baseline performance

*Impact: Operational excellence*

Establishing a performance baseline provides insight into the capabilities of Azure VMware Solution and helps you identify performance constraints.

##### Recommendations

- Use tools to benchmark your existing environment before you migrate to the Azure VMware Solution private cloud. Some common utilities that you can use to establish baseline performance include [VMware vRealize Operations](/azure/azure-vmware/vrealize-operations-for-azure-vmware-solution), [Perfmon](/message-analyzer/perfmon-viewer), and [iostat](https://linux.die.net/man/1/iostat).
- Use a [performance-based assessment](/azure/migrate/best-practices-assessment#sizing-criteria) when you estimate Azure VMware Solution private cloud capacity.

## Use debugging and troubleshooting tools

*Impact: Operational excellence*

Having a systematic approach to identifying, troubleshooting, and fixing problems in the SDDC leads to faster resolution times. Operations teams must be able to define the problem or symptom that the workload is experiencing and the scope of the issue. They also need to be able to collect information, including error messages, logs, and any specific conditions or actions that trigger the issue.

For detailed coverage of infrastructure monitoring, see [Monitoring considerations for Azure VMware Solution workloads](./monitoring.md).
  
##### Recommendations

- Familiarize yourself with the following debugging and troubleshooting tools. These tools are indispensable for identifying potential performance bottlenecks quickly.
  - [Azure Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview)
  - [Kusto Query Language](/azure/data-explorer/kusto/query/tutorials/learn-common-operators?pivots=azuremonitor)
  - [PsPing](/sysinternals/downloads/psping)

## Next steps

Now that you've examined the underlying Azure VMware Solution platform, investigate the application platform, which includes databases, VMs, operating systems, and configurations.

> [!div class="nextstepaction"]
> [Application platform](./application-platform.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)
