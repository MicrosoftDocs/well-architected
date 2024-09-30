---
title: AI workload operations on Azure
description: AI workload operations including deployment on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-ai
---

# AI workload operations on Azure
As you build and take your AI workload to production, your operations teams need to be fully prepared to support the workload like any other workload in production. Your operations teams may have little or no experience with AI technologies, so its important to start training on these technologies and getting the workload integrated into their workflows early on. Getting operations and data teams together early in the workload development can help both teams become familiar with each other and build an understanding of how each team works. This will be important because both teams will need to collaborate on the support of the workload in many cases. The data teams will need to rely on the operations teams to provide quality health signals and actionable alerts, whereas the operations teams will need to rely on the data teams to efficiently assist in diagnosing potential issues and resolve actual issues in accordance with operations' standards.

This guide provides recommendations for building operational mechanisms and practices to better support AI workloads, taking into consideration how operations and data teams will need to collaborate efficiently.

## Extend the observability platform

Robust observability is at the heart of operational excellence and as your organization adopts AI technologies, your observability platform must be uplifted to ensure that a full picture of the workload health is captured. Organizations new to AI may not have big data or data science and DataOps expertise in the operations team, so training on the operational best practices for these disciplines is an important first step in uplifting your observability platform. For this reason, operations and data teams should collaborate to determine the correct modes of monitoring and metrics to capture and analyze. In particular, determining the health of a model relies on building a full picture of its specific quality measurements. Quality measurements generally include metrics like model freshness, output correctness, and response latency, but the specific metrics that determine your workload's quality should be determined with data scientists and engineers. The non-deterministic nature of AI workloads make vigilant monitoring of quality especially important as those measurements can change unexpectedly at any time after deployment. Recommendations for observability include:

- Work with data scientists and engineers to determine quality metrics.
- Build or extend dashboards to capture the workload's health in totality (a combination of the components availability metrics and the quality metrics).
- Implement well-designed availability and quality alerts that are understandable and actionable for the operations teams.
- Codify standard operating procedures (SOPs) defining how operations teams respond to quality alerts, like engaging with data teams to investigate and remeditate potential malfunctions.
- Pay special attention to utilization metrics. Running AI workloads is cost-intensive, and costs can balloon quickly if the workload team doesn't shutdown, scale down, or deallocate resources when they're not in use. Operations can help ensure that costs remain within the expected parameters by monitoring utilization.

## Include AI workload components in your safe deployment practices

AI workloads rely on production data, and in many cases, sensitive data. As such, ensuring the highest degree of safety around the workload is imperative. Extending your [safe deployment practices](../operational-excellence/safe-deployments.md) to include all of the code related to your workload's AI components is one important way of protecting your data. If you have zero-downtime (ZDT) requirements for your workload, design your deployment approach for the AI components accordingly:

- For inferencing endpoints, use blue-green or canary deployments with or without traffic mirroring depending on your deployment model.
- For index serving, use a side-by-side deployment model with alias updates to cut traffic over.
- For orchestration code, use feature flags or blue-green deployments.

Depending on your choice of application and data platform and your particular network topology, you may need to use a gateway solution, like [Application Gateway](/azure/application-gateway/overview) or [Azure Front Door](/azure/frontdoor/front-door-overview).

### Tools

Azure Machine Learning supports [blue-green deployments](/azure/machine-learning/how-to-safely-rollout-online-endpoints) natively with built-in traffic splitting.

### Don't reinvent the wheel

Online inferencing endpoints are essentially microservices; they are fully self-contained workload components with their own data and code that play a specific role in the workflow. For this reason, they should be treated like other critical microservices with their own lifecycles. While online inferencing endpoints are independetly updatable, like other microservices in a larger workload, interoperability is a primary concern, so integration testing is vital when making deployments. Ensure that other services like the orchestrator and the model serving are not adversely affected by your deployments. On the other hand, batch inferencing endpoints are often closely coupled with the job processing compute, and are included in the data pipeline. In cases like this, they should be treated as part of a larger solution rather than a microservice.

## Support testing in production

The operations teams will likely not design or perform AI workload [testing](./testing.md), but must support that testing through monitoring, alerting, and investigation because testing should happen in production. Because of AI's non-deterministic nature, testing in production is necessary to accurately ensure the workload is behaving as expected over time. The operations team should partner closely with the workload team to ensure that abnormal test results are captured and diagnosed efficiently and according to operational standards. To ensure that operations is able to successfully support testing and workload teams can successfully perform testing, both teams need to align on their processes to work smoothly together. Training on testing activities in non-production environments will help build familiarity between the teams and the order of operations.

