---
title: Workload team personas for AI workloads
description: Different personas work on different aspects of the workload. This article highlights their roles and responsibilities and interactions with each other. 
author: jostrm
ms.author: prwilk
ms.date: 08/06/2025
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# Workload team personas for AI workloads

In the context of building AI workloads, in contrast to traditional code deployment, nondeterministic models require iterative experimentation and collaboration across multiple roles and teams. Early integration of operations, application development, and data teams is essential to foster mutual understanding. This collaboration demands diverse skills and continuous learning to keep pace with technological advancements.

Effective collaboration hinges on *integrating tools, processes, and people* and is driven by workload needs and specific goals. The recommended strategies include:

- Establishing clear roles and accountabilities.
- Taking advantage of your team's skill set for appropriate tasks.
- Standardizing processes and subprocesses, such as tracking work as part of a shared backlog.
- Relying on automation to achieve consistency and reproducibility.

Personas can be an effective tool for materializing those strategies and standardizing responsibilities. This article describes the personas for AI workloads and their benefits in workload design. It also provides examples and tools for defining and using these team-level personas effectively.

## What are personas?

Personas represent subsets of humans and processes that are involved in the creation and operation of a workload. Personas capture both the roles and the real behaviors and accountabilities of these individuals and processes. An individual can embody one or multiple personas, depending on context. A persona doesn't have to be a person. It can also be an unattended process, such as an agent process in the architecture.

Your workload might have user personas that drive feature development. Those personas aren't in scope for this article.

Unlike roles, which are relatively static functions or positions within an organization, personas are dynamic and goal oriented. They can be used to map skill requirements to processes and tools, like architectural components. Personas primarily help define the scope of responsibility and set context within a project. They provide several other benefits, such as:

- **Identification of resource gaps.** Identifying gaps helps you decide whether to recruit or train resources or redesign the solution. If your workload team lacks individuals that fit a necessary persona, you might need to adjust the architecture, modify the process, or onboard new personnel. For example, if a senior data science persona is missing, you can redesign the architecture to rely more on general-purpose software as a service (SaaS) AI solutions or incorporate non-Microsoft AI solutions.
- **Enhanced skills.** Mapping personas to specific architectural components also facilitates educational opportunities, like sessions and online courses to enhance skills.
- **Ensuring appropriate levels of access.** You should use personas to define security and access needs by mapping personas to processes, architectures, and services. This mapping helps to ensure appropriate access levels.
- **Facilitating project planning and communication.** In project planning, personas help identify key interactions to facilitate the setup of sync meetings and overall planning. Typically, personas are integrated into the hierarchy of tracking user stories, features, and requirements to streamline project management.

## Agentic personas

Agentic team roles need different persona management than traditional human-centered or traditional process-centered roles because they work fundamentally differently from deterministic systems. Unlike traditional systems where you know exactly which services will be called and in what order, agentic systems make decisions on their own at runtime. These decisions depend on the data they receive and how well the AI models perform. This creates complexity in managing authentication, authorization, and controlling what tools and agents can access.

Agentic workloads require specialized persona management built on key areas:

- **Multi-cloud and cross-system persona definition**. Set up persona governance that works across different cloud platforms, SaaS services, and Model Context Protocol (MCP) servers, and not just a single cloud environment. This means defining agent personas with the right access permissions, dynamic authentication methods like agent-to-agent (A2A), and clear rules for interactions across different platforms.

- **Dynamic persona permissions**. Create persona models that support flexible, workflow-aware access control. This includes providing permissions just when they're needed, granting tool-specific access during different phases, and being able to increase or restrict agent capabilities based on the situation and risk level. Unlike traditional managed identities that follow predictable patterns, agent personas must handle unpredictable decision-making and runtime tool selection.

- **Autonomous agent persona accountability**. Set up persona governance that keeps clear accountability chains for what agents do, since they make their own decisions at runtime. This includes audit trails that connect agent decisions back to responsible human personas, ways for humans to step in and override agent decisions, and clear escalation paths when agents go beyond their defined boundaries. Because agent behavior is non-deterministic, calling different tools and agents based on data and model performance, you need stronger monitoring and governance than traditional automation systems.

Consider a CI/CD pipeline for an AI workload with agentic personas for code review, testing orchestration, and deployment validation. When a developer submits code changes, the Dev Router agent analyzes the code changes and routes the request dynamically to the right testing agent,  based on the type of changes. Unlike traditional pipelines with fixed routing rules, this approach allows agent personas to join or leave the system dynamically, creating non-deterministic behavior that needs specialized persona management for governance, security, and accountability.

