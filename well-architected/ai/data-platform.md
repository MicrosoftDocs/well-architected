---
title: Data Platform for AI Workloads on Azure
description: Learn about functional and nonfunctional data source considerations for your data platform for AI workloads on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/11/2025
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# Data platform for AI workloads on Azure

Choosing a data platform involves understanding the unique data challenges that these solutions bring. GenAI models, especially those built on foundation models, depend on diverse, high-quality data, fast access to embeddings, and scalable storage that supports vector search. The goal is to meet these needs without adding unnecessary complexity to your architecture. Because GenAI models depend on large, diverse, and high-quality datasets, understanding the principles of effective data pipeline design is essential before evaluating platform options.

When evaluating platform choices, start by asking whether you truly need additional components. Simpler architectures are often faster to deploy, easier to manage, and more cost-effective. Ask yourself:

- Can the model achieve its expected performance using data from a single source?
- Does the chosen data store already provide the analytics or search capabilities you need?
- Is the source data already structured and indexed for AI or vector search?

If the answer is yes to most of these questions, a complex architecture might not be necessary. For example, modern databases such as Azure Cosmos DB and Azure SQL Database already support native vector data types and vector search. These capabilities can reduce the need for separate indexing or specialized vector databases, minimizing data movement while improving performance.

As your workload grows and data comes from multiple sources, the platform decision becomes more complex. You may need to consider solutions that support ETL or ELT pipelines, specialized search indexes, and scalable storage for large datasets. Each added capability should serve a clear purpose rather than simply expanding the technology stack.

This article provides guidance on choosing a data platform for workloads where data needs to be stored, processed, or analyzed. The focus is on solutions that support generative AI (GenAI), particularly scenarios built on foundation models. We highly recommend that you understand the principles of good data pipeline design before you explore the technological capabilities that this article describes. For more information, see [Grounding data design](./grounding-data-design.md).

For recommendations specific to model training and fine-tuning, see [Training data platform considerations](#training-data-platform-considerations).

## Considerations for data storage platform

In AI workloads, data usually moves through several stages of storage and processing, guided by pipelines that connect each step. One important stage is the data store that holds information collected and combined from multiple sources. This store allows you to process and refine the data until it's ready for the next stage. 

> [!NOTE]
> You might not need this component in your architecture. In some cases, you could access data directly from the source systems. However, doing so can lead to performance issues and may overload those systems with AI queries. It can also cause access or reliability challenges. To prevent these issues, it's usually better to copy the data into a dedicated store for aggregation and processing.

When you choose a platform for this store, make sure it follows the same security standards as your source systems, is cost-effective, and works well with ETL, ELT, or EL processing tasks. Your options can range from simple storage solutions to large-scale data platforms, depending on your data volume and performance needs. Look for a storage option that's reliable, scalable, and provides good value for your workload.

Here are a few questions to help guide your choice of data store technology.

- **Can the platform handle different data formats?**

  Your data store should be able to store a variety of data formats and, when needed, convert data between them.

  For example, if your ingestion pipeline brings in data from both a relational database and a Parquet file, it must support structured and semi-structured data. You might want to convert relational data into Parquet format to align with schema definitions. The platform should provide built-in tools for this kind of transformation so that you do not need to write custom code.

- **Do you expect to store multiple versions of the data?**

  Data changes over time in both values and structure, and source systems usually store only the current state. If you need historical context, choose a data platform that supports versioning. Without it, you might have to duplicate datasets, which adds complexity.

  Versioning has other benefits. In some cases, you may need separate copies of data for different use cases. Each copy can evolve independently, and the platform should manage versioning across all copies to preserve context for your AI models.

- **Does the platform have built-in data lifecycle management capabilities?**

  Data lifecycle management (DLM) helps control growth from creation to deletion. Your platform should automatically remove intermediate copies, manage archived data, and support regulatory retention when needed. Without that, data can grow uncontrollably and that unnecessary volume can make processing difficult. For instance, you might need to rerun preprocessing steps several times to improve data quality. The platform should automatically remove intermediate copies when they are no longer needed.

  In other cases, you may need to retain data for compliance or audits. Look for storage options that support cold or archived tiers for rarely accessed data at lower cost.

- **Does the platform support data governance features?**

  Auditability is an important aspect for AI workloads. Your platform should maintain audit trails to track data access, ensure privacy, and document data origins. It should also support a data dictionary or catalog that manages metadata, data types, purpose, and lineage, especially when data comes from multiple sources.

- **How much data do you expect to store?**

  AI workloads generate large volumes of data, which can grow further with multiple versions and extra metadata. Your data platform should scale efficiently for both storage and throughput, handling high ingestion rates, concurrent writes, and intensive processing without performance degradation.

  When choosing a platform, consider the entire workflow, as ingestion and processing often happen at the same time. The system should support parallel processing and frequent data movement, and provide telemetry to give clear insight into read and write performance.

- **Is this data store critical to your workload's reliability?**
  
  Choose a platform that supports reliability and scalability through replication or multiple instances. Many big data stores use controllers that distribute processing automatically and provide failover when an instance becomes unavailable.

  Data also needs to be both durable and accessible. Make sure the platform guarantees data integrity, provides accessible APIs, and supports backup or restore capabilities if rebuilding data from scratch would be costly.

- **Do you have any cost constraints?**

  Once reliability and performance requirements are met, consider how to optimize cost. For many AI workloads, a write once, read many pattern is sufficient and helps control expenses. Grounding data should be cost-effective to store and retrieve, even if it does not require the same level of responsiveness as a production database. The goal is to balance cost, efficiency, and performance.

### Technology options

If these needs sound familiar, a **data lake** might be a good fit. Data lakes offer DLM, support quality tiers, handle diverse file formats, and include strong observability features. If your workload already uses a data lake, you can likely extend it for your AI scenarios.

Alternatively, options such as **Azure Blob Storage** can provide many of these benefits, including lifecycle management, monitoring, and high transaction rates, while keeping costs under control.


## Considerations for data processing platform

The data processing platform plays a key role in preparing and transforming data so it's ready for downstream use, whether that's RAG indexing, analytics, or other use case. 

> [!NOTE] 
> For AI workloads, especially generative AI and retrieval-augmented generation (RAG), it's useful to understand the difference between ETL, ELT, and EL processes.
>  - ETL: Extract, transform, then load, typical for traditional data warehousing.
>  - ELT: Extract, load, then transform, common for data lakes and big data tools like PySpark.
>  - EL: Extract and load, used in RAG scenarios where you store documents first, then perform transformations like text chunking or image extraction later.

There are two places where processing can happen:

- Ingestion Layer. The ingestion pipeline collects data from various sources and moves it into your aggregate data store. Along the way, it often performs basic preprocessing or formatting so the data is queryable. To reduce the need for custom code, it's best to use a data platform that handles as much of this as possible. When evaluating tools, consider the ETL or ELT features required to support your AI workloads, such as model augmentation.

- Processing Layer. After data lands in the aggregate store, it typically needs deeper processing before it's ready for indexing or use in AI models. These pipelines should offer similar levels of reliability and scalability as your ingestion layer, but the focus shifts to transforming and reshaping the data.

  Typical tasks include:

  - Entity recognition and enrichment
  - Integrating additional data sources
  - Performing lookups and transformations
  - Cleaning or deleting irrelevant data

A strong data platform helps automate and orchestrate these operations efficiently.

- **What's the support for connecting to data sources?**

  The platform should easily connect to data sources you expect ingest from, whether that's relational databases, big data sources, or blob storage.

  Look for prebuilt connectors and low-code integrations. Ideally, you want drag-and-drop or configuration-based connectors that support lookups, data copying, and governance.

- **Can the platform process various data formats?**
  
  Data comes in many shapes: structured (SQL, JSON), unstructured (documents, images), and streaming (IoT data). Choose a platform that can handle the formats your use case requires considering immediate and longer term requirements.

- **Does the platform offer features for data preparation and rescoping?**

  Before your data is ready for indexing or model consumption, it needs to be cleaned, enriched, and reshaped. Your data design strategies should explicitly outline the requirements. A good platform should:

  - Remove duplicates and fill missing values
  - Handle case normalization and other basic cleanup tasks
  - Support advanced transformations like chunking, enrichment, and document analysis

  If your data store supports these operations natively, you can process data in place without moving it. Otherwise, use external tools like Azure Databricks or Azure Data Factory for heavy transformations.

  In some cases, you may choose to externalize part of this responsibility to the data consumer. A common example of this approach is RAG implementation. During processing, documents are divided into smaller chunks, with each chunk stored as a separate row in the index. These chunks are then paired with embeddings, often generated through an OpenAI service. In AI search scenarios, this entire process, from chunking to embedding,is orchestrated within the indexing workflow, whether through OpenAI or Azure AI Search.

  
- **Is there a built-in orchestrator for managing workflows?**

  Data processing typically happens as modular jobs that need complex coordination. Your platform should include an orchestrator to define, schedule, and monitor these workflows. Look for:

  - Support for job dependencies and checks that validate sequence of exectution
  - Flexible modification of workflows that allows for easy adjustments without rewriting large portions of code.
  - Monitoring and logging capabilities

  Popular tools include Azure Data Factory for its rich feature set for workflow management, or Azure Databricks for more complex orchestration. If cost is a concern, Apache NiFi or Airflow can be more economical alternatives.
  
- **How much data do you expect to ingest?**

  Estimate how much data you'll ingest and the frequency of ingestion. For example, if you expect to load 10 terabytes of data daily into an index, the platform should support strong parallelization and distributed execution. For smaller workloads, simpler tools like Logic Apps might work, but for higher volumes, Data Factory or Databricks are more suitable. For scalability and throughput, consider:

  - Data volume and frequency
  - Tolerable latency requirements
  - Job complexity
  
  For instance, data cleansing involves validating and potentially replacing invalid fields or masking sensitive information. These tasks, though basic, require significant resources because each row is processed individually, which adds to the overall time.

- **What monitoring capabilities do you need?**

  Data processing pipelines should have monitoring capabilities and provide insights into the pipeline's performance and status of jobs. Your platform should provide:

  - Job progress tracking
  - Logs, metrics, and alerts to understand pipeline behavior
  - Integration with your broader monitoring stack

  Identify any gaps in the built-in telemetry, and determine what additional monitoring you need to implement. This monitoring might involve adding custom logging or metrics to capture specific details about the job steps.
  
- **How much reliability do you expect of the data processing platform?**

  Choose a platform that minimizes single points of failure and supports retries for failed tasks. For example, Data Factory on Azure Kubernetes Service (AKS) typically offers stronger reliability than Logic Apps.
  
  If your data updates infrequently and you handle processing thorugh weekly batch processing, occasional failures may be acceptable. But for real-time AI scenarios, you'll need higher reliability.


- **Are there any cost constraints?**

  The goal is to avoid over-engineering and pick a platform that fits your needs today while leaving room to scale. For example, if you don't need Databricks' advanced features, Data Factory might offer a more affordable option. Open-source tools like Airflow or NiFi can further reduce costs.

- **What are the security requirements on the workflows and on the data that you process?**

  Be clear about the security, privacy, and data residency requirements. For example, consider any geographic regulatory requirements. Comply with data residency requirements by ensuring that data is stored and processed within specific regions. You might need to run separate pipelines for different regions, such as one for Europe and another for America, to meet local compliance regulations.

  The data pipeline platform should support identity and access management to ensure that only authorized identities have access to specific jobs or steps within workflows. For instance, if your ETL process consists of several workflows, and one of them handles highly confidential data, the platform should allow you to restrict access to that workflow while keeping the others accessible. This capability helps you meet the security requirements without needing separate platforms for different data sensitivity levels. Ideally, the platform should provide built-in support for such isolation that enables efficient and secure data management.

Data processing pipelines can output the data to either a search index. Depending on the use case, see the sections on [search indexes](#considerations-for-a-search-index).

## Considerations for a search index

The search index is designed to store contextual or grounding data to send to the model inference endpoint, along with the prompt. Both calls, the index query and the inference endpoint invocation, take place in the context of servicing the same client HTTP requests. Unlike ETL processes that handle offline and batch jobs, this index supports real-time inferencing, which requires high performance and reliability. It's specialized for AI queries and offers features like keyword indexing and filtering, which aren't typical of big data stores. The goal is to have a high-performing, _write-once, read-many_ data store that supports impromptu and fuzzy queries. This data store can provide relevant results without precise queries.

- **What types of search does the search index support?**

  Queries that the system receives are essentially searches, and the index needs to support rich search capabilities. For RAG, vector search is nonnegotiable because data is stored as calculated vectors, or embeddings, which are used for searching.
  
  Vector search is powerful, and combining it with filtering and full-text search enhances the search index's effectiveness. Your data design should account for combining these types of searches, such as vector, full-text search, filtering, and special data types like geo-location. 
  
  Your data design should explicitly state those requirements. For more information, see [Efficient querying in the data design](./grounding-data-design.md#efficient-querying).

- **Does the index support multimodal data?**

  Choose index technologies that support multimodal data. For instance, AI searches can analyze an email, convert an image within it to vectors, and store the description in the index. Use this function to search across various content modalities, including images, videos, and audio files.

- **Does the index support automatic update capabilities when the data in the data sources changes?**

  Choose an index that has automatic update features. If one isn't available, you need to manually detect and push changes to the index. With these capabilities, the indexer can detect changes in data sources and pull updates automatically. By offloading this responsibility to the platform, you can reduce operational overhead and simplify the maintenance process.

- **Can the index perform with large volumes of data?**

  The index should be able to handle large amounts of data, be scalable, and perform well for heavy search workloads. The index stores the raw data and all of the metadata, enrichments, and entities that are associated with it. In the context of the RAG pattern, a single document that's split into multiple chunks can result in a significant increase in data volume.

- **Does the index have built-in reliability features?**

  Consider the alignment between the reliability of the inferencing endpoint, or the model, and the data store because they depend on each other. 
  
  The search process involves two steps: querying the data store and then querying the inferencing endpoint. Both steps need to have similar reliability characteristics. Balance your reliability objectives between both components to ensure search effectiveness.

  To ensure resiliency, the workload should support the expected number of concurrent users and have sufficient bandwidth to handle traffic surges. Ideally, the platform should survive zonal outages. 
  
  The data platform should be designed to prevent the use of a corrupted index for inferencing. In such cases, you should be able to rebuild the index easily. The index should also support reliable swapping between indexes by using features like aliasing to minimize downtime during index swaps. Without this functionality, you might need to rely on a backup of the index. Managing a backup comes with more complexity.

  From a workload perspective, understand the potential failure modes or stress indicators, such as throttling. For instance, although the system might normally support 50 concurrent users, it might only support 30 users during a reindexing process that runs as a background job. In that case, the timing of the background job becomes important. When you evaluate throughput of an index, include both front-end queries and back-end jobs.

- **What are the main cost drivers of this technology?**

  When you model costs, estimate the expenses that are associated with the volume of data, number of queries, and expected throughput of the index. Keep in mind that indexes are mostly platform as a service (PaaS), where pricing is abstracted. Research tiers and their capabilities to avoid overpaying for unused capacity or features.
  
  For example, AI Search bills as units, which can include capacity, throughput, and storage. Extra features can lead to more charges. For example, extensive use of image extraction features can result in a high bill. Dependencies, such as the skill set feature, that are outside the scope of the index but are part of data processing can incur extra costs.

  Paying for a tier without using the full capacity can lead to overpaying. Similarly, the number of tables in your index and the ability to handle concurrent traffic affect costs.

  To understand the costs associated with AI Search, see [Plan and manage costs of an AI Search service](/azure/search/search-sku-manage-costs).

- **Do the security features of the index fulfill your security data design?**

  Your data design should clearly specify the security and privacy requirements. In development and test environments where real production data is used, the index should support capabilities that comply with all access controls and traceability measures. Review security features such as data masking and the removal of personal information in the index.
  
  Choose an index that has the ability to uniquely identify clients through Microsoft Entra ID. The search index should also support document-level access controls to allow querying relevancy by identities. If the index doesn't offer those features, adjust your design to achieve similar capabilities with query filters. For more information, see [Security filters for trimming results in AI Search](/azure/search/search-security-trimming-for-azure-search).

  Ideally, the search index should align with network security requirements. For example, if you need to filter egress traffic to non-Microsoft sites and maintain observability, the index should offer egress controls. It should also support network segmentation. If the back-end compute is in a virtual network, private connectivity for key components, including the index, is essential to avoid exposure to the public internet. The index should easily integrate with private networks and support managed identities for authentication via Microsoft Entra ID.



## Training data platform considerations

We highly recommend that you understand the principles of good data pipeline design before you explore the technological capabilities that this article describes. For more information, see [Training data design](./training-data-design.md).


#### Considerations for storing aggregated data

- **Do you plan to conduct training with production data?**

There are two approaches to deployments, model deployment and code deployment. In model deployment, production data is used in development, which requires stringent security measures. In code deployment, the model doesn't see production data until it's in production. Although code deployment simplifies security concerns in the development environment, it can increase compute costs. Whichever approach you choose, your data platform should support separate environments for development and production.

- **Are you prioritizing convenience features over key functional features?**
When you choose a data platform for AI or machine learning, don't rely just on its notebook capabilities. Although notebooks are useful for exploratory data analysis, they shouldn't be the deciding factor. Compute resources for notebooks are typically outside the scope of the aggregation data store. They're usually integrated with other resources, such as Azure Machine Learning.

#### Considerations for processing data

 In the context of machine learning and generative AI, it's important to distinguish between ETL, ELT, and EL processes. Traditional ETL is crucial for data warehousing and object-relational mappings, where, because of schema restrictions, data must be transformed before you load it into the target system. ELT involves extracting data, loading it into a data lake, and then transforming it by using tools like Python or PySpark. In generative AI, particularly for retrieval-augmented generation (RAG), the process often involves extracting and loading documents to storage first, followed by transformations like chunking or image extraction.

#### Considerations for a feature store

For discriminative models, your data design might include an intermediate data store that caches data for extra refinement. This store, known as a feature store, allows data scientists to store features as a final step, outside of the aggregated data store.

The feature store helps catalog data for multiple uses by adding metadata like generation time and origin. This intermediate landing spot is ideal for golden training data.

The managed feature store in Machine Learning is a data storage option that integrates with MLflow and other tools. It fetches and trains data from the aggregate data store, adding a reusable layer for better data lineage and formal identification within Machine Learning.

When you use a feature store, treat it like a data store with security and access considerations.

#### Considerations for an offline inferencing data store

In some scenarios, use of a separate store is appropriate for faster future lookups because inferencing is done on pre-collected and pre-calculated data, in advance. In this process, the user request never reaches the AI model. There are several benefits:

Improved efficiency and user experience by reducing latency. Results are served faster for frequent queries, such as generating FAQs as the result.
Inference calls can be scaled out more easily as a batch process without the constraints of real-time processing.
Allows prevalidation to ensure accuracy before production.
Because the request isn't directed to the interference endpoint, it reduces the load, contributing to the reliability of the workload.
Could be more cost-effective as it reduces the need for high-performance hardware required for real-time processing.
However, this approach is only effective if you can predict the possible requests and a significant portion of the predictions are expected to be requested by users. For scenarios with fewer repeated requests, an offline inference store might be less effective.

The data store for this scenario should be optimized for read operations, must be able to handle large volumes of data and provide efficient retrieval. It should also be able to integrate into the aggregated data store. Any store with those capabilities can be considered, such as Azure Cosmos DB, or even a table storage.


## Resources

These articles provide more details about Azure products that we recommend as technology options for the considerations discussed in this article.

- [Machine Learning](/azure/well-architected/service-guides/azure-machine-learning)
- [Blob Storage](/azure/well-architected/service-guides/azure-blob-storage)
- [Azure Databricks](/azure/well-architected/service-guides/azure-databricks-security)
- [Data Factory](/azure/data-factory/)
- [AI Search](/azure/search/search-what-is-azure-search)
- [Azure Cosmos DB](/azure/well-architected/service-guides/cosmos-db)
- [Azure Managed Redis](/azure/redis/overview)

## Next steps

> [!div class="nextstepaction"]
> [Design area: MLOps and GenAIOps](./mlops-genaiops.md)
