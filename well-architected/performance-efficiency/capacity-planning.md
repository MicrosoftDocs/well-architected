---
title: Recommendations for capacity planning
description: Learn about Well-Architected Framework recommendations for implementing capacity planning in your workloads.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for capacity planning

**Applies to this Azure Well-Architected Framework Performance Efficiency checklist recommendation:** 

|[PE:02](checklist.md)| Conduct capacity planning. Perform capacity planning before there are predicted changes in usage patterns, such as seasonal variations, product updates, marketing campaigns, special events, or regulatory changes. | 
|---|---| 

This guide describes the recommendations for capacity planning. Capacity planning refers to the process of determining the resources required to meet workload performance targets. It involves estimating the amount of computing resources such as CPU, memory, storage, and network bandwidth needed to support the workload's performance requirements. Capacity planning helps avoid underprovisioning and ensures the workload has sufficient resources to handle the expected workload demands without experiencing performance degradation or bottlenecks. It also helps prevent overprovisioning and unnecessary costs. A lack of capacity planning can lead to performance issues, resource bottlenecks, increased costs, inefficient allocation, scalability challenges, and unpredictable workload performance.

**Definitions**

|  Term|                                Definition|
|-|-|
|  Capacity planning |                  The process of predicting the resources a workload needs to meet its performance targets.|
| Functional requirements|             The features and capabilities a workload must have to fulfill its intended purpose.|
|  Technical requirements|              The code and infrastructure needed to meet functional requirements.|
|  Trend analysis                      |Historical data analysis to forecast future demand.|

## Key design strategies

Capacity planning is a forward-looking process that involves making decisions based on anticipated workload demands and patterns. Its goal is to optimize workload performance across both continuous and peak load scenarios. By understanding changes in usage, such as seasonal shifts or product releases, you can allocate resources strategically, preventing system strain during high demand periods. This proactive strategy reduces disruptions and bolsters performance efficiency. By analyzing past usage trends and growth data, you can forecast short and long-term needs. You can pinpoint potential bottlenecks and scaling issues, ensuring consistent and efficient workload performance.

### Gather capacity data

Gathering workload utilization data entails collecting and analyzing information on how a workload uses resources, based on both historical patterns for existing workloads and predictive measures for new ones. This process helps translate business objectives into technical requirements and is essential for forecasting capacity. Consider the following recommendations:

#### Understand an existing workload

Understanding an existing workload for capacity planning involves analyzing historical data related to how the workload utilizes resources. It encompasses metrics like resource utilization, performance data, and workload patterns. This understanding ensures efficient resource allocation, translates business goals into technical requirements, and helps identify potential bottlenecks.

- *Understand the data*: Review the available historical data and understand its structure, format, and relevance to capacity planning. The review might include resource utilization metrics, workload patterns, performance metrics, and other relevant data points.

- *Clean and preprocess the data*: Prepare the data for analysis by removing any inconsistencies, errors, or outliers. Preparing the data might involve data cleaning techniques like data imputation, the handling of missing values, or normalization.

- *Identify key metrics*: Identify the metrics that are relevant for capacity planning. Metrics can include CPU utilization, memory usage, network throughput, and response times.

- *Identify bottlenecks*: Measure throughput and response times to identify the specific components of your system that might become bottlenecks as the workload grows. Requests per second and database CPU usage can be good indicators of capacity.

- *Visualize the data*: Create visualizations, like charts or plots, to gain better insights into historical data. Visualizations can help you identify patterns, trends, and anomalies in data to give you a clearer understanding of workload behavior.

#### Understand a new workload

Understanding a new workload for capacity planning refers to predicting the resource requirements of a future task without historical data, often relying on methods such as market research, expert judgment, pilot projects, and external data sources. Predicting the future needs of new a workload without historical data can be more challenging. This process is vital to ensure that resources are efficiently allocated and aligned with business objectives when the workload is introduced. In such cases, you should consider alternative approaches:

- *Market research*: Conducting market research to understand the demand for similar products or services can provide valuable insights into the potential demand for a new workload. The research can involve analyzing market trends, conducting surveys, or studying competitor offerings.

