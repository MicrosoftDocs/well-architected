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
  
### MLOps for AI Workloads on Azure  
  
**MLOps** (Machine Learning Operations) is a crucial component for managing and scaling machine learning workflows in production environments. Azure provides a robust ecosystem that supports various stages of the ML lifecycle, from development to deployment and monitoring. Here are some thoughts on key aspects:  

#### Developing and Training Models  
- **Model Training Environments**: Set up scalable compute environments with Azure ML Compute.  
- **Data Ingestion**: Utilize Azure Data Factory for efficient data ingestion and preprocessing.  
- **Model Training**: Use Azure ML SDK to train models in a reproducible manner.  
- **Notebook Integration**: Develop models interactively using Azure ML Notebooks.
  
#### Deployment, Scalibility and Flexibility
-  Azure Machine Learning Service offers scalable compute options, including GPU and CPU clusters, which are essential for handling large datasets and complex models. The flexibility to choose different compute resources ensures that you can optimize costs and performance based on your specific needs.
- **Managed Online Endpoints and Batch Endpoints**: Deploy models using AML online and batch endpoints for scalable inference.  
- **Azure Kubernetes Service (AKS)**: Deploy models using AKS for scalable inference.  
- **Azure App Service**: Use Azure App Service for easy and scalable deployment.  
- **CI/CD Pipelines**: Implement CI/CD pipelines with Azure DevOps for continuous deployment.  
- **Model Versioning**: Use Azure ML Model Registry for version control of models.   
  
#### Operational Efficiency and Testing
- Azure's integration with other services like Azure DevOps, Data Factory, and Azure Kubernetes Service (AKS) enhances the automation capabilities of MLOps. Automated CI/CD pipelines help in maintaining consistent model updates and deployments, reducing manual intervention and potential errors.
- **A/B Testing**: Conduct A/B testing to compare model versions.  
- **Shadow Deployment**: Use shadow deployments to test models without affecting production.  
- **Canary Releases**: Implement canary releases to gradually roll out new models.  
- **Performance Monitoring**: Monitor model performance in real-time using Azure Monitor.
- **A/B Testing**: Conduct A/B testing to compare model versions.  
- **AML Model Monitoring**: USe AML model data collection and model monitoring for data drift, model drift and other customer metrics.
- **Canary Releases**: Implement canary releases to gradually roll out new models.     
  
#### Security, Compliance and governance
- With built-in security features such as Role-Based Access Control (RBAC), Azure Active Directory (AAD), and encryption, Azure ensures that ML workloads comply with various industry standards and regulations. This is particularly important for industries like healthcare and finance, where data security is paramount.
- **Data Encryption**: Ensure data is encrypted at rest and in transit using Azure Key Vault.  
- **Data Anonymization**: Implement data anonymization techniques to protect sensitive information.  
- **Compliance**: Adhere to compliance standards like GDPR and HIPAA.  
- **Secure Data Storage**: Use Azure Blob Storage with secure access policies for data storage.    
  
#### Monitoring and Management
- Azure Monitor and Application Insights provide comprehensive tools for monitoring model performance and system health. These tools enable proactive issue detection and resolution, ensuring that ML models perform optimally in production environments.
- **Performance Monitoring**: Monitor model performance in real-time using Azure Monitor.  
   
  
### LLMOps for Large Language Models
  
