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
 
#### Developing and Training Models  
- **Model Training Environments**: Set up scalable compute environments with Azure ML Compute.  
- **Data Ingestion**: Utilize Azure Data Factory for efficient data ingestion and preprocessing.  
- **Model Training**: Use Azure ML SDK to train models in a reproducible manner.  
- **Notebook Integration**: Develop models interactively using Azure ML Notebooks.  
  
#### Secure Access for End Users  
- **Role-Based Access Control (RBAC)**: Implement RBAC to manage user permissions.  
- **Azure Active Directory (AAD)**: Integrate with AAD for secure authentication and authorization.  
- **Private Endpoints**: Use Azure Private Link to provide secure access to services.  
- **Access Policies**: Define and enforce access policies for end users and services.  
  
#### Verification of Training System  
- **Model Validation**: Use Azure ML validation datasets to verify model performance.  
- **Cross-Validation**: Implement cross-validation techniques to ensure model robustness.  
- **Audit Trails**: Maintain detailed logs of training sessions for audit purposes.  
- **Performance Metrics**: Monitor training metrics and logs via Azure Monitor.  
  
#### Secure Data Handling  
- **Data Encryption**: Ensure data is encrypted at rest and in transit using Azure Key Vault.  
- **Data Anonymization**: Implement data anonymization techniques to protect sensitive information.  
- **Compliance**: Adhere to compliance standards like GDPR and HIPAA.  
- **Secure Data Storage**: Use Azure Blob Storage with secure access policies for data storage.  
  
#### Deployment of Newly Trained Models  
- **Azure Kubernetes Service (AKS)**: Deploy models using AKS for scalable inference.  
- **Azure App Service**: Use Azure App Service for easy and scalable deployment.  
- **CI/CD Pipelines**: Implement CI/CD pipelines with Azure DevOps for continuous deployment.  
- **Model Versioning**: Use Azure ML Model Registry for version control of models.  
  
#### Operational Consideration around Verifying and Testing a Model  
- **A/B Testing**: Conduct A/B testing to compare model versions.  
- **Shadow Deployment**: Use shadow deployments to test models without affecting production.  
- **Canary Releases**: Implement canary releases to gradually roll out new models.  
- **Performance Monitoring**: Monitor model performance in real-time using Azure Monitor.  
  
#### Prompt Evaluation  
- **Human-in-the-Loop**: Incorporate human feedback for evaluating model prompts.  
- **Automated Testing**: Use Azure ML for automated prompt evaluation and testing.  
- **Feedback Loops**: Set up feedback loops to continuously improve model prompts.  
- **Prompt Benchmarking**: Benchmark prompts against known datasets for consistency.  
  
#### Monitoring (Required)  
- **Azure Monitor**: Use Azure Monitor for end-to-end monitoring of ML workloads.  
- **Application Insights**: Integrate with Application Insights for detailed telemetry.  
- **Alerts and Notifications**: Set up alerts for critical events and anomalies.  
- **Log Analytics**: Use Azure Log Analytics for advanced log analysis and insights.  
  

## Aleksandra's (SME & area co-lead) seed material

TODO

## Anurag's (SME & area co-lead) seed material

TODO

## Jose's (Azure Patterns & Practices engineering) seed material

TODO

## Chad's (Azure Patterns & Practices engineering) seed material

TODO