- *Expert judgment*: Input from subject matter experts or professionals who have experience in the industry can help you estimate the demand for a new workload. Their expertise and insights can provide valuable inputs for forecasting.

- *Pilot projects or prototypes*: Small-scale pilot projects or prototypes can help you gather real-time data and feedback. You can then use this data to inform the capacity planning process and adjust the forecasted demand.

- *External data sources*: External data sources like industry reports, market studies, or customer surveys can provide additional information for estimating demand for a new workload. These sources can offer valuable insights into customer preferences, market trends, and potential demand drivers.

### Align capacity with workload objectives

Aligning utilization data with workload objectives means comparing utilization data against the strategic objectives. The goal is to accurately predict and plan for future capacity needs. For example, if the goal is to support an API that facilitates file uploads for 1 million users with an average file size of 1 MB and ensures storage within a second, this poses certain demands on the system. However, if the current data indicates potential bottlenecks or shortfalls in write speeds, it becomes evident that capacity adjustments are necessary. This alignment helps inform where capacity enhancements and allows you to plan for them.

### Forecast demand

Forecasting demand involves using workload data to predict future needs for a service or product. It's essential for capacity planning to ensure efficient resource allocation, anticipate growth patterns, and prepare for potential surges in demand. When you forecast future demand, you use data to get a sense of future needs. You apply statistical analysis, trend analysis, or predictive modeling techniques to the data you have to forecast future demand. These methods take into account historical or anticipated patterns and project them into the future to provide estimates of the expected workload demand. To forecast demand, consider these strategies:

**Account for various scenarios.** When you perform capacity planning, you need to plan for different scenarios that might occur. This planning should include both predictable growth patterns and unexpected surges in demand. Usage patterns can grow or shrink. They can be organic (more or less users) or inorganic (an event or security incident). You need to conduct capacity planning before usage changes, at key times:

- Design (prediction)
- Regular spikes (8:00 AM sign-in rush)
- Launch (prediction validation)
- Business model change
- Acquisition or merger
- Marketing push
- Seasonal change
- Feature launch
- Periodically

**Use prediction techniques.** Forecasting future demand for a service or product involves using techniques like statistical analysis, trend analysis, and predictive modeling. Here's an overview of how you can use these techniques:

- *Statistical analysis*: Statistical methods can you help uncover patterns and relationships within historical data. You can use these patterns to forecast future demand. You can use techniques like time series analysis, regression analysis, and moving averages to identify trends, seasonality, and other patterns in the data.

- *Trend analysis*: Trend analysis involves examining historical data to identify consistent patterns and extrapolating those patterns into the future. For example, if workload demand has increased by 10 percent during the past year, you might forecast a continuation of this trend. When you analyze historical demand data over a period of time, you can identify growth or reduction trends. Use these trends as a basis for forecasting future demand. Trend analysis can also identify the effects of one-time events that cause rapid shifts in traffic (inorganic). For example, feature releases might consistently increase demand by 5 percent. If you have four major releases a year, you should plan for 5 percent growth each time.

- *Predictive modeling*: Predictive modeling is the process of building mathematical models that use historical data and other relevant variables to make predictions about future demand. You can use techniques like machine learning algorithms, neural networks, or decision trees. These models can take into account multiple factors and variables to provide more accurate forecasts.

### Determine resource requirements

Determining resource requirements for capacity planning involves assessing the resources that you need to meet forecasted demand. A workload can have many components, so there's no one metric to observe. You need to measure capacity at the component level to get meaningful results. Estimate the expected demand for your components based on historical data, market trends, and business projections. Consider the number of transactions, concurrent users, or any other relevant metrics.

**Calculate resource needs.** Based on the forecasted demand, calculate the resources needed to meet that demand. Consider factors such as server capacity, network bandwidth, storage capacity, and personnel.

- *Server capacity*: Determine the required server capacity based on the estimated number of concurrent users or transactions. Consider factors like CPU, memory, and disk space requirements to ensure that your servers can handle the expected workload.

- *Network bandwidth*: Evaluate the network bandwidth that you need to support the anticipated level of traffic. You should include both inbound and outbound data transfer rates to ensure smooth and efficient communication between servers and clients.

