---
title: Workload team personas involved in AI workloads
description: Different personas work on different aspects of the workload. This article highlights their roles and responsibilities and interactions with each other. 
author: jostrm
ms.author: prwilk
ms.date: 10/29/2024
ms.topic: conceptual
---

# Workload team personas involved in AI workloads

In the context of building AI workloads, unlike traditional code deployment, nondeterministic models require iterative experimentation and collaboration across multiple roles and teams. Early integration of operations, application development, and data teams is essential to foster mutual understanding. This collaboration demands diverse skills and continuous learning to keep pace with technological advancements.

Effective collaboration hinges on integrating tools, processes, and people, all driven by workload needs and specific goals. The recommended strategies include:

- Establishing clear roles and accountabilities.
- Using your team's skill set for appropriate tasks.
- Standardizing processes and subprocesses, such as tracking work as part of a shared backlog.
- Relying on automation to achieve consistency and reproducibility.

Personas are an effective tool for materializing those strategies and standardizing responsibilities. This article explores the concept of personas found in AI workloads and their benefits in workload design. Examples and tools are provided for defining and using these team-level personas effectively.

## What are personas?

Personas represent subsets of humans and processes involved in the creation and running of a workload. They capture their roles and their real behaviors and accountabilities. An individual can embody one or multiple personas depending on the context. Interestingly, a persona doesn't have to be a person. It can also be an unattended process, such as an agent process within the architecture.

Your workload might have user personas that drive feature development. Those personas aren't in scope for this article.

Unlike roles, which are typically more static functions or positions within an organization, personas are dynamic and goal-oriented. You can use them to map skill requirements to the processes and tools, such as architectural components. Personas primarily help define the scope of responsibility and set context within a project. They offer several other benefits, such as:

- **Identification of resource gaps:** Personas can help you decide whether to recruit, train, or redesign the solution. If your workload team lacks individuals that fit a necessary persona, you might need to adjust the architecture, modify the process, or onboard new personnel. For example, if a senior data science persona is missing, redesign the architecture. Consider higher reliability on general-purpose software as a service (SaaS) AI solutions or incorporate third-party AI solutions.
- **Enhanced skills:** Mapping personas to specific architectural components also helps you to develop educational opportunities by providing sessions and online courses to enhance skills.
- **Ensure the appropriate levels of access:** Personas help to define security and access needs. Map them to processes, architectures, and services to ensure appropriate access levels.
- **Project planning and communication:** In project planning, personas can identify key interactions. They help to facilitate the setup of sync meetings and overall planning. Typically, personas are integrated into the hierarchy of tracking user stories, features, and requirements to help streamline project management.

## How to define personas

Identify your team members' specializations and align them with the appropriate roles in your AI operations or design. Create a template to document the persona's skill expectations, team information, and process involvement.

Here's an example baseline template:

|Persona template|
|---|
|&#128313; Persona name: [Insert persona name]<br>&#128313; Team: [Team responsible for this persona]<br>&#128313; Primary interaction: [Other teams this persona interacts with]<br>&#128313; Components access: [Security and access requirements for processes and system components]<br>&#128313; Processes: [Processes the persona is responsible for or contributes to]<br>&#128313; Skills: [Skills required to complete the tasks, including domain and technology specifics such as model training or search index optimization]

### Tools

A table can help you organize and visualize information for each persona. The advantage of using a table is that you can create and link other tables for deeper information. For example, you can link architecture components to another table where identity-based access control is specified for each service and environment (Dev, Stage, Production).

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Having too few personas can make it difficult to implement role-based access control (RBAC) with least-privileged access and to distribute work responsibilities effectively. Conversely, having too many personas adds management overhead. Starting with 5 to 10 personas is a good balance. Add personas that are only necessary for your operations.

You can also use cards to define personas. The cards contain the same information as the table or a quick summary. To create cards, use PowerPoint or make them as a set of Markdown files.

