---
title: Application delivery considerations for Azure Virtual Desktop workloads
description: Understand Azure Virtual Desktop application platforms. See how to design for scalability, resiliency, efficient resource distribution, and enhanced security.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 10/12/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Application delivery considerations for Azure Virtual Desktop workloads

This article discusses the application delivery design area of an Azure Virtual Desktop workload. Considering application delivery is crucial when you deploy a new Azure Virtual Desktop environment or update an existing one. Application delivery enables organizations to provide remote desktops and applications to users in a reliable, cost-effective, and performant manner.

Review the considerations and recommendations in this article to learn how to optimize your host pools and session hosts. These guidelines reflect and extend the quality pillars of the Azure Well-Architected Framework—Reliability, Security, Cost Optimization, Operational Excellence, and Performance Efficiency. Adhering to these considerations and recommendations helps ensure a robust and efficient Azure Virtual Desktop environment.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-an-azure-virtual-desktop-workload).

## Host pool settings

When you create host pools, various settings directly affect the performance and efficiency of your Azure Virtual Desktop environment.

### Host pool types

*Impact: Cost Optimization, Reliability*

In Azure Virtual Desktop, you can create two types of host pools:

- **Personal** host pools assign a specific user to a specific virtual machine (VM). With this setup, the user connects to the same machine each time, and the user profile data is stored directly on the operating system disk of the VM. This scenario necessitates a robust backup solution to ensure that all user modifications are stored and can be restored if there's a disaster. This type of host pool is useful for scenarios where users need to maintain their application state over time.
- **Pooled** host pools provide a way for multiple users to connect to different VMs in a pool. Because users can connect to different session hosts with each connection, the admin needs to configure and use FSLogix to store user profile data. Host pools that are pooled offer a communal remote experience to users, promoting cost-effectiveness and increased efficiency.

Each type of host pool comes with its own set of pros and cons. It's important to carefully select the type of host pool by thoroughly evaluating the functionalities that users need.

##### Recommendations

- Consider using a personal pool if you aim to give users power to personalize their environment and work freely within a VM.
- Use a pooled host pool to streamline your reliability solution and minimize costs.

### Load-balancing algorithms

*Impact: Cost Optimization, Performance Efficiency*

If you use a pooled host pool, there are two types of load-balancing algorithms that you can use. Each one directly affects your cost and performance efficiency.

- **Breadth-first** load balancing distributes user sessions across session hosts. Users are assigned to the session host with the lowest usage, which can improve the user experience.
- **Depth-first** load balancing saturates one session host at a time before assigning user sessions to other session hosts, ensuring efficient use of resources. This approach is particularly cost-effective, because it fully uses the capacity of a single host before allocating users to the next session host. It's especially beneficial in scale-down scenarios.

##### Recommendations

- Use breadth-first load balancing to quickly improve your user experience.
- Use depth-first load balancing for a cost-effective approach.
- Use depth-first load balancing for scale-down scenarios.

### Scaling plans

*Impact: Cost Optimization, Performance Efficiency*

Scaling plans in Azure Virtual Desktop can help you meet user demand and reduce the cost of maintaining your virtual desktop environment. When you use scaling plans, you can adjust the minimum and maximum percentage of hosts and the capacity threshold. By changing these settings, you can optimize the number of session hosts that are online and ready to accept user sessions.

Using the scaling plans in an optimal way helps you:

- Have an adequate number of session hosts ready to serve your users.
- Turn off session hosts when users no longer need your service.

You can also change the load-balancing algorithm to further improve your cost efficiency.

Unlike Azure Virtual Machine Scale Sets, scaling plans don't deploy or delete existing session hosts. Instead, the plans automatically turn the hosts off and on to help you maximize your cost efficiency.

It's important to set an adequate maximum number of session hosts from the beginning. This practice helps ensure that your Azure Virtual Desktop environment meets performance needs but can also scale down resources when they're not needed. Scaling down excessively can be harmful. For instance, in some scenarios, there might not be enough session hosts ready to serve the user. In that case, users might experience a performance degradation or not be able to connect to the Azure Virtual Desktop environment. As a result, it's important to determine ideal values for the lowest point that you can scale down to and highest point that you can scale up to.

##### Recommendations

- Use scaling plans, which automatically turn hosts off and on to help ensure adequate performance for users.
- Adjust the settings of scaling plans to improve cost efficiency.

## Session host settings

Like host pool settings, the settings on the VMs that serve as your session hosts can also affect the performance of your Azure Virtual Desktop environment.

### Regions

*Impact: Reliability, Performance Efficiency*

The location of a session host correlates directly with the latency that end users experience. If you use FSLogix, the distance between your host pool location and the FSLogix storage location also affects your end-user experience. Deploy session hosts close to user locations.

