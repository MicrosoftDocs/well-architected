---
title: Performance Efficiency checklist 
description: Review a checklist about using monitoring for performance efficiency. Consider scalability, app and infrastructure performance, and resiliency.
author: martinekuan
ms.author: martinek
ms.date: 12/08/2021
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-efficiency
products:
  - azure-monitor
  - azure-application-insights
categories:
  - management-and-governance
ms.custom:
  - fasttrack-edit
  - article
---

# Performance Efficiency checklist 

This checklist presents a set of recommendations for your system to scale its ability to grow to meet workload usage demand. The goal of performance is that every legitimate interaction against an otherwise healthy system continues to result in a “success” even as demand increases. This must be designed & implemented with efficiency in cost, complexity, supporting new requirements, technical debt, reporting, and toil factored in.

Every system has a limit on how far it can be scaled without the need for re-architecting, introducing a workaround, or having human involvement. If you haven’t checked the boxes and considered the tradeoffs, the design could be at risk. Careful consideration of all the points covered in the checklist will give you confidence in success.

## Recommendations

|Code|Recommendation|
|---|---|
|[**PE:01**](./performance-targets.md)| Define numeric indicators and metrics that provide contextualized and measurable information about the components, flows, dependencies, and other entities that are in-scope for performance calibration. Make it easy for all responsible teams to review and act through alerts, dashboards, and reports.|
|PE:02|Instrument the solution. Automatically and continuously capture complete & accurate performance data, from across the workload and user flows. This data exclusively drives the alerts, dashboards, and reports on the defined indicators and metrics. This instrumentation must cover Azure resources and any custom code deployed.|
|PE:03|Implement a reliable scale-out and partitioning scaling strategy to ensure there are always enough resources available to meet legitimate usage demands. Document your scale-unit design approach. Bound autoscaling to prevent runaway automation|
|PE:04|Select Azure resources, their SKUs, their regions & zones, and their features that support your performance targets, scaling strategy, and ability to gather performance insights. Measure the performance impact of custom code vs offloading functionality to platform functionality.|
|PE:05|Establish performance baselines for all critical user & data flows and overall system metrics. Predict production impact of all candidate changes by testing in an environment that can approximate production in terms of topology, features, and load. Always “test in production” by measuring the actual impact against baselines during and after production deployment as well. When traffic patterns are not diverse enough, use synthetic transactions in production to have consistent traffic to track performance of healthy and error flows.|
|PE:06|Eliminate the need for instance pinning, state affinity, or singletons. Design all components to distribute and redistribute load across multiple resources or resource instances to simplify load balancing and scaling operations.|
|PE:07|Respect your performance targets while performing SDLC and routine operations, such as deploying a new version, performing periodic or one-off backups, or performing audits. Deploying a new version into production should be done “at scale” of the current run state of the system, do not deploy to a “minimum/default” and force the system to immediately perform a scale operation.|
|PE:08|Where your workload controls its own data, design all stores, partitions, & indexes explicitly for their intended usage in the workload. Tune data shape and indexes based on coded CRUD usage patterns. Use eventual consistency where practical to facilitate data caching, shard replication, and deferred data capture.|
|PE:09|Keep all resource utilization limited to its core purpose and only use the resource when necessary by using architectural techniques like data caching, gateway processing, and utilizing available client resources. Also reduce resource utilization (such as CPU cores and memory) by optimizing code to avoid memory leaks, unnecessary large object heap allocations, seeking O(1) solutions, and taking advantage of the processor architectures and their threading models.|
|PE:10|Perform formal capacity planning and drilling activities at all key points in the lifecycle of the workload. These key points include things like initial business requirements, significant business model changes, marketing pushes, seasonal drift.|
|PE:11|Post-mortem (RCA) all live-site activities related to performance or scaling malfunction to seek ways to optimize the operations team’s ability to efficiently restore operations or ways to remediate via workload’s techniques of self-preservation or self-healing.|
|PE:12|When deciding where to invest any limited resources on performance, prioritize the performance of critical flows and critical users. Where practical, yield lower & non-priority usage to higher priority usage. This might align with your workload’s business or tenant model.|
|PE:13|Institute continuous effort to optimize performance of solution components that may deteriorate over time (growth/usage), like databases and networking components. Consider developing or taking advantage of existing specialized resources across workloads.|

## Tradeoffs

TBD

## Next section

Based on insights gained through monitoring, optimize your code. One option might be to consider other Azure services that may be more appropriate for your objectives.

> [!div class="nextstepaction"]
> [Optimize](optimize.md)

## Related links

> [Back to the main article](overview.md)
