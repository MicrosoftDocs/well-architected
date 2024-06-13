---
title: MLOps and LLMOps for AI workloads on Azure
description: MLOps and LLMOps for AI workloads on Azure for running AI workloads.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-ai
---

# MLOps and LLMOps for AI workloads on Azure

- Fine Tunining of models
- Developing and training models
- Secure access for end users
- Verification of training system

- Secure data handling
- Deployment of newly trained models

- Operational consideration around verifying and testing a model
- Prompt Evaluation
- Monitoring (required)

## Kapil's (SME & area co-lead) seed material

TODO

## Aleksandra's (SME & area co-lead) seed material

TODO

## Anurag's (SME & area co-lead) seed material

1. **Why are operational processes important in your AI workload? (What would happen if you didn't have a process?)**
    - Operational processes are crucial in AI workloads to ensure consistency, reliability, safety, security, and efficiency.
    - Without established processes, AI projects could suffer from errors, unsafe content, inconsistencies, and inefficiencies, leading to unreliable outcomes, security and compliance violations, increased costs, and delayed project timelines.

2. **Why do we need LLMOps?**
    - The goal of LLMOps is to ensure continuous quality, reliability, security, and ethical standards of generative AI models and their applications in production with enhanced efficiency and automation.

3. **How are those processes related to normal processes you'd already have in your workload? (DevOps)**
    - LLMOps extends the principles of DevOps by incorporating the unique needs of LLM models, such as model discovery, fine-tuning, deployment, monitoring, and upgrading.
    - While DevOps focuses on software development and IT operations, LLMOps includes additional layers of model discovery and tuning, data preparation, and ethical considerations, ensuring that AI systems are robust, secure, and aligned with business goals. Some of the specific steps in LLMOps include:
      1. Data Prep
          - Access data
          - Cleanse data
          - Transform data
      2. Discover and Tune
          - Discover LLM Model
          - Prompt Engineering
          - Fine-Tuning
          - Hyperparameter Tuning
          - Test Model
          - Repeatable pipelines
      3. Deployment
          - Validate model
          - Package Model
          - Deploy Model
          - Inferencing
      4. Monitor
          - Monitor Data Drift
          - Monitor Model Metrics
          - Monitor Infra metrics
          - A/B Testing

4. **Are there any processes already built for AI components in workloads? Summarize and link off to MLOps and FMOps.**
    Target Audience:
    - MLOps: ML Engineers, Data Scientists, Operational Staff.
    - LLMOps: ML Engineers, Application Developers, Operational Staff, Data Engineers.
    Deliverables:
    - MLOps: Model, data, environments, features.
    - LLMOps: LLM model, agents, plugins, prompts, chains, APIs.
    Model Selection:
    - MLOps: Select a model version or use automated ML (AutoML)
    - LLMOps: Select a pretrained foundation model adapted to use case via model cards, benchmarks, evaluations.
    Model Training:
    - MLOps: Train against selected ML algorithms.
    - LLMOps: Fine-tune existing foundation model, use RAG pattern, ground against own data, perform prompt engineering.
    Model Validation and Metrics:
    - MLOps: Validate using metrics like Accuracy, AUC, F1 scores.
    - LLMOps: Use human feedback and/or other LLMs for metrics like quality (accuracy, similarity), harm (bias, toxicity), correctness (groundedness), cost (tokens per request), latency (response time), perplexity. Examples: BLEU, ROUGE, MMLU, Perplexity, ARC, HellaSwag, TruthfulQA.
    Model Deployment:
    - MLOps: Package and deploy via automated processes and pipelines.
    - LLMOps: Deployments include components like vector databases, incorporating LLM lifecycle techniques.
    Model Monitoring:
    - MLOps: Monitor model performance and data drift.
    - LLMOps: Monitor prompts and completions, content filtering for harmful content, prompt injection attacks, jailbreaks, performance, and data and model drift.

5. **When do you know if your workload is complex enough to feel like you need to move from just DevOps into something formally for AI features? Is there a tipping point?**
    Determining when your workload is complex enough to move from DevOps to a more formal AI-focused operations approach like MLOps or LLMOps can be guided by several factors and signs that indicate increased complexity and the need for specialized practices. Here are key indicators and the tipping points to consider:
    Indicators for Transitioning from DevOps to MLOps/LLMOps
    1. Data Volume and Complexity:
        - Indicator: You have large volumes of data, multiple data sources, and complex data pipelines.
        - Tipping Point: When managing, processing, and maintaining data quality become challenging and require more robust tools and processes.
    2. Model Lifecycle Management:
        - Indicator: You are developing and deploying multiple machine learning models.
        - Tipping Point: When tracking versions, retraining, and updating models become cumbersome, indicating the need for automated model lifecycle management.
    3. Performance Monitoring:
        - Indicator: You need to monitor model performance and accuracy in real-time.
        - Tipping Point: When manual monitoring is insufficient and automated performance metrics, alerts, and model retraining are needed to maintain model efficacy.
    4. Scalability and Deployment:
        - Indicator: Your AI models need to scale across different environments or platforms.
        - Tipping Point: When deploying models across various environments manually is error-prone and time-consuming, indicating the need for automated, scalable deployment pipelines.
    5. Regulatory and Compliance Requirements:
        - Indicator: You must comply with strict regulatory requirements for data privacy and model transparency.
        - Tipping Point: When compliance and audit requirements necessitate detailed tracking and documentation of data lineage, model training processes, and decision-making.
    6. Model Performance and Drift:
        - Indicator: Your models' performance degrades over time, and you experience concept drift.
        - Tipping Point: When the need for continuous monitoring, evaluation, and retraining of models becomes apparent to maintain accuracy and relevance.
    7. Collaboration and Reproducibility:
        - Indicator: Multiple teams collaborate on AI model development and deployment.
        - Tipping Point: When ensuring consistency, reproducibility, and collaboration between data scientists, engineers, and operations teams becomes challenging without standardized processes.
    8. Complex Workflows and Pipelines:
        - Indicator: Your workflows involve complex data preprocessing, feature engineering, model training, and deployment stages.
        - Tipping Point: When the need for orchestrating and automating these workflows becomes critical to streamline operations and reduce manual intervention.
    Tipping Points for Transitioning to LLMOps
    1. Integration of LLMs:
        - Indicator: You are using or planning to use LLMs or SLMs like GPT, Llama, etc.
        - Tipping Point: When integrating, fine-tuning, and deploying LLMs require specialized tools and practices beyond traditional MLOps capabilities. For example, orchestration between LLMs, vector indexes, prompts, and a block of code.
    2. Custom Prompt Engineering and Fine-Tuning:
        - Indicator: You need to fine-tune LLMs and design custom prompts for specific applications.
        - Tipping Point: When managing prompt engineering, fine-tuning, and evaluating responses for quality and safety become critical.
    3. Advanced NLP Applications:
        - Indicator: Your applications rely heavily on natural language understanding and generation.
        - Tipping Point: When the complexity of NLP tasks and the need for robust evaluation metrics like BLEU, ROUGE, and human feedback necessitate specialized operational practices.

6. **Are there any downsides to processes like these, such as impact on workload cost, reliability, or security?**
    Implementing processes like LLMOps can bring numerous benefits, but it also comes with potential downsides that need to be carefully managed. Here are some key considerations:
    Downsides of LLMOps
    1. Increased Costs:
        - Infrastructure/API Costs: Running and maintaining LLMs may require significant computational resources or API costs, which can lead to high cloud or hardware costs.
        - Operational Costs: Implementing LLMOps involves additional tools, processes, and possibly specialized personnel, which can increase operational expenses.
        - Model Training Costs: Fine-tuning and retraining large models can be computationally expensive, consuming considerable amounts of time and resources.
    2. Complexity and Overhead:
        - Process Complexity: LLMOps can add layers of complexity with specialized workflows, monitoring, and maintenance practices that need to be managed effectively.
        - Skill Requirements: Requires skilled personnel who are proficient in both AI and operations, which can be hard to find and hire.
    3. Reliability Challenges:
        - Model Stability: Large models can be sensitive to changes and might require frequent retraining to maintain performance, potentially impacting reliability.
        - Dependency on Upstream Models: Relying on pretrained models means that any updates or changes to these models by the original providers can affect your applications.
    4. Security and Compliance:
        - Data Privacy: Handling large volumes of data, especially personal data, requires strict adherence to privacy regulations, which can be challenging to maintain.
        - Model Security: Large models can be vulnerable to attacks such as prompt injection attacks and jailbreaks. Ensuring robust security measures are in place is crucial.
        - Compliance Overhead: Meeting compliance requirements for AI, such as transparency, fairness, and accountability, can add additional operational overhead.
    5. Scalability Issues:
        - Resource Allocation: Ensuring that resources are efficiently allocated for model training, inference, and scaling can be complex and might require sophisticated orchestration.
        - Latency: Serving large models in real-time applications can introduce latency, affecting the user experience.
    6. Maintenance and Monitoring:
        - Continuous Monitoring: Continuous monitoring for performance, drift, and security issues requires significant effort and advanced tooling.
        - Regular Updates: Models and their associated components (data, prompts, etc.) need regular updates and maintenance to remain effective and secure.
    7. Ethical and Societal Implications:
        - Bias and Fairness: Ensuring that the models do not propagate biases or produce harmful content requires rigorous testing and ongoing monitoring.
        - Transparency: Providing transparency into how models make decisions can be challenging, especially with complex LLMs.

Mitigating the Downsides
1. Cost Management:
    - Implement telemetry with APIM along with cost optimization strategies at the model, application, and infrastructure layer.
    - Monitor and control resource usage with cloud cost management tools.
2. Simplifying Processes:
    - Use automation to reduce the complexity of operational workflows.
    - Invest in training and upskilling existing staff to bridge the skill gap.
3. Enhancing Reliability:
    - Implement robust CI/CD pipelines for model updates.
    - Use version control and rollback mechanisms to handle model changes smoothly.
4. Strengthening Security and Compliance:
    - Apply strong encryption and access controls to protect data.
    - Regularly audit and update security practices to counter emerging threats.
5. Ensuring Scalability:
    - Use scalable cloud infrastructure and auto-scaling capabilities on Azure APIM.
    - If needed, optimize inference strategies, such as edge or local deployment, to reduce latency.
6. Maintaining Ethical Standards:
    - Establish clear guidelines and frameworks for ethical AI usage.
    - Regularly review and update models to ensure fairness and mitigate biases adhering to Microsoft's RAI principles.

By being aware of these potential downsides and proactively addressing them, you can effectively implement LLMOps while minimizing negative impacts on workload cost, reliability, and security.



## Jose's (Azure Patterns & Practices engineering) seed material

TODO

## Chad's (Azure Patterns & Practices engineering) seed material

TODO