Each agent persona needs different permissions. For example, the unit testing agent needs access to the repo where the source is located, but a load testing agent may also need access to Azure AI Foundry, Azure Monitor and other resources. Because agent interactions are dynamic, you need just-in-time permission provisioning and audit trails that connect autonomous agent decisions back to responsible human personas. This ensures accountability even when agents make unexpected tool choices at runtime.

## How to define personas

Identify your team members' specializations and align them with the appropriate roles in your AI operations or design. Create a template to document personas' skill expectations, team information, and the processes in which they'll be involved.

Here's an example baseline template:

|Persona template|
|---|
|&#128313;Persona name: [Name]<br>&#128313;Team: [Team responsible for the persona]<br>&#128313;Primary interaction: [Other teams or agents the persona interacts with]<br>&#128313;Component access: [Security and access requirements for processes and system components]<br>&#128313;Processes: [Processes the persona is responsible for or contributes to]<br>&#128313;Skills: [Skills required to complete the tasks, including domain and technology specifics like model training or search index optimization]

### Tools

You can use a table to organize and visualize information for each persona. One advantage to this method is that you can create and link to other tables that provide more specific information. For example, you can link architecture components to another table where identity-based access control is specified for each service and environment (Dev, Stage, Production).

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Having too few personas can make it difficult to implement role-based access control with least privileged access and to distribute work responsibilities effectively. Conversely, having too many personas adds management overhead. Starting with between 5 and 10 personas is a good balance, and you should only add personas that are necessary for your operations.

You can also use cards to define personas. These cards contain the same information as the table, or a quick summary. You can use PowerPoint or make a set of Markdown files to create these cards.

