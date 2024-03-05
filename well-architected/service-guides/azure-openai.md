---
title: Azure Well-Architected Framework perspective on Azure OpenAI
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure OpenAI.
author: robbagby
ms.author: robbag
ms.date: 03/05/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-service-guide
products:
  - azure-openai
categories:
  - ai-machine-learning
---

# Azure Well-Architected Framework perspective on Azure OpenAI

The Microsoft Azure OpenAI Service provides REST API access to OpenAI language models, adding Azure networking and security capabilities. This article provides architectural recommendations for making informed decisions when using Azure OpenAI as part of your workload's architecture. The guidance is based on the [**Azure Well-Architected Framework pillars**](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a _design checklist_ that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are _recommendations_ on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure OpenAI and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments.
>
> Foundational architecture that demonstrates the key recommendations:
> [Baseline OpenAI end-to-end chat reference architecture](/azure/architecture/ai-ml/architecture/baseline-openai-e2e-chat) .

### Technology scope

This review focuses solely on the Azure OpenAI service.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover quickly from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the [**Design review checklist for Reliability**](../reliability/checklist.md) and determine its relevance to your business requirements. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Resilience**: Choose the appropriate deployment option of either pay-as-you-go or [provisioned throughput](/azure/ai-services/openai/concepts/provisioned-throughput) based on your use case. Because reserved capacity increases resiliency, choose provisioned throughput for production solutions. The pay-as-you-go approach is ideal for development/test environments.
>
> - **Redundancy**: Add the appropriate gateway(s) in front of your Azure OpenAI deployments. The gateway must have the capability to withstand transient failures like throttling and also route to multiple Azure OpenAI instances. Consider routing to instances in different regions to build regional redundancy.
>
> - **Resilience**: If you're using [provisioned throughput](/azure/ai-services/openai/concepts/provisioned-throughput), consider also deploying a pay-as-you-go instance to handle overflow. Through your gateway, route calls to the pay-as-you-go instance when your provisioned throughput model is being throttled, or through monitoring, when you predict the model will be throttled.
>
> - **Resilience**: Monitor capacity usage to ensure you aren't exceeding throughput limits. Regularly review capacity usage to achieve more accurate forecasting and help prevent service interruptions due to capacity constraints.
>
> - **Resilience**: Follow the guidance for large data files in [Customize a model with fine-tuning](/azure/ai-services/openai/how-to/fine-tuning) by importing the data from an Azure Blob store. The guidance states that large files can become unstable when uploaded through multipart forms because the requests are atomic and can't be retried or resumed. Consider files 100 MB or larger as large files.
>
> - **Recovery**: Define a recovery strategy that includes a recovery plan for models that have been fine-tuned and for training data uploaded to the Azure OpenAI service. Because Azure OpenAI doesn't have automatic failover, you must design a strategy that encompasses the Azure OpenAI service and all of its dependencies, such as storage that contains training data.
>
### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
| **Monitor rate limits for pay-as-you-go**: If you're using the pay-as-you-go approach, follow the [guidance to manage rate limits](/azure/ai-services/openai/how-to/quota) for your model deployments and [monitor usage](/azure/ai-services/openai/how-to/quota?tabs=rest#view-and-request-quota) of tokens per minute (TPM) and requests per minute (RPM). | This important throughput information provides information required to ensure that you assign enough TPM from your quota to meet the demand for your deployments.<br>Assigning enough quota prevents throttling of calls to your deployed models. |
| **Monitor provision-managed utilization for provisioned throughput**: If you're using the [provisioned throughput](/azure/ai-services/openai/concepts/provisioned-throughput) payment model, monitor [provision-managed utilization](/azure/ai-services/openai/how-to/monitoring). | It's important to monitor provision-managed utilization to ensure it doesn't exceed 100%, to prevent throttling of calls to your deployed models. |
| **Enable dynamic quota**: If your workload budget supports it, perform overprovisioning by enabling dynamic quota on model deployments. | Dynamic quota allows your deployment to consume more capacity than your quota normally would, as long as there's available capacity from an Azure perspective. Although not guaranteed, the chance of additional quota capacity could prevent undesired throttling. |
| **Tune content filters**: Tune content filters to minimize false positives from overly aggressive filters. | Content filters block prompts or completions based on an opaque risk analysis. Ensure content filters are tuned to allow expected usage for your workload. |

