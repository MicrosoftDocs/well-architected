---
title: Application Delivery Considerations for Azure Virtual Desktop Workloads
description: Understand Azure Virtual Desktop application platforms. See how to design for scalability, resiliency, efficient resource distribution, and enhanced security.
author: roarrioj
ms.author: roarrioj
ms.date: 01/30/2025
ms.topic: conceptual
ms.service: azure-waf
ms.subservice: waf-workload-azure-virtual-desktop
---

# Application delivery considerations for Azure Virtual Desktop workloads

Effective application delivery helps ensure reliable, cost-effective, and high-performance remote desktops and applications. This article provides guidelines to optimize host pools and session hosts. These recommendations align with the Azure Well-Architected Framework pillars.

## Host pool settings

When you create host pools, there are various configuration settings that  directly affect the performance and efficiency of the environment.

### Host pool types

You can create two types of host pools in Azure Virtual Desktop: 

- **Personal host pools** assign a user to a specific virtual machine (VM). The user connects to the same VM each time. User profile data is stored on the VM's operating system disk. This setup maintains users' application state over time. Personal host pools require a robust backup if a disaster occurs.

- **Pooled host pools** assign multiple users to various VMs within a pool. Users potentially connect to different session hosts each time. FSLogix is required to store user profile data. This setup provides a communal remote experience and enhances efficiency because of the ephemeral nature of the connections. Pooled host pools can also be cheaper. 

