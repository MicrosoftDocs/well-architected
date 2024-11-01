---
title: Design Principles for AI Workloads on Azure
description: Review the design principles of the Azure Well-Architected Framework. See how to apply these principles to an AI workload.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
---

# Design principles for AI workloads on Azure

This article outlines the core principles for AI workloads on Azure with a focus on the AI aspects of an architecture. It's essential to consider all Well-Architected Framework pillars collectively, including their tradeoffs. Apply each pillar to the functional and nonfunctional requirements of the workload.

## Reliability

AI workloads running in Azure must consider many of the same reliability requirements as other classes of workloads. However, specific considerations for model training, hosting, and inferencing are particularly important and are primarily addressed in this principle. It's important to note that the practices outlined here are in addition to standard good design practices for cloud applications and should be integrated accordingly.

Review the [Reliability design principles](../reliability/principles.md) to get a foundational understanding of the concepts described here.

|Design principle|Considerations|
|---|---|
|Mitigate single points of failure.| Relying on a single instance for critical components can lead to significant issues. To prevent this, build redundancy into all critical components. Use platforms with built-in fault tolerance and high availability features, and implement redundancy by deploying multiple instances or nodes.|
|Conduct failure mode analysis. <br><br> Take advantage of the well-known design patterns.|Regularly analyze potential failure modes in your system components and build resilience against these failures. Utilize well-known design patterns to isolate parts of your system and prevent cascading failures. For example, the Bulkhead Pattern can help isolate failures, while retry mechanisms and circuit breakers can handle transient errors like throttling requests. In the data layer, the Cache-Aside pattern can be used to precalculate predictions, enabling faster lookups and reducing the burden on the model. |
| Balance reliability objectives across dependent components.|  Ensure that the inferencing endpoint (or model) and the data store are aligned in terms of reliability. Application components must remain available even under unexpected conditions, such as a surge in concurrent users. Additionally, the workload should have the capability to fail over to backup systems in case of failures. If one component fails, it impacts the reliability of the other. <br><br>Similarly, serve layer APIs, being critical production resources, should adhere to the same reliability standards as other high-criticality workload flows. If these APIs require high availability, the hosting platform must support availability zones or a multiregion design to ensure continuous operation and resilience.|
|Design for operational reliability.| Maintain the reliability of model responses by ensuring updates are frequent and timely. Decide whether to use recent data or if slightly older data is sufficient, balancing the need for up-to-date information with the practicality of update frequency. Online training should be automated due to its frequency and reliability requirements, while offline training can be justified through a cost-benefit analysis and performed using cheaper resources.|
|Design for a reliable user experience.|Use load testing to evaluate how your workload handles stress and design your user interface to manage user expectations regarding response times. Implement UI elements that inform users of potential wait times and adopt asynchronous cloud design principles to address intermittence, latency, or failures.|

## Security

Models often use sensitive production data to produce relevant results. So, robust security measures must be used at all layers of the architecture. This includes implementing secure application design early in the lifecycle, encrypting data both at rest and in transit, and adhering to industry compliance standards. Regular security assessments are crucial for identifying and mitigating vulnerabilities, while advanced threat detection mechanisms ensure prompt responses to potential threats. 

[Security principles](../security/principles.md) are fundamental for AI solutions, safeguarding data integrity, design integrity, and user privacy. As a workload owner, you're responsible for building trust and protecting sensitive information, ensuring a safe user experience.