Interestingly enough, the life cycle for LLMs is very similar to classical ML models as outlined above but we do not have to go through expensive model training because the LLMs are already pre-trained. However, we still have to consider discovering an LLM Model that fits use case, tune the prompts (i.e., prompt engineering or prompt tuning) and if necessary, fine-tune the models for domain specific grounding.
#### Using Azure AI Studio or Machine Learning for LLMOps 
- Ai studio or Azure machine learning prompt flow provides a comprehensive solution that simplifies the process of prototyping, experimenting and tuning the prompt engineering process. Below are some important features:
- Create executable flows that link LLMs, prompts, and Python tools.
- Debug, share, and iterate your flows with ease through team collaboration.
- Create prompt variants and evaluate their performance through large-scale testing.
- Deploy the prompt flow as real-time endpoint to integrate into the workflow.
- Azure AI studio and Machine Learning both provides advanced capabilities throughout the entire LLM lifecycle. This includes everything from data preparation to the discovery and tuning of foundational models, and their deployment. It also assists in the development and deployment of Prompt flows. Finally, it enables monitoring of the deployed model and Prompt flow endpoints for attributes such as groundedness, relevance, and coherence.
#### Data Preparation for LLMs
- The first step in the process is to access the data for LLMs similar to ML models. Azure AI Studio or Machine Learning provides seamless access to Onelake, Azure Data Lake Storage Gen2, Azure Blob Storage, Azure SQL Databases etc.
#### Model Discover
- One main advantage of LLMs is that we do not have to go through the expensive training process because they are already available models like GPT-4, Llama 2, Falcon etc. However, we still have to consider tuning the prompts (i.e., prompt engineering or prompt tuning) and if necessary, fine-tune the models for domain specific grounding.
- The model catalog is a hub for discovering various foundation models from Azure OpenAI Service, Llama 2, Falcon, Hugging Face and a diverse suite of open-source vision models for image classification, object detection, and image segmentation. These models are curated, tested thoroughly to easily deploy and integrate with the applications.
####Ethical Considerations
- Large language models come with ethical and operational challenges, such as bias and fairness. Azure provides tools and frameworks to help address these issues, ensuring that models are not only performant but also responsible and ethical.  
  
### LLM Fine-Tuning
Fine-tuning for large language models is a process where a pre-trained model is adapted to generate answers specific to a particular domain. Fine-tuning allows the model to grasp the nuances and context relevant to that domain, thus improving its performance. The following are the steps involved in fine-tuning:
- **Select a relevant dataset**: Choose a dataset that represents the specific domain or task you want the model to excel in, ensuring it has adequate quality and size for effective fine-tuning.
- **Adjust training parameters**: Modify parameters like learning rate, batch size, and the number of training epochs to optimize the fine-tuning process and prevent overfitting.
- **Evaluate and iterate**: Regularly assess the fine-tuned model's performance using validation data and make necessary adjustments to improve its accuracy and effectiveness in the target domain.
Azure Machine Learning supports advanced optimization and distributed computing technologies such as ONNX Runtime Training’s ORTModule ,DeepSpeed and LoRA to significantly accelerate the training process.




## Aleksandra's (SME & area co-lead) seed material

### Client in the insurance industry
#### Intro

##### -	Situation:
The client is a large company operating on the insurance domain that has a Microsoft competitor (AWS) already offering their services to the customer. Therefore, Microsoft first needed to win the customer’s trust to expand the Data & AI pipeline. After having presented a micro PoC in Prompt Flow to the customer’s CIO and his manager team, the CIO decided to acquire 100 hours of CSA support to give a go to the 1st ever PoC based on Generative AI. The CIO also appointed a small team of developers aiming to land their first GenAI PoC and learn more about Microsoft technology.

##### -	Use cases:
1.	The 1st use case presented by the customer’s business and IT departments is about building an AI assistant for creating a financial report resulting from a tailored analysis of a specific company.
The AI-generated report should include both the extraction of relevant indicators and the analyst's view on the company's financial performance and its positioning in the competitors' landscape.
2.	Following the successful implementation of the 1st use case prototype, the customer decided to present  a 2nd use case, dedicated to Log Analysis, for which another IT team was asked to join in. 
The capabilities expected from AI are therefore, the following: an Error Log Analyst that must classify logs, according to 3 pre-defined categories and if not present in any of the categories, suggest a new one. Additionally, the job of this AI copilot is to re-write error logs in a more user-friendly way in order to be more accessible for the business users once classified.
3.	While working on the 2nd use case and the evolution of the 1st use case (supported by Microsoft partner), the customer recognized the need for a well-architected and scalable upskilling journey for their IT teams, supervised by the company’s leadership. The most relevant domains of upskilling cited by the customer were identified as agentic and multiagent scenarios for GenAI. The Account team and the customer’s trusted CSA started more concrete discussions about how and when, also introducing PTU as a potential solution driver as the needs for generative AI inside the company are growing. 
#### Considerations 
Use case 1 – Report generation assistant:

-	Due to the customer’s relatively low commitment to generative AI and Microsoft products at the beginning of the journey, we could not count on reaching to a trusted Microsoft partner to accelerate the workload. For us it meant to keep a PoC in-house and implement it by the efforts of 
1) a CSA Data & AI (for solution backend) and a CSA App Innovation (for solution frontend) and 
2) a group of developers from the customer’s IT team appointed by the customer’s CIO. 
-	The absence both of a Microsoft partner and a background on generative AI / MLOps or LLMOps on the customer side resulted in the need of combining upskilling and solution development during meetings with the customer. Arguments such as embedding, tokenization, AI Search, RAG, Entity Extraction, Ingestion Pipeline, AI Studio, Prompt Flow, LLM Evaluation, Virtual Machines etc. were introduced  --> Operational excellence
-	As the prototype’s backend was implemented in Azure AI Studio (that at the time of implementation was still in preview), we tried to maximize our transparency about the product’s potential issues and shortcomings. Therefore, in some situations, s.a. developing Evaluation flows in Prompt Flow or trying different compute configurations with/and without custom package maintenance, the meetings with the customer evolved into a collective learning (also benefitting the Microsoft CSAs involved). Moreover, to provide the maximum support for the solution development, we stayed in a continuous contact with the customer, responding to technical questions outside of agreed meetings as well. --> Reliability, Operational Excellence
-	As the customer was new to LLMOps, first prompts that were developed were rather long and cumbersome, which resulted in an elevated cost in terms of token consumption and time to response. As the customer became more skilled on prompt engineering, also by following our recommendations, the prompt size and the overall number of LLM calls were reduced to the more optimal one, leading the flow to execute in  ca. 1 minute instead of 2+ minutes.  Cost optimization
In addition to the cost optimization, by leveraging prompt engineering and the collaboration with SMEs on the customer side, we reached improvements in terms of LLM performance on requested topics. --> Performance
-	 In order to ensure the solution’s security, all customer’s cloud policies were respected and approved by GOSP, the provider managing the customer’s cloud infrastructure. For instance, OpenAI models were only deployed in Europe and the majority of resources even stayed in West Europe as region. To support this compliance model, we have organized periodic sync-s with GOSP and informed them about all necessary configurations. --> Security
-	Once the PoC was concluded, it was presented to the customer’s leadership and business teams, as well as empirically tested. Upon positive results of solution testing, the customer decided to engage a Microsoft partner to bring the solution prototype into production by expanding it, keeping Microsoft CSAs as trusted advisors. At the moment we are working on the prototype evolution, which includes improving the document chunking strategy, processing of slides with multimodal LLM and drafting a system of custom evaluation for every report indicator getting extracted from documents and slides provided. Moreover, we are currently evaluating different LLM options, s.a. GPTo, GPT-4 turbo and so on, that can be put into PTUs for guaranteeing the flow execution in a time frame that is acceptable for the end user.

Use case 2 – Error log analysis assistant:

-	For the Log Analysis use case, we have started from a more beneficial position in terms of customer commitment and trust. This meant that the customer already had a collaboration model in mind – from PoC to Microsoft and partner-driven project – and was more autonomous in working with Microsoft products thanks to a previously carried out brief upskilling.
-	Despite some of the core team from the use case 1 being present in the development team for the use case 2, we still opted for a brief upskilling on Generative AI, AI Studio, Prompt Flow and Assistant API to bring everyone on the same page. After a ½ day of such tailored upskilling in which the 1st results of log processing on sample data in Azure OpenAI playground were shown, we won more customer trust and motivation to kick off the project the week after. The customer also returned home with concrete ideas in mind.
-	The customer showed proactivity in designing the PoC architecture and is currently putting weekly effort in developing a custom Prompt Flow supporting the log processing. In parallel, at the managerial level, we have started PTU discussions to be able to scale in the future, once the PoC is concluded.

