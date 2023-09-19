---
title: Recommendations for continuous performance optimization
description: Get recommendations for continuous performance optimization.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for continuous performance optimization: PE 12

This guide describes the best practices for continuous performance optimization. Continuous performance optimization refers to the process of constantly monitoring, analyzing, and improving performance efficiency. Performance efficiency adapts to increase and decreases in demand. Performance optimization needs to be ongoing activity throughout the life of the workload. Workload performance often degrades or can even become excessive over time. Factors include changes in usage patterns, demand, features, and technical debt.

**Definitions**

|  Term|                                Definition|
|-|-|  
|Technical debt                      |The accumulated inefficiencies, suboptimal design choices, or shortcuts taken during the development process.|
| Time-to-live | A mechanism that sets an expiration time for data.
| Data tiering |

## Key design strategy

Performance efficiency is when workload capacity aligns to actual usage. A workload that overperforms is as problematic as one that underperforms. The tradeoffs differ. Over performance affects cost optimization. Poor performance affects users. The key to performance efficiency is monitoring, adjusting, and testing over time. You need to regularly review performance metrics and adjust as necessary to ensure the workload is efficient. Testing all changes pre- and post- implementation is required to reach performance targets.

### Prioritize optimization efforts

Proactively optimizing performance in a workload means taking proactive measures to improve and enhance the performance of the workload before any performance issues arise. It involves identifying potential bottlenecks, monitoring performance metrics, and implementing optimizations to ensure that the workload operates efficiently and meets the desired performance goals. Based on the analysis of deteriorating components, critical flows, and technical debt, implement performance optimizations specific to each area. It may involve code changes, infrastructure adjustments, or configuration updates.

**Prioritize deteriorating components.** A workload often has components such as databases and networking components that are prone to performance degradations over time. As the workload evolves and usage patterns change, these changes often affect the performance of individual components in the workload. Increased data in databases can lead to longer query execution times and slower data retrieval. Changes in usage patterns may result in suboptimal query design. Queries that were once efficient may become inefficient as the workload evolves. Inefficient queries can consume excessive resources and degrade database performance. Increased workload usage can lead to higher network traffic, causing congestion and latency issues.

It's important to  make continuous efforts to optimize the performance of these components. Be proactive in identifying and addressing performance issues in your workload. By prioritizing known deteriorating components, you can proactively address potential performance issues and ensure the smooth operation of your workload. It may involve implementing performance tuning techniques, optimizing resource allocation, or upgrading hardware or software components as needed.

**Prioritize critical flows.** Critical flows refer to the most important and high-priority processes or workflows within the workload. By prioritizing these critical flows, you ensure that the most essential parts of the workload are optimized for performance. Knowing critical flows helps prioritize optimization efforts. Optimizing the performance efficiency of the most important areas of your application provides the highest return on investment. You should monitor critical flows and the most popular pages and look for ways to make them more efficient.

**Address technical debt.** Technical debt refers to the accumulated inefficiencies, suboptimal design choices, or shortcuts taken during the development process that can impact performance. Technical debt, unclear code, and overly complex implementations can make performance efficiency more difficult to attain. Addressing technical debt involves identifying and resolving these issues to improve the overall performance and maintainability of the workload. This work may include refactoring code, optimizing database queries, improving architectural design, or implementing best practices. You might have introduced technical debt to hit a deadline, but you need address the technical debt in your workload sooner than later.

### Use platform features

Take advantage of the many platform features and tooling available that can provide performance recommendations based on actual usage. It's crucial to understand the full capabilities of your hosting platform or insights tooling. By enabling and using automation features provided by the platform, you can customize them to fit your evolving needs. Regular updates are essential to receive the best recommendations and insights from third-party tools. Capture and evaluate performance signals from your platform and tools

### Automate performance optimization

Automation can eliminate repetitive and time-consuming manual processes, allowing them to be performed automatically and efficiently. Automation reduces the chances of human error and ensures consistency in executing optimization tasks. By automating these tasks, you can also free up human resources to focus on more complex and value-added activities. You can apply automation can be applied to various tasks such as performance testing, deployment, and monitoring:

- *Automated performance testing:* Use automated performance testing tools like JMeter, K6, or Selenium to simulate different workloads and scenarios.
- *Automated deployment:* Implement automated deployment processes to ensure consistent and error-free deployments. Use CI/CD tools to automate the deployment process. These tools can help you identify performance bottlenecks by testing against endpoints, checking HTTP statuses, and even validating data quality and variations.
- *Monitoring and alerting:* Set up automated monitoring and alerting systems to continuously monitor performance metrics and detect any deviations or anomalies. When performance issues are detected, automated alerts can be triggered to notify the appropriate teams or individuals.
- *Incident management:* Implement an automated incident management system that can receive alerts, create tickets, and assign them to the appropriate teams for resolution. It helps ensure that performance issues are promptly addressed and assigned to the right resources.
- *Automated diagnostics:* Develop automated diagnostic tools or scripts that can analyze performance data and identify the root causes of performance issues. These tools can help pinpoint specific areas or components of the system that are causing the performance problems.
- *Automated remediation actions:* Define and implement automated remediation actions that can be triggered when specific performance issues are detected. These actions can include restarting services, adjusting resource allocation, clearing caches, or implementing other performance optimization techniques.
- *Self-healing systems:* Build self-healing capabilities into your system by automating the recovery process for known performance issues. It can involve automatically applying fixes or adjustments to the system configuration to restore optimal performance.

