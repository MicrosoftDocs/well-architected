---
title: AI Workload Operations on Azure
description: Learn about AI workload operations on Azure and how to build operational mechanisms and practices to better support AI workloads.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2024
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# AI workload operations on Azure

When you build and transition your AI workloads to production, it's important that your operations teams are fully equipped to support these workloads like they would any other production workload. Because your operations teams might have limited experience with AI technologies, it's essential to train them on these technologies and integrate the AI workloads into their workflows early in the process. Bring operations and data teams together early in the development of the AI workload to foster mutual understanding of each team's processes. This early collaboration is crucial because both teams need to work closely to support the AI workload effectively. Data teams depend on operations teams to provide reliable health signals and actionable alerts. Operations teams depend on data teams to help diagnose potential problems and resolve actual problems according to operational standards. This partnership helps ensure smooth and efficient system performance.

This guide provides recommendations for how to develop operational mechanisms and practices to improve support for AI workloads. It emphasizes efficient collaboration between operations and data teams.

## Recommendations

Here's the summary of recommendations provided in this article.

|Recommendation|Description|
|---|---|
|**Monitor all aspects of your workload**. | While many general monitoring and observability concerns also apply to AI workloads, there are specific considerations that you need to work through to ensure that the whole of your workload is appropriately monitored at all times. To build your monitoring and observability strategy, you might need to work across different teams to get the right expertise and cover all relevant modes and metrics.<br><br>&#9642; [Extend the observability platform](#extend-the-observability-platform)|
|**Apply safe deployment practices to your AI workload**. | Take steps to ensure the highest level of safety around sensitive production data and align deployment approaches with zero-downtime requirements. Use the appropriate tools where necessary, and put an emphasis on not reinventing tools and processes that already exist. Often you'll be able to use established services to achieve a high level of efficiency while also enabling safe deployments. <br><br>&#9642; [Include AI workload components in your safe deployment practices](#include-ai-workload-components-in-your-safe-deployment-practices)|
|**Embrace DevOps practices on testing and automation**. | Apply DevOps practices when building, deploying, and operating your AI workload in production. Your workload should allow for observability and testing with real user input in production. This can only be provided in a safe manner if a strong DevOps process and streamlined automation allow for swift deployments, error correction, and A/B testing. <br><br>&#9642; [Support testing in production](#support-testing-in-production)<br>&#9642; [Automate operational practices when possible](#automate-operational-practices-when-possible)<br>&#9642; [Embrace DevOps practices](#embrace-devops-practices)|
|**Document your progress**. | Build good documentation habits from the start to allow the capturing of strategic decisions, change history, and key information about the data that your workload uses. <br><br>&#9642; [Adopt good documentation practices](#adopt-good-documentation-practices)|

## Extend the observability platform

To achieve operational excellence, robust observability is essential. When your organization adopts AI technologies, it's crucial that you enhance your observability platform to help ensure comprehensive monitoring of workload health. Organizations new to AI might lack big data, data science, and DataOps expertise within their operations team. Therefore, training on the operational best practices is an important first step to improve your observability platform. For this reason, operations and data teams should collaborate to determine the correct modes of monitoring and metrics to capture and analyze.

To evaluate the health of a model, a comprehensive overview of its specific quality metrics is required. Quality measurements typically include metrics such as model freshness, output correctness, and response latency. However, you should collaborate with data scientists and engineers to establish the specific metrics that define your workload's quality. The nondeterministic nature of AI workloads makes vigilant monitoring of quality especially important because those measurements can change unexpectedly at any time after deployment. Recommendations for observability include:

- Work with data scientists and engineers to determine quality metrics.

- Build or extend dashboards to assess the overall health of the workload. This approach should include component availability metrics and quality metrics.

- Implement well-designed availability and quality alerts that the operations teams can understand and take action on.

- Codify standard operating procedures that define how operations teams respond to quality alerts, like working with data teams to investigate and remediate potential malfunctions.

- Pay close attention to utilization metrics because it can be cost-intensive to run AI workloads. Costs can grow quickly if the workload team doesn't shut down, scale down, or deallocate resources when they're not in use. Operations can help ensure that costs remain within expected parameters by monitoring utilization.

## Include AI workload components in your safe deployment practices

AI workloads depend on production data, which often includes sensitive information. Therefore, it's important to maintain the highest level of safety around these workloads. To help protect your data, extend your [safe deployment practices](../operational-excellence/safe-deployments.md) to include all of the code that's related to your workload's AI components. If you have zero-downtime requirements for your workload, design your deployment approach for the AI components accordingly.

- For inferencing endpoints, use blue-green or canary deployments with or without traffic mirroring, depending on your deployment model.

- For index serving, use a side-by-side deployment model with alias updates to cut traffic over.

- For orchestration code, use feature flags or blue-green deployments.

Depending on your application, data platform, and specific network topology, you might need to use a gateway solution, like [Azure Application Gateway](/azure/application-gateway/overview) or [Azure Front Door](/azure/frontdoor/front-door-overview).

### Tools

Azure Machine Learning supports [blue-green deployments](/azure/machine-learning/how-to-safely-rollout-online-endpoints) natively with built-in traffic splitting.

### Don't reinvent the wheel