|Design principle|Considerations|
|---|---|
|Earn user trust. |Integrate content safety at every stage of the lifecycle using custom code, appropriate tools, and effective security controls. <br><br> Remove unneeded personal and confidential information across all data storage points, including aggregated data stores, search indexes, caches, and applications. Maintain this level of security consistently throughout the architecture. <br><br> Make sure there's thorough content moderation that inspects data in both directions and filters out inappropriate and offensive content.|
|Protect data at rest, in transit, and in use according to the sensitivity requirements of the workload. |At minimum use platform-level encryption with Microsoft-managed or customer-managed keys on all data stores used in the architecture. Evaluate where you need higher levels of encryption and use double encryption, if needed. <br><br> Ensure all traffic uses HTTPS for encryption. Decide on the TLS termination points at each hop. <br><br> The model itself needs to be protected to prevent attackers from extracting sensitive information used during training. This requires the highest level of security measures. Consider using homomorphic encryption that allows inferencing on encrypted data.|
|Invest in robust access management in preserving identities (user and system) that access the system.|Implement Role-Based Access Control (RBAC) and, or Attribute-Based Access Control (ABAC), for both control and data planes. <br><br> Maintain proper identity segmentation to protect privacy, only allowing access to content they're authorized to view.|
|Protect the integrity of the design through segmentation.|Provide private networking for access to centralized repositories for container images, data, and code assets. <br><br> When sharing infrastructure with other workloads create segmentation. For example, when hosting your inference server in AKS, node pool should be isolated from other APIs or any other workloads. |
|Conduct security testing.|Develop a detailed test plan that includes tests to detect unethical behaviors whenever changes are introduced to the system. <br><br> Integrate AI components into your existing security testing. For instance, incorporate the inferencing endpoint into your routine tests alongside other public endpoints. <br><br>  Consider conducting tests on the live system, such as penetration testing or red team exercises, to identify and address potential vulnerabilities effectively.|
|Reduce the attack surface by enforcing strict user access and API design. |Require authentication for all inferencing endpoints, including system-to-system calls. Avoid anonymous endpoints. <br><br>Prefer constrained API design at the cost of client-side flexibility.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Providing highest security has trade-offs in cost and accuracy because the ability to analyze, inspect, or log the encrypted data is limited. Content safety checks and achieving explainability can also be challenging in highly secured environments.

These design areas provide details on the preceding principles and considerations:

- [Responsible AI](./responsible-ai.md)
- [Testing](./testing.md)
- [Operations](./operations.md)
- [MLOps and GenAIOps](./mlops-genaiops.md)

## Cost Optimization

The Cost Optimization pillar aims to maximize investment, not necessarily reduce costs. Every architectural choice carries both direct and indirect financial impacts. Understand the costs tied to various options, including build versus buy decisions, technology selections, billing models, licensing, training, and operational expenses. It's important to maximize your investment in the selected tier and continuously reassess billing models. Continuous evaluate cost associated with changes in architecture, business needs, processes, and team structure. 

Review all the [Cost Optimization principles](../cost-optimization/principles.md) with focus on rate and usage optimization. 