- *Storage capacity*: Estimate the amount of data that the workload generates or processes during the forecasted demand. Consider factors like database size, file storage requirements, and any other data storage needs that are specific to your application.

- *Personnel*: Assess the human resources that are required to manage and maintain the infrastructure, handle customer support, perform system maintenance, and ensure smooth operations. Take into account factors like workload distribution, skill sets, and required expertise.

**Identify reachable limits.** Capacity planning should find hard limits in the workload and evaluate whether those limits are in reach. These limits usually apply to infrastructure (compute, memory, storage, network), application (concurrent database connections, response times, availability), service (requests per second), and scaling. When capacity planning identifies reachable limits, you need to modify the workload before the limit creates a performance problem. Performance baselines, continuous monitoring, and testing are essential to validating the limits and the solution.

**Define technical requirements.** Workload requirements include functional and technical requirements. You should meet with stakeholders to ensure that you understand the functional requirements of the workload and define technical requirements to meet the functional requirements. For example, a great user experience requires a response time of 100 ms. You should agree on a maximum median response time that aligns with your performance targets. You should then consider the service-level agreements (SLAs) that your service provider offers. Align your capacity planning with the SLAs to ensure that you meet your performance targets. You generate technical requirements to support functional requirements. You also need to understand the workload architecture so you can identify the correct components to modify.

**Understand resource limitations.** Resources in your workload have performance limitations. Performance limitations apply to services and SKUs within each service. You need to understand the limitations of the resources in your workload and factor those limitations into your design decisions. For example, you should know whether resource limitations require you to change SKUs or to change resources altogether.

**Test conclusions.** Capacity planning is an iterative process. You should test the conclusions of your capacity planning and adjust estimates accordingly.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Misjudged capacity planning can lead to over-provisioning or under-provisioning of resources. Over-provisioning leads to higher costs. Under-provisioning can result in poor performance. Try to find the right balance.

## Azure facilitation

**Gathering data and forecasting**: [Azure Monitor](/azure/azure-monitor/overview) enables you to collect and analyze telemetry data from your applications and infrastructure. It supports the monitoring of various Azure resources, including virtual machines, containers, and storage accounts. Key tools include [Application Insights](/azure/azure-monitor/app/app-insights-overview) and [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview). By configuring data collection and defining metrics and logs that you want to monitor, you can gather valuable workload data for analysis. For [network monitoring](/azure/networking/fundamentals/networking-overview#monitor), combine Azure Monitor with Azure Network Watcher, Azure Monitor network insights, and Azure ExpressRoute monitoring.

Azure Monitor allows you to analyze historical data and apply forecasting techniques to predict future workload trends and capacity requirements. You can generate forecasts that can help you with capacity planning. These forecasts help estimate server capacity, network bandwidth, storage capacity, and other resource needs by using predicted demand patterns.

**Scaling**: Azure provides scalability options like autoscaling, which can automatically adjust resources based on workload demand. You can scale vertically by increasing the capacity of a resource, such as by using a larger virtual machine size, or horizontally by adding new instances of a resource. Azure services that have autoscaling capabilities can automatically scale out to ensure capacity during workload peaks and return to normal when the load decreases.

**Defining technical requirements**: Because they provide a wide range of configurations, Azure tools and services can help you define technical requirements. You can align your workload requirements with available Azure resources, ensuring that you select the appropriate components and settings to meet your functional needs.

**Defining resource limitations**: Azure provides documentation and resources to help you understand the performance limitations of various Azure services and SKUs. Taking into consideration these limitations can help you make informed design decisions and optimize your workload architecture for performance and cost-effectiveness.

**Testing conclusions**: Azure provides tools like Azure Load Testing, which can simulate load and different usage patterns to help you gather relevant data about your workload.

## Related links

- [Azure Monitor](/azure/azure-monitor/overview)
- [Application Insights](/azure/azure-monitor/app/app-insights-overview)
- [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview)
- [Network monitoring services](/azure/networking/fundamentals/networking-overview#monitor)

## Performance Efficiency checklist  

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Performance Efficiency checklist](checklist.md) 