In certain cases, you can use a combined set of tools. For example, each architecture component in a persona card can open a Markdown file with a table that maps security and RBAC for each service and environment. For a reference example, see [Machine learning operations (MLOps) accelerator: Identity RBAC](/azure/architecture/ai-ml/guide/machine-learning-operations-v2#identity-rbac).

### Example personas

By using cards, you can define the services to which a persona needs access within a process and outline the prerequisite skills required for each persona (whether a person or an agent).

> [!IMPORTANT]
> Although the personas defined here serve as baseline examples, we recommend that you create your own personas by using tools like tables, persona template cards, and graphs.
>
> Align these personas with your specific processes, organization, and users.

|AI Data Engineer (P001)|
|---|
| Team: Data Ingestion Team<br>&#128313; Primary interaction: AI Development Team<br>&#128313; Components access: Azure Data Factory, Azure Databricks, Azure SQL Database, Azure Storage <br>&#128313; Processes: DataOps, ETL, ELT<br>&#128313; Skills: SQL, Python, PySpark

|BI Analyst (P003)|
|---|
| Team: Analytics Team<br>&#128313; Primary interaction: Data Ingestion Team<br>&#128313; Components access: Power BI, Azure Data Explorer, Azure Storage <br>&#128313; Processes: Data analysis, data warehousing process<br>&#128313; Skills: SQL, Python, PySpark

|Discriminative AI Data Scientist (P004)|
|---|
| Team: AI Team<br>&#128313; Primary interaction: Data Ingestion Team, DevOps Team<br>&#128313; Components access: Azure Machine Learning, Azure Databricks, Azure Storage, Azure Key Vault <br>&#128313; Processes: MLOps, MLflow<br>&#128313; Skills: Azure Machine Learning, Python, model training

|GenAI Data Scientist (P006)|
|---|
| Team: AI Team<br>&#128313; Primary interaction: Data Ingestion Team, DevOps Team<br>&#128313; Components access: Azure AI Studio, Azure OpenAI Service, Azure AI Search, Azure Storage, Azure Key Vault <br>&#128313; Processes: GenAIOps<br>&#128313; Skills: Azure Machine Learning, Python, model (LLM, SLM) knowledge, fine-tuning, RAG, agentic concept

|GenAI Chat Developer (P007)|
|---|
| Team: Engineering Team<br>&#128313; Primary interaction: AI Team<br>&#128313; Components access: Azure WebApps, Azure API Management, Azure Cosmos DB, Azure Container Apps, Azure Functions <br>&#128313; Processes: DevOps, event-driven processing, microservices<br>&#128313; Skills: Web application architecture (front end/back end), React, Node.js, HTML, CSS

|Build Agent MLOps (P009)|
|---|
| Team: Engineering Team<br>&#128313; Primary interaction: AI Team<br>&#128313; Components access: Azure Machine Learning, Azure DevOps, GitHub <br>&#128313; Processes: Process/Serving of LAMBDA, outer loop MLOps <br>&#128313; Skills: Python, Pyspark

## Use case: Personas for AI processes

The main processes related to AI workloads are:

- **DataOps**: Focuses on data ingestion and preparation.
- **MLOps**: Involves operationalizing machine learning models.
- **GenAIOps**: Pertains to discovering and evaluating existing models and then refining them to your workload context.
- **Inner loop**: Refines solutions in the development environment, either during research or triggered by outer loop monitoring.
- **Outer loop**: Moves solutions from development to production by using continuous monitoring and evaluation to identify necessary improvements.

Mapping personas to those processes provides context for each persona. Mapping helps to identify the processes where a persona might need upskilling.

:::image type="content" source="images/personas-to-processes.png" alt-text="Diagram that shows DataOps, MLOps, and GenAIOps within a production environment." lightbox="images/personas-to-processes.png":::

The image shows the workflow for DataOps, MLOps, and GenAIOps within a production environment. Data flows from ingestion to model deployment and evaluation by using continuous integration/continuous deployment (CI/CD) practices. Key tasks include refining data models, evaluating batches, deploying endpoints, evaluating real-time models, and fine-tuning models. The example personas participate in the entire workflow.

## Use case: Personas for architecture design

Connecting processes to the supporting architecture helps you to identify the services with which a persona needs to interact. The exercise highlights areas for potential upskilling.

To visualize this connection, create a graphical image that shows how architecture components are connected. It can illustrate data flow and interactions between services and how flows are automated in deployment. This visual aid helps stakeholders understand the architecture and the roles of different personas within it.

The following image shows a LAMBDA architecture for modern analytics on Azure.

:::image type="content" source="images/personas-to-architectures.png" alt-text="Diagram that shows a LAMBDA architecture for modern analytics in Azure." lightbox="./images/personas-to-architectures.png":::

## Next step

Now proceed to the assessment tool to evaluate your design.

> [!div class="nextstepaction"]
> [AI workload assessment](./assessment.md)