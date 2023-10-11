---
title: Application considerations for Azure Virtual Desktop workloads
description: Understand Azure Virtual Desktop application platforms. See how to design for scalability, resiliency, efficient resource distribution, and enhanced security.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 09/15/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Application considerations for Azure Virtual Desktop workloads

This article discusses the application delivery design area of an Azure Virtual Desktop workload. This area covers end-user compute delivery considerations.

Virtual Desktop is a desktop and app virtualization service that runs on Azure. Because the key components of Virtual Desktop are cloud services, there are many design considerations to keep in mind when you use Virtual Desktop. Various features and architectural decisions affect various pillars of the Azure Well-Architected Framework. For more information about how the pillars apply to this workload, see [Azure Virtual Desktop design principles](./design-principles.md).

This article looks at some of the considerations that influence the architecture in the following diagram. The discussion also covers various technologies and architectures that affect the quality of your virtual desktop environment.

:::image type="content" source="./images/azure-virtual-desktop-accelerator-baseline-architecture.png" alt-text="Architecture diagram that shows the flow of traffic in a virtual desktop workload." border="false" lightbox="./images/azure-virtual-desktop-accelerator-baseline-architecture.png":::

*Download a [Visio file](https://github.com/Azure/avdaccelerator/blob/main/workload/docs/diagrams/avd-accelerator-baseline-architecture.vsdx) of this architecture.*

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-a-virtual-desktop-workload).

## Host pool settings

When you create virtual desktop host pools, various settings directly affect the performance and efficiency of your virtual desktop environment.

### Pool type

*Impact: Cost Optimization, Reliability*

As you can see in the diagram in the **Azure Virtual Desktop landing zone subscription** box, you can create two types of host pool:

- Personal
- Pooled

A personal pool is designed so that each virtual machine (VM) inside the host pool is dedicated to a specific user. With this setup, each user can save, edit, and personalize applications and settings within a VM. In contrast, a pooled pool is designed to fit multiple users into a single VM.

The two host pool types affect your cost efficiency in different ways. Because pooled pools place multiple users in a single VM, you can serve all your users with a relatively small number of VMs. With personal pools, you usually need a greater number of VMs, which increases your maintenance costs.

Different reliability considerations apply to each pool type:

- For a pooled host pool, it's sufficient for most reliability considerations to capture and maintain a golden image. Because users don't directly personalize the application and VM settings, there's often no information that needs to be backed up frequently. Capturing and maintaining golden images in a secure way, such as by using Azure Compute Gallery, provides a sufficient disaster recovery solution.  
- For a personal host pool, users personalize and edit applications and registries within the VM. As a result, a more reliable solution is required than holding a golden image. Consider enabling Azure Backup for the VMs inside a personal pool. Using Backup helps ensure that all the changes that users make are stored and can be restored during a disaster.

Each pool type has its own pros and cons. Choose your host pool type carefully by examining the functionalities that your virtual desktop users require.

##### Assessment question

Have you considered how different types of host pools affect the cost and reliability of Virtual Desktop?

##### Recommendations

- Consider using a personal pool if you aim to give users power to personalize their environment and work freely within a VM.
- Use a pooled pool to streamline your reliability solution and minimize costs.

### Load-balancing algorithms

*Impact: Cost Optimization, Performance Efficiency*

If you use a pooled host pool, there are two types of load-balancing algorithms that you can use. Each one directly affects your cost efficiency and performance efficiency.

- Breadth-first load balancing distributes user sessions across session hosts. Users are assigned to the session host with the lowest usage, which can improve the user experience.
- Depth-first load balancing saturates one session host at a time before assigning user sessions to other session hosts.
  - This approach is cost-effective. It assigns users to a single host pool until the capacity is reached before it assigns users to the next session host.
  - This approach is ideal for scale-down scenarios.
  - If you use proper sizing and maximum session rules, this approach can outperform a breadth-first algorithm.

##### Assessment question

If you use a pooled host pool, have you considered how the load-balancing algorithm affects your cost efficiency and performance?

##### Recommendations

- Use breadth-first load balancing to quickly improve your user experience.
- Use depth-first load balancing for a cost-effective approach.
- Use depth-first load balancing for scale-down scenarios.
- Use optimized sizing and maximum session rules to improve the performance of depth-first load balancing.

### Scaling plans

*Impact: Cost Optimization, Performance Efficiency*

You can use the autoscale feature of Virtual Desktop to help meet user demand and also reduce the cost of maintaining your virtual desktop environment. When you use scaling plans, you can adjust the minimum and maximum percentage of hosts and the capacity threshold. By changing these settings, you can optimize the number of session hosts that are online and ready to accept user sessions.

Using the scaling plans in an optimal way helps you:

- Have an adequate number of session hosts ready to serve your users.
- Turn off session hosts when users no longer need your service.

You can also change the load-balancing algorithm to further improve your cost efficiency.

Unlike Azure Virtual Machine Scale Sets, scaling plans don't deploy or delete existing session hosts. Instead, the plans automatically turn the hosts off and on to help you maximize your cost efficiency.

It's important to set an adequate maximum number of session hosts from the beginning. This practice helps ensure that your Virtual Desktop environment meets performance needs but can also scale down resources when they're not needed. Scaling down excessively can be harmful. For instance, in some scenarios, there might not be enough session hosts ready to serve the user. In that case, users either have a slower virtual desktop experience or can't connect to the virtual desktop environment. As a result, it's important to determine ideal values for the lowest point that you can scale down to and highest point that you can scale up to.

##### Assessment question

Do you use the autoscale feature and scaling plans for Virtual Desktop session hosts?

##### Recommendations

