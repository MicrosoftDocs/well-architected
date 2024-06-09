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
  
#### MLOps for AI Workloads on Azure  
  
**MLOps** (Machine Learning Operations) is a crucial component for managing and scaling machine learning workflows in production environments. Azure provides a robust ecosystem that supports various stages of the ML lifecycle, from development to deployment and monitoring. Here are some thoughts on key aspects:  

### Developing and Training Models  
- **Model Training Environments**: Set up scalable compute environments with Azure ML Compute.  
- **Data Ingestion**: Utilize Azure Data Factory for efficient data ingestion and preprocessing.  
- **Model Training**: Use Azure ML SDK to train models in a reproducible manner.  
- **Notebook Integration**: Develop models interactively using Azure ML Notebooks.
  
- ### Deployment, Scalibility and Flexibility
-  Azure Machine Learning Service offers scalable compute options, including GPU and CPU clusters, which are essential for handling large datasets and complex models. The flexibility to choose different compute resources ensures that you can optimize costs and performance based on your specific needs.
- **Managed Online Endpoints and Batch Endpoints**: Deploy models using AML online and batch endpoints for scalable inference.  
- **Azure Kubernetes Service (AKS)**: Deploy models using AKS for scalable inference.  
- **Azure App Service**: Use Azure App Service for easy and scalable deployment.  
- **CI/CD Pipelines**: Implement CI/CD pipelines with Azure DevOps for continuous deployment.  
- **Model Versioning**: Use Azure ML Model Registry for version control of models.   
  
- ### Integration, Automation and Operation
- Azure's integration with other services like Azure DevOps, Data Factory, and Azure Kubernetes Service (AKS) enhances the automation capabilities of MLOps. Automated CI/CD pipelines help in maintaining consistent model updates and deployments, reducing manual intervention and potential errors.
- **A/B Testing**: Conduct A/B testing to compare model versions.  
- **Shadow Deployment**: Use shadow deployments to test models without affecting production.  
- **Canary Releases**: Implement canary releases to gradually roll out new models.  
- **Performance Monitoring**: Monitor model performance in real-time using Azure Monitor.    
  
- ### Security, Compliance and governance
- With built-in security features such as Role-Based Access Control (RBAC), Azure Active Directory (AAD), and encryption, Azure ensures that ML workloads comply with various industry standards and regulations. This is particularly important for industries like healthcare and finance, where data security is paramount.
- **Data Encryption**: Ensure data is encrypted at rest and in transit using Azure Key Vault.  
- **Data Anonymization**: Implement data anonymization techniques to protect sensitive information.  
- **Compliance**: Adhere to compliance standards like GDPR and HIPAA.  
- **Secure Data Storage**: Use Azure Blob Storage with secure access policies for data storage.    
  
- ### Monitoring and Management
- Azure Monitor and Application Insights provide comprehensive tools for monitoring model performance and system health. These tools enable proactive issue detection and resolution, ensuring that ML models perform optimally in production environments.
- **A/B Testing**: Conduct A/B testing to compare model versions.  
- **AML Model Monitoring**: USe AML model data collection and model monitoring for data drift, model drift and other customer metrics.
- **Canary Releases**: Implement canary releases to gradually roll out new models.  
- **Performance Monitoring**: Monitor model performance in real-time using Azure Monitor.  
   
  
#### LLMOps for AI Workloads on Azure  
  
**LLMOps** (Large Language Model Operations) is an emerging field that focuses on the operationalization of large language models, such as GPT-3 and BERT, which require specialized handling due to their size and complexity. Azure is well-positioned to support LLMOps through its advanced infrastructure and services. Here are some thoughts:  
  
- **Pre-trained Models and Transfer Learning**: Azure provides access to pre-trained large language models, which can be fine-tuned for specific tasks using transfer learning. This approach significantly reduces the time and resources needed to develop high-performing models for niche applications.  
  
- **Compute Resources**: Large language models are resource-intensive, requiring powerful GPUs and TPUs for efficient training and inference. Azure's scalable compute options, including Azure Machine Learning Compute and Azure Kubernetes Service (AKS), make it feasible to handle these requirements.  
  
- **Data Management**: Managing the vast amounts of data needed for training and fine-tuning large language models is a significant challenge. Azure's data services, such as Azure Data Lake Storage and Azure Data Factory, provide robust solutions for data ingestion, storage, and preprocessing.  
  
- **Operational Efficiency**: Azure ML Pipelines facilitate the automation of complex workflows involved in fine-tuning and deploying large language models. This streamlining of processes ensures that models can be updated and deployed rapidly, keeping up with changing requirements and new data.  
  
- **Ethical Considerations**: Large language models come with ethical and operational challenges, such as bias and fairness. Azure provides tools and frameworks to help address these issues, ensuring that models are not only performant but also responsible and ethical.  
  
### Fine-Tuning of Models  
  
#### MLOps for Fine-Tuning of Models  
  
- **Azure Machine Learning Service**: Utilize Azure ML for managing and orchestrating the fine-tuning process, offering scalable compute and integrated development environments.  
- **Hyperparameter Tuning**: Use Azure's HyperDrive for automated optimization of hyperparameters to enhance model performance.  
- **Automated Machine Learning**: Leverage Azure AutoML to automate model selection, feature selection, and hyperparameter tuning.  
- **Custom Scripts**: Implement specialized fine-tuning tasks using custom scripts within Azure ML pipelines for greater flexibility.  
  
#### LLMOps for Fine-Tuning of Models  
  
- **Pre-trained Models**: Utilize Azure's pre-trained large language models as a starting point for domain-specific fine-tuning.  
- **Transfer Learning**: Implement transfer learning techniques to adapt pre-trained models to specific tasks by training on new datasets.  
- **Azure ML Pipelines**: Use Azure ML Pipelines to automate and streamline the fine-tuning workflow from data preparation to model evaluation.  
 


## Aleksandra's (SME & area co-lead) seed material

TODO

## Anurag's (SME & area co-lead) seed material

TODO

## Jose's (Azure Patterns & Practices engineering) seed material

TODO

## Chad's (Azure Patterns & Practices engineering) seed material

TODO
