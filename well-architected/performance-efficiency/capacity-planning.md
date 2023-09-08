---
title: Recommendations for capacity planning
description: Learn best practices for capacity planning.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for capacity planning: PE 02

This guide describes the best practices for capacity planning. You should perform formal capacity planning at key points in the lifecycle of the workload. Capacity planning enables you to anticipate short and long-term demand and manage workload performance effectively. By analyzing historical usage patterns and growth trends, you can identify potential bottlenecks, scalability challenges, or performance constraints.

**Definitions**

|  Term|                                Definition|
|-|-|
 |  Capacity planning |                  The process of predicting the resources a workload needs to meet its performance targets.|
|  Trend analysis                      |Historical data analysis to forecast future demand.|
| Functional requirements|             The features and capabilities a workload must have to fulfill its intended purpose.|
|  Technical requirements|              The code and infrastructure needed to meet the functional requirements.|

## Key strategies

Capacity planning is a predictive exercise. It's about making informed decisions based on load predictions and workload behavior. The objective is to ensure efficient workload performance, not just when demand grows or shrinks, but by recognizing the nuances of continuous load versus peak load events.

### Gather workload data

Before you can predict the capacity requirements of a workload, you need to understand the workload. You need to gather information about the workload and its characteristics. The information gathering includes understanding the purpose, requirements, and behavior of the workload. You should gather workload data for an existing workload with historical data or a new workload in the design phase.

**Understand an existing workload.** Gather the historical data related to the workload and include metrics like resource utilization, performance data, workload patterns, and any other relevant data points.

- *Understand the data:* Review the available historical data and understand its structure, format, and relevance to capacity planning. The review could include resource utilization metrics, workload patterns, performance metrics, and any other relevant data points.
- *Clean and preprocess the data:* Prepare the data for analysis by removing any inconsistencies, errors, or outliers. Preparing the data might involve data cleaning techniques like data imputation, handling missing values, or normalization.
*Identify key metrics:* Identify the metrics that are relevant for capacity planning. Metrics could include CPU utilization, memory usage, network throughput, and response times.
*Visualize the data:* Create visualizations, such as charts or plots, to gain better insights into the historical data. Visualizations can help identify patterns, trends, and anomalies in the data, providing a clearer understanding of the workload behavior.

**Understand a new workload.** When it comes to new a workload without historical data, predicting the future needs of a workload can be more challenging. In such cases, you should consider alternative approaches such as:

- *Market research:* Conduct market research to understand the demand for similar products or services can provide valuable insights into potential demand for the new workload. The research can involve analyzing market trends, conducting surveys, or studying competitor offerings.
- *Expert judgment:* Seek input from subject matter experts or professionals with experience in the industry can help in estimating the demand for a new workload. Their expertise and insights can provide valuable inputs for forecasting.
- *Pilot projects or prototypes:* Launch small-scale pilot projects or prototypes can help gather real-time data and feedback. This data can then be used to inform the capacity planning process and adjust the forecasted demand.
- *External data sources:* Use external data sources such as industry reports, market studies, or customer surveys can provide additional information for estimating demand for a new workload. These sources can offer valuable insights into customer preferences, market trends, and potential demand drivers.

### Forecast future demand

Forecasting future demand uses data to help get a sense of the future needs. For a workload with or without historical data, you can apply statistical analysis, trend analysis, or predictive modeling techniques to the data you have to forecast future demand. These methods take into account historical or anticipated patterns and project them into the future, providing estimates of the expected workload demand.

**Account for different scenarios.** When you do capacity planning, you need to plan for different scenarios that might occur. This planning should include both predictable growth patterns and unexpected surges in demand. Usage patterns can grow or shrink. They can be organic (more or less users) or inorganic (event or security incident). You need to conduct capacity planning before usage changes, including the following examples:

- Design (prediction)
- Regular spikes (8am sign-on rush)
- Launch (prediction validation)
- Business model change
- Acquisition or merger
- Marketing push
- Seasonal change
- Feature launch
- Periodically

**Use prediction techniques.** Forecasting future demand for a service or product involves utilizing various techniques such as statistical analysis, trend analysis, and predictive modeling. Here's an overview of how you can use these techniques:

- *Statistical analysis:* Statistical methods can help uncover patterns and relationships within historical data, which you can use to forecast future demand. You can use techniques such as time series analysis, regression analysis, and moving averages to identify trends, seasonality, and other patterns in the data.
- *Trend analysis:* Trend analysis involves examining historical data to identify consistent patterns and extrapolating those patterns into the future. For example, if workload demand has increased by 10% over the past year, then you might forecast a continuation of this trend. When you analyze historical demand data over a period of time, you can identify growth or decline trends. Use them as a basis for forecasting future demand. Trend analysis can also identify the effects of one-time events that cause rapid shifts in traffic up or down (inorganic). For example, feature releases might consistently increase demand by 5%. If you have four major releases a year, you should plan for 5% growth each time.
- *Predictive modeling:* Predictive modeling involves building mathematical models that use historical data and other relevant variables to make predictions about future demand. You could include techniques such as machine learning algorithms, neural networks, or decision trees. These models can take into account multiple factors and variables to provide more accurate forecasts.