Upskilling journey:

-	The customer was recommended to approach upskilling granularly, therefore progressively understanding the needs of automation for every department involved in the upskilling program and their respective knowledge bases that could power the eventual RAG chatbots. The customer was advised to consider Microsoft-driven AI orchestration frameworks, such as Semantic Kernel and Autogen to support new scenarios. The customer is currently gathering business needs to design a tailored upskilling journey resulting in new projects.
#### Recommendations (Ways to achieve the best outcome)
-	Generative AI is not a guarantee of success, it is rather probabilistic and subject to variations in terms of response quality and overall stability. One should be transparent with the customer about this aspect and explore ways of making LLMs to act more deterministically: such as plugins, prompt engineering, application logics, well prepared and structured KBs, eventual human interventions.
-	A PoC on the generative AI domain needs to be well parametrized and designed in advance in order to build realistic expectations. Without an architectural diagram and supportive slides, the customer might mistake spoken or written words for something else that is not included in the PoC or project perimeter.
-	A PoC on the generative AI domain should be at least supervised by multiple solution areas in order to respect desired application and infrastructure standards. Therefore, generative AI is not only a Data & AI topic, it should rather be addressed in a cross-solution manner.
-	There is no “best” technology or orchestration framework for LLMOps ad hoc – there are options that should be explored and evaluated based on customer needs.
-	A PoC on the generative AI domain, once concluded, should evolve into a project assisted by a Microsoft partner while Microsoft CSAs should remain in the advisory role. It is not sustainable to continue running a full-scale project while only relying on the CSA support if the skills required for the project success are not fully present in the customer’s development teams.
-	When presenting the results of a PoC on the generative AI domain, it is crucial to talk about its evolution to provide a vision for the customer of what can be possible, emphasizing the need of partner support.  A useful way of creating a powerful vision is to prepare a high-level architecture design chart.
-	Upskilling materials are crucial to provide a theoretical foundation for the customer in order to be “intellectually” onboarded to the LLMOps area and gain some comfort in using the technology portfolio. If possible, hands-on tutorials (from Microsoft learning paths) or sample materials (sample prompt flows, Jupiter notebooks etc.) should be recommended to the customer in order to make conscious choices in using the technologies.
-	Tendentially, clients have more than 1 generative AI use case in their departments but only invest in the subsequent use cases after observing the success / the first positive outcomes of the first one. For this reason, it is important to demonstrate the potential of GenAI while also tuning the customer expectations in terms of effort / costs / project duration that must be agreed upon with the STU and ATU (OneMicrosoft approach).
### Client in public services
#### Intro
-	Situation:
Customer is a very fragmented, not very digitalized public service provider operating across the whole Italy that tendentially faces a low-education user base on their webpages. The customer reached out upon upskilling support for improving the performance of a RAG chatbot already operating customer’s information website. Later in the timeline, the customer discovered the need to scale up their AI-driven operations and centralize their governance process under a unified “GenAI platform” for the whole company. Microsoft CSAs from Data & AI / App Innovation / Infra solution areas were engaged to support this workload.
-	Use cases:
Every application on the generative AI domain should be governed by the GenAI platform that needs to be configured in terms of resource / cost & usage monitoring policies. The customer requested the possibility to use multiple LLMs (not only GPT) on the same platform while also being able to accelerate the development of new applications with predisposed APIs (s.a. institutional RAG service, institutional Entity Extraction service, institutional Completion service and so one). Moreover, at the infrastructure level the customer expects having smart load balancing and charge back logics applied to LLMs allowed on the platform.
#### Considerations
-	No similar platform building and configuration experience was present in the Italian subsidiary at the time of project kickoff.
-	The customer had at least 5 different projects that were considered eligible for onboarding on the GenAI platform. Every project was guided by a separate team that had different project requirements and expectations from the platform, which made the communication very complex.
-	The customer had very agile internal dynamics, s.a. changes in the leadership teams, re-definition of responsibility zones and so on, which slowed down the decision-making processes.
#### Recommendations
-	Supporting such an ambitious project requires constant time and organization efforts, as well as a well-articulated brainstorming & creativity capacities. In a situation of repeating intellectual challenges and communication efforts it is important to maintain a healthy boundary between what can and cannot be impacted on the customer’s side because no matter how well prepared the CSAs are, the customer might be unable to follow their advice due to internal company dynamics.
-	Continuous improvement and revisions of the platform are crucial to guarantee its success as the solution naturally evolves with time and as the customer better consolidates their ideas. It is important to have periodic checks up with the customer and their trusted partners to monitor changes and intervene, if necessary.