## Automate operational practices to the extent practical

Many of the operational practices related to your workload should be automatable and automation is a primary strategy in keeping processes repeatable, efficient, and consistent. For example, monitoring, alerting, and testing processes should all be automated. When designing your automation strategies, ensure that human interaction is required for many activities, like properly diagnosing model inconsistencies and and other quality signals. This is especially true of initial releases. All processes related to maintaining the model will be new for the operations teams, so the risk of improper responses to quality signals is higher at this stage. As your workload matures, you may be able to rely on automation to identify false alarms with well-designed quality gates, but getting to that point may be a lengthy and complex endeavor.

Whenever practical reuse existing automation tools to perform new automation tasks for your AI workload. For example, if you already use Azure DevOps Pipelines or Github Workflows, continue using that for deploying orchestration code rather than trying to use a separate tool. that being said, avoid over-engineering for the sake of only using one automation tool. If there are tasks or jobs that aren't a good fit for your automation tool of choice, using a better tool for the job is recommended over adding undue complexity by building a custom solution so you can keep using your tool of choice.

> [!NOTE]
> Be aware that there are many intersecting roles and technologies that will need to draw on automation to fully support the workload. Data teams, infrastructure teams, security teams, and operations teams will all rely on automation to fulfill their roles in some capacity, so using the minimum number of tools and standardizing on those tools will help keep the automation strategy manageable and trainable.

## Embrace DevOps practices

Early in your workload development, standardize the use of [DevOps practices](/devops/what-is-devops#implement-devops-practices) in your development processes. For all environments besides sandbox environments, strictly defined and enforced standards should govern the entire development lifecycle. Key to enforcing these standards is a strict policy forbidding any manual deployment activities. All deployments whether they are updates, patches, or new resource deployments must be done through a continuous deployment / continuous integration (CI/CD) pipeline following [safe deployment practices](../operational-excellence/safe-deployments.md). Good DevOps hygiene practices should include:

- **[Version control](/devops/what-is-devops#version-control):** Use version control for all code assets to the extent possible. Version control is a cornerstone tenet of DevOps and good change management practices, and is vital for collaboration to run smoothly. It's recommended that you use an easy to understand version naming scheme so that you can be certain that you're always using the most recently approved version of a given asset. AI-specific assets can include:
    - Notebook code
    - Orchestrator code
    - Data processing job code
    - Azure Machine Learning (AML) job code
    - The model
- Apply version control to library packages, including SDKs and container images as well. Its important to be intentional about consistent use of a specific version of library packages across the SDLC because using a different version of something like XGBoost or scikit-learn between development environments can yield differences in your workload's behavior.
    - The same is true for model versioning. Ensure that model versions are consistent across the SDLC so that you're not testing one version of the model in a pre-production environment, and releasing a different version in production.
- **Use tools that can be operationalized:** There are some tools that are great for experimentation, but aren't designed to be operationalized, meaning its difficult or impossible to integrate them into your operational automation. For example, notebooks are a good tool for experimentation and perhaps for exploratory data analysis (EDA) work, but they aren't a good tool for production pipeline development work. So, when you're done with your experimentation, pull the logic out of those tools and put it into a Python package that can be used for you job code.

## Adopt good documentation practices

Good documentation habits are important for all types of workloads, but the complexity of AI workloads makes it even more important. Ensure that you have a repository specific to your workload team where documentation is securely stored and version controlled. Like other workloads, you should document standard information like all of the tools used in the workload, security configurations, network designs, and setup guides. Consider documenting the following AI-specific information as well:

- **Model training and grounding data index management information:** Capture data sources, the owner of data sources, refresh frequency, and bias elimination processes to build a full picture of how the model is trained and how grounding data is handled.
- **History of the training process** Detail how you arrived at the current approved configuration by capturing things like why certain hyperparameters, temperatures, weights, and other parameters were chosen, what other configurations were tested, and what behavioral changes have been observed throughout the training process. Capturing this type of information will help you avoid repeating failed or unproductive configurations.
- **Feature store information:** Capture which features have the best predictive power and how you arrived at that determination.
- **Data scientist workstation configuration:** To help make data scientist onboarding efficient, ensure that you capture workstation configurations like what libraries need to be available to use conda environments.
- **Data transformation information:** If there is data transformation happening, capture the process from beginning to end; how the data is presented at ingestion and how it is presented after transformation.

## Tools

Take advantage of automated configuration and history capturing through MLFlow and Azure Machine Learning.