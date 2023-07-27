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

## Definitions

|  Term|                                Definition|
|-|-|  
|Technical debt                      |The implied cost of implementing easier design decisions that require future rework.|

## Your responsibility

**Understand performance efficiency.** Performance efficiency is when workload capacity aligns to actual usage. A workload that overperforms is as problematic as one that underperforms. The tradeoffs differ. Over performance affects cost optimization. Under performance affects users. The key to performance efficiency is monitoring, adjusting, and testing. You need to regularly review performance metrics and adjust as necessary to ensure the workload is efficient. Testing all changes pre and post implementation is required to reach performance targets.

Knowing critical flows helps prioritize optimization efforts. Optimizing the performance efficiency of the most important areas of your application provides the highest return on investment. You should monitor critical flows and the most popular pages and look for ways to make them more efficient.

Technical debt, unclear code, overly complex implementations can make performance efficiency more difficult to attain. Some performance-impacting technical debt might have been intentionally introduced to hit deliverable targets but needs to be paid back down.

**Build a performance culture on your team.** A performance culture involves creating an environment where continuous improvement is expected and the team learns from production. Performance optimization requires specialized skills. Workload teams need the right skills and mindset to optimize performance to meet increases and decreases in demand. You also need to allocate their time to support the required monitoring and remediation of performance issues as they arise. These teams need clear expectations. For example, performance targets, baselines, and deviation thresholds (how far from baseline is acceptable) need to be highly visible and socialized.

**Prioritize known deteriorating components.** You need to make a continuous effort to optimize the performance of solution components that may deteriorate over time. Databases and networking components are prone to performance degradations over time. Workload growth and usage pattern changes can disproportionally impact the performance of individual components in your workload. You need to attend to these components. 

**Automate performance optimization.** Automation reduces manual effort required in some optimization tasks.

-   **View automated recommendations.** Cloud platforms have features that generate performance recommendations. You should view and address automated recommendations regularly.

-   **Use automatic tuning capabilities.** For example, you should configure automatic tuning on databases where available. These features include automatic creating missing indexes, dropping unused indexes, and plan correction. 

**Optimize database queries.** Poorly written SQL statements can degrade database performance. Inefficient JOIN conditions can cause unneeded data processing. Complex subqueries, nested queries, and excessive functions can cause slow execution. Queries that retrieve too much data should be rewritten. You should identify your most common or critical database queries and optimize them. The optimization helps ensure faster query execution.

**Maintain indexes.** Database indexes help accelerate data retrieval operations. For relational databases, you need to monitor index fragmentation. You should rebuild or reorganize indexes on a regular basis. For non-relational databases, you need to pick the correct indexing policy for your workload. For more information, see [Maintaining indexes to improve performance](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes?view=sql-server-ver16).

**Maximize platform features and tooling.** Many platform features and tooling can surface performance recommendations based on the actual usage. Understanding the full capabilities of your hosting platform or insights tooling is critical. You can't maximize their benefits until you know how to use them. You should enable and use any automation feature they provide while customizing them to fit your needs as they evolve. Regular updates are essential to getting the best recommendations and insights from third-party tools. You should capture and evaluate performance signals from your platform and tools.

**Maximize data efficiency.** Data tiering and setting a time to live on data can have significant effects on performance efficiency.

-   **Use data tiering.** Data tiering is the process of categorizing data based on usage patterns and value. Priority data might use high-performance storage tiers and less critical data on slower resources. The goal is to review data usage over time to ensure data is in the correct tier. As data priorities change, data should move from one tier to another.

-   **Implement a time to live.** A time-to-live (TTL) is a mechanism that sets an expiration on data. When the data reaches its TTL, the data is removed. Session data, temporary files, and cache data are frequent targets for TTL. Database entries can also have a TTL. Setting a time to live on data can significantly reduce unnecessary storage of unneeded data over time and avoid performance degradation in query situations. However, setting a TTL that's too short could actually introduce performance issues.

## Azure facilitation

-   Azure Advisor provides automatic [performance recommendations](/azure/advisor/advisor-performance-recommendations) based on workload telemetry. You should review and address these recommendations regularly.

-   Azure SQL Database has an [automatic tuning](/azure/azure-sql/database/automatic-tuning-overview?view=azuresql-mi) feature that continuously monitors and improves queries. You should use this features to improve SQL queries automatically.

-   The SQL family of products has a number of [built-in features](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes?view=sql-server-ver16) that allow you to monitor and remediate SQL database performance. You should use these features to maintain database performance.

-   Azure Cosmos DB has features that allows you to [customize your indexing policies](/azure/cosmos-db/index-policy). You should customize the policies to meet the performance needs of your workload.

## Tradeoff

-   Continuous performance optimizations requires a team with the right skills and time to find and fix performance issues. Dedicating personnel to performance adds operational cost. With limited personnel resources, continuous performance optimization could take time away from other operational tasks.

## Example

## Related links