### Determine resource requirements

Determining resource requirements for capacity planning involves assessing the resources needed to meet forecasted demand. You need to estimate the expected demand for your services or products based on historical data, market trends, and business projections. Consider the number of transactions, concurrent users, or any other relevant metric.

**Calculate resource needs.** Based on the forecasted demand, calculate the resources needed to meet that demand. Consider factors such as server capacity, network bandwidth, storage capacity, and personnel.

- *Server capacity:* Determine the required server capacity based on the estimated number of concurrent users or transactions. Consider factors like CPU, memory, and disk space requirements to ensure that your servers can handle the expected workload.
- *Network bandwidth:* Evaluate the network bandwidth needed to support the anticipated level of traffic. You should include both inbound and outbound data transfer rates to ensure smooth and efficient communication between servers and clients.
- *Storage capacity:* Estimate the amount of data that the workload generates or processes during the forecasted demand. Consider factors like database size, file storage requirements, and any other data storage needs specific to your application.
- *Personnel:* Assess the number of human resources required to manage and maintain the infrastructure, handle customer support, perform system maintenance, and ensure smooth operations. Take into account factors like workload distribution, skill set, and expertise required.

**Identify reachable limits.** Capacity planning should find hard limits in the workload and evaluate if those limits are in reach. These limits usually apply to infrastructure (compute, memory, storage, network), application (concurrent database connections, response times, availability), service (requests per second), and scaling. When capacity planning identifies reachable limits, you need to modify the workload before the limit becomes a performance issue. Performance baseline, continuous monitoring, and testing are essential to validating the limits and the fix.

**Define technical requirements.** Workload requirements include functional and technical requirements. You should meet with stakeholders to ensure you understand the functional requirements of the workload and define technical requirements to meet the functional requirements. For example, a great user experience requires a response time of 100 ms. You should agree on a maximum median response time that aligns with your performance targets. At this point, you should consider the service level agreements (SLAs) your service provider offers. Align your capacity planning with the SLAs to ensure you meet your performance targets. You generate the technical requirements to support the functional requirements. You also need to understand the workload architecture so you can identify the correct components to modify.

**Understand resource limitations.** Resources in your workload have performance limitations. Performance limitations apply to services and SKUs within each service. You need to understand the limitations of the resources in your workload and should factor those limitations into your design decisions. For example, you should know if resources limitations require changing SKUs or changing resources altogether.

**Test conclusions.** Capacity planning is an iterative process. You should test the conclusions of your capacity planning and adjust the estimates accordingly.

*Tradeoff:* Misjudged capacity planning could lead to over provisioning or under-provisioning of resources. Over provisioning leads to higher costs. Under-provisioning can result in poor performance. Aim to find the right balance.

## Azure facilitation

**Gathering data and forecasting:** [Azure Monitor](/azure/azure-monitor/overview) enables you to collect and analyze telemetry data from your applications and infrastructure. It supports monitoring of various Azure resources, including virtual machines, containers, storage accounts, and more. Key tools include [Application Insights](/azure/azure-monitor/app/app-insights-overview) and [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview). By configuring data collection and defining metrics and logs to monitor, you can gather valuable workload data for analysis. For [networking monitoring](/azure/networking/fundamentals/networking-overview#monitor), combine Azure Monitor with Azure Network Watcher, Network Insights, and ExpressRoute Monitor.

Azure Monitor allows you to analyze historical data and apply forecasting techniques to predict future workload trends and capacity requirements. You can generate forecasts that can help capacity planning. These forecasts help estimate server capacity, network bandwidth, storage capacity, and other resource needs based on predicted demand patterns.

**Scaling:** Azure offers scalability options like autoscaling, which can automatically adjust resources based on workload demand. You can scale vertically by increasing the capacity of a resource, such as using a larger virtual machine size, or horizontally by adding new instances of a resource. Azure services with autoscaling capabilities can automatically scale out to ensure capacity during workload peaks and return to normal when the load decreases.

**Define technical requirements:** Azure tools and services can help define technical requirements by offering a wide range of services and configurations. You can align your workload requirements with the available Azure resources, ensuring that you select the appropriate components and settings to meet your functional needs.

**Understand resource limitations:** Azure provides documentation and resources to help you understand the performance limitations of different Azure services and SKUs. By considering these limitations, you can make informed design decisions and optimize your workload architecture for performance and cost-effectiveness

**Testing conclusions.** Azure provides tools like Azure Load Testing, which can simulate load and different usage patterns to help you gather relevant data about your workload.