Because online inferencing endpoints are essentially microservices, they operate as fully self-contained workload components with their own data and code that serve a specific function in the workflow. For this reason, treat online inferencing endpoints like other critical microservices that have their own life cycles. You can update online inferencing endpoints individually. However, like other microservices in a larger workload, they must work together seamlessly. Therefore, you should prioritize integration testing when you deploy updates. Make sure that your deployments don't negatively affect other services, like model serving and the orchestrator. Alternatively, batch inferencing endpoints are often closely coupled with the job processing compute, and are included in the data pipeline. In these cases, treat them as part of a larger solution instead of as microservices.

## Support testing in production

The operations teams will likely not design or perform [AI workload testing](./test.md). However, they must support AI workload testing through monitoring, alerting, and investigation because testing is needed in production. Because of AI's nondeterministic nature, testing in production is necessary to help ensure that the workload is performing as expected over time. The operations team should partner closely with the workload team to capture and diagnose abnormal test results efficiently and according to operational standards. To ensure that operations can support testing and that workload teams can perform testing, both teams need to align on their processes to work together. Training on testing activities in nonproduction environments helps familiarize the team with the order of operations.

## Automate operational practices when possible

Automate all workload-related operational practices, including monitoring, alerting, and testing processes. Automation is a primary strategy to keep processes repeatable, efficient, and consistent. When you design your automation strategies, require human oversight for activities, such as properly diagnosing model inconsistencies and other quality signals. This is particularly true of initial releases. Processes that are related to maintaining the model are new for the operations teams, so the risk of improper responses to quality signals is higher at this stage. As your workload matures, you might be able to use automation to identify false alarms with well-designed quality gates, but getting to that point can be a long and complex process.

When possible, reuse existing automation tools to perform new automation tasks for your AI workload. For example, if you already use Azure Pipelines or GitHub Workflows, continue to use them for deploying orchestration code instead of using a separate tool. However, avoid over-engineering in an effort to only use one automation tool. If certain tasks or jobs aren't well-suited to your chosen tool, choose a more appropriate tool instead of building a custom solution that adds unnecessary complexity.

> [!NOTE]
> To fully support the workload, many intersecting roles and technologies are required. Data, infrastructure, security, and operations teams all depend on automation to some extent. Use the fewest possible tools and standardize them to help keep the automation strategy manageable and easier to train on.

## Embrace DevOps practices

Early in your workload development, standardize the use of [DevOps practices](/devops/what-is-devops#implement-devops-practices) in your development processes. For all environments besides sandbox environments, strictly defined and enforced standards should govern the entire development life cycle. Enforce these standards by strictly prohibiting any manual deployment activities. All deployments, whether they're updates, patches, or new resource deployments, must be done through a continuous integration and continuous deployment pipeline that adheres to [safe deployment practices](../operational-excellence/safe-deployments.md). Good DevOps hygiene practices should include:

- **[Version control](/devops/what-is-devops#version-control):** Use version control for all code assets as much as possible. Version control is a cornerstone tenet of DevOps and good change management practices and is vital for smooth collaboration. Apply version control to library packages, including SDKs and container images. Be consistent in using a specific version of library packages across the software development life cycle (SDLC). Different versions of libraries like XGBoost or scikit-learn in various development environments can cause variations in your workload's behavior. The same is true for model versioning. Ensure that model versions are consistent across the SDLC so that you don't test one version of the model in a preproduction environment and release a different version in production.

- **Simple version naming scheme:** Use a simple version naming scheme to help ensure that you always use the most recently approved version of a given asset. AI-specific assets can include:

  - Notebook code.
  - Orchestrator code.
  - Data processing job code.
  - Machine Learning job code.
  - The model.
  
- **Tools that you can operationalize:** Some tools are great for experimentation but aren't designed for operationalization. This limitation can make it difficult or impossible to integrate them into your operational automation. For example, notebooks are a good tool for experimentation and exploratory data analysis work, but they aren't a good tool for production pipeline development work. When you complete your experimentation, pull the logic out of those tools and put it into a Python package that you can use for your job code.

## Adopt good documentation practices

Good documentation habits are important for all types of workloads. The complexity of AI workloads makes those habits even more important. Ensure that you have a repository that's specific to your workload team where documentation is securely stored and version controlled. Like with other workloads, you should document standard information. This standard information includes all of the tools that are used in the workload, security configurations, network designs, and setup guides. Consider documenting the following AI-specific workload information:

- **Model training and grounding data index management information:** Document data sources, the owner of data sources, refresh frequency, and bias elimination processes to establish how to train the model and how to handle grounding data.

- **History of the training process:** Detail how you arrived at the current approved configuration by documenting why you chose specific hyperparameters, temperatures, weights, and other parameters. Include what other configurations you tested and the behavioral changes you observed throughout the training process. This information helps prevent the repetition of unsuccessful or inefficient configurations.

- **Feature store information:** Document which features have the best predictive power and how you made that determination.

- **Data scientist workstation configuration:** Document workstation configurations thoroughly to streamline the onboarding process for data scientists. Specify the necessary libraries and dependencies that are required to use conda environments.

- **Data transformation information:** Document the entire process from start to finish when data transformation occurs. Document how the data appears at ingestion and how it's presented after the transformation.

### Tools

Take advantage of automated configuration and history capturing through MLFlow and Machine Learning.

## Next steps

> [!div class="nextstepaction"]
> [Design area: Testing and evaluation](./test.md)