The region of your session hosts also affects the reliability of your Azure Virtual Desktop environment. It's important to deploy your session hosts with redundancy. We recommend enabling availability zones.

- Availability zones enhance the resilience of your session hosts against zone outages, but they're limited to specific regions.
- Availability sets provide protection against specific fault domain and update domain failures, but they don't protect your environment from zone outages.

For more information about availability zones and availability sets, see the following resources:

- [Availability zone service and regional support](/azure/reliability/availability-zones-service-support)
- [Availability sets overview](/azure/virtual-machines/availability-set-overview)

##### Recommendations

- Deploy session hosts close to your users to minimize latency.
- Deploy session hosts in an availability zone or an availability set to help protect your environment from outages.

### Compute size

*Impact: Cost Optimization, Performance Efficiency*

Your session host compute size also affects the performance of your environment. Azure offers various compute sizes. There are also many families, architecture types, core counts, storage features, and specialty hardware options like GPUs that are available. Choosing the right size for your workload helps you achieve optimal performance at an optimal price point.

Some sizes offer special features:

- DCasv5 and ECasv5 are confidential sizes that provide robust high-security features. Examples of these features include hardware-based isolation, encryption, and dedicated virtual trusted-platform modules.
- Certain sizes provide GPU support. The NV-series, which is backed by NVIDIA Tesla M60 GPUs, can be helpful if you use frameworks such as OpenGL and DirectX, or in general, if you use graphics-intensive applications.

##### Recommendations

- Look at the various compute sizes, families, and features that Azure offers, and choose the option that optimizes the performance and cost efficiency of your workload.
- Consider VMs in the DCasv5 or ECasv5 series if you run high-security workloads.
- Consider NV-series VMs if you use graphics-intensive applications.

### Storage solutions

*Impact: Cost Optimization, Performance Efficiency*

Your storage solution also affects the performance of Azure Virtual Desktop. Session hosts use Azure managed disks as virtual hard drives. Several types of disks are available:

- Premium solid-state drives (SSDs)
- Standard SSDs
- Standard hard-disk drives (HDDs)

Each disk has its own maximum size, throughput, and I/O operations per second (IOPS). By choosing the right disk size and series, you can get the performance that's needed at an optimal price point:

- If you choose a disk size that offers adequate performance for the applications that you run in your Azure Virtual Desktop environment, users avoid experiencing severe performance issues.
- If you choose a disk size that's not too large, you avoid paying for extra performance that's not used.

The service-level agreement (SLA) of a disk for the session hosts depends on the disk type. To compare the SLAs of session hosts that use various types of disks, see [Host pool resiliency](/azure/cloud-adoption-framework/scenarios/azure-virtual-desktop/eslz-business-continuity-and-disaster-recovery#host-pool-resiliency).

##### Recommendations

- Consider the maximum size, throughput, and IOPS of the various types of Azure managed disks when you design your storage solution.
- Choose a type of managed disk that optimizes the performance and cost efficiency of your workload.

### Fault tolerance

*Impact: Cost Optimization, Reliability*

Fault tolerance is fundamentally centered on the principles of high availability and disaster recovery. You can achieve high availability by increasing your number of session hosts, especially across different availability zones. If you scatter session hosts across various availability zones and locations within availability zones, you can decrease the chance that your Azure Virtual Desktop environment becomes unavailable due to maintenance or outage.

For disaster recovery of session hosts, you can use golden images or backups:

- If your session hosts contain data or applications that don't actively need to be saved, use golden images. Saving those images in a redundant fashion should provide sufficient disaster recovery.
- If your session host contains valuable data that gets updated frequently, consider backups to save those changes. The cost of using backups is considerably higher than the cost of maintaining golden images.

It's also important to perform a failure mode analysis (FMA) on your environment. With a proper FMA, you can prepare for future outages and help prevent them. Potential failure points include:

- Session hosts that are deployed in a single region. This setup can result in a complete service shutdown during an availability zone outage.
- Personal pool session hosts that don't have backups. Without backups enabled, users can't quickly deploy other identical session hosts and can experience data loss.
- The inability to quickly deploy new session hosts during an outage. If you don't use infrastructure as code (IaC) to save session host information and VM images, you can encounter this problem. Examples of IaC include Azure Resource Manager templates, Bicep, and Terraform.

##### Recommendations

- Spread session hosts across different availability zones to improve availability.
- Spread out session hosts within availability zones.
- Use golden images that you save in a redundant fashion for disaster recovery if you don't need to back up session host data or applications.
- Use backups for disaster recovery if you update session host data frequently.
- Perform an FMA on your environment to prepare for future outages and help prevent them.

## Next steps

Now that you've examined application delivery considerations, see how to establish connectivity, create perimeters for your workload, and evenly distribute traffic to your workloads.

> [!div class="nextstepaction"]
> [Networking and connectivity](./networking.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)