### Optimize databases

Continuously optimizing databases involves identifying and implementing optimizations to ensure that databases can handle loads, deliver fast response times, and minimize resource utilization. By regularly optimizing databases, organizations can improve application performance, reduce downtime, and enhance the overall user experience.

- *Optimize database queries.* Poorly written SQL statements can degrade database performance. Inefficient JOIN conditions can cause unneeded data processing. Complex subqueries, nested queries, and excessive functions can cause slow execution. Queries that retrieve too much data should be rewritten. You should identify your most common or critical database queries and optimize them. The optimization helps ensure faster query execution.
- *Maintain indexes.* Evaluate the indexing strategy to ensure that indexes are properly designed and maintained. It includes identifying unused or redundant indexes and creating indexes that align with the query patterns. Database indexes help accelerate data retrieval operations. For relational databases, you need to monitor index fragmentation. You should rebuild or reorganize indexes regularly. For nonrelational databases, you need to pick the correct indexing policy for your workload. Use automatic tuning on databases where available. These features include automatically creating missing indexes, dropping unused indexes, and plan correction. For more information, see [Maintaining indexes to improve performance](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
- *Review model design.* Review the data model to ensure it's optimized for the specific requirements of the application. It may involve denormalization, partitioning, or other techniques to improve query performance and data retrieval.
- *Database configuration.* Optimize database configuration settings such as memory allocation, disk I/O, and concurrency settings to maximize performance and resource utilization.

### Optimize data efficiency

Optimizing data efficiency involves techniques to ensure that data is stored, processed, and accessed in the most efficient way possible. Data tiering and using a time to live (TTL) are techniques that can be used to optimize data efficiency. You can apply these techniques in various data storage scenarios, such as databases, file systems, or object storage.

- *Use data tiering.* Data tiering involves categorizing data based on its importance or frequency of access and storing it in different tiers accordingly. It allows for more efficient use of storage resources and improves performance. Frequently accessed or critical data can be stored in high-performance tiers, while less frequently accessed or less critical data can be stored in lower-cost tiers. The goal is to review data usage over time to ensure data is in the correct tier. As data priorities change, data should move from one tier to another.

- *Implement time-to-live.* Using a time to live (TTL) is a mechanism that sets an expiration time for data. It allows data to be automatically deleted or archived after a certain period, reducing storage requirements and improving data management. By setting an appropriate TTL, unnecessary data can be removed, freeing up storage space and improving overall efficiency. Session data, temporary files, and cache data are frequent targets for TTL. Database entries can also have a TTL.

:::image type="icon" source="../_images/risk.svg"::: **Risk:** Setting a TTL that's too short can actually create performance issues.

### Build a performance culture

A performance culture involves creating an environment where continuous improvement is expected and the team learns from production. Performance optimization requires specialized skills. Workload teams need the right skills and mindset to optimize performance to meet increases and decreases in demand. You also need to allocate their time to support the required monitoring and remediation of performance issues as they arise. These teams need clear expectations. For example, performance targets, baselines, and deviation thresholds (how far from baseline is acceptable) need to be highly visible and socialized.

:::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** Continuous performance optimizations require a team with the right skills and time to find and fix performance issues. Dedicating personnel to performance adds operational cost. With limited personnel resources, continuous performance optimization could take time away from other operational tasks.

## Azure facilitation

**Automating performance optimization:** Azure Advisor provides automatic [performance recommendations](/azure/advisor/advisor-performance-recommendations) based on workload telemetry. You should review and address these recommendations regularly. Azure Monitor provides real-time insights into the performance of your system.
and allows you to set up alerts based on specific performance metrics. Azure Log Analytics provides automated diagnostics and analysis on collected logs and metrics.

Azure DevOps can help automated deployment pipelines. Tools like Azure Application Insights provide insights and recommendations for optimizing performance.

**Automating remediation:** Use automation tools or scripts to execute the remediation actions automatically when the alerts are triggered. You could use Azure Automation, Azure Functions, or custom automation solutions.

**Automated performance testing:** Automate performance testing to simulate different user scenarios and workloads. It can help you identify performance bottlenecks and optimize your system accordingly. Tools like Azure DevOps can automate performance testing.

**Optimizing databases:** The SQL family of products has many [built-in features](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) that allow you to monitor and remediate SQL database performance. You should use these features to maintain database performance. Azure SQL Database has an [automatic tuning](/azure/azure-sql/database/automatic-tuning-overview) feature that continuously monitors and improves queries. You should use this feature to improve SQL queries automatically.

Azure Cosmos DB has features that allow you to [customize your indexing policies](/azure/cosmos-db/index-policy). You should customize the policies to meet the performance needs of your workload.

**Optimizing data efficiency:** Data tiering allows you to store data in different tiers based on its access frequency and importance. It helps optimize storage costs and performance. Azure provides different storage tiers such as hot, cool, and archive tiers for blob data. Hot tier is optimized for frequently accessed data, cool tier is for infrequently accessed data, and archive tier is for rarely accessed data. By using the best suited storage access tier for your data, you can ensure efficient data storage and retrieval