In certain cases, you can use a combination of tools. For example, each architecture component in a persona card can open a Markdown file that includes a table that maps security and role-based access control for each service and environment. For an example, see [MLOps accelerator: Identity RBAC](/azure/architecture/ai-ml/guide/machine-learning-operations-v2#identity-rbac).

### Example personas

The example personas represent the teams that participate in developing and operating AI workload solutions. These include both human personas and automated agent personas that help build and manage the solution, which are distinct from the agents that implement the actual business functionality. Even in non-agentic solutions, automated processes run under specific identities to support development and operations workflows.

You can use cards to define the services a persona needs to be able to access within a process and outline the skills required for each persona (whether it's a person or an agent).

> [!IMPORTANT]
> Although the personas defined here serve as baseline examples, we recommend that you create your own personas by using tools like tables, persona template cards, and graphs.
>
> It's important that these personas align with your processes, organization, and users.

#### Development personas 

|GenAI Data Scientist (P006)| 
|---| 
| Team: AI Team<br>&#128313; Primary interaction: Data Ingestion Team, DevOps Team<br>&#128313; Component access: Azure AI Foundry, Azure OpenAI Service, Azure AI Search, Azure Storage, Azure Key Vault <br>&#128313; Processes: GenAIOps, inner loop development<br>&#128313; Skills: Azure AI Foundry, Azure OpenAI Service, Python, model knowledge (LLM, SLM), fine-tuning, RAG, agentic solutions 

|GenAI Chat Developer (P007)| 
|---| 
| Team: Engineering Team<br>&#128313; Primary interaction: AI Team<br>&#128313; Component access: Azure AI Foundry, Azure Web Apps, Azure API Management, Azure Cosmos DB, Azure Container Apps, Azure Functions <br>&#128313; Processes: DevOps, event-driven processing, microservices, inner loop development<br>&#128313; Skills: Web application architecture (front end/back end), React, Node.js, HTML, CSS,  agentic solutions 

|Dev Router Agent (P010)| 
|---| 
| Team: Engineering Team (automation)<br>&#128313; Primary interaction: Unit Test Agent, Load Test Agent<br>&#128313; Component access: Azure DevOps, GitHub, Azure AI Foundry, Azure Container Registry<br>&#128313; Processes: Automated agent routing, DevOps<br>&#128313; Skills: Python, Agent-2-Agent 

|Dev Unit Test Agent (P011)| 
|---| 
| Team: Engineering Team (automated)<br>&#128313; Primary interaction: Dev Router Agent, AI Team, Engineering Team<br>&#128313; Component access: Azure DevOps, GitHub, Azure AI Foundry, Azure Container Apps, MCP Server Tools<br>&#128313; DevOps<br>&#128313; Skills: Test automation, code coverage analysis, performance benchmarking, mcp-testing frameworks 


#### Operations personas

|AI Data Engineer (P001)| 
|---| 
| Team: Data Ingestion Team<br>&#128313; Primary interaction: AI Development Team, Operations Team<br>&#128313; Component access: Azure Data Factory, Azure Databricks, Azure AI Foundry, Azure SQL Database, Azure Storage <br>&#128313; Processes: DataOps, ETL, ELT<br>&#128313; Skills: SQL, Python, PySpark 

|BI Analyst (P003)| 
|---| 
| Team: Analytics Team<br>&#128313; Primary interaction: Data Ingestion Team<br>&#128313; Component access: Power BI, Azure Data Explorer, Azure AI Foundry, Azure Storage <br>&#128313; Processes: Data analysis, data warehousing<br>&#128313; Skills: SQL, Python, PySpark | 

|Discriminative AI Data Scientist (P004)| 
|---| 
| Team: AI Team<br>&#128313; Primary interaction: Data Ingestion Team, DevOps Team, Operations Team<br>&#128313; Component access: Azure Machine Learning (for training scenarios), Azure AI Foundry, Azure Databricks, Azure Storage, Azure Key Vault <br>&#128313; Processes: MLOps, MLflow<br>&#128313; Skills: Azure AI Foundry, Azure Machine Learning, Python, model training, production model monitoring 

|Build Agent MLOps (P009)| 
|---| 
| Team: Engineering Team (automated)<br>&#128313; Primary interaction: AI Team, Operations Team<br>&#128313; Component access: Azure AI Foundry, Azure Machine Learning (for training scenarios), Azure DevOps, GitHub <br>&#128313; Processes: Processing and serving of Lambda, outer loop MLOps, automated deployment<br>&#128313; Skills: Python, PySpark, model versioning 

|Production Monitoring Agent (P012)| 
|---| 
|Team: Operations Team (automated)<br>&#128313; Primary interaction: AI Team, Engineering Team, Operations Team<br>&#128313; Component access: Azure Monitor, Azure Application Insights, Azure Log Analytics, Azure AI Foundry<br>&#128313; Processes: Continuous monitoring, anomaly detection, performance tracking, automated alerting<br>&#128313; Skills: Monitoring automation, log analysis, performance metrics, alerting workflows 

## Use case: Personas for AI processes

These main processes are used in AI workloads:

- DataOps is the ingestion and preparation of data.
- MLOps is the operationalization of machine learning models.
- GenAIOps is the discovery and evaluation of existing models and the refinement of these models to the workload context.
- Inner loop is the refinement of solutions in the development environment, either during research or as triggered by outer loop monitoring.
- Outer loop is the movement of solutions from development to production. This loop uses continuous monitoring and evaluation to identify necessary improvements.

Mapping personas to those processes provides context for each persona. This step can help identify the processes where a persona might need upskilling.

:::image type="content" source="images/personas-to-processes.png" alt-text="Diagram that shows DataOps, MLOps, and GenAIOps within a production environment." lightbox="images/personas-to-processes.png":::

The image shows the workflow for DataOps, MLOps, and GenAIOps within a production environment. Data flows from ingestion to model deployment and evaluation. The workflow uses continuous integration and continuous delivery (CI/CD) practices. Key tasks include refining data models, evaluating batches, deploying endpoints, evaluating models in real-time, and fine-tuning models. The example personas participate in the entire workflow.

## Use case: Personas for architecture design

Connecting processes to the supporting architecture helps you identify the services that a persona needs to interact with and highlights areas for potential upskilling.

To visualize this connection, create a graphical image that shows how architecture components are connected. This visual aid can illustrate data flow and interactions between services and how flows are automated in deployment. It helps stakeholders understand the architecture and the roles of different personas within it.

The following image shows a Lambda architecture for modern analytics on Azure.

:::image type="content" source="images/personas-to-architectures.png" alt-text="Diagram that shows a Lambda architecture for modern analytics in Azure." lightbox="./images/personas-to-architectures.png":::

## Next step

Next, proceed to the assessment tool to evaluate your design.

> [!div class="nextstepaction"]
> [AI workload assessment](./assessment.md)