### Azure Policy and Azure Advisor

There are no [Azure Advisor](https://azure.microsoft.com/products/advisor) best-practice recommendations or [built-in Azure Policy definitions](/azure/machine-learning/policy-reference) for Reliability for Azure OpenAI.

## Security

The purpose of the **Security** pillar is to provide **confidentiality**, **integrity**, and **availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around Azure OpenAI.

### Design checklist

Start your design strategy based on the [**Design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Then, review the [Azure security baseline for Azure OpenAI](/security/benchmark/azure/baselines/azure-openai-security-baseline). Finally, extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Protect confidentiality**: If you upload training data to the Azure OpenAI service, use [customer-managed keys](/azure/ai-services/openai/encrypt-data-at-rest#customer-managed-keys-with-azure-key-vault) for data encryption, implement a key-rotation strategy, and follow [guidance on deleting training, validation, and training results data](/azure/ai-services/openai/encrypt-data-at-rest#delete-training-validation-and-training-results-data). If you use an external data store for training data, follow security best practices for that store. For example, for Azure Blob Storage, use customer-managed keys for encryption and implement a key-rotation strategy. Use managed identity-based access, implement a network perimeter by using private endpoints, and enable access logs.
>
> - **Protect confidentiality**: Guard against data exfiltration by limiting the outbound URLs the Azure OpenAI services resources can access.
>
> - **Protect integrity**: Implement access controls to authenticate and authorize user access to the system by using the least-privilege principle and by using individual identities instead of keys.
>
> - **Protect integrity**: Follow the guidance in [jailbreak risk detection](/azure/ai-services/content-safety/concepts/jailbreak-detection) to safeguard your language model deployments against prompt injection attacks.
>
> - **Protect availability**: Use security controls to prevent attacks that might exhaust model usage quotas. These controls can include isolating the service on a network. If the service must be accessible from the internet, consider using a gateway to make routing or throttling decisions that block suspected abuse.

### Recommendations

| Recommendation | Benefit |
|--------|----|
| **Secure keys**: If your architecture requires Azure OpenAI key-based authentication, store those keys in Azure Key Vault, not in application code. | Separating secrets from code, such as storing them in Azure Key Vault, reduces the chance of leaking secrets. Separation also facilitates central management of secrets, easing responsibilities like key rotation. |
| **Restrict access**: [Disable public access](/azure/ai-services/openai/how-to/use-your-data-securely#disable-public-network-access) to Azure OpenAI unless your workload requires it. Create [private endpoints](/azure/ai-services/cognitive-services-virtual-networks#use-private-endpoints) if you're connecting from consumers in an Azure virtual network. | Controlling access to Azure OpenAI helps prevent attacks from unauthorized users. Using private endpoints ensures network traffic remains private between the application and the platform. |
| **Microsoft Entra ID**: Use Microsoft Entra ID for authentication and to authorize access to OpenAI using role-based access control (RBAC). Follow the guidance in [Disable local authentication in Azure AI Services](/azure/ai-services/disable-local-auth) and set `disableLocalAuth` to `true`. Grant identities performing completions or image generation the [Cognitive Services OpenAI User](/azure/ai-services/openai/how-to/role-based-access-control#cognitive-services-openai-user) role. Grant model automation pipelines and ad-hoc data-science access a role like [Cognitive Services OpenAI Contributor](/azure/ai-services/openai/how-to/role-based-access-control#cognitive-services-openai-contributor). | Using Microsoft Entra ID centralizes the identity-management component and eliminates the use of API keys. Using RBAC with Microsoft Entra ID ensures users or groups have exactly the permissions they need to do their job. This kind of fine-grained access control isn't possible with Azure OpenAI API keys. |
| **Use customer-managed keys**: Follow the guidance in [Azure OpenAI Service encryption of data at rest](/azure/ai-services/openai/encrypt-data-at-rest) to use customer-managed keys for fine-tuned models and training data uploaded to the OpenAI service. | Using customer-managed keys gives you greater flexibility to create, rotate, disable, and revoke access controls. |
| **Protect against jailbreak**: Use [Azure Content Safety Studio](https://contentsafety.cognitive.azure.com/) to detect jailbreak risks. | Detect jailbreak attempts to identify and block prompts intending to bypass the safety mechanisms of your Azure OpenAI deployments. |

### Azure Advisor and Azure Policy

The following are examples of [built-in Azure Policy definitions for Azure OpenAI](/azure/machine-learning/policy-reference) security:

- [Disable key access](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc)
- [Restrict network access](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3)
- [Disable public network access](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca)
- [Use private link](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01)
- [Enable data encryption with customer-managed key](https://portal.azure.com/#view/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67121cc7-ff39-4ab8-b7e3-95b84dab487d)

These Azure Policy definitions are also [Azure Advisor](https://azure.microsoft.com/products/advisor) security best-practice recommendations for Azure OpenAI.

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

Read the [Cost Optimization design principles](../cost-optimization/principles.md) to learn about approaches for achieving those goals and the tradeoffs necessary in technical design choices related to Azure OpenAI.

### Design checklist

Start your design strategy based on the [**Design review checklist for Cost Optimization**](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with its allocated budget. Your design should use the appropriate Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Cost management**: Develop your cost model, considering prompt sizes. Understanding prompt input and response sizes and how text translates into tokens helps you create a viable cost model.
>
> - **Usage optimization**: Start with [pay-as-you-go pricing](https://azure.microsoft.com/pricing/details/cognitive-services/openai-service/) for Azure OpenAI until your token utilization is predictable.
>
> - **Rate optimization**: When your token utilization is sufficiently high and predictable over a period of time, use the [provisioned throughput](/azure/ai-services/openai/concepts/provisioned-throughput) pricing model for better cost optimization.
>
> - **Usage optimization**: Take [model pricing](https://azure.microsoft.com/pricing/details/cognitive-services/openai-service/) and capabilities into account when choosing models. Start with less costly models for less complex tasks like text generation or completion tasks. For more complex tasks like language translation or content understanding, consider using more advanced models. Consider different [model capabilities](/azure/ai-services/openai/concepts/models) and maximum token usage limits when choosing a model appropriate for use cases like text embedding, image generation, or transcription scenarios. By carefully selecting the model that best fits your needs, you can optimize costs while still achieving desired application performance.
>
> - **Usage optimization**: Use the token-limiting constraints offered by the API calls, such as `max_tokens` and `n`, which indicate the number of completions to generate.
>
> - **Usage optimization**: Maximize OpenAI service price breakpoints like fine-tuning and model breakpoints like image generation. For example, because fine-tuning is charged per hour, use as much time as you have available per hour to improve fine-tuning results while avoiding slipping into the next billing period. Similarly, the cost for generating 100 images is the same as the cost for 1 image. Maximize price breakpoints to your advantage.
>
> - **Usage optimization**: Remove unused fine-tuned models when they're no longer being consumed to avoid incurring an ongoing hosting fee.
>
> - **Adjust usage**: Optimize prompt input and response length. Longer prompts raise costs by consuming more tokens. However, prompts missing sufficient context won't help the models yield good results. Create concise prompts that provide enough context for the model to generate a useful response. Also ensure that you optimize the limit of the response length.
>
> - **Cost efficiency**: Batch requests where possible to minimize per-call overhead, which can reduce overall costs. Ensure that you optimize batch size.
>
> - **Cost efficiency**: Because models have different fine-tuning costs, consider these costs if your solution requires fine-tuning.
>
> - **Monitor and optimize**: Set up a cost-tracking system that monitors model usage. Use that information to help inform model choices and prompt sizes.

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
| **Design client code to set limits**: Your custom clients should use the limit features of the [Azure OpenAI completions API](/azure/ai-services/openai/reference#completions), such as maximum limit on the number of tokens per model (`max_tokens`) or number of completions to generation (`n`). Setting limits ensures that the server doesn't produce more than the client needs. | Using API features to restrict usage aligns service consumption with client needs. This saves money by ensuring the model doesn't generate an overly long response that consumes more tokens than necessary. |
| **Monitor pay-as-you-go usage**: If you use the pay-as-you-go approach, [monitor usage](/azure/ai-services/openai/how-to/quota?tabs=rest#view-and-request-quota) of tokens per minute (TPM) and requests per minute (RPM). Use that information to inform architectural design decisions such as what models to use, and to optimize prompt sizes. | Continuously monitoring TPM and RPM gives you relevant metrics to optimize the cost of OpenAI models. You can couple this monitoring with model features and [model pricing](https://azure.microsoft.com/pricing/details/cognitive-services/openai-service/) to optimize model usage. You can also use this monitoring to optimize prompt sizes. |
| **Monitor provisioned throughput usage**: If you use [provisioned throughput](/azure/ai-services/openai/concepts/provisioned-throughput), monitor [provision-managed utilization](/azure/ai-services/openai/how-to/monitoring) to ensure you're not underutilizing the provisioned throughput you purchased. | Continuously monitoring provision-managed utilization gives you the information you need to understand if you're underutilizing your provisioned throughput. |
| **Cost management**: Follow the guidance on [using cost management features with OpenAI](/azure/ai-services/openai/how-to/manage-costs) to monitor costs, set budgets to manage costs, and create alerts to notify stakeholders of risks or anomalies.	| Cost monitoring, setting budgets, and setting alerts provides governance with the appropriate accountability processes. |

### Azure Policy and Azure Advisor

There are no [Azure Advisor](https://azure.microsoft.com/products/advisor) best-practice recommendations or [built-in Azure Policy definitions](/azure/machine-learning/policy-reference) for cost optimization for Azure OpenAI.

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices**, **observability**, and **release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals toward the workload's operational requirements.

### Design checklist

Start your design strategy based on the [**Design review checklist for Operational Excellence**](../operational-excellence/checklist.md). This checklist defines processes for observability, testing, and deployment related to Azure OpenAI.

> [!div class="checklist"]
>
> - **Azure DevOps culture**: Ensure deployment of Azure OpenAI Service instances across your various environments, such as development, test, and production. Ensure that you have environments to support continuous learning and experimentation throughout the development cycle.
>
> - **Observability**: Monitor, aggregate, and visualize appropriate metrics.
>
> - **Observability**: If Azure OpenAI diagnostics are insufficient for your needs, consider using a gateway like Azure API Management in front of Azure OpenAI to log both incoming prompts and outgoing responses where permitted. This information can help you understand the effectiveness of the model for incoming prompts.
>
> - **Deploy with confidence**: Use Infrastructure as Code (IaC) to deploy the Azure OpenAI Service, model deployments, and other infrastructure required for fine-tuning models.
>
> - **Deploy with confidence**: Follow [Large Language Operations](https://azure.microsoft.com/blog/the-new-ai-imperative-unlock-repeatable-value-for-your-organization-with-llmops/) (LLMOps) practices to operationalize management of your Azure OpenAI Service language models, including deployment, fine-tuning, and prompt engineering.
>
> - **Automate for efficiency**: If you use key-based authentication, implement an automated key-rotation strategy.

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
| **Enable and configure Azure Diagnostics**: Enable and configure Azure Diagnostics for the Azure OpenAI Service. | Azure Diagnostics collects and analyzes metrics and logs, helping you monitor the availability, performance, and operation of the Azure OpenAI Service. |

### Azure Policy and Azure Advisor

There are no [Azure Advisor](https://azure.microsoft.com/products/advisor) best-practice recommendations or [built-in Azure Policy definitions](/azure/machine-learning/policy-reference) for operational excellence for Azure OpenAI.

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [**Design review checklist for Performance Efficiency**](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for Azure OpenAI workloads.

> [!div class="checklist"]
>
> - **Capacity**: Estimate consumers' elasticity demands. Identify high-priority traffic that requires synchronous responses and low-priority traffic that can be asynchronous and batched.
>
> - **Capacity**: Benchmark token consumption requirements based on estimated demands from consumers. Consider using the [Azure OpenAI benchmarking tool](https://github.com/Azure/azure-openai-benchmark/) to help you validate the throughput if you're using provisioned throughput unit (PTU) deployments.
>
> - **Capacity**: Use provisioned throughput for production workloads. Provisioned throughput offers dedicated memory and compute, reserved capacity, and consistent maximum latency for the specified model version. The pay-as-you-go offering can suffer from "noisy neighbor" problems like increased latency and throttling in regions under heavy use. Also, the pay-as-you-go approach doesn't offer guaranteed capacity.
>
> - **Capacity**: Add the appropriate gateway(s) in front of your OpenAI deployments. Ensure that the gateway can route to multiple instances in the same or different regions.
>
> - **Capacity**: Allocate PTUs to cover your predicted usage, and complement these PTUs with a token-per-minute (TPM) deployment to handle elasticity above that limit. This approach combines base throughput with elastic throughput for efficiency. Like other considerations, this approach requires a custom gateway implementation to route requests to the TPM deployment when the PTU limits are reached.
>
> - **Capacity**: Send high-priority requests synchronously. Queue low-priority requests and send them through in batches when demand is low.
>
> - **Capacity**: Select a model that aligns with your performance requirements, considering the trade-off between speed and output complexity. Model performance can vary significantly based on the chosen model type. Models designed for speed offer faster response times, which can be beneficial for applications that require quick interactions. Conversely, more sophisticated models might deliver higher-quality outputs at the expense of increased response time.
>
> - **Achieve performance**: For applications like chatbots or conversational interfaces, consider implementing streaming. Streaming can enhance the perceived performance of Azure OpenAI applications by delivering responses to users in an incremental manner, improving the user experience.
>
> - **Achieve performance**: Follow the guidance in [When to use Azure OpenAI fine-tuning](/azure/ai-services/openai/concepts/fine-tuning-considerations) before committing to fine-tuning. Although there are good use cases for fine-tuning, such as when the information needed to steer the model is too long or complex to fit into the prompt, make sure that prompt engineering and Retrieval Augmented Generation (RAG) approaches won't work or are demonstrably more expensive.
>
> - **Achieve performance**: Consider using dedicated model deployments per consumer group to provide per-model usage isolation that can help prevent noisy neighbors between your consumer groups.

### Recommendations

There are no recommended configurations for Performance Efficiency for the Azure OpenAI service.

## Azure Policy and Azure Advisor

There are no [Azure Advisor](https://azure.microsoft.com/products/advisor) best-practice recommendations or [built-in Azure Policy definitions](/azure/machine-learning/policy-reference) for performance efficiency for Azure OpenAI.

## Next steps

The following resources demonstrate the recommendations highlighted in this article:

- Use this reference architecture as an example of how you can apply these recommendations to a workload such as the [Baseline OpenAI end-to-end chat reference architecture](/azure/architecture/ai-ml/architecture/baseline-openai-e2e-chat).
- Build implementation expertise using [Azure Machine Learning](/azure/ai-services/openai/) product documentation.