|Design principle|Considerations|
|---|---|
|Determine the cost drivers by doing a comprehensive cost modeling exercise.|Consider the key factors of the data and application platform: <br> - **Volume of data**. Estimate the amount of data you'll be indexing and processing. Larger volumes can increase storage and processing costs. <br> - **Number of queries**. Predict the frequency and complexity of queries. Higher query volumes can drive up costs, especially if they require significant computational resources. <br> - **Throughput**. Assess the expected throughput, which impacts the performance tier you need. Higher throughput demands can lead to higher costs. <br> - **Dependency costs**. There might be hidden costs in dependencies. For example, when calculating the cost of index, include cost of skill set, which is part of data processing but outside the index scope. |
|Pay for what you intend to use. | Choose a technology solution that meets your needs without incurring unnecessary expenses. If advanced features aren't required, consider more economical options and open-source tools. <br><br>Factor in the frequency of usage. Prefer elastic compute options for orchestration tools to minimize utilization costs, as they are _always-on_. Avoid serverless compute for full-time operations due to potential cost escalation. <br><br> Don't pay for higher tiers without utilizing their full capacity. Ensure your chosen tier aligns with your production usage patterns to optimize spending. Use standard pricing for regular tasks and spot pricing for highly interruptible training.  Use GPU-based compute only for AI workload functions to reduce costs.  <br><br>Extensively test and benchmark your training and fine-tuning to find the ideal SKU that balances performance and cost.|
|Use what you pay for. Minimize wastage.| Monitor utilization metrics closely. AI workloads can be expensive, and costs can escalate quickly if resources aren't shut down, scaled down, or deallocated when not in use. <br><br> Optimize the system for _write once, read many_ to avoid overspending on data storage. Training data doesn't need the instant responsiveness of a production database. <br><br> Stress testing an Azure OpenAI inference endpoint can be expensive due to charges for every call. To reduce costs, use unused PTUs of OpenAI in a test environment or simulate the inference endpoint instead. <br><br> Activities like, exploratory data analysis (EDA),  model training and fine-tuning are typically not run full time, prefer a platform that can be stopped when it's not in use. For example, EDA jobs are typically interactive, so users need to be able to start VMs and stop them as they run jobs. <br><br> Set cost accountabilities on the operations teams, who should ensure costs stay within expected parameters by actively monitoring and managing resource utilization.|
|Optimize operational costs.|Online training can be costly due to its frequent nature. Automating this process helps maintain consistency and minimizes costs from human errors. Additionally, using slightly older data for training and delaying updates, when possible, can further reduce expenses without significantly affecting model accuracy. <br><br> For offline training, evaluate if cheaper resources can be used, such as offline hardware.<br><br>In general, delete data from feature stores to reduce clutter and storage costs for features.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Cost Optimization and Performance Efficiency.** Balancing cost with performance in database management involves considering tradeoffs. Efficient index design speeds up queries but can increase costs due to metadata management and index size. Similarly, partitioning large tables can improve query performance and reduce storage load but incurs additional costs. Conversely, techniques that avoid excessive indexing, can reduce costs but may impact performance if not managed properly.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Cost Optimization and Operational Excellence.**  There are tradeoffs in the two primary approaches for model training. Training in the development environment with full production data can lower computational costs by training the model once and promoting only the artifact, but it requires stringent security measures for handling production data in lower environments, which can be complex and resource-intensive. Conversely, training the model in each environment enhances stability and reliability through thorough code review and testing, but it increases computational costs due to multiple training runs.

## Operational Excellence

The primary objective is to deliver capabilities efficiently throughout the development lifecycle. This involves establishing repeatable processes that support the design methodology of experimentation and yield results to improve model performance. Operational excellence is also about sustained accuracy of models over time, implementing effective monitoring practices and governance to minimize risks, and developing change management processes to adapt to model drifts.

While all [Operational Excellence principles](../operational-excellence/principles.md) apply to AI workloads, prioritize automation and monitoring as your foundational operational strategies. 

|Design principle|Considerations|
|---|---|
| Foster a continuous learning and experimentation mindset throughout the lifecycles of application development, data handling, and AI model management.| Workload operations should build on industry-proven methodologies like DevOps, DataOps, MLOps, and GenAIOps. <br><br>Early collaboration between operations, application development, and data teams is essential to establish a mutual understanding of acceptable model performance. Operations teams provide quality signals and actionable alerts, while application and data teams assist in diagnosing and resolving issues efficiently.|
|Choose technologies that minimize operational burden.|When choosing platform solutions, prefer  PaaS over self-hosted options to simplify design, automate workflow orchestration, and make Day-2 operations easier.|
| Build an automated monitoring system that supports alerting functionality, including logging and auditability. | Given the non-deterministic nature of AI, quality measurements are important to establish early in the lifecycle. Work with data scientists to define quality metrics, visualize ongoing insights in comprehensive dashboards. <br><br> Track experiments through tooling that can capture details like code versions, environments, parameters, runs, and results. <br><br> Implement actionable alerts with just enough information for the operators to respond quickly.|
| Automate the detection and mitigation of model decay. | Use automated tests to evaluate drift over time. Ensure your monitoring system sends alerts when responses start deviating from the expected results and start doing so on a regular basis. Use tools that can automatically discover and update new models. <br><br>Adapt to new use cases by adjusting data processing and model training logic, as needed. |
| Practice safe deployments.|Choose between side-by-side deployments or in-place updates to minimize downtime. Implement thorough testing before deployment to ensure the model is correctly configured and meets targets, user expectations, and quality standards. Always plan for emergency operations, regardless of the deployment strategy.|
| Continuously evaluate the user experience in production| Enable the workload users to provide feedback about their experience and get consent to share parts or all of their conversation an associated log for troubleshooting. Consider how much of a user interaction is viable, compliant, safe, and useful to capture and use the data diligently to evaluate the performance of your workload with real user interactions.|


> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Operational excellence and the cost of complexity.** 

These design areas provide details on the preceding principles and considerations:

- [Operations](./operations.md)
- [MLOps and GenAIOps](./mlops-genaiops.md)
- [Testing](./testing.md)

## Performance Efficiency

The goal of AI model performance evaluation is to determine how effectively a model accomplishes its intended tasks. This involves assessing various metrics such as accuracy, precision, and fairness. Additionally, the performance of the platform and application components that support the model is crucial.

Model performance also influenced by the efficiency of operations like experiment tracking and data processing. Applying [Performance Efficiency principles](../performance-efficiency/principles.md) helps measure performance against an acceptable quality bar, which is key to detecting degradation or decay. To maintain the workload, including AI components, automated processes for continuous monitoring and evaluation are necessary.

|Design principle|Considerations|
|---|---|
| Establish performance benchmarks. | Conduct rigorous performance testing across different architectural areas and set acceptable targets. This ongoing assessment should be part of your operational processes, not just a one-time test. <br><br> Benchmarking applies to prediction performance. Start with a baseline to understand initial model performance and continuously re-evaluate to ensure it meets expectations.  |
| Evaluate the resource needs to meet performance targets.|Understand the load characteristics to choose the right platform and right-size resources. Factor in this data for capacity planning to operate at-scale. <br><br>For example, use load testing to determine the optimal compute platform and SKU. High-performance GPU-optimized compute is often needed for model training and fine-tuning, while general-purpose SKUs are suitable for orchestration tools. <br><br> Similarly, choose a data platform that efficiently handles data ingestion, manages concurrent writes, and maintains individual write performance without degradation. <br><br> Different inferencing servers have varying performance characteristics, impacting model performance at run-time. Select a server that meets baseline expectations.|
| Collect and analyze performance metrics and identify bottlenecks. <br><br> |Evaluate telemetry from the data pipeline to ensure performance targets for throughput and read/write operations are met.  <br> For the search index, consider metrics such as the query latency, throughput, the accuracy of the results. As data volume increases, error rates shouldn't rise.  <br><br> Analyze telemetry from the code components, such as the orchestrator, which collects data from service calls. Analyzing that data can help you understand time spent on local processing versus network calls and identify potential latency in other components. <br><br>Assess UI experience using engagement metrics to determine if users are positively engaged or frustrated. Increased time on a page can indicate either. Multimodal capabilities, like voice or video responses, can experience significant latency, leading to longer response times.|
| Continuously improve benchmark performance using quality measurements from production.|Automated collection and analysis of performance metrics, alerts, and model retraining are needed to maintain model efficacy. |

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**. When considering platform capabilities, it's important to balance cost and performance. For example, GPU SKUs are expensive, so continuously monitor for underutilization and right-size resources as needed. After adjustments, test resource utilization to ensure a balance between cost of platform resources and their operations and the expected performance as per the baseline. For Cost Optimization strategies, see [Recommendations for optimizing component costs](../cost-optimization/optimize-component-costs.md).

These design areas provide details on the preceding principles and considerations:

- [Testing](./testing.md)

## Next step

> [!div class="nextstepaction"]
> [Design area: Application design](./application-design.md)