- Use the autoscale feature to reduce the cost of maintaining your environment.
- Use scaling plans, which automatically turn hosts off and on to help ensure adequate performance for users.
- Adjust the settings of scaling plans to improve cost efficiency.

## Session host settings

Like host pool settings, the settings on the VMs that serve as your session hosts can also affect the performance of your virtual desktop environment.

### Regions

*Impact: Reliability, Performance Efficiency*

The location of a session host correlates directly with the latency that end users experience. If you use FSLogix, the distance between your host pool location and the FSLogix storage location also affects your end-user experience. Deploy session hosts close to user locations. A single host pool can hold session hosts from various regions.

The region of your session hosts also affects the reliability of your virtual desktop environment. It's important to deploy your session hosts in an availability zone or an availability set.

- Availability zones help prevent availability zone outages from affecting your session hosts. But availability zones are only available in certain regions.
- Availability sets don't protect your environment from availability zone outages, but they do offer protection from failures in specific fault domains and update domains.

For more information, see the following resources:

- [Availability zone service and regional support](/azure/reliability/availability-zones-service-support)
- [Availability sets overview](/azure/virtual-machines/availability-set-overview)

##### Assessment question

Do you place your session hosts in availability zones or availability sets?

##### Recommendations

- Deploy session hosts close to your users to minimize latency.
- Deploy session hosts in an availability zone or an availability set to help protect your environment from outages.

### Compute size

*Impact: Cost Optimization, Performance Efficiency*

Your session host compute size also affects the performance of your environment. Azure offers various compute sizes. There are also many families, architecture types, core counts, storage features, and specialty hardware options like GPUs that are available. Choosing the right size for your workload helps you achieve optimal performance at an optimal price point.

Some sizes offer special features:

- DCasv5 and ECasv5 are confidential sizes that provide robust high-security features. Examples of these features include hardware-based isolation, encryption, and dedicated virtual trusted-platform modules.
- Certain sizes provide GPU support. The NV-series, which is backed by NVIDIA Tesla M60 GPUs, can be helpful if you use frameworks such as OpenGL and DirectX.

##### Assessment question

When you chose a compute size, did you consider the various sizes, families, features, and options that Azure offers?

##### Recommendations

- Look at the various compute sizes, families, and features that Azure offers, and choose the option that optimizes the performance and cost efficiency of your workload.
- Consider VMs in the DCasv5 or ECasv5 series if you run high-security workloads.
- Consider NV-series VMs if you use frameworks like OpenGL and DirectX.

### Storage solutions

*Impact: Cost Optimization, Performance Efficiency*

Your storage solution also affects the performance of your virtual desktop. Session hosts use Azure managed disks as virtual hard drives. Several types of disks are available:

- Ultra disks
- Premium solid-state drives (SSDs)
- Standard SSDs
- Standard hard-disk drives (HDDs)

Each disk has its own maximum size, throughput, and I/O operations per second (IOPS). By choosing the right disk size and series, you can get the performance that your virtual desktop needs at an optimal price point:

- If you choose a disk size that offers adequate performance for the applications that you run in your Virtual Desktop environment, users avoid experiencing severe performance issues.
- If you choose a disk size that's not too large, you avoid paying for extra performance that's not used.

The service-level agreement (SLA) of a disk for the session hosts depends on the disk type. To compare the SLAs of session hosts that use various types of disks, see [Host pool resiliency](/azure/cloud-adoption-framework/scenarios/wvd/eslz-business-continuity-and-disaster-recovery#host-pool-resiliency).

##### Assessment question

Did you consider how your storage solution affects the performance of your virtual desktop?

##### Recommendations

- Consider the maximum size, throughput, and IOPS of the various types of Azure managed disks when you design your storage solution.
- Choose a type of managed disk that optimizes the performance and cost efficiency of your workload.

### Fault tolerance

*Impact: Cost Optimization, Reliability*

Fault tolerance revolves around high availability and disaster recovery. You can achieve high availability by increasing your number of session hosts, especially across different availability zones. If you scatter session hosts across various availability zones and locations within availability zones, you can decrease the chance that your virtual desktop environment becomes unavailable due to maintenance or outage.

For disaster recovery of session hosts, you can use golden images or Backup:

- If your session hosts contain data or applications that don't actively need to be saved, use golden images. Saving those images in a redundant fashion should provide sufficient disaster recovery.
- If your session host contains valuable data that gets updated frequently, consider investing in Backup to save those changes. The cost of using Backup is considerably higher than the cost of maintaining golden images.

It's also important to perform a failure mode analysis (FMA) on your environment. With a proper FMA, you can prepare for future outages and help prevent them. Potential failure points include:

- Session hosts that are deployed in a single region. This setup can result in a complete service shutdown during an availability zone outage.
- Personal pool session hosts that don't have Backup enabled. Without Backup enabled, users can't quickly deploy other identical session hosts and can experience data loss.
- The inability to quickly deploy new session hosts during an outage. If you don't use infrastructure as code (IaC) to save session host information and VM images, you can encounter this problem. Examples of IaC include Azure Resource Manager templates, Bicep, and Terraform.

##### Assessment question

How do you achieve high availability and disaster recovery for your workload?

##### Recommendations

- Spread session hosts across different availability zones to improve availability.
- Spread out session hosts within availability zones.
- Use golden images that you save in a redundant fashion for disaster recovery if you don't need to back up session host data or applications.
- Use Backup for disaster recovery if you update session host data frequently.
- Perform an FMA on your environment to prepare for future outages and help prevent them.

## Next steps

Now that you've examined application delivery considerations, see how to establish connectivity, create perimeters for your workload, and evenly distribute traffic to your workloads.

> [!div class="nextstepaction"]
> [Networking](./networking.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)