To choose a host pool type, see [Host pools](/azure/virtual-desktop/terminology#host-pools).

In general, understand the commonalities between use cases, and standardize host pools to simplify your environment. This approach leads to streamlined operations and makes it easier to meet reliability and performance expectations.

#### Recommendations

|Recommendation|Benefit|
|---|---|
|Understand the criticality of the host pool and the workloads that it supports. Ensure that the combined service-level agreements (SLAs) meet the requirements of the workload. Define each workloads RPO and RTO. Evaluate the benefits and implications of using Active-Active vs. Active-Passive architecture across personal and pooled host pools.| Invoking the appropriate BCDR strategy will ensure that business leaders expectations and end users needs are both met or exceeded. |
|Target a Stage 3 multi-region deployment if regional failure isn't tolerable. <br> Understand how the metadata is replicated across your AVD objects and resources.|Maintain business continuity for your workload(s) and keep users functional even during a regional outage.|
|Onboard your Virtual Machines to AVD Insights using Azure Monitor Agent to monitor your VMs' health.|Observability into the state of your VMs will be crucial for operational health and stability.|
|Use trusted images from the Azure Marketplace and only install software from trusted sources. Regularly update your base image and software. <br><br>For pooled host pools, ensure that you update all session hosts and avoid configuration drift.|Incrases security posture and reduces attack surface from vulnerabilities that could compromise your workload(s). <br><br> Ensures a consistent and realiable user experience no matter the session host a user is brokered to.|
|Apply appropriate segmentation for your sensitive workloads, such as isolating high-sensitivity workloads and running them on confidential compute-series VMs.|Hardware-based TEE hardens guest OS memory and state, increasing your security posture.|
|Understand the cost profile of the host pool's sessionhosts and users, and run cost modeling to create an optimized environment. Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) to get estimates for the solution. Create budgets in Azure to ensure that the deployment aligns with cost estimates.|Establishing budgets helps remove any surprise in costs for AVD, especially if you include future growth or VMs running more than the expected number of hours.|
|Evaluate the cost associated with cloud-based desktop solutions, such as Windows 365.| Cloud-based desktop solutions might be more suitable for personal desktop needs because of their lower management overhead and fixed monthly cost, versus the consumption-based model of Azure Virtual Desktop. |
|Evaluate which applications or users could operate on a multi-session session host. <br><br>Create your use cases with this consideration in mind. You can use FSLogix to restrict application access so that different users get apps than others on the same host pool.|Multi-session host pools usually offer a lower cost per user than Personal Desktop configurations.|
|Use Azure Policy to promote consistency across Azure Virtual Desktop environments, such as VM configurations, enabling AVD Insights, tagging, and much more. This improves resource deployment consistency, avoiding costly redeployments or risks to security posture.|
|Use infrastructure as code (IaC) for deployments. Use resources, such as Azure VM Image Builder and the [Virtual Desktop Accelerator](https://github.com/microsoft/AVDAccelerator). |  These approaches facilitate standardization across multiple host pools, which drives repeatability. They also reduce engineering and operations overhead. |
|Use validation host pools to test incoming AVD updates. Ensure that you have several users that regularly use the environment to ensure thorough testing.|Ensures that any incoming features coming to AVD are tested and validated. If issues arrise, operations can temporarily disable automatic updates of the AVD agents.|


> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off.** Select the appropriate architecture and hostpool types for each user persona that your Azure Virtual Desktop environment supports. Higher resiliency comes with more cost and added complexity, but lower cost also incurs gaps in resiliency for the sake of simplicity. 

> [!NOTE]
> If you use a pooled host pool, you have two load balancing algorithm options. Each one directly affects your cost and performance efficiency.
>
>- **Breadth-first** load balancing distributes user sessions across session hosts. Users are assigned to the session host that has the lowest usage, which can improve the user experience. 
>
>- **Depth-first** load balancing saturates one session host at a time before assigning user sessions to other session hosts, which ensures efficient use of resources. This approach is particularly cost-effective, because it fully uses the capacity of a single host before allocating users to the next session host. Depth-first load balancing is especially beneficial in scale-down scenarios.

## Scaling plans

Use scaling plans in Virtual Desktop to help meet user demand and reduce the cost of maintaining your virtual desktop environment. When you use scaling plans, you can dynamically target your host pool capacity based on schedules, select load balancing behaviors based on the time of day, and manage user sessions' idle, disconnect, and log off experience. Virtual Desktop operators can combine all these controls to balance the user experience, improve performance, and optimize cost efficiency for their Virtual Desktop workloads.

For product documentation, see [Scaling plans](/azure/virtual-desktop/autoscale-scenarios). 

To create a scaling strategy for a pooled host pool, capture the following data points: 

- Total number of users that are assigned to the host pool and the forecasted growth or reduction
- Expected or known peak concurrent user sessions
- Total number of session hosts (VMs) deployed 
- Maximum session limit per session host
- User personas and working schedules

To determine an appropriate scaling strategy, use the total number of assigned users that meet or exceed your total capacity. Find your total capacity by multiplying the maximum session limit by the session host count. Then use your peak concurrent user sessions to determine the right number of session hosts to target across the schedule phases. Most organizations target 80-90% host pool usage, which helps reduce the session host footprint and overdeployment and manage costs.  

The most common method is to align your scaling plan schedules and phases with the user personas that are assigned to your host pool. Incorporate the appropriate load balancing algorithm based on whether you prioritize the user experience or cost.

> [!Important]
> Use a breadth-first approach during your ramp-up period to avoid excessive sign-ins that might overwhelm a session host because of users starting their workday. Then, shift to a depth-first approach during peak, ramp-down, and off-peak hours. This strategy varies based on your organizational goals and workload demands. 

Consider the following example criteria:

- **Host pool type:** Pooled desktop 
- **Total users:**  300 
- **Maximum session limit:**  8 
- **VM size:**  D16as_v5 
- **Total session host count:**  38 (This value is rounded up based on the equation *300 / 8 = 37.5*)  
- **Total host pool capacity:**  304 sessions 
- **Expected peak concurrent sessions:**  280 

The following table shows an example scaling plan based on the preceding criteria:

|Schedule phase|Phase configuration|
|------|------|
|Ramp up| **Start time:** 7 AM <br>**Load balancing algorithm:** Breadth-first <br> **Minimum percent of hosts:** 20% (8 session hosts = 64 sessions available to users)    <br> **Capacity threshold:** 50% (scaling plan turns on more sessions when 40% of the used host pool capacity exceeds 50% = 32 sessions) |
|Peak hours|**Start time:** 9 AM <br> **Load balancing algorithm:** Depth-first  <br> **Capacity threshold:** 50% (fixed based on ramp-up phase) |
|Ramp down| **Start time:** 2 PM <br> **Load balancing algorithm:** Depth-first <br> **Minimum percent of active hosts:** 10% (four session hosts minimum)  <br> **Capacity threshold:** 90%  <br> **Force sign off users:** No <br> **Stop VMs when:** VM has no active sessions |
|Off peak| **Start time:** 8 PM <br> **Minimum percent of active hosts:** 10% (not shown in GUI but reflected in logic) <br> **Load balancing algorithm:** Depth-first <br> **Capacity threshold:** 90%  |

For more scenarios, see [Autoscale scaling plans and example scenarios in Virtual Desktop](/azure/virtual-desktop/autoscale-scenarios#example-scenarios-for-autoscale-for-pooled-host-pools).

Personal host pools assign each user to a dedicated, persistent VM to ensure that they always connect to the same session host. A personal host pool strategy has fewer scaling considerations. Focus your strategy more on user experience and cost management and less on total host pool capacity. To create a scaling strategy for a personal host pool, consider the following factors:

- Determine whether you use the *Start VM on Connect* feature.
- Determine which VMs to start during the ramp-up period.
- Consider disconnect and sign-off settings. 
- Consider hibernation.

#### Recommendations

| Recommendation | Benefit |
|---|---|
| Use Virtual Desktop scaling plans to fine tune scalability and turn off session hosts when there's no demand. <br><br> Have sufficient session hosts to support the demand of your Virtual Desktop workload, which provides desktops or remote apps to your users. <br><br> Understand scale limits of subscriptions, services, and VMs. | Scaling plans help increase scaling speed. They don't deploy or delete existing session hosts. Instead, they automatically turn the hosts off. <br><br> For more information, see [How a scaling plan works](/azure/virtual-desktop/autoscale-scenarios#how-a-scaling-plan-works) and [Recommendations for designing a reliable scaling strategy](../reliability/scaling.md). |
| Align your scaling schedule with the expected load patterns, whether they're static, dynamic, predictable, or surge patterns. | TBD |
| For multi-region deployments, // guidance about scaling plans when users are pinned to a region vs just distribution of load. | TBD |
| Adjust the load balancing algorithm. | An optimized load balancing algorithm helps ensure system stability and prevent overloads. It can also reduce latency, which improves user satisfaction and efficiency. |
| Take advantage of Virtual Desktop Insights to capture your scaling plan diagnostic logs, which populate usage trends and operations. | These metrics help ensure that your scaling plan and schedules remain effective and provide teams with insights so that they can make adjustments accordingly. |
| Implement Azure Monitor alerts to notify stakeholders. Or use action groups to take action if there's a lack of host pool capacity or scaling plan operation failure. | TBD |
| For Greenfield workloads, use a thorough proof of concept and user acceptance testing environment to capture adequate data for performance testing. | The results help you determine the optimal values for the appropriate scaling plan configurations. |
| Review idle time out and disconnect options that minimize disruption when a user returns to work. | This practice helps optimize the user experience. |
| For personal host pools: <br><br> - Set assigned VMs to start during the ramp-up phase. <br> - Take advantage of hibernation.  | VMs that start during the ramp-up phase improve the connection experience during the start of a user's day. <br><br> Hibernation improves the user experience when they return to work and the VM is powered off. |
| For pooled host pools: <br><br> - Use breadth-first load balancing to spread the user sessions across available hosts. <br> - Target 80-90% of the maximum session capacity based on your total assigned users and peak concurrent sessions. <br> - Use exclusion tags on faulty session hosts so that the scaling plan doesn't turn on unhealthy session hosts. <br> - Ensure that your host pool capacity can accommodate sudden demand, unplanned maintenance, or outages.| Breadth-first load balancing reduces the impact to users if a session host goes offline or experiences a critical error. <br><br> An 80-90% capacity helps reduce the session host footprint and overdeployment and helps manage costs. <br><br> TBD |

> [!Note]
> Scaling plans can't create or delete session hosts. If you want to create or delete session hosts, you must create custom automation by using a combination of IaC and continuous integration and continuous delivery (CI/CD) pipelines. 

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off.** Reliable scaling operations have a cost trade-off. Overprovisioning can lead to increased expenses, especially during extended periods of high demand. //Insert something about "Custom autoscaling using PS/CLI/REST API can be developed inhouse if desired. " 

For more information, see [Recommendations for optimizing scaling costs](../cost-optimization/optimize-scaling-costs.md) and [Recommendations for optimizing scaling and partitioning](../performance-efficiency/scale-partition.md).

## Application groups

You can use application groups in Virtual Desktop to manage and publish a set of applications or desktops to users. Application groups define which applications or desktops a user can access and how those resources are presented. Application groups help you organize resources and control access in a scalable manner, improving both security and user experience.

The two types of application groups in Virtual Desktop include: 

- **Desktop application groups:** Provide users access to a full Windows desktop from a session host. Supported by both pooled and personal host pools.

- **RemoteApp application groups:** Allow users to access individual applications published in the group. Supported only by pooled host pools.

When using pooled host pools, both types of application groups can be assigned to the same host pool. However, each host pool can have only one Desktop application group but multiple RemoteApp application groups.


| Recommendation | Benefit |
|---|---|
| Tailor each Virtual Desktop application group to specific user roles or departments. For example, you might create separate application groups for finance, HR, and IT. | Custom application groups help meet business needs effectively. Each application group contains only the applications necessary for their respective workflows. |
| Tailor each Virtual Desktop application group to specific user roles or departments. For example, you might create separate application groups for finance, HR, and IT. | Improves operational efficiency by showing only relevant applications to each user, reducing complexity and streamlining the workspace. Enhances security by enforcing least-privilege access, reducing the risk of data exposure and operational errors. |
| Assign users to Application Groups using Microsoft Entra ID security groups instead of assigning directly.| Simplifies user management and ensures consistency.|
| Set a session limit on each application group to prevent overloading. Keep the number of apps in a RAG below 50.| Minimizes session load and enhances overall performance.|
| Enable Log Analytics to monitor user sessions, application performance, and connection health.| Improves troubleshooting and helps identify performance bottlenecks.|
| Develop a criticality scale from 1 to 5 based on business requirements. For example: <br><br>  - *Level 5:* Applications that are essential to business continuity, such as accounting software <br> - *Level 3:* Noncritical but important tools, such as document editing software <br> - *Level 1:* Optional or infrequently used applications | A criticality scale helps you prioritize maintenance, updates, and availability for application groups. |
| Assign higher priority to application groups that support business-critical operations. Host those application groups on reliable or redundant infrastructure. <br><br> For example, if you rate your finance department's application group a 5 (critical), assign the application group to host pools that have high-availability configurations and fault tolerance, such as availability zones or Azure Premium SSD storage. | Prioritizing business-critical application groups and hosting them on reliable or redundant infrastructure ensures higher availability, minimizes downtime, and enhances business continuity. This approach reduces the risk of service disruptions, providing a more stable and resilient user experience. |
| Establish reliability targets for application groups that support business-critical applications. For example, you can define the uptime, such as 99.9% SLA, and recovery time objectives (RTOs). | Defining reliability targets for business-critical application groups ensures clear expectations for uptime and recovery, enhancing service continuity. By setting SLAs (e.g., 99.9%) and recovery time objectives (RTOs), organizations can proactively plan for resilience, minimize disruptions, and align infrastructure investments with business needs. |
| Assign higher priority to application groups that support business-critical operations. Host those application groups on reliable or redundant infrastructure. <br><br> For example, if you rate your finance department's application group a 5 (critical), assign the application group to host pools that have high-availability configurations and fault tolerance, such as availability zones or Azure Premium SSD storage. | Ensures high availability and fault tolerance for critical applications, reducing downtime and improving business continuity.|
| Establish reliability targets for application groups that support business-critical applications. For example, you can define the uptime, such as 99.9% SLA, and recovery time objectives (RTOs). | Ensures consistent service availability and faster recovery in case of failures. Establishing clear reliability targets helps set user expectations, improve operational planning, and enhance business continuity by minimizing downtime and disruption.|
| Use Azure Site Recovery and Azure Backup for recovery purposes. You can define the RTO and recovery point objectives (RPOs) in Azure Backup policies. | Implementing Azure Site Recovery and Azure Backup enhances disaster recovery capabilities by ensuring data protection and minimizing downtime. Defining RTOs and RPOs within Azure Backup policies helps align recovery strategies with business requirements, reducing data loss and ensuring rapid restoration in case of failures.|
| Assign sensitivity labels to each application group based on the type of data processed and the criticality of the applications. <br><br> For example, for an application group that handles financial data, assign a *Confidential* label. For an application group that has general administrative tools, assign a *General* label. | This practice helps ensure that sensitive applications have higher levels of security and access control. |
| Use Azure Key Vault to securely store application secrets, such as API keys and database credentials, that the applications in application groups use. Ensure that you encrypt secrets to help protect them from unauthorized access. | Using Azure Key Vault to securely store and encrypt application secrets enhances security by protecting sensitive information from unauthorized access. Centralizing secret management reduces the risk of credential exposure, ensures compliance with security best practices, and enables secure secret rotation without requiring changes to application code. |
| Use Azure Key Vault to securely store application secrets, such as API keys and database credentials, that the applications in application groups use. Ensure that you encrypt secrets to help protect them from unauthorized access. |Enhances security by centralizing secret management and encryption. Reduces the risk of credential exposure, simplifies credential rotation, and improves compliance with security policies.|
| Track and monitor the usage patterns of application groups. | This approach helps you identify unused or underused application groups. Use this data to determine when to deallocate resources, which reduces cost. |
| Resize or reduce the number of active VMs that support application groups based on usage patterns. | This practice minimizes the cost of overprovisioned resources. | 
| Take advantage of IaC tools, like Azure Resource Manager templates (ARM templates), Bicep, or Terraform. <br><br> For example, you can define a declarative ARM template that provisions a host pool, assigns session hosts, and configures application groups with specific application assignments and policies. | This approach standardizes the deployment and configuration of host pools and application groups. You can create deployments that are repeatable, scalable, and consistent across environments. |

## Session host settings

Like host pool settings, the settings on the VMs that serve as your session hosts can also affect the performance of your Virtual Desktop environment.

### Regions

The location of a session host correlates directly with the latency that users experience. If you use FSLogix, the distance between your host pool location and the FSLogix storage location also affects the user experience. Deploy session hosts close to user locations.

The region of your session hosts also affects the reliability of your Virtual Desktop environment. It's important to deploy your session hosts with redundancy. You can enable availability zones or virtual machine scale sets.

- Availability zones enhance the resilience of your session hosts against zone outages, but they're limited to specific regions.

- Virtual machine scale sets that have flexible orchestration provide deployment options across multiple zones. Within each zone, you can deploy across different fault domains.

For more information about availability zones and scale sets that have flexible orchestration, see the following articles:

- [Availability zone service support](/azure/reliability/availability-zones-service-support)
- [Availability zone region support](/azure/reliability/availability-zones-region-support)
- [Scale sets with flexible orchestration](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes#scale-sets-with-flexible-orchestration)

### Compute size

Your session host compute size also affects the performance of your environment. Azure offers various compute sizes. There are also many families, architecture types, core counts, storage features, and specialty hardware options like GPUs. Choose the right size for your workload to help achieve optimal performance at an optimal price point.

Some sizes offer special features:

- DCasv5 and ECasv5 are confidential sizes that provide robust high-security features. Examples of these features include hardware-based isolation, encryption, and dedicated virtual trusted-platform modules.

- Some sizes provide GPU support. Use the NV-series, which is backed by NVIDIA Tesla M60 GPUs, if you use frameworks such as OpenGL and DirectX or if you use graphics-intensive applications.

### Storage solutions

Your storage solution also affects the performance of Virtual Desktop. Session hosts use Azure managed disks as virtual hard drives. The types of disks include:

- Azure Premium SSD
- Azure Standard SSD
- Azure Standard HDD

Each disk has its own maximum size, throughput, and input/output operations per second (IOPS). Choose the right disk size and series to help achieve optimal performance at an optimal price point. An optimal disk size can help prevent severe performance problems for users and prevent overspending on performance that you don't use.

The SLA of a disk for the session hosts depends on the disk type. To compare the SLAs of session hosts that use various types of disks, see [Host pool resiliency](/azure/cloud-adoption-framework/scenarios/azure-virtual-desktop/eslz-business-continuity-and-disaster-recovery#host-pool-resiliency).

### Fault tolerance

Fault tolerance is fundamentally centered on the principles of high availability and disaster recovery. To achieve high availability, increase your number of session hosts, especially across different availability zones. If you spread session hosts across availability zones and locations within availability zones, you can decrease the chance that your Virtual Desktop environment becomes unavailable because of maintenance or outage.

For disaster recovery of session hosts, you can use golden images or backups:

- If your session hosts contain data or applications that don't actively need to be saved, use golden images. Save those images in a redundant fashion to help ensure that you can recover session hosts if a disaster occurs.

- If your session host contains valuable data that you update frequently, consider backups to save those changes. The cost backups is considerably higher than the cost of maintaining golden images.

You should also perform a failure mode analysis (FMA) on your environment. With a proper FMA, you can prepare for future outages and help prevent them. Potential failure points include:

- Session hosts that are deployed in a single region. This setup can result in a complete service shutdown during an availability zone outage.

- Personal pool session hosts that don't have backups. Without backups enabled, users can't quickly deploy other identical session hosts and can experience data loss.
- The inability to quickly deploy new session hosts during an outage. If you don't use IaC to save session host information and VM images, you can encounter this problem. Examples of IaC include ARM templates, Bicep, and Terraform.

#### Recommendations

| Recommendation | Benefit |
|---|---|
| Deploy session hosts close to your users. | This practice minimizes latency. |
| Deploy session hosts in an availability zone or a flexible virtual machine scale set. | This approach helps protect your environment from outages. |
| Research the various compute sizes, families, and features that Azure offers. | Appropriate resources optimize the performance and cost efficiency of your workload. |
| Consider VMs in the DCasv5 or ECasv5 series if you run high-security workloads. | These VMs with no local disk provide a better value proposition for workloads where you don't need a local temporary disk. |
| Consider NV-series VMs if you use graphics-intensive applications. | NV-series VMs provide a robust platform for rendering and processing graphics-heavy tasks. |
| Consider the maximum size, throughput, and IOPS of the various types of Azure managed disks when you design your storage solution. | This practice helps optimizing application performance |
| Choose a type of managed disk that optimizes the performance and cost efficiency of your workload. | To simplify provisioning, managing scaling storage. |
| Spread session hosts across different availability zones. | This approach improves availability. |
| Spread out session hosts within availability zones. | To decrease the chance that of environment unavailability due to maintenance or outage. |
| Use golden images that you save in a redundant fashion for disaster recovery if you don't need to back up session host data or applications. | This practice helps with disaster recovery. |
| Use backups for disaster recovery if you update session host data frequently. | This practice helps protect valuable data. |
| Perform an FMA on your environment. | An FMA helps you prepare for future outages and potentially prevent them. |

## Next steps

The following article describes how to establish connectivity, create perimeters for your workload, and evenly distribute traffic to your workloads.

> [!div class="nextstepaction"]
> [Networking and connectivity](./networking.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)
