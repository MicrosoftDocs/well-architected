---
title: Azure Well-Architected Framework - Azure Machine Learning
description: Design considerations and recommendations about Azure Machine Learning.
author: robbagby
ms.author: robbag
ms.date: 02/06/2024
ms.topic: conceptual
products:
  - azure-machine-learning
categories:
  - ai-machine-learning
---

# Azure Well-Architected Framework perspective on Azure Machine Learning

Azure Machine Learning is a managed cloud service that is used to train, deploy, and manage machine learning models. There are a wide range of choices and configurations for both training and deploying models, including compute SKUs and configurations. Machine learning models can be deployed to Azure Machine Learning compute or to other Azure services such as Azure Kubernetes Service (AKS).

This article provides architectural recommendations for making informed decisions when using Azure Machine Learning to train, deploy, and manage machine learning models. The guidance is based on the [**Azure Well-Architected Framework pillars**](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a _design checklist_ that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are _recommendations_ on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure Machine Learning and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments.
>
> The following foundational architecture demonstrates many of the key recommendations: [Baseline OpenAI end-to-end chat reference architecture](/azure/architecture/ai-ml/architecture/baseline-openai-e2e-chat).

##### Technology scope

This review focuses on the interrelated decisions for these Azure resources:  

- Azure Machine Learning
- Azure Machine Learning compute clusters
- Azure Machine Learning compute instances

The review doesn't address connected resources such as data stores or Azure Key Vault.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

##### Design checklist

Start your design strategy based on the [**design review checklist for Reliability**](../reliability/checklist.md) and determine its relevance to your business requirements. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Resilience** Deploy models to environments that support availability zones, such as Azure Kubernetes Service (AKS). By ensuring deployments are distributed across availability zones, you're ensuring a deployment is available even in the event of a datacenter failure. For enhanced reliability and availability, consider a multi-region deployment topology.
>
> - **Resilience** Ensure you have sufficient compute for both training and inferencing. Through resource planning, make sure your compute SKU and scale settings meets the requirements of your workload.
>
> - **Resilience** Segregate Azure Machine Learning workspaces used for exploratory work from those used for production.
>
> - **Resilience** When using managed online endpoints for inferencing, use a release strategy such as blue-green deployments to minimize downtime and reduce the risk associated with deploying new versions.
>
> - **Business requirements** Select your use of compute clusters, compute instances, and/or externalized inference hosts based on reliability needs, considering service SLAs as a factor.
>
> - **Recovery** Ensure you have self-healing capabilities, such as checkpointing features supported by Azure Machine Learning, when training large models.
>
> - **Recovery** Ensure you have a recovery strategy defined. Azure Machine Learning doesn't have automatic failover. Therefore, you must design a strategy that encompasses the workspace and all its dependencies, such as Azure Key Vault, Storage, and Azure Container Registry.

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
| **Multi-region model deployment** For enhanced reliability and availability, consider a multi-region deployment environment when required. | A multi-region deployment ensures that your Azure Machine Learning workloads continue to run even if one region experiences an outage. In addition, multi-region deployment improves load distribution across regions, potentially enhancing performance for users located in different geographical areas.  For more information, see [Failover for business continuity and disaster recovery](/azure/machine-learning/how-to-high-availability-machine-learning). |
| **Model training resiliency** Use checkpointing features supported by Azure Machine Learning including Azure Container for PyTorch, the TensorFlow Estimator class, or the Run object and the FileDataset class that support model checkpointing. | Model checkpointing periodically saves the state of your machine learning model during training, so that it can be restored in case of interruption, failure, or termination. For more information, see [Boost Checkpoint Speed and Reduce Cost with Nebula](/azure/machine-learning/reference-checkpoint-performance-for-large-models). |
| **Use the Dedicated virtual machine tier for compute clusters** Use the `Dedicated` virtual machine tier for compute clusters for batch inferencing to ensure your batch job is not preempted. | Low-priority virtual machines come at a reduced price but are preemptible. Clusters that use the dedicated tier aren't preempted. |

##### Azure Policy and Azure Advisor

There are no [Azure Advisor](https://azure.microsoft.com/products/advisor) best practice recommendations or [built-in Azure Policy definitions](/azure/machine-learning/policy-reference) for reliability for Azure Machine Learning.

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around Azure Machine Learning.

##### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Availability** Reduce the attack surface of the Azure Machine Learning workspace by restricting access to the workspace to resources within the virtual network.
>
> - **Confidentiality** Guard against data exfiltration from the Azure Machine Learning workspace by implementing network isolation. Ensure access to all external resources is explicitly approved and access to all other external resources is not permitted.
>
> - **Integrity** Implement access controls that authenticate and authorize Azure Machine Learning workspace to external resources based on the least privilege principle.
>
> - **Integrity** Implement use case segregation for Azure Machine Learning workspaces by setting up workspaces based on specific use cases or projects. This adheres to the least privileged principle by ensuring that workspaces are only accessible by individuals that require access to data and experimentation assets for the use case or project.
>
> - **Integrity** Regulate access to foundational models. Ensure only approved registries have access to models in the model registry.
>
> - **Integrity** Regulate access to approved Azure Container Registries. Ensure Azure Machine Learning compute can only access approved registries.
>
> - **Integrity** Regulate the Python packages that can be run on Azure Machine Learning compute. Regulating the Python packages ensures only trusted packages are run.
>
> - **Integrity** Require code used for training in Azure Machine Learning compute environments to be signed. Requiring code signing ensures that the code running is from a trusted source and has not been tampered with.
>
> - **Confidentiality** Adhere to the principle of least privilege for Role-Based Access Control (RBAC) to the Azure Machine Learning workspace and related resources, such as the workspace Storage Account, to ensure individuals have only the necessary permissions for their role, thereby minimizing potential security risks.
>
> - **Integrity** Establish trust and verified access by implementing encryption for data at rest and in transit.

##### Recommendations

| Recommendation | Benefit |
|--------|----|
| **Security Baseline** To enhance the security and compliance of your Azure Machine Learning Service, apply the [Azure security baseline for Machine Learning Service](/security/benchmark/azure/baselines/machine-learning-service-security-baseline). | The security baseline provides tailored guidance on crucial security aspects such as network security, identity management, data protection, and privileged access. Monitor these aspects by using Microsoft Defender for Cloud for optimal security. |
| **Managed virtual network isolation** Configure [managed virtual network isolation](/azure/machine-learning/how-to-managed-network) for Azure Machine Learning. When you enable managed virtual network isolation, a managed VNet is created for the workspace and managed compute resources you create for the workspace automatically use this managed VNet. If you can't use managed virtual network isolation, then you must follow the [network topology recommendations](/azure/machine-learning/how-to-network-isolation-planning#recommended-architecture-use-your-azure-vnet) of separating compute into a dedicated subnet away from the rest of the resources in the solution, including the private endpoints for workspace resources. | Managed virtual network isolation enhances security by isolating your workspace from other networks, reducing the risk of unauthorized access. In a scenario in which a breach occurs in another network within your organization, the isolated network of your Azure Machine Learning workspace would remain unaffected, protecting your machine learning workloads. |
| **Azure Machine Learning network isolation** [Configure a private endpoint](/azure/machine-learning/how-to-configure-private-link) for your Azure Machine Learning workspace and connect to the workspace over that private endpoint. | Azure Machine Learning network isolation enhances security by ensuring that access to your workspace is secure and controlled. With a private endpoint configured for your workspace, you can then limit access to your workspace to only occur over the private IP addresses. |
| **Allow only approved outbound access** [Configure the outbound mode](/azure/machine-learning/how-to-access-azureml-behind-firewall) on the Azure Machine Learning workspace managed outbound access to `Allow only approved outbound` to minimize the risk of data exfiltration. Configure private endpoints, service tags, or FQDNs for resources that you need to access. | Minimizes the risk of data exfiltration, improving data security. This configuration would prevent a malicious actor who gains access to your system from sending your data to an unapproved external destination. |
| **Virtual network isolation for dependent services** Configure dependent services, such as Azure Storage, Azure Key Vault, and Azure Container Registry with private endpoints and disable public access. | Network isolation bolsters security by restricting access to Azure PaaS services to private IP addresses only. |
| **Managed identity** [Use managed identities for authentication](/azure/machine-learning/how-to-setup-authentication#configure-a-managed-identity) between Azure Machine Learning and other services. | Managed identities improve security by eliminating the need to store credentials and eliminating the need for manual management and rotation of service principles. |
| **Disable local authentication** [Disable local authentication](/azure/machine-learning/how-to-integrate-azure-policy#disable-local-authentication) for Azure Machine Learning compute clusters and instances | Disabling local authentication increases the security of your Azure Machine Learning compute and provides centralized control and management of identities and resource credentials. |
| **Disable the public SSH port** Ensure the public ssh port is closed on the Azure Machine Learning compute cluster by setting `remoteLoginPortPublicAccess` to `Disabled`. Use similar configuration if you're using other compute. | Disabling SSH access helps prevent unauthorized individuals from gaining access and potentially causing harm to your system and protects you against brute force attacks. |
| **Do not provision public IPs for Azure Machine Learning Compute** Set [enableNodePublicIp](/azure/templates/microsoft.machinelearningservices/workspaces/computes#amlcomputeproperties) to `false` when provisioning Azure Machine Learning compute clusters or compute instances. Use similar configuration if you're using other compute. | Not provisioning public IPs enhances security by limiting the potential for unauthorized access to your compute instance or clusters. |
| **Get latest OS image** [Recreate compute instances to get the latest OS image](/azure/ai-studio/how-to/create-manage-compute) | Using the latest images ensures you're maintaining a consistent, stable, and secure environment, including ensuring you have the latest security patches. |
| **Strict Azure Machine Learning workspace access controls** Use [Microsoft Entra ID groups to manage workspace access](/azure/machine-learning/how-to-assign-roles) and adhere to the principle of least privilege for Role-Based Access Control (RBAC). | Strict workspace access controls enhance security by ensuring that individuals have only the necessary permissions for their role. A data scientist, for instance, might have access to run experiments but not to modify security settings, minimizing potential security risks. |
| **Restrict Model Catalog deployments** Follow the guidance to [restrict model deployments to specific registries](/azure/machine-learning/how-to-regulate-registry-deployments) | Restricting the deployments from the Model Catalog to specific registries ensures you only deploy models to approved registries. This helps regulate access to the open-source foundational models. |
| **Encrypt data at rest** Consider using [customer-managed keys with Azure Machine Learning](/azure/machine-learning/how-to-setup-customer-managed-keys) | Encrypting data at rest enhances data security by ensuring that sensitive data is encrypted by using keys directly managed by you. If you have a regulatory requirement to manage your own encryption keys, use this feature to comply with that requirement. |
| **Follow Azure Machine Learning data exfiltration prevention guidance** Follow the guidance in [Azure Machine Learning data exfiltration prevention](/azure/machine-learning/how-to-prevent-data-loss-exfiltration) to minimize data exfiltration risk. For example, create a service endpoint policy to filter egress virtual network traffic and permit data exfiltration only to specific Azure Storage accounts. | Following this guidance helps minimize the risk of data exfiltration by limiting inbound and outbound requirements. |

##### Azure Advisor

The following are some examples of the [Azure Advisor](https://azure.microsoft.com/products/advisor) security best practice recommendations for Azure Machine Learning:

- Workspaces should be encrypted with a customer-managed key (CMK)
- Workspaces should use private link
- Workspaces should disable public network access
- Compute should be in a virtual network
- Compute instances should be recreated to get the latest software updates

##### Azure Policy

The following are examples of [built-in Azure Policy definitions for Azure Machine Learning](/azure/machine-learning/policy-reference) security:

- [Restrict model deployment to specific registries](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F19539b54-c61e-4196-9a38-67598701be90)
- [Require code signing for training code for computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a6f7384-63de-11ea-bc55-0242ac130003)
- [Configure allowed Python packages for specified Azure Machine Learning computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77eeea86-7e81-4a7d-9067-de844d096752)
- [Allowed registries for specified Azure Machine Learning computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5853517a-63de-11ea-bc55-0242ac130003)
- [Require an approval endpoint called prior to jobs running for specified Azure Machine Learning computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3948394e-63de-11ea-bc55-0242ac130003)
- [Azure Machine Learning compute instances should be recreated to get the latest software updates](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff110a506-2dcb-422e-bcea-d533fc8c35e2)
- [Azure Machine Learning Computes should be in a virtual network](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7804b5c7-01dc-4723-969b-ae300cc07ff1)
- [Azure Machine Learning Computes should have local authentication methods disabled](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe96a9a5f-07ca-471b-9bc5-6a0f33cbd68f)
- [Azure Machine Learning workspaces should be encrypted with a customer-managed key](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fba769a63-b8cc-4b2d-abf6-ac33c7204be8)
- [Azure Machine Learning Workspaces should disable public network access](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F438c38d2-3772-465a-a9cc-7a6666a275ce)
- [Azure Machine Learning workspaces should use private link](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45e05259-1eb5-4f70-9574-baf73e9d219b)
- [Azure Machine Learning workspaces should use user-assigned managed identity](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0c7d88-c7de-45b8-ac49-db49e72eaa78)

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

Read the [Cost Optimization design principles](../cost-optimization/principles.md) to understand the approaches for achieving those goals and understand the tradeoffs necessary in technical design choices related to training and deploying models and the environment they run in.

##### Design checklist

Start your design strategy based on the [**design review checklist for Cost Optimization**](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Usage optimization** Choose compute resources to ensure features such as CPU versus GPU, SKU, or low versus regular priority VMs matches workload requirements.
>
> - **Usage optimization** Ensure compute resources that aren't being used are scaled down or shut down when idle to reduce waste.
>
> - **Usage optimization** Apply policies and configure quotas to comply with the design's upper and lower limits.
>
> - **Usage optimization** Test parallelizing training workloads to determine if training requirements can be met on lower cost SKUs.
>
> - **Rate optimization** Purchase Azure VM reserved instances if you have a good estimate of usage over the next one to three years.
>
> - **Monitor and optimize** Regularly monitor your resource utilization such as CPU and GPU utilization when training models. If the resources aren't being fully used, modify your code to better use resources or scale down to smaller or cheaper VM sizes.

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
| Optimize compute resources: Optimize your compute resources based on the requirements of your workload. Choose the SKU that best suits your workload:<br><ul><li>General Purpose – balanced CPU to memory ratio, good for all purposes</li><li>Compute Optimized – high CPU to memory ratio, good for math heavy computations</li><li>Memory Optimized – high memory to CPU, good for in memory computations or database applications</li><li>M Series – monster sized machines with huge amounts of Memory and CPU</li></ul><br>GPU – better for models with high number of variables that can benefit from higher parallelism and specialized core instructions. Typical applications are deep learning, image/video processing, scientific simulations, data mining, and leveraging GPU development frameworks. Test with multiple families and document the results as your baseline. As your model and data evolve, the most adequate compute resource may change. Monitor execution times and reevaluate as needed. | Selecting the right compute is critical as it directly impacts the cost of running your workload. Choosing a GPU or a high-performance SKU without proper utilization can lead to wasteful spending, while choosing undersized compute can lead to prohibitively long training times and performance issues. |
| **Optimize compute scaling** [Configure your compute clusters for autoscaling](/azure/machine-learning/how-to-manage-optimize-cost#configure-training-clusters-for-autoscaling) to ensure you only use what you need.<br>For training clusters, set the minimum number of nodes to 0 and configure the amount of time the node is idle to an appropriate time. For less iterative experimentation, reduce the time to save costs. For more iterative experimentation, use a higher time to prevent paying for scaling up or down after each change. | Configure autoscaling for compute clusters to scale down when it is under lower use. <br> Set the minimum number of nodes to 0 for training clusters to scale down to 0 when not in use. |
| **Set training termination policies** [Set early termination policies](/azure/machine-learning/how-to-tune-hyperparameters#early-termination) to limit the duration of training runs or terminate them early. | Setting termination policies can help you save costs by stopping nonperforming runs early. |
| **Use low-priority virtual machines for batch workloads** Consider using [low-priority virtual machines for batch workloads](/azure/machine-learning/how-to-use-low-priority-batch) that aren't time-sensitive and in which interruptions are recoverable. | Low priority virtual machines enable a large amount of compute power to be used for a low cost. They take advantage of surplus capacity in Azure. |
| **Enable idle shutdown for compute instances** Enable [idle shutdown for compute instances](/azure/machine-learning/how-to-create-compute-instance#configure-idle-shutdown) or [schedule a start and stop time](/azure/machine-learning/how-to-create-compute-instance#schedule-automatic-start-and-stop) if usage time is known. | By default, compute instances are available to you, accruing cost. Configuring compute instances to shut down when idle or configuring a schedule for them saves cost when they aren't in use. |
| **Parallelize training workloads** Consider [parallelizing training workloads](/azure/machine-learning/how-to-use-parallel-job-in-pipeline). Test running them with the help of the parallel component in Azure Machine Learning. | Parallel workloads can be run on multiple smaller instances, potentially yielding cost savings. |
| **Azure VM reserved instances** Purchase Azure VM reserved instances if you have a good estimate of usage over the next one to three years. Take advantage of reserved capacity options for services when you have good estimates of usage. | Purchase Azure Reserved VM Instances to prepay for virtual machine usage and provide discounts over the pay-as-you-go pricing. The discount is automatically applied for virtual machine usage that matches the reservation. |

##### Azure Policy and Azure Advisor

There are no [Azure Advisor](https://azure.microsoft.com/products/advisor) recommendations for Cost Optimization.
 
The following is an example of a cost optimization [built-in Azure Policy definition for Azure Machine Learning](/azure/machine-learning/policy-reference):

[Azure Machine Learning Compute Instance should have idle shutdown](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F679ddf89-ab8f-48a5-9029-e76054077449).

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

##### Design checklist

Start your design strategy based on the [**design review checklist for Operational Excellence**](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Machine Learning.

> [!div class="checklist"]
>
> - **Development standards** Leverage Azure Machine Learning model catalogs and registries to store, version and share machine learning assets.
>
> - **Automate for efficiency** Follow good [MLOps](https://azure.microsoft.com/products/machine-learning/mlops/) practices. When possible, build end-to-end automated pipelines for data preparation, training, and scoring processes. In development, use scripts instead of notebooks for training models, as scripts are easier to integrate into automated pipelines.
>
> - **Deploy with confidence** Implement infrastructure as code (IaC) for Azure Machine Learning workspaces, compute clusters, compute instances, and other deployment environments.
>
> - **Observability** Monitor the performance of your deployed models including data drift.
>
> - **Observability** If your models are deployed to online endpoints, follow the guidance in [Monitor online endpoints](/azure/machine-learning/how-to-monitor-online-endpoints) and enable [Application Insights](/azure/machine-learning/how-to-monitor-online-endpoints#using-application-insights) for endpoints and deployments. Monitor training infrastructure to ensure you're meeting your baseline requirements.
>
> - **Simplicity** Use curated environments optimized for AML, when available.

##### Recommendations

| Recommendation | Benefit |
|--------|----|
| **Minimize Azure Machine Learning workspace instances** Minimize the number of workspaces, when possible, to reduce maintenance. | Limiting the number of workspaces reduces the maintenance effort and cost of operation. Other requirements, such as security, may necessitate multiple, separate workspaces, the guidance is to minimize the number, when possible. |
| **Leverage model catalogs and registries** Leverage Azure Machine Learning model catalogs and registries to store, version, and share machine learning assets.<br>Use Azure Machine Learning model catalogs to help you implement A/B testing and deployment of models. | Use Azure Machine Learning model registries to store and version your machine learning models to track changes and maintain lineage with the job and datasets used for training.<br> With Azure Machine Learning model catalogs, your data science teams can discover, evaluate, and fine-tune pretrained foundational machine learning models.<br>Storing versioned models in Azure Machine Learning model registries supports deployment strategies such as A/B and canary releases, and rollbacks. |
| **Monitor model performance** Monitor the performance of your deployed models. Follow the guidance in [monitor performance of models deployed to production](/azure/machine-learning/how-to-monitor-model-performance). | Monitoring deployed models ensures your models meet the performance requirements of your models.<br>Monitoring data drift helps you detect changes in the input data that can lead to a decline in your model’s performance. Managing data drift helps you ensure that your model provides accurate results over time. |
| **Monitor infrastructure** If your models are deployed to online endpoints, follow the guidance in [Monitor online endpoints](/azure/machine-learning/how-to-monitor-online-endpoints) and enable [Application Insights](/azure/machine-learning/how-to-monitor-online-endpoints#using-application-insights) for endpoints and deployments.<br>Monitor training infrastructure to ensure you're meeting your baseline requirements.<br>Ensure you're [collecting resource logs](/azure/machine-learning/monitor-azure-machine-learning#collection-and-routing) for Azure Machine Learning. | Monitoring endpoints gives you visibility into metrics such as request latency and requests per minute. You can compare your performance versus your baseline and use this information to make changes to compute resources accordingly. Monitoring metrics such as network bytes can alert you if you're approaching quota limits and prevent throttling.<br>Likewise, monitoring your training environment provides you with the information to make changes to your training environment such as scaling in/out, and helps you determine whether to scale up or down with different performant SKUs or make a CPU versus GPU determination. |
| **Curate model training environments** Use curated environments optimized for AML, when available. | [Curated environments](/azure/machine-learning/resource-curated-environments) are pre-created environments provided by Azure Machine Learning that speed up deployment time and reduce deployment and training latency. Using curated environments improves training and deployment success rates and avoids unnecessary image builds. <br>Curated environments, such as [Azure Container for PyTorch](/azure/machine-learning/resource-azure-container-for-pytorch), can also be optimized for training large models on Azure Machine Learning. |

##### Azure Advisor

The following is an example of a [Azure Advisor](https://azure.microsoft.com/products/advisor) recommendation for operational excellence:

- Resource logs in Azure Machine Learning Workspaces should be enabled

##### Azure Policy

The following are the examples of [built-in Azure Policy definitions for Azure Machine Learning](/azure/machine-learning/policy-reference) related to operational excellence:

- [Require log filter expressions and datastore to be used for full logs for specified Azure Machine Learning computes](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d413020-63de-11ea-bc55-0242ac130003)
- [Resource logs in Azure Machine Learning Workspaces should be enabled](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafe0c3be-ba3b-4544-ba52-0c99672a8ad6)
## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

##### Design checklist

Start your design strategy based on the [**design review checklist for Performance Efficiency**](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for Azure Machine Learning workloads.

> [!div class="checklist"]
>
> - **Performance targets** Determine the acceptable training time and retrain frequency for your model. Setting a clear target for training time, along with testing, helps you determine the compute resources, CPU versus GPU, and CPU SKUs, required to meet the training time goal.
>
> - **Performance targets** Define the acceptable performance targets for your deployed models including response time, requests per second, error rate, and uptime. Performance targets, such as these, act as a benchmark for your deployed model's efficiency. Targets can help you make CPU versus GPU determinations, CPU SKU choices, and scaling requirements.
>
> - **Meet capacity requirements** Choose the right compute resources for model training.
>
> - **Meet capacity requirements** Choose the right compute resources for model deployments.
>
> - **Meet capacity requirements** Choose deployment environments with autoscaling capabilities to add and remove capacity as demand fluctuates. 
>
> - **Achieve and sustain performance** Continuously monitor the performance of your deployed models, review results and take appropriate actions. Follow the guidance in [monitor performance of models deployed to production](/azure/machine-learning/how-to-monitor-model-performance).
>
> - **Achieve and sustain performance** Continuously monitor the performance of your infrastructure of deployed models, review results and take appropriate actions. Monitor training infrastructure to ensure you're meeting your training requirements for training time.

##### Recommendations

| Recommendation | Benefit |
|--------|----|
| **Select appropriate compute services for model training** Consider Azure Machine Learning compute clusters over compute instances for model training if you require the ability to autoscale.<br>Optimize your compute resources based on the training requirements. First choose between CPU and GPU. Default to CPU, but consider GPUs for workloads such as deep learning, image or video processing, or large amounts of data. Next, choose the image SKU that best suits your workload.<br>Use testing to choose the compute option that optimizes cost against training time when determining your baseline. | Selecting the right compute is critical as it directly impacts the training time. Choosing the right SKU and CPU versus GPU ensures your model training can meet your requirements and performance targets. Choosing a low-performance SKU that is over-utilized can lead to prohibitively long training times and performance issues.<br> Compute clusters provide the ability to improve performance by scaling out workloads that support horizontal scaling. This provides flexibility for handing workloads with different demands, and lets you add or remove machines as needed. |
| **Model deployment environment scaling** Use autoscaling capabilities of deployment environment. For AKS deployment environments, use the cluster autoscaler to scale to meet demand. For online endpoints, [autoscale through integration with the Azure Monitor autoscale feature](/azure/machine-learning/how-to-autoscale-endpoints). | Autoscaling adjusts the number of instances of the deployed model to match demand. |
| **Monitor model performance** Monitor the performance of your deployed models. Follow the guidance in [monitor performance of models deployed to production](/azure/machine-learning/how-to-monitor-model-performance).<br>Follow the guidance in [detect data drift on datasets](/azure/machine-learning/how-to-monitor-datasets). | Tracking the performance of models in production alerts you to potential issues such as data drift, prediction drift, data quality, and feature attribution drift.<br>Monitoring data drift helps you detect changes in the input data that can lead to a decline in your model’s performance. Managing data drift helps you ensure that your model provides accurate results over time. |
| **Monitor infrastructure** Follow the guidance in [Monitor online endpoints](/azure/machine-learning/how-to-monitor-online-endpoints) to integrate with Azure Monitor to track and monitor the appropriate metrics and logs. Enable [Application Insights](/azure/machine-learning/how-to-monitor-online-endpoints#using-application-insights) when creating online deployments.<br>Monitor training infrastructure and review resource utilization such as memory and CPU/GPU utilization when training models to ensure you're meeting your baseline requirements. | Monitoring endpoints gives you visibility into metrics such as request latency and requests per minute. You can compare your performance versus your baseline and use this information to make changes to compute resources accordingly. Monitoring metrics such as network bytes can alert you if you're approaching quota limits and prevent throttling.<br>Likewise, monitoring your training environment provides you with the information to make changes to your training environment such as scaling in/out, and helps you determine whether to scale up or down with different performant SKUs or make a CPU versus GPU determination. |

##### Azure Policy and Azure Advisor

There are no [Azure Advisor](https://azure.microsoft.com/products/advisor) best practice recommendations or [built-in Azure Policy definitions](/azure/machine-learning/policy-reference) for performance efficiency for Azure Machine Learning.

## Next steps

Consider these articles as resources that demonstrate the recommendations highlighted in this article.

- Use this reference architecture as an example of how these recommendations can be applied to a workload such as the [Baseline OpenAI end-to-end chat reference architecture](/azure/architecture/ai-ml/architecture/baseline-openai-e2e-chat).
- Build implementation expertise by using product documentation: [Azure Machine Learning](/azure/machine-learning)
