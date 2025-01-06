---
title: Design principles for AI workloads on Azure
description: Review the design principles of Azure Well-Architected Framework. See how to apply these principles to an AI workload.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
---

# Design principles for AI workloads on Azure

This article outlines the core principles for AI workloads on Azure with a focus on the AI aspects of an architecture. It's essential to consider all Azure Well-Architected Framework pillars collectively, including their tradeoffs. Apply each pillar to the functional and nonfunctional requirements of the workload.

## Reliability

AI workloads running in Azure must consider many of the same reliability requirements as other classes of workloads. Specific considerations for model training, hosting, and inferencing are particularly important and are primarily addressed in this principle. The practices outlined here are in addition to standard design best practices for cloud applications and should be integrated accordingly.

Review the [reliability design principles](../reliability/principles.md) to get a foundational understanding of the concepts described here.

|Design principle|Considerations|
|---|---|
|Mitigate single points of failure.| Relying on a single instance for critical components can lead to significant issues. To prevent problems, build redundancy into all critical components. Use platforms with built-in fault tolerance and high-availability features. Deploy multiple instances or nodes to implement redundancy.|
|Conduct failure mode analysis. <br><br> Take advantage of the well-known design patterns.|Regularly analyze potential failure modes in your system components and build resilience against these failures. Use well-known design patterns to isolate parts of your system and prevent cascading failures. For example, the Bulkhead Pattern can help isolate failures. Retry mechanisms and circuit breakers can handle transient errors like throttling requests.|
| Balance reliability objectives across dependent components.| Ensure that the inferencing endpoint, or model, and the data store are aligned in terms of reliability. Application components must remain available even under unexpected conditions, such as a surge in concurrent users. The workload should also have the capability to fail over to backup systems in case of failures. If one component fails, it affects the reliability of the other. <br><br>Similarly, because serve layer APIs are critical production resources, they should adhere to the same reliability standards as other high-criticality workload flows. If these APIs require high availability, the hosting platform must support availability zones or a multiregion design to ensure continuous operation and resilience.|
|Design for operational reliability.| Maintain the reliability of model responses by ensuring that updates are frequent and timely. Decide whether to use recent data or if slightly older data is sufficient. Balance the need for up-to-date information with the practicality of update frequency. Automate online training because of its frequency and reliability requirements. Justify offline training through a cost-benefit analysis and perform it by using cheaper resources.|
|Design for a reliable user experience.|Use load testing to evaluate how your workload handles stress and design your user interface to manage user expectations regarding response times. Implement UI elements that inform users of potential wait times and adopt asynchronous cloud design principles to address intermittence, latency, or failures.|

## Security

Models often use sensitive production data to produce relevant results. So, you must use robust security measures at all layers of the architecture. Implement secure application design early in the lifecycle, encrypt data both at rest and in transit, and adhere to industry compliance standards. Regular security assessments are crucial for identifying and mitigating vulnerabilities. Advanced threat detection mechanisms ensure prompt responses to potential threats.

[Security principles](../security/principles.md) are fundamental for AI solutions to safeguard data integrity, design integrity, and user privacy. As a workload owner, you're responsible for building trust and protecting sensitive information to ensure a safe user experience.

|Design principle|Considerations|
|---|---|
|Earn user trust. |Integrate content safety at every stage of the lifecycle by using custom code, appropriate tools, and effective security controls. <br><br> Remove unneeded personal and confidential information across all data storage points, such as aggregated data stores, search indexes, caches, and applications. Maintain this level of security consistently throughout the architecture. <br><br> Make sure that thorough content moderation inspects data in both directions and filters out inappropriate and offensive content.|
|Protect data at rest, in transit, and in use according to the sensitivity requirements of the workload. |At minimum, use platform-level encryption with Microsoft-managed or customer-managed keys on all data stores that are used in the architecture. Evaluate where you need higher levels of encryption and use double encryption, if needed. <br><br> Ensure that all traffic uses HTTPS for encryption. Decide on the TLS termination points at each hop. <br><br> The model itself must be protected to prevent attackers from extracting sensitive information that's used during training. This protection requires the highest level of security measures. Consider using homomorphic encryption that allows inferencing on encrypted data.|
|Invest in robust access management in preserving identities (user and system) that access the system.|Implement role-based access control or attribute-based access control for both control and data planes. <br><br> Maintain proper identity segmentation to protect privacy. only allowing access to content they're authorized to view.|
|Protect the integrity of the design through segmentation.|Provide private networking for access to centralized repositories for container images, data, and code assets. <br><br> When sharing infrastructure with other workloads creates segmentation, for example, when hosting your inference server in Azure Kubernetes Service, isolate the node pool from other APIs or any other workloads. |
|Conduct security testing.|Develop a detailed test plan that includes tests to detect unethical behaviors whenever changes are introduced to the system. <br><br> Integrate AI components into your existing security testing. For instance, incorporate the inferencing endpoint into your routine tests alongside other public endpoints. <br><br> Consider conducting tests on the live system, such as penetration testing or red team exercises, to identify and address potential vulnerabilities effectively.|
|Reduce the attack surface by enforcing strict user access and API design. |Require authentication for all inferencing endpoints, including system-to-system calls. Avoid anonymous endpoints. <br><br>Prefer constrained API design at the cost of client-side flexibility.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Providing highest security has tradeoffs in cost and accuracy because the ability to analyze, inspect, or log the encrypted data is limited. Content safety checks and achieving explainability can also be challenging in highly secured environments.

The following design areas provide details on the preceding principles and considerations:

- [Responsible AI](./responsible-ai.md)
- [Testing](./test.md)
- [Operations](./operations.md)
- [MLOps and GenAIOps](./mlops-genaiops.md)

## Cost Optimization

The Cost Optimization pillar aims to maximize investment but not necessarily reduce costs. Every architectural choice carries both direct and indirect financial impacts. Understand the costs tied to various options, including build versus buy decisions, technology selections, billing models, licensing, training, and operational expenses. Maximize your investment in the selected tier and continuously reassess billing models. Continuously evaluate costs associated with changes in architecture, business needs, processes, and team structure.

Review the [Cost Optimization principles](../cost-optimization/principles.md) with a focus on rate and usage optimization.

|Design principle|Considerations|
|---|---|
|Determine the cost drivers by doing a comprehensive cost modeling exercise.|Consider the key factors of the data and application platform: <br> - **Volume of data**. Estimate the amount of data you'll be indexing and processing. Larger volumes can increase storage and processing costs. <br> - **Number of queries**. Predict the frequency and complexity of queries. Higher query volumes can drive up costs, especially if they require significant computational resources. <br> - **Throughput**. Assess the expected throughput, which affects the performance tier you need. Higher throughput demands can lead to higher costs. <br> - **Dependency costs**. There might be hidden costs in dependencies. For example, when you calculate the cost of an index, include the cost of the skill set, which is part of data processing but outside the index scope. |
|Pay for what you intend to use. | Choose a technology solution that meets your needs without incurring unnecessary expenses. If advanced features aren't required, consider more economical options and open-source tools. <br><br>Factor in the frequency of usage. Prefer elastic compute options for orchestration tools to minimize utilization costs because they're *always on*. Avoid serverless compute for full-time operations because of potential cost escalation. <br><br> Don't pay for higher tiers without making use of their full capacity. Ensure that your chosen tier aligns with your production usage patterns to optimize spending. Use standard pricing for regular tasks and spot pricing for highly interruptible training. Use GPU-based compute only for AI workload functions to reduce costs. <br><br>Extensively test and benchmark your training and fine-tuning to find the ideal product version that balances performance and cost.|
|Use what you pay for. Minimize waste.| Monitor utilization metrics closely. AI workloads can be expensive, and costs can escalate quickly if resources aren't shut down, scaled down, or deallocated when not in use. <br><br> Optimize the system for _write once, read many_ to avoid overspending on data storage. Training data doesn't need the instant responsiveness of a production database. <br><br> Stress testing an Azure OpenAI Service inference endpoint can be expensive because of charges for every call. To reduce costs, use unused PTUs of Azure OpenAI in a test environment or simulate the inference endpoint instead. <br><br> Activities like exploratory data analysis (EDA), model training, and fine-tuning are typically not run full time. Prefer a platform that you can stop when it's not in use. For example, EDA jobs are typically interactive, so users need to be able to start VMs and stop them as they run jobs. <br><br> Set cost accountabilities on the operations teams. They should ensure that costs stay within expected parameters by actively monitoring and managing resource utilization.|
|Optimize operational costs.|Online training can be costly because of its frequent nature. Automating this process helps to maintain consistency and minimizes costs from human errors. Use slightly older data for training and delaying updates, when possible, to further reduce expenses without significantly affecting model accuracy. <br><br> For offline training, evaluate if you can use cheaper resources, such as offline hardware.<br><br>In general, delete data from feature stores to reduce clutter and storage costs for features.|

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Cost optimization and performance efficiency.** Balancing cost with performance in database management involves considering tradeoffs. Efficient index design speeds up queries but can increase costs because of metadata management and index size. Similarly, partitioning large tables can improve query performance and reduce storage load but incurs extra costs. Conversely, techniques that avoid excessive indexing can reduce costs but might affect performance if not managed properly.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Cost optimization and operational excellence.** There are tradeoffs in the two primary approaches for model training. Training in the development environment with full production data can lower computational costs by training the model once and promoting only the artifact.   Stringent security measures for handling production data in lower environments are required. The process can be complex and resource intensive. Conversely, training the model in each environment enhances stability and reliability through thorough code review and testing, but computational costs increase because of multiple training runs.

## Operational Excellence

The primary objective of operational excellence is to deliver capabilities efficiently throughout the development lifecycle. You must establish repeatable processes that support the design methodology of experimentation and yield results to improve model performance. Operational excellence is also about observing sustained accuracy of models over time, implementing effective monitoring practices and governance to minimize risks, and developing change management processes to adapt to model drifts.

Although all [Operational Excellence principles](../operational-excellence/principles.md) apply to AI workloads, prioritize automation and monitoring as your foundational operational strategies.

|Design principle|Considerations|
|---|---|
| Foster a continuous learning and experimentation mindset throughout the lifecycles of application development, data handling, and AI model management.| Workload operations should build on industry-proven methodologies like DevOps, DataOps, MLOps, and GenAIOps. <br><br>Early collaboration between operations, application development, and data teams is essential to establish a mutual understanding of acceptable model performance. Operations teams provide quality signals and actionable alerts. Application and data teams help to diagnose and resolve issues efficiently.|
|Choose technologies that minimize operational burden.|When you choose platform solutions, prefer platform as a service (PaaS) over self-hosted options to simplify design, automate workflow orchestration, and make Day-2 operations easier.|
| Build an automated monitoring system that supports alerting functionality and includes logging and auditability. | Because of the nondeterministic nature of AI, quality measurements are important to establish early in the lifecycle. Work with data scientists to define quality metrics and visualize ongoing insights in comprehensive dashboards. <br><br> Track experiments through tooling that can capture details like code versions, environments, parameters, runs, and results. <br><br> Implement actionable alerts with just enough information for the operators to respond quickly.|
| Automate the detection and mitigation of model decay. | Use automated tests to evaluate drift over time. Ensure that your monitoring system sends alerts when responses start deviating from the expected results and start doing so on a regular basis. Use tools that can automatically discover and update new models. <br><br>Adapt to new use cases by adjusting data processing and model training logic, as needed. |
| Practice safe deployments.|Choose between side-by-side deployments or in-place updates to minimize downtime. Implement thorough testing before deployment to ensure that the model is correctly configured and meets targets, user expectations, and quality standards. Always plan for emergency operations, regardless of the deployment strategy.|
| Continuously evaluate the user experience in production.| Enable the workload users to provide feedback about their experience and get consent to share parts or all of their conversation in an associated log for troubleshooting. Consider how much of a user interaction is viable, compliant, safe, and useful to capture. Use the data diligently to evaluate the performance of your workload with real user interactions.|

The following design areas provide details on the preceding principles and considerations:

- [Operations](./operations.md)
- [MLOps and GenAIOps](./mlops-genaiops.md)
- [Testing](./test.md)

## Performance Efficiency

The goal of AI model performance evaluation is to determine how effectively a model accomplishes its intended tasks. Evaluation involves assessing various metrics such as accuracy, precision, and fairness. The performance of the platform and application components that support the model is crucial.

Model performance is also influenced by the efficiency of operations like experiment tracking and data processing. Apply [Performance Efficiency principles](../performance-efficiency/principles.md) to help measure performance against an acceptable quality bar, which is key to detecting degradation or decay. To maintain the workload, including AI components, automated processes for continuous monitoring and evaluation are necessary.

|Design principle|Considerations|
|---|---|
| Establish performance benchmarks. | Conduct rigorous performance testing across different architectural areas and set acceptable targets. This ongoing assessment should be part of your operational processes, not just a one-time test. <br><br> Benchmarking applies to prediction performance. Start with a baseline to understand initial model performance and continuously reevaluate to ensure that it meets expectations. |
| Evaluate the resource needs to meet performance targets.|Understand the load characteristics to choose the right platform and right-size resources. Factor in this data for capacity planning to operate at scale. <br><br>For example, use load testing to determine the optimal compute platform and product version. High-performance GPU-optimized compute is often needed for model training and fine-tuning. General-purpose product versions are suitable for orchestration tools. <br><br> Similarly, choose a data platform that efficiently handles data ingestion, manages concurrent writes, and maintains individual write performance without degradation. <br><br> Different inferencing servers have various performance characteristics that affect model performance at runtime. Select a server that meets baseline expectations.|
| Collect and analyze performance metrics and identify bottlenecks. <br><br> |Evaluate telemetry from the data pipeline to ensure that performance targets for throughput and read/write operations are met. <br> For the search index, consider metrics such as the query latency, throughput, and the accuracy of the results. As data volume increases, error rates shouldn't rise. <br><br> Analyze telemetry from the code components, such as the orchestrator, which collects data from service calls. Analyze that data to help you understand time spent on local processing versus network calls and identify potential latency in other components. <br><br>Assess UI experience by using engagement metrics to determine if users are positively engaged or frustrated. Increased time on a page can indicate either one. Multimodal capabilities, like voice or video responses, can experience significant latency, which leads to longer response times.|
| Continuously improve benchmark performance by using quality measurements from production.|Automated collection and analysis of performance metrics, alerts, and model retraining are needed to maintain model efficacy. |

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** When you consider platform capabilities, make sure to balance cost and performance. For example, GPU versions are expensive, so continuously monitor for underutilization and right-size resources as needed. After adjustments, test resource utilization to ensure a balance between cost of platform resources and their operations and the expected performance according to the baseline. For cost-optimization strategies, see [Recommendations for optimizing component costs](../cost-optimization/optimize-component-costs.md).

The following design areas provide details on the preceding principles and considerations:

- [Testing](./test.md)
- [MLOps and GenAIOps](./mlops-genaiops.md)

## Next step

> [!div class="nextstepaction"]
> [Design area: Application design](./application-design.md)