## Mauro's (SME & area co-lead) seed material

### Client in the insurance industry
#### Intro
-	Situation:
Ferrero Group is one of the world’s largest sweet-packaged food companies, with over 35 iconic brands sold in more than 170 countries. The most famous words associated with Ferrero Group include Nutella, Kinder, Tic Tac, and Ferrero Rocher. With more than 47,000 employees, the Ferrero Group’s family culture, now in its third generation, is based on dedication to quality and excellence, heritage, and a commitment to the planet's health.
Our primary contact, Christophe, is a smart Enterprise Architect and a direct report of the CIO; part of his job is to collect the internal opportunities to attach AI to their challenges, formalize the requirements, analyze, and finally choose their partners’ proposals.
By the beginning of FY24, our CSAM approached him to start discussing an evolution of the traditional delivery of the Unified Support contract, offering a deeper understanding of the opportunities offered by our AI services.
-	Use cases:
When I met Christophe and started an open discussion with him, he explained to me the two main challenges he was facing:
1.	He lacked the necessary awareness to objectively analyze the three AI projects that he had approved with some partners of ours.
2.	His internal business stakeholders had always been rather cautious about the actual effectiveness of such projects, influenced by the multitude of technologies available today on the AI market, possible security implications, and other concerns..

Due to these reasons, a classical program based on a series of dedicated technical meetings as we’re used to doing with many other (more mature and committed) Customers could not be proposed at the moment. However, we agreed on proceeding with a double-faced approach:
1.	One-on-one work between Christophe and me to analyze the three current projects, starting from the high level solution proposed by each partner followed by the architecture, technologies and frameworks chosen for the implementation. This allowed to identify, with a top down approach, the skills that Christophe should have gained to become more aware of the ongoing project gaps, risks and current status: in a serie of dedicated sessions made of theory, architecture and hands-on tasks, we implemented a sort of “training on the job” approach which made him more confident of investing in something that could be immediately put in practice, as he did.
2.	Creation of an internal culture that explained, at a higher level, the opportunities and investments that the whole Company was doing in the AI area, with the goal of creating the awareness and confidence to stimulate more ideas. 

#### Recommendations (Ways to achieve the best outcome)
As explained, we had to implement a dedicated approach; however, I think that this kind of situation is not uncommon nowadays. This is how we implemented it:
-	On his side, the Customer engaged the internal HR division to identify the right internal department stakholders to attend a consistent workshop that exaplained the most recent technologies and allowed people to share any concerns and ask questions collected so far. 
-	On my side, I worked to build a customized workshop agenda and contents with the proper balance of theory, architecture and practice. It was a big investment and a challenge, being aware that we created very high expection and asked a consistent investment to dedicate fifty managers to spend four hours with us: a second opportunity would have been quite impossible in the near future.

I delivered this workshop on May 30th, 2024. Not a single person left the meeting before the end, a lot of questions were asked and several enthusiastic feedbacks where shared in the meeting chat and in the survey that followed the event. Furthermore, despite the whole meeting was recorded, a second session of this event was required to be re-delivered for other colleagues on June 20th, 2024. I’m reporting these feedbacks just to confirm that this investment was considered helpful by the Customer in this specific situation. So I’m happy to share this experience with the team and more practically the workshop slides and demos, plus the material associated to the more technical sessions.


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

## Mauro's (SME & area co-lead) seed material


## Jose's (Azure Patterns & Practices engineering) seed material

TODO

## Chad's (Azure Patterns & Practices engineering) seed material

TODO
