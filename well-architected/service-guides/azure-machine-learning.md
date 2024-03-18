---
title: Azure Well-Architected Framework perspective on Azure Machine Learning
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure Machine Learning.
author: robbagby
ms.author: robbag
ms.date: 03/11/2024
ms.topic: conceptual
products:
  - azure-machine-learning
categories:
  - ai-machine-learning
---

# Azure Well-Architected Framework perspective on Azure Machine Learning

Azure Machine Learning is a managed cloud service that you can use to train, deploy, and manage machine learning models. There are a wide range of choices and configurations for both training and deploying models, including compute SKUs and configurations. You can deploy Machine learning models to Machine Learning compute or to other Azure services such as Azure Kubernetes Service (AKS).

This article provides architectural recommendations for making informed decisions when you use Machine Learning to train, deploy, and manage machine learning models. The guidance is based on the [Azure Well-Architected Framework pillars](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a _design checklist_ that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are _recommendations_ on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Machine Learning and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments.
>
> The foundational architecture [baseline OpenAI end-to-end chat reference architecture](/azure/architecture/ai-ml/architecture/baseline-openai-e2e-chat) demonstrates many of the key recommendations.

##### Technology scope

This review focuses on the interrelated decisions for these Azure resources:  

- Machine Learning
- Machine Learning compute clusters
- Machine Learning compute instances

The review doesn't address connected resources such as data stores or Azure Key Vault.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [Reliability design principles](/azure/well-architected/reliability/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

##### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md) and determine its relevance to your business requirements. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Resiliency**: Deploy models to environments that support availability zones, such as AKS. By ensuring deployments are distributed across availability zones, you're ensuring a deployment is available even in the event of a datacenter failure. For enhanced reliability and availability, consider a multi-region deployment topology.
>
> - **Resiliency**: Ensure you have sufficient compute for both training and inferencing. Through resource planning, make sure your compute SKU and scale settings meet the requirements of your workload.
>
> - **Resiliency**: Segregate Machine Learning workspaces used for exploratory work from those used for production.
>
> - **Resiliency**: When using managed online endpoints for inferencing, use a release strategy such as blue-green deployments to minimize downtime and reduce the risk associated with deploying new versions.
>
> - **Business requirements**: Select your use of compute clusters, compute instances, and externalized inference hosts based on reliability needs, considering service-level agreements (SLAs) as a factor.
>
> - **Recovery**: Ensure you have self-healing capabilities, such as checkpointing features supported by Machine Learning, when training large models.
>
> - **Recovery**: Ensure you have a recovery strategy defined. Machine Learning doesn't have automatic failover. Therefore, you must design a strategy that encompasses the workspace and all its dependencies, such as Key Vault, Azure Storage, and Azure Container Registry.

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
| **Multi-region model deployment**: For enhanced reliability and availability, consider a multi-region deployment environment when possible. | A multi-region deployment ensures that your Machine Learning workloads continue to run even if one region experiences an outage. Multi-region deployment improves load distribution across regions, potentially enhancing performance for users located in different geographical areas. For more information, see [Failover for business continuity and disaster recovery](/azure/machine-learning/how-to-high-availability-machine-learning). |
| **Model training resiliency**: Use checkpointing features supported by Machine Learning including Azure Container for PyTorch, the TensorFlow Estimator class, or the Run object and the FileDataset class that support model checkpointing. | Model checkpointing periodically saves the state of your machine learning model during training, so that it can be restored in case of interruption, failure, or termination. For more information, see [Boost checkpoint speed and reduce cost with Nebula](/azure/machine-learning/reference-checkpoint-performance-for-large-models). |
| **Use the Dedicated virtual machine tier for compute clusters**: Use the Dedicated virtual machine tier for compute clusters for batch inferencing to ensure your batch job isn't preempted. | Low-priority virtual machines come at a reduced price but are preemptible. Clusters that use the Dedicated virtual machine tier aren't preempted. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around Machine Learning.

##### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Availability**: Reduce the attack surface of the Machine Learning workspace by restricting access to the workspace to resources within the virtual network.
>
> - **Confidentiality**: Guard against data exfiltration from the Machine Learning workspace by implementing network isolation. Ensure access to all external resources is explicitly approved and access to all other external resources isn't permitted.
>
> - **Integrity**: Implement access controls that authenticate and authorize the Machine Learning workspace for external resources based on the least privilege principle.
>
> - **Integrity**: Implement use case segregation for Machine Learning workspaces by setting up workspaces based on specific use cases or projects. This approach adheres to the principle of least privilege by ensuring that workspaces are only accessible to individuals that require access to data and experimentation assets for the use case or project.
>
> - **Integrity**: Regulate access to foundational models. Ensure only approved registries have access to models in the model registry.
>
> - **Integrity**: Regulate access to approved container registries. Ensure Machine Learning compute can only access approved registries.
>
> - **Integrity**: Regulate the Python packages that can be run on Machine Learning compute. Regulating the Python packages ensures only trusted packages are run.
>
> - **Integrity**: Require code used for training in Machine Learning compute environments to be signed. Requiring code signing ensures that the code running is from a trusted source and hasn't been tampered with.
>
> - **Confidentiality**: Adhere to the principle of least privilege for role-based access control (RBAC) to the Machine Learning workspace and related resources, such as the workspace storage account, to ensure individuals have only the necessary permissions for their role, thereby minimizing potential security risks.
>
> - **Integrity**: Establish trust and verified access by implementing encryption for data at rest and data in transit.

##### Recommendations

| Recommendation | Benefit |
|--------|----|
| **Security baseline**: To enhance the security and compliance of your Machine Learning Service, apply the [Azure security baseline for Machine Learning](/security/benchmark/azure/baselines/machine-learning-service-security-baseline). | The security baseline provides tailored guidance on crucial security aspects such as network security, identity management, data protection, and privileged access. For optimal security, use Microsoft Defender for Cloud to monitor these aspects. |
| **Managed virtual network isolation**: Configure [managed virtual network isolation](/azure/machine-learning/how-to-managed-network) for Machine Learning. When you enable managed virtual network isolation, a managed virtual network is created for the workspace. Managed compute resources you create for the workspace automatically use this managed virtual network. If you can't implement managed virtual network isolation, then you must follow the [network topology recommendations](/azure/machine-learning/how-to-network-isolation-planning#recommended-architecture-use-your-azure-vnet) to separate compute into a dedicated subnet away from the rest of the resources in the solution, including the private endpoints for workspace resources. | Managed virtual network isolation enhances security by isolating your workspace from other networks, reducing the risk of unauthorized access. In a scenario in which a breach occurs in another network within your organization, the isolated network of your Machine Learning workspace remains unaffected, protecting your machine learning workloads. |
| **Machine Learning network isolation**: [Configure a private endpoint](/azure/machine-learning/how-to-configure-private-link) for your Machine Learning workspace and connect to the workspace over that private endpoint. | Machine Learning network isolation enhances security by ensuring that access to your workspace is secure and controlled. With a private endpoint configured for your workspace, you can then limit access to your workspace to only occur over the private IP addresses. |
| **Allow only approved outbound access**: [Configure the outbound mode](/azure/machine-learning/how-to-access-azureml-behind-firewall) on the Machine Learning workspace managed outbound access to `Allow only approved outbound` to minimize the risk of data exfiltration. Configure private endpoints, service tags, or fully qualified domain names (FQDNs) for resources that you need to access. | This configuration minimizes the risk of data exfiltration, improving data security. With this configuration enabled, a malicious actor who gains access to your system can’t send your data to an unapproved external destination. |
| **Virtual network isolation for dependent services**: Configure dependent services, such as Storage, Key Vault, and Container Registry with private endpoints and disable public access. | Network isolation bolsters security by restricting access to Azure platform as a service (PaaS) solutions to private IP addresses only. |
| **Managed identity**: [Use managed identities for authentication](/azure/machine-learning/how-to-setup-authentication#configure-a-managed-identity) between Machine Learning and other services. | Managed identities improve security by eliminating the need to store credentials and manually manage and rotate service principals. |
| **Disable local authentication**: [Disable local authentication](/azure/machine-learning/how-to-integrate-azure-policy#disable-local-authentication) for Machine Learning compute clusters and instances. | Disabling local authentication increases the security of your Machine Learning compute and provides centralized control and management of identities and resource credentials. |
| **Disable the public SSH port**: Ensure the public Secure Shell (SSH) port is closed on the Machine Learning compute cluster by setting `remoteLoginPortPublicAccess` to `Disabled`. Apply a similar configuration if you use a different compute. | Disabling SSH access helps prevent unauthorized individuals from gaining access and potentially causing harm to your system and protects you against brute force attacks. |
| **Don't provision public IP addresses for Machine Learning compute**: Set [enableNodePublicIp](/azure/templates/microsoft.machinelearningservices/workspaces/computes#amlcomputeproperties) to `false` when provisioning Machine Learning compute clusters or compute instances. Apply a similar configuration if you use a different compute. |Refrain from provisioning public IP addresses to enhance security by limiting the potential for unauthorized access to your compute instance or clusters. |
| **Get the latest operating system image**: [Recreate compute instances to get the latest operating system image](/azure/ai-studio/how-to/create-manage-compute). | Using the latest images ensures you're maintaining a consistent, stable, and secure environment, including ensuring you have the latest security patches. |
| **Strict Machine Learning workspace access controls**: Use [Microsoft Entra ID groups to manage workspace access](/azure/machine-learning/how-to-assign-roles) and adhere to the principle of least privilege for RBAC. | Strict workspace access controls enhance security by ensuring that individuals have only the necessary permissions for their role. A data scientist, for instance, might have access to run experiments but not to modify security settings, minimizing potential security risks. |
| **Restrict model catalog deployments**: [Restrict model deployments to specific registries](/azure/machine-learning/how-to-regulate-registry-deployments). | Restricting the deployments from the model catalog to specific registries ensures you only deploy models to approved registries. This approach helps regulate access to the open-source foundational models. |
| **Encrypt data at rest**: Consider using [customer-managed keys with Machine Learning](/azure/machine-learning/how-to-setup-customer-managed-keys). | Encrypting data at rest enhances data security by ensuring that sensitive data is encrypted by using keys directly managed by you. If you have a regulatory requirement to manage your own encryption keys, use this feature to comply with that requirement. |
| **Minimize the risk of data exfiltration**: [Implement data exfiltration prevention](/azure/machine-learning/how-to-prevent-data-loss-exfiltration). For example, create a service endpoint policy to filter egress virtual network traffic and permit data exfiltration only to specific Azure Storage accounts. | Minimize the risk of data exfiltration by limiting inbound and outbound requirements. |

##### Advisor

The following are some examples of the [Advisor](https://azure.microsoft.com/products/advisor) security best practice recommendations for Machine Learning:

- Workspaces should be encrypted with a customer-managed key (CMK).
- Workspaces should use Azure Private Link.
- Workspaces should disable public network access.
- Compute should be in a virtual network.
- Compute instances should be recreated to get the latest software updates.

##### Azure Policy

The following are examples of [built-in Azure Policy definitions for Machine Learning](/azure/machine-learning/policy-reference) security:

- [Configure allowed registries for specified Machine Learning computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5853517a-63de-11ea-bc55-0242ac130003).
- [Configure allowed Python packages for specified Machine Learning computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77eeea86-7e81-4a7d-9067-de844d096752).
- [Machine Learning Workspaces should disable public network access](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F438c38d2-3772-465a-a9cc-7a6666a275ce).
- [Machine Learning compute instances should be recreated to get the latest software updates](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff110a506-2dcb-422e-bcea-d533fc8c35e2).
- [Machine Learning computes should be in a virtual network](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7804b5c7-01dc-4723-969b-ae300cc07ff1).
- [Machine Learning computes should have local authentication methods disabled](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe96a9a5f-07ca-471b-9bc5-6a0f33cbd68f).
- [Machine Learning workspaces should be encrypted with a CMK](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fba769a63-b8cc-4b2d-abf6-ac33c7204be8).
- [Machine Learning workspaces should use Private Link](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45e05259-1eb5-4f70-9574-baf73e9d219b).
- [Machine Learning workspaces should use a user-assigned managed identity](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0c7d88-c7de-45b8-ac49-db49e72eaa78).
- [Require an approval endpoint called prior to jobs running for specified Machine Learning computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3948394e-63de-11ea-bc55-0242ac130003).
- [Require code signing for training code for computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a6f7384-63de-11ea-bc55-0242ac130003).
- [Restrict model deployment to specific registries](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F19539b54-c61e-4196-9a38-67598701be90).

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

Read the [Cost Optimization design principles](../cost-optimization/principles.md) to understand the approaches to achieve those goals and the necessary tradeoffs in technical design choices related to training and deploying models in their environments. 

##### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Usage optimization**: Choose the appropriate resources to ensure that they align with your workload requirements. For example, choose between CPUs or GPUs, various SKUs, or low versus regular-priority VMs.
>
> - **Usage optimization**: Ensure compute resources that aren't being used are scaled down or shut down when idle to reduce waste.
>
> - **Usage optimization**: Apply policies and configure quotas to comply with the design's upper and lower limits.
>
> - **Usage optimization**: Test parallelizing training workloads to determine if training requirements can be met on lower cost SKUs.
>
> - **Rate optimization**: Purchase Azure Reserved Virtual Machine Instances if you have a good estimate of usage over the next one to three years.
>
> - **Monitor and optimize**: Monitor your resource usage such as CPU and GPU usage when training models. If the resources aren't being fully used, modify your code to better use resources or scale down to smaller or cheaper VM sizes.

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
| **Optimize compute resources**: Optimize your compute resources based on the requirements of your workload. Choose the SKU that best suits your workload:<br><ul><li>General Purpose – Balanced CPU to memory ratio, good for all purposes.</li><li>Compute Optimized – High CPU to memory ratio, good for math-heavy computations.</li><li>Memory Optimized – High memory to CPU, good for in-memory computations or database applications.</li><li>M Series – Very large machines that have huge amounts of memory and CPU. </li><li> GPU – Better for models with a high number of variables that can benefit from higher parallelism and specialized core instructions. Typical applications are deep learning, image or video processing, scientific simulations, data mining, and taking advantage of GPU development frameworks. Test with multiple families and document the results as your baseline. As your model and data evolve, the most adequate compute resource might change. Monitor execution times and reevaluate as needed. | Selecting the right compute is critical as it directly impacts the cost of running your workload. Choosing a GPU or a high-performance SKU without proper usage can lead to wasteful spending, while choosing undersized compute can lead to prohibitively long training times and performance problems. |
| **Optimize compute scaling**: [Configure your compute clusters for autoscaling](/azure/machine-learning/how-to-manage-optimize-cost#configure-training-clusters-for-autoscaling) to ensure you only use what you need.<br><br>For training clusters, set the minimum number of nodes to 0 and configure the amount of time the node is idle to an appropriate time. For less iterative experimentation, reduce the time to save costs. For more iterative experimentation, use a higher time to prevent paying for scaling up or down after each change. | Configure autoscaling for compute clusters to scale down when their usage is low. <br><br> Set the minimum number of nodes to 0 for training clusters to scale down to 0 when not in use. |
| **Set training termination policies**: [Set early termination policies](/azure/machine-learning/how-to-tune-hyperparameters#early-termination) to limit the duration of training runs or terminate them early. | Setting termination policies can help you save costs by stopping nonperforming runs early. |
| **Use low-priority virtual machines for batch workloads**: Consider using [low-priority virtual machines for batch workloads](/azure/machine-learning/how-to-use-low-priority-batch) that aren't time-sensitive and in which interruptions are recoverable. | Low-priority virtual machines enable a large amount of compute power to be used for a low cost. They take advantage of surplus capacity in Azure. |
| **Enable idle shutdown for compute instances**: Enable [idle shutdown for compute instances](/azure/machine-learning/how-to-create-compute-instance#configure-idle-shutdown) or [schedule a start and stop time](/azure/machine-learning/how-to-create-compute-instance#schedule-automatic-start-and-stop) if usage time is known. | By default, compute instances are available to you, accruing cost. Configuring compute instances to shut down when idle or configuring a schedule for them saves cost when they aren't in use. |
| **Parallelize training workloads**: Consider [parallelizing training workloads](/azure/machine-learning/how-to-use-parallel-job-in-pipeline). Test running them with the help of the parallel components in Machine Learning. | Parallel workloads can be run on multiple smaller instances, potentially yielding cost savings. |
| **Azure Reserved VM Instances**: Purchase Azure Reserved VM Instances if you have a good estimate of usage over the next one to three years. Take advantage of reserved capacity options for services when you have good estimates of usage. | Purchase Azure Reserved VM Instances to prepay for virtual machine usage and provide discounts with pay-as-you-go pricing. The discount is automatically applied for virtual machine usage that matches the reservation. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

##### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Machine Learning.

> [!div class="checklist"]
>
> - **Development standards**: Take advantage of Machine Learning model catalogs and registries to store, version, and share machine learning assets.
>
> - **Automate for efficiency**: Follow good [machine learning operations (MLOps)](https://azure.microsoft.com/products/machine-learning/mlops/) practices. When possible, build end-to-end automated pipelines for data preparation, training, and scoring processes. In development, use scripts instead of notebooks for training models, as scripts are easier to integrate into automated pipelines.
>
> - **Deploy with confidence**: Implement infrastructure as code (IaC) for Machine Learning workspaces, compute clusters, compute instances, and other deployment environments.
>
> - **Observability**: Monitor the performance of your deployed models including data drift.
>
> - **Observability**: If your models are deployed to online endpoints, [enable Application Insights](/azure/machine-learning/how-to-monitor-online-endpoints#using-application-insights) to [monitor online endpoints and deployments](/azure/machine-learning/how-to-monitor-online-endpoints). Monitor training infrastructure to ensure you're meeting your baseline requirements.
>
> - **Simplicity**: Use curated environments optimized for Machine Learning, when available.

##### Recommendations

| Recommendation | Benefit |
|--------|----|
| **Minimize Machine Learning workspace instances**: Minimize the number of workspaces, when possible, to reduce maintenance. | Limiting the number of workspaces reduces the maintenance effort and cost of operation. For requirements, such as security, you might need multiple separate workspaces. Minimize the number of workspaces when possible. |
| **Take advantage of model catalogs and registries**: Take advantage of Machine Learning model catalogs and registries to store, version, and share machine learning assets.<br><br>Use Machine Learning model catalogs to help you implement A/B testing and deployment of models. | Use Machine Learning model registries to store and version your machine learning models to track changes and maintain lineage with the job and datasets used for training.<br><br> With Machine Learning model catalogs, your data science teams can discover, evaluate, and fine tune pretrained foundational machine learning models.<br><br> Storing versioned models in Machine Learning model registries supports deployment strategies such as A/B releases, canary releases, and rollbacks. |
| **Monitor model performance**: [Monitor the performance of your deployed models](/azure/machine-learning/how-to-monitor-model-performance), and [detect data drift on datasets](/azure/machine-learning/how-to-monitor-datasets).| Monitoring deployed models ensures your models meet the performance requirements.<br><br>Monitoring data drift helps you detect changes in the input data that can lead to a decline in your model’s performance. Managing data drift helps you ensure that your model provides accurate results over time. |
| **Monitor infrastructure**: If your models are deployed to online endpoints, [enable Application Insights](/azure/machine-learning/how-to-monitor-online-endpoints#using-application-insights) to [monitor online endpoints and deployments](/azure/machine-learning/how-to-monitor-online-endpoints).<br><br>Monitor training infrastructure to ensure you're meeting your baseline requirements.<br><br>Ensure you're [collecting resource logs](/azure/machine-learning/monitor-azure-machine-learning#collection-and-routing) for Machine Learning. | Monitoring endpoints gives you visibility into metrics such as request latency and requests per minute. You can compare your performance versus your baseline and use this information to make changes to compute resources accordingly. Monitoring metrics such as network bytes can alert you if you're approaching quota limits and prevent throttling.<br><br>Likewise, monitoring your training environment provides you with the information to make changes to your training environment. Use that information to decide to scale in or out, scale up or down with different performant SKUs, or choose between CPUs or GPUs. |
| **Curate model training environments**: Use curated environments optimized for Machine Learning, when available. | [Curated environments](/azure/machine-learning/resource-curated-environments) are pre-created environments provided by Machine Learning that speed up deployment time and reduce deployment and training latency. Using curated environments improves training and deployment success rates and avoids unnecessary image builds. <br><br>Curated environments, such as [Azure Container for PyTorch](/azure/machine-learning/resource-azure-container-for-pytorch), can also be optimized for training large models on Machine Learning. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

##### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for Machine Learning workloads.

> [!div class="checklist"]
>
> - **Performance targets**: Determine the acceptable training time and retrain frequency for your model. Setting a clear target for training time, along with testing, helps you determine the compute resources, CPU versus GPU, and CPU SKUs required to meet the training time goal.
>
> - **Performance targets**: Define the acceptable performance targets for your deployed models including response time, requests per second, error rate, and uptime. Performance targets act as a benchmark for your deployed model's efficiency. Targets can help you make CPU versus GPU determinations, CPU SKU choices, and scaling requirements.
>
> - **Meet capacity requirements**: Choose the right compute resources for model training.
>
> - **Meet capacity requirements**: Choose the right compute resources for model deployments.
>
> - **Meet capacity requirements**: Choose deployment environments with autoscaling capabilities to add and remove capacity as demand fluctuates. 
>
> - **Achieve and sustain performance**: Continuously [monitor the performance of your deployed models](/azure/machine-learning/how-to-monitor-model-performance), review results, and take appropriate actions.
>
> - **Achieve and sustain performance**: Continuously monitor the performance of your infrastructure of deployed models, review results, and take appropriate actions. Monitor training infrastructure to ensure you're meeting your requirements for training time.

##### Recommendations

| Recommendation | Benefit |
|--------|----|
| **Select appropriate compute services for model training**: Consider Machine Learning compute clusters over compute instances for model training if you require autoscaling.<br><br>Optimize your compute resources based on the training requirements. First choose between CPUs and GPUs. Default to CPUs, but consider GPUs for workloads such as deep learning, image or video processing, or large amounts of data. Next, choose the image SKU that best suits your workload.<br><br>Use testing to choose the compute option that optimizes cost against training time when determining your baseline. | Selecting the right compute is critical as it directly impacts the training time. Choosing the right SKU and CPU versus GPU ensures your model training can meet your requirements and performance targets. Choosing a low-performance SKU that's overused can lead to prohibitively long training times and performance problems.<br><br> Compute clusters provide the ability to improve performance by scaling out workloads that support horizontal scaling. This method provides flexibility for handling workloads with different demands and lets you add or remove machines as needed. |
| **Model deployment environment scaling**: Use the deployment environment’s autoscale capabilities. For AKS deployment environments, use the cluster autoscaler to scale to meet demand. For online endpoints, [automatically scale via integration with the Azure Monitor autoscale feature](/azure/machine-learning/how-to-autoscale-endpoints). | Autoscaling adjusts the number of instances of the deployed model to match demand. |
| **Monitor model performance**: [Monitor the performance of your deployed models.](/azure/machine-learning/how-to-monitor-model-performance) | Tracking the performance of models in production alerts you to potential problems such as data drift, prediction drift, data quality, and feature attribution drift.<br><br>Monitoring data drift helps you detect changes in the input data that can lead to a decline in your model’s performance. Managing data drift helps you ensure that your model provides accurate results over time. |
| **Monitor infrastructure**: [Monitor online endpoints](/azure/machine-learning/how-to-monitor-online-endpoints) and integrate with Monitor to track and monitor the appropriate metrics and logs. Enable [Application Insights](/azure/machine-learning/how-to-monitor-online-endpoints#using-application-insights) when creating online deployments.<br><br>Monitor training infrastructure and review resource usage such as memory and CPU or GPU usage when training models to ensure you're meeting your baseline requirements. | Monitoring endpoints gives you visibility into metrics such as request latency and requests per minute. You can compare your performance versus your baseline and use this information to make changes to compute resources accordingly. Monitoring metrics such as network bytes can alert you if you're approaching quota limits and prevent throttling.<br><br>Likewise, monitoring your training environment provides you with the information to make changes to your training environment. Use that information to decide to scale in or out, scale up or down with different performant SKUs, or choose between CPUs or GPUs. |

## Azure policies

Azure provides an extensive set of built-in policies related to
Machine Learning and its dependencies. Some of the preceding recommendations can be audited through Azure policies. Consider the following policies that are related to security:

- [Allowed registries for specified Machine Learning computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5853517a-63de-11ea-bc55-0242ac130003).
- [Configure allowed Python packages for specified Machine Learning computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77eeea86-7e81-4a7d-9067-de844d096752).
- [Machine Learning computes should be in a virtual network](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7804b5c7-01dc-4723-969b-ae300cc07ff1).
- [Machine Learning computes should have local authentication methods disabled](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe96a9a5f-07ca-471b-9bc5-6a0f33cbd68f).
- [Machine Learning workspaces should disable public network access](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F438c38d2-3772-465a-a9cc-7a6666a275ce).
- [Machine Learning compute instances should be recreated to get the latest software updates](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff110a506-2dcb-422e-bcea-d533fc8c35e2).
- [Machine Learning workspaces should be encrypted with a customer-managed key](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fba769a63-b8cc-4b2d-abf6-ac33c7204be8).
- [Machine Learning workspaces should use private link](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45e05259-1eb5-4f70-9574-baf73e9d219b).
- [Machine Learning workspaces should use user-assigned managed identity](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0c7d88-c7de-45b8-ac49-db49e72eaa78).
- [Require an approval endpoint called prior to jobs running for specified Machine Learning computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3948394e-63de-11ea-bc55-0242ac130003).
- [Require code signing for training code for computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a6f7384-63de-11ea-bc55-0242ac130003).
- [Restrict model deployment to specific registries](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F19539b54-c61e-4196-9a38-67598701be90).

Consider the following policy that's related to cost optimization:

- [Machine Learning Compute instance should have idle shutdown](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F679ddf89-ab8f-48a5-9029-e76054077449).

Consider the following policies that are related to operational excellence:

- [Require log filter expressions and datastore to be used for full logs for specified Machine Learning computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d413020-63de-11ea-bc55-0242ac130003).
- [Resource logs in Machine Learning workspaces should be enabled](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafe0c3be-ba3b-4544-ba52-0c99672a8ad6).

For comprehensive governance, review the
[Azure Policy built-in definitions for Machine Learning]( /azure/governance/policy/samples/built-in-policies#machine-learning).

## Advisor recommendations

Advisor is a personalized cloud consultant that helps you follow
best practices to optimize your Azure deployments. Advisor recommendations can help you improve the reliability, security,
cost effectiveness, performance, and operational excellence of Machine Learning.

Consider the following [Advisor](https://azure.microsoft.com/products/advisor) recommendations for security:

- Workspaces should be encrypted with a customer-managed key (CMK). 
- Workspaces should use private link.
- Workspaces should disable public network access.
- Compute should be in a virtual network.
- Compute instances should be recreated to get the latest software updates.

Consider the following [Advisor](https://azure.microsoft.com/products/advisor) recommendation for operational excellence: 

- Resource logs in Machine Learning workspaces should be enabled.

## Next steps

Consider these articles as resources that demonstrate the recommendations highlighted in this article.

- Use the [baseline OpenAI end-to-end chat reference architecture](/azure/architecture/ai-ml/architecture/baseline-openai-e2e-chat) as an example of how these recommendations can be applied to a workload.
- Use [Machine Learning](/azure/machine-learning) product documentation to build implementation expertise.