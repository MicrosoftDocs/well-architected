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

TODO

## Anurag's (SME & area co-lead) seed material

TODO

## Jose's (Azure Patterns & Practices engineering) seed material

TODO

## Chad's (Azure Patterns & Practices engineering) seed material

TODO
