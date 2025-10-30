---
title: Responsible AI in Azure Workloads
description: Learn how to develop and implement policies that support responsible AI and handle user data appropriately in workload operations on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 10/08/2024
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# Responsible AI in Azure workloads

The goal of responsible AI in workload design is to help ensure that the use of AI algorithms is **fair**, **transparent**, and **inclusive**. Microsoft Azure Well-Architected Framework security principles are interrelated and focus on **confidentiality** and **integrity**. Security measures must be in place to maintain user privacy, protect data, and safeguard the integrity of the design. The design shouldn't be misused for unintended purposes.

In AI workloads, models often use opaque logic to make decisions. **Users should trust the system's functionality and feel confident that models make these decisions responsibly.** Unacceptable behaviors, such as manipulation, content toxicity, IP infringement, and fabricated responses, must be prevented.

Consider a use case in which a media entertainment company wants to provide recommendations by using AI models. If the company doesn't implement responsible AI and proper security protocols, a bad actor might take control of the models. The model might recommend content that causes harm. For the organization, this behavior can result in brand damage, unsafe environments, and legal problems. Therefore, maintaining appropriate vigilance throughout the system's life cycle is essential and nonnegotiable.

You should prioritize security and workload management and keep human outcomes in mind when you make design decisions. Familiarize yourself with [the Microsoft framework for responsible AI](https://www.microsoft.com/ai/responsible-ai) and ensure that you measure and implement the framework's principles in your design. The following image shows the core concepts of the framework.

:::image type="content" source="./images/responsible-ai.png" alt-text="A diagram that shows the Microsoft framework for responsible AI." border="false" lightbox="./images/responsible-ai.png":::

> [!IMPORTANT]
> The accuracy of predictions and the metrics for responsible AI are typically interconnected. By improving a model's accuracy, you can enhance its fairness and alignment with reality. Responsible AI frequently aligns with accuracy, but accuracy alone doesn't include all safety considerations. It's crucial to validate these principles responsibly.

This article provides recommendations about responsible decision-making, validating user input, and helping to ensure a safe user experience. It also provides guidance about data security to help protect user data.

## Recommendations

The following table summarizes the recommendations in this article.

|Recommendation|Description|
|---|---|
|**Develop policies that enforce moral practices at each stage of the life cycle.** |Include checklist items that explicitly state safety requirements and are tailored to the workload context. Examples include user data transparency, consent configuration, and procedures for how to handle the right to be forgotten (RTBF).<br><br>&#9642; [Develop your policies for responsible AI](#develop-policies-for-responsible-ai)<br>&#9642; [Enforce governance on policies for responsible AI](#enforce-governance-on-policies-for-responsible-ai)|
|**Protect user data with the goal to maximize privacy.** |Collect only what's necessary and with proper user consent. Apply technical controls to protect the profiles of users, their data, and access to that data. <br><br>&#9642; [Handle user data appropriately](#handle-user-data-appropriately)<br>&#9642; [Inspect incoming and outgoing data](#inspect-incoming-and-outgoing-data)|
|**Keep AI decisions clear and understandable.** |Clearly explain how recommendation algorithms work. Provide users with insights into data usage and algorithmic decision-making to help them understand and trust the process. <br><br>&#9642; [Make the user experience safe](#make-the-user-experience-safe)|
|**Implement responsible practices for agentic AI systems.** |For agents accessing public and private data, executing tasks in internal and external systems, and autonomously driving decisions, implement auditability of agent activities, role-based access control, and circuit breaker functionality. <br><br>&#9642; [Implement agentic AI safeguards](#implement-agentic-ai-safeguards)| 

## Develop policies for responsible AI

**Document your approach to responsible AI usage.** Explicitly state policies that you apply at each stage of the life cycle so that the workload team understands their responsibilities. Microsoft standards for responsible AI provide guidelines, but you must define what these guidelines mean specifically for your context.

For example, the policies should include checklist items for mechanisms that support user data transparency and consent configuration. Ideally, these mechanisms should allow users to opt out of data inclusion. Data pipelines, analysis, model training, and other stages all must respect that choice. Another example is procedures for handling the RTBF. Consult your organization's ethics department and legal team to make informed decisions.

Create transparent policies for data use and algorithmic decision-making to help users understand and trust the process. Document these decisions to maintain a clear history for potential future litigation.

Responsible AI implementation includes three key roles: the research team, the policy team, and the engineering team. Collaboration among these teams should be operationalized. If your organization has an existing team, use their work. Otherwise, establish these practices yourself.

Each team should have their own responsibilities. For example:

- **The research team conducts risk discovery** by consulting organizational guidelines, industry standards, laws, regulations, and known red-team tactics.

- **The policy team develops policies that are specific to the workload.** They incorporate guidelines from the parent organization and government regulations.

- **The engineering team implements the policies** into their processes and deliverables. The team validates and tests for adherence.

Each team formalizes its guidelines, but the **workload team must be accountable for its own documented practices**. The team should clearly document any extra steps or intentional deviations to make sure that there's no ambiguity about what is allowed. The team should also be transparent about any potential shortcomings or unexpected results in the solution.

## Enforce governance on policies for responsible AI

Design your workload to **comply with organizational and regulatory governance**. For example, if transparency is an organizational requirement, determine how it applies to your workload. Identify areas in your design, life cycle, code, or other components where you should introduce transparency features to meet that standard.

Understand the required governance, accountability, review boards, and reporting mandates. Ensure that your governance council **approves and signs off on workload designs** to avoid redesigns and mitigate safety or privacy concerns. You might need to go through multiple layers of approval. The following diagram outlines a typical governance structure in an organization.

:::image type="content" source="./images/organizational-governance-structure.png" alt-text="A diagram that shows a typical governance structure in an organization." border="false" lightbox="./images/organizational-governance-structure.png":::

For more information about organizational policies and approvers, see [Define a responsible AI strategy](/azure/cloud-adoption-framework/scenarios/ai/strategy#define-a-responsible-ai-strategy).

## Make the user experience safe

User experiences should be based on industry guidelines. Use the [Microsoft Human-AI Experiences Design Library](https://www.microsoft.com/en-us/haxtoolkit/library/), which includes principles and provides implementation guidelines. It also gives examples from Microsoft products and other industry sources.

There are workload responsibilities throughout the life cycle of user interaction. They start with a user's intent to use the system, and they continue throughout a session and during any disruptions that system errors cause. Consider the following practices:

- **Build transparency.** Make users aware of how the system generates responses to their query.

    Include links to data sources that the model consults for predictions. This practice increases user confidence by showing them the origins of the information. Data design should include these sources in the metadata. For example, when the orchestrator in a retrieval-augmented application performs a search, it retrieves 20 document chunks and sends the top 10 chunks to the model as context. The top 10 chunks belong to three different documents. The UI can then reference these three source documents when it displays the model's response. This transparency increases user trust.

    Transparency becomes more important when you use agents, which act as intermediaries between front-end interfaces and back-end systems. For example, in a ticketing system, the orchestration code interprets user intent and makes application programming interface (API) calls to agents to retrieve necessary information. Exposing these interactions helps make the user aware of the system's actions.

    For automated workflows that include multiple agents, create log files that record each step. Log files can help you identify and correct errors. They also give users an explanation for decisions, which operationalizes transparency.

    > [!CAUTION]
    > When you implement transparency recommendations, avoid overwhelming the user with too much information. Take a gradual approach by using minimally disruptive UI methods.
    >
    > For example, display a tooltip that shows a confidence score from the model. You can incorporate links, such as links to source documents, that users can select for more details. This user-initiated method keeps the UI nondisruptive and lets users seek more information only if they choose to.

- **Collect feedback.** Implement feedback mechanisms.

    Avoid overwhelming users with extensive questionnaires after each response. Use quick and simple feedback mechanisms instead, like thumbs up or thumbs down, or a rating system for specific aspects of the answer on a scale of 1 to 5. These methods help improve the system over time and allow for granular feedback without being intrusive. Be mindful of potential fairness issues in feedback because there might be secondary reasons behind user responses.

    Implementing a feedback mechanism affects architecture because of the need for data storage. Treat feedback like user data and apply levels of privacy control as needed.

    In addition to response feedback, collect feedback about the efficacy of the user experience. Collect engagement metrics through your monitoring stack of the system.

    Ensure that the feedback mechanism provides functionality to allow users **to contest AI decisions**. For example, a financial institution may use AI to automatically approve or reject credit applications, and users should be able to contest rejections. Establish clear procedures for the workload team to review and address contested decisions. 

- **Apply ethical safeguards for AI-genereated audio and video.**  Users have the right to know when they're interacting with AI-generated media rather than human-created content. Implement clear disclosure mechanisms that inform users when content is AI-generated without disrupting the user experience, and provide options for human interaction when users prefer or require human oversight for sensitive scenarios. 

    Consider implementing content watermarking or metadata tagging for AI-generated media to support transparency and traceability. Document your approach to handling requests for content verification and establish clear policies for content authenticity disclosure. 

## Implement agentic AI safeguards 

For agents accessing public and private data, executing tasks in internal and external systems, and autonomously driving decisions, **implement auditability of agent activities, role-based access control, and circuit breaker functionality to prevent catastrophic outcomes**. 

Agentic AI systems require specialized responsible AI practices beyond traditional machine learning models, built on three foundational aspects: 

- **Robust data ingress and egress control.** Establish comprehensive governance for data flows into and out of agentic systems. This includes validating data quality and security at system boundaries, ensuring proper authorization for data access, and maintaining clear separation between different agent contexts and external systems. 

- **Data validation and integrity assurance.** Maintain data quality and consistency throughout multi-agent interactions. This involves continuous monitoring of data transformations, comprehensive audit trails for decision-making processes, and verification mechanisms to prevent data corruption or manipulation across agent workflows. 

- **Autonomous agentic execution with independent guardrails.** Deploy oversight mechanisms that operate independently from the agents themselves. This includes monitoring systems that can detect anomalous behavior, automated controls that can halt problematic executions, and human intervention capabilities that allow operators to override agent decisions at any point. 

### Agent complexity considerations 

Different types of agents require varying levels of these safeguards because the potential risks and impact of their actions increase with their capabilities and autonomy. As agents move from simple retrieval tasks to complex autonomous decision-making, the need for comprehensive oversight and control mechanisms becomes more critical. 

Additionally, the level of complexity increases significantly, making it more difficult to implement responsible AI principles like fairness and transparency: 

- **Retrieval agents (read-only):** Focus on data access controls and audit logging. 

- **Task-based agents (read and write):** Require comprehensive authorization and transaction monitoring. 

- **Fully autonomous agents (multi-turn):** Need all three aspects with the highest level of oversight.

## Operationalize content safety measures

Integrate content safety into every stage of the AI life cycle by using custom solution code, appropriate tools, and effective security practices. Consider the following strategies:

- **Anonymize data.** As data moves from ingestion to training or evaluation, implement checks along the way to minimize the risk of personal information leakage and avoid raw user data exposure. Use intelligent tooling like [Azure AI Language Personally Identifiable Information (PII) detection](/azure/ai-services/language-service/personally-identifiable-information/overview?tabs=text-pii) to automatically detect and redact personal information like phone numbers, email addresses, and other types of personal information. 

- **Moderate content.** Use the content safety API that evaluates requests and responses in real time. Ensure that these APIs are reachable.

- **Identify and mitigate threats.** Apply well-known security practices to your AI scenarios. For example, conduct threat modeling and then document the threats and how you mitigated them. Typical security practices like red team exercises apply to AI workloads. Red teams can test whether models can be manipulated to generate harmful content. These activities should be integrated into AI operations.

    For information, see [Plan red teaming for large language models and their applications](/azure/ai-services/openai/concepts/red-teaming).

- **Use the right metrics.** Use metrics that effectively measure the behavior of the model. **Metrics vary depending on the type of AI model.** In some cases, measurement of generative models might not apply to regression models. For example, a model predicts life expectancy, and the results affect insurance rates. Fairness issues in this model can result in fairness-related harms. This problem stems from deviations in core metric testing because fairness and accuracy metrics are typically interconnected. Improve accuracy to help reduce fairness-related harms.

- **Add appropriate instrumentation.** AI model results must be explainable. **You need to justify and trace how inferences are made**, including the training data, the calculated features, and the grounding data. In discriminative AI, you can justify decisions step by step. However, for generative models, explaining outcomes can be complex. **Document the decision-making process** to address potential legal implications and provide transparency.

    You should implement this explainability aspect throughout the entire AI life cycle. Data cleaning, lineage, selection criteria, and processing are critical stages where decisions should be tracked.

- **Build escape hatches into your agentic design.** For agentic solutions, implement mechanisms that allow human intervention or override at critical decision points. This approach ensures that risky behaviors can be avoided or corrected early in the execution process. Escape hatches are particularly important in complex agentic workflows where multiple agents interact and make autonomous decisions that can have significant business or safety implications. 

    Design your architecture to enable continuous tracing and monitoring of agent interactions across the digital ecosystem. Implement governance agents that operate across different system components to identify and flag concerns before they escalate. Consider these essential patterns:
    
    - **Coordinator agents** that monitor task execution and escalate anomalies to human operators when predefined thresholds are exceeded or unusual patterns are detected.
    - **Human-in-the-loop checkpoints** where agents pause execution for validation before proceeding with high-risk or high-impact actions.

    Build interception points at all critical junctures in the execution path, including routing decisions, resource allocation choices, and external system integrations. This multi-layered approach provides multiple opportunities to prevent unintended consequences while maintaining system efficiency.

- **Make technical decisions about the AI system auditable.** Ensure that decisions made about AI technologies are recorded and auditable. You might include the record of these decisions in a larger audit trail for the workload, but ensure that necessary details are included to meet governance and compliance standards and requirements. Decisions for AI technologies can include:
    - Model selections
    - Model updates
    - Algorithm adjustments and hyper-parameters
    - Changes to your data processing design
 
### Tools

 Integrate tools for content safety and data traceability, like [Microsoft Purview](/purview/purview). [Azure AI Content Safety](/azure/ai-services/content-safety/overview) APIs can be called from your testing to facilitate content safety testing.

 Azure AI Foundry provides metrics that evaluate the behavior of the model. For more information, see [Evaluation and monitoring metrics for generative AI](/azure/ai-studio/concepts/evaluation-metrics-built-in).

 For training models, see the [metrics that Azure Machine Learning provides](/azure/machine-learning/component-reference/evaluate-model#metrics).

## Inspect incoming and outgoing data

Prompt injection attacks, such as jailbreaking, are a common concern for AI workloads. In this case, some users might attempt to misuse the model for unintended purposes. To help ensure safety, **inspect data to prevent attacks and filter out inappropriate content**. Apply this analysis to both the user's input and the system's responses to help ensure thorough content moderation in incoming flows and outgoing flows.

In some cases, you need to make multiple model invocations, such as through Azure OpenAI in Foundry Models, to serve a single client request. In these scenarios, applying content safety checks to every invocation can be costly and unnecessary. Consider **centralizing that work in the architecture while keeping security as a server-side responsibility**. Suppose an architecture has a gateway in front of the model inference endpoint to offload specific back-end capabilities. You can design that gateway to handle content safety checks for requests and responses that the back end might not support natively. Although a gateway is a common solution, an orchestration layer can handle these tasks effectively in simpler architectures. In both cases, you can selectively apply these checks when needed, which optimizes performance and cost.

**Inspections should be multimodal** and cover various formats. When you use multimodal inputs, such as images, it's important to analyze them for hidden messages that might be harmful or violent. These messages might not be immediately visible, so they require careful inspection. Use tools like Content Safety APIs for this purpose.

To help enforce privacy and data security policies, inspect user data and grounding data for compliance with privacy regulations. Make sure that the data is sanitized or filtered as it flows through the system. For example, data from prior customer support conversations can serve as grounding data. This data should be sanitized before reuse.

>[!IMPORTANT]
> **Content safety validation is essential for all AI scenarios**, regardless of architecture complexity. Implement content safety checks using services like [Azure AI Content Safety](/azure/ai-services/content-safety/overview) to evaluate both requests and responses before they reach or leave your AI models. 

## Handle user data appropriately

Responsible practices involve careful handling of user data management. This management includes knowing when to use data and when to avoid relying on user data.

- **Practice inference without sharing user data.** To securely share user data with other organizations for insights, **use a clearinghouse model**. In this scenario, organizations provide data to a trusted partner that trains the model by using the aggregated data. Then all institutions can use this model and share insights without exposing individual datasets. The goal is to use the model's inference capabilities without sharing detailed training data.

- **Promote diversity and inclusivity.** When user data is necessary, use a diverse range of data, including underrepresented genres and creators, to mitigate fairness-related harms. Implement features that encourage users to explore new and varied content. Monitor usage on an ongoing basis and adjust recommendations to avoid overrepresenting any single content type.

- **Respect the RTBF.** Avoid using user data when possible. Help ensure compliance with the RTBF by having the necessary measures in place to make sure that user data is deleted diligently.

    To help ensure compliance, there might be requests to remove user data from the system. For smaller models, you can remove user data by retraining the model by using data that excludes personal information. For larger models, which can consist of several smaller, independently trained models, the process is more complex and the cost and effort is significant. Seek legal and ethical guidance about handling these situations and make sure to include the guidance in your [policies for responsible AI](#develop-policies-for-responsible-ai).

- **Retain data responsibly.** When data deletion isn't possible, **obtain explicit user consent for data collection** and provide clear privacy policies. **Collect and retain data only when absolutely necessary.** Have operations in place to remove data aggressively when it's no longer needed. For example, clear chat history as soon as practical, and anonymize sensitive data before retention. Use advanced encryption methods for this data at rest.

- **Support explainability.** Trace decisions in the system to support explainability requirements. Develop clear explanations of how recommendation algorithms work. Provide users with insights into why specific content is recommended to them. The goal is to ensure that AI workloads and their results are transparent and justifiable by detailing how they make decisions, what data they use, and how models were trained.

- **Encrypt user data.** Input data must be encrypted at every stage of the data processing pipeline from the moment that the user enters data. These stages include data as it moves from one point to another, data that's stored, and data that's inferenced, if necessary. Balance security and functionality and aim to **keep data private throughout its life cycle**.

- **Provide robust access controls.** Several types of identities can potentially access user data. Implement role-based access control for both the control plane and data plane so that it covers user and system-to-system communication.

    Maintain proper user segmentation to protect privacy. For example, Microsoft 365 Copilot can search and provide answers based on a user's specific documents and emails, while ensuring that only content relevant to that user is accessed.

- **Reduce surface area.** A fundamental strategy of the Well-Architected Framework Security pillar is to minimize the attack surface and harden resources. You should apply this strategy to standard endpoint security practices by tightly controlling API endpoints, exposing only essential data, and avoiding extraneous information in responses. Balance the design choice between flexibility and control.

    Make sure that there aren't any anonymous endpoints. In general, avoid giving users more control than necessary. In most scenarios, users don't need to adjust hyperparameters except in experimental environments. For typical use cases, such as interacting with a virtual agent, users should only control essential aspects to help ensure security by limiting unnecessary control.

For more information, see [Application design for AI workloads on Azure](./application-design.md).

## Next step

> [!div class="nextstepaction"]
> [Workload team personas for AI workloads](./personas.md)
